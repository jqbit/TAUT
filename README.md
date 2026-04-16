# TAUT — Terse Agent Communication Mode

> A single-file system prompt that compresses coding-agent prose output by **80 % on average** across 8 different agent harnesses, while keeping a professional senior-engineer register. No fine-tuning. No model swap. No harness change. One Markdown file dropped into each agent's global instruction slot.

---

## Headline result

| Agent | Prose tokens (no TAUT) | Prose tokens (TAUT v0.13) | Δ % | Compliance |
|---|---:|---:|---:|---:|
| **gemini** | 3 854 | 156 | **−96.0 %** | 100 % |
| **droid** | 3 735 | 532 | −85.8 % | 100 % |
| **pi** | 2 736 | 574 | −79.0 % | 100 % |
| **openclaw** | 1 382 | 296 | −78.6 % | 100 % |
| **codex** | 2 765 | 624 | −77.4 % | 93 % |
| **claude** | 1 680 | 400 | −76.2 % | 100 % |
| **hermes** | 4 958 | 1 228 | −75.2 % | 87 % * |
| **cursor-agent** | 4 616 | 1 323 | −71.3 % | 93 % |
| **TOTAL** | **25 726** | **5 133** | **−80.0 %** | avg 96.7 % |

*\* hermes ceiling at 87 % is harness-bounded — its CLI binary forcibly emits diff views and `session_id:` trailers that no prompt can suppress. See [`EVOLUTION.md`](./EVOLUTION.md) §7.*

Bench: 8 agents × 15 prompts × 3 trials per cell (gemini and openclaw N=1). Same suite, same harness, same `tiktoken o200k_base` tokenizer. **Beats caveman's published 65 % average by 15 percentage points.**

---

## What is TAUT?

TAUT is a ~9 KB Markdown system prompt that, when loaded as a coding agent's global instruction file, makes the agent communicate like a senior engineer briefing another senior engineer instead of like a chatbot trying to be helpful. It cuts preamble, hedges, redundant explanation, structural padding (headers, tables, bold-on-everything), tool-use narration, and unsolicited "when to use which" closers — without sacrificing accuracy or readability.

Drop it into your agent's global config. Restart your terminal session. Your agent now writes like a peer.

---

## Quick install (3 commands)

```bash
# 1. From this folder, run the deploy script (handles all 8 agents)
bash <(curl -s https://raw.githubusercontent.com/<YOUR-FORK>/TAUT/main/deploy.sh)

# OR manually for the 6 simple-overwrite targets:
for dest in ~/.claude/CLAUDE.md ~/.codex/AGENTS.md ~/.gemini/GEMINI.md \
            ~/.factory/AGENTS.md ~/.pi/agent/AGENTS.md ~/AGENTS.md; do
  mkdir -p "$(dirname "$dest")"
  cp TAUT.md "$dest"
done

# 2. For openclaw + hermes (preserve their existing system prompt, append TAUT):
# See AGENT-LOCATIONS.md for the surgical-append snippet.

# 3. Verify
for p in ~/.claude/CLAUDE.md ~/.codex/AGENTS.md ~/.gemini/GEMINI.md \
         ~/.factory/AGENTS.md ~/.pi/agent/AGENTS.md \
         ~/.openclaw/workspace/AGENTS.md ~/.hermes/SOUL.md ~/AGENTS.md; do
  grep -q "Mode (v0.13)" "$p" && echo "✓ $p" || echo "✗ $p"
done
```

Smoke test:

```bash
claude -p "What's the git command to undo the last commit but keep changes staged?"
# Should print exactly:  `git reset --soft HEAD~1`
# (no preamble, no trailing prose)
```

If you see `Use \`git reset --soft HEAD~1\`. This will move HEAD back…` — the file isn't loading. Check the verification output for the missing `✓`.

---

## What's in this folder

| File | Purpose |
|---|---|
| **[`README.md`](./README.md)** | This file — entry point + headline numbers |
| **[`TAUT.md`](./TAUT.md)** | The actual prompt. Drop this into each agent's global config. |
| **[`AGENT-LOCATIONS.md`](./AGENT-LOCATIONS.md)** | Where exactly to deploy `TAUT.md` for each of the 8 supported agents (paths + overwrite-vs-append modes), plus a one-shot deploy script. |
| **[`PHILOSOPHY.md`](./PHILOSOPHY.md)** | The deep paper. Why output-side compression matters in 2026. Why we built TAUT instead of just using caveman. ML grounding with cited research. Methodology, results, limitations. |
| **[`EVOLUTION.md`](./EVOLUTION.md)** | The version-by-version journey from v0.1 (−34 %, 27 % compliance floor) to v0.13 (−80 %, 87 % compliance floor). What worked, what didn't, with proper ML keywords. The variance-shrinkage story (66 pp → 13 pp gap = 5× cross-agent variance reduction). Includes the full per-version per-agent matrices for plotting. |
| **[`BENCHMARKS.md`](./BENCHMARKS.md)** | Raw data dump for the data nerds. Every per-version, per-agent, per-prompt number. Structural-metric reductions. Trap-pattern hits. Sample responses (qualitative comparison). Designed for downstream visualisation, statistical analysis, or independent verification. |

---

## Origin & credit

TAUT is built on the work of **caveman** by [Julius Brussee](https://github.com/JuliusBrussee/caveman). Caveman was the first widely-shared prompt to take output-side compression seriously, with a published ~65 % average reduction across 30+ test prompts. Without caveman, TAUT does not exist.

TAUT diverges from caveman on one specific axis: instead of instructing the model to "be a caveman" (which causes some models to *personify* the persona — emitting "ugh, caveman ready", "ooga booga project info?", and similar character-roleplay artefacts that burn tokens, drift latent representations toward a stereotyped subspace, and read as unprofessional in production contexts), TAUT describes the *register* of a senior engineer briefing another senior engineer. The compression mechanics are imported wholesale from caveman; the persona is replaced.

The full design rationale is in [`PHILOSOPHY.md`](./PHILOSOPHY.md) §3.

---

## Supported agents (8)

```
1. claude         — Claude Code (Anthropic)         → ~/.claude/CLAUDE.md
2. codex          — OpenAI Codex CLI                → ~/.codex/AGENTS.md
3. gemini         — Google Gemini CLI               → ~/.gemini/GEMINI.md
4. droid          — Factory Droid                   → ~/.factory/AGENTS.md
5. pi             — Pi Coding Agent                 → ~/.pi/agent/AGENTS.md
6. cursor-agent   — Cursor CLI                      → ~/AGENTS.md
7. openclaw       — OpenClaw TUI                    → ~/.openclaw/workspace/AGENTS.md  (append)
8. hermes         — Hermes Agent (Nous)             → ~/.hermes/SOUL.md                (append)
```

See [`AGENT-LOCATIONS.md`](./AGENT-LOCATIONS.md) for the per-agent details.

---

## How TAUT compares to caveman

| | caveman Ultra | TAUT v0.13 |
|---|---|---|
| Average prose-token reduction | ~65 % (published) | **80 %** (measured) |
| Cross-agent variance | not reported | 13 pp compliance spread |
| Register | "caveman" persona | senior-engineer voice |
| Persona collapse risk | yes (documented "ooga booga" failures) | no (no character invoked) |
| Hard numerical caps | no | yes (per-prompt-shape token budgets) |
| Hard response templates | no | yes (verbatim scripted responses for high-variance shapes) |
| Production-suitable register | with `--brief` mode | by default |
| Additive-compatible scope clause | yes | yes |
| Bench breadth | Claude API + tiktoken | 8 agents × 15 prompts × N=3 trials |

Both are valid choices. caveman optimises for compression and accepts the persona cost. TAUT optimises for *production deployability across heterogeneous toolchains* and accepts a slightly larger prompt file.

---

## Versioning

Current: **v0.13** (final shipped).

The version is encoded in the title line of `TAUT.md`:

```
# TAUT — Terse Agent Communication Mode (v0.13)
```

This makes deployment verification a one-liner: `grep "Mode (v0.13)" ~/.claude/CLAUDE.md`.

---

## What if it doesn't work for me?

1. **Verify the file is loading**: run the verification snippet above. Every agent's global file should contain the v0.13 marker.
2. **Restart your agent session**: most CLIs read the global instruction file at session start, not on every turn.
3. **Smoke test with a one-liner factual prompt** (e.g., the git-undo example). If you get the bare command, TAUT is loaded. If you get prose around it, the file isn't being read.
4. **Check for conflicts with project-local instructions**: some agents (cursor, droid, pi) walk the cwd up looking for an additional `AGENTS.md`. A project-local file may override TAUT.
5. **Hermes users**: expect ~87 % compliance. The hermes CLI emits a diff view and `session_id:` trailer that TAUT cannot suppress. See [`EVOLUTION.md`](./EVOLUTION.md) §7.

If you're seeing genuine non-compliance (long preamble, verbose explanations, tool-use narration), file an issue with: agent name, agent version, the prompt, the response, and the output of the verification command.

---

## License

MIT (placeholder — adjust before public release). TAUT is open source and freely usable in commercial and personal projects.

Caveman by Julius Brussee is also open source; see [github.com/JuliusBrussee/caveman](https://github.com/JuliusBrussee/caveman) for its license.

---

## Citation

If you use TAUT in research, benchmark publications, or production deployments, please cite:

```
TAUT — Terse Agent Communication Mode (v0.13).
8-agent cross-harness compression benchmark, April 2026.
Inspired by caveman (Julius Brussee, 2026).
```

For the full methodology, citations, and ML grounding, see [`PHILOSOPHY.md`](./PHILOSOPHY.md).

---

## Repo skeleton (when promoted to GitHub)

```
TAUT/
├── README.md              ← you are here
├── TAUT.md                ← the prompt
├── AGENT-LOCATIONS.md     ← deploy reference
├── PHILOSOPHY.md          ← design rationale + ML grounding
├── EVOLUTION.md           ← version-by-version journey + lessons
├── BENCHMARKS.md          ← raw data dump for plotting / analysis
├── deploy.sh              ← (optional) one-shot deploy script
└── bench/                 ← (optional) benchmark harness + raw data
    ├── prompts/prompts.json
    ├── scripts/run_one.py
    ├── scripts/run_agent.sh
    ├── scripts/extract_metrics.py
    ├── scripts/compare.py
    └── results/v1.13/
```

The `bench/` directory is optional but recommended for reproducibility — the full per-version raw response data lives at `bench/v1*/` on the development machine.

---

*Built with iteration, measurement, and respect for the reader's time.*
