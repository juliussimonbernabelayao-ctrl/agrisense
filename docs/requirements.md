# AgriSense — MAGRO Requirements

**Organization:** Municipal Agriculture Office (MAGRO), Carmen, Davao del Norte  
**Status:** Updated planning baseline — implementation not yet authorized  
**Source of truth:** The approved AgriSense project brief.

## 1. Purpose and boundaries

AgriSense is a browser-based agricultural management and decision-support system with extended offline operation for selected staff workflows for authorized MAGRO personnel. It centralizes farmer, farm, crop, program, application, transaction, intervention, GIS, IoT monitoring, analytics, SMS, reporting, document, and administrative information.

Farmers are beneficiaries/data subjects and SMS recipients. Farmers do not receive administrative web accounts. MAGRO personnel retain final responsibility for decisions.

Out of scope: full inventory management, machinery lending, What-if Simulation, machine-learning predictions, automatic irrigation/pump control, farmer administrative login, public farmer maps, and document-expiration tracking. Offline support is limited to approved staff workflows; approvals and live SMS operations remain online-only.

## 2. User groups

- **Administrator:** one user with full administrative control.
- **Municipal Agriculturists:** two users with configurable management, review, approval, monitoring, analytics, communication, and reporting permissions.
- **Encoders:** two to five users with configurable data-entry and operational permissions.

The system must support multiple users working simultaneously.

## 3. Functional requirements

### REQ-001 — Authentication and access control (MUST)
Provide login, logout, forgot-password flow, account activation/deactivation, session timeout, user management, role assignment, and granular configurable permissions.

**Acceptance criteria**
- Unauthenticated users cannot access protected pages or API operations.
- Authorization is enforced server-side.
- The Administrator can change Encoder permissions without code changes.
- Farmers do not have administrative web accounts.
- User and permission changes are auditable.

### REQ-002 — Farmer management (MUST)
Generate a permanent Farmer ID in registration-year format (e.g. `MAG-2026-000001`) and retain the RSBSA number. Support create, view, edit, search, filter, archive/inactivate, history, documents, farms, crops, programs, transactions, interventions, GIS associations, monitoring associations, and SMS history. Farmer statuses: Active, Inactive, Deceased, Transferred, Invalid.

**Acceptance criteria**
- Farmer IDs are unique and permanent.
- RSBSA number is required before farmer registration is completed. Duplicate checks normalize and compare RSBSA number and other approved matching signals; potential duplicates are flagged and never silently merged.
- Offline-created records use a separate globally unique local ID until linked to the permanent server Farmer ID.
- Potential duplicates are shown before creating a record; obvious duplicates are not silently created.
- Historical records remain available for reporting after a farmer is inactivated.

### REQ-003 — Farms and crops (MUST)
A farmer may have multiple farms. Farm records support barangay, GPS coordinates, area, crop associations, history, interventions, monitoring points, and GIS display. Crop records preserve different growing periods instead of overwriting history.

**Acceptance criteria**
- A farmer can have multiple farms and each farm can have historical crop records.
- Invalid coordinates and area values are rejected.
- Old crop records remain queryable.

### REQ-004 — Programs, eligibility, applications, and transactions (MUST)
Manage programs, periods, targets, requirements, configurable eligibility rules, applications, transactions, and accomplishments. Eligibility results explain failed conditions and check previous assistance when a configured rule requires it. Support the planned MAGRO service types, including registration/validation, seed, fertilizer and other inputs, equipment/material, financial, training, technical, irrigation-related, disaster, certification, and other services.

Application workflow supports Draft, Submitted, Under Review, Returned for Correction, Approved, Rejected, Cancelled, and Fulfilled/completed states as applicable. Approval is distinct from assistance delivery; allowed transitions are enforced server-side. Transactions have unique references (e.g. `APP-2026-000001`). Offline applications sync as drafts and must be reviewed and submitted online.

**Acceptance criteria**
- Eligibility is driven by configured rules, not hard-coded assumptions.
- Invalid workflow transitions are rejected by the server.
- Approval, rejection, and return actions record actor, time, action, and applicable reason.
- Rejection and return require a reason.
- A user cannot approve their own transaction, even if they have approval permission.
- Transaction numbers are unique.

