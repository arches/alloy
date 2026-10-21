---
name: Technology standards catalog
slug: technology-standards
aka:
  [
    tech stack,
    technology portfolio,
    standards catalog,
    approved technology list,
  ]
discovery: ["*.stack.md", "STACK.md"]
slots:
  core:
    category: "What kind of choice is this? Name the slot rather than the thing: UI components, package manager, unit tests."
    selection: "Which one? Exactly one per category. If you need two, you have two categories or an unmade decision."
  optional: [decision, excludes, version, status, scope, since]
---

## What it is

A technology standards catalog is a table with one row per kind of choice your system makes, each naming the one thing that fills it. UI components: shadcn/ui. Package manager: npm. Unit tests: vitest. Database access: pg.

This is not your dependency list. A lockfile records what is installed, which includes everything your choices dragged in behind them. A catalog records what was chosen. Most of a `package.json` has no business here, and a few things here may not appear in any manifest at all, such as the hosting platform or the language version. A system with forty categories has either grown a problem or started cataloging its transitive dependencies.

**Use when** more than one person or one generator will add code to the system, which in practice means from the first week.

**Not when** the choice had a reason worth preserving and alternatives you seriously rejected. You can duplicate the result here, but use an ADR to record the decision.

## Catalog or manifest?

The package lockfile or manifest (e.g. `package.json` or `pyproject.toml`) is already a list of technologies, already in the repo, and already accurate about what is installed, so the catalog has to earn the duplication. Three things it does that a manifest cannot:

1. **It has categories.** A manifest is a flat bag of names. Nothing in it says that `tailwindcss` and `shadcn/ui` answer different questions while `tailwindcss` and a second CSS framework would answer the same one. The category column is what turns a name into a rule.
1. **It covers things that are not packages.** The hosting platform, the database, the language version, the CI runner. These are choices with the same force as library choices and they appear in no manifest.
1. **It separates choices from consequences.** A manifest cannot distinguish the twelve things you picked from the four hundred they pulled in. The catalog is only the twelve.

## How you build one

1. Write down only the things someone chose. Skip anything that arrived as a dependency of something else.
2. For each one, name the category it fills before you name the thing. If you cannot name a category, it is probably not a standard, just a package.
3. Collapse any two rows that turn out to share a category. One of them is the answer and the other is either a mistake or a second category you have not named yet.
4. Where a row had a real decision behind it, point at the ADR rather than restating the reasoning here.
5. Add the things that are not in any manifest: hosting, database, language version, anything a generator would otherwise guess at.

A typical README might have something similar already built in. That's a great starting point; just make sure to use the categories.

### Common categories you might want to consider including

This is not a checklist, just a starting point. Many systems will have more/less/different categories than this.

- Language and version
- Data access
- Package manager
- Framework
- Authentication
- Hosting or runtime target
- Data store
- Test runners
- Background work
- Styling and UI components
- Config and secrets
- Logging and observability

## How you use it

- **Generation reaches for the named thing.** This is the artifact that stops a model inventing a second component library, writing raw SQL where you chose a query builder, or reaching for whatever is most common in its training data.
- **Review gets a cheap check.** A pull request that adds a library in a category that already has an answer is visible as a category with two answers, which is a shorter conversation than arguing about taste.
- **Cross-checking against other artifacts.** The same library may appear here as a category answer and also on a component diagram. A disagreement between the two is a useful finding. We don't want to be redundant, but repeating a fact across typed artifacts gives you a check.
- **Onboarding, human or otherwise.** It is the shortest honest answer to "what is this built with," and unlike the README version it says what not to add.

## Required slots

- **Category**: the kind of choice, named as a slot rather than as the thing filling it. "UI components," not "shadcn." The test is whether a second answer to the same category would be a conflict. If it would not, the category is too broad.
- **Selection**: exactly one answer. Two answers in one row is the single most useful defect this artifact surfaces, because it means either an undeclared split or a decision nobody finished. Record it as two rows with narrower categories, or resolve it.

## Optional slots

- **Version**: a floor, a ceiling, or a pin, where the version is itself load-bearing. Omit it when the manifest already pins it and nothing depends on the number.
- **Status**: current, deprecated, or trial. Deprecated is the one that earns its keep: it says "there is some of this in the codebase, do not add more," which neither the manifest nor an empty row can say.
- **Scope**: which elements this applies to, when it is not the whole system. Most rows are system-wide and should say nothing.
- **Decision**: a pointer to the ADR, where one exists. This is the slot that carries a contested choice, and a row without one was a choice nobody argued about.
- **Excludes**: what this rules out, for the rare case where the exclusion is neither implied by the category nor recorded in an ADR. Most rows do not need it, because one answer per category already excludes the alternatives.
- **Since**: when the row was adopted. Useful only on a codebase old enough to have strata.

## Notation & naming

A Markdown table, one row per category.

| Category        | Selection    | Decision                    |
| --------------- | ------------ | --------------------------- |
| Framework       | Next.js      | ADR 0002, over a backend    |
| Language        | TypeScript   |                             |
| UI components   | shadcn/ui    |                             |
| Styling         | Tailwind CSS |                             |
| Database        | PostgreSQL   | ADR 0001, hosted over local |
| Database access | `pg`         | ADR 0003, over an ORM       |
| Unit tests      | vitest       |                             |
| E2E tests       | Playwright   |                             |
| Package manager | npm          |                             |
| Hosting         | Vercel       |                             |

A CSV or a spreadsheet with the same columns is equivalent. Use whichever one people will keep current.

File: `STACK.md` at the root, or `<system>.stack.md` where one repository holds several systems.

## Common gaps

- **No category column.** The commonest failure, because the list already existed in the README and nobody added the column. Without it the artifact is the bare list again and asserts nothing.
- **Two selections in one category, unremarked.** Usually the trace of a migration nobody finished, and the reader cannot tell whether both are blessed or one is on the way out.
- **Transitive dependencies listed as choices.** Thirty rows, most of which nobody picked, and the four that matter are now hard to find.
- **Deprecated technologies absent.** The codebase contains something the catalog does not mention, so a reader concludes it is fine to add more of it.
- **No row for anything outside the manifest.** Hosting, database, and language version get left out because the author built the list from `package.json`, and those are exactly the choices a generator will otherwise guess.

## Further reading

- **TOGAF**, content framework: the Technology Standards Catalog, which records the agreed standards, alongside the Technology Portfolio Catalog, which records what is actually deployed. For one system the two collapse into this table, and the distinction is worth knowing once more than one system shares a standard.
- **Rozanski and Woods**, _Software Systems Architecture_: no catalog by this name, but the Development viewpoint's "standardization of design" concern is the same idea, and third-party software requirements sit in their Deployment viewpoint. Their companion site is viewpoints-and-perspectives.info.
