---
name: commit
description: Stage, commit, and optionally push changes. Use when user says "commit", "save changes", or work is ready to be preserved.
argument-hint: "[commit message]"
---

# Commit Skill

## Overview

Stage and commit changes with proper git hygiene.

## Steps

### 1. Pre-Commit Checks
1. Run `git status` to see changes
2. Run `git diff` to review changes
3. Check quality score meets threshold (80+)
4. Verify no sensitive files (.env, credentials)

### 2. Stage Changes
```bash
git add [specific files]
# OR for all changes:
git add -A
```

### 3. Commit
```bash
git commit -m "$(cat <<'EOF'
type: brief description

- Detail 1
- Detail 2

EOF
)"
```

### 4. Optional Push
```bash
git push origin [branch]
```

## Commit Message Format

```
type: brief description (50 chars max)

- What changed
- Why it changed
- Any caveats

Refs: #issue (if applicable)
```

### Types
- `feat:` New feature
- `fix:` Bug fix
- `docs:` Documentation
- `style:` Formatting
- `refactor:` Code restructuring
- `test:` Adding tests
- `chore:` Maintenance

## Quality Gate

**Do not commit if score < 80**

Exception: User explicitly overrides with justification.

## Checklist

- [ ] All tests pass
- [ ] Code compiles/runs
- [ ] No sensitive data
- [ ] Quality score >= 80
- [ ] Commit message clear
