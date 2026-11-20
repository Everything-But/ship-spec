# Fix plan

A **fix plan** changes a plan that was already approved: the first card is past `backlog`. It usually comes straight from a grilling session (`/grill-with-docs`) on the card's branch. The reduced flow below replaces the matching parts of each step; every step not named runs as written.

## 0. Inputs and branch

- **Kind of plan**: ask it first. When the first card's ID is already known, call `clickup_get_task` on it before asking. Recommend `Fix plan` when its status is past `backlog`, else `New plan`.
- **Branch**: stay on the first card's branch (one branch per card). Skip the rebase question.
- **Base branch**: the base the card's branch already starts from. Recommend it first.
- **Reviewer**: `No review` (default), the tracker doc's plan reviewer, `Someone else`. The plan was approved once, so a fix plan usually needs no review, but the user sometimes wants one before implementing.

## 1. Cards

- Create a repo card only for a repository the fix adds to the spec.

## 2. Card fields

- On existing cards, change only the description block, and only where the fix changes its Owned Paths or Must Not Modify. A repo card created in step 1 gets every field.

## 3. Commit and push

- Commit as `docs(<area>): spec fix <Feature>`.
- Push to the first card's branch. If a PR is already open for that branch, the push updates it.

## 4. Review request

- `No review`: skip it.
- A reviewer: post the review request on each card the fix changes.
