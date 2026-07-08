# cuevlife-skills

> Custom skills collection for OpenCode Superpowers — Grounded Debate, Tester, Ask-User, Lessons Learned + more

---

## สารบัญ

- [ requirements](#-requirements)
- [ วิธีติดตั้ง](#-วิธีติดตั้ง)
   - [1. OpenCode](#1-opencode)
   - [2. opencode.jsonc](#2-opencodejsonc)
   - [3. Superpowers (npm local fallback สำหรับ Windows)](#3-superpowers-npm-local-fallback-สําหรับ-windows)
   - [4. Custom Skills](#4-custom-skills)
- [ MCP Servers](#-mcp-servers)
- [ Skills ทั้งหมด](#-skills-ทั้งหมด)
- [ การใช้งาน](#-การใช้งาน)

---

## 📦 Requirements

| สิ่งที่ต้องมี | หมายเหตุ |
|-------------|----------|
| **Node.js** v20+ | `node --version` |
| **npm** | ติดมากับ Node.js |
| **OpenCode** | `npm install -g opencode-ai` |
| **Android SDK** | สำหรับ mobile-mcp (optional) |
| **Chrome/Chromium** | สำหรับ chrome-devtools-mcp |
| **API Keys** | context7, firecrawl (ดูด้านล่าง) |

---

## 🚀 วิธีติดตั้ง

### 1. OpenCode

```powershell
npm install -g opencode-ai
```

เช็คเวอร์ชัน:

```powershell
opencode --version
# ควรเป็น 1.17.13+
```

### 2. opencode.jsonc

วางไฟล์ `opencode.jsonc` ที่ `%USERPROFILE%\.config\opencode\opencode.jsonc`:

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "plugin": ["~/.config/opencode/node_modules/superpowers", "opencode-parser"],
  "command": {
    "test": {
      "description": "Run UI tests via Chrome DevTools + MySQL verification",
      "subtask": true,
      "template": "Load the tester skill and test the application on localhost. Test scenario: $ARGUMENTS. Use Chrome DevTools MCP for browser actions and MySQL MCP for data verification."
    }
  },
  "mcp": {
    "mobile-mcp": {
      "type": "local",
      "command": ["npx", "@mobilenext/mobile-mcp@latest"],
      "enabled": true
    },
    "context7": {
      "type": "remote",
      "url": "https://mcp.context7.com/mcp",
      "headers": {
        "CONTEXT7_API_KEY": "<YOUR_CTX7_API_KEY>"
      },
      "enabled": true
    },
    "firecrawl": {
      "type": "remote",
      "url": "https://mcp.firecrawl.dev/v2/mcp",
      "enabled": true
    },
    "chrome-devtools": {
      "type": "local",
      "command": ["npx", "-y", "chrome-devtools-mcp@latest"],
      "enabled": true
    }
  }
}
```

> **⚠️ Warning:** เปลี่ยน `<YOUR_CTX7_API_KEY>` เป็น API Key จริงของคุณ

### 3. Superpowers (npm local fallback สำหรับ Windows)

Windows OpenCode บางเวอร์ชันมีปัญหากับ `git+https` plugin spec ให้ใช้วิธีนี้:

```powershell
npm install superpowers@git+https://github.com/obra/superpowers.git --prefix "$HOME\.config\opencode"
```

จากนั้นใน `opencode.jsonc` ใช้:

```jsonc
"plugin": ["~/.config/opencode/node_modules/superpowers", "opencode-parser"]
```

### 4. Custom Skills

Skills ที่เพิ่มเข้าไปใน Superpowers (copy ไปที่ `%USERPROFILE%\.config\opencode\node_modules\superpowers\skills\`):

| Skill | ที่มา | รายละเอียด |
|-------|------|-----------|
| **tester** | KZ Custom | UI testing via Chrome DevTools + MySQL |
| **ask-user** | KZ Custom | ถาม user ด้วย `question` tool + batch mode + recommend |
| **lessons-learned** | KZ Custom | บันทึก bug/solution ข้าม session |
| **agents** | KZ Custom | สร้าง AGENTS.md อัตโนมัติ |
| **grounded-debate** | KZ Custom | คู่หูที่เถียงด้วยข้อมูลจริง ป้องกัน blind agreement |
| **anti-ai-slop-ux** | KZ Custom | ป้องกัน AI-slop UI — gradient, shadows, decoration ที่ไม่จำเป็น |

```powershell
# ถ้ามี skills เหล่านี้ใน repo ของคุณ
$SKILLS_DIR = "$HOME\.config\opencode\node_modules\superpowers\skills"
Copy-Item -Path ".\skills\tester\SKILL.md" -Destination "$SKILLS_DIR\tester\SKILL.md" -Force
Copy-Item -Path ".\skills\ask-user\SKILL.md" -Destination "$SKILLS_DIR\ask-user\SKILL.md" -Force
Copy-Item -Path ".\skills\lessons-learned\SKILL.md" -Destination "$SKILLS_DIR\lessons-learned\SKILL.md" -Force
Copy-Item -Path ".\skills\agents\SKILL.md" -Destination "$SKILLS_DIR\agents\SKILL.md" -Force
Copy-Item -Path ".\skills\grounded-debate\SKILL.md" -Destination "$SKILLS_DIR\grounded-debate\SKILL.md" -Force
Copy-Item -Path ".\skills\anti-ai-slop-ux\SKILL.md" -Destination "$SKILLS_DIR\anti-ai-slop-ux\SKILL.md" -Force
```

---

## 🔌 MCP Servers

| MCP | Type | คำสั่ง | API Key |
|-----|------|--------|---------|
| **mobile-mcp** | local | `npx @mobilenext/mobile-mcp@latest` | ไม่ต้องใช้ |
| **context7** | remote | `https://mcp.context7.com/mcp` | `CONTEXT7_API_KEY` |
| **firecrawl** | remote | `https://mcp.firecrawl.dev/v2/mcp` | ไม่ต้องใช้ (free tier) |
| **chrome-devtools** | local | `npx -y chrome-devtools-mcp@latest` | ไม่ต้องใช้ |

### context7 API Key

สมัครที่ [context7.com](https://context7.com) แล้วเอา API Key มาใส่ใน `opencode.jsonc`

### firecrawl

Remote mode ใช้ free tier ได้เลย ไม่ต้องใช้ API Key

ถ้าต้องการ local mode:

```jsonc
"firecrawl": {
  "type": "local",
  "command": ["npx", "-y", "firecrawl-mcp"],
  "env": {
    "FIRECRAWL_API_KEY": "<YOUR_API_KEY>"
  },
  "enabled": true
}
```

### chrome-devtools

Optional flags:
- `"--headless"` — รันแบบไม่มี UI
- `"--slim"` — เฉพาะ basic navigation/screenshot
- `"--isolated"` — temp profile ล้างเมื่อปิด

```jsonc
"chrome-devtools": {
  "type": "local",
  "command": ["npx", "-y", "chrome-devtools-mcp@latest", "--headless", "--isolated"],
  "enabled": true
}
```

---

## 📋 Skills ทั้งหมด

### Built-in จาก Superpowers

| Skill | ใช้เมื่อ |
|-------|---------|
| brainstorming | ก่อนเริ่ม creative work |
| systematic-debugging | เจอ bug / test fail |
| test-driven-development | เขียน test ก่อน code |
| writing-plans | วางแผน implementation |
| executing-plans | รันแผนทีละ checkpoint |
| dispatching-parallel-agents | ทำงาน parallel |
| subagent-driven-development | dispatch subagent ทำ task |
| verification-before-completion | เช็คก่อนบอกเสร็จ |
| requesting-code-review | review ก่อน merge |
| receiving-code-review | รับ feedback |
| finishing-a-development-branch | จบ branch |
| using-git-worktrees | แยก workspace |
| writing-skills | สร้าง skill ใหม่ |
| customize-opencode | แก้ไข config opencode |

### Custom Skills (KZ)

| Skill | ใช้เมื่อ |
|-------|---------|
| **tester** | `/test` — ทดสอบ UI อัตโนมัติ |
| **ask-user** | เวลาต้องถาม user — บังคัดใช้ `question` tool |
| **lessons-learned** | จำ bug/solution ไว้ใช้ทีหลัง |
| **agents** | สร้าง AGENTS.md |
| **grounded-debate** | ป้องกัน AI เห็นด้วยทุกอย่าง — เถียงด้วยข้อมูลจริง |
| **anti-ai-slop-ux** | สร้าง UI ที่ดูเป็นมืออาชีพ — ไม่ใช่ AI slop |

---

## 🎯 การใช้งาน

### เช็คว่า Superpowers พร้อมใช้งาน

```
Tell me about your superpowers
```

### รันเทส

```
/test
/test login flow
```

### ใช้ Skill

```
use skill tool to load tester
use skill tool to load ask-user
```

### Browser Automation

```
เปิด chrome ไปที่ localhost:3000 แล้วกดปุ่ม Login
```

### Web Scraping

```
crawl https://example.com แล้วสรุปเนื้อหา
```

---

## 🔄 อัปเดต

```powershell
# อัปเดต OpenCode
npm install -g opencode-ai

# อัปเดต Superpowers
npm install superpowers@git+https://github.com/obra/superpowers.git --prefix "$HOME\.config\opencode"

# ลบ cache ถ้า plugin ไม่โหลด
Remove-Item "$HOME\.cache\opencode\packages\superpowers*" -Recurse -Force
```

---

## 🐛 Troubleshooting

| ปัญหา | วิธีแก้ |
|-------|--------|
| Plugin ไม่โหลด | เช็ค logs: `opencode run --print-logs "hello" 2>&1 \| Select-String "superpowers"` |
| SQLite error (`replacement_seq`) | อัปเดต OpenCode: `npm install -g opencode-ai` |
| MCP ไม่เชื่อมต่อ | restart OpenCode |
| Windows cache issues | ใช้ npm local fallback (ข้อ 3) |

---

> **KZ Starter Pack** — fork/clone ได้เลย ปรับแต่งตามต้องการ
