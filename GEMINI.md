# GEMINI AI - GLOBAL AUTOMATION AGENT RULES

> **Scope:** Applies to all Test Automation tasks performed by Gemini (Antigravity) within this project.
> **Objective:** Generate test scripts that are efficient, stable – easy to debug – easy to scale – CI friendly.

---

## 🔐 Security & Credentials (HIGHEST PRIORITY — READ FIRST)

> ⚠️ **This rule takes effect immediately upon task start. AI MUST read and comply before performing any actions related to authentication.**

### ❌ PROHIBITED: Reading `.env` file directly

AI **MUST NOT** use any tools (`view_file`, `read_url_content`, `run_command`, `grep`, etc.) to read the contents of the `.env` file for the purpose of obtaining login information (username, password, token, API key...).

> **Reason:** The `.env` file contains sensitive credentials. Reading it directly risks exposing information in logs, chat history, or AI artifacts.

---

## Browser Rules (MANDATORY)

### 🖥️ Viewport & Mode

* All **UI debugging** must run with **desktop viewport**: **`1920x1080`**
* **Real browser must be opened** during debug (headed mode)
* **Headless mode** may only be used **after the test has PASSED debugging on UI**
* CI/CD pipeline **is permitted to run headless by default**

### 🔄 Mandatory Debug Sequence (Playwright MCP)

When using Playwright MCP for UI debugging, **ALWAYS** follow this order:

```
navigate → resize(1920×1080) → wait_for(page_load) → snapshot → interact → screenshot(on_fail)
```

* **DO NOT** call `browser_navigate` again if already on the correct page — avoid unintended reloads
* **ALWAYS** call `browser_resize(width=1920, height=1080)` immediately after `browser_navigate`
* **ALWAYS** verify the page has finished loading before taking a snapshot or interacting

### 📸 Screenshot & Snapshot

* Use **`snapshot`** to analyze DOM and identify locators
* Use **`screenshot`** to save evidence when a test fails or for reporting
* Take a **screenshot immediately when an assertion fails** to support error tracing
* **DO NOT** take screenshots excessively — only when necessary (fail / key milestone)

---

## Tools

### 🛠️ Usage Priority

* Prioritize using **Playwright MCP** for all UI debugging tasks
* Refer to detailed rule: [Playwright Rules](.agent/rules/playwright_rules.md)

### 🔍 Inspect & Debug

* Open real browser for debugging (headed mode)
* Inspect **actual DOM / HTML** on the browser — **DO NOT guess locators**
* Execute and debug tests directly on UI before generating code
* **DO NOT** generate code without inspecting the DOM

### ⚡ Principles

* A locator must be **verified as functional** on the current browser before being added to code
* If a locator is taken from old code → **must re-verify** before use

---

## Cleanup & Delivery

### ✅ Definition of Done

A test is only considered **complete** when it meets **all** of the following criteria:

#### 🧹 Code Cleanup

- [ ] Remove all `print()`, `console.log()`, temporary debug logs
- [ ] Remove unused locators
- [ ] Do not leave commented-out code
- [ ] No hardcoded `waitForTimeout` / `Thread.sleep`
- [ ] No hardcoded test data (email, username, ID must be random/traceable)

#### 🏗️ Structure & POM

- [ ] Adhere to **Page Object Model** — separate Page class, Test class, Utils
- [ ] Locators are defined in Page class, not written inline in tests
- [ ] File, class, method names follow clear and consistent conventions
- [ ] No unused imports

#### ✔️ Test Quality

- [ ] Test **PASSES stably** at least **2 consecutive times** on UI (headed mode)
- [ ] Assertions have clear messages, easy to debug on failure
- [ ] Each test case is independent — does not rely on execution order
- [ ] Test data is generated dynamically (timestamp/random) and traceable

#### 📁 File Output

- [ ] Source code is saved in the correct location within the project structure
- [ ] No temporary files, redundant test files in the source directory
- [ ] Configuration files (config, .env) do not contain real credentials

#### 📋 Result Report

- [ ] Summary of results: number of tests PASS / FAIL / SKIP
- [ ] Clearly state which TCs were implemented and which were skipped (with reason)
- [ ] Note any known issues or limitations if applicable

---

## 1. Language & Communication

- Always communicate, explain ideas, and report in **English**.
- Explain **concisely, clearly, and understandably**.
- Avoid programming speculation or vague error explanations; base conclusions on direct evidence.

## 2. Workflow

- **Recon:** Always inspect the actual interface or DOM/HTML/XML before writing automation. Absolutely **DO NOT GUESS** locators.
- **Implementation:** Maintain **Page Object Model (POM)** . Clearly separate Page objects, Test execution, and Utils/Test data.
- **Execution & Self-fix:** Run test immediately after coding. If test FAILS → read logs → analyze root cause → fix code → rerun → until it PASSES stably. Only ask User when encountering conflicting business rules.
- **Cleanup:** Remove debug logs, redundant code, unused locators before delivering.

## 3. Supported Tech Stack

| Type              | Technology                                      |
| ----------------- | ----------------------------------------------- |
| Language          | Java, TypeScript                                |
| Web Automation    | Playwright (TS/Java), Selenium WebDriver (Java) |
| Mobile Automation | Appium (Java)                                   |
| API Automation    | REST Assured                                    |
| Test Framework    | TestNG, Playwright Test                         |
| Build Tool        | Maven, npm                                      |

## 4. Detailed Rule References

Agent must reference detailed rules in `.agent/rules/`:

