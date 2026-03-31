# Auto-Compaction Deep Dive

Auto-compaction is Claude Code's mechanism for keeping conversations within the context window. Understanding it lets you work with it, not against it.

## When Does Auto-Compact Trigger?

```
threshold = effective_context_window - 13,000 tokens
```

Where `effective_context_window = context_window - min(max_output_tokens, 20,000)`.

For the default 200K context window:
```
effective = 200,000 - 20,000 = 180,000
threshold = 180,000 - 13,000 = 167,000 tokens
```

**Auto-compact triggers when context usage exceeds ~167K tokens.**

### Override the Threshold

```bash
# Trigger at 50% of context (useful for keeping sessions lean)
export CLAUDE_AUTOCOMPACT_PCT_OVERRIDE=50
```

This sets the threshold to `min(50% of effective_window, default_threshold)`.

## What Happens During Compaction

1. **Session memory compaction** is tried first — a background agent extracts key information
2. If that's insufficient, **traditional compaction** summarizes the conversation
3. Claude receives the summary + recent messages as the new context
4. Cache breakpoints are reset (notified via `notifyCompaction()`)

### Session Memory Compaction

A forked agent runs in the background and:
- Extracts key decisions, findings, and state from the conversation
- Preserves tool_use/tool_result pairs to avoid API errors
- Default config: triggers after 10K tokens and 5+ text block messages
- Maximum: 40K tokens of session memory

### Traditional Compaction

If session memory isn't enough:
- Claude summarizes the conversation, preserving critical context
- Summary output is capped at 20K tokens (based on p99.99 analysis)
- The compressed conversation replaces the original messages

## Circuit Breaker

After **3 consecutive compaction failures**, Claude Code stops trying:

```
MAX_CONSECUTIVE_AUTOCOMPACT_FAILURES = 3
```

This prevents wasting API calls on irrecoverable situations (e.g., a single message that's too large).

## Warning and Error Thresholds

```
Warning: effective_window - 20,000 tokens (WARNING_THRESHOLD_BUFFER_TOKENS)
Error:   effective_window - 20,000 tokens (ERROR_THRESHOLD_BUFFER_TOKENS)
```

You'll see a warning in the UI when approaching these limits.

## Manual Compaction

The `/compact` command uses a smaller buffer:

```
MANUAL_COMPACT_BUFFER_TOKENS = 3,000
```

You can also provide a hint to guide what gets preserved:
```
/compact keep the database schema and migration plan
```

## Microcompact: Inline Trimming

Separate from auto-compaction, **microcompact** trims old tool results inline:

- **Time-based**: Old tool results are cleared when there's a gap since the last assistant message
- **Cached microcompact**: Uses cache editing API to remove old results without breaking prompt cache
- Cleared results show: `[Old tool result content cleared]`

**Compactable tools** (results that can be trimmed):
- FileRead, FileWrite, FileEdit
- Shell commands (Bash)
- Grep, Glob
- WebSearch, WebFetch

## Disabling Compaction

| Variable | Effect |
|----------|--------|
| `DISABLE_AUTO_COMPACT` | Disable auto-compaction only (manual `/compact` still works) |
| `DISABLE_COMPACT` | Disable ALL compaction |

**Warning**: Disabling compaction risks hitting the context limit, which causes API errors.

## Best Practices

1. **Run `/compact` proactively** when switching topics — don't wait for auto-compact
2. **Use `/compact <hint>`** to guide what gets preserved
3. **Watch for compaction notifications** — they mean you're burning through context fast
4. **If you see repeated compactions**, break the task into smaller subtasks
5. **Use sub-agents** for exploratory work — they have their own context window

## Next Steps

- [Prompt Caching](04-prompt-caching.md) — how caching interacts with compaction
- [Practical Tips](08-practical-tips.md) — actionable advice for daily use
