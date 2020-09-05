---
title: Azure Storage 모니터링 | Microsoft Docs
description: Azure Storage의 성능 및 가용성을 모니터링하는 방법을 알아봅니다. Azure Storage 데이터를 모니터링 하 고, 구성에 대해 알아보고, 메트릭 및 로그 데이터를 분석 합니다.
author: normesta
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: 2cc275028b64c3df06e2c0275bc0f4ac21fa0f6e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020153"
---
# <a name="monitor-azure-storage"></a>Azure Storage 모니터링

Azure 리소스를 사용하는 중요한 애플리케이션 및 비즈니스 프로세스가 있으면 이러한 리소스의 가용성, 성능 및 작업을 모니터링해야 합니다. 이 문서에서는 Azure Storage에서 생성하는 모니터링 데이터 및 Azure Monitor 기능을 사용하여 이러한 데이터에 대한 경고를 분석하는 방법을 설명합니다.

> [!NOTE]
> Azure Monitor의 Azure Storage 로그는 현재 공개 미리 보기이며 모든 퍼블릭 클라우드 지역에서 미리 보기 테스트에 사용할 수 있습니다. 미리 보기에 등록하려면 [이 페이지](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)를 참조하세요. 이 미리 보기는 blob (Azure Data Lake Storage Gen2 포함), 파일, 큐 및 테이블에 대 한 로그를 사용 하도록 설정 합니다. 이 기능은 Azure Resource Manager 배포 모델을 사용 하 여 만든 모든 저장소 계정에 사용할 수 있습니다. [저장소 계정 개요](../common/storage-account-overview.md)를 참조 하세요.

## <a name="monitor-overview"></a>모니터링 개요

각 스토리지 리소스에 대한 Azure Portal의 **개요** 페이지에는 요청 및 시간당 청구와 같은 리소스 사용에 대한 간략한 보기가 있습니다. 이 정보는 유용하지만, 사용할 수 있는 모니터링 데이터의 양이 적습니다. 이 데이터 중 일부는 자동으로 수집되며, 스토리지 리소스를 만드는 즉시 분석에 사용할 수 있습니다. 약간의 구성을 통해 추가적인 데이터 수집 형식을 사용할 수 있습니다.

## <a name="what-is-azure-monitor"></a>Azure Monitor란?
Azure Storage는 Azure의 전체 스택 모니터링 서비스인 [Azure Monitor](../../azure-monitor/overview.md)를 사용하여 모니터링 데이터를 만듭니다. Azure Monitor는 Azure 리소스와 다른 클라우드 및 온-프레미스의 리소스를 모니터링하는 완전한 기능 세트를 제공합니다. 

Azure Monitor에 대한 자세한 내용은 [Azure Monitor를 사용하여 Azure 리소스 모니터링](../../azure-monitor/insights/monitor-azure-resource.md)을 참조하세요. 이 문서에서는 다음 주제에 대해 설명합니다.

- Azure Monitor란?
- 모니터링과 관련된 비용
- Azure에서 수집된 데이터 모니터링
- 데이터 수집 구성
- 모니터링 데이터를 분석하고 경고하는 Azure의 표준 도구

본 문서의 다음 섹션에서는 Azure Storage에서 수집하는 데이터에 대해 설명합니다. 그리고 Azure 도구를 사용하여 데이터 수집을 구성하고 수집한 데이터를 분석하는 방법을 보여주는 예제를 제공합니다.

## <a name="monitor-data-from-azure-storage"></a>Azure Storage에서 데이터 모니터링

Azure Storage는 다른 Azure 리소스와 동일한 종류의 모니터링 데이터를 수집합니다. 이에 대한 내용은 [Azure 리소스의 데이터 모니터링](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data)에 설명되어 있습니다. Azure Storage에서 만든 로그 및 메트릭에 대한 자세한 내용은 [Azure Storage 모니터링 데이터 참조](monitor-storage-reference.md)에서 확인할 수 있습니다.

Azure Monitor의 메트릭과 로그는 Azure Resource Manager 스토리지 계정만 지원합니다. Azure Monitor는 클래식 스토리지 계정을 지원하지 않습니다. 클래식 스토리지 계정에서 메트릭 또는 로그를 사용하려면 Azure Resource Manager 스토리지 계정으로 마이그레이션해야 합니다. [Azure Resource Manager로 마이그레이션](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview)을 참조하세요.

