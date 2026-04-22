---
name: jira_integration
description: Skill for integrating Jira/Xray — fetching requirements from Jira, authenticating with Xray, and pushing test results to Xray Cloud/Server.
---

# Jira & Xray Integration Skill

## Description

This skill provides integration capabilities between Antigravity Testing Kit and Jira/Xray systems for:

1. **Fetch Requirements/User Stories** from Jira → convert to standard requirement documents.
2. **Xray Authentication** (Cloud or Server/Data Center).
3. **Push Test Results** (Playwright, JUnit, Allure) to Xray Test Management.

---

## When to Use

The agent uses this skill when the user requests:

- Fetch requirements / user stories from Jira.
- Connect / test connection to Jira API.
- Push test results to Xray.
- Import test results to Jira.
- Authenticate Xray token.
- CI/CD integration with Jira.

Trigger keywords:
- "fetch jira", "get requirement from jira", "get jira ticket"
- "import xray", "push results to xray", "push test results"
- "test jira connection", "check jira connection"

---

## Script Structure

```
scripts/integrations/
├── jira/
│   ├── jira_fetcher.js      # Fetch Requirement/User Story from Jira
│   ├── xray_auth.js         # Authenticate and get Xray Token
│   ├── xray_importer.js     # Import test results to Xray
│   └── utils.js             # Shared utility functions
└── package.json             # Dependencies (axios, dotenv)
```

---

## Prerequisites

### 1. Install Dependencies

```bash
cd scripts/integrations
npm install
```

### 2. Configure .env

Copy `.env.example` to `.env` in the project root:

```bash
cp .env.example .env
```

Fill in mandatory information:

| Variable | Description | Mandatory |
|------|--------|----------|
| `JIRA_BASE_URL` | Jira instance URL (e.g., `https://domain.atlassian.net`) | ✅ |
| `JIRA_EMAIL` | Jira account email (Cloud) | ✅ (Cloud) |
| `JIRA_API_TOKEN` | API Token (Cloud) | ✅ (Cloud) |
| `JIRA_PAT` | Personal Access Token (Server/DC) | ✅ (Server) |
| `JIRA_PROJECT_KEY` | Default project key | Recommended |
| `XRAY_PLATFORM` | `cloud` or `server` | Default: cloud |
| `XRAY_CLIENT_ID` | Xray API Client ID | When using Xray Cloud |
| `XRAY_CLIENT_SECRET` | Xray API Client Secret | When using Xray Cloud |

### 3. How to get Jira API Token (Cloud)

1. Log in to [https://id.atlassian.com/manage-profile/security/api-tokens](https://id.atlassian.com/manage-profile/security/api-tokens)
2. Click **Create API token**.
3. Set a label (e.g., "Antigravity Automation").
4. Copy token → paste into `JIRA_API_TOKEN` in the `.env` file.

### 4. How to get Xray Cloud API Key

1. Visit [https://app.getxray.app](https://app.getxray.app) → Settings → API Keys.
2. Or in Jira: Apps → Xray → Settings → API Keys.
3. Create a new API Key → Copy **Client ID** and **Client Secret**.

---

## Usage Instructions

### Fetch a specific issue

```bash
node scripts/integrations/jira/jira_fetcher.js --issue PROJ-123
```

### Fetch issues by project

```bash
node scripts/integrations/jira/jira_fetcher.js --project PROJ --type Story --max 20
```

### Find via JQL

```bash
node scripts/integrations/jira/jira_fetcher.js --jql "project = PROJ AND status = 'To Do'"
```

### Export as Markdown requirement

```bash
node scripts/integrations/jira/jira_fetcher.js --issue PROJ-123 --format md
```

### Fetch children of an Epic

```bash
node scripts/integrations/jira/jira_fetcher.js --epic PROJ-10 --format md
```

### Test Xray authentication

```bash
node scripts/integrations/jira/xray_auth.js
node scripts/integrations/jira/xray_auth.js --verify
```

### Import Playwright results to Xray

```bash
node scripts/integrations/jira/xray_importer.js --format playwright --file ./test-results.json --project PROJ
```

### Import JUnit XML to Xray

```bash
node scripts/integrations/jira/xray_importer.js --format junit --file ./junit-results.xml --project PROJ
```

---

## Related Workflows

| Workflow | Description |
|----------|--------|
| `/fetch_jira_requirements` | Fetch requirements from Jira ticket and save as file |
| `/import_test_results_xray` | Push test results to Xray |

---

## Important Notes

- **Security**: NEVER commit the `.env` file to Git. The `.gitignore` file is already configured to ignore `.env`.
- **Rate Limiting**: Jira Cloud has limits on API calls. The script supports pagination to avoid exceeding limits.
- **Atlassian Document Format (ADF)**: Jira Cloud uses ADF for descriptions. The script automatically converts ADF → plain text.
- **Test Key Convention**: When importing Playwright results, include the test key in the title: `test('[PROJ-123] Login should work', ...)` for proper Xray mapping.

---

## Troubleshooting

| Error | Cause | Solution |
|------|-------------|-----------|
| HTTP 401 | Incorrect token/password | Check JIRA_API_TOKEN or JIRA_PAT |
| HTTP 403 | No permission | Check permissions on the Jira project |
| HTTP 404 | Wrong URL or issue does not exist | Check JIRA_BASE_URL and issue key |
| `ENOTFOUND` | DNS does not resolve | Check if JIRA_BASE_URL domain is correct |
| `ECONNREFUSED` | Server not running | Check if Jira Server is online |
| File .env not found | .env not created | Copy `.env.example` → `.env` |
