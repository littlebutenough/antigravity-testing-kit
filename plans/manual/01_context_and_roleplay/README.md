# Step 1: Initialize Context (Context & Role-play)

---

## Purpose

Establish the role of a **Senior QA Engineer** and load the project context so the AI adopts an expert testing mindset. This step limits the AI's knowledge space, helping responses become more in-depth and realistic.

## How to Use

1. Open the `prompt.txt` file in this directory.
2. Copy the content below the `---START---` line.
3. Replace the parts in `[...]` with actual data:
   - **Project / Feature name** to be tested
   - **System context** (brief description of the current app)
   - **MVP goal** (scope of this testing round)
   - **Attached documents** (Requirements, User Stories, Figma, PDF...)
4. Paste into an AI chat and send.
5. Wait for the AI to confirm **"I understand the context and am ready"** before moving to Step 2.

## Notes

- The more detailed the documents → the more accurate the AI's analysis.
- If there is a Figma or screenshot, additionally describe the key UI elements.
- This step only needs to be run **once** at the beginning of the conversation.