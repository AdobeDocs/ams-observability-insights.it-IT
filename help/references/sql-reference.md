---
source-git-commit: 4949fcad799260aa73354a8c0b9d79e6394cb257
workflow-type: tm+mt
source-wordcount: '1479'
ht-degree: 9%
---
## Riferimento SQL {#sql-reference}

Questo riferimento descrive la sintassi SQL, le clausole, le funzioni e gli operatori. Espandere ogni voce per dettagli ed esempi.

**In questo documento:** [Componenti query](#41-query-components) · [Clausole obbligatorie](#42-required-clauses) · [Clausole facoltative](#43-optional-clauses) · [Funzioni aggregatore](#44-aggregator-functions) · [Operatori e condizioni](#45-operators-and-conditions) · [Gestione tipi](#46-type-handling-blank-vs-null) · [SQL in SQL](#47-translate-sql-to-sql) · [Importazione dashboard esistenti](#48-importing-existing-dashboards-to-insights)

### Componenti query

```text
SELECT <select-item> [, <select-item> ...]
FROM   <Entity>
[WHERE      <condition>]
[FACET      <attribute> [, <attribute> ...]]          -- up to 5
[SINCE      <n> <unit> [AGO]]
[UNTIL      NOW | <n> <unit> [AGO]]
[TIMESERIES AUTO | MAX | <n> <unit>]
[ORDER BY   <alias-or-selected-name> [ASC | DESC]]
[LIMIT      <1..5000>]

<select-item> := <function>(<args>) [FILTER (WHERE <condition>)] [AS <alias>]
               | <attribute> [AS <alias>]
<unit>        := second | seconds | minute | minutes | hour | hours | day | days | week | weeks
```

> [!NOTE]
> Le `--` annotazioni di cui sopra sono solo documentazione. SQL stesso non accetta commenti.

### Clausole obbligatorie

<details>
<summary><code>SELECT</code> — cosa restituire</summary>

`SELECT` elenca **attributi raw** (una query evento) o **funzioni di aggregazione** (una query di aggregazione). Non si possono mescolare le due cose.

| Modulo | Esempio | Risultato |
| ------------ | -------------------------------------------------- | ------------------------------------------------- |
| Attributi | `SELECT timestamp, name, duration` | Righe di singoli eventi (impostazione predefinita: 100) |
| Aggregati | `SELECT count(*), average(duration)` | Un valore per aggregato (per facet/periodo fisso) |
| Con alias | `SELECT count(*) AS requests` | Etichetta colonna `requests` |
| Alias citato | `SELECT percentile(duration, 95) AS 'p95 latency'` | Etichetta con spazi |

Elenco eventi:

```sql
SELECT timestamp, service, name, duration, status, traceId FROM Transaction WHERE status = 'Error' LIMIT 50
```

Diversi aggregati:

```sql
SELECT count(*) AS requests, average(duration) AS avgMs, max(duration) AS maxMs FROM Transaction
```

Regole:

- `SELECT *` non è supportato. Elencare gli attributi desiderati.
- Matematica tra attributi o aggregati (`sum(a) / count(*)`) non supportata. Utilizza un widget [Formula](#86-combine-queries-with-formula-widgets).
- La combinazione di attributi e aggregati non riesce con _Gli attributi non elaborati non possono essere combinati con gli aggregati. Utilizzare FACET per raggruppare i risultati_.

</details>

<details>
<summary><code>FROM</code> — quale entità eseguire la query</summary>

`FROM` nomina esattamente **una** entità dal [dizionario dati](#data-dictionary). Il nome dell’entità non fa distinzione tra maiuscole e minuscole.

```sql
SELECT count(*) FROM Transaction
```

```sql
FROM Span SELECT count(*) FACET kind
```

Ogni entità ha un ambito fisso. Ad esempio, `Transaction` è sempre l&#39;estensione del server e `CpuUtilization` è sempre la metrica occupata da CPU con `value` convertito in percentuale. Non è possibile eseguire query su più di un&#39;entità in una singola query. Per combinare le entità, utilizzare un widget [Formula](#86-combine-queries-with-formula-widgets).

</details>

### Clausole facoltative

<details>
<summary><code>AS</code> — etichetta una colonna</summary>

Utilizzare `AS` per assegnare un nome leggibile a un aggregato o a un attributo. L&#39;alias diventa l&#39;intestazione della colonna, l&#39;etichetta della legenda e il nome a cui si fa riferimento in `ORDER BY`.

```sql
SELECT count(*) AS requests, percentile(duration, 95) AS p95 FROM Transaction FACET service ORDER BY p95 DESC LIMIT 10
```

Utilizza una stringa racchiusa tra virgolette singole per le etichette con spazi:

```sql
SELECT percentile(duration, 95) AS 'p95 latency (ms)' FROM Transaction TIMESERIES AUTO
```

</details>

<details>
<summary><code>FACET</code> — risultati gruppo</summary>

`FACET` raggruppa i risultati in base a un massimo di **5** attributi, ad esempio SQL `GROUP BY`.

```sql
SELECT count(*) FROM Transaction FACET service, http.method, http.statusCode LIMIT 30
```

| Query | Cosa significa `LIMIT n` | Predefiniti |
| ---------------------------- | ----------------------------------------------------------------------------------------------------------------------------- | --------------------------------- |
| `FACET` senza `TIMESERIES` | Numero di righe | 10 |
| `FACET` con `TIMESERIES` | **Prime n serie**, classificate in base alla somma del primo aggregato sull&#39;intera finestra. Ogni periodo fisso viene mantenuto per quelle serie. | Tutte le serie (con un limite di 5.000 righe) |

Serie temporali sfaccettate, primi 5:

```sql
SELECT count(*) AS requests FROM Transaction FACET service TIMESERIES AUTO LIMIT 5
```

I valori facet possono essere stringhe o numeri. Le stringhe vuote costituiscono il proprio gruppo. Escludili con `WHERE attr != ''`.

</details>

<details>
<summary><code>FILTER</code> — aggregati condizionali</summary>

Aggiungi `FILTER (WHERE condition)` a qualsiasi aggregato per calcolarlo in un sottoinsieme di righe. Questo consente di confrontare diverse condizioni in un’unica passata.

```sql
SELECT count(*) AS total, count(*) FILTER (WHERE status = 'Error') AS errors, percentile(duration, 95) FILTER (WHERE http.method = 'GET') AS getP95 FROM Transaction FACET service LIMIT 20
```

`FILTER` non è consentito con `apdex`, `rate` o `derivative`. `percentage` ha già la propria condizione.

</details>

<details>
<summary><code>LIMIT</code> — quante righe o serie</summary>

`LIMIT` accetta un numero intero positivo. I valori superiori a 5.000 vengono bloccati a 5.000.

| Forma risultato | Predefiniti |
| ------------ | ---------- |
| Affissioni | 1 |
| Facet | 10 |
| Serie temporali | 5.000 righe |
| Eventi | 100 |

```sql
SELECT count(*) FROM Transaction FACET urlPath LIMIT 50
```

> [!TIP]
> Imposta sempre `LIMIT` sulle variabili basate su query e sui facet ad alta cardinalità (`urlPath`, `traceId`, `jcr.path`, `url`).

</details>

<details>
<summary><code>ORDER BY</code> — ordina i risultati</summary>

`ORDER BY` ordina per un **alias selezionato o attributo selezionato**. La direzione predefinita è `DESC`.

```sql
SELECT count(*) AS requests, percentile(duration, 95) AS p95 FROM Transaction FACET name ORDER BY p95 DESC LIMIT 20
```

| Forma risultato | Ordine predefinito |
| ----------------- | --------------------------------------------------------------------------- |
| Affissioni/facet | Primo aggregato, `DESC` |
| Serie temporali | Ora, `ASC` |
| Eventi | Prima colonna selezionata, `DESC` (inserisci `timestamp` come prima colonna per &quot;la più recente&quot;) |

Regole:

- Il riferimento a un elemento non selezionato non riesce con _ORDER BY &#39;x&#39; deve fare riferimento a un valore o alias selezionato_.
- Non aggiungere `ORDER BY` a una query `TIMESERIES`. Ignora l’ordine del tempo necessario per i grafici.

</details>

<details>
<summary><code>SINCE</code> — inizio della finestra</summary>

`SINCE n unit [AGO]` imposta la distanza di inizio della finestra. Unità: `second`, `minute`, `hour`, `day`, `week` (singolare o plurale). `AGO` è facoltativo.

```sql
SELECT count(*) FROM Transaction SINCE 6 hours ago
```

- Il valore predefinito è `SINCE 1 hour ago`.
- La finestra non può superare **31 giorni** (_L&#39;intervallo di tempo non può superare i 31 giorni_).
- Le date assolute (`SINCE '2026-01-01'`) e `TODAY` / `YESTERDAY` non sono supportate.

> [!IMPORTANT]
> Nei dashboard e in Esplora, il selettore delle **ore sostituisce** `SINCE` e `UNTIL`. Vedi [Precedenza finestra temporale](#time-window-precedence).

</details>

<details>
<summary><code>TIMESERIES</code> — plottaggio nel tempo</summary>

`TIMESERIES` restituisce una riga per periodo fisso (per valore facet), che è pronto per i grafici a linee, ad area o a barre.

| Modulo | Dimensione bucket |
| ---------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| `TIMESERIES 5 minutes` | Esattamente quello che si richiede (qualsiasi unità) |
| `TIMESERIES AUTO` | Finestra ÷ 120, arrotondata a 1s / 5s / 10s / 15s / 30s / 1m / 5m / 10m / 15m / 30m / 1h / 3h / 6h / 12h / 1d. **Mai meno di 2 minuti.** |
| `TIMESERIES MAX` | Periodi fissi di 1 secondo. Usare solo in pochi minuti. |

`AUTO` bucket tipici:

| Finestra | 1 ora | 6 ore | 24 ore | 7 d | 30d |
| ------ | ----- | ----- | ------ | --- | ---- |
| Bucket | 2 min | 5 min. | 15 min. | 3 ore | 6 ore |

```sql
SELECT percentile(duration, 95) AS p95 FROM Transaction TIMESERIES AUTO
```

```sql
SELECT count(*) FROM Transaction TIMESERIES 1 hour SINCE 1 day ago
```

> [!TIP]
> Preferisci `AUTO` nei dashboard. Mantiene circa 120 punti qualunque sia il tempo indicato dal selettore. Evita inoltre le linee &quot;frammentate&quot; per host, perché le metriche host vengono raschiate ogni 90 secondi circa.

</details>

<details>
<summary><code>UNTIL</code> — fine della finestra</summary>

`UNTIL NOW` (impostazione predefinita) o `UNTIL n unit [AGO]`. Combinalo con `SINCE` per guardare una finestra passata:

```sql
SELECT count(*) AS yesterday FROM Transaction SINCE 2 days ago UNTIL 1 day ago
```

`SINCE` deve essere precedente a `UNTIL`. In caso contrario, la query non riesce con _SINCE deve essere precedente a UNTIL_.

</details>

<details>
<summary><code>WHERE</code> — filtra righe</summary>

`WHERE` mantiene solo le righe che corrispondono a una condizione. Combinare le condizioni con `AND`, `OR`, `NOT` e parentesi. Vedi [Operatori e condizioni](#45-operators-and-conditions).

```sql
SELECT count(*) FROM Transaction WHERE service = 'checkout' AND (status = 'Error' OR http.statusCode >= 500)
```

`WHERE` può essere visualizzato una sola volta. Mettici dentro tutte le condizioni.

</details>

### Funzioni aggregatore

| Funzione | Argomenti | Restituisce | Funziona su |
| -------------------------------------- | ----------------------------------- | ---------------------- | ------------------------------------- |
| [`apdex`](#fn-apdex) | durata numerica, soglia `T` | 0-1 | Entità di traccia |
| [`average` / `avg`](#fn-average) | numerico | Stessa unità dell&#39;ingresso | Qualsiasi |
| [`count`](#fn-count) | `*` o attributo | Numero di righe | Qualsiasi |
| [`derivative`](#fn-derivative) | numerico | Cambia al secondo | Qualsiasi (richiede `TIMESERIES`) |
| [`earliest`](#fn-latest) | qualsiasi [, rango] | Valore attributo | Qualsiasi |
| [`latest`](#fn-latest) | qualsiasi [, rango] | Valore attributo | Qualsiasi |
| [`max` / `min`](#fn-minmax) | qualsiasi | Stessa unità dell&#39;ingresso | Qualsiasi |
| [`percentage`](#fn-percentage) | `*` o attributo, condizione `WHERE` | % | Qualsiasi |
| [`percentile`](#fn-percentile) | numerico, livello `p` | Stessa unità dell&#39;ingresso | Qualsiasi |
| [`percentOfTotal`](#fn-percentoftotal) | numerico | % | Qualsiasi (nessun `TIMESERIES`) |
| [`rate`](#fn-rate) | contatore numerico | Al secondo | Entità contatore (richiede `TIMESERIES`) |
| [`stddev`](#fn-stddev) | numerico | Stessa unità dell&#39;ingresso | Qualsiasi |
| [`sum`](#fn-sum) | numerico | Stessa unità dell&#39;ingresso | Qualsiasi |
| [`uniqueCount`](#fn-uniquecount) | qualsiasi | Valori distinti | Qualsiasi |

<a id="fn-apdex"></a>

<details>
<summary><code>apdex(attribute, T)</code></summary>

Calcola un punteggio di [Apdex](https://www.apdex.org/) da 0 (frustrato) a 1 (soddisfatto), utilizzando la soglia `T` nell&#39;unità dell&#39;attributo (millisecondi per `duration`).

| Bucket | Condizione | Spessore |
| ---------- | ------------------------------------ | ------ |
| Soddisfatto | `duration ≤ T` e non un errore | 1 |
| Tollerante | `T < duration ≤ 4T` e non un errore | 0.5 |
| Frustrato | `duration > 4T`, **o qualsiasi errore** | 0 |

```sql
SELECT apdex(duration, 500) AS apdex FROM Transaction FACET service
```

- Funziona solo sulle entità di traccia (`Transaction`, `AppRequest`, `Span`).
- `T` deve essere maggiore di 0. `FILTER` non è consentito.
- `apdex(duration, 500)` su `Transaction` viene eseguito il rollup-accelerato su finestre lunghe.

</details>

<a id="fn-average"></a>

<details>
<summary><code>average(attribute)</code> / <code>avg(attribute)</code></summary>

Media aritmetica di un attributo numerico.

```sql
SELECT average(duration) AS avgMs FROM Transaction FACET service TIMESERIES AUTO
```

</details>

<a id="fn-count"></a>

<details>
<summary><code>count(*)</code> / <code>count(attribute)</code></summary>

- `count(*)` conta le righe.
- `count(attribute)` conta le righe in cui l&#39;attributo è **not NULL**. Per gli attributi stringa, i valori mancanti sono vuoti (`''`) anziché NULL, pertanto vengono ancora conteggiati (vedi [Gestione dei tipi](#46-type-handling-blank-vs-null)).

```sql
SELECT count(*) AS requests, count(http.statusCode) AS withStatusCode FROM Transaction
```

</details>

<a id="fn-derivative"></a>

<details>
<summary><code>derivative(attribute)</code></summary>

Modifica di `average(attribute)` al secondo tra bucket consecutivi: (questo bucket − quello precedente) ÷ bucket secondi. Il risultato può essere negativo. Utilizzalo sui misuratori per vedere se un valore sta crescendo o diminuendo.

```sql
SELECT derivative(value) AS heapGrowthPerSec FROM JvmHeapUsed WHERE host = 'publish-1' TIMESERIES 5 minutes
```

- Richiede `TIMESERIES` e deve essere l&#39;aggregato **only**. `FILTER` non è consentito.
- Il primo bucket non ha predecessori.

</details>

<a id="fn-latest"></a>

<details>
<summary><code>latest(attribute [, rankAttribute])</code> / <code>earliest(…)</code></summary>

- `latest(attr)` restituisce il valore della riga più recente. `earliest(attr)` restituisce il valore della riga meno recente.
- Con un secondo argomento, la riga viene scelta da tale attributo anziché dal tempo. `latest(traceId, duration)` restituisce l&#39;ID di traccia della richiesta **slower**. `earliest(traceId, duration)` restituisce il valore più veloce.

```sql
SELECT latest(value) AS current FROM MemoryUtilization FACET host
```

```sql
SELECT max(duration) AS slowestMs, latest(traceId, duration) AS slowestTrace FROM Transaction FACET name ORDER BY slowestMs DESC LIMIT 10
```

</details>

<a id="fn-minmax"></a>

<details>
<summary><code>max(attribute)</code> / <code>min(attribute)</code></summary>

Il valore più grande o più piccolo. Funziona su numeri, stringhe e marche temporali.

```sql
SELECT min(duration) AS fastestMs, max(duration) AS slowestMs, max(timestamp) AS lastSeen FROM Transaction FACET service
```

</details>

<a id="fn-percentage"></a>

<details>
<summary><code>percentage(*, WHERE condition)</code></summary>

La condivisione di righe corrispondenti a `condition`, da 0 a 100.

```sql
SELECT percentage(*, WHERE status = 'Error') AS errorPct FROM Transaction FACET service TIMESERIES AUTO
```

```sql
SELECT percentage(*, WHERE duration > 2000) AS slowPct FROM Transaction
```

`percentage` ha una propria condizione, quindi non accetta `FILTER`.

</details>

<a id="fn-percentile"></a>

<details>
<summary><code>percentile(attribute, p)</code></summary>

Il p-esimo percentile approssimativo, dove `0 < p ≤ 100`. Utilizza un `percentile` per livello.

```sql
SELECT percentile(duration, 50) AS p50, percentile(duration, 95) AS p95, percentile(duration, 99) AS p99 FROM Transaction TIMESERIES AUTO
```

p50, p95 e p99 su `Transaction` sono accelerati da rollup su lunghe finestre.

</details>

<a id="fn-percentoftotal"></a>

<details>
<summary><code>percentOfTotal(attribute)</code></summary>

La quota di ogni riga facet della **somma totale** di un attributo numerico. La somma delle righe è 100%.

```sql
SELECT sum(duration) AS totalMs, percentOfTotal(duration) AS shareOfTime FROM Transaction FACET name LIMIT 15
```

Non consentito con `TIMESERIES`.

</details>

<a id="fn-rate"></a>

<details>
<summary><code>rate(attribute)</code></summary>

Numero di contatori cumulativi **al secondo**. In ogni bucket, `rate` considera la lettura del contatore più alta per serie e la differenzia rispetto al bucket precedente. I resettaggi dei contatori hanno un valore pari a 0 e il primo bucket è 0.

```sql
SELECT rate(value) AS bytesPerSec FROM NetworkReceiveRate FACET host TIMESERIES AUTO
```

- Funziona solo sulle entità contatore (contrassegnate ✔ nel [Dizionario dati](#53-curated-metric-entities)).
- Richiede `TIMESERIES` e deve essere l&#39;aggregato **only**. `FILTER` non è consentito.

> [!WARNING]
> `FACET host` o filtra su un singolo host. Un `rate()` non sfaccettato in molti host confronta diversi contatori di host e produce numeri privi di significato. I contatori del disco sono per dispositivo, quindi `FACET host, device` è il più preciso.

</details>

<a id="fn-stddev"></a>

<details>
<summary><code>stddev(attribute)</code></summary>

Deviazione standard del campione. Utilizzalo con `average` per verificare la coerenza della latenza.

```sql
SELECT average(duration) AS avgMs, stddev(duration) AS stddevMs FROM Transaction FACET name LIMIT 20
```

</details>

<a id="fn-sum"></a>

<details>
<summary><code>sum(attribute)</code></summary>

Totale di un attributo numerico.

```sql
SELECT sum(callCount) AS calls FROM SpanBreakdown FACET segment LIMIT 20
```

</details>

<a id="fn-uniquecount"></a>

<details>
<summary><code>uniqueCount(attribute)</code></summary>

Numero **esatto** di valori distinti.

```sql
SELECT uniqueCount(host) AS hosts, uniqueCount(traceId) AS traces FROM Transaction FACET service
```

</details>

I vincoli di funzione in breve:

| Vincolo | Funzionalità |
| ------------------------------------- | ----------------------------------------------------------------------------------------- |
| Attributo numerico obbligatorio | `average`, `sum`, `percentile`, `stddev`, `percentOfTotal`, `rate`, `derivative`, `apdex` |
| Nessun `FILTER (WHERE …)` | `apdex`, `rate`, `derivative`, `percentage` |
| Richiede `TIMESERIES`, solo aggregato | `rate`, `derivative` |
| Non con `TIMESERIES` | `percentOfTotal` |
| Solo entità di traccia | `apdex` |
| Solo entità contatore | `rate` |

### Operatori e condizioni

| Operatore | Significato | Esempio |
| ------------------------- | -------------------------------------------------------------------------- | ---------------------------------------- |
| `=` | Uguale | `status = 'Error'` |
| `!=`, `<>` | Non uguale | `kind != 'Internal'` |
| `<`, `<=`, `>`, `>=` | Confronto | `http.statusCode >= 500` |
| `LIKE` | Corrispondenza con caratteri jolly: `%` = qualsiasi carattere, `_` = un carattere. Distinzione maiuscole/minuscole. | `urlPath LIKE '/content/%'` |
| `NOT LIKE` | Carattere jolly negato | `urlPath NOT LIKE '%.css'` |
| `IN (…)` | Corrisponde a uno qualsiasi di 1-100 valori | `service IN ('checkout', 'cart')` |
| `NOT IN (…)` | Non corrisponde a nessuno dei valori | `http.method NOT IN ('OPTIONS', 'HEAD')` |
| `IS NULL` / `IS NOT NULL` | Controllo nullo | `http.statusCode IS NOT NULL` |
| `AND`, `OR`, `NOT` | Logica | `NOT (status = 'Ok')` |
| `( … )` | Raggruppamento | `(a = 1 OR b = 2) AND c = 3` |

```sql
SELECT count(*) FROM Transaction WHERE (status = 'Error' OR duration > 2000) AND NOT host LIKE '%dispatcher%' AND http.method IN ('GET', 'POST')
```

> [!WARNING]
> `attr = NULL` è un errore. Usa `attr IS NULL`.

### Gestione dei tipi: vuoto e NULL

Ogni attributo ha un tipo nel catalogo: `string`, `number`, `boolean` o `timestamp`. Il tipo SQL verifica le funzioni in base a esso (ad esempio, `average(service)` è rifiutato). Non sono necessarie o disponibili funzioni di conversione dei tipi.

> [!IMPORTANT]
> **Vuoto vs. NULL** La maggior parte degli attributi di stringa proviene dalle mappe degli attributi OpenTelemetry, dove una chiave _missing_ viene letta come stringa vuota `''`, non `NULL`. Per mantenere solo le estensioni che contengono un attributo, utilizzare **`attr != ''`** anziché `IS NOT NULL`. Gli attributi numerici come `http.statusCode` sono autenticamente `NULL` quando assenti, quindi `IS NOT NULL` funziona per loro.

```sql
SELECT count(*) AS jcrCalls FROM Span WHERE jcr.operation != '' FACET jcr.operation
```

### Traduci da SQL a SQL

| SQL | SQL |
| ------------------------------------------------------------ | ------------------------------------------------------------------- |
| `WHERE ts > now() - INTERVAL 1 HOUR` | Niente. Il selettore ora (o `SINCE 1 hour ago`) imposta la finestra |
| `GROUP BY service` | `FACET service` |
| `GROUP BY toStartOfInterval(ts, INTERVAL 5 MINUTE), service` | `FACET service TIMESERIES 5 minutes` |
| `quantile(0.95)(duration)` | `percentile(duration, 95)` |
| `countIf(status = 'Error')` | `count(*) FILTER (WHERE status = 'Error')` |
| `100 * countIf(x) / count()` | `percentage(*, WHERE x)` |
| `count(DISTINCT host)` | `uniqueCount(host)` |
| `argMax(traceId, duration)` | `latest(traceId, duration)` |
| `x / sum(x) OVER ()` | `percentOfTotal(x)` |
| `ORDER BY p95 DESC LIMIT 20` | `ORDER BY p95 DESC LIMIT 20` |
| `HAVING count() > 100` | _Non supportato_. Ordina e `LIMIT`. |
| `JOIN` / sottoquery | _Non supportato_. Utilizza un widget **Formula**. |
| `CASE WHEN` | _Non supportato_. Usa `FILTER` colonne o `WHERE … LIKE` + `FACET`. |

### Importazione di dashboard esistenti in Insights

Se conosci la query esistente, di seguito sono riportati i dettagli della migrazione

| Query esistente | SQL | Note |
| ------------------------------------------- | ------------------------------------------------------------------ | ------------------------------------------------------------ |
| `FROM Transaction SELECT count(*)` | Uguale | Supporto di `FROM`-primo ordine |
| `SINCE 1 day ago` | Uguale | Ignorato nei dashboard; il selettore vince |
| `TIMESERIES AUTO` / `MAX` / `5 minutes` | Uguale | Il pavimento di `AUTO` è di 2 minuti |
| `FACET appName` | `FACET appName` / `FACET service` |                                                              |
| `percentage(count(*), WHERE error IS true)` | `percentage(*, WHERE status = 'Error')` |                                                              |
| `filter(count(*), WHERE …)` | `count(*) FILTER (WHERE …)` | Modulo standard SQL |
| `apdex(duration, t: 0.5)` | `apdex(duration, 500)` | `duration` in **ms** |
| `latest(x)` / `earliest(x)` | Uguale | Più un modulo classificato: `latest(x, rankAttr)` |
| `uniqueCount(x)` | Uguale | Esatto |
| `rate(count(*), 1 minute)` | `count(*)` con `TIMESERIES 1 minute` | SQL `rate()` è solo per contatori |
| `derivative(x, 1 minute)` | `derivative(x)` + `TIMESERIES` | Al secondo |
| `uniques(x)` | `count(*) … FACET x LIMIT n` |                                                              |
| `keyset()` / `SHOW EVENT TYPES` | [Query di individuazione](#101-data-discovery) o pannello della guida Query |                                                              |
| `histogram(duration, …)` | `FILTER` bucket | Consulta [Esercitazione 3](#84-build-a-latency-histogram) |
| `FACET CASES (…)` | `FILTER` colonne | Consulta [Esercitazione 3](#83-group-values-into-cases-with-filter) |
| `COMPARE WITH 1 week ago` | Non supportato | Modifica il selettore ora o utilizza `SINCE … UNTIL …` tramite l&#39;API |
| `RLIKE` | `LIKE` / `IN` |                                                              |
| Matematica in `SELECT` / aggregazione nidificata | Widget **Formula** |                                                              |
| `WITH TIMEZONE`, `EXTRAPOLATE`, `SLIDE BY` | Non supportato |                                                              |
