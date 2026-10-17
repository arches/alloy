---
name: Glossary
slug: glossary
aka: [ubiquitous language, domain dictionary, term list]
discovery: ["*.glossary.md", "GLOSSARY.md"]
slots:
  core:
    term: "What is the one spelling of this term that code should use?"
    definition: "Define it in one sentence, without using a synonym for it."
    not-confused-with: "What names does this replace, or get mistaken for?"
  optional: [context, example, counter-example, code-binding, status]
---

## What it is

A glossary is the list of terms your team has agreed on for the things in your domain: one canonical spelling each, one sentence of definition, and the names it replaces. It isn't documentation _about_ the system; it's the naming authority _for_ the system. The names here are the names used in conversation, in the other artifacts, and in the code. When the business says a subscription _lapses_, the code has `Subscription.lapse()`; the glossary is what makes those the same word.

It earns its keep on contested terms, not on every noun. "Customer," "user," and "account" all in play for one thing. "Order" meaning a sales order to one team and a picking order to another. Those collisions are invisible in conversation (everyone nods and means something slightly different), and they surface later as two tables that should have been one, or an integration mapping the wrong field. A glossary is cheap enough to make the disagreement surface in a sentence instead of in a migration.

**Use when** a second artifact or a second person starts naming things: the moment two descriptions of the system can disagree about a word.

**Not when** the argument is about the shape of the data rather than agreement on its name. Use a schema for that, or a domain model when what you need is the structure and relationships between the things themselves.

## Glossary or schema?

The glossary says a _lapsed_ subscription is one whose paid period ended without renewal. A schema says `subscription` has a `status` of a given enum, a required `period_end`, and at most one `renewal`. Both are naming authorities, which is the overlap, but they settle different questions: _what do we call this and what does the word mean_ versus _what does a valid instance look like_.

Two tests. If the argument is whether to say **user** or **customer**, it's a glossary. If the argument is whether `email` is required and whether there can be more than one, it's a schema. And many terms have no schema at all (a role, a policy, a lifecycle state have no fields), while plenty of schemas describe structures whose names nobody disputes.

**Folding the glossary into a schema.** If the schema is already the artifact your team actually reads, and the contested terms map onto its fields, putting the definitions there is a defensible call: JSON Schema `description`, protobuf comments, `COMMENT ON COLUMN`. The definition then sits beside the identifier it governs, which solves drift for free. Know what you give up:

- **Retired aliases.** A schema records the name that won; it has no slot for the names that lost. That's _Not to be confused with_, the slot the contradiction check below runs against.
- **Terms that aren't data.** Roles, events, policies, states. No field to hang them off.
- **One word, two senses.** A field comment can't record "this is two terms in two contexts", which is the exact finding a glossary exists to hold.
- **Non-technical readers.** Nobody settles a vocabulary argument inside a `.proto`.

If none of those bite, fold it in. If two or more do, a separate glossary is paying for itself.

## How you build one

You harvest it; you don't author it. Listen for the nouns already in use (tickets, meetings, existing schemas and code) and write down the ones where people hesitate, correct each other, or use two words for one thing. For each, get a ruling: the spelling code will use, a definition that doesn't lean on a synonym, and the aliases it retires. Keep it to the terms people actually get wrong; once it grows past a few dozen entries it has stopped being a set of decisions and become a dictionary nobody opens.

Expect to discover, while doing this, that a word means two different things in two parts of the business. That's a finding, not a problem to fix: it means you have two bounded contexts, and two terms.

## How you use it

- **When writing another artifact.** The glossary is a tie-breaker, not a checklist. A name in a state machine or scenario doesn't need an entry, but if it _contradicts_ one, by using a retired alias or a term in a sense the definition doesn't support, that's a defect in the artifact. Authoring is also where you find new contested terms: the moment you hesitate between two words, you've found one.
- **When naming things in code.** Identifiers come from here. That's what keeps the agreed word and the shipped word from drifting.
- **When a borderline case comes up.** Rule on it once, record it as an example, and it stops getting re-argued.

## Required slots

- **Term**: the single canonical name, in the spelling code will use.
- **Definition**: one sentence, applicable by someone outside the team. A definition that is a synonym is not a definition.
- **Not to be confused with**: the names this term replaces or is mistaken for, so a stale name can be flagged on sight.

## Optional slots

- **Context**: the bounded context where the term holds. The same word meaning two things in two contexts is two terms, not an ambiguity to resolve.
- **Example and counter-example**: one borderline case with a ruling, so it isn't re-argued.
- **Code binding**: the type, table, or field name the term maps to.
- **Status**: `current` | `deprecated` | `superseded by <term>`.

## Notation & naming

Text only; no visual form. A three-column table is enough:

| Term | Definition | Not to be confused with |
| ---- | ---------- | ----------------------- |

File naming: `<context>.glossary.md` where a project has multiple bounded contexts, `GLOSSARY.md` at repo root where it has one. A CSV or spreadsheet with the same three columns is equivalent; use whichever one people will actually keep up to date.

Because the glossary holds only contested terms, it can't be checked for coverage: a name absent from it is usually just a name nobody has argued about. What it can be checked for is contradiction, a name in another artifact that appears here as a retired alias or is used in a sense the definition doesn't support. That check runs against _Not to be confused with_, which is why that slot is required and not optional.

## Common gaps

- Circular definitions ("Customer: a client of the business").
- No retired aliases, so the superseded name keeps leaking back into code and conversation, and nothing can be checked against the glossary at all.
- Definitions written by one person and never actually ruled on, so the artifact records an opinion rather than an agreement.
- Terms defined but never bound to code, so the agreed name and the shipped identifier drift.
- Never revisited when the business renames something, leaving the glossary authoritative and wrong.

## Further reading

- Evans, _Domain-Driven Design_ (2003), ch. 2, Ubiquitous Language.
- Vernon, _Implementing Domain-Driven Design_ (2013), on context-local meaning.
- arc42 §12, Glossary: the same artifact as a named template section.
