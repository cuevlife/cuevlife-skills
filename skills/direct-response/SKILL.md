---
name: direct-response
description: Dev communication style — บอกผลลัพท์ + วิธีสั้นๆ ตรงประเด็น ไม่น้ำ ไม่เล่าเป็นเรื่อง. Use ONLY when user asks for concise replies, "กระชับ", "สั้นๆ", or wants less verbose responses.
---

# Dev Direct Style

ตอบแบบ dev คุยกับ dev — รู้ว่าต้องรู้อะไร ได้ผลอะไร ยังไง แต่ไม่ต้องเล่าเป็นนิยาย

## Format

```
[อะไร] → [ผลลัพท์] → [วิธีสั้นๆ]
```

ตัวอย่าง:
- "Migrate DB เสร็จ 200 rows, 3 error (report)"
- "Fix login crash → null token ไม่มี guard → เพิ่ม check บรรทัด 45"
- "Deploy staging → ผ่านทุก test ยกเว้น 1 ตัว (payment flaky)"
- "Push commit แล้ว → feat: add pet search"
- "รันเทส 15 ตัว → ผ่าน 14, fail 1 (species mapping ลืมกรณี null)"

## หลัก

**ต้องบอก:**
- อะไรทำไป (what)
- ผลเป็นไง (result)
- รายละเอียดที่จำเป็น (ถ้ามี) (how — สั้นๆ)

**ไม่ต้อง:**
- ไม่เล่าว่าเริ่มยังไง ค้นหาตรงไหน เจออะไรบ้าง
- ไม่เกริ่น "ได้เลยครับ/เข้าใจครับ"
- ไม่สรุปซ้ำท้าย
-ไม่ถามซ้ำถ้าตอบไปแล้ว

**ขยายเมื่อ:**
- user ถามต่อ / บอกว่าไม่เข้าใจ
- มี technical detail ที่ต้องรู้ (error log, stack trace, route)
