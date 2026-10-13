# MAGRO Decision Log

**Status:** Decisions 1–36 recorded. User instructed that recommended defaults should be accepted automatically; do not ask for approval on each routine design choice. Planning only; coding is not yet authorized.

## Identity and records
1. Permanent Farmer ID format: `MAG-YYYY-000001`; unique, stable, never reassigned.
2. RSBSA is required before registration completion; duplicate matching flags likely duplicates and never silently merges records.
3. Each farm is associated with one farmer; one farmer may have multiple farms.
4. Crop records are historical by season; preserve previous crop records.

## Programs and interventions
5. Applications have explicit states, auditable transitions, required return/rejection reasons, and no self-approval. Approval and delivery are distinct.
6. Only Administrator edits eligibility rules; preserve rule versions.
7. Previous-assistance and gap rules are Administrator-configured; calculate a gap only when an applicable rule exists.
8. Administrator and designated Municipal Agriculturists may configure permitted sensor thresholds/calibration; audit changes.
9. Valid configured sensor threshold crossings automatically SMS the configured recipients; staff review the alert afterward. No equipment control.
10. Failed SMS retries a configurable number of times then flags for staff; avoid duplicate logical notifications.
11. Sensor alert recipients use geographic matching plus manual assignment/review when configuring recipient groups.
12. Exact farm coordinates are permission-controlled consistently across maps, search, reports, and exports.
13. Recognized inbound replies are processed automatically; unclear replies are flagged. Major transaction changes require explicit mapping rules. Repeated replies are deduplicated.
14. Validate readings before alerting; suspicious/invalid readings cannot trigger farmer SMS.
15. Detect device outages by configurable no-reading timeout; internal staff alert only, never threshold SMS based on missing data.
16. GIS Shapefile/KML/KMZ uploads validate and preview before authorized publication; failed/cancelled uploads leave current layer unchanged.
17. Excel imports validate and preview, then require explicit confirmation; no silent duplicate merging.
18. Scheduled database backups; Administrator-only confirmed restore; audit and post-restore verification.
19. Backups are in protected storage separate from the live database; no geographically separate second copy selected.
20. Role-based permissions; backend enforcement and least privilege; no per-user exceptions by default.
21. One authorized Administrator can make permitted sensitive configuration changes with appropriate confirmation and mandatory audit.
22. Archive/soft-delete operational records rather than routine hard deletion; preserve history.
23. Coverage uses unique farmers served divided by eligible/target population for same program, period, and barangay. ICDI is derived against a documented municipal benchmark; official formula must be sourced, not invented.
24. SIM800L GSM modem/SIM integration for two-way SMS; queue outbound messages and report only supported status evidence.
25. Target a municipality-managed office server if suitable hardware, power, network, and administration are available.
26. Extended offline mode for user-authorized cached records, farmer/farm/crop changes, draft applications, and draft interventions; sensitive approvals and live SMS remain online-only.
27. User-scoped protected offline storage; revoke/expire access safely and recheck server permissions at sync.
28. Detect sync conflicts, preserve both versions, require manual resolution, audit the outcome.
29. Administrator-configurable cache expiry; pending unsynced work must be preserved for recovery.
30. Warn on sign-out with pending changes; preserve encrypted pending changes inaccessible to other users.
31. Offline records get globally unique local IDs retained through sync and linked to server IDs; Farmer ID remains separate.
32. Automatic synchronization when server is reachable; show results, preserve queue on failure, prevent duplicate processing; sync never approves or sends live SMS.
33. Offline applications sync as drafts, are revalidated, and require online review/submission.
34. Offline intervention records sync as drafts and require authorized online confirmation before official history.
35. Offline farmer/farm/crop records auto-sync with validation, RSBSA duplicate checks, permission enforcement, audit, and manual conflict resolution; never silently merge/overwrite.
36. Preserve actual crop/intervention event dates separately from local creation and server sync timestamps; flag invalid/inconsistent dates for review.

## Cross-cutting implementation defaults
- Frontend: React + TypeScript + Vite. Backend: Laravel + PHP. Database: PostgreSQL + PostGIS. Maps: Leaflet. Charts: Recharts. IoT edge: ESP32.
- Authentication/authorization is enforced server-side; audit consequential changes and sensitive access as required.
- Store protected documents outside public web paths; secrets stay out of source control.
- Use synthetic test data only. Do not use real farmer data in development or CI.
- Use explicit queueing for SMS, report jobs, scheduled backups, and sync processing where appropriate.

## Items that must not be guessed
- Official ICDI formula, rounding, benchmark semantics, and zero-denominator behavior.
- Official barangay boundaries and coordinate reference system.
- Real device payload/protocol, calibration/ranges/sampling/timeouts, and SIM800L inbound/delivery-status capability.
- Municipal server readiness, recovery objectives, retention periods, and performance/data-volume targets.

These are blockers only for the affected production features. Continue planning and implementation preparation for unaffected areas, but mark unresolved checks BLOCKED or NOT VERIFIED.
