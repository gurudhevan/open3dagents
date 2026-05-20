# Architecture

## Component Overview

| Component | Role | Repo |
|---|---|---|
| **Ollama** | LLM runtime — serves local and cloud models via REST API at `:11434` | [ollama/ollama](https://github.com/ollama/ollama) |
| **Open WebUI Desktop** | Chat interface + MCP client (v0.9.5+) | [open-webui/desktop](https://github.com/open-webui/desktop) |
| **mcpo** | Converts stdio MCP servers to HTTP/OpenAPI endpoints | [open-webui/mcpo](https://github.com/open-webui/mcpo) |
| **blender-mcp** | MCP server — translates tool calls into Blender bpy commands | [ahujasid/blender-mcp](https://github.com/ahujasid/blender-mcp) |
| **Blender Addon** | TCP socket server inside Blender — receives and executes bpy commands | same repo |

---

## Data Flow

```
User types prompt in Open WebUI
        │
        ▼
Open WebUI sends prompt to Ollama (:11434)
        │
        ▼
Ollama model generates response + tool call JSON
        │
        ▼
Open WebUI detects tool call → sends HTTP POST to mcpo (:8008)
        │
        ▼
mcpo forwards to blender-mcp via stdio
        │
        ▼
blender-mcp sends JSON command to Blender addon via TCP (:9876)
        │
        ▼
Blender addon executes bpy Python code
        │
        ▼
Result returned up the chain → displayed in chat
```

---

## Port Reference

| Port | Service | Notes |
|---|---|---|
| `:11434` | Ollama REST API | Auto-starts with Ollama |
| `:8080` | Open WebUI server | Auto-starts with desktop app |
| `:8008` | mcpo proxy (HTTP) | Must be started manually each session |
| `:9876` | Blender addon TCP socket | Must be started manually each session |

> **Windows port conflict:** If `:9876` fails, change to `:9001` in Blender addon preferences and set `BLENDER_PORT=9001` when starting mcpo.

---

## OS Support

| OS | Supported | Terminal |
|---|---|---|
| Windows 10/11 | ✅ | CMD |
| macOS 11+ | ✅ | Terminal (bash/zsh) |
| Linux | ⚠️ Untested | bash |

---

## Why mcpo Is Needed

Open WebUI communicates with tools over **HTTP (OpenAPI)**. The `blender-mcp` server communicates over **stdio** (standard input/output pipes). These are incompatible transports.

`mcpo` acts as a bridge — it starts `blender-mcp` as a child process, wraps its stdio interface, and exposes all its tools as standard REST endpoints that Open WebUI can call.

---

## Why Not Use Ollama's Chat UI Directly

Ollama's built-in desktop chat UI does not support MCP tool calling as of May 2026. Open WebUI is required as the MCP client layer. This is a known open GitHub issue on the Ollama repo.

---

## Python Dependency

**Python does not need to be installed.** All components manage their own runtimes:

| Component | Runtime |
|---|---|
| Ollama | Go binary — self-contained |
| Open WebUI Desktop | Bundled Node/Python runtime |
| uv / uvx | Rust binary — manages Python internally |
| blender-mcp / mcpo | Run via uvx in isolated environments |
| Blender addon | Uses Blender's bundled Python (bpy) |
