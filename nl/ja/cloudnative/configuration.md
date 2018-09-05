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

# Swift 環境の構成
{: #configuration}

クラウド・ネイティブ開発には、構成データの処理方法とも関連し合う、2 つの密接に関連する慣例があります。1 つ目に、アプリケーションの開発から実動への過程でエラーが入り込む可能性を最小限に抑えるために、変更不可能な成果物をビルドする必要があります。2 つ目に、環境固有のコードで問題が発生しないように、開発環境と実稼働環境の間でパリティーを確保する必要があります。 

サービス構成と資格情報 (サービス・バインディング) の管理は、プラットフォーム間で異なります。Cloud Foundry は、環境変数 (`VCAP_SERVICES`) としてアプリケーションに渡される、ストリング化された JSON オブジェクト内にサービス・バインディングの詳細情報を保管します。Kubernetes はサービス・バインディングを、ストリング化された JSON 属性またはフラットな属性として `ConfigMaps` または `Secrets` 内に格納するので、コンテナー化されたアプリケーションに環境変数として渡したり、一時ボリュームとしてマウントしたりできます。ローカル開発には独自の構成があります。この構成では、ローカル・テストは、多くの場合クラウド内で実行されているものを単純化した派生物です。これらの違いがある中で、環境固有のコード・パスを使用せずに移植可能な方法で作業することは容易ではありません。

簡単なガイドラインに従うことにより、移植可能なアプリケーションを書いたり、環境固有の場所に検索サービス・バインディング (またはその他の構成) をカプセル化するために使用できるユーティリティーを書いたりすることができます。既存のアプリケーションにクラウド・サポートを追加する必要があるか、スターター・キットを使用してアプリを作成するかにかかわらず、目標はデプロイメント・プラットフォームに関係なくアプリの移植性を最大にすることです。

## 既存の Swift アプリケーションへの {{site.data.keyword.cloud_notm}} の追加
{: #addcloud-env}

クラウド環境が異なると、特定の環境値を取得するためのパスも異なる可能性があります。[CloudEnvironment](https://github.com/IBM-Swift/CloudEnvironment.git) ライブラリーは各種クラウド・プロバイダーからの環境構成や資格情報を抽象化するので、Swift アプリをローカルで実行しても、Cloud Foundry、Kubernetes、または {{site.data.keyword.openwhisk}} 内で実行しても、情報へのアクセスに一貫性があります。資格情報を抽象化するには `CloudEnvironment` ライブラリーを使用します。このライブラリーは、Cloud Foundry 構成には [Swift-cfenv](https://github.com/IBM-Swift/Swift-cfenv) を内部使用し、構成マネージャーとしては [Configuration](https://github.com/IBM-Swift/Configuration) を内部使用します。

`CloudEnvironment` を使用すると、Swift アプリケーションが対応する値の検索に利用できるルックアップ・キーを定義して、アプリケーションのソース・コードから低水準の詳細情報を抽象化できます。

`CloudEnvironment` ライブラリーは、ソース・コード内で使用できる、一貫性のあるルックアップ・キーを提供します。さらにこのライブラリーは、検索パターンの配列全体を検索して、構成属性やサービス資格情報を含む JSON オブジェクトを検出します。 

### Swift アプリケーションへの CloudEnvironment パッケージの追加
Swift アプリケーション内で `CloudEnvironment` パッケージを利用するには、`Package.swift` ファイルの **dependencies:** セクション内で以下のように指定します。
```swift
.package(url: "https://github.com/IBM-Swift/CloudEnvironment.git", from: "8.0.0"),
```
{: codeblock}

次に、以下の計測コードをアプリケーションに追加します。
```swift
import CloudEnvironment

let cloudEnv = CloudEnv()
```
{: codeblock}

### 資格情報へのアクセス
この時点で、`CloudEnvironment` ライブラリーが初期化されているので、以下の例のように資格情報にアクセスできます。
```swift
let cloudantCredentials = cloudEnv.getCloudantCredentials(name: "cloudant-credentials")
// cloudantCredentials.username, cloudantCredentials.password, cloudantCredentials.url, etc.
let objStorageCredentials = cloudEnv.getObjectStorageCredentials(name: "object-storage-credentials")
// objStorageCredentials.username, objStorageCredentials.password, objStorageCredentials.projectID, etc.

let service1Credentials = cloudEnv.getDictionary("service1-credentials")
let service1CredentialsStr = cloudEnv.getString("service1-credentials")

// Get a default PORT and URL
let port = cloudEnv.port
let url = cloudEnv.url
```
{: codeblock}

この例では、サービスに関する一連の資格情報にアクセスできるようになります。この時点でこれらの資格情報を使用して、これらの[サポートされているサービスまたは汎用ディクショナリー](https://github.com/IBM-Swift/CloudEnvironment#supported-services)への接続を初期化できます。Cloud Foundry 固有の構成については [Swift-cfenv](https://github.com/IBM-Swift/Swift-cfenv#api) を確認し、構成データのロードについては[構成の詳細](https://github.com/IBM-Swift/Configuration)を確認してください。

## サービス資格情報について
{: #service_creds}

`CloudEnvironment` ライブラリーは、`config` ディレクトリー内にある `mappings.json` という名前のファイルを使用して、サービスごとに資格情報が保管される場所を伝えます。`mappings.json` ファイルは、以下の 3 つの検索パターン・タイプを使用する、値の検索をサポートしています。
- **`cloudfoundry`** - Cloud Foundry のサービス環境変数 (`VCAP_SERVICES`) 内での値の検索に使用されるパターン・タイプ。
- **`env`** - Kubernetes や Functions のように、環境変数にマップされている値の検索に使用されるパターン・タイプ。
- **`file`** - JSON ファイル内の値の検索に使用されるパターン・タイプ。パスは、Swift アプリケーションのルート・フォルダーに対する相対パスでなければなりません。

ルックアップ・キーに関連付けられている値の配列は、配列で示されている順序で検索されます。

以下に `mappings.json` ファイルの例を示します。
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

この例で、`cloudant-credentials` と `object-storage-credentials`  は、Swift アプリケーションが対応する資格情報の検索に使用するルックアップ・キーです。検索パターンの配列に従って、構成マネージャーは最初に `VCAP_SERVICES` 内で `my-awesome-cloudant-db` を検索し、次に環境変数として `my_awesome_cloudant_db_credentials` を検索し、それが定義されていない場合は `localdev` フォルダー内で `my-awesome-object-storage-credentials.json` の内容を検索します。 

アプリケーションがローカルで実行される場合、前の例に示されている `localdev/my-awesome-object-storage-credentials.json` のような、ファイル内に保管されている資格情報を使用できます。ユーザー名、パスワード、ホスト名などの、ローカルにアクセスしようとしているサービスに関する接続情報は、すべてこのファイル内に保管されます。 

セキュリティー上の理由により、資格情報ファイルはリポジトリーに追加されません。前の例では、`localdev` フォルダーを使用してローカルの資格情報が保管されるので、誤ってコミットされないようにするには、このフォルダーを `.gitignore` ファイルに追加しなければなりません。スターター・キット・アプリを使用している場合は、このフォルダーは自動的に作成され、`.gitignore` ファイルに示されます。

`mappings.json` ファイルの詳細については、[サービス資格情報の概要](configuration.html#service_creds)のセクションを確認してください。

## スターター・キット・アプリからの Swift 構成マネージャーの使用

[スターター・キット](https://console.bluemix.net/developer/appledevelopment/starter-kits/)で作成された Swift アプリケーションには、資格情報と構成が自動的に付属していますが、この構成は、ローカルに実行するか、多くの Cloud デプロイメント環境 (CF、K8s、VSI、Functions) 内で実行する必要があります。
構成マネージャーの基本的な作成内容は、`Sources/Application/Application.swift` にあります。サービスを使用して Swift ベースのスターター・キット・アプリを作成すると、`mappings.json` が含まれる `config` フォルダーが作成されます。アプリをダウンロードすると、サービスに関するすべての資格情報がある `localdev-config.json` ファイルが `config` フォルダーに組み込まれ、このフォルダーが `.gitignore` ファイルに示されます。

## 次のステップ
{: #next notoc}

アプリケーションがそれぞれの環境から自己抽象化するのに役立つ以下の 3 つのライブラリーを確認します。

* [CloudEnvironment](https://github.com/ibm-developer/ibm-cloud-env)
* [Swift-cfenv](https://github.com/IBM-Swift/Swift-cfenv)
* [Configuration](https://github.com/IBM-Swift/Configuration)