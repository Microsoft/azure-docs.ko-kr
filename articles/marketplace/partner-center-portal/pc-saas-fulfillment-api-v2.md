---
title: SaaS 행 API v2 | Azure Marketplace
description: 이 문서에서는 연결 된 상품 v2 Api를 사용 하 여 AppSource 및 Azure Marketplace에서 SaaS 제품을 만들고 관리 하는 방법을 설명 합니다.
services: Azure, Marketplace, Cloud Partner Portal,
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 10/18/2019
ms.author: evansma
ms.openlocfilehash: 4c73a59352422626ec3c6012607009995479d0cc
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73816597"
---
# <a name="saas-fulfillment-apis-version-2"></a>SaaS 처리 Api, 버전 2 

이 문서에서는 파트너가 AppSource marketplace 및 Azure Marketplace에서 SaaS 응용 프로그램을 판매할 수 있도록 하는 Api에 대해 자세히 설명 합니다. 이러한 Api는 AppSource 및 Azure Marketplace에서 SaaS 제품을 불가능 하는 데 필요 합니다.

## <a name="managing-the-saas-subscription-life-cycle"></a>SaaS 구독 수명 주기 관리

Azure SaaS는 SaaS 구독 구매의 전체 수명 주기를 관리 합니다. 이는 처리 Api를 메커니즘으로 사용 하 여 실제 처리, 계획에 대 한 변경 및 파트너와의 구독 삭제를 구동 합니다. 고객의 청구서는 Microsoft에서 유지 관리 하는 SaaS 구독의 상태를 기준으로 합니다. 다음 다이어그램은 상태와 상태를 변경 하는 작업을 보여 줍니다.

![SaaS 구독 수명-주기 상태](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>SaaS 구독의 상태

다음 표에서는 각 (해당 하는 경우)에 대 한 설명 및 시퀀스 다이어그램을 포함 하 여 SaaS 구독의 프로 비전 상태를 보여 줍니다. 

#### <a name="provisioning"></a>프로비전

고객이 구매를 시작 하면 파트너는 URL 매개 변수를 사용 하는 고객 대화형 웹 페이지의 인증 코드에서이 정보를 받습니다. 예는 `https://contoso.com/signup?token=..`반면 파트너 센터의 방문 페이지 URL은 `https://contoso.com/signup`됩니다. 인증 코드는 확인 API를 호출 하 여 프로 비전 서비스의 세부 정보를 확인 하 고 교환할 수 있습니다.  SaaS 서비스는 프로 비전을 완료 하면 해당 작업이 완료 되 고 고객의 요금이 청구 될 수 있음을 알리기 위해 활성화 호출을 보냅니다. 

다음 다이어그램에서는 프로 비전 시나리오에 대 한 API 호출의 시퀀스를 보여 줍니다.  

![SaaS 서비스 프로 비전을 위한 API 호출](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>프로비전됨

이 상태는 프로 비전 된 서비스의 안정 된 상태입니다.

##### <a name="provisioning-for-update"></a>업데이트 프로 비전 

이 상태는 기존 서비스에 대 한 업데이트가 보류 중임을 나타냅니다. 이러한 업데이트는 marketplace 또는 SaaS 서비스에서 고객에 의해 시작 될 수 있습니다 (직접-고객 트랜잭션만 해당).

##### <a name="provisioning-for-update-when-its-initiated-from-the-marketplace"></a>업데이트 프로 비전 (marketplace에서 시작 된 경우)

다음 다이어그램에서는 marketplace에서 업데이트가 시작 될 때의 일련의 작업을 보여 줍니다.

![업데이트가 marketplace에서 시작 되는 경우 API 호출](./media/saas-update-api-v2-calls-from-marketplace-a.png)

##### <a name="provisioning-for-update-when-its-initiated-from-the-saas-service"></a>업데이트 프로 비전 (SaaS 서비스에서 시작 된 경우)

다음 다이어그램은 SaaS 서비스에서 업데이트를 시작 하는 경우의 동작을 보여 줍니다. (웹 후크 호출은 SaaS 서비스에 의해 시작 되는 구독에 대 한 업데이트로 대체 됩니다.) 

![SaaS 서비스에서 업데이트를 시작 하면 API 호출](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>일시 중단

이 상태는 고객의 지불을 받지 못했음을 나타냅니다. 정책에 따라 구독을 취소 하기 전에 고객에 게 유예 기간을 제공 합니다. 구독이 다음 상태에 있는 경우: 

- 파트너는 서비스에 대 한 사용자의 액세스를 차단 하거나 차단 하도록 선택할 수 있습니다.
- 구독은 데이터 나 설정의 손실 없이 전체 기능을 복원할 수 있는 복구 가능한 상태로 유지 되어야 합니다. 
- 유예 기간이 종료 되 면 처리 Api 또는 프로 비전 해제 요청을 통해이 구독에 대해 복원 요청을 받을 것으로 간주 합니다. 

#### <a name="unsubscribed"></a>취소 

구독은 명시적인 고객 요청 또는 dues에 대 한 응답으로이 상태에 도달 합니다. 파트너의 기대는 특정 기간 (일) 동안 요청 시 복구를 위해 고객의 데이터를 보존 한 후 삭제 하는 것입니다. 


## <a name="api-reference"></a>API 참조

이 섹션에서는 SaaS *구독 api* 및 *작업 api*에 대해 설명 합니다.  버전 2 Api에 대 한 `api-version` 매개 변수의 값이 `2018-08-31`됩니다.  


### <a name="parameter-and-entity-definitions"></a>매개 변수 및 엔터티 정의

다음 표에서는 처리 중인 Api에서 사용 하는 일반 매개 변수 및 엔터티에 대 한 정의를 나열 합니다.

|     엔터티/매개 변수     |     정의                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | SaaS 리소스의 GUID 식별자입니다.  |
| `name`                   | 고객이이 리소스에 대해 제공 하는 이름입니다. |
| `publisherId`            | 각 게시자의 고유한 문자열 식별자입니다 (예: "contoso"). |
| `offerId`                | 각 제품에 대 한 고유 문자열 식별자입니다 (예: "offer1").  |
| `planId`                 | 각 계획/SKU에 대 한 고유 문자열 식별자입니다 (예: "은색"). |
| `operationId`            | 특정 작업에 대 한 GUID 식별자입니다.  |
|  `action`                | 리소스에서 수행 되는 작업 (`Unsubscribe`, `Suspend`, `Reinstate`또는 `ChangePlan`, `ChangeQuantity`, `Transfer`입니다. |
|   |   |

[Guid](https://en.wikipedia.org/wiki/Universally_unique_identifier)(Globally unique identifier)는 일반적으로 자동으로 생성 되는 128 비트 (32-16 진수) 번호입니다. 

#### <a name="resolve-a-subscription"></a>구독 확인 

확인 엔드포인트은 게시자가 marketplace 토큰을 영구 리소스 ID로 확인할 수 있도록 합니다. 리소스 ID는 SaaS 구독의 고유 식별자입니다. 사용자가 파트너의 웹 사이트로 리디렉션되는 경우 URL에는 쿼리 매개 변수의 토큰이 포함 됩니다. 파트너는이 토큰을 사용 하 고이 토큰을 확인 하도록 요청 해야 합니다. 응답에는 고유한 SaaS 구독 ID, 이름, 제품 ID 및 리소스에 대 한 계획이 포함 됩니다. 이 토큰은 1 시간 동안만 유효 합니다. 

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>올리기<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  이 요청에 사용할 작업의 버전입니다.  |

*요청 헤더:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  콘텐츠 형식      | `application/json` |
|  x-ms-requestid    |  클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값을 제공 하지 않으면 하나는 생성 되 고 응답 헤더에 제공 됩니다. |
|  x-ms-correlationid |  클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수는 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상관 관계를 설정 합니다. 이 값을 제공 하지 않으면 하나는 생성 되 고 응답 헤더에 제공 됩니다.  |
|  권한 부여     |  [JWT (JSON web token) 전달자 토큰을 가져옵니다](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). 예를 들면 "`Bearer <access_token>`"입니다. |
|  x-ms-marketplace-token  |  사용자가 Azure에서 SaaS 파트너의 웹 사이트로 리디렉션되는 경우 URL의 토큰 쿼리 매개 변수 (예: `https://contoso.com/signup?token=..`). *참고:* URL은 사용 하기 전에 브라우저에서 토큰 값을 디코딩합니다.  |

*응답 코드:*

코드: 200<br>
불투명 토큰을 SaaS 구독으로 확인 합니다. 응답 본문:
 

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
잘못된 요청입니다. x-y-토큰이 누락 되었거나 형식이 잘못 되었거나 만료 되었습니다.

코드: 403<br>
권한이 없습니다. 인증 토큰이 제공 되지 않았거나 잘못 되었거나 요청에서 현재 게시자에 속하지 않은 획득에 액세스 하려고 합니다.

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

구독 API는 **Get**, **Post**, **PATCH**및 **Delete**와 같은 HTTPS 작업을 지원 합니다.


#### <a name="list-subscriptions"></a>구독 나열

게시자에 대 한 모든 SaaS 구독을 나열 합니다.

##### <a name="getbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>가져오기<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  이 요청에 사용할 작업의 버전입니다.  |

*요청 헤더:*

|                    |                   |
|  ---------------   |  ---------------  |
| 콘텐츠 형식       |  `application/json`  |
| x-ms-requestid     |  클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값을 제공 하지 않으면 하나는 생성 되 고 응답 헤더에 제공 됩니다. |
| x-ms-correlationid |  클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수는 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상관 관계를 설정 합니다. 이 값을 제공 하지 않으면 하나는 생성 되 고 응답 헤더에 제공 됩니다.  |
| 권한 부여      |  [JWT (JSON web token) 전달자 토큰을 가져옵니다](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). 예를 들면 "`Bearer <access_token>`"입니다.  |

*응답 코드:*

코드: 200 <br/>
인증 토큰을 기반으로 모든 게시자의 제품에 대 한 게시자 및 해당 구독을 가져옵니다.

>[!Note]
>[모의 api](#mock-apis) 는 제품을 처음 개발할 때 사용 되는 반면 실제 api는 실제로 제품을 게시할 때 사용 해야 합니다.  실제 Api와 모의 Api는 코드의 첫 번째 줄과 다릅니다.  실제 API에는 `subscription` 섹션이 있지만이 섹션은 모의 API에는 존재 하지 않습니다.

모의 API에 대 한 응답 페이로드:<br>

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
            "term": {
                "startDate": "2019-05-31",
                "endDate": "2019-06-29",
                "termUnit": "P1M"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "isFreeTrial": "true", // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial.
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```
실제 API의 경우: <br>

```json
{
  "subscriptions": [
      {
          "id": "<guid>",
          "name": "Contoso Cloud Solution",
          "publisherId": "contoso",
          "offerId": "offer1",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant, object id and email address for which SaaS subscription is purchased.
              "emailId": "<email>",
              "objectId": "<guid>",                     
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant, object id and email address that purchased the SaaS subscription. These could be different for reseller scenario
              "emailId": "<email>",
              "objectId": "<guid>",                      
              "tenantId": "<guid>"
          },
            "term": {
                "startDate": "2019-05-31",
                "endDate": "2019-06-29",
                "termUnit": "P1M"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "isFreeTrial": true, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial.(optional field - default false)
          "isTest": false, //indicating whether the current subscription is a test asset
          "sandboxType": "None", // Possible Values: None, Csp (Csp sandbox purchase)
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "@nextLink": ""
}
```
연속 토큰은 검색할 계획의 추가 "페이지"가 있는 경우에만 표시 됩니다. 

코드: 403 <br>
권한이 없습니다. 인증 토큰이 제공 되지 않았거나 잘못 되었거나 요청에서 현재 게시자에 속하지 않은 획득에 액세스 하려고 합니다. 

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

지정 된 SaaS 구독을 가져옵니다. 이 호출을 사용 하 여 라이선스 정보 및 계획 정보를 가져옵니다.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>가져오기<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   확인 API를 통해 토큰을 확인 한 후에 얻은 SaaS 구독의 고유 식별자입니다.   |
|  ApiVersion        |   이 요청에 사용할 작업의 버전입니다.   |

*요청 헤더:*

|                    |                   |
|  ---------------   |  ---------------  |
|  콘텐츠 형식      |  `application/json`  |
|  x-ms-requestid    |  클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값을 제공 하지 않으면 하나는 생성 되 고 응답 헤더에 제공 됩니다. |
|  x-ms-correlationid |  클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수는 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상관 관계를 설정 합니다. 이 값을 제공 하지 않으면 하나는 생성 되 고 응답 헤더에 제공 됩니다.  |
|  권한 부여     |  [JWT (JSON web token) 전달자 토큰을 가져옵니다](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). 예를 들면 "`Bearer <access_token>`"입니다.  |

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
        "isFreeTrial": "true", // true - customer subscription is currently in free trial, false - customer subscription is not currently in free trial.
        "status": "Subscribed", // Indicates the status of the operation.
          "term": { //This gives the free trial term start and end date
            "startDate": "2019-05-31",
            "endDate": "2019-06-29",
            "termUnit": "P1M" //where P1M: Monthly, P1Y: Yearly 
        },
}
```

코드: 403<br>
권한이 없습니다. 인증 토큰이 제공 되지 않았거나 잘못 되었거나 요청에서 현재 게시자에 속하지 않은 획득에 액세스 하려고 합니다.

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

#### <a name="list-available-plans"></a>사용 가능한 계획 나열

이 호출을 사용 하 여 현재 게시자에 대 한 비공개 또는 공용 제안이 있는지 확인 합니다.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>가져오기<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   이 요청에 사용할 작업의 버전입니다.  |

*요청 헤더:*

|                    |                   |
|  ---------------   |  ---------------  |
|   콘텐츠 형식     |  `application/json` |
|   x-ms-requestid   |   클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값을 제공 하지 않으면 하나는 생성 되 고 응답 헤더에 제공 됩니다. |
|  x-ms-correlationid  | 클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수는 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상관 관계를 설정 합니다. 이 값을 제공 하지 않으면 하나는 생성 되 고 응답 헤더에 제공 됩니다. |
|  권한 부여     |  [JWT (JSON web token) 전달자 토큰을 가져옵니다](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  예를 들면 "`Bearer <access_token>`"입니다. |

*응답 코드:*

코드: 200<br>
고객에 대해 사용 가능한 계획의 목록을 가져옵니다. 응답 본문:

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
권한이 없습니다. 인증 토큰이 제공 되지 않았거나 잘못 되었거나 요청에서 현재 게시자에 속하지 않은 획득에 액세스 하려고 합니다. <br> 

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
| subscriptionId     | 확인 API를 사용 하 여 토큰을 확인 한 후에 얻은 SaaS 구독의 고유 식별자입니다.  |

*요청 헤더:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  콘텐츠 형식      | `application/json`  |
|  x-ms-requestid    | 클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값을 제공 하지 않으면 하나는 생성 되 고 응답 헤더에 제공 됩니다.  |
|  x-ms-correlationid  | 클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 문자열은 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상관 관계를 설정 합니다. 이 값을 제공 하지 않으면 하나는 생성 되 고 응답 헤더에 제공 됩니다.  |
|  권한 부여     |  [JWT (JSON web token) 전달자 토큰을 가져옵니다](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  예를 들면 "`Bearer <access_token>`"입니다. |

*요청 페이로드:*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*응답 코드:*

코드: 200<br>
구독을 활성화 합니다.<br>

코드: 400<br>
잘못 된 요청: 유효성 검사에 실패 했습니다.

코드: 403<br>
권한이 없습니다. 인증 토큰이 제공 되지 않았거나 잘못 되었거나 요청에서 현재 게시자에 속하지 않은 획득에 액세스 하려고 합니다.

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

#### <a name="change-the-plan-on-the-subscription"></a>구독에 대 한 계획 변경

구독에 대 한 계획을 업데이트 합니다.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>패치<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  이 요청에 사용할 작업의 버전입니다.  |
| subscriptionId     | 확인 API를 사용 하 여 토큰을 확인 한 후에 얻은 SaaS 구독의 고유 식별자입니다.  |

*요청 헤더:*

|                    |                   |
|  ---------------   |  ---------------  |
|  콘텐츠 형식      | `application/json` |
|  x-ms-requestid    |   클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값을 제공 하지 않으면 하나는 생성 되 고 응답 헤더에 제공 됩니다.  |
|  x-ms-correlationid  |  클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수는 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상관 관계를 설정 합니다. 이 값을 제공 하지 않으면 하나는 생성 되 고 응답 헤더에 제공 됩니다.    |
| 권한 부여      |  [JWT (JSON web token) 전달자 토큰을 가져옵니다](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  예를 들면 "`Bearer <access_token>`"입니다.  |

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
| Operation-Location | 작업 상태를 가져올 리소스에 대 한 링크입니다.   |

*응답 코드:*

코드: 202<br>
계획 변경 요청이 수락 되었습니다. 파트너는 작업 위치를 폴링하여 성공 또는 실패를 확인할 것으로 예상 됩니다. <br>

코드: 400<br>
잘못 된 요청: 유효성 검사에 실패 했습니다.

코드: 403<br>
권한이 없습니다. 인증 토큰이 제공 되지 않았거나 잘못 되었거나 요청에서 현재 게시자에 속하지 않은 획득에 액세스 하려고 합니다.

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
>한 번에 하나의 계획 또는 수량만 패치를 적용할 수 있습니다. **업데이트** 를 사용 하는 구독에 대 한 편집은 `allowedCustomerOperations`되지 않습니다.

#### <a name="change-the-quantity-on-the-subscription"></a>구독 수량 변경

구독 수량을 업데이트 합니다.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>패치나<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  이 요청에 사용할 작업의 버전입니다.  |
| subscriptionId     | 확인 API를 사용 하 여 토큰을 확인 한 후에 얻은 SaaS 구독의 고유 식별자입니다.  |

*요청 헤더:*

|                    |                   |
|  ---------------   |  ---------------  |
|  콘텐츠 형식      | `application/json` |
|  x-ms-requestid    |   클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값을 제공 하지 않으면 하나는 생성 되 고 응답 헤더에 제공 됩니다.  |
|  x-ms-correlationid  |  클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수는 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상관 관계를 설정 합니다. 이 값을 제공 하지 않으면 하나는 생성 되 고 응답 헤더에 제공 됩니다.    |
| 권한 부여      |  [JWT (JSON web token) 전달자 토큰을 가져옵니다](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  예를 들면 "`Bearer <access_token>`"입니다.  |

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
| Operation-Location | 작업 상태를 가져오기 위한 리소스에 대 한 링크입니다.   |

*응답 코드:*

코드: 202<br>
수량 변경 요청이 수락 되었습니다. 파트너는 작업 위치를 폴링하여 성공 또는 실패를 확인할 것으로 예상 됩니다. <br>

코드: 400<br>
잘못 된 요청: 유효성 검사에 실패 했습니다.


코드: 403<br>
권한이 없습니다. 인증 토큰이 제공 되지 않았거나 잘못 되었거나 요청에서 현재 게시자에 속하지 않은 획득에 액세스 하려고 합니다.

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
>한 번에 하나의 계획 또는 수량만 패치를 적용할 수 있습니다. **업데이트** 를 사용 하는 구독에 대 한 편집은 `allowedCustomerOperations`되지 않습니다.

#### <a name="delete-a-subscription"></a>구독 삭제

구독을 취소 하 고 지정 된 구독을 삭제 합니다.

##### <a name="deletebr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionid-api-versionapiversion"></a>삭제<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId> ?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  이 요청에 사용할 작업의 버전입니다.  |
| subscriptionId     | 확인 API를 사용 하 여 토큰을 확인 한 후에 얻은 SaaS 구독의 고유 식별자입니다.  |

*요청 헤더:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   콘텐츠 형식     |  `application/json` |
|  x-ms-requestid    |   클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값을 제공 하지 않으면 하나는 생성 되 고 응답 헤더에 제공 됩니다.   |
|  x-ms-correlationid  |  클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수는 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상관 관계를 설정 합니다. 이 값을 제공 하지 않으면 하나는 생성 되 고 응답 헤더에 제공 됩니다.   |
|  권한 부여     |  [JWT (JSON web token) 전달자 토큰을 가져옵니다](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  예를 들면 "`Bearer <access_token>`"입니다.  |

*응답 코드:*

코드: 202<br>
파트너가 SaaS 구독을 구독 취소 하는 호출을 시작 했습니다.<br>

코드: 400<br>
`allowedCustomerOperations`에서 **삭제가** 없는 구독에 대해를 삭제 합니다.

코드: 403<br>
권한이 없습니다. 인증 토큰이 제공 되지 않았거나 잘못 되었거나 요청에서 현재 게시자에 속하지 않은 획득에 액세스 하려고 합니다.

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


### <a name="operations-api"></a>작업 API

작업 API는 다음과 같은 패치 및 가져오기 작업을 지원 합니다.

#### <a name="list-outstanding-operations"></a>미해결 작업 나열 

현재 게시자에 대해 처리 중인 작업을 나열 합니다. 

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>가져오기<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   이 요청에 사용할 작업의 버전입니다.                |
| subscriptionId     | 확인 API를 사용 하 여 토큰을 확인 한 후에 얻은 SaaS 구독의 고유 식별자입니다.  |

*요청 헤더:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   콘텐츠 형식     |  `application/json` |
|  x-ms-requestid    |  클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값을 제공 하지 않으면 하나는 생성 되 고 응답 헤더에 제공 됩니다.  |
|  x-ms-correlationid |  클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수는 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상관 관계를 설정 합니다. 이 값을 제공 하지 않으면 하나는 생성 되 고 응답 헤더에 제공 됩니다.  |
|  권한 부여     |  [JWT (JSON web token) 전달자 토큰을 가져옵니다](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  예를 들면 "`Bearer <access_token>`"입니다.  |

*응답 코드:*

코드: 200<br> 구독에서 보류 중인 작업 목록을 가져옵니다. 응답 페이로드:

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
잘못 된 요청: 유효성 검사에 실패 했습니다.

코드: 403<br>
권한이 없습니다. 인증 토큰이 제공 되지 않았거나 잘못 되었거나 요청에서 현재 게시자에 속하지 않은 획득에 액세스 하려고 합니다.

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

게시자가 지정 된 트리거된 비동기 작업 (예: `Subscribe`, `Unsubscribe`, `ChangePlan`또는 `ChangeQuantity`)의 상태를 추적할 수 있도록 합니다.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>가져오기<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  이 요청에 사용할 작업의 버전입니다.  |

*요청 헤더:*

|                    |                   |
|  ---------------   |  ---------------  |
|  콘텐츠 형식      |  `application/json`   |
|  x-ms-requestid    |   클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값을 제공 하지 않으면 하나는 생성 되 고 응답 헤더에 제공 됩니다.  |
|  x-ms-correlationid |  클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수는 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상관 관계를 설정 합니다. 이 값을 제공 하지 않으면 하나는 생성 되 고 응답 헤더에 제공 됩니다.  |
|  권한 부여     |  [JWT (JSON web token) 전달자 토큰을 가져옵니다](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). 예를 들면 "`Bearer <access_token>`"입니다.  |

*응답 코드:*<br>

코드: 200<br> 지정 된 보류 중인 SaaS 작업을 가져옵니다. 응답 페이로드:

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
잘못 된 요청: 유효성 검사에 실패 했습니다.

코드: 403<br>
권한이 없습니다. 인증 토큰이 제공 되지 않았거나 잘못 되었거나 요청에서 현재 게시자에 속하지 않은 획득에 액세스 하려고 합니다.
 
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
#### <a name="update-the-status-of-an-operation"></a>작업 상태 업데이트

작업 상태를 업데이트 하 여 제공 된 값을 사용 하 여 성공 또는 실패를 표시 합니다.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>패치<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*쿼리 매개 변수:*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  이 요청에 사용할 작업의 버전입니다.  |
| subscriptionId     | 확인 API를 사용 하 여 토큰을 확인 한 후에 얻은 SaaS 구독의 고유 식별자입니다.  |
|  operationId       | 완료 중인 작업입니다. |

*요청 헤더:*

|                    |                   |
|  ---------------   |  ---------------  |
|   콘텐츠 형식     | `application/json`   |
|   x-ms-requestid   |   클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값을 제공 하지 않으면 하나는 생성 되 고 응답 헤더에 제공 됩니다. |
|  x-ms-correlationid |  클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 매개 변수는 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상관 관계를 설정 합니다. 이 값을 제공 하지 않으면 하나는 생성 되 고 응답 헤더에 제공 됩니다. |
|  권한 부여     |  [JWT (JSON web token) 전달자 토큰을 가져옵니다](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  예를 들면 "`Bearer <access_token>`"입니다.  |

*요청 페이로드:*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*응답 코드:*

코드: 200<br> 파트너 측에서 작업 완료를 알리는 호출입니다. 예를 들어이 응답은 사용자 또는 계획의 변경에 신호를 보낼 수 있습니다.

코드: 400<br>
잘못 된 요청: 유효성 검사에 실패 했습니다.

코드: 403<br>
권한이 없습니다. 인증 토큰이 제공 되지 않았거나 잘못 되었거나 요청에서 현재 게시자에 속하지 않은 획득에 액세스 하려고 합니다.

코드: 404<br>
찾을 수 없음

코드: 409<br>
약속. 예를 들어 새 트랜잭션은 이미 충족 된 것입니다.

코드: 500<br> 내부 서버 오류.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="implementing-a-webhook-on-the-saas-service"></a>SaaS 서비스에서 webhook 구현

게시자는이 SaaS 서비스에서 webhook을 구현 하 여 사용자에 게 서비스의 변경 사항을 사전에 알려야 합니다. SaaS 서비스는 webhook 알림에 대 한 작업을 수행 하기 전에 유효성을 검사 하 고 권한을 부여 하기 위해 작업 API를 호출 해야 합니다.


```json
{
  "id": "<this is a GUID operation id, you can call operations API with this to get status>",
  "activityId": "<this is a Guid correlation id>",
  "subscriptionId": "<Guid to uniquely identify this resource>",
  "publisherId": "<this is the publisher's name>",
  "offerId": "<this is the offer name>",
  "planId": "<this is the plan id>",
  "quantity": "<the number of seats, will be null if not per-seat saas offer>",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Unsubscribe",
  "status": "NotStarted"  

}
```
작업은 다음 중 하나일 수 있습니다. 
- `Unsubscribe` (리소스가 삭제 된 경우)
- `ChangePlan` (변경 계획 작업이 완료 된 경우)
- `ChangeQuantity` (수량 변경 작업이 완료 된 경우)
- `Suspend` (리소스가 일시 중단 된 경우)
- `Reinstate` (일시 중단 후 리소스가 복원 된 경우)

여기서 상태는 다음 중 하나일 수 있습니다. 
- **NotStarted** <br>
 - **InProgress** <br>
- **성공함** <br>
- **실패** <br>
- **약속** <br>

웹 후크 알림에서 실행 가능한 상태는 **성공** 및 **실패**중 하나입니다. 작업의 수명 주기는 **성공**, **실패**또는 **충돌**과 같이 **NotStarted** 에서 터미널 상태로 전환 하는 것입니다. **NotStarted** 또는 **InProgress**를 수신 하는 경우 작업을 수행 하기 전에 작업이 터미널 상태에 도달할 때까지 GET API를 통해 상태를 계속 요청 합니다. 

## <a name="mock-apis"></a>모의 Api

모의 Api를 사용 하 여 개발, 특히 프로토타입 작성 및 프로젝트 테스트를 시작할 수 있습니다. 

호스트 엔드포인트: `https://marketplaceapi.microsoft.com/api` (인증 필요 없음)<br/>
API 버전: `2018-09-15`<br/>
샘플 URI: `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

API 엔드포인트 경로는 모의 Api와 실제 Api에서 동일 하지만 API 버전은 다릅니다. 버전은 모의 버전과 프로덕션 버전의 `2018-08-31` `2018-09-15` 됩니다. 

이 문서의 모든 API 호출은 모의 호스트 엔드포인트에 대해 설정할 수 있습니다. 일반적으로 모의 데이터는 응답으로 다시 가져올 것으로 간주 됩니다. 모의 API에서 구독 업데이트 메서드를 호출 하면 항상 500이 반환 됩니다. 

## <a name="next-steps"></a>다음 단계

개발자는 [CLOUD 파트너 포털 REST api](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)를 사용 하 여 프로그래밍 방식으로 작업, 제품 및 게시자 프로필을 검색 하 고 조작할 수도 있습니다.
