---
title: Azure Monitor의 Azure Storage 메트릭 | Microsoft Docs
description: Azure Monitor에서 제공하는 새 메트릭에 대해 알아봅니다.
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 09/05/2017
ms.author: fryu
ms.subservice: common
ms.openlocfilehash: 244d7fc3caa96173e408a193e13acd656d4a7f77
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62101985"
---
# <a name="azure-storage-metrics-in-azure-monitor"></a>Azure Monitor의 Azure Storage 메트릭

Azure Storage에 관한 메트릭을 사용하여 사용량 추세를 분석하고 요청을 추적하며 스토리지 계정에 대한 문제를 진단할 수 있습니다.

Azure Monitor는 다양한 Azure 서비스를 모니터링하기 위한 통합된 사용자 인터페이스를 제공합니다. 자세한 내용은 [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)를 참조하세요. Azure Storage는 메트릭 데이터를 Azure Monitor 플랫폼에 전송하여 Azure Monitor를 통합합니다.

## <a name="access-metrics"></a>메트릭에 액세스

Azure Monitor는 메트릭에 액세스하는 여러 가지 방법을 제공합니다. 액세스할 수 있습니다 합니다 [Azure portal](https://portal.azure.com), Azure Monitor Api (REST 및.NET) 및 Event Hubs 같은 분석 솔루션입니다. 자세한 내용은 [Azure Monitor 메트릭](../../monitoring-and-diagnostics/monitoring-overview-metrics.md)을 참조하세요.

메트릭은 기본적으로 활성화되며 지난 93일간의 데이터에 액세스할 수 있습니다. 더 오랜 기간에 대한 데이터를 보존해야 하는 경우 메트릭 데이터를 Azure Storage 계정에 보관할 수 있습니다. Azure Monitor의 [진단 설정](../../azure-monitor/platform/diagnostic-logs-overview.md)에서 이렇게 구성합니다.

### <a name="access-metrics-in-the-azure-portal"></a>Azure Portal에서 메트릭에 액세스

Azure Portal에서 시간 경과에 따른 메트릭을 모니터링할 수 있습니다. 다음 예에서는 계정 수준에서 **트랜잭션**을 보는 방법을 보여 줍니다.

![Azure Portal에서 메트릭에 액세스 스크린 샷](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal.png)

차원을 지원하는 메트릭의 경우 원하는 차원 값을 사용하여 메트릭을 필터링할 수 있습니다. 다음 예제에서는 **API 이름** 차원에 대한 값을 선택하여 특정 작업의 계정 수준에서 **트랜잭션**을 보는 방법을 보여줍니다.

![Azure Portal에서 차원을 사용하여 메트릭에 액세스 스크린 샷](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal-with-dimension.png)

### <a name="access-metrics-with-the-rest-api"></a>REST API를 사용하여 메트릭 액세스

Azure Monitor는 메트릭 정의 및 값을 읽는 [REST API](/rest/api/monitor/)를 제공합니다. 이 섹션에서는 저장 메트릭을 읽는 방법을 보여 줍니다. 리소스 ID는 모든 REST APIS에 사용됩니다. 자세한 내용은 Storage의 서비스에 대한 리소스 ID 이해를 참조하세요.

다음 예제는 명령줄에서 [ArmClient](https://github.com/projectkudu/ARMClient)를 사용하여 REST API를 통한 테스트를 단순화하는 방법을 보여 줍니다.

#### <a name="list-account-level-metric-definition-with-the-rest-api"></a>REST API를 사용하여 계정 차원 메트릭 정의 나열

다음 예제는 계정 차원에서 메트릭 정의를 나열하는 방법을 보여 줍니다.

```
# Login to Azure and enter your credentials when prompted.
> armclient login

> armclient GET /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions?api-version=2018-01-01

```

Blob, 테이블, 파일 또는 큐에 대한 메트릭 정의를 나열하려면 API를 사용하여 각 서비스에 대해 서로 다른 리소스 ID를 지정해야 합니다.

응답은 JSON 형식의 메트릭 정의를 포함합니다.

```Json
{
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions/UsedCapacity",
      "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
      "category": "Capacity",
      "name": {
        "value": "UsedCapacity",
        "localizedValue": "Used capacity"
      },
      "isDimensionRequired": false,
      "unit": "Bytes",
      "primaryAggregationType": "Average",
      "metricAvailabilities": [
        {
          "timeGrain": "PT1M",
          "retention": "P30D"
        },
        {
          "timeGrain": "PT1H",
          "retention": "P30D"
        }
      ]
    },
    ... next metric definition
  ]
}

```

#### <a name="read-account-level-metric-values-with-the-rest-api"></a>REST API를 사용하여 계정 차원 메트릭 값 읽기

다음 예제는 계정 차원에서 메트릭 데이터를 읽는 방법을 보여 줍니다.

```
> armclient GET "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metrics?metricnames=Availability&api-version=2018-01-01&aggregation=Average&interval=PT1H"

```

위의 예제에서 Blob, 테이블, 파일 또는 큐에 대한 메트릭 정의를 나열하려면 API를 사용하여 각 서비스에 대해 서로 다른 리소스 ID를 지정해야 합니다.

다음 응답은 JSON 형식의 메트릭 값을 포함합니다.

```Json
{
  "cost": 0,
  "timespan": "2017-09-07T17:27:41Z/2017-09-07T18:27:41Z",
  "interval": "PT1H",
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/Microsoft.Insights/metrics/Availability",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Availability",
        "localizedValue": "Availability"
      },
      "unit": "Percent",
      "timeseries": [
        {
          "metadatavalues": [],
          "data": [
            {
              "timeStamp": "2017-09-07T17:27:00Z",
              "average": 100.0
            }
          ]
        }
      ]
    }
  ]
}

```

### <a name="access-metrics-with-the-net-sdk"></a>.NET SDK를 사용 하 여 메트릭 액세스

Azure Monitor는 제공 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) 메트릭 정 및 값을 읽을 수 있습니다. [샘플 코드](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/)는 다른 매개 변수로 SDK를 사용하는 방법을 보여줍니다. 저장소 메트릭을 사용하려면 `0.18.0-preview` 이상의 버전을 사용해야 합니다. 리소스 ID는.NET SDK에서 사용 됩니다. 자세한 내용은 Storage의 서비스에 대한 리소스 ID 이해를 참조하세요.

다음 예제에서는 저장소 메트릭의 읽을 Azure Monitor.NET SDK를 사용 하는 방법을 보여 줍니다.

#### <a name="list-account-level-metric-definition-with-the-net-sdk"></a>.NET SDK를 사용 하 여 계정 차원 메트릭 정의 나열

다음 예제는 계정 차원에서 메트릭 정의를 나열하는 방법을 보여 줍니다.

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        // Using metrics in Azure Monitor is currently free. However, if you use additional solutions ingesting metrics data, you may be billed by these solutions. For example, you are billed by Azure Storage if you archive metrics data to an Azure Storage account. Or you are billed by Operation Management Suite (OMS) if you stream metrics data to OMS for advanced analysis.
        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            //Enumrate metric definition:
            //    Id
            //    ResourceId
            //    Name
            //    Unit
            //    MetricAvailabilities
            //    PrimaryAggregationType
            //    Dimensions
            //    IsDimensionRequired
        }
    }

