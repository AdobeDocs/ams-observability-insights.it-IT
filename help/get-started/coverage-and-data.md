---
title: Copertura, ambienti e conservazione dei dati
description: Scopri i monitor di Observability Insights in AEM Managed Services, come vengono rappresentate le applicazioni e per quanto tempo vengono conservati i dati di monitoraggio.
feature: Operations
role: Admin
source-git-commit: 1d54a6a398360b040221db5b2780d301722894bf
workflow-type: tm+mt
source-wordcount: '267'
ht-degree: 1%

---


# Copertura, ambienti e conservazione dei dati {#coverage-environments-and-data-retention}

Questa pagina riepiloga quali dati vengono raccolti in Observability Insights for AEM Managed Services e come vengono organizzati.

## Monitoraggio della copertura {#monitoring-coverage}

Monitor Adobe:

- Livelli di authoring di AEM con il plug-in Java APM Observability Insights
- Livelli di pubblicazione di AEM con il plug-in Java APM Observability Insights
- Server in hosting nella topologia gestita con l’agente Observability Insights Infrastructure

Il monitoraggio personalizzato di APM e infrastruttura è abilitato sia negli ambienti Managed Services di produzione che in quelli non di produzione.

## Modalità di rappresentazione delle applicazioni {#how-applications-are-represented}

Ogni ambiente AEM Managed Services include in genere:

- Un’applicazione APM per l’authoring
- Un&#39;applicazione APM per la pubblicazione

Tutte le topologie di un contratto Managed Services vengono riportate in un unico account Observability Insights.

## Conservazione dei dati {#data-retention}

Le metriche APM, le metriche dell&#39;infrastruttura e gli eventi correlati vengono conservati per un massimo di **30 giorni**.

## Tabelle di riepilogo {#summary-tables}

| Area di copertura | Cosa viene monitorato |
| -------------- | ------------------------------------------ |
| APM | Applicazioni AEM Author e Publish |
| Infrastruttura | Tutti i server ospitati nella topologia gestita |

| Elemento | Rappresentazione |
| ------------------------------ | ------------------------------------------------------------- |
| Ambiente AEM | Un&#39;applicazione APM Author e un&#39;applicazione APM Publish |
| Account Observability Insights | Un account gestito da Adobe per ambito cliente Managed Services |

| Tipo di dati | Conservazione |
| --------------------------------- | ------------- |
| Metriche ed eventi APM | Fino a 30 giorni |
| Metriche ed eventi per l’infrastruttura | Fino a 30 giorni |

## Cosa significa operativamente {#what-this-means-operationally}

- Observability Insights è adatto per l’analisi operativa, gli incidenti attivi e il confronto delle tendenze recenti.
- L&#39;analisi cronologica oltre l&#39;intervallo di conservazione deve essere gestita tramite altri processi di reporting o archiviazione, se necessario.
- Durante l’analisi di problemi ricorrenti, acquisisci screenshot o prove esportate prima che i dati si deteriorino.
