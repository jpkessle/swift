---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-07"

keywords: swiftmetrics-dash, swiftmetrics, prometheus swift, application metrics swift, swift performance, slow swift, swift dashboard, metris swift

subcollection: swift

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Utilisation de métriques d'application avec les applications Swift
{: #metrics}

Les métriques d'application sont importantes pour la surveillance des performances de votre application. Il est essentiel de disposer d'une vue en direct des métriques (UC, mémoire, latence et mesures HTTP) pour garantir un fonctionnement efficace de votre application dans le temps. Des services Kubernetes et Cloud Foundry, par exemple la [mise à l'échelle automatique](/docs/services/Auto-Scaling?topic=Auto-Scaling-get-started), reposent sur ces métriques pour déterminer quand il est nécessaire d'ajouter ou de retirer de manière dynamique des instances en fonction de la charge, et de nettoyer des instances qui ne sont plus nécessaires, et ce afin de réduire les coûts.

Les métriques d'application sont capturées sous forme de données de séries temporelles. L'agrégation et la visualisation des métriques capturées permettent d'identifier des problèmes courants liés aux performances tels que :

* Faibles temps de réponse HTTP sur tout ou partie des routes
* Débit médiocre dans l'application
* Pics de demandes entraînant des ralentissements
* Utilisation de l'UC plus élevée que prévu
* Utilisation de la mémoire élevée ou en augmentation (possible fuite de mémoire)

## Ajout de métriques d'application à votre application Swift existante
{: #add-appmetrics-existing}

Utilisez [Application Metrics for Swift](https://developer.ibm.com/swift/monitoring-diagnostics/application-metrics-for-swift/){: new_window} ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe") pour ajouter la surveillance des performances à votre application Swift. Application Metrics for Swift comporte deux bibliothèques : `SwiftMetrics` et `SwiftMetricsDash`.

* La bibliothèque `SwiftMetrics` est une bibliothèque d'instrumentation complète qui collecte et rassemble les métriques de votre application. Elle comporte plusieurs extensions, notamment un module Kitura pour les métriques HTTP, une [prise en charge Prometheus](https://github.com/RuntimeTools/SwiftMetrics#prometheus-support){: new_window} ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe"), et un [émetteur](https://github.com/RuntimeTools/SwiftMetrics#application-metrics-for-swift-agent){: new_window} ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe") autonome.

* La bibliothèque `SwiftMetricsDash` consomme les métriques qui sont produites par `SwiftMetrics` et fournit un tableau de bord intégré pour la visualisation.

Pour activer l'API de contrôle de base, ajoutez `SwiftMetrics` à la section **dependencies:** dans votre fichier `Package.swift` et assurez-vous de l'ajouter à la cible appropriée :
```swift
.package(url: "https://github.com/RuntimeTools/SwiftMetrics.git", from: "2.4.0")
```
{: codeblock}

Ajoutez le code d'instrumentation suivant pour initialiser le code `SwiftMetrics` :
```swift
import SwiftMetrics
import SwiftMetricsKitura

let metrics = try SwiftMetrics()
SwiftMetricsKitura(swiftMetricsInstance: metrics)
let monitoring = metrics.monitor()
```
{: codeblock}

Ajoutez le code suivant à l'exemple d'origine afin de fournir les métriques dans le tableau de bord de surveillance de surveillance :
```swift
import SwiftMetricsDash

let smd = try SwiftMetricsDash(swiftMetricsInstance : metrics)
```  
{: codeblock}

Par défaut, `SwiftMetricsDash` démarre son propre serveur Kitura, puis présente la page sous `http://<hostname>:<port>/swiftmetrics-dash`. Accédez au tableau de bord pour voir vos nouvelles métriques d'application, y compris les demandes HTTP et la latence de boucle d'événements.

## Utilisation de métriques d'application dans les kits de démarrage
{: #appmetrics-starterkits}

Les applications Swift côté serveur qui sont créées à partir des kits de démarrage incluent `SwiftMetrics`, `SwiftMetricsDash` et `SwiftMetricsPrometheus`, et sont donc prêtes à être utilisées dans des environnements Kubernetes utilisant des noeuds finaux Prometheus pour la collecte de métriques.

Le code `SwiftMetrics` se trouve dans `/Sources/Application/Metrics.swift` :
```swift
import Kitura
import SwiftMetrics
import SwiftMetricsDash
import SwiftMetricsPrometheus
import LoggerAPI

var swiftMetrics: SwiftMetrics?
var swiftMetricsDash: SwiftMetricsDash?
var swiftMetricsPrometheus: SwiftMetricsPrometheus?

func initializeMetrics(router: Router) {
    do {
        let metrics = try SwiftMetrics()
        let dashboard = try SwiftMetricsDash(swiftMetricsInstance: metrics, endpoint: router)
        let prometheus = try SwiftMetricsPrometheus(swiftMetricsInstance: metrics, endpoint: router)

        swiftMetrics = metrics
        swiftMetricsDash = dashboard
        swiftMetricsPrometheus = prometheus
        Log.info("Initialized metrics.")
    } catch {
        Log.warning("Failed to initialize metrics: \(error)")
    }
}
```
{: codeblock}

Une fois votre application lancée, vous pouvez accéder au tableau de bord à partir du noeud final `/swiftmetrics-dash`.

Par défaut, `SwiftMetricsPrometheus` fournit le [noeud final Prometheus](https://prometheus.io/){: new_window} ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe") sous `http://<hostname>:<port>/metrics`.
