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

1. `.alloy/artifact_types/*.md`, searching from the working directory up to the git repo root. A vendored catalog is a deliberate project-level pin, so it wins.
2. `artifact_types/*.md` at the git repo root. You are inside the catalog repo itself.
3. `artifact_types/*.md` at the plugin root, one directory above this file. From a shell that is `${CLAUDE_PLUGIN_ROOT}/artifact_types/`.

If none of the three hit, say the catalog is not installed and stop. Without it there is no standard, and criteria recalled from memory are invented criteria. Point to github.com/arches/alloy for troubleshooting and support.

## 2. Identify the type, out loud

Signals, strongest first:

1. A provenance footer, the slug being the first token after `alloy:`: `<!-- alloy: state-machine -->`
2. The filename against each entry's `discovery` globs
3. The content shape against each entry's `slots.core`

Say which type you matched and which signal told you, in one line.

Three cases need more than that.

- **Two types genuinely fit.** Name both and ask rather than guessing.
- **The content fits a different type than the filename or footer claims.** Lead with that. It is the most useful thing you will say in the whole run.
- **Nothing in the catalog matches.** Check each entry's `aka` before concluding this, since `*.erd.svg` is a data model under another name. If it still does not match, say so plainly and go to step 3. Do not name a nearest entry: suggesting the closest catalog type to somebody who deliberately authored something else is how a wireframe ends up being graded as a component diagram.

## 3. If it is not a catalog type

People author artifacts the catalog has no entry for, and that is expected rather than a problem. The catalog is a menu, not a boundary.

Look for a companion structure file next to the artifact, named for the same stem: `renegades.wireframes.svg` is described by `renegades.wireframes.structure.md` and `renegades.schema.md` is defined by `renegades.schema.structure.md`. The companion exists because many artifacts cannot carry frontmatter of their own.

**If the structure file exists**, it is the standard for this artifact, exactly as a catalog entry would be. Read it, run step 5 against whatever it declares, and run the check in both directions: the artifact against what the file says is in it, and the file against what the artifact actually contains. A structure file describing a screen that is not in the SVG is as much a finding as a screen in the SVG that nothing describes.

**If it does not exist**, offer to write one, and say what it buys: generation steps can use the artifact instead of guessing at it. Keep the first version small.

```yaml
---
custom: true
name: Wireframe set
describes: renegades.wireframes.svg
binding: "Which screens exist, the fields on each, and how fields are grouped."
illustrative: "Spacing, exact positions, placeholder copy, colour."
---
```

Then a few lines of prose saying what is in the artifact. That is enough to start.

`binding` and `illustrative` are the two that matter most and the two nobody writes unprompted. Without them a generator either treats pixel positions as a specification or ignores the file entirely.

`slots` are optional here. If the user wants them, they take the same shape as a catalog entry's, and a custom type that keeps earning its slots is a candidate for the catalog itself. Say so when it happens: the gaps in the catalog are discovered this way, not guessed.

Never measure a custom artifact against a catalog entry. If the user asks which entry is closest, answer, but do not volunteer it.

## 4. Read the entry in full

`slots`, **Required slots**, **Notation & naming**, **Common gaps**. The entry is the standard and you are not.

**Required slots** is the prose behind each frontmatter slot question, and it is what tells you whether a filled slot is actually doing its job. A state machine naming two subjects has a subject, but the body says the test is exclusivity, so that slot is thin rather than done.

**Common gaps** is your checklist for step 5. It is the entry's own list of what usually goes wrong in this type, which is what keeps you from grading the file against your own taste.

## 5. Report in four groups

Most actionable first. Omit any group that is empty. Name the slot for every finding.

**Unconsidered.** Core slots absent from the file, with no TBD and no N/A. This is the only real signal of incompleteness. Phrase each one as the question that would resolve it, reusing the entry's own slot question.

**Thin.** Slots that are present but are not doing what the entry asks: states that are verbs, a Given that is a UI script, an ADR context written so the decision looks easy. Quote at most one line of the entry per finding, and say what the weakness will cost downstream.

**Off-pattern.** Notation and naming only. The filename against `discovery`, the structure against **Notation & naming**, a term used against a definition the file itself carries. Cheap to fix, so keep this group terse.

**Open on purpose.** TBD and N/A slots, listed and otherwise left alone. These are finished slots. Listing them is how the user sees the difference between what they declined and what they never got to.

## 6. Close with one line

Name the single thing most worth doing next, then stop. If that thing is authoring work rather than a fix, say so and let the user ask for it.

## Guardrails

- **No score, ever.** No percentage, no "4 of 6 slots", no grade. The command name invites a number, which is precisely why it is out of bounds: a number gets gamed, and it reads as a gate even when nothing is gated.
- **Nothing blocks.** A thin artifact is a valid artifact. Most artifacts are partly undecided when written, and that is the normal case.
- **TBD and N/A are answers.** Never report either as a gap.
- **Report, don't rewrite.** Make no edits to the file unless the user asks for them after reading the fit.
- **Never invent domain facts.** A plausible missing state is still a guess, and a guess that looks decided will not be revisited. Name the gap and leave it.
- **Visual artifacts are text.** A `.drawio.svg` is XML, so read it and check its shapes and labels against **Notation & naming** like any other file. If you truly cannot parse one, say that rather than reporting an empty fit.
- **Don't paste the entry at the user.** Cite it and quote a line at most. They can read it.
- **Never force a catalog type.** An artifact that matches no entry is not a defect. Measuring it against the nearest entry produces findings that are all noise, and it teaches the user that the catalog is a boundary rather than a menu.
- **Correct the catalog, not the user's repo.** If an entry's slots or gaps turn out to be wrong, say so. That is a catalog bug worth reporting, not something to work around silently.
