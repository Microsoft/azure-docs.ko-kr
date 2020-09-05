---
title: 진단 로그
titleSuffix: Azure Content Delivery Network
description: Azure 진단 로그를 사용 하 여 Azure Content Delivery Network 끝점에서 사용 메트릭을 내보낼 수 있는 핵심 분석을 저장 하는 방법을 알아봅니다.
services: cdn
author: asudbring
manager: KumudD
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: allensu
ms.openlocfilehash: 164560fff27adc2d4e63cc8471a26d1d710b89a5
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88191276"
---
# <a name="diagnostic-logs---azure-content-delivery-network"></a>진단 로그-Azure Content Delivery Network

Azure 진단 로그를 통해 이제 핵심 분석을 보고 다음을 포함한 하나 이상의 대상에 저장할 수 있습니다.

* Azure Storage 계정
* Log Analytics 작업 영역
* Azure Event Hubs

이 기능은 모든 가격 책정 계층에 대한 CDN 엔드포인트에서 사용할 수 있습니다. 

진단 로그를 사용 하면 사용자 지정 된 방식으로 사용할 수 있도록 CDN 끝점에서 다양 한 종류의 원본으로 기본 사용 메트릭을 내보낼 수 있습니다. 다음 유형의 데이터 내보내기를 수행할 수 있습니다.

* 데이터를 Blob Storage로 내보내고, CSV로 내보낸 후 Excel에서 그래프를 생성합니다.
* 데이터를 Event Hubs로 내보내고 다른 Azure 서비스의 데이터와 상관 관계를 설정합니다.
* Azure Monitor 로그로 데이터 내보내기 및 사용자 고유의 Log Analytics 작업 영역에서 데이터 보기

Azure CDN 프로필은 다음 단계를 수행 하는 데 필요 합니다. 계속 하기 전에 [Azure CDN 프로필 및 끝점 만들기](cdn-create-new-endpoint.md) 를 참조 하세요.

## <a name="enable-logging-with-the-azure-portal"></a>Azure Portal에서 로깅을 사용하도록 설정

Azure CDN 끝점에 대 한 로깅을 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 

2. Azure Portal에서 **모든 리소스**  ->  **-cdn-프로필** 로 이동 합니다.

2. 진단 로그를 사용 하도록 설정 하려는 CDN 끝점을 선택 합니다.

    :::image type="content" source="./media/cdn-diagnostics-log/02_browse-to-diagnostics-logs.png" alt-text="CDN 끝점을 선택 합니다." border="true":::

3. **모니터링** 섹션에서 **진단 로그** 를 선택 합니다.

    :::image type="content" source="./media/cdn-diagnostics-log/03_diagnostics-logs-options.png" alt-text="진단 로그를 선택 합니다." border="true":::

### <a name="enable-logging-with-azure-storage"></a>Azure Storage에서 로깅을 사용하도록 설정

