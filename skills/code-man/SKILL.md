---
name: code-man
description: สไตล์การเขียนโค้ดครบสาย เน้นใช้ง่าย แก้ง่าย ไม่หลุดกรอบ framework — Human Coding (ไม่โผล่กลิ่น AI + Framework Boundary), Extensible Architecture (config-driven/feature flag/RBAC + สูตร solodev), CSS-First (CSS ก่อน JS เสมอ), Design-from-Reference (สร้างเว็บจากลิงค์ ref จริง), Grounded DB (schema/SQL/migration) รวม code + bend-not-break + css-first + design-from-ref + grounded-db เป็นก้อนเดียว ใช้เมื่อเขียน/แก้/ตรวจโค้ดทุกชนิด, refactor ให้ยืดหยุ่น, ทำงาน UI ที่เกี่ยวกับ visual behavior, สร้างเว็บจากลิงค์ ref, หรือสร้าง/แก้ตาราง DB
---

# Code-Man — Full Coding Style

รวม 5 เรื่องของสไตล์การเขียนโค้ดที่ใช้ต่อกันได้ตลอดสาย:

1. **Human Coding** — หลักทั่วไปทุกครั้งที่เขียน/แก้โค้ด
2. **Extensible Architecture** — ตอนออกแบบให้ยืดหยุ่น ไม่ hardcode
3. **CSS-First** — ตอนทำงาน UI/visual
4. **Design-from-Reference** — ตอนสร้างเว็บจากลิงค์ ref จริง (เรียกใช้ #1 และ #3 ตอน build)
5. **Grounded DB** — ตอนแตะ schema/SQL/migration

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
| 15 | Return type ไม่คงที่ — ฟังก์ชันเดียวกันคืน array บางครั้ง คืน null บางครั้ง โดยไม่มี type hint บอก | type hint ชัด (`: ?array`, `: array`) — ถ้าไม่เจอคืน `[]`/`throw` ไม่ใช่ปนกันไปมา |
| 16 | Hidden side effect — ชื่อบอกว่า "get/read" แต่ข้างในไป write DB/ไฟล์/session ด้วย | ชื่อต้องสะท้อนของจริง (`getUser()` ต้อง read-only, มี effect → ตั้งชื่อ `getUserAndTouchLastSeen()` หรือแยกเป็น 2 ฟังก์ชัน) |
| 17 | Error/exception message บอกแค่ "invalid input"/"error occurred" ไม่บอกอะไรต่อ | บอกค่าที่ผิดจริงและเงื่อนไขที่ควรจะเป็น — `"qty ต้อง > 0 ได้รับ -5"` ไม่ใช่ `"invalid qty"` |

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

```
❌ $result = $pdo->query("SELECT * FROM users WHERE id = $id"); // framework มี Eloquent/query builder อยู่แล้ว
✅ User::where('id', $id)->first();

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

### Pattern A: Installer

```
core/Installer.php
├── isInstalled(): bool
│     — เช็ค SELECT COUNT(*) FROM users WHERE role >= SUPER_ADMIN
│     — DB connect fail / ตาราง users ไม่มี → return false (ไม่ใช่ throw)
│     — cache ผลลัพธ์ true ลง marker file (เช่น storage/installed.lock) เพื่อไม่ query ทุก request
├── preflightChecks(): เช็ค PHP version, extension (pdo_mysql, curl), โฟลเดอร์ writable
├── withInstallLock(callable $fn)
│     — flock() กันติดตั้งซ้อนกันสองคนพร้อมกัน
└── install($env, $adminAccount)
      — สร้าง DB (ถ้ายังไม่มี), เขียน .env, รัน schema.sql, seed superadmin คนแรกจากฟอร์ม, เขียน lock marker

InstallController — redirect /login อัตโนมัติถ้า isInstalled() แล้ว, ไม่งั้น render หน้าติดตั้ง
```

### Pattern B: Dynamic Role

```sql
CREATE TABLE roles (
    id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50) NOT NULL UNIQUE,      -- 'superadmin', 'collector', หรือ role ใหม่ที่ user สร้าง
    label VARCHAR(100) NOT NULL,           -- ป้ายชื่อแสดงผล
    is_system TINYINT(1) NOT NULL DEFAULT 0 -- 1 = built-in ห้ามลบ (superadmin/admin)
);

