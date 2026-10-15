---
title: Esempi di query SQL
description: Esplora gli esempi di query SQL per la telemetria di Observability Insights.
source-git-commit: 4ad45ae5cbb2a7b348fdad1cd2681a72be12f5eb
workflow-type: tm+mt
source-wordcount: '311'
ht-degree: 10%
---

## Esempi di query SQL

Ecco un esempio più completo. Per questa query:

- Si sceglie `Transaction` come entità.
- Utilizzare `SELECT` per calcolare la durata del 95° percentile.
- I risultati vengono raggruppati per servizio con `FACET`.
- Si utilizza `TIMESERIES AUTO` per tracciare i dati in un intervallo ridimensionato automaticamente.

```sql
FROM Transaction SELECT percentile(duration, 95) AS p95 FACET service TIMESERIES AUTO
```

In un grafico a linee viene generata una riga per servizio, con i 10 servizi più attivi visualizzati per impostazione predefinita.

### Query di base delle richieste in entrata

+++Visualizza query

Tempo medio di risposta, in millisecondi, di tutte le richieste in entrata:

```sql
SELECT average(duration) FROM Transaction
```

+++

### Nomi di attributi che contengono punti

+++Visualizza query

I nomi punteggiati non richiedono virgolette:

```sql
SELECT count(*) FROM Transaction WHERE http.statusCode >= 500 FACET http.route
```

+++

### Query che restituisce più colonne

+++Visualizza query

Diversi aggregati in una query diventano colonne in una tabella:

```sql
SELECT count(*) AS requests, average(duration) AS avgMs, percentile(duration, 95) AS p95, max(duration) AS maxMs FROM Transaction FACET service
```

+++

### Aggregati condizionali in un passaggio

+++Visualizza query

Totale, errori e 5xx affiancati:

```sql
SELECT count(*) AS total, count(*) FILTER (WHERE status = 'Error') AS errors, count(*) FILTER (WHERE http.statusCode >= 500) AS http5xx FROM Transaction FACET service
```

+++

### Query di una metrica curata

+++Visualizza query

Media di CPU per host nel tempo:

```sql
SELECT average(value) AS cpu FROM CpuUtilization FACET host TIMESERIES AUTO
```

+++

### Query di qualsiasi metrica OpenTelemetry per nome

+++Visualizza query

Usa `MetricGauge` / `MetricSum` per le metriche senza un&#39;entità curata:

```sql
SELECT latest(value) AS bytesUsed FROM MetricSum WHERE metric = 'jvm.memory.used' FACET host, pool
```

+++

<a id="what-sql-doesnt-do"></a>

## Quali sono le operazioni non eseguite da SQL

SQL è volutamente piccolo. In questo modo le query sono sicure, veloci e isolate dal tenant. Se hai bisogno di una di queste funzionalità, utilizza l’alternativa.

| Funzionalità | Stato | Alternativa |
| ------------------------------------------- | ------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| `SELECT *` | Non supportato | Elencare gli attributi desiderati: `SELECT timestamp, name, duration, traceId …`. |
| Combinazione di attributi raw e aggregati | Non supportato | Utilizza `FACET attr` o `latest(attr)` / `earliest(attr)`. |
| Matematica in `SELECT` (`a - b`, `count(*) / 60`) | Non supportato | Widget **Formula** (dashboard). |
| Join e sottoquery | Non supportato | **Widget Formula** o due widget affiancati. |
| `HAVING` | Non supportato | `ORDER BY alias` + `LIMIT`. |
| `COMPARE WITH` | Non supportato | Modificate il selettore dell&#39;ora o posizionate due widget su schede diverse. |
| `FACET CASES` | Non supportato | Una colonna `count(*) FILTER (WHERE …)` per ogni caso (vedere [Esercitazione 3](../../tutorials/tutorial-3.md#group-values-into-cases-with-filter)). |
| `histogram()` | Non supportato | `FILTER` colonne con bucket (vedi [Esercitazione 3](../../tutorials/tutorial-3.md#build-a-latency-histogram)). |
| `uniques()` / `keyset()` | Non supportato | `FACET attr LIMIT n` elenca valori distinti. Le query di individuazione elencano le metriche e gli attributi compilati. |
| `RLIKE` (regex) | Non supportato | `LIKE` / `NOT LIKE` con `%` e `_` caratteri jolly o `IN (…)`. |
| Commenti (`--`, `//`, `/* */`) | Non supportato | Inserisci le note nel titolo del widget o in un widget **Testo**. |
| Date assolute in `SINCE` | Non supportato | Utilizza l’intervallo assoluto del selettore del tempo. |
| Conversione tipo (`numeric()`, `string()`) | Non necessario | Gli attributi vengono digitati nel catalogo. |
