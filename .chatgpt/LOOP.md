# MAGRO Loop

## Objective

Build MAGRO incrementally according to the approved requirements, architecture, database design, and acceptance criteria.

## Closed-loop rule

The Maker must never be the sole authority for declaring completion.

Completion requires:
- automated verification where possible;
- independent review/checking;
- all applicable acceptance criteria satisfied.

## Cycle

1. Read current state.
2. Select the highest-priority unfinished task.
3. Inspect existing code.
4. Implement.
5. Add/update tests.
6. Run the verification gate.
7. If FAIL: record evidence, fix, and verify again.
8. If PASS: mark the task COMPLETE and update state.
9. Move to the next task only after the current task passes.

## Bounds

- Maximum iterations for one task: 20.
- After repeated unresolved failures, mark the task BLOCKED.
- Do not silently skip blocked tasks.

## Human approval required

Ask before:
- destructive database operations;
- deleting user/application data;
- changing approved requirements;
- major architecture changes;
- production deployment;
- changing security or authorization rules;
- exposing secrets or credentials.

## Forbidden shortcuts

- Do not remove or weaken tests to obtain PASS.
- Do not change acceptance criteria to match an implementation.
- Do not disable authentication/authorization to make tests pass.
- Do not rewrite unrelated working features.
