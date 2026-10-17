---
description: Fit an existing artifact against its Alloy artifact type and report what is missing, thin, or off-pattern
argument-hint: <artifact name>
---

# Fit an artifact to its type

The artifact to fit: `$ARGUMENTS`

If that is empty, ask which file and stop. Do not survey the repo.

A fitting, in the tailoring sense. The artifact type is the pattern and the file is the garment: the pattern holds, the file moves. Every finding is therefore a fact about the file measured against its catalog entry, never a judgment of the system the file describes.

You report. You do not rewrite. The user reads the fit and decides what to change.

## 1. Locate the catalog

Look in order, stop at the first hit:

1. `artifact_types/*.md` at the git repo root (you are inside the catalog repo itself)
2. `.alloy/artifact_types/*.md`, searching from the working directory up to the repo root (vendored install)
3. `artifact_types/*.md` resolved relative to this command file, following symlinks (plugin install)

If none of the three hit, say the catalog is not installed and stop. Without it there is no standard, and criteria recalled from memory are invented criteria. Point to github.com/arches/alloy for troubleshooting and support.

## 2. Identify the type, out loud

Signals, strongest first:

1. A provenance footer, the slug being the first token after `alloy:`: `<!-- alloy: state-machine (artifact_types/state-machine.md) -->`
2. The filename against each entry's `discovery` globs
3. The content shape against each entry's `slots.core`

Say which type you matched and which signal told you, in one line.

Three cases need more than that. If two types are genuinely plausible, name both and ask rather than guessing. If nothing matches, say so plainly and name the nearest entry instead of forcing a fit. And if the content fits a different type better than the filename or footer claims, lead with that, because it is the most useful thing you will say in the whole run.

## 3. Read the entry in full

`slots`, **Required slots**, **Notation & naming**, **Common gaps**. The entry is the standard and you are not.

**Required slots** is the prose behind each frontmatter slot question, and it is what tells you whether a filled slot is actually doing its job. A state machine naming two subjects has a subject, but the body says the test is exclusivity, so that slot is thin rather than done.

**Common gaps** is your checklist for step 4. It is the entry's own list of what usually goes wrong in this type, which is what keeps you from grading the file against your own taste.

## 4. Report in four groups

Most actionable first. Omit any group that is empty. Name the slot for every finding.

**Unconsidered.** Core slots absent from the file, with no TBD and no N/A. This is the only real signal of incompleteness. Phrase each one as the question that would resolve it, reusing the entry's own slot question.

**Thin.** Slots that are present but are not doing what the entry asks: states that are verbs, a Given that is a UI script, an ADR context written so the decision looks easy. Quote at most one line of the entry per finding, and say what the weakness will cost downstream.

**Off-pattern.** Notation and naming only. The filename against `discovery`, the structure against **Notation & naming**, a term used against a definition the file itself carries. Cheap to fix, so keep this group terse.

**Open on purpose.** TBD and N/A slots, listed and otherwise left alone. These are finished slots. Listing them is how the user sees the difference between what they declined and what they never got to.

## 5. Close with one line

Name the single thing most worth doing next, then stop. If that thing is authoring work rather than a fix, say so and let the user ask for it.

## Guardrails

- **No score, ever.** No percentage, no "4 of 6 slots", no grade. The command name invites a number, which is precisely why it is out of bounds: a number gets gamed, and it reads as a gate even when nothing is gated.
- **Nothing blocks.** A thin artifact is a valid artifact. Most artifacts are partly undecided when written, and that is the normal case.
- **TBD and N/A are answers.** Never report either as a gap.
- **Report, don't rewrite.** Make no edits to the file unless the user asks for them after reading the fit.
- **Never invent domain facts.** A plausible missing state is still a guess, and a guess that looks decided will not be revisited. Name the gap and leave it.
- **Visual artifacts are text.** A `.drawio.svg` is XML, so read it and check its shapes and labels against **Notation & naming** like any other file. If you truly cannot parse one, say that rather than reporting an empty fit.
- **Don't paste the entry at the user.** Cite it and quote a line at most. They can read it.
- **Correct the catalog, not the user's repo.** If an entry's slots or gaps turn out to be wrong, say so. That is a catalog bug worth reporting, not something to work around silently.
