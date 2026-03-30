---
name: problem-analyzer
description: Deeply analyze empirical puzzles to identify promising theoretical directions. Use before theory generation to understand why standard models fail.
argument-hint: "[puzzle_description] [targets] [baseline_model]"
---

# Problem Analyzer Skill (AutoTheory Paper - Appendix A.1)

## Overview

Before generating theories, deeply analyze the empirical puzzle to understand:
- Why existing models fail
- What assumptions might be wrong
- What mechanisms could work

## System Prompt (Use This Exactly)

```
You are an expert theoretical economist analyzing empirical puzzles.
Your task is to deeply understand why standard models fail to explain
observed data, and identify promising directions for new
theoretical explanations.

THINK REALLY HARD. This is a genuine research problem that has stumped
economists.

Take your time to reason through the mechanics carefully.
Think like a researcher preparing to write a theory paper:
- What are the key facts that need explaining?
- Why EXACTLY do existing models fail? Be mechanistic, not hand-wavy.
- What assumptions might be wrong? Which ones matter most?
- What novel mechanisms could explain the data? Be specific about
  functional forms.

Be specific and analytical. Avoid vague suggestions like "add
frictions" - instead say exactly what kind of friction and how it would affect the equilibrium.
```

## Analysis Prompt Template

```
# Task
Think really hard about this. Deeply analyze the following empirical
puzzle.
Your analysis will guide the development of new theoretical models to
explain the data.
This is a real research problem - your insights matter. Don't rush.

# The Puzzle 
{puzzle_description}

# Empirical Targets 
{targets_description}

# Baseline Model 
{baseline_description}

# Additional Context 
{additional_context}

# Your Analysis
Provide a thorough analysis addressing:

1. **Empirical Facts**: What are the 2-4 key facts that need explaining?

2. **Baseline Failure**: Why exactly does the baseline model fail? Be mechanistic.

3. **Violated Assumptions**: Which assumptions of the baseline are likely wrong?

4. **Promising Directions**: What are 3-5 theoretical directions worth exploring?
   - Be specific (not just "add frictions" but "transaction costs that scale with...")
   - Consider: behavioral biases, market frictions, heterogeneous agents,
     information asymmetry, institutional constraints, etc.

5. **Dead Ends**: What approaches are unlikely to work? Why?

6. **Key Constraints**: What must any successful theory satisfy?
   - Mathematical constraints
   - Economic plausibility requirements
   - Parsimony considerations

7. **Unconventional Idea**: Suggest one surprising or unconventional angle that
   might work but isn't obvious.

Respond in the structured JSON format.
```

## Required JSON Output Format

```json
{
  "puzzle_summary": "Brief description of the puzzle",
  "empirical_facts": [
    "Fact 1: ...",
    "Fact 2: ...",
    "Fact 3: ..."
  ],
  "baseline_failure": {
    "model": "Standard CARA with representative agent",
    "prediction": "M_style/M_idio = N_s * sigma_style^2 / sigma_idio^2 = 14.5",
    "actual": "M_style/M_idio = 100",
    "gap": "Model underpredicts by factor of 7",
    "mechanistic_reason": "The representative agent assumption ignores..."
  },
  "violated_assumptions": [
    {
      "assumption": "Complete diversification",
      "why_wrong": "Institutional investors hold limited positions",
      "importance": "High"
    },
    {
      "assumption": "Homogeneous agents",
      "why_wrong": "Different investor types have different risk exposure",
      "importance": "Medium"
    }
  ],
  "promising_directions": [
    {
      "direction": "Limited attention/breadth constraints",
      "mechanism": "Investors can only process N positions, creating concentration",
      "functional_form": "Add quadratic holding cost λΣθ²",
      "expected_effect": "Amplifies style multiplier relative to idiosyncratic"
    },
    {
      "direction": "Heterogeneous agents",
      "mechanism": "Some agents are fully diversified, others concentrated",
      "functional_form": "Two-type model with fraction α diversified",
      "expected_effect": "Concentrated agents bear more style risk"
    }
  ],
  "dead_ends": [
    {
      "approach": "Higher risk aversion",
      "why_fails": "Scales both multipliers equally, doesn't change ratio"
    }
  ],
  "constraints": {
    "mathematical": ["Equilibrium must exist", "Prices must be finite"],
    "plausibility": ["Risk aversion γ ∈ [1, 10]", "Participation rates realistic"],
    "parsimony": ["Fewer parameters than target moments (N=3)"]
  },
  "unconventional_idea": {
    "idea": "Information quality varies by aggregation level",
    "mechanism": "Style-level signals are noisier, requiring more compensation",
    "why_unconventional": "Usually assume signal quality is exogenous"
  }
}
```

## Example: Price Multiplier Puzzle

### Input

```
puzzle_description: "Price multipliers for style-level order flows are much larger 
than for idiosyncratic flows, by a factor of ~7 relative to standard model predictions."

targets_description: "
- M_style ≈ 0.15 (style-level price multiplier)
- M_idio ≈ 0.02 (idiosyncratic price multiplier)  
- M_style/M_idio ≈ 100 (empirical ratio)
- Baseline prediction: ratio = 14.5
"

baseline_description: "
Standard CARA-Normal model with representative agent:
- Utility: U = -exp(-γW)
- Returns: r ~ N(μ, Σ) where Σ = σ²_style 11' + σ²_idio I
- Equilibrium: μ = γΣq (supply shock q requires premium)
- Implies: M_style/M_idio = N_s σ²_style / σ²_idio = 14.5
"

additional_context: "
Calibration from Li and Lin (2022):
- N = 2278 total stocks
- S = 9 style portfolios  
- N_s = 253 stocks per style
- σ_style = 0.104 (annual)
- σ_idio = 0.435 (annual)
"
```

### Analysis Output

The problem analyzer would produce a detailed JSON analysis identifying:
- The key discrepancy (7x gap between model and data)
- Why homogeneous CARA fails (doesn't differentiate aggregation levels)
- Promising mechanisms (breadth constraints, heterogeneous agents, information frictions)
- Constraints any solution must satisfy

## Pipeline Position

```
Problem Analyzer (YOU ARE HERE)
        ↓
Theory Evolution (Random/Mutate/Crossover)
        ↓
Math Auditor
        ↓
... rest of pipeline
```

The analysis output feeds directly into the `{puzzle_context}` and `{previous_attempts}` fields of the Theory Evolution prompts.

## Usage Tips

1. **Be mechanistic, not vague** - "Add frictions" is useless; "Add quadratic holding cost that penalizes position count" is useful

2. **Think about functional forms** - What mathematical structure would generate the pattern?

3. **Consider parsimony early** - If the puzzle has 3 targets, you need a mechanism with <3 parameters

4. **Identify dead ends** - Save iteration time by ruling out approaches that can't work

5. **Look for unconventional angles** - The obvious approaches may already have been tried