- [General Automation Rules](.agent/rules/automation_rules.md) — POM, Test Data, Naming, Assertions
- [Locator Strategy](.agent/rules/locator_strategy.md) — Locator priority order
- [Playwright Rules](.agent/rules/playwright_rules.md) — Browser setup, locator semantic, wait strategy
- [Selenium Rules](.agent/rules/selenium_rules.md) — WebDriverWait, TestNG structure
- [Appium Rules](.agent/rules/appium_rules.md) — Mobile locator, scroll, permission

## 5. Skill References

Agent uses skills in `.agent/skills/` depending on the task:

| Skill                      | Role                                                                                         |
| -------------------------- | -------------------------------------------------------------------------------------------- |
| `qa_automation_engineer`   | Master skill for automation — coordinates the entire process                                  |
| `rbt_manual_testing`       | Master skill for manual testing — 2 modes: QUICK (generate TCs fast) and FULL RBT (6 steps) |
| `requirements_analyzer`    | Analyzes requirements from website/document                                                   |
| `ui_debug_agent`           | Inspects UI/DOM, collects locators                                                           |
| `smart_locator_agent`      | Generates new stable locators                                                                |
| `locator_healer_agent`     | Fixes broken locators                                                                        |
| `test_data_generator`      | Generates unique, traceable test data — supports multi-step pipeline & combinatorial data    |
| `flaky_test_analyzer`      | Analyzes and resolves flaky tests                                                            |
| `jira_integration`         | Integrates Jira/Xray — fetches requirements, pushes test results                              |

## 6. Test Plan Templates

Ready-to-use prompt templates available in `plans/`:

- **`plans/manual/`** — Process for generating Manual Test Cases (2 modes: QUICK + FULL RBT)
  - See `plans/manual/QUICK_START.md` for quick start
  - QUICK Workflow: `/generate_testcases_from_requirements`
  - FULL RBT Workflow: `/generate_manual_testcases_rbt`
- **`plans/automation/`** — 6-step process for generating Automation Scripts
  - See `plans/automation/QUICK_START.md` for quick start
  - One-click: Copy `plans/automation/prompt_automation.txt`
  - Workflow: `/generate_automation_from_testcases`
- **`plans/cross-module/`** — Process for Cross-Module analysis & Combination Matrix
  - See `plans/cross-module/QUICK_START.md` for quick start
  - Analysis Workflow: `/generate_cross_module_test_plan`
  - Data Generation Workflow: `/generate_combinatorial_test_data`

## 7. Test Data

- All fields requiring **unique** values (Email, Username, Code/ID): **MUST** use random data.
- Random data must be **traceable / deterministic** — able to trace back to the failing test.
- Recommended format: combine `test name + timestamp + prefix`.

Example:

```
email:    test_login_1712049200@auto.test
username: auto_user_1712049200
code:     TC_LOGIN_1712049200
```

## 8. Code Quality (Smart Waits)

- **DO NOT** use hard sleep (`waitForTimeout`, `Thread.sleep`, fixed delay).
- Only use **smart waits** / auto-waiting:

| Framework  | Smart Wait                                                           |
| ---------- | -------------------------------------------------------------------- |
| Playwright | `expect().toBeVisible()`, `expect().toBeEnabled()`, Locator APIs |
| Selenium   | `WebDriverWait` + `ExpectedConditions`                           |
| Appium     | `WebDriverWait` + custom conditions                                |

- Limit `waitForSelector` if `expect()` suffices.
- Every assertion must have a **clear timeout** or use a reasonable default timeout.

## 9. Anti-Patterns (FORBIDDEN)

| ❌ Anti-Pattern                                   | ✅ Correct Replacement                        |
| ------------------------------------------------- | ---------------------------------------------- |
| Guess selector                                    | Inspect actual DOM before coding             |
| Hard sleep (`waitForTimeout`, `Thread.sleep`) | Smart waits (`expect()`, `WebDriverWait`)  |
| Copy selector from old code without verification  | Always verify selector on current browser   |
| Write test without running it                     | Run test immediately after implementation      |
| Commit FAIL test                                  | Only commit when test PASSES stably          |
| Leave debug log / commented code on delivery      | Cleanup before delivery                        |
| Use hardcoded duplicate test data                 | Generate random + traceable data               |

## 10. Workflow References

Agent uses workflows in `.agent/workflows/` via slash commands:

| Workflow                                  | Description                                                                 |
| ----------------------------------------- | --------------------------------------------------------------------------- |
| `/generate_requirements_from_website`     | Generate requirements from website/module                                    |
| `/analyze_requirement_document`           | Analyze requirement document (Jira/.doc) — generate analysis doc, NOT TCs  |
| `/generate_manual_testcases_rbt`          | Generate manual test cases via AI-RBT 6 steps (FULL RBT mode)              |
| `/generate_testcases_from_requirements`   | Generate test cases quickly from requirements (QUICK mode)                   |
| `/generate_automation_from_testcases`     | Convert manual test cases → automation scripts                             |
| `/generate_automation_from_ui_flow`       | Generate automation directly from UI flow                                    |
| `/generate_application_test_plan`         | Explore app, generate test plan (Mode PLAN) or full suite (Mode FULL)       |
| `/generate_automation_framework`          | Design automation framework                                                  |
| `/generate_locator`                       | Generate stable locator for UI element                                       |
| `/generate_test_data`                     | Generate structured test data                                                |
| `/generate_cross_module_test_plan`        | Analyze cross-module, generate Data Flow Map + combination matrix (Pairwise/Cartesian) |
| `/generate_combinatorial_test_data`       | Generate test data for combination matrix — offline or pipeline via browser  |
| `/generate_api_tests_from_swagger`        | Generate API tests from Swagger spec                                         |
| `/analyze_flaky_tests`                    | Analyze and resolve flaky tests                                              |
| `/fetch_jira_requirements`                | Fetch requirements/user stories from Jira                                    |
| `/import_test_results_xray`               | Push test results to Xray                                                    |