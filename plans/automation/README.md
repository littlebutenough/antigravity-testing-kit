# AI-DRIVEN AUTOMATION TESTING FRAMEWORK

**Objective:**
Use AI to automate the process of building Automation Framework, designing POM, and generating high-quality code — maintainable, CI/CD friendly.

## 📌 Core Principles

1. **AI DOM Recon First:** AI must use browser MCP (Playwright/Selenium) to automatically open browser, inspect real DOM. ABSOLUTELY no guessing locators.
2. **POM Architecture:** All scripts follow Page Object Model — clearly separate Pages and Tests.
3. **Smart Waits & Stability:** No hard sleep (`Thread.sleep`, `waitForTimeout`). Use auto-waiting only.
4. **Deterministic Data:** Test data unique + traceable, no hardcoding.
5. **Self-fix Loop:** AI runs test → if fails → reads logs, fixes code, reruns → until PASS.

---

## 🚀 6-Step Process + Step 0 (Setup)

| Step | Name | Purpose | Skill |
|------|------|---------|-------|
| **0** | Project Architecture | Setup standard directory structure | `qa_automation_engineer` |
| **1** | Context & Role-play | Establish role + tech stack | `qa_automation_engineer` |
| **2** | Analysis & UI Recon | AI opens browser, collects locators | `qa_automation_engineer` + `ui_debug_agent` |
| **3** | POM Design | Design Page Object classes | `qa_automation_engineer` |
| **4** | Test Data Strategy | Generate Data Generator class | `qa_automation_engineer` + `test_data_generator` |
| **5** | Script Generation | Generate test script + auto-run + auto-fix | `qa_automation_engineer` |
| **6** | Review & Refactoring | Clean code + CI/CD readiness | `qa_automation_engineer` |

*(Each step corresponds to a subdirectory, containing `README.md` + `prompt.txt`)*

---

## 🎯 Execution Strategy

### Method 1: Sequential (Manual Control)

Manually use each `prompt.txt` file (01 → 06):
- **Suitable when:** Only need AI for a specific step (e.g., "Only generate POM, no test script needed")
- **Or when:** Large project, need detailed control over each module

### Method 2: One-Click Auto Workflow (Recommended)

Call workflow `/generate_automation_from_testcases` + attach Test Cases:
- Agent automatically: Reads TCs → Opens Browser → Gets Locators → Generates POM → Generates Script → Runs Test → Fixes Bugs → Until PASS
- **Advantage:** Fully automated with a single command

---

## 📋 Reference Rules

- `.agent/rules/automation_rules.md` — POM, Data, Naming conventions
- `.agent/rules/locator_strategy.md` — Locator priority order
- `.agent/rules/playwright_rules.md` — Playwright-specific rules
- `.agent/rules/selenium_rules.md` — Selenium-specific rules
- `.agent/rules/appium_rules.md` — Appium-specific rules

## 📁 Quick Start Guide

See the `QUICK_START.md` file in this directory to get started quickly.