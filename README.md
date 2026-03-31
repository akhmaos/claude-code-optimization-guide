# Claude Code Token Optimization Guide

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-CLI-blueviolet)](https://docs.anthropic.com/en/docs/claude-code)

> **Every recommendation in this guide is backed by constants and logic from the Claude Code source code.**

[🇷🇺 Версия на русском](README.ru.md)

## Why This Guide?

Claude Code is powerful, but tokens cost money. A single debugging session can burn $5–20 if you don't understand the internal mechanics. This guide teaches you how Claude Code manages context, caches prompts, and trims results — so you can work smarter, not more expensively.

## Quick Start

**Just want the essentials?** Read the [Cheatsheet](cheatsheet.md) — one page with everything you need.

## Table of Contents

### Core Mechanics

| # | Topic | What You'll Learn |
|---|-------|-------------------|
| 01 | [How Tokens Work](docs/en/01-how-tokens-work.md) | Token basics, pricing model, why optimization matters |
| 02 | [Context Window](docs/en/02-context-window.md) | 200K default, 1M opt-in, environment overrides |
| 03 | [Auto-Compaction](docs/en/03-auto-compaction.md) | When and how context gets compressed (threshold: `context - 13K`) |
| 04 | [Prompt Caching](docs/en/04-prompt-caching.md) | Cache TTL (5 min / 1 hr), what breaks the cache |
| 05 | [Model Selection](docs/en/05-model-selection.md) | Pricing tiers from $1/Mtok (Haiku) to $150/Mtok (Fast Opus) |
| 06 | [Tool Result Limits](docs/en/06-tool-results.md) | 50K chars per tool, 200K per message — what happens when exceeded |
| 07 | [Sub-agents](docs/en/07-subagents.md) | Context isolation, when to delegate to agents |

### Practical Guides

| # | Topic | What You'll Learn |
|---|-------|-------------------|
| 08 | [Practical Tips](docs/en/08-practical-tips.md) | 20+ actionable tips to cut token usage |
| 09 | [Environment Variables](docs/en/09-environment-vars.md) | Every env var that affects token consumption |
| 10 | [CLAUDE.md Guide](docs/en/10-claudemd-guide.md) | Write effective project instructions |
| 11 | [Cost Monitoring](docs/en/11-cost-monitoring.md) | `/cost` command, token budgets (`+500k` syntax) |

### Examples

- [CLAUDE.md Templates](examples/claudemd-templates/) — Ready-to-use templates for different project types
- [Workflow Guides](examples/workflows/) — Step-by-step efficient workflows

## Key Numbers (from source code)

```
MODEL PRICING (per million tokens: input / output)
──────────────────────────────────────────────────
Haiku 4.5        $1 / $5       ← Simple tasks
Sonnet 4.x       $3 / $15      ← Default, balanced
Opus 4.5/4.6     $5 / $25      ← Complex reasoning
Opus 4/4.1       $15 / $75     ← Legacy
Fast Opus 4.6    $30 / $150    ← Speed mode (6x Opus!)

Cache reads are ~10x cheaper than input tokens.

AUTO-COMPACTION
──────────────────────────────────────────────────
Trigger:          context_window - 13,000 tokens
Circuit breaker:  stops after 3 consecutive failures
Override:         CLAUDE_AUTOCOMPACT_PCT_OVERRIDE=<percent>

TOOL RESULT LIMITS
──────────────────────────────────────────────────
Per tool:         50,000 characters
Per token:        100,000 tokens max
Per message:      200,000 characters (aggregate)
```

## Contributing

Found an inaccuracy? Know a trick that's not listed? PRs are welcome!

## License

MIT — see [LICENSE](LICENSE)
