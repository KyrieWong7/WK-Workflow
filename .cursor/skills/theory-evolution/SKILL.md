---
name: theory-evolution
description: Generate economic theories using evolutionary strategies. Use when developing new theoretical explanations, improving existing models, or combining mechanisms.
argument-hint: "[strategy: random|mutate|crossover] [optional: parent model(s)]"
---

# Theory Evolution Skill (AutoTheory)

## Overview

Generate candidate economic theories using three evolutionary strategies inspired by genetic algorithms.

## Three Strategies

### 1. Random (35% of runs)
Generate a new theory from scratch.

**When to use:**
- Starting exploration of a puzzle
- Seeking diverse mechanisms
- No strong prior on approach

**Process:**
1. Activate random expert persona(s)
2. Analyze the puzzle from that perspective
3. Propose a novel mechanism
4. Derive equilibrium conditions
5. Produce testable predictions

### 2. Mutate (35% of runs)
Modify an existing successful theory.

**When to use:**
- Have a promising model that needs refinement
- Referee feedback identifies weaknesses
- Want to simplify or extend

**Process:**
1. Read parent theory and its referee report
2. Identify specific weakness to address
3. Propose targeted modification
4. Re-derive affected equations
5. Verify improvement

**Mutation Types:**
- Simplify: Remove unnecessary parameters
- Extend: Add new mechanism
- Reparameterize: Change parameter mapping
- Generalize: Relax assumptions

### 3. Crossover (30% of runs)
Combine elements from two successful theories.

**When to use:**
- Two theories have complementary strengths
- Want to test mechanism combinations
- Seeking more parsimonious hybrid

**Process:**
1. Read both parent theories
2. Identify compatible mechanisms
3. Design hybrid that combines best elements
4. Aim for fewer parameters than sum of parents
5. Derive new equilibrium

## Theory Proposal Template

```markdown
# Theory Proposal: [Name]

## Strategy
[Random / Mutate / Crossover]
Parents: [if applicable]

## The Puzzle
[What empirical fact needs explaining]

## Key Mechanism
[1-2 sentence summary of the core insight]

## Model Setup

### Agents
[Who are the decision makers]

### Preferences
[Utility function / objective]

### Constraints
[Budget, information, institutional]

### Market Structure
[How do agents interact]

## Equilibrium Derivation

### Step 1: [Name]
[Derivation]

### Step 2: [Name]
[Derivation]

...

## Main Results

### Proposition 1
[Statement]

### Proof
[Derivation]

## Calibration Targets
| Target | Value | Source |
|--------|-------|--------|
| [name] | [value] | [citation] |

## Parameters
| Parameter | Interpretation | Range |
|-----------|----------------|-------|
| [symbol] | [meaning] | [bounds] |

## Testable Predictions
1. [Prediction 1]
2. [Prediction 2]

## Comparison to Existing Models
[How does this differ from standard approaches]
```

## Quality Criteria

A good theory proposal has:

1. **Clear mechanism** - The economic intuition is transparent
2. **Mathematical rigor** - Derivations are correct
3. **Parsimony** - Fewest parameters possible
4. **Empirical fit** - Matches calibration targets
5. **Testable predictions** - Falsifiable implications

## Parent Selection

When selecting parents for mutate/crossover:

```python
# Select from models with referee score >= 35
eligible = [m for m in models if m.referee_score >= 35]

# Probability proportional to score
weights = [m.referee_score - 35 for m in eligible]
weights = [w / sum(weights) for w in weights]

parent = random.choices(eligible, weights)[0]
```

## Example: Mutate Strategy

```markdown
## Parent Model
Bayesian-Breadth-Cost CARA (Score: 84)

## Referee Feedback
"The holding cost λ is ad hoc. Can it be microfounded?"

## Mutation
Replace quadratic holding cost with explicit portfolio attention cost.

## Modified Model
- Replace: λ∑θ²ᵢ with attention cost c(n) where n = |{i: θᵢ ≠ 0}|
- Derive new FOC under discrete attention constraint
- Show this generates similar multiplier structure
- Benefit: Microfounded attention friction
```
