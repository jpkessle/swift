---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-12"

keywords: reduce cost swift, serverless swift, openwhisk swift, functions swift, faas swift, stateless swift, api reference swift, high availability swift, serverless ios

subcollection: swift

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:note: .deprecated}
{:tip: .tip}

# Serverunabhängige Entwicklung
{: #serverless-dev-swift}

Was bedeutet "serverunabhängig"? Das serverunabhängige Entwicklungsmuster
nimmt auf eine Anwendungsentwicklung Bezug, bei der die serverseitige Logik in
statusunabhängigen Containern ausgeführt wird. Die Container sind ereignisgesteuert sowie
ephemer (also nur für eine einzige Ausführung bestehend) und werden vollständig
durch einen Dritten verwaltet. Bei diesem Konzept, das auch "Functions
as a Service" (FaaS) genannt wird, stellt der Entwickler eine statusunabhängige
Funktion bereit, die ohne die explizite Erstellung oder Verwaltung eines
Servers ausgelöst und ausgeführt werden kann.

Durch die Ausgliederung der für die serverseitige Entwicklung erforderlichen Infrastruktur und Frameworks können sich Anwendungsentwickler bei der serverunabhängigen Architektur ganz darauf konzentrieren, Code zu schreiben, der zur Änderung von Daten reaktiv ausgeführt wird.

[{{site.data.keyword.openwhisk}}](https://{DomainName}/openwhisk){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link"), das IBM Produktangebot für FaaS, soll eine einfache, serverseitige Entwicklungsumgebung bereitstellen, die kein Fachwissen zur Serverseite erfordert. Bei Verwendung der serverunabhängigen Technologie können
Sie zeitnah erweiterbare Back-End-Lösungen für praktisch jeden Workloadbedarf
entwickeln, ohne vorzeitig Ressourcen erstellen zu müssen. Für Anwendungen mit unvorhersehbaren Auslastungsmustern oder hohen Serverausfallzeiten kann {{site.data.keyword.openwhisk_short}} eine ausgezeichnete Cloudlösung mit verbesserter Leistung sein und dank seiner nutzungsabhängigen Gebührenstruktur zur Kosteneinsparung beitragen.

## Architekturänderungen
{: #comparison-serverless}

Um Ihnen die Vorteile zu veranschaulichen, die sich hinsichtlich der
Architektur durch einen Umstieg auf FaaS ergeben, werden anhand einer einfachen
iOS-Anwendung, die mit einer Datenbank verknüpft ist, eine konventionelle und
eine FaaS-Architektur miteinander verglichen.

In einer eher konventionellen Architektur lagert die iOS-Anwendung
netzintensive Tasks aus oder verarbeitet Daten über Fernzugriff auf einem
zentralisierten Server, der wiederum mit eigenen Services und Speicheroptionen
verbunden ist. Ein Großteil der
Auslastung findet auf einem singulären Server statt, der viele
verarbeitungsintensive Tasks abwickelt, um die Belastung auf dem Client zu
minimieren und die Synchronisierung für die gesamte Benutzerbasis
sicherzustellen.

Eine serverunabhängige Architektur kann diese Struktur so ändern, dass
der Zustand mehr der Darstellung in der folgenden Abbildung entspricht.

![Serverunabhängige Architektur](./images/Architecture.png "Serverunabhängige Architektur")

Statt die gesamte Verarbeitungs- und Authentifizierungslogik in einem
einzigen Server zu verarbeiten, verwendet eine serverunabhängige Architektur Funktionen, die einen Großteil der serverseitigen Logik
einkapseln und bestimmte Logik auf den Client (und in externe Services) auslagern.

Aus der schematischen Darstellung sind die folgenden Aspekte ersichtlich:

1. Der Client authentifiziert sich bei einem Identitätsprovider wie dem
Service "App ID".
2. Unter Einbeziehung des Zugriffstokens ruft der Client die API für das
FaaS-Back-End auf.
3. Das Back-End ist mit {{site.data.keyword.openwhisk_short}} implementiert. Die
als Web-Aktionen zugänglich gemachten serverunabhängigen Aktionen erwarten, dass Tokens im Anforderungsheader gesendet werden, um die Gültigkeit (Signatur und Ablaufdatum) zu prüfen und den Zugriff auf die eigentliche API zu ermöglichen.
4. Sobald der Client Daten übergibt, wird die Rückmeldung in {{site.data.keyword.cloudant_short_notm}} gespeichert.
5. Der Text der Rückmeldung wird mithilfe von
{{site.data.keyword.toneanalyzershort}} verarbeitet.
6. Abhängig vom Analyseergebnis wird eine Benachrichtigung durch {{site.data.keyword.mobilepushshort}} zurück an den Client gesendet.
7. Der Client empfängt die Benachrichtigung.

In einem rein serverunabhängigen Modell übernimmt der Client häufig zusätzliche Aufgaben, weil der Benutzerstatus nicht gespeichert werden kann. Die Autorisierung wird vom Client und dem Identitätsprovider-Service "{{site.data.keyword.appid_short_notm}}"
abgewickelt.

Serverunabhängige Architekturen sind zwar nicht immer ideal, können jedoch bei den richtigen Team- und Nutzungsbedingungen große Vorteile bieten. Über einige der häufigsten [Anwendungsfälle](#use_cases)
können Sie sich anhand einiger spezieller Beispiele informieren.

## Vorteile der Serverunabhängigkeit
{: #benefits-serverless}

### Verringerte Kosten
{: #reduced-cost-serverless}

Das Outsourcing von Zeit und Kosten, die mit der Systemadministration
einhergehen, verringert die Gesamtkosten, die mit konventionellen
Back-End-Servern verbunden sind. Darüber hinaus unterscheidet sich
{{site.data.keyword.openwhisk_short}} von den herkömmlichen
Datenverarbeitungstechnologien, da Sie nur für die Zeit bezahlen, die Ihr Code
benötigt, um Anforderungen zu erfüllen (aufgerundet auf die nächsten 100
Millisekunden). Dies ermöglicht im Vergleich zu anderen Technologien wie
virtuellen Maschinen und Containern, die wahrscheinlich nicht zu
100 % ausgelastet sind und Speicher im System Ihres Cloud-Providers belegen,
deutliche Kosteneinsparungen.

### Hochverfügbarkeit und Skalierbarkeit
{: #ha-serverless}

Serverunabhängige Architekturen bieten eine sofortige Skalierbarkeit mit nahezu konstanter Verfügbarkeit.

### Geschwindigkeit und vereinfachte Entwicklung
{: #speed-serverless}

Das serverunabhängige Konzept beschleunigt die Anwendungsentwicklung,
weil keine Systemadministration mehr erforderlich ist und einfache
Schnittstellen für die Entwicklung bereitstehen. Entwickler können zügig Apps
mit Aktionsfolgen erstellen, die als Reaktion auf eine ereignisgesteuerte
Realität ausgeführt werden.

## Beispielanwendungsfälle
{: #use-cases-serverless}

### Mobiles Back-End
{: #mobile-backend-serverless}
![Mobiles Back-End](./images/cloud-functions-rest-api-trigger.png "Mobiles Back-End")

Entwickler für Mobilgeräte können leicht auf serverseitige Logik zugreifen und rechenintensive Tasks auf eine Cloudplattform auslagern. Unter Verwendung des iOS-SDK und ohne serverseitige Erfahrungen
besitzen zu müssen, können Sie Funktionen in Sprachen
wie Swift implementieren und problemlos serverseitige Funktionen nutzen.

### Datenverarbeitung
{: #data-processing-serverless}

![Serverunabhängige Datenverarbeitung](./images/cloud-functions-cloudant-trigger.png "Serverunabhängige Datenverarbeitung")

Sie können Code immer dann ausführen, wenn Daten in Ihrem Datenspeicher über integrierte Auslöser aktualisiert werden. Außerdem können Sie
durch ein funktionales Modell für die serverseitige Programmierung Prozesse
wie Klangnormalisierung, Grafikdrehung, Geräuschreduzierung,
Piktogrammgenerierung oder Videotranscodierung ohne Weiteres automatisieren.

### Kognitive Datenverarbeitung
{: #cognitive-serverless}

Sie können Daten analysieren, sobald sie verfügbar sind. Binden Sie
leistungsfähige kognitive Services wie IBM Watson in Ihre Funktionalität ein,
um Objekte oder Personen in Bildern bzw. Videos zu erkennen.

### Geplante Tasks
{: #tasks-serverless}

Sie können Funktionen regelmäßig ausführen lassen und Zeitpläne
definieren, die den Zeitpunkt für die Ausführung von Aktionen mit einer
Cron-ähnlichen Syntax angeben.

## API-Referenz
{: #apiref-serverless notoc}

<!-- * [REST API Documentation](./openwhisk_reference.html#openwhisk_ref_restapi)-->
* [REST-API](https://{DomainName}/apidocs){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")

## Zugehörige Links
{: #related-serverless notoc}

* [Entdecken Sie {{site.data.keyword.openwhisk_short}}](https://www.ibm.com/cloud/functions){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")
<!-- redirects to link above * [{{site.data.keyword.openwhisk_short}} on IBM developerWorks](https://developer.ibm.com/openwhisk/)-->
* [Apache OpenWhisk-Projektwebsite](http://openwhisk.incubator.apache.org/){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")
* [Weitere Informationen zur Serverunabhängigkeit](https://martinfowler.com/articles/serverless.html){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link")
