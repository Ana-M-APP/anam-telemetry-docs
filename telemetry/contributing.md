# Contributing — como adicionar um evento

**Audiência:** engenharia + Christian  
**Fluxo:** docs SoT → package tipado → instrumentação no monorepo

---

## Quem é o quê

| Artefato | Papel |
| --- | --- |
| [`events.yaml`](./events.yaml) | **Machine SoT** — nomes, destinos, aliases, `fe_suppressed`, identity allowlist |
| [`events.md`](./events.md) | Espelho humano (review); codegen **não** lê MD |
| [`status-checklist.md`](./status-checklist.md) | Liberação Mixpanel/GA (`live` vs `in review`) |
| [`Ana-M-APP/anam-telemetry`](https://github.com/Ana-M-APP/anam-telemetry) | `@anam/telemetry` — sync YAML → `generated.ts` → publish |
| Monorepo apps | Emitem via adapters; **não** inventam nomes fora do catálogo |

```
anam-telemetry-docs/telemetry/events.yaml  (editar + bump catalog.version)
        │
        ▼  npm run catalog:sync
anam-telemetry  catalog mirror
        │
        ▼  npm run catalog:generate
src/catalog/generated.ts  →  publish @anam/telemetry  →  apps pinam versão
```

---

## Passo a passo

1. **Decidir a medida** — fato de produto (sucesso server) vs intenção UI; destino de reporting (Mixpanel vs GA4 vs Both); sem PII/clínico. Ler [`naming.md`](./naming.md) e [`destinations.md`](./destinations.md).
2. **Editar** [`events.yaml`](./events.yaml):
   - Nova chave em `events:` (`anam_*` snake_case).
   - Campos: `destinations`, `where` (`fe` \| `be` \| `both`), `ga4_name` se GA4/both.
   - **Novos eventos começam com `destinations: none`** até Christian marcar `live`.
   - Rename/legado → entrada em `aliases:`.
   - Sucesso agora server-only → nome FE antigo em `fe_suppressed:`.
   - Bump `catalog.version` (additive = minor; breaking allowlist/schema = major).
3. **Espelho** [`events.md`](./events.md) — tabela/descrição para review.
4. **Checklist** [`status-checklist.md`](./status-checklist.md) — linha Edit/Comment/Status (`in review` até liberação).
5. **PR** neste repo → merge em `main`.
6. **Em anam-telemetry:**
   ```bash
   npm run catalog:sync      # raw GitHub anam-telemetry-docs@main (ou CATALOG_DOCS_PATH=…)
   npm run catalog:generate
   npm run typecheck && npm test
   ```
7. **Publicar** `@anam/telemetry` (semver alinhado) e subir pin no monorepo.
8. **Instrumentar** (`eventTracker.track('…', props)` / helpers Stripe) — só depois do nome existir no catálogo tipado (senão allowlist bloqueia).
9. Quando Christian marcar **`live`**: atualizar checklist **e** YAML `destinations` no mesmo PR de liberação (sem flip silencioso).

---

## Campos YAML (codegen hoje)

| Campo | Obrigatório | Valores | Significado |
| --- | --- | --- | --- |
| `destinations` | sim | `mixpanel` \| `ga4` \| `both` \| `ops_only` \| `none` | Allowlist de fan-out |
| `ga4_name` | se ga4/both | string ≤40 | Nome enviado ao GA4 |
| `where` | recomendado | `fe` \| `be` \| `both` | Locus grosso |
| `mixpanel_via_marketplace` | raro | bool | Skip product adapter — Marketplace SoT comercial |

Top-level: `catalog.version` / `catalog.schema_version` · `identity` · `aliases` · `fe_suppressed`.

---

## Checklist de PR (docs)

- [ ] Nome inglês `anam_*` + particípio ([`naming.md`](./naming.md))
- [ ] `destinations: none` se ainda não `live`
- [ ] Checklist atualizado
- [ ] `events.md` espelhado
- [ ] Sem PII clínica nas props documentadas
- [ ] Isolamento preservado se tocar adapters ([`isolation.md`](./isolation.md))

---

## O que ainda não está no YAML tipado

Path fino de arquivo, owners, status planned/implemented e “o que medir” em prosa ainda vivem em review humano (checklist + roadmap + PRs de wire). Não inventar path clínico no YAML.
