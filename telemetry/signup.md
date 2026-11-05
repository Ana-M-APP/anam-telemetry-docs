# Signup attribution and currency

Approved by Christian on 2026-11-03. Catalog and package version 0.5.0. This release reconciles the existing monorepo catalog/package 0.4.4 at develop e1455803020a4d84387cf2314967c5b3867398f0 before applying the approved additions.



`signup_channel` is `website` or `app` and is set by the owning server flow. `source` keeps its emitter meaning (`server` or `web`). Existing `origin` attribution is preserved independently. Historical missing channels stay absent and are reported as unknown.

`anam_signed_up` is a committed user/profile fact, emitted by the server with stable per-user deduplication. `anam_account_created` is a committed account fact with stable per-account deduplication. Both retain their current destinations. Failures use `anam_signup_failed` in Mixpanel and a bounded `stage` (`security`, `identity`, `profile`, `account`, `handoff`), with a safe `error_code`. Unknown network outcomes are recovery states.

`session_value` is the submitted major-unit amount. `session_currency` is BRL, EUR, GBP or USD, without conversion. `sessions_per_month` accepts the existing numeric value or the public signup's declared range.

Identity adds `anam_signup_channel` (original user acquisition channel) and `anam_member_session_currency` (active membership currency) alongside existing origin/profession/field/volume/value attributes. Intercom receives only signed identity attributes, never product event fan-out.

Success metrics count unique Ana M users by channel. Account creation and user signup are separate measures. Website form failure collection respects statistics consent, uses manual events, disables replay/autocapture and excludes field contents, passwords and tokens.

## Website step funnel — 0.6.1

Approved 2026-11-04. Emit `anam_signup_step_viewed` for each displayed visit, including revisits. Emit `anam_signup_step_completed` only after valid forward progress, including password and legal validation immediately before an account request. Emit `anam_signup_submitted` once per explicit request attempt. Disabled/busy double clicks emit nothing. Server `anam_account_created` and `anam_signed_up` remain the only successful creation facts.

The property allowlist is `step`, `step_index`, `locale`, `flow_version`, `signup_channel`. Step values for flow `website_signup_v2` are `email`, `contact`, `profession`, `sessions`, `session_value`, `terminology`, `approach`, `password`; positions are 0–7. `signup_channel` is `website`. Consent is required before initialization or emission. No replay, autocapture, email, phone, password, free text or clinical profile values are collected by this funnel. No retrospective replay after consent. Local development sends nothing.

Measure unique anonymous visitors and conversion by step, then elapsed progression. A last visible step without completion is an incomplete session, not a proven reason for abandonment. Page unload is unreliable and does not emit a new abandonment event. A refresh starts a fresh in-memory sequence.

Customer.io lead capture belongs to the website backend and remains separate from analytics consent. It runs only after the optional email marketing choice, enriches unverified contact details during the flow and stops at signup submission. The API stores the versioned channel choices privately. Account creation is not projected directly from the monorepo to Customer.io; the authenticated identity reaches it through the existing Intercom integration. Provider ingestion must be verified per environment before claiming delivery.

## Website pricing intent

The pricing CTA sends the existing GA4 `sign_up_click`. That event and the existing Mixpanel website funnel events and server `anam_account_created` may carry `intended_plan_key` and `intended_billing_cycle` as a pair. The current key is `anam-complete`. The billing cycle is a number of months, one of `1`, `6` or `12`. Generic signup omits both. These values do not represent a subscription selection, payment, trial entitlement, or confirmed conversion.

The Customer.io profile projection uses `anam_signup_intended_plan_key` and `anam_signup_intended_billing_cycle`. This is a separate server integration with its own consent rules. Contact details never pass through these analytics events.

## Canonical cycle and Member ownership — 0.6.1

Approved 2026-11-05. All new event properties use `intended_billing_cycle`, an integer in `1`, `6`, `12`.
The pair remains optional, is carried on existing signup events only, and never changes the active plan.
The API accepts the old `intended_billing_period` only for compatibility and emits canonical names.
The source is `Accounts/{accountId}/Members/{uid}.params.signupPlanIntent`. The historical private location
is retired for new writes. Intercom reads the active membership and receives the verified People CDAs
`anam_signup_intended_plan_key` and `anam_signup_intended_billing_cycle`. No clinical content is included.

Pre-account Customer.io capture belongs to the website backend, identified by email, only after the
separate communication choice. It receives incremental unverified name/phone and intention attributes;
final submission stops website capture. Account identity after login follows the existing Intercom integration.
The monorepo's retired `WebsiteSignupCustomerIo` trigger must be removed during staging cutover.
