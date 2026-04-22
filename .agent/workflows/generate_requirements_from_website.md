---
description: Generate Requirements content from a provided website module
skills:
  - requirements_analyzer
---

# Workflow: Generate Requirements from Website Module

> **MANDATORY SKILL:** You MUST load and carefully read the **`requirements_analyzer`** skill (at `.agent/skills/requirements_analyzer/SKILL.md`) to understand the standard formatting for Requirement documents before starting this task.

This workflow helps you analyze a provided module or website and generate detailed, accurate Requirement documents for testing or development purposes.

## Execution Steps:

1. **Information Gathering:**
   - Read the instructions from the `requirements_analyzer` skill to understand the required output standard.
   - Obtain the website URL, module name, or descriptions/images provided by the user.
   - If necessary, ask the user for login information or special states to note.

2. **Recon & Investigation:**
   - Use web browsing tools (Browser tools/MCP) or `read_url_content` to access the requested website module.
   - Thoroughly inspect the HTML structure, DOM, input forms, interactive elements (buttons, links), and error messages (validation messages).
   - *Note: Do not guess field information if it is not visible on the actual interface.*

3. **Analyze UI & Interactions:**
   - Analyze operation flows (User Flows).
   - Record static and dynamic data fields (e.g., TextBox, Dropdown, Checkbox).
   - Record business rules visible on the interface, such as: mandatory fields, valid formats (email, phone number), or character limits.

4. **Draft Requirements:**
   - Based on the collected data, create a descriptive document including:
     * **Overview:** Purpose of the module/page.
     * **Functional Requirements:** List of features users can perform (e.g., Login, Add New, Delete...).
     * **Field Specifications:** Detailed table of each UI component (Field Name, Type, Mandatory/Optional, Data Constraints).
     * **Business/User Flows:** Steps to complete a primary function.
     * **Non-functional Requirements (if observable):** Compatibility, static performance.

5. **Review & Delivery:**
   - Format the document using clear Markdown.
   - Present all content in clear, professional, and easy-to-understand **English**.
   - Use the Artifact feature for lengthy documents for convenient storage or export.