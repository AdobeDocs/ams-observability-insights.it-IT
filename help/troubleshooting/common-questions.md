---
title: Domande frequenti
description: Domande comuni e punti di partenza per le indagini su Observability Insights in AEM Managed Services.
feature: Operations
role: Admin
source-git-commit: 3e9cd3734665dc06a4b90902b229dffb8f5421df
workflow-type: tm+mt
source-wordcount: '216'
ht-degree: 0%

---


# Domande frequenti {#faq}

Utilizza questa pagina come punto di partenza quando non sai da dove iniziare o hai bisogno di una risposta rapida durante un’indagine attiva.

## Perché non posso accedere a Observability Insights? {#cannot-access-observability-insights}

Inizia con [Gestione degli accessi e degli account](../get-started/access-and-accounts.md). Se il provisioning è incompleto o obsoleto, contatta il Customer Success Engineer (CSE) per richiedere l’accesso o un aggiornamento.

## Perché viene visualizzato &quot;Caricamento autorizzazioni&quot; quando si tenta di accedere? {#loading-permissions-error}

Questo in genere indica un problema con il provisioning degli utenti. Contatta il tuo Customer Success Engineer (CSE), che può lavorare con i team pertinenti per risolvere il problema di accesso.

## Come determinare se un problema è correlato all&#39;applicazione o all&#39;infrastruttura? {#application-or-infrastructure}

Inizia con [Monitoraggio delle prestazioni delle applicazioni](/help/applications.md) per rivedere le percentuali di richieste, le percentuali di errore e la latenza durante l&#39;authoring o la pubblicazione. Se i segnali dell&#39;applicazione sono elevati, utilizzare [Host](/help/hosts.md) per verificare se la pressione delle risorse a livello di host (CPU, memoria, disco o rete) spiega o aggiunge i dati visualizzati.

## Come posso comprendere un grafico o una metrica specifica? {#understand-graph-or-metric}

Utilizza le pagine di riferimento della dashboard per le descrizioni pannello per pannello, i nomi delle metriche, le unità e le schermate:

- [Riferimento dashboard APM](../reference/apm-dashboard-reference.md)
- [Riferimento dashboard infrastruttura](../reference/infrastructure-dashboard-reference.md)

## Quali dati vengono effettivamente raccolti da Observability Insights? {#what-data-is-collected}

Vedere [Copertura, ambienti e conservazione dei dati](../get-started/coverage-and-data.md) per informazioni su ambito di monitoraggio, rappresentazione delle applicazioni, periodi di conservazione e implicazioni operative.
