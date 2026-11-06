---
name: ship-spec
description: "After /to-spec: publish the spec as a ClickUp Planning subtask, fill its fields, commit and push the planning docs, open the PR, move the card to in review, and ask for plan review on the parent card."
disable-model-invocation: true
---

If `.scratch/<feature>/spec.md` is missing, stop and tell the user to run `/to-spec`. If the ClickUp tools are missing, stop and tell the user to reconnect ClickUp.

`<feature>` is that folder's name. `<Feature>` is the feature's name as the spec's title writes it (e.g. `Order export`).

Read each file in `references/` in its own call, as the first action of the step or rule that names it, after the previous step's **Done** check passes.

Confirm every **Done** line from a read-back of what you wrote, not from a tool's `success`.

## Corrections and liked outputs

1. At any step, when the user corrects an output or explicitly says they like one, first ask in one `AskUserQuestion` call whether to make it a permanent update to this skill. Quote the rule or example you would add and name the step it belongs to. Options: update the skill, or keep it to this run.
2. On yes, read `references/self-improvement.md` and follow it, then return to the step you were on.
3. On no, leave the skill as it is and continue.

## 0. Inputs and branch

1. Ask in one `AskUserQuestion` call, 2–4 options per question, each question's default first and marked recommended:
   - **ClickUp ID** of the parent card (e.g. `DEV-1234`). Skip it if the user gave it in this conversation. Options: the ID in the branch name or the spec's `ClickUp-Task:` line, then the user's other open tasks from `clickup_filter_tasks` with `assignees: ["me"]`.
   - **Feature**, only when `.scratch/` holds more than one `spec.md`: one option per folder.
   - **Base branch** to branch from and merge into: `develop` (default), `master`.
   - **Reviewer** of the plan: `boy-thairun` (default), `Someone else`.
2. Run `git fetch upstream`.
3. If the current branch is `feat/<CLICKUP-ID>-…`, stay on it. If it does not start from `upstream/<base>`, ask the user whether to move it onto `upstream/<base>` (default) or keep it. To move it, run `git rebase --autostash --onto upstream/<base> $(git merge-base HEAD upstream/<other base>)`, where `<other base>` is the base option the user did not pick.
4. Otherwise run `git switch -c feat/<CLICKUP-ID>-<short-description> upstream/<base>`.

**Done** when you have the ClickUp ID, feature, base branch and reviewer, and the feature branch is checked out on `upstream/<base>` or kept as the user chose.

## 1. Planning subtask

1. Read `references/planning-description.md`.
2. Call `clickup_get_user`. Keep the user's id and `current_time`.
3. Call `clickup_get_task` on the parent with `include: ["custom_fields"]`. If it is not assigned to the user, stop with `TASK OWNERSHIP INVALID`. Keep its list id, priority, `Project`, `Type` and `Starting on`.
4. Call `clickup_create_task` in the parent's list with `parent` = the parent ID, `name: "Planning"`, and `markdown_description` written as `references/planning-description.md` says.

**Done** when `clickup_get_task` on the new card shows the full description and the right parent.

## 2. Card fields

1. Read `references/card-fields.md`.
2. Set the Planning card's fields in one `clickup_update_task` call.
3. Set the parent's `Project`, `Type` and `Starting on` in one `clickup_update_task` call.
4. Give the user the recommended sprint points and the `curl` command from `references/card-fields.md`.

**Done** when `clickup_get_task` with `include: ["custom_fields"]` on both cards shows every value you set.

## 3. Commit and push

1. Stage only `GLOSSARY.md`, this feature's new ADRs under `docs/adr/`, and `.scratch/<feature>/`.
2. Commit as `docs(<area>): spec <Feature>`, where `<area>` is the code area the spec changes (e.g. `orders` for `src/orders/`), with a one- or two-line body, then the trailers `ClickUp-Task: <CLICKUP-ID>` and the session's co-author line.
3. Run `git push -u origin <branch>`. Keep the "Create pull request" URL it prints.

**Done** when `git status` shows the three paths committed and the push reports the new branch.

## 4. Pull request

1. Read `references/pull-request.md`.
2. Load the `claude-in-chrome` skill and open the "Create pull request" URL.
3. Fill the form with the values from `references/pull-request.md`.
4. Screenshot the form and check the repos, the branches, and that the files changed are the ones committed in step 3.
5. Press **Create pull request**.

**Done** when the tab's URL is the new PR in the `upstream` repo. Then close the tab.

## 5. Card to in review

1. Call `clickup_get_task` on the Planning card with `expand_statuses`.
2. Set the Planning card's status to `in review`. If the user named a status the list lacks, ask them.
3. Leave the parent card's status and assignee to the status-sync action.

**Done** when a read-back shows `in review`.

## 6. Review request

1. Read `references/comments.md`.
2. Call `clickup_resolve_assignees` for the reviewer's numeric id.
3. Post the pull requests comment with `clickup_create_comment`, `entity_type: "task"` and `entity_id` = the parent ID.
4. Post the review request as a second comment.

**Done** when `clickup_get_task_comments` on the parent shows both comments: the first with every PR, the second with the `@<username>` mention.

## Report

Give the user:

- Links to the Planning card, each PR and the parent's two comments.
- Each field and its value, with the estimate's breakdown and the recommended sprint points.
- What is left for them: sprint points, empty fields, and the reviewer's plan review.
