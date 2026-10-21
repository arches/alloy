# Alloy

Alloy is a catalog of established techniques for describing software systems, and a collection of Claude skills and commands to turn them into code.

You author the artifacts. The code is generated from them.

# Philosophy

Typing code used to be where a lot of decisions got made. Product may have decided the feature direction in advance, but every line we wrote also proposed and settled more questions: what happens on a timeout, which value wins when two sources disagree, what does an empty list look like. We made those calls at the keyboard, as we typed.

But that’s not what code is for anymore. Code just runs.

If you've built anything with a coding agent lately, you know where the time goes: a lot of explaining. What the feature should do, what it shouldn't. What we decided last week, and why. Then correcting the LLM when it guesses wrong. Then explaining everything again next session, because it forgot.

What we need is a way to organize this information; a more structured approach to these tools that have historically been very helpful but also very ad hoc. And, we need a methodical way to turn these disparate artifacts into a cohesive codebase.

Enter Alloy.

_For more on this concept, see [https://streamlinetechconsulting.com/decision-engineering](https://streamlinetechconsulting.com/decision-engineering)_

## The catalog

| Artifact type                                                          | What it pins down                                                             |
| ---------------------------------------------------------------------- | ----------------------------------------------------------------------------- |
| [Context diagram](artifact_types/context-diagram.md)                   | What the system is responsible for, and everything outside it that it touches |
| [Component diagram](artifact_types/component-diagram.md)               | What the system is made of, and which parts may depend on which               |
| [Data model](artifact_types/data-model.md)                             | What the system stores, and what must always be true of it                    |
| [State machine](artifact_types/state-machine.md)                       | One thing's lifecycle, and which transitions are illegal                      |
| [Scenario set](artifact_types/scenario.md)                             | What the system does from outside, in terms someone can check                 |
| [Glossary](artifact_types/glossary.md)                                 | The one spelling of each term, and the names it gets mistaken for             |
| [Architecture decision record](artifact_types/adr.md)                  | One decision, the alternatives it closed off, and why they lost               |
| [Architecture principles](artifact_types/principles.md)                | A rule that holds across decisions, and what following it costs               |
| [Technology standards catalog](artifact_types/technology-standards.md) | One selection per kind of choice, so nobody re-picks it                       |

Every entry lists the other names its type goes by, so an ERD, a statechart, a Gherkin feature, or a ubiquitous language all land somewhere. Each file reads on its own, with or without the plugin installed.

## A starting point, not a checklist

The catalog is not a complete account of how to describe software, and it never will be. The only claim it makes for the entries it has is that these are forms which have worked well for a lot of people over a long time, so you do not have to invent one from scratch.

Describe your system in whatever way makes it clearest. If that is a table, a diagram with no established name, or a page of prose about the one thing everybody gets wrong, write that. Documentation that already works does not need converting, and a project using three entries from this list and nothing else is a project using the catalog correctly.

What the entries buy you is a shared vocabulary and a known shape: a name two people can agree on, and a list of what tends to be missing. Where no entry fits, the catalog has nothing to say and you lose nothing by ignoring it.

Entries do get added. One earns its place only if people outside this repo already use its name, and only if it answers a question no existing entry answers. UML alone offers fourteen diagram types, so without that gate the catalog becomes a list instead of a choice, which is the one failure it cannot recover from.

## Install

In your shell:

```
claude plugin marketplace add arches/alloy
claude plugin install alloy@alloy
```

Then start a session. Nothing lands in your project: the catalog and both entry points live in Claude Code's plugin cache.

## Use

`alloy:author` fires on its own when you set out to describe part of a system. You do not call it by name; it picks the artifact type, says which one it picked and what it rejected, and interviews you for the slots that type requires.

`/alloy:fit <file>` is explicit. Point it at an artifact you already have and it reports what is unconsidered, what is thin, and what is off-pattern, measured against that type's entry.

Neither one blocks. A half-filled artifact is a valid artifact.

## Update

The catalog gets refined over time, and the plugin carries no pinned version, so any commit counts as a new version and an update always brings the current catalog:

```
claude plugin update alloy@alloy
```

That refreshes the marketplace and installs the new copy in one step. Open a new session and it is live. Keep the `@alloy` suffix: naming the plugin alone reads the cached catalog without refreshing it, so it can report that you are current when you are not.

To receive changes without asking, open `/plugin`, select **Marketplaces**, select this marketplace, and choose **Enable auto-update**. Updates then land on a delay after your first message, and load on your next launch.

Expect updates to change what `/alloy:fit` tells you. When an entry gains a required slot, artifacts that read as complete last month will show a gap. That is the catalog getting sharper rather than your artifact getting worse, and the commit log is the record of which is which.
