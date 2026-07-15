---
name: self-doc
description: บันทึกทำไม, ไม่ใช่แค่บันทึกอะไร — ADR, glossary, session log, bug journal สำหรับ solo dev ที่กลับมาดูโค้ดอีก 6 เดือนแล้วต้องเข้าใจ. Use ONLY when user asks about documentation, ADR, session notes, bug journal, หรือให้บันทึกอะไรก็ตาม
---

# Self-Doc — เขียนให้คนอื่น (รวมถึงตัวเองในอนาคต)

---

## หลักการ

**บันทึก "ทำไม" ไม่ใช่แค่ "อะไร"**

- โค้ดบอก "อะไร"  — comment บอก "ทำไม"
- 6 เดือนข้างหน้า คุณจะลืม — เขียนไว้ตอนนี้
- ลงทุน 5 นาทีวันนี้ = ประหยัด 1 ชม. ในอนาคต

---

## ADR — Architecture Decision Record

บันทึกเวลาตัดสินใจเรื่องสำคัญ:

```
# ADR-001: ใช้ Gemini API แทน OCR ตัวอื่น

วันที่: 2025-06-01
บริบท: ต้อง OCR ใบเสร็จ แต่ Tesseract แม่นไม่พอ
ตัวเลือกที่ลอง: Tesseract, Google Vision, Gemini
เหตุผลที่เลือก: Gemini แม่นสุด, จัดการ ambiguity ได้ดี, ราคาถูกกว่า Vision
ข้อเสียที่ยอมรับ: latency สูงกว่า, ต้องพึ่ง API ภายนอก
ผลกระทบ: ต้องมี key rotation, มี fallback เมื่อ API ล่ม

---

# ADR-002: ทำไมถึงเก็บ extracted_data เป็น JSON

วันที่: 2025-06-15
บริบท: field ของ OCR result เปลี่ยนบ่อยตาม template
เหตุผล: ถ้าแยก column ทุก field จะ migration บ้า
ข้อเสียที่ยอมรับ: query ลึกยาก, filter ใน SQL ไม่ได้
```

**ADR ไม่ต้องยาว — แค่พอให้เข้าใจว่าตอนนั้นคิดอะไรอยู่**

---

## Project Glossary — คำศัพท์เฉพาะ project

ป้องกัน confusion:

| คำในโค้ด/DB | ความหมาย | อย่าสับสนกับ |
|-------------|----------|-------------|
| Customer | Workspace ใน UI | ลูกค้าจริง |
| Slip | Folder ที่เก็บ bills | ใบเสร็จ |
| workflow_status | สถานะ processing | status (active/ inactive) |
| role=5 | Manager (UI) | ROLE_OWNER ในโค้ด |

---

## Session Log — วันนี้ทำอะไรไป

เขียนตอนเลิกงาน หรือก่อน commit:

```
# Session 2025-07-10

## ทำ
- แก้ bug export field order
- เพิ่ม VAT calc ใน summary mode
- refactor CsvExporter แยก method

## ติด
- Export rate limit ยังไม่รู้จะ handle ยังไง
- ต้องถาม user ว่า prefetch เท่าไหร่

## พรุ่งนี้
- ทำ rate limit
- test VAT กับ data จริง
```

---

## Bug Journal — เจออะไร, แก้ยังไง

ไม่ต้องลงลึก — แค่ timeline:

```
# Bug Journal

## 2025-07-09 — Export field order พลิก
อาการ: CSV columns เรียงไม่ตรงกับ template
สาเหตุ: CsvExporter ใช้ array_keys ซึ่ง PHP preserve order ไม่คงที่
แก้: ใช้ template field order แทน
ป้องกัน: เพิ่ม test ที่เช็ค column order
```

---

## โครงสร้าง docs/ ที่แนะนำ

```
docs/
├── ADR/               # การตัดสินใจ (ADR-001.md, ADR-002.md)
├── decisions.md       # หรือรวมในไฟล์เดียวถ้า project เล็ก
├── glossary.md        # คำศัพท์
├── sessions/          # session log (2025-07-10.md)
└── bugs.md            # bug journal
```

---

## ใช้ Subagent สำหรับเขียน Doc

**Mode: BACKGROUND (ไม่ต้องรอ)** — subagent เขียน doc ไป, main agent coding ต่อ, เสร็จค่อย review ที่หลัง 30 วิ

### ทำไมต้องใช้ subagent

main agent ควร focus coding — delegate documentation writing ไป subagent:
- ADR — main agent ตัดสินใจ → subagent เขียน ADR
- Session log — subagent เก็บ log ตามที่ main agent ทำ
- Bug journal — subagent เขียน journal จาก bug ที่ main agent แก้
- Glossary — subagent update เมื่อ main agent ใช้คำศัพท์ใหม่

หลักการ: main agent coding → subagent recording (background)

### Subagent Prompt Template

```
ใช้ self-doc skill
เป็น role: documentarian

Context (จาก main agent):
- Task: {สิ่งที่ทำ}
- Decision: {สิ่งที่ตัดสินใจ}
- Reason: {เหตุผล}
- Alternative: {ตัวเลือกอื่นที่ลอง}
- Trade-off: {ข้อเสียที่ยอมรับ}

Output:
- ADR: docs/ADR/ADR-{number}.md
  - วันที่, บริบท, ตัวเลือก, เหตุผล, ข้อเสีย, ผลกระทบ

---

ใช้ self-doc skill
เป็น role: session logger

สิ่งที่ main agent ทำวันนี้:
1. {task1} → {result}
2. {task2} → {result}
3. ยังติด: {blocker}
4. พรุ่งนี้: {plan}

Output:
- docs/sessions/{date}.md
- สั้น ตรงประเด็น ไม่เกิน 10 บรรทัด

---

ใช้ self-doc skill
เป็น role: bug journalist

Bug context:
- อาการ: {symptom}
- สาเหตุ: {root cause}
- แก้: {fix}
- ป้องกัน: {prevention}

Output:
- docs/bugs.md เพิ่ม record
- format: {date} — {symptom} → {cause} → {fix}
```

### Workflow

```
main agent:
  1. coding + ตัดสินใจ
  2. delegate doc → subagent (self-doc)
  3. subagent เขียน ADR / session / bug journal
  4. main agent review (30 วินาที)
  → ไม่เสีย flow coding
```

---

## สรุป

| ชนิด | ไว้ตอนไหน | ความยาว |
|------|-----------|---------|
| ADR | ตอนเลือก solution | 3-5 ประโยค |
| Glossary | เมื่อมีคำ ambiguou | ตารางสั้นๆ |
| Session Log | ตอนเลิกงานทุกวัน | 5 นาที |
| Bug Journal | หลังจากแก้ bug | 30 วินาที |

**ข้อควรจำ:** Self-Doc ไม่ใช่ report — เป็น memo ให้ตัวเองในอนาคต ลงทุนน้อยแต่คุ้มมาก