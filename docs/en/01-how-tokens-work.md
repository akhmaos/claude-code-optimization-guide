# How Tokens Work in Claude Code

## What is a Token?

A token is the basic unit of text that language models process. Roughly:
- **1 token ≈ 4 characters** of English text (conservative estimate used internally)
- **1 token ≈ 2 characters** for JSON (Claude Code uses a separate estimate for structured data)
- A typical line of code ≈ 10-20 tokens

Claude Code uses `roughTokenCountEstimation()` internally, which defaults to 4 bytes per token for text and 2 bytes per token for JSON content.

## What Consumes Tokens?

Every API call to Claude includes:

| Component | Tokens | Cached? |
|-----------|--------|---------|
| **System prompt** | ~2,000-5,000 | Yes (stable between calls) |
| **Tool definitions** | ~10,000-30,000 | Yes (40+ tools registered) |
| **Conversation history** | Varies | Partially (cache breakpoints) |
| **Your message** | Varies | No (new each time) |
| **Tool results** | Up to 100K per result | No |
| **Claude's response** | Varies | N/A (output) |

### The Hidden Cost: Tool Definitions

Claude Code registers **40+ tools** (Read, Write, Edit, Bash, Grep, Glob, Agent, etc.). Each tool includes:
- Name and description
- JSON Schema for input parameters
- Permission model

This adds up to **10,000-30,000 tokens** sent with every single API call. However, these are **cached** — you only pay full price the first time, then ~10x cheaper for subsequent calls in the same session.

## Pricing Model

Claude Code supports multiple models with different pricing:

| Model | Input ($/Mtok) | Output ($/Mtok) | Best For |
|-------|---------------|-----------------|----------|
| Haiku 4.5 | $1 | $5 | Simple tasks, classification |
| Sonnet 4.x | $3 | $15 | Default, balanced |
| Opus 4.5/4.6 | $5 | $25 | Complex reasoning |
| Opus 4/4.1 | $15 | $75 | Legacy |
| Fast Opus 4.6 | $30 | $150 | Speed-critical (6x cost!) |

### Prompt Caching Discounts

| Operation | Discount vs Input |
|-----------|-------------------|
| Cache read | ~10x cheaper |
| Cache write | ~1.25x input cost |

**Example**: If Opus 4.6 input costs $5/Mtok, cache reads cost $0.50/Mtok.

## Why Optimization Matters

A typical Claude Code session involves 10-50 API calls. Each call sends the full conversation history + system prompt + tool definitions. Without optimization:

- **Short session** (10 calls, 50K context): ~$0.50-2.00
- **Medium session** (30 calls, 150K context): ~$2.00-10.00
- **Long session** (50+ calls, hitting compaction): ~$5.00-25.00

With optimization (caching, compact prompts, targeted reads):

- **Short session**: ~$0.10-0.50
- **Medium session**: ~$0.50-3.00
- **Long session**: ~$2.00-8.00

The difference is **3-5x** — and it compounds across sessions.

## Next Steps

- [Context Window](02-context-window.md) — understand the 200K/1M limits
- [Auto-Compaction](03-auto-compaction.md) — how context gets automatically compressed
