---
title: จัดการเมนู ราคา และการเปิด/ปิดขายรายรายการ — Test Plan
date: 2026-08-19
status: ร่าง
spec: ["[[../../01-requirements/01-spec/20260802-01-table-self-ordering|20260802-01]]"]
test-cases: ["[[20260819-01-menu-management-test-cases|Test Cases]]"]
---

# จัดการเมนู ราคา และการเปิด/ปิดขายรายรายการ — Test Plan

> สร้างจากการรัน skill `test-design` (Procedure B) แบบ pilot

## Scope

ในขอบเขต: การเปิด/ปิดขายเมนู (US-6), การแก้ไขชื่อ/ราคาเมนูโดยเจ้าของร้าน (US-7),
การปฏิเสธสิทธิ์เมื่อพนักงานทั่วไปพยายามแก้ราคา (BR-7, AC-8)

นอกขอบเขต: ผลกระทบฝั่งลูกค้าของการปิดขายเมนู (อยู่ใน BL-003), การสร้าง/จัดการ
บัญชีผู้ใช้ (BL-006)

## Test Strategy

Feature นี้เล็กและเป็น CRUD + permission ล้วน ๆ ไม่มี integration ภายนอก (ไม่มี
payment, ไม่มี realtime) ยกเว้นจุดเดียวคือ BR-4 (ราคาใหม่ต้องไม่กระทบออร์เดอร์เดิม)
ซึ่งข้ามไปแตะระบบออร์เดอร์ — TC-4 จึงเป็น integration test เดียวในชุดนี้
กลยุทธ์หลักคือ functional testing ตรงจาก AC บวก permission testing เพราะ BR-7
เป็นกฎที่ผิดแล้วมีผลกระทบทางธุรกิจจริง (พนักงานแก้ราคาได้เองโดยไม่ควร)

## Test Types

Positive (TC-1, TC-3), Permission (TC-2), Integration/Boundary (TC-4) —
ไม่มี Loading/Empty/Error test เพราะยังไม่มี AC รองรับ (ดูช่องว่างในเอกสาร test case)

## Test Environment

`TBD` — ยังไม่มี environment จริง โปรเจกต์ยังไม่ได้ scaffold (ดู
[[../../02-design/02-technical/20260824-04-adr-revise-backend-go|ADR-03]] และ
`CLAUDE.md` หัวข้อ "Current state") เมื่อ scaffold แล้วให้เติมส่วนนี้ด้วย
environment จริง ไม่ใช่เดาไว้ล่วงหน้า

## Test Data

- บัญชีผู้ใช้ 2 ระดับ: เจ้าของร้าน 1 บัญชี, พนักงานทั่วไป 1 บัญชี (ต้องมาจาก BL-006
  ซึ่งยังไม่มีอยู่จริง — เป็น dependency ของชุดทดสอบนี้)
- เมนูตัวอย่างอย่างน้อย 1 รายการ
- ออร์เดอร์ที่ชำระเงินแล้ว 1 ใบ (สำหรับ TC-4) — ต้องมาจาก BL-004

## Test Tools

`TBD` — ไม่มี test runner ในโปรเจกต์นี้ (ยืนยันจาก `CLAUDE.md`: "no build, lint,
or test commands")

## Risk Management

ความเสี่ยงหลักคือ **BR-7 ถูกละเมิดแบบเงียบ** (พนักงานแก้ราคาได้โดยไม่มีใครสังเกต)
เพราะยังไม่มี Permission State pattern ที่ตัดสินใจแล้ว (ดู prototype audit
ด้านล่าง) — ถ้า UI ไม่บังคับ permission ให้ชัดเจน การพึ่งพา backend อย่างเดียว
อาจพลาดได้หากมี UI path ที่ไม่ผ่านการตรวจสอบ

## Entry Criteria

- Prototype audit ของ feature นี้ต้องไม่มี BLOCKER (ตรวจแล้ว — ดูรายงานด้านล่าง)
- AC ที่เกี่ยวข้อง (AC-7, AC-8, AC-9) ต้องมีอยู่จริงในสเปค (ตรวจแล้ว)

## Exit Criteria

TC-1 ถึง TC-4 ผ่านทั้งหมด และไม่มี regression ต่อ AC อื่นของสเปคเดียวกัน

## Schedule

`TBD` — ไม่มีวันที่จริงให้ระบุ

## Responsibilities

`TBD` — ยังไม่มีโครงสร้างทีมที่ตัดสินใจแล้ว

## Defect Management

ใช้กลไก backlog เดิมของ repo — บั๊กที่พบให้เข้า `requirement-to-backlog` เป็นงาน
ใหม่ ไม่ใช่ระบบ bug tracker แยกต่างหาก

## Reporting

`TBD` — `docs/03-testing/02-test-result/` ยังเป็น scaffolding เปล่า ยังไม่มี
รูปแบบผลทดสอบจริงให้ยึด

## Shift-left note

การทดสอบ feature นี้ไม่ได้เริ่มตอนเขียนเอกสารนี้ — เริ่มตั้งแต่ตอนเขียนสเปค
(กรณีขอบใน §7 ของสเปค 01 คือวัตถุดิบ test ตั้งแต่ต้น) และต่อเนื่องมาที่ตอน
prototype audit ซึ่งพบว่ายังไม่มี Permission State pattern ที่ตัดสินใจแล้ว —
ความเสี่ยงนั้นถูกบันทึกไว้ใน Risk Management ข้างต้น ไม่ใช่รอมาเจอตอนทดสอบจริง

## เอกสารอ้างอิง

- [[20260819-01-menu-management-test-cases|Test Cases]]
- [[../../01-requirements/01-spec/20260802-01-table-self-ordering|20260802-01]]
