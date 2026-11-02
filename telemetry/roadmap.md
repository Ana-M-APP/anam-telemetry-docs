# Roadmap telemetria Ana M

Atualizado: 2026-11-02 (America/Sao_Paulo).  
**SoT:** este arquivo em `Ana-M-APP/anam-telemetry-docs`. Fonte de decisões: worksheets + prefs do Project.

Status operacional por evento: [`status-checklist.md`](./status-checklist.md). Catálogo machine: [`events.yaml`](./events.yaml).

---

## Revisado e implementado (em `develop`)

| Onda | PR | O quê |
| --- | --- | --- |
| Pacote + SoT + isolamento | #689, #706 | `@anam/telemetry`, fail-open vs auth/billing |
| Sentry UID only | #693 | `user.id` + `account_id` (depois `anam_account_id`) |
| Identity Mixpanel/GA | #709, #724, #725 | People / user_properties, inglês, `anam_*` |
| Rename wave-1 auth/billing | #730 | particípio mercado; billing props; HOLD fora |
| Identity Intercom + MP/GA member attrs | #735 | JWT dual-pass; conta ativa singular; CDAs manuais no Intercom |
| Customer + sessões | #737 | `anam_customer_*`, `anam_session_*`, `anam_clinical_record_*`; form approved → também `anam_customer_created` |
| Geo + Session Replay + allowlist | #742 | Mixpanel `ip: true`, Replay 100% masked, autocapture safe; catálogo: só checklist `live` → Mixpanel/GA |

**Worksheets fechados (Project store):** rename worksheet, patients/sessions worksheet, full inventory.

**Ops pendente (não-código):** criar CDAs `anam_*` no Intercom People (manual). Catálogo `0.4.1` + status checklist/roadmap sincronizados neste repo.

---

## Revisado, ainda não implementado / backlog próximo

| Item | Status | Notas |
| --- | --- | --- |
| Pageviews Mixpanel (+ GA) | **roadmap** | Plataforma tem pageview; nós ainda **não** emitimos canônico. Opções: `anam_page_viewed` manual vs autocapture Mixpanel. Ver depois. |
| Eventos HOLD da wave-1 (assistants, deletion, prefs, password_reset_* error) | backlog rename | Continuam com nomes inventário antigo até nova onda — ver checklist `in review` |
| Matriz legado completa | Project: `telemetry-event-catalog-matrix.md` | Muitas linhas sem PASS — revisar por domínio |
| Gold plan restante | Project: `telemetry-gold-standard-plan.md` | Auth/account/stripe slice avançou; outros gold-novo pendentes |
| Release develop → staging | KM-24 | Reabrir quando Christian pedir |
| Arquivar `Ana-M-APP/anam-docs` antigo | KM-31 | Manual no GitHub |

---

## Falta revisar (produto / Christian)

| Domínio | O quê revisar |
| --- | --- |
| Agenda | create/update/cancel appointment, no-show, reminders |
| Documents / templates | create, share, export (sem conteúdo clínico) |
| Assistants / multi-account | invites já HOLD; role em identity já parcialmente (#735) |
| Billing além invoice paid/fail | trial já tem; subscription plan pick, refund, past-due product events |
| Onboarding / welcome | signup flow pós-public-API |
| Pageviews / funis de navegação | quais rotas importam vs autocapture |
| Chrome `*_clicked` legado | confirmar drop em massa na matriz |

---

## Regras já cristalizadas (não reabrir sem MITM)

- Eventos: particípio mercado (`*_created` / `*_failed` / `*_completed`)
- Prefixo `anam_*` em eventos; props snake_case (podem omitir `anam_`)
- Entidade atendida = **`customer`** (neutro); mapa atual = handbook/paciente
- Conta = singular **ativa** `anam_account_id` (sem `account_0_*`)
- Email/senha = **user**-scoped
- Sem PII clínica em props; telemetria fail-open
- Intercom CDAs precisam existir no workspace (manual)
- GA4 dual names nativos (`login`, `purchase`) OK

---

## Backfill vs corte

- **Corte:** eventos de comportamento a partir do go-live (sem reemitir meses de funil).
- **Backfill one-shot OK:** People/Group **estado** (`customer_count`, `session_count`, profession, role) — não milhares de `*_created` históricos.
- **Não backfill:** pageviews, clinical_record, invoices (Stripe SoT), deletion, etc.

## Próximo passo sugerido

1. Decisão backfill de contagens (KM-49)
2. CDAs Intercom (se ainda faltar)
3. Pageviews (manual vs autocapture)
4. Próximo domínio (Agenda) no [checklist](./status-checklist.md)
