# 📋 Quick Start Guide: Automation Testing 6 Steps

## Two Usage Methods

### Method 1: One-Click (Fully Automated — Recommended)

```
/generate_automation_from_testcases

URL: [https://your-app.com/login]
Account: [admin@test.com / Test@123]
Framework: [Playwright TypeScript / Selenium Java]

Manual Test Cases:
[Paste test cases here]
```

→ Agent automatically runs all 6 steps, auto-fixes until test PASS.

---

### Method 2: Sequential (Step-by-Step)

| Step | Prompt to send to Antigravity | Wait for User? |
|------|-------------------------------|----------------|
| **0** | View `project_architecture/README.md` | Setup once |
| **1** | Copy `01.../prompt.txt` + fill in `[...]` | ✅ Wait for confirmation |
| **2** | Copy `02.../prompt.txt` + fill in URL & TCs | ✅ Review locators |
| **3** | Copy `03.../prompt.txt` | Review POM |
| **4** | Copy `04.../prompt.txt` | Quick review |
| **5** | Copy `05.../prompt.txt` | Wait for test PASS |
| **6** | Copy `06.../prompt.txt` | Receive clean code |

### Execution Flow:

```
[Step 1] Establish role + tech stack
    ↓  AI confirms → OK
[Step 2] Provide URL + Test Cases
    ↓  AI opens browser, collects locators
    ↓  ⏸️ User reviews locator table
[Step 3] AI designs POM classes
    ↓  User reviews architecture
[Step 4] AI generates Data Generator class
    ↓  User quick review
[Step 5] AI generates test script + auto-runs
    ↓  Auto-fix loop until PASS ✅
[Step 6] AI cleans up code
    ↓  User receives clean code → commit ✅
```

---

## Optimization Tips

1. **Use Method 1** for 80% of cases — fastest and most efficient
2. **Use Method 2** when project is large or need detailed control over each module
3. **Always run in the same conversation** so AI maintains context
4. **Provide test account** if app requires login