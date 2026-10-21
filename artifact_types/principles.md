---
name: Architecture principles
slug: principles
aka: [design principles, engineering policy, policies, guiding principles]
discovery: ["*.principles.md", "PRINCIPLES.md"]
slots:
  core:
    name: "What do you call this rule, in a few words someone could cite in a review?"
    statement: "State the rule in one sentence. What must or must not happen. Not why."
    rationale: "Why does this hold? What goes wrong without it? If the reason comes from outside the project, say whose it is."
    implications: "What does following this cost, and what do you do instead? This is the half a prohibition usually leaves out."
  optional: [revisit-when, scope, enforcement, status, source]
---

## What it is

An architecture principle is a standing rule that applies to decisions nobody has made yet. "Authorization is enforced on the server, never solely in the client" does not describe a choice you made on a Tuesday. It governs every future choice about where a check belongs, including the ones you have not thought of.

The statement is the smallest part. Without a rationale nobody can tell when the rule has stopped applying; without implications it says what not to do and leaves you guessing what to do instead.

The tense is what separates a principle from a decision record. An ADR looks backward: here is what we chose, when, and what we rejected. A principle looks forward: here is what holds for choices not yet made. That is why an ADR has a date and a principle does not.

TOGAF imagines these being agreed by an architecture board, which is not most people's situation and should not put you off. One person setting rules for one system, or for a generator working on their behalf, is the same shape with a smaller agreeing party. What matters is that the rule is written once, in a place that owns it.

**Use when** the same kind of decision keeps coming up and you want the answer to hold without being re-argued each time.

**Not when** you made one choice, in one situation, having weighed alternatives you rejected. Use an ADR.

## Principle or ADR?

The two often get confused, in both directions, and the cost of confusing them is a document full of rules nobody can date and decisions nobody can apply.

Three tests, any of which is decisive:

**Does the date matter?** If a reader needs to know when this was settled in order to use it, it is an ADR. Principles have no useful date, because they were not true only after some meeting.

**Were there alternatives?** An ADR closes off options that were genuinely on the table. A principle usually closes off things nobody proposed, which is precisely why it needs writing down.

**Does the ADR template degenerate?** Write it as an ADR and look at the result. If Options Considered is empty and Decision just restates Context, you have a principle wearing the wrong template.

They are not rivals, and they refer to each other in one direction only. An ADR can cite a principle as a decision driver, and an ADR can change a principle. A principle never records a decision. If you find a principle explaining what you chose and why you rejected the alternative, that content wants to move.

## How you build one

1. Start from the prohibitions you keep repeating. A rule you have written in three places is a principle with no home, and the three phrasings have probably already drifted apart.
2. Write the statement as one sentence. If it needs two, you likely have two principles.
3. Write the rationale. If you cannot, this may be a habit rather than a principle, and habits belong in a style guide.
4. Write the implications: what to do instead, and what following the rule costs. A prohibition without an alternative sends the reader looking for one, and they will find a worse one.
5. If the rule is conditional, write what would satisfy the condition. "Unless there is a demonstrated need" is not a condition, it is a gap, and the party most inclined to find the need met is usually the one interpreting it.
6. Check that someone could reasonably do the opposite. If not, the principle is vacuous and you can delete it.

## How you use it

- **Deciding without re-arguing.** The value shows up on the fifth occurrence, not the first. A principle that has never saved you a conversation probably is not one.
- **As an ADR's decision drivers.** Most good ADRs cite one or two principles in their Context, which is how a record stays short: the reasoning that is shared across decisions lives in one place.
- **In review.** A pull request that violates a principle becomes a conversation about the principle rather than about taste, and either the change is wrong or the principle is out of date. Both outcomes are useful; arguing about preference is not.
- **In generation.** This is the artifact that stops a model reaching for a pattern you have ruled out. A generator has no way to know you do not want a repository abstraction unless something says so.

## Required slots

- **Name**: short enough to cite. "Server-side authorization," not "we should probably make sure that authorization happens on the server." A principle people cannot name is a principle people will not reference.
- **Statement**: one sentence saying what must or must not happen. Resist putting the reason in here; it has a slot of its own.
- **Rationale**: what goes wrong without the rule. This is what lets a future reader judge whether the rule still applies, and it is the slot that distinguishes a principle from a preference. When the reason is external, name the source: a provider's terms, a regulation, a contract. External rationale is still rationale, and knowing it is external tells you that you cannot negotiate with it.
- **Implications**: the consequences of following it, including what you do instead. "Do not introduce row-level security" is half a rule; "authorization is enforced in the server layer instead, and every mutation checks it" is the whole one. This is the slot most often missing and the one that makes the principle usable by someone who was not in the room.

## Optional slots

- **Revisit when**: the condition under which the principle stops applying. Fill this for any rule containing "unless," "until," or "for now." It is the difference between a deliberate simplification and a gap.
- **Scope**: which parts of the system this governs, when it is not all of them. Most principles are system-wide and should say nothing.
- **Enforcement**: how a violation is detected. Some principles are mechanically checkable and belong in a linter or a CI check rather than only in prose, and naming the check is the first step to having one.
- **Status**: active, or superseded by a named ADR. Principles change less often than decisions, but when one changes, the old statement should not simply vanish.
- **Source**: who or what imposes this, when it is not you. A client contract, a platform's terms of service, a regulator.

## Notation & naming

A Markdown file with one section per principle: the name as an `##` heading, the four slots beneath it.

**Statement.** Authorization is enforced on the server. The client may hide what a user cannot do, but it never decides it.

**Rationale.** Anything the browser decides, a user can change. A hidden button is a convenience, not a control.

**Implications.** Every server action verifies the caller before mutating. Client code may be written as though the server will refuse it, because it will. Costs one check per mutation, including the obvious ones.

**Revisit when.** Never for this rule.

A table with the same four columns is equivalent for short principles and worse for long ones, since Implications tends to run to several sentences.

File: `PRINCIPLES.md` at the root, or `<system>.principles.md` where one repository holds several systems.

## Common gaps

- **Statement only.** By far the commonest failure: a bullet list of prohibitions with neither reason nor alternative. It reads as complete and is unusable, because every future reader has to reconstruct both halves and they will reconstruct them differently.
- **Tautologies.** "We keep the system simple because simplicity is valuable." Nothing there survives contact with a situation where complexity is the cheaper option.
- **Conditional rules with no condition.** "Unless there is a demonstrated need" appears in a great many principle lists and almost never with a definition of need attached.
- **Principles that are one decision.** Dated, with alternatives, and a specific trigger. These are ADRs, and leaving them here means losing the date and the rejected options.
- **The same principle in several places.** Nothing owns the rule, so it gets restated near whatever it is currently relevant to, and the fourth phrasing contradicts the first.
- **Vacuous principles.** "Write maintainable code." Nobody could do the opposite on purpose, so the rule constrains nothing and dilutes the ones that do.
- **No implications on a prohibition.** The reader now knows one thing not to do and nothing to do instead, which reliably produces a worse alternative than the one you banned.

## Further reading

- **TOGAF**, Part III, Architecture Principles: the Name, Statement, Rationale, Implications template this entry uses, plus the useful separation of principles from assumptions, constraints, and requirements. Those four are routinely lumped together and they behave differently.
- **Rozanski and Woods**, _Software Systems Architecture_: architectural principles appear here as inputs that shape views rather than as a view of their own, which is the right relationship. Their companion site is viewpoints-and-perspectives.info.
- **Neal Ford, Rebecca Parsons, and Patrick Kua**, _Building Evolutionary Architectures_: fitness functions, for the subset of principles that can be checked by a machine instead of by a reviewer. Worth reading before filling in the Enforcement slot, because the ones you can automate should not stay as prose.
