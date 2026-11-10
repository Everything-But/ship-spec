# Parent card comments

## 1. Pull requests

One line per PR, labelled with its repository's name (e.g. `photos-api`):

```markdown
Pull requests:
- <repository>: [[<CLICKUP-ID>] Spec: <Feature>](<PR URL>)
```

## 2. Review request

Only when the user picked a reviewer. With `No review`, nobody is tagged; the user asks for a review later if they want one.

Write the mention as `[@<username>](#user_mention#<id>)`, with the numeric id from `clickup_resolve_assignees`:

```markdown
[@<reviewer>](#user_mention#<id>) please review the plan: [Planning](<Planning card URL>) (<Planning ID>)
```
