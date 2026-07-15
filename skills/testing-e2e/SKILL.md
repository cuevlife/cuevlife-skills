---
name: testing-e2e
description: E2E testing สำหรับ dev จริง — ใช้ chrome-devtools MCP เท่านั้น, ห้ามเขียน code, รายงาน bug. Use ONLY for e2e testing, form validation, UI bug hunting, regression test
---

# E2E Testing — Subagent Mode

**Mode: SYNC (ต้องรอ)** — เทสเสร็จ main agent ถึงจะรู้ว่าแก้ถูกหรือยัง ถ้าเทส regression ย่อยๆ background ได้แต่ main ต้องเช็คผลก่อน implement ต่อ

ใช้ chrome-devtools MCP เท่านั้น ห้าม Playwright / Puppeteer / Codecept

---

## กฎเหล็ก

### 1. ห้ามเขียน code ใดๆ

- **ห้าม** สร้างไฟล์ test script, spec, หรือ automation code
- **ห้าม** แก้โค้ด app เพื่อให้ test ผ่าน
- ใช้แค่ chrome-devtools tools: `take_snapshot`, `fill`, `click`, `take_screenshot`, `list_console_messages`, `list_network_requests`
- เทสแล้วรายงาน bug ไปให้ main agent จัดการ

### 2. ทำงานเป็น subagent เท่านั้น

skill นี้ถูกเรียกผ่าน `task` tool เสมอ ห้ามรันเทสตรงๆ ใน main chat

### 3. พบ bug = หยุด + รายงาน

เจออะไรไม่ถูกต้อง → บันทึกหลักฐาน (screenshot + console + network) → รายงานกลับ
**ห้าม** แก้ไขโค้ด หรือ เสนอแนวทางแก้
**ให้ main agent เป็นคนตัดสินใจ**

### 4. Error ที่ไปต่อไม่ได้ (environment/infra) — รายงาน + หยุดทันที

**Error ประเภทนี้: ไม่เกี่ยวกับโค้ดที่กำลังเทส แต่เป็นปัญหาสภาพแวดล้อม**

เจอแล้วรายงาน BLOCKER กลับมา ห้ามพยายาม bypass หรือเทสต่อ:

```
Error ที่ต้องหยุด:
- [ ] 500 error ทุกหน้า — server พัง
- [ ] 404 หน้าหลัก — deploy ไม่ครบ, route หาย
- [ ] PHP Fatal Error แสดงบนหน้าจอ — white screen of death
- [ ] DB connection error — .env ผิด, service ไม่รัน
- [ ] Dependency / CDN / asset ไม่โหลด — js, css 404
- [ ] Login ไม่ได้ — auth system ล่ม
- [ ] Session ไม่ทำงาน — redirect loop
- [ ] CSRF token error ทุก POST — token mechanism พัง
- [ ] API endpoint 500 ทุก request — backend ตาย
- [ ] Blank page / nothing renders — JS รันไม่ผ่าน
- [ ] SSL / Certificate error — https ปัญหา
```

**วิธีปฏิบัติ:**
1. เจอ error → take_screenshot + list_console_messages + list_network_requests
2. รายงาน BLOCKER กลับไป main agent ทันที
3. **ห้าม** ลอง navigate หน้าอื่นต่อ (เสียเวลา)
4. **ห้าม** bypass โดยการเปลี่ยน URL หรือ params
5. ให้ main agent ตัดสินใจว่าแก้ environment ก่อน หรือ ยกเลิกเทสรอบนี้

**เทียบกับ code bug:**
- ถ้า error เกิดจากโค้ดที่เทส → report เป็น FAIL (rule 3)
- ถ้า error เกิดจาก environment/server/infra → report เป็น BLOCKER (rule 4)

### 5. ดูหน้าจอก่อน — DB ใช้ยืนยันว่าข้อมูลตรงกันเท่านั้น

**ลำดับ: ดู UI → ถ้า UI ถูก → ค่อย query DB เช็ค consistency**

```
ขั้นตอน:
1. interact ผ่าน browser → take_screenshot + take_snapshot
2. เช็ค UI: success message, ข้อมูลขึ้นถูก字段, layout ไม่พัง ← หลัก
3. ต่อเมื่อ UI ถูก → query DB เช็คว่าข้อมูลใน DB ตรงกับที่แสดงบนหน้าจอ ← รอง
4. รายงาน: UI = PASS, Data consistency = MATCH / MISMATCH
```

