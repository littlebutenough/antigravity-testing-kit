---
name: requirements_analyzer
description: Skill for analyzing websites/modules and generating standard Requirements Documents/User Stories.
---

# Requirements Analyzer Skill

This skill provides detailed instructions so that AI (Antigravity) can convert the UI interface or DOM/HTML structure of a website into clear, detailed Requirements documents, directly serving QA, Testers, and Developers.

## 1. Core Objectives
- Build requirements documents that closely follow the actual running system.
- Ensure consistency and comprehensiveness for both Happy Path and Edge Cases (Exceptions/Error reporting).
- Format the output professionally (using the Artifact structure).

## 2. Information Extraction Process
When requested to create Requirements from a website:
1. **Layout Analysis:** Identify Header, Footer, Sidebar, and Main Content sections.
2. **Collect Forms & Inputs:**
   - Find all input fields (`input`, `select`, `textarea`).
   - Record attributes like `type` (text, email, password, number), `required`, `maxlength`, `minlength`, `pattern`.
3. **Collect Interactive Buttons/Links/Actions:**
   - Identify the function of each button (Save, Submit, Cancel, Delete, Edit).
   - Note alerts, toasts, and validation messages that appear during erroneous interaction.
4. **Extract Workflows:**
   - Component dependencies (e.g., Submit button is only enabled when the "I agree" checkbox is ticked).

## 3. Output Requirements Document Structure
The document should be formatted in professional Markdown or saved as an Artifact (`requirements_spec.md`).

**Mandatory Content:**

### 3.1. Overview
Briefly describe the feature and purpose of the website/module.

### 3.2. Functional Requirements
Divide into **User Stories** or **Use Cases**:
- **Feature Name** (e.g., Login Function)
- **Description:** "As a user, I want to... so that..."
- **Acceptance Criteria (AC):** Clearly state the conditions that must be met.

### 3.3. Field Specifications
This is the core section for Automation Testers:
* Use a Markdown Table to list:
  - Field Name (Label)
  - Type (UI Type)
  - Validation Rules (Required / Default / Length limits).
  - Notes.

### 3.4. Business Rules & Validations
Detail the expected validation messages when users enter incorrect data.

## 4. Strict Rules
- Always write in **English**.
- Do not speculate on complex business requirements without a basis from the UI. If logic is missing, list them in a "Questions/Clarifications for PO/User" section.
- If Playwright MCP is available, prioritize opening a real browser to screenshot/capture the interface if necessary.
