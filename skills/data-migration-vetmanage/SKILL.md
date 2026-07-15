---
name: data-migration-vetmanage
description: ย้ายข้อมูล vetmanage — pet_exam, owner_exam, item_exam, species, item_type_id, pet_breed. Use ONLY when user sends xlsx/csv files or asks about data migration, species mapping, item_type mapping, pet_breed mapping for vetmanage
---

# Data Migration — VetManage

---

## ไฟล์ที่ต้องใช้

| ไฟล์ | มีอะไร | อยู่ใน skill |
|------|--------|-------------|
| `pet_exam_vetmanage.csv` (.xlsx) | ข้อมูลสัตว์ (pet) → name, species, breed, sex | ✅ pet_exam section |
| `owner_exam_vetmanage.csv` (.xlsx) | ข้อมูลเจ้าของ → name, surname, phone, mobile | ✅ owner_exam section |
| `item_exam_vetmanage.csv` (.xlsx) | ข้อมูลสินค้า/บริการ → name_local, item_type, price | ✅ item_exam section |
| `species` | รหัสชนิดสัตว์ (1=สุนัข, 2=แมว, ฯลฯ) | ✅ ใน pet_exam |
| `item_type_id` | รหัสประเภทสินค้า (1=ยา, 2=หัตถการ, ฯลฯ) | ✅ ใน item_exam |
| `pet_breed` | รหัสพันธุ์สัตว์ (1-296) | ✅ ใน pet_exam |

## กฎเหล็ก

### 1. species, item_type_id, pet_breed — ห้าม map เอง

**ก่อน map ค่า ต้องถาม user ทุกครั้ง** — ข้อมูลจริงอาจไม่ตรงกับ default

- ข้อมูลบางชุดมาแบบไม่มีค่า → ต้องถาม
- บางชุดมาแบบมีค่าบางส่วน → ต้องถาม
- บางชุดมาแน่นแล้ว → ก็ยังต้องถามเพื่อยืนยัน

### 2. Default range (ไว้参考 ถ้า user ไม่รู้)

| Field | Default Range | หมายเหตุ |
|-------|---------------|----------|
| `item_type_id` | 1-7 | 1=ยา, 2=หัตถการ, 3=Lab, 4=?, 5=สินค้า, 6=Test kit, 7=ยาฉีด |
| `pet_breed` | 1-296 | breed id จากระบบ vetmanage |
| `species` | 1-13 | 1=สุนัข, 2=แมว, 3=กระต่าย... |

**แต่!** แม้มี default — ก็ถามก่อนเสมอ

### 3. Required Fields — ถ้าไม่มีให้ report

| Template | Required | ถ้าขาด |
|----------|----------|--------|
| `pet_exam` | name, species, pet_breed, sex | report + keep raw |
| `owner_exam` | name, surname | report + keep raw |
| `item_exam` | name_local, item_type, price | report + keep raw |

---

## pet_exam_vetmanage.csv

**Header:**
```
id,store_id,opd_no,microchip,owner_id,name,species,birthday,age_y,age_m,sex,breed_id,color,neutering,earmark,picture,note,status,sp_dis,id_card,insurance,created_date,created_by,modified_date,modified_by
```

**Required:** name, species, pet_breed, sex

**species (จากข้อมูลจริง):**
```
0 = ไม่ระบุ
1 = สุนัข
2 = แมว
3 = กระต่าย
6 = หนูแฮมสเตอร์/อื่นๆ
9 = นก
13 = ไก่
14 = หนู
18 = กระรอก
21 = หมู
23 = เป็ด
25 = เต่า
28 = กระต่าย (แบบ 2)
29 = กระต่าย (แบบ 3)
```

**sex:**
```
M = male
F = female
ว่าง = ไม่ระบุ
```

**ตัวอย่าง data:**
```
name,species,birthday,age_y,age_m,sex,breed_id,color,neutering,note
black,1,1970-01-01,56,3,F,10,#312B2B,Y,...
Red,1,1985-01-22,41,2,M,13,#552D2D,Y,...
Pugkung,1,2015-02-06,9,4,M,123,#BE631E,N,Lovely
```

**Clean steps:**
- species: map string → int (หมา→1, แมว→2)
- birthday: 0000-00-00 = unknown → null
- phone: เอา `-` `space` `(` `)` ออก
- sex: normalize เป็น M/F/empty

**ถ้าขาด field:**
```
[Report] row id=5: missing pet_breed → skip, raw saved
[Report] row id=7: missing species → skip, raw saved
```

---

## owner_exam_vetmanage.csv

**Header:**
```
id,store_id,name,surname,nickname,citizen_id,passport,sex,birthday,address1,address2,county,zip_code,phone,mobile,email,line_user_id,picture,tax_id,note,status,created_date,created_by,modified_date,modified_by
```

**Required:** name, surname