원한다면 클래식 메트릭 및 로그를 계속 사용할 수 있습니다. 실제로 클래식 메트릭 및 로그는 Azure Monitor의 메트릭 및 로그와 동시에 사용할 수 있습니다. 이 지원은 Azure Storage가 레거시 메트릭 및 로그에 대한 서비스를 종료할 때까지 유지됩니다.

### <a name="logs-in-azure-monitor-preview"></a>Azure Monitor의 로그(미리 보기)

서비스 엔드포인트에 대한 요청이 있는 경우에만 로그 항목이 만들어집니다. 예를 들어 스토리지 계정에 BLOB 엔드포인트의 활동은 있지만 테이블 또는 큐 엔드포인트의 활동이 없는 경우 BLOB 서비스와 관련된 로그만 만들어집니다. Azure Storage는 스토리지 서비스에 대해 성공한 요청과 실패한 요청의 상세 정보를 기록합니다. 이 정로를 사용하면 개별 요청을 모니터링하고 스토리지 서비스의 문제를 진단할 수 있습니다. 요청은 최상의 노력을 기준으로 기록됩니다.

#### <a name="log-authenticated-requests"></a>인증된 요청 기록

 다음과 같은 유형의 인증된 요청이 기록됩니다.

- 성공한 요청
- 실패한 요청(시간 제한, 제한, 네트워크, 권한 부여 및 기타 오류)
- 실패한 요청과 성공한 요청을 포함하여 SAS(공유 액세스 서명) 또는 OAuth를 사용하는 요청
- 분석 데이터( **$logs** 컨테이너의 클래식 로그 데이터 및 **$metric** 테이블의 클래식 메트릭 데이터)에 대한 요청

로그 만들기 또는 삭제처럼 스토리지 서비스 자체에서 만든 요청은 기록되지 않습니다. 기록되는 데이터의 전체 목록은 [Storage에서 기록한 작업 및 상태 메시지](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)와 [Storage 로그 형식](monitor-storage-reference.md)을 참조하세요.

#### <a name="log-anonymous-requests"></a>익명 요청 기록

 다음과 같은 유형의 익명 요청이 기록됩니다.

- 성공한 요청
- 서버 오류
- 클라이언트와 서버 모두에 대한 시간 제한 오류
- 304(수정되지 않음) 오류 코드와 함께 실패한 GET 요청

그 외의 실패한 익명 요청은 기록되지 않습니다. 기록되는 데이터의 전체 목록은 [Storage에서 기록한 작업 및 상태 메시지](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)와 [Storage 로그 형식](monitor-storage-reference.md)을 참조하세요.

## <a name="configuration"></a>구성

플랫폼 메트릭과 활동 로그는 자동으로 수집되지만, 리소스 로그를 수집하거나 Azure Monitor 외부로 전달하려면 진단 설정을 만들어야 합니다. Azure Portal, Azure CLI 또는 PowerShell을 사용하여 진단 설정을 만드는 프로세스는 [Azure에서 플랫폼 로그 및 메트릭을 수집하는 진단 설정 만들기](../../azure-monitor/platform/diagnostic-settings.md)를 참조하세요.

진단 설정을 만들 때 로그를 사용할 스토리지 유형(예: BLOB, 큐, 테이블, 파일)을 선택하세요. Data Lake Storage Gen2은 저장소 유형으로 표시 되지 않습니다. Data Lake Storage Gen2는 Blob Storage에서 사용할 수 있는 기능 집합 이기 때문입니다. 

Azure Portal에서 진단 설정을 만드는 경우 목록에서 리소스를 선택할 수 있습니다. PowerShell 또는 Azure CLI를 사용하는 경우 스토리지 유형의 리소스 ID를 사용해야 합니다. 리소스 ID는 Azure Portal에서 스토리지 계정의 **속성** 페이지를 열어 확인할 수 있습니다.

로그를 수집하려는 작업의 범주도 지정해야 합니다. 다음 표에는 Azure Storage의 범주가 나와 있습니다.

