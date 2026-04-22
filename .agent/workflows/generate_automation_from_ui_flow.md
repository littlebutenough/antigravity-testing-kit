---
description: Execute UI flows directly on a browser, collect locators from the actual DOM, and generate automation scripts. Supports Playwright, Selenium, Appium.
skills:
  - ui_debug_agent
  - smart_locator_agent
  - qa_automation_engineer
---

# Workflow: Generate Automation from UI Flow

> **MANDATORY SKILL:** You MUST load and carefully read the **`ui_debug_agent`** skill (at `.agent/skills/ui_debug_agent/SKILL.md`) before starting. Additionally, refer to the **`smart_locator_agent`** skill for generating stable locators and **`qa_automation_engineer`** for general automation rules.

This workflow helps the agent **directly execute** a sequence of UI actions on a real browser, collect locators from the actual DOM, and generate complete automation scripts — all in an automated flow, without needing an existing manual test case.

## ⚠️ Execution Principles

- **All output in English**
- **ABSOLUTELY NO guessing of locators** — must be obtained from the actual DOM using MCP browser tools.
- **Must execute each UI step on a real browser** before generating code.
- **Desktop viewport 1920×1080** for all UI debugging.
- ⚠️ **Rule E3:** When a test FAILS → read logs → analyze → fix → re-run. DO NOT ask the user.

## How is this workflow different from `generate_automation_from_testcases`?

| | `from_testcases` | `from_ui_flow` (this workflow) |
|---|---|---|
| **Input** | Existing manual test case file | Verbal UI step descriptions or URL + actions |
| **Approach** | Read TC → inspect UI → generate code | **Live browser execution** → collect locators → generate code |
| **When to use** | Already have a test case document | No TC available, only know "go to this page, click that" |

## Required Input

The agent needs at least **one of the following inputs** from the user:

| Input | Example | Priority |
|---|---|---|
| **URL + UI steps description** | "Go to https://example.com, login, create a new user" | ⭐ Most common |
| **URL + recording/video** | User-provided interaction video | Optional |
| **URL + screenshots** | User-provided step-by-step screenshots | Optional |
| **URL only** | "Automate login flow of this page" | Agent explores autonomously |

If insufficient input is provided → ask:
- Application URL?
- Description of the flow to automate (step-by-step)?
- Credentials if login is required?
- Preferred framework? (default: Playwright + TypeScript)

## Execution Steps

### Step 1: Reception & Preparation (Setup)

1. **Parse UI steps** from user input:
   - Convert verbal descriptions into a structured list of steps:
     ```
     Step 1: Navigate to https://example.com/login
     Step 2: Enter username "admin@test.com"
     Step 3: Enter password "***"
     Step 4: Click Login button
     Step 5: Verify dashboard is displayed
     ```

2. **Confirm tech stack** with the user (if unclear):

   | Framework | Language | When to Use |
   |---|---|---|
   | **Playwright** | TypeScript | Default for web automation |
   | **Playwright** | Python | When the user uses a Python stack |
   | **Selenium** | Java | When the user requests Java/Selenium |
   | **Appium** | Java | Mobile app automation |

3. **Create task.md artifact** to track progress:
   ```markdown
   # UI Flow Automation Progress
   - [ ] Step 1: Preparation — parse UI steps
   - [ ] Step 2: Run UI flow on browser — collect locators
   - [ ] Step 3: Generate Page Objects + Test scripts
   - [ ] Step 4: Run test + Auto-heal
   ```

### Step 2: Run UI Flow on Browser & Collect Locators (Live Recon)

> ⚡ This is the **most important step** — distinguishing this workflow from others.

1. **Open browser via MCP** and navigate to the URL:
   ```
   browser_navigate → URL
   browser_resize → 1920 × 1080
   browser_wait_for → page load complete
   browser_snapshot → collect initial DOM
   ```

2. **Execute each step** according to the list. For each step:

   ```
   a. browser_snapshot → read DOM, identify the element to interact with
   b. Identify the best locator (according to locator priority)
   c. Execute action (click / type / select / hover)
   d. browser_snapshot → confirm action result
   e. Record in the locator collection table
   ```

3. **Locator Collection Table** (recorded after each step):

   | Step | Action | Element | Primary Locator | Fallback Locator | Verified |
   |---|---|---|---|---|---|
   | 1 | Navigate | — | — | — | ✅ |
   | 2 | Type | Username input | `getByLabel('Email')` | `#email` | ✅ |
   | 3 | Type | Password input | `getByLabel('Password')` | `#password` | ✅ |
   | 4 | Click | Login button | `getByRole('button', {name: 'Login'})` | `button[type=submit]` | ✅ |
   | 5 | Assert | Dashboard title | `getByRole('heading', {name: 'Dashboard'})` | `.dashboard-title` | ✅ |

4. **Locator Priority** (complies with `.agent/rules/locator_strategy.md`):

   **Playwright:**
   `getByRole()` → `getByLabel()` → `getByPlaceholder()` → `getByText()` → `getByTestId()` → CSS → XPath

   **Selenium:**
   `id` → `data-testid` → `name` → CSS selector → XPath

   **Appium:**
   `accessibility-id` → `id` → `name` → `xpath` (relative)

