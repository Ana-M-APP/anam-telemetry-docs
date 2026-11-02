# Checklist de eventos por domínio

**SoT:** este arquivo em `Ana-M-APP/anam-telemetry-docs`. Edite **Edit** + **Comment**.  
Índice: [`README.md`](./README.md).

Status `live` = **liberado por Christian** para Mixpanel/GA (develop pós [#730](https://github.com/Ana-M-APP/anam-monorepo/pull/730)/[#735](https://github.com/Ana-M-APP/anam-monorepo/pull/735)/[#737](https://github.com/Ana-M-APP/anam-monorepo/pull/737); catálogo `0.4.1` em [#742](https://github.com/Ana-M-APP/anam-monorepo/pull/742)). Status `in review` = **suspenso** de Mixpanel/GA (`destinations: none`) até liberação explícita — HOLD / matriz pendente / pageviews / stubs.

Só **eventos** neste arquivo. Identidade / People / Intercom / Sentry → [`identity.md`](./identity.md). Naming → [`naming.md`](./naming.md). Destinos → [`destinations.md`](./destinations.md). Roadmap → [`roadmap.md`](./roadmap.md). Machine SoT → [`events.yaml`](./events.yaml) · espelho [`events.md`](./events.md).

---

## 1. Auth / Account

| Event (Current) | Edit | Comment | Status |
| --- | --- | --- | --- |
| `anam_account_created` | `anam_account_created` |  | live |
| `anam_account_create_failed` | `anam_account_create_failed` |  | live |
| `anam_user_email_change_requested` | `anam_user_email_change_requested` |  | live |
| `anam_user_email_changed` | `anam_user_email_changed` |  | live |
| `anam_user_password_changed` | `anam_user_password_changed` |  | live |
| `anam_easter_egg` | `anam_easter_egg` |  | live |
| `anam_login_completed` | `anam_login_completed` |  | live |
| `anam_login_failed` | `anam_login_failed` |  | live |
| `anam_password_reset_email_sent` | `anam_password_reset_email_sent` |  | live |
| `anam_password_reset_completed` | `anam_password_reset_completed` |  | live |
| `anam_signed_up` | `anam_signed_up` |  | live |
| `anam_signup_failed` | `anam_signup_failed` |  | live |

---

## 2. Billing / Subscription

| Event (Current) | Edit | Comment | Status |
| --- | --- | --- | --- |
| `anam_billing_invoice_paid` | `anam_billing_invoice_paid` |  | live |
| `anam_billing_invoice_payment_failed` | `anam_billing_invoice_payment_failed` |  | live |
| `anam_subscription_trial_started` | `anam_subscription_trial_started` |  | live |

---

## 3. Customer (handbooks)

| Event (Current) | Edit | Comment | Status |
| --- | --- | --- | --- |
| `anam_customer_created` | `anam_customer_created` |  | live |
| `anam_customer_create_failed` | `anam_customer_create_failed` |  | live |
| `anam_customer_updated` | `anam_customer_updated` |  | live |
| `anam_customer_archived` | `anam_customer_archived` |  | live |
| `anam_customer_unarchived` | `anam_customer_unarchived` |  | live |
| `anam_customer_registration_form_approved` | `anam_customer_registration_form_approved` |  | live |
| `anam_customer_registration_form_shared` | `anam_customer_registration_form_shared` |  | live |

---

## 4. Sessions (+ clinical_record)

| Event (Current) | Edit | Comment | Status |
| --- | --- | --- | --- |
| `anam_session_created` | `anam_session_created` |  | live |
| `anam_session_create_failed` | `anam_session_create_failed` |  | live |
| `anam_session_updated` | `anam_session_updated` |  | live |
| `anam_session_archived` | `anam_session_archived` |  | live |
| `anam_session_payment_updated` | `anam_session_payment_updated` |  | live |
| `anam_clinical_record_requested` | `anam_clinical_record_requested` |  | live |
| `anam_clinical_record_created` | `anam_clinical_record_created` |  | live |
| `anam_clinical_record_failed` | `anam_clinical_record_failed` |  | live |
| `anam_session_recording_started` | `anam_session_recording_started` |  | live |
| `anam_session_recording_completed` | `anam_session_recording_completed` |  | live |
| `anam_session_transcription_completed` | `anam_session_transcription_completed` |  | live |
| `anam_session_exported` | `anam_session_exported` |  | live |

---

## 5. Backlog / not live

HOLD wave-1 (ainda emitidos com nome inventário antigo), pageviews, stubs de catálogo / matriz pendente.

### Assistants / multi-account (HOLD)

| Event (Current) | Edit | Comment | Status |
| --- | --- | --- | --- |
| `account_assistant_access_suspended` | `account_assistant_access_suspended` |  | in review |
| `account_assistant_invite_accepted` | `account_assistant_invite_accepted` |  | in review |
| `account_assistant_invite_cancelled` | `account_assistant_invite_cancelled` |  | in review |
| `account_assistant_invite_sent` | `account_assistant_invite_sent` |  | in review |
| `account_assistant_login` | `account_assistant_login` |  | in review |
| `account_switched` | `account_switched` |  | in review |

### Account deletion (HOLD)

| Event (Current) | Edit | Comment | Status |
| --- | --- | --- | --- |
| `account_deleted_block_reactivate_failed` | `account_deleted_block_reactivate_failed` |  | in review |
| `account_deleted_block_reactivate_success` | `account_deleted_block_reactivate_success` |  | in review |
| `account_deletion_abandoned` | `account_deletion_abandoned` |  | in review |
| `account_deletion_confirmed` | `account_deletion_confirmed` |  | in review |
| `account_deletion_export_requested` | `account_deletion_export_requested` |  | in review |
| `account_deletion_failed` | `account_deletion_failed` |  | in review |
| `account_deletion_requested` | `account_deletion_requested` |  | in review |
| `account_deletion_reverted` | `account_deletion_reverted` |  | in review |

### Prefs + password reset errors (HOLD)

| Event (Current) | Edit | Comment | Status |
| --- | --- | --- | --- |
| `configuration_preferences_updated_error` | `configuration_preferences_updated_error` |  | in review |
| `configuration_preferences_updated_success` | `configuration_preferences_updated_success` |  | in review |
| `password_reset_email_sent_error` | `password_reset_email_sent_error` |  | in review |
| `password_reset_error` | `password_reset_error` |  | in review |

### Pageviews + stubs catálogo / matriz

| Event (Current) | Edit | Comment | Status |
| --- | --- | --- | --- |
| `page_viewed` | `page_viewed` |  | in review |
| `anam_page_viewed` | `anam_page_viewed` |  | in review |
| `signup_started` | `signup_started` |  | in review |
| `onboarding_step_completed` | `onboarding_step_completed` |  | in review |
| `onboarding_completed` | `onboarding_completed` |  | in review |
| `stripe_subscription_created` | `stripe_subscription_created` |  | in review |
| `stripe_subscription_updated` | `stripe_subscription_updated` |  | in review |
| `stripe_subscription_deleted` | `stripe_subscription_deleted` |  | in review |
| `first_subscription_paid` | `first_subscription_paid` |  | in review |
| `refund` | `refund` |  | in review |
| `subscription_cancelled` | `subscription_cancelled` |  | in review |
| `subscription_reactivated` | `subscription_reactivated` |  | in review |
| `subscription_plan_changed` | `subscription_plan_changed` |  | in review |
| `purchase` | `purchase` |  | in review |
