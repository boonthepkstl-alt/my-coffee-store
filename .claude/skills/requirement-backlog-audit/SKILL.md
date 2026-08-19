---
name: requirement-backlog-audit
description: Audit consistency across the whole pipeline — requirement specs, backlog.md, User Journeys, Prototypes, and Test Cases/Plans — then repair what's safely repairable. Originally scoped to spec↔backlog only; extended to cover the full chain (see checks.md groups 1-4 for spec/backlog, group 5 for journey/prototype/test) rather than standing up a separate traceability-auditor, since the two would duplicate almost all of their logic. Use when asked whether requirements, backlog, journeys, prototypes, or tests are up to date, in sync, or consistent; before planning or a sprint; after several documents have been added or edited; or when any document in the chain looks orphaned or unrepresented. Triggers on "ตรวจสอบ requirement", "backlog ตรงไหม", "up to date", "sync", "ตรวจ backlog", "traceability", "audit ทั้ง pipeline".
---

# Requirement ↔ Backlog audit

Find where the specs and the backlog disagree, then fix what is safe to fix and
escalate what is not. This is a read-heavy task: **read everything before
changing anything.**

## The one rule that decides every repair

`docs/01-requirements/01-spec/` is the **declared source of truth** (see
`CLAUDE.md`). When a spec and the backlog disagree, the backlog is normally
what gets corrected.

**But not always.** Drift sometimes means the *spec* is wrong — a BL id that
exists in the backlog with no spec behind it may mean a spec was deleted, which
this repo forbids. Repairing the backlog there would erase the evidence.

So every finding is sorted into one of two classes, and only one of them is
yours to fix:

| Class | Meaning | Action |
| --- | --- | --- |
| **A — ซ่อมได้เลย** | The spec is clearly right and the backlog simply lags | Fix `backlog.md`, report what changed |
| **B — ต้องให้คนตัดสิน** | Ambiguous which side is wrong, or the spec itself looks broken | **Do not touch anything.** Report and ask |

Never silently "fix" a class B finding to make the audit come out clean. A
clean report that hid a deleted spec is worse than a report with open items.

## Step 1 — Gather

```bash
ls docs/01-requirements/01-spec/
grep -H -m1 '^backlog:' docs/01-requirements/01-spec/2026*.md
grep -H -m1 '^status:'  docs/01-requirements/01-spec/2026*.md
grep -o '^| BL-[0-9]*' docs/01-requirements/backlog.md
```

Read every spec's frontmatter, its section 8 (คำถามที่ยังค้าง) and section 9
(รายการงานใน backlog), plus the whole of `backlog.md`. Also read
`docs/05-log/` entries for the current month — the log is where a deliberate
divergence is usually explained, and a finding the log already justifies is not
a finding.

## Step 2 — Run the checks

Work through `references/checks.md` in order. It holds all checks (spec↔backlog
in groups 1-4, full-pipeline traceability in group 5 covering
journey/prototype/AC-extension/test-case/test-plan), the shell command for
each, and the class (A or B) each one lands in.

If the caller only wants spec↔backlog checked and journeys/prototypes/tests
don't exist yet for the scope in question, group 5 checks simply find nothing
to check — report that plainly rather than skipping the group silently.

Do not stop at the first failure — collect everything, then repair once.
Findings interact: a missing BL row and a stale count are usually one cause.

## Step 3 — Repair class A only

Edit `docs/01-requirements/backlog.md`:

- Add rows a spec declares but the backlog lacks
- Correct a row's source wikilink, priority, or `อัปเดตล่าสุด`
- Re-sort rows by `BL-###` ascending

Constraints that do not bend:

- **Never delete a backlog row.** Cancelled work becomes `สถานะ: ยกเลิก` and stays.
- **Never reuse a `BL-###`**, including ids of cancelled rows.
- Escape the pipe inside a wikilink in a table cell as `\|`.
- Keep the status vocabulary — `ยังไม่เริ่ม` / `กำลังทำ` / `เสร็จแล้ว` / `ยกเลิก`.

When a repair needs a new id, take `max(all ids ever seen) + 1` — scan the log
and `docs/00-archived/` too, not just the current table.

## Step 4 — Escalate class B

Use `AskUserQuestion`. Each question offers **at least 3 concrete approaches**
with the trade-off spelled out, recommendation first, labelled `(แนะนำ)`.
Maximum 4 options and 4 questions per call; write them in Thai.

For a class B finding, the options are usually: correct the spec / correct the
backlog / accept the divergence and record why. "Accept and record" is a real
answer — write it into the log so the next audit does not re-report it.

If the user does not answer, **do not guess.** Leave class B untouched and say
plainly in the report which findings are still open.

## Step 5 — Log it

Append to `docs/05-log/{YYYYMMDD}-log.md` (create if absent; format in
`.claude/skills/requirement-to-backlog/references/backlog-format.md`).

Record what was checked, what drifted, **why it drifted** if you can tell, what
you repaired, and what is still open. An audit that found nothing is still
worth one short entry — it establishes when the two were last known to agree.

## Step 6 — Report back

In Thai, and lead with the verdict, not the process:

- **ตรงกัน** / **ไม่ตรงกัน** — say which immediately
- Table of findings: check id, what is wrong, class, what you did
- What you repaired in `backlog.md`
- What needs their decision, and what happens if they leave it
- Path to the log entry

Never report "up to date" while class B findings are open. Say
"ซ่อมส่วนที่ทำได้แล้ว เหลือ N ข้อที่ต้องตัดสินใจ".

## Delegating

For a large audit, or a scheduled sweep, hand the checks to the
`requirement-auditor` subagent. It cannot ask the user, so it returns class B
findings for you to raise via `AskUserQuestion`.
