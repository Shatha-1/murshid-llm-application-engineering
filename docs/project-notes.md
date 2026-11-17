# Project notes — Murshid Mini

## Idea and intended user

A fictional government-style service assistant for learners studying how LLM applications are structured. It answers two public questions, returns application status only to the record's owner, refuses everything else clearly, and proves its behavior with fixed tests. `FakeClient` stands in for a model so every run is free, offline and repeatable (`mode=mock`).

## Pipeline

```
input
 → guard_input        length limit, example injection strings, ten-digit ID pattern
 → extract_ticket     rule/regex extraction → {service, city, request_id, needs_clarification}
 → validate_ticket    exact keys, types, enums, missing-reference consistency
 → route              public answer | status lookup | clarification | refusal
 → check_status       read-only, ownership check against the session fixture
 → output check       response contract
 → response + metrics (trace_id, code, cache_hit, mock_calls, tool_calls, latency_ms)
```

## Components by day

| Day | Component | Role |
|---|---|---|
| 1 | `SERVICE_DIRECTORY`, `FakeClient.generate` | Finite lookup of two public answers; counts mock calls; never calls an API |
| 1 | `ask` | Baseline call returning `trace_id`, `answer`, `model`, `latency_ms`; blank input is rejected before the client |
| 2 | `extract_ticket`, `validate_ticket` | Turn free text into a validated structured ticket |
| 2 | `check_status` | Local lookup with ownership check; never mutates records |
| 3 | `build_prompt` | Versioned prompt template (documented artifact; the mock does not execute prompts) |
| 3 | `guard_input`, `run_pipeline` | Input guards and the end-to-end flow |
| 3 | `evaluate` | Ten golden cases with pass/fail reasons; no network or model judge |
| 4 | `run_app` | Exact public cache around the pipeline plus minimal metrics |

## Data and authorization fixture

| Reference | Owner | Status |
|---|---|---|
| `REF-1001` | `demo-user-1` | `under_review` |
| `REF-1002` | `demo-user-2` | `approved` |
| `REF-9999` | — | absent |

The session is `demo-user-1`. Knowing a reference number is not proof of ownership, so `REF-1002` returns `access_denied`. This is a teaching fixture, not authentication.

## Cache rules

- Only the two exact public FAQs are cacheable; status requests, guarded inputs and malformed tickets bypass the cache.
- Key: `(question, language, model, prompt_version, directory_version)` — changing the prompt or directory version invalidates old entries.
- TTL: 300 seconds. Entries store the answer and creation time only — no status or session data.
- Metrics log no questions, references, owners or raw answers.

## Tests

- 10 golden cases (Day 3), re-run after caching (Day 4): all pass.
- Cache experiment from a cleared cache: 3 → 1 mock calls, hits `[false, true, true]`.
- Deterministic: codes, counters, pass/fail. Variable: `trace_id`, `latency_ms`.

## Limitations

- No live model in the core project; latency is local Python time, not inference.
- Guards are examples, not a security or privacy control.
- 10/10 on fixed cases is not a general reliability guarantee.
- The cost figure in `final_report.json` uses invented prices; actual API spend is zero.
