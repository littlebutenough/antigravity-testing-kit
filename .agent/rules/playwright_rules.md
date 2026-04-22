# Specific Rules for Playwright

> Applies when setting up and running automation with Playwright (TypeScript or Java).

## 1. Browser Setup (MANDATORY)

- **Debug Viewport:** All UI debugging processes must run with the desktop viewport: **`1920x1080`**.
- **Playwright MCP — Mandatory Resize:** When using Playwright MCP to debug UI, **ALWAYS** call `browser_resize(width=1920, height=1080)` **immediately after opening the browser** (after the first `browser_navigate` command). This is a mandatory step and must not be skipped.
  ```
  Mandatory Order:
  1. browser_navigate(url) → Open the page
  2. browser_resize(width=1920, height=1080) → Set viewport
  3. browser_snapshot() or browser_take_screenshot() → Start inspecting
  ```
- **Headed Mode:** The browser must be opened in visible (headed) mode during setup and test debugging.
- **Headless Mode:** Only allowed when:
  - The test has passed 100% in headed mode during debugging.
  - Or in the default CI/CD pipeline.

## 2. Development Workflow & Element Discovery

- Prioritize using **Playwright MCP** to open the browser and interact with the target page.
- **Inspect Real DOM:** Verify and capture selectors directly from the browser DOM.
- **ABSOLUTELY DO NOT:**
  - Guess locators.
  - Blindly copy locators from old code without verification.
  - Rely on URLs/documentation without confirming existence on the real UI.

## 3. Playwright Locator Priority Order

Playwright provides a set of user-centric semantic locators. Prioritize using these over CSS/XPath:

1. `getByRole()` — Best for semantic elements (button, link, heading...)
2. `getByLabel()` — Best for form fields with labels
3. `getByPlaceholder()` — Best for inputs with placeholder text
4. `getByText()` — Best for text content
5. `getByTestId()` — Best when elements have `data-testid`
6. `locator("css")` — Fallback when no better options are available

Example:
```typescript
// Correct — Semantic locator
page.getByRole('button', { name: 'Login' })
page.getByLabel('Email')
page.getByPlaceholder('Enter password')

// Incorrect — Raw XPath/CSS when semantic alternatives exist
page.locator('//button[@class="btn-login"]')
page.locator('.form-input:nth-child(2)')
```

## 4. Wait Strategy

**FORBIDDEN:**
- `page.waitForTimeout()` — hard sleep
- `await new Promise(r => setTimeout(r, N))` — manual delay
- Any method that fixes wait time

**USE:**
- Leverage Playwright's default auto-waiting.
- Web-First Assertions:
  ```typescript
  await expect(locator).toBeVisible();
  await expect(locator).toBeEnabled();
  await expect(locator).toHaveText('Success');
  await expect(page).toHaveURL(/dashboard/);
  ```
- Only use `waitForSelector()` when `expect()` does not meet special requirements.

## 5. Test Structure

```typescript
test.describe('Module Name', () => {
  test.beforeEach(async ({ page }) => {
    // Setup: navigate, login...
  });

  test('behavior description to test', async ({ page }) => {
    // Arrange: initialize page objects, data
    // Act: perform actions
    // Assert: check results
  });
});
```

- Each test block must have **clear assertions**.
- Use `test.describe` to group tests by module.
- Use `beforeEach` / `afterEach` for setup / teardown.
