---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-12"

keywords: watson studio swift, machine learning swift, custom model swift, data set swift, predictive swift, watson api swift, generated model swift, dataset swift

subcollection: swift

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# 使用自訂產生的模型來分析資料集
{: #dsx-overview}

Watson Studio 提供的環境及工具，可讓您藉由協同分析資料來解決商業問題。您可以選擇需要的工具，來分析、清理及組織資料。學習汲取多媒體串流資料，或建立、訓練及部署機器學習模型。Watson Studio 與各式各樣的 {{site.data.keyword.cloud}} 服務及 Watson Knowledge Catalog 整合，提供原則管理來控制資產，並提供型錄來建立索引，以進行定位。[進一步瞭解資訊](https://dataplatform.cloud.ibm.com/){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")。

Watson Studio 的結構為專案型架構，其會組織您的資源以解決商業問題。資源包括與雲端的連線，以及內部部署資料儲存、資料檔案、合作程式以及分析資產（例如，模型）。如需相關資訊，請參閱 [Watson Studio 概觀](https://dataplatform.cloud.ibm.com/docs/content/wsj/getting-started/overview-ws.html){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")。

## {{site.data.keyword.DSX}} 的機器學習
{: #dsx-learning}

使用 {{site.data.keyword.DSX}}，即可利用 API 來訓練模型然後進行部署，最後再好好利用結果。然後，可以在您的 iOS 或 Swift 應用程式中使用這些 API。

使用 IBM Watson Machine Learning，在設定環境之後，您可以建立模型、將其部署至雲端，然後進行訓練。如需相關資訊，請參閱[使用 {{site.data.keyword.pm_full}} 和 {{site.data.keyword.DSX}}](https://dataplatform.cloud.ibm.com/docs/content/wsj/analyze-data/wml-ai.html){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示") 建立、部署和訓練模型。

### 指導教學
{: #dsx-tutorials}

## 使用 iOS 及 Swift 來設定 {{site.data.keyword.DSX}}
{: #dsx_ios}

1. 若要讓認證整合更加簡單，您必須將 {{site.data.keyword.pm_short}} 實例新增至 iOS 應用程式或後端應用程式。為了更方便存取，您的認證包括在專案儀表板上。

![應用程式詳細資料](images/ios-machinelearning-app.png "應用程式詳細資料"){: caption="圖 1. 應用程式詳細資料" caption-side="bottom"}

2. 下載應用程式程式碼。
3. 起始設定
  * 若為 iOS 專案，只要將 {{site.data.keyword.pm_short}} 服務新增至 iOS 專案，認證就會即時注入您的應用程式。若要從應用程式中存取認證，請複製貼上下列程式碼 Snippet。此外，也請務必將評分端點新增至應用程式，其位於您模型的部署 `implementation` 標籤內。

    ```swift
    /* The url to your model's scoring endpoint */
    let modelScoringURL: String = "<your-ml-model-scoringUrl>"

    /* Your credentials */
    var machineLearningUsername: String!
    var machineLearningPassword: String!

    /* Machine Learning initialization */
    if let contents = Bundle.main.path(forResource:"BMSCredentials", ofType: "plist"),
       let dictionary = NSDictionary(contentsOfFile: contents),
       let username = dictionary["machinelearningUsername"] as? String,
       let password = dictionary["machinelearningPassword"] as? String {
        

           machineLearningUsername = username
           machineLearningPassword = password

    }
    ```
    {: codeblock}

  * 若為伺服器端應用程式，請手動將使用者名稱及密碼新增至您的應用程式，評分端點也要新增，其位於模型的部署 `implementation` 標籤內。

    ```swift
    /* Your Machine Learning Credentials */
    let machineLearningUsername: String = "<your-ml-service-username>"
    let machineLearningPassword: String = "<your-ml-service-password>"

    /* The url to your model's scoring endpoint */
    let modelScoringURL: String = "<your-ml-model-scoringUrl>"
    ```
    {: codeblock}

4. 使用簡單的 Client SDK，對應用程式中的資料集，擷取存取記號並執行預測分析。

  ```swift
  public class MachineLearning {

      private let username: String
      private let password: String

      public init(username: String, password: String) {
          self.username = username
          self.password = password
      }

      public static func failure(_ error: Error) {
          print("Error:", error.localizedDescription)
      }

      public func retrieveToken(failure: @escaping (Error) -> Void = MachineLearning.failure, success: @escaping (String) -> Void) {

          guard var request = createRequest(url: "https://ibm-watson-ml.mybluemix.net/v3/identity/token") else {
              failure(NSError(domain: "Could not create url", code: 1, userInfo: nil))
              return
          }

          guard let authString = (username + ":" + password).data(using: .utf8)?.base64EncodedString() else {
              failure(NSError(domain: "Could not encode credentials", code: 1, userInfo: nil))
              return
          }

          request.setValue("Basic \(authString)", forHTTPHeaderField: "Authorization")

          execute(request, failure: failure) { dict in

              guard let token = dict["token"] as? String else {
                  failure(NSError(domain: "Invalid Response", code: 1, userInfo: nil))
                  return
              }

              success(token)
          }
      }

      public func retrieveScore(_ scoringUrl: String, token: String, payload: [String: Any], failure: @escaping (Error) -> Void  = failure, success: @escaping ([String: Any]) -> Void) {

          guard let data = try? JSONSerialization.data(withJSONObject: payload, options: .prettyPrinted) else {
              failure(NSError(domain: "Could not encode data", code: 1, userInfo: nil))
              return
          }

          guard var request = createRequest(method: "POST", url: scoringUrl, body: data) else {
              failure(NSError(domain: "Could not create url", code: 1, userInfo: nil))
              return
          }

          request.setValue("Bearer " + token, forHTTPHeaderField: "Authorization")
          request.setValue("application/json", forHTTPHeaderField: "Content-Type")

          execute(request, failure: failure, success: success)
      }

      public func createRequest(method: String = "GET", url: String, body: Data? = nil) -> URLRequest? {
          guard let url = URL(string: url) else {
              return nil
          }
          var req = URLRequest(url: url)
          req.httpMethod = method
          req.httpBody = body
          return req
      }

      private func execute(_ request: URLRequest, failure: @escaping (Error) -> Void, success: @escaping ([String: Any]) -> Void) {
          URLSession.shared.dataTask(with: request) { data, _, error in

              guard let data = data, error == nil else {
                  let error = error ?? NSError(domain: "No data was returned", code: 1, userInfo: nil)
                  failure(error)
                  return
              }

              guard let body = try? JSONSerialization.jsonObject(with: data, options: .allowFragments) as? [String: Any],
                    let dict = body else {
                  failure(NSError(domain: "Could not create dictionary", code: 1, userInfo: nil))
                  return
              }

              guard let resp = response as? HTTPURLResponse , resp.statusCode == 200 else {
                 let msg = (dict["errors"] as? [[String: Any]])?.first?["message"] as? String ?? "An error occurred"
                 let code = (response as? HTTPURLResponse)?.statusCode ?? 1
                 failure(NSError(domain: msg, code: code, userInfo: nil))
                 return
             }

              success(dict)

          }.resume()
      }
  }
  ```
  {: codeblock}

### 範例
{: #dsx-example}

**情境名稱：**產品線預測

**情境說明：**一家銷售戶外設備的公司建置並部署了一個模型，用來預測客戶對其產品線的興趣。您的任務是針對已部署的模型建立評分要求。

在模型部署完成之後，您即可使用評分端點來執行預測分析。

```swift
// The data you want to have analyzed
let examplePayload: [String: Any] = [
    "fields": ["GENDER", "AGE", "MARITAL_STATUS", "PROFESSION"],
    "values": [["M", 23, "Single", "Student"],["M", 55, "Single", "Executive"]]
]

// Your service credentials
let machineLearningUsername: String = "<your-ml-service-username>"
let machineLearningPassword: String = "<your-ml-service-password>"

// Your scoring end-point
let scoringUrl: String = "<your-model-scoring-url>"

// Define your client
let client = MachineLearning(username: username, password: password)

// Retrieve your access token and score your payload!
client.retrieveToken { token in
    client.retrieveScore(scoringUrl, token: token, payload: examplePayload) { dict in
        print(dict)
    }
}
```
{: codeblock}

## 後續步驟
{: #dsx_next notoc}

做得好！現在，您可以使用自訂產生的機器學習模型來分析資料集。藉由在[資料科學及機器學習](https://www.ibm.com/analytics/machine-learning){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示") 中進一步瞭解 {{site.data.keyword.pm_short}} 提供的特性，來保持動力。

### 相關鏈結
{: #dsx-related}

* [{{site.data.keyword.pm_short}}](/docs/services/PredictiveModeling?topic=PredictiveModeling-WMLgettingstarted)
* [{{site.data.keyword.DSX}}](https://www.ibm.com/cloud/watson-studio){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")
* [{{site.data.keyword.DSX}} 文件 ](https://dataplatform.cloud.ibm.com/docs/content/wsj/getting-started/welcome-main.html){: new_window} ![外部鏈結圖示](../../icons/launch-glyph.svg "外部鏈結圖示")
