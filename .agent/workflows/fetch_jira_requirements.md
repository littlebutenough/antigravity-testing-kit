---
description: Fetch Requirements / User Stories from Jira tickets and save as markdown or JSON files.
skills:
  - jira_integration
---

# Workflow: Fetch Jira Requirements

> **MANDATORY SKILL:** You MUST load and carefully read the **`jira_integration`** skill (at `.agent/skills/jira_integration/SKILL.md`) to understand how to use the scripts before starting.

This workflow helps fetch requirements, user stories, or issues from Jira and convert them into documents for test automation.

## Execution Steps:

1. **Check prerequisites:**
   - Read the `jira_integration` skill to understand the script structure.
   - Check if the `.env` file exists and is correctly configured.
   - Check if dependencies are installed (`scripts/integrations/node_modules/`).
   - If not installed, run: `cd scripts/integrations && npm install`

2. **Identify information to fetch:**
   - Ask the user to provide information:
     - **Issue key** specifically (e.g., `PROJ-123`) → use `--issue`
     - **Project key + Issue type** (e.g., `PROJ`, `Story`) → use `--project --type`
     - **Custom JQL query** → use `--jql`
     - **Epic key** (fetch children) → use `--epic`
   - Identify output format: `json` (default) or `md` (markdown requirement)

3. **Execute the script:**
   - Run the appropriate command:
   ```bash
   # Fetch a specific issue
   node scripts/integrations/jira/jira_fetcher.js --issue <ISSUE_KEY>

   # Fetch issues by project
   node scripts/integrations/jira/jira_fetcher.js --project <PROJECT_KEY> --type <TYPE> --max <N>

   # Find via JQL
   node scripts/integrations/jira/jira_fetcher.js --jql "<JQL_QUERY>"

   # Export Markdown
   node scripts/integrations/jira/jira_fetcher.js --issue <KEY> --format md --output ./requirements/jira
   ```

4. **Handle results:**
   - Check the output file created in `requirements/jira/` (or specified via `--output`).
   - If JSON format: Read and display issue summary to the user.
   - If Markdown format: Display markdown requirement content for user review.
   - If errors occur: Read logs, analyze causes according to the Troubleshooting table in the skill.

5. **Handle common errors:**
   - **HTTP 401**: Incorrect token → guide user to check `JIRA_API_TOKEN` or `JIRA_PAT`
   - **HTTP 404**: Issue does not exist or `JIRA_BASE_URL` is incorrect
   - **File .env not found**: Guide user to copy `.env.example` → `.env`
   - **Module not found**: Run `npm install` in `scripts/integrations/`

6. **Delivery:**
   - Present results in English.
   - If the user requests conversion to a requirement document, use the `requirements_analyzer` skill to reformat.
   - Save the output file to the appropriate directory (`requirements/jira/`).
