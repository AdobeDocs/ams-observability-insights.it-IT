---
source-git-commit: d3cd77e213674587152d6d33efa65328f420a547
workflow-type: tm+mt
source-wordcount: '295'
ht-degree: 10%
---
### Esempi di query SQL

Ecco un esempio più completo. Per questa query:

- Si sceglie `Transaction` come entità.
- Utilizzare `SELECT` per calcolare la durata del 95° percentile.
- I risultati vengono raggruppati per servizio con `FACET`.
- Si utilizza `TIMESERIES AUTO` per tracciare i dati in un intervallo ridimensionato automaticamente.

```sql
FROM Transaction SELECT percentile(duration, 95) AS p95 FACET service TIMESERIES AUTO
```

In un grafico a linee viene generata una riga per servizio, con i 10 servizi più attivi visualizzati per impostazione predefinita.

Fai clic su ogni esempio di seguito per espanderlo.

<details>
<summary><strong>Query di base delle richieste in entrata</strong></summary>

Tempo medio di risposta, in millisecondi, di tutte le richieste in entrata:

```sql
SELECT average(duration) FROM Transaction
```

</details>

<details>
<summary><strong>Nomi di attributi contenenti punti</strong></summary>

I nomi punteggiati non richiedono virgolette:

```sql
SELECT count(*) FROM Transaction WHERE http.statusCode >= 500 FACET http.route
```

</details>

<details>
<summary><strong>La query restituisce più colonne</strong></summary>

Diversi aggregati in una query diventano colonne in una tabella:

```sql
SELECT count(*) AS requests, average(duration) AS avgMs, percentile(duration, 95) AS p95, max(duration) AS maxMs FROM Transaction FACET service
```

</details>

<details>
<summary><strong>Aggregazioni condizionali in un passaggio</strong></summary>

Totale, errori e 5xx affiancati:

```sql
SELECT count(*) AS total, count(*) FILTER (WHERE status = 'Error') AS errors, count(*) FILTER (WHERE http.statusCode >= 500) AS http5xx FROM Transaction FACET service
```

</details>

<details>
<summary><strong>Query di una metrica curata</strong></summary>

Media di CPU per host nel tempo:

```sql
SELECT average(value) AS cpu FROM CpuUtilization FACET host TIMESERIES AUTO
```

</details>

<details>
<summary><strong>Query di qualsiasi metrica OpenTelemetry per nome</strong></summary>

Usa `MetricGauge` / `MetricSum` per le metriche senza un&#39;entità curata:

```sql
SELECT latest(value) AS bytesUsed FROM MetricSum WHERE metric = 'jvm.memory.used' FACET host, pool
```

</details>

<a id="what-sql-doesnt-do"></a>

### Quali sono le operazioni non eseguite da SQL

SQL è volutamente piccolo. In questo modo le query sono sicure, veloci e isolate dal tenant. Se hai bisogno di una di queste funzionalità, utilizza l’alternativa.

| Funzionalità | Stato | Alternativa |
| ------------------------------------------- | ------------- | ------------------------------------------------------------------------------------------------------------ |
| `SELECT *` | Non supportato | Elencare gli attributi desiderati: `SELECT timestamp, name, duration, traceId …`. |
| Combinazione di attributi raw e aggregati | Non supportato | Utilizza `FACET attr` o `latest(attr)` / `earliest(attr)`. |
| Matematica in `SELECT` (`a - b`, `count(*) / 60`) | Non supportato | Widget **Formula** (dashboard). |
| Join e sottoquery | Non supportato | **Widget Formula** o due widget affiancati. |
| `HAVING` | Non supportato | `ORDER BY alias` + `LIMIT`. |
| `COMPARE WITH` | Non supportato | Modificate il selettore dell&#39;ora o posizionate due widget su schede diverse. |
| `FACET CASES` | Non supportato | Una colonna `count(*) FILTER (WHERE …)` per ogni caso (vedere [Esercitazione 3](#83-group-values-into-cases-with-filter)). |
| `histogram()` | Non supportato | `FILTER` colonne con bucket (vedi [Esercitazione 3](#84-build-a-latency-histogram)). |
| `uniques()` / `keyset()` | Non supportato | `FACET attr LIMIT n` elenca valori distinti. Le query di individuazione elencano le metriche e gli attributi compilati. |
| `RLIKE` (regex) | Non supportato | `LIKE` / `NOT LIKE` con `%` e `_` caratteri jolly o `IN (…)`. |
| Commenti (`--`, `//`, `/* */`) | Non supportato | Inserisci le note nel titolo del widget o in un widget **Testo**. |
| Date assolute in `SINCE` | Non supportato | Utilizza l’intervallo assoluto del selettore del tempo. |
| Conversione tipo (`numeric()`, `string()`) | Non necessario | Gli attributi vengono digitati nel catalogo. |
