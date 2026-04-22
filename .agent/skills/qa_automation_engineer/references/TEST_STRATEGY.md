# Test Strategy

## Usage Instructions

This file defines the testing strategy for the project. The Agent refers to this file to understand the scope, priorities, and approach when generating test cases or automation scripts.

> ⚠️ **You need to update this file** for each specific project. Below is the template.

---

## Testing Objectives

- Ensure software quality across all test levels.
- Detect bugs early in the development lifecycle.
- Maintain a stable regression suite for CI/CD.

## Scope of Testing

| Test Type | Applied | Tool/Framework |
|-----------|---------|----------------|
| UI Functional Testing | ✅ | Selenium / Playwright |
| API Testing | ✅ | REST Assured / Postman |
| Unit Testing | ✅ | JUnit / TestNG |
| Integration Testing | ✅ | TestNG + REST Assured |
| Performance Testing | ⬜ | JMeter / k6 |
| Security Testing | ⬜ | OWASP ZAP |
| Mobile Testing | ⬜ | Appium |

## Test Automation Strategy

### Framework Architecture
- **Design Pattern:** Page Object Model (POM)
- **Language:** Java
- **Test Runner:** TestNG
- **Build Tool:** Maven
- **Reporting:** Allure / ExtentReports

### Automation Scope
- Smoke tests: Cover the happy path of core functionalities.
- Regression tests: Cover all previously passed test cases.
- Data-driven tests: Use external data sources (Excel, CSV, JSON).

## Test Data Management

- Use random data with prefix + timestamp for traceability.
- Separate test data from test logic.
- Do not hard-code credentials in the code.

## Execution Plan

| Phase | Description | Trigger |
|-------|-------------|---------|
| Smoke Test | Core happy path | Every build |
| Regression | Full suite | Before release |
| Integration | API + UI | Daily |

## Test Environment

- Tests run on the Staging environment.
- CI/CD pipeline runs in headless mode.
- Local debug runs in headed mode (viewport 1920x1080).
