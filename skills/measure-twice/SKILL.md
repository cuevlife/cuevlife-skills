---
name: measure-twice
description: วินัยตลอด dev lifecycle แบบ solo dev — วางแผนก่อนลงมือ (planner/diagnostic loop/grilling), TDD (test-first, RED-GREEN-REFACTOR), security checklist (SQLi/XSS/CSRF/upload/secrets/session), code review ก่อน commit (Standards+Spec), deploy checklist (backup/verify/rollback). รวม think-before-type + bug-first + before-hacked + second-eyes + no-yolo-deploy เป็นก้อนเดียว ใช้เมื่อเริ่มงานซับซ้อน/เจอ bug/requirement ไม่ชัด, เขียน/แก้โค้ดที่มี logic หรือรับ input/secret/upload, ก่อน commit/merge, หรือก่อนขึ้น production (ต่างจาก built-in /code-review /security-review ที่รีวิว diff จริง — อันนี้คือ checklist/workflow หลักที่ใช้ทั้ง lifecycle)
---

# Measure Twice — Full Dev Lifecycle Discipline

## Objective

วัดสองครั้งตัดครั้งเดียว — คุม lifecycle ของงานทั้งสาย ตั้งแต่คิดก่อนลงมือ, พิสูจน์ด้วย test, กัน security blind spot, ตรวจงานก่อน commit, ไปจนขึ้น production แบบกลับหลังได้ เหมาะกับ solo dev ที่ไม่มี peer review — AI ทำหน้าที่วางแผน/พิสูจน์/ตรวจ/คุม release แทน

ใช้เป็น 5 phase ต่อเนื่องกัน (ข้ามไป phase ที่เกี่ยวกับงานนั้นได้ ไม่ต้องทำครบทุก phase เสมอ — เช่น งาน UI เล็กไม่ต้องเข้า security phase):

0. **Think** — วางแผนก่อนลงมือ
1. **Build (TDD)** — เขียน test ก่อนโค้ด
2. **Security** — เช็คก่อน deploy ถ้ารับ input/secret/upload
3. **Review** — ตรวจก่อน commit/merge
4. **Deploy** — ขึ้น production แบบมี backup/rollback

---

## Phase 0 — Think (ก่อนลงมือ)

**Verify:** assumptions ครบ, อะไรไม่ชัดถามเป็นช้อยก่อนเริ่ม, จัดประเภทงานแล้วขอ approve ตามน้ำหนักที่ตรงกับประเภท — ห้ามข้ามไป implement เฉยๆ

ก่อนลงมือ:
- State assumptions explicitly
- Multiple interpretations → present all, don't pick silently
- Simpler approach exists → say so
- Something unclear → stop, name it, ask

### Classify Scope — Spike / Bounded / Architectural

จัดงานเป็น 3 ระดับก่อนเริ่ม ถ้าไม่แน่ใจว่าอยู่ระดับไหน → เลือกระดับที่หนักกว่าไว้ก่อนเสมอ ถ้าเจอ complexity แฝงกลางทาง → ยกระดับขึ้นได้ ห้ามลดระดับ

- **Spike** — คำถาม feasibility ล้วนๆ ("ทำได้ไหม/ทางไหนดีกว่า") ไม่ต้องเขียน production code บอกแผน probe สั้นๆ (2-3 ประโยค) → user approve → ทดลองแบบถูกที่สุด → รายงานผลเป็นข้อเสนอแนะ
- **Bounded** — แก้ไข/เพิ่มใน codebase ที่ scope ชัด: สำรวจ context → ถามคำถามที่ยังไม่ชัด → ออกแบบสั้นๆ ในแชท → user approve ก่อนลงมือ ไม่ต้องมี spec แยกไฟล์
- **Architectural** — ระบบใหม่ หรือ restructure ส่วนสำคัญ: สำรวจ context → ถามคำถาม → เสนอ 2-3 approach พร้อม trade-off → ออกแบบเป็น section → เขียน spec ที่ `docs/specs/YYYY-MM-DD-<topic>-design.md` → self-review (เช็ค placeholder, ข้อขัดแย้ง, ขอบเขต, ความกำกวม) → user approve → ค่อยเข้า Plan → Execute

**กติกาสำคัญ:** งานเล็กแค่ไหนก็ยังต้อง design + approve เสมอ — สิ่งที่ลดตามขนาดงานคือ**ความยาวของ artifact** (2 ประโยคในแชท พอสำหรับงานเล็ก) ไม่ใช่ตัว approval gate เอง ห้าม implement ก่อนบอก user ว่าจะทำอะไรและได้รับ approve

