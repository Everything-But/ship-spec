# Card fields

## Work date

Take dates from `current_time` in `clickup_get_user`. It is in the user's timezone; your own clock may not be.

The **work date** is `current_time` minus 6 hours, date part only. Work done before 06:00 counts toward the previous day.

## Planning card

- `start_date`: the work date.
- `due_date`: the day after the work date.
- `priority`: the parent's priority.
- `assignees`: `["me"]`.
- `time_estimate`: the planning effort in minutes, as a string (`"240"`). Build it from a breakdown you state to the user (code reading, design decisions, glossary/ADR/spec writing, Tech Lead review).
- Size guide: a feature-sized plan is about 4h.
- Custom fields: read them with `clickup_get_custom_fields` for the list and the folder, then:
  - Project and repository dropdowns: from this repo.
  - `Type`: the parent's value, else the kind of work the parent is.
  - `Starting on`, and any other date field that means "started": the work date.
  - `PR Link`: empty, on every card. A task can span several repositories, so its PRs are listed in the parent card's comment instead.
  - Any field nothing true fills yet, such as `Completed on` until the card is done: empty. Tell the user which fields you left empty and why.

## Parent card

Set `Project`, `Type` and `Starting on`, and no other field.

- `Project` and `Type`: the Planning card's values. Where the parent already has a value, keep it and give the Planning card the parent's value instead.
- `Starting on`: the work date. Where the parent already has one, keep it: work on the card started earlier.

## Sprint points

Sprint points go on the Planning card only. ClickUp sums subtask points into the parent, so the parent's points stay as they are.

Points are a ClickApp field this connector cannot write. Recommend a value (Fibonacci; 1 point ≈ a quarter day, about 2h; a settled half-day plan, about 4h, is 2). Tell the user to set it in the Planning card's **Sprint Points** field, or to run this command with their own token:

```bash
curl -X PUT "https://api.clickup.com/api/v2/task/<task-id>" -H "Authorization: $CLICKUP_API_TOKEN" -H "Content-Type: application/json" -d '{"points": 2}'
```
