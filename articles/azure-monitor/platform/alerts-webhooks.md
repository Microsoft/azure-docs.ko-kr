---
title: 웹후크를 사용하여 비 Azure 시스템을 알리도록 클래식 메트릭 경고 설정
description: Azure 메트릭 경고를 다른 비 Azure 시스템으로 다시 라우팅하는 방법을 알아봅니다.
author: snehithm
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/03/2017
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 264f3eb042a3c29523ed93df93dfa6d45c00ae87
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60345784"
---
# <a name="have-a-classic-metric-alert-notify-a-non-azure-system-using-a-webhook"></a>웹후크를 사용하여 비 Azure 시스템을 알리도록 클래식 메트릭 경고 설정
웹후크를 사용하면 사후 처리 또는 사용자 지정 작업을 위해 Azure 경고 알림을 다른 시스템으로 라우팅할 수 있습니다. SMS 메시지 보내기, 버그 기록, 채팅/메시징 서비스를 통한 팀 알림 또는 다양한 다른 작업 수행 등을 처리하는 서비스에 라우팅하도록 웹후크를 경고에 사용할 수 있습니다. 

이 아티클에서는 Azure 메트릭 경고에 웹후크를 설정하는 방법을 설명합니다. 웹후크에 대한 HTTP POST의 페이로드 형태도 보여 줍니다. Azure 활동 로그 경고(이벤트에 대한 경고)에 대한 설정과 스키마에 대한 자세한 내용은 [Azure 활동 로그 경고에서 웹후크 호출](alerts-log-webhook.md)을 참조하세요.

Azure 경고는 HTTP POST를 사용하여 JSON 형식의 경고 콘텐츠를 이 경고를 만들 때 입력한 웹후크 URI로 보냅니다. 이 스키마는 이 아티클의 뒷부분에 정의되어 있습니다. 이 URI의 HTTP 또는 HTTPS 엔드포인트는 유효해야 합니다. 경고가 활성화되면 Azure에서 요청당 항목 하나만 게시합니다.

