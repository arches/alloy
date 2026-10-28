# Alloy

Alloy is a catalog of established techniques for describing software systems, and a collection of Claude skills and commands to turn them into code.

You author the artifacts. The code is generated from them.

## Philosophy

Typing code used to be where a lot of decisions got made. Product may have decided the feature direction in advance, but every line we wrote also proposed and settled more questions: what happens on a timeout, which value wins when two sources disagree, what does an empty list look like. We made those calls at the keyboard, as we typed.

But that’s not what code is for anymore. Code just runs.

If you've built anything with a coding agent lately, you know where the time goes: a lot of explaining. What the feature should do, what it shouldn't. What we decided last week, and why. Then correcting the LLM when it guesses wrong. Then explaining everything again next session, because it forgot.

What we need is a way to organize this information; a more structured approach to managing the decisions we've made. And, we need a methodical way to turn them into a cohesive codebase.

Enter Alloy.

_For more on this concept, see [https://streamlinetechconsulting.com/decision-engineering](https://streamlinetechconsulting.com/decision-engineering)_

_For an example project developed using Alloy, see [arches/shortlist](https://github.com/arches/shortlist)_

## A starting point, not a checklist

The catalog is not a complete account of how to describe software. The only claim it makes for the entries it has is that these are forms which have worked well for a lot of people over a long time, so you do not have to invent one from scratch.

Describe your system in whatever way makes it clearest for you. Documentation that already works does not need converting. What a catalog buys you is a shared vocabulary and a known shape: a name two people can agree on, and a list of what tends to be missing.

If you find a gap, see if you can find some prior art (you probably can) so you don't have to reinvent the wheel. And consider a PR to push it back into this catalog for everyone else.

## Workflow

- **Author the artifacts.** Pick a part of the system and describe it in the type that fits. One note becomes one artifact, and there is no conversion step to get through first.
- **Fit each one.** `/alloy:fit <file>` measures a single artifact against its type and reports what is unconsidered, what is thin, and what is off-pattern.
- **Rationalize the set.** `/alloy:crossfit` measures the artifacts against each other rather than against their types: a state no scenario reaches, a term the glossary spells one way and the data model spells another, an entity nothing stores.
  -️ **Generate.** `/alloy:generate` updates the code from the artifacts, landing it where the project already puts code, as a reviewable diff.
  -️ **Review the decisions.** The diff will contain choices the artifacts did not settle. Any decisions worth adjusting or validating should be pulled back into an artifact to inform future generation runs.
- **Repeat.** The artifacts are the source of truth for intent, so the next change starts with them and not with the code.

Nothing in the loop blocks. You can generate from a half-filled set, and a fit is a report rather than a gate.

## Install

In your shell:

```
claude plugin marketplace add arches/alloy
claude plugin install alloy@alloy
```

Then start a session. Nothing lands in your project: the catalog, the skill, and the commands live in Claude Code's plugin cache.

## Usage

Step 1 needs no command. `alloy:author` is a skill, so it fires on intent and helps you construct artifacts.

Iterate on artifacts with the `/alloy:fit <artifact file>` command, to nudge the artifact toward the standard for its type.

Iterate on your overall system design with `/alloy:crossfit` command, to rationalize artifacts against each other and find gaps.

Use `/alloy:generate` to turn artifact changes into code changes. Review `ASSUMPTIONS.md` to see what the generation had to assume in order to make progress. Use `DIRECTIVES.md` sparingly to guide the generation. Ideally, guidance takes the form of additional artifact changes - that's the more durable way to adjust your system description.

## Update

The catalog gets refined over time, and the plugin carries no pinned version, so any commit counts as a new version and an update always brings the current catalog:

```
claude plugin update alloy@alloy
```

That refreshes the marketplace and installs the new copy in one step. Open a new session and it is live. Keep the `@alloy` suffix: naming the plugin alone reads the cached catalog without refreshing it, so it can report that you are current when you are not.

To receive changes without asking, open `/plugin`, select **Marketplaces**, select this marketplace, and choose **Enable auto-update**. Updates then land on a delay after your first message, and load on your next launch.

Expect updates to change what `/alloy:fit` tells you. When an entry gains a required slot, artifacts that read as complete last month will show a gap. That is the catalog getting sharper rather than your artifact getting worse, and the commit log is the record of which is which.
