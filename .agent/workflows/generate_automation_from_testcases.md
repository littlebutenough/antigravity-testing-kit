---
description: Convert manual test cases into automation scripts autonomously using the 6-step AI-RBT Framework via Antigravity Capabilities.
skills:
  - qa_automation_engineer
  - ui_debug_agent
  - smart_locator_agent
  - test_data_generator
---

# Workflow: Generate Automation Scripts from Manual Test Cases

> **MANDATORY SKILLS:** You MUST load and carefully read the following skills before starting:
> - **`qa_automation_engineer`** (`.agent/skills/qa_automation_engineer/SKILL.md`) — General automation rules + workflow routing.
> - **`ui_debug_agent`** (`.agent/skills/ui_debug_agent/SKILL.md`) — Inspect DOM, collect locators.
> - **`smart_locator_agent`** (`.agent/skills/smart_locator_agent/SKILL.md`) — Generate stable locators.
> - **`test_data_generator`** (`.agent/skills/test_data_generator/SKILL.md`) — Generate unique, traceable test data.

This workflow helps the agent read manual test case files provided by the user, automatically open a browser to inspect the UI, collect actual locators, generate complete automation scripts (POM + Test), run tests, and auto-fix errors until they PASS.

## ⚠️ Execution Principles

- **Role:** Agent acts as a Senior Automation Engineer — complies with Clean Code + POM.
- **All output in English**
- **ABSOLUTELY NO guessing of locators** — must inspect actual DOM using MCP browser tools.
- **Desktop viewport 1920×1080** for all UI debugging.
- ⚠️ **Rule E3 (CRITICAL):** When a test FAILS → read logs → analyze → fix code → re-run. **DO NOT ask the user during the fix process.** Only ask if there is a business rule contradiction or after 5 auto-heal cycles.
- **`task.md` artifact** — MUST be created to track progress across the 6 steps.

## How is this workflow different from `generate_automation_from_ui_flow`?

| | `from_testcases` (this workflow) | `from_ui_flow` |
|---|---|---|
| **Input** | Structured manual test case file (MD/Excel/JSON) | Verbal UI step descriptions or just a URL |
| **Existing TC** | ✅ Available — read and convert | ❌ Not available — agent explores autonomously |
| **Approach** | Read TC → inspect UI to verify → generate code | Live browser execution → collect → generate code |

## Required Input

| Input | How to Obtain | Priority |
|---|---|---|
| **Test case file** (MD/Excel/JSON/URL) | Provided path or URL | ⭐ Mandatory |
| **Application URL** | Provided by user or in TC | ⭐ Mandatory |
| **Credentials** (if login required) | Provided by user or use existing fixture | Optional |
| **Tech stack** | Specified by user or detected from project | Optional |

If insufficient input is provided → ask before starting.

## Execution Steps

### Step 1: Initialization, Analysis & Planning (Context & Analysis)

1. **Read the test case file** provided by the user:
   - Local file → `view_file`.
   - URL (Google Sheets, Confluence, etc.) → `read_url_content`.
   - Identify format: Markdown table, Excel, JSON, or free-form text.

2. **Parse test cases** and extract:
   - TC List (ID, Title, Steps, Expected Results, Test Data, Priority).
   - Pages/screens visited by the TC.
   - Pre-conditions (login, data setup, navigation...).
   - Dependencies between TCs (if any).

3. **Identify tech stack** (if unclear):

   | Framework | Language | Runner | When to Choose |
   |---|---|---|---|
   | Playwright | TypeScript | Playwright Test | Default for web. |
   | Playwright | Python | Pytest | When project uses Python. |
   | Selenium | Java | TestNG | When user requests Java. |
   | Appium | Java | TestNG | Mobile app. |

4. **Create task.md artifact** to track progress:
   ```markdown
   # Automation Generation Progress
   - [x] Step 1: Analyze test cases
   - [ ] Step 2: UI Recon (MCP Recon)
   - [ ] Step 3: POM Design
   - [ ] Step 4: Prepare test data
   - [ ] Step 5: Generate automation scripts
   - [ ] Step 6: Run test + Auto-heal

   ## Test Cases to Automate
   | TC ID | Title | Pages | Priority | Status |
   |---|---|---|---|---|
   | TC01 | Successful Login | LoginPage, DashboardPage | P1 | ⏳ |
   | TC02 | Login with wrong password | LoginPage | P1 | ⏳ |
   ```

