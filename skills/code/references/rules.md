# Code Rules — รายละเอียด (ข้อ 1-21)

อ่านไฟล์นี้เมื่อ SKILL.md หลักไม่พอ (ex: ต้องดูรายละเอียดกฎข้อใดข้อหนึ่งที่ Constraints อ้างถึง)

## 1. MVC boundary — Controller owns all data

**View/template ไม่ควรเรียก global state (auth, request, config) เอง** — Controller ต้อง inject ทุกอย่างที่ view ต้องใช้มาให้ชัดเจน (explicit data passing) ไม่ใช่ implicit global access จากใน view

```
❌ view เรียก CurrentUser::get() / Request::current() เอง
✅ controller ส่ง $user, $requestPath มาทาง render/props ที่ชัดเจน
```

ผูก view กับ global state ตรงๆ ทำให้ test view ยาก และ view ผูกกับ framework internals เกินจำเป็น — รายละเอียด helper/auto-inject จริงของโปรเจค (ถ้ามี) ดู `AGENTS.md`

## 2. Scan first, then write

ก่อนเขียน 1 บรรทัด: เช็คของที่มีอยู่ก่อน (helper, component, service, pattern) → reuse
Local convention > global best practice เสมอ

## 3. YAGNI + KISS

- ไม่เขียนเผื่อ — ไม่สร้าง abstraction, flexibility, configurability ที่ยังไม่ต้องใช้
- เริ่มจาก simplest thing ก่อน
- Pattern ซ้ำ 2+ ครั้ง → extract. ยังไม่ซ้ำ → ปล่อยไว้
- โค้ดที่ maintain ง่าย > โค้ดสวยแต่ไม่มีใครกล้าแตะ

## 4. แต่ละภาษาอยู่ในไฟล์ของมันเอง — อย่า generate ข้ามภาษา

**อย่าใช้ backend language generate frontend script เป็น string** (PHP heredoc dump JS, template ที่ print `<script>` เป็น string จาก class ฯลฯ) — debug ยาก, ไม่มี syntax highlighting, IDE ช่วยอะไรไม่ได้:

```
❌ class Component { static function toggleJs() { return "<script>...</script>"; } }
❌ ฝัง JS ทั้งก้อนใน backend string/heredoc

✅ ไฟล์ .js ธรรมดาใน static asset dir — IDE ตรวจ syntax ได้, browser cache ได้
✅ HTML/markup component → partial/include ธรรมดา ไม่ใช่ static method return string
```

## 5. Debug-friendly — ห้ามซ่อน logic ไว้ใน class wrapper

- JS ต้องเป็น `.js` ไฟล์ตรงๆ — dev tools หาเจอ, breakpoint ได้, console.log ได้
- ห้ามฝัง JS ใน backend string/heredoc — มองไม่เห็น, IDE ช่วยอะไรไม่ได้
- HTML components → partial/include ธรรมดา ไม่ใช่ static method return string
- ถ้า component มี logic → controller/view logic, ไม่ใช่ magic class wrapper

## 6. Comment — ห้ามเล่าโค้ด อนุญาตบอกทำไม

ห้าม what-comment (เล่าซ้ำสิ่งที่โค้ดทำ — ไม่เพิ่มข้อมูล): `// get user by id` ❌
อนุญาต why-comment (บอก ทำไม/ขั้นตอน/ที่มา/ข้อควรระวัง — สั้น ตรงประเด็น): `// ลบรูปเก่าก่อน insert เพราะ FK ไม่ allow duplicate` ✅

- หลัก: ตั้งชื่อให้โค้ดอ่านรู้เรื่องก่อน — เหลือที่ชื่อบอกไม่ได้ค่อย comment
- why-comment ใช้ได้ทั้ง EN/TH — สั้น 1 บรรทัด อย่าเป็นเรียงความ
- ข้อยกเว้น: `@param`/`@return`/`@throws`/`@var` type hints, `@phpstan-ignore-next-line`
- ห้าม comment โค้ดเก่าทิ้งไว้ทั้งก้อน — ลบ หรือใช้ version control แทน

## 7. ห้าม hardcode เด็ดขาด

- passwords, secrets, API keys → env var / secret manager เท่านั้น (ชื่อ helper จริงดู `AGENTS.md`)
- magic numbers → named constant ของโปรเจค
- URLs → base-URL helper + route params (ไม่ต่อ string เอง)
- default values → random generate หรือ require explicit input
- ห้าม `'123123'`, `'admin'`, `'password'` ในโค้ด

## 8. Naming

ชื่อตัวแปร/ฟังก์ชันต้องบอกว่ามันทำอะไร
`$loan->updateStatus('closed')` ไม่ใช่ `$x->save()`
`if ($user->isAdmin())` ไม่ใช่ `if ($x->st === 1)`

