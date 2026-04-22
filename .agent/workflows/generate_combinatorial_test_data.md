---
description: Generate test data for multi-dimensional combinatorial matrices using a live pipeline across multiple modules. Input from /generate_cross_module_test_plan.
skills:
  - test_data_generator
  - ui_debug_agent
  - qa_automation_engineer
---

# /generate_combinatorial_test_data — Generate Test Data for Combinatorial Matrix

> **Use case:** When you already have a combinatorial matrix (from `/generate_cross_module_test_plan`) and need to **create actual test data** by running through multiple modules in a browser, or generate a structured data set ready for automation.

> **MANDATORY:** Before starting, you MUST load and carefully read:
> - **Skill:** `.agent/skills/test_data_generator/SKILL.md` — Data generation rules (see Multi-Step Pipeline section).
> - **Skill:** `.agent/skills/ui_debug_agent/SKILL.md` — Inspect DOM during browser execution.
> - **Workflow:** `.agent/workflows/generate_cross_module_test_plan.md` — Understand the input matrix structure.

---

## Relationship with Other Workflows

```
/generate_cross_module_test_plan     →  Combinatorial Matrix (input)
        ↓
/generate_combinatorial_test_data    →  Test Data Set (this workflow)
        ↓
/generate_manual_testcases_rbt       →  Detailed Test Cases
        ↓
/generate_automation_from_testcases  →  Automation Scripts
```

---

## 2 Modes

| Mode | When to Use | Output |
|------|-------------|--------|
| **GENERATE** (default) | Generate structured data set from matrix — NO browser execution | JSON/CSV/Markdown file with data for each combination. |
| **PIPELINE** | Need to create REAL data in the system by running through each module in a browser | Actual created data + IDs + screenshot evidence. |

> Agent chooses mode automatically:
> - User says "generate data", "create data set" → **GENERATE**.
> - User says "create data in the system", "run data creation", "setup real data" → **PIPELINE**.
> - If unclear → ask the user.

---

## Required User Input

| Input | Mandatory | Description |
|-------|----------|-------|
| **Combinatorial Matrix** | ✅ | .md file / Markdown table from `/generate_cross_module_test_plan`. |
| **Application URL** | ✅ (PIPELINE) | For agent to run browser and create data. |
| **Credentials** | ⚠️ PIPELINE | If app requires login. |
| **Output format** | ❌ | `json` (default), `csv`, `markdown`, `code` (TS/Java/Python). |
| **Data Language** | ❌ | English (default), or as per context. |

---

## Execution Steps

### Step 1: Read & Parse Combinatorial Matrix

1. **Read the matrix file** provided by the user:
   - Local file → `view_file`.
   - Inline in chat → parse directly.
   - URL → `read_url_content`.

2. **Parse and validate:**
   - Identify list of dimensions (D1, D2, D3...).
   - Identify values for each dimension.
   - Read expected template/formula for each set.
   - Count total number of combinations needing data generation.

3. **Present Summary:**
   ```markdown
   📊 Matrix Read Summary:
   - Dimensions: 5 (Partner, Payment, Tax, Debt, Asset Source).
   - Total Combinations: 20 (Pairwise).
   - Mode: GENERATE / PIPELINE.
   
   Start generating data? (Y/N)
   ```

---

### Step 2: Analyze Fields & Data Requirements Per Module

1. **For each module** in the chain, identify fields requiring data:

   ```markdown
   | Module | Field | Type | Required | Constraints | Data Source |
   |--------|-------|------|----------|-------------|-------------|
   | Partner | partner_name | string | ✅ | max: 200 | Random + prefix |
   | Partner | partner_type | select | ✅ | enum: [TC, CN, HKD] | From dimension D1 |
   | Partner | tax_id | string | ✅ | 10-13 digits | Random unique |
   | Payment | currency | select | ✅ | enum: [VND, USD] | From dimension D2 |
   | Payment | amount | number | ✅ | min: 1 | Business-relevant values |
   | Tax | tax_type | select | ✅ | enum: [PIT, VAT, NT, MT] | From dimension D3 |
   | ...| ... | ... | ... | ... | ... |
   ```

