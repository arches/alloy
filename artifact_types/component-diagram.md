---
name: Component diagram
slug: component-diagram
aka:
  [functional structure model, functional view, component view, module diagram]
discovery:
  [
    "*.components.md",
    "*.components.mmd",
    "*.components.d2",
    "*.components.drawio.svg",
  ]
slots:
  core:
    subject: "Which system is this the inside of? Name it and say nothing else about it here."
    elements: 'What are the runtime pieces? Name each one by what it is responsible for, in a single sentence. If the sentence needs an "and", you have two pieces.'
    interactions: "For each pair that communicates: what flows, in which direction, and what for?"
    external-dependencies: "What outside the system do these pieces talk to, and which pieces are allowed to do it?"
  optional:
    [interfaces, forbidden-interactions, groupings, designed-flexibility]
---

## What it is

A component diagram is one level inside the system: the runtime pieces, what each one is accountable for, and which pieces are allowed to talk to which. It is the answer to "what is this thing made of," where the pieces are things that exist while the system is running rather than things that exist in your editor. If you write code, the shortest version is that this is the interface and a development view is the implementation: rewrite an element in another language and this diagram is unchanged, because its responsibility and its permitted dependencies are unchanged.

That distinction is the discipline. The most common version of this diagram is not a component diagram at all, it is a picture of the directory tree with `utils` and `lib` and `components` drawn as boxes. The test is whether you could point at the element while the system is running. A request handler, a scheduled job, a cache, a queue consumer: all pointable. A folder is not. Where your code lives is a real question with a real answer, and it belongs in a development view.

Each element gets exactly one responsibility, written as one sentence. The "and" test does the work here: if you cannot say what a piece is accountable for without joining two clauses, you have drawn one box around two elements, and every later decision about either of them will be muddied by the other. The sentence is not documentation you add afterward. It is what the box means, and an element nobody can write a sentence for is the diagram telling you something.

The arrows carry as much as the boxes. An unlabeled line records that two pieces are connected, which the existence of a working system already implied. A line labeled with what flows and what for records the dependency you actually have, and a dependency you can read is a dependency you can question. This is also the only artifact where you can draw an interaction that must _not_ exist, which is often the single most valuable thing on the page.

**Use when** the system has more than one part that can be reasoned about separately, and you need to say which parts may depend on which.

**Not when** the question is the order in which the pieces talk for one particular case. Use a sequence diagram for that.

## Component diagram or your folder structure?

They look the same on a whiteboard and they answer different questions, which is why the confusion survives so long. Your folder structure answers "where do I put this file," and that is a development concern: build order, ownership, what gets packaged together, what a new contributor reads first. A component diagram answers "what exists at runtime and what may it depend on."

In a monolith the two often land on nearly the same boxes, and that is fine. There is no separate process to distinguish them, so one name answers both questions. The two diverge in both directions, and the divergences are the interesting part. A single folder can hold three runtime elements that should never call each other. Three folders can hold one element that happens to be split for tidiness. A `shared` folder is almost never a runtime element and is almost always four of them.

Drawing the folder tree and calling it architecture costs you the dependency rules, because folders cannot express a forbidden edge. Nothing about a directory layout says that the thing in `components/` must never reach the thing in `db/`. That rule is the whole reason to draw this, and it only exists on a diagram whose boxes are runtime things.

## How you build one

1. Name the system, and resist saying anything else about it. This diagram is the inside of one box.
2. List the runtime pieces. For each, write the responsibility sentence before you draw anything, and apply the "and" test to the sentence.
3. Delete any box whose sentence you could not write. Whatever was in it belongs to the elements around it.
4. For each pair that communicates, write the arrow label: what flows, which way, what for.
5. Then go looking for the edges that must not exist. For each pair that is _not_ connected, ask whether that is an accident or a rule. The rules get drawn.

## How you use it

- **Deciding where new behavior goes.** A change that fits inside one element's responsibility sentence is a small change. One that needs a new element, or a new arrow, is a different size, and you can see which before writing anything.
- **Catching a dependency you did not intend.** New arrows are the cheapest architectural review there is, because an arrow nobody can label is usually an arrow nobody meant to add.
- **Stating the rules that are otherwise unenforceable.** A forbidden edge drawn here is a rule with a location. Stated only in prose, it gets restated in four places and drifts.
- **Scoping a rewrite.** An element with one responsibility and few labeled arrows can be replaced. One with five responsibilities cannot, and the diagram says so before anyone volunteers.

