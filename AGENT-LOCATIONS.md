# TAUT — Agent Deployment Locations

Where to drop `TAUT.md` for each of the 8 supported coding-agent CLIs.

## The eight files

| # | Agent | File path | Mode |
|---|---|---|---|
| 1 | claude (Claude Code) | `~/.claude/CLAUDE.md` | full overwrite |
| 2 | codex (OpenAI Codex CLI) | `~/.codex/AGENTS.md` | full overwrite |
| 3 | gemini (Google Gemini CLI) | `~/.gemini/GEMINI.md` | full overwrite |
| 4 | droid (Factory Droid) | `~/.factory/AGENTS.md` | full overwrite |
| 5 | pi (Pi Coding Agent) | `~/.pi/agent/AGENTS.md` | full overwrite |
| 6 | cursor-agent (Cursor CLI) | `~/AGENTS.md` | full overwrite |
| 7 | openclaw (OpenClaw TUI) | `~/.openclaw/workspace/AGENTS.md` | TAUT block appended after openclaw's built-in agent contract |
| 8 | hermes (Hermes Agent) | `~/.hermes/SOUL.md` | TAUT block appended after Nous's persona |

For agents 1–6 the file is just the TAUT prompt by itself. For agents 7–8 the TAUT block is appended after the agent's pre-existing baseline persona/contract, separated by a `---` divider and a blank line.

## One-shot deploy script

Drop the script below next to `TAUT.md` and run it once. Idempotent — re-running does not duplicate the appended block on openclaw / hermes.

```bash
#!/usr/bin/env bash
set -euo pipefail
SRC="$(dirname "$0")/TAUT.md"
[ -f "$SRC" ] || { echo "TAUT.md not found next to this script"; exit 1; }

# 1. Six full-overwrite targets
for dest in \
  "$HOME/.claude/CLAUDE.md" \
  "$HOME/.codex/AGENTS.md" \
  "$HOME/.gemini/GEMINI.md" \
  "$HOME/.factory/AGENTS.md" \
  "$HOME/.pi/agent/AGENTS.md" \
  "$HOME/AGENTS.md"; do
  mkdir -p "$(dirname "$dest")"
  cp "$SRC" "$dest"
  echo "wrote $dest"
done

# 2. Two surgical-append targets — preserve existing prefix, swap the TAUT block
for dest in \
  "$HOME/.openclaw/workspace/AGENTS.md" \
  "$HOME/.hermes/SOUL.md"; do
  mkdir -p "$(dirname "$dest")"
  if [ ! -f "$dest" ]; then
    cp "$SRC" "$dest"
    echo "created $dest"
    continue
  fi
  python3 - <<PY
from pathlib import Path
import re
dest = Path("$dest")
src  = Path("$SRC").read_text()
text = dest.read_text()
m = re.search(r"\n*---\n+# TAUT — Terse Agent Communication Mode.*$", text, re.S)
new = (text[:m.start()].rstrip() if m else text.rstrip()) + "\n\n---\n\n" + src
dest.write_text(new)
PY
  echo "appended TAUT block in $dest"
done

# 3. Verify
echo "---"
echo "verification (every file should print ✓):"
for p in \
  "$HOME/.claude/CLAUDE.md" \
  "$HOME/.codex/AGENTS.md" \
  "$HOME/.gemini/GEMINI.md" \
  "$HOME/.factory/AGENTS.md" \
  "$HOME/.pi/agent/AGENTS.md" \
  "$HOME/.openclaw/workspace/AGENTS.md" \
  "$HOME/.hermes/SOUL.md" \
  "$HOME/AGENTS.md"; do
  if grep -q "Terse Agent Communication Mode" "$p" 2>/dev/null; then
    echo "  ✓ $p"
  else
    echo "  ✗ $p (TAUT marker missing)"
  fi
done
```

Save the block above as `deploy.sh`, `chmod +x deploy.sh`, then run `./deploy.sh`.

## Manual one-by-one (if you prefer)

```bash
# Full-overwrite targets
cp TAUT.md ~/.claude/CLAUDE.md
cp TAUT.md ~/.codex/AGENTS.md
cp TAUT.md ~/.gemini/GEMINI.md
cp TAUT.md ~/.factory/AGENTS.md
cp TAUT.md ~/.pi/agent/AGENTS.md
cp TAUT.md ~/AGENTS.md          # cursor-agent walks cwd up to home and finds this

# Surgical-append targets — open each file in your editor, scroll to bottom,
# add a "---" line, blank line, then paste TAUT.md content
${EDITOR:-vim} ~/.openclaw/workspace/AGENTS.md
${EDITOR:-vim} ~/.hermes/SOUL.md
```

## Per-agent notes worth knowing

- **claude / codex / gemini / droid / pi** — the TAUT marker appears at column 1 of the file. These agents read their global instruction file at session start and apply it to every turn.
- **cursor-agent** — its CLI walks the current working directory upward looking for `AGENTS.md`. Putting the file at `~/AGENTS.md` means any cwd under your home picks it up. Some agents that *also* walk the cwd tree (droid, pi) will encounter `~/AGENTS.md` in addition to their own global slot — this is harmless duplicate loading, ~2 KB of extra cached input.
- **openclaw** — its built-in `~/.openclaw/workspace/AGENTS.md` carries the agent's session-startup contract (memory protocols, red lines, heartbeats). The deploy script preserves that prefix and appends the TAUT block after a `---` divider, exactly where openclaw's own "Make It Yours" section invites local conventions.
- **hermes** — `~/.hermes/SOUL.md` carries Nous Research's default persona text. The deploy script preserves that prefix and appends the TAUT block after a `---` divider. Note: hermes's CLI binary forcibly emits diff views and `session_id:` trailers on tool-write tasks; this is harness-level layout that TAUT cannot suppress (see `EVOLUTION.md` §7 for the full write-up).

## Verification command

After deploying, sanity-check that every file carries the v0.13 marker:

```bash
for p in ~/.claude/CLAUDE.md ~/.codex/AGENTS.md ~/.gemini/GEMINI.md \
         ~/.factory/AGENTS.md ~/.pi/agent/AGENTS.md \
         ~/.openclaw/workspace/AGENTS.md ~/.hermes/SOUL.md ~/AGENTS.md; do
  grep -q "Terse Agent Communication Mode (v0.13)" "$p" && echo "✓ $p" || echo "✗ $p"
done
```

You should see eight `✓` lines.

## Smoke test (optional, recommended)

After deploy, ask any agent a one-liner factual question — TAUT-compliant output should be a single line, no preamble:

```bash
claude -p "What's the git command to undo the last commit but keep changes staged?"
# expect: `git reset --soft HEAD~1`   (and nothing else)
```

If you see `Use \`git reset --soft HEAD~1\`. This will move HEAD back one commit…` — the file isn't being loaded. Check the deploy command output above for the missing `✓`.
