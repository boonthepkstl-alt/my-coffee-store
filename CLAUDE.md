# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Current state

This repository contains **no application code yet** — only the `docs/` documentation scaffold described below, plus a stub `README.md`. There is no package manifest, build tooling, or test runner.

`git` is initialized on branch `main`, with `origin` at https://github.com/boonthepkstl-alt/my-coffee-store.git. `docs/` is opened as an Obsidian vault.

`.obsidian/` settings (`app`, `appearance`, `core-plugins`) are **tracked** so the vault config is shared. `workspace.json` is **gitignored and untracked** — Obsidian rewrites it as panes and cursor positions change, which made the tree look dirty with no real change. Keep it that way; don't re-add it.

Consequently there are **no build, lint, or test commands to document**. When the first code lands, add the real commands to this file rather than assuming a stack.

## `docs/` — the documentation system

`docs/` is the substance of this repository. It is an Obsidian-style vault modeling a **linear project pipeline**, where each stage feeds the next:

```
01-requirements  →  02-design  →  03-testing  →  04-retrospectives
   01-spec              01-prototypes    01-test-plan
   02-plan              02-technical     02-test-result
   03-task

05-log        cross-cutting chronological record (changelog, decision log)
00-archived   superseded documents, retained for history
```

- **`01-requirements/01-spec`** is the declared source of truth for requirements. It flows into `02-plan` (roadmap, phases, priorities), which breaks down into `03-task` (actionable to-dos with status).
- **`02-design/02-technical`** holds the architecture / DB schema / API contracts blueprint — read this first when implementing, and it is the basis for `03-testing/01-test-plan`.
- **`04-retrospectives`** draws its evidence from `03-testing/02-test-result` and `05-log`.

Each `index.md` states its own inputs and outputs at the bottom. Read the relevant `index.md` before adding a document to a folder — it defines what belongs there.

**The pipeline is currently empty scaffolding.** All 14 files under `docs/` are `index.md` folder descriptions and nothing else — there are no actual requirements, plans, tasks, designs, test cases, results, or log entries yet. Nothing defines what "my-coffee-store" is or does. Don't go looking for a spec to implement against; if asked to build something, the requirement has to be established first (and belongs in `docs/01-requirements/01-spec/`).

### Conventions to follow when writing docs

- **Language: Thai.** All existing documentation prose is written in Thai. Match it.
- **Every folder has an `index.md`** acting as its entry point and description. A new subfolder needs one.
- **Numeric prefixes (`01-`, `02-`, …) encode pipeline order**, not arbitrary naming. `00-` is reserved for archived material. Preserve the ordering semantics when adding folders.
- **Links are Obsidian wikilinks with an explicit display label**, using relative paths that always terminate at `index`:
  `[[../02-plan/index|02-plan]]`, `[[../../01-requirements/01-spec/index|01-spec]]`
- **Never delete a document.** Move superseded documents to `docs/00-archived/` to preserve the decision history — this rule is stated explicitly in `docs/00-archived/index.md`.
- **Record significant decisions and events in `docs/05-log/`** with the reasoning, so later work can reconstruct why a choice was made.
