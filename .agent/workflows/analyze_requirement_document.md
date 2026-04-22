---
description: Analyze requirement documents (Jira ticket, .doc, user story) — generate detailed analysis documents, DO NOT generate test cases.
skills:
  - requirements_analyzer
---

> **MANDATORY SKILL:** You MUST load and carefully read the **`requirements_analyzer`** skill (at `.agent/skills/requirements_analyzer/SKILL.md`) to understand standard requirement analysis before starting.

# Workflow: Requirement Document Analysis

This workflow analyzes requirement documents (Jira tickets, .doc files, user stories, design mockups) and generates a detailed analysis document. **DO NOT generate test cases** — focus only on understanding, decomposing, and detecting risks/ambiguities in the requirements.

## When to Use

- The user provides a Jira ticket (.doc) or requirement document and asks for an "analysis".
- The user wants to clearly understand the scope, acceptance criteria, and dependencies before writing tests.
- The user needs a list of ambiguities to clarify with the PO/BA.
- The user says: "analyze requirement", "review requirement", "analyze this ticket".

## Input

The agent needs to collect from the user:

| # | Input | Mandatory | Description |
|---|---|---|---|
| 1 | **Requirement document** | ✅ | .doc, .md files, Jira URL, or requirement description text. |
| 2 | **Mockup/Screenshot** | ⭕ Encouraged | UI design images, wireframes, or current screenshots. |
| 3 | **Related tickets** | ⭕ Optional | Dependent or related tickets (dependencies). |
| 4 | **Additional Context** | ⭕ Optional | Information about the current system, business domain. |

> [!NOTE]
> If the user only provides a .doc file without mockups, the agent must still perform a full analysis based on the document content. If mockups/screenshots are provided, the agent performs a more detailed UI analysis.

## Execution Steps

### Step 1: Information Gathering

1. **Read the requirement document** provided by the user (.doc, .md file, or URL).
   - If the .doc file is in HTML format (exported from Jira): parse the HTML to extract content.
   - Identify: Ticket ID, Type, Priority, Status, Reporter, Assignee, Fix Version, Sprint, Labels.
2. **Read mockups/screenshots** if available — analyze UI layout, components, and fields.
3. **Check related tickets** if available in the same directory or provided by the user.
   - Read and summarize dependencies.
4. **Confirm** context understanding → continue analysis.

### Step 2: Core Analysis

1. **Ticket Overview** — Metadata table (ID, Type, Priority, Status, Sprint, Assignee...).
2. **User Story** — Extract "As a... I want... So that..." format.
3. **Scope** — Clearly identify affected modules/pages/components.
4. **Acceptance Criteria** — Decompose each AC into logical groups, including:
   - Detailed description of each AC.
   - Comparison tables (if there are new columns, fields, or rules).
   - Clearly distinguish between **default vs. optional** (if applicable).

### Step 3: UI Analysis from Mockup (if available)

If the user provides mockups/screenshots:

1. **Layout description** — Breadcrumb, header, sidebar, main content, footer.
2. **Component listing** — Tables, forms, modals, buttons, dropdowns, tabs.
3. **Field details** — Field name, type (input/dropdown/date picker), label, placeholder.
4. **Compare mockup with document** — detect inconsistencies.
5. **Capture observations** into a carousel in the artifact (if images are available).

### Step 4: Dependency Analysis

1. Identify related tickets/features (referenced in AC or comments).
2. Read and summarize dependent ticket content.
3. If there is a separate mockup for the dependency → detailed UI analysis (fields, modals, interactions).
4. Synthesize **Business Rules** from all requirements + mockups.
5. Clearly mark which rules are from the main ticket vs. dependent tickets.

### Step 5: Detecting Ambiguities & Risks (Core Focus)

> [!IMPORTANT]
> This is the **highest value** part of the workflow — identifying what the requirements DO NOT state clearly.

**5.1. Ambiguities:**

For each ambiguity, specify:
- **Code:** AMB-XX (sequential numbering).
- **Question:** Clear description of what is unclear.
- **Risk:** Impact if not resolved.
- **Level:** 🔴 High / 🟡 Medium / 🟢 Low.

Directions for detecting ambiguity:
- Vague keywords: "where applicable", "as needed", "similar to", "etc."
- Missing validation rules: min/max, format, required/optional.
- Edge case behavior: network errors, concurrent access, empty data.
- Inconsistency between document and mockup (column names, format, layout).
- Undefined thresholds/configs (e.g., how many days = "approaching deadline"?).
- Conflicts between old and new requirements.

**5.2. Testing Risks:**

For each risk, specify:
- **Code:** RISK-XX.
- **Risk Name**
- **Description**
- **Mitigation**

### Step 6: Synthesis & Delivery

1. **State Matrix** (if state transitions exist) — state to behavior mapping table.
2. **AC Checklist** — Summary of all AC in checkbox format, grouped by function.
3. **Testing Recommendations** — Suggestions for the top 10 most important things to consider during testing.
4. **Export Artifact** — Save the entire analysis to a `.md` file.

## Output Structure (Artifact Template)

The agent MUST export the artifact following this structure:

```markdown
# 📋 Requirement Analysis: [TICKET-ID]
## [Ticket Title]

## 1. Ticket Overview
(Metadata table)

## 2. User Story
(As a... I want... So that...)

## 3. Scope
(Table listing affected modules/pages)

## 4. Acceptance Criteria — Detailed Analysis
### 4.1. [AC Group 1]
### 4.2. [AC Group 2]
### 4.N. [AC Group N]

## 5. Dependencies
### 5.1. [Dependent Ticket]
#### 5.1.1. [UI Details if mockups available]
#### 5.1.N. Synthesized Business Rules

## 6. Mockup/Screenshot Analysis
### 6.1. [Mockup 1]
### 6.N. [Mockup N]

## 7. Ambiguities & Risks
### 7.1. Ambiguities
(Table: #, Question, Risk, Level)
### 7.2. Testing Risks
(Table: #, Risk, Description, Mitigation)

## 8. State Matrix (if applicable)
(State → behavior table)

## 9. Acceptance Criteria Summary (Checklist)
(Checkboxes grouped by function)

## 10. Testing Recommendations
(List of suggestions, NOT test cases)
```

## Important Rules

- ❌ **DO NOT generate test cases** — this workflow is for analysis only, not TC creation.
- ❌ **DO NOT guess business logic** if the document is unclear → add to Ambiguities.
- ❌ **DO NOT ignore comments** in the Jira ticket — comments often contain important supplementary information.
- ✅ **MUST read related tickets** if referenced in the AC.
- ✅ **MUST analyze mockups** in detail if provided (fields, layout, interactions).
- ✅ **MUST clearly state inconsistencies** between document and mockup.
- ✅ **MUST write in English**, Markdown format, export Artifact.
- ✅ **MUST copy images** to the artifacts directory if embedding in the artifact is needed.

## Relationship with Other Workflows

| After analysis is complete | Next Workflow |
|---|---|
| Need to generate test cases quickly | `/generate_testcases_from_requirements` |
| Need formal test case generation (6-step RBT) | `/generate_manual_testcases_rbt` |
| Need to generate automation scripts | `/generate_automation_from_testcases` |
| Need cross-module analysis | `/generate_cross_module_test_plan` |
