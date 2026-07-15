---
name: human-coding-style
description: เขียนโค้ดเหมือน dev จริง — scan project ก่อน, ใช้ของที่มีก่อนเสมอ, ถ้าไม่มีค่อยสร้าง. Use ONLY before writing code in an existing project — check existing patterns, imports, and conventions first
---

# Human Coding Style

หลักการเหล่านี้มีมาก่อน AI หลายสิบปี — เป็นสิ่งที่ dev จริงใช้กันทุกวัน แต่มักถูกละเลยตอนใช้ AI เขียนโค้ด

---

## หลักการอ้างอิง

| Acronym | หลักการ | มาจาก | ปี |
|---------|---------|-------|-----|
| **DRY** | Don't Repeat Yourself — ความรู้ทุกชิ้นต้องมีที่เดียว | The Pragmatic Programmer | 1999 |
| **YAGNI** | You Aren't Gonna Need It — อย่าเขียนเผื่อที่ยังไม่ต้องใช้ | Extreme Programming | 1999 |
| **POLA** | Principle of Least Astonishment — ทำตามที่ project นี้คาดหวัง | PL/I → Unix Philosophy | 1970s |
| **AHA** | Avoid Hasty Abstractions — อย่าสร้าง abstraction เร็วเกินไป | Sandi Metz / Kent C. Dodds | 2010s |
| **DTSTTCPW** | Do The Simplest Thing That Could Possibly Work | XP (Kent Beck) | 1999 |
| **SSOT/SPOT** | Single Source of Truth — data normalization | Database theory | 1970s |
| **SoC** | Separation of Concerns — แต่ละ module มีหน้าที่เดียว | Dijkstra | 1974 |
| **CoC** | Convention over Configuration — ตาม project เขาไว้ก่อน | Rails (DHH) | 2004 |

---

## กฎเหล็ก

### 1. ใช้ของที่มีก่อนเสมอ — ถ้าไม่มีค่อยสร้าง (DRY + SSOT)

ก่อนเขียนโค้ด 1 บรรทัด scan project:
- มี base layout / template ไหม
- มี component / helper / service อะไรอยู่แล้ว
- มี style tokens / theme variables ไหม
- project นี้ใช้ library / framework อะไร

**ถ้ามี → ใช้ของนั้น ไม่ต้องเขียนใหม่**

### 2. อย่าเขียนเผื่อที่ยังไม่ต้องใช้ (YAGNI + DTSTTCPW)

- ไม่สร้าง abstraction "เผื่ออนาคต"
- ไม่เพิ่ม "flexibility" หรือ "configurability" ที่ยังไม่ต้องใช้
- ไม่ error handling สำหรับ impossible scenarios
- **เริ่มจาก simplest thing ที่ใช้ได้ก่อน — แล้วค่อย refactor เมื่อจำเป็น**

### 3. ง่ายไว้ก่อน — ห้ามเล่นท่ายาก (KISS + POLA)

**ถ้าโครงสร้างที่เขียนดูงง / ยาก / ซับซ้อนเกินกว่าคนทั่วไปจะเข้าใจ → เสนอรูปแบบที่ง่ายกว่าเสมอ**

```
ผิด: Factory Pattern + Dependency Injection + interface เผื่อเปลี่ยน DB
     ทั้งที่ project ใช้ DB ตัวเดียวมา 5 ปี
ถูก: เรียก DB ตรงๆ ตามที่ project ทำกัน
```

กฏ:
- **มนุษย์ต้องอ่านโค้ดคุณได้** — ถ้าต้องใช้เวลาทำความเข้าใจ 5 นาที = ซับซ้อนเกิน
- เริ่มจาก simplest thing ที่ใช้ได้ก่อน
- abstraction, design pattern, layer เพิ่ม = มาเฉพาะเมื่อพิสูจน์แล้วว่า simple version ใช้ไม่ได้
- ถ้าผมเสนอ pattern ซับซ้อน → ทักถามได้ "ตรงนี้จำเป็นขนาดนั้นเลย?"

**จำไว้: โค้ดที่ maintain ง่าย ดีกว่าโค้ดที่สวย แต่ไม่มีใครกล้าแตะ**

### 4. ทำตามที่ project นี้คาดหวัง (POLA + CoC)

- project ใช้ camelCase → ใช้ camelCase
- project ใช้ error handling แบบไหน → ใช้แบบนั้น
- project ใช้ folder structure ยังไง → ตามนั้น
- **Local conventions ชนะ global best practices เสมอ**

### 5. Abstraction = Reward ของการ Reuse ไม่ใช่ Planning (AHA)

```
Step 1: เขียนให้ใช้ได้ก่อน
Step 2: ถ้า pattern ซ้ำ 2+ ครั้ง → extract เป็น component
Step 3: ถ้ายังไม่ซ้ำ → ปล่อยไว้ เขียนซ้ำไปก่อน
```

AHA = **A**void **H**asty **A**bstractions — abstraction ที่เกิดจากการ reuse จริงเท่านั้นถึงจะดี

### 6. แต่ละ module รับผิดชอบเรื่องเดียว (SoC)

- UI component → ไม่ควรมี business logic
- service layer → ไม่ควรรู้เรื่อง UI
- database query → ไม่ควรปนกับ presentation

### 7. Import ให้เป็นนิสัย — รู้จัก project vocabulary

```python
# ❌ AI-style: ไม่รู้ project
def get_users():
    conn = mysql.connect(...)

# ✅ Human-style: รู้จักของที่มีใน project
from app.database import db
def get_users():
    return db.query("SELECT * FROM users")
```

---

## ความสัมพันธ์กับสกิลอื่น

| ใช้คู่กับ | ได้ผล |
|----------|-------|
| **karpathy-guidelines** | human-coding-style = reuse + awareness, karpathy = วิธีคิด + ความเรียบร้อย — ครบ |
| **premium-design-system** | ตอนออกแบบ UI ให้ทำตาม design tokens ของ project |

---

## ใช้ Subagent สำหรับ Project Scan

**Mode: BACKGROUND (รอหรือไม่รอก็ได้)** — scan project ได้ parallel กับ main agent ที่คิด plan, main agent ได้ report ตอนไหนค่อย implement ต่อ

### ทำไมต้องใช้ subagent

"ใช้ของที่มีก่อน" — main agent ควร delegate การสำรวจ project ให้ subagent เพราะมันกิน context มาก (glob, grep, read หลายไฟล์) และ main agent ควร focus ที่ design/implementation

subagent scan สำหรับ:
1. ดู folder structure
2. ดู libraries/framework ที่ใช้ (composer.json, package.json)
3. ดู patterns ของ project (conventions, naming, structure)
4. ดูไฟล์ที่เกี่ยวข้องกับ task
5. ดูว่า project ใช้ helper / base class / component อะไร

### Subagent Prompt Template

```
ใช้ human-coding-style skill
เป็น role: project scout
สำหรับ task: {describe what we need to do}

Tasks:
1. Scan project structure (top-level + relevant subdirectories)
2. Check composer.json / package.json — libraries available
3. Find existing patterns ที่เกี่ยวข้อง:
   - controllers ใช้รูปแบบไหน
   - views ใช้ layout ไหม
   - models / services มีอะไร
   - routes อยู่ไฟล์ไหน
   - validation ทำที่ไหน
4. ถ้า task เกี่ยวกับ form → หา form ที่ใกล้เคียงที่สุด
5. ถ้า task เกี่ยวกับ API → หา API endpoint pattern

Report:
- project type + framework
- relevant files สำหรับ task นี้ (path + brief)
- patterns ที่ต้อง follow (naming, structure, imports)
- existing helpers / components ที่ reuse ได้
- RED FLAG: ถ้าไม่เจอ pattern ที่ match → บอกให้ main agent ตัดสินใจ
```

### Workflow

```
main agent:
  1. ได้ task จาก user
  2. delegate project scan → subagent (human-coding-style)
  3. subagent ส่ง report: structure + patterns + relevant files
  4. main agent ใช้ report นี้เป็น reference ตอน implement
  → ไม่ต้อง main agent เสียเวลา scan เอง
```

### รวมกับ karpathy-guidelines

```
ก่อน coding:
  1. karpathy-guidelines → คิดก่อนเขียน (main agent)
  2. human-coding-style → project scan (subagent)
  3. main agent implement โดยใช้ของที่มีแล้ว
```

---

**สรุป:** Dev จริงเขาไม่ได้คิดค้น wheel ใหม่ทุกครั้ง — เขารู้จัก codebase, ใช้ของที่มี, match patterns, และสร้างใหม่ตอนที่จำเป็นจริงๆ เท่านั้น