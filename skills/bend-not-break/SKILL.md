---
name: bend-not-break
description: Reduce hardcode และออกแบบให้ extensible — Config-Driven, Feature Flags, Strategy, Pipeline, Addon, Event-Driven, RBAC. รวมสูตรคอนกรีตสำหรับโปรเจค solodev ด้วย — installer เอง, dynamic role, feature flag. ใช้เมื่อ refactor ให้โค้ดยืดหยุ่น หรือเริ่มโปรเจคใหม่ที่ user บอกว่าเป็น solodev/ต้องการความยืดหยุ่นแบบ SaaS (ไม่เกี่ยวกับการอ่าน config file, ไม่เกี่ยวกับ settings.json ของ Claude Code เอง)
---

# Bend Not Break — Extensible Architecture

## Objective

ลด hardcode และทำให้โค้ด extensible — ยืดได้โดยไม่หัก: รองรับการเปลี่ยนค่า, เปิด/ปิด feature, เพิ่ม module/role โดยไม่แก้โค้ดหลัก หมายเหตุ: skill นี้เกี่ยวกับ design patterns (ไม่ใช่การอ่าน/เขียน config file ธรรมดา)

## Steps/Workflow

1. **ระบุค่าที่ hardcode อยู่** → verify: รายการ values/features/modules ที่ต้องยืดหยุ่น
2. **เลือก pattern ที่ง่ายสุดที่พอ** → verify: ไล่ตามตาราง 7 patterns — Config-Driven → Feature Flags → Strategy → Pipeline → Addon → Event-Driven → RBAC
3. **แยก Core vs Addon** → verify: Admin tools/export/notification/OCR เป็น addon, Auth/Router/DB/Security เป็น core
4. **Deploy-first, toggle-later** → verify: เปลี่ยนค่า/ปิด feature ได้โดยไม่ redeploy
5. **โปรเจค solodev ที่ต้องการ installer/dynamic role/feature flag** → verify: เช็คว่าต้องการอันไหนจริง (ไม่ใช่ทำครบ 3) แล้วดู section "Solodev Concrete Recipes" ด้านล่าง

## Examples

```
❌ if ($env === 'production') { ... }      → hardcode ค่าในโค้ด
✅ $config->get('app.env')                → Config-Driven

❌ ฝัง feature ใหม่ในโค้ดหลัก เปิดปิดไม่ได้
✅ Feature flag ใน admin UI → toggle โดยไม่ deploy

❌ สร้าง AddonSystem ตั้งแต่ยังมี 1 module
✅ รอจนมี 2+ module ที่ต้อง drop-in ค่อยสร้าง
```

## Constraints

- ห้ามเลือก pattern ใหญ่กว่าที่จำเป็น — pick simplest first
- ห้าม refactor เป็น addon ในเมื่อยังไม่ต้อง (YAGNI)
- ห้ามแก้ production code ตรงๆ — แก้ที่ source แล้ว deploy
- ห้าม hardcode flag ใหม่โดยไม่ผ่าน config layer
- ห้ามทำครบ installer+role+flag ถ้า solodev project ไม่ต้องการ — ถามก่อนว่าต้องการอันไหน

## 7 Patterns (pick simplest first)

| Pattern | When |
|---------|------|
| **Config-Driven** | Business logic values that change → DB/JSON config |
| **Feature Flags** | New feature → toggle on/off without deploy |
| **Strategy Pattern** | Multiple variants of same logic → swap at runtime |
| **Pipeline** | Multi-step process → chain of stages |
| **Addon System** | Business logic module → drop-in folder + JSON manifest |
| **Event-Driven** | Decoupled side effects → events + listeners |
| **Entitlement/RBAC** | Per-role/per-entity permissions → policy class |

## Core vs Addon

| Core (keep) | Addon (extract) |
|-------------|-----------------|
| Auth, Router, DB, Security | Admin tools, export formats, notification channels, OCR providers |

## Principle

- Deploy first → toggle later (zero-downtime)
- Feature toggle in admin UI (no dev needed)
- Ask: "Should this be an addon?" before building

---

## Solodev Concrete Recipes

โปรเจคที่ดูแลคนเดียว ไม่มีทีม ops คอยรัน migration/sql มือให้ — 4 pattern คอนกรีตที่ implement ได้ทันที เลือกทำเฉพาะที่ต้องการจริง ไม่ใช่ทำครบทุกครั้ง:

### Pattern A: Installer

