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

# 傳送 {{site.data.keyword.mobilepushshort}}
{: #push_notifications}

若要加強您的 Swift 應用程式，您可以在 {{site.data.keyword.cloud}} 上使用 {{site.data.keyword.mobilepushshort}} 服務，將即時通知傳送至行動裝置及 Web 應用程式。

 - 通知可以分送至所有應用程式使用者，或者分送至選取的一群使用者或裝置。
 - 同時支援互動式和無聲自動通知。
 - 客戶可以選擇訂閱要接收通知的特定標籤或主題。
 - 可讓應用程式擁有者分析已登錄要接收通知的裝置數量，以及傳送的通知數量。

您可以選擇將 {{site.data.keyword.mobilepushshort}} 服務當作 MobileFirst Services Starter Boilerplate 的一部分使用，或者當作 {{site.data.keyword.cloud_notm}} [專用服務](/docs/dedicated?topic=dedicated-dedicated#dedicated) 使用。您也可以使用 SDK（軟體開發套件）及 [REST API ](https://mobilefirstplatform.ibmcloud.com/tutorials/en/foundation/8.0/notifications/rest-apis/){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")，以進一步開發用戶端應用程式。

![Push 概觀](images/push_notification_lifecycle.jpg "Push 概觀"){: caption="圖 1. {{site.data.keyword.mobilepushshort}} 服務生命週期概觀" caption-side="bottom"}

## 開始之前
{: #prereqs-push}

首先，請確定您具備下列必要條件：

 - iOS 8.0+
 - Xcode 7.3、8.0
 - Swift 2.3 - 4.0
 - CocoaPods 或 Carthage

## 步驟 1. 建立 {{site.data.keyword.mobilepushshort}} 實例
{: #push_create}

1. 在 {{site.data.keyword.cloud_notm}} 型錄中，按一下**行動** > **{{site.data.keyword.mobilepushshort}}**。即會開啟服務配置畫面。
2. 提供服務實例的名稱，或使用預設名稱。
3. 按一下**建立**。
4. 在導覽窗格中，按一下**連線**，選取應用程式，並將其連結至您的服務。如果建立期間未連結服務實例與應用程式，您可以稍後再進行此操作。


## 步驟 2. 取得您的通知提供者認證
{: #get_creds-push}

若要設定 Push Notifications 服務，您需要從 Apple Push Notification Service (APNs) 取得必要的認證。請遵循這裡的步驟，以[取得並配置您的 APNs 認證 ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")](/docs/services/mobilepush?topic=mobile-pushnotification-push_step_1){: new_window}。


## 步驟 3. 配置服務實例
{: #config-resource-push}

若要使用 {{site.data.keyword.mobilepushshort}} 服務來傳送通知，請上傳您建立的 `.p12` 金鑰儲存庫，它具有建置和發佈應用程式所需的私密金鑰及 SSL 憑證。您也可以使用 REST API 來上傳 APNs 憑證。

當 `.cer` 檔案位於您的金鑰鏈存取中之後，將其匯出至您的電腦，以建立 `.p12` 憑證。

如需如需使用 APNs 的相關資訊，請參閱 [iOS Developer Library: Local and Push Notification Programming Guide ](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")。

若要在 Push Notification 服務主控台上設定 APNs，請完成以下步驟：

1. 在 {{site.data.keyword.mobilepushshort}} 服務主控台上，選取**配置**。
2. 選擇**行動**選項，以更新 **APNs Push 認證**表單中的資訊。
3. 選擇下列一個選項：
	- 對於**行動**選項
		1. 選取**沙盤推演**（開發）或**正式作業**（分佈），然後上傳您建立的 `p.12` 憑證。
		  ![{{site.data.keyword.mobilepushshort}} 主控台](images/wizard.jpg "推送通知行動配置"){: caption="圖 2. 推送通知行動配置" caption-side="bottom"}

		2. 在**密碼**欄位中，輸入與 `.p12` 憑證檔案相關聯的密碼，然後按一下**儲存**。

	- 對於 **Web** 選項
		- 在 Safari Push 區段中，使用必要的資訊更新表單。
		- **網站名稱**：「通知」中心內所提供的網站名稱。
		- **Website Push ID**：使用 Website Push ID 的反向網域字串來更新。例如，`web.com.example.www`。
		- **網站 URL**：提供應該推送通知的網站 URL。例如 `https://www.example.com`。
		- **容許的網域**：（選用參數）要求使用者許可權的網站清單。請確定 URL 是以逗點區隔的值。若未提供資訊，則會使用網站 URL 中的值。
		- **URL 格式字串**：按一下通知時要解析的 URL。例如 `https://www.example.com`。確定 URL 使用 http 或 https 綱目。
		-**Safari Web 推送憑證**：上傳 `.p12` 憑證並提供密碼。
4. 按一下**儲存**。

  ![{{site.data.keyword.mobilepushshort}} 主控台](images/push_configure_safari.jpg "推送通知 Web 配置"){: caption="圖 3. 推送通知 Web 配置" caption-side="bottom"}

## 步驟 4. 設定服務 Client SDK
{: #service-client-push}

若要讓 iOS 應用程式接收推送通知到您的裝置，您需要為 {{site.data.keyword.mobilepushshort}} 服務配置 iOS SDK。

{{site.data.keyword.cloud_notm}} Mobile Services Swift SDK 可與 Cocoapods 或 Carthage 一同安裝。如需相關資訊，請參閱 [https://github.com/ibm-bluemix-mobile-services/bms-clientsdk-swift-push/tree/Doc#setup-client-application](https://github.com/ibm-bluemix-mobile-services/bms-clientsdk-swift-push/tree/Doc#setup-client-application){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")。

## 步驟 5. 傳送通知
{: #send-notify-push}

開發應用程式之後，您可以傳送基本推送通知。

若要傳送基本推送通知，請完成下列步驟：

1. 選取**傳送通知**，然後選擇**傳送至**選項來編寫訊息。支援的選項為**依據標籤的裝置**、**裝置 ID**、**使用者 ID**、**iOS 裝置**、**Web 通知**以及**所有裝置**。**附註**：當您選取**所有裝置**選項時，訂閱 {{site.data.keyword.mobilepushshort}} 的所有裝置都會收到通知。

  ![「傳送通知」畫面](images/tag_notification.jpg "「傳送通知」畫面"){: caption="圖 4. 「傳送通知」畫面" caption-side="bottom"}

2. 在**訊息**欄位中，編寫訊息。視需要選擇配置選用設定。
3. 按一下**傳送**。
3. 驗證您的裝置或瀏覽器已接收到通知。

  下列畫面擷取顯示裝置前景中處理推送通知的警示方框。
  
  ![Android 上的前景推送通知](images/Android_Screenshot.jpg "前景通知警示"){: caption="圖 5. Android 上的前景通知警示" caption-side="bottom"}

  下列畫面擷取顯示背景中的推送通知。
  
  ![iOS 上的背景推送通知](images/background.png "背景通知警示"){: caption="圖 6. iOS 上的背景通知警示" caption-side="bottom"}

### 選用設定
{: #optional-push}

您可以自訂 {{site.data.keyword.mobilepushshort}} 設定，以將通知傳送給 iOS 裝置。支援下列選用自訂選項。


- **徽章**：指出應用程式徽章上所顯示的號碼。預設值為零 (0)，不顯示徽章。
- **音效**：指出要在收到通知時播放的音效短片。支援預設，或是在應用程式中組合的音效資源名稱。
- **其他有效負載**：指定通知的自訂有效負載值。

您也可以選擇啟用[互動式通知](https://github.com/ibm-bluemix-mobile-services/bms-clientsdk-swift-push/tree/Doc#interactive-notifications){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示") 及[複合式多媒體通知](https://github.com/ibm-bluemix-mobile-services/bms-clientsdk-swift-push/tree/Doc#enabling-rich-media-notifications){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")。

## 步驟 6. 監視已分送的通知
{: #monitor-push}

{{site.data.keyword.mobilepushshort}} 服務提供監視公用程式，以協助您檢查已傳送訊息的狀態。若要配置您的監視公用程式，請參閱[啟用 iOS 應用程式的監視功能](https://github.com/ibm-bluemix-mobile-services/bms-clientsdk-swift-push/tree/Doc#enable-monitoring){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")。

## 後續步驟
{: #next-push notoc}

 - 若要進一步瞭解服務並充分運用所有特性，請閱讀我們的[文件](/docs/services/mobilepush?topic=mobile-pushnotification-overview-push)。

 - 如需使用行動服務和 {{site.data.keyword.cloud_notm}} 的簡介，請參閱 [{{site.data.keyword.cloud_notm}} 上的行動應用程式開始使用](/docs/services/mobile?topic=mobile-about)。

 - 「入門範本套件」是使用 {{site.data.keyword.cloud_notm}} 特性最快的方式之一。請檢視[行動開發人員儀表板](https://{DomainName}/developer/mobile/dashboard){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示") 中的可用入門範本套件。下載程式碼。執行應用程式！

 - 您可以使用 [Swagger 使用者介面](https://mobilefirstplatform.ibmcloud.com/tutorials/en/foundation/8.0/notifications/rest-apis/){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")，來快速檢閱 REST API 文件。
