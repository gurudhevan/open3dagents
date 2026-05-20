# Blender AI Control — Ollama + Open WebUI + MCP

Control Blender through natural language chat using a fully local AI stack. No cloud APIs required. Supports cloud-hosted open-weight models via Ollama.

![Stack](https://img.shields.io/badge/Blender-4.0+-orange) ![Ollama](https://img.shields.io/badge/Ollama-0.24+-blue) ![OpenWebUI](https://img.shields.io/badge/Open%20WebUI-0.9.5+-green) ![License](https://img.shields.io/badge/License-MIT-yellow)

---

## What This Is

A setup guide and configuration for connecting **Ollama** (local LLM runtime) to **Blender 4.0+** via the **Model Context Protocol (MCP)**, using **Open WebUI** as the chat interface.

You chat in Open WebUI → the model calls Blender tools → objects appear in your 3D viewport.

---

## Architecture

```
You (chat prompt)
      │
Open WebUI Desktop  ←─── Ollama (local + cloud models)
      │  OpenAPI/HTTP
mcpo proxy (:8008)
      │  stdio
blender-mcp server
      │  TCP :9876
Blender Addon (addon.py)
      │
Blender 4.0+ (bpy API)
```

---

## Prerequisites

| Tool | Version | Windows | Mac |
|---|---|---|---|
| OS | — | Windows 10/11 64-bit | macOS 11 Big Sur+ |
| Blender | 4.0+ | [blender.org](https://www.blender.org/download/) | [blender.org](https://www.blender.org/download/) |
| Ollama | 0.24+ | [ollama.com/download/windows](https://ollama.com/download/windows) | [ollama.com/download/mac](https://ollama.com/download/mac) |
| Open WebUI Desktop | 0.9.5+ | [open-webui/desktop](https://github.com/open-webui/desktop) | [open-webui/desktop](https://github.com/open-webui/desktop) |
| uv | 0.10+ | See Step 1 | See Step 1 |

---

## Installation

### Step 1 — Install uv

> **Note:** Python does not need to be installed for this workflow. uv manages its own Python environments internally. All other components (Ollama, Open WebUI, blender-mcp, mcpo) bundle their own runtimes.

#### Windows (CMD)

**Option A — WinGet** (recommended, built into Windows 10/11):

```cmd
winget install --id=astral-sh.uv -e
```

**Option B — PowerShell** (if WinGet is unavailable):

```cmd
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

**Option C — pip** (only if Python is already installed):

```cmd
pip install uv
```

Restart CMD, then verify:

```cmd
uv --version
uvx --version
```

#### Mac (Terminal)

**Option A — curl** (recommended):

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

**Option B — Homebrew:**

```bash
brew install uv
```

**Option C — pip** (only if Python is already installed):

```bash
pip install uv
```

Restart Terminal, then verify:

```bash
uv --version
uvx --version
```

---

### Step 2 — Install Blender Addon

1. Download `addon.py` from [ahujasid/blender-mcp](https://github.com/ahujasid/blender-mcp/blob/main/addon.py) → click **Raw** → Save As `addon.py`
2. Open Blender
3. `Edit → Preferences → Add-ons → Install...`
4. Select `addon.py`
5. Enable **"Interface: Blender MCP"** checkbox

> **Windows port fix:** If the socket fails to start, change port from `9876` → `9001` in addon preferences and match it in Step 4.

---

### Step 3 — Start Blender Socket Server

1. In Blender 3D Viewport press `N` to open the sidebar
2. Click the **BlenderMCP** tab
3. Click **"Connect to MCP server"**
4. Confirm status shows: `Running on port 9876`

---

### Step 4 — Start mcpo Proxy + blender-mcp Server

#### Windows (CMD)

```cmd
uvx mcpo --port 8008 -- uvx blender-mcp
```

#### Mac (Terminal)

```bash
uvx mcpo --port 8008 -- uvx blender-mcp
```

Expected output (both):
```
Uvicorn running on http://0.0.0.0:8008
```

Keep this window open — do not close it.

> If you changed the Blender port in Step 2:
>
> **Windows CMD:**
> ```cmd
> set BLENDER_PORT=9001 && uvx mcpo --port 8008 -- uvx blender-mcp
> ```
> **Mac:**
> ```bash
> BLENDER_PORT=9001 uvx mcpo --port 8008 -- uvx blender-mcp
> ```

---

### Step 5 — Install & Configure Open WebUI Desktop

1. Download and install from [github.com/open-webui/desktop](https://github.com/open-webui/desktop/releases)
2. Open the app — the Open WebUI server starts automatically
3. Click **Open WebUI** in the sidebar to open the chat interface
4. Sign up / log in

---

### Step 6 — Connect Ollama to Open WebUI

Ollama is connected automatically at `http://localhost:11434`.

Verify: `Admin Panel → Settings → Connections → Ollama API` shows `http://localhost:11434` with a green status.

---

### Step 7 — Register blender-mcp as a Tool Server

1. `Admin Panel → Settings → Integrations`
2. Click **`+`** next to "Manage Tool Servers"
3. Fill in:
   - **Type:** OpenAPI
   - **Name:** Blender-MCP
   - **URL:** `http://localhost:8008`
   - **Auth:** None
4. Click **Save** — toggle should be green

---

### Step 8 — Configure Model for Tool Calling

1. `Admin Panel → Settings → Models`
2. Select your model (e.g. `nemotron-3-nano:30b-cloud`)
3. Under **Tools** → check **Blender-MCP**
4. Click **Advanced Params → Show**
5. Set **Function Calling** → `Native`
6. Save

---

## Usage

### Every Session Startup Order

#### Windows (CMD)
```
1. Ollama        — running in system tray (auto-starts)
2. Blender       — N panel → BlenderMCP → "Connect to MCP server"
3. CMD           — uvx mcpo --port 8008 -- uvx blender-mcp
4. Open WebUI    — open app, start chatting
```

#### Mac (Terminal)
```
1. Ollama        — running in menu bar (auto-starts)
2. Blender       — N panel → BlenderMCP → "Connect to MCP server"
3. Terminal      — uvx mcpo --port 8008 -- uvx blender-mcp
4. Open WebUI    — open app, start chatting
```

### Example Prompts

```
Create a red metallic cube at the origin
```
```
Add a point light above the scene and set its energy to 1000
```
```
Create a low poly mountain landscape
```
```
Get information about all objects in the current scene
```
```
Delete all objects and create a simple room with walls and a floor
```

---

## Recommended Models

Models with reliable tool calling support:

| Model | Size | Notes |
|---|---|---|
| `nemotron-3-super:cloud` | Cloud (120B MoE) | Best for complex agentic tasks |
| `nemotron-3-nano:30b-cloud` | Cloud (30B) | Best general results — runs on Ollama cloud |
| `qwen2.5:14b` | 14B | Best fully local option |
| `qwen3-coder:30b` | 30B | Best local option for scripting-heavy tasks |
| `llama3.1:8b` | 8B | Minimum viable, simpler tasks only |

> Models under 14B may struggle with complex multi-tool Blender operations.

### ⚠️ Important — Activating a New Model in Open WebUI

**A model must be run in Ollama at least once before it appears in Open WebUI's model list.**

**Windows (CMD):**
```cmd
ollama run nemotron-3-super:cloud
```

**Mac (Terminal):**
```bash
ollama run nemotron-3-super:cloud
```

Wait for the model to load and respond, then press `Ctrl+C` to exit. The model is now registered and will appear in Open WebUI's model dropdown.

Repeat this step for every new model you want to use.

---

## Troubleshooting

| Issue | Fix |
|---|---|
| Blender socket won't start | Change port to `9001` in addon preferences |
| `mcpo` command not found | Run `uv --version` — reinstall uv if missing |
| Tool not appearing in chat | Check Blender-MCP toggle is green in Integrations |
| Model ignores tools | Set Function Calling to `Native` in model Advanced Params |
| Commands not executing in Blender | Ensure "Connect to MCP server" is active (shows Disconnect button) |
| First command fails | Known issue — try sending the same prompt again |

---

## Project Structure

```
blender-ollama-mcp/
├── README.md           ← This file
├── LICENSE             ← MIT License
├── CONTRIBUTING.md     ← How to contribute
└── docs/
    ├── architecture.md ← Detailed architecture notes
    └── models.md       ← Model compatibility notes
```

---

## Credits

- [ahujasid/blender-mcp](https://github.com/ahujasid/blender-mcp) — Blender MCP server and addon
- [open-webui/open-webui](https://github.com/open-webui/open-webui) — Chat interface
- [open-webui/mcpo](https://github.com/open-webui/mcpo) — MCP to OpenAPI proxy
- [ollama/ollama](https://github.com/ollama/ollama) — Local LLM runtime

---

## License

MIT © 2026 [Gurudhevan](https://github.com/Gurudhevan) — see [LICENSE](LICENSE)
