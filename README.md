# Murshid Mini — LLM Application Engineering

A small, fictional service assistant built step by step over four days in the **LLM Application Engineering (SDA-AIE-213)** program. This repository holds one cumulative notebook that grows each day.

> **Mode: `mock`.** `FakeClient` is a finite, deterministic simulator — **not a live language model**. The project focuses on the engineering around an LLM-shaped interface. All data is synthetic.

## Progress

| Day | Focus | Notebook cells | Status |
|---|---|---:|---|
| 1 · Foundations | Synthetic directory, `FakeClient`, `ask`, response contract | 1–3 | ✅ Done |
| 2 · Structure and tools | Validated ticket, read-only status lookup, ownership check | 4–6 | Upcoming |
| 3 · Guard and test | Versioned prompt, input guards, pipeline, golden cases | 7–10 | Upcoming |
| 4 · Measure and finish | Public cache, metrics, regression, export | 11–14 | Upcoming |

## Day 1 — Foundations

**What was built**

- **Synthetic service directory** — two public answers (Arabic and English) plus a clarification fallback.
- **`FakeClient`** — a deterministic lookup that returns a known answer and counts mock calls. It never calls an API.
- **`ask(question)`** — wraps the client and returns a fixed response contract.
- **Blank-input check** — empty or whitespace-only input is rejected before the client is called, so it adds no mock call.

**Response contract**

```json
{
  "trace_id": "uuid4 string",
  "answer": "text",
  "model": "training-mock",
  "latency_ms": 0.01
}
```

**Results** — [`results/core/baseline_results.json`](results/core/baseline_results.json)

| Input | Answer |
|---|---|
| `كيف أتابع طلبي؟` | يمكنك متابعة حالة طلبك باستخدام رقم المرجع. |
| `How can I check my application status?` | You can check your application status using its reference number. |
| Vague request `ساعدني` | ما الخدمة التي تحتاج مساعدة بشأنها؟ (clarification) |

Blank input adds no mock call. `trace_id` and `latency_ms` change on every run; the answers and the model label are deterministic. Latency measures local Python execution, not model inference.

## How to run

No installation, API key, GPU or download needed — Python 3.9+ standard library only.

Open [`notebooks/murshid.ipynb`](notebooks/murshid.ipynb) in Google Colab (File → Open notebook → GitHub), select a CPU runtime, and run all cells from the top. Outputs are written to `murshid_outputs/`.

## Limitations

- No live language model; answers come from a hand-written mapping.
- Only a few known questions are supported at this stage.

## Attribution

Built as part of **LLM Application Engineering — SDA-AIE-213**, delivered within the [SDAIA Academy](https://github.com/SDAIAAcademy) training context, instructor **Meaad Al-Marri**. Based on the course's [reference materials](https://github.com/almiyead-rgb/murshid-llm-application-engineering-sda-aie-213). This repository does not claim SDAIA endorsement or certification.
