# Markdown log

Use this log when the board is unavailable, or when the user asks to save a session to the repo.

Write it to `docs/grill/<YYYY-MM-DD>-<topic-slug>.md` and tell the user the path in one line. During an interview, create the file once from this template, then change it with small in-place edits sent in parallel with the next round's card: append each decision and fact, add or delete single frontier lines, update the status line. Write **Summary** once, at Done.

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
