---
source-git-commit: e5523081fcd68500602e5d1bf853694d1f6c3980
workflow-type: tm+mt
source-wordcount: '1104'
ht-degree: 7%

---
# Observability Insights - API pubblica

L’API pubblica di Observability Insights consente di estrarre i propri dati di osservabilità (panoramiche delle richieste, cataloghi dei servizi, tracce e metriche) direttamente nei propri strumenti, script e dashboard.

- **URL di base API (API_BASE_URL):** `https://insights.adobecqms.net/`
- **Formato:** JSON su HTTPS
- **Autenticazione:** chiave API (token Bearer)

> Sostituisci `{{API_BASE_URL}}` in questo documento con l&#39;host API dell&#39;istanza Observability Insights, ad esempio `https://insights.adobecqms.net/`.

---

## &#x200B;1. Ottenere una chiave API

Le chiavi API sono credenziali personali collegate al tuo account e con ambito a una singola organizzazione. Una chiave può leggere solo i dati dei tenant che appartengono all’organizzazione per la quale è stata creata e non può mai visualizzare i dati di un’altra organizzazione.

### Generare una chiave

1. Accedi al dashboard di [Observability Insights](https://insights.adobecqms.net/).
2. Apri il menu del tuo profilo (in alto a destra) → **Chiavi API**.
   ![Menu Chiavi API](v2-assets/api-key.png)
3. Nella scheda **Chiavi API**, fai clic su **Genera chiave**.
   ![Genera chiave API](v2-assets/api-key-gen.png)
4. Assegnare un nome descrittivo (ad esempio `CI pipeline`, `Grafana datasource`), scegliere l&#39;organizzazione a cui assegnare l&#39;ambito e, facoltativamente, impostare una data di scadenza.
5. Fai clic su **Genera chiave**. La chiave viene visualizzata **una volta**, nel formato:

   ```
   synx_9pQ2v6f1WYbLZk3n0aRtEo4jXcHsVmDgUiPq7B8l1yc
   ```

   **Copiarlo immediatamente e archiviarlo in un luogo sicuro** (un gestore segreti, un archivio segreto CI, ecc.) — la dashboard non può più essere visualizzata. Se lo perdi, revocalo e generane uno nuovo.

### Gestisci chiavi esistenti

La sezione Chiavi API elenca tutte le chiavi create, inclusa l’organizzazione, la data di creazione, la scadenza e la marca temporale dell’ultimo utilizzo. Fare clic sull&#39;icona del cestino accanto a una chiave per **revocarla**. La revoca è immediata e non può essere annullata.

### Sicurezza chiave

- Considera una chiave API esattamente come una password. Chiunque disponga della chiave può leggere tutti i dati di osservabilità per ogni tenant dell’organizzazione a cui ha ambito, fino alla revoca o alla scadenza.
- Non eseguire mai il commit di una chiave nel controllo del codice sorgente o condividerla in testo normale (chat, e-mail, ticket).
- Ruota le chiavi periodicamente e revoca tutte le chiavi non più in uso.
- Se una chiave è compromessa, revocala immediatamente da **Impostazioni organizzazione → Chiavi API** e generane una sostitutiva.

---

## &#x200B;2. Autenticazione delle richieste

Ogni richiesta all&#39;API pubblica deve includere la chiave nell&#39;intestazione `Authorization`:

```
Authorization: Bearer synx_9pQ2v6f1WYbLZk3n0aRtEo4jXcHsVmDgUiPq7B8l1yc
```

Le richieste senza una chiave valida o con una chiave scaduta/revocata ricevono `401 Unauthorized`. Gli accessi di sessione (cookie/token del browser) sono **non** accettati in questa API.

---

## &#x200B;3. Concetti di base

### Tenant

Ogni endpoint richiede un parametro di query `tenant_id` che identifica i dati del tenant da leggere. Una chiave può eseguire query solo sui tenant che appartengono all&#39;organizzazione per la quale è stata creata; la richiesta di un tenant esterno all&#39;organizzazione restituisce `403 Forbidden`. Non esiste una modalità &quot;tutti i tenant&quot; in questa API. Passare sempre un `tenant_id` specifico.

Non sei sicuro di quali `tenant_id` valori possa utilizzare la tua chiave? Chiamare [`GET /public/v1/tenants`](#get-publicv1tenants): elenca esattamente i tenant per i quali la chiave è autorizzata a eseguire query.

### Intervalli di tempo

Gli endpoint che accettano i parametri `from` / `to` richiedono marche temporali Unix (secondi), marche temporali millisecondi o stringhe datetime ISO 8601, ad esempio:

```
from=1735689600
from=2025-01-01T00:00:00Z
```

Se omesso, la maggior parte degli endpoint viene impostata in modo predefinito su una finestra continua recente (vedere ogni endpoint di seguito).

### Limiti di tariffa

Le richieste hanno un tasso limitato per ogni chiave API. Se superi il limite, riceverai:

```http
HTTP/1.1 429 Too Many Requests
Retry-After: 60

{ "error": "Too Many Requests", "message": "Rate limit of 300 requests/60s exceeded" }
```

Indietro e riprovare dopo il numero di secondi nell&#39;intestazione `Retry-After`. Se il tuo caso d’uso richiede un limite più alto, contatta il supporto.

### Errori

Gli errori vengono restituiti come JSON con un campo `error` e, in genere, un `message` leggibile:

```json
{ "error": "Bad Request", "message": "tenant_id is required" }
```

| Stato | Significato |
| ------------------------- | ------------------------------------------------------------------ |
| `400 Bad Request` | Parametro mancante o non valido (ad esempio, nessun `tenant_id`, intervallo di tempo non valido) |
| `401 Unauthorized` | Chiave API mancante, non valida, scaduta o revocata |
| `403 Forbidden` | La chiave non è autorizzata per il tenant richiesto |
| `429 Too Many Requests` | Limite di frequenza superato — vedere `Retry-After` |
| `502 Bad Gateway` | Query upstream non riuscita. Riprovare in sicurezza |
| `503 Service Unavailable` | Back-end dei dati temporaneamente non disponibile |

---

## &#x200B;4. Endpoint

### `GET /public/v1/tenants`

Elenca gli ID tenant autorizzati per la query della chiave. Chiamare prima questo endpoint. Ogni altro endpoint richiede uno di questi valori come `tenant_id`.

```bash
curl -s "{{API_BASE_URL}}/public/v1/tenants" \
  -H "Authorization: Bearer $Observability_Insights_API_KEY"
```

```json
{ "tenants": ["tenant1", "tenant2"] }
```

### `GET /public/v1/overview`

KPI di integrità di alto livello per un tenant in un intervallo di tempo: volume della richiesta, tasso di errore e percentili di latenza.

| Parametro | Obbligatorio | Descrizione |
| ------------ | -------- | ------------------------------------------------------------------------- |
| `tenant_id` | Sì | Tenant da interrogare |
| `from`, `to` | No | Intervallo di tempo (vedi [Intervalli di tempo](#time-ranges)) |
| `minutes` | No | Sintesi abbreviata per &quot;ultimi N minuti&quot; se `from`/`to` non sono specificati (impostazione predefinita `15`) |

```bash
curl -s "{{API_BASE_URL}}/public/v1/overview?tenant_id=<tenant_id>&minutes=30" \
  -H "Authorization: Bearer $Observability_Insights_API_KEY"
```

```json
{
  "tenant_id": "<tenant_id>",
  "from": 1735689000,
  "to": 1735690800,
  "total_spans": 48213,
  "errors": 112,
  "error_rate_pct": 0.23,
  "p50_ms": 34,
  "p95_ms": 210,
  "p99_ms": 480,
  "service_count": 12,
  "trace_count": 9021
}
```

### `GET /public/v1/services`

Elenca i rapporti con nomi di servizio distinti per un tenant.

| Parametro | Obbligatorio | Descrizione |
| ------------ | -------- | --------------------------------------------------------------------- |
| `tenant_id` | Sì | Tenant da interrogare |
| `from`, `to` | No | Limita ai servizi visualizzati in questa finestra; impostazione predefinita: ultimi 7 giorni |

```bash
curl -s "{{API_BASE_URL}}/public/v1/services?tenant_id=<tenant_id>" \
  -H "Authorization: Bearer $Observability_Insights_API_KEY"
```

```json
{
  "tenant_id": "<tenant_id>",
  "services": ["checkout-api", "payments-worker", "web-frontend"]
}
```

### `GET /public/v1/traces`

Cerca tracce recenti per un tenant, con filtri facoltativi.

| Parametro | Obbligatorio | Descrizione |
| ----------------- | -------- | ------------------------------------------------- |
| `tenant_id` | Sì | Tenant da interrogare |
| `from`, `to` | No | Intervallo di tempo; impostazione predefinita ultime 24 ore |
| `limit` | No | Numero massimo di righe da restituire (1-200, valore predefinito 100) |
| `offset` | No | Offset paginazione (valore predefinito: 0) |
| `service` | No | Filtra per nome di servizio |
| `app_name` | No | Filtra per nome applicazione/istanza |
| `status` | No | Filtra per stato traccia: `ok`, `error` o `unset` |
| `search` | No | Ricerca di testo libero nei nomi di estensione/operazione |
| `min_duration_ms` | No | Solo tracce di questa durata o superiori |

```bash
curl -s "{{API_BASE_URL}}/public/v1/traces?tenant_id=<tenant_id>&status=error&limit=25" \
  -H "Authorization: Bearer $Observability_Insights_API_KEY"
```

```json
{
  "data": [
    {
      "TraceId": "4bf92f3577b34da6a3ce929d0e0e4736",
      "ServiceName": "checkout-api",
      "DurationMs": 812,
      "StatusCode": "Error",
      "Timestamp": "2026-08-30T09:12:44Z"
    }
  ],
  "rows": 137,
  "limit": 25,
  "offset": 0
}
```

Utilizza `rows` (il conteggio totale corrispondente) insieme a `limit`/`offset` per passare da un risultato all&#39;altro.

### `GET /public/v1/traces/:traceId`

Restituisce la cascata di estensione completa per una singola traccia.

| Parametro | Obbligatorio | Descrizione |
| ----------- | -------- | ---------------------------------------- |
| `tenant_id` | Sì | Tenant a cui appartiene la traccia |
| `limit` | No | Estensioni massime da restituire (1-500, 500 predefinite) |
| `offset` | No | Offset paginazione per tracce molto grandi |

```bash
curl -s "{{API_BASE_URL}}/public/v1/traces/4bf92f3577b34da6a3ce929d0e0e4736?tenant_id=<tenant_id>" \
  -H "Authorization: Bearer $Observability_Insights_API_KEY"
```

```json
{
  "spans": [
    {
      "SpanId": "00f067aa0ba902b7",
      "Name": "POST /checkout",
      "DurationMs": 812,
      "children": []
    }
  ],
  "totalDurationMs": 812,
  "spanCount": 14,
  "limit": 500,
  "offset": 0
}
```

### `GET /public/v1/metrics`

Restituisce i punti dati della metrica non elaborati per un tenant.

| Parametro | Obbligatorio | Descrizione |
| ---------------------------------- | ---------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| `tenant_id` | Sì | Tenant da interrogare |
| `metric` | Uno di `metric`/`like` | Nome metrica esatto |
| `like` | Uno di `metric`/`like` | Schema SQL `LIKE` per la corrispondenza con più nomi di metrica |
| `type` | No | `gauge` (predefinito) o `sum` |
| `from`, `to` | No | Intervallo di tempo; impostazione predefinita ultime 24 ore |
| `service` | No | Filtra per nome di servizio |
| `host` | No | Filtra per nome host. Richiesto per le metriche host dell’infrastruttura riportate di seguito: senza di esso, le letture da ogni host del tenant vengono combinate |
| `attribute_key`, `attribute_value` | No | Filtrare per un attributo di metrica specifico (deve essere utilizzato insieme) |

```bash
curl -s "{{API_BASE_URL}}/public/v1/metrics?tenant_id=<tenant_id>&metric=jvm.memory.used&type=gauge" \
  -H "Authorization: Bearer $Observability_Insights_API_KEY"
```

```json
{
  "data": [
    {
      "TimeUnix": "2026-08-30T09:00:00Z",
      "MetricName": "jvm.memory.used",
      "Value": 512482816,
      "ServiceName": "checkout-api",
      "host": ""
    }
  ],
  "rows": 1
}
```

#### Metriche host per l’infrastruttura

Lo stesso endpoint serve anche le metriche a livello di host visualizzate nel dashboard dell’infrastruttura (CPU, memoria, media di carico, I/O del disco, I/O della rete). Utilizza queste combinazioni esatte `metric` / `attribute_key` / `attribute_value`, sempre con `host`:

| Widget dashboard | `metric` | `attribute_key` | `attribute_value` |
| --------------------- | ------------------------------------- | --------------- | ------------------------------------------------------------------------------------------- |
| CPU % | `system.cpu.utilization` | `state` | `idle` (sottrarre da 1 per &quot;in uso&quot;) o query `user`/`system`/`iowait` separatamente e somma |
| % utilizzo memoria | `system.memory.utilization` | `state` | `used` |
| Media carico (1 m) | `system.cpu.load_average.1m` | — | — |
| I/O lettura disco | `system.disk.io` (`type=sum`) | `direction` | `read` |
| I/O di scrittura su disco | `system.disk.io` (`type=sum`) | `direction` | `write` |
| Operazioni di lettura disco | `system.disk.operations` (`type=sum`) | `direction` | `read` |
| Operazioni di scrittura su disco | `system.disk.operations` (`type=sum`) | `direction` | `write` |
| Ingresso rete | `system.network.io` (`type=sum`) | `direction` | `receive` |
| Uscita di rete | `system.network.io` (`type=sum`) | `direction` | `transmit` |

```bash
curl -s "{{API_BASE_URL}}/public/v1/metrics?tenant_id=<tenant_id>&metric=system.cpu.utilization&type=gauge&attribute_key=state&attribute_value=idle&host=<host_name>" \
  -H "Authorization: Bearer $Observability_Insights_API_KEY"
```

**Importante: i valori del disco e della rete sono contatori non elaborati in continua crescita, non percentuali.** I grafici &quot;byte/sec&quot; e &quot;operazioni/sec&quot; del dashboard vengono calcolati eseguendo due letture consecutive del contatore e dividendo per il tempo trascorso:

```
rate = (value_at_t2 - value_at_t1) / (t2 - t1_in_seconds)
```

### `GET /public/v1/pages`

Prime pagine di contenuto richieste (`.html`) per istanza di Dispatcher, ordinate per numero di richieste. Supportato dalla metrica `dispatcher.httpd.requests`: questo endpoint è specifico per i registri di accesso in stile AEM Dispatcher/CDN, non è uno strumento generale di analisi delle pagine.

| Parametro | Obbligatorio | Descrizione |
| ------------ | -------- | -------------------------------------- |
| `tenant_id` | Sì | Tenant da interrogare |
| `from`, `to` | No | Intervallo di tempo; impostazione predefinita ultime 24 ore |
| `limit` | No | Numero massimo di righe da restituire (1-500, valore predefinito 50) |

```bash
curl -s "{{API_BASE_URL}}/public/v1/pages?tenant_id=<tenant_id>&limit=50" \
  -H "Authorization: Bearer $Observability_Insights_API_KEY"
```

```json
{
  "tenant_id": "<tenant_id>",
  "from": 1735689000,
  "to": 1735690800,
  "data": [
    {
      "instance": "<instance_name>",
      "domain": "www.abc.com",
      "path": "/join-us/insights.html",
      "full_url": "https://www.abc.com/join-us/insights.html",
      "requests": 7
    }
  ],
  "rows": 1
}
```

---

## &#x200B;5. Quali funzioni non svolge questa API

- **Nessun accesso SQL non elaborato.** Tutti gli endpoint restituiscono forme dati specifiche e curate, pertanto non è possibile eseguire direttamente una query sull&#39;archivio dati sottostante.
- **Nessuna query tra tenant.** Ogni richiesta ha l&#39;ambito esatto di un `tenant_id`.
- **Nessun accesso in scrittura.** L’API pubblica è di sola lettura.

---

## &#x200B;6. Supporto

Se riscontri errori imprevisti o un caso d’uso non coperto da questi endpoint, contatta il Customer Success/Enablement Engineer per ulteriore assistenza.
