---
title: Riferimento dashboard APM
description: Riferimento pannello per pannello per dashboard di Observability Insights APM, incluse schermate, metriche e unità.
feature: Operations
role: Admin
source-git-commit: 1d54a6a398360b040221db5b2780d301722894bf
workflow-type: tm+mt
source-wordcount: '806'
ht-degree: 7%

---


# Riferimento dashboard APM {#apm-dashboard-reference}

Questa documentazione documenta i principali pannelli APM di Observability Insights utilizzati in AEM Managed Services.

## Navigazione dashboard

![Navigazione dashboard](../assets/apm/1_opening_screen.png)

Il dashboard è organizzato in sezioni espandibili che raggruppano le metriche delle prestazioni delle applicazioni correlate. Quando si espande una sezione vengono visualizzati uno o più grafici associati a tale categoria.

## Panoramica

![Panoramica](../assets/apm/1.1_apm_overview.png)

### Descrizione

La sezione **Panoramica** presenta indicatori prestazioni chiave (KPI) di alto livello che riepilogano lo stato corrente dell&#39;applicazione monitorata.

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

![Frequenza richieste](../assets/apm/2_red_metrics_request_rate.png)

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

![Frequenza errori](../assets/apm/3_error_rate.png)

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

![Durata richiesta](../assets/apm/4_request_duration_p50_p95.png)

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

![Richieste per codice di stato](../assets/apm/5_requests_by_status_code.png)

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

![Frequenza richieste per endpoint](../assets/apm/6_request_rate_by_end_point.png)

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

![Tempo di risposta P95](../assets/apm/7_response_time_p95_1h.png)

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

![APDEX](../assets/apm/8_apdex_score_overtime.png)

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

![Velocità effettiva e latenza](../assets/apm/9_throughput_vs_p95latency.png)

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

![Frequenza errori per gruppo di stati](../assets/apm/10_error_rate_pct_by_status_group.png)

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

![Rapporto errori 1 ora](../assets/apm/11_error_ratio_trend_1h.png)

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

![Rapporto errori 6 ore](../assets/apm/12_error_ratio_trend_6h.png)

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
