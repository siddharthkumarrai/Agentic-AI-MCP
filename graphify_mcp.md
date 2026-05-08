# Graphify + MCP Integration Guide

> Connect [safishamsi/graphify](https://github.com/safishamsi/graphify) to Claude Code for AI-powered codebase navigation with full web research capability.

---

## What This Setup Does

| Tool | Role |
|---|---|
| **graphify** | Turns your codebase into a queryable knowledge graph |

---

## Prerequisites

- [Claude Code](https://claude.ai/code) installed
- Python 3.10+
- Node.js 18+

---

## Part 1 — Install Graphify

### Windows (PowerShell)

```powershell
# Install graphify CLI
pip install graphifyy

# Add to PATH (replace with your actual Python scripts path)
$scriptsPath = "C:\Users\<YOU>\AppData\Local\Python\pythoncore-3.14-64\Scripts"
[Environment]::SetEnvironmentVariable("PATH", $env:PATH + ";" + $scriptsPath, "User")
$env:PATH += ";$scriptsPath"
```

### macOS / Linux

```bash
pip install graphifyy
# or
pipx install graphifyy
# or (recommended)
uv tool install graphifyy
```

> ⚠️ The PyPI package is `graphifyy` (double-y). The CLI command is still `graphify`.

---

## Part 2 — Install the Claude Code Skill

### Windows (PowerShell)

```powershell
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.claude\skills\graphify"

Invoke-WebRequest `
  -Uri "https://raw.githubusercontent.com/safishamsi/graphify/v4/graphify/skill.md" `
  -OutFile "$env:USERPROFILE\.claude\skills\graphify\SKILL.md"
```

### macOS / Linux

```bash
mkdir -p ~/.claude/skills/graphify
curl -fsSL https://raw.githubusercontent.com/safishamsi/graphify/v4/graphify/skill.md \
  > ~/.claude/skills/graphify/SKILL.md
```

### Verify

```powershell
# Windows
Test-Path "$env:USERPROFILE\.claude\skills\graphify\SKILL.md"
# Should print: True
```

---

## Part 3 — Register with Claude Code

Navigate to your project folder and run:

```powershell
cd D:\your-project
graphify claude install
```

Expected output:
```
graphify section written to D:\your-project\CLAUDE.md
  .claude/settings.json  ->  PreToolUse hook registered
Claude Code will now check the knowledge graph before answering
codebase questions and rebuild it after code changes.
```

> ⚠️ Always run this from your **project folder**, not System32 or any system directory.

---

## Part 4 — Build the Knowledge Graph

```powershell
cd D:\your-project

# Full build (requires API key — see below)
graphify extract . --backend claude

# Free build — AST/code structure only, no LLM needed
graphify extract . --no-cluster
```

### Set API Key (for full semantic extraction)

```powershell
# Permanent — survives restarts
[Environment]::SetEnvironmentVariable("ANTHROPIC_API_KEY", "sk-ant-your-key", "User")

# Then run
graphify extract . --backend claude
```

### Output files created in `graphify-out/`

| File | Description |
|---|---|
| `graph.html` | Interactive graph — open in any browser |
| `GRAPH_REPORT.md` | Architecture summary Claude reads automatically |
| `graph.json` | Raw queryable graph data |

---

### Verify all servers connected

Inside Claude Code:
```
/mcp
```

Expected output:
```
1 servers

  User MCPs
  ✔ scrapling  · connected · 6 tools
```

---

## Part 6 — Using Everything Together

### Inside Claude Code terminal

```bash
# Build/update graph after code changes
graphify extract .          # full rebuild
graphify update .           # incremental (code only, no LLM)
graphify extract . --update # incremental with docs

# Query your codebase
/graphify query "how does authentication work"
/graphify query "where is payment processing handled"
/graphify explain "OrderController"
/graphify path "UserLogin" "Database"
```

### Auto-update on every git commit

```powershell
cd D:\your-project
graphify hook install
```

---

## Part 7 — Incremental Updates (Save Tokens)

```powershell
# After adding/modifying files
graphify update .

# Force rebuild after large refactor
graphify extract . --force
```

---

## Full MCP Stack Reference

```json
{
  "mcpServers": {
    "context7": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp"]
    },
    "scrapling": {
      "type": "stdio",
      "command": "python",
      "args": ["-m", "scrapling.mcp"],
      "env": {}
    },
  }
}
```

Add this block inside `C:\Users\<YOU>\.claude.json` under the `"mcpServers"` key.

---

## Troubleshooting

### `graphify: command not found`
```powershell
# Add scripts folder to PATH
$scriptsPath = "C:\Users\<YOU>\AppData\Local\Python\pythoncore-3.14-64\Scripts"
[Environment]::SetEnvironmentVariable("PATH", $env:PATH + ";" + $scriptsPath, "User")
```

### `/graphify` in Claude Code gives bash errors on Windows
The `/graphify` skill uses bash internally. On Windows, always use the CLI directly:
```powershell
graphify extract .   # in PowerShell, not inside Claude Code terminal
```

### `error: no LLM API key found`
```powershell
[Environment]::SetEnvironmentVariable("ANTHROPIC_API_KEY", "sk-ant-...", "User")
# Restart PowerShell, then retry
graphify extract . --backend claude
```

---

## Quick Reference Card

| Task | Command |
|---|---|
| Install graphify | `pip install graphifyy` |
| Install skill | See Part 2 above |
| Register with project | `graphify claude install` |
| Build graph (free) | `graphify extract . --no-cluster` |
| Build graph (full) | `graphify extract . --backend claude` |
| Incremental update | `graphify update .` |
| Query graph | `/graphify query "..."` |
| Explain a node | `/graphify explain "ClassName"` |
| Find path | `/graphify path "A" "B"` |
| Open visual graph | Open `graphify-out/graph.html` in browser |
| Check MCPs | `/mcp` inside Claude Code |

---

## References

- [graphify GitHub](https://github.com/safishamsi/graphify)
- [Claude Code MCP Docs](https://code.claude.com/docs/en/mcp)
