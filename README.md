# anam-telemetry-docs

Ana M **telemetry** documentation — public source of truth for the event catalog, identity, destinations, naming, isolation, and live/in-review status.

**Not** the canonical product docs repo (`Ana-M-Docs` / `anam-docs`).

## Start here

→ **[`telemetry/README.md`](./telemetry/README.md)** — index completo (o que vive onde, status legend, como adicionar evento, links).

| Path | Role |
| --- | --- |
| [`telemetry/events.yaml`](./telemetry/events.yaml) | Machine SoT (catalog `0.5.0`) |
| [`telemetry/events.md`](./telemetry/events.md) | Human-readable mirror |
| [`telemetry/status-checklist.md`](./telemetry/status-checklist.md) | Live vs in review (Mixpanel/GA liberation) |
| [`telemetry/roadmap.md`](./telemetry/roadmap.md) | Waves / backlog / backfill |
| [`telemetry/naming.md`](./telemetry/naming.md) | Locked naming rules |
| [`telemetry/identity.md`](./telemetry/identity.md) | People / user_properties / Intercom / Sentry |
| [`telemetry/destinations.md`](./telemetry/destinations.md) | Vendor roles, allowlist, geo/Replay |
| [`telemetry/isolation.md`](./telemetry/isolation.md) | Must not block auth / payments / access |
| [`telemetry/contributing.md`](./telemetry/contributing.md) | How to add an event |

Cursor rules: [`.cursor/rules/telemetry-isolation.mdc`](./.cursor/rules/telemetry-isolation.mdc), [`.cursor/rules/telemetry-english-naming.mdc`](./.cursor/rules/telemetry-english-naming.mdc).

**Consumer:** [`Ana-M-APP/anam-telemetry`](https://github.com/Ana-M-APP/anam-telemetry) / monorepo `packages/telemetry` → `@anam/telemetry`.

### Versioning

1. Edit `telemetry/events.yaml` (bump `catalog.version`) and keep [`status-checklist.md`](./telemetry/status-checklist.md) in sync for live vs suspended destinations.
2. Sync into anam-telemetry → regenerate `generated.ts`.
3. Publish `@anam/telemetry@x.y.z` (GitHub Packages).
4. Monorepo apps pin the package version.

Do **not** parse Markdown for codegen — YAML only. Checklist/roadmap Markdown is the team SoT for liberation decisions.
