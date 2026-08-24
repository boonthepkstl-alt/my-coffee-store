# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Current state

This repository contains **no application code yet** — only the `docs/` documentation scaffold described below, plus a stub `README.md`. There is no package manifest, build tooling, or test runner.

`git` is initialized on branch `main`, with `origin` at https://github.com/boonthepkstl-alt/my-coffee-store.git. `docs/` is opened as an Obsidian vault.

`.obsidian/` settings (`app`, `appearance`, `core-plugins`) are **tracked** so the vault config is shared. `workspace.json` is **gitignored and untracked** — Obsidian rewrites it as panes and cursor positions change, which made the tree look dirty with no real change. Keep it that way; don't re-add it.

**The stack is decided but not yet scaffolded.** [ADR-01](docs/00-archived/20260802-01-adr-platform-stack.md) (2026-08-02) originally settled a unified TypeScript/Next.js app (frontend + backend + long-running Node runtime) — it has since been **superseded** by [ADR-03](docs/02-design/02-technical/20260824-04-adr-revise-backend-go.md) (`ยอมรับแล้ว`, 2026-08-24), which splits the platform into a React/Next.js (TypeScript) frontend and a separate **Go** backend service (Gin for HTTP, gorilla/websocket for the held-open order-to-barista-screen WebSocket, sqlc + pgx for DB access), still on PostgreSQL and a Thailand-based VPS (now running two processes instead of one). No `npm create next-app` or Go module has been scaffolded yet, so there is still no `package.json`/`go.mod` and still no real build/lint/test command to give. Once the project is scaffolded, replace this paragraph with the actual commands — do not assume a stack beyond what ADR-03 states, and do not invent commands before the scaffold exists.

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
- **[DESIGN.md](DESIGN.md)** (repo root, outside `docs/`) is the single source of truth for UI design tokens and component patterns — colors, typography, spacing, and per-component rules (Table vs Card, Button, Badge, etc.), each decided with a stated reason and logged in its own §7. Read it before touching any UI, and never invent a token or pattern it marks `TBD`.
- **`04-retrospectives`** draws its evidence from `03-testing/02-test-result` and `05-log`.

Each `index.md` states its own inputs and outputs at the bottom. Read the relevant `index.md` before adding a document to a folder — it defines what belongs there.

**The pipeline is no longer empty, but coverage is uneven.** `docs/01-requirements/01-spec/` holds three specs (table self-ordering, PDPA/IT law, cookie consent — all `status: ร่าง`, all with open questions in their own section 8) plus `docs/01-requirements/backlog.md` with 17 items. `docs/02-design/02-technical/` holds two accepted ADRs (platform stack, payment gateway) plus one architecture/API/database-schema design document — the design doc has sections explicitly marked `[สมมติฐาน]` where they depend on spec `02`'s still-open Q1/Q3, so don't treat those parts as settled. `docs/02-design/01-prototypes/` holds journeys and prototype specs for two features (BL-001, BL-005); the rest of the backlog has neither yet. `01-requirements/02-plan`, `03-task`, all of `03-testing`, `04-retrospectives`, and `00-archived` are still just `index.md`. Don't assume a folder is populated — check it — and don't treat a `ร่าง` spec's open questions as settled.

### Conventions to follow when writing docs

- **Language: Thai.** All existing documentation prose is written in Thai. Match it.
- **Every folder has an `index.md`** acting as its entry point and description. A new subfolder needs one.
- **Numeric prefixes (`01-`, `02-`, …) encode pipeline order**, not arbitrary naming. `00-` is reserved for archived material. Preserve the ordering semantics when adding folders.
- **Links are Obsidian wikilinks with an explicit display label**, using relative paths that always terminate at `index`:
  `[[../02-plan/index|02-plan]]`, `[[../../01-requirements/01-spec/index|01-spec]]`
- **Never delete a document.** Move superseded documents to `docs/00-archived/` to preserve the decision history — this rule is stated explicitly in `docs/00-archived/index.md`.
- **Record significant decisions and events in `docs/05-log/`** with the reasoning, so later work can reconstruct why a choice was made.
