---
description: Analyze unstable automation tests (flaky tests), identify root causes, and automatically fix them. Supports 2 modes — ANALYZE (report only) and FIX (report + automatic code fix).
skills:
  - flaky_test_analyzer
  - smart_locator_agent
  - locator_healer_agent
---

# Workflow: Analyze & Fix Flaky Tests

> **MANDATORY SKILL:** You MUST load and carefully read the **`flaky_test_analyzer`** skill (at `.agent/skills/flaky_test_analyzer/SKILL.md`) before starting. Additionally, refer to the **`smart_locator_agent`** and **`locator_healer_agent`** skills when needed to fix/replace locators.

This workflow helps the agent automatically analyze unstable automation tests (intermittent pass/fail), accurately identify the root cause, and (depending on the mode) automatically fix the code to stabilize the test.

## ⚠️ Execution Principles

- **All output in English**
- **DO NOT guess** the cause — analyze logs, code, and actual DOM.
- **Must wait for user confirmation** of the fix list at Step 3 before modifying code (FIX Mode).
- If the user has not provided a test file/error log → ask before starting.
- ⚠️ Once the user confirms the fix → the agent automatically fixes + verifies, DO NOT ask again during the fix process.

## 2 Modes

| Mode | When to Use | Output |
|---|---|---|
| **ANALYZE** (default) | User needs to understand flaky causes, doesn't want to fix code yet | Analysis report + Proposed fix |
| **FIX** | User wants the agent to fix the code immediately | Same as ANALYZE + Fixed code + Verification results |

> If the user says "fix it now", "please fix", "remedy this test", or requests the agent to fix code → automatically switch to **FIX Mode**.

## Inputs to Collect

The agent needs at least **one of the following inputs** from the user:

| Input | How to Get | Priority |
|---|---|---|
| **Test file path** | User provides or agent finds in the project | ⭐ Mandatory |
| **Error log / stack trace** | User pastes or agent runs test to collect | ⭐ Mandatory |
| **CI/CD log** | User provides URL or log file | Optional |
| **Test report** (HTML/JSON) | Playwright report, Allure, TestNG report | Optional |
| **Failure history** | Number of failures / total runs | Optional |

## Execution Steps

### Step 1: Information Gathering & Error Reproduction (Detect & Reproduce)

1. **Read test file** using `view_file`:
   - Identify the framework (Playwright / Selenium / Appium / Pytest / TestNG).
   - Note the structure: Page Objects, fixtures, helper functions.
   - Flag suspicious code areas (waits, locators, assertions, setup/teardown).

2. **Run test** to reproduce the error (if no error log exists):
   - Run the test **3 consecutive times** using `run_command`:
     ```bash
     # Playwright
     npx playwright test <test_file> --retries=0 --reporter=list

     # Pytest
     python -m pytest <test_file> -v --count=3

     # Maven/TestNG
     mvn test -Dtest=<TestClass> -Dsurefire.rerunFailingTestsCount=0
     ```
   - Note patterns: Which runs failed? At which step? Are error messages the same or different?

3. **Collect evidence:**
   - Error log / stack trace from each run.
   - Screenshots (if the test captures on failure).
   - Console log / Network log (if related to API calls).

### Step 2: Root Cause Analysis (Inspect & Classify)

1. **Read error log** and classify according to the root cause table:

| Category | Identification Signs | Error Example |
|---|---|---|
| 🎯 **Unstable Locator** | `ElementNotFound`, `No matching element`, selector contains dynamic class/index | `Locator('.css-1a2b3c')` fails because class changes every build |
| ⏱️ **Timing / Race condition** | `Timeout`, `Element not visible`, test passes when running slowly (debug mode) | `expect(element).toBeVisible()` timeout because animation isn't finished |
| 📊 **Test data conflict** | `Duplicate key`, `Already exists`, fails when running in parallel | 2 tests both creating user `test@email.com` |
| 🔄 **State dependency** | Passes when running independently, fails when running in suite | Test B depends on data created by Test A |
| 🌐 **Environment / Network** | `ECONNREFUSED`, `503`, `CORS error`, fails on CI but passes locally | API server slow, CDN timeout |
| 🖼️ **UI Animation / Transition** | `Element is not clickable`, `intercept`, fails randomly when clicking | Modal animation not finished, overlay obscuring button |
| 📱 **Viewport / Responsive** | Element hidden, scroll doesn't reach, fails at different resolution | Button outside viewport on CI (headless 800x600) |
| 🧹 **Cleanup / Teardown** | Fails on 2nd+ run, passes first time | Test doesn't cleanup data → subsequent conflict |

2. **Inspect code** in detail, checking for each anti-pattern:

   **Locator check:**
   - [ ] Using dynamic classes (`css-xxx`, `sc-xxx`, `MuiXxx-root`)? → ❌
   - [ ] Using positional xpath (`//div[3]/button[2]`)? → ❌
   - [ ] Is the locator unique? (check on actual DOM if needed)

   **Wait strategy check:**
   - [ ] Using `waitForTimeout()` / `Thread.sleep()` / `time.sleep()`? → ❌
   - [ ] Using `waitForSelector()` when `expect()` is sufficient? → ⚠️
   - [ ] Does the assertion have an appropriate timeout?

   **Test data check:**
   - [ ] Is data hardcoded? (`test@email.com`, `user123`)
   - [ ] Is data unique per run? (timestamp / random)
   - [ ] Is data cleaned up after the test?

   **Test independence check:**
   - [ ] Does the test depend on execution order?
   - [ ] Does the test share state via global variables?
   - [ ] Are Setup/Teardown complete?

