---
name: self-improvement
description: "Captures learnings, errors, and corrections to enable continuous improvement across sessions. Use when: (1) A command or operation fails unexpectedly, (2) User corrects you, (3) User requests a capability that doesn't exist, (4) An external API or tool fails, (5) You realize your knowledge is outdated or incorrect, (6) A better approach is discovered for a recurring task."
metadata:
  category: self-improving-agent
---

# Self-Improvement Skill

Log learnings, errors, and corrections to persistent memory for continuous improvement. Important learnings are automatically promoted to skills.

## When to Log

| Situation | Action |
|-----------|--------|
| Command/operation fails | Save to memory with `[PRIORITY: high] [AREA: infra/tests/etc]` |
| User corrects you | Save to memory as `correction` fact |
| User wants missing capability | Offer to save as feature request to memory |
| API/external tool fails | Save to memory with integration details |
| Knowledge was outdated | Save to memory as `knowledge_gap` |
| Found better approach | Save to memory as `best_practice` |
| Same issue reappears | **Update** existing memory entry — increment Count |

## Structured Memory Format

Use this format when saving learnings via `memory(action='add')`:

```
[PRIORITY: high|medium|low] [AREA: frontend|backend|infra|tests|docs|config|general]
Key learning in one line.
  Detail: 1-2 lines of context
  Count: 1
  First: YYYY-MM-DD
  Last: YYYY-MM-DD
```

### Priority Guidelines

| Priority | When to Use |
|----------|-------------|
| `high` | Blocks core functionality, security issue, frequently recurring (Count >= 3) |
| `medium` | Moderate impact, workaround exists |
| `low` | Minor inconvenience, edge case, nice-to-have |

### Area Tags

| Area | Scope |
|------|-------|
| `frontend` | UI, components, client-side code |
| `backend` | API, services, server-side code |
| `infra` | CI/CD, deployment, Docker, cloud |
| `tests` | Test files, testing utilities, coverage |
| `docs` | Documentation, comments, READMEs |
| `config` | Configuration files, environment, settings |
| `general` | User preferences, conventions, behavioral patterns |

## Recurrence Tracking

When the **same topic** comes up again:

1. **Do NOT add a new entry** — use `memory(action='replace')` to update the existing one
2. Increment `Count` by 1
3. Update `Last` to today's date
4. If `Count >= 3` across different tasks, **offer to promote** to a skill

## Promotion to Skill

When a learning qualifies, create a skill:

### When to Promote

ALL of these should be true:
- Same pattern appeared 3+ times across different tasks
- The fix/approach is non-obvious (required debugging to discover)
- The pattern is broadly applicable (not a one-off fix)

### How to Promote

1. Use `skill_manage(action='create')` to save it as a skill
2. Update the original memory entry: replace it to note it was promoted

### Detection Triggers

Watch for these signals in conversation:
- "Save this as a skill" or user asks you to remember a procedure
- "I keep running into this" / user expresses frustration with a recurring issue
- "This would be useful for other projects"
- "Remember this pattern next time"

## Linked Files

- `references/cron-gotchas.md` — Cron job creation bugs: enabled_toolsets silent failure, jobs that never run, output verification.
- `references/xiaping-platform-api-key-masking.md` — Xiaping (虾评) platform masks API keys as `***` in JSON; workaround using raw terminal capture or verbose curl.

## Detection Triggers

Automatically log when you notice:

**Corrections** (→ memory entry):
- "No, that's not right..."
- "Actually, it should be..."
- "You're wrong about..."
- "That's outdated..."

**Knowledge Gaps** (→ memory entry):
- User provides information you didn't know
- Documentation you referenced is outdated
- API behavior differs from your understanding

**Errors** (→ memory entry):
- Command returns non-zero exit code
- Exception or stack trace
- Unexpected output or behavior

**Better Approaches** (→ memory entry):
- You discover a more efficient method
- User shows a better way to do something
- A workaround proves more reliable than the standard approach

## Best Practices

1. **Log immediately** — context is freshest right after the issue
2. **Be specific** — future sessions need to understand quickly
3. **Include reproduction steps** — especially for errors
4. **Use consistent categories** — enables filtering across sessions
5. **Promote when recurring** — Count >= 3 → consider skill
6. **Update existing entries** — never create duplicates for the same issue
