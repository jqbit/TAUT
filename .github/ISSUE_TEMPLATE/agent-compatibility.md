---
name: Agent compatibility report
about: Report whether STFU.md works (or doesn't) on a specific AI coding agent
title: "[agent] <agent-name> — <works|partial|broken>"
labels: agent-compatibility
assignees: ''
---

## Agent

- **Name + version**: e.g. `claude` 2.1.110 / `cursor-agent` 2026.04.15
- **Provider/model**: e.g. Anthropic Opus 4.6 / OpenAI gpt-5.4
- **Global instruction file path**: e.g. `~/.claude/CLAUDE.md`
- **STFU.md version deployed**: e.g. `v0.13`

## Result

- [ ] Works as expected (responses are noticeably terser, no preamble bloat)
- [ ] Partial — see notes
- [ ] Broken — see notes

## Smoke test output

Paste the response to:

```
What's the git command to undo the last commit but keep changes staged?
```

Expected: a single line, just the command.

```
<paste your agent's response here>
```

## Notes

- Anything weird? CLI emits extra metadata? Agent ignores certain rules?
- If proposing STFU.md v0.14+ rule additions, list them here.
