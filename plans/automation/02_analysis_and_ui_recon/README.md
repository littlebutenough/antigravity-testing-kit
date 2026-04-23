# Step 2: Analysis & UI Investigation (Analysis & UI Recon)

**Workflow:** `/generate_automation_from_testcases` (continued)
**Skill:** `qa_automation_engineer` + `ui_debug_agent`

---

## Purpose

Instead of humans manually inspecting DOM, this step delegates the task to AI to **automatically open the browser**, navigate the web, and extract real locators from DOM. This is the step that distinguishes quality between "guessing" automation and "real inspection" automation.

## Usage

1. Fill in URL, test account, and Test Cases in the `prompt.txt` file.
2. Send to AI — AI will use Playwright/Selenium MCP to:
   - Automatically launch browser
   - Navigate according to test steps
   - Collect locators from real DOM
3. Review the locator table returned by AI.
4. If any locator is not correct → request AI to re-inspect that element.
5. Confirm → proceed to Step 3.

## Important Notes

- AI will use **Accessibility Tree** and **DOM inspection** to find locators — no guessing.
- If login is required, provide test account in the prompt.
- Default viewport **1920x1080** (desktop) according to rules.
- AI prioritizes locators according to the order in `.agent/rules/locator_strategy.md`.