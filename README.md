# ssg-dashboard-data

> ⚠️ **NÃO CONECTE ESTE REPO À VERCEL.** ⚠️
>
> Conectar reintroduz exatamente o problema que motivou criar este repo separado:
> a quota Hobby de 100 deploys/dia da Vercel esgota em poucas horas porque o
> sync gera ~720 commits/dia, e cada commit cria um deployment record (consome
> slot mesmo com `ignoreCommand` retornando exit 0).

## O que é

Repositório dedicado a **snapshots de dados** do GW Command Center.
Recebe ~720 commits/dia do workflow GWMS Sync (a cada 10min, 8 arquivos).

Foi separado de `ssg-create/ssg-dashboard` em 2026-04-30 justamente para
desacoplar a hemorragia de commits do repo conectado à Vercel.

## Conteúdo

8 JSONs servidos publicamente via raw.githubusercontent.com:

- `historico_completo.json` — fonte de verdade, ~2300 tickets, janela 4 meses
- `tickets_ativos.json` — tickets em aberto agora
- `utilizacao.json` — carga por atendente
- `gwms-insights.json` — IAL (Sinais Ao Vivo)
- `aios-insights.json` — pipeline XLSX (legado, órfão)
- `silenciosos.json` / `triagem.json` / `reaberturas.json`

## Quem grava aqui

Workflow `.github/workflows/gwms-sync.yml` no repo **`ssg-create/ssg-dashboard`**
(o repo de código, não este). Disparado por cron-job.org a cada 10min via
`workflow_dispatch`. Usa secret `GH_PAT_CROSS_REPO` para autenticar PUT no
GitHub Contents API.

## Quem lê daqui

Dashboard pública **https://gw-command.vercel.app**.
Rewrites no `vercel.json` (do repo `ssg-dashboard`) apontam para
`raw.githubusercontent.com/ssg-create/ssg-dashboard-data/main/<arquivo>.json`.

## Por que não privado

Vercel rewrites não conseguem injetar `Authorization` header em URLs de
proxy → repo precisa ser público para `raw.githubusercontent.com` responder
sem auth. Os dados expostos são tickets internos OTRS — não há credencial.
