## What this PR changes

Brief description of the change to `TAUT.md` (or other files).

## Why

Which prompt-shape, agent, or behaviour this addresses. Reference `BENCHMARKS.md` rows where applicable.

## Bench impact

If you ran the benchmark with this change, paste the per-agent delta:

| agent | TAUT v0.13 (current) | this PR | Δ |
|---|---:|---:|---:|
| claude | … | … | … |
| codex | … | … | … |
| … | … | … | … |

If you didn't run the bench, that's fine — flag it and a maintainer will run it.

## Verification

- [ ] `TAUT.md` deploys cleanly to all 8 agent paths (per `AGENT-LOCATIONS.md`)
- [ ] Smoke test passes (`claude -p "What's the git command to undo the last commit but keep changes staged?"` returns the bare command)
- [ ] No regression on a previously-passing prompt (manual spot check is fine)

## Risk of breaking other agents

Which other agents/prompt-shapes might this rule affect? Anything you'd want extra eyes on?
