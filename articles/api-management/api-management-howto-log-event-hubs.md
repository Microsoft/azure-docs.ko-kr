---
title: Azure API Management에서 Azure Event Hubs에 이벤트를 기록하는 방법 | Microsoft Docs
description: Azure API Management에서 Azure Event Hubs에 이벤트를 기록하는 방법 배우기
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 88f6507d-7460-4eb2-bffd-76025b73f8c4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: apimpm
ms.openlocfilehash: 14f84b5380a1c106114cdab425de7f69f4e19825
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60657546"
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Azure API Management에서 Azure Event Hubs에 이벤트를 기록하는 방법
Azure Event Hubs는 초당 수백만 개의 이벤트를 수집할 수 있는 확장성이 뛰어난 데이터 수집 서비스이므로 연결된 디바이스와 애플리케이션이 생성하는 대량의 데이터를 처리하고 분석할 수 있습니다. Event Hubs는 이벤트 파이프라인에 대한 "현관"의 역할을 하고 데이터가 이벤트 허브에 수집되면 실시간 분석 공급자 또는 일괄 처리/저장소 어댑터를 사용하여 변환 및 저장될 수 있습니다. Event Hubs는 이러한 이벤트를 소비하는 데에서 이벤트 스트림의 프로덕션을 분리하므로 이벤트 소비자가 자신의 개인 일정에 이벤트를 액세스할 수 있습니다.

이 문서는 [Event Hubs와 Azure API Management 통합](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) 동영상과 함께 제공되며 Azure Event Hubs를 사용하여 API Management 이벤트를 기록하는 방법을 설명합니다.

## <a name="create-an-azure-event-hub"></a>Azure 이벤트 허브 만들기

Event Hub를 만들고 Event Hub 간에 이벤트를 송신 및 수신해야 하는 연결 문자열을 가져오는 방법에 대한 자세한 내용은 [Azure Portal을 사용하여 Event Hubs 네임스페이스 및 Event Hub 만들기](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)를 참조하세요.

## <a name="create-an-api-management-logger"></a>API Management 로거 만들기
이제 이벤트 허브를 만들었으므로 다음 단계는 API Management 서비스에서 [로거](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) 를 구성하여 이벤트 허브에 이벤트를 기록할 수 있도록 하는 것입니다.

