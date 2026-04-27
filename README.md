# STFU.md — Shut The Flip Up

Makes your agents shut the flip up.

STFU.md is a 671-byte system prompt that cuts coding-agent prose by ~80 % without touching tool-use, code correctness, or reasoning. Drop it into any agent's instruction slot.

## Headline (v0.14.2, 11 harnesses)

| metric | value |
|---|---|
| char count | **671** (STFU.md) |
| communication-only | yes — Q08 (write & run) and Q13 (regex-only) sanity gates |
| harnesses tested | 11 (claude, codex, copilot, droid, hermes, opencode, openclaw, pi, cline, agent/cursor, gemini) |
| target | 100 % compliance · ≥ 80 % prose reduction |

See [`data/benchmarks.md`](data/benchmarks.md) for full per-harness numbers + [`data/visualizations/`](data/visualizations/) for charts. Honest gaps documented in [`data/research/critical-findings.md`](data/research/critical-findings.md).

## Install

Drop `STFU.md` into the matching slot per agent:

| Agent | Slot |
|---|---|
| Claude Code | `~/.claude/CLAUDE.md` |
| Gemini CLI | `~/.gemini/GEMINI.md` |
| Codex | `~/.codex/AGENTS.md` |
| GitHub Copilot CLI | `~/.copilot/copilot-instructions.md` |
| Factory Droid | `~/.factory/AGENTS.md` |
| Hermes | `~/.hermes/SOUL.md` |
| OpenCode | `~/.opencode/AGENTS.md` |
| OpenClaw | `~/.openclaw/AGENTS.md` |
| Pi | `~/.pi/agent/AGENTS.md` |
| Cline | `~/.cline/data/rules/STFU.md` |
| Cursor Agent | `~/AGENTS.md` |

One-liner deploy across all 11:

```bash
URL=https://raw.githubusercontent.com/jqbit/STFU.md/main/STFU.md
for d in ~/.claude/CLAUDE.md ~/.gemini/GEMINI.md ~/.codex/AGENTS.md \
         ~/.copilot/copilot-instructions.md ~/.factory/AGENTS.md \
         ~/.hermes/SOUL.md ~/.opencode/AGENTS.md ~/.openclaw/AGENTS.md \
         ~/.pi/agent/AGENTS.md ~/.cline/data/rules/STFU.md ~/AGENTS.md; do
  mkdir -p "$(dirname "$d")" && curl -fsSL "$URL" -o "$d"
done
```

## License

MIT. See [`LICENSE`](LICENSE).
