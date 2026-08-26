---
name: no-yolo-deploy
description: ขั้นตอน deploy ที่ปลอดภัย — pre-deploy checklist, backup, deploy, verify, rollback. ขั้นตอนเฉพาะของแต่ละโปรเจค (server, commands, env) ต้องอ่านจาก AGENTS.md ของโปรเจคนั้น ใช้เมื่อต้องขึ้น production
---

# No YOLO Deploy — Safe Release

## Objective

ขึ้น production แบบมีขั้นตอน ปลอดภัย และกลับหลังได้ — เป็น workflow ภาพรวม: ขั้นตอน/คำสั่งเฉพาะแต่ละโปรเจค (server, path, env, วิธีอัปโหลด) **อยู่ใน `AGENTS.md` ของโปรเจคนั้น** — skill นี้คือ checklist ที่ใช้ร่วมกันทุกโปรเจค

## Steps/Workflow

1. **อ่าน AGENTS.md ของโปรเจค** → verify: รู้ commands, โครงสร้าง deploy เฉพาะ (mirror? server? env?), gotchas ที่เกี่ยวข้อง
2. **Pre-deploy checklist** → verify: test ผ่านทั้งหมด, static analysis ผ่าน, security checklist ผ่าน (ถ้ามี `before-hacked` skill), .env/config ถูกต้อง, migrations ครบ
3. **Backup ก่อน deploy** → verify: DB dump + ไฟล์สำคัญ เก็บไว้ — ต้อง restore กลับได้จริง
4. **Deploy** → verify: ตามขั้นตอนเฉพาะโปรเจค (mirror/upload/build) — อย่าแก้ production ตรงๆ ถ้าโปรเจคมี source + mirror
5. **Verify หน้างาน (smoke test)** → verify: หน้า login/ฟีเจอร์หลักทำงาน, ไม่มี error ใน log, ข้อมูลไม่หาย
6. **ถ้าพัง → Rollback** → verify: restore จาก backup ข้อ 3, ตรวจว่าเวอร์ชันเก่ากลับมาใช้ได้

## Examples

```
❌ แก้ไฟล์บน server ตรงๆ แล้ว deploy เลย  → ไม่รู้ว่าทำอะไร, กลับหลังไม่ได้
✅ แก้ที่ source → test → backup → deploy ผ่าน mirror/ขั้นตอนของโปรเจค → smoke test

❌ deploy โดย test ไม่ผ่าน "เดี๋ยวค่อยแก้"  → พังแล้วแก้ยากบน production
✅ test ผ่านก่อน — deploy เฉพาะของที่พิสูจน์แล้ว

❌ ไม่มี backup "ข้อมูลไม่น่าพังหรอก"
✅ dump DB + สำรองไฟล์ก่อนทุกครั้ง — คืนค่าได้เมื่อผิดพลาด
```

## Constraints

- ห้าม deploy โดย test ยังไม่ผ่าน
- ห้ามแก้ production/server ตรงๆ โดยไม่ผ่านขั้นตอนของโปรเจค (mirror/source)
- ห้าม deploy โดยไม่มี backup (DB + ไฟล์) — ต้อง restore ได้จริง
- ห้ามข้าม smoke test หลัง deploy
- ห้าม deploy กลางดึก/ตอนรีบ โดยไม่มีเวลา rollback — เผื่อเวลาไว้เสมอ
- ห้ามใช้ credentials production ใน dev หรือ hardcode ในโค้ด
