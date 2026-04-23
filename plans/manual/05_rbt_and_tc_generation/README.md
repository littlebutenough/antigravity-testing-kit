# Step 5: Detailed Test Case Generation (RBT & Test Case Generation)

---

## Purpose

Generate detailed Test Cases based on **Risk-Based Testing (RBT)** strategy: high risk → thorough testing, low risk → basic testing.

## How to Use

1. Ensure you have reviewed and confirmed the scenarios from Step 4.
2. Send the `prompt.txt` file to the AI, customize the `[Hint]` section if needed.
3. The AI will generate complete test cases including: Title, Pre-condition, Steps, Expected Result, Test Data, Risk Level, Priority.
4. Review the results → proceed to Step 6.

## Important Tips

### Split When There Are Many Modules
If Step 4 has more than 5 modules, **don't ask the AI to generate all at once**. Instead:
```
Please generate Test Cases for Module 1 and Module 2 first.
```
After reviewing, continue with:
```
Continue generating Test Cases for Module 3 and Module 4.
```

### Test Data Must Be Specific
The AI will generate realistic test data instead of placeholders:
- ✅ `test_customer_01@domain.com` instead of ❌ `valid email`
- ✅ `CUST-2026-0012` instead of ❌ `valid ID`

### Risk Level Determines Depth
| Risk Level | Expected TC Count | Coverage |
|------------|------------------|----------|
| **High** | 8-15+ TC/module | Happy + Negative + Boundary + Edge |
| **Medium** | 4-8 TC/module | Happy + Key Negative |
| **Low** | 2-4 TC/module | Basic Happy Path |

### Test Case Design Techniques

The prompt integrates 4 classic techniques to help the AI generate systematic test cases:

| Technique | When to Use | Example |
|----------|-------------|---------|
| **Equivalence Partitioning** | Fields with multiple input types | Age: <18, 18-60, >60 |
| **Boundary Value Analysis** | Fields with min/max limits | Password 6-20 chars: test 5,6,20,21 |
| **Decision Table** | Logic with multiple combined conditions | Login: email + password + active status |
| **State Transition** | Objects with workflow/states | Order: New → Processing → Delivered → Completed |