---
title: Prestazioni
description: Migliora le prestazioni di query, grafici e dashboard in Observability Insights.
source-git-commit: 143a9567da06f0137ef690795cfbc41bda1780ca
workflow-type: tm+mt
source-wordcount: '227'
ht-degree: 3%
---

## Prestazioni {#performance}

Ogni query viene eseguita con un timeout di 15 secondi e un budget di lettura dei byte. Questo documento spiega come Observability Insights accelera automaticamente le query e come progettare dashboard che rimangano veloci in qualsiasi intervallo di tempo.

### Accelerazione rollup (automatica)

`Transaction` grafici leggono tabelle preaggregate quando **tutti** di questi blocchi:

| Condizione | Requisito |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------ |
| Forma | La query ha `TIMESERIES` (i totali di numero/tabella vengono sempre letti come non elaborati per l&#39;esattezza) |
| Finestra | ≥ 3 h (livello da 1 min) · ≥ 1 d (livello da 5 min) · ≥ 7 d (livello da 1 h) |
| Aggregati | solo `count(*)`, `percentage(*, WHERE status = 'Error')`, `apdex(duration, 500)`, `percentile(duration, 50 \| 95 \| 99)` |
| `FACET` / `WHERE` | solo il `service`, `appInstance`, `kind` |

`CpuUtilization` e `MemoryUtilization` (`average(value)`) e `JvmCpuRecentUtilization` (`average(value)`) grafici con `FACET host` / `WHERE host …` su ≥ 3 ore hanno letto aggregazioni di 1 minuto.

**Suggerimento per la progettazione:** per widget di tendenza di 7-31 giorni, attenersi al set di facile rollup (p95, % errore, Apdex @ 500, conteggio, servizio FACET): rimangono veloci a qualsiasi intervallo. Salvare `FACET host`/`name`, `average(duration)` o `urlPath` filtri per finestre o tabelle più brevi.

### Suggerimenti generali

- Utilizza `LIMIT` per ogni query con facet, in particolare `FACET urlPath`, `traceId`, `jcr.path` (elevata cardinalità).
- Filtra in anticipo con `WHERE` sugli attributi a bassa cardinalità (`service`, `kind`, `status`).
- Preferisci `Transaction` rispetto a `Span` quando sono necessarie solo le richieste in entrata: un numero di righe molto inferiore.
- `uniqueCount` è esatto; per gli attributi ad alta cardinalità su lunghe finestre è l&#39;aggregato più costoso.
- Evitare `TIMESERIES MAX`, tranne finestre di pochi minuti.
- I risultati della cache dei widget del dashboard per 30 secondi; il pulsante di aggiornamento forza una nuova esecuzione.
- Un widget che raggiunge il timeout di 15 secondi o gli errori di budget di lettura dei byte, restringendo la finestra, aggiungendo filtri o riducendo i facet.
