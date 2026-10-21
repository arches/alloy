---
name: Context diagram
slug: context-diagram
aka:
  [
    system context diagram,
    context view,
    level-0 data flow diagram,
    C4 system context,
  ]
discovery:
  ["*.context.drawio.svg", "*.context.d2", "*.context.mmd", "*.context.mermaid"]
slots:
  core:
    subject: "Which system is this? Name the one thing the box stands for. If you cannot say what is inside it in a sentence, you have more than one system."
    responsibilities: "What is this system responsible for? Two sentences at most, stated positively rather than as a list of features."
    actors: "Who uses it? Name the roles, not the people, and include the ones that feel too obvious to mention."
    external-systems: "What else does it talk to that somebody else owns? Include the infrastructure you depend on but did not think of as a system."
    interactions: "For each thing outside the box: what flows, in which direction, and what for?"
  optional:
    [out-of-scope, protocols, trust-boundaries, data-ownership, failure-modes]
---

## What it is

A context diagram puts your whole system in one box, draws everything outside it that the system talks to, and labels what passes between them. Nothing goes inside the box. It is the smallest complete description of a system: three to six shapes, one of which is everything you are going to build.

The box is empty on purpose, and that is the discipline. The entire artifact is a claim about where the boundary falls. Inside is what you control and get to decide later; outside is what you do not control and have to accommodate. Every other artifact you write assumes that line already exists, which makes drawing it in the wrong place expensive in a way that drawing it incompletely is not.

One test keeps the boundary honest: can you change that thing by editing your own code? If you can, it belongs inside, however much it feels like a separate concern. "Search" and "Reporting" drawn as outside boxes are the usual form of this mistake, and they make the diagram look busier while saying less. A genuinely external element has its own owner and its own release schedule, and your job is to cope with it rather than to change it.

The arrows are the other half of the content. An arrow labeled "uses" records that two things are connected, which you already knew. An arrow labeled "searches for restaurants by name and area" records what the connection is _for_, which is what tells you what stops working when that thing goes down.

**Use when** you are starting something, or when two people disagree about whether some piece of behavior is your system's job.

**Not when** the question is what the system is made of rather than what it touches. Use a functional view for that.

## Diagram or a paragraph?

Diagram. A paragraph can state the same facts, and for a small system it will be shorter. What it cannot do is make the omissions visible. Prose lets you simply not mention the identity provider; a diagram has a conspicuous hole where it should have been. Five boxes can be scanned by someone who then says "you've forgotten the email sender," and five sentences cannot.

Small is not a reason to skip it. A three-box diagram still earns its place, because those three boxes are what every later artifact refers back to, and because a system with no external dependencies at all is a real and uncommon finding that you only make by going looking.

## How you build one

1. Name the system and write its responsibilities in one or two sentences. If you cannot, stop: the boundary is in the wrong place and the rest will inherit that.
2. List the people, by role. Include the roles that feel too obvious.
3. List everything else the system talks to that somebody else owns. Walk the plumbing deliberately: identity, email, payments, storage, anything you call over a network.
4. Write each arrow's label before you draw the arrow. What flows, which way, what for.
5. Then check every box against the test: could you change this by editing your own code? If yes, move it inside.

## How you use it

- **Orienting someone new**, including the person who asked for the system.
- **Settling scope arguments.** "Is that our job?" is a question about which side of the line something sits on, and this is the only artifact that answers it directly.
- **Finding what you have to handle failing.** Every box outside the system is something that can be slow, down, or changed without warning. The list of external boxes is the list of failures you owe an answer for.
- **Sizing a change.** A requested feature that needs a new external box is a different size of change from one that does not, and that is visible before any code exists.

## Required slots

- **Subject**: the one system the box stands for, named in the domain's words. The test is the one-sentence rule: if you cannot say what is inside it without using "and," you are probably drawing two systems and should pick one.
- **Responsibilities**: what the system is for, positively stated. Two sentences. This is the only content that belongs inside the box, and without it the box has a name and nothing a reader can check the boundary against.
- **Actors**: the roles that use the system, including the unglamorous ones. An administrator who exists only to approve things is still an actor, and the count of distinct roles is a fact the rest of your artifacts will depend on.
- **External systems**: everything the system talks to that has a different owner. This slot is where diagrams are most often incomplete, because infrastructure feels like background rather than like a system.
- **Interactions**: one labeled arrow per relationship, carrying what flows and which way. Two directions get two arrows, because the two directions almost never carry the same thing.

## Optional slots

- **Out of scope**: things a reader would reasonably expect inside the box but which are not, with a word on why. Worth filling when a boundary call was contested.
- **Protocols**: the mechanism on each arrow (HTTPS and JSON, SQL over TCP, SFTP). Cheap to add and it removes a guess from anything generated downstream.
- **Trust boundaries**: which crossings are between trusted and untrusted parties. Usually drawn as a dashed enclosure rather than as an extra box.
- **Data ownership**: for each flow, who is the system of record. Matters as soon as the same fact exists on both sides of a boundary.
- **Failure modes**: per external system, what the system does when that one is unavailable. Fill it for the dependencies that sit in a user-facing path.

## Notation & naming

- **The subject**: one box, visually heaviest, holding the system's name and its responsibilities. Nothing else inside it. The moment you want to draw something inside, you have started a functional view and should start a new file.
- **People**: an avatar icon or a rounded box, labeled with the role rather than a name.
- **External systems**: plain boxes, filled or outlined differently from the subject so the boundary reads at a glance.
- **Arrows**: one per direction, each labeled with what flows and what for. No bare verbs, and no double-headed arrows standing in for two different flows.
- **Legend**: required as soon as fill or shape carries meaning, and never rely on color alone to distinguish element types.

Name the file after the system, not the diagram: `restaurants.context.drawio.svg`.

## Common gaps

- **Features drawn as external systems.** "Search," "Reporting," "Notifications" sitting outside the box. They are parts of the system, and putting them outside hides the boundary rather than describing it.
- **Arrows labeled with a bare verb.** "Uses," "calls," "integrates with." The label is where the content is, and these labels have none.
- **Infrastructure you depend on but did not choose.** The identity provider, the email sender, the object store, the payment processor. These get left out because they feel like plumbing, and they are precisely the elements whose outage you will eventually need to have thought about.
- **Human actors omitted for being obvious.** The diagram ends up showing only machines, which loses the role count that the rest of the artifacts are built on.
- **No responsibilities.** The box carries a product name and nothing more, so there is no statement a reader can test the boundary against, and scope arguments have nowhere to land.

## Further reading

- **Structured analysis** the context diagram is the level-0 data flow diagram, the DFD drawn with exactly one process. Tom DeMarco, _Structured Analysis and System Specification_ (1979); Chris Gane and Trish Sarson, _Structured Systems Analysis_ (1979).
- **Rozanski and Woods**, _Software Systems Architecture_, 2nd edition, where the Context viewpoint was added to the original six. Their companion site is viewpoints-and-perspectives.info, which carries a quick reference card for the viewpoint set.
- **Simon Brown's C4 model**, where it is level 1 of four, published openly at c4model.com. C4 is the strictest of the three about labeling arrows, and that strictness is worth borrowing wherever you draw runtime structure.
