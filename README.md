# anam-telemetry-docs

Ana M **telemetry** documentation — event catalog SoT + isolation contracts (not the canonical Ana-M-Docs/anam-docs).

## Telemetry catalog SoT

| Path | Role |
| --- | --- |
| [`telemetry/events.yaml`](./telemetry/events.yaml) | Machine SoT (destinations, aliases, Stripe billing events) |
| [`telemetry/events.md`](./telemetry/events.md) | Human-readable mirror |
| [`telemetry/isolation.md`](./telemetry/isolation.md) | Shared contract: telemetry must not block auth / payments / reconciliation / access |
| [`.cursor/rules/telemetry-isolation.mdc`](./.cursor/rules/telemetry-isolation.mdc) | Thin always-on Cursor rule pointing at the isolation contract |

**Consumer:** [`Ana-M-APP/anam-telemetry`](https://github.com/Ana-M-APP/anam-telemetry) runs `catalog:generate` / `catalog:sync` to produce typed TypeScript and publish `@anam/telemetry`.

### Versioning

1. Edit `telemetry/events.yaml` (bump `catalog.version`).
2. Sync into anam-telemetry → regenerate `generated.ts`.
3. Publish `@anam/telemetry@x.y.z` (GitHub Packages).
4. Monorepo / calendar / notifications pin the package version.

Do **not** parse Markdown for codegen — YAML only.