```
core/Installer.php
├── isInstalled(): bool
│     — เช็ค SELECT COUNT(*) FROM users WHERE role >= SUPER_ADMIN
│     — DB connect fail / ตาราง users ไม่มี → return false (ไม่ใช่ throw)
│     — cache ผลลัพธ์ true ลง marker file (เช่น storage/installed.lock) เพื่อไม่ query ทุก request
├── preflightChecks(): เช็ค PHP version, extension (pdo_mysql, curl), โฟลเดอร์ writable
├── withInstallLock(callable $fn)
│     — flock() กันติดตั้งซ้อนกันสองคนพร้อมกัน
└── install($env, $adminAccount)
      — สร้าง DB (ถ้ายังไม่มี), เขียน .env, รัน schema.sql, seed superadmin คนแรกจากฟอร์ม, เขียน lock marker

InstallController — redirect /login อัตโนมัติถ้า isInstalled() แล้ว, ไม่งั้น render หน้าติดตั้ง
```

### Pattern B: Dynamic Role

```sql
CREATE TABLE roles (
    id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50) NOT NULL UNIQUE,      -- 'superadmin', 'collector', หรือ role ใหม่ที่ user สร้าง
    label VARCHAR(100) NOT NULL,           -- ป้ายชื่อแสดงผล
    is_system TINYINT(1) NOT NULL DEFAULT 0 -- 1 = built-in ห้ามลบ (superadmin/admin)
);

CREATE TABLE role_permissions (
    role_id INT UNSIGNED NOT NULL,
    permission_id INT UNSIGNED NOT NULL,
    PRIMARY KEY (role_id, permission_id)
);
```

- Permission check ในโค้ดอ่านจาก DB (`PermissionService::forRole($roleId)`) — **ไม่มี** role ID ผูกตายในโค้ดเลย นอกจาก `is_system` role ที่ต้องกันตัวเองไว้ (เช่น superadmin ต้องมี permission จัดการ role เสมอ กันสร้าง role ใหม่แล้วแก้จนตัวเองล็อกตัวเองไม่ได้)
- UI: หน้า "จัดการ role" ต้องมี create/rename/delete role ใหม่ได้ (delete บล็อกถ้า `is_system=1` หรือยังมี user ผูกอยู่) — ไม่ใช่แค่หน้า toggle permission ของ role เดิม 5 ตัว

### Pattern C: Feature Flag

```php
class Feature
{
    private const DEFAULTS = ['auto_seed' => false, 'export' => true, ...];
    private static ?array $cache = null;

    public static function enabled(string $key): bool
    {
        self::$cache ??= array_merge(self::DEFAULTS, SystemConfig::get('SYSTEM_FEATURES', []));
        return (bool) (self::$cache[$key] ?? self::DEFAULTS[$key] ?? false);
    }
}
```

- `system_configs` เก็บ override เป็น JSON, ไม่มี override → ใช้ `DEFAULTS` ในโค้ด (โค้ด deploy ครั้งเดียว ค่า toggle เปลี่ยนได้ทีหลังไม่ต้อง deploy)
- Admin UI มีปุ่ม toggle ตรงๆ — ไม่ต้องแก้ `.env`/DB มือ

### Pattern D: Per-User Permission Flag (override บน role)

ใช้เมื่อ role ให้สิทธิ์เป็นค่าเริ่มต้นพอสำหรับคนส่วนใหญ่ในตำแหน่งนั้น แต่มีบางคนต้องการสิทธิ์เพิ่มเฉพาะตัว (คนที่ทำงานแทนกันได้บางส่วน) — **ไม่ใช่** เหตุผลให้ยุบ role ทิ้งแล้วตั้ง flag รายคนล้วนๆ (เสียประโยชน์ของ role = ค่าเริ่มต้นที่ setup ได้เร็ว), ก็ไม่ใช่เหตุผลให้สร้าง role ใหม่สำหรับคนเดียว (role ระเบิดจำนวนเปล่าๆ) — คำตอบคือ role (RBAC) + per-user extra (ACL) ผสมกัน เหมือน AWS IAM (policy บน role + inline policy เฉพาะ user)

```sql
CREATE TABLE user_permissions (
    user_id INT UNSIGNED NOT NULL,
    permission_id INT UNSIGNED NOT NULL,
    PRIMARY KEY (user_id, permission_id)
    -- FK ไป users/permissions ON DELETE CASCADE ทั้งคู่
);
```

```php
// รวมสิทธิ์จาก role + extra เฉพาะคน แล้ว dedupe — cache ต่อ user ต่อ request
public static function forUser(int $userId, int $roleId): array
{
    $extra = /* SELECT permission.key FROM user_permissions JOIN permissions WHERE user_id = :userId */;
    return array_values(array_unique([...self::forRole($roleId), ...$extra]));
}
```

**Deep link สำคัญ — เขียนไว้แค่ที่เดียว ห้ามมี 2 จุด:** ทุก entry point ที่เช็คสิทธิ์ (`can()` ใน controller, helper ที่ view เรียก) ต้องเปลี่ยนมาเรียก `forUser()` ตัวนี้ทั้งหมด — ถ้าเหลือจุดไหนยังเรียก `forRole()` ตรงๆ จุดนั้นจะมองไม่เห็น extra ของ user เลย (bug เงียบ ไม่มี error ให้เห็น)