```

Blob, 테이블, 파일 또는 큐에 대한 메트릭 정의를 나열하려면 API를 사용하여 각 서비스에 대해 서로 다른 리소스 ID를 지정해야 합니다.

#### <a name="read-metric-values-with-the-net-sdk"></a>.NET SDK를 사용 하 여 메트릭 값 읽기

다음 예제는 계정 차원에서 `UsedCapacity` 데이터를 읽는 방법을 보여줍니다.

```csharp
    public static async Task ReadStorageMetricValue()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metricnames: "UsedCapacity",

            aggregation: "Average",
            resultType: ResultType.Data,
            cancellationToken: CancellationToken.None);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

위의 예제에서 Blob, 테이블, 파일 또는 큐에 대한 메트릭 정의를 나열하려면 API를 사용하여 각 서비스에 대해 서로 다른 리소스 ID를 지정해야 합니다.

#### <a name="read-multi-dimensional-metric-values-with-the-net-sdk"></a>.NET SDK를 사용 하 여 다차원 메트릭 값 읽기

다차원 메트릭에서 특정 차원 값에 대한 메트릭 데이터를 읽으려면 메타 데이터 필터를 정의해야 합니다.

다음 예제는 다차원을 지원하는 메트릭에서 메트릭 데이터를 읽는 방법을 보여줍니다.

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metricnames: "BlobCapacity",
                        odataQuery: odataFilterMetrics,
                        aggregation: "Average",
                        resultType: ResultType.Data);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

