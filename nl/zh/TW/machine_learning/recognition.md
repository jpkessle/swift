---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-06"

keywords: swift visual recognition, train swift, cocoapods swift, swift sdk install, starter kit watson swift, image swift classify, machine learning swift

subcollection: swift

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# {{site.data.keyword.visualrecognitionshort}}
{: #recognition}

{{site.data.keyword.visualrecognitionfull}} 服務可讓您的應用程式利用機器學習，來快速且準確地標記、分類及訓練視覺化內容。此服務可協助虛擬分類任何視覺化內容、快速訓練您自己的自訂模型，以及偵測臉孔。

## 如何運作
{: #how-it-works-recognition}

1. 您的應用程式選擇要進行分析的一批影像。
2. 您的應用程式使用 Watson Swift SDK 將影像傳送至 {{site.data.keyword.visualrecognitionshort}} 服務。
3. 此服務使用分類分析來分析影像，以識別場景、物件、臉孔及其他資訊。
4. Watson Swift SDK 將服務的分析傳回您的應用程式。

## 開始之前
{: #prereqs-recognition}

請確定您具備下列必要條件：

* iOS 10.0+
* Xcode 9.3+
* Swift 4.1+
* CocoaPods、Carthage 或 Swift Package Manager

您可以使用 [CocoaPods](https://github.com/watson-developer-cloud/swift-sdk#cocoapods){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")、[Carthage](https://github.com/watson-developer-cloud/swift-sdk#carthage){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示") 或 [Swift Package Manager](https://github.com/watson-developer-cloud/swift-sdk#swift-package-manager){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")，來安裝 [Watson Swift SDK](https://github.com/watson-developer-cloud/swift-sdk){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")。藉由使用 [CocoaPods](https://cocoapods.org/) 來管理相依關係，您只會得到您需要的架構，而不是整個 Watson Swift SDK。如果您是 CocoaPods 新手，可以輕鬆地安裝它：
```console
sudo gem install cocoapods
```
{: codeblock}

## 步驟 1. 建立 Visual Recognition 的實例
{: #create-instance-recognition}

佈建 {{site.data.keyword.visualrecognitionshort}} 服務的實例：

1. 在 {{site.data.keyword.cloud_notm}} 型錄中，選取 **{{site.data.keyword.visualrecognitionshort}}**。即會開啟服務配置畫面。
2. 提供服務實例的名稱，或使用預設名稱。
3. 如果您要將實例連結至應用程式，請從**連接**功能表中選取應用程式。
4. 選取定價方案，然後按一下**建立**。
5. 選取**認證**標籤，以檢視您的服務認證。這些值用來從您的應用程式連接至服務。

## 步驟 2. 下載及建置相依關係
{: #download-depend-recognition}

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

## 步驟 3. 分析應用程式中的影像
{: #analyze-images-recognition}

下列範例會協助您為應用程式新增 {{site.data.keyword.visualrecognitionshort}} 功能，一般是在 `ViewController.swift`。使用下列範例，您可以為您的使用案例延伸 Visual Recognition 呼叫。

1. 為 Visual Recognition 新增一個 import 陳述式：
  ```swift
  import VisualRecognition
  ```
  {: codeblock}

2. 傳遞 API 金鑰及版本（您可以使用今日）來起始設定 SDK：
  ```swift
  let visualRecognition = VisualRecognition(version: "yyyy-mm-dd", apiKey: "your-api-key")
  ```
  {: codeblock}

  您可以參閱[版本參數文件 ](https://{DomainName}/apidocs/visual-recognition#versioning){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示") 或者使用建立 {site.data.keyword.conversationshort}} 服務的日期。
  {: tip}

3. 新增下列程式碼，以分類影像：
  ```swift
  let url = "your-image-url"
  visualRecognition.classify(url: url) { response, error in
      if let error = error {
          print(error)
      }
      guard let classifiedImages = response?.result else {
          print("Failed to classify the image")
          return
      }
      print(classifiedImages)
  }
  ```
  {: codeblock}

Visual Recognition 架構支援多個分類方法。請探索 Watson SDK [Visual Recognition 文件](https://watson-developer-cloud.github.io/swift-sdk/services/VisualRecognitionV3/index.html){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")，以找出何者最適合您的應用程式。
{: tip}

## 使用入門範本套件
{: #recognition_starterkits}

[入門範本套件](https://{DomainName}/developer/appledevelopment/starter-kits){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示") 是使用 {{site.data.keyword.cloud_notm}} 功能最快的方式之一。選取 **Visual Recognition for iOS with Watson** 入門範本套件，即可使用 {{site.data.keyword.visualrecognitionshort}} 服務。此服務會評估並分類您的影像。從您的行動裝置上傳新的或現有影像，然後 Visual Recognition 應用程式即會快速標記並分類影像內容。

若要開始使用，請執行下列動作：
1. 選取[這裡](https://{DomainName}/developer/appledevelopment/starter-kits/visual-recognition-for-ios-with-watson){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示") 找到的入門範本套件。
2. 建立含有預設服務的專案。
3. 按一下**下載程式碼**，以下載專案。服務認證會注入對應金鑰欄位的 `BMSCredentials.plist` 檔案中。

## 後續步驟
{: #recognition_next notoc}

做得好！現在，您的應用程式可以使用 Visual Recognition。嘗試下列其中一個選項，以保持動力：
* 參閱 [Watson Swift SDK](https://github.com/watson-developer-cloud/swift-sdk){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示") 並探索其他受支援的 Watson 服務。
* 如需相關資訊，請參閱 [IBM Watson {{site.data.keyword.visualrecognitionshort}}](https://www.ibm.com/watson/services/visual-recognition/){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")。
