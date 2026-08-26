---
name: css-first
description: เลือก CSS ล้วนก่อน JS เสมอสำหรับ animation, responsive sizing, layout, popup/dropdown positioning — ใช้เมื่อทำงาน UI/UX ที่เกี่ยวกับ visual behavior เพื่อเลือกวิธีที่ performant กว่าและบั๊กน้อยกว่า
---

# CSS-First — เลือก CSS ก่อน JS เสมอเมื่อทำได้

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