### Step 2: Autonomous UI Recon (MCP Recon)

1. **Open browser** via MCP and navigate according to test case steps:
   ```
   browser_navigate → App URL
   browser_resize → 1920 × 1080
   browser_wait_for → page load complete
   browser_snapshot → collect DOM
   ```

2. **For each page in the test cases**, perform:
   - `browser_snapshot` → read accessibility tree.
   - Identify all interaction elements (inputs, buttons, links, dropdowns...).
   - Collect best locator for each element (according to priority in `smart_locator_agent`).
   - Verify locator by trying interactions (`browser_click`, `browser_type`).

3. **Record in the Locator Collection table:**

   | Page | Element | Action | Primary Locator | Fallback Locator | Verified |
   |---|---|---|---|---|---|
   | LoginPage | Email input | Type | `getByLabel('Email')` | `#email` | ✅ |
   | LoginPage | Password input | Type | `getByLabel('Password')` | `#password` | ✅ |
   | LoginPage | Login button | Click | `getByRole('button', {name: 'Login'})` | `button[type=submit]` | ✅ |
   | DashboardPage | Welcome text | Assert | `getByRole('heading', {name: /Welcome/})` | `.welcome-header` | ✅ |

4. **Situation Handling:**

   | Situation | How to Handle |
   |---|---|
   | URL blocked / needs VPN | Notify the user. |
   | Login required | Use existing fixture or ask user for credentials. |
   | Element not found | Re-snapshot → try other locators → notify user if DOM changed. |
   | CAPTCHA / 2FA | Notify the user — cannot automate. |
   | Dynamic content / SPA | `browser_wait_for` specific text before snapshotting. |

5. **ABSOLUTELY NO guessing of selectors** — every locator must be verified on the actual DOM.

### Step 3: POM Design (Page Object Model Architecture)

1. **Identify the list of Page classes** to create:
   - Each page/screen in the test flow → 1 Page class.
   - Consider creating `BasePage` if not already in the project.

2. **Generate Page Object classes** using `write_to_file`:

   **Structure of each Page class:**
   ```
   - Locators (declared at the top of the class — from Step 2)
   - Constructor (accepts page/driver instance)
   - Action methods (describe user behavior, not DOM)
   - Verification methods (check state/text after action)
   ```

   **Principles:**
   - Method names describe behavior: `login()`, `fillRegistrationForm()`, not `clickButton()`.
   - No hardcoded waits — only smart waits.
   - Locators from Step 2 (verified) — DO NOT GUESS.
   - Return `this` or the next page object for method chaining (if applicable).

3. **Check current project structure:**
   - If the project already has `pages/` → generate files in the correct directory.
   - If it's a new project → create structure according to the `framework_architect` skill.
   - Do not create duplicates — check if the page exists before creating a new one.

### Step 4: Prepare Data (Test Data Strategy)

1. **Analyze test data** from test cases:
   - Data needing to be **unique per run** (email, username, ID) → generate random + traceable.
   - **Fixed** data (URL, config values) → read from env/config.
   - Data needing **multiple sets** (data-driven) → create external files (JSON/YAML).

2. **Generate test data utilities** (using `test_data_generator` skill):
   ```
   Format: <prefix>_<testName>_<timestamp>
   Example:
   - Email:    auto_login_1712049200@test.com
   - Username: auto_user_1712049200
   - Code:     TC_REG_1712049200
   ```

3. **Sensitive data** (credentials):
   - Read from env variables or config files.
   - **DO NOT hardcode** in test code.
   - **DO NOT read .env directly** (security rule).

### Step 5: Generate Automation Scripts (Test Classes)

1. **Create test classes** — each test case or group of related TCs → 1 test file:

   **Structure of each test:**
   ```
   Setup (Arrange):
   - Initialize page objects.
   - Prepare test data.
   - Navigate to the page under test.
   - Login (if needed, via fixture).

   Execution (Act):
   - Perform steps according to the test case.
   - Call methods from Page Objects.

   Verification (Assert):
   - Assert results against expected results from the TC.
   - Clear assertion message, easy to debug on failure.
   ```

