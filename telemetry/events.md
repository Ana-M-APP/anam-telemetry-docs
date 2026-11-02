# Event catalog (human mirror)

**Catalog version:** `0.4.1`  
**Machine SoT:** [`events.yaml`](./events.yaml) — typed codegen reads YAML only.  
**Repo:** `Ana-M-APP/anam-telemetry-docs` (authoritative). Consumer package: `@anam/telemetry`.  
Índice: [`README.md`](./README.md).

**Policy:** only Christian-liberated checklist `live` events fan out to Mixpanel/GA4.
HOLD / in-review catalog rows use `destinations: none` (suspended).

**Status:** [`status-checklist.md`](./status-checklist.md) · [`roadmap.md`](./roadmap.md) · [`naming.md`](./naming.md) · [`destinations.md`](./destinations.md) · [`contributing.md`](./contributing.md)

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
| `anam_account_created` | both (BE SoT) | `source`, `account_id`, `profession?`, `field_of_work?`, `sessions_per_month?`, `session_value?` |
| `anam_account_create_failed` | Mixpanel | `message`, `error_code` |
| `anam_user_email_change_requested` | Mixpanel | (none) |
| `anam_user_email_changed` | Mixpanel | (none) — NEW on confirm |
| `anam_user_password_changed` | Mixpanel | (none) |
| `anam_easter_egg` | both | `theme` (e.g. `halloween`) |
| `anam_login_completed` | both | (none); GA4 native `login` |
| `anam_login_failed` | Mixpanel | `error_code`, `error_message` |
| `anam_password_reset_email_sent` | Mixpanel | (none) |
| `anam_password_reset_completed` | Mixpanel | (none) |
| `anam_signed_up` | both (BE SoT) | `source`, `origin`; GA4 native `sign_up` |
| `anam_signup_failed` | Mixpanel | `source`, `origin`, `error_code`, `error_message` |
| `anam_billing_invoice_paid` | both | `amount`, `currency`, `account_id`, `stripe_customer_id`, `source`; GA4 `purchase` |
| `anam_billing_invoice_payment_failed` | both | same billing props; no `plan` / `plan_id` |
| `anam_subscription_trial_started` | both | `operation`, `account_id`, `user_id`, `stripe_customer_id` |

## Stripe subscription lifecycle (stubs — `in review`)

Names unchanged this wave; **suspended** until checklist `live` (`destinations: none` in YAML — do not flip silently).

| Event | Destinations | GA4 name | Dedupe |
| --- | --- | --- | --- |
| `stripe_subscription_created` | none | same | Stripe Event.id |
| `stripe_subscription_updated` | none | same | Stripe Event.id |
| `stripe_subscription_deleted` | none | same | Stripe Event.id |

Also `none` / `in review`: `purchase`, `refund`, `first_subscription_paid`, `subscription_cancelled` / `_reactivated` / `_plan_changed`.  
Live billing today: `anam_billing_invoice_paid` / `_payment_failed`, `anam_subscription_trial_started`.

Use `trackStripeWebhookBilling` / `trackStripeBillingEvent` from `@anam/telemetry/server` when liberated.

## Identity

Tabela mestra (Mixpanel / GA4 / Intercom / Sentry): **[`identity.md`](./identity.md)**.  
Naming dual reserved: [`naming.md`](./naming.md). Destinos / geo / Replay: [`destinations.md`](./destinations.md).

Ana M UID = Mixpanel `distinct_id` / `$user_id` = GA4 `user_id` = Sentry `user.id`.  
Account = Mixpanel Group key `anam_account_id` — never person identity.  
Ana-invented identity props use `anam_*`. Event props may omit `anam_`. Portuguese in identifiers is forbidden.

Allowlist machine: bloco `identity:` em [`events.yaml`](./events.yaml) (`0.4.1`).
