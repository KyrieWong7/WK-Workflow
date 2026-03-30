---
name: tournament
description: Head-to-head comparison of papers/theories using LLM judge. Use when comparing two research outputs, ranking candidates, or evaluating relative quality.
argument-hint: "[paper1] [paper2]"
---

# Tournament Skill (Project APE - 完整实现)

**来源:** https://ape.socialcatalystlab.org/ & https://github.com/SocialCatalystLab/ape-papers

---

## Overview

APE 使用 **Tournament 评估系统** 替代单一评分，通过头对头比较和 TrueSkill 评分确定论文排名。

**核心原则:**
- **Position Swapping**: 每场比赛两轮，交换顺序控制 LLM 位置偏差
- **No Self-Evaluation**: Judge 模型不能是生成论文的模型
- **Conservative Rating**: 用 μ - 3σ 排名，要求一致性

---

## Tournament Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                    APE Tournament System                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   ┌─────────────┐        ┌──────────────────┐                  │
│   │  Paper A    │        │  Tournament      │                  │
│   │  (μ_a, σ_a) │◄──────►│  Judge           │                  │
│   └─────────────┘        │  (Gemini Flash)  │                  │
│                          │                  │                  │
│   ┌─────────────┐        │  Criteria:       │                  │
│   │  Paper B    │◄──────►│  - Identification│                  │
│   │  (μ_b, σ_b) │        │  - Novelty       │                  │
│   └─────────────┘        │  - Policy        │                  │
│                          │  - Execution     │                  │
│         │                │  - Scope         │                  │
│         │                └──────────────────┘                  │
│         ▼                                                       │
│   ┌─────────────────────────────────────┐                      │
│   │         Match Protocol               │                      │
│   │   Round 1: [A, B] → Winner X        │                      │
│   │   Round 2: [B, A] → Winner Y        │                      │
│   │   Final: X == Y ? X : TIE           │                      │
│   └─────────────────────────────────────┘                      │
│         │                                                       │
│         ▼                                                       │
│   ┌─────────────────────────────────────┐                      │
│   │      TrueSkill Rating Update        │                      │
│   │   Conservative: μ - 3σ              │                      │
│   └─────────────────────────────────────┘                      │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Tournament Protocol

### 1. Match Setup
```
Paper A vs Paper B
  │
  ├── Round 1: Present as [A, B]
  │   └── Judge evaluates → Winner Round 1
  │
  └── Round 2: Present as [B, A] (position swap)
      └── Judge evaluates → Winner Round 2
```

### 2. Outcome Rules
- **Win**: Paper must win BOTH rounds
- **Tie**: If winners differ between rounds
- This controls for LLM position bias

### 3. Rating Update
TrueSkill Bayesian rating after each match.

---

## Evaluation Dimensions (APE)

The judge evaluates on five dimensions (simulating senior economics journal editor):

| Dimension | Weight | Description | Reward/Penalty |
|-----------|--------|-------------|----------------|
| Identification Strategy | 30% | Is causal inference credible? | +5 if novel instrument/design |
| Novelty | 20% | Does it contribute new insights? | -10 if incremental |
| Policy Relevance | 20% | Does it matter for real decisions? | +5 if actionable |
| Execution Quality | 20% | Is analysis well-executed? | -5 if missing robustness |
| Appropriate Scope | 10% | Is scope well-calibrated? | -5 if overclaiming |

---

## Judge System Prompt (APE Verbatim)

