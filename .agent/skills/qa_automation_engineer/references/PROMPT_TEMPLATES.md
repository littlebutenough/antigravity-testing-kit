# Prompt Templates

Reusable prompt templates for common QA automation tasks. The Agent can refer to these when formatting output or when the user provides unclear requests.

---

## 1. Test Case Generation

```
Analyze the following requirement and generate test cases:

**Requirement:** [Requirement description]

**Output format:**
| TC ID | Test Case Title | Precondition | Steps | Expected Result | Priority | Type |

**Requirements:**
- Include positive, negative, boundary, and edge cases
- Use English for descriptions
- Priority: High / Medium / Low
- Type: Positive / Negative / Boundary / Edge
```

---

## 2. Automation Script Generation

```
Convert the following test case into an automation script:

**Test Case:** [TC content]
**Framework:** [Selenium Java / Playwright TypeScript]
**Pattern:** Page Object Model

**Output:**
1. Page Object class(es)
2. Test class
3. Test data (if needed)

**Rules:**
- Smart waits only (no hard sleep)
- Random test data with prefix + timestamp
- Clear assertions
```

---

## 3. API Test Generation

```
Generate API tests from Swagger specification:

**Swagger URL:** [URL]
**Endpoint(s):** [Endpoint to test]
**Framework:** REST Assured + TestNG

**Include:**
- Happy path (200 OK)
- Validation errors (400)
- Authentication (401/403)
- Not found (404)
- Boundary values
- Schema validation
```

---

## 4. Locator Generation

```
Inspect element and generate stable locators:

**Element:** [Description of the element to find]
**Page URL:** [URL]
**Tool:** [Selenium / Playwright]

**Output:**
- Primary locator
- Fallback locator
- Reasoning for choosing the locator
```

---

## 5. Flaky Test Analysis

```
Analyze flaky test and propose fixes:

**Test file:** [Path to test]
**Symptom:** [Description of flaky behavior]

**Analysis:**
1. Root cause
2. Detection pattern (timing, data, environment, selector)
3. Specific fix proposal
4. Code fix
```

---

## 6. Test Data Generation

```
Generate test data for module:

**Module:** [Module name]
**Fields:** [List of fields requiring data]

**Include:**
- Valid data (happy path)
- Invalid data (negative)
- Boundary values (min, max, empty, null)
- Special characters
- Format: JSON / CSV / Excel
```