| 범주 | Description |
|:---|:---|
| StorageRead | 개체에 대 한 읽기 작업입니다. |
| StorageWrite | 개체에 대 한 쓰기 작업입니다. |
| StorageDelete | 개체에 대 한 삭제 작업입니다. |

## <a name="analyze-metric-data"></a>메트릭 데이터 분석

메트릭 탐색기를 사용하여 다른 Azure 서비스의 메트릭과 함께 Azure Storage의 메트릭을 분석할 수 있습니다. **Azure Monitor** 메뉴에서 **메트릭**을 선택하여 메트릭 탐색기를 엽니다. 이 도구 사용에 대한 자세한 내용은 [Azure 메트릭 탐색기 시작](../../azure-monitor/platform/metrics-getting-started.md)을 참조하세요. 

다음 예제는 계정 수준에서 **트랜잭션**을 보는 방법을 보여줍니다.

![Azure Portal에서 메트릭에 액세스하는 스크린 샷](./media/monitor-storage/access-metrics-portal.png)

차원을 지원하는 메트릭의 경우 원하는 차원 값을 사용하여 메트릭을 필터링할 수 있습니다. 다음 예제는 **API 이름** 차원의 값을 선택하여 특정 작업의 계정 수준에서 **트랜잭션**을 보는 방법을 보여줍니다.

![Azure Portal에서 차원을 사용하여 메트릭에 액세스하는 스크린 샷](./media/monitor-storage/access-metrics-portal-with-dimension.png)

Azure Storage에서 지원하는 차원의 전체 목록은 [메트릭 차원](monitor-storage-reference.md#metrics-dimensions)을 참조하세요.

Azure Storage에 대한 모든 메트릭은 다음 네임스페이스에 있습니다.

- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/blobServices
- Microsoft.Storage/storageAccounts/fileServices
- Microsoft.Storage/storageAccounts/queueServices
- Microsoft.Storage/storageAccounts/tableServices

Azure Storage를 비롯한 모든 Azure Monitor 지원 메트릭의 목록은 [Azure Monitor에서 지원하는 메트릭](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)을 참조하세요.


### <a name="access-metrics"></a>메트릭에 액세스

> [!TIP]
> Azure CLI 또는 .NET 예제를 보려면 아래의 탭 중에서 해당 탭을 선택합니다.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>메트릭 정의 나열

스토리지 계정 또는 BLOB, 파일, 테이블, 큐 서비스 등과 같은 개별 스토리지 서비스의 메트릭 정의를 나열할 수 있습니다. [Get-AzMetricDefinition](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricdefinition?view=azps-3.3.0)을 사용합니다.

이 예제에서는 `<resource-ID>` 자리 표시자를 전체 스토리지 계정의 리소스 ID 또는 BLOB, 파일, 테이블, 큐 서비스와 같은 개별 스토리지 서비스의 리소스 ID로 바꿉니다. 리소스 ID는 Azure Portal에서 스토리지 계정의 **속성** 페이지에서 확인할 수 있습니다.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="read-metric-values"></a>메트릭 값 읽기

스토리지 계정 또는 BLOB, 파일, 테이블, 큐 서비스 등과 같은 개별 스토리지 서비스의 계정 수준 메트릭 값을 읽을 수 있습니다. [Get-AzMetric](https://docs.microsoft.com/powershell/module/Az.Monitor/Get-AzMetric?view=azps-3.3.0) cmdlet을 사용합니다.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="list-the-account-level-metric-definition"></a>계정 수준 메트릭 정의 나열

스토리지 계정 또는 BLOB, 파일, 테이블, 큐 서비스 등과 같은 개별 스토리지 서비스의 메트릭 정의를 나열할 수 있습니다. [az monitor metrics list-definitions](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list-definitions) 명령을 사용합니다.
 
이 예제에서는 `<resource-ID>` 자리 표시자를 전체 스토리지 계정의 리소스 ID 또는 BLOB, 파일, 테이블, 큐 서비스와 같은 개별 스토리지 서비스의 리소스 ID로 바꿉니다. 리소스 ID는 Azure Portal에서 스토리지 계정의 **속성** 페이지에서 확인할 수 있습니다.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>계정 수준 메트릭 값 읽기

스토리지 계정 또는 BLOB, 파일, 테이블, 큐 서비스 등과 같은 개별 스토리지 서비스의 메트릭 값을 읽을 수 있습니다. [az monitor metrics list](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) 명령을 사용합니다.

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/dotnet)

Azure Monitor는 메트릭 정의 및 값을 읽는 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/)를 제공합니다. [샘플 코드](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/)는 다른 매개 변수로 SDK를 사용하는 방법을 보여줍니다. 스토리지 메트릭을 사용하려면 `0.18.0-preview` 이상의 버전을 사용해야 합니다.
 
이 예제에서는 `<resource-ID>` 자리 표시자를 전체 스토리지 계정의 리소스 ID 또는 BLOB, 파일, 테이블, 큐 서비스와 같은 개별 스토리지 서비스의 리소스 ID로 바꿉니다. 리소스 ID는 Azure Portal에서 스토리지 계정의 **속성** 페이지에서 확인할 수 있습니다.

`<subscription-ID>` 변수를 구독의 ID로 바꿉니다. `<tenant-ID>`, `<application-ID>` 및 `<AccessKey>` 값을 얻는 방법에 대한 지침은 [포털을 사용하여 리소스에 액세스할 수 있는 Azure AD 애플리케이션 및 서비스 주체 만들기](https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/)를 참조하세요. 

#### <a name="list-the-account-level-metric-definition"></a>계정 수준 메트릭 정의 나열

다음 예제는 계정 수준에서 메트릭 정의를 나열하는 방법을 보여줍니다.

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";


        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            // Enumrate metric definition:
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

#### <a name="read-account-level-metric-values"></a>계정 수준 메트릭 값 읽기

다음 예제는 계정 수준에서 `UsedCapacity` 데이터를 읽는 방법을 보여줍니다.

```csharp
    public static async Task ReadStorageMetricValue()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

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
            // Enumrate metric value
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

#### <a name="read-multidimensional-metric-values"></a>다차원 메트릭 값 읽기

다차원 메트릭에서 특정 차원 값에 대한 메트릭 데이터를 읽으려면 메타데이터 필터를 정의해야 합니다.

다음 예제는 다차원을 지원하는 메트릭에서 메트릭 데이터를 읽는 방법을 보여줍니다.

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
        var subscriptionId = "<subscription-ID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

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

---

## <a name="analyze-log-data"></a>로그 데이터 분석

리소스 로그는 스토리지 계정의 BLOB으로, 이벤트 데이터로 또는 로그 분석 쿼리를 통해 액세스할 수 있습니다.

이러한 로그에 표시되는 필드에 대한 자세한 내용은 [Azure Storage 모니터링 데이터 참조](monitor-storage-reference.md)에서 확인할 수 있습니다.

> [!NOTE]
> Azure Monitor의 Azure Storage 로그는 현재 공개 미리 보기이며 모든 퍼블릭 클라우드 지역에서 미리 보기 테스트에 사용할 수 있습니다. 미리 보기에 등록하려면 [이 페이지](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)를 참조하세요. 이 미리 보기에서는 BLOB(Azure Data Lake Storage Gen2 포함), 파일, 큐, 테이블, 범용 v1의 프리미엄 스토리지 계정, 범용 v2 스토리지 계정에 로그를 사용할 수 있습니다. 클래식 스토리지 계정은 지원되지 않습니다.

### <a name="access-logs-in-a-storage-account"></a>스토리지 계정에서 로그 액세스

로그는 대상 스토리지 계정의 컨테이너에 저장된 BLOB으로 표시됩니다. 데이터는 단일 BLOB 내에 줄로 구분된 JSON 페이로드로 수집 및 저장됩니다. BLOB 이름은 다음가 같은 명명 규칙을 따릅니다.

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/blobServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

예를 들면 다음과 같습니다.

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="access-logs-in-an-event-hub"></a>이벤트 허브에서 로그 액세스

이벤트 허브에 전송된 로그는 파일로 저장되지 않지만, 이벤트 허브에서 로그 정보를 받았는지 확인할 수 있습니다. Azure Portal에서 이벤트 허브로 이동하여 **들어오는 메시지** 수가 0보다 큰지 확인하면 됩니다. 

![감사 로그](media/monitor-storage/event-hub-log.png)

보안 정보와 이벤트 관리 및 모니터링 도구를 사용하여 이벤트 허브로 전송된 로그 데이터에 액세스하여 읽을 수 있습니다. 자세한 내용은 [내 이벤트 허브로 보내지는 모니터링 데이터를 사용하여 수행할 수 있는 작업은 무엇인가요?](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)를 참조하세요.

### <a name="access-logs-in-a-log-analytics-workspace"></a>Log Analytics 작업 영역에서 로그 액세스

Azure Monitor 로그 쿼리를 사용하여 Log Analytics 작업 영역으로 전송된 로그에 액세스할 수 있습니다.

자세한 내용은 [Azure Monitor에서 Log Analytics 시작](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)을 참조하세요.

데이터는 다음 테이블에 저장됩니다.

| 테이블 | Description |
|:---|:---|
|StorageBlobLogs | BLOB 스토리지의 작업을 설명하는 로그입니다. |
|StorageFileLogs | 파일 공유의 작업을 설명하는 로그입니다. |
|StorageQueueLogs | 큐의 작업을 설명하는 로그입니다.|
|StorageTableLogs| 테이블의 작업을 설명하는 로그입니다.|

Data Lake Storage Gen2에 대 한 로그는 전용 테이블에 표시 되지 않습니다. Data Lake Storage Gen2은 서비스 되지 않기 때문입니다. Blob storage 계정에서 사용할 수 있는 기능 집합입니다. 이러한 기능을 사용 하도록 설정 하면 로그가 StorageBlobLogs 테이블에 계속 표시 됩니다. 

### <a name="azure-storage-log-analytics-queries-in-azure-monitor"></a>Azure Monitor의 Azure Storage Log Analytics 쿼리

다음 쿼리를 **로그 검색창**에 입력하면 Azure Storage 계정을 모니터링하는 데 도움이 됩니다. 이러한 쿼리는 [새 언어](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)에서 작동합니다.

다음 쿼리를 사용하여 Azure Storage 계정을 모니터링할 수 있습니다.

* 아래는 지난 사흘 동안 가장 많이 발생한 상위 10개 오류를 나열하는 쿼리입니다.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```
* 아래는 지난 사흘 동안 가장 많은 오류를 일으킨 상위 10개 작업을 나열하는 쿼리입니다.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```
* 아래는 지난 사흘 동안 엔드투엔드 대기 시간이 가장 길었던 상위 10개 작업을 나열하는 쿼리입니다.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```
* 아래는 지난 사흘 동안 서버 쪽 제한 오류를 일으킨 모든 작업을 나열하는 쿼리입니다.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText contains "ServerBusy"
    | project TimeGenerated, OperationName, StatusCode, StatusText
    ```
* 아래는 지난 사흘 동안 익명 액세스를 사용한 모든 요청을 나열하는 쿼리입니다.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and AuthenticationType == "Anonymous"
    | project TimeGenerated, OperationName, AuthenticationType, Uri
    ```
* 아래는 지난 사흘 동안 사용된 작업의 원형 차트를 만드는 쿼리입니다.
    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc 
    | render piechart
    ```
## <a name="faq"></a>FAQ

**Azure Storage가 Managed Disks 또는 Unmanaged Disks에 대한 메트릭을 지원하나요?**

아니요. Azure Compute는 디스크에 대한 메트릭을 지원합니다. 자세한 내용은 [관리형 디스크와 비관리형 디스크의 디스크당 메트릭](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- Azure Storage에서 만든 로그 및 메트릭의 참조는 [Azure Storage 모니터링 데이터 참조](monitor-storage-reference.md)에서 확인할 수 있습니다.
- Azure 리소스 모니터링에 대한 자세한 내용은 [Azure Monitor를 사용한 Azure 리소스 모니터링](../../azure-monitor/insights/monitor-azure-resource.md)을 참조하세요.
- 메트릭 마이그레이션에 대한 자세한 내용은 [Azure Storage 메트릭 마이그레이션](./storage-metrics-migration.md)을 참조하세요.
