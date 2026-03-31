# Model Selection & Pricing

Choosing the right model for each task is the simplest way to control costs.

## Pricing Tiers (from source code)

All prices are per million tokens (Mtok):

| Model | Input | Output | Cache Read | Cache Write | Web Search |
|-------|-------|--------|------------|-------------|------------|
| **Haiku 3.5** | $0.80 | $4.00 | $0.08 | $1.00 | $0.01/req |
| **Haiku 4.5** | $1.00 | $5.00 | $0.10 | $1.25 | $0.01/req |
| **Sonnet 4/4.5/4.6** | $3.00 | $15.00 | $0.30 | $3.75 | $0.01/req |
| **Opus 4.5/4.6** | $5.00 | $25.00 | $0.50 | $6.25 | $0.01/req |
| **Opus 4/4.1** | $15.00 | $75.00 | $1.50 | $18.75 | $0.01/req |
| **Fast Opus 4.6** | $30.00 | $150.00 | $3.00 | $37.50 | $0.01/req |

### Cost Multipliers Relative to Sonnet

| Model | Input Cost | Output Cost |
|-------|-----------|-------------|
| Haiku 4.5 | **0.3x** | **0.3x** |
| Sonnet | **1x** (baseline) | **1x** |
| Opus 4.6 | **1.7x** | **1.7x** |
| Opus 4/4.1 | **5x** | **5x** |
| Fast Opus 4.6 | **10x** | **10x** |

## When to Use Each Model

### Haiku 4.5 — Simple Tasks
- File formatting and renaming
- Simple search and replace
- Generating boilerplate
- Classification tasks
- **Cost**: ~$0.01-0.05 per interaction

### Sonnet — Default Choice
- Code review
- Bug fixes
- Feature implementation
- Refactoring
- **Cost**: ~$0.05-0.30 per interaction

### Opus 4.5/4.6 — Complex Reasoning
- Architecture design
- Complex debugging across multiple files
- Large refactors
- Understanding unfamiliar codebases
- **Cost**: ~$0.10-0.50 per interaction

### Fast Opus 4.6 — Speed Critical
- Same model as Opus 4.6 but with faster output
- **6x more expensive** than regular Opus 4.6
- Use ONLY when response speed is critical
- Toggle with `/fast` command

## The Fast Mode Trap

Fast mode (`/fast`) does NOT switch to a different model. It uses the same Opus 4.6 but at **6x the price**:

```
Regular Opus 4.6:  $5/$25 per Mtok
Fast Opus 4.6:     $30/$150 per Mtok
```

A session that costs $2 in normal mode costs **$12** in fast mode. Only use it when speed truly matters.

## Internal Model Routing

Claude Code uses Haiku internally for some operations:
- **Token counting fallback** — `countTokensViaHaikuFallback()`
- **Memory relevance selection** — finding relevant memories
- These are cheap by design

## Tips

1. **Start with Sonnet** — switch to Opus only for tasks that require deeper reasoning
2. **Never leave Fast Mode on** — toggle it off after speed-critical work
3. **Check `/cost`** — it shows per-model breakdown
4. **Consider Haiku** for batch operations on many files

## Next Steps

- [Tool Result Limits](06-tool-results.md) — managing the cost of tool outputs
- [Cost Monitoring](11-cost-monitoring.md) — tracking spending in real-time
