---
title: 'Esercitazione 1: Le prime query'
description: Scrivere le prime query SQL sulla telemetria di Observability Insights.
source-git-commit: 09c8edf64e744ff0c16acd0d5905fc6796490f82
workflow-type: tm+mt
source-wordcount: '549'
ht-degree: 0%
---

## Esercitazione 1: Le prime query {#tutorial-1-your-first-queries}

SQL. In questa esercitazione scriverai le prime query, controllerai la finestra temporale, le tendenze del tracciamento, filtrerai e raggrupperai risultati e inserirai il primo grafico in una dashboard. Ogni passaggio si basa sul precedente.

### Prima di iniziare

- Devi accedere a Observability Insights e ad almeno un tenant cliente che invia tracce o metriche.
- Seleziona **cliente** nel commutatore tenant. SQL non viene eseguito mentre è selezionato **Tutti gli account**.
- Apri **Esplora** e passa alla scheda **SQL**. Utilizza **Esegui** (⌘/Ctrl + Invio) dopo ogni query.

### Eseguire query sugli eventi con `SELECT`, `FROM` e `LIMIT`

Ogni query richiede due clausole: `SELECT` indica cosa restituire e `FROM` indica quale entità leggere. Inizia con le richieste in entrata:

```sql
SELECT timestamp, service, name, duration FROM Transaction
```

Questa è una **query evento**. Ogni riga corrisponde a una richiesta. Senza `LIMIT` si ottengono fino a 100 righe. Richiedi meno:

```sql
SELECT timestamp, service, name, duration FROM Transaction LIMIT 10
```

Le righe sono ordinate in base alla prima colonna, in ordine decrescente, pertanto il posizionamento di `timestamp` offre le richieste più recenti nella parte superiore.

>[!NOTE]
>
> SQL non ha `SELECT *`. Gli attributi dell’elenco mantengono i risultati piccoli e leggibili. Il [dizionario dati](#data-dictionary) e il completamento automatico nell&#39;editor mostrano i dati disponibili.

### Aggregare i dati

La maggior parte delle dashboard non mostra singoli eventi. Le riassumono. Sostituisci gli attributi con una **funzione di aggregazione**:

```sql
SELECT count(*) FROM Transaction
```

Questo è il numero di richieste in entrata nella finestra. Prova ancora:

```sql
SELECT average(duration) FROM Transaction
```

```sql
SELECT max(duration) FROM Transaction
```

```sql
SELECT uniqueCount(host) FROM Transaction
```

### Restituire più aggregati alla volta

Separa gli aggregati con virgole e denomina ciascuno con `AS`:

```sql
SELECT count(*) AS requests, average(duration) AS avgMs, max(duration) AS maxMs FROM Transaction
```

>[!TIP]
>
> Gli alias diventano intestazioni di colonna ed etichette di legenda e sono ciò a cui si fa riferimento in `ORDER BY`. Prendi l&#39;abitudine di denominare ogni aggregato.

### Impostare la finestra temporale

Per impostazione predefinita, una query copre l&#39;**ultima ora**. Usa `SINCE` per guardare più indietro:

```sql
SELECT count(*) AS requests FROM Transaction SINCE 1 day ago
```

Utilizza `UNTIL` per terminare la finestra in passato. Questo conta il traffico di ieri, da 48 a 24 ore fa:

```sql
SELECT count(*) AS requests FROM Transaction SINCE 2 days ago UNTIL 1 day ago
```

Le unità possono essere `second`, `minute`, `hour`, `day` o `week`. La finestra può essere al massimo di 31 giorni.

> **Importante**
>
> In **Esplora** e in **dashboard**, il **selettore ora** nella parte superiore della pagina controlla la finestra ed esegue l&#39;override di `SINCE`/`UNTIL`. Per esaminare un altro periodo, modificare il selettore. Il resto di questa guida lascia `SINCE` fuori dalle query del dashboard per questo motivo.

### Traccia le tendenze con `TIMESERIES`

Aggiungi `TIMESERIES` per trasformare un singolo numero in una riga nel tempo:

```sql
SELECT count(*) AS requests FROM Transaction TIMESERIES AUTO
```

`AUTO` sceglie una dimensione di bucket in modo che il grafico contenga circa 120 punti (2 minuti per una finestra di 1 ora, 15 minuti per 24 ore). Puoi anche impostare esplicitamente il bucket:

```sql
SELECT average(duration) AS avgMs FROM Transaction TIMESERIES 5 minutes
```

### Filtra con `WHERE`

`WHERE` mantiene solo le righe che ti interessano. Conteggio solo richieste non riuscite:

```sql
SELECT count(*) AS errors FROM Transaction WHERE status = 'Error'
```

Combinare le condizioni con `AND`, `OR` e parentesi:

```sql
SELECT count(*) AS slowOrFailed FROM Transaction WHERE service = 'checkout' AND (status = 'Error' OR duration > 2000)
```

Utilizza `LIKE` con `%` per le corrispondenze parziali:

```sql
SELECT count(*) AS contentRequests FROM Transaction WHERE urlPath LIKE '/content/%'
```

>[!NOTE]
>
> I valori stringa fanno distinzione tra maiuscole e minuscole e utilizzano apici: `'Error'`, non `"Error"` o `'error'`. Sostituisci `checkout` con un servizio del tuo tenant. Il passaggio successivo mostra come elencarli.

### Raggruppa con `FACET`

`FACET` raggruppa i risultati, come SQL `GROUP BY`. Elencare i servizi e la loro disponibilità:

```sql
SELECT count(*) AS requests FROM Transaction FACET service
```

Per impostazione predefinita, una query con facet restituisce i primi 10 gruppi. Utilizza `LIMIT` per visualizzare altri contenuti:

```sql
SELECT count(*) AS requests, average(duration) AS avgMs FROM Transaction FACET service LIMIT 50
```

Combina `FACET` e `TIMESERIES` per ottenere una riga per gruppo:

```sql
SELECT count(*) AS requests FROM Transaction FACET service TIMESERIES AUTO LIMIT 5
```

In questo caso, `LIMIT 5` mantiene le cinque serie più impegnate.

### Salvare la query in un dashboard

1. Apri **Dashboard → Crea dashboard → Crea un dashboard personalizzato** e denominalo _Primo dashboard_.
2. Fai clic su **Aggiungi widget**, scegli **Query singola** e passa a **SQL**.
3. Incollare la query dal passaggio 6.7 e fare clic su **Esegui query**.
4. Imposta **Titolo** su _Richieste per servizio_ e scegli il tipo di grafico **Riga**.
5. Fai clic su **Salva widget**, quindi su **Salva** nella barra superiore.

La prima dashboard è stata creata.
