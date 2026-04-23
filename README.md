# Antigravity Testing Kit 🚀

> 🌐 **English Translation Fork**
> This repository is an English-translated fork of the original [Antigravity Testing Kit](https://github.com/anhtester/antigravity-testing-kit) by **Anh Tester**. The original content was written in Vietnamese; this fork provides an English version of all documentation, prompts, and instructions for the international community.

👋 Welcome to the **Antigravity Testing Kit**!

This Kit was built and developed by **Anh Tester**, dedicated to the **Vietnamese Tester Community**. The goal of this repo is to provide ready-made configurations, behavioral rules, skills, and standard workflows based on Antigravity's documentation to support using AI Agents on the **Antigravity** software.

This Kit is **not just for Automation** — it is comprehensively designed for both **Manual Testing** and **Automation Testing**, covering the entire software testing lifecycle from requirements analysis and test case design to execution and result reporting.

Notably, every stage is **systematically integrated with AI**, forming a **complete End-to-End AI Testing Workflow** — helping Testers work smarter, faster, and more effectively in the AI era.

---

## 🌟 Key Features

- **🔁 End-to-End AI Process:** Built as a closed-loop AI application process — from requirements analysis, manual test case design, to automated script writing, CI/CD integration, and result reporting — all AI-assisted.
- **📋 Supports Both Manual & Automation Testing:** Beyond Automation, the Kit fully equips processes, skills, and prompts for **Manual Testers** — including Risk-Based Testing (RBT) analysis, high-quality test case design, and test result management.
- **🧠 Optimized for QA/Testers:** All prompts, rules, and workflows are fine-tuned based on the real-world thinking and working processes of both **Manual Testers** and **Automation Engineers**.
- **🌐 Multi-Platform Support:** Compatible with popular frameworks such as Web (Playwright, Selenium), Mobile (Appium), and API (Playwright, REST Assured).
- **🛡️ High Standard Compliance (Strict Rules):** Ensures AI always follows the Page Object Model (POM) structure, writes clean code, never guesses locators, and performs automatic self-fixing.
- **🇻🇳 Originally Vietnamese Communication:** The original AI was configured to communicate, explain, and report entirely in Vietnamese, friendly to Vietnamese users. *(This fork provides English translations.)*

---

## 📂 Main Directory Structure

```
antigravity-testing-kit/
├── .agent/
│   ├── rules/           # Mandatory rules AI must follow
│   ├── skills/          # 10 specialized skills for AI
│   └── workflows/       # 15 step-by-step execution scripts (slash commands)
├── plans/
│   ├── manual/          # 6-step process for Manual Test Cases (AI-RBT)
│   ├── automation/      # 6-step process for Automation Scripts
│   └── cross-module/    # Cross-Module analysis & Combination Matrix process
├── practices/
│   ├── requirements/    # Stores generated requirements
│   └── testcases/       # Stores generated test cases (or sample test cases)
├── prompt_templates/    # Quick-use prompt templates (copy → paste → send)
├── scripts/
│   ├── convert_excel/   # Convert Markdown Test Cases to Excel
│   └── integrations/    # External tool integrations
│       ├── jira/        # Jira & Xray integration (self-contained)
│       └── google_sheet/# Read/write data with Google Sheets
├── GEMINI.md            # General AI Agent rules
├── RULE_GLOBAL.md       # Global rules for all tasks
└── TIPS_QUOTA.md        # Token quota optimization guide
```

### `.agent/` — The Brain of the AI Agent

| Directory | Role |
|-----------|------|
| `rules/` | Mandatory rules: POM, locator strategy, smart waits, Playwright/Selenium/Appium rules |
| `skills/` | 10 specialized skills: automation engineer, manual testing, UI debug, locator healer, test data generator, framework architect, jira integration... |
| `workflows/` | 15 slash commands: `/generate_automation_from_testcases`, `/generate_manual_testcases_rbt`, `/generate_cross_module_test_plan`, `/generate_combinatorial_test_data`... |

---

### `scripts/` — Utility Tools & Integrations

Contains utility tools and system connection scripts. Each tool/integration is an **independent directory** (with its own dependencies, config, and README).

| Tool / Integration | Function | Docs |
|--------------------|----------|------|
| `convert_excel/` | Convert Markdown Test Cases to Excel with optimized layout | [README](scripts/convert_excel/README.md) |
| `integrations/jira/` | Fetch Requirements from Jira, authenticate Xray, push test results to Xray | [README](scripts/integrations/jira/README.md) |
| `integrations/google_sheet/` | Sync, read/write test data from Google Sheets via API | [README](scripts/integrations/google_sheet/README.md) |

```bash
# Quick setup (e.g., Jira)
cd scripts/integrations/jira
npm install
cp .env.example .env    # Fill in credentials
```

---

### `plans/` — 6-Step In-Depth Processes

For complex tasks that need to be executed **sequentially within the same conversation**.

| Plan | Description | Quick Start |
|------|-------------|-------------|
| `plans/manual/` | Generate Manual Test Cases using the **AI-RBT 6-step process** (Risk-Based Testing) | See `plans/manual/QUICK_START.md` |
| `plans/automation/` | Generate Automation Scripts in **6 steps** from context → review | See `plans/automation/QUICK_START.md` |
| `plans/cross-module/` | Analyze **multi-module** features & generate **combination matrices** (Pairwise/Cartesian) | See `plans/cross-module/QUICK_START.md` |

**How to use:** Open `QUICK_START.md` → Follow each step → Send each step's prompt to Antigravity.

### `prompt_templates/` — Quick-Use Prompt Templates

For **single** tasks, just copy → replace `[...]` with actual data → paste → send.

| # | Prompt | Purpose |
|---|--------|---------|
| 01 | `prompt_01_generate_requirements.txt` | Analyze website to generate Requirements |
| 02 | `prompt_02_create_test_cases.txt` | Generate test cases from requirements |
| 03 | `prompt_03_create_framework_playwright.txt` | Set up Playwright TS framework |
| 03 | `prompt_03_create_framework_selenium.txt` | Set up Selenium Java framework |
| 04 | `prompt_04_create_script_playwright.txt` | Write Playwright TS test scripts |
| 04 | `prompt_04_create_script_selenium.txt` | Write Selenium Java test scripts |
| 05 | `prompt_05_convert_manual_to_automation.txt` | Convert manual TCs to automation |
| 06 | `prompt_06_review_automation_code.txt` | Review automation code |
| 07 | `prompt_07_generate_test_data.txt` | Generate structured test data |
| 08 | `prompt_08_analyze_flaky_tests.txt` | Analyze unstable tests |
| 09 | `prompt_09_create_api_tests.txt` | Write API tests from Swagger |

> 💡 The `prompt_templates/prompt_workflow_template/` directory contains shorter, workflow-optimized prompt versions.

---

## ✳️ How to Use in Antigravity

1. **Clone this repo to your machine:**
   Or you can directly copy the `.agent` directory from this repo.

2. **Integrate into your project:**
   Copy the `.agent` directory into the root directory of your Automation or Manual Test project.

3. **Start chatting with AI on Antigravity:**
   When you open the project in Antigravity, the AI will automatically detect the `.agent` directory and immediately apply all the Rules, Skills, and Workflows set up by **Anh Tester**.

4. **(Optional) Use Plans or Prompt Templates:**
   - Complex tasks (1 module) → Open `plans/manual/QUICK_START.md` or `plans/automation/QUICK_START.md`
   - Multi-module tasks (combination matrix) → Open `plans/cross-module/QUICK_START.md`
   - Quick tasks → Copy a prompt from `prompt_templates/` → paste into chat

---

## 🤝 Support & Contribution

- If you encounter difficulties while using this kit or want to contribute to make it better, feel free to create an **Issue** or **Pull Request**.
- Join the **Anh Tester** community to exchange ideas and learn more about Automation Testing!
  - 📘 **Facebook Fanpage:** [Anh Tester](https://www.facebook.com/anhtester)
  - 👥 **Facebook Automation Group:** [Automation Testing Community](https://www.facebook.com/groups/automationtest)
  - 👥 **Facebook Manual Group:** [Manual Testing Community](https://www.facebook.com/groups/manualtest)
  - ✈️ **Telegram Automation:** [Automation Testing Community](https://t.me/+kSUGJ3pVvxkyZWU1)
  - ✈️ **Telegram Manual:** [Manual Testing Community](https://t.me/+8eChRz7OVqliZWRl)

---

## 📄 License

This project is distributed under the open-source **[MIT License](LICENSE)**.

---
Anh Tester Automation Testing 🎯
https://anhtester.com