2. **Mandatory Assertions:**
   - Each TC MUST have at least one assertion.
   - Assertion message clearly describes: `"Expected dashboard to show after login"`.
   - Use soft assertions when checking multiple points.
   - Appropriate timeouts (do not use defaults that are too short).

3. **Coding Principles:**
   - No `waitForTimeout()` / `Thread.sleep()` — only smart waits.
   - No inline locators in tests — locators belong in the Page class.
   - Clean imports — no unused imports.
   - Independent tests — no execution order dependency.
   - Cleanup/teardown if tests create data.

### Step 6: Execution & Auto-Heal (Execution & Auto-Heal — RULE E3)

1. **Run tests** using `run_command`:
   ```bash
   # Playwright TS
   npx playwright test <test_file> --headed

   # Playwright Python
   python -m pytest <test_file> --headed

   # Selenium Java
   mvn test -Dtest=<TestClass>
   ```

2. **Monitor results** via `command_status`:

   **If PASS:**
   - Run **one more time** to confirm stability.
   - Update `task.md`: TC status → ✅ PASS.
   - Cleanup debug logs, commented code.

   **If FAIL → Enter Auto-Heal loop:**

   ```
   WHILE test FAIL (max 5 cycles):
     1. Read error log / stack trace → identify the failing step.
     2. Classify the error:

        | Error | Action |
        |---|---|
        | Element not found | Open MCP → snapshot → verify/change locator. |
        | Click intercepted | Wait for overlay to disappear → retry click. |
        | Timeout | Increase timeout or add wait condition. |
        | Assertion fail | Check expected vs actual value → update assertion. |
        | Navigation error | Check URL, redirect, page load. |
        | Test data conflict | Generate new unique data. |
        | Import/compile error | Fix imports, check class name. |

     3. Fix code using replace_file_content / multi_replace_file_content.
     4. Re-run test.
     5. Log in task.md: "Cycle 2: Fix locator XYZ → PASS"
   ```

3. **⚠️ Rule E3 — DO NOT ASK THE USER while fixing.** Only ask if:
   - Business logic contradiction (TC says A but app shows B).
   - Server/app not accessible.
   - Failed after 5 auto-heal cycles.

4. **Verify stability** — tests must PASS **2 consecutive times**:
   ```bash
   # Playwright
   npx playwright test <test_file> --repeat-each=2 --retries=0
   ```

### Step 7: Cleanup & Delivery

1. **Code cleanup** (mandatory):
   - [ ] Delete temporary `console.log()` / `print()` / debug logs.
   - [ ] Delete unused locators.
   - [ ] Delete commented-out code.
   - [ ] No `waitForTimeout()` / `Thread.sleep()`.
   - [ ] No hardcoded test data (email, password).
   - [ ] Clean imports — no unused imports.

2. **Update task.md artifact** with final results:
   ```markdown
   ## Results
   | TC ID | Title | Status | Stability | Notes |
   |---|---|---|---|---|
   | TC01 | Successful Login | ✅ PASS | 2/2 stable | — |
   | TC02 | Login with wrong password | ✅ PASS | 2/2 stable | — |
   | TC03 | Account Registration | ⚠️ SKIP | — | Requires CAPTCHA |

   ## Files Created
   - src/pages/login.page.ts
   - src/pages/dashboard.page.ts
   - src/tests/login.spec.ts
   ```

3. **Report** to the user:
   - Total: X TCs PASS / Y TCs FAIL / Z TCs SKIP.
   - List of files created/modified.
   - Known issues / limitations.
   - Locator Collection table (reference).

## Output

- **`task.md` artifact** — progress checklist + test run results.
- **Page Object classes** — 1 file per page, locators verified from DOM.
- **Test classes** — complete automation scripts, PASS stable.
- **Test data utilities** — generators for unique + traceable data.
- **Locator Collection table** — all elements + primary/fallback locators.
- **Results report** — PASS/FAIL/SKIP summary.