# Step 4: Ensuring Coverage (Traceability & Gap Analysis)

---

## Purpose

Cross-check and establish a **Traceability Matrix** to ensure 100% of the original requirements are covered by Test Scenarios.

## How to Use

1. Send the `prompt.txt` file after reviewing the decomposition results from Step 3.
2. AI will return:
   - **Traceability Matrix:** A mapping table of REQ ID ↔ Module ↔ Scenario
   - **Gap Analysis:** A report of any missing coverage (if any)
   - **High-Level Scenarios:** A list of high-level scenarios
3. **Review carefully** the scenario list:
   - Are there any missing scenarios?
   - Are there any redundant / duplicate scenarios?
   - Add more if needed → Confirm → proceed to Step 5.

## ⚠️ Human Checkpoint

**This is a human checkpoint.** Reasons:

- AI may miss project-specific edge cases.
- The Tester (human) must assess the **risk level** for each module **before** letting AI generate detailed test cases in Step 5.
- If gaps are found, ask AI to add more: *"Please add scenarios for the case [X]"*.