# Environment Variables Reference

Every environment variable that affects token consumption in Claude Code.

## Context Window & Compaction

| Variable | Type | Description |
|----------|------|-------------|
| `CLAUDE_CODE_MAX_CONTEXT_TOKENS` | number | Hard override for context window size |
| `CLAUDE_CODE_AUTO_COMPACT_WINDOW` | number | Cap effective context window (useful for testing) |
| `CLAUDE_CODE_DISABLE_1M_CONTEXT` | boolean | Disable 1M context window (HIPAA compliance) |
| `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE` | number (0-100) | Force auto-compact at N% of effective context |
| `DISABLE_AUTO_COMPACT` | boolean | Disable auto-compaction (manual `/compact` still works) |
| `DISABLE_COMPACT` | boolean | Disable ALL compaction |
| `CLAUDE_CODE_BLOCKING_LIMIT_OVERRIDE` | number | Override blocking limit for manual compact |

## Prompt Caching

| Variable | Type | Description |
|----------|------|-------------|
| `DISABLE_PROMPT_CACHING` | boolean | Disable prompt caching globally |
| `DISABLE_PROMPT_CACHING_HAIKU` | boolean | Disable caching for Haiku model only |
| `DISABLE_PROMPT_CACHING_SONNET` | boolean | Disable caching for Sonnet model only |

## Thinking & Reasoning

| Variable | Type | Description |
|----------|------|-------------|
| `CLAUDE_CODE_DISABLE_THINKING` | boolean | Disable extended thinking entirely |
| `DISABLE_INTERLEAVED_THINKING` | boolean | Disable interleaved thinking mode |
| `CLAUDE_CODE_DISABLE_ADAPTIVE_THINKING` | boolean | Disable adaptive thinking |

## Session Memory

| Variable | Type | Description |
|----------|------|-------------|
| `CLAUDE_CODE_DISABLE_AUTO_MEMORY` | boolean | Disable automatic memory extraction |
| `ENABLE_CLAUDE_CODE_SM_COMPACT` | boolean | Force enable session memory compaction |
| `DISABLE_CLAUDE_CODE_SM_COMPACT` | boolean | Disable session memory compaction |
| `CLAUDE_CODE_DISABLE_BACKGROUND_TASKS` | boolean | Disable all background tasks |

## Streaming & Timeouts

| Variable | Type | Description |
|----------|------|-------------|
| `CLAUDE_ENABLE_STREAM_WATCHDOG` | boolean | Enable streaming timeout watchdog |
| `CLAUDE_STREAM_IDLE_TIMEOUT_MS` | number | Stream idle timeout (default: 90000ms) |

## API

| Variable | Type | Description |
|----------|------|-------------|
| `CLAUDE_CODE_EXTRA_BODY` | JSON string | Extra parameters sent to the API |

## Usage Examples

### Keep sessions lean (aggressive compaction)

```bash
# Compact at 30% of context window
export CLAUDE_AUTOCOMPACT_PCT_OVERRIDE=30
```

### Save money by disabling thinking for simple tasks

```bash
# Thinking tokens are output tokens ($15-150/Mtok!)
export CLAUDE_CODE_DISABLE_THINKING=true
```

### Limit context window for testing

```bash
# Force 50K context window
export CLAUDE_CODE_AUTO_COMPACT_WINDOW=50000
```

### Disable background token consumption

```bash
# No background tasks or auto memory extraction
export CLAUDE_CODE_DISABLE_BACKGROUND_TASKS=true
export CLAUDE_CODE_DISABLE_AUTO_MEMORY=true
```

## Next Steps

- [CLAUDE.md Guide](10-claudemd-guide.md) — reduce tokens by not repeating yourself
- [Cost Monitoring](11-cost-monitoring.md) — track what you're spending