### Complex tasks → Planner Agent

ถ้า task มี 3+ concerns (architecture, security, multi-file, data migration) → อย่าลงมือเอง อย่า spawn subagent หลายตัวแยก role ก็อย่า (เคยลองแล้วผลลัพธ์มักไม่ตรงโจทย์ synthesize ยาก) — เรียก agent ชื่อ `planner` (model: opus, effort: high/xhigh, read-only ห้ามแก้ไฟล์) ตัวเดียวให้สำรวจโค้ดเบสแล้วออกแบบแผนที่ verify ได้เป็นขั้นๆ กลับมา

ถ้า `planner` agent ยังไม่มีในเครื่อง ให้สร้างที่ `~/.claude/agents/planner.md` ก่อน

**ทันทีที่ planner ส่งแผนกลับมา ก่อนสรุปให้ user ในแชท — main session (Sonnet) ต้องเขียนแผนเต็มลงไฟล์ `docs/specs/YYYY-MM-DD-<topic>-design.md` ก่อนเสมอ** (planner เขียนไฟล์เองไม่ได้ตาม design — หน้าที่นี้เป็นของ main session) ไม่ทำข้อนี้ = แผนหายไปกับแชท ดูย้อนหลังไม่ได้ ห้ามข้าม แม้ user จะยังไม่ได้ approve แผนก็ตาม (เขียนไฟล์ไว้ก่อน แก้/เพิ่ม note ทับทีหลังได้เมื่อ user ตอบคำถามที่ยังไม่ชัด) จากนั้นค่อยสรุปแผนแบบย่อในแชท พร้อมบอก path ไฟล์ที่เขียนไว้ แล้ว main session execute ทีละขั้นตาม Plan → Execute ด้านล่างเมื่อ approve แล้ว

### Bug → Diagnostic Loop

**Core loop:** Reproduce → isolate → hypothesis → fix → verify. Build tight pass/fail feedback signal ก่อน debug. Fix รอบเดียว อย่า shotgun

**Step 1 — Reproduce จนแน่ใจ**
- ถ้า reproduce ไม่ได้ → หยุด หาวิธีจนกว่าจะได้
- บันทึก precondition, input, environment, steps ที่แน่ชัด
- reproduce ใน local/dev environment ก่อนคิด fix
- อย่า connect ไป production DB เพื่อ debug

**Step 2 — Read the flow**
- อ่านโค้ดทั้งฟังก์ชัน/controller ตั้งแต่ต้นจนจบ
- ยังไม่ต้องแก้ — แค่ทำความเข้าใจ data flow และ intent
- เช็ค caller → callee chain — อย่าดูแค่จุดที่สงสัย

**Step 3 — Isolate root cause**
- ถาม "ทำไม?" ซ้ำจนกว่าจะเจอสาเหตุจริง (5 Whys)
- hypothesis ต้อง specific — "เพราะ type mismatch ที่ line 42" ไม่ใช่ "เพราะ input มันผิด"
- แยก symptom ออกจาก root cause ให้ชัด

**Step 4 — Build feedback signal**
- สร้าง test / scenario ที่ทำให้ bug โผล่ชัดเจน
- signal ต้อง quick — กดครั้งเดียวรู้ว่าผ่านหรือไม่
- ใช้ signal นี้เพื่อยืนยันว่า fix ได้ผล

**Step 5 — Fix with minimal diff**
- แก้เฉพาะสาเหตุ — อย่าแตะของที่ไม่เกี่ยวกับ bug
- 1 commit = 1 fix — ถ้าต้องแก้หลายสาเหตุ → แยกกัน
- ห้าม refactor, rename, reformat ใน commit เดียวกับ fix

**Step 6 — Verify**
- ใช้ feedback signal จาก step 4 ยืนยัน bug หาย
- ตรวจ regression — ของอื่นที่เกี่ยวข้องยังทำงานปกติไหม
- เช็คว่าไม่มี debug code, log, หรือ config หลงเหลือ

**Step 7 — Learn (optional but recommended)**
- ถ้า recurring pattern → เพิ่ม guard / test / validation ป้องกัน
- บันทึกสั้นๆ ว่าสาเหตุคืออะไร (ถ้าช่วยให้ debug เร็วขึ้นในอนาคต)

