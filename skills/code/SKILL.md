---
name: code
description: Human Coding — โค้ดเหมือนคนเขียน ไม่โผล่กลิ่น AI (ชื่อ generic, abstraction เผื่อ, ทำเกินขอ, comment, hardcode) ใช้เมื่อเขียน/แก้ไข/ตรวจโค้ดทุกชนิด
---

# Code — Human Coding Style

## Objective

เขียนโค้ดให้ "เหมือนคนเขียน" — อ่านแล้วเข้าใจได้ทันที, ต่อยอดได้, ไม่โผล่กลิ่น AI ใช้เมื่อเขียนหรือแก้ไขโค้ดทุกชนิด (PHP, JS, SQL, view, doc)

## หลักคิด 4 มิติ

| มิติ | สาระ |
|------|------|
| **Style** | โค้ดอ่านแล้วรู้เรื่องเหมือนคนเขียน — ชื่อบอก intent, flow เป็นธรรมชาติ, ไม่ clever |
| **Workflow** | พฤติกรรมตอนทำงานเหมือนคน — คิดก่อนแก้, อ่านรอบข้าง, ไม่เร่ง, ไม่ทำเกินขอ |
| **Anti-AI** | ไม่มี AI smell — ตรวจก่อนเสร็จทุกครั้ง (ตารางด้านล่าง) |
| **Collaboration** | ทำงานกับคน — ถามเป็นช้อย, เสนอทางเลือก, ให้คนตัดสินใจเรื่องสำคัญ |

## Steps/Workflow (Human Coding)

งานซับซ้อน/เจอ bug/requirement ไม่ชัด → ผ่าน `think-before-type` skill ก่อน (Phase 0 → เลือก path → execute) แล้วค่อยมาขั้นตอนนี้

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
| 13 | ครอบทั้งหน้า PHP ด้วย JS/AJAX ทั้งที่ HTML ล้วนก็ทำงานได้ | native HTML ก่อนเสมอ (`<a href>`, `<form>`, `<details>`) — เสริม AJAX เฉพาะจุดที่ HTML ทำไม่ได้จริง (ข้อ 24) |

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

โปรเจคที่มี framework/convention เฉพาะตัว (เช่น modal component, MVC helper class เฉพาะ, container name) — ดูกฎเพิ่มเติมใน `AGENTS.md` ของโปรเจคนั้น กฎในนี้เป็นหลักการทั่วไปข้ามโปรเจค

## Core Principle

**Simplest thing that could possibly work. No comments. No hardcode. No overengineering.**
**เขียนเหมือนคนที่อยากให้คนอื่นอ่านต่อ — ไม่งั้นก็ไม่ต่างจาก AI**

## Effectiveness Indicators — รู้ได้ยังไงว่า human coding ได้ผลจริง

ไม่ใช่แค่ทำตาม checklist แล้วจบ — เช็คผลจริงว่าดีขึ้นไหม:

| สัญญาณ | ดี (✅) | แย่ (❌) |
|--------|---------|---------|
| ขนาด diff | เล็ก ตรงเป้า เท่าที่ task ขอ | บวมเพราะ "improve" ของข้างเคียง |
| Rewrite | แทบไม่มี — คิดครบตั้งแต่รอบแรก | เขียนใหม่ซ้ำเพราะ overengineer แล้วต้องถอด |
| จังหวะถาม | ถามช้อยก่อนเริ่ม เมื่อไม่แน่ใจ | เดาแล้วเขียน พลาดแล้วค่อยถาม |
| Bug ซ้ำ root cause เดิม | ลดลง — เพราะมี post-mortem (rule 19.8) | เจอ pattern เดิมพังคนละจุดซ้ำเรื่อยๆ |

---

## Full Rules (ข้อ 1-23)

รายละเอียดกฎแต่ละข้อที่ Constraints อ้างถึง (เช่น MVC boundary, hardcode, comment, naming, bug fix protocol, git commit/branch, refactor โค้ดเดิม, temp script, การเขียน skill ให้ AI) อยู่ใน [references/rules.md](references/rules.md) — เปิดอ่านเมื่อ Constraints/AI Smell table ไม่พอ
