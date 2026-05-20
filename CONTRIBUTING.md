# Contributing to Blender AI Control

Contributions are welcome. Please read this before submitting.

---

## Ways to Contribute

- **Bug reports** — open an Issue with steps to reproduce
- **Model compatibility** — tested a new model? Add it to `docs/models.md`
- **OS compatibility** — Windows and Mac are tested; Linux results welcome
- **Prompt examples** — great prompts that produce reliable Blender results
- **Documentation** — improve clarity, fix typos, add screenshots
- **Troubleshooting** — document new issues and fixes you discover

---

## Reporting Bugs

Open an Issue and include:

| Field | Example |
|---|---|
| OS | Windows 11 / macOS 14 |
| Blender version | 4.2 |
| Ollama version | `ollama --version` output |
| Model used | `nemotron-3-nano:30b-cloud` |
| Exact prompt | "Create a red cube at origin" |
| Expected result | Cube appears in viewport |
| Actual result | No response / error message |
| CMD/Terminal output | Paste from mcpo window |

---

## Pull Requests

1. Fork the repo
2. Create a branch: `git checkout -b fix/your-description`
3. Make your changes
4. Submit a PR with a clear description of what changed and why

---

## Scope

This repo is a **setup guide and configuration** — not a custom codebase. PRs that modify `addon.py` or `blender-mcp` source code should be directed to upstream:

- Blender addon + MCP server → [ahujasid/blender-mcp](https://github.com/ahujasid/blender-mcp)
- mcpo proxy → [open-webui/mcpo](https://github.com/open-webui/mcpo)
- Open WebUI → [open-webui/open-webui](https://github.com/open-webui/open-webui)
- Ollama → [ollama/ollama](https://github.com/ollama/ollama)

---

## Adding a Model to docs/models.md

To add a tested model, include:

- Ollama model name (exact string)
- Hardware used (GPU/VRAM or cloud)
- Tool calling reliability (Excellent / Good / Inconsistent / Fails)
- Specific Blender tasks tested
- Any known issues

---

## Code of Conduct

Be respectful. Keep discussions focused on the project.
