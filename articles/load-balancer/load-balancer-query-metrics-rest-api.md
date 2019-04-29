---
title: REST API를 사용하여 메트릭 검색
titlesuffix: Azure Load Balancer
description: Azure REST API를 사용하여 특정 시간 및 날짜 범위의 Load Balancer 상태 및 사용률 메트릭을 수집합니다.
services: sql-database
author: KumudD
ms.reviewer: routlaw
manager: jeconnoc
ms.service: load-balancer
ms.custom: REST, seodec18
ms.topic: article
ms.date: 06/06/2017
ms.author: KumudD
ms.openlocfilehash: 9f5206ef5348ee8fd7b3fe981a9cfe4afc1367fb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60734544"
---
# <a name="get-load-balancer-utilization-metrics-using-the-rest-api"></a>REST API를 사용하여 Load Balancer 사용률 메트릭 얻기

이 방법 문서에서는 [Azure REST API](/rest/api/azure/)를 사용하여 [표준 Load Balancer](/azure/load-balancer/load-balancer-standard-overview)가 시간 간격 동안 처리하는 바이트 수를 수집하는 방법을 보여줍니다.

REST API에 대한 전체 참조 설명서 및 추가 샘플은 [Azure Monitor REST 참조](/rest/api/monitor)에서 사용할 수 있습니다. 

## <a name="build-the-request"></a>요청 빌드

다음 GET 요청을 사용하여 표준 Load Balancer의 [ByteCount 메트릭](/azure/load-balancer/load-balancer-standard-diagnostics#multi-dimensional-metrics)을 수집합니다. 

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=ByteCount&timespan=2018-06-05T03:00:00Z/2018-06-07T03:00:00Z
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
| resourceGroupName | 리소스를 포함하는 리소스 그룹의 이름입니다. Azure Resource Manager API, CLI 또는 포털에서 이 값을 얻을 수 있습니다. |
| loadBalancerName | Azure Load Balancer의 이름입니다. |
| metricnames | 쉼표로 구분된 유효한 [Load Balancer 메트릭](/azure/load-balancer/load-balancer-standard-diagnostics) 목록입니다. |
| api-version | 요청에 사용할 API 버전입니다.<br /><br /> 이 문서에서는 위 URL에 포함되어 있는 api-version `2018-01-01`을 다룹니다.  |
| timespan | 쿼리의 시간 범위입니다. `startDateTime_ISO/endDateTime_ISO` 형식의 문자열입니다. 이 선택적 매개 변수는 예제에서 하루 동안의 데이터를 반환하도록 설정되어 있습니다. |
| &nbsp; | &nbsp; |

### <a name="request-body"></a>요청 본문

이 작업에는 요청 본문이 필요하지 않습니다.

## <a name="handle-the-response"></a>응답 처리

상태 코드 200은 메트릭 값 목록이 성공적으로 반환되면 반환됩니다. 오류 코드의 전체 목록은 [참조 설명서](/rest/api/monitor/metrics/list#errorresponse)에서 사용할 수 있습니다.

## <a name="example-response"></a>예제 응답 

```json
{
    "cost": 0,
    "timespan": "2018-06-05T03:00:00Z/2018-06-07T03:00:00Z",
    "interval": "PT1M",
    "value": [
        {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/loadBalancers/{loadBalancerName}/providers/Microsoft.Insights/metrics/ByteCount",
            "type": "Microsoft.Insights/metrics",
            "name": {
                "value": "ByteCount",
                "localizedValue": "Byte Count"
            },
            "unit": "Count",
            "timeseries": [
                {
                    "metadatavalues": [],
                    "data": [
                        {
                            "timeStamp": "2018-06-06T17:24:00Z",
                            "total": 1067921034.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:25:00Z",
                            "total": 0.0
                        },
                        {
                            "timeStamp": "2018-06-06T17:26:00Z",
                            "total": 3781344.0
                        },
                    ]
                }
            ]
        }
    ],
    "namespace": "Microsoft.Network/loadBalancers",
    "resourceregion": "eastus"
}
```
