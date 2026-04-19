# Contributing to TAUT

Thanks for thinking about contributing. The bar is low — TAUT is a single Markdown file and a benchmark harness; any concrete improvement is welcome.

## What kinds of contributions land fast

1. **Agent compatibility reports.** Tested TAUT on a CLI that's not in the supported-9 list? Open an issue with the [agent-compatibility template](./.github/ISSUE_TEMPLATE/agent-compatibility.md). Even "doesn't work" reports are valuable — they reveal harness-level constraints we don't know about.

2. **Bug reports.** A specific prompt where TAUT v0.13 produces non-compliant or wrong output? File via the [bug template](./.github/ISSUE_TEMPLATE/bug.md) with the agent name, prompt, expected output, and actual output.

3. **Rule-addition ideas.** Have a TAUT.md edit in mind for v0.14+? File via the [idea template](./.github/ISSUE_TEMPLATE/idea.md). The best ideas come with a predicted bench impact.

4. **PRs to TAUT.md.** Surgical edits with bench data attached land fastest. See [`EVOLUTION.md`](./EVOLUTION.md) §4-§5 for what's worked / what hasn't, so you don't repeat dead-ends.

## How to propose a TAUT.md change

1. Fork the repo and edit `TAUT.md`.
2. Bump the version line at the top (e.g. `(v0.13)` → `(v0.14)`).
3. *Ideally*: re-run the bench against your change. The harness lives at `bench/` (see [`BENCHMARKS.md`](./BENCHMARKS.md) §12 for layout). You don't strictly need to bench all 8 agents — even a 2-agent comparison (claude + the noisiest one for your change, e.g., cursor) is a useful signal.
4. Open a PR with the [PR template](./.github/PULL_REQUEST_TEMPLATE.md) filled out — bench delta + risk-of-regression notes.

## Running the benchmark

The benchmark harness is included for reproducibility. From the repo root:

```bash
# 1. Install tiktoken into a venv
python3 -m venv bench/.venv
bench/.venv/bin/pip install tiktoken

# 2. Deploy your edited TAUT.md to all agents
#    (use the install commands from README.md or AGENT-LOCATIONS.md)

# 3. Run one agent (sequentially, N=3 trials, ~3-5 min)
BENCH_DIR=$(pwd)/bench/v1.14 TRIALS=3 bash bench/scripts/run_agent.sh claude

# 4. Extract metrics
BENCH_DIR=$(pwd)/bench/v1.14 bench/.venv/bin/python bench/scripts/extract_metrics.py

# 5. Compare to baseline (v1, the no-TAUT bench)
LABEL_A="v1 (no TAUT)" LABEL_B="v1.14 (TAUT v0.14)" \
  bench/.venv/bin/python bench/scripts/compare.py bench/v1 bench/v1.14 \
  > bench/v1.14/comparison.md
```

For 8-agent parallel runs, see the launcher snippets in `EVOLUTION.md` §10.

## Code of conduct

Be a senior engineer briefing another senior engineer. (Drink your own champagne.)

## License

By contributing, you agree your contributions are licensed under the [MIT License](./LICENSE).
