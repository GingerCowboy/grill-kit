# grill-kit

A Claude Code plugin that grills you on a plan **one question at a time**. Each question arrives as a clickable card with Claude's recommendation first, and every answer lands in a decision log you can open while you go.

## Install

```
/plugin marketplace add GingerCowboy/grill-kit
/plugin install grill-kit@grill-kit
```

## Use

| Command | What it does |
|---|---|
| `/grill-kit:grill <plan>` | One-question-at-a-time interview, logged to `docs/grill/<date>-<topic>.md` |
| `/grill-kit:grill-docs <plan>` | Same, plus a glossary (`CONTEXT.md`) and decision records (`docs/adr/`) |

While grilling, type in the card's **Other** box or in chat:

- `skip` — defer this question
- `back to Q3` — reopen an earlier decision
- `batch` — get the next few independent questions in one go
- `stop` — wrap up with what's settled so far

Claude acts on the plan only after you confirm the final summary.

## Credits

Based on the `grilling` skill from Matt Pocock's skills, reworked to ask one question per turn.
