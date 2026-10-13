# MAGRO Planning Package

This package consolidates the current AgriSense/MAGRO planning baseline. It is not application source code and does not authorize feature implementation by itself.

## Documents
- `docs/requirements.md` — functional and non-functional requirements, including extended offline mode.
- `docs/architecture.md` — selected stack, integrations, flows, security boundaries, deployment assumptions, and blockers.
- `docs/database.md` — logical schema and required synchronization data model; not migration scripts.
- `docs/decision-log.md` — decisions 1–36 and implementation defaults.
- `docs/implementation-plan.md` — phased delivery sequence.
- `docs/verification-plan.md` — test strategy and release gate.

## Current status
Planning documents updated; coding remains paused until the readiness review is complete. Some source-specific production decisions must not be guessed, especially the official ICDI formula, GIS boundary/CRS, real sensor protocol/calibration, and tested SMS hardware capabilities.

Use synthetic data only. Never commit real farmer personal data, credentials, sensor secrets, or production backups.
