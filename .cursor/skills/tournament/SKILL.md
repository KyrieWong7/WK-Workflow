---
name: tournament
description: Head-to-head comparison of papers/theories using LLM judge. Use when comparing two research outputs, ranking candidates, or evaluating relative quality.
argument-hint: "[paper1] [paper2]"
---

# Tournament Skill (Project APE)

## Overview

Run head-to-head comparisons between research outputs using an LLM judge, with position-swapping to control for bias.

## Tournament Protocol

### 1. Match Setup
```
Paper A vs Paper B
  │
  ├── Round 1: [A, B] order
  │   └── Judge evaluates
  │
  └── Round 2: [B, A] order (position swap)
      └── Judge evaluates
```

### 2. Outcome Rules
- Paper must win BOTH rounds to win the match
- Otherwise, it's a tie
- This controls for LLM position bias

### 3. Rating Update
Use TrueSkill algorithm to update ratings after each match.

## Evaluation Dimensions

The judge evaluates on five dimensions (simulating senior economics journal editor):

| Dimension | Weight | Description |
|-----------|--------|-------------|
| Identification Strategy | 30% | Is causal inference credible? |
| Novelty | 20% | Does it contribute new insights? |
| Policy Relevance | 20% | Does it matter for real decisions? |
| Execution Quality | 20% | Is analysis well-executed? |
| Appropriate Scope | 10% | Is scope well-calibrated? |

## Judge Prompt Template

```markdown
You are a senior editor at a top economics journal (AER, Econometrica, QJE).

You will compare two research papers/models and determine which is preferred.

## Evaluation Criteria

1. **Identification Strategy (30%)**: Is the causal identification credible? Are assumptions reasonable and stated clearly?

2. **Novelty (20%)**: Does this contribute genuinely new insights? Is it differentiated from existing work?

3. **Policy Relevance (20%)**: Does this matter for real-world policy decisions? Are implications clear?

4. **Execution Quality (20%)**: Is the analysis well-executed? Are methods appropriate? Are robustness checks included?

5. **Appropriate Scope (10%)**: Is the scope well-calibrated? Are limitations acknowledged?

## Papers to Compare

### Paper A
[Paper A content]

### Paper B
[Paper B content]

## Your Task

1. Score each paper on each dimension (0-100)
2. Calculate weighted total
3. Declare winner or tie
4. Provide brief justification

## Output Format

```json
{
  "paper_a": {
    "identification": 75,
    "novelty": 80,
    "policy_relevance": 70,
    "execution": 85,
    "scope": 80,
    "total": 77.5
  },
  "paper_b": {
    "identification": 80,
    "novelty": 70,
    "policy_relevance": 85,
    "execution": 75,
    "scope": 75,
    "total": 77.0
  },
  "winner": "A",
  "justification": "Paper A has stronger identification..."
}
```
```

## TrueSkill Rating System

### Parameters
- μ (mu): Estimated skill level (starts at 25)
- σ (sigma): Uncertainty (starts at 25/3 ≈ 8.33)
- β: Performance variation (default: σ/2)
- τ: Dynamic factor (default: σ/100)

### Update Rules
After a match:
```python
from trueskill import Rating, rate_1vs1

r1 = Rating(mu=paper1.mu, sigma=paper1.sigma)
r2 = Rating(mu=paper2.mu, sigma=paper2.sigma)

if winner == "paper1":
    new_r1, new_r2 = rate_1vs1(r1, r2)
elif winner == "paper2":
    new_r2, new_r1 = rate_1vs1(r2, r1)
else:  # tie
    new_r1, new_r2 = rate_1vs1(r1, r2, drawn=True)
```

### Conservative Rating
Papers are ranked by: `μ - 3σ`

This ensures papers need consistent wins across multiple matches.

## Match Report Format

```markdown
# Tournament Match Report

## Match ID: [timestamp]

## Competitors
- Paper A: [Title] (μ=X, σ=Y)
- Paper B: [Title] (μ=X, σ=Y)

## Round 1 (Order: A, B)
| Dimension | Paper A | Paper B |
|-----------|---------|---------|
| Identification | [score] | [score] |
| Novelty | [score] | [score] |
| Policy Relevance | [score] | [score] |
| Execution | [score] | [score] |
| Scope | [score] | [score] |
| **Total** | **[score]** | **[score]** |

Winner: [A/B]

## Round 2 (Order: B, A)
[Same table format]

Winner: [A/B]

## Match Result
**Winner:** [A/B/Tie]

## Rating Updates
- Paper A: μ [old→new], σ [old→new], Conservative [old→new]
- Paper B: μ [old→new], σ [old→new], Conservative [old→new]

## Justification
[Why the winner was preferred]
```

## Usage Examples

```bash
# Compare two theory proposals
/tournament theory_proposal_1.md theory_proposal_2.md

# Compare paper drafts
/tournament draft_v1.pdf draft_v2.pdf

# Run tournament round (batch)
/tournament --batch models/*.md
```
