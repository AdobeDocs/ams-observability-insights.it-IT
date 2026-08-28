---
title: Monitora il tuo ambiente AEM Managed Services con Observability Insights
description: Inizia qui per capire cosa copre Observability Insights in AEM Managed Services, a chi serve e come navigare nel resto di questa guida.
feature: Operations
role: Admin
source-git-commit: fc38d43e53a366fb16151f3bd105b561f55fcbfa
workflow-type: tm+mt
source-wordcount: '741'
ht-degree: 0%

---


# Monitora il tuo ambiente AEM Managed Services con Observability Insights {#observability-insights-monitoring}

**Observability Insights** fornisce visibilità sulle prestazioni delle applicazioni, sullo stato dell&#39;infrastruttura e sul comportamento dei servizi in AEM Managed Services, senza richiedere una piattaforma di monitoraggio separata.

Se sei responsabile dell&#39;affidabilità dei servizi, della risposta agli incidenti o dell&#39;analisi delle prestazioni, **Observability Insights** ti aiuta a passare rapidamente dai sintomi alle prove. Combina la telemetria delle applicazioni e i segnali di integrità a livello di host in modo che i team dei clienti e Adobe possano analizzare i problemi da una vista operativa condivisa.

## White paper Observability Insights

[Scarica il white paper Observability Insights](v2-assets/Observability_Insights_Overview.pdf)

## Perché i team utilizzano Observability Insights? {#why-teams-use-observability-insights}

Utilizza Observability Insights per rispondere a domande operative quali:

- Il problema riguarda l’authoring, la pubblicazione o entrambi?
- Il problema è causato dal comportamento dell&#39;applicazione, dalla pressione delle risorse host o da una combinazione di entrambi?
- Quali transazioni, endpoint o gruppi di stato spiegano il picco di errori o latenza?
- Il problema è isolato in un ambiente o visibile in una topologia più ampia?

Observability Insights è progettato per l’analisi operativa dei comportamenti recenti. Consente di identificare cosa è cambiato, dove è cambiato e quali segnali sono più rilevanti prima dell’escalation o dell’azione correttiva.

## Cosa ti aiuta a fare Observability Insights? {#what-observability-insights-helps-you-do}

Utilizza Observability Insights per:

- Comprendere il comportamento dei livelli Author e Publish con traffico reale.
- Correlazione tra latenza dell&#39;applicazione, tassi di errore e integrità JVM con i segnali a livello di host.
- Conferma se un problema è isolato in un ambiente, un livello o un host.
- Offri ad Adobe Managed Services e ai tuoi team interni una visualizzazione operativa condivisa durante le indagini.

Observability Insights è incluso in AEM Managed Services. Adobe fornisce e gestisce l’account, strumenta gli ambienti supportati ed espone i dashboard risultanti al team come strumenti operativi di sola lettura.

Poiché Adobe gestisce la configurazione e la strumentazione della piattaforma, è possibile concentrarsi sull’analisi e l’interpretazione anziché sulla distribuzione degli agenti, sull’amministrazione dell’account o sull’assembly del dashboard.

## Panoramica {#at-a-glance}

Come parte di AEM Managed Services, riceverai:

- **Account Observability Insights dedicato** - Eseguito e supervisionato da Adobe Managed Services, con accesso in sola lettura per il team.
- **Monitoraggio approfondito delle transazioni di AEM**: l&#39;agente APM Observability Insights traccia le transazioni significative fino alle chiamate di metodo (inclusi i numeri di riga), alle dipendenze esterne e alle operazioni dell&#39;archivio.
- **Visualizzazione applicazioni e host unificati**: combinare applicazioni e metriche a livello di host per ottimizzare le prestazioni in modo olistico.

## Per chi è questa documentazione {#who-this-documentation-is-for}

Questa documentazione è progettata principalmente per:

- Amministratori AEM Managed Services che necessitano di visibilità negli ambienti monitorati
- Operazioni e team di supporto che gestiscono incidenti, analisi delle tendenze e revisione dei servizi
- Team di progettazione clienti che collaborano con Adobe Managed Services durante le indagini
- Parti interessate che devono comprendere la portata del monitoraggio e le responsabilità operative

## Cosa monitora Adobe con Observability Insights {#what-we-monitor}

Adobe monitora i livelli di AEM **Author** e **Publish** con il plug-in Java APM Observability Insights. Tutti i server ospitati nella topologia vengono monitorati con l’agente dell’infrastruttura Observability Insights. Il monitoraggio personalizzato di APM e infrastruttura è abilitato sia negli ambienti Managed Services di produzione che in quelli non di produzione.

![Diagramma che mostra gli Approfondimenti sull&#39;osservabilità APM e il monitoraggio dell&#39;infrastruttura nei server AEM Author, Publish e in hosting](v2-assets/login-screen.png)

### Applicazioni nel tuo account {#applications-in-your-account}

L’account Observability Insights è collegato a un singolo account principale Adobe e può ricevere dati da più applicazioni, tra cui:

- Un&#39;applicazione APM per il livello **Author** per ogni ambiente AEM Managed Services
- Un&#39;applicazione APM per il livello **Pubblica** per ogni ambiente AEM Managed Services

Ogni applicazione dispone di una propria chiave di licenza. Tutte le topologie nel tuo contratto Managed Services vengono riportate in un unico account Observability Insights. Le metriche e gli eventi di APM e Infrastructure vengono conservati per un massimo di **30 giorni**.

## Accedi al tuo account {#access}

I dati di monitoraggio sono consolidati in un account Observability Insights che Adobe fornisce e gestisce. Gli utenti del cliente ricevono **accesso in sola lettura** ai dati di APM e infrastruttura raccolti dagli agenti. Adobe Managed Services mantiene la proprietà dell’account e il controllo amministrativo.

### Prerequisiti {#access-prerequisites}

Prima di accedere, verifica quanto segue:

- La tua organizzazione dispone di un abbonamento a **AEM Managed Services** attivo. Observability Insights è incluso senza costi aggiuntivi.
- Il tuo Customer Success Engineer (CSE) ha effettuato il provisioning del tuo account Adobe IMS e ti ha concesso l’accesso all’account Observability Insights per la tua organizzazione.

>[!NOTE]
>
> **Per accedere a:** l&#39;accesso a Observability Insights richiede il provisioning di Adobe IMS. Contatta il tuo Customer Success Engineer (CSE) per effettuare il provisioning e gestire l’accesso degli utenti per la tua organizzazione.

Dopo che il CSE ha eseguito il provisioning dell&#39;account, accedi a [insights.adobecqms.net](https://insights.adobecqms.net). Questo URL è lo stesso per tutti i clienti AEM Managed Services; gli ambienti e le dashboard della tua organizzazione hanno l’ambito del tuo account con provisioning.
