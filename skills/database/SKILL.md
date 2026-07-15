---
name: database
description: วิธีสร้าง database แบบติดดิน — schema จริงที่ใช้ใน production, normalize status เป็น int, ไม่ซ้ำข้อมูล, migration พื้นฐาน. Use ONLY when user asks about database schema, SQL, table design, normalization, migration, or data structure
---

# Database — สไตล์คนทำงานจริง

---

## กฎเหล็ก

### 1. Status ต้องเป็น int, ห้ามเป็น string

```sql
-- ✅ ดี — query ไว, index ง่าย, เปลี่ยนชื่อ label ได้
status TINYINT(1) NOT NULL DEFAULT 1
-- 1 = active, 0 = inactive/archived

-- ❌ ไม่เอา — query ช้า, กินเนื้อที่, เปลี่ยน label ทีต้อง update ทั้ง table
status VARCHAR(20) DEFAULT 'ปกติ'
```

**ในโค้ด:** ใช้ constant แทนค่า magic number
```php
class User {
    const STATUS_ACTIVE = 1;
    const STATUS_SUSPENDED = 0;
}
```

### 2. ห้ามเก็บซ้ำ — ถ้าซ้ำต้องมีเหตุผล

- ชื่อลูกค้าซ้ำกันหลายตาราง → ควรมีแค่ใน `customers` แล้ว `JOIN`
- ถ้าต้องเก็บซ้ำจริงๆ (เช่น denormalize เพื่อ performance) → เขียน comment ไว้ด้วย

### 3. ชื่อ column ให้สั้นแต่เข้าใจ

```sql
-- ดี
created_at, updated_at, status, role, config, user_id

-- ไม่ดี
user_authentication_information, customer_billing_address_detail
```

### 4. PK = auto-increment int → UUID เฉพาะตอนจำเป็น

```sql
id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY  -- 99% ของตาราง
-- UUID เฉพาะตอน: sync ข้าม server, offline-first
```

### 5. JSON column = เพื่อนสนิท

สำหรับ data ที่ structure เปลี่ยนบ่อย หรือไม่ต้อง query แยก:
```sql
config JSON NOT NULL,         -- settings, preferences
extracted_data JSON NULL,     -- ai scan results, flexible fields
meta JSON NULL,               -- logs, audit trail
```

**ข้อดี:** ไม่ต้อง migration ทุกครั้งที่เพิ่ม field  
**ข้อเสีย:** query ลึกยาก — ใช้เมื่อไม่ต้อง WHERE บ่อยๆ

---

## Schema Template

### ตารางพื้นฐานที่แทบทุก project มี

```sql
CREATE TABLE users (
    id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    email VARCHAR(255) NOT NULL UNIQUE,
    role TINYINT NOT NULL DEFAULT 1,    -- 1=user, 5=admin, 9=super
    status TINYINT(1) NOT NULL DEFAULT 1,
    config JSON NULL,                    -- preferences, settings
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);

CREATE TABLE companies (
    id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    status TINYINT(1) NOT NULL DEFAULT 1,
    config JSON NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);

CREATE TABLE customers (
    id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    company_id INT UNSIGNED NOT NULL,
    name VARCHAR(255) NOT NULL,
    status TINYINT(1) NOT NULL DEFAULT 1,
    config JSON NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (company_id) REFERENCES companies(id) ON DELETE CASCADE
);
```

---

## Enum → Int Map แทน ENUM type

```sql
-- ❌ ENUM — แก้ลำบาก, เพิ่มค่าทีต้อง ALTER TABLE
status ENUM('pending','active','suspended','deleted')

-- ✅ TINYINT + constant ในโค้ด
status TINYINT NOT NULL DEFAULT 0
-- 0 = pending, 1 = active, 2 = suspended, 9 = deleted
```

**ใน PHP:**
```php
const STATUS_PENDING = 0;
const STATUS_ACTIVE = 1;
const STATUS_SUSPENDED = 2;
const STATUS_DELETED = 9;
```

**เวลาสร้าง dropdown ใน UI:**
```php
$statusOptions = [
    0 => __('Pending'),
    1 => __('Active'),
    2 => __('Suspended'),
    9 => __('Deleted'),
];
```

---

## Migration แบบติดดิน (ไม่มี framework)

สร้างไฟล์ `migrations/` ไว้ตั้งแต่เริ่ม project:

```sql
-- migrations/001_create_users.sql
CREATE TABLE IF NOT EXISTS users (...);

-- migrations/002_add_phone_to_users.sql
ALTER TABLE users ADD COLUMN phone VARCHAR(20) AFTER email;
```

**track ความคืบหน้า:** สร้างตาราง `migrations` ไว้ว่าอะไรรันแล้วบ้าง

```sql
CREATE TABLE migrations (
    id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    migration VARCHAR(255) NOT NULL UNIQUE,
    ran_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

---

## Index — ใส่เท่าที่จำเป็น

```sql
-- column ที่ใช้ WHERE, JOIN, ORDER BY → ควรมี index
INDEX(company_id),
INDEX(status),
INDEX(created_at),

-- column ที่ไม่เคย WHERE → ไม่ต้องมี index
-- PRIMARY KEY = index อยู่แล้ว
```

---

## สรุปความต่าง: Bad vs Good

| Bad | Good |
|-----|------|
| `status VARCHAR(20) DEFAULT 'ปกติ'` | `status TINYINT DEFAULT 1` |
| `ENUM('a','b','c')` | `TINYINT + constant` |
| 30 columns ใน 1 table | แยก JSON สำหรับ fields ที่ไม่ต้อง query |
| ไม่มี `created_at`/`updated_at` | มีทุกตาราง |
| string PK (UUID ทุกที่) | `INT AUTO_INCREMENT` + UUID เฉพาะจำเป็น |
| ไม่มี index | index บน column ที่ใช้ WHERE/JOIN |
| migration แก้ด้วยมือใน phpMyAdmin | migration ไฟล์ + track table |