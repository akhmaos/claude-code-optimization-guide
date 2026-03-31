# Cost Monitoring

## The `/cost` Command

Run `/cost` at any time to see your current session's spending:
- Total cost in USD
- Token breakdown (input, output, cache read, cache write)
- Per-model costs
- Number of API calls

**Tip**: Run `/cost` every 10-15 minutes during long sessions to catch inefficiencies early.

## Token Budgets

Set a spending cap directly in your prompt:

### Shorthand Syntax
```
Fix the auth bug +500k          ← 500K token budget
Refactor the API layer +1m      ← 1M token budget
Full code review +2.5m          ← 2.5M token budget
```

The `+Nk` / `+Nm` syntax works at the **start or end** of your message.

### Verbose Syntax
```
Use 500k tokens to fix the auth bug
Spend 2M tokens on the refactor
```

### How Budgets Work

- Claude Code tracks cumulative token usage for the task
- At percentage milestones, it receives: `"Stopped at N% of token target (X / Y). Keep working — do not summarize."`
- When the budget is reached, the task is wrapped up

### Budget Guidelines

| Task Type | Suggested Budget |
|-----------|-----------------|
| Simple bug fix | `+200k` - `+500k` |
| Feature implementation | `+500k` - `+1m` |
| Large refactor | `+1m` - `+3m` |
| Full codebase review | `+2m` - `+5m` |

## Cost Estimation Formulas

From the source code, cost is calculated as:

```
cost = (input_tokens / 1M) × input_price
     + (output_tokens / 1M) × output_price
     + (cache_read_tokens / 1M) × cache_read_price
     + (cache_creation_tokens / 1M) × cache_write_price
     + web_search_requests × $0.01
```

### Quick Mental Math

For **Opus 4.6** ($5/$25):
- 100K input tokens = $0.50
- 100K output tokens = $2.50
- 100K cache read = $0.05 (10x cheaper!)

For **Sonnet** ($3/$15):
- 100K input tokens = $0.30
- 100K output tokens = $1.50
- 100K cache read = $0.03

## Warning Signs

You might be wasting tokens if:
- `/cost` shows >$5 for a simple task
- You see repeated auto-compact notifications
- Cache read ratio is low (means you're breaking cache)
- Output tokens are very high (extended thinking on simple tasks)

## Next Steps

- [How Tokens Work](01-how-tokens-work.md) — understand the fundamentals
- [Practical Tips](08-practical-tips.md) — reduce your spending