2. **Classify Fields:**

   | Category | Description | Data Generation Method |
   |------|-------|----------------|
   | **Dimension fields** | Values belonging to dimensions in the matrix | Extracted from combination (no random). |
   | **Supporting fields** | Mandatory fields that are not dimensions | Generated random + unique + traceable. |
   | **Computed fields** | Automatically calculated from formula | Calculated according to business rules. |
   | **Reference fields** | ID/code from previous module | Copied from output of previous module. |

---

### Step 3: Generate Test Data — GENERATE Mode

> Perform when mode = GENERATE (default).

1. **For each combination** in the matrix, generate 1 complete data set:

   ```json
   {
     "combination_id": "COMBO_01",
     "dimensions": {
       "D1_partner_type": "Organization",
       "D2_payment_type": "VND",
       "D3_tax_type": "VAT 10%",
       "D4_debt_type": "Normal",
       "D5_asset_source": "Fund A"
     },
     "module_data": {
       "module_1_partner": {
         "partner_name": "auto_combo01_tc_1712049200",
         "partner_type": "Organization",
         "tax_id": "0123456789",
         "address": "1 Nguyen Hue, Dist 1, HCM"
       },
       "module_2_payment": {
         "currency": "VND",
         "amount": 100000000,
         "payment_date": "2026-04-15",
         "description": "Payment for combo01"
       },
       "module_3_tax": {
         "tax_type": "VAT",
         "tax_rate": 10,
         "tax_amount": 10000000
       },
       "module_4_debt": {
         "debt_type": "Normal",
         "advance_amount": 0
       }
     },
     "expected_output": {
       "template": "BB_TC_VND_VAT",
       "formula": "Amount × 1.10",
       "computed_total": 110000000,
       "expected_fields": ["partner_name", "tax_id", "amount", "tax_amount", "total"]
     }
   }
   ```

2. **Generate data for ALL combinations** → package into 1 output file.

3. **Ensure Data Rules:**
   - Unique per combo (no overlap between sets).
   - Traceable: prefix `auto_combo{XX}_{dimension_short}`.
   - No real PII.
   - Computed values must correctly follow the formula.

---

### Step 3P: Generate Test Data — PIPELINE Mode (Live browser execution)

> Perform when mode = PIPELINE.

1. **Open browser via MCP:**
   ```
   browser_navigate → App URL
   browser_resize → 1920 × 1080
   ```

2. **Loop through each combination:**

   ```
   FOR each combo in matrix:
     FOR each module in chain:
       1. Navigate → module URL.
       2. `browser_snapshot` → confirm state.
       3. Fill data according to combo:
          - Dimension fields → choose value per combo.
          - Supporting fields → generate random + traceable.
       4. Submit / Save.
       5. `browser_wait_for` → confirm success.
       6. `browser_snapshot` → capture result.
       7. Extract output (ID, code...) → save for next module.
     END FOR
     
     // Final module — verify output
     8. Capture final report / output.
     9. `browser_take_screenshot` → evidence.
     10. Record: combo_id, created_ids, template_found, formula_verified.
   END FOR
   ```

3. **Error Handling in Pipeline:**

   | Error | How to Handle |
   |-----|-----------|
   | Submit fail (validation) | Screenshot → log error → skip combo → notify user. |
   | Slow module load | `browser_wait_for` with incremental timeouts. |
   | Session expired | Re-login → retry from the failed module. |
   | Duplicate data | Re-generate unique data, retry. |
   | Invalid combo (constraint) | Skip → mark as "INVALID" in the report. |

4. **Pipeline Limits:**
   - Maximum **30 combinations** per session (to avoid timeout).
   - If > 30 → split into batches, ask user: "Continue with next batch?".
   - Every 10 sets → report progress to the user.

---

### Step 4: Packaging Output & Reporting

#### Output for GENERATE Mode:

Create artifact file(s) in the format requested by the user:

