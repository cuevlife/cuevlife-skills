---
name: git-style
description: Git workflow สไตล์คนทำงานจริง — ห้าม auto-push, push เมื่อ test ผ่านเท่านั้น, ติด loop bug ห้าม push. Use ONLY when user wants to commit, push, branch, merge, PR, or any git operation
---

# Git Style — สไตล์คนทำงานจริง

---

## กฎเหล็กห้ามละเมิด

### 🚫 ห้าม auto-push เด็ดขาด

**AI ห้าม push, commit with push, หรือ suggest auto-push เอง** — ผู้ใช้เท่านั้นที่เป็นคนสั่ง push

### ✅ Push ได้เมื่อเงื่อนไขนี้ครบ

1. **code ใช้งานได้** — รันจริงแล้วไม่พัง หรือ
2. **test ผ่าน** — run test suite แล้วไม่มี fail
3. **ไม่ error ตกค้าง** — ไม่มี known bug, ไม่มี TODO/FIXME ที่ไม่รู้ทางแก้
4. **diff ผ่านการตรวจ** — รู้ว่าอะไรเปลี่ยนก่อน push

### 🛑 ถ้าติด loop bug — ห้าม push

- ถ้ากำลัง debug, ลองไปลองมา, ยังไม่เจอ root cause → **ห้าม push อย่างเด็ดขาด**
- commit `wip:` ได้ แต่ **ห้าม push** จนกว่าบั๊กจะถูกแก้จริง
- ติด loop → หยุดถามผู้ใช้ก่อน อย่า push แล้วมาบอกทีหลัง

### 📋 ขั้นตอนก่อน push ทุกครั้ง

```
1. git diff --stat         ← ดูว่าอะไรเปลี่ยนบ้าง
2. git log --oneline -3    ← ดู commit ล่าสุด
3. รัน test หรือรัน app     ← ตรวจว่ามันทำงาน
4. เช็คว่าไม่ error         ← ไม่มี known bug ค้าง
5. ถ้าทุกอย่าง OK → รอผู้ใช้สั่ง push
```

---

## หลักการ

### 1. Commit บ่อย — commit เล็ก

- 1 commit = 1 ความเปลี่ยนแปลง logical
- `wip:` commit ได้ แต่**ห้าม push** commit wip
- ยิ่ง commit บ่อย = ยิ่ง revert ได้ง่าย = ยิ่งปลอดภัย

### 2. Commit Message

```
ภาษา: ไทยหรืออังกฤษ
โครงสร้าง: สั้น กระชับ บอกว่าทำอะไร

[type]: สิ่งที่ทำ

type: fix | feat | refactor | docs | style | wip
```

**ตัวอย่าง:**
```
fix: แก้ login ไม่ทำงานตอน network error
feat: เพิ่ม export PDF
refactor: ย้าย db query ออกจาก controller
wip: ยังไม่เสร็จ กลับมาทำต่อ
```

### 3. Branch Strategy (KISS)

```
main      = ใช้งานได้จริง, deploy แล้ว
dev       = รวมของที่กำลังทำ (optional ถ้าทำคนเดียว)
feature/* = สร้างเฉพาะตอนที่มันใหญ่จริงๆ
fix/*     = แก้บั๊ก
```

- ถ้าทำคนเดียว → commit เข้า main ตรงๆ ได้ (แต่ห้าม auto-push)
- ลบ branch ทิ้งเมื่อ merge แล้ว

### 4. Merge, ไม่ rebase (ยกเว้นจำเป็น)

- **merge commit** = ปลอดภัย, รู้ว่ามาจาก branch ไหน
- **rebase** = เฉพาะตอน branch แตกจาก main นานไป
- ถ้าไม่แน่ใจ → ใช้ merge

### 5. .gitignore — ตั้งแต่แรก

- ใส่ `.env`, `node_modules/`, `vendor/`, `build/`, `dist/`, `*.log`
- ถ้าลืม → `git add .gitignore && git commit -m "fix: add gitignore"`

### 6. Stash = เพื่อนซี้

```
git stash        # เก็บของที่กำลังทำไว้ก่อน
git stash pop    # เอาคืนมา
```

ใช้ตอน: ต้องไปแก้ branch อื่นด่วน แต่ของปัจจุบันยัง commit ไม่ได้

### 7. Undo สไตล์คนไม่ตื่นเต้น

```
git checkout -- file          # เลิกแก้ไฟล์นี้ (ยังไม่ commit)
git add . && git commit --amend  # แก้ commit ล่าสุด (ยังไม่ push)
git revert HEAD               # เอาคืน commit ล่าสุด (push แล้ว)
```

### 8. Diff ก่อน push เสมอ

```bash
git diff --stat
git log --oneline -5
```

---

## Command Cheatsheet

| สถานการณ์ | คำสั่ง |
|-----------|--------|
| เริ่ม project | `git init` |
| แก้แล้ว commit | `git add . && git commit -m "fix: ..."` |
| push | **รอผู้ใช้สั่งเท่านั้น** |
| ดึงของล่าสุด | `git pull` |
| สร้าง branch | `git checkout -b feature/xxx` |
| merge branch | `git checkout main && git merge feature/xxx` |
| ดู diff | `git diff --stat` |
| ดู log | `git log --oneline -10` |
| stash | `git stash` |
| เอาคืน | `git stash pop` |

---

## ใช้ Subagent สำหรับ Git Ops

**Mode: SYNC (ต้องรอ)** — ต้องได้ review result ก่อน main agent ถึง commit/push ได้

### ทำไมต้องใช้ subagent

git operations กิน context:
- `git diff` — ดูว่าอะไรเปลี่ยน
- `git status` — ดู staged/unstaged
- `git log` — ดู history
- `git stash` — เก็บของก่อน

main agent ควรรัน git ง่ายๆ เอง (commit, add) แต่ใช้ subagent สำหรับ:
- review diff ก่อน commit (เช็ค secrets, accidental changes)
- ดูว่า conflict หรือไม่
- generate commit message จาก diff
- ตรวจ test ผ่านก่อนสั่ง push

### Subagent Prompt Template

```
ใช้ git-style skill
เป็น role: git reviewer

Tasks:
1. git status → ดู staged/unstaged
2. git diff --stat → summary changes
3. git diff → full diff (เฉพาะ staged ถ้ามี)
4. git log --oneline -5 → recent history

Review:
- มี secrets, keys, .env ติดมาหรือไม่
- มีไฟล์ที่ตั้งใจเปลี่ยนหรือไม่
- diff สมเหตุสมผลกับ task หรือไม่
- มี file conflict หรือไม่
- test ผ่านหรือไม่

Report:
- files changed
- summary of changes
- RED FLAG: ถ้าเจอ secrets / .env / unintended changes
- commit message suggestion (สั้น กระชับ)
- test status
- push ready? YES / NO (NO = ต้องแก้ก่อน)
```

### Workflow

```
ก่อน commit:
  main agent → subagent (git reviewer)
    → report: clean / RED FLAG
    → main agent commit (ถ้า clean)

ก่อน push:
  main agent → subagent (test runner)
    → run tests
    → report: PASS / FAIL
    → main agent push (ถ้า PASS)
```

**ห้าม subagent commit หรือ push — main agent เท่านั้น**

---

**สรุป:** Git ไม่ใช่ศาสนา — เป็นแค่เครื่องมือ Commit บ่อย, message สั้น, branch ง่าย, **ไม่ rebase ถ้าไม่จำเป็น, และที่สำคัญที่สุด — ห้าม auto-push, push เฉพาะเมื่อพร้อม**