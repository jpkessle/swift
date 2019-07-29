---

copyright:
  years: 2017, 2019
lastupdated: "2019-06-21"

keywords: push swift, swift notifications, push notifications swift, sending push swift, configure service instance swift, provider credentials swift

subcollection: swift

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Envoi de {{site.data.keyword.mobilepushshort}}
{: #push_notifications}

Etendez votre application Swift à l'aide du service {{site.data.keyword.mobilepushshort}} sur {{site.data.keyword.cloud}} pour envoyer des notifications en direct aux appareils mobiles et aux applications Web.

 - Les notifications peuvent être envoyées à tous les utilisateurs de l'application, ou à un ensemble spécifique d'utilisateurs ou d'appareils.
 - Les notifications interactives et silencieuses sont prises en charge.
 - Les clients peuvent choisir de s'abonner à des étiquettes ou des rubriques spécifiques pour les notifications.
 - Le propriétaire de l'application peut analyser le nombre d'appareils enregistrés pour la réception des notifications et le nombre de notifications envoyées.

Vous pouvez choisir d'utiliser le service {{site.data.keyword.mobilepushshort}} dans le cadre de MobileFirst Services Starter Boilerplate ou en tant que [services dédiés](/docs/dedicated?topic=dedicated-dedicated#dedicated) {{site.data.keyword.cloud_notm}}. Vous pouvez également utiliser un SDK (kit de développement de logiciels) et des [API REST ](https://mobilefirstplatform.ibmcloud.com/tutorials/en/foundation/8.0/notifications/rest-apis/){: new_window} ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe") pour affiner plus encore le développement de vos applications client.

![Présentation de la commande push](images/push_notification_lifecycle.jpg "Présentation de la commande push")

## Avant de commencer
{: #prereqs-push}

Tout d'abord, assurez-vous que la configuration prérequise suivante est respectée :

 - iOS 8.0+
 - Xcode 7.3, 8.0
 - Swift 2.3 - 4.0
 - CocoaPods ou Carthage

## Etape 1. Création d'une instance de {{site.data.keyword.mobilepushshort}}
{: #push_create}

1. Dans le catalogue {{site.data.keyword.cloud_notm}}, cliquez sur **Mobile** > **{{site.data.keyword.mobilepushshort}}**. L'écran de configuration du service s'ouvre.
2. Donnez un nom à votre instance de service ou utilisez le nom prédéfini.
3. Cliquez sur **Créer**.
4. Dans le panneau de navigation, cliquez sur **Connexions** pour sélectionner une application et la lier à votre service. Vous pouvez lier l'instance de service à votre application ultérieurement si vous ne la liez pas pendant l'étape de création.


## Etape 2. Obtention des données d'identification de votre fournisseur de notification
{: #get_creds-push}

Pour configurer le service de notifications push, vous devez obtenir les données d'identification requises auprès du service APNs (Apple Push Notification Service). Suivez les étapes décrites ici pour [obtenir et configurer les données d'identification de vos APN ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](/docs/services/mobilepush?topic=mobile-pushnotification-push_step_1){: new_window}.


## Etape 3. Configuration d'une instance de service
{: #config-resource-push}

Pour utiliser le service {{site.data.keyword.mobilepushshort}} pour envoyer des notifications, téléchargez le magasin de clés `.p12` que vous avez créé, qui contient la clé privée et les certificats SSL requis pour générer et publier votre application. Vous pouvez également utiliser l'API REST pour télécharger un certificat APNS.

Une fois le fichier `.cer` dans votre accès de chaîne de clé, exportez-le sur votre ordinateur afin de créer un certificat `.p12`.

Pour plus d'information sur l'utilisation du service APNS, reportez-vous au manuel [iOS Developer Library: Local and Push Notification Programming Guide ](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1){: new_window} ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe").

Pour configurer des APN sur la console des services Push Notification, procédez comme suit :

1. Sélectionnez **Configure** sur la console des services {{site.data.keyword.mobilepushshort}}.
2. Sélectionnez l'option **Mobile** pour mettre à jour les informations dans le formulaire **Données d'identification push APNS**.
3. Sélectionnez l'une des options suivantes :
	- Pour l'option **Mobile**
		1. Sélectionnez **Sandbox** (développement) ou **Production** (distribution), puis importez le certificat `p.12` que vous avez créé.
		  ![{{site.data.keyword.mobilepushshort}} - console](images/wizard.jpg "Configuration mobile de notification push")

		2. Dans la zone **Mot de passe**, entrez le mot de passe qui est associé au fichier de certificat `.p12`, puis cliquez sur **Sauvegarde**.
	- Pour l'option **Web**
		- Dans la section Safari Push, mettez à jour le formulaire avec les informations requises.
		- **Nom du site Web** : nom du site Web fourni dans le centre de notification.
		- **ID push du site web **: mettez à jour cette zone avec la chaîne de domaine inverse pour votre ID push de site Web. Par exemple, `web.com.example.www`.
		- **URL du site Web** : fournissez l'URL du site Web abonné aux notifications push. Par exemple, `https://www.example.com`.
		- **Domaines autorisés** : (paramètre facultatif) Liste des sites Web qui demandent l'autorisation de l'utilisateur. Vérifiez que les URL sont des valeurs séparées par des virgules. Les valeurs de l'URL de site Web sont utilisés si les informations ne sont pas fournies.
		- **Chaîne de format de l'URL **: URL à résoudre lors d'un clic sur la notification. Par exemple, `https://www.example.com`. Vérifiez que l'URL utilise le schéma http ou https.
		-** Certificat push Web de Safari** : téléchargez le certificat `.p12` et indiquez le mot de passe.
4. Cliquez sur **Sauvegarder**.

  ![{{site.data.keyword.mobilepushshort}} - console](images/push_configure_safari.jpg "Configuration Web de notification push")

## Etape 4. Configuration du logiciel SDK client du service
{: #service-client-push}

Pour permettre aux applications iOS de recevoir des notifications push sur vos appareils, vous devez configurer le logiciel SDK iOS pour le service {{site.data.keyword.mobilepushshort}}.

Les logiciels SDK Swift d'{{site.data.keyword.cloud_notm}} Mobile Services peuvent être installés avec Cocoapods ou Carthage. Pour plus d'informations, consultez le site [https://github.com/ibm-bluemix-mobile-services/bms-clientsdk-swift-push/tree/Doc#setup-client-application](https://github.com/ibm-bluemix-mobile-services/bms-clientsdk-swift-push/tree/Doc#setup-client-application){: new_window} ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe").

## Etape 5. Envoi d'une notification
{: #send-notify-push}

Une fois votre application développée, vous pouvez envoyer des notifications push de base.

Pour envoyer des notifications push de base, procédez comme suit :

1. Sélectionnez **Envoyer des notifications** et rédigez un message en choisissant une option **Envoyer à**. Les options prises en charge sont **Appareil par étiquette**, **ID appareil**, **ID utilisateur**, **Appareils iOS**, **Notifications Web** et **Tous les appareils**.
**Remarque** : lorsque vous sélectionnez l'option **Tous les appareils**, tous les appareils inscrits à {{site.data.keyword.mobilepushshort}} reçoivent des notifications.

  ![Ecran d'envoi des notifications](images/tag_notification.jpg "Ecran d'envoi des notifications")

2. Dans la zone **Message**, composez votre message. Configurez les paramètres facultatifs, selon les besoins.
3. Cliquez sur **Envoyer**.
3. Vérifiez que vos appareils ou votre navigateur ont reçu la notification.

  La capture d'écran suivante montre une alerte traitant une notification push en avant-plan sur l'appareil.
  
  ![Alerte de notification en avant-plan](images/Android_Screenshot.jpg "Alerte de notification en avant-plan")

  La capture d'écran suivante montre une notification push en arrière-plan.
  
  ![Alerte de notification en arrière-plan](images/background.png "Alerte de notification en arrière-plan")

### Paramètres facultatifs
{: #optional-push}

Vous pouvez personnaliser les paramètres {{site.data.keyword.mobilepushshort}} pour envoi de notifications aux appareils iOS. Les options de personnalisation facultative suivantes sont prises en charge.

- **Badge** : indique le nombre qui s'affiche sur le badge d'application. La valeur par défaut est zéro (0) et elle n'affiche pas de badge.
- **Son** : indique le clip audio à exécuter à réception d'une notification. Prend en charge la valeur par défaut ou le nom d'une ressource audio qui est fournie dans l'application.
- **Contenu supplémentaire** : spécifie les valeurs de contenu personnalisées pour vos notifications.

Vous pouvez également choisir d'activer les [notifications interactives](https://github.com/ibm-bluemix-mobile-services/bms-clientsdk-swift-push/tree/Doc#interactive-notifications){: new_window} ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe") et les [notifications de média riche](https://github.com/ibm-bluemix-mobile-services/bms-clientsdk-swift-push/tree/Doc#enabling-rich-media-notifications){: new_window} ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe").

## Etape 6. Surveillance pour les notifications distribuées
{: #monitor-push}

Le service {{site.data.keyword.mobilepushshort}} fournit un utilitaire de surveillance destiné à vous aider à vérifier le statut des messages qui sont envoyés. Pour configurer votre utilitaire de surveillance, voir [Activation de la surveillance pour les applications iOS](https://github.com/ibm-bluemix-mobile-services/bms-clientsdk-swift-push/tree/Doc#enable-monitoring){: new_window} ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe").

## Etapes suivantes
{: #next-push notoc}

 - Pour en savoir plus sur le service et tirer parti de toutes les fonctionnalités, parcourez notre [documentation](/docs/services/mobilepush?topic=mobile-pushnotification-overview-push).

 - Pour une présentation de l'utilisation des services mobiles et d'{{site.data.keyword.cloud_notm}}, voir [Mise en route avec les applications mobiles sur {{site.data.keyword.cloud_notm}}](/docs/services/mobile?topic=mobile-about).

 - Les kits de démarrage constituent l'un des moyens les plus rapides d'utiliser les fonctionnalités d'{{site.data.keyword.cloud_notm}}. Vous pouvez voir les kits de démarrage disponibles dans le [tableau de bord Mobile Developer](https://{DomainName}/developer/mobile/dashboard){: new_window} ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe"). Téléchargez le code. Exécutez l'application.

 - Vous pouvez utiliser l'[interface utilisateur Swagger](https://mobilefirstplatform.ibmcloud.com/tutorials/en/foundation/8.0/notifications/rest-apis/){: new_window} ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe") pour passer rapidement en revue la documentation d'API REST.
