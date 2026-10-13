# MAGRO Verification Plan

**Current status:** Planning baseline only. Exact executable commands must be filled in after the application scaffold and dependencies are selected. Until then, the verification gate must report NOT READY rather than PASS.

## Verification layers

### 1. Repository/build checks
- Frontend install, type-check, lint, unit tests, production build.
- Backend dependency integrity, formatter/linter, static analysis where configured, unit/feature tests.
- Migration creation and fresh-database migration test.
- Dependency audit and secret scan.
- CI must fail when a required command or test suite is missing, not silently skip it.

### 2. Authorization/security tests
- Unauthenticated access is rejected.
- Every protected API operation checks server-side permission.
- Encoder cannot perform unassigned privileged actions.
- Self-approval is always rejected.
- Exact farm coordinates, farmer phone numbers, documents, search results, reports, and exports respect permissions.
- Audit records cannot be modified by ordinary users.
- Revoked users/permissions cannot continue using stale offline data to apply server changes.

### 3. Data integrity tests
- Farmer ID generation is unique under concurrent registration and uses `MAG-YYYY-000001`.
- RSBSA is required at registration completion; normalized duplicate candidates are flagged and not silently merged.
- A farm has one farmer; a farmer may have multiple farms.
- Crop history is appended/preserved; invalid date order is rejected or flagged according to policy.
- Approval and delivery are separate; application events and intervention history are preserved.
- Soft-delete/archive preserves historical reporting.

### 4. Workflow and eligibility tests
- Every allowed application transition succeeds and every forbidden transition fails.
- Return/rejection requires a reason.
- Eligibility evaluation records the rule version and explains failed conditions.
- Previous-assistance and gap logic runs only when an applicable configured rule exists.
- Offline applications sync as drafts and cannot become submitted/approved merely due to sync.
- Offline interventions remain drafts until authorized online confirmation.

### 5. Offline/sync tests
- Create/edit farmer/farm/crop records offline; reconnect; verify automatic synchronization.
- Retry a sync batch multiple times; verify idempotency and no duplicate records.
- Duplicate RSBSA found on server is flagged; no silent merge.
- Concurrent server and client edits preserve both versions and require manual resolution.
- Failed sync preserves pending queue and displays a useful error.
- Permission revoked while offline is rechecked at sync and unauthorized changes are rejected.
- Cache expiry does not silently delete pending work.
- Sign-out with pending changes warns and keeps data inaccessible to another user.
- Actual event date, local creation time, server receipt time, and sync time remain distinct.

### 6. IoT and SMS tests
- Valid device identity and payload accepted; unknown device or invalid payload rejected.
- Calibration and threshold interpretation are deterministic and traceable.
- Invalid/suspicious reading never triggers farmer SMS.
- Missing readings trigger an internal device-offline alert only.
- A configured valid threshold crossing queues one logical SMS to the configured recipients.
- Retry failures do not duplicate logical notifications; exhaustion flags staff follow-up.
- Inbound recognized replies process once; repeated replies are deduplicated; unclear replies enter staff review.
- Major application status changes occur only through explicitly configured reply rules.
- Delivery UI distinguishes modem acceptance from handset delivery when the latter is not provable.
- No test or production path can control irrigation/pumps.

### 7. GIS/import/report tests
- Invalid GIS uploads fail validation; cancelled/failed replacements do not alter published layers.
- Preview is required before authorized layer publication.
- Exact-coordinate permission is enforced in APIs, maps, search, and exports.
- Excel import shows row errors/duplicates before explicit confirmation and reports created/skipped/failed rows.
- Report filters and permissions are identical between preview and export.
- Separate exports create separate files; combined exports include each report as a separate sheet/section.
- Coverage fixture confirms unique farmer counting and same-period denominator.
- ICDI tests cannot be finalized until the official formula is sourced and approved.

### 8. Backup/restore and operations tests
- Scheduled backup success/failure is recorded and staff are notified as configured.
- Backups are stored separately from the live database with restricted access.
- Only authorized Administrator can restore; explicit confirmation and audit entry required.
- Restore test verifies schema, records, and application startup. A backup is not verified until a restore succeeds.
- HTTPS, protected secrets, logging, monitoring, and rollback are checked before production.

## Release gate

A release may be marked READY only when all applicable critical checks pass, the build/test evidence is retained, no required checks are missing, and all unresolved blockers are explicitly excluded from release scope. ICDI and official GIS boundary publication remain blocked until authoritative definitions/data are available.
