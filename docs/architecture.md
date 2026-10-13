# AgriSense — System Architecture

**Project:** Online MAGRO Agricultural Management and Decision Support System  
**Organization:** Municipal Agriculture Office (MAGRO), Carmen, Davao del Norte  
**Status:** Updated proposed architecture — planning decisions recorded; implementation gate still closed

## 1. Confirmed decisions

- Browser application with extended offline mode for selected staff record workflows; sensitive approvals and live SMS operations remain online-only.
- Frontend: React + TypeScript + Vite.
- Backend/API: Laravel + PHP.
- Relational database: PostgreSQL.
- Spatial database capabilities: PostGIS.
- GIS display: Leaflet.
- Charts: Recharts.
- IoT edge devices: ESP32-based sensor nodes.
- SMS integration: SIM800L GSM modem with SIM card, subject to field/network capability validation.
- Hosting target: municipality-managed office server if hardware, power, network, and administration prerequisites are met.

Extended offline mode is in scope for cached records, farmer/farm/crop edits, draft applications, and draft interventions. A protected local queue and explicit conflict-resolution flow are required.

## 2. Logical architecture

```text
MAGRO staff (Administrator, Municipal Agriculturists, Encoders)
                         |
                         v
                 Web browser (HTTPS)
                         |
                         v
       React + TypeScript + Vite frontend
       - Dashboard and navigation
       - Farmer/farm/crop records
       - Programs, applications, transactions
       - Interventions and history
       - Monitoring and GIS views
       - SMS, notifications, reports
                         |
                  HTTPS / JSON API
                         |
                         v
              Laravel + PHP backend
       - Authentication and sessions
       - Server-side authorization
       - Request validation
       - Business rules and workflow
       - Eligibility and previous-assistance checks
       - Transaction approval/self-approval prevention
       - IoT ingestion and interpretation
       - GIS layer/data access
       - SMS adapter and reply processing
       - Report generation and exports
       - Audit events and administrative operations
                         |
                         v
                 PostgreSQL + PostGIS
       - Core operational records
       - Spatial records and geometry
       - Sensor readings and status history
       - SMS and notification records
       - Documents metadata and audit logs
```

External connections are mediated by the backend or a controlled integration service:

```text
ESP32 sensors -> authenticated IoT ingestion endpoint -> validation/rules -> database
Database + validated readings + configured threshold -> alert + automatic SMS to configured recipients -> staff review
Laravel backend <-> chosen SMS provider/module -> outbound messages and inbound replies
Authorized GIS uploads -> validation/import process -> PostGIS
```

## 3. Architectural principles

1. **Backend is the security boundary.** The browser never connects directly to PostgreSQL.
2. **Server-side authorization.** Hiding a button in the frontend is not security. Every protected API operation checks permissions.
3. **Separate records by entity.** Farmer, farm, crop, program, application, transaction, intervention, monitoring point, reading, SMS, document, and audit record are separate related entities.
4. **Traceability.** Derived eligibility results, monitoring statuses, coverage metrics, and ICDI must be traceable to source records and configured rules.
5. **Human decision authority.** Valid configured threshold crossings can send automatic SMS to the configured recipient list; staff review the alert afterward. Sensors never control irrigation/pumps or automatically create official interventions.
6. **Configurable policy.** Eligibility, role permissions, soil thresholds, water thresholds, and monitoring timeout are configured by authorized users.
7. **Audit important changes.** Record sensitive or consequential actions with actor, time, target record, action, and relevant before/after values.
8. **No silent data loss.** Use database transactions and clear error handling for multi-step operations.
9. **Offline safeguards.** Local data is user-scoped and protected. The server rechecks permissions and validation during sync. Conflicts are never silently overwritten; offline applications and interventions sync as drafts.
10. **Secrets stay server-side.** Database credentials, device secrets, and SMS credentials must not be exposed to frontend code or committed to Git.

## 4. Frontend responsibilities

The React application renders the user interface and calls the backend API. It may perform convenience validation for usability, but the backend remains authoritative.

Main interface areas:
- Dashboard
- Farmer, farm, and crop records
- Programs, eligibility, applications, transactions, and interventions
- Soil/water monitoring and device management
- GIS map and layer controls
- Intervention analytics, coverage, ICDI, and journey
- SMS inbox/outbox and notifications
- Reports and documents
- User/role management, audit logs, backup/restore, and settings

Use Leaflet for maps and Recharts for chart visualization. Exact UI component libraries and frontend state-management choices remain open until implementation planning.

## 5. Backend responsibilities

Laravel provides versioned or consistently structured API routes, authentication, authorization policies, request validation, business services, persistence, background processing where needed, and audit recording.

Important backend rules:
- Reject unauthorized access regardless of frontend behavior.
- Prevent users from approving their own transactions.
- Enforce allowed transaction state transitions.
- Require reasons for rejection/return actions.
- Apply eligibility only from configured rules.
- Preserve raw IoT readings and record derived interpretation separately or in a traceable form.
- Apply the configured sensor alert policy: validated threshold crossings may trigger automatic SMS; invalid readings and outage-only events must not trigger farmer threshold SMS.
- Enforce permissions on search, exports, document access, GIS, backup, and restore.

The exact authentication mechanism, API versioning, queue/worker setup, and file-storage service are not finalized.

## 6. Data architecture

PostgreSQL stores relational business records. PostGIS supports geographic features and spatial queries. The database design should use primary/foreign keys, unique constraints, check constraints, indexes, and transactions as appropriate.

