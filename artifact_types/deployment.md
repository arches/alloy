---
name: Deployment view
slug: deployment
aka:
  [deployment diagram, environment map, infrastructure view, runtime topology]
discovery:
  [
    "*.deployment.md",
    "*.deployment.mmd",
    "*.deployment.d2",
    "*.deployment.drawio.svg",
  ]
slots:
  core:
    environments: "Which environments exist, and what is each one for?"
    nodes: "For each environment, what runs where? The host, the runtime, and the region if it matters."
    stateful-resources: "For each environment, which databases, buckets, queues, and caches does it touch? Name every one."
    connections: "What talks to what, inside an environment and across them, and over what?"
    forbidden-connections: "Which connections must never exist? If there is only one environment, say so here rather than leaving it empty."
  optional:
    [configuration, secrets, promotion-path, regions, scaling, observability]
---

## What it is

A deployment view says where the system runs: the environments, what is running in each one, what state each one touches, and which of them are allowed to reach each other.

The discipline is that **an environment is defined by its resources, not by its name.** "Staging" is a label. "A Vercel preview deployment, a dedicated hosted Postgres, and the sandbox credentials for the payments provider" is an environment. Until the resources are named, nobody can tell whether two environments are genuinely separate, and the answer to that question is usually discovered by accident.

Which is why the sharp axis here is **stateful versus stateless**. Two environments sharing a CDN or a container image costs nothing. Two environments sharing a database is how test data ends up in front of customers, or worse, how a test run truncates a table somebody needed. List every stateful thing each environment touches, and sharing becomes visible instead of implicit.

The other half of the artifact is the connections that must not exist. A node list is an inventory, and an inventory cannot say that a preview deployment must never reach the production database. That sentence is not a fact about what is running, it is a fact about what must never be wired up, and nothing else you write will hold it.

**Use when** the system runs in more than one place, which usually means from the first time a deployment exists alongside a developer machine.

**Not when** the question is which runtime pieces exist and what each is responsible for. Use a component diagram.

## How much belongs here?

The failure mode is this turning into an inventory of infrastructure, which is long and goes stale. Three rules keep it short:

1. **Every stateful resource, exhaustively**, because the value is in comparing the lists across environments and a list with a hole compares falsely.
2. **Nothing stateless unless it differs between environments**, since a shared image or a shared CDN is not a finding.
3. **Names, never values**: which database, not its connection string; which secret store, not the secrets.

If a detail would change on the next deploy and nobody would care, it belongs in the thing that gets generated, not here.

## How you build one

1. List the environments and say what each is for in a few words. If two of them have the same purpose, you have one environment and two names.
2. For each, list what runs there: the host, the runtime, the region when it matters.
3. For each, list every stateful resource it touches. Databases, object storage, queues, caches, third-party accounts that hold data. This is the step that pays for the artifact.
4. Compare the lists. Any stateful resource appearing under two environments is either deliberate and worth a note, or the finding you wrote this to surface.
5. Write the connections, then write the ones that must never exist. The second list is shorter and more valuable, and you build it by asking, for each pair that is not connected, whether that is an accident or a rule.

## How you use it

- **Preventing the expensive mistake.** This is the artifact that holds "staging never connects to the production database". Stated here it is checkable; stated nowhere it depends on everyone remembering.
- **Onboarding to the environments.** "Which database does Test use" is a question that otherwise costs somebody an afternoon and sometimes costs them the dev database.
- **Sizing an infrastructure change.** Adding a queue is a different change from adding one to every environment, and the row count tells you which you are proposing.
- **Checking against what exists.** The resources named here should be the resources that exist. A drift in either direction is worth knowing: something unused, or something nobody declared.

## Required slots

- **Environments**: the named environments and the purpose of each. Two environments with the same purpose is a finding, usually a copy somebody made and never retired.
- **Nodes**: what runs in each environment, and where. The host and runtime matter because they are what a generator would otherwise guess at, and the region matters when data residency or latency depends on it.
- **Stateful resources**: every database, bucket, queue, cache, and data-holding third-party account that each environment touches. The slot is deliberately exhaustive rather than interesting, because the value is in comparing the lists, and a list with an omission compares falsely.
- **Connections**: what reaches what, inside an environment and between them, and over what protocol. Cross-environment connections deserve particular care, since those are the ones nobody expects to exist.
- **Forbidden connections**: the ones that must never be wired up, with the consequence stated. Production isolation is the universal case and is worth writing even when it feels obvious, because the audience includes a generator with no sense of which mistakes are expensive. A single-environment system should say so here rather than leaving the slot blank.

## Optional slots

- **Configuration**: what differs per environment, and where it lives. Usually a short list of names rather than values.
- **Secrets**: where each environment's credentials come from, and who can read them. Never the secrets themselves.
- **Promotion path**: how code moves from one environment to the next, and what gates each step. Fill it once there is more than one way for something to reach production.
- **Regions**: where each environment physically runs, when residency, latency, or regulation depends on it.
- **Scaling**: instance counts and limits, where they are load-bearing rather than defaults.
- **Observability**: where logs and metrics from each environment go, which is most useful for the environments nobody watches.

## Notation & naming

**Mermaid**: `flowchart`; a `subgraph` per environment holding its nodes; `[(name)]` for stateful resources; labeled edges for connections. Forbidden connections have no native form, so they belong in a list beneath the diagram. File: `<system>.deployment.mmd`, or a fenced block in `<system>.deployment.md`.

**D2**: a container per environment; `shape: cylinder` for stateful resources; a `forbidden` class carrying `style.stroke` and `style.stroke-dash`, applied as `a -> b: the rule { class: forbidden }`. File: `<system>.deployment.d2`.

**draw.io**: box per node, cylinder per store, dashed enclosure per environment, struck-through dashed arrows for forbidden connections. File: `<system>.deployment.drawio.svg`. Turn compression off, or no change to it is reviewable.

## Common gaps

- **Environments named but not described.** "We have staging and production" with no resources listed, which is the state in which nobody can answer whether they are actually separate.
- **Stateful resources unlisted**, so a shared database stays invisible until a test run deletes something.
- **No forbidden connections**, leaving production isolation as a convention that holds until somebody new, or something generated, wires up the obvious thing.
- **Local and test treated as one environment.** They usually share a host and must not share a database, and collapsing them is how test runs eat development data.
- **Configuration and secrets unstated per environment**, so the question of what differs between them has no written answer and gets rediscovered at each deploy.
- **No promotion path**, meaning there could be more than one route to production and nobody has said what is intended.
- **Drift from the real infrastructure**, in either direction: a resource that exists and is not listed, or one that is listed and no longer exists.

## Further reading

- **Rozanski and Woods**, _Software Systems Architecture_: the Deployment viewpoint, which covers the runtime environment, third-party software requirements, network requirements, and technology compatibility. Their companion site is viewpoints-and-perspectives.info.
- **UML 2.5.1**, the deployment diagram: nodes, artifacts, and communication paths, for the formal notation if you want one. Heavier than this artifact needs, but it settles what a node is.
- **Simon Brown's C4 model**, which treats deployment as a supplementary diagram mapping containers onto infrastructure. Published at c4model.com.
- **Jez Humble and David Farley**, _Continuous Delivery_: the source for thinking of environments as a pipeline rather than a set, which is what the promotion path slot is reaching for.
