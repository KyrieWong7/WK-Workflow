# Orchestrator Protocol: Contractor Mode

**After a plan is approved, the orchestrator takes over autonomously.**

## The Loop

```
Plan approved → orchestrator activates
  │
  Step 1: IMPLEMENT — Execute plan steps
  │
  Step 2: VERIFY — Run code, check outputs
  │         If verification fails → fix → re-verify
  │
  Step 3: REVIEW — Run review agents (by task type)
  │         - Math audit for derivations
  │         - Code review for scripts
  │         - Domain review for substance
  │
  Step 4: FIX — Apply fixes (critical → major → minor)
  │
  Step 5: RE-VERIFY — Confirm fixes are clean
  │
  Step 6: SCORE — Apply quality-gates rubric
  │         - Fit (50%): Empirical target matching
  │         - Plausibility (25%): Parameter reasonableness  
  │         - Parsimony (25%): Model simplicity
  │
  └── Score >= threshold?
        YES → Present summary to user
        NO  → Loop back to Step 3 (max 5 rounds)
              After max rounds → present with remaining issues
```

## Agent Selection by Task Type

| Task Type | Agents Selected |
|-----------|-----------------|
| Theory derivation | math-auditor + domain-reviewer |
| Data analysis | code-scanner + replication-verifier |
| Paper writing | proofreader + simulated-referee |
| Model calibration | math-auditor + tournament-judge |

## Limits

- **Main loop:** max 5 review-fix rounds
- **Critic-fixer sub-loop:** max 5 rounds
- **Verification retries:** max 2 attempts
- Never loop indefinitely

## "Just Do It" Mode

When user says "just do it" / "handle it":
- Skip final approval pause
- Auto-commit if score >= 80
- Still run the full verify-review-fix loop
- Still present the summary

## Integration with Tournament System

For comparative evaluations:
1. Generate candidate theories/analyses
2. Run Tournament match with position-swap
3. Update TrueSkill ratings
4. Report results with confidence intervals
