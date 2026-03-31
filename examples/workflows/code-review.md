# Workflow: Efficient Code Review

## The Problem

Code reviews can be token-expensive because they involve reading many files and understanding context across the codebase.

## The Efficient Approach

### Step 1: Scope the Review

```
Prompt: "Review the changes in the current branch compared to main.
         Focus on: correctness, security, and error handling. +500k"
```

Key elements:
- Specific comparison target (main branch)
- Focus areas (prevents broad exploration)
- Token budget (prevents runaway analysis)

### Step 2: Use Git Diff, Not File Reads

```
Prompt: "Show me git diff main...HEAD for the changed files"
```

Git diff shows only changes — much smaller than reading full files.

### Step 3: Targeted Deep Dives

If something needs closer inspection:

```
Prompt: "Read src/auth/validate.ts lines 42-60 — I need to understand
         the error handling change there"
```

### Step 4: Batch Your Feedback

Don't ask for review comments one file at a time:

```
Bad:  "Review file A" → "Review file B" → "Review file C"
Good: "Review the changes in src/auth/, src/api/, and src/utils/"
```

## Slash Command Alternative

Claude Code has a built-in review command:

```
/review-pr 123
```

This is a pre-optimized workflow — use it when available.

## Cost Comparison

| Approach | Estimated Cost |
|----------|---------------|
| "Review this PR" (unbounded) | $3-10 |
| Scoped + budget + git diff | $0.50-2 |
| `/review-pr` slash command | $0.50-1.50 |
