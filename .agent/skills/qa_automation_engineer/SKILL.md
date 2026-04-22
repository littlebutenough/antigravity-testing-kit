---
name: QA Automation Engineer
description: Skill supporting the agent in performing QA automation testing tasks including generating test cases, automation scripts, API tests, locators, flaky test analysis, and test data creation.
---

# QA Automation Engineer

## Description

This skill enables the agent to assist with software testing and automation tasks.

The agent can:

- Generate manual test cases from requirements
- Generate test automation scripts from test cases or UI flows
- Generate API tests from Swagger/OpenAPI specifications
- Explore applications and discover test scenarios
- Generate automation frameworks
- Generate test data
- Analyze flaky tests
- Generate stable locators
- Generate requirements from website analysis

This skill is designed for modern QA workflows and automation development.

---

# When to Use

Use this skill when the user asks about:

- Test automation
- Manual testing
- Automation frameworks
- API testing
- UI testing
- Test data generation
- Flaky test debugging
- Locator generation
- Requirements analysis from website
- Jira integration (fetch requirements, push test results)
- Xray test management

Typical prompts include:

- Generate test cases from requirement
- Generate Selenium automation from test case
- Generate automation from UI steps
- Generate API tests from Swagger
- Generate regression suite → _(redirect to `generate_application_test_plan` or `generate_manual_testcases_rbt`)_
- Generate test data
- Analyze flaky test
- Generate locator for element
- Generate requirements from website

---

# Workflow Routing

When the user request matches a specific task, select the appropriate workflow file from `.agent/workflows/`.

### Generate test cases from requirements

> **Delegate:** This task belongs to the **`rbt_manual_testing`** skill — not `qa_automation_engineer`.

Use workflow: `generate_testcases_from_requirements` (QUICK mode) or `generate_manual_testcases_rbt` (FULL RBT mode).

Triggers when user asks:

- generate test cases → **delegate to `rbt_manual_testing` (QUICK mode)**
- write manual test cases → **delegate to `rbt_manual_testing` (QUICK mode)**
- test scenarios from requirement → **delegate to `rbt_manual_testing` (QUICK mode)**
- generate complete test cases / 6-step process → **delegate to `rbt_manual_testing` (FULL RBT mode)**

---

### Generate automation from manual test case

Use workflow: `generate_automation_from_testcases`

Triggers when user asks:

- convert test case to automation
- generate Selenium automation
- generate Playwright automation from test case

---

### Generate automation from UI steps

Use workflow: `generate_automation_from_ui_flow`

Triggers when user asks:

- automate this UI flow
- generate automation from steps
- run UI steps and generate Selenium script

---

### Generate API tests

Use workflow: `generate_api_tests_from_swagger`

Triggers when user provides:

- Swagger URL
- OpenAPI specification

---

### Generate test data

Use workflow: `generate_test_data`

Triggers when user asks:

- generate test data
- generate boundary test data

---

### Analyze cross-module feature & generate combinatorial matrix

Use workflow: `generate_cross_module_test_plan`

> Workflow for **complex features spanning multiple sequential modules**. Generates Data Flow Map + Multi-dimensional Combinatorial Matrix (Pairwise / Business-critical / Full Cartesian).

Triggers when user asks:

- analyze cross-module features
- test multiple linked modules
- generate combinatorial matrix
- test features with multiple combined conditions
- analyze multi-module feature
- pairwise testing
- multi-dimensional decision table

---

### Generate combinatorial test data (multi-module pipeline)

Use workflow: `generate_combinatorial_test_data`

> Generates test data for combinatorial matrices. Supports 2 modes: **GENERATE** (offline generation) and **PIPELINE** (live browser execution through N modules).

Triggers when user asks:

- generate data for combinatorial matrix
- create test data for combinatorial matrix
- run pipeline to create data across multiple modules
- generate combinatorial test data
- setup data for cross-module test

---

### Generate regression suite

> **No standalone workflow.** Use `generate_application_test_plan` (Mode PLAN) or `generate_manual_testcases_rbt` (FULL RBT) depending on the input.

Triggers when user asks:

- create regression test suite
- generate regression scenarios

---

### Generate automation framework

> **Delegate:** This task uses the **`framework_architect`** skill to design the framework.

Use workflow: `generate_automation_framework`

Triggers when user asks:

- create automation framework
- design Selenium framework
- design Playwright framework
- design Appium framework
- scaffold automation project
- design new framework

---

### Explore application and generate test plan

Use workflow: `generate_application_test_plan`

> This workflow has **2 modes**: PLAN (default — test plan only) and FULL (test plan + automation skeleton).
> When the user requests a "full automation suite" or "bootstrap automation" → automatically selects Mode FULL.

Triggers when user asks:

- explore application
- discover test scenarios
- generate test plan
- generate full automation suite
- bootstrap automation for project

---

### Analyze flaky tests

Use workflow: `analyze_flaky_tests`

Triggers when user asks:

- why is this test flaky
- analyze unstable automation

---

### Generate stable locators

Use workflow: `generate_locator`

Triggers when user asks:

- generate locator for this element
- find stable selector
- create automation locator

---

### Generate requirements from website

Use workflow: `generate_requirements_from_website`

Triggers when user asks:

- generate requirements from website
- analyze website module and create requirements
- extract user stories from web page

---

### Analyze requirement document

> **Delegate:** This task uses the **`requirements_analyzer`** skill to analyze requirement documents.

Use workflow: `analyze_requirement_document`

> Workflow only **analyzes** requirements — DOES NOT generate test cases. Output is a detailed analysis document including: AC breakdown, dependencies, ambiguities, risks.

Triggers when user asks:

- analyze requirement document
- review requirement / analyze this ticket
- analyze Jira ticket / requirement
- find ambiguities in requirements
- analyze requirement / review requirement document

---

### Fetch requirements from Jira

Use workflow: `fetch_jira_requirements`

Triggers when user asks:

- fetch jira requirements
- get requirements from jira
- get jira ticket
- import user stories from jira

---

### Import test results to Xray

Use workflow: `import_test_results_xray`

Triggers when user asks:

- push test results to xray
- push test results to xray
- import test execution to jira
- upload playwright results to xray

---

# Automation Framework

Default automation stack:

- **Language:** Java
- **UI automation:** Selenium WebDriver or Playwright
- **Test framework:** TestNG
- **API automation:** REST Assured
- **Mobile automation:** Appium
- **Design pattern:** Page Object Model (POM)

---

# Locator Strategy

## Selenium Locator Priority

1. `id`
2. `data-testid`
3. `name`
4. `css selector`
5. `xpath` (last resort)

Avoid fragile locators such as auto-generated class names or positional xpaths.

## Playwright Locator Priority

1. `getByRole()`
2. `getByLabel()`
3. `getByPlaceholder()`
4. `getByText()`
5. `getByTestId()`
6. `css selector`
7. `xpath` (last resort)

Avoid fragile selectors such as dynamic class names.

> **Note:** For detailed locator rules, refer to `.agent/rules/locator_strategy.md`.

---

# Rules References

The agent MUST also follow the detailed rules defined in `.agent/rules/`:

- [automation_rules.md](.agent/rules/automation_rules.md) — General automation best practices
- [locator_strategy.md](.agent/rules/locator_strategy.md) — Detailed locator selection rules
- [playwright_rules.md](.agent/rules/playwright_rules.md) — Playwright-specific rules
- [selenium_rules.md](.agent/rules/selenium_rules.md) — Selenium-specific rules
- [appium_rules.md](.agent/rules/appium_rules.md) — Appium mobile automation rules

---

# References

The agent may consult additional documentation in the `references/` folder:

- `PROJECT_CONTEXT.md` — Project domain, tech stack, key modules
- `TEST_STRATEGY.md` — Testing objectives, scope, execution plan
- `PROMPT_TEMPLATES.md` — Reusable prompt templates for common QA tasks

External references (replacement for merged files):

- `plans/automation/project_architecture/README.md` — Repository structure & project architecture (replaces REPOSITORY_MAP.md)
- `GEMINI.md` > "Cleanup & Delivery" — Quality checklist / Definition of Done (replaces SELF_CHECK.md)

---

# Output

Depending on the request, the agent may return:

- Manual test cases (structured format)
- Automation scripts (Java/TypeScript)
- API tests (REST Assured)
- Locator recommendations
- Test data (structured, randomized, traceable)
- Automation framework design
- Requirements documents

Automation outputs should include:

- Page Object classes
- Test classes
- Assertions validating expected behavior
- Clean, readable, maintainable code (no debug logs, no commented code)