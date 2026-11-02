# Telemetry (SoT)

This directory is the **source of truth** for Ana M product analytics.

| Path | Role |
| --- | --- |
| [`events.yaml`](./events.yaml) | Machine event catalog (codegen) |
| [`events.md`](./events.md) | Human-readable event mirror |
| [`status-checklist.md`](./status-checklist.md) | Live vs in review per domain (Edit/Comment) |
| [`roadmap.md`](./roadmap.md) | Implemented waves, backlog, product review |
| [`isolation.md`](./isolation.md) | Isolation contract vs essential ops (auth, payments, reconciliation, access) |

`@anam/telemetry` consumes the YAML and publishes typed helpers. Edit the
catalog here first; then sync/regenerate in `Ana-M-APP/anam-telemetry` (or
monorepo `packages/telemetry`) before publishing a new package version.

Liberation policy: only checklist **`live`** events may set Mixpanel/GA
destinations; **`in review`** stays `destinations: none` until Christian
liberates. See [`status-checklist.md`](./status-checklist.md) and
[`roadmap.md`](./roadmap.md).

Isolation policy lives in [`isolation.md`](./isolation.md) — extend that
contract; do not fork conflicting copies into consumer apps.