## <a name="understanding-resource-id-for-services-in-azure-storage"></a>Azure Storage의 서비스에 대한 리소스 ID 이해

리소스 ID는 Azure에서 리소스의 고유 ID입니다. Azure Monitor REST API를 사용하여 메트릭 정의 또는 값을 읽는 경우 작동하려는 리소스에 대한 리소스 ID를 사용해야 합니다. 리소스 ID 템플릿은 다음 형식을 따릅니다.

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

저장소는 Azure Monitor를 통해 저장소 계정 수준 및 서비스 수준에서 모두 메트릭을 제공합니다. 예를 들어 Blob Storage에 대해서만 메트릭을 검색할 수 있습니다. 각 수준에는 해당 수준에 대해서만 메트릭을 검색하는 데 사용하는 자체 리소스 ID가 있습니다.

### <a name="resource-id-for-a-storage-account"></a>저장소 계정에 대한 리소스 ID

다음은 저장소 계정에 대한 리소스 ID를 지정하는 형식을 보여 줍니다.

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}
```

### <a name="resource-id-for-the-storage-services"></a>저장소 서비스에 대한 리소스 ID

다음은 각 저장소 서비스에 대한 리소스 ID를 지정하는 형식을 보여 줍니다.

* Blob service 리소스 ID
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default
```
* Table service 리소스 ID
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/tableServices/default
```
* 큐 서비스 리소스 ID
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default
```
* 파일 서비스 리소스 ID
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default
```

### <a name="resource-id-in-azure-monitor-rest-api"></a>Azure Monitor REST API의 리소스 ID

다음은 Azure Monitor REST API를 호출할 때 사용하는 패턴을 보여 줍니다.

```
GET {resourceId}/providers/microsoft.insights/metrics?{parameters}
```

## <a name="capacity-metrics"></a>용량 메트릭
용량 메트릭 값은 매 1시간마다 Azure Monitor에 전송됩니다. 값은 매일 새로 고쳐집니다. 시간 조직은 메트릭 값이 표시되는 시간 간격을 정의합니다. 모든 용량 메트릭에 대해 지원되는 시간 조직은 1시간(PT1H)입니다.

Azure Storage는 Azure Monitor에서 다음과 같은 용량 메트릭을 제공합니다.

### <a name="account-level"></a>계정 수준

| 메트릭 이름 | 설명 |
| ------------------- | ----------------- |
| UsedCapacity | 저장소 계정에서 사용한 저장소 양입니다. 표준 저장소 계정의 경우 이는 Blob, 테이블, 파일 및 큐에서 사용한 용량의 합계입니다. Premium Storage 계정 및 Blob Storage 계정의 경우 BlobCapacity와 같습니다. <br/><br/> 단위: 바이트 <br/> 집계 유형: 평균 <br/> 값 예제: 1024 |

### <a name="blob-storage"></a>Blob 저장소

| 메트릭 이름 | 설명 |
| ------------------- | ----------------- |
| BlobCapacity | 스토리지 계정에 사용한 Blob Storage의 총계입니다. <br/><br/> 단위: 바이트 <br/> 집계 유형: 평균 <br/> 값 예제: 1024 <br/> 차원: **BlobType**, 및 **BlobTier** ([정의](#metrics-dimensions)) |
| BlobCount    | 저장소 계정에 저장된 Blob 개체 수입니다. <br/><br/> 단위: 카운트 <br/> 집계 유형: 평균 <br/> 값 예제: 1024 <br/> 차원: **BlobType**, 및 **BlobTier** ([정의](#metrics-dimensions)) |
| ContainerCount    | 저장소 계정의 컨테이너 수입니다. <br/><br/> 단위: 카운트 <br/> 집계 유형: 평균 <br/> 값 예제: 1024 |
| IndexCapacity     | ADLS Gen2 계층적 인덱스에 사용한 스토리지 양입니다. <br/><br/> 단위: 바이트 <br/> 집계 유형: 평균 <br/> 값 예제: 1024 |

### <a name="table-storage"></a>테이블 저장소

| 메트릭 이름 | 설명 |
| ------------------- | ----------------- |
| TableCapacity | 스토리지 계정에 사용한 Table Storage 양입니다. <br/><br/> 단위: 바이트 <br/> 집계 유형: 평균 <br/> 값 예제: 1024 |
| TableCount   | 저장소 계정의 테이블 수입니다. <br/><br/> 단위: 카운트 <br/> 집계 유형: 평균 <br/> 값 예제: 1024 |
| TableEntityCount | 저장소 계정의 테이블 엔터티 수입니다. <br/><br/> 단위: 카운트 <br/> 집계 유형: 평균 <br/> 값 예제: 1024 |

### <a name="queue-storage"></a>큐 저장소

| 메트릭 이름 | 설명 |
| ------------------- | ----------------- |
| QueueCapacity | 스토리지 계정에 사용한 Queue Storage 양입니다. <br/><br/> 단위: 바이트 <br/> 집계 유형: 평균 <br/> 값 예제: 1024 |
| QueueCount   | 저장소 계정의 큐 수입니다. <br/><br/> 단위: 카운트 <br/> 집계 유형: 평균 <br/> 값 예제: 1024 |
| QueueMessageCount | 저장소 계정의 만료되지 않은 큐 메시지 수입니다. <br/><br/>단위: 카운트 <br/> 집계 유형: 평균 <br/> 값 예제: 1024 |

### <a name="file-storage"></a>File Storage

| 메트릭 이름 | 설명 |
| ------------------- | ----------------- |
| FileCapacity | 스토리지 계정에 사용한 File Storage 양입니다. <br/><br/> 단위: 바이트 <br/> 집계 유형: 평균 <br/> 값 예제: 1024 |
| FileCount   | 저장소 계정의 파일 수입니다. <br/><br/> 단위: 카운트 <br/> 집계 유형: 평균 <br/> 값 예제: 1024 |
| FileShareCount | 저장소 계정의 파일 공유 수입니다. <br/><br/> 단위: 카운트 <br/> 집계 유형: 평균 <br/> 값 예제: 1024 |

## <a name="transaction-metrics"></a>트랜잭션 메트릭

트랜잭션 메트릭은 스토리지 계정에 대한 모든 요청이 있을 때 Azure Storage에서 Azure Monitor로 내보내집니다. 스토리지 계정에 작업이 없는 경우 해당 기간에 트랜잭션 메트릭에도 데이터가 없습니다. 모든 트랜잭션 메트릭은 계정 수준과 서비스 수준에서 모두 사용할 수 있습니다(Blob Storage, Table Storage, Azure 파일 및 Queue Storage). 시간 조직은 메트릭 값이 표시되는 시간 간격을 정의합니다. 모든 트랜잭션 메트릭에 대해 지원되는 시간 조직은 PT1H 및 PT1M입니다.

Azure Storage는 Azure Monitor에서 다음과 같은 트랜잭션 메트릭을 제공합니다.

| 메트릭 이름 | 설명 |
| ------------------- | ----------------- |
| 트랜잭션 | 저장소 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. <br/><br/> 단위: 카운트 <br/> 집계 유형: 합계 <br/> 적용 가능한 차원: ResponseType, GeoType, ApiName, Authentication([정의](#metrics-dimensions))<br/> 값 예제: 1024 |
| 수신 | 수신 데이터 양입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다. <br/><br/> 단위: 바이트 <br/> 집계 유형: 합계 <br/> 적용 가능한 차원: GeoType, ApiName, Authentication([정의](#metrics-dimensions)) <br/> 값 예제: 1024 |
| 송신 | 송신 데이터 양입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다. <br/><br/> 단위: 바이트 <br/> 집계 유형: 합계 <br/> 적용 가능한 차원: GeoType, ApiName, Authentication([정의](#metrics-dimensions)) <br/> 값 예제: 1024 |
| SuccessServerLatency | Azure Storage에서 성공적인 요청을 처리하는 데 사용한 평균 시간입니다. 이 값은 SuccessE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다. <br/><br/> 단위: 밀리초 <br/> 집계 유형: 평균 <br/> 적용 가능한 차원: GeoType, ApiName, Authentication([정의](#metrics-dimensions)) <br/> 값 예제: 1024 |
| SuccessE2ELatency | 저장소 서비스 또는 지정된 API 작업에 대해 제기된 성공적인 요청의 평균 종단 간 대기 시간입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다. <br/><br/> 단위: 밀리초 <br/> 집계 유형: 평균 <br/> 적용 가능한 차원: GeoType, ApiName, Authentication([정의](#metrics-dimensions)) <br/> 값 예제: 1024 |
| 가용성 | 저장소 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 총 청구 가능 요청 값을 적용 가능한 요청 수(예기치 않은 오류를 발생시킨 요청 포함)로 나누어서 계산합니다. 모든 예기치 않은 오류는 저장소 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다. <br/><br/> 단위: 백분율 <br/> 집계 유형: 평균 <br/> 적용 가능한 차원: GeoType, ApiName, Authentication([정의](#metrics-dimensions)) <br/> 값 예제: 99.99 |

## <a name="metrics-dimensions"></a>메트릭 차원

Azure Storage는 Azure Monitor의 메트릭에 대해 다음과 같은 차원을 지원합니다.

| 차원 이름 | 설명 |
| ------------------- | ----------------- |
| **BlobType** | Blob 메트릭용 Blob 형식만. 지원 되는 값 **BlockBlob**하십시오 **PageBlob**, 및 **Azure Data Lake Storage**합니다. 추가 Blob는 BlockBlob에 포함됩니다. |
| **BlobTier** | Azure storage는 가장 비용 효율적인 방식으로 blob 개체 데이터를 저장할 수 있는 다양 한 액세스 계층을 제공 합니다. 에 대해 자세히 알아보기 [Azure Storage blob 계층](../blobs/storage-blob-storage-tiers.md)합니다. 지원 되는 값은 다음과 같습니다. <br/> <li>**핫**: 핫 계층</li> <li>**쿨**: 쿨 계층</li> <li>**보관**: 보관 계층</li> <li>**Premium**: 블록 blob에 대 한 프리미엄 계층</li> <li>**P4/P6/P10/P15/P20/P30/P40/P50/P60**: 프리미엄 페이지 blob에 대 한 계층 유형</li> <li>**표준**: 표준 페이지 Blob에 대 한 계층 유형</li> <li>**Untiered**: 범용 v1 storage 계정에 대 한 계층 유형</li> |
| **GeoType** | 기본 또는 보조 클러스터에서 전송되는 트랜잭션입니다. 사용 가능한 값을 포함 **주** 하 고 **보조**합니다. 이는 보조 테넌트에서 개체를 읽을 때 RA-GRS(Read Access Geo Redundant Storage)에 적용됩니다. |
| **ResponseType** | 트랜잭션 응답 형식입니다. 사용 가능한 값은 다음을 포함합니다. <br/><br/> <li>**ServerOtherError**: 설명한 것을 제외한 다른 모든 서버 쪽 오류입니다. </li> <li>**ServerBusyError**: HTTP 503 상태 코드를 반환한 인증된 요청입니다. </li> <li>**ServerTimeoutError**: HTTP 500 상태 코드를 반환한 시간 초과된 인증된 요청입니다. 서버 오류로 인해 시간 제한이 발생하였습니다. </li> <li>**AuthorizationError**: 무단 데이터 액세스 또는 인증 실패로 인해 실패한 인증된 요청입니다. </li> <li>**NetworkError**: 네트워크 오류로 인해 실패한 인증된 요청입니다. 가장 일반적으로 시간 제한이 만료하기 전에 클라이언트가 연결을 너무 일찍 닫은 경우에 발생합니다. </li> <li>**ClientThrottlingError**: 클라이언트 쪽 제한 오류입니다. </li> <li>**ClientTimeoutError**: HTTP 500 상태 코드를 반환한 시간 초과된 인증된 요청입니다. 클라이언트의 네트워크 시간 제한 또는 요청 시간 제한이 저장소 서비스에서 예상한 것보다 낮은 값으로 설정된 경우 이는 예상된 시간 제한입니다. 그렇지 않은 경우 이는 ServerTimeoutError로 보고됩니다. </li> <li>**ClientOtherError**: 설명한 것을 제외한 다른 모든 클라이언트 쪽 오류입니다. </li> <li>**성공**: 성공한 요청입니다.</li> |
| **ApiName** | 작업 이름입니다. 예를 들면 다음과 같습니다. <br/> <li>**CreateContainer**</li> <li>**DeleteBlob**</li> <li>**GetBlob**</li> 모든 작업 이름은 [문서](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)를 참조하세요. |
| **인증** | 트랜잭션에 사용되는 인증 형식입니다. 사용 가능한 값은 다음을 포함합니다. <br/> <li>**AccountKey**: 트랜잭션이 스토리지 계정 키를 사용하여 인증됩니다.</li> <li>**SAS**: 트랜잭션이 공유 액세스 서명을 사용하여 인증됩니다.</li> <li>**OAuth**: 트랜잭션이 OAuth 액세스 토큰을 사용하여 인증됩니다.</li> <li>**익명**: 트랜잭션이 익명으로 요청됩니다. 실행 전 요청은 포함되지 않습니다.</li> <li>**AnonymousPreflight**: 트랜잭션이 실행 전 요청입니다.</li> |

메트릭 지원 차원의 경우 해당 메트릭 값을 보려면 차원 값을 지정해야 합니다. 예를 들어 성공적인 응답에 대한 **트랜잭션** 값을 조사하는 경우 **성공**을 포함한 **ResponseType** 차원을 필터링해야 합니다. 또는 블록 Blob에 대한 **BlobCount** 값을 조사하는 경우 **BlockBlob**을 포함한 **BlobType** 차원을 필터링해야 합니다.

## <a name="service-continuity-of-legacy-metrics"></a>레거시 메트릭의 서비스 연속성

레거시 메트릭은 Azure Monitor 관리되는 메트릭과 동시에 사용할 수 있습니다. 이 지원은 Azure Storage가 레거시 메트릭에 대한 서비스를 종료할 때까지 동일하게 유지됩니다.

## <a name="faq"></a>FAQ

**새 메트릭에서 클래식 스토리지 계정을 지원하나요?**

아니요, Azure Monitor의 새 메트릭은 Azure Resource Manager 스토리지 계정만 지원합니다. 스토리지 계정에서 메트릭을 사용하려면 Azure Resource Manager 스토리지 계정으로 마이그레이션해야 합니다. [Azure Resource Manager로 마이그레이션](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview)을 참조하세요.

**Azure Storage가 Managed Disks 또는 Unmanaged Disks에 대한 메트릭을 지원하나요?**

예. Azure Compute는 디스크에서 메트릭을 지원합니다. 자세한 내용은 [문서](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/)를 참조하세요.

**새 메트릭을 사용하여 클래식 메트릭을 어떻게 매핑 및 마이그레이션하나요?**

[Azure Storage 메트릭 마이그레이션](./storage-metrics-migration.md)에서 클래식 메트릭과 새 메트릭 간의 자세한 매핑을 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
