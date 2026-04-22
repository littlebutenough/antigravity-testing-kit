---
description: Generate API test cases and automation scripts from Swagger/OpenAPI specification. Supports 2 modes — SPEC (test cases only) and FULL (test cases + automation scripts).
skills:
  - qa_automation_engineer
  - test_data_generator
---

# Workflow: Generate API Tests from Swagger/OpenAPI

> **MANDATORY SKILL:** You MUST load and carefully read the **`qa_automation_engineer`** skill (at `.agent/skills/qa_automation_engineer/SKILL.md`) before starting. Additionally, refer to the **`test_data_generator`** skill to generate standard test data.

This workflow helps the agent analyze Swagger/OpenAPI specifications, identify endpoints, generate structured API test cases, and (depending on the mode) automatically generate complete automation scripts.

## ⚠️ Execution Principles

- **All output in English**
- **DO NOT guess** schemas/endpoints — analyze the actual spec (JSON/YAML).
- **Must wait for user confirmation** of the scope at Step 2 before generating details.
- If the user has not provided a Swagger URL/file → ask before starting.
- ⚠️ **Rule E3:** When a test FAILS → read logs → analyze → fix → re-run. DO NOT ask the user during the fix process.

## 2 Modes

| Mode | When to Use | Output |
|---|---|---|
| **SPEC** (default) | User needs API test cases as documentation | API Test Cases (Markdown) + Test Data Matrix |
| **FULL** | User requests automation scripts as well | Same as SPEC + Automation Scripts + Project Structure |

> If the user says "generate automation", "write API test code", or requests scripts → automatically switch to **FULL Mode**.

## Execution Steps

### Step 1: Reception & Spec Analysis (Parse & Analyze)

1. **Collect Swagger/OpenAPI spec** from the user:
   - **Direct URL** (e.g., `https://api.example.com/swagger.json`) → use `read_url_content` to fetch.
   - **Local file** (JSON/YAML) → use `view_file` to read.
   - **Swagger UI URL** → extract the original spec URL (usually `/v2/api-docs` or `/v3/api-docs`).
   - **Scalar API Reference URL** → inspect HTML to find `data-configuration` containing the spec URL (usually `/swagger/json`, `/reference/json`, or relative path in the `url` attribute). E.g., `https://book.anhtester.com/swagger` → spec at `https://book.anhtester.com/swagger/json`.
   - **Other API Doc formats** (Redoc, Stoplight, RapiDoc) → find the spec URL in page source or network requests.
2. **Parse spec** and extract information:
   - Base URL, API version, authentication scheme (Bearer, API Key, OAuth2, Basic).
   - List of all endpoints: `method + path`
   - Request parameters: path, query, header, body (schema + required fields)
   - Response schemas: status codes, response body structure
   - Models/Definitions: reusable data models
3. **Classify endpoints** into groups:
   - **CRUD operations** — Create, Read, Update, Delete.
   - **Authentication** — Login, Register, Token refresh.
   - **Business Logic** — Complex business processing APIs.
   - **Utility** — Health check, config, metadata.

### Step 2: Scope & Tech Stack Confirmation (CHECKPOINT — ⏸️ STOP)

1. **Present a summary** for user review:
   - Total endpoints detected (grouped).
   - Authentication method.
   - List of endpoint groups + number of APIs per group.
   - Proposed Mode (SPEC or FULL).
2. **Ask the user to confirm:**
   - "Do you want to test all endpoints or just focus on specific groups?"
   - "Do you want the output to be test cases (SPEC) or both test cases and automation scripts (FULL)?"
   - If FULL Mode: "What is your preferred tech stack?" (default according to the table below).
3. **Wait for user confirmation** of the scope before proceeding to Step 3.

**Default Tech Stack (FULL Mode):**

| Framework | Language | When to Use |
|---|---|---|
| **REST Assured** | Java | Default for Java projects, TestNG runner |
| **Playwright API Testing** | TypeScript | When the user uses Playwright or TypeScript stack |
| **Supertest + Jest** | TypeScript/JS | When the user uses Node.js backend |
| **Requests + Pytest** | Python | When the user uses Python stack |

### Step 3: Generate API Test Scenarios & Test Data

1. **For each endpoint** within the confirmed scope, generate test scenarios in 5 categories:
   - **✅ Happy Path** — Valid request, response matches schema + status code.
   - **❌ Negative — Validation** — Missing required fields, wrong data type, exceeds max length.
   - **❌ Negative — Auth** — No token, expired token, token with wrong role.
   - **🔲 Boundary** — Min/max values, empty string, null, special characters.
   - **⚡ Edge Cases** — Concurrent requests, duplicate creation, large payload, unicode/emoji.

2. **For each scenario**, clearly define:
   - **Request:** Method, URL, Headers, Body/Params (specific values).
   - **Expected Response:** Status code, Response body structure, Error message.
   - **Priority:** P1 (Critical) / P2 (High) / P3 (Medium) / P4 (Low).

3. **Generate Test Data Matrix** (using the `test_data_generator` skill):
   - Valid data for Happy Path.
   - Invalid data for Negative cases (one negative set per field).
   - Boundary values according to schema constraints (minLength, maxLength, min, max, pattern).
   - Data must be **unique + traceable** (e.g., `auto_api_1712049200@test.com`).

### Step 4: Package API Test Cases (Output — SPEC Mode)

