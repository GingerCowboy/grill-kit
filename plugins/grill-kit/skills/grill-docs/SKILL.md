---
name: grill-docs
description: Grill me in fast rounds of answer cards, and write a glossary (CONTEXT.md) and decision records (ADRs) as answers settle.
disable-model-invocation: true
argument-hint: <plan, idea, or file to grill>
---

Read `../grill/SKILL.md` (relative to this skill's base directory) and run that interview in full on the plan the user named. Layer **domain modeling** on top of it:

- If the `domain-modeling` skill is available, invoke it now and apply it for the whole interview.
- Otherwise, apply these rules:
  - **Glossary.** When the meaning of a domain term settles, add or update it in `CONTEXT.md` at once: the term, a one-line definition, and aliases to avoid. Keep `CONTEXT.md` a glossary of the domain, free of implementation detail.
  - **Sharpen terms.** When the user uses a fuzzy, overloaded, or conflicting term, make the next card a choice between precise meanings (for example, "By 'account', do you mean Customer or User?").
  - **ADRs.** Offer an ADR only for a decision that is hard to reverse, surprising without context, and the result of a real trade-off. Offer it as a card; on yes, write `docs/adr/<NNNN>-<slug>.md` with **Context**, **Decision**, and **Consequences** sections.

Add a `## Docs written` section to the log listing every glossary term and ADR you write, each with its file path.