**ใช้ DB verify เมื่อ:**
- submit form → ดูว่าขึ้น success → query DB ว่าข้อมูล insert ถูก字段
- edit → ดูว่าข้อมูลเปลี่ยนบนหน้าจอ → query DB ว่าค่าตรงกัน
- delete → ดูว่าหายจาก list → query DB ว่า soft delete / hard delete

**ข้อห้าม (BYPASS):**
- ❌ ดูแค่ DB แล้วสรุปว่า "ผ่าน" โดยไม่เช็ค UI
- ❌ UI พัง แต่ DB ถูก → สรุปว่า "ผ่าน" (เพราะ user ไม่เห็น DB)
- ❌ query DB แล้วไม่เช็ค UI เลย
- ✅ DB verification = secondary confirmation เท่านั้น

**หลัก: user เห็นอะไร = คำตัดสิน, DB ใช้ confirm ว่าข้อมูลตรง**

### 5. ต้องเช็ค Visual Bug — ของเบี้ยว / หาย / ทับกัน

นอกจาก logic แล้ว ต้องหา UI bug ที่ user เห็น:
- layout พัง — element ซ้อนทับ, float ผิด
- CSS หาย — หน้าโหลดมาไม่สวย, ไม่มี style
- responsive พัง — จอยู่มือถือแล้วเละ
- ภาษาเพี้ยน — ไทยอังกฤษปน, รูปเลขอารบิก
- loading state — submit แล้วไม่มี spinner, button ไม่ disable
- empty state — list ว่างแล้วไม่มีข้อความ
- error state — 500 error หน้าไม่พัง, แสดง message
- missing elements — element ที่ควรมี แต่ไม่ขึ้น

### 6. ใช้ environment จริง

- URL: `http://acc.local/` หรือตามที่ main agent ส่งมา
- login credentials ได้จาก main agent

---

## Flow การเทส (subagent prompt template)

```
ใช้ testing-e2e skill
เทสฟังก์ชัน: {ชื่อฟังก์ชัน}
URL: {base_url}
หน้า: {path}

Steps:
1. navigate ไป {path}
2. take_snapshot → ดู form structure + visual layout
3. เช็ค elements, fields, buttons — ครบ? เรียงถูก? visible?
4. fill ข้อมูล {test data}
5. click submit
6. take_screenshot → เก็บผลลัพธ์ (ใช้ดู visual bug)
7. take_snapshot → เช็ค UI elements เปลี่ยนถูกต้อง
8. list_console_messages → เช็ค JS error
9. list_network_requests → เช็ค API response status

Criteria (จาก UI เท่านั้น ห้าม query DB):
- form submit สำเร็จ? → เช็ค success message บนหน้าจอ
- validation error แสดงถูก字段? → เช็ค error text ข้างฟิลด์
- console ไม่มี error? → เช็ค red messages
- network response status === 200?
- layout ไม่พัง? → element ไม่ซ้อนทับ, ไม่หลุดขอบ
- CSS ครบ? → หน้าไม่ขาว, style มาหมด
- loading state? → button disable + spinner (ถ้ามี)

รายงานกลับ:
- PASS / FAIL / BLOCKER
- ถ้า FAIL → แนบ screenshot path + อธิบาย visual bug
- ถ้า BLOCKER → บอกสาเหตุ (เช่น 404, 500, login required)
```

---

## Subagent Prompt สำเร็จรูป

### สำหรับ subagent ไป identify elements ก่อนเทส

```
ใช้ testing-e2e skill
เป็น role: test scout
URL: {base_url}

Tasks:
1. navigate ไป {path}
2. take_snapshot verbose
3. list elements ที่ interact ได้ทั้งหมด — inputs, selects, buttons, links, checkboxes
4. list form fields พร้อม type (text/number/select/date/file)
5. เช็ค required attributes
6. เช็ค CSRF token
7. ถ้าเป็น form → ดูว่า action path คืออะไร

รายงานกลับ:
- list of fields (name, type, required, placeholder)
- form action URL
- มี CSRF token หรือไม่
- elements ที่น่าสนใจ (hidden inputs, disabled buttons, etc.)
```

### สำหรับเทส validation (UI only)

```
ใช้ testing-e2e skill
เป็น role: test validator
URL: {base_url}
เทสฟอร์ม: {path}
fields: {field1, field2, ...}

Test cases:
1. submit ว่าง → เช็ค required error ข้างฟิลด์ (UI)
2. special chars → `<script>`, `<>"'&`
3. whitespace padding → `  john  `
4. submit twice → เช็คปุ่ม disabled หรือมี duplicate handling
5. submit success → เช็คว่า success message + ข้อมูลขึ้นใน list

⚠️ ห้าม query DB เพื่อ verify — ดูจากหน้าจอเท่านั้น

Steps ทั้งหมด:
take_snapshot → fill → click submit → take_screenshot → take_snapshot อีกครั้ง → list_console_messages → list_network_requests

เช็ค visual:
- error message ขึ้นถัดจากฟิลด์หรือไม่
- ปุ่ม submit ไม่หาย, form ไม่กระตุก
- หลัง success → หน้า redirect หรือมี message

รายงาน:
- แต่ละ test case: PASS / FAIL
- ถ้า FAIL → screenshot + console + visual bug description
- bug severity: critical / major / minor / cosmetic
```

### สำหรับ visual regression test (layout check)

```
ใช้ testing-e2e skill
เป็น role: visual reviewer
URL: {base_url}
เทสหน้า: {path1}, {path2}, {path3}

แต่ละหน้า:
1. navigate ไปหน้า
2. take_screenshot fullPage → เก็บ layout
3. take_snapshot → เช็ค structure
4. เช็ค:
   - elements ครบ (ไม่หาย, ไม่เกิน)
   - ไม่มี element ซ้อนทับกัน
   - responsive: ลอง resize viewport 375x812 (mobile)
   - font / text ไม่เบลอหรือเพี้ยน
   - images / icons โหลดมา
   - console ไม่มี CSS/JS error

รายงาน:
- แต่ละหน้า: PASS / VISUAL_ISSUE / FAIL
- VISUAL_ISSUE: แนบ screenshot + อธิบาย (element ตก, over lap, missing)
- ไม่ query DB
```

### สำหรับ regression test

```
ใช้ testing-e2e skill
เป็น role: regression tester
URL: {base_url}

Smoke test paths:
1. {path1}
2. {path2}
3. {path3}

แต่ละ path:
- navigate → take_snapshot → เช็คหน้าโหลดปกติ
- console ไม่มี JS error
- network status !== 500
- screenshot เก็บไว้

รายงาน:
- overview: ผ่านกี่ข้อ / ไม่ผ่านกี่ข้อ
- แต่ละ path: PASS / FAIL
- ถ้า FAIL → screenshot + console + network
```

### สำหรับเทส role-based access

```
ใช้ testing-e2e skill
เป็น role: security tester
URL: {base_url}

เทส authorization:
1. login as {role} → navigate {protected_path} → ควรได้ 200
2. logout → navigate {protected_path} → ควร redirect login
3. login as {other_role} → navigate {protected_path} → ควรได้ 403

รายงาน:
- แต่ละ scenario: PASS / FAIL
- ถ้า FAIL → screenshot + response status
```

---

## Bug Report Template

รายงาน bug กลับไปให้ main agent ทุกครั้งที่เจอ issue:

```
## Bug Report

### Environment
- URL: {url}
- Page: {path}
- Browser: Chrome (chrome-devtools)
- Timestamp: {datetime}

### Summary
{สั้นๆ ว่าเจออะไร}

### Steps to Reproduce
1. navigate ไป {path}
2. {action}
3. {result}

### Expected
{สิ่งที่ควรเกิดขึ้น}

### Actual
{สิ่งที่เกิดขึ้นจริง}

### Evidence
- Screenshot: {path/to/screenshot.png}
- Console: {error messages ที่พบ}
- Network: {request URL + status + response}

### Severity
[critical / major / minor / cosmetic]