## 9. Early return — ไม่ if ซ้อนลึก

```
function getLoan($id) {
    if (!$id) return null;
    return $this->db->find($id) ?? null;
}
```

## 10. Function ≤ 30 บรรทัด

เกิน → แยก function ย่อยเรียกเรียงกัน

## 11. เงื่อนไขซับซ้อน → extract function

`if ($this->isOverdue($loan))` ไม่ใช่ `if ($loan['status']===1 && ...)`

## 12. Surgical changes

แก้เฉพาะที่จำเป็น — ไม่แตะบรรทัด/ฟังก์ชันที่ไม่เกี่ยวกับ task
ลบ orphan code ที่เกิดจากการแก้ของตัวเอง

## 13. Commit = WHAT + WHY

`fix: wrong interest calc when payment date is holiday`
ห้าม: `update code`, `fix bug`

## 14. ภาษา EN/TH เท่านั้น

ห้ามจีน ญี่ปุ่น เกาหลี — ทุกอย่าง: code, variable, UI, doc

## 15. ห้าม emoji ใน UI

ใช้ icon (Bootstrap Icons), illustration, SVG graphic แทน

## 16. Tool-first

ก่อนใช้ LLM ประมวลผลข้อมูล — ถามก่อน: มี tool/API/service ที่ทำได้ดีกว่า/ถูกกว่าไหม?

## 17. ตอบสั้น ตรงประเด็น

บอกผลลัพท์ + วิธีสั้นๆ — ไม่น้ำ ไม่เล่าเป็นเรื่อง
ถามเมื่อไม่แน่ใจ → เป็นช้อย 3-5 ตัวเลือก อย่าถามปลายเปิด

## 18. เมื่อคำถามคลุมเครือ → ถามเป็นช้อย

ไม่เดา — ถ้าตีความได้หลายทาง ให้ถามกลับทันทีด้วย multiple-choice
ถามก่อนผิด 1 นาที > เสียเวลาแก้โค้ดผิด 30 นาที

## 19. Bug fix — ความละเอียดอ่อนก่อนลงมือ

### 19.1 Read-diagnose-fix — ห้าม skip ขั้นตอน

```
❌ เห็น bug → คิดว่าใช่ → แก้เลย → push → พัง
✅ อ่านโค้ดให้เข้าใจก่อน → หา root cause → hypothesis → fix → verify
```

ทุกครั้งก่อน fix bug: อ่าน flow ของฟังก์ชัน/ไฟล์นั้นให้ครบก่อน
อย่าเดา root cause จากชื่อฟังก์ชันหรือความเคยชิน

### 19.2 เข้าใจก่อนเปลี่ยน — อย่าแก้สิ่งที่ยังไม่เข้าใจ

- อ่านทั้งฟังก์ชัน หรือทั้ง controller method ก่อนแก้
- เช็ค caller/callee chain — มองให้เห็น上下游 ก่อนแตะตรงกลาง
- เข้าใจ data flow — ตัวแปรนี้มากจากไหน, ผ่านอะไรมาบ้าง
- เข้าใจ business logic — พฤติกรรมที่ถูกต้องคืออะไร?
- เข้าใจ intent — โค้ดเดิม *ตั้งใจ* ให้ทำอะไร? (ถึงแม้จะผิดก็ตาม)

### 19.3 Minimal diff — แก้เท่าที่ต้องแก้

- แก้เฉพาะบรรทัดที่ทำให้เกิด bug — อย่า refactor ข้างเคียง
- อย่าเปลี่ยน formatting, rename, หรือ extract function พร้อม fix
- อย่า "แก้เผื่อ" จุดอื่นที่คิดว่าอาจจะมีปัญหา
- 1 fix = 1 concern ถ้าต้องแก้หลายจุด → แยก commit

### 19.4 Reproduce + verify — มี feedback signal ก่อน fix

- ถ้า bug ที่ environment จริง reproduce ไม่ได้ → หยุด หาวิธี reproduce ให้ได้ก่อน
- สร้าง test case / scenario / manual step ที่ทำให้ bug แน่ชัด
- ใช้ signal นั้นยืนยันว่า fix ได้ผล และไม่พังของเดิม
- ถ้าเป็น production bug: reproduce ใน local/dev environment ก่อน

### 19.5 Surgical — แก้เสร็จต้องไม่ทิ้งรอย

- ลบ debug code, var_dump, console.log, commented code ที่ตัวเองใส่ตอน debug
- ไม่ทิ้งฟังก์ชัน/import ที่ไม่ได้ใช้แล้ว
- reset ค่าที่เปลี่ยนตอน debug (config, flag, environment) กลับเป็นค่าเดิม

### 19.6 Root cause > symptom

