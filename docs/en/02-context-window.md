# Context Window Management

## Default Context Window

Claude Code uses a **200,000 token** context window by default (`MODEL_CONTEXT_WINDOW_DEFAULT`).

This means the total of system prompt + tool definitions + conversation history + current message + response must fit within 200K tokens.

## 1M Context Opt-In

For complex tasks requiring more context, you can opt into a **1,000,000 token** window:

- Add `[1m]` suffix to the model name in settings
- Claude Code detects this and sends the `context-window-1m-2025-10-01` beta header

**Trade-off**: 1M context is more expensive because there's more data per API call. Only use it when you genuinely need to hold large amounts of code in memory.

## Effective Context Window

The "effective" context window is smaller than the raw limit because Claude Code reserves tokens for output:

```
effective_window = context_window - min(max_output_tokens, 20,000)
```

The `MAX_OUTPUT_TOKENS_FOR_SUMMARY = 20,000` is based on the p99.99 of compact summary outputs.

## Environment Variable Overrides

| Variable | Effect |
|----------|--------|
| `CLAUDE_CODE_MAX_CONTEXT_TOKENS` | Hard override for context window size |
| `CLAUDE_CODE_AUTO_COMPACT_WINDOW` | Cap the effective window (useful for testing) |
| `CLAUDE_CODE_DISABLE_1M_CONTEXT` | Disable 1M context (e.g., for HIPAA compliance) |

### Example: Force smaller context to trigger compaction sooner

```bash
# Compact at 50K instead of waiting until 200K
export CLAUDE_CODE_AUTO_COMPACT_WINDOW=50000
```

This is useful for testing compaction behavior or when you want to keep sessions lean.

## How Context Grows

```
Turn 1:  [System + Tools] + [Your msg] + [Response]     = ~30K
Turn 2:  [System + Tools] + [History] + [Your msg] + [Response]  = ~45K
Turn 3:  ...with tool results...                         = ~70K
Turn 10: ...conversation accumulates...                  = ~150K
Turn 15: Auto-compact triggers at ~187K (200K - 13K)     = compressed back to ~50K
```

Each turn adds:
- Your message tokens
- Claude's response tokens (including thinking)
- Any tool results (file reads, bash output, search results)

Tool results are the biggest contributor — a single `Read` of a large file can add 50K+ tokens.

## Practical Tips

1. **Read only what you need** — use `offset` and `limit` parameters
2. **Use targeted searches** — `Grep` with specific patterns, not broad matches
3. **Monitor context** — watch for the auto-compact notification
4. **Run `/compact` manually** — when you know you're done with a topic

## Next Steps

- [Auto-Compaction](03-auto-compaction.md) — what happens when context gets full
- [Prompt Caching](04-prompt-caching.md) — how caching reduces the cost of repeated content
