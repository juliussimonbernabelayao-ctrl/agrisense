# MAGRO Implementation Plan

**Status:** Planned phases; do not begin feature coding until the readiness gate in Phase 0 is passed.

## Phase 0 — Planning readiness (current)
- [ ] Review requirements, architecture, database model, decision log, and verification plan as a consistent set.
- [ ] Resolve/supply official ICDI formula and denominator/rounding behavior before implementing ICDI.
- [ ] Identify authoritative barangay boundary data and coordinate reference system before publishing official GIS layers.
- [ ] Confirm municipal server hardware/network/power/security maintenance readiness.
- [ ] Bench-test ESP32 payloads, device authentication, sensor calibration, valid ranges, and timeout.
- [ ] Bench-test SIM800L network coverage, outbound queueing, inbound SMS reading, and supported status reporting.
- [ ] Define approved fields, retention rules, backup retention, RPO/RTO, and expected record volumes.
- [ ] Agree on environment setup and real verification commands; verify the gate fails correctly when checks are missing.

## Phase 1 — Repository and foundation
- Configure React + TypeScript + Vite and Laravel + PHP.
- Configure PostgreSQL/PostGIS, migrations, environment configuration, formatting, linting, tests, and CI.
- Establish API conventions, error shape, authentication, password reset, session timeout, RBAC, and audit logging.
- Add synthetic fixtures and seed only non-sensitive reference data.
- Exit criteria: clean build, passing baseline tests, protected API smoke tests, no secrets committed.

## Phase 2 — Core farmer and agriculture records
- Implement barangays/reference data, farmers, farms, crops, historical crop seasons, documents metadata, archive/restore.
- Implement permanent Farmer ID generation and required RSBSA duplicate detection.
- Implement global search and paginated/filterable lists with permission-aware exports.
- Exit criteria: constraints, duplicate warnings, historical preservation, access controls, and import validation tests pass.

## Phase 3 — Programs, applications, eligibility, interventions
- Implement program setup, versioned eligibility rules, prior-assistance rules, applications, transaction events, review/approval, delivery, interventions.
- Enforce no self-approval and required reasons for return/rejection.
- Implement draft review and official intervention confirmation.
- Exit criteria: workflow transition matrix, eligibility explainability, event history, and authorization tests pass.

## Phase 4 — Offline mode and synchronization
- Implement user-scoped encrypted cache, cache expiry, sign-out warning, and pending-change recovery.
- Add local UUIDs, sync batches/items, idempotency, server revalidation, permission checks, duplicate checks, and conflict snapshots.
- Automatically sync farmer/farm/crop records; sync applications/interventions as drafts only.
- Add manual conflict resolution UI and audit.
- Exit criteria: offline/reconnect/retry/conflict/revoked-permission tests pass; no silent overwrite, duplicate creation, approval, or SMS.

## Phase 5 — IoT and alerting
- Implement device registry, authenticated ingestion, payload validation, raw readings, calibration, threshold interpretation, timeout/offline alerts, and history charts.
- Add recipient assignment (geographic suggestion plus manual review), alert deduplication, automatic SMS trigger for valid threshold crossing, retry queue, and post-alert staff review.
- Exit criteria: invalid/suspicious readings and missing data cannot send farmer threshold SMS; threshold crossings and retries are traceable; no equipment control.

## Phase 6 — GIS
- Implement permission-checked spatial APIs, layers, farmer/farm points, monitoring points, program/intervention coverage, and flood-risk indicators.
- Add Shapefile/KML/KMZ validation, preview, and authorized publish/replace workflow.
- Load official boundary data only after source and CRS validation.
- Exit criteria: exact-location permissions are enforced across map, search, reports, and exports; failed uploads leave published layers unchanged.

## Phase 7 — SMS, notifications, reporting, analytics
- Complete inbound/outbound SMS, reply matching/deduplication, templates, consent/opt-out policy, cost guardrails, and staff notifications.
- Implement PDF/Excel single and combined reports and saved report configurations.
- Implement coverage formula from confirmed denominator rules. Implement ICDI only after its official formula is sourced.
- Exit criteria: permissions/filters carry into exports; status claims match hardware evidence; analytics tests use known fixtures.

## Phase 8 — Governance, backup, deployment, hardening
- Complete audit review, protected documents, scheduled backups, restore process, post-restore checks, logging, monitoring, HTTPS, and rollback plan.
- Perform security, privacy, performance, accessibility/usability, and end-to-end smoke tests.
- Validate deployment prerequisites and train authorized staff.
- Exit criteria: restore test succeeds, critical end-to-end tests pass, no unresolved high-severity security issues, and blocked features are clearly excluded from production.

## Change-control rule
A phase is not complete because code exists. It is complete only when the corresponding verification evidence and acceptance criteria pass. Do not weaken tests or requirements to force a pass.