## Required slots

- **Subject**: the system this is the inside of, named once. If you find yourself describing the subject's own responsibilities here, that content belongs in a context diagram and this file should only name it.
- **Elements**: the runtime pieces, each with a one-sentence responsibility. The sentence is the slot, not the name. A box labeled only `Notifications` has a name and no content; `Notifications: decides who should hear about a change and hands each message to the sender` is an element.
- **Interactions**: one labeled arrow per communicating pair, carrying what flows and which way. Two directions get two arrows, because the two directions rarely carry the same thing.
- **External dependencies**: the things outside the system that these pieces reach, and which pieces are permitted to reach them. Drawn at the edge rather than inside. This slot is what stops an external dependency being reachable from everywhere by default.

## Optional slots

- **Interfaces**: what each element exposes, named operations and their inputs and outputs. Worth filling as soon as two people work on opposite sides of one arrow.
- **Forbidden interactions**: pairs that must never communicate, and why. Fill this whenever a rule exists, because this is the only artifact that can hold it.
- **Groupings**: layers, subsystems, or bounded contexts drawn as enclosures. Useful once there are more than seven or eight elements, misleading before that.
- **Designed flexibility**: where change is anticipated, and which element absorbs it. The honest version of this slot is short and sometimes empty.

## Notation & naming

**draw.io**: box holding the element name and its responsibility sentence; solid arrow labeled with what flows for interactions, one per direction; dashed arrow struck through for forbidden interactions; distinct fill at the edge for external dependencies; dashed enclosure for groupings. File: `<subject>.components.drawio.svg`.

**D2**: nodes for elements, with the responsibility after `\n` in the label; `a -> b: what flows` for interactions; a `forbidden` class carrying `style.stroke` and `style.stroke-dash`, applied as `a -> b: the rule { class: forbidden }`; a distinct class or `shape: cylinder` for external dependencies; containers for groupings. File: `<subject>.components.d2`.

**Mermaid**: `flowchart`; `id["Name<br/>responsibility"]` for elements; `a -->|what flows| b` for interactions; `subgraph` for groupings; `[(name)]` for data stores. Forbidden interactions have no native form here: the nearest is `a -.->|the rule| b` plus `linkStyle N stroke:#b02a2a`, which is index-based and breaks whenever an edge is added above it. File: `<subject>.components.mmd`, or a fenced block in `<subject>.components.md`.

## Common gaps

- **No forbidden interactions.** Only the permitted edges are drawn, so the diagram records what the system does and cannot record what it must not do. This is the most valuable slot and the one most often left empty.
- **Folders drawn as elements.** `lib`, `utils`, `helpers`, `shared`, `core`. These are places, not runtime things, and a diagram of places cannot carry a dependency rule.
- **An element with no responsibility sentence.** Usually the one named `core` or `common`, and usually where everything that did not fit elsewhere ended up. The missing sentence is the finding.
- **Responsibility sentences with an "and" in them.** One box, two elements, and every decision about either one now has to consider the other.
- **Unlabeled arrows.** The label is where the content is. "Uses" and "calls" have none.
- **External dependencies reachable from everywhere.** When no slot records which elements may reach an outside system, the answer defaults to all of them, and nobody notices until something in the UI is calling a payment provider.

## Further reading

- **Rozanski and Woods**, _Software Systems Architecture_, 2nd edition: the Functional viewpoint, which treats the functional structure model as its primary model alongside interface and interaction descriptions. Their companion site is viewpoints-and-perspectives.info.
- **David Parnas**, "On the Criteria To Be Used in Decomposing Systems into Modules" (1972): where responsibility-driven decomposition starts, and still the clearest argument for why the boxes should be drawn around what changes together rather than around what runs together.
- **UML 2.5.1**, the component diagram, for the formal notation if you want one. OMG's specification is heavier than this artifact needs, but it settles arguments about what a connector means.
- **Simon Brown's C4 model**, level 3, published at c4model.com. Worth reading for the caveat as much as the content: Brown's own position is that most teams should not maintain this level by hand, which is a useful corrective if you find yourself drawing one of these per element.
