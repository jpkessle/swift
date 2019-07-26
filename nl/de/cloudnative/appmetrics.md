---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-07"

keywords: swiftmetrics-dash, swiftmetrics, prometheus swift, application metrics swift, swift performance, slow swift, swift dashboard, metris swift

subcollection: swift

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Anwendungsmetriken bei Swift-Apps verwenden
{: #metrics}

Anwendungsmetriken sind wichtig, um die Leistung Ihrer Anwendung zu
überwachen. Eine Live-Ansicht von Metriken wie CPU-, Speicher-, Latenzzeit- und HTTP-Metriken ist notwendig, um sicherzustellen, dass Ihre Anwendung jederzeit effektiv ausgeführt wird. Kubernetes- und Cloud Foundry-Services wie [autoscaling](/docs/services/Auto-Scaling?topic=Auto-Scaling-get-started) verwenden Metriken, um zu ermitteln, wann Instanzen aufgrund der Arbeitslast dynamisch hinzugefügt bzw. entfernt oder nicht mehr benötigte Instanzen bereinigt werden sollen, um Kosten zu sparen.

Anwendungsmetriken werden als Zeitreihendaten erfasst. Das Zusammenfassen und Visualisieren von erfassten Metriken kann Sie bei der Erkennung allgemeiner
Leistungsprobleme unterstützen. Beispiele:

* Lange HTTP-Antwortzeiten bei einigen oder allen Routen
* Schlechter Durchsatz in der Anwendung
* Leistungsminderung infolge von Nachfragespitzen
* CPU-Auslastung in unerwarteter Höhe
* Hohe oder zunehmende Hauptspeicherbelegung (potenzieller
Speicherverlust)

## Anwendungsmetriken zu einer vorhandenen Swift-Anwendung hinzufügen
{: #add-appmetrics-existing}

Mit [Application Metrics for Swift](https://developer.ibm.com/swift/monitoring-diagnostics/application-metrics-for-swift/){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link") können Sie die Leistungsüberwachung zu Ihrer Swift-Anwendung hinzufügen. Application Metrics for Swift besteht aus zwei
Bibliotheken namens `SwiftMetrics` und `SwiftMetricsDash`.

* Die Bibliothek `SwiftMetrics` ist eine umfassende
Instrumentierungsbibliothek, in der Metriken für Ihre Anwendung
zusammengestellt und aggregiert werden. Sie hat mehrere Erweiterungen, unter anderem ein Kitura-Modul für HTTP-Metriken, [Prometheus-Unterstützung](https://github.com/RuntimeTools/SwiftMetrics#prometheus-support){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link") und einen eigenständigen [Emitter](https://github.com/RuntimeTools/SwiftMetrics#application-metrics-for-swift-agent){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link").

* Die Bibliothek `SwiftMetricsDash` verarbeitet die von
`SwiftMetrics` erzeugten Metriken und bietet ein integriertes
Dashboard für ihre Darstellung.

Um die Basis-API für die Überwachung zu
aktivieren, fügen Sie `SwiftMetrics` zum Abschnitt
**dependencies:** in der Datei
`Package.swift` hinzu (achten Sie darauf, sie zum entsprechenden
Ziel hinzuzufügen):
```swift
.package(url: "https://github.com/RuntimeTools/SwiftMetrics.git", from: "2.4.0")
```
{: codeblock}

Fügen Sie den folgenden Instrumentierungscode hinzu, um den Code von
`SwiftMetrics` zu initialisieren:
```swift
import SwiftMetrics
import SwiftMetricsKitura

let metrics = try SwiftMetrics()
SwiftMetricsKitura(swiftMetricsInstance: metrics)
let monitoring = metrics.monitor()
```
{: codeblock}

Fügen Sie den folgenden Code zum ursprünglichen Beispiel hinzu, damit
Metriken im Überwachungsdashboard zur Verfügung gestellt werden:
```swift
import SwiftMetricsDash

let smd = try SwiftMetricsDash(swiftMetricsInstance : metrics)
```  
{: codeblock}

`SwiftMetricsDash` startet standardmäßig einen eigenen Kitura-Server und stellt die Seite unter `http://<hostname>:<port>/swiftmetrics-dash` bereit. Wenn Sie auf das Dashboard zugreifen, werden Ihre neuen
Anwendungsmetriken inklusive HTTP-Anforderungen und Ereignisschleifenlatenz
angezeigt.

## Anwendungsmetriken in Starter-Kits verwenden
{: #appmetrics-starterkits}

Die serverseitigen Swift-Anwendungen, die ausgehend von Starter-Kits erstellt werden, beinhalten den Code für `SwiftMetrics`, `SwiftMetricsDash` und `SwiftMetricsPrometheus` und können daher sofort in Kubernetes-Umgebungen eingesetzt werden, die Prometheus-Endpunkte zur Erfassung von Metriken verwenden.

Der Code für `SwiftMetrics` ist in der Datei
`/Sources/Application/Metrics.swift` zu finden:
```swift
import Kitura
import SwiftMetrics
import SwiftMetricsDash
import SwiftMetricsPrometheus
import LoggerAPI

var swiftMetrics: SwiftMetrics?
var swiftMetricsDash: SwiftMetricsDash?
var swiftMetricsPrometheus: SwiftMetricsPrometheus?

func initializeMetrics(router: Router) {
    do {
        let metrics = try SwiftMetrics()
        let dashboard = try SwiftMetricsDash(swiftMetricsInstance: metrics, endpoint: router)
        let prometheus = try SwiftMetricsPrometheus(swiftMetricsInstance: metrics, endpoint: router)

        swiftMetrics = metrics
        swiftMetricsDash = dashboard
        swiftMetricsPrometheus = prometheus
        Log.info("Initialized metrics.")
    } catch {
        Log.warning("Failed to initialize metrics: \(error)")
    }
}
```
{: codeblock}

Sobald Ihre Anwendung ausgeführt wird, können Sie über den Endpunkt
`/swiftmetrics-dash` auf das Dashboard zugreifen.

Der Code für `SwiftMetricsPrometheus` stellt standardmäßig den [Prometheus-Endpunkt](https://prometheus.io/){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link") unter der Adresse `http://<hostname>:<port>/metrics` bereit.
