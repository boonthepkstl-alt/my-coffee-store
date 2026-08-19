---
name: acceptance-criteria
description: Extend or fill gaps in the Acceptance Criteria that already live in each requirement spec's section 6 — does not create a separate AC document. Use when a prototype/journey needs AC coverage a spec doesn't have yet, or to reformat existing AC as Given/When/Then for test design. Triggers on "acceptance criteria", "AC", "เกณฑ์การยอมรับ", "given when then".
---

# Acceptance Criteria — extend, don't duplicate

**Read this before writing anything:** every spec in this repo already has a
`## 6. เกณฑ์การยอมรับ (Acceptance criteria)` section with numbered, testable
`AC-#` items — 40 across the three current specs. That section **is** the
acceptance criteria deliverable for this repo. Creating a second, separate
"Acceptance Criteria" document would duplicate it, and the two would drift
out of sync the first time either one is edited — exactly the kind of finding
`requirement-backlog-audit` exists to catch.

This skill therefore has two modes, and no third mode that creates a
standalone AC file:

## Mode 1 — Fill a gap in an existing spec's §6

Used when `prototype-audit` (or manual review) finds a user story, business
rule, or edge case with no AC covering it.

1. Open the spec, find §6, find the highest existing `AC-#`.
2. Add new items continuing that numbering — never renumber or reuse an
   existing `AC-#`, for the same reason backlog ids are never reused.
3. Each new AC must be:
   - **Testable** — a tester can mark pass/fail without asking what it meant
   - **Specific** — names the trigger and the exact expected outcome
   - **Traceable** — cite the user story, business rule, or edge case it
     covers
   - Free of implementation detail (no "call `POST /orders`" — that belongs
     in a technical-design document, not here)
4. Update the spec's `updated:` frontmatter date.
5. If the gap traces back to something the user stories/business rules
   themselves don't cover, that's a requirement gap — route it through
   `requirement-to-backlog`, don't invent a business rule here to justify
   the AC.

## Mode 2 — Given/When/Then restatement for test design

`test-writer` sometimes needs AC in Given/When/Then form to derive test steps
cleanly. This mode restates existing `AC-#` items in that form **without
changing their meaning or number** — it produces a companion view, not a new
source of truth.

```text
AC-4 (20260802-01):
  Given a customer has confirmed an order and payment has succeeded
  When the payment confirmation is received
  Then the order must appear on the barista screen within 5 seconds
```

Keep this restatement either inline in the test-case document that needs it,
or as an appendix in the spec itself — never as a competing top-level
document with its own numbering.

## Coverage check before either mode

Before adding anything, check what categories §6 already covers for the
feature in scope: Happy Path, Validation, Negative/Boundary, Permission,
Error, Business Rule. Only add what's missing — do not pad a well-covered
spec with redundant restatements to look thorough.

## Constraints

- Never create a file whose only purpose is to hold acceptance criteria
  separately from its spec.
- Never renumber or delete an existing `AC-#`, even a superseded one — mark it
  superseded in place and explain why, per the repo's never-delete rule.
- If a spec is still `ร่าง` with open questions touching the feature in
  scope, gate exactly as `feature-journey` and `prototype` do — don't write
  AC against a requirement that might still change.

## Output

Report: which spec's §6 was extended (or which AC were restated as
Given/When/Then and where), the new `AC-#` range added, and what remains
uncovered.
