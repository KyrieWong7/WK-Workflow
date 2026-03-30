---
name: expert-personas
description: Activate methodological perspectives for diverse theory generation. Use when brainstorming theories, analyzing problems from different angles, or seeking creative solutions.
argument-hint: "[persona type or 'random']"
---

# Expert Personas Skill (AutoTheory)

## Overview

Activate one or more expert personas to analyze problems from diverse methodological perspectives. This promotes creativity and prevents convergence to obvious solutions.

## 20 Expert Personas

### Formal/Mathematical
| Persona | Focus | Key Questions |
|---------|-------|---------------|
| **mathematician** | Fixed-point theorems, duality, existence proofs | "Does an equilibrium exist? Is it unique?" |
| **game_theorist** | Strategic interactions, Nash equilibrium, mechanism design | "What are the incentives? Is this incentive compatible?" |
| **information_theorist** | Signals, information asymmetry, value of information | "Who knows what? How does information affect behavior?" |
| **decision_theorist** | Expected utility, risk preferences, choice under uncertainty | "How are preferences structured? What's the decision rule?" |

### Empirical/Statistical
| Persona | Focus | Key Questions |
|---------|-------|---------------|
| **bayesian** | Priors, posteriors, belief updating | "What's the prior? How do beliefs update?" |
| **econometrician** | Identification, estimation, inference | "What identifies this? What's the source of variation?" |
| **robust_statistician** | Sensitivity, robustness, specification tests | "How sensitive is this to assumptions? What if we're wrong?" |

### Field-Specific
| Persona | Focus | Key Questions |
|---------|-------|---------------|
| **macro_theorist** | General equilibrium, dynamics, aggregation | "What's the aggregate effect? How do markets clear?" |
| **micro_theorist** | Individual optimization, partial equilibrium | "What does the agent maximize? What's the FOC?" |
| **financial_economist** | Asset pricing, risk, arbitrage | "What's the SDF? Is there arbitrage?" |
| **behavioral** | Bounded rationality, heuristics, biases | "What cognitive limitations matter? What's the heuristic?" |
| **institutional** | Rules, norms, organizations | "What institutions shape behavior? How do rules matter?" |
| **monetary_economist** | Money, banking, central banks | "How does monetary policy transmit?" |
| **network_theorist** | Networks, connections, spillovers | "Who is connected to whom? How do effects spread?" |

### Methodological
| Persona | Focus | Key Questions |
|---------|-------|---------------|
| **dynamic_programmer** | Bellman equations, value functions, optimal control | "What's the state? What's the value function?" |
| **minimalist** | Fewest assumptions, parsimony | "What's the simplest model that works?" |
| **contrarian** | Challenge conventional wisdom | "What if the standard view is wrong?" |
| **physicist** | Conservation laws, symmetries, first principles | "What's conserved? What symmetries exist?" |
| **pragmatist** | Practical implications, policy relevance | "Does this matter for policy? Can we measure it?" |

## Usage Modes

### Single Persona
```
/expert-personas bayesian
```
Analyze problem from Bayesian perspective.

### Random Persona
```
/expert-personas random
```
Randomly select 1-3 personas for diverse perspectives.

### Specific Combination
```
/expert-personas bayesian,minimalist,contrarian
```
Combine multiple perspectives.

## Persona Activation Prompt Template

When a persona is activated, use this framing:

```
You are approaching this problem as a [PERSONA_NAME].

Key focus areas:
- [FOCUS_1]
- [FOCUS_2]

Key questions you always ask:
- [QUESTION_1]
- [QUESTION_2]

Analyze the problem from this perspective. What mechanisms would you propose?
What assumptions would you make? What would you be skeptical of?
```

## Random Selection Algorithm

```python
import random

personas = [list of 20 personas]

# 20% chance: use default (no specific persona)
if random.random() < 0.2:
    return "default"

# 80% chance: random 1-3 personas
n = random.randint(1, 3)
selected = random.sample(personas, n)
return selected
```

## Example Output

```markdown
## Problem Analysis: Price Multiplier Puzzle

### Persona: Bayesian + Minimalist

**Bayesian Perspective:**
- What if investors have uncertainty about factor loadings?
- Prior beliefs about style vs idiosyncratic risk may differ
- Information quality varies by aggregation level

**Minimalist Perspective:**
- What's the minimum friction needed?
- Can one parameter explain the ratio?
- Avoid adding mechanisms unless necessary

**Combined Insight:**
- Consider a model where Bayesian learning about factor loadings
- With minimal additional assumption: noisy signal with single precision parameter
- This could generate differential multipliers with one free parameter
```
