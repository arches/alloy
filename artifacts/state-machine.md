---
name: State machine
slug: state-machine
aka: [statechart, state diagram, state transition table, finite state machine]
related: [entity, scenario, sequence, glossary]
formats: [visual, tabular]
viewpoints: [functional, information]
perspectives: [security, evolution]   # who may trigger a transition; adding a state later
frames: [legal-lifecycle, transition-guards]
level: feature
discovery: ["*.states.drawio.svg", "*.states.md"]
---

**Use when** one thing's lifecycle has rules — some transitions are legal, some are not, and
the difference is enforceable.

**Not when** the steps always run in one order with no illegal paths — use a sequence diagram.

## Required slots
- **Subject** — the one entity whose lifecycle this is, named with a glossary term.
- **States** — named, mutually exclusive, and exhaustive for the subject.
- **Initial state** — exactly one.
- **Transitions** — each with a source state, a target state, and a triggering event.
- **Terminal states** — or an explicit statement that the lifecycle is perpetual.

## Optional slots
- **Guards** — the condition under which a transition is permitted.
- **Effects** — side effects on transition; entry and exit actions on states.
- **Authority** — who or what may trigger each event. This is where the Security perspective
  attaches; an unauthorized actor triggering a legal transition is still a defect.
- **Timeouts** — time-triggered transitions, with the clock that starts them.
- **Composite and parallel states** — Harel hierarchy, when a flat machine would duplicate the
  same transition across many states.
- **Illegal-event behavior** — reject, ignore, or error, stated once for the whole machine.

## Notation & naming
**draw.io**: rounded box = state; arrow labeled `event [guard] / effect` = transition; double
border = initial; thick border = terminal; dashed container = composite state or parallel
region. File: `<subject>.states.drawio.svg`.

**Tabular**: rows = source state, columns = event, cells = target state or `—` for illegal.
File: `<subject>.states.md`.

The two forms carry the same slots but fail differently, and the choice is not cosmetic. A
table has a cell for every state-event pair, so an undecided combination is visibly blank; a
diagram simply omits the arrow and looks finished. Prefer the table when the question is
completeness, the diagram when the question is shape.

## Common gaps
- No terminal state and no statement that there isn't one.
- States that are two orthogonal flags crossed — `paid` × `shipped` becoming four states. The
  answer is parallel regions, not a state for each combination.
- Arrows with no event: the transition happens, but nothing says what causes it.
- Guards written in prose beside the diagram instead of on the transition they gate.
- Unreachable states, and states with no exit.
- Nothing says what happens when an illegal event arrives — the most common source of
  generated code that silently accepts it.
- Status values in the domain model that don't match the state names here.

## Further reading
- Harel, "Statecharts: A Visual Formalism for Complex Systems" (1987) — hierarchy and
  parallel regions, and why flat machines explode without them.
- UML 2.5.1 spec, ch. 14, State Machines.
- Shlaer & Mellor, *Object Lifecycles* (1992) — state models bound to entities.
- Fowler & Parsons, *Domain-Specific Languages* (2010), ch. on state machines.
