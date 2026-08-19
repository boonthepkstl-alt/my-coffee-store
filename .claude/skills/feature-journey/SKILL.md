---
name: feature-journey
description: Turn an approved-enough requirement spec (plus its backlog rows) into a User Journey document under docs/02-design/01-prototypes/ — the missing link between requirements and prototypes. Use when a spec/backlog group is ready for UI work and no journey exists yet. Triggers on "user journey", "feature journey", "user flow", "เส้นทางผู้ใช้".
---

# Feature → User Journey

## What "Feature" means in this repo — read before doing anything else

There is no separate "Feature list" document type in this pipeline, and this
skill does not create one. `docs/01-requirements/backlog.md` already groups
backlog rows by source spec (BL-001..006 = table ordering, BL-007..013 = PDPA,
BL-014..017 = cookie/analytics) — that grouping **is** the feature boundary.
A "Feature" for this skill's purposes is: **one spec, or a named subset of its
backlog rows that forms one coherent user-facing capability.**

Do not invent a parallel Feature document. If asked to write one, redirect to
this skill instead — the journey document below carries that information.

## Step 1 — Gate

Same discipline as `technical-design`: gate on whether an **open question in
the spec's section 8 blocks this specific feature**, not on the spec's overall
`status`. Check the spec's own log entries too — several already state which
BL a given open question blocks (e.g. spec `01`'s log notes Q5 blocks BL-002
specifically).

| Open question affects | Action |
| --- | --- |
| The feature's core flow, screens, or who can do what | **Stop.** Report which question blocks it |
| A detail this feature doesn't touch | Proceed |
| The spec still has `[สมมติฐาน]` markers touching this feature | **Stop** |

## Step 2 — Gather

Read: the source spec in full (especially §3 ผู้ใช้, §4 User stories, §6 AC,
§7 edge cases), the backlog rows in scope, and `DESIGN.md` (especially §2
device-context hints and §3's component catalogue — reuse those names, don't
invent new component vocabulary).

## Step 3 — Write the journey

Path: `docs/02-design/01-prototypes/{YYYYMMDD}-{NN}-{slug}-journey.md`. Same
date/running-number/slug rules as spec filenames.

```markdown
---
title: <ชื่อ feature ภาษาไทย> — User Journey
date: <YYYY-MM-DD>
status: ร่าง
spec: ["[[../../01-requirements/01-spec/<spec-file>|<spec-id>]]"]
backlog: [BL-###, ...]
related: []
---

# <ชื่อ feature> — User Journey

## 1. ขอบเขตของ Journey นี้

Feature นี้ครอบคลุม backlog ข้อใดบ้าง และ **ไม่ครอบคลุม** ข้อใด (ชี้ไป journey อื่น)

## 2. ผู้ใช้และเป้าหมาย

ดึงจากสเปค §3/§4 ตรง ๆ — ห้ามเติมเป้าหมายที่สเปคไม่ได้พูด

## 3. เส้นทางหลัก (Happy Path)

ลำดับขั้นแบบ step-by-step อ้าง user story ที่เกี่ยวข้อง (US-#) ทุกขั้น
ระบุว่าแต่ละขั้นเกิดที่ "หน้าจอ/บทบาท" ใด (ลูกค้า / บาริสต้า / พนักงาน / เจ้าของร้าน)

```text
1. <ขั้นตอน>          [หน้าจอ: ...]  [US-#]
2. ...
```

## 4. เส้นทางรอง / กรณีขอบ

ดึงจากสเปค §7 (กรณีข้อผิดพลาดและกรณีขอบ) ตรง ๆ — ทุกแถวในตารางนั้นต้องปรากฏเป็น
เส้นทางรองใน journey นี้ ถ้า journey ไม่ครอบคลุมกรณีขอบข้อใด ให้ระบุว่าเพราะเหตุใด
(เช่น อยู่นอกขอบเขตของ feature ที่ตัดมา)

## 5. หน้าจอที่เกี่ยวข้อง

| หน้าจอ | บทบาทที่ใช้ | Component ที่ต้องมี (อ้างชื่อจาก DESIGN.md §3) | สถานะที่ต้องมี |
| --- | --- | --- | --- |
| | | | Default / Loading / Empty / Error / ... |

Component ต้องอ้างชื่อที่มีอยู่ใน `DESIGN.md` §3 เท่านั้น ถ้าต้องการ component ที่
ไม่มีในแคตตาล็อกนั้น ให้บันทึกไว้ในหัวข้อ 7 ไม่ใช่ตั้งชื่อใหม่เอง

## 6. Non-goals ของ Journey นี้

สิ่งที่ user อาจคาดหวังแต่ feature นี้ไม่ทำ — ดึงจาก out of scope ของสเปค

## 7. สิ่งที่ต้องกลับไปเพิ่มใน requirement หรือ DESIGN.md

ถ้า journey ต้องการหน้าจอ/component/state ที่ไม่มีหลักฐานในสเปคหรือ DESIGN.md
ห้ามคิดขึ้นเอง — บันทึกไว้ที่นี่ให้คนตัดสินใจ

## 8. คำถามที่ยังค้าง

ต้องว่างก่อนส่งต่อให้ prototype-writer

## 9. เอกสารอ้างอิง

- [[../../01-requirements/01-spec/<spec-file>|<spec-id>]]
- [DESIGN.md](../../../DESIGN.md) — outside the Obsidian vault (`docs/`), use a
  Markdown link here, not a wikilink — a wikilink across the vault boundary
  won't resolve in Obsidian
```

## Step 4 — Cross-link

Add the journey's wikilink back into the source spec's §10 (เอกสารอ้างอิง).

## Step 5 — Log and report

Log per `.claude/skills/requirement-to-backlog/references/backlog-format.md`
format. Report: journey path, feature/backlog scope, edge cases covered vs.
not, open items, and whether it's ready to hand to `prototype-writer`.

Never claim a journey is complete while §8 has entries.
