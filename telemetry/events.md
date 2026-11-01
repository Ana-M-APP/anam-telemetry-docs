# Event catalog (human mirror)

**Catalog version:** `0.3.0`  
**Machine SoT:** [`events.yaml`](./events.yaml) — typed codegen reads YAML only.  
**Repo:** `Ana-M-APP/anam-telemetry-docs` (authoritative). Consumer package: `@anam/telemetry`.

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

## Stripe subscription lifecycle (unchanged names this wave)

| Event | Destinations | GA4 name | Dedupe |
| --- | --- | --- | --- |
| `stripe_subscription_created` | both | same | Stripe Event.id |
| `stripe_subscription_updated` | both | same | Stripe Event.id |
| `stripe_subscription_deleted` | both | same | Stripe Event.id |

Use `trackStripeWebhookBilling` / `trackStripeBillingEvent` from `@anam/telemetry/server`.

## Identity

Ana M UID = Mixpanel `distinct_id` / `$user_id` = GA4 `user_id` = Sentry `user.id`.  
Account = Mixpanel Group key `anam_account_id` — never person identity.  
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
| `anam_last_login_at` | Ana custom | ISO8601 `Users.lastLoginAt` |

**Removed from Mixpanel/GA this wave:** `origin`, `profession`, `field_of_work`, `language` (Intercom `anam_member_*` / `anam_signup_origin` only).

Email/name/phone belong on **People**, not on product event properties.  
Mixpanel browser geo stays off (`ip: false`).

### GA4 `user_properties` (gtag `set`)

| Prop | Kind | Notes |
| --- | --- | --- |
| `name`, `email`, `phone` | Dual of Mixpanel `$name` / `$email` / `$phone` | No GA reserved People equivalents |
| `anam_user_id` | Ana custom (+ dual of `user_id`) | Same Ana M UID as gtag/MP `user_id` |
| `anam_account_id` | Ana custom | Selected account (Group key also `anam_account_id`) |
| `anam_signup` | Ana custom | Dual of Mixpanel `$created` |
| `anam_last_login_at` | Ana custom | ISO8601 last product login |

### Dual naming (purpose → Mixpanel → GA4)

| Purpose | Mixpanel | GA4 |
| --- | --- | --- |
| Person id | `distinct_id` / `$user_id` + People `anam_user_id` | `user_id` (+ user_property `anam_user_id`) |
| Display name | People `$name` | user_property `name` |
| Email | People `$email` | user_property `email` |
| Phone | People `$phone` | user_property `phone` |
| Signup / created at | People `$created` | user_property `anam_signup` |
| Last product login | People `anam_last_login_at` | user_property `anam_last_login_at` |
| Account | Group key `anam_account_id` | user_property `anam_account_id` |
| Stripe invoice amount | event `amount` | event `amount` |
| Currency (ISO 4217) | event `currency` | event `currency` |

## Sentry

| Field | Value |
| --- | --- |
| `user.id` | Ana M Firebase UID |
| tag `anam_account_id` | selected / route account (singular; not `user.id`) |
| `sendDefaultPii` | `false` |
