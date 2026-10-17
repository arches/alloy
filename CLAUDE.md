# Alloy

We're building a new way of working with LLMs.

The idea is to bring existing types of system design artifacts together and let the LLM combine them into the code. So e.g. perhaps we author an entity diagram, a state diagram, a sequence diagram, and a list of scenarios that describe our app. Then we'll use LLMs to turn that into code.

## Purpose

This repo will serve as a public reference cataloging established techniques for describing software systems: scenarios, state machines, ADRs, and so on. Each entry explains when to use the artifact type, what a complete one contains, and where to learn more. It serves two audiences:

1. Humans choosing the right artifact for an idea and building the artifact
2. LLM skills that "snap" loose notes into these forms, finding gaps, and generating code

One file per artifact type serves both: frontmatter for the skills, body for the humans.

## Principles

- Curate, don't invent. Every artifact type must be an established technique. Never make one up. This governs the artifact _types_. The notation mappings and naming conventions below are ours, and that's fine: curated types, invented conventions.
- Earn inclusion. An artifact type belongs here if people outside this repo already use its name, and it answers a question no included type already answers. UML alone offers 14 diagram types; without this gate the catalog becomes a list instead of a choice.
- Density. Entries are short and scannable. No filler, no marketing tone.
- Looseness is fine. Entries describe what a complete artifact looks like, not what every artifact must look like on day one.
- Propose, don't scaffold. Artifacts are the source of truth for intent, not for file layout. Generated code lands where the project already puts code, as a reviewable diff.

## Entry template (one Markdown file per artifact type, in `artifact_types/`)

```yaml
name: State machine
slug: state-machine
aka: [statechart, state diagram, finite state machine] # other names for this same thing
discovery: ["*.states.drawio.svg", "*.states.md"] # how a skill finds instances in a project
slots:
  core: # slot name, then the question that fills it, asked verbatim
    subject: "Whose lifecycle is this? Name one thing that is in exactly one state at a time."
  optional: [guards, effects, timeouts] # named only, no questions
```

Frontmatter carries only what a skill reads. Everything a human needs is in the body, and **Notation & naming** is the authority on format, which is why there are no `formats` or `related` fields.

Body sections, in this order:

- **What it is** (a few paragraphs): what the artifact is, and the one discipline that makes it work. This is the teaching content, and it comes first.
- **Use when** (one line)
- **Not when** (one line, naming the better artifact type)
- **A question section**, headed as the question itself: the confusion that most often follows, such as "Glossary or schema?", "ADR or your PR history?", "How big should a set be?". For most types this is the boundary against the nearest neighbor, and it is where the real distinction lives, since **Use when** and **Not when** are only triggers.
- **How you build one**: the order to work in. A skill follows this rather than inventing an interview.
- **How you use it**: what the artifact is for once it exists, in a few bullets.
- **Required slots**: the prose behind each core slot question, and what a good answer has to satisfy. The frontmatter question is what gets asked; this is what the answer gets checked against.
- **Optional slots**
- **Notation & naming**: for visual artifact types, give the notation (e.g. draw.io: rounded box = state; arrow labeled "event / effect"; double border = initial) and a file-naming convention (e.g. `*.states.drawio.svg`).
- **Common gaps** (what's usually missing)
- **Further reading** (where the technique comes from)

## Integration

People must be able to drop this into an existing project without disrupting what they already do.

- **The catalog never spreads into the project.** Reference content is read-only and lives in exactly one directory, `.alloy/`. Install: add that directory, append one pointer line to CLAUDE.md. Uninstall: delete both. If removal takes more than one sentence to describe, the install was too invasive.
- **Discover the user's artifacts, don't relocate them.** Config carries a glob, defaulting to suffix-based discovery anywhere in the repo (`*.states.drawio.svg`, `*.scenarios.md`). The naming convention earns its keep as the discovery mechanism. Projects that already have `docs/design/` keep it.
- **Two install tiers.** Zero-footprint: a Claude Code plugin, nothing in the repo, nothing to collide with. Vendored: commit `.alloy/` with a pinned catalog version so a team shares one definition, and updating is re-vendoring one directory, a contained and reviewable diff.
- **Two entry points, invoked differently.** `alloy:author` is a skill, so it fires on intent and the user never needs to know its name; the artifact types are what they learn, not the tooling. `/alloy:fit <file>` is a command, invoked explicitly, because no conversational signal reliably means "check this existing file against its type."
- **The repo is the plugin.** `.claude-plugin/` holds both `marketplace.json` and `plugin.json`, and the marketplace's single entry has `"source": "./"`, so the marketplace root and the plugin root are the same directory. `artifact_types/`, `skills/`, and `commands/` therefore sit at the top level. The catalog is both the first thing a human browsing the repo sees and a directory genuinely inside the plugin that ships, with no symlink crossing a boundary. The cost is that a second plugin would need a repo of its own.
- **Updates track commits, not releases.** `plugin.json` omits `version` on purpose. Claude Code hands an installed user a new copy only when the computed version string changes, so a `version` left unbumped silently withholds catalog changes from everyone. With it omitted, an update always brings the current catalog. Add a version only once the catalog is stable enough that teams want to pin one, and then never push content without bumping it.
- **No conversion step.** The entry point is one note becoming one artifact. The catalog is a menu; a project with three artifact types filled in is a valid project.
