---
title: Appendice
description: Fai riferimento a informazioni supplementari per query SQL e dashboard di Observability Insights.
source-git-commit: 143a9567da06f0137ef690795cfbc41bda1780ca
workflow-type: tm+mt
source-wordcount: '145'
ht-degree: 5%
---

## Appendice {#appendix}

**In questo documento:** [14.1 Limiti](#141-limits) · [14.2 Formato file di esportazione](#142-export-file-format) · [14.3 Scheda di riferimento rapido a una pagina](#143-one-page-cheat-sheet)

### Limiti

| Elemento | Limite |
| -------------------------------- | ----------------------------------------------------------------------- |
| Testo della query | 4 KB |
| Attributi `FACET` | 5 |
| `LIMIT` | 1 - 5 000 (valori predefiniti: Number 1, facet 10, events 100, time series 5 000) |
| `IN (…)` valori | 1 - 100 |
| Finestra temporale | 31 giorni |
| Timeout query | 15 s (più un budget di lettura byte) |
| `TIMESERIES AUTO` bucket minimo | 2 minuti |
| Formula | 2 - 6 query; espressione ≤ 500 caratteri; nomi ≤ 40 caratteri |
| Dashboard | ≤ 40 schede · ≤ 200 widget per scheda · ≤ 50 variabili |
| Widget | titolo ≤ 200 · sottotitolo ≤ 500 · unità ≤ 40 · soglie ≤ 10 |
| Widget testo | ≤ 4.000 caratteri |
| Variabile | nome ≤ 64 caratteri · ≤ 500 opzioni/valori · query ≤ 4 KB |

### Esporta formato file

Utilizza **Esporta** per produrre questo; **Importa** per caricarlo. Utile per le dashboard di controllo delle versioni o per promuoverle tra tenant.

```json
{
  "name": "Golden Signals",
  "templateVariables": [
    {
      "id": "var-1",
      "name": "svc",
      "label": "Service",
      "kind": "query",
      "multi": true,
      "query": "SELECT count(*) FROM Transaction FACET service LIMIT 200",
      "value": null
    }
  ],
  "pages": [
    {
      "id": "page-1",
      "name": "Overview",
      "widgets": [
        {
          "id": "sec-kpis",
          "title": "SLO KPIs",
          "queryLanguage": "text",
          "text": "",
          "isSection": true,
          "collapsed": false,
          "layout": { "x": 0, "y": 0, "w": 12, "h": 1 }
        },
        {
          "id": "w-error-rate",
          "title": "Error %",
          "queryLanguage": "SQL",
          "query": "SELECT percentage(*, WHERE status = 'Error') AS errorRate FROM Transaction",
          "vizType": "stat",
          "unit": "%",
          "thresholds": [
            { "value": 0, "status": "ok" },
            { "value": 1, "status": "warning" },
            { "value": 5, "status": "error" }
          ],
          "layout": { "x": 0, "y": 1, "w": 3, "h": 2 }
        },
        {
          "id": "w-throughput",
          "title": "Throughput by service",
          "queryLanguage": "SQL",
          "query": "SELECT count(*) AS requests FROM Transaction FACET service TIMESERIES AUTO LIMIT 8",
          "vizType": "area",
          "layout": { "x": 3, "y": 1, "w": 9, "h": 4 }
        },
        {
          "id": "w-availability",
          "title": "Availability %",
          "queryLanguage": "SQL-formula",
          "formulaQueries": [
            {
              "name": "ok",
              "query": "SELECT count(*) AS v FROM Transaction WHERE status != 'Error'"
            },
            {
              "name": "total",
              "query": "SELECT count(*) AS v FROM Transaction"
            }
          ],
          "formulaExpression": "(ok / total) * 100",
          "vizType": "stat",
          "unit": "%",
          "layout": { "x": 0, "y": 3, "w": 3, "h": 2 }
        }
      ]
    }
  ],
  "exportedAt": "2026-10-01T00:00:00.000Z"
}
```

`vizType` valori: `timeseries` (riga), `area`, `bar`, `pie`, `stat` (numero), `table`. Soglia `status`: `ok` (verde), `warning` (giallo ambra), `error` (rosso).

### Scheda di riferimento rapido di una pagina

```text
SHAPE        SELECT agg FROM Entity                         -> Number
             ... FACET a[, b..e] LIMIT n                     -> Bar / Pie / Table
             ... TIMESERIES AUTO                             -> Line / Area
             ... FACET a TIMESERIES AUTO LIMIT n             -> top-n series
             SELECT attr1, attr2 FROM Entity LIMIT n         -> event Table

AGGREGATES   count(*)  average(x)  sum(x)  min(x)  max(x)  uniqueCount(x)
             percentile(x, 95)  stddev(x)  latest(x[, rank])  earliest(x[, rank])
             percentage(*, WHERE c)  apdex(duration, 500)  percentOfTotal(x)
             rate(value)        -- counters, TIMESERIES only
             derivative(value)  -- gauges,   TIMESERIES only
             agg FILTER (WHERE c) AS alias

WHERE        = != < <= > >=   LIKE / NOT LIKE ('%', '_')   IN / NOT IN (...)
             IS [NOT] NULL   AND OR NOT ( )   strings in 'single quotes'
             string attr present?  ->  attr != ''

ORDER/LIMIT  ORDER BY alias [ASC|DESC]   LIMIT 1..5000

VARIABLES    = {{name}}   IN ({{name}})   numeric: > {{name}}

FORMULA      2-6 named queries, one metric each, no FACET, same shape
             expression: + - * / ( ) numbers names
```
