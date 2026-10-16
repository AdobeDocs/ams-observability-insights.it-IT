---
title: Risoluzione di problemi
description: Risolvere i problemi relativi a query SQL, grafici e dashboard in Observability Insights.
source-git-commit: 873e7b9f279c3276fc0197969d667d40d022968a
workflow-type: tm+mt
source-wordcount: '335'
ht-degree: 5%
---

## Risoluzione di problemi {#troubleshooting}

I messaggi di errore vengono visualizzati nell&#39;editor quando **Convalida** o **Esegui** una query e nel widget stesso in un dashboard. Trova il messaggio nella prima colonna.

| Messaggio/sintomo | Causa | Correzione |
| ---------------------------------------------------------------------------- | ------------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------- |
| `Unknown entity 'X'` | Digita o non inserisci il nome nel catalogo | Selezionare [Dizionario dati](#data-dictionary) / pannello Guida query. |
| `Attribute 'x' is not available on Entity` | Attributo non presente nell&#39;entità (ad esempio `duration` in `CpuUtilization`) | Attributi di traccia e attributi di metrica. Vedere [Dizionario dati](#data-dictionary). |
| `Raw attributes cannot be mixed with aggregates; use FACET to group results` | `SELECT service, count(*)` | `SELECT count(*) … FACET service`. |
| `FACET and TIMESERIES require an aggregate query` | Query evento con FACET/TIMESERIES | Utilizza un aggregato. |
| `FACET supports at most five attributes` | > 5 facet | Ridurre. |
| `average requires a numeric attribute` | e.g. `average(service)` | Utilizzare `uniqueCount`/`latest` o un attributo numerico. |
| `rate requires TIMESERIES` / `rate is only available for counter measures` | `rate()` senza TIMESERIES o su un misuratore | Aggiungi `TIMESERIES AUTO`; utilizza un&#39;entità contatore ✔ ([Entità metriche curate](../references/data-dictionary.md#curated-metric-entities)); per i contatori utilizza `derivative()`. |
| `rate cannot be combined with other aggregates` | Più elementi con `rate()` | Un `rate()` per widget; combinare con una formula. |
| `percentOfTotal cannot be combined with TIMESERIES` | — | Rimuovi SERIE TEMPORALI. |
| `apdex requires an entity with a status attribute` | `apdex` su un&#39;entità metrica | Usa `Transaction`/`Span`/`AppRequest`. |
| `ORDER BY 'x' must reference a selected value or alias` | Ordinamento per elemento non selezionato | Alias dell&#39;aggregato e ordine in base all&#39;alias. |
| `IN requires between 1 and 100 values` | — | Dividere o utilizzare `LIKE`. |
| `Use IS NULL or IS NOT NULL for null comparisons` | `attr = NULL` | Usa `IS NULL`. |
| `Unexpected character '{'` | `{{variable}}` nell&#39;anteprima Aggiungi widget o al salvataggio | Consulta la limitazione dell&#39;[esercitazione 3](../tutorials/tutorial-3.md#make-dashboards-reusable-with-variables). |
| `Unexpected character ';'` / `'"'` | Punto e virgola o virgolette doppie | Rimuovi `;`; usa virgolette singole. |
| `Expected a positive duration such as 30 minutes` | `TIMESERIES 5m` | Scrivi `TIMESERIES 5 minutes`. |
| `Time range cannot exceed 31 days` | Selettore > 31 d | Restringi la finestra. |
| `Select a customer before running a SQL query` | Nessun tenant/tenant demo | Selezionare un cliente nel commutatore tenant. |
| `All queries in a formula must be the same kind…` | Combinazione di totale e SERIE TEMPORALI o un FACET | Rendere tutte le sottoquery normali o tutte `TIMESERIES AUTO`; rimuovere FACET. |
| `Query 'x' must select exactly one metric…` | La sottoquery ha due aggregati | Un aggregato per sottoquery. |
| `Unknown query name 'x' - must be one of: …` | La formula fa riferimento a un nome mancante | Fai corrispondere esattamente i nomi (distinzione maiuscole/minuscole). |
| **Salvataggio non riuscito** | Query widget non valida sul lato server (spesso `{{var}}`) | Aprire ogni widget modificato di recente e **Eseguirlo**; correggere o rimuovere quello non valido. |
| _Selezionare un valore per{{x}}_ | Nessuna selezione per la variabile | Scegliete un valore nella barra della variabile. |
| _Nessuna attività nell&#39;ultimo ..._ | Nessuna riga nella finestra | Ampliare la finestra; verificare i filtri con una query di individuazione ([Individuazione dati](../examples/query-cookbook.md#data-discovery)). |
| Linee divise in punti | Bucket più piccolo della cadenza dati | Utilizzare `TIMESERIES AUTO` (≥ 2 min) o un grafico a barre. |
| Facet mostra un gruppo di stringhe vuoto | Attributo mancante in alcune righe | Aggiungi `WHERE attr != ''`. |
| _Questo pannello SQL legacy non è più in esecuzione_ | Pannello dal generatore raw-SQL precedente | Modificare → ricompilare in SQL. |
