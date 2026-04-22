---
description: Quickly generate manual test cases from requirements (QUICK mode — bypasses the 6-step process).
skills:
  - rbt_manual_testing
---

# Workflow: Quickly Generate Manual Test Cases from Requirements

> **MANDATORY SKILL:** You MUST load and carefully read the **`rbt_manual_testing`** skill (at `.agent/skills/rbt_manual_testing/SKILL.md`) before starting this task. Use the **QUICK Mode** of the skill.

This workflow uses the **QUICK Mode** of the `rbt_manual_testing` skill to quickly generate test cases from existing requirements.

## ⚠️ Principles

- **Mode:** QUICK (Single pass, no mid-process waiting for the user).
- Suitable for simple modules with clear requirements.
- If requirements are found to be too complex or ambiguous → **automatically switch to FULL RBT** and notify the user.
- **All output in English**

## Execution Steps

1. **Read and understand the requirements** provided by the user.
2. **Identify primary flows:** Happy Path, Negative Path, and Boundary Cases.
3. **Apply automated test case design techniques:**
   - Equivalence Partitioning (EP)
   - Boundary Value Analysis (BVA)
   - Decision Table (if multiple rules exist)
   - State Transition (if workflow exists)
4. **Generate test cases with full fields:**
   - TC ID (format: `[PROJECT]_[MODULE]_TC_[NUMBER]`)
   - Module
   - Test Scenario / Test Case Title
   - Pre-conditions
   - Test Steps (numbered)
   - Expected Results (correspondingly numbered)
   - Test Data (**must be specific**, no placeholders)
   - Priority (Critical / High / Medium / Low)
5. **Export to standard Markdown table.**

## Output Table

```
| TC ID | Module | Test Scenario | Pre-Condition | Test Steps | Test Data | Expected Result | Priority |
```

## Important Rules

- Test Data must be specific: `test_login_01@domain.com`, not "valid email".
- Must include Positive, Negative, and Boundary cases.
- TC ID follows a consistent format as per user convention or default `[PROJECT]_[MODULE]_TC_[NUMBER]`.
- If there are too many TCs → split into Part 1, Part 2, and ask the user.

## When to Switch to FULL RBT

The agent **automatically proposes switching modes** if it detects:
- Ambiguous requirements needing Q&A.
- Large scope (>3 modules).
- Complex business logic with overlapping conditions.
- User requests a Traceability Matrix or Risk Assessment.