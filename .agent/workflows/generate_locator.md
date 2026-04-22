---
description: Generate stable locators for UI elements. Supports Playwright, Selenium, Appium.
skills:
  - smart_locator_agent
  - ui_debug_agent
---

# /generate_locator — Generate Stable Locators for UI Automation

> The user provides the element needing a locator (description, screenshot, URL, or HTML snippet).
> AI inspects the actual DOM/UI hierarchy, generates a stable locator based on standard priority, verifies uniqueness, and returns the result.

> **MANDATORY:** Before starting, you MUST load and carefully read:
> - **Skill:** `.agent/skills/smart_locator_agent/SKILL.md` — Locator generation process.
> - **Skill:** `.agent/skills/ui_debug_agent/SKILL.md` — DOM inspection process.
> - **Rule:** `.agent/rules/locator_strategy.md` — Locator priority map.
> - **Rule:** `.agent/rules/<framework>_rules.md` — Specific rules for the framework in use.

---

## Required User Input

| Input | Mandatory | Description |
|-------|----------|-------|
| Element Description | ✅ | e.g., "Login button", "Country dropdown", "Email input field". |
| Page URL | ✅ | For the AI to navigate and inspect the actual DOM. |
| Framework | ✅ | `playwright`, `selenium`, or `appium`. |
| HTML snippet | ❌ | If the User has existing DOM context — AI uses it for quick analysis. |
| Target Page Class | ❌ | The Page class file where the locator will be added. |
| Login Requirement | ❌ | If the page requires login — User specifies how to login. |

> **Note on Login:** If the page requires login, the User MUST specify how to login (fixture, script, login URL...). The AI MUST NOT read `.env` or guess credentials.

---

## Execution Steps

### Phase 1: Requirement Analysis

1. **Understand the target element** — Clearly identify:
   - Element type: button, input, link, dropdown, dialog, table row, checkbox, radio...
   - Context: Is it in the main page, dialog/modal, sidebar, table, iframe?
   - Action: click, fill, select, hover, verify text, verify visibility?

2. **Identify framework and read corresponding rules:**

   | Framework | Rule file |
   |-----------|-----------|
   | Playwright | `.agent/rules/playwright_rules.md` |
   | Selenium | `.agent/rules/selenium_rules.md` |
   | Appium | `.agent/rules/appium_rules.md` |

3. **Check current Page class (if specified):**
   - Read Page class file → see existing locators.
   - Avoid duplication or naming conflicts.

---

### Phase 2: Inspect Actual DOM / UI Hierarchy

> ⚠️ **UNALTERABLE PRINCIPLE: NEVER GUESS LOCATORS. MUST INSPECT ACTUAL UI.**

#### 2A. Web (Playwright MCP)

4. **Navigate to the page containing the element:**
   ```
   browser_navigate(url=<target_url>)
   ```

5. **Resize viewport (MANDATORY):**
   ```
   browser_resize(width=1920, height=1080)
   ```

6. **Capture DOM:**
   ```
   browser_snapshot()
   ```

7. **Analyze element in snapshot:**
   - Find reference element in the DOM tree.
   - Record all valued attributes: `role`, `aria-label`, `aria-labelledby`, `data-testid`, `data-test`, `data-qa`, `id`, `name`, `placeholder`, `type`, `href`, text content.
   - Record parent context (dialog? table? sidebar? iframe?).

8. **If element is hidden** (dropdown menu, modal, tooltip...):
   - Execute action to open element: `browser_click(ref=<trigger>)`.
   - Re-capture: `browser_snapshot()`.

#### 2B. Mobile (Appium)

4. **Use Appium Inspector** or `page_source` to retrieve UI hierarchy.
5. **Record attributes:** `accessibility-id`, `resource-id`, `content-desc`, `text`, `class`, `bounds`.
6. **If element is in a scroll view** → scroll to the element before inspecting.

---

### Phase 3: Generate Locator by Priority

