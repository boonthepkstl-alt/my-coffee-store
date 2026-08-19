---
name: prototype-audit
description: Audit a Prototype Specification against its requirement, journey, and DESIGN.md. Produces a PASS/WARNING/GAP/BLOCKER report — requirement coverage, design compliance, UX-state coverage. Use after prototype-writer produces a spec and before it's treated as ready to build from. Triggers on "audit prototype", "ตรวจ prototype", "prototype review".
---

# Prototype Audit

This is a **separate pass from `prototype`** by design — the writer and the
auditor must not be the same act, or gaps the writer didn't notice won't be
caught. Do not skip this step even if the prototype "looks obviously fine."

## What to check, in order

### 1. Requirement coverage

```text
Requirement (spec §4 user stories, §6 AC)
    ↓
Journey
    ↓
Prototype
```

For every user story and every AC in scope, confirm a screen or interaction in
the prototype satisfies it. Build a table:

| US / AC | Covered by (screen) | Verdict |
| --- | --- | --- |
| US-2 | Screen: เมนู | PASS |
| AC-7 | — | **GAP** |

### 2. Design compliance

```text
Prototype
    ↓
DESIGN.md
```

Check every component name used in the prototype against `DESIGN.md` §3.

| Finding | Verdict |
| --- | --- |
| Component name exists in DESIGN.md §3 | PASS |
| Component used but marked `TBD`/unspecified in DESIGN.md (e.g. no token value, no defined variant) | **WARNING** — not wrong, but nothing to check it against |
| Component invented, not in DESIGN.md §3 at all | **GAP** — should have been flagged in the prototype's own §4, not silently used |
| Prototype assigns a real color/font/spacing value where DESIGN.md says `TBD` | **BLOCKER** — this is exactly the "invented design" failure mode Rule 1 exists to prevent |
| Prototype violates a binding UX rule from DESIGN.md §4 (e.g. cookie reject button less prominent than accept) | **BLOCKER** |

### 3. UX / state coverage

Check every applicable state is present per screen: Happy Path, Validation,
Error, Empty, Loading, Permission, Confirmation, Success. Cross-reference the
spec's §7 edge-case table (or the journey's §4) — every edge case listed there
must map to a state in some screen.

| Edge case (spec §7 / journey §4) | Covered by screen/state | Verdict |
| --- | --- | --- |
| | | |

### 4. Responsive / accessibility

Check the prototype addressed both known device contexts (customer phone,
staff/barista larger screen — DESIGN.md §2) rather than assuming one. Where
DESIGN.md marks accessibility `TBD`, do not fail the prototype for not having
accessibility detail — flag it as an **inherited gap** (WARNING, not GAP),
since the prototype can't specify what the design system hasn't decided yet.

## Verdict definitions

- **PASS** — covered, consistent, nothing to flag.
- **WARNING** — covered but resting on an upstream `TBD`; not the prototype's
  fault, but worth tracking so it isn't lost.
- **GAP** — a requirement/state/edge-case exists and nothing in the prototype
  addresses it. Fixable by extending the prototype.
- **BLOCKER** — the prototype actively invents design or violates a binding
  rule. Must be fixed before this prototype is used for anything downstream.

## Output

Write the audit report inline in the response (or as a dated note under
`docs/05-log/` if the caller wants it persisted) using this shape:

```text
PROTOTYPE AUDIT — <prototype path>

Requirement coverage: <n PASS> / <n GAP>
Design compliance:    <n PASS> / <n WARNING> / <n GAP> / <n BLOCKER>
UX/state coverage:    <n PASS> / <n GAP>

<the four tables above, filled in>

Overall: <PASS overall | NEEDS FIXES | BLOCKED>
```

Never report "PASS overall" while any BLOCKER is open. A prototype with only
WARNINGs (all traceable to an upstream `TBD`) can be reported as usable with
caveats — say so explicitly rather than rounding up to a clean pass.

## Constraints

- This skill does not rewrite the prototype, the journey, or the spec. It
  reports findings. Fixes are the writer's job, on request.
- Do not invent a requirement to justify a GAP, and do not soften a BLOCKER
  because fixing it is inconvenient.
