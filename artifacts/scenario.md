---
name: Scenario set
slug: scenario
aka: [use case, acceptance criteria, Gherkin feature, specification by example]
related: [glossary, state-machine, entity, sequence, interface-contract]
formats: [text, tabular]
viewpoints: [functional]   # spans all others; Kruchten's "+1"
perspectives: []
frames: [observable-behavior, acceptance]
level: feature
discovery: ["*.scenarios.md", "*.feature"]
---

**Use when** you need to say what the system does from outside it, in terms someone could
check without reading the code.

**Not when** the question is how the parts collaborate to produce that behavior — use a
sequence diagram.

## Required slots
- **Goal** — the capability, named from the actor's side.
- **Actor** — who wants it, as a role defined in the glossary.
- **Given** — the state of the world before, stated as observable facts rather than as setup
  steps.
- **When** — the single action that starts it.
- **Then** — the outcome, observable from outside the system.
- **Alternate and failure paths** — the ones that change the outcome. A set with only the
  happy path is one scenario, not a set, and it is the single most common defect in this
  artifact type.

## Optional slots
- **Concrete example data** — real values, not placeholders. Disagreement hides inside "a
  valid amount" and surfaces immediately at "$4,312.00".
- **Examples table** — a `Scenario Outline` where one shape varies only by data.
- **Out of scope** — what this deliberately does not cover.
- **Quality expectation** — a pointer to a quality attribute scenario, never a loose adjective
  like "fast" in a Then.
- **Status** — `draft` | `agreed` | `implemented`.

## Notation & naming
**Gherkin**: `Feature` / `Scenario` / `Given`–`When`–`Then`, with `Scenario Outline` plus
`Examples` for data variation. File: `<capability>.scenarios.md`, or `*.feature` where the
project already runs Cucumber.

**Use case**: Cockburn's form — a main success scenario as numbered steps, with extensions
numbered against the step they branch from (`3a`, `3b`). Better than Gherkin when one goal has
many branches, because the branch point stays visible.

Steps must be **declarative, not imperative**. "When the customer submits an order" survives a
redesign; "When the customer clicks Place Order and types their postcode" is a UI script
wearing a scenario's clothes, and it will be wrong before the feature ships. A step naming a
class, endpoint, or table has crossed the boundary and belongs in another artifact.

This is the type every other behavioral artifact is checked against. Kruchten calls scenarios
deliberately redundant with the other views: they exist to discover the elements the other
artifacts need, and then to validate them. Expect the correspondences to run outward from
here — every state in a state machine reachable by some scenario, every term here resolving to
a glossary entry.

## Common gaps
- Only the happy path.
- Imperative UI steps instead of intent.
- Placeholder data, so the set reads as agreed when it isn't.
- Outcomes that aren't externally observable ("the record is saved" — observable how?).
- `Given` written as a click-through setup rather than a statement of state.
- Many near-duplicate scenarios on one path and none on the error paths.
- Terms that don't match the glossary; states that don't exist in the subject's state machine.
- A `Then` asserting something the actor can't see, which makes the scenario untestable
  without reaching into internals.

## Further reading
- Jacobson, *Object-Oriented Software Engineering* (1992) — use cases, origin.
- Cockburn, *Writing Effective Use Cases* (2000) — main success scenario and extensions.
- North, "Introducing BDD" (2006) — Given/When/Then.
- Adzic, *Specification by Example* (2011) — why concrete values beat generalizations.
- Gherkin reference; Wynne & Hellesøy, *The Cucumber Book*.
