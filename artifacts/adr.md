---
name: Architecture decision record
slug: adr
aka: [ADR, decision record, design decision record]
discovery: ["**/adr/*.md", "**/decisions/*.md", "*.adr.md"]
slots:
  core:
    title: "State the decision as a decision, in a few words."
    status: "Is this proposed, or already accepted?"
    date: "When did the status last change? (Infer today's date if this is new.)"
    context: "What forces are in tension? Write it so the decision still looks hard. If you did research or held a meeting to decide, what you learned there goes here."
    decision: 'One sentence, active voice: "We will ..."'
    options-considered: "What else did you seriously consider, and why did each one lose? If nothing else was ever on the table, say so."
    consequences: "What gets easier, and what gets harder? Both halves."
  optional: [decision-drivers, deciders, confirmation, scope]
---

## What it is

An architecture decision record is one file per decision, written when the decision is made, saying what was decided, what situation forced it, and what it costs. Records are immutable. When a decision changes you write a new record that supersedes the old one, and the old one keeps its body exactly as it was.

The decision itself is the least valuable part, because anyone can read the code and see what was chosen. What is unrecoverable later is why the obvious alternative was wrong and which constraints applied at the time. Code records the choice; an ADR records the reasoning.

So two things carry the weight. The context has to be written so the decision still looks hard: if a competent reader could not reach a different conclusion from your context alone, you reverse-engineered it from the answer and the record is worthless. And the consequences have to include what got worse. Every real decision costs something, and a record listing only benefits tells the future reader nothing about what to watch for.

Without a record, a decision fails in one of two directions. Either it gets silently re-litigated, when someone sees the odd choice, assumes it was an accident, fixes it, and rediscovers the constraint the hard way. Or it calcifies, because nobody knows why it was made and nobody will change what they cannot explain. A record is what lets a decision be revisited on purpose, by letting you ask whether the forces that produced it still hold.

An ADR rarely does anything by itself. Accepting a decision usually means editing several other documents at once: the worker shows up wherever you describe your components, the queue wherever you describe your topology, the retry rule wherever you state your policies. Those edits are the action, and the record is the explanation attached to them. Expect to write one alongside other changes rather than instead of them.

**Use when** a choice closes off alternatives and someone will later ask why. The sharpest signal is that you had to do research or hold a meeting to reach it.

**Not when** you would simply delete the thing once it stopped applying. That is a directive, a choice made in the moment and written down so the next build honors it, and it carries a one-line reason rather than a rationale.

## ADR or your PR history?

The reasoning is often already in your git history, in PR descriptions and review threads, and "the commit log is my decision record" is a real position. Two things are wrong with it. A PR is indexed by the change it made, so finding a decision means knowing which diff carried it, and the reasoning sits interleaved with review noise about naming and test coverage. And a PR answers "why this change," whereas the question that comes back a year later is "why this approach, and what was wrong with the obvious one." That second question is usually absent from the thread, because at review time everybody already knew.

Most decisions do leave a trail. Choosing Celery puts a worker in your component diagram and a queue in your topology, so the change is visible in several places at once. But each of those describes the world you chose. None of them shows the cron-driven poller you rejected, or that the choice was forced by already running Redis. Recording the alternatives is the one job nothing else does.

A minority of decisions leave no trail anywhere, and those are the clearest case for the type. Deciding not to adopt something, accepting a constraint, or deciding to keep doing what you already do produce no diff and no artifact edit, and exist precisely to stop the question being reopened.

## How you build one

Write it when the decision is made. A record reconstructed three months later smooths over the uncertainty that was the most valuable thing in it.

Write the context before you write the decision, because that is what forces you to notice when the decision does not actually follow from the situation. If you did research to get here, the findings are the context, and they are the part a later reader cannot reconstruct. Then one decision per file, numbered, never renumbered, and keep it short: the original form is five sections and fits on a page. For small decisions there is a one-sentence version, _in the context of this use case, facing this concern, we decided on this option and rejected those, to achieve this, accepting this downside._

## How you use it

- **When the choice gets questioned.** Read the context and ask whether those forces still hold. That is the whole purpose, and it is why the context section is worth more than the decision section.
- **As a standing constraint on what gets built.** "We will use optimistic locking" is not a historical note, it governs every write path from then on. The consequences are the part that must not get violated.
- **When reversing it.** Supersede, never edit. The new record cites the old, the old record's status changes, and its body stays untouched, so the pair reads as a history of how the thinking moved.

## Required slots

- **Title**: the decision stated as a decision, under an immutable number.
- **Status**: `proposed` | `accepted` | `superseded by <id>` | `deprecated`.
- **Date**: when the status last changed.
- **Context**: the forces in tension, written so the decision still looks hard. No conclusion in this section.
- **Decision**: one sentence, active voice, "We will ...".
- **Options considered**: each with the reason it lost, or an explicit note that there was no alternative.
- **Consequences**: what becomes easier _and_ what becomes harder. Both halves are required.

## Optional slots

- **Decision drivers**: the forces that actually forced the choice, measurable where possible.
- **Deciders and consulted**: who, not as credit but so a reader knows whom to ask.
- **Confirmation**: how you would know the decision was actually implemented.
- **Scope**: what this constrains, such as the components, contracts, or topology it governs.

## Notation & naming

Text only. One decision per file, immutably numbered: `NNNN-short-title.md` under `docs/adr/` or `docs/decisions/`.

Records are append-only. A decision that changes gets a new record superseding the old one by number. The superseded file's Status is the only line that may change, and its body never does. This is the rule most often broken, and breaking it costs the whole point of the artifact, which is the record of what was believed, and on what evidence, at the time.

## Common gaps

- No rejected options, which is the one thing no other artifact records.
- Edited in place rather than superseded, which destroys the record of what was believed when.
- Status never updated, so superseded records read as current.
- Written for decisions nobody would question, diluting the set until no one reads any of it. If you did no research and held no meeting, this is probably a directive.

## Further reading

- Nygard, "Documenting Architecture Decisions" (2011): the original five-section form.
- MADR, _Markdown Any Decision Records_: the fuller template, with considered options.
- Zimmermann et al. on Y-statements: the one-sentence form.
- ISO/IEC/IEEE 42010, which requires recorded architecture rationale in a conforming architecture description. ADRs are the common way to satisfy it.
