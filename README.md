# Thai.run agent skills

Skills for Claude Code and other agents that read `SKILL.md`, used across Thai.run repositories.

## Install

```bash
npx skills add <owner>/skills
```

Run it in the repository you want the skills in. The CLI records each skill's source and hash in `skills-lock.json`; run it again to pick up new versions.

## Skills

### `/ship-spec`

Run after `/to-spec` has written `.scratch/<feature>/spec.md`. It ships the plan:

1. Creates a **Planning** subtask under the ClickUp task, with the spec as its description.
2. Fills its dates, priority, assignee, time estimate and custom fields, and recommends sprint points.
3. Commits the planning docs (`GLOSSARY.md`, new ADRs, `.scratch/<feature>/`) and pushes the branch to your fork.
4. Opens the PR from your fork to `upstream` `develop` on Bitbucket, through Claude in Chrome.
5. Moves the Planning card to `in review`.
6. Comments on the parent card twice: the PR links, then a request to review the plan.

Needs:

- `/to-spec` and `/setup-matt-pocock-skills` from [mattpocock/skills](https://github.com/mattpocock/skills), with the repo's `docs/agents/issue-tracker.md` set to ClickUp.
- The ClickUp connector in Claude Code.
- Claude in Chrome, signed in to Bitbucket.
