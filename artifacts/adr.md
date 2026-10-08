---
name: Architecture decision record
slug: adr
aka: [ADR, decision record, design decision record]
related: [quality-attribute-scenario, component, deployment, context]
formats: [text]
viewpoints: []          # cross-cutting; a decision may affect any view
perspectives: []        # a decision names the perspectives it trades against
frames: [decision-rationale, rejected-alternatives]
level: system
discovery: ["**/adr/*.md", "**/decisions/*.md", "*.adr.md"]
---

**Use when** a choice closes off alternatives and someone will later ask why — a technology
selection, a boundary placement, a tradeoff accepted deliberately.

**Not when** it states a requirement rather than a choice — use a scenario set, or a quality
attribute scenario if it's a measurable one.

## Required slots
- **Title** — the decision stated as a decision, under an immutable number.
- **Status** — `proposed` | `accepted` | `superseded by <id>` | `deprecated`.
- **Date** — when the status last changed.
- **Context** — the forces in tension, written so the decision still looks hard. No conclusion
  in this section.
- **Decision** — one sentence, active voice: "We will …".
- **Consequences** — what becomes easier *and* what becomes harder. Both halves are required.

## Optional slots
- **Options considered** — each with the reason it lost.
- **Decision drivers** — the quality attribute scenarios that forced the choice.
- **Deciders and consulted** — who, not as credit but so a reader knows whom to ask.
- **Confirmation** — how we would know the decision was actually implemented.
- **Affected artifacts** — the components, contracts, or topology this changes.

## Notation & naming
Text only. One decision per file, immutably numbered: `NNNN-short-title.md` under `docs/adr/`
or `docs/decisions/`.

Records are **append-only**. A decision that changes gets a new record superseding the old one
by number; the superseded file's Status changes and its body never does. This is the slot most
often broken, and breaking it costs the whole point of the artifact — the record of what was
believed, and on what evidence, at the time.

For small decisions the Y-statement compresses every required slot into one sentence: *In the
context of `<use case>`, facing `<concern>`, we decided for `<option>` and neglected
`<alternatives>`, to achieve `<quality>`, accepting `<downside>`.*

## Common gaps
- Context written backwards from the conclusion, so the decision reads as obvious. The test:
  could a competent reader reach a different decision from the context alone?
- Consequences listing only benefits. The "harder" half is the half that pays off in two years.
- No rejected options — the one thing no other artifact type records.
- Edited in place rather than superseded.
- Status never updated, so superseded records read as current.
- Written for decisions nobody would question, diluting the set until no one reads it.

## Further reading
- Nygard, "Documenting Architecture Decisions" (2011) — the original five-section form.
- MADR, *Markdown Any Decision Records* — the fuller template, with considered options.
- Zimmermann et al. on Y-statements — the one-sentence form.
- ISO/IEC/IEEE 42010, which requires recorded architecture rationale in a conforming
  architecture description; ADRs are the common way to satisfy it.
