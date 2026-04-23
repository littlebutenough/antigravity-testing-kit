# Step 5: Automation Script Generation (Script Generation)

**Workflow:** `/generate_automation_from_testcases` (continued)
**Skill:** `qa_automation_engineer`

---

## Purpose

The core step — AI combines POM (Step 3) and Data Strategy (Step 4) to generate complete Test script files, run tests, and auto-fix if they fail.

## Usage

1. Send the `prompt.txt` file to AI.
2. AI will:
   - Generate complete Test script files
   - **Automatically run tests** using terminal
   - If fail → **automatically read logs, auto-fix, auto-rerun**
   - Loop until stable PASS
3. When test PASS → proceed to Step 6.

## Code Pattern: Arrange → Act → Assert

```
Arrange: Setup data, initialize pages
Act:     Perform actions
Assert:  Verify results
```

## Self-fix Loop (Auto-fix)

AI executes the loop:
```
Generate code → Run test → FAIL?
    ├── Read error log
    ├── Analyze root cause
    ├── Fix code
    └── Rerun → PASS? → DONE ✅
```

## Notes

- AI will **not ask user** during self-fix process (unless encountering conflicting business rules).
- Smart Waits mandatory — see `.agent/rules/playwright_rules.md` or `.agent/rules/selenium_rules.md`.
- Each test case must have **clear assertion** at the end.