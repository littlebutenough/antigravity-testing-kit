# AI-DRIVEN RISK-BASED TESTING FRAMEWORK (AI-RBT)

**Objective:**
Leverage AI speed for detailed execution, combined with human strategic RBT (Risk-Based Testing) thinking to optimize testing resources.

## 📌 Core Principles

1. **Human Strategy:** Humans define strategy, risk levels, and standards.
2. **AI Execution:** AI performs analysis, writes TCs, and reviews gaps.
3. **Human Verification:** Humans review AI results before finalizing.

---

## 🚀 Overall Process (6 Steps)

1. **Context & Role-play:** Initialize the QA/Tester expert mindset for AI by defining the role and providing project context.
2. **Analysis & QnA:** AI reads documents and analyzes requirements to clarify ambiguities before writing scenarios.
3. **Decomposition:** Break down the system into smaller Modules (Feature Mapping - FM) for easier assessment.
4. **Traceability:** Establish a traceability matrix to ensure full requirement coverage.
   - *(Note: After Step 4, there may be a Human-only Risk Assessment Checkpoint.)*
5. **RBT & TC Generation:** Apply Risk-Based Testing strategy for AI to generate detailed test scenarios (Logic & Scenario).
6. **Template Mapping:** Standardize all Test Case formats and populate template files/tables for management tools (e.g., Jira, Excel).

*(Each step above corresponds to a sub-directory in this folder, including a `README.md` with detailed instructions and a `prompt.txt` with sample AI prompts.)*

---

## ⚠️ Important Note: Execution Strategy

For **Manual Testing** (starting from Functional Requirements / Figma), you **MUST** run each prompt sequentially rather than bundling them into a single 100% automated workflow command.

**Reasons:**
1. **Bottleneck at Step 2 (Analysis & QnA):** AI needs time to analyze and raise questions (Ambiguities) about the Requirement's logic for you to answer. If run all at once, AI will guess the logic, leading to severely incorrect Test Cases.
2. **Human in the Loop:** At Steps 4 and 5, the human Tester must review and assess risks (RBT) before AI generates detailed scenarios.
3. **Prevent Hallucination:** Loading a long document and asking AI to output hundreds of scenarios at once causes the AI to lose focus and miss coverage. Breaking it down into individual prompts delivers the highest quality Test Cases.