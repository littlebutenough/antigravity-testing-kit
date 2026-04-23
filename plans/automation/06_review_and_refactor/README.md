# Step 6: Review & Optimization (Review & Refactoring)

**Workflow:** `/generate_automation_from_testcases` (continued)
**Skill:** `qa_automation_engineer`

---

## Purpose

After test runs PASS, "clean up" code to achieve **Clean Code** standard before merging into repository. Avoid dirty code polluting the project.

## Usage

1. Send the `prompt.txt` file after test has PASSED in Step 5.
2. AI will perform:
   - Delete debug logs, commented code, unused variables
   - Review code quality
   - Attach CI/CD tags (smoke, regression...)
3. Receive final code → commit/merge.

## Review Checklist

| # | Check | Status |
|---|---|---|
| 1 | No more `console.log` / `System.out.println` | ☐ |
| 2 | No more commented code | ☐ |
| 3 | No unused variables/locators | ☐ |
| 4 | No hard sleep | ☐ |
| 5 | Assertions complete and accurate | ☐ |
| 6 | Test data unique, traceable | ☐ |
| 7 | Each test case independent | ☐ |
| 8 | CI/CD tags attached | ☐ |

## Notes

- This step can run automatically in One-Click workflow (AI auto-cleans up after test PASS).
- If running manually, paste source code for AI to review.
- Code must meet standards in `.agent/rules/automation_rules.md` before commit.