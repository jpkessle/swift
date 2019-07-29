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

# Création d'une base de données hautement disponible et sécurisée
{: #create-database-cluster}

Pour tirer pleinement parti d'une base de données hautement disponible et sécurisée, vous imbriquez une logique supplémentaire dans votre application. A l'aide des fragments de code fournis, vous pouvez créer une base de données MongoDB et y accéder. 

Actuellement, le langage de programmation pris en charge pour l'utilisation de {{site.data.keyword.ihsdbaas_full}}
est Swift 4.0 avec MongoKitten SDK 4.0.0.

## Etape 1. Création d'un cluster de base de données
{: #create_dbcluster}

1. Accédez à l'écran [Configuration du service {{site.data.keyword.ihsdbaas_full}}](https://{DomainName}/catalog/services/hyper-protect-dbaas){: new_window} ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe").

2. Fournissez les informations suivantes :

	<dl>
		<dt>Nom du service :</dt>
		<dd>Nom pour le service de base de données.</dd>

    <dt>Sélectionnez une région/un emplacement où effectuer le déploiement :</dt>
    <dd>Sélectionnez le centre de données dans lequel se trouve la base de données.</dd>

    <dt>Sélectionnez un groupe de ressources :</dt>
    <dd>Si aucun groupe de ressources ne peut être sélectionné, vous pouvez en créer un dans le tableau de bord IBM Cloud.</dd>

		<dt>Nom de cluster / jeu de répliques :</dt>
		<dd>Nom pour le cluster de base de données.</dd>

		<dt>Nom de l'administrateur de base de données :</dt>
		<dd>ID utilisateur de l'administrateur de base de données (DBA).</dd>

		<dt>Mot de passe de l'administrateur de base de données :</dt>
		<dd>Mot de passe pour l'ID utilisateur DBA.</dd>

    <dt>Confirmation du mot de passe de l'administrateur de base de données :</dt>
    <dd>Confirmation du mot de passe pour l'ID utilisateur de l'administrateur de base de données.</dd>

		<dt>Type de base de données :</dt>
		<dd>Sélectionnez le type de base de données. Actuellement, seul le type MongoDB est pris en charge.</dd>

    <dt>Contrat de licence :</dt>
    <dd>Lisez le contrat de licence et cochez la case pour confirmer votre accord.</dd>

    <dt>Tarification :</dt>
		<dd>La solution actuelle ne fournit qu'une seule catégorie de tarification, qui est gratuite. Dans les versions ultérieures, vous pourrez sélectionner la catégorie de tarification.</dd>
	</dl>

3. Cliquez sur **Créer**.

  Le tableau de bord d'{{site.data.keyword.cloud_notm}} s'affiche. Vous devrez peut-être actualiser votre navigateur pour voir le nouveau cluster, lequel est répertorié dans la section Services.</p></li>

4. Lorsque vous sélectionnez le service, les informations de cluster s'affichent.

5. Dans l'onglet Gérer des informations de cluster, cliquez sur **Ouvrir**.

	Le tableau de bord de {{site.data.keyword.ihsdbaas_full}} s'affiche.

6. Obtenez le nom d'hôte et les numéros de port des trois instances de base de données créées qui appartiennent à votre cluster de base de données. Vous aurez besoin des noms d'hôtes, des numéros de port et des données d'identification de l'utilisateur pour les étapes de la section [Connexion à la base de données](#connect_db).

## Etape 2. Création d'un projet à l'aide d'un kit de démarrage
{: #create_starter}

Vous avez besoin d'un kit qui repose sur l'infrastructure Web Swift côté serveur Kitura.

![Détails d'une fonction](videos/StarterKit.gif){: gif}

Utilisez un projet existant qui a été créé à partir de ce kit de démarrage, ou créez-en un nouveau.

1. Ouvrez le [tableau de bord {{site.data.keyword.cloud_notm}} App Service](https://{DomainName}/developer/appservice/dashboard){: new_window} ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe").

2. Sélectionnez l'onglet **Kits de démarrage**.

3. Sélectionnez le kit de démarrage BFF (Backend for Frontend) **Swift Kitura**. Assurez-vous de ne pas le confondre avec le kit de démarrage de l'application Web de base Swift Kitura.
  La page Créer un projet s'affiche.

4. Entrer les détails du projet et cliquez sur **Créer un projet**.
  La page du projet s'affiche.

5. Sur la page des projets, cliquez sur **Télécharger le code**.

6. Développez le fichier compressé dans le répertoire du projet.

## Etape 3. Connexion à la base de données
{: #connect_db}

Pour garantir un transfert de données sécurisé, téléchargez le fichier de l'autorité de certification (CA) et copiez-le dans répertoire de votre projet.

1. Accédez au répertoire de votre projet où se trouvent les fichiers de code téléchargés développés.

2. Créez un fichier JSON nommé `cred.json` pour stocker vos données d'identification d'accès au cluster de base de données.

3. Entrez les valeurs collectées dans les étapes de [Création d'un cluster de base de données](/docs/swift?topic=swift-create-database-cluster#create_dbcluster). Les valeurs doivent être spécifiées sur une seule ligne.
  ```hljs
  {
  "uri": "mongodb://<admin_ID>:<admin_pwd>@<Hostname_1>:<PortNumber_1>,
  <Hostname_2>:<PortNumber_2>,<Hostname_3>:<PortNumber_3>
   /admin?ssl=true&ssl_ca_certs=/swift-project/<CA_file>"
  }
  ```
  {: codeblock}

### Description des paramètres de base de données
{: #db-parameter-descriptions}

Consultez les descriptions de paramètre de base de données suivantes :

* `admin_ID` - ID utilisateur de l'administrateur de base de données comme indiqué dans [Création d'un cluster de base de données](/docs/swift?topic=swift-create-database-cluster#create_dbcluster).
* `admin_pwd` - ID utilisateur du mot de passe de l'administrateur de base de données comme indiqué dans [Création d'un cluster de base de données](/docs/swift?topic=swift-create-database-cluster#create_dbcluster).
* `Hostname_i` - Réplique de base de données *i* (*i*=1,2,3) telle que retournée dans [Création d'un cluster de base de données](/docs/swift?topic=swift-create-database-cluster#create_dbcluster).
* `PortNumber_i` - Numéro de port *i* (*i*=1,2,3) tel que retourné dans [Création d'un cluster de base de données](/docs/swift?topic=swift-create-database-cluster#create_dbcluster).
* `CA_file` - Nom de fichier du fichier de l'autorité de certification téléchargé. Lors du déploiement, il est copié dans le répertoire `/rapide-projet`.

4. Editez le fichier `Package.swift` afin d'ajouter les dépendances de package pour l'utilisation
du MongoKitten.

  * Dans la section dependencies, ajoutez la ligne suivante :
   ```swift
   .package(url: "https://github.com/OpenKitten/MongoKitten.git", from: "4.0.0"),
   ```
   {: codeblock}

  * Dans la section targets, ajoutez la dépendance "MongoKitten" sur la ligne suivante. **Remarque :** La valeur doit être indiquée sur une seule ligne.
   ```swift
   .target(name: "Application", dependencies: [ "Kitura",
   "CloudEnvironment","SwiftMetrics","Health","MongoKitten", ]),
   ```
   {: codeblock}

5. Editez le fichier `Sources/Application/Application.swift` pour initialiser la connectivité à MongoDB à l'aide de MongoKitten.

  * Importez le logiciel SDK MongoKitten :
    ```swift
	  import MongoKitten
	  ```
	  {: codeblock}

  * Ajoutez la classe `ApplicationServices` :
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

  * Dans public class `App`, ajoutez les lignes suivantes afin d'initialiser la connexion de base de données :
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

## Etape 4. Vérification de la connexion de base de données
{: #verify_database}

1. Vérifiez votre connexion de base de données en éditant le fichier `Sources/Application/Application.swift` afin d'ajouter une commande pour tester la connexion à la base de données.
Par exemple, ajoutez la commande suivante dans `class ApplicationServices` :

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

Après avoir déployé votre application dans l'[étape 6](#use-step6), le message suivant s'affiche, si votre connexion à la base de données aboutit :

```
...
Connected to mongodb:
MongoKitten.Database&lt;mongodb:/&sol;&lt;<em>Hostname_1</em>&gt;&colon;&lt;<em>PortNumber_1</em>&gt;,&lt;<em>Hostname_2</em>&gt;&colon;&lt;<em>PortNumber_2</em>&gt;,&lt;<em>Hostname_3</em>&gt;&colon;&lt;<em>PortNumber_3</em>&gt;/admin&gt;
...
```
{: screen}

## Etape 5. Imbrication de votre code d'application
{: #embed_appcode}

Vous pouvez maintenant ajouter votre propre code d'application au projet. Pour plus d'informations, voir la documentation sur l'[API MongoKitten](http://beta.openkitten.org/tutorials/){: new_window} ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe").

## Etape 6. Déploiement de votre application
{: #deploy-dbcluster}

Vous pouvez exécuter l'application [localement](/docs/swift?topic=swift-swift_cli#swift-install-tools) avec les outils de génération requis ou déployer sur {{site.data.keyword.cloud_notm}}.

Pour créer une chaîne d'outils de déploiement dans le tableau de bord, cliquez sur **Déployer**. Configurez votre cible de déploiement en fonction des instructions s'appliquant à la méthode choisie :
  * **Déployer dans IBM Kubernetes Service**. Cette option crée un cluster d'hôtes, appelé noeuds worker, afin de déployer et de gérer des conteneurs d'application haute disponibilité. Vous pouvez créer un cluster ou effectuer un déploiement sur un cluster existant. Pour plus d'informations, voir [Déploiement d'applications dans des clusters Kubernetes](/docs/containers?topic=containers-app).
  * **Déployer dans Cloud Foundry**. Cette option déploie votre application cloud native sans qu'il soit nécessaire de gérer l'infrastructure sous-jacente. Si votre compte a accès à {{site.data.keyword.cfee_full_notm}}, vous pouvez sélectionner un déployeur de type **Public Cloud** ou **Enterprise Environment**, que vous pouvez utiliser pour créer et gérer des environnements isolés pour l'hébergement de vos applications Cloud Foundry exclusivement pour votre entreprise. Pour plus d'informations, voir [Déploiement d'applications dans Cloud Foundry Public](/docs/cloud-foundry-public?topic=cloud-foundry-public-deployingapps) et [Déploiement d'applications dans {{site.data.keyword.cfee_full_notm}}](/docs/cloud-foundry?topic=cloud-foundry-deploy_apps).
  * **Déployer sur un serveur virtuel**. Cette option met à disposition une instance de serveur virtuel, charge une image qui inclut votre application, crée une chaîne d'outils DevOps et initie pour vous le premier cycle de déploiement. Pour plus d'informations, voir [Déploiement d'applications dans un serveur virtuel](/docs/vsi?topic=virtual-servers-deploying-to-a-virtual-server).
  
