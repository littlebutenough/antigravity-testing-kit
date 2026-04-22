---
description: Push automation test results (Playwright/JUnit/Allure) to Xray Test Management.
skills:
  - jira_integration
---

# Workflow: Import Test Results to Xray

> **MANDATORY SKILL:** You MUST load and carefully read the **`jira_integration`** skill (at `.agent/skills/jira_integration/SKILL.md`) to understand how to use the scripts before starting.

This workflow helps push automation test results to Xray (Cloud or Server/Data Center) for tracking in Jira.

## Execution Steps:

1. **Check Prerequisites:**
   - Read the `jira_integration` skill to understand the script structure.
   - Verify that the `.env` file has Xray credentials configured:
     - **Xray Cloud**: `XRAY_CLIENT_ID`, `XRAY_CLIENT_SECRET`
     - **Xray Server**: `JIRA_PAT` or `JIRA_EMAIL` + `JIRA_API_TOKEN`
   - Check `XRAY_PLATFORM` (cloud/server) in `.env`.
   - If dependencies aren't installed: `cd scripts/integrations && npm install`.

2. **Verify Xray authentication:**
   ```bash
   node scripts/integrations/jira/xray_auth.js --verify
   ```
   - If failed: check credentials in `.env`.

3. **Identify Report for Import:**
   - Ask the user for the report type:
     - **Playwright JSON**: `--format playwright --file <path-to-results.json>`
     - **JUnit XML**: `--format junit --file <path-to-junit.xml>`
     - **Xray JSON** (already converted): `--format xray --file <path-to-xray.json>`
   - Confirm **Project Key** (from `.env` or `--project`).

4. **Execute Import:**
   ```bash
   # Playwright report
   node scripts/integrations/jira/xray_importer.js --format playwright --file ./test-results.json --project PROJ

   # JUnit XML
   node scripts/integrations/jira/xray_importer.js --format junit --file ./junit-results.xml --project PROJ

   # Xray JSON
   node scripts/integrations/jira/xray_importer.js --format xray --file ./xray-payload.json
   ```

5. **Process Results:**
   - Check output: Test Execution Key created in Jira.
   - If successful: notify the user of the new Test Execution key.
   - If failed: read logs → analyze cause → fix → re-run.

6. **Important Notes:**
   - **Test Key Convention**: Test titles should include the Jira key for automatic Xray mapping:
     ```typescript
     test('[PROJ-123] Login should work', async ({ page }) => { ... });
     ```
   - **Xray Cloud** requires separate authentication (Client ID + Secret), distinct from Jira auth.
   - **Rate limit**: Avoid importing too many times in quick succession.
