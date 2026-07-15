---
name: premium-design-system
description: Context-first design system — brand personality, typography, color palettes, spacing, and UI patterns for premium web/app design. Use ONLY when designing UI, defining brand identity, picking colors/fonts, or creating design tokens
---

# Premium Design System — Context-First

Premium ≠ หน้าตาแบบเดียว Premium = perfect fit for the brand

**Product UI (Linear/Stripe), fitness brand, yoga studio, designer portfolio — ทุกอย่าง premium ได้ ถ้าทุก choice มีเหตุผลและ consistent กับ brand personality**

---

## Core Framework: Brand Personality Axes

ก่อนเริ่มออกแบบ วาง brand บน 5 axes นี้:

| Axis | ← Pole | → Pole |
|------|--------|--------|
| **Tone** | Soft, organic, warm | Hard, industrial, cold |
| **Energy** | Calm, slow, intentional | Aggressive, fast, urgent |
| **Color** | Earth tones, muted pastels | High contrast, pure B&W, neons |
| **Space** | Generous whitespace, airy | Dense, gridded, information-packed |
| **Texture** | Smooth, clean, minimal | Raw grain, paper, concrete, grit |

**ตัวอย่าง:**
- Yoga studio → Soft + Calm + Earth + Airy + Smooth
- Fitness brand → Hard + Aggressive + High contrast + Dense + Grit
- Product UI (Linear) → Between soft & hard + Calm + High contrast + Airy + Smooth
- Designer portfolio → Hard + Calm + B&W + Sparse + Raw

**กฏข้อเดียว:** พอเลือก pole แล้วต้อง **consistent ทุก element** — ถ้าเลือก Hard แล้วใช้ border-radius 16px ทุกที่ = inconsistency

---

## Anti-Patterns (ใช้ได้ทุกบริบท)

สิ่งเหล่านี้ไม่มีทางดี ไม่ว่า brand จะเป็นอะไร:
- Gradients ที่ไม่จำเป็น (purple/blue AI slop)
- Glassmorphism / frosted glass ที่ decorative
- **Emoji ในงาน client work ทุกประเภท — emoji = AI slop flag เด่นที่สุด ใช้ตุยได้**
- Decorative illustrations ที่ไม่สื่อ brand meaning
- UI ที่รวมทุก trend โดยไม่มีความ consistent personality
- Stock photography ที่ generic (คนยิ้มทำงาน group high-five)

---

## Design Toolbox

### Typography

เลือก font ให้ตรงกับ brand personality:

| Brand Type | Heading | Body |
|------------|---------|------|
| Product UI | System sans (SF Pro, Inter) | System sans |
| Fitness / Street | Condensed sans (Oswald, Bebas) | Clean sans (Roboto, Inter) |
| Yoga / Wellness | Humanist serif (Mrs. Eaves, Lyon) | Lightweight sans (Proxima Nova) |
| Portfolio / Editorial | Oversized serif or display | Minimal sans or serif |
| Luxury | Elegant serif (Didot, Bodoni) | Thin sans |

**กฎ:**
- Maximum 2 typefaces ต่อ project
- Weight: 2-3 weights ก็พอ (อย่าใช้ทุกตัวที่มี)
- Letter-spacing: headings ของ brand ที่ hard/tight-content ใช้ negative tracking, brand ที่ soft/open ใช้ normal หรือ positive

### Color Palettes by Context

| Context | Primary | Accent | Vibe |
|---------|---------|--------|------|
| **Fitness** | Pure black, charcoal | Neon orange, silver | Aggressive, powerful |
| **Yoga** | Warm sand, sage | Terracotta, dusk pink | Grounded, warm |
| **Portfolio** | High-contrast B&W | Single chromatic accent | Editorial, focused |
| **Product UI** | White, #0d0d0d | Blue (#1a73e8) | Clean, trustworthy |
| **Luxury** | Cream, charcoal | Gold, deep navy | Refined, exclusive |

**กฎสี (สากล):**
- 1-2 accent colors max — accent ต้องสื่อ brand identity
- Gray scale สำหรับ hierarchy
- สีที่เลือกต้องมี contrast ผ่าน AA สำหรับ body
- Accent ต้องมี purpose (CTA, hover, active, link) — ไม่ใช่แค่แต่ง

### Border Radius by Context

| Context | Buttons | Cards | Overall feel |
|---------|---------|-------|-------------|
| Product UI | 8px | 8-12px | Rounded, approachable |
| Fitness | 0-2px | 0-4px | Sharp, aggressive |
| Yoga | 12-16px | 16-24px | Soft, organic |
| Portfolio | 0px | 0-8px | Editorial, sharp |
| Luxury | 4px | 8-12px | Refined, subtle |

**กฎ: radius = brand expression ถ้า brand aggressive / editorial → 0-2px ถ้า brand soft / organic → 12px+**

### Shadows by Context

| Context | Shadow style |
|---------|-------------|
| Product UI | Subtle offset shadow, low opacity — elevation metaphor |
| Fitness | No shadow หรือ heavy 1px hard shadow (no blur) — comic-book style |
| Yoga | No shadow หรือ very soft (large blur, low opacity) |
| Portfolio | No shadow ตกแต่ง → ใช้ ink trap หรือ hard line แทน |
| Luxury | Very subtle shadow หรือ no shadow → rely on material quality |

**กฎ: shadow ไม่จำเป็นต้องมีทุกที่ fitness brand ที่ aggressive ใช้ hard shadow ได้ดีกว่า offset soft**

### Spacing by Context

| Context | Density |
|---------|---------|
| Product UI | 8pt grid, generous whitespace |
| Fitness | Tighter — 4pt grid, high density, minimal whitespace |
| Yoga | Very generous — 8pt หรือ 12pt grid, lots of breathing room |
| Portfolio | Content-driven — asymmetric, editorial spacing |
| Luxury | Symmetrical, generous margins, centered |

**กฎ: ถ้า desire = "intense, information-packed" → spacing เล็ก / ถ้า desire = "calm, intentional" → spacing ใหญ่**

---

## Context-Adaptive Patterns

### Navigation
- **Product:** Top bar 48-56px, minimal, logo left, links right
- **Fitness:** Large bold logo, condensed links, full-width dark strip, mega menu
- **Yoga:** Minimal, centered logo, tertiary links, hamburger on desktop OK (soft feel)
- **Portfolio:** Hidden nav (hamburger or bottom-right persistent), let content lead

### Hero
- **Product:** Value prop + CTA centered, clean bg
- **Fitness:** Full-viewport video/image + oversized headline overlaid, low opacity overlay
- **Yoga:** Slow-breathing visual, 4-second expand/contract cycle, centered text
- **Portfolio:** Oversized typographic name treatment, kinetic animation

### Cards
- **Product:** Border-based, subtle, consistent padding
- **Fitness:** Dark bg, or no bg, sharp corners, image-led
- **Yoga:** Soft radius, generous padding, image + text
- **Portfolio:** Full-bleed image spreads, editorial captions

### Buttons / CTAs
- **Product:** 3-tier (solid/outline/link), 8px radius, scale feedback
- **Fitness:** Solid bold, all-caps, wide tracking, 0-2px radius, pulse glow animation
- **Yoga:** Soft solid or ghost, rounded, lowercase or title case, organic easing
- **Portfolio:** Text-only (link-style) or bare outline, type-driven

---

## Interaction Principles by Context

### Easing
- **Product / Yoga:** `cubic-bezier(0.16, 1, 0.3, 1)` — smooth, natural
- **Fitness:** Overshoot or elastic — `cubic-bezier(0.5, 1.5, 0.5, 1)` สำหรับ emphasis moments
- **Portfolio:** Variable — kinetic for hero, linear for scroll, stepped for type reveal

### Animation
- **Product:** Subtle scale hover, purposeful transitions only
- **Fitness:** Bold — section entry with staggered fade+translate, grain texture parallax, pulse on CTA
- **Yoga:** Soft fade-up 600ms, slow Ken Burns on image hover, breath cycle
- **Portfolio:** Kinetic typography on load, cross-fade spreads, logo build animation

### Cursor
- **Product:** Default cursor, pointer on clickable
- **Fitness:** Default
- **Yoga:** Default
- **Portfolio:** Custom cursor — risograph dot trail, cursor-following swatch

---

## Number Input UX (Live Comma Formatting)

สำหรับ input ตัวเลขที่ users ต้องกรอกจำนวนเงิน (principal, rate, fee):

### กฎ
- **ใช้ `type="text" inputmode="decimal"`** แทน `type="number"` — เพื่อให้รองรับ comma และ mobile keyboard แสดงทศนิยม
- **Live formatting ขณะพิมพ์** — เพิ่ม comma ทุก 3 หลักอัตโนมัติ (5000 → 5,000)
- **Strip comma ก่อน submit** — ใช้ JS `.replace(/,/g, '')` ก่อนส่งค่าไป backend
- **Preview ต้อง parse commas ได้** ก่อนคำนวณ

### Implementation Pattern

```javascript
function formatLive(el) {
    var start = el.selectionStart;
    var raw = el.value.replace(/,/g, '').replace(/[^\d.]/g, '');
    var parts = raw.split('.');
    if (parts.length > 2) parts = [parts[0], parts.slice(1).join('')];
    var intPart = parts[0].replace(/^0+(\d)/, '$1');
    var formatted = intPart.replace(/\B(?=(\d{3})+(?!\d))/g, ',');
    if (parts.length > 1 && parts[1] !== undefined) formatted += '.' + parts[1];
    var rawCursor = start - ((el.value.slice(0, start).match(/,/g) || []).length);
    if (rawCursor < 0) rawCursor = 0;
    if (rawCursor > raw.length) rawCursor = raw.length;
    var newPos = raw.slice(0, rawCursor).replace(/\B(?=(\d{3})+(?!\d))/g, ',').length;
    el.value = formatted;
    el.setSelectionRange(Math.min(newPos, formatted.length), Math.min(newPos, formatted.length));
}
```

### Strip on Submit
```javascript
function stripCommas() {
    document.querySelectorAll('input[inputmode="decimal"]').forEach(function(el) {
        if (el.value) el.value = el.value.replace(/,/g, '');
    });
}
```

### เมื่อใดควรใช้
- จำนวนเงิน, เปอร์เซ็นต์, ตัวเลขที่มีโอกาสเกิน 1,000
- **ไม่ใช้** สำหรับ ID, phone, postal code, หรือเลขที่ไม่มี comma significance

---

## Modal Scrolling (Fixed Header + Footer)

เมื่อ modal มีเนื้อหาเยอะจนเกิน viewport (จอเล็ก, ฟิลด์เยอะ):

### โครงสร้าง

```html
<div id="modal" class="fixed inset-0 z-50 flex p-4">
  <!-- Backdrop -->
  <div class="fixed inset-0 bg-black/50" onclick="closeModal()"></div>

  <!-- Card: max height 85vh, flex column -->
  <div class="relative w-full max-w-md m-auto bg-white rounded-2xl shadow-2xl
              flex flex-col max-h-[85vh]">

    <!-- Header: shrink-0 (ไม่หด) -->
    <div class="shrink-0 px-6 pt-6 pb-3">
      <h2 class="text-[17px] font-semibold">หัวข้อ</h2>
    </div>

    <!-- Body: overflow-y-auto (เลื่อนได้) -->
    <div class="overflow-y-auto px-6">
      <form>...เนื้อหาเยอะๆ...</form>
    </div>

    <!-- Footer buttons: shrink-0 (ไม่หด) -->
    <div class="flex gap-2 shrink-0 px-6 pb-6 pt-3">
      <button>ยกเลิก</button>
      <button>ยืนยัน</button>
    </div>

  </div>
</div>
```

### กฎ
- **ห้าม** ใช้ `overflow-y-auto` ครอบ modal ภายนอก (ทำให้ backdrop เลื่อนไม่ติดที่)
- ใช้ `flex flex-col` + `max-h-[85vh]` บน card แทน
- ส่วนหัว + ปุ่ม = `shrink-0`
- ส่วนเนื้อหา = `overflow-y-auto`
- backdrop = `fixed inset-0` (ไม่เกี่ยวข้องกับ scroll)
- `85vh` เป็นค่าที่ safe ที่สุดสำหรับ mobile + desktop

---

## Split Input (Input + Display Group)

เมื่อต้องการ input ที่แบ่งเป็น 2 ส่วน: ด้านซ้ายกรอกค่า, ด้านขวาแสดงผลลัพธ์/หน่วย

### โครงสร้าง

```html
<div class="flex rounded-xl border border-black/10 dark:border-white/20 overflow-hidden">
    <input type="text" inputmode="decimal" class="flex-1 min-w-0 px-3 py-2.5 bg-white dark:bg-[#1C1C1E] border-0 rounded-none text-[13px] outline-none shadow-none appearance-none focus:ring-2 focus:ring-inset focus:ring-apple-blue/40">
    <span class="flex items-center px-3 py-2.5 bg-black/[0.02] dark:bg-white/[0.04] text-[12px] shrink-0 border-l border-black/10 dark:border-white/20">
        ผลลัพธ์
    </span>
</div>
```

