---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-12"

keywords: bluepic swift, ios bluepic, bff swift image, sample swift, swift example bff

subcollection: swift

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:note: .deprecated}

# iOS 及 Cloud Logic 使用案例
{: #sample_cloud}

透過 [BluePic 影像分享範例應用程式](https://github.com/IBM/BluePic){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")，可看到使用 Backend For Frontend (BFF) 的 iOS 應用程式範例。BluePic 應用程式使用下列技術：

* Object Storage 及 Cloudant，用來儲存影像資料。
* Watson Visual Recognition 及 IBM Weather Company 服務，用來將其他資訊新增至影像。
* Kitura 及 {{site.data.keyword.openwhisk}}，用來提供後端邏輯，並控制鑑別與推送通知，這全都以 Swift 撰寫。

![BluePic](images/cloudlogic.png "BluePic 流程"){: caption="圖 1. BluePic 流程" caption-side="bottom"}

在 BluePic 範例中，張貼影像時，其資料會記錄在 Cloudant 中，而影像二進位則儲存在 Object Storage 中。從那裡，呼叫 {{site.data.keyword.openwhisk_short}} 序列，並導致根據影像的上傳位置來計算天氣資料，例如溫度及目前狀況（例如，晴天、陰天）。{{site.data.keyword.openwhisk_short}} 序列中也會使用 Watson Visual Recognition，根據影像的內容來分析影像並擷取文字標記。最後，將推送通知傳送給使用者，通知使用者影像已獲得處理，現在已包括天氣及標記資料。
