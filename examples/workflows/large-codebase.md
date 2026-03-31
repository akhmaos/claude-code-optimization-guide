# Workflow: Navigating Large Codebases (500K+ LOC)

## The Problem

Large codebases can burn through tokens fast if you explore blindly. A single "help me understand this project" can cost $5-10 in token usage.

## The Efficient Approach

### Step 1: Write CLAUDE.md First (if it doesn't exist)

Before using Claude Code, spend 5 minutes creating a basic CLAUDE.md:

```markdown
# ProjectName
[Tech stack, commands, structure]
```

This saves 200+ tokens per future session.

### Step 2: Use Sub-agents for Exploration

```
Prompt: "Use an Explore agent to understand the authentication flow
         in this project. Look at src/auth/ and any middleware."
```

The agent explores in its own context, returning a compact summary.

### Step 3: Target Specific Files

Once you know the structure from the agent's summary:

```
Prompt: "Read src/auth/middleware.ts lines 1-50"
```

Not:
```
Prompt: "Show me the auth code"  (triggers broad search)
```

### Step 4: Use Grep with Limits

```
Prompt: "Grep for 'authenticate' in src/**/*.ts with head_limit 10"
```

Not:
```
Prompt: "Find all authentication-related code"
```

### Step 5: Compact Between Topics

```
/compact keep auth middleware understanding, forget the exploration details
```

Then move to the next area:

```
Prompt: "Now let's look at the database layer. Read src/db/connection.ts"
```

## Token Budget

For a large codebase exploration session, set a budget:

```
Help me understand the API layer of this project +1m
```

## Cost Comparison

| Approach | Estimated Cost |
|----------|---------------|
| Unstructured exploration | $5-15 |
| Agent + targeted reads | $1-3 |
| With CLAUDE.md + compact | $0.50-2 |
