---
title: Azure Monitor를 사용하여 데이터 팩터리 모니터링
description: Azure Monitor를 사용 하 여 Data Factory 정보로 진단 로그를 사용 하도록 설정 하 여/Azure Data Factory 파이프라인을 모니터링 하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: 8325b4ef6b89a76eeec418386cec4922cb5916b1
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979159"
---
# <a name="alert-and-monitor-data-factories-by-using-azure-monitor"></a>Azure Monitor를 사용 하 여 데이터 팩터리 경고 및 모니터링

클라우드 응용 프로그램은 복잡 하며 많은 부분을 이동 하 고 있습니다. 모니터는 응용 프로그램이 정상 상태로 유지 되 고 실행 되도록 하는 데 도움이 되는 데이터를 제공 합니다. 모니터는 잠재적인 문제를 방지 하 고 이전 문제를 해결 하는 데도 도움이 됩니다.

모니터링 데이터를 사용 하 여 응용 프로그램에 대한 깊은 통찰력을 얻을 수 있습니다. 이 정보는 응용 프로그램 성능 및 유지 관리를 개선 하는 데 도움이 됩니다. 또한 수동 개입이 필요한 작업을 자동화 하는 데 도움이 됩니다.

Azure Monitor는 대부분의 Azure 서비스에 대한 기본 수준의 인프라 메트릭과 로그를 제공 합니다. Azure 진단 로그는 리소스에서 내보내며, 해당 리소스의 작업에 대한 풍부 하 고 빈번한 데이터를 제공 합니다. 및 Azure Data Factory 모니터에서 진단 로그를 기록 합니다.

자세한 내용은 [Azure Monitor 개요](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor)를 참조 하세요.

## <a name="keeping-azure-data-factory-data"></a>Azure Data Factory 데이터 유지

Data Factory은 파이프라인 실행 데이터를 45 일간만 저장 합니다. 데이터를 더 오랜 시간 동안 유지 하려면 모니터를 사용 합니다. 모니터를 사용 하 여 분석을 위해 진단 로그를 라우팅할 수 있습니다. 선택한 기간에 대한 팩터리 정보를 갖도록 저장소 계정에 보관할 수도 있습니다.

## <a name="diagnostic-logs"></a>진단 로그

* 감사 또는 수동 검사를 위해 저장소 계정에 진단 로그를 저장 합니다. 진단 설정을 사용 하 여 보존 기간 (일)을 지정할 수 있습니다.
* 로그를 Azure Event Hubs로 스트리밍합니다. 로그는 파트너 서비스 또는 Power BI와 같은 사용자 지정 분석 솔루션에 대한 입력이 됩니다.
* Log Analytics를 사용 하 여 로그를 분석 합니다.

로그를 내보내는 리소스의 구독에 없는 저장소 계정 또는 이벤트 허브 네임 스페이스를 사용할 수 있습니다. 설정을 구성 하는 사용자에 게 두 구독에 대한 적절 한 RBAC (역할 기반 액세스 제어) 액세스 권한이 있어야 합니다.

## <a name="set-up-diagnostic-logs"></a>진단 로그 설정

### <a name="diagnostic-settings"></a>진단 설정

진단 설정을 사용 하 여 비 계산 리소스에 대한 진단 로그를 구성 합니다. 리소스 컨트롤에 대한 설정에는 다음과 같은 기능이 있습니다.

* 진단 로그를 보낼 위치를 지정 합니다. 예를 들면 Azure storage 계정, Azure 이벤트 허브 또는 모니터 로그가 있습니다.
* 전송 되는 로그 범주를 지정 합니다.
* 각 로그 범주가 저장소 계정에 유지 되는 기간을 지정 합니다.
* 보존 기간이 0일이라는 것은 로그가 영원히 보관된다는 의미입니다. 그렇지 않은 경우 값은 1에서 2147483647 사이의 모든 일 수 있습니다.
* 보존 정책이 설정 되었지만 저장소 계정에 로그를 저장 하지 않은 경우 보존 정책은 적용 되지 않습니다. 예를 들어 Event Hubs 또는 모니터 로그 옵션만 선택한 경우이 문제가 발생할 수 있습니다.
* 보존 정책은 하루에 적용 됩니다. 일 사이의 경계는 UTC (협정 세계시) 자정에 발생 합니다. 하루 끝에 보존 정책을 초과 하는 일의 로그가 삭제 됩니다. 예를 들어 1 일의 보존 정책이 있는 경우 오늘부터 어제 까지의 로그를 삭제 합니다.

