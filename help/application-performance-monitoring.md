---
title: Monitoraggio delle prestazioni delle applicazioni (APM) con  [!DNL Synoptryx]
description: Utilizza il plug-in APM  [!DNL Synoptryx]  per tracciare le transazioni AEM, monitorare la JVM, analizzare le transazioni e controllare le tracce delle transazioni e i servizi esterni in AEM Managed Services.
feature: Operations
role: Admin
source-git-commit: 12876ba185fd6d155f02639fba9601a3616c7e90
workflow-type: tm+mt
source-wordcount: '1078'
ht-degree: 5%

---


# Monitoraggio delle prestazioni delle applicazioni (APM) con [!DNL Synoptryx] {#application-performance-monitoring}

Il monitoraggio delle prestazioni delle applicazioni (APM) di [!DNL Synoptryx] fornisce dati cronologici e in tempo reale di insight nelle prestazioni di Adobe [!DNL Experience Manager] (AEM) e nell&#39;esperienza dell&#39;utente finale. Traccia delle transazioni, grafici e report end-to-end forniscono visibilità sul comportamento dell&#39;applicazione fino al livello di codice Java.

## Plug-in APM di Managed Services [!DNL Synoptryx] {#apm-plugin}

AEM funziona come applicazione Java su Jetty con moduli OSGi Apache Felix, basati su Apache Sling e Jackrabbit Oak. Adobe Managed Services, AEM Engineering e [!DNL Synoptryx] Engineering hanno sviluppato congiuntamente strumenti personalizzati per gli ambienti Managed Services.

Tale strumentazione raccoglie:

- **Denominazione significativa delle transazioni**. Le estensioni Sling allineano i nomi delle transazioni alla struttura della pagina e aggiungono un attributo `requestURL` agli eventi Insights in modo da poter correlare gli URL Sling tra dashboard diversi.

![Visualizzazione traccia APM Synoptryx con un nome di transazione AEM descrittivo con una route di verifica stato Sling e una sequenza temporale di estensione](assets/image19a.png)

- **Strumentazione JCR**: le operazioni a livello di repository (inclusi XPath e JCR-SQL2) sono categorizzate e collegate alle tracce delle transazioni nella sezione database di APM.

![Visualizzazione traccia APM Synoptryx che mostra gli intervalli dei componenti nidificati di AEM e la sequenza temporale di esecuzione per una richiesta di pagina](assets/image19.png)

## Utilizzo di [!DNL Synoptryx] APM {#using-apm}

Utilizzare APM per individuare i problemi dell&#39;applicazione prima che interessino gli utenti finali. Autore e Pubblicazione condividono una base di codice ma sono monitorati come **applicazioni APM separate** in modo da poter analizzare ogni livello in modo indipendente.

Ogni ambiente Managed Services include:

- Un’applicazione APM per l’authoring
- Un&#39;applicazione APM per la pubblicazione

Selezionare il nome di un&#39;applicazione nell&#39;APM [!DNL Synoptryx] per aprire la relativa dashboard di panoramica e monitoraggio.

![Elenco di applicazioni Synoptryx APM che mostra le applicazioni Author e Publish](assets/image1a.png)

## Sezioni del dashboard

Il dashboard Gestione prestazioni applicazioni contiene le sezioni seguenti:

- Panoramica
- Metriche RED (Frequenza · Errori · Durata)
- Traffico
- Latenza e prestazioni
- Dettagli errore
- Prime transazioni
- Integrità JVM
- Memoria JVM
- Raccolta oggetti inattivi

Solo le sezioni mostrate di seguito sono documentate in questa guida.

## Navigazione dashboard

![Navigazione dashboard](assets/apm/1_opening_screen.png)

Il dashboard è organizzato in sezioni espandibili che raggruppano le metriche delle prestazioni delle applicazioni correlate. Quando si espande una sezione vengono visualizzati uno o più grafici associati a tale categoria.

## Panoramica

![Panoramica](assets/apm/1.1_apm_overview.png)

### Descrizione

La sezione **[!UICONTROL Panoramica]** presenta indicatori prestazioni chiave (KPI) di alto livello che riepilogano lo stato corrente dell&#39;applicazione monitorata.

Questi KPI forniscono un riepilogo immediato dell’attività dell’applicazione, della velocità effettiva, del successo delle richieste e dell’esperienza utente complessiva.

### Metrica

#### Richieste totali

Visualizza il numero totale di richieste elaborate dall&#39;applicazione durante l&#39;intervallo di tempo selezionato.

**Metrica**

```
total_requests
```

**Unità**

- Conteggio

#### Throughput corrente

Visualizza la velocità di elaborazione della richiesta corrente.

**Metrica**

```
throughput
```

**Unità**

- Richieste al secondo (richieste/e)

#### Tasso di errore corrente

Visualizza la percentuale di richieste che generano errori.

**Metrica**

```
error_rate
```

**Unità**

- Percentuale (%)

#### Punteggio APDEX

Visualizza l&#39;indice delle prestazioni dell&#39;applicazione (APDEX), una misurazione standardizzata della soddisfazione dell&#39;utente finale basata sui tempi di risposta dell&#39;applicazione.

La soglia configurata viene visualizzata all’interno del widget.

**Metrica**

```
apdex_score
```

**Unità**

- Punteggio (0,0 - 1,0)

## Metriche rosse

La metodologia RED misura tre caratteristiche principali di un&#39;applicazione:

- **Tariffa**
- **Errori**
- **Durata**

### Frequenza richieste

![Frequenza richieste](assets/apm/2_red_metrics_request_rate.png)

#### Descrizione

Visualizza il numero di richieste di applicazione ricevute nel tempo.

Questo grafico rappresenta il throughput delle richieste utilizzando una visualizzazione di serie temporali.

#### Metrica

```
req_min
```

#### Unità

- Richieste al minuto (richiesta/m)

#### Informazioni visualizzate

- Percentuale di richieste di serie temporali
- Attività di richiesta storica
- Tendenza del tasso di richieste
- Legenda della metrica

### Frequenza errori

![Frequenza errori](assets/apm/3_error_rate.png)

#### Descrizione

Visualizza la percentuale di richieste che hanno generato errori.

Il grafico confronta le percentuali di errore storiche e correnti.

#### Metrica

```
error_pct (now)
error_pct (1h ago)
```

#### Unità

- Percentuale (%)

#### Informazioni visualizzate

- Percentuale di errore corrente
- Confronto storico
- Valori medi
- Tendenza delle serie temporali

### Durata richiesta

![Durata richiesta](assets/apm/4_request_duration_p50_p95.png)

#### Descrizione

Visualizza la latenza della richiesta in più percentili del tempo di risposta.

Il grafico traccia simultaneamente le misurazioni della latenza percentile raccolte durante il periodo di osservazione selezionato.

#### Metrica

```
P50
P75
P90
```

#### Unità

- Millisecondi (ms)
- Secondi (s)

Le unità vengono ridimensionate automaticamente in base alla durata della risposta.

#### Statistiche visualizzate

Per ogni percentile:

- Media
- Ultim*
- Massimo

#### Definizioni percentuali

| Metrica | Descrizione |
| ------ | ----------------------------- |
| P50 | Tempo di risposta del 50° percentile |
| P75 | Tempo di risposta del 75° percentile |
| P90 | Tempo di risposta del 90° percentile |

## Traffico

### Richieste per codice di stato HTTP

![Richieste per codice di stato](assets/apm/5_requests_by_status_code.png)

#### Descrizione

Visualizza la velocità effettiva delle richieste raggruppata per codice di stato della risposta HTTP.

Ogni codice di stato viene tracciato in modo indipendente nel tempo.

#### Metrica

Le metriche comuni includono:

```
req_s 200
req_s 300
req_s 400
req_s 500
```

a seconda dell’attività dell’applicazione.

#### Unità

- Richieste al secondo (richieste/e)

#### Informazioni visualizzate

- Throughput per stato HTTP
- Velocità effettiva media
- Velocità effettiva più recente
- Throughput massimo
- Attività di serie temporali

### Frequenza richieste per endpoint

![Frequenza richieste per endpoint](assets/apm/6_request_rate_by_end_point.png)

#### Descrizione

Visualizza gli endpoint dell&#39;applicazione con traffico più alto classificati in base alla frequenza di richieste.

Ogni endpoint viene visualizzato come una barra orizzontale che rappresenta il volume della richiesta.

#### Metrica

```
endpoint_request_rate
```

#### Unità

- Richieste al minuto (richiesta/m)

#### Informazioni visualizzate

- Percorso endpoint
- Percentuale richieste
- Elenco endpoint classificati
- Volume di richiesta relativo

## Latenza e prestazioni

### Tempo di risposta: P95 rispetto a 1 ora

![Tempo di risposta P95](assets/apm/7_response_time_p95_1h.png)

#### Descrizione

Visualizza un confronto tra il tempo di risposta P95 corrente e il tempo di risposta P95 registrato un&#39;ora prima.

Entrambi i set di dati vengono visualizzati sullo stesso grafico delle serie temporali.

#### Metrica

```
P95 (Current)
P95 (1 Hour Ago)
```

#### Unità

- Millisecondi (ms)
- Secondi (s)

#### Statistiche visualizzate

- Media
- Ultim*
- Massimo

### Punteggio APDEX nel tempo

![APDEX](assets/apm/8_apdex_score_overtime.png)

#### Descrizione

Visualizza l&#39;Indice prestazioni applicazione come serie temporale continua.

Il grafico visualizza i valori APDEX per l&#39;intero intervallo di monitoraggio selezionato.

#### Metrica

```
APDEX Score
```

#### Unità

- Punteggio (0,0-1,0)

#### Statistiche visualizzate

- Media
- Ultim*
- Massimo

### Throughput e latenza P95

![Velocità effettiva e latenza](assets/apm/9_throughput_vs_p95latency.png)

#### Descrizione

Visualizza la velocità effettiva delle richieste e la latenza di risposta P95 sulla stessa timeline.

Il grafico consente la visualizzazione simultanea del volume di traffico e della latenza di risposta.

#### Metrica

```
Throughput
P95 Latency
```

#### Unità

| Metrica | Unità |
| ----------- | ------------ |
| Velocità effettiva | Richieste/sec |
| Latenza P95 | millisecondi |

#### Informazioni visualizzate

- Throughput delle serie temporali
- Latenza serie temporale
- Confronto delle metriche doppie

## Dettagli errore

### Percentuale di errori per gruppo di stati

![Frequenza errori per gruppo di stati](assets/apm/10_error_rate_pct_by_status_group.png)

#### Descrizione

Visualizza le percentuali di errore dell&#39;applicazione raggruppate per classe di risposta HTTP.

Vengono tracciate serie separate per ogni categoria di risposta.

#### Metrica

I gruppi comuni includono:

```
2xx
3xx
4xx
5xx
Combined Error Trend
```

a seconda del traffico osservato.

#### Unità

- Percentuale (%)

#### Informazioni visualizzate

- Percentuale di errori per classe di risposta
- Percentuale di errore media
- Tendenza delle serie temporali


### Tendenza rapporto errori — Ora rispetto a 1 ora fa

![Rapporto errori 1 ora](assets/apm/11_error_ratio_trend_1h.png)

#### Descrizione

Visualizza il rapporto di errore corrente dell&#39;applicazione insieme al rapporto di errore registrato un&#39;ora prima.

#### Metrica

```
Current Error Ratio
1 Hour Error Ratio
```

#### Unità

- Percentuale (%)

#### Informazioni visualizzate

- Tendenza attuale
- Confronto storico
- Visualizzazione serie temporali

### Tendenza rapporto errori — Ora rispetto a 6 ore fa

![Rapporto errori 6 ore](assets/apm/12_error_ratio_trend_6h.png)

#### Descrizione

Visualizza il rapporto di errore corrente dell&#39;applicazione insieme al rapporto di errore registrato sei ore prima.

#### Metrica

```
Current Error Ratio
6 Hour Error Ratio
```

#### Unità

- Percentuale (%)

#### Informazioni visualizzate

- Proporzione di errori corrente
- Confronto storico
- Visualizzazione serie temporali

## Riepilogo delle metriche del dashboard

| Dashboard | Metriche primarie |
| -------------------------- | --------------------------------------------- |
| Panoramica | Totale richieste, Throughput, Frequenza errori, APDEX |
| Frequenza richieste | Richieste al minuto |
| Frequenza errori | Percentuale errori |
| Durata richiesta | Latenza P50, P75, P90 |
| Richieste per codice di stato | Throughput stato HTTP |
| Frequenza richieste per endpoint | Volume richiesta endpoint |
| Confronto dei tempi di risposta | P95 attuale e storico |
| Punteggio APDEX | Indice di soddisfazione utente |
| Throughput e latenza | Throughput richieste e latenza P95 |
| Tasso di errore per gruppo di stati | Percentuale errori gruppo di stato HTTP |
| Tendenze proporzioni errori | Rapporto di errori corrente e storico |
