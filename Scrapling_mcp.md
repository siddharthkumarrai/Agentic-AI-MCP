# 🕷️ Scrapling MCP Server — Windows Setup Guide

> Connect Scrapling's headless browser scraping tools to **Claude Code** on Windows in 4 steps.

---

## 📋 Prerequisites

- Python 3.x installed on your system
- Claude Code installed and working
- Terminal access (PowerShell or CMD)

---

## ⚡ Quick Start

```powershell
# 1. Install with AI extras
python.exe -m pip install "scrapling[ai]"

# 2. Install browser binaries
scrapling.exe install

# 3. Edit .claude.json (see Step 3 below)

# 4. Verify: open Claude Code → type /mcp → look for scrapling ✔ connected · 10 tools
```

---

## 🔧 Step-by-Step Setup

### Step 1 — Install the Package with AI Extras

The base `scrapling` package does **not** include MCP server components. You must use the `[ai]` extra flag.

> **Why `[ai]`?** Without it, the MCP tools won't be available when Claude Code tries to connect.

Open PowerShell and run:

```powershell
# Replace the path with your specific Python installation or venv path
C:\Users\siddh\AppData\Local\Python\pythoncore-3.14-64\python.exe -m pip install "scrapling[ai]"
```

> ⚠️ **PowerShell note:** Always wrap the package name in double quotes (`"scrapling[ai]"`) to prevent PowerShell from misinterpreting the square brackets.

---

### Step 2 — Install Browser Binaries

Scrapling needs headless browser binaries (Chromium via Playwright) to render dynamic JavaScript and bypass bot-protection systems like Cloudflare.

> ❗ **Do not skip this step.** Missing binaries will cause MCP tools to fail silently when invoked.

```powershell
# Use the absolute path to the Scripts folder of your Python installation
C:\Users\siddh\AppData\Local\Python\pythoncore-3.14-64\Scripts\scrapling.exe install
```

Wait for the command to finish fully before continuing.

---

### Step 3 — Configure Claude Code (`.claude.json`)

You need to register the Scrapling executable with Claude Code.

**1. Open the config file:**

```
C:\Users\siddh\.claude.json
```

**2. Add the `scrapling` entry to the `mcpServers` block:**

```json
{
  "mcpServers": {
    "scrapling": {
      "type": "stdio",
      "command": "C:\\Users\\siddh\\AppData\\Local\\Python\\pythoncore-3.14-64\\Scripts\\scrapling.exe",
      "args": [
        "mcp"
      ],
      "env": {}
    }
  }
}
```

**3. Critical Windows rules:**

| Rule | Detail |
|------|--------|
| Use absolute path | Do not rely on PATH resolution for `scrapling.exe` |
| Escape backslashes | Double every `\` in JSON → `C:\\Users\\siddh\\...` |
| Don't use `python -m` | It does not work reliably in this context |

---

### Step 4 — Verify the Connection

1. Open your terminal in your project directory
2. Start Claude Code: `claude`
3. Type `/mcp` and press **Enter**
4. You should see:

```
scrapling · ✔ connected · 10 tools
```

✅ You're ready to use Scrapling inside Claude Code!

---

## 🛠️ Troubleshooting

If the server shows `✘ failed` in the `/mcp` menu:

1. **Stop the session** — press `Esc` or `Ctrl+C`
2. **Run debug mode:**
   ```powershell
   claude --debug
   ```
3. **Check the logs** — look at the printed output or generated debug file for the exact `stderr` error
4. **Fix based on the error** — common causes below

### Common Issues

| Issue | Resolution |
|-------|------------|
| Incorrect path in `.claude.json` | Verify the exact path to `scrapling.exe` using File Explorer or `where scrapling` |
| Single backslashes in path | Double every backslash: `C:\\Users\\...` not `C:\Users\...` |
| Browser binaries not installed | Re-run `scrapling.exe install` from Step 2 and wait for it to complete |
| Wrong Python environment | Ensure you installed `[ai]` in the same Python env whose `Scripts` folder you reference |

---

## 📁 File Reference

```
C:\Users\siddh\
├── .claude.json                          ← Claude Code MCP config
└── AppData\Local\Python\
    └── pythoncore-3.14-64\
        ├── python.exe                    ← Used for pip install
        └── Scripts\
            └── scrapling.exe             ← MCP server command
```

---

## 🔗 Resources

- [Scrapling on PyPI](https://pypi.org/project/scrapling/)
- [Claude Code Docs](https://docs.anthropic.com/en/docs/claude-code)
- [MCP Protocol](https://modelcontextprotocol.io/)
