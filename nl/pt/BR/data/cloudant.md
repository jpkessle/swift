---

copyright:
  years: 2017, 2019
lastupdated: "2019-06-06"

keywords: cloudant swift, store data swift, dbaas swift, cloudant instance swift, initialize sdk swift, create document swift, read document swift, delete document swift

subcollection: swift

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Armazenando documentos no  {{site.data.keyword.cloud_notm}}
{: #cloudant}

O {{site.data.keyword.cloudantfull}} é um Banco de dados como um serviço (DBaaS) orientado a documentos. Ele armazena dados como documentos no formato JSON. Ele é construído com escalabilidade, alta disponibilidade e durabilidade em mente, além de ser fácil de configurar para uso em aplicativos Swift. Ele é fornecido com uma ampla variedade de opções de indexação que incluem MapReduce,
Consulta do {{site.data.keyword.cloudant_short_notm}},
índice de texto total
e indexação geoespacial. Os recursos de replicação facilitam a manutenção de dados em sincronia entre os
clusters de banco de dados, PCs desktop e dispositivos móveis. 
{: shortdesc}

Para todas as maneiras que é possível usar o {{site.data.keyword.cloudant_short_notm}}, consulte [Noções básicas do {{site.data.keyword.cloudant_short_notm}}](/docs/services/Cloudant/basics?topic=cloudant-ibm-cloudant-basics#cloudant-nosql-db-basics).

## Antes de iniciar
{: #prereqs-cloudant}

Primeiro, verifique se os pré-requisitos a seguir estão prontos para execução:
 * CocoaPods (versão 1.1.0 ou superior)
 * iOS (versão 9 ou superior)
 * MacOS (versão 10.11.5 ou superior)
 * Xcode (versão 9.0.1 ou superior)

O [{{site.data.keyword.cloudant_short_notm}} SDK for Swift](https://github.com/cloudant/swift-cloudant){: new_window} ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo") é construído com o Swift 3.2. Se você estiver planejando usar o {{site.data.keyword.cloudant_short_notm}} com o Kitura, verifique a [Biblioteca Kitura-CouchDB ](https://github.com/IBM-Swift/Kitura-CouchDB){: new_window} ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo"), que é construída com o Swift 4.0.
{: tip}

## Etapa 1. Criando uma instância do  {{site.data.keyword.cloudant_short_notm}}
{: #create-instance-cloudant}

Consulte o tutorial [Criando uma instância do IBM Cloudant no {{site.data.keyword.cloud_notm}}](/docs/services/Cloudant/tutorials?topic=cloudant-creating-an-ibm-cloudant-instance-on-ibm-cloud#creating-an-ibm-cloudant-instance-on-ibm-cloud){:new_window} para criar uma instância do serviço.

## Etapa 2. Instalando o SDK
{: #install-sdk-cloudant}

### Instalando o iOS Swift SDK
{: #install-swift-sdk-cloudant}

Use o Swift Cloudant SDK para ajudar a tornar a codificação do app mais fácil. O SDK deve ser instalado em seu código de app.

1. Abra o diretório de projeto do Xcode existente para o `Podfile`.
2. No destino de seus projetos, inclua uma dependência para o pod `SwiftCloudant`. Certifique-se
de que o `use_frameworks!` também está sob o seu destino, conforme mostrado no exemplo a seguir.
    ```
    target '<yourTarget>' do
      use_frameworks!
        pod 'SwiftCloudant', :git => 'https://github.com/cloudant/swift-cloudant.git'
    end
    ```
    {: screen}

3. Faça download da dependência  ` SwiftCloudant ` .
    ```
    instalação do pod
    ```
    {: codeblock}

### Instalando o SDK do Swift do Lado do Servidor
{: #install-serverside-cloudant}

Para usar com o Swift Package Manager para desenvolvimento do lado do servidor, inclua a linha a seguir em suas dependências no `Package.swift`:
```swift
.Package (url: "https: //github.com/cloudant/swift-cloudant.git")
```
{: codeblock}

## Etapa 3. Inicializando o SDK
{: #initialize-cloudant}

Depois de inicializar o SDK no app, é possível começar usando o {{site.data.keyword.cloudant_short_notm}} para armazenar dados.

1.  Inclua a importação a seguir em seu arquivo swift `AppDelegate.swift` ou do lado do servidor.
    ```swift
    import SwiftCloudant
    ```
    {: codeblock}

2. Inicialize a conexão com o banco de dados.
    ```swift
    let cloudantURL = NSURL(string: "https://username.cloudant.com")!
    let client = CouchDBClient(url: cloudantURL, username: "username", password: "password")
    let dbName = "database"
    ```
    {: codeblock}

### Operações Básicas
{: #basic-operations-cloudant}

Essas operações básicas ilustram as ações fundamentais para criar, ler e excluir seus documentos, usando o cliente inicializado.

#### Criar um documento
```swift
let create = PutDocumentOperation(id: "doc1", body: ["hello": "world"], databaseName: dbName) {(response, httpInfo, error) in
    if let error = error {
        print("Encountered an error while creating a document. Erro: \(error)") } else {
        print("Created document \(response?["id "]) com ID de revisão \ (response? ["rev "])")
    }
} client.add (operação: criar)
```
{: codeblock}

#### Ler um documento
```swift
let read = GetDocumentOperation(id: "doc1", databaseName: dbName) { (response, httpInfo, error) in
    if let error = error {
        print("Encountered an error while reading a document. Erro: \(error)") } else {
        print ("Documento de leitura: \ (resposta)")
    }   
}
client.add(operation: read)
```
{: codeblock}

#### Excluir um documento
```swift
let delete = DeleteDocumentOperation(id: "doc1",
    revision: "1-revisionidhere",
    databaseName: dbName) { (response, httpInfo, error) in
    if let error = error {
        print("Encountered an error while deleting a document. Erro: \(error)") } else {
        print ("Documento excluído")
    }   
}
client.add(operation: delete)
```
{: codeblock}

## Etapa 4. Testando o app
{: #cloudant_testing}

Tudo está configurado corretamente? Teste-o para fora!

1. Execute seu aplicativo, certificando-se de iniciar a inicialização e as respectivas operações, como criar um documento.
2. Retorne para a instância de serviço do {{site.data.keyword.cloudant_short_notm}} criada anteriormente em seu navegador da web e abra o painel de serviço.
3. Selecione o banco de dados usado e será possível ver os documentos no painel.

Tendo problemas? Verifique a [Referência de API do {{site.data.keyword.cloudant_short_notm}}](/docs/services/Cloudant/api?topic=cloudant-ibm-cloudant-basics#api-reference-overview).

## Próximas etapas
{: #cloudant_next notoc}

Ótimo trabalho! Você incluiu um nível de persistência segura em seu app. Tente uma das opções a seguir para manter o ritmo:

* Visualize o código-fonte do [{{site.data.keyword.cloudant_short_notm}} SDK for Swift](https://github.com/cloudant/swift-cloudant){: new_window} ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo").
* Os Kits de iniciador são uma das maneiras mais rápidas de usar os recursos do {{site.data.keyword.cloud_notm}}. O kit do iniciador **Rolagem infinita com o Cloudant NoSQL for iOS** ilustra como estender um `ViewController` para exibir dados usando a paginação. Esse padrão de app é comum para os desenvolvedores do iOS e é um ótimo exemplo para ilustrar os recursos do {{site.data.keyword.cloudant_short_notm}}. Visualize os kits de iniciador disponíveis no [Painel do Mobile Developer ](https://{DomainName}/developer/mobile/dashboard){: new_window} ![Ícone de link externo](../../icons/launch-glyph.svg "Ícone de link externo"). Faça download do código. Execute o app.
* Obtenha mais informações e aproveite todos os recursos que o {{site.data.keyword.cloudant_short_notm}} tem para oferecer: [verifique os documentos](/docs/services/Cloudant?topic=cloudant-ibm-cloudant-basics).