Expected entity groups:
- Identity/access: users, roles, permissions, role_permissions
- Agriculture: farmers, farms, crop_records, barangays
- Programs/workflow: programs, program_requirements, eligibility_rules, program_targets, applications, transactions, interventions
- Monitoring: monitoring_points, iot_devices, sensor_readings, soil_moisture_rules, water_level_thresholds, advisories, alerts
- GIS: gis_layers, spatial features/geometries
- Communication: sms_messages, sms_replies, sms_templates, notifications
- Files/output: documents, reports, saved_report_filters
- Governance: audit_logs, backup_records, system_settings

This is an initial entity inventory, not a finalized schema. Relationships, fields, retention policies, and indexes belong in `docs/database.md`.

## 7. IoT data flow

1. A device submits a payload to the designated IoT endpoint.
2. The backend authenticates the device and validates the payload.
3. The backend resolves the device and monitoring point.
4. The reading is timestamped and stored with source/device information.
5. Configured calibration and thresholds are applied.
6. The system records an interpreted status and creates an internal flag when required.
7. Authorized MAGRO staff review the flag.
8. A valid configured threshold crossing can queue automatic SMS to the configured recipients; staff review the alert afterward. Invalid readings and outage-only events cannot trigger farmer SMS.
9. Device last-seen status is updated; configured timeout logic can mark a device offline.

Implementation parameters still to validate: transport/protocol, payload schema, device identity/provisioning, calibration method, accepted ranges, sampling interval, timestamp source, and timeout duration. Invalid/suspicious readings cannot trigger SMS; missing readings trigger internal device-offline alerts only. Failed SMS is retried a configurable number of times, then flagged for staff follow-up.

## 8. SMS data flow

1. An authorized user selects a template or writes a message.
2. The backend checks permission, validates recipients/content, and previews bulk recipient count.
3. The user explicitly confirms bulk transmission.
4. A backend SMS adapter sends through the selected provider or hardware integration.
5. Provider outcomes and message IDs are recorded when available.
6. Inbound replies are authenticated/validated as supported by the chosen mechanism and linked to a farmer/transaction when reliably identifiable.
7. Transaction state changes only when an explicit configured rule permits them.

The selected integration direction is a SIM800L GSM modem with SIM card. Validate local carrier support, inbound SMS reading, message queue behavior, cost controls, notice/consent and opt-out policy. Distinguish modem acceptance from confirmed handset delivery; report only status evidence available from the hardware/network.

## 9. GIS data flow and privacy

- GIS is available only to authenticated users with appropriate permissions.
- Exact farmer/farm locations are never exposed publicly.
- GIS data uploads are validated before being stored or published.
- Spatial layers and records are served through permission-checked backend endpoints.
- Coordinate reference system, trusted barangay boundary source, and import implementation must be confirmed.

## 10. Reports and documents

Report generation must apply the same filters and access rules as the underlying data. PDF/Excel exports must not expose fields the user cannot view. Large reports may require background jobs, but the queue/worker and storage choices are not yet finalized.

Documents should be stored in protected storage; the database stores metadata and links. Direct public file URLs are not acceptable for protected farmer documents.

## 11. Deployment and operations

Hosting target is a municipality-managed office server, provided hardware, reliable power/network, security administration, and maintenance are available. Before production deployment, validate:
- application hosting/runtime for Laravel and frontend assets;
- managed or self-managed PostgreSQL/PostGIS;
- HTTPS/domain and secret management;
- file/object storage;
- queue/worker and scheduled-task mechanism;
- automated backup frequency, retention, and restore testing;
- logging, monitoring, and error reporting;
- deployment and rollback procedure;
- production data access and recovery objectives.

Do not deploy with placeholder secrets, public database access, or disabled authorization.

## 12. Verification gate

The repository verification gate should eventually run, as applicable:
1. frontend dependency/type/lint checks;
2. frontend unit tests and production build;
3. backend dependency checks, formatter/linter, and static analysis if selected;
4. backend unit/feature tests against a test database;
5. migration/schema checks;
6. authorization tests, especially self-approval and restricted GIS/documents/exports;
7. IoT payload validation and rule tests;
8. report/export and SMS workflow tests using mocks or sandbox services;
9. dependency/security checks;
10. end-to-end smoke tests for critical workflows.

The exact commands cannot be finalized until the application code and dependency scripts exist. A verification gate must fail if required checks are missing or fail; it must never report PASS merely because tests were skipped.

## 13. Implementation defaults and blockers

**Defaults selected for planning:** Laravel backend authorization; React + TypeScript + Vite; PostgreSQL/PostGIS; Leaflet; Recharts; municipality-managed office server if ready; SIM800L GSM/SIM integration; protected file storage; background queue for SMS/reports/scheduled backups; scheduled database backups to protected storage separate from the live database; user-scoped encrypted offline cache; automatic sync for farmer/farm/crop records; draft-only sync for applications/interventions; manual conflict resolution.

**Must be validated before affected production features are signed off:** official ICDI formula and zero-denominator policy; official barangay boundary data and coordinate reference system; sensor protocol/payload/calibration/ranges/timeout; inbound SMS mechanism and actual delivery-status capabilities; local server readiness; backup retention and recovery objectives; password reset delivery; file size/type and malware scanning; performance targets and expected data volumes. Use safe placeholders in development, not real farmer data or production credentials.

## 14. Architecture status

**Status: PROPOSED — PLANNING BASELINE UPDATED; NOT YET APPROVED FOR IMPLEMENTATION.**  
Do not start feature implementation until this architecture, the database design, unresolved decisions, and verification plan have been reviewed.