**ข้อควรจำ:**
- Shotgun debugging (เดา → แก้ → ดู → เดาใหม่) = ห้ามเด็ดขาด
- ถ้า root cause ตีความได้หลายทาง → ถาม user เป็น options
- ถ้าโค้ดซับซ้อนเกินกว่าจะ isolate ได้ → spawn subagent ช่วยวิเคราะห์
- Debug นานเกิน 30 นาทีโดยไม่มีความคืบหน้า → หยุด ถาม user

### Unclear requirements → Grill

1 question at a time, multiple-choice options, stop at 95% confidence

### Plan → Execute

1. Break into small, verifiable steps
2. Each step = ทำอย่างเดียว
3. Verify each step before next
4. เมื่อเริ่มเขียนโค้ดจริง → เปิดใช้ `code-man` skill (หัวข้อ 1. Human Coding) สำหรับสไตล์การเขียน แล้วเข้า **Phase 1 — Build (TDD)** ด้านล่างสำหรับ logic/flow

---

## Phase 1 — Build with TDD

**Objective:** พิสูจน์ว่าโค้ดทำงานถูกต้องแบบมีหลักฐาน — เขียน test ก่อน, ครอบคลุม path หลัก + edge case, เร็วพอจะรันบ่อย ไม่ over-test

1. **เขียน test case ก่อนเขียนโค้ด** → verify: input → expected output ครบ path หลัก + edge case (empty, ผิด format, ขอบเขต)
2. **RED** → verify: test fail เพราะ feature ยังไม่มี — ไม่ fail เพราะ test พัง/ตั้ง expected ผิด
3. **GREEN** → verify: เขียนโค้ดเท่าที่ test ผ่าน — ไม่เขียนเกินที่ test ต้องการ
4. **REFACTOR** → verify: โค้ดสะอาดขึ้น แต่ test ยังผ่านทั้งหมด
5. **E2E ตามสเกลงาน** → verify: logic ซับซ้อน → unit test ใน tests/ (phpunit); flow/UI → browser จริง (Playwright/browser tools) ตรวจ viewport + interaction + state หลัก (default/loading/empty/error/success/disabled)
6. **Regression** → verify: test เก่าทั้งหมดยังผ่าน — ของเดิมไม่พัง
7. **Mock external dependency** → verify: logic ที่เรียก payment gateway/webhook sender/SMTP/API ภายนอก (ดู `code-man` หัวข้อ Idempotency) — test ต้อง mock ของเหล่านี้ ไม่ยิงของจริงตอนรัน test เพราะช้า, ไม่ deterministic, และเสี่ยงตัดเงิน/ส่งจริงถ้า mock หลุด

```
❌ เขียนโค้ดเสร็จ → "น่าจะเวิร์ค" → push      → ไม่มีหลักฐาน
✅ เขียน test ก่อน: input {qty:3, price:10} → expected 30
   RED: test fail (ยังไม่มีฟังก์ชัน)
   GREEN: เขียนฟังก์ชัน → test pass
   → มีหลักฐานว่าถูกต้องจริง

❌ test ทุกอย่างรวม getter/setter trivial    → เสียเวลา, รก, maintenance แพง
✅ test เฉพาะ logic ที่มีโอกาสผิด: คำนวณ, เงื่อนไข, edge case

❌ E2E คลิกทุกอย่างบน browser             → ช้า, fragile
✅ logic → unit test; flow สำคัญ → E2E บน browser เฉพาะ path หลัก
```

**Constraints:**
- ห้ามเขียนโค้ดโดยไม่มี test case (อย่างน้อย walk-through)
- ห้ามข้าม RED — ถ้า test ผ่านตั้งแต่แรก แสดงว่าเขียน test หลัง หรือ expected ผิด
- ห้ามเขียน test ที่ผูกกับ implementation (รู้ชื่อ method ภายใน) — brittle แก้อะไรก็พัง
- ห้าม over-test — getter/setter/ของ trivial ไม่ต้อง test
- ห้าม E2E ทุกอย่าง — ใช้ unit ก่อน, E2E เฉพาะ flow หลัก
- ห้ามลบ/แก้ test ที่ผ่านอยู่โดยไม่มีเหตุผล — test เก่า = หลักฐาน regression
- ห้ามบอกว่า "เสร็จแล้ว" โดย test ยังไม่ผ่าน — verify จริงก่อน
- ห้าม test ที่เรียก external service จริง (payment gateway, SMTP, third-party API) — ต้อง mock เสมอ

---

## Phase 2 — Security Checklist

ใช้เมื่อโค้ดรับ input/เก็บ secret/อัปโหลดไฟล์ หรือก่อนขึ้น production **Objective:** กัน blind spot ด้าน security พื้นฐานของงาน solo dev — ตรวจให้ครบก่อน deploy โดยไม่ต้องเป็น expert: SQLi, XSS, CSRF, file upload, secrets, session, error leak

1. **Scan secrets** → verify: ไม่มี password/API key/token ในโค้ดหรือ git history — ใช้ Env/config เท่านั้น
2. **เช็ค input ทุกทาง** → verify: DB query ใช้ prepared statement เสมอ (ห้ามต่อ string), output escape ทุกจุด, validation ฝั่ง server (ไม่ใช่แค่ client)
3. **เช็ค CSRF** → verify: ทุก write action (POST/PUT/PATCH/DELETE) มี token ตรวจ
4. **เช็ค file upload** → verify: จำกัดประเภท/ขนาด, ตั้งชื่อใหม่, เก็บนอก web root หรือเช็ค execution, ไม่เชื่อ extension จาก user
5. **เช็ค session/auth** → verify: cookie HttpOnly + SameSite, logout จริง, role check ฝั่ง server ทุก action ที่ต้องสิทธิ์
6. **เช็ค error leak** → verify: error/stack trace/DB message ไม่โผล่หน้าจอ user — log ไว้ข้างหลัง
7. **เช็ค headers/อื่นๆ** → verify: X-Frame-Options/CSP ที่จำเป็น, rate limit บน endpoint ละเอียดอ่อน (login, upload), hash password (bcrypt/argon2)

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

**Constraints:**
- ห้าม hardcode secret — password/API key ต้องผ่าน Env/config เท่านั้น
- ห้ามต่อ SQL string ด้วย input — prepared statement เสมอ
- ห้าม echo error/stack trace ตรงๆ หน้าผู้ใช้
- ห้ามเชื่อ extension/type จาก client ฝั่งเดียว — ตรวจด้วย
- ห้าม deploy ก่อนผ่าน checklist ข้อ 1-7 ครบ
- ห้ามเก็บ password plaintext หรือ hash ที่ไม่ใช่ bcrypt/argon2

---

## Phase 3 — Review (ก่อน commit/merge)

**Objective:** ตรวจงานก่อน commit/merge ให้จับปัญหาได้จริง (ไม่ใช่ดูผ่านๆ) — 2 แกน: Standards (เขียนถูก convention ไหม) + Spec (ตรง requirement ไหม behavior เดิมพังไหม) รายงานเป็น severity พร้อมหลักฐาน

1. **กำหนดขอบเขต review** → verify: รู้ว่า review อะไร — diff ตั้งแต่ branch point / commit ที่เกี่ยวข้อง / ผลงาน AI ทั้งชิ้น; diff กว้าง/ปนหลายเรื่องจนรีวิวไม่มั่นใจว่าครบ → หยุดบอก user ว่าควรแยกเป็นหลาย commit/PR ก่อน ไม่ใช่รีวิวรวดเดียวแบบเดา
2. **แกน 1 — Standards** → verify: ไล่ AI smell table + Human Essence + Framework Boundary + Blast Radius + Idempotency + Observability (ใน `code-man` skill หัวข้อ 1. Human Coding): ชื่อ, abstraction, ทำเกินขอ, hardcode, comment, pattern ของโปรเจค, หลุดกรอบ framework, caller ที่ยังไม่เช็ค, double-submit/webhook ซ้ำ, log ที่ไม่ actionable
3. **แกน 2 — Spec** → verify: ตรง requirement? behavior เดิมไม่พัง? edge case คิดครบ? (เทียบ test case จาก Phase 1)
4. **รัน/ตรวจจริง** → verify: test ผ่าน, lint/static analysis ผ่าน, ลอง flow จริง (ไม่ใช่แค่อ่าน)
5. **รายงาน severity** → verify: แต่ละ finding มี: severity (blocker/major/minor/nit) + ที่อยู่ (ไฟล์:บรรทัด) + หลักฐาน + วิธีแก้
6. **แก้/ปิด findings** → verify: blocker/major แก้ครบ, minor/nit ตัดสินใจชัด (แก้หรือเก็บเป็นของรู้), test ผ่านอีกครั้ง

```
❌ "โค้ดดูโอเคนะ"                              → ไม่ได้ review จริง
✅ "blocker: บรรทัด 42 SQL ต่อ string → SQLi
   major: ฟังก์ชันนี้ไม่รองรับ qty=0 → เดิมรองรับ
   minor: ชื่อ $data ควรเป็น $invoice"         → แก้ได้ทันที รู้ว่าตรงไหน

❌ review แต่ไม่รัน ไม่ลอง                    → พลาดของที่รันแล้วพัง
✅ review + รัน test + ลอง flow จริง          → จับได้ก่อน user เจอ

❌ nitpick ไปหมด (เว้นวรรค, ชื่อตัวแปร)      → noise จน finding ใหญ่จมหาย
✅ เน้น blocker/major ก่อน, nit รวมท้าย      → priority ชัด
```

**Constraints:**
- ห้าม review โดยไม่กำหนดขอบเขต (diff/commit) — review ทั่วหมด = ไม่ครบ
- ห้าม review โดยไม่รัน/ไม่ลอง — อ่านอย่างเดียวจับ bug ไม่ได้
- ห้าม nitpick นำหน้า — blocker/major มาก่อนเสมอ
- ห้ามแก้โค้ดของคนอื่น (subagent output) โดยไม่ดู findings ก่อน — review ก่อน แก้ทีหลัง
- ห้ามรายงานโดยไม่มีหลักฐาน (ไฟล์:บรรทัด) — "ตรงนี้ผิด" ไม่พอ ต้องบอกว่าทำไม
- ห้ามผ่านงานที่ blocker/major ยังไม่แก้ — จบเมื่อ findings ปิดครบ + test ผ่าน
- ห้าม review งานตัวเองตอนเพิ่งเขียนเสร็จ — ทิ้งไว้สักครู่/อ่านในมุมคนอื่นก่อน

---

## Phase 4 — Deploy

**Objective:** ขึ้น production แบบมีขั้นตอน ปลอดภัย และกลับหลังได้ — เป็น workflow ภาพรวม: ขั้นตอน/คำสั่งเฉพาะแต่ละโปรเจค (server, path, env, วิธีอัปโหลด) **อยู่ใน `AGENTS.md` ของโปรเจคนั้น** — phase นี้คือ checklist ที่ใช้ร่วมกันทุกโปรเจค

1. **อ่าน AGENTS.md ของโปรเจค** → verify: รู้ commands, โครงสร้าง deploy เฉพาะ (mirror? server? env?), gotchas ที่เกี่ยวข้อง
2. **Pre-deploy checklist** → verify: test ผ่านทั้งหมด (Phase 1), static analysis ผ่าน, security checklist ผ่าน (Phase 2), .env/config ถูกต้อง, migrations ครบ
3. **Backup ก่อน deploy** → verify: DB dump + ไฟล์สำคัญ เก็บไว้ — ต้อง restore กลับได้จริง
4. **Deploy** → verify: ตามขั้นตอนเฉพาะโปรเจค (mirror/upload/build) — อย่าแก้ production ตรงๆ ถ้าโปรเจคมี source + mirror
5. **Verify หน้างาน (smoke test)** → verify: หน้า login/ฟีเจอร์หลักทำงาน, ไม่มี error ใน log, ข้อมูลไม่หาย
6. **ถ้าพัง → Rollback** → verify: restore จาก backup ข้อ 3, ตรวจว่าเวอร์ชันเก่ากลับมาใช้ได้

```
❌ แก้ไฟล์บน server ตรงๆ แล้ว deploy เลย  → ไม่รู้ว่าทำอะไร, กลับหลังไม่ได้
✅ แก้ที่ source → test → backup → deploy ผ่าน mirror/ขั้นตอนของโปรเจค → smoke test

❌ deploy โดย test ไม่ผ่าน "เดี๋ยวค่อยแก้"  → พังแล้วแก้ยากบน production
✅ test ผ่านก่อน — deploy เฉพาะของที่พิสูจน์แล้ว

❌ ไม่มี backup "ข้อมูลไม่น่าพังหรอก"
✅ dump DB + สำรองไฟล์ก่อนทุกครั้ง — คืนค่าได้เมื่อผิดพลาด
```

**Constraints:**
- ห้าม deploy โดย test ยังไม่ผ่าน
- ห้ามแก้ production/server ตรงๆ โดยไม่ผ่านขั้นตอนของโปรเจค (mirror/source)
- ห้าม deploy โดยไม่มี backup (DB + ไฟล์) — ต้อง restore ได้จริง
- ห้ามข้าม smoke test หลัง deploy
- ห้าม deploy กลางดึก/ตอนรีบ โดยไม่มีเวลา rollback — เผื่อเวลาไว้เสมอ
- ห้ามใช้ credentials production ใน dev หรือ hardcode ในโค้ด
