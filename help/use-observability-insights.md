---
title: Utilizzare Observability Insights
description: Scopri le quattro esperienze principali di monitoraggio e indagine in Observability Insights e quando utilizzarle tutte.
feature: Operations
role: Admin
source-git-commit: 6bbc906fa1c5570bc7ee2a6f536dd806c0c0db41
workflow-type: tm+mt
source-wordcount: '426'
ht-degree: 0%

---


# Utilizzare Observability Insights {#use-observability-insights}

In questa sezione vengono descritti i flussi di lavoro quotidiani di monitoraggio e di analisi più utilizzati dal team, organizzati in due aree di monitoraggio: Monitoraggio delle prestazioni delle applicazioni e Monitoraggio dell&#39;infrastruttura.

## Interfaccia Observability Insights {#observability-insights-interface}

Il pannello di navigazione a sinistra Observability Insights consente di accedere a tutte le aree di monitoraggio per gli ambienti AEM Managed Services.

![Interfaccia Observability Insights che mostra la navigazione a sinistra con le opzioni APM e Infrastructure e il dashboard di monitoraggio dell&#39;infrastruttura con le metriche host e i filtri dell&#39;ambiente](v2-assets/navigation-panel-desc.png)

La navigazione include:

- **Catalogo**: inventario centrale delle applicazioni e degli host AEM monitorati. Sfoglia le risorse tra **livelli Author, Publish e Dispatcher**, con indicatori di stato e prestazioni chiave quali tempo di risposta, velocità effettiva, tasso di errore e Apdex.

- **Esplora**: esamina la telemetria di osservabilità ed esamina i dati sulle prestazioni nelle risorse monitorate.

- **Tracce** - Analizzare le transazioni dell&#39;applicazione end-to-end e richiedere tracce per identificare latenza, errori e colli di bottiglia delle prestazioni.

- **Dashboard**: accedi a dashboard curate per una visualizzazione e un monitoraggio più approfonditi dei segnali di applicazioni e infrastrutture.

Le risorse possono essere filtrate per account e livello, mentre il catalogo fornisce una visualizzazione consolidata dello stato dell’applicazione e dell’host nella topologia AEM gestita.

## Applicazioni{#applications}

Utilizza [Applicazioni](applications.md) quando il problema riguarda l&#39;applicazione: pagine lente, tassi di errore crescenti, transazioni instabili o latenza imprevista durante l&#39;authoring o la pubblicazione.

Le applicazioni consentono di rispondere alle seguenti domande:

- Il problema è isolato per l’authoring, la pubblicazione o interessa entrambi i livelli?
- Quali endpoint o transazioni contribuiscono maggiormente al traffico e ai rallentamenti?
- La latenza o gli errori sono cambiati prima o dopo un picco di distribuzione o di traffico?
- Le tracce puntano alle operazioni dell’archivio, alle dipendenze esterne o alla pressione JVM?

Applicazioni strumenti AEM transazioni fino a chiamate di metodo, dipendenze esterne e operazioni di repository, in modo da poter passare rapidamente da un sintomo ampio a un percorso di esecuzione specifico.

## Host {#hosts}

Utilizzare [Host](hosts.md) quando è necessario determinare se il comportamento dell&#39;applicazione è causato o aggravato dalle condizioni delle risorse host: saturazione del CPU, pressione della memoria, I/O del disco, throughput di rete o capacità di archiviazione.

Il monitoraggio host consente di rispondere alle seguenti domande:

- Il rallentamento dell&#39;applicazione è accompagnato dalla pressione di CPU, memoria o I/O a livello di host?
- Un host si comporta in modo diverso dagli altri nello stesso ambiente?
- Le tendenze relative all&#39;utilizzo del disco o dello storage indicano un problema di capacità imminente?
- I modelli di infrastruttura spiegano il comportamento dell&#39;applicazione o sono un effetto a valle?

Utilizza le dashboard host insieme alle applicazioni per distinguere tra regressioni a livello di applicazione e vincoli delle risorse a livello di ambiente.

Entrambi gli articoli includono flussi di lavoro di indagine, domande per guidare la valutazione e un elenco di prove da acquisire quando si passa ad Adobe Managed Services.
