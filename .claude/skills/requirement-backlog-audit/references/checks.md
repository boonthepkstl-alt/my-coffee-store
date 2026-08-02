# Check catalogue

15 checks in four groups. Each states its class — **A** repairs the backlog,
**B** stops and asks. Commands assume the repo root as cwd.

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

## Reporting shape

```
ตรวจ 15 ข้อ — พบปัญหา N ข้อ (class A: X ซ่อมแล้ว / class B: Y รอตัดสินใจ)

| ข้อ | สิ่งที่พบ | Class | ผลลัพธ์ |
| --- | --- | --- | --- |
| C13 | related: ของ 01 ไม่ชี้กลับ 03 | A | เพิ่มแล้ว |
| C8  | BL-003 กำลังทำ แต่ spec 01 ยังร่าง | B | รอตัดสินใจ |
```

State the verdict before the table. Do not report "up to date" with class B
findings open.
