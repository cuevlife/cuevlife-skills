---
name: migrate-vetmanage
description: Migrate a drxvet (dd2vmn) MySQL dump into VetManage-ready owner/pet/item CSV+XLSX. ONLY invoke when the user explicitly types /migrate-vetmanage — never auto-trigger from context, even if the conversation mentions VetManage, drxvet, or pet-clinic data migration.
---

# Migrate drxvet -> VetManage

ทำงานเมื่อถูกเรียกผ่าน `/migrate-vetmanage` เท่านั้น ห้าม invoke เองจาก context แม้จะเห็นคำว่า VetManage/drxvet/dd2vmn ในบทสนทนา

## Precondition — ต้องมีครบก่อนเริ่ม

ต้องได้ไฟล์ 4 อย่างจาก VetManage (export จากหน้า admin จริง เป็น HTML table หรือเทียบเท่า) ก่อนเริ่มงานเสมอ:
1. รายการ**ชนิดสินค้า** (item_type) — id, ชื่อไทย, ชื่ออังกฤษ
2. รายการ**หน่วยสินค้า** (unit) — id, ชื่อไทย, ชื่ออังกฤษ
3. รายการ**ชนิดสัตว์** (species) — id, ชื่อไทย, ชื่ออังกฤษ
4. รายการ**สายพันธุ์** (breed) — id, ชื่อพันธุ์, ชนิดสัตว์

ถ้าไม่มีครบ 4 ไฟล์ **ให้หยุดและถามหาก่อน** ห้ามเดา/สมมติ target lookup list เอง เพราะ id จริงในระบบต้องตรงเป๊ะ ไม่งั้น import แล้วจะไปทับข้อมูลผิด id

## ขอ parameter จากผู้ใช้ทุกครั้งที่รัน (ห้าม hardcode path)

ถามให้ครบก่อนเริ่มทำงาน:
- path ไฟล์ SQL dump ต้นทาง (drxvet mysqldump, .sql)
- path โฟลเดอร์ที่มีไฟล์ mapping 4 อย่างด้านบน
- ชื่อ MySQL database ที่จะ import เข้าไป (แนะนำชื่อที่สื่อถึงคลินิก/ลูกค้า ไม่ชนกับ database เดิมที่มีอยู่ — เช็ค `SHOW DATABASES` ก่อนเสมอ)
- path โฟลเดอร์ output

## เครื่องมือที่ใช้ (เช็คว่ามีจริงก่อนใช้ อย่าสมมติ path)

- MySQL client: หา `mysql.exe` ใน XAMPP (`.../xampp/mysql/bin/mysql.exe`) หรือจากที่อื่นที่ `where.exe mysql.exe` เจอ — root ไม่มี password ปกติ ลอง connect เปล่าก่อน
- Python + openpyxl สำหรับสร้าง .xlsx (เช็คด้วย `python -c "import openpyxl"` ก่อน ถ้าไม่มีให้แจ้ง user)

### ⚠️ MySQL Thai encoding bug (สำคัญมาก)

รัน query ผ่าน `mysql -e "..."` แบบ inline บน Windows แล้วมี literal ภาษาไทยอยู่ใน query (ทั้ง SELECT list, WHERE clause, INSERT VALUES) **ค่าไทยจะเพี้ยนเป็น `?????` แม้ใส่ `--default-character-set=utf8` แล้วก็ตาม** เกิดจาก Windows argv encoding ตอนส่งผ่าน command line ไม่ใช่ปัญหา MySQL

**กฎ**: ทุก query/insert ที่มี literal ภาษาไทย ต้องเขียนลงไฟล์ `.sql` ด้วย Write tool ก่อน (utf-8 ไม่มี BOM) แล้ว `mysql ... --default-character-set=utf8 < file.sql` เท่านั้น — ห้ามใช้ `-e "...ภาษาไทย..."` เด็ดขาด ส่วน query ที่ไม่มี literal ไทย (column/table name ล้วน) ใช้ `-e` ได้ปกติ

CSV/text dump จาก drxvet มักเป็น **CP874** ไม่ใช่ UTF-8 — เช็คด้วย `file` ก่อน ถ้าอ่านไม่ออกให้ `iconv -f CP874 -t UTF-8`

## ขั้นตอน

### 1. Import ข้อมูลต้นทาง
สร้าง database ใหม่ (utf8), import ไฟล์ .sql, verify ด้วย table count + row count ของ `customer`/`pet`/`stock` (ชื่อ table มาตรฐานของ dd2vmn)

