# Event catalog (human mirror)

**Catalog version:** `0.2.5`  
**Machine SoT:** [`events.yaml`](./events.yaml) — typed codegen reads YAML only.  
**Isolation:** [`isolation.md`](./isolation.md) — telemetry must not block essential ops.  
**Repo:** `Ana-M-APP/anam-telemetry-docs` (authoritative). Consumer package: `@anam/telemetry`.

## Seasonal / product moments

| Event | Destinations | Where | Measure |
| --- | --- | --- | --- |
| `halloween_easter_egg_clicked` | both (Mixpanel + GA4) | fe | Logo click that successfully shows the Halloween ghost (once per show) |

## Stripe billing (anam-telemetry)

Marketplace **only configures** Mixpanel projects. Product billing facts are emitted
by `@anam/telemetry` from Stripe webhooks with Ana M UID.

| Event | Destinations | GA4 name | Dedupe |
| --- | --- | --- | --- |
| `stripe_invoice_paid` | both | `purchase` | Stripe Event.id |
| `stripe_invoice_payment_failed` | both | same | Stripe Event.id |
| `stripe_subscription_created` | both | same | Stripe Event.id |
| `stripe_subscription_updated` | both | same | Stripe Event.id |
| `stripe_subscription_deleted` | both | same | Stripe Event.id |

**Props:** `stripe_customer_id`, `anam_account_id`, `anam_invoice_amount` (major units; Stripe invoice amount, not session cost), `anam_currency`, `anam_plan_id`.

Use `trackStripeWebhookBilling` / `trackStripeBillingEvent` from `@anam/telemetry/server`.

## Identity

Ana M UID = Mixpanel `distinct_id` / `$user_id` = GA4 `user_id` = Sentry `user.id`.  
Account = Mixpanel Group key `account_id` — never person identity.  
**Rule:** Ana-invented props use `anam_*` English snake_case. Portuguese in
identifiers is forbidden. Platform reserved names stay vendor-native.

### Mixpanel People (`people.set` on identify)

Send **only** when non-empty (omit undefined / `''` / literal `"undefined"`):

| Prop | Kind | Source |
| --- | --- | --- |
| `$name` | Mixpanel reserved | `name` / `userName` |
| `$email` | Mixpanel reserved | `email` / `userEmail` |
| `$phone` | Mixpanel reserved | `phone` / `phoneNumber` |
| `$created` | Mixpanel reserved | `signupAt` / `userCreatedAt` |
| `anam_user_id` | Ana custom | Ana M UID |
| `anam_origin` | Ana custom | Users.origin |
| `anam_profession` | Ana custom | membership.params.profession |
| `anam_field_of_work` | Ana custom | membership.params.fieldOfWork |
| `anam_language` | Ana custom | i18n language |
| `anam_last_login_at` | Ana custom | ISO8601 `Users.lastLoginAt` |

Email/name/phone belong on **People**, not on product event properties.  
Mixpanel browser geo stays off (`ip: false`) — do not set `$country_code` / `$region` / `$city`.

### GA4 `user_properties` (gtag `set`)

| Prop | Kind | Notes |
| --- | --- | --- |
| `name`, `email`, `phone` | Dual of Mixpanel `$name` / `$email` / `$phone` | No GA reserved People equivalents |
| `anam_user_id` | Ana custom (+ dual of `user_id`) | Same Ana M UID as gtag/MP `user_id` |
| `anam_account_id` | Ana custom | Selected account (Mixpanel Group key remains `account_id`) |
| `anam_origin`, `anam_profession`, `anam_field_of_work`, `anam_language`, `anam_signup_at`, `anam_last_login_at` | Ana custom | Stable profile attrs |

No clinical keys (patient/handbook/session/transcript/notes/diagnosis).

### Dual naming (purpose → Mixpanel → GA4)

| Purpose | Mixpanel | GA4 |
| --- | --- | --- |
| Person id | `distinct_id` / `$user_id` + People `anam_user_id` | `user_id` (+ user_property `anam_user_id`) |
| Display name | People `$name` | user_property `name` |
| Email | People `$email` | user_property `email` |
| Phone | People `$phone` | user_property `phone` |
| Signup / created at | People `$created` | user_property `anam_signup_at` |
| Last product login | People `anam_last_login_at` | user_property `anam_last_login_at` |
| Account | Group key `account_id` | user_property `anam_account_id` |
| Origin / profession / field / language | People `anam_*` | user_property `anam_*` |
| Stripe invoice amount (major units) | event `anam_invoice_amount` | event `anam_invoice_amount` |
| Currency (ISO 4217) | event `anam_currency` | event `anam_currency` |
| Stripe Price / plan id | event `anam_plan_id` | event `anam_plan_id` |
