---

copyright:
  years: 2018, 2019
lastupdated: "2019-06-07"

keywords: object storage swift, static storage swift, file services swift, swift storage class, cos swift, swift data encryption, static swift

subcollection: swift

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# 정적 컨텐츠에 Object Storage 사용
{: #object-storage}

Object Storage는 클라우드 컴퓨팅의 기본 컴포넌트이며 Apple 개발자와 애플리케이션에게 강력한 기능을 제공합니다. 파일 계층 구조(예: 블록 또는 파일 스토리지)와 달리, 오브젝트 저장소는 버킷으로 알려진 콜렉션에 저장된 파일 및 메타데이터로만 구성됩니다. 정의상 이러한 오브젝트는 불변이며, 따라서 이미지, 동영상 및 기타 정적 문서에 대해 매우 적합합니다. 종종 변경되거나 관계형인 데이터의 경우 [Cloudant](/docs/swift/data?topic=swift-cloudant#cloudant) 및 [SQL](/docs/swift/data?topic=swift-sql_data#sql_data) 데이터베이스 서비스를 사용할 수 있습니다.

{{site.data.keyword.cos_full_notm}}(COS)는 유연하고 비용 효과적이며 스케일링 가능한 구조화되지 않은 데이터를 저장하는 데 사용될 수 있는 스토리지 시스템입니다. 데이터는 SDK를 통하거나 IBM 사용자 인터페이스를 사용하여 액세스할 수 있습니다. {{site.data.keyword.cos_full_notm}}를 사용하여 RESTful API 및 SDK로 지원되는 셀프 서비스 포털을 통해 구조화되지 않은 데이터에 액세스할 수 있습니다. 

필요에 따라 다음 서비스를 위해 {{site.data.keyword.cos_short}}를 사용할 수 있습니다.

* 컨텐츠 저장소
* 백업 및 복구
* 데이터 아카이브
* 파일 서비스
* 데이터 전송

버킷을 작성하는 경우 복원 레벨(교차 지역 또는 지역)을 선택해야 합니다. 사용자의 선택에 따라 데이터가 분산되어 둘 이상의 지리적 위치에 저장됩니다.

## API
{: #api-cos}

{{site.data.keyword.cos_full}} API는 오브젝트를 읽고 쓰기 위한 REST 기반 API입니다. 애플리케이션을 {{site.data.keyword.cloud_notm}}로 쉽게 마이그레이션하기 위해 S3 API의 서브세트를 지원합니다. 모든 S3 SDK는 {{site.data.keyword.cos_full}}를 사용하기 위해 사용될 수 있습니다. 자세한 정보는 전체 [{{site.data.keyword.cos_short}} API 참조](/docs/services/cloud-object-storage?topic=cloud-object-storage-compatibility-api)를 참조하십시오.

## 오브젝트 스토리지 보안
{: #security-cos}

{{site.data.keyword.cos_full_notm}}는 매우 안전합니다. 초기에는 버킷 및 오브젝트 소유자만 작성하는 {{site.data.keyword.cos_full_notm}} 서비스에 액세스할 수 있습니다. 데이터에 액세스하기 위해 사용자 인증도 지원합니다. 버킷 정책과 같은 액세스 제어 메커니즘을 사용하여 선택적으로 사용자 및 애플리케이션에게 권한을 부여하십시오. HTTPS 프로토콜을 사용하여 SSL 엔드포인트를 통해 데이터를 {{site.data.keyword.cos_short}}에 안전하게 전송할 수 있습니다. 추가 보안이 필요한 SSE(Server-Side Encryption) 옵션 또는 SSE-C(Server-Side Encryption with Customer-Provided Keys) 옵션을 사용하여 유휴 상태로 저장된 데이터를 암호화할 수 있습니다. {{site.data.keyword.cos_full_notm}}는 SSE 및 SSE-C 모두에 대한 암호화 기술을 제공합니다. 또는 Cloud Object Storage에 데이터를 저장하기 전에 고유한 암호화 라이브러리를 사용하여 데이터를 암호화할 수 있습니다.

다음 액세스 제어 메커니즘을 사용하여 {{site.data.keyword.cos_full_notm}}의 데이터를 보호할 수 있습니다.

### IAM(Identity and Access Management) 정책
{: #iam-cos}

IAM을 통해 많은 직원이 있는 조직은 하나의 계정으로 여러 명의 사용자를 작성하고 관리할 수 있습니다. IAM 정책을 사용하면 회사는 IAM 사용자에게 {{site.data.keyword.cos_short}} 버킷에 대한 제어를 부여할 수 있습니다.

### 액세스 제어 목록(ACL)
{: #acls-cos}

ACL을 사용하면 개별 버킷을 위해 특정 사용자에게 특정 권한(예: READ, WRITE)을 부여할 수 있습니다.

저장 데이터는 자동 제공자 측 고급 암호화 표준(AES) 256비트 암호화 및 보안 해시 알고리즘(SHA)-256 해시로 암호화됩니다. 작동 중인 데이터는 기본 캐리어 등급의 TLS(Transport Layer Security), SSL(Secure Sockets Layer) 또는 AES 암호화가 적용된 SNMPv3를 사용하여 보호됩니다.

### 암호화
{: #encryption-cos}

저장 데이터는 자동 제공자 측 고급 암호화 표준(AES) 256비트 암호화 및 보안 해시 알고리즘(SHA)-256 해시로 암호화됩니다. 작동 중인 데이터는 기본 캐리어 등급의 TLS/SSL(Transport Layer Security/Secure Sockets Layer) 또는 AES 암호화가 적용된 SNMPv3를 사용하여 보호됩니다.

서버 측 암호화는 항상 사용자 데이터에 대해 설정되어 있습니다. 인증 및 삭제 코딩에 필요한 해싱과 비교하면 암호화는 Cloud Object Storage의 처리 비용에서 중요한 부분이 아닙니다.

SSE-C가 {{site.data.keyword.cos_full_notm}}에서 지원되므로 암호화를 위해 고유한 키를 제공할 수 있습니다. 그러나 저장하기 위해 키를 관리 및 제공하고 데이터를 검색하는 책임은 사용자에게 있습니다.

## 복원
{: #resiliency-cos}

버킷을 작성하는 경우 복원 레벨(교차 지역 또는 지역)을 선택해야 합니다. 사용자의 선택에 따라 데이터가 분산되어 둘 이상의 지리적 위치에 저장됩니다.

지역 복원은 짧은 대기 시간에 적합하고 데이터는 단일 지역 내 세 개의 센터에 분배됩니다. 데이터가 세 개 이상의 다른 지역에 저장되므로 중요 가용성을 위해 교차 지역 복원성을 사용하십시오. 교차 지역은 지리적 복원을 제공하고 둘 이상의 엔드포인트에서 사용 가능합니다. 두 개 중에서 결정해야 하는 애플리케이션 요구사항을 고려하십시오.

### 지역
{: #geographies-cos}

전세계 어디서든 {{site.data.keyword.cos_full_notm}}를 사용할 수 있습니다. 버킷 작성 시 데이터를 저장할 위치를 선택할 수 있습니다. IBM COS에 저장된 정보는 암호화되어 있고 IBM Object Storage System에서 제공하는 분산 스토리지 기술을 사용하여 둘 이상의 지리적 위치에 분산되어 있습니다. 

지역과 교차 지역 옵션 중에서 결정하는 경우 오브젝트 저장소의 지리적 위치를 선택하려면 다음 요소를 고려하십시오.

지리적 위치 고려사항:
* 중복성을 위해 오퍼레이션에서 원격인 위치.
* 법적 및 규제 요구사항을 처리할 수 있는 위치.
* 데이터 액세스 대기 시간 단축.
* 가격 모델 고려.

## 유스 케이스 및 스토리지 클래스
{: #usecases-cos}

유스 케이스에 따라 요구사항을 충족하는 서비스 플랜을 선택하여 비용을 줄일 수 있습니다. 오브젝트 저장소에 대한 최소 액세스가 포함된 아카이브 오퍼레이션은 빈번하게 액세스되는 오브젝트의 속도 및 내구성이 필요하지 않으며, 이 차이는 사용자의 애플리케이션에 적합한 스토리지 클래스 지원 및 가격 플랜에 반영됩니다. 스토리지 클래스는 버킷 레벨에서 정의되므로 사용자의 요구사항에 적합한 플랜의 조합을 사용할 수 있습니다. 사용할 스토리지 클래스로 설정된 버킷을 작성하십시오.

가격에 대한 자세한 정보는 [{{site.data.keyword.cos_short}} 스토리지 클래스](/docs/services/cloud-object-storage/help?topic=cloud-object-storage-billing#ibm-cos-pricing) 문서에서 볼 수 있습니다.

### 샘플 스토리지 클래스
{: #samples-cos}

- Standard
  
  이 서비스는 DevOps, 협업 및 조치 컨텐츠 저장소와 같이 빈번한 액세스가 필요한 구조화되지 않은 데이터에 적합합니다.

- Vault
  
  이 서비스는 백업, 아카이브 및 규제 준수 워크로드와 같이 드물게 액세스되는 데이터가 포함된 워크로드에 적합합니다.

- Cold Vault
  
  이 배치 옵션은 최소 액세스 요구사항, 히스토리 레코드 규제 준수 및 장기 백업에 이상적입니다.

- Flex

  가변적인 데이터 액세스 요구사항에 맞게 배치하고 예기치 않은 비용 변동으로부터 예산을 보호합니다. 스토리지 클래스는 버킷 레벨에서 정의됩니다. 사용할 스토리지 클래스로 설정된 버킷을 작성하십시오.


