---
description: Generate structured, unique, and traceable test data for test cases. Supports UI forms, API payloads, and data-driven tests.
skills:
  - test_data_generator
  - qa_automation_engineer
---

# /generate_test_data — Generate Structured Test Data

> The user provides the feature/module or test cases needing data generation.
> The AI analyzes fields and constraints, generating a complete data set (positive, negative, boundary, edge cases) in a ready-to-use format.

> **MANDATORY:** Before starting, you MUST load and carefully read:
> - **Skill:** `.agent/skills/test_data_generator/SKILL.md` — Data generation rules.
> - **Rule:** `.agent/rules/automation_rules.md` — Test Data section.

---

## Required User Input

| Input | Mandatory | Description |
|-------|----------|-------|
| Feature / Module | ✅ | e.g., "Registration Form", "API Create User", "Login Page". |
| Fields requiring data | ⚠️ Recommended | List of fields + constraints. If absent, AI analyzes from DOM/Spec. |
| Page URL / Swagger spec | ❌ | If provided, AI inspects DOM/Spec for accurate validation rules. |
| Test cases | ❌ | If existing, AI generates data matching each TC. |
| Output format | ❌ | `json` (default), `csv`, `markdown table`, `code` (TypeScript/Java/Python). |
| Data Language | ❌ | English (default), or as per context. |

---

## Execution Steps

### Step 1: Analyze Fields & Constraints

1. **Identify Information Sources:**

   | Source | How to Obtain | Priority |
   |-------|----------|---------|
   | User-provided | Read from input | ⭐ Highest |
   | Actual DOM (UI form) | `browser_navigate` → `browser_snapshot` → analyze input fields | ⭐ High |
   | Swagger/OpenAPI spec | `read_url_content` → parse schema + constraints | ⭐ High |
   | Existing Test cases | Read test steps → extract fields | Medium |
   | Domain-based guess | Based on domain experience | ⭐ Lowest |

2. **For each field, define:**

   | Attribute | Example |
   |-----------|-------|
   | **Field Name** | email, password, name, phone |
   | **Data Type** | string, number, boolean, date, file, enum |
   | **Required?** | Mandatory or optional |
   | **Validation rules** | min/maxLength, pattern/regex, format (email, phone), unique |
   | **Default Value** | If any |
   | **Enum values** | e.g., status = ["AVAILABLE", "UNAVAILABLE"] |
   | **Dependencies** | e.g., `password_confirm` must match `password` |

3. **Present Fields table for User confirmation (CHECKPOINT ⏸️):**

   ```markdown
   | # | Field | Type | Required | Constraints | Notes |
   |---|-------|------|----------|-------------|-------|
   | 1 | email | string | ✅ | format: email, unique | Used for login |
   | 2 | password | string | ✅ | minLength: 6 | |
   | 3 | name | string | ✅ | maxLength: 100 | |
   | 4 | phone | string | ❌ | pattern: /^0[0-9]{9}$/ | 10 digits, starts with 0 |
   ```

   > Wait for User confirmation or additional constraints before proceeding to Step 2.

---

### Step 2: Generate Test Data across 4 Categories

For each identified field, generate data according to the following:

#### 2A. Positive Data (Happy Path)

- Valid values, correct format, within constraints.
- All required fields are filled.
- Data must be **unique + traceable**.

**Format unique data:**
```
<prefix>_<testName>_<timestamp>
```
Example:
```
email:    auto_register_1712049200@test.com
username: user_login_1712049200
code:     TC_BOOK_1712049200
```

#### 2B. Negative Data

| Type | Description | Example |
|------|--------|-------|
| **Missing required** | Empty mandatory field | `email: ""` |
| **Invalid format** | Incorrect format | `email: "not-an-email"` |
| **Invalid type** | Incorrect data type | `price: "abc"` (expect number) |
| **Duplicate** | Existing value | `email: "existing@test.com"` |
| **Invalid characters** | Forbidden characters | `name: "<script>alert(1)</script>"` |
| **Wrong relationship** | Breaks field relationship | `password_confirm ≠ password` |

#### 2C. Boundary Values

| Type | Description | Example (minLength=6, maxLength=20) |
|------|--------|-------|
| **Min** | Exactly min | `"abcdef"` (6 chars) |
| **Min - 1** | Below min | `"abcde"` (5 chars) |
| **Max** | Exactly max | `"a" * 20` (20 chars) |
| **Max + 1** | Above max | `"a" * 21` (21 chars) |
| **Empty** | Empty string | `""` |
| **Zero** | Zero (for numeric fields) | `0` |
| **Negative** | Negative (for numeric fields) | `-1` |

#### 2D. Edge Cases

| Type | Description | Example |
|------|--------|-------|
| **Unicode** | Special Unicode characters | `"Nguyễn Văn 🎉"` |
| **Very long** | Extremely long string | `"a" * 10000` |
| **Whitespace** | Leading/trailing whitespace | `"  email@test.com  "` |
| **SQL injection** | SQL injection patterns | `"'; DROP TABLE users; --"` |
| **HTML tags** | HTML in text fields | `"<b>bold</b><img src=x onerror=alert(1)>"` |
| **Null/undefined** | Null values | `null` |
| **Special numbers** | Special numbers | `0.1 + 0.2`, `Number.MAX_SAFE_INTEGER`, `NaN` |
| **Date edge** | Special dates | `"2024-02-29"` (leap year), `"2024-12-31"`, `"1970-01-01"` |

