---
name: RBT Manual Testing
description: Skill for generating manual test cases with 2 modes — QUICK (fast generation from requirements) and FULL RBT (6-step AI-RBT process with risk assessment). Master skill for all manual test case tasks.
---

# RBT Manual Testing

## Description

This is the **Master Skill** for all manual test case generation tasks. The skill provides **2 operating modes** to suit all requirement scales:

| Mode | When to use | Execution Time |
|------|-------------|----------------|
| **QUICK** | Simple modules, fast TC needed, clear requirements | 1 turn (no waiting for user) |
| **FULL RBT** | Complex modules, risk analysis needed, large systems | 6 sequential steps (with checkpoints) |

**Core Principles:**
- **Human Strategy:** Humans define strategy, risk levels, and standards.
- **AI Execution:** AI performs analysis, writes TCs, and reviews vulnerabilities.
- **Human Verification:** Humans re-verify results before finalizing.

---

## When to Use

Use this skill when:

- Generating manual test cases from requirements / user stories.
- Analyzing requirements to detect ambiguities.
- Decomposing systems into modules / features.
- Building traceability matrices.
- Applying Risk-Based Testing (risk assessment for test cases).
- Standardizing test cases into Markdown tables (Jira/Excel format).
- Fast generation of test cases from simple requirements.

**DO NOT** use this skill when:

- Automation code generation is needed → use `qa_automation_engineer`.
- DOM inspection / locator generation is needed → use `ui_debug_agent` / `smart_locator_agent`.
- Only test data generation is needed → use `test_data_generator`.

---

## Mode Routing — How to Choose a Mode

Agent automatically selects the mode based on **trigger keywords** and **context**:

### → Mode QUICK

Triggers when:
- User uses the `/generate_testcases_from_requirements` workflow.
- User says: "generate fast test cases", "create TC from this requirement", "write test cases for form..."
- Requirements are already clear, small scope (1 module / 1 feature).
- User does not request risk analysis or a formal process.

### → Mode FULL RBT

Triggers when:
- User uses the `/generate_manual_testcases_rbt` workflow.
- User says: "6-step process", "RBT analysis", "generate full test cases", "generate a formal TC suite".
- Large scope (multiple modules, complex system).
- User requests a Traceability Matrix or Risk Level assessment.
- Requirements are unclear, Ambiguity analysis is needed.

### → When Uncertain

If the mode cannot be determined, the agent **asks the user**:
```
Which mode would you like to use for test case generation?
1. QUICK — Fast generation from requirements (skipping analysis steps).
2. FULL RBT — Complete 6-step process (analysis → decomposition → RBT → TC generation).
```

---

# Mode 1: QUICK — Fast Test Case Generation

## Purpose

Generate **fast, high-quality** test cases from clear requirements/user stories, suitable for simple modules or when immediate results are needed.

## Process (Single turn)

**Agent must:**

1. **Read and understand the provided requirements**.
2. **Identify core flows:**
   - Happy Path (main flow)
   - Negative Path (incorrect/missing data)
   - Boundary Cases (edge values)
3. **Apply automated test design techniques:**
   - **Equivalence Partitioning (EP):** Group inputs into equivalent sets.
   - **Boundary Value Analysis (BVA):** Test values at boundaries.
   - **Decision Table:** List condition combinations (if multiple rules exist).
   - **State Transition:** Test state changes (if a workflow exists).
4. **Generate test cases** with complete fields:
   - TC ID (format: `[PROJECT]_[MODULE]_TC_[NUMBER]`)
   - Module
   - Test Case Title / Test Scenario
   - Pre-conditions
   - Test Steps (numbered)
   - Expected Results (correspondingly numbered)
   - Test Data (**must be specific**, no placeholders)
   - Priority (Critical / High / Medium / Low)
5. **Output a standard Markdown table**, ready for copy-pasting to Excel/Jira.

## Output Table

```
| TC ID | Module | Test Scenario | Pre-Condition | Test Steps | Test Data | Expected Result | Priority |
```

## Test Data Rules (applies to both modes)

