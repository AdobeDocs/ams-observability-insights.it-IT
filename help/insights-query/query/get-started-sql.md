---
source-git-commit: d3cd77e213674587152d6d33efa65328f420a547
workflow-type: tm+mt
source-wordcount: '671'
ht-degree: 1%
---
# Inizia

**Linguaggio query di Adobe Observability Insights (SQL)**: consente di eseguire query, grafici e dashboard della telemetria in Adobe Observability Insights.

|                |                                                                  |
| -------------- | ---------------------------------------------------------------- |
| **Si applica a** | Interfaccia utente approfondimenti → **Esplora** e **Dashboard** (dashboard personalizzati) |
| **Pubblico** | Ingegneri, SRE e utenti esperti che conoscono SQL |

&#x200B;> [!TIP]
> Ogni blocco di codice contrassegnato `sql` è una query SQL che è possibile incollare direttamente in **Esplora → SQL** o in **Dashboard → Aggiungi widget → SQL**.

## Introduzione a SQL: lingua della telemetria {#get-started-with-sql-the-language-of-your-telemetry}

Insights Il linguaggio di query è un linguaggio di query simile a SQL per i dati di Observability Insights. È la stessa lingua dietro ogni widget del dashboard personalizzato e la pagina **Esplora**, in modo da poter creare le visualizzazioni necessarie al team.

SQL funziona con **entità**, che sono viste logiche intuitive come `Transaction`, `Span` o `CpuUtilization`. Non è mai possibile eseguire query dirette sulle tabelle di archiviazione. Observability Insights compila ogni query SQL in una query warehouse sicura con parametri. La query viene sempre eseguita nel tenant e nell&#39;intervallo di tempo selezionato.

```sql
SELECT count(*) AS requests FROM Transaction TIMESERIES AUTO
```

### Operazioni possibili con SQL

Usa SQL per:

- **Genera grafici** di velocità effettiva, frequenza di errori, latenza, approssimativa, CPU, memoria, heap JVM, disco e rete nel tempo.
- **Rispondere a domande specifiche**, ad esempio &quot;quali percorsi URL sul livello di pubblicazione hanno il peggiore p95?&quot; o &quot;quale dominio a valle ha esito negativo?&quot;
- **Analizzare gli incidenti** eseguendo l&#39;espansione da un indicatore KPI a singole richieste e ID di traccia.
- **Crea dashboard** con KPI, tendenze, tabelle, formule, note, intestazioni di sezione e variabili.
- **Profilo dei carichi di lavoro di AEM** (Adobe Experience Manager) utilizzando gli attributi Sling, JSP, JCR, Dispatcher e GraphQL.

SQL supporta sia **query evento**, che restituiscono singole righe di richiesta, sia **query aggregate**, che restituiscono totali, percentili, percentuali, raggruppamenti e serie temporali.

&#x200B;> [!TIP]
> I modelli in **Dashboard → Crea dashboard** sono interamente generati da SQL. Apri qualsiasi widget modello con l’icona a forma di matita per visualizzare la query corrispondente e imparare da essa.

### Elementi su cui è possibile eseguire query

SQL può eseguire query su quattro famiglie di dati. L&#39;elenco completo si trova nel [dizionario dati](#data-dictionary).

| Famiglia | Entità | Domande tipiche |
| ------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------- |
| **Tracce (APM)** | `Transaction` (richieste in entrata), `AppRequest` (richieste + consumer di messaggi), `Span` (ogni intervallo) | Throughput, latenza, errori, Apdex, chiamate downstream, percorsi del codice AEM |
| **Metriche host e runtime curate** | `CpuUtilization`, `MemoryUtilization`, `LoadAverage5m`, `DiskUtilization`, `NetworkReceiveRate`, `JvmHeapUsed`, `JvmThreadCount`, `DispatcherRequestRate` e altri | Saturazione, capacità, stato JVM, carico del dispatcher |
| **Metriche generiche** | `MetricGauge`, `MetricSum` | Qualsiasi metrica OpenTelemetry per nome, individuazione metrica |
| **Composizione span preaggregata** | `SpanBreakdown` | Quali operazioni figlio costituiscono una transazione |

&#x200B;> [!NOTE]
> I registri, le definizioni degli avvisi e le relazioni di inventario non sono disponibili.

### Dove utilizzare SQL

| Dove | Come aprirlo | A cosa serve |
| ----------------------- | ------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Esplora** | Navigazione a sinistra → **Esplora** | Analisi ad hoc. Utilizza **Visual Builder** o la scheda **SQL**, **Convalida**, quindi **Esegui** (⌘/Ctrl + Invio). Include **Cronologia** e **query salvate** e un pannello **Guida query**. |
| **Dashboard** | Il → di navigazione sinistro **Dashboard** → aprire o creare un dashboard → **Aggiungi widget** | Grafici, KPI e tabelle persistenti, combinati in dashboard. |
| **Widget formula** | **Aggiungi widget → Formula** | Combina 2-6 query SQL con dati aritmetici (rapporti, spazio di crescita, velocità di masterizzazione). |
| **Variabili del dashboard** | **+ Variabile → da una query** | Compilare un selettore dai valori distinti di una query SQL `FACET`. |
| **Verifica query** | Passare il puntatore del mouse su un grafico → controllare la query | Visualizzare l&#39;istruzione SQL sottostante un grafico integrato o dashboard. |

### Struttura query SQL

SQL segue una sintassi basata su una clausola. Questa query conta tutte le richieste in entrata nella finestra selezionata:

```sql
SELECT count(*) FROM Transaction
```

Di seguito sono elencate tutte le clausole disponibili:

```text
SELECT  function(attribute) [FILTER (WHERE condition)] [AS alias] [, ...]
        | attribute [AS alias] [, ...]
FROM    Entity
  [WHERE      condition [AND | OR ...]]
  [FACET      attribute [, attribute ...]]        -- up to 5
  [SINCE      n unit [AGO]]
  [UNTIL      NOW | n unit [AGO]]
  [TIMESERIES AUTO | MAX | n unit]
  [ORDER BY   alias [ASC | DESC]]
  [LIMIT      number]                              -- 1 to 5000
```

Di seguito sono riportate le regole SQL seguenti:

| Regola | Dettagli |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Clausole obbligatorie** | `SELECT` e `FROM` sono obbligatori. Ogni altra clausola è facoltativa. È possibile iniziare con: `FROM Transaction SELECT count(*)` è valido anche. |
| **Ordine clausole** | Le clausole successive a `FROM` possono essere visualizzate in qualsiasi ordine. Ogni clausola può essere visualizzata una sola volta. |
| **Dimensione query** | Una query non può superare i 4 KB. |
| **Distinzione maiuscole/minuscole** | Parole chiave, nomi di funzioni, nomi di entità e nomi di attributi sono **senza** distinzione tra maiuscole e minuscole. I valori stringa **sono** con distinzione tra maiuscole e minuscole (`'Error'` ≠ `'error'`). |
| **Stringhe** | Usa virgolette singole: `WHERE status = 'Error'`. Per includere un preventivo, raddoppiarlo: `'O''Brien'`. Le virgolette doppie non sono accettate. |
| **Valori letterali** | Stringhe, numeri (inclusi negativi e decimali), `TRUE`, `FALSE`, `NULL`. |
| **Nomi di attributo** | I nomi possono contenere lettere, cifre, `_` e `.` (ad esempio `http.statusCode`). Non sono necessari backtick. |
| **Aggregati e attributi** | Una query seleziona attributi non elaborati (un elenco di eventi) o aggrega, mai entrambi. Per raggruppare, utilizzare `FACET`. |
| **Intervallo temporale** | L&#39;impostazione predefinita è l&#39;ultima ora. Il massimo è di 31 giorni. Nei dashboard e in Esplora, il **selettore ore** imposta la finestra (vedi [Finestra temporale](#time-window-precedence)). |
| **Non supportato** | Punti e virgola, commenti, sottoquery, join, `SELECT *`, corrispondenza tra gli attributi in `SELECT`, `HAVING`, `COMPARE WITH`. Per informazioni sulle alternative, vedere [Funzionalità non disponibili in SQL](#what-sql-doesnt-do). |
