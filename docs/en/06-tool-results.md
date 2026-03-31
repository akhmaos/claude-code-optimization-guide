# Tool Result Limits

Tool results (file contents, bash output, search results) are often the largest consumers of context tokens. Understanding the limits helps you write more efficient prompts.

## Size Limits (from `toolLimits.ts`)

| Limit | Value | What Happens When Exceeded |
|-------|-------|---------------------------|
| **Per tool** | 50,000 characters | Saved to disk; Claude gets preview + file path |
| **Per tool (tokens)** | 100,000 tokens (~400KB) | Hard cap, result truncated |
| **Per message (aggregate)** | 200,000 characters | Largest results in the batch get persisted to disk |

### Per-Message Budget

When you trigger parallel tool calls (e.g., Claude reads 5 files at once), the aggregate limit prevents context explosion:

```
5 files × 40K chars each = 200K chars → EXACTLY at budget
5 files × 50K chars each = 250K chars → largest files get truncated
```

The `applyToolResultBudget()` function trims the largest results first until the total is under 200K characters.

## How Persistence Works

When a tool result exceeds 50K characters:
1. Full result is saved to a temporary file on disk
2. Claude receives a truncated preview (~first few KB)
3. The file path is included so Claude can read specific sections if needed

This means Claude loses the full context of very large results but can still access the data on demand.

## Token Estimation for Tool Results

Claude Code uses these heuristics:

```
Text:    1 token ≈ 4 bytes (BYTES_PER_TOKEN = 4)
JSON:    1 token ≈ 2 bytes (denser encoding)
Images:  ~2,000 tokens per image (conservative estimate)
```

## Common Token-Heavy Operations

| Operation | Typical Token Cost | How to Reduce |
|-----------|-------------------|---------------|
| `Read` a 1000-line file | 5,000-15,000 | Use `offset` + `limit` |
| `Bash` with verbose output | 5,000-50,000 | Pipe through `head` or `tail` |
| `Grep` with many matches | 5,000-30,000 | Use more specific patterns, add `head_limit` |
| `Glob` in large repos | 1,000-10,000 | Use more specific patterns |
| `WebFetch` on large pages | 10,000-50,000 | Target specific URLs |

## Tips for Reducing Tool Result Costs

### 1. Read Only What You Need

```
Bad:  "Read the entire config file"
Good: "Read src/config.ts lines 45-80"
```

The `Read` tool supports `offset` and `limit` parameters. Use them.

### 2. Use Specific Search Patterns

```
Bad:  "Search for error handling in the project"
Good: "Grep for 'catch.*Error' in src/services/*.ts"
```

The `Grep` tool has `head_limit` (default 250 results) and `glob` filters. Use them to narrow results.

### 3. Avoid Bash for File Operations

```
Bad:  "Run find . -name '*.ts' | xargs grep 'TODO'"
Good: "Grep for 'TODO' in *.ts files"
```

Dedicated tools (Grep, Glob, Read) are token-optimized. Bash output is raw and unstructured.

### 4. Limit Bash Output

If you must use Bash:
```
Bad:  "Run npm test"     (can produce thousands of lines)
Good: "Run npm test 2>&1 | tail -50"
```

### 5. Be Specific About What You Need

```
Bad:  "Show me the database models"
Good: "Read src/models/User.ts"
```

Vague instructions lead to broad searches → many tool calls → lots of tokens.

## Next Steps

- [Sub-agents](07-subagents.md) — isolating heavy exploration from your main context
- [Practical Tips](08-practical-tips.md) — more ways to reduce token usage
