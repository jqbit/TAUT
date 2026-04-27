# STFU.md — Agent Deployment Locations

Where to drop `STFU.md` for each of the 5 supported coding-agent CLIs.

## The five files

| # | Agent | File path | Mode |
|---|---|---|---|
| 1 | claude (Claude Code) | `~/.claude/CLAUDE.md` | full overwrite |
| 2 | gemini (Google Gemini CLI) | `~/.gemini/GEMINI.md` | full overwrite |
| 3 | droid (Factory Droid) | `~/.factory/AGENTS.md` | full overwrite |
| 4 | pi (Pi Coding Agent) | `~/.pi/agent/AGENTS.md` | full overwrite |
| 5 | agent (Cursor Agent CLI) | `~/AGENTS.md` | full overwrite |

The file is just the STFU.md prompt by itself — no merge, no append.

## ⚡ Fastest install — pick your agent, run one line

Each command below downloads `STFU.md` straight from GitHub and writes it to the right path. No clone. No script. Just curl.

### Single-agent installs

```bash
# Claude Code
mkdir -p ~/.claude && curl -fsSL https://raw.githubusercontent.com/jqbit/STFU.md/main/STFU.md -o ~/.claude/CLAUDE.md

# Google Gemini
mkdir -p ~/.gemini && curl -fsSL https://raw.githubusercontent.com/jqbit/STFU.md/main/STFU.md -o ~/.gemini/GEMINI.md

# Factory Droid
mkdir -p ~/.factory && curl -fsSL https://raw.githubusercontent.com/jqbit/STFU.md/main/STFU.md -o ~/.factory/AGENTS.md

# Pi Coding Agent
mkdir -p ~/.pi/agent && curl -fsSL https://raw.githubusercontent.com/jqbit/STFU.md/main/STFU.md -o ~/.pi/agent/AGENTS.md

# Cursor Agent
curl -fsSL https://raw.githubusercontent.com/jqbit/STFU.md/main/STFU.md -o ~/AGENTS.md
```

### Install all five at once

```bash
STFU_URL=https://raw.githubusercontent.com/jqbit/STFU.md/main/STFU.md
for d in ~/.claude/CLAUDE.md ~/.gemini/GEMINI.md ~/.factory/AGENTS.md ~/.pi/agent/AGENTS.md ~/AGENTS.md; do
  mkdir -p "$(dirname "$d")" && curl -fsSL "$STFU_URL" -o "$d"
done
```

## Per-agent notes

### claude / gemini / droid / pi

The STFU.md marker appears at column 1 of the file. These agents read their global instruction file at session start and apply it to every turn. No flags needed.

- **droid** runs as `droid exec --auto medium "<prompt>"` for non-interactive mode. The `--auto` flag bypasses droid's permission prompts (which would otherwise hang in headless mode).

### Cursor Agent

Cursor's CLI walks the current working directory upward looking for `AGENTS.md`. Putting the file at `~/AGENTS.md` means any cwd under your home picks it up.

**IMPORTANT — model choice matters for cursor.** STFU.md compliance on cursor depends on which underlying model is selected:

| Cursor model | Compression with STFU.md v0.13.1 | Notes |
|---|---:|---|
| `composer-2-fast` (default) | ~30 % reduction | Always describes workspace, adds tips, ignores hard templates. RLHF-trained for context-rich responses. |
| `composer-2` | ~30 % reduction | Same family, same behaviour |
| `gpt-5.3-codex` | **~78 % reduction** | Follows STFU.md register cleanly; recommended |
| `gpt-5.2` | **~75 % reduction** | Similar to gpt-5.3-codex |

Recommended alias to make STFU.md-compliant cursor invocations the default:

```bash
alias cursor-agent='agent --yolo --model gpt-5.3-codex'
```

Then `cursor-agent -p "your prompt"` will produce STFU.md-compliant output.

## Verification command

After deploying, sanity-check that every file carries the STFU.md prompt:

```bash
for p in ~/.claude/CLAUDE.md ~/.gemini/GEMINI.md \
         ~/.factory/AGENTS.md ~/.pi/agent/AGENTS.md ~/AGENTS.md; do
  grep -q "^# STFU.md" "$p" && echo "✓ $p" || echo "✗ $p"
done
```

You should see five `✓` lines.

## Smoke test (recommended)

After deploy, ask any agent a one-liner factual question — STFU.md-compliant output should be a single line, no preamble:

```bash
claude -p "What's the git command to undo the last commit but keep changes staged?"
# expect: `git reset --soft HEAD~1`   (and nothing else)
```

For cursor, use the recommended model:

```bash
agent --yolo --model gpt-5.3-codex -p "What's the git command to undo the last commit but keep changes staged?"
# expect: `git reset --soft HEAD~1`   (and nothing else)
```

If you see `Use \`git reset --soft HEAD~1\`. This will move HEAD back one commit…` — the file isn't being loaded (or you're on cursor's `composer-2-fast` model). Check the deploy command output above for the missing `✓`.
