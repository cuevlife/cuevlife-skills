---
name: second-eyes
description: ตรวจงาน (ของตัวเอง/AI/subagent) ก่อน commit — 2 แกน Standards + Spec, รายงานตาม severity พร้อมหลักฐาน ใช้เมื่อ review โค้ด/PR/ผลงาน AI ก่อน merge หรือก่อนบอกเสร็จ
---

# Second Eyes — Human Code Review

## Objective

ตรวจงานก่อน commit/merge ให้จับปัญหาได้จริง (ไม่ใช่ดูผ่านๆ) — 2 แกน: Standards (เขียนถูก convention ไหม) + Spec (ตรง requirement ไหม behavior เดิมพังไหม) รายงานเป็น severity พร้อมหลักฐาน — เหมาะกับ solo dev ที่ไม่มี peer review (AI เป็นคนตรวจแทน)

## Steps/Workflow

1. **กำหนดขอบเขต review** → verify: รู้ว่า review อะไร — diff ตั้งแต่ branch point / commit ที่เกี่ยวข้อง / ผลงาน AI ทั้งชิ้น
2. **แกน 1 — Standards** → verify: ไล่ AI smell table + Human Essence (ใน `code` skill): ชื่อ, abstraction, ทำเกินขอ, hardcode, comment, pattern ของโปรเจค
3. **แกน 2 — Spec** → verify: ตรง requirement? behavior เดิมไม่พัง? edge case คิดครบ? (เทียบ test case)
4. **รัน/ตรวจจริง** → verify: test ผ่าน, lint/static analysis ผ่าน, ลอง flow จริง (ไม่ใช่แค่อ่าน)
5. **รายงาน severity** → verify: แต่ละ finding มี: severity (blocker/major/minor/nit) + ที่อยู่ (ไฟล์:บรรทัด) + หลักฐาน + วิธีแก้
6. **แก้/ปิด findings** → verify: blocker/major แก้ครบ, minor/nit ตัดสินใจชัด (แก้หรือเก็บเป็นของรู้), test ผ่านอีกครั้ง

## Examples

```
❌ "โค้ดดูโอเคนะ"                              → ไม่ได้ review จริง
✅ "blocker: บรรทัด 42 SQL ต่อ string → SQLi
   major: ฟังก์ชันนี้ไม่รองรับ qty=0 → เดิมรองรับ
   minor: ชื่อ $data ควรเป็น $invoice"         → แก้ได้ทันที รู้ว่าตรงไหน

❌ review แต่ไม่รัน ไม่ลอง                    → พลาดของที่รันแล้วพัง
✅ review + รัน test + ลอง flow จริง          → จับได้ก่อน user เจอ

❌ nitpick ไปหมด (เว้นวรรค, ชื่อตัวแปร)      → noise จน finding ใหญ่จมหาย
✅ เน้น blocker/major ก่อน, nit รวมท้าย      → priority ชัด
```

## Constraints

- ห้าม review โดยไม่กำหนดขอบเขต (diff/commit) — review ทั่วหมด = ไม่ครบ
- ห้าม review โดยไม่รัน/ไม่ลอง — อ่านอย่างเดียวจับ bug ไม่ได้
- ห้าม nitpick นำหน้า — blocker/major มาก่อนเสมอ
- ห้ามแก้โค้ดของคนอื่น (subagent output) โดยไม่ดู findings ก่อน — review ก่อน แก้ทีหลัง
- ห้ามรายงานโดยไม่มีหลักฐาน (ไฟล์:บรรทัด) — "ตรงนี้ผิด" ไม่พอ ต้องบอกว่าทำไม
- ห้ามผ่านงานที่ blocker/major ยังไม่แก้ — จบเมื่อ findings ปิดครบ + test ผ่าน
- ห้าม review งานตัวเองตอนเพิ่งเขียนเสร็จ — ทิ้งไว้สักครู่/อ่านในมุมคนอื่นก่อน
