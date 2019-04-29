---
title: Azure 저장소 분석 로깅
description: Azure Storage에 대 한 요청에 대 한 세부 정보를 기록 하는 방법에 알아봅니다.
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 03/11/2019
ms.author: fryu
ms.subservice: common
ms.openlocfilehash: 09a5a6d823240b724e6ec88de38df068a58982d9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483505"
---
# <a name="azure-storage-analytics-logging"></a>Azure 저장소 분석 로깅

스토리지 분석은 Storage 서비스에 대해 성공한 요청과 실패한 요청 관련 상세 정보를 기록합니다. 이 정로를 사용하면 개별 요청을 모니터링하고 저장소 서비스의 문제를 진단할 수 있습니다. 요청은 최상의 노력을 기준으로 기록됩니다.

 저장소 분석 로깅 저장소 계정에 대해 기본적으로 사용 되지 않습니다. 이 작업은 [Azure Portal](https://portal.azure.com/)에서 수행할 수 있습니다. 자세한 내용은 [Azure Portal에서 저장소 계정 모니터링](/azure/storage/storage-monitor-storage-account)을 참조하세요. REST API 또는 클라이언트 라이브러리를 통해 프로그래밍 방식으로 저장소 분석을 사용하도록 설정할 수도 있습니다. 사용 하 여는 [Blob 서비스 속성 가져오기](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API), [큐 서비스 속성 가져오기](https://docs.microsoft.com/rest/api/storageservices/Get-Queue-Service-Properties), 및 [Table Service 속성 가져오기](https://docs.microsoft.com/rest/api/storageservices/Get-Table-Service-Properties) 각 서비스에 대해 Storage Analytics를 사용 하도록 설정 하는 작업입니다.

 서비스 엔드포인트에 대한 요청이 있는 경우에만 로그 항목이 만들어집니다. 예를 들어 저장소 계정에 활동이 Blob 끝점에 있지만 해당 테이블 또는 큐 끝점에는 없는 Blob service에 관련 된 로그만 만들어집니다.

> [!NOTE]
>  Storage 분석 로깅은 현재 Blob, Queue 및 Table services에 대해서만 사용할 수 있습니다. 그러나 프리미엄 저장소 계정은 지원 되지 않습니다.

## <a name="requests-logged-in-logging"></a>요청 기록 로깅
### <a name="logging-authenticated-requests"></a>인증된 요청 로깅

 다음과 같은 유형의 인증된 요청이 기록됩니다.

- 성공한 요청
- 실패한 요청(시간 제한, 제한, 네트워크, 권한 부여 및 기타 오류)
- 공유 액세스 서명 (SAS) 또는 실패 한 실행과 성공한 요청을 포함 하 여 OAuth를 사용 하 여 요청
- 분석 데이터에 대한 요청

  로그 만들기/삭제 등 저장소 분석 자체에서 수행한 요청은 기록되지 않습니다. 기록되는 데이터의 전체 목록은 [저장소 분석에서 기록한 작업 및 상태 메시지](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) 및 [저장소 분석 로그 형식](/rest/api/storageservices/storage-analytics-log-format) 항목에 나와 있습니다.

### <a name="logging-anonymous-requests"></a>익명 요청 로깅

 다음과 같은 유형의 익명 요청이 기록됩니다.

- 성공한 요청
- 서버 오류
- 클라이언트와 서버 모두에 대 한 시간 초과 오류
- 오류 코드가 304(수정되지 않음)인 실패한 GET 요청

  기타 모든 실패한 익명 요청은 기록되지 않습니다. 기록되는 데이터의 전체 목록은 [저장소 분석에서 기록한 작업 및 상태 메시지](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) 및 [저장소 분석 로그 형식](/rest/api/storageservices/storage-analytics-log-format) 항목에 나와 있습니다.

## <a name="how-logs-are-stored"></a>로그 저장 방법

모든 로그 라는 컨테이너에 블록 blob에 저장 됩니다 `$logs`, 저장소 계정에 대해 저장소 분석이 활성화 되었는지 하는 경우 자동으로 생성 됩니다. 합니다 `$logs` 컨테이너에에서 위치한 저장소 계정의 blob 네임 스페이스 예를 들어: `http://<accountname>.blob.core.windows.net/$logs`합니다. Storage 계정을 사용하도록 설정한 후에는 이 컨테이너를 삭제할 수 없지만 해당 콘텐츠는 삭제할 수 있습니다. 저장소 검색 도구를 사용 하 여 컨테이너로 직접 이동 하 여 로깅 데이터를 포함 하는 모든 blob 표시 됩니다.

> [!NOTE]
>  `$logs` 컨테이너 나열 작업 등 컨테이너 나열 작업을 수행할 때 컨테이너 표시 되지 않습니다. 직접 액세스해야 합니다. 예를 들어, blob에 액세스 하려면 Blob 나열 작업을 사용할 수 있습니다는 `$logs` 컨테이너입니다.

요청을 기록할 때 저장소 분석은 중간 결과를 블록으로 업로드합니다. 그리고 이러한 블록을 정기적으로 커밋하여 Blob로 제공합니다. 로그 데이터가 있는 blob에 표시 한 시간이 걸릴 수 있습니다 합니다 **$logs** 컨테이너 때문에 저장소 서비스가 로그 기록기를 플러시하는 빈도입니다. 같은 시간에 생성되는 로그의 경우 중복 레코드가 있을 수 있습니다. **RequestId** 및 **Operation** 번호를 검사하여 레코드 중복 여부를 확인할 수 있습니다.

각 시간에 대 한 여러 파일을 사용 하 여 로그 데이터의 높은 볼륨에 있는 경우 blob 메타 데이터 필드를 검사 하 여 로그에 포함 된 데이터를 확인 하려면 blob 메타 데이터를 사용할 수 있습니다. 또한이 유용 수 있기 때문에 경우에 따라 지연 데이터는 로그 파일에 쓰는 동안: blob 메타 데이터를 더 정확 하 게 나타내려면 blob 이름 보다 blob 콘텐츠를 제공 합니다.

대부분의 저장소 검색 도구를 사용 합니다; blob의 메타 데이터를 볼 수 있습니다. PowerShell을 사용 하 여이 정보를 참조할 수 있습니다 또는 프로그래밍 방식으로 합니다. 다음 PowerShell 코드 조각을 포함 된 로그만 식별 하는 메타 데이터 및 시간을 지정 하는 이름으로 로그 blob 목록을 필터링 한 예로 **쓰기** 작업 합니다.  

 ```powershell
 Get-AzureStorageBlob -Container '$logs' |  
 Where-Object {  
     $_.Name -match 'table/2014/05/21/05' -and   
     $_.ICloudBlob.Metadata.LogType -match 'write'  
 } |  
 ForEach-Object {  
     "{0}  {1}  {2}  {3}" –f $_.Name,   
     $_.ICloudBlob.Metadata.StartTime,   
     $_.ICloudBlob.Metadata.EndTime,   
     $_.ICloudBlob.Metadata.LogType  
 }  
 ```  

프로그래밍 방식으로 blob을 나열 하는 방법에 대 한 정보를 참조 하세요 [Blob 리소스 열거](http://msdn.microsoft.com/library/azure/hh452233.aspx) 하 고 [검색 속성 및 Blob 리소스에 대 한 메타 데이터 설정 및](http://msdn.microsoft.com/library/azure/dd179404.aspx)합니다.  

### <a name="log-naming-conventions"></a>로그 명명 규칙

 각 로그 형식은 쓰입니다.

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 다음 표에서 로그 이름의 각 특성을 보여 줍니다.

|특성|설명|
|---------------|-----------------|
|`<service-name>`|저장소 서비스의 이름입니다. 예를 들어: `blob`, `table`, 또는 `queue`|
|`YYYY`|로그에 대 한 4 자리 연도입니다. 예: `2011`|
|`MM`|로그에 대 한 두 자리 월입니다. 예: `07`|
|`DD`|로그에 대 한 두 일입니다. 예: `31`|
|`hh`|로그에 대 한 시작 시간을 나타내는 두 자리 시간에서 24 시간 UTC 형식입니다. 예: `18`|
|`mm`|로그의 시작 분을 나타내는 2 자리 숫자입니다. **참고:**  이 값은 Storage Analytics의 현재 버전에서 지원 되지 않습니다 및 해당 값은 항상 `00`합니다.|
|`<counter>`|1시간 동안 저장소 서비스에 대해 생성되는 로그 Blob의 수를 나타내는 0부터 시작되는 6자리 카운터입니다. 이 카운터부터 `000000`합니다. 예: `000001`|

 다음은 위 예제를 결합 하는 완전 한 샘플 로그 이름입니다.

 `blob/2011/07/31/1800/000001.log`

 다음은 위 로그에 액세스 할 수 있는 예제 URI입니다.

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 저장소 요청이 기록되면 생성되는 로그 이름과 요청한 작업이 완료된 시간 간의 상관 관계가 지정됩니다. 예를 들어 GetBlob 요청이 2011 년 7 월 31 오후 6시 30분에서 완료 된 경우 다음 접두사로 로그가 기록 됩니다. `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>로그 메타데이터

 모든 로그 Blob는 해당 Blob에 포함된 로깅 데이터를 식별하는 데 사용할 수 있는 메타데이터와 함께 저장됩니다. 다음 표에서 각 메타 데이터 특성을 보여 줍니다.

|특성|설명|
|---------------|-----------------|
|`LogType`|로그에 읽기, 쓰기 또는 삭제 작업과 관련된 정보가 포함되는지 여부를 설명합니다. 이 값에는 한 가지 형식이 포함될 수도 있고 3개 형식이 모두 조합(쉼표로 구분)되어 포함될 수도 있습니다.<br /><br /> 예 1: `write`<br /><br /> 예 2: `read,write`<br /><br /> 예제 3: `read,write,delete`|
|`StartTime`|로그 형식에에서 있는 항목의 가장 이른 시간 `YYYY-MM-DDThh:mm:ssZ`합니다. 예: `2011-07-31T18:21:46Z`|
|`EndTime`|가장 늦은 시간 형식으로 로그에 항목을 `YYYY-MM-DDThh:mm:ssZ`입니다. 예: `2011-07-31T18:22:09Z`|
|`LogVersion`|로그 형식의 버전입니다.|

 다음은 위 예제를 사용 하 여 전체 샘플 메타 데이터를 표시 합니다.

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`

## <a name="enable-storage-logging"></a>저장소 로깅을 사용 하도록 설정

Azure portal, PowerShell 및 저장소 Sdk를 사용 하 여 저장소 로깅을 활성화할 수 있습니다.

### <a name="enable-storage-logging-using-the-azure-portal"></a>Azure portal을 사용 하 여 저장소 로깅을 사용 하도록 설정  

Azure portal에서 사용 하 여는 **진단 설정 (클래식)** 블레이드에서 저장소 로깅에서 액세스할 수 있는 컨트롤에는 **모니터링 (클래식)** 저장소 계정의 부분 **메뉴 블레이드에서** .

를 기록 하려는 저장소 서비스 및 기록된 된 데이터에 대 한 일 단위로 보존 기간을 지정할 수 있습니다.  

### <a name="enable-storage-logging-using-powershell"></a>PowerShell을 사용 하 여 저장소 로깅을 사용 하도록 설정  

 로컬 컴퓨터에 PowerShell를 사용 하 여 Azure PowerShell cmdlet을 사용 하 여 저장소 계정의 저장소 로깅을 구성할 수 있습니다 **Get-azurestorageserviceloggingproperty** 현재 설정과 cmdlet을검색하려면 **Set-azurestorageserviceloggingproperty** 현재 설정을 변경 합니다.  

 저장소 로깅을 제어 하는 cmdlet 사용을 **LoggingOperations** 매개 변수는 기록할 요청 유형의 쉼표로 구분 된 목록이 포함 된 문자열입니다. 세 가지 가능한 요청 유형은 **읽을**를 **작성**, 및 **삭제**합니다. 로깅을 해제를 전환 하려면 값을 사용 하 여 **none** 에 대 한 합니다 **LoggingOperations** 매개 변수입니다.  

 다음 명령은 읽기에 대 한 로깅을 전환, 쓰기 및 기본 저장소 계정의 큐 서비스에 요청을 5 일로 설정 하는 보존을 사용 하 여 삭제할 수 있습니다.  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5  
```  

 다음 명령은 기본 저장소 계정의 table service에 대해 로깅을 해제 전환합니다.  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none  
```  

 Azure 구독에서 작동하도록 Azure PowerShell cmdlet을 구성하고 사용할 기본 스토리지 계정을 선택하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](https://azure.microsoft.com/documentation/articles/install-configure-powershell/)을 참조하세요.  

### <a name="enable-storage-logging-programmatically"></a>프로그래밍 방식으로 로깅 저장소를 사용 하도록 설정  

 Azure 포털 또는 저장소 로깅을 제어 하는 Azure PowerShell cmdlet을 사용 하는 것 외에도 Azure Storage Api 중 하나을 사용할 수 있습니다. 예를 들어,.NET 언어를 사용 하는 경우 저장소 클라이언트 라이브러리를 사용할 수 있습니다.  

 클래스 **CloudBlobClient**를 **CloudQueueClient**, 및 **CloudTableClient** 와 같은 모든는 메서드가 **SetServiceProperties** 및 **SetServicePropertiesAsync** 사용 하는 한 **ServiceProperties** 개체를 매개 변수로 합니다. 사용할 수는 **ServiceProperties** 저장소 로깅을 구성 하는 개체입니다. 예를 들어, 다음 C# 조각은 로깅되는 및 큐 로깅에 대 한 보존 기간을 변경 하는 방법을 보여 줍니다.  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
```  

 .NET 언어를 사용 하 여 저장소 로깅을 구성 하는 방법에 대 한 자세한 내용은 참조 하세요. [Storage Client Library Reference](https://msdn.microsoft.com/library/azure/dn261237.aspx)합니다.  

 REST API를 사용 하 여 저장소 로깅을 구성 하는 방법에 대 한 일반적인 정보를 참조 하세요 [저장소 분석 구성 및 사용 하도록 설정 하면](https://msdn.microsoft.com/library/azure/hh360996.aspx)합니다.  

## <a name="download-storage-logging-log-data"></a>저장소 로깅 로그 데이터 다운로드

 보고 분석 하 여 로그 데이터를 로컬 컴퓨터에 원하는 로그 데이터가 포함 된 blob을 다운로드 해야 합니다. 많은 저장소 검색 도구를 사용 하면 저장소 계정에서 blob을 다운로드 명령줄 Azure 복사 도구를 제공 하는 Azure Storage 팀을 사용할 수도 있습니다 (**AzCopy**) 로그 데이터를 다운로드 합니다.  

 관심이 있다면 로그 데이터를 다운로드 해야 하 고 동일한 로그 데이터를 두 번 이상 다운로드를 방지:  

-   날짜를 사용 하 고 다시 동일한 데이터를 두 번 이상 다운로드를 방지 하는 분석을 위해 이미 다운로드 한 blob을 추적 하려면 로그 데이터가 포함 된 blob에 대 한 시간 명명 규칙.  

-   로그 데이터가 포함 된 blob에 메타 데이터를 사용 하 여 로그 데이터를 다운로드 해야 하는 정확한 blob을 식별 하는 blob을 보관 하는 특정 기간을 확인 하 합니다.  

> [!NOTE]
>  AzCopy는 Azure SDK의 일부 이지만 항상에서 최신 버전을 다운로드할 수 있습니다 [ https://aka.ms/AzCopy ](https://aka.ms/AzCopy)합니다. 기본적으로 AzCopy는 폴더에 설치 됩니다 **C:\Program Files (x86) \Microsoft SDKs\Windows Azure\AzCopy**, 명령 프롬프트 또는 PowerShell 창에서 도구를 실행 하기 전에이 폴더 경로에 추가 해야 합니다.  

 다음 예제에서는 09 AM, AM, 10 및 20 2014 년 5 월 오전 11 시부터 시간에 대 한 큐 서비스에 대 한 로그 데이터를 다운로드할 수 하는 방법을 보여 줍니다. 합니다 **/S** 날짜와 시간; 로그 파일 이름에 따라 로컬 폴더 구조를 작성 하는 데 AzCopy 사용 하면 매개 변수를 **/V** 매개 변수 하면 자세한 정보 출력을 생성 하는 데 AzCopy는 **/Y** 매개 변수 하면 AzCopy를 로컬 파일을 덮어씁니다. 바꿉니다 **< yourstorageaccount\>**  으로 바꾸고 저장소 계정 이름의 **< yourstoragekey\>**  저장소 계정 키를 사용 하 여 합니다.  

```
AzCopy 'http://<yourstorageaccount>.blob.core.windows.net/$logs/queue'  'C:\Logs\Storage' '2014/05/20/09' '2014/05/20/10' '2014/05/20/11' /sourceKey:<yourstoragekey> /S /V /Y  
```  

 AzCopy 역시 유용한 일부 매개 변수를 다운로드 한 파일의 마지막 수정된 시간을 설정 하는 방법과 해당 컨트롤 및 로컬 컴퓨터에 이미 존재 하는 모든 파일 보다 오래 되거나 최신인 파일 다운로드를 시도할지 여부를 합니다. 다시 시작 가능 모드에서 실행할 수 있습니다. 자세한 내용은 실행 하 여 도움말을 확인할는 **AzCopy /?** 명령을 통해 Visual Studio의 대화형 창에 직접 통합됩니다.  

 로그 데이터를 프로그래밍 방식으로 다운로드 하는 방법의 예로, 블로그 게시물을 참조 하세요. [Windows Azure Storage 로깅: 저장소 요청 추적 로그를 사용 하 여](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx) 및 페이지에서 단어 "DumpLogs"를 검색 합니다.  

 로그 데이터를 다운로드 한 경우에 파일에 로그 항목을 볼 수 있습니다. 이러한 로그 파일 사용 하 여 구분 기호로 분리 된 텍스트 형식으로 많은 로그 읽기 도구가 구문 분석할 수 Microsoft Message Analyzer를 비롯 한 (자세한 내용은 가이드를 참조 하세요. [모니터링, 진단 및 문제 해결 Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md)). 다른 도구는 형식 지정, 필터링, 정렬 및 로그 파일의 콘텐츠를 검색 하기 위한 다양 한 기능을 갖습니다. 저장소 로깅 로그 파일 형식 및 콘텐츠에 대 한 자세한 내용은 참조 하세요. [저장소 분석 로그 형식](/rest/api/storageservices/storage-analytics-log-format) 하 고 [저장소 분석에서 기록한 작업 및 상태 메시지](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)합니다.

## <a name="next-steps"></a>다음 단계

* [저장소 분석 로그 형식](/rest/api/storageservices/storage-analytics-log-format)
* [저장소 분석에서 기록한 작업 및 상태 메시지](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [저장소 분석 메트릭 (클래식)](storage-analytics-metrics.md)