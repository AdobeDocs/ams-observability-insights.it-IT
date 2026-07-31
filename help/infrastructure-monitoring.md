---
title: Monitoraggio dell'infrastruttura con  [!DNL Synoptryx]
description: Utilizza  [!DNL Synoptryx] Monitoraggio dell'infrastruttura per esaminare le metriche di sistema, rete, processo e archiviazione a livello di host per l'ingombro di AEM Managed Services.
feature: Operations
role: Admin
source-git-commit: 12876ba185fd6d155f02639fba9601a3616c7e90
workflow-type: tm+mt
source-wordcount: '1107'
ht-degree: 7%

---


# Dashboard di monitoraggio dell&#39;infrastruttura host

Questa sezione descrive ogni grafico di monitoraggio dell&#39;infrastruttura a livello di host visualizzato sul dashboard di monitoraggio dell&#39;infrastruttura. Ogni sezione spiega lo scopo della metrica, i dati raccolti, le unità di misura e le informazioni presentate nella visualizzazione.

## Panoramica del dashboard

Il dashboard di monitoraggio dell&#39;infrastruttura host fornisce visibilità in tempo reale sull&#39;utilizzo e sulle prestazioni dell&#39;host sottostante. Queste metriche aiutano gli operatori a monitorare le risorse di elaborazione, memoria, storage e rete, identificando al contempo i potenziali colli di bottiglia delle risorse.

Il dashboard include i seguenti pannelli di monitoraggio:

- Utilizzo di Host CPU
- I/O disco host
- I/O di rete host
- Attesa I/O CPU
- Utilizzo archiviazione
- Utilizzo disco
- Media carico CPU host
- Utilizzo memoria host

## &#x200B;1. Utilizzo di Host CPU

![Utilizzo CPU host](assets/host-monitoring/host_cpu_utilization.png)

### Descrizione

Nel pannello **[!UICONTROL Utilizzo CPU host]** viene visualizzata la percentuale di risorse CPU attualmente utilizzate dal sistema operativo e da tutti i processi in esecuzione nel tempo.

Questa metrica rappresenta l&#39;utilizzo complessivo di CPU nell&#39;host e fornisce una visualizzazione della serie temporale dell&#39;attività del processore.

Il grafico consente agli operatori di monitorare il cambiamento del consumo di CPU durante la finestra di osservazione selezionata.

### Metrica

| Metrica | Descrizione |
| --------- | ---------------------------------------- |
| `cpu_pct` | Percentuale del CPU totale attualmente in uso |

### Unità

- Percentuale (%)

### Statistiche visualizzate

Il pannello riepiloga l’utilizzo di CPU utilizzando tre valori:

| Statistica | Descrizione |
| --------- | --------------------------------------------------------------- |
| Media | Utilizzo medio di CPU durante l&#39;intervallo di tempo selezionato |
| Ultim* | Valore di utilizzo CPU più recente raccolto |
| Max | Massimo utilizzo del CPU osservato durante l&#39;intervallo di tempo selezionato |

### Componenti del grafico

- Linea di serie temporali che rappresenta l’utilizzo di CPU.
- Asse Y basato su percentuale compreso tra **0% e 100%**.
- Statistiche di riepilogo visualizzate sotto il grafico.
- Tendenza cronologica nell&#39;intervallo di monitoraggio selezionato.

## &#x200B;2. I/O disco host

![I/O disco host](assets/host-monitoring/host_disk_io.png)

### Descrizione

Il pannello **[!UICONTROL I/O disco host]** visualizza la velocità effettiva di archiviazione per le operazioni di lettura e scrittura su disco eseguite dall&#39;host.

Il grafico presenta due serie temporali indipendenti che rappresentano i dati trasferiti tra il sistema operativo e i dispositivi di storage.

Questa visualizzazione aiuta a monitorare l’attività di archiviazione nel tempo e fornisce ad insight il volume di dati letti e scritti dai dischi.

### Metrica

| Metrica | Descrizione |
| ------------ | --------------------------------- |
| `disk_read` | Quantità di dati letti dall’archiviazione |
| `disk_write` | Quantità di dati scritti nell&#39;archiviazione |

Internamente, queste metriche vengono visualizzate utilizzando valori di velocità effettiva arrotondati.

### Unità

- Byte al secondo (B/s)
- Kilobyte al secondo (KB/s)
- Megabyte al secondo (MB/s)
- Gigabyte al secondo (GB/s)

L&#39;unità visualizzata viene ridimensionata automaticamente in base alla velocità effettiva.

### Componenti del grafico

- Linea verde che rappresenta la velocità effettiva di lettura del disco.
- Linea arancione che rappresenta il throughput di scrittura su disco.
- Visualizzazione di serie temporali.
- Legenda separata per ogni metrica.
- I valori delle metriche correnti vengono visualizzati accanto a ciascuna serie.

## &#x200B;3. I/O di rete host

![I/O rete host](assets/host-monitoring/host_network_io.png)

### Descrizione

Il pannello **[!UICONTROL I/O di rete host]** visualizza il volume di traffico di rete trasmesso e ricevuto dall&#39;host nel tempo.

Il grafico misura la velocità con cui i dati scorrono attraverso le interfacce di rete e fornisce visibilità sul consumo della larghezza di banda della rete.
Questa metrica rappresenta il throughput di rete aggregato.

### Metrica

| Metrica | Descrizione |
| --------------- | --------------------------------------------------------------------- |
| `bytes_per_sec` | Throughput di rete aggregato misurato in byte trasferiti al secondo |

### Unità

Il grafico viene automaticamente ridimensionato tra:

- Byte/sec
- KB/sec
- MB/sec
- GB/sec

a seconda del volume di traffico osservato.

### Statistiche visualizzate

| Statistica | Descrizione |
| --------- | ---------------------------------- |
| Media | Throughput di rete medio |
| Ultim* | Misurazione della velocità effettiva più recente |
| Max | Throughput massimo osservato |

### Componenti del grafico

- Singola linea di trasmissione.
- Visualizzazione di serie temporali.
- Scalabilità dinamica della larghezza di banda.
- Statistiche di riepilogo visualizzate sotto il grafico.

## &#x200B;4. Attesa I/O CPU

![Attesa I/O CPU](assets/host-monitoring/cpu_io_wait.png)

### Descrizione

Nel pannello **[!UICONTROL Attesa I/O di CPU]** viene visualizzata la percentuale di tempo di CPU impiegato per l&#39;attesa del completamento delle operazioni di input/output.

Questa metrica rappresenta il tempo di inattività del processore che si verifica perché i processi attivi vengono bloccati durante l&#39;attesa di dispositivi di storage o altre operazioni di I/O.

Il grafico visualizza il modo in cui l’attesa di I/O cambia nel tempo.

### Metrica

| Metrica | Descrizione |
| --------- | ------------------------------------------------------ |
| `cpu_pct` | Percentuale di tempo di attesa CPU per le operazioni di I/O |

### Unità

- Percentuale (%)

### Statistiche visualizzate

| Statistica | Descrizione |
| --------- | ------------------------------- |
| Media | Percentuale media di attesa I/O CPU |
| Ultim* | Valore registrato più di recente |
| Max | Valore più alto registrato |

### Componenti del grafico

- Linea di serie temporali.
- Asse Y basato sulla percentuale.
- Statistiche di riepilogo.
- Visualizzazione delle tendenze storiche.

## &#x200B;5. Utilizzo archiviazione

![Utilizzo archiviazione](assets/host-monitoring/storage_disk_usage.png)

### Descrizione

Il pannello **[!UICONTROL Utilizzo archiviazione]** visualizza la percentuale complessiva di capacità di archiviazione attualmente utilizzata nell&#39;host monitorato.

Il grafico fornisce una visualizzazione cronologica dell&#39;utilizzo della capacità del file system durante l&#39;intervallo di tempo selezionato.

### Metrica

| Metrica | Descrizione |
| --------------- | -------------------------------------------------- |
| Utilizzo archiviazione % | Percentuale di spazio di archiviazione allocato attualmente utilizzato |

### Unità

- Percentuale (%)

### Componenti del grafico

- Grafico di utilizzo delle serie temporali.
- Scala percentuale.
- Tendenza cronologica dell&#39;utilizzo dello storage.

## &#x200B;6. Utilizzo disco

![Utilizzo disco](assets/host-monitoring/storage_disk_usage.png)

### Descrizione

Nel pannello **[!UICONTROL Utilizzo disco]** viene visualizzato l&#39;utilizzo dello spazio di archiviazione per ogni file system o dispositivo di archiviazione montato.

Ogni riga corrisponde a un dispositivo a blocchi o a una partizione montata specifica e riporta la percentuale di spazio attualmente in uso.

Questa tabella fornisce una suddivisione dell&#39;utilizzo dello storage a livello di file system.

### Informazioni visualizzate

Ogni voce include:

| Campo | Descrizione |
| --------------- | -------------------------------------------- |
| Dispositivo | Dispositivo di storage o file system montato |
| Utilizzato % | Percentuale di capacità di storage utilizzata |
| Barra di utilizzo | Rappresentazione visiva del consumo di storage |

### Unità

- Percentuale (%)

### Componenti del grafico

- Elenco di file system/dispositivi.
- Percentuale di utilizzo.
- Indicatore di capacità codificato a colori.
- Valori di utilizzo ordinati.

## &#x200B;7. Media carico CPU host

![Media caricamento CPU host](assets/host-monitoring/host_cpu_load_average.png)

### Descrizione

Nel pannello **[!UICONTROL Media carico CPU host]** vengono visualizzate le medie del carico del sistema Linux su tre finestre di tempo continue.

A differenza dell&#39;utilizzo di CPU, la media di carico rappresenta il numero medio di processi che sono in esecuzione o in attesa della pianificazione CPU o del completamento di I/O.

Il grafico mostra simultaneamente tre medie mobili che forniscono tendenze del carico di lavoro a breve e lungo termine.

### Metrica

| Metrica | Descrizione |
| ---------- | -------------------------------------------- |
| `load_1m` | Carico di sistema medio nell&#39;ultimo minuto |
| `load_5m` | Carico di sistema medio negli ultimi 5 minuti |
| `load_15m` | Carico medio di sistema negli ultimi 15 minuti |

### Unità

- Media carico (valore senza dimensioni)

### Statistiche visualizzate

Per ogni metrica della media di carico:

| Statistica | Descrizione |
| --------- | --------------------------------------- |
| Media | Carico medio durante l&#39;intervallo di tempo selezionato |
| Ultim* | Ultimo valore di carico registrato |
| Max | Valore di carico più alto osservato |

### Componenti del grafico

- Tre linee di tendenza indipendenti.
- Visualizzazione di serie temporali.
- Legende singole per ogni media mobile.
- Statistiche di riepilogo per ciascuna metrica.

## &#x200B;8. Utilizzo memoria host

![Utilizzo memoria host](assets/host-monitoring/host_memory_usage.png)

### Descrizione

Il pannello **[!UICONTROL Utilizzo memoria host]** visualizza la percentuale di memoria di sistema fisica attualmente allocata dal sistema operativo.

Questa metrica rappresenta l&#39;utilizzo complessivo della RAM in tutti i processi in esecuzione, memoria kernel, buffer e cache.

Il grafico fornisce una visualizzazione continua del consumo di memoria per tutto il periodo di monitoraggio selezionato.

### Metrica

| Metrica | Descrizione |
| ------------ | ---------------------------------------------- |
| `memory_pct` | Percentuale di memoria fisica attualmente in uso |

### Unità

- Percentuale (%)

### Statistiche visualizzate

| Statistica | Descrizione |
| --------- | ---------------------------------- |
| Media | Utilizzo medio della memoria |
| Ultim* | Utilizzo registrato più recentemente |
| Max | Massimo utilizzo osservato |

### Componenti del grafico

- Grafico dell&#39;utilizzo della memoria della serie temporale.
- Asse Y basato sulla percentuale.
- Tendenza utilizzo cronologico.
- Statistiche di riepilogo.

## Riepilogo delle metriche del dashboard

| Pannello Cruscotto | Metrica principale | Unità |
| --------------------- | -------------------------------- | ------------ |
| Utilizzo di Host CPU | `cpu_pct` | % |
| I/O disco host | `disk_read`, `disk_write` | Byte/sec |
| I/O di rete host | `bytes_per_sec` | Byte/sec |
| Attesa I/O CPU | `cpu_pct` | % |
| Utilizzo archiviazione | Utilizzo archiviazione % | % |
| Utilizzo disco | Utilizzo file system | % |
| Media carico CPU host | `load_1m`, `load_5m`, `load_15m` | Media carico |
| Utilizzo memoria host | `memory_pct` | % |
