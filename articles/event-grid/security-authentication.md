---
title: Azure Event Grid 보안 및 인증
description: Azure Event Grid 및 해당 개념을 설명합니다.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: babanisa
ms.openlocfilehash: e8913c1f198c89bdcd779d2faf2706f9d4079c5c
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846299"
---
# <a name="event-grid-security-and-authentication"></a>Event Grid 보안 및 인증 

Azure Event Grid에는 세 가지 유형의 인증이 있습니다.

* WebHook 이벤트 전달
* 이벤트 구독
* 사용자 지정 토픽 게시

## <a name="webhook-event-delivery"></a>WebHook 이벤트 전달

웹후크는 Azure Event Grid에서 이벤트를 수신하는 여러 가지 방법 중 하나입니다. 새 이벤트가 준비되면 Event Grid 서비스는 요청 본문에 이벤트가 포함되어 구성된 엔드포인트로 HTTP 요청을 게시합니다.

웹후크를 지원하는 여러 다른 서비스와 마찬가지로, Event Grid를 사용하려면 해당 엔드포인트로 이벤트 제공을 시작하기 전에 웹후크 엔드포인트에 대한 소유권을 증명해야 합니다. 이 요구 사항으로 인해 악의적인 사용자가 엔드포인트에서 과도한 이벤트를 발생하는 일이 방지됩니다. 아래 나열된 세 가지 Azure 서비스를 사용하는 경우 Azure 인프라는 자동으로 이 유효성 검사를 처리합니다.

* [Event Grid 커넥터](https://docs.microsoft.com/connectors/azureeventgrid/)를 포함하는 Azure Logic Apps
* [웹후크](../event-grid/ensure-tags-exists-on-new-virtual-machines.md)를 통한 Azure Automation
* [Event Grid Trigger](../azure-functions/functions-bindings-event-grid.md)를 포함하는 Azure Functions

HTTP 트리거 기반 Azure 함수와 같은 엔드포인트의 다른 형식을 사용하는 경우, 엔드포인트 코드가 Event Grid를 통해 핸드셰이크 유효성 검사에 참여해야 합니다. Event Grid는 두 가지 방법의 구독 유효성 검사를 지원합니다.

1. **Validationcode 핸드셰이크 (프로그래밍 방식)** : 엔드포인트에 대한 소스 코드를 제어 하는 경우이 메서드를 권장 합니다. 이벤트 구독 생성 시 Event Grid는 엔드포인트에 구독 유효성 검사 이벤트를 전송합니다. 이 이벤트의 스키마는 다른 Event Grid 이벤트와 비슷합니다. 이 이벤트의 데이터 부분에는 `validationCode` 속성이 포함됩니다. 애플리케이션은 예상된 이벤트 구독에 대해 유효성 검사 요청이 수행된 것인지 확인하고 Event Grid로 유효성 검사 코드를 에코합니다. 이 핸드셰이크 메커니즘은 모든 Event Grid 버전에서 지원됩니다.

2. **Validationurl 핸드셰이크 (수동)** : 특정 한 경우에는 엔드포인트의 소스 코드에 액세스 하 여 validationurl 핸드셰이크를 구현할 수 없습니다. 예를 들어, 타사 서비스를 사용하는 경우(예: [Zapier](https://zapier.com) 또는 [IFTTT](https://ifttt.com/)) 유효성 검사 코드를 통해 프로그래밍 방식으로 응답하지 못할 수 있습니다.

   버전 2018-05-01-미리 보기부터 Event Grid는 수동 유효성 검사 핸드셰이크를 지원합니다. API 버전 2018-05-01-미리 보기 이상을 사용하는 SDK 또는 도구에서 이벤트 구독을 만드는 경우 Event Grid는 구독 유효성 검사 이벤트의 데이터 부분에 `validationUrl` 속성을 전송합니다. 핸드셰이크를 완료하려면 이벤트 데이터에서 해당 URL을 찾은 후 GET 요청을 수동으로 전송합니다. REST 클라이언트 또는 웹 브라우저를 사용할 수 있습니다.

   제공 된 URL은 5 분 동안 유효 합니다. 이 시간 동안 이벤트 구독의 프로비전 상태가 `AwaitingManualAction`입니다. 5 분 내에 수동 유효성 검사를 완료 하지 않으면 프로 비전 상태가 `Failed`로 설정 됩니다. 수동 유효성 검사를 시작하기 전에 이벤트 구독을 다시 작성해야 합니다.

    또한이 인증 메커니즘을 사용 하려면 웹 후크 엔드포인트에서 HTTP 상태 코드 200을 반환 하 여 유효성 검사 이벤트의 게시물이 수동 유효성 검사 모드에 배치 되기 전에 승인 되었음을 알 수 있습니다. 즉, 엔드포인트이 200을 반환 하지만 프로그래밍 방식으로 유효성 검사 응답을 반환 하지 않는 경우 모드가 수동 유효성 검사 모드로 전환 됩니다. 5 분 이내에 유효성 검사 URL에 대한 GET이 있으면 유효성 검사 핸드셰이크가 성공한 것으로 간주 됩니다.

> [!NOTE]
> 유효성 검사에 자체 서명 된 인증서를 사용 하는 것은 지원 되지 않습니다. 대신 CA (인증 기관)에서 서명 된 인증서를 사용 합니다.

### <a name="validation-details"></a>유효성 검사 세부 정보

* 이벤트 구독 생성/업데이트 시 Event Grid는 대상 엔드포인트에 구독 유효성 검사 이벤트를 게시합니다. 
* 이벤트에는 “aeg-event-type: SubscriptionValidation” 헤더 값이 포함됩니다.
* 이벤트 본문에는 다른 Event Grid 이벤트와 동일한 스키마가 있습니다.
* 이벤트의 eventType 속성은 `Microsoft.EventGrid.SubscriptionValidationEvent`입니다.
* 이벤트의 데이터 속성에는 임의로 생성된 문자열을 포함한 `validationCode` 속성이 있습니다. 예를 들어 “validationCode: acb13...”과 같습니다.
* 이벤트 데이터는 구독에 대해 수동으로 유효성 검사를 수행하기 위해 URL에 `validationUrl` 속성도 포함되어 있습니다.
* 배열에는 유효성 검사 이벤트만 포함됩니다. 다른 이벤트는 유효성 검사 코드를 에코 백한 후 별도의 요청으로 전송됩니다.
* EventGrid DataPlane SDK에는 구독 유효성 검사 이벤트 데이터 및 구독 유효성 검사 응답에 해당하는 클래스가 있습니다.

SubscriptionValidationEvent 예가 다음 예제에 나와 있습니다.

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
    "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=512d38b6-c7b8-40c8-89fe-f46f9e9622b6&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1A1A1A1A"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]
```

엔드포인트 소유권을 증명하기 위해 다음 예제와 같이 validationResponse 속성의 유효성 검사 코드를 에코 백합니다.

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

HTTP 200 정상 응답 상태 코드를 반환해야 합니다. HTTP 202 수락은 유효한 Event Grid 구독 유효성 검사 응답으로 인식되지 않습니다. Http 요청은 30 초 내에 완료 되어야 합니다. 작업이 30 초 내에 완료 되지 않으면 작업이 취소 되 고 5 초 후에 다시 시도 될 수 있습니다. 모든 시도가 실패 하면 유효성 검사 핸드셰이크 오류로 처리 됩니다.

또는 유효성 검사 URL에 GET 요청을 수동으로 전송하여 구독이 유효한지 수동으로 검사할 수 있습니다. 이벤트 구독은 유효성을 검사할 때까지 보류 상태로 유지됩니다. 유효성 검사 Url은 포트 553를 사용 합니다. 방화벽 규칙이 포트 553를 차단할 경우 성공적인 수동 핸드셰이크를 위해 규칙을 업데이트 해야 할 수 있습니다.

구독 유효성 검사 핸드셰이크 처리 예제를 보려면 [C# 샘플](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs)을 참조하세요.

### <a name="checklist"></a>검사 목록

이벤트 구독을 만드는 동안 "제공 된 엔드포인트의 유효성을 검사 하는 데 실패 했습니다. https:\// 자세한 내용은 https:\//aka.ms/esvalidation "를 참조 하세요 .이는 유효성 검사 핸드셰이크에 오류가 있음을 나타냅니다. 이 오류를 해결하려면 다음과 같은 측면을 확인합니다.

* 대상 엔드포인트에서 실행 되는 응용 프로그램 코드를 제어 하나요? 예를 들어, HTTP 트리거 기반 Azure Function을 작성하는 경우 이를 변경하기 위해 애플리케이션 코드에 액세스할 수 있습니까?
* 애플리케이션 코드에 액세스할 수 있는 경우 위의 샘플에서와 같이 ValidationCode 기반 핸드셰이크 메커니즘을 구현하세요.

* 애플리케이션 코드에 액세스할 수 없는 경우(예: 웹후크를 지원하는 타사 서비스를 사용하는 경우), 수동 핸드셰이크 메커니즘을 사용할 수 있습니다. 2018-05-01-미리 보기 API 버전 이상(Event Grid Azure CLI 확장 설치)을 사용하여 유효성 검사 이벤트에서 validationUrl을 수신했는지 확인합니다. 수동 유효성 검사 핸드셰이크를 완료하려면 `validationUrl` 속성의 값을 가져오고 웹 브라우저에서 해당 URL을 방문합니다. 유효성 검사에 성공하는 경우 유효성 검사가 성공했다는 메시지가 웹 브라우저에 표시됩니다. 해당 이벤트 구독의 프로비저닝 상태가 “성공”이라고 표시됩니다. 

### <a name="event-delivery-security"></a>이벤트 전달 보안

#### <a name="azure-ad"></a>Azure AD

Azure Active Directory를 사용 하 여 엔드포인트에 이벤트를 게시할 Event Grid를 인증 하 고 권한을 부여 하 여 webhook 엔드포인트의 보안을 유지할 수 있습니다. Azure Active Directory 응용 프로그램을 만들고 응용 프로그램에서 Event Grid 권한을 부여 하는 역할 및 서비스 주체를 만들고 Azure AD 응용 프로그램을 사용 하도록 이벤트 구독을 구성 해야 합니다. [Event Grid를 사용 하 여 AAD를 구성 하는 방법을 알아봅니다](secure-webhook-delivery.md).

#### <a name="query-parameters"></a>쿼리 매개 변수
이벤트 구독을 만들 때 Webhook URL에 쿼리 매개 변수를 추가하여 Webhook 엔드포인트를 보호할 수 있습니다. 해당 쿼리 매개 변수 중 하나를 [액세스 토큰](https://en.wikipedia.org/wiki/Access_token)과 같은 비밀로 설정합니다. 웹후크는 이 비밀을 사용하여 이벤트가 유효한 사용 권한이 있는 Event Grid에서 제공된다고 인식할 수 있습니다. Event Grid는 Webhook에 모든 이벤트 전달에서 해당 쿼리 매개 변수를 포함합니다.

이벤트 구독을 편집할 때 [--include-full-endpoint-url](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) 매개 변수가 Azure [CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)에 사용되지 않는 한 쿼리 매개 변수는 표시되거나 반환되지 않습니다.

마지막으로 Azure Event Grid가 HTTPS 웹후크 엔드포인트를 지원한다는 점에 유의합니다.

## <a name="event-subscription"></a>이벤트 구독

이벤트를 구독하려면 이벤트 소스 및 처리기에 액세스할 수 있는 권한이 있음을 입증해야 합니다. WebHook 소유에 대한 입증은 이전 섹션에서 설명했습니다. WebHook(예: 이벤트 허브 또는 Queue Storage)이 아닌 이벤트 처리기를 사용하는 경우 해당 리소스에 대한 쓰기 권한이 필요합니다. 이 권한 검사는 권한 없는 사용자가 리소스에 이벤트를 전송하지 못하도록 합니다.

이벤트 소스인 리소스에 **Microsoft.EventGrid/EventSubscriptions/Write** 권한이 있어야 합니다. 리소스의 범위에서 새 구독을 작성하기 때문에 이 권한이 있어야 합니다. 필요한 리소스는 시스템 항목 또는 사용자 지정 항목을 구독하는지 여부에 따라 다릅니다. 두 형식은 모두 이 섹션에 설명되어 있습니다.

### <a name="system-topics-azure-service-publishers"></a>시스템 항목(Azure 서비스 게시자)

시스템 항목의 경우 이벤트를 게시하는 리소스의 범위에서 새 이벤트 구독을 쓸 수 있는 사용 권한이 필요합니다. 리소스의 형식은 다음과 같습니다. `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

예를 들어 **myacct**라는 스토리지 계정에 이벤트를 구독하려면 `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`에 대한 Microsoft.EventGrid/EventSubscriptions/Write 권한이 필요합니다.

### <a name="custom-topics"></a>사용자 지정 항목

사용자 지정 항목의 경우 Event Grid 항목의 범위에서 새 이벤트 구독을 쓸 수 있는 권한이 필요합니다. 리소스의 형식은 다음과 같습니다. `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

예를 들어 **mytopic**이라는 사용자 지정 항목을 구독하려면 `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`에 대한 Microsoft.EventGrid/EventSubscriptions/Write 권한이 필요합니다.

## <a name="custom-topic-publishing"></a>사용자 지정 토픽 게시

사용자 지정 토픽은 SAS(공유 액세스 서명) 또는 키 인증을 사용합니다. SAS를 사용하는 것이 좋지만 키 인증에서는 간단한 프로그래밍을 제공하며 기존의 많은 Webhook 게시자와 호환 가능합니다. 

HTTP 헤더에서 인증 값을 포함합니다. SAS의 경우 헤더 값에 **aeg-sas-token**을 사용합니다. 키 인증의 경우 헤더 값에 **aeg-sas-key**를 사용합니다.

### <a name="key-authentication"></a>키 인증

키 인증은 인증의 가장 간단한 양식입니다. 사용할 양식은 다음과 같습니다. `aeg-sas-key: <your key>`

예를 들어 다음을 사용하여 키를 전달합니다.

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>SAS 토큰

Event Grid의 SAS 토큰에는 리소스, 만료 시간 및 서명이 포함됩니다. SAS 토큰의 형식은 다음과 같습니다. `r={resource}&e={expiration}&s={signature}`

리소스는 이벤트를 전송할 Event Grid 항목의 경로입니다. 예를 들어 올바른 리소스 경로는 다음과 같습니다. `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

키에서 서명을 생성합니다.

예를 들어 유효한 **aeg-sas-token** 값은 다음과 같습니다.

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

다음 예제에서는 Event Grid를 사용하기 위한 SAS 토큰을 만듭니다.

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    var culture = CultureInfo.CreateSpecificCulture("en-US");
    var encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString(culture));

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

## <a name="management-access-control"></a>관리 Access Control

Azure Event Grid를 사용하면 여러 사용자가 이벤트 구독 나열, 새 구독 만들기 및 키 생성과 같은 다양한 관리 작업을 수행할 수 있는 액세스 수준을 제어할 수 있습니다. Event Grid는 Azure의 RBAC(역할 기반 액세스 제어)를 사용합니다.

### <a name="operation-types"></a>작업 형식

Event Grid는 다음 작업을 지원합니다.

* Microsoft.EventGrid/*/read
* Microsoft.EventGrid/*/write
* Microsoft.EventGrid/*/delete
* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action
* Microsoft.EventGrid/topics/listKeys/action
* Microsoft.EventGrid/topics/regenerateKey/action

마지막 세 가지 작업에서는 일반 읽기 작업에서 필터링을 가져오는 비밀 정보를 잠재적으로 반환합니다. 이 작업에 대한 액세스를 제한하는 것이 좋습니다. 

### <a name="built-in-roles"></a>기본 제공 역할

Event Grid는 이벤트 구독을 관리하기 위한 두 가지 기본 제공 역할을 제공합니다. [이벤트 도메인](event-domains.md)을 구현할 때 이벤트 도메인의 토픽에 가입하는 데 필요한 권한을 사용자에게 부여하기 때문에 중요합니다. 이러한 역할은 이벤트 구독에 중점을 두며, 토픽 만들기와 같은 작업에 대한 액세스 권한을 부여하지 않습니다.

[사용자 또는 그룹에 이러한 역할을 할당](../role-based-access-control/quickstart-assign-role-user-portal.md)할 수 있습니다.

**Eventgrid Eventgrid 참여자**: Event Grid 구독 작업 관리

```json
[
  {
    "Description": "Lets you manage EventGrid event subscription operations.",
    "IsBuiltIn": true,
    "Id": "428e0ff05e574d9ca2212c70d0e0a443",
    "Name": "EventGrid EventSubscription Contributor",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/*",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "Condition": null
      }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

**Eventgrid Eventgrid Reader**: Event Grid 구독 읽기

```json
[
  {
    "Description": "Lets you read EventGrid event subscriptions.",
    "IsBuiltIn": true,
    "Id": "2414bbcf64974faf8c65045460748405",
    "Name": "EventGrid EventSubscription Reader",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/read",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": []
       }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

### <a name="custom-roles"></a>사용자 지정 역할

기본 제공 역할이 아닌 다른 사용 권한을 지정해야 할 경우 사용자 지정 역할을 만들 수 있습니다.

사용자가 다른 동작을 수행할 수 있는 샘플 Event Grid 역할 정의는 다음과 같습니다. 이러한 사용자 지정 역할은 이벤트 구독보다 더 광범위한 액세스 권한을 부여하기 때문에 기본 제공 역할과는 다릅니다.

**EventGridReadOnlyRole.json**: 읽기 전용 작업만을 허용합니다.

```json
{
  "Name": "Event grid read only role",
  "Id": "7C0B6B59-A278-4B62-BA19-411B70753856",
  "IsCustom": true,
  "Description": "Event grid read only role",
  "Actions": [
    "Microsoft.EventGrid/*/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription Id>"
  ]
}
```

**EventGridNoDeleteListKeysRole.json**: 제한된 게시 작업을 허용하지만 삭제 동작을 허용하지 않습니다.

```json
{
  "Name": "Event grid No Delete Listkeys role",
  "Id": "B9170838-5F9D-4103-A1DE-60496F7C9174",
  "IsCustom": true,
  "Description": "Event grid No Delete Listkeys role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action"
  ],
  "NotActions": [
    "Microsoft.EventGrid/*/delete"
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

**EventGridContributorRole.json**: 모든 Event Grid 작업을 허용합니다.

```json
{
  "Name": "Event grid contributor role",
  "Id": "4BA6FB33-2955-491B-A74F-53C9126C9514",
  "IsCustom": true,
  "Description": "Event grid contributor role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/*/delete",
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

[PowerShell](../role-based-access-control/custom-roles-powershell.md), [Azure CLI](../role-based-access-control/custom-roles-cli.md) 및 [REST](../role-based-access-control/custom-roles-rest.md)를 사용하여 사용자 지정 역할을 만들 수 있습니다.

## <a name="encryption-at-rest"></a>휴지 상태의 암호화

Event Grid 서비스에서 디스크에 기록 하는 모든 이벤트 또는 데이터는 Microsoft에서 관리 하는 키로 암호화 되어 암호화 된 상태로 유지 됩니다. 또한 이벤트 또는 데이터가 유지 되는 최대 기간은 [Event Grid 재시도 정책을](delivery-and-retry.md)준수 하 여 24 시간입니다. Event Grid는 24 시간 후에 모든 이벤트 또는 데이터를 자동으로 삭제 하 고, 이벤트의 ttl (time to live) 중 더 작은 값을 자동으로 삭제 합니다.

## <a name="next-steps"></a>다음 단계

* Event Grid에 대한 소개는 [Event Grid 정보](overview.md)를 참조하세요.
