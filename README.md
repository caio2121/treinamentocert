# HCIA Storage — Simulador de prática

Simulador web para treinar questões no estilo **HCIA-Storage** (banco atual: **362** questões; mock HCIA-Storage V5.0: **730** questões).

## Acesse online (GitHub Pages)

Abra: **https://caio2121.github.io/treinamentocert/**

(Se a URL 404, em **Settings → Pages** do repositório escolha *Deploy from a branch* → branch `main` → pasta `/docs`.)

## Uso local

Baixe ou clone o repositório e abra no navegador:

```text
docs/index.html
```

Não precisa de servidor, npm nem internet.

## O que este repositório publica

Somente o necessário para o site:

| Caminho | Conteúdo |
|---------|----------|
| `README.md` | Esta documentação |
| `docs/index.html` | Simulador standalone (HTML + CSS + JS + bancos embutidos; mock_v5 incluso) |
| `docs/assets/questions/current/` | Mídia runtime do banco legado referenciada pelo HTML |
| `docs/.nojekyll` | Flag para o GitHub Pages servir os arquivos sem Jekyll |
| `.gitignore` | Bloqueia PDFs, material oficial e workspace local |

## O que NÃO é publicado

Material de estudo, capturas, OCR, scripts e dados brutos ficam **apenas na máquina local** (ignorados pelo Git), por exemplo:

- PDFs de curso / conversas / Training Material (`*.pdf`, `docshuawei/`, …)
- Workspace `hcia-simulator/` (fonte, `data/`, `work/`, `reports/`, `scripts/`, assets de origem)
- Inventários e exports temporários (`*.csv`, `*.json` de catálogo, etc.)

O build local gera `docs/index.html` e `docs/assets/questions/current/`. O mock
HCIA-Storage V5.0 mantém suas 43 mídias como data URIs no HTML publicado; não
depende do PDF, de `novas/` ou dos assets de origem para executar.

## Aviso

Gabaritos históricos (ex.: confirmações de chat) **não** são documentação oficial Huawei. Use o simulador para prática, não como fonte normativa.
