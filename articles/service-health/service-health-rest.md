---
title: REST API를 사용하여 Azure 리소스 상태 이벤트 가져오기 | Microsoft Docs
description: Azure REST API를 사용하여 Azure 리소스의 상태 이벤트를 가져오는 방법을 설명합니다.
author: stephbaron
ms.author: stbaron
ms.service: service-health
ms.custom: REST
ms.topic: article
ms.date: 06/06/2017
ms.openlocfilehash: 6d83aed6910127ceb34b9a694f48ca9c19ab6d18
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60790915"
---
# <a name="get-resource-health-using-the-rest-api"></a>REST API를 사용하여 리소스 상태 가져오기 

이 예제 문서에서는 [Azure REST API](/rest/api/azure/)를 사용하여 구독의 Azure 리소스에 대한 상태 이벤트 목록을 검색하는 방법을 보여 줍니다.

REST API에 대한 전체 참조 설명서 및 추가 샘플은 [Azure Monitor REST 참조](/rest/api/monitor)에서 사용할 수 있습니다. 

## <a name="build-the-request"></a>요청 빌드

`2018-05-16` 및 `2018-06-20` 사이의 시간 범위에서 구독에 대한 상태 이벤트를 나열하려면 다음 `GET` HTTP 요청을 사용합니다.

```http
https://management.azure.com/subscriptions/{subscription-id}/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&%24filter=eventTimestamp%20ge%20'2018-05-16T04%3A36%3A37.6407898Z'%20and%20eventTimestamp%20le%20'2018-06-20T04%3A36%3A37.6407898Z'
```

### <a name="request-headers"></a>헤더 요청

다음과 같은 헤더가 필요합니다. 

|요청 헤더|설명|  
|--------------------|-----------------|  
|*Content-Type:*|필수 사항입니다. `application/json`로 설정합니다.|  
|*권한 부여*|필수 사항입니다. 유효한 `Bearer` [액세스 토큰](/rest/api/azure/#authorization-code-grant-interactive-clients)으로 설정합니다. |  

### <a name="uri-parameters"></a>URI 매개 변수

| 이름 | 설명 |
| :--- | :---------- |
| subscriptionId | Azure 구독을 식별하는 구독 ID입니다. 구독이 여러 개인 경우 [여러 구독으로 작업](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)을 참조합니다. |
| api-version | 요청에 사용할 API 버전입니다.<br /><br /> 이 문서에서는 위 URL에 포함되어 있는 api-version `2015-04-01`을 다룹니다.  |
| $filter | 반환되는 결과 집합을 줄이는 필터링 옵션입니다. 이 매개 변수에 허용되는 패턴은 [활동 로그 작업의 참조](/rest/api/monitor/activitylogs/list#uri-parameters)에서 제공됩니다. 표시된 예제는 2018-05-16과 2018-06-20 사이의 시간 범위에서 발생한 모든 이벤트를 캡처합니다. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>요청 본문

이 작업에는 요청 본문이 필요하지 않습니다.

## <a name="handle-the-response"></a>응답 처리

상태 코드 200과 필터 매개 변수에 해당하는 상태 이벤트 값 목록, 그리고 다음 결과 페이지를 검색할 수 있는 `nextlink` URI가 반환됩니다.

## <a name="example-response"></a>예제 응답 

```json
{
  "value": [
    {
      "correlationId": "1e121103-0ba6-4300-ac9d-952bb5d0c80f",
      "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
      },
      "id": "/subscriptions/{subscription-id}/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841/events/44ade6b4-3813-45e6-ae27-7420a95fa2f8/ticks/635574752669792776",
      "resourceGroupName": "MSSupportGroup",
      "resourceProviderName": {
        "value": "microsoft.support",
        "localizedValue": "microsoft.support"
      },
      "operationName": {
        "value": "microsoft.support/supporttickets/write",
        "localizedValue": "microsoft.support/supporttickets/write"
      },
      "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
      },
      "eventTimestamp": "2015-01-21T22:14:26.9792776Z",
      "submissionTimestamp": "2015-01-21T22:14:39.9936304Z",
      "level": "Informational"
    }
  ],
  "nextLink": "https://management.azure.com/########-####-####-####-############$skiptoken=######"
}
```
