---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-12"

keywords: swift database, secure database swift, cluster database swift, mongokitten swift, verify database swift, credentials swift, storage api swift

subcollection: swift

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:gif: data-image-type='gif'}
{:tip: .tip}

# 가용성이 높고 안전한 데이터베이스 작성
{: #create-database-cluster}

가용성이 높고 안전한 데이터베이스를 최대한 활용하기 위해 추가 로직을 애플리케이션에 임베드합니다. 제공된 코드 스니펫을 사용하여 MongoDB 데이터베이스를 작성하고 액세스할 수 있습니다. 

{{site.data.keyword.ihsdbaas_full}} 사용을 위해 현재 지원되는 프로그래밍 언어는
MongoKitten SDK 4.0.0이 포함된 Swift 4.0입니다.

## 1단계. 데이터베이스 클러스터 작성
{: #create_dbcluster}

1. [{{site.data.keyword.ihsdbaas_full}} 서비스 구성](https://{DomainName}/catalog/services/hyper-protect-dbaas){: new_window} ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘") 화면에 액세스하십시오.

2. 다음 정보를 제공하십시오.

	<dl>
		<dt>서비스 이름:</dt>
		<dd>데이터베이스 서비스의 이름입니다.</dd>

    <dt>배치할 지역/위치 선택:</dt>
    <dd>데이터베이스가 위치한 데이터 센터를 선택합니다.</dd>

    <dt>리소스 그룹 선택:</dt>
    <dd>리소스 그룹을 선택할 수 없는 경우 IBM Cloud 대시보드에서 리소스 그룹을 작성할 수 있습니다.</dd>

		<dt>클러스터/복제 세트 이름:</dt>
		<dd>데이터베이스 클러스터의 이름입니다.</dd>

		<dt>데이터베이스 관리 이름:</dt>
		<dd>데이터베이스 관리자(DBA)의 사용자 ID입니다.</dd>

		<dt>데이터베이스 관리 비밀번호:</dt>
		<dd>DBA 사용자 ID의 비밀번호입니다.</dd>

    <dt>데이터베이스 관리 비밀번호 확인:</dt>
    <dd>DBA 사용자 ID의 비밀번호를 확인합니다.</dd>

		<dt>데이터베이스 유형:</dt>
		<dd>데이터베이스 유형을 선택합니다. 현재 MongoDB만 지원됩니다.</dd>

    <dt>라이센스 계약</dt>
    <dd>라이센스 계약을 읽고 계약을 확인했으면 선택란을 선택하십시오.</dd>

    <dt>가격:</dt>
		<dd>현재 솔루션은 하나의 가격 카테고리만 무료로 제공합니다. 이후 버전에서는 가격 카테고리를 선택할 수 있습니다.</dd>
	</dl>

3. **작성**을 클릭하십시오.

  {{site.data.keyword.cloud_notm}} 대시보드가 표시됩니다. 서비스 섹션에 나열된 새 클러스터를 보려면 브라우저를 새로 고쳐야 할 수도 있습니다.</p></li>

4. 서비스를 선택하면 클러스터 정보가 표시됩니다.

5. 클러스터 정보의 관리 탭에서 **열기**를 클릭하십시오.

	{{site.data.keyword.ihsdbaas_full}} 대시보드가 표시됩니다.

6. 호스트 이름과 데이터베이스 클러스터에 속하는 작성된 세 가지 데이터베이스 인스턴스의 포트 번호를 수집하십시오. [데이터베이스에 연결](#connect_db) 섹션의 단계에서 호스트 이름, 포트 번호 및 사용자 인증 정보가 필요합니다.

## 2단계. 스타터 킷을 사용하여 프로젝트 작성
{: #create_starter}

서버 측 Swift 웹 프레임워크 Kitura를 기반으로 한 스타터 킷이 필요합니다.

![기능 세부사항](videos/StarterKit.gif){: gif}

스타터 킷에서 작성된 기존 프로젝트를 사용하거나 새 프로젝트를 작성하십시오.

1. [{{site.data.keyword.cloud_notm}} 앱 서비스 대시보드](https://{DomainName}/developer/appservice/dashboard){: new_window} ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")를 여십시오.

2. **스타터 킷** 탭을 선택하십시오.

3. 프론트 엔드 스타터 킷에 적합한 **Swift Kitura** 백엔드를 선택하십시오. Swift Kitura 기본 웹 앱 스타터 킷과 혼동하지 마십시오.
  새 프로젝트 작성 페이지가 표시됩니다.

4. 프로젝트 세부사항을 입력하고 **프로젝트 작성**을 클릭하십시오.
  프로젝트 페이지가 표시됩니다.

5. 프로젝트 페이지에서 **코드 다운로드**를 클릭하십시오.

6. 프로젝트 디렉토리에 압축한 파일의 압축을 푸십시오.

## 3단계. 데이터베이스에 연결
{: #connect_db}

안전한 데이터 전송을 위해 다음 웹 사이트에서 인증 기관(CA) 파일을 다운로드하고 프로젝트 디렉토리에 이를 복사하십시오.

1. 압축을 푼 다운로드된 코드 파일이 있는 프로젝트 디렉토리로 변경하십시오.

2. `cred.json`이라고 하는 JSON 파일을 작성하여 액세스 인증 정보를 데이터베이스 클러스터에 저장하십시오.

3. [데이터베이스 클러스터 작성](/docs/swift?topic=swift-create-database-cluster#create_dbcluster)의 단계에서 수집된 값을 입력하십시오. 값은 한 행에 지정해야 합니다.
  ```hljs
  {
  "uri": "mongodb://<admin_ID>:<admin_pwd>@<Hostname_1>:<PortNumber_1>,
  <Hostname_2>:<PortNumber_2>,<Hostname_3>:<PortNumber_3>
   /admin?ssl=true&ssl_ca_certs=/swift-project/<CA_file>"
  }
  ```
  {: codeblock}

### 데이터베이스 매개변수 설명
{: #db-parameter-descriptions}

다음 데이터베이스 매개변수 설명을 참조하십시오.

* `admin_ID` - [데이터베이스 클러스터 작성](/docs/swift?topic=swift-create-database-cluster#create_dbcluster)에서 지정된 데이터베이스 관리자의 사용자 ID입니다.
* `admin_pwd` - [데이터베이스 클러스터 작성](/docs/swift?topic=swift-create-database-cluster#create_dbcluster)에서 지정된 관리자의 사용자 ID 비밀번호입니다.
* `Hostname_i` - [데이터베이스 클러스터 작성](/docs/swift?topic=swift-create-database-cluster#create_dbcluster)에서 리턴된 데이터베이스 복제본 *i*(*i*=1,2,3)입니다.
* `PortNumber_i` - [데이터베이스 클러스터 작성](/docs/swift?topic=swift-create-database-cluster#create_dbcluster)에서 리턴된 포트 번호 *i*(*i*=1,2,3)입니다.
* `CA_file` - 다운로드된 CA 파일의 파일 이름입니다. 배치 중에 `/swift-project` 디렉토리에 복사됩니다.

4. `Package.swift` 파일을 편집하여 MongoKitten SDK 사용을 위한 패키지 종속성을
복사하십시오.

  * 종속성 섹션에서 다음 행을 추가하십시오.
   ```swift
   .package(url: "https://github.com/OpenKitten/MongoKitten.git", from: "4.0.0"),
   ```
   {: codeblock}

  * 대상 섹션에서 다음 행에 종속성 "MongoKitten"을 추가하십시오. **참고:** 값은 한 행에 지정해야 합니다.
   ```swift
   .target(name: "Application", dependencies: [ "Kitura",
   "CloudEnvironment","SwiftMetrics","Health","MongoKitten", ]),
   ```
   {: codeblock}

5. `Sources/Application/Application.swift` 파일을 편집하여 MongoKitten 사용을 통해 MongoDB에 대한 연결을 초기화십시오.

  * MongoKitten SDK를 가져오십시오.
    ```swift
	  import MongoKitten
	  ```
	  {: codeblock}

  * 클래스 `ApplicationServices`를 추가하십시오.
    ```swift
	  cclass ApplicationServices {
	  /* Service references */
	  public let mongoDBService: MongoKitten.Database
	  public let myCredFile = "/swift-project/cred.json"

    public init() throws {
        /* Read credentials from json file cred.json */
        struct ResponseData: Decodable {
            var uri: String
	        }
	        let data = try? Data(contentsOf: URL(fileURLWithPath: myCredFile))
	        let decoder = JSONDecoder()
	        let jsonData = try decoder.decode(ResponseData.self, from: data!)

        /* Run service initializers */
        let server = try Server(jsonData.uri)
        mongoDBService = MongoKitten.Database(named: "admin", atServer: 		server)
    }
	}
	```
	{: codeblock}

  * 공용 클래스 `App`에서 다음 행을 추가하여 데이터베이스 연결을 초기화하십시오.
    ```swift
	  public class App {
	  ...
	  let services: ApplicationServices

	  public init() throws {
	  /* Services */
	  services = try ApplicationServices()
	  }
	  ...
    ```
    {: codeblock}

## 4단계. 데이터베이스 연결 확인
{: #verify_database}

1. `Sources/Application/Application.swift` 파일을 편집하고 데이터베이스 연결을 테스트하는 명령을 추가하여 데이터베이스 연결을 확인하십시오.
예를 들어, `class ApplicationServices`에서 다음 명령을 추가하십시오.

	```swift
		class ApplicationServices {
		    ...
		    public init() throws {
		        ...
		        let server = try Server(jsonData.uri)
		        mongoDBService = MongoKitten.Database(named: "admin", atServer: server)
		        if server.isConnected {
		            print("Connected to mongodb: " + String(describing: mongoDBService))
		        }
		        ...
		    }
		}
	```
	{: codeblock}

[6단계](#use-step6)에서 설명된 대로 애플리케이션을 배치한 후 데이터베이스에 연결되면
다음 메시지가 표시됩니다.

```
...
Connected to mongodb:
MongoKitten.Database&lt;mongodb:/&sol;&lt;<em>Hostname_1</em>&gt;&colon;&lt;<em>PortNumber_1</em>&gt;,&lt;<em>Hostname_2</em>&gt;&colon;&lt;<em>PortNumber_2</em>&gt;,&lt;<em>Hostname_3</em>&gt;&colon;&lt;<em>PortNumber_3</em>&gt;/admin&gt;
...
```
{: screen}

## 5단계. 애플리케이션 코드 임베드
{: #embed_appcode}

이제 고유한 애플리케이션 코드를 프로젝트에 추가할 수 있습니다. 자세한 정보는 [MongoKitten API](http://beta.openkitten.org/tutorials/){: new_window} ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘") 문서를 참조하십시오.

## 6단계. 애플리케이션 배치
{: #deploy-dbcluster}

필요한 빌드 도구를 사용하여 [로컬로](/docs/swift?topic=swift-swift_cli#swift-install-tools) 애플리케이션을 실행하거나 {{site.data.keyword.cloud_notm}}에 배치할 수 있습니다.

대시보드에서 배치 도구 체인을 작성하려면 **배치**를 클릭하십시오. 선택하는 방법의 지시사항에 따라 배치 대상을 설정하십시오.
  * **IBM Kubernetes Service에 배치**. 이 옵션은 작업자 노드라고 하는 호스트의 클러스터를 작성하여 고가용성 앱 컨테이너를 배치하고 관리합니다. 사용자는 클러스터를 작성하거나 기존 클러스터에 배치할 수 있습니다. 자세한 정보는 [Kubernetes 클러스터에 앱 배치](/docs/containers?topic=containers-app)를 참조하십시오.
  * **Cloud Foundry에 배치**. 이 옵션은 기본 인프라를 관리하지 않고도 클라우드 고유 앱을 배치합니다. 계정에 {{site.data.keyword.cfee_full_notm}}에 대한 액세스 권한이 있는 경우, **퍼블릭 클라우드** 또는 **엔터프라이즈 환경**의 배치자 유형을 선택할 수 있으며, 이는 사용자 엔터프라이즈에서 독점적으로 Cloud Foundry 앱을 호스팅하기 위한 격리 환경을 작성하고 관리하는 데 사용할 수 있습니다. 자세한 정보는 [Cloud Foundry Public에 앱 배치](/docs/cloud-foundry-public?topic=cloud-foundry-public-deployingapps) 및 [{{site.data.keyword.cfee_full_notm}}에 앱 배치](/docs/cloud-foundry?topic=cloud-foundry-deploy_apps)를 참조하십시오.
  * **가상 서버에 배치**. 이 옵션은 가상 서버 인스턴스를 프로비저닝하고 앱이 포함된 이미지를 로드하며 DevOps 도구 체인을 작성하고 첫 번째 배치 주기를 시작합니다. 자세한 정보는 [가상 서버에 앱 배치](/docs/vsi?topic=virtual-servers-deploying-to-a-virtual-server)를 참조하십시오.
  
