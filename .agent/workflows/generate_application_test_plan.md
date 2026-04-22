---
description: Explore web applications, generate test plans and test scenarios. Supports 2 modes — PLAN (test plan only) and FULL (test plan + automation skeleton).
skills:
  - qa_automation_engineer
  - requirements_analyzer
  - ui_debug_agent
---

# Workflow: App Exploration & Test Plan Generation

> **MANDATORY SKILL:** You MUST load and carefully read the **`qa_automation_engineer`** skill (at `.agent/skills/qa_automation_engineer/SKILL.md`) before starting. Additionally, refer to the **`requirements_analyzer`** and **`ui_debug_agent`** skills to support UI analysis.

This workflow helps the agent automatically explore a web application, analyze its structure, identify key modules/user flows, and generate a complete Test Plan.

## ⚠️ Execution Principles

- **All output in English**
- **DO NOT guess** the app structure — inspect the actual DOM via MCP/browser tools.
- **Must wait for user confirmation** of the scope at Step 2 before generating details.
- If the user has not provided a URL → ask before starting.

## 2 Modes

| Mode | When to Use | Output |
|---|---|---|
| **PLAN** (default) | User needs to explore the app, create a test plan, define scenarios | Modules, User Flows, Test Scenarios, Priority |
| **FULL** | User requests an automation skeleton or "full automation suite" | Same as PLAN + Manual Test Cases + Automation Skeleton (POM + Test classes) |

> If the user says "generate full automation suite", "bootstrap automation", or requests code → automatically switch to **FULL Mode**.

## Execution Steps

### Step 1: Reception & App Exploration (Recon)

1. Receive application URL from the user.
2. Use **MCP browser tools** (Playwright MCP) to open the application:
   - `browser_navigate` → URL
   - `browser_resize(1920, 1080)` → desktop viewport
   - `browser_snapshot` → collect DOM structure.
3. Explore **navigation menus**, sidebar, and header to identify main modules.
4. Access each main module sequentially, use `browser_snapshot` to record:
   - Module / page name.
   - Key UI components (forms, tables, buttons, modals).
   - Actions possible (CRUD, search, filter, export...).
5. If the app requires login → ask the user for credentials or use existing fixtures.

### Step 2: Analysis & Scope Confirmation (Analysis — CHECKPOINT)

1. Synthesize exploration results into a list:
   - Detected modules (name, short description, number of features).
   - Main User Flows (Happy Path for each module).
   - Dependencies between modules (if any).
2. Preliminary **Risk Level** assessment for each module:
   - 🔴 **High Risk** — Core module, high user impact, complex logic.
   - 🟡 **Medium Risk** — Supporting module, frequently used.
   - 🟢 **Low Risk** — Rarely used module, minimal changes.
3. **⏸️ STOP — Present for user review:**
   - List of modules + risk levels.
   - Identified user flows.
   - Ask user: "Which modules do you want to focus on? Are there any missing flows?"
4. **Wait for user confirmation** of the scope before proceeding to Step 3.

### Step 3: Generate Test Scenarios & Priority

1. For each module/flow confirmed by the user, generate test scenarios:
   - **Happy Path** — main successful flow.
   - **Negative Path** — invalid input, missing data, validation errors.
   - **Edge Cases** — boundary values, concurrent access, empty states.
2. Assign **Priority** to each scenario based on Risk Level:
   - **P1 (Critical)** — Core flows, regression blockers, sensitive data.
   - **P2 (High)** — Main features, frequently used functionality.
   - **P3 (Medium)** — Secondary features, UI/UX checks.
   - **P4 (Low)** — Nice-to-have, cosmetic checks.

### Step 4: Package Test Plan (Output — PLAN Mode)

1. Create the `test_plan.md` **artifact** with the following structure:
   - **App Overview** — purpose, tech stack (if identified), URL.
   - **Module List** — table including: Module, Description, Risk Level, Number of scenarios.
   - **User Flows** — description of each main flow (steps).
   - **Test Scenarios** — table: `| ID | Module | Scenario | Priority | Category (Happy/Negative/Edge) |`.
   - **Automation Candidates** — mark scenarios suitable for automation and why.
2. If the user chooses **PLAN Mode** → **END** here.

### Step 5: Generate Manual Test Cases (FULL Mode)

> Only perform in **FULL Mode**.

1. Convert test scenarios (Step 3) into **full manual test cases**:
   - TC ID, Module, Test Title, Pre-conditions, Test Steps, Expected Results, Test Data, Priority.
   - Test Data must be specific (no generic placeholders).
2. Export as Markdown tables in the artifact.

### Step 6: Generate Automation Skeleton (FULL Mode)

> Only perform in **FULL Mode**.

1. Identify automation **tech stack** (ask user if unclear):
   - Default: Playwright + TypeScript (or Selenium + Java according to preference).
2. Generate **Page Object classes** for each module:
   - Locators collected from actual DOM (Step 1), DO NOT guess.
   - Clearly named interaction methods.
3. Generate **Test class skeleton** for top-priority scenarios (P1, P2).
4. Ensure compliance with **POM pattern** and **locator strategy** rules.

## Output

### Mode PLAN
- Artifact `test_plan.md` includes: App overview, Modules, User Flows, Test Scenarios (with Priority), Automation Candidates.

### Mode FULL
- All output from PLAN Mode, plus:
- Manual Test Cases (Markdown table).
- Page Object classes.
- Test class skeletons.
- Assertions validating expected behavior.