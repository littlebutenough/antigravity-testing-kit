---
name: UI Debug Agent
description: Skill for inspecting web/mobile applications using browser tools, analyzing DOM elements, determining stable locators, debugging UI automation failures, and supporting the generation of Page Object classes.
---

# UI Debug Agent

## Description

A specialized skill that helps the agent inspect web/mobile applications directly in a real browser, analyze the DOM, collect stable locators, and debug UI automation issues.

The agent can:

- Open a real browser and navigate to any URL.
- Inspect DOM elements — identify attributes, hierarchy, and state.
- Collect stable locators for Playwright, Selenium, and Appium.
- Debug automation failures (element not found, click intercepted, timeout).
- Capture UI state (snapshot, screenshot) for analysis.
- Analyze dynamic content, iframes, shadow DOM, and SPA navigation.

---

## When to Use

Use this skill when:

- You need to **explore the UI** of a new website/module.
- You need to **find a locator** for a specific element.
- You need to **debug** test automation failure due to UI changes.
- You need to **verify** if a locator works on the actual DOM.
- You need to **analyze the DOM** to understand UI structure (forms, tables, modals).
- You need to **capture evidence** (screenshots) for a test report.

Trigger keywords: "inspect UI", "find locator", "debug element", "open browser to view", "check DOM"

---

## MCP Command Sequence (MANDATORY)

When using Playwright MCP to debug UI, **ALWAYS** follow this order:

```
1. browser_navigate(url)           → Open page
2. browser_resize(1920, 1080)      → Desktop viewport
3. browser_wait_for(text/time)     → Wait for page load
4. browser_snapshot()              → Collect DOM (for analysis + finding locators)
5. browser_click/type/hover(ref)   → Interact (if needed)
6. browser_take_screenshot()       → Take photo (evidence for failure or milestone)
```

### Important Rules:

| Rule | Details |
|---|---|
| **DO NOT navigate again** if already on the correct page | Avoid unintentional reloads. |
| **ALWAYS resize** immediately after navigate | `browser_resize(1920, 1080)` — ensures desktop viewport. |
| **ALWAYS wait** before snapshot | Wait for page load completion. |
| **Use snapshot for analysis** | Snapshot returns an accessibility tree — fast, accurate, with `ref` for interaction. |
| **Use screenshot for reporting** | Screenshot is an image — use when visual evidence is needed. |

---

## Snapshot vs Screenshot

| | `browser_snapshot` | `browser_take_screenshot` |
|---|---|---|
| **Returns** | Accessibility tree (text + ref IDs) | Image (PNG/JPEG) |
| **Purpose** | Analyze DOM, find locators, identify elements | Visual evidence, reporting, debug layout |
| **When to use** | ⭐ Always use before interacting | Only upon failure or important milestones |
| **Has ref to interact** | ✅ Yes — use ref to click/type/hover | ❌ No — image only |
| **Speed** | Fast | Slower |

**Rule:** Prioritize `snapshot` for analysis, use `screenshot` for evidence.

---

## UI Inspection Process

### 1. Open & Prepare the Page

```
browser_navigate → target URL
browser_resize → 1920 × 1080
browser_wait_for → wait for page load indicator (text or time)
```

If the page requires login:
- Ask for user credentials OR use existing fixtures in the project.
- **DO NOT read `.env` files directly** (security rule).

### 2. Collect DOM Structure

```
browser_snapshot → accessibility tree
```

From the snapshot, identify:
- **Key elements:** buttons, inputs, links, headings, tables.
- **Important attributes:** role, name, label, placeholder, testid.
- **Hierarchy:** parent → child relationships.
- **State:** visible, enabled, disabled, checked, expanded.

### 3. Identify Locators

For each element requiring a locator, apply the **priority order** according to the framework:

**Playwright:**

| Priority | Locator | Example | When to use |
|---|---|---|---|
| 1 ⭐ | `getByRole()` | `getByRole('button', {name: 'Submit'})` | Element with clear role + accessible name |
| 2 | `getByLabel()` | `getByLabel('Email')` | Form input with a label |
| 3 | `getByPlaceholder()` | `getByPlaceholder('Enter email')` | Input with placeholder, no label |
| 4 | `getByText()` | `getByText('Welcome back')` | Unique text content |
| 5 | `getByTestId()` | `getByTestId('submit-btn')` | Element with data-testid attribute |
| 6 | CSS | `page.locator('.submit-button')` | No suitable semantic option |
| 7 | XPath | `page.locator('//div[@class="x"]')` | Last resort — avoid |

**Selenium:**

