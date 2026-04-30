# Changelog

All STFU.md prompt versions, with the headline metric (total prose-token reduction across 8 agents) and the key change for each.

The format is loosely based on [Keep a Changelog](https://keepachangelog.com/). Versions are STFU.md prompt versions; benchmarks are the matching `v1.<N>` bench run.

## [0.14.3] — 2026-04-30

**Empirical refactor: removed `## Templates` section.**

### Changed
- `STFU.md` no longer contains the three exact-match response templates ("Fix this" no code → *"Need code or error first."*, "Undo last commit keep staged" → `git reset --soft HEAD~1`, IPv4 regex). Reduces prompt by ~250 bytes (1119 → 871).

### Why
Controlled ablation on Claude Sonnet 4.6 (n=12 single-turn × 4 conditions = 48 calls + 8-turn × 4 conditions × 3 conversations = 96 calls; isolated `claude -p --append-system-prompt` runs with empty user-CLAUDE.md) showed templates produced **near-zero hits on real prompts** but caused **engagement-refusal failures**. Specifically, on the prompt *"TypeError: Cannot read properties of undefined (reading 'map') — what's wrong"*, the templates triggered the literal string *"Need code or error stack first."* instead of providing actual debugging guidance.

| metric | with templates (v0.14.2) | without (v0.14.3) | Δ |
|---|---:|---:|---|
| single-turn prose reduction | −83.6% | −80.0% | −3.6 pp |
| 8-turn overall prose reduction | −77.9% | −75.1% | −2.8 pp |
| 8-turn T8 prose words/response | 9.0 | 7.3 | tighter |
| pairwise t-test V1 vs V4 at T8 | — | — | p=0.74 (ns) |
| pairwise t-test V1 vs V4 at T1 | — | — | p=0.05 (borderline) |
| compliance markers (opener%/closer%/recap%) | ~0% | ~0% | unchanged |
| decay over 8 turns | none | none | unchanged |
| engagement on `error-undef` prompt | 0 prose words (refusal) | 48 prose words (3 causes + 3 fixes) | **fixed** |

### Trade-offs
- Templates were the v0.5 mechanism that first crossed the −70% reduction target. Removing them costs ~3 pp of compression in exchange for engagement reliability on under-specified prompts.
- Compliance markers (opener/closer/recap rates) unchanged — the rest of the prompt does that work.
- Templates may still help in deployments that *only* see well-specified prompts; the ablation above tested mixed-shape coverage.

### Methodology note
This was a single-model controlled A/B (Claude Sonnet 4.6, paired by prompt). Earlier benchmarks (v0.13.1, see §[0.13.1] below) used a 5-agent multi-harness sweep with different methodology. The two are complementary, not directly comparable. Test rig kept at `/tmp/stfu-test/scripts/` for re-runs.

### V1 → V4 ablation (the path to v0.14.3)

The v0.14.3 release came out of a 4-variant comparison. Each variant was run against the same controlled bench (12 single-turn prompts × paired conditions, plus 3 × 8-turn realistic coding conversations) on Claude Sonnet 4.6 via `claude -p --append-system-prompt` with empty user-CLAUDE.md.

| Variant | What changed vs v0.14.2 | Single-turn (n=12) | 8-turn overall (n=24) | T1 → T8 | Verdict |
|---|---|---:|---:|---:|---|
| **V1** | nothing (v0.14.2 baseline, with `## Templates`) | 14.0 words (−83.6%) | 18.8 (−77.9%) | 39.7 → 9.0 | Highest compression. Refused engagement on under-specified prompts (e.g. *"Need code or error first."* on `error-undef`). |
| **V2** | full research rewrite: positive examples in `<example>` XML, role + tool-call carve-out, recency anchor (delete-pass), no banned-phrase list | 43.4 (−49.1%) | 50.3 (−41.0%) | 101.3 → 23.7 | Most natural register; preserves reasoning. Least efficient. Slight closer-phrase rate (33–67% on some turns). |
| **V3** | hybrid: V2 scaffolding + V1's enforceable shape rules ("verdict first; one supporting clause; stop"; "one sentence per claim") + 5th example demonstrating allowed reasoning | not run | 33.9 (−60.2%) | 88.7 → 14.3 | Sat between V1 and V2 as designed. Missed the 70–80% target band. Steepest negative slope (tightens over conversation, p=0.016). |
| **V4** | V1 minus `## Templates` only — single-section deletion, otherwise byte-identical | **17.1 (−80.0%)** | **21.2 (−75.1%)** | 49.7 → 7.3 | **Selected as v0.14.3.** Statistically indistinguishable from V1 (T8 p=0.74, T4 p=0.07, T1 p=0.05 borderline). Fixes the engagement-refusal failure for ~3 pp of compression cost. |

**Decay:** No condition decayed across 8 turns. All slopes were negative or flat (responses tightened as conversations progressed, opposite of the failure mode the redesign was originally hypothesized to fix).

**Compliance markers (opener%, closer%, recap%):** Near-zero for V1, V3, V4 across all turns. V2 had a slight closer-phrase tendency at some turns (33–67%), but no meaningful drift.

**Code-chars preservation (carve-out check):** All four variants reduce code output relative to control by 15–35%, but ranking preserved (V2 > V3 > V4 ≈ V1). Carve-out is partially leaky in all variants — known limitation.

**Pairwise V1 vs V4 (the headline ablation):**

| metric | V1 | V4 | Δ | p | sig |
|---|---:|---:|---:|---:|---|
| Single-turn avg prose words | 14.0 | 17.1 | +3.1 | — | small effect |
| 8-turn T1 | 39.7 | 49.7 | +10.0 | 0.05 | borderline |
| 8-turn T4 | 13.7 | 19.3 | +5.7 | 0.07 | ns |
| 8-turn T8 | 9.0 | 7.3 | **−1.7** | 0.74 | ns (V4 slightly tighter) |
| Engagement on `error-undef` | 0 prose words (refusal) | 48 prose words (3 causes + 3 fixes) | — | — | **fixed** |

**Why not V2 or V3?**
- **V2** was research-correct (Anthropic best practices: examples > rules; positive over negative; role + recency placement) but the empirical compression cost was too steep (−41% vs control vs V1's −78%). The structural improvements didn't translate into compression on this model. The original premise — that V1 "doesn't follow instructions" — was empirically refuted by every benchmark V1 was in.
- **V3** correctly landed between V1 and V2, but the 60% reduction missed the 70–80% target band and the added complexity (extra examples, anchor sentence, dual-rule structure) didn't earn its tokens against a single-section deletion of V1.

**Total benchmark cost across all variants:** ~$2.70 USD across 159 API calls (Sonnet 4.6 standard tier). The full test rig (parallel xargs runners, paired t-test analysis script, decay regression) is preserved at `/tmp/stfu-test/scripts/` for future re-runs.

---

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
