---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-10"

keywords: swift logging, ios logging, debug swift, add logging swift, heliumlogger swift, loggerapi swift, logger swift, starter kit swift logger

subcollection: swift

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Protokollierung in Swift
{: #logging_swift}

Protokollnachrichten sind Zeichenfolgen mit Kontextinformationen über den Status und die Aktivität des Mikroservice zum Zeitpunkt des Protokolleintrags. Protokolle werden benötigt, um zu diagnostizieren, wie und warum
Services fehlschlagen. Sie spielen ferner eine unterstützende Rolle für [App-Metriken](/docs/swift/cloudnative?topic=swift-metrics#metrics) bei der Überwachung des Anwendungsstatus.

Prozesse in Cloudumgebungen sind naturgemäß transient, weshalb erfasste Protokolle an eine andere Position gesendet werden müssen, bei der es sich in der Regel um eine zentrale Analysestelle handelt. Das konsistenteste Verfahren für die
Protokollierung in Cloudumgebungen besteht darin, Protokolleinträge an
Standardausgabe und Fehlerdatenströme zu senden und die weitere Verarbeitung
der Infrastruktur zu überlassen.

## Protokollierung zu einer Swift-App hinzufügen
{: #logging-add}

[HeliumLogger](https://github.com/IBM-Swift/HeliumLogger){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link") ist ein gängiges einfaches Protokollierungsframework für Swift und bietet viele native Vorteile wie beispielsweise die Protokollierung in der Standardausgabe und mit verschiedenen Protokollebenen.

[LoggerAPI](https://github.com/IBM-Swift/LoggerAPI){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link") ist das Protokollierungsprotokoll, das für verschiedene Arten von Protokollfunktionen in Swift eine gemeinsame Protokollierungsschnittstelle bereitstellt. Kitura verwendet `LoggerAPI`
während seiner Implementierung.

Um `HeliumLogger` zu verwenden, fügen Sie den folgenden Code für alle entsprechenden Ziele zum Abschnitt **dependencies:** in `Package.swift` hinzu:
```swift
.package(url: "https://github.com/IBM-Swift/HeliumLogger.git", from: "1.7.1")
```
{: codeblock}

Fügen Sie den folgenden Instrumentierungscode hinzu, um
`HeliumLogger` zu initialisieren und als die von
`LoggerAPI` verwendete Protokollfunktion festzulegen:
```swift
import HeliumLogger
import LoggerAPI

let logger = HeliumLogger(.verbose)
Log.logger = logger

Log.info("This is an informational log message.")
```
{: codeblock}

Im genannten Beispiel war als [Protokollebene](http://ibm-swift.github.io/HeliumLogger/){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link") explizit `.verbose` festgelegt, was die Standardeinstellung ist.

Weitere Informationen zum Anpassen von Protokollnachrichten finden Sie in der offiziellen [Referenzdokumentation zur API 'HeliumLogger'](http://ibm-swift.github.io/HeliumLogger/){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link").

## Protokollierung mit Starter-Kits
{: #logging-starterkits}

Swift-Apps, die mit dem App-Service von
{{site.data.keyword.cloud_notm}} erstellt werden, enthalten
`HeliumLogger` standardmäßig. Bei der nativen Ausführung der
App oder ihrer Ausführung in einer Cloudumgebung wird die folgende Ausgabe
erzeugt:
```
[2018-07-31T15:41:05.332-05:00] [INFO] [HTTPServer.swift:195 listen(on:)] Listening on port 8080.
```
{: screen}

Diese Nachrichten befinden sich lokal in `stdout` (Standardausgabe) oder in den Protokollen für Cloud Foundry- und Kubernetes-Bereitstellungen, auf die mit `[ibmcloud app logs --recent <APP_NAME>]`(/docs/cli/reference?topic=cloud-cli-ibmcloud_commands_apps#ibmcloud_app_logs) und `[kubectl logs <deployment name>`](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link") zugegriffen wird.

In der Datei `/Sources/AppName/main.swift` ist der
folgende Code enthalten:
```swift
HeliumLogger.use(LoggerMessageType.info)
```
{: codeblock}

Als Protokollebene ist explizit `.info` angegeben, damit
Informationsnachrichten wie beispielsweise Protokolle über den Anwendungsstart
protokolliert werden.
{: tip}

## Nächste Schritte
{: #next-logging notoc}

Erfahren Sie mehr über das Anzeigen von Protokollen in Ihren einzelnen Bereitstellungszielen:
* [Kubernetes-Protokolle](https://kubernetes.io/docs/concepts/cluster-administration/logging/#basic-logging-in-kubernetes){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")
* [Cloud
Foundry-Protokolle](/docs/cli/reference/ibmcloud?topic=cloud-cli-ibmcloud_cli#ibmcloud_cli)
* [Cloud Foundry Enterprise Environment - Prüfung und Protokollierung](/docs/cloud-foundry?topic=cloud-foundry-auditing-logging#auditing-logging)
* [Protokolle & Überwachung in {{site.data.keyword.openwhisk}}](/docs/openwhisk?topic=cloud-functions-logs)

Hier erfahren Sie, wie Sie einen Protokollaggregator implementieren und verwenden:
* [{{site.data.keyword.cloud_notm}}-Protokollanalyse](/docs/services/CloudLogAnalysis?topic=cloudloganalysis-log_analysis_ov#log_analysis_ov)
* [Privater {{site.data.keyword.cloud_notm}}-ELK-Stack](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.2/manage_metrics/logging_elk.html){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")
