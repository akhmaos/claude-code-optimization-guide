# Practical Tips for Token Optimization

20+ actionable strategies organized by impact.

## High Impact

### 1. Write a CLAUDE.md File
Put project context, coding standards, and common patterns in `CLAUDE.md` at your project root. This prevents you from re-explaining the same things every session.

```markdown
# CLAUDE.md
This is a Next.js 14 app with TypeScript, Prisma ORM, and PostgreSQL.
- Run tests: `npm test`
- Run dev server: `npm run dev`
- Database migrations: `npx prisma migrate dev`
- Use `src/lib/db.ts` for database access
```

**Savings**: 200-500 tokens per session that you'd otherwise spend typing context.

### 2. Use Specific File Paths

```
Bad:  "Find the user authentication code"     → multiple searches
Good: "Read src/auth/middleware.ts"            → single read
```

**Savings**: 3,000-30,000 tokens per vague request avoided.

### 3. Run `/compact` Proactively

Don't wait for auto-compact at 167K tokens. If you've finished one task and are moving to another:

```
/compact keep the API endpoint changes, forget the test exploration
```

**Savings**: Prevents unnecessary context from inflating future API calls.

### 4. Set Token Budgets

For bounded tasks, add a budget to your prompt:

```
Fix the null pointer exception in auth.ts +500k
```

This caps the session at 500K tokens, preventing runaway exploration.

### 5. Use Sub-agents for Research

```
Bad:  "Help me understand how the caching layer works"
      (reads 20 files into main context = 100K tokens)

Good: "Use an Explore agent to understand how the caching layer works"
      (agent reads files, returns 500 token summary)
```

**Savings**: 90-99% of research tokens stay out of main context.

## Medium Impact

### 6. Read Files with offset/limit

```
Bad:  "Read src/services/auth.ts"            → all 2000 lines
Good: "Read src/services/auth.ts lines 45-80" → just 35 lines
```

### 7. Use Grep/Glob Instead of Bash

Dedicated tools are optimized for token-efficient output:

```
Bad:  "Run: find . -name '*.ts' | xargs grep 'TODO'"
Good: "Grep for 'TODO' in *.ts files with head_limit 20"
```

### 8. Keep Sessions Stable

Each action that breaks prompt cache costs extra:
- Don't switch models mid-session
- Don't toggle fast mode unless needed
- Don't restart sessions just because

### 9. Be Specific in Prompts

```
Bad:  "Fix the bug"
Good: "Fix the null check on line 42 of src/auth/validate.ts —
       it should handle undefined email gracefully"
```

Specific prompts = fewer clarification rounds = fewer API calls.

### 10. Use Plan Mode for Complex Tasks

For large features, enter plan mode first:
- Read-only exploration (no wasted edits)
- Aligned on approach before writing code
- Fewer revisions and rollbacks

### 11. Batch Related Questions

```
Bad:  "What does function A do?"
      "What does function B do?"
      "How do A and B relate?"  (3 separate API calls)

Good: "Explain functions A and B in src/utils.ts and how they relate"
      (1 API call)
```

### 12. Limit Bash Output

```
Bad:  "Run npm test"               → 500 lines of output
Good: "Run npm test 2>&1 | tail -20" → last 20 lines
```

## Lower Impact (but adds up)

### 13. Don't Ask Claude to Explain What It Just Did

Claude Code shows you the diffs. Don't ask "what did you change?" — read the diff.

### 14. Avoid Unnecessary Confirmations

Don't ask "are you sure?" or "can you double-check?" — each confirmation is another API call.

### 15. Use Slash Commands for Common Tasks

```
/commit          → optimized commit workflow
/review-pr 123   → optimized PR review
/compact         → manual context compression
```

Slash commands are pre-built, efficient workflows.

### 16. Don't Read Documentation Files Unless Needed

README.md, CHANGELOG.md, etc. are often large and rarely needed for coding tasks.

### 17. Use Targeted Git Commands

```
Bad:  "Show me the git log"          → full history
Good: "Show me the last 5 commits"   → focused output
```

### 18. Close Topics Explicitly

When you're done with a topic, say so:
```
"The auth fix is done. Now let's work on the API endpoint."
```

This helps Claude (and you) mentally compact the conversation.

### 19. Reuse Context Between Related Tasks

If you're doing multiple related tasks, do them in one session:
```
Session 1: Fix auth bug → context built up
Session 2: Fix related validation bug → context rebuilt from scratch ($$$)

Better: Fix auth bug → fix related validation bug (reuse context)
```

### 20. Monitor Costs Regularly

Run `/cost` periodically to see:
- Total session cost
- Per-model breakdown
- Cache hit rates

If costs seem high, you're probably doing something inefficiently.

## Next Steps

- [Environment Variables](09-environment-vars.md) — advanced configuration
- [CLAUDE.md Guide](10-claudemd-guide.md) — writing effective project instructions