```
❌ Wrong: "Enter a valid code"
✅ Correct: "Enter code: KH-2026-0012"

❌ Wrong: "Enter a valid email"
✅ Correct: "Enter email: test_customer_01@domain.com"

❌ Wrong: "Enter value exceeding limit"
✅ Correct: "Enter 256 characters into the Name field (max: 255)"
```

## Anti-Patterns (QUICK Mode)

- ❌ Generating generic/placeholder test data.
- ❌ Only including Happy Path, missing Negative/Boundary cases.
- ❌ Ignoring validation rules in requirements.
- ❌ Vague Test Steps ("enter data" → must specify what to enter and where).

---

# Mode 2: FULL RBT — 6-Step AI-RBT Process

## Purpose

A formal, sequential process for complex modules. Includes Ambiguity analysis, system decomposition, Traceability Matrix, Risk Level assessment, and detailed test case generation.

> ⚠️ **IMPORTANT:** This process **MUST run sequentially** step-by-step. DO NOT combine multiple steps into one run. Each step must be completed and confirmed by the user before moving to the next.

> [!NOTE]
> **2 separate usage flows:**
> - **Antigravity Flow (slash command):** Agent follows the general instructions below. The agent does NOT need to read the prompt.txt file.
> - **Copy-Paste Flow (ChatGPT/Claude):** QA team copies detailed prompt content from `plans/manual/01-06/prompt.txt` into the AI chat, step-by-step.

### Step 1: Context & Role-play (Initialize Context)

**Purpose:** Set the Senior QA Engineer role and load project context.

**Agent must:**
1. Ask the user to provide:
   - Project / feature name.
   - Current system description.
   - MVP testing objectives.
   - Requirement documents (Requirements, User Stories, Figma link, PDF...).
2. Carefully read the documents and confirm understanding of the context.
3. Summarize the testing scope.
4. **Wait for user confirmation** before moving to Step 2.

**Output:** Confirmation of context understanding + summary of testing scope.

---

### Step 2: Analysis & QnA (Requirement Analysis)

**Purpose:** Analyze documentation to detect ambiguities, omissions, and contradictions.

**Agent must:**
1. Identify flows:
   - Happy Path (main flow).
   - Alternate Paths (branching flows).
   - Exception Paths (exception flows).
2. Detect Ambiguities:
   - Omissions (textbox length not specified, timeout, connection loss behavior, etc.).
   - Contradictions.
   - Unclear requirements.
3. Post numbered Q&A questions (Q1, Q2...) for user/PO/BA clarification, with each question including context and an assumption if left unanswered.
4. **STOP — Wait for user answers** to the questions before proceeding.

**Output:** List of flows + Ambiguities + Q&A questions.

> [!IMPORTANT]
> **This is the most critical bottleneck.** If the agent skips this step and guesses logic, test cases will be seriously flawed. The agent MUST stop and wait for user feedback.

---

### Step 3: Decomposition (System Decomposition)

**Purpose:** Divide complex features into smaller, manageable Modules / Sub-modules.

**Agent must:**
1. Decompose using one of two ways:
   - **By UI:** Header, Data Table, Form popup, Sidebar...
   - **By flow:** Create flow, Edit flow, Delete flow...
2. Briefly describe the function of each Module.
3. Indicate Dependencies between Modules.

**Output:** List of Modules/Sub-modules + Dependencies.

---

### Step 4: Traceability (Ensuring Coverage)

**Purpose:** Establish a traceability matrix to ensure 100% of requirements are covered by test scenarios.

**Agent must:**
1. Map each Module/Rule to a Requirement code (REQ-01, REQ-02...).
2. Cross-check for missing requirements in the decomposition list (Gap Analysis).
3. List High-Level Test Scenarios for each Module, focusing on:
   - Security / Authorization.
   - UI Validation.
   - Business Logic.
   - Data Integrity.
   - Error Handling.
4. **Wait for user review** of the scenarios list before generating detailed test cases.

**Output:** Traceability Matrix + High-Level Test Scenarios.

> [!WARNING]
> **Human Checkpoint:** User needs to review the scenarios list to add specific cases that AI might miss. This is a risk assessment step performed by humans.