### <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Azure Monitor REST API를 통해 진단 로그를 사용 하도록 설정

#### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>모니터 REST API에서 진단 설정을 만들거나 업데이트 합니다.

##### <a name="request"></a>요청

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>headers

* `{api-version}`을 `2016-09-01`로 바꿉니다.
* `{resource-id}`를 진단 설정을 편집 하려는 리소스의 ID로 바꿉니다. 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/management/manage-resource-groups-portal.md)(영문)를 참조하세요.
* `Content-Type` 헤더를 `application/json`으로 설정합니다.
* 권한 부여 헤더를 Azure Active Directory (Azure AD)에서 가져온 JSON 웹 토큰으로 설정 합니다. 자세한 내용은 [요청 인증](../active-directory/develop/authentication-scenarios.md)을 참조하세요.

##### <a name="body"></a>본문

```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
}
```

| 속성 | 유형 | Description |
| --- | --- | --- |
| **storageAccountId** |String | 진단 로그를 보낼 저장소 계정의 리소스 ID입니다. |
| **serviceBusRuleId** |String | 진단 로그 스트리밍을 위해 Event Hubs 생성 하려는 service bus 네임 스페이스의 service bus 규칙 ID입니다. 규칙 ID의 형식은 `{service bus resource ID}/authorizationrules/{key name}`입니다.|
| **workspaceId** | 복합 형식 | 메트릭 시간 조직 및 해당 보존 정책의 배열입니다. 이 속성의 값이 비어 있습니다. |
|**메트릭**| 호출된 파이프라인에 전달될 파이프라인 실행의 매개 변수 값| 매개 변수 이름을 인수 값에 매핑하는 JSON 개체입니다. |
| **logs**| 복합 형식| 리소스 종류에 대한 진단 로그 범주의 이름입니다. 리소스에 대한 진단 로그 범주의 목록을 가져오려면 진단 설정 가져오기 작업을 수행 합니다. |
| **category**| String| 로그 범주 및 해당 보존 정책의 배열입니다. |
| **timeGrain** | String | ISO 8601 기간 형식으로 캡처된 메트릭의 세분성입니다. 속성 값은 1 분을 지정 하는 `PT1M`이어야 합니다. |
| **enabled**| 부울 | 이 리소스에 대해 메트릭 또는 로그 범주의 컬렉션을 사용할 수 있는지 여부를 지정 합니다. |
| **retentionPolicy**| 복합 형식| 메트릭 또는 로그 범주에 대한 보존 정책을 설명합니다. 이 속성은 저장소 계정에만 사용 됩니다. |
|**days**| Int| 메트릭 또는 로그를 유지할 일 수입니다. 속성 값이 0 이면 로그가 영구적으로 유지 됩니다. 이 속성은 저장소 계정에만 사용 됩니다. |

##### <a name="response"></a>응답

200 정상.


```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

#### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>모니터의 진단 설정에 대한 정보를 가져옵니다 REST API

##### <a name="request"></a>요청

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>headers

* `{api-version}`을 `2016-09-01`로 바꿉니다.
* `{resource-id}`를 진단 설정을 편집 하려는 리소스의 ID로 바꿉니다. 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/management/manage-resource-groups-portal.md)(영문)를 참조하세요.
* `Content-Type` 헤더를 `application/json`으로 설정합니다.
* 권한 부여 헤더를 Azure AD에서 가져온 JSON 웹 토큰으로 설정 합니다. 자세한 내용은 [요청 인증](../active-directory/develop/authentication-scenarios.md)을 참조하세요.

##### <a name="response"></a>응답

200 정상.

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}

```
자세한 내용은 [진단 설정](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)을 참조 하세요.

## <a name="schema-of-logs-and-events"></a>로그 및 이벤트의 스키마

### <a name="monitor-schema"></a>모니터 스키마

