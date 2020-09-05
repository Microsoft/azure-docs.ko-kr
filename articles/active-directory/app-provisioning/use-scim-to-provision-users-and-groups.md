---
title: Azure AD에서 앱에 대한 사용자 프로비저닝을 위한 SCIM 엔드포인트 개발
description: SCIM(도메인 간 ID 관리를 위한 시스템)은 자동 사용자 프로비저닝을 표준화합니다. SCIM 엔드포인트를 개발하고, SCIM API를 Azure Active Directory와 통합하고, 프로비저닝 사용자 및 그룹을 클라우드 애플리케이션으로 자동화하는 방법을 알아봅니다.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 03/07/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: a8fa409a8ee66cd69016b7978f0d5f0194b338c4
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88959156"
---
# <a name="build-a-scim-endpoint-and-configure-user-provisioning-with-azure-ad"></a>Azure AD를 사용하여 SCIM 엔드포인트 빌드 및 사용자 프로비저닝 구성

애플리케이션 개발자는 SCIM(도메인 간 ID 관리를 위한 시스템) 사용자 관리 API를 사용하여 애플리케이션과 Azure AD간에 사용자 및 그룹을 자동으로 프로비저닝할 수 있습니다. 이 문서에서는 SCIM 엔드포인트를 빌드하고 Azure AD 프로비저닝 서비스와 통합하는 방법을 설명합니다. SCIM 사양에서는 프로비저닝을 위한 일반 사용자 스키마를 제공합니다. SCIM은 SAML 또는 OpenID Connect와 같은 페더레이션 표준과 함께 사용하면 액세스 관리를 위한 전반적인 표준 기반 솔루션을 관리자에게 제공합니다.

SCIM은 두 엔드포인트(/Users 엔드포인트 및 /Groups 엔드포인트)의 표준화된 정의입니다. 일반 REST 동사를 사용하여 개체를 생성, 업데이트 및 삭제하며 그룹 이름, 사용자 이름, 이름, 성, 메일 등의 일반 특성에 대한 스키마를 사전 정의합니다. SCIM 2.0 REST API를 제공하는 앱은 독점적인 사용자 관리 API로 작업할 필요를 줄이거나 없앨 수 있습니다. 예를 들어 모든 규격 SCIM 클라이언트는 /Users 엔드포인트에 대한 JSON 개체의 HTTP POST를 만들어 새 사용자 항목을 생성하는 방법을 알고 있습니다. SCIM 표준을 준수하는 앱은 동일한 기본 작업에 대해 약간 다른 API를 사용하지 않고도 기존 클라이언트, 도구 및 코드를 바로 활용할 수 있습니다. 

![SCIM을 사용하여 Azure AD에서 앱으로 프로비저닝](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

SCIM 2.0(RFC [7642](https://tools.ietf.org/html/rfc7642), [7643](https://tools.ietf.org/html/rfc7643), [7644](https://tools.ietf.org/html/rfc7644))에 정의된 관리용 표준 사용자 개체 스키마 및 REST API를 사용하여 ID 공급자와 앱을 서로 쉽게 통합할 수 있습니다. SCIM 엔드포인트를 빌드하는 애플리케이션 개발자는 사용자 지정 작업을 수행하지 않고도 SCIM 규격 클라이언트와 통합할 수 있습니다.

애플리케이션에 프로비저닝을 자동화하려면 SCIM 엔드포인트를 빌드하고 Azure AD SCIM 클라이언트와 통합해야 합니다. 애플리케이션에 사용자 및 그룹 프로비저닝을 시작하려면 다음 단계를 수행합니다. 
    
  * **[1단계: 사용자 및 그룹 스키마 디자인.](#step-1-design-your-user-and-group-schema)** 애플리케이션에 필요한 개체 및 특성을 확인하고 Azure AD SCIM 구현에서 지원하는 사용자 및 그룹 스키마에 매핑되는 방법을 결정합니다.

  * **[2단계: Azure AD SCIM 구현 이해.](#step-2-understand-the-azure-ad-scim-implementation)** Azure AD SCIM 클라이언트를 구현하는 방법을 이해하고 SCIM 프로토콜 요청 처리 및 응답을 모델링합니다.

  * **[3단계: SCIM 엔드포인트 빌드.](#step-3-build-a-scim-endpoint)** Azure AD 프로비저닝 서비스와 통합하려면 엔드포인트가 SCIM 2.0과 호환되어야 합니다. 옵션으로 Microsoft CLI(공용 언어 인프라) 라이브러리 및 코드 샘플을 사용하여 엔드포인트를 빌드할 수 있습니다. 샘플은 참조 및 테스트용으로만 사용할 수 있습니다. 이에 대한 종속성을 가지려면 프로덕션 앱을 코딩에 대비하는 것이 좋습니다.

  * **[4단계: SCIM 엔드포인트를 Azure AD SCIM 클라이언트와 통합.](#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)** Azure AD가 지원하는 SCIM 2.0의 프로필을 구현하는 타사 애플리케이션을 조직이 사용하는 경우 사용자 및 그룹의 프로비저닝 및 프로비저닝 해제를 모두 자동화할 수 있습니다.

  * **[5단계: Azure AD 애플리케이션 갤러리에 애플리케이션 게시.](#step-5-publish-your-application-to-the-azure-ad-application-gallery)** 고객이 애플리케이션을 검색하고 프로비저닝을 쉽게 구성할 수 있도록 합니다. 

![SCIM 엔드포인트를 Azure AD와 통합하기 위한 단계](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="step-1-design-your-user-and-group-schema"></a>1단계: 사용자 및 그룹 스키마 디자인

모든 애플리케이션에는 사용자 또는 그룹을 만들기 위해 여러 특성이 필요합니다. 애플리케이션에 필요한 개체(사용자, 그룹) 및 특성(이름, 관리자, 직함 등)을 식별하여 통합을 시작합니다. SCIM 표준은 사용자 및 그룹을 관리하기 위한 스키마를 정의합니다. 핵심 사용자 스키마에는 **id**\(서비스 공급자 정의 식별자\), **externalId**\(클라이언트 정의 식별자\) 및 **meta**\(서비스 공급자가 유지 관리하는 읽기 전용 메타데이터\)의 세 가지 특성만 필요합니다. 기타 모든 특성은 선택 사항입니다. SCIM 표준은 핵심 사용자 스키마 외에도 애플리케이션의 요구에 맞게 사용자 스키마를 확장하기 위한 모델 및 엔터프라이즈 사용자 확장을 정의합니다. 예를 들어 애플리케이션에 사용자 관리자가 필요한 경우 엔터프라이즈 사용자 스키마를 사용하여 사용자 관리자를 수집하고 핵심 스키마를 사용하여 사용자의 메일을 수집할 수 있습니다. 스키마를 디자인하려면 다음 단계를 수행합니다.
  1. 애플리케이션에 필요한 특성을 나열합니다. 그러면 요구 사항을 인증에 필요한 특성(예: loginName 및 메일), 사용자의 수명 주기를 관리하는 데 필요한 특성(예: 상태/활성) 및 특정 애플리케이션이 작동하는 데 필요한 기타 특성(예: 관리자, 태그)으로 분류하는 데 도움이 될 수 있습니다.
  2. 해당 특성이 핵심 사용자 스키마 또는 엔터프라이즈 사용자 스키마에 이미 정의되어 있는지 확인하세요. 필요한 특성이 핵심 또는 엔터프라이즈 사용자 스키마에 포함되어 있지 않으면 필요한 특성을 포함하는 사용자 스키마에 대한 확장을 정의해야 합니다. 아래 예제에서는 사용자에게 “tag”를 프로비저닝할 수 있도록 사용자에 대한 확장을 추가했습니다. 핵심 및 엔터프라이즈 사용자 스키마로 시작하고 나중에 추가 사용자 지정 스키마로 확장하는 것이 가장 좋습니다.  
  3. SCIM 특성을 Azure AD의 사용자 특성에 매핑합니다. SCIM 엔드포인트에 정의된 특성 중 하나에 Azure AD 사용자 스키마에 대한 명확한 대응 항목이 없으면 대부분의 테넌트에서 데이터를 사용자 개체에 저장하지 않는 것이 좋습니다. 사용자를 생성하기 위해 이 특성을 선택적으로 사용할 수 있는지 고려하세요. 애플리케이션이 작동하는 데 중요한 특성이면 테넌트 관리자로 해당 스키마를 확장하거나 아래와 같이 “tag” 특성에 대한 확장 특성을 사용하도록 안내합니다.

### <a name="table-1-outline-the-attributes-that-you-need"></a>테이블 1: 필요한 특성을 간략하게 설명 
| 1단계: 앱에 필요한 특성 결정| 2단계: SCIM 표준에 앱 요구 사항 매핑| 3단계: Azure AD 특성에 SCIM 특성 매핑|
|--|--|--|
|loginName|userName|userPrincipalName|
|firstName|name.givenName|givenName|
|lastName|name.lastName|lastName|
|workMail|Emails[type eq “work”].value|Mail|
|manager|manager|manager|
|tag|urn:ietf:params:scim:schemas:extension:2.0:CustomExtension:tag|extensionAttribute1|
|상태|활성|isSoftDeleted(사용자에 저장되지 않은 계산된 값)|

위에서 정의한 스키마는 아래 JSON 페이로드를 사용 하 여 표시 됩니다. 응용 프로그램에 필요한 특성 외에도 JSON 표현에는 필수 `id` , `externalId` 및 특성이 포함 됩니다 `meta` .

```json
{
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen",
     "externalId":"bjensen",
     "name":{
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "Manager": "123456"
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:CustomAttribute:User": {
     "tag": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
 ```

### <a name="table-2-default-user-attribute-mapping"></a>표 2: 기본 사용자 특성 매핑
그런 다음, 아래 표를 사용하여 애플리케이션에 필요한 특성을 이해하고, Azure AD 및 SCIM RFC의 특성에 매핑할 수 있는 방법을 살펴볼 수 있습니다. Azure AD 및 SCIM 엔드포인트 간에 특성이 매핑되는 방식을 [사용자 지정](customize-application-attributes.md)할 수 있습니다. 사용자와 그룹 둘 다 또는 아래에 표시된 모든 특성을 지원할 필요는 없습니다. Azure AD의 특성이 SCIM 프로토콜의 속성에 매핑되는 빈도에 대한 참조입니다. 

| Azure Active Directory 사용자 | "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |활성 |
|department|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|
| displayName |displayName |
|employeeId|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|
| Facsimile-TelephoneNumber |phoneNumbers[type eq "fax"].value |
| givenName |name.givenName |
| jobTitle |title |
| mail |emails[type eq "work"].value |
| mailNickname |externalId |
| manager |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager |
| mobile |phoneNumbers[type eq "mobile"].value |
| postalCode |addresses[type eq "work"].postalCode |
| proxy-Addresses |emails[type eq "other"].Value |
| physical-Delivery-OfficeName |addresses[type eq "other"].Formatted |
| streetAddress |addresses[type eq "work"].streetAddress |
| surname |name.familyName |
| telephone-Number |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |


### <a name="table-3-default-group-attribute-mapping"></a>표 3: 기본 그룹 특성 매핑

| Azure Active Directory 그룹 | urn:ietf:params:scim:schemas:core:2.0:Group |
| --- | --- |
| displayName |displayName |
| mail |emails[type eq "work"].value |
| mailNickname |displayName |
| members |members |
| objectId |externalId |
| proxyAddresses |emails[type eq "other"].Value |

SCIM RFC에는 여러 엔드포인트가 정의되어 있습니다. /User 엔드포인트를 시작한 후 해당 위치에서 확장할 수 있습니다. /Schemas 엔드포인트는 사용자 지정 특성을 사용하거나 스키마가 자주 변경되는 경우에 유용합니다. 이를 통해 클라이언트는 최신 스키마를 자동으로 검색할 수 있습니다. /Bulk 엔드포인트는 그룹을 지원할 때 특히 유용합니다. 다음 표에서는 SCIM 표준에 정의된 다양한 엔드포인트에 대해 설명합니다. /Schemas 엔드포인트는 사용자 지정 특성을 사용하거나 스키마가 자주 변경되는 경우에 유용합니다. 이를 통해 클라이언트는 최신 스키마를 자동으로 검색할 수 있습니다. /Bulk 엔드포인트는 그룹을 지원할 때 특히 유용합니다. 다음 표에서는 SCIM 표준에 정의된 다양한 엔드포인트에 대해 설명합니다. 
 
### <a name="table-4-determine-the-endpoints-that-you-would-like-to-develop"></a>표 4: 개발하려는 엔드포인트 결정
|엔드포인트|설명|
|--|--|
|/User|사용자 개체에 대한 CRUD 작업을 수행합니다.|
|/Group|그룹 개체에 대한 CRUD 작업을 수행합니다.|
|/ServiceProviderConfig|지원되는 리소스 및 인증 방법 등 지원되는 SCIM 표준의 기능에 대한 세부 정보를 제공합니다.|
|/ResourceTypes|각 리소스에 대한 메타데이터를 지정합니다.|
|/Schemas|각 클라이언트 및 서비스 공급자가 지원하는 특성 집합은 다를 수 있습니다. 한 서비스 공급자에는, 및이 포함 될 수 있지만 `name` `title` `emails` 다른 서비스 공급자는 `name` , 및를 사용 `title` `phoneNumbers` 합니다. 스키마 엔드포인트는 지원되는 특성의 검색을 허용합니다.|
|/Bulk|대량 작업을 수행하면 단일 작업에서 대규모 리소스 개체 컬렉션에 대한 작업을 수행할 수 있습니다(예: 대규모 그룹의 멤버 자격 업데이트).|


## <a name="step-2-understand-the-azure-ad-scim-implementation"></a>2단계: Azure AD SCIM 구현 이해
> [!IMPORTANT]
> Azure AD SCIM 구현 동작이 2018년 12월 18일에 마지막으로 업데이트되었습니다. 변경된 내용에 대한 자세한 내용은 [Azure AD 사용자 프로비저닝 서비스의 SCIM 2.0 프로토콜 규정 준수](application-provisioning-config-problem-scim-compatibility.md)를 참조하세요.

SCIM 2.0 사용자 관리 API를 지원하는 애플리케이션을 빌드하는 경우 이 섹션에서는 Azure AD SCIM 클라이언트를 구현하는 방법을 자세히 설명합니다. 또한 SCIM 프로토콜 요청 처리 및 응답을 모델링하는 방법을 보여 줍니다. SCIM 엔드포인트를 구현하면 이전 섹션에서 설명한 절차에 따라 테스트할 수 있습니다.

[SCIM 2.0 프로토콜 사양](http://www.simplecloud.info/#Specification) 내에서 애플리케이션은 다음의 요구 사항을 충족해야 합니다.

* [SCIM 프로토콜의 섹션 3.3](https://tools.ietf.org/html/rfc7644#section-3.3)에 따라 사용자 또는 필요에 따라 그룹 만들기를 지원합니다.  
* [SCIM 프로토콜의 섹션 3.5.2](https://tools.ietf.org/html/rfc7644#section-3.5.2)에 따라 PATCH 요청을 사용하여 사용자 또는 그룹 수정을 지원합니다.  
* [SCIM 프로토콜의 섹션 3.4.1](https://tools.ietf.org/html/rfc7644#section-3.4.1)에 따라 앞에서 만든 사용자나 그룹에 대해 알려진 리소스 검색을 지원합니다.  
* [SCIM 프로토콜의 섹션 3.4.2](https://tools.ietf.org/html/rfc7644#section-3.4.2)에 따라 사용자 또는 그룹 쿼리를 지원합니다.  기본적으로 사용자는 `id`로 검색되고 `username` 및 `externalId`에 의해 쿼리되며 그룹은 `displayName`에 의해 쿼리됩니다.  
* SCIM 프로토콜의 섹션 3.4.2에 따라 ID 및 관리자에 의한 사용자 쿼리를 지원합니다.  
* SCIM 프로토콜의 섹션 3.4.2에 따라 ID 및 멤버에 의한 그룹 쿼리를 지원합니다.  
* 애플리케이션에 대한 Azure AD의 인증 및 권한 부여를 위해 단일 전달자 토큰을 허용합니다.
* 사용자를 일시 삭제 하 고 사용자를 복원 하는 기능을 지원 합니다. 사용자가 `active=false` `active=true` 활성 상태 인지 여부에 관계 없이 사용자 개체가 요청에서 반환 되어야 합니다. 응용 프로그램에서 하드 삭제 된 경우에만 사용자가 반환 되는 것은 아닙니다. 

Azure AD와의 호환성을 보장하기 위해 SCIM 엔드포인트를 구현할 때 다음 일반 지침을 따릅니다.

* `id`는 모든 리소스의 필수 속성입니다. 리소스를 반환하는 모든 응답은 멤버가 0인 `ListResponse`를 제외하고 각 리소스에 이 속성이 있는지 확인해야 합니다.
* 쿼리/필터 요청에 대한 응답은 항상 `ListResponse`여야 합니다.
* 그룹은 선택 사항이지만 SCIM 구현이 PATCH 요청을 지원하는 경우에만 지원됩니다.
* PATCH 응답에 전체 리소스를 포함할 필요는 없습니다.
* Microsoft Azure AD는 다음 연산자만 사용합니다.  
    - `eq`
    - `and`
* https://tools.ietf.org/html/rfc7644#section-3.5.2 에 정의된 SCIM의 구조적 요소, 특히 PATCH `op` 작업 값에 대해 대소문자를 구분하지 않아도 됩니다. Azure AD는 `Add`, `Replace` 및 `Remove`로 ‘op’의 값을 내보냅니다.
* Microsoft Azure AD는 엔드포인트 및 자격 증명이 유효한지 확인하기 위해 임의 사용자 및 그룹을 가져오도록 요청합니다. [Azure Portal](https://portal.azure.com)에서 **테스트 연결** 흐름의 일부로도 수행됩니다. 
* 리소스를 쿼리할 수 있는 특성은 [Azure Portal](https://portal.azure.com)의 애플리케이션에 일치하는 특성으로 설정해야 합니다. 자세한 내용은 [사용자 프로비저닝 특성 매핑 사용자 지정](customize-application-attributes.md)을 참조하세요.

### <a name="user-provisioning-and-deprovisioning"></a>사용자 프로비저닝 및 프로비저닝 해제

다음 그림은 애플리케이션의 ID 저장소에 사용자의 수명 주기를 관리하도록 Azure Active Directory가 SCIM 서비스를 보낸다는 메시지를 보여 줍니다.  

![사용자 프로비저닝 및 프로비저닝 해제 시퀀스를 보여 줌](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
‘사용자 프로비저닝 및 프로비저닝 해제 시퀀스’

### <a name="group-provisioning-and-deprovisioning"></a>그룹 프로비저닝 및 프로비저닝 해제

그룹 프로비저닝 및 프로비저닝 해제는 선택 사항입니다. 구현 및 사용하도록 설정된 경우 다음 그림은 애플리케이션의 ID 저장소에 그룹의 수명 주기를 관리하도록 Azure Active Directory가 SCIM 서비스를 보낸다는 메시지를 보여 줍니다.  해당 메시지는 두 가지 측면에서 사용자에 대한 메시지와 다릅니다.

* 그룹을 검색하는 요청은 멤버 특성이 요청에 대한 응답에서 제공된 리소스로부터 제외된다고 지정합니다.  
* 참조 특성에 특정 값이 있는지를 확인하는 요청은 멤버 특성에 대한 요청입니다.  

![그룹 프로비저닝 및 프로비저닝 해제 시퀀스를 보여 줌](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
‘그룹 프로비저닝 및 프로비저닝 해제 시퀀스’

### <a name="scim-protocol-requests-and-responses"></a>SCIM 프로토콜 요청 및 응답
이 섹션에서는 Azure AD SCIM 클라이언트에서 내보낸 예제 SCIM 요청 및 예상 응답 예제를 제공합니다. 최상의 결과를 위해서는 요청을 이 형식으로 처리하고 예상되는 응답을 내보내는 앱을 코딩해야 합니다.

> [!IMPORTANT]
> Azure AD 사용자 프로비저닝 서비스에서 아래에 설명된 작업을 내보내는 방법과 시기를 알아보려면 [프로비저닝 주기 섹션을 참조하세요. [프로비저닝 작동 방식](how-provisioning-works.md)의 초기 및 증분](how-provisioning-works.md#provisioning-cycles-initial-and-incremental)

[사용자 작업](#user-operations)
  - [사용자 만들기](#create-user)([요청](#request) / [응답](#response))
  - [사용자 가져오기](#get-user)([요청](#request-1) / [응답](#response-1))
  - [쿼리로 사용자 가져오기](#get-user-by-query)([요청](#request-2) / [응답](#response-2))
  - [쿼리로 사용자 가져오기 - 결과 없음](#get-user-by-query---zero-results)([요청](#request-3)
/ [응답](#response-3))
  - [사용자 업데이트[다중값 속성]](#update-user-multi-valued-properties)([요청](#request-4) /  [응답](#response-4))
  - [사용자 업데이트[단일값 속성]](#update-user-single-valued-properties)([요청](#request-5)
/ [응답](#response-5)) 
  - [사용자 사용 안 함](#disable-user)([요청](#request-14) / 
[응답](#response-14))
  - [사용자 삭제](#delete-user)([요청](#request-6) / 
[응답](#response-6))


[그룹 작업](#group-operations)
  - [그룹 만들기](#create-group)([요청](#request-7) / [응답](#response-7))
  - [그룹 가져오기](#get-group)([요청](#request-8) / [응답](#response-8))
  - [displayName으로 그룹 가져오기](#get-group-by-displayname)([요청](#request-9) / [응답](#response-9))
  - [그룹 업데이트[비멤버 특성]](#update-group-non-member-attributes)([요청](#request-10) /
 [응답](#response-10))
  - [그룹 업데이트[멤버 추가]](#update-group-add-members)([요청](#request-11) /
[응답](#response-11))
  - [그룹 업데이트[멤버 제거]](#update-group-remove-members)([요청](#request-12) /
[응답](#response-12))
  - [그룹 삭제](#delete-group)([요청](#request-13) /
[응답](#response-13))

### <a name="user-operations"></a>사용자 작업

* `userName` 또는 `email[type eq "work"]` 특성으로 사용자를 쿼리할 수 있습니다.  

#### <a name="create-user"></a>사용자 만들기

###### <a name="request"></a>요청

*POST /Users*
```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "active": true,
    "emails": [{
        "primary": true,
        "type": "work",
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com"
    }],
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName"
    },
    "roles": []
}
```

##### <a name="response"></a>응답

*HTTP/1.1 201 Created*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "48af03ac28ad4fb88478",
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="get-user"></a>사용자 가져오기

###### <a name="request"></a><a name="request-1"></a>요청
*GET /Users/5d48a0a8e9f04aa38008* 

###### <a name="response-user-found"></a><a name="response-1"></a>응답(사용자를 찾음)
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5d48a0a8e9f04aa38008",
    "externalId": "58342554-38d6-4ec8-948c-50044d0a33fd",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_feed3ace-693c-4e5a-82e2-694be1b39934",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_22370c1a-9012-42b2-bf64-86099c2a1c22@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

###### <a name="request"></a>요청
*GET /Users/5171a35d82074e068ce2* 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>응답(사용자를 찾을 수 없습니다. 세부 정보는 필요하지 않으며 상태만 필요합니다.)

```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:Error"
    ],
    "status": "404",
    "detail": "Resource 23B51B0E5D7AE9110A49411D@7cca31655d49f3640a494224 not found"
}
```

#### <a name="get-user-by-query"></a>쿼리로 사용자 가져오기

##### <a name="request"></a><a name="request-2"></a>요청

*GET /Users?filter=userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response"></a><a name="response-2"></a>응답

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
        "id": "2441309d85324e7793ae",
        "externalId": "7fce0092-d52e-4f76-b727-3955bd72c939",
        "meta": {
            "resourceType": "User",
            "created": "2018-03-27T19:59:26.000Z",
            "lastModified": "2018-03-27T19:59:26.000Z"
            
        },
        "userName": "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081",
        "name": {
            "familyName": "familyName",
            "givenName": "givenName"
        },
        "active": true,
        "emails": [{
            "value": "Test_User_91b67701-697b-46de-b864-bd0bbe4f99c1@testuser.com",
            "type": "work",
            "primary": true
        }]
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="get-user-by-query---zero-results"></a>쿼리로 사용자 가져오기 - 결과 없음

##### <a name="request"></a><a name="request-3"></a>요청

*GET /Users?filter=userName eq "non-existent user"*

##### <a name="response"></a><a name="response-3"></a>응답

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 0,
    "Resources": [],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="update-user-multi-valued-properties"></a>사용자 업데이트[다중값 속성]

##### <a name="request"></a><a name="request-4"></a>요청

*PATCH /Users/6764549bef60420686bc HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [
            {
            "op": "Replace",
            "path": "emails[type eq \"work\"].value",
            "value": "updatedEmail@microsoft.com"
            },
            {
            "op": "Replace",
            "path": "name.familyName",
            "value": "updatedFamilyName"
            }
    ]
}
```

##### <a name="response"></a><a name="response-4"></a>응답

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "6764549bef60420686bc",
    "externalId": "6c75de36-30fa-4d2d-a196-6bdcdb6b6539",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_fbb9dda4-fcde-4f98-a68b-6c5599e17c27",
    "name": {
        "formatted": "givenName updatedFamilyName",
        "familyName": "updatedFamilyName",
        "givenName": "givenName"
    },
    "active": true,
    "emails": [{
        "value": "updatedEmail@microsoft.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="update-user-single-valued-properties"></a>사용자 업데이트[단일값 속성]

##### <a name="request"></a><a name="request-5"></a>요청

*PATCH /Users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "userName",
        "value": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com"
    }]
}
```

##### <a name="response"></a><a name="response-5"></a>응답

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5171a35d82074e068ce2",
    "externalId": "aa1eca08-7179-4eeb-a0be-a519f7e5cd1a",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "userName": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_49dc1090-aada-4657-8434-4995c25a00f7@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

### <a name="disable-user"></a>사용자 사용 안 함

##### <a name="request"></a><a name="request-14"></a>요청

*PATCH /Users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```

##### <a name="response"></a><a name="response-14"></a>응답

```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User"
    ],
    "id": "CEC50F275D83C4530A495FCF@834d0e1e5d8235f90a495fda",
    "userName": "deanruiz@testuser.com",
    "name": {
        "familyName": "Harris",
        "givenName": "Larry"
    },
    "active": false,
    "emails": [
        {
            "value": "gloversuzanne@testuser.com",
            "type": "work",
            "primary": true
        }
    ],
    "addresses": [
        {
            "country": "ML",
            "type": "work",
            "primary": true
        }
    ],
    "meta": {
        "resourceType": "Users",
        "location": "/scim/5171a35d82074e068ce2/Users/CEC50F265D83B4530B495FCF@5171a35d82074e068ce2"
    }
}
```
#### <a name="delete-user"></a>사용자 삭제

##### <a name="request"></a><a name="request-6"></a>요청

*DELETE /Users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response"></a><a name="response-6"></a>응답

*HTTP/1.1 204 No Content*

### <a name="group-operations"></a>그룹 작업

* 그룹은 항상 빈 멤버 목록으로 만들어야 합니다.
* `displayName` 특성으로 그룹을 쿼리할 수 있습니다.
* 그룹 PATCH 요청을 업데이트하면 응답에 *HTTP 204 No Content*이 나타납니다. 모든 멤버 목록이 포함된 본문을 반환하는 것은 권장되지 않습니다.
* 그룹의 모든 멤버를 반환하는 작업을 지원할 필요는 없습니다.

#### <a name="create-group"></a>그룹 만들기

##### <a name="request"></a><a name="request-7"></a>요청

*POST /Groups HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group", "http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/2.0/Group"],
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "displayName": "displayName",
    "meta": {
        "resourceType": "Group"
    }
}
```

##### <a name="response"></a><a name="response-7"></a>응답

*HTTP/1.1 201 Created*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "927fa2c08dcb4a7fae9e",
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "displayName": "displayName",
    "members": []
}
```

#### <a name="get-group"></a>그룹 가져오기

##### <a name="request"></a><a name="request-8"></a>요청

*GET /Groups/40734ae655284ad3abcc?excludedAttributes=members HTTP/1.1*

##### <a name="response"></a><a name="response-8"></a>응답
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "40734ae655284ad3abcc",
    "externalId": "60f1bb27-2e1e-402d-bcc4-ec999564a194",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "displayName": "displayName",
}
```

#### <a name="get-group-by-displayname"></a>displayName으로 그룹 가져오기

##### <a name="request"></a><a name="request-9"></a>요청
*GET /Groups?excludedAttributes=members&filter=displayName eq "displayName" HTTP/1.1*

##### <a name="response"></a><a name="response-9"></a>응답

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
        "id": "8c601452cc934a9ebef9",
        "externalId": "0db508eb-91e2-46e4-809c-30dcbda0c685",
        "meta": {
            "resourceType": "Group",
            "created": "2018-03-27T22:02:32.000Z",
            "lastModified": "2018-03-27T22:02:32.000Z",
            
        },
        "displayName": "displayName",
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}
```

#### <a name="update-group-non-member-attributes"></a>그룹 업데이트[비멤버 특성]

##### <a name="request"></a><a name="request-10"></a>요청

*PATCH /Groups/fa2ce26709934589afc5 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "displayName",
        "value": "1879db59-3bdf-4490-ad68-ab880a269474updatedDisplayName"
    }]
}
```

##### <a name="response"></a><a name="response-10"></a>응답

*HTTP/1.1 204 No Content*

### <a name="update-group-add-members"></a>그룹 업데이트[멤버 추가]

##### <a name="request"></a><a name="request-11"></a>요청

*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Add",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response"></a><a name="response-11"></a>응답

*HTTP/1.1 204 No Content*

#### <a name="update-group-remove-members"></a>그룹 업데이트[멤버 제거]

##### <a name="request"></a><a name="request-12"></a>요청

*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Remove",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response"></a><a name="response-12"></a>응답

*HTTP/1.1 204 No Content*

#### <a name="delete-group"></a>그룹 삭제

##### <a name="request"></a><a name="request-13"></a>요청

*DELETE /Groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response"></a><a name="response-13"></a>응답

*HTTP/1.1 204 No Content*

### <a name="security-requirements"></a>보안 요구 사항
**TLS 프로토콜 버전**

TLS 프로토콜 버전은 TLS 1.2 및 TLS 1.3만 허용됩니다. 다른 버전의 TLS는 허용되지 않습니다. SSL 버전은 허용되지 않습니다. 
- RSA 키는 2048비트 이상이어야 합니다.
- ECC 키는 승인된 타원 곡선을 사용하여 생성된 256비트 이상이어야 합니다.


**키 길이**

모든 서비스는 충분한 길이의 암호화 키를 사용하여 생성된 X.509 인증서를 사용해야 합니다. 즉, 다음을 의미합니다.

**암호 그룹**

모든 서비스는 아래에 지정된 정확한 순서대로 다음 암호 도구 모음을 사용하도록 구성해야 합니다. RSA 인증서만 있는 경우에는 설치된 ECDSA 암호 도구 모음에 영향을 주지 않습니다. </br>

TLS 1.2 암호 도구 모음 최소 막대:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

### <a name="ip-ranges"></a>IP 범위
Azure AD 프로 비전 서비스는 현재 [여기](https://www.microsoft.com/download/details.aspx?id=56519&WT.mc_id=rss_alldownloads_all)에 나열 된 대로 AzureActiveDirectory 및 AzureActiveDirectoryDomainServices에 대 한 IP 범위에서 작동 합니다. AzureActiveDirectory 아래에 있는 IP 범위에만 통합 하는 작업이 진행 중입니다. 

## <a name="step-3-build-a-scim-endpoint"></a>3단계: SCIM 엔드포인트 빌드

스키마를 디자인하고 Azure AD SCIM 구현을 이해했으므로 이제 SCIM 엔드포인트 개발을 시작할 수 있습니다. 처음부터 시작하여 구현을 완전히 빌드하지 않고도 SCIM 커뮤니티에서 게시한 여러 오픈 소스 SCIM 라이브러리를 사용할 수 있습니다.

Azure AD 프로비저닝 팀이 게시한 오픈 소스 .NET Core [참조 코드](https://aka.ms/SCIMReferenceCode)는 개발을 바로 시작할 수 있는 리소스 중 하나입니다. SCIM 엔드포인트를 빌드하면 테스트를 수행할 수 있습니다. 참조 코드의 일부로 제공된 [postman 테스트](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) 컬렉션을 사용하거나 [위의](#user-operations)에서 제공된 샘플 요청/응답을 실행할 수 있습니다.  

   > [!Note]
   > 참조 코드는 SCIM 엔드포인트 빌드를 시작하도록 고안되었으며 “현재 상태로” 제공됩니다. 커뮤니티에서의 기여는 코드 빌드 및 유지 관리에 도움이 됩니다.

이 솔루션은 2개의 프로젝트인 _Microsoft.SCIM_ 및 _Microsoft.SCIM.WebHostSample_로 구성되어 있습니다.

_Microsoft.SCIM_ 프로젝트는 웹 서비스의 구성 요소를 정의하는 라이브러리로, SCIM 사양을 준수합니다. 이 프로젝트는 _Microsoft.SCIM.IProvider_ 인터페이스를 선언하고 요청은 공급자의 메서드에 대한 호출로 변환되며, 이는 ID 저장소에서 작동하도록 프로그래밍됩니다.

![분석 결과: 공급자의 메서드에 대한 호출로 변환된 요청](media/use-scim-to-provision-users-and-groups/scim-figure-3.png)

_Microsoft.SCIM.WebHostSample_ 프로젝트는 ‘빈’ 템플릿을 기반으로 하는 Visual Studio ASP.NET Core 웹 애플리케이션입니다. 이를 통해 샘플 코드를 독립형으로 배포하며 컨테이너 또는 인터넷 정보 서비스 내에 호스팅할 수 있습니다. 또한 메모리의 클래스를 샘플 ID 저장소로 유지하는 _Microsoft.SCIM.IProvider_ 인터페이스를 구현합니다.

```csharp
    public class Startup
    {
        ...
        public IMonitor MonitoringBehavior { get; set; }
        public IProvider ProviderBehavior { get; set; }

        public Startup(IWebHostEnvironment env, IConfiguration configuration)
        {
            ...
            this.MonitoringBehavior = new ConsoleMonitor();
            this.ProviderBehavior = new InMemoryProvider();
        }
        ...
```

### <a name="building-a-custom-scim-endpoint"></a>사용자 지정 SCIM 엔드포인트 빌드

SCIM 서비스에는 루트 인증 기관의 이름이 다음 중 하나인 HTTP 주소 및 서버 인증 인증서가 있어야 합니다.

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

.NET Core SDK에는 개발 중에 사용할 수 있는 HTTPS 개발 인증서가 포함되어 있으며 인증서는 최초 실행 환경의 일부로 설치됩니다. ASP.NET Core 웹 애플리케이션을 실행하는 방법에 따라 다른 포트를 수신 대기합니다.

* Microsoft.SCIM.WebHostSample: https://localhost:5001
* IIS Express: https://localhost:44359/

ASP.NET Core의 HTTPS에 대한 자세한 내용은 다음 링크를 참조하세요. [ASP.NET Core에서 HTTPS 적용](/aspnet/core/security/enforcing-ssl)

### <a name="handling-endpoint-authentication"></a>엔드포인트 인증 처리

Azure Active Directory에서 요청은 OAuth 2.0 전달자 토큰을 포함합니다. 요청을 받는 모든 서비스는 예상된 Azure Active Directory 테넌트의 Azure Active Directory로 발급자를 인증해야 합니다.

토큰에서 발급자는 `"iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/"`와 같은 ISS 클레임으로 식별됩니다. 이 예제에서 클레임 값의 기본 주소인 `https://sts.windows.net`은 Azure Active Directory를 발급자로 식별하며, 상대 주소 세그먼트인 _cbb1a5ac-f33b-45fa-9bf5-f37db0fed422_는 토큰이 발급된 Azure Active Directory 테넌트의 고유한 식별자입니다.

토큰의 대상 그룹은 갤러리의 애플리케이션에 대한 애플리케이션 템플릿 ID이며 단일 테넌트에 등록된 각 애플리케이션은 SCIM 요청과 동일한 `iss` 클레임을 받을 수 있습니다. 모든 사용자 지정 앱의 애플리케이션 템플릿 ID는 _8adf8e6e-67b2-4cf2-a259-e3dc5476c621_입니다. Azure AD 프로비저닝 서비스로 생성된 토큰은 테스트에만 사용해야 합니다. 프로덕션 환경에서는 사용하면 안 됩니다.

샘플 코드에서 요청은 Microsoft.AspNetCore.Authentication.JwtBearer 패키지를 사용하여 인증됩니다. 다음 코드는 서비스의 엔드포인트에 대한 요청이 지정된 테넌트에 대해 Azure Active Directory에서 발급한 전달자 토큰을 사용하여 인증되도록 합니다.

```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            if (_env.IsDevelopment())
            {
                ...
            }
            else
            {
                services.AddAuthentication(options =>
                {
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                })
                    .AddJwtBearer(options =>
                    {
                        options.Authority = " https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/";
                        options.Audience = "8adf8e6e-67b2-4cf2-a259-e3dc5476c621";
                        ...
                    });
            }
            ...
        }

        public void Configure(IApplicationBuilder app)
        {
            ...
            app.UseAuthentication();
            app.UseAuthorization();
            ...
       }
```

전달자 토큰은 제공된 [postman 테스트](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint)를 사용하고 localhost를 사용하여 로컬 디버깅을 수행하는 데에도 필요합니다. 샘플 코드는 ASP.NET Core 환경을 사용하여 개발 단계 중에 인증 옵션을 변경하고 자체 서명된 토큰을 사용할 수 있도록 합니다.

ASP.NET Core에서의 여러 환경에 대한 자세한 내용은 다음 링크를 참조하세요. [ASP.NET Core에서 여러 환경 사용](
https://docs.microsoft.com/aspnet/core/fundamentals/environments)

다음 코드는 서비스의 엔드포인트에 대한 요청이 사용자 지정 키로 서명된 전달자 토큰을 사용하여 인증되도록 합니다.

```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            if (_env.IsDevelopment())
            {
                services.AddAuthentication(options =>
                {
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                })
                    .AddJwtBearer(options =>
                    {
                        options.TokenValidationParameters =
                            new TokenValidationParameters
                            {
                                ValidateIssuer = false,
                                ValidateAudience = false,
                                ValidateLifetime = false,
                                ValidateIssuerSigningKey = false,
                                ValidIssuer = "Microsoft.Security.Bearer",
                                ValidAudience = "Microsoft.Security.Bearer",
                                IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"))
                            };
                    });
            }
        ...
```

유효한 전달자 토큰을 받으려면 토큰 컨트롤러에 GET 요청을 보냅니다. _GenerateJSONWebToken_ 메서드는 개발을 위해 구성된 매개 변수와 일치하는 토큰을 만듭니다.

```csharp
        private string GenerateJSONWebToken()
        {
            // Create token key
            SymmetricSecurityKey securityKey =
                new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"));
            SigningCredentials credentials =
                new SigningCredentials(securityKey, SecurityAlgorithms.HmacSha256);

            // Set token expiration
            DateTime startTime = DateTime.UtcNow;
            DateTime expiryTime = startTime.AddMinutes(120);

            // Generate the token
            JwtSecurityToken token =
                new JwtSecurityToken(
                    "Microsoft.Security.Bearer",
                    "Microsoft.Security.Bearer",
                    null,
                    notBefore: startTime,
                    expires: expiryTime,
                    signingCredentials: credentials);

            string result = new JwtSecurityTokenHandler().WriteToken(token);
            return result;
        }
```

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>사용자의 프로비저닝 및 프로비저닝 해제 처리

***예제 1. 일치하는 사용자에 대해 서비스 쿼리***

Azure Active Directory는 `externalId` AZURE AD에서 사용자의 mailNickname 특성 값과 일치 하는 특성 값을 가진 사용자에 대해 서비스를 쿼리 합니다. 쿼리는 이 예제처럼 HTTP(Hypertext Transfer Protocol) 요청으로 표현되며, 여기서 jyoung은 Azure Active Directory에서 사용자의 mailNickname 샘플입니다.

>[!NOTE]
> 이것은 예로 든 것일 뿐입니다. 모든 사용자에게 mailNickname 특성이 있는 것은 아니며 사용자가 가진 값이 디렉터리에서 고유하지 않을 수도 있습니다. 또한 일치에 사용 되는 특성 (이 경우 `externalId` )은 [Azure AD 특성 매핑에서](customize-application-attributes.md)구성할 수 있습니다.

```
GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
 Authorization: Bearer ...
```

샘플 코드에서 요청은 서비스 공급자의 QueryAsync 메서드에 대한 호출로 변환됩니다. 해당 메서드의 서명은 다음과 같습니다. 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource is defined in 
 // Microsoft.SCIM.Schemas.  
 // Microsoft.SCIM.IQueryParameters is defined in 
 // Microsoft.SCIM.Protocol.  

 Task<Resource[]> QueryAsync(IRequest<IQueryParameters> request);
```

예제 쿼리에서 특성에 대해 지정 된 값을 가진 사용자에 대해 `externalId` QueryAsync 메서드에 전달 된 인수의 값은 다음과 같습니다.

* parameters.AlternateFilters.Count: 1
* parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
* parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"

***예제 2. 사용자 프로비저닝***

사용자의 mailNickname 특성 값과 일치 하는 특성 값을 가진 사용자에 대 한 웹 서비스에 대 한 쿼리에 대 한 응답이 사용자 `externalId` 를 반환 하지 않는 경우 Azure Active Directory는 Azure Active Directory에서 해당 서비스에 해당 하는 사용자를 프로 비전 하도록 요청 합니다.  다음은 그러한 요청의 예제입니다. 

```
 POST https://.../scim/Users HTTP/1.1
 Authorization: Bearer ...
 Content-type: application/scim+json
 {
   "schemas":
   [
     "urn:ietf:params:scim:schemas:core:2.0:User",
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
   "externalId":"jyoung",
   "userName":"jyoung",
   "active":true,
   "addresses":null,
   "displayName":"Joy Young",
   "emails": [
     {
       "type":"work",
       "value":"jyoung@Contoso.com",
       "primary":true}],
   "meta": {
     "resourceType":"User"},
    "name":{
     "familyName":"Young",
     "givenName":"Joy"},
   "phoneNumbers":null,
   "preferredLanguage":null,
   "title":null,
   "department":null,
   "manager":null}
```

샘플 코드에서 요청은 서비스 공급자의 CreateAsync 메서드에 대한 호출로 변환됩니다. 해당 메서드의 서명은 다음과 같습니다. 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource is defined in 
 // Microsoft.SCIM.Schemas.  

 Task<Resource> CreateAsync(IRequest<Resource> request);
```

사용자 프로비저닝 요청에서 리소스 인수의 값은 Microsoft.SCIM.Schemas 라이브러리에 정의된 Microsoft.SCIM.Core2EnterpriseUser 클래스의 인스턴스입니다.  사용자를 프로비저닝하기 위한 요청이 성공하는 경우 이 메서드의 구현은 Identifier 특성 값이 새로 프로비저닝된 사용자의 고유 식별자로 설정된 Microsoft.SCIM.Core2EnterpriseUser 클래스의 인스턴스를 반환해야 합니다.  

***예제 3. 사용자의 현재 상태 쿼리*** 

SCIM에 의해 제어되는 ID 저장소에 있는 것으로 알려진 사용자를 업데이트하기 위해 Azure Active Directory는 다음과 같은 요청으로 서비스에서 해당 사용자의 현재 상태를 요청합니다. 

```
 GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
 Authorization: Bearer ...
```

샘플 코드에서 요청은 서비스 공급자의 RetrieveAsync 메서드에 대한 호출로 변환됩니다. 해당 메서드의 서명은 다음과 같습니다. 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource and 
 // Microsoft.SCIM.IResourceRetrievalParameters 
 // are defined in Microsoft.SCIM.Schemas 

 Task<Resource> RetrieveAsync(IRequest<IResourceRetrievalParameters> request);
```

사용자의 현재 상태를 검색하는 요청 예제에서 매개 변수 인수 값으로 제공되는 개체의 속성 값은 다음과 같습니다. 
  
* 식별자: "54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

***예제 4. 업데이트할 참조 특성의 값 쿼리*** 

참조 특성을 업데이트해야 하는 경우 Azure Active Directory는 서비스를 쿼리하여 서비스에 의해 제어되는 ID 저장소에 있는 참조 특성의 현재 값이 Azure Active Directory의 해당 특성 값과 이미 일치하는지를 확인합니다. 사용자의 경우 현재 값이 이 방식으로 쿼리된 유일한 특성은 관리자 특성입니다. 사용자 개체의 관리자 특성 값에 현재 특정 값이 있는지 여부를 결정하는 요청의 예는 다음과 같습니다. 샘플 코드에서 요청은 서비스 공급자의 QueryAsync 메서드에 대한 호출로 변환됩니다. 매개 변수 인수의 값으로 제공되는 개체의 속성 값은 다음과 같습니다. 
  
* parameters.AlternateFilters.Count: 2
* parameters.AlternateFilters.ElementAt(x).AttributePath: "ID"
* parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
* parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(y).ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
* parameters.RequestedAttributePaths.ElementAt(0): "ID"
* parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

여기에서 필터 쿼리 매개 변수 식의 순서에 따라 인덱스 x의 값은 0이고 인덱스 y의 값은 1일 수 있습니다. 또는 x 값이 1이고 y 값이 0일 수 있습니다.   

***예제 5. 사용자를 업데이트 하기 위해 Azure AD에서 SCIM 서비스로 요청*** 

다음은 Azure Active Directory에서 SCIM 서비스로 사용자를 업데이트하는 요청의 예입니다. 

```
  PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
  Content-type: application/scim+json
  {
    "schemas": 
    [
      "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations":
    [
      {
        "op":"Add",
        "path":"manager",
        "value":
          [
            {
              "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
              "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
```

샘플 코드에서 요청은 서비스 공급자의 UpdateAsync 메서드에 대한 호출로 변환됩니다. 해당 메서드의 서명은 다음과 같습니다. 

```csharp
 // System.Threading.Tasks.Tasks and 
 // System.Collections.Generic.IReadOnlyCollection<T>  // are defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in
 // Microsoft.SCIM.Service.
 // Microsoft.SCIM.IPatch, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task UpdateAsync(IRequest<IPatch> request);
```

사용자를 업데이트하는 요청의 예에서 패치 인수의 값으로 제공되는 개체는 다음과 같은 속성 값이 적용됩니다. 
  
* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier:  "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
* (PatchRequest as PatchRequest2).Operations.Count: 1
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).OperationName: OperationName.Add
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Path.AttributePath: "manager"
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.Count: 1
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Reference: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Value: 2819c223-7f76-453a-919d-413861904646

***예제 6. 사용자 프로비저닝 해제***

SCIM 서비스에 의해 제어되는 ID 저장소에서 사용자의 프로비저닝을 해제하기 위해 Azure AD에서 다음과 같은 요청을 보냅니다.

```
  DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
```

샘플 코드에서 요청은 서비스 공급자의 DeleteAsync 메서드에 대한 호출로 변환됩니다. 해당 메서드의 서명은 다음과 같습니다. 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.IResourceIdentifier, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task DeleteAsync(IRequest<IResourceIdentifier> request);
```

사용자의 프로비저닝을 해제하는 요청의 예에서 resourceIdentifier 인수의 값으로 제공되는 개체는 다음과 같은 속성 값이 적용됩니다. 

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client"></a>4단계: SCIM 엔드포인트를 Azure AD SCIM 클라이언트와 통합

[SCIM 2.0 프로토콜](https://tools.ietf.org/html/rfc7644)의 특정 프로필을 구현하는 애플리케이션에 할당된 사용자 및 그룹을 자동으로 프로비저닝하도록 Azure AD를 구성할 수 있습니다. 프로필에 대한 자세한 내용은 [2단계: Azure AD SCIM 구현 이해](#step-2-understand-the-azure-ad-scim-implementation)에 설명되어 있습니다.

애플리케이션 공급자 또는 애플리케이션 공급자의 설명서를 통해 이러한 요구 사항과의 호환성을 확인합니다.

> [!IMPORTANT]
> Azure AD SCIM 구현은 Azure AD 사용자 프로비저닝 서비스 위에 빌드되며, Azure AD와 대상 애플리케이션 간에 사용자를 지속적으로 유지하고 매우 구체적인 표준 작업을 구현하도록 디자인되었습니다. Azure AD SCIM 클라이언트의 동작을 이해하려면 해당 동작을 이해해야 합니다. 자세한 내용은 [프로비저닝 주기 섹션을 참조하세요. [프로비저닝 작동 방식](how-provisioning-works.md)의 초기 및 증분](how-provisioning-works.md#provisioning-cycles-initial-and-incremental)

### <a name="getting-started"></a>시작

Azure AD 애플리케이션 갤러리에 있는 "비-갤러리 애플리케이션" 기능을 사용하여 이 문서에서 설명한 SCIM 프로필을 지원하는 애플리케이션을 Azure Active Directory에 연결할 수 있습니다. 일단 연결되면 Azure AD는 할당된 사용자 및 그룹에 대해 애플리케이션의 SCIM 엔드포인트를 쿼리하고, 할당 세부 정보에 따라 이러한 사용자 및 그룹을 만들거나 수정하는 동기화 프로세스를 40분마다 실행합니다.

**SCIM을 지원하는 애플리케이션을 연결하려면:**

1. [Azure Active Directory 포털](https://aad.portal.azure.com)에 로그인합니다. [개발자 프로그램](https://developer.microsoft.com/office/dev-program)에 가입하면 P2 라이선스가 포함된 Azure Active Directory 평가판에 액세스할 수 있습니다.
2. 왼쪽 창에서 **엔터프라이즈 애플리케이션**을 선택합니다. 갤러리에서 추가된 앱을 포함하여 구성된 모든 앱 목록이 표시됩니다.
3. **+ 새 애플리케이션** > **모든** > **비갤러리 애플리케이션**을 선택합니다.
4. 애플리케이션 이름을 입력하고 **추가**를 선택하여 앱 개체를 만듭니다. 새 앱이 엔터프라이즈 애플리케이션 목록에 추가되고 해당 앱 관리 화면이 열립니다.

   ![Azure AD 애플리케이션 갤러리를 보여 주는 스크린샷](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)<br/>
   ‘Azure AD 애플리케이션 갤러리’

5. 앱 관리 화면의 왼쪽 패널에서 **프로비저닝**을 선택합니다.
6. **프로비전 모드** 메뉴에서 **자동**을 선택합니다.

   ![예: Azure Portal의 앱 프로비저닝 페이지](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   ‘Azure Portal에서 프로비저닝 구성’

7. **테넌트 URL** 필드에 애플리케이션의 SCIM 엔드포인트 URL을 입력합니다. 예: `https://api.contoso.com/scim/`
8. SCIM 엔드포인트에 Azure AD가 아닌 다른 발급자의 OAuth 전달자 토큰이 필요한 경우 필요한 OAuth 전달자 토큰을 **비밀 토큰** 필드(선택 사항)에 복사합니다. 이 필드를 비워 두면 Azure AD에 각 요청에 따라 Azure AD에서 발급한 OAuth 전달자 토큰이 포함됩니다. ID 공급자로 Azure AD를 사용하는 앱은 Azure AD에서 발급한 토큰의 유효성을 검사할 수 있습니다. 
   > [!NOTE]
   > 이 필드를 비워두고 Azure AD에서 생성한 토큰을 사용하는 것은 권장되지 ***않습니다***. 이 옵션은 주로 테스트 목적으로 사용할 수 있습니다.
9. **연결 테스트**를 선택하여 Azure Active Directory에서 SCIM 엔드포인트에 연결을 시도합니다. 시도가 실패하면 오류 정보가 표시됩니다.  

    > [!NOTE]
    > **테스트 연결**은 Azure AD 구성에서 선택된 일치하는 속성으로 임의 GUID를 사용하여 존재하지 않는 사용자의 SCIM 엔드포인트를 쿼리합니다. 예상되는 올바른 응답은 SCIM ListResponse 메시지가 비어 있는 HTTP 200 OK입니다.

10. 애플리케이션에 연결 시도가 성공하면 **저장**을 선택하여 관리자 자격 증명을 저장합니다.
11. **매핑** 섹션에는 선택 가능한 [특성 매핑](customize-application-attributes.md) 집합이 두 개 있는데, 하나는 사용자 개체용이고 다른 하나는 그룹 개체용입니다. 각 특성 매핑을 선택하여 Azure Active Directory에서 앱으로 동기화되는 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 앱의 사용자와 그룹을 일치시키는 데 사용됩니다. 변경 내용을 커밋하려면 **저장**을 선택합니다.

    > [!NOTE]
    > 필요에 따라 "그룹" 매핑을 사용하지 않도록 설정하여 그룹 개체의 동기화를 비활성화할 수 있습니다.

12. **설정** 아래의 **범위** 필드는 동기화되는 사용자 및 그룹을 정의합니다. **할당된 사용자 및 그룹만 동기화**(권장)를 선택하면 **사용자 및 그룹** 탭에서 할당된 사용자 및 그룹만 동기화됩니다.
13. 구성이 완료되면 **프로비저닝 상태**를 **켜기**로 설정합니다.
14. **저장**을 선택하여 Azure AD 프로비저닝 서비스를 시작합니다.
15. 할당된 사용자 및 그룹만 동기화하는 경우(권장) **사용자 및 그룹** 탭을 선택하고 동기화하려는 사용자 또는 그룹을 할당합니다.

초기 추기가 시작되면 왼쪽 패널의 **프로비저닝 로그** 탭을 선택하여 진행 상황을 모니터링할 수 있습니다. 이 탭에는 앱의 프로비저닝 서비스에서 수행하는 모든 작업이 표시됩니다. Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](check-status-user-account-provisioning.md)를 참조하세요.

> [!NOTE]
> 초기 주기는 서비스가 실행되는 동안 약 40분마다 발생하는 후속 동기화보다 더 오래 걸립니다.

## <a name="step-5-publish-your-application-to-the-azure-ad-application-gallery"></a>5단계: Azure AD 애플리케이션 갤러리에 애플리케이션 게시

둘 이상의 테넌트가 사용할 애플리케이션을 빌드하는 경우 Azure AD 애플리케이션 갤러리에서 사용하도록 할 수 있습니다. 그러면 조직은 쉽게 애플리케이션을 찾고 프로비저닝을 구성할 수 있습니다. Azure AD 갤러리에 앱을 게시하고 다른 사용자가 프로비저닝을 쉽게 사용할 수 있도록 합니다. [여기](../azuread-dev/howto-app-gallery-listing.md)서 단계를 확인하세요. Microsoft는 사용자와 협력하여 애플리케이션을 갤러리에 통합하고, 엔드포인트를 테스트하며, 고객이 사용할 수 있도록 온보딩 [설명서](../saas-apps/tutorial-list.md)를 제공합니다. 

### <a name="gallery-onboarding-checklist"></a>갤러리 온보딩 검사 목록
아래의 검사 목록에 따라 애플리케이션이 빠르게 온보드되고 고객이 원활한 배포 환경을 갖추도록 하세요. 갤러리에 온보딩하면 해당 정보가 수집됩니다. 
> [!div class="checklist"]
> * [SCIM 2.0 ](#step-2-understand-the-azure-ad-scim-implementation) 사용자 및 그룹 엔드포인트 지원(하나만 필요하지만 둘 다 권장됨)
> * 테넌트당 25개 이상의 요청 지원(필수)
> * 고객이 갤러리 온보딩을 게시할 수 있도록 엔지니어링 및 지원 연락처 설정(필수)
> * 3 애플리케이션에 대해 만료되지 않은 테스트 자격 증명(필수)
> * 아래 설명된 대로 OAuth 인증 코드 부여 또는 수명이 긴 토큰 지원(필수)
> * 고객이 갤러리 온보딩을 게시할 수 있도록 엔지니어링 및 지원 연락처 설정(필수)
> * 단일 PATCH로 여러 그룹 멤버십 업데이트 지원(권장) 
> * SCIM 엔드포인트를 공개적으로 문서화(권장) 
> * [지원 스키마 검색](https://tools.ietf.org/html/rfc7643#section-6)(권장)


### <a name="authorization-for-provisioning-connectors-in-the-application-gallery"></a>애플리케이션 갤러리에서 커넥터 프로비저닝을 위한 권한 부여
SCIM 사양은 인증 및 권한 부여를 위한 SCIM별 스키마를 정의하지 않습니다. 기존 산업 표준의 사용을 기반으로 합니다. Azure AD 프로비저닝 클라이언트는 갤러리의 애플리케이션에 대해 두 가지 권한 부여 방법을 지원합니다. 

|권한 부여 방법|장점|단점|지원|
|--|--|--|--|
|사용자 이름 및 비밀번호(Azure AD에서 권장되지 않거나 지원되지 않음)|쉬운 구현|안전하지 않음 - [암호는 중요하지 않습니다.](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|갤러리 앱에 대해 사례별로 지원됩니다. 비갤러리 앱에는 지원되지 않습니다.|
|수명이 긴 전달자 토큰|수명이 긴 토큰에는 사용자가 없어도 됩니다. 프로비저닝을 설정할 때 관리자가 쉽게 사용할 수 있습니다.|수명이 간 토큰은 메일처럼 안전하지 않은 방법을 사용하지 않으면 관리자와 공유하기 어려울 수 있습니다. |갤러리 및 비갤러리 앱에 지원됩니다. |
|OAuth 인증 코드 부여|액세스 토큰은 비밀번호보다 수명이 훨씬 짧으며 수명이 긴 전달자 토큰에 없는 자동화된 새로 고침 메커니즘이 있습니다.  책임 수준을 추가하는 초기 권한 부여 중에 실제 사용자가 있어야 합니다. |사용자가 있어야 합니다. 사용자가 조직을 떠나면 토큰이 유효하지 않으므로 권한 부여를 다시 완료해야 합니다.|갤러리 앱에 대해서는 지원 되지만 비 갤러리 앱에 대해서는 지원 됩니다. 비 갤러리에 대 한 지원은 백로그에 있습니다.|
|OAuth 클라이언트 자격 증명 부여|액세스 토큰은 비밀번호보다 수명이 훨씬 짧으며 수명이 긴 전달자 토큰에 없는 자동화된 새로 고침 메커니즘이 있습니다. 인증 코드 부여 및 클라이언트 자격 증명 부여는 모두 동일한 유형의 액세스 토큰을 만들기 때문에 이러한 메서드 간에 이동하는 것은 API에 인식됩니다.  프로비저닝은 완전히 자동화할 수 있으며 사용자 개입 없이도 새 토큰을 자동으로 요청할 수 있습니다. ||갤러리 앱 및 비갤러리 앱에는 지원되지 않습니다. 지원은 백로그에 있습니다.|

> [!NOTE]
> Azure AD 프로비저닝 구성 사용자 지정 앱 UI에서 토큰 필드를 비워 두지 않는 것이 좋습니다. 생성된 토큰은 주로 테스트 목적으로 사용할 수 있습니다.

**OAuth 인증 코드 부여 흐름:** 프로비저닝 서비스는 [인증 코드 부여](https://tools.ietf.org/html/rfc6749#page-24)를 지원합니다. 갤러리에 앱 게시 요청을 제출하면 Microsoft 팀에서 다음 정보를 수집하기 위해 사용자와 협력합니다.
*  권한 부여 URL 사용자 에이전트 리디렉션을 통해 리소스 소유자로부터 권한을 얻기 위한 클라이언트의 URL입니다. 액세스 권한을 부여하기 위해 사용자가 URL로 리디렉션됩니다. 현재는 URL을 테넌트별로 구성할 수 없습니다.
*  토큰 교환 URL: 클라이언트가 일반적으로 클라이언트 인증을 사용하여 액세스 토큰에 대한 인증 부여를 교환하기 위한 URL입니다. 현재는 URL을 테넌트별로 구성할 수 없습니다.
*  클라이언트 ID: 권한 부여 서버는 등록된 클라이언트에 클라이언트 식별자를 제공합니다. 클라이언트 식별자는 클라이언트가 제공한 등록 정보를 나타내는 고유한 문자열입니다.  클라이언트 식별자는 암호가 아닙니다. 리소스 소유자에게 표시되며 클라이언트 인증을 위해 단독으로 사용해서는 **안 됩니다**.  
*  클라이언트 암호: 클라이언트 암호는 권한 부여 서버에서 생성하는 암호입니다. 암호는 권한 부여 서버에만 알려진 고유한 값이어야 합니다. 

OAuth v1은 클라이언트 암호가 노출되어 지원되지 않습니다. OAuth v2는 지원됩니다.  

모범 사례(권장되지만 필수는 아님):
* 여러 리디렉션 URL을 지원합니다. 관리자는 “portal.azure.com” 및 “aad.portal.azure.com”에서 모두 프로비저닝을 구성할 수 있습니다. 여러 리디렉션 URL을 지원하기 때문에 사용자가 두 포털에서 액세스 권한을 부여할 수 있습니다.
* 가동 중지 시간 없이 원활한 암호 갱신을 보장하기 위해 여러 암호를 지원합니다. 

**수명이 긴 OAuth 전달자 토큰:** 응용 프로그램에서 OAuth 인증 코드 부여 흐름을 지원 하지 않는 경우 관리자가 프로 비전 통합을 설정 하는 데 사용할 수 있는 수명이 긴 OAuth 전달자 토큰을 생성할 수도 있습니다. 토큰은 영구적이어야 합니다. 그렇지 않은 경우 토큰이 만료되면 프로비저닝 작업이 [격리](application-provisioning-quarantine-status.md)됩니다. 이 토큰의 크기는 1KB 미만이어야 합니다.  

추가 인증 및 권한 부여 방법에 대한 자세한 내용은 [UserVoice](https://aka.ms/appprovisioningfeaturerequest)에 알려주세요.

### <a name="gallery-go-to-market-launch-check-list"></a>갤러리 시장 진출 시작 검사 목록
공동 통합에 대한 인식과 수요를 높이려면 기존 설명서를 업데이트하고 마케팅 채널에서 통합을 확대하는 것이 좋습니다.  아래는 시작을 지원하기 위해 완료하면 좋은 검사 목록 활동입니다.

* **영업 및 고객 지원 준비** 영업 및 지원 팀이 통합 기능을 인지하고 말할 수 있는지 확인합니다. 영업 및 지원 팀에 간략하게 설명하고 FAQ를 제공 하며 영업 자료에 통합을 포함합니다. 
* **블로그 게시물 및/또는 보도 자료** 공동 통합, 이점 및 시작 방법을 설명하는 블로그 게시물 또는 보도 자료를 만듭니다. [예: Imprivata 및 Azure Active Directory 보도 자료](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
* **소셜 미디어** Twitter, Facebook 또는 LinkedIn과 같은 소셜 미디어를 활용하여 고객과의 통합을 촉진하세요. 게시물을 리트윗할 수 있도록 @AzureAD를 포함해야 합니다. [예: Imprivata Twitter 게시물](https://twitter.com/azuread/status/1123964502909779968)
* **마케팅 웹 사이트** 공동 통합의 가용성을 포함하도록 마케팅 페이지(예: 통합 페이지, 파트너 페이지, 가격 책정 페이지 등)를 만들거나 업데이트하세요. [예: Pingboard 통합 페이지](https://pingboard.com/org-chart-for), [Smartsheet 통합 페이지](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad), [Monday.com 가격 책정 페이지](https://monday.com/pricing/) 
* **기술 문서** 고객이 시작하는 방법에 대한 도움말 센터 문서 또는 기술 문서를 생성합니다. [예: Envoy + Microsoft Azure Active Directory 통합](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
* **고객 커뮤니케이션** 고객 커뮤니케이션(월간 뉴스 레터, 메일 캠페인, 제품 릴리스 노트)을 통해 고객에게 새로운 통합을 알리세요. 

## <a name="related-articles"></a>관련 문서

* [SaaS 앱에 사용자 프로비저닝 및 프로비저닝 해제 자동화](user-provisioning.md)
* [사용자 프로비전을 위한 사용자 지정 특성 매핑](customize-application-attributes.md)
* [특성 매핑에 대한 식 작성](functions-for-customizing-application-data.md)
* [사용자 프로비저닝에 대한 필터 범위 지정](define-conditional-rules-for-provisioning-user-accounts.md)
* [계정 프로비전 알림](user-provisioning.md)
* [SaaS App을 통합하는 방법에 대한 자습서 목록](../saas-apps/tutorial-list.md)
