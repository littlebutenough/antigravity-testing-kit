# Step 2: Requirement Analysis & Q&A

---

## Purpose

Ask the AI to analyze the document from Step 1 to identify missing points, contradictions, and ambiguities **before** writing test scenarios. This step simulates a Tester's mindset when reading documentation and asking questions to the BA/PO.

## How to Use

1. Copy the content of `prompt.txt` and send it right after the AI has confirmed in Step 1.
2. The AI will return:
   - A list of **Flows** (Happy / Alternate / Exception Paths)
   - A list of **Ambiguities** (unclear points detected)
   - A list of **numbered Q&A questions**
3. **Read carefully** through each question and provide answers to the AI.
4. Once all questions are answered → proceed to Step 3.

## ⚠️ Important Notes

- **This is the most critical step** in the process. Skipping it will cause the AI to guess the logic → severely incorrect test cases.
- Answer **as specifically as possible**. If you don't know, clearly state "Not determined, assuming..." so the AI acknowledges it.
- You can add extra hints in the `[...]` section of `prompt.txt` to steer the AI toward areas you care about.