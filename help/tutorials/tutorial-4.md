---
source-git-commit: 263ba9e9b3eb1c6e4dd253ca03bd0ce8954c588b
workflow-type: tm+mt
source-wordcount: '491'
ht-degree: 1%
---
## Esercitazione 4: Funzioni avanzate {#tutorial-4-advanced-functions}

Questo tutorial illustra le funzioni e le entità che consentono di eseguire indagini più approfondite: variabilità, crescita, ricerche basate sull’esempio peggiore, quota di totali, composizione delle transazioni e metriche OpenTelemetry arbitrarie.

**In questa esercitazione:** [9.1 stddev](#91-measure-consistency-with-stddev) · [9.2 derivato](#92-spot-growth-with-derivative) · [9.3 Classificato più recente](#93-find-the-worst-example-with-ranked-latest) · [9.4 percentOfTotal](#94-find-where-time-goes-with-percentoftotal) · [9.5 SpanBreakdown](#95-break-down-transactions-with-spanbreakdown) · [9.6 Qualsiasi metrica](#96-discover-and-query-any-metric) · [9.7 Scegli l&#39;entità](#97-choose-the-right-trace-entity) · [9.8 Aggregazione nidificata](#98-work-around-nested-aggregation)

### Misura la coerenza con `stddev`

Due operazioni possono condividere una media e continuare a comportarsi in modo molto diverso. La deviazione standard mostra la variazione della latenza:

```sql
SELECT count(*) AS requests, average(duration) AS avgMs, stddev(duration) AS stddevMs FROM Transaction FACET name ORDER BY stddevMs DESC LIMIT 20
```

Una deviazione standard maggiore della media punta a un’operazione con richieste occasionali molto lente. Guardate il suo p99.

### Crescita a pronti con `derivative`

`derivative(attr)` restituisce la modifica al secondo della media dell&#39;attributo tra i bucket. È utile per individuare un indicatore che si sta arrampicando, ad esempio quando si utilizza un heap prima di una perdita:

```sql
SELECT derivative(value) AS heapMBPerSec FROM JvmHeapUsed FACET host TIMESERIES 5 minutes
```

Una linea positiva sostenuta significa crescita. I valori negativi indicano che il contatore è in calo (ad esempio, dopo la raccolta dei rifiuti). Come `rate`, `derivative` richiede `TIMESERIES` e deve essere l&#39;unico aggregato.

### Trova l&#39;esempio peggiore con `latest` classificato

Il secondo argomento di `latest` e `earliest` cambia da &quot;più recente&quot; a &quot;più recente in base a questo attributo&quot;. `latest(traceId, duration)` restituisce l&#39;ID traccia della richiesta più lenta, in modo da poterlo incollare nella ricerca di traccia:

```sql
SELECT count(*) AS requests, max(duration) AS slowestMs, latest(traceId, duration) AS slowestTrace, latest(urlPath, duration) AS slowestPath FROM Transaction FACET name ORDER BY slowestMs DESC LIMIT 15
```

Utilizza `earliest(traceId, duration)` per recuperare l&#39;esempio più veloce per il confronto.

### Trova dove va il tempo con `percentOfTotal`

`percentOfTotal(attr)` mostra la quota del totale di ogni gruppo. Applicato a `duration`, mostra quali operazioni richiedono più tempo al server. Questo è spesso più utile di quali operazioni sono più lente:

```sql
SELECT count(*) AS requests, sum(duration) AS totalMs, percentOfTotal(duration) AS shareOfServerTime FROM Transaction FACET name LIMIT 15
```

Impossibile combinare `percentOfTotal` con `TIMESERIES`.

### Suddividi transazioni con `SpanBreakdown`

`SpanBreakdown` è preaggregato al momento dell&#39;acquisizione. Registra le operazioni figlio (`segment`) che ogni transazione (`rootOperation`) impiega in minuti. Risponde: &quot;Che cosa fa questo endpoint?&quot; a basso costo:

```sql
SELECT sum(callCount) AS calls, average(avgDurationMs) AS avgMs FROM SpanBreakdown WHERE rootOperation LIKE '%GET%' FACET rootOperation, segment LIMIT 25
```

Tendenza di un segmento nel tempo:

```sql
SELECT average(avgDurationMs) AS avgMs FROM SpanBreakdown FACET segment TIMESERIES AUTO LIMIT 8
```

&#x200B;> [!NOTE]
> `avgDurationMs` non è contrassegnato automaticamente con un&#39;unità. Impostare il widget **Unità** su `ms`.

### Scopri ed esegui query su qualsiasi metrica

SQL senza `keyset()` o `SHOW EVENT TYPES`. Elenca invece tutte le metriche che generano rapporti, insieme alla relativa unità e al valore più recente:

```sql
SELECT count(*) AS samples, latest(value) AS latestValue FROM MetricGauge FACET metric, unit LIMIT 200
```

```sql
SELECT count(*) AS samples, latest(value) AS latestValue FROM MetricSum FACET metric, unit LIMIT 200
```

Quindi esegui una query su qualsiasi metrica per nome. Ad esempio, la memoria JVM utilizzata, per pool:

```sql
SELECT latest(value) AS bytesUsed FROM MetricSum WHERE metric = 'jvm.memory.used' FACET host, pool LIMIT 50
```

Per vedere quali **attributi span** sono compilati per ogni servizio, conteggia valori non vuoti:

```sql
SELECT count(*) FILTER (WHERE http.route != '') AS withRoute, count(*) FILTER (WHERE db.system != '') AS dbCalls, count(*) FILTER (WHERE peer.service != '') AS withPeer, count(*) FILTER (WHERE error.type != '') AS withErrorType FROM Span FACET service LIMIT 50
```

&#x200B;> [!TIP]
> Quando esiste un&#39;entità curata (ad esempio `CpuUtilization`), preferirla a `MetricGauge`. Applica il filtro e la conversione dell’unità corretti e potrebbe essere accelerata dal rollup.

### Scegli l&#39;entità di traccia corretta

| Utilizza | Quando vuoi | Esempio |
| ------------- | ------------------------------------------------------------------------------------------- | --------------------------------- |
| `Transaction` | KPI della richiesta HTTP/RPC in entrata (velocità effettiva, latenza, errori, Apdex). Rollup-accelerato. | Segnali dorati |
| `AppRequest` | Gli stessi KPI, **inclusi i consumer di messaggi** (Kafka e simili) | Servizi basati su eventi |
| `Span` | Qualsiasi cosa sotto la richiesta: HTTP in uscita, DB, messaggistica, AEM Sling/JCR/JSP, espansioni interne | Analisi delle dipendenze e dei percorsi di codice |

Conta ogni tipo di estensione per vedere cosa è strumentato:

```sql
SELECT count(*) AS spans, percentile(duration, 95) AS p95 FROM Span FACET kind
```

Chiamate in uscita per dominio esterno:

```sql
SELECT count(*) AS calls, percentage(*, WHERE status = 'Error') AS errorPct, percentile(duration, 95) AS p95 FROM Span WHERE kind = 'Client' AND externalDomain != '' FACET externalDomain LIMIT 20
```

### Utilizzare l’aggregazione nidificata

NRQL consente di aggregare un aggregato con una sottoquery, ad esempio &quot;la media dei massimi per host&quot;. SQL non lo fa. Di seguito sono elencate le alternative:

| Obiettivo | Approccio SQL |
| --------------------------------------- | ---------------------------------------------------------- |
| Un valore per gruppo, quindi classifica i gruppi | `FACET group` + `ORDER BY alias` + `LIMIT` |
| Rapporto tra due aggregati | [Widget formula](#86-combine-queries-with-formula-widgets) |
| Un valore per host, quindi l’&quot;host peggiore&quot; | `FACET host ORDER BY alias DESC LIMIT 1` |
| Media per host nel tempo | `FACET host TIMESERIES AUTO` e leggi la serie |

Esempio: l’host peggiore per p95:

```sql
SELECT percentile(duration, 95) AS p95 FROM Transaction FACET host ORDER BY p95 DESC LIMIT 1
```
