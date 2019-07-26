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

# 將文件儲存在 {{site.data.keyword.cloud_notm}} 中
{: #cloudant}

{{site.data.keyword.cloudantfull}} 是文件導向的「資料庫即服務 (DBaaS)」。它會將資料儲存為 JSON 格式的文件。其建置在可調整性、高可用性以及延續性上，可輕鬆地配置成在 Swift 應用程式中使用。並且具有各種檢索選項，包括 MapReduce、{{site.data.keyword.cloudant_short_notm}} 查詢、全文檢索及地理空間檢索。抄寫功能讓您能輕鬆保持資料庫叢集、桌上型電腦和行動裝置之間的資料同步。
{: shortdesc}

如需可以使用 {{site.data.keyword.cloudant_short_notm}} 的所有方式，請參閱 [{{site.data.keyword.cloudant_short_notm}} 基本概念](/docs/services/Cloudant/basics?topic=cloudant-ibm-cloudant-basics#cloudant-nosql-db-basics)。

## 開始之前
{: #prereqs-cloudant}

首先，請確定您具備下列必要條件：
 * CocoaPods（1.1.0 版或更新版本）
 * iOS（第 9 版或更新版本）
 * MacOS（10.11.5 版或更新版本）
 * Xcode（9.0.1 版或更新版本）

[{{site.data.keyword.cloudant_short_notm}} SDK for Swift ](https://github.com/cloudant/swift-cloudant){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示") 使用 Swift 3.2 建置。如果您計劃搭配使用 {{site.data.keyword.cloudant_short_notm}} 與 Kitura，請參閱使用 Swift 4.0 建置的 [Kitura-CouchDB Library ](https://github.com/IBM-Swift/Kitura-CouchDB){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")。
{: tip}

## 步驟 1. 建立 {{site.data.keyword.cloudant_short_notm}} 實例
{: #create-instance-cloudant}

請參閱[在 {{site.data.keyword.cloud_notm}} 上建立 IBM Cloudant 實例指導教學](/docs/services/Cloudant/tutorials?topic=cloudant-creating-an-ibm-cloudant-instance-on-ibm-cloud#creating-an-ibm-cloudant-instance-on-ibm-cloud){:new_window}，以建立服務的實例。

## 步驟 2. 安裝 SDK
{: #install-sdk-cloudant}

### 安裝 iOS Swift SDK
{: #install-swift-sdk-cloudant}

使用 Swift Cloudant SDK，協助您輕鬆地以程式碼撰寫應用程式。SDK 必須安裝在應用程式程式碼中。

1. 開啟現有 Xcode 專案目錄的 `Podfile`。
2. 在您的專案目標下，新增 `SwiftCloudant` pod 的相依關係。請確定 `use_frameworks!` 指令也在您的目標下，如下列範例所示。
    ```
    target '<yourTarget>' do
      use_frameworks!
        pod 'SwiftCloudant', :git => 'https://github.com/cloudant/swift-cloudant.git'
    end
    ```
    {: screen}

3. 下載 `SwiftCloudant` 相依關係。
    ```
    pod install
    ```
    {: codeblock}

### 安裝伺服器端 Swift SDK
{: #install-serverside-cloudant}

若要搭配使用 Swift Package Manager 來進行伺服器端開發，請在 `Package.swift` 中，將下列這一行新增至相依關係中：
```swift
.Package(url: "https://github.com/cloudant/swift-cloudant.git")
```
{: codeblock}

## 步驟 3. 起始設定 SDK
{: #initialize-cloudant}

在應用程式中起始設定 SDK 之後，您可以開始使用 {{site.data.keyword.cloudant_short_notm}} 來儲存資料。

1.  將下列 import 新增至 `AppDelegate.swift` 或伺服器端 Swift 檔。
    ```swift
    import SwiftCloudant
    ```
    {: codeblock}

2. 起始設定與資料庫的連線。
    ```swift
    let cloudantURL = NSURL(string: "https://username.cloudant.com")!
    let client = CouchDBClient(url: cloudantURL, username: "username", password: "password")
    let dbName = "database"
    ```
    {: codeblock}

### 基本作業
{: #basic-operations-cloudant}

這些基本作業說明使用已起始設定的用戶端來建立、讀取及刪除文件的基本動作。

#### 建立文件
```swift
let create = PutDocumentOperation(id: "doc1", body: ["hello": "world"], databaseName: dbName) {(response, httpInfo, error) in
    if let error = error {
        print("Encountered an error while creating a document. Error: \(error)")
    } else {
        print("Created document \(response?["id"]) with revision id \(response?["rev"])")
    }
}
client.add(operation: create)
```
{: codeblock}

#### 讀取文件
```swift
let read = GetDocumentOperation(id: "doc1", databaseName: dbName) { (response, httpInfo, error) in
    if let error = error {
        print("Encountered an error while reading a document. Error: \(error)")
    } else {
        print("Read document: \(response)")
    }   
}
client.add(operation: read)
```
{: codeblock}

#### 刪除文件
```swift
let delete = DeleteDocumentOperation(id: "doc1",
    revision: "1-revisionidhere",
    databaseName: dbName) { (response, httpInfo, error) in
    if let error = error {
        print("Encountered an error while deleting a document. Error: \(error)")
    } else {
        print("Document deleted")
    }   
}
client.add(operation: delete)
```
{: codeblock}

## 步驟 4. 測試應用程式
{: #cloudant_testing}

所有項目都正確配置嗎？請測試看看！

1. 執行應用程式，確定啟動起始設定及個別作業，例如，建立文件。
2. 回到先前在 Web 瀏覽器中建立的 {{site.data.keyword.cloudant_short_notm}} 服務實例，然後開啟服務儀表板。
3. 選取使用的資料庫，您可在儀表板中查看文件。

有困難嗎？請參閱 [{{site.data.keyword.cloudant_short_notm}} API 參考資料](/docs/services/Cloudant/api?topic=cloudant-ibm-cloudant-basics#api-reference-overview)。

## 後續步驟
{: #cloudant_next notoc}

做得好！您已為應用程式新增一個安全持續性等級。嘗試下列其中一個選項，以保持動力：

* 檢視 [{{site.data.keyword.cloudant_short_notm}} SDK for Swift ](https://github.com/cloudant/swift-cloudant){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示") 原始碼。
* 「入門範本套件」是使用 {{site.data.keyword.cloud_notm}} 功能最快的方式之一。**Infinite Scrolling with Cloudant NoSQL for iOS** 入門範本套件說明如何延伸 `ViewController`，以使用分頁來顯示資料。iOS 開發人員經常使用這個應用程式型樣，這個型樣是說明 {{site.data.keyword.cloudant_short_notm}} 的功能時的良好範例。請檢視[行動開發人員儀表板](https://{DomainName}/developer/mobile/dashboard){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示") 中的可用入門範本套件。下載程式碼。執行應用程式！
* 進一步瞭解並充分運用 {{site.data.keyword.cloudant_short_notm}} 提供的所有特性，[請參閱文件](/docs/services/Cloudant?topic=cloudant-ibm-cloudant-basics)！
