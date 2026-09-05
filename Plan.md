# Agenthon 2026 — Track 1 (Quant-Finance Coding Agents) Prep Plan

Team: William (Applied Math + Information Systems, Finance minor), Sergey (Applied Math + CS), Koushik (Information Systems + Business Management). None of the three have significant prior LLM/agent-building experience.

## Competition facts (verified 2026-08-28 from agenthon.net)

- Four tracks: T1 Quant-Finance Coding Agents, T2 Reasoning-Augmented Time Series, T3 Accelerated Market Simulation, T4 Evidence-Grounded Prediction. We are entering T1 only for now.
- T1 spec: "Build a Docker agent that solves quantitative finance coding tasks under pytest and financial-invariant checks." Scored pass@1 / pass@3.
- Submission = a Docker image implementing one stable CLI verb. It goes through a 4-gate admissibility check (g0–g3: integrity, schema, cutoff/resource compliance, domain semantics) before it receives a track metric + bootstrap confidence interval.
- Public practice repos (baselines, smoke scorers, docs) open 2026-08-28. Private sealed exam repo holds the held-out tasks + oracle solutions.
- Track 1 lead: Zhikang Dong.
- Sponsors providing infra: NVIDIA, Nebius — worth checking if compute/API credits are offered to registered teams.
- Timeline: Registration Aug 17–Sep 28 · Development Aug 28–Sep 28 (validation leaderboard live) · Final submission Sep 29–Oct 12 · Verification Oct 13–Oct 25 · NeurIPS workshop presentations Dec 9–13, Atlanta.
- Reference last year's problem style via the "Questions 2025" link on the site once accessible.
- Full rules: agenthon.net/terms (Terms of Participation) — read before building anything.

Open items the public site didn't spell out (confirm via FAQ / practice repo / organizer contact once available): which LLM providers/models are permitted, any per-run token/cost budget, exact task input format, and how pass@3 attempts are structured.

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
3. **Docker refresher**: Wenyuan flagged as new to Docker (see /areas/job-market-tracker.md-adjacent note) — a short Docker course/tutorial covering building an image and a CLI entrypoint covers what's needed here.
4. **Build a minimal end-to-end baseline** against the practice repo as soon as it's usable: get *something* through all four gates (g0–g3) and onto the validation leaderboard, even with a low score. Establishing the full pipeline early de-risks the rest of the month.
5. **Iterate on agent quality**: better prompts, self-verification (agent runs pytest itself before returning), handling multiple attempts for pass@3, staying inside resource/token budgets.

## Immediate next steps

- [ ] Read agenthon.net in full (all sections/anchors), the Terms of Participation, and the FAQ section once the team can access practice repos.
- [ ] Get LLM API keys (Claude and/or OpenAI) sorted for the team; check whether Agenthon/NVIDIA/Nebius offer any credits to registered teams.
- [ ] Pull down the Track 1 public practice repo as soon as available (opened 2026-08-28) and read its baseline + smoke scorer.
- [ ] Pick a reference agent scaffold to study (mini-SWE-agent / SWE-agent / OpenHands) and get it running locally on a toy task.
- [ ] Get a trivial "hello world" Docker CLI verb submission through the g0–g3 gates before optimizing anything.
