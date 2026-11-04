# Signup attribution and currency

Approved by Christian on 2026-11-03. Catalog and package version 0.5.0. This release reconciles the existing monorepo catalog/package 0.4.4 at develop e1455803020a4d84387cf2314967c5b3867398f0 before applying the approved additions.



`signup_channel` is `website` or `app` and is set by the owning server flow. `source` keeps its emitter meaning (`server` or `web`). Existing `origin` attribution is preserved independently. Historical missing channels stay absent and are reported as unknown.

`anam_signed_up` is a committed user/profile fact, emitted by the server with stable per-user deduplication. `anam_account_created` is a committed account fact with stable per-account deduplication. Both retain their current destinations. Failures use `anam_signup_failed` in Mixpanel and a bounded `stage` (`security`, `identity`, `profile`, `account`, `handoff`), with a safe `error_code`. Unknown network outcomes are recovery states.

`session_value` is the submitted major-unit amount. `session_currency` is BRL, EUR, GBP or USD, without conversion. `sessions_per_month` accepts the existing numeric value or the public signup's declared range.

Identity adds `anam_signup_channel` (original user acquisition channel) and `anam_member_session_currency` (active membership currency) alongside existing origin/profession/field/volume/value attributes. Intercom receives only signed identity attributes, never product event fan-out.

Success metrics count unique Ana M users by channel. Account creation and user signup are separate measures. Website form failure collection respects statistics consent, uses manual events, disables replay/autocapture and excludes field contents, passwords and tokens.
