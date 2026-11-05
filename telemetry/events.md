# Event catalog (human mirror)

**Catalog version:** `0.6.1`
**Machine SoT:** [`events.yaml`](./events.yaml) — typed codegen reads YAML only.
**Repo:** `Ana-M-APP/anam-telemetry-docs` (authoritative). Consumer package: `@anam/telemetry`.

**Policy:** only Christian-liberated checklist `live` events fan out to Mixpanel/GA4.
HOLD / in-review catalog rows use `destinations: none` (suspended).
**Prop allowlist (0.4.3):** each live event declares `props:` in YAML. Mixpanel/GA
adapters strip every host prop not on that list before track. Adapter envelope
(`environment`, `source`, `event_version`) and Mixpanel Group key
`anam_account_id` (list) are attached after the allowlist — never camel
`accountId` on product events.

## Customer + sessions (`0.4.0`)

`customer` = attended party (handbook/patient in current health product). Soft-delete session = `archived`. Session completeness = clinical record generation (`enhance` module → `anam_clinical_record_*`). Approving a registration form also emits `anam_customer_created`. No clinical content / patient IDs in props.

| Event | Destinations | Props |
| --- | --- | --- |
| `anam_customer_created` | both (BE SoT) | `account_id`, `source` |
| `anam_customer_create_failed` | Mixpanel | `account_id`, `error_code`, `error_message` |
| `anam_customer_updated` | Mixpanel | `account_id`, `fields_changed?` |
| `anam_customer_archived` | Mixpanel | `account_id` |
| `anam_customer_unarchived` | Mixpanel | `account_id` |
| `anam_customer_registration_form_approved` | Mixpanel | `account_id` |
| `anam_customer_registration_form_shared` | Mixpanel | `account_id`, `channel?` |
| `anam_session_created` | both (BE SoT) | `account_id`, `batch?`, `count?` |
| `anam_session_create_failed` | Mixpanel | `account_id`, `error_code`, `error_message` |
| `anam_session_updated` | Mixpanel | `account_id` |
| `anam_session_archived` | Mixpanel | `account_id`, `batch?`, `count?` |
| `anam_session_payment_updated` | Mixpanel | `account_id`, `payment_status` |
| `anam_clinical_record_requested` | Mixpanel | `account_id` |
| `anam_clinical_record_created` | both (BE SoT) | `account_id` |
| `anam_clinical_record_failed` | Mixpanel | `account_id`, `error_code` |
| `anam_session_recording_started` | Mixpanel (FE) | `account_id` |
| `anam_session_recording_completed` | Mixpanel (FE) | `account_id`, `duration` |
| `anam_session_transcription_completed` | Mixpanel (BE) | `account_id` |
| `anam_session_exported` | Mixpanel (FE) | `account_id`, `export_type` |

## Wave-1 product events (`anam_*`)

| Event | Destinations | Props (snake_case; may omit `anam_`) |
| --- | --- | --- |
| `anam_account_created` | both (BE SoT) | `source`, `account_id`, `profession?`, `field_of_work?`, `sessions_per_month?`, `session_value?`, `session_currency?`, `signup_channel?`, `intended_plan_key?`, `intended_billing_cycle?` |
| `anam_account_create_failed` | Mixpanel | `message`, `error_code` |
| `anam_user_email_change_requested` | Mixpanel | (none) |
| `anam_user_email_changed` | Mixpanel | (none) — NEW on confirm |
| `anam_user_password_changed` | Mixpanel | (none) |
| `anam_easter_egg` | both | `theme` (e.g. `halloween`) |
| `anam_login_completed` | both | (none); GA4 native `login` |
| `anam_login_failed` | Mixpanel | `error_code`, `error_message` |
| `anam_password_reset_email_sent` | Mixpanel | (none) |
| `anam_password_reset_completed` | Mixpanel | (none) |
| `anam_signed_up` | both (BE SoT) | `source`, `origin`, `signup_channel`; GA4 native `sign_up` |
| `anam_signup_failed` | Mixpanel | `source`, `origin`, `error_code`, `error_message`, `signup_channel`, `stage` |
| `anam_billing_invoice_paid` | both | `amount`, `currency`, `account_id`, `stripe_customer_id`, `source`; GA4 `purchase` |
| `anam_billing_invoice_payment_failed` | both | same billing props; no `plan` / `plan_id` |
| `anam_subscription_trial_started` | both | `operation`, `account_id`, `user_id`, `stripe_customer_id`, `source`, `reason` |
| `anam_subscription_trial_ended` | both | `account_id`, `stripe_customer_id`, `source`, `reason` (`expired` \| `converted`) |
| `anam_subscription_paid_started` | both | `account_id`, `stripe_customer_id`, `source`, `reason` |
| `anam_subscription_paid_ended` | both | `account_id`, `stripe_customer_id`, `source`, `reason` (`canceled` \| `unpaid` \| …) |

## Stripe subscription lifecycle stubs (HOLD)

| Event | Destinations | Notes |
| --- | --- | --- |
| `stripe_subscription_created` | none | HOLD — funnel events above are the liberated surface |
| `stripe_subscription_updated` | none | HOLD |
| `stripe_subscription_deleted` | none | HOLD |

Funnel + identity: `emitSubscriptionLifecycle` / host `emitSubscriptionProductTelemetry`.
Invoice billing: `trackStripeWebhookBilling` / `trackStripeBillingEvent`.

## Identity

Ana M UID = Mixpanel `distinct_id` / `$user_id` = GA4 `user_id` = Sentry `user.id`.
Account = Mixpanel Group key `anam_account_id` — never person identity.
Server HTTP attaches Group as `anam_account_id: [accountId]` (list = Group Analytics
wire form — expected in Live UI). Catalog event prop `account_id` is sent **only**
when that event’s `props` allowlist includes it. Never both camel `accountId` and
snake `account_id`. Conflicts → `anam_account_group_conflict` (no silent pick).
Intercom People `anam_account_id` ≠ Companies `company.company_id` (JWT nests
Company = Account ID). Intercom receives **identity / Companies only** — no
product event fan-out.
**Rule:** Ana-invented identity props use `anam_*`. Event props may omit `anam_`.
Portuguese in identifiers is forbidden. Platform reserved names stay vendor-native.

### Mixpanel People (`people.set` on identify)

Send **only** when non-empty (omit undefined / `''` / literal `"undefined"`):

| Prop | Kind | Source |
| --- | --- | --- |
| `$name` | Mixpanel reserved | `name` / `userName` |
| `$email` | Mixpanel reserved | `email` / `userEmail` |
| `$phone` | Mixpanel reserved | `phone` / `phoneNumber` |
| `$created` | Mixpanel reserved | `signupAt` / `userCreatedAt` |
| `anam_user_id` | Ana custom | Ana M UID |
| `anam_account_id` | Ana custom | Selected account (also Group key) |
| `anam_signup_channel` | Ana custom | Original server-assigned website/app channel |
| `anam_member_session_currency` | Ana custom | Currency of the active membership session value |
| `anam_signup_origin` | Ana custom | `Users.origin` |
| `anam_last_login_at` | Ana custom | ISO8601 `Users.lastLoginAt` |
| `anam_member_profession` | Ana custom | Active membership.params.profession |
| `anam_member_field_of_work` | Ana custom | Active membership.params.fieldOfWork |
| `anam_member_sessions_per_month` | Ana custom | Active membership.params.sessionsPerMonth |
| `anam_member_role` | Ana custom | `owner` \| `assistant` from membership.role |
| `anam_member_session_value` | Ana custom | Active membership.params.sessionValue |
| `anam_billing_provider` | Ana custom | Gateway (`stripe`, …) — Group SoT; People mirror |
| `anam_plan_key` | Ana custom | Stable plan key (`lookup_key` / Ana code) |
| `anam_billing_price_id` | Ana custom | Native provider price id |
| `anam_plan_name` | Ana custom | Display label only |
| `anam_subscription_status` | Ana custom | Stripe-like status on the **account** |

Same `anam_*` identity names as GA4 (and Intercom People for membership attrs).
Subscription commercial attrs mirror to Intercom **Company**, not People.
Do **not** emit `language` on People.
Email/name/phone belong on **People**, not on product event properties.
Mixpanel browser geo is on (`ip: true`) for Country/Region/City. Session Replay
and autocapture use privacy-safe defaults (mask text/inputs; no clinical
element text). GA4 browser geo depends on GTM + consent on the same `G-`
stream. Server GA4 MP `ip_override` / Mixpanel `ip` only on user-facing API
tracks with a real client IP — never Stripe webhooks.

### GA4 `user_properties` (gtag `set`)

| Prop | Kind | Notes |
| --- | --- | --- |
| `name`, `email`, `phone` | Dual of Mixpanel `$name` / `$email` / `$phone` | No GA reserved People equivalents |
| `anam_user_id` | Ana custom (+ dual of `user_id`) | Same Ana M UID as gtag/MP `user_id` |
| `anam_account_id` | Ana custom | Selected account (Group key also `anam_account_id`) |
| `anam_signup` | Ana custom | Dual of Mixpanel `$created` |
| `anam_signup_origin` | Ana custom | `Users.origin` |
| `anam_last_login_at` | Ana custom | ISO8601 last product login |
| `anam_member_profession` | Ana custom | Active membership profession |
| `anam_member_field_of_work` | Ana custom | Active membership field of work |
| `anam_member_sessions_per_month` | Ana custom | Active membership sessions/month |
| `anam_member_role` | Ana custom | `owner` \| `assistant` |
| `anam_member_session_value` | Ana custom | Active membership session value |

### Dual naming (purpose → Mixpanel → GA4)

| Purpose | Mixpanel | GA4 |
| --- | --- | --- |
| Person id | `distinct_id` / `$user_id` + People `anam_user_id` | `user_id` (+ user_property `anam_user_id`) |
| Display name | People `$name` | user_property `name` |
| Email | People `$email` | user_property `email` |
| Phone | People `$phone` | user_property `phone` |
| Signup / created at | People `$created` | user_property `anam_signup` |
| Signup origin | People `anam_signup_origin` | user_property `anam_signup_origin` |
| Last product login | People `anam_last_login_at` | user_property `anam_last_login_at` |
| Account | Group + People `anam_account_id` | user_property `anam_account_id` |
| Member profession / field / sessions / role / session value | People `anam_member_*` | user_property `anam_member_*` |
| Stripe invoice amount | event `amount` | event `amount` |
| Currency (ISO 4217) | event `currency` | event `currency` |

## Sentry

| Field | Value |
| --- | --- |
| `user.id` | Ana M Firebase UID |
| tag `anam_account_id` | selected / route account (singular; not `user.id`) |
| `sendDefaultPii` | `false` |

## Signup attribution (0.5.0)

`signup_channel` is `website` or `app` and is set by the owning server flow. `source` keeps its emitter meaning (`server` or `web`). Existing `origin` attribution is preserved independently. Historical missing channels stay absent and are reported as unknown.

`anam_signed_up` is a committed user/profile fact, emitted by the server with stable per-user deduplication. `anam_account_created` is a committed account fact with stable per-account deduplication. Both retain their current destinations. Failures use `anam_signup_failed` in Mixpanel and a bounded `stage` (`security`, `identity`, `profile`, `account`, `handoff`), with a safe `error_code`. Unknown network outcomes are recovery states.

`session_value` is the submitted major-unit amount. `session_currency` is BRL, EUR, GBP or USD, without conversion. `sessions_per_month` accepts the existing numeric value or the public signup's declared range.

Identity adds `anam_signup_channel` (original user acquisition channel) and `anam_member_session_currency` (active membership currency) alongside existing origin/profession/field/volume/value attributes. Intercom receives only signed identity attributes, never product event fan-out.

Success metrics count unique Ana M users by channel. Account creation and user signup are separate measures. Website form failure collection respects statistics consent, uses manual events, disables replay/autocapture and excludes field contents, passwords and tokens.

## Website signup funnel — catalog 0.6.1

Approved by Christian on 2026-11-04. All three events are frontend-only and released for Mixpanel, subject to analytics consent. Properties are strictly limited to `step`, `step_index`, `locale`, `flow_version` and `signup_channel`. No form contents, contact details, consent choices or clinical profile fields are included.

| Event | Fact |
| --- | --- |
| `anam_signup_step_viewed` | A signup step became visible, `intended_plan_key?`, `intended_billing_cycle?` |
| `anam_signup_step_completed` | Validation succeeded and the visitor advanced, `intended_plan_key?`, `intended_billing_cycle?` |
| `anam_signup_submitted` | The visitor explicitly submitted the account request, `intended_plan_key?`, `intended_billing_cycle?` |

Committed account success remains server-owned. Abandonment is inferred from incomplete sequences, never asserted by page unload. See [signup](signup.md).
