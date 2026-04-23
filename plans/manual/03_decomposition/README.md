# Step 3: System Decomposition (Decomposition Strategy)

---

## Purpose

Guide the AI to break down a complex system/feature into smaller, manageable **Modules** or **Sub-modules**. This prevents the AI from being overwhelmed by information and missing test scenarios.

## How to Use

1. Ensure all questions from Step 2 have been fully answered.
2. Send the `prompt.txt` file to the AI.
3. The AI will return:
   - A list of **Modules / Sub-modules** with functional descriptions
   - **Dependencies** (relationships between modules)
4. Quickly review the results → proceed to Step 4.

## Decomposition Strategies

There are 3 approaches, depending on the project:

| Approach | Best suited when | Example |
|----------|-----------------|---------|
| **By UI** | The page has clearly distinct sections | Header, Sidebar, Data Table, Form Popup |
| **By Flow** | The feature involves multiple CRUD operations | Create Flow, Edit Flow, Delete Flow |
| **By Entity** | The system has multiple entities | User Management, Product Management, Order Management |

Customize the `[Hint]` section in `prompt.txt` to guide the AI toward the most suitable decomposition approach.