### CSS สำรอง (กัน browser ตก)\nถ้า Tailwind ไม่จัดการ border-radius หรือ box-shadow ของ `<input>` ให้เพิ่ม CSS นี้:

```css
.split-input input { border-radius: 0 !important; box-shadow: none !important; -webkit-appearance: none; -moz-appearance: none; appearance: none; }
```

แล้วเพิ่ม class `split-input` ที่ flex container

### กฎ
- Input: `border-0 rounded-none appearance-none` + `bg-white` (เท่ากับพื้นหลัง card)
- Display: `border-l` เส้นแบ่ง | `bg-black/[0.02]` ตัดพื้นให้ต่างนิด
- Container: `rounded-xl border overflow-hidden` ตัดมุมทั้งหมดด้วย container เดียว
- `flex-1 min-w-0` ให้ input ยืดเต็มที่, `shrink-0` ให้ display ไม่หด
- `!important` ใน CSS สำรอง จำเป็นเพราะ browser บางตัวมี `border-radius` ติดมากับ `<input>` ตาม default
- ใช้ `focus:ring-2 focus:ring-inset` แทน `outline` เพราะ `overflow-hidden` ตัด outline ได้

---

## Flatpickr Premium Calendar

Custom month/year dropdown selectors สำหรับ flatpickr — รองรับไทย, ปี พ.ศ., dark mode, glassmorphism

### Files

| File | Description |
|------|-------------|
| `examples/flatpickr-premium.html` | Standalone reference page (see it live) |
| (copy CSS + JS จาก HTML นั้นไปใช้ใน project) | |

### Key Features

- **Premium header** — ซ่อน default flatpickr header, ใช้ custom dropdown แทน
- **ภาษาไทย** — ใช้ `flatpickr/dist/l10n/th.js`, แสดงชื่อเดือนไทย + วัน "จ อ พฤ ศ ส อา"
- **ปี พ.ศ.** — แสดง `ค.ศ. + 543` (2026 → 2569)
- **Navigation arrows** — `< prev` / `next >` ปุ่มเปลี่ยนเดือน
- **Glassmorphism** — backdrop-blur, rounded-2xl, apple-style shadow
- **Dark mode** — `.dark` class support
- **ปรับ accent color** — เปลี่ยน `#007AFF` และ `rgba(0,122,255,...)` เป็นสีธีมคุณ

### Implementation Steps

**1. CDN:**
```html
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/flatpickr/dist/flatpickr.min.css">
<script src="https://cdn.jsdelivr.net/npm/flatpickr"></script>
<script src="https://cdn.jsdelivr.net/npm/flatpickr/dist/l10n/th.js"></script>
```

**2. Input:**
```html
<input type="text" class="js-date" value="<?= date('Y-m-d') ?>">
```

**3. CSS:** ก็อป `/* FLATPICKR PREMIUM — START COPY HERE */` block จาก `examples/flatpickr-premium.html`

**4. JavaScript:** ก็อป `createPremiumSelect` helper + `flatpickr(...)` init จาก example

### Accent Color Cheatsheet

| Theme | CSS values |
|-------|-----------|
| Apple Blue | `#007AFF`, `rgba(0,122,255,...)` |
| Apple Green | `#34C759`, `rgba(52,199,89,...)` |
| Apple Red | `#FF3B30`, `rgba(255,59,48,...)` |
| Purple | `#AF52DE`, `rgba(175,82,222,...)` |

### Variants

```javascript
// Single date (default)
flatpickr('.js-date', { onReady: premiumOnReady });

// Date range
flatpickr('#date-range', { mode: 'range', maxDate: new Date(), onReady: premiumOnReady });

// Disable future dates
flatpickr('.js-date', { maxDate: new Date(), onReady: premiumOnReady });
```


---

## UI Components

Components ready-to-use ใน `examples/components.html`:

