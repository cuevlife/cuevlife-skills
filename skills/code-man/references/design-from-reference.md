# Design From Reference — ใช้เว็บจริงเป็นต้นแบบ design

ใช้เมื่อ user ให้ลิงค์เว็บมาพร้อมคำสั่งสไตล์ เช่น "เอาแบบ https://... แต่ทำเป็น minimal premium" (ไม่ใช้เมื่อแก้ CSS หน้าเดิมเล็กน้อย หรือไม่มีลิงค์ ref)

## Flow บังคับ 4 เฟส — ห้ามข้าม

```
0. อ่าน ref  →  1. ถามให้ครบ  →  2. เขียน DESIGN.md แล้วหยุดรอ approve  →  3. build
```

ห้ามเขียนโค้ดหน้าเว็บก่อนผ่านเฟส 2 ห้ามเดา requirement แทนการถามในเฟส 1

---

## เฟส 0 — อ่านเว็บ ref

ใช้ Chrome MCP เปิดจริง (เว็บ template สมัยใหม่ render ด้วย JS — WebFetch จะเห็นแค่ shell ว่าง)

1. `tabs_context_mcp` → `tabs_create_mcp` เปิดลิงค์ ref
2. `resize_window` 1440x900 → screenshot ด้วย `computer` (scroll เก็บ hero, section กลาง, footer)
3. `resize_window` 390x844 → screenshot ซ้ำ ดูว่า mobile ยุบ layout ยังไง
4. รัน `javascript_tool` ดึง token จริง แล้วอ่านผลด้วย `read_console_messages` (pattern: `\[REF\]`)

```js
(() => {
  const seen = { color:{}, bg:{}, font:{}, size:{}, weight:{}, radius:{}, shadow:{}, sectionPad:{} };
  const bump = (o,k) => { if (k && k !== 'none' && k !== 'rgba(0, 0, 0, 0)' && k !== '0px') o[k] = (o[k]||0)+1; };
  document.querySelectorAll('body *').forEach(el => {
    const r = el.getBoundingClientRect();
    if (r.width < 4 || r.height < 4) return;
    const s = getComputedStyle(el);
    bump(seen.color, s.color); bump(seen.bg, s.backgroundColor);
    bump(seen.font, s.fontFamily); bump(seen.size, s.fontSize);
    bump(seen.weight, s.fontWeight); bump(seen.radius, s.borderRadius);
    bump(seen.shadow, s.boxShadow);
  });
  document.querySelectorAll('section, header, footer, main > div').forEach(el => {
    const s = getComputedStyle(el);
    bump(seen.sectionPad, `${s.paddingTop}/${s.paddingBottom} w=${Math.round(el.getBoundingClientRect().width)}`);
  });
  const top = o => Object.entries(o).sort((a,b) => b[1]-a[1]).slice(0,8);
  console.log('[REF]', JSON.stringify(Object.fromEntries(Object.entries(seen).map(([k,v]) => [k, top(v)]))));
})()
```

5. ถ้า Chrome ใช้ไม่ได้ (ไม่มี permission / เว็บมี login gate / โหลดไม่ขึ้น) → fallback `WebFetch` อ่าน HTML + ไฟล์ CSS แล้ว**บอก user ตรงๆ ว่าอ่านได้แค่ text ไม่เห็นภาพจริง** ห้ามแกล้งวิเคราะห์ภาพที่ไม่ได้เห็น

สรุปจาก ref ให้ครบ 8 หัวข้อนี้ ก่อนไปเฟสถัดไป — ค่าที่ได้ต้องเป็นเลขจริงจาก computed style ไม่ใช่คำบรรยายลอยๆ

| หัวข้อ | ต้องได้อะไร |
|---|---|
| Color | HEX จริงของ bg / surface / text หลัก / text รอง / accent / border |
| Typography | font family heading+body, size scale (h1→body→caption), weight, line-height |
| Rhythm | base spacing unit, section padding desktop/mobile, container max-width |
| Shape | border-radius, ความหนา border, shadow มี/ไม่มี และแรงแค่ไหน |
| Layout | grid กี่คอลัมน์, gap, hero สูงเท่าไหร่, nav แบบไหน (sticky/transparent) |
| Component | ปุ่มมีกี่แบบ, card หน้าตายังไง, form field style |
| Motion | hover เปลี่ยนอะไร, duration/easing ประมาณเท่าไหร่ |
| Imagery | อัตราส่วนภาพ, มี overlay/filter ไหม, ภาพเต็มจอหรืออยู่ในกรอบ |

---

## เฟส 1 — ถาม requirement ให้ครบ

ถามด้วย `AskUserQuestion` แบ่งเป็น 2 รอบ (รอบละไม่เกิน 4 คำถาม) ทุกข้อต้องมีตัวเลือกที่เดาไว้ให้แล้วพร้อมข้อแนะนำ ไม่ใช่ปล่อยช่องว่าง

