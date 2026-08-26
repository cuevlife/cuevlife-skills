---
name: think-before-type
description: วางแผนก่อนลงมือ — complex task ใช้ swarm-brain, bug ใช้ diagnostic loop, unclear requirements ใช้ grilling
---

# Think Before Type — Before-Do Workflow

## Objective

วางแผนก่อนลงมือ เปลี่ยน task เป็นขั้นตอนที่ verify ได้ — complex task ใช้ swarm-brain, bug ใช้ diagnostic loop, requirement ไม่ชัดใช้ grilling ใช้เมื่อเริ่มงานซับซ้อน/เจอ bug/ไม่แน่ใจ requirements

## Steps/Workflow

1. **Phase 0 Think** → verify: assumptions ครบ, อะไรไม่ชัดถามเป็นช้อยก่อนเริ่ม (รายละเอียด: section `Phase 0 — Think` ด้านล่าง)
2. **เลือก path ตามความซับซ้อน** → verify: 3+ concerns → ตาม section `Complex tasks → Swarm Brain`; เจอ bug → ทำตาม section `Bug → Diagnostic Loop` ครบทุก step; requirement ไม่ชัด → ตาม section `Unclear requirements → Grill`
3. **แยกเป็นขั้นที่ verify ได้** → verify: แต่ละ step มี check ชัดเจน (ตาม section `Plan → Execute`)
4. **Execute ทีละขั้น** → verify: ผ่านขั้นก่อนค่อยขั้นถัดไป

> หมายเหตุ: Steps ด้านบนเป็นภาพรวม — section ด้านล่างคือขั้นตอนจริงของแต่ละ path ต้องเปิดอ่านและทำตามครบ ก่อนลงมือ

## Examples

```
❌ task ซับซ้อน → ลงมือเขียนเลย → ผิดทางกลางทาง
✅ task ซับซ้อน → swarm-brain (Architect/Security/DB/UX) → unified plan → execute

❌ เจอ bug → เดา → แก้ → push → พัง
✅ reproduce → isolate → hypothesis → fix → verify (diagnostic loop)

❌ requirement ไม่ชัด → เดาเอาเอง
✅ ถามเป็นช้อยทีละข้อ → หยุดที่ 95% confidence (grilling)
```

## Constraints

- ห้ามลงมือเขียนโค้ดก่อนผ่าน Phase 0
- ห้าม shotgun debugging (เดา → แก้ → ดู → เดาใหม่)
- ห้าม connect production DB เพื่อ debug
- ห้าม fix โดยมี assumption ที่ยังไม่ confirmed — ถามก่อน
- Debug นาน > 30 นาทีไม่มีคืบหน้า → หยุด ถาม user
- ห้าม refactor ปน commit เดียวกับ fix

## Phase 0 — Think

ก่อนลงมือ:
- State assumptions explicitly
- Multiple interpretations → present all, don't pick silently
- Simpler approach exists → say so
- Something unclear → stop, name it, ask
- **จัดประเภทงานก่อน** (ดู section `Classify Scope` ด้านล่าง) แล้วขอ approve ตามน้ำหนักที่ตรงกับประเภท — ห้ามข้ามไป implement เฉยๆ

## Classify Scope — Spike / Bounded / Architectural

จัดงานเป็น 3 ระดับก่อนเริ่ม ถ้าไม่แน่ใจว่าอยู่ระดับไหน → เลือกระดับที่หนักกว่าไว้ก่อนเสมอ ถ้าเจอ complexity แฝงกลางทาง → ยกระดับขึ้นได้ ห้ามลดระดับ

- **Spike** — คำถาม feasibility ล้วนๆ ("ทำได้ไหม/ทางไหนดีกว่า") ไม่ต้องเขียน production code บอกแผน probe สั้นๆ (2-3 ประโยค) → user approve → ทดลองแบบถูกที่สุด → รายงานผลเป็นข้อเสนอแนะ
- **Bounded** — แก้ไข/เพิ่มใน codebase ที่ scope ชัด: สำรวจ context → ถามคำถามที่ยังไม่ชัด → ออกแบบสั้นๆ ในแชท → user approve ก่อนลงมือ ไม่ต้องมี spec แยกไฟล์
- **Architectural** — ระบบใหม่ หรือ restructure ส่วนสำคัญ: สำรวจ context → ถามคำถาม → เสนอ 2-3 approach พร้อม trade-off → ออกแบบเป็น section → เขียน spec ที่ `docs/specs/YYYY-MM-DD-<topic>-design.md` → self-review (เช็ค placeholder, ข้อขัดแย้ง, ขอบเขต, ความกำกวม) → user approve → ค่อยเข้า `Plan → Execute`

