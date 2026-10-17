---
name: Scenario set
slug: scenario
aka: [use case, acceptance criteria, Gherkin feature, specification by example]
discovery: ["*.scenarios.md", "*.feature"]
slots:
  core:
    goal: "What is the actor trying to accomplish, in their words?"
    actor: "Who wants this? Name the role rather than a person."
    given: "What is true about the world before this starts?"
    when: "What single action starts it?"
    then: "What can the actor observe afterward that proves it worked?"
    alternate-paths: "What else can happen? Name the paths that end differently, and the outcome on each."
  optional:
    [example-data, examples-table, out-of-scope, quality-expectation, status]
---

## What it is

A scenario set describes what a capability does from outside the system, as concrete situations: given some state of the world, when someone does one thing, then something observable happens. The set covers one capability, with the main path plus every path that ends differently.

Three properties of each scenario do the work.

1. **Observable from outside.** "The record is saved" fails the test; "the order shows up in their order history" passes.
2. **Declarative, not imperative.** "When the customer submits an order" survives a redesign. "When the customer clicks Place Order, types their postcode, and clicks Next" is a UI script that will be wrong before the feature ships.
3. **Real data.** Disagreement hides comfortably inside "a valid amount" and surfaces immediately at "$4,312.00".

There's one key property of the whole: it is a set. One scenario is not a scenario set, and a set with only the happy path is the most common defect in this type by a wide margin. The alternate and failure paths are where the decisions actually live. What happens when the list is empty, what happens when it has already been done, what the actor sees when it fails. If these are not written down then whatever writes the code decides them silently, which leaves the failure paths decided by default, and the default is usually a stack trace.

**Use when** you need to say what the system does from outside it, in terms someone could check without reading the code.

**Not when** the question is how the parts collaborate to produce that behavior. Use a sequence diagram for that.

## How big should a set be?

The pull on this artifact is toward becoming a test suite written in prose, and it has to be resisted, because the two have different jobs. A scenario set holds behavioral decisions. The generated tests hold cases. There will be far more cases than scenarios, and that is correct: the generator expands, the artifact decides.

So a scenario earns its place when the outcome differs in kind, not when the input differs in value. Three scenarios that vary only by amount are one scenario with an examples table. Boundary values, permutations, and null handling are expansions of a decision you already made, and writing each one out by hand gives you a longer document that is harder to read and no more decided than the short one.

## How you build one

Start from the goal, named from the actor's side, one capability per set. Write the main path first, three to five declarative steps. Keep one When per scenario: two Whens means either two scenarios or a procedure you have written in the wrong artifact.

Then ask the question that turns a scenario into a set: what else can happen? Chase the paths that _end_ differently, not variations that end the same way. Five scenarios covering the happy path and none covering failure is a common shape, and it is backwards.

Put real values in. If you cannot, that is a finding rather than a formatting problem, because it means nobody has yet decided what a valid one looks like, and that is worth marking open rather than papering over with a placeholder.

## How you use it

- **Deciding behavior before anything is built.** The failure paths are the decisions, and both the code and the tests come from them. If a path is not written here, you risk having the decision made for you.
- **As the definition of done.** This is the one artifact the person who asked for the feature can check without help, which is why the outcome has to stay externally observable.
- **Walking it through your other descriptions.** Follow one scenario, step by step, against whatever else describes the system, and the question nobody answered tends to fall out. Payment fails and the order gets cancelled, but nothing anywhere says when the reserved stock is released.
- **When a bug arrives.** It is either a scenario that was wrong, a scenario that was missing, or an incomplete expansion of the scenario into the test suite - deciding which is how the set stays current instead of rotting.

## Required slots

- **Goal**: the capability, named from the actor's side.
- **Actor**: who wants it, as a role rather than a named person.
- **Given**: the state of the world before, stated as observable facts rather than as setup steps.
- **When**: the single action that starts it.
- **Then**: the outcome, observable from outside the system.
- **Alternate and failure paths**: the ones that change the outcome. A set with only the happy path is one scenario, not a set.

## Optional slots

- **Example data block**: a separate table of values, for when concrete data clutters the steps themselves.
- **Examples table**: a `Scenario Outline` where one shape varies only by data.
- **Out of scope**: what this deliberately does not cover.
- **Quality expectation**: a measurable target such as a latency or a rate, never a loose adjective like "fast" sitting in a Then.
- **Status**: `draft` | `agreed` | `implemented`.

## Notation & naming

**Gherkin**: `Feature` / `Scenario` / `Given`, `When`, `Then`, with `Scenario Outline` plus `Examples` for data variation. File: `<capability>.scenarios.md`, or `*.feature` where the project already runs Cucumber.

**Use case**: Cockburn's form, a main success scenario as numbered steps with extensions numbered against the step they branch from, `3a` and `3b`. Better than Gherkin when one goal has many branches, because the branch point stays visible.

A step that names a class, an endpoint, or a table has crossed the boundary and belongs in a different artifact.

## Common gaps

- `Given` written as a click-through setup rather than a statement of state.
- Many near-duplicate scenarios on one path and none at all on the error paths.
- Acceptance criteria bullets lifted from a ticket, which usually have no Given, so nothing anchors them, and which get discarded when the ticket closes even though the capability outlives it.
- A set that covers a whole journey rather than one capability, so the goal stops being checkable.

## Further reading

- Jacobson, _Object-Oriented Software Engineering_ (1992): use cases, origin.
- Cockburn, _Writing Effective Use Cases_ (2000): main success scenario and extensions.
- North, "Introducing BDD" (2006): Given, When, Then.
- Adzic, _Specification by Example_ (2011): why concrete values beat generalizations.
- Gherkin reference; Wynne and Hellesøy, _The Cucumber Book_.