### 2. Parse ไฟล์ mapping 4 อย่างจาก VetManage
แปลงเป็น target lookup table ในใจ (id, ชื่อไทย, ชื่ออังกฤษ, species สำหรับ breed) — นี่คือ "ของเดิมที่มีอยู่แล้วในระบบจริง" (`status=EXISTING`)

### 3. จับคู่ source -> target แต่ละหมวด
- **item_type**: จาก `stock_type.typename` เทียบชื่อกับ target item_type — ตรงชื่อ/ความหมายชัดเจนใช้ id เดิม ไม่ตรง = ใหม่ ต่อท้าย id สูงสุดเดิม
- **unit**: หา table หน่วยจริงที่ `stock` join ผ่าน (ปกติชื่อทำนอง `rxtx_unit`, join ผ่าน field แบบ `stock_number_rxtx_unit_id` หรือใกล้เคียง) — เทียบกับ target unit ตรงชื่อ/synonym (เช่น Vial≈ขวด, Pack≈ซอง) ที่เหลือใหม่
- **species**: pet.pettype (dog/cat/others ปกติ) join ตรงกับ target ก่อน ถ้ามี field แบบ `pettypeothers` (ชนิดสัตว์จริงตอน pettype='others') **ต้องดึงมาด้วย อย่าข้าม** ทำ keyword matching กับ target species ที่มีอยู่แล้ว (เช่น คำว่า "นก" อยู่ใน species text -> map ไปที่ target นก) ส่วนที่ไม่มี match ค่อยตกลงกับ user ว่าจะลงที่ species ไหน (ปกติ "อื่นๆ"/"ชนิดพิเศษ")
- **breed**: petbreed (free text จาก user กรอกเอง) เทียบ normalize (ตัดช่องว่าง/จุด, lowercase ฝั่งอังกฤษ) กับ target breed name ก่อน, เทียบผ่าน EN-TH synonym lookup table ของ source เอง (ถ้ามี) เป็นลำดับสอง, ที่เหลือ nomatch = พันธุ์ใหม่จริง — ✋ **ก่อนสร้าง breed ใหม่ ต้อง dedupe ภายในกลุ่ม nomatch เองก่อนเสมอ** (คนละสะกด/EN-TH ของพันธุ์เดียวกันมักถูกนับซ้ำเป็นสิบรายการ — รวมให้เหลือ canonical เดียวก่อนค่อยเสนอสร้างใหม่ ไม่งั้น breed ใหม่จะบวมจนกรอกเองไม่ไหว)
- ค่า placeholder แบบ "ไม่ระบุ"/"unspecified" ไม่ถือเป็นพันธุ์/หน่วย/ชนิดจริง — resolve เป็นค่าว่างเสมอ ไม่สร้าง master data ใหม่ให้

### 4. ยืนยันขนาดกับ user ก่อนสร้าง breed/unit/item_type ใหม่จำนวนมาก
ถ้านับได้เกิน ~50 รายการใหม่ในหมวดไหน ให้เตือน user ก่อนว่าต้องกรอกเองใน VetManage เยอะแค่ไหน และเสนอทางลด (dedupe เพิ่ม / ตัดกลุ่มความถี่ต่ำออกไปเก็บใน note แทนการสร้าง master data ใหม่) — อย่าสร้างรวดเดียวโดยไม่เตือน

### 5. Business rules ที่ fix ตายตัว (ไม่ต้องถาม user ซ้ำ)

**owner:**
- `mobile` = source phone field (เช่น tel_1), `phone` = source mobile field (เช่น mobile_1) — สลับกัน ให้ mobile มาก่อนเสมอ
- address รวม sub-fields (บ้านเลขที่+ซอย+ถนน+ตำบล+อำเภอ) เป็น address1 เดียว, จังหวัดแยกไปช่อง county
- status: มี hidden/deleted flag = 0, ไม่มี = 1