CREATE TABLE role_permissions (
    role_id INT UNSIGNED NOT NULL,
    permission_id INT UNSIGNED NOT NULL,
    PRIMARY KEY (role_id, permission_id)
);
```

- Permission check ในโค้ดอ่านจาก DB (`PermissionService::forRole($roleId)`) — **ไม่มี** role ID ผูกตายในโค้ดเลย นอกจาก `is_system` role ที่ต้องกันตัวเองไว้ (เช่น superadmin ต้องมี permission จัดการ role เสมอ กันสร้าง role ใหม่แล้วแก้จนตัวเองล็อกตัวเองไม่ได้)
- UI: หน้า "จัดการ role" ต้องมี create/rename/delete role ใหม่ได้ (delete บล็อกถ้า `is_system=1` หรือยังมี user ผูกอยู่) — ไม่ใช่แค่หน้า toggle permission ของ role เดิม 5 ตัว

### Pattern C: Feature Flag

```php
class Feature
{
    private const DEFAULTS = ['auto_seed' => false, 'export' => true, ...];
    private static ?array $cache = null;

    public static function enabled(string $key): bool
    {
        self::$cache ??= array_merge(self::DEFAULTS, SystemConfig::get('SYSTEM_FEATURES', []));
        return (bool) (self::$cache[$key] ?? self::DEFAULTS[$key] ?? false);
    }
}
```

- `system_configs` เก็บ override เป็น JSON, ไม่มี override → ใช้ `DEFAULTS` ในโค้ด (โค้ด deploy ครั้งเดียว ค่า toggle เปลี่ยนได้ทีหลังไม่ต้อง deploy)
- Admin UI มีปุ่ม toggle ตรงๆ — ไม่ต้องแก้ `.env`/DB มือ

### Pattern D: Per-User Permission Flag (override บน role)

ใช้เมื่อ role ให้สิทธิ์เป็นค่าเริ่มต้นพอสำหรับคนส่วนใหญ่ในตำแหน่งนั้น แต่มีบางคนต้องการสิทธิ์เพิ่มเฉพาะตัว (คนที่ทำงานแทนกันได้บางส่วน) — **ไม่ใช่** เหตุผลให้ยุบ role ทิ้งแล้วตั้ง flag รายคนล้วนๆ (เสียประโยชน์ของ role = ค่าเริ่มต้นที่ setup ได้เร็ว), ก็ไม่ใช่เหตุผลให้สร้าง role ใหม่สำหรับคนเดียว (role ระเบิดจำนวนเปล่าๆ) — คำตอบคือ role (RBAC) + per-user extra (ACL) ผสมกัน เหมือน AWS IAM (policy บน role + inline policy เฉพาะ user)

```sql
CREATE TABLE user_permissions (
    user_id INT UNSIGNED NOT NULL,
    permission_id INT UNSIGNED NOT NULL,
    PRIMARY KEY (user_id, permission_id)
    -- FK ไป users/permissions ON DELETE CASCADE ทั้งคู่
);
```

```php
// รวมสิทธิ์จาก role + extra เฉพาะคน แล้ว dedupe — cache ต่อ user ต่อ request
public static function forUser(int $userId, int $roleId): array
{
    $extra = /* SELECT permission.key FROM user_permissions JOIN permissions WHERE user_id = :userId */;
    return array_values(array_unique([...self::forRole($roleId), ...$extra]));
}
```

**Deep link สำคัญ — เขียนไว้แค่ที่เดียว ห้ามมี 2 จุด:** ทุก entry point ที่เช็คสิทธิ์ (`can()` ใน controller, helper ที่ view เรียก) ต้องเปลี่ยนมาเรียก `forUser()` ตัวนี้ทั้งหมด — ถ้าเหลือจุดไหนยังเรียก `forRole()` ตรงๆ จุดนั้นจะมองไม่เห็น extra ของ user เลย (bug เงียบ ไม่มี error ให้เห็น)

**UI: checklist ต้องอัพเดทตาม role ที่เลือกแบบ real-time** — ถ้าไม่ทำ user จะสับสนว่า checkbox ไหนเป็น "role ให้อยู่แล้ว" ไหนเป็น "extra ที่ตั้งเอง" หลัง submit เท่านั้น วิธีทำ: embed `{roleName: [permissionKey,...]}` ของทุก role เป็น JSON ในหน้า, ฟัง event ตอนเปลี่ยน role selector แล้ว toggle `disabled`+`checked` ของแต่ละ checkbox สด — extra ที่ user ตั้งไว้จริง (ไม่ใช่ที่ได้จาก role) ต้องติ๊กค้างเสมอไม่ว่าจะสลับ role ไปมากี่รอบ (เทียบกับ set ที่โหลดมาตอนเปิดหน้า ไม่ใช่ recompute จาก role ปัจจุบัน)

**เจอจริง 2 บั๊กตอน implement หน้า checklist นี้ — ระวังซ้ำ:**

1. **ปุ่ม action เดี่ยว (ลบ role ฯลฯ) ห้ามอยู่ใน `<form>` ใหญ่ที่ save ทั้งหน้า** — nested `<form>` เป็น invalid HTML, browser hoist/พังเงียบๆ (กด submit ในกลับไปเรียก submit นอกแทน) เอาปุ่ม action แยกออกมาเป็น `<form>` คนละก้อน อยู่นอก form หลักเสมอ
2. **ถ้า entity เดียวกันแก้ได้จากหลาย UI (เช่น modal เร็วๆ ในหน้า list + หน้า edit เต็ม) ต้องมี field ตรงกันทุกจุด** — ตอนเพิ่ม checklist สิทธิ์พิเศษเข้าไปในหน้า edit เต็ม แต่ modal เดิมที่หน้า list ใช้จริงไม่มี field นี้ → แก้ user ผ่าน modal (ทางที่คนใช้จริง) จะเซฟทับ extra permissions เป็นค่าว่างเงียบๆ ทุกครั้ง ก่อนบอกว่า "เสร็จแล้ว" ต้อง grep หา endpoint เดียวกันทั้งโปรเจคว่ามี form อื่น POST ไปที่เดียวกันไหม — ถ้ามีต้องอัพเดทให้ตรงกันหรือตัดเหลือทางเดียว

### Solodev Examples

```
❌ if ($_SESSION['role'] === 9) { ... }              — role ผูกตายเป็นเลข
✅ if ($this->can('admin.permissions.manage')) {...} — เช็คผ่าน permission ที่ผูกกับ role แบบ dynamic

