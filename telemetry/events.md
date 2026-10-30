# Event catalog (human mirror)

**Catalog version:** `0.2.2`  
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

**Props:** `stripe_customer_id`, `anam_account_id`, `valor` (major units), `moeda`, `plano`.

Use `trackStripeWebhookBilling` / `trackStripeBillingEvent` from `@anam/telemetry/server`.

## Identity

Ana M UID = Mixpanel `distinct_id` / `$user_id` = GA4 `user_id` = Sentry `user.id`.  
Account = Mixpanel Group key `account_id` — never person identity.

### Mixpanel People (`people.set` on identify)

Send **only** when non-empty (omit undefined / `''` / literal `"undefined"`):

| Prop | Source |
| --- | --- |
| `$name` | `name` / `userName` |
| `$email` | `email` / `userEmail` |
| `$phone` | `phone` / `phoneNumber` |
| `$created` | `signupAt` / `userCreatedAt` |
| `anam_user_id` | Ana M UID |
| `origin` | Users.origin |
| `profession` | membership.params.profession |
| `field_of_work` | membership.params.fieldOfWork |
| `language` | i18n language |

Email/name/phone belong on **People**, not on product event properties.

### GA4 `user_properties` (gtag `set`)

| Prop | Notes |
| --- | --- |
| `name`, `email`, `phone` | Professional identity when available |
| `anam_user_id` | Same Ana M UID as `user_id` |
| `account_id` | Selected account (also Mixpanel Group) |
| `origin`, `profession`, `field_of_work`, `language`, `signup_at` | Stable profile attrs |

No clinical keys (patient/handbook/session/transcript/notes/diagnosis).