---

### Step 3: Packaging Output

Return results in the user's requested format (default: JSON):

#### JSON Format (default)

```json
{
  "module": "User Registration",
  "totalDataSets": 15,
  "fields": ["email", "password", "name", "phone"],
  "positive": [
    {
      "id": "POS_01",
      "description": "Successful registration with all valid fields",
      "data": {
        "email": "auto_register_1712049200@test.com",
        "password": "Test@12345",
        "name": "Auto User Register",
        "phone": "0912345001"
      },
      "expectedResult": "Account created successfully, status 201"
    }
  ],
  "negative": [
    {
      "id": "NEG_01",
      "description": "Empty email",
      "data": { "email": "", "password": "Test@12345", "name": "Test User" },
      "expectedResult": "Validation error: Email is required",
      "targetField": "email",
      "negativeType": "missing_required"
    },
    {
      "id": "NEG_02",
      "description": "Incorrect email format",
      "data": { "email": "not-email", "password": "Test@12345", "name": "Test User" },
      "expectedResult": "Validation error: Invalid email format",
      "targetField": "email",
      "negativeType": "invalid_format"
    }
  ],
  "boundary": [
    {
      "id": "BND_01",
      "description": "Password at min length (6 chars)",
      "data": { "email": "auto_bnd01_1712049200@test.com", "password": "Abc@12", "name": "Test User" },
      "expectedResult": "Success",
      "targetField": "password",
      "boundaryType": "min"
    }
  ],
  "edgeCases": [
    {
      "id": "EDGE_01",
      "description": "Name contains Vietnamese Unicode + emoji",
      "data": { "email": "auto_edge01_1712049200@test.com", "password": "Test@12345", "name": "Nguyễn Văn 🎉" },
      "expectedResult": "Success — system accepts Unicode",
      "targetField": "name",
      "edgeType": "unicode"
    }
  ]
}
```

#### Markdown Table Format

```markdown
| ID | Category | Description | email | password | name | Expected Result |
|----|----------|-------------|-------|----------|------|-----------------|
| POS_01 | Positive | Successful registration | auto_reg@test.com | Test@12345 | Auto User | 201 Created |
| NEG_01 | Negative | Empty email | (empty) | Test@12345 | Test User | 422: Email is required |
| BND_01 | Boundary | Password min length | auto_bnd@test.com | Abc@12 | Test User | 201 Created |
```

#### Code Format (TypeScript example)

```typescript
// test-data/registration.data.ts
export const registrationData = {
  positive: {
    email: `auto_register_${Date.now()}@test.com`,
    password: 'Test@12345',
    name: 'Auto User Register',
    phone: '0912345001',
  },
  negative: {
    emptyEmail: { email: '', password: 'Test@12345', name: 'Test' },
    invalidEmail: { email: 'not-email', password: 'Test@12345', name: 'Test' },
    shortPassword: { email: `auto_neg_${Date.now()}@test.com`, password: '123', name: 'Test' },
  },
  boundary: {
    minPassword: { email: `auto_bnd_${Date.now()}@test.com`, password: 'Abc@12', name: 'Test' },
    maxName: { email: `auto_bnd_${Date.now()}@test.com`, password: 'Test@12345', name: 'A'.repeat(100) },
  },
};
```

---

## Data Rules (MANDATORY)

| # | Rule | Description |
|---|------|-------|
| 1 | **Unique** | No duplication in the test suite — use timestamp/random. |
| 2 | **Traceable** | Able to trace back to the test that generated the data — use prefix + test name. |
| 3 | **No real PII** | DO NOT use real personal identifiable information (SSN, real email, real phone). |
| 4 | **Respect constraints** | Data must adhere to the analyzed validation rules. |
| 5 | **Include expectedResult** | Each data set MUST include an expected result for assertions. |
| 6 | **Deterministic when needed** | Same seed → same data (for reproducible tests). |

---

## FORBIDDEN

| ❌ Forbidden Action | ✅ Correct Alternative |
|-------------------|-----------------| 
| Using placeholders (valid email, valid code) | Specific values: `auto_tc01@test.com`, `KH-2026-0012`. |
| Hardcoding duplicate data across tests | Random data with prefix + timestamp. |
| Using real PII | Synthetic data: `auto_*@test.com`. |
| Generating only positive data | Mandatory inclusion of all 4 categories: Positive + Negative + Boundary + Edge. |
| Generating data without an expected result | Each data set MUST specify an expected result. |
| Guessing validation rules without verification | Inspect DOM/Spec or ask the User. |
| Reading .env for credentials | Ask the User or use a placeholder `[FROM_ENV]`. |

---

## Final Checklist

- [ ] Analyzed all fields + constraints fully.
- [ ] User confirmed the fields table before data generation.
- [ ] Data covers all 4 categories (Positive, Negative, Boundary, Edge Cases).
- [ ] Each data set has a clear expected result.
- [ ] Data is unique + traceable (prefix + timestamp/random).
- [ ] Contains no real PII.
- [ ] Output format matches User's requirements (JSON/CSV/Markdown/Code).
- [ ] Boundary values align with constraints (min, min-1, max, max+1).