---
title: 'Esercitazione 3: Migliorare il dashboard'
description: Crea dashboard con variabili, formule e più widget in Observability Insights.
source-git-commit: 143a9567da06f0137ef690795cfbc41bda1780ca
workflow-type: tm+mt
source-wordcount: '902'
ht-degree: 3%
---

## Esercitazione 3: Migliorare il dashboard {#tutorial-3-advance-your-dashboarding}

In questa esercitazione verranno creati i componenti di un dashboard di livello produzione: punteggi dell’esperienza utente, tassi di conteggio, raggruppamento di maiuscole e minuscole, istogrammi, tessere KPI con soglie, sezioni, widget di formule e variabili.

**In questa esercitazione:** [8.1 apdex](#81-score-user-experience-with-apdex) · [8.2 rate](#82-turn-counters-into-rates-with-rate) · [8.3 Casi](#83-group-values-into-cases-with-filter) · [8.4 Istogrammi](#84-build-a-latency-histogram) · [8.5 Tessere e sezioni KPI](#85-build-kpi-tiles-and-sections) · [8.6 Formule](#86-combine-queries-with-formula-widgets) · [8.7 Variabili](#87-make-dashboards-reusable-with-variables) · [8.8 Mettilo insieme](#88-put-it-together)

### Punteggio esperienza utente con `apdex`

Apdex converte la latenza e gli errori in un punteggio compreso tra 0 e 1. Con una soglia di 500 ms, una richiesta è _soddisfatta_ a un valore pari o inferiore a 500 ms, _tollerante_ fino a 2.000 ms e _frustrata_ al di sopra di **o in caso di errore**:

```sql
SELECT apdex(duration, 500) AS apdex FROM Transaction
```

Tendenza IT per servizio:

```sql
SELECT apdex(duration, 500) AS apdex FROM Transaction FACET service TIMESERIES AUTO LIMIT 8
```

Inseriscilo in un widget **Numero** con soglie `0 → Red`, `0.7 → Amber`, `0.85 → Green`. La fascia con il punteggio più alto vince, quindi i punteggi più bassi rimangono rossi.

### 8.2 Trasforma i contatori in tariffe con `rate`

Le entità contatore (✔ nel [dizionario dati](#53-curated-metric-entities)) memorizzano totali in continua crescita. `rate(value)` li converte in valori al secondo. Ha bisogno di `TIMESERIES` e deve essere l&#39;unico aggregato:

```sql
SELECT rate(value) AS rxBytesPerSec FROM NetworkReceiveRate FACET host TIMESERIES AUTO
```

```sql
SELECT rate(value) AS writeBytesPerSec FROM DiskWriteRate FACET host, device TIMESERIES AUTO LIMIT 10
```

>[!WARNING]
>
> Effettua sempre il facet `rate()` per `host` (e per `device` per i dischi) o filtra su un host. Senza questo, le letture da host diversi si mescolano tra loro e il risultato non ha senso.

Per ottenere una percentuale di **richiesta** dalle tracce, conta le richieste in bucket fissi. Questo fornisce richieste al minuto:

```sql
SELECT count(*) AS requestsPerMinute FROM Transaction TIMESERIES 1 minute
```

### Raggruppa i valori in casi con `FILTER`

`FACET CASES` di NRQL raggruppa le righe in contenitori denominati. In SQL, scrivere una colonna `FILTER` per ogni caso. Di seguito sono riportate le classi di stato per ogni servizio:

```sql
SELECT count(*) FILTER (WHERE http.statusCode < 400) AS ok, count(*) FILTER (WHERE http.statusCode >= 400 AND http.statusCode < 500) AS clientErrors, count(*) FILTER (WHERE http.statusCode >= 500) AS serverErrors FROM Transaction FACET service LIMIT 20
```

La stessa idea funziona nel tempo, fornendo una riga per ogni caso:

```sql
SELECT count(*) FILTER (WHERE urlPath LIKE '/content/%') AS pages, count(*) FILTER (WHERE urlPath LIKE '/bin/%') AS servlets, count(*) FILTER (WHERE urlPath LIKE '/graphql/%') AS graphql FROM Transaction TIMESERIES AUTO
```

### Creare un istogramma di latenza

SQL non ha una funzione `histogram()`. Crea i bucket autonomamente con `FILTER` ed esegui il rendering del risultato come widget **Barra** o **Tabella**:

```sql
SELECT count(*) FILTER (WHERE duration <= 100) AS 'le 100ms', count(*) FILTER (WHERE duration > 100 AND duration <= 250) AS '100-250ms', count(*) FILTER (WHERE duration > 250 AND duration <= 500) AS '250-500ms', count(*) FILTER (WHERE duration > 500 AND duration <= 1000) AS '0.5-1s', count(*) FILTER (WHERE duration > 1000 AND duration <= 2000) AS '1-2s', count(*) FILTER (WHERE duration > 2000) AS 'over 2s' FROM Transaction
```

Aggiungere `FACET service` per confrontare le distribuzioni di diversi servizi affiancate.

### Creare sezioni e riquadri KPI

Un dashboard valido inizia con una riga di tessere KPI, quindi raggruppa i grafici di dettaglio in sezioni.

1. Aggiungi quattro **widget numero**, ciascuno largo 3 colonne:

   | Titolo | Query | Unità | Soglie |
   | -------- | --------------------------------------------------------------------------- | ---- | ---------------------------------------- |
   | Richieste | `SELECT count(*) AS requests FROM Transaction` |      |                                          |
   | Errore % | `SELECT percentage(*, WHERE status = 'Error') AS errorPct FROM Transaction` | `%` | `0 → Green`, `1 → Amber`, `5 → Red` |
   | p95 | `SELECT percentile(duration, 95) AS p95 FROM Transaction` | `ms` | `0 → Green`, `800 → Amber`, `2000 → Red` |
   | Apdex | `SELECT apdex(duration, 500) AS apdex FROM Transaction` |      | `0 → Red`, `0.7 → Amber`, `0.85 → Green` |

2. Aggiungi un widget **Testo**, spunta _Imposta come intestazione di sezione_ e chiamala **LATENZA**. Aggiungi i grafici della latenza sotto di esso.
3. Aggiungi un&#39;altra intestazione di sezione, **ERRORI**, seguita da grafici degli errori. Comprimi una sezione con la freccia per evidenziare l&#39;altra.
4. Utilizza **tabs** per tipi di pubblico diversi, ad esempio _Panoramica_, _AEM_, _Infrastruttura_. Solo la scheda visibile esegue le relative query.

### Combinare query con widget formula

SQL non è in grado di eseguire calcoli in `SELECT` e non ha sottoquery. **I widget formula** colmano questo vuoto: eseguono 2-6 query con nome e combinano i risultati con `+ - * / ( )`.

**Esempio: richieste per host nel tempo** (formula, riga)

1. **Aggiungi widget → Formula**.
2. Denomina la prima query `requests` e la seconda `hosts`.
3. Immettere le query e la formula, quindi fare clic su **Esegui formula**.

Query `requests`:

```sql
SELECT count(*) AS v FROM Transaction TIMESERIES AUTO
```

Query `hosts`:

```sql
SELECT uniqueCount(host) AS v FROM Transaction TIMESERIES AUTO
```

Formula:

```text
requests / hosts
```

**Esempio: condivisione del tempo trascorso nel database** (formula, numero, unità `%`)

Query `db`:

```sql
SELECT sum(duration) AS v FROM Span WHERE db.system != ''
```

Query `server`:

```sql
SELECT sum(duration) AS v FROM Transaction
```

Formula:

```text
(db / server) * 100
```

Regole per le query di formula:

| Regola | Dettagli |
| --------------- | -------------------------------------------------------------------------------------------------------------------- |
| Nomi | Lettere, cifre e `_`, a partire da una lettera o `_`; fino a 40 caratteri; univoco |
| Espressione | `+ - * / ( )`, numeri e nomi di query; fino a 500 caratteri |
| Stessa forma | Tutte le query sono totali semplici (numero), **o** sono tutte `TIMESERIES` con **stesse dimensioni bucket** (riga/area/barra) |
| Una metrica per ciascuno | Ogni query restituisce esattamente un aggregato e nessun `FACET` |
| Dati mancanti | Un valore mancante o una divisione per zero produce un punto vuoto, mai `Infinity` o `NaN` |

>[!TIP]
>
> Utilizzare `TIMESERIES AUTO` in ogni query di una formula di serie temporali. Poiché le query condividono una finestra, vengono sempre risolte con la stessa dimensione di bucket.

### Rendi le dashboard riutilizzabili con le variabili

Le variabili consentono a un singolo dashboard di fornire molti servizi, livelli o soglie. Ogni variabile è un selettore nella barra delle variabili. Fai riferimento a esso come `{{name}}` nei titoli dei widget e nei widget di testo (e, una volta corretta la limitazione seguente, in widget SQL).

**Crea una variabile** (_org e super admins_): fai clic su **+ Variabile**.

| Campo | Note |
| ----------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Nome** | Lettere, cifre e `_`, a partire da una lettera o `_`. Deve essere univoco. Utilizzato come `{{name}}`. |
| **Etichetta** | Descrizione del selettore (facoltativo). |
| **Tipo** | **Elenco personalizzato** (un valore per riga), **Da una query** (una query SQL con un `FACET`; i relativi valori distinti diventano opzioni) o **Casella di testo** (testo libero). |
| **Consenti selezione di più valori** | Si espande in un elenco separato da virgole. Utilizzala con `IN (…)`. |
| **I valori sono numeri** | Inserisce valori non tra virgolette (`500` invece di `'500'`). |

**Funzionamento della sostituzione**

| Selezione | `{{svc}}` diventa |
| ---------------------------------- | ------------------------------------------------------- |
| Stringa singola `checkout` | `'checkout'` |
| Più valori `checkout`, `cart` | `'checkout', 'cart'` |
| Numero `500` | `500` |
| Valore contenente `'` | Escape come `''` |
| Nessuna selezione | Il widget attende e mostra _Seleziona un valore per{{svc}}_ |

```text
WHERE service = {{svc}}            single select
WHERE service IN ({{svc}})         multiple select
WHERE duration > {{slowMs}}        numeric text box
```

Per una variabile basata su query, **aggiungi sempre un`LIMIT`**. Per impostazione predefinita, una query con facet restituisce solo i primi 10 valori. Sono elencati fino a 200 servizi:

```sql
SELECT count(*) FROM Transaction FACET service LIMIT 200
```

Le opzioni si aggiornano quando cambia il tenant o il selettore ora e vengono ordinate alfabeticamente. Quando la selezione corrente non è più valida, il primo valore (selezione singola) o tutti i valori (selezione multipla) vengono selezionati automaticamente. La query di una variabile non può fare riferimento ad altre variabili.

>[!WARNING]
>
> **Limitazione della compilazione corrente.** La sostituzione avviene nella griglia del dashboard, ma l&#39;anteprima **Aggiungi widget** e il **controllo di salvataggio lato server** ricevono il token `{{name}}` non elaborato e lo rifiutano con `Unexpected character '{'`. In pratica:
>
> - `{{variables}}` in **titoli widget** e **widget di testo** funzionano in modo end-to-end.
> - `{{variables}}` all&#39;interno di **widget SQL** esegue correttamente il rendering dopo un **Import**, ma il dashboard non può essere **save** mentre tali widget esistono.
>
> Fino a quando questo problema non viene risolto, genera widget per contesto con filtri letterali (ad esempio `WHERE service = 'checkout'`) e utilizza **Duplica** per clonarli.

### Mettilo insieme

Nelle [blueprint del dashboard](#dashboard-blueprints) sono ora utilizzati tutti i blocchi predefiniti: tessere KPI con soglie, sezioni, percentili, Apdex, tassi, colonne di maiuscole/minuscole, istogrammi, formule e variabili. I blueprint mostrano come combinarli in dashboard completi per segnale d’oro, valutazione degli errori, AEM, infrastruttura e JVM.