❌ ติดตั้งใหม่ต้อง SSH เข้าไปรัน php seed.php มือ
✅ เข้าเว็บครั้งแรก เจอหน้า installer อัตโนมัติเพราะ isInstalled()=false

❌ เปิด/ปิด feature ต้องแก้โค้ด constant แล้ว deploy ใหม่
✅ Feature::enabled('export') อ่านจาก DB, admin toggle เองได้

❌ ยุบ role ทิ้งเหลือ 2 อย่าง (admin/user) แล้วตั้ง flag ทุกคนตั้งแต่ศูนย์
✅ role เดิมให้ default ตามหน้าที่งาน + ติ๊ก extra เฉพาะคนที่ต้องการจริง (ชะเอมเป็น opener แต่ทำหน้าที่ collector บางอย่างได้ด้วย)
```

### Solodev Constraints

- ห้าม fresh-install detect พังแบบ throw exception ตรงๆ — ต้อง fail-safe เป็น "ยังไม่ติดตั้ง" เสมอเมื่อเช็คไม่ได้
- ห้ามลบ/แก้ role ที่ `is_system=1` ได้จาก UI โดยไม่มี safeguard กันคนใช้ล็อกตัวเองออกจากระบบ
- ห้าม cache feature flag ข้าม request — toggle แล้วต้องเห็นผลทันที
- ห้าม over-engineer เป็น multi-tenant (per-company feature flag) ถ้า user ไม่ได้ขอ — เริ่มจาก single-tenant global flag ก่อน
- ห้ามยุบ role เหลือ 2-3 อย่างแบบกว้างๆ แล้วใช้ per-user flag ล้วนแทน RBAC ทั้งระบบ — เสียประโยชน์ "ตั้งค่าเริ่มต้นเร็ว" ของ role ไปเปล่าๆ ใช้ผสมกันเสมอ (role = default, per-user = ข้อยกเว้น)
- ห้ามมีจุดเช็คสิทธิ์ที่ยังเรียก `forRole()` ตรงๆ หลงเหลืออยู่หลังทำ per-user extra แล้ว — ต้องเปลี่ยนเป็น `forUser()` ให้ครบทุกจุด ไม่งั้น extra จะไม่มีผลแบบเงียบๆ

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

## Flow บังคับ 4 เฟส — ห้ามข้าม

```
0. อ่าน ref  →  1. ถามให้ครบ  →  2. เขียน DESIGN.md แล้วหยุดรอ approve  →  3. build
```

ห้ามเขียนโค้ดหน้าเว็บก่อนผ่านเฟส 2 ห้ามเดา requirement แทนการถามในเฟส 1

---

## เฟส 0 — อ่านเว็บ ref

ใช้ Chrome MCP เปิดจริง (เว็บ template สมัยใหม่ render ด้วย JS — WebFetch จะเห็นแค่ shell ว่าง)

1. `tabs_context_mcp` → `tabs_create_mcp` เปิดลิงค์ ref
2. `resize_window` 1440x900 → screenshot ด้วย `computer` (scroll เก็บ hero, section กลาง, footer)
3. `resize_window` 390x844 → screenshot ซ้ำ ดูว่า mobile ยุบ layout ยังไง
4. รัน `javascript_tool` ดึง token จริง แล้วอ่านผลด้วย `read_console_messages` (pattern: `\[REF\]`)

```js
(() => {
  const seen = { color:{}, bg:{}, font:{}, size:{}, weight:{}, radius:{}, shadow:{}, sectionPad:{} };
  const bump = (o,k) => { if (k && k !== 'none' && k !== 'rgba(0, 0, 0, 0)' && k !== '0px') o[k] = (o[k]||0)+1; };
  document.querySelectorAll('body *').forEach(el => {
    const r = el.getBoundingClientRect();
    if (r.width < 4 || r.height < 4) return;
    const s = getComputedStyle(el);
    bump(seen.color, s.color); bump(seen.bg, s.backgroundColor);
    bump(seen.font, s.fontFamily); bump(seen.size, s.fontSize);
    bump(seen.weight, s.fontWeight); bump(seen.radius, s.borderRadius);
    bump(seen.shadow, s.boxShadow);
  });
  document.querySelectorAll('section, header, footer, main > div').forEach(el => {
    const s = getComputedStyle(el);
    bump(seen.sectionPad, `${s.paddingTop}/${s.paddingBottom} w=${Math.round(el.getBoundingClientRect().width)}`);
  });
  const top = o => Object.entries(o).sort((a,b) => b[1]-a[1]).slice(0,8);
  console.log('[REF]', JSON.stringify(Object.fromEntries(Object.entries(seen).map(([k,v]) => [k, top(v)]))));
})()
```

5. ถ้า Chrome ใช้ไม่ได้ (ไม่มี permission / เว็บมี login gate / โหลดไม่ขึ้น) → fallback `WebFetch` อ่าน HTML + ไฟล์ CSS แล้ว**บอก user ตรงๆ ว่าอ่านได้แค่ text ไม่เห็นภาพจริง** ห้ามแกล้งวิเคราะห์ภาพที่ไม่ได้เห็น

สรุปจาก ref ให้ครบ 8 หัวข้อนี้ ก่อนไปเฟสถัดไป — ค่าที่ได้ต้องเป็นเลขจริงจาก computed style ไม่ใช่คำบรรยายลอยๆ

| หัวข้อ | ต้องได้อะไร |
|---|---|
| Color | HEX จริงของ bg / surface / text หลัก / text รอง / accent / border |
| Typography | font family heading+body, size scale (h1→body→caption), weight, line-height |
| Rhythm | base spacing unit, section padding desktop/mobile, container max-width |
| Shape | border-radius, ความหนา border, shadow มี/ไม่มี และแรงแค่ไหน |
| Layout | grid กี่คอลัมน์, gap, hero สูงเท่าไหร่, nav แบบไหน (sticky/transparent) |
| Component | ปุ่มมีกี่แบบ, card หน้าตายังไง, form field style |
| Motion | hover เปลี่ยนอะไร, duration/easing ประมาณเท่าไหร่ |
| Imagery | อัตราส่วนภาพ, มี overlay/filter ไหม, ภาพเต็มจอหรืออยู่ในกรอบ |

---

## เฟส 1 — ถาม requirement ให้ครบ

ถามด้วย `AskUserQuestion` แบ่งเป็น 2 รอบ (รอบละไม่เกิน 4 คำถาม) ทุกข้อต้องมีตัวเลือกที่เดาไว้ให้แล้วพร้อมข้อแนะนำ ไม่ใช่ปล่อยช่องว่าง

**รอบ 1 — ไม่รู้แล้วทำต่อไม่ได้**

| ถามอะไร | ตัวเลือกตั้งต้น |
|---|---|
| เว็บประเภทไหน เป้าหมายหลักคืออะไร | e-commerce / company profile / landing page ขายของชิ้นเดียว / portfolio / SaaS marketing / booking / dashboard ภายใน |
| Stack ที่จะ generate | static HTML+CSS / เข้าโครง PHP-MVC ของโปรเจคที่เปิดอยู่ / อื่นๆ |
| มีกี่หน้า หน้าอะไรบ้าง | 1 หน้า (long-scroll) / 3-5 หน้าหลัก / ระบุเอง — ถ้า user ระบุเอง ให้ทวนเป็น sitemap กลับไปยืนยัน |
| กลุ่มผู้ใช้ + ภาษาเนื้อหา | ไทยล้วน / อังกฤษล้วน / สองภาษา (มีผลตรงกับ font pairing และความยาวบรรทัด) |

**รอบ 2 — ดีเทลที่ทำให้ build ได้จบในรอบเดียว**

| ถามอะไร | ตัวเลือกตั้งต้น |
|---|---|
| ลอก ref แค่ไหน | เอา design language อย่างเดียว (แนะนำ) / ลอก layout ใกล้เคียงด้วย / เอาแค่ mood แล้วออกแบบใหม่ |
| Brand ที่มีอยู่ | มีโลโก้+สีแบรนด์แล้ว (ขอไฟล์/HEX) / ยังไม่มี ให้ derive จาก ref + คำสไตล์ |
| Content | มี copy จริงแล้ว / ใช้ placeholder ที่เขียนให้ตรงธุรกิจ (ไม่ใช่ lorem ipsum) |
| Feature ที่ต้องทำงานจริง | cart+checkout / ฟอร์มติดต่อส่งเมล / search / auth / แก้เนื้อหาผ่านหลังบ้าน / ไม่มี static ล้วน |

ถามเพิ่มเฉพาะเมื่อยังคลุมเครือ: dark mode เอาไหม, ระดับ animation (นิ่ง / hover เท่านั้น / scroll reveal), รองรับ browser เก่าแค่ไหน, มี deadline/ขนาดงานที่จำกัดไหม

ข้อที่ user ตอบไปแล้วในข้อความแรก **ห้ามถามซ้ำ** — ทวนสั้นๆ ว่ารับทราบแล้วข้ามไป

---

## เฟส 2 — DESIGN.md แล้วหยุด

เขียนไฟล์ `DESIGN.md` ที่ root ของโปรเจค (หรือโฟลเดอร์งานนั้น) ประกอบด้วย:

1. **สรุป brief** — ประเภทเว็บ, เป้าหมาย, กลุ่มผู้ใช้, stack, sitemap (จากเฟส 1)
2. **ref ที่ใช้ + สิ่งที่หยิบมา / สิ่งที่ตั้งใจไม่เอา** ระบุเป็นข้อ
3. **Design token เป็นตาราง** พร้อมค่าจริง — เขียนให้เป็น CSS custom property ได้ทันที (`--color-bg`, `--space-6`, `--radius-sm`, `--font-heading`, `--dur-fast`)
4. **Type scale + spacing scale** เป็นตัวเลขครบทุกขั้น
5. **Component spec** — ปุ่ม (default/hover/disabled), card, nav, hero, form field, footer อธิบายด้วย token ไม่ใช่ค่า hardcode
6. **Responsive** — breakpoint กี่จุด, แต่ละจุด layout เปลี่ยนยังไง
7. **ไฟล์ที่จะสร้าง/แก้** เป็นลิสต์พาธ

จบแล้ว**หยุด** สรุปให้ user 5-10 บรรทัด ถามว่าอนุมัติหรือปรับตรงไหน ห้ามเขียนโค้ดต่อจนกว่าจะได้ไฟเขียว

---

## เฟส 3 — Build

ยึด token จาก DESIGN.md เป็น CSS custom property ใน `:root` ที่เดียว ห้าม hardcode สี/ระยะซ้ำในไฟล์ component แล้วทำตามลำดับ: token + layout shell (nav/footer) → หน้าแรก → หน้าที่เหลือ → responsive pass → hover/motion pass เสร็จแต่ละหน้าหลัก เปิดดูด้วย Chrome เทียบกับ screenshot ref ที่เก็บไว้ อย่าเชื่อว่าโค้ดถูกโดยไม่ดูของจริง

**ระหว่าง build ต้องทำตาม checklist นี้เสมอ (ยกมาจากหัวข้อ 1 และ 3 ด้านบน — ไม่ต้องเปิดไฟล์อื่น):**

- **Visual/motion ทุกจุด** → เช็คตาราง "เลือกอะไรเมื่อไหร่" ในหัวข้อ 3. CSS-First ก่อนเขียน JS เสมอ — animation/responsive/sticky footer/modal ทำด้วย CSS ล้วน, เขียน JS เฉพาะ dropdown ที่ต้อง escape overflow หรือรู้พิกัด runtime จริงๆ
- **ชื่อ class/component/ตัวแปร** → ตาม intent ไม่ใช่ generic (`$data`, `process()`) — ดู AI Smell ข้อ 1 ในหัวข้อ 1. Human Coding
- **ห้าม abstraction เผื่ออนาคต** → ไม่สร้าง component wrapper/layer ที่ spec ไม่ได้ขอ (AI Smell ข้อ 2, 3)
- **ห้าม comment obvious** — comment เฉพาะที่บอกทำไม/ที่มาของค่า token (Human Essence ข้อ 4)
- **ทำเท่าที่ DESIGN.md ระบุ** — ไม่เพิ่ม section/feature ที่ไม่ได้อนุมัติ (AI Smell ข้อ 3)

---

## แปลคำสไตล์เป็นกฎที่จับต้องได้

user มักพูดเป็นคำกว้างๆ ต้องแปลงเป็นตัวเลขก่อน แล้วเขียนลง DESIGN.md

| คำสไตล์ | กฎที่ใช้จริง |
|---|---|
| minimal premium | whitespace เยอะ (section padding 96-160px desktop / 48-64px mobile); สีไม่เกิน 3 + neutral; accent ใช้พื้นที่ไม่เกิน 5%; font ไม่เกิน 2 ตระกูล; radius 0-8px; shadow แทบไม่มีหรือนุ่มมาก; border hairline 1px; motion 150-250ms ease-out; ห้าม gradient ฉูดฉาด ห้าม icon เยอะ |
| bold / editorial | heading ใหญ่มาก (clamp ถึง 72px+); contrast สูง; asymmetric grid; ภาพเต็มความกว้าง; สี accent จัดจ้าน |
| soft / friendly | radius 12-24px; palette pastel; shadow ฟุ้งกว้าง; illustration แทนภาพถ่าย; motion 250-400ms ease-in-out |
| tech / dark | bg เข้ม #0b0d12 ระดับ; accent เรืองแสง 1 สี; mono font สำหรับตัวเลข/โค้ด; border บางสว่างกว่า bg เล็กน้อย |
| luxury | serif heading; letter-spacing กว้างใน label ตัวพิมพ์ใหญ่; palette เกือบขาวดำ + 1 สีโลหะ; ภาพอัตราส่วนสูง; motion ช้า 400ms+ |

คำที่ไม่อยู่ในตาราง — ถาม user ว่าหมายถึงอะไรด้วยการยกตัวอย่าง 2-3 อย่างที่จับต้องได้ ห้ามเดาเงียบๆ

---

## Constraints

- **ห้ามก็อป asset จาก ref** — รูป โลโก้ ไอคอน ข้อความโฆษณา ชื่อแบรนด์ เป็นของเจ้าของเว็บนั้น เอาได้แค่ design language (สัดส่วน จังหวะ โทน โครง layout) ถ้า user สั่งให้ก็อปตรงๆ ให้บอกความเสี่ยงหนึ่งประโยคแล้วทำตามที่สั่ง
- ห้ามดึงไฟล์ CSS/JS ของ ref มาใส่โปรเจคทั้งก้อน — เขียนใหม่จาก token ที่สรุปได้
- ไม่แน่ใจว่าค่าไหนคือค่าจริงจาก ref (อ่านไม่ได้ / fallback text) → เขียนใน DESIGN.md ว่า "ประมาณจาก..." อย่าเขียนเหมือนวัดมาจริง
- ref ที่เป็น template ขาย (flatlogic, themeforest ฯลฯ) มักมี license — ถ้า user จะเอาไปใช้เชิงพาณิชย์ เตือนหนึ่งบรรทัดว่าให้เช็ค license ของ font และภาพที่ใช้
- font ที่ ref ใช้อาจเป็น commercial font — ถ้าไม่ฟรีบน Google Fonts ให้เสนอตัวใกล้เคียงที่ฟรี พร้อมบอกว่าตัวจริงคืออะไร
- โปรเจคที่ไม่มี build step อยู่แล้ว ห้ามลาก Tailwind/Sass เข้ามาโดยไม่ถาม

---

# 5. Grounded DB

## Objective

ออกแบบ schema, เขียน SQL, ทำ migration/data import ให้ถูกหลัก production — ใช้เมื่อสร้าง/แก้ตาราง, เขียน query, import xlsx/csv

## Steps/Workflow

0. **ก่อนสร้างตารางใหม่ (หรือคอลัมน์ใหม่แบบเผื่อไว้)** → verify: มีใครจะ**เขียน**มันจริงจาก UI/flow ที่มีอยู่จริงไหม ไม่ใช่แค่ "เผื่ออนาคต" — ดูหัวข้อ Code-first vs DB-first ด้านล่าง
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
