---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-13"

keywords: swift-cfenv, service bindings swift, environment swift, swift configuration, cloudenvironment swift, VCAP_SERVICES swift, swift credentials

subcollection: swift

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}
{:note: .note}

# Swift-Umgebung konfigurieren
{: #configuration}

Bei der cloudnativen Entwicklung gibt es zwei eng zusammengehörende
Verfahren, die in Bezug auf die Verarbeitung von Konfigurationsdaten
Überschneidungen aufweisen. Erstens sollten Sie nicht veränderbare Artefakte
erstellen, um die Wahrscheinlichkeit zu minimieren, dass sich Fehler
einschleichen, wenn Ihre Anwendung den Schritt von der Entwicklung in die
Produktion vollzieht. Zweitens müssen Sie eine Parität zwischen der
Entwicklungs- und der Produktionsumgebung anstreben, um Probleme zu verhindern,
die durch umgebungsspezifischen Code verursacht werden. 

Die Verwaltung der Servicekonfiguration und der Berechtigungsnachweise
(Servicebindungen) variiert je nach Plattform. Cloud Foundry speichert Details
über die Servicebindung in einem in Zeichenfolge konvertierten JSON-Objekt, das
an die Anwendung als Umgebungsvariable `VCAP_SERVICES`
übergeben wird. Kubernetes speichert Servicebindungen als in Zeichenfolge
konvertierte JSON- oder unstrukturierte Attribute in
Objekten des Typs `ConfigMap` (Konfigurationszuordnung)
oder `Secret` (geheimer Schlüssel), die als Umgebungsvariablen an
die containerisierte Anwendung übergeben oder als temporärer Datenträger
angehängt werden können. Die lokale Entwicklung besitzt eine eigene
Konfiguration, da lokale Tests häufig eine vereinfachte Ableitung dessen sind,
was in der Cloud ausgeführt wird. Die Arbeit mit allen diesen Varianten in
einer portierbaren Weise und ohne die Verwendung umgebungsspezifischer
Codepfade kann eine Herausforderung darstellen.

Es gibt einfache Richtlinien, durch deren Berücksichtigung Sie
portierbare Anwendungen schreiben können, sowie Dienstprogramme, mit
deren Hilfe Sie die Suche nach Servicebindungen (oder anderen
Konfigurationsfakten) in umgebungsspezifische Positionen einbinden können.Unabhängig
davon, ob Sie bestehende Anwendungen mit einer Cloudunterstützung ausstatten
oder Apps mit Starter-Kits erstellen müssen, besteht das Ziel in einer
Portierbarkeit für Swift-Apps, damit diese auf vielen Bereitstellungsplattform verwendet werden können.

## {{site.data.keyword.cloud_notm}} zu vorhandenen
Swift-Anwendungen hinzufügen
{: #addcloud-env}

Der Pfad für die Abstraktion von Umgebungswerten kann von Cloudumgebung zu Cloudumgebung unterschiedlich sein. In der Bibliothek [CloudEnvironment](https://github.com/IBM-Swift/CloudEnvironment){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link") sind die Umgebungskonfigurationen und Berechtigungsnachweise verschiedener Cloud-Anbieter in abstrahierter Form enthalten, sodass Ihre Swift-App bei einer lokalen Ausführung bzw. einer Ausführung in Cloud Foundry, Cloud Foundry Enterprise Environment, Kubernetes, {{site.data.keyword.openwhisk}} oder virtuellen Instanzen konsistent auf die Informationen zugreifen kann. Die Abstraktion der Berechtigungsnachweise wird von der Bibliothek `CloudEnvironment` bereitgestellt, die intern [Swift-cfenv](https://github.com/IBM-Swift/Swift-cfenv){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link") für die Cloud Foundry-Konfiguration und [Configuration](https://github.com/IBM-Swift/Configuration){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link") als Konfigurationsmanager verwendet.

Mithilfe der Bibliothek `CloudEnvironment` können Sie
maschinennahe Details aus dem Anwendungsquellcode abstrahieren, indem Sie einen
Suchschlüssel definieren, der von Ihrer Swift-Anwendung zur Suche nach dem
entsprechenden Wert verwendet werden kann.

Die Bibliothek `CloudEnvironment` bietet einen
konsistenten Suchschlüssel, den Sie im Quellcode verwenden können. Die
Bibliothek sucht dann in einem Bereich von Suchmustern nach einem JSON-Objekt mit
den Konfigurationsattributen oder Serviceberechtigungsnachweisen. 

### Paket für CloudEnvironment zu Ihrer Swift-Anwendung hinzufügen
{: #add-cloudenv}

Um das Paket für `CloudEnvironment` in Ihrer
Swift-Anwendung zu verwenden, geben Sie es im Abschnitt
**dependencies:** der Datei `Package.swift`
an:
```swift
.package(url: "https://github.com/IBM-Swift/CloudEnvironment.git", from: "8.0.0"),
```
{: codeblock}

Anschließend fügen Sie den folgenden Instrumentierungscode zu Ihrer
Anwendung hinzu:
```swift
import CloudEnvironment

let cloudEnv = CloudEnv()
```
{: codeblock}

### Auf Berechtigungsnachweise zugreifen
{: #access-credentials}

Nachdem die Bibliothek `CloudEnvironment` hiermit
initialisiert wurde, können Sie nun wie in den folgenden Beispielen gezeigt auf
Ihre Berechtigungsnachweise zugreifen:
```swift
let cloudantCredentials = cloudEnv.getCloudantCredentials(name: "cloudant-credentials")
// cloudantCredentials.username, cloudantCredentials.password, cloudantCredentials.url, etc.
let objStorageCredentials = cloudEnv.getObjectStorageCredentials(name: "object-storage-credentials")
// objStorageCredentials.username, objStorageCredentials.password, objStorageCredentials.projectID, etc.

let service1Credentials = cloudEnv.getDictionary("service1-credentials")
let service1CredentialsStr = cloudEnv.getString("service1-credentials")

// Get a default PORT and URL
let port = cloudEnv.port
let url = cloudEnv.url
```
{: codeblock}

Dieses Beispiel stellt den Zugriff auf Berechtigungsnachweissätze für Services bereit, die dann zum Initialisieren von Verbindungen zu diesen [unterstützten Services oder einem generischen Wörterverzeichnis](https://github.com/IBM-Swift/CloudEnvironment#supported-services){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link") verwendet werden können. Testen Sie [Swift-cfenv](https://github.com/IBM-Swift/Swift-cfenv#api){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link") für Cloud Foundry-spezifische Konfigurationen und [Details zu Configuration](https://github.com/IBM-Swift/Configuration){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link") für das Laden von Konfigurationsdaten.

## Wissenswertes über Serviceberechtigungsnachweise
{: #service_creds}

Die Bibliothek `CloudEnvironment` verwendet eine Datei
namens `mappings.json`, die sich im
Verzeichnis `config` befindet, um mitzuteilen, wo die
Berechtigungsnachweise für jeden Service gespeichert sind. Die Datei `mappings.json` unterstützt die Suche nach Werten, die die folgenden
drei Suchmustertypen verwenden:
- **`cloudfoundry`** - Dieser
Mustertyp wird für die Suche nach einem Wert in der Umgebungsvariablen für
Cloud Foundry-Services (`VCAP_SERVICES`) verwendet. Weitere Informationen zu Cloud Foundry Enterprise Edition finden Sie in diesem [Lernprogramm zu Einführung](/docs/cloud-foundry?topic=cloud-foundry-getting-started#getting-started).
- **`env`** - Dieser Mustertyp wird
für die Suche nach einem Wert verwendet, der einer Umgebungsvariablen
zugeordnet ist, beispielsweise in Kubernetes oder Functions.
- **`file`** - Dieser Mustertyp wird
für die Suche nach einem Wert in einer JSON-Datei verwendet. Der Pfad muss
relativ zum Stammordner der Swift-Anwendung sein.

Der Bereich der Werte, die einem Suchschlüssel zugeordnet sind, wird in
der Reihenfolge durchsucht, in der die Werte angegeben sind.

Das folgende Beispiel zeigt eine Datei `mappings.json`:
```javascript
{
    "cloudant-credentials": {
        "credentials": {
            "searchPatterns": [
                "cloudfoundry:my-awesome-cloudant-db",
                "env:my_awesome_cloudant_db_credentials",
                "file:localdev/my-awesome-cloudant-db-credentials.json"
            ]
        }
    },
    "object-storage-credentials": {
        "credentials": {
            "searchPatterns": [
                "cloudfoundry:my-awesome-object-storage",
                "env:my_awesome_object_storage_credentials",
                "file:localdev/my-awesome-object-storage-credentials.json"
            ]
        }
    }
}
```
{: codeblock}

In diesem Beispiel sind `cloudant-credentials` und
`object-storage-credentials` die Suchschlüssel, die von
Ihrer Swift-Anwendung für die Suche nach den entsprechenden
Berechtigungsnachweisen verwendet werden. Entsprechend dem Bereich der
Suchmuster sucht der Konfigurationsmanager zuerst nach
`my-awesome-cloudant-db` in `VCAP_SERVICES`,
dann nach `my_awesome_cloudant_db_credentials` als
Umgebungsvariable. Falls diese nicht definiert ist, sucht er anschließend
nach dem Inhalt der Datei
`my-awesome-object-storage-credentials.json` im Ordner
`localdev`. 

Wenn die Anwendung lokal ausgeführt wird, kann sie Berechtigungsnachweise
verwenden, die in einer Datei gespeichert sind, z. B. wie im obigen
Beispiel in der Datei `localdev/my-awesome-object-storage-credentials.json`. In dieser Datei
müssen alle Verbindungsinformationen (z. B. Benutzername, Kennwort und
Hostname) für Services gespeichert sein, auf die Sie lokal zugreifen wollen. 

Aus Sicherheitsgründen sollten Berechtigungsnachweisdateien nicht zu
Repositorys hinzugefügt werden. Im obigen Beispiel wird ein Ordner namens
`localdev` zum Speichern von lokalen Berechtigungsnachweisen
verwendet. Sie müssen daher diesen Ordner zur Datei
`.gitignore` hinzufügen, um eine versehentliche Festschreibung
zu vermeiden. Falls Sie eine Starter-Kit-App verwenden, wird dieser Ordner
automatisch erstellt und ist in der Datei `.gitignore`
vorhanden.

Weitere Informationen zur Datei `mappings.json` enthält
der Abschnitt [Wissenswertes über
Serviceberechtigungsnachweise](#service_creds).

## Swift-Konfigurationsmanager von Starter-Kit-Apps verwenden
{: #configmanager-swift}

Mit [Starter-Kits](https://{DomainName}/developer/appledevelopment/starter-kits){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link") erstellte Swift-Apps werden automatisch mit Berechtigungsnachweisen und Konfigurationen ausgestattet, die für die lokale Ausführung sowie die Ausführung in vielen Cloudbereitstellungszielen benötigt werden, z. B. [Kubernetes](/docs/containers?topic=containers-getting-started), [Cloud Foundry](/docs/cloud-foundry-public?topic=cloud-foundry-public-about-cf), [{{site.data.keyword.cfee_full_notm}}](/docs/cloud-foundry?topic=cloud-foundry-about), [Virtual Server (VSI)](/docs/vsi?topic=virtual-servers-getting-started-tutorial) oder [{{site.data.keyword.openwhisk_short}}](/docs/openwhisk?topic=cloud-functions-getting_started).

  VSI-Bereitstellung ist für einige Starter-Kits verfügbar. Wenn Sie diese Funktion verwenden möchten, rufen Sie das [{{site.data.keyword.cloud_notm}}-Dashboard](https://{DomainName}) auf und klicken Sie auf **App erstellen** in der Kachel **Apps**.
  {: note}

Die
Basiserstellung des Konfigurationsmanagers finden Sie in der Datei
`Sources/Application/Application.swift`. Wenn Sie eine
Starter-Kit-App auf Swift-Basis mit Services erstellen, wird automatisch der
Ordner `config` und die Datei `mappings.json`
erstellt. Falls Sie Ihre App herunterladen, enthält der
Ordner `config`
eine Datei `localdev-config.json`, in der alle
Berechtigungsnachweise für Ihre Services enthalten sind; der Ordner befindet
sich in der Datei `.gitignore`.

## Nächste Schritte
{: #next-configß notoc}

Sehen Sie sich die drei folgenden Bibliotheken an, damit Ihre Anwendungen
selbst Abstraktionen aus ihren Umgebungen definieren können:

* [CloudEnvironment](https://github.com/ibm-developer/ibm-cloud-env){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")
* [Swift-cfenv](https://github.com/IBM-Swift/Swift-cfenv){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")
* [Configuration](https://github.com/IBM-Swift/Configuration){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")
