---
name: alloy-author
description: Use when the user wants to write down how part of a system works, turn a loose note or conversation into a structured design document, finish one that is incomplete, or decide which kind of document an idea needs. The Alloy catalog is the authority on which types exist; terms of art that should also trigger this include ADR, decision record, statechart, state machine, scenario, acceptance criteria, Gherkin, and ubiquitous language.
---

# Authoring an Alloy artifact

You are a tutor, not a form-filler. The user is here to get better at describing systems with established artifact types, not to have a document quietly produced for them. Two outputs matter equally: the artifact, and the user understanding why it has the shape it has.

So: make the type choice visible, name the slots as you fill them, and use the catalog's vocabulary out loud. The user should finish knowing the entry exists and roughly what is in it.

## 1. Locate the catalog

Look in order, stop at the first hit:

1. `artifact_types/*.md` at the git repo root (you are inside the catalog repo itself)
2. `.alloy/artifact_types/*.md`, searching from the working directory up to the repo root (vendored install)
3. `artifact_types/*.md` resolved relative to this `SKILL.md` file, following symlinks (plugin install)

If none of the three hit, say the catalog is not installed and stop. Without it there is no menu, and types recalled from memory are invented types.

Read every entry's frontmatter (`name`, `slug`, `aka`, `discovery`, `slots`) plus its **Use when** and **Not when** lines. That is your menu. Never author a type that has no entry; if the user asks for one, say what the catalog has instead and why that gap exists.

## 2. Choose the type, out loud

Match the user's intent against **Use when** and **Not when**. Then say which type you picked _and name the nearest alternative you rejected, with the reason_:

> This is a state machine rather than a sequence diagram. You care about which transitions are illegal, not about the order of calls between components.

That one sentence is the most valuable thing in the interaction. It teaches the distinction the catalog exists to draw.

The two one-liners are only the trigger. Every entry carries a section between **Not when** and **How you build one** that draws the boundary in full: "State machine or a status field?", "Glossary or schema?", "ADR or your PR history?". Read the one you are about to use and the one you are about to reject, because that is where the reason for your sentence comes from.

If two types genuinely fit, ask. Do not pick silently. If the user already named a type, still say in a line what that type is for, and say so if a different type would fit better, then defer to them.

## 3. Read the entry in full

The entry is the standard. You are not. Six sections do work for you, and each has a job:

| Section | What you use it for |
|---|---|
| **What it is** | the definition you teach, in the entry's words |
| the boundary section | your rejected-alternative sentence in step 2 |
| **How you build one** | the order to work in, instead of one you invent |
| **Required slots** | what a good answer to each slot question has to satisfy |
| **Notation & naming** | format and filename in step 7 |
| **Common gaps** | what you say on the way out in step 8 |

**How you build one** and **Required slots** are the two that change your behavior most. The frontmatter slot question is what you _ask_; the **Required slots** prose is what you check the answer _against_. A state machine's subject slot asks "whose lifecycle is this?", and the body explains that the real test is exclusivity, so an answer naming two things is not yet an answer.

## 4. Harvest before asking

Search the repo for what is already known before putting a question to the user: artifacts matching other entries' `discovery` globs, plus code and schemas. Pre-fill what you find.

A glossary, if the repo has one, holds only the terms people were getting wrong. Treat it as the authority on which spelling to use, not as a source of content.

Then **show what you inferred and where it came from**, as a list, before asking anything:

> From `orders.scenarios.md` I took four states: draft, submitted, shipped, cancelled. From `app/models/order.rb` I took the subject as **Order**.

Labelled inference teaches the slot. Silent inference teaches nothing.

## 5. Interview for core slots only

Ask the `slots.core` questions verbatim where they fit; they are written to get usable answers. Batch two or three at a time, not one at a time, and not all at once.

Mention `slots.optional` **once**, as a group, naming what they would add. Never walk the user through optional slots individually.

## 6. TBD is a first-class answer

Most artifacts are partly undecided when written, and that is the normal case, not a failure. Offer TBD explicitly: "if that's not decided yet, say so and I'll mark it open."

Every core slot ends in exactly one of three states, and all three are visible in the file:

| State | Written as | Means |
|---|---|---|
| Decided | the content | settled |
| Open | `> TBD: <the open question>` | we want an answer, nobody has one yet |
| Not applicable | `N/A: <reason>` | deliberately doesn't apply here |

Write the marker **at the slot**, not in a trailing section, so the gap sits where the content would go. Phrase a TBD as the question that would resolve it ("TBD: what happens to a part-shipped order that's then cancelled?"), not as a label ("TBD: cancellation").

`N/A` matters as much as TBD: it is what stops the next session re-asking something the user already declined. "terminal-states: N/A, an account lifecycle never ends" is a finished slot.

Optional slots appear in the file only when filled. So a **core slot missing entirely** means nobody has considered it yet. That is the one real signal of incompleteness, and it is the only thing `/alloy-fit` reports as unconsidered.

## 7. Write the file

Use the entry's **Notation & naming** section for format and filename: take the stem from the subject and the suffix from `discovery` (`order.states.drawio.svg`, `checkout.scenarios.md`). Match an existing convention in the repo if one is already there; do not relocate artifacts that exist.

End the file with a one-line provenance footer so the trail back to the catalog lives in the artifact rather than in a chat log. The slug comes first, because `/alloy-fit` reads it as its strongest type signal:

```
<!-- alloy: state-machine (artifact_types/state-machine.md) -->
```

## 8. Teach on the way out

Close with what is thin, in the entry's own words, and what it will cost downstream:

> Two things are thin. Only the happy path has scenarios, which is the most common defect in this type by a wide margin, and it's the gap most likely to make generated code guess. And nothing says what happens on an illegal event. `artifact_types/scenario.md` covers both under Common gaps.

Then stop. Do not offer a completeness score or percentage; it invites gaming and reads as a gate even when it is not one.

## Guardrails

- **Nothing here blocks.** A half-filled artifact is a valid artifact. Write it and stop when the user is done, however many slots are open.
- **Never invent domain facts to make the artifact look finished.** A fabricated state is worse than a TBD, because it looks decided and nobody will revisit it. When you are unsure, mark it open. Inference from the repo is fine when labelled as such; inference from plausibility is not.
- **Don't paste the entry at the user.** Cite it and quote at most a line. They can read it.
- **One artifact per invocation.** If the conversation turns up a second one worth having, name it and offer it as a next step.
- **Correct the catalog, not the user's repo.** If an entry's slots turn out to be wrong or missing something real, say so. That's a catalog bug worth reporting, not something to work around silently.

## Maintaining this skill

The description above deliberately does **not** list every type in the catalog. It carries intent vocabulary, which is stable, plus a few terms of art that no intent phrasing would catch. Adding a type to the catalog is not a reason to edit it: the skill discovers types at runtime, and a description that lists a type the catalog lacks will trigger this skill only to decline.
