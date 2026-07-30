---
title: Monitorare l’ambiente AEM Managed Services con Synoptryx
description: 'Panoramica del monitoraggio di Synoptryx su Adobe Experience Manager Managed Services: quali monitor Adobe, come è configurato l’account e come il team ha accesso.'
feature: Operations
role: Admin
source-git-commit: f937aa4e3cebd1aae6945a35a77154add5db980c
workflow-type: tm+mt
source-wordcount: '651'
ht-degree: 0%

---


# Monitorare l’ambiente AEM Managed Services con Synoptryx {#synoptryx-monitoring}

Synoptryx offre al team la visibilità delle prestazioni delle applicazioni, dello stato dell&#39;infrastruttura e dell&#39;esperienza dell&#39;utente finale, senza dover configurare una piattaforma di monitoraggio separata.

>[!NOTE]
>
> È disponibile un white paper di panoramica del prodotto Synoptryx per una panoramica completa dell’osservabilità e del monitoraggio di AEM Managed Services, ideale per la condivisione con le parti interessate o la revisione offline.

## Panoramica {#overview}

Synoptryx è la piattaforma di osservabilità di nuova generazione di Adobe progettata per fornire una visibilità unificata delle prestazioni delle applicazioni, dello stato dell&#39;infrastruttura e del monitoraggio sintetico. Consente il monitoraggio proattivo dei servizi aziendali critici attraverso un&#39;unica esperienza integrata. Synoptryx combina il monitoraggio delle prestazioni delle applicazioni (APM), il monitoraggio dell&#39;infrastruttura e il monitoraggio sintetico del Percorso di utenti per aiutare a identificare e risolvere i problemi prima che influiscano sugli utenti finali. La piattaforma offre funzionalità avanzate di analisi delle transazioni, insights JVM, telemetria dell&#39;infrastruttura e diagnostica avanzata per un&#39;analisi più rapida delle root cause. Basato sulle moderne tecnologie di osservabilità, fornisce un monitoraggio scalabile e sicuro in ambienti aziendali complessi. Synoptryx offre funzioni estese di conservazione dei dati, dashboard avanzati e analisi intelligenti per supportare l’eccellenza operativa. Un’esperienza di accesso fluida con Adobe IMS garantisce accesso sicuro e governance. La piattaforma è progettata per migliorare l&#39;affidabilità del servizio, accelerare la risoluzione dei problemi e migliorare l&#39;esperienza del cliente. Synoptryx, la soluzione di osservabilità strategica di Adobe, fornisce una base pronta per il futuro per il monitoraggio, l&#39;automazione e le informazioni operative negli ambienti di servizi gestiti.

Synoptryx è incluso in Adobe Experience Manager Managed Services: non è richiesta alcuna piattaforma di monitoraggio o licenza separata. Adobe monitora la disponibilità e le prestazioni dell’ambiente come parte dell’offerta standard e Synoptryx è la piattaforma dedicata che il team può utilizzare per comprendere le prestazioni dell’applicazione Adobe Experience Manager (AEM) e dell’infrastruttura di supporto.

Questa guida spiega cosa viene monitorato, come viene configurato l’account Synoptryx e come esplorare le dashboard utilizzate per l’analisi quotidiana e la risoluzione dei problemi.

## Panoramica {#at-a-glance}

Come parte di AEM Managed Services, riceverai:

- **Account Synoptryx dedicato**: fornito e supervisionato da Adobe Managed Services, con accesso in sola lettura per il team.
- **Monitoraggio delle transazioni AEM approfondite**: l&#39;agente APM Synoptryx traccia le transazioni significative fino alle chiamate di metodo (inclusi i numeri di riga), alle dipendenze esterne e alle operazioni dell&#39;archivio.
- **Visualizzazione unificata dell&#39;infrastruttura e dell&#39;applicazione**: combinazione di metriche APM e a livello di host per ottimizzare le prestazioni in modo olistico.

## Cosa monitora Adobe con Synoptryx {#what-we-monitor}

Adobe monitora i livelli di AEM **Author** e **Publish** con il plug-in Java Synoptryx APM. Tutti i server ospitati nella topologia vengono monitorati con l&#39;agente dell&#39;infrastruttura Synoptryx. Il monitoraggio personalizzato di APM e infrastruttura è abilitato sia negli ambienti Managed Services di produzione che in quelli non di produzione.

![Diagramma che mostra il monitoraggio di Synoptryx APM e dell&#39;infrastruttura nei server AEM Author, Publish e in hosting](assets/image6.png)

### Applicazioni nel tuo account {#applications-in-your-account}

L’account Synoptryx è collegato a un singolo account principale Adobe e può ricevere dati da più applicazioni, tra cui:

- Un&#39;applicazione APM per il livello **Author** per ogni ambiente AEM Managed Services
- Un&#39;applicazione APM per il livello **Pubblica** per ogni ambiente AEM Managed Services

Ogni applicazione dispone di una propria chiave di licenza. Tutte le topologie nel contratto Managed Services vengono riportate in un unico account Synoptryx. Le metriche e gli eventi di APM e Infrastructure vengono conservati per un massimo di **30 giorni**.

## Accesso e account {#access}

I dati di monitoraggio sono consolidati in un account Synoptryx gestito e gestito da Adobe. Il tuo team riceve **l&#39;accesso completo in sola lettura** a tutte le metriche di APM e infrastruttura raccolte dagli agenti. Adobe Managed Services mantiene la proprietà e il controllo amministrativo dell’account.

>[!NOTE]
>
> **Per accedere a:** l&#39;accesso a Synoptryx richiede il provisioning di Adobe IMS. Il Customer Success Engineer (CSE) può effettuare il provisioning e gestire l’accesso degli utenti per la tua organizzazione.

Dopo che il CSE ha eseguito il provisioning dell&#39;account, puoi accedere a [synoptryx.adobecqms.net](https://synoptryx.adobecqms.net).

## Passaggio successivo {#whats-next}

Continua con le dashboard di monitoraggio utilizzate quotidianamente dal tuo team:

- [Monitoraggio delle prestazioni delle applicazioni (APM)](application-performance-monitoring.md): traccia le transazioni AEM, analizza il comportamento di JVM e controlla i servizi esterni.
- [Monitoraggio dell&#39;infrastruttura](infrastructure-monitoring.md): verifica delle metriche di sistema, rete, processo e archiviazione a livello di host.