1. Create the `api_test_cases.md` **artifact** with the following structure:
   - **API Overview** — Base URL, Version, Auth method, Total endpoints.
   - **Endpoint Catalog** — Table: `| # | Method | Path | Description | Number of Test Cases |`.
   - **Detailed Test Cases** — Per endpoint:

   ```
   | TC ID | Endpoint | Scenario | Request | Expected Response | Priority | Category |
   ```

   - **Test Data Matrix** — Valid/invalid/boundary data table for each model.
   - **Dependencies & Execution Order** — Test execution order (e.g., must create user before testing get user).

2. If the user chooses **SPEC Mode** → **END** here.

### Step 5: Generate Automation Scripts (FULL Mode)

> Only perform in **FULL Mode**.

1. **Design project structure** appropriate for the framework:

   **REST Assured (Java):**
   ```
   src/test/java/
   ├── api/                    # API client classes (per resource)
   │   ├── BaseApi.java        # Base config: baseURI, auth, logging
   │   ├── UserApi.java        # Methods: createUser(), getUser(), ...
   │   └── AuthApi.java        # Methods: login(), refreshToken(), ...
   ├── models/                 # POJO/DTO classes (from Swagger models)
   │   ├── UserRequest.java
   │   └── UserResponse.java
   ├── tests/                  # Test classes (TestNG)
   │   ├── UserApiTest.java
   │   └── AuthApiTest.java
   ├── utils/                  # Helpers
   │   ├── TestDataGenerator.java
   │   └── AssertionHelper.java
   └── testdata/               # Test data files (JSON/YAML)
   ```

   **Playwright API (TypeScript):**
   ```
   tests/
   ├── api/
   │   ├── helpers/
   │   │   ├── base-api.ts     # Base request context, auth
   │   │   ├── user-api.ts     # API methods per resource
   │   │   └── test-data.ts    # Data generators
   │   ├── user.api.spec.ts    # Test file per resource
   │   └── auth.api.spec.ts
   └── fixtures/
       └── api-fixtures.ts     # Shared fixtures (auth tokens, etc.)
   ```

2. **Generate code** in order:
   - **Base API class** — Config baseURL, default headers, auth interceptor, request/response logging.
   - **Model/DTO classes** — From Swagger definitions/schemas.
   - **API client classes** — Methods for each endpoint (return typed response).
   - **Test Data generators** — Data factory with random + traceable values.
   - **Test classes** — Call API client, assert status code + response body + schema.

3. **Mandatory assertions** for each test:
   - ✅ HTTP Status Code (exact match).
   - ✅ Response body — key fields matching expected values.
   - ✅ Response time < threshold (if SLA exists).
   - ✅ Response schema validation (correct structure).
   - ✅ Accurate error message for negative cases.
   - ✅ Headers check (Content-Type, CORS if relevant).

4. **Code Best Practices:**
   - Use **Builder pattern or Factory** for test data.
   - **Chain requests** for data setup (e.g., create → get → update → delete).
   - **Soft assertions** for checking multiple fields simultaneously.
   - **Parameterized tests** for data-driven scenarios.
   - **Cleanup/teardown** — delete created test data after completion.
   - Do not hardcode tokens/credentials — read from env or config.

### Step 6: Testing & Auto-Heal (Execution & Auto-Heal)

> Only perform in **FULL Mode**.

1. **Run test** using `run_command`:
   - REST Assured: `mvn test -Dtest=<TestClass>`
   - Playwright: `npx playwright test tests/api/`
   - Pytest: `python -m pytest tests/api/`

2. **Monitor** results via `command_status`:
   - If **PASS** → update artifact, report results.
   - If **FAIL** → apply Auto-Heal loop:

   ```
   WHILE test FAIL:
     1. Read error log → identify root cause.
     2. Classify error:
        - Schema mismatch → update model/assertion.
        - Auth failure → check token flow.
        - 404/405 → re-check endpoint path/method.
        - Timeout → increase timeout or check server.
        - Data conflict → replace with new unique test data.
     3. Fix code using replace_file_content or multi_replace_file_content.
     4. Re-run test.
     5. Repeat until PASS (max 5 cycles).
   ```

3. **⚠️ Rule E3:** DO NOT ask the user during the fix process. Only ask if:
   - Server API does not respond (down/blocked).
   - Business rule contradicts the spec.
   - Still failing after 5 auto-heal cycles.

## Common Error Handling

| Error | Cause | How to Handle |
|---|---|---|
| Swagger URL returns HTML | URL is Swagger UI, not the spec | Find original spec URL (/v2/api-docs, /v3/api-docs, /swagger.json). |
| Scalar URL returns HTML | URL is Scalar API Reference UI | Inspect HTML for data-configuration → get url field → append to base URL. Try: /swagger/json, /reference/json. |
| Spec empty or cannot be parsed | Corrupt file or non-standard format | Ask user to re-provide, try converting YAML ↔ JSON. |
| Unknown auth endpoint | Spec doesn't document auth flow | Ask user about auth method and how to obtain token. |
| Response schema differs from spec | Actual API doesn't match document | Note as known issue, adjust test per actual response. |
| Rate limiting | API has request-per-minute limit | Add delay between tests or use retry logic. |

## Output

### SPEC Mode
- Artifact `api_test_cases.md`:
  - API Overview (Base URL, Version, Auth).
  - Endpoint Catalog (summary table).
  - Detailed Test Cases (grouped by endpoint).
  - Test Data Matrix.
  - Execution Order & Dependencies.

### FULL Mode
- All output from SPEC Mode, plus:
  - Project structure (framework-appropriate).
  - Base API class + Auth helper.
  - Model/DTO classes.
  - API client classes (per resource).
  - Test classes with full assertions.
  - Test data generators.
  - Test run results (PASS/FAIL report).