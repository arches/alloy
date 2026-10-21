---
name: Data model
slug: data-model
aka:
  [
    entity relationship diagram,
    ERD,
    entity diagram,
    logical data model,
    domain model,
  ]
discovery: ["*.data.md", "*.data.mmd", "*.data.d2", "*.data.drawio.svg"]
slots:
  core:
    entities: "What things does the system store? Name each in the domain's words and say in one sentence what it is."
    attributes: "For each entity: what does it hold? Give every one a semantic type, and say whether it is optional."
    identity: "What makes one of these a different thing from another, in the domain's terms? Not the surrogate key."
    relationships: "For each pair that relates: how many of each, and is the relationship optional on either side?"
    invariants: "What must always be true that the attributes and relationships do not already say?"
  optional: [ownership, enumerations, derived, history, retention, volume]
---

## What it is

A data model says what the system stores: the things, what each one holds, how they relate, and what must always be true about them. It sits at the logical level, which means no column types, no indexes, and no migration order. Those are generated.

One discipline carries the artifact: **every attribute has a semantic type, and a sentence is not a type.** "A name" is not a type; `short text` is. "The price" is not a type; `monetary amount in GBP` is. "The video" is not a type; `YouTube video id` is. This is the whole difference between a sketch that reads as finished and a model something can be generated from, and it is mechanically checkable, because an attribute whose type is a sentence is a defect you can find without understanding the domain.

The division of labor with the database is just as strict. **The model says what must be true. The migration says how the database enforces it.** That a restaurant's YouTube video id is unique belongs here; the unique index does not. That a visit belongs to exactly one restaurant belongs here; the foreign key and its `ON DELETE` behavior are derived from the ownership statement. That an amount is money in GBP belongs here; `numeric(10,2)` does not.

Because the schema is generated, this is the only place the structure is decided.

**Use when** the system stores anything at all, which is almost immediately, and before any code exists to store it.

**Not when** the disagreement is about what a word means rather than about what is stored. Use a glossary.

## How you build one

1. List the things, in the domain's words. A Visit, not a `visits` row. If you cannot write one sentence saying what a thing is, you do not yet know whether it is an entity or an attribute of one.
2. For each, list what it holds, and give every attribute a semantic type before you give it anything else. Mark the optional ones.
3. Say what identity means. For most entities this is a natural key such as a YouTube video id or an email address, and it is not the surrogate id you will generate.
4. Draw the relationships with both numbers: how many on each side, and whether zero is allowed on either side. A relationship recorded as "one to many" when it is really "zero-or-one to many" produces a wrong constraint.
5. Write the invariants last, because they are the rules that no single attribute or relationship can hold, and they only become visible once the rest is on the page.

### Semantic types worth having

A small named vocabulary beats free text, because it is what makes generation deterministic and review mechanical. Most systems need a handful of these and should invent a few of their own.

| identifier                    | short text         | long text           |
| ----------------------------- | ------------------ | ------------------- |
| email address                 | URL                | timestamp with zone |
| date                          | duration           | boolean flag        |
| monetary amount with currency | quantity with unit | enumerated value    |
| external reference            | file reference     | structured document |

Two of these carry warnings. **Enumerated value** is incomplete without the values listed, and when the entity has a lifecycle those values are the state machine's states and must match. **Structured document**, meaning a JSON or blob column, is the escape hatch, and a model with several of them is usually a model where the work was deferred rather than done.

## How you use it

- **Generating the schema.** Migrations, types, and query layers all derive from here, which is the reason the semantic types have to be precise rather than descriptive.
- **Checking the live database.** The generated schema and the real one should agree. A divergence is either a hand-edited migration or a stale model, and finding out which is usually five minutes well spent.
- **Cross-checking a lifecycle.** When an entity has a state machine, the states and this model's enumerated values are the same list written twice. Disagreement between them is a defect in one or the other, and it is the cheapest check in the set.
- **Settling scope.** A requested feature that needs a new entity is a different size of change from one that needs a new attribute, and that is visible before any code exists.

## Required slots

- **Entities**: the things stored, each named in the domain's words with a one-sentence meaning. The sentence is doing real work: it is what distinguishes an entity from an attribute that grew too big, and an entity nobody can describe in a sentence is usually two things or none.
- **Attributes**: what each entity holds, every one with a semantic type and an optionality. Optionality is the half most often skipped, and skipping it means everything reads as required until the first nullable column surprises somebody.
- **Identity**: what makes one instance a different thing from another, in domain terms. A surrogate id answers this for the database and not for a person, so this slot holds the natural key: the email address, the video id, the combination of date and account. Entities with no natural identity exist, and saying so explicitly is a finished slot rather than an empty one.
- **Relationships**: for each related pair, the cardinality on both sides and whether zero is permitted on either. "One to many" without the optionality is the most common imprecision here, and it is the one that generates a wrong constraint.
- **Invariants**: the rules that span attributes or entities, which neither a type nor a cardinality can express. A visit cannot predate the restaurant that was visited. A published video must have a blurb. These are where the bugs live, because they are the rules nothing enforces by accident.

