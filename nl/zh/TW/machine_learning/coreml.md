---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-12"

keywords: coreml swift, core ml swift, watson swift core, create model swift, image classification swift, version parameter swift, swift coreml watson

subcollection: swift

---

{:tip: .tip}
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:gif: data-image-type='gif'}

# 搭配使用 Core ML 與 Watson
{: #swift-coreml}

使用 [Core ML](https://developer.apple.com/documentation/coreml){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")，您可以將各式各樣的機器學習模型類型整合至您的應用程式。除了支援超過 30 種層次類型的廣泛深度學習之外，還支援標準模型，例如，樹狀結構組合、SVM 及一般線性模型。Core ML 利用低階技術（例如 Metal 及 Accelerate），無縫充分運用 CPU 及 GPU 來提供最高效能與效率，而非遠端傳送資料以進行分析。

您可以藉由使用下列步驟來訓練及建立模型、下載及建置相依關係，以及新增映像檔分類，將 Core ML 和 Watson Visual Recognition 新增至 Swift 應用程式。
{: shortdesc}

## 開始之前
{: #prereqs-coreml}

若要使用 Core ML 及 Watson Visual Recognition 搭配 Swift，您需要下列元件：

* iOS 11.0+
* Xcode 9.3+
* Swift 4.1+
* CocoaPods、Carthage 或 Swift Package Manager

您可以使用 [CocoaPods](https://github.com/watson-developer-cloud/swift-sdk#cocoapods){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")、[Carthage](https://github.com/watson-developer-cloud/swift-sdk#carthage){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示") 或 [Swift Package Manager](https://github.com/watson-developer-cloud/swift-sdk#swift-package-manager){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")，來安裝 [Watson Swift SDK](https://github.com/watson-developer-cloud/swift-sdk){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")。藉由使用 CocoaPods 來管理相依關係，您只會得到您需要的架構，而不是整個 Watson Swift SDK。如果您是 CocoaPods 新手，可以輕鬆地安裝它：

```console
sudo gem install cocoapods
```
{: codeblock}

## 步驟 1. 使用 {{site.data.keyword.watson}} {{site.data.keyword.visualrecognitionshort}} 訓練模型
{: #train-module-coreml}

如果沒有現行模型，則會使用遠端找到的第一個模型，或存在於本端的任何一個模型。下列 gif 及隨附的指示顯示如何將您的服務鏈結至 Watson Studio，並訓練您的模型。

![Core ML 模型逐步演練](images/CoreMLWalkthrough.gif)

### 從 Core ML 應用程式儀表板設定服務
{: #service-coreml}

1. 從「入門範本套件」儀表板中，選取**啟動工具**，以啟動「視覺化識別工具」。
2. 選取**建立模型**，以開始建立您的模型。
2. 如果專案尚未與您建立的 Visual Recognition 實例相關聯，則會建立一個專案。否則，會跳到**建立模型**區段。
3. 為專案命名，然後按一下**建立**。
  
  
  如果未定義任何儲存空間，請按一下重新整理。
  {: tip}

### 將服務連結至專案
{: #bind-service-coreml}

1. 建立您的專案之後，即會顯示專案儀表板。
2. 移至設定標籤，往下捲動至**關聯的服務**，選取**新增服務** -> **Watson**。
3. 從 Watson 服務頁面中，選取 **Visual Recognition**。
4. 在服務資訊頁面上，選取**現有**標籤，然後從儀表板中選取您的服務實例。
5. 現在已連結您的服務，您可以開始建立模型，從「專案」儀表板選取**資產**，然後按一下**新增 Visual Recognition 模型**。

### 建立模型
{: #create-model-coreml}

1. 您可以從模型建立工具更新分類器名稱。如果您要使用特定的模型，請務必修改主要「視圖控制器」中的 `defaultClassifierID` 欄位。

2. 從資訊看板，以 `.zip` 檔上傳模型訓練課程。然後，從下拉功能表選取每個資料集，並將其新增至您的模型。您可以隨意增加使用您專屬影像集的類別，以加強分類器！

![新增類別](images/add_classes.png "將服務鏈結到 Watson Studio"){: caption="圖 1. 將服務鏈結到 Watson Studio" caption-side="bottom"}

3. 選取**訓練模型**，然後等待模型完成訓練。

都完成了！現在，您已準備好可以下載 Core ML 模型，然後使用 [Watson Developer Cloud Swift SDK](https://github.com/watson-developer-cloud/swift-sdk){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示") 將其整合至您的應用程式。

## 步驟 2. 下載及建置相依關係
{: #install-depend-coreml}

使用您最愛的文字編輯器，在您專案的根目錄（`.xcodeproj` 檔案所在之處）中，執行 `pod init` 建立一個新的 `Podfile`。然後，新增一行，指定 Watson Swift SDK 的 {{site.data.keyword.visualrecognitionshort}} 架構作為相依關係：

```pod
use_frameworks!

target 'MyApp' do
    pod 'IBMWatsonVisualRecognitionV3'
```
{: codeblock}

若為正式作業應用程式，您也可能想要指定特定的[版本需求](https://guides.cocoapods.org/using/the-podfile.html#specifying-pod-versions){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")，以避免新版本 Watson Swift SDK 有非預期的變更。

`Podfile` 就緒後，現在您可以下載相依關係。使用終端機來導覽至您專案的根目錄，然後執行 CocoaPods：

```console
    pod install
    ```
{: codeblock}

Cocoapods 會下載 {{site.data.keyword.visualrecognitionshort}} 架構，並且會在您專案的 `Pods/` 資料夾中建置它。

為了避免 Pod 建置失敗，當您在 Xcode 開啟專案時，請開啟以 `.xcworkspace` 為結尾的檔案，而不是 `.xcodeproj`。
{: tip}

## 步驟 3. 將影像分類新增至應用程式
{: #add-image-coreml}

下列範例會協助您為應用程式新增 {{site.data.keyword.visualrecognitionshort}} Core ML 功能，一般是在 `ViewController.swift`。使用下列範例，您可以為您的使用案例延伸本端模型呼叫。

1. 為 Visual Recognition 新增一個 import 陳述式：
  ```swift
  import VisualRecognition
  ```
  {: codeblock}

2. 傳遞 API 金鑰及版本來起始設定 SDK：
  ```swift
  let visualRecognition = VisualRecognition(version: "yyyy-mm-dd", apiKey: "your-api-key")
  ```
  {: codeblock}

  查看[版本參數文件](https://{DomainName}/apidocs/visual-recognition#versioning){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示") 或者使用建立 {site.data.keyword.visualrecognitionshort}} 服務的日期。
  {: tip}

3. 新增下列程式碼，以用 Watson 分類器下載或更新本端 Core ML 模型：
  ```swift
  // Name of the classifier to use
  let classifierID = "your-classifier-ID-here"

  // Minimum confidence threshold for image recognition
  let threshold = 0.5

  // Update or download your model
  visualRecognition.updateLocalModel(classifierID: classifierID) { _, error in
      if let error = error {
          print(error)
      } else {
          print("model successfully updated")
      }                
  }
  ```
  {: codeblock}

4. 新增下列程式碼，以使用本端 Core ML 模型分類影像：
  ```swift
  let image = UIImage(named: "your-image-filename")
  let classifierID = "your-classifier-ID-here"
  let threshold = 0.5

  // Classify your image using your model                                         
  visualRecognition.classifyWithLocalModel(image: image, classifierIDs: [classifierID], threshold: threshold) { classifiedImages, error in
      if let error = error {
          print(error)
      }
      guard let classifiedImages = classifiedImages? else {
          print("Failed to classify the image")
          return
      }
      print(classifiedImages)
  }
  ```
  {: codeblock}

5. 探索 Watson SDK 支援的其他 [Core ML 分類功能](https://watson-developer-cloud.github.io/swift-sdk/services/VisualRecognitionV3/index.html){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")。

## 步驟 4. 使用入門範本套件
{: #coreml_starterkits}

使用入門範本套件，您可以快速且輕鬆地運用 {{site.data.keyword.cloud_notm}} 及 Core ML 的功能。您可以使用入門範本套件，將 {{site.data.keyword.visualrecognitionshort}} 新增至任何用戶端 iOS 應用程式或伺服器端後端。Custom Vision Model for Core ML with {{site.data.keyword.watson}} Starter Kit 說明如何建立自訂的 {{site.data.keyword.visualrecognitionshort}} 模型，並將其實例化成可在您裝置上本端執行的 Core ML 模型。

若要將 {{site.data.keyword.visualrecognitionshort}} 新增至入門範本套件，請完成下列步驟：

1. 選取您要使用的[入門範本套件](https://{DomainName}/developer/appledevelopment/starter-kits){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")。
2. 建立含有預設服務的應用程式。
3. 按一下**新增服務 > Watson > {{site.data.keyword.visualrecognitionshort}}**。
4. 按一下**下載程式碼**，以下載專案。若為 iOS 專案，認證會插入對應金鑰欄位的 `BMSCredentials.plist` 檔案中。若為伺服器端 Swift 專案，您可以在 `config/local-dev.json` 檔案中找到這些認證。

## 後續步驟
{: #coreml_next}

現在，您可以使用自訂產生的 Core ML 模型來分析影像。嘗試下列其中一個選項，以保持動力：

* 參閱 [{{site.data.keyword.watson}}Swift SDK](https://github.com/watson-developer-cloud/swift-sdk){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示") 並探索其他受支援的 Watson 服務。
* 新增雲端邏輯。開始於[開發無伺服器應用程式](/docs/swift/backend?topic=swift-serverless-dev-swift#serverless-dev-swift)。
* 充分運用 {{site.data.keyword.visualrecognitionshort}} 提供的所有特性。如需詳細資料，請參閱[文件](/docs/services/visual-recognition?topic=visual-recognition-index#index)。
