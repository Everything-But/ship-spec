# Review request comment

One comment on each card. The plan is reviewed and approved in that comment's thread, so it carries the card's whole Task Card.

Write the mention as `[@<username>](#user_mention#<id>)`, with the numeric id from `clickup_resolve_assignees`. Copy the Task Card's parts from the spec as written. The spec link is the spec file's URL on the pushed branch of the fork.

```markdown
[@<reviewer>](#user_mention#<id>) please review the plan for `[<TASK-ID>] <card name>`

- **Spec file:** [`Implementation/<feature>-spec.md`](<spec URL on the pushed branch>), branch `<branch>` in `<spec repo>`
- **Repository:** <repo> · **Type:** <Type> · **Points:** <N>
- **Owned Paths:**
  - `<path>`
- **Must Not Modify:**
  - `<path>`
- **RED Scenarios:**
  1. `RED-1`: <scenario>
- **Acceptance Criteria:**
  - `AC-1`: <criterion>
- **Other cards of this spec:** <ID and name of each, or none>
```

For a fix plan, the first line reads `please review the plan fix for …`, and the list holds only the parts the fix changes.
