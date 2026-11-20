# Card fields

## Work date

Take dates from `current_time` in `clickup_get_user`. It is in the user's timezone; your own clock may not be.

The **work date** is `current_time` minus 6 hours, date part only. Work done before 06:00 counts toward the previous day.

## Every card

Set these on the first card and on each repo card, and no other field.

- Name: `[<TASK-ID>] <card name>`, from its Task Card. Where the first card's name already starts with a `[<TASK-ID>]`, keep it.
- Description: the block the tracker doc gives for a repo card. On the first card, keep what is there and add the block at the end; where an earlier run added one, replace it.
- Custom fields: read them with `clickup_get_custom_fields` for the list and the folder, then:
  - `Project`: this spec's project. Where the first card already has a value, keep it and give the repo cards the same.
  - `Repository`: the Task Card's repository.
  - `Type`: the Task Card's Type. Where the first card already has a value, keep it.
  - `Starting on`, and any other date field that means "started": the work date. Where the first card already has one, keep it: work on the card started earlier.
  - `PR Link`: empty. It is set when the card's draft PR opens, as the tracker doc says.
  - Any field nothing true fills yet, such as `Completed on` until the card is done: empty. Tell the user which fields you left empty and why.

Sprint points and time estimates go on the cards' subtasks, in `/to-tickets`. ClickUp sums them into each card.
