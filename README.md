# Murshid Mini — LLM Application Engineering

A small, fictional service assistant built step by step over four days in the **LLM Application Engineering (SDA-AIE-213)** program. It answers known public service questions, looks up application status through an authorized read-only tool, rejects unsuitable inputs, tests its own behavior against fixed cases, and caches public answers safely.

> **Mode: `mock`.** `FakeClient` is a finite, deterministic simulator — **not a live language model**. The core project demonstrates the engineering around an LLM-shaped interface (validation, tools, authorization, guards, testing, caching, measurement). All data is synthetic.

## Notebooks

| Notebook | What it is | Open |
|---|---|---|
| [`notebooks/murshid.ipynb`](notebooks/murshid.ipynb) | Core project, cumulative through Day 4 | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Shatha-1/murshid-llm-application-engineering/blob/main/notebooks/murshid.ipynb) |
| [`homework/day1_real_model.ipynb`](homework/day1_real_model.ipynb) | Day 1 homework — real model (FLAN-T5 small) | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Shatha-1/murshid-llm-application-engineering/blob/main/homework/day1_real_model.ipynb) |
| [`homework/day2_tools.ipynb`](homework/day2_tools.ipynb) | Day 2 homework — validate and dispatch tool calls | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Shatha-1/murshid-llm-application-engineering/blob/main/homework/day2_tools.ipynb) |
| [`homework/day3_errors.ipynb`](homework/day3_errors.ipynb) | Day 3 homework — intentional errors and corrected input | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Shatha-1/murshid-llm-application-engineering/blob/main/homework/day3_errors.ipynb) |
| [`homework/day4_evaluation.ipynb`](homework/day4_evaluation.ipynb) | Day 4 homework — evaluate three real-model answers | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Shatha-1/murshid-llm-application-engineering/blob/main/homework/day4_evaluation.ipynb) |

## Repository structure

```text
murshid-llm-application-engineering/
├── README.md                       Project overview, results, structure and how to run (this file)
├── requirements.txt                Core project needs no packages — Python 3.9+ standard library only
├── .gitignore                      Keeps caches, Colab output folders, ZIPs and model weights out of Git
│
├── notebooks/
│   └── murshid.ipynb               Core project — one cumulative notebook, Days 1–4 (14 code cells, all outputs saved)
│
├── homework/                       One separate notebook per day, never mixed into the core project
│   ├── day1_real_model.ipynb       Runs google/flan-t5-small on CPU and reports answer, tokens and time
│   ├── day2_tools.ipynb            Validates and dispatches structured tool calls (only check_status allowed)
│   ├── day3_errors.ipynb           Triggers expected input errors and shows the corrected input
│   └── day4_evaluation.ipynb       Generates answers to three paraphrased questions and evaluates them
│
├── results/                        JSON outputs saved from the runs
│   ├── core/                       Written by notebooks/murshid.ipynb
│   │   ├── baseline_results.json   Day 1 — three answers from ask() with trace_id, model and latency
│   │   ├── day2_results.json       Day 2 — five status scenarios: ticket, tool result and code
│   │   ├── day3_results.json       Day 3 — ten golden cases with pass/fail and reasons
│   │   ├── day4_results.json       Day 4 — cache experiment: mock calls without/with cache, hit sequence
│   │   ├── metrics.json            Day 4 — per-request metrics (code, cache_hit, mock/tool calls, latency); no questions, references or answers
│   │   ├── synthetic_data.json     The synthetic directory and status records used by the project
│   │   └── final_report.json       Final summary: golden tests, cache experiment, illustrative cost, limits
│   ├── day1/live_demo_result.json  Day 1 homework — real-model answer, tokens, inference time
│   ├── day2/tools_result.json      Day 2 homework — 7 tool-call cases and records_unchanged
│   ├── day3/errors_result.json     Day 3 homework — 3 caught errors and the accepted corrected input
│   └── day4/evaluation_result.json Day 4 homework — 3 generated answers, keyword hits, tokens and time
│
└── docs/
    └── project-notes.md            Technical notes: pipeline, components by day, data fixture, cache rules, tests, limits
```

### Inside the core notebook

Each code cell starts with a `# DAY N - Cell X:` label; the first cell of the file is the Open in Colab badge.

| Cell | Day | What it does |
|---:|---|---|
| 1 | 1 | Synthetic service directory, `save_json` helper and `FakeClient` (finite lookup, counts mock calls) |
| 2 | 1 | `ask()` — the baseline app and its response contract |
| 3 | 1 | Runs three questions, checks blank input adds no mock call, saves `baseline_results.json` |
| 4 | 2 | Synthetic status records, `extract_ticket` and `validate_ticket` |
| 5 | 2 | Read-only `check_status` with the ownership check, and `route` |
| 6 | 2 | Five scenarios plus a Day 1 re-check, saves `day2_results.json` |
| 7 | 3 | Versioned `build_prompt`, `guard_input` and `validate_response` |
| 8 | 3 | `run_pipeline` — the guarded end-to-end flow |
| 9 | 3 | Ten golden cases and `evaluate` with pass/fail reasons |
| 10 | 3 | Saves the tested milestone, `day3_results.json` |
| 11 | 4 | `run_app` — exact cache for the two public FAQs plus metrics |
| 12 | 4 | Before/after cache experiment; checks version invalidation, TTL expiry, and that status requests bypass the cache and leave records unchanged |
| 13 | 4 | Regression (10/10), illustrative cost; saves `day4_results.json`, `final_report.json`, `metrics.json`, `synthetic_data.json` |
| 14 | 4 | Packs the JSON outputs into `murshid_submission.zip` |

Running the notebook writes the same files to `murshid_outputs/` (ignored by Git) and packs them into `murshid_submission.zip`. Codes, counters and pass/fail results are deterministic, so a new run reproduces `results/core/`; only `trace_id` and `latency_ms` differ.

## Four-day development journey

| Day | Built | Notebook cells | Result |
|---|---|---:|---|
| 1 · Foundations | Synthetic directory, `FakeClient`, `ask`, response contract | 1–3 | Three known answers; blank input adds no mock call |
| 2 · Structure and tools | Validated ticket, read-only `check_status`, ownership check | 4–6 | Five scenarios; no unauthorized disclosure or mutation |
| 3 · Guard and test | Versioned prompt, input guards, pipeline, ten golden cases | 7–10 | **10/10** golden cases pass |
| 4 · Measure and finish | Public exact cache, metrics, regression, export | 11–14 | Mock calls **3 → 1**; cache hit rate **66.7%** |

Each day is a separate commit on the same notebook path, so the history shows the project growing.

## Pipeline

```
input → input guard → ticket extraction → schema / business-rule validation
      → public mock answer  OR  read-only status tool (ownership check)
      → output validation → response + minimal metrics
```

Day 4 wraps only the two public FAQs in an exact cache keyed by `(question, language, model, prompt_version, directory_version)` with a 300-second TTL. Status requests, malformed tickets and guarded requests bypass the cache. Details: [docs/project-notes.md](docs/project-notes.md).

## What it does — and does not do

| Does | Does not |
|---|---|
| Answer two public FAQs (Arabic and English) | Call any real model or API |
| Return application status only to the owning session | Implement real authentication |
| Validate a structured ticket (keys, types, enums) | Perform general NLP — extraction is rule-based |
| Block example injection strings, long input and ID-like numbers | Provide comprehensive security or privacy detection |
| Run 10 fixed behavioral test cases | Measure real LLM quality |
| Cache the two public FAQs with versioned keys and a TTL | Claim real speedups or cost savings |

## Dataset

Hand-written synthetic teaching fixtures embedded in the code. No real records or personal data.

| Reference | Owner | Status | Purpose |
|---|---|---|---|
| `REF-1001` | `demo-user-1` | `under_review` | Owner can see status |
| `REF-1002` | `demo-user-2` | `approved` | Access-denied test for `demo-user-1` |
| `REF-9999` | — | absent | Not-found test |

The session fixture is `demo-user-1`. Knowing a reference number does not prove ownership.

## Results by day

### Day 1 — Foundations

[`results/core/baseline_results.json`](results/core/baseline_results.json)

| Input | Answer |
|---|---|
| `كيف أتابع طلبي؟` | يمكنك متابعة حالة طلبك باستخدام رقم المرجع. |
| `How can I check my application status?` | You can check your application status using its reference number. |
| Vague request `ساعدني` | ما الخدمة التي تحتاج مساعدة بشأنها؟ (clarification) |

Blank input is rejected before the client is called, so it adds no mock call. Response contract: `trace_id`, `answer`, `model`, `latency_ms`.

### Day 2 — Structure and tools

[`results/core/day2_results.json`](results/core/day2_results.json)

| Request | Code |
|---|---|
| Status of `REF-1001` (own record) | `under_review` |
| Status request with no reference | `needs_clarification` |
| Status of `REF-1002` (another user) | `access_denied` |
| Status of `REF-9999` | `not_found` |
| Request to change an application | `unsupported_action` — the demo is read-only |

### Day 3 — Guard and test

[`results/core/day3_results.json`](results/core/day3_results.json) — **10 / 10** golden cases pass: `ok` ×2, `under_review`, `not_found`, `needs_clarification`, `out_of_scope`, `access_denied`, `blocked_instruction`, `invalid_input`, `invalid_schema`. A correct refusal counts as a pass when the request should be rejected.

### Day 4 — Measure and finish

[`results/core/day4_results.json`](results/core/day4_results.json) · [`results/core/final_report.json`](results/core/final_report.json)

| Check | Result |
|---|---|
| Mock calls for 3 identical FAQs — without / with cache | 3 / 1 |
| Cache hit sequence | miss, hit, hit (66.7%) |
| Regression after caching | 10 / 10 still pass |
| Actual external API calls | 0 |

Trace IDs and latency change between runs; status codes, counters and pass/fail results are deterministic. The token-cost figure in the final report uses invented prices and is illustrative only.

## Homework

### Day 1 — a real model

[`results/day1/live_demo_result.json`](results/day1/live_demo_result.json) — `google/flan-t5-small` run locally on a Colab CPU (no API key, no hosted inference).

| Field | Value |
|---|---|
| Question | How can I check my application status? |
| Generated answer | Use its reference number |
| Input / output tokens | 34 / 6 |
| Inference time | 0.223 s (CPU; varies between runs) |

**Review:** the answer tells the user to use the reference number and invents no fees, dates or status.

**Input → tokens → model → answer:** the prompt (context + question) is split into 34 input tokens, and FLAN-T5 small generates the answer one token at a time (6 output tokens) from its learned weights, so the wording can vary and must be reviewed. `FakeClient`, by contrast, returns a hand-written answer from a fixed mapping without generating anything.

**Setup note:** the pinned `transformers==4.49.0` / `sentencepiece==0.2.0` failed to install on Colab's Python 3.13, so the setup cell uses Colab's preinstalled `torch` 2.14.1 and `transformers` 5.22.1 instead.

### Day 2 — controlled tool calls

[`results/day2/tools_result.json`](results/day2/tools_result.json) — **7 / 7** cases pass and records stay unchanged. The dispatcher accepts only `check_status`, validates the call shape and arguments, and checks ownership: own record → `ok`; another user's or a missing record → `unavailable` (same answer, so existence is not leaked); other tools → `tool_not_allowed`; bad arguments → `invalid_arguments`; malformed call → `invalid_call`.

### Day 3 — expected errors

[`results/day3/errors_result.json`](results/day3/errors_result.json) — `None`, blank text and a 501-character question are each rejected with a caught `ValueError`, and a corrected question is accepted. A caught rejection is a successful test here: the validator did its job.

### Day 4 — evaluate three real-model answers

[`results/day4/evaluation_result.json`](results/day4/evaluation_result.json) — `google/flan-t5-small` on CPU, greedy decoding (`do_sample=False`, `num_beams=1`, `max_new_tokens=48`), same context for three paraphrased questions.

| ID | Question | Answer | Keyword hit | Tokens in / out | Time |
|---|---|---|:---:|---|---|
| E01 | How can I check my application status? | Use its reference number | ✓ | 34 / 6 | 0.556 s |
| E02 | What do I need to check my application status? | Reference number | ✓ | 36 / 4 | 3.198 s |
| E03 | How can I find out the status of my application? | Use its reference number | ✓ | 37 / 6 | 2.156 s |

**Keyword indicator: 3 / 3.** It only checks that `reference` and `number` both appear — a weak proxy, not semantic accuracy.

**Manual review:** all three answers have the right meaning. E02 is shorter but correctly answers "what do I need". None invents fees, dates or an application status. Paraphrasing did not change the substance of the answer, but three cases on one context are far too few to claim robustness, and the timings vary with Colab load.

## How to run

Core project: no installation, API key, GPU or download needed — Python 3.9+ standard library only. Click **Open in Colab** above, choose a CPU runtime, and run all cells from the top. Outputs are written to `murshid_outputs/`.

Real-model homework (Days 1 and 4) needs `torch`, `transformers` and `sentencepiece` (preinstalled on Colab) and downloads `google/flan-t5-small` from Hugging Face on first run; allow a few minutes. Days 2 and 3 homework use the standard library only.

## Limitations

- No live language model in the core project; routing is finite and rule-based.
- Guards are educational examples, not a security control.
- No real authentication — ownership comes from a fixture.
- 10/10 on fixed cases is not a general reliability guarantee.
- A small English real-model example is not evidence of Arabic service quality.

## Attribution

Built as part of **LLM Application Engineering — SDA-AIE-213**, delivered within the [SDAIA Academy](https://github.com/SDAIAAcademy) training context, instructor **Meaad Al-Marri**. Based on the course's [reference materials](https://github.com/almiyead-rgb/murshid-llm-application-engineering-sda-aie-213). This repository does not claim SDAIA endorsement or certification.