스토리지 계정을 사용하여 로그를 저장하려면 아래 단계를 따릅니다.

 >[!NOTE] 
 >이러한 단계를 완료 하려면 저장소 계정이 필요 합니다. 자세한 내용은 **[Azure Storage 계정 만들기](https://docs.microsoft.com/azure/storage/common/storage-account-create?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal)** 를 참조 하세요.
    
1. **진단 설정 이름**에 진단 로그 설정의 이름을 입력 합니다.
 
2. **스토리지 계정에 보관**을 선택한 다음, **CoreAnalytics**을 선택합니다. 

3. **보존(일)** 의 경우 보존 일 수를 선택합니다. 0일의 보존 기간은 로그를 무기한 저장합니다. 

4. 로그에 대 한 구독 및 저장소 계정을 선택 합니다.

    :::image type="content" source="./media/cdn-diagnostics-log/04_diagnostics-logs-storage.png" alt-text="진단 로그-저장소." border="true":::

3. **저장**을 선택합니다.

### <a name="send-to-log-analytics"></a>Log Analytics에 보내기

로그에 Log Analytics을 사용 하려면 다음 단계를 수행 합니다.

>[!NOTE] 
>이러한 단계를 완료 하려면 log analytics 작업 영역이 필요 합니다. 자세한 내용은 **[Azure Portal에서 Log Analytics 작업 영역 만들기](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)** 를 참조 하세요.
    
1. **진단 설정 이름**에 진단 로그 설정의 이름을 입력 합니다.

2. **Log Analytics 보내기를 선택 하**고 **CoreAnalytics**를 선택 합니다. 

3. 로그에 대 한 구독 및 Log Analytics 작업 영역을 선택 합니다.

   :::image type="content" source="./media/cdn-diagnostics-log/05-la-workspace.png" alt-text="진단 로그-Log Analytics." border="true":::

4. **저장**을 선택합니다.

### <a name="stream-to-an-event-hub"></a>이벤트 허브로 스트림

로그에 이벤트 허브를 사용 하려면 다음 단계를 수행 합니다.

>[!NOTE] 
>이러한 단계를 완료 하려면 이벤트 허브가 필요 합니다. 자세한 내용은 **[빠른 시작: Azure Portal을 사용 하 여 이벤트 허브 만들기](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)** 를 참조 하세요.
    
1. **진단 설정 이름**에 진단 로그 설정의 이름을 입력 합니다.

2. **이벤트 허브로 스트림**을 선택한 다음 **CoreAnalytics**를 선택 합니다. 

3. 로그에 대 한 구독 및 이벤트 허브 네임 스페이스를 선택 합니다.

   :::image type="content" source="./media/cdn-diagnostics-log/06-eventhub-namespace.png" alt-text="진단 로그-이벤트 허브" border="true":::

4. **저장**을 선택합니다.


## <a name="enable-logging-with-powershell"></a>PowerShell을 통해 로깅을 사용하도록 설정

다음 예제는 Azure PowerShell Cmdlet을 통해 진단 로그를 사용하도록 설정하는 방법을 보여줍니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enable-diagnostic-logs-in-a-storage-account"></a>저장소 계정에서 진단 로그 사용

1. Azure PowerShell에 로그인 합니다.

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```

2. 저장소 계정에서 진단 로그를 사용 하도록 설정 하려면 다음 명령을 입력 합니다. 변수를 값으로 바꿉니다.

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $storageacct = <your-storage-account-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $storage = Get-AzStorageAccount -ResourceGroupName $rsg -Name $storageacct

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -StorageAccountId $storage.id -Enabled $true -Categories CoreAnalytics
    ```

### <a name="enable-diagnostics-logs-for-log-analytics-workspace"></a>Log Analytics 작업 영역에 대 한 진단 로그 사용

1. Azure PowerShell에 로그인 합니다.

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Log Analytics 작업 영역에 대 한 진단 로그를 사용 하도록 설정 하려면 다음 명령을 입력 합니다. 변수를 값으로 바꿉니다.

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $workspacename = <your-log-analytics-workspace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $rsg -Name $workspacename

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -WorkspaceId $workspace.ResourceId -Enabled $true -Categories CoreAnalytics
    ```
### <a name="enable-diagnostics-logs-for-event-hub-namespace"></a>이벤트 허브 네임 스페이스에 대 한 진단 로그 사용

1. Azure PowerShell에 로그인 합니다.

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Log Analytics 작업 영역에 대 한 진단 로그를 사용 하도록 설정 하려면 다음 명령을 입력 합니다. 변수를 값으로 바꿉니다.

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $eventhubname = <your-event-hub-namespace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -EventHubName $eventhubname -Enabled $true -Categories CoreAnalytics
    ```

## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Azure Storage에서 진단 로그 사용
이 섹션에서는 CDN 핵심 분석의 스키마, Azure storage 계정의 구성에 대해 설명 하 고, 로그를 CSV 파일로 다운로드 하기 위한 샘플 코드를 제공 합니다.

### <a name="using-microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer 사용
도구를 다운로드하려면 [Azure Storage Explorer](https://storageexplorer.com/)를 참조하세요. 소프트웨어를 다운로드하여 설치한 후에는 CDN 진단 로그의 대상으로 구성된 동일한 Azure Storage 계정을 사용하도록 구성합니다.

1.  **Microsoft Azure Storage Explorer**를 엽니다.
2.  스토리지 계정을 찾습니다.
3.  이 스토리지 계정 아래의 **Blob 컨테이너** 노드를 확장합니다.
4.  *“insights-logs-coreanalytics”* 라는 컨테이너를 선택합니다.
5.  오른쪽 창에 결과가 표시되고 *resourceId=* 와 같은 첫 번째 수준에서 시작됩니다. *PT1H.json* 파일을 찾을 때까지 각 수준을 계속해서 선택합니다. 경로에 대한 설명은 [Blob 경로 형식](cdn-azure-diagnostic-logs.md#blob-path-format)을 참조하세요.
6.  각 blob *PT1H.json* 파일은 특정 CDN 엔드포인트 또는 사용자 지정 도메인에 대해 1시간의 분석 로그를 표시합니다.
7.  이 JSON 파일 콘텐츠의 스키마는 핵심 분석 로그의 스키마 섹션에 설명되어 있습니다.


#### <a name="blob-path-format"></a>Blob 경로 형식

핵심 분석 로그는 1시간 마다 생성되고 데이터는 JSON 페이로드로 단일 Azure blob 내부에 수집되고 저장됩니다. Storage 탐색기 도구는 '/'를 디렉터리 구분 기호로 해석 하 고 계층 구조를 표시 합니다. Azure blob에 대 한 경로는 계층 구조가 있고 blob 이름을 나타내는 것 처럼 표시 됩니다. blob의 이름은 다음 명명 규칙을 따릅니다.    

```resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json```

**필드 설명:**

|값|설명|
|-------|---------|
|구독 ID    |GUID 형식에서 Azure 구독의 ID입니다.|
|리소스 그룹 이름 |CDN 리소스가 속한 리소스 그룹의 이름입니다.|
|프로필 이름 |CDN 프로필의 이름입니다.|
|엔드포인트 이름 |CDN 엔드포인트의 이름입니다.|
|Year|  4자리 연도 표시(예: 2017)입니다.|
|월| 2자리 월 표시입니다. 01 = 1 월 ... 12 = 12 월|
|일|   2자리 월의 일 표시입니다.|
|PT1H.json| 분석 데이터가 저장되는 실제 JSON 파일입니다.|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>핵심 분석 데이터를 CSV 파일로 내보내기

핵심 분석에 액세스 하기 위해 도구의 샘플 코드가 제공 됩니다. 이 도구를 사용하여 JSON 파일을 쉼표로 구분된 일반 파일 형식으로 다운로드한 다음, 차트를 만들거나 기타 집계를 수행하는 데 사용할 수 있습니다.

도구를 사용하는 방법은 다음과 같습니다.

1.  GitHub 링크를 방문 합니다. [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  코드를 다운로드합니다.
3.  지침에 따라 컴파일 및 구성합니다.
4.  도구를 실행합니다.
5.  결과 CSV 파일은 분석 데이터를 간단한 평면 계층으로 표시합니다.

## <a name="log-data-delays"></a>로그 데이터 지연

다음 표에는 **Microsoft의 Azure CDN 표준**, **Akamai의 Azure CDN 표준** 및 **Verizon의 Azure CDN 표준/프리미엄**에 대한 로그 데이터 지연이 표시됩니다.

Microsoft 로그 데이터 지연 | Verizon 로그 데이터 지연 | Akamai 로그 데이터 지연
--- | --- | ---
1시간 지연됩니다. | 1시간 지연되고, 엔드포인트 전파가 완료된 후 나타날 때까지 최대 2시간이 걸릴 수 있습니다. | 24시간 지연되고, 24시간 이전에 만들어진 경우 나타날 때까지 최대 2시간이 걸립니다. 최근에 만든 경우 로그가 나타날 때까지 최대 25시간이 걸릴 수 있습니다.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>CDN 핵심 분석에 대한 진단 로그 유형

현재 Microsoft는 CDN POP/Edge에서 볼 수 있는 HTTP 응답 통계 및 송신 통계를 보여주는 메트릭이 포함된 핵심 분석 로그만 제공하고 있습니다.

### <a name="core-analytics-metrics-details"></a>핵심 분석 메트릭 정보
다음 표에서는의 핵심 분석 로그에서 사용할 수 있는 메트릭의 목록을 보여 줍니다.

* **Microsoft의 Azure CDN Standard**
* **Akamai의 Azure CDN Standard**
* **Verizon의 Azure CDN Standard/Premium**

모든 공급자의 모든 메트릭을 사용할 수 있는 것은 아니지만 이러한 차이는 미미합니다. 이 표에는 지정된 메트릭을 공급자에서 사용할 수 있는지 여부도 나와 있습니다. 메트릭은 트래픽이 있는 해당 CDN 엔드포인트에 대해서만 사용할 수 있습니다.


|메트릭                     | 설명 | Microsoft | Verizon | Akamai |
|---------------------------|-------------|-----------|---------|--------|
| RequestCountTotal         | 이 기간 동안의 요청 적중의 총 수입니다. | 예 | 예 |예 |
| RequestCountHttpStatus2xx | 2xx HTTP 코드(예: 200, 202)를 생성한 모든 요청의 수입니다. | 예 | 예 |예 |
| RequestCountHttpStatus3xx | 3xx HTTP 코드(예: 300, 302)를 생성한 모든 요청의 수입니다. | 예 | 예 |예 |
| RequestCountHttpStatus4xx | 4xx HTTP 코드(예: 400, 404)를 생성한 모든 요청의 수입니다. | 예 | 예 |예 |
| RequestCountHttpStatus5xx | 5xx HTTP 코드(예: 500, 504)를 생성한 모든 요청의 수입니다. | 예 | 예 |예 |
| RequestCountHttpStatusOthers | 다른 모든 HTTP 코드의 수(2xx-5xx 이외)입니다. | 예 | 예 |예 |
| RequestCountHttpStatus200 | 200 HTTP 코드 응답을 생성한 모든 요청의 수입니다. | 예 | 아니요  |예 |
| RequestCountHttpStatus206 | 206 HTTP 코드 응답을 생성한 모든 요청의 수입니다. | 예 | 아니요  |예 |
| RequestCountHttpStatus302 | 302 HTTP 코드 응답을 생성한 모든 요청의 수입니다. | 예 | 아니요  |예 |
| RequestCountHttpStatus304 | 304 HTTP 코드 응답을 생성한 모든 요청의 수입니다. | 예 | 아니요  |예 |
| RequestCountHttpStatus404 | 404 HTTP 코드 응답을 생성한 모든 요청의 수입니다. | 예 | 아니요  |예 |
| RequestCountCacheHit | 캐시 적중을 발생한 모든 요청의 수. 자산이 POP에서 클라이언트로 직접 제공되었습니다. | 예 | 예 | 아니요  |
| RequestCountCacheMiss | 캐시 누락을 발생한 모든 요청의 수. 캐시 누락은 자산을 클라이언트와 가장 가까운 POP에서 찾을 수 없고 원본에서 검색 했음을 의미 합니다. | 예 | 예 | 아니요 |
| RequestCountCacheNoCache | 에 지에 대 한 사용자 구성 때문에 캐시 되지 못하도록 방지 된 자산에 대 한 모든 요청의 수입니다. | 예 | 예 | 아니요 |
| RequestCountCacheUncacheable | 자산의 Cache-control 및 Expires 헤더에 의해 캐시 되지 못하도록 하는 자산에 대 한 모든 요청의 수입니다. 이 개수는 POP 또는 HTTP 클라이언트에 의해 캐시 되지 않아야 함을 나타냅니다. | 예 | 예 | 아니요 |
| RequestCountCacheOthers | 위에 포함되지 않는 캐시 상태를 갖는 모든 요청의 수 | 아니요 | 예 | 아니요  |
| EgressTotal | 아웃바운드 데이터 전송(GB) | 예 |예 |예 |
| EgressHttpStatus2xx | 2xx HTTP 상태 코드를 나타내는 응답에 대한 아웃바운드 데이터 전송(GB)입니다.* | 예 | 예 | 아니요  |
| EgressHttpStatus3xx | 3xx HTTP 상태 코드를 나타내는 응답에 대한 아웃바운드 데이터 전송(GB)입니다. | 예 | 예 | 아니요  |
| EgressHttpStatus4xx | 4xx HTTP 상태 코드를 나타내는 응답에 대한 아웃바운드 데이터 전송(GB)입니다. | 예 | 예 | 아니요  |
| EgressHttpStatus5xx | 5xx HTTP 상태 코드를 나타내는 응답에 대한 아웃바운드 데이터 전송(GB)입니다. | 예 | 예 | 아니요 |
| EgressHttpStatusOthers | 다른 HTTP 상태 코드를 나타내는 응답에 대한 아웃바운드 데이터 전송(GB)입니다. | 예 | 예 | 아니요  |
| EgressCacheHit | CDN POP/Edge의 CDN 캐시에서 직접 전달된 응답에 대한 아웃바운드 데이터 전송입니다. | 예 | 예 | 아니요 |
| EgressCacheMiss. | 가장 가까운 POP 서버에서 찾을 수 없고 원본 서버에서 검색 된 응답에 대 한 아웃 바운드 데이터 전송입니다. | 예 | 예 | 아니요 |
| EgressCacheNoCache | Edge의 사용자 구성 때문에 캐시 되지 않도록 방지 된 자산에 대 한 아웃 바운드 데이터 전송. | 예 | 예 | 아니요 |
| EgressCacheUncacheable | 자산의 Cache 컨트롤 및 또는 Expires 헤더에 의해 캐시 되지 못하도록 방지 된 자산에 대 한 아웃 바운드 데이터 전송. POP 또는 HTTP 클라이언트에 의해 캐시 되지 않아야 함을 나타냅니다. | 예 | 예 | 아니요 |
| EgressCacheOthers | 다른 캐시 시나리오에 대한 아웃바운드 데이터 전송 | 아니요 | 예 | 아니요 |

* 아웃바운드 데이터 전송은 CDN POP 서버에서 클라이언트로 전달되는 트래픽을 나타냅니다.


### <a name="schema-of-the-core-analytics-logs"></a>핵심 분석 로그의 스키마 

모든 로그는 JSON 형식으로 저장되며 각 항목에는 다음 스키마에 따른 문자열 필드가 있습니다.

```json
    "records": [
        {
            "time": "2017-04-27T01:00:00",
            "resourceId": "<ARM Resource Id of the CDN Endpoint>",
            "operationName": "Microsoft.Cdn/profiles/endpoints/contentDelivery",
            "category": "CoreAnalytics",
            "properties": {
                "DomainName": "<Name of the domain for which the statistics is reported>",
                "RequestCountTotal": integer value,
                "RequestCountHttpStatus2xx": integer value,
                "RequestCountHttpStatus3xx": integer value,
                "RequestCountHttpStatus4xx": integer value,
                "RequestCountHttpStatus5xx": integer value,
                "RequestCountHttpStatusOthers": integer value,
                "RequestCountHttpStatus200": integer value,
                "RequestCountHttpStatus206": integer value,
                "RequestCountHttpStatus302": integer value,
                "RequestCountHttpStatus304": integer value,
                "RequestCountHttpStatus404": integer value,
                "RequestCountCacheHit": integer value,
                "RequestCountCacheMiss": integer value,
                "RequestCountCacheNoCache": integer value,
                "RequestCountCacheUncacheable": integer value,
                "RequestCountCacheOthers": integer value,
                "EgressTotal": double value,
                "EgressHttpStatus2xx": double value,
                "EgressHttpStatus3xx": double value,
                "EgressHttpStatus4xx": double value,
                "EgressHttpStatus5xx": double value,
                "EgressHttpStatusOthers": double value,
                "EgressCacheHit": double value,
                "EgressCacheMiss": double value,
                "EgressCacheNoCache": double value,
                "EgressCacheUncacheable": double value,
                "EgressCacheOthers": double value,
            }
        }

    ]
}
```

여기서 *time*은 통계가 보고되는 시간 범위의 시작 시간을 나타냅니다. CDN 공급자가 지원 하지 않는 메트릭에는 double 또는 정수 값 대신 null 값이 생성 됩니다. 이 null 값은 메트릭이 없음을 나타내며 값 0과는 다릅니다. 도메인당 이러한 메트릭 집합 하나는 끝점에서 구성 됩니다.

예제 속성은 다음과 같습니다.

```json
{
     "DomainName": "manlingakamaitest2.azureedge.net",
     "RequestCountTotal": 480,
     "RequestCountHttpStatus2xx": 480,
     "RequestCountHttpStatus3xx": 0,
     "RequestCountHttpStatus4xx": 0,
     "RequestCountHttpStatus5xx": 0,
     "RequestCountHttpStatusOthers": 0,
     "RequestCountHttpStatus200": 480,
     "RequestCountHttpStatus206": 0,
     "RequestCountHttpStatus302": 0,
     "RequestCountHttpStatus304": 0,
     "RequestCountHttpStatus404": 0,
     "RequestCountCacheHit": null,
     "RequestCountCacheMiss": null,
     "RequestCountCacheNoCache": null,
     "RequestCountCacheUncacheable": null,
     "RequestCountCacheOthers": null,
     "EgressTotal": 0.09,
     "EgressHttpStatus2xx": null,
     "EgressHttpStatus3xx": null,
     "EgressHttpStatus4xx": null,
     "EgressHttpStatus5xx": null,
     "EgressHttpStatusOthers": null,
     "EgressCacheHit": null,
     "EgressCacheMiss": null,
     "EgressCacheNoCache": null,
     "EgressCacheUncacheable": null,
     "EgressCacheOthers": null
}

```

## <a name="additional-resources"></a>추가 리소스

* [Azure 진단 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Azure CDN 보조 포털을 통한 핵심 분석](https://docs.microsoft.com/azure/cdn/cdn-analyze-usage-patterns)
* [Azure Monitor 로그](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)
* [Azure Log Analytics REST API](https://docs.microsoft.com/rest/api/loganalytics)