**pet:**
- owner_id ใช้เลข id ของ owner ตรงๆ (join ผ่าน source's customer FK เช่น cuid) ไม่ใช่ text — ต้อง import owner ก่อน pet เสมอเพราะ id ต้องตรงกัน
- species: ใช้ pettypeothers ประกอบด้วยเสมอถ้ามี field นี้ (ดูข้อ 3)
- breed_id: species หลัก (สุนัข/แมว) resolve ตามข้อ 3, ส่วน species แปลก/exotic ที่ target ไม่มี breed list รองรับ — **ปล่อย breed_id ว่าง แล้วเอาชื่อชนิด/พันธุ์เดิมไปต่อท้าย note แทน** (ข้อความล้วน ไม่ใส่ [] หรือ prefix ใดๆ) ไม่สร้าง breed master data สำหรับกลุ่มนี้
- ทุก field ที่ต้นฉบับเป็น placeholder "ไม่ระบุ" (microchip, birthday, earmark, สี ฯลฯ) -> ค่าว่างเปล่า ไม่ใส่ text "ไม่ระบุ" ลงไปตรงๆ
- birthday: ถ้า source เป็นวันที่ไทย "D เดือนไทย พ.ศ." แปลงเป็น `YYYY-MM-DD` (ลบ 543 ปี), parse ไม่ได้ = ว่าง
- color: แปลงชื่อสีไทยเป็น hex ด้วยตารางนี้ (ไม่ตรง/สีผสม = ว่าง):
  ขาว=#FFFFFF ดำ=#000000 น้ำตาล=#8B4513 แดง=#FF0000 ส้ม=#FFA500 เหลือง=#FFFF00 เขียว=#008000 ฟ้า=#87CEEB น้ำเงิน=#0000FF ม่วง=#800080 ชมพู=#FFC0CB เทา=#808080 ครีม=#FFFDD0 ทอง=#FFD700 เงิน=#C0C0C0 สลิด=#D2B48C สามสี=#D2B48C ลายสลิด=#D2B48C
- status: 1=ใช้งานปกติ, 0=ระงับการใช้งาน (hidden/deleted flag), 2=สัตว์เสียชีวิต (dead flag) — เช็คลำดับ priority: dead ก่อน hidden

**item:**
- item_code / barcode แยกกันตาม field จริงของ source (อย่ารวม)
- name_eng = generic/scientific name field ถ้ามี ไม่มี fallback เป็น name_local
- unit=-1 หรือ "ไม่ระบุ" -> unit_id ว่าง
- no_vat: 1 = ไม่มี VAT (exclude flag=1), **ว่าง** = มี VAT (ไม่ใช่ 0)
- ข้าม field ด้านยา/ใบสั่งยา (dose, unit_dose, medication, daily, period, every, suggestion) เว้นแต่ source มี table แยกที่ join ได้จริง

### 6. Sentinel/placeholder rows
เช็ค table หลัก (customer/pet/stock) หา row ที่เป็น sentinel (เช่น uid=-1, ทุก field NULL, hospital_id=-1) แล้วตัดออกจาก export เสมอ — ไม่ใช่ข้อมูลจริง

### 7. Output

โครงสร้างคอลัมน์ (ชื่อ+ลำดับ) ของ `owner`/`pet`/`item` **ต้องตรงกับ VetManage template จริงเป๊ะ** ถ้า user มีไฟล์ template (.xlsx) ให้อ่านหัวคอลัมน์จากไฟล์นั้นก่อนเสมอ (ผ่าน python openpyxl, อย่าเดา schema เอง) รวม field เสริมที่มักไม่รู้ล่วงหน้าเช่น `id`, `store_id`, `created_date/by`, `modified_date/by` — ปล่อยว่างให้ VetManage auto-set เอง ยกเว้น `id`/`store_id` ที่ควรใส่ (id = source uid เรียงลำดับเดิม, store_id = 1 ถ้าเป็นคลินิกเดียว)

สร้างในโฟลเดอร์ output:
- `owner.csv`, `pet.csv`, `item.csv` — พร้อม import
- `full_species.csv`, `full_item_type.csv`, `full_unit.csv`, `full_breed.csv` — master data แบบเต็ม (EXISTING เดิม + NEW ใหม่ที่ dedupe แล้ว) เรียง id ต่อเนื่องกัน สำหรับสร้างใน VetManage ก่อน import owner/pet/item
- `vetmanage_migration.xlsx` — รวมทุก sheet ข้างบนในไฟล์เดียว (ผ่าน python+openpyxl, header ตัวหนา + freeze แถวแรก), sheet order ตรงกับ template
- `README.md` — สรุป field mapping ทุกจุดที่ resolve ยังไง + ข้อสมมติที่ยังไม่ confirm 100% + จำนวน row ที่ resolve ได้/ไม่ได้ในแต่ละหมวด ให้ user เช็คได้

## เมื่อ user ขอแก้ field mapping/business rule เพิ่มเติมระหว่างทาง

แก้ที่ SQL/script แล้ว **rebuild ใหม่ทั้ง pipeline ที่ได้รับผลกระทบ** (ไม่ใช่แก้แค่ CSV ปลายทาง) เพื่อให้ full_breed/full_unit/full_item_type กับ owner/pet/item ยัง sync กันอยู่เสมอ แล้วอัปเดต README ให้ตรงกับกฎล่าสุดทุกครั้ง
