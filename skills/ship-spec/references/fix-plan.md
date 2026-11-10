# Fix plan

A **fix plan** extends or fixes a card whose plan was already approved: the parent already has a `Planning` subtask. It usually comes straight from a grilling session (`/grill-with-docs`) on the parent's branch. The reduced flow below replaces the matching parts of each step; every step not named runs as written.

## 0. Inputs and branch

- **Kind of plan**: ask it first. When the parent ID is already known, call `clickup_get_task` on it with `subtasks: true` before asking. Recommend `Fix plan` when the parent already has a subtask named `Planning…`, else `New plan`.
- **Branch**: stay on the parent's branch (one branch per parent card). Skip the rebase question.
- **Base branch**: the base the parent's branch already starts from. Recommend it first.
- **Reviewer**: `No review` (default), the tracker doc's plan reviewer, `Someone else`. The plan was approved once, so a fix plan usually needs no review, but the user sometimes wants one before implementing.

## 1. Planning subtask

- Name: `Planning fix <N>`. `<N>` is one more than the number of `Planning fix …` subtasks the parent already has.
- The description's first line also names the earlier Planning card it extends.

## 2. Card fields

- `start_date` and `due_date`: both the work date.
- `time_estimate`: the grilling and spec-writing effort only.
- Parent card: leave every field as it is.

## 3. Commit and push

- Push to the parent's branch. If a PR is already open for that branch, the push updates it.

## 4. Pull request

- Skip it.

## 5. Card status

- `No review`: set the status to `done`.
- A reviewer: set the status to `in review`.

## 6. Review request

- `No review`: skip it.
- A reviewer: post only the review request comment, naming `Planning fix <N>`.
