---
title: Monitoraggio dell’infrastruttura con Observability Insights
description: Scopri quando utilizzare le dashboard dell’infrastruttura, quali segnali esaminare per primi e dove trovare il riferimento completo alle metriche dell’host.
feature: Operations
role: Admin
source-git-commit: 825334e003ae814af1b0845c6de1a533b4b5f47b
workflow-type: tm+mt
source-wordcount: '580'
ht-degree: 0%

---


# Host {#hosts}

Utilizza gli host in Observability Insights per monitorare lo stato, le prestazioni e l’utilizzo delle risorse dell’infrastruttura che supporta le applicazioni e i servizi. Utilizzare i dashboard dell&#39;infrastruttura per identificare i problemi relativi alla capacità host, alla pressione di storage, alla velocità effettiva di rete o al conflitto di risorse del sistema operativo.

## Quali sono i vantaggi del monitoraggio dell&#39;infrastruttura? {#what-infrastructure-monitoring-helps-you-answer}

Il monitoraggio dell&#39;infrastruttura è particolarmente utile quando è necessario rispondere a domande quali:

- Il rallentamento dell&#39;applicazione è accompagnato dalla pressione di CPU, memoria o I/O?
- Un host si comporta in modo diverso dagli altri nello stesso ambiente?
- I modelli di rete o disco cambiano durante lo stesso intervallo di un problema che interessa il cliente?
- Le tendenze di utilizzo dello storage indicano un problema di capacità imminente?

## Accesso agli host dell&#39;infrastruttura {#infrastructure-host-overview}

Il monitoraggio dell&#39;infrastruttura fornisce visibilità a livello di host sullo stato e sulle prestazioni dell&#39;infrastruttura che supporta gli ambienti AEM gestiti. Dal **Catalogo osservabilità**, è possibile sfogliare gli host dell&#39;infrastruttura ed eseguire il drilling in un singolo host per analizzare CPU, memoria, rete, storage e altri segnali a livello di sistema.

## Accesso agli host dell&#39;infrastruttura

Da **Catalogo**, seleziona la scheda **Host** per visualizzare l&#39;infrastruttura associata all&#39;account selezionato.

![Host dell&#39;infrastruttura](v2-assets/1_host.png)

La visualizzazione **Host dell&#39;infrastruttura** fornisce un inventario degli host monitorati e include:

- **Nome host**: nome dell&#39;host dell&#39;infrastruttura monitorata.
- **Account** — Account associato all&#39;host.
- **Ambiente** — Classificazione ambiente, ad esempio `DEV` o `STAGE`.
- **Integrità**: stato di integrità corrente dell&#39;host.
- **Ultima visualizzazione** - Recente ricezione della telemetria dall&#39;host.

![HostsOverview](v2-assets/2_hostOverview.png)

## Flusso di indagine consigliato {#suggested-investigation-flow}

Per la maggior parte degli incidenti, controlla il dashboard host in questo ordine:

1. Controllare l&#39;utilizzo del CPU, la media di carico e l&#39;utilizzo della memoria per verificare una saturazione evidente.
2. Esaminare l&#39;attesa di I/O CPU e il throughput del disco se i tempi di risposta aumentano senza un picco CPU corrispondente.
3. Confrontare il throughput di rete con il traffico dell&#39;applicazione per identificare gli spostamenti correlati al carico.
4. Controllare l&#39;utilizzo dello storage e l&#39;utilizzo del disco a livello di file system per il rischio di capacità persistente.
5. Confronta più host per verificare se il problema è localizzato o sistemico.

## Cosa esaminare per primo {#what-to-review-first}

- **CPU e memoria** quando un&#39;applicazione appare lenta o instabile in un intervallo di tempo più ampio.
- **I/O del disco e I/O di CPU in attesa** quando le richieste si bloccano o si mettono in coda in modo imprevisto.
- **I/O di rete** quando si sospettano modifiche delle caratteristiche del traffico o dipendenze downstream.
- **Utilizzo dello spazio di archiviazione** quando gli incidenti comportano errori di distribuzione, pressione di indicizzazione o problemi di capacità a lungo termine.

Utilizza il campo **Name contains** e il filtro **Tier** per limitare l&#39;elenco degli host. Seleziona un nome host per aprire i dettagli di monitoraggio dell’infrastruttura.

## Monitoraggio host

Dopo aver selezionato un host, la visualizzazione **Infrastruttura** fornisce pagine di monitoraggio dedicate per tale host.

La navigazione host include:

- **Panoramica**: visualizzazione consolidata dei segnali chiave di utilizzo e di integrità dell&#39;infrastruttura.
- **Metriche**: metriche dettagliate delle prestazioni dell&#39;host, tra cui CPU, memoria, carico, I/O su disco e throughput di rete.
- **Rete**: traffico di rete, attività dell&#39;interfaccia ed errori di trasmissione/ricezione.
- **Processo**: monitoraggio a livello di processo host.
- **Archiviazione**: utilizzo del disco, I/O del disco e utilizzo del file system.
- **Sistema**: metriche delle risorse di sistema di base quali CPU, memoria e media di carico.

## Domande a cui rispondere durante l&#39;indagine {#questions-to-answer}

- Il problema è isolato per un host o visibile in tutto l’ambiente?
- I segnali CPU, di memoria o del disco sono elevati durante la stessa finestra dell&#39;incidente?
- La crescita dello storage tende verso una soglia che potrebbe influire sulle operazioni?
- I sintomi dell&#39;infrastruttura spiegano il comportamento dell&#39;applicazione o appaiono solo come un effetto a valle?

## Prove da acquisire in caso di escalation {#evidence-to-capture}

- Host e ambiente interessati
- Finestra temporale del problema
- Schermate relative a CPU, memoria, disco e rete
- Se l’anomalia è isolata o sistemica
- Sintomi dell’applicazione correlati da APM
