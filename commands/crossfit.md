---
description: Check every artifact against every other one and report contradictions, gaps, and orphans across the set
argument-hint: "[directory to scope to]"
---

# Cross-check the artifact set

Scope: `$ARGUMENTS`. If empty, the whole repository.

`/alloy:fit` asks whether one artifact is complete. This asks whether the artifacts agree with each other. Nothing here is about any single file being thin. It runs in two passes: a mechanical one that catches disagreements sharing a name, and a reading of the whole set that catches the ones that do not.

**The artifacts are the only source of information.** Do not open source files, migrations, schema dumps, configuration, or the database. If a disagreement can only be found by reading code, it is out of scope and belongs to a different exercise. The question here is whether the design is coherent with itself, which is answerable before any code exists and is worth answering then.

You report. You do not rewrite.

## 1. Locate the catalog

Look in order, stop at the first hit:

1. `.alloy/artifact_types/*.md`, searching from the working directory up to the git repo root.
2. `artifact_types/*.md` at the git repo root. You are inside the catalog repo itself.
3. `artifact_types/*.md` at the plugin root, one directory above this file. From a shell that is `${CLAUDE_PLUGIN_ROOT}/artifact_types/`.

If none hit, say the catalog is not installed and stop. Point to github.com/arches/alloy for support.

## 2. Gather the artifacts

Collect every entry's `discovery` globs and run them across the scope. Then find custom artifacts: any `*.structure.md`, together with the file each one `describes`. Then find ad hoc artifacts: any other files in the directory of interest.

Say what you found, as a list, before analyzing anything. The user needs to know what was in scope, because a file you failed to discover is invisible in the report and looks like agreement.

Mark the ones that are still skeletons, meaning nothing filled in outside their scaffolding comments. They take no part in anything below, since a file with no content cannot agree or disagree with another. **If every artifact is a skeleton, say so and stop.** An empty set produces a report with no findings, and a report with no findings reads as a coherent design rather than an undescribed one.

If you found one artifact, say there is nothing to cross-check and stop. If you found none, say the discovery globs matched nothing and name a few of the globs, since the usual cause is a naming convention that was never adopted.

## 3. Build the shared vocabulary

Read every artifact in full, then list the named things that appear in more than one of them. These are what the whole exercise turns on:

entity names, state names, element names, actor and role names, external system names, environment names, technology selections, term definitions, screen names.

A name appearing in exactly one artifact is not yet interesting. A name appearing in two is where every finding comes from.

Read content only. A name that appears in a scaffolding comment, a placeholder, or a worked notation example is not in the artifact's vocabulary, and two skeletons sharing an `ALPHA` from the same example are not two artifacts agreeing about anything.

## 4. Find the disagreements

Three kinds, and the difference between them is how certain you can be.

**Contradictions.** Two artifacts name the same thing and say incompatible things about it. These are decisive: one of the two is wrong, always, and saying which is usually the user's call rather than yours.

The checks that fall out of the catalog as it stands:

- A state machine's states against the data model's enumerated values for the same entity. The same list written twice, and the cheapest check in the set.
- A technology standards selection against a technology named on a component diagram.
- A context diagram's external systems against a component diagram's external dependencies.
- A scenario's Given, or the `Background` a whole set shares, naming a state the state machine does not have.
- A scenario's actor against the roles on the context diagram.
- An ADR's decision against a technology standards selection, or against a principle it never claims to supersede.
- A deployment view's forbidden connections against a component diagram's.
- Any artifact using a term in a sense the glossary's definition does not support, or using a name the glossary records as retired.

Do not treat that list as exhaustive. The general rule generates the rest: **two artifacts contradict when they both name the same thing and disagree about it**, and new artifact types bring new instances of the same rule.

**Gaps.** One artifact depends on something no artifact provides. A state machine whose subject is an entity no data model defines. A component diagram element responsible for data nothing models. A scenario asserting an observable outcome nothing could store.

Only report a missing artifact type when an existing artifact depends on it. "You have no wireframes" is not a finding; a set of artifacts is a menu and a project with three of them is a valid project. "Your state machine's subject is Order and nothing defines what an Order holds" is a finding, because something already reached for it.

**Orphans.** A name that appears in exactly one artifact and nowhere else. Weak evidence: it is often perfectly fine, occasionally the trace of something deleted elsewhere. Report these last, briefly, and never as defects.

