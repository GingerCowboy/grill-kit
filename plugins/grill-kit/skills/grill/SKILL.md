---
name: grill
description: Grill me on a plan one question at a time, with clickable answer cards and a live decision log.
disable-model-invocation: true
argument-hint: <plan, idea, or file to grill>
---

Interview the user relentlessly about the plan they named when invoking this skill (or the plan in the conversation so far) until you reach a shared understanding. Ask **one question per turn**, each as a **card**, and write every answer to the **log**.

## Design tree

Map the plan as a **design tree**: every decision branches into the decisions that hang off it. The **frontier** is every decision whose prerequisites are settled: the questions you can ask now without guessing at answers you haven't heard. The tree lives in the log; the user sees one question at a time.

Facts are your job; decisions are the user's. When a frontier question needs a fact from the environment (code, files, docs, tools), dispatch a subagent to find it, park the question under **Waiting on**, and ask another frontier question while the subagent runs. Record what it finds under **Facts**.

## Loop

1. **Start the log.** Create `docs/grill/<YYYY-MM-DD>-<topic-slug>.md` from the [log template](#log-template), with the first frontier filled in. Tell the user the path in one line.
2. **Pick one question**: the frontier question whose answer unblocks the most of the tree.
3. **Ask it as a card**: one AskUserQuestion call holding exactly one question.
   - `header`: `Q<n> <topic>`, 12 characters max. Number questions in the order asked.
   - `question`: the decision, self-contained, ending in `?`. Carry the context the user needs inside it, in three sentences or fewer.
   - `options`: 2–4 concrete answers, your recommendation first with its label ending ` (Recommended)`. Each `description` is the trade-off in one sentence. The user's free-text "Other" box is added for you, so every option is a real answer.
   - `preview`: only when comparing code, layouts, or wording side by side.
   - `multiSelect: true` only when answers genuinely combine.

   When a question has no sensible candidate answers (a name, a number, a free description), or AskUserQuestion is unavailable, ask in plain text instead: the question, then `➡️ <your recommended answer>`, then wait.
4. **Record the answer.** Update the log: add the decision under **Decisions** with the user's reasoning (their "Other" text or annotation notes; "took recommendation" if none), move newly unblocked questions into **Frontier**, drop branches the answer closed, and refresh the status line. Reply in one line. Challenge the answer only when it contradicts an earlier decision or a recorded fact, and make that challenge the next card.
5. **Repeat** from step 2.

## User controls

The user steers by typing in the card's "Other" box or in chat:

- `skip` / `later`: move the question to **Deferred**; continue.
- `back to Q<n>`: reopen that decision and re-derive the branches it fed.
- `batch`: ask up to 4 independent frontier questions in one AskUserQuestion call (the UI still shows them one at a time); return to one per call afterwards.
- `stop`: jump to **Done** with what is settled.

## Done

The interview is done when nothing is left to ask: **Frontier** is empty, no fact lookup is running, and the user has answered or accepted as open every **Deferred** item (along with whatever waits on it). Then write **Summary** in the log (the plan as now specified, in a few bullets, plus any accepted open items), show it in chat, and ask the user to confirm the shared understanding. Act on the plan only after they confirm.

## Log template

Rewrite the whole file after each answer, so it always reads as the current state.

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
