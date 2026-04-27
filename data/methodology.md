# STFU.md v0.14 — bench methodology

## Goal

Quantify how much STFU.md reduces an agent's prose output (in tokens) without degrading tool-use, code correctness, or reasoning. Keep the prompt < 1 500 chars.

## Axes

1. **Harness** — 11 CLIs: claude, codex, copilot, droid, hermes, opencode, openclaw, pi, cline, agent (cursor), gemini. 9 routed via `ollama launch <h> --model <m>`; agent + gemini on native backends.
2. **Model** (where applicable) — 6 Tier-A Ollama cloud models: kimi-k2.6, deepseek-v4-flash, gemini-3-flash-preview, qwen3-coder-next, glm-5.1, minimax-m2.7. Tier-B (6 more) screened on demand.
3. **Prompt** — 15 prompts (Q01..Q15) spanning shapes (one-liner, greet, error, debug, comparison, how-to, tool-use, recap, regex-only, open-ended, etc.) with per-prompt token caps.
4. **Condition** — `baseline` (STFU.md moved aside via `mv <slot> <slot>.bak`) vs `stfu` (STFU.md in place).
5. **Trial** — N=2 per cell (N=3 used when σ > 25 % of mean).

## Measurement

- Cell output captured to file with `timeout` wrapper.
- Tokenizer: `tiktoken` `o200k_base`.
- Prose-only count: strip ANSI escapes, harness banners (config-mod messages, TUI frames, copilot footer, codex session/header lines), and fenced code blocks. If stripping fences leaves < 3 chars, the fence content WAS the answer (one-liner) — count fence content stripped of backticks.
- Per-cell token = mean across N trials.
- Per-harness reduction % = `(sum_baseline - sum_stfu) / sum_baseline * 100`.
- Compliance = fraction of cells whose mean tok ≤ shape cap.

## Sanity gates

Two prompts validate the "communication-only" guarantee:
- **Q08** — write `/tmp/groupby.py` and run a smoke test. Tool-use must succeed.
- **Q13** — output IPv4 regex only, no prose. The output must parse as a regex AND contain no prose lines.

Variants failing either gate are auto-rejected.

## Lexicographic selection metric (A/B promotion)

For each candidate variant, compute:
1. `compliance_rate` (must equal 100 %)
2. `Q08_tool_ok AND Q13_regex_valid`
3. `reduction_pct` (higher better)
4. `σ / mean` (lower better — stability)
5. `1 / char_count` (shorter better — terseness)
6. `wall_time_median` (lower better — inference cost proxy)

The variant that wins on 1, then 2, then 3, ... is promoted.

## Harness invocation cheat-sheet

| harness | non-interactive command |
|---|---|
| claude | `ollama launch claude --model <m> -y -- -p "<p>"` |
| codex | `ollama launch codex --model <m> -y -- exec --skip-git-repo-check "<p>"` |
| copilot | `ollama launch copilot --model <m> -y -- -p "<p>" --allow-all-tools` |
| droid | `ollama launch droid --model <m> -y -- exec --auto medium "<p>"` |
| hermes | `ollama launch hermes --model <m> -y -- chat -q "<p>"` |
| opencode | `ollama launch opencode --model <m> -y -- run "<p>"` |
| openclaw | `printf "/new\n<p>\n" \| timeout 240 script -qc "ollama launch openclaw --model <m> -y -- tui" /dev/null` |
| pi | `ollama launch pi --model <m> -y -- -p "<p>"` |
| cline | `ollama launch cline --model <m> -y -- -y "<p>"` |
| agent (cursor) | `agent --yolo --model gpt-5.3-codex -p "<p>"` |
| gemini | `gemini -p "<p>" --yolo` |

## Reproducibility

- `bench/v0.14-bench.sh` — baseline + STFU.md phases, per-harness streams.
- `bench/tokenize.js` — strip + tokenize one log.
- `bench/analyze.js` — per-harness reduction + compliance + cell matrix.
- `bench/make-charts.js` — emit SVG bar/heatmap/line charts.

Sample run: `cd bench && N_TRIALS=3 bash v0.14-bench.sh && node analyze.js && node make-charts.js`.