### REQ-005 — Interventions and farmer history (MUST)
Record interventions linked to farmer, farm, crop where applicable, program, type, date, quantity/unit, funding source, personnel, transaction/application, documents, and status. Show a chronological farmer intervention journey.

**Acceptance criteria**
- Intervention history is linked to relevant source records.
- Elapsed time since a prior intervention is calculated from recorded dates.
- A gap is flagged only when a valid program/MAGRO interval is configured; otherwise show elapsed time without inventing a rule.

### REQ-006 — IoT soil-moisture and water-level monitoring (MUST)
Support three selected soil-monitoring locations and selected water-level sites. Manage devices, monitoring points, calibration, readings, thresholds, last communication, and online/offline/inactive status. Soil thresholds may be crop-specific; water thresholds are site-specific. Validate and timestamp readings, store raw values, derive interpretations, create internal advisory/alert flags, and detect devices that exceed a configured timeout.

**Acceptance criteria**
- Device submissions are authenticated and payloads validated.
- Raw readings and derived statuses remain traceable.
- Soil interpretation uses configured crop rules and requires calibration.
- Water-level status uses site-specific warning/critical thresholds.
- Historical views support Today, Last 7 Days, Last 30 Days, and custom dates.
- Offline devices create internal notifications.
- Sensor devices never directly send SMS or control irrigation/pumps; the backend applies validated readings and configured threshold rules to queue approved automatic alert messages.
- A valid configured threshold crossing may automatically send SMS to the configured recipient list. Authorized staff review the alert afterward. Invalid or suspicious readings and missing-data/outage events never trigger threshold SMS.
- Failed SMS attempts are retried a configurable number of times, then flagged for staff follow-up; retries must not create duplicate logical notifications.
- Sensor-generated alerts never control irrigation/pumps or automatically create official intervention records.

### REQ-007 — GIS (MUST)
Provide authenticated layers for farmers, farms, barangay boundaries, soil and water monitoring, intervention distribution/coverage, program coverage, and flood-risk indicators. Support layer toggles, marker summaries, and authorized upload/update/activation of Shapefile, KML, and KMZ layers.

**Acceptance criteria**
- Farmer/farm locations are never publicly accessible.
- Layer visibility follows role permissions.
- Users can independently toggle layers.
- Uploaded layer status and validation results are visible to authorized administrators.

### REQ-008 — Coverage, ICDI, and analytics (MUST)
Calculate coverage from unique farmers served divided by eligible/target farmers for the same program, period, and barangay where applicable. Calculate ICDI as a derived comparison of barangay coverage against the municipal benchmark. Negative means below benchmark, zero equal, positive above. Support filters for barangay, program, intervention type, crop, and date range.

**Acceptance criteria**
- Metrics use actual recorded data, not hypothetical scenarios.
- Coverage counts unique farmers within the defined calculation scope.
- ICDI is derived, never manually entered.
- The exact ICDI formula, rounding, and zero-denominator behavior must be agreed before implementation.
- No What-if Simulation or machine-learning prediction is included.

### REQ-009 — Two-way SMS and notifications (MUST)
Support individual and bulk SMS through a SIM800L GSM modem/SIM integration, templates, inbox/outbox/history, replies such as CONFIRM/DECLINE/UNABLE, farmer-linked history, transaction linking where reliable, and in-system notifications for pending tasks, sensor flags, offline devices, and new replies. Queue outgoing SMS and track only delivery states supported by the modem/network. Recognized replies may be processed automatically; unclear replies go to staff review. Transaction state changes only under explicit configured rules.

**Acceptance criteria**
- Bulk SMS shows recipient count and requires explicit confirmation.
- Only authorized users can send SMS.
- Sensor threshold SMS follows the approved automatic alert policy and configured recipient list; staff review follows transmission. Invalid readings and outage-only events cannot trigger farmer SMS.
- Transaction status changes from a reply only when an explicit configured rule allows it.
- Provider credentials are stored as secrets, not in source control.

