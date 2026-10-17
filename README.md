# Alloy

A catalog of established techniques for describing software systems: scenarios, state machines, ADRs, glossaries. Each entry says when to use that artifact type, what a complete one contains, and where the technique comes from.

You author the artifacts. The code is generated from them.

For the idea behind it, start here: [https://streamlinetechconsulting.com/decision-engineering](https://streamlinetechconsulting.com/decision-engineering)

## Install

In a Claude Code session:

```
/plugin marketplace add arches/alloy
/plugin install alloy@alloy
```

Nothing lands in your project. The catalog and both skills live in Claude Code's plugin cache.

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
