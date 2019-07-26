---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-12"

keywords: swift database, secure database swift, cluster database swift, mongokitten swift, verify database swift, credentials swift, storage api swift

subcollection: swift

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:gif: data-image-type='gif'}
{:tip: .tip}

# Hoch verfügbare und sichere Datenbank erstellen
{: #create-database-cluster}

Damit Sie eine hoch verfügbare und sichere Datenbank optimal nutzen
können, müssen Sie zusätzliche Logik in Ihre Anwendung integrieren. Mithilfe
der bereitgestellten Code-Snippets können Sie eine MongoDB-Datenbank erstellen
und auf diese Datenbank zugreifen. 

Gegenwärtig wird zur Verwendung von
{{site.data.keyword.ihsdbaas_full}} als Programmiersprache Swift 4.0
mit MongoKitten SDK 4.0.0 unterstützt.

## Schritt 1. Datenbankcluster erstellen
{: #create_dbcluster}

1. Rufen Sie die Anzeige [{{site.data.keyword.ihsdbaas_full}}-Servicekonfiguration](https://{DomainName}/catalog/services/hyper-protect-dbaas){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link") auf.

2. Geben Sie die folgenden Informationen an:

	<dl>
		<dt>Servicename:</dt>
		<dd>Der Name für den Datenbankservice.</dd>

    <dt>Region/Standort für die Bereitstellung auswählen:</dt>
    <dd>Wählen Sie das Rechenzentrum aus, in dem sich die Datenbank befinden soll.</dd>

    <dt>Ressourcengruppe auswählen:</dt>
    <dd>Falls keine Ressourcengruppe auswählbar ist, können Sie im IBM Cloud-Dashboard eine Ressourcengruppe erstellen.</dd>

		<dt>Cluster-/Replikatgruppenname:</dt>
		<dd>Der Name für den Datenbankcluster.</dd>

		<dt>Name des Datenbankadministrators:</dt>
		<dd>Eine Benutzer-ID für den Datenbankadministrator (DBA).</dd>

		<dt>Kennwort des Datenbankadministrators:</dt>
		<dd>Das Kennwort für die Benutzer-ID des DBA.</dd>

    <dt>Kennwort des Datenbankadministrators bestätigen:</dt>
    <dd>Bestätigen Sie das Kennwort für die Benutzer-ID des DBA.</dd>

		<dt>Datenbanktyp:</dt>
		<dd>Wählen Sie den Datenbanktyp aus. Gegenwärtig wird ausschließlich MongoDB unterstützt.</dd>

    <dt>Lizenzvereinbarung:</dt>
    <dd>Lesen Sie die Lizenzvereinbarung und wählen Sie das Kontrollkästchen aus, um Ihre Zustimmung zu bestätigen.</dd>

    <dt>Preisstruktur:</dt>
		<dd>Die aktuelle Lösung bietet nur eine einzige - gebührenfreie - Kategorie in der Preisstruktur. In späteren Versionen können Sie die Preiskategorie auswählen.</dd>
	</dl>

3. Klicken Sie auf **Erstellen**.

  Das {{site.data.keyword.cloud_notm}}-Dashboard wird angezeigt. Möglicherweise müssen Sie Ihren Browser aktualisieren, damit der neue Cluster
angezeigt wird, der im Abschnitt "Services" aufgelistet ist.</p></li>

4. Wenn Sie den Service auswählen, werden die Clusterinformationen angezeigt.

5. Klicken Sie auf der Registerkarte "Verwalten" der
Clusterinformationen auf **Öffnen**.

	Das {{site.data.keyword.ihsdbaas_full}}-Dashboard wird
angezeigt.

6. Rufen Sie die Hostnamen und die Portnummern der drei erstellten Datenbankinstanzen ab, die zu Ihrem Datenbankcluster gehören. Sie benötigen die
Hostnamen, die Portnummern und die Benutzerberechtigungen für die Schritte im
Abschnitt [Verbindung zur Datenbank herstellen](#connect_db).

## Schritt 2. Projekt mithilfe eines Starter-Kits erstellen
{: #create_starter}

Sie benötigen ein Starter-Kit, das auf dem serverseitigen
Swift-Web-Framework "Kitura" basiert.

![Featuredetails](videos/StarterKit.gif){: gif}

Verwenden Sie ein vorhandenes Projekt, das aus diesem Starter-Kit erstellt wurde, oder erstellen Sie ein neues Projekt.

1. Öffnen Sie das [{{site.data.keyword.cloud_notm}}-Dashboard für App-Services](https://{DomainName}/developer/appservice/dashboard){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link").

2. Wählen Sie die Registerkarte **Starter-Kits** aus.

3. Wählen Sie das BFF-Starter-Kit **Swift
Kitura** aus (BFF = Backend for
Frontend). Verwechseln Sie es nicht mit dem Basis-Starter-Kit von Swift Kitura
für Web-Apps.
  Die Seite "Neues Projekt erstellen" wird angezeigt.

4. Geben Sie die Projektdetails ein und klicken Sie auf **Projekt erstellen**.
  Die
Projektseite wird angezeigt.

5. Klicken Sie auf der Projektseite auf **Code herunterladen**.

6. Erweitern Sie die komprimierte Datei in Ihrem Projektverzeichnis.

## Schritt 3. Verbindung zur Datenbank herstellen
{: #connect_db}

Um die Sicherheit der Datenübertragung zu erhöhen, laden Sie die Datei der Zertifizierungsstelle herunter und kopieren Sie sie in Ihr Projektverzeichnis.

1. Wechseln Sie in Ihr Projektverzeichnis, das die dekomprimierten
Codedateien des Downloads enthält.

2. Erstellen Sie eine JSON-Datei namens `cred.json`, um
Ihre Zugriffsberechtigungsnachweise für den Datenbankcluster zu speichern.

3. Geben Sie die Werte ein, die Sie in den Schritten im Abschnitt
[Datenbankcluster erstellen](/docs/swift?topic=swift-create-database-cluster#create_dbcluster) gesammelt haben. Die Werte müssen in einer einzigen Zeile angegeben werden.
  ```hljs
  {
  "uri": "mongodb://<admin_ID>:<admin_pwd>@<Hostname_1>:<PortNumber_1>,
  <Hostname_2>:<PortNumber_2>,<Hostname_3>:<PortNumber_3>
   /admin?ssl=true&ssl_ca_certs=/swift-project/<CA_file>"
  }
  ```
  {: codeblock}

### Beschreibungen der Datenbankparameter
{: #db-parameter-descriptions}

Beachten Sie die folgenden Beschreibungen der Datenbankparameter:

* `admin_ID` - Die Benutzer-ID des Datenbankadministrators, die in [Datenbankcluster erstellen](/docs/swift?topic=swift-create-database-cluster#create_dbcluster) angegeben wurde.
* `admin_pwd` - Das Kennwort der Benutzer-ID des Administrators, das in [Datenbankcluster erstellen](/docs/swift?topic=swift-create-database-cluster#create_dbcluster) angegeben wurde.
* `Hostname_i` - Ein Datenbankreplikat *i* (*i*=1, 2, 3), das in [Datenbankcluster erstellen](/docs/swift?topic=swift-create-database-cluster#create_dbcluster) zurückgegeben wurde.
* `PortNumber_i` - Eine Portnummer *i* (*i*=1, 2, 3), die in [Datenbankcluster erstellen](/docs/swift?topic=swift-create-database-cluster#create_dbcluster) zurückgegeben wurde.
* `CA_file` - Der Dateiname der heruntergeladenen Datei der Zertifizierungsstelle. Während der Bereitstellung wird diese Datei in das Verzeichnis `/swift-project` kopiert.

4. Bearbeiten Sie die Datei `Package.swift` und fügen
Sie Paketabhängigkeiten für die Verwendung des
MongoKitten-SDK hinzu.

  * Fügen Sie im Abschnitt "dependencies" die folgende Zeile hinzu:
   ```swift
   .package(url: "https://github.com/OpenKitten/MongoKitten.git", from: "4.0.0"),
   ```
   {: codeblock}

  * Fügen Sie im Abschnitt "targets" die Abhängigkeit "MongoKitten" zur
folgenden Zeile hinzu. **Hinweis:** Die Werte müssen in
einer einzigen Zeile angegeben werden.
   ```swift
   .target(name: "Application", dependencies: [ "Kitura",
   "CloudEnvironment","SwiftMetrics","Health","MongoKitten", ]),
   ```
   {: codeblock}

5. Bearbeiten Sie
die Datei `Sources/Application/Application.swift`, damit die
Konnektivität zu MongoDB mittels MongoKitten initialisiert wird.

  * Importieren Sie das MongoKitten-SDK:
    ```swift
	  import MongoKitten
	  ```
	  {: codeblock}

  * Fügen Sie die Klasse `ApplicationServices` hinzu:
    ```swift
	  cclass ApplicationServices {
	  /* Service references */
	  public let mongoDBService: MongoKitten.Database
	  public let myCredFile = "/swift-project/cred.json"

    public init() throws {
        /* Read credentials from json file cred.json */
        struct ResponseData: Decodable {
            var uri: String
	        }
	        let data = try? Data(contentsOf: URL(fileURLWithPath: myCredFile))
	        let decoder = JSONDecoder()
	        let jsonData = try decoder.decode(ResponseData.self, from: data!)

        /* Run service initializers */
        let server = try Server(jsonData.uri)
        mongoDBService = MongoKitten.Database(named: "admin", atServer: 		server)
    }
	}
	```
	{: codeblock}

  * Fügen Sie in der öffentlichen Klasse `App` die
folgenden Zeilen hinzu, um die Datenbankverbindung zu initialisieren:
    ```swift
	  public class App {
	  ...
	  let services: ApplicationServices

	  public init() throws {
	  /* Services */
	  services = try ApplicationServices()
	  }
	  ...
    ```
    {: codeblock}

## Schritt 4. Datenbankverbindung überprüfen
{: #verify_database}

1. Überprüfen Sie Ihre Datenbankverbindung, indem Sie die Datei
`Sources/Application/Application.swift` bearbeiten und einen
Befehl zum Testen der Datenbankverbindung hinzufügen.
Fügen Sie beispielsweise
den folgenden Befehl in der
Klasse `ApplicationServices` hinzu:

	```swift
		class ApplicationServices {
		    ...
		    public init() throws {
		        ...
		        let server = try Server(jsonData.uri)
		        mongoDBService = MongoKitten.Database(named: "admin", atServer: server)
		        if server.isConnected {
		            print("Connected to mongodb: " + String(describing: mongoDBService))
		        }
		        ...
		    }
		}
	```
	{: codeblock}

Nachdem Sie Ihre Anwendung in [Schritt
6](#use-step6) bereitgestellt haben, wird die folgende Nachricht ausgegeben, falls
die Verbindung zur Datenbank erfolgreich hergestellt werden konnte:

```
...
Connected to mongodb:
MongoKitten.Database&lt;mongodb:/&sol;&lt;<em>Hostname_1</em>&gt;&colon;&lt;<em>PortNumber_1</em>&gt;,&lt;<em>Hostname_2</em>&gt;&colon;&lt;<em>PortNumber_2</em>&gt;,&lt;<em>Hostname_3</em>&gt;&colon;&lt;<em>PortNumber_3</em>&gt;/admin&gt;
...
```
{: screen}

## Schritt 5. Anwendungscode integrieren
{: #embed_appcode}

Jetzt können Sie Ihren eigenen Anwendungscode zum Projekt hinzufügen. Weitere Informationen finden Sie in der Dokumentation zur [MongoKitten-API](http://beta.openkitten.org/tutorials/){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link").

## Schritt 6. Anwendung bereitstellen
{: #deploy-dbcluster}

Sie können die Anwendung mit den erforderlichen Build-Tools [lokal](/docs/swift?topic=swift-swift_cli#swift-install-tools) ausführen oder in {{site.data.keyword.cloud_notm}} bereitstellen.

Klicken Sie zum Erstellen einer Toolchain für die Bereitstellung auf **Bereitstellen** im Dashboard. Richten Sie Ihr Bereitstellungsziel gemäß den Anweisungen für die von Ihnen gewählte Methode ein:
  * **Bereitstellung in IBM Kubernetes Service**. Mit dieser Option wird ein Cluster mit Hosts erstellt, die als Workerknoten bezeichnet werden, um hoch verfügbare App-Container bereitzustellen und zu verwalten. Sie können einen Cluster erstellen oder die Bereitstellung in einem vorhandenen Cluster vornehmen. Weitere Informationen finden Sie in [Apps in Kubernetes-Clustern bereitstellen](/docs/containers?topic=containers-app).
  * **Bereitstellung in Cloud Foundry**. Mit dieser Option wird die cloudnative App bereitgestellt, ohne dass Sie die zugrunde liegende Infrastruktur verwalten müssen. Wenn Ihr Konto über Zugriff auf {{site.data.keyword.cfee_full_notm}} verfügt, können Sie als Bereitstellertyp entweder **Public Cloud** oder **Enterprise Environment** auswählen, das zum Erstellen und Verwalten isolierter Umgebungen für das exklusive Hosting von Cloud Foundry-Apps für Ihr Unternehmen verwendet werden kann. Weitere Informationen finden Sie in [Apps in Cloud Foundry Public bereitstellen](/docs/cloud-foundry-public?topic=cloud-foundry-public-deployingapps) und [Apps in {{site.data.keyword.cfee_full_notm}} bereitstellen](/docs/cloud-foundry?topic=cloud-foundry-deploy_apps).
  * **Bereitstellung auf einem virtuellen Server**. Mit dieser Option wird eine virtuelle Serverinstanz bereitgestellt, ein Image mit Ihrer App geladen, eine DevOps-Toolchain erstellt und der erste Bereitstellungszyklus initiiert. Weitere Informationen finden Sie in [Apps auf einem virtuellen Server bereitstellen](/docs/vsi?topic=virtual-servers-deploying-to-a-virtual-server).
  
