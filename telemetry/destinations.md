# Destinos — Mixpanel / GA4 / Intercom / Sentry

**Status:** locked (ondas até #742 + prefs)  
**Catálogo:** [`events.yaml`](./events.yaml) campo `destinations`  
**Liberação:** só checklist [`status-checklist.md`](./status-checklist.md) **`live`**

---

## Papéis

| Destino | Emite | Não emite |
| --- | --- | --- |
| **Mixpanel** | Product usage SoT: ativação, adoção, funis, retention, Group Analytics por conta, receita de assinatura Ana M | Ads/ROAS; conteúdo clínico; LLM token-a-token / chunks |
| **GA4** (client GTM/gtag + server Measurement Protocol) | Acquisition, ads/return, subconjunto **Both** acordado no catálogo; mesmo `G-…` por env (GTM + MP) | Espelho cego de todo Mixpanel; UI clicks; AI partials; clinical props |
| **Intercom** | Identidade JWT + attrs allowlisted (CDAs); suporte Messenger | Eventos de produto (`trackEvent`); `stripe_*` inventados no app (Stripe nativo); conteúdo clínico |
| **Sentry** | Errors / traces / releases; tags de suporte; Feedback depois | Product Lexicon; Replay clínico; PII default (`sendDefaultPii: false`) |

**Anti double-count:** nunca somar o mesmo KPI em Mixpanel + GA4. “Both” só quando cada plataforma precisa do fato para uso distinto (ex. invoice pago → Mixpanel revenue **e** GA4 `purchase` / ROAS).

Intercom e Sentry **não** são valores de `destinations` no YAML de product events. Product fan-out = Mixpanel e/ou GA4. Sentry/ops verboso ≈ `ops_only` (conceitual) ou fora do catálogo de produto.

---

## Valores `destinations` no YAML

| Valor | Mixpanel | GA4 | Notas |
| --- | --- | --- | --- |
| `mixpanel` | sim | não | Product usage |
| `ga4` | não | sim | Raro; acquisition-only |
| `both` | sim | sim | Precisa `ga4_name` |
| `ops_only` | não | não | Ops / logs — não Lexicon de produto (ex. inbox/calendar stream reliability → Sentry) |
| `none` | não | não | **Suspenso** até Christian marcar `live` |

Catalog **0.6.2** adds sparse FE `ops_only` events for inbox connection unavailable / rate limit and calendar stream grant / bookings refresh failures. Never emit on successful grant renewals or coalesced bookings GETs.

### Regra de allowlist (hard)

1. Christian marca o evento `live` no checklist.
2. Só então o YAML pode usar `mixpanel` / `ga4` / `both`.
3. Enquanto `in review` → **`destinations: none`**. Sem flip silencioso.

Staging ≠ produção (projetos Mixpanel e property/stream GA4 separados). Sem secret → NoOp. Sem `PUBLIC_*` / kill-switch env de “ligar telemetria”.

---

## Fail-open

Telemetria é **best-effort**. Timeout, 429, secret ausente ou enrichment throw **não** podem bloquear signup, login, pagamento, reconciliação ou acesso.

Contrato completo: [`isolation.md`](./isolation.md).

---

## Geo / IP + Session Replay (#742)

| Concern | Norma |
| --- | --- |
| Mixpanel browser geo | `ip: true` — Country / Region / City enrichment |
| GA4 browser geo | Depende de GTM + consent no mesmo stream `G-` |
| Server IP | GA4 MP `ip_override` / Mixpanel `ip` só em tracks de API user-facing com IP de cliente confiável — **nunca** em webhooks Stripe |
| Session Replay (Mixpanel) | Privacy-safe: máscara 100% de texto/inputs; sem element text / form values; sem canvas/network clínicos |
| Autocapture | Defaults seguros (sem vazar texto clínico) |
| Authenticated shell | Session Replay clínico **off** no sentido de não expor PHI; masking agressivo onde Replay existir |

---

## Disponibilizar as dimensões nos relatórios

O SDK entrega as propriedades nos eventos e perfis. O SDK e este catálogo ainda não automatizam o cadastro das definições nos fornecedores.

| Ferramenta | Cadastro por ambiente | Campos |
| --- | --- | --- |
| GA4, escopo de evento | Em cada propriedade GA4, criar as dimensões para analisar a Account de cada operação | `account_country`, `routing_region`, `calendar_version`, `account_cohort`, `adoption_stage` |
| GA4, escopo de usuário | Em cada propriedade, cadastrar os atributos para segmentar perfis | `anam_account_country`, `anam_routing_region`, `anam_2_0_user` |
| Intercom, Company | Em cada workspace, registrar os atributos da Account no modelo `company` | `anam_account_country` e `anam_routing_region` do tipo `string` |
| Intercom, People | Em cada workspace, registrar a qualificação da pessoa no modelo `contact` | `anam_2_0_user` do tipo `boolean` |
| Mixpanel | O projeto recebe as propriedades junto com os eventos. O relatório de Groups usa a chave de grupo existente `anam_account_id` | País e região no evento descrevem a Account daquela operação. Os perfis de People espelham a Account ativa. |

Uma rotina de sincronização pode listar as definições de cada propriedade GA4 e comparar por `parameterName` e `scope`. No Intercom, pode comparar por workspace, nome, modelo e tipo. A rotina cria as definições ausentes e sinaliza incompatibilidades já existentes para revisão. Os mesmos campos aceitam novos valores de países ou regiões. Só um novo campo, propriedade GA4 ou workspace exige atualizar esse cadastro. [Google Analytics Admin API](https://developers.google.com/analytics/devguides/config/admin/v1/rest/v1beta/properties.customDimensions) · [Intercom Data Attributes API](https://developers.intercom.com/docs/references/2.12/rest-api/api.intercom.io/data-attributes/createdataattribute)

## Quem é SoT de reporting

| KPI | SoT |
| --- | --- |
| Product usage / activation / retention | **Mixpanel** |
| Ads / acquisition / ROAS / BQ export | **GA4** |
| Receita de assinatura (boards produto) | Mixpanel (`anam_billing_invoice_*`) |
| Suporte / Messenger | Intercom (identity attrs; sync usage **adiado**) |
| Errors / traces | Sentry |

---

## Stripe

- Product billing events: emitidos por **anam-telemetry** a partir de webhooks validados — **não** pelo Marketplace app.
- Marketplace configura projetos Mixpanel apenas.
- Dedupe por Stripe Event.id (`insert_id` / GA4 `event_id`).
- Lifecycle stubs (`stripe_subscription_*`, `purchase`, `refund`, …) permanecem `none` / `in review` até liberação — ver checklist.
