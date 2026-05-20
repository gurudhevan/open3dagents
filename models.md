# Model Compatibility

## Requirements

For MCP tool calling to work, the model must support **native function calling** (also called tool use). Models that only support text generation will not trigger Blender tool calls.

Set **Function Calling → Native** in Open WebUI's model Advanced Params for every model you use.

---

## Recommended Models

### Cloud Models (via Ollama Cloud — no local GPU needed)

| Model | Ollama Command | Size | Tool Calling | Best For |
|---|---|---|---|---|
| `nemotron-3-super:cloud` | `ollama run nemotron-3-super:cloud` | 120B MoE | ✅ Excellent | Complex multi-step scenes, agentic tasks |
| `nemotron-3-nano:30b-cloud` | `ollama run nemotron-3-nano:30b-cloud` | 30B | ✅ Reliable | General Blender control, everyday use |

### Local Models (run on your machine)

| Model | Ollama Command | VRAM | Tool Calling | Best For |
|---|---|---|---|---|
| `qwen3-coder:30b` | `ollama run qwen3-coder:30b` | ~20GB | ✅ Excellent | Scripting-heavy and code-driven tasks |
| `qwen2.5:14b` | `ollama run qwen2.5:14b` | ~10GB | ✅ Good | Best balance of size and reliability |
| `qwen2.5:7b` | `ollama run qwen2.5:7b` | ~5GB | ⚠️ Inconsistent | Simple single-tool tasks only |

> Models under 14B may struggle with complex multi-tool Blender operations and frequently fail to generate valid tool call JSON.

---

## Minimum Recommendation

- **Cloud:** `nemotron-3-nano:30b-cloud` — free, no GPU required
- **Local:** `qwen2.5:14b` — best balance of reliability and hardware requirements

---

## Adding a New Model

### Step 1 — Activate in Ollama first

> ⚠️ **A model must be run in Ollama at least once before it appears in Open WebUI's model list.** Simply pulling is not enough — you must run it.

**Windows (CMD):**
```cmd
ollama run nemotron-3-super:cloud
```

**Mac (Terminal):**
```bash
ollama run nemotron-3-super:cloud
```

Wait for the model to load and respond, then press `Ctrl+C` to exit. The model is now registered and will appear in Open WebUI's model dropdown.

### Step 2 — Configure in Open WebUI

1. Select the model from the chat dropdown in Open WebUI
2. `Admin Panel → Settings → Models` → edit the model
3. Under **Tools** → check **Blender-MCP**
4. Click **Advanced Params → Show**
5. Set **Function Calling** → `Native`
6. Save

---

## Model Notes

**Nemotron (NVIDIA):**
- `nemotron-3-super` is a 120B Mixture-of-Experts model — activates only 12B parameters per token, making it fast despite its size
- Purpose-built for agentic tool calling and multi-step workflows
- Runs entirely on Ollama's cloud infrastructure — free to use

**Qwen (Alibaba):**
- `qwen3-coder` is RL-trained on code and agentic tasks — highly reliable for Blender scripting
- `qwen2.5:14b` is the most practical local option for most hardware setups
- Both support native function calling out of the box via Ollama

---

## Community Results

Tested a model not listed here? Open a PR to add it to this table with your findings.