API Management 로거는 [API Management REST API](https://aka.ms/smapi)를 사용하여 구성됩니다. REST API를 처음으로 사용하기 전에 [필수 구성 요소](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest)를 검토하고 [REST API에 액세스할 수 있도록 설정](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#EnableRESTAPI)했는지 확인합니다.

로거를 만들려면 다음 URL 템플릿을 사용하여 HTTP PUT 요청을 만듭니다.

`https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2017-03-01`

* `{your service}` 를 API Management 서비스 인스턴스의 이름으로 바꿉니다.
* `{new logger name}` 을 새 로거에 대하여 원하는 이름으로 바꿉니다. [log-to-eventhub](/azure/api-management/api-management-advanced-policies#log-to-eventhub) 정책을 구성할 때 이 이름을 참조하게 됩니다.

요청에 다음 헤더를 추가합니다.

* 콘텐츠 형식 : 애플리케이션/json
* 권한 부여: SharedAccessSignature 58...
  * `SharedAccessSignature` 생성에 대한 지침은 [Azure API Management REST API 인증](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication)을 참조하세요.

다음 템플릿을 사용하여 요청 본문을 지정합니다.

```json
{
  "loggerType" : "AzureEventHub",
  "description" : "Sample logger description",
  "credentials" : {
    "name" : "Name of the Event Hub from the portal",
    "connectionString" : "Endpoint=Event Hub Sender connection string"
    }
}
```

* `loggerType`은 `AzureEventHub`로 설정해야 합니다.
* `description`는 로거에 대한 선택적 설명을 제공하고 원하는 경우 길이가 0인 문자열이 될 수 있습니다.
* `credentials`는 Azure 이벤트 허브의 `name` 및 `connectionString`을 포함합니다.

요청을 만들 때 로거가 생성되면 상태 코드 `201 Created`가 반환됩니다. 위의 샘플 요청을 기준으로 하는 샘플 응답은 다음과 같습니다.

```json
{
    "id": "/loggers/{new logger name}",
    "loggerType": "azureEventHub",
    "description": "Sample logger description",
    "credentials": {
        "name": "Name of the Event Hub from the Portal",
        "connectionString": "{{Logger-Credentials-xxxxxxxxxxxxxxx}}"
    },
    "isBuffered": true,
    "resourceId": null
}
```

> [!NOTE]
> 다른 가능한 반환 코드 및 해당 이유의 경우 [로거 만들기](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity#PUT)를 참조하세요. 목록, 업데이트, 삭제 등의 다른 작업을 수행하는 방법을 보려면 [로거](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) 엔터티 설명서를 참조하세요.
>
>

## <a name="configure-log-to-eventhubs-policies"></a>log-to-eventhubs 정책 구성

API Management에 로거가 구성되면 원하는 이벤트를 기록하는 log-to-eventhubs 정책을 구성할 수 있습니다. log-to-eventhubs 정책은 인바운드 정책 섹션 또는 아웃바운드 정책 섹션에서 사용할 수 있습니다.

1. APIM 인스턴스로 이동합니다.
2. API 탭을 선택합니다.
3. 정책을 추가하려는 API를 선택합니다. 이 예제에서는 **무제한** 제품의 **Echo API**에 정책을 추가합니다.
4. **모든 작업**을 선택합니다.
5. 화면 맨 위에서 디자인 탭을 선택합니다.
6. 인바운드 또는 아웃바운드 처리 창에서 삼각형(연필 옆에 있는)을 클릭합니다.
7. 코드 편집기를 선택합니다. 자세한 내용은 [정책을 설정하거나 편집하는 방법](set-edit-policies.md)을 참조하세요.
8. 커서를 `inbound` 또는 `outbound` 정책 섹션에 둡니다.
9. 오른쪽 창에서 **고급 정책** > **EventHub에 대한 로그**를 선택합니다. 그러면 `log-to-eventhub` 정책 문 템플릿을 삽입합니다.

```xml
<log-to-eventhub logger-id ='logger-id'>
  @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
</log-to-eventhub>
```
이전 단계에서 `logger-id`를 URL에서 `{new logger name}`에 사용한 값으로 바꾸어 로거를 만듭니다.

문자열을 `log-to-eventhub` 요소에 대한 값으로 반환하는 모든 식을 사용할 수 있습니다. 이 예제에서는 날짜 및 시간, 서비스 이름, 요청 ID, 요청 IP 주소 및 작업 이름을 포함하는 문자열이 기록됩니다.

**저장** 을 클릭하여 업데이트된 정책 구성을 저장합니다. 저장되는 즉시 정책이 활성화되며 지정된 이벤트 허브에 이벤트가 기록됩니다.

## <a name="next-steps"></a>다음 단계
* Azure Event Hubs에 대해 자세히 알아보기
  * [Azure Event Hubs 시작](../event-hubs/event-hubs-c-getstarted-send.md)
  * [EventProcessorHost를 사용하여 메시지 수신](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Event Hubs 프로그래밍 가이드](../event-hubs/event-hubs-programming-guide.md)
* API Management 및 Event Hubs 통합에 대해 자세히 알아보기
  * [로거 엔터티 참조](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)
  * [log-to-eventhub 정책 참조](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#log-to-eventhub)
  * [Azure API Management, Event Hubs 및 Moesif를 사용하여 API 모니터링](api-management-log-to-eventhub-sample.md)  
* [Azure Application Insights와의 통합](api-management-howto-app-insights.md)에 대해 자세히 알아보기

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png
