# Meta Muse Glimmer 30B Locally — Prime Agent vs Hermes

![Model](https://img.shields.io/badge/Model-Muse%20Glimmer%2030B-7c3aed?style=for-the-badge)
![GPU](https://img.shields.io/badge/Tested%20on-RTX%203090-76b900?style=for-the-badge&logo=nvidia&logoColor=white)
![Runtime](https://img.shields.io/badge/Runtime-llama.cpp-2563eb?style=for-the-badge)
![Harnesses](https://img.shields.io/badge/Harnesses-Prime%20Agent%20%7C%20Hermes-f59e0b?style=for-the-badge)

 Run **Muse Glimmer 30B** locally through an OpenAI-compatible endpoint, connect it to **Prime Agent** and **Hermes**, and compare both harnesses under the same conditions.

---

## 📺 Watch the Video First

[![Watch on YouTube](https://img.shields.io/badge/▶_Watch_the_Full_Video-FF0000?style=for-the-badge&logo=youtube&logoColor=white)](https://www.youtube.com/watch?v=Lkdww0s_xxs)

### **Meta Muse Glimmer 30B Locally: Prime Agent vs Hermes on RTX 3090**

The video contains the complete walkthrough, performance measurements, architecture explanation, controlled test, workflow differences and final verdict.

 **This repository is intentionally a companion—not a replacement for the video.** It provides the essential commands and configuration references, while the complete benchmark prompt, test project and detailed results remain in the video.

---

## What Is Covered?

- Running Muse Glimmer 30B locally on an RTX 3090
- VRAM usage, generation speed and context support
- Prime Agent's persistent IPython and RLM workflow
- Connecting Prime Agent to a local OpenAI-compatible endpoint
- Connecting Hermes to the same model
- Comparing both harnesses with the same project and instructions

---

## Test Architecture

```text
                         ┌── Prime Agent
RTX 3090 → Muse Glimmer ─┤
      llama.cpp :8080    └── Hermes
```

Both harnesses use the same:

- Muse Glimmer model
- Local endpoint
- Context window
- Project copy
- User instructions

Only the **agent harness** changes.

---

## Requirements

| Component | Tested configuration |
|---|---|
| Operating system | Linux / WSL2 |
| GPU | NVIDIA RTX 3090, 24 GB VRAM |
| Runtime | llama.cpp / llama.app |
| API endpoint | `http://127.0.0.1:8080/v1` |
| Context used | `65,536` tokens |
| Harnesses | Prime Agent and Hermes Agent |

Different quantizations and context sizes can change memory usage and speed.

---

## 1. Start Muse Glimmer

Install or build [llama.cpp](https://github.com/ggml-org/llama.cpp), then start its OpenAI-compatible server:

```bash
llama serve \
  -hf meta-models/Muse-Glimmer-30B-GGUF \
  --host 127.0.0.1 \
  --port 8080 \
  -c 65536
```

Confirm the model ID exposed by the server:

```bash
curl http://127.0.0.1:8080/v1/models
```

Copy the exact model ID from this response. You will use it in both harnesses.

> Quantization selection, GPU offloading and performance tuning are demonstrated in the video.

---

## 2. Connect Prime Agent

Install Prime Agent:

```bash
curl -fsSL https://app.primeintellect.ai/prime-agent/install.sh | sh
```

Add a custom provider to:

```text
~/.prime/agent/models.json
```

```json
{
  "providers": {
    "local-muse": {
      "baseUrl": "http://127.0.0.1:8080/v1",
      "api": "openai-completions",
      "apiKey": "local",
      "compat": {
        "supportsDeveloperRole": false,
        "supportsReasoningEffort": false
      },
      "models": [
        {
          "id": "PASTE_MODEL_ID_FROM_V1_MODELS",
          "name": "Muse Glimmer 30B Local",
          "reasoning": false,
          "contextWindow": 65536,
          "maxTokens": 8192
        }
      ]
    }
  }
}
```

Validate the JSON and launch Prime Agent:

```bash
python -m json.tool ~/.prime/agent/models.json
prime-agent
```

---

## 3. Connect Hermes

Install Hermes Agent:

```bash
curl -fsSL https://hermes-agent.nousresearch.com/install.sh | bash
```

Start the configuration wizard:

```bash
hermes setup
```

Use these values:

| Setting | Value |
|---|---|
| Provider | Custom OpenAI-compatible endpoint |
| Base URL | `http://127.0.0.1:8080/v1` |
| API mode | `chat_completions` |
| API key | `local` |
| Model | Exact ID returned by `/v1/models` |
| Context length | `65536` |
| Terminal backend | Local |

Launch Hermes from the project directory:

```bash
hermes
```

---

## Controlled Comparison

The comparison used isolated copies of the same Python project. Both agents received identical instructions and were required to run the complete test suite before reporting completion.

The public video shows:

- The original failing tests
- Both complete agent runs
- Files modified by each harness
- Final test result
- Workflow, efficiency and transparency comparison

The full benchmark prompt and project are intentionally not reproduced here. This keeps the video as the primary practical walkthrough and avoids results being interpreted without the execution context.

---

## Important Notes

- Local model behavior depends on quantization, context size, runtime version and hardware.
- A successful test suite confirms only the behaviors covered by those tests.
- Both agents can execute model-generated commands with your user permissions.
- Use disposable project copies, review all changes and never expose an unauthenticated endpoint publicly.

---

## Official Resources

| Resource | Link |
|---|---|
| Muse Glimmer 30B GGUF | [Hugging Face](https://huggingface.co/meta-models/Muse-Glimmer-30B-GGUF) |
| Prime Agent | [GitHub](https://github.com/PrimeIntellect-ai/prime-agent) |
| Hermes Agent | [GitHub](https://github.com/NousResearch/hermes-agent) |
| llama.cpp | [GitHub](https://github.com/ggml-org/llama.cpp) |
| RTX 3090 specifications | [NVIDIA](https://www.nvidia.com/en-us/geforce/graphics-cards/30-series/rtx-3090-3090ti/) |

---

## Follow NetworkCoder

[![YouTube](https://img.shields.io/badge/YouTube-NetworkCoder-red?style=for-the-badge&logo=youtube)](https://www.youtube.com/@NetworkCoder)
[![GitHub](https://img.shields.io/badge/GitHub-network--tocoder-black?style=for-the-badge&logo=github)](https://github.com/network-tocoder)

---

<p align="center">
  <strong>Same model. Same project. Different harness.</strong><br/>
  <em>Watch the complete practical comparison before reproducing the setup.</em>
</p>
