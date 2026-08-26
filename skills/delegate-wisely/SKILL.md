---
name: delegate-wisely
description: ใช้ subagent ให้มีประสิทธิภาพ — งานไหนเหมาะ (research/explore/review/isolated output), เขียน prompt ไม่ให้เพี้ยน, ข้อจำกัด ใช้เมื่อต้องมอบหมายงานย่อยให้ subagent
---

# Delegate Wisely — Subagent Usage Guide

## Objective

ใช้ subagent ทำงาน parallel/isolated ให้ได้ผลลัพธ์แม่นยำโดยไม่เปลือง main context — รู้ว่างานไหนควร delegate, เขียน prompt ยังไงไม่เพี้ยน, verify ผลลัพธ์ยังไง

## งานที่ใช้บ่อยจริง (จากคนทั่วโลก)

| # | งาน | ตัวอย่าง |
|---|-----|---------|
| 1 | **Research/สำรวจโค้ดเบส** | อ่าน 10+ ไฟล์ โดยไม่ให้ main context อุดตัน |
| 2 | **Code review / verification** | ใช้ context ใหม่ ไม่ bias อย่างคนเขียน |
| 3 | **เขียน/รันเทสต์** | งาน noisy ให้คืนแค่ summary |
| 4 | **Parallel แก้หลายไฟล์อิสระ** | แต่ละตัวคนละไฟล์/คนละ domain |
| 5 | **Security audit** | ตัวอ่านอย่างเดียว บังคับก่อน commit |
| 6 | **Docs/dependency lookup** | คืน summary สั้นๆ |

## Steps/Workflow

1. **ตัดสินใจว่าเหมาะไหม** → verify: งานเป็น research/explore/review/output จำนวนมาก? ไม่ต้อง context ต่อเนื่อง? → ถ้าใช่ delegate, ถ้าไม่ใช่ทำเอง
2. **ตั้งชื่อ/เลือก agent แบบ job-shaped** → verify: `repo-explorer`/`code-reviewer` (ตามบทบาทงาน) ✅ — หลีกเลี่ยง persona กว้างๆ `frontend-engineer` ❌ (route ได้แม่นกว่า)
3. **เขียน prompt ละเอียด** → verify: มี objective + scope แคบ + output format + แหล่งข้อมูล + ข้อห้าม ครบ 5
4. **จำกัด tools (whitelist)** → verify: ระบุ tools ที่จำเป็น — ไม่ระบุ = ได้ทุก tool รวม MCP (tool sprawl)
5. **Spawn parallel ถ้าอิสระ** → verify: แต่ละตัวทำงานคนละไฟล์/คนละส่วน — ไฟล์เดียวกันห้าม writer 2 คน
6. **Verify ผลลัพธ์** → verify: summary ตรง objective, งานสำคัญ review อีกครั้ง — test เขียว ≠ งานถูก

## Examples

```
❌ "ช่วยดูหน่อยว่าโค้ดมีปัญหาอะไร"          → กว้างเกิน subagent เดา คืนข้อมูลรก
✅ "ค้นหาใน app/Controllers/ ว่าใครเรียก CompanyService::create
    นอก CompanyController → return: ไฟล์ + บรรทัด + เหตุผลที่สงสัย — read-only เท่านั้น"

❌ งานแก้ bug หลายไฟล์ต้องเห็นภาพรวม        → subagent stateless ไม่เห็น history → พลาด
✅ งานต่อเนื่อง (plan → implement → test)  → ทำใน main agent
   subagent เอาไว้ research/explore/review เท่านั้น

❌ spawn 5 ตัวหาคำตอบเดียวกัน              → ซ้ำกัน เสีย token ~15x
✅ แบ่ง domain: ตัว A หา auth, ตัว B หา DB, ตัว C หา API → แต่ละตัวคืน summary สั้น

❌ สร้าง 10 subagent ตั้งแต่แรก            → flooding ทำให้ auto-delegation เพี้ยน
✅ เริ่ม 2-3 ตัวก่อน — เพิ่มเมื่องานพิสูจน์แล้วว่าต้อง
```

## Lessons Learned (จากคนที่ใช้จริง)

- เริ่ม subagent น้อยๆ (2-3 ตัว) — เยอะเกิน = auto-delegation เพี้ยน
- State ต้องอยู่นอก session — session ตายได้ งานต้องไม่ตายตาม (plan/tracker เป็น system of record)
- Test เขียว ≠ งานถูก — ต้อง second-opinion review + หลักฐาน (screenshot, test output)
- Agent ขยาย scope เอง — ส่งงานทุกครั้งถาม "อะไรคือเวอร์ชันที่ง่ายกว่านี้?"
- งาน dependent/sequential หรือเล็กๆ → อยู่ main session ดีกว่า (overhead setup + tokens ไม่คุ้ม)
- swarm มีค่าใช้จ่ายจริง (20 agents/สัปดาห์ ≈ $6,000) — ใช้เฉพาะงานที่ value สูง

## Constraints

- ห้าม delegate งานที่ต้อง context ต่อเนื่อง / iterative refinement
- ห้าม delegate การตัดสินใจที่ user ต้อง approve
- ห้าม spawn โดยไม่มี division of labor — งานซ้ำกัน = เสีย token
- ห้ามให้ subagent 2 ตัวแก้ไฟล์เดียวกัน (writer collision = งานหาย)
- ห้ามให้ subagent คุยกันเองผ่าน orchestrator หลายรอบ (game of telephone) — ให้คืน summary ตรงๆ
- ห้ามใช้กับงานสั้นๆ เร็วๆ — start fresh + gather context ช้ากว่าทำเอง
- ห้ามเชื่อผล subagent 100% — ต้อง verify/spot-check งานสำคัญ
- ห้ามให้ subagent คืนผลละเอียดหมด — บังคับ summary เพื่อประหยัด main context
- ห้ามละเว้นบอก scope — prompt กำกวม = ผลลัพธ์เพี้ยน
- ห้ามไม่ระบุ tools — จำกัด whitelist เสมอ
