# Step 3: POM Structure Design (POM Design)

**Workflow:** `/generate_automation_from_testcases` (continued)
**Skill:** `qa_automation_engineer`

---

## Purpose

After AI has collected locators from real DOM in Step 2, this step guides AI to define **Page Classes** according to POM standard. The goal is to properly allocate files, have meaningful method names **before** implementing test logic.

## Usage

1. Ensure you have reviewed and confirmed locators from Step 2.
2. Send the `prompt.txt` file to AI.
3. AI will outline:
   - Page class name + file path
   - Locator declarations (from Step 2)
   - Method signatures + body
4. Review POM architecture → proceed to Step 4.

## Naming Rules

| Component | Rule | Example |
|---|---|---|
| **Class name** | PascalCase + suffix `Page` | `LoginPage`, `CustomerFormPage` |
| **Locator** | camelCase, describes element | `emailInput`, `submitButton` |
| **Method** | camelCase, describes business action | `fillLoginForm()`, `verifySuccessToast()` |
| **File** | Follow framework convention | `LoginPage.ts` / `LoginPage.java` |

## Notes

- This step only designs **Page classes**, NOT generating test script yet.
- Methods should return `this` or target Page (fluent pattern) for chaining.
- Adhere to rules in `.agent/rules/automation_rules.md`.