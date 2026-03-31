# Claude Code Token Optimization Cheatsheet

## Model Pricing (per million tokens)

| Model | Input | Output | Cache Read | Cache Write |
|-------|-------|--------|------------|-------------|
| **Haiku 4.5** | $1 | $5 | $0.10 | $1.25 |
| **Sonnet 4/4.5/4.6** | $3 | $15 | $0.30 | $3.75 |
| **Opus 4.5/4.6** | $5 | $25 | $0.50 | $6.25 |
| **Opus 4/4.1** | $15 | $75 | $1.50 | $18.75 |
| **Fast Opus 4.6** | $30 | $150 | $3.00 | $37.50 |

> Cache reads are **10x cheaper** than regular input. Keep sessions stable!

## Commands

| Command | What it does |
|---------|-------------|
| `/compact` | Compress context immediately |
| `/compact <hint>` | Compress with specific focus |
| `/cost` | Show session cost breakdown |
| `+500k` | Set 500K token budget (put in your prompt) |
| `+1m` | Set 1M token budget |
| `use 2M tokens` | Verbose budget syntax |

## Context Window

| Setting | Value |
|---------|-------|
| Default context | 200,000 tokens |
| 1M opt-in | Add `[1m]` suffix to model name |
| Auto-compact trigger | `context_window - 13,000` tokens |
| Circuit breaker | Stops after 3 failed compactions |

## Tool Result Limits

| Limit | Value |
|-------|-------|
| Per tool result | 50,000 characters |
| Per tool (tokens) | 100,000 tokens |
| Per message (aggregate) | 200,000 characters |

> Results exceeding limits are saved to disk; Claude gets a preview + file path.

## Environment Variables

| Variable | Effect |
|----------|--------|
| `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE` | Force auto-compact at N% of context |
| `CLAUDE_CODE_AUTO_COMPACT_WINDOW` | Cap effective context window size |
| `DISABLE_AUTO_COMPACT` | Disable auto-compaction (keep manual `/compact`) |
| `DISABLE_COMPACT` | Disable ALL compaction |
| `CLAUDE_CODE_MAX_CONTEXT_TOKENS` | Override context window size |
| `CLAUDE_CODE_DISABLE_1M_CONTEXT` | Disable 1M context |
| `CLAUDE_CODE_DISABLE_THINKING` | Disable extended thinking |
| `DISABLE_PROMPT_CACHING` | Disable prompt caching globally |

## DO: Save Tokens

- **Use specific file paths** — `Read src/utils/config.ts` not "find the config file"
- **Use offset/limit for large files** — `Read file.ts offset=100 limit=50` not the whole file
- **Use Grep/Glob** instead of `bash find/grep` — dedicated tools are cheaper
- **Run `/compact` proactively** — don't wait for auto-compact
- **Write CLAUDE.md** — avoid re-explaining project context each session
- **Use sub-agents** for exploratory research — isolates context
- **Keep sessions stable** — switching models/tools breaks prompt cache
- **Set token budgets** — `+500k` prevents runaway spending
- **Use Plan Mode** for complex tasks — read-only exploration first
- **Be specific in prompts** — "fix the null check on line 42 of auth.ts" > "fix the bug"

## DON'T: Waste Tokens

- **Don't read entire large files** — use offset/limit parameters
- **Don't use Bash for file search** — Grep/Glob tools are more efficient
- **Don't restart sessions** unnecessarily — breaks prompt cache ($$$)
- **Don't repeat project context** verbally — put it in CLAUDE.md
- **Don't use Fast Opus** for simple tasks — 6x more expensive
- **Don't ask vague questions** — leads to unnecessary back-and-forth
- **Don't run broad searches** — `grep "error"` across entire repo = huge results
- **Don't ignore `/cost`** — check spending regularly

## Mental Model

```
Your prompt → [System prompt + Tools + Messages] → API call
                    ↓                                  ↓
              Cached (cheap)                    New tokens (expensive)
                    ↓                                  ↓
              Cache TTL: 5min                   Full price input
              (1hr for some)                    + output tokens
```

**The key insight**: Everything that stays the same between calls (system prompt, tool definitions) gets cached. Everything that changes (your messages, tool results) costs full price. Keep sessions stable → maximize cache hits → save money.