9. **Apply Master Priority Map:**

   | # | Type | When to Use |
   |---|------|-------------|
   | 1 | Accessibility / Aria | Element has clear `role`, `aria-label`. |
   | 2 | Test attribute | Element has `data-testid`, `data-test`, `data-qa`. |
   | 3 | ID / name | Element has stable `id` or `name` (not auto-generated). |
   | 4 | Framework semantic | Playwright: `getByRole`, `getByLabel`... |
   | 5 | CSS Selector | Use specific attributes, avoid dynamic classes. |
   | 6 | XPath | Final choice — only use relative XPath. |

10. **Generate locator by framework:**

    **Playwright (TypeScript/JavaScript):**
    ```typescript
    // Priority 1: Role-based
    page.getByRole('button', { name: 'Submit' })

    // Priority 2: Test ID
    page.getByTestId('submit-btn')

    // Priority 3: Label / Placeholder
    page.getByLabel('Email')
    page.getByPlaceholder('Enter your password')

    // Priority 4: Text
    page.getByText('Submit')

    // Priority 5: CSS
    page.locator('#submit-button')
    page.locator('[data-testid="submit-btn"]')

    // Priority 6: XPath (Final)
    page.locator('//button[@type="submit"]')
    ```

    **Playwright (Python):**
    ```python
    # Priority 1: Role-based
    page.get_by_role("button", name="Submit")

    # Priority 2: Test ID
    page.get_by_test_id("submit-btn")

    # Priority 3: Label / Placeholder
    page.get_by_label("Email")
    page.get_by_placeholder("Enter your password")

    # Priority 4: Text
    page.get_by_text("Submit")

    # Priority 5: CSS
    page.locator("#submit-button")

    # Priority 6: XPath (Final)
    page.locator("//button[@type='submit']")
    ```

    **Selenium (Java):**
    ```java
    // Priority 1: ID
    driver.findElement(By.id("submit-button"));

    // Priority 2: Test attribute
    driver.findElement(By.cssSelector("[data-testid='submit-btn']"));

    // Priority 3: Name
    driver.findElement(By.name("submit"));

    // Priority 4: CSS Selector
    driver.findElement(By.cssSelector("button.btn-primary[type='submit']"));

    // Priority 5: XPath (Final)
    driver.findElement(By.xpath("//button[@type='submit']"));
    ```

    **Appium (Java):**
    ```java
    // Priority 1: Accessibility ID
    driver.findElement(AppiumBy.accessibilityId("login_button"));

    // Priority 2: Resource ID (Android)
    driver.findElement(AppiumBy.id("com.app:id/login_button"));

    // Priority 3: iOS Predicate
    driver.findElement(AppiumBy.iOSNsPredicateString("label == 'Login'"));

    // Priority 4: Class Chain (iOS)
    driver.findElement(AppiumBy.iOSClassChain("**/XCUIElementTypeButton[`label == 'Login'`]"));

    // Priority 5: XPath (Final)
    driver.findElement(AppiumBy.xpath("//android.widget.Button[@text='Login']"));
    ```

---

### Phase 4: Validate Locator

11. **Verify uniqueness — MUST match exactly 1 element:**

    **Web (Playwright MCP):**
    ```
    browser_evaluate(function="() => document.querySelectorAll('<css_selector>').length")
    ```

    **Selenium:**
    ```java
    List<WebElement> matches = driver.findElements(By.<strategy>("<locator>"));
    assert matches.size() == 1;
    ```

    **Appium:**
    ```java
    List<WebElement> matches = driver.findElements(AppiumBy.<strategy>("<locator>"));
    assert matches.size() == 1;
    ```

12. **Verify visibility** — Element must be interactable:
    - Not overlaid by another element.
    - Not in `hidden`, `display:none`, or `visibility:hidden` state.
    - Not outside the viewport (requires scrolling).

