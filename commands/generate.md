---
description: Plan and generate code from the artifacts, as a reviewable series of small self-contained commits
argument-hint: "[what to build: a scenario, an entity, a feature, or nothing for a proposal]"
---

# Generate from the artifacts

What to build: `$ARGUMENTS`. If empty, propose a scope and ask.

The artifacts are the specification. This command reads them and writes code.

It also reads the existing codebase, for exactly one purpose: to learn **where things go and how they are written**. Directory layout, naming, framework idiom, test conventions. Never to learn what the system should do. If the code and the artifacts disagree about behavior, the artifacts are right and the disagreement is worth reporting.

Two phases, and the first one does not end until the user says so.

## 1. Find the change

Generation is usually incremental. Somebody edited an artifact, and the job is to bring the code up to it. So start from the artifact diff, not from the whole set.

1. **Uncommitted artifact changes**, if any. This is the common case during a working session.
2. Otherwise, **the most recent commit touching any artifact file**. Find it with the entries' `discovery` globs. Name its ID in the plan and in every commit you generate, because that is the link between a line of code and the decision it came from.

That artifact diff is what to build. The rest of the set is the context it only makes sense inside: you cannot generate a state machine change without the data model, even though the data model did not change.

So read everything. Locate the catalog the same way `/alloy:fit` does, collect every artifact through the `discovery` globs, plus any `*.structure.md` and the files they describe, plus anything else in /docs, plus any directives (see below).

If the diff is empty and no artifact has ever been committed, say so and ask what to build.

## 2. Directives

Before planning, read `DIRECTIVES.md` if the project has one. Directives are the user's throwaway control channel: things they want for this run that are not worth writing into an artifact. "Use the existing Button component." "Skip the admin screens for now."

They outrank your judgment and they do not outrank an artifact. A directive that contradicts an artifact is worth raising, because one of the two is wrong and the user may not have noticed.

They are session-scoped by design. Do not treat them as durable, do not fold them into the plan as if they were specification, and do not expect them to be there next time.

Clearing the file is the user's job, which means it is sometimes a leftover from last session rather than instructions for this one. So quote the directives back in the plan, in full. A stale directive that gets obeyed silently is indistinguishable from one the user meant, and showing them costs three lines.

## 3. Keep going

Artifacts will contradict each other and artifacts will be incomplete. Both are normal rather than exceptional, and stopping on either would mean never generating anything. Three moves, in order of preference.

**Scope around it.** The first and best resolution. If the state machine and the data model disagree about one entity, generate the parts that are unambiguous and leave that entity out of this run. Narrowing the scope resolves a contradiction without anybody having to guess which side is right, and it is almost always available.

**Assume, and record it.** When scoping around it would gut the run, pick the reading that is most consistent with the rest of the set, continue, and write it down. Every assumption goes in the assumptions file described below, as you make it, not at the end.

**Stop only when neither works.** A contradiction at the centre of what you were asked to build, where every reading produces a materially different system. Say which artifacts disagree and what each would imply, and wait.

The thing that makes this safe is not avoiding assumptions, it is that none of them is silent.

## 4. Propose a plan

The plan is the deliverable of this phase. Present it in the conversation. Write it to a file only if the user asks.

**Scope.** What this run builds, and what it leaves. Generating a whole system in one pass produces a diff nobody can review, which defeats the point. Propose a slice that stands on its own and say what you are deferring.

**What the artifacts do not say.** Every place you will have to assume something, with the artifact that should own the decision. This is the most valuable part of the plan and belongs near the top, because it is what sends the user back to the artifacts before any code exists. Say what you intend to assume, so the user can correct it now rather than reading it out of the diff later.

**What is contested, and how you scoped around it.** Any contradiction you found, and the narrowing you propose to avoid having to resolve it. If you could not narrow around one, say what you intend to assume and why.

**Where the code lands.** Name the directories and files, matched to what the project already does. If the project is empty there is no convention to match, so propose a layout explicitly and expect to discuss it rather than assuming one.

**The commit sequence.** A numbered list. For each: one line on what it does, which artifact it derives from, and what its tests cover.

**The test strategy**, per the section below.

## 5. Iterate

Expect several rounds. The rule that makes this work:

**When the plan is wrong because an artifact is wrong, fix the artifact.** The user says orders should be cancellable after shipping. That is not a note in the plan, it is a change to `order.states.md`, and the plan is regenerated from the changed artifact. Propose the artifact edit, make it once agreed, then re-plan.

A plan that quietly carries a correction the artifacts do not reflect is a plan that generates code nobody can trace back to anything, and the next run will undo it.

Only three kinds of change belong in the plan itself rather than in an artifact: the scope of this run, the commit sequence, and where files land. Everything else about what the system does belongs upstream.

