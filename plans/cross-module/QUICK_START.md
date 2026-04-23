# 📋 Quick Start Guide: Cross-Module Testing & Combination Matrix

## 🔀 Choose Your Workflow

### Workflow 1: Antigravity (Slash Command) — Automated (Recommended ⭐)

> Use when you're using **Antigravity** (Google Gemini plugin).

```
/generate_cross_module_test_plan

Feature: [Feature name, e.g., "Payment reconciliation statement for partners"]
URL: [https://your-app.com]
Account: [admin@test.com / Test@123]

Related modules:
1. [Module 1: Partner Management — select partner type]
2. [Module 2: Create Payment — select VND/USD]
3. [Module 3: Tax Configuration — select tax type]
4. [Module 4: Debt Management — select debt type]
5. [Final Module: Generate Statement — output]

Matrix strategy: pairwise (or: business-critical / full-cartesian)
```

→ AI opens browser → explores each module → draws Data Flow → generates combination matrix.

---

### Workflow 2: Copy-Paste into ChatGPT / Claude — Manual

> Use when you want to use a different AI (not Antigravity).

**Sample prompt — Copy and paste into AI chat:**

```
You are a Senior QA Engineer specializing in Combinatorial Testing.

I have a feature "[FEATURE NAME]" that goes through multiple sequential modules.
Each module has multiple options, and the combination of these options determines the final output.

Modules and dimensions:
- Module 1 [NAME]: dimension [DIMENSION NAME] = [value 1, value 2, ...]
- Module 2 [NAME]: dimension [DIMENSION NAME] = [value 1, value 2, ...]
- Module 3 [NAME]: dimension [DIMENSION NAME] = [value 1, value 2, ...]
- ...

Please perform the following:
1. Draw a Data Flow Diagram between modules (which module outputs what → inputs to which module)
2. List all constraints (invalid combinations)
3. Generate a combination matrix using Pairwise Testing strategy
4. For each combination, specify the Expected Output (template, formula if applicable)

Output as a Markdown table, ready to copy to Excel.
```

---

## 🎯 End-to-End Workflow — Step by Step

### Step 1: Cross-Module Analysis & Matrix Generation

```
/generate_cross_module_test_plan

Feature: Payment reconciliation statement for partners
URL: https://example.com/partners
```

**Result:** AI generates:
- 📊 Data Flow Diagram (module → module)
- 📋 Dimensions Table (all dimensions + values)
- 📈 **Combination Matrix** (Pairwise ~20 sets instead of 216 Full sets)

**⏸️ User review:** Review matrix → add/edit → confirm OK.

---

### Step 2: Generate Test Data for the Matrix

```
/generate_combinatorial_test_data

Matrix: [paste matrix table from Step 1]
Mode: GENERATE (or PIPELINE if you want to create real data on the system)
Format: json (or: csv, markdown, typescript)
```

**Result:**
- GENERATE Mode: JSON/CSV file containing N data sets, each set = 1 combo
- PIPELINE Mode: AI runs a real browser → creates data on the system → pass/fail report

---

### Step 3: Generate Test Cases (Optional)

```
/generate_manual_testcases_rbt

Requirements: [paste requirements + combination matrix from Step 1]
```

→ AI generates detailed test cases for each important combination set.

---

### Step 4: Generate Automation Scripts (Optional)

```
/generate_automation_from_testcases

URL: https://example.com
Test cases: [paste test cases from Step 3]
Framework: Playwright TypeScript
```

→ AI generates scripts + runs them + self-fixes → PASS stable.

---

## 📊 Real-World Example: Partner Payment Reconciliation Statement

### Input (you provide):

```
Feature: Payment reconciliation statement for partners
Modules:
1. Partner Management: Type = [Organization, Individual, Business Household]
2. Payment: Type = [VND, USD]
3. Tax: Type = [PIT, VAT, Contractor, Tax Exempt]
4. Debt: Type = [Regular, Advance, Adjustment]
5. Asset Source: Type = [Fund A, Fund B, Fund C]
```

### Output (AI generates):

**Data Flow:**
```
Partner → Payment → Tax → Debt → Statement
(type)     (currency)   (Depends on    (Depends on
                         type+currency)  all)
```

**Pairwise Matrix (20 sets instead of 3×2×4×3×3 = 216 sets):**

| # | Partner | Payment | Tax | Debt | Source | Expected |
|---|---------|---------|-----|------|--------|----------|
| 1 | Organization | VND | VAT | Regular | Fund A | ST_ORG_VND_VAT |
| 2 | Organization | USD | PIT | Advance | Fund B | ST_ORG_USD_PIT |
| 3 | Individual | VND | PIT | Regular | Fund A | ST_IND_VND_PIT |
| 4 | Individual | USD | VAT | Adjustment | Fund C | ST_IND_USD_VAT |
| 5 | Business HH | VND | Contractor | Regular | Fund B | ST_BH_VND_CTR |
| ... | ... | ... | ... | ... | ... | ... |

→ Covers 100% of pairs between any 2 dimensions, needing only ~20 sets!

---

## 💡 Optimization Tips

1. **Start with Pairwise** — good enough for 90% of cases, reduces 80-90% of effort
2. **Provide business rules** if available — AI will map them to the "Expected Output" column more accurately
3. **Review Data Flow in Step 2** — this is the most important checkpoint; if wrong here → the matrix will be wrong
4. **Use PIPELINE Mode** when test data must be created through the real UI (cannot seed the database)
5. **Run within the same conversation** with Antigravity so AI maintains context throughout
6. **Split into batches** if the matrix exceeds 30 sets — avoid timeout and maintain quality

---

## ⚠️ When NOT to Use This Workflow?

| Scenario | Which workflow to use? |
|----------|----------------------|
| Testing a single module, simple form | `/generate_manual_testcases_rbt` or `/generate_testcases_from_requirements` |
| Multiple modules but **independent** (no cross-influence) | `/generate_application_test_plan` |
| Multiple **sequential** modules, output depends on **combination** | ✅ `/generate_cross_module_test_plan` ← **Use this workflow** |
| Only need test data for 1 form | `/generate_test_data` |