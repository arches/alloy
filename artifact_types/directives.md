---
name: Directives
slug: directives
aka: [agent instructions, custom instructions, rules file, prompt notes]
discovery: ["DIRECTIVES.md", "*.directives.md"]
slots:
  core:
    instruction: "What do you want done differently this time? One line, imperative."
  optional: [rationale, scope, promote-to]
---

## What it is

A directives file is a list of instructions for the generator, for this session only. "Use the existing Button component." "Skip the admin screens for now." "Don't touch the migration folder." Things you would otherwise type into a prompt and then lose.

Every other artifact in the catalog says what is **true**. A directive says what to **do**. That is the whole distinction and it gives you the test: if the statement would still be true once the code exists, it is not a directive. "Client components never reach the database" holds forever and is a principle. "Use the existing Button component" is meaningless the moment generation finishes.

The second property is that a directive you want twice is a mistake. Writing the same instruction into a second session is the signal that it was never throwaway, and it belongs in an artifact that will still be there next month. The file is a staging area as much as a control channel, and the things that keep reappearing in it are telling you which artifact is thin.

This is deliberately the lowest-ceremony type here. One slot, no template, no completeness to worry about. Adjusting an artifact is always the better move; this is for the cases where that is not worth the trouble, and the cost of the escape hatch is that it has to be cheap enough to use.

**Use when** you need to steer one generation run and the thing you want is not worth writing into an artifact.

**Not when** the instruction expresses something that will still be true next month. Use a principle.

## Directive or principle?

Both are rules the generator obeys, which is why they get confused. They differ on tense and on audience.

A principle describes the system and survives everything: the people, the sessions, the framework. It is written for whoever reads the project later, the generator included. A directive is addressed to one run and expires with it.

Three questions settle it. **Will you want this next month?** If yes, it is a principle. **Does it describe the system or the act of building it?** Build instructions are directives. **Have you written it before?** Twice means it was never ephemeral and you have been using the wrong file.

They also rank differently. A directive outranks the generator's judgment and does not outrank an artifact. When a directive contradicts an artifact, one of the two is wrong, and that is worth raising rather than silently resolving in either direction.

## Why this matters

The risk here is particular to this type: a directives file is the one artifact designed to be deleted, which makes it the worst available place for anything you need later.

And the leak happens by accident rather than by carelessness. You are mid-session, you notice the generator does not know that a cancelled order keeps its line items, and the fastest fix in the world is one line in the directives file. It works. The code comes out right. Then the session ends, the file gets cleared, and that fact is gone from the project. Next month the same gap produces the same wrong code and nobody can reconstruct why it was ever right.

That is the same failure as typing it into the prompt, because it is the same act. **A prompt is not a lighter-weight artifact. It is the thing artifacts replace.** Anything you tell the generator about what the system is, you are saying instead of writing down, and a fact stated in a prompt has the same standing as a fact hand-edited into generated code: it was true once, it survives nothing, and the next run overwrites it. The whole premise here is that you touch the artifacts and never the code, and a prompt is on the code side of that line.

So the test, every time something goes in this file: **am I telling the generator what to do, or telling it what is true?** The second always belongs upstream, however small it looks and however much slower that feels. The feeling is accurate, incidentally. The directive is genuinely faster every single time, which is why this leak is the default rather than the exception, and why the test has to be deliberate rather than left to good judgment in the moment.

## How you build one

1. Write the instruction as one imperative line. "Use X." "Skip Y." "Don't touch Z."
2. Say which part of the system it applies to, if it is not all of it.
3. Delete the file when the session ends. Nothing here is supposed to survive.

That is the whole procedure, and the shortness is the point. A directives file that took effort to write is a sign the content wanted a real artifact.

## How you use it

- **Steering one run** without disturbing anything durable.
- **Deferring work without forgetting it.** "Skip the admin screens for now" keeps the run small and says why the gap is there.
- **Working around something temporary.** A broken dependency, a half-finished migration, a branch you are not ready to touch.
- **Finding thin artifacts.** The directive you keep rewriting is the one that should have been a principle, a technology standards row, or a slot somewhere. Promote it and stop retyping it.

## Required slots

- **Instruction**: one line, imperative, addressed to whoever is generating. The imperative mood is not a style preference, it is the test. A line you cannot phrase as an instruction is a statement about the system, and statements about the system belong in an artifact.

## Optional slots

- **Rationale**: why, for the ones you might otherwise undo by accident. Most directives need none, and a file where every line has a reason is a file that should have been artifacts.
- **Scope**: which part of the system this applies to, when it is not all of it.

## Notation & naming

`DIRECTIVES.md` at the docs root, or `<system>.directives.md` where one repository holds several systems.

A flat list. Markdown bullets, one instruction each. No frontmatter, no sections, no structure worth maintaining.

```
- Use the existing Button component, do not add a new one.
- Skip the admin screens this run.
- Leave the migration folder alone, Sam is mid-change there.
- Prefer composition over inheritance in the new services.
```

Clearing it is the user's job and nothing automatic does it. That makes a stale file the normal failure, so anything reading directives should quote them back before acting on them.

## Common gaps

- **Directives that are really principles.** Written once, meant forever, and then lost when the file gets cleared. The tell is a line that describes the system rather than the work.
- **A file nobody cleared.** Last session's instructions steering this one, silently. The commonest failure by a distance, and the reason tools should echo the file rather than just obey it.
- **Directives that contradict an artifact**, which is always one of two defects and never a thing to quietly resolve.
- **Instructions too vague to act on.** "Make it clean." "Follow best practices." These read as directives and constrain nothing.
- **A file that only grows.** Nothing is ever deleted and nothing is ever promoted, so it becomes an unreviewed standing prompt that nobody remembers agreeing to.
- **The same directive, session after session.** Not a defect in the directive. A defect in whichever artifact should have absorbed it.

## Further reading

The name is ours. The artifact is not, and it is currently one of the most widely written files in software: `CLAUDE.md`, `AGENTS.md`, `.cursorrules`, `.github/copilot-instructions.md`, and every project-local prompt file people keep beside their code. What none of those conventions separate is the durable content from the throwaway content, which is the distinction this entry exists to draw.

- **Agent instruction files** as a category, whichever tool's convention you use. Worth reading your own: most of them are a mix of principles, technology standards, and directives in one undifferentiated list, and separating them is usually the fastest improvement available to a project.
- **The ADR entry in this catalog**, for the other axis. An ADR records a decision at a point in time, a principle states what holds without one, and a directive instructs for a single run. Those three get written into the same file constantly.