### REQ-010 — Reports and exports (MUST)
Provide farmer, farm, program, transaction, intervention, analytics/ICDI, monitoring, communication, and security reports. Support filters, preview, PDF and Excel, separate files, combined report packages, and saved report configurations.

**Acceptance criteria**
- Report filters apply to exported data.
- Exports respect the requesting user's permissions.
- Separate exports create one file per selected report.
- Combined exports include every selected report in separate sections/sheets.
- Saved configurations can be run again.

### REQ-011 — Documents, global search, and Excel import (MUST)
Attach documents to farmers, farms, programs, applications, transactions, and interventions. Provide a central document browser and global search across farmer ID, RSBSA, farmer, farm, program, transaction, intervention, document, SMS, and device. Support Excel import with column validation, duplicate detection, error preview, and explicit confirmation.

**Acceptance criteria**
- Document and search results respect authorization.
- Imports validate columns and data before committing.
- Duplicate candidates and invalid rows are shown before confirmation.
- Import results report created, skipped, and failed rows.
- Document-expiration tracking is not required.

### REQ-012 — Audit, backup, restore, and settings (MUST)
Record important actions with user, action, module, record, timestamp, and previous/new values where appropriate. Ordinary users cannot edit/delete audit records. Provide Administrator-only manual and scheduled backups, backup history/status, and a confirmed restore workflow. Make roles, permissions, transaction types, eligibility, thresholds, timeouts, SMS templates, barangay references, GIS layers, and preferences configurable where appropriate.

**Acceptance criteria**
- Important state changes and privileged actions create audit entries.
- Ordinary users cannot modify audit logs through the UI or API.
- Backup/restore is restricted and restore actions are logged.
- Secrets, backups, documents, and personal/geographic data are protected.
- Deployed environments use HTTPS.

### REQ-013 — Dashboard and search/list behavior (MUST)
Show KPIs for farmers, farms, active programs, pending applications, approved/completed transactions, farmers served, and active devices. Include charts for farmers/crops/barangays, interventions, program accomplishment, coverage, ICDI, and transaction status; monitoring summaries; recent activity; and permission-aware quick actions. Major lists support search, filters, sort, pagination, and authorized export.

**Acceptance criteria**
- Dashboard values are derived from stored data.
- Quick actions appear only when the user has permission.
- Lists and exports respect authorization and selected filters.

### REQ-014 — UI/UX and non-functional requirements (MUST)
Use a modern government/agriculture style with dark-green navigation, neutral content, clear headings, compact forms, status indicators, charts, responsive tables, global search, notifications, profile, and logout. Desktop office use is primary; tablet use should be practical. The system must be secure, reliable, maintainable, traceable, and able to scale as records and sensor history grow.

**Acceptance criteria**
- Core workflows work in current Chrome and Edge.
- Forms show clear validation, loading, empty, and error states.
- Consequential actions use suitable confirmation.
- Server-side permission checks, input validation, least privilege, secure transport, and protected secrets are implemented.
- Performance targets and expected data volumes are agreed before performance testing.

## 4. Proposed technical direction

The brief recommends React + TypeScript + Vite, Laravel + PHP, PostgreSQL + PostGIS, Leaflet, Recharts, ESP32, and SIM800L GSM/GPRS. Current planning defaults: municipality-managed office server if suitable; SIM800L GSM modem/SIM integration; authenticated device ingestion with validated payloads; protected separate backup storage; offline cache protected per user. Confirm physical prerequisites and deployment-specific values before production.

## 5. Decisions that must not be guessed

Before implementation, confirm:
1. Exact ICDI formula, rounding, and zero-denominator behavior.
2. Approved farmer fields, duplicate/RSBSA policy, and retention/deletion rules.
3. Allowed transaction state transitions and permissions.
4. IoT payload format, device authentication, calibration, valid ranges, and timeout.
5. SMS provider, delivery status, inbound reply mechanism, and consent/cost controls.
6. GIS coordinate system and trusted barangay boundary data.
7. Hosting, backup retention, recovery objectives, and performance targets.

## 6. Verification policy

