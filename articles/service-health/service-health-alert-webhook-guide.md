---
title: 웹후크를 사용하여 기존 문제 관리 시스템에 대한 상태 알림 구성 | Microsoft Docs
description: 기존 문제 관리 시스템에 서비스 상태 이벤트에 대한 개인 설정 알림 가져오기
author: stephbaron
ms.author: stbaron
ms.topic: conceptual
ms.service: service-health
ms.workload: Supportability
ms.date: 3/27/2018
ms.openlocfilehash: 69b142cd46c006e562218c949fb450864589a661
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622161"
---
# <a name="configure-health-notifications-for-existing-problem-management-systems-using-a-webhook"></a>웹후크를 사용하여 기존 문제 관리 시스템에 대한 상태 알림 구성

이 문서에서는 웹후크를 통해 기존 알림 시스템에 데이터를 보내도록 서비스 상태 경고를 구성하는 방법을 보여 줍니다.

현재 Azure Service 인시던트의 영향을 받는 경우 문자 메시지나 이메일을 통해 알림을 받을 수 있도록 서비스 상태 경고를 구성할 수 있습니다.
그러나 사용하려는 위치에 이미 기존 외부 알림 시스템이 있을 수 있습니다.
이 문서에서는 웹후크 페이로드의 가장 중요한 부분과 서비스 문제에 영향을 받을 때 알림을 받을 수 있는 사용자 지정 경고를 만드는 방법을 보여 줍니다.

미리 구성된 통합을 사용하려면 다음 방법을 참조하세요.
* [ServiceNow를 사용하여 경고 구성](service-health-alert-webhook-servicenow.md)
* [PagerDuty를 사용하여 경고 구성](service-health-alert-webhook-pagerduty.md)
* [OpsGenie를 사용하여 경고 구성](service-health-alert-webhook-opsgenie.md)

## <a name="configuring-a-custom-notification-using-the-service-health-webhook-payload"></a>서비스 상태 웹후크 페이로드를 사용하여 사용자 지정 알림 구성
고유의 사용자 지정 웹후크 통합을 설정하려면 서비스 상태 알림 중 전송되는 JSON 페이로드를 구문 분석해야 합니다.

`ServiceHealth` 웹후크 페이로드에 대한 [예제를 보려면 여기](../azure-monitor/platform/activity-log-alerts-webhook.md)를 확인합니다.

`context.eventSource == "ServiceHealth"`를 보면 서비스 상태 경고인지 검색할 수 있습니다. 여기에서 수집할 가장 연관된 속성은 다음과 같습니다.
 * `data.context.activityLog.status`
 * `data.context.activityLog.level`
 * `data.context.activityLog.subscriptionId`
 * `data.context.activityLog.properties.title`
 * `data.context.activityLog.properties.impactStartTime`
 * `data.context.activityLog.properties.communication`
 * `data.context.activityLog.properties.impactedServices`
 * `data.context.activityLog.properties.trackingId`

## <a name="creating-a-direct-link-to-the-service-health-dashboard-for-an-incident"></a>인시던트의 Service Health 대시보드에 대한 직접 링크 만들기
특수한 URL을 생성하여 데스크톱 또는 모바일에 Service Health 대시보드에 대한 직접 링크를 만들 수 있습니다. `subscriptionId`의 처음 및 마지막 세 자리와 `trackingId`를 사용하여 다음과 같이 만듭니다.
```
https://app.azure.com/h/<trackingId>/<first and last three digits of subscriptionId>
```

예를 들어 `subscriptionId`가 `bba14129-e895-429b-8809-278e836ecdb3`이고 `trackingId`가 `0DET-URB`인 경우 Service Health URL은 다음과 같습니다.

```
https://app.azure.com/h/0DET-URB/bbadb3
```

## <a name="using-the-level-to-detect-the-severity-of-the-issue"></a>수준을 사용하여 문제의 심각도 검색
가장 낮은 심각도에서 가장 높은 심각도까지 페이로드의 `level` 속성은 `Informational`, `Warning`, `Error` 및 `Critical` 중 하나일 수 있습니다.

## <a name="parsing-the-impacted-services-to-understand-the-full-scope-of-the-incident"></a>영향을 받는 서비스를 구문 분석하여 인시던트의 전체 범위 이해
서비스 상태 경고는 여러 지역 및 여러 서비스에서 문제에 대해 알릴 수 있습니다. 전체 세부 정보를 얻으려면 `impactedServices` 값을 구문 분석해야 합니다.
콘텐츠 내에 [JSON 이스케이프된](https://json.org/) 문자열이 있으며 이스케이프되지 않으면 정기적으로 구문 분석할 수 있는 다른 JSON 개체가 포함됩니다.

```json
{"data.context.activityLog.properties.impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"},{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"Alerts & Metrics\"},{\"ImpactedRegions\":[{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"App Service\"}]"}
```

다음과 같이 됩니다.

```json
[
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia East"
         },
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"Alerts & Metrics"
   },
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"App Service"
   }
]
```

Australia East 및 Southeast에서 "Alerts & Metrics"에 문제가 있고 Australia Southeast에서 "App Service"에 문제가 있음을 보여 줍니다.


## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>HTTP POST 요청을 통해 웹후크 통합 테스트
1. 보낼 서비스 상태 페이로드를 만듭니다. [Azure 활동 로그 경고에 대한 웹후크](../azure-monitor/platform/activity-log-alerts-webhook.md)에서 예제 서비스 상태 웹후크 페이로드를 찾을 수 있습니다.

2. 다음과 같이 HTTP POST 요청을 만듭니다.

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
3. `2XX - Successful` 응답이 표시됩니다.

4. [PagerDuty](https://www.pagerduty.com/)로 이동하여 통합이 성공적으로 설정되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계
- [활동 로그 경고 웹후크 스키마](../azure-monitor/platform/activity-log-alerts-webhook.md)를 검토하세요. 
- [서비스 상태 알림](../azure-monitor/platform/service-notifications.md)에 대해 자세히 알아보세요.
- [작업 그룹](../azure-monitor/platform/action-groups.md)에 대해 자세히 알아보세요.