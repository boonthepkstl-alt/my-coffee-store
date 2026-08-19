# Check catalogue

22 checks in five groups. Each states its class — **A** repairs a document
(usually the backlog; group 5's C13-equivalents may edit a journey/prototype
back-link instead), **B** stops and asks. Commands assume the repo root as cwd.
Groups 1-4 (C1-C15) check spec↔backlog; group 5 (C16-C22) checks the rest of
the pipeline — journey, prototype, test case, test plan.

Checks marked 🔥 have already fired on this repo at least once. They are not
hypothetical.

---

## Group 1 — Coverage: does every item have both sides?

### C1 — Spec declares a BL id that the backlog lacks — **class A** 🔥

A spec's `backlog:` frontmatter or section 9 names an id with no row.

```bash
for f in docs/01-requirements/01-spec/2026*.md; do
  sed -n 's/^backlog: \[\(.*\)\]/\1/p' "$f" | tr -d ' ' | tr ',' '\n' | while read -r id; do
    [ -n "$id" ] && grep -q "^| $id " docs/01-requirements/backlog.md || echo "MISSING $id  <- $(basename "$f")"
  done
done
```

Repair: add the row, taking title from the spec's section 9.

### C2 — Backlog row points at a spec that does not exist — **class B**

Either a typo, or a spec was deleted — which `docs/00-archived/index.md`
forbids. Check `docs/00-archived/` before concluding anything.

```bash
grep -o '\[\[01-spec/[^\\|]*' docs/01-requirements/backlog.md | sed 's/\[\[//' | sort -u | while read -r p; do
  [ -f "docs/01-requirements/$p.md" ] || echo "DEAD SOURCE  $p"
done
```

### C3 — Spec has no backlog rows at all — **class B**

An orphan spec means the breakdown never happened, or the work was dropped
without recording why. Both need a person.

### C4 — Backlog row's id appears in no spec — **class B**

Work with no requirement behind it. Do not delete the row; ask.

---

## Group 2 — Integrity: are the ids themselves sound?

### C5 — Duplicate `BL-###` — **class B**

```bash
grep -o '^| BL-[0-9]*' docs/01-requirements/backlog.md | sort | uniq -d
```

Two rows sharing an id means one is a mistaken copy. Which one is wrong is not
guessable — ask.

### C6 — Reused id — **class B**

An id that appears in a log entry or in `docs/00-archived/` with a different
title than the current row. The never-reuse rule has been broken and history is
now ambiguous.

```bash
grep -rho 'BL-[0-9]\+' docs/05-log/ docs/00-archived/ 2>/dev/null | sort -u
```

### C7 — Gap in the sequence — **class A, report only**

```bash
grep -o '^| BL-[0-9]*' docs/01-requirements/backlog.md | grep -o '[0-9]*' | awk 'NR>1 && $1!=p+1 {print "GAP after BL-"p} {p=$1}'
```

A gap is legitimate when a row was cancelled and later archived. Report it so
the next id is chosen above the true maximum, and do not "close" the gap.

---

## Group 3 — Status coherence: do the states make sense together?

### C8 — Work in progress against a `ร่าง` spec — **class B** 🔥

The most consequential check here. A backlog row at `กำลังทำ` or `เสร็จแล้ว`
whose source spec is still `ร่าง` means someone is building from a requirement
that was never approved.

```bash
for f in docs/01-requirements/01-spec/2026*.md; do
  st=$(sed -n 's/^status: //p' "$f" | head -1)
  [ "$st" = "ร่าง" ] || continue
  n=$(basename "$f" .md)
  grep "$n" docs/01-requirements/backlog.md | grep -E 'กำลังทำ|เสร็จแล้ว' | sed "s|^|BUILDING ON DRAFT ($n): |"
done
```

Worse when the spec still carries `[สมมติฐาน]` markers — then the work rests on
guesses. Say so explicitly in the report.

### C9 — Spec is `ใช้งาน` but section 8 still has open questions — **class B**

Every spec states that section 8 must be empty before status changes. A spec
violating its own gate is a process failure, not a backlog problem.

```bash
for f in docs/01-requirements/01-spec/2026*.md; do
  st=$(sed -n 's/^status: //p' "$f" | head -1)
  [ "$st" = "ใช้งาน" ] || continue
  sed -n '/## 8\./,/## 9\./p' "$f" | grep -q '^- \[ \]' && echo "GATE VIOLATED  $(basename "$f")"
done
```

### C10 — Spec is `ใช้งาน` but assumption markers remain — **class B**

```bash
grep -l 'สมมติฐาน' docs/01-requirements/01-spec/2026*.md | while read -r f; do
  [ "$(sed -n 's/^status: //p' "$f" | head -1)" = "ใช้งาน" ] && echo "ASSUMPTIONS IN APPROVED SPEC  $(basename "$f")"
done
```

### C11 — Section 8 is empty but the spec is still `ร่าง` — **class A, report only**

The opposite drift: the spec is ready for promotion and nobody promoted it.
Report it — changing a spec's status is the owner's call, not the auditor's.

### C12 — Backlog row blocked by an open question — **class A, report only**

A row whose source spec has an open question that names that row's id. Flag it
so nobody starts blocked work.

```bash
grep -n 'BL-[0-9]' docs/01-requirements/01-spec/2026*.md | grep -E 'Q[0-9]|ยังไม่ได้|ค้าง'
```

---

## Group 4 — Link and cross-document consistency

### C13 — `related:` is not bidirectional — **class A** 🔥

If A lists B, B must list A. This fired on `20260802-01` — it listed `02` but
not `03`, while `03` listed it.

```bash
for f in docs/01-requirements/01-spec/2026*.md; do
  a=$(basename "$f" .md)
  sed -n 's/^related: //p' "$f" | grep -o '\[\[[^|]*' | sed 's/\[\[//' | while read -r b; do
    t="docs/01-requirements/01-spec/$b.md"
    [ -f "$t" ] && { grep -m1 '^related:' "$t" | grep -q "$a" || echo "ONE-WAY  $a -> $b"; }
  done
done
```

Repair: add the missing back-reference. This one edits a **spec**, not the
backlog — the exception to step 3, because a missing back-link carries no
requirement meaning and cannot hide a decision.

### C14 — Dead wikilink — **class A**

```bash
for f in docs/01-requirements/01-spec/*.md docs/01-requirements/backlog.md docs/05-log/*.md; do
  d=$(dirname "$f")
  grep -o '\[\[[^]]*' "$f" | sed 's/\[\[//; s/\\|.*//; s/|.*//' | sort -u | while read -r l; do
    [ -f "$d/$l.md" ] || echo "DEAD LINK  $f -> $l"
  done
done
```

### C15 — Cross-document contradiction — **class B** 🔥

One spec's **out of scope** is another spec's **in scope**. No command finds
this; it needs reading. Compare every spec's section 2 against the others.

This fired for real: `20260802-02` listed cross-border data transfer as out of
scope while `20260802-03` required it for Google Analytics. It was resolved by
striking the line through with a pointer, not deleting it.

When found, check whether a log entry already explains it. If not, it is a live
contradiction and the specs disagree about what is being built.

---

## Group 5 — Full-pipeline traceability (journey / prototype / test)

Added when the pipeline grew past spec+backlog. These check the chain:
`spec → journey → prototype → AC (in spec §6) → test case → test plan`.
If a stage doesn't exist yet for a given feature, its checks simply find
nothing — that's a normal early-pipeline state, not a finding.

### C16 — Journey exists with no matching spec/backlog scope — class B

A journey's frontmatter names a spec or BL id that doesn't exist. Same
reasoning as C2: could be a typo, could trace back to a deleted spec.

```bash
grep -H -m1 '^spec:' docs/02-design/01-prototypes/*-journey.md 2>/dev/null
```

### C17 — Spec/backlog scope has no journey yet — class A, report only

Not a defect — most features don't have journeys yet at this pipeline's
current stage. Report what's missing so the next `feature-journey` pass knows
where to start; don't treat it as drift needing repair.

### C18 — Prototype references a component not in DESIGN.md §3 — class B 🔥

This is exactly the failure mode `prototype`'s own skill instructs against.
If it shows up anyway, it's not safely auto-fixable — either the prototype
needs its invented component removed, or `DESIGN.md` genuinely needs that
component added. Both require a person to decide.

```bash
grep -A2 '^### Components ที่ใช้' docs/02-design/01-prototypes/*-prototype.md 2>/dev/null
```

Cross-check named components against the `DESIGN.md` §3 headings by eye — no
single grep captures this reliably; treat this check as a required manual
read, not a shell one-liner.

### C19 — Prototype assigns a real value to a DESIGN.md `TBD` token — class B

The other half of C18's failure mode: a color/font/spacing value appearing in
a prototype where `DESIGN.md` §2 says `TBD`. Never silently accept the
prototype's value as the new decision — that's a design decision hiding
inside a document meant to only reference decisions, and it needs a person to
confirm it was actually decided somewhere.

### C20 — AC referenced by a test case doesn't exist in the spec — class B

```bash
grep -oh 'AC-[0-9.]*' docs/03-testing/01-test-plan/*-test-cases.md 2>/dev/null | sort -u | while read -r ac; do
  grep -rq -- "\*\*$ac\*\*" docs/01-requirements/01-spec/2026*.md || echo "ORPHAN TEST -> $ac"
done
```

An orphan test case means either the AC was renumbered (forbidden — see C6's
reasoning) or the test was written against a requirement that never existed.
Either way, a person needs to say which.

### C21 — AC in scope has no test case — class A, report only

```bash
grep -oh '\*\*AC-[0-9.]*\*\*' docs/01-requirements/01-spec/2026*.md | tr -d '*' | sort -u | while read -r ac; do
  grep -rq -- "$ac" docs/03-testing/01-test-plan/*-test-cases.md 2>/dev/null || echo "UNCOVERED  $ac"
done
```

Report as coverage gaps for `test-writer` to pick up — not a repair this audit
makes itself, since writing a test case is drafting work, not tidying.

### C22 — Test plan exists with no test cases behind it — class B

A test plan should never be written before its test cases per
`test-design`'s own Procedure B. If one exists anyway, something skipped a
step — flag it rather than assume the plan is still valid.

---

## Reporting shape

```
ตรวจ N ข้อ — พบปัญหา N ข้อ (class A: X ซ่อมแล้ว / class B: Y รอตัดสินใจ)

| ข้อ | สิ่งที่พบ | Class | ผลลัพธ์ |
| --- | --- | --- | --- |
| C13 | related: ของ 01 ไม่ชี้กลับ 03 | A | เพิ่มแล้ว |
| C8  | BL-003 กำลังทำ แต่ spec 01 ยังร่าง | B | รอตัดสินใจ |
```

State the verdict before the table. Do not report "up to date" with class B
findings open.
