---
name: Glossary
slug: glossary
aka: [ubiquitous language, domain dictionary, term list]
related: [entity, state-machine, scenario, interface-contract]
formats: [tabular, text]
viewpoints: [information]            # cross-cutting; other artifacts validate against it
perspectives: []
frames: [term-definition, naming-authority]
level: system
discovery: ["*.glossary.md", "GLOSSARY.md"]
---

**Use when** a second artifact or a second person starts naming things — the moment two
descriptions of the system can disagree about a word.

**Not when** what you need is the structure and relationships between the things rather than
agreement on their names — use a domain model.

## Required slots
- **Term** — the single canonical name, in the spelling code will use.
- **Definition** — one sentence, applicable by someone outside the team. A definition that is
  a synonym is not a definition.
- **Not to be confused with** — the names this term replaces or is mistaken for, so a stale
  name can be flagged on sight.

## Optional slots
- **Context** — the bounded context where the term holds. The same word meaning two things in
  two contexts is two terms, not an ambiguity to resolve.
- **Example and counter-example** — one borderline case with a ruling, so it isn't re-argued.
- **Code binding** — the type, table, or field name the term maps to.
- **Status** — `current` | `deprecated` | `superseded by <term>`.

## Notation & naming
Text only; no visual form. A three-column table is enough:

| Term | Definition | Not to be confused with |
|------|------------|-------------------------|

File naming: `<context>.glossary.md` where a project has multiple bounded contexts,
`GLOSSARY.md` at repo root where it has one.

Terms are the resolution target for every name in other artifacts: entity names, event and
transition names on a state machine, field names in an interface contract. A name appearing in
those artifacts and not here is either a missing term or a typo, and nothing else can tell the
difference.

## Common gaps
- Circular definitions ("Customer: a client of the business").
- No forbidden aliases, so the superseded name keeps leaking back into code and conversation.
- One global glossary covering contexts where a word legitimately differs in meaning.
- No examples, so every borderline case gets re-litigated from scratch.
- Every noun in the system, rather than the contested ones — a glossary earns its keep on
  terms people get wrong.
- Terms defined but never bound to code, so the agreed name and the shipped identifier drift.

## Further reading
- Evans, *Domain-Driven Design* (2003), ch. 2 — Ubiquitous Language.
- Vernon, *Implementing Domain-Driven Design* (2013), on context-local meaning.
- arc42 §12, Glossary — the same artifact as a named template section.
