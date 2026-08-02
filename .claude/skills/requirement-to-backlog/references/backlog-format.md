# Backlog and log formats

## `docs/01-requirements/backlog.md`

Create with this skeleton when absent, then append rows. Keep rows sorted by
`BL-###` ascending.

```markdown
# Backlog

รายการงานทั้งหมดที่แตกออกมาจากเอกสารข้อกำหนดใน [[01-spec/index|01-spec]]
ไฟล์นี้ถูกปรับปรุงทุกครั้งที่มีเอกสาร requirement ใหม่

| ID | หัวข้อ | ที่มา | ความสำคัญ | ขนาด | สถานะ | อัปเดตล่าสุด |
| --- | --- | --- | --- | --- | --- | --- |
| BL-001 | <ชื่องาน> | [[01-spec/20260802-01-online-ordering\|20260802-01]] | สูง | M | ยังไม่เริ่ม | 2026-08-02 |

## คำอธิบายคอลัมน์

- **ID** — `BL-###` เรียงต่อเนื่อง ไม่นำเลขเดิมกลับมาใช้ซ้ำ
- **ที่มา** — wikilink ไปยังเอกสารสเปคต้นทาง
- **ความสำคัญ** — `สูง` / `กลาง` / `ต่ำ`
- **ขนาด** — `S` / `M` / `L` (ประเมินคร่าว ๆ ยังไม่ใช่ story point)
- **สถานะ** — `ยังไม่เริ่ม` / `กำลังทำ` / `เสร็จแล้ว` / `ยกเลิก`
- **อัปเดตล่าสุด** — `YYYY-MM-DD`

งานที่ยกเลิกให้คงแถวไว้และเปลี่ยนสถานะเป็น `ยกเลิก` — ห้ามลบแถวออก
เพื่อรักษาประวัติการตัดสินใจตามข้อตกลงใน [[../00-archived/index|00-archived]]
```

Status vocabulary matches `docs/01-requirements/03-task/index.md` — do not
invent alternatives. Cancelled rows stay, per the repo's never-delete rule.

Escape the pipe inside a wikilink as `\|` when it sits in a table cell,
otherwise Markdown reads it as a column separator.

## `docs/05-log/{YYYYMMDD}-log.md`

One file per day, many entries. Append; never rewrite an earlier entry.

```markdown
# บันทึกประจำวันที่ <YYYY-MM-DD>

## <HH:MM> — <สิ่งที่ทำโดยสรุป>

**สิ่งที่ทำ**

- สร้างเอกสาร [[../01-requirements/01-spec/20260802-01-online-ordering|20260802-01]]
- เพิ่ม BL-001, BL-002 ลงใน backlog

**เหตุผลและการตัดสินใจ**

- <ทำไมจึงสร้างเอกสารใหม่แทนการแก้ของเดิม>
- <ผู้ใช้เลือกแนวทางใดจากคำถามที่ถามไป และเลือกเพราะอะไร>

**สิ่งที่ยังค้าง**

- <คำถามที่ยังไม่ได้คำตอบ หรือ `— ไม่มี —`>
```

Get `HH:MM` from `date +%H:%M`; never guess a time.

The reasoning section is the point of the file — `docs/05-log/index.md` defines
this as the record used to reconstruct why a choice was made later. A log entry
listing only changed files is not worth writing.
