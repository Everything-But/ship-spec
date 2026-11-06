# Updating this skill

1. Load the `skill-creator` skill.
2. Edit the source: `skills/ship-spec/` in the user's clone of `Everything-But/ship-spec`. If this skill's base directory is inside that clone, use it. Otherwise ask the user for the clone's path. The installed copy is overwritten on the next `npx skills add`.
3. Put the change where the step that produced the output will read it:
   - A correction to what the step does: that step in `SKILL.md`.
   - A value, template or rule detail: that step's file in `references/`.
   - A liked output: that step's file in `references/`, under an `## Example` heading.
   - If the step has no reference file, create `references/<topic>.md` and add "Read `references/<topic>.md`." to that step.
4. Keep this skill's style: steps are short imperative instructions ending on a **Done** line; reasons, templates and examples go in the step's reference file.
5. Skip skill-creator's test runs and description optimization. Test runs would create real ClickUp cards, branches, PRs and comments.
6. Show the user the diff. Tell them the installed skill picks it up after they commit, push and rerun `npx skills add Everything-But/ship-spec`.

**Done** when the change is in the source files and the user has seen the diff.
