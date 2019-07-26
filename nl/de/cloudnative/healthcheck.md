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

# Statusprüfung in einer Swift-App verwenden
{: #healthcheck}

Statusprüfungen stellen einen einfachen Mechanismus zur Verfügung, mit
dem festgestellt werden kann, ob eine serverseitige Anwendung ordnungsgemäß
funktioniert. Cloud-Umgebungen wie [Kubernetes](https://www.ibm.com/cloud/container-service){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link") und [Cloud Foundry](https://www.ibm.com/cloud/cloud-foundry){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link") können so konfiguriert werden, dass sie in regelmäßigen Abständen Statusendpunkte abfragen, um zu ermitteln, ob eine Instanz Ihres Service für die Annahme von Datenverkehr bereit ist.
{: shortdesc}

## Übersicht über die Statusprüfung
{: #overview}

Statusprüfungen stellen einen einfachen Mechanismus zur Verfügung, mit
dem festgestellt werden kann, ob eine serverseitige Anwendung ordnungsgemäß
funktioniert. Statusprüfungen werden normalerweise über HTTP verarbeitet und verwenden
Standardrückgabecodes, um den Status UP oder DOWN mitzuteilen. Der
Rückgabewert einer Statusprüfung ist zwar variabel, aber eine minimale
JSON-Antwort wie `{"status": "UP"}` ist typisch.

Kubernetes hat ein differenziertes Verständnis vom Prozessstatus. Es werden zwei Prüfungen definiert:

- Eine _**Bereitschafts**_prüfung (Readiness Probe) wird verwendet, um anzuzeigen, ob der Prozess Anforderungen verarbeiten kann (routingfähig ist).

  Kubernetes leitet Arbeit nicht an Container weiter, die die Bereitschaftsprüfung nicht bestanden haben. Eine Bereitschaftsprüfung kann fehlschlagen, wenn ein Service die Initialisierung nicht abgeschlossen hat oder anderweitig belegt oder überlastet ist oder aus anderen Gründen nicht in der Lage ist, Anforderungen zu verarbeiten.

- Eine _**Aktivitäts**_prüfung (livenessProbe) wird verwendet, um anzugeben, ob der Prozess erneut gestartet werden soll.

  Kubernetes stoppt und startet einen Container mit einer fehlgeschlagenen Aktivitätsprüfung erneut. Verwenden Sie Aktivitätsprüfungen, um Prozesse in einem nicht wiederherstellbaren Zustand zu bereinigen, wenn diese zum Beispiel den Speicher überlasten oder wenn der Container nach einem internen Prozessabsturz nicht ordnungsgemäß stoppte.

Als Hinweis für den Vergleich verwendet Cloud Foundry einen Statusendpunkt. Wenn die Prüfung fehlschlägt, wird der Prozess erneut gestartet; aber wenn die Prüfung erfolgreich ist, werden Anforderungen an ihn weitergeleitet. In dieser Umgebung ist der Endpunkt mindestens dann erfolgreich, wenn der Prozess aktiv ist. Eine Anfangswartezeit ist so konfiguriert, dass die Statusprüfung so lange verzögert wird, bis die Initialisierung des Service abgeschlossen ist, um das erneute Starten von Zyklen zu vermeiden.

Die folgende Tabelle enthält Informationen zu den Reaktionen der Prüfung von Bereitschaft, Aktivität und einzelnen Statusendpunkten.

| Status    | Bereitschaft                   | Aktivität                   | Statusendpunkt                    |
|----------|-----------------------------|----------------------------|---------------------------|
|          | Kein-OK keine Auslastung       | Kein-OK bewirkt Neustart      | Kein-OK bewirkt Neustart     |
| wird gestartet | 503 - nicht verfügbar           | 200 - OK                   | Verzögerung um Test zu vermeiden   |
| Aktiv       | 200 - OK                    | 200 - OK                   | 200 - OK                  |
| Wird gestoppt | 503 - nicht verfügbar           | 200 - OK                   | 503 - nicht verfügbar         |
| Inaktiv     | 503 - nicht verfügbar           | 503 - nicht verfügbar          | 503 - nicht verfügbar         |
| Fehlerhaft  | 500 - Serverfehler          | 500 - Serverfehler         | 500 - Serverfehler        |
{: caption="Tabelle 1. HTTP-Statuscodes." caption-side="bottom"}

## Statusprüfung zu einer vorhandenen Swift-App hinzufügen
{: #existing-app}

Die Bibliothek [Health](https://github.com/IBM-Swift/Health){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link") macht das Hinzufügen einer Statusprüfung zu einer Swift-Anwendung einfach. Statusprüfungen
können erweitert werden. Weitere Informationen zum [Caching](https://github.com/IBM-Swift/Health#caching){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link") zur Verhinderung von DoS-Attacken oder zur Hinzufügung von [benutzerdefinierten Überprüfungen](https://github.com/IBM-Swift/Health#implementing-a-health-check){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link") finden Sie in der Bibliothek [Health](https://github.com/IBM-Swift/Health){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link").

Führen Sie folgende Schritte aus, um die Bibliothek 'Health' einer vorhandenen Swift-App hinzuzufügen:

1. Geben Sie sie im Abschnitt *dependencies:* der Datei `Package.swift` an und fügen Sie sie zu allen entsprechenden Zielen hinzu:

    ```swift
    .package(url: "https://github.com/IBM-Swift/Health.git", .from: "1.0.0"),
    ```
    {: codeblock}

2. Fügen Sie folgenden Initialisierungscode zu Ihrer Anwendung hinzu:

    ```swift
    import Health

    let health = Health()
    ```
    {: codeblock}

3. Fügen Sie die Routendefinition hinzu, um den Endpunkt der Statusprüfung zu definieren:

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

4. Überprüfen Sie den Status der App, indem Sie mit einem Browser auf den
Endpunkt
`/health` zugreifen. Der Code gibt Nutzdaten im Format
`{"status": "UP"}` wie durch das einfache Wörterverzeichnis
definiert zurück.

## Status einer serverseitigen Swift-Starter-Kit-App prüfen
{: #healthcheck-starterkit}

Wenn Sie eine Kitura-basierte Swift-App mithilfe eines Starter-Kits generieren, ist standardmäßig ein Basisendpunkt für die Statusprüfung namens `/health` enthalten. Der Endpunkt verwendet das in Swift 4 verfügbare Codable-Protokoll, so wie es von der Bibliothek [Health](https://github.com/IBM-Swift/Health){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link") unterstützt wird.

Der Basisinitialisierungscode, wie zum Beispiel die Initialisierung des Zustandsobjekts erscheint in `Sources/Application.swift`. Der Endpunkt der Statusprüfung selbst wird von der Datei `/Sources/Application/Routes/HealthRoutes.swift` bereitgestellt und verwenden den folgenden Code:

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

Im Beispiel wird das Standardwörterverzeichnis verwendet, das Nutzdaten wie die folgenden zurückgibt:
```
{"status":"UP","details":[],"timestamp":"2018-07-31T17:41:16+0000"}
```

Dies tritt auf, wenn Sie auf den Endpunkt `/health` zugreifen.

## Empfehlungen für die Bereitschafts- und Aktivitätsprüfungen
{: #recommend-probes}

Die Bereitschaftsprüfungen müssen die Durchführbarkeit von Verbindungen zu nachfolgenden Services in ihr Ergebnis einschließen, falls ein nicht akzeptierbarer Rückgriff für den Fall vorkommt, dass der nachfolgende Service nicht verfügbar ist. Das heißt nicht, dass die Statusüberprüfung, die vom nachfolgenden Service bereitgestellt wird, direkt aufgerufen wird, während die Infrastruktur das für Sie überprüft. Überprüfen Sie stattdessen den Zustand der vorhandenen Verweise Ihrer Anwendung zu nachfolgenden Services: dies kann eine JMS-Verbindung zu WebSphere MQ oder ein initialisierter Kafka-Nutzer oder -Erzeuger sein. Wenn Sie die Durchführbarkeit von internen Verweisen zu nachfolgenden Services überprüfen, stellen Sie das Ergebnis in den Cache, um die Auswirkung der Statusüberprüfung auf Ihre Anwendung möglichst gering zu halten.

Bei einer Aktivitätsprüfung dagegen kann darüber nachgedacht werden, was geprüft wird, da der Fehler zu einem sofortigen Abbruch des Prozesses führt. Ein einfacher HTTP-Endpunkt, der immer `{"status": "UP"}` mit dem Statuscode `200` zurückgibt ist eine angemessene Wahl.

### Unterstützung für Kubernetes-Bereitschafts- und Aktivitätsprüfung zu einer Swift-App hinzufügen
{: #kube-readiness-swift}

Informationen zu alternativen Implementierungen wie z. B. zur Verwendung von **Codable** oder des Standardwörterverzeichnisses finden Sie in den [Beispielen zur Bibliothek 'Health'](https://github.com/IBM-Swift/Health#usage){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link"). Einige dieser Implementierungen vereinfachen die Erstellung von erweiterbaren Statusprüfungen mit Unterstützung für Cacheprüfungen, die für Unterstützungsservices ausgeführt werden. In diesem Szenario möchten Sie die einfache Aktivitätsprüfung von der robusteren und detaillierteren Bereitschaftsprüfung trennen.

## Konfigurieren Sie Bereitschafts- und Aktivitätsprüfungen in Kubernetes
{: #config-kube-readiness}

Deklarieren Sie Bereitschafts- und Aktivierungsprüfungen neben Ihrer Kubernetes-Bereitstellung. Beide Prüfungen verwenden dieselben Konfigurationsparameter:

* Der Kubelet wartet vor der ersten Prüfung auf `initialDelaySeconds`.

* Der Kubelet prüft den Service alle `periodSeconds` Sekunden. Der Standardwert ist 1.

* Die Prüfung überschreitet nach `timeoutSeconds` Sekunden das Zeitlimit. Der Standard- und Mindestwert ist 1.

* Die Prüfung ist erfolgreich, wenn nach einem Fehler nach so häufig wie in `successThreshold` angegebenen Prüfungen erfolgreich geprüft wurde. Der Standard- und Mindestwert ist 1. Der Wert muss für Aktivitätsprüfungen 1 sein.

* Wenn ein Pod startet und die Prüfung fehlschlägt, versucht Kubernetes, so oft wie durch `failureThreshold` angegeben den Pod erneut zu starten und gibt danach auf. Der Mindestwert ist 1 und der Standardwert ist 3.
    - Bei einer Aktivitätsprüfung bedeutet 'aufgeben', den Pod erneut zu starten.
    - Bei einer Bereitschaftsprüfung bedeutet 'aufgeben', den Pod als nicht bereit (`Unready`) zu markieren.

Um wiederholte Neustarts zu vermeiden, legen Sie für `livenessProbe.initialDelaySeconds` einen Wert fest, der mit Sicherheit länger ist als die Zeit, die der Service zum Initialisieren benötigt. Sie können dann einen kürzeren Wert für `readinessProbe.initialDelaySeconds` verwenden, um Anforderungen an den Service weiterzuleiten, sobald dieser bereit ist.

Sehen Sie sich das folgende `yaml`-Beispiel an:
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

Weitere Informationen finden Sie im Abschnitt [Aktivitäts- und Bereitschaftsprüfungen konfigurieren](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link").
