# CrewAI Studio — Essentials


## What you need

| | |
| --- | --- |
| **OS** | Windows 10/11 (x64). The IDE is Windows-only; the code it generates is not. |
| **Python** | 3.10+ with `crewai` installed. Not the app's own runtime — the one it shells out to. |
| **A provider key** | One is enough. OpenAI, Anthropic, DeepSeek, Google, Groq, Mistral, xAI, Cohere… or Ollama for fully offline. |

```powershell
# A dedicated environment is worth it: crewai pulls ~150 packages.
conda create -n crewai python=3.12 -y
conda activate crewai
pip install crewai crewai-tools python-dotenv pydantic
```

---

## First five minutes

1. **Settings → Python** — point it at the interpreter that has `crewai`
   (`...\envs\crewai\python.exe`). This is stored **per machine**, so opening someone else's
   project does not change it.
2. **Settings → API Keys** — paste one provider key. Keys are encrypted with DPAPI and are
   stripped from `project.json` on save.
3. **File → Open Project → `samples/travel-blog-ko.json`** — a working three-agent crew.
   See [samples/README.md](samples/README.md).
4. **Run** — press it. The preflight states any missing precondition *before* the run instead of
   discovering it halfway through.
5. **Save Output…** — keeps the complete text. (The view shows the last 20,000 characters; the
   file and the Copy button take all of it.)

---

## The thirteen tabs that matter

The sidebar opens on **Essentials**, which is these — the rest is behind **All**.

**Design** → Dashboard · Templates · Agents · Tasks · Crews · Crew Canvas · Tools
**Try** → Playground
**Ship** → Code Gen · Run
**Watch** → Analytics · Cost Dashboard
**Configure** → Settings

---

## What it produces

Ten runnable files, syntax-verified against real parsers (`ast.parse`, PyYAML, `tomllib`):

`crew_pipeline.py` · `requirements.txt` · `Dockerfile` · `.env` · `tests.py` · `compose.yaml`
· CI workflow · `pyproject.toml` · `crewai.yaml` · flow code

It is ordinary CrewAI Python. It runs without this app.

---

## Four things that actually bite

**Each agent's answer language is a setting, not a hint.** Design → Agents → Language. It becomes
an instruction inside the generated `Agent(...)` backstory and inside the app's own calls, so a
Korean agent answers in Korean from generated code and from the Playground alike. `kr` — the
country code — resolves to `ko`.

**Crew Memory needs a second credential, and DeepSeek cannot provide it.** Memory embeds text, and
DeepSeek serves chat completions only — it has **no embeddings endpoint** (measured: `POST
/v1/embeddings` → 404). Use **Ollama** or **ONNX** (both local, no key), or turn Memory off. Four
of the eight embedders also need an optional pip package; the app names it before you run.

**Reasoning models spend the budget before they answer.** DeepSeek V4 thinks first. A small
`max_tokens` is consumed by that thinking and the step returns nothing, which CrewAI retries until
the crew fails. The app raises the budget for those models and says so in the generated file.

**Cost follows the task, not the model tier.** Measured on the sample: 79% of all completion tokens
were reasoning tokens, and the two steps on the cheaper model burned 85% of them. If a step is
open-ended, say how long the answer should be.

---

## When a run fails

The failure line names the cause and the tab that fixes it — a missing package, a rejected key, a
retired model id, a timeout. Take that route rather than regenerating: most failures are the
environment, not the project.

`Save Output…` works on failed runs too. That is usually the one worth keeping.
