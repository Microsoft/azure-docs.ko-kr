---
title: Azure Storage analytics 메트릭 (클래식)
description: Azure Storage 메트릭을 사용 하는 방법에 알아봅니다.
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 03/11/2019
ms.author: fryu
ms.subservice: common
ms.openlocfilehash: c15242b0c480e2da39897b850ab7b2a2fd05bf11
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483522"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Azure Storage analytics 메트릭 (클래식)

스토리지 분석에서는 Storage 서비스에 대한 요청 관련 용량 데이터 및 집계된 트랜잭션 통계를 포함하는 메트릭을 저장할 수 있습니다. 트랜잭션은 API 작업 수준과 저장소 서비스 수준에서 모두 보고되며 용량은 저장소 서비스 수준에서 보고됩니다. 메트릭 데이터를 사용하면 저장소 서비스 사용량을 분석하고 저장소 서비스에 대한 요청의 문제를 진단하며 서비스를 사용하는 애플리케이션의 성능을 개선할 수 있습니다.  

 스토리지 분석 메트릭은 새 Storage 계정에서 기본적으로 활성화됩니다. 메트릭을 구성할 수 있습니다는 [Azure portal](https://portal.azure.com/); 자세한 내용은 참조 하십시오 [Azure portal에서 저장소 계정 모니터링](/azure/storage/storage-monitor-storage-account)합니다. REST API 또는 클라이언트 라이브러리를 통해 프로그래밍 방식으로 저장소 분석을 사용하도록 설정할 수도 있습니다. 각 서비스에 대해 Storage Analytics를 사용 하도록 설정 하려면 서비스 속성 설정 작업을 사용 합니다.  

> [!NOTE]
> 저장소 분석 메트릭은 Blob, 큐, 테이블 및 파일 서비스에 대해 사용할 수 있습니다.
> 저장소 분석 메트릭은 이제 클래식 메트릭입니다. 사용 하는 것이 좋습니다 [Azure Monitor의 Storage 메트릭](storage-metrics-in-azure-monitor.md) 저장소 분석 메트릭 대신 합니다.

## <a name="transaction-metrics"></a>트랜잭션 메트릭  
 각 저장소 서비스 및 요청한 API 작업에 대해 시간 또는 분 간격으로 유용한 데이터 집합이 기록됩니다. 이러한 데이터에는 수신/송신, 가용성, 오류, 분류된 요청 비율 등이 포함됩니다. 트랜잭션 세부 정보의 전체 목록은 [저장소 분석 메트릭 테이블 스키마](/rest/api/storageservices/storage-analytics-metrics-table-schema) 항목에서 확인할 수 있습니다.  

 트랜잭션 데이터는 두 개의 수준, 즉 서비스 수준과 API 작업 수준에서 기록됩니다. 서비스 수준에서는 서비스에 대한 요청이 없더라도 요청한 모든 API 작업을 요약하는 통계가 1시간마다 테이블 엔터티에 기록됩니다. API 작업 수준에서는 해당 시간 동안 작업이 요청된 경우에만 통계가 엔터티에 기록됩니다.  

 예를 들어 Blob service에 대해 **GetBlob** 작업을 수행하면 저장소 분석 메트릭이 요청을 기록한 다음 **GetBlob** 작업뿐만 아니라 Blob service에 대해 집계된 데이터에도 포함합니다. 그러나 없으면 **GetBlob** 시간 동안 작업이 요청 될 엔터티는 쓸 수 없습니다는 *$MetricsTransactionsBlob* 해당 작업에 대 한 합니다.  

 저장소 분석 자체에서 수행한 요청과 사용자 요청 둘 모두에 대해 트랜잭션 메트릭이 기록됩니다. 예를 들어 로그 및 테이블 엔터티 작성을 위한 저장소 분석의 요청이 기록됩니다.

## <a name="capacity-metrics"></a>용량 메트릭  

> [!NOTE]
>  현재는 Blob service에 대해서만 용량 메트릭이 제공됩니다.

 용량 데이터는 저장소 계정의 Blob service에 대해 매일 기록되며 2개 테이블 엔터티가 작성됩니다. 이러한 엔터티 중 하나는 사용자 데이터에 대한 통계를 제공하며 다른 하나는 저장소 분석에서 사용하는 `$logs` Blob 컨테이너에 대한 통계를 제공합니다. 합니다 *$MetricsCapacityBlob* 테이블에는 다음 통계가 포함 됩니다.  

- **Capacity**: 바이트의 저장소 계정의 Blob service를 사용 하는 저장소 양입니다.  
- **ContainerCount**: 저장소 계정의 Blob service에서 blob 컨테이너의 수입니다.  
- **ObjectCount**: 저장소 계정의 Blob service에서 커밋된 블록과 커밋되지 않은 블록 또는 페이지 blob의 수입니다.  

  용량 메트릭에 대한 자세한 내용은 [저장소 분석 메트릭 테이블 스키마](/rest/api/storageservices/storage-analytics-metrics-table-schema)를 참조하세요.  

## <a name="how-metrics-are-stored"></a>메트릭 저장 방법  

 각 저장소 서비스의 모든 메트릭 데이터는 해당 서비스용으로 예약된 테이블 3개에 저장됩니다. 이러한 테이블은 각각 트랜잭션 정보용, 세부 트랜잭션 정보용, 용량 정보용 테이블입니다. 트랜잭션 및 세부 트랜잭션 정보는 요청과 응답 데이터로 구성되며 용량 정보는 저장소 사용량 데이터로 구성됩니다. 시간 메트릭, 분 메트릭 및 용량 정보는 저장소 계정의 Blob service에는 다음 표에 설명 된 대로 이름이 지정 된 테이블에 액세스할 수 있습니다.  

|메트릭 수준|테이블 이름|버전에 대 한 지원|  
|-------------------|-----------------|----------------------------|  
|시간 메트릭, 기본 위치|-   $MetricsTransactionsBlob<br />-   $MetricsTransactionsTable<br />-   $MetricsTransactionsQueue|2013-08-15 이전 버전만 해당됩니다. 이러한 이름은 계속 지원되기는 하지만 아래에 나와 있는 테이블을 사용하도록 전환하는 것이 좋습니다.|  
|시간 메트릭, 기본 위치|-   $MetricsHourPrimaryTransactionsBlob<br />-   $MetricsHourPrimaryTransactionsTable<br />-   $MetricsHourPrimaryTransactionsQueue<br />-   $MetricsHourPrimaryTransactionsFile|모든 버전입니다. 파일 서비스 메트릭에 대 한 지원은 이상 버전 2015-04-05 에서만에서 사용할 수 있습니다.|  
|분 메트릭, 기본 위치|-   $MetricsMinutePrimaryTransactionsBlob<br />-   $MetricsMinutePrimaryTransactionsTable<br />-   $MetricsMinutePrimaryTransactionsQueue<br />-   $MetricsMinutePrimaryTransactionsFile|모든 버전입니다. 파일 서비스 메트릭에 대 한 지원은 이상 버전 2015-04-05 에서만에서 사용할 수 있습니다.|  
|시간 메트릭, 보조 위치|-   $MetricsHourSecondaryTransactionsBlob<br />-   $MetricsHourSecondaryTransactionsTable<br />-   $MetricsHourSecondaryTransactionsQueue|모든 버전입니다. 읽기 권한의 지역 중복 복제를 사용하도록 설정해야 합니다.|  
|분 메트릭, 보조 위치|-   $MetricsMinuteSecondaryTransactionsBlob<br />-   $MetricsMinuteSecondaryTransactionsTable<br />-   $MetricsMinuteSecondaryTransactionsQueue|모든 버전입니다. 읽기 권한의 지역 중복 복제를 사용하도록 설정해야 합니다.|  
|용량(Blob service만 해당)|$MetricsCapacityBlob|모든 버전입니다.|  

 이러한 테이블 저장소 서비스 끝점에 대 한 저장소 분석 사용 하는 경우에 자동으로 만들어집니다. 예를 들어 저장소 계정의 네임 스페이스를 통해 액세스할: `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`합니다. 메트릭 테이블 나열 작업에서 표시 되지 않으며 테이블 이름을 통해 직접 액세스할 수 있어야 합니다.  

## <a name="enable-metrics-using-the-azure-portal"></a>Azure portal을 사용 하 여 메트릭을 사용 하도록 설정
[Azure Portal](https://portal.azure.com)에서 메트릭을 사용하도록 설정하려면 다음 단계를 따릅니다.

1. 저장소 계정으로 이동합니다.
1. 선택 **진단 설정 (클래식)** 에서 합니다 **메뉴** 창입니다.
1. **상태**가 **켜기**로 설정되어 있는지 확인합니다.
1. 모니터링하려는 서비스에 대한 메트릭을 선택합니다.
1. 보존 정책을 지정하여 메트릭 및 로그 데이터를 보존하는 기간을 나타냅니다.
1. **저장**을 선택합니다.

[Azure Portal](https://portal.azure.com)에서는 현재 저장소 계정에서 분 메트릭을 구성할 수 없습니다. 따라서 PowerShell을 사용하거나 프로그래밍 방식으로 분 메트릭을 사용하도록 설정해야 합니다.

> [!NOTE]
>  참고가 Azure portal 않습니다 하지 현재 사용할 수 있도록 저장소 계정에서 분 메트릭을 구성할 수 있습니다. PowerShell을 사용 하 여 분 메트릭을 사용 하도록 설정 해야 하거나 프로그래밍 방식으로 합니다.

## <a name="enable-storage-metrics-using-powershell"></a>PowerShell을 사용 하 여 저장소 메트릭을 사용 하도록 설정  
로컬 컴퓨터에 PowerShell를 사용 하 여 Azure PowerShell cmdlet을 사용 하 여 저장소 계정의 저장소 메트릭을 구성할 수 있습니다 **Get-azurestorageservicemetricsproperty** 현재 설정과 cmdlet을검색하려면 **Set-azurestorageservicemetricsproperty** 현재 설정을 변경 합니다.  

Storage 메트릭을 제어하는 cmdlet은 다음 매개 변수를 사용합니다.  

* **ServiceType**에 가능한 값은 **Blob**를 **큐**를 **테이블**, 및 **파일**합니다.
* **MetricsType**에 가능한 값은 **시간** 하 고 **분**합니다.  
* **MetricsLevel**, 가능한 값은:
* **없음**: 모니터링을 해제 합니다.
* **서비스**: 수신/송신, 가용성, 대기 시간 및 성공 비율 등 blob, 큐, 테이블 및 파일 서비스에 대해 집계 되는 메트릭을 수집 합니다.
* **ServiceAndApi**: 서비스 메트릭 외에도 동일한 Azure Storage 서비스 API에서 각 저장소 작업에 대 한 메트릭 집합을 수집합니다.

예를 들어 다음 명령은 보존 기간을 5일로 설정하여 기본 저장소 계정의 Blob 서비스에 대해 분 메트릭을 설정합니다.  

```  
Set-AzureStorageServiceMetricsProperty -MetricsType Minute   
-ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5  
```  

다음 명령은 기본 저장소 계정의 Blob 서비스에 대해 현재 시간 메트릭 수준 및 보존 기간(일)을 검색합니다.  

```  
Get-AzureStorageServiceMetricsProperty -MetricsType Hour   
-ServiceType Blob  
```  

Azure 구독에서 작동하도록 Azure PowerShell cmdlet을 구성하고 사용할 기본 스토리지 계정을 선택하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](https://azure.microsoft.com/documentation/articles/install-configure-powershell/)을 참조하세요.  

## <a name="enable-storage-metrics-programmatically"></a>프로그래밍 방식으로 Storage 메트릭을 사용 하도록 설정  
Azure portal 또는 Storage 메트릭을 제어 하는 Azure PowerShell cmdlet을 사용 하는 것 외에도 Azure Storage Api 중 하나을 사용할 수 있습니다. 예를 들어,.NET 언어를 사용 하는 경우 저장소 클라이언트 라이브러리를 사용할 수 있습니다.  

클래스 **CloudBlobClient**를 **CloudQueueClient**합니다 **CloudTableClient**, 및 **CloudFileClient** 같은방법이모두 **SetServiceProperties** 하 고 **SetServicePropertiesAsync** 사용 하는 한 **ServiceProperties** 개체를 매개 변수로 합니다. 사용할 수는 **ServiceProperties** 저장소 메트릭을 구성 하는 개체입니다. 예를 들어, 다음 C# 조각은 시간 큐 메트릭의 메트릭 수준과 보존 기간을 변경 하는 방법을 보여 줍니다.  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

.NET 언어를 사용 하 여 저장소 메트릭을 구성할 하는 방법에 대 한 자세한 내용은 참조 하세요. [.NET 용 저장소 클라이언트 라이브러리](https://msdn.microsoft.com/library/azure/mt347887.aspx)합니다.  

REST API를 사용 하 여 저장소 메트릭을 구성 하는 방법에 대 한 일반적인 정보를 참조 하세요 [저장소 분석 구성 및 사용 하도록 설정 하면](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics)합니다.  

##  <a name="viewing-storage-metrics"></a>Storage 메트릭 보기  
Storage 계정을 모니터링하도록 스토리지 분석 메트릭을 구성하면 스토리지 분석에서 Storage 계정의 알려진 테이블 집합에 메트릭을 기록합니다. [Azure Portal](https://portal.azure.com)에서 시간별 메트릭을 보도록 차트를 구성할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)의 저장소 계정으로 이동합니다.
1. 선택 **메트릭 (클래식)** 에 **메뉴** 블레이드에서 메트릭을 보려는 서비스에 대 한 합니다.
1. 구성 하려는 차트를 클릭 합니다.
1. **차트 편집** 블레이드에서 **시간 범위**, **차트 종류** 및 차트에 표시할 메트릭을 선택합니다.

에 **모니터링 (클래식)** 섹션의 Azure portal에서 저장소 계정의 메뉴 블레이드에서 구성할 수 있습니다 [경고 규칙](#metrics-alerts), 특정 메트릭이 되 면 알리도록 경고를에 도달 하면 전자 메일을 보내는 등을 특정 값입니다.

장기 저장용 메트릭을 다운로드 하거나 로컬로 분석 하 여 원하는 도구를 사용 하거나 테이블을 읽는 코드를 작성 해야 합니다. 분석용으로는 분 메트릭을 다운로드해야 합니다. 저장소 계정의 모든 테이블을 나열해도 테이블은 표시되지 않지만 테이블 이름을 통해 직접 액세스할 수는 있습니다. 많은 저장소 검색 도구는 이러한 테이블의 인식 및을 직접 볼 수 있습니다 (참조 [Azure Storage Client Tools](/azure/storage/storage-explorers) 사용 가능한 도구 목록은).

||||  
|-|-|-|  
|**Metrics**(메트릭)|**테이블 이름**|**참고 사항**|  
|시간 메트릭|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|2013-08-15 이전 버전에서는 이러한 테이블 라고 했습니다.<br /><br /> $MetricsTransactionsBlob <br /><br /> $MetricsTransactionsTable<br /><br />  $MetricsTransactionsQueue<br /><br /> 파일 서비스에 대 한 메트릭은 버전 2015-04-05부터 사용할 수 있습니다.|  
|분 메트릭|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|PowerShell을 사용 하 여 설정할 수 있습니다. 또는 프로그래밍 방식으로 합니다.<br /><br /> 파일 서비스에 대 한 메트릭은 버전 2015-04-05부터 사용할 수 있습니다.|  
|용량|$MetricsCapacityBlob|Blob service만 해당 합니다.|  

[저장소 분석 메트릭 테이블 스키마](/rest/api/storageservices/storage-analytics-metrics-table-schema)에서 이러한 테이블의 스키마에 대한 전체 세부 정보를 확인할 수 있습니다. 아래의 샘플 행에는 사용 가능한 열 중 일부만 나와 있습니다. 그러나 Storage 메트릭에서 이러한 메트릭을 저장하는 방식과 관련한 몇 가지 중요한 기능을 확인할 수 있습니다.  

||||||||||||  
|-|-|-|-|-|-|-|-|-|-|-|  
|**PartitionKey**|**RowKey**|**Timestamp**|**TotalRequests**|**TotalBillableRequests**|**TotalIngress**|**TotalEgress**|**가용성**|**AverageE2ELatency**|**AverageServerLatency**|**PercentSuccess**|  
|20140522T1100|user;All|2014-05-22T11:01:16.7650250Z|7|7|4003|46801|100|104.4286|6.857143|100|  
|20140522T1100|user;QueryEntities|2014-05-22T11:01:16.7640250Z|5|5|2694|45951|100|143.8|7.8|100|  
|20140522T1100|user;QueryEntity|2014-05-22T11:01:16.7650250Z|1|1|538|633|100|3|3|100|  
|20140522T1100|user;UpdateEntity|2014-05-22T11:01:16.7650250Z|1|1|771|217|100|9|6|100|  

이 예제 분 메트릭 데이터에서 파티션 키는 분 확인 시의 시간을 사용합니다. 행 키는 행에 저장되는 정보의 유형을 식별합니다. 이 정보는 액세스 형식과 요청 형식의 두 가지 정보로 구성됩니다.  

-   액세스 유형은 **사용자** 또는 **시스템**여기서 **사용자** 저장소 서비스에 대 한 모든 사용자 요청을 참조 하 고 **시스템** 참조 저장소 분석에서 수행한 요청 합니다.  

-   요청 유형은 **모든** 와 같은 특정 API를 식별 하거나 경우 요약 줄 것 **QueryEntity** 하거나 **UpdateEntity**합니다.  

1 분 동안의 (오전 11 시부터 시작), 따라서 모든 레코드를 표시 하는 위의 샘플 데이터 수가 **QueryEntities** 요청의 수가 **QueryEntity** 수와 요청  **UpdateEntity** 7 총 인 요청 추가에 표시 합니다 **user: All** 행입니다. 마찬가지로,에 평균 종단 간 대기 시간 104.4286을 파생할 수 있습니다 합니다 **user: All** 를 계산 하 여 행 ((143.8 * 5) + 3 + 9) / 7입니다.  

## <a name="metrics-alerts"></a>메트릭 알림
경고를 설정 해야 합니다 [Azure portal](https://portal.azure.com) 되므로 storage 서비스 동작의 주요 변경 내용 자동 알림이 나타납니다. 저장소 탐색기 도구를 사용하여 이 메트릭 데이터를 구분된 형식에서 다운로드하려면 Microsoft Excel을 사용하여 데이터를 분석할 수 있습니다. 사용 가능한 스토리지 탐색기 도구의 목록은 [Azure Storage Client Tools](/azure/storage/storage-explorers)를 참조하세요. 경고를 구성할 수 있습니다는 **경고 (클래식)** 블레이드에서 아래에서 액세스할 수 있습니다 **모니터링 (클래식)** 저장소 계정 메뉴 블레이드에서 합니다.

> [!IMPORTANT]
> 저장소 이벤트와 해당 시간 또는 분 메트릭 데이터가 기록되는 시간 사이에 지연이 있을 수 있습니다. 분 메트릭의 경우 데이터의 몇 분을 한 번에 작성할 수 있습니다. 이로 인해 현재 분의 트랜잭션에 집계되는 이전 분의 트랜잭션이 발생할 수 있습니다. 이 경우 알림 서비스에는 구성된 알림 간격에 대한 사용 가능한 모든 메트릭 데이터가 없을 수 있어서 예기치 않게 알림이 발생할 수 있습니다.
>

## <a name="accessing-metrics-data-programmatically"></a>프로그래밍 방식으로 메트릭 데이터 액세스  
다음 목록에서는 특정 시간(분) 범위에 대한 분 메트릭에 액세스하여 결과를 콘솔 창에 표시하는 샘플 C# 코드를 보여 줍니다. 코드 샘플에서는 Azure Storage 클라이언트 라이브러리 버전 4.x 이상을 포함 하는 또는 **CloudAnalyticsClient** 메트릭 테이블 저장소에 액세스를 간소화 하는 클래스입니다.  

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)  
{  
 // Convert the dates to the format used in the PartitionKey  
 var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  
 var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  

 var services = Enum.GetValues(typeof(StorageService));  
 foreach (StorageService service in services)  
 {  
     Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);  
     var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);  
     var t = analyticsClient.GetMinuteMetricsTable(service);  
     var opContext = new OperationContext();  
     var query =  
             from entity in metricsQuery  
             // Note, you can't filter using the entity properties Time, AccessType, or TransactionType  
             // because they are calculated fields in the MetricsEntity class.  
             // The PartitionKey identifies the DataTime of the metrics.  
             where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0   
             select entity;  

     // Filter on "user" transactions after fetching the metrics from Table Storage.  
     // (StartsWith is not supported using LINQ with Azure Table storage)  
     var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));  
     var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();  
     Console.WriteLine(resultString);  
 }  
}  

private static string MetricsString(MetricsEntity entity, OperationContext opContext)  
{  
 var entityProperties = entity.WriteEntity(opContext);  
 var entityString =  
         string.Format("Time: {0}, ", entity.Time) +  
         string.Format("AccessType: {0}, ", entity.AccessType) +  
         string.Format("TransactionType: {0}, ", entity.TransactionType) +  
         string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));  
 return entityString;  
}  
```  

## <a name="billing-on-storage-metrics"></a>저장소 메트릭에 대 한 청구  
메트릭용 테이블 엔터티 작성을 위한 쓰기 요청에는 모든 Azure Storage 작업에 적용되는 표준 요금이 부과됩니다.  

클라이언트에서 메트릭 데이터 읽기 및 삭제 요청 표준 요금이 청구 됩니다. 데이터 보존 정책을 구성한 경우에는 Azure Storage에서 이전 메트릭 데이터를 삭제할 때 요금이 부과되지 않습니다. 그러나 분석 데이터를 삭제하는 경우 계정에 대해 삭제 작업 요금이 부과됩니다.  

용량 메트릭 테이블에서 사용 하는 청구 가능한 이기도 합니다. 메트릭 데이터를 저장 하는 데 사용 되는 용량 크기를 추정 하려면 다음을 사용할 수 있습니다.  

-   그런 다음 1 시간 마다 서비스는 모든 서비스의 모든 API 사용을 모두 서비스 및 API 수준 요약을 사용 하도록 설정한 경우 약 148KB의 데이터가 메트릭 트랜잭션 테이블에 시간당을 저장 됩니다.  
-   각 시간 내에 있으면 서비스에서 모든 API를 사용 하는 서비스를 서비스 수준 요약을 사용 하도록 설정한 경우 약 12KB의 데이터가 매시간 메트릭 트랜잭션 테이블에 저장 됩니다.  
-   Blob 용 용량 테이블에 제공 하면 옵트인 로그에 대 한 각 날짜를 추가 하는 두 개의 행에 있습니다. 이 매일이이 테이블의 크기가 증가 하 여 약 300 바이트씩 것을 의미 합니다.

## <a name="next-steps"></a>다음 단계
* [Storage 계정을 모니터링 하는 방법](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [저장소 분석 메트릭 테이블 스키마](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [저장소 분석에서 기록한 작업 및 상태 메시지](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [스토리지 분석 로깅](storage-analytics-logging.md)