5. **Situation Handling while running UI:**

   | Situation | How to Handle |
   |---|---|
   | Element not found | `browser_snapshot` again → check DOM → try different locator |
   | Page not fully loaded | `browser_wait_for` text/element → retry |
   | Modal/popup appears | Handle popup first → continue flow |
   | Redirect/navigation | `browser_snapshot` again on the new page |
   | Scroll required | `browser_evaluate` → scrollIntoView |
   | Login required | Ask user for credentials or use existing fixture |
   | CAPTCHA / 2FA | Notify the user — cannot automate |

6. **Screenshot evidence** — capture at key milestones:
   - After successful login.
   - After completing the main flow.
   - When encountering an error/unexpected state.

### Step 3: Generate Automation Scripts (Code Generation)

1. **Generate Page Object classes** from the locator collection:

   **Playwright TypeScript:**
   ```typescript
   // src/pages/login.page.ts
   import { Page, Locator } from '@playwright/test';

   export class LoginPage {
     readonly page: Page;
     readonly emailInput: Locator;
     readonly passwordInput: Locator;
     readonly loginButton: Locator;

     constructor(page: Page) {
       this.page = page;
       this.emailInput = page.getByLabel('Email');
       this.passwordInput = page.getByLabel('Password');
       this.loginButton = page.getByRole('button', { name: 'Login' });
     }

     async login(email: string, password: string) {
       await this.emailInput.fill(email);
       await this.passwordInput.fill(password);
       await this.loginButton.click();
     }
   }
   ```

   **Selenium Java:**
   ```java
   // src/main/java/.../pages/LoginPage.java
   public class LoginPage extends BasePage {
     @FindBy(id = "email")
     private WebElement emailInput;

     @FindBy(id = "password")
     private WebElement passwordInput;

     @FindBy(css = "button[type='submit']")
     private WebElement loginButton;

     public void login(String email, String password) {
       waitAndType(emailInput, email);
       waitAndType(passwordInput, password);
       waitAndClick(loginButton);
     }
   }
   ```

2. **Generate Test class:**
   - Import Page Objects
   - Structure: **Arrange → Act → Assert**
   - Clear assertions with descriptive messages
   - Unique + traceable test data (using timestamp/random)

3. **Code Generation Principles:**
   - Locators MUST be from Step 2 (verified on DOM) — DO NOT GUESS.
   - Do not hardcode test data (credentials read from env/config).
   - No `waitForTimeout()` / `Thread.sleep()` — only smart waits.
   - Method names describe user behavior, not DOM operations.
   - One file per page, one file per test.

### Step 4: Run Test & Auto-Heal (Execution & Auto-Heal)

1. **Run test** using `run_command`:
   ```bash
   # Playwright TS
   npx playwright test <test_file> --headed

   # Playwright Python
   python -m pytest <test_file> --headed

   # Selenium Java
   mvn test -Dtest=<TestClass>
   ```

2. **Monitor results** via `command_status`:
   - If **PASS** → proceed to verify stability
   - If **FAIL** → enter Auto-Heal loop:

   ```
   WHILE test FAIL (max 5 cycles):
     1. Read error log → identify failing step
     2. Classify the error:
        - Incorrect locator → open MCP browser, re-inspect DOM, change locator
        - Timing issue → add smart wait or adjust assertion timeout
        - Incorrect page state → check flow, add wait for navigation
        - Test data conflict → generate new (unique) data
     3. Fix code using replace_file_content / multi_replace_file_content
     4. Re-run test
   ```

3. **Verify stability** — run test **2 consecutive times** PASS:
   ```bash
   # Playwright
   npx playwright test <test_file> --repeat-each=2

   # Pytest
   python -m pytest <test_file> --count=2
   ```

4. **⚠️ Rule E3:** DO NOT ask the user during the fix process. Only ask if:
   - URL blocked / captcha required
   - Business logic contradiction (unclear expected behavior)
   - Failed after 5 auto-heal cycles

### Step 5: Cleanup & Delivery

1. **Code cleanup** (mandatory before delivery):
   - [ ] Delete `console.log()` / `print()` / debug logs.
   - [ ] Delete unused locators.
   - [ ] Delete commented-out code.
   - [ ] No `waitForTimeout()` / `Thread.sleep()`.
   - [ ] No redundant imports.

2. **Update task.md artifact** with results:
   ```markdown
   ## Results
   - ✅ Pages created: LoginPage, DashboardPage
   - ✅ Tests created: login.spec.ts
   - ✅ Test status: 2/2 PASS (stable)
   - 📊 Locators collected: 8 elements, all verified
   ```

3. **Report** to the user:
   - List of files created
   - Number of tests PASS/FAIL
   - Locator collection table (for user reference)
   - Known limitations (if any)

## Output

- **`task.md` artifact** — progress checklist + results
- **Page Object classes** — 1 file per page/screen, locators verified from DOM
- **Test classes** — complete automation scripts, PASS tested
- **Locator Collection table** — all elements collected + primary/fallback locators
- **Evidence screenshots** — captured at key milestones