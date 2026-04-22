# General Rules for QA Automation

> Applies to all automation testing tasks, regardless of framework (Playwright, Selenium, Appium).

## 1. Architecture & Framework

- **Page Object Model (POM)** is mandatory.
- Clear separation:
  - **Page classes:** Declaration of locators + UI interaction methods
  - **Test classes:** Contains test logic + assertions
  - **Test data:** Separated from functional code (JSON, DataProvider, Utils)
- Assertions should only be placed in Test classes, NOT in Page classes.

## 2. Test Data Generation

- All unique fields (Email, Username, Customer ID...) **must be dynamic**, no hardcoding.
- Use UUID, Timestamp, or Faker library.
- Data must be **traceable** — looking at the DB should immediately identify which test created it:
  ```
  Format: [prefix]_[testName]_[timestamp]_[random]
  Example: auto_createCustomer_20260402_A3F2@test.com
  ```
- Support parallel execution: each test method has its own data, no conflicts.

## 3. Code Quality

- No duplicate logic — create helper methods for repetitive actions.
- Code must be simple, readable, and maintainable.
- Before delivering code:
  - Remove all `console.log`, `System.out.println`, `print()` generated during debugging.
  - Remove commented-out code (`//`, `/* */`).
  - Remove unused locators/variables.

## 4. File & Directory Management

- DO NOT automatically delete source files without user confirmation.
- Check existing directory structure before creating new files — avoid duplicates.
- Place files in the correct directory according to project architecture (refer to `plan/automation/0_project_architecture`).

## 5. Naming Conventions

### Java

| Component | Rule | Example |
|---|---|---|
| Page class | PascalCase + `Page` suffix | `LoginPage.java`, `CartPage.java` |
| Test class | PascalCase + `Test` suffix | `LoginTest.java`, `CartTest.java` |
| Test method | Starts with `test` + behavior description | `testLoginWithValidCredentials()` |
| Locator variable | lowerCamelCase + element description suffix | `loginButton`, `usernameInput` |
| Utils class | PascalCase + functional description | `DataGenerator.java`, `WaitHelper.java` |

### TypeScript / Playwright

| Component | Rule | Example |
|---|---|---|
| Page class | PascalCase + `Page` suffix | `LoginPage.ts`, `CartPage.ts` |
| Test file | kebab-case + `.spec.ts` | `login.spec.ts`, `cart.spec.ts` |
| Test block | `test('behavior description')` | `test('login successfully')` |
| Locator variable | lowerCamelCase or readonly | `readonly loginButton` |
| Utils | PascalCase or kebab-case | `DataGenerator.ts`, `data-generator.ts` |

## 6. Assertions

- Each test case **MUST** have at least one assertion at the end.
- Use interleaved assertions at critical steps.
- Assertions must clearly describe expected behavior:
  ```java
  // Java/TestNG
  Assert.assertTrue(dashboardPage.isDisplayed(), "Dashboard should be displayed after login");
  ```
  ```typescript
  // Playwright
  await expect(page.getByText('Login successful')).toBeVisible();
  ```

## 7. Test Independence

- Each test case must be **independent** — not dependent on the result of another test.
- Clear setup/teardown (`@BeforeMethod/@AfterMethod` or `beforeEach/afterEach`).
- Do not share state between test methods.