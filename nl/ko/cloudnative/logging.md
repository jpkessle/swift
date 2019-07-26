---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-10"

keywords: swift logging, ios logging, debug swift, add logging swift, heliumlogger swift, loggerapi swift, logger swift, starter kit swift logger

subcollection: swift

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Swift로 로깅
{: #logging_swift}

로그 메시지는 로그 항목이 작성된 시점의, 마이크로서비스의 상태 및 활동에 대한 컨텍스트 정보를 포함하는 문자열입니다. 로그는 서비스의 실패 과정 및 이유를 진단하는 데 필요하고 모니터링 애플리케이션 상태에서 [앱 메트릭](/docs/swift/cloudnative?topic=swift-metrics#metrics)에 대한 지원 역할을 수행합니다.

클라우드 환경에서 프로세스의 일시적인 특성을 고려하면, 로그는 수집되고 다른 위치(일반적으로 분석을 위해 중앙 위치)로 전송되어야 합니다. 클라우드 환경에서의 가장 일관된 로그 방법은 로그 항목을 표준 출력 및 오류 스트림으로 전송하여 인프라가 나머지를 처리하도록 하는 것입니다.

## Swift 앱에 로깅 추가
{: #logging-add}

[HeliumLogger](https://github.com/IBM-Swift/HeliumLogger){: new_window} ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")는 많이 사용되는 Swift에 대한 경량의 로깅 프레임워크이며 표준 출력으로의 로깅 및 다양한 로그 레벨과 같은 다양한 기본적인 이점을 제공합니다.

[LoggerAPI](https://github.com/IBM-Swift/LoggerAPI){: new_window} ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")는 Swift에서 다양한 유형의 로거에 대한 공통 로깅 인터페이스를 제공하는 로거 프로토콜입니다. Kitura는 그 구현 과정에서 `LoggerAPI`를 사용합니다.

`HeliumLogger`를 사용하려면 다음 코드를 모든 적합한 대상에 대한 `Package.swift`의 **dependencies:** 섹션에 추가하십시오.
```swift
.package(url: "https://github.com/IBM-Swift/HeliumLogger.git", from: "1.7.1")
```
{: codeblock}

`HeliumLogger`를 초기화하고 `LoggerAPI`에서 사용되는 로거로 이를 설정하도록 다음 인스트루먼테이션 코드를 추가하십시오.
```swift
import HeliumLogger
import LoggerAPI

let logger = HeliumLogger(.verbose)
Log.logger = logger

Log.info("This is an informational log message.")
```
{: codeblock}

제공된 예에서 [로그 레벨](http://ibm-swift.github.io/HeliumLogger/){: new_window} ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")은 명시적으로 `.verbose`(기본값)로 설정됩니다.

로그 메시지 사용자 정의에 대한 자세한 정보는 [HeliumLogger API 참조 문서](http://ibm-swift.github.io/HeliumLogger/){: new_window} ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")를 참조하십시오.

## 스타터 킷으로 로깅
{: #logging-starterkits}

기본적으로 {{site.data.keyword.cloud_notm}} 앱 서비스를 사용하여 작성된 Swift 앱은 `HeliumLogger`와 함께 제공됩니다. 기본적으로 또는 클라우드 환경에서 앱을 실행하면 다음 출력이 생성됩니다.
```
[2018-07-31T15:41:05.332-05:00] [INFO] [HTTPServer.swift:195 listen(on:)] Listening on port 8080.
```
{: screen}

이러한 메시지는 로컬로 `stdout`(표준 출력)에 있거나 Cloud Foundry 및 Kubernetes 개발의 로그(`[ibmcloud app logs --recent <APP_NAME>]`(/docs/cli/reference?topic=cloud-cli-ibmcloud_commands_apps#ibmcloud_app_logs) 및 `[kubectl logs <deployment name>`](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs){: new_window} ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")으로 액세스됨)에 있습니다.

`/Sources/AppName/main.swift` 파일에서 다음 코드를 볼 수 있습니다.
```swift
HeliumLogger.use(LoggerMessageType.info)
```
{: codeblock}

로그 레벨은 애플리케이션 시작 로그와 같은 정보 레벨 메시지를 로그하도록 명시적으로 `.info`로 설정됩니다.
{: tip}

## 다음 단계
{: #next-logging notoc}

각 배치 대상에서 로그 보기에 대해 자세히 알아보십시오.
* [Kubernetes 로그](https://kubernetes.io/docs/concepts/cluster-administration/logging/#basic-logging-in-kubernetes){: new_window} ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")
* [Cloud Foundry 로그](/docs/cli/reference/ibmcloud?topic=cloud-cli-ibmcloud_cli#ibmcloud_cli)
* [Cloud Foundry 엔터프라이즈 환경 - 감사 및 로깅](/docs/cloud-foundry?topic=cloud-foundry-auditing-logging#auditing-logging)
* [{{site.data.keyword.openwhisk}} 로그 및 모니터링](/docs/openwhisk?topic=cloud-functions-logs)

로그 집계자 구현 및 사용 방법 알아보기
* [{{site.data.keyword.cloud_notm}} Log Analysis](/docs/services/CloudLogAnalysis?topic=cloudloganalysis-log_analysis_ov#log_analysis_ov)
* [{{site.data.keyword.cloud_notm}} Private ELK 스택](https://www.ibm.com/support/knowledgecenter/en/SSBS6K_2.1.0.2/manage_metrics/logging_elk.html){: new_window} ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")
