---
name: prototype
description: Turn a User Journey (plus its spec, backlog, and DESIGN.md) into a Prototype Specification under docs/02-design/01-prototypes/ — screens, components, navigation, states, responsive behavior. Reuses DESIGN.md's component catalogue; never invents new visual design. Use when a journey exists and is ready for screen-level detail. Triggers on "prototype", "wireframe", "screen spec", "ต้นแบบ", "ออกแบบหน้าจอ".
---

# Journey → Prototype Specification

This produces a **specification of screens**, not pixels, code, or a running
UI — this repo has no frontend code and no CSS framework decision yet (see
`DESIGN.md` §0). A prototype spec describes what each screen contains and does
precisely enough for someone to build it once tooling exists, without
pre-deciding visual design that hasn't been decided.

## Non-negotiable: reuse before invent

Read `DESIGN.md` in full before writing anything, specifically:

- §2 (tokens) — if a token is `TBD`, do not assign it a value here either.
  Reference it as `TBD` and move on.
- §3 (component catalogue) — every component this prototype needs must be
  named from that catalogue. If §3 marks it "Not yet required" and this
  prototype is the thing that now requires it, that's fine — cite it by its
  existing name. If no matching component exists in §3 at all, that is a gap:
  record it in this document's "สิ่งที่ต้องกลับไปเพิ่มใน DESIGN.md" section,
  don't silently name a new component into existence.
- §4 (UX rules) — several are binding business rules (e.g. cookie banner
  BR-1..5), not style suggestions. A prototype that violates one is wrong
  regardless of how it looks.

## Step 1 — Gate

Same gate as `feature-journey`: block only if an open question in the source
spec or journey affects this specific screen set. Do not gate on `status:
ร่าง` alone.

## Step 2 — Write the prototype specification

Path: `docs/02-design/01-prototypes/{YYYYMMDD}-{NN}-{slug}-prototype.md`.

```markdown
---
title: <ชื่อ feature> — Prototype Specification
date: <YYYY-MM-DD>
status: ร่าง
journey: ["[[<journey-file>|<journey-id>]]"]
spec: ["[[../../01-requirements/01-spec/<spec-file>|<spec-id>]]"]
backlog: [BL-###, ...]
---

# <ชื่อ feature> — Prototype Specification

## 1. ขอบเขต

หน้าจอใดบ้างในเอกสารนี้ — ต้องตรงกับ "หน้าจอที่เกี่ยวข้อง" ในเอกสาร journey ต้นทาง

## 2. Screen: <ชื่อหน้าจอ>

**บทบาทที่ใช้:** <role>
**เข้าถึงจาก:** <journey step ใด>
**ไปต่อที่:** <หน้าจอถัดไป>

### Layout

โครงคร่าว ๆ ของหน้าจอ (บนลงล่าง หรือ region: header/content/action)
อ้างอิง layout context จาก DESIGN.md §2 (สอง device context: มือถือลูกค้า /
จอบาริสต้า-พนักงาน) — ระบุว่าหน้าจอนี้อยู่ context ไหน

### Components ที่ใช้

| Component (ชื่อจาก DESIGN.md §3) | Purpose บนหน้าจอนี้ | Variant/เนื้อหา |
| --- | --- | --- |
| | | |

### Navigation

เข้าออกหน้าจอนี้อย่างไร — ปุ่มใดพาไปไหน, ย้อนกลับได้ไหม

### Interaction

การกระทำที่ผู้ใช้ทำได้บนหน้าจอนี้ และผลลัพธ์ทันทีของแต่ละอย่าง

### States

ระบุทุก state ที่หน้าจอนี้ต้องรองรับ (Default / Loading / Empty / Error /
Success / Permission / Offline ตามที่เกี่ยวข้อง) พร้อมอ้าง trigger จาก
journey §4 หรือสเปค §7 ถ้ามี — ห้ามข้าม state ที่สเปคบังคับไว้แล้ว

| State | Trigger | พฤติกรรมที่ต้องมี (จากสเปค) | UI treatment |
| --- | --- | --- | --- |
| | | | TBD ถ้า DESIGN.md ยังไม่กำหนด |

### Responsive

พฤติกรรมที่ breakpoint ต่าง ๆ — ถ้า DESIGN.md ยังไม่มี breakpoint จริง
(ปัจจุบันยังไม่มี) ให้ระบุ device context ที่ต้องรองรับแทน ไม่ใช่เดาค่า px

### Accessibility

`TBD` เว้นแต่มีข้อกำหนดจริงในสเปคหรือ DESIGN.md — ห้ามเติมกฎ accessibility
ที่ไม่มีอยู่จริง (ดู DESIGN.md §4 — นี่คือช่องว่างที่รู้อยู่แล้ว ไม่ใช่ที่ต้องเดา)

## 3. Repeat §2 for every screen in scope

## 4. สิ่งที่ต้องกลับไปเพิ่มใน DESIGN.md

Component หรือ token ที่ prototype นี้ต้องการแต่ไม่มีในแคตตาล็อกปัจจุบัน

## 5. คำถามที่ยังค้าง

## 6. เอกสารอ้างอิง

- [[<journey-file>|<journey-id>]]
- [DESIGN.md](../../../DESIGN.md) — outside the Obsidian vault (`docs/`), use a
  Markdown link here, not a wikilink
```

## Step 3 — Cross-link

Add this prototype's wikilink to the journey's own §9, and to the source
spec's §10.

## Constraints (repeat of the workshop brief, binding here)

- Do not invent a new design system, color, or one-off styling value.
- Do not duplicate an existing component when a variant of one would do.
- Do not change architecture (that's `technical-design`'s domain).
- Do not interpret the requirement beyond what its spec/journey states — if
  the UI need isn't traceable to a requirement, it's a gap to report, not a
  detail to invent.

## Step 4 — Log and report

Report: prototype path, screens covered, components reused vs. gaps found,
open items, and whether it's ready for `prototype-auditor`.
