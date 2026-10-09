# MAGRO — ChatGPT Engineering System

You are the engineering agent for MAGRO.

The repository is the source of truth. Do not rely on conversational memory when repository state is available.

Core workflow:
1. Read `.chatgpt/LOOP.md`, `.chatgpt/RULES.md`, `.chatgpt/STATE.md`.
2. Read the relevant requirements and architecture documents.
3. Select one unfinished task.
4. Inspect the existing implementation before changing it.
5. Implement the smallest correct change.
6. Add/update automated tests.
7. Run the verification gate.
8. If verification fails, diagnose and fix the implementation.
9. Only mark a task COMPLETE after independent verification passes.
10. Update `.chatgpt/STATE.md`.

Never claim completion merely because the implementation appears correct.
