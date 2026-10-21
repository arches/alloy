---
name: State machine
slug: state-machine
aka: [statechart, state diagram, state transition table, finite state machine]
discovery:
  [
    "*.states.drawio.svg",
    "*.states.md",
    "*.states.d2",
    "*.states.mmd",
    "*.states.mermaid",
  ]
slots:
  core:
    subject: "Whose lifecycle is this? Name one thing that is in exactly one state at a time: an entity, or a single aspect of one, such as an order's payment tracked separately from its fulfillment."
    states: "What states can it be in? They should be mutually exclusive."
    initial-state: "Which state is it in before anything has happened?"
    transitions: "For each change: what event causes it, and from which state to which?"
    terminal-states: "Which states end the lifecycle? There may be several, or none at all if it runs forever."
  optional:
    [
      guards,
      effects,
      authority,
      timeouts,
      composite-states,
      illegal-event-behavior,
    ]
---

## What it is

A state machine says, for one thing in your system, which states it can be in and which moves between them are legal. An order is draft, submitted, shipped, or cancelled. From draft it can be submitted; from shipped it cannot go back to draft. The artifact is the full list of states plus the full list of permitted moves, each move labeled with the event that causes it.

One discipline makes it work: a state is something the thing _is_, not something that happens to it. An order _is_ awaiting payment. "Charge the card" is not a state, because it is an action someone takes. The test is whether the thing can sit there. An order can sit in awaiting payment for a week; it cannot sit in charge the card.

If your boxes are verbs, you have drawn a flowchart. A flowchart is organized by sequence: what happens after what. A state machine is organized by situation: for each state the thing can be in, which events are legal and where each one leads. That is what makes it checkable. A cancel request comes in for an order that has already shipped, so what happens? You can ask that of every state and every event, and the pairs nobody has an answer for are where the bugs are.

The states are usually already in your system, as a `status` column or an enum. What is almost never written down is the edges. So the rules end up scattered across conditionals in three services, and nobody can answer "can a shipped order be cancelled" without reading all of them. Every unwritten edge is a place where code will either accept something it should refuse or crash on something it should have handled.

**Use when** one thing's lifecycle has rules, where some transitions are legal, some are not, and the difference is enforceable.

**Not when** the steps always run in one order with no illegal paths. Use a sequence diagram for that.

## State machine or a status field?

You probably already have `order.status`. That enum is the state list; it is not the state machine. The enum says which values exist, while the machine says which value may follow which, on what event, and who is allowed to trigger it. You cannot fold the machine into the enum, because an enum has no concept of an edge.

## How you build one

Start from the thing, not the process. Name the single thing whose lifecycle this is. If you find yourself tracking payment and fulfillment in the same diagram, that is two machines, or one machine with parallel regions. List the states and check they are mutually exclusive, so the thing is in exactly one of them at any moment. Then do the edges: for each state, which events can arrive, and where does each one leave the thing. Then the two slots everyone forgets, which are what state it is in before anything has happened (initial state), and what ends the lifecycle (terminal states), or an explicit note that it never ends.

The completeness trick is to draft it as a table, states down the side and events across the top. Every cell has to say something, even if it says illegal. A diagram lets you omit an arrow and still look finished; a table shows you the hole.

## How you use it

- **Checking scenarios.** Every state should be reachable by some scenario, and every scenario's Given should name a real state. A state no scenario reaches is either dead or untested, and you cannot tell which from the machine alone.
- **Naming.** The state names are the identifiers. Whatever gets generated downstream uses these words, so a different word appearing there means something was authored twice.
- **When requirements change.** Adding a state is usually cheap. Adding one that needs an edge from every existing state is a signal you have two orthogonal flags crossed, and the answer is parallel regions rather than a state per combination.

## Required slots

- **Subject**: the one thing whose lifecycle this is. Usually an entity or a single aspect of one. The test is exclusivity: at any moment the subject is in exactly one of the listed states. If you cannot say that, you have more than one subject.
- **States**: named, mutually exclusive, and exhaustive for the subject.
- **Initial state**: exactly one.
- **Transitions**: each with a source state, a target state, and a triggering event.
- **Terminal states**: or an explicit statement that the lifecycle is perpetual.

## Optional slots

- **Guards**: the condition under which a transition is permitted.
- **Effects**: side effects on transition; entry and exit actions on states.
- **Authority**: who or what may trigger each event. An unauthorized actor triggering a legal transition is still a defect.
- **Timeouts**: time-triggered transitions, with the clock that starts them.
- **Composite and parallel states**: Harel hierarchy, for when a flat machine would duplicate the same transition across many states.
- **Illegal-event behavior**: reject, ignore, or error, stated once for the whole machine.

## Notation & naming

**draw.io**: rounded box = state; arrow labeled `event [guard] / effect` = transition; double border = initial; thick border = terminal; dashed container = composite state or parallel region. File: `<subject>.states.drawio.svg`.
**D2**: nodes for state; `a -> b: event [guard] / effect` for transitions; `style.double-border: true` for initial state; `style.stroke-width: 4` for terminal states; container for composite states or parallel regions. File: `<subject>.states.d2`.
**Mermaid**: `stateDiagram-v2`; nodes for state; `a --> b : event [guard] / effect` for transitions; `[*] -->` for initial state; `--> [*]` for terminal states; `state s { ... }` with `--` between regions for composite states or parallel regions. File: `<subject>.states.mmd`, or a fenced block in `<subject>.states.md`.
**Tabular**: rows = source state, columns = event, cells = target state or `x` where the event is illegal. File: `<subject>.states.md`.

Tabular and diagrammatic forms carry the same slots but fail differently, so prefer the table when the question is completeness and the diagram when the question is shape. Both can be included, but must remain in sync.

_Condition_ is reserved here for a guard, the boolean test gating a transition. It never means a state.

## Common gaps

- Arrows with no event: the transition happens, but nothing says what causes it.
- States that are two orthogonal flags crossed, such as `paid` x `shipped` becoming four states. The answer is parallel regions, not a state per combination.
- Guards written in prose beside the diagram instead of on the transition they gate.
- Unreachable states, and states with no exit.
- Nothing says what happens when an illegal event arrives, which is the most common source of generated code that silently accepts it.
- Status values in the domain model that don't match the state names here.

## Further reading

- Harel, "Statecharts: A Visual Formalism for Complex Systems" (1987): hierarchy and parallel regions, and why flat machines explode without them.
- UML 2.5.1 spec, ch. 14, State Machines.
- Shlaer & Mellor, _Object Lifecycles_ (1992): state models bound to entities.
- Fowler & Parsons, _Domain-Specific Languages_ (2010), ch. on state machines.
