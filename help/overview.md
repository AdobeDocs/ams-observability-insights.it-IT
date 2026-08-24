---
title: Monitora il tuo ambiente AEM Managed Services con [!DNL Synoptryx]
description: 'Panoramica del monitoraggio di  [!DNL Synoptryx] su Adobe [!DNL Experience Manager] Managed Services: cosa controlla Adobe, come è configurato il tuo account e come il tuo team ha accesso.'
feature: Operations
role: Admin
source-git-commit: e8de2213d91e09da68a8f7014b075f81bd7f07ef
workflow-type: tm+mt
source-wordcount: '618'
ht-degree: 0%

---


# Monitora l&#39;ambiente AEM Managed Services con [!DNL Synoptryx] {#synoptryx-monitoring}

[!DNL Synoptryx] offre al team la visibilità delle prestazioni delle applicazioni, dello stato dell&#39;infrastruttura e dell&#39;esperienza dell&#39;utente finale, senza dover configurare una piattaforma di monitoraggio separata.

>[!NOTE]
>
> Un white paper di panoramica del prodotto [!DNL Synoptryx] è disponibile per la panoramica completa di monitoraggio e osservabilità di AEM Managed Services, ideale per la condivisione con le parti interessate o la revisione offline.

## Panoramica {#overview}

[!DNL Synoptryx] è la piattaforma di osservabilità di nuova generazione di Adobe progettata per fornire una visibilità unificata per le prestazioni delle applicazioni, lo stato dell&#39;infrastruttura e il monitoraggio sintetico. Consente il monitoraggio proattivo dei servizi aziendali critici attraverso un&#39;unica esperienza integrata. [!DNL Synoptryx] combina il monitoraggio delle prestazioni delle applicazioni (APM), il monitoraggio dell&#39;infrastruttura e il monitoraggio sintetico del Percorso di utenti per aiutare a identificare e risolvere i problemi prima che influiscano sugli utenti finali. La piattaforma offre funzionalità avanzate di analisi delle transazioni, insights JVM, telemetria dell&#39;infrastruttura e diagnostica avanzata per un&#39;analisi più rapida delle root cause. Basato sulle moderne tecnologie di osservabilità, fornisce un monitoraggio scalabile e sicuro in ambienti aziendali complessi. [!DNL Synoptryx] offre funzioni di conservazione dei dati estese, dashboard avanzati e analisi intelligenti per supportare l&#39;eccellenza operativa. L&#39;esperienza di accesso senza soluzione di continuità con [!DNL Adobe IMS] garantisce accesso sicuro e governance. La piattaforma è progettata per migliorare l&#39;affidabilità del servizio, accelerare la risoluzione dei problemi e migliorare l&#39;esperienza del cliente. Come soluzione di osservabilità strategica di Adobe, [!DNL Synoptryx] fornisce una base pronta per il futuro per il monitoraggio, l&#39;automazione e le informazioni operative negli ambienti dei servizi gestiti.

[!DNL Synoptryx] è incluso in Adobe [!DNL Experience Manager] Managed Services. Non è richiesta alcuna piattaforma o licenza di monitoraggio separata. Adobe monitora la disponibilità e le prestazioni dell&#39;ambiente come parte dell&#39;offerta standard e [!DNL Synoptryx] è la piattaforma dedicata che il team può utilizzare per comprendere le prestazioni dell&#39;applicazione Adobe [!DNL Experience Manager] (AEM) e dell&#39;infrastruttura di supporto.

In questa guida vengono illustrati gli elementi monitorati, la configurazione dell&#39;account [!DNL Synoptryx] e la navigazione nelle dashboard utilizzate per l&#39;analisi quotidiana e la risoluzione dei problemi.

## Panoramica {#at-a-glance}

Come parte di AEM Managed Services, riceverai:

- **Account [!DNL Synoptryx] dedicato**: fornito e supervisionato da Adobe Managed Services, con accesso in sola lettura per il team.
- **Monitoraggio delle transazioni AEM approfondite**: l&#39;agente APM [!DNL Synoptryx] traccia le transazioni significative fino alle chiamate ai metodi (inclusi i numeri di riga), alle dipendenze esterne e alle operazioni dell&#39;archivio.
- **Visualizzazione unificata dell&#39;infrastruttura e dell&#39;applicazione**: combinazione di metriche APM e a livello di host per ottimizzare le prestazioni in modo olistico.

## Cosa monitora Adobe con [!DNL Synoptryx] {#what-we-monitor}

Adobe monitora i livelli di AEM **Author** e **Publish** con il plug-in Java [!DNL Synoptryx] APM. Tutti i server ospitati nella topologia vengono monitorati con l&#39;agente dell&#39;infrastruttura [!DNL Synoptryx]. Il monitoraggio personalizzato di APM e infrastruttura è abilitato sia negli ambienti Managed Services di produzione che in quelli non di produzione.

![Diagramma che mostra il monitoraggio di Synoptryx APM e dell&#39;infrastruttura nei server AEM Author, Publish e in hosting](assets/image6.png)

### Applicazioni nel tuo account {#applications-in-your-account}

L&#39;account [!DNL Synoptryx] è collegato a un singolo account principale Adobe e può ricevere dati da più applicazioni, tra cui:

- Un&#39;applicazione APM per il livello **Author** per ogni ambiente AEM Managed Services
- Un&#39;applicazione APM per il livello **Pubblica** per ogni ambiente AEM Managed Services

Ogni applicazione dispone di una propria chiave di licenza. Tutte le topologie nel contratto Managed Services vengono riportate in un unico account [!DNL Synoptryx]. Le metriche e gli eventi di APM e Infrastructure vengono conservati per un massimo di **30 giorni**.

## Accesso e account {#access}

I dati di monitoraggio sono consolidati in un account [!DNL Synoptryx] gestito e predisposto da Adobe. Il tuo team riceve **l&#39;accesso completo in sola lettura** a tutte le metriche di APM e infrastruttura raccolte dagli agenti. Adobe Managed Services mantiene la proprietà e il controllo amministrativo dell’account.

>[!NOTE]
>
> **Per accedere a**, l&#39;accesso a [!DNL Synoptryx] richiede il provisioning di [!DNL Adobe IMS]. Il Customer Success Engineer (CSE) può effettuare il provisioning e gestire l’accesso degli utenti per la tua organizzazione.

Dopo che il CSE ha eseguito il provisioning dell&#39;account, puoi accedere a [synoptryx.adobecqms.net](https://synoptryx.adobecqms.net).

## Passaggio successivo {#whats-next}

Continua con le dashboard di monitoraggio utilizzate quotidianamente dal tuo team:

- [Monitoraggio delle prestazioni delle applicazioni (APM)](application-performance-monitoring.md): traccia le transazioni AEM, analizza il comportamento di JVM e controlla i servizi esterni.
- [Monitoraggio dell&#39;infrastruttura](infrastructure-monitoring.md): verifica delle metriche di sistema, rete, processo e archiviazione a livello di host.