Do not generate anything until the user says to.

## 6. Generate, commit by commit

Work the sequence in order. After each commit, say in one line what it did, and continue. Expect to be interrupted.

**Every commit leaves the tree green.** It builds, the tests pass, nothing is half-wired. A commit that needs the next one to work is not self-contained, and reviewing it separately is then impossible, which is the whole reason for the sequence.

**One commit does one thing, and the thing is either structure or behavior, never both.** Make the change easy, then make the easy change. A commit that reshapes code without changing what it does, followed by a commit that changes what it does without reshaping anything. The moment one diff carries both, the behavior change is hidden inside the noise of the move, and that is the commonest reason a diff cannot be reviewed. If a commit message needs the word "and," it is two commits.

**Pair them, do not batch them.** Prepare for one change, make that change, then prepare for the next. A run that does all the restructuring first and all the behavior afterwards puts every revealing commit at the end, which is exactly backwards for somebody reading in order to find the mistake.

**Within that, order so a wrong assumption surfaces early.** Dependencies constrain this, but where there is a choice, put the commits that would most change the plan if they are wrong at the front. Finding the mistake at commit two is worth more than a tidier sequence that hides it until commit twenty.

**Each commit message cites its artifact.** The subject says what changed; the body names the artifact file it derives from and the artifact commit id from step 1. That trail is what lets a reviewer check the code against the specification rather than against their expectations.

**When the user flags something, stop and go back to the artifact.** Not to the code. The same rule as step 5, and this is where it gets tested, because fixing the code directly is always faster in the moment and always wrong.

## The assumptions file

Write it as you go, not at the end. A run that gets interrupted at commit four must still leave a record of the four assumptions it made, and an assumptions file compiled afterwards from memory is a different and worse document.

It lives at the docs root as `ASSUMPTIONS.md`, and it is committed along with the code it explains. One entry per assumption: a one-line title and four fields.

```
Orders cancelled after dispatch

Assumed:  they cannot. Cancellation is rejected once state is shipped.
Because:  order.states.md has no transition out of shipped, but
          checkout.scenarios.md has a cancel path with no state named.
Applied:  commit 4, OrderService.cancel
```

The file is a queue, not an archive. An entry leaves it when the user promotes it into the artifact that owns it, or when they mark it deliberately out of scope. A file that only ever grows has stopped being read.

Entries from earlier runs may still be sitting there, since they only leave when somebody deals with them. Those are pending, not stale, so leave them alone and do not re-litigate them.

At the end of the run, bring the user the assumptions **this run** made, shortest first, and ask nothing. They will act on the ones that matter.

## Test strategy

Scenarios are the positive space: the happy path and the alternate paths somebody thought to name. Tests have to cover the negative space too, and the artifacts already contain it. Derive, do not improvise.

| Source                                    | Cases                                                           |
| ----------------------------------------- | --------------------------------------------------------------- |
| Data model, optionality                   | every optional attribute absent, and every required one missing |
| Data model, semantic types                | one invalid value per type, not an exhaustive fuzz              |
| Data model, invariants                    | one violation per invariant                                     |
| Data model, identity                      | a duplicate of whatever identity says is unique                 |
| State machine, transitions                | every legal transition once                                     |
| State machine, illegal pairs              | every state and event pair the machine does not permit          |
| Component diagram, forbidden interactions | one test per rule, asserting it cannot happen                   |
| Principles with an `enforcement` slot     | the check that slot names                                       |
| Scenarios                                 | each scenario, including every alternate path                   |

This is what "efficiently" means: the set is finite because the artifacts are finite. One case per stated constraint, not a combinatorial sweep. A boundary nobody specified does not get a test, it gets a line in **what the artifacts do not say**.

Tests and their code go in the same commit. A commit that adds failing tests does not leave the tree green.

## Guardrails

- **Never invent anything silently.** Assumptions are expected and necessary; unrecorded ones are the defect. Every one goes in `ASSUMPTIONS.md` as you make it, with the artifact that should have answered it. An assumption nobody can see looks exactly like a specification.
- **Never edit an artifact without saying so.** Propose it, get agreement, then make the change and re-plan. The artifacts are the user's, and a generator that edits its own specification is not a generator.
- **Read code for convention, never for intent.** Where it goes and how it is written, yes. What the system does, never.
- **Do not scaffold over an existing layout.** Match what the project does. Propose a structure only where there is none.
- **No commit that spans two artifact decisions**, however small each one is.
- **Do not pad the test suite.** Tests derive from stated constraints. A test nothing in the artifacts calls for is a test that will be wrong when the artifacts change.
- **Report what you could not generate**, and why, rather than approximating it. An approximation that compiles is harder to find than an absence.
