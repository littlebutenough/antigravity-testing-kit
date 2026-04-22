---
description: Generate high-quality manual test cases using the 6-step AI-RBT (Risk-Based Testing) process from requirements.
skills:
  - rbt_manual_testing
  - requirements_analyzer
---

> **MANDATORY SKILL:** You MUST load and carefully read the **`rbt_manual_testing`** skill (at `.agent/skills/rbt_manual_testing/SKILL.md`) before starting this task. Use the **FULL RBT Mode** of the skill. Additionally, refer to the **`requirements_analyzer`** skill for UI analysis if needed.

# Workflow: Generate Manual Test Cases using AI-RBT Framework (FULL RBT Mode)

This workflow uses the **FULL RBT Mode** of the `rbt_manual_testing` skill — a sequential 6-step **AI-RBT (AI-Driven Risk-Based Testing)** process to generate manual test cases from requirement documents.

> [!NOTE]
> **This flow is for Antigravity (slash command).** The agent follows the instructions in the skill; there is NO need to read the `prompt.txt` file.
> If the QA team wishes to use a more detailed prompt (ChatGPT/Claude), please copy-paste each step from `plans/manual/01-06/prompt.txt`.

## ⚠️ Execution Principles

- **Mode:** FULL RBT (6 sequential steps)
- **MANDATORY sequential execution** of each step; DO NOT combine steps.
- **MUST stop** and wait for user feedback at Step 2 (Q&A) and Step 4 (Review Scenarios).
- If the user hasn't provided requirements, ask them to do so before starting.
- **All output in English**

## Execution Steps

Follow the detailed instructions in the `rbt_manual_testing` skill → **Mode 2: FULL RBT** section.

### Step 1: Context & Role-play Initialization
1. Ask the user for: project name, system description, MVP goals, requirement documents.
2. Read documents carefully, confirm understanding of the context.
3. **Wait for user confirmation** → proceed to Step 2.

### Step 2: Requirement Analysis (Analysis & Q&A)
1. Identify Happy Path, Alternate Paths, and Exception Paths.
2. Detect Ambiguities (omissions, contradictions, lack of clarity).
3. Pose numbered Q&A questions (Q1, Q2...) to the user/PO/BA, including context + assumptions.
4. **STOP — Wait for user answers** → proceed to Step 3.

### Step 3: System Decomposition
1. Divide features into Modules / Sub-modules.
2. Describe the functionality of each module + their dependencies.

### Step 4: Ensure Coverage (Traceability)
1. Map Module → Requirement ID (REQ-01, REQ-02...).
2. Cross-check for gaps (Gap Analysis), list High-Level Scenarios.
3. **Wait for user scenario review** → proceed to Step 5.

### Step 5: Detailed Test Case Generation (RBT & TC Gen)
1. Assess Risk Level (High/Medium/Low) for each Module.
2. Generate complete test cases: Title, Pre-condition, Steps, Expected, Test Data, Priority.
3. Apply techniques: EP, BVA, Decision Table, State Transition.
4. Test Data must be specific (no generic placeholders).
5. If too many → generate per Module, ask user to continue.

### Step 6: Format Standardization (Template Mapping)
1. Package all test cases into a standard Markdown table:
   `| TC ID | Module | Risk Level | Test Title | Pre-Condition | Test Steps | Expected Result | Priority | Test Data |`
2. Do not omit any test cases.
3. Export as an Artifact if lengthy.

## Output

- Complete Markdown Test Case table, ready for copy-pasting to Excel/Jira/TestRail.
- Traceability Matrix
- List of resolved Ambiguities.
