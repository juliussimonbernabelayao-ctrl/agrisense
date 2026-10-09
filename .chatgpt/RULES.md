# MAGRO Engineering Rules

## Code
- Preserve the existing architecture unless a change is justified.
- Prefer small, reviewable changes.
- Keep business logic testable.
- Avoid unnecessary dependencies.

## Testing
- Every feature should have appropriate automated tests.
- Test both success and failure paths.
- Include authorization tests for protected functionality.
- Regression tests are required when fixing bugs.

## Security
- Never hard-code secrets.
- Never commit credentials, tokens, or private keys.
- Validate untrusted input.
- Enforce authorization server-side.
- Never treat client-side restrictions as security boundaries.

## Data
- Preserve data integrity.
- Never use destructive migrations without explicit approval.
- Keep auditability for important state-changing operations.

## Completion
A task is not complete until the verification gate passes.