| Component | Description | JS |
|-----------|-------------|----|
| **Custom Select** | Dropdown แทน native `<select>` ตรงธีมทุก pixel | Vanilla |
| **Toggle Switch** | iOS-style toggle animation smooth | Vanilla |
| **Checkbox / Radio** | Custom styled checkbox + radio button | Vanilla |
| **Button Variants** | primary / secondary / danger / ghost + sizes (sm/normal/lg) | CSS |
| **Form Inputs** | text, number, select, textarea พร้อม field-label | CSS |
| **Badges / Tags** | สถานะสีต่างๆ (blue/green/red/orange/purple) | CSS |
| **Tabs** | Tab navigation พร้อม active indicator | Vanilla |
| **Table** | Premium table กับ glass card wrapper | CSS |
| **Alerts** | success / error / warning / info | CSS |
| **Search Bar** | Input พร้อม search icon | Vanilla |
| **Split Input** | Input + label ด้านข้าง (บาท, % ฯลฯ) | CSS |
| **Avatar** | User avatar — initials or `<img>`, 3 sizes, custom colors | CSS |
| **Empty State** | Empty data placeholder with icon + button | CSS |
| **Skeleton** | Loading shimmer placeholder | CSS |
| **Accordion** | Collapsible sections, single open | Vanilla |
| **Pagination** | Page buttons + per-page select | CSS |
| **Dropdown Menu** | Action menu with icons, danger variant | Vanilla |
| **Modal** | Overlay dialog with backdrop blur, scrollable body | Vanilla |
| **Toast** | Slide-in notification, success/error/warning/info | Vanilla |

### Import Components

```html
<!-- CSS components block (copy from `examples/components.html` <style>) -->
<link rel="stylesheet" href="path/to/components.css">

<!-- ไม่ต้องใช้ Alpine — components ใช้ Vanilla JS ทั้งหมด -->
```

### Vanilla JS — ฟังก์ชัน共用 (ใส่ก่อน `</body>`)

```javascript
// ═══ Custom Select ═══
(function() {
    document.addEventListener('click', function(e) {
        var sel = e.target.closest('.custom-select');
        document.querySelectorAll('.custom-select.open').forEach(function(el) {
            if (el !== sel) el.classList.remove('open');
        });
    });
    document.addEventListener('click', function(e) {
        var item = e.target.closest('.custom-select-item');
        if (!item) return;
        var sel = item.closest('.custom-select');
        if (!sel) return;
        var value = item.dataset.value;
        var label = sel.querySelector('.custom-select-label');
        var hidden = sel.querySelector('input[type="hidden"]');
        if (!hidden) {
            hidden = document.createElement('input');
            hidden.type = 'hidden';
            hidden.name = sel.dataset.name || '';
            sel.appendChild(hidden);
        }
        hidden.value = value;
        label.textContent = item.querySelector('span') ? item.querySelector('span').textContent : item.textContent;
        sel.querySelectorAll('.custom-select-item').forEach(function(i) {
            i.classList.remove('selected');
            var c = i.querySelector('.bi-check2'); if (c) c.remove();
        });
        item.classList.add('selected');
        var check = item.querySelector('.bi-check2') || document.createElement('i');
        if (!check.classList.contains('bi-check2')) { check.className = 'bi bi-check2 text-xs'; item.appendChild(check); }
        sel.classList.remove('open');
    });
    document.querySelectorAll('.custom-select-trigger').forEach(function(el) {
        el.addEventListener('click', function(e) {
            e.stopPropagation();
            var sel = el.closest('.custom-select');
            if (!sel) return;
            var wasOpen = sel.classList.contains('open');
            document.querySelectorAll('.custom-select.open').forEach(function(s) { s.classList.remove('open'); });
            if (!wasOpen) sel.classList.add('open');
        });
    });
})();

// ═══ Toast ═══
function showToast(type, title, msg) {
    var c = document.getElementById('toast-container');
    if (!c) return;
    var colors = { success: '#34C759', error: '#FF3B30', warning: '#FF9500', info: '#007AFF' };
    var icons = { success: 'bi-check-circle-fill', error: 'bi-x-circle-fill', warning: 'bi-exclamation-triangle-fill', info: 'bi-info-circle-fill' };
    var t = document.createElement('div');
    t.className = 'toast';
    t.innerHTML = '<i class="bi ' + icons[type] + ' toast-icon" style="color:' + colors[type] + '"></i><div class="toast-body"><div class="toast-title">' + title + '</div><div class="toast-msg">' + msg + '</div></div>';
    c.appendChild(t);
    requestAnimationFrame(function() { t.classList.add('show'); });
    setTimeout(function() { t.classList.remove('show'); setTimeout(function() { t.remove(); }, 300); }, 3500);
}

// ═══ Action Menu Close ═══
function closeActionMenus() {
    document.querySelectorAll('.action-menu.open').forEach(function(m) { m.classList.remove('open'); });
}
document.addEventListener('click', closeActionMenus);

// ═══ Tabs ═══
function switchTab(btn, tab) {
    var parent = btn.closest('[data-tabs]');
    if (!parent) return;
    parent.querySelectorAll('.tab-btn').forEach(function(b) { b.classList.remove('active'); });
    btn.classList.add('active');
    parent.querySelectorAll('[data-tab-content]').forEach(function(c) { c.hidden = c.dataset.tabContent !== tab; });
}
```

### Custom Select — Quick Reference

**HTML:**
```html
<div class="custom-select" data-name="field_name">
    <div class="custom-select-trigger">
        <span class="custom-select-label">เลือก</span>
    </div>
    <div class="custom-select-menu">
        <div class="custom-select-item selected" data-value="a">
            <span>ตัวเลือก A</span><i class="bi bi-check2 text-xs"></i>
        </div>
        <div class="custom-select-item" data-value="b">
            <span>ตัวเลือก B</span>
        </div>
    </div>
</div>
```

**JS (ใส่ก่อน `</body>`):**
```javascript
// ═══ Custom Select (Vanilla JS) ═══
(function() {
    document.addEventListener('click', function(e) {
        var sel = e.target.closest('.custom-select');
        document.querySelectorAll('.custom-select.open').forEach(function(el) {
            if (el !== sel) el.classList.remove('open');
        });
    });

    document.addEventListener('click', function(e) {
        var item = e.target.closest('.custom-select-item');
        if (!item) return;
        var sel = item.closest('.custom-select');
        if (!sel) return;
        var value = item.dataset.value;
        var label = sel.querySelector('.custom-select-label');
        var hidden = sel.querySelector('input[type="hidden"]');
        if (!hidden) {
            hidden = document.createElement('input');
            hidden.type = 'hidden';
            hidden.name = sel.dataset.name || '';
            sel.appendChild(hidden);
        }
        hidden.value = value;
        label.textContent = item.querySelector('span') ? item.querySelector('span').textContent : item.textContent;
        sel.querySelectorAll('.custom-select-item').forEach(function(i) {
            i.classList.remove('selected');
            var c = i.querySelector('.bi-check2'); if (c) c.remove();
        });
        item.classList.add('selected');
        var check = item.querySelector('.bi-check2') || document.createElement('i');
        if (!check.classList.contains('bi-check2')) {
            check.className = 'bi bi-check2 text-xs';
            item.appendChild(check);
        }
        sel.classList.remove('open');
    });

    document.querySelectorAll('.custom-select-trigger').forEach(function(el) {
        el.addEventListener('click', function(e) {
            e.stopPropagation();
            var sel = el.closest('.custom-select');
            if (!sel) return;
            var wasOpen = sel.classList.contains('open');
            document.querySelectorAll('.custom-select.open').forEach(function(s) { s.classList.remove('open'); });
            if (!wasOpen) sel.classList.add('open');
        });
    });
})();
```

### Toggle Switch — Quick Reference

**HTML:**
```html
<div class="toggle" onclick="this.classList.toggle('active')">
    <div class="toggle-knob"></div>
</div>
```

### Tabs — Quick Reference

**HTML:**
```html
<div data-tabs>
    <div class="tab-bar">
        <button class="tab-btn active" data-tab="1" onclick="switchTab(this,'1')">แท็บ 1</button>
        <button class="tab-btn" data-tab="2" onclick="switchTab(this,'2')">แท็บ 2</button>
    </div>
    <div class="tab-content" data-tab-content="1">เนื้อหา 1</div>
    <div class="tab-content" data-tab-content="2" hidden>เนื้อหา 2</div>
</div>
```

**JS:**
```javascript
function switchTab(btn, tab) {
    var parent = btn.closest('[data-tabs]');
    parent.querySelectorAll('.tab-btn').forEach(function(b) { b.classList.remove('active'); });
    btn.classList.add('active');
    parent.querySelectorAll('[data-tab-content]').forEach(function(c) { c.hidden = c.dataset.tabContent !== tab; });
}
```
