# Telemetry (SoT)

This directory is the **source of truth** for Ana M product analytics.

| Path | Role |
| --- | --- |
| [`events.yaml`](./events.yaml) | Machine event catalog (codegen) |
| [`events.md`](./events.md) | Human-readable event mirror |
| [`isolation.md`](./isolation.md) | Isolation contract vs essential ops (auth, payments, reconciliation, access) |

`@anam/telemetry` consumes the YAML and publishes typed helpers. Edit the
catalog here first; then sync/regenerate in `Ana-M-APP/anam-telemetry` before
publishing a new package version.

Isolation policy lives in [`isolation.md`](./isolation.md) — extend that
contract; do not fork conflicting copies into consumer apps.