**รอบ 1 — ไม่รู้แล้วทำต่อไม่ได้**

| ถามอะไร | ตัวเลือกตั้งต้น |
|---|---|
| เว็บประเภทไหน เป้าหมายหลักคืออะไร | e-commerce / company profile / landing page ขายของชิ้นเดียว / portfolio / SaaS marketing / booking / dashboard ภายใน |
| Stack ที่จะ generate | static HTML+CSS / เข้าโครง PHP-MVC ของโปรเจคที่เปิดอยู่ / อื่นๆ |
| มีกี่หน้า หน้าอะไรบ้าง | 1 หน้า (long-scroll) / 3-5 หน้าหลัก / ระบุเอง — ถ้า user ระบุเอง ให้ทวนเป็น sitemap กลับไปยืนยัน |
| กลุ่มผู้ใช้ + ภาษาเนื้อหา | ไทยล้วน / อังกฤษล้วน / สองภาษา (มีผลตรงกับ font pairing และความยาวบรรทัด) |

**รอบ 2 — ดีเทลที่ทำให้ build ได้จบในรอบเดียว**

| ถามอะไร | ตัวเลือกตั้งต้น |
|---|---|
| ลอก ref แค่ไหน | เอา design language อย่างเดียว (แนะนำ) / ลอก layout ใกล้เคียงด้วย / เอาแค่ mood แล้วออกแบบใหม่ |
| Brand ที่มีอยู่ | มีโลโก้+สีแบรนด์แล้ว (ขอไฟล์/HEX) / ยังไม่มี ให้ derive จาก ref + คำสไตล์ |
| Content | มี copy จริงแล้ว / ใช้ placeholder ที่เขียนให้ตรงธุรกิจ (ไม่ใช่ lorem ipsum) |
| Feature ที่ต้องทำงานจริง | cart+checkout / ฟอร์มติดต่อส่งเมล / search / auth / แก้เนื้อหาผ่านหลังบ้าน / ไม่มี static ล้วน |

ถามเพิ่มเฉพาะเมื่อยังคลุมเครือ: dark mode เอาไหม, ระดับ animation (นิ่ง / hover เท่านั้น / scroll reveal), รองรับ browser เก่าแค่ไหน, มี deadline/ขนาดงานที่จำกัดไหม

ข้อที่ user ตอบไปแล้วในข้อความแรก **ห้ามถามซ้ำ** — ทวนสั้นๆ ว่ารับทราบแล้วข้ามไป

---

## เฟส 2 — DESIGN.md แล้วหยุด

เขียนไฟล์ `DESIGN.md` ที่ root ของโปรเจค (หรือโฟลเดอร์งานนั้น) ประกอบด้วย:

1. **สรุป brief** — ประเภทเว็บ, เป้าหมาย, กลุ่มผู้ใช้, stack, sitemap (จากเฟส 1)
2. **ref ที่ใช้ + สิ่งที่หยิบมา / สิ่งที่ตั้งใจไม่เอา** ระบุเป็นข้อ
3. **Design token เป็นตาราง** พร้อมค่าจริง — เขียนให้เป็น CSS custom property ได้ทันที (`--color-bg`, `--space-6`, `--radius-sm`, `--font-heading`, `--dur-fast`)
4. **Type scale + spacing scale** เป็นตัวเลขครบทุกขั้น
5. **Component spec** — ปุ่ม (default/hover/disabled), card, nav, hero, form field, footer อธิบายด้วย token ไม่ใช่ค่า hardcode
6. **Responsive** — breakpoint กี่จุด, แต่ละจุด layout เปลี่ยนยังไง
7. **ไฟล์ที่จะสร้าง/แก้** เป็นลิสต์พาธ

จบแล้ว**หยุด** สรุปให้ user 5-10 บรรทัด ถามว่าอนุมัติหรือปรับตรงไหน ห้ามเขียนโค้ดต่อจนกว่าจะได้ไฟเขียว

---

## เฟส 3 — Build

ยึด token จาก DESIGN.md เป็น CSS custom property ใน `:root` ที่เดียว ห้าม hardcode สี/ระยะซ้ำในไฟล์ component แล้วทำตามลำดับ: token + layout shell (nav/footer) → หน้าแรก → หน้าที่เหลือ → responsive pass → hover/motion pass เสร็จแต่ละหน้าหลัก เปิดดูด้วย Chrome เทียบกับ screenshot ref ที่เก็บไว้ อย่าเชื่อว่าโค้ดถูกโดยไม่ดูของจริง

**ระหว่าง build ต้องทำตาม checklist นี้เสมอ (อ้างอิงหัวข้อใน SKILL.md หลัก):**

