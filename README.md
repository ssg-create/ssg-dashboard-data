# Branch `data`

Branch órfã (sem histórico compartilhado com `main`) que armazena apenas os
JSONs gerados pelo workflow `gwms-sync.yml`.

## Por que existe

Vercel free tier limita a **100 deployments/dia**. O workflow GWMS Sync
commita 8 JSONs a cada 10min (~144 commits/dia). Mesmo com `ignoreCommand`
cancelando os builds, cada commit em `main` consumia 1 slot da cota.

Solução: workflow commita aqui em `data`. Vercel ignora pushes nessa branch
via `vercel.json` em `main`:

```json
{
  "git": {
    "deploymentEnabled": {
      "data": false
    }
  }
}
```

Resultado: **0 deployments consumidos** pelos commits do GWMS Sync.

## Como o dashboard usa

`index.html` em `main` fetcha JSONs direto do raw do GitHub apontando pra esta branch:

```js
fetch('https://raw.githubusercontent.com/ssg-create/ssg-dashboard/data/historico_completo.json?v=' + Date.now())
```

CDN do GitHub tem TTL de ~5min, então o dashboard sempre vê dados frescos.

## Arquivos

| Arquivo | Origem | Atualização |
|---|---|---|
| `historico_completo.json` | `q_historico_completo()` no `gwms_sync.py` | a cada 10 min |
| `tickets_ativos.json`     | `q_tickets_ativos()`     | a cada 10 min |
| `utilizacao.json`         | `q_utilizacao()`         | a cada 10 min |
| `silenciosos.json`        | `q_silenciosos()`        | a cada 10 min |
| `triagem.json`            | `q_triagem()`            | a cada 10 min |
| `reaberturas.json`        | `q_reaberturas()`        | a cada 10 min |
| `gwms-insights.json`      | `generate_insights()`    | a cada 10 min |
| `aios-insights.json`      | gerado pelo pipeline OTRS legado | obsoleto (manter por compat) |

## Não commitar manualmente aqui

Esta branch é gerenciada pelo workflow. Mexer manualmente quebra o ciclo do sync.
