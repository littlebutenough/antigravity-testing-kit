# Step 6: Format Standardization (Template Mapping)

---

## Purpose

Package all Test Cases generated in Step 5 into a standardized Markdown table, ready to copy to **Excel**, **Google Sheets**, or import directly into **Jira/TestRail/Xray/Zephyr**.

## How to Use

1. Send the `prompt.txt` file after reviewing the test cases from Step 5.
2. AI will output a Markdown table with this format:
   ```
   | TC ID | Module | Risk Level | Test Title | Pre-Condition | Test Steps | Expected Result | Priority | Test Data |
   ```
3. Copy the table result → paste into your test management tool.

## TC ID Rules

Default format: `[PROJECT]_[MODULE]_TC_[NUMBER]`

Example: `CRM_CUST_TC_001`, `CRM_LOGIN_TC_001`

If the project has its own ID convention, change it in the `[Customization]` section of `prompt.txt`.

## Handling Large Outputs

If the total number of Test Cases exceeds 30, AI will automatically split them into **Part 1, Part 2...** and ask you before continuing. Ensure:
- No TC is missed between parts
- TC ID numbering is continuous across parts