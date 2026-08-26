---
name: catch-up
description: สรุปบริบท/สถานะ/เหตุผลของโปรเจกต์ PicSqueeze (C:\Dev\mobileproject) ที่คุยกันมา (AdMob ban, AppLovin MAX, Play Console, roadmap) ใช้เมื่อทำงานในโปรเจกต์ PicSqueeze และเริ่มบทสนทนาใหม่ หรือ user ถามว่า "คุยอะไรไปแล้ว"/"ตอนนี้ถึงไหนแล้ว"/"สรุปให้หน่อย"
---

# Catch Up — PicSqueeze Project Recap

เฉพาะโปรเจกต์ **PicSqueeze** (`C:\Dev\mobileproject`) เท่านั้น — ถ้า working directory ปัจจุบันไม่ใช่โปรเจกต์นี้ ให้บอก user ตรงๆ ว่า skill นี้มีไว้สำหรับ PicSqueeze ไม่เกี่ยวกับโปรเจกต์ปัจจุบัน แล้วหยุด ไม่ต้องเดา

## Objective

ให้คุณกรานต์ (solodev เจ้าของ PicSqueeze) รู้สึกว่า Claude เข้าใจสถานะและเหตุผลเบื้องหลังของโปรเจกต์อยู่แล้ว ไม่ต้องอธิบายซ้ำทุกครั้ง

## Steps

1. อ่าน memory index ก่อน: `C:\Users\Kraniaz\.claude\projects\C--Dev-mobileproject\memory\MEMORY.md` แล้วเปิดไฟล์ memory ที่เกี่ยวข้องทั้งหมด (โดยเฉพาะ type: project และ feedback)
2. อ่าน `C:\Dev\mobileproject\GDD.md` ส่วน §2 (Current Status), §3b (Monetization Recovery Plan), §4 (AdMob Incident Log), §9 (Open Decisions) — ไฟล์นี้คือ source of truth ล่าสุดของสถานะจริง memory เป็นแค่บริบท/เหตุผลเสริม
3. เช็คว่า memory กับ GDD.md ตรงกันไหม — ถ้าไม่ตรง (เช่น user อัปเดตสถานะไปแล้วแต่ยังไม่ได้บอก Claude ตอนนี้) ให้ยึด GDD.md เป็นหลัก แล้วถามคุณกรานต์สั้นๆ ว่าจะให้ sync memory ให้ตรงไหม
4. สรุปให้คุณกรานต์แบบสั้น ตรงประเด็น (ภาษาไทย, ไม่น้ำ) ครอบคลุม:
   - อยู่ตรงไหนแล้ว (สถานะปัจจุบัน)
   - บล็อกอะไรอยู่ ถ้ามี พร้อม ETA ถ้ารู้
   - ขั้นตอนถัดไปที่ user ต้องทำเอง vs. ที่ Claude ทำต่อได้เลย
5. ห้ามถามคำถามที่คำตอบอยู่ใน memory/GDD.md อยู่แล้ว (เช่น "ทำไมเลิกใช้ AdMob" — คำตอบมีอยู่แล้วใน [[admob-ban-final]])

## เมื่อจบบทสนทนาที่มีการตัดสินใจ/เหตุผลใหม่

บันทึกลง memory ทันที (ไม่ต้องรอ user สั่ง) ตามกติกาใน `~/.claude/CLAUDE.md` เรื่อง memory types — โดยเฉพาะ **เหตุผล (why)** ไม่ใช่แค่ผลลัพธ์ แล้วอัปเดต `MEMORY.md` index ให้ตรง
