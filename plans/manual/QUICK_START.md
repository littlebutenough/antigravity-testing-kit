# 📋 Quick Start Guide: Using AI-RBT 6 Steps

## 🔀 Choose Your Workflow

There are **2 separate workflows**, depending on the AI tool you're using:

### Workflow 1: Antigravity (Slash Command) — Automated

```
Type: /generate_manual_testcases_rbt + paste requirements
→ AI automatically runs 6 steps using the skill, stops at checkpoints waiting for you
→ NO need to copy-paste prompt templates
```

**Pros:** Fast, automated, agent retains context throughout.
**Cons:** Instructions are at a general level (not as detailed as prompt templates).

### Workflow 2: Copy-Paste Prompt — Manual (ChatGPT / Claude / Any AI)

```
Copy Step 1 prompt → paste into chat → AI processes it
→ Copy Step 2 prompt → paste → AI processes it
→ ... repeat through Step 6
```

**Pros:** More detailed prompts with specific examples and deeper suggestions.
**Cons:** Must manually copy-paste 6 times.

---

## Workflow 1: Antigravity — Quick Prompt

```
/generate_manual_testcases_rbt

Project: [Project Name]
Feature: [Feature Name]
Goal: [Short Description]

[Paste requirements/user stories here]
```

When AI stops at a checkpoint, simply answer the question or type:
```
Continue to Step [X]
```

---

## Workflow 2: Copy-Paste — Step-by-Step Guide

| Step | Name | Prompt file | Wait for User? |
|------|------|-------------|-----------|
| **1** | Context & Role-play | Copy `plans/manual/01_context_and_roleplay/prompt.txt` + fill in `[...]` | ✅ Wait for confirmation |
| **2** | Analysis & QnA | Copy `plans/manual/02_analysis_and_qna/prompt.txt` | ✅ **Wait for Q&A answers** |
| **3** | Decomposition | Copy `plans/manual/03_decomposition/prompt.txt` | Quick review |
| **4** | Traceability | Copy `plans/manual/04_traceability/prompt.txt` | ✅ **Wait for scenario review** |
| **5** | RBT & TC Generation | Copy `plans/manual/05_rbt_and_tc_generation/prompt.txt` | Review results |
| **6** | Template Mapping | Copy `plans/manual/06_template_mapping/prompt.txt` | Copy table → Excel |

### Flow Diagram:

```
[Step 1] Copy prompt + paste requirements document
    ↓  AI confirms understanding → User confirms OK
[Step 2] Copy analysis prompt
    ↓  AI asks questions → ⏸️ User answers each question
[Step 3] Copy decomposition prompt
    ↓  AI generates Module list → User quick review
[Step 4] Copy traceability prompt
    ↓  AI generates scenarios → ⏸️ User reviews + adds more
[Step 5] Copy TC generation prompt
    ↓  AI generates detailed test cases → User reviews
[Step 6] Copy standardization prompt
    ↓  AI generates Markdown table → Copy to Excel/Jira ✅
```

---

## Optimization Tips

1. **Step 2 is the most important** — Don't rush, answer each question the AI asks thoroughly
2. **Split modules when there are many** — In Step 5, if there are >5 modules, ask AI to generate one module at a time
3. **Review before formatting** — In Step 5, review test cases before moving to Step 6
4. **Use the same conversation** — Run all 6 steps in **the same conversation** so AI retains context
5. **Copy-Paste workflow is more detailed** — If you need the highest quality, use Workflow 2 (even when using Antigravity)