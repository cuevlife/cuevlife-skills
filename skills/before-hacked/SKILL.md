---
name: before-hacked
description: Security checklist พื้นฐานก่อน deploy — SQL injection, XSS, CSRF, file upload, secrets, session, error leak ใช้เมื่อเขียนโค้ดที่รับ input/เก็บ secret/อัปโหลดไฟล์ หรือตรวจงานก่อนขึ้น production (ต่างจาก built-in /security-review ที่รีวิว diff ปัจจุบันจริง — อันนี้คือ checklist หลักการใช้ตอนเขียนโค้ด)
---

# Before Hacked — Pragmatic Security Checklist

## Objective

กัน blind spot ด้าน security พื้นฐานของงาน solo dev — ตรวจให้ครบก่อน deploy โดยไม่ต้องเป็น expert: SQLi, XSS, CSRF, file upload, secrets, session, error leak

## Steps/Workflow

1. **Scan secrets** → verify: ไม่มี password/API key/token ในโค้ดหรือ git history — ใช้ Env/config เท่านั้น
2. **เช็ค input ทุกทาง** → verify: DB query ใช้ prepared statement เสมอ (ห้ามต่อ string), output escape ทุกจุด, validation ฝั่ง server (ไม่ใช่แค่ client)
3. **เช็ค CSRF** → verify: ทุก write action (POST/PUT/PATCH/DELETE) มี token ตรวจ
4. **เช็ค file upload** → verify: จำกัดประเภท/ขนาด, ตั้งชื่อใหม่, เก็บนอก web root หรือเช็ค execution, ไม่เชื่อ extension จาก user
5. **เช็ค session/auth** → verify: cookie HttpOnly + SameSite, logout จริง, role check ฝั่ง server ทุก action ที่ต้องสิทธิ์
6. **เช็ค error leak** → verify: error/stack trace/DB message ไม่โผล่หน้าจอ user — log ไว้ข้างหลัง
7. **เช็ค headers/อื่นๆ** → verify: X-Frame-Options/CSP ที่จำเป็น, rate limit บน endpoint ละเอียดอ่อน (login, upload), hash password (bcrypt/argon2)

## Examples

```
❌ $sql = "SELECT * FROM users WHERE name = '" . $_POST['name'] . "'";   → SQLi
✅ $db->prepare("SELECT * FROM users WHERE name = ?") → bind value

❌ echo $row['comment'];                          → XSS
✅ echo e($row['comment']);

❌ $password === $row['password']                 → raw เทียบ + เก็บ plaintext
✅ password_verify($input, $row['password_hash']) → bcrypt

❌ echo "Database Error: " . $e->getMessage();    → leak schema/credential
✅ log($e->getMessage()) + แสดงข้อความทั่วไป

❌ if ($_FILES['img']['type'] === 'image/png')    → ปลอมได้
✅ ตรวจ magic bytes + ตั้งชื่อ random + เก็บนอก web root
```

## Constraints

- ห้าม hardcode secret — password/API key ต้องผ่าน Env/config เท่านั้น
- ห้ามต่อ SQL string ด้วย input — prepared statement เสมอ
- ห้าม echo error/stack trace ตรงๆ หน้าผู้ใช้
- ห้ามเชื่อ extension/type จาก client ฝั่งเดียว — ตรวจด้วย
- ห้าม deploy ก่อนผ่าน checklist ข้อ 1-7 ครบ
- ห้ามเก็บ password plaintext หรือ hash ที่ไม่ใช่ bcrypt/argon2
