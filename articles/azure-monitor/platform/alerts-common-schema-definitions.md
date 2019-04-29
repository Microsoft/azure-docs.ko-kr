---
title: 웹 후크/논리 앱/a z 함수/Automation Runbook에 대 한 일반적인 경고 스키마 정의
description: 웹 후크/논리 앱/a z 함수/Automation Runbook에 대 한 일반적인 경고 스키마 정의 이해합니다.
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: e29a1f5d1e258ab66540010dc12f9326b8fd57a2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60775749"
---
# <a name="common-alert-schema-definitions"></a>일반 경고 스키마 정의

이 문서에 설명 합니다 [일반적인 경고 스키마 정의](https://aka.ms/commonAlertSchemaDocs) 웹 후크/논리 앱/a z 함수/Automation Runbook에 대 한 합니다. 

## <a name="overview"></a>개요

모든 경고 인스턴스를 설명 **영향을 받은 리소스** 및 **경고를 발생 시킨**, 하며 이러한 인스턴스는 다음 섹션에서 일반적인 스키마에 설명 되어 있습니다.
* **Essentials**: 집합이 **필드를 표준화**설명 하는 모든 경고 유형에 대해 공통 **어떤 리소스** 경고는에 경고 추가 메타 데이터 (예: 심각도 또는 설명)이 일반적인와 함께 합니다. 
* **경고 컨텍스트**: 설명 하는 필드의 집합을 **경고의 원인인**, 달라 지는 필드를 사용 하 여 **경고 유형에 따라**합니다. 예를 들어, 있지만 활동 로그 경고는 경고를 생성 하는 이벤트에 대 한 정보는 메트릭 경고 메트릭 이름 및 경고 컨텍스트에 메트릭 값과 같은 필드 해야 합니다. 

##### <a name="sample-alert-payload"></a>샘플 경고 페이로드
```json
{
  "schemaId": "azureMonitorCommonAlertSchema",
  "data": {
    "essentials": {
      "alertId": "/subscriptions/<subscription ID>/providers/Microsoft.AlertsManagement/alerts/b9569717-bc32-442f-add5-83a997729330",
      "alertRule": "WCUS-R2-Gen2",
      "severity": "Sev3",
      "signalType": "Metric",
      "monitorCondition": "Resolved",
      "monitoringService": "Platform",
      "alertTargetIDs": [
        "/subscriptions/<subscription ID>/resourcegroups/pipelinealertrg/providers/microsoft.compute/virtualmachines/wcus-r2-gen2"
      ],
      "originAlertId": "3f2d4487-b0fc-4125-8bd5-7ad17384221e_PipeLineAlertRG_microsoft.insights_metricAlerts_WCUS-R2-Gen2_-117781227",
      "firedDateTime": "2019-03-22T13:58:24.3713213Z",
      "resolvedDateTime": "2019-03-22T14:03:16.2246313Z",
      "description": "",
      "essentialsVersion": "1.0",
      "alertContextVersion": "1.0"
    },
    "alertContext": {
      "properties": null,
      "conditionType": "SingleResourceMultipleMetricCriteria",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Percentage CPU",
            "metricNamespace": "Microsoft.Compute/virtualMachines",
            "operator": "GreaterThan",
            "threshold": "25",
            "timeAggregation": "Average",
            "dimensions": [
              {
                "name": "ResourceId",
                "value": "3efad9dc-3d50-4eac-9c87-8b3fd6f97e4e"
              }
            ],
            "metricValue": 7.727
          }
        ]
      }
    }
  }
}
```

## <a name="essentials-fields"></a>'Essentials' 필드

| 필드 | 설명|
|:---|:---|
| alertId | 경고 인스턴스를 고유 하 게 식별 하는 GUID입니다. |
| alertRule | 경고 인스턴스를 생성 하는 경고 규칙의 이름입니다. |
| 심각도 | 경고의 심각도입니다. 가능한 값은 다음과 같습니다. Sev0, Sev1, Sev2, Sev3, Sev4 |
| signalType | 경고 규칙 정의한 신호를 식별 합니다. 가능한 값은 다음과 같습니다. 메트릭, 로그, 활동 로그 |
| monitorCondition | 경고가 발생할 때 경고의 모니터 조건이 '발생 한'으로 설정 됩니다. 경고 발생의 원인이 된 기본 조건을 지워질 때 모니터 조건이 '해결'로 설정 됩니다.   |
| monitoringService | 모니터링 서비스 또는 솔루션은 경고를 생성 합니다. 경고 컨텍스트에 대 한 필드는 모니터링 서비스에 의해 지정 됩니다. |
| alertTargetIds | 경고의 ARM Id 모든 영향을 받는 대상 목록입니다. 로그 경고는 Log Analytics 작업 영역 또는 Application Insights 인스턴스 정의 해당 작업 영역/응용 프로그램입니다. |
| originAlertId | 모니터링 하는 것이 서비스에서 생성 된 경고 인스턴스 ID입니다. |
| firedDateTime | Utc에서 경고 인스턴스가 발생 된 날짜 시간 |
| resolvedDateTime | 경고 인스턴스에 대 한 모니터 조건이 '해결' utc에서로 설정 되었을 때의 날짜 시간입니다. 현재 메트릭 경고에만 적용 됩니다.|
| description | 경고 규칙에 정의 된 대로 설명 |
|essentialsVersion| Essentials 섹션에 대 한 버전 번호입니다.|
|alertContextVersion | AlertContext 섹션에 대 한 버전 번호 |

##### <a name="sample-values"></a>샘플 값
```json
{
  "essentials": {
    "alertId": "/subscriptions/<subscription ID>/providers/Microsoft.AlertsManagement/alerts/b9569717-bc32-442f-add5-83a997729330",
    "alertRule": "Contoso IT Metric Alert",
    "severity": "Sev3",
    "signalType": "Metric",
    "monitorCondition": "Fired",
    "monitoringService": "Platform",
    "alertTargetIDs": [
      "/subscriptions/<subscription ID>/resourceGroups/aimon-rg/providers/Microsoft.Insights/components/ai-orion-int-fe"
    ],
    "originAlertId": "74ff8faa0c79db6084969cf7c72b0710e51aec70b4f332c719ab5307227a984f",
    "firedDateTime": "2019-03-26T05:25:50.4994863Z",
    "description": "Test Metric alert",
    "essentialsVersion": "1.0",
    "alertContextVersion": "1.0"
  }
}
```

## <a name="alert-context-fields"></a>'경고 컨텍스트' 필드

### <a name="metric-alerts"></a>메트릭 경고

#### <a name="monitoringservice--platform"></a>monitoringService = 'Platform'

##### <a name="sample-values"></a>샘플 값
```json
{
  "alertContext": {
      "properties": null,
      "conditionType": "SingleResourceMultipleMetricCriteria",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Percentage CPU",
            "metricNamespace": "Microsoft.Compute/virtualMachines",
            "operator": "GreaterThan",
            "threshold": "25",
            "timeAggregation": "Average",
            "dimensions": [
              {
                "name": "ResourceId",
                "value": "3efad9dc-3d50-4eac-9c87-8b3fd6f97e4e"
              }
            ],
            "metricValue": 31.1105
          }
        ],
        "windowStartTime": "2019-03-22T13:40:03.064Z",
        "windowEndTime": "2019-03-22T13:45:03.064Z"
      }
    }
}
```

### <a name="log-alerts"></a>로그 경고

> [!NOTE]
> 사용자 지정 JSON 옵션을 사용 하 여 기존 로그 경고에 대 한 공통 스키마에서 사용자 지정 유지 되지 않습니다.

#### <a name="monitoringservice--log-analytics"></a>monitoringService = 'Log Analytics'

##### <a name="sample-values"></a>샘플 값
```json
{
  "alertContext": {
      "SearchQuery": "search * \n| where Type == \"Heartbeat\" \n| where Category == \"Direct Agent\" \n| where TimeGenerated > ago(30m) ",
      "SearchIntervalStartTimeUtc": "3/22/2019 1:36:31 PM",
      "SearchIntervalEndtimeUtc": "3/22/2019 1:51:31 PM",
      "ResultCount": 15,
      "LinkToSearchResults": "https://portal.azure.com#@72f988bf-86f1-41af-91ab-2d7cd011db47/blade/Microsoft_OperationsManagementSuite_Workspace/AnalyticsBlade/initiator/AnalyticsShareLinkToQuery/isQueryEditorVisible/true/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%<subscription ID>%2FresourceGroups%2Fpipelinealertrg%2Fproviders%2FMicrosoft.OperationalInsights%2Fworkspaces%2FINC-OmsAlertRunner%22%7D%5D%7D/query/search%20%2A%20%0A%7C%20where%20Type%20%3D%3D%20%22Heartbeat%22%20%0A%7C%20where%20Category%20%3D%3D%20%22Direct%20Agent%22%20%0A%7C%20where%20TimeGenerated%20%3E%20%28datetime%282019-03-22T13%3A51%3A31.0000000%29%20-%2030m%29%20%20/isQuerybase64Compressed/false/timespanInIsoFormat/2019-03-22T13%3a36%3a31.0000000Z%2f2019-03-22T13%3a51%3a31.0000000Z",
      "SeverityDescription": "Warning",
      "WorkspaceId": "2a1f50a7-ef97-420c-9d14-938e77c2a929",
      "SearchIntervalDurationMin": "15",
      "AffectedConfigurationItems": [
        "INC-Gen2Alert"
      ],
      "SearchIntervalInMinutes": "15",
      "Threshold": 10000,
      "Operator": "Less Than"
    }
}
```

#### <a name="monitoringservice--application-insights"></a>monitoringService = 'Application Insights'

##### <a name="sample-values"></a>샘플 값
```json
{
  "alertContext": {
      "SearchQuery": "search *",
      "SearchIntervalStartTimeUtc": "3/22/2019 1:36:33 PM",
      "SearchIntervalEndtimeUtc": "3/22/2019 1:51:33 PM",
      "ResultCount": 0,
      "LinkToSearchResults": "https://portal.azure.com#@72f988bf-86f1-41af-91ab-2d7cd011db47/blade/Microsoft_OperationsManagementSuite_Workspace/AnalyticsBlade/initiator/AnalyticsShareLinkToQuery/isQueryEditorVisible/true/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%<subscription ID>%2FresourceGroups%2FPipeLineAlertRG%2Fproviders%2Fmicrosoft.insights%2Fcomponents%2FWEU-AIRunner%22%7D%5D%7D/query/search%20%2A/isQuerybase64Compressed/false/timespanInIsoFormat/2019-03-22T13%3a36%3a33.0000000Z%2f2019-03-22T13%3a51%3a33.0000000Z",
      "SearchIntervalDurationMin": "15",
      "SearchIntervalInMinutes": "15",
      "Threshold": 10000,
      "Operator": "Less Than",
      "ApplicationId": "8e20151d-75b2-4d66-b965-153fb69d65a6"
    }
}
```

### <a name="activity-log-alerts"></a>활동 로그 경고

#### <a name="monitoringservice--activity-log---administrative"></a>monitoringService = ' 활동 로그-관리 '

##### <a name="sample-values"></a>샘플 값
```json
{
  "alertContext": {
      "authorization": {
        "action": "Microsoft.Compute/virtualMachines/restart/action",
        "scope": "/subscriptions/<subscription ID>/resourceGroups/PipeLineAlertRG/providers/Microsoft.Compute/virtualMachines/WCUS-R2-ActLog"
      },
      "channels": "Operation",
      "claims": "{\"aud\":\"https://management.core.windows.net/\",\"iss\":\"https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/\",\"iat\":\"1553260826\",\"nbf\":\"1553260826\",\"exp\":\"1553264726\",\"aio\":\"42JgYNjdt+rr+3j/dx68v018XhuFAwA=\",\"appid\":\"e9a02282-074f-45cf-93b0-50568e0e7e50\",\"appidacr\":\"2\",\"http://schemas.microsoft.com/identity/claims/identityprovider\":\"https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/\",\"http://schemas.microsoft.com/identity/claims/objectidentifier\":\"9778283b-b94c-4ac6-8a41-d5b493d03aa3\",\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier\":\"9778283b-b94c-4ac6-8a41-d5b493d03aa3\",\"http://schemas.microsoft.com/identity/claims/tenantid\":\"72f988bf-86f1-41af-91ab-2d7cd011db47\",\"uti\":\"v5wYC9t9ekuA2rkZSVZbAA\",\"ver\":\"1.0\"}",
      "caller": "9778283b-b94c-4ac6-8a41-d5b493d03aa3",
      "correlationId": "8ee9c32a-92a1-4a8f-989c-b0ba09292a91",
      "eventSource": "Administrative",
      "eventTimestamp": "2019-03-22T13:56:31.2917159+00:00",
      "eventDataId": "161fda7e-1cb4-4bc5-9c90-857c55a8f57b",
      "level": "Informational",
      "operationName": "Microsoft.Compute/virtualMachines/restart/action",
      "operationId": "310db69b-690f-436b-b740-6103ab6b0cba",
      "status": "Succeeded",
      "subStatus": "",
      "submissionTimestamp": "2019-03-22T13:56:54.067593+00:00"
    }
}
```

## <a name="next-steps"></a>다음 단계

- [일반 경고 스키마에 자세히 알아보기](https://aka.ms/commonAlertSchemaDocs)