13. **Verify stability — Checklist:**
    - [ ] No dynamic CSS classes (e.g., `css-1n2xyz`, `sc-bdnxRM`).
    - [ ] No absolute XPath (e.g., `//html/body/div[1]/div[2]/button`).
    - [ ] No auto-generated IDs (e.g., `ember123`, `react-select-2-input`).
    - [ ] No `nth-child` / `nth-of-type` if better options exist.
    - [ ] Survives page reload.
    - [ ] Stable across multiple page states (loading, loaded, with data, no data).

---

### Phase 5: Return Results

14. **Output Format — MUST provide all 3 sections:**

```markdown
## Locator Result: [Element description]

**Framework:** [Playwright / Selenium / Appium]

### 🎯 Primary Locator (Recommended)
```<language>
// Locator code — copy-paste ready
```
- **Type:** [Role-based / Test ID / CSS / ...]
- **Unique:** ✅ Matches 1 element
- **Stability:** ✅ No dynamic class / absolute XPath used

### 🔄 Fallback Locator
```<language>
// Backup locator when primary fails
```
- **Type:** [CSS / XPath / ...]
- **When to use:** When primary locator breaks due to DOM changes.

### 💡 Reasoning
- Explain why this Primary locator was chosen.
- Why other candidates were discarded.
- Potential risks (if any).

### 📋 Usage Example (if requested)
```<language>
// Example usage in test code
```
```

15. **(Optional) If User requests addition to Page class:**
    - Add locator in the correct position within the Page class.
    - Name it according to project naming conventions.
    - Create methods using the locator if necessary.

---

## Common Patterns (Reference)

### Scoping locator within Dialog / Modal:
```typescript
// Playwright — scope into dialog first
const dialog = page.getByRole('dialog');
dialog.getByRole('button', { name: 'Confirm' }).click();
```
```java
// Selenium — scope into dialog
WebElement dialog = driver.findElement(By.cssSelector("[role='dialog']"));
dialog.findElement(By.cssSelector("button[data-testid='confirm']")).click();
```

### Dynamic text matching:
```typescript
// Playwright — exact vs partial
page.getByText('Submit', { exact: true })     // exact match
page.getByText(/submit/i)                      // regex, case-insensitive
```
```python
# Playwright Python — normalize-space XPath
page.locator(f"//a[normalize-space()='{text}']")
```

### Table row action:
```typescript
// Playwright — filter row then interact
const row = page.getByRole('row').filter({ hasText: 'John Doe' });
row.getByRole('button', { name: 'Edit' }).click();
```
```java
// Selenium — XPath relative in table
driver.findElement(By.xpath("//tr[contains(., 'John Doe')]//button[text()='Edit']"));
```

---

## FORBIDDEN

| ❌ Forbidden Action | ✅ Correct Alternative |
|-------------------|-----------------|
| Guessing locators without inspecting DOM/UI | Use `browser_snapshot()` or Appium Inspector first. |
| Using dynamic CSS classes (`css-1n2xyz`, `sc-xxx`) | Use role, aria, data-testid, text. |
| Using absolute XPath (`//html/body/div[1]...`) | Use relative XPath with attributes. |
| Using auto-generated IDs (`ember123`, `:r1:`) | Use stable attributes or text. |
| Returning locator without verifying uniqueness | Always verify it matches exactly 1 element. |
| Returning only 1 locator without fallback | Return Primary + Fallback + Reasoning. |
| Reading `.env` for login credentials | Ask User for login method or use available fixture. |

---

## Final Checklist

- [ ] Inspected actual DOM/UI hierarchy (no guessing).
- [ ] Locator follows priority: accessibility > test-id > id/name > semantic > css > xpath.
- [ ] Primary locator matches exactly 1 unique element.
- [ ] Provided Fallback locator.
- [ ] Provided Reasoning for selection.
- [ ] No dynamic classes, absolute XPath, or auto-generated IDs used.
- [ ] Locator is stable across page reloads and multiple states.
- [ ] (If added to Page class) Verified code runs without errors.