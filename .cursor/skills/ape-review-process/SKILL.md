# APE 6-Stage Review Process Skill

**来源:** https://ape.socialcatalystlab.org/methodology  
**Purpose:** 论文发表前的完整多阶段审稿流程

---

## Overview

APE 项目使用 6 阶段审稿流程，确保论文质量：

```
┌─────────────────────────────────────────────────────────────────┐
│                    APE Review Pipeline                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Stage 1: Advisor Review ────────────────────────────────────►  │
│           (4 models independently, 3/4 must pass)               │
│                          │                                      │
│                          ▼                                      │
│  Stage 2: Theory Review (Conditional) ───────────────────────►  │
│           (Only for papers with structural models/proofs)       │
│                          │                                      │
│  ─────────────────┬──────┴──────┬─────────────────               │
│                   │             │                                │
│                   ▼             ▼                                │
│  Stage 3: Exhibit Review   Stage 4: Prose Review                │
│           (Figures/Tables)          (Writing Quality)           │
│           [ITERATIVE]               [ITERATIVE]                 │
│                   │             │                                │
│  ─────────────────┴──────┬──────┴─────────────────               │
│                          │                                      │
│                          ▼                                      │
│  Stage 5: Referee Review ────────────────────────────────────►  │
│           (3 parallel reviewers, journal-style)                 │
│                          │                                      │
│                          ▼                                      │
│  Stage 6: Revision ─────────────────────────────────────────►   │
│           (Address ALL feedback before publication)             │
│                          │                                      │
│                          ▼                                      │
│                    PUBLICATION                                  │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Stage 1: Advisor Review

### Purpose
初步质量检查，过滤明显不合格的论文。

### Process
1. 4 个独立模型（不同provider）各自评审
2. 需要 3/4 通过才能进入下一阶段
3. 每个模型执行两阶段检查：
   - **Phase 1: Format Check** (PASS/FAIL)
   - **Phase 2: Content Check** (仅在 Phase 1 通过后执行)

### Format Check Criteria

```python
FORMAT_REQUIREMENTS = {
    "min_pages": 25,           # 不含参考文献/附录
    "min_references": 15,
    "prose_sections": True,    # 主要部分必须是段落，非bullet points
    "min_paragraphs_per_section": 3,
    "figures_rendered": True,  # 非 ASCII art
    "tables_real_numbers": True  # 非 placeholders
}
```

### Decision Matrix

| Phase 1 | Phase 2 | Advisors Passing | Decision |
|---------|---------|------------------|----------|
| FAIL | N/A | 0-2 | REJECT - Fix format issues |
| PASS | FAIL | 0-2 | REJECT - Major content issues |
| PASS | PASS | 3-4 | PROCEED to Stage 2/3/4 |

---

## Stage 2: Theory Review (Conditional)

### Trigger Conditions
仅当论文包含以下内容时执行：
- 结构模型 (structural models)
- 数学证明 (mathematical proofs)
- 校准 (calibration exercises)
- 福利分析 (welfare calculations)

### Review Focus
```
1. Model Consistency
   - Are assumptions internally consistent?
   - Do equilibrium conditions follow from primitives?

2. Mathematical Correctness
   - Are derivations correct?
   - Are proofs complete?

3. Calibration Validity
   - Are parameter values economically sensible?
   - Is the calibration transparent?

4. Welfare Claims
   - Are welfare statements supported by the model?
   - Are distributional assumptions stated?
```

### Output
- PASS: Theory is sound, proceed
- FAIL: Specific issues that must be corrected

---

## Stage 3: Exhibit Review

### Purpose
确保图表达到出版质量。

### Iterative Process
```
while not all_exhibits_approved:
    for exhibit in paper.figures + paper.tables:
        issues = review_exhibit(exhibit)
        if issues:
            provide_specific_feedback(issues)
            author_revises(exhibit)
        else:
            mark_approved(exhibit)
```

### Figure Checklist
- [ ] Axes labeled with units
- [ ] Legend clear and complete
- [ ] Confidence intervals shown (where appropriate)
- [ ] Resolution sufficient for print (≥300 DPI)
- [ ] Color-blind friendly palette
- [ ] Caption complete and self-contained

### Table Checklist
- [ ] Column headers descriptive
- [ ] Standard errors/CI in parentheses or brackets
- [ ] Significance stars consistent (*, **, ***)
- [ ] Sample sizes reported
- [ ] Notes explain all symbols
- [ ] Proper alignment (numbers right-aligned)

---

## Stage 4: Prose Review

### Purpose
确保写作清晰、准确、学术化。

### Iterative Process
```
while writing_issues_exist:
    scan_prose()
    flag_issues()
    provide_revision_suggestions()
    author_revises()
