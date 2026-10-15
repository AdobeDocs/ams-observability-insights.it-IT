---
title: Dizionario dei dati
description: Fai riferimento alle entità e agli attributi disponibili per le query SQL di Observability Insights.
source-git-commit: 91a61d33010192cedce7d40a45ffe86f32e4824b
workflow-type: tm+mt
source-wordcount: '598'
ht-degree: 20%
---

## Dizionario dei dati {#data-dictionary}

Tutte le entità e gli attributi sui quali è possibile eseguire query. Il catalogo live, con le descrizioni, si trova anche nel pannello **Guida query** in Esplora e nella finestra di dialogo Aggiungi widget.

### Entità di traccia

| Entità | Contiene | Ambito fisso | Rollup |
| ------------- | --------------------------------------------------------- | ---------------------------- | ------------------------------------------------------- |
| `Transaction` | Richieste server in entrata | `kind = Server` | 1m / 5m / 1h ([Prestazioni](../operate/performance.md)) |
| `AppRequest` | Richieste consumer di messaggi del server in entrata **e** | `kind IN (Server, Consumer)` | — |
| `Span` | Ogni estensione (server, client, producer, consumer, internal) | — | — |

<details open>
<summary><strong>Attributi di traccia</strong> (identici su tutte e tre le entità)</summary>

| Attributo | Tipo | Descrizione |
| --------------------------------------------------- | ----------- | ----------------------------------------------------------------------------------------------- |
| `timestamp` | timestamp | Ora di inizio estensione |
| `traceId`, `spanId`, `parentSpanId` | stringa | Identificatori (le estensioni radice hanno `parentSpanId = ''`) |
| `name` | stringa | Nome operazione o transazione |
| `kind` | stringa | `Server`, `Client`, `Producer`, `Consumer`, `Internal` |
| `service` | stringa | Nome servizio di emissione |
| `serviceKey` | stringa | Identità di servizio effettiva (fallback a `appName` quando `service` è vuoto o un elemento padre approssimativo) |
| `appName` | stringa | Nome dell’applicazione Observability Insights |
| `appInstance` | stringa | Identità per istanza: prefisso host di authoring/pubblicazione, nome app, servizio |
| `hostTierPrefix` | stringa | Nome host troncato nel suffisso di ruolo `author` / `publish` / `dispatcher` |
| `duration` | numero (ms) | Durata estensione in millisecondi |
| `status` | stringa | `Ok`, `Error`, `Unset` |
| `statusMessage` | stringa | Descrizione stato |
| `host` | stringa | Nome host |
| `environment` | stringa | Ambiente di implementazione |
| `region` | stringa | Area geografica cloud |
| `cloud.provider` | stringa | AWS/Azure/GCP |
| `http.method` | stringa | `GET`, `POST`, ... |
| `http.statusCode` | numero | Stato della risposta HTTP |
| `http.route` | stringa | Modello di percorso normalizzato |
| `url` | stringa | URL completo |
| `urlPath` | stringa | Parte percorso dell&#39;URL (`/content/site/en.html`) |
| `externalDomain` | stringa | Dominio di un URL in uscita (da utilizzare su estensioni client) |
| `peer.service` | stringa | Servizio a valle chiamato |
| `db.system`, `db.operation`, `db.namespace` | stringa | Tecnologia, funzionamento e schema del database |
| `messaging.system`, `messaging.destination` | stringa | Kafka, RabbitMQ, ... e argomento o coda |
| `rpc.system`, `rpc.method` | stringa | gRPC e simili |
| `error.type` | stringa | Tipo di eccezione o errore |
| `sling.script`, `sling.servlet`, `sling.requestUri` | stringa | Dettagli di AEM Sling |
| `jsp.path` | stringa | JSP AEM eseguito |
| `jcr.operation`, `jcr.path` | stringa | Operazione e percorso dell’archivio JCR di AEM |
| `graphql.operation` | stringa | Nome operazione GraphQL |

</details>

### Entità metriche generiche

| Entità | Contiene |
| ------------- | -------------------------------------------------- |
| `MetricGauge` | Campione di misuratore OpenTelemetry point-in-time |
| `MetricSum` | Ogni campione cumulativo o delta della somma di OpenTelemetry |

Utilizzali per **individuazione** o per metriche che non hanno un&#39;entità curata: `WHERE metric = 'jvm.gc.duration'`.

<details open>
<summary><strong>Attributi della metrica</strong> (condivisi da tutte le entità metriche)</summary>

| Attributo | Tipo | Descrizione |
| ----------------------------------------------------------------------- | --------- | ------------------------------------------------------- |
| `timestamp` | timestamp | Tempo di esempio |
| `metric` | stringa | Nome metrica OpenTelemetry |
| `value` | numero | Valore di esempio (convertito su entità curate; vedi di seguito) |
| `unit` | stringa | Unità di metrica |
| `service`, `appName`, `host`, `environment`, `region`, `cloud.provider` | stringa | Identità risorsa |
| `state` | stringa | Modalità CPU o stato della memoria |
| `cpu`, `device`, `direction`, `mountpoint` | stringa | Per CPU, per dispositivo, direzione I/O, file system |
| `pool` | stringa | Pool di memoria JVM o pool di connessioni |
| `topic` | stringa | Argomento Kafka |
| `process.pid`, `process.name`, `process.owner` | stringa | Identità del processo |

</details>

### Entità metriche curate

Ogni entità curata esegue il wrapping di una metrica OpenTelemetry con il filtro e la conversione di unità corretti, pertanto `value` è già significativo. **Le entità contatore** (✔) contengono totali cumulativi. Eseguire una query con `rate(value)` e `TIMESERIES`.

| Entità | `value` significa | Unità | Contatore | Note |
| ------------------------------------------------------------------------------ | ----------------------------------- | --------- | ------- | -------------------------------------------- |
| `CpuUtilization` | CPU occupato | % |         | Rollup di 1 minuto per `average(value)` per host |
| `CpuUserPercent` / `CpuSystemPercent` / `CpuIowaitPercent` / `CpuStealPercent` | Tempo CPU per modalità | % |         |                                              |
| `MemoryUtilization` | Memoria utilizzata | % |         | Rollup di 1 minuto per `average(value)` |
| `LoadAverage1m` / `LoadAverage5m` / `LoadAverage15m` | Media del carico | — |         |                                              |
| `LoadAverageWithFallback` | 5 m di carico, riduzione a 1 m | — |         |                                              |
| `DiskUtilization` | Disco utilizzato per montaggio | % |         | Facet di `mountpoint` |
| `DiskFreeBytes` | File system radice disponibile | byte |         |                                              |
| `DiskReadRate` / `DiskWriteRate` | Velocità effettiva del disco | byte/s | ✔ |                                              |
| `DiskReadOpsRate` / `DiskWriteOpsRate` | IOPS su disco | ops/s | ✔ |                                              |
| `NetworkReceiveRate` / `NetworkTransmitRate` | throughput eth0 | byte/s | ✔ |                                              |
| `NetworkReceiveRateAllDevices` / `NetworkTransmitRateAllDevices` | Tutti i dispositivi | byte/s | ✔ |                                              |
| `NetworkIoRate` | Tutte le direzioni e i dispositivi combinati | byte/s | ✔ |                                              |
| `NetworkReceiveErrorRate` / `NetworkTransmitErrorRate` | Errori di rete | errori/sec | ✔ |                                              |
| `NetworkReceivePacketRate` / `NetworkTransmitPacketRate` | Pacchetti | pacchetti/s | ✔ |                                              |
| `ProcessCpuUtilization` | CPU per processo | % |         | Facet di `process.name` |
| `ProcessMemoryUsage` | Memoria per processo | MB |         |                                              |
| `JvmHeapUsed` / `JvmHeapLimit` | Heap JVM | MB |         | Segnalato per pool di memoria |
| `JvmThreadCount` | Thread JVM | — |         |                                              |
| `JvmCpuRecentUtilization` | CPU del processo JVM | % |         | rollup di 1 minuto |
| `DispatcherRequestRate` | Richieste HTTP del dispatcher di AEM | req/s | ✔ |                                              |

>[!NOTE]
>
> Le metriche della memoria JVM sono riportate **per pool di memoria**, quindi `average(value)` su `JvmHeapUsed` / `JvmHeapLimit` medie tra pool. Utilizzare `FACET host, pool` per una visualizzazione esatta per pool.

### `SpanBreakdown`: composizione transazione preaggregata

`SpanBreakdown` contiene la composizione dell&#39;estensione figlio al minuto di ogni transazione. Viene calcolato al momento dell&#39;acquisizione, quindi è economico eseguire query.

| Attributo | Tipo | Descrizione |
| --------------- | --------- | ---------------------------------------------------------------------- |
| `timestamp` | timestamp | Bucket di un minuto |
| `service` | stringa | Servizio |
| `rootOperation` | stringa | Nome transazione padre |
| `segment` | stringa | Nome estensione figlio |
| `callCount` | numero | Chiamate di questo segmento nel bucket |
| `avgDurationMs` | numero | Durata media del segmento nel bucket (impostare manualmente l&#39;unità su `ms`) |