ถามตัวเอง: "ทำไมถึงเป็นอย่างนั้น?" ซ้ำ 3-5 รอบจนเจอสาเหตุจริง
แก้ symptom = bug กลับมา แก้ root cause = จบ

### 19.7 ถ้าไม่แน่ใจ → ถามเป็นช้อยก่อน fix

ถ้า root cause ตีความได้หลายทาง → หยุด ถาม user เป็น options
อย่า fix โดยมี assumption ที่ไม่confirmed

### 19.8 Post-mortem สั้นๆ หลัง fix — กันแก้ปัญหาเดิมซ้ำ

Bug ที่เกิดจาก root cause เดิมซ้ำ (เช่น logic เดียวกันพังคนละจุด) เสียเวลากว่าคิดครั้งแรกให้ครบ — หลัง fix เสร็จ สรุปสั้นๆ 3 บรรทัดในนี้ใน commit message (ไม่ต้องแยกไฟล์):

```
fix: <what — บรรทัด/เงื่อนไขที่พัง>
root cause: <ทำไมพัง — ที่มาจริง ไม่ใช่ symptom>
prevention: <ถ้ามี — pattern/เช็คที่ทำให้กันไม่ให้พังแบบนี้อีกในจุดอื่น>
```

ถ้า fix รอบนี้เป็นจุดที่ 2-3 ที่ logic เดิมพังซ้ำ (เช่น calculation logic เดียวกันพังคนละ edge case) → หยุดคิดว่ามี root cause กว้างกว่าที่เห็นไหม ก่อน fix เฉพาะจุด

## 20. ห้ามเขียน script ชั่วคราว (temp script) ลงในโปรเจค

**ห้ามเด็ดขาด** — อย่าสร้างไฟล์ `tmp-*.ext`, `debug-*.ext`, `test-*.ext` หรืออะไรก็ตามที่แค่ใช้ debug แล้วจะลบ ที่ root โปรเจคหรือที่ใดใน source tree

```
❌ tmp-debug-c07.php วางไว้ที่ root โปรเจค
❌ copy ไฟล์ debug เข้า container/production path
```

ใช้วิธีที่เขียนโค้ดเดี่ยวไม่ต้องเก็บไฟล์แทน:

- รันแบบ one-liner/REPL ของภาษา/runtime นั้น (php -r, node -e, python -c, หรือ `docker exec <container> ...` ตามชื่อ container จริงของโปรเจค — ดู `AGENTS.md`)
- เขียนไฟล์ใน `/tmp/` ของเครื่อง/container (นอก source tree) → ใช้เสร็จลบ
- เขียน test จริงใน test suite ของโปรเจค — ไม่ใช่ script ลอยๆ

ถ้าจำเป็นต้องใช้ไฟล์ชั่วคราวจริง → ใช้ `/tmp/` เท่านั้น + ลบหลังใช้เสมอ
ถ้าเจอ temp script ค้างในโปรเจค → ลบทิ้งทันที

## 21. Git — commit format + branch naming

- Commit format: `what: short description` — ห้าม `update code`, `fix bug`
- 1 commit = 1 fix/concern — ห้าม refactor/rename/reformat ปนกับ commit ที่เป็น fix (ซ้ำกับ rule 19.3 — ย้ำเพราะเป็นจุดพลาดบ่อย)
- Push ต่อเมื่อ test ผ่านแล้วเท่านั้น — ห้าม auto-push, ห้าม push ขณะติด bug loop
- Branch: `feature/*` งานใหม่, `fix/*` bug — merge ต้องผ่าน `second-eyes` skill ก่อนเสมอ

## 22. Refactor โค้ดเดิม — behavior ต้องไม่เปลี่ยน

ใช้เมื่องานคือ "ทำโค้ดเดิมให้ดีขึ้น" ล้วนๆ (ไม่ใช่ fix bug ไม่ใช่ feature ใหม่) — เช่น cleanup ไฟล์รก, แยก function ใหญ่, ลด duplicate

1. **เข้าใจก่อนแตะ** — อ่านทั้งไฟล์/ฟังก์ชัน, เข้าใจ intent เดิม, เช็ค caller/callee — ยังไม่แก้
2. **มี safety net ก่อนเริ่ม** — test ที่มีอยู่ / สร้าง test ครอบจุดสำคัญ / จด expected output ไว้เทียบ
3. **เปลี่ยนทีละก้าวเล็ก** — 1 refactor = 1 concern, รัน test หลังทุกก้าว ต้อง output เดิมเป๊ะ
4. **Cleanup เฉพาะที่ตัวเองแตะ** — ของเดิมที่ไม่เกี่ยวกับงาน ปล่อยไว้/พูดถึงก่อน ห้ามลบเอง

ห้ามเด็ดขาด: big-bang rewrite ทั้งไฟล์ในรอบเดียว, refactor ปนกับ fix bug/feature ในรอบเดียว, refactor โดยไม่มี safety net ที่เทียบผลได้

## 23. โครงสร้างการเขียน Skill ให้ AI

ทุก skill ที่เขียน/แก้ไข ต้องมี 4 ส่วนนี้ครบ — skill ที่ขาดส่วนไหน AI จะตีความพลาด

### 23.1 ระบุเป้าหมาย (Objective)

บอกหน้าที่หลักของ skill สั้นๆ ว่ามันใช้ทำอะไร — เขียนใน frontmatter `description` ด้วย

```
❌ description: บันทึกช่วยจำ
✅ description: แก้ bug แบบมีขั้นตอน — reproduce → isolate → fix → verify ใช้เมื่อเจอ bug
```

### 23.2 กำหนดขั้นตอน (Steps/Workflow)

เรียงลำดับขั้นตอนเป็นข้อๆ 1, 2, 3 — ป้องกัน AI ข้ามขั้นตอน
แต่ละขั้นตอนต้องมี `verify: [check]` ที่พิสูจน์ได้ว่าทำถูก

```
## Workflow
1. Reproduce → verify: bug โผล่ซ้ำได้
2. Isolate root cause → verify: ระบุบรรทัด/สาเหตุได้
3. Fix → verify: bug หาย + regression ปกติ
```

### 23.3 ให้ตัวอย่าง (Examples)

ใส่ Input/Output ที่เป็นรูปธรรม — AI เห็นภาพชัดกว่าคำอธิบาย

```
❌ ใส่ข้อมูลผู้ใช้ให้ถูกต้อง
✅ Input: { name: "สมชาย", age: 30 }
   Output: { name: "สมชาย", status: "adult" }
   ❌ Input: { name: "เด็กชายแดง", age: 12 }
   ❌ Output: { name: "เด็กชายแดง", status: "child" }
```

### 23.4 ระบุข้อห้าม (Constraints)

บอกสิ่งที่ห้ามทำเด็ดขาด — เพื่อลดความผิดพลาด

```
## Constraints
- ห้าม connect production DB เพื่อ debug
- ห้าม refactor ใน commit เดียวกับ fix
- ห้ามถาม user ปลายเปิด — ถามเป็นช้อย 3-5 ตัวเลือก
```

## 24. โปรเจค PHP — Progressive Enhancement (HTML-first, เสริม AJAX เท่าที่จำเป็น)

หน้าเว็บต้องทำงานได้ด้วย HTML ล้วนก่อน (baseline) — ใช้ JS/AJAX เสริมเฉพาะจุดที่ HTML ทำไม่ได้จริงๆ (เช่น live search แบบพิมพ์แล้วหาเองไม่ต้อง reload) ไม่ใช่ครอบทั้งหน้าด้วย JS ตั้งแต่แรกแบบ SPA

เช็คก่อนเขียน interactive element ใหม่ในหน้า PHP:
1. ทำเป็น native HTML ได้ไหม? — `<a href="?...">` สำหรับ sort/filter/pagination, `<details>/<summary>` สำหรับ toggle/popup/dropdown, `<form method="POST">` สำหรับ submit → ทำแบบนี้ก่อนเสมอ
2. ทำไม่ได้จริงๆ (ต้องอัปเดตแบบ real-time โดยไม่ reload หน้าทั้งหน้า) → ค่อยเสริม `fetch()` เฉพาะจุดนั้น สลับแค่ส่วนของ DOM ที่เกี่ยวข้อง ไม่แตะส่วนอื่น

```
✅ sort/filter/pagination = <a href="?sort=name_asc">
✅ toggle/popup/edit-inline = <details>/<summary>
✅ create/update/delete = <form method="POST"> ธรรมดา (ใช้ _method override ถ้า framework รองรับ)
✅ live search ต้อง real-time จริงๆ = fetch() เฉพาะช่องนั้น สลับ innerHTML เฉพาะส่วนผลลัพธ์

❌ ใช้ fetch/AJAX ครอบทั้งหน้าตั้งแต่ต้น (SPA-in-PHP) ทั้งที่ HTML ธรรมดาก็ทำงานได้
❌ ปิด JS ในเบราว์เซอร์แล้วปุ่ม/ลิงก์พื้นฐานใช้งานไม่ได้เลย
❌ วาง <script> ที่ query DOM element ก่อน element นั้นถูก parse ในหน้า (ทำให้ getElementById คืน null เงียบๆ) — ต้องอยู่หลัง element ที่มันอ้างถึงเสมอ
```

ศัพท์ที่ใช้เรียก pattern นี้: **Progressive Enhancement** — เขียนโค้ดให้ baseline (ไม่มี JS) ใช้งานได้ก่อน แล้วค่อย "เสริม" ไม่ใช่ "แทนที่" ด้วย JS