```

### Review Criteria

| Dimension | Check |
|-----------|-------|
| Clarity | Is argument easy to follow? |
| Precision | Are claims appropriately qualified? |
| Flow | Do paragraphs connect logically? |
| Tone | Academic, not journalistic? |
| Length | Sentences/paragraphs appropriate? |

### Common Issues to Flag
- Excessive passive voice
- Jargon without definition
- Vague claims ("substantial effect", "large impact")
- Unnecessary repetition
- Informal language
- Unqualified causal claims

---

## Stage 5: Referee Review

### Purpose
模拟顶级期刊同行评审。

### Process
1. 3 个独立 reviewer（不同模型/prompt变体）
2. 每个 reviewer 提供完整评审报告
3. 评审之间不共享意见

### Referee Report Structure

```markdown
## Summary
[2-3 sentence contribution summary]

## Major Comments

### 1. Identification Strategy
[Assess credibility of causal claims]

### 2. Data Quality
[Assess data sources, measurement, sample selection]

### 3. Robustness
[Are alternative specifications tested?]

### 4. Literature Positioning
[Is related work acknowledged? Contribution clear?]

## Minor Comments
[Specific suggestions]

## Decision
[ACCEPT / CONDITIONAL_ACCEPT / MINOR_REVISION / MAJOR_REVISION / R&R / REJECT]

## Confidence
[1-5 scale]
```

### Aggregation Rule
```python
def aggregate_referee_decisions(decisions: list[str]) -> str:
    """
    Aggregate 3 referee decisions into final recommendation.
    """
    accept_levels = ["ACCEPT", "CONDITIONAL_ACCEPT", "MINOR_REVISION"]
    
    if all(d in accept_levels for d in decisions):
        return "PROCEED_TO_REVISION"
    elif any(d == "REJECT" for d in decisions):
        return "REQUIRES_MAJOR_WORK"
    else:
        return "REVISE_AND_RESUBMIT"
```

---

## Stage 6: Revision

### Purpose
确保所有 referee 反馈都被处理。

### Requirements
1. 创建 Response to Referees 文档
2. 对每个 comment 提供：
   - 修改内容（如果同意）
   - 解释原因（如果不同意）
3. 高亮论文中的所有修改

### Response Template

```markdown
# Response to Referee [N]

## Major Comment 1: [Summary]

**Referee's concern:** [Quote or paraphrase]

**Our response:** [How we addressed it]

**Changes made:** [Specific locations in paper]

---

## Major Comment 2: [Summary]
...
```

### Completion Criteria
- All major comments addressed
- All minor comments addressed or explained
- Changes tracked and documented
- Revised paper passes re-review

---

## Usage in Cursor

### Running the Full Review Pipeline

```bash
# Invoke with paper path
/ape-review paper.pdf

# Or step by step:
/ape-review --stage advisor paper.pdf
/ape-review --stage theory paper.pdf    # if applicable
/ape-review --stage exhibits paper.pdf
/ape-review --stage prose paper.pdf
/ape-review --stage referee paper.pdf
/ape-review --stage revision paper.pdf --responses response.md
```

### Output Files

| Stage | Output |
|-------|--------|
| Advisor | `review_advisor.md` |
| Theory | `review_theory.md` (if applicable) |
| Exhibits | `review_exhibits.md` |
| Prose | `review_prose.md` |
| Referee | `review_referee_1.md`, `review_referee_2.md`, `review_referee_3.md` |
| Final | `review_summary.md` |

---

## Integration with Tournament

论文通过所有 6 个阶段后才能进入 Tournament：

```python
def is_tournament_eligible(paper: Paper) -> bool:
    """Check if paper can enter tournament."""
    return (
        paper.advisor_review.passed and
        (not paper.needs_theory_review or paper.theory_review.passed) and
        paper.exhibit_review.all_approved and
        paper.prose_review.approved and
        paper.referee_review.aggregated_decision in ["PROCEED", "MINOR_REVISION"] and
        paper.revision.all_comments_addressed
    )
```

---

## Key Principles (from APE)

1. **Multi-model Independence**: 不同阶段使用不同模型，避免单一模型盲点
2. **Position Swapping**: 适用于 Tournament 的 judge 评估
3. **No Self-Evaluation**: 生成论文的模型不评审自己的工作
4. **Iterative Improvement**: Exhibit 和 Prose 阶段允许多轮修改
5. **Full Transparency**: 所有评审记录保存

---

*Based on APE methodology (https://ape.socialcatalystlab.org/methodology)*
