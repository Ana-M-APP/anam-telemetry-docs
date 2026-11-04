# Naming — telemetria Ana M

**Status:** locked (prefs + ondas #730 / #735 / #737)  
**SoT de identifiers:** inglês `snake_case` apenas. Copy de UI pode ser PT/ES.

Ver também: [`events.yaml`](./events.yaml) · [`.cursor/rules/telemetry-english-naming.mdc`](../.cursor/rules/telemetry-english-naming.mdc) · [`identity.md`](./identity.md)

---

## Eventos

| Regra | Detalhe |
| --- | --- |
| Prefixo | Sempre `anam_*` nos eventos de produto canônicos |
| Forma | `object_action[_qualifier]` — ex. `anam_session_created` |
| Sucesso | Particípio / estado de mercado: `*_created`, `*_completed`, `*_changed`, `*_sent`, `*_archived` |
| Falha | `*_failed` (não `*_fail` / `*_error` no canônico novo) |
| UI intent | `*_clicked` / `*_opened` / `*_viewed` — não contam como sucesso canônico de negócio |
| Idioma | Inglês only — proibido `anam_valor`, `anam_plano`, etc. |

Exemplos locked:

| Bom | Ruim |
| --- | --- |
| `anam_account_created` | `account_created_success` (legado → alias) |
| `anam_login_failed` | `login_error` (legado → alias) |
| `anam_signed_up` | `signup` / `sign_up` como SoT interno (aliases OK → canônico) |
| `anam_billing_invoice_paid` | `stripe_invoice_paid` como nome canônico (alias OK) |

---

## Props de evento

| Regra | Detalhe |
| --- | --- |
| Case | `snake_case` |
| Prefixo `anam_` | **Opcional** em props de evento (`account_id`, `amount`, `currency`, `error_code`) |
| Sem clinical PII | Sem patient/handbook/session IDs clínicos, transcript, nota, diagnóstico, instrumento, busca livre-texto |
| Sem email/nome/telefone no payload de evento | Vão para People / user_properties no identify — adapters stripam do event |
| Billing | `amount` = valor da **invoice** Stripe (unidades maiores); `currency` ISO 4217; nesta onda **sem** `plan` / `plan_id` nos eventos de invoice |

Sugestões de domínio (custom Ana → `anam_*` quando identity / People):

| Bad | Good |
| --- | --- |
| `anam_valor` / `valor` | `amount` (evento) ou `anam_invoice_amount` se custom People |
| `anam_moeda` | `currency` / `anam_currency` |
| `anam_plano` | `anam_plan_id` (quando existir na onda) |

---

## Escopo user vs account

| Conceito | Escopo | Exemplos |
| --- | --- | --- |
| Email / senha / login da pessoa | **user** | `anam_user_email_changed`, `anam_user_password_changed`, `anam_login_completed` |
| Conta selecionada / membership / billing da conta | **account** | `anam_account_created`, `anam_account_id`, `anam_member_*` |
| Multi-account | Singular **ativa** | Só `anam_account_id` (+ membership daquela conta). **Proibido** `account_0_*` / arrays como SoT |
| Switch de conta | Re-identify / Intercom re-boot | Troca group + limpa super-props ambíguas |

Uma pessoa = um Ana M UID em todos os sinks. Uma pessoa pode ter muitas contas; email/senha são únicos da pessoa.

---

## Entidade `customer`

- **`customer`** = parte atendida (neutro cross-setor).
- No produto saúde atual mapeia para handbook / paciente.
- Eventos: `anam_customer_*` — **não** `anam_patient_*` / `anam_handbook_*` no canônico novo (aliases legados → canônico).

Soft-delete de sessão = `archived` (`anam_session_archived`). Completude de sessão de produto = geração do clinical record (`anam_clinical_record_*`), não um `session_completed` separado nesta onda.

---

## Destinos e nomes GA4

- Nome **interno** Ana M permanece `anam_*`.
- Quando há equivalente recomendado GA4, o adapter mapeia via `ga4_name` no YAML (`login`, `sign_up`, `purchase`, `page_view`) sem renomear o contrato interno.
- Dual Mixpanel reserved ↔ GA custom: ver [`identity.md`](./identity.md).

---

## Aliases

Legado → canônico vive em `aliases:` no [`events.yaml`](./events.yaml). Não reinventar o canônico; adicione alias + mantenha Lexicon alinhado.
