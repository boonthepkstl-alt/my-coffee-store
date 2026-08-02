---
name: technical-design
description: Turn approved requirement specs into technical design documents under docs/02-design/02-technical/ — architecture, database schema, API contracts, and the technology decisions behind them, each traced back to the business rules and acceptance criteria it satisfies. Use when asked to design the system, choose a stack, model the database, define APIs, or write an ADR; also when a spec is ready and the next pipeline stage is design. Triggers on "technical design", "ออกแบบระบบ", "architecture", "database schema", "API", "เลือก stack", "ADR", "พิมพ์เขียว".
---

# Technical design

Convert requirement specs into the blueprint developers code against. Per
`docs/02-design/02-technical/index.md` this folder is also the basis for
`docs/03-testing/01-test-plan/`, so **a design that cannot be traced to
acceptance criteria is not finished** — the test plan is built from that trace.

Output goes to `docs/02-design/02-technical/{YYYYMMDD}-{NN}-{slug}.md`, same
naming rules as specs: date from `date +%Y%m%d`, `NN` running per day starting
at `01`, ASCII kebab-case slug, Thai title in frontmatter.

## Two document types — pick deliberately

| Type | When | Template |
| --- | --- | --- |
| **ADR** — บันทึกการตัดสินใจ | One standalone decision that many things depend on: stack, database engine, payment provider, hosting | `references/adr-template.md` |
| **Design** — เอกสารออกแบบ | Architecture, schema, and API contracts for a feature area | `references/design-template.md` |

Slug an ADR `adr-{topic}` so the two are distinguishable in a directory listing.

A decision that other documents will need to cite belongs in its own ADR, not
buried in a design section. If you find yourself writing "as decided in section
5.3 of the ordering design", that decision should have been an ADR.

## Step 1 — Gate on the source spec

Read the spec you are designing from, and check its section 8.

```bash
ls docs/01-requirements/01-spec/
grep -H -m1 '^status:' docs/01-requirements/01-spec/2026*.md
```

**Status alone is not the gate.** A spec at `ร่าง` can still be designed from
when its open questions do not touch architecture. What matters is whether an
open question would change the design:

| Open question affects | Action |
| --- | --- |
| Architecture, data model, or an external integration | **Stop.** Designing on it wastes the work. Report which question blocks what |
| Only volume, copy, or a later phase | Proceed. Record the question under `## ที่ยังตัดสินใจไม่ได้` in the design |
| Still carries `[สมมติฐาน]` markers | **Stop.** The requirement is a guess; design built on it is a guess squared |

Say which case applies and why before writing. If a spec is entirely
unblocked, say that too.

## Step 2 — Ask before choosing

Technology choices are expensive to reverse and are almost never implied by a
requirement. Use `AskUserQuestion`: **at least 3 concrete approaches** per
question, each with the trade-off and what it costs later, recommendation first
labelled `(แนะนำ)`. Max 4 options and 4 questions per call, written in Thai.

Ask about, at minimum, whatever of these the design touches and the repo has
not already settled:

- Language and framework, frontend and backend
- Database engine, and relational versus document
- Hosting and deployment target
- Any external service the spec forces (payment gateway, analytics, storage)

**This repository has no stack, no package manifest, and no test runner yet.**
The first design here therefore has to settle that, and it deserves its own
ADR rather than a line in a design document.

Prefer boring, well-documented technology unless the requirement genuinely
demands otherwise, and say that is why. A stack nobody on the team knows is a
schedule risk that a design document cannot mitigate.

If the user does not answer, write the design on the recommended option, mark
every dependent part `[สมมติฐาน]`, and list the question under
`## ที่ยังตัดสินใจไม่ได้`. Never present an unanswered choice as settled.

## Step 3 — Write the design

Use the template verbatim. Two sections are the ones that make it useful:

**Traceability (mandatory).** Every BR and AC in scope maps to the design
element that satisfies it. There are currently 36 BR and 40 AC across the three
specs — an incomplete table is the main way this deliverable fails.

| ข้อกำหนด | องค์ประกอบที่รองรับ | หมายเหตุ |
| --- | --- | --- |
| AC-4 (`01`) | `POST /orders` + WebSocket `order.created` | ต้องส่งถึงหน้าจอบาริสต้าภายใน 5 วินาที |

Any BR or AC with no row is either unaddressed or out of this document's scope
— state which. Do not leave it absent.

**Scope guard.** If the design needs something no spec asks for, that is a new
requirement, not a design detail. Stop and route it through
`requirement-to-backlog` instead of inventing it here. Note it under
`## สิ่งที่ต้องกลับไปเพิ่มใน requirement`.

Write the schema and the API contract concretely enough to implement — real
table and column names, real endpoints, real status codes. "จัดเก็บข้อมูล
ออร์เดอร์" is not a schema.

## Step 4 — Cross-link both directions

- Design frontmatter: `spec:` listing the source specs as wikilinks
- Source spec section 10: add a link to the new design document

The audit skill's check C13 treats one-way links as drift, so add both.

## Step 5 — Update CLAUDE.md when the stack lands

`CLAUDE.md` states that this repo has no build, lint, or test commands, and
says to add the real ones when the first code lands. **Choosing the stack in an
ADR is that moment.** Add the commands the chosen stack will use, and replace
the "no application code yet" claim once it stops being true.

Skipping this leaves CLAUDE.md telling the next session there is no stack, after
the stack was decided.

## Step 6 — Log it

Append to `docs/05-log/{YYYYMMDD}-log.md` (format in
`.claude/skills/requirement-to-backlog/references/backlog-format.md`).

Record the reasoning: the step 1 gate decision, what the user chose in step 2
**and what was rejected**, and anything left open. For a technical decision the
rejected alternatives are the most valuable thing in the entry — they are what
stops the same debate reopening in three months.

## Step 7 — Report back

In Thai: the document path, which spec it designs from, the technology decided
and what was rejected, traceability coverage as a count (`AC ที่ครอบคลุม: 34/40`),
what is still open, and whether CLAUDE.md needed updating.

Do not claim the design is ready to build from while
`## ที่ยังตัดสินใจไม่ได้` has entries, or while traceability is incomplete.

## Next stage

The design feeds `docs/03-testing/01-test-plan/`. Leave the traceability table
in a shape a test planner can read straight off — that is the handoff.
