---
title: 'Esercitazione 2: Controllare i dati'
description: Scopri come filtrare, raggruppare e controllare i risultati delle query in Observability Insights.
source-git-commit: 143a9567da06f0137ef690795cfbc41bda1780ca
workflow-type: tm+mt
source-wordcount: '361'
ht-degree: 0%
---

## Esercitazione 2: Controllare i dati {#tutorial-2-control-your-data}

In questa esercitazione imparerai le funzioni e le clausole che trasformano i conteggi non elaborati in risposte: conteggi distinti, valori più recenti, percentuali, percentili, caratteri jolly, ordinamento e raggruppamento multidimensionale.

**In questa esercitazione:** [7.1 uniqueCount](#71-count-distinct-values-with-uniquecount) · [7.2 più recente/meno recente](#72-find-the-latest-and-earliest-values) · [7.3 percentuale](#73-calculate-percentages) · [7.4 percentile](#74-understand-latency-with-percentile) · [7.5 Etichette](#75-make-results-readable-with-labels) · [7.6 Caratteri jolly ed elenchi](#76-match-patterns-and-lists) · [7.7 Ordinamento](#77-sort-and-rank-with-order-by) · [7.8 Più facet](#78-group-by-several-attributes) · [7.9 Finestre precedenti](#79-look-at-a-past-window)

### Conta valori distinti con `uniqueCount`

Quanti host e tracce distinte ha ciascun servizio?

```sql
SELECT uniqueCount(host) AS hosts, uniqueCount(traceId) AS traces FROM Transaction FACET service
```

`uniqueCount` è esatto, non una stima.

Per **elencare** i valori distinti stessi (ciò che NRQL chiama `uniques()`), facet sull&#39;attributo:

```sql
SELECT count(*) AS requests FROM Transaction FACET host LIMIT 100
```

### Trova i valori più recenti e meno recenti

`latest(attr)` restituisce il valore più recente e `earliest(attr)` il più vecchio. Sono utili per le tabelle &quot;Stato corrente&quot;:

```sql
SELECT latest(value) AS currentCpu FROM CpuUtilization FACET host
```

```sql
SELECT earliest(timestamp) AS firstSeen, latest(timestamp) AS lastSeen FROM Transaction FACET service
```

### Calcola percentuali

`percentage(*, WHERE condition)` restituisce la condivisione di righe che corrispondono a una condizione. Questo è il tasso di errore:

```sql
SELECT percentage(*, WHERE status = 'Error') AS errorPct FROM Transaction
```

Tendenza IT per servizio:

```sql
SELECT percentage(*, WHERE status = 'Error') AS errorPct FROM Transaction FACET service TIMESERIES AUTO LIMIT 10
```

E tieni traccia della condivisione di richieste lente:

```sql
SELECT percentage(*, WHERE duration > 2000) AS slowPct FROM Transaction FACET service
```

### Comprendere la latenza con `percentile`

Le medie nascondono gli outlier. I percentili mostrano l’esperienza della maggior parte degli utenti (p50) e quella degli sfortunati (p95, p99):

```sql
SELECT percentile(duration, 50) AS p50, percentile(duration, 95) AS p95, percentile(duration, 99) AS p99 FROM Transaction TIMESERIES AUTO
```

>[!TIP]
>
> p50, p95 e p99 di `Transaction.duration` sono forniti da rollup su lunghe finestre, quindi rimangono veloci per le visualizzazioni settimanali.

### Rendi i risultati leggibili con le etichette

Utilizza `AS` con virgolette singole per le etichette che contengono spazi:

```sql
SELECT count(*) AS 'Requests', percentile(duration, 95) AS 'p95 (ms)' FROM Transaction FACET service
```

>[!NOTE]
>
> Gli alias sono validi solo per `SELECT` elementi. Le colonne facet sono sempre etichettate con il nome attributo.

### Corrispondenza di pattern ed elenchi

`LIKE` utilizza `%` per un numero qualsiasi di caratteri e `_` per uno esatto:

```sql
SELECT count(*) AS requests FROM Transaction WHERE urlPath LIKE '/content/%.html' FACET urlPath LIMIT 20
```

Escludi risorse statiche con `NOT LIKE`:

```sql
SELECT count(*) AS requests FROM Transaction WHERE urlPath NOT LIKE '%.js' AND urlPath NOT LIKE '%.css' AND urlPath NOT LIKE '%.png' FACET urlPath LIMIT 20
```

Confronta con un elenco con `IN` e `NOT IN` (fino a 100 valori):

```sql
SELECT count(*) AS requests FROM Transaction WHERE http.method IN ('POST', 'PUT', 'DELETE') FACET service
```

>[!NOTE]
>
> SQL senza espressioni regolari (`RLIKE`). Combinare `LIKE` pattern con `OR` o utilizzare `IN`.

### Ordina e classifica con `ORDER BY`

Per impostazione predefinita, i risultati sfaccettati sono ordinati in base al primo aggregato. Utilizza `ORDER BY` per classificare in base a qualcos&#39;altro, ad esempio le operazioni più lente per p95:

```sql
SELECT count(*) AS requests, percentile(duration, 95) AS p95 FROM Transaction FACET name ORDER BY p95 DESC LIMIT 20
```

Oppure, prima di tutto, i servizi meno utilizzati:

```sql
SELECT count(*) AS requests FROM Transaction FACET service ORDER BY requests ASC LIMIT 10
```

>[!TIP]
>
> Nessun `HAVING`. Per trovare solo le operazioni &quot;occupate&quot;, classifica per `requests` e mantieni la N superiore, oppure aggiungi un `WHERE` che rimuove il rumore (ad esempio `WHERE http.method != 'OPTIONS'`).

### Raggruppa per diversi attributi

`FACET` accetta fino a cinque attributi. Ogni combinazione diventa una riga o una serie:

```sql
SELECT count(*) AS requests FROM Transaction WHERE http.statusCode >= 400 FACET service, http.method, http.statusCode LIMIT 30
```

### Esaminare una finestra passata

Nessun `COMPARE WITH`. Nei dashboard e in Esplora, modifica il **selettore ore**. Quando si chiama l&#39;API senza un intervallo esplicito, `SINCE … UNTIL …` fornisce una finestra passata:

```sql
SELECT count(*) AS lastWeekSameHour FROM Transaction SINCE 169 hours ago UNTIL 168 hours ago
```
