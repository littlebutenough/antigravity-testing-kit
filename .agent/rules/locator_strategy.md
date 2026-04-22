# Locator Selection Strategy (Applicable to All Frameworks)

> The stability and readability of locators determine the health of an automation framework.
> Core Principle: NEVER select elements based on DOM structure tied to styling. Build locators based on semantic attributes.

## 1. Master Priority Map

Priority order from highest to lowest:

1. Accessibility / Aria attributes (semantic, supports screen readers)
2. Dedicated test attributes (`data-testid`, `data-test`, `data-qa`)
3. Primary identification attributes (`id`, `resource-id`, `name`)
4. Framework-specific semantic functions (Playwright: `getByRole`, `getByLabel`...)
5. CSS Selector
6. XPath (last resort)

## 2. Stability Rules

Every locator must ensure:
- Matches **exactly 1** unique element on the page (unique in scope).
- Survives UI changes — not affected when DOM layout changes (adding/removing div wrappers, changing flexbox).

**FORBIDDEN to use:**
- Dynamic CSS class names / temporary hashes (e.g., `css-1n2xyz-btn`)
- `nth-child`, `nth-of-type` strings when better options are available
- Auto-generated IDs by frameworks
- Absolute XPath based on position (e.g., `//div[3]/div[2]/form/button`)

## 3. Locator Verification Process

Before adding a locator to the code, verify:

1. Does the locator match **exactly 1 element** in the DOM?
2. Is the matched element the interactive component? (not a shadow DOM overlay)
3. Reload / navigate the page — is the locator still correct?
4. Test on multiple page states (loading, loaded, with data, without data) — is the locator stable?

## 4. Framework-Specific Locators

For detailed locators for each framework, see:
- Playwright: `.agent/rules/playwright_rules.md` (Section 3)
- Selenium: `.agent/rules/selenium_rules.md` (Section 1)
- Appium: `.agent/rules/appium_rules.md` (Section 1)
