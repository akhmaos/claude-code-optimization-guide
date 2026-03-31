# Workflow: Debugging Without Burning Context

## The Problem

Debugging is inherently exploratory. Without discipline, it spirals into reading dozens of files and running many commands, consuming 100K+ tokens.

## The Efficient Approach

### Step 1: Provide Maximum Context Upfront

```
Prompt: "There's a null pointer exception in src/auth/validate.ts:42
         when email is undefined. The error trace shows it's called from
         src/api/routes/login.ts:15. Fix the null check. +500k"
```

What this does right:
- Exact file and line number
- Error description
- Call chain
- Clear action needed
- Token budget

### Step 2: Don't Explore — Direct Claude

```
Bad:  "There's a bug somewhere in authentication"
      → Claude reads 10+ files searching for the bug = 50K+ tokens

Good: "Read src/auth/validate.ts:40-50 and fix the null check on email"
      → Claude reads 10 lines, makes the fix = 2K tokens
```

### Step 3: Use Error Output Efficiently

If you have an error trace:

```
Prompt: "Fix this error: [paste only the relevant 5-10 lines of trace]"
```

Not:
```
Prompt: "Fix this error: [paste 200 lines of full stack trace]"
```

### Step 4: Test Incrementally

```
Prompt: "Run npm test -- --testPathPattern=auth/validate.test
         and show me only failures (tail -20)"
```

Not:
```
Prompt: "Run the full test suite"
```

### Step 5: If You Must Explore, Use Agents

```
Prompt: "Use an Explore agent to find all callers of validateEmail()
         in src/. I need to understand the impact of changing its
         null handling."
```

The agent's exploration stays in its own context.

## Cost Comparison

| Approach | Estimated Cost |
|----------|---------------|
| "Fix the bug" (vague) | $3-15 |
| Exact file + line + error | $0.30-1 |
| With agent for impact analysis | $0.50-2 |