**JSON (default):**
```json
{
  "feature": "Partner Payment Report",
  "generated_at": "2026-04-15T17:00:00Z",
  "strategy": "pairwise",
  "total_combinations": 20,
  "dimensions": ["partner_type", "payment_type", "tax_type", "debt_type", "asset_source"],
  "data_sets": [
    { "combination_id": "COMBO_01", "dimensions": {...}, "module_data": {...}, "expected_output": {...} },
    { "combination_id": "COMBO_02", ... }
  ]
}
```

**Markdown Table:**
```markdown
| Combo | Partner | Payment | Tax | Debt | Source | Partner Name | Amount | Expected Template | Expected Total |
|-------|---------|-----------|------|---------|-------|-------------|--------|------------------|----------------|
| 01 | Org | VND | VAT | Normal | Fund A | auto_c01_tc | 100M | BB_TC_VND_VAT | 110M |
| 02 | ... | ... | ... | ... | ... | ... | ... | ... | ... |
```

**Code (TypeScript example):**
```typescript
// test-data/payment-record.data.ts
export const combinatorialData = [
  {
    id: 'COMBO_01',
    partner: { name: `auto_combo01_${Date.now()}`, type: 'Organization', taxId: '0123456789' },
    payment: { currency: 'VND', amount: 100_000_000 },
    tax: { type: 'VAT', rate: 10 },
    expected: { template: 'BB_TC_VND_VAT', total: 110_000_000 },
  },
  // ... more combos
];
```

#### Output for PIPELINE Mode:

```markdown
## Pipeline Execution Report

| # | Combo | Status | Module 1 ID | Module 2 ID | Module 3 ID | Output Template | Formula ✓ | Screenshot |
|---|-------|--------|------------|------------|------------|-----------------|-----------|------------|
| 1 | COMBO_01 | ✅ PASS | PTR-001 | PAY-001 | TAX-001 | BB_TC_VND_VAT | ✅ Match | combo01.png |
| 2 | COMBO_02 | ✅ PASS | PTR-002 | PAY-002 | TAX-002 | BB_TC_USD_PIT | ✅ Match | combo02.png |
| 3 | COMBO_03 | ❌ FAIL | PTR-003 | PAY-003 | — | — | — | combo03_fail.png |

### Summary
- ✅ Passed: 18/20
- ❌ Failed: 2/20 (COMBO_03: Tax module validation error, COMBO_17: Timeout)
- 📊 Data created: 18 partners, 18 payments, 18 tax configs, 18 reports.
```

---

## Data Rules (MANDATORY)

| # | Rule | Description |
|---|------|-------|
| 1 | **Unique per combo** | Each combination uses its own data — no sharing between combos. |
| 2 | **Traceable** | Prefix: `auto_combo{XX}_{dimension_short}_{timestamp}`. |
| 3 | **Dimension values exact** | Dimension values MUST be exactly as in the matrix — NO random. |
| 4 | **Supporting fields random** | Fields not in dimensions → generate random + unique. |
| 5 | **Computed values verified** | Calculated values must be correct according to the matrix formula. |
| 6 | **No real PII** | DO NOT use real personal identifiable information. |
| 7 | **Include expected output** | Each combo MUST have an expected template + formula + computed values. |

---

## FORBIDDEN

| ❌ Forbidden Action | ✅ Correct Alternative |
|-------------------|-----------------|
| Random dimension values | Dimension values must be extracted exactly from the matrix. |
| Hardcoding same data for all combos | Unique data per combo with traceable prefix. |
| Ignoring expected output | Each combo MUST include expected template + values. |
| Running pipeline > 30 combos without asking | Batch by 30, ask user to continue. |
| Ignoring failed combos without reporting | Log details: which combo failed, why, with screenshot. |
| Reading .env for credentials | Ask the User or use an existing fixture. |

---

## Final Checklist

- [ ] Read and parsed the combinatorial matrix fully.
- [ ] Classified fields: dimension / supporting / computed / reference.
- [ ] Generated data is unique per combo + traceable.
- [ ] Dimension values match the matrix 100%.
- [ ] Computed values match the formula.
- [ ] (PIPELINE) Screenshot evidence for each combo.
- [ ] (PIPELINE) Pass/fail report for each combo.
- [ ] Contains no real PII.
- [ ] Output file matches user's requested format.
