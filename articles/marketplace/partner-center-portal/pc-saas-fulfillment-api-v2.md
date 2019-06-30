---
title: SaaS Fulfillment API v2 | Azure Marketplace
description: 이 문서를 만들고 연결된 처리를 사용 하 여 Azure Marketplace 및 AppSource에 SaaS 제품을 관리 하는 방법을 설명 v2 Api.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 05/23/2019
ms.author: evansma
ms.openlocfilehash: ecee1669c29d7b298741f9e5521de03da6dd7e3b
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331631"
---
# <a name="saas-fulfillment-apis-version-2"></a>SaaS fulfillment Api 버전 2 

이 문서에서는 Azure Marketplace 및 AppSource marketplace SaaS 응용 프로그램을 판매 하는 파트너를 사용 하도록 설정 하는 Api를 자세히 설명 합니다. 이러한 Api는 Azure Marketplace 및 AppSource transactable SaaS에 대 한 요구 사항을 제공 합니다.

## <a name="managing-the-saas-subscription-life-cycle"></a>SaaS 구독 수명 주기 관리

Azure SaaS SaaS 구독 구매의 전체 수명 주기를 관리합니다. 실제 fulfillment 드라이브를 메커니즘으로 처리 Api를 사용 하 여, 계획 및 파트너를 사용 하 여 구독의 삭제로 변경 합니다. 고객 청구는 Microsoft에서 유지 관리 하는 SaaS 구독의 상태를 기반으로 합니다. 다음 다이어그램에서는 상태와 상태 간의 변경 내용의 구동 하는 작업을 보여 줍니다.

![SaaS 구독 수명 주기 상태](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>SaaS 구독 상태

다음 표에서 해당 하는 경우 각각에 대 한 설명 및 시퀀스 다이어그램을 포함 하 여 SaaS 구독에 대 한 프로 비전 상태를 나열 합니다. 

#### <a name="provisioning"></a>프로비전

고객이 구매를 시작, 파트너 URL 매개 변수를 사용 하는 고객이 대화형 웹 페이지에는 권한 부여 코드에서이 정보를 받습니다. 예로 `https://contoso.com/signup?token=..`반면 파트너 센터에서 방문 페이지 URL은 `https://contoso.com/signup`합니다. 권한 부여 코드의 유효성을 검사 하 고 해결 API를 호출 하 여 프로 비전 서비스의 세부 정보에 대 한 교환할 수 있습니다.  SaaS 서비스를 프로 비전 완료 되 면 활성화 호출을 처리 완료 되 고 고객이 대금을 청구할 수는 신호를 보냅니다. 

다음 다이어그램은 프로 비전 시나리오에 대 한 API 호출의 순서를 보여줍니다.  

![SaaS 서비스를 프로 비전에 대 한 API 호출](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>프로비전됨

이 상태는 프로 비전 된 서비스의 정상 상태입니다.

##### <a name="provisioning-for-update"></a>업데이트에 대 한 프로 비전 

이 상태는 기존 서비스에 대 한 업데이트 보류 중임을 나타냅니다. 이러한 업데이트는 고객이 marketplace에서 또는 SaaS 서비스 (direct 고객 트랜잭션)에 시작할 수 있습니다.

##### <a name="provisioning-for-update-when-its-initiated-from-the-marketplace"></a>(Marketplace에서 시작) 하는 경우 업데이트에 대 한 프로 비전

다음 다이어그램은 marketplace에서 업데이트가 시작 되 면 작업 순서를 보여줍니다.

![Marketplace에서 업데이트가 시작 되 면 API 호출](./media/saas-update-api-v2-calls-from-marketplace-a.png)

##### <a name="provisioning-for-update-when-its-initiated-from-the-saas-service"></a>(SaaS 서비스에서 시작) 하는 경우 업데이트에 대 한 프로 비전

다음 다이어그램은 SaaS 서비스에서 업데이트를 시작한 경우 작업을 보여 줍니다. (웹 후크 호출 SaaS 서비스에 의해 시작 된 구독에 대 한 업데이트 대체 됩니다.) 

![SaaS 서비스에서 업데이트를 시작한 경우 API 호출](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>Suspended

이 상태는 고객의 지불 수신 되지 않았습니다 나타냅니다. 정책에 의해 고객 구독을 취소 하기 전에 유예 기간이 제공 됩니다. 구독 중인 경우이 상태: 

- 파트너는 저하 또는 서비스에 대 한 사용자의 액세스를 차단할 수도 있습니다.
- 구독 설정 데이터의 손실 없이 전체 기능을 복원할 수 있는 복구 가능한 상태 유지 되어야 합니다. 
- 유예 기간이 끝날 때 복원 요청을 처리 Api 통해이 구독에 프로 비전 해제 요청을 가져올 수 있어야 합니다. 

#### <a name="unsubscribed"></a>구독 취소 

구독에 대 한 응답 명시적 고객 요청 또는 dues의 사용료에이 상태에 도달 합니다. 파트너 로부터 것 이란 가정 고객의 데이터가 특정 기간 (일)에 대 한 요청 시 복구를 위해 보관 및 삭제 됩니다. 


## <a name="api-reference"></a>API 참조

이 섹션에서는 설명 SaaS *구독 API* 하 고 *Operations API*합니다.  값을 `api-version` 버전 2에 대 한 매개 변수 Api는 `2018-08-31`합니다.  


### <a name="parameter-and-entity-definitions"></a>매개 변수 및 엔터티 정의

다음 표에서 일반적인 매개 변수 및 fulfillment Api에서 사용 하는 엔터티에 대 한 정의 나열 합니다.

|     엔터티/매개 변수     |     정의                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | SaaS 리소스에 대 한 GUID 식별자입니다.  |
| `name`                   | 이 리소스에 대해 고객이 제공한 이름입니다. |
| `publisherId`            | 각 게시자에 대 한 고유한 문자열 식별자 (예: "contoso"). |
| `offerId`                | 각 제품에 대 한 고유한 문자열 식별자 (예: "offer1").  |
| `planId`                 | 각 계획/SKU에 대 한 고유한 문자열 식별자 (예: "silver"). |
| `operationId`            | 특정 작업에 대 한 GUID 식별자입니다.  |
|  `action`                | 하거나 리소스에서 수행 되는 작업 `subscribe`, `unsubscribe`를 `suspend`를 `reinstate`, 또는 `changePlan`를 `changeQuantity`, `transfer`합니다.  |
|   |   |

전역적으로 고유 식별자 ([Guid](https://en.wikipedia.org/wiki/Universally_unique_identifier))는 일반적으로 자동으로 생성 된 128 비트 (32-16 진수) 숫자입니다. 

#### <a name="resolve-a-subscription"></a>구독 확인 

해결 끝점 marketplace 토큰을 영구 리소스 ID를 확인 하려면 게시자를 사용 하도록 설정 리소스 ID에 SaaS 구독에 대 한 고유 식별자입니다. 사용자, 파트너 웹 사이트에 리디렉션될 때 URL 쿼리 매개 변수에서 토큰을 포함 합니다. 파트너는이 토큰을 사용 하 고 문제를 해결 하는 요청을 수행 해야 합니다. 응답에는 고유한 SaaS 구독 ID, 이름, 제품 ID 및 리소스에 대 한 계획 포함 되어 있습니다. 이 토큰은 1 시간에만 유효 합니다. 

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>올리기<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  이 요청에 사용할 작업의 버전입니다.  |

*요청 헤더:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  콘텐츠 형식      | `application/json` |
|  x-ms-requestid    |  클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값이 제공 되지 않는 경우 하나 생성 되며 응답 헤더에 제공 합니다. |
|  x-ms-correlationid |  클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수 서버 쪽에서 이벤트를 사용 하 여 클라이언트 작업에서 모든 이벤트를 상호 연결 합니다. 이 값이 제공 되지 않는 경우 하나 생성 되며 응답 헤더에 제공 합니다.  |
|  authorization     |  [JSON 웹 토큰 (JWT) 전달자 토큰을 가져옵니다](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)합니다. |
|  x-ms-marketplace-token  |  Azure에서 SaaS 파트너의 웹 사이트에 사용자가 리디렉션될 경우 URL에 토큰 쿼리 매개 변수 (예: `https://contoso.com/signup?token=..`). *참고:* URL을 사용 하기 전에 브라우저에서 토큰 값을 디코딩합니다.  |

*응답 코드:*

코드: 200<br>
SaaS 구독으로 불투명 토큰을 확인합니다. 응답 본문:
 

```json
{
    "id": "<guid>",  
    "subscriptionName": "Contoso Cloud Solution",
    "offerId": "offer1",
    "planId": "silver",
    "quantity": "20" 
}
```

코드: 400<br>
잘못된 요청입니다. x-ms-marketplace-토큰이 없거나 형식이 잘못 되었거나 만료 된 경우

코드: 403<br>
권한이 없습니다. 인증 토큰이 제공 되지 않았습니다 유효 하지 않은 또는 요청이 현재 게시자에 속하지 않는 인수에 액세스 하려고 합니다.

코드: 404<br>
찾을 수 없음

코드: 500<br>
내부 서버 오류.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

### <a name="subscription-api"></a>구독 API

API 구독에는 다음 HTTPS 작업을 지원합니다. **가져올**, **Post**를 **패치**, 및 **삭제**합니다.


#### <a name="list-subscriptions"></a>구독 나열

게시자에 대 한 모든 SaaS 구독을 나열합니다.

##### <a name="getbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>가져오기<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  이 요청에 사용할 작업의 버전입니다.  |

*요청 헤더:*

|                    |                   |
|  ---------------   |  ---------------  |
| 콘텐츠 형식       |  `application/json`  |
| x-ms-requestid     |  클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값이 제공 되지 않는 경우 하나 생성 되며 응답 헤더에 제공 합니다. |
| x-ms-correlationid |  클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수 서버 쪽에서 이벤트를 사용 하 여 클라이언트 작업에서 모든 이벤트를 상호 연결 합니다. 이 값이 제공 되지 않는 경우 하나 생성 되며 응답 헤더에 제공 합니다.  |
| authorization      |  [JSON 웹 토큰 (JWT) 전달자 토큰을 가져옵니다](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)합니다.  |

*응답 코드:*

코드: 200 <br/>
게시자 및 게시자의 모든 제품의 경우 인증 토큰을 기반으로 해당 구독을 가져옵니다.
응답 페이로드:<br>

```json
{
  [
      {
          "id": "<guid>",
          "name": "Contoso Cloud Solution",
          "publisherId": "contoso",
          "offerId": "offer1",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```

연속 토큰 추가 "페이지"를 검색 하는 계획의 경우에 표시 됩니다. 

코드: 403 <br>
권한이 없습니다. 인증 토큰이 제공 되지 않았습니다 유효 하지 않은 또는 요청이 현재 게시자에 속하지 않는 인수에 액세스 하려고 합니다. 

코드: 500<br>
내부 서버 오류.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="get-subscription"></a>구독 가져오기

지정 된 SaaS 구독을 가져옵니다. 이 호출을 사용 하 여 계획 정보 및 라이선스 정보를 제공 합니다.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>가져오기<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   해결 API를 통해 토큰을 해결 한 후에 얻은 SaaS 구독의 고유 식별자입니다.   |
|  ApiVersion        |   이 요청에 사용할 작업의 버전입니다.   |

*요청 헤더:*

|                    |                   |
|  ---------------   |  ---------------  |
|  콘텐츠 형식      |  `application/json`  |
|  x-ms-requestid    |  클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값이 제공 되지 않는 경우 하나 생성 되며 응답 헤더에 제공 합니다. |
|  x-ms-correlationid |  클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수 서버 쪽에서 이벤트를 사용 하 여 클라이언트 작업에서 모든 이벤트를 상호 연결 합니다. 이 값이 제공 되지 않는 경우 하나 생성 되며 응답 헤더에 제공 합니다.  |
|  authorization     |  [JSON 웹 토큰 (JWT) 전달자 토큰을 가져옵니다](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)합니다. |

*응답 코드:*

코드: 200<br>
식별자에서 SaaS 구독을 가져옵니다. 응답 페이로드:<br>

```json
Response Body:
{ 
        "id":"",
        "name":"Contoso Cloud Solution",
        "publisherId": "contoso",
        "offerId": "offer1",
        "planId": "silver",
        "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
        "allowedCustomerOperations": ["Read"], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
        "sessionMode": "None", // Dry Run indicates all transactions run as Test-Mode in the commerce stack
        "status": "Subscribed", // Indicates the status of the operation.
}
```

코드: 403<br>
권한이 없습니다. 인증 토큰이 제공 되지 않았습니다 유효 하지 않은 또는 요청이 현재 게시자에 속하지 않는 인수에 액세스 하려고 합니다.

코드: 404<br>
찾을 수 없음<br> 

코드: 500<br>
내부 서버 오류.<br>

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }  
```

#### <a name="list-available-plans"></a>사용 가능한 계획 목록

현재 게시자에 대 한 개인 또는 공용 오퍼 있는지 확인 하려면이 호출을 사용 합니다.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>가져오기<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   이 요청에 사용할 작업의 버전입니다.  |

*요청 헤더:*

|                    |                   |
|  ---------------   |  ---------------  |
|   콘텐츠 형식     |  `application/json` |
|   x-ms-requestid   |   클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값이 제공 되지 않는 경우 하나 생성 되며 응답 헤더에 제공 합니다. |
|  x-ms-correlationid  | 클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수 서버 쪽에서 이벤트를 사용 하 여 클라이언트 작업에서 모든 이벤트를 상호 연결 합니다. 이 값이 제공 되지 않는 경우 하나 생성 되며 응답 헤더에 제공 합니다. |
|  authorization     |  [JSON 웹 토큰 (JWT) 전달자 토큰을 가져옵니다](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)합니다. |

*응답 코드:*

코드: 200<br>
고객에 대 한 사용 가능한 계획의 목록을 가져옵니다. 응답 본문:

```json
{
    "plans": [{
        "planId": "Platinum001",
        "displayName": "Private platinum plan for Contoso",
        "isPrivate": true
    }]
}
```

코드: 404<br>
찾을 수 없음<br> 

코드: 403<br>
권한이 없습니다. 인증 토큰이 제공 되지 않았습니다 유효 하지 않은 또는 요청이 현재 게시자에 속하지 않는 인수에 액세스 하려고 합니다. <br> 

코드: 500<br>
내부 서버 오류.<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
    } 
```

#### <a name="activate-a-subscription"></a>구독 활성화

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>올리기<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  이 요청에 사용할 작업의 버전입니다.  |
| subscriptionId     | 해결 API를 사용 하 여 토큰을 확인 한 후에 얻은 SaaS 구독의 고유 식별자입니다.  |

*요청 헤더:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  콘텐츠 형식      | `application/json`  |
|  x-ms-requestid    | 클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값이 제공 되지 않는 경우 하나 생성 되며 응답 헤더에 제공 합니다.  |
|  x-ms-correlationid  | 클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 문자열에서 서버 쪽 이벤트를 사용 하 여 클라이언트 작업의 모든 이벤트를 상호 연결합니다. 이 값이 제공 되지 않는 경우 하나 생성 되며 응답 헤더에 제공 합니다.  |
|  authorization     |  [JSON 웹 토큰 (JWT) 전달자 토큰을 가져옵니다](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)합니다. |

*요청 페이로드:*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*응답 코드:*

코드: 200<br>
구독을 활성화합니다.<br>

코드: 400<br>
잘못 된 요청: 유효성 검사에 실패 합니다.

코드: 403<br>
권한이 없습니다. 인증 토큰이 제공 되지 않았습니다 유효 하지 않은 또는 요청이 현재 게시자에 속하지 않는 인수에 액세스 하려고 합니다.

코드: 404<br>
찾을 수 없음

코드: 500<br>
내부 서버 오류.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="change-the-plan-on-the-subscription"></a>구독에서 계획 변경

구독 계획을 업데이트 합니다.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>패치<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  이 요청에 사용할 작업의 버전입니다.  |
| subscriptionId     | 해결 API를 사용 하 여 토큰을 확인 한 후에 얻은 SaaS 구독의 고유 식별자입니다.  |

*요청 헤더:*

|                    |                   |
|  ---------------   |  ---------------  |
|  콘텐츠 형식      | `application/json` |
|  x-ms-requestid    |   클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값이 제공 되지 않는 경우 하나 생성 되며 응답 헤더에 제공 합니다.  |
|  x-ms-correlationid  |  클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수 서버 쪽에서 이벤트를 사용 하 여 클라이언트 작업에서 모든 이벤트를 상호 연결 합니다. 이 값이 제공 되지 않는 경우 하나 생성 되며 응답 헤더에 제공 합니다.    |
| authorization      |  [JSON 웹 토큰 (JWT) 전달자 토큰을 가져옵니다](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)합니다.  |

*요청 페이로드:*

```json
Request Body:
{
    "planId": "gold"
}
```

*요청 헤더:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | 작업의 상태를 가져오려면 리소스에 대 한 링크입니다.   |

*응답 코드:*

코드: 202<br>
계획을 변경 하는 요청이 수락 되었습니다. 파트너는 성공 또는 실패를 확인 하려면 작업 위치를 폴링할 해야 합니다. <br>

코드: 400<br>
잘못 된 요청: 유효성 검사에 실패 합니다.

코드: 403<br>
권한이 없습니다. 인증 토큰이 제공 되지 않았습니다 유효 하지 않은 또는 요청이 현재 게시자에 속하지 않는 인수에 액세스 하려고 합니다.

코드: 404<br>
찾을 수 없음

코드: 500<br>
내부 서버 오류.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

>[!Note]
>한 번에 패치가 적용 된, 둘 다가 아닌 계획 또는 수량만 될 수 있습니다. 사용 하 여 구독에서 편집 **업데이트** 에 없는 `allowedCustomerOperations`합니다.

#### <a name="change-the-quantity-on-the-subscription"></a>구독에 수량 변경

구독에 수량을 업데이트 합니다.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>패치 합니다.<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  이 요청에 사용할 작업의 버전입니다.  |
| subscriptionId     | 해결 API를 사용 하 여 토큰을 확인 한 후에 얻은 SaaS 구독의 고유 식별자입니다.  |

*요청 헤더:*

|                    |                   |
|  ---------------   |  ---------------  |
|  콘텐츠 형식      | `application/json` |
|  x-ms-requestid    |   클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값이 제공 되지 않는 경우 하나 생성 되며 응답 헤더에 제공 합니다.  |
|  x-ms-correlationid  |  클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수 서버 쪽에서 이벤트를 사용 하 여 클라이언트 작업에서 모든 이벤트를 상호 연결 합니다. 이 값이 제공 되지 않는 경우 하나 생성 되며 응답 헤더에 제공 합니다.    |
| authorization      |  [JSON 웹 토큰 (JWT) 전달자 토큰을 가져옵니다](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)합니다.  |

*요청 페이로드:*

```json
Request Body:
{
    "quantity": 5
}
```

*요청 헤더:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | 작업의 상태를 가져오려면 리소스에 연결 합니다.   |

*응답 코드:*

코드: 202<br>
수량을 변경 하는 요청이 수락 되었습니다. 파트너는 성공 또는 실패를 확인 하려면 작업 위치를 폴링할 해야 합니다. <br>

코드: 400<br>
잘못 된 요청: 유효성 검사에 실패 합니다.


코드: 403<br>
권한이 없습니다. 인증 토큰이 제공 되지 않았습니다 유효 하지 않은 또는 요청이 현재 게시자에 속하지 않는 인수에 액세스 하려고 합니다.

코드: 404<br>
찾을 수 없음

코드: 500<br>
내부 서버 오류.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

>[!Note]
>한 번에 패치가 적용 된, 둘 다가 아닌 계획 또는 수량만 될 수 있습니다. 사용 하 여 구독에서 편집 **업데이트** 에 없는 `allowedCustomerOperations`합니다.

#### <a name="delete-a-subscription"></a>구독 삭제

구독을 취소 하 고 지정된 된 구독을 삭제 합니다.

##### <a name="deletebr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionid-api-versionapiversion"></a>삭제<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId> ?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  이 요청에 사용할 작업의 버전입니다.  |
| subscriptionId     | 해결 API를 사용 하 여 토큰을 확인 한 후에 얻은 SaaS 구독의 고유 식별자입니다.  |

*요청 헤더:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   콘텐츠 형식     |  `application/json` |
|  x-ms-requestid    |   클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값이 제공 되지 않는 경우 하나 생성 되며 응답 헤더에 제공 합니다.   |
|  x-ms-correlationid  |  클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수 서버 쪽에서 이벤트를 사용 하 여 클라이언트 작업에서 모든 이벤트를 상호 연결 합니다. 이 값이 제공 되지 않는 경우 하나 생성 되며 응답 헤더에 제공 합니다.   |
|  authorization     |  [JSON 웹 토큰 (JWT) 전달자 토큰을 가져옵니다](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)합니다.  |

*응답 코드:*

코드: 202<br>
파트너는 SaaS 구독 취소에 대 한 호출을 시작 합니다.<br>

코드: 400<br>
구독을 삭제 **삭제할** 나타나지 `allowedCustomerOperations`합니다.

코드: 403<br>
권한이 없습니다. 인증 토큰이 제공 되지 않았습니다 유효 하지 않은 또는 요청이 현재 게시자에 속하지 않는 인수에 액세스 하려고 합니다.

코드: 404<br>
찾을 수 없음

코드: 500<br>
내부 서버 오류.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```


### <a name="operations-api"></a>API 작업

작업 API를 다음 패치 및 Get 작업을 지원합니다.

#### <a name="list-outstanding-operations"></a>목록 처리 중인 작업 

현재 게시자에 대 한 처리 중인 작업을 나열합니다. 

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>가져오기<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   이 요청에 사용할 작업의 버전입니다.                |
| subscriptionId     | 해결 API를 사용 하 여 토큰을 확인 한 후에 얻은 SaaS 구독의 고유 식별자입니다.  |

*요청 헤더:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   콘텐츠 형식     |  `application/json` |
|  x-ms-requestid    |  클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값이 제공 되지 않는 경우 하나 생성 되며 응답 헤더에 제공 합니다.  |
|  x-ms-correlationid |  클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수 서버 쪽에서 이벤트를 사용 하 여 클라이언트 작업에서 모든 이벤트를 상호 연결 합니다. 이 값이 제공 되지 않는 경우 하나 생성 되며 응답 헤더에 제공 합니다.  |
|  authorization     |  [JSON 웹 토큰 (JWT) 전달자 토큰을 가져옵니다](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)합니다.  |

*응답 코드:*

코드: 200<br> 보류 중인 구독에서 작업의 목록을 가져옵니다. 응답 페이로드:

```json
[{
    "id": "<guid>",  
    "activityId": "<guid>",
    "subscriptionId": "<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",  
    "status": "NotStarted"  
}]
```


코드: 400<br>
잘못 된 요청: 유효성 검사에 실패 합니다.

코드: 403<br>
권한이 없습니다. 인증 토큰이 제공 되지 않았습니다 유효 하지 않은 또는 요청이 현재 게시자에 속하지 않는 인수에 액세스 하려고 합니다.

코드: 404<br>
찾을 수 없음

코드: 500<br>
내부 서버 오류.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

#### <a name="get-operation-status"></a>작업 상태 가져오기

지정 된 트리거된 비동기 작업의 상태를 추적 하려면 게시자를 사용 하도록 설정 (같은 `subscribe`, `unsubscribe`, `changePlan`, 또는 `changeQuantity`).

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>가져오기<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  이 요청에 사용할 작업의 버전입니다.  |

*요청 헤더:*

|                    |                   |
|  ---------------   |  ---------------  |
|  콘텐츠 형식      |  `application/json`   |
|  x-ms-requestid    |   클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값이 제공 되지 않는 경우 하나 생성 되며 응답 헤더에 제공 합니다.  |
|  x-ms-correlationid |  클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수 서버 쪽에서 이벤트를 사용 하 여 클라이언트 작업에서 모든 이벤트를 상호 연결 합니다. 이 값이 제공 되지 않는 경우 하나 생성 되며 응답 헤더에 제공 합니다.  |
|  authorization     |  [JSON 웹 토큰 (JWT) 전달자 토큰을 가져옵니다](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)합니다.  |

*응답 코드:*<br>

코드: 200<br> SaaS 작업 보류 된를 가져옵니다. 응답 페이로드:

```json
Response body:
{
    "id  ": "<guid>",
    "activityId": "<guid>",
    "subscriptionId":"<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",
    "status": "NotStarted"
}

```

코드: 400<br>
잘못 된 요청: 유효성 검사에 실패 합니다.

코드: 403<br>
권한이 없습니다. 인증 토큰이 제공 되지 않았습니다 유효 하지 않은 또는 요청이 현재 게시자에 속하지 않는 인수에 액세스 하려고 합니다.
 
코드: 404<br>
찾을 수 없음

코드: 500<br> 내부 서버 오류.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```
#### <a name="update-the-status-of-an-operation"></a>작업의 상태를 업데이트 합니다.

제공 된 값을 사용 하 여 성공 여부를 나타내는 작업의 상태를 업데이트 합니다.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>패치<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  이 요청에 사용할 작업의 버전입니다.  |
| subscriptionId     | 해결 API를 사용 하 여 토큰을 확인 한 후에 얻은 SaaS 구독의 고유 식별자입니다.  |
|  operationId       | 완료 중인 작업입니다. |

*요청 헤더:*

|                    |                   |
|  ---------------   |  ---------------  |
|   콘텐츠 형식     | `application/json`   |
|   x-ms-requestid   |   클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값이 제공 되지 않는 경우 하나 생성 되며 응답 헤더에 제공 합니다. |
|  x-ms-correlationid |  클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수 서버 쪽에서 이벤트를 사용 하 여 클라이언트 작업에서 모든 이벤트를 상호 연결 합니다. 이 값이 제공 되지 않는 경우 하나 생성 되며 응답 헤더에 제공 합니다. |
|  authorization     |  [JSON 웹 토큰 (JWT) 전달자 토큰을 가져옵니다](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)합니다.  |

*요청 페이로드:*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*응답 코드:*

코드: 200<br> 파트너 측에 대 한 작업의 완료를 알리기 위해 호출 합니다. 예를 들어이 응답 계획 또는 실제 사용자 수를 변경 하 여 신호를 보낼 수 있습니다.

코드: 400<br>
잘못 된 요청: 유효성 검사에 실패 합니다.

코드: 403<br>
권한이 없습니다. 인증 토큰이 제공 되지 않았습니다 유효 하지 않은 또는 요청이 현재 게시자에 속하지 않는 인수에 액세스 하려고 합니다.

코드: 404<br>
찾을 수 없음

코드: 409<br>
충돌 합니다. 예를 들어 최신 트랜잭션이 이미 충족 합니다.

코드: 500<br> 내부 서버 오류.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="implementing-a-webhook-on-the-saas-service"></a>SaaS 서비스에서 웹 후크를 구현합니다.

게시자는 사용자에 게 사전에 알리는 해당 서비스의 변경 내용을이 SaaS 서비스에서 웹 후크를 구현 해야 합니다. SaaS 서비스는 작업의 유효성을 검사 하 고 웹 후크 알림을 작업을 수행 하기 전에 권한을 부여 하는 API를 호출 해야 합니다.

```json
{
  "id": "<this is a GUID operation id, you can call operations API with this to get status>",
  "activityId": "<this is a Guid correlation id>",
  "subscriptionId": "<Guid to uniquely identify this resource>",
  "publisherId": "<this is the publisher’s name>",
  "offerId": "<this is the offer name>",
  "planId": "<this is the plan id>",
  "quantity": "<the number of seats, will be null if not per-seat saas offer>",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Unsubscribe",
  "status": "NotStarted"  

}
```
여기서 작업은 다음 중 하나일 수 있습니다. 
- `subscribe` (경우 리소스 활성화 된)
- `unsubscribe` (경우 리소스가 삭제 되었습니다)
- `changePlan` (변경 계획 작업이 완료 되 면)
- `changeQuantity` (변경 수량 작업이 완료 되 면)
- `suspend` (경우 리소스 일시 중단 됨)
- `reinstate` (경우 리소스에 복원 되었습니다 일시 중단 후)

여기서 상태는 다음 중 하나일 수 있습니다. 
- **NotStarted** <br>
 - **InProgress** <br>
- **성공함** <br>
- **실패** <br>
- **충돌** <br>

웹 후크 알림을에서 가능한 상태는 **Succeeded** 하 고 **실패**합니다. 작업의 수명 주기는 **NotStarted** 와 같은 터미널 상태로 **Succeeded**를 **실패**, 또는 **충돌**합니다. 표시 되 면 **NotStarted** 하거나 **InProgress**, 작업에 작업을 수행 하기 전에 터미널 상태에 도달할 때까지 상태 가져오기 API를 통해 요청을 계속 합니다. 

## <a name="mock-apis"></a>모의 Api

모의 Api를 개발, 특히 프로토타입도을 테스트 프로젝트를 사용 하 여 시작 하는 데 사용할 수 있습니다. 

끝점 호스트: `https://marketplaceapi.microsoft.com/api` (인증 필요 없음)<br/>
API 버전: `2018-09-15`<br/>
샘플 URI: `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

API 끝점 경로 mock와 실제 Api에서 동일 하지만 API 버전 다릅니다. 버전이 `2018-09-15` 모의 버전 및 `2018-08-31` 프로덕션 버전에 대 한 합니다. 

이 문서에서는 API 호출의 모의 호스트 끝점을 만들 수 있습니다. 일반적으로 모의 데이터를 응답으로 예상 됩니다. 모의 API에서 업데이트 구독 메서드를 호출 하는 항상 500을 반환합니다. 

## <a name="next-steps"></a>다음 단계

또한 프로그래밍 방식으로 검색 하 고 사용 하 여 워크 로드, 제품 및 게시자 프로필을 조작할 수 개발자는 [클라우드 파트너 포털 REST Api](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)합니다.
