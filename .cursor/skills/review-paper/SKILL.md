---
name: review-paper
description: Full manuscript review simulating top journal referee. Use when user asks to "review paper", "critique draft", "prepare for submission", or wants referee feedback.
argument-hint: "[paper file or manuscript]"
---

# Paper Review Skill

## Overview

Comprehensive manuscript review simulating a senior referee at AER/JPE/QJE.

## Review Dimensions

### 1. Contribution Assessment
- What is the main contribution?
- Is it novel relative to existing literature?
- Is it important for economics?

### 2. Identification Strategy
- Is the causal claim credible?
- Are assumptions stated and reasonable?
- What are the threats to identification?
- Are robustness checks sufficient?

### 3. Data and Measurement
- Is data appropriate for the question?
- Are variables well-measured?
- Is sample selection reasonable?
- Are limitations acknowledged?

### 4. Execution Quality
- Are methods correctly applied?
- Are results correctly interpreted?
- Are tables/figures clear?
- Is the writing clear?

### 5. Presentation
- Is the paper well-organized?
- Is length appropriate?
- Are claims proportional to evidence?

## Seven-Audit Protocol (Project APE)

Run seven sequential independent audits:

1. **Abstract Audit** - Does abstract reflect findings?
2. **Introduction Structure** - Does intro follow template?
3. **Section-by-Section** - Data, identification, results each checked
4. **Argumentation** - Logical gaps, alternative explanations
5. **Prose Quality** - Passive voice, hedging, jargon
6. **Citation Fidelity** - Every claim has citation?
7. **"So What" Test** - What's the question? Why matters? What found?

## Output Template

```markdown
# Referee Report: [Paper Title]

## Summary
[2-3 sentence summary of the paper]

## Overall Assessment
**Recommendation:** [Accept / Minor Revision / Major Revision / R&R / Reject]
**Score:** [0-100]

## Major Comments

### 1. [Issue Title]
**Severity:** Critical/Major
**Page/Section:** [reference]
**Issue:** [description]
**Suggestion:** [how to fix]

### 2. [Issue Title]
...

## Minor Comments

### 1. [Issue Title]
**Severity:** Minor
**Page/Section:** [reference]
**Issue:** [description]

## Strengths
1. [Strength 1]
2. [Strength 2]

## Weaknesses
1. [Weakness 1]
2. [Weakness 2]

## Suggestions for Revision
1. [Suggestion 1]
2. [Suggestion 2]

## Novelty Assessment
[Assessment of originality and contribution]

## Detailed Scores

| Dimension | Score | Notes |
|-----------|-------|-------|
| Contribution | /20 | |
| Identification | /30 | |
| Data/Measurement | /15 | |
| Execution | /20 | |
| Presentation | /15 | |
| **Total** | **/100** | |
```

## Quality Checklist

- [ ] All major issues identified
- [ ] Constructive suggestions provided
- [ ] Strengths acknowledged
- [ ] Tone appropriate (critical but fair)
- [ ] Actionable feedback
