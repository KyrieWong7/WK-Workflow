---
name: learn
description: Extract discoveries into persistent skills or MEMORY.md entries. Use when a non-obvious solution is found, a bug is debugged, or a workflow is discovered worth preserving.
argument-hint: "[skill-name or discovery description]"
---

# Learn Skill

## Overview

Capture discoveries so they persist across sessions and contexts.

## Decision Tree

```
Discovery made
  │
  ├── Is it a one-liner fix?
  │   YES → [LEARN] tag in MEMORY.md
  │   NO  ↓
  │
  ├── Is it a multi-step workflow?
  │   YES → Create full skill in .cursor/skills/
  │   NO  ↓
  │
  └── Is it a correction to existing knowledge?
      YES → Update existing skill or rule
      NO  → Document in session log
```

## Phase 1: EVALUATE

Ask:
1. Was this non-obvious? (>10 min to figure out)
2. Would future-me benefit from knowing this?
3. Is this likely to recur?

If YES to any → continue

## Phase 2: CHECK EXISTING

1. Search `.cursor/skills/` for related skills
2. Search `MEMORY.md` for related entries
3. Decide: Create new? Update existing?

## Phase 3: CREATE/UPDATE

### For [LEARN] Tags (One-Liners)

Add to `MEMORY.md`:
```markdown
- [LEARN:category] wrong → right
```

Categories:
- `r-code`: R programming
- `stata`: Stata programming
- `citation`: Reference issues
- `workflow`: Process issues
- `math`: Mathematical errors

### For Full Skills

Create `.cursor/skills/[name]/SKILL.md`:

```markdown
---
name: [skill-name]
description: [What it does] + [When to use] + [Trigger phrases]
argument-hint: "[hint]"
---

# [Skill Name]

## Problem
[What problem does this solve?]

## Solution
[Step-by-step solution]

## Example
[Concrete example]

## Verification
[How to verify it worked]
```

## Phase 4: QUALITY GATE

Check:
- [ ] Description has specific triggers
- [ ] Solution verified to work
- [ ] Specific enough to be actionable
- [ ] General enough to be reusable

## When to Use /learn

| Trigger | Example |
|---------|---------|
| Non-obvious debugging | 10+ min investigation |
| Misleading errors | Error message was wrong |
| Workarounds | Found limitation with fix |
| Undocumented APIs | Tool trick not in docs |
| Trial-and-error | Multiple attempts needed |
| Repeatable workflows | Would do again |