- **Visual/motion ทุกจุด** → เช็คตาราง "เลือกอะไรเมื่อไหร่" ในหัวข้อ 3. CSS-First ก่อนเขียน JS เสมอ — animation/responsive/sticky footer/modal ทำด้วย CSS ล้วน, เขียน JS เฉพาะ dropdown ที่ต้อง escape overflow หรือรู้พิกัด runtime จริงๆ
- **ชื่อ class/component/ตัวแปร** → ตาม intent ไม่ใช่ generic (`$data`, `process()`) — ดู AI Smell ข้อ 1 ในหัวข้อ 1. Human Coding
- **ห้าม abstraction เผื่ออนาคต** → ไม่สร้าง component wrapper/layer ที่ spec ไม่ได้ขอ (AI Smell ข้อ 2, 3)
- **ห้าม comment obvious** — comment เฉพาะที่บอกทำไม/ที่มาของค่า token (Human Essence ข้อ 4)
- **ทำเท่าที่ DESIGN.md ระบุ** — ไม่เพิ่ม section/feature ที่ไม่ได้อนุมัติ (AI Smell ข้อ 3)
- **ทุกหน้าที่โหลดข้อมูล/มี form ต้องมีครบ 5 states** และ contrast สีผ่าน WCAG AA — ดูหัวข้อ 6. Senior UX/UI Principles ก่อน build จริง ไม่ใช่แค่ตอน DESIGN.md

---

## แปลคำสไตล์เป็นกฎที่จับต้องได้

user มักพูดเป็นคำกว้างๆ ต้องแปลงเป็นตัวเลขก่อน แล้วเขียนลง DESIGN.md

| คำสไตล์ | กฎที่ใช้จริง |
|---|---|
| minimal premium | whitespace เยอะ (section padding 96-160px desktop / 48-64px mobile); สีไม่เกิน 3 + neutral; accent ใช้พื้นที่ไม่เกิน 5%; font ไม่เกิน 2 ตระกูล; radius 0-8px; shadow แทบไม่มีหรือนุ่มมาก; border hairline 1px; motion 150-250ms ease-out; ห้าม gradient ฉูดฉาด ห้าม icon เยอะ |
| bold / editorial | heading ใหญ่มาก (clamp ถึง 72px+); contrast สูง; asymmetric grid; ภาพเต็มความกว้าง; สี accent จัดจ้าน |
| soft / friendly | radius 12-24px; palette pastel; shadow ฟุ้งกว้าง; illustration แทนภาพถ่าย; motion 250-400ms ease-in-out |
| tech / dark | bg เข้ม #0b0d12 ระดับ; accent เรืองแสง 1 สี; mono font สำหรับตัวเลข/โค้ด; border บางสว่างกว่า bg เล็กน้อย |
| luxury | serif heading; letter-spacing กว้างใน label ตัวพิมพ์ใหญ่; palette เกือบขาวดำ + 1 สีโลหะ; ภาพอัตราส่วนสูง; motion ช้า 400ms+ |

คำที่ไม่อยู่ในตาราง — ถาม user ว่าหมายถึงอะไรด้วยการยกตัวอย่าง 2-3 อย่างที่จับต้องได้ ห้ามเดาเงียบๆ

---

## Constraints

- **ห้ามก็อป asset จาก ref** — รูป โลโก้ ไอคอน ข้อความโฆษณา ชื่อแบรนด์ เป็นของเจ้าของเว็บนั้น เอาได้แค่ design language (สัดส่วน จังหวะ โทน โครง layout) ถ้า user สั่งให้ก็อปตรงๆ ให้บอกความเสี่ยงหนึ่งประโยคแล้วทำตามที่สั่ง
- ห้ามดึงไฟล์ CSS/JS ของ ref มาใส่โปรเจคทั้งก้อน — เขียนใหม่จาก token ที่สรุปได้
- ไม่แน่ใจว่าค่าไหนคือค่าจริงจาก ref (อ่านไม่ได้ / fallback text) → เขียนใน DESIGN.md ว่า "ประมาณจาก..." อย่าเขียนเหมือนวัดมาจริง
- ref ที่เป็น template ขาย (flatlogic, themeforest ฯลฯ) มักมี license — ถ้า user จะเอาไปใช้เชิงพาณิชย์ เตือนหนึ่งบรรทัดว่าให้เช็ค license ของ font และภาพที่ใช้
- font ที่ ref ใช้อาจเป็น commercial font — ถ้าไม่ฟรีบน Google Fonts ให้เสนอตัวใกล้เคียงที่ฟรี พร้อมบอกว่าตัวจริงคืออะไร
- โปรเจคที่ไม่มี build step อยู่แล้ว ห้ามลาก Tailwind/Sass เข้ามาโดยไม่ถาม
