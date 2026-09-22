# Zero-Downtime Schema Migration — ตารางที่มีข้อมูลจริง/production ใหญ่

ใช้เมื่อ: ตารางมีข้อมูลจริงมากพอที่ `ALTER TABLE` ตรงๆ จะ lock นานจนกระทบ user (นับหลักหมื่น row ขึ้นไป หรือระบบมี user ใช้งานพร้อมกันตลอดเวลาแม้ table เล็ก) — ตารางเล็ก/dev environment ใช้ `ALTER TABLE` ตรงๆ ได้ ไม่ต้องอ่านไฟล์นี้

## หลักการ: Expand → Migrate → Contract

อย่าเปลี่ยน schema แบบ breaking ในขั้นเดียว แยกเป็น 3 ช่วง ทุกช่วงต้องรันแอปเวอร์ชันเก่าได้โดยไม่พัง:

1. **Expand** — เพิ่มของใหม่ (column ใหม่, index ใหม่) โดยไม่ลบ/แก้ของเดิม แอปเวอร์ชันเก่ายังทำงานได้เหมือนไม่มีอะไรเปลี่ยน
2. **Migrate** — backfill ข้อมูลเข้า column ใหม่, deploy โค้ดใหม่ให้เขียน/อ่านทั้ง 2 ที่พร้อมกัน (dual write/read) จนแน่ใจว่าของใหม่ถูกต้อง
3. **Contract** — ลบของเก่า (column เก่า, code path เก่า) หลังยืนยันว่าไม่มีใครใช้แล้ว

## ตัวอย่าง: เพิ่ม column NOT NULL บนตารางที่มีข้อมูลอยู่แล้ว

```
❌ ALTER TABLE orders ADD COLUMN currency VARCHAR(3) NOT NULL;
   -- พังทันทีถ้ามี row เดิมอยู่ (ไม่มี default ให้ row เก่า) และ lock table นานถ้า row เยอะ

✅ Step 1 (Expand):  ALTER TABLE orders ADD COLUMN currency VARCHAR(3) NULL DEFAULT NULL;
✅ Step 2 (Migrate): UPDATE orders SET currency = 'THB' WHERE currency IS NULL;
                     -- ทำเป็น batch (เช่นทีละ 5,000-10,000 row) ไม่ update ทีเดียวทั้งตาราง กัน lock นาน:
                     UPDATE orders SET currency = 'THB' WHERE currency IS NULL LIMIT 5000;
                     -- วนซ้ำจน affected rows = 0
✅ Step 3 (Contract): ALTER TABLE orders MODIFY COLUMN currency VARCHAR(3) NOT NULL;
                     -- ใส่ constraint หลัง backfill ครบแล้วเท่านั้น
```

## ตัวอย่าง: Rename/Drop column ที่มีคน query อยู่

**ก่อนแตะต้องทำ Blast Radius check ในหัวข้อ 1 ก่อนเสมอ** (grep หา query ที่ใช้ column นี้ทั้งโปรเจกต์) แล้วค่อยตาม pattern นี้:

```
1. Expand:   ADD COLUMN new_name — เขียนโค้ดให้เขียนทั้ง old_name และ new_name พร้อมกัน (dual write)
2. Migrate:  backfill new_name จาก old_name ของ row เก่า, เปลี่ยนโค้ดอ่านให้อ่านจาก new_name
3. Contract: ยืนยันไม่มี query ที่ยัง SELECT old_name แล้วค่อย DROP COLUMN old_name
```

ห้าม `RENAME COLUMN` ตรงๆ ในทีเดียวถ้าแอปเวอร์ชันเก่ายังรันอยู่ระหว่าง deploy (rolling deploy มีสองเวอร์ชันรันพร้อมกันชั่วคราวเสมอ) — เวอร์ชันเก่าจะ query column ที่หายไปแล้วพังทันที

## Index บนตารางใหญ่

- MySQL/MariaDB: `ALTER TABLE ... ADD INDEX` แบบปกติ lock table เขียนไม่ได้ระหว่างสร้าง — ตารางใหญ่ใช้ `ALGORITHM=INPLACE, LOCK=NONE` (ถ้า storage engine รองรับ) หรือเครื่องมือเฉพาะ (เช่น pt-online-schema-change จาก Percona Toolkit) ที่สร้าง shadow table คัดลอกข้อมูลเป็น chunk แล้วสลับตารางแบบ atomic
- PostgreSQL: ใช้ `CREATE INDEX CONCURRENTLY` เสมอบนตารางที่มีการเขียนพร้อมกัน — วิธีปกติ (`CREATE INDEX`) lock การเขียนไว้ตลอดที่สร้าง

## Constraints

- ห้าม `ALTER TABLE` ที่เพิ่ม `NOT NULL` โดยไม่ backfill ก่อนบนตารางที่มีข้อมูลจริงอยู่แล้ว
- ห้าม `RENAME`/`DROP COLUMN` ในขั้นเดียวถ้าระบบ deploy แบบ rolling (มีแอปเก่า/ใหม่รันพร้อมกันชั่วขณะ) — ต้องผ่าน expand-contract เสมอ
- Backfill ข้อมูลจำนวนมากต้องทำเป็น batch เล็กๆ ไม่ใช่ UPDATE/DELETE ทีเดียวทั้งตาราง (lock นาน + transaction log บวม)
- ทุก migration ที่แก้ตารางใหญ่ ต้องมี rollback plan และทดสอบบน staging ก่อน — ไม่ใช่แก้ production แล้วดูหน้างาน
- ก่อนรัน ต้องมี backup ตาม `measure-twice` Phase 4 (Deploy) เสมอ

Sources: [Zero-Downtime Database Schema Migration Strategies (Bytebase)](https://www.bytebase.com/blog/zero-downtime-database-schema-migration/), [Database Migrations in Production: Zero-Downtime Schema Changes](https://dev.to/young_gao/database-migrations-in-production-zero-downtime-schema-changes-5fng)
