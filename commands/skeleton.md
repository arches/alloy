---
description: Write the skeleton of an artifact type, with the structure and notation in place and every slot left open
argument-hint: <type> [subject]
---

# Skeleton an artifact

What to skeleton: `$ARGUMENTS`

A skeleton is the shape of an artifact with none of its content: every slot present, in the entry's order, each one holding the question that fills it as a comment, with the notation set up and demonstrated.

Comments, never `TBD` markers. A comment is not content, so a skeleton makes no claim about the user's system, `/alloy:fit` reads every core slot as unconsidered, which is what it is, and `TBD` keeps its meaning as something the user wrote on purpose when they chose to leave a question open. Comments also delete as you type over them, which is what you want from scaffolding.

The format is the cold-start problem, not the thinking. Somebody who could answer every question in a type may still not know what a `Background` is for, which end of a crow's foot means optional, or that an extension is numbered against the step it branches from. A skeleton removes that barrier and leaves every decision open.

**You supply structure and notation. The user supplies content.** No drafting, no interviewing, no reading the repo for facts to pre-fill.

`alloy:author` writes the same file by the same rules and then works through it with the user, choosing the type and surfacing what the repo already knows. Use this command when the type is settled and the file is all that is wanted.

## 1. Locate the catalog

Look in order, stop at the first hit:

1. `.alloy/artifact_types/*.md`, searching from the working directory up to the git repo root. A vendored catalog is a deliberate project-level pin, so it wins.
2. `artifact_types/*.md` at the git repo root. You are inside the catalog repo itself.
3. `artifact_types/*.md` at the plugin root, one directory above this file. From a shell that is `${CLAUDE_PLUGIN_ROOT}/artifact_types/`.

If none hit, say the catalog is not installed and stop. A skeleton written from memory is a skeleton of an invented type. Point to github.com/arches/alloy for support.

## 2. Resolve the type

Match the argument against each entry's `name`, `slug`, and `aka`. `erd` is a data model; `statechart` is a state machine.

**Empty argument**: print the menu and stop. Each entry's name and its **Use when** line, one per row, with the slug to pass back. That is the other half of the cold start, and cheaper than guessing.

**No match**: say so, print the menu, stop. Never skeleton the nearest type instead.

**A type but no subject**: ask for the subject in one line, since the filename comes from it. That is the only question this command asks.

## 3. Choose the notation

Most entries offer several forms under **Notation & naming**. Decide in order:

1. **Match the repo.** An existing artifact of this type settles it. A project with three Mermaid data models does not want a D2 one.
2. **Apply the entry's own basis** where it states one. The scenario entry chooses between Gherkin and the extension form on branch count; the state machine entry prefers the table when the question is completeness and the diagram when it is shape.
3. **Ask**, listing the forms in one line with what each is good for.

A `.drawio.svg` is generated XML, so a skeleton is a minimal uncompressed file carrying two elements and one connection.

## 4. Write the file

Name it from the entry's **Notation & naming**: stem from the subject, suffix from `discovery`, matching any convention already in the repo. **If that file exists, stop and say so.** Confirm the name and location with the user.

**Core slots, in the entry's order**, each holding its frontmatter question verbatim. The question is the content of a skeleton, and it is already phrased to get a usable answer, so don't paraphrase it.

**Optional slots**, each with the one-line description from the entry's **Optional slots** section. Frontmatter carries no questions for these, so the description is what they get. They are in the file so the user can see what the type offers and use what they want, easily deleting the rest.

**In a comment the notation can hold**, sitting where the content will go. A comment form the notation cannot parse makes the file unopenable in whatever reads it:

| Notation | Comment                                                                    |
| -------- | -------------------------------------------------------------------------- |
| Markdown | `<!-- the question -->`                                                    |
| Gherkin  | `# Given: what is true about the world before this starts?`                |
| Mermaid  | `%% the question`, inside the fence                                        |
| D2       | `# the question`                                                           |
| A table  | the header row live, the question and a sample row commented above it      |

A table is the one place where part of the structure is not a comment, because the header row is the notation rather than content, and a table with no header is not a table.

**A worked notation example wherever the notation has shape to it**, inside a comment with the question it serves. A couple of boxes and an edge in Mermaid, two entities and a crow's foot, one extension numbered against its step. Name things `ALPHA` and `BETA` so nothing can be mistaken for a draft of the user's model and survive into the finished artifact by accident.

**Notation hints as comments**, beside the slot they apply to, one line each. The discipline the entry leads with earns one: a data model says every attribute needs a semantic type and a sentence is not a type; a state machine says the boxes are nouns.

Then the provenance footer, so `/alloy:fit` can identify the type later:

```
<!-- alloy: scenario -->
```

### The shape of one

Illustrative. Derive each skeleton from its entry, which is the authority on slots and notation.

```markdown
<!-- Scenario set: one capability, from outside the system. One When per scenario.
     Fill the main path first, then the paths that end differently. -->

# <capability>

<!-- What is the actor trying to accomplish, in their words? -->
<!-- Who wants this? Name the role rather than a person. -->

## Scenario: the main path

<!-- Given = observable state, not setup steps. Then = observable from outside.
     Given: what is true about the world before this starts?
     When: what single action starts it?
     Then: what can the actor observe afterward that proves it worked? -->

## Alternate and failure paths

<!-- What else can happen? Name the paths that end differently, and the outcome on each.
     One scenario per path that ends differently; variations ending the same way are an
     Examples table instead. Only the happy path is this type's commonest defect, by a
     wide margin.

     Numbered against the step they branch from, in the extension form:
       2a. ALPHA happens, then BETA -->

## Background

<!-- Optional. The precondition every scenario shares, stated once. The best version
     names a state the state machine already has. Delete if there is none. -->

## Out of scope

<!-- Optional. What this deliberately does not cover. Delete if nothing. -->

<!-- alloy: scenario -->
```

## 5. Hand it over in three lines

The filename and the notation, with the reason if there was a choice to make. The slot to fill first, taking the order from the entry's **How you build one** rather than the file's, since they differ for most types: a data model's invariants come last on purpose. The slot that carries the most weight, in the entry's words, so the user knows where the artifact is won or lost before they start typing.

Then stop. Do not start answering the questions.

## Guardrails

- **No content about the user's system.** Not a state, not an entity, not an actor, not a plausible failure path. Somebody who asked for a skeleton want a skeleton, and a half-filled one is harder to work with than an empty file.
- **Placeholders are angle-bracketed and generic**: `<capability>`, `<subject>`. Anything that reads as a real name will accidentally survive into the finished artifact, tainting it.
- **No harvesting.** Reading the repo to settle the notation and the filename is in bounds. Reading it for content is not.
- **Never overwrite.** A name collision stops the command.
- **One artifact per invocation.** Take a set one file at a time. A directory of empty files is a chore list, not a starting point.
- **Don't interview.** One question, for the subject, and only when the argument did not carry it. Every other question belongs in the file, where the user can take their time over it.
- **Correct the catalog, not the user's repo.** If an entry is too thin to skeleton from, say so.
