---
name: bug-first
description: ทดสอบงานแบบ human — test-first (input → expected output), RED-GREEN-REFACTOR, เร็วและครอบคลุมจุดสำคัญ ไม่ over-test. รวมแนวทาง TDD + webapp-testing ใช้เมื่อเขียน/แก้โค้ดที่มี logic หรือ flow
---

# Bug First — Human TDD

## Objective

พิสูจน์ว่าโค้ดทำงานถูกต้องแบบมีหลักฐาน — เขียน test ก่อน, ครอบคลุม path หลัก + edge case, เร็วพอจะรันบ่อย ไม่ over-test (test เท่าที่จำเป็นจริง ตามหลัก human coding)

## Steps/Workflow

1. **เขียน test case ก่อนเขียนโค้ด** → verify: input → expected output ครบ path หลัก + edge case (empty, ผิด format, ขอบเขต)
2. **RED** → verify: test fail เพราะ feature ยังไม่มี — ไม่ fail เพราะ test พัง/ตั้ง expected ผิด
3. **GREEN** → verify: เขียนโค้ดเท่าที่ test ผ่าน — ไม่เขียนเกินที่ test ต้องการ
4. **REFACTOR** → verify: โค้ดสะอาดขึ้น แต่ test ยังผ่านทั้งหมด
5. **E2E ตามสเกลงาน** → verify: logic ซับซ้อน → unit test ใน tests/ (phpunit); flow/UI → browser จริง (Playwright/browser tools) ตรวจ viewport + interaction + state หลัก (default/loading/empty/error/success/disabled)
6. **Regression** → verify: test เก่าทั้งหมดยังผ่าน — ของเดิมไม่พัง

## Examples

```
❌ เขียนโค้ดเสร็จ → "น่าจะเวิร์ค" → push      → ไม่มีหลักฐาน
✅ เขียน test ก่อน: input {qty:3, price:10} → expected 30
   RED: test fail (ยังไม่มีฟังก์ชัน)
   GREEN: เขียนฟังก์ชัน → test pass
   → มีหลักฐานว่าถูกต้องจริง

❌ test ทุกอย่างรวม getter/setter trivial    → เสียเวลา, รก, maintenance แพง
✅ test เฉพาะ logic ที่มีโอกาสผิด: คำนวณ, เงื่อนไข, edge case — ตามหลัก human coding

❌ E2E คลิกทุกอย่างบน browser             → ช้า, fragile
✅ logic → unit test; flow สำคัญ → E2E บน browser เฉพาะ path หลัก
```

## Constraints

- ห้ามเขียนโค้ดโดยไม่มี test case (อย่างน้อย walk-through) — ตาม Step 1
- ห้ามข้าม RED — ถ้า test ผ่านตั้งแต่แรก แสดงว่าเขียน test หลัง หรือ expected ผิด
- ห้ามเขียน test ที่ผูกกับ implementation (รู้ชื่อ method ภายใน) — brittle แก้อะไรก็พัง
- ห้าม over-test — getter/setter/ของ trivial ไม่ต้อง test
- ห้าม E2E ทุกอย่าง — ใช้ unit ก่อน, E2E เฉพาะ flow หลัก
- ห้ามลบ/แก้ test ที่ผ่านอยู่โดยไม่มีเหตุผล — test เก่า = หลักฐาน regression
- ห้ามบอกว่า "เสร็จแล้ว" โดย test ยังไม่ผ่าน — verify จริงก่อน
