# TAUT — Agent Deployment Locations

Where to drop `TAUT.md` for each of the 9 supported coding-agent CLIs.

## The nine files

| # | Agent | File path | Mode |
|---|---|---|---|
| 1 | claude (Claude Code) | `~/.claude/CLAUDE.md` | full overwrite |
| 2 | codex (OpenAI Codex CLI) | `~/.codex/AGENTS.md` | full overwrite |
| 3 | gemini (Google Gemini CLI) | `~/.gemini/GEMINI.md` | full overwrite |
| 4 | droid (Factory Droid) | `~/.factory/AGENTS.md` | full overwrite |
| 5 | pi (Pi Coding Agent) | `~/.pi/agent/AGENTS.md` | full overwrite |
| 6 | cursor-agent (Cursor CLI) | `~/AGENTS.md` | full overwrite |
| 7 | copilot (GitHub Copilot CLI) | `~/.copilot/copilot-instructions.md` | full overwrite |
| 8 | openclaw (OpenClaw TUI) | `~/.openclaw/workspace/AGENTS.md` | TAUT block appended after openclaw's built-in agent contract |
| 9 | hermes (Hermes Agent) | `~/.hermes/SOUL.md` | TAUT block appended after Nous's persona |

For agents 1–7 the file is just the TAUT prompt by itself. For agents 8–9 the TAUT block is appended after the agent's pre-existing baseline persona/contract, separated by a `---` divider and a blank line.

## ⚡ Fastest install — pick your agent, run one line

Each command below downloads `TAUT.md` straight from GitHub and writes it to the right path. No clone. No script. Just curl.

### Single-agent installs (just the one you use)

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

# GitHub Copilot CLI
mkdir -p ~/.copilot && curl -fsSL https://raw.githubusercontent.com/jqbit/TAUT/main/TAUT.md -o ~/.copilot/copilot-instructions.md
```

### All-seven-overwrite-agents at once

```bash
TAUT_URL=https://raw.githubusercontent.com/jqbit/TAUT/main/TAUT.md
for d in ~/.claude/CLAUDE.md ~/.codex/AGENTS.md ~/.gemini/GEMINI.md ~/.factory/AGENTS.md ~/.pi/agent/AGENTS.md ~/AGENTS.md ~/.copilot/copilot-instructions.md; do
  mkdir -p "$(dirname "$d")" && curl -fsSL "$TAUT_URL" -o "$d"
done
```

### Append-mode agents (openclaw + hermes)

These two agents already have a system prompt you want to keep (openclaw's session contract, hermes's persona). You append TAUT after it, separated by a `---` divider. Easiest manual approach: open the file in your editor, scroll to the bottom, paste a `---` blank-line block, then paste the contents of `TAUT.md`.

```bash
# OpenClaw
${EDITOR:-nano} ~/.openclaw/workspace/AGENTS.md
# Hermes
${EDITOR:-nano} ~/.hermes/SOUL.md
```

If you want it pasted automatically, this one-liner appends `TAUT.md` to a file with the divider, idempotently (re-running won't duplicate):

```bash
TAUT_URL=https://raw.githubusercontent.com/jqbit/TAUT/main/TAUT.md
TARGET=~/.hermes/SOUL.md   # or ~/.openclaw/workspace/AGENTS.md
mkdir -p "$(dirname "$TARGET")" && touch "$TARGET"
grep -q "TAUT — Terse Agent Communication Mode" "$TARGET" || \
  { printf '\n\n---\n\n' >> "$TARGET" && curl -fsSL "$TAUT_URL" >> "$TARGET"; }
```

## Per-agent notes worth knowing

- **claude / codex / gemini / droid / pi** — the TAUT marker appears at column 1 of the file. These agents read their global instruction file at session start and apply it to every turn.
- **cursor-agent** — its CLI walks the current working directory upward looking for `AGENTS.md`. Putting the file at `~/AGENTS.md` means any cwd under your home picks it up. Some agents that *also* walk the cwd tree (droid, pi, copilot) will encounter `~/AGENTS.md` in addition to their own global slot — this is harmless duplicate loading, ~2 KB of extra cached input.
- **copilot** — GitHub Copilot CLI reads user-level instructions from `~/.copilot/copilot-instructions.md` (undocumented but hardcoded in the CLI binary; verify with `copilot /env`). It *also* walks the cwd tree for `AGENTS.md` and reads per-repo `.github/copilot-instructions.md`. The global file applies across every repo without committing TAUT into `.github/`.
- **openclaw** — its built-in `~/.openclaw/workspace/AGENTS.md` carries the agent's session-startup contract (memory protocols, red lines, heartbeats). The append-mode install preserves that prefix and adds the TAUT block after a `---` divider, exactly where openclaw's own "Make It Yours" section invites local conventions.
- **hermes** — `~/.hermes/SOUL.md` carries Nous Research's default persona text. The append-mode install preserves that prefix and adds the TAUT block after a `---` divider. Note: hermes's CLI binary forcibly emits diff views and `session_id:` trailers on tool-write tasks; this is harness-level layout that TAUT cannot suppress (see [`EVOLUTION.md`](./EVOLUTION.md) §8 for the full write-up).

## Verification command

After deploying, sanity-check that every file carries the v0.13 marker:

```bash
for p in ~/.claude/CLAUDE.md ~/.codex/AGENTS.md ~/.gemini/GEMINI.md \
         ~/.factory/AGENTS.md ~/.pi/agent/AGENTS.md ~/AGENTS.md \
         ~/.copilot/copilot-instructions.md \
         ~/.openclaw/workspace/AGENTS.md ~/.hermes/SOUL.md; do
  grep -q "Terse Agent Communication Mode (v0.13)" "$p" && echo "✓ $p" || echo "✗ $p"
done
```

You should see nine `✓` lines.

## Smoke test (optional, recommended)

After deploy, ask any agent a one-liner factual question — TAUT-compliant output should be a single line, no preamble:

```bash
claude -p "What's the git command to undo the last commit but keep changes staged?"
# expect: `git reset --soft HEAD~1`   (and nothing else)
```

If you see `Use \`git reset --soft HEAD~1\`. This will move HEAD back one commit…` — the file isn't being loaded. Check the deploy command output above for the missing `✓`.
