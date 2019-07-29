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

# Invio di {{site.data.keyword.mobilepushshort}}
{: #push_notifications}

Migliora la tua applicazione Swift utilizzando il servizio {{site.data.keyword.mobilepushshort}} su {{site.data.keyword.cloud}} per inviare notifiche live ai dispositivi mobili e alle applicazioni web.

 - Le notifiche possono essere recapitate a tutti gli utenti dell'applicazione oppure a una serie selezionata di utenti o dispositivi.
 - Supporta sia le notifiche interattive che quelle automatiche.
 - I clienti possono scegliere di sottoscrivere specifiche tag o specifici argomenti per la notifica.
 - Abilita il proprietario dell'applicazione ad analizzare il numero di dispositivi registrati per ricevere le notifiche e il numero di notifiche inviate.

Puoi scegliere di utilizzare il servizio {{site.data.keyword.mobilepushshort}} come una parte del contenitore tipo MobileFirst Services Starter o come {{site.data.keyword.cloud_notm}} [Servizi dedicati](/docs/dedicated?topic=dedicated-dedicated#dedicated). Puoi anche utilizzare un SDK (software development kit) e le [API REST ](https://mobilefirstplatform.ibmcloud.com/tutorials/en/foundation/8.0/notifications/rest-apis/){: new_window} ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno") per sviluppare ulteriormente le tue applicazioni client.

![Panoramica Push](images/push_notification_lifecycle.jpg "Panoramica Push")

## Prima di cominciare
{: #prereqs-push}

Assicurati innanzitutto di disporre dei seguenti prerequisiti pronti a essere utilizzati:

 - iOS 8.0+
 - Xcode 7.3, 8.0
 - Swift 2.3 - 4.0
 - CocoaPods o Carthage

## Passo 1: Creazione di un'istanza di {{site.data.keyword.mobilepushshort}}
{: #push_create}

1. Nel catalogo {{site.data.keyword.cloud_notm}}, fai clic su **Mobile** > **{{site.data.keyword.mobilepushshort}}**. Viene visualizzata la schermata di configurazione del servizio.
2. Dai un nome alla tua istanza del servizio oppure utilizza il nome preimpostato.
3. Fai clic su **Create**.
4. Nel riquadro di navigazione, fai clic su **Connessioni** per selezionare un'applicazione ed eseguirne il bind al tuo servizio. Puoi eseguire il bind dell'istanza del servizio alla tua applicazione in un secondo momento, se la lasci senza bind durante la creazione.


## Passo 2. Ottieni le tue credenziali del provider di notifiche
{: #get_creds-push}

Per configurare il servizio Push Notifications, devi ottenere le credenziali richieste dall'APNs (Apple Push Notification Service). Attieniti alla procedura qui indicata per [ottenere e configurare le tue credenziali APNs ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](/docs/services/mobilepush?topic=mobile-pushnotification-push_step_1){: new_window}.


## Passo 3. Configura un'istanza del servizio
{: #config-resource-push}

Per utilizzare il servizio {{site.data.keyword.mobilepushshort}} per inviare notifiche, carica il keystore `.p12` che hai creato, che dispone della chiave privata e dei certificati SSL necessari per creare e pubblicare la tua applicazione. Puoi inoltre utilizzare l'API REST per caricare un certificato APNs.

Una volta che il file `.cer` si trova nel tuo accesso alla catena di chiavi, esportalo sul tuo computer per creare un certificato `.p12`.

Per ulteriori informazioni sull'utilizzo di APNs, consulta il manuale [iOS Developer Library: Local and Push Notification Programming Guide ](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1){: new_window} ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno").

Per configurare APNs sulla console dei servizi Push Notifications, completa la seguente procedura:

1. Seleziona **Configure** nella console dei servizi {{site.data.keyword.mobilepushshort}}.
2. Scegli l'opzione **Mobile** per aggiornare le informazioni nel formato **APNs Push Credentials**.
3. Scegli una delle seguenti opzioni:
	- Per l'opzione **Mobile**
		1. Seleziona **Sandbox** (sviluppo) o **Production** (distribuzione) e carica quindi il certificato `p.12` che hai creato.
		  ![{{site.data.keyword.mobilepushshort}} - Console](images/wizard.jpg "Configurazione mobile delle notifiche push")

		2. Nel campo **Password**, immetti la password associata al file di certificato `.p12` e quindi fai clic su **Save**.
	- Per l'opzione **Web**
		- Nella sezione Safari Push, aggiorna il modulo con le informazioni richieste.
		- **Website Name**: il nome del sito web fornito nel Notification center.
		- **Website Push ID**: aggiorna con la stringa reverse-domain per il tuo ID push sito web. Ad esempio, `web.com.example.www`.
		- **Website URL**: fornisci l'URL del sito web sottoscritto alle notifiche di push. Ad esempio, `https://www.example.com`.
		- **Allowed Domains**: (parametro facoltativo) Un elenco di siti web che richiedono l'autorizzazione dall'utente. Assicurati che gli URL siano valori separati da virgola. Vengono utilizzati i valori in Website URL se non vengono fornite le informazioni.
		- **URL Format String**: l'URL da risolvere quando si fa clic sulla notifica. Ad esempio, `https://www.example.com`. Assicurati che l'URL utilizzi lo schema http o https.
		-**Safari web push certificate**: Carica il certificato `.p12` e fornisci la password.
4. Fai clic su **Save**.

  ![{{site.data.keyword.mobilepushshort}} - Console](images/push_configure_safari.jpg "Configurazione web notifiche push")

## Passo 4. Configura l'SDK client del servizio
{: #service-client-push}

Per abilitare le applicazioni iOS a ricevere le notifiche di push ai tuoi dispositivi, devi configurare l'SDK iOS per il servizio {{site.data.keyword.mobilepushshort}}.

Gli SDK Swift {{site.data.keyword.cloud_notm}} Mobile Services possono essere installati con Cocoapods o Carthage. Per ulteriori informazioni, vedi [https://github.com/ibm-bluemix-mobile-services/bms-clientsdk-swift-push/tree/Doc#setup-client-application](https://github.com/ibm-bluemix-mobile-services/bms-clientsdk-swift-push/tree/Doc#setup-client-application){: new_window} ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno").

## Passo 5. Invio di una notifica
{: #send-notify-push}

Dopo che hai sviluppato la tua applicazione, puoi inviare delle notifiche di push di base.

Per inviare le notifiche di push di base, completa la seguente procedura:

1. Seleziona **Send Notifications** e componi un messaggio scegliendo l'opzione **Send to**. Le opzioni supportate sono **Device by Tag**, **Device Id**, **User Id**, **iOS devices**, **Web Notifications** e **All Devices**.
**Nota**: quando selezioni l'opzione **All Devices**, tutti i dispositivi sottoscritti a {{site.data.keyword.mobilepushshort}} ricevono le notifiche.

  ![Schermata Send Notifications](images/tag_notification.jpg "Schermata Send Notifications")

2. Nel campo **Message**, componi il messaggio. Scegli di configurare le impostazioni facoltative come richiesto.
3. Fai clic su **Send**.
3. Verifica che i tuoi dispositivi o il tuo browser abbiano ricevuto la notifica.

  La seguente acquisizione di schermo mostra una casella di avviso che gestisce una notifica di push in primo piano sul dispositivo.
  
  ![Notifica push in primo piano su Android](images/Android_Screenshot.jpg "Avviso di notifica in primo piano")

  La seguente acquisizione di schermo mostra una notifica di push in background.
  
  ![Notifica push in background su iOSd](images/background.png "Avviso di notifica in background")

### Impostazioni facoltative
{: #optional-push}

Puoi personalizzare le impostazioni {{site.data.keyword.mobilepushshort}} per l'invio di notifiche ai dispositivi iOS. Sono supportate le seguenti opzioni di personalizzazione facoltative.

- **Badge**:  indica il numero che viene visualizzato nel badge dell'applicazione. Il valore predefinito è zero (0) e non visualizza un badge.
- **Sound**: indica un file audio da riprodurre al ricevimento di un notifica. Supporta il valore predefinito oppure il nome di una risorsa audio inclusa nell'applicazione.
- **Additional payload**: specifica i valori di payload personalizzati per le tue notifiche.

Puoi anche scegliere di abilitare le [notifiche interattive](https://github.com/ibm-bluemix-mobile-services/bms-clientsdk-swift-push/tree/Doc#interactive-notifications){: new_window} ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno") e le [notifiche rich media](https://github.com/ibm-bluemix-mobile-services/bms-clientsdk-swift-push/tree/Doc#enabling-rich-media-notifications){: new_window} ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno").

## Passo 6. Monitoraggio per le notifiche recapitate
{: #monitor-push}

Il servizio {{site.data.keyword.mobilepushshort}} fornisce un programma di utilità di monitoraggio per aiutarti a controllare lo stato dei messaggi inviati. Per configurare il tuo programma di utilità di monitoraggio, vedi il documento relativo all'[abilitazione del monitoraggio per le applicazioni iOS](https://github.com/ibm-bluemix-mobile-services/bms-clientsdk-swift-push/tree/Doc#enable-monitoring){: new_window} ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno").

## Passi successivi
{: #next-push notoc}

 - Per ulteriori informazioni sul servizio e per avvalerti di tutte le funzioni, leggi attentamente la nostra [documentazione](/docs/services/mobilepush?topic=mobile-pushnotification-overview-push).

 - Per un'introduzione alla gestione dei servizi mobili e di {{site.data.keyword.cloud_notm}}, vedi [Introduzione alle applicazioni mobili su {{site.data.keyword.cloud_notm}}](/docs/services/mobile?topic=mobile-about).

 - I kit starter sono uno dei modi più rapidi per utilizzare le funzioni di {{site.data.keyword.cloud_notm}}. Visualizza i nostri kit starter disponibili nel [Dashboard dello sviluppatore mobile ](https://{DomainName}/developer/mobile/dashboard){: new_window} ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno"). Scarica il codice. Esegui l'applicazione!

 - Puoi utilizzare l'[IU Swagger](https://mobilefirstplatform.ibmcloud.com/tutorials/en/foundation/8.0/notifications/rest-apis/){: new_window} ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno") per esaminare rapidamente la documentazione dell'API REST.
