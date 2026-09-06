# Track 1 Sprint Ledger

**Agenthon 2026 · Track 1 — Quant-Finance Coding Agents**

Three weeks, 5 hours a week each, for William, Sergey, and Koushik — 45 person-hours total, broken into 30–60 minute blocks. Target: a Docker image passing its own checks by **Sep 28**, the close of the development/validation window. `Sep 29–Oct 12` is the organizer's final-submission and verification window — held as slack, not counted below.

## Timeline

| Sep 7 | Sep 13 | Sep 20 | Sep 27 | Sep 28 | ⋯ Oct 12 |
|---|---|---|---|---|---|
| Kickoff | Wk 1 sync | Wk 2 sync | Wk 3 sync | **Target ready** | Submission close |

---

## Week 1 — Foundations
`Sep 7 – Sep 13`

Everyone reads the same ground truth, hand-solves a few units to learn what "correct" means per category, and a single-shot agent runs against the exemplar with zero Docker involved.

### William — Finance correctness (0 / 300 min)

- [ ] **60 min** — Read `AGENTS.md`, `docs/CONCEPTS.md`, `docs/CATEGORIES.md` end to end; note the 10 categories and their invariant patterns. `READ`
- [ ] **60 min** — Hand-solve `t1-cir-bond-pricing` (fixed-income) end to end; get the offline checker green. `FIXED-INCOME`
- [ ] **60 min** — Hand-solve `t1-var-es-estimation` (risk-management) end to end. `RISK MGMT`
- [ ] **45 min** — Hand-solve `t1-sma-crossover-spy` (backtesting) end to end. `BACKTESTING`
- [ ] **45 min** — Distill invariant patterns from the exemplar + the 3 units above into a one-page "self-check spec." `SPEC`
- [ ] **30 min** — Joint sync. `SYNC`

### Sergey — Agent architecture (0 / 300 min)

- [ ] **60 min** — Read `baselines/README.md` + `SUBMISSION_CLI.md`; note the solve verb, restricted network, budget, api vs byo-large. `READ`
- [ ] **60 min** — Clone and run mini-SWE-agent (or SWE-agent) on a toy task; study its loop shape. `SCAFFOLD`
- [ ] **60 min** — Design and stub a swappable model-client function (dev API now, `$MODEL_ENDPOINT` later). `ARCH`
- [ ] **45 min** — Build a minimal single-shot agent against the exemplar unit. `AGENT`
- [ ] **45 min** — Run it through the local pytest-checker loop (no Docker); fix first bugs. `DEBUG`
- [ ] **30 min** — Joint sync. `SYNC`

### Koushik — Task engineering & tracking (0 / 300 min)

- [ ] **45 min** — Read `README.md` + `docs/CATEGORIES.md`; build the team's unit-tracking sheet. `READ`
- [ ] **60 min** — Draft an `instruction.md` → structured-agent-input template using the exemplar as the worked example. `TEMPLATE`
- [ ] **60 min** — Read the exemplar's `checks/test_outputs.py` beside its `instruction.md`; write a "what correct means" rubric card. `RUBRIC`
- [ ] **60 min** — Skim 10–15 candidate units across categories; log difficulty/category into the tracker. `TRIAGE`
- [ ] **45 min** — Pick the first 6 practice units for weeks 2–3, balanced across categories. `PLANNING`
- [ ] **30 min** — Joint sync. `SYNC`

---

## Week 2 — The real loop
`Sep 14 – Sep 20`

The agent grows a multi-turn shape with self-repair and a token-budget guard, Docker packaging begins, and category coverage widens past the exemplar.

### William — Finance correctness (0 / 300 min)

- [ ] **45 min** — Hand-solve `t1-fx-forward-cross-rate` (fx). `FX`
- [ ] **45 min** — Hand-solve `t1-credit-spread-decomposition` (credit). `CREDIT`
- [ ] **60 min** — Write per-category "hint cards" — common mistakes from `docs/CATEGORIES.md` — for the 6 categories covered so far. `DOCS`
- [ ] **60 min** — Run Sergey's in-progress agent against 4 tracked units; diagnose why each failure happens and log the type. `DEBUG`
- [ ] **60 min** — Turn the self-check spec into per-category assertion snippets Sergey can wire in. `SPEC`
- [ ] **30 min** — Joint sync. `SYNC`

### Sergey — Agent architecture (0 / 300 min)

- [ ] **60 min** — Build the multi-turn loop: call → write output → run local checker → feed failure back → bounded retry. `AGENT`
- [ ] **60 min** — Wire a token-budget guard against the 1M-input/100K-output per-unit cap. `BUDGET`
- [ ] **60 min** — Start the Dockerfile: `FROM finance-bench-sandbox:latest`, thin layer, entrypoint, read `[agent].timeout_sec` from `card.toml`. `DOCKER`
- [ ] **60 min** — Run the non-Dockerized loop across the 6 tracked units; fix bugs found. `TESTING`
- [ ] **30 min** — Wire William's per-category hint snippets into the system prompt, keyed on `card.toml`'s category. `PROMPT`
- [ ] **30 min** — Joint sync. `SYNC`

### Koushik — Task engineering & tracking (0 / 300 min)

- [ ] **60 min** — Iterate the instruction-parsing template against 3 more units. `TEMPLATE`
- [ ] **60 min** — Log and categorize every failure William/Sergey report; surface the top 2 recurring types. `TRIAGE`
- [ ] **45 min** — Draft `submission.json` fields (category api, model + training-cutoff disclosure). `SUBMISSION`
- [ ] **45 min** — Re-check agenthon.net / CodaBench page for anything new. `TRACKING`
- [ ] **60 min** — Expand the tracked-unit list to 12+ spanning all 10 categories. `TRACKING`
- [ ] **30 min** — Joint sync. `SYNC`

---

## Week 3 — Full suite & freeze
`Sep 21 – Sep 27`

Full-suite pass, a real Docker image built and smoke-tested on a real machine, and submission paperwork ready before the Sep 28 target.

### William — Finance correctness (0 / 300 min)

- [ ] **60 min** — Full-suite pass: run the agent against all 12+ tracked units; log pass/fail per invariant category. `TESTING`
- [ ] **60 min** — For every failure, tighten the relevant hint card or self-check assertion — prompt-level, not code. `PROMPT`
- [ ] **60 min** — Hand-verify agent outputs on the 3 categories not yet double-checked. `VERIFY`
- [ ] **45 min** — Write the model/data-disclosure section of `submission.json` with Koushik. `SUBMISSION`
- [ ] **45 min** — Re-read `docs/QFBENCH-HERITAGE.md` + `AUTHORING-GUIDE.md` for any organizer amendments. `READ`
- [ ] **30 min** — Joint sync / go–no-go. `SYNC`

### Sergey — Agent architecture (0 / 300 min)

- [ ] **60 min** — Build `finance-bench-sandbox:latest` + the thin agent image on your own machine. `DOCKER`
- [ ] **60 min** — Run `qfbench2 smoke <unit> <out> --track coding --agent-image your-agent:latest` end to end on 4+ units; fix mount/path/env issues. `SMOKE TEST`
- [ ] **60 min** — Run the full local test matrix (12+ units) through the Dockerized agent; capture pass-rate. `TESTING`
- [ ] **60 min** — Freeze and tag the image, push to a registry; verify `sha256(image)` matches. `RELEASE`
- [ ] **30 min** — Fix whatever the pass-rate run turned up. `FIXES`
- [ ] **30 min** — Joint sync / go–no-go. `SYNC`

### Koushik — Task engineering & tracking (0 / 300 min)

- [ ] **60 min** — Consolidate the tracker into a final pass-rate report; flag anything still red. `REPORT`
- [ ] **60 min** — Register the image on the CodaBench competition page; verify `submission.json` is complete. `SUBMISSION`
- [ ] **45 min** — Triple-check budget/network-compliance language and model disclosure. `COMPLIANCE`
- [ ] **45 min** — Write a short internal README for the Sep 29–Oct 12 verification window. `DOCS`
- [ ] **60 min** — Buffer/catch-up block for whatever slipped this week. `BUFFER`
- [ ] **30 min** — Joint sync / go–no-go. `SYNC`

---

Each person: 5 hrs/week · 45 hrs total across the team over 3 weeks.
