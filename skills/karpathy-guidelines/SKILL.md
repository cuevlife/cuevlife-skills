---
name: karpathy-guidelines
description: Behavioral coding guidelines inspired by Andrej Karpathy — think before coding, simplicity first, surgical changes, goal-driven execution. Use ONLY before any coding task to reduce overengineering and assumptions. ใช้ก่อนเริ่มเขียนโค้ดทุกครั้งเพื่อลด overengineering
---

# Karpathy Coding Guidelines

Behavioral guidelines to reduce common LLM coding mistakes.
Based on Andrej Karpathy's observations on LLM coding pitfalls.

**Tradeoff:** These guidelines bias toward caution over speed. For trivial tasks, use judgment.

## 1. Think Before Coding

**Don't assume. Don't hide confusion. Surface tradeoffs.**

Before implementing:
- State your assumptions explicitly. If uncertain, ask.
- If multiple interpretations exist, present them — don't pick silently.
- If a simpler approach exists, say so. Push back when warranted.
- If something is unclear, stop. Name what's confusing. Ask.

## 2. Simplicity First

**Minimum code that solves the problem. Nothing speculative.**

- No features beyond what was asked.
- No abstractions for single-use code.
- No "flexibility" or "configurability" that wasn't requested.
- No error handling for impossible scenarios.
- If you write 200 lines and it could be 50, rewrite it.

Ask yourself: "Would a senior engineer say this is overcomplicated?" If yes, simplify.

## 3. Surgical Changes

**Touch only what you must. Clean up only your own mess.**

When editing existing code:
- Don't "improve" adjacent code, comments, or formatting.
- Don't refactor things that aren't broken.
- Match existing style, even if you'd do it differently.
- If you notice unrelated dead code, mention it — don't delete it.

When your changes create orphans:
- Remove imports/variables/functions that YOUR changes made unused.
- Don't remove pre-existing dead code unless asked.

The test: Every changed line should trace directly to the user's request.

## 4. Goal-Driven Execution

**Define success criteria. Loop until verified.**

Transform tasks into verifiable goals:
- "Add validation" → "Write tests for invalid inputs, then make them pass"
- "Fix the bug" → "Write a test that reproduces it, then make it pass"
- "Refactor X" → "Ensure tests pass before and after"

For multi-step tasks, state a brief plan:
```
1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]
```

Strong success criteria let you loop independently. Weak criteria ("make it work") require constant clarification.

---

## ใช้ Subagent สำหรับ Research / Explore

**Mode: BACKGROUND (ไม่ต้องรอ หรือ SYNC ก็ได้)** — main agent เริ่มคิด plan ไปก่อน, subagent หาข้อมูล, ได้ข้อมูลมาปุ๊บ main agent ปรับ plan หรือ implement ต่อ

### ทำไมต้องใช้ subagent

"Think before coding" — ส่วนหนึ่งของการคิดคือการหาข้อมูล:
- ค้นหา library / API docs ที่เกี่ยวข้อง
- explore codebase เพิ่มเติม
- ตรวจสอบว่ามี approach อะไรที่ใช้ได้

main agent ควร delegate การ explore/research ไป subagent เพื่อไม่ให้ context main chat พัง

### Subagent Prompt Template

```
ใช้ karpathy-guidelines skill
เป็น role: researcher
สำหรับ task: {what we need to build/fix}

Before implementation:
1. Search codebase for existing solutions (grep, glob)
2. Search docs for relevant API/library usage
3. Check for 3 approaches — simplest first
4. Identify potential pitfalls

Report:
- What already exists (reuse opportunity)
- Approach recommendation (with reason)
- Complexity estimate (simple / medium / complex)
- RED FLAG: ถ้า task ใหญ่กว่าที่คิด
```

### Workflow

```
ก่อน implement:
  main agent:
    1. คิด + state assumptions
    2. delegate research → subagent (karpathy-guidelines)
    3. subagent ส่ง: reuse + approach + complexity
    4. main agent ตัดสินใจ → implement
```

---

**These guidelines are working if:** fewer unnecessary changes in diffs, fewer rewrites due to overcomplication, and clarifying questions come before implementation rather than after mistakes.