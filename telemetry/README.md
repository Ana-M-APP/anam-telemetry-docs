# Telemetry — Ana M (public SoT)

Este diretório é a **única fonte de verdade pública** de product analytics / telemetria Ana M.
Teammates abrem este repo (`Ana-M-APP/anam-telemetry-docs`) — **não** o Project store do Cursor.

**Pacote tipado:** [`@anam/telemetry`](https://github.com/Ana-M-APP/anam-telemetry) (também espelhado no monorepo `packages/telemetry`). Codegen lê **só** [`events.yaml`](./events.yaml).

---

## O que vive onde

| Doc | Papel |
| --- | --- |
| [`events.yaml`](./events.yaml) | **Machine SoT** — catálogo `0.4.1`: eventos, destinos, aliases, `fe_suppressed`, allowlist de identity |
| [`events.md`](./events.md) | Espelho humano do catálogo (review); codegen **não** lê MD |
| [`status-checklist.md`](./status-checklist.md) | Checklist operacional por domínio — Edit / Comment / Status (`live` \| `in review`) |
| [`roadmap.md`](./roadmap.md) | Roadmap vivo: ondas feitas, backlog, regras locked, backfill |
| [`naming.md`](./naming.md) | Regras de naming (eventos `anam_*`, props, particípio, user vs account, `customer`) |
| [`identity.md`](./identity.md) | Mixpanel People, GA4 user_properties, Intercom CDAs/JWT, Sentry tags |
| [`destinations.md`](./destinations.md) | Papéis Mixpanel / GA4 / Intercom / Sentry; fail-open; allowlist `live`; geo + Replay |
| [`isolation.md`](./isolation.md) | Contrato: telemetria **nunca** bloqueia auth / pagamento / reconciliação / acesso |
| [`contributing.md`](./contributing.md) | Como adicionar um evento (YAML → checklist → sync → instrumentar) |

Cursor rules no repo (apontam para estes contratos):

- [`.cursor/rules/telemetry-isolation.mdc`](../.cursor/rules/telemetry-isolation.mdc)
- [`.cursor/rules/telemetry-english-naming.mdc`](../.cursor/rules/telemetry-english-naming.mdc)

---

## Legenda de status

| Status | Significado | Destinos no YAML |
| --- | --- | --- |
| **`live`** | Liberado por Christian para Mixpanel e/ou GA4 | `mixpanel` \| `ga4` \| `both` |
| **`in review`** | Suspenso (HOLD / matriz / pageviews / stubs) | **`none`** até liberação explícita |

Só eventos marcados `live` no [`status-checklist.md`](./status-checklist.md) podem fan-out para Mixpanel/GA. Não “ligar” destino no YAML sem atualizar o checklist.

---

## Regras SoT (não reabrir sem MITM)

1. **Identifiers** = inglês `snake_case` (`anam_*` em eventos; props de evento podem omitir `anam_`). PT/ES só em copy i18n — nunca em keys.
2. **Particípio de mercado** — sucesso `*_created` / `*_completed`; falha `*_failed`.
3. **Pessoa ≠ conta** — Ana M UID = person; conta ativa singular = `anam_account_id` (sem `account_0_*`).
4. **Email/senha** = escopo **user** (`anam_user_*`), não account.
5. **Entidade atendida** = `customer` (mapa atual = handbook/paciente).
6. **Fail-open** — ver [`isolation.md`](./isolation.md). Sem `PUBLIC_*` / feature-toggle env; secret ausente → NoOp.
7. **Sem PII clínica** em props de analytics (IDs de paciente, transcript, notas, diagnóstico, etc.).
8. **Timezone de reporting Mixpanel** = `America/Sao_Paulo`.

Detalhe: [`naming.md`](./naming.md) · [`destinations.md`](./destinations.md) · [`identity.md`](./identity.md) · [`roadmap.md`](./roadmap.md).

---

## Como adicionar um evento (resumo)

1. Editar [`events.yaml`](./events.yaml) (+ bump `catalog.version`) e espelho em [`events.md`](./events.md).
2. Atualizar [`status-checklist.md`](./status-checklist.md) — novos eventos começam `in review` / `destinations: none` até Christian marcar `live`.
3. PR neste repo → merge em `main`.
4. Em `anam-telemetry`: `catalog:sync` → `catalog:generate` → publish `@anam/telemetry`.
5. Instrumentar no monorepo **depois** do nome existir no catálogo tipado.

Passo a passo: [`contributing.md`](./contributing.md).

---

## Pacote `@anam/telemetry`

| Repo | Uso |
| --- | --- |
| [`Ana-M-APP/anam-telemetry`](https://github.com/Ana-M-APP/anam-telemetry) | Package + codegen (`catalog:sync` / `catalog:generate`) |
| Monorepo `packages/telemetry` | Espelho / pin de versão nos apps |
| Apps (`anam-web`, `anam-server`, …) | Emit via adapters — **não** editam o catálogo “na mão” |

Stripe billing product events saem de **anam-telemetry** (webhooks), não do Marketplace app. Marketplace só configura projetos Mixpanel.

---

## Fora deste repo (Project store / histórico)

Worksheets de review, inventário legado completo, matriz PASS/drop e o gold plan longo ficam no **Project store** (histórico de decisão). O que importa para o dia a dia já está espelhado aqui: checklist, roadmap, catálogo, naming, identity, destinations, isolation.
