# Thai.run agent skills

Skills for Claude Code and other agents that read `SKILL.md`, used across Thai.run repositories.

## Install

```bash
npx skills add Everything-But/ship-spec
```

Run it in the repository you want the skills in. The CLI records each skill's source and hash in `skills-lock.json`; run it again to pick up new versions.

## Skills

### `/ship-spec`

Run after `/to-spec` has written `.scratch/<feature>/spec.md`. It ships the plan:

0. Asks which branch to start from and merge into (default `develop`) and who reviews the plan (default `boy-thairun`), and puts the work on a `feat/<CLICKUP-ID>-…` branch from that base.
1. Creates a **Planning** subtask under the ClickUp task, with the spec as its description.
2. Fills its dates (from the work date: now minus 6 hours, so late-night work counts toward the day before), priority, assignee, time estimate and custom fields, sets the parent card's Project and Type to match, and recommends sprint points.
3. Commits the planning docs (`GLOSSARY.md`, new ADRs, `.scratch/<feature>/`) and pushes the branch to your fork.
4. Opens the PR from your fork to the chosen base branch on `upstream` (Bitbucket), through Claude in Chrome.
5. Moves the Planning card to `in review`.
6. Comments on the parent card twice: the PR links, then a request to the reviewer to review the plan.

Needs:

- `/to-spec` and `/setup-matt-pocock-skills` from [mattpocock/skills](https://github.com/mattpocock/skills), with the repo's `docs/agents/issue-tracker.md` set to ClickUp.
- The ClickUp connector in Claude Code.
- Claude in Chrome, signed in to Bitbucket.