## <a name="configure-webhooks-via-the-azure-portal"></a>Azure Portal을 통해 웹후크 구성
웹후크 URI를 추가하거나 업데이트하려면 [Azure Portal](https://portal.azure.com/)에서 **경고 만들기/업데이트**로 이동합니다.

![경고 규칙 추가 창](./media/alerts-webhooks/Alertwebhook.png)

또한 [Azure PowerShell cmdlet](../../azure-monitor/platform/powershell-quickstart-samples.md#create-metric-alerts), [플랫폼 간 CLI](../../azure-monitor/platform/cli-samples.md#work-with-alerts) 또는 [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn933805.aspx)를 사용하여 웹후크 URI에 게시할 경고를 구성할 수 있습니다.

## <a name="authenticate-the-webhook"></a>웹후크 인증
웹후크는 토큰 기반 인증을 사용하여 인증할 수 있습니다. 웹후크 URI는 토큰 ID를 사용하여 저장됩니다. 예: `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue`

## <a name="payload-schema"></a>페이로드 스키마
POST 작업에는 모든 메트릭 기반 경고에 대해 다음과 같은 JSON 페이로드와 스키마가 포함됩니다.

```JSON
{
    "status": "Activated",
    "context": {
        "timestamp": "2015-08-14T22:26:41.9975398Z",
        "id": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.insights/alertrules/ruleName1",
        "name": "ruleName1",
        "description": "some description",
        "conditionType": "Metric",
        "condition": {
            "metricName": "Requests",
            "metricUnit": "Count",
            "metricValue": "10",
            "threshold": "10",
            "windowSize": "15",
            "timeAggregation": "Average",
            "operator": "GreaterThanOrEqual"
        },
        "subscriptionId": "s1",
        "resourceGroupName": "useast",
        "resourceName": "mysite1",
        "resourceType": "microsoft.foo/sites",
        "resourceId": "/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1",
        "resourceRegion": "centralus",
        "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/useast/providers/microsoft.foo/sites/mysite1"
    },
    "properties": {
        "key1": "value1",
        "key2": "value2"
    }
}
```


| 필드 | 필수 | 고정된 값 집합 | 메모 |
|:--- |:--- |:--- |:--- |
| status |Y |Activated, Resolved |설정한 조건을 기반으로 하는 경고에 대한 상태입니다. |
| context |Y | |경고 컨텍스트입니다. |
| timestamp |Y | |경고가 트리거된 시점의 시간입니다. |
| id |Y | |모든 경고 규칙에는 고유한 ID가 있습니다. |
| name |Y | |경고 이름입니다. |
| description |Y | |경고에 대한 설명입니다. |
| conditionType |Y |Metric, Event |metric과 event라는 두 형식의 경고가 지원됩니다. 메트릭 경고는 메트릭 조건을 기반으로 합니다. 이벤트 경고는 활동 로그의 이벤트를 기반으로 합니다. 이 값을 사용하여 경고가 메트릭 또는 이벤트를 기반으로 하는지 확인하세요. |
| condition |Y | |**conditionType** 값에 기반하여 확인할 특정 필드입니다. |
| metricName |메트릭 경고의 경우 | |규칙은 모니터링을 정의하는 메트릭의 이름입니다. |
| metricUnit |메트릭 경고의 경우 |Bytes, BytesPerSecond, Count, CountPerSecond, Percent, Seconds |메트릭에 사용되는 단위입니다. [허용되는 값](https://msdn.microsoft.com/library/microsoft.azure.insights.models.unit.aspx)을 참조하세요. |
| metricValue |메트릭 경고의 경우 | |경고를 발생시킨 메트릭의 실제 값입니다. |
| threshold |메트릭 경고의 경우 | |경고가 활성화되는 임계값입니다. |
| windowSize |메트릭 경고의 경우 | |임계값에 기반하여 경보 활동을 모니터링하는 데 사용되는 기간입니다. 값은 5분에서 1일 사이여야 합니다. 값은 ISO 8601 기간 형식이어야 합니다. |
| timeAggregation |메트릭 경고의 경우 |Average, Last, Maximum, Minimum, None, Total |데이터가 수집되는 방법은 시간이 지남에 따라 결합되어야 합니다. 기본값은 평균입니다. [허용되는 값](https://msdn.microsoft.com/library/microsoft.azure.insights.models.aggregationtype.aspx)을 참조하세요. |
| operator |메트릭 경고의 경우 | |현재 메트릭 데이터를 설정한 임계값과 비교하는 데 사용되는 연산자입니다. |
| subscriptionId |Y | |Azure 구독 ID입니다. |
| resourceGroupName |Y | |영향을 받는 리소스의 리소스 그룹 이름입니다. |
| resourceName |Y | |영향을 받는 리소스의 리소스 이름입니다. |
| resourceType |Y | |영향을 받는 리소스의 리소스 종류입니다. |
| resourceId |Y | |영향을 받는 리소스의 리소스 ID입니다. |
| resourceRegion |Y | |영향을 받는 리소스의 지역 또는 위치입니다. |
| portalLink |Y | |포털 리소스 요약 페이지에 대한 직접 링크입니다. |
| properties |N |옵션 |이벤트에 대한 세부 정보를 포함하는 키/값 쌍의 집합입니다. 예: `Dictionary<String, String>`. 속성 필드는 선택 사항입니다. 사용자 지정 UI 또는 논리 앱 기반 워크플로에서 페이로드를 통해 전달될 수 있는 키/값 쌍을 입력할 수 있습니다. 사용자 지정 속성을 웹후크에 다시 전달할 대체 방법은 웹후크 URI 자체를 통하는 것입니다(쿼리 매개 변수로). |

> [!NOTE]
> [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn933805.aspx)를 사용하여 **properties** 필드만 설정할 수 있습니다.
>
>

## <a name="next-steps"></a>다음 단계
* [Azure 경고와 PagerDuty의 통합](https://go.microsoft.com/fwlink/?LinkId=627080) 비디오에서 Azure 경고와 웹후크에 대해 자세히 알아봅니다.
* [Azure 경고에 대한 Azure Automation 스크립트(Runbook) 실행](https://go.microsoft.com/fwlink/?LinkId=627081) 방법을 알아봅니다.
* [논리 앱을 사용하여 Azure 경고에서 Twilio를 통해 SMS 메시지 보내기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app) 방법을 알아봅니다.
* [논리 앱을 사용하여 Azure 경고에서 Slack 메시지 보내기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app) 방법을 알아봅니다.
* [논리 앱을 사용하여 Azure 경고에서 Azure 큐에 메시지 보내기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app) 방법을 알아봅니다.

