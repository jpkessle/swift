---

copyright:
  years: 2018
lastupdated: "2018-08-17"

---
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Configuración del entorno Swift
{: #configuration}

El desarrollo de Cloud Native tiene dos prácticas estrechamente relacionadas que se cruzan en la forma en que manejan los datos de configuración. La primera es que debe crear artefactos inmutables para minimizar la probabilidad de que se introduzcan errores a medida que la aplicación pase de desarrollo a producción. La segunda es que debe esforzarse por la paridad entre sus entornos de desarrollo y de producción para evitar problemas creados por el código específico del entorno. 

La gestión de la configuración de servicio y las credenciales (enlaces de servicio) varía entre plataformas. Cloud Foundry almacena detalles de enlace de servicio en un objeto JSON en serie que se pasa a la aplicación como una variable de entorno (`VCAP_SERVICES`). Kubernetes almacena los enlaces de servicio como atributos JSON o sin formato en serie en `ConfigMaps` o `Secrets`, que se pueden pasar a la aplicación contenerizada como variables de entorno o montarse como un volumen temporal. El desarrollo local tiene su propia configuración, ya que las pruebas locales a menudo son un derivado simplificado de lo que se ejecuta en la nube. Trabajar con estas variaciones de una forma portátil sin tener vías de acceso de código específicas del entorno puede ser un reto.

Puede seguir directrices sencillas que le pueden ayudar a escribir aplicaciones portátiles, y algunos programas de utilidad que puede utilizar para encapsular los enlaces de servicio (u otra configuración) en ubicaciones específicas del entorno. Tanto si necesita añadir soporte de nube a las aplicaciones existentes como crear apps con los Kits de iniciación, el objetivo es proporcionar la portabilidad máxima para las apps independientemente de la plataforma de despliegue.

## Adición de {{site.data.keyword.cloud_notm}} a aplicaciones Swift existentes
{: #addcloud-env}

La vía de acceso para obtener determinados valores de entorno puede diferir de un entorno de nube a otro. La biblioteca [CloudEnvironment](https://github.com/IBM-Swift/CloudEnvironment.git) abstrae la configuración del entorno y las credenciales de varios proveedores de nube para que la app Swift pueda acceder a la información de forma coherente ejecutándose localmente o en Cloud Foundry, Kubernetes o {{site.data.keyword.openwhisk}}. La abstracción de credenciales la proporciona la biblioteca `CloudEnvironment`, que utiliza internamente [Swift-cfenv](https://github.com/IBM-Swift/Swift-cfenv) para la configuración de Cloud Foundry y [Configuración](https://github.com/IBM-Swift/Configuration) como gestor de configuración.

Con `CloudEnvironment`, puede abstraer detalles de nivel bajo del código de origen de la aplicación definiendo una clave de búsqueda que la aplicación Swift puede aprovechar para buscar su valor correspondiente.

La biblioteca `CloudEnvironment` proporciona una clave de búsqueda coherente que se puede utilizar en el código fuente. A continuación, la biblioteca busca en una matriz de patrones de búsqueda para buscar el objeto JSON que contiene los atributos de configuración o las credenciales de servicio. 

### Adición del paquete de CloudEnvironment a la aplicación Swift
Para aprovechar el paquete `CloudEnvironment` en la aplicación Swift, especifíquelo en la sección **dependencies:** del archivo `Package.swift`:
```swift
.package(url: "https://github.com/IBM-Swift/CloudEnvironment.git", from: "8.0.0"),
```
{: codeblock}

A continuación, añada el código de instrumentación siguiente a la aplicación:
```swift
import CloudEnvironment

let cloudEnv = CloudEnv()
```
{: codeblock}

### Acceso a credenciales
Ahora que se inicializa la biblioteca `CloudEnvironment`, puede acceder a las credenciales tal como se muestra en los ejemplos siguientes:
```swift
let cloudantCredentials = cloudEnv.getCloudantCredentials(name: "cloudant-credentials")
// cloudantCredentials.username, cloudantCredentials.password, cloudantCredentials.url, etc.
let objStorageCredentials = cloudEnv.getObjectStorageCredentials(name: "object-storage-credentials")
// objStorageCredentials.username, objStorageCredentials.password, objStorageCredentials.projectID, etc.

let service1Credentials = cloudEnv.getDictionary("service1-credentials")
let service1CredentialsStr = cloudEnv.getString("service1-credentials")

// Obtener un PUERTO y URL predeterminados
let port = cloudEnv.port
let url = cloudEnv.url
```
{: codeblock}

En este ejemplo se proporciona acceso a los conjuntos de credenciales para servicios, que ahora se pueden utilizar para inicializar conexiones con estos [servicios soportados o un ejemplo genérico](https://github.com/IBM-Swift/CloudEnvironment#supported-services). Consulte [Swift-cfenv](https://github.com/IBM-Swift/Swift-cfenv#api) para la configuración específica de Cloud Foundry y [detalles de configuración](https://github.com/IBM-Swift/Configuration) sobre la carga de datos de configuración.

## Comprensión de las credenciales de servicio
{: #service_creds}

La biblioteca `CloudEnvironment` utiliza un archivo denominado `mappings.json`, que se encuentra en el directorio `config`, para comunicar dónde se almacenan las credenciales para cada servicio. El archivo `mappings.json` da soporte a la búsqueda de valores que utilizan los tres tipos de patrón de búsqueda siguientes:
- **`cloudfoundry`**: Un tipo de patrón que se utiliza para buscar un valor en la variable de entorno de servicios de Cloud Foundry (`VCAP_SERVICES`).
- **`env`**: Un tipo de patrón utilizado para buscar un valor que se correlaciona con una variable de entorno, como en Kubernetes o Functions.
- **`file`**: Un tipo de patrón que se utiliza para buscar un valor en un archivo JSON. La vía de acceso debe ser relativa a la carpeta raíz de la aplicación Swift.

La matriz de valores que están asociados a una clave de búsqueda se busca en el orden en el que aparecen.

A continuación se muestra un ejemplo de un archivo `mappings.json`:
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

En este ejemplo, `cloudant-credentials` y `object-storage-credentials` son las claves de búsqueda que utiliza la aplicación Swift para buscar las credenciales correspondientes. Según la matriz de patrones de búsqueda, el gestor de configuración busca en primer lugar `my-awesome-cloudant-db` en `VCAP_SERVICES`, a continuación busca `my_awesome_cloudant_db_credentials` como una variable de entorno y, si no está definido, busca el contenido de `my-awesome-object-storage-credentials.json` en la carpeta `localdev`. 

Cuando la aplicación se ejecuta localmente, puede utilizar las credenciales almacenadas en un archivo, como `localdev/my-awesome-object-storage-credentials.json`, tal como se muestra en el ejemplo anterior. La información de conexión para los servicios a los que desea acceder localmente, como por ejemplo nombre de usuario, contraseña y nombre de host, se debe almacenar en este archivo. 

Por motivos de seguridad, los archivos de credenciales no se deben añadir a los repositorios. En el ejemplo anterior, se utiliza una carpeta `localdev` para almacenar las credenciales locales, de modo que debe añadir esta carpeta al archivo `.gitignore` para evitar una confirmación accidental. Si utiliza una app de Kit de iniciación, esta carpeta se crea para usted y está presente en el archivo `.gitignore`.

Para obtener más información sobre el archivo `mappings.json`, consulte la sección [Comprensión de la credencial de servicio](configuration.html#service_creds).

## Utilización del gestor de configuración de Swift desde apps del Kit de iniciación

Las apps Swift creadas con [Kits de iniciación](https://console.bluemix.net/developer/appledevelopment/starter-kits/) se proporcionan automáticamente con las credenciales y la configuración necesarias para ejecutar localmente, y también en muchos entornos de despliegue de Cloud (CF, K8s, VSI y Functions). La creación básica del gestor de configuración se puede encontrar en `Sources/Application/Application.swift`. Cuando crea una app de Kit de iniciación basada en Swift con servicios, se crea una carpeta `config`, que contiene un `mappings.json`. Si descarga la app, la carpeta `config` contiene un archivo `localdev-config.json`, que tiene todas las credenciales para los servicios y está presente en el archivo `.gitignore`.

## Pasos siguientes
{: #next notoc}

Consulte nuestras tres bibliotecas para que sus aplicaciones se abstraigan de sus entornos:

* [CloudEnvironment](https://github.com/ibm-developer/ibm-cloud-env)
* [Swift-cfenv](https://github.com/IBM-Swift/Swift-cfenv)
* [Configuración](https://github.com/IBM-Swift/Configuration)