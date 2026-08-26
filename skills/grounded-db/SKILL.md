---
name: grounded-db
description: Database schema, SQL, table design, normalization, migration — pragmatic rules for real production databases. ใช้เมื่อสร้าง/แก้ตาราง, เขียน query, ทำ migration, import xlsx/csv
---

# Grounded DB — Pragmatic Rules

## Objective

ออกแบบ schema, เขียน SQL, ทำ migration/data import ให้ถูกหลัก production — ใช้เมื่อสร้าง/แก้ตาราง, เขียน query, import xlsx/csv

## Steps/Workflow

0. **ก่อนสร้างตารางใหม่ (หรือคอลัมน์ใหม่แบบเผื่อไว้)** → verify: มีใครจะ**เขียน**มันจริงจาก UI/flow ที่มีอยู่จริงไหม ไม่ใช่แค่ "เผื่ออนาคต" — ดูหัวข้อ [Code-first vs DB-first](#code-first-vs-db-first)
1. **ออกแบบ schema** → verify: status เป็น int, ไม่มี duplicate data, FK ชัดเจน, มี created_at/updated_at ทุกตาราง
2. **กำหนด type/size ตามการใช้งานจริง** → verify: column ยาวเท่าที่ใช้จริง ไม่เผื่อเกิน — username VARCHAR(30) ถ้าไม่มีใครใช้เกิน 30 (ห้าม VARCHAR(255) มั่ว), DECIMAL(12,2) ไม่ใช่ FLOAT, ENUM หลีกเลี่ยง (ใช้ TINYINT + constant)
3. **เขียน migration** → verify: soft delete ก่อน hard delete, critical write ใช้ transaction + FOR UPDATE
4. **Import xlsx/csv** → verify: read → map → validate → ยืนยันกับ user → execute
5. **ตรวจก่อน INSERT** → verify: รายงาน conflict/duplicate/missing FK ให้ user เห็น

## Examples

```
❌ status VARCHAR('active','closed')    → string เปรียบเทียบช้า, typo ได้
✅ status TINYINT + Slip::STATUS_ACTIVE → int + constant ใน model

❌ username VARCHAR(255)                → เผื่อเกินจริง — เสีย index/MEMORY
✅ username VARCHAR(30)                 → เท่าที่ใช้จริง (เช็คข้อมูลจริงก่อนตั้ง)

❌ import ข้อมูลทันทีเมื่ออัปโหลด       → ผิดพลาดแล้วแก้ยาก
✅ แสดง preview conflicts/duplicates  → รอ user ยืนยัน → execute

❌ DELETE ข้อมูลเด็ดขาดทันที
✅ UPDATE status=0 (soft delete) ก่อน — เก็บประวัติไว้
```

## Code-first vs DB-first

หลักตัดสิน: **ตารางนี้เคยถูกเขียนจริงจาก UI/flow ในแอปไหม (ไม่ใช่แค่ insert ตอน migration/seed)?**

- **ไม่เคยมีใครเขียนเลย ณ runtime** (เปลี่ยนได้ทาง migration อย่างเดียว) → ย้ายเข้า code เป็น constant/array ไปเลย ไม่ต้องมีตาราง ตัวอย่างจริง: `roles`/`permissions`/`role_permissions` ใน vpay ถูกรื้อทิ้งเพราะไม่มี UI ไหนสร้าง role หรือแก้ grant จริง (`Role.php`, `PermissionService::ROLE_PERMISSIONS`)
- **ข้อมูลจริงที่เปลี่ยนบ่อยผ่าน UI แต่เป็น list เล็กๆ ไม่มีตารางอื่น join เข้ามา** → ไม่ต้องแยกตารางใหม่ เก็บเป็น JSON ใน generic key-value config table (เช่น `settings`) แทน ประหยัด migration ตัวอย่างจริง: `bank_accounts` (list เลขบัญชี, ไม่มี FK ชี้เข้ามา) พับเข้า `settings['bank_accounts']` เป็น JSON array — โมเดล (`BankAccount.php`) คง public API เดิมทั้งหมด เปลี่ยนแค่ internal storage
- **ข้อมูลจริงที่ join/query/filter บ่อย หรือมี FK จากตารางอื่นชี้เข้ามา** → ต้องเป็นตารางแยกเสมอ (index/join ทำกับ JSON blob ไม่ได้) เช่น `loans`, `payments`
- **free-text ต่อ record ที่ไม่มีชุดค่าจำกัด** (เช่น เหตุผลปิดสัญญาที่คนพิมพ์เอง) → เป็น column ธรรมดาใน DB เสมอ ย้ายเข้า code ไม่ได้เพราะไม่มี "ชุดค่า" ให้ทำเป็น constant

**ข้อควรระวัง**: หลัก "DB โดนขโมยแล้วไร้ประโยชน์เพราะต้องพึ่ง code" ใช้ได้กับ**โครงสร้าง/นิยาม** (id ไหนคือ role อะไร) เท่านั้น ไม่ใช้กับ**ข้อมูลจริงที่มีค่าในตัวเอง** (เลขบัญชี, รหัสผ่าน) — เลขบัญชีมีค่าเท่ากันไม่ว่าจะอยู่ใน DB หรือถูก grep เจอในซอร์สโค้ด แถมย้ายเข้า code จะทำให้ข้อมูลจริงฝังถาวรใน git history (กระจายกว้างกว่า DB access อีก) และแก้ค่าทุกครั้งต้อง deploy โค้ด — ถ้าอยากให้ DB dump อย่างเดียวไร้ประโยชน์จริง ให้เข้ารหัสคอลัมน์นั้นด้วย key ที่อยู่นอก DB (เช่น .env) แทนการย้ายข้อมูลเข้า source code

## Constraints

- ห้าม hardcode id/status ใน SQL — ใช้ constant จาก model
- ห้ามตั้ง column type/size เผื่อเกินกว่าการใช้งานจริง — VARCHAR(255) ทุก column = schema ขยะ
- ห้ามสร้างตารางใหม่ "เผื่ออนาคต" โดยไม่มี UI/flow เขียนจริง ณ ตอนนั้น — เพิ่มทีหลังตอนมี flow จริงดีกว่า (ย้อนกลับยาก ต้อง migration รื้อ)
- ห้าม auto-import โดยไม่ให้ user ยืนยัน
- ห้าม JOIN/query ที่ไม่ใช้ index — ดู EXPLAIN เมื่อ table ใหญ่
- ห้าม SQL ที่อ่านไม่ออก (alias ลึกลับ, subquery ซ้อนลึก)

## Normalize status as int

- `status` = TINYINT (1=active, 0=archived)
- `workflow_status` = TINYINT state machine
- Constants in model: `Slip::STATUS_ACTIVE`, `Slip::WORKFLOW_PENDING`

## No duplicate data

- One source of truth per fact
- JSON columns for flexible config only
- FK relationships explicit

## JSON columns — MariaDB has no native JSON storage type

MariaDB (ต่างจาก MySQL 8) **ไม่มี native binary JSON type** — keyword `JSON` เป็นแค่ alias ของ `LONGTEXT` + `CHECK(json_valid(...))` เท่านั้น (`SHOW CREATE TABLE` จะโชว์ `longtext` เสมอ ไม่ใช่บั๊ก) เพดาน LONGTEXT คือ 4GB ต่อค่อลัมน์ ซึ่งเผื่อเกินจริงสำหรับ config เล็กๆ เกือบทุกกรณี

- ถ้า content จริงเล็ก (toggle, list สั้นๆ ไม่กี่สิบ row) → เขียน CHECK เองบน `TEXT` แทนใช้ keyword `JSON` ตรงๆ: `value TEXT NOT NULL CHECK (json_valid(value))` ได้ enforcement เดียวกัน แต่เพดานแค่ 64KB ไม่ใช่ 4GB
- ถ้า content อาจโตได้จริง (ไม่มีเพดานชัดเจน) → ใช้ `JSON`/`LONGTEXT` ตามปกติ
- Aggregate เป็น JSON array ใน SQL บน MariaDB ≤10.4 อย่าใช้ `JSON_ARRAYAGG` (ต้อง 10.5+) — ใช้ `CONCAT('[', GROUP_CONCAT(JSON_OBJECT(...) SEPARATOR ','), ']')` แทน (ใช้ได้ตั้งแต่ 10.2+)

## Migration

- `created_at` / `updated_at` on every table
- Soft delete (status=0) before hard delete
- `FOR UPDATE` + transaction for critical writes

## Data migration (xlsx/csv)

1. Read source → map fields → validate → confirm with user → execute
2. Never auto-import without user confirmation
3. Report conflicts, duplicates, missing FKs before INSERT