3. **If actual DOM inspection is needed** (locator issue):
   - Open browser via MCP: `browser_navigate` → `browser_resize(1920, 1080)` → `browser_snapshot`
   - Compare locator in code vs. actual DOM.
   - Identify a more stable replacement locator (using the `smart_locator_agent` skill).

### Step 3: Reporting & Fix Proposal (Report — CHECKPOINT)

1. **Create artifact** `flaky_analysis.md` with the following structure:

   ```markdown
   # Flaky Test Analysis Report

   ## Overview
   - **Test file:** <path>
   - **Framework:** Playwright / Selenium / ...
   - **Failure frequency:** X/Y runs
   - **Severity:** 🔴 Critical / 🟡 Medium / 🟢 Low

   ## Root Cause Analysis

   | # | Vị trí (Line) | Category | Problem description | Severity |
   |---|---|---|---|---|
   | 1 | test.ts:45 | ⏱️ Timing | waitForTimeout(3000) instead of smart wait | 🔴 |
   | 2 | page.ts:12 | 🎯 Locator | .css-1abc dynamic class | 🟡 |

   ## Fix Proposal

   | # | Issue | Current Code | Proposed Code | Reason |
   |---|---|---|---|---|
   | 1 | Hard sleep | `waitForTimeout(3000)` | `expect(el).toBeVisible()` | Smart wait with auto-retry |
   | 2 | Dynamic class | `.css-1abc` | `getByRole('button', {name: 'Submit'})` | Semantic, no CSS dependency |
   ```

2. **⏸️ STOP — Present for user review:**
   - List of identified root causes.
   - Fix proposal table (old code → new code).
   - Ask: "Do you agree with this analysis? Should I proceed with fixing the code or just provide the report?"

3. If user chooses **ANALYZE Mode** → **END** here.
4. If user agrees to fix → proceed to Step 4.

### Step 4: Automatic Code Fixing (Auto-Fix — FIX Mode)

> Only perform in **FIX Mode** once the user has confirmed.

1. **Modify code** in priority order (fix most critical first):

   **Fix Locator:**
   - Use the `locator_healer_agent` skill to replace broken locators.
   - Comply with locator priority in `.agent/rules/locator_strategy.md`.
   - Verify new locators on actual DOM before committing to code.

   **Fix Timing:**
   ```
   ❌ page.waitForTimeout(3000)
   ✅ await expect(page.getByRole('button')).toBeVisible()

   ❌ Thread.sleep(5000)
   ✅ wait.until(ExpectedConditions.visibilityOfElementLocated(...))
   ```

   **Fix Test Data:**
   ```
   ❌ const email = "test@email.com"
   ✅ const email = `auto_${Date.now()}@test.com`
   ```

   **Fix Test Independence:**
   - Add setup/teardown to create and cleanup separate data.
   - Remove dependencies between test cases.
   - Ensure each test creates its own preconditions.

2. **Use** `replace_file_content` or `multi_replace_file_content` to apply changes.
3. **Note** each change in the `flaky_analysis.md` artifact.

### Step 5: Verify & Ensure Stability (Verify Stability)

> Only perform in **FIX Mode**

1. **Run the test 3 consecutive times** after the fix:
   ```bash
   # Playwright
   npx playwright test <test_file> --retries=0 --repeat-each=3

   # Pytest
   python -m pytest <test_file> -v --count=3

   # Maven
   mvn test -Dtest=<TestClass> (run 3 times manually)
   ```

2. **Evaluate Results:**

   | Result | Action |
   |---|---|
   | ✅ **3/3 PASS** | Test stabilized → update artifact, report success. |
   | ⚠️ **2/3 PASS** | Still flaky → return to Step 2 to analyze failures, fix again (max 3 cycles). |
   | ❌ **0-1/3 PASS** | Fix moved in wrong direction → rollback, re-analyze root cause. |

3. **Stability Checklist** (must meet all criteria before ending):
   - [ ] Locator is unique and stable across multiple reloads.
   - [ ] No hard sleep / fixed delays.
   - [ ] Test data is unique + traceable each run.
   - [ ] Test is independent — no dependency on execution order.
   - [ ] Test passes 3+ consecutive times.
   - [ ] No remaining debug logs / commented code.

4. **Update artifact** `flaky_analysis.md`:
   - Add "Results after Fix" section with run results table.
   - Mark status: ✅ STABILIZED / ⚠️ PARTIALLY FIXED / ❌ STILL FLAKY.

## Special Situation Handling

| Situation | How to Handle |
|---|---|
| **Test only fails on CI** | Compare CI vs. local env (viewport, timezone, headless, resources). Check if 1920x1080 viewport is set on CI. |
| **Test fails during parallel execution** | Check test data isolation, shared state, database locks. Propose unique data per worker. |
| **Test fails after new deployment** | Check DOM changes, API response changes. Use `locator_healer_agent` to update locators. |
| **Test fails randomly with no pattern** | Collect more data (10+ runs), check for memory leaks, resource exhaustion. |
| **Multiple tests are flaky** | Find common factors (shared fixture, shared page object, shared config). |
| **Test fails due to external API** | Propose mocking/stubbing external dependencies, retry logic for API calls. |

## Output

### ANALYZE Mode
- Artifact `flaky_analysis.md`:
  - Test overview (file, framework, failure frequency).
  - Root Cause Analysis (classification table).
  - Detailed fix proposal (old code → new code).
  - Stability checklist.

### FIX Mode
- All output from ANALYZE Mode, plus:
  - Modified code (files changed).
  - Verification results (3 runs PASS/FAIL).
  - Final status: STABILIZED / PARTIALLY FIXED / STILL FLAKY.