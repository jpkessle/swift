---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-19"

keywords: authentication swift, security swift, forgot password swift, social swift, identity provider swift, tentantid swift, cloud directory swift

subcollection: swift

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}
{:note: .note}

# Benutzerauthentifizierung hinzufügen
{: #appid}

Anwendungssicherheit ist ein sehr kompliziertes Thema. Für die meisten
Entwickler stellt sie eine der schwierigsten Aufgaben beim Erstellen einer App
dar. Es muss gewährleistet sein, dass die Daten Ihrer Benutzer geschützt sind. Durch
die Integration von {{site.data.keyword.appid_full}} in Ihre Apps
können Sie auch ohne große Erfahrungen in Bezug auf die Sicherheit Ressourcen
schützen und eine Authentifizierung hinzufügen.

Wenn sich Benutzer anmelden müssen, können Sie Benutzerdaten wie
App-Vorgaben (oder auch Informationen aus öffentlichen Social-Media-Profilen)
speichern und dann unter Nutzung dieser Daten die Attraktivität Ihrer App für
die einzelnen Benutzer anpassen. {{site.data.keyword.appid_short_notm}}
stellt Ihnen ein Anmeldeframework bereit; Sie können jedoch auch Ihre eigenen
markenspezifischen Anmeldeanzeigen zur Verwendung beim Cloudverzeichnis einbinden.

Informationen zu allen Möglichkeiten für die Nutzung von
{{site.data.keyword.appid_short_notm}} sowie Angaben über die
Architektur finden Sie unter [Informationen zu {{site.data.keyword.appid_short_notm}}](/docs/services/appid?topic=appid-about#about).

## Vorbereitende Schritte
{: #prereqs-appid}

Stellen Sie zunächst sicher, dass die folgenden Voraussetzungen gegeben
sind:
* CocoaPods (Version 1.1.0 oder höher)
* iOS (Version 9 oder höher)
* MacOS (Version 10.11.5 oder höher)
* Xcode (Version 9.0.1 oder höher)

## Schritt 1. Instanz von {{site.data.keyword.appid_short_notm}}
erstellen
{: #create-instance-appid}

Stellen Sie eine Instanz des {{site.data.keyword.appid_short_notm}}-Service bereit:

1. Wählen Sie {{site.data.keyword.appid_short_notm}} im [{{site.data.keyword.cloud_notm}}-Katalog](https://{DomainName}/catalog){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link") aus. Die Anzeige für die Servicekonfiguration wird geöffnet.
2. Vergeben Sie für die Serviceinstanz einen Namen oder verwenden Sie den voreingestellten Namen.
3. Wählen Sie Ihren Preistarif aus und
klicken Sie auf **Erstellen**.

## Schritt 2. Swift-SDK für iOS installieren
{: #install-sdk-appid}

Der Service bietet ein SDK, das die Codierung Ihrer App
vereinfacht. Das SDK
muss in Ihrem App-Code installiert sein.

1. Öffnen Sie das vorhandene XCode-Projektverzeichnis für die
`Poddatei`.

2. Fügen Sie unter dem Ziel Ihres Projekts (Abschnitt "target") eine Abhängigkeit für den Pod `IBMCloudAppID` hinzu. Stellen Sie sicher, dass der Befehl
`use_frameworks!` ebenfalls wie im
folgenden Beispiel gezeigt unter Ihrem Ziel angegeben ist.
    ```swift
    target '<yourTarget>' do
      use_frameworks!
        pod 'IBMCloudAppID'
    end
    ```
    {: pre}

3. Laden Sie die Abhängigkeit `IBMCloudAppID` herunter.
    ```
    pod install --repo-update
    ```
    {: pre}

## Schritt 3. SDK initialisieren
{: #initialize-sdk-appid}

Nachdem Sie das SDK in Ihrer App initialisiert haben, können Sie mit der
Konfiguration der {{site.data.keyword.appid_short_notm}}-Einstellungen beginnen.

1. Navigieren Sie zu **Projekteinstellungen > Funktionen >
Gemeinsame Nutzung der Schlüsselkette** und aktivieren Sie die
gemeinsame Nutzung der Schlüsselkette in Ihrem XCode-Projekt.

2. Navigieren Sie zu **Projekteinstellungen >
Info > URL-Typen**
und fügen Sie den folgenden Wert zu beiden Textfeldern
**URL-Schema** und **ID** hinzu.
  ```
  $(PRODUCT_BUNDLE_IDENTIFIER)
  ```
  {: codeblock}

3. Fügen Sie den folgenden Import zu
Ihrer Datei `AppDelegate.swift` hinzu.
  ```swift
  import IBMCloudAppID
  ```
  {: codeblock}

4. Übergeben Sie die Parameter `tenantID` und `AppID_region`, um das SDK zu initialisieren. Eine gängige, aber nicht verbindliche Position für den Code ist
die Methode `application:didFinishLaunchingWithOptions` von `AppDelegate` in Ihrer App.
  ```swift
  AppID.getInstance().initialize(getApplicationContext(), <tenantId>, <AppID_region>);
  ```
  {: codeblock}
  
  * `tenantID`: Die Tenant-ID ist eine eindeutige Kennung, die zum Initialisieren Ihrer App verwendet wird. Sie können den Wert im {{site.data.keyword.appid_short_notm}}-Dashboard aufrufen, indem Sie die Registerkarte **Serviceberechtigungsnachweise** auswählen und anschließend auf **Berechtigungsnachweise anzeigen** klicken.
  * `AppID_region`: Die {{site.data.keyword.appid_short_notm}}-Region ist die {{site.data.keyword.cloud_notm}}-Region, in der Sie mit dem Service arbeiten. Diese Region ist im Service-Dashboard zu finden und kann `AppID.REGION_US_SOUTH`, `AppID.REGION_SYDNEY` oder `AppID.REGION_UK` lauten.

5. Fügen Sie Ihrer Datei `AppDelegate` den folgenden Code hinzu.
    ```swift
    func application(_ application: UIApplication, open url: URL, options :[UIApplicationOpenURLOptionsKey : Any]) -> Bool {
            return AppID.sharedInstance.application(application, open: url, options: options)
        }
    ```
    {: codeblock}

## Schritt 4. Anmeldefunktionalität verwalten
{: #managing-signin-appid}

Ein Identitätsprovider stellt die Authentifizierungsinformationen für
Ihre Benutzer bereit, damit Sie sie autorisieren können. Bei
{{site.data.keyword.appid_short_notm}} können Sie
Social-Media-Identitätsprovider wie Facebook und Google+ verwenden oder eine
Benutzerregistry mit Cloudverzeichnis verwalten.

Ihre Konfigurationen können Sie jederzeit ohne eine Aktualisierung Ihres
App-Codes aktualisieren.
{: tip}

### Social-Media-Identitätsprovider
{: #social-appid}

Bei {{site.data.keyword.appid_short_notm}} können Sie
Social-Media-Identitätsprovider wie Facebook und Google+ verwenden, um Ihre
Apps zu schützen.

So konfigurieren Sie Social-Media-Identitätsprovider:

1. Öffnen Sie im {{site.data.keyword.appid_short_notm}}-Dashboard
die Anzeige
**Identitätsprovider > Verwalten**.
2. Legen Sie für die Identitätsprovider, die Sie verwenden wollen, die
Einstellung **Ein** fest. Sie können eine beliebige
Kombination von Identitätsprovidern verwenden. Wenn Sie jedoch angepasste
Anmeldebildschirme verwenden möchten, müssen Sie lediglich die Option
"Cloudverzeichnis" aktivieren.
3. Aktualisieren Sie die
[Standardkonfiguration](/docs/services/appid?topic=appid-social#social)
mit Ihren eigenen Berechtigungsnachweisen. {{site.data.keyword.appid_short_notm}}
stellt IBM Berechtigungsnachweise bereit, mit deren Hilfe Sie den Service
ausprobieren können. Bevor Sie Ihre App veröffentlichen, müssen Sie jedoch die
Konfiguration aktualisieren.
4. Passen Sie die Anmeldeanzeige so an, dass das Bild und die Farben Ihrer Wahl angezeigt werden.
5. Fügen Sie den folgenden Befehl zu Ihrem Code hinzu, um das
Anmeldewidget mit Ihrer App aufzurufen.
    ```swift
    import IBMCloudAppID
    class delegate : AuthorizationDelegate {
        public func onAuthorizationSuccess(accessToken: AccessToken, identityToken: IdentityToken, response:Response?) {
            //User authenticated
        }

        public func onAuthorizationCanceled() {
            //Authentication cancelled by the user
        }

        public func onAuthorizationFailure(error: AuthorizationError) {
            //Exception occurred
        }
    }

    AppID.sharedInstance.loginWidget?.launch(delegate: delegate())
    ```
    {: codeblock}

### Cloudverzeichnis
{: #cloud-dir-appid}

Bei {{site.data.keyword.appid_short_notm}} können Sie eine eigene
Benutzerregistry verwalten, die als "Cloudverzeichnis" bezeichnet wird. Das
Cloudverzeichnis ermöglicht es den Benutzern, sich bei Ihren mobilen und
Web-Apps unter Verwendung ihrer E-Mail-Adresse und eines Kennworts anzumelden.

Wenn Sie eigene markenspezifische Anmeldeanzeigen verwenden wollen, kann nur das
Cloudverzeichnis als Identitätsprovider aktiviert werden.
{: tip}

So konfigurieren Sie das Cloudverzeichnis:

1. Öffnen Sie im {{site.data.keyword.appid_short_notm}}-Dashboard
die Registerkarte **Identitätsprovider verwalten** und legen
Sie für das Cloudverzeichnis die Einstellung **Ein** fest.
2. Konfigurieren Sie Ihre
[Verzeichnis- und
Nachrichteneinstellungen](/docs/services/appid?topic=appid-cloud-directory).
4. Wählen Sie die Kombinationen der Anmeldeanzeigen aus, die angezeigt
werden sollen, und geben Sie den Code für den Aufruf dieser Anzeigen in Ihrer
Anwendung an.
    * Anmeldung
        ```swift
        class delegate : TokenResponseDelegate {
            public func onAuthorizationSuccess(accessToken: AccessToken?, identityToken: IdentityToken?, response:Response?) {
            /* User authenticated */
            }

            public func onAuthorizationFailure(error: AuthorizationError) {
            /* Exception occurred */
            }
        }

        AppID.sharedInstance.obtainTokensWithROP(username: username, password: password, delegate: delegate())
        ```
        {: codeblock}

    * Anmeldung
        ```swift
        class delegate : AuthorizationDelegate {
          public func onAuthorizationSuccess(accessToken: AccessToken?, identityToken: IdentityToken?, response:Response?) {
             if accessToken == nil && identityToken == nil {
              /* email verification is required */
              return
             }
           /* User authenticated */
          }

          public func onAuthorizationCanceled() {
              /* Sign up cancelled by the user */
          }

          public func onAuthorizationFailure(error: AuthorizationError) {
              /* Exception occurred */
          }
        }

        AppID.sharedInstance.loginWidget?.launchSignUp(delegate: delegate())
        ```
        {: codeblock}

    * Vergessenes Kennwort
        ```swift
        class delegate : AuthorizationDelegate {
           public func onAuthorizationSuccess(accessToken: AccessToken?, identityToken: IdentityToken?, response:Response?) {
              /* forgot password finished, in this case accessToken and identityToken will be null. */
           }

           public func onAuthorizationCanceled() {
               /* forgot password canceled by the user */
           }

           public func onAuthorizationFailure(error: AuthorizationError) {
               /* Exception occurred */
           }
        }

        AppID.sharedInstance.loginWidget?.launchForgotPassword(delegate: delegate())
        ```
        {: codeblock}

    * Kontodetails
        ```swift

         class delegate : AuthorizationDelegate {
             public func onAuthorizationSuccess(accessToken: AccessToken?, identityToken: IdentityToken?, response:Response?) {
             }

             public func onAuthorizationCanceled() {
             }

             public func onAuthorizationFailure(error: AuthorizationError) {
             }
         }

         AppID.sharedInstance.loginWidget?.launchChangeDetails(delegate: delegate())
        ```
        {: codeblock}

    * Kennwortänderung
        ```swift
         class delegate : AuthorizationDelegate {
             public func onAuthorizationSuccess(accessToken: AccessToken?, identityToken: IdentityToken?, response:Response?) {
             }

             public func onAuthorizationCanceled() {
             }

             public func onAuthorizationFailure(error: AuthorizationError) {
             }
          }

          AppID.sharedInstance.loginWidget?.launchChangePassword(delegate: delegate())
        ```
        {: codeblock}


## Schritt 5. App testen
{: #testing-appid}

Ist alles richtig konfiguriert? Jetzt können Sie testen, ob Sie alles richtig konfiguriert haben.

1. Öffnen Sie Ihre App mit dem Xcode-Emulator.
2. Durchlaufen Sie den Prozess für die Anmeldung bei Ihrer Anwendung
mithilfe der GUI. Falls Sie das Cloudverzeichnis konfiguriert haben, stellen
Sie fest, ob alle Anzeigen wie von Ihnen beabsichtigt aufgerufen werden.
3. Aktualisieren Sie die Identitätsprovider oder die Anzeige für das
Anmeldewidget im {{site.data.keyword.appid_short_notm}}-Dashboard.
4. Wiederholen Sie die Schritte 1 und 2, um die sofort implementierten
Änderungen anzuzeigen. Aktualisierungen des App-Codes sind nicht erforderlich.

Gibt es Probleme? Werfen Sie einen Blick in den Abschnitt zur [Fehlerbehebung von {{site.data.keyword.appid_short_notm}}](/docs/services/appid?topic=appid-troubleshooting).

## Nächste Schritte
{: #next-appid notoc}

Hervorragend! Sie haben Ihre App mit einer Sicherheitsstufe ausgestattet. Nutzen Sie diesen Schwung und probieren
Sie gleich eine der folgenden Optionen aus:

* Lesen Sie weitere Informationen zu
{{site.data.keyword.appid_short_notm}} in der
[Dokumentation](/docs/services/appid?topic=appid-getting-started#getting-started). Dort
erfahren Sie auch, wie Sie alle gebotenen Funktionen nutzen können.
* Starter-Kits bieten eine der schnellsten Möglichkeiten zur Nutzung des Leistungsspektrums von {{site.data.keyword.cloud_notm}}. Im [Dashboard für Entwickler für Mobilgeräte](https://{DomainName}/developer/mobile/dashboard){: new_window} ![Symbol für externen Link](../../icons/launch-glyph.svg "Symbol für externen Link") werden die verfügbaren Starter-Kits angezeigt. Laden Sie den Code herunter. Führen Sie die App aus!
