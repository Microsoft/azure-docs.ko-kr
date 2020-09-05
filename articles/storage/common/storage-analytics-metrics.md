---
title: Azure 스토리지 분석 메트릭(클래식)
description: Azure Storage에서 스토리지 분석 메트릭을 사용하는 방법에 대해 알아봅니다. 트랜잭션 및 용량 메트릭에 대해 알아보고, 메트릭을 저장 하 고, 메트릭을 사용 하도록 설정 하는 방법에 대해 알아봅니다.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: 2f3fa755f61d398ce7f0965fba86262c3e3ec863
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89021156"
---
# <a name="azure-storage-analytics-metrics-classic"></a>Azure 스토리지 분석 메트릭(클래식)

Azure Storage는 Azure 스토리지 분석 솔루션을 사용하여 스토리지 서비스에 대한 요청과 관련하여 집계된 트랜잭션 통계 및 용량 데이터를 포함하는 메트릭을 저장합니다. 트랜잭션은 API 작업 수준과 스토리지 서비스 수준에서 보고됩니다. 용량은 스토리지 서비스 수준에서 보고됩니다. 메트릭 데이터를 사용하여 다음 작업을 수행할 수 있습니다.
- 스토리지 서비스 사용량을 분석합니다.
- 스토리지 서비스에 대해 수행된 요청에 대한 문제를 진단합니다.
- 서비스를 사용하는 애플리케이션의 성능을 향상시킵니다.

 스토리지 분석 메트릭은 새 Storage 계정에서 기본적으로 활성화됩니다. [Azure Portal](https://portal.azure.com/)에서 메트릭을 구성할 수 있습니다. 자세한 내용은 [Azure Portal에서 스토리지 계정 모니터링](/azure/storage/storage-monitor-storage-account)을 참조하세요. REST API 또는 클라이언트 라이브러리를 통해 프로그래밍 방식으로 스토리지 분석을 사용하도록 설정할 수도 있습니다. 서비스 속성 설정 작업을 사용하여 각 서비스에 대한 스토리지 분석을 사용하도록 설정할 수 있습니다.  

> [!NOTE]
> 스토리지 분석 메트릭은 Azure Blob Storage, Azure Queue Storage, Azure Table Storage 및 Azure Files에 대해 사용하도록 설정됩니다.
> 스토리지 분석 메트릭은 현재 클래식 메트릭입니다. 스토리지 분석 메트릭 대신 [Azure Monitor에서 스토리지 메트릭](monitor-storage.md)을 사용하는 것이 좋습니다.

## <a name="transaction-metrics"></a>트랜잭션 메트릭  
 각 스토리지 서비스 및 요청한 API 작업에 대해 시간 또는 분 간격으로 유용한 데이터 세트가 기록됩니다. 해당 데이터에는 수신 및 송신, 가용성, 오류, 분류된 요청 비율 등이 포함됩니다. 트랜잭션 세부 정보의 전체 목록은 [스토리지 분석 메트릭 테이블 스키마](/rest/api/storageservices/storage-analytics-metrics-table-schema)를 참조하세요.  

 트랜잭션 데이터는 서비스 수준과 API 작업 수준에서 기록됩니다. 서비스 수준에서는 서비스에 대한 요청이 없더라도 요청한 모든 API 작업을 요약하는 통계가 1시간마다 테이블 엔터티에 기록됩니다. API 작업 수준에서는 해당 시간 동안 작업이 요청된 경우에만 통계가 엔터티에 기록됩니다.  

 예를 들어 Blob service에 대해 **GetBlob** 작업을 수행하면 스토리지 분석 메트릭이 요청을 기록한 다음 이를 Blob service 및 **GetBlob** 작업에 대해 집계된 데이터에 포함시킵니다. 해당 시간 중에 **GetBlob** 작업이 요청되지 않았으면 엔터티가 해당 작업에 대한 *$MetricsTransactionsBlob*에 기록되지 않습니다.  

 스토리지 분석 자체에서 수행한 요청과 사용자 요청에 대해 트랜잭션 메트릭이 기록됩니다. 예를 들어 로그 및 테이블 엔터티 작성을 위한 스토리지 분석의 요청이 기록됩니다.

## <a name="capacity-metrics"></a>용량 메트릭  

> [!NOTE]
>  현재는 Blob service에 대해서만 용량 메트릭이 제공됩니다.

 용량 데이터는 스토리지 계정의 Blob service에 대해 매일 기록되며 2개의 테이블 엔터티가 작성됩니다. 이러한 엔터티 중 하나는 사용자 데이터에 대한 통계를 제공하며 다른 하나는 스토리지 분석에서 사용하는 `$logs` Blob 컨테이너에 대한 통계를 제공합니다. *$MetricsCapacityBlob* 테이블에는 다음과 같은 통계가 포함됩니다.  

- **Capacity**: 스토리지 계정의 Blob service가 사용하는 스토리지의 양(바이트)입니다.  
- **ContainerCount**: 스토리지 계정 Blob service의 Blob 컨테이너 수입니다.  
- **ObjectCount**: 스토리지 계정 Blob service에서 커밋된/커밋되지 않은 블록이나 페이지 Blob의 수입니다.  

  용량 메트릭에 대한 자세한 내용은 [스토리지 분석 메트릭 테이블 스키마](/rest/api/storageservices/storage-analytics-metrics-table-schema)를 참조하세요.  

## <a name="how-metrics-are-stored"></a>메트릭 저장 방법  

 각 스토리지 서비스에 대한 모든 메트릭 데이터는 해당 서비스에 대해 예약된 3개의 테이블인 트랜잭션 정보용 테이블, 세부 트랜잭션 정보용 테이블 및 용량 정보용 테이블에 저장됩니다. 트랜잭션 및 세부 트랜잭션 정보는 요청과 응답 데이터로 구성됩니다. 용량 정보는 스토리지 사용량 현황 데이터로 구성됩니다. 다음 표에서 설명하는 대로 이름이 지정된 테이블에서 스토리지 계정 Blob service의 시간 메트릭, 분 메트릭 및 용량 정보에 액세스합니다.  

|메트릭 수준|테이블 이름|지원되는 버전|  
|-------------------|-----------------|----------------------------|  
|시간 메트릭, 기본 위치|-   $MetricsTransactionsBlob<br />-   $MetricsTransactionsTable<br />-   $MetricsTransactionsQueue|2013년 8월 15일 이전 버전에만 해당됩니다. 해당 이름은 계속 지원되기는 하지만 다음 표를 사용하도록 전환하는 것이 좋습니다.|  
|시간 메트릭, 기본 위치|-   $MetricsHourPrimaryTransactionsBlob<br />-   $MetricsHourPrimaryTransactionsTable<br />-   $MetricsHourPrimaryTransactionsQueue<br />-   $MetricsHourPrimaryTransactionsFile|모든 버전. 파일 서비스 메트릭에 대한 지원은 2015년 4월 5일 이후 버전에서만 사용할 수 있습니다.|  
|분 메트릭, 기본 위치|-   $MetricsMinutePrimaryTransactionsBlob<br />-   $MetricsMinutePrimaryTransactionsTable<br />-   $MetricsMinutePrimaryTransactionsQueue<br />-   $MetricsMinutePrimaryTransactionsFile|모든 버전. 파일 서비스 메트릭에 대한 지원은 2015년 4월 5일 이후 버전에서만 사용할 수 있습니다.|  
|시간 메트릭, 보조 위치|-   $MetricsHourSecondaryTransactionsBlob<br />-   $MetricsHourSecondaryTransactionsTable<br />-   $MetricsHourSecondaryTransactionsQueue|모든 버전. 읽기 권한의 지역 중복 복제를 사용하도록 설정해야 합니다.|  
|분 메트릭, 보조 위치|-   $MetricsMinuteSecondaryTransactionsBlob<br />-   $MetricsMinuteSecondaryTransactionsTable<br />-   $MetricsMinuteSecondaryTransactionsQueue|모든 버전. 읽기 권한의 지역 중복 복제를 사용하도록 설정해야 합니다.|  
|용량(Blob service만 해당)|$MetricsCapacityBlob|모든 버전.|  

 해당 테이블은 스토리지 서비스 엔드포인트에 대해 스토리지 분석을 사용하도록 설정하면 자동으로 작성됩니다. 스토리지 계정의 네임스페이스를 통해 해당 테이블에 액세스할 수 있습니다. 예: `https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")` 메트릭 테이블은 목록 작업에 표시되지 않으며 테이블 이름을 통해 직접 액세스해야 합니다.

## <a name="enable-metrics-by-using-the-azure-portal"></a>Azure Portal을 사용하여 메트릭 사용 설정
[Azure Portal](https://portal.azure.com)에서 메트릭을 사용하도록 설정하려면 다음 단계를 따릅니다.

1. 스토리지 계정으로 이동합니다.
1. 메뉴 창에서 **진단 설정(클래식)** 을 선택합니다.
1. **상태**가 **켜기**로 설정되어 있는지 확인합니다.
1. 모니터링하려는 서비스에 대한 메트릭을 선택합니다.
1. 보존 정책을 지정하여 메트릭 및 로그 데이터를 보존하는 기간을 나타냅니다.
1. **저장**을 선택합니다.

현재 [Azure Portal](https://portal.azure.com)은 스토리지 계정에서 분 메트릭을 구성할 수 없습니다. PowerShell을 사용하거나 프로그래밍 방식으로 분 메트릭을 사용하도록 설정해야 합니다.

## <a name="enable-storage-metrics-by-using-powershell"></a>PowerShell을 사용하여 스토리지 메트릭 사용 설정  
로컬 컴퓨터에서 PowerShell을 사용하여 스토리지 계정의 스토리지 메트릭을 구성할 수 있습니다. Azure PowerShell cmdlet **Get-AzStorageServiceMetricsProperty**를 사용하여 현재 설정을 검색합니다. cmdlet **Set-AzStorageServiceMetricsProperty**를 사용하여 현재 설정을 변경합니다.  

스토리지 메트릭을 제어하는 cmdlet은 다음 매개 변수를 사용합니다.  

* **ServiceType**: 가능한 값은 **Blob**, **Queue**, **Table** 및 **File**입니다.
* **MetricsType**: 가능한 값은 **Hour** 및 **Minute**입니다.  
* **MetricsLevel**: 가능한 값은 다음과 같습니다.
   * **없음**: 모니터링을 해제합니다.
   * **서비스**: 수신 및 송신, 가용성, 대기 시간, 성공 비율 등 Blob, 큐, 테이블 및 파일 서비스에 대해 집계되는 메트릭을 수집합니다.
   * **ServiceAndApi**: 서비스 메트릭뿐 아니라 Azure Storage 서비스 API의 각 스토리지 작업에 대한 동일한 메트릭 집합을 수집합니다.

예를 들어 다음 명령은 보존 기간을 5일로 설정하여 스토리지 계정의 Blob service에 대해 분 메트릭을 설정합니다. 

> [!NOTE]
> 이 명령은 `Connect-AzAccount` 명령을 사용하여 Azure 구독에 로그인한 것으로 가정합니다.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"

Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $storageAccount.Context
```  

* `<resource-group-name>` 자리 표시자 값을 리소스 그룹의 이름으로 바꿉니다.      
* `<storage-account-name>` 자리 표시자 값을 스토리지 계정 이름으로 바꿉니다.



다음 명령은 기본 스토리지 계정의 Blob 서비스에 대해 현재 시간 메트릭 수준 및 보존 기간(일)을 검색합니다.  

```powershell
Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
```  

Azure 구독에서 작동하도록 Azure PowerShell cmdlet을 구성하고 사용할 기본 스토리지 계정을 선택하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성](https://azure.microsoft.com/documentation/articles/install-configure-powershell/)을 참조하세요.  

## <a name="enable-storage-metrics-programmatically"></a>프로그래밍 방식으로 스토리지 메트릭 사용 설정  
Azure Portal 또는 Azure PowerShell cmdlet을 사용하여 스토리지 메트릭을 제어하는 방법 외에 Azure Storage API 중 하나를 사용할 수도 있습니다. 예를 들어 .NET 언어를 사용하는 경우 Azure Storage 클라이언트 라이브러리를 사용할 수 있습니다.  

클래스 **CloudBlobClient**, **CloudQueueClient**, **CloudTableClient** 및 **CloudFileClient**는 모두 **SetServiceProperties** 및 **SetServicePropertiesAsync**처럼 **ServiceProperties** 개체를 매개 변수로 하는 메서드를 갖고 있습니다. **ServiceProperties** 개체를 사용하여 스토리지 메트릭을 구성할 수 있습니다. 예를 들어 다음 C# 코드 조각은 시간 큐 메트릭의 메트릭 수준과 보존 기간을 변경하는 방법을 보여 줍니다.  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

.NET 언어를 사용하여 스토리지 메트릭을 구성하는 방법에 대한 자세한 내용은 [.NET용 Azure Storage 클라이언트 라이브러리](https://msdn.microsoft.com/library/azure/mt347887.aspx)를 참조하세요.  

REST API를 사용하여 스토리지 메트릭을 구성하는 방법에 대한 일반적인 내용은 [스토리지 분석 설정 및 구성](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics)을 참조하세요.  

##  <a name="view-storage-metrics"></a>스토리지 메트릭 보기  
Storage 계정을 모니터링하도록 스토리지 분석 메트릭을 구성하면 스토리지 분석에서 Storage 계정의 알려진 테이블 집합에 메트릭을 기록합니다. [Azure Portal](https://portal.azure.com)에서 시간별 메트릭을 보도록 차트를 구성할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 스토리지 계정으로 이동합니다.
1. 메트릭을 보려는 서비스에 대한 메뉴 창에서 **메트릭(클래식)** 을 선택합니다.
1. 구성하려는 차트를 선택합니다.
1. **차트 편집** 창에서 **시간 범위**, **차트 종류** 및 차트에 표시할 메트릭을 선택합니다.

Azure Portal에서 스토리지 계정의 메뉴 창의 **모니터링(클래식)** 섹션에서 [경고 규칙](#metrics-alerts)을 구성할 수 있습니다. 예를 들어 특정 메트릭이 특정 값에 도달할 때 알려주는 메일 알림을 보내도록 구성할 수 있습니다.

장기 스토리지용 메트릭을 다운로드하거나 메트릭을 로컬에서 분석하려는 경우에는 테이블을 읽는 코드를 작성하거나 도구를 사용해야 합니다. 분석용으로는 분 메트릭을 다운로드해야 합니다. 스토리지 계정의 모든 테이블을 나열해도 테이블은 표시되지 않지만 테이블 이름을 통해 직접 액세스할 수는 있습니다. 스토리지 찾아보기 도구는 대체로 해당 테이블을 인식하며 테이블을 직접 보는 기능을 제공합니다. 사용 가능한 도구의 목록은 [Azure Storage 클라이언트 도구](/azure/storage/storage-explorers)를 참조하세요.

|메트릭|테이블 이름|메모| 
|-|-|-|  
|시간 메트릭|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|2013년 8월 15일 이전 버전에서는 해당 테이블을 다음과 같이 지칭했습니다.<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> 파일 서비스에 대한 메트릭은 2015년 4월 5일 버전부터 사용할 수 있습니다.|  
|분 메트릭|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|PowerShell을 사용하거나 프로그래밍 방식으로만 사용하도록 설정할 수 있습니다.<br /><br /> 파일 서비스에 대한 메트릭은 2015년 4월 5일 버전부터 사용할 수 있습니다.|  
|용량|$MetricsCapacityBlob|Blob service만 해당합니다.|  

해당 테이블의 스키마에 대한 전체 세부 정보는 [스토리지 분석 메트릭 테이블 스키마](/rest/api/storageservices/storage-analytics-metrics-table-schema)를 참조하세요. 다음 샘플 행에는 사용 가능한 열 중 일부만 나와 있습니다. 그러나 스토리지 메트릭에서 해당 메트릭을 저장하는 방식과 관련한 몇 가지 중요한 기능을 확인할 수 있습니다.  

|PartitionKey|RowKey|타임스탬프|TotalRequests|TotalBillableRequests|TotalIngress|TotalEgress|가용성|AverageE2ELatency|AverageServerLatency|PercentSuccess| 
|-|-|-|-|-|-|-|-|-|-|-|  
|20140522T1100|user;All|2014-05-22T11:01:16.7650250Z|7|7|4003|46801|100|104.4286|6.857143|100|  
|20140522T1100|user;QueryEntities|2014-05-22T11:01:16.7640250Z|5|5|2694|45951|100|143.8|7.8|100|  
|20140522T1100|user;QueryEntity|2014-05-22T11:01:16.7650250Z|1|1|538|633|100|3|3|100|  
|20140522T1100|user;UpdateEntity|2014-05-22T11:01:16.7650250Z|1|1|771|217|100|9|6|100|  

분 메트릭 데이터의 이 예에서 파티션 키는 분 확인 시의 시간을 사용합니다. 행 키는 행에 저장되는 정보의 유형을 식별합니다. 정보는 액세스 형식 및 요청 형식으로 구성됩니다.  

-   액세스 형식은 **user** 또는 **system**입니다. 여기서 **user**는 스토리지 서비스에 대한 모든 사용자 요청을 지칭하며 **system**은 스토리지 분석에서 수행한 요청을 지칭합니다.  
-   요청 형식은 **all**(이 경우 요약 줄)이거나 **QueryEntity** 또는 **UpdateEntity** 등의 특정 API를 식별합니다.  

이 샘플 데이터는 오전 11시부터 1분 동안의 모든 레코드를 표시하므로 **QueryEntities** 요청의 수 + **QueryEntity** 요청의 수 + **UpdateEntity** 요청의 수 = 7입니다. 이 합계는 **user:All** 행에 표시됩니다. 마찬가지로 ((143.8 * 5) + 3 + 9)/7을 계산하여 **user:All** 행에 평균 엔드투엔드 대기 시간인 104.4286을 표시할 수 있습니다.  

## <a name="metrics-alerts"></a>메트릭 알림
스토리지 서비스 동작의 중요한 변경 내용을 자동으로 알릴 수 있도록 [Azure Portal](https://portal.azure.com)에서 경고를 설정하는 것이 좋습니다. Storage Explorer 도구를 사용하여 이 메트릭 데이터를 구분된 형식에서 다운로드하려면 Microsoft Excel을 사용하여 데이터를 분석할 수 있습니다. 사용 가능한 Storage Explorer 도구의 목록은 [Azure Storage 클라이언트 도구](/azure/storage/storage-explorers)를 참조하세요. 스토리지 계정 메뉴 창에서 **모니터링(클래식)** 아래에서 액세스할 수 있는 **경고(클래식)** 창에서 알림을 구성할 수 있습니다.

> [!IMPORTANT]
> 스토리지 이벤트와 해당 시간 또는 분 메트릭 데이터가 기록되는 시간 사이에 지연이 있을 수 있습니다. 분 메트릭의 경우 데이터의 몇 분을 한 번에 작성할 수 있습니다. 이 문제로 인해 현재 분의 트랜잭션에 집계되는 이전 분의 트랜잭션이 발생할 수 있습니다. 이 문제가 발생하는 경우 알림 서비스에는 구성된 알림 간격에 대한 사용 가능한 모든 메트릭 데이터가 없을 수 있어서 예기치 않게 알림이 발생할 수 있습니다.
>

## <a name="access-metrics-data-programmatically"></a>프로그래밍 방식으로 메트릭 데이터 액세스  
다음 목록에서는 특정 시간(분) 범위에 대한 분 메트릭에 액세스하여 결과를 콘솔 창에 표시하는 샘플 C# 코드를 보여 줍니다. 코드 샘플은 스토리지의 메트릭 테이블 액세스를 간소화하는 **CloudAnalyticsClient** 클래스가 포함된 Azure Storage 클라이언트 라이브러리 버전 4.x 이상을 사용합니다.  

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)  
{  
 // Convert the dates to the format used in the PartitionKey.  
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

     // Filter on "user" transactions after fetching the metrics from Azure Table storage.  
     // (StartsWith is not supported using LINQ with Azure Table storage.)  
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

## <a name="billing-on-storage-metrics"></a>스토리지 메트릭에 대한 청구
메트릭용 테이블 엔터티 작성을 위한 쓰기 요청에는 모든 Azure Storage 작업에 적용되는 표준 요금이 부과됩니다.  

클라이언트의 메트릭 데이터 읽기 및 삭제 요청 역시 표준 요금이 청구됩니다. 데이터 보존 정책을 구성한 경우에는 Azure Storage에서 이전 메트릭 데이터를 삭제할 때 요금이 부과되지 않습니다. 분석 데이터를 삭제하는 경우 계정에 대해 삭제 작업 요금이 부과됩니다.  

메트릭 테이블에 사용되는 용량도 청구됩니다. 다음 정보를 통해 메트릭 데이터 저장에 사용되는 용량을 예상할 수 있습니다.  

-   서비스가 1시간마다 모든 서비스의 모든 API를 사용하는 경우, 서비스 수준 및 API 수준 요약을 사용하도록 설정했다면 메트릭 트랜잭션 테이블에 시간당 약 148KB의 데이터가 저장됩니다.  
-   서비스가 매 1시간 이내에 서비스의 모든 API를 사용하는 경우, 서비스 수준 요약만 사용하도록 설정했다면 메트릭 트랜잭션 테이블에 시간당 약 12KB의 데이터가 저장됩니다.  
-   로그를 옵트인한 경우 Blob의 용량 테이블에는 매일 두 개의 행이 추가됩니다. 이 시나리오는 매일 이 테이블의 크기가 최대 약 300바이트 증가함을 의미합니다.

## <a name="next-steps"></a>다음 단계
* [스토리지 계정 모니터링](https://www.windowsazure.com/manage/services/storage/how-to-monitor-a-storage-account/)   
* [스토리지 분석 메트릭 테이블 스키마](/rest/api/storageservices/storage-analytics-metrics-table-schema)   
* [스토리지 분석에서 기록한 작업 및 상태 메시지](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)   
* [스토리지 분석 로깅](storage-analytics-logging.md)
