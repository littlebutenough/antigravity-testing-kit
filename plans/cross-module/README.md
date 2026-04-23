# AI-DRIVEN CROSS-MODULE TESTING FRAMEWORK

**Objective:**
Analyze and test complex features that span **multiple sequential modules**, where the output depends on **multi-dimensional combination conditions** (Combinatorial Testing).

## 📌 Problem Statement

When a feature is **NOT contained within a single module** but must pass through a chain of modules, with each module having multiple choices — and the combination of those choices determines the final output (different templates, formulas, business rules).

**Real-world examples:**

| Feature | Combination Dimensions |
|-----------|------|
| Partner Payment Statement | Partner Type × Payment Type × Tax × Debt × Asset Source |
| Insurance Contract | Insurance Type × Subject × Package × Term × Payment Method |
| Export Order | Market × Product Type × Shipping × Payment × Documents |
| Approval Process | Request Type × Department × Level × Amount → Different approval flow |

**If each dimension has 3-5 values →** Full Cartesian combination can easily reach **hundreds of test combinations**.

---

## 🚀 2-Phase Process

### Phase 1: Analysis & Matrix Generation (`/generate_cross_module_test_plan`)

| Step | Name | Description | Awaiting User? |
|------|-----|-----------|-----------|
| **1** | Multi-Module Recon | AI opens browser to explore each module, collects fields + values | ❌ |
| **2** | Data Flow Mapping | Determine what module A outputs → module B inputs | ✅ **Checkpoint** |
| **3** | Dimension Extraction | List all combination "dimensions" + values + constraints | ❌ |
| **4** | Combinatorial Matrix | Generate combination matrix (Pairwise / Business-critical / Full) | ❌ |
| **5** | Expected Output Mapping | Map expected template + formula for each combination | ✅ **Checkpoint** |

**Main output:** Combination matrix table — ready for import into Excel/Jira.

### Phase 2: Test Data Generation (`/generate_combinatorial_test_data`)

| Mode | When to use | Output |
|------|-------------|--------|
| **GENERATE** | Generate data offline (JSON/CSV/Code) | Structured test data file |
| **PIPELINE** | Run through the actual browser to create data on the system | Real data + IDs + screenshots |

---

## 3 Matrix Strategies

| Strategy | Description | When to use | Example |
|-----------|-------|-------------|-------|
| **Pairwise** (Default) | Cover 100% of pairs between any 2 dimensions | Large combinations (>50) | 216 combos → ~20 combos |
| **Business-Critical** | Select only the most important combinations based on risk | Need focus, limited time | 216 combos → ~10 combos |
| **Full Cartesian** | Test ALL valid combinations | Critical systems (finance, healthcare) | 216 combos → 216 combos |

> 💡 **Pairwise Testing** reduces 80-90% of test combinations while still catching the majority of bugs.

---

## 🔗 Complete End-to-End Flow

```
┌─────────────────────────────────────────────────────────────────┐
│                    CROSS-MODULE TESTING FLOW                     │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  📋 Step 0: Analyze Requirements for Each Module                │
│      Workflow: /generate_requirements_from_website (run N times)│
│                          ↓                                       │
│  📊 Step 1: Cross-Module Analysis & Matrix Generation           │
│      Workflow: /generate_cross_module_test_plan    ← NEW        │
│      Output: Data Flow Map + Combination Matrix                 │
│                          ↓                                       │
│  🗃️ Step 2: Generate Test Data for Matrix                      │
│      Workflow: /generate_combinatorial_test_data   ← NEW        │
│      Output: Test data set (offline or on the system)          │
│                          ↓                                       │
│  📝 Step 3: Generate Detailed Test Cases                        │
│      Workflow: /generate_manual_testcases_rbt (FULL RBT)        │
│      Input: Matrix + Requirements                               │
│      Output: Complete test cases                                │
│                          ↓                                       │
│  🤖 Step 4: Generate Automation Scripts                         │
│      Workflow: /generate_automation_from_testcases              │
│      Output: Stable PASS scripts                                │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## 📁 Directory Structure

```
plans/cross-module/
├── README.md              ← Introduction + Overview (you are reading this file)
└── QUICK_START.md         ← Quick start guide (sample prompts + execution flow)
```

**Referenced Workflows:**

```
.agent/workflows/
├── generate_cross_module_test_plan.md       ← New workflow: Analysis + Matrix
└── generate_combinatorial_test_data.md      ← New workflow: Generate test data
```

**Extended Skill:**

```
.agent/skills/test_data_generator/SKILL.md   ← Added: Multi-Step Pipeline + Combinatorial Data
```

---

## 📋 Quick Start

See `QUICK_START.md` in this directory to get started quickly.