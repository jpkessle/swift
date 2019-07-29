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

# Creazione di un database protetto e altamente disponibile
{: #create-database-cluster}

Per avvalerti appieno di un database protetto e altamente disponibile, integri della logica supplementare nella tua applicazione. Utilizzando i frammenti di codice forniti, puoi creare e accedere a un database MongoDB. 

Attualmente, il linguaggio di programmazione supportato per l'utilizzo di {{site.data.keyword.ihsdbaas_full}}
è Swift 4.0 con MongoKitten SDK 4.0.0.

## Passo 1. Creazione di un cluster di database
{: #create_dbcluster}

1. Accesso alla schermata di [configurazione del servizio {{site.data.keyword.ihsdbaas_full}}](https://{DomainName}/catalog/services/hyper-protect-dbaas){: new_window} ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno").

2. Fornisci le seguenti informazioni:

	<dl>
		<dt>Nome servizio:</dt>
		<dd>Un nome per il servizio database.</dd>

    <dt>Scegli una regione/ubicazione in cui eseguire la distribuzione:</dt>
    <dd>Seleziona il centro dati in cui si trova il database.</dd>

    <dt>Seleziona un gruppo di risorse:</dt>
    <dd>Se nessun gruppo di risorse è selezionabile, puoi crearne uno nel dashboard IBM Cloud.</dd>

		<dt>Nome cluster/serie di repliche:</dt>
		<dd>Un nome per il cluster di database.</dd>

		<dt>Nome amministratore database:</dt>
		<dd>Un ID utente per l'amministratore del database (DBA).</dd>

		<dt>Password amministratore database:</dt>
		<dd>Una password per l'ID utente DBA.</dd>

    <dt>Conferma password amministratore database:</dt>
    <dd>Conferma la password per l'ID utente DBA.</dd>

		<dt>Tipo di database:</dt>
		<dd>Seleziona il tipo di database. Attualmente è supportato solo MongoDB.</dd>

    <dt>Accordo di licenza:</dt>
    <dd>Leggi l'accordo di licenza e seleziona la casella per confermare che lo accetti.</dd>

    <dt>Prezzi:</dt>
		<dd>L'attuale soluzione fornisce solo una singola categoria di prezzi, che è quella gratuita. Nelle versioni successive, puoi selezionare la categoria di prezzi.</dd>
	</dl>

3. Fai clic su **Create**.

  Viene visualizzato il dashboard {{site.data.keyword.cloud_notm}}. Potresti dover aggiornare il tuo browser per visualizzare il nuovo cluster, che è elencato nella sezione Servizi.</p></li>

4. Quando selezioni il servizio, vengono visualizzate le informazioni sul cluster.

5. Nella scheda Gestisci delle informazioni sul cluster, fai clic su **APRI**.

	Viene visualizzato il dashboard {{site.data.keyword.ihsdbaas_full}}.

6. Raccogli i nomi host e i numeri di porta delle tre istanze di database create che appartengono al tuo cluster di database. Ti servono i nomi host, i numeri di porta e le credenziali utente per i passi nella sezione [Connessione al database](#connect_db).

## Passo 2. Creazione di un progetto utilizzando un kit starter
{: #create_starter}

Ti serve un kit starter basato sul framework web Swift lato server Kitura.

![Dettagli della funzione](videos/StarterKit.gif){: gif}

Utilizza un progetto esistente che era stato creato da questo kit starter oppure crea un nuovo progetto.

1. Apri il [dashboard {{site.data.keyword.cloud_notm}} App Service](https://{DomainName}/developer/appservice/dashboard){: new_window} ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno").

2. Seleziona la scheda **Kit starter**.

3. Seleziona il kit starter **Swift Kitura** Backend for Frontend. Assicurati di non confonderlo con il kit starter Swift Kitura Basic App Web.
  Viene visualizzata la pagina Create new project.

4. Immetti i dettagli del progetto e fai clic su **Create Project**.
  Viene visualizzata la pagina del progetto.

5. Nella pagina del progetto, fai clic su **Download Code**.

6. Espandi il file compresso nella directory del tuo progetto.

## Passo 3. Connessione al database
{: #connect_db}

Per garantire un trasferimento dati protetto, scarica il file dell'autorità di certificazione (o CA, certificate authority) e copialo nella tua directory del progetto.

1. Passa alla directory del tuo progetto dove si trovano i file di codice scaricati espansi.

2. Crea un file JSON denominato `cred.json` per memorizzare le credenziali di accesso al cluster di database.

3. Immetti i valori raccolti dai passi in [Creazione di un cluster di database](/docs/swift?topic=swift-create-database-cluster#create_dbcluster). I valori devono essere specificati in una singola riga.
  ```hljs
  {
  "uri": "mongodb://<admin_ID>:<admin_pwd>@<Hostname_1>:<PortNumber_1>,
  <Hostname_2>:<PortNumber_2>,<Hostname_3>:<PortNumber_3>
   /admin?ssl=true&ssl_ca_certs=/swift-project/<CA_file>"
  }
  ```
  {: codeblock}

### Descrizioni del parametro di database
{: #db-parameter-descriptions}

Consulta le seguenti descrizioni del parametro di database:

* `admin_ID` - L'ID utente dell'amministratore del database come specificato in [Creazione di un cluster di database](/docs/swift?topic=swift-create-database-cluster#create_dbcluster).
* `admin_pwd` - L'ID utente della password dell'amministratore come specificato in [Creazione di un cluster di database](/docs/swift?topic=swift-create-database-cluster#create_dbcluster).
* `Hostname_i` - Una replica del database *i* (*i*=1,2,3) come restituita in [Creazione di un cluster di database](/docs/swift?topic=swift-create-database-cluster#create_dbcluster).
* `PortNumber_i` - Un numero di porta *i* (*i*=1,2,3) come restituito in [Creazione di un cluster di database](/docs/swift?topic=swift-create-database-cluster#create_dbcluster).
* `CA_file` - È il nome del file CA scaricato. Durante la distribuzione, viene copiato nella directory `/swift-project`.

4. Modifica il file `Package.swift` per aggiungere le dipendenze di pacchetto per l'utilizzo dell'SDK
MongoKitten.

  * Nella sezione dependencies, aggiungi la seguente riga:
   ```swift
   .package(url: "https://github.com/OpenKitten/MongoKitten.git", from: "4.0.0"),
   ```
   {: codeblock}

  * Nella sezione targets, aggiungi la dipendenza "MongoKitten" alla seguente riga. **Nota:** i valori devono essere specificati in una singola riga.
   ```swift
   .target(name: "Application", dependencies: [ "Kitura",
   "CloudEnvironment","SwiftMetrics","Health","MongoKitten", ]),
   ```
   {: codeblock}

5. Modifica il file `Sources/Application/Application.swift` per inizializzare la connettività a MongoDB utilizzando MongoKitten.

  * Importa l'SDK MongoKitten:
    ```swift
	  import MongoKitten
	  ```
	  {: codeblock}

  * Aggiungi la classe `ApplicationServices`:
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

  * Nella classe pubblica `App`, aggiungi le seguenti righe per inizializzare la connessione al database:
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

## Passo 4. Verifica della connessione al database
{: #verify_database}

1. Verifica la tua connessione al database modificando il file `Sources/Application/Application.swift` per aggiungere un comando per verificare la connessione al database.
Ad esempio, aggiungi il seguente comando in `class ApplicationServices`:

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

Dopo che hai distribuito la tua applicazione nel [Passo 6](#use-step6), viene visualizzato il seguente messaggio, se la tua connessione al database viene stabilita correttamente:

```
...
Connected to mongodb:
MongoKitten.Database&lt;mongodb:/&sol;&lt;<em>Hostname_1</em>&gt;&colon;&lt;<em>PortNumber_1</em>&gt;,&lt;<em>Hostname_2</em>&gt;&colon;&lt;<em>PortNumber_2</em>&gt;,&lt;<em>Hostname_3</em>&gt;&colon;&lt;<em>PortNumber_3</em>&gt;/admin&gt;
...
```
{: screen}

## Passo 5. Integrazione del codice dell'applicazione
{: #embed_appcode}

Puoi ora aggiungere il tuo codice dell'applicazione al progetto. Per ulteriori informazioni, vedi la documentazione dell'[API MongoKitten](http://beta.openkitten.org/tutorials/){: new_window} ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno").

## Passo 6. Distribuzione della tua applicazione
{: #deploy-dbcluster}

Puoi eseguire l'applicazione [in locale](/docs/swift?topic=swift-swift_cli#swift-install-tools) con gli strumenti di creazione necessari oppure eseguire la distribuzione a {{site.data.keyword.cloud_notm}}.

Per creare una toolchain di distribuzione nel dashboard, fai clic su **Distribuisci**. Configura la tua destinazione di distribuzione in base alle istruzioni per il metodo che scegli:
  * **Distribuisci a IBM Kubernetes Service**. Questa opzione crea un cluster di host, denominati nodi di lavoro, per distribuire e gestire contenitori applicazione ad elevata disponibilità. Puoi creare un cluster o distribuire un cluster esistente. Per ulteriori informazioni, vedi [Distribuzione delle applicazioni ai cluster Kubernetes](/docs/containers?topic=containers-app).
  * **Distribuisci a Cloud Foundry**. Questa opzione distribuisce la tua applicazione nativa del cloud senza che tu debba gestire l'infrastruttura sottostante. Se il tuo account ha accesso a {{site.data.keyword.cfee_full_notm}}, puoi selezionare un tipo di deployer **Cloud pubblico** o **Ambiente aziendale**, che puoi utilizzare per creare e gestire ambienti isolati per ospitare applicazioni Cloud Foundry esclusivamente per la tua azienda. Per ulteriori informazioni, vedi [Distribuzione delle applicazioni a Cloud Foundry Public](/docs/cloud-foundry-public?topic=cloud-foundry-public-deployingapps) e [Distribuzione delle applicazioni a {{site.data.keyword.cfee_full_notm}}](/docs/cloud-foundry?topic=cloud-foundry-deploy_apps).
  * **Distribuisci a Virtual Server**. Questa opzione esegue il provisioning di un'istanza del server virtuale, carica un'immagine che include la tua applicazione, crea una toolchain DevOps e avvia il primo ciclo di distribuzione per tuo conto. Per ulteriori informazioni, vedi [Distribuzione delle applicazioni a un server virtuale](/docs/vsi?topic=virtual-servers-deploying-to-a-virtual-server).
  
