# Prompt Caching

Prompt caching is the single most impactful cost-saving mechanism in Claude Code. It makes repeated content **~10x cheaper** to process.

## How It Works

Claude's API caches content that stays identical between API calls:

```
API Call 1: [System prompt] + [Tools] + [Messages]
            ↑ cached          ↑ cached   ↑ new = full price

API Call 2: [System prompt] + [Tools] + [Messages + new msg]
            ↑ cache HIT $$    ↑ cache HIT $$  ↑ new = full price
```

Cache hits cost ~10x less than fresh input tokens:

| Model | Input | Cache Read | Savings |
|-------|-------|------------|---------|
| Sonnet | $3/Mtok | $0.30/Mtok | 90% |
| Opus 4.6 | $5/Mtok | $0.50/Mtok | 90% |
| Opus 4/4.1 | $15/Mtok | $1.50/Mtok | 90% |

## Cache TTL

- **Default**: 5 minutes (ephemeral cache)
- **Extended**: 1 hour for eligible users/configurations

If you pause for more than 5 minutes between interactions, the cache expires and you pay full price for the next call.

## What Gets Cached

Claude Code places `cache_control` breakpoints on:

1. **System prompt blocks** — stable between calls
2. **Tool definitions** — all 40+ tool schemas

These are the largest components (~15-30K tokens) and they're cached on every call.

## What Breaks the Cache

The cache break detection system (`promptCacheBreakDetection.ts`) tracks multiple factors:

| Change | Breaks Cache? |
|--------|---------------|
| Sending a new message | No (appended after cached content) |
| Switching models | **Yes** |
| Changing effort level | **Yes** |
| Toggling fast mode | **Yes** |
| Adding/removing MCP tools | **Yes** |
| Changing beta headers | **Yes** |
| Running `/compact` | **Yes** (expected, resets baseline) |
| Switching system prompt | **Yes** |
| 5+ minutes of inactivity | **Yes** (TTL expiration) |

### Minimum Cache Miss Detection

Claude Code only reports cache breaks when the drop exceeds **2,000 tokens** (`MIN_CACHE_MISS_TOKENS`). Small fluctuations are filtered as noise.

## Practical Impact

**Scenario: 30-turn session with Opus 4.6**

Without caching:
- System prompt + tools: ~20K tokens × 30 calls × $5/Mtok = **$3.00**

With caching:
- First call: 20K × $5/Mtok = $0.10
- 29 cache hits: 20K × 29 × $0.50/Mtok = **$0.29**
- **Total: $0.39** (saving $2.61, or **87%**)

## Tips for Maximizing Cache Hits

1. **Don't restart sessions** unless necessary — every restart breaks the cache
2. **Stay on the same model** — switching models invalidates the cache
3. **Don't toggle fast mode** mid-session
4. **Keep MCP tool set stable** — adding/removing tools changes tool schemas
5. **Work in bursts** — long pauses (>5 min) cause cache TTL expiry
6. **Use `/compact`** deliberately — it breaks cache but reclaims context space

## Next Steps

- [Model Selection](05-model-selection.md) — choosing the right model for cost efficiency
- [Cost Monitoring](11-cost-monitoring.md) — tracking cache hit rates
