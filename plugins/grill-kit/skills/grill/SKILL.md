---
name: grill
description: Grill me on a plan in fast rounds of clickable answer cards, one question on screen at a time, with a live decision board.
disable-model-invocation: true
argument-hint: <plan, idea, or file to grill>
---

Interview the user relentlessly about the plan they named when invoking this skill (or the plan in the conversation so far) until you reach a shared understanding. Ask in **rounds** of **cards**, and record every answer on the **board**.

## Design tree

Map the plan as a **design tree**: every decision branches into the decisions that hang off it. The **frontier** is every decision whose prerequisites are settled: the questions you can ask now without guessing at answers you haven't heard.

Facts are your job; decisions are the user's. When a frontier question needs a fact from the environment (code, files, docs, tools), dispatch a subagent in the background to find it, record the lookup on the board, and keep asking the rest of the frontier while it runs.

## Rounds

A **round** is one AskUserQuestion call holding up to 4 frontier questions. The UI shows them one at a time and moves to the next instantly, so the user waits on you only between rounds, which is exactly where their answers reshape what comes next.

- Every question in a round stands on its own: a question whose answer depends on another question in the same round belongs to a later round.
- When the frontier holds more than 4, take the 4 whose answers unblock the most of the tree.
- A challenge goes in a round of its own, as the only card.

Keep the turn between rounds **lean**: the user is waiting through every token you write.

## Board

The board is one artifact page, titled `Grill Board`, that shows every grilling session live on web and mobile. You write to its database with the ArtifactData tool; the page derives the frontier, what's waiting, what's deferred and the counts itself. When the Artifact or ArtifactData tool is unavailable, keep a Markdown log instead, following [LOG.md](LOG.md), everywhere this skill says board.

**Find or create it** once per session: Artifact `list` with `limit: 200` and take the artifact titled exactly `Grill Board`. If there is none, copy `board.html` from this skill's base directory into your scratchpad, read it, and publish that copy with `icon: "checklist"` and `capabilities: {"db": {"rules": [{"path": "", "read": "view", "write": "owner"}]}}`.

**Write creates only**: until Done, every board write is an `op: "set"` on a new document id, sent in one ArtifactData `batch` per round, with no `if_version`. A change is a new document, never an edit:
- an answer revised by `back to Q<n>` or by a challenge: a new answer with the same `key`;
- `skip`: an answer with `kind: "deferred"`;
- a branch the answer closed: its key in that answer's `closes`.

The documents, with `<sid>` = `<YYYY-MM-DD>-<topic-slug>`:

| Collection | Id | Fields |
|---|---|---|
| `sessions` | `<sid>` | `topic`, `started` (`YYYY-MM-DD`), `status: "active"` |
| `sessions/<sid>/items` | a short slug key (`lk-` prefix for lookups) | `kind: "question" \| "lookup"`, `text`, `needs` (keys of the decisions or lookups it waits on; empty means frontier), `round` |
| `sessions/<sid>/answers` | `q01`, `q02`, … | `n`, `key` (the item it answers), `title`, `answer`, `why` (their "Other" text or annotation notes, else your one-line reason), `took` (true when they picked your recommendation), `kind: "decision" \| "challenge" \| "deferred"`, `closes`, `round` |
| `sessions/<sid>/facts` | `f01`, `f02`, … | `text`, `source` (URL or `path:line`), `resolves` (lookup keys it answers) |

Add an item for each question when it enters the tree, frontier or not, and for each lookup when you dispatch it.

## Loop

1. **Open the session.** Find or create the board. Then, in one message, send a batch with the session document and the first items alongside the first round (step 2), and give the user the board link in one line (the first time, suggest pinning it).
2. **Ask the round.** One AskUserQuestion call; each question is a card:
   - `header`: `Q<n> <topic>`, 12 characters max. Number questions in the order asked.
   - `question`: the decision, self-contained, ending in `?`. Carry the context the user needs inside it, in three sentences or fewer.
   - `options`: 2–4 concrete answers, your recommendation first with its label ending ` (Recommended)`. Each `description` is the trade-off in one sentence. The user's free-text "Other" box is added for you, so every option is a real answer.
   - `preview`: only when comparing code, layouts, or wording side by side.
   - `multiSelect: true` only when answers genuinely combine.

   When a question has no sensible candidate answers (a name, a number, a free description), or AskUserQuestion is unavailable, ask it in plain text instead: the question, then `➡️ <your recommended answer>`, then wait. Plain-text questions go one per turn.
3. **Record and ask on, in one message.** Send the round's board batch (an answer per question, items for newly opened questions, facts that arrived) and the next round's AskUserQuestion call (cards as in step 2) as parallel tool calls in the same message, so the board never delays the next card. Write chat text only when a challenge or a new fact needs explaining. Challenge an answer only when it contradicts an earlier decision or a recorded fact.
4. **Repeat** from step 3 until **Done**.

## User controls

The user steers by typing in a card's "Other" box or in chat:

- `skip` / `later`: defer that question; continue.
- `back to Q<n>`: reopen that decision and re-derive the branches it fed.
- `one at a time`: shrink rounds to a single question each, so every answer can reshape the next card; `rounds` switches back.
- `stop`: jump to **Done** with what is settled.

## Done

The interview is done when nothing is left to ask: the frontier is empty, no fact lookup is running, and the user has answered or accepted as open every deferred question (along with whatever waits on it). Then `get` the session document and `update` it, pinned to its version, with `status: "done"` and `summary` (the plan as now specified, in a few bullets, plus any accepted open items). Show the summary in chat, ask the user to confirm the shared understanding, and offer once to also save the session to the repo as `docs/grill/<sid>.md` in the [LOG.md](LOG.md) format. Act on the plan only after they confirm.
