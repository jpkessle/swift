---

copyright:
  years: 2017, 2019
lastupdated: "2019-06-06"

keywords: cloudant swift, store data swift, dbaas swift, cloudant instance swift, initialize sdk swift, create document swift, read document swift, delete document swift

subcollection: swift

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Dokumente in {{site.data.keyword.cloud_notm}} speichern
{: #cloudant}

{{site.data.keyword.cloudantfull}} ist ein
dokumentorientiertes Produkt der Kategorie "DataBase as a Service" (DBaas). Es speichert Daten als Dokumente im JSON-Format. Es wurde im Hinblick
auf Skalierbarkeit, Hochverfügbarkeit und Dauerhaftigkeit konzipiert und kann
problemlos für den Einsatz in Swift-Anwendungen konfiguriert werden. Bereits im
Lieferzustand enthält es eine breite Vielzahl von Indexierungsoptionen, zu
denen MapReduce, {{site.data.keyword.cloudant_short_notm}} Query,
Volltextindexierung und georäumliche Indexierung gehören. Dank seiner
Replikationsfunktionen ist es denkbar einfach, Daten zwischen
Datenbankclustern, Desktop-PCs und Mobilgeräten synchron zu halten. 
{: shortdesc}

Informationen zu allen Möglichkeiten für die Verwendung von {{site.data.keyword.cloudant_short_notm}} finden Sie in den [{{site.data.keyword.cloudant_short_notm}}-Basisinformationen](/docs/services/Cloudant/basics?topic=cloudant-ibm-cloudant-basics#cloudant-nosql-db-basics).

## Vorbereitende Schritte
{: #prereqs-cloudant}

Stellen Sie zunächst sicher, dass die folgenden Voraussetzungen gegeben
sind:
 * CocoaPods (Version 1.1.0 oder höher)
 * iOS (Version 9 oder höher)
 * MacOS (Version 10.11.5 oder höher)
 * Xcode (Version 9.0.1 oder höher)

Das [{{site.data.keyword.cloudant_short_notm}}-SDK für Swift](https://github.com/cloudant/swift-cloudant){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link") wurde mit Swift 3.2 erstellt. Falls Sie den Einsatz von
{{site.data.keyword.cloudant_short_notm}} mit Kitura planen, prüfen Sie
die [Bibliothek Kitura-CouchDB ](https://github.com/IBM-Swift/Kitura-CouchDB){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link"), die mit Swift 4.0 erstellt wurde.
{: tip}

## Schritt 1. Instanz von
{{site.data.keyword.cloudant_short_notm}} erstellen
{: #create-instance-cloudant}

Informationen zum Erstellen einer Instanz des Service finden Sie im [Lernprogramm 'IBM Cloudant-Instanz in {{site.data.keyword.cloud_notm}} erstellen'](/docs/services/Cloudant/tutorials?topic=cloudant-creating-an-ibm-cloudant-instance-on-ibm-cloud#creating-an-ibm-cloudant-instance-on-ibm-cloud){:new_window}.

## Schritt 2. SDK installieren
{: #install-sdk-cloudant}

### Swift-SDK für iOS installieren
{: #install-swift-sdk-cloudant}

Das Cloudant-SDK für Swift vereinfacht die Codierung Ihrer App. Das SDK
muss in Ihrem App-Code installiert sein.

1. Öffnen Sie das vorhandene XCode-Projektverzeichnis für die
`Poddatei`.
2. Fügen Sie unter dem Ziel Ihres Projekts (Abschnitt "target") eine
Abhängigkeit für den Pod `SwiftCloudant` hinzu. Stellen Sie
sicher, dass der Befehl `use_frameworks!` ebenfalls wie im
folgenden Beispiel gezeigt unter Ihrem Ziel angegeben ist.
    ```
    target '<yourTarget>' do
      use_frameworks!
        pod 'SwiftCloudant', :git => 'https://github.com/cloudant/swift-cloudant.git'
    end
    ```
    {: screen}

3. Laden Sie die Abhängigkeit `SwiftCloudant` herunter.
    ```
    pod install
    ```
    {: codeblock}

### Serverseitiges Swift-SDK installieren
{: #install-serverside-cloudant}

Zur Verwendung mit Swift Package Manager für die serverseitige
Entwicklung fügen Sie die folgende Zeile zu den Abhängigkeiten in Ihrer Datei
`Package.swift` hinzu:
```swift
.Package(url: "https://github.com/cloudant/swift-cloudant.git")
```
{: codeblock}

## Schritt 3. SDK initialisieren
{: #initialize-cloudant}

Sobald Sie das SDK in Ihrer App initialisiert habe, können Sie
{{site.data.keyword.cloudant_short_notm}} zum Speichern von Daten
nutzen.

1.  Fügen Sie den folgenden Import zu Ihrer Datei
`AppDelegate.swift` oder zur serverseitigen Swift-Datei hinzu.
    ```swift
    import SwiftCloudant
    ```
    {: codeblock}

2. Initialisieren Sie die Verbindung zur Datenbank.
    ```swift
    let cloudantURL = NSURL(string: "https://username.cloudant.com")!
    let client = CouchDBClient(url: cloudantURL, username: "username", password: "password")
    let dbName = "database"
    ```
    {: codeblock}

### Basisoperationen
{: #basic-operations-cloudant}

Die folgenden Basisoperationen veranschaulichen die grundlegenden
Aktionen zum Erstellen, Lesen und Löschen Ihrer Dokumente mithilfe des
initialisierten Clients.

#### Dokument erstellen
```swift
let create = PutDocumentOperation(id: "doc1", body: ["hello": "world"], databaseName: dbName) {(response, httpInfo, error) in
    if let error = error {
        print("Encountered an error while creating a document. Error: \(error)")
    } else {
        print("Created document \(response?["id"]) with revision id \(response?["rev"])")
    }
}
client.add(operation: create)
```
{: codeblock}

#### Dokument lesen
```swift
let read = GetDocumentOperation(id: "doc1", databaseName: dbName) { (response, httpInfo, error) in
    if let error = error {
        print("Encountered an error while reading a document. Error: \(error)")
    } else {
        print("Read document: \(response)")
    }   
}
client.add(operation: read)
```
{: codeblock}

#### Dokument löschen
```swift
let delete = DeleteDocumentOperation(id: "doc1",
    revision: "1-revisionidhere",
    databaseName: dbName) { (response, httpInfo, error) in
    if let error = error {
        print("Encountered an error while deleting a document. Error: \(error)")
    } else {
        print("Document deleted")
    }   
}
client.add(operation: delete)
```
{: codeblock}

## Schritt 4. App testen
{: #cloudant_testing}

Ist alles richtig konfiguriert? Jetzt können Sie testen, ob Sie alles richtig konfiguriert haben.

1. Führen Sie Ihre Anwendung aus; achten Sie dabei darauf, dass die
Initialisierung und die gewünschten Operationen (z. B. die Erstellung eines
Dokuments) gestartet werden.
2. Kehren Sie zu der {{site.data.keyword.cloudant_short_notm}}-Serviceinstanz zurück, die zuvor in Ihrem Web-Browser erstellt wurde, und
öffnen Sie das Service-Dashboard.
3. Wählen Sie die verwendete Datenbank aus. Daraufhin werden die
Dokumente im Dashboard angezeigt.

Gibt es Probleme? Ziehen Sie die [API-Referenz von {{site.data.keyword.cloudant_short_notm}}](/docs/services/Cloudant/api?topic=cloudant-ibm-cloudant-basics#api-reference-overview) zu Rate.

## Nächste Schritte
{: #cloudant_next notoc}

Hervorragend! Sie haben Ihre App mit einer Ebene für die sichere
Datenspeicherung ausgestattet. Nutzen Sie diesen Schwung und probieren
Sie gleich eine der folgenden Optionen aus:

* Sehen Sie sich den Quellcode von [{{site.data.keyword.cloudant_short_notm}} SDK for Swift](https://github.com/cloudant/swift-cloudant){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link") an.
* Starter-Kits bieten eine der schnellsten Möglichkeiten zur Nutzung des
Leistungsspektrums von {{site.data.keyword.cloud_notm}}. Das Starter Kit **Infinite Scrolling with Cloudant NoSQL for iOS** veranschaulicht, wie Sie einen `ViewController` so erweitern, dass Daten mit Seitenaufteilung angezeigt
werden. Dieses Muster einer App ist für iOS-Entwickler geläufig und ein sehr
gutes Beispiel für die Darstellung des Leistungsspektrums von {{site.data.keyword.cloudant_short_notm}}. Im [Dashboard für Entwickler für Mobilgeräte](https://{DomainName}/developer/mobile/dashboard){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link") werden die verfügbaren Starter-Kits angezeigt. Laden Sie den Code herunter. Führen Sie die App aus!
* Lesen Sie weitere Informationen zu {{site.data.keyword.cloudant_short_notm}} in der [Dokumentation](/docs/services/Cloudant?topic=cloudant-ibm-cloudant-basics). Dort erfahren Sie auch, wie Sie alle gebotenen Funktionen nutzen können.
