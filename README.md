<div align="center">

# TAUT 🪡

**TAUT** *(taut, adj.)* — pulled tight; not slack.
*Also a backronym: **T**erse **A**gent **U**tterance **T**uning.*

### A one-file system prompt that cuts AI coding-agent output by 80%

No fine-tuning. No API change. No harness change. **Just one Markdown file** dropped into your agent's global instruction slot.

[![release](https://img.shields.io/github/v/release/jqbit/TAUT?style=flat-square&color=blue&label=release)](https://github.com/jqbit/TAUT/releases)
[![license](https://img.shields.io/badge/license-MIT-green?style=flat-square)](./LICENSE)
[![agents](https://img.shields.io/badge/agents-8-orange?style=flat-square)](#-supported-agents)
[![reduction](https://img.shields.io/badge/output_reduction-80%25-red?style=flat-square)](#-benchmark)
[![compliance](https://img.shields.io/badge/avg_compliance-96.7%25-brightgreen?style=flat-square)](#-benchmark)
[![stars](https://img.shields.io/github/stars/jqbit/TAUT?style=flat-square&color=yellow)](https://github.com/jqbit/TAUT/stargazers)
[![last commit](https://img.shields.io/github/last-commit/jqbit/TAUT?style=flat-square)](https://github.com/jqbit/TAUT/commits/main)

**Works with:** Claude Code · OpenAI Codex · Google Gemini · Factory Droid · Cursor Agent · Pi · Hermes · OpenClaw

</div>

---

## ⚡ Why TAUT?

Modern AI coding agents are RLHF-trained to be *helpful*, which makes them **verbose by default** — preambles, hedges, "when to use which" closers, security postscripts you didn't ask for. Output tokens are 3–5× the price of input. Latency scales linearly with output length. Reading 600 tokens to extract one command costs the most expensive token rate of all: **your attention**.

**TAUT fixes that.** One file. Eight agents. Eighty-percent reduction. Production-safe register.

---

## 📊 Benchmark

| Agent | Prose tokens (no TAUT) | With TAUT v0.13 | Δ % | Compliance |
|---|---:|---:|---:|---:|
| **gemini** | 3 854 | 156 | **−96.0 %** | 100 % |
| **droid** | 3 735 | 532 | −85.8 % | 100 % |
| **pi** | 2 736 | 574 | −79.0 % | 100 % |
| **openclaw** | 1 382 | 296 | −78.6 % | 100 % |
| **codex** | 2 765 | 624 | −77.4 % | 93 % |
| **claude** | 1 680 | 400 | −76.2 % | 100 % |
| **hermes** | 4 958 | 1 228 | −75.2 % | 87 % * |
| **cursor-agent** | 4 616 | 1 323 | −71.3 % | 93 % |
| **TOTAL** | **25 726** | **5 133** | **−80.0 %** | avg **96.7 %** |

> 8 agents · 15 prompts · N=3 trials per cell · `tiktoken o200k_base` cross-agent fair tokenizer · ~3 900 measured responses across v0.1 → v0.13.

**Beats [caveman](https://github.com/JuliusBrussee/caveman)'s published 65% reduction by 15 percentage points** — while keeping a professional senior-engineer register (no persona collapse, no "ooga booga" responses, no character roleplay). [Full design rationale →](./PHILOSOPHY.md#3-the-caveman-personification-problem-the-reason-taut-exists)

\* hermes ceiling is harness-bounded; its CLI emits diff views + `session_id:` trailers no prompt can suppress. [Details →](./EVOLUTION.md#8-the-hermes-structural-ceiling)

---

## 🚀 Install — pick your agent, run one line

Each command downloads `TAUT.md` straight from this repo and writes it to the right path. No clone. No script. Just `curl`.

### Single-agent installs

```bash
# Claude Code
mkdir -p ~/.claude && curl -fsSL https://raw.githubusercontent.com/jqbit/TAUT/main/TAUT.md -o ~/.claude/CLAUDE.md

# OpenAI Codex
mkdir -p ~/.codex && curl -fsSL https://raw.githubusercontent.com/jqbit/TAUT/main/TAUT.md -o ~/.codex/AGENTS.md

# Google Gemini
mkdir -p ~/.gemini && curl -fsSL https://raw.githubusercontent.com/jqbit/TAUT/main/TAUT.md -o ~/.gemini/GEMINI.md

# Factory Droid
mkdir -p ~/.factory && curl -fsSL https://raw.githubusercontent.com/jqbit/TAUT/main/TAUT.md -o ~/.factory/AGENTS.md

# Pi Coding Agent
mkdir -p ~/.pi/agent && curl -fsSL https://raw.githubusercontent.com/jqbit/TAUT/main/TAUT.md -o ~/.pi/agent/AGENTS.md

# Cursor CLI
curl -fsSL https://raw.githubusercontent.com/jqbit/TAUT/main/TAUT.md -o ~/AGENTS.md
```

<details>
<summary><b>All-six-overwrite-agents at once</b></summary>

```bash
TAUT_URL=https://raw.githubusercontent.com/jqbit/TAUT/main/TAUT.md
for d in ~/.claude/CLAUDE.md ~/.codex/AGENTS.md ~/.gemini/GEMINI.md ~/.factory/AGENTS.md ~/.pi/agent/AGENTS.md ~/AGENTS.md; do
  mkdir -p "$(dirname "$d")" && curl -fsSL "$TAUT_URL" -o "$d"
done
```
</details>

<details>
<summary><b>OpenClaw + Hermes (append-mode — preserves their existing system prompt)</b></summary>

```bash
TAUT_URL=https://raw.githubusercontent.com/jqbit/TAUT/main/TAUT.md
TARGET=~/.hermes/SOUL.md   # or ~/.openclaw/workspace/AGENTS.md
mkdir -p "$(dirname "$TARGET")" && touch "$TARGET"
grep -q "TAUT — Terse Agent Communication Mode" "$TARGET" || \
  { printf '\n\n---\n\n' >> "$TARGET" && curl -fsSL "$TAUT_URL" >> "$TARGET"; }
```

Run it once for each of the two append-mode agents. Re-running is safe — won't duplicate.
</details>

### Smoke test (any agent)

```bash
claude -p "What's the git command to undo the last commit but keep changes staged?"
# expect: `git reset --soft HEAD~1`   (and nothing else)
```

If you see a paragraph instead of one line, the file isn't loading. See [`AGENT-LOCATIONS.md`](./AGENT-LOCATIONS.md#verification-command).

---

## 📁 What's inside

| File | Purpose |
|---|---|
| [`TAUT.md`](./TAUT.md) | The prompt itself |
| [`AGENT-LOCATIONS.md`](./AGENT-LOCATIONS.md) | Per-agent deploy paths + verification |
| [`PHILOSOPHY.md`](./PHILOSOPHY.md) | Design rationale, ML grounding, cited research |
| [`EVOLUTION.md`](./EVOLUTION.md) | v0.1 → v0.13 journey, what worked, what didn't |
| [`BENCHMARKS.md`](./BENCHMARKS.md) | Full raw data — every per-version per-agent number |
| [`CHANGELOG.md`](./CHANGELOG.md) | Version-by-version headline metrics |
| [`CONTRIBUTING.md`](./CONTRIBUTING.md) | How to add agents, run benchmarks, propose changes |

---

## 🤖 Supported agents

```
1. Claude Code (Anthropic)        →  ~/.claude/CLAUDE.md
2. OpenAI Codex CLI               →  ~/.codex/AGENTS.md
3. Google Gemini CLI              →  ~/.gemini/GEMINI.md
4. Factory Droid                  →  ~/.factory/AGENTS.md
5. Pi Coding Agent                →  ~/.pi/agent/AGENTS.md
6. Cursor CLI                     →  ~/AGENTS.md
7. OpenClaw TUI                   →  ~/.openclaw/workspace/AGENTS.md  (append)
8. Hermes Agent (Nous Research)   →  ~/.hermes/SOUL.md                (append)
```

Same file, eight agents, near-uniform behaviour. **5× reduction in cross-agent variance** (66 pp baseline compliance spread → 13 pp with TAUT). See [`EVOLUTION.md`](./EVOLUTION.md#7-the-variance-shrinkage-story-centerpiece) §7.

---

## 💡 What TAUT actually does

- **Hard numerical caps** on response length per prompt-shape (one-liner ≤ 25 tokens, comparison ≤ 70, best-practices list ≤ 120, etc.)
- **Hard response templates** for under-specified prompts ("Fix this bug." → *"Need code or error first."*)
- **Structural caps** (headers, tables, bold, bullets, emoji)
- **Anti-helpfulness rule** — no unsolicited security postscripts, no "when to use which" closers
- **Tool-use silence** — code/file writes execute without narration
- **Self-trim & draft-then-halve** rules — meta-cognitive compression loops

---

## ❓ FAQ

<details>
<summary><b>Will TAUT make my agent dumber?</b></summary>

No — and there's published evidence brevity *improves* accuracy. The "Brevity Constraints Reverse Performance Hierarchies in Language Models" paper (cited in [`PHILOSOPHY.md`](./PHILOSOPHY.md) §6) found that constraining models to brief responses improved accuracy by up to 26 percentage points on certain benchmarks. Verbose answers give the model more room to be wrong, contradict itself, or wander. Tighter answers stay on-target.
</details>

<details>
<summary><b>How is this different from just saying "be concise"?</b></summary>

"Be concise" has no measurable success criterion. TAUT specifies **numerical caps per prompt-shape** ("max 16 words per sentence", "comparison = 1 sentence pick + 0 supporting clauses"), **hard response templates** for high-variance prompts, and **structural caps** (max 1 bullet list, ≤6 words per bullet). RLHF-trained models follow checkable rules ~25 percentage points better than they follow stylistic suggestions. See [`EVOLUTION.md`](./EVOLUTION.md#3-key-inflection-points) §3.
</details>

<details>
<summary><b>Why not just use caveman?</b></summary>

Caveman's compression mechanics work — they're the foundation TAUT builds on. The problem is the *caveman persona*. Under heavy "caveman" framing, several models personify the character ("ugh, caveman ready", "ooga booga project info?"). This burns tokens on roleplay, drifts latent representations toward a stereotyped subspace ([Shanahan et al., *Nature*, 2023](https://www.nature.com/articles/s41586-023-06647-8)), and reads as unprofessional in production contexts. TAUT keeps caveman's compression and replaces the persona with a senior-engineer register. Full rationale in [`PHILOSOPHY.md`](./PHILOSOPHY.md#3-the-caveman-personification-problem-the-reason-taut-exists) §3.
</details>

<details>
<summary><b>Does TAUT work on agents that aren't in your list?</b></summary>

Probably yes, with caveats. TAUT.md is a generic system prompt — any agent that loads a global Markdown instruction file will read it. The 8 agents tested are just the ones we benched. If you try it on another agent (Aider, Continue, Sweep, etc.), please [open a compatibility report](./.github/ISSUE_TEMPLATE/agent-compatibility.md) — these reports are valuable.
</details>

<details>
<summary><b>What if I want it on only some prompts, not always?</b></summary>

TAUT has built-in override clauses: it explicitly resumes full verbosity for security warnings, destructive-action confirmations, or when the user says "I don't understand". You can also just say *"verbose mode"* or *"disable TAUT"* in any turn. See `TAUT.md` §Override + §Persistence.
</details>

<details>
<summary><b>Why is hermes only at 87% compliance?</b></summary>

Hermes's CLI binary forcibly emits two artefacts on coding tasks: a `┊ review diff` block showing the unified diff of any file written, and a `session_id: <id>` trailer on every response. Both appear in the response stream because they're injected by hermes's CLI layer between the LLM's output and the user — TAUT can instruct the LLM not to generate them, but the CLI appends them anyway. Tested across v0.9 → v0.13 with explicit anti-metadata rules; none worked. This is a harness-level constraint, not a TAUT failure. Full write-up in [`EVOLUTION.md`](./EVOLUTION.md#8-the-hermes-structural-ceiling) §8.
</details>

<details>
<summary><b>Will TAUT bloat my input tokens?</b></summary>

`TAUT.md` is ~9 KB ≈ 2 000 tokens. Every major API supports prompt caching (Anthropic, OpenAI, Google) at ~90% input-cost reduction on cached prefixes. Loaded once per session, TAUT pays for itself within ~3 turns of normal usage and saves dramatically more from there. Long-running terminals (`claude` opened once, used many turns) maximise the cache hit rate.
</details>

<details>
<summary><b>Can I customise TAUT for my team's style?</b></summary>

Absolutely. `TAUT.md` is just a Markdown file — fork it, edit it, deploy your fork. The compression mechanics are stable; the register, examples, and per-prompt-shape caps are all tunable. See [`CONTRIBUTING.md`](./CONTRIBUTING.md) for how to bench your variant.
</details>

---

## 🎓 Origin

Inspired by **[caveman](https://github.com/JuliusBrussee/caveman)** by Julius Brussee — the first widely-shared prompt to take output-side compression seriously. TAUT diverges from caveman by replacing the caveman *persona* with a senior-engineer *register* that preserves caveman-grade compression without the personification, token-waste-on-character-maintenance, or production-unsuitable voice. Full credit and design-divergence rationale in [`PHILOSOPHY.md`](./PHILOSOPHY.md#2-inspiration-caveman) §2-§3.

---

## 🌟 Star this repo if it saved you tokens.

[![star history](https://img.shields.io/github/stars/jqbit/TAUT?style=social)](https://github.com/jqbit/TAUT/stargazers)

Issues + PRs welcome. Particularly interested in:
- Agent-compatibility reports for CLIs not in the supported-8 list
- Per-agent override blocks (e.g., a `--quiet`-mode wrapper for hermes)
- Variance / methodology improvements
- Translations of the prompt suite to non-English contexts

See [`CONTRIBUTING.md`](./CONTRIBUTING.md) for how to add agents, run benchmarks, and propose v0.14+ changes.

---

## 📚 Citation

```
TAUT — Terse Agent Utterance Tuning (v0.13).
8-agent cross-harness compression benchmark, 2026.
https://github.com/jqbit/TAUT
Inspired by caveman (Julius Brussee, 2026).
```

GitHub auto-renders a "Cite this repository" button from [`CITATION.cff`](./CITATION.cff).

---

## License

[MIT](./LICENSE). Free for commercial and personal use.

---

<div align="center">

**Keywords**: AI coding agent · LLM system prompt · prompt engineering · token reduction · output compression · Claude Code prompt · GPT prompt · Gemini prompt · Cursor agent prompt · agentic AI · prompt optimization · LLMOps · Anthropic Claude prompt · OpenAI Codex prompt · Google Gemini prompt · concise output · terse AI · brevity constraints · LLM cost reduction · production AI · AI engineering

*Built with iteration, measurement, and respect for the reader's time.*

</div>
