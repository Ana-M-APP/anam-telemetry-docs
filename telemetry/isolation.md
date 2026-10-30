# Telemetry isolation from essential Ana M ops

**Status:** shared contract (SoT for agents and implementers)  
**Host:** `Ana-M-APP/anam-telemetry-docs` → [`telemetry/`](./)  
**Related:** monorepo root `AGENTS.md` hard rule (optional integrations vs app/billing); catalog SoT [`events.yaml`](./events.yaml); package `@anam/telemetry`

This contract specializes the monorepo optional-integration isolation rule for **product analytics and telemetry** (Mixpanel, GA4 client/MP, Intercom product attrs/sync, Sentry feedback, and any enrichment that exists only to emit analytics). It does **not** replace Stripe, Auth, Firestore financial/audit truth, or entitlement checks.

## Essential ops (must never depend on telemetry)

Authentication, account creation, payments, subscription reconciliation, and access to an entitled account remain authoritative and must complete regardless of analytics health. When choosing between degraded telemetry and blocking the product, **preserve the product** and retain/report the telemetry failure for independent recovery.

## Christian brief (normative)

1. **Telemetry must not block business.** Mixpanel / GA4 / Intercom / Sentry-feedback (and equivalent) calls, retries, locks, rate limits, or missing secrets must never be a prerequisite for auth, payments, reconciliation, or access. App/billing responses must not await vendor success.
2. **Enrichment and preparation are telemetry.** Identity graph wiring, property allowlisting/sanitization, `$insert_id` / dedupe prep, Group Analytics attachment, Measurement Protocol body building, and “warm the tracker” work that exists only to emit analytics are on the **telemetry path**. They inherit the same isolation rules as the HTTP emit itself.
3. **`catch` ≠ wait.** Wrapping a promise in `try/catch` (or `.catch`) does not isolate the product if the critical path still **awaits** the vendor or enrichment. Prefer finite deadlines, non-blocking enqueue, `void` fire-and-forget after local commit, or explicit timeouts that resolve without failing the business step.
4. **Best-effort ≠ guaranteed delivery.** Best-effort means the product may proceed with **no** analytics event. Limited retries and stable insert ids improve delivery; they do not create an SLA that billing or auth may depend on. Missing Mixpanel/GA4 data is an observability gap, not a product defect that warrants rollback.
5. **Analytics absence does not remove financial or audit records.** Stripe invoices, webhook processing, canonical subscription/access state, and financial/audit ledgers remain written and authoritative whether or not an analytics emit succeeded, was NoOp’d, or was dropped. Do not delete, rewrite, or skip financial truth because analytics failed or was absent.
6. **Failure tests are required on these paths.** Before approving related changes, test rejected, hung, and delayed telemetry; missing/invalid secrets (NoOp); enrichment throws; and timeout/rate-limit behavior. Assert that auth, payment, reconciliation, and access outcomes are preserved, including independent post-creation steps. Do not claim zero regression from a narrower happy-path suite.

## Implementation notes (reuse existing patterns)

- **Live by secret, NoOp when absent** — no `PUBLIC_*` / feature-toggle env to “enable” telemetry; absent or invalid credentials → safe NoOp (gold-standard / catalog workflow).
- **Authoritative surfaces** — Stripe + canonical billing/access state; telemetry may mirror allowlisted facts (e.g. `stripe_invoice_paid`) with dedupe (`insert_id` / Stripe Event.id) but never owns payment truth.
- **Clinical / PII** — isolation does not permit clinical payloads or default PII in analytics; see gold-standard privacy rules and package sanitize helpers.
- **Monorepo pointer** — keep a thin always-on Cursor rule that points here (and to root `AGENTS.md`); do not fork a second conflicting isolation essay inside apps.

## What this file is not

- Not the event catalog (names/destinations stay in `events.yaml`).
- Not permission to merge, deploy, repair production data, or activate gates.
- Not a substitute for monorepo `AGENTS.md` optional-integration wording for non-telemetry CRM/support channels.
