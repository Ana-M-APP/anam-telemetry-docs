# Changelog

## 0.7.0 — Calendar welcome and migration

- Add calendar welcome, setup and migration-window events with bounded labels. Six reach Mixpanel and GA4; setup failures stay `ops_only`. Committed start and completion stay in `anam_calendar_adoption_changed`.

## 0.6.5 — Account geography and Ana M 2.0

- Deliver Calendar use and committed adoption to Mixpanel and GA4 with the operation Account country and resolved hosting region. Support regional profiles and a person-level confirmed production connection marker. Exclude names, email addresses and phone numbers from GA4.

## 0.6.4 — Ana M 2.0 agenda rollout

- Add Mixpanel usage and server-confirmed adoption events with separate reporting for preview, legacy migration, and native Account creation.

## 0.6.1 — prepared 2026-11-05

- Add the approved, consent-gated website signup step funnel and strict property allowlists.
- Use `intended_billing_cycle` (1, 6, 12) with `intended_plan_key` on existing signup events; no new plan-selection event.
- Document Member-owned intention, signed Intercom attributes and website-owned pre-account Customer.io capture.

## 0.5.0 — 2026-11-03

### Added

- Approved signup channel and membership currency event/profile fields, bounded failure stages and explicit user/account deduplication semantics.

### Changed

- Reconcile the reviewed monorepo 0.4.4 catalog and human mirror, including existing prop allowlists, Group identity and subscription lifecycle events. Preserve live/HOLD decisions and existing event names.