## 5. Then step back

Step 4 only catches disagreements that share a name, and plenty do not. Read the set once more as a whole, as somebody meeting this system for the first time.

The point of this pass is timing. A question raised here costs one line in one artifact. The same question discovered after generation costs a regeneration, and arrives as "I never said it should do that." Surfacing it early is the entire value, which means erring toward mentioning something slightly obvious beats staying quiet about something that might have mattered.

Answer four questions in your own words.

**Could someone build this without asking you anything?** The sharpest version of the question, because the audience is a generator and a generator will not ask, it will invent. Walk the set looking for the places where something would have to be made up: an unstated default, an unnamed behavior, a decision everybody deferred. Name them specifically. "This is underspecified" is not a finding. "Nothing says what happens to a part-shipped order that is then cancelled" is.

A `TBD` is not one of these. The user wrote it, so reading it back is a completeness finding with one artifact open, which belongs to `/alloy:fit`. A TBD becomes a finding here only when something else leans on it: a scenario whose Given names a state the state machine has left open, a component responsible for data the data model has not decided how to hold. Then the finding is the dependency, not the TBD, and it is worth more than either artifact shows alone.

**Do any two artifacts imply different systems?** This is where the subtle disagreements live, and they never collide on a name. A principle saying complexity must be earned, beside a component diagram with fourteen elements. A scenario implying live updates, beside a deployment view with no mechanism for them. An ADR justified by scale, beside a deployment view with one instance of everything. Each artifact looks fine alone and together they describe two different projects.

**Is the set lopsided?** Ten ADRs and no scenarios means every decision was recorded and the purpose never was. A detailed data model and no behavior means somebody modeled the storage without saying what it was for. The proportions are a fact about where the attention went, and they are visible only from above.

**What is odd?** Open on purpose. An entity with forty attributes. Four scenarios that all exercise one screen. A glossary with a single term. Anything that is not wrong but would make you ask a question if a colleague showed it to you.

Everything from this step is an impression rather than a finding, and has to be labeled that way. Step 4 is decisive because a name either matches or it does not. This is judgment, and a confident wrong judgment costs more than an uncertain right one. Write "this reads as" rather than "this is."

Phrase each one as the question it should prompt, so the user can answer it in a line and get on with it. The output of a good sweep is a handful of decisions somebody can make in five minutes, not a critique of their work.

## 6. Report

Four groups, most certain first. Omit any that is empty. For every finding in the first three, name both files and quote the conflicting text from each, because a disagreement nobody can see is a disagreement nobody will act on.

```
CONTRADICTION  order.states.md / orders.data.md
  states:      draft, submitted, paid, shipped, cancelled
  enumeration: draft, submitted, shipped, cancelled
  The data model has no paid. One of them is wrong.
```

State the disagreement. Do not pick a winner, with one exception: the glossary is definitionally authoritative on which spelling of a term is current, so a retired alias used elsewhere is a defect in the other artifact rather than an open question.

Then **Impressions**, last and visibly separated from the rest, carrying step 5 in a few paragraphs of plain prose. Not bullets: the value of that pass is in connecting things, and a list breaks exactly the connections it exists to make.

Close with the single finding most worth acting on, then stop.

## Guardrails

- **Artifacts only.** No source files, no migrations, no config, no database. The codebase is a separate exercise and mixing them makes it impossible to tell whether the design is incoherent or merely unimplemented.
- **Not a second fit.** If a finding would be identical with only one artifact open, it is a completeness finding and belongs to `/alloy:fit`. This command reports relationships, and a report full of per-file thinness buries the ones that matter.
- **No score.** No count of conflicts as a health metric, no percentage of agreement. The findings are the output.
- **Never invent the resolution.** Name the disagreement and leave it. Choosing which artifact is correct usually requires knowing something neither file says, and a confident wrong resolution is worse than an open question.
- **Absence is not disagreement.** An artifact type nobody has written is not a contradiction. Only report it when an existing artifact already reached for it.
- **Custom artifacts take part, through their `binding` content only.** What a structure file marks as illustrative cannot contradict anything, by its own declaration.
- **The sweep is not a place to shop for artifact types.** "You should write a deployment view" is only a finding if something already reached for one. Otherwise it is a menu being read as a checklist.
- **Say what you could not read.** An artifact you failed to parse is a hole in the analysis, and a report that silently omits it reads as a clean bill of health.