## Optional slots

- **Ownership**: which entity owns which, in lifecycle terms. "Deleting a restaurant deletes its visits" is a domain statement, and the cascade behavior is generated from it.
- **Enumerations**: the permitted values for each enumerated attribute. Required in practice the moment you have one, and left optional only because plenty of models have none.
- **Derived**: attributes computed from others, with the rule. Worth stating so a generator stores them deliberately or not at all, rather than guessing.
- **History**: which entities need their past kept, and at what granularity. Expensive to retrofit and cheap to decide now.
- **Retention**: how long each entity is kept, and what happens at the end. Fill this when a regulator or a privacy policy has an opinion.
- **Volume**: expected row counts and growth, where they are extreme enough to change the design.

## Notation & naming

**Mermaid**: `erDiagram`; entity blocks for entities, with `type name` per attribute and `PK`, `FK`, or `UK` markers; crow's-foot relationships where the left and right symbols carry cardinality and optionality (`||--o{` is exactly one to zero-or-more). Semantic types go where the type goes. File: `<subject>.data.mmd`, or a fenced block in `<subject>.data.md`.

```
erDiagram
    RESTAURANT ||--o{ VISIT : "was visited on"
    VISIT ||--o| NOTE : "carries"
    RESTAURANT {
        identifier id PK
        short_text name
        short_text neighbourhood UK
        date added_on
    }
```

**D2**: `shape: sql_table`; one key per attribute with its semantic type as the value, and `{constraint: primary_key}`, `foreign_key`, or `unique` where it applies; arrows between tables labeled with the cardinality. File: `<subject>.data.d2`.

**draw.io**: box per entity holding its attributes, crow's-foot ends on the relationship lines, cardinality labeled at both ends. File: `<subject>.data.drawio.svg`. Turn compression off, otherwise the model is a base64 blob and no change to it is reviewable.

**Tabular**: one table per entity with attribute, semantic type, optional, and meaning as columns; one table of relationships with both cardinalities; a list of invariants. File: `<subject>.data.md`.

A diagram makes the shape obvious but has nowhere to put an invariant, which is the slot most likely to contain the thing that breaks. The tables hold everything and make the shape hard to see. Keep both if you like, but the invariants need a home either way.

## Common gaps

- **Attributes with no type, or a type that is a sentence.** The defining failure of a schema written as prose. It reads as a finished model and cannot be generated from, and nobody notices until the generator invents a column.
- **No optionality anywhere.** Everything appears required, so the first nullable column arrives as a surprise and usually as a migration nobody planned.
- **Cardinality without optionality.** "One to many" recorded for a relationship that is really zero-or-one to many, which produces a constraint that rejects legitimate data.
- **Identity that is only a surrogate id.** Nothing says what makes two rows the same thing, so deduplication, imports, and idempotency all become guesses later.
- **Invariants missing entirely.** The rules that span entities are the ones no column can hold and no type can imply, and a model without any has almost certainly not asked the question.
- **Enumerated attributes with the values unlisted**, and where the entity has a lifecycle, values that disagree with the state machine.
- **A structured-document column standing in for modeling that was deferred.** One is an escape hatch; three is a model that stopped early.
- **The model and the live schema having quietly diverged**, with nothing in the build that would have noticed.

## Further reading

- **Peter Chen**, "The Entity-Relationship Model: Toward a Unified View of Data" (1976): where entities, relationships, and cardinality come from, and still the clearest statement of why the structure is worth separating from the storage.
- **ANSI/SPARC**, the three-schema architecture: the conceptual, external, and internal levels. This entry sits at the conceptual-to-logical level deliberately, and knowing the split is what tells you which details belong in the generated output.
- **Rozanski and Woods**, _Software Systems Architecture_: the Information viewpoint, which covers more than structure, including ownership, latency, consistency, and archiving. Several of the optional slots here are its concerns. Their companion site is viewpoints-and-perspectives.info.
- **Eric Evans**, _Domain-Driven Design_: worth reading for the distinction this entry's `aka` papers over. A domain model carries behavior as well as structure, where a data model carries only structure. When people use the terms interchangeably, this is what gets lost.
- **David Hay**, _Data Model Patterns: Conventions of Thought_: for when an entity feels wrong and you cannot say why. Most domains have been modeled before.
