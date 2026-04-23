# Step 1: Context Initialization (Context & Role-play)

**Workflow:** `/generate_automation_from_testcases`
**Skill:** `qa_automation_engineer`

---

## Purpose

Define the role of **Senior Automation Engineer** for AI and load technical context. This step helps AI know precisely:
- Which framework (Playwright / Selenium / Appium)
- Which language (TypeScript / Java)
- What the project architecture looks like
- Which coding principles must be followed

## Usage

1. Open the `prompt.txt` file.
2. Replace the parts in `[...]`:
   - **Tech Stack:** Choose framework, language, build tool
   - **Objective:** System/feature to be automated
   - **Context:** Web architecture, frontend technology, element specifics
   - **Architecture:** Copy from `0_project_architecture/README.md` or describe existing project
3. Send to AI and wait for confirmation → proceed to Step 2.

## Notes

- Choose the correct framework from the start — AI will generate code using that framework's syntax throughout.
- If the project already exists, describe the current structure so AI generates code into the correct directories.
- This step only needs to run **once** at the beginning of the conversation.