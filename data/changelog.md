# Changelog

All STFU.md prompt versions, with the headline metric (total prose-token reduction across 8 agents) and the key change for each.

The format is loosely based on [Keep a Changelog](https://keepachangelog.com/). Versions are STFU.md prompt versions; benchmarks are the matching `v1.<N>` bench run.

## [0.13.1] — 2026-04-24

**Format-only release. No rule changes; no re-bench.**

### Changed
- `STFU.md` collapsed to a single 1497-char ultra-compact form (down from 9377 chars). All distinct rules from v0.13 preserved: anti-restate, anti-metadata, last-character, diff-fence, Override, Persistence, all 14 budget caps, Cut + Density lists.

### Removed
- `STFU.md-mini.md` — folded into the new `STFU.md` (same 1497-char budget).
- `STFU.md-compact.md` — superseded; mid-tier no longer needed.

### Trade-offs
- 6 of 9 worked examples dropped (kept: implicit→"Need code…", IPv4 regex, write+test→silence).
- Anti-metadata specifics (`@@`, `┊ review diff`) collapsed to the umbrella term `diff-trailers`.
- Sentence-cap qualifier "if/when/because count as new sentence" dropped from prose.

### Rationale
- One file per agent slot; `~3.9 k` Claude Code memory tokens → `~1.5 k`. Rule semantics unchanged.

### v0.13.1 5-agent re-bench (2026-04-24)

After spot-check identified cursor regression on its default `composer-2-fast` model, full re-bench was run on **5 agents × 5 prompts × N=1** with cursor on `gpt-5.3-codex`. **All 5 hit the ≥70 % reduction + ≥80 % compliance threshold:**

| Agent  | Reduction | Compliance |
|--------|----------:|-----------:|
| gemini |  −86.8 %  | 100 % (5/5)|
| pi     |  −84.2 %  | 100 % (5/5)|
| claude |  −80.1 %  | 100 % (5/5)|
| agent  |  −78.1 %  | 100 % (5/5) (requires `--model gpt-5.3-codex`) |
| droid  |  −77.4 %  | 100 % (5/5)|
| **TOT**|  **−82.1 %** | avg **100 %** |

### Cursor model recommendation (new)

Cursor's default `composer-2-fast` model is RLHF-trained for context-rich responses and does NOT respect STFU.md's hard templates (workspace descriptions, alternative-command tips, explanatory closers always emitted). Switching cursor's model to `gpt-5.3-codex` (or `gpt-5.2`) restores STFU.md compliance and the bench numbers above. Recommended alias:

```bash
alias cursor-agent='agent --yolo --model gpt-5.3-codex'
```

### Supported agents narrowed to 5

The repo now supports 5 agents (down from 9). Dropped: codex, copilot, hermes, openclaw — none re-bench-tested at v0.13.1 on this host. The v0.13 numbers for those 4 are preserved in [`BENCHMARKS.md` §1–§11](./benchmarks.md) for historical reference and are reproducible by anyone running them with the v0.13 9 377-char form.

See [`BENCHMARKS.md` §14](./benchmarks.md#14-v0131-five-agent-bench-2026-04-24) for raw per-cell numbers, methodology, caveats, and reproducibility steps.

---

## [0.13] — 2026-04-16

**Headline**: −80.0 % total · 96.7 % avg compliance · 86.7 % lowest (hermes — harness-bounded)

### Added
- **Diff-fence rule**: instructs agents whose CLI auto-injects diff views to wrap them in `\`\`\`diff` code fences so they count as code, not prose.

### Result
- Best total Δ % achieved across the entire iteration.
- Hermes Q08 still capped by CLI-level diff emission — confirmed structural ceiling.

---

## [0.12] — 2026-04-16

**Headline**: −77.9 % total · 97.5 % avg compliance · 86.7 % lowest

### Changed
- Reverted the over-bloated anti-metadata rule from v0.11 to a tight 2-line form.
- Cursor compliance recovered from 86.7 % → 100 %.

### Lesson
- A leaner STFU.md outperforms a bloated one when marginal rules have low impact. Prompt-self-bloat dilutes earlier rules.

---

## [0.11] — 2026-04-16

**Headline**: −77.2 % total · 95.8 % avg compliance · 86.7 % lowest

### Added
- Expanded anti-metadata rule covering more CLI-emitted artefacts.

### Regressed
- Total Δ % dropped slightly; cursor went 100 → 86.7. Reverted in v0.12.

---

## [0.10] — 2026-04-16

**Headline**: −78.0 % total · 96.7 % avg compliance · 86.7 % lowest

### Added
- Anti-metadata rule (`session_id`, `runId`, diff-views).
- Last-character rule (final char of response = final meaningful char).

### Discovery
- Hermes still 87 % — confirmed CLI-level rather than LLM-level cause.

---

## [0.9] — 2026-04-16

**Headline**: −76.0 % total · 96.7 % avg compliance · 86.7 % lowest

### Added
- **Anti-helpfulness rule** — no unsolicited security postscripts, "when to use which" closers, usage examples, or rate-limiting reminders.

### Result
- **Cursor compliance jumped from 73 % → 100 %** in one version. Largest single-version compliance gain across the iteration. Counters RLHF "be helpful" inflation.

---

## [0.8] — 2026-04-16

**Headline**: −73.1 % total · 92.5 % avg compliance · 73.3 % lowest

### Changed
- Concept cap → 60 (was 80).
- Best-practices cap → 120 (was 150).

### Added
- Draft-then-halve meta-cognitive rule.

### Regressed
- Aggressive cap tightening + growing STFU.md regressed total Δ %. Diminishing-returns territory.

---

## [0.7] — 2026-04-16

**Headline**: −75.6 % total · 93.3 % avg compliance · 73.3 % lowest

### Added
- Anti-helpfulness rule (precursor to v0.9's stronger version).
- Error-interpretation cap → 50.
- One-liner cap → 20.

---

## [0.6] — 2026-04-16

**Headline**: −75.9 % total · 92.5 % avg compliance · 66.7 % lowest

### Added
- **Regex-simplicity rule**: prefer simplest correct artifact; strict octet-bound IPv4 patterns forbidden unless user asks. Unblocked Q13 universal failure.
- Concept cap → 80.
- Coding-task closing prose → 0.

---

## [0.5] — 2026-04-16

**Headline**: −70.5 % total · 89.2 % avg compliance · 53.3 % lowest

### Added
- **Hard response templates** for under-specified prompts (Q11 "Fix this bug." → exactly "Need code or error first.").

### Result
- Crossed the −70 % target for the first time. Cursor's Q11 fabrication failure mode eliminated entirely.

---

## [0.4] — 2026-04-16

**Headline**: −62.2 % total · 86.7 % avg compliance · 26.7 % lowest

### Added
- **Per-prompt-shape token budget table** (one-liner = 25, comparison = 70, best-practices list = 120, etc.).

### Lesson
- Numerical budgets per prompt-shape outperform global caps. RLHF-trained models have a strong instruction-following bias toward measurable success criteria.

---

## [0.3] — 2026-04-16

**Headline**: −57.6 % total · 82.5 % avg compliance · 53.3 % lowest

### Added
- Per-sentence cap (max 16 words).
- Per-paragraph cap (max 3 sentences).
- Per-prompt-shape behavioural rules (yes/no = 2 sentences, debug = 1 root cause + 1 fix).
- Fragment-grammar permission.
- Banned parenthetical asides.

### Result
- **First major jump** — total reduction went 35 % → 58 %. The structural-vs-prose pivot: structure caps target the *shape*, length caps target the *substance*. Both are needed.

---

## [0.2] — 2026-04-16

**Headline**: −34.8 % total · 70.8 % avg compliance · 40.0 % lowest

### Added
- Hard structure caps on headers, tables, bold per token.
- Stated compression target (50–70 %).

### Discovery
- Structure caps cut markdown by 86 % (headers) and 50 % (bullets) — but total prose tokens barely moved. Markdown stripping ≠ compression.

---

## [0.1] — 2026-04-16

**Headline**: −33.9 % total · 79.2 % avg compliance · 53.3 % lowest

### Added
- Initial Lyra-optimized port of caveman compression mechanics into a senior-engineer register.
- Soft "default to" / "prefer" language throughout.

### Discovery
- Modern coding agents already kill classical preamble traps (Sure / Great question). The bloat is now structural (headers, bullets, bold) — drove the v0.2 design.

---

## Notes on the iteration

- Every version was bench-tested across **8 agents × 15 prompts × 3 trials** (gemini and openclaw at N=1 due to harness constraints). ~300 measured responses per version, ~3 900 total across the v0.1 → v0.13 journey.
- The single most important macro-finding was **cross-agent variance shrinkage**: baseline compliance spread of 66 percentage points (cursor 27 % ↔ openclaw 93 %) collapsed to 13 pp by v0.13 (hermes 87 % ↔ five agents at 100 %). 5× reduction.
- Full per-version per-agent matrices in [`BENCHMARKS.md`](./benchmarks.md). Narrative in [`EVOLUTION.md`](./progression.md).
