# Identity attributes

**Status:** locked (ondas #709 / #724 / #725 / #735 / #693 + rename worksheet)
**Machine allowlist:** bloco `identity:` em [`events.yaml`](./events.yaml)
**Espelho props:** [`events.md`](./events.md) § Identity

Estes são **atributos de identidade / perfil** — não eventos de produto. Intercom e Sentry **não** entram em `destinations` do catálogo de eventos.

---

## Modelo

```
Ana M UID  →  Mixpanel distinct_id / $user_id
           →  GA4 user_id (gtag + Measurement Protocol)
           →  Sentry user.id
           →  Intercom user_id (+ anam_user_id)

Conta ativa (singular)  →  Mixpanel Group key anam_account_id
                        →  People / GA4 user_property anam_account_id
                        →  Intercom anam_account_id
                        →  Sentry tag anam_account_id
```

| Regra | Detalhe |
| --- | --- |
| Pessoa | Sempre Ana M UID — **nunca** usar account como identidade da pessoa |
| Conta | **Singular ativa** `anam_account_id` — sem `account_0_*` / arrays como SoT |
| Switch | Re-identify Mixpanel + Intercom re-boot; troca Group; limpa ambiguidade |
| Logout / login-as | `reset` Mixpanel + Intercom shutdown + Sentry reset |
| Omitir vazios | Não enviar `undefined` / `''` / literal `"undefined"` |
| PII em eventos | Email/nome/telefone **não** vão no payload de product event (só People / user_properties / Intercom JWT) |

---

## Tabela mestra

| Attribute | Mixpanel | GA4 | Intercom | Sentry | Notes |
| --- | --- | --- | --- | --- | --- |
| Person id (Ana M UID) | `identify` → `distinct_id` / `$user_id` | `user_id` | `user_id` | `user.id` | Também People/user_property `anam_user_id` |
| Display name | People `$name` | user_property `name` | `name` | — | Dual vendor; **não** no user Sentry |
| Email | People `$email` | user_property `email` | `email` | — | Dual; não em props de evento |
| Phone | People `$phone` | user_property `phone` | `phone` (E.164) | — | Dual |
| Signup / created at | People `$created` | user_property `anam_signup` | `created_at` (epoch s) | — | Mixpanel reserved ↔ Ana custom no GA |
| `anam_user_id` | People | user_property | JWT claim | — | Mesmo UID (redundância explícita p/ workflows) |
| `anam_account_id` | Group + People | user_property | JWT claim | tag `anam_account_id` | Conta **selecionada**; tag Sentry ≠ `user.id` |
| `anam_signup_channel` | People | user_property | JWT claim | — | Original server-assigned `website` or `app`; absent for unknown legacy users |
| `anam_member_session_currency` | People | user_property | JWT claim | — | Active membership BRL, EUR, GBP or USD |
| `anam_signup_origin` | People | user_property | JWT claim | — | `Users.origin` |
| `anam_last_login_at` | People | user_property | JWT claim | — | ISO8601 (MP/GA) / epoch no Intercom path; ≠ `$last_seen` |
| `anam_member_profession` | People | user_property | JWT claim | — | Membership da conta ativa |
| `anam_member_field_of_work` | People | user_property | JWT claim | — | Idem |
| `anam_member_sessions_per_month` | People | user_property | JWT claim | — | Declaração de cadastro — ≠ uso real |
| `anam_member_role` | People | user_property | JWT claim | — | `owner` \| `assistant` |
| `anam_member_session_value` | People | user_property | JWT claim | — | Membership params |
| `language` | **não** | **não** | (se path Messenger) | — | Não emitir em Mixpanel/GA People |
| Geo Country/Region/City | SDK `ip: true` | via GTM/consent | — | — | Sem inventar `$country_code` no app |

---

## Mixpanel People

Allowlist em `identity.mixpanel_people` no YAML. Host típico: `SetupTrackers` → `eventTracker.trackUser` / `people.set` pós-identify.

Group Analytics key = `anam_account_id` (conta ativa).

---

## GA4 user_properties

Allowlist em `identity.ga4_user_properties`. Dual de reserved Mixpanel:

| Purpose | Mixpanel | GA4 |
| --- | --- | --- |
| Display name | `$name` | `name` |
| Email | `$email` | `email` |
| Phone | `$phone` | `phone` |
| Created / signup | `$created` | `anam_signup` |
| Person id field | `anam_user_id` (+ `$user_id`) | `user_id` + `anam_user_id` |

Server Measurement Protocol usa o **mesmo** Ana M UID em `user_id`. `client_id` preferindo o real do tag (`_ga`) quando disponível.

---

## Intercom (Messenger / JWT)

Só identidade — **sem** `trackEvent` de produto.

- Claims assinados em `createIntercomSession` → `boot` / `update`.
- Claims opcionais omitidos se ausentes/inválidos.
- Layout boot (`api_base`, padding, `z_index`, …) fora desta tabela.
- **CDAs `anam_*` devem existir no workspace Intercom People** — criação **manual** no Intercom Admin (ops, não código). Sem CDA, o attr não aparece no People mesmo que o JWT envie.
- Sync de usage Mixpanel → Intercom / aquisição GA4·BQ → Intercom: **adiado** (não no path do Messenger request).

Nomes alinhados à allowlist atual (`anam_signup_origin`, `anam_member_*`) — não usar nomes antigos (`anam_origin`, `anam_profession`) em docs/implementação novas.

---

## Sentry

Sentry **não** é destino do catálogo de product events.

| Field | Value |
| --- | --- |
| `user.id` | Ana M Firebase UID |
| tag `anam_account_id` | Conta selecionada / route — singular; **não** vira `user.id` |
| `sendDefaultPii` | `false` |

Explicitamente **não** no user Sentry: email, name, profession, plan, session value, clinical PII.