#### <a name="activity-run-log-attributes"></a>작업-실행 로그 특성

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties":
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| 속성 | 유형 | Description | 예 |
| --- | --- | --- | --- |
| **Level** |String | 진단 로그의 수준입니다. 활동 실행 로그에 대해 속성 값을 4로 설정 합니다. | `4` |
| **correlationId** |String | 특정 요청을 추적 하기 위한 고유 ID입니다. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | Timespan UTC 형식의 이벤트 시간 `YYYY-MM-DDTHH:MM:SS.00000Z`입니다. | `2017-06-28T21:00:27.3534352Z` |
|**activityRunId**| String| 작업 실행의 ID입니다. | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| String| 파이프라인 실행의 ID입니다. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| String | 데이터 팩터리 리소스와 연결 된 ID입니다. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | 진단 로그의 범주입니다. 속성 값을 `ActivityRuns`로 설정합니다. | `ActivityRuns` |
|**level**| String | 진단 로그의 수준입니다. 속성 값을 `Informational`로 설정합니다. | `Informational` |
|**operationName**| String | 상태의 작업 이름입니다. 활동이 시작 하트 비트 이면 속성 값이 `MyActivity -`됩니다. 활동이 끝 하트 비트 이면 속성 값이 `MyActivity - Succeeded`됩니다. | `MyActivity - Succeeded` |
|**pipelineName**| String | 파이프라인의 이름 | `MyPipeline` |
|**activityName**| String | 작업의 이름입니다. | `MyActivity` |
|**start**| String | 작업의 시작 시간은 timespan UTC 형식으로 실행 됩니다. | `2017-06-26T20:55:29.5007959Z`|
|**end**| String | 활동의 종료 시간은 timespan UTC 형식으로 실행 됩니다. 진단 로그에 작업이 시작 되었지만 아직 종료 되지 않은 것으로 표시 되는 경우 속성 값이 `1601-01-01T00:00:00Z`됩니다. | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-log-attributes"></a>파이프라인-실행 로그 특성

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties":
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| 속성 | 유형 | Description | 예 |
| --- | --- | --- | --- |
| **Level** |String | 진단 로그의 수준입니다. 활동 실행 로그에 대해 속성 값을 4로 설정 합니다. | `4` |
| **correlationId** |String | 특정 요청을 추적 하기 위한 고유 ID입니다. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | Timespan UTC 형식의 이벤트 시간 `YYYY-MM-DDTHH:MM:SS.00000Z`입니다. | `2017-06-28T21:00:27.3534352Z` |
|**runId**| String| 파이프라인 실행의 ID입니다. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| String | 데이터 팩터리 리소스와 연결 된 ID입니다. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | 진단 로그의 범주입니다. 속성 값을 `PipelineRuns`로 설정합니다. | `PipelineRuns` |
|**level**| String | 진단 로그의 수준입니다. 속성 값을 `Informational`로 설정합니다. | `Informational` |
|**operationName**| String | 상태와 함께 파이프라인 이름입니다. 파이프라인 실행이 완료 되 면 속성 값이 `Pipeline - Succeeded`됩니다. | `MyPipeline - Succeeded`에 대한 답변에 설명되어 있는 단계를 성공적으로 완료하면 활성화됩니다. |
|**pipelineName**| String | 파이프라인의 이름 | `MyPipeline` |
|**start**| String | 작업의 시작 시간은 timespan UTC 형식으로 실행 됩니다. | `2017-06-26T20:55:29.5007959Z`에 대한 답변에 설명되어 있는 단계를 성공적으로 완료하면 활성화됩니다. |
|**end**| String | 활동의 종료 시간은 timespan UTC 형식으로 실행 됩니다. 진단 로그에 시작 되었지만 아직 종료 되지 않은 활동이 표시 되 면 속성 값이 `1601-01-01T00:00:00Z`됩니다.  | `2017-06-26T20:55:29.5007959Z` |
|**status**| String | 파이프라인 실행의 최종 상태입니다. 가능한 속성 값은 `Succeeded` 및 `Failed`입니다. | `Succeeded`|

#### <a name="trigger-run-log-attributes"></a>트리거-실행 로그 특성

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
}

