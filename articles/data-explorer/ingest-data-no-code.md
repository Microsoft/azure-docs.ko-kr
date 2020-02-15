---
title: '자습서: 코드 없이 모니터링 데이터 수집 - Azure Data Explorer'
description: 이 자습서에서는 코드를 사용하지 않고 모니터링 데이터를 Azure Data Explorer에 수집하고 해당 데이터를 쿼리하는 방법을 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: tutorial
ms.date: 01/29/2020
ms.openlocfilehash: 24e09f6578431e6b7f2a83be13bae59bf2e707de
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76986209"
---
# <a name="tutorial-ingest-and-query-monitoring-data-in-azure-data-explorer"></a>자습서: Azure Data Explorer에서 모니터링 데이터 수집 및 쿼리 

이 자습서에서는 코드를 작성하지 않고 진단 및 활동 로그 데이터를 Azure Data Explorer 클러스터에 수집하는 방법을 설명합니다. 이 간단한 수집 메서드를 사용하면 데이터 분석을 위해 Azure Data Explorer에 대한 쿼리를 신속하게 시작할 수 있습니다.

이 자습서에서 학습할 방법은 다음과 같습니다.

> [!div class="checklist"]
> * Azure Data Explorer 데이터베이스에서 테이블 및 수집 매핑을 만듭니다.
> * 업데이트 정책을 사용하여 수집된 데이터의 형식을 지정합니다.
> * [이벤트 허브](/azure/event-hubs/event-hubs-about)를 만들어서 Azure Data Explorer에 연결합니다.
> * 데이터를 Azure Monitor [진단 메트릭 및 로그](/azure/azure-monitor/platform/diagnostic-settings)와 [활동 로그](/azure/azure-monitor/platform/activity-logs-overview)에서 이벤트 허브로 스트림합니다.
> * Azure Data Explorer를 사용하여 수집된 데이터를 쿼리합니다.

> [!NOTE]
> 모든 리소스를 동일한 Azure 위치나 지역에 만듭니다. 

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [Azure Data Explorer 클러스터 및 데이터베이스](create-cluster-database-portal.md). 이 자습서에서 데이터베이스 이름은 *TestDatabase*입니다.

## <a name="azure-monitor-data-provider-diagnostic-metrics-and-logs-and-activity-logs"></a>Azure Monitor 데이터 공급자: 진단 메트릭 및 로그와 활동 로그

아래의 Azure Monitor 메트릭 및 로그와 활동 로그에서 제공하는 데이터를 확인하고 이해합니다. 이러한 데이터 스키마를 기반으로 하여 수집 파이프라인을 만듭니다. 로그의 각 이벤트에는 레코드 배열이 있습니다. 이 레코드 배열은 나중에 자습서에서 분할됩니다.

### <a name="examples-of-diagnostic-metrics-and-logs-and-activity-logs"></a>진단 메트릭 및 로그와 활동 로그의 예제

Azure 진단 메트릭 및 로그와 활동 로그는 Azure 서비스에서 내보내, 해당 서비스의 작업에 대한 데이터를 제공합니다. 

# <a name="diagnostic-metricstabdiagnostic-metrics"></a>[진단 메트릭](#tab/diagnostic-metrics)
#### <a name="example"></a>예제

진단 메트릭은 1분의 시간 조직으로 집계됩니다. 쿼리 기간에 대한 Azure Data Explorer 메트릭 이벤트 스키마의 예제는 다음과 같습니다.

```json
{
    "records": [
    {
        "count": 14,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
        "time": "2018-12-20T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    },
    {
        "count": 12,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
        "time": "2018-12-21T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    }
    ]
}
```

# <a name="diagnostic-logstabdiagnostic-logs"></a>[진단 로그](#tab/diagnostic-logs)
#### <a name="example"></a>예제

Azure Data Explorer [진단 수집 로그](using-diagnostic-logs.md#diagnostic-logs-schema)의 예제는 다음과 같습니다.

```json
{
    "time": "2019-08-26T13:22:36.8804326Z",
    "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "FailedIngestion",
    "resultType": "Failed",
    "correlationId": "d59882f1-ad64-4fc4-b2ef-d663b6cc1cc5",
    "properties": {
        "OperationId": "00000000-0000-0000-0000-000000000000",
        "Database": "Kusto",
        "Table": "Table_13_20_prod",
        "FailedOn": "2019-08-26T13:22:36.8804326Z",
        "IngestionSourceId": "d59882f1-ad64-4fc4-b2ef-d663b6cc1cc5",
        "Details":
        {
            "error": 
            {
                "code": "BadRequest_DatabaseNotExist",
                "message": "Request is invalid and cannot be executed.",
                "@type": "Kusto.Data.Exceptions.DatabaseNotFoundException",
                "@message": "Database 'Kusto' was not found.",
                "@context": 
                {
                    "timestamp": "2019-08-26T13:22:36.7179157Z",
                    "serviceAlias": "<cluster-name>",
                    "machineName": "KEngine000001",
                    "processName": "Kusto.WinSvc.Svc",
                    "processId": 5336,
                    "threadId": 6528,
                    "appDomainName": "Kusto.WinSvc.Svc.exe",
                    "clientRequestd": "DM.IngestionExecutor;a70ddfdc-b471-4fc7-beac-bb0f6e569fe8",
                    "activityId": "f13e7718-1153-4e65-bf82-8583d712976f",
                    "subActivityId": "2cdad9d0-737b-4c69-ac9a-22cf9af0c41b",
                    "activityType": "DN.AdminCommand.DataIngestPullCommand",
                    "parentActivityId": "2f65e533-a364-44dd-8d45-d97460fb5795",
                    "activityStack": "(Activity stack: CRID=DM.IngestionExecutor;a70ddfdc-b471-4fc7-beac-bb0f6e569fe8 ARID=f13e7718-1153-4e65-bf82-8583d712976f > DN.Admin.Client.ExecuteControlCommand/5b764b32-6017-44a2-89e7-860eda515d40 > P.WCF.Service.ExecuteControlCommandInternal..IAdminClientServiceCommunicationContract/c2ef9344-069d-44c4-88b1-a3570697ec77 > DN.FE.ExecuteControlCommand/2f65e533-a364-44dd-8d45-d97460fb5795 > DN.AdminCommand.DataIngestPullCommand/2cdad9d0-737b-4c69-ac9a-22cf9af0c41b)"
                },
                "@permanent": true
            }
        },
        "ErrorCode": "BadRequest_DatabaseNotExist",
        "FailureStatus": "Permanent",
        "RootActivityId": "00000000-0000-0000-0000-000000000000",
        "OriginatesFromUpdatePolicy": false,
        "ShouldRetry": false,
        "IngestionSourcePath": "https://c0skstrldkereneus01.blob.core.windows.net/aam-20190826-temp-e5c334ee145d4b43a3a2d3a96fbac1df/3216_test_3_columns_invalid_8f57f0d161ed4a8c903c6d1073005732_59951f9ca5d143b6bdefe52fa381a8ca.zip"
    }
}
```
# <a name="activity-logstabactivity-logs"></a>[활동 로그](#tab/activity-logs)
#### <a name="example"></a>예제

Azure 활동 로그는 구독에 있는 리소스에 대해 수행되는 작업에 대한 인사이트를 제공하는 구독 수준 로그입니다. 액세스를 확인하기 위한 활동 로그 이벤트의 예제는 다음과 같습니다.

```json
{
    "records": [
    {
        "time": "2018-12-26T16:23:06.1090193Z",
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Start",
        "resultSignature": "Started.",
        "durationMs": 0,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            ...
        }
    },
    {
        "time": "2018-12-26T16:23:06.3040244Z",
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Success",
        "resultSignature": "Succeeded.OK",
        "durationMs": 194,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            "statusCode": "OK",
            "serviceRequestId": "87acdebc-945f-4c0c-b931-03050e085626"
        }
    }]
}
```
---

## <a name="set-up-an-ingestion-pipeline-in-azure-data-explorer"></a>Azure Data Explorer에서 수집 파이프라인 설정

Azure Data Explorer 파이프라인을 설정하려면 [테이블 생성 및 데이터 수집](/azure/data-explorer/ingest-sample-data#ingest-data)과 같은 여러 단계가 필요합니다. 데이터를 조작, 매핑 및 업데이트할 수도 있습니다.

### <a name="connect-to-the-azure-data-explorer-web-ui"></a>Azure Data Explorer 웹 UI에 연결

Azure Data Explorer *TestDatabase* 데이터베이스에서 **쿼리**를 선택하여 Azure Data Explorer 웹 UI를 엽니다.

![쿼리 페이지](media/ingest-data-no-code/query-database.png)

### <a name="create-the-target-tables"></a>대상 테이블 만들기

Azure Monitor 로그의 구조는 테이블 형식이 아닙니다. 데이터를 조작하고 각 이벤트를 하나 이상의 레코드로 확장합니다. 원시 데이터는 활동 로그의 경우 *ActivityLogsRawRecords*, 진단 메트릭 및 로그의 경우 *DiagnosticRawRecords*라는 중간 테이블로 수집됩니다. 이 때 데이터가 조작되고 확장됩니다. 그런 다음, 업데이트 정책을 사용하면 확장된 데이터가 활동 로그의 경우 *ActivityLogs* 테이블, 진단 메트릭의 경우 *DiagnosticMetrics* 및 진단 로그의 경우 *DiagnosticLogs*로 수집됩니다. 즉, 활동 로그를 수집하기 위해 별도의 세 개 테이블을 만들고, 진단 메트릭 및 로그 수집을 수집하기 위해 별도의 두 개 테이블을 만들어야 합니다.

Azure Data Explorer 웹 UI를 사용하여 Azure Data Explorer 데이터베이스에 대상 테이블을 만듭니다.

# <a name="diagnostic-metricstabdiagnostic-metrics"></a>[진단 메트릭](#tab/diagnostic-metrics)
#### <a name="create-tables-for-the-diagnostic-metrics"></a>진단 메트릭용 테이블 만들기

1. *TestDatabase* 데이터베이스에서 진단 메트릭 레코드를 저장하기 위한 *DiagnosticMetrics*라는 테이블을 만듭니다. 다음과 같은 `.create table` 제어 명령을 사용합니다.

    ```kusto
    .create table DiagnosticMetrics (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. **실행**을 선택하여 테이블을 만듭니다.

    ![쿼리 실행](media/ingest-data-no-code/run-query.png)

1. *TestDatabase* 데이터베이스에서 다음 쿼리를 사용하여 데이터를 조작하기 위한 *DiagnosticRawRecords*라는 중간 데이터 테이블을 만듭니다. **실행**을 선택하여 테이블을 만듭니다.

    ```kusto
    .create table DiagnosticRawRecords (Records:dynamic)
    ```

1. 중간 테이블에 대한 [보존 정책](/azure/kusto/management/retention-policy)을 0으로 설정합니다.

    ```kusto
    .alter-merge table DiagnosticRawRecords policy retention softdelete = 0d
    ```

# <a name="diagnostic-logstabdiagnostic-logs"></a>[진단 로그](#tab/diagnostic-logs)
#### <a name="create-tables-for-the-diagnostic-logs"></a>진단 로그용 테이블 만들기 

1. *TestDatabase* 데이터베이스에서 진단 로그 레코드를 저장하기 위한 *DiagnosticLogs*라는 테이블을 만듭니다. 다음과 같은 `.create table` 제어 명령을 사용합니다.

    ```kusto
    .create table DiagnosticLogs (Timestamp:datetime, ResourceId:string, OperationName:string, Result:string, OperationId:string, Database:string, Table:string, IngestionSourceId:string, IngestionSourcePath:string, RootActivityId:string, ErrorCode:string, FailureStatus:string, Details:string)
    ```

1. **실행**을 선택하여 테이블을 만듭니다.

1. *TestDatabase* 데이터베이스에서 다음 쿼리를 사용하여 데이터를 조작하기 위한 *DiagnosticRawRecords*라는 중간 데이터 테이블을 만듭니다. **실행**을 선택하여 테이블을 만듭니다.

    ```kusto
    .create table DiagnosticRawRecords (Records:dynamic)
    ```

1. 중간 테이블에 대한 [보존 정책](/azure/kusto/management/retention-policy)을 0으로 설정합니다.

    ```kusto
    .alter-merge table DiagnosticRawRecords policy retention softdelete = 0d
    ```

# <a name="activity-logstabactivity-logs"></a>[활동 로그](#tab/activity-logs)
#### <a name="create-tables-for-the-activity-logs"></a>활동 로그용 테이블 만들기 

1. *TestDatabase* 데이터베이스에서 활동 로그 레코드를 받기 위한 *ActivityLogs*라는 테이블을 만듭니다. 테이블을 만들려면 다음 Azure Data Explorer 쿼리를 실행합니다.

    ```kusto
    .create table ActivityLogs (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. 데이터 조작을 위해 *TestDatabase* 데이터베이스에 *ActivityLogsRawRecords*라는 중간 데이터 테이블을 만듭니다.

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    ```

1. 중간 테이블에 대한 [보존 정책](/azure/kusto/management/retention-policy)을 0으로 설정합니다.

    ```kusto
    .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    ```
---

### <a name="create-table-mappings"></a>테이블 매핑 만들기

 데이터 형식이 `json`이기 때문에 데이터 매핑이 필요합니다. `json` 매핑은 각 json 경로를 테이블 열 이름에 매핑합니다.

# <a name="diagnostic-metrics--diagnostic-logstabdiagnostic-metricsdiagnostic-logs"></a>[진단 메트릭/진단 로그](#tab/diagnostic-metrics+diagnostic-logs) 
#### <a name="map-diagnostic-metrics-and-logs-to-the-table"></a>진단 메트릭 및 로그를 테이블에 매핑

진단 메트릭 및 로그 데이터를 테이블에 매핑하려면 다음 쿼리를 사용합니다.

```kusto
.create table DiagnosticRawRecords ingestion json mapping 'DiagnosticRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

# <a name="activity-logstabactivity-logs"></a>[활동 로그](#tab/activity-logs)
#### <a name="map-activity-logs-to-the-table"></a>활동 로그를 테이블에 매핑

활동 로그 데이터를 테이블에 매핑하려면 다음 쿼리를 사용합니다.

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```
---

### <a name="create-the-update-policy-for-metric-and-log-data"></a>메트릭 및 로그 데이터에 대한 업데이트 정책 만들기

# <a name="diagnostic-metricstabdiagnostic-metrics"></a>[진단 메트릭](#tab/diagnostic-metrics)
#### <a name="create-data-update-policy-for-diagnostics-metrics"></a>진단 메트릭에 대한 데이터 업데이트 정책 만들기

1. 컬렉션의 각 값에서 별도의 행을 받도록 진단 메트릭 레코드의 해당 컬렉션을 확장하는 [함수](/azure/kusto/management/functions)를 만듭니다. [`mv-expand`](/azure/kusto/query/mvexpandoperator) 연산자를 사용합니다.
     ```kusto
    .create function DiagnosticMetricsExpand() {
        DiagnosticRawRecords
        | mv-expand events = Records
        | where isnotempty(events.metricName)
        | project
            Timestamp = todatetime(events['time']),
            ResourceId = tostring(events.resourceId),
            MetricName = tostring(events.metricName),
            Count = toint(events['count']),
            Total = todouble(events.total),
            Minimum = todouble(events.minimum),
            Maximum = todouble(events.maximum),
            Average = todouble(events.average),
            TimeGrain = tostring(events.timeGrain)
    }
    ```

2. 대상 테이블에 [업데이트 정책](/azure/kusto/concepts/updatepolicy)을 추가합니다. 이 정책은 *DiagnosticRawRecords* 중간 데이터 테이블에서 새로 수집된 데이터에 대한 쿼리를 자동으로 실행하고, 해당 결과를 *DiagnosticMetrics* 테이블에 수집합니다.

    ```kusto
    .alter table DiagnosticMetrics policy update @'[{"Source": "DiagnosticRawRecords", "Query": "DiagnosticMetricsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```

# <a name="diagnostic-logstabdiagnostic-logs"></a>[진단 로그](#tab/diagnostic-logs)
#### <a name="create-data-update-policy-for-diagnostics-logs"></a>진단 로그에 대한 데이터 업데이트 정책 만들기

1. 컬렉션의 각 값에서 별도의 행을 받도록 진단 로그 레코드의 해당 컬렉션을 확장하는 [함수](/azure/kusto/management/functions)를 만듭니다. Azure Data Explorer 클러스터에서 수신 로그를 사용하도록 설정하고 [수집 로그 스키마](/azure/data-explorer/using-diagnostic-logs#diagnostic-logs-schema)를 사용합니다. 성공하고 실패한 수집을 위해 테이블을 하나씩 만들지만, 필드 중 일부는 성공한 수집에 대비하여 비어 있습니다(예: ErrorCode). [`mv-expand`](/azure/kusto/query/mvexpandoperator) 연산자를 사용합니다.

    ```kusto
    .create function DiagnosticLogsExpand() {
        DiagnosticRawRecords
        | mv-expand events = Records
        | where isnotempty(events.operationName)
        | project
            Timestamp = todatetime(events['time']),
            ResourceId = tostring(events.resourceId),
            OperationName = tostring(events.operationName),
            Result = tostring(events.resultType),
            OperationId = tostring(events.properties.OperationId),
            Database = tostring(events.properties.Database),
            Table = tostring(events.properties.Table),
            IngestionSourceId = tostring(events.properties.IngestionSourceId),
            IngestionSourcePath = tostring(events.properties.IngestionSourcePath),
            RootActivityId = tostring(events.properties.RootActivityId),
            ErrorCode = tostring(events.properties.ErrorCode),
            FailureStatus = tostring(events.properties.FailureStatus),
            Details = tostring(events.properties.Details)
    }
    ```

2. 대상 테이블에 [업데이트 정책](/azure/kusto/concepts/updatepolicy)을 추가합니다. 이 정책은 *DiagnosticRawRecords* 중간 데이터 테이블에서 새로 수집된 데이터에 대한 쿼리를 자동으로 실행하고, 해당 결과를 *DiagnosticLogs* 테이블에 수집합니다.

    ```kusto
    .alter table DiagnosticLogs policy update @'[{"Source": "DiagnosticRawRecords", "Query": "DiagnosticLogsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```

# <a name="activity-logstabactivity-logs"></a>[활동 로그](#tab/activity-logs)
#### <a name="create-data-update-policy-for-activity-logs"></a>활동 로그에 대한 데이터 업데이트 정책 만들기

1. 컬렉션의 각 값이 별도의 행을 받도록 활동 로그 레코드 컬렉션을 확장하는 [함수](/azure/kusto/management/functions)를 만듭니다. [`mv-expand`](/azure/kusto/query/mvexpandoperator) 연산자를 사용합니다.

    ```kusto
    .create function ActivityLogRecordsExpand() {
        ActivityLogsRawRecords
        | mv-expand events = Records
        | project
            Timestamp = todatetime(events['time']),
            ResourceId = tostring(events.resourceId),
            OperationName = tostring(events.operationName),
            Category = tostring(events.category),
            ResultType = tostring(events.resultType),
            ResultSignature = tostring(events.resultSignature),
            DurationMs = toint(events.durationMs),
            IdentityAuthorization = events.identity.authorization,
            IdentityClaims = events.identity.claims,
            Location = tostring(events.location),
            Level = tostring(events.level)
    }
    ```

2. 대상 테이블에 [업데이트 정책](/azure/kusto/concepts/updatepolicy)을 추가합니다. 이 정책은 *ActivityLogsRawRecords* 중간 데이터 테이블에서 새로 수집된 데이터에 대한 쿼리를 자동으로 실행하고, 해당 결과를 *ActivityLogs* 테이블에 수집합니다.

    ```kusto
    .alter table ActivityLogs policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True", "IsTransactional": true}]'
    ```
---

## <a name="create-an-azure-event-hubs-namespace"></a>Azure Event Hubs 네임스페이스 만들기

Azure 진단 설정을 사용하면 메트릭 및 로그를 스토리지 계정 또는 이벤트 허브에 내보낼 수 있습니다. 이 자습서에서는 이벤트 허브를 통해 메트릭 및 로그를 라우팅합니다. 다음 단계에서는 진단 메트릭 및 로그에 대한 Event Hubs 네임스페이스 및 이벤트 허브를 만듭니다. Azure Monitor는 활동 로그에 대해 이벤트 허브 *insights-operational-logs*를 만듭니다.

1. Azure Portal에서 Azure Resource Manager 템플릿을 사용하여 이벤트 허브를 만듭니다. 이 문서의 나머지 단계를 수행하려면, **Azure에 배포** 단추를 마우스 오른쪽 단추로 클릭하고 **새 창에서 열기**를 선택합니다. **Azure에 배포** 단추를 선택하면 Azure Portal로 이동합니다.

    [![Azure에 배포 단추](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. Event Hubs 네임스페이스 및 진단 로그에 대한 이벤트 허브를 만듭니다.

    ![이벤트 허브 만들기](media/ingest-data-no-code/event-hub.png)

1. 다음 정보로 양식을 작성합니다. 다음 표에 나와 있지 않은 설정에는 기본값을 사용하세요.

    **설정** | **제안 값** | **설명**
    |---|---|---|
    | **구독** | *구독* | 이벤트 허브에 사용할 Azure 구독을 선택합니다.|
    | **리소스 그룹** | *test-resource-group* | 새 리소스 그룹을 만듭니다. |
    | **위치** | 요구에 가장 적합한 지역을 선택합니다. | 다른 리소스와 동일한 위치에 Event Hubs 네임스페이스를 만듭니다.
    | **네임스페이스 이름** | *AzureMonitoringData* | 네임스페이스를 식별하는 고유한 이름을 선택합니다.
    | **이벤트 허브 이름** | *DiagnosticData* | 이벤트 허브는 고유한 범위 지정 컨테이너 역할을 하는 네임스페이스 아래에 배치됩니다. |
    | **소비자 그룹 이름** | *adxpipeline* | 소비자 그룹 이름을 만듭니다. 소비자 그룹을 사용하면 각기 별도의 이벤트 스트림 보기가 표시되는 여러 애플리케이션을 사용할 수 있습니다. |
    | | |

## <a name="connect-azure-monitor-metrics-and-logs-to-your-event-hub"></a>Azure Monitor 메트릭 및 로그를 이벤트 허브에 연결

이제 진단 메트릭 및 로그와 활동 로그를 이벤트 허브에 연결해야 합니다.

# <a name="diagnostic-metrics--diagnostic-logstabdiagnostic-metricsdiagnostic-logs"></a>[진단 메트릭/진단 로그](#tab/diagnostic-metrics+diagnostic-logs) 
### <a name="connect-diagnostic-metrics-and-logs-to-your-event-hub"></a>진단 메트릭 및 로그를 이벤트 허브에 연결

메트릭을 내보낼 리소스를 선택합니다. Event Hubs 네임스페이스, Azure Key Vault, Azure IoT Hub 및 Azure Data Explorer 클러스터를 포함한 여러 가지 리소스 종류에서 진단 데이터 내보내기를 지원합니다. 이 자습서에서는 Azure Data Explorer 클러스터를 리소스로 사용하고, 쿼리 성능 메트릭과 수집 결과 로그를 검토합니다.

1. Azure Portal에서 Kusto 클러스터를 선택합니다.
1. **진단 설정**을 선택한 다음, **진단 켜기** 링크를 선택합니다. 

    ![진단 설정](media/ingest-data-no-code/diagnostic-settings.png)

1. **진단 설정** 창이 열립니다. 다음과 같은 단계를 수행합니다.
   1. 진단 로그 데이터 이름을 *ADXExportedData*라고 지정합니다.
   1. **로그** 아래에서 **SucceededIngestion** 및 **FailedIngestion** 확인란을 모두 선택합니다.
   1. **메트릭** 아래에서 **쿼리 성능** 확인란을 선택합니다.
   1. **이벤트 허브로의 스트림** 확인란을 선택합니다.
   1. **구성**을 선택합니다.

      ![진단 설정 창](media/ingest-data-no-code/diagnostic-settings-window.png)

1. **이벤트 허브 선택** 창에서 직접 만든 이벤트 허브로 진단 로그의 데이터를 내보내는 방법을 구성합니다.
    1. **이벤트 허브 네임스페이스 선택** 목록에서 *AzureMonitoringData*를 선택합니다.
    1. **이벤트 허브 이름 선택** 목록에서 *DiagnosticData*를 선택합니다.
    1. **이벤트 허브 정책 이름** 목록에서 **RootManagerSharedAccessKey**를 선택합니다.
    1. **확인**을 선택합니다.

1. **저장**을 선택합니다.

# <a name="activity-logstabactivity-logs"></a>[활동 로그](#tab/activity-logs)
### <a name="connect-activity-logs-to-your-event-hub"></a>이벤트 허브에 활동 로그 연결

1. Azure Portal 왼쪽 메뉴에서 **활동 로그**를 선택합니다.
1. **활동 로그** 창이 열립니다. **이벤트 허브로 내보내기**를 선택합니다.

    ![활동 로그 창](media/ingest-data-no-code/activity-log.png)

1. **활동 로그 내보내기** 창이 열립니다.
 
    ![활동 로그 내보내기 창](media/ingest-data-no-code/export-activity-log.png)

1. **활동 로그 내보내기** 창에서 다음 단계를 수행합니다.
      1. 구독을 선택합니다.
      1. **지역** 목록에서 **모두 선택**을 선택합니다.
      1. **이벤트 허브로 내보내기** 확인란을 선택합니다.
      1. **Service Bus 네임스페이스를 선택합니다.** 를 선택하여 **이벤트 허브 선택** 창을 엽니다.
      1. **이벤트 허브 선택** 창에서 구독을 선택합니다.
      1. **이벤트 허브 네임스페이스 선택** 목록에서 *AzureMonitoringData*를 선택합니다.
      1. **이벤트 허브 정책 이름 선택** 목록에서 기본 이벤트 허브 정책 이름을 선택합니다.
      1. **확인**을 선택합니다.
      1. 창의 왼쪽 위 모서리에서 **저장**을 선택합니다.
   이름이 *insights-operational-logs*인 이벤트 허브가 만들어집니다.
---

### <a name="see-data-flowing-to-your-event-hubs"></a>이벤트 허브로 흐르는 데이터 보기

1. 연결이 정의되고 이벤트 허브로 활동 로그 내보내기가 완료 될 때까지 잠시 기다립니다. Event Hubs 네임스페이스로 이동하여 직접 만든 이벤트 허브를 봅니다.

    ![만들어진 Event Hubs](media/ingest-data-no-code/event-hubs-created.png)

1. 이벤트 허브로 흐르는 데이터 보기:

    ![이벤트 허브의 데이터](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-an-event-hub-to-azure-data-explorer"></a>Azure Data Explorer에 이벤트 허브연결

이제 진단 메트릭 및 로그와 활동 로그에 대한 데이터 연결을 만들어야 합니다.

### <a name="create-the-data-connection-for-diagnostic-metrics-and-logs-and-activity-logs"></a>진단 메트릭 및 로그와 활동 로그에 대한 데이터 연결 만들기

1. *kustodocs*라는 Azure Data Explorer 클러스터의 왼쪽 메뉴에서 **Databases**를 선택합니다.
1. **데이터베이스** 창에서 *TestDatabase*를 선택합니다.
1. 왼쪽 메뉴에서 **데이터 수집**을 선택합니다.
1. **데이터 수집** 창에서 **+ 데이터 연결 추가**를 클릭합니다.
1. **데이터 연결** 창에서 다음 정보를 입력합니다.

    ![이벤트 허브 데이터 연결](media/ingest-data-no-code/event-hub-data-connection.png)

# <a name="diagnostic-metrics--diagnostic-logstabdiagnostic-metricsdiagnostic-logs"></a>[진단 메트릭/진단 로그](#tab/diagnostic-metrics+diagnostic-logs) 

1. **데이터 연결** 창에 다음 설정을 사용합니다.

    데이터 원본:

    **설정** | **제안 값** | **필드 설명**
    |---|---|---|
    | **데이터 연결 이름** | *DiagnosticsLogsConnection* | Azure 데이터 탐색기에서 만들 연결의 이름입니다.|
    | **이벤트 허브 네임스페이스** | *AzureMonitoringData* | 앞에서 선택한 네임스페이스를 식별하는 이름입니다. |
    | **이벤트 허브** | *DiagnosticData* | 앞에서 만든 이벤트 허브입니다. |
    | **소비자 그룹** | *adxpipeline* | 앞에서 만든 이벤트 허브에 정의된 소비자 그룹입니다. |
    | | |

    대상 테이블:

    라우팅 옵션으로는 *고정* 라우팅과 *동적* 라우팅이라는 두 가지 옵션이 있습니다. 이 자습서에서는 고정 라우팅(기본값)을 사용합니다. 이 경우 테이블 이름, 데이터 형식 및 매핑을 직접 지정합니다. **내 데이터에 라우팅 정보 포함**을 선택 취소합니다.

     **설정** | **제안 값** | **필드 설명**
    |---|---|---|
    | **테이블** | *DiagnosticRawRecords* | *TestDatabase* 데이터베이스에 만든 테이블입니다. |
    | **날짜 형식** | *JSON* | 테이블에 사용되는 형식입니다. |
    | **열 매핑** | *DiagnosticRawRecordsMapping* | *TestDatabase* 데이터베이스에 만든 매핑이며, 들어오는 JSON 데이터를 *DiagnosticRawRecords* 테이블의 열 이름과 데이터 형식에 매핑합니다.|
    | | |

1. **만들기**를 선택합니다.  

# <a name="activity-logstabactivity-logs"></a>[활동 로그](#tab/activity-logs)

1. **데이터 연결** 창에 다음 설정을 사용합니다.

    데이터 원본:

    **설정** | **제안 값** | **필드 설명**
    |---|---|---|
    | **데이터 연결 이름** | *ActivityLogsConnection* | Azure 데이터 탐색기에서 만들 연결의 이름입니다.|
    | **이벤트 허브 네임스페이스** | *AzureMonitoringData* | 앞에서 선택한 네임스페이스를 식별하는 이름입니다. |
    | **이벤트 허브** | *insights-operational-logs* | 앞에서 만든 이벤트 허브입니다. |
    | **소비자 그룹** | *$Default* | 기본 소비자 그룹입니다. 필요하면 다른 소비자 그룹을 만들 수 있습니다. |
    | | |

    대상 테이블:

    라우팅 옵션으로는 *고정* 라우팅과 *동적* 라우팅이라는 두 가지 옵션이 있습니다. 이 자습서에서는 고정 라우팅(기본값)을 사용합니다. 이 경우 테이블 이름, 데이터 형식 및 매핑을 직접 지정합니다. **내 데이터에 라우팅 정보 포함**을 선택 취소합니다.

     **설정** | **제안 값** | **필드 설명**
    |---|---|---|
    | **테이블** | *ActivityLogsRawRecords* | *TestDatabase* 데이터베이스에 만든 테이블입니다. |
    | **날짜 형식** | *JSON* | 테이블에 사용되는 형식입니다. |
    | **열 매핑** | *ActivityLogsRawRecordsMapping* | *TestDatabase* 데이터베이스에 만든 매핑이며, 들어오는 JSON 데이터를 *ActivityLogsRawRecords* 테이블의 열 이름 및 데이터 형식에 매핑합니다.|
    | | |

1. **만들기**를 선택합니다.  
---

## <a name="query-the-new-tables"></a>새 테이블 쿼리

이제 데이터가 흐르는 파이프라인이 있습니다. 클러스터를 통한 수집은 기본적으로 5분이 소요되므로 쿼리를 시작하기 전에 몇 분 동안 데이터 흐름을 허용합니다.

# <a name="diagnostic-metricstabdiagnostic-metrics"></a>[진단 메트릭](#tab/diagnostic-metrics)
### <a name="query-the-diagnostic-metrics-table"></a>진단 메트릭 테이블 쿼리

다음 쿼리는 Azure Data Explorer의 진단 메트릭 레코드에서 쿼리 기간 데이터를 분석합니다.

```kusto
DiagnosticMetrics
| where Timestamp > ago(15m) and MetricName == 'QueryDuration'
| summarize avg(Average)
```

쿼리 결과:

|   |   |
| --- | --- |
|   |  avg_Average |
|   | 00:06.156 |
| | |

# <a name="diagnostic-logstabdiagnostic-logs"></a>[진단 로그](#tab/diagnostic-logs)
### <a name="query-the-diagnostic-logs-table"></a>진단 로그 테이블 쿼리

이 파이프라인은 이벤트 허브를 통해 수집을 생성합니다. 이러한 수집의 결과를 검토합니다.
다음 쿼리는 각 간격에 대해 `Database`, `Table` 및 `IngestionSourcePath`의 샘플을 포함하여 분 단위로 발생한 수집 횟수를 분석합니다.

```kusto
DiagnosticLogs
| where Timestamp > ago(15m) and OperationName has 'INGEST'
| summarize count(), any(Database, Table, IngestionSourcePath) by bin(Timestamp, 1m)
```

쿼리 결과:

|   |   |
| --- | --- |
|   |  count_ | any_Database | any_Table | any_IngestionSourcePath
|   | 00:06.156 | TestDatabase | DiagnosticRawRecords | https://rtmkstrldkereneus00.blob.core.windows.net/20190827-readyforaggregation/1133_TestDatabase_DiagnosticRawRecords_6cf02098c0c74410bd8017c2d458b45d.json.zip
| | |

# <a name="activity-logstabactivity-logs"></a>[활동 로그](#tab/activity-logs)
### <a name="query-the-activity-logs-table"></a>활동 로그 테이블 쿼리

다음 쿼리는 Azure Data Explorer에서 활동 로그 레코드의 데이터를 분석합니다.

```kusto
ActivityLogs
| where OperationName == 'MICROSOFT.EVENTHUB/NAMESPACES/AUTHORIZATIONRULES/LISTKEYS/ACTION'
| where ResultType == 'Success'
| summarize avg(DurationMs)
```

쿼리 결과:

|   |   |
| --- | --- |
|   |  avg(DurationMs) |
|   | 768.333 |
| | |

---

## <a name="next-steps"></a>다음 단계

* [Azure Data Explorer에 대한 쿼리 작성](write-queries.md) 문서를 사용하여 Azure Data Explorer에서 추출한 데이터에 대해 더 많은 쿼리를 작성하는 방법을 알아봅니다.
* [진단 로그를 사용하여 Azure Data Explorer 수집 작업 모니터링](using-diagnostic-logs.md)
* [메트릭을 사용하여 클러스터 상태 모니터링](using-metrics.md)