**กติกาสำคัญ:** งานเล็กแค่ไหนก็ยังต้อง design + approve เสมอ — สิ่งที่ลดตามขนาดงานคือ**ความยาวของ artifact** (2 ประโยคในแชท พอสำหรับงานเล็ก) ไม่ใช่ตัว approval gate เอง ห้าม implement ก่อนบอก user ว่าจะทำอะไรและได้รับ approve

## Complex tasks → Swarm Brain

ถ้า task มี 3+ concerns (architecture, security, multi-file, data migration) → spawn sub-agents:

```
Architecture | Security | Performance | DB | UX | Ops → synthesize → unified plan
```

## Bug → Diagnostic Loop

### Core loop

1. Reproduce → isolate → hypothesis → fix → verify
2. Build tight pass/fail feedback signal ก่อน debug
3. Fix รอบเดียว อย่า shotgun

### Step-by-step

#### Step 1 — Reproduce จนแน่ใจ

- ถ้า reproduce ไม่ได้ → หยุด หาวิธีจนกว่าจะได้
- บันทึก precondition, input, environment, steps ที่แน่ชัด
- reproduce ใน local/dev environment ก่อนคิด fix
- อย่า connect ไป production DB เพื่อ debug

#### Step 2 — Read the flow

- อ่านโค้ดทั้งฟังก์ชัน/controller ตั้งแต่ต้นจนจบ
- ยังไม่ต้องแก้ — แค่ทำความเข้าใจ data flow และ intent
- เช็ค caller → callee chain — อย่าดูแค่จุดที่สงสัย

#### Step 3 — Isolate root cause

- ถาม "ทำไม?" ซ้ำจนกว่าจะเจอสาเหตุจริง (5 Whys)
- hypothesis ต้อง specific — "เพราะ type mismatch ที่ line 42" ไม่ใช่ "เพราะ input มันผิด"
- แยก symptom ออกจาก root cause ให้ชัด

#### Step 4 — Build feedback signal

- สร้าง test / scenario ที่ทำให้ bug โผล่ชัดเจน
- signal ต้อง quick — กดครั้งเดียวรู้ว่าผ่านหรือไม่
- ใช้ signal นี้เพื่อยืนยันว่า fix ได้ผล

#### Step 5 — Fix with minimal diff

- แก้เฉพาะสาเหตุ — อย่าแตะของที่ไม่เกี่ยวกับ bug
- 1 commit = 1 fix — ถ้าต้องแก้หลายสาเหตุ → แยกกัน
- ห้าม refactor, rename, reformat ใน commit เดียวกับ fix

#### Step 6 — Verify

- ใช้ feedback signal จาก step 4 ยืนยัน bug หาย
- ตรวจ regression — ของอื่นที่เกี่ยวข้องยังทำงานปกติไหม
- เช็คว่าไม่มี debug code, log, หรือ config หลงเหลือ

#### Step 7 — Learn (optional but recommended)

- ถ้า recurring pattern → เพิ่ม guard / test / validation ป้องกัน
- บันทึกสั้นๆ ว่าสาเหตุคืออะไร (ถ้าช่วยให้ debug เร็วขึ้นในอนาคต)

### ข้อควรจำ

- Shotgun debugging (เดา → แก้ → ดู → เดาใหม่) = ห้ามเด็ดขาด
- ถ้า root cause ตีความได้หลายทาง → ถาม user เป็น options
- ถ้าโค้ดซับซ้อนเกินกว่าจะ isolate ได้ → spawn subagent ช่วยวิเคราะห์
- Debug นานเกิน 30 นาทีโดยไม่มีความคืบหน้า → หยุด ถาม user

## Unclear requirements → Grill

1 question at a time, multiple-choice options, stop at 95% confidence

## Plan → Execute

1. Break into small, verifiable steps
2. Each step = ทำอย่างเดียว
3. Verify each step before next
4. เมื่อเริ่มเขียนโค้ดจริง → เปิดใช้ `code` skill (Human Coding) — ขั้นตอนการเขียน