**Phone: mobile ก่อน phone**
```
ถ้ามี mobile → ใช้ mobile
ถ้าไม่มี mobile แต่มี phone → ใช้ phone
ถ้ามีทั้งสอง → ใช้ mobile + phone ต่อกัน (fallback)
clean: เอา `-` `space` `(` `)` `ต่อ` `#` ออก
```

**ตัวอย่าง data:**
```
name,surname,nickname,citizen_id,sex,birthday,phone,mobile
สมชาย,D-,โดม,6-5004-0____-__-__,M,2021-03-21,0981112226,0988888888
อุดม,รักษาสัตย์,,1-1111-11111-11-1,M,1988-09-30,,0988465465
```

**ผลลัพธ์:**
```
row 1: mobile = 0988888888, phone_fallback = 0981112226
row 2: mobile = 0988465465, phone_fallback = (none)
```

**Clean steps:**
- normalize phone (เอา `-`, ` `, `ต่อ` ออก)
- citizen_id: เอา `-` ออกเก็บแต่ตัวเลข
- ถ้า name = ว่าง → ใช้ surname แทน หรือ report

---

## item_exam_vetmanage.csv

**Header:**
```
id,store_id,item_code,barcode,name_local,name_eng,item_type_id,stock,cost,price,unit_id,treat_fee,min_stock,no_vat,expire_num,expire_day,regular,course,image,trade_name,indications,drug_use,dose,unit_dose,medication,daily,period,every,suggestion,status,note,redeem_points,account_id,created_date,created_by,modified_date,modified_by
```

**Required:** name_local, item_type (แปลงจาก item_type_id), price

**item_type_id (จากข้อมูลจริง):**
```
1  = ยา/เวชภัณฑ์
2  = หัตถการ/บริการ
3  = Lab/ตรวจ
5  = อาหารสัตว์/สินค้า
6  = Test kit
7  = วัคซีน/ยาฉีด
8  = สินค้าทั่วไป
9  = ค่าแพทย์/Care
13 = วัคซีน
14 = ชุดตรวจ
20 = อุปกรณ์
36 = ค่าใช้จ่ายอื่น
65 = Package/ชุดบริการ
69 = อาหารสัตว์
75 = Package
89 = วิตามิน/เสริม
90 = อาหารเสริม
92 = วัคซีน package
```

**ตัวอย่าง data:**
```
name_local,name_eng,item_type_id,stock,cost,price,unit_id,status
Acetar 1000ml (10/ขวด),Acetar 1000ml,1,462.35,70,300,1,1
CBC (Automate Manual diff),,3,50,0,250,0,1
Cesar puppy,cesar puppy,69,50,25,65,34,1
```

**item_type = int map:**
```php
$itemTypes = [
    1 => 'ยา', 2 => 'หัตถการ', 3 => 'Lab',
    5 => 'สินค้า', 6 => 'Test kit', 7 => 'ยาฉีด',
    8 => 'ทั่วไป', 9 => 'ค่าแพทย์', 13 => 'วัคซีน',
    14 => 'ชุดตรวจ', 69 => 'อาหารสัตว์',
];
```

---

## Report Format

```json
{
  "template": "pet_exam_vetmanage",
  "total_rows": 500,
  "imported": 480,
  "skipped": 20,
  "missing_fields": [
    { "row": 5, "field": "species", "raw": { "name": "black", "species": "" } },
    { "row": 7, "field": "pet_breed", "raw": { "name": "Red", "breed_id": "" } }
  ],
  "errors": [
    { "row": 99, "error": "duplicate microchip", "raw": { ... } }
  ]
}
```

---

## ใช้ Subagent สำหรับ Process จริง

**Mode: SYNC (ต้องรอ)** — migration ต้องได้ SQL + report ก่อน main agent ถึง review + import ต่อได้

### ทำไมต้องใช้ subagent

migration process คือ:
1. main agent ถาม user → ได้ mapping rules
2. main agent วางแผน
3. **delegate process จริงไป subagent** — มันกิน context (read CSV, clean, map, generate SQL)
4. main agent review ผลลัพธ์ + แก้ไข

### Subagent Prompt Template

```
ใช้ data-migration-vetmanage skill
เป็น role: migration executor

Template: {pet_exam / owner_exam / item_exam}
Source: {path/to/csv}

Mapping rules (from user):
- species: {map}
- item_type_id: {map}
- pet_breed: {map}

Tasks:
1. read CSV → parse header + rows
2. validate required fields ตาม skill rules
3. clean data (normalize phone, sex, birthday)
4. map species / item_type_id / pet_breed
5. generate SQL INSERT statements
6. track skipped rows + reason
7. save report.json

Output files:
- {template}_import.sql
- {template}_report.json
- {template}_skipped.csv (ถ้ามี)

ข้อห้าม:
- ห้าม import จริง — generate SQL เท่านั้น
- ห้ามแก้ mapping rules เอง
- ถ้าเจอค่า map ไม่ตรง — หยุด รายงานให้ main agent
```

### Workflow (SYNC)

```
main agent:
  1. ถาม user → ได้ mapping → วางแผน
  2. สร้าง task → subagent (data-migration-vetmanage) ← รอ
  3. subagent ส่งกลับ: SQL + report + skipped
  4. main agent review → ส่งให้ user approve
  5. user approve → main agent รัน SQL
```

---

## สรุป

```
1. เปิด CSV → อ่าน header
2. ตรวจ required fields
3. ถ้าขาด → เก็บ raw + report → ข้ามไป
4. ถ้าครบ → clean → map → import
5. เขียน report.json ไว้ตรวจสอบ
```