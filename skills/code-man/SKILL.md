---
name: code-man
description: สไตล์การเขียนโค้ดครบสาย เน้นใช้ง่าย แก้ง่าย ไม่หลุดกรอบ framework, blast radius, idempotency (double-submit/webhook ซ้ำ), observability/logging, senior UX/UI (5 states, Nielsen heuristics, accessibility) — Human Coding (ไม่โผล่กลิ่น AI + Framework Boundary), Extensible Architecture (config-driven/feature flag/RBAC + สูตร solodev), CSS-First (CSS ก่อน JS เสมอ), Design-from-Reference (สร้างเว็บจากลิงค์ ref จริง), Grounded DB (schema/SQL/migration), Senior UX/UI รวม code + bend-not-break + css-first + design-from-ref + grounded-db เป็นก้อนเดียว ใช้เมื่อเขียน/แก้/ตรวจโค้ดทุกชนิด, refactor ให้ยืดหยุ่น, ทำงาน UI/UX ที่เกี่ยวกับ visual behavior, สร้างเว็บจากลิงค์ ref, หรือสร้าง/แก้ตาราง DB
---

# Code-Man — Full Coding Style

รวม 6 เรื่องของสไตล์การเขียนโค้ดที่ใช้ต่อกันได้ตลอดสาย:

1. **Human Coding** — หลักทั่วไปทุกครั้งที่เขียน/แก้โค้ด
2. **Extensible Architecture** — ตอนออกแบบให้ยืดหยุ่น ไม่ hardcode
3. **CSS-First** — ตอนทำงาน UI/visual (เทคนิค CSS vs JS)
4. **Design-from-Reference** — ตอนสร้างเว็บจากลิงค์ ref จริง (เรียกใช้ #1, #3, #6 ตอน build)
5. **Grounded DB** — ตอนแตะ schema/SQL/migration
6. **Senior UX/UI Principles** — ตอนออกแบบ/ตรวจ UI (states, Nielsen heuristics, accessibility)

---

# 1. Human Coding Style

## Objective

เขียนโค้ดให้ "เหมือนคนเขียน" — อ่านแล้วเข้าใจได้ทันที, ต่อยอดได้, ไม่โผล่กลิ่น AI, **ใช้ง่าย (เรียกใช้ไม่ต้องเดา), แก้ง่าย (บั๊กแล้ว isolate ได้เร็ว), และไม่หลุดกรอบ framework/convention ของโปรเจกต์** ใช้เมื่อเขียนหรือแก้ไขโค้ดทุกชนิด (PHP, JS, SQL, view, doc)

## หลักคิด 6 มิติ

| มิติ | สาระ |
|------|------|
| **Style** | โค้ดอ่านแล้วรู้เรื่องเหมือนคนเขียน — ชื่อบอก intent, flow เป็นธรรมชาติ, ไม่ clever |
| **Easy to Use / Easy to Fix** | เรียกใช้ได้โดยไม่ต้องเปิดอ่าน implementation ก่อน (ชื่อ+parameter บอกพอ), บั๊กแล้ว isolate ได้เร็ว — error ชัด, ไม่ไล่หลายไฟล์เพื่อแก้จุดเดียว |
| **Framework Boundary** | ใช้ mechanism ที่ framework/library มีให้ก่อนเสมอ (ORM, router, validation) ไม่เขียนทางลัดข้าม, และเขียนตาม pattern/convention เดิมของโปรเจกต์ (ดู "Framework Boundary" ด้านล่าง) |
| **Workflow** | พฤติกรรมตอนทำงานเหมือนคน — คิดก่อนแก้, อ่านรอบข้าง, ไม่เร่ง, ไม่ทำเกินขอ |
| **Anti-AI** | ไม่มี AI smell — ตรวจก่อนเสร็จทุกครั้ง (ตารางด้านล่าง) |
| **Collaboration** | ทำงานกับคน — ถามเป็นช้อย, เสนอทางเลือก, ให้คนตัดสินใจเรื่องสำคัญ |

## Steps/Workflow (Human Coding)

งานซับซ้อน/เจอ bug/requirement ไม่ชัด → ผ่าน `measure-twice` skill ก่อน (Phase 0 → เลือก path → execute) แล้วค่อยมาขั้นตอนนี้

1. **เข้าใจก่อนเขียน (Think Before Coding)** → verify: อ่านโค้ด/โดเมนที่เกี่ยวข้องครบ, assumptions ชัด, ตีความได้หลายทาง → present ทั้งหมด ไม่เลือกเงียบๆ, มีวิธีง่ายกว่า → บอก + push back, ไม่ชัดถามเป็นช้อยก่อน 1 บรรทัด
2. **ตั้งเป้าหมายที่ verify ได้ (Goal-Driven)** → verify: มี plan `Step → verify: check`, criteria แข็งแรงพอทำงานต่อได้เองโดยไม่ต้องถามตลอด
3. **ค้นหาวิธีที่เคยผ่านมาก่อน (Research First)** → verify: search โค้ดเดิม/AGENTS.md/docs/web — เคยมีคนแก้แบบนี้ไหม? ใช้ pattern/approach ที่พิสูจน์แล้วว่าผ่านก่อน; ไม่มี → ค่อยออกแบบเอง
4. **หา test case ครอบคลุม** → verify: มี input → expected output ครบทุก path หลัก (รวม edge case); logic ซับซ้อน → เขียนใน tests/ (phpunit), flow/UI → browser E2E
5. **ตั้งชื่อตาม intent** → verify: ชื่อบอก "ทำอะไร" ไม่ใช่ "ยังไง" — `getOverdueLoans()` ไม่ใช่ `getData()`
6. **เขียนแบบเล่าเรื่อง** → verify: อ่านบนลงล่างรู้เรื่อง, early return, ไม่ if ซ้อน
7. **ใช้ของที่มีอยู่** → verify: reuse pattern/helper เดิมของโปรเจค — ไม่สร้างของใหม่ซ้ำ
8. **เช็ค AI smell** → verify: ไล่ตารางด้านล่าง — ไม่มีข้อไหนโผล่
9. **Verify จริง** → verify: test case จาก step 4 ผ่าน (รัน/ตรวจจริง), get_errors/php -l ผ่าน, commit = what: why

## AI Smell — ตรวจทุกครั้งก่อนเสร็จ

| # | AI smell (❌) | ทำแบบนี้แทน (✅) |
|---|--------------|-----------------|
| 1 | ชื่อ generic: `$data`, `$result`, `$item`, `process()` | ชื่อตามโดเมน: `$invoice`, `$overdueLoans`, `sendInvoiceEmail()` |
| 2 | สร้าง abstraction เผื่ออนาคต: interface, factory, service layer | เขียนตรงๆ — pattern ซ้ำ 2 ครั้งค่อย extract |
| 3 | ทำเกินขอ: เพิ่ม validation, logging, error handling ที่ไม่สั่ง | แก้เท่าที่ขอ — เกิน = กลิ่น AI ทันที |
| 4 | Defensive เกิน: เช็ค `null` ทุกจุด, try-catch รอบทุกอย่าง | เช็คเท่าที่จำเป็นจริง — โค้ดสั้นตรงไปตรงมา |
| 5 | เขียนยาวเกิน: 30 บรรทัดที่จบใน 5 | คิดก่อนเขียน — เขียนสั้นที่สุดที่อ่านรู้เรื่อง |
| 6 | Clever/golf: ternary ซ้อน, `??` มั่ว, เขียนสั้นจนงง | ตรงไปตรงมา > ฉลาด — อ่านง่ายคือเป้าหมาย |
| 7 | เรียก DB ซ้ำใน loop (N+1) | ดึงมาครั้งเดียวแล้ว map ใน memory |
| 8 | Comment อธิบาย obvious: `// get user by id` | ไม่ comment — ชื่อฟังก์ชันทำหน้าที่อธิบาย |
| 9 | magic number/string ลอย | named constant ของโปรเจค |
| 10 | ไม่ match pattern เดิมของโปรเจค | ดูไฟล์ข้างๆ แล้วเขียนตาม — local convention ชนะ |
| 11 | Refactor/rename/format ปนกับงาน | แตะเท่าที่ต้องแตะ — ของอื่นไม่เกี่ยวกับงาน |
| 12 | แก้หลายจุดในรอบเดียว | 1 commit = 1 เรื่อง — แก้ทีละจุดดูผลก่อน |
| 13 | ครอบทั้งหน้า PHP ด้วย JS/AJAX ทั้งที่ HTML ล้วนก็ทำงานได้ | native HTML ก่อนเสมอ (`<a href>`, `<form>`, `<details>`) — เสริม AJAX เฉพาะจุดที่ HTML ทำไม่ได้จริง (ดูหัวข้อ 3. CSS-First) |
| 14 | หลุดกรอบ framework (raw SQL ทั้งที่มี ORM, custom router/validator ทับของเดิม) | ดูหัวข้อ "Framework Boundary" ด้านล่าง — เช็ค mechanism ก่อนเขียนเองเสมอ |
| 15 | Return type ไม่คงที่ — ฟังก์ชันเดียวกันคืน array บางครั้ง คืน null บางครั้ง โดยไม่มี type hint บอก, หรือฟังก์ชันเลเยอร์เดียวกัน (เช่น repository ทั้งชุด) บาง function throw บาง function คืน false สำหรับ error แบบเดียวกัน | type hint ชัด (`: ?array`, `: array`) เขียนแบบเดียวกันทั้งเลเยอร์ — ถ้าไม่เจอคืน `[]`/`throw` เลือกทางเดียวแล้วใช้ให้เหมือนกันหมด |
| 16 | Hidden side effect — ชื่อบอกว่า "get/read" แต่ข้างในไป write DB/ไฟล์/session ด้วย | ชื่อต้องสะท้อนของจริง (`getUser()` ต้อง read-only, มี effect → ตั้งชื่อ `getUserAndTouchLastSeen()` หรือแยกเป็น 2 ฟังก์ชัน) |
| 17 | Error/exception message บอกแค่ "invalid input"/"error occurred" ไม่บอกอะไรต่อ | บอกค่าที่ผิดจริงและเงื่อนไขที่ควรจะเป็น — `"qty ต้อง > 0 ได้รับ -5"` ไม่ใช่ `"invalid qty"` |
| 18 | Boolean parameter trap — `sendInvoice($invoice, true, false)` เดาไม่ออกว่า `true`/`false` คืออะไรถ้าไม่เปิด source | ใช้ named argument (`sendInvoice($invoice, sendEmail: true)`), enum/constant, หรือแยกเป็นฟังก์ชันคนละชื่อ (`sendInvoiceSilently()`) |

Item 15-18 มาจากหลักที่เรียกว่า **Principle of Least Astonishment** — ฟังก์ชัน/API ต้องทำสิ่งที่คาดเดาได้จากชื่อ ไม่ทำอะไรที่คนเรียกไม่คาดคิด (อ้างอิง Scott Meyers: "easy to use correctly, hard to use incorrectly")

**ข้อ 3 กับข้อ 4 ไม่ได้ขัดกับหัวข้อ Idempotency/Observability ด้านล่าง — คนละเรื่อง:**
- ข้อ 3 "ทำเกินขอ" หมายถึง feature/validation/logging ที่ "เผื่อไว้" โดยไม่มีเหตุผลจริงรองรับ ไม่ใช่ baseline correctness ที่ action มี side effect จริง (สร้าง order, ตัดเงิน, webhook) ต้องมีเสมอ — idempotency กับ error logging ที่ actionable เป็นของที่ต้องทำแม้ user ไม่ได้พูดถึง เหมือนกฎ security ที่ทำเสมอไม่ต้องรอสั่ง (ดู "ลำดับความสำคัญ" ในหัวข้อ Framework Boundary)
- ข้อ 4 "defensive เกิน" หมายถึง null-check/try-catch แบบเหวี่ยงแหทุกจุดไม่มีเป้า ไม่ใช่การ catch exception ที่รู้แน่ชัดว่าจะเกิดและมีทางจัดการชัดเจน (เช่น duplicate-key exception ในหัวข้อ Idempotency) — catch แบบเจาะจงมีเหตุผลรองรับ ≠ defensive เกิน

## Human Essence — สิ่งที่ทำให้โค้ดดูมีคนเขียน

AI Smell = สิ่งที่ต้องลบ, Human Essence = สิ่งที่ต้องมี — ตรวจว่าโค้ดที่เขียนมีกลิ่นมนุษย์เหล่านี้ไหม

| # | Human essence (✅ ควรมี) | หมายเหตุ |
|---|--------------------------|----------|
| 1 | Hack ที่คิดเองจากปัญหา — แก้จุดที่เจอจริง ไม่ใช่แก้ตาม template | เช่น งัดข้อมูลจาก string, sort ด้วยวิธีง่ายๆ ที่คนจริงใช้ |
| 2 | ตั้งชื่อตามวิธีคิด — ชื่อที่คนเขียนนึกถึงตอนเขียน | ไม่ต้อง perfect convention แต่ต้องอ่านแล้วรู้ว่าคิดอะไร |
| 3 | เขียนตรงๆ ไม่มี layer เกิน — ไม่มี service/interface ที่ยังไม่ต้องใช้ | ใช้ของตรงๆ ก่อน, pattern ซ้ำ 2 ครั้งค่อย extract |
| 4 | Comment บอก "ทำไม/ขั้นตอน" ไม่ใช่ "โค้ดนี้ทำอะไร" | `// ลบรูปเก่าก่อน insert` ✅ — `// get user by id` ❌ |

### ข้อเสียของมนุษย์ที่ห้ามลอก

| ห้าม | เพราะ |
|------|-------|
| Hardcode password/secret ตรงๆ | ผิดกฎข้อ 7 — security |
| Comment โค้ดเก่าทิ้งไว้ทั้งก้อน | ผิดกฎข้อ 6 + รก |
| ตั้งชื่อผิดความหมาย (Sidebar = Navbar) | อ่านแล้วเข้าใจผิด — ตั้งชื่อตาม intent |
| ทำผิดพลาดแล้วปล่อย (undefined variable, ส่ง object ไป json) | ต้อง verify จริง (Step 9) |
| เขียนซ้ำ 8-10 จุดไม่ DRY | pattern ซ้ำ 2+ ครั้ง → extract (ข้อ 3) |

## Framework Boundary — ไม่หลุดกรอบ framework

หลุดกรอบมี 2 แบบ ต้องเช็คทั้งคู่ก่อนเขียนโค้ดในโปรเจกต์ที่มี framework:

1. **หลุดกรอบ mechanism** — framework/library มีทางให้ใช้อยู่แล้ว (ORM, router, validator, migration tool, auth middleware) แต่เขียนข้ามไปทำเอง (raw SQL ทั้งที่มี query builder, custom auth check ทั้งที่มี middleware) → เสียของที่ framework ทดสอบมาแล้ว, คนอื่นอ่านโค้ดคาดเดา flow ไม่ได้เพราะไม่ตรงกับที่ framework สอน
2. **หลุดกรอบ pattern** — โปรเจกต์มี convention เดิม (โฟลเดอร์, naming, MVC boundary, ชื่อ helper class) แต่เขียนไฟล์ใหม่ด้วยสไตล์ตัวเอง ไม่ดูของเดิมก่อน → โปรเจกต์มีสองสไตล์ปนกัน แก้ทีต้องรู้ว่าไฟล์นี้ "แบบไหน"

**ก่อนเขียนโค้ดใหม่ในโปรเจกต์ที่มี framework:**
1. เช็คว่า framework มี mechanism ให้ใช้ไหม (เปิด doc/comment ของ framework หรือดูโค้ดที่เคยเรียกใช้ฟีเจอร์เดียวกัน) — มี → ใช้ทางนั้น ไม่มี → ค่อยเขียนเอง
2. เปิดไฟล์ข้างๆ (ไฟล์เดียว controller/model เดียวกัน หรือไฟล์ประเภทเดียวกันในโฟลเดอร์) ดู pattern จริงก่อนเขียนไฟล์ใหม่
3. โปรเจกต์มี `AGENTS.md`/`CLAUDE.md` เฉพาะตัว → อ่านกฎ framework-specific ที่นั่นก่อนเสมอ กฎในหัวข้อนี้เป็น fallback ทั่วไปเท่านั้น
4. จำเป็นต้องข้าม mechanism จริงๆ (framework ทำไม่ได้ตามที่ต้องการ) → บอก user ตรงๆ ว่าข้ามเพราะอะไร ไม่ใช่ข้ามเงียบๆ

**ถ้าโปรเจกต์เดิมเขียนแบบไม่ปลอดภัยอยู่แล้ว (เช่น `$pdo->query("SELECT * FROM users WHERE id = $id")` ต่อ string ตรงจาก input) — ห้ามยึด "match pattern เดิม" มาเลียนแบบ** นี่ไม่ใช่ mechanism ของ framework ที่ต้องเคารพ แต่เป็นการใช้ mechanism ผิดวิธี (PDO เองมี `prepare()`/bind อยู่แล้ว mechanism ที่ถูกอยู่ในมือแล้ว แค่โค้ดเดิมไม่ได้ใช้) กฎ security (`measure-twice` Phase 2 — ห้ามต่อ SQL string) ชนะกฎ "match pattern เดิม" เสมอ:
- เขียนโค้ดใหม่ให้ใช้ prepared statement/bind ของ mechanism เดิม ไม่ใช่ raw string ต่อแบบเดิม
- ไม่ต้องไปแก้โค้ดเก่าที่ไม่เกี่ยวกับงาน (ยังอยู่ตาม AI Smell #11) แต่ต้องบอก user สั้นๆ ว่าเจอโค้ดเดิมที่มีช่องโหว่นี้ ไม่ใช่เงียบๆ ปล่อยผ่าน
- ถ้าไม่มี ORM/query builder ในโปรเจกต์เลย (raw PDO คือ mechanism เดียวที่มี) → ยังต้องใช้ PDO ให้ถูกวิธี (`prepare()` + `bindValue`/`execute([...])`) ไม่ใช่เขียนแบบเดิมที่ผิด

```
❌ $result = $pdo->query("SELECT * FROM users WHERE id = $id"); // framework มี Eloquent/query builder อยู่แล้ว
✅ User::where('id', $id)->first();

❌ $pdo->query("SELECT * FROM users WHERE id = $id");           // โปรเจกต์นี้ไม่มี ORM, ของเดิมก็เขียนแบบนี้ทั้งโปรเจกต์
✅ $stmt = $pdo->prepare('SELECT * FROM users WHERE id = ?');   // ใช้ mechanism เดิม (PDO) แต่ใช้ให้ถูกวิธี — ไม่ลอกช่องโหว่ตาม
   $stmt->execute([$id]);

❌ if ($_SESSION['role'] === 1) { ... }              // framework มี middleware/policy อยู่แล้ว
✅ $this->authorize('admin.access');                 // ใช้ auth mechanism ของ framework

❌ สร้าง app/Helpers/MyValidator.php เอง             // framework มี validation rule ให้แล้ว
✅ ใช้ validation rule ของ framework, extend เฉพาะ rule ที่ framework ไม่มีจริงๆ
```

**Constraints:**
- ห้ามเขียน raw query/manual auth/manual validation ถ้า framework มี mechanism ให้ใช้อยู่แล้ว
- ห้ามสร้างไฟล์ใหม่โดยไม่เปิดดู pattern ไฟล์ประเภทเดียวกันในโปรเจกต์ก่อน
- ข้าม mechanism ของ framework ได้เฉพาะกรณีพิสูจน์แล้วว่าทำไม่ได้จริง — ต้องบอก user เหตุผลเสมอ ห้ามข้ามเงียบๆ
- `AGENTS.md`/`CLAUDE.md` ของโปรเจกต์ (ถ้ามี) มีน้ำหนักเหนือกฎทั่วไปในหัวข้อนี้
- **ลำดับความสำคัญเมื่อชนกัน: security > match pattern เดิม** — pattern เดิมที่ไม่ปลอดภัย (raw SQL ต่อ string, manual auth check ที่มีช่องโหว่) ไม่ใช่ของที่ต้อง "เคารพ" ห้ามเลียนแบบ แม้ทั้งโปรเจกต์เขียนแบบนั้นหมด ต้องบอก user เมื่อเจอ

## Blast Radius — เช็คก่อนแก้ function/API ที่คนอื่นเรียกอยู่

**Objective:** แก้/เปลี่ยน signature หรือ behavior ของ function ที่มีคนเรียกอยู่แล้ว โดยไม่รู้ว่าใครเรียกบ้าง = ของพังแบบไม่มีใครเห็นจนกว่าจะรันจริง

1. **หา caller ทั้งหมดก่อนแก้** → verify: grep ชื่อ function/method/endpoint นี้ทั้งโปรเจกต์ (ไม่ใช่แค่ไฟล์ที่เปิดอยู่) — รวม view ที่เรียกผ่าน Blade/template, JS ที่ยิง endpoint นี้, cron/queue job
2. **แยกว่าเป็น internal หรือ public contract** → verify: private method ในคลาสเดียว (แก้ได้ตรงๆ) vs public method/API endpoint ที่ frontend อื่น หรือระบบอื่นเรียก (ต้องคิดเรื่อง breaking change)
3. **เปลี่ยน behavior ของ public contract** → verify: caller ทุกจุดที่ grep เจอยังทำงานถูกหลังแก้ ถ้าเปลี่ยน behavior จริง (ไม่ใช่แค่ internal refactor) → เพิ่ม parameter ใหม่แบบมี default เดิม/เพิ่ม method ใหม่แทนแก้ทับ ไม่ใช่เปลี่ยน behavior เดิมเงียบๆ
4. **แก้ column/table ที่หลายจุด query อยู่** → verify: grep หาทุก query ที่ใช้ column นี้ ก่อน DROP/RENAME (ดู `grounded-db` เรื่อง migration)

```
❌ เปลี่ยน getInvoiceTotal() ให้คืนค่ารวม VAT ทันที โดยไม่เช็คว่ามีที่ไหนเรียกแล้วเอาไปคำนวณต่อ
✅ grep หา getInvoiceTotal( ทั้งโปรเจกต์ก่อน → เจอ 3 จุดเรียกอยู่ → เพิ่ม getInvoiceTotalWithVat() แทน หรือแก้ 3 จุดพร้อมกันในรอบเดียว

❌ RENAME COLUMN status TO order_status ตรงๆ เพราะคิดว่าชื่อเดิมไม่ดี
✅ grep หา `status` ในบริบทตาราง orders ก่อน RENAME — เจอ query ตรงๆ ที่ไหนบ้าง แก้ให้ครบในรอบเดียว
```

**Constraints:**
- ห้ามแก้ signature/behavior ของ public function/API/column โดยไม่ grep หา caller ทั้งโปรเจกต์ก่อน
- เปลี่ยน behavior ของ contract ที่มีคนเรียกอยู่ (ไม่ใช่ private/internal) → เพิ่มของใหม่แทนแก้ทับ หรือแก้ caller ให้ครบทุกจุดในรอบเดียว ห้ามแก้ครึ่งเดียว
- ไม่แน่ใจว่า grep ครบไหม (เรียกผ่าน reflection, dynamic method name, string ที่สร้าง endpoint) → บอก user ตรงๆ ว่าเช็คได้ไม่ครบ 100%

## Idempotency — เรียกซ้ำต้องไม่พัง (double-submit, retry, webhook ซ้ำ)

**Objective:** action ที่มี side effect จริง (สร้าง order, ตัดเงิน, ส่งอีเมล) ถูกเรียกซ้ำได้เสมอในโลกจริง (double-click, network timeout แล้ว browser retry, webhook ผู้ให้บริการยิงซ้ำ) — ต้องออกแบบให้เรียกซ้ำแล้วผลลัพธ์เหมือนเรียกครั้งเดียว ไม่ใช่สร้างซ้ำ/ตัดเงินซ้ำ

1. **ระบุ action ที่มี side effect จริง** → verify: อะไรที่กดครั้งเดียวแต่ effect เกิดซ้ำได้ (form submit, payment webhook, retry job, API ที่เรียกจาก client ที่เน็ตหลุดได้)
2. **ป้องกันฝั่ง client (UX เท่านั้น ไม่ใช่ security)** → verify: disable submit button ทันทีที่กด + แสดง loading state — กันคนกดซ้ำมือ แต่**ห้ามหยุดแค่นี้** เพราะ network retry/สอง request พร้อมกันข้าม client ไม่ได้ผ่านปุ่มเดียว
3. **ป้องกันฝั่ง server ด้วย idempotency key/unique constraint** → verify: มี unique key ที่กันการสร้างซ้ำจริงที่ระดับ DB (ไม่ใช่แค่เช็คใน PHP แล้วมี race window) — เช่น unique constraint บน `(order_id)` ถ้า order สร้างจาก client เดียวครั้งเดียว, หรือ idempotency token ที่ client สร้างแล้วส่งมา เก็บลง DB ด้วย unique constraint ก่อน process
4. **Webhook จากผู้ให้บริการภายนอก (payment gateway, LINE, etc.)** → verify: มี event id/transaction id จากผู้ส่ง → เก็บ id ที่ประมวลผลแล้วไว้ (unique constraint) เช็คก่อน process ทุกครั้ง ถ้าเคยประมวลผล id นี้แล้ว → return success เฉยๆ ไม่ทำซ้ำ

```
❌ ปุ่ม "ชำระเงิน" กดได้เรื่อยๆ ไม่ disable, insert order ทุกครั้งที่ submit form
✅ disable ปุ่มทันทีที่กด (UX) + unique constraint บน orders(cart_session_id, created_date) หรือ idempotency_token ที่ client ส่งมาครั้งเดียว (server) — กดซ้ำ/double-click/retry ก็ insert ไม่ซ้ำ

❌ webhook handler: รับ payment.success แล้ว credit เงินเข้าบัญชีทันทีทุกครั้งที่ยิงมา
✅ เช็ค payment_events(gateway_event_id) unique constraint ก่อน — เคยเห็น event นี้แล้ว → return 200 เฉยๆ ไม่ credit ซ้ำ

❌ เช็คซ้ำด้วย SELECT ก่อน INSERT ในโค้ด PHP (if not exists → insert) — มี race window ระหว่าง SELECT กับ INSERT ถ้ามี 2 request พร้อมกัน
✅ ใช้ unique constraint ระดับ DB เป็นตัวกันจริง (catch duplicate-key exception แล้วถือว่าสำเร็จ) — DB เท่านั้นที่การันตี atomic ได้
```

**Constraints:**
- action ที่มี side effect จริง (สร้าง record, ตัดเงิน, ส่งอีเมล/SMS) ต้องมี unique constraint หรือ idempotency key ระดับ DB กันการสร้างซ้ำ — ห้ามกันด้วย "disable ปุ่มฝั่ง client" อย่างเดียว
- ห้ามใช้ "SELECT ก่อน แล้วค่อย INSERT" เป็นตัวกัน duplicate เพียงอย่างเดียว (มี race window) — ต้องมี unique constraint/lock ระดับ DB รองรับเสมอ
- Webhook/callback จากภายนอกต้องเก็บ event id ที่ประมวลผลแล้ว เช็คซ้ำก่อน process ทุกครั้ง — สมมติว่าผู้ส่งจะยิงซ้ำได้เสมอ
- ไม่ชัดว่า action ไหนต้องกัน idempotency บ้าง → ถามหรือระบุให้ user เห็นว่าจุดไหนกันแล้ว จุดไหนยังไม่กัน ไม่ใช่เดาว่าไม่จำเป็น

## Observability — log ที่ช่วย debug ได้จริงตอนพังจริง

**Objective:** error message ที่ actionable (มีอยู่แล้วในตาราง AI Smell #17) พอสำหรับตอน dev เจอ error ตรงหน้า แต่ตอนพังบน production ต้องมี log ที่สืบไปหา root cause ได้โดยไม่ต้องเดา

1. **เลือก log level ให้ตรงความจริง** → verify: error = ของพังต้องดูจริง, warning = ผิดปกติแต่ยังทำงานต่อได้ (fallback ถูกใช้), info = event สำคัญของ business (order created, payment success) — ไม่ log ทุกอย่างเป็น error จนหา signal จริงไม่เจอ
2. **log พร้อม context ที่สืบได้** → verify: มี identifier ที่ตามรอยได้ (user id, order id, request id) ไม่ใช่แค่ข้อความลอยๆ อย่าง "Error occurred"
3. **ไม่ log ข้อมูลอ่อนไหว** → verify: password, token, เลขบัตร, ข้อมูลลูกค้าเต็มรูป ห้ามอยู่ใน log — ตรงกับกฎ "ห้ามเขียน" ของ `handoff` และ security checklist ใน `measure-twice`
4. **log เฉพาะจุดที่ช่วย debug จริง** → verify: ไม่ log ทุก function เข้า-ออก (log spam หา signal จริงไม่เจอ) — log ที่ decision point, error path, external call (API/payment) ที่ตอบช้า/ล้มเหลวได้

```
❌ Log::error('Something went wrong');                          // ไม่รู้ว่าอะไรพัง ที่ไหน ของใคร
✅ Log::error('Payment capture failed', ['order_id' => $order->id, 'gateway_ref' => $ref, 'reason' => $e->getMessage()]);

❌ Log::info('User: '.$user->email.' password attempt: '.$password); // log ข้อมูลอ่อนไหว
✅ Log::warning('Login failed', ['user_id' => $user->id, 'attempt_count' => $attempts]); // ไม่มี password/token

❌ log ทุก method เข้า-ออกทั้งโปรเจกต์เป็น info                  // log spam หา signal จริงไม่เจอ
✅ log เฉพาะ error path, external call, business event สำคัญ
```

**Constraints:**
- ห้าม log password/token/เลขบัตร/ข้อมูลอ่อนไหวเด็ดขาด (ตรงกับ security checklist)
- ห้าม log message ที่ไม่มี context ตามรอยได้ (id ที่เกี่ยวข้อง) เมื่อเป็น error/warning
- ห้ามใช้ level `error` กับทุกอย่าง — แยก error/warning/info ตามความจริงของสถานการณ์
- ห้าม log ทุก function call เป็น info จนกลายเป็น spam — log เฉพาะจุดที่ช่วย debug จริง

## Examples

```
❌ function process($data) {
       $result = [];
       foreach ($data as $item) {
           if ($item['status'] === 1) {           // magic number
               $result[] = $item;
           }
       }
       return $result;                            // "process" ไม่บอกอะไร
   }

✅ function getActiveInvoices(array $invoices): array {
       return array_filter($invoices, fn($i) => $i->isActive());
   }

❌ interface PaymentGateway {}                    // เผื่อมี provider อื่น (ไม่มี)
   class StripePayment implements PaymentGateway {}
   // user ขอแค่ "ตัดเงินจากบัญชี"

✅ if ($account->balance >= $amount) {
       $account->debit($amount);
   }

❌ // update user profile                        // comment obvious
   $user->name = $request['name'];
   $user->save();

✅ $user->updateProfile($request['name']);

❌ function getUser($id) {                        // ชื่อบอก "get" แต่ข้างในเขียน DB
       $user = User::find($id);
       $user->update(['last_seen' => now()]);      // hidden side effect
       return $user;
   }

✅ function getUser($id): ?User {                  // read-only จริงตามชื่อ, type hint ชัด
       return User::find($id);
   }
   function touchLastSeen($id): void { ... }        // effect แยกฟังก์ชัน ชื่อบอกตรง

❌ throw new Exception('Invalid input');           // ไม่รู้ว่าอะไรผิด แก้ไม่ได้ถ้าไม่เปิด debugger

✅ throw new Exception("qty ต้องมากกว่า 0, ได้รับ {$qty}");
```

## Constraints

- ห้ามถามปลายเปิด — ถามเป็นช้อย 3-5 ตัวเลือก (ข้อ 17, 18)
- ห้ามทำเกินขอ — feature/validation/logging ที่ไม่สั่ง = fail (ข้อ 3)
- ห้าม abstraction ที่ยังไม่ต้องใช้ — YAGNI + KISS (ข้อ 3)
- ห้าม what-comment (เล่าโค้ด) — อนุญาต why-comment ที่บอก ทำไม/ขั้นตอน/ที่มา สั้นๆ (ข้อ 6)
- ห้าม hardcode — secrets → env var/config เท่านั้น, magic numbers → named constant (ข้อ 7)
- ห้าม function > 30 บรรทัด, if ซ้อนลึก, เงื่อนไขซับซ้อน inline (ข้อ 9, 10, 11)
- ห้าม generate front-end script จาก backend template language (PHP/JSP/ERB ฯลฯ) / ซ่อน logic ไว้ใน wrapper class (ข้อ 4, 5)
- โปรเจค PHP: เริ่มจาก HTML ล้วนก่อนเสมอ (baseline ต้องกดใช้งานได้แม้ไม่มี JS) เสริม AJAX เฉพาะจุดที่ HTML ทำไม่ได้จริง — Progressive Enhancement (ข้อ 24)
- ห้าม temp script ใน source tree (ข้อ 20)
- ห้าม "improve" โค้ดข้างๆ ที่ไม่เกี่ยวกับงาน (ข้อ 12)
- ห้าม emoji ใน UI, ห้ามภาษา จีน/ญี่ปุ่น/เกาหลี (ข้อ 14, 15)
- ห้ามลบ dead code เดิมที่ไม่ได้ขอ (ข้อ 12)
- Commit format `what: description`, push ต่อเมื่อ test ผ่าน, ห้าม refactor ปนกับ commit fix (ข้อ 21)
- Refactor โค้ดเดิม (ไม่ใช่ fix/feature) ต้องมี safety net ก่อนแตะ + ทีละก้าวเล็ก (ข้อ 22)
- ห้ามเขียนข้าม mechanism ของ framework ที่มีให้อยู่แล้ว และห้ามเขียนไฟล์ใหม่โดยไม่ดู pattern เดิมก่อน (ดู "Framework Boundary")
- ห้าม return type ไม่คงที่โดยไม่มี type hint, ห้ามฟังก์ชันที่ชื่อไม่ตรงกับ side effect จริง, ห้าม error message ที่ไม่บอกค่า/เงื่อนไขที่ผิด (ข้อ 15-17)

โปรเจคที่มี framework/convention เฉพาะตัว (เช่น modal component, MVC helper class เฉพาะ, container name) — ดูกฎเพิ่มเติมใน `AGENTS.md` ของโปรเจคนั้น กฎในนี้เป็นหลักการทั่วไปข้ามโปรเจค

## Core Principle

**Simplest thing that could possibly work. No comments. No hardcode. No overengineering.**
**เขียนเหมือนคนที่อยากให้คนอื่นอ่านต่อ — ไม่งั้นก็ไม่ต่างจาก AI**
**ใช้ง่าย แก้ง่าย ไม่หลุดกรอบ framework — โค้ดที่ดีคือโค้ดที่คนอื่น (หรือตัวเองอีก 3 เดือนข้างหน้า) เรียกใช้ถูกโดยไม่ต้องเดา และแก้บั๊กได้โดยไม่ต้องไล่อ่านทั้งโปรเจกต์**

## Effectiveness Indicators — รู้ได้ยังไงว่า human coding ได้ผลจริง

ไม่ใช่แค่ทำตาม checklist แล้วจบ — เช็คผลจริงว่าดีขึ้นไหม:

| สัญญาณ | ดี (✅) | แย่ (❌) |
|--------|---------|---------|
| ขนาด diff | เล็ก ตรงเป้า เท่าที่ task ขอ | บวมเพราะ "improve" ของข้างเคียง |
| Rewrite | แทบไม่มี — คิดครบตั้งแต่รอบแรก | เขียนใหม่ซ้ำเพราะ overengineer แล้วต้องถอด |
| จังหวะถาม | ถามช้อยก่อนเริ่ม เมื่อไม่แน่ใจ | เดาแล้วเขียน พลาดแล้วค่อยถาม |
| Bug ซ้ำ root cause เดิม | ลดลง — เพราะมี post-mortem (rule 19.8) | เจอ pattern เดิมพังคนละจุดซ้ำเรื่อยๆ |
| เวลาแก้บั๊ก 1 จุด | ไล่ไฟล์เดียว/ฟังก์ชันเดียวจบ | ต้องไล่ข้ามหลายไฟล์เพราะ logic กระจาย/ข้าม mechanism ของ framework |
| เรียกใช้ function/API ที่เพิ่งเขียน | เดา parameter ถูกจากชื่อ ไม่ต้องเปิด implementation | ต้องเปิดไฟล์อ่าน source ก่อนถึงเรียกถูก |

รายละเอียดกฎแต่ละข้อที่ Constraints อ้างถึง (เช่น MVC boundary, hardcode, comment, naming, bug fix protocol, git commit/branch, refactor โค้ดเดิม, temp script, การเขียน skill ให้ AI) อยู่ใน [references/rules.md](references/rules.md) — เปิดอ่านเมื่อ Constraints/AI Smell table ไม่พอ

---

# 2. Extensible Architecture (Bend Not Break)

## Objective

ลด hardcode และทำให้โค้ด extensible — ยืดได้โดยไม่หัก: รองรับการเปลี่ยนค่า, เปิด/ปิด feature, เพิ่ม module/role โดยไม่แก้โค้ดหลัก หมายเหตุ: หัวข้อนี้เกี่ยวกับ design patterns (ไม่ใช่การอ่าน/เขียน config file ธรรมดา, ไม่เกี่ยวกับ settings.json ของ Claude Code เอง)

## Steps/Workflow

1. **ระบุค่าที่ hardcode อยู่** → verify: รายการ values/features/modules ที่ต้องยืดหยุ่น
2. **เลือก pattern ที่ง่ายสุดที่พอ** → verify: ไล่ตามตาราง 7 patterns — Config-Driven → Feature Flags → Strategy → Pipeline → Addon → Event-Driven → RBAC
3. **แยก Core vs Addon** → verify: Admin tools/export/notification/OCR เป็น addon, Auth/Router/DB/Security เป็น core
4. **Deploy-first, toggle-later** → verify: เปลี่ยนค่า/ปิด feature ได้โดยไม่ redeploy
5. **โปรเจค solodev ที่ต้องการ installer/dynamic role/feature flag** → verify: เช็คว่าต้องการอันไหนจริง (ไม่ใช่ทำครบ 3) แล้วดู section "Solodev Concrete Recipes" ด้านล่าง

## Examples

```
❌ if ($env === 'production') { ... }      → hardcode ค่าในโค้ด
✅ $config->get('app.env')                → Config-Driven

❌ ฝัง feature ใหม่ในโค้ดหลัก เปิดปิดไม่ได้
✅ Feature flag ใน admin UI → toggle โดยไม่ deploy

❌ สร้าง AddonSystem ตั้งแต่ยังมี 1 module
✅ รอจนมี 2+ module ที่ต้อง drop-in ค่อยสร้าง
```

## Constraints

- ห้ามเลือก pattern ใหญ่กว่าที่จำเป็น — pick simplest first
- ห้าม refactor เป็น addon ในเมื่อยังไม่ต้อง (YAGNI)
- ห้ามแก้ production code ตรงๆ — แก้ที่ source แล้ว deploy
- ห้าม hardcode flag ใหม่โดยไม่ผ่าน config layer
- ห้ามทำครบ installer+role+flag ถ้า solodev project ไม่ต้องการ — ถามก่อนว่าต้องการอันไหน

## 7 Patterns (pick simplest first)

| Pattern | When |
|---------|------|
| **Config-Driven** | Business logic values that change → DB/JSON config |
| **Feature Flags** | New feature → toggle on/off without deploy |
| **Strategy Pattern** | Multiple variants of same logic → swap at runtime |
| **Pipeline** | Multi-step process → chain of stages |
| **Addon System** | Business logic module → drop-in folder + JSON manifest |
| **Event-Driven** | Decoupled side effects → events + listeners |
| **Entitlement/RBAC** | Per-role/per-entity permissions → policy class |

## Core vs Addon

| Core (keep) | Addon (extract) |
|-------------|-----------------|
| Auth, Router, DB, Security | Admin tools, export formats, notification channels, OCR providers |

## Principle

- Deploy first → toggle later (zero-downtime)
- Feature toggle in admin UI (no dev needed)
- Ask: "Should this be an addon?" before building

---

## Solodev Concrete Recipes

โปรเจคที่ดูแลคนเดียว ไม่มีทีม ops คอยรัน migration/sql มือให้ — 4 pattern คอนกรีตที่ implement ได้ทันที เลือกทำเฉพาะที่ต้องการจริง ไม่ใช่ทำครบทุกครั้ง:

| Pattern | ใช้เมื่อ |
|---|---|
| A: Installer | โปรเจคใหม่ต้องมีหน้าติดตั้งอัตโนมัติ ไม่อยาก SSH รัน seed มือ |
| B: Dynamic Role | role/permission ต้องสร้าง-แก้ได้จาก UI ไม่ผูกตายในโค้ด |
| C: Feature Flag | ต้อง toggle feature ได้โดยไม่ redeploy |
| D: Per-User Permission | role ให้ default พอส่วนใหญ่ แต่บางคนต้องมีสิทธิ์เพิ่มเฉพาะตัว |

**รายละเอียดเต็ม (schema, โค้ดตัวอย่าง, บั๊กที่เจอจริง, constraints) อยู่ใน [references/solodev-recipes.md](references/solodev-recipes.md) — เปิดอ่านก่อนเริ่ม implement pattern ไหนก็ตาม ห้ามเดาจากแค่ตารางบนนี้**

---

# 3. CSS-First

## หลักการ

งาน UI/UX ที่เป็นเรื่อง "ภาพ / การเคลื่อนไหว / ขนาด" ให้ default ไปที่ CSS ก่อนเสมอ — เขียน JS เฉพาะตอนที่ CSS ทำไม่ได้จริงๆ (ต้องรู้พิกัด runtime ที่คำนวณล่วงหน้าไม่ได้)

เหตุผล: CSS transition/animation รันบน compositor thread (GPU) ไม่บล็อก JS main thread, โค้ดน้อยกว่า, บั๊กน้อยกว่า (ไม่มี state ให้ desync กับ DOM จริง), responsive ผ่าน media query/`clamp()` ปรับตามจออัตโนมัติไม่ต้องมี resize listener

## ตาราง: เลือกอะไรเมื่อไหร่

| งาน | ใช้ CSS ล้วน | ใช้ JS (มีเหตุผลจำเป็น) |
|---|---|---|
| Animation/transition (hover, fade, slide, spin) | ✅ เสมอ — `transition` / `@keyframes` | ❌ ไม่ต้องใช้ requestAnimationFrame loop |
| Responsive size/breakpoint | ✅ เสมอ — media query, `clamp()`, container query | ❌ ไม่ต้องมี `window.addEventListener('resize', ...)` |
| Dropdown/tooltip ที่อยู่ใน parent ปกติ (ไม่มี overflow ตัด) | ✅ `position: absolute` เกาะ parent `relative` | — |
| Dropdown ที่ต้อง escape จาก container ที่มี `overflow-x/y-auto` ตัด (เช่น cell ในตารางที่ scroll ได้) | ⚠️ ได้ถ้า target browser รองรับ CSS Anchor Positioning (`anchor-name` / `position-anchor` / `position-visibility: no-overflow`) — เช็ค browser support ก่อนเสมอ | ✅ ถ้าไม่รองรับ ต้อง JS: คำนวณ `getBoundingClientRect()` runtime + reposition/close ตอน scroll |
| Sticky footer / fill-remaining-height layout | ✅ เสมอ — flexbox (`flex: 1 0 auto`) ไม่ต้อง JS วัดความสูง | — |
| Modal/dialog เปิด-ปิด | ✅ CSS `transition` + toggle class พอ | ใช้ JS แค่ตอน toggle class / focus trap |

## Checklist ก่อนเขียน JS สำหรับงาน UI

1. ถามตัวเองก่อน: "อันนี้ CSS ทำได้ไหม" — เช็คตารางด้านบนก่อนเปิด editor เขียน JS
2. ไม่แน่ใจว่า CSS ทำได้ไหม → ลองก่อน (media query / `clamp()` / flexbox / `position: absolute`) ก่อนถอยไป JS
3. ต้องใช้ JS จริง (รู้พิกัด runtime, escape overflow) → เช็ค CSS Anchor Positioning ก่อนว่ารองรับ target browser ไหม (caniuse.com/css-anchor-positioning) — โปรเจค internal tool ที่ fix browser (เช่น Chrome-only) มักคุ้มสวิตช์ไปใช้
4. เขียน JS ที่จำเป็นจริงให้เบาที่สุด — คำนวณตำแหน่งครั้งเดียวตอน event ไม่ loop ทุก frame, ใช้ scroll listener แบบ capture (`{capture: true}`) ปิด/reposition ไม่ใช่ poll

## Anti-pattern ที่เจอมาแล้ว (บทเรียนจริง)

| ❌ อย่าทำ | ✅ ทำแบบนี้แทน |
|---|---|
| เขียน JS resize listener คำนวณ layout เอง | ใช้ flexbox/grid + media query ให้ browser คำนวณให้ |
| Sticky footer ด้วย JS วัดความสูง content แล้ว set padding | Flexbox: parent `display: flex; flex-direction: column`, content `flex: 1 0 auto`, footer ปกติ |
| Popup อยู่ใน `.card` ที่มี `backdrop-filter` แล้วใช้ `position: fixed` ตรงๆ ในโครง DOM เดิม | ต้อง append เข้า `document.body` เพราะ `backdrop-filter` / `transform` / `filter` บน ancestor เปลี่ยน containing block ของ `position: fixed` (ไม่ใช่ viewport แล้ว) — เป็น CSS spec behavior ต้องรู้ก่อนเขียน JS วางตำแหน่ง |
| ปล่อย popup แบบ `position: fixed` ไว้เฉยๆ ตอน scroll | ต้องมี scroll listener (`capture: true`) ปิดหรือ reposition เอง เพราะ fixed ไม่ตาม container ย่อยที่ scroll |

## Constraints

- ก่อนเขียน JS สำหรับงาน visual ใดๆ ต้องเช็ค checklist ด้านบนก่อนเสมอ
- เลี่ยง JS ไม่ได้ (ต้องรู้พิกัด runtime) → บอก user ว่าทำไมเลี่ยงไม่ได้ ไม่ใช่เขียนเงียบๆ แล้วไม่อธิบาย
- โปรเจคที่ไม่มี build step (plain CSS handwritten) — ห้ามเสนอ preprocessor/framework ใหม่ (Sass, Tailwind) โดยไม่ถาม เขียน plain CSS ตรงไฟล์เดิม
- Animation ต้อง prefer `transform` / `opacity` (GPU-accelerated) มากกว่า `width` / `height` / `top` / `left` (trigger layout reflow)

---

# 4. Design From Reference

ใช้เมื่อ user ให้ลิงค์เว็บมาพร้อมคำสั่งสไตล์ เช่น "เอาแบบ https://... แต่ทำเป็น minimal premium" (ไม่ใช้เมื่อแก้ CSS หน้าเดิมเล็กน้อย หรือไม่มีลิงค์ ref)

Flow บังคับ 4 เฟส ห้ามข้าม: **0. อ่าน ref → 1. ถามให้ครบ → 2. เขียน DESIGN.md แล้วหยุดรอ approve → 3. build** — ห้ามเขียนโค้ดหน้าเว็บก่อนผ่านเฟส 2 ห้ามเดา requirement แทนการถามในเฟส 1

**รายละเอียดเต็มทั้ง 4 เฟส (ขั้นตอนอ่าน ref ด้วย Chrome + JS scraper script, คำถามทั้ง 2 รอบ, โครง DESIGN.md, checklist ตอน build, ตารางแปลคำสไตล์, constraints เรื่อง license/asset) อยู่ใน [references/design-from-reference.md](references/design-from-reference.md) — งานนี้ต้องเปิดอ่านไฟล์นั้นทั้งไฟล์ก่อนเริ่มเฟส 0 เสมอ ห้ามเดาขั้นตอนจากแค่บทสรุปนี้**

---

# 5. Grounded DB

## Objective

ออกแบบ schema, เขียน SQL, ทำ migration/data import ให้ถูกหลัก production — ใช้เมื่อสร้าง/แก้ตาราง, เขียน query, import xlsx/csv

## Steps/Workflow

0. **ก่อนสร้างตารางใหม่ (หรือคอลัมน์ใหม่แบบเผื่อไว้)** → verify: มีใครจะ**เขียน**มันจริงจาก UI/flow ที่มีอยู่จริงไหม ไม่ใช่แค่ "เผื่ออนาคต" — ดูหัวข้อ Code-first vs DB-first ด้านล่าง
1. **ออกแบบ schema** → verify: status เป็น int, ไม่มี duplicate data, FK ชัดเจน, มี created_at/updated_at ทุกตาราง
2. **กำหนด type/size ตามการใช้งานจริง** → verify: column ยาวเท่าที่ใช้จริง ไม่เผื่อเกิน — username VARCHAR(30) ถ้าไม่มีใครใช้เกิน 30 (ห้าม VARCHAR(255) มั่ว), DECIMAL(12,2) ไม่ใช่ FLOAT, ENUM หลีกเลี่ยง (ใช้ TINYINT + constant)
3. **เขียน migration** → verify: soft delete ก่อน hard delete, critical write ใช้ transaction + FOR UPDATE (กันแก้ row เดิมชนกัน — ถ้าต้องกัน "สร้าง record ซ้ำ" เช่น double-submit/webhook ซ้ำ ดูหัวข้อ Idempotency ในหัวข้อ 1 ด้วย คนละปัญหากับ FOR UPDATE)
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

---

# 6. Senior UX/UI Principles

## Objective

ออกแบบ/ตรวจ UI ให้ผ่านมาตรฐานที่คนใช้คาดหวังจาก dev มีประสบการณ์ — ไม่ใช่แค่ "กดได้" แต่ "กดแล้วรู้ว่าเกิดอะไรขึ้น ไม่งง ไม่กลัวพัง" อ้างอิง Nielsen's 10 Usability Heuristics + WCAG พื้นฐาน แปลงเป็น checklist ที่ทำได้จริงในโปรเจค solodev (ไม่ต้องมี UX designer แยก)

## 5 States — ทุกหน้า/component ที่โหลดข้อมูลหรือรับ action ต้องคิดครบ

คิดตอนออกแบบ ไม่ใช่ค่อยเติมทีหลังตอนมีคนถามว่า "ทำไมหน้าขาวๆ":

| State | ต้องมีอะไร |
|---|---|
| default | หน้าปกติที่มีข้อมูล |
| loading | บอกว่า "กำลังทำอะไร" (ไม่ใช่ spinner ลอยๆ ไม่มีข้อความ) และ**ต้อง disable action ซ้ำทันที** — เชื่อมตรงกับหัวข้อ Idempotency ในหัวข้อ 1 (double-submit เกิดตรงจังหวะนี้พอดี) |
| empty | ไม่ใช่กล่องขาวเปล่า — บอกว่าทำไมไม่มีข้อมูล + ทำอะไรต่อได้ ("ยังไม่มีรายการ — เพิ่มรายการแรก") |
| error | บอกว่าเกิดอะไรขึ้นและทำอะไรต่อได้ ไม่ใช่ raw error จาก API/stack trace (ตรงกับ AI Smell #17 actionable error — ที่นี่คือฝั่ง UI ต้องแสดงใกล้จุดที่ผิดจริง ไม่ใช่ alert ลอยบนสุดของหน้า) |
| success | ยืนยันชัดว่า action สำเร็จจริง ไม่ใช่หวังให้ user เข้าใจเองจาก URL เปลี่ยนหรือหน้านิ่งไปเฉยๆ |

`measure-twice` ใช้ตรวจว่า 5 state นี้มีจริงตอน test — ที่นี่คือขั้นตอน**สร้าง**ให้มันมีตั้งแต่แรก

## Nielsen Heuristics → กฎที่ทำได้จริง

| Heuristic | กฎที่ใช้จริงในโปรเจค |
|---|---|
| System status | action ที่ใช้เวลา (network, upload) ต้องมี feedback ทันที (disable + loading text) — ห้ามปล่อยหน้าเงียบจน user กดซ้ำ |
| Error prevention | จำกัดตัวเลือกด้วย dropdown/date picker แทน free text ที่ผิดง่าย, disable ปุ่ม submit จนกว่า required field ครบ |
| Error recovery | error message บอก "เกิดอะไร" + "ทำอะไรต่อได้" อยู่ใกล้จุดที่ผิด (inline ใต้ field) ไม่ใช่ alert บนสุดของหน้า |
| Consistency | ปุ่ม/สี/spacing เดียวกันทำหน้าที่เดียวกันทั้งระบบ — สีที่ใช้แทน destructive action ต้องหมายถึงอย่างนั้นทุกที่ ไม่ใช่บางหน้าเป็น primary action |
| Recognition over recall | ไม่ให้ user จำ id/code จากหน้าก่อน — แสดงชื่อ/label ที่เข้าใจได้ตรงหน้าปัจจุบัน |
| Minimalist design | ซ่อนตัวเลือกที่ไม่ใช่ทางหลักไว้หลัง "ตัวเลือกเพิ่มเติม" ไม่ยัดทุกอย่างขึ้นจอเดียว |
| User control | modal/flow ที่แก้ไขข้อมูลต้องมีทางออก (cancel/back) ที่ไม่ทำลายข้อมูลที่ยังไม่ submit โดยไม่เตือนก่อน |

## Accessibility พื้นฐานที่ทำได้จริง (ไม่ต้องเป็น a11y expert)

- contrast ตัวอักษร/พื้นหลังผ่าน WCAG AA (ปกติ 4.5:1, ตัวใหญ่ 3:1) — เช็คด้วย contrast checker ก่อน lock สีจริงใน DESIGN.md (เฟส 2 ของ Design-from-Reference)
- ทุก `<img>` มี `alt` ที่บอกเนื้อหาจริง (ไม่ใช่ชื่อไฟล์), icon-only button มี `aria-label`
- form field ทุกอันมี `<label>` จริง — placeholder ไม่ใช่ label (หายตอนพิมพ์ user ลืมว่ากรอกอะไรอยู่)
- error/success/warning ห้ามสื่อด้วยสีอย่างเดียว (คนตาบอดสีมองไม่เห็นต่าง) ต้องมี icon/ข้อความคู่กันเสมอ
- ปุ่ม/link กด Tab ไล่ลำดับได้จริง ไม่ต้องพึ่งเมาส์อย่างเดียว

## Examples

```
❌ กด submit แล้วหน้านิ่งเงียบ 2 วิ ไม่มีอะไรบอก           → user กดซ้ำเพราะคิดว่าไม่ติด
✅ กด submit → ปุ่ม disable + "กำลังบันทึก..." ทันที         → รู้ว่าระบบรับคำสั่งแล้ว กันกดซ้ำในตัว

❌ list ว่าง → แสดงตารางเปล่าไม่มีข้อความ
✅ list ว่าง → "ยังไม่มีรายการลูกหนี้ — เพิ่มรายการแรก" + ปุ่มเพิ่ม

❌ error → alert("Error: Undefined index id")               → raw, ไม่รู้ทำอะไรต่อ, ดูไม่เป็นมือโปร
✅ error → inline ใต้ field ที่ผิด: "กรุณาเลือกลูกหนี้ก่อนบันทึก"

❌ ปุ่มลบใช้สีแดง แต่บางหน้าปุ่ม "ยืนยัน" ก็สีแดงเหมือนกัน    → user กดผิดเพราะสีบอกความหมายไม่ตรงกันทั้งระบบ
✅ สีแดง = destructive action เท่านั้น ทั้งระบบ
```

## Constraints

- ทุกหน้าที่โหลดข้อมูล async ต้องออกแบบ 4 state ที่ไม่ใช่ default (loading/empty/error/success) ตั้งแต่ตอนเขียน ไม่ใช่ปล่อยว่างไว้แล้วมาเติมทีหลัง
- ห้าม error message ที่เป็น raw exception/stack trace โผล่ที่ UI (ตรงกับ security checklist เรื่อง error leak ใน `measure-twice` ด้วย)
- ห้ามสื่อสถานะ (error/success/warning) ด้วยสีอย่างเดียวโดยไม่มี text/icon ประกอบ
- ปุ่ม action ที่มี loading state ต้อง disable ทันทีที่กด — ไม่ใช่แค่ UX เฉยๆ แต่กัน double-submit จริงด้วย (เชื่อมกับหัวข้อ Idempotency)
- ก่อน lock สีจริงใน DESIGN.md ต้องเช็ค contrast ผ่าน WCAG AA — ไม่ใช่เอาสีจาก ref มาใช้ตรงๆ โดยไม่เช็ค
