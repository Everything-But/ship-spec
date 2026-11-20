# Thai.run agent skills

Skills for Claude Code and other agents that read `SKILL.md`, used across Thai.run repositories.

## Install

```bash
npx skills add Everything-But/ship-spec
```

Run it in the repository you want the skills in. The CLI records each skill's source and hash in `skills-lock.json`; run it again to pick up new versions.

## Skills

### `/ship-spec`

Run after `/to-spec` has written the spec. It ships the plan:

0. Asks for the ClickUp ID of the first card if you haven't given it, which branch to start from and merge into (default `develop`), and who reviews the plan (default: the plan reviewer in `docs/agents/issue-tracker.md`), checks the spec has a complete Task Card for each repository it changes, then puts the work on the first card's `<type>/<CLICKUP-ID>-…` branch from that base. An existing card branch that starts elsewhere is moved onto the base if you agree.
1. Creates a ClickUp **repo card** for each other repository the spec changes (one card = one repository = one PR), links it to the first card, and writes its ID into the spec.
2. Names each card `[<TASK-ID>] …`, adds the spec file, Owned Paths and Must Not Modify to its description, and sets its Project, Repository, Type and Starting on (from the work date: now minus 6 hours, so late-night work counts toward the day before).
3. Commits the planning docs (`GLOSSARY.md`, new ADRs, the spec and any other notes in `.scratch/<feature>/`) and pushes the branch to your fork.
4. Comments on each card asking the reviewer to approve the plan, with that card's whole Task Card in the comment.

It creates no Planning subtask and opens no PR. Each card waits in `backlog` until the reviewer moves it to `ready`; the card's draft PR opens in its RED phase.

Needs:

- `/to-spec` and `/setup-matt-pocock-skills` from [mattpocock/skills](https://github.com/mattpocock/skills), with the repo's `docs/agents/issue-tracker.md` set to ClickUp. `/ship-spec` also reads five things from it: where specs live, what a Task Card carries, how a repo card is created, how a card's branch is named, and the plan reviewer.
- The ClickUp connector in Claude Code.
- For Bitbucket repos, Claude in Chrome signed in to Bitbucket. For GitHub repos, `gh` signed in.
