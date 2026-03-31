# Sub-agents and Context Isolation

Sub-agents are one of the most powerful token optimization tools in Claude Code. They run in a separate context window, protecting your main conversation from context bloat.

## How Sub-agents Work

When Claude spawns a sub-agent:

```
Main conversation (200K context)
    ↓ spawns Agent
Sub-agent (own 200K context)
    ↓ does heavy research
    ↓ reads many files
    ↓ runs searches
    ↓ returns summary
Main conversation receives: compact summary (few hundred tokens)
```

The sub-agent's full exploration (potentially 100K+ tokens of file reads and searches) is **not** added to the main context. Only the final summary comes back.

## Available Agent Types

| Agent Type | Use Case |
|------------|----------|
| `Explore` | Fast codebase exploration, file search, code understanding |
| `Plan` | Architecture design, implementation planning |
| `general-purpose` | Multi-step tasks, complex research |
| `golang-pro` | Go-specific development |
| `backend-developer` | Backend API and service development |
| `rust-engineer` | Rust systems programming |

## When to Use Sub-agents

### Use a sub-agent when:
- **Exploring unfamiliar code** — the agent reads many files, you get a summary
- **Searching for patterns** — "find all places where X is used" across a large codebase
- **Research tasks** — understanding how a system works before making changes
- **Parallel investigations** — launch multiple agents simultaneously

### Don't use a sub-agent when:
- You already know the exact file and location
- The task is a simple read or edit
- You need the agent to modify files (agents should generally research, not edit)

## Token Savings Example

**Without sub-agent** (exploratory task in main context):
```
Read file A (5K tokens) + Read file B (8K) + Grep results (3K) +
Read file C (6K) + Read file D (4K) = 26K tokens in main context
```

**With sub-agent:**
```
Agent explores: 26K tokens (in agent's own context)
Returns summary: ~500 tokens to main context
Savings: 25,500 tokens in main context
```

## Parallel Agents

You can launch multiple agents simultaneously for independent investigations:

```
Agent 1: "Explore how authentication works"
Agent 2: "Find all database query patterns"
Agent 3: "Understand the testing setup"
```

Each runs in its own context. Results come back as compact summaries.

## Tips

1. **Give agents clear, specific prompts** — vague prompts lead to broad exploration = wasted tokens
2. **Tell agents whether to research or implement** — they don't know your intent
3. **Use `Explore` type for quick searches** — it's optimized for speed
4. **Launch agents in parallel** when investigations are independent
5. **Limit to 3 agents max** — more agents don't always mean better results

## Next Steps

- [Practical Tips](08-practical-tips.md) — complete list of optimization strategies