```
You are a senior editor at a top economics journal (AER, QJE, JPE, 
Econometrica, REStud).

Your role is to compare two research papers and determine which makes 
a stronger contribution to the field.

## Evaluation Process

1. Read both papers carefully
2. Score each on the five criteria below
3. Calculate weighted totals
4. Declare a winner (or tie if very close)
5. Provide a brief justification

## Criteria (Weights)

### 1. Identification Strategy (30%)
- Is the causal identification credible?
- Are assumptions reasonable and clearly stated?
- Is the research design appropriate for the question?

REWARD (+5): Novel identification strategy or instrument
PENALTY (-10): Causal claims without credible identification

### 2. Novelty (20%)
- Does this contribute genuinely new insights?
- Is it differentiated from existing work?
- Does it advance our understanding?

REWARD (+5): Opens new research agenda
PENALTY (-10): Purely incremental

### 3. Policy Relevance (20%)
- Does this matter for real-world policy decisions?
- Are implications clear and actionable?
- Can policymakers use these findings?

REWARD (+5): Clear, actionable policy implications
PENALTY (-5): No practical relevance

### 4. Execution Quality (20%)
- Is the analysis well-executed?
- Are methods appropriate?
- Are robustness checks included?

REWARD (+5): Exceptional methodological rigor
PENALTY (-5): Missing robustness checks

### 5. Appropriate Scope (10%)
- Is the scope well-calibrated?
- Are limitations acknowledged?
- Are claims proportionate to evidence?

PENALTY (-5): Overclaiming or scope creep
```

---

## Judge Output Format

```json
{
  "paper_a": {
    "identification": {
      "base_score": 75,
      "adjustments": ["+5: Novel regression discontinuity design"],
      "final": 80
    },
    "novelty": {
      "base_score": 70,
      "adjustments": [],
      "final": 70
    },
    "policy_relevance": {
      "base_score": 85,
      "adjustments": ["+5: Actionable policy recommendations"],
      "final": 90
    },
    "execution": {
      "base_score": 75,
      "adjustments": ["-5: Limited robustness checks"],
      "final": 70
    },
    "scope": {
      "base_score": 80,
      "adjustments": [],
      "final": 80
    },
    "weighted_total": 78.0
  },
  "paper_b": {
    "identification": {...},
    "novelty": {...},
    "policy_relevance": {...},
    "execution": {...},
    "scope": {...},
    "weighted_total": 76.5
  },
  "winner": "A",
  "margin": 1.5,
  "confidence": "medium",
  "justification": "Paper A demonstrates stronger identification through its novel RD design, though both papers are competitive in overall quality."
}
```

---

## TrueSkill Rating System (APE Implementation)

### Initial Values

```python
# APE default parameters
MU_INITIAL = 25.0          # Initial skill estimate
SIGMA_INITIAL = 25.0 / 3   # Initial uncertainty (~8.333)
BETA = SIGMA_INITIAL / 2   # Performance variation
TAU = SIGMA_INITIAL / 100  # Dynamics factor (skill drift)
```

### Mathematical Formulas

**Conservative Rating (for ranking):**
$$\text{ConservativeRating} = \mu - 3\sigma$$

**Performance Variance:**
$$c^2 = 2\beta^2 + \sigma_{\text{winner}}^2 + \sigma_{\text{loser}}^2$$

**Update After Win:**
$$\mu_{\text{winner}}' = \mu_{\text{winner}} + \frac{\sigma_{\text{winner}}^2}{c} \cdot v$$
$$\sigma_{\text{winner}}' = \sigma_{\text{winner}} \cdot \sqrt{1 - \frac{\sigma_{\text{winner}}^2}{c^2} \cdot w}$$

Where $v$ and $w$ are functions of the rating difference.

### Python Implementation

```python
from dataclasses import dataclass
from typing import Tuple
import math

@dataclass
class TrueSkillRating:
    mu: float = 25.0
    sigma: float = 25.0 / 3
    
    @property
    def conservative(self) -> float:
        """Conservative rating for ranking: μ - 3σ"""
        return self.mu - 3 * self.sigma
    
    def __repr__(self):
        return f"Rating(μ={self.mu:.2f}, σ={self.sigma:.2f}, CR={self.conservative:.2f})"


def update_ratings(
    winner: TrueSkillRating, 
    loser: TrueSkillRating,
    beta: float = 25.0 / 6
) -> Tuple[TrueSkillRating, TrueSkillRating]:
    """
    Update TrueSkill ratings after a match.
    
    Args:
        winner: Rating of winning paper
        loser: Rating of losing paper
        beta: Performance variation parameter
    
    Returns:
        (new_winner_rating, new_loser_rating)
    """
    c = math.sqrt(2 * beta**2 + winner.sigma**2 + loser.sigma**2)
    
    # Rating difference
    delta = (winner.mu - loser.mu) / c
    
    # Truncated Gaussian functions
    v = _v_win(delta)
    w = _w_win(delta)
    
    # Update winner
    winner_new = TrueSkillRating(
        mu=winner.mu + (winner.sigma**2 / c) * v,
        sigma=winner.sigma * math.sqrt(1 - (winner.sigma**2 / c**2) * w)
    )
    
    # Update loser
    loser_new = TrueSkillRating(
        mu=loser.mu - (loser.sigma**2 / c) * v,
        sigma=loser.sigma * math.sqrt(1 - (loser.sigma**2 / c**2) * w)
    )
    
    return winner_new, loser_new


def update_ratings_draw(
    a: TrueSkillRating, 
    b: TrueSkillRating,
    beta: float = 25.0 / 6
) -> Tuple[TrueSkillRating, TrueSkillRating]:
    """
    Update TrueSkill ratings after a draw.
    
    Both ratings move toward the average with reduced uncertainty.
    """
    c = math.sqrt(2 * beta**2 + a.sigma**2 + b.sigma**2)
    
    # For draws, use truncated Gaussian for draw region
    delta = (a.mu - b.mu) / c
    v = _v_draw(delta)
    w = _w_draw(delta)
    
    # Update A
    a_new = TrueSkillRating(
        mu=a.mu + (a.sigma**2 / c) * v,
        sigma=a.sigma * math.sqrt(1 - (a.sigma**2 / c**2) * w)
    )
    
    # Update B (symmetric)
    b_new = TrueSkillRating(
        mu=b.mu - (b.sigma**2 / c) * v,
        sigma=b.sigma * math.sqrt(1 - (b.sigma**2 / c**2) * w)
    )
    
    return a_new, b_new


# Helper functions (truncated Gaussian)
def _v_win(t: float, epsilon: float = 0.0) -> float:
    """Additive correction factor for win."""
    from scipy.stats import norm
    return norm.pdf(t - epsilon) / norm.cdf(t - epsilon)

def _w_win(t: float, epsilon: float = 0.0) -> float:
    """Multiplicative correction factor for win."""
    v = _v_win(t, epsilon)
    return v * (v + t - epsilon)

def _v_draw(t: float, epsilon: float = 0.1) -> float:
    """Additive correction factor for draw."""
    from scipy.stats import norm
    return (norm.pdf(-epsilon - t) - norm.pdf(epsilon - t)) / \
           (norm.cdf(epsilon - t) - norm.cdf(-epsilon - t))

def _w_draw(t: float, epsilon: float = 0.1) -> float:
    """Multiplicative correction factor for draw."""
    v = _v_draw(t, epsilon)
    return v**2 + ((epsilon - t) * norm.pdf(epsilon - t) + 
                   (-epsilon - t) * norm.pdf(-epsilon - t)) / \
                  (norm.cdf(epsilon - t) - norm.cdf(-epsilon - t))
```

### Rating Interpretation

| Conservative Rating | Interpretation |
|---------------------|----------------|
| > 10 | Top tier paper |
| 5 - 10 | Strong paper |
| 0 - 5 | Competitive paper |
| < 0 | Needs improvement |

---

## Using trueskill Package

```python
# Install: pip install trueskill

from trueskill import Rating, rate_1vs1

# Initialize
paper_a = Rating(mu=25.0, sigma=8.333)
paper_b = Rating(mu=25.0, sigma=8.333)

# After Paper A wins
paper_a, paper_b = rate_1vs1(paper_a, paper_b)

# After tie
paper_a, paper_b = rate_1vs1(paper_a, paper_b, drawn=True)

# Get conservative rating
conservative_a = paper_a.mu - 3 * paper_a.sigma
```

---

## Leaderboard Schema

```json
{
  "leaderboard": [
    {
      "rank": 1,
      "paper_id": "apep_0464",
      "title": "Effect of X on Y",
      "mu": 28.5,
      "sigma": 4.2,
      "conservative": 15.9,
      "matches": 12,
      "wins": 9,
      "losses": 2,
      "draws": 1,
      "last_updated": "2026-03-28T10:30:00Z"
    },
    ...
  ],
  "total_papers": 50,
  "total_matches": 245
}
```

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
