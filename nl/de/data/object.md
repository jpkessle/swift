---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-07"

keywords: object storage swift, static storage swift, file services swift, swift storage class, cos swift, swift data encryption, static swift

subcollection: swift

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Object Storage für statischen Inhalt verwenden
{: #object-storage}

Object Storage ist eine der Basiskomponenten für das Cloud-Computing und
stellt leistungsfähige Funktionen für Apple-Entwickler und deren Anwendungen
bereit. Anders als bei der Speicherung von Informationen in einer
Dateihierarchie (z. B. Block- oder Dateispeicher) besteht ein Objektspeicher nur
aus den Dateien und ihren Metadaten, die in als "Buckets"
bezeichneten Objektgruppen gespeichert sind. Diese Objekte sind
definitionsgemäß nicht veränderbar, wodurch sie für Daten wie
Bilder,
Videos und andere statische Dokumente perfekt geeignet sind. Für sich häufig ändernde oder relationale Daten können Sie [Cloudant](/docs/swift/data?topic=swift-cloudant#cloudant)- und [SQL](/docs/swift/data?topic=swift-sql_data#sql_data)-Datenbankservices verwenden.

{{site.data.keyword.cos_full_notm}} (COS) ist ein
flexibles, kosteneffizientes und skalierbares Speichersystem für
unstrukturierte Daten. Auf die Daten kann über SDKs oder die IBM
Benutzerschnittstelle zugegriffen werden. Mit
{{site.data.keyword.cos_full_notm}} können Sie über ein
Self-Service-Portal, das sich auf REST-konforme APIs und SDKs stützt, auf Ihre
unstrukturierten Daten zugreifen. 

Je nach bestehendem Bedarf können Sie
{{site.data.keyword.cos_short}} für die folgenden Services nutzen:

* Repository für Inhaltsdaten
* Sicherung und Wiederherstellung
* Datenarchivierung
* Dateiservices
* Datenübertragung

Beim Erstellen eines Buckets müssen Sie eine Ebene für die
Ausfallsicherheit auswählen (regionsübergreifend oder regional). Auf der
Grundlage Ihrer Auswahl werden Ihre Daten verteilt und an mehreren Standorten
gespeichert.

## API
{: #api-cos}

Die {{site.data.keyword.cos_full}}-API ist eine REST-basierte API
für das Lesen und Schreiben von Objekten. Sie unterstützt eine Untergruppe der
S3-API für die einfache Migration von Anwendungen auf
{{site.data.keyword.cloud_notm}}. Zur Nutzung von
{{site.data.keyword.cos_full}} kann jedes beliebige S3-SDK verwendet
werden. Weitere Informationen finden Sie in der vollständigen [{{site.data.keyword.cos_short}}-API-Referenz](/docs/services/cloud-object-storage?topic=cloud-object-storage-compatibility-api).

## Objektspeicher sichern
{: #security-cos}

{{site.data.keyword.cos_full_notm}} ist äußerst sicher. Anfangs
haben nur die Bucket- und Objekteigner Zugriff auf den
{{site.data.keyword.cos_full_notm}}-Service, den sie erstellen. Außerdem
wird für den Zugriff auf die Daten die Benutzerauthentifizierung unterstützt. Mithilfe
von Zugriffssteuerungsmechanismen wie Bucketrichtlinien können Sie Benutzern
und Anwendungen selektiv Berechtigungen erteilen. Ihre Daten können Sie mit
dem HTTPS-Protokoll über SSL-Endpunkte sicher an
{{site.data.keyword.cos_short}} übertragen. Falls Sie zusätzliche
Sicherheit benötigen, können Sie mit der Option "SSE" (Server-Side Encryption,
serverseitige Verschlüsselung) oder der Option "SSE-C" (Server-Side Encryption
with Customer-Provided Keys, serverseitige Verschlüsselung mit vom Kunden
bereitgestellten Schlüsseln) ruhende Daten verschlüsseln. {{site.data.keyword.cos_full_notm}} stellt die
Verschlüsselungstechnologie für SSE und SSE-C bereit. Alternativ können Sie
Ihre eigenen Verschlüsselungsbibliotheken verwenden, um Daten zu verschlüsseln,
bevor sie in Cloud Object Storage gespeichert werden.

Zum Schutz Ihrer Daten in
{{site.data.keyword.cos_full_notm}} können Sie die folgenden
Zugriffssteuerungsmechanismen verwenden.

### IAM-Richtlinien (IAM = Identity and Access Management)
{: #iam-cos}

IAM ermöglicht es Unternehmen mit mehreren Mitarbeitern, unter einem einzigen Konto mehrere Benutzer zu erstellen und zu verwalten. Mit
IAM-Richtlinien können Unternehmen den IAM-Benutzern die Steuerung ihrer
{{site.data.keyword.cos_short}}-Buckets gewähren.

### Zugriffssteuerungslisten (Access Control Lists, ACLs)
{: #acls-cos}

Mithilfe von ACLs können Sie bestimmten Benutzern für ein einzelnes Bucket spezielle Berechtigungen (z. B. READ oder WRITE) erteilen.

Ruhende Daten werden auf Providerseite automatisch mit der
256-Bit-Verschlüsselung gemäß Advanced
Encryption Standard (AES) und 256-Hash gemäß Secure Hash Algorithm
(SHA) verschlüsselt. Bewegte Daten werden durch die Verwendung der integrierten
Netzbetreiberstufe TLS (Transport Layer Security), SSL (Secure Sockets Layer)
oder SNMPv3 mit AES-Verschlüsselung geschützt.

### Verschlüsselung
{: #encryption-cos}

Ruhende Daten werden auf Providerseite automatisch mit der
256-Bit-Verschlüsselung gemäß Advanced
Encryption Standard (AES) und 256-Hash gemäß Secure Hash Algorithm
(SHA) verschlüsselt. Bewegte Daten werden durch die
Verwendung der integrierten Netzbetreiberstufe TLS/SSL (Transport Layer
Security/Secure Sockets Layer) oder SNMPv3 mit
AES-Verschlüsselung geschützt.

Die serverseitige Verschlüsselung ist für Kundendaten jederzeit
aktiviert. Verglichen mit dem für die Authentifizierung erforderlichen
Hashverfahren und der Löschungscodierung trägt die Verschlüsselung nur
unwesentlich zu den Verarbeitungskosten von Cloud Object Storage bei.

Da SSE-C in
{{site.data.keyword.cos_full_notm}} unterstützt wird, können Sie Ihren
eigenen Schlüssel für die Verschlüsselung bereitstellen. Die Verwaltung und
Bereitstellung des Schlüssels zum Speichern und Abrufen der Daten liegt jedoch
in Ihrer Verantwortung.

## Ausfallsicherheit
{: #resiliency-cos}

Beim Erstellen eines Buckets müssen Sie eine Ebene für die
Ausfallsicherheit auswählen (regionsübergreifend oder regional). Auf der
Grundlage Ihrer Auswahl werden Ihre Daten verteilt und an mehreren Standorten
gespeichert.

Die regionale Ausfallsicherheit ist für geringe Latenzzeiten gedacht;
Ihre Daten werden hierbei auf drei Zentren in einer einzigen Region verteilt. Verwenden Sie die Ausfallsicherheit über Regionen hinweg für kritische Verfügbarkeit, da Ihre Daten dann in drei oder mehr unterschiedlichen Regionen gespeichert werden. Die Ausfallsicherheit über Regionen hinweg bietet geografische Mehrfachverfügbarkeit und ist über mehrere Endpunkte hinweg verfügbar. Richten Sie sich
bei Ihrer Entscheidung für eine der beiden Optionen nach Ihren
Anwendungsanforderungen.

### Ländergruppen
{: #geographies-cos}

{{site.data.keyword.cos_full_notm}} kann überall auf der Welt
genutzt werden. Bei der Bucketerstellung können Sie auswählen, wo Ihre Daten
gespeichert werden sollen. Die in IBM COS gespeicherten Daten werden
verschlüsselt und mithilfe der von IBM Object Storage System bereitgestellten
Technologien für dezentralen Speicher auf mehrere Standorte verteilt. 

Berücksichtigen Sie bei der Entscheidung zwischen der regionalen und
der regionsübergreifenden Option bei der Auswahl des Standortes für Ihren
Objektspeicher die folgenden Faktoren.

Hinweise zum geografischen Standort
* Nutzen Sie zur Redundanz einen Standort, der sich fern von Ihrem
Betrieb befindet.
* Nutzen Sie einen Standort für rechtliche und gesetzliche Bestimmungen.
* Sorgen Sie für geringere Latenzzeiten beim Datenzugriff.
* Berücksichtigen Sie die Preismodelle.

## Anwendungsfälle und Speicherklassen
{: #usecases-cos}

Abhängig von Ihrem Anwendungsfall können Sie Kosten reduzieren, indem
Sie einen Serviceplan auswählen, der Ihren Anforderungen entspricht. Archivierungsoperationen,
die nur einen minimalen Zugriff auf den Objektspeicher beinhalten, benötigen
nicht die Geschwindigkeit und Dauerhaftigkeit eines Objekts, auf das häufig
zugegriffen wird. Diese Unterscheidung schlägt sich in der
Speicherklassenunterstützung
und dem Preistarif für Ihre Anwendungen nieder. Speicherklassen werden auf
Bucketebene definiert, so dass Sie eine Kombination aus Plänen verwenden
können, die Ihren Anforderungen entspricht. Erstellen Sie einen Bucket, der für die Speicherklasse definiert ist, die Sie verwenden möchten.

Weitere Informationen zur Preisstruktur finden Sie in der Dokumentation [Speicherklassen von {{site.data.keyword.cos_short}}](/docs/services/cloud-object-storage/help?topic=cloud-object-storage-billing#ibm-cos-pricing).

### Beispiele für Speicherklassen
{: #samples-cos}

- Standard
  
  Dieser Service ist für unstrukturierte Daten konzipiert, auf die häufig zugegriffen werden muss, beispielsweise DevOps-Daten sowie Repositorys für die Onlinezusammenarbeit und für Aktionsinhalt.

- Vault
  
  Dieser Service ist für Workloads mit Daten konzipiert, auf die selten zugegriffen wird, beispielsweise zur Sicherung, Archivierung und Einhaltung von Vorschriften.

- Cold Vault
  
  Diese Bereitstellungsoption eignet sich hervorragend für Mindestzugriffsanforderungen, die Einhaltung der Protokollaufzeichnung und die langfristige Sicherung.

- Flex

  Diese Bereitstellung ist auf variable Datenzugriffsanforderungen abgestimmt und schützt Ihr Budget vor unerwarteten Kostenschwankungen. Speicherklassen werden auf Bucketebene definiert. Erstellen Sie einen Bucket, der für die Speicherklasse definiert ist, die Sie verwenden möchten.