| Priority | Locator | Example |
|---|---|---|
| 1 ⭐ | `By.id()` | `By.id("email")` |
| 2 | `By.cssSelector("[data-testid]")` | `By.cssSelector("[data-testid='submit']")` |
| 3 | `By.name()` | `By.name("username")` |
| 4 | `By.cssSelector()` | `By.cssSelector(".login-form button")` |
| 5 | `By.xpath()` | `By.xpath("//button[text()='Login']")` |

**Appium (Mobile):**

| Priority | Locator | Example |
|---|---|---|
| 1 ⭐ | Accessibility ID | `MobileBy.accessibilityId("loginButton")` |
| 2 | ID (resource-id) | `MobileBy.id("com.app:id/login_btn")` |
| 3 | Name | `MobileBy.name("Login")` |
| 4 | XPath (relative) | `MobileBy.xpath("//android.widget.Button[@text='Login']")` |

### 4. Verify Locator

After identifying the locator, **verification is mandatory** on the actual DOM:

```
browser_snapshot → find element by ref
browser_click/type(ref) → try interacting
browser_snapshot → confirm results
```

**Locator is accepted when:**
- [ ] Unique on the page (matches exactly 1 element).
- [ ] Stable across multiple reloads.
- [ ] Does not contain dynamic classes (css-xxx, sc-xxx, MuiXxx-root).
- [ ] Does not contain positional xpath (//div[3]/button[2]).
- [ ] Does not depend on auto-generated attributes.

---

## Special Situation Handling

### Page Requires Login
- Use an existing login fixture in the project or ask for user credentials.
- **DO NOT read .env directly**.
- After logging in, navigate to the page to be inspected.

### Modal / Dialog / Popup
- Modals are usually overlays on the main page.
- `browser_snapshot` will show modal content in the accessibility tree.
- Interact with modal elements using the ref from the snapshot.
- Wait for modal animation to complete before interacting.

### Iframe
- `browser_snapshot` might not see content inside an iframe.
- Use `browser_evaluate` to switch into the iframe:
  ```javascript
  () => document.querySelector('iframe').contentDocument.body.innerHTML
  ```
- Or use Playwright's frame locator: `page.frameLocator('#iframe-id')`.

### Shadow DOM
- Playwright `locator()` automatically pierces the shadow DOM.
- Selenium requires `shadowRoot.findElement()`.
- `browser_snapshot` may see shadow DOM content depending on the MCP version.

### Dynamic Content (SPA / AJAX)
- Wait for content to load using `browser_wait_for(text)` before taking a snapshot.
- If content loads lazily → scroll down first, then snapshot.
- If content changes over time → take multiple snapshots.

### Tables / Lists (Multiple Repeated Elements)
- Determine the locator pattern for rows/cells.
- Use `nth()` or `filter()` to target specific elements.
- Playwright example: `page.getByRole('row').filter({hasText: 'John'}).getByRole('button', {name: 'Edit'})`.

### Obscured Element (Overlay / Toast)
- Check z-index, opacity, and visibility in the DOM.
- Wait for overlay to disappear: `browser_wait_for(textGone: 'Loading...')`.
- If a toast notification covers a button → wait for the toast to timeout.

---

## Anti-Patterns (FORBIDDEN)

| ❌ Incorrect | ✅ Correct | Reason |
|---|---|---|
| Guessing locator from feature name | Inspect actual DOM before taking locator | Locator 100% accurate |
| Using screenshot to choose locator | Use snapshot (accessibility tree) | Snapshot has ref, screenshot doesn't |
| Copying locator from old code without verify | Always verify locator on current browser | DOM might have changed |
| Using dynamic class `.css-1abc` | Use role/label/testid | Dynamic class changes every build |
| Using positional xpath `//div[3]` | Use relative xpath or CSS | Positional xpath is fragile |
| Continuous screenshots | Only screenshot on fail or milestone | Resource intensive, slow |
| Re-navigating when already on correct page | Only navigate when URL change is needed | Avoid unnecessary reload |

---

## Output

This skill can return:

- **Locator recommendations** — primary + fallback tables for each element.
- **DOM analysis** — element structure, attributes, state, and hierarchy.
- **Page Object suggestions** — suitable class structure for the inspected page.
- **Screenshots** — visual evidence at milestones.
- **Debug findings** — cause of element not found / click fail + fix.

---

## Rules References

The agent MUST comply with detailed rules:

- `.agent/rules/locator_strategy.md` — Master locator priority map
- `.agent/rules/playwright_rules.md` — Playwright browser setup and locator rules
- `.agent/rules/selenium_rules.md` — Selenium locator and wait rules
- `.agent/rules/appium_rules.md` — Appium mobile locator rules
- `.agent/rules/automation_rules.md` — General automation best practices