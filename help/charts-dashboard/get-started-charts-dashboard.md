---
title: Introduzione a grafici e dashboard
description: Scopri come visualizzare i dati di Observability Insights con grafici e dashboard.
source-git-commit: 143a9567da06f0137ef690795cfbc41bda1780ca
workflow-type: tm+mt
source-wordcount: '309'
ht-degree: 2%
---

## Grafici e dashboard: visualizzazione dei dati {#charts-and-dashboards-visualizing-your-data}

Le query consentono di trovare le informazioni necessarie. Grafici e dashboard rendono tali informazioni utili per la tua organizzazione. In questo documento viene illustrato come i risultati SQL diventano grafici e come creare, organizzare e condividere dashboard.

### Grafici

È possibile eseguire il rendering di ogni risultato SQL come uno dei sei tipi di grafico.

| Tipo di grafico | Utilizza per | Forma Query |
| --------------- | --------------------------------------------------- | ----------------------------------------------- |
| **Riga** | Tendenze nel tempo; una riga per valore di sfaccettatura | Aggregato + `TIMESERIES` |
| **Area** | Volume nel tempo, ad esempio throughput per servizio | Aggregato + `TIMESERIES` |
| **Barra** | Confronto di categorie; serie temporali sparse | Aggregato + `FACET` (± `TIMESERIES`) |
| **Torta/Anello** | Quota di un intero (combinazione di codici di stato, traffico per livello) | Un aggregato + `FACET`, pochi valori |
| **Numero** | Un singolo KPI, con soglie colore opzionali | Affissioni (solo aggregati) |
| **Tabella** | Più aggregati per gruppo o elenchi di eventi | Diversi aggregati + `FACET` o attributi non elaborati |

Dopo che un widget è su una dashboard puoi:

- **Modifica** la query, il titolo, il tipo di grafico e l&#39;unità (icona a forma di matita).
- **Duplica** per creare una variante, ad esempio lo stesso grafico per un altro livello.
- **Scarica CSV** delle righe correnti o **PNG** alla risoluzione 2×.
- **Trascinalo** per la maniglia di presa e **ridimensionalo** dall&#39;angolo in basso a destra.

### Dashboard

I grafici funzionano bene da soli, ma i dashboard li uniscono. Un dashboard è un insieme denominato con ambito tenant di **tabs**. Ogni scheda è una griglia a 12 colonne di **widget**.

| Concetto | Significato |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| **Dashboard** | Una raccolta denominata di 1-40 schede, memorizzata lato server per il tenant selezionato. |
| **Scheda** | Una griglia di widget (fino a 200). Rinominalo con la matita, eliminalo con ×, aggiungine uno con **+**. Deve rimanere almeno una scheda. |
| **Widget** | Una **query singola**, una **formula** o **testo** (una nota o un&#39;intestazione di sezione comprimibile). |
| **Variabile** | Selezione sopra la griglia. Il valore viene inserito come `{{name}}`. |
| **Selettore ora** | Finestra per ogni widget nel dashboard. |
