# STFU.md — Shut The Flip Up

**The tiny prompt that cuts your agent’s yap by ~80%.**

STFU.md makes AI assistants answer directly — no filler, no fake enthusiasm, no “let me know if...” sludge.

> **It does NOT make the model DUMBER.**
>
> It **ONLY CHANGES** the **COMMUNICATION STYLE**.

## Which file should I use?

| File | Use this if... |
|---|---|
| [`STFU.md`](STFU.md) | You use coding agents or instruction files like `AGENTS.md`, `CLAUDE.md`, `GEMINI.md`, Cursor rules, Cline rules, etc. |
| [`STFU.chat.md`](STFU.chat.md) | You use ChatGPT, Claude, Gemini, Perplexity, or another web/mobile AI app. |

## Quick install

### Coding agents

Manual install:

1. Open [`STFU.md`](STFU.md).
2. Copy the prompt.
3. Paste it at the **top** of your agent instruction file.

You can also add [`STFU.md`](STFU.md) to the top of your `AGENTS.md` or global equivalent.

Need the right file path? See [`common agent locations`](data/agent-locations.md).

### ChatGPT / Claude / Gemini / Perplexity

Copy [`STFU.chat.md`](STFU.chat.md) into your app's custom instructions, project instructions, system prompt, or saved prompt.

[Click here to see the regular chat mode version — for regular AI use, not coding agents.](STFU.chat.md)

## What it fixes

Default AI often writes like this:

> Sure — here’s a comprehensive breakdown of the command you can use, why it works, and a few things to keep in mind...

STFU mode pushes it toward this:

```bash
git reset --soft HEAD~1
```

Other stuff it cuts:

- repeating your question back to you
- unnecessary caveats
- “here’s a breakdown” preambles
- explaining when you asked for only a command
- summary paragraphs you did not ask for
- “let me know if you want me to...” endings

## Why not just use caveman mode?

Caveman-style prompting helped prove that agents can be much more concise. STFU.md is inspired by that idea.

The difference: STFU.md aims for concise output **without** turning the assistant into a character. No roleplay. No broken tone. Just shorter answers.

## Benchmarks

The current coding-agent prompt is **671 bytes**.

Reference bench: **−82.1% total prose reduction** with **100% average compliance** across 5 tested agents.

See [`data/benchmarks.md`](data/benchmarks.md) for details.

## Example outputs

```text
Cause: port already bound.
Fix: kill process or change PORT.
```

```text
Yes — use SQLite first. Switch when writes/concurrency hurt.
```

```text
Need code or error first.
```

## Share line

```text
STFU.md makes your agents shut the flip up — cuts coding-agent yap by ~80%.
```

## Contributing

Want to improve the prompt, add an agent path, or share benchmark results? See [`CONTRIBUTING.md`](CONTRIBUTING.md).

## License

MIT. See [`LICENSE`](LICENSE).
