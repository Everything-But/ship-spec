---
name: ship-spec
description: "After /to-spec: publish the spec as a ClickUp Planning subtask, fill its fields, commit and push the planning docs, open the PR, move the card to in review, and ask for plan review on the parent card."
disable-model-invocation: true
---

Run after `/to-spec` has written `.scratch/<feature>/spec.md`. You ship the plan, not code: a ClickUp **Planning** subtask holding the spec, a docs-only commit, a PR, the card in `in review`, and a review request on the parent card. Each step ends on a check you make by reading back what you wrote; a tool's `success` is not that check.

Needed in hand: the spec file and the parent ClickUp ID (e.g. `DEV-5822`). If the ClickUp tools are missing, stop and tell the user to reconnect ClickUp; there is no fallback.

## 0. Base branch and reviewer

Ask both in one `AskUserQuestion` call, each with its default as the first, recommended option:

- **Base branch**: the branch the feature branch is created from and the PR merges into. Default `develop`; offer `master` beside it.
- **Reviewer**: the ClickUp user asked to review the plan in step 6. Default `boy-thairun`.

Then put the work on a feature branch from that base. On a branch already named for this card (`feat/<CLICKUP-ID>-…`), keep it, and say so if it does not start from `upstream/<base>`. Otherwise `git fetch upstream` and `git switch -c feat/<CLICKUP-ID>-<short-description> upstream/<base>`; the untracked planning docs come along.

Done when both answers are in hand and the feature branch is checked out.

## 1. Planning subtask

1. `clickup_get_user` for the user's id and `current_time` (dates come from this clock, not yours). `clickup_get_task` on the parent: it must be assigned to the user, or stop with `TASK OWNERSHIP INVALID`. Note its list id, priority and `Type` field.
2. `clickup_create_task` in the parent's list with `parent` set, `name: "Planning"`, and `markdown_description` = the spec, opening with one line naming the parent ID, the repo copy path and the branch. Name repo files by path in backticks, never as relative links ClickUp cannot follow. Put every dotted name (`GLOSSARY.md`, `Thai.run`) in backticks too, or ClickUp turns it into an `http://` link.

Done when `clickup_get_task` on the new card shows the full description and the right parent.

## 2. Card fields

One `clickup_update_task` call:

- `start_date` = today, `due_date` = today + 1 day (from `current_time`, in the user's timezone).
- `priority` = the parent's priority.
- `assignees: ["me"]`.
- `time_estimate` in minutes: the planning effort, built from a breakdown you state to the user (code reading, design decisions, glossary/ADR/spec writing, Tech Lead review). About 4h for a feature-sized plan.
- Custom fields: read them with `clickup_get_custom_fields` for the list and folder, then pick each value from what you know: the project and repository dropdowns from this repo, `Type` from the parent (else the kind of work the parent is), date fields that mean "started" = the start date. Leave a field empty when nothing true fills it yet, such as `Completed on` until the card is done. `PR Link` stays empty on every card: a task can span several repositories, so its PRs are listed in the parent card's comment (step 6). Say which fields you left empty and why.

**Sprint points** go on the Planning card only. The parent card's points stay as they are: ClickUp sums its subtasks' points into the parent. They are a ClickApp field this connector cannot write, so recommend a value (Fibonacci, 1 point ≈ a quarter to half a day; a settled half-day plan is 2) and give the user the UI path and this command to run with their own token on the Planning card:

```bash
curl -X PUT "https://api.clickup.com/api/v2/task/<task-id>" -H "Authorization: $CLICKUP_API_TOKEN" -H "Content-Type: application/json" -d '{"points": 2}'
```

Done when a read-back of the card shows every value you set.

## 3. Commit and push

Stage only the planning docs this feature produced: `GLOSSARY.md`, the new ADRs under `docs/adr/`, and `.scratch/<feature>/`. Agent tooling stays local: leave `CLAUDE.md`, an untracked `AGENTS.md`, and `docs/agents/` unstaged, along with anything that came over untracked from another branch.

Commit as `docs(<area>): spec <Feature>` with a body of one or two lines, then the trailers `ClickUp-Task: <CLICKUP-ID>` and the session's co-author line. `git push -u origin <branch>`; keep the "Create pull request" URL Bitbucket prints.

Done when `git status` shows the three paths committed and the push reports the new branch.

## 4. Pull request

The remotes are on Bitbucket, which has no CLI here: create the PR in the user's Chrome. Load the `claude-in-chrome` skill, open the URL from the push, and set:

- **Destination**: the `upstream` repository (the fork is `origin`), the base branch from step 0.
- **Title**: `[<CLICKUP-ID>] Spec: <Feature>`.
- **Description**, one line each: `ClickUp-Task: <CLICKUP-ID>` first, then the task and its Planning card ID, the goal, the files changed, the base branch when it is not `develop` (with the user's reason, if they gave one), `Contracts: NO. Migrations: none. Tests: none (docs only). Security impact: N/A.`, and the session's generated-with line.
- No reviewers unless the user names them.

Check the form (repos, branches, file count) on a screenshot before pressing **Create pull request**. Done when the tab's URL is the new PR in the upstream repo; close the tab you opened.

## 5. Card to in review

Set the Planning card's status to `in review` (the list's statuses come from `clickup_get_task` with `expand_statuses`; a status the user names that the list lacks is a question for them, not a guess). The parent card's status and assignee stay untouched: the status-sync action owns them.

Done when a read-back shows `in review`.

## 6. Review request on the parent card

Post **two** separate comments on the parent card with `clickup_create_comment`, in this order:

1. **Every related PR**, one line per PR labelled with its repository:

   ```markdown
   Pull requests:
   - api: [[<CLICKUP-ID>] Spec: <Feature>](<PR URL>)
   ```

2. **The review request**, a mention of the reviewer from step 0 with `please review the plan` and the Planning card's link. Resolve the reviewer's numeric id with `clickup_resolve_assignees` and write the mention as `[@<username>](#user_mention#<id>)`:

   ```markdown
   [@<reviewer>](#user_mention#<id>) please review the plan: [Planning](<Planning card URL>) (<Planning ID>)
   ```

Done when `clickup_get_task_comments` on the parent shows both comments: the first with every PR, the second with the `@<username>` mention.

## Report

Link the Planning card, each PR and the parent's two comments, list the fields and the values you chose (with the estimate's breakdown and the recommended sprint points), and name what is left for the user: sprint points, anything left empty, and the plan review the reviewer was asked for.
