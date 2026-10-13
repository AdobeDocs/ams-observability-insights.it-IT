---
source-git-commit: 05ed98c07602488843c52cd262af2d8cc204fef1
workflow-type: tm+mt
source-wordcount: '1117'
ht-degree: 1%
---
### Creare un dashboard, passo dopo passo

#### Passaggio 1: prima di iniziare

- **Selezionare un singolo cliente** nel commutatore tenant. **Crea dashboard** è disabilitato mentre **Sono selezionati tutti gli account**. Impossibile eseguire SQL con il tenant demo.
- **Ruoli.** Chiunque abbia accesso al dashboard può aggiungere, modificare e salvare widget. La creazione o la modifica di **variabili** e l&#39;analisi della query di un widget sono limitate a **amministratori organizzazione** e **amministratori privilegiati**.

#### Passaggio 2: creare il dashboard

1. Apri **Dashboard** nel menu di navigazione a sinistra.
2. Fai clic su **Crea dashboard**.
3. Scegli un punto di partenza:

   | Opzione | I risultati ottenuti |
   | ---------------------------- | -------------------------------------------------------------------------------------------------------- |
   | **Creare un dashboard personalizzato** | Un dashboard vuoto con una scheda. |
   | **Nozioni di base su CPU** | CPU host e di processo (user/system/iowait/steal), throughput, carico, circa |
   | **Panoramica all-in-one** | KPI (volume, % errore, Apdex, p95), errori/problemi di traffico, tabelle CPU/memoria, thread JVM, operazioni lente. |
   | **Prime transazioni** | Apdex, p95 per operazione, carico per host/CPU, transazioni più lente con ID di traccia, suddivisione degli errori HTTP. |

4. Immetti un **nome** e conferma. Viene visualizzata l’area di lavoro del dashboard.

#### Passaggio 3: conoscere l’area di lavoro

| Area | Controlli |
| ---------------------------- | --------------------------------------------------------------------------------------------------------- |
| **Barra superiore** | **Aggiungi widget**, **Guida** (foglio di riferimento rapido in-app), **Importa**, **Esporta**, **Salva**, selezione ora e aggiorna |
| **Barra variabile** | Badge tenant, un selettore per variabile, **+ Variabile** (amministratori) |
| **Barra delle schede** | Cambia schede; rinomina (matita), elimina (×), aggiungi (+) |
| **Griglia** | 12 colonne. Trascinate i widget dal grip, ridimensionate dall&#39;angolo (minimo 2×2). |
| **Intestazione widget** (al passaggio del mouse) | Scarica CSV, scarica PNG, modifica, duplica, rimuovi, trascina handle |

&#x200B;> [!IMPORTANT]
> **Il salvataggio è esplicito.** Modifiche al layout, nuovi widget, schede e variabili rimangono nel browser fino a quando non fai clic su **Salva**. Il pulsante mostra _Salvataggio in corso... → salvato_ o _Salvataggio non riuscito_ (vedere [Risoluzione dei problemi](#troubleshooting)).

#### Passaggio 4: aggiungere un widget

Fare clic su **Aggiungi widget**. La finestra di dialogo dispone di tre modalità: **Query singola**, **Formula** e **Testo**. Inoltre, dispone di un selettore del tempo specifico, che consente di visualizzare l’anteprima del widget su qualsiasi finestra.

**Opzione A: Generatore (nessun codice)**

1. _(Facoltativo)_ **Inizia con un helper**: scegli uno dei 57 predefiniti, raggruppati come Golden signal, APM, AEM, Transactions, Traces &amp; spans, Infrastructure, Publishers, JVM &amp; runtime e Data Discovery. Un predefinito riempie il titolo, la query, il tipo di grafico e l’unità.
2. **Mostra**: scegliere `Count`, `Average`, `Percentile` (e il relativo livello), `Sum`, `Min`, `Max` o `Unique count`, quindi un attributo.
3. **da**: scegli l&#39;entità.
4. **Filtro**: aggiungere righe di `attribute operator value` (`=`, `!=`, `>`, `<`, `>=`, `<=`, `LIKE`). Tutte le righe sono combinate con `AND`.
5. **Raggruppa per**: scegli un attributo facet e **primo N** per mantenere solo la serie più occupata.
6. **Nel tempo**: attiva `TIMESERIES`, con dimensioni del bucket **ogni N minuti**.
7. Controlla l&#39;istruzione SQL generata nel modulo, quindi fai clic su **Esegui query**.

**Opzione B: editor SQL**

1. Passa da **Builder** a **SQL**. La modifica di un widget esistente si apre sempre in SQL.
2. Digitare o incollare una query. L’editor viene completato automaticamente dal catalogo live.
3. Utilizza il pannello **Guida query** per sfogliare entità, attributi ed esempi.
4. Fare clic su **Esegui query**. _`<Entity>`query è valida_, un&#39;anteprima live e un riepilogo (`N rows · N time points · N groups`). Viene visualizzato un avviso di _Sparse series_ quando sono presenti meno di tre punti temporali.

&#x200B;> [!TIP]
> Il Generatore di copre le query più comuni. Passa a SQL per `apdex`, `percentage`, `rate`, `FILTER`, più aggregati, `OR`, `IN`, diversi facet o `ORDER BY`.

#### Passaggio 5: configurare l’aspetto

| Campo | Note |
| --------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Titolo** | Fino a 200 caratteri. Può contenere `{{variable}}` token. |
| **Tipo di grafico** | `Line`, `Area`, `Bar`, `Pie / Donut`, `Number`, `Table`. Dopo ogni esecuzione, si consiglia un tipo di grafico fino a quando non ne scegli uno personalmente. |
| **Unità** | Facoltativo; fino a 40 caratteri (`ms`, `%`, `req/s`, `MB`, `bytes`). Lasciare vuoto il campo per utilizzare l&#39;unità dedotta (ad esempio `ms` per `duration`, `%` per `CpuUtilization`). |
| **Soglie colore** | Solo widget numero. Fino a 10 bande del modulo _a partire da `value` → Verde/Giallo/Rosso_. La banda corrispondente più alta vince. |

#### Passaggio 6: salva il widget, quindi la dashboard

**Il widget di salvataggio** è abilitato solo dopo che un&#39;esecuzione ha restituito dati. I nuovi widget sono costituiti da 6 colonne × 3 righe e sono compressi due per riga. Disporre i file, quindi fare clic su **Salva** nella barra superiore.

### Scegli il grafico a destra

| Forma Query | Grafico Migliore | Perché |
| ------------------------------------------------- | ------------------- | ---------------------------------------------- |
| Un aggregato, nessun `FACET`, nessun `TIMESERIES` | **Numero** | Un singolo riquadro KPI; aggiungi soglie. |
| Aggregato + `TIMESERIES` | **Riga** / **Area** | Una tendenza; una serie per valore di sfaccettatura. |
| Aggregato + `FACET` (pochi valori) | **Barra** / **Torta** | Confronta le categorie; torta per la quota di un intero. |
| Diversi aggregati + `FACET` | **Tabella** | Ogni aggregato diventa una colonna. |
| Attributi non elaborati | **Tabella** | Un elenco di eventi. |
| Aggregato + `FACET` + `TIMESERIES` su dati sparse | **Barra** | Legge meglio delle righe vuote. |

Quando la singola riga di un widget Numero contiene un valore di testo (ad esempio `latest(host)`), il widget lo mostra come sottotitolo.

### Widget di testo: note e intestazioni di sezione

**Aggiungi widget → testo**:

- **Nota**: testo normale (fino a 4.000 caratteri, righe vuote mantenute). Utilizzalo per collegamenti runbook, proprietari e definizioni SLO. Rendering di `{{variables}}` eseguito.
- **Intestazione sezione**: segno di spunta _Imposta come intestazione sezione_. Si estende per tutta la larghezza e la sua freccia comprime ogni widget sotto di esso, fino all&#39;intestazione di sezione successiva. Utilizzalo per raggruppamenti come **PUBLISH**, **AUTHOR** e **DISPATCHER**.

&#x200B;> [!TIP]
> I titoli dei widget che terminano in `- PUBLISH` o `- AUTHOR` ricevono un colore sottile che facilita la scansione delle dashboard di AEM per livello.

### Selettore ora

- Imposta la finestra per **ogni** widget e per le opzioni delle variabili basate su query.
- Sostituisce `SINCE` / `UNTIL` nelle query widget.
- La finestra massima è **31 giorni**.
- `TIMESERIES AUTO` adatta le dimensioni del bucket alla finestra.
- Il sottotitolo di ogni widget mostra la finestra effettivamente applicata, ad esempio _Da 1 ora fa_.
- I risultati vengono memorizzati nella cache per 30 secondi. Utilizza il pulsante di aggiornamento per forzare una nuova esecuzione.

### Variabili

Le variabili consentono a un dashboard di gestire più contesti, ad esempio un servizio, un ambiente o una soglia alla volta. Sono trattate in modo approfondito nel [tutorial 3](#87-make-dashboards-reusable-with-variables).

&#x200B;> [!WARNING]
> **Limitazione nota nella build corrente:** `{{variables}}` funziona nei widget **titoli** e **widget di testo**. All&#39;interno del widget **SQL**, viene eseguito il rendering nel dashboard, ma l&#39;anteprima **Aggiungi widget** e la verifica **Salva** lato server rifiutano il token `{{name}}` non elaborato con _Carattere imprevisto &#39;&lbrace;&#39;_. Finché questo non sarà risolto, usa valori letterali nelle query widget e usa **Duplica** per creare varianti.

### Widget formula

Una formula combina aritmeticamente 2-6 query SQL denominate, ad esempio `(errors / total) * 100`. Utilizzane uno quando un KPI richiede più di una query, ad esempio un rapporto, uno spazio di crescita, una somma di tassi o una velocità di masterizzazione. Consulta [Esercitazione 3](#86-combine-queries-with-formula-widgets).

### Condivisione, esportazione e importazione

| Azione | Effetto |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Salva** | Persiste nel nome, nelle schede, nei widget, nel layout e nelle variabili. Ogni query di widget viene riconvalidata lato server; un widget non valido non riesce nell’intero salvataggio. |
| **Esporta** | Scarica `<dashboard-name>.json` (schede, widget, variabili). |
| **Importa** | Carica un&#39;esportazione JSON **nel dashboard aperto**, sostituendo le relative schede e variabili. Quindi fai clic su **Salva**. Suggerimento: crea prima un dashboard vuoto, quindi importalo all’interno. |
| **CSV / PNG** | Download per widget dall&#39;intestazione del widget. |

&#x200B;> [!TIP]
> Le funzioni Esporta e Importa consentono di usare dashboard per il controllo delle versioni in Git e di promuovere un dashboard da un cliente all’altro. Il formato del file è descritto nell&#39;[appendice](#142-export-file-format).

### Suggerimenti per le prestazioni di dashboard e grafici

- **Utilizzare tabelle per set di risultati di grandi dimensioni** anziché barre o righe di molte serie.
- **Dividi dashboard occupati in schede.** Vengono eseguiti solo i widget della scheda visibile.
- **Sempre `LIMIT` query con facet**, in particolare su attributi ad alta cardinalità come `urlPath`, `traceId` e `jcr.path`.
- **Filtra in anticipo** gli attributi con bassa cardinalità (`service`, `kind`, `status`).
- **Preferisci`TIMESERIES AUTO`** per risolvere i piccoli bucket ed evitare `TIMESERIES MAX` tranne in pochi minuti.
- **Progetta tendenze a lungo termine per aggregazioni dati.** Vedi [Prestazioni](#performance).