A task is complete only after its acceptance criteria have been verified. Use automated tests where practical and retain test/build/lint/security-check evidence. Do not weaken requirements, disable tests, or remove authorization checks to get a pass. If a decision is unresolved or verification cannot run, mark it **BLOCKED** or **NOT VERIFIED**, not **PASS**.


## 7. Extended offline-mode requirements (MUST)

Offline operation is intended for authorized staff and is not a second authority for official decisions.

- Cache only records and fields the signed-in user is authorized to access; protect local data and lock/clear it when access is revoked where technically possible.
- Support offline viewing and creating/editing farmer, farm, and crop records; preparing applications; and preparing intervention records.
- Queue changes and synchronize automatically when the server is reachable. Preserve queued changes on failures and show sync progress/results.
- Use a globally unique local identifier for each offline-created record. Keep it when syncing and link it to the permanent server ID; do not substitute it for the permanent Farmer ID or RSBSA number.
- Validate changes and permissions again on the server. Duplicate RSBSA matches are flagged; never silently merge farmer records.
- Detect conflicts, preserve both versions, identify conflicting fields, and require authorized manual resolution. Do not silently overwrite. Keep unresolved records pending and audit resolution.
- Offline applications upload as drafts; server revalidates them. A user must review and submit online. Sync never approves an application or sends live SMS.
- Offline intervention records upload as drafts and require authorized online review/confirmation before becoming official assistance history. Preserve the actual event date separately from local creation and server synchronization timestamps.
- Automatic sync of farmer/farm/crop records does not bypass validation, duplicate checks, permissions, or audit logging.
- Cache expiry is Administrator-configurable. Expiry requires reconnection to refresh access, but pending unsynchronized changes must be preserved for recovery and cannot silently disappear.
- Signing out with pending changes warns the user. Pending changes remain encrypted and inaccessible to other users until the original user signs in or authorized recovery occurs.
- Sensitive approvals and live SMS operations remain online-only.

## 8. Confirmed governance decisions

- One Administrator, two Municipal Agriculturists, and two to five Encoders; farmers are beneficiaries/SMS recipients, not administrative account holders.
- One farmer owns each farm in the current model; a farmer may have multiple farms. Crop records are historical by season.
- Only Administrators edit eligibility rules; rule versions are preserved. Previous-assistance and gap checks run only when an applicable configured rule exists.
- Role-based permissions are enforced by the backend; no per-user exception model by default. Sensitive permitted configuration changes may be made by one authorized Administrator with confirmation where appropriate and mandatory audit logging.
- Operational records are archived/soft-deleted rather than routinely hard-deleted. Backups are scheduled and stored in protected storage separate from the live database; restoration is Administrator-only, confirmed, audited, and verified.
- Exact farm coordinates are permission-controlled consistently across maps, search, reports, and exports. GIS uploads are validated and previewed before publication. Excel imports are validated and previewed before confirmation.
- Coverage uses unique farmers served divided by the eligible/target population for the same program, reporting period, and barangay as applicable. ICDI is derived against a documented municipal benchmark; its exact official formula must be sourced from project documentation and must not be guessed.
- Preserve crop/intervention event dates separately from local creation and synchronization timestamps; flag invalid or inconsistent dates for authorized review.

## 9. Remaining implementation blockers and assumptions

The following must be sourced or validated before the affected production feature can be signed off: official ICDI formula/rounding/zero-denominator handling; official barangay boundary dataset and coordinate reference system; final sensor payload/protocol, calibration procedure, valid ranges, sampling interval, and timeout values; SIM800L network capability, inbound-reply mechanism and delivery-status limitations; municipality server hardware/network/power readiness; backup retention and recovery objectives; approved farmer fields and applicable data-retention policy; and performance targets/data volumes. These are not reasons to stop unrelated planning, but affected features must remain marked BLOCKED until confirmed.

## 10. Verification policy

A task is complete only after acceptance criteria are verified. Use automated tests where practical and retain build/lint/test/security-check evidence. Do not weaken requirements or disable checks to get a pass. If a required decision or verification is unresolved, mark the affected item BLOCKED or NOT VERIFIED, never PASS.
