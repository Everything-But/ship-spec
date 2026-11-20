---
name: ship-spec
description: "After /to-spec or a grilling session: create a ClickUp card for each extra repository the spec changes, fill every card's name, description and fields from its Task Card, commit and push the planning docs on the first card's branch, and ask the plan reviewer on each card to approve the plan. Also ships a fix plan: a change to a plan that was already approved."
disable-model-invocation: true
---

Read `docs/agents/issue-tracker.md` first: the **tracker doc**. Take five things from it: where specs live, what a Task Card carries, how a repo card is created, how a card's branch is named, and the plan reviewer. If it is missing, stop and tell the user.

The **spec** is the one this branch adds or changes where the tracker doc keeps specs: uncommitted (`git status`), or in a commit that no `upstream` branch has (`git log HEAD --not --remotes=upstream`). If there is none, stop and tell the user to run `/to-spec`; when this conversation holds a grilling session, offer to run it on that session's decisions. If the ClickUp tools are missing, stop and tell the user to reconnect ClickUp.

`<feature>` is the spec's short name as its path gives it (e.g. `order-download` for `Implementation/order-download-spec.md`). `<Feature>` is the feature's name as the spec's title writes it (e.g. `Order export`).

The spec has one **Task Card** per repository it changes. The **first card** is the ClickUp card the user gives: it covers this repository. Every other Task Card gets a **repo card**, which this skill creates. The **cards** are the first card and the repo cards together.

This skill creates no subtask and opens no pull request. Tickets become subtasks in `/to-tickets`; each card's PR opens as a draft in its RED phase, after its plan is approved.

Read each file in `references/` in its own call, as the first action of the step or rule that names it, after the previous step's **Done** check passes.

Confirm every **Done** line from a read-back of what you wrote, not from a tool's `success`.

A **fix plan** changes a plan that was already approved. For a fix plan, read `references/fix-plan.md` as soon as the user picks it, and let it override each step it names.

## Corrections and liked outputs

1. At any step, when the user corrects an output or explicitly says they like one, first ask in one `AskUserQuestion` call whether to make it a permanent update to this skill. Quote the rule or example you would add and name the step it belongs to. Options: update the skill, or keep it to this run.
2. On yes, read `references/self-improvement.md` and follow it, then return to the step you were on.
3. On no, leave the skill as it is and continue.

## 0. Inputs and branch

1. Ask in one `AskUserQuestion` call, 2–4 options per question, each question's default first and marked recommended:
   - **Kind of plan**: `New plan` or `Fix plan`, recommended as `references/fix-plan.md` says. For a fix plan, ask the branch, base and reviewer questions that file gives instead of the ones below.
   - **ClickUp ID** of the first card (e.g. `DEV-1234`). Skip it if the user gave it in this conversation. Options: the ID in the branch name or this repository's Task Card `ClickUp:` line, then the user's other open tasks from `clickup_filter_tasks` with `assignees: ["me"]`.
   - **Feature**, only when this branch adds or changes more than one spec: one option per spec.
   - **Base branch** to branch from and merge into: `develop` (default), `master`.
   - **Reviewer** of the plan: the tracker doc's plan reviewer (default), `Someone else`. A new plan always has a reviewer: their sign-off opens the work.
2. If the spec lacks a Task Card for a repository it changes, or a Task Card lacks a part the tracker doc lists, stop and tell the user what is missing.
3. Run `git fetch upstream`.
4. If the current branch is the first card's (`<type>/<CLICKUP-ID>-…`), stay on it. If it does not start from `upstream/<base>`, ask the user whether to move it onto `upstream/<base>` (default) or keep it. To move it, run `git rebase --autostash --onto upstream/<base> $(git merge-base HEAD upstream/<other base>)`, where `<other base>` is the base option the user did not pick.
5. Otherwise run `git switch -c <branch> upstream/<base>`, with `<branch>` named as the tracker doc says for the first card's Type.

**Done** when you have the kind of plan, ClickUp ID, feature, base branch and reviewer, every Task Card is complete, and the first card's branch is checked out on `upstream/<base>` or kept as the user chose.

## 1. Cards

1. Call `clickup_get_user`. Keep the user's id and `current_time`.
2. Call `clickup_get_task` on the first card with `include: ["custom_fields"]`. If it is not assigned to the user, stop with `TASK OWNERSHIP INVALID`. Keep its list id, name, description, priority, `Project`, `Type` and `Starting on`.
3. For each other Task Card whose `ClickUp:` line has no ID, create its repo card as the tracker doc says.
4. Write each new card's ID into its Task Card's `ClickUp:` line in the spec.

**Done** when `clickup_get_task` on each repo card shows its name, `backlog` status, assignee and link to the first card, and every Task Card in the spec names a ClickUp ID.

## 2. Card fields

1. Read `references/card-fields.md`.
2. Set each card's name, description and fields in one `clickup_update_task` call per card.

**Done** when `clickup_get_task` with `include: ["custom_fields"]` on every card shows every value you set.

## 3. Commit and push

1. Stage only `GLOSSARY.md`, this feature's new ADRs under `docs/adr/`, the spec, and any other planning notes for this feature where the tracker doc keeps them.
2. Commit as `docs(<area>): spec <Feature>`, where `<area>` is the code area the spec changes (e.g. `orders` for `src/orders/`), with a one- or two-line body, then the trailers `ClickUp-Task: <first card's ID>` and the session's co-author line.
3. Run `git push -u origin <branch>`.

**Done** when `git status` shows those paths committed and the push reports the new branch.

## 4. Review request

1. Read `references/comments.md`.
2. Call `clickup_resolve_assignees` for the reviewer's numeric id.
3. Post one review request on each card with `clickup_create_comment`, `entity_type: "task"` and `entity_id` = that card's ID.
4. Leave every card's status alone: each waits in `backlog` until the reviewer moves it to `ready`.

**Done** when `clickup_get_task_comments` on each card shows its review request with the `@<username>` mention and every part of its Task Card.

## Report

Give the user:

- Links to each card, its review request comment, and the pushed branch.
- Each field and its value, per card.
- What is left for them: empty fields, and the reviewer's sign-off on each card, after which that card moves to `ready` and its RED phase can start.
