---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-12"

keywords: reduce cost swift, serverless swift, openwhisk swift, functions swift, faas swift, stateless swift, api reference swift, high availability swift, serverless ios

subcollection: swift

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:note: .deprecated}
{:tip: .tip}

# 無伺服器開發
{: #serverless-dev-swift}

何謂無伺服器？無伺服器開發型樣是指其伺服器端邏輯在無狀態容器中執行的應用程式開發。容器由事件觸發且很短暫（持續時間為單一次執行），並由協力廠商完全管理。在此參照範例（也稱為「函數即服務 (FaaS)」）中，開發人員所提供的無狀態函數，可以在不明確建立或管理伺服器的情況下，進行觸發然後執行。

藉由摘要伺服器端開發所需的基礎架構及架構，無伺服器架構容許開發人員專注於撰寫程式碼，並反應性地執行以變更資料。

IBM 的 FaaS 供應項目 [{{site.data.keyword.openwhisk}}](https://{DomainName}/openwhisk){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")，力求提供簡單的伺服器端開發經驗，而不需要任何特殊的伺服器端知識。使用無伺服器技術，您可以快速開發可延伸的後端解決方案，以符合幾乎所有工作負載需求，而不需要提前建立資源。對於具有無法預期之負載型樣或伺服器關閉時間過長的應用程式，{{site.data.keyword.openwhisk_short}} 可以是出色的雲端解決方案，其效能已獲得改進，且其「使用者付費」系統可協助降低成本。

## 架構變更
{: #comparison-serverless}

為了協助您瞭解切換成 FaaS 在架構上的好處，我們使用鏈結至資料庫的簡單 iOS 應用程式來比較傳統與 FaaS 架構。

在比較傳統的架構中，iOS 應用程式會卸載網路密集的作業，或在中央伺服器遠端處理資料，而且本身是由自己的服務或儲存空間選項所連接。沉重的負擔都壓在單一伺服器上，它要處理許多密集作業以將用戶端的壓力減到最低，還要在使用者基礎之間提供同步化。

無伺服器架構可以變更這個結構，讓它看起來更像下列影像。

![無伺服器架構](./images/Architecture.png "無伺服器架構"){: caption="圖 1. 無伺服器架構流程" caption-side="bottom"}

無伺服器架構使用封裝大量伺服器端邏輯的函數，將部分邏輯卸載給用戶端（及外部服務），而不是在單一伺服器內處理所有處理作業及鑑別邏輯。

看一下圖解，您可以瞭解到下列重點：

1. 用戶端會針對「應用程式 ID」這類「身分提供者」進行鑑別。
2. 對 FaaS 後端 API 的呼叫包括存取記號。
3. 後端由 {{site.data.keyword.openwhisk_short}} 實作。無伺服器動作會公開為 Web 動作、預期在要求標頭中傳送記號以供驗證（簽章及到期日），以提供實際 API 的存取權。
4. 當用戶端提交資料時，意見會儲存在 {{site.data.keyword.cloudant_short_notm}}。
5. 意見文字由 {{site.data.keyword.toneanalyzershort}} 進行處理。
6. 根據分析結果，由 {{site.data.keyword.mobilepushshort}} 將通知傳回給用戶端。
7. 用戶端接收到通知。

在純正的無伺服器模型中，因為無法儲存使用者的狀態，所以用戶端通常需要承擔額外的責任。授權由用戶端及 {{site.data.keyword.appid_short_notm}} 身分提供者服務進行處理。

雖然無伺服器架構並不總是理想的，但在適當的團隊和使用狀況下，還是可以提供非常多的好處。請參閱一些特定範例，以瞭解一些常用的[使用案例](#use_cases)。

## 無伺服器好處
{: #benefits-serverless}

### 減少成本
{: #reduced-cost-serverless}

外包與系統管理相關聯的時間與貨幣成本，可以減少與傳統後端伺服器相關聯的整體成本。此外，{{site.data.keyword.openwhisk_short}} 不同於傳統的計算技術，因為您僅支付程式碼在滿足要求時所花費的時間（四捨五入到最近的 100 毫秒）。相對於 VM 及容器等其他技術（其利用率不可能 100% 且會佔用雲端提供者系統的記憶體），可節省大量成本。

### 高可用性及可調整性
{: #ha-serverless}

無伺服器架構基本上可立即調整且持續可用。

### 速度和簡化的開發
{: #speed-serverless}

藉由刪除系統管理的需求，並提供簡單的部署介面，無伺服器參照範例加速了應用程式的開發速度。開發人員可以使用為了回應事件驅動世界而執行的動作序列，來快速建置應用程式。

## 範例使用案例
{: #use-cases-serverless}

### 行動後端
{: #mobile-backend-serverless}
![行動後端](./images/cloud-functions-rest-api-trigger.png "行動後端"){: caption="圖 2. 行動後端" caption-side="bottom"}

行動開發人員可以輕鬆地存取伺服器端邏輯，並將運算密集作業外包給雲端平台。使用 iOS SDK，您可以用 Swift 等語言來實作函數，並輕鬆地取用伺服器端函數，而不需要任何伺服器端經驗。

### 資料處理
{: #data-processing-serverless}

![無伺服器資料處理](./images/cloud-functions-cloudant-trigger.png "無伺服器資料處理"){: caption="圖 3. 無伺服器資料處理" caption-side="bottom"}

您只要透過內建觸發程式更新資料儲存庫中的資料，就可以執行程式碼。您也可以透過實用的伺服器端程式設計模型，輕鬆地自動執行處理程序，例如，音訊正規化、旋轉影像、銳利化、降噪、產生縮圖或視訊轉碼。

### 認知資料處理
{: #cognitive-serverless}

只要資料可供使用，您就可立即進行分析。您的函數可以充分運用功能強大的認知服務（例如，IBM Watson），來偵測影像或視訊中的物件或人員。

### 排定的作業
{: #tasks-serverless}

定期執行您的函數，並定義遵循類似 cron 語法的排程，以指定要執行動作的時間。

## API 參考資料
{: #apiref-serverless notoc}

<!-- * [REST API Documentation](./openwhisk_reference.html#openwhisk_ref_restapi)-->
* [REST API](https://{DomainName}/apidocs){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")

## 相關鏈結
{: #related-serverless notoc}

* [Discover {{site.data.keyword.openwhisk_short}}](https://www.ibm.com/cloud/functions){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")
<!-- redirects to link above * [{{site.data.keyword.openwhisk_short}} on IBM developerWorks](https://developer.ibm.com/openwhisk/)-->
* [Apache OpenWhisk 專案網站](http://openwhisk.incubator.apache.org/){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")
* [More on Serverless](https://martinfowler.com/articles/serverless.html){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")
