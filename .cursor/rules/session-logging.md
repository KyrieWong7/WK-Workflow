# Session Logging Protocol

**Session logs capture WHY decisions were made, not just WHAT changed.**

## Three Logging Triggers

### 1. After Plan Approval

Create the log with:
- Goal statement
- Plan summary
- Rationale for chosen approach
- Rejected alternatives and why

**Why now:** Context is richest immediately after planning.

### 2. During Implementation

Append to the log as you work:
- Design decisions made
- Problems discovered
- Approach deviations from plan
- 1-3 line entries, immediately

**Why now:** Context gets compressed; decisions in conversation will be lost.

### 3. At Session End

Add final section:
- What was accomplished
- Open questions
- Unresolved issues
- Next steps

## Log Format

```markdown
# Session Log: YYYY-MM-DD [Brief Description]

## Goal
[What we're trying to accomplish]

## Plan Summary
[Key steps from the approved plan]

## Rationale
[Why this approach was chosen]
[Alternatives considered and rejected]

## Implementation Log

### [Timestamp] Decision: [Brief Title]
[1-3 line explanation]

### [Timestamp] Issue: [Brief Title]
[What was found and how it was resolved]

## Outcome
[What was accomplished]

## Open Questions
- [Question 1]
- [Question 2]

## Next Steps
- [Step 1]
- [Step 2]
```

## File Location

Save to: `quality_reports/session_logs/YYYY-MM-DD_description.md`

## Why Session Logs Matter

Git records WHAT changed. Session logs record WHY.

Example:
- Commit: "Update model parameters"
- Session log: "Recalibrated the dual-agent model because the original γ=3.0 produced implausible risk premia. Tried γ∈[1,5], settled on γ=2.1 which matches both the style and idiosyncratic multiplier targets within 2%."

## Weekly Reviews

For multi-project work:
1. Read all session logs from past week
2. Synthesize status report
3. Identify priorities and blockers
