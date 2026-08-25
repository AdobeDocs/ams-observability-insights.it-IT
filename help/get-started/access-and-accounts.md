---
title: Gestione di accessi e account
description: Scopri come viene eseguito il provisioning degli account Observability Insights, chi gestisce l’accesso e quale livello di controllo dispongono i team dei clienti.
feature: Operations
role: Admin
source-git-commit: 6526a90a017147ac3483c0b2b626b9aa903819ba
workflow-type: tm+mt
source-wordcount: '232'
ht-degree: 0%

---


# Gestione di accessi e account {#access-and-account-management}

Adobe fornisce e gestisce l’account Observability Insights per la tua organizzazione AEM Managed Services. I team del cliente utilizzano Adobe IMS per accedere e ricevere visibilità in sola lettura sui dati monitorati.

## Modello di proprietà dell’account {#account-ownership-model}

- Adobe Managed Services possiede l’account Observability Insights.
- Ai team del cliente viene concesso l’accesso in sola lettura.
- Le modifiche amministrative, il provisioning e gli aggiornamenti degli accessi vengono gestiti tramite Adobe.

## Modalità di accesso degli utenti {#how-users-get-access}

L’accesso a Observability Insights richiede il provisioning di Adobe IMS.

Per richiedere o aggiornare l&#39;accesso:

1. Contatta il tuo Customer Success Engineer (CSE).
2. Fornisci i dettagli utente necessari per il provisioning di Adobe IMS.
3. Verificare che siano stati assegnati l&#39;organizzazione e l&#39;ambito di accesso corretti.

Al termine del provisioning, accedi a [insights.adobecqms.net](https://insights.adobecqms.net).

## Cosa possono fare gli utenti {#what-users-can-do}

In genere, gli utenti del cliente possono:

- Visualizza dashboard APM
- Visualizzare i dashboard dell’infrastruttura
- Ispezionare le metriche host e dell&#39;applicazione monitorate
- Partecipare alle indagini utilizzando dashboard e tracce condivise

## Cosa non possono fare gli utenti {#what-users-cannot-do}

Gli utenti del cliente devono supporre che il controllo amministrativo rimanga su Adobe Managed Services, a meno che Adobe non documenti esplicitamente il contrario.

Esempi comuni includono:

- Gestione della proprietà dell’account
- Modifica del provisioning a livello di piattaforma
- Modifica del comportamento della strumentazione gestita

## Informazioni da aggiungere in un secondo momento {#information-to-add-later}

Utilizzare questa sezione quando diventano disponibili dettagli di processo completi:

- Prerequisiti per Adobe IMS
- Tempi di risposta previsti per il provisioning
- Contatti e percorsi di riassegnazione
- Gestione del ciclo di vita degli utenti per utenti entranti e uscenti