**UI: checklist ต้องอัพเดทตาม role ที่เลือกแบบ real-time** — ถ้าไม่ทำ user จะสับสนว่า checkbox ไหนเป็น "role ให้อยู่แล้ว" ไหนเป็น "extra ที่ตั้งเอง" หลัง submit เท่านั้น วิธีทำ: embed `{roleName: [permissionKey,...]}` ของทุก role เป็น JSON ในหน้า, ฟัง event ตอนเปลี่ยน role selector แล้ว toggle `disabled`+`checked` ของแต่ละ checkbox สด — extra ที่ user ตั้งไว้จริง (ไม่ใช่ที่ได้จาก role) ต้องติ๊กค้างเสมอไม่ว่าจะสลับ role ไปมากี่รอบ (เทียบกับ set ที่โหลดมาตอนเปิดหน้า ไม่ใช่ recompute จาก role ปัจจุบัน)

**เจอจริง 2 บั๊กตอน implement หน้า checklist นี้ — ระวังซ้ำ:**

1. **ปุ่ม action เดี่ยว (ลบ role ฯลฯ) ห้ามอยู่ใน `<form>` ใหญ่ที่ save ทั้งหน้า** — nested `<form>` เป็น invalid HTML, browser hoist/พังเงียบๆ (กด submit ในกลับไปเรียก submit นอกแทน) เอาปุ่ม action แยกออกมาเป็น `<form>` คนละก้อน อยู่นอก form หลักเสมอ
2. **ถ้า entity เดียวกันแก้ได้จากหลาย UI (เช่น modal เร็วๆ ในหน้า list + หน้า edit เต็ม) ต้องมี field ตรงกันทุกจุด** — ตอนเพิ่ม checklist สิทธิ์พิเศษเข้าไปในหน้า edit เต็ม แต่ modal เดิมที่หน้า list ใช้จริงไม่มี field นี้ → แก้ user ผ่าน modal (ทางที่คนใช้จริง) จะเซฟทับ extra permissions เป็นค่าว่างเงียบๆ ทุกครั้ง ก่อนบอกว่า "เสร็จแล้ว" ต้อง grep หา endpoint เดียวกันทั้งโปรเจคว่ามี form อื่น POST ไปที่เดียวกันไหม — ถ้ามีต้องอัพเดทให้ตรงกันหรือตัดเหลือทางเดียว

### Solodev Examples

```
❌ if ($_SESSION['role'] === 9) { ... }              — role ผูกตายเป็นเลข
✅ if ($this->can('admin.permissions.manage')) {...} — เช็คผ่าน permission ที่ผูกกับ role แบบ dynamic

❌ ติดตั้งใหม่ต้อง SSH เข้าไปรัน php seed.php มือ
✅ เข้าเว็บครั้งแรก เจอหน้า installer อัตโนมัติเพราะ isInstalled()=false

❌ เปิด/ปิด feature ต้องแก้โค้ด constant แล้ว deploy ใหม่
✅ Feature::enabled('export') อ่านจาก DB, admin toggle เองได้

❌ ยุบ role ทิ้งเหลือ 2 อย่าง (admin/user) แล้วตั้ง flag ทุกคนตั้งแต่ศูนย์
✅ role เดิมให้ default ตามหน้าที่งาน + ติ๊ก extra เฉพาะคนที่ต้องการจริง (ชะเอมเป็น opener แต่ทำหน้าที่ collector บางอย่างได้ด้วย)
```

### Solodev Constraints

- ห้าม fresh-install detect พังแบบ throw exception ตรงๆ — ต้อง fail-safe เป็น "ยังไม่ติดตั้ง" เสมอเมื่อเช็คไม่ได้
- ห้ามลบ/แก้ role ที่ `is_system=1` ได้จาก UI โดยไม่มี safeguard กันคนใช้ล็อกตัวเองออกจากระบบ
- ห้าม cache feature flag ข้าม request — toggle แล้วต้องเห็นผลทันที
- ห้าม over-engineer เป็น multi-tenant (per-company feature flag) ถ้า user ไม่ได้ขอ — เริ่มจาก single-tenant global flag ก่อน
- ห้ามยุบ role เหลือ 2-3 อย่างแบบกว้างๆ แล้วใช้ per-user flag ล้วนแทน RBAC ทั้งระบบ — เสียประโยชน์ "ตั้งค่าเริ่มต้นเร็ว" ของ role ไปเปล่าๆ ใช้ผสมกันเสมอ (role = default, per-user = ข้อยกเว้น)
- ห้ามมีจุดเช็คสิทธิ์ที่ยังเรียก `forRole()` ตรงๆ หลงเหลืออยู่หลังทำ per-user extra แล้ว — ต้องเปลี่ยนเป็น `forUser()` ให้ครบทุกจุด ไม่งั้น extra จะไม่มีผลแบบเงียบๆ