---

### Step 5: RBT & TC Generation (Detailed Test Case Generation)

**Purpose:** Generate detailed test cases based on a Risk-Based Testing strategy.

**Agent must:**
1. Assess Risk Level for each Module:
   - **High Risk:** Thorough testing, many cases (critical business logic, involving money, security).
   - **Medium Risk:** Moderate testing.
   - **Low Risk:** Basic testing, Happy Path.
2. Generate test cases with complete fields:
   - Module / Sub-module.
   - Test Case Title.
   - Pre-conditions
   - Test Steps (numbered).
   - Expected Results (correspondingly numbered).
   - Test Data (**must be specific**, no generic placeholders).
   - Priority.
3. Cover diverse scenarios:
   - Happy Path.
   - Negative Path (boundary values, exceeding character limits).
   - Edge Cases (timeout, connection loss, etc.).
4. Apply appropriate **test case design techniques**:
   - **Equivalence Partitioning:** Divide inputs into equivalent groups, test a representative of each.
   - **Boundary Value Analysis (BVA):** Test values at boundaries (min, min+1, max-1, max).
   - **Decision Table:** List combinations of conditions → results (for multi-condition logic).
   - **State Transition:** Test valid + invalid state changes (for workflows).
5. If there are too many scenarios → generate Module by Module, asking the user to continue.

**Output:** List of detailed Test Cases with Risk Levels.

---

### Step 6: Template Mapping (Standardizing Format)

**Purpose:** Package test cases into a standard Markdown table, ready for copy-pasting to Excel/Jira.

**Agent must:**
1. Standardize all test cases into a Markdown table:

```
| TC ID | Module | Risk Level | Test Title | Pre-Condition | Test Steps | Expected Result | Priority | Test Data |
```

2. Table rules:
   - TC ID in a consistent format (e.g., `CRM_CUST_TC_001`).
   - Test Steps and Expected Result numbered, using `<br>` for new lines within cells.
   - **ABSOLUTELY DO NOT omit** any test case generated in Step 5.
   - If too long → split into Part 1, Part 2... and ask the user to continue.
3. Export output as an Artifact (`test_cases_<module>.md`).

**Output:** Complete Markdown Test Case Table.

---

## Anti-Patterns (FORBIDDEN — applies to both modes)

- ❌ Combining multiple steps into one run in FULL RBT (MUST be sequential).
- ❌ Guessing business logic without asking the user (Step 2 - FULL RBT).
- ❌ Skipping the Ambiguity analysis step (FULL RBT).
- ❌ Generating generic/placeholder test data.
- ❌ Shortening or omitting test cases when mapping to the table.
- ❌ Generating all test cases at once for large systems (must divide by module).
- ❌ Only including Happy Path, missing Negative/Boundary cases (QUICK).
- ❌ Vague Test Steps, not specifying input data.

---

## Prompt Templates

Sample prompt templates for the FULL RBT process are located at:

```
plans/manual/
├── 01_context_and_roleplay/prompt.txt
├── 02_analysis_and_qna/prompt.txt
├── 03_decomposition/prompt.txt
├── 04_traceability/prompt.txt
├── 05_rbt_and_tc_generation/prompt.txt
└── 06_template_mapping/prompt.txt
```

The agent needs to read the corresponding prompt template **before** performing each step (FULL RBT mode).

QUICK mode does not require reading prompt templates — the agent directly applies EP/BVA/Decision Table techniques.

---

## Output Format

### Mode QUICK

| Output | Description |
|--------|-------------|
| Markdown TC Table | Complete Test Cases, ready to copy to Excel/Jira |

### Mode FULL RBT

| Step | Output |
|------|--------|
| 1 | Context confirmation |
| 2 | Flows + Ambiguities + Q&A Questions |
| 3 | Module Decomposition + Dependencies |
| 4 | Traceability Matrix + High-Level Scenarios |
| 5 | Detailed Test Cases (Risk Level + Test Data) |
| 6 | Standard Markdown Table (Jira/Excel ready) |

All output must be in **English**, **Markdown** format, using **Artifact** if the content is long.
