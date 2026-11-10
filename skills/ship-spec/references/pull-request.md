# Pull request

Bitbucket has no CLI here, so the PR is created in the user's Chrome. `origin` is the user's fork; `upstream` is the shared repository.

- **Destination**: the `upstream` repository and the base branch from step 0.
- **Title**: `[<CLICKUP-ID>] Spec: <Feature>`.
- **Description**, one line each, in this order:
  1. `ClickUp-Task: <CLICKUP-ID>`
  2. The task and its Planning card ID.
  3. The goal.
  4. The files changed.
  5. The base branch, only when it is not `develop`, with the user's reason if they gave one.
  6. `Contracts: NO. Migrations: none. Tests: none (docs only). Security impact: N/A.`
  7. The session's generated-with line.
- **Reviewers**: empty unless the user names PR reviewers. Bitbucket adds default reviewers when you choose the `upstream` repository; remove them. The plan reviewer from step 0 reviews on the ClickUp card, not on the PR.
- **Draft**: create the PR with **Create draft**. The PR stays open for the whole card: the reviewer approves the plan by marking it ready for review, the implementation is pushed to the same branch, and the reviewer approves the PR once that passes. So it starts as a draft, and only the reviewer marks it ready.
