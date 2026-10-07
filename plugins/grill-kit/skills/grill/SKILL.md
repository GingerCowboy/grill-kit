---
name: grill
description: Grill me on a plan in fast rounds of clickable answer cards, one question on screen at a time, with a live decision log.
disable-model-invocation: true
argument-hint: <plan, idea, or file to grill>
---

Interview the user relentlessly about the plan they named when invoking this skill (or the plan in the conversation so far) until you reach a shared understanding. Ask in **rounds** of **cards**, and write every answer to the **log**.

## Design tree

Map the plan as a **design tree**: every decision branches into the decisions that hang off it. The **frontier** is every decision whose prerequisites are settled: the questions you can ask now without guessing at answers you haven't heard. The tree lives in the log.

Facts are your job; decisions are the user's. When a frontier question needs a fact from the environment (code, files, docs, tools), dispatch a subagent in the background to find it, park the question under **Waiting on**, and keep asking the rest of the frontier while it runs. Record what it finds under **Facts**.

## Rounds

A **round** is one AskUserQuestion call holding up to 4 frontier questions. The UI shows them one at a time and moves to the next instantly, so the user waits on you only between rounds, which is exactly where their answers reshape what comes next.

- Every question in a round stands on its own: a question whose answer depends on another question in the same round belongs to a later round.
- When the frontier holds more than 4, take the 4 whose answers unblock the most of the tree.
- A challenge goes in a round of its own, as the only card.

Keep the turn between rounds **lean**: the user is waiting through every token you write.

## Loop

1. **Start the log.** Create `docs/grill/<YYYY-MM-DD>-<topic-slug>.md` from the [log template](#log-template), with the first frontier filled in. Tell the user the path in one line.
2. **Ask the round.** One AskUserQuestion call; each question is a card:
   - `header`: `Q<n> <topic>`, 12 characters max. Number questions in the order asked.
   - `question`: the decision, self-contained, ending in `?`. Carry the context the user needs inside it, in three sentences or fewer.
   - `options`: 2–4 concrete answers, your recommendation first with its label ending ` (Recommended)`. Each `description` is the trade-off in one sentence. The user's free-text "Other" box is added for you, so every option is a real answer.
   - `preview`: only when comparing code, layouts, or wording side by side.
   - `multiSelect: true` only when answers genuinely combine.

   When a question has no sensible candidate answers (a name, a number, a free description), or AskUserQuestion is unavailable, ask it in plain text instead: the question, then `➡️ <your recommended answer>`, then wait. Plain-text questions go one per turn.
3. **Record and ask on, in one message.** Make the log edits for the round's answers and the next round's AskUserQuestion call (cards as in step 2) as parallel tool calls in the same message, so the log never delays the next card. For each answer, record the decision under **Decisions** with the user's reasoning (their "Other" text or annotation notes; "took recommendation" if none), move newly unblocked questions into **Frontier**, drop branches the answer closed, and refresh the status line. Write chat text only when a challenge or a new fact needs explaining. Challenge an answer only when it contradicts an earlier decision or a recorded fact.
4. **Repeat** from step 3 until **Done**.

## User controls

The user steers by typing in a card's "Other" box or in chat:

- `skip` / `later`: move that question to **Deferred**; continue.
- `back to Q<n>`: reopen that decision and re-derive the branches it fed.
- `one at a time`: shrink rounds to a single question each, so every answer can reshape the next card; `rounds` switches back.
- `stop`: jump to **Done** with what is settled.

## Done

The interview is done when nothing is left to ask: **Frontier** is empty, no fact lookup is running, and the user has answered or accepted as open every **Deferred** item (along with whatever waits on it). Then write **Summary** in the log (the plan as now specified, in a few bullets, plus any accepted open items), show it in chat, and ask the user to confirm the shared understanding. Act on the plan only after they confirm.

## Log template

Create the file once from this template, then change it with small in-place edits: append each decision and fact, add or delete single frontier lines, update the status line. Write **Summary** once, at **Done**.

```markdown
# Grill: <topic>

> **<In progress | Done>** · <n> decided · <n> open · <n> deferred

## Summary

_Written when the frontier is empty._

## Decisions

### Q1 · <title>
- **Answer:** <answer>
- **Why:** <reasoning>
- **Unblocked:** <titles of questions this opened, or "nothing">

## Frontier

- <question>

## Waiting on

- <question> (needs: <open decision> | fact lookup: <what>)

## Deferred

- Q<n> · <title>

## Facts

- <fact> (`<source path:line or URL>`)
```