### Category
[validation / authorization / UI/UX / performance / data / security]
```

---

## ประเภท Test Scenarios (สำหรับ Project ACC)

### Authentication
- [ ] Login สำเร็จ — redirect ถูก
- [ ] Login fail — error message แสดง
- [ ] Login 5 ครั้ง fail — lock หรือ rate limit
- [ ] Session timeout — redirect login
- [ ] Logout — กลับไป login
- [ ] CSRF token missing → reject

### Collector CRUD (core business)
- [ ] สร้าง payment — data ถูกต้อง, slip upload ได้
- [ ] ลบ payment (วันเดียว) — ลบได้
- [ ] ลบ payment (วันก่อน) — reject
- [ ] Undo delete — 30s window
- [ ] Batch close loans — ทำงาน
- [ ] สร้าง debtor — ข้อมูลครบ
- [ ] Export CSV — file download

### Loan Management
- [ ] สร้าง loan — zero interest
- [ ] สร้าง loan — fixed interest
- [ ] สร้าง loan — reducing balance
- [ ] validation error — field ไหน error
- [ ] Locked debtor — auto fill

### User Management
- [ ] สร้าง user — role ต่างๆ
- [ ] แก้ไข user — เปลี่ยน role
- [ ] ลบ user — confirm
- [ ] ป้องกัน self-delete
- [ ] ป้องกัน self-demote

### Authorization
- [ ] Debtor → collector routes = 403
- [ ] Collector → /users/* = 403
- [ ] Collector → /system = 403
- [ ] Collector → /audit = 403

### Cross-cutting
- [ ] 404 page — invalid URL
- [ ] 500 error — graceful handling
- [ ] Responsive — mobile layout
- [ ] Search — found / not found
- [ ] Upload — ไฟล์ผิด format

---

## Chrome DevTools MCP Quick Reference

### ก่อนเริ่มเทส
```
1. navigate_page → ไปหน้า target
2. take_snapshot → ดู structure
3. เช็คว่า form/login พร้อม
```

### ระหว่างเทส
```
1. fill → ใส่ข้อมูล
2. fill_form → ใส่หลาย field พร้อมกัน
3. click → submit / button
4. take_snapshot → ดู result
5. take_screenshot → เก็บหลักฐาน
6. list_console_messages → เช็ค JS error
7. list_network_requests → เช็ค API
```

### เช็ค error
```
console messages ที่ level: error, warn, assert, issue
network requests ที่ status: 400, 401, 403, 404, 422, 500
```

---

## Anti-Patterns (ห้ามทำ)

| ห้าม | เพราะ |
|------|-------|
| Query DB เพื่อ verify ผล | ต้องดูจากหน้าจอเท่านั้น — SQL ไม่บอก visual bug |
| เขียน Playwright script | skill นี้ใช้ MCP เท่านั้น |
| แก้โค้ด app | บทบาท tester ห้ามแก้ |
| เสนอ solution ใน report | แค่รายงาน bug, main agent ตัดสิน |
| รันเทสใน main chat | กิน context — ใช้ task tool เสมอ |
| Dump raw logs กลับมา | เก็บลงไฟล์แล้วแจ้ง path |
| เทสแบบไม่มี screenshot | ต้องมีหลักฐานทุกครั้ง |
| ข้ามเทสเพราะ "น่าจะผ่าน" | ต้องเทสจริงเสมอ |
| สรุปว่า "success" โดยไม่เช็ค visual | success DB ≠ success UI |

---

## สรุป Workflow

```
main agent (SYNC - ต้องรอ):
  → [fix something]
  → task tool → subagent (testing-e2e)
    → navigate → snapshot → fill → click → screenshot → console → network
    → รายงาน PASS/FAIL + หลักฐาน ← UI verification เท่านั้น ห้าม DB
  → main agent รอผล → ผ่าน? → ไปต่อ
                          → ไม่ผ่าน? → แก้ใหม่ → เทสใหม่

main agent (BACKGROUND - regression เล็ก):
  → [implement feature A]
  → task tool → subagent (regression/visual) ← background
  → [implement feature B] ← ทำงานต่อได้ ไม่รอ
  → subagent ส่งผลมา → เช็ค visual ทีหลัง
```

ถ้าผลเป็น FAIL → main agent เท่านั้นที่แก้โค้ด แล้วเรียก subagent รันเทสใหม่

ห้ามบอกว่าเทสผ่านถ้าไม่ได้รันจริง
