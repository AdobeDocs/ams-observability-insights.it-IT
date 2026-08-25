---
title: Introduzione a Observability Insights
description: Scopri come accedere a Observability Insights, cosa monitora Adobe per tuo conto e dove trovare ciò che ti serve in questa guida.
feature: Operations
role: Admin
source-git-commit: cc405e8b70973c33ecc6137114315998e8f9af50
workflow-type: tm+mt
source-wordcount: '533'
ht-degree: 0%

---


# Introduzione a Observability Insights {#get-started}

Questa sezione descrive le funzionalità di base per i nuovi utenti: come accedere all’account Observability Insights, quali ambienti e dati Adobe monitora per tuo conto e come consultare il resto della documentazione.

## Interfaccia Observability Insights {#observability-insights-interface}

Quando accedi a [insights.adobecqms.net](https://insights.adobecqms.net), la schermata di apertura ti offre un punto di ingresso in tutte le aree di monitoraggio per gli ambienti AEM Managed Services.

![Schermata di apertura di Observability Insights che mostra i punti di ingresso per il monitoraggio di APM e infrastruttura](../v2-assets/observability-catalog-listing.png)

L’interfaccia è organizzata in base a due aree di monitoraggio principali:

- **Applicazioni**: visualizza i dati sulle prestazioni delle applicazioni per i livelli Author e Publish. Utilizzare questa funzione per analizzare la velocità effettiva delle richieste, i tassi di errore, la latenza, il comportamento JVM e i dettagli di esecuzione a livello di traccia. Vedi [Applicazioni](../applications.md).
- **Host**: visualizza i dati di integrità a livello di host nella topologia gestita. Utilizzare questa funzione per valutare i segnali di CPU, memoria, disco, rete e storage su singoli server. Vedi [Host](../hosts.md).

Entrambe le aree sono di sola lettura per gli utenti del cliente. Adobe Managed Services gestisce il provisioning degli account, la strumentazione e il controllo amministrativo.

## Gestione di accessi e account {#access-overview}

L’accesso a Observability Insights viene gestito tramite Adobe IMS. Adobe esegue il provisioning e gestisce l’account della tua organizzazione; i team dei clienti ricevono l’accesso in sola lettura a tutti i dati monitorati.

Punti chiave:

- L’account Observability Insights della tua organizzazione è collegato a un singolo account principale di Adobe.
- Tutti gli ambienti inclusi nel contratto Managed Services (di authoring e pubblicazione, di produzione e non di produzione) generano rapporti su questo account.
- L’accesso utente viene fornito e gestito dal Customer Success Engineer (CSE).

Per i passaggi di provisioning, i ruoli utente e le operazioni che gli utenti possono e non possono eseguire, vedere [Gestione degli accessi e degli account](access-and-accounts.md).

## Copertura, ambienti e conservazione dei dati {#coverage-overview}

Adobe monitora i livelli di authoring e pubblicazione di AEM utilizzando il plug-in Java APM Observability Insights e tutti i server ospitati utilizzando l’agente dell’infrastruttura Observability Insights. Il monitoraggio è abilitato sia negli ambienti di produzione che in quelli non di produzione.

Punti chiave:

- Ogni ambiente AEM Managed Services include un’applicazione APM per Author e una per Publish.
- Le metriche APM, le metriche dell&#39;infrastruttura e gli eventi vengono conservati per un massimo di **30 giorni**.
- Observability Insights è adatto all’analisi operativa e al confronto delle tendenze recenti; non è uno strumento di archiviazione o di reporting a lungo termine. Acquisisci screenshot o prove esportate prima che i dati vengano cancellati.

Per informazioni dettagliate sulla copertura completa, tra cui il modo in cui le applicazioni vengono rappresentate nel tuo account e le implicazioni operative dell&#39;intervallo di conservazione, consulta [Copertura, ambienti e conservazione dei dati](coverage-and-data.md).

## Struttura della guida {#how-this-guide-is-structured}

La documentazione è suddivisa in quattro aree. Utilizza le descrizioni di seguito per passare direttamente a ciò che ti serve.

**Introduzione** - Questa sezione. Include l&#39;accesso, il provisioning degli account, l&#39;ambito di monitoraggio e la conservazione dei dati.

**[Utilizza Observability Insights](../use-observability-insights.md)**: guida orientata alle attività per le indagini quotidiane. Utilizza [Applicazioni](../applications.md) quando il sintomo è rivolto all&#39;applicazione: pagine lente, picchi di errore o transazioni instabili. Utilizza [Host](../hosts.md) per determinare se la pressione delle risorse a livello di host (CPU, memoria, disco o rete) spiega ciò che visualizzi nelle applicazioni. I flussi di analisi dettagliate sono disponibili in [Analisi dei problemi dell&#39;applicazione](../use-cases/investigate-application-issues.md) e [Analisi dei problemi dell&#39;infrastruttura](../use-cases/investigate-infrastructure-issues.md).

**[Domande frequenti](../troubleshooting/common-questions.md)**: domande comuni e punti di ingresso orientati al supporto per i casi in cui non si è sicuri da dove iniziare o si necessitano di risposte rapide durante un incidente in corso.