```

| 속성 | 유형 | Description | 예 |
| --- | --- | --- | --- |
| **Level** |String | 진단 로그의 수준입니다. 활동 실행 로그에 대해 속성 값을 4로 설정 합니다. | `4` |
| **correlationId** |String | 특정 요청을 추적 하기 위한 고유 ID입니다. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | String | Timespan UTC 형식의 이벤트 시간 `YYYY-MM-DDTHH:MM:SS.00000Z`입니다. | `2017-06-28T21:00:27.3534352Z` |
|**triggerId**| String| 트리거의 실행 ID입니다. | `08587023010602533858661257311` |
|**resourceId**| String | 데이터 팩터리 리소스와 연결 된 ID입니다. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| String | 진단 로그의 범주입니다. 속성 값을 `PipelineRuns`로 설정합니다. | `PipelineRuns` |
|**level**| String | 진단 로그의 수준입니다. 속성 값을 `Informational`로 설정합니다. | `Informational` |
|**operationName**| String | 트리거가 성공적으로 실행 되었는지 여부를 나타내는 최종 상태의 트리거의 이름입니다. 하트 비트가 성공적으로 완료 되 면 속성 값이 `MyTrigger - Succeeded`됩니다. | `MyTrigger - Succeeded` |
|**triggerName**| String | 트리거의 이름입니다. | `MyTrigger` |
|**triggerType**| String | 트리거의 유형입니다. 가능한 속성 값은 `Manual Trigger` 및 `Schedule Trigger`입니다. | `ScheduleTrigger` |
|**triggerEvent**| String | 트리거의 이벤트입니다. | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**start**| String | Timespan UTC 형식의 트리거 시작 시간입니다. | `2017-06-26T20:55:29.5007959Z`|
|**status**| String | 트리거가 성공적으로 실행 되었는지 여부를 보여 주는 최종 상태입니다. 가능한 속성 값은 `Succeeded` 및 `Failed`입니다. | `Succeeded`|

### <a name="log-analytics-schema"></a>Log Analytics 스키마

Log Analytics는 다음과 같은 예외를 제외 하 고 모니터에서 스키마를 상속 합니다.

* 각 열 이름의 첫 글자는 대문자로 되어 있습니다. 예를 들어 Monitor의 열 이름 "correlationId"는 Log Analytics의 "CorrelationId"입니다.
* "Level" 열이 없습니다.
* 동적 "속성" 열은 다음과 같은 동적 JSON blob 유형으로 유지 됩니다.

    | Azure Monitor 열 | Log Analytics 열 | 유형 |
    | --- | --- | --- |
    | $. 속성. UserProperties | UserProperties | 동적 |
    | $. 속성. 달 | 주석 | 동적 |
    | $. 속성. 입력 | 입력 | 동적 |
    | $. 속성. 출력 | 출력 | 동적 |
    | $. 속성. 오류입니다. errorCode | ErrorCode | int |
    | $. 속성. 오류 메시지 | ErrorMessage | 문자열 |
    | $. 속성. 메시지가 | 오류 | 동적 |
    | $. 속성. 이전 | 이전 | 동적 |
    | $. 속성. 변수의 | 매개 변수 | 동적 |
    | $. 속성. SystemParameters | SystemParameters | 동적 |
    | $. 속성. 사이 | 태그 | 동적 |
    
## <a name="metrics"></a>메트릭

모니터를 사용 하 여 Azure 워크 로드의 성능 및 상태를 파악할 수 있습니다. 가장 중요 한 유형의 모니터 데이터는 메트릭 (성능 카운터 라고도 함)입니다. 메트릭은 대부분의 Azure 리소스에서 내보내집니다. 모니터는 모니터링 및 문제 해결을 위해 이러한 메트릭을 구성 하 고 사용 하는 여러 가지 방법을 제공 합니다.

Azure Data Factory 버전 2는 다음 메트릭을 내보냅니다.

| **메트릭**           | **메트릭 표시 이름**         | **단위** | **집계 유형** | **설명**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRuns | 성공한 파이프라인 실행 메트릭 | 카운트    | 합계                | 1 분 기간 내에 성공한 총 파이프라인 실행 수입니다. |
| PipelineFailedRuns   | 실패한 파이프라인 실행 메트릭    | 카운트    | 합계                | 1 분 기간 내에 실패 한 총 파이프라인 실행 수입니다.    |
| ActivitySucceededRuns | 성공한 활동 실행 메트릭 | 카운트    | 합계                | 1 분 기간 내에 성공한 총 활동 실행 수입니다.  |
| ActivityFailedRuns   | 실패한 활동 실행 메트릭    | 카운트    | 합계                | 1 분 기간 내에 실패 한 작업 실행의 총 수입니다.     |
| TriggerSucceededRuns | 성공한 트리거 실행 메트릭  | 카운트    | 합계                | 1 분 기간 내에 성공한 총 트리거 실행 수입니다.   |
| TriggerFailedRuns    | 실패한 트리거 실행 메트릭     | 카운트    | 합계                | 1 분 기간 내에 실패 한 총 트리거 실행 수입니다.      |

메트릭에 액세스 하려면 [Azure Monitor data platform](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)의 지침을 완료 합니다.

> [!NOTE]
> 완료, 트리거된 작업 및 파이프라인 실행 이벤트만 내보내집니다. 진행 중이 고 sandbox/디버그 실행을 내보내지 **않습니다** . 

## <a name="monitor-data-factory-metrics-with-azure-monitor"></a>Azure Monitor를 사용 하 여 Data Factory 메트릭 모니터링

모니터와 Data Factory 통합을 사용 하 여 데이터를 모니터로 라우팅할 수 있습니다. 이 통합은 다음과 같은 시나리오에서 유용합니다.

* Data Factory 모니터링을 위해 게시 되는 다양 한 메트릭 집합에 복잡 한 쿼리를 작성 하려고 합니다. 모니터를 통해 이러한 쿼리에 대한 사용자 지정 경고를 만들 수 있습니다.

* 데이터 팩터리를 모니터링하려고 합니다. 여러 데이터 팩터리에서 데이터를 단일 모니터 작업 영역으로 라우팅할 수 있습니다.

7분 동안 이 기능의 소개 및 데모에 대한 다음 비디오를 시청하세요.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

### <a name="configure-diagnostic-settings-and-workspace"></a>진단 설정 및 작업 영역 구성

데이터 팩터리에 대한 진단 설정을 만들거나 추가 합니다.

1. 포털에서 모니터로 이동 합니다. **설정** > **진단 설정**을 선택 합니다.

1. 진단 설정을 지정할 데이터 팩터리를 선택 합니다.

1. 선택한 데이터 팩터리에 설정이 없는 경우 설정을 만들라는 메시지가 표시 됩니다. **진단 켜기**를 선택합니다.

   ![설정이 없는 경우 진단 설정 만들기](media/data-factory-monitor-oms/monitor-oms-image1.png)

   Data factory에 기존 설정이 있으면 데이터 팩터리에 이미 구성 된 설정 목록이 표시 됩니다. **진단 설정 추가**를 선택 합니다.

   ![설정이 있는 경우 진단 설정 추가](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. 설정에 이름을 지정 하 고 **Log Analytics 보내기**를 선택한 다음 **Log Analytics 작업 영역**에서 작업 영역을 선택 합니다.

    ![설정의 이름을로 설정 하 고 log analytics 작업 영역을 선택 합니다.](media/data-factory-monitor-oms/monitor-oms-image2.png)

1. **저장**을 선택합니다.

몇 분 후에이 데이터 팩터리에 대한 설정 목록에 새 설정이 표시 됩니다. 진단 로그는 새 이벤트 데이터가 생성 되는 즉시 해당 작업 영역으로 스트리밍됩니다. 이벤트가 내보내지는 때와 Log Analytics에 표시 될 때까지 최대 15 분이 경과 될 수 있습니다.

* _리소스 특정_ 모드에서 _ADFPipelineRun_, _ADFTriggerRun_및 _adfactivityrun_ 테이블로 Azure Data Factory 흐름의 진단 로그
* _Azure 진단_ 모드에서 진단 로그는 _AzureDiagnostics_ 테이블로 흐릅니다.

> [!NOTE]
> Azure 로그 테이블에는 500 개 이상의 열을 사용할 수 없기 때문에 리소스 특정 모드를 선택 하는 것이 좋습니다. 자세한 내용은 [Log Analytics 알려진 제한 사항](../azure-monitor/platform/resource-logs-collect-workspace.md#column-limit-in-azurediagnostics)을 참조 하세요.

### <a name="install-azure-data-factory-analytics-from-azure-marketplace"></a>Azure Marketplace에서 Azure Data Factory Analytics 설치

!["Azure Marketplace"으로 이동 하 여 "Analytics 필터"를 입력 하 고 "Azure Data Factory 분석 (미리 보기)"을 선택 합니다.](media/data-factory-monitor-oms/monitor-oms-image3.png)

!["Azure Data Factory 분석 (미리 보기)"에 대한 세부 정보](media/data-factory-monitor-oms/monitor-oms-image4.png)

**만들기** 를 선택 하 고 **oms 작업 영역** 및 **oms 작업 영역 설정**을 선택 합니다.

![새 솔루션 만들기](media/data-factory-monitor-oms/monitor-oms-image5.png)

### <a name="monitor-data-factory-metrics"></a>Data Factory 메트릭 모니터링

Azure Data Factory 분석을 설치 하면 다음 메트릭을 사용할 수 있도록 기본 뷰 집합이 생성 됩니다.

- ADF 실행-1) Data Factory에의 한 파이프라인 실행
 
- ADF 실행- 2) Data Factory의 활동 실행

- ADF 실행-3) Data Factory에서 트리거 실행

- ADF 오류-1) Data Factory 별 상위 10 개의 파이프라인 오류

- ADF 오류-2) Data Factory에서 상위 10 개의 작업 실행

- ADF 오류-3) Data Factory 별 상위 10 개 트리거 오류

- ADF 통계-1) 유형별 작업 실행

- ADF 통계-2) 유형별 트리거 실행

- ADF 통계-3) 최대 파이프라인 실행 기간

!["통합 문서 (미리 보기)" 및 "AzureDataFactoryAnalytics"가 강조 표시 된 창](media/data-factory-monitor-oms/monitor-oms-image6.png)

위의 메트릭을 시각화 하 고, 이러한 메트릭 뒤의 쿼리를 살펴보고, 쿼리를 편집 하 고, 경고를 만들고, 다른 작업을 수행할 수 있습니다.

![데이터 팩터리에서 파이프라인 실행의 그래픽 표현 "](media/data-factory-monitor-oms/monitor-oms-image8.png)

> [!NOTE]
> Azure Data Factory 분석 (미리 보기) _은 리소스 관련_ 대상 테이블에 진단 로그를 보냅니다. _ADFPipelineRun_, _ADFTriggerRun_및 _adfactivityrun_테이블에 대해 쿼리를 작성할 수 있습니다.

## <a name="alerts"></a>경고

Azure Portal에 로그인 하 고 > 경고 **모니터링** 을 선택 하 여 경고를 만듭니다.

![포털 메뉴의 경고](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>경고 만들기

1. **+ 새로운 경고 규칙**을 선택하여 새 경고를 만듭니다.

    ![새 경고 규칙](media/monitor-using-azure-monitor/alerts_image4.png)

1. 경고 조건을 정의 합니다.

    > [!NOTE]
    > **리소스 유형별 필터** 드롭다운 목록에서 **모두** 를 선택 해야 합니다.

    !["Select target" > "경고 조건 정의"를 선택 하면 "리소스 선택" 창이 열립니다. ](media/monitor-using-azure-monitor/alerts_image5.png)

    !["경고 조건 정의" > "조건 추가"를 통해 "신호 논리 구성" 창이 열립니다.](media/monitor-using-azure-monitor/alerts_image6.png)

    !["신호 유형 구성" 창](media/monitor-using-azure-monitor/alerts_image7.png)

1. 경고 세부 정보를 정의합니다.

    ![경고 세부 정보](media/monitor-using-azure-monitor/alerts_image8.png)

1. 작업 그룹을 정의 합니다.

    !["새 작업 그룹"이 강조 표시 된 규칙 만들기](media/monitor-using-azure-monitor/alerts_image9.png)

    ![새 작업 그룹 만들기](media/monitor-using-azure-monitor/alerts_image10.png)

    ![전자 메일, SMS, 푸시 및 음성 구성](media/monitor-using-azure-monitor/alerts_image11.png)

    ![작업 그룹 정의](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>다음 단계
[프로그래밍 방식으로 파이프라인 모니터링 및 관리](monitor-programmatically.md)
