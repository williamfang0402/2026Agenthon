# Agenthon 2026 — Track 1 (Quant-Finance Coding Agents) Prep Plan

Team: William (Applied Math + Information Systems, Finance minor), Sergey (Applied Math + CS), Koushik (Information Systems + Business Management). None of the three have significant prior LLM/agent-building experience.

## Update 2026-09-06 — verified directly from the public practice repo

Cloned and worked hands-on in `github.com/Agenthon-2026/track1-coding-public` (the public starter
kit that opened 2026-08-28). This supersedes some of the assumptions below — most importantly the
model-access plan.

**Model access is NOT "get API keys."** There are no vendor API keys at all in official scoring —
the sandbox runs on a `restricted` network whose only egress is an audited proxy to one
organizer-hosted, OpenAI-compatible model endpoint (`$MODEL_ENDPOINT` / `$MODEL_NAME`). Calls to
`api.anthropic.com`, `api.openai.com`, etc. are refused by the proxy and no participant keys are
injected — there's nothing for a vendor key to reach. Two submission categories, one leaderboard:
- **`api`** — your whole contribution is the agent/prompt/harness; you call the free house model
  through `$MODEL_ENDPOINT`. This is almost certainly what we should build first.
- **`byo-large`** (legacy name, no separate small tier) — you ship a LoRA adapter (rank ≤ 64) that
  the organizer loads onto the base model; no weights, no model server of your own.
- A uniform budget applies either way: **1,000,000 input + 100,000 output tokens per unit** (ruled
  2026-08-28), audited via proxy logs.
- We can (and should) prototype now against real, free-tier LLM APIs (Claude/OpenAI/etc.) for
  development speed, since local dev doesn't touch the eval network — just remember the *submitted*
  agent must talk only to `$MODEL_ENDPOINT`, so keep the model client swappable (one function that
  takes a base_url/model name) rather than hardcoding a vendor SDK.

**What a submission actually is.** A Docker image, built `FROM finance-bench-sandbox:latest` (the
shared base — numpy/pandas/scipy/pyarrow/pytest already baked in, built once from
`docker/sandbox.Dockerfile`), implementing exactly one CLI verb:
`solve --task-dir /input --out /app/output` (some units' instructions say `/output` instead — the
harness binds both to the same host dir, so write to whichever the unit's `instruction.md` names).
Per-unit time limit is declared in that unit's own `card.toml` (`[agent].timeout_sec`, ranges
1200–5400s across the 87 public units, 1800s most common) — always read the card, it's
authoritative over any prose. 16 vCPUs / 128GB RAM / GPU available per run.

**Scoring.** Four sequential admissibility gates, `g0_integrity → g1_schema → g2_cutoff_resource →
g3_domain_semantics` — g3 is "did `checks/test_outputs.py` pass" (the financial-invariant tests).
Only a full pass earns `score = 1.0`. **Leaderboard metric is pass@1 only** (one execution per
task, no confidence interval); pass@3 with bootstrap CIs exists only in the offline Harbor dev
report, not the official ranking. **There is no baseline agent to beat** — we're ranked against
other teams' entries on the hidden `private-test` split.

**The practice set.** 87 public-dev units under `units/`, spanning 10 categories (derivatives-
pricing, fixed-income, credit, factor-research, backtesting, risk-management, microstructure, fx,
nlp-on-finance, cross-domain — full writeups with worked examples and invariant tables in
`docs/CATEGORIES.md`). The canonical worked exemplar is `units/t1-EXAMPLE-bs-greeks-pde` (Black-
Scholes Greeks via a finite-difference PDE) — read its `card.toml`, `instruction.md`, and
`checks/test_outputs.py` before writing anything; it's the reference for what our agent will see
and how it's graded on every unit.

**Validated local dev loop (no Docker needed to iterate on logic):**
1. `python3.13 -m venv .venv && source .venv/bin/activate`
2. `pip install "qfbench2-common @ git+https://github.com/Agenthon-2026/Agenthon2026-public.git@v2.3.1#subdirectory=common"`
3. From the repo root: `pip install -e .` (installs `qfbench2_track_coding`, gives you the `qfbench2` CLI)
4. `qfbench2 card validate units/<unit-id>` — confirmed working (schema-validates card.toml)
5. Stage `/input/card.toml` + `/input/environment/data/...` yourself, write your solver's output to
   an `/output/` dir, then run the unit's checker directly:
   `OUTPUT_DIR=/output python -m pytest units/<unit-id>/checks/test_outputs.py --tb=short -q`
   — **confirmed working end-to-end**: wrote a plain closed-form Black-Scholes solver for the
   exemplar and it passed 14/15 checks (1 skipped — no bumped-spot rows in this data split) with
   zero Docker involved. This is the fast loop for iterating on the agent's *reasoning/code*
   before ever touching Docker: get the invariants passing locally, then Dockerize.
6. Full Docker packaging (`docker build -f docker/sandbox.Dockerfile .` for the shared base, then a
   thin per-unit image) needs to happen on a team member's own machine, a cloud VM, or CI —
   Docker Hub isn't reachable from Claude's own cloud sandbox (org egress policy), so that step
   can't be validated from here.

**Immediate next steps (revised):**
- [x] ~~Get LLM API keys sorted~~ — not needed for the submission itself (house endpoint only);
      keep API keys only for *local development/prototyping* convenience.
- [x] Pull down the public practice repo — done, cloned and explored.
- [x] Read `docs/CONCEPTS.md`, `docs/CATEGORIES.md`, `README.md`, `AGENTS.md`, `baselines/README.md`,
      `SUBMISSION_CLI.md` — done.
- [x] Get a trivial baseline through the checks — done for the exemplar, offline, no Docker.
- [ ] Pick 2-3 more units across different categories (e.g. one `fixed-income`, one `backtesting`,
      one `risk-management`) and hand-write baseline solutions to build intuition for how varied
      the invariant checks get before designing the general agent loop.
- [ ] Pick a reference agent scaffold to study (mini-SWE-agent / SWE-agent / OpenHands) — still
      open — and adapt its model-calling layer to point at a swappable endpoint (house model in
      prod, any dev API locally).
- [ ] Build `finance-bench-sandbox:latest` and a first thin agent image on a real machine (not this
      cloud sandbox) and run `qfbench2 smoke units/t1-EXAMPLE-bs-greeks-pde <out> --track coding
      --agent-image your-agent:latest` end-to-end through Docker.
- [ ] Check CodaBench competition page (linked from the site) for the actual submission mechanics
      once we're ready to register an image.
- [ ] Read `agenthon.net/terms` in full and the shared-toolkit `AGENTS.md` /
      `docs/GLOSSARY.md` in `Agenthon-2026/Agenthon2026-public`.

---

## Competition facts (verified 2026-08-28 from agenthon.net)

- Four tracks: T1 Quant-Finance Coding Agents, T2 Reasoning-Augmented Time Series, T3 Accelerated Market Simulation, T4 Evidence-Grounded Prediction. We are entering T1 only for now.
- T1 spec: "Build a Docker agent that solves quantitative finance coding tasks under pytest and financial-invariant checks." Scored pass@1 / pass@3 (see update above: official leaderboard is pass@1 only).
- Submission = a Docker image implementing one stable CLI verb. It goes through a 4-gate admissibility check (g0–g3: integrity, schema, cutoff/resource compliance, domain semantics) before it receives a track metric + bootstrap confidence interval (dev-report only, see update above).
- Public practice repos (baselines, smoke scorers, docs) open 2026-08-28. Private sealed exam repo holds the held-out tasks + oracle solutions.
- Track 1 lead: Zhikang Dong.
- Sponsors providing infra: NVIDIA, Nebius — worth checking if compute/API credits are offered to registered teams.
- Timeline: Registration Aug 17–Sep 28 · Development Aug 28–Sep 28 (validation leaderboard live) · Final submission Sep 29–Oct 12 · Verification Oct 13–Oct 25 · NeurIPS workshop presentations Dec 9–13, Atlanta.
- Reference last year's problem style via the "Questions 2025" link on the site once accessible.
- Full rules: agenthon.net/terms (Terms of Participation) — read before building anything.

Open items the public site didn't spell out (confirm via FAQ / practice repo / organizer contact once available): exact task input format nuances beyond what's now confirmed in the repo (see update above for what's already answered — model access, budget, verb, gates).

## What "Track 1" actually is, in practice

This is effectively a SWE-bench-style coding-agent challenge scoped to quant finance: the agent receives a natural-language task (e.g., implement a signal, fix a backtest bug, compute a risk metric correctly), must autonomously read/write code, run tests against itself, and iterate — packaged so the whole thing runs unattended inside a container. The hard part isn't inventing agent theory; it's the engineering loop (plan → write code → run pytest → read failures → retry) plus getting the financial logic right (no lookahead bias, correct invariants like weights summing to 1, correct Sharpe/return calculations, etc.).

## Suggested role split (based on backgrounds)

- **Sergey (Applied Math + CS)**: agent architecture — the LLM tool-calling loop, sandboxed code execution, Docker packaging, CLI verb implementation, CI harness against practice tasks.
- **Koushik (Info Systems + Business Mgmt)**: task/prompt engineering, turning finance problem statements into structured agent inputs, documentation, tracking leaderboard submissions and deadlines, evaluating output correctness against the rubric.
- **William**: quant-finance domain correctness (financial invariants, backtest logic — leverages existing momentum-backtester and pytest experience), bridging agent output with the financial-invariant checks, working with the practice repo's test suite.

All three should learn baseline agent concepts together (below) — this is a small enough surface area that no one needs to specialize before the others understand it.

## Learning path (compressed, ~1 week to first working baseline)

1. **LLM basics + tool/function calling** (1-2 days): read Anthropic's "Building effective agents" writeup and either the Claude API or OpenAI API docs on tool use. This is the single concept the whole agent depends on.
2. **Study an existing coding-agent reference implementation** rather than building the loop from scratch: mini-SWE-agent, SWE-agent, or OpenHands are open-source and solve a very similar problem (LLM + repo + tests, containerized). Reading one is faster than inventing the architecture.
3. **Docker refresher**: William flagged as new to Docker (see /areas/job-market-tracker.md-adjacent note) — a short Docker course/tutorial covering building an image and a CLI entrypoint covers what's needed here. (Note: build/test the image on a real machine, not inside Claude's own cloud sandbox — see update above.)
4. **Build a minimal end-to-end baseline** against the practice repo as soon as it's usable: get *something* through all four gates (g0–g3) and onto the validation leaderboard, even with a low score. Establishing the full pipeline early de-risks the rest of the month. **(Partially done — see update above: the offline checker loop is validated for the exemplar; Docker packaging is the remaining piece.)**
5. **Iterate on agent quality**: better prompts, self-verification (agent runs pytest itself before returning), handling multiple attempts for pass@3 (dev-report metric — the leaderboard itself is pass@1), staying inside the 1M-input/100K-output token budget per unit.

## Immediate next steps (original — see revised checklist above for current status)

- [ ] Read agenthon.net in full (all sections/anchors), the Terms of Participation, and the FAQ section once the team can access practice repos.
- [ ] Get LLM API keys (Claude and/or OpenAI) sorted for the team; check whether Agenthon/NVIDIA/Nebius offer any credits to registered teams. *(Superseded — see update above: not needed for the submission itself, only useful for local dev convenience.)*
- [x] Pull down the Track 1 public practice repo as soon as available (opened 2026-08-28) and read its baseline + smoke scorer.
- [ ] Pick a reference agent scaffold to study (mini-SWE-agent / SWE-agent / OpenHands) and get it running locally on a toy task.
- [x] Get a trivial "hello world" Docker CLI verb submission through the g0–g3 gates before optimizing anything. *(Done for the pytest/g3 layer without Docker; full g0-g3 through an actual image is still open — needs a real machine.)*
