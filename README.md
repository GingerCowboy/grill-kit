# grill-kit

A Claude Code plugin that grills you on a plan **one question on screen at a time**. Each question arrives as a clickable card with Claude's recommendation first, and every answer lands on a live **Grill Board** you can keep open on your phone or in another tab.

Questions come in **rounds** of up to 4 that don't depend on each other, so you click through a round with no waiting; Claude only pauses between rounds, where your answers decide what to ask next.

## The Grill Board

The board is a private claude.ai artifact page that Claude creates the first time you grill something, and reuses after that. It shows every session: what's up next, what's waiting on a decision or a fact lookup, each decision with its reasoning, and the facts behind them, updating as you answer. Pin it on claude.ai to keep it one tap away.

Where the artifact tools aren't available (for example some local setups), Claude keeps a Markdown log in `docs/grill/` instead.

## Install

```
/plugin marketplace add GingerCowboy/grill-kit
/plugin install grill-kit@grill-kit
```

## Use

| Command | What it does |
|---|---|
| `/grill-kit:grill <plan>` | Card-by-card interview in rounds, recorded live on the Grill Board |
| `/grill-kit:grill-docs <plan>` | Same, plus a glossary (`CONTEXT.md`) and decision records (`docs/adr/`) |

While grilling, type in the card's **Other** box or in chat:

- `skip` — defer this question
- `back to Q3` — reopen an earlier decision
- `one at a time` — single-question rounds, so every answer shapes the next card (`rounds` switches back)
- `stop` — wrap up with what's settled so far

Claude acts on the plan only after you confirm the final summary.

## Credits

Based on the `grilling` skill from Matt Pocock's skills, reworked to ask one question per turn.
