---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-05"

keywords: liveness probe swift, readiness probe swift, health swift, healthcheck swift, swift app status, kubernetes endpoint swift, health endpoint swift, swift health check

subcollection: swift

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Utilizzo di un controllo di integrità nella tua applicazione Swift
{: #healthcheck}

I controlli di integrità forniscono un semplice meccanismo per determinare se un'applicazione lato server sta funzionando correttamente o meno. Gli ambienti cloud come [Kubernetes](https://www.ibm.com/cloud/container-service){: new_window} ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno") e [Cloud Foundry](https://www.ibm.com/cloud/cloud-foundry){: new_window} ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno") possono essere configurati per eseguire periodicamente il polling degli endpoint di integrità per determinare se un'istanza del tuo servizio è pronta ad accettare traffico.
{: shortdesc}

## Panoramica sul controllo di integrità
{: #overview}

I controlli di integrità forniscono un semplice meccanismo per determinare se un'applicazione lato server sta funzionando correttamente o meno. Normalmente sono utilizzati tramite HTTP e utilizzano i codici di ritorno standard per indicare lo stato UP o DOWN. Il valore di ritorno di un controllo di integrità è variabile, ma una risposta JSON minima, come `{"status": "UP"}`, è normale.

Kubernetes ha una nozione con più sfumature dell'integrità del processo. Definisce due probe:

- Viene utilizzato un probe di _**disponibilità**_ per indicare se il processo può gestire le richieste (è instradabile).

  Kubernetes non instrada il lavoro a un contenitore con un probe di disponibilità in errore. Un probe di disponibilità può avere esito negativo se un servizio non ha terminato l'inizializzazione o se è altrimenti occupato, sovraccaricato o se non può elaborare le richieste.

- Viene utilizzato un probe di _**attività**_ per indicare se il processo deve essere riavviato.

  Kubernetes arresta e riavvia un contenitore con un probe di attività in errore. Utilizza i probe di attività per ripulire i processi in uno stato irreversibile, ad esempio, se la memoria è esaurita o se il contenitore non è stato arrestato correttamente dopo che un processo interno si è arrestato in modo anomalo.

Come titolo di confronto, Cloud Foundry utilizza un endpoint di integrità. Se questo controllo non riesce, il processo viene riavviato, ma se ha esito positivo, gli vengono instradate le richieste. In questo ambiente, l'endpoint come minimo ha esito positivo quando il processo è live. Viene configurato un tempo di attesa iniziale per posporre il controllo di integrità finché il servizio non termina l'inizializzazione per evitare cicli di riavvio.

La seguente tabella fornisce le indicazioni sulle risposte che possono essere fornite dagli endpoint di integrità singolare, di attività e di disponibilità.

| Stato    | Disponibilità                   | Attività                   | Integrità                    |
|----------|-----------------------------|----------------------------|---------------------------|
|          | Non OK provoca nessun caricamento       | Non OK provoca il riavvio      | Non OK provoca il riavvio     |
| In avvio | 503 - Non disponibile           | 200 - OK                   | Utilizza il ritardo per evitare il test   |
| Attivo       | 200 - OK                    | 200 - OK                   | 200 - OK                  |
| In arresto | 503 - Non disponibile           | 200 - OK                   | 503 - Non disponibile         |
| Non attivo     | 503 - Non disponibile           | 503 - Non disponibile          | 503 - Non disponibile         |
| In errore  | 500 - Errore server          | 500 - Errore server         | 500 - Errore server        |
{: caption="Tabella 1. Codici di stato HTTP." caption-side="bottom"}

## Aggiunta di un controllo di integrità a un'applicazione Swift esistente
{: #existing-app}

La libreria [Health](https://github.com/IBM-Swift/Health){: new_window} ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno") facilita l'aggiunta di un controllo di integrità alla tua applicazione Swift. I controlli di integrità sono estensibili. Per ulteriori informazioni su [caching](https://github.com/IBM-Swift/Health#caching){: new_window} ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno") per evitare attacchi DoS o aggiungere [custom checks](https://github.com/IBM-Swift/Health#implementing-a-health-check){: new_window} ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno"), vedi la libreria [Health](https://github.com/IBM-Swift/Health){: new_window} ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno").

Per aggiungere la libreria Health a un'applicazione Swift esistente, vedi la seguente procedura:

1. Specificala nella sezione *dependencies:* del tuo file `Package.swift` e aggiungila a tutte le destinazioni appropriate:

    ```swift
    .package(url: "https://github.com/IBM-Swift/Health.git", .from: "1.0.0"),
    ```
    {: codeblock}

2. Aggiungi il seguente codice di inizializzazione alla tua applicazione:

    ```swift
    import Health

    let health = Health()
    ```
    {: codeblock}

3. Aggiungi la definizione di instradamento per definire l'endpoint di controllo di integrità:

    ```swift
    router.get("/health") { request, response, next in
        /* let status = health.status.toDictionary() */
        let status = health.status.toSimpleDictionary()
        if health.status.state == .UP {
            try response.send(json: status).end()
  } else {
            try response.status(.serviceUnavailable).send(json: status).end()
        }
    }
    ```
    {: codeblock}

4. Controlla lo stato dell'applicazione con un browser accedendo all'endpoint `/health`. Il codice restituisce un payload `{"status": "UP"}`, come definito dal dizionario semplice.

## Controllo dell'integrità di un'applicazione kit starter Swift lato server
{: #healthcheck-starterkit}

Quando generi un'applicazione Swift basata su Kitura utilizzando un kit starter, un endpoint di controllo di integrità di base `/health`, viene incluso per impostazione predefinita. L'endpoint utilizza il protocollo Codable disponibile in Swift 4, come supportato dalla libreria [Health](https://github.com/IBM-Swift/Health){: new_window} ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno").

Il codice di inizializzazione di base, quale l'inizializzazione dell'oggetto Health, si verifica in `Sources/Application.swift`. L'endpoint del controllo di integrità stesso viene fornito dal file `/Sources/Application/Routes/HealthRoutes.swift` e utilizza il seguente codice:

```swift
import LoggerAPI
import Health
import KituraContracts

func initializeHealthRoutes(app: App) {

    app.router.get("/health") { (respondWith: (Status?, RequestError?) -> Void) -> Void in
        if health.status.state == .UP {
            respondWith(health.status, nil)
        } else {
            respondWith(nil, RequestError(.serviceUnavailable, body: health.status))
        }
    }

}
```
{: codeblock}

L'esempio utilizza il dizionario standard, che produce un payload simile al seguente:
```
{"status":"UP","details":[],"timestamp":"2018-07-31T17:41:16+0000"}
```

quando accedi all'endpoint `/health`.

## Suggerimenti per i probe di disponibilità e di attività
{: #recommend-probes}

I controlli di disponibilità devono includere l'applicabilità delle connessioni ai servizi in downstream nei propri risultati se non esiste alcun fallback accettabile per quando non è disponibile il servizio in downstream. Questo non significa di chiamare il controllo di integrità fornito direttamente dal servizio in downstream, perché l'infrastruttura esegue il controllo per te. Invece, prendi in considerazione di verificare l'integrità dei riferimenti esistenti che la tua applicazione ha con i servizi in downstream: che potrebbero essere una connessione JMS a WebSphere MQ o un consumatore o produttore Kafka inizializzato. Se non controlli l'applicabilità dei riferimenti interni ai servizi in downstream, memorizza nella cache il risultato per ridurre al minimo l'impatto del controllo di integrità sulla tua applicazione.

Un probe di attività, al contrario, può essere cauto su cosa viene controllato, perché un errore comporta una terminazione immediata del processo. Un endpoint HTTP semplice che restituisce sempre `{"status": "UP"}` con il codice di stato `200` è una scelta ragionevole.

### Aggiungi il supporto per la disponibilità e l'attività di Kubernetes a un'applicazione Swift
{: #kube-readiness-swift}

Per delle implementazioni alternative, quali l'utilizzo di **Codable** o del dizionario standard, vedi gli [esempi della libreria Health](https://github.com/IBM-Swift/Health#usage){: new_window} ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno"). Alcune di queste implementazioni semplificano la creazione di controlli di integrità estensibili con il supporto per la memorizzazione nella cache dei controlli eseguiti sui servizi di backup. In questo scenario, vuoi separare il test di attività semplice dal controllo di disponibilità più dettagliato e solido.

## Configurazione dei probe di disponibilità e di attività in Kubernetes
{: #config-kube-readiness}

Dichiara i probe di disponibilità e di attività insieme alla tua distribuzione Kubernetes. Entrambi i probe utilizzano gli stessi parametri di configurazione.

* Il kubelet attende i secondi del ritardo iniziale (`initialDelaySeconds`) prima del primo probe.

* Il kubelet analizza il servizio ogni `periodSeconds` secondi. Il valore predefinito è 1.

* Il probe va in timeout dopo `timeoutSeconds` secondi. Il valore predefinito e minimo è 1.

* Il probe ha esito positivo se riesce `successThreshold` volte dopo un errore. Il valore predefinito e minimo è 1. Il valore deve essere 1 per i probe di attività.

* Quando si avvia un pod e il probe ha esito negativo, Kubernetes tenta `failureThreshold` volte di riavviare il pod dopodiché smette. Il valore minimo è 1 e il valore predefinito è 3.
    - Per un probe di attività, "smettere" significa riavviare il pod.
    - Per un probe di disponibilità, "smettere" significa contrassegnare il pod come `Unready`.

Per evitare i cicli di riavvio, imposta `livenessProbe.initialDelaySeconds` in modo da essere tranquillamente più lungo del tempo necessario al tuo servizio per l'inizializzazione. Puoi poi utilizzare un valore più corto per `readinessProbe.initialDelaySeconds` per instradare le richieste al servizio come è pronto.

Consulta il seguente esempio `yaml`:
```yaml
spec:
  containers:
  - name: ...
    image: ...
    readinessProbe:
      httpGet:
        path: /health
        port: 8080
      initialDelaySeconds: 120
      timeoutSeconds: 5
    livenessProbe:
      httpGet:
        path: /liveness
        port: 8080
      initialDelaySeconds: 130
      timeoutSeconds: 10
      failureThreshold: 10
```

Per ulteriori informazioni, consulta [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/){: new_window} ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno").
