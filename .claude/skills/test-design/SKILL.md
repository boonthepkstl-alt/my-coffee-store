---
name: test-design
description: Create Test Cases and Test Plans under docs/03-testing/01-test-plan/, traced to acceptance criteria and requirements. Covers both the test-writer and test-plan-writer roles as one reusable capability. Use when AC exist (or are being extended) and need test coverage, or when a feature needs an overall test strategy. Triggers on "test case", "test plan", "เคสทดสอบ", "แผนทดสอบ", "shift-left".
---

# Test Design — Test Cases and Test Plans

One skill, two procedures, because they share the same inputs and the same
traceability discipline — kept as one reusable capability per the "skill =
capability, agent = role" split, invoked by two different agents
(`test-writer`, `test-plan-writer`).

Both procedures write into `docs/03-testing/01-test-plan/`, per that folder's
own `index.md` ("แผนการทดสอบและ test case ก่อนลงมือทดสอบ" — plan and cases
share the folder by the repo's own convention, not this skill's invention).

## Shared Step 0 — Gather

Read: the spec(s) in scope (§4 user stories, §5 business rules, §6 AC, §7
edge cases), the journey and prototype if they exist, and every `AC-#` in
scope. Do not write a test case against a story with no AC — extend the AC
first via the `acceptance-criteria` skill.

---

## Procedure A — Test Cases

Path: `docs/03-testing/01-test-plan/{YYYYMMDD}-{NN}-{slug}-test-cases.md`.

Each test case needs every one of these fields — an entry missing one is
incomplete, not "good enough":

```markdown
### TC-<seq>

| Field | Value |
| --- | --- |
| Test ID | TC-<seq> |
| Requirement ID | <spec id> |
| Acceptance Criteria ID | AC-# |
| Title | |
| Pre-condition | |
| Test Steps | 1. ... 2. ... |
| Test Data | |
| Expected Result | |
| Actual Result | *(blank until executed)* |
| Status | ยังไม่ทดสอบ |
```

Cover, for every AC in scope where applicable — not every category applies to
every AC, use judgment, but don't skip a category that clearly applies:

- **Positive** — the AC's stated happy path
- **Negative** — inputs/actions the AC implies must be rejected
- **Boundary** — edges (e.g. the 5-second limit in AC-4 of `20260802-01`,
  the 90-day retention in `20260802-02`)
- **Validation** — malformed/missing input where a form is involved
- **Permission** — role-restricted actions (owner vs. staff, per BL-006)
- **Error** — the spec's §7 edge cases, each one, by name
- **Loading / Empty** — where the prototype defines these states
- **Integration** — where an AC spans two systems (e.g. payment gateway →
  order creation, GA4 consent → tag firing)

A spec's §7 edge-case table is not optional test material — every row there
needs at least one test case. Missing one is a coverage gap to report, not a
scope decision to make silently.

## Procedure B — Test Plan

Path: `docs/03-testing/01-test-plan/{YYYYMMDD}-{NN}-{slug}-test-plan.md`.

```markdown
---
title: <feature> — Test Plan
date: <YYYY-MM-DD>
status: ร่าง
spec: []
test-cases: ["[[<test-cases-file>|...]]"]
---

# <feature> — Test Plan

## Scope
In scope / out of scope for this test effort — mirror the spec's own §2.

## Test Strategy
What kinds of testing apply (functional, integration, edge-case) and why,
given what this feature actually does — not a generic boilerplate list.

## Test Types
Which of Positive/Negative/Boundary/Validation/Permission/Error/Integration
from Procedure A apply here, and roughly how many cases each has.

## Test Environment
`TBD` until the platform is scaffolded — reference ADR-01 for what the
environment will eventually run on; don't invent detail ADR-01 hasn't fixed.

## Test Data
What data (menu items, orders, accounts) the test cases need, at a level a
tester could actually prepare.

## Test Tools
`TBD` — no test runner exists yet (CLAUDE.md's "Current state" section is
authoritative on this; check it before writing this section).

## Risk Management
What's likely to break, and why — reference the spec's §7 edge cases and any
BLOCKER findings from `prototype-audit`.

## Entry Criteria
What must be true before testing starts (e.g. prototype audit passed with no
BLOCKER, AC exist for everything in scope).

## Exit Criteria
What "done testing this feature" means.

## Schedule
`TBD` unless a real date exists — don't invent one.

## Responsibilities
Who does what — `TBD` if no team structure has been decided.

## Defect Management
How a failed test case becomes a tracked issue — reference the repo's
existing backlog mechanism (`requirement-to-backlog`) rather than inventing a
separate bug tracker concept.

## Reporting
How results roll up — `TBD` until `docs/03-testing/02-test-result/` has a
real format (currently empty scaffolding).

## Shift-left note
Testing for this feature does not start when this document is written — it
starts at requirement time (the spec's §7 edge cases are already test
material) and continues through design (`prototype-audit`'s findings feed
back into Risk Management here). This document formalizes coverage; it does
not originate it.
```

## Traceability discipline for both procedures

- Every test case must trace to a real `AC-#` — no orphan test cases.
- Every `AC-#` in scope must have at least one test case — no uncovered AC.
- No duplicate test case (same AC + same scenario) — check existing test-case
  documents for the feature before adding.
- If AC coverage is incomplete, say so and stop rather than writing a test
  plan that claims full coverage it doesn't have.

## Output

Report: test cases created (count and ID range), AC coverage (`covered/total`
for the scope), test plan path if produced, and anything left uncovered.
