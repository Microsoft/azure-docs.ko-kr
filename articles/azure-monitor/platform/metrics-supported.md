---
title: Azure Monitor에서 지원되는 리소스 종류별 메트릭
description: Azure Monitor의 각 리소스 유형별로 사용 가능한 메트릭 목록.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 07/16/2020
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: 59df49d320b23686a3d053335ea2b95e98125b28
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88135558"
---
# <a name="supported-metrics-with-azure-monitor"></a>Azure Monitor에서 지원되는 메트릭

> [!NOTE]
> 이 목록은 Azure Monitor 메트릭 REST API에서 대부분 자동으로 생성 됩니다. GitHub를 통해이 목록에 대 한 모든 수정 내용이 경고 없이 기록 될 수 있습니다. 영구적 업데이트를 수행 하는 방법에 대 한 자세한 내용은이 문서의 작성자에 게 문의 하세요.

Azure Monitor에서는 포털에서의 차트 작성, REST API를 통한 액세스, PowerShell이나 CLI를 통한 쿼리 등, 메트릭과 상호 작용하는 몇 가지 방법을 제공합니다. 

이 문서는 Azure Monitor의 통합 된 메트릭 파이프라인에서 현재 사용할 수 있는 모든 플랫폼 (즉, 자동으로 수집 된) 메트릭의 전체 목록입니다. 이 목록은 3 월 27 일 2020에 마지막으로 업데이트 되었습니다. 이 날짜 이후에 변경 되거나 추가 된 메트릭은 아래에 표시 되지 않을 수 있습니다. 프로그래밍 방식으로 메트릭 목록을 쿼리하고 액세스 하려면 [2018-01-01 api-version](/rest/api/monitor/metricdefinitions)을 사용 하세요. 이 목록에 없는 다른 메트릭은 포털에서 또는 레거시 Api를 사용 하 여 사용할 수 있습니다.

메트릭은 리소스 공급자와 리소스 종류별로 구성 됩니다. 서비스 및 해당 서비스에 속한 리소스 공급자 목록은 [Azure 서비스에 대 한 리소스 공급자](../../azure-resource-manager/management/azure-services-resource-providers.md)를 참조 하세요. 

## <a name="exporting-platform-metrics-to-other-locations"></a>플랫폼 메트릭을 다른 위치로 내보내기

Azure Monitor 파이프라인에서 다른 위치로 플랫폼 메트릭을 내보내는 방법은 두 가지입니다.
1. [메트릭 REST API](/rest/api/monitor/metrics/list)를 사용합니다.
2. [진단 설정을](diagnostic-settings.md) 사용 하 여 플랫폼 메트릭 라우팅 
    - Azure Storage
    - Azure Monitor 로그 (따라서 Log Analytics)
    - Microsoft 이외의 시스템으로 가져오는 방법 인 Event hubs 

메트릭을 라우팅하는 가장 쉬운 방법은 진단 설정을 사용 하는 것입니다. 하지만 몇 가지 제한 사항이 있습니다. 

- **일부 내보낼 수 없음** -모든 메트릭은 REST API를 사용 하 여 내보낼 수 있지만 일부 메트릭은 Azure Monitor 백 엔드에서 복잡 하지 않기 때문에 진단 설정을 사용 하 여 내보낼 수 없습니다. 아래 표에서 *진단 설정을 통해 내보낼* 수 있는 열은 이러한 방식으로 내보낼 수 있는 메트릭을 나열 합니다.  

- **다차원 메트릭** -진단 설정을 통해 여러 차원 메트릭을 다른 위치로 보내는 것은 현재 지원 되지 않습니다. 차원이 있는 메트릭은 차원 값 전체에서 집계된 플랫 단일 차원 메트릭으로 내보내집니다. *예*: Event Hub의 '들어오는 메시지' 메트릭은 큐 수준별로 탐색하고 차트화할 수 있습니다. 하지만 진단 설정을 통해 내보내면 메트릭은 Event Hub의 모든 큐에서 모두 수신되는 메시지로 표시됩니다.

## <a name="guest-os-and-host-os-metrics"></a>게스트 OS 및 호스트 OS 메트릭

> [!WARNING]
> Azure Virtual Machines, Service Fabric 및 Cloud Services에서 실행 되는 게스트 운영 체제 (게스트 OS)에 대 한 메트릭은 여기에 나열 **되지 않습니다** . 게스트 운영 체제의 일부로 또는에서 실행 되는 하나 이상의 에이전트를 통해 게스트 OS 메트릭을 수집 해야 합니다.  게스트 OS 메트릭에는 게스트 CPU 비율 또는 메모리 사용량을 추적 하는 성능 카운터가 포함 되어 있으며, 둘 다 자동 크기 조정 또는 경고에 자주 사용 됩니다. 
>
> **호스트 OS 메트릭은 아래에 나와 있습니다.** 동일 하지는 않습니다. 호스트 OS 메트릭은 게스트 OS 세션을 호스트 하는 Hyper-v 세션과 관련이 있습니다. 

> [!TIP]
> 모범 사례는 [Azure 진단 확장](diagnostics-extension-overview.md) 을 사용 및 구성 하 여 플랫폼 메트릭이 저장 된 것과 동일한 Azure Monitor 메트릭 데이터베이스에 게스트 OS 성능 메트릭을 전송 하는 것입니다. 확장은 [사용자 지정 메트릭](metrics-custom-overview.md) API를 통해 게스트 OS 메트릭을 라우팅합니다. 그런 다음 플랫폼 메트릭과 같은 게스트 OS 메트릭을 차트로 만들고, 경고 하 고, 사용할 수 있습니다. 또는 Log Analytics 에이전트를 사용 하 여 게스트 OS 메트릭을 Azure Monitor Logs/Log Analytics로 보낼 수도 있습니다. 이 경우 메트릭이 아닌 데이터와 함께 이러한 메트릭을 쿼리할 수 있습니다. 

중요 한 추가 정보는 [모니터링 에이전트 개요](agents-overview.md)를 참조 하세요.    

## <a name="table-formatting"></a>테이블 서식 지정

> [!IMPORTANT] 
> 이 최신 업데이트는 새 열을 추가 하 고 메트릭을 사전순으로 다시 정렬 합니다. 추가 정보는 아래 표에 브라우저 창의 너비에 따라 아래쪽에 가로 스크롤 막대가 있을 수 있음을 의미 합니다. 누락 된 정보가 있다고 생각 되는 경우 스크롤 막대를 사용 하 여 테이블 전체를 확인 합니다.


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|CleanerCurrentPrice|예|메모리: 클리너 현재 가격|개수|평균|현재 메모리 가격, $/바이트/시간, 1000으로 일반화됩니다.|ServerResourceType|
|CleanerMemoryNonshrinkable|예|메모리: 클리너 메모리 축소 불가능|바이트|평균|메모리 양, 바이트 단위, 백그라운드 클리너에 의해 제거되는 대상이 아닙니다.|ServerResourceType|
|CleanerMemoryShrinkable|예|메모리: 클리너 메모리 축소 가능|바이트|평균|메모리 양, 바이트 단위, 백그라운드 클리너에 의해 제거되는 대상입니다.|ServerResourceType|
|CommandPoolBusyThreads|예|스레드: 명령 풀 사용 중인 스레드|개수|평균|명령 스레드 풀의 사용 중인 스레드 수입니다.|ServerResourceType|
|CommandPoolIdleThreads|예|스레드: 명령 풀 유휴 상태 스레드|개수|평균|명령 스레드 풀의 유휴 상태 스레드 수입니다.|ServerResourceType|
|CommandPoolJobQueueLength|예|명령 풀의 작업 큐 길이|개수|평균|명령 스레드 풀의 큐에 있는 작업 수입니다.|ServerResourceType|
|CurrentConnections|예|연결: 현재 연결|개수|평균|현재 설정된 클라이언트 연결 수입니다.|ServerResourceType|
|CurrentUserSessions|예|현재 사용자 세션 수|개수|평균|현재 설정된 사용자 세션 수입니다.|ServerResourceType|
|LongParsingBusyThreads|예|스레드: 긴 구문 분석 사용 중인 스레드|개수|평균|긴 구문 분석 스레드 풀에서 사용 중인 스레드 수입니다.|ServerResourceType|
|LongParsingIdleThreads|예|스레드: 긴 구문 분석 유휴 상태 스레드|개수|평균|긴 구문 분석 스레드 풀에서 유휴 상태 스레드 수입니다.|ServerResourceType|
|LongParsingJobQueueLength|예|스레드: 긴 구문 분석 작업 큐 길이|개수|평균|긴 구문 분석 스레드 풀의 큐에 있는 작업 수입니다.|ServerResourceType|
|mashup_engine_memory_metric|예|M 엔진 메모리|바이트|평균|매시업 엔진 프로세스별 메모리 사용량|ServerResourceType|
|mashup_engine_private_bytes_metric|예|M 엔진 프라이빗 바이트|바이트|평균|매시업 엔진 프로세스의 전용 바이트 사용량입니다.|ServerResourceType|
|mashup_engine_qpu_metric|예|M 엔진 QPU|개수|평균|매시업 엔진 프로세스별 QPU 사용량|ServerResourceType|
|mashup_engine_virtual_bytes_metric|예|M 엔진 가상 바이트|바이트|평균|매시업 엔진 프로세스의 가상 바이트 사용량입니다.|ServerResourceType|
|memory_metric|예|메모리|바이트|평균|메모리. 범위는 S1의 경우 0-25GB, S2의 경우 0-50GB, S4의 경우 0-100GB임|ServerResourceType|
|memory_thrashing_metric|예|메모리 쓰래싱|백분율|평균|평균 메모리 쓰래싱입니다.|ServerResourceType|
|MemoryLimitHard|예|메모리: 메모리 제한 하드|바이트|평균|하드 메모리 제한, 구성 파일 원본입니다.|ServerResourceType|
|MemoryLimitHigh|예|메모리: 메모리 제한 상한|바이트|평균|상한 메모리 제한, 구성 파일 원본입니다.|ServerResourceType|
|MemoryLimitLow|예|메모리: 메모리 제한 하한|바이트|평균|하한 메모리 제한, 구성 파일 원본입니다.|ServerResourceType|
|MemoryLimitVertiPaq|예|메모리: 메모리 제한 VertiPaq|바이트|평균|메모리 내 제한, 구성 파일 원본입니다.|ServerResourceType|
|MemoryUsage|예|메모리: 메모리 사용량|바이트|평균|클리너 메모리 가격을 계산하는 데 사용되는 서버 프로세스의 메모리 사용량입니다. 메모리 매핑된 데이터 크기를 더한 카운터 Process\PrivateBytes와 동일하며 xVelocity 엔진 메모리 제한을 초과하여 xVelocity 메모리 내 분석 엔진(VertiPaq)에서 매핑하거나 할당하는 메모리를 무시합니다.|ServerResourceType|
|private_bytes_metric|예|프라이빗 바이트|바이트|평균|전용 바이트.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|예|스레드: 처리 풀 사용 중인 I/O 작업 스레드|개수|평균|처리 스레드 풀에서 I/O 작업을 실행 중인 스레드 수입니다.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|예|스레드: 처리 풀 사용 중인 비-I/O 스레드|개수|평균|처리 스레드 풀에서 비-I/O 작업을 실행 중인 스레드 수입니다.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|예|스레드: 처리 풀 유휴 상태 I/O 작업 스레드|개수|평균|처리 스레드 풀에서 I/O 작업의 유휴 상태 스레드 수입니다.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|예|스레드: 처리 풀 유휴 상태 비-I/O 스레드|개수|평균|비-I/O 작업 전용인 처리 스레드 풀에서 유휴 상태 스레드 수입니다.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|예|스레드: 처리 중인 풀 I/O 작업 큐 길이|개수|평균|처리 스레드 풀의 큐에 있는 I/O 작업 수입니다.|ServerResourceType|
|ProcessingPoolJobQueueLength|예|처리 풀의 작업 큐 길이|개수|평균|처리 스레드 풀의 큐에 있는 비-I/O 작업 수입니다.|ServerResourceType|
|qpu_metric|예|QPU|개수|평균|QPU. 범위는 S1의 경우 0-100, S2의 경우 0-200, S4의 경우 0-400임|ServerResourceType|
|QueryPoolBusyThreads|예|쿼리 풀의 사용 중인 스레드|개수|평균|쿼리 스레드 풀의 사용 중인 스레드 수입니다.|ServerResourceType|
|QueryPoolIdleThreads|예|스레드: 쿼리 풀 유휴 상태 스레드|개수|평균|처리 스레드 풀에서 I/O 작업의 유휴 상태 스레드 수입니다.|ServerResourceType|
|QueryPoolJobQueueLength|예|스레드: 쿼리 풀 작업 큐 길이|개수|평균|쿼리 스레드 풀의 큐에 있는 작업 수입니다.|ServerResourceType|
|할당량|예|메모리: 할당량|바이트|평균|현재 메모리 할당량, 바이트 단위입니다. 메모리 할당량은 메모리 부여 또는 메모리 예약이라고도 합니다.|ServerResourceType|
|QuotaBlocked|예|메모리: 차단된 할당량|개수|평균|다른 메모리 할당량이 해제될 때까지 차단되는 할당량 요청의 현재 수입니다.|ServerResourceType|
|RowsConvertedPerSec|예|처리: 초당 변환된 행|초당 개수|평균|처리하는 동안 변환된 행의 비율입니다.|ServerResourceType|
|RowsReadPerSec|예|처리: 초당 읽은 행|초당 개수|평균|모든 관계형 데이터베이스에서 읽은 행의 비율입니다.|ServerResourceType|
|RowsWrittenPerSec|예|처리: 초당 작성된 행|초당 개수|평균|처리하는 동안 작성된 행의 비율입니다.|ServerResourceType|
|ShortParsingBusyThreads|예|스레드: 짧은 구문 분석 사용 중인 스레드|개수|평균|짧은 구문 분석 스레드 풀에서 사용 중인 스레드 수입니다.|ServerResourceType|
|ShortParsingIdleThreads|예|스레드: 짧은 구문 분석 유휴 상태 스레드|개수|평균|짧은 구문 분석 스레드 풀에서 유휴 상태 스레드 수입니다.|ServerResourceType|
|ShortParsingJobQueueLength|예|스레드: 짧은 구문 분석 작업 큐 길이|개수|평균|짧은 구문 분석 스레드 풀의 큐에 있는 작업 수입니다.|ServerResourceType|
|SuccessfullConnectionsPerSec|예|초당 성공한 연결 수|초당 개수|평균|성공적으로 완료된 연결 비율입니다.|ServerResourceType|
|TotalConnectionFailures|예|총 연결 실패 수|개수|평균|실패한 총 연결 시도 수입니다.|ServerResourceType|
|TotalConnectionRequests|예|총 연결 요청 수|개수|평균|도착한 총 연결 요청 수입니다.|ServerResourceType|
|VertiPaqNonpaged|예|메모리: 페이징되지 않은 VertiPaq|바이트|평균|메모리 내 엔진에서 사용하기 위해 설정된 작동 중에 잠긴 메모리 바이트입니다.|ServerResourceType|
|VertiPaqPaged|예|메모리: 페이징된 VertiPaq|바이트|평균|메모리 내 데이터에 사용 중인 페이징된 메모리 바이트입니다.|ServerResourceType|
|virtual_bytes_metric|예|가상 바이트|바이트|평균|가상 바이트.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|BackendDuration|예|백 엔드 요청 지속 시간|밀리초|평균|백 엔드 요청 기간 (밀리초)|위치, 호스트 이름|
|용량|예|용량|백분율|평균|ApiManagement 서비스에 대한 사용률 메트릭|위치|
|Duration|예|게이트웨이 요청의 전체 기간|밀리초|평균|게이트웨이 요청의 전체 기간(밀리초)|위치, 호스트 이름|
|EventHubDroppedEvents|예|삭제된 EventHub 이벤트|개수|합계|큐 크기 제한에 도달 하 여 건너뛴 이벤트 수|위치|
|EventHubRejectedEvents|예|거부된 EventHub 이벤트|개수|합계|거부 된 EventHub 이벤트 수 (잘못 된 구성 또는 권한 없음)|위치|
|EventHubSuccessfulEvents|예|성공한 EventHub 이벤트|개수|합계|성공한 EventHub 이벤트 수|위치|
|EventHubThrottledEvents|예|제한된 EventHub 이벤트|개수|합계|제한 된 EventHub 이벤트 수|위치|
|EventHubTimedoutEvents|예|시간 초과된 EventHub 이벤트|개수|합계|시간 초과 된 EventHub 이벤트 수|위치|
|EventHubTotalBytesSent|예|EventHub 이벤트 크기|바이트|합계|EventHub 이벤트의 총 크기 (바이트)|위치|
|EventHubTotalEvents|예|총 EventHub 이벤트|개수|합계|EventHub로 전송 된 이벤트 수|위치|
|EventHubTotalFailedEvents|예|실패한 EventHub 이벤트|개수|합계|실패 한 EventHub 이벤트 수|위치|
|FailedRequests|예|실패한 게이트웨이 요청(사용되지 않음)|개수|합계|게이트웨이 요청의 실패 횟수-대신 GatewayResponseCodeCategory 차원을 사용 하 여 다중 차원 요청 메트릭을 사용 합니다.|위치, 호스트 이름|
|NetworkConnectivity|예|리소스의 네트워크 연결 상태 (미리 보기)|개수|평균|API Management 서비스에서 종속 된 리소스 유형의 네트워크 연결 상태|위치, ResourceType|
|OtherRequests|예|기타 게이트웨이 요청(사용되지 않음)|개수|합계|다른 게이트웨이 요청 수-대신 GatewayResponseCodeCategory 차원을 사용 하 여 다중 차원 요청 메트릭을 사용 합니다.|위치, 호스트 이름|
|요청|예|요청|개수|합계|여러 차원이 포함 된 게이트웨이 요청 메트릭|Location, Hostname, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory|
|SuccessfulRequests|예|성공적인 게이트웨이 요청(사용되지 않음)|개수|합계|성공한 게이트웨이 요청 수-대신 GatewayResponseCodeCategory 차원을 사용 하 여 다중 차원 요청 메트릭을 사용 합니다.|위치, 호스트 이름|
|TotalRequests|예|총 게이트웨이 요청(사용되지 않음)|개수|합계|게이트웨이 요청 수-대신 GatewayResponseCodeCategory 차원을 사용 하 여 다중 차원 요청 메트릭을 사용 합니다.|위치, 호스트 이름|
|UnauthorizedRequests|예|권한이 없는 게이트웨이 요청(사용되지 않음)|개수|합계|권한 없는 게이트웨이 요청 수-대신 GatewayResponseCodeCategory 차원을 사용 하 여 다중 차원 요청 메트릭을 사용 합니다.|위치, 호스트 이름|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|HttpIncomingRequestCount|예|HttpIncomingRequestCount|개수|개수|들어오는 http 요청의 총 수입니다.|StatusCode, 인증|
|HttpIncomingRequestDuration|예|HttpIncomingRequestDuration|개수|평균|Http 요청에 대 한 대기 시간입니다.|StatusCode, 인증|
|ThrottledHttpRequestCount|예|ThrottledHttpRequestCount|개수|개수|제한 된 http 요청입니다.|차원 없음|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|jvm. 데이터 크기|예|jvm. 데이터 크기|바이트|평균|전체 GC 후의 이전 세대 메모리 풀 크기|배포, AppName, Pod|
|jvm. 최대 데이터 크기|예|jvm. 최대 데이터 크기|바이트|평균|이전 세대 메모리 풀의 최대 크기|배포, AppName, Pod|
|jvm. 메모리 할당|예|jvm. 메모리 할당|바이트|최대|1 GC 후의 젊은 생성 메모리 풀 크기가 다음 보다 이전으로 증가 하는 경우 증가|배포, AppName, Pod|
|jvm.|예|jvm.|바이트|최대|Gc 이전 세대 메모리 풀의 크기에서 GC 이후로의 양의 증가량 수|배포, AppName, Pod|
|jvm. 일시 중지. 총 개수|예|jvm. 일시 중지. 총 개수|개수|합계|GC 일시 중지 횟수|배포, AppName, Pod|
|jvm. gc. total. time|예|jvm. gc. total. time|밀리초|합계|GC 일시 중지 총 시간|배포, AppName, Pod|
|jvm. 메모리 커밋|예|jvm. 메모리 커밋|바이트|평균|JVM에 할당 된 메모리 (바이트)|배포, AppName, Pod|
|jvm. memory. max|예|jvm. memory. max|바이트|최대|메모리 관리에 사용할 수 있는 최대 메모리 양 (바이트)입니다.|배포, AppName, Pod|
|jvm. memory. 사용 됨|예|jvm. memory. 사용 됨|바이트|평균|사용 되는 앱 메모리 (바이트)|배포, AppName, Pod|
|프로세스. cpu 사용량|예|프로세스. cpu 사용량|백분율|평균|앱 JVM CPU 사용량 비율|배포, AppName, Pod|
|시스템 cpu 사용량|예|시스템 cpu 사용량|백분율|평균|전체 시스템의 최근 cpu 사용량|배포, AppName, Pod|
|tomcat|예|tomcat|개수|합계|Tomcat 전역 오류|배포, AppName, Pod|
|tomcat|예|tomcat|바이트|합계|Tomcat 총 수신 바이트|배포, AppName, Pod|
|tomcat-평균 시간|예|tomcat-평균 시간|밀리초|평균|Tomcat 요청 평균 시간|배포, AppName, Pod|
|tomcat. 최대|예|tomcat. 최대|밀리초|최대|Tomcat 요청 최대 시간|배포, AppName, Pod|
|tomcat. 총 개수|예|tomcat. 총 개수|개수|합계|Tomcat 요청 총 수|배포, AppName, Pod|
|tomcat. 전체 시간|예|tomcat. 전체 시간|밀리초|합계|Tomcat 요청 총 시간|배포, AppName, Pod|
|tomcat|예|tomcat|바이트|합계|Tomcat 총 보낸 바이트|배포, AppName, Pod|
|tomcat.|예|tomcat.|개수|합계|Tomcat 세션 활성 수|배포, AppName, Pod|
|tomcat.|예|tomcat.|개수|합계|Tomcat 세션 최대 활성 수|배포, AppName, Pod|
|tomcat.|예|tomcat.|밀리초|최대|Tomcat 세션 최대 연결 시간|배포, AppName, Pod|
|tomcat 생성|예|tomcat 생성|개수|합계|Tomcat 세션 생성 횟수|배포, AppName, Pod|
|tomcat.|예|tomcat.|개수|합계|Tomcat 세션 만료 수|배포, AppName, Pod|
|tomcat 거부|예|tomcat 거부|개수|합계|Tomcat 세션 거부 횟수|배포, AppName, Pod|
|tomcat.threads.config. 최대값|예|tomcat.threads.config. 최대값|개수|합계|Tomcat Config 최대 스레드 수|배포, AppName, Pod|
|tomcat|예|tomcat|개수|합계|Tomcat 현재 스레드 수|배포, AppName, Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|TotalJob|예|총 작업 수|개수|합계|총 작업 수|Runbook, 상태|
|TotalUpdateDeploymentMachineRuns|예|총 업데이트 배포 머신 실행|개수|합계|소프트웨어 업데이트 배포 실행 시 총 소프트웨어 업데이트 배포 컴퓨터 실행|SoftwareUpdateConfigurationName, Status, TargetComputer, SoftwareUpdateConfigurationRunId|
|TotalUpdateDeploymentRuns|예|총 업데이트 배포 실행|개수|합계|총 소프트웨어 업데이트 배포 실행|SoftwareUpdateConfigurationName, 상태|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|CoreCount|아니요|전용된 코어 수|개수|합계|배치 계정의 총 전용 코어 수|차원 없음|
|CreatingNodeCount|아니요|노드 수 만들기|개수|합계|만든 노드 수|차원 없음|
|IdleNodeCount|아니요|유휴 상태인 노드 수|개수|합계|유휴 노드 수|차원 없음|
|JobDeleteCompleteEvent|예|작업 삭제 완료 이벤트|개수|합계|정상적으로 삭제된 총 작업 수입니다.|jobId|
|JobDeleteStartEvent|예|작업 삭제 시작 이벤트|개수|합계|삭제되도록 요청된 총 작업 수입니다.|jobId|
|JobDisableCompleteEvent|예|작업 비활성화 완료 이벤트|개수|합계|정상적으로 사용하지 않도록 설정된 총 작업 수입니다.|jobId|
|JobDisableStartEvent|예|작업 비활성화 시작 이벤트|개수|합계|비활성화되도록 요청된 총 작업 수입니다.|jobId|
|JobStartEvent|예|작업 시작 이벤트|개수|합계|정상적으로 시작된 총 작업 수입니다.|jobId|
|JobTerminateCompleteEvent|예|작업 종료 완료 이벤트|개수|합계|정상적으로 종료된 총 작업 수입니다.|jobId|
|JobTerminateStartEvent|예|작업 종료 시작 이벤트|개수|합계|종료되도록 요청된 총 작업 수입니다.|jobId|
|LeavingPoolNodeCount|아니요|나가는 풀 노드 수|개수|합계|풀을 나가는 노드 수|차원 없음|
|LowPriorityCoreCount|아니요|LowPriority 코어 수|개수|합계|배치 계정의 우선 순위가 낮은 총 코어 수|차원 없음|
|OfflineNodeCount|아니요|오프라인 노드 수|개수|합계|오프라인 노드 수|차원 없음|
|PoolCreateEvent|예|풀 만들기 이벤트|개수|합계|만든 총 풀 수|poolId|
|PoolDeleteCompleteEvent|예|풀 삭제 완료 이벤트|개수|합계|완료된 총 풀 삭제 수|poolId|
|PoolDeleteStartEvent|예|풀 삭제 시작 이벤트|개수|합계|시작된 총 풀 삭제 수|poolId|
|PoolResizeCompleteEvent|예|풀 크기 조정 완료 이벤트|개수|합계|완료된 총 풀 크기 조정 수|poolId|
|PoolResizeStartEvent|예|풀 크기 조정 시작 이벤트|개수|합계|시작된 총 풀 크기 조정 작업 수|poolId|
|PreemptedNodeCount|아니요|선점된 노드 수|개수|합계|선점된 노드 수|차원 없음|
|RebootingNodeCount|아니요|재부팅 노드 수|개수|합계|다시 부팅하는 노드의 수|차원 없음|
|ReimagingNodeCount|아니요|이미지로 다시 설치하는 노드 수|개수|합계|이미지로 다시 설치하는 노드의 수|차원 없음|
|RunningNodeCount|아니요|실행 노드 수|개수|합계|실행 중인 노드의 수|차원 없음|
|StartingNodeCount|아니요|시작 노드 수|개수|합계|시작 노드 수|차원 없음|
|StartTaskFailedNodeCount|아니요|시작 작업 실패 노드 수|개수|합계|시작 작업이 실패한 노드 수|차원 없음|
|TaskCompleteEvent|예|작업 완료 이벤트|개수|합계|완료된 총 작업 수|poolId, jobId|
|TaskFailEvent|예|작업 실패 이벤트|개수|합계|실패한 상태로 완료된 총 작업 수|poolId, jobId|
|TaskStartEvent|예|작업 시작 이벤트|개수|합계|시작된 총 작업 수|poolId, jobId|
|TotalLowPriorityNodeCount|아니요|우선 순위가 낮은 노드 수|개수|합계|배치 계정의 우선 순위가 낮은 총 노드 수|차원 없음|
|TotalNodeCount|아니요|전용된 노드 수|개수|합계|배치 계정의 총 전용 노드 수|차원 없음|
|UnusableNodeCount|아니요|사용 불가 노드 수|개수|합계|사용할 수 없는 노드 수|차원 없음|
|WaitingForStartTaskNodeCount|아니요|작업 시작 대기 노드 수|개수|합계|시작 작업 완료를 기다리는 노드 수|차원 없음|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/workspaces

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|활성 코어|예|활성 코어|개수|평균|활성 코어 수|시나리오, ClusterName|
|활성 노드|예|활성 노드|개수|평균|실행 중인 노드의 수|시나리오, ClusterName|
|유휴 코어|예|유휴 코어|개수|평균|유휴 코어 수|시나리오, ClusterName|
|유휴 노드|예|유휴 노드|개수|평균|유휴 노드 수|시나리오, ClusterName|
|작업 완료됨|예|작업 완료됨|개수|합계|완료 된 작업 수|시나리오, ClusterName, ResultType|
|작업 제출됨|예|작업 제출됨|개수|합계|제출 된 작업 수|시나리오, ClusterName|
|나가는 코어|예|나가는 코어|개수|평균|종료 된 코어 수|시나리오, ClusterName|
|나가는 노드|예|나가는 노드|개수|평균|종료 노드 수|시나리오, ClusterName|
|선점된 코어|예|선점된 코어|개수|평균|선점 된 코어 수|시나리오, ClusterName|
|선점된 노드|예|선점된 노드|개수|평균|선점된 노드 수|시나리오, ClusterName|
|할당량 사용률|예|할당량 사용률|개수|평균|사용한 할당량 백분율|시나리오, ClusterName, VmFamilyName, VmPriority|
|총 코어 수|예|총 코어 수|개수|평균|총 코어 수|시나리오, ClusterName|
|총 노드 수|예|총 노드 수|개수|평균|총 노드 수|시나리오, ClusterName|
|사용 불가 코어|예|사용 불가 코어|개수|평균|사용할 수 없는 코어 수|시나리오, ClusterName|
|사용 불가 노드|예|사용 불가 노드|개수|평균|사용할 수 없는 노드 수|시나리오, ClusterName|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|BroadcastProcessedCount|예|처리 된 브로드캐스트 수|개수|평균|처리 된 트랜잭션 수|노드, 채널, 유형, 상태|
|ConnectionAccepted|예|수락된 연결|개수|합계|수락된 연결|노드|
|ConnectionActive|예|활성 연결 수|개수|평균|활성 연결 수|노드|
|ConnectionHandled|예|처리된 연결|개수|합계|처리된 연결|노드|
|ConsensusEtcdraftCommittedBlockNumber|예|합의 Etccdraft 커밋된 블록 번호|개수|평균|커밋된 최신 블록의 블록 번호|노드, 채널|
|CpuUsagePercentageInDouble|예|CPU 사용 백분율|백분율|최대|CPU 사용 백분율|노드|
|EndorserEndorsementFailures|예|Endorser 인증 실패|개수|평균|실패 한 인증 수입니다.|Node, channel, chaincode, chaincodeerror|
|GossipLeaderElectionLeader|예|십 리더 선거 지도자|개수|합계|피어가 리더 (1) 또는 종동체 (0)입니다.|노드, 채널|
|GossipMembershipTotalPeersKnown|예|십 멤버 자격이 알려진 총 피어|개수|평균|알려진 총 피어|노드, 채널|
|GossipStateHeight|예|십 상태 높이|개수|평균|현재 원장 높이|노드, 채널|
|IOReadBytes|예|IO 읽기 바이트|바이트|합계|IO 읽기 바이트|노드|
|IOWriteBytes|예|IO 쓰기 바이트|바이트|합계|IO 쓰기 바이트|노드|
|LedgerTransactionCount|예|원장 트랜잭션 수|개수|평균|처리 된 트랜잭션 수|Node, channel, transaction_type, chaincode, validation_code|
|MemoryLimit|예|메모리 제한|바이트|평균|메모리 제한|노드|
|MemoryUsage|예|메모리 사용량|바이트|평균|메모리 사용량|노드|
|MemoryUsagePercentageInDouble|예|메모리 사용 백분율|백분율|평균|메모리 사용 백분율|노드|
|PendingTransactions|예|보류 중인 트랜잭션|개수|평균|보류 중인 트랜잭션|노드|
|ProcessedBlocks|예|처리된 블록|개수|합계|처리된 블록|노드|
|ProcessedTransactions|예|처리된 트랜잭션|개수|합계|처리된 트랜잭션|노드|
|QueuedTransactions|예|지연 트랜잭션|개수|평균|지연 트랜잭션|노드|
|RequestHandled|예|처리된 요청|개수|합계|처리된 요청|노드|
|StorageUsage|예|스토리지 사용량|바이트|평균|스토리지 사용량|노드|


## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|cachehits|예|캐시 적중|개수|합계||ShardId|
|cachehits0|예|캐시 적중(분할 0)|개수|합계||차원 없음|
|cachehits1|예|캐시 적중(분할 1)|개수|합계||차원 없음|
|cachehits2|예|캐시 적중(분할 2)|개수|합계||차원 없음|
|cachehits3|예|캐시 적중(분할 3)|개수|합계||차원 없음|
|cachehits4|예|캐시 적중(분할 4)|개수|합계||차원 없음|
|cachehits5|예|캐시 적중(분할 5)|개수|합계||차원 없음|
|cachehits6|예|캐시 적중(분할 6)|개수|합계||차원 없음|
|cachehits7|예|캐시 적중(분할 7)|개수|합계||차원 없음|
|cachehits8|예|캐시 적중(분할 8)|개수|합계||차원 없음|
|cachehits9|예|캐시 적중(분할 9)|개수|합계||차원 없음|
|cacheLatency|예|캐시 대기 시간 마이크로초(미리 보기)|개수|평균||ShardId|
|cachemisses|예|캐시 누락|개수|합계||ShardId|
|cachemisses0|예|캐시 누락(분할 0)|개수|합계||차원 없음|
|cachemisses1|예|캐시 누락(분할 1)|개수|합계||차원 없음|
|cachemisses2|예|캐시 누락(분할 2)|개수|합계||차원 없음|
|cachemisses3|예|캐시 누락(분할 3)|개수|합계||차원 없음|
|cachemisses4|예|캐시 누락(분할 4)|개수|합계||차원 없음|
|cachemisses5|예|캐시 누락(분할 5)|개수|합계||차원 없음|
|cachemisses6|예|캐시 누락(분할 6)|개수|합계||차원 없음|
|cachemisses7|예|캐시 누락(분할 7)|개수|합계||차원 없음|
|cachemisses8|예|캐시 누락(분할 8)|개수|합계||차원 없음|
|cachemisses9|예|캐시 누락(분할 9)|개수|합계||차원 없음|
|cachemissrate|예|캐시 누락 률|백분율|cachemissrate||ShardId|
|cacheRead|예|캐시 읽기|초당 바이트 수|최대||ShardId|
|cacheRead0|예|캐시 읽기(분할 0)|초당 바이트 수|최대||차원 없음|
|cacheRead1|예|캐시 읽기(분할 1)|초당 바이트 수|최대||차원 없음|
|cacheRead2|예|캐시 읽기(분할 2)|초당 바이트 수|최대||차원 없음|
|cacheRead3|예|캐시 읽기(분할 3)|초당 바이트 수|최대||차원 없음|
|cacheRead4|예|캐시 읽기(분할 4)|초당 바이트 수|최대||차원 없음|
|cacheRead5|예|캐시 읽기(분할 5)|초당 바이트 수|최대||차원 없음|
|cacheRead6|예|캐시 읽기(분할 6)|초당 바이트 수|최대||차원 없음|
|cacheRead7|예|캐시 읽기(분할 7)|초당 바이트 수|최대||차원 없음|
|cacheRead8|예|캐시 읽기(분할 8)|초당 바이트 수|최대||차원 없음|
|cacheRead9|예|캐시 읽기(분할 9)|초당 바이트 수|최대||차원 없음|
|cacheWrite|예|캐시 쓰기|초당 바이트 수|최대||ShardId|
|cacheWrite0|예|캐시 쓰기(분할 0)|초당 바이트 수|최대||차원 없음|
|cacheWrite1|예|캐시 쓰기(분할 1)|초당 바이트 수|최대||차원 없음|
|cacheWrite2|예|캐시 쓰기(분할 2)|초당 바이트 수|최대||차원 없음|
|cacheWrite3|예|캐시 쓰기(분할 3)|초당 바이트 수|최대||차원 없음|
|cacheWrite4|예|캐시 쓰기(분할 4)|초당 바이트 수|최대||차원 없음|
|cacheWrite5|예|캐시 쓰기(분할 5)|초당 바이트 수|최대||차원 없음|
|cacheWrite6|예|캐시 쓰기(분할 6)|초당 바이트 수|최대||차원 없음|
|cacheWrite7|예|캐시 쓰기(분할 7)|초당 바이트 수|최대||차원 없음|
|cacheWrite8|예|캐시 쓰기(분할 8)|초당 바이트 수|최대||차원 없음|
|cacheWrite9|예|캐시 쓰기(분할 9)|초당 바이트 수|최대||차원 없음|
|connectedclients|예|연결된 클라이언트|개수|최대||ShardId|
|connectedclients0|예|연결된 클라이언트(분할 0)|개수|최대||차원 없음|
|connectedclients1|예|연결된 클라이언트(분할 1)|개수|최대||차원 없음|
|connectedclients2|예|연결된 클라이언트(분할 2)|개수|최대||차원 없음|
|connectedclients3|예|연결된 클라이언트(분할 3)|개수|최대||차원 없음|
|connectedclients4|예|연결된 클라이언트(분할 4)|개수|최대||차원 없음|
|connectedclients5|예|연결된 클라이언트(분할 5)|개수|최대||차원 없음|
|connectedclients6|예|연결된 클라이언트(분할 6)|개수|최대||차원 없음|
|connectedclients7|예|연결된 클라이언트(분할 7)|개수|최대||차원 없음|
|connectedclients8|예|연결된 클라이언트(분할 8)|개수|최대||차원 없음|
|connectedclients9|예|연결된 클라이언트(분할 9)|개수|최대||차원 없음|
|오류|예|오류|개수|최대||ShardId, ErrorType|
|evictedkeys|예|제거된 키|개수|합계||ShardId|
|evictedkeys0|예|제거된 키(분할 0)|개수|합계||차원 없음|
|evictedkeys1|예|제거된 키(분할 1)|개수|합계||차원 없음|
|evictedkeys2|예|제거된 키(분할 2)|개수|합계||차원 없음|
|evictedkeys3|예|제거된 키(분할 3)|개수|합계||차원 없음|
|evictedkeys4|예|제거된 키(분할 4)|개수|합계||차원 없음|
|evictedkeys5|예|제거된 키(분할 5)|개수|합계||차원 없음|
|evictedkeys6|예|제거된 키(분할 6)|개수|합계||차원 없음|
|evictedkeys7|예|제거된 키(분할 7)|개수|합계||차원 없음|
|evictedkeys8|예|제거된 키(분할 8)|개수|합계||차원 없음|
|evictedkeys9|예|제거된 키(분할 9)|개수|합계||차원 없음|
|expiredkeys|예|만료된 키|개수|합계||ShardId|
|expiredkeys0|예|만료된 키(분할 0)|개수|합계||차원 없음|
|expiredkeys1|예|만료된 키(분할 1)|개수|합계||차원 없음|
|expiredkeys2|예|만료된 키(분할 2)|개수|합계||차원 없음|
|expiredkeys3|예|만료된 키(분할 3)|개수|합계||차원 없음|
|expiredkeys4|예|만료된 키(분할 4)|개수|합계||차원 없음|
|expiredkeys5|예|만료된 키(분할 5)|개수|합계||차원 없음|
|expiredkeys6|예|만료된 키(분할 6)|개수|합계||차원 없음|
|expiredkeys7|예|만료된 키(분할 7)|개수|합계||차원 없음|
|expiredkeys8|예|만료된 키(분할 8)|개수|합계||차원 없음|
|expiredkeys9|예|만료된 키(분할 9)|개수|합계||차원 없음|
|getcommands|예|가져오기|개수|합계||ShardId|
|getcommands0|예|가져오기(분할 0)|개수|합계||차원 없음|
|getcommands1|예|가져오기(분할 1)|개수|합계||차원 없음|
|getcommands2|예|가져오기(분할 2)|개수|합계||차원 없음|
|getcommands3|예|가져오기(분할 3)|개수|합계||차원 없음|
|getcommands4|예|가져오기(분할 4)|개수|합계||차원 없음|
|getcommands5|예|가져오기(분할 5)|개수|합계||차원 없음|
|getcommands6|예|가져오기(분할 6)|개수|합계||차원 없음|
|getcommands7|예|가져오기(분할 7)|개수|합계||차원 없음|
|getcommands8|예|가져오기(분할 8)|개수|합계||차원 없음|
|getcommands9|예|가져오기(분할 9)|개수|합계||차원 없음|
|operationsPerSecond|예|초당 작업|개수|최대||ShardId|
|operationsPerSecond0|예|초당 작업(분할 0)|개수|최대||차원 없음|
|operationsPerSecond1|예|초당 작업(분할 1)|개수|최대||차원 없음|
|operationsPerSecond2|예|초당 작업(분할 2)|개수|최대||차원 없음|
|operationsPerSecond3|예|초당 작업(분할 3)|개수|최대||차원 없음|
|operationsPerSecond4|예|초당 작업(분할 4)|개수|최대||차원 없음|
|operationsPerSecond5|예|초당 작업(분할 5)|개수|최대||차원 없음|
|operationsPerSecond6|예|초당 작업(분할 6)|개수|최대||차원 없음|
|operationsPerSecond7|예|초당 작업(분할 7)|개수|최대||차원 없음|
|operationsPerSecond8|예|초당 작업(분할 8)|개수|최대||차원 없음|
|operationsPerSecond9|예|초당 작업(분할 9)|개수|최대||차원 없음|
|percentProcessorTime|예|CPU|백분율|최대||ShardId|
|percentProcessorTime0|예|CPU(분할 0)|백분율|최대||차원 없음|
|percentProcessorTime1|예|CPU(분할 1)|백분율|최대||차원 없음|
|percentProcessorTime2|예|CPU(분할 2)|백분율|최대||차원 없음|
|percentProcessorTime3|예|CPU(분할 3)|백분율|최대||차원 없음|
|percentProcessorTime4|예|CPU(분할 4)|백분율|최대||차원 없음|
|percentProcessorTime5|예|CPU(분할 5)|백분율|최대||차원 없음|
|percentProcessorTime6|예|CPU(분할 6)|백분율|최대||차원 없음|
|percentProcessorTime7|예|CPU(분할 7)|백분율|최대||차원 없음|
|percentProcessorTime8|예|CPU(분할 8)|백분율|최대||차원 없음|
|percentProcessorTime9|예|CPU(분할 9)|백분율|최대||차원 없음|
|serverLoad|예|서버 부하|백분율|최대||ShardId|
|serverLoad0|예|서버 부하(분할 0)|백분율|최대||차원 없음|
|serverLoad1|예|서버 부하(분할 1)|백분율|최대||차원 없음|
|serverLoad2|예|서버 부하(분할 2)|백분율|최대||차원 없음|
|serverLoad3|예|서버 부하(분할 3)|백분율|최대||차원 없음|
|serverLoad4|예|서버 부하(분할 4)|백분율|최대||차원 없음|
|serverLoad5|예|서버 부하(분할 5)|백분율|최대||차원 없음|
|serverLoad6|예|서버 부하(분할 6)|백분율|최대||차원 없음|
|serverLoad7|예|서버 부하(분할 7)|백분율|최대||차원 없음|
|serverLoad8|예|서버 부하(분할 8)|백분율|최대||차원 없음|
|serverLoad9|예|서버 부하(분할 9)|백분율|최대||차원 없음|
|setcommands|예|집합|개수|합계||ShardId|
|setcommands0|예|설정(분할 0)|개수|합계||차원 없음|
|setcommands1|예|집합(분할 1)|개수|합계||차원 없음|
|setcommands2|예|설정(분할 2)|개수|합계||차원 없음|
|setcommands3|예|설정(분할 3)|개수|합계||차원 없음|
|setcommands4|예|설정(분할 4)|개수|합계||차원 없음|
|setcommands5|예|설정(분할 5)|개수|합계||차원 없음|
|setcommands6|예|설정(분할 6)|개수|합계||차원 없음|
|setcommands7|예|설정(분할 7)|개수|합계||차원 없음|
|setcommands8|예|설정(분할 8)|개수|합계||차원 없음|
|setcommands9|예|설정(분할 9)|개수|합계||차원 없음|
|totalcommandsprocessed|예|총 작업|개수|합계||ShardId|
|totalcommandsprocessed0|예|총 작업(분할 0)|개수|합계||차원 없음|
|totalcommandsprocessed1|예|총 작업(분할 1)|개수|합계||차원 없음|
|totalcommandsprocessed2|예|총 작업(분할 2)|개수|합계||차원 없음|
|totalcommandsprocessed3|예|총 작업(분할 3)|개수|합계||차원 없음|
|totalcommandsprocessed4|예|총 작업(분할 4)|개수|합계||차원 없음|
|totalcommandsprocessed5|예|총 작업(분할 5)|개수|합계||차원 없음|
|totalcommandsprocessed6|예|총 작업(분할 6)|개수|합계||차원 없음|
|totalcommandsprocessed7|예|총 작업(분할 7)|개수|합계||차원 없음|
|totalcommandsprocessed8|예|총 작업(분할 8)|개수|합계||차원 없음|
|totalcommandsprocessed9|예|총 작업(분할 9)|개수|합계||차원 없음|
|totalkeys|예|전체 키|개수|최대||ShardId|
|totalkeys0|예|총 키(분할 0)|개수|최대||차원 없음|
|totalkeys1|예|총 키(분할 1)|개수|최대||차원 없음|
|totalkeys2|예|총 키(분할 2)|개수|최대||차원 없음|
|totalkeys3|예|총 키(분할 3)|개수|최대||차원 없음|
|totalkeys4|예|총 키(분할 4)|개수|최대||차원 없음|
|totalkeys5|예|총 키(분할 5)|개수|최대||차원 없음|
|totalkeys6|예|총 키(분할 6)|개수|최대||차원 없음|
|totalkeys7|예|총 키(분할 7)|개수|최대||차원 없음|
|totalkeys8|예|총 키(분할 8)|개수|최대||차원 없음|
|totalkeys9|예|총 키(분할 9)|개수|최대||차원 없음|
|usedmemory|예|사용된 메모리|바이트|최대||ShardId|
|usedmemory0|예|사용된 메모리(분할 0)|바이트|최대||차원 없음|
|usedmemory1|예|사용된 메모리(분할 1)|바이트|최대||차원 없음|
|usedmemory2|예|사용된 메모리(분할 2)|바이트|최대||차원 없음|
|usedmemory3|예|사용된 메모리(분할 3)|바이트|최대||차원 없음|
|usedmemory4|예|사용된 메모리(분할 4)|바이트|최대||차원 없음|
|usedmemory5|예|사용된 메모리(분할 5)|바이트|최대||차원 없음|
|usedmemory6|예|사용된 메모리(분할 6)|바이트|최대||차원 없음|
|usedmemory7|예|사용된 메모리(분할 7)|바이트|최대||차원 없음|
|usedmemory8|예|사용된 메모리(분할 8)|바이트|최대||차원 없음|
|usedmemory9|예|사용된 메모리(분할 9)|바이트|최대||차원 없음|
|usedmemorypercentage|예|사용된 메모리 비율|백분율|최대||ShardId|
|usedmemoryRss|예|사용된 메모리 RSS|바이트|최대||ShardId|
|usedmemoryRss0|예|사용된 메모리 RSS(분할 0)|바이트|최대||차원 없음|
|usedmemoryRss1|예|사용된 메모리 RSS(분할 1)|바이트|최대||차원 없음|
|usedmemoryRss2|예|사용된 메모리 RSS(분할 2)|바이트|최대||차원 없음|
|usedmemoryRss3|예|사용된 메모리 RSS(분할 3)|바이트|최대||차원 없음|
|usedmemoryRss4|예|사용된 메모리 RSS(분할 4)|바이트|최대||차원 없음|
|usedmemoryRss5|예|사용된 메모리 RSS(분할 5)|바이트|최대||차원 없음|
|usedmemoryRss6|예|사용된 메모리 RSS(분할 6)|바이트|최대||차원 없음|
|usedmemoryRss7|예|사용된 메모리 RSS(분할 7)|바이트|최대||차원 없음|
|usedmemoryRss8|예|사용된 메모리 RSS(분할 8)|바이트|최대||차원 없음|
|usedmemoryRss9|예|사용된 메모리 RSS(분할 9)|바이트|최대||차원 없음|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft Cdn/cdnwebapplicationfirewallpolicies

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|예|웹 애플리케이션 방화벽 요청 수|개수|합계|웹 애플리케이션 방화벽에서 처리된 클라이언트 요청 수|PolicyName, RuleName, Action|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|디스크 읽기 바이트/초|아니요|디스크 읽기|초당 바이트 수|평균|모니터링 기간 동안 디스크에서 읽은 평균 바이트.|RoleInstanceId|
|디스크 읽기 작업/초|예|디스크 읽기 작업/초|초당 개수|평균|디스크 읽기 IOPS.|RoleInstanceId|
|디스크 쓰기 바이트/초|아니요|디스크 쓰기|초당 바이트 수|평균|모니터링 기간 동안 디스크에 쓴 평균 바이트.|RoleInstanceId|
|디스크 쓰기 작업/초|예|디스크 쓰기 작업/초|초당 개수|평균|디스크 쓰기 IOPS.|RoleInstanceId|
|네트워크 인|예|네트워크 인|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 수신한(들어오는 트래픽) 바이트 수.|RoleInstanceId|
|네트워크 아웃|예|네트워크 아웃|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 내보낸(나가는 트래픽) 바이트 수.|RoleInstanceId|
|백분율 CPU|예|백분율 CPU|백분율|평균|현재 Virtual Machine에서 사용 중인 할당된 컴퓨팅 단위의 백분율.|RoleInstanceId|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|디스크 읽기 바이트/초|아니요|디스크 읽기|초당 바이트 수|평균|모니터링 기간 동안 디스크에서 읽은 평균 바이트.|차원 없음|
|디스크 읽기 작업/초|예|디스크 읽기 작업/초|초당 개수|평균|디스크 읽기 IOPS.|차원 없음|
|디스크 쓰기 바이트/초|아니요|디스크 쓰기|초당 바이트 수|평균|모니터링 기간 동안 디스크에 쓴 평균 바이트.|차원 없음|
|디스크 쓰기 작업/초|예|디스크 쓰기 작업/초|초당 개수|평균|디스크 쓰기 IOPS.|차원 없음|
|네트워크 인|예|네트워크 인|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 수신한(들어오는 트래픽) 바이트 수.|차원 없음|
|네트워크 아웃|예|네트워크 아웃|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 내보낸(나가는 트래픽) 바이트 수.|차원 없음|
|백분율 CPU|예|백분율 CPU|백분율|평균|현재 Virtual Machine에서 사용 중인 할당된 컴퓨팅 단위의 백분율.|차원 없음|


## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAccounts

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|사용 가능성|예|가용성|백분율|평균|스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|GeoType, ApiName, Authentication|
|송신|예|송신|바이트|합계|송신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|GeoType, ApiName, Authentication|
|수신|예|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|예|성공 E2E 대기 시간|밀리초|평균|저장소 서비스 또는 지정 된 API 작업에 대해 수행 된 성공한 요청의 종단 간 대기 시간 (밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|GeoType, ApiName, Authentication|
|SuccessServerLatency|예|성공 서버 대기 시간|밀리초|평균|성공한 요청을 처리 하기 위해 Azure Storage에서 사용 하는 대기 시간 (밀리초)입니다. 이 값은 SuccessE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|GeoType, ApiName, Authentication|
|트랜잭션|예|트랜잭션|개수|합계|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|ResponseType, GeoType, ApiName, Authentication|
|UsedCapacity|아니요|사용된 용량|바이트|평균|계정 사용 용량|차원 없음|


## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft.ClassicStorage/storageAccounts/blobServices

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|사용 가능성|예|가용성|백분율|평균|스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|GeoType, ApiName, Authentication|
|BlobCapacity|아니요|Blob 용량|바이트|평균|스토리지 계정의 Blob service가 사용하는 스토리지의 양(바이트)입니다.|BlobType, 계층|
|BlobCount|아니요|Blob 수|개수|평균|스토리지 계정의 Blob service에 있는 Blob 수입니다.|BlobType, 계층|
|ContainerCount|예|Blob 컨테이너 수|개수|평균|스토리지 계정의 Blob service에 있는 컨테이너 수입니다.|차원 없음|
|송신|예|송신|바이트|합계|송신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|GeoType, ApiName, Authentication|
|IndexCapacity|아니요|인덱스 용량|바이트|평균|ADLS Gen2 (계층적) 인덱스에서 사용 하는 저장소의 양 (바이트)입니다.|차원 없음|
|수신|예|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|예|성공 E2E 대기 시간|밀리초|평균|저장소 서비스 또는 지정 된 API 작업에 대해 수행 된 성공한 요청의 종단 간 대기 시간 (밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|GeoType, ApiName, Authentication|
|SuccessServerLatency|예|성공 서버 대기 시간|밀리초|평균|성공한 요청을 처리 하기 위해 Azure Storage에서 사용 하는 대기 시간 (밀리초)입니다. 이 값은 SuccessE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|GeoType, ApiName, Authentication|
|트랜잭션|예|트랜잭션|개수|합계|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft.ClassicStorage/storageAccounts/fileServices

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|사용 가능성|예|가용성|백분율|평균|스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|GeoType, ApiName, Authentication, 파일 공유|
|송신|예|송신|바이트|합계|송신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|GeoType, ApiName, Authentication, 파일 공유|
|FileCapacity|아니요|파일 용량|바이트|평균|스토리지 계정의 파일 서비스가 사용하는 스토리지의 양(바이트)입니다.|FileShare|
|FileCount|아니요|파일 수|개수|평균|스토리지 계정의 파일 서비스에 있는 파일 수입니다.|FileShare|
|FileShareCount|아니요|파일 공유 수|개수|평균|스토리지 계정의 파일 서비스에 있는 파일 공유 수입니다.|차원 없음|
|FileShareQuota|아니요|파일 공유 할당량 크기|바이트|평균|Azure Files 서비스에서 사용할 수 있는 저장소 크기의 상한 (바이트)입니다.|FileShare|
|FileShareSnapshotCount|아니요|파일 공유 스냅샷 개수|개수|평균|저장소 계정의 파일 서비스에서 공유에 있는 스냅숏의 수입니다.|FileShare|
|FileShareSnapshotSize|아니요|파일 공유 스냅샷 크기|바이트|평균|저장소 계정의 파일 서비스에서 스냅숏에서 사용 되는 저장소의 크기 (바이트)입니다.|FileShare|
|수신|예|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|GeoType, ApiName, Authentication, 파일 공유|
|SuccessE2ELatency|예|성공 E2E 대기 시간|밀리초|평균|저장소 서비스 또는 지정 된 API 작업에 대해 수행 된 성공한 요청의 종단 간 대기 시간 (밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|GeoType, ApiName, Authentication, 파일 공유|
|SuccessServerLatency|예|성공 서버 대기 시간|밀리초|평균|성공한 요청을 처리 하기 위해 Azure Storage에서 사용 하는 대기 시간 (밀리초)입니다. 이 값은 SuccessE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|GeoType, ApiName, Authentication, 파일 공유|
|트랜잭션|예|트랜잭션|개수|합계|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|ResponseType, GeoType, ApiName, Authentication, 파일 공유|


## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft.ClassicStorage/storageAccounts/queueServices

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|사용 가능성|예|가용성|백분율|평균|스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|GeoType, ApiName, Authentication|
|송신|예|송신|바이트|합계|송신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|GeoType, ApiName, Authentication|
|수신|예|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|GeoType, ApiName, Authentication|
|QueueCapacity|예|큐 용량|바이트|평균|스토리지 계정의 큐 서비스가 사용하는 스토리지의 양(바이트)입니다.|차원 없음|
|QueueCount|예|큐 수|개수|평균|스토리지 계정의 큐 서비스에 있는 큐 수입니다.|차원 없음|
|QueueMessageCount|예|큐 메시지 수|개수|평균|스토리지 계정의 큐 서비스에 있는 대략적인 큐 메시지 수입니다.|차원 없음|
|SuccessE2ELatency|예|성공 E2E 대기 시간|밀리초|평균|저장소 서비스 또는 지정 된 API 작업에 대해 수행 된 성공한 요청의 종단 간 대기 시간 (밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|GeoType, ApiName, Authentication|
|SuccessServerLatency|예|성공 서버 대기 시간|밀리초|평균|성공한 요청을 처리 하기 위해 Azure Storage에서 사용 하는 대기 시간 (밀리초)입니다. 이 값은 SuccessE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|GeoType, ApiName, Authentication|
|트랜잭션|예|트랜잭션|개수|합계|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft.ClassicStorage/storageAccounts/tableServices

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|사용 가능성|예|가용성|백분율|평균|스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|GeoType, ApiName, Authentication|
|송신|예|송신|바이트|합계|송신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|GeoType, ApiName, Authentication|
|수신|예|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|예|성공 E2E 대기 시간|밀리초|평균|저장소 서비스 또는 지정 된 API 작업에 대해 수행 된 성공한 요청의 종단 간 대기 시간 (밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|GeoType, ApiName, Authentication|
|SuccessServerLatency|예|성공 서버 대기 시간|밀리초|평균|성공한 요청을 처리 하기 위해 Azure Storage에서 사용 하는 대기 시간 (밀리초)입니다. 이 값은 SuccessE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|GeoType, ApiName, Authentication|
|TableCapacity|예|테이블 용량|바이트|평균|스토리지 계정의 Table service가 사용하는 스토리지의 양(바이트)입니다.|차원 없음|
|TableCount|예|테이블 수|개수|평균|스토리지 계정의 Table service에 있는 테이블 수입니다.|차원 없음|
|TableEntityCount|예|테이블 엔터티 수|개수|평균|스토리지 계정의 Table service에 있는 테이블 엔터티 수입니다.|차원 없음|
|트랜잭션|예|트랜잭션|개수|합계|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|BlockedCalls|예|차단된 호출|개수|합계|요금 또는 할당량 한도를 초과한 호출 수입니다.|ApiName, OperationName, 지역|
|CharactersTrained|예|학습된 문자|개수|합계|학습 된 총 문자 수입니다.|ApiName, OperationName, 지역|
|CharactersTranslated|예|변환된 문자|개수|합계|들어오는 텍스트 요청에 있는 문자의 총 수입니다.|ApiName, OperationName, 지역|
|ClientErrors|예|클라이언트 오류|개수|합계|클라이언트 쪽 오류(HTTP 응답 코드 4xx)가 있는 호출 수입니다.|ApiName, OperationName, 지역|
|DataIn|예|데이터 입력|바이트|합계|들어오는 데이터 크기(바이트)입니다.|ApiName, OperationName, 지역|
|DataOut|예|데이터 출력|바이트|합계|나가는 데이터 크기(바이트)입니다.|ApiName, OperationName, 지역|
|대기 시간|예|대기 시간|밀리초|평균|대기 시간(밀리초)입니다.|ApiName, OperationName, 지역|
|Processeages|예|처리 된 이미지|개수|합계| 이미지 처리에 대 한 트랜잭션 수입니다.|ApiName, FeatureName, UsageChannel, 지역|
|ServerErrors|예|서버 오류|개수|합계|서비스 내부 오류(HTTP 응답 코드 5xx)가 있는 호출 수입니다.|ApiName, OperationName, 지역|
|SpeechSessionDuration|예|음성 세션 기간|초|합계|음성 세션의 총 기간(초)입니다.|ApiName, OperationName, 지역|
|SuccessfulCalls|예|성공한 호출|개수|합계|성공한 호출 수입니다.|ApiName, OperationName, 지역|
|TotalCalls|예|총 호출|개수|합계|총 호출 수.|ApiName, OperationName, 지역|
|TotalErrors|예|총 오류|개수|합계|오류 응답(HTTP 응답 코드 4xx 또는 5xx)이 있는 총 호출 수입니다.|ApiName, OperationName, 지역|
|TotalTokenCalls|예|총 토큰 호출|개수|합계|총 토큰 호출 수입니다.|ApiName, OperationName, 지역|
|TotalTransactions|예|총 트랜잭션|개수|합계|총 트랜잭션 수|차원 없음|


## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|사용된 CPU 크레딧|예|사용된 CPU 크레딧|개수|평균|Virtual Machine에서 사용하는 총 크레딧 수|차원 없음|
|남은 CPU 크레딧|예|남은 CPU 크레딧|개수|평균|버스트에 사용할 수 있는 총 크레딧 수|차원 없음|
|데이터 디스크 큐 크기|예|데이터 디스크 큐 크기(미리 보기)|개수|평균|데이터 디스크 큐 깊이(또는 큐 길이)|LUN|
|데이터 디스크 읽기 바이트/초|예|데이터 디스크 읽기 바이트/초(미리 보기)|초당 개수|평균|모니터링 기간에 단일 디스크에서 읽은 바이트/초|LUN|
|데이터 디스크 읽기 작업/초|예|데이터 디스크 읽기 작업/초(미리 보기)|초당 개수|평균|모니터링 기간에 단일 디스크에서 IOPS 읽기|LUN|
|데이터 디스크 쓰기 바이트/초|예|데이터 디스크 쓰기 바이트/초(미리 보기)|초당 개수|평균|모니터링 기간 동안 단일 디스크에 쓴 바이트/초|LUN|
|데이터 디스크 쓰기 작업/초|예|데이터 디스크 쓰기 작업/초(미리 보기)|초당 개수|평균|모니터링 기간 동안 단일 디스크의 쓰기 IOPS|LUN|
|디스크 읽기 바이트|예|디스크 읽기 바이트|바이트|합계|모니터링 기간 동안 디스크에서 읽은 바이트 수|차원 없음|
|디스크 읽기 작업/초|예|디스크 읽기 작업/초|초당 개수|평균|디스크 읽기 IOPS|차원 없음|
|디스크 쓰기 바이트|예|디스크 쓰기 바이트|바이트|합계|모니터링 기간 동안 디스크에 쓴 바이트 수|차원 없음|
|디스크 쓰기 작업/초|예|디스크 쓰기 작업/초|초당 개수|평균|디스크 쓰기 IOPS|차원 없음|
|인바운드 흐름|예|인바운드 흐름|개수|평균|인바운드 흐름은 인바운드 방향의 현재 흐름 수 (VM으로 이동 하는 트래픽)입니다.|차원 없음|
|인바운드 흐름 최대 생성 속도|예|인바운드 흐름 최대 생성 속도|초당 개수|평균|인바운드 흐름의 최대 생성 률 (VM으로 이동 하는 트래픽)|차원 없음|
|네트워크 인|예|청구 가능 네트워크 입력(사용되지 않음)|바이트|합계|가상 머신에서 모든 네트워크 인터페이스에서 받은 청구 가능 바이트 수 (들어오는 트래픽) (사용 되지 않음)|차원 없음|
|전체 네트워크 입력|예|전체 네트워크 입력|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 수신한(들어오는 트래픽) 바이트 수|차원 없음|
|네트워크 아웃|예|청구 가능 네트워크 출력(사용되지 않음)|바이트|합계|가상 컴퓨터의 모든 네트워크 인터페이스에서 청구 가능한 바이트 수 (나가는 트래픽) (사용 되지 않음)|차원 없음|
|전체 네트워크 출력|예|전체 네트워크 출력|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 내보낸(나가는 트래픽) 바이트 수|차원 없음|
|OS 디스크 큐 크기|예|OS 디스크 큐 크기(미리 보기)|개수|평균|OS 디스크 큐 깊이(또는 큐 길이)|차원 없음|
|OS 디스크 읽기 바이트/초|예|OS 디스크 읽기 바이트/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 읽은 바이트/초|차원 없음|
|OS 디스크 읽기 작업/초|예|OS 디스크 읽기 작업/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 IOPS 읽기|차원 없음|
|OS 디스크 쓰기 바이트/초|예|OS 디스크 쓰기 바이트/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에 쓴 바이트/초|차원 없음|
|OS 디스크 쓰기 작업/초|예|OS 디스크 쓰기 작업/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 IOPS 쓰기|차원 없음|
|디스크당 OS QD|예|OS 디스크 QD(사용되지 않음)|개수|평균|OS 디스크 큐 깊이(또는 큐 길이)|차원 없음|
|디스크당 OS 읽기 바이트/초|예|OS 디스크 읽기 바이트/초(사용되지 않음)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 읽은 바이트/초|차원 없음|
|디스크당 OS 읽기 작업/초|예|OS 디스크 읽기 작업/초(사용되지 않음)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 IOPS 읽기|차원 없음|
|디스크당 OS 쓰기 바이트/초|예|OS 디스크 쓰기 바이트/초(사용되지 않음)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에 쓴 바이트/초|차원 없음|
|디스크당 OS 쓰기 작업/초|예|OS 디스크 쓰기 작업/초(사용되지 않음)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 IOPS 쓰기|차원 없음|
|아웃바운드 흐름|예|아웃바운드 흐름|개수|평균|아웃 바운드 흐름은 아웃 바운드 방향의 현재 흐름 수 (VM에서 나가는 트래픽)입니다.|차원 없음|
|아웃바운드 흐름 최대 생성 속도|예|아웃바운드 흐름 최대 생성 속도|초당 개수|평균|아웃 바운드 흐름의 최대 생성 률 (VM에서 나가는 트래픽)|차원 없음|
|디스크당 QD|예|데이터 디스크 QD(사용되지 않음)|개수|평균|데이터 디스크 큐 깊이(또는 큐 길이)|SlotId|
|디스크당 읽기 바이트/초|예|데이터 디스크 읽기 바이트/초(사용되지 않음)|초당 개수|평균|모니터링 기간에 단일 디스크에서 읽은 바이트/초|SlotId|
|디스크당 읽기 작업/초|예|데이터 디스크 읽기 작업/초(사용되지 않음)|초당 개수|평균|모니터링 기간에 단일 디스크에서 IOPS 읽기|SlotId|
|디스크당 쓰기 바이트/초|예|데이터 디스크 쓰기 바이트/초(사용되지 않음)|초당 개수|평균|모니터링 기간 동안 단일 디스크에 쓴 바이트/초|SlotId|
|디스크당 쓰기 작업/초|예|데이터 디스크 쓰기 작업/초(사용되지 않음)|초당 개수|평균|모니터링 기간 동안 단일 디스크의 쓰기 IOPS|SlotId|
|백분율 CPU|예|백분율 CPU|백분율|평균|현재 Virtual Machine에서 사용 중인 할당된 컴퓨팅 단위의 백분율|차원 없음|
|프리미엄 데이터 디스크 캐시 읽기 적중|예|프리미엄 데이터 디스크 캐시 읽기 적중(미리 보기)|백분율|평균|프리미엄 데이터 디스크 캐시 읽기 적중|LUN|
|프리미엄 데이터 디스크 캐시 읽기 누락|예|프리미엄 데이터 디스크 캐시 읽기 누락(미리 보기)|백분율|평균|프리미엄 데이터 디스크 캐시 읽기 누락|LUN|
|프리미엄 OS 디스크 캐시 읽기 적중|예|프리미엄 OS 디스크 캐시 읽기 적중(미리 보기)|백분율|평균|프리미엄 OS 디스크 캐시 읽기 적중|차원 없음|
|프리미엄 OS 디스크 캐시 읽기 누락|예|프리미엄 OS 디스크 캐시 읽기 누락(미리 보기)|백분율|평균|프리미엄 OS 디스크 캐시 읽기 누락|차원 없음|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|사용된 CPU 크레딧|예|사용된 CPU 크레딧|개수|평균|Virtual Machine에서 사용하는 총 크레딧 수|차원 없음|
|남은 CPU 크레딧|예|남은 CPU 크레딧|개수|평균|버스트에 사용할 수 있는 총 크레딧 수|차원 없음|
|데이터 디스크 큐 크기|예|데이터 디스크 큐 크기(미리 보기)|개수|평균|데이터 디스크 큐 깊이(또는 큐 길이)|LUN, VMName|
|데이터 디스크 읽기 바이트/초|예|데이터 디스크 읽기 바이트/초(미리 보기)|초당 개수|평균|모니터링 기간에 단일 디스크에서 읽은 바이트/초|LUN, VMName|
|데이터 디스크 읽기 작업/초|예|데이터 디스크 읽기 작업/초(미리 보기)|초당 개수|평균|모니터링 기간에 단일 디스크에서 IOPS 읽기|LUN, VMName|
|데이터 디스크 쓰기 바이트/초|예|데이터 디스크 쓰기 바이트/초(미리 보기)|초당 개수|평균|모니터링 기간 동안 단일 디스크에 쓴 바이트/초|LUN, VMName|
|데이터 디스크 쓰기 작업/초|예|데이터 디스크 쓰기 작업/초(미리 보기)|초당 개수|평균|모니터링 기간 동안 단일 디스크의 쓰기 IOPS|LUN, VMName|
|디스크 읽기 바이트|예|디스크 읽기 바이트|바이트|합계|모니터링 기간 동안 디스크에서 읽은 바이트 수|VMName|
|디스크 읽기 작업/초|예|디스크 읽기 작업/초|초당 개수|평균|디스크 읽기 IOPS|VMName|
|디스크 쓰기 바이트|예|디스크 쓰기 바이트|바이트|합계|모니터링 기간 동안 디스크에 쓴 바이트 수|VMName|
|디스크 쓰기 작업/초|예|디스크 쓰기 작업/초|초당 개수|평균|디스크 쓰기 IOPS|VMName|
|인바운드 흐름|예|인바운드 흐름|개수|평균|인바운드 흐름은 인바운드 방향의 현재 흐름 수 (VM으로 이동 하는 트래픽)입니다.|VMName|
|인바운드 흐름 최대 생성 속도|예|인바운드 흐름 최대 생성 속도|초당 개수|평균|인바운드 흐름의 최대 생성 률 (VM으로 이동 하는 트래픽)|VMName|
|네트워크 인|예|청구 가능 네트워크 입력(사용되지 않음)|바이트|합계|가상 머신에서 모든 네트워크 인터페이스에서 받은 청구 가능 바이트 수 (들어오는 트래픽) (사용 되지 않음)|VMName|
|전체 네트워크 입력|예|전체 네트워크 입력|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 수신한(들어오는 트래픽) 바이트 수|VMName|
|네트워크 아웃|예|청구 가능 네트워크 출력(사용되지 않음)|바이트|합계|가상 컴퓨터의 모든 네트워크 인터페이스에서 청구 가능한 바이트 수 (나가는 트래픽) (사용 되지 않음)|VMName|
|전체 네트워크 출력|예|전체 네트워크 출력|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 내보낸(나가는 트래픽) 바이트 수|VMName|
|OS 디스크 큐 크기|예|OS 디스크 큐 크기(미리 보기)|개수|평균|OS 디스크 큐 깊이(또는 큐 길이)|VMName|
|OS 디스크 읽기 바이트/초|예|OS 디스크 읽기 바이트/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 읽은 바이트/초|VMName|
|OS 디스크 읽기 작업/초|예|OS 디스크 읽기 작업/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 IOPS 읽기|VMName|
|OS 디스크 쓰기 바이트/초|예|OS 디스크 쓰기 바이트/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에 쓴 바이트/초|VMName|
|OS 디스크 쓰기 작업/초|예|OS 디스크 쓰기 작업/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 IOPS 쓰기|VMName|
|디스크당 OS QD|예|OS 디스크 QD(사용되지 않음)|개수|평균|OS 디스크 큐 깊이(또는 큐 길이)|차원 없음|
|디스크당 OS 읽기 바이트/초|예|OS 디스크 읽기 바이트/초(사용되지 않음)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 읽은 바이트/초|차원 없음|
|디스크당 OS 읽기 작업/초|예|OS 디스크 읽기 작업/초(사용되지 않음)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 IOPS 읽기|차원 없음|
|디스크당 OS 쓰기 바이트/초|예|OS 디스크 쓰기 바이트/초(사용되지 않음)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에 쓴 바이트/초|차원 없음|
|디스크당 OS 쓰기 작업/초|예|OS 디스크 쓰기 작업/초(사용되지 않음)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 IOPS 쓰기|차원 없음|
|아웃바운드 흐름|예|아웃바운드 흐름|개수|평균|아웃 바운드 흐름은 아웃 바운드 방향의 현재 흐름 수 (VM에서 나가는 트래픽)입니다.|VMName|
|아웃바운드 흐름 최대 생성 속도|예|아웃바운드 흐름 최대 생성 속도|초당 개수|평균|아웃 바운드 흐름의 최대 생성 률 (VM에서 나가는 트래픽)|VMName|
|디스크당 QD|예|데이터 디스크 QD(사용되지 않음)|개수|평균|데이터 디스크 큐 깊이(또는 큐 길이)|SlotId|
|디스크당 읽기 바이트/초|예|데이터 디스크 읽기 바이트/초(사용되지 않음)|초당 개수|평균|모니터링 기간에 단일 디스크에서 읽은 바이트/초|SlotId|
|디스크당 읽기 작업/초|예|데이터 디스크 읽기 작업/초(사용되지 않음)|초당 개수|평균|모니터링 기간에 단일 디스크에서 IOPS 읽기|SlotId|
|디스크당 쓰기 바이트/초|예|데이터 디스크 쓰기 바이트/초(사용되지 않음)|초당 개수|평균|모니터링 기간 동안 단일 디스크에 쓴 바이트/초|SlotId|
|디스크당 쓰기 작업/초|예|데이터 디스크 쓰기 작업/초(사용되지 않음)|초당 개수|평균|모니터링 기간 동안 단일 디스크의 쓰기 IOPS|SlotId|
|백분율 CPU|예|백분율 CPU|백분율|평균|현재 Virtual Machine에서 사용 중인 할당된 컴퓨팅 단위의 백분율|VMName|
|프리미엄 데이터 디스크 캐시 읽기 적중|예|프리미엄 데이터 디스크 캐시 읽기 적중(미리 보기)|백분율|평균|프리미엄 데이터 디스크 캐시 읽기 적중|LUN, VMName|
|프리미엄 데이터 디스크 캐시 읽기 누락|예|프리미엄 데이터 디스크 캐시 읽기 누락(미리 보기)|백분율|평균|프리미엄 데이터 디스크 캐시 읽기 누락|LUN, VMName|
|프리미엄 OS 디스크 캐시 읽기 적중|예|프리미엄 OS 디스크 캐시 읽기 적중(미리 보기)|백분율|평균|프리미엄 OS 디스크 캐시 읽기 적중|VMName|
|프리미엄 OS 디스크 캐시 읽기 누락|예|프리미엄 OS 디스크 캐시 읽기 누락(미리 보기)|백분율|평균|프리미엄 OS 디스크 캐시 읽기 누락|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|사용된 CPU 크레딧|예|사용된 CPU 크레딧|개수|평균|Virtual Machine에서 사용하는 총 크레딧 수|차원 없음|
|남은 CPU 크레딧|예|남은 CPU 크레딧|개수|평균|버스트에 사용할 수 있는 총 크레딧 수|차원 없음|
|데이터 디스크 큐 크기|예|데이터 디스크 큐 크기(미리 보기)|개수|평균|데이터 디스크 큐 깊이(또는 큐 길이)|LUN|
|데이터 디스크 읽기 바이트/초|예|데이터 디스크 읽기 바이트/초(미리 보기)|초당 개수|평균|모니터링 기간에 단일 디스크에서 읽은 바이트/초|LUN|
|데이터 디스크 읽기 작업/초|예|데이터 디스크 읽기 작업/초(미리 보기)|초당 개수|평균|모니터링 기간에 단일 디스크에서 IOPS 읽기|LUN|
|데이터 디스크 쓰기 바이트/초|예|데이터 디스크 쓰기 바이트/초(미리 보기)|초당 개수|평균|모니터링 기간 동안 단일 디스크에 쓴 바이트/초|LUN|
|데이터 디스크 쓰기 작업/초|예|데이터 디스크 쓰기 작업/초(미리 보기)|초당 개수|평균|모니터링 기간 동안 단일 디스크의 쓰기 IOPS|LUN|
|디스크 읽기 바이트|예|디스크 읽기 바이트|바이트|합계|모니터링 기간 동안 디스크에서 읽은 바이트 수|차원 없음|
|디스크 읽기 작업/초|예|디스크 읽기 작업/초|초당 개수|평균|디스크 읽기 IOPS|차원 없음|
|디스크 쓰기 바이트|예|디스크 쓰기 바이트|바이트|합계|모니터링 기간 동안 디스크에 쓴 바이트 수|차원 없음|
|디스크 쓰기 작업/초|예|디스크 쓰기 작업/초|초당 개수|평균|디스크 쓰기 IOPS|차원 없음|
|인바운드 흐름|예|인바운드 흐름|개수|평균|인바운드 흐름은 인바운드 방향의 현재 흐름 수 (VM으로 이동 하는 트래픽)입니다.|차원 없음|
|인바운드 흐름 최대 생성 속도|예|인바운드 흐름 최대 생성 속도|초당 개수|평균|인바운드 흐름의 최대 생성 률 (VM으로 이동 하는 트래픽)|차원 없음|
|네트워크 인|예|청구 가능 네트워크 입력(사용되지 않음)|바이트|합계|가상 머신에서 모든 네트워크 인터페이스에서 받은 청구 가능 바이트 수 (들어오는 트래픽) (사용 되지 않음)|차원 없음|
|전체 네트워크 입력|예|전체 네트워크 입력|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 수신한(들어오는 트래픽) 바이트 수|차원 없음|
|네트워크 아웃|예|청구 가능 네트워크 출력(사용되지 않음)|바이트|합계|가상 컴퓨터의 모든 네트워크 인터페이스에서 청구 가능한 바이트 수 (나가는 트래픽) (사용 되지 않음)|차원 없음|
|전체 네트워크 출력|예|전체 네트워크 출력|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 내보낸(나가는 트래픽) 바이트 수|차원 없음|
|OS 디스크 큐 크기|예|OS 디스크 큐 크기(미리 보기)|개수|평균|OS 디스크 큐 깊이(또는 큐 길이)|차원 없음|
|OS 디스크 읽기 바이트/초|예|OS 디스크 읽기 바이트/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 읽은 바이트/초|차원 없음|
|OS 디스크 읽기 작업/초|예|OS 디스크 읽기 작업/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 IOPS 읽기|차원 없음|
|OS 디스크 쓰기 바이트/초|예|OS 디스크 쓰기 바이트/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에 쓴 바이트/초|차원 없음|
|OS 디스크 쓰기 작업/초|예|OS 디스크 쓰기 작업/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 IOPS 쓰기|차원 없음|
|디스크당 OS QD|예|OS 디스크 QD(사용되지 않음)|개수|평균|OS 디스크 큐 깊이(또는 큐 길이)|차원 없음|
|디스크당 OS 읽기 바이트/초|예|OS 디스크 읽기 바이트/초(사용되지 않음)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 읽은 바이트/초|차원 없음|
|디스크당 OS 읽기 작업/초|예|OS 디스크 읽기 작업/초(사용되지 않음)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 IOPS 읽기|차원 없음|
|디스크당 OS 쓰기 바이트/초|예|OS 디스크 쓰기 바이트/초(사용되지 않음)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에 쓴 바이트/초|차원 없음|
|디스크당 OS 쓰기 작업/초|예|OS 디스크 쓰기 작업/초(사용되지 않음)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 IOPS 쓰기|차원 없음|
|아웃바운드 흐름|예|아웃바운드 흐름|개수|평균|아웃 바운드 흐름은 아웃 바운드 방향의 현재 흐름 수 (VM에서 나가는 트래픽)입니다.|차원 없음|
|아웃바운드 흐름 최대 생성 속도|예|아웃바운드 흐름 최대 생성 속도|초당 개수|평균|아웃 바운드 흐름의 최대 생성 률 (VM에서 나가는 트래픽)|차원 없음|
|디스크당 QD|예|데이터 디스크 QD(사용되지 않음)|개수|평균|데이터 디스크 큐 깊이(또는 큐 길이)|SlotId|
|디스크당 읽기 바이트/초|예|데이터 디스크 읽기 바이트/초(사용되지 않음)|초당 개수|평균|모니터링 기간에 단일 디스크에서 읽은 바이트/초|SlotId|
|디스크당 읽기 작업/초|예|데이터 디스크 읽기 작업/초(사용되지 않음)|초당 개수|평균|모니터링 기간에 단일 디스크에서 IOPS 읽기|SlotId|
|디스크당 쓰기 바이트/초|예|데이터 디스크 쓰기 바이트/초(사용되지 않음)|초당 개수|평균|모니터링 기간 동안 단일 디스크에 쓴 바이트/초|SlotId|
|디스크당 쓰기 작업/초|예|데이터 디스크 쓰기 작업/초(사용되지 않음)|초당 개수|평균|모니터링 기간 동안 단일 디스크의 쓰기 IOPS|SlotId|
|백분율 CPU|예|백분율 CPU|백분율|평균|현재 Virtual Machine에서 사용 중인 할당된 컴퓨팅 단위의 백분율|차원 없음|
|프리미엄 데이터 디스크 캐시 읽기 적중|예|프리미엄 데이터 디스크 캐시 읽기 적중(미리 보기)|백분율|평균|프리미엄 데이터 디스크 캐시 읽기 적중|LUN|
|프리미엄 데이터 디스크 캐시 읽기 누락|예|프리미엄 데이터 디스크 캐시 읽기 누락(미리 보기)|백분율|평균|프리미엄 데이터 디스크 캐시 읽기 누락|LUN|
|프리미엄 OS 디스크 캐시 읽기 적중|예|프리미엄 OS 디스크 캐시 읽기 적중(미리 보기)|백분율|평균|프리미엄 OS 디스크 캐시 읽기 적중|차원 없음|
|프리미엄 OS 디스크 캐시 읽기 누락|예|프리미엄 OS 디스크 캐시 읽기 누락(미리 보기)|백분율|평균|프리미엄 OS 디스크 캐시 읽기 누락|차원 없음|


## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|CpuUsage|예|CPU 사용량|개수|평균|모든 코어의 CPU 사용량(밀리코어)|containerName|
|MemoryUsage|예|메모리 사용량|바이트|평균|총 메모리 사용량(바이트)|containerName|
|NetworkBytesReceivedPerSecond|예|초당 수신된 네트워크 바이트|바이트|평균|초당 수신된 네트워크 바이트입니다.|차원 없음|
|NetworkBytesTransmittedPerSecond|예|초당 전송된 네트워크 바이트|바이트|평균|초당 전송된 네트워크 바이트입니다.|차원 없음|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|AgentPoolCPUTime|예|AgentPool CPU 시간|초|합계|AgentPool CPU 시간 (초)|차원 없음|
|RunDuration|예|실행 기간|밀리초|합계|실행 지속 시간 (밀리초)|차원 없음|
|SuccessfulPullCount|예|성공한 끌어오기 횟수|개수|평균|성공한 이미지 끌어오기 수|차원 없음|
|SuccessfulPushCount|예|성공한 밀어넣기 횟수|개수|평균|성공한 이미지 푸시 수|차원 없음|
|TotalPullCount|예|총 끌어오기 횟수|개수|평균|총 이미지 가져오기 수|차원 없음|
|TotalPushCount|예|총 밀어넣기 횟수|개수|평균|총 이미지 푸시 수|차원 없음|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|아니요|관리 클러스터에서 사용 가능한 cpu 코어의 총 수|개수|평균|관리 클러스터에서 사용 가능한 cpu 코어의 총 수|차원 없음|
|kube_node_status_allocatable_memory_bytes|아니요|관리 클러스터에서 사용 가능한 총 메모리 양|바이트|평균|관리 클러스터에서 사용 가능한 총 메모리 양|차원 없음|
|kube_node_status_condition|아니요|다양한 노드 조건에 대한 상태|개수|평균|다양한 노드 조건에 대한 상태|조건, 상태, status2, 노드|
|kube_pod_status_phase|아니요|단계별 Pod 수|개수|평균|단계별 Pod 수|단계, 네임스페이스, Pod|
|kube_pod_status_ready|아니요|준비 상태인 Pod 수|개수|평균|준비 상태인 Pod 수|네임스페이스, Pod|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft CustomProviders/resourceproviders

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|FailedRequests|예|실패한 요청|개수|합계|사용자 지정 리소스 공급자에 대해 사용 가능한 로그를 가져옵니다.|HttpMethod, CallPath, StatusCode|
|SuccessfullRequests|예|성공한 요청|개수|합계|사용자 지정 공급자가 수행한 성공한 요청|HttpMethod, CallPath, StatusCode|


## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|AvailableCapacity|예|사용 가능한 용량|바이트|평균|보고 기간 동안 사용 가능한 용량 (바이트)입니다.|차원 없음|
|BytesUploadedToCloud|예|업로드된 클라우드 바이트(디바이스)|바이트|평균|보고 기간 동안 장치에서 Azure로 업로드 되는 총 바이트 수입니다.|차원 없음|
|BytesUploadedToCloudPerShare|예|업로드된 클라우드 바이트(공유)|바이트|평균|보고 기간 동안 공유에서 Azure로 업로드 되는 총 바이트 수입니다.|공유|
|CloudReadThroughput|예|클라우드 다운로드 처리량|초당 바이트 수|평균|보고 기간 동안 Azure에 대 한 클라우드 다운로드 처리량입니다.|차원 없음|
|CloudReadThroughputPerShare|예|클라우드 다운로드 처리량(공유)|초당 바이트 수|평균|보고 기간 동안 공유에서 Azure로의 다운로드 처리량입니다.|공유|
|CloudUploadThroughput|예|클라우드 업로드 처리량|초당 바이트 수|평균|보고 기간 동안 Azure에 대 한 클라우드 업로드 처리량입니다.|차원 없음|
|CloudUploadThroughputPerShare|예|클라우드 업로드 처리량(공유)|초당 바이트 수|평균|보고 기간 동안 공유에서 Azure로의 업로드 처리량입니다.|공유|
|HyperVMemoryUtilization|예|Edge 컴퓨팅 - 메모리 사용량|백분율|평균|사용 중인 RAM의 양|InstanceName|
|HyperVVirtualProcessorUtilization|예|Edge 컴퓨팅 - 백분율 CPU|백분율|평균|CPU 사용률 (%)|InstanceName|
|NICReadThroughput|예|읽기 처리량(네트워크)|초당 바이트 수|평균|게이트웨이의 모든 볼륨에 대 한 보고 기간의 장치에서 네트워크 인터페이스의 읽기 처리량입니다.|InstanceName|
|NICWriteThroughput|예|쓰기 처리량(네트워크)|초당 바이트 수|평균|게이트웨이의 모든 볼륨에 대 한 보고 기간의 장치에서 네트워크 인터페이스의 쓰기 처리량입니다.|InstanceName|
|TotalCapacity|예|총 용량|바이트|평균|총 용량|차원 없음|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|FailedRuns|예|실패한 실행|개수|합계||pipelineName, activityName|
|SuccessfulRuns|예|성공한 실행|개수|합계||pipelineName, activityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ActivityCancelledRuns|예|취소된 활동 실행 메트릭|개수|합계||ActivityType, PipelineName, FailureType, Name|
|ActivityFailedRuns|예|실패한 활동 실행 메트릭|개수|합계||ActivityType, PipelineName, FailureType, Name|
|ActivitySucceededRuns|예|성공한 활동 실행 메트릭|개수|합계||ActivityType, PipelineName, FailureType, Name|
|FactorySizeInGbUnits|예|총 팩터리 크기(GB 단위)|개수|최대||차원 없음|
|IntegrationRuntimeAvailableMemory|예|통합 런타임 사용 가능한 메모리|바이트|평균||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableNodeNumber|예|Integration runtime 사용 가능한 노드 수|개수|평균||IntegrationRuntimeName|
|IntegrationRuntimeAverageTaskPickupDelay|예|Integration runtime 큐 지속 시간|초|평균||IntegrationRuntimeName|
|IntegrationRuntimeCpuPercentage|예|통합 런타임 CPU 사용률|백분율|평균||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeQueueLength|예|Integration runtime 큐 길이|개수|평균||IntegrationRuntimeName|
|MaxAllowedFactorySizeInGbUnits|예|허용되는 최대 팩터리 크기(GB 단위)|개수|최대||차원 없음|
|MaxAllowedResourceCount|예|허용되는 최대 엔터티 수|개수|최대||차원 없음|
|PipelineCancelledRuns|예|취소된 파이프라인 실행 메트릭|개수|합계||FailureType, Name|
|PipelineFailedRuns|예|실패한 파이프라인 실행 메트릭|개수|합계||FailureType, Name|
|PipelineSucceededRuns|예|성공한 파이프라인 실행 메트릭|개수|합계||FailureType, Name|
|ResourceCount|예|총 엔터티 수|개수|최대||차원 없음|
|TriggerCancelledRuns|예|취소된 트리거 실행 메트릭|개수|합계||Name, FailureType|
|TriggerFailedRuns|예|실패한 트리거 실행 메트릭|개수|합계||Name, FailureType|
|TriggerSucceededRuns|예|성공한 트리거 실행 메트릭|개수|합계||Name, FailureType|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|DataRead|예|데이터 읽기|바이트|합계|계정에서 읽어 온 총 데이터 양.|차원 없음|
|DataWritten|예|기록된 데이터|바이트|합계|계정에 기록된 총 데이터 양.|차원 없음|
|ReadRequests|예|읽기 요청|개수|합계|계정에 데이터 읽기 요청 수.|차원 없음|
|TotalStorage|예|총 스토리지|바이트|최대|계정에 저장된 총 데이터 양.|차원 없음|
|WriteRequests|예|쓰기 요청|개수|합계|계정에 데이터 쓰기 요청 수.|차원 없음|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|active_connections|예|활성 연결 수|개수|평균|활성 연결 수|차원 없음|
|backup_storage_used|예|사용된 백업 스토리지|바이트|평균|사용된 백업 스토리지|차원 없음|
|connections_failed|예|실패한 연결|개수|합계|실패한 연결|차원 없음|
|cpu_percent|예|CPU 백분율|백분율|평균|CPU 백분율|차원 없음|
|io_consumption_percent|예|IO 백분율|백분율|평균|IO 백분율|차원 없음|
|memory_percent|예|메모리 백분율|백분율|평균|메모리 백분율|차원 없음|
|network_bytes_egress|예|네트워크 아웃|바이트|합계|활성 연결에서 네트워크 출력|차원 없음|
|network_bytes_ingress|예|네트워크 인|바이트|합계|활성 연결에서 네트워크 입력|차원 없음|
|seconds_behind_master|예|복제 지연 시간(초)|개수|최대|복제 지연 시간(초)|차원 없음|
|serverlog_storage_limit|예|서버 로그 스토리지 제한|바이트|평균|서버 로그 스토리지 제한|차원 없음|
|serverlog_storage_percent|예|서버 로그 스토리지 비율|백분율|평균|서버 로그 스토리지 비율|차원 없음|
|serverlog_storage_percent|예|사용된 서버 로그 스토리지|바이트|평균|사용된 서버 로그 스토리지|차원 없음|
|storage_limit|예|스토리지 제한|바이트|최대|스토리지 제한|차원 없음|
|storage_percent|예|스토리지 비율|백분율|평균|스토리지 비율|차원 없음|
|storage_used|예|스토리지 사용됨|바이트|평균|스토리지 사용됨|차원 없음|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|active_connections|예|활성 연결 수|개수|평균|활성 연결 수|차원 없음|
|backup_storage_used|예|사용된 백업 스토리지|바이트|평균|사용된 백업 스토리지|차원 없음|
|connections_failed|예|실패한 연결|개수|합계|실패한 연결|차원 없음|
|cpu_percent|예|CPU 백분율|백분율|평균|CPU 백분율|차원 없음|
|io_consumption_percent|예|IO 백분율|백분율|평균|IO 백분율|차원 없음|
|memory_percent|예|메모리 백분율|백분율|평균|메모리 백분율|차원 없음|
|network_bytes_egress|예|네트워크 아웃|바이트|합계|활성 연결에서 네트워크 출력|차원 없음|
|network_bytes_ingress|예|네트워크 인|바이트|합계|활성 연결에서 네트워크 입력|차원 없음|
|seconds_behind_master|예|복제 지연 시간(초)|개수|최대|복제 지연 시간(초)|차원 없음|
|serverlog_storage_limit|예|서버 로그 스토리지 제한|바이트|최대|서버 로그 스토리지 제한|차원 없음|
|serverlog_storage_percent|예|서버 로그 스토리지 비율|백분율|평균|서버 로그 스토리지 비율|차원 없음|
|serverlog_storage_percent|예|사용된 서버 로그 스토리지|바이트|평균|사용된 서버 로그 스토리지|차원 없음|
|storage_limit|예|스토리지 제한|바이트|최대|스토리지 제한|차원 없음|
|storage_percent|예|스토리지 비율|백분율|평균|스토리지 비율|차원 없음|
|storage_used|예|스토리지 사용됨|바이트|평균|스토리지 사용됨|차원 없음|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|active_connections|예|활성 연결 수|개수|평균|활성 연결 수|차원 없음|
|backup_storage_used|예|사용된 백업 스토리지|바이트|평균|사용된 백업 스토리지|차원 없음|
|connections_failed|예|실패한 연결|개수|합계|실패한 연결|차원 없음|
|cpu_percent|예|CPU 백분율|백분율|평균|CPU 백분율|차원 없음|
|io_consumption_percent|예|IO 백분율|백분율|평균|IO 백분율|차원 없음|
|memory_percent|예|메모리 백분율|백분율|평균|메모리 백분율|차원 없음|
|network_bytes_egress|예|네트워크 아웃|바이트|합계|활성 연결에서 네트워크 출력|차원 없음|
|network_bytes_ingress|예|네트워크 인|바이트|합계|활성 연결에서 네트워크 입력|차원 없음|
|pg_replica_log_delay_in_bytes|예|복제본 간 최대 지연 시간|바이트|최대|가장 지연 복제본의 지연 (바이트)|차원 없음|
|pg_replica_log_delay_in_seconds|예|복제본 지연 시간|초|최대|복제본 지연 (초)|차원 없음|
|serverlog_storage_limit|예|서버 로그 스토리지 제한|바이트|최대|서버 로그 스토리지 제한|차원 없음|
|serverlog_storage_percent|예|서버 로그 스토리지 비율|백분율|평균|서버 로그 스토리지 비율|차원 없음|
|serverlog_storage_percent|예|사용된 서버 로그 스토리지|바이트|평균|사용된 서버 로그 스토리지|차원 없음|
|storage_limit|예|스토리지 제한|바이트|최대|스토리지 제한|차원 없음|
|storage_percent|예|스토리지 비율|백분율|평균|스토리지 비율|차원 없음|
|storage_used|예|스토리지 사용됨|바이트|평균|스토리지 사용됨|차원 없음|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|active_connections|예|활성 연결 수|개수|평균|활성 연결 수|차원 없음|
|cpu_percent|예|CPU 백분율|백분율|평균|CPU 백분율|차원 없음|
|IOPS|예|IOPS|개수|평균|초당 IO 작업 수|차원 없음|
|memory_percent|예|메모리 백분율|백분율|평균|메모리 백분율|차원 없음|
|network_bytes_egress|예|네트워크 아웃|바이트|합계|활성 연결에서 네트워크 출력|차원 없음|
|network_bytes_ingress|예|네트워크 인|바이트|합계|활성 연결에서 네트워크 입력|차원 없음|
|storage_percent|예|스토리지 비율|백분율|평균|스토리지 비율|차원 없음|
|storage_used|예|스토리지 사용됨|바이트|평균|스토리지 사용됨|차원 없음|


## <a name="microsoftdbforpostgresqlsingleservers"></a>DBforPostgreSQL/singleservers

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|active_connections|예|활성 연결 수|개수|평균|활성 연결 수|차원 없음|
|connections_failed|예|실패한 연결|개수|합계|실패한 연결|차원 없음|
|connections_succeeded|예|성공한 연결|개수|합계|성공한 연결|차원 없음|
|cpu_percent|예|CPU 백분율|백분율|평균|CPU 백분율|차원 없음|
|IOPS|예|IOPS|개수|평균|초당 IO 작업 수|차원 없음|
|maximum_used_transactionIDs|예|사용 되는 최대 트랜잭션 Id|개수|평균|사용 되는 최대 트랜잭션 Id|차원 없음|
|memory_percent|예|메모리 백분율|백분율|평균|메모리 백분율|차원 없음|
|network_bytes_egress|예|네트워크 아웃|바이트|합계|활성 연결에서 네트워크 출력|차원 없음|
|network_bytes_ingress|예|네트워크 인|바이트|합계|활성 연결에서 네트워크 입력|차원 없음|
|storage_percent|예|스토리지 비율|백분율|평균|스토리지 비율|차원 없음|
|storage_used|예|스토리지 사용됨|바이트|평균|스토리지 사용됨|차원 없음|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|c2d.commands.egress.abandon.success|예|중단된 C2D 메시지|개수|합계|장치에서 중단 한 클라우드-장치 메시지 수|차원 없음|
|c2d.commands.egress.complete.success|예|C2D 메시지 배달 완료|개수|합계|장치에서 성공적으로 완료 한 클라우드-장치 메시지 배달 수|차원 없음|
|c2d.commands.egress.reject.success|예|거부된 C2D 메시지|개수|합계|장치에서 거부 한 클라우드-장치 메시지 수|차원 없음|
|c2d.methods.failure|예|실패한 직접 메서드 호출|개수|합계|실패한 모든 직접 메서드 호출의 수입니다.|차원 없음|
|c2d.methods.requestSize|예|직접 메서드 호출의 요청 크기|바이트|평균|성공한 모든 직접 메서드 요청의 평균, 최소값, 최대값입니다.|차원 없음|
|c2d.methods.responseSize|예|직접 메서드 호출의 응답 크기|바이트|평균|성공한 모든 직접 메서드 응답의 평균, 최소값, 최대값입니다.|차원 없음|
|c2d.methods.success|예|성공한 직접 메서드 호출|개수|합계|성공한 모든 직접 메서드 호출의 수입니다.|차원 없음|
|c2d.twin.read.failure|예|백 엔드에서의 실패한 쌍 읽기|개수|합계|실패한 모든 백 엔드 시작 쌍 읽기 수입니다.|차원 없음|
|c2d.twin.read.size|예|백 엔드에서의 쌍 읽기 응답 크기|바이트|평균|성공한 모든 백 엔드 시작 쌍 읽기 수의 평균, 최소값 및 최대값입니다.|차원 없음|
|c2d.twin.read.success|예|백 엔드에서의 성공한 쌍 읽기|개수|합계|성공한 모든 백 엔드 시작 쌍 읽기 수입니다.|차원 없음|
|c2d.twin.update.failure|예|백 엔드에서의 실패한 쌍 업데이트|개수|합계|실패한 모든 백 엔드 시작 쌍 업데이트 수입니다.|차원 없음|
|c2d.twin.update.size|예|백 엔드에서의 쌍 업데이트 크기|바이트|평균|성공한 모든 백 엔드 시작 쌍 업데이트 수의 평균, 최소 및 최대 크기입니다.|차원 없음|
|c2d.twin.update.success|예|백 엔드에서의 성공한 쌍 업데이트|개수|합계|성공한 모든 백 엔드 시작 쌍 업데이트 수입니다.|차원 없음|
|C2DMessagesExpired|예|만료된 C2D 메시지(미리 보기)|개수|합계|만료 된 클라우드-장치 메시지 수|차원 없음|
|구성|예|구성 메트릭|개수|합계|구성 작업에 대한 메트릭|차원 없음|
|connectedDeviceCount|아니요|연결된 디바이스(미리 보기)|개수|평균|IoT 허브에 연결된 디바이스 수|차원 없음|
|d2c.endpoints.egress.builtIn.events|예|라우팅: 메시지/이벤트에 배달된 메시지|개수|합계|IoT Hub 라우팅에서 기본 제공 엔드포인트(메시지/이벤트)에 메시지를 성공적으로 배달한 횟수입니다.|차원 없음|
|d2c.endpoints.egress.eventHubs|예|라우팅: 이벤트 허브에 배달된 메시지|개수|합계|IoT Hub 라우팅에서 이벤트 허브 엔드포인트에 메시지를 성공적으로 배달한 횟수입니다.|차원 없음|
|d2c.endpoints.egress.serviceBusQueues|예|라우팅: Service Bus 큐에 배달된 메시지|개수|합계|IoT Hub 라우팅에서 Service Bus 큐 엔드포인트에 메시지를 성공적으로 배달한 횟수입니다.|차원 없음|
|d2c.endpoints.egress.serviceBusTopics|예|라우팅: Service Bus 토픽에 배달된 메시지|개수|합계|IoT Hub 라우팅에서 Service Bus 토픽 엔드포인트에 메시지를 성공적으로 배달한 횟수입니다.|차원 없음|
|d2c.endpoints.egress.storage|예|라우팅: 스토리지에 배달된 메시지|개수|합계|IoT Hub 라우팅에서 스토리지 엔드포인트에 메시지를 성공적으로 배달한 횟수입니다.|차원 없음|
|d2c.endpoints.egress.storage.blobs|예|라우팅: 스토리지에 배달된 Blob|개수|합계|IoT Hub 라우팅에서 스토리지 엔드포인트에 Blob을 배달한 횟수입니다.|차원 없음|
|d2c.endpoints.egress.storage.bytes|예|라우팅: 스토리지에 배달된 데이터|바이트|합계|IoT Hub 라우팅에서 스토리지 엔드포인트에 배달된 데이터 양입니다(바이트).|차원 없음|
|d2c.endpoints.latency.builtIn.events|예|라우팅: 메시지/이벤트에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 기본 제공 엔드포인트(메시지.이벤트)에 대한 원격 분석 메시지 수신 간의 평균 대기 시간(밀리초)입니다.|차원 없음|
|d2c.endpoints.latency.eventHubs|예|라우팅: 이벤트 허브에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 이벤트 허브 엔드포인트에 대한 메시지 수신 간의 평균 대기 시간(밀리초)입니다.|차원 없음|
|d2c.endpoints.latency.serviceBusQueues|예|라우팅: Service Bus 큐에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 Service Bus 큐 엔드포인트에 대한 원격 분석 메시지 수신 간의 평균 대기 시간(밀리초)입니다.|차원 없음|
|d2c.endpoints.latency.serviceBusTopics|예|라우팅: Service Bus 토픽에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 Service Bus 토픽 엔드포인트에 대한 원격 분석 메시지 수신 간의 평균 대기 시간(밀리초)입니다.|차원 없음|
|d2c.endpoints.latency.storage|예|라우팅: 스토리지에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 스토리지 엔드포인트에 대한 원격 분석 메시지 수신 간의 평균 대기 시간(밀리초)입니다.|차원 없음|
|d2c.telemetry.egress.dropped|예|라우팅: 삭제된 원격 분석 메시지 |개수|합계|데드 엔드포인트로 인해 IoT Hub 라우팅에서 메시지가 삭제된 횟수입니다. 삭제된 메시지는 배달되지 않으므로 이 값은 대체 경로에 배달된 메시지를 계산에 넣지 않습니다.|차원 없음|
|d2c.telemetry.egress.fallback|예|라우팅: 대체에 배달된 메시지|개수|합계|IoT Hub 라우팅에서 대체 경로와 연결된 엔드포인트에 메시지를 배달한 횟수입니다.|차원 없음|
|d2c.telemetry.egress.invalid|예|라우팅: 원격 분석 메시지 호환되지 않음|개수|합계|IoT Hub 라우팅에서 엔드포인트와의 비호환성으로 인해 메시지를 배달하지 못한 횟수입니다. 이 값은 재시도를 포함하지 않습니다.|차원 없음|
|d2c.telemetry.egress.orphaned|예|라우팅: 분리된 원격 분석 메시지 |개수|합계|메시지가 라우팅 규칙(대체 규칙 포함)과 일치하지 않았으므로 IoT Hub 라우팅에 의해 분리된 횟수입니다. |차원 없음|
|d2c.telemetry.egress.success|예|라우팅: 배달된 원격 분석 메시지|개수|합계|IoT Hub 라우팅을 사용하여 모든 엔드포인트에 메시지가 성공적으로 배달된 횟수입니다. 메시지가 여러 엔드포인트로 라우팅되는 경우 이 값은 각 성공적인 배달에 대해 하나씩 증가합니다. 메시지가 동일한 엔드포인트로 여러 번 배달되는 경우 이 값은 각 성공적인 배달에 대해 하나씩 증가합니다.|차원 없음|
|d2c.telemetry.ingress.allProtocol|예|원격 분석 메시지 보내기 시도|개수|합계|IoT Hub로 보내려 한 디바이스-클라우드 원격 분석 메시지 수|차원 없음|
|d2c.telemetry.ingress.sendThrottle|예|제한 오류 수|개수|합계|디바이스 처리량 제한으로 인한 제한 오류 수|차원 없음|
|d2c.telemetry.ingress.success|예|보낸 원격 분석 메시지|개수|합계|IoT Hub로 보내기 성공한 디바이스-클라우드 원격 분석 메시지 수|차원 없음|
|d2c.twin.read.failure|예|디바이스에서의 실패한 쌍 읽기|개수|합계|실패한 모든 디바이스 시작 쌍 읽기 수입니다.|차원 없음|
|d2c.twin.read.size|예|디바이스에서의 쌍 읽기 응답 크기|바이트|평균|성공한 모든 디바이스 시작 쌍 읽기 수의 평균, 최소값 및 최대값입니다.|차원 없음|
|d2c.twin.read.success|예|디바이스에서의 성공한 쌍 읽기|개수|합계|성공한 모든 디바이스 시작 쌍 읽기 수입니다.|차원 없음|
|d2c.twin.update.failure|예|디바이스에서의 실패한 쌍 업데이트|개수|합계|실패한 모든 디바이스 시작 쌍 업데이트 수입니다.|차원 없음|
|d2c.twin.update.size|예|디바이스에서의 쌍 업데이트 크기|바이트|평균|성공한 모든 디바이스 시작 쌍 업데이트 수의 평균, 최소 및 최대 크기입니다.|차원 없음|
|d2c.twin.update.success|예|디바이스에서의 성공한 쌍 업데이트|개수|합계|성공한 모든 디바이스 시작 쌍 업데이트 수입니다.|차원 없음|
|dailyMessageQuotaUsed|예|사용된 전체 메시지 수|개수|최대|현재 사용되는 전체 메시지 수|차원 없음|
|deviceDataUsage|예|총 디바이스 데이터 사용량|바이트|합계|IotHub에 연결된 모든 디바이스에서 전송된 바이트|차원 없음|
|deviceDataUsageV2|예|총 디바이스 데이터 사용량(미리 보기)|바이트|합계|IotHub에 연결된 모든 디바이스에서 전송된 바이트|차원 없음|
|devices.connectedDevices.allProtocol|예|연결된 디바이스(사용되지 않음) |개수|합계|IoT 허브에 연결된 디바이스 수|차원 없음|
|devices.totalDevices|예|총 디바이스(사용되지 않음)|개수|합계|IoT 허브에 등록된 디바이스 수|차원 없음|
|EventGridDeliveries|예|Event Grid 배달 (미리 보기)|개수|합계|Event Grid에 게시 된 IoT Hub 이벤트 수입니다. 성공 및 실패 한 요청의 수에 대해 결과 차원을 사용 합니다. EventType dimension 이벤트 유형 ()을 표시 합니다 https://aka.ms/ioteventgrid) .|Result, EventType|
|EventGridLatency|예|Event Grid 대기 시간(미리 보기)|밀리초|평균|Event Grid에 이벤트가 게시 될 때 Iot Hub 이벤트가 생성 된 시간에 대 한 평균 대기 시간 (밀리초)입니다. 이 숫자는 모든 이벤트 유형 사이의 평균입니다. 특정 유형의 이벤트에 대 한 대기 시간을 확인 하려면 EventType 차원을 사용 합니다.|EventType|
|jobs.cancelJob.failure|예|실패한 작업 취소|개수|합계|작업 취소에 대한 실패한 모든 호출 수입니다.|차원 없음|
|jobs.cancelJob.success|예|성공한 작업 취소|개수|합계|작업 취소에 대한 성공한 모든 호출 수입니다.|차원 없음|
|jobs.completed|예|완료된 작업|개수|합계|완료된 모든 작업의 수입니다.|차원 없음|
|jobs.createDirectMethodJob.failure|예|실패한 메서드 호출 작업 만들기|개수|합계|직접 메서드 호출 작업에 대한 실패한 모든 만들기의 수입니다.|차원 없음|
|jobs.createDirectMethodJob.success|예|메서드 호출 작업에 대한 성공한 만들기|개수|합계|직접 메서드 호출 작업에 대한 성공한 모든 만들기의 수입니다.|차원 없음|
|jobs.createTwinUpdateJob.failure|예|쌍 업데이트 작업에 대한 실패한 만들기|개수|합계|쌍 업데이트 작업에 대한 실패한 모든 만들기의 수입니다.|차원 없음|
|jobs.createTwinUpdateJob.success|예|쌍 업데이트 작업에 대한 성공한 만들기|개수|합계|쌍 업데이트 작업에 대한 성공한 모든 만들기의 수입니다.|차원 없음|
|jobs.failed|예|실패한 작업|개수|합계|실패한 모든 작업의 수입니다.|차원 없음|
|jobs.listJobs.failure|예|목록 작업에 대한 실패한 호출|개수|합계|목록 작업에 대한 실패한 모든 호출 수입니다.|차원 없음|
|jobs.listJobs.success|예|목록 작업에 대한 성공한 호출|개수|합계|목록 작업에 대한 성공한 모든 호출 수입니다.|차원 없음|
|jobs.queryJobs.failure|예|실패한 작업 쿼리|개수|합계|쿼리 작업에 대한 실패한 모든 호출 수입니다.|차원 없음|
|jobs.queryJobs.success|예|성공한 작업 쿼리|개수|합계|쿼리 작업에 대한 성공한 모든 호출 수입니다.|차원 없음|
|RoutingDataSizeInBytesDelivered|예|라우팅 배달 메시지 크기 (바이트) (미리 보기)|바이트|합계|IoT hub에서 끝점으로 배달 된 메시지의 총 크기 (바이트)입니다. EndpointName 및 EndpointType 차원을 사용 하 여 다른 끝점에 배달 된 메시지의 크기 (바이트)를 볼 수 있습니다. 메시지를 여러 끝점으로 배달 하는 경우 또는 메시지가 동일한 끝점으로 여러 번 전달 되는 경우를 포함 하 여 전달 된 모든 메시지에 대해 메트릭 값이 늘어납니다.|EndpointType, EndpointName, RoutingSource|
|RoutingDeliveries|예|라우팅 배달 (미리 보기)|개수|합계|라우팅을 사용 하 여 모든 끝점에 메시지를 배달 하려고 시도 IoT Hub 횟수입니다. 성공 또는 실패 횟수를 확인 하려면 결과 차원을 사용 합니다. 잘못 됨, 삭제 됨 또는 분리 됨과 같은 실패의 원인을 확인 하려면 FailureReasonCategory 차원을 사용 합니다. EndpointName 및 EndpointType 차원을 사용 하 여 여러 끝점에 배달 된 메시지 수를 이해할 수도 있습니다. 메시지를 여러 끝점으로 배달 하거나 메시지가 동일한 끝점으로 여러 번 배달 되는 경우를 포함 하 여 각 배달 시도에 대해 메트릭 값이 하나씩 늘어납니다.|EndpointType, EndpointName, FailureReasonCategory, Result, RoutingSource|
|RoutingDeliveryLatency|예|라우팅 배달 대기 시간 (미리 보기)|밀리초|평균|IoT Hub에 대 한 메시지 수신 간의 평균 대기 시간 (밀리초) 및 끝점에 대 한 원격 분석 메시지 수신입니다. EndpointName 및 EndpointType 차원을 사용 하 여 다른 끝점에 대 한 대기 시간을 파악할 수 있습니다.|EndpointType, EndpointName, RoutingSource|
|totalDeviceCount|아니요|총 디바이스(미리 보기)|개수|평균|IoT 허브에 등록된 디바이스 수|차원 없음|
|twinQueries.failure|예|실패한 쌍 쿼리|개수|합계|실패한 모든 쌍 쿼리의 수입니다.|차원 없음|
|twinQueries.resultSize|예|쌍 쿼리 결과 크기|바이트|평균|성공한 모든 쌍 쿼리 결과 크기의 평균, 최소값 및 최대값입니다.|차원 없음|
|twinQueries.success|예|성공한 쌍 쿼리|개수|합계|성공한 모든 쌍 쿼리의 수입니다.|차원 없음|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|AttestationAttempts|예|증명 시도|개수|합계|디바이스 증명 시도 수|ProvisioningServiceName, 상태, 프로토콜|
|DeviceAssignments|예|할당된 디바이스|개수|합계|IoT Hub에 할당된 디바이스 수|ProvisioningServiceName, IotHubName|
|RegistrationAttempts|예|등록 시도|개수|합계|디바이스 등록 시도 수|ProvisioningServiceName, IotHubName, 상태|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|AddRegion|예|추가 된 지역|개수|개수|추가 된 지역|Azure 지역|
|AutoscaleMaxThroughput|아니요|자동 크기 조정 최대 처리량|개수|최대|자동 크기 조정 최대 처리량|DatabaseName, CollectionName|
|AvailableStorage|아니요|mapi 사용 가능한 저장소|바이트|합계|9 월 2020 일 말에 Azure Monitor에서 "사용 가능한 저장소"가 제거 됩니다. Cosmos DB 컬렉션 저장소 크기는 이제 제한 되지 않습니다. 유일한 제한 사항은 각 논리 파티션 키의 저장소 크기가 20GB는 것입니다. 진단 로그에서 분할 키 통계를 사용 하도록 설정 하 여 상위 파티션 키에 대 한 저장소 사용량을 파악할 수 있습니다. Cosmos DB 저장소 할당량에 대 한 자세한 내용은이 문서를 확인 하세요 https://docs.microsoft.com/azure/cosmos-db/concepts-limits . 사용 중단 후 더 이상 사용 되지 않는 메트릭에 아직 정의 된 나머지 경고 규칙은 사용 중단 날짜 후에도 자동으로 비활성화 됩니다.|CollectionName, DatabaseName, Region|
|CassandraConnectionClosures|아니요|Cassandra 연결 차단|개수|합계|1 분 세분성으로 보고 된 Cassandra 연결의 수입니다.|지역, ClosureReason|
|CassandraConnectorAvgReplicationLatency|아니요|Cassandra 커넥터 평균 ReplicationLatency|밀리초|평균|Cassandra 커넥터 평균 ReplicationLatency|차원 없음|
|CassandraConnectorReplicationHealthStatus|아니요|Cassandra 커넥터 복제 상태|개수|개수|Cassandra 커넥터 복제 상태|NotStarted, ReplicationInProgress, 오류|
|CassandraKeyspaceCreate|아니요|만든 Cassandra Keyspace|개수|개수|만든 Cassandra Keyspace|ResourceName |
|CassandraKeyspaceDelete|아니요|Cassandra Keyspace Deleted|개수|개수|Cassandra Keyspace Deleted|ResourceName |
|CassandraKeyspaceThroughputUpdate|아니요|Cassandra Keyspace 처리량 업데이트 됨|개수|개수|Cassandra Keyspace 처리량 업데이트 됨|ResourceName |
|CassandraKeyspaceUpdate|아니요|Cassandra Keyspace 업데이트 됨|개수|개수|Cassandra Keyspace 업데이트 됨|ResourceName |
|CassandraRequestCharges|아니요|Cassandra 요청 요금|개수|합계|Cassandra 요청에 사용 된 RUs|DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|아니요|Cassandra 요청|개수|개수|Cassandra 요청 수|DatabaseName, CollectionName, Region, OperationType, ResourceType, ErrorCode|
|CassandraTableCreate|아니요|Cassandra 테이블을 만들었습니다.|개수|개수|Cassandra 테이블을 만들었습니다.|Context.resourcename, ChildResourceName, |
|CassandraTableDelete|아니요|Cassandra 테이블 삭제 됨|개수|개수|Cassandra 테이블 삭제 됨|Context.resourcename, ChildResourceName, |
|CassandraTableThroughputUpdate|아니요|Cassandra 테이블 처리량이 업데이트 됨|개수|개수|Cassandra 테이블 처리량이 업데이트 됨|Context.resourcename, ChildResourceName, |
|CassandraTableUpdate|아니요|Cassandra 테이블 업데이트 됨|개수|개수|Cassandra 테이블 업데이트 됨|Context.resourcename, ChildResourceName, |
|CreateAccount|예|계정이 만들어짐|개수|개수|계정이 만들어짐|차원 없음|
|DataUsage|아니요|데이터 사용량|바이트|합계|5 분 세분성으로 보고 된 총 데이터 사용량|CollectionName, DatabaseName, Region|
|DeleteAccount|예|계정이 삭제 됨|개수|개수|계정이 삭제 됨|차원 없음|
|DocumentCount|아니요|문서 수|개수|합계|5 분 세분성으로 보고 된 총 문서 수|CollectionName, DatabaseName, Region|
|DocumentQuota|아니요|문서 할당량|바이트|합계|5 분 세분성에 보고 된 총 저장소 할당량|CollectionName, DatabaseName, Region|
|GremlinDatabaseCreate|아니요|Gremlin 데이터베이스를 만들었습니다.|개수|개수|Gremlin 데이터베이스를 만들었습니다.|ResourceName |
|GremlinDatabaseDelete|아니요|Gremlin 데이터베이스 삭제 됨|개수|개수|Gremlin 데이터베이스 삭제 됨|ResourceName |
|GremlinDatabaseThroughputUpdate|아니요|Gremlin 데이터베이스 처리량이 업데이트 됨|개수|개수|Gremlin 데이터베이스 처리량이 업데이트 됨|ResourceName |
|GremlinDatabaseUpdate|아니요|Gremlin 데이터베이스 업데이트 됨|개수|개수|Gremlin 데이터베이스 업데이트 됨|ResourceName |
|GremlinGraphCreate|아니요|Gremlin 그래프가 만들어짐|개수|개수|Gremlin 그래프가 만들어짐|Context.resourcename, ChildResourceName, |
|GremlinGraphDelete|아니요|Gremlin 그래프가 삭제 됨|개수|개수|Gremlin 그래프가 삭제 됨|Context.resourcename, ChildResourceName, |
|GremlinGraphThroughputUpdate|아니요|Gremlin Graph 처리량이 업데이트 됨|개수|개수|Gremlin Graph 처리량이 업데이트 됨|Context.resourcename, ChildResourceName, |
|GremlinGraphUpdate|아니요|Gremlin 그래프가 업데이트 됨|개수|개수|Gremlin 그래프가 업데이트 됨|Context.resourcename, ChildResourceName, |
|IndexUsage|아니요|인덱스 사용량|바이트|합계|5 분 세분성에 보고 된 총 인덱스 사용|CollectionName, DatabaseName, Region|
|MetadataRequests|아니요|메타데이터 요청|개수|개수|메타데이터 요청 수. Cosmos DB는 컬렉션, 데이터베이스 등과 해당 구성을 무료로 열거할 수 있는 각 계정에 대한 시스템 메타데이터 컬렉션을 유지 관리합니다.|DatabaseName, CollectionName, Region, StatusCode, |
|MongoCollectionCreate|아니요|Mongo 수집을 만듦|개수|개수|Mongo 수집을 만듦|Context.resourcename, ChildResourceName, |
|MongoCollectionDelete|아니요|Mongo 컬렉션이 삭제 됨|개수|개수|Mongo 컬렉션이 삭제 됨|Context.resourcename, ChildResourceName, |
|MongoCollectionThroughputUpdate|아니요|Mongo 수집 처리량이 업데이트 됨|개수|개수|Mongo 수집 처리량이 업데이트 됨|Context.resourcename, ChildResourceName, |
|MongoCollectionUpdate|아니요|Mongo 수집 업데이트 됨|개수|개수|Mongo 수집 업데이트 됨|Context.resourcename, ChildResourceName, |
|MongoDatabaseDelete|아니요|Mongo 데이터베이스가 삭제 됨|개수|개수|Mongo 데이터베이스가 삭제 됨|ResourceName |
|MongoDatabaseThroughputUpdate|아니요|Mongo 데이터베이스 처리량이 업데이트 됨|개수|개수|Mongo 데이터베이스 처리량이 업데이트 됨|ResourceName |
|MongoDBDatabaseCreate|아니요|Mongo 데이터베이스를 만들었습니다.|개수|개수|Mongo 데이터베이스를 만들었습니다.|ResourceName |
|MongoDBDatabaseUpdate|아니요|Mongo 데이터베이스 업데이트 됨|개수|개수|Mongo 데이터베이스 업데이트 됨|ResourceName |
|MongoRequestCharge|예|Mongo 요청 요금|개수|합계|사용된 Mongo 요청 단위|DatabaseName, CollectionName, Region, CommandName, ErrorCode, Status|
|MongoRequests|예|Mongo 요청|개수|개수|생성된 Mongo 요청 수|DatabaseName, CollectionName, Region, CommandName, ErrorCode, Status|
|MongoRequestsCount|아니요|Mongo 요청 속도|초당 개수|평균|초당 mongo 요청 수|DatabaseName, CollectionName, Region, ErrorCode|
|MongoRequestsDelete|아니요|Mongo 삭제 요청 속도|초당 개수|평균|초당 mongo 삭제 요청 수|DatabaseName, CollectionName, Region, ErrorCode|
|MongoRequestsInsert|아니요|Mongo 삽입 요청 속도|초당 개수|평균|초당 mongo 삽입 수|DatabaseName, CollectionName, Region, ErrorCode|
|MongoRequestsQuery|아니요|Mongo 쿼리 요청 속도|초당 개수|평균|Mongo 쿼리 요청 수/초|DatabaseName, CollectionName, Region, ErrorCode|
|MongoRequestsUpdate|아니요|Mongo 업데이트 요청 속도|초당 개수|평균|Mongo 업데이트 요청 수/초|DatabaseName, CollectionName, Region, ErrorCode|
|NormalizedRUConsumption|아니요|정규화 된 과도 소비|백분율|최대|분당 최대 r 소비 비율|CollectionName, DatabaseName, Region, PartitionKeyRangeId|
|ProvisionedThroughput|아니요|프로비전된 처리량|개수|최대|프로비전된 처리량|DatabaseName, CollectionName|
|지역 장애 조치 (failover)|예|지역 장애 조치|개수|개수|지역 장애 조치|차원 없음|
|RemoveRegion|예|지역이 제거 됨|개수|개수|지역이 제거 됨|Azure 지역|
|ReplicationLatency|예|P99 복제 대기 시간|밀리초|평균|지역 사용 계정에 대한 원본 및 대상 지역의 P99 복제 대기 시간|SourceRegion, TargetRegion|
|ServerSideLatency|아니요|서버 쪽 대기 시간|밀리초|평균|서버 쪽 대기 시간|DatabaseName, CollectionName, Region, ConnectionMode, OperationType, PublicAPIType|
|ServiceAvailability|아니요|서비스 가용성|백분율|평균|1 시간, 일 또는 월 세분성의 계정 요청 가용성|차원 없음|
|SqlContainerCreate|아니요|만든 Sql 컨테이너|개수|개수|만든 Sql 컨테이너|Context.resourcename, ChildResourceName, |
|SqlContainerDelete|아니요|삭제 된 Sql 컨테이너|개수|개수|삭제 된 Sql 컨테이너|Context.resourcename, ChildResourceName, |
|SqlContainerThroughputUpdate|아니요|Sql 컨테이너 처리량 업데이트|개수|개수|Sql 컨테이너 처리량 업데이트|Context.resourcename, ChildResourceName, |
|SqlContainerUpdate|아니요|업데이트 된 Sql 컨테이너|개수|개수|업데이트 된 Sql 컨테이너|Context.resourcename, ChildResourceName, |
|SqlDatabaseCreate|아니요|만든 Sql 데이터베이스|개수|개수|만든 Sql 데이터베이스|ResourceName |
|SqlDatabaseDelete|아니요|Sql 데이터베이스 삭제 됨|개수|개수|Sql 데이터베이스 삭제 됨|ResourceName |
|SqlDatabaseThroughputUpdate|아니요|Sql Database 처리량 업데이트|개수|개수|Sql Database 처리량 업데이트|ResourceName |
|SqlDatabaseUpdate|아니요|업데이트 된 Sql 데이터베이스|개수|개수|업데이트 된 Sql 데이터베이스|ResourceName |
|TableTableCreate|아니요|AzureTable 테이블을 만들었습니다.|개수|개수|AzureTable 테이블을 만들었습니다.|ResourceName |
|TableTableDelete|아니요|AzureTable 테이블 삭제 됨|개수|개수|AzureTable 테이블 삭제 됨|ResourceName |
|TableTableThroughputUpdate|아니요|AzureTable 테이블 처리량이 업데이트 됨|개수|개수|AzureTable 테이블 처리량이 업데이트 됨|ResourceName |
|TableTableUpdate|아니요|AzureTable 테이블 업데이트 됨|개수|개수|AzureTable 테이블 업데이트 됨|ResourceName |
|TotalRequests|예|총 요청 수|개수|개수|요청 수|DatabaseName, CollectionName, Region, StatusCode, OperationType, Status|
|TotalRequestUnits|예|총 요청 단위|개수|합계|사용된 요청 단위|DatabaseName, CollectionName, Region, StatusCode, OperationType, Status|
|UpdateAccountKeys|예|계정 키가 업데이트 됨|개수|개수|계정 키가 업데이트 됨|KeyType|
|UpdateAccountNetworkSettings|예|계정 네트워크 설정 업데이트 됨|개수|개수|계정 네트워크 설정 업데이트 됨|차원 없음|
|UpdateAccountReplicationSettings|예|계정 복제 설정 업데이트 됨|개수|개수|계정 복제 설정 업데이트 됨|차원 없음|
|UpdateDiagnosticsSettings|아니요|계정 진단 설정 업데이트 됨|개수|개수|계정 진단 설정 업데이트 됨|DiagnosticSettingsName, ResourceGroupName|


## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|DeadLetteredCount|예|배달 못한 편지 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 배달 못한 이벤트|토픽, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|
|DeliveryAttemptFailCount|아니요|배달 실패 이벤트|개수|합계|이 이벤트 구독에 배달하지 못한 총 이벤트|토픽, EventSubscriptionName, DomainEventSubscriptionName, Error, ErrorType|
|DeliverySuccessCount|예|배달된 이벤트|개수|합계|이 이벤트 구독에 배달된 총 이벤트|토픽, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|아니요|대상 처리 기간|밀리초|평균|대상 처리 기간(밀리초)|토픽, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|예|삭제된 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 삭제된 이벤트|토픽, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|MatchedEventCount|예|일치하는 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 이벤트|토픽, EventSubscriptionName, DomainEventSubscriptionName|
|PublishFailCount|예|실패한 이벤트 게시|개수|합계|이 토픽에 게시하지 못한 총 이벤트|토픽, ErrorType, 오류|
|PublishSuccessCount|예|게시된 이벤트|개수|합계|이 토픽에 게시된 총 이벤트|항목|
|PublishSuccessLatencyInMs|예|게시 성공 대기 시간|밀리초|합계|게시 성공 대기 시간 (밀리초)|차원 없음|


## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|DeadLetteredCount|예|배달 못한 편지 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 배달 못한 이벤트|DeadLetterReason|
|DeliveryAttemptFailCount|아니요|배달 실패 이벤트|개수|합계|이 이벤트 구독에 배달하지 못한 총 이벤트|Error, ErrorType|
|DeliverySuccessCount|예|배달된 이벤트|개수|합계|이 이벤트 구독에 배달된 총 이벤트|차원 없음|
|DestinationProcessingDurationInMs|아니요|대상 처리 기간|밀리초|평균|대상 처리 기간(밀리초)|차원 없음|
|DroppedEventCount|예|삭제된 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 삭제된 이벤트|DropReason|
|MatchedEventCount|예|일치하는 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 이벤트|차원 없음|


## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|PublishFailCount|예|실패한 이벤트 게시|개수|합계|이 토픽에 게시하지 못한 총 이벤트|ErrorType, Error|
|PublishSuccessCount|예|게시된 이벤트|개수|합계|이 토픽에 게시된 총 이벤트|차원 없음|
|PublishSuccessLatencyInMs|예|게시 성공 대기 시간|밀리초|합계|게시 성공 대기 시간 (밀리초)|차원 없음|
|UnmatchedEventCount|예|일치하지 않는 이벤트|개수|합계|이 토픽에 대한 이벤트 구독과 일치하지 않는 총 이벤트|차원 없음|


## <a name="microsofteventgridsystemtopics"></a>Microsoft EventGrid/systemTopics

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|DeadLetteredCount|예|배달 못한 편지 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 배달 못한 이벤트|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|아니요|배달 실패 이벤트|개수|합계|이 이벤트 구독에 배달하지 못한 총 이벤트|오류, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|예|배달된 이벤트|개수|합계|이 이벤트 구독에 배달된 총 이벤트|EventSubscriptionName|
|DestinationProcessingDurationInMs|아니요|대상 처리 기간|밀리초|평균|대상 처리 기간(밀리초)|EventSubscriptionName|
|DroppedEventCount|예|삭제된 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 삭제된 이벤트|DropReason, EventSubscriptionName|
|MatchedEventCount|예|일치하는 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 이벤트|EventSubscriptionName|
|PublishFailCount|예|실패한 이벤트 게시|개수|합계|이 토픽에 게시하지 못한 총 이벤트|ErrorType, Error|
|PublishSuccessCount|예|게시된 이벤트|개수|합계|이 토픽에 게시된 총 이벤트|차원 없음|
|PublishSuccessLatencyInMs|예|게시 성공 대기 시간|밀리초|합계|게시 성공 대기 시간 (밀리초)|차원 없음|
|UnmatchedEventCount|예|일치하지 않는 이벤트|개수|합계|이 토픽에 대한 이벤트 구독과 일치하지 않는 총 이벤트|차원 없음|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|DeadLetteredCount|예|배달 못한 편지 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 배달 못한 이벤트|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|아니요|배달 실패 이벤트|개수|합계|이 이벤트 구독에 배달하지 못한 총 이벤트|오류, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|예|배달된 이벤트|개수|합계|이 이벤트 구독에 배달된 총 이벤트|EventSubscriptionName|
|DestinationProcessingDurationInMs|아니요|대상 처리 기간|밀리초|평균|대상 처리 기간(밀리초)|EventSubscriptionName|
|DroppedEventCount|예|삭제된 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 삭제된 이벤트|DropReason, EventSubscriptionName|
|MatchedEventCount|예|일치하는 이벤트|개수|합계|이 이벤트 구독에 일치하는 총 이벤트|EventSubscriptionName|
|PublishFailCount|예|실패한 이벤트 게시|개수|합계|이 토픽에 게시하지 못한 총 이벤트|ErrorType, Error|
|PublishSuccessCount|예|게시된 이벤트|개수|합계|이 토픽에 게시된 총 이벤트|차원 없음|
|PublishSuccessLatencyInMs|예|게시 성공 대기 시간|밀리초|합계|게시 성공 대기 시간 (밀리초)|차원 없음|
|UnmatchedEventCount|예|일치하지 않는 이벤트|개수|합계|이 토픽에 대한 이벤트 구독과 일치하지 않는 총 이벤트|차원 없음|


## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ActiveConnections|아니요|ActiveConnections|개수|평균|Microsoft.EventHub에 대한 총 활성 연결.|차원 없음|
|AvailableMemory|아니요|사용 가능한 메모리|백분율|최대|총 메모리의 백분율로 나타낸 이벤트 허브 클러스터에 사용할 수 있는 메모리입니다.|역할|
|CaptureBacklog|아니요|캡처 백로그.|개수|합계|Microsoft.EventHub에 대한 캡처 백로그.|차원 없음|
|CapturedBytes|아니요|캡처된 바이트.|바이트|합계|Microsoft.EventHub에 대한 캡처된 바이트.|차원 없음|
|CapturedMessages|아니요|캡처된 메시지.|개수|합계|Microsoft.EventHub에 대한 캡처된 메시지.|차원 없음|
|ConnectionsClosed|아니요|끊어진 연결.|개수|평균|Microsoft.EventHub에 대한 끊어진 연결.|차원 없음|
|ConnectionsOpened|아니요|열린 연결.|개수|평균|Microsoft.EventHub에 대한 열린 연결.|차원 없음|
|CPU|아니요|CPU|백분율|최대|이벤트 허브 클러스터에 대한 CPU 사용률(백분율)|역할|
|IncomingBytes|예|들어오는 바이트|바이트|합계|Microsoft.EventHub에 대한 들어오는 바이트.|차원 없음|
|IncomingMessages|예|들어오는 메시지|개수|합계|Microsoft.EventHub에 대한 들어오는 메시지.|차원 없음|
|IncomingRequests|예|들어오는 요청|개수|합계|Microsoft.EventHub에 대한 들어오는 요청.|차원 없음|
|OutgoingBytes|예|보내는 바이트|바이트|합계|Microsoft.EventHub에 대한 보내는 바이트.|차원 없음|
|OutgoingMessages|예|보내는 메시지|개수|합계|Microsoft.EventHub에 대한 보내는 메시지.|차원 없음|
|QuotaExceededErrors|아니요|할당량 초과 오류.|개수|합계|Microsoft.EventHub에 대한 할당량 초과 오류.|차원 없음|
|ServerErrors|아니요|서버 오류.|개수|합계|Microsoft.EventHub에 대한 서버 오류.|차원 없음|
|크기|아니요|크기|바이트|평균|EventHub 크기(바이트)|역할|
|SuccessfulRequests|아니요|성공한 요청|개수|합계|Microsoft.EventHub에 대한 성공한 요청.|차원 없음|
|ThrottledRequests|아니요|제한된 요청.|개수|합계|Microsoft.EventHub에 대한 제한된 요청.|차원 없음|
|UserErrors|아니요|사용자 오류.|개수|합계|Microsoft.EventHub에 대한 사용자 오류.|차원 없음|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ActiveConnections|아니요|ActiveConnections|개수|평균|Microsoft.EventHub에 대한 총 활성 연결.|차원 없음|
|CaptureBacklog|아니요|캡처 백로그.|개수|합계|Microsoft.EventHub에 대한 캡처 백로그.|EntityName|
|CapturedBytes|아니요|캡처된 바이트.|바이트|합계|Microsoft.EventHub에 대한 캡처된 바이트.|EntityName|
|CapturedMessages|아니요|캡처된 메시지.|개수|합계|Microsoft.EventHub에 대한 캡처된 메시지.|EntityName|
|ConnectionsClosed|아니요|끊어진 연결.|개수|평균|Microsoft.EventHub에 대한 끊어진 연결.|EntityName|
|ConnectionsOpened|아니요|열린 연결.|개수|평균|Microsoft.EventHub에 대한 열린 연결.|EntityName|
|EHABL|예|백로그 메시지 보관(사용되지 않음)|개수|합계|네임 스페이스에 대 한 백로그의 이벤트 허브 보관 메시지 (사용 되지 않음)|차원 없음|
|EHAMBS|예|메시지 보관 처리량(사용되지 않음)|바이트|합계|네임 스페이스에서 이벤트 허브 보관 된 메시지 처리량 (사용 되지 않음)|차원 없음|
|EHAMSGS|예|메시지 보관(사용되지 않음)|개수|합계|네임 스페이스에서 이벤트 허브 보관 된 메시지 (사용 되지 않음)|차원 없음|
|EHINBYTES|예|들어오는 바이트(사용되지 않음)|바이트|합계|네임 스페이스에 대 한 이벤트 허브 들어오는 메시지 처리량 (사용 되지 않음)|차원 없음|
|EHINMBS|예|들어오는 바이트(구식)(사용되지 않음)|바이트|합계|네임스페이스에 들어오는 Event Hub 메시지 처리량 이 메트릭은 사용되지 않습니다. 대신 들어오는 바이트 메트릭을 사용 하세요 (사용 되지 않음).|차원 없음|
|EHINMSGS|예|들어오는 메시지(사용되지 않음)|개수|합계|네임 스페이스에 대 한 총 수신 메시지 (사용 되지 않음)|차원 없음|
|EHOUTBYTES|예|보내는 바이트(사용되지 않음)|바이트|합계|네임 스페이스에 대 한 이벤트 허브 나가는 메시지 처리량 (사용 되지 않음)|차원 없음|
|EHOUTMBS|예|나가는 바이트(구식)(사용되지 않음)|바이트|합계|네임스페이스에 보내는 Event Hub 메시지 처리량 이 메트릭은 사용되지 않습니다. 대신 나가는 바이트 메트릭을 사용 하십시오 (사용 되지 않음).|차원 없음|
|EHOUTMSGS|예|보내는 메시지(사용되지 않음)|개수|합계|네임 스페이스에 대 한 총 보내는 메시지 (사용 되지 않음)|차원 없음|
|FAILREQ|예|실패한 요청(사용되지 않음)|개수|합계|네임 스페이스에 대해 실패 한 총 요청 (사용 되지 않음)|차원 없음|
|IncomingBytes|예|들어오는 바이트|바이트|합계|Microsoft.EventHub에 대한 들어오는 바이트.|EntityName|
|IncomingMessages|예|들어오는 메시지|개수|합계|Microsoft.EventHub에 대한 들어오는 메시지.|EntityName|
|IncomingRequests|예|들어오는 요청|개수|합계|Microsoft.EventHub에 대한 들어오는 요청.|EntityName|
|INMSGS|예|들어오는 메시지(구식)(사용되지 않음)|개수|합계|네임스페이스에 들어오는 총 메시지 수 이 메트릭은 사용되지 않습니다. 대신 들어오는 메시지 메트릭을 사용 하세요 (사용 되지 않음).|차원 없음|
|INREQS|예|들어오는 요청(사용되지 않음)|개수|합계|네임 스페이스에 대 한 총 들어오는 전송 요청 (사용 되지 않음)|차원 없음|
|INTERR|예|내부 서버 오류(사용되지 않음)|개수|합계|네임 스페이스에 대 한 총 내부 서버 오류 (사용 되지 않음)|차원 없음|
|MISCERR|예|기타 오류(사용 되지 않음)|개수|합계|네임 스페이스에 대해 실패 한 총 요청 (사용 되지 않음)|차원 없음|
|OutgoingBytes|예|보내는 바이트|바이트|합계|Microsoft.EventHub에 대한 보내는 바이트.|EntityName|
|OutgoingMessages|예|보내는 메시지|개수|합계|Microsoft.EventHub에 대한 보내는 메시지.|EntityName|
|OUTMSGS|예|나가는 메시지(구식)(사용되지 않음)|개수|합계|네임스페이스에 보내는 총 메시지 수 이 메트릭은 사용되지 않습니다. 대신 보내는 메시지 메트릭을 사용 하세요 (사용 되지 않음).|차원 없음|
|QuotaExceededErrors|아니요|할당량 초과 오류.|개수|합계|Microsoft.EventHub에 대한 할당량 초과 오류.|EntityName, |
|ServerErrors|아니요|서버 오류.|개수|합계|Microsoft.EventHub에 대한 서버 오류.|EntityName, |
|크기|아니요|크기|바이트|평균|EventHub 크기(바이트)|EntityName|
|SuccessfulRequests|아니요|성공한 요청|개수|합계|Microsoft.EventHub에 대한 성공한 요청.|EntityName, |
|SUCCREQ|예|성공한 요청(사용되지 않음)|개수|합계|네임 스페이스에 대 한 총 성공한 요청 (사용 되지 않음)|차원 없음|
|SVRBSY|예|서버 작업 중 오류(사용되지 않음)|개수|합계|네임 스페이스에 대 한 총 서버 사용 중 오류 (사용 되지 않음)|차원 없음|
|ThrottledRequests|아니요|제한된 요청.|개수|합계|Microsoft.EventHub에 대한 제한된 요청.|EntityName, |
|UserErrors|아니요|사용자 오류.|개수|합계|Microsoft.EventHub에 대한 사용자 오류.|EntityName, |


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|CategorizedGatewayRequests|예|분류된 게이트웨이 요청|개수|합계|범주별 게이트웨이 요청 수(1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|GatewayRequests|예|게이트웨이 요청|개수|합계|게이트웨이 요청 수|HttpStatus|
|NumActiveWorkers|예|활성 작업자 수|개수|최대|활성 작업자 수|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|MetricThreshold|예|메트릭 임계값|개수|평균|자동 크기 조정이 실행되었을 때 구성된 자동 크기 조정 임계값입니다.|MetricTriggerRule|
|ObservedCapacity|예|관찰된 용량|개수|평균|실행될 때 자동 크기 조정을 위해 보고된 용량입니다.|차원 없음|
|ObservedMetricValue|예|관찰된 메트릭 값|개수|평균|실행될 때 자동 크기 조정에서 계산된 값|MetricTriggerSource|
|ScaleActionsInitiated|예|시작된 크기 조정 작업|개수|합계|크기 조정 작업의 방향입니다.|ScaleDirection|


## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|예|가용성|백분율|평균|성공적으로 완료 된 가용성 테스트 비율|availabilityResult/name, availabilityResult/location|
|availabilityResults/count|아니요|가용성 테스트|개수|개수|가용성 테스트 수|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|availabilityResults/duration|예|가용성 테스트 기간|밀리초|평균|가용성 테스트 기간|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|browserTimings/networkDuration|예|페이지 로드 네트워크 연결 시간|밀리초|평균|사용자 요청과 네트워크 연결 사이의 시간입니다. DNS 조회 및 전송 연결을 포함합니다.|차원 없음|
|browserTimings/processingDuration|예|클라이언트 처리 시간|밀리초|평균|DOM을 로드할 때부터 문서의 마지막 바이트를 받는 사이의 시간입니다. 비동기 요청은 계속 처리 중일 수 있습니다.|차원 없음|
|browserTimings/receiveDuration|예|응답 수신 시간|밀리초|평균|첫 번째 및 마지막 바이트 사이 또는 연결 끊기까지의 시간입니다.|차원 없음|
|browserTimings/sendDuration|예|요청 전송 시간|밀리초|평균|네트워크 연결과 첫 번째 바이트 받기 사이의 시간입니다.|차원 없음|
|browserTimings/totalDuration|예|브라우저 페이지 로드 시간|밀리초|평균|사용자가 요청한 때부터 DOM, 스타일시트, 스크립트 및 이미지가 로드될 때까지 소요된 시간입니다.|차원 없음|
|dependencies/count|아니요|종속성 호출|개수|개수|외부 리소스에 대해 애플리케이션이 만든 호출 수입니다.|종속성/유형, 종속성/performanceBucket, 종속성/성공, 종속성/대상, 종속성/resultCode, 작업/가상, 클라우드/roleInstance, 클라우드/roleName|
|dependencies/duration|예|종속성 기간|밀리초|평균|외부 리소스에 대한 서버 애플리케이션의 호출 기간입니다.|종속성/유형, 종속성/performanceBucket, 종속성/성공, 종속성/대상, 종속성/resultCode, 작업/가상, 클라우드/roleInstance, 클라우드/roleName|
|dependencies/failed|아니요|종속성 호출 실패|개수|개수|외부 리소스에 대해 애플리케이션이 만든 실패한 종속성 호출 수입니다.|종속성/유형, 종속성/performanceBucket, 종속성/대상, 종속성/resultCode, 작업/가상, 클라우드/roleInstance, 클라우드/roleName|
|exceptions/browser|아니요|브라우저 예외|개수|개수|브라우저에서 발생한 확인할 수 없는 예외의 개수입니다.|클라우드/roleName|
|exceptions/count|예|예외|개수|개수|모든 Catch되지 않은 예외의 결합된 수입니다.|cloud/roleName, cloud/roleInstance, client/type|
|exceptions/server|아니요|서버 예외|개수|개수|서버 애플리케이션에서 발생된 확인할 수 없는 예외의 수입니다.|cloud/roleName, cloud/roleInstance|
|pageViews/count|예|페이지 보기|개수|개수|페이지 보기 수입니다.|작업/가상, 클라우드/roleName|
|pageViews/duration|예|페이지 보기 로드 시간|밀리초|평균|페이지 보기 로드 시간|작업/가상, 클라우드/roleName|
|performanceCounters/exceptionsPerSecond|예|예외 속도|초당 개수|평균|.NET 예외 및 .NET 예외로 변환된 관리되지 않는 예외를 포함하여 Windows에 보고된 처리된 예외 및 처리되지 않은 예외 수입니다.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|예|사용 가능한 메모리|바이트|평균|프로세스에 할당하거나 시스템에서 사용할 수 있는 실제 메모리입니다.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|예|CPU 프로세스|백분율|평균|모든 프로세스 스레드가 명령을 실행하기 위해 프로세서를 사용한 경과된 시간의 백분율입니다. 0~100 사이로 달라질 수 있습니다. 이 메트릭은 w3wp 프로세스만의 성능을 나타냅니다.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|예|프로세스 IO 속도|초당 바이트 수|평균|파일, 네트워크 및 디바이스에서 읽고 쓴 초당 총 바이트 수입니다.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|예|프로세서 시간|백분율|평균|프로세서가 비 유휴 스레드에 소요한 시간의 비율입니다.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|예|프로세스 프라이빗 바이트|바이트|평균|모니터링되는 애플리케이션의 프로세스에 독점적으로 할당된 메모리입니다.|cloud/roleInstance|
|performanceCounters/requestExecutionTime|예|HTTP 요청 실행 시간|밀리초|평균|가장 최근 요청의 실행 시간입니다.|cloud/roleInstance|
|performanceCounters/requestsInQueue|예|애플리케이션 큐의 HTTP 요청|개수|평균|애플리케이션 요청 큐의 길이입니다.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|예|HTTP 요청 속도|초당 개수|평균|ASP.NET에서 애플리케이션에 전송된 모든 요청의 속도(초)입니다.|cloud/roleInstance|
|requests/count|아니요|서버 요청|개수|개수|완료된 HTTP 요청 수입니다.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/duration|예|서버 응답 시간|밀리초|평균|HTTP 요청을 받은 후 응답 전송을 완료한 때까지의 시간입니다.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/failed|아니요|실패한 요청|개수|개수|실패한 것으로 표시된 HTTP 요청 수입니다. 대부분의 경우 응답 코드가 >= 400이고 401과 같지 않은 요청입니다.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|요청/속도|아니요|서버 요청 속도|초당 개수|평균|초당 서버 요청 수|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|traces/count|예|Traces|개수|개수|문서 개수 추적|trace/severityLevel, operation/synthetic, cloud/roleName, cloud/roleInstance|

## <a name="microsoftiotcentraliotapps"></a>Microsoft 응용 프로그램

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|c2d.|예|IoT Central에서 장치 속성 읽기가 실패 했습니다.|개수|합계|IoT Central에서 시작 된 모든 실패 한 속성 읽기 수|차원 없음|
|c2d. 성공|예|IoT Central에서 장치 속성 읽기가 성공 했습니다.|개수|합계|IoT Central에서 시작 된 모든 성공한 속성 읽기 수|차원 없음|
|c2d.|예|IoT Central에서 장치 속성을 업데이트 하지 못했습니다.|개수|합계|IoT Central에서 시작 된 모든 실패 한 속성 업데이트 수|차원 없음|
|c2d. 성공|예|IoT Central에서 장치 속성 업데이트 성공|개수|합계|IoT Central에서 시작 된 모든 성공한 속성 업데이트 수|차원 없음|
|connectedDeviceCount|아니요|연결 된 총 장치|개수|평균|IoT Central에 연결 된 장치 수|차원 없음|
|d2c.|예|장치에서 실패 한 장치 속성 읽기|개수|합계|장치에서 시작 된 모든 실패 한 속성 읽기 수|차원 없음|
|d2c. 성공|예|장치에서 장치 속성 읽기가 성공 했습니다.|개수|합계|장치에서 시작 된 모든 성공한 속성 읽기 수|차원 없음|
|d2c.|예|장치에서 장치 속성 업데이트 실패|개수|합계|장치에서 시작 된 모든 실패 한 속성 업데이트 수|차원 없음|
|d2c. 성공|예|장치에서 장치 속성 업데이트 성공|개수|합계|장치에서 시작 된 모든 성공한 속성 업데이트 수|차원 없음|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|사용 가능성|예|전체 자격 증명 모음 가용성|백분율|평균|자격 증명 모음 요청 가용성|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|SaturationShoebox|아니요|전체 자격 증명 모음 채도|백분율|평균|사용 되는 자격 증명 모음 용량|ActivityType, ActivityName, TransactionType|
|ServiceApiHit|예|Service API 총 방문 횟수|개수|개수|Service API의 총 방문 횟수|ActivityType, ActivityName|
|ServiceApiLatency|예|전체 Service API 대기 시간|밀리초|평균|Service API 요청의 전체 대기 시간|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|예|Service API 총 결과|개수|개수|Service API의 총 결과 수|ActivityType, ActivityName, StatusCode, StatusCodeClass|


## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|BatchBlobCount|예|일괄 처리 Blob 수|개수|평균|수집을 위해 집계 된 일괄 처리의 데이터 원본 수입니다.|데이터베이스|
|BatchDuration|예|일괄 처리 기간|초|평균|수집 흐름의 집계 단계 기간입니다.|데이터베이스|
|BatchesProcessed 됨|예|일괄 처리|개수|평균|수집을 위해 집계 된 일괄 처리의 수입니다. 일괄 처리 완료 이유: 일괄 처리가 일괄 처리 시간, 데이터 크기 또는 일괄 처리 정책에 의해 설정 된 파일 수 제한에 도달 했는지 여부|데이터베이스, SealReason|
|BatchSize|예|Batch 크기|바이트|평균|수집을 위해 집계 된 일괄 처리에서 압축 되지 않은 예상 데이터 크기입니다.|데이터베이스|
|CacheUtilization|예|캐시 사용률|백분율|평균|클러스터 범위의 사용률 수준|차원 없음|
|ContinuousExportMaxLatenessMinutes|예|연속 내보내기 최대 지연|개수|최대|클러스터의 연속 내보내기 작업에서 보고 한 지연 (분)입니다.|차원 없음|
|ContinuousExportNumOfRecordsExported|예|연속 내보내기 – 내보낸 레코드의 개수|개수|합계|내보내기 작업 중에 작성 된 모든 저장소 아티팩트에 대해 발생 된 내보낸 레코드 수입니다.|ContinuousExportName, 데이터베이스|
|ContinuousExportPendingCount|예|보류 중인 연속 내보내기 수|개수|최대|실행 준비가 완료 된 보류 중인 연속 내보내기 작업의 수입니다.|차원 없음|
|ContinuousExportResult|예|연속 내보내기 결과|개수|개수|연속 내보내기가 성공 또는 실패 여부를 나타냅니다.|ContinuousExportName, 결과, 데이터베이스|
|CPU|예|CPU|백분율|평균|CPU 사용률 수준|차원 없음|
|EventsProcessedForEventHubs|예|이벤트 처리됨(Event/IoT Hubs의 경우)|개수|합계|수집 때 클러스터에서 처리 한 이벤트 수/IoT Hub|EventStatus|
|ExportUtilization|예|내보내기 사용률|백분율|최대|사용률 내보내기|차원 없음|
|IngestionLatencyInSeconds|예|수집 대기 시간(초)|초|평균|원본(예: EventHub에 있는 메시지)에서 클러스터로 수집 시간(초)|차원 없음|
|IngestionResult|예|수집 결과|개수|개수|수집 작업 수|IngestionResultDetails|
|IngestionUtilization|예|수집 사용률|백분율|평균|클러스터에서 사용되는 수집 슬롯의 비율|차원 없음|
|IngestionVolumeInMB|예|수집 볼륨(MB)|개수|합계|클러스터에 수집된 데이터의 전체 볼륨(MB)|차원 없음|
|InstanceCount|예|인스턴스 수|개수|평균|총 인스턴스 수|차원 없음|
|KeepAlive|예|연결 유지|개수|평균|온전성 검사는 쿼리에 대한 클러스터 응답을 나타냅니다.|차원 없음|
|QueryDuration|예|쿼리 기간|밀리초|평균|쿼리 기간(초)|QueryStatus|
|SteamingIngestRequestRate|예|스트리밍 수집 요청 속도|개수|RateRequestsPerSecond|스트리밍 수집 요청 률 (초당 요청 수)|차원 없음|
|StreamingIngestDataRate|예|스트리밍 수집 데이터 속도|개수|평균|스트리밍 수집 데이터 전송률 (초당 MB)|차원 없음|
|StreamingIngestDuration|예|스트리밍 수집 지속 시간|밀리초|평균|스트리밍 수집 기간 (밀리초)|차원 없음|
|StreamingIngestResults|예|스트리밍 수집 결과|개수|평균|스트리밍 수집 결과|결과|
|TotalNumberOfConcurrentQueries|예|총 동시 쿼리 수|개수|합계|총 동시 쿼리 수|차원 없음|
|TotalNumberOfExtents|예|총 익스텐트 수|개수|합계|총 데이터 익스텐트 수|차원 없음|
|TotalNumberOfThrottledCommands|예|제한 된 명령의 총 수|개수|합계|제한 된 명령의 총 수|CommandType|
|TotalNumberOfThrottledQueries|예|제한 된 쿼리의 총 수|개수|합계|제한 된 쿼리의 총 수|차원 없음|


## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ActionLatency|예|작업 대기 시간 |초|평균|완료된 워크플로 작업 대기 시간|차원 없음|
|ActionsCompleted|예|작업 완료됨 |개수|합계|완료된 워크플로 작업 수|차원 없음|
|ActionsFailed|예|작업 실패함 |개수|합계|실패한 워크플로 작업 수|차원 없음|
|ActionsSkipped|예|작업 생략됨 |개수|합계|생략한 워크플로 작업 수|차원 없음|
|ActionsStarted|예|작업 시작됨 |개수|합계|시작된 워크플로 작업 수|차원 없음|
|ActionsSucceeded|예|작업 성공함 |개수|합계|성공한 워크플로 작업 수|차원 없음|
|ActionSuccessLatency|예|작업 성공 대기 시간 |초|평균|성공한 워크플로 작업 대기 시간|차원 없음|
|ActionThrottledEvents|예|작업 제한 이벤트|개수|합계|워크플로 작업 제한 이벤트 수|차원 없음|
|IntegrationServiceEnvironmentConnectorMemoryUsage|예|통합 서비스 환경의 커넥터 메모리 사용량|백분율|평균|Integration service environment의 커넥터 메모리 사용량입니다.|차원 없음|
|IntegrationServiceEnvironmentConnectorProcessorUsage|예|통합 서비스 환경의 커넥터 프로세서 사용량|백분율|평균|Integration service environment의 커넥터 프로세서 사용량입니다.|차원 없음|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|예|통합 서비스 환경의 워크플로 메모리 사용량|백분율|평균|통합 서비스 환경에 대 한 워크플로 메모리 사용량입니다.|차원 없음|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|예|통합 서비스 환경의 워크플로 프로세서 사용량|백분율|평균|Integration service environment에 대 한 워크플로 프로세서 사용량입니다.|차원 없음|
|RunFailurePercentage|예|실행 오류 비율|백분율|합계|실행 실패한 워크플로 비율.|차원 없음|
|RunLatency|예|실행 대기 시간|초|평균|완료된 워크플로 실행 대기 시간|차원 없음|
|RunsCancelled|예|실행 취소됨|개수|합계|실행 취소된 워크플로 수|차원 없음|
|RunsCompleted|예|실행 완료됨|개수|합계|실행 완료된 워크플로 수|차원 없음|
|RunsFailed|예|실행 실패함|개수|합계|실행 실패한 워크플로 수|차원 없음|
|RunsStarted|예|실행 시작됨|개수|합계|실행 시작된 워크플로 수|차원 없음|
|RunsSucceeded|예|실행 성공함|개수|합계|실행 성공한 워크플로 수|차원 없음|
|RunStartThrottledEvents|예|실행 시작 제한 이벤트|개수|합계|워크플로 실행 시작 제한 이벤트의 수입니다.|차원 없음|
|RunSuccessLatency|예|실행 성공 대기 시간|초|평균|성공한 워크플로 실행 대기 시간|차원 없음|
|RunThrottledEvents|예|실행 제한 이벤트|개수|합계|워크플로 작업 또는 트리거 제한 이벤트 수|차원 없음|
|TriggerFireLatency|예|트리거 실행 대기 시간 |초|평균|실행한 워크플로 트리거 대기 시간|차원 없음|
|TriggerLatency|예|트리거 대기 시간 |초|평균|완료된 워크플로 트리거 대기 시간|차원 없음|
|TriggersCompleted|예|트리거 완료됨 |개수|합계|완료된 워크플로 트리거 수|차원 없음|
|TriggersFailed|예|트리거 실패함 |개수|합계|실패한 워크플로 트리거 수|차원 없음|
|TriggersFired|예|트리거 실행됨 |개수|합계|실행한 워크플로 트리거 수|차원 없음|
|TriggersSkipped|예|트리거 생략함|개수|합계|생략한 워크플로 트리거 수|차원 없음|
|TriggersStarted|예|트리거 시작됨 |개수|합계|시작된 워크플로 트리거 수|차원 없음|
|TriggersSucceeded|예|트리거 성공함 |개수|합계|성공한 워크플로 트리거 수|차원 없음|
|TriggerSuccessLatency|예|트리거 성공 대기 시간 |초|평균|성공한 워크플로 트리거 대기 시간|차원 없음|
|TriggerThrottledEvents|예|트리거 제한 이벤트|개수|합계|워크플로 트리거 제한 이벤트 수|차원 없음|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ActionLatency|예|작업 대기 시간 |초|평균|완료된 워크플로 작업 대기 시간|차원 없음|
|ActionsCompleted|예|작업 완료됨 |개수|합계|완료된 워크플로 작업 수|차원 없음|
|ActionsFailed|예|작업 실패함 |개수|합계|실패한 워크플로 작업 수|차원 없음|
|ActionsSkipped|예|작업 생략됨 |개수|합계|생략한 워크플로 작업 수|차원 없음|
|ActionsStarted|예|작업 시작됨 |개수|합계|시작된 워크플로 작업 수|차원 없음|
|ActionsSucceeded|예|작업 성공함 |개수|합계|성공한 워크플로 작업 수|차원 없음|
|ActionSuccessLatency|예|작업 성공 대기 시간 |초|평균|성공한 워크플로 작업 대기 시간|차원 없음|
|ActionThrottledEvents|예|작업 제한 이벤트|개수|합계|워크플로 작업 제한 이벤트 수|차원 없음|
|BillableActionExecutions|예|청구 가능한 작업 실행|개수|합계|청구되는 워크플로 작업 실행 수.|차원 없음|
|BillableTriggerExecutions|예|청구 가능한 트리거 실행|개수|합계|청구되는 워크플로 트리거 실행 수.|차원 없음|
|BillingUsageNativeOperation|예|기본 작업 실행에 대한 청구 사용량|개수|합계|청구되는 기본 작업 실행 수입니다.|차원 없음|
|BillingUsageStandardConnector|예|표준 커넥터 실행에 대한 청구 사용량|개수|합계|청구되는 표준 커넥터 실행 수입니다.|차원 없음|
|BillingUsageStorageConsumption|예|스토리지 소비 실행에 대한 청구 사용량|개수|합계|청구되는 스토리지 소비 실행 수입니다.|차원 없음|
|RunFailurePercentage|예|실행 오류 비율|백분율|합계|실행 실패한 워크플로 비율.|차원 없음|
|RunLatency|예|실행 대기 시간|초|평균|완료된 워크플로 실행 대기 시간|차원 없음|
|RunsCancelled|예|실행 취소됨|개수|합계|실행 취소된 워크플로 수|차원 없음|
|RunsCompleted|예|실행 완료됨|개수|합계|실행 완료된 워크플로 수|차원 없음|
|RunsFailed|예|실행 실패함|개수|합계|실행 실패한 워크플로 수|차원 없음|
|RunsStarted|예|실행 시작됨|개수|합계|실행 시작된 워크플로 수|차원 없음|
|RunsSucceeded|예|실행 성공함|개수|합계|실행 성공한 워크플로 수|차원 없음|
|RunStartThrottledEvents|예|실행 시작 제한 이벤트|개수|합계|워크플로 실행 시작 제한 이벤트의 수입니다.|차원 없음|
|RunSuccessLatency|예|실행 성공 대기 시간|초|평균|성공한 워크플로 실행 대기 시간|차원 없음|
|RunThrottledEvents|예|실행 제한 이벤트|개수|합계|워크플로 작업 또는 트리거 제한 이벤트 수|차원 없음|
|TotalBillableExecutions|예|총 청구 가능 실행|개수|합계|청구되는 워크플로 실행 수.|차원 없음|
|TriggerFireLatency|예|트리거 실행 대기 시간 |초|평균|실행한 워크플로 트리거 대기 시간|차원 없음|
|TriggerLatency|예|트리거 대기 시간 |초|평균|완료된 워크플로 트리거 대기 시간|차원 없음|
|TriggersCompleted|예|트리거 완료됨 |개수|합계|완료된 워크플로 트리거 수|차원 없음|
|TriggersFailed|예|트리거 실패함 |개수|합계|실패한 워크플로 트리거 수|차원 없음|
|TriggersFired|예|트리거 실행됨 |개수|합계|실행한 워크플로 트리거 수|차원 없음|
|TriggersSkipped|예|트리거 생략함|개수|합계|생략한 워크플로 트리거 수|차원 없음|
|TriggersStarted|예|트리거 시작됨 |개수|합계|시작된 워크플로 트리거 수|차원 없음|
|TriggersSucceeded|예|트리거 성공함 |개수|합계|성공한 워크플로 트리거 수|차원 없음|
|TriggerSuccessLatency|예|트리거 성공 대기 시간 |초|평균|성공한 워크플로 트리거 대기 시간|차원 없음|
|TriggerThrottledEvents|예|트리거 제한 이벤트|개수|합계|워크플로 트리거 제한 이벤트 수|차원 없음|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|활성 코어|예|활성 코어|개수|평균|활성 코어 수|시나리오, ClusterName|
|활성 노드|예|활성 노드|개수|평균|활성 노드 수입니다. 작업을 적극적으로 실행 중인 노드입니다.|시나리오, ClusterName|
|요청 된 실행 취소|예|요청 된 실행 취소|개수|합계|이 작업 영역에 대해 취소가 요청 된 실행 수입니다. 실행에 대 한 취소 요청을 받으면 개수가 업데이트 됩니다.|Scenario, RunType, PublishedPipelineId, PipelineStepType Etype,|
|취소 실행|예|취소 실행|개수|합계|이 작업 영역에 대해 취소 된 실행 수입니다. 실행이 성공적으로 취소 되 면 개수가 업데이트 됩니다.|Scenario, RunType, PublishedPipelineId, PipelineStepType Etype,|
|완료된 실행|예|완료된 실행|개수|합계|이 작업 영역에 대 한 실행 수가 성공적으로 완료 되었습니다. 실행이 완료 되 고 출력이 수집 되 면 개수가 업데이트 됩니다.|Scenario, RunType, PublishedPipelineId, PipelineStepType Etype,|
|CpuUtilization|예|CpuUtilization|개수|평균|CPU 노드의 메모리 사용률 비율입니다. 사용률은 1 분 간격으로 보고 됩니다.|시나리오, runId, NodeId, ClusterName|
|오류|예|오류|개수|합계|이 작업 영역에 있는 실행 오류 수입니다. 실행 시 오류가 발생할 때마다 개수가 업데이트 됩니다.|시나리오|
|실패한 실행|예|실패한 실행|개수|합계|이 작업 영역에 대 한 실행 수가 실패 했습니다. 실행이 실패 하면 개수가 업데이트 됩니다.|Scenario, RunType, PublishedPipelineId, PipelineStepType Etype,|
|실행 완료 중|예|실행 완료 중|개수|합계|이 작업 영역의 상태를 완료 하는 동안 입력 한 실행 수입니다. Count는 실행이 완료 되었지만 출력 수집이 아직 진행 중일 때 업데이트 됩니다.|Scenario, RunType, PublishedPipelineId, PipelineStepType Etype,|
|GpuUtilization|예|GpuUtilization|개수|평균|GPU 노드의 메모리 사용률 비율입니다. 사용률은 1 분 간격으로 보고 됩니다.|시나리오, runId, NodeId, DeviceId, ClusterName|
|유휴 코어|예|유휴 코어|개수|평균|유휴 코어 수|시나리오, ClusterName|
|유휴 노드|예|유휴 노드|개수|평균|유휴 노드의 수입니다. 유휴 노드는 작업을 실행 하지 않지만 사용 가능한 경우 새 작업을 허용할 수 있는 노드입니다.|시나리오, ClusterName|
|나가는 코어|예|나가는 코어|개수|평균|종료 된 코어 수|시나리오, ClusterName|
|나가는 노드|예|나가는 노드|개수|평균|종료 노드 수입니다. 노드가 종료 되 면 작업 처리가 완료 되 고 유휴 상태로 전환 됩니다.|시나리오, ClusterName|
|모델 배포 실패|예|모델 배포 실패|개수|합계|이 작업 영역에서 실패 한 모델 배포 수|시나리오, StatusCode|
|모델 배포 시작|예|모델 배포 시작|개수|합계|이 작업 영역에서 시작 된 모델 배포 수|시나리오|
|모델 배포 성공|예|모델 배포 성공|개수|합계|이 작업 영역에서 성공한 모델 배포 수|시나리오|
|모델 레지스터 실패|예|모델 레지스터 실패|개수|합계|이 작업 영역에서 실패 한 모델 등록 수|시나리오, StatusCode|
|모델 레지스터 성공|예|모델 레지스터 성공|개수|합계|이 작업 영역에서 성공한 모델 등록 수|시나리오|
|응답 하지 않음|예|응답 하지 않음|개수|합계|이 작업 영역에 대해 응답 하지 않는 실행 수입니다. 실행이 응답 하지 않는 상태로 전환 되 면 개수가 업데이트 됩니다.|Scenario, RunType, PublishedPipelineId, PipelineStepType Etype,|
|시작 되지 않음 실행|예|시작 되지 않음 실행|개수|합계|이 작업 영역에 대해 시작 되지 않음 상태의 실행 수입니다. 실행을 만들기 위해 요청을 받았지만 실행 정보는 아직 채워지지 않은 경우 수가 업데이트 됩니다. |Scenario, RunType, PublishedPipelineId, PipelineStepType Etype,|
|선점된 코어|예|선점된 코어|개수|평균|선점 된 코어 수|시나리오, ClusterName|
|선점된 노드|예|선점된 노드|개수|평균|선점 된 노드 수입니다. 이러한 노드는 사용 가능한 노드 풀에서 멀리 떨어져 있는 낮은 우선 순위 노드입니다.|시나리오, ClusterName|
|실행 준비|예|실행 준비|개수|합계|이 작업 영역에 대해 준비 중인 실행 수입니다. 실행 환경을 준비 하는 동안 실행이 준비 상태에 들어가면 개수가 업데이트 됩니다.|Scenario, RunType, PublishedPipelineId, PipelineStepType Etype,|
|프로 비전 실행|예|프로 비전 실행|개수|합계|이 작업 영역에 대해 프로 비전 되는 실행 수입니다. 실행이 계산 대상 생성 또는 프로 비전을 기다리는 경우 개수가 업데이트 됩니다.|Scenario, RunType, PublishedPipelineId, PipelineStepType Etype,|
|대기 중인 실행|예|대기 중인 실행|개수|합계|이 작업 영역에 대해 큐에 대기 중인 실행의 수입니다. 계산 대상에서 실행이 큐에 대기 하면 개수가 업데이트 됩니다. 필수 계산 노드가 준비 될 때까지 대기 하는 경우 occure 수 있습니다.|Scenario, RunType, PublishedPipelineId, PipelineStepType Etype,|
|할당량 사용률|예|할당량 사용률|개수|평균|사용한 할당량 백분율|시나리오, ClusterName, VmFamilyName, VmPriority|
|실행 시작|예|실행 시작|개수|합계|이 작업 영역에 대해 실행 중인 실행 수입니다. 실행이 필요한 리소스에서 실행 되기 시작 하면 개수가 업데이트 됩니다.|Scenario, RunType, PublishedPipelineId, PipelineStepType Etype,|
|실행 시작|예|실행 시작|개수|합계|이 작업 영역에 대해 시작 된 실행 수입니다. 실행 Id와 같은 실행 및 실행 정보에 대 한 요청을 채운 후에 개수가 업데이트 됩니다.|Scenario, RunType, PublishedPipelineId, PipelineStepType Etype,|
|총 코어 수|예|총 코어 수|개수|평균|총 코어 수|시나리오, ClusterName|
|총 노드 수|예|총 노드 수|개수|평균|총 노드 수입니다. 이 합계에는 활성 노드, 유휴 노드, 사용할 수 없는 노드, Premepted 노드, 노드가 남아 있습니다.|시나리오, ClusterName|
|사용 불가 코어|예|사용 불가 코어|개수|평균|사용할 수 없는 코어 수|시나리오, ClusterName|
|사용 불가 노드|예|사용 불가 노드|개수|평균|사용할 수 없는 노드 수입니다. 확인할 수 없는 노드는 확인할 수 없는 문제 때문에 작동 하지 않습니다. Azure는 이러한 노드를 재활용 합니다.|시나리오, ClusterName|
|경고|예|경고|개수|합계|이 작업 영역에 있는 실행 경고 수입니다. 실행에 경고가 발생할 때마다 개수가 업데이트 됩니다.|시나리오|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|사용 가능성|예|가용성|백분율|평균|Api의 가용성|ApiCategory, ApiName|
|사용|아니요|사용|개수|개수|API 호출 수|ApiCategory, ApiName, ResultType, ResponseCode|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|AssetCount|예|자산 수|개수|평균|현재 미디어 서비스 계정에 이미 생성 된 자산 수|차원 없음|
|AssetQuota|예|자산 할당량|개수|평균|현재 미디어 서비스 계정에 대해 허용 되는 자산 수|차원 없음|
|AssetQuotaUsedPercentage|예|사용된 자산 할당량 백분율|백분율|평균|현재 미디어 서비스 계정에서 사용 되는 자산 비율|차원 없음|
|ContentKeyPolicyCount|예|콘텐츠 키 정책 수|개수|평균|현재 미디어 서비스 계정에 이미 생성 된 콘텐츠 키 정책 수|차원 없음|
|ContentKeyPolicyQuota|예|콘텐츠 키 정책 할당량|개수|평균|현재 미디어 서비스 계정에 대해 허용 되는 콘텐츠 키 정책 수|차원 없음|
|ContentKeyPolicyQuotaUsedPercentage|예|사용된 콘텐츠 키 정책 할당량 백분율|백분율|평균|현재 미디어 서비스 계정에서 사용 되는 콘텐츠 키 정책 비율|차원 없음|
|StreamingPolicyCount|예|스트리밍 정책 수|개수|평균|현재 미디어 서비스 계정에 이미 생성 된 스트리밍 정책 수|차원 없음|
|StreamingPolicyQuota|예|스트리밍 정책 할당량|개수|평균|현재 미디어 서비스 계정에 대해 허용 되는 스트리밍 정책 수|차원 없음|
|StreamingPolicyQuotaUsedPercentage|예|사용된 스트리밍 정책 할당량 백분율|백분율|평균|현재 미디어 서비스 계정에서 사용 되는 스트리밍 정책 비율|차원 없음|


## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft.Media/mediaservices/streamingEndpoints

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|송신|예|송신|바이트|합계|송신 데이터의 양 (바이트)입니다.|OutputFormat|
|요청|예|요청|개수|합계|스트리밍 끝점에 대 한 요청입니다.|OutputFormat, HttpStatusCode, ErrorCode|
|SuccessE2ELatency|예|성공 엔드투엔드 대기 시간|밀리초|평균|성공한 요청의 평균 대기 시간 (밀리초)입니다.|OutputFormat|


## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|VolumePoolAllocatedSize|예|풀 할당 크기|바이트|평균|이 풀의 프로 비전 된 크기|차원 없음|
|VolumePoolAllocatedUsed|예|볼륨 크기에 할당 된 풀|바이트|평균|풀의 사용되는 할당된 크기|차원 없음|
|VolumePoolTotalLogicalSize|예|풀 사용 크기|바이트|평균|풀에 속한 모든 볼륨의 논리적 크기의 합계|차원 없음|
|VolumePoolTotalSnapshotSize|예|풀의 총 스냅숏 크기|바이트|평균|이 풀에 있는 모든 볼륨의 스냅숏 크기 합계입니다.|차원 없음|


## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/volumes

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|AverageReadLatency|예|평균 읽기 대기 시간|밀리초|평균|작업당 평균 읽기 대기 시간(밀리초)|차원 없음|
|AverageWriteLatency|예|평균 쓰기 대기 시간|밀리초|평균|작업당 평균 쓰기 대기 시간(밀리초)|차원 없음|
|CbsVolumeBackupActive|예|볼륨 백업 활성 상태|개수|평균|현재 볼륨에 대해 백업이 일시 중단 되었습니다.|차원 없음|
|CbsVolumeLogicalBackupBytes|예|논리적으로 백업 되는 바이트|바이트|평균|이 볼륨에 대해 백업 된 압축 취소/암호화 되지 않은 바이트 바이트입니다.|차원 없음|
|CbsVolumeOperationComplete|예|작업 상태|개수|평균|마지막 백업/복원 작업이 성공 했습니다.|차원 없음|
|CbsVolumeOperationTransferredBytes|예|작업에 대해 전송 된 바이트|바이트|평균|마지막 백업/복원 작업에 대해 전송 된 총 바이트 수입니다.|차원 없음|
|CbsVolumeProtected|예|볼륨 보호 상태|개수|평균|은 (는) 클라우드 백업 서비스에 의해 볼륨이 보호 되 고 있습니다.|차원 없음|
|ReadIops|예|읽기 IOPS|초당 개수|평균|초당 읽기 입력/출력 작업|차원 없음|
|VolumeAllocatedSize|예|할당된 볼륨 크기|바이트|평균|프로 비전 된 볼륨 크기|차원 없음|
|VolumeLogicalSize|예|사용 된 볼륨 크기|바이트|평균|볼륨의 논리적 크기(사용되는 바이트)|차원 없음|
|VolumeSnapshotSize|예|볼륨 스냅샷 크기|바이트|평균|볼륨의 모든 스냅샷 크기|차원 없음|
|WriteIops|예|쓰기 IOPS|초당 개수|평균|초당 쓰기 입력/출력 작업|차원 없음|
|XregionReplicationHealthy|예|볼륨 복제 상태가 정상 임|개수|평균|관계의 조건으로, 1 또는 0입니다.|차원 없음|
|XregionReplicationLagTime|예|볼륨 복제 지연 시간|초|평균|미러의 데이터가 원본 뒤에서 지연 되는 시간 (초)입니다.|차원 없음|
|XregionReplicationLastTransferDuration|예|볼륨 복제 마지막 전송 기간|초|평균|마지막 전송을 완료 하는 데 걸린 시간 (초)입니다.|차원 없음|
|XregionReplicationLastTransferSize|예|볼륨 복제 마지막 전송 크기|바이트|평균|마지막 전송의 일부로 전송 된 총 바이트 수입니다.|차원 없음|
|XregionReplicationRelationshipProgress|예|볼륨 복제 진행률|바이트|평균|현재 전송 작업에 대해 전송 된 총 데이터 양입니다.|차원 없음|
|XregionReplicationRelationshipTransferring|예|볼륨 복제 전송|개수|평균|볼륨 복제 상태가 ' 전송 중 ' 인지 여부입니다.|차원 없음|
|XregionReplicationTotalTransferBytes|예|볼륨 복제 총 전송|바이트|평균|관계에 대해 전송 된 누적 바이트 수입니다.|차원 없음|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ApplicationGatewayTotalTime|아니요|Application Gateway 총 시간|밀리초|평균|요청을 처리 하는 데 걸리는 평균 시간 및 응답을 보내는 데 걸리는 시간입니다. 이는 Application Gateway에서 HTTP 요청의 첫 번째 바이트를 수신 하는 시점부터 응답 전송 작업이 완료 되는 시간 까지의 평균 간격으로 계산 됩니다. 일반적으로 처리 시간, 요청 및 응답 패킷이 네트워크를 통해 이동 하는 시간 및 백 엔드 서버에서 응답 하는 데 걸린 시간을 Application Gateway 포함 하는 것이 중요 합니다.|수신기|
|AvgRequestCountPerHealthyHost|아니요|분당 요청 수/정상 호스트|개수|평균|풀의 정상 백 엔드 호스트 당 분당 평균 요청 수|BackendSettingsPool|
|BackendConnectTime|아니요|백 엔드 연결 시간|밀리초|평균|백 엔드 서버와의 연결을 설정 하는 데 걸린 시간|Listener, BackendServer, BackendPool, BackendHttpSetting|
|BackendFirstByteResponseTime|아니요|백 엔드 첫 번째 바이트 응답 시간|밀리초|평균|백 엔드 서버에 대 한 연결을 설정 하 고 응답 헤더의 첫 번째 바이트를 수신 하는 시작 사이의 시간 간격 (백 엔드 서버의 처리 시간에 가까운)|Listener, BackendServer, BackendPool, BackendHttpSetting|
|BackendLastByteResponseTime|아니요|백 엔드 마지막 바이트 응답 시간|밀리초|평균|백 엔드 서버에 대 한 연결을 설정 하 고 응답 본문의 마지막 바이트를 받는 시작 사이의 시간 간격입니다.|Listener, BackendServer, BackendPool, BackendHttpSetting|
|BackendResponseStatus|예|백 엔드 응답 상태|개수|합계|백 엔드 멤버에 의해 생성 된 HTTP 응답 코드의 수입니다. Application Gateway에서 생성 된 응답 코드는 여기에 포함 되지 않습니다.|BackendServer, BackendPool, BackendHttpSetting, HttpStatusGroup|
|BlockedCount|예|웹 애플리케이션 방화벽 차단 요청 규칙 배포|개수|합계|웹 응용 프로그램 방화벽 차단 된 요청 규칙 배포|RuleGroup, RuleId|
|BlockedReqCount|예|웹 애플리케이션 방화벽 차단 요청 수|개수|합계|웹 응용 프로그램 방화벽 차단 된 요청 수|차원 없음|
|BytesReceived|예|수신된 바이트|바이트|합계|클라이언트에서 Application Gateway 받은 총 바이트 수입니다.|수신기|
|BytesSent|예|보낸 바이트|바이트|합계|Application Gateway 클라이언트에 보낸 총 바이트 수입니다.|수신기|
|CapacityUnits|아니요|현재 용량 단위|개수|평균|소비 된 용량 단위|차원 없음|
|ClientRtt|아니요|클라이언트 RTT|밀리초|평균|클라이언트와 Application Gateway 사이의 평균 왕복 시간입니다. 이 메트릭은 연결을 설정 하 고 승인을 반환 하는 데 걸리는 시간을 나타냅니다.|수신기|
|ComputeUnits|아니요|현재 컴퓨팅 단위|개수|평균|소비 된 계산 단위|차원 없음|
|CpuUtilization|아니요|CPU 사용률|백분율|평균|Application Gateway의 현재 CPU 사용률|차원 없음|
|CurrentConnections|예|현재 연결|개수|합계|Application Gateway와 설정된 현재 연결 수|차원 없음|
|EstimatedBilledCapacityUnits|아니요|예상 청구 용량 단위|개수|평균|요금이 부과 되는 예상 용량 단위|차원 없음|
|FailedRequests|예|실패한 요청|개수|합계|Application Gateway가 제공하는 실패한 요청 수|BackendSettingsPool|
|FixedBillableCapacityUnits|아니요|고정 청구 가능 용량 단위|개수|평균|요금이 부과 되는 최소 용량 단위|차원 없음|
|HealthyHostCount|예|정상 호스트 수|개수|평균|정상 백 엔드 호스트 수|BackendSettingsPool|
|MatchedCount|예|웹 애플리케이션 방화벽 총 규칙 배포|개수|합계|들어오는 트래픽에 대 한 웹 응용 프로그램 방화벽 총 규칙 배포|RuleGroup, RuleId|
|NewConnectionsPerSecond|아니요|초당 새 연결 수|초당 개수|평균|Application Gateway로 설정 된 초당 새 연결 수|차원 없음|
|ResponseStatus|예|응답 상태|개수|합계|Application Gateway에서 반환된 HTTP 응답 상태|HttpStatusGroup|
|처리량|아니요|처리량|초당 바이트 수|평균|Application Gateway에서 제공하는 초당 바이트 수|차원 없음|
|TlsProtocol|예|클라이언트 TLS 프로토콜|개수|합계|Application Gateway와의 연결을 설정한 클라이언트에서 시작한 tls 및 비 TLS 요청 수입니다. TLS 프로토콜 배포를 보려면 차원 TLS 프로토콜을 기준으로 필터링 합니다.|수신기, TlsProtocol|
|TotalRequests|예|총 요청 수|개수|합계|Application Gateway가 제공하는 성공한 요청 수|BackendSettingsPool|
|UnhealthyHostCount|예|비정상 호스트 수|개수|평균|비정상 백 엔드 호스트 수|BackendSettingsPool|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ApplicationRuleHit|예|애플리케이션 규칙 적중 횟수|개수|합계|응용 프로그램 규칙이 적중 된 횟수|상태, 이유, 프로토콜|
|DataProcessed|예|처리된 데이터|바이트|합계|이 방화벽에서 처리 한 총 데이터 양입니다.|차원 없음|
|FirewallHealth|예|Firewall 성능 상태|백분율|평균|이 방화벽의 전반적인 상태를 나타냅니다.|상태, 이유|
|NetworkRuleHit|예|네트워크 규칙 적중 횟수|개수|합계|네트워크 규칙이 적중 된 횟수|상태, 이유, 프로토콜|
|SNATPortUtilization|예|SNAT 포트 사용률|백분율|평균|현재 사용 중인 아웃 바운드 SNAT 포트의 백분율|프로토콜|
|처리량|아니요|처리량|BitsPerSecond|평균|이 방화벽에 의해 처리 된 처리량|차원 없음|


## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|BitsInPerSecond|예|BitsInPerSecond|초당 개수|평균|초당 Azure 수신 비트|차원 없음|
|BitsOutPerSecond|예|BitsOutPerSecond|초당 개수|평균|초당 Azure 송신 비트|차원 없음|


## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|QueryVolume|예|쿼리 볼륨|개수|합계|DNS 영역에 대해 제공된 쿼리 수|차원 없음|
|RecordSetCapacityUtilization|아니요|레코드 집합 용량 사용률|백분율|최대|DNS 영역에서 사용된 레코드 집합 용량 백분율|차원 없음|
|RecordSetCount|예|레코드 집합 수|개수|최대|DNS 영역의 레코드 집합 수|차원 없음|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ArpAvailability|예|Arp 가용성|백분율|평균|M의 ARP 가용성 모든 피어를 확인 합니다.|PeeringType, 피어|
|BgpAvailability|예|Bgp 가용성|백분율|평균|모든 피어에 대 한 MSEE의 BGP 가용성|PeeringType, 피어|
|BitsInPerSecond|아니요|BitsInPerSecond|초당 개수|평균|초당 Azure 수신 비트|PeeringType|
|BitsOutPerSecond|아니요|BitsOutPerSecond|초당 개수|평균|초당 Azure 송신 비트|PeeringType|
|GlobalReachBitsInPerSecond|아니요|GlobalReachBitsInPerSecond|초당 개수|평균|초당 Azure 수신 비트|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|아니요|GlobalReachBitsOutPerSecond|초당 개수|평균|초당 Azure 송신 비트|PeeredCircuitSKey|
|QosDropBitsInPerSecond|아니요|DroppedInBitsPerSecond|초당 개수|평균|초당 삭제 된 데이터의 수신 비트 수|차원 없음|
|QosDropBitsOutPerSecond|아니요|DroppedOutBitsPerSecond|초당 개수|평균|초당 삭제 된 데이터의 송신 비트 수|차원 없음|


## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|BitsInPerSecond|예|BitsInPerSecond|초당 개수|평균|초당 Azure 수신 비트|차원 없음|
|BitsOutPerSecond|예|BitsOutPerSecond|초당 개수|평균|초당 Azure 송신 비트|차원 없음|


## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft.Network/expressRouteGateways

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|아니요|BitsInPerSecond|초당 개수|평균|초당 Azure 수신 비트|연결 이름|
|ErGatewayConnectionBitsOutPerSecond|아니요|BitsOutPerSecond|초당 개수|평균|초당 Azure 송신 비트|연결 이름|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft.Network/expressRoutePorts

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|AdminState|예|AdminState|개수|평균|포트의 관리 상태|링크|
|LineProtocol|예|LineProtocol|개수|평균|포트의 선 프로토콜 상태|링크|
|PortBitsInPerSecond|예|BitsInPerSecond|초당 개수|평균|초당 Azure 수신 비트|링크|
|PortBitsOutPerSecond|예|BitsOutPerSecond|초당 개수|평균|초당 Azure 송신 비트|링크|
|RxLightLevel|예|RxLightLevel|개수|평균|DBm 광원 수준 (dBm)|링크, 레인|
|TxLightLevel|예|TxLightLevel|개수|평균|Tx 라이트 수준 (dBm)|링크, 레인|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|BackendHealthPercentage|예|백 엔드 상태 비율|백분율|평균|HTTP/S 프록시에서 백 엔드로 성공한 상태 프로브의 비율|Backend, BackendPool|
|BackendRequestCount|예|백 엔드 요청 수|개수|합계|HTTP/S 프록시에서 백 엔드로 전송된 요청 수|HttpStatus, HttpStatusGroup, Backend|
|BackendRequestLatency|예|백 엔드 요청 대기 시간|밀리초|평균|HTTP/S 프록시에서 백 엔드의 마지막 응답 바이트를 받을 때까지 HTTP/S 프록시에서 백 엔드로 요청이 전송될 때 계산된 시간|백 엔드|
|BillableResponseSize|예|청구 대상 응답 크기|바이트|합계|HTTP/S 프록시에서 클라이언트에 응답으로 보낸 청구 가능 바이트 (요청당 최소 2KB) 수입니다.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestCount|예|요청 수|개수|합계|HTTP/S 프록시에서 제공하는 클라이언트 요청 수|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|예|요청 크기|바이트|합계|클라이언트에서 HTTP/S 프록시로 요청으로 전송된 바이트 수|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|예|응답 크기|바이트|합계|HTTP/S 프록시에서 클라이언트로 응답으로 전송된 바이트 수|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|TotalLatency|예|총 대기 시간|밀리초|평균|클라이언트가 HTTP/S 프록시의 마지막 응답 바이트를 승인할 때까지 클라이언트 요청이 HTTP/S 프록시에서 수신될 때 계산된 시간|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|WebApplicationFirewallRequestCount|예|웹 애플리케이션 방화벽 요청 수|개수|합계|웹 애플리케이션 방화벽에서 처리된 클라이언트 요청 수|PolicyName, RuleName, Action|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|AllocatedSnatPorts|아니요|할당 된 SNAT 포트|개수|평균|기간 내에 할당된 총 SNAT 포트 수|FrontendIPAddress, BackendIPAddress, ProtocolType, |
|ByteCount|예|바이트 수|개수|합계|기간 내에 전송된 총 바이트 수|FrontendIPAddress, FrontendPort, Direction|
|DipAvailability|예|상태 프로브 상태|개수|평균|기간당 평균 Load Balancer 상태 프로브 상태|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|PacketCount|예|패킷 수|개수|합계|기간 내에 전송된 총 패킷 수|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|예|SNAT 연결 수|개수|합계|기간 내에 만들어진 새 SNAT 연결의 총 수|FrontendIPAddress, BackendIPAddress, ConnectionState|
|SYNCount|예|SYN 수|개수|합계|기간 내에 전송된 총 SYN 패킷 수|FrontendIPAddress, FrontendPort, Direction|
|UsedSnatPorts|아니요|사용 되는 SNAT 포트|개수|평균|기간 내에 사용된 총 SNAT 포트 수|FrontendIPAddress, BackendIPAddress, ProtocolType, |
|VipAvailability|예|데이터 경로 가용성|개수|평균|기간당 평균 Load Balancer 데이터 경로 가용성|FrontendIPAddress, FrontendPort|


## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|BytesReceivedRate|예|수신된 바이트|바이트|합계|네트워크 인터페이스가 수신한 바이트 수|차원 없음|
|BytesSentRate|예|보낸 바이트|바이트|합계|네트워크 인터페이스가 보낸 바이트 수|차원 없음|
|PacketsReceivedRate|예|수신된 패킷|개수|합계|네트워크 인터페이스가 수신한 패킷 수|차원 없음|
|PacketsSentRate|예|보낸 패킷|개수|합계|네트워크 인터페이스가 보낸 패킷 수|차원 없음|


## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|AverageRoundtripMs|예|평균 왕복 시간(ms)|밀리초|평균|원본과 대상 간에 전송된 연결 모니터링 프로브의 평균 네트워크 왕복 시간(ms)|차원 없음|
|ChecksFailedPercent|예|검사 실패 백분율(미리 보기)|백분율|평균|%의 연결 모니터링 검사가 실패 했습니다.|SourceAddress, SourceName, Sourceresourceid 여야, SourceType, Protocol, DestinationAddress, Destinationaddress, Destinationaddress, DestinationType, Destinationaddress, TestGroupName, Testgroupname|
|ProbesFailedPercent|예|실패한 프로브 %|백분율|평균|실패한 연결 모니터링 프로브 %|차원 없음|
|RoundTripTimeMs|예|왕복 시간(밀리초)(미리 보기)|밀리초|평균|연결 모니터링 검사의 왕복 시간 (밀리초)|SourceAddress, SourceName, Sourceresourceid 여야, SourceType, Protocol, DestinationAddress, Destinationaddress, Destinationaddress, DestinationType, Destinationaddress, TestGroupName, Testgroupname|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ByteCount|예|바이트 수|개수|합계|기간 내에 전송된 총 바이트 수|포트, 방향|
|BytesDroppedDDoS|예|인바운드 바이트가 삭제된 DDoS|초당 바이트 수|최대|인바운드 바이트가 삭제된 DDoS|차원 없음|
|BytesForwardedDDoS|예|인바운드 바이트가 전달된 DDoS|초당 바이트 수|최대|인바운드 바이트가 전달된 DDoS|차원 없음|
|BytesInDDoS|예|인바운드 바이트 DDoS|초당 바이트 수|최대|인바운드 바이트 DDoS|차원 없음|
|DDoSTriggerSYNPackets|예|DDoS 완화를 트리거하는 인바운드 SYN 패킷|초당 개수|최대|DDoS 완화를 트리거하는 인바운드 SYN 패킷|차원 없음|
|DDoSTriggerTCPPackets|예|DDoS 완화를 트리거하는 인바운드 TCP 패킷|초당 개수|최대|DDoS 완화를 트리거하는 인바운드 TCP 패킷|차원 없음|
|DDoSTriggerUDPPackets|예|DDoS 완화를 트리거하는 인바운드 UDP 패킷|초당 개수|최대|DDoS 완화를 트리거하는 인바운드 UDP 패킷|차원 없음|
|IfUnderDDoSAttack|예|DDoS 공격 진행 여부|개수|최대|DDoS 공격 진행 여부|차원 없음|
|PacketCount|예|패킷 수|개수|합계|기간 내에 전송된 총 패킷 수|포트, 방향|
|PacketsDroppedDDoS|예|인바운드 패킷이 삭제된 DDoS|초당 개수|최대|인바운드 패킷이 삭제된 DDoS|차원 없음|
|PacketsForwardedDDoS|예|인바운드 패킷이 전달된 DDoS|초당 개수|최대|인바운드 패킷이 전달된 DDoS|차원 없음|
|PacketsInDDoS|예|인바운드 패킷 DDoS|초당 개수|최대|인바운드 패킷 DDoS|차원 없음|
|SynCount|예|SYN 수|개수|합계|기간 내에 전송된 총 SYN 패킷 수|포트, 방향|
|TCPBytesDroppedDDoS|예|인바운드 TCP 바이트가 삭제된 DDoS|초당 바이트 수|최대|인바운드 TCP 바이트가 삭제된 DDoS|차원 없음|
|TCPBytesForwardedDDoS|예|인바운드 TCP 바이트가 전달된 DDoS|초당 바이트 수|최대|인바운드 TCP 바이트가 전달된 DDoS|차원 없음|
|TCPBytesInDDoS|예|인바운드 TCP 바이트 DDoS|초당 바이트 수|최대|인바운드 TCP 바이트 DDoS|차원 없음|
|TCPPacketsDroppedDDoS|예|인바운드 TCP 패킷이 삭제된 DDoS|초당 개수|최대|인바운드 TCP 패킷이 삭제된 DDoS|차원 없음|
|TCPPacketsForwardedDDoS|예|인바운드 TCP 패킷이 전달된 DDoS|초당 개수|최대|인바운드 TCP 패킷이 전달된 DDoS|차원 없음|
|TCPPacketsInDDoS|예|인바운드 TCP 패킷 DDoS|초당 개수|최대|인바운드 TCP 패킷 DDoS|차원 없음|
|UDPBytesDroppedDDoS|예|인바운드 UDP 바이트가 삭제된 DDoS|초당 바이트 수|최대|인바운드 UDP 바이트가 삭제된 DDoS|차원 없음|
|UDPBytesForwardedDDoS|예|인바운드 UDP 바이트가 전달된 DDoS|초당 바이트 수|최대|인바운드 UDP 바이트가 전달된 DDoS|차원 없음|
|UDPBytesInDDoS|예|인바운드 UDP 바이트 DDoS|초당 바이트 수|최대|인바운드 UDP 바이트 DDoS|차원 없음|
|UDPPacketsDroppedDDoS|예|인바운드 UDP 패킷이 삭제된 DDoS|초당 개수|최대|인바운드 UDP 패킷이 삭제된 DDoS|차원 없음|
|UDPPacketsForwardedDDoS|예|인바운드 UDP 패킷이 전달된 DDoS|초당 개수|최대|인바운드 UDP 패킷이 전달된 DDoS|차원 없음|
|UDPPacketsInDDoS|예|인바운드 UDP 패킷 DDoS|초당 개수|최대|인바운드 UDP 패킷 DDoS|차원 없음|
|VipAvailability|예|데이터 경로 가용성|개수|평균|기간당 평균 IP 주소 가용성|포트|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ProbeAgentCurrentEndpointStateByProfileResourceId|예|엔드포인트별 엔드포인트 상태|개수|최대|엔드포인트의 프로브 상태가 "사용"이면 1이고 그렇지 않으면 0입니다.|EndpointName|
|QpsByEndpoint|예|반환된 엔드포인트별 쿼리|개수|합계|Traffic Manager 엔드포인트가 주어진 시간 내에 반환된 횟수|EndpointName|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|AverageBandwidth|예|게이트웨이 S2S 대역폭|초당 바이트 수|평균|초당 게이트웨이의 평균 사이트 간 대역폭(바이트)|차원 없음|
|P2SBandwidth|예|게이트웨이 P2S 대역폭|초당 바이트 수|평균|초당 게이트웨이의 평균 지점 및 사이트 간 대역폭(바이트)|차원 없음|
|P2SConnectionCount|예|P2S 연결 수|개수|최대|게이트웨이의 지점 및 사이트 간 연결 수|프로토콜|
|TunnelAverageBandwidth|예|터널 대역폭|초당 바이트 수|평균|초당 터널의 평균 대역폭(바이트)|ConnectionName, RemoteIP|
|TunnelEgressBytes|예|터널 송신 바이트|바이트|합계|터널의 송신 바이트|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|예|터널 송신 TS 불일치 패킷 삭제|개수|합계|터널의 트래픽 선택기 불일치에서 나가는 패킷 삭제 수|ConnectionName, RemoteIP|
|TunnelEgressPackets|예|터널 송신 패킷|개수|합계|터널의 송신 패킷 수|ConnectionName, RemoteIP|
|TunnelIngressBytes|예|터널 수신 바이트|바이트|합계|터널의 수신 바이트|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|예|터널 수신 TS 불일치 패킷 삭제|개수|합계|터널의 트래픽 선택기 불일치에서 들어오는 패킷 삭제 수|ConnectionName, RemoteIP|
|TunnelIngressPackets|예|터널 수신 패킷|개수|합계|터널의 수신 패킷 수|ConnectionName, RemoteIP|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|예|VM에 대한 Ping 왕복 시간|밀리초|평균|대상 VM에 전송 된 Ping에 대 한 왕복 시간|SourceCustomerAddress, DestinationCustomerAddress|
|PingMeshProbesFailedPercent|예|VM에 대한 Ping 실패|백분율|평균|대상 VM의 총 전송 된 Ping에 대 한 실패 한 Ping 수의 백분율|SourceCustomerAddress, DestinationCustomerAddress|


## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|incoming|예|들어오는 메시지|개수|합계|성공한 모든 API 호출 전송의 수입니다. |차원 없음|
|incoming.all.failedrequests|예|들어오는 모든 실패한 요청|개수|합계|알림 허브에 대해 들어오는 실패한 전체 요청|차원 없음|
|incoming.all.requests|예|들어오는 모든 요청|개수|합계|알림 허브에 대해 들어오는 전체 요청|차원 없음|
|incoming.scheduled|예|전송된 예약된 푸시 알림|개수|합계|취소된 예약된 푸시 알림|차원 없음|
|incoming.scheduled.cancel|예|취소된 예약된 푸시 알림|개수|합계|취소된 예약된 푸시 알림|차원 없음|
|installation.all|예|설치 관리 작업|개수|합계|설치 관리 작업|차원 없음|
|installation.delete|예|설치 작업 삭제|개수|합계|설치 작업 삭제|차원 없음|
|installation.get|예|설치 작업 가져오기|개수|합계|설치 작업 가져오기|차원 없음|
|installation.patch|예|설치 작업 패치|개수|합계|설치 작업 패치|차원 없음|
|installation.upsert|예|설치 작업 만들기 또는 업데이트|개수|합계|설치 작업 만들기 또는 업데이트|차원 없음|
|notificationhub.pushes|예|모든 나가는 알림 수|개수|합계|알림 허브의 모든 나가는 알림 수|차원 없음|
|outgoing.allpns.badorexpiredchannel|예|잘못되거나 만료된 채널 오류|개수|합계|등록의 channel/token/registrationId가 만료되었거나 올바르지 않기 때문에 실패한 푸시의 수입니다.|차원 없음|
|outgoing.allpns.channelerror|예|채널 오류|개수|합계|채널이 잘못되었거나 제한 또는 만료된 올바른 앱과 연결되지 않았으므로 실패 한 푸시의 수입니다.|차원 없음|
|outgoing.allpns.invalidpayload|예|페이로드 오류|개수|합계|PNS가 잘못된 페이로드 오류를 반환하기 때문에 실패한 푸시의 수입니다.|차원 없음|
|outgoing.allpns.pnserror|예|외부 알림 시스템 오류|개수|합계|PNS와 통신하는 데 문제(인증 문제 제외)가 있기 때문에 실패한 푸시의 수입니다.|차원 없음|
|outgoing.allpns.success|예|성공적인 알림|개수|합계|성공한 모든 알림의 수입니다.|차원 없음|
|outgoing.apns.badchannel|예|APNS 잘못된 채널 오류|개수|합계|토큰이 잘못되어 실패한 푸시의 수입니다(APNS 상태 코드: 8).|차원 없음|
|outgoing.apns.expiredchannel|예|APNS 만료된 채널 오류|개수|합계|APNS 피드백 채널에서 무효화된 토큰의 수입니다.|차원 없음|
|outgoing.apns.invalidcredentials|예|APNS 권한 부여 오류|개수|합계|PNS가 제공된 자격 증명을 수락하지 않았거나 자격 증명이 차단되어 실패한 푸시의 수입니다.|차원 없음|
|outgoing.apns.invalidnotificationsize|예|APNS 잘못된 알림 크기 오류|개수|합계|페이로드가 너무 커서 실패한 푸시의 수입니다(APNS 상태 코드: 7).|차원 없음|
|outgoing.apns.pnserror|예|APNS 오류|개수|합계|APNS와의 통신 오류로 인해 실패한 푸시의 수입니다.|차원 없음|
|outgoing.apns.success|예|APNS 성공적인 알림|개수|합계|성공한 모든 알림의 수입니다.|차원 없음|
|outgoing.gcm.authenticationerror|예|GCM 인증 오류|개수|합계|PNS가 제공된 자격 증명을 수락하지 않았거나, 자격 증명이 차단되었거나, 앱에서 SenderId가 올바르게 구성되지 않았기 때문에 실패한 푸시의 수입니다(GCM 결과: MismatchedSenderId).|차원 없음|
|outgoing.gcm.badchannel|예|GCM 잘못된 채널 오류|개수|합계|등록의 registrationId가 인식되지 않기 때문에 실패한 푸시의 수입니다(GCM 결과: 잘못된 등록).|차원 없음|
|outgoing.gcm.expiredchannel|예|GCM 만료된 채널 오류|개수|합계|등록의 registrationId가 만료되었기 때문에 실패한 푸시의 수입니다(GCM 결과: NotRegistered).|차원 없음|
|outgoing.gcm.invalidcredentials|예|GCM 권한 부여 오류(잘못된 자격 증명)|개수|합계|PNS가 제공된 자격 증명을 수락하지 않았거나 자격 증명이 차단되어 실패한 푸시의 수입니다.|차원 없음|
|outgoing.gcm.invalidnotificationformat|예|GCM 잘못된 알림 형식|개수|합계|페이로드 형식이 올바르지 않기 때문에 실패한 푸시의 수입니다(GCM 결과: InvalidDataKey 또는 InvalidTtl).|차원 없음|
|outgoing.gcm.invalidnotificationsize|예|GCM 잘못된 알림 크기 오류|개수|합계|페이로드가 너무 커서 실패한 푸시의 수입니다(GCM 결과: MessageTooBig).|차원 없음|
|outgoing.gcm.pnserror|예|GCM 오류|개수|합계|GCM과의 통신 오류로 인해 실패한 푸시의 수입니다.|차원 없음|
|outgoing.gcm.success|예|GCM 성공적인 알림|개수|합계|성공한 모든 알림의 수입니다.|차원 없음|
|outgoing.gcm.throttled|예|GCM 제한된 알림|개수|합계|GCM이 이 앱을 제한하기 때문에 실패한 푸시의 수(GCM 상태 코드: 501-599 또는 결과: Unavailable).|차원 없음|
|outgoing.gcm.wrongchannel|예|GCM 잘못된 채널 오류|개수|합계|등록의 registrationId가 현재 앱과 연결되지 않기 때문에 실패한 푸시의 수입니다(GCM 결과: InvalidPackageName).|차원 없음|
|outgoing.mpns.authenticationerror|예|MPNS 인증 오류|개수|합계|PNS가 제공된 자격 증명을 수락하지 않았거나 자격 증명이 차단되어 실패한 푸시의 수입니다.|차원 없음|
|outgoing.mpns.badchannel|예|MPNS 잘못된 채널 오류|개수|합계|등록의 ChannelURI가 인식되지 않아 실패한 푸시의 수입니다(MPNS 상태: 404 찾을 수 없음).|차원 없음|
|outgoing.mpns.channeldisconnected|예|MPNS 채널 연결 끊김|개수|합계|등록의 ChannelURI 연결이 끊어졌으므로 실패한 푸시의 수입니다(MPNS 상태: 412 찾을 수 없음).|차원 없음|
|outgoing.mpns.dropped|예|MPNS 삭제된 알림|개수|합계|MPNS에서 삭제된 푸시의 수입니다(MPNS 응답 헤더: X-NotificationStatus: QueueFull 또는 Suppressed).|차원 없음|
|outgoing.mpns.invalidcredentials|예|MPNS 잘못된 자격 증명|개수|합계|PNS가 제공된 자격 증명을 수락하지 않았거나 자격 증명이 차단되어 실패한 푸시의 수입니다.|차원 없음|
|outgoing.mpns.invalidnotificationformat|예|MPNS 잘못된 알림 형식|개수|합계|알림의 페이로드가 너무 커서 실패한 푸시의 수입니다.|차원 없음|
|outgoing.mpns.pnserror|예|MPNS 오류|개수|합계|MPNS와의 통신 오류로 인해 실패한 푸시의 수입니다.|차원 없음|
|outgoing.mpns.success|예|MPNS 성공적인 알림|개수|합계|성공한 모든 알림의 수입니다.|차원 없음|
|outgoing.mpns.throttled|예|MPNS 제한된 알림|개수|합계|MPNS가 이 앱을 제한하기 때문에 실패한 푸시의 수입니다(WNS MPNS: 406 승인 금지).|차원 없음|
|outgoing.wns.authenticationerror|예|WNS 인증 오류|개수|합계|Windows Live와의 통신 오류(잘못된 자격 증명 또는 잘못된 토큰)로 인해 알림이 배달되지 않습니다.|차원 없음|
|outgoing.wns.badchannel|예|WNS 잘못된 채널 오류|개수|합계|등록의 ChannelURI가 인식되지 않아 실패한 푸시의 수입니다(WNS 상태: 404 찾을 수 없음).|차원 없음|
|outgoing.wns.channeldisconnected|예|WNS 채널 연결 끊김|개수|합계|등록의 ChannelURI가 제한되어 알림이 삭제되었습니다(WNS 응답 헤더: X-WNS-DeviceConnectionStatus: disconnected).|차원 없음|
|outgoing.wns.channelthrottled|예|WNS 채널 제한|개수|합계|등록의 ChannelURI가 제한되어 알림이 삭제되었습니다(WNS 응답 헤더: X-WNS-NotificationStatus:channelThrottled).|차원 없음|
|outgoing.wns.dropped|예|WNS 삭제된 알림|개수|합계|등록의 ChannelURI가 제한되어 알림이 삭제되었습니다(X-WNS-NotificationStatus: dropped but not X-WNS-DeviceConnectionStatus: disconnected).|차원 없음|
|outgoing.wns.expiredchannel|예|WNS 만료된 채널 오류|개수|합계|ChannelURI가 만료되어 실패한 푸시의 수입니다(WNS 상태: 410 없음).|차원 없음|
|outgoing.wns.invalidcredentials|예|WNS 권한 부여 오류(잘못된 자격 증명)|개수|합계|PNS가 제공된 자격 증명을 수락하지 않았거나 자격 증명이 차단되어 실패한 푸시의 수입니다. (Windows Live는 자격 증명을 인식하지 못함)|차원 없음|
|outgoing.wns.invalidnotificationformat|예|WNS 잘못된 알림 형식|개수|합계|알림의 형식이 잘못되었습니다(WNS 상태: 400). WNS가 잘못된 모든 페이로드를 거부하지는 않습니다.|차원 없음|
|outgoing.wns.invalidnotificationsize|예|WNS 잘못된 알림 크기 오류|개수|합계|알림 페이로드가 너무 큽니다(WNS 상태: 413).|차원 없음|
|outgoing.wns.invalidtoken|예|WNS 권한 부여 오류(잘못된 토큰)|개수|합계|WNS에 제공한 토큰이 잘못되었습니다(WNS 상태: 401 권한 없음).|차원 없음|
|outgoing.wns.pnserror|예|WNS 오류|개수|합계|WNS와의 통신 오류로 인해 알림이 배달되지 않습니다.|차원 없음|
|outgoing.wns.success|예|WNS 성공적인 알림|개수|합계|성공한 모든 알림의 수입니다.|차원 없음|
|outgoing.wns.throttled|예|WNS 제한된 알림|개수|합계|WNS가 이 앱을 제한하기 때문에 실패한 푸시의 수입니다(WNS 상태: 406 승인 금지).|차원 없음|
|outgoing.wns.tokenproviderunreachable|예|WNS 권한 부여 오류(연결할 수 없음)|개수|합계|Windows Live에 연결할 수 없습니다.|차원 없음|
|outgoing.wns.wrongtoken|예|WNS 권한 부여 오류(잘못된 토큰)|개수|합계|WNS에 제공된 토큰은 유효하지만 다른 애플리케이션에 대해서는 유효하지 않습니다(WNS 상태: 403 사용 권한 없음). 등록의 ChannelURI가 다른 앱에 연결된 경우 이 문제가 발생할 수 있습니다. 클라이언트 앱은 자격 증명이 알림 허브에 있는 동일한 앱과 연결되어 있는지 확인합니다.|차원 없음|
|registration.all|예|등록 작업|개수|합계|성공한 모든 등록 작업(만들기 업데이트 쿼리 및 삭제)입니다. |차원 없음|
|registration.create|예|등록 만들기 작업|개수|합계|성공한 모든 등록 만들기의 수입니다.|차원 없음|
|registration.delete|예|등록 삭제 작업|개수|합계|성공한 모든 등록 삭제의 수입니다.|차원 없음|
|registration.get|예|등록 읽기 작업|개수|합계|성공한 모든 등록 쿼리의 수입니다.|차원 없음|
|registration.update|예|등록 업데이트 작업|개수|합계|성공한 모든 등록 업데이트의 수입니다.|차원 없음|
|scheduled.pending|예|보류 중인 예약된 알림|개수|합계|보류 중인 예약된 알림|차원 없음|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|Average_% Available Memory|예|% 사용 가능한 메모리|개수|평균|Average_% Available Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Available Swap Space|예|% 사용 가능한 스왑 공간|개수|평균|Average_% Available Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Committed Bytes In Use|예|% Committed Bytes In Use|개수|평균|Average_% Committed Bytes In Use|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% DPC Time|예|% DPC 시간|개수|평균|Average_% DPC Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Inodes|예|% 사용 가능한 Inodes|개수|평균|Average_% Free Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Space|예|% 사용 가능한 공간|개수|평균|Average_% Free Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Idle Time|예|% 유휴 시간|개수|평균|Average_% Idle Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Interrupt Time|예|% 인터럽트 시간|개수|평균|Average_% Interrupt Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% IO Wait Time|예|% IO 대기 시간|개수|평균|Average_% IO Wait Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Nice Time|예|% Nice 시간|개수|평균|Average_% Nice Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Privileged Time|예|% 권한이 부여된 시간|개수|평균|Average_% Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Processor Time|예|% Processor Time|개수|평균|Average_% Processor Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Inodes|예|% 사용된 Inodes|개수|평균|Average_% Used Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Memory|예|% 사용된 메모리|개수|평균|Average_% Used Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Space|예|% 사용된 공간|개수|평균|Average_% Used Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Swap Space|예|% 사용된 스왑 공간|개수|평균|Average_% Used Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% User Time|예|% 사용자 시간|개수|평균|Average_% User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes|예|Available MBytes|개수|평균|Average_Available MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Memory|예|사용 가능한 MB 메모리|개수|평균|Average_Available MBytes Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Swap|예|사용 가능한 MB 스왑|개수|평균|Average_Available MBytes Swap|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. 디스크 초/읽기|예|평균 디스크 초/읽기|개수|평균|Average_Avg. 디스크 초/읽기|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. 디스크 초/전송|예|평균 디스크 초/전송|개수|평균|Average_Avg. 디스크 초/전송|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. 디스크 초/쓰기|예|평균 디스크 초/쓰기|개수|평균|Average_Avg. 디스크 초/쓰기|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Received/sec|예|Bytes Received/sec|개수|평균|Average_Bytes Received/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Sent/sec|예|Bytes Sent/sec|개수|평균|Average_Bytes Sent/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Total/sec|예|Bytes Total/sec|개수|평균|Average_Bytes Total/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current Disk Queue Length|예|Current Disk Queue Length|개수|평균|Average_Current Disk Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Read Bytes/sec|예|디스크 읽기 바이트/초|개수|평균|Average_Disk Read Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Reads/sec|예|디스크 읽기/초|개수|평균|Average_Disk Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|예|디스크 전송/초|개수|평균|Average_Disk Transfers/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Write Bytes/sec|예|디스크 쓰기 바이트/초|개수|평균|Average_Disk Write Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Writes/sec|예|디스크 쓰기/초|개수|평균|Average_Disk Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|예|사용 가능한 메가바이트|개수|평균|Average_Free Megabytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Physical Memory|예|사용 가능한 실제 메모리|개수|평균|Average_Free Physical Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Space in Paging Files|예|페이징 파일에 사용 가능한 공간|개수|평균|Average_Free Space in Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Virtual Memory|예|사용 가능한 가상 메모리|개수|평균|Average_Free Virtual Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical Disk Bytes/sec|예|논리 디스크 바이트/초|개수|평균|Average_Logical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Reads/sec|예|페이지 읽기/초|개수|평균|Average_Page Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Writes/sec|예|페이지 쓰기/초|개수|평균|Average_Page Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/sec|예|페이지/초|개수|평균|Average_Pages/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct Privileged Time|예|Pct 권한이 부여된 시간|개수|평균|Average_Pct Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct User Time|예|Pct 사용자 시간|개수|평균|Average_Pct User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical Disk Bytes/sec|예|물리적 디스크 바이트/초|개수|평균|Average_Physical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|예|프로세스|개수|평균|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor Queue Length|예|프로세서 큐 길이|개수|평균|Average_Processor Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size Stored In Paging Files|예|페이징 파일에 저장된 크기|개수|평균|Average_Size Stored In Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes|예|총 바이트|개수|평균|Average_Total Bytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Received|예|받은 총 바이트|개수|평균|Average_Total Bytes Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Transmitted|예|전송된 총 바이트|개수|평균|Average_Total Bytes Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Collisions|예|총 충돌|개수|평균|Average_Total Collisions|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Received|예|받은 총 패킷|개수|평균|Average_Total Packets Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Transmitted|예|전송된 총 패킷|개수|평균|Average_Total Packets Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Rx Errors|예|총 Rx 오류|개수|평균|Average_Total Rx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Tx Errors|예|총 Tx 오류|개수|평균|Average_Total Tx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|예|작동 시간|개수|평균|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used MBytes Swap Space|예|사용된 MB 스왑 공간|개수|평균|Average_Used MBytes Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory kBytes|예|사용된 메모리 KB|개수|평균|Average_Used Memory kBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory MBytes|예|사용된 메모리 MB|개수|평균|Average_Used Memory MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|예|사용자|개수|평균|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual Shared Memory|예|가상 공유 메모리|개수|평균|Average_Virtual Shared Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|이벤트|예|이벤트|개수|평균|이벤트|Source, EventLog, Computer, EventCategory, EventLevel, EventLevelName, EventID|
|하트비트|예|Heartbeat|개수|합계|하트비트|Computer, OSType, Version, SourceComputerId|
|업데이트|예|업데이트|개수|평균|업데이트|Computer, Product, Classification, UpdateState, Optional, Approved|


## <a name="microsoftpeeringpeerings"></a>Microsoft 피어 링/피어 링

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|EgressTrafficRate|예|송신 트래픽 요금|BitsPerSecond|평균|초당 송신 트래픽 전송률 (비트)|ConnectionId|
|IngressTrafficRate|예|수신 트래픽 율|BitsPerSecond|평균|수신 트래픽 전송률 (비트/초)|ConnectionId|
|SessionAvailabilityV4|예|세션 가용성 V4|백분율|평균|V4 세션의 가용성|ConnectionId|
|SessionAvailabilityV6|예|세션 가용성 V6|백분율|평균|V6 세션의 가용성|ConnectionId|


## <a name="microsoftpeeringpeeringservices"></a>Microsoft 피어 링/peeringServices

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|PrefixLatency|예|접두사 대기 시간|밀리초|평균|중앙값 전위 대기 시간|PrefixName|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|memory_metric|예|메모리|바이트|평균|메모리. A1은 0-3GB, A2는 0-5GB, A3는 0-10GB, A4는 0-25GB, A5는 0-50GB, A6는 0-100GB 범위|차원 없음|
|memory_thrashing_metric|예|메모리 쓰래싱(데이터 세트)|백분율|평균|평균 메모리 쓰래싱입니다.|차원 없음|
|qpu_high_utilization_metric|예|QPU 높은 사용률|개수|합계|지난 1분 간의 QPU 높은 사용률로, QPU 사용률이 높으면 1, 그렇지 않으면 0|차원 없음|
|QueryDuration|예|쿼리 기간(데이터 세트)|밀리초|평균|마지막 간격의 DAX 쿼리 기간|차원 없음|
|QueryPoolJobQueueLength|예|쿼리 풀 작업 큐 길이(데이터 세트)|개수|평균|쿼리 스레드 풀의 큐에 있는 작업 수입니다.|차원 없음|


## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ActiveConnections|아니요|ActiveConnections|개수|합계|Microsoft.Relay의 총 ActiveConnections.|EntityName|
|ActiveListeners|아니요|ActiveListeners|개수|합계|Microsoft.Relay의 총 ActiveListeners.|EntityName|
|BytesTransferred|예|BytesTransferred|개수|합계|Microsoft.Relay의 총 BytesTransferred.|EntityName|
|ListenerConnections-ClientError|아니요|ListenerConnections-ClientError|개수|합계|Microsoft.Relay의 총 ListenerConnections에서의 ClientError.|EntityName, |
|ListenerConnections-ServerError|아니요|ListenerConnections-ServerError|개수|합계|Microsoft.Relay의 ListenerConnections에서의 ServerError.|EntityName, |
|ListenerConnections-Success|아니요|ListenerConnections-Success|개수|합계|Microsoft.Relay의 성공적인 ListenerConnections.|EntityName, |
|ListenerConnections-TotalRequests|아니요|ListenerConnections-TotalRequests|개수|합계|Microsoft.Relay의 총 ListenerConnections.|EntityName|
|ListenerDisconnects|아니요|ListenerDisconnects|개수|합계|Microsoft.Relay의 총 ListenerDisconnects.|EntityName|
|SenderConnections-ClientError|아니요|SenderConnections-ClientError|개수|합계|Microsoft.Relay의 SenderConnections에서 ClientError.|EntityName, |
|SenderConnections-ServerError|아니요|SenderConnections-ServerError|개수|합계|Microsoft.Relay의 SenderConnections에서의 ServerError.|EntityName, |
|SenderConnections-Success|아니요|SenderConnections-Success|개수|합계|Microsoft.Relay의 성공적인 SenderConnections.|EntityName, |
|SenderConnections-TotalRequests|아니요|SenderConnections-TotalRequests|개수|합계|Microsoft.Relay의 총 SenderConnections 요청.|EntityName|
|SenderDisconnects|아니요|SenderDisconnects|개수|합계|Microsoft.Relay의 총 SenderDisconnects.|EntityName|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|SearchLatency|예|검색 대기 시간|초|평균|검색 서비스에 대한 평균 검색 대기 시간|차원 없음|
|SearchQueriesPerSecond|예|초당 검색 쿼리 수|초당 개수|평균|Search 서비스에 대한 초당 검색 쿼리|차원 없음|
|ThrottledSearchQueriesPercentage|예|제한된 검색 쿼리 백분율|백분율|평균|검색 서비스에 대해 제한된 검색 쿼리의 백분율|차원 없음|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ActiveConnections|아니요|ActiveConnections|개수|합계|Microsoft.ServiceBus에 대한 총 활성 연결.|차원 없음|
|ActiveMessages|아니요|큐/토픽에 있는 활성 메시지 수|개수|평균|큐/토픽에 있는 활성 메시지 수|EntityName|
|ConnectionsClosed|아니요|끊어진 연결.|개수|평균|Microsoft.ServiceBus에 대한 끊어진 연결.|EntityName|
|ConnectionsOpened|아니요|열린 연결.|개수|평균|Microsoft.ServiceBus에 대한 열린 연결.|EntityName|
|CPUXNS|아니요|CPU(사용되지 않음)|백분율|최대|Service bus 프리미엄 네임 스페이스 CPU 사용량 메트릭 이 메트릭은 depricated입니다. 대신 CPU 메트릭 (NamespaceCpuUsage)을 사용 하세요.|차원 없음|
|DeadletteredMessages|아니요|큐/토픽에서 배달 못한 메시지 수입니다.|개수|평균|큐/토픽에서 배달 못한 메시지 수입니다.|EntityName|
|IncomingMessages|예|들어오는 메시지|개수|합계|Microsoft.ServiceBus에 대한 들어오는 메시지.|EntityName|
|IncomingRequests|예|들어오는 요청|개수|합계|Microsoft.ServiceBus에 대한 들어오는 요청.|EntityName|
|메시지|아니요|큐/토픽에 있는 메시지 수|개수|평균|큐/토픽에 있는 메시지 수|EntityName|
|NamespaceCpuUsage|아니요|CPU|백분율|최대|Service bus 프리미엄 네임 스페이스 CPU 사용량 메트릭|차원 없음|
|NamespaceMemoryUsage|아니요|메모리 사용량|백분율|최대|서비스 버스 프리미엄 네임 스페이스 메모리 사용량 메트릭입니다.|차원 없음|
|OutgoingMessages|예|보내는 메시지|개수|합계|Microsoft.ServiceBus에 대한 보내는 메시지.|EntityName|
|ScheduledMessages|아니요|큐/토픽에서 예약된 메시지 수입니다.|개수|평균|큐/토픽에서 예약된 메시지 수입니다.|EntityName|
|ServerErrors|아니요|서버 오류.|개수|합계|Microsoft.ServiceBus에 대한 서버 오류.|EntityName, |
|크기|아니요|크기|바이트|평균|큐/토픽 크기(바이트)|EntityName|
|SuccessfulRequests|아니요|성공한 요청|개수|합계|네임스페이스에 대한 총 성공한 요청|EntityName, |
|ThrottledRequests|아니요|제한된 요청.|개수|합계|Microsoft.ServiceBus에 대한 제한된 요청.|EntityName, |
|UserErrors|아니요|사용자 오류.|개수|합계|Microsoft.ServiceBus에 대한 사용자 오류.|EntityName, |
|WSXNS|아니요|메모리 사용량(사용되지 않음)|백분율|최대|서비스 버스 프리미엄 네임 스페이스 메모리 사용량 메트릭입니다. 이 메트릭은 사용되지 않습니다. 대신 Memory Usage (NamespaceMemoryUsage) 메트릭을 사용 하세요.|차원 없음|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ActualCpu|아니요|ActualCpu|개수|평균|밀리초 코어의 실제 CPU 사용량|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|아니요|ActualMemory|바이트|평균|실제 메모리 사용량 (MB)|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedCpu|아니요|AllocatedCpu|개수|평균|이 컨테이너에 할당 된 Cpu (밀리초 코어)|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedMemory|아니요|AllocatedMemory|바이트|평균|이 컨테이너에 할당 된 메모리 (MB)|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|아니요|ApplicationStatus|개수|평균|Service Fabric 메시 응용 프로그램의 상태|ApplicationName, 상태|
|ContainerStatus|아니요|ContainerStatus|개수|평균|Service Fabric 메시 응용 프로그램의 컨테이너 상태|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, Status|
|CpuUtilization|아니요|CpuUtilization|백분율|평균|이 컨테이너의 CPU 사용률 (%)은 AllocatedCpu의 백분율입니다.|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|MemoryUtilization|아니요|MemoryUtilization|백분율|평균|이 컨테이너의 CPU 사용률 (%)은 AllocatedCpu의 백분율입니다.|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|RestartCount|아니요|RestartCount|개수|평균|Service Fabric 메시 응용 프로그램에서 컨테이너의 다시 시작 횟수|ApplicationName, Status, ServiceName, ServiceReplicaName, CodePackageName|
|ServiceReplicaStatus|아니요|ServiceReplicaStatus|개수|평균|Service Fabric 메시 응용 프로그램의 서비스 복제본 상태|ApplicationName, Status, ServiceName, ServiceReplicaName|
|ServiceStatus|아니요|ServiceStatus|개수|평균|Service Fabric 메시 응용 프로그램의 서비스 상태|ApplicationName, Status, ServiceName|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ConnectionCount|예|연결 수|개수|최대|사용자 연결의 양.|엔드포인트|
|InboundTraffic|예|인바운드 트래픽|바이트|합계|서비스의 인바운드 트래픽|차원 없음|
|MessageCount|예|메시지 수|개수|합계|총 메시지 양|차원 없음|
|OutboundTraffic|예|아웃바운드 트래픽|바이트|합계|서비스의 아웃바운드 트래픽|차원 없음|
|SystemErrors|예|시스템 오류|백분율|최대|시스템 오류의 비율|차원 없음|
|UserErrors|예|사용자 오류|백분율|최대|사용자 오류의 비율|차원 없음|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|avg_cpu_percent|예|평균 CPU 비율|백분율|평균|평균 CPU 비율|차원 없음|
|io_bytes_read|예|읽은 IO 바이트|바이트|평균|읽은 IO 바이트|차원 없음|
|io_bytes_written|예|기록된 IO 바이트|바이트|평균|기록된 IO 바이트|차원 없음|
|io_requests|예|IO 요청 수|개수|평균|IO 요청 수|차원 없음|
|reserved_storage_mb|예|예약된 스토리지 공간|개수|평균|예약된 스토리지 공간|차원 없음|
|storage_space_used_mb|예|사용된 스토리지 공간|개수|평균|사용된 스토리지 공간|차원 없음|
|virtual_core_count|예|가상 코어 수|개수|평균|가상 코어 수|차원 없음|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|active_queries|예|활성 쿼리|개수|합계|모든 작업 그룹에 걸쳐 활성 쿼리 데이터 웨어하우스에만 적용 됩니다.|차원 없음|
|allocated_data_storage|예|할당된 데이터 공간|바이트|평균|할당 된 데이터 저장소입니다. 데이터 웨어하우스에는 적용 되지 않습니다.|차원 없음|
|app_cpu_billed|예|청구된 앱 CPU|개수|합계|청구 되는 앱 CPU입니다. 서버를 사용 하지 않는 데이터베이스에 적용 됩니다.|차원 없음|
|app_cpu_percent|예|앱 CPU 백분율|백분율|평균|앱 CPU 비율입니다. 서버를 사용 하지 않는 데이터베이스에 적용 됩니다.|차원 없음|
|app_memory_percent|예|앱 메모리 비율|백분율|평균|앱 메모리 비율입니다. 서버를 사용 하지 않는 데이터베이스에 적용 됩니다.|차원 없음|
|base_blob_size_bytes|예|기본 blob 저장소 크기|바이트|최대|기본 blob 저장소 크기입니다. Hyperscale 데이터베이스에 적용 됩니다.|차원 없음|
|blocked_by_firewall|예|방화벽에 의해 차단|개수|합계|방화벽에 의해 차단|차원 없음|
|cache_hit_percent|예|캐시 적중 비율|백분율|최대|캐시 적중 비율입니다. 데이터 웨어하우스에만 적용 됩니다.|차원 없음|
|cache_used_percent|예|캐시 사용 비율|백분율|최대|캐시 사용 백분율입니다. 데이터 웨어하우스에만 적용 됩니다.|차원 없음|
|connection_failed|예|실패한 연결|개수|합계|실패한 연결|차원 없음|
|connection_successful|예|성공적인 연결|개수|합계|성공적인 연결|차원 없음|
|cpu_limit|예|CPU 제한|개수|평균|CPU 제한. VCore 기반 데이터베이스에 적용 됩니다.|차원 없음|
|cpu_percent|예|CPU 비율|백분율|평균|CPU 비율|차원 없음|
|cpu_used|예|사용된 CPU|개수|평균|사용 되는 CPU입니다. VCore 기반 데이터베이스에 적용 됩니다.|차원 없음|
|교착 상태|예|교착 상태|개수|합계|상태가. 데이터 웨어하우스에는 적용 되지 않습니다.|차원 없음|
|diff_backup_size_bytes|예|차등 백업 저장소 크기|바이트|최대|누적 차등 백업 저장소 크기입니다. VCore 기반 데이터베이스에 적용 됩니다. Hyperscale 데이터베이스에는 적용 되지 않습니다.|차원 없음|
|dtu_consumption_percent|예|DTU 비율|백분율|평균|DTU 백분율. DTU 기반 데이터베이스에 적용 됩니다.|차원 없음|
|dtu_limit|예|DTU 제한|개수|평균|DTU 한도입니다. DTU 기반 데이터베이스에 적용 됩니다.|차원 없음|
|dtu_used|예|DTU 사용됨|개수|평균|DTU를 사용 했습니다. DTU 기반 데이터베이스에 적용 됩니다.|차원 없음|
|dw_backup_size_gb|예|데이터 스토리지 크기(GB)|개수|합계|데이터 저장소 크기는 데이터의 크기 및 트랜잭션 로그로 구성 됩니다. 메트릭은 청구서의 ' 저장소 ' 부분으로 계산 됩니다. 데이터 웨어하우스에만 적용 됩니다.|차원 없음|
|dw_geosnapshot_size_gb|예|재해 복구 저장소 크기 (GB)|개수|합계|재해 복구 저장소 크기는 청구서에 ' 재해 복구 저장소 '로 반영 됩니다. 데이터 웨어하우스에만 적용 됩니다.|차원 없음|
|dw_snapshot_size_gb|예|스냅숏 저장소 크기 (GB)|개수|합계|스냅숏 저장소 크기는 사용자 정의 및 자동 복원 점수를 만들기 위해 스냅숏에서 캡처한 증분 변경 내용의 크기입니다. 메트릭은 청구서의 ' 저장소 ' 부분으로 계산 됩니다. 데이터 웨어하우스에만 적용 됩니다.|차원 없음|
|dwu_consumption_percent|예|DWU 백분율|백분율|최대|DWU 백분율. 데이터 웨어하우스에만 적용 됩니다.|차원 없음|
|dwu_limit|예|DWU 제한|개수|최대|DWU 제한. 데이터 웨어하우스에만 적용 됩니다.|차원 없음|
|dwu_used|예|DWU 사용됨|개수|최대|DWU를 사용 했습니다. 데이터 웨어하우스에만 적용 됩니다.|차원 없음|
|full_backup_size_bytes|예|전체 백업 저장소 크기|바이트|최대|누적 전체 백업 저장소 크기입니다. VCore 기반 데이터베이스에 적용 됩니다. Hyperscale 데이터베이스에는 적용 되지 않습니다.|차원 없음|
|local_tempdb_usage_percent|예|로컬 tempdb 백분율|백분율|평균|로컬 tempdb 비율입니다. 데이터 웨어하우스에만 적용 됩니다.|차원 없음|
|log_backup_size_bytes|예|로그 백업 저장소 크기|바이트|최대|누적 로그 백업 저장소 크기입니다. VCore 기반 및 Hyperscale 데이터베이스에 적용 됩니다.|차원 없음|
|log_write_percent|예|로그 IO 비율|백분율|평균|로그 IO 백분율입니다. 데이터 웨어하우스에는 적용 되지 않습니다.|차원 없음|
|memory_usage_percent|예|메모리 백분율|백분율|최대|메모리 비율입니다. 데이터 웨어하우스에만 적용 됩니다.|차원 없음|
|physical_data_read_percent|예|데이터 IO 비율|백분율|평균|데이터 IO 비율|차원 없음|
|queued_queries|예|대기 중인 쿼리|개수|합계|모든 작업 그룹의 대기 중인 쿼리 데이터 웨어하우스에만 적용 됩니다.|차원 없음|
|sessions_percent|예|세션 백분율|백분율|평균|세션 백분율. 데이터 웨어하우스에는 적용 되지 않습니다.|차원 없음|
|snapshot_backup_size_bytes|예|스냅숏 백업 저장소 크기|바이트|최대|누적 스냅숏 백업 저장소 크기입니다. Hyperscale 데이터베이스에 적용 됩니다.|차원 없음|
|sqlserver_process_core_percent|예|SQL Server 프로세스 코어 백분율|백분율|최대|SQL DB 프로세스의 백분율로 나타낸 CPU 사용량입니다. 데이터 웨어하우스에는 적용 되지 않습니다.|차원 없음|
|sqlserver_process_memory_percent|예|SQL Server 프로세스 메모리 비율|백분율|최대|SQL DB 프로세스의 백분율로 나타낸 메모리 사용량입니다. 데이터 웨어하우스에는 적용 되지 않습니다.|차원 없음|
|스토리지|예|사용된 데이터 공간|바이트|최대|사용 되는 데이터 공간. 데이터 웨어하우스에는 적용 되지 않습니다.|차원 없음|
|storage_percent|예|사용된 데이터 공간 백분율|백분율|최대|사용 되는 데이터 공간 (%)입니다. 데이터 웨어하우스 또는 대규모 데이터베이스에는 적용 되지 않습니다.|차원 없음|
|tempdb_data_size|예|Tempdb 데이터 파일 크기(KB)|개수|최대|Tempdb 데이터 파일 크기 (Kb)입니다. 데이터 웨어하우스에는 적용 되지 않습니다.|차원 없음|
|tempdb_log_size|예|Tempdb 로그 파일 크기(KB)|개수|최대|Tempdb 로그 파일 크기 (Kb)입니다. 데이터 웨어하우스에는 적용 되지 않습니다.|차원 없음|
|tempdb_log_used_percent|예|로그가 사용된 Tempdb 백분율|백분율|최대|Tempdb 백분율 로그가 사용 되었습니다. 데이터 웨어하우스에는 적용 되지 않습니다.|차원 없음|
|wlg_active_queries|예|작업 그룹 활성 쿼리|개수|합계|작업 그룹 내의 활성 쿼리입니다. 데이터 웨어하우스에만 적용 됩니다.|WorkloadGroupName, IsUserDefined|
|wlg_active_queries_timeouts|예|작업 그룹 쿼리 시간 제한|개수|합계|작업 그룹에 대해 시간이 초과 된 쿼리입니다. 데이터 웨어하우스에만 적용 됩니다.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_system_percent|예|시스템 비율별 작업 그룹 할당|백분율|최대|작업 그룹당 전체 시스템을 기준으로 할당 된 리소스 비율입니다. 데이터 웨어하우스에만 적용 됩니다.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|예|Cap 리소스 percent 별 작업 그룹 할당|백분율|최대|작업 그룹당 지정 된 cap 리소스를 기준으로 리소스의 할당 된 비율입니다. 데이터 웨어하우스에만 적용 됩니다.|WorkloadGroupName, IsUserDefined|
|wlg_effective_cap_resource_percent|예|유효 상한 리소스 비율|백분율|최대|작업 그룹에 대해 허용 되는 리소스의 백분율에 대 한 하드 한도는 다른 작업 그룹에 할당 된 유효 최소 리소스 비율을 고려 합니다. 데이터 웨어하우스에만 적용 됩니다.|WorkloadGroupName, IsUserDefined|
|wlg_effective_min_resource_percent|예|유효 최소 리소스 비율|백분율|최대|작업 그룹에 대해 예약 되 고 격리 된 최소 리소스 비율 (서비스 수준 최소)을 고려 합니다. 데이터 웨어하우스에만 적용 됩니다.|WorkloadGroupName, IsUserDefined|
|wlg_queued_queries|예|작업 그룹 큐에 대기 중인 쿼리|개수|합계|작업 그룹 내에서 대기 중인 쿼리 데이터 웨어하우스에만 적용 됩니다.|WorkloadGroupName, IsUserDefined|
|workers_percent|예|작업자 백분율|백분율|평균|작업자 비율. 데이터 웨어하우스에는 적용 되지 않습니다.|차원 없음|
|xtp_storage_percent|예|메모리 내 OLTP 스토리지 백분율|백분율|평균|메모리 내 OLTP 저장소 백분율입니다. 데이터 웨어하우스에는 적용 되지 않습니다.|차원 없음|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|allocated_data_storage|예|할당된 데이터 공간|바이트|평균|할당된 데이터 공간|차원 없음|
|allocated_data_storage_percent|예|데이터 공간 할당 백분율|백분율|최대|데이터 공간 할당 백분율|차원 없음|
|cpu_limit|예|CPU 제한|개수|평균|CPU 제한. VCore 기반 탄력적 풀에 적용 됩니다.|차원 없음|
|cpu_percent|예|CPU 비율|백분율|평균|CPU 비율|차원 없음|
|cpu_used|예|사용된 CPU|개수|평균|사용 되는 CPU입니다. VCore 기반 탄력적 풀에 적용 됩니다.|차원 없음|
|database_allocated_data_storage|아니요|할당된 데이터 공간|바이트|평균|할당된 데이터 공간|DatabaseResourceId|
|database_cpu_limit|아니요|CPU 제한|개수|평균|CPU 제한|DatabaseResourceId|
|database_cpu_percent|아니요|CPU 비율|백분율|평균|CPU 비율|DatabaseResourceId|
|database_cpu_used|아니요|사용된 CPU|개수|평균|사용된 CPU|DatabaseResourceId|
|database_dtu_consumption_percent|아니요|DTU 비율|백분율|평균|DTU 비율|DatabaseResourceId|
|database_eDTU_used|아니요|eDTU 사용|개수|평균|eDTU 사용|DatabaseResourceId|
|database_log_write_percent|아니요|로그 IO 비율|백분율|평균|로그 IO 비율|DatabaseResourceId|
|database_physical_data_read_percent|아니요|데이터 IO 비율|백분율|평균|데이터 IO 비율|DatabaseResourceId|
|database_sessions_percent|아니요|세션 백분율|백분율|평균|세션 백분율|DatabaseResourceId|
|database_storage_used|아니요|사용된 데이터 공간|바이트|평균|사용된 데이터 공간|DatabaseResourceId|
|database_workers_percent|아니요|작업자 백분율|백분율|평균|작업자 백분율|DatabaseResourceId|
|dtu_consumption_percent|예|DTU 비율|백분율|평균|DTU 백분율. DTU 기반 탄력적 풀에 적용 됩니다.|차원 없음|
|eDTU_limit|예|eDTU 제한|개수|평균|eDTU 제한. DTU 기반 탄력적 풀에 적용 됩니다.|차원 없음|
|eDTU_used|예|eDTU 사용|개수|평균|eDTU를 사용 했습니다. DTU 기반 탄력적 풀에 적용 됩니다.|차원 없음|
|log_write_percent|예|로그 IO 비율|백분율|평균|로그 IO 비율|차원 없음|
|physical_data_read_percent|예|데이터 IO 비율|백분율|평균|데이터 IO 비율|차원 없음|
|sessions_percent|예|세션 백분율|백분율|평균|세션 백분율|차원 없음|
|sqlserver_process_core_percent|예|SQL Server 프로세스 코어 백분율|백분율|최대|SQL DB 프로세스의 백분율로 나타낸 CPU 사용량입니다. 탄력적 풀에 적용 됩니다.|차원 없음|
|sqlserver_process_memory_percent|예|SQL Server 프로세스 메모리 비율|백분율|최대|SQL DB 프로세스의 백분율로 나타낸 메모리 사용량입니다. 탄력적 풀에 적용 됩니다.|차원 없음|
|storage_limit|예|데이터 최대 크기|바이트|평균|데이터 최대 크기|차원 없음|
|storage_percent|예|사용된 데이터 공간 백분율|백분율|평균|사용된 데이터 공간 백분율|차원 없음|
|storage_used|예|사용된 데이터 공간|바이트|평균|사용된 데이터 공간|차원 없음|
|tempdb_data_size|예|Tempdb 데이터 파일 크기(KB)|개수|최대|Tempdb 데이터 파일 크기(KB)|차원 없음|
|tempdb_log_size|예|Tempdb 로그 파일 크기(KB)|개수|최대|Tempdb 로그 파일 크기(KB)|차원 없음|
|tempdb_log_used_percent|예|로그가 사용된 Tempdb 백분율|백분율|최대|로그가 사용된 Tempdb 백분율|차원 없음|
|workers_percent|예|작업자 백분율|백분율|평균|작업자 백분율|차원 없음|
|xtp_storage_percent|예|메모리 내 OLTP 스토리지 백분율|백분율|평균|메모리 내 OLTP 스토리지 백분율|차원 없음|


## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|사용 가능성|예|가용성|백분율|평균|스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|GeoType, ApiName, Authentication|
|송신|예|송신|바이트|합계|송신 데이터 양입니다. 이 수는 Azure Storage에서 외부 클라이언트로의 송신 및 Azure 내에서의 송신을 포함 합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|GeoType, ApiName, Authentication|
|수신|예|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|예|성공 E2E 대기 시간|밀리초|평균|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 성공적인 요청의 평균 엔드투엔드 대기 시간(밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|GeoType, ApiName, Authentication|
|SuccessServerLatency|예|성공 서버 대기 시간|밀리초|평균|Azure Storage에서 성공적인 요청을 처리하는 데 사용한 평균 시간입니다. 이 값은 SuccessE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|GeoType, ApiName, Authentication|
|트랜잭션|예|트랜잭션|개수|합계|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|ResponseType, GeoType, ApiName, Authentication|
|UsedCapacity|아니요|사용된 용량|바이트|평균|스토리지 계정에서 사용한 스토리지 양입니다. 표준 스토리지 계정의 경우 이는 Blob, 테이블, 파일 및 큐에서 사용한 용량의 합계입니다. 프리미엄 저장소 계정 및 Blob 저장소 계정의 경우 BlobCapacity 또는 FileCapacity와 동일 합니다.|차원 없음|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|사용 가능성|예|가용성|백분율|평균|스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|GeoType, ApiName, Authentication|
|BlobCapacity|아니요|Blob 용량|바이트|평균|스토리지 계정의 Blob service가 사용하는 스토리지의 양(바이트)입니다.|BlobType, 계층|
|BlobCount|아니요|Blob 수|개수|평균|스토리지 계정에 저장된 Blob 개체 수입니다.|BlobType, 계층|
|BlobProvisionedSize|아니요|Blob 프로 비전 된 크기|바이트|평균|저장소 계정의 Blob service에서 프로 비전 된 저장소의 크기 (바이트)입니다.|BlobType, 계층|
|ContainerCount|예|Blob 컨테이너 수|개수|평균|스토리지 계정의 컨테이너 수입니다.|차원 없음|
|송신|예|송신|바이트|합계|송신 데이터 양입니다. 이 수는 Azure Storage에서 외부 클라이언트로의 송신 및 Azure 내에서의 송신을 포함 합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|GeoType, ApiName, Authentication|
|IndexCapacity|아니요|인덱스 용량|바이트|평균|Azure Data Lake Storage Gen2 계층 구조 인덱스에서 사용 하는 저장소의 양입니다.|차원 없음|
|수신|예|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|예|성공 E2E 대기 시간|밀리초|평균|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 성공적인 요청의 평균 엔드투엔드 대기 시간(밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|GeoType, ApiName, Authentication|
|SuccessServerLatency|예|성공 서버 대기 시간|밀리초|평균|Azure Storage에서 성공적인 요청을 처리하는 데 사용한 평균 시간입니다. 이 값은 SuccessE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|GeoType, ApiName, Authentication|
|트랜잭션|예|트랜잭션|개수|합계|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|사용 가능성|예|가용성|백분율|평균|스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|GeoType, ApiName, Authentication, 파일 공유|
|송신|예|송신|바이트|합계|송신 데이터 양입니다. 이 수는 Azure Storage에서 외부 클라이언트로의 송신 및 Azure 내에서의 송신을 포함 합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|GeoType, ApiName, Authentication, 파일 공유|
|FileCapacity|아니요|파일 용량|바이트|평균|스토리지 계정에 사용한 File Storage 양입니다.|FileShare|
|FileCount|아니요|파일 수|개수|평균|스토리지 계정의 파일 수입니다.|FileShare|
|FileShareCapacityQuota|아니요|파일 공유 용량 할당량|바이트|평균|Azure Files 서비스에서 사용할 수 있는 저장소 크기의 상한 (바이트)입니다.|FileShare|
|FileShareCount|아니요|파일 공유 수|개수|평균|스토리지 계정의 파일 공유 수입니다.|차원 없음|
|FileShareProvisionedIOPS|아니요|파일 공유의 프로 비전 된 IOPS|바이트|평균|Premium 파일 저장소 계정의 프리미엄 파일 공유에 대해 프로 비전 된 IOPS의 기준 번호입니다. 이 숫자는 공유 용량의 프로 비전 된 크기 (할당량)를 기반으로 계산 됩니다.|FileShare|
|FileShareSnapshotCount|아니요|파일 공유 스냅샷 개수|개수|평균|저장소 계정의 파일 서비스에서 공유에 있는 스냅숏의 수입니다.|FileShare|
|FileShareSnapshotSize|아니요|파일 공유 스냅샷 크기|바이트|평균|저장소 계정의 파일 서비스에서 스냅숏에서 사용 되는 저장소의 크기 (바이트)입니다.|FileShare|
|수신|예|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|GeoType, ApiName, Authentication, 파일 공유|
|SuccessE2ELatency|예|성공 E2E 대기 시간|밀리초|평균|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 성공적인 요청의 평균 엔드투엔드 대기 시간(밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|GeoType, ApiName, Authentication, 파일 공유|
|SuccessServerLatency|예|성공 서버 대기 시간|밀리초|평균|Azure Storage에서 성공적인 요청을 처리하는 데 사용한 평균 시간입니다. 이 값은 SuccessE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|GeoType, ApiName, Authentication, 파일 공유|
|트랜잭션|예|트랜잭션|개수|합계|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|ResponseType, GeoType, ApiName, Authentication, 파일 공유|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|사용 가능성|예|가용성|백분율|평균|스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|GeoType, ApiName, Authentication|
|송신|예|송신|바이트|합계|송신 데이터 양입니다. 이 수는 Azure Storage에서 외부 클라이언트로의 송신 및 Azure 내에서의 송신을 포함 합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|GeoType, ApiName, Authentication|
|수신|예|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|GeoType, ApiName, Authentication|
|QueueCapacity|예|큐 용량|바이트|평균|스토리지 계정에 사용한 Queue Storage 양입니다.|차원 없음|
|QueueCount|예|큐 수|개수|평균|스토리지 계정의 큐 수입니다.|차원 없음|
|QueueMessageCount|예|큐 메시지 수|개수|평균|스토리지 계정의 만료되지 않은 큐 메시지 수입니다.|차원 없음|
|SuccessE2ELatency|예|성공 E2E 대기 시간|밀리초|평균|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 성공적인 요청의 평균 엔드투엔드 대기 시간(밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|GeoType, ApiName, Authentication|
|SuccessServerLatency|예|성공 서버 대기 시간|밀리초|평균|Azure Storage에서 성공적인 요청을 처리하는 데 사용한 평균 시간입니다. 이 값은 SuccessE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|GeoType, ApiName, Authentication|
|트랜잭션|예|트랜잭션|개수|합계|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|사용 가능성|예|가용성|백분율|평균|스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|GeoType, ApiName, Authentication|
|송신|예|송신|바이트|합계|송신 데이터 양입니다. 이 수는 Azure Storage에서 외부 클라이언트로의 송신 및 Azure 내에서의 송신을 포함 합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|GeoType, ApiName, Authentication|
|수신|예|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|예|성공 E2E 대기 시간|밀리초|평균|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 성공적인 요청의 평균 엔드투엔드 대기 시간(밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|GeoType, ApiName, Authentication|
|SuccessServerLatency|예|성공 서버 대기 시간|밀리초|평균|Azure Storage에서 성공적인 요청을 처리하는 데 사용한 평균 시간입니다. 이 값은 SuccessE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|GeoType, ApiName, Authentication|
|TableCapacity|예|테이블 용량|바이트|평균|스토리지 계정에 사용한 Table Storage 양입니다.|차원 없음|
|TableCount|예|테이블 수|개수|평균|스토리지 계정의 테이블 수입니다.|차원 없음|
|TableEntityCount|예|테이블 엔터티 수|개수|평균|스토리지 계정의 테이블 엔터티 수입니다.|차원 없음|
|트랜잭션|예|트랜잭션|개수|합계|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ServerSyncSessionResult|예|동기화 세션 결과|개수|평균|서버 끝점이 클라우드 끝점과 동기화 세션을 성공적으로 완료할 때마다 1 값을 기록 하는 메트릭입니다.|SyncGroupName, ServerEndpointName, Syncgroupname|
|StorageSyncBatchTransferredFileBytes|예|동기화되는 바이트 수|바이트|합계|동기화 세션에 대해 전송 된 총 파일 크기|SyncGroupName, ServerEndpointName, Syncgroupname|
|StorageSyncRecalledNetworkBytesByApplication|예|애플리케이션별 클라우드 계층화 회수 크기|바이트|합계|응용 프로그램에서 회수 한 데이터 크기|SyncGroupName, ServerName, ApplicationName|
|StorageSyncRecalledTotalNetworkBytes|예|클라우드 계층화 회수 크기|바이트|합계|회수되는 데이터 크기|SyncGroupName, ServerName|
|StorageSyncRecallIOTotalSizeBytes|예|클라우드 계층화 회수|바이트|합계|서버에서 회수 한 데이터의 총 크기|ServerName|
|StorageSyncRecallThroughputBytesPerSecond|예|클라우드 계층화 회수 처리량|초당 바이트 수|평균|데이터 회수 처리량 크기|SyncGroupName, ServerName|
|StorageSyncServerHeartbeat|예|서버 온라인 상태|개수|최대|Resigtered 서버가 클라우드 끝점과 하트 비트를 성공적으로 기록할 때마다 값 1을 기록 하는 메트릭입니다.|ServerName|
|StorageSyncSyncSessionAppliedFilesCount|예|동기화된 파일 수|개수|합계|동기화 된 파일 수|SyncGroupName, ServerEndpointName, Syncgroupname|
|StorageSyncSyncSessionPerItemErrorsCount|예|동기화 상태가 아닌 파일|개수|합계|동기화 하지 못한 파일 수|SyncGroupName, ServerEndpointName, Syncgroupname|


## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>microsoft.storagesync/storageSyncServices/registeredServers

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ServerHeartbeat|예|서버 온라인 상태|개수|최대|Resigtered 서버가 클라우드 끝점과 하트 비트를 성공적으로 기록할 때마다 값 1을 기록 하는 메트릭입니다.|ServerResourceId, ServerName|
|ServerRecallIOTotalSizeBytes|예|클라우드 계층화 회수|바이트|합계|서버에서 회수 한 데이터의 총 크기|ServerResourceId, ServerName|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>microsoft.storagesync/storageSyncServices/syncGroups

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|SyncGroupBatchTransferredFileBytes|예|동기화되는 바이트 수|바이트|합계|동기화 세션에 대해 전송 된 총 파일 크기|SyncGroupName, ServerEndpointName, Syncgroupname|
|SyncGroupSyncSessionAppliedFilesCount|예|동기화된 파일 수|개수|합계|동기화 된 파일 수|SyncGroupName, ServerEndpointName, Syncgroupname|
|SyncGroupSyncSessionPerItemErrorsCount|예|동기화 상태가 아닌 파일|개수|합계|동기화 하지 못한 파일 수|SyncGroupName, ServerEndpointName, Syncgroupname|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ServerEndpointBatchTransferredFileBytes|예|동기화되는 바이트 수|바이트|합계|동기화 세션에 대해 전송 된 총 파일 크기|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionAppliedFilesCount|예|동기화된 파일 수|개수|합계|동기화 된 파일 수|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|예|동기화 상태가 아닌 파일|개수|합계|동기화 하지 못한 파일 수|ServerEndpointName, SyncDirection|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|AMLCalloutFailedRequests|예|실패한 기능 요청|개수|합계|실패한 기능 요청|LogicalName, PartitionId|
|AMLCalloutInputEvents|예|함수 이벤트|개수|합계|함수 이벤트|LogicalName, PartitionId|
|AMLCalloutRequests|예|기능 요청|개수|합계|기능 요청|LogicalName, PartitionId|
|ConversionErrors|예|데이터 변환 오류|개수|합계|데이터 변환 오류|LogicalName, PartitionId|
|DeserializationError|예|입력 역직렬화 오류|개수|합계|입력 역직렬화 오류|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|예|잘못된 이벤트|개수|합계|잘못된 이벤트|LogicalName, PartitionId|
|EarlyInputEvents|예|초기 입력 이벤트|개수|합계|초기 입력 이벤트|LogicalName, PartitionId|
|오류|예|런타임 오류|개수|합계|런타임 오류|LogicalName, PartitionId|
|InputEventBytes|예|입력 이벤트 바이트|바이트|합계|입력 이벤트 바이트|LogicalName, PartitionId|
|InputEvents|예|입력 이벤트|개수|합계|입력 이벤트|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|예|백로그된 입력 이벤트|개수|최대|백로그된 입력 이벤트|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|예|수신된 입력 원본|개수|합계|수신된 입력 원본|LogicalName, PartitionId|
|LateInputEvents|예|늦은 입력 이벤트|개수|합계|늦은 입력 이벤트|LogicalName, PartitionId|
|OutputEvents|예|출력 이벤트|개수|합계|출력 이벤트|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|예|워터마크 지연|초|최대|워터마크 지연|LogicalName, PartitionId|
|ResourceUtilization|예|SU % 사용률|백분율|최대|SU % 사용률|LogicalName, PartitionId|


## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/workspaces

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|OrchestrationActivityRunsEnded|아니요|작업 실행 종료 됨|개수|합계|성공, 실패 또는 취소 된 오케스트레이션 작업 수|Result, FailureType, Activity, ActivityType, 파이프라인|
|OrchestrationPipelineRunsEnded|아니요|파이프라인 실행이 종료 되었습니다.|개수|합계|성공, 실패 또는 취소 된 오케스트레이션 파이프라인 실행 수|결과, FailureType, 파이프라인|
|OrchestrationTriggersEnded|아니요|트리거 종료|개수|합계|성공, 실패 또는 취소 된 오케스트레이션 트리거의 수입니다.|Result, FailureType, Trigger|
|SQLOnDemandLoginAttempts|아니요|로그인 시도|개수|합계|성공 또는 실패 한 로그인 시도 횟수|결과|
|SQLOnDemandQueriesEnded|아니요|쿼리 종료|개수|합계|성공, 실패 또는 취소 된 쿼리 수|결과|
|SQLOnDemandQueryProcessedBytes|아니요|처리된 데이터|바이트|합계|쿼리에서 처리 하는 데이터 양|차원 없음|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Synapse/작업 영역/bigDataPools

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|CoresCapacity|아니요|코어 용량|개수|최대|코어 용량|차원 없음|
|MemoryCapacityGB|아니요|메모리 용량 (GB)|개수|최대|메모리 용량 (GB)|차원 없음|
|SparkJobsEnded|예|종료 된 응용 프로그램|개수|합계|종료 된 응용 프로그램 수|JobType, JobResult|


## <a name="microsoftsynapseworkspacessqlpools"></a>Synapse/workspaces/sqlPools

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|AdaptiveCacheHitPercent|아니요|적응 캐시 적중률|백분율|최대|워크 로드가 적응 캐시를 얼마나 잘 활용 하 고 있는 지를 측정 합니다. 캐시 적중률 메트릭과 함께이 메트릭을 사용 하 여 추가 용량을 조정 하거나 작업을 다시 실행 하 여 캐시를 하이드레이션 하며 나중 여부를 결정 합니다.|차원 없음|
|AdaptiveCacheUsedPercent|아니요|적응 캐시 사용 백분율|백분율|최대|워크 로드가 적응 캐시를 얼마나 잘 활용 하 고 있는 지를 측정 합니다. 캐시 사용 백분율 메트릭에이 메트릭을 사용 하 여 추가 용량에 대해 크기를 조정할지 또는 작업을 다시 실행 하 여 캐시를 하이드레이션 하며 나중 여부를 결정 합니다.|차원 없음|
|Connections|예|Connections|개수|합계|SQL 풀에 대 한 총 로그인 수|결과|
|ConnectionsBlockedByFirewall|아니요|방화벽에 의해 차단 된 연결|개수|합계|방화벽 규칙에 의해 차단 된 연결 수입니다. SQL 풀에 대 한 액세스 제어 정책을 다시 방문 하 고, 수가 높으면 이러한 연결을 모니터링 합니다.|차원 없음|
|DWULimit|아니요|DWU 제한|개수|최대|SQL 풀의 서비스 수준 목표|차원 없음|
|DWUUsed|아니요|DWU 사용됨|개수|최대|SQL 풀에서의 사용에 대 한 상위 수준 표시를 나타냅니다. DWU 제한 * DWU 백분율로 측정 됩니다.|차원 없음|
|DWUUsedPercent|아니요|DWU 사용 백분율|백분율|최대|SQL 풀에서의 사용에 대 한 상위 수준 표시를 나타냅니다. CPU 비율과 데이터 IO 비율의 최대값을 취하여 측정 됩니다.|차원 없음|
|LocalTempDBUsedPercent|아니요|로컬 tempdb 사용 백분율|백분율|최대|모든 계산 노드의 로컬 tempdb 사용률-5 분 마다 값이 내보내집니다.|차원 없음|
|MemoryUsedPercent|아니요|사용 되는 메모리 백분율|백분율|최대|SQL 풀의 모든 노드에 대 한 메모리 사용률|차원 없음|
|wlg_effective_min_resource_percent|예|유효 최소 리소스 비율|백분율|최소값|서비스 수준 및 작업 그룹 설정을 고려 하 여 유효 min 리소스 비율 설정이 허용 됩니다. 낮은 서비스 수준에서 효과적인 min_percentage_resource을 더 높은 수준으로 조정할 수 있습니다.|IsUserDefined,|
|WLGActiveQueries|아니요|작업 그룹 활성 쿼리|개수|합계|작업 그룹 내의 활성 쿼리입니다. 필터링 되지 않은이 메트릭과 분할 되지 않은 메트릭을 사용 하 여 시스템에서 실행 중인 모든 활성 쿼리를 표시 합니다.|IsUserDefined,|
|WLGActiveQueriesTimeouts|아니요|작업 그룹 쿼리 시간 제한|개수|합계|시간이 초과 된 작업 그룹에 대 한 쿼리입니다. 이 메트릭에 의해 보고 된 쿼리 시간 제한은 쿼리가 실행을 시작한 후에만 수행 됩니다 (잠금 또는 리소스 대기로 인 한 대기 시간을 포함 하지 않음).|IsUserDefined,|
|WLGAllocationByMaxResourcePercent|아니요|최대 리소스 비율별 작업 그룹 할당|백분율|최대|작업 그룹당 유효 캡 리소스 비율을 기준으로 하는 리소스의 백분율 할당을 표시 합니다. 이 메트릭은 작업 그룹의 효과적인 사용률을 제공 합니다.|IsUserDefined,|
|WLGAllocationBySystemPercent|아니요|시스템 비율별 작업 그룹 할당|백분율|최대|전체 시스템에 상대적인 리소스 할당 비율|IsUserDefined,|
|WLGEffectiveCapResourcePercent|예|유효 상한 리소스 비율|백분율|최대|작업 그룹에 대 한 유효 상한 리소스 비율입니다. Min_percentage_resource > 0 인 다른 작업 그룹이 있는 경우 effective_cap_percentage_resource 비례적으로 줄어듭니다.|IsUserDefined,|
|WLGQueuedQueries|아니요|작업 그룹 큐에 대기 중인 쿼리|개수|합계|최대 동시성 제한에 도달한 후 큐에 대기 중인 총 요청 수|IsUserDefined,|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|예|수신된 바이트|바이트|합계|모든 이벤트 원본에서 읽은 바이트 수입니다.|차원 없음|
|IngressReceivedInvalidMessages|예|수신된 잘못된 메시지|개수|합계|모든 이벤트 허브 또는 IoT 허브 이벤트 원본에서 읽은 잘못된 메시지 수입니다.|차원 없음|
|IngressReceivedMessages|예|수신된 메시지|개수|합계|모든 이벤트 허브 또는 IoT 허브 이벤트 원본에서 읽은 메시지 수입니다.|차원 없음|
|IngressReceivedMessagesCountLag|예|수신된 메시지 수 지연|개수|평균|이벤트 원본 파티션에서 마지막으로 큐에 넣은 메시지의 시퀀스 번호와 수신 중 처리 되는 메시지의 시퀀스 번호 간의 차이|차원 없음|
|IngressReceivedMessagesTimeLag|예|수신된 메시지 시간 지연|초|최대|메시지가 이벤트 원본의 큐에 대기되는 시간과 수신 처리되는 시간 간의 차이입니다.|차원 없음|
|IngressStoredBytes|예|저장된 수신 바이트|바이트|합계|성공적으로 처리되어 쿼리에 사용할 수 있는 총 이벤트 크기|차원 없음|
|IngressStoredEvents|예|저장된 수신 이벤트|개수|합계|성공적으로 처리되어 쿼리에 사용할 수 있는 총 평면화된 이벤트 크기|차원 없음|
|WarmStorageMaxProperties|예|웜 저장소 최대 속성|개수|최대|S1/S2 SKU에 대해 환경에서 허용 하는 최대 속성 수 및 PAYG SKU에 대 한 웜 저장소에서 허용 하는 최대 속성 수|차원 없음|
|WarmStorageUsedProperties|예|웜 저장소 사용 속성 |개수|최대|S1/S2 SKU에 대해 환경에서 사용 하는 속성 수 및 PAYG SKU에 대 한 웜 저장소에서 사용 하는 속성 수|차원 없음|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|예|수신된 바이트|바이트|합계|이벤트 원본에서 읽은 바이트 수|차원 없음|
|IngressReceivedInvalidMessages|예|수신된 잘못된 메시지|개수|합계|이벤트 원본에서 읽은 잘못된 메시지 수|차원 없음|
|IngressReceivedMessages|예|수신된 메시지|개수|합계|이벤트 원본에서 읽은 메시지 수|차원 없음|
|IngressReceivedMessagesCountLag|예|수신된 메시지 수 지연|개수|평균|이벤트 원본 파티션에서 마지막으로 큐에 넣은 메시지의 시퀀스 번호와 수신 중 처리 되는 메시지의 시퀀스 번호 간의 차이|차원 없음|
|IngressReceivedMessagesTimeLag|예|수신된 메시지 시간 지연|초|최대|메시지가 이벤트 원본의 큐에 대기되는 시간과 수신 처리되는 시간 간의 차이입니다.|차원 없음|
|IngressStoredBytes|예|저장된 수신 바이트|바이트|합계|성공적으로 처리되어 쿼리에 사용할 수 있는 총 이벤트 크기|차원 없음|
|IngressStoredEvents|예|저장된 수신 이벤트|개수|합계|성공적으로 처리되어 쿼리에 사용할 수 있는 총 평면화된 이벤트 크기|차원 없음|
|WarmStorageMaxProperties|예|웜 저장소 최대 속성|개수|최대|S1/S2 SKU에 대해 환경에서 허용 하는 최대 속성 수 및 PAYG SKU에 대 한 웜 저장소에서 허용 하는 최대 속성 수|차원 없음|
|WarmStorageUsedProperties|예|웜 저장소 사용 속성 |개수|최대|S1/S2 SKU에 대해 환경에서 사용 하는 속성 수 및 PAYG SKU에 대 한 웜 저장소에서 사용 하는 속성 수|차원 없음|


## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|디스크 읽기 바이트|예|디스크 읽기 바이트|바이트|합계|샘플 기간 동안 읽기 작업으로 인 한 총 디스크 처리량입니다.|차원 없음|
|디스크 읽기 작업/초|예|디스크 읽기 작업/초|초당 개수|평균|이전 샘플 기간의 평균 IO 읽기 작업 수입니다. 이러한 작업에는 가변 크기를 사용할 수 있습니다.|차원 없음|
|디스크 쓰기 바이트|예|디스크 쓰기 바이트|바이트|합계|샘플 기간에 대 한 쓰기 작업으로 인 한 총 디스크 처리량입니다.|차원 없음|
|디스크 쓰기 작업/초|예|디스크 쓰기 작업/초|초당 개수|평균|이전 샘플 기간의 평균 IO 쓰기 작업 수입니다. 이러한 작업에는 가변 크기를 사용할 수 있습니다.|차원 없음|
|DiskReadBytesPerSecond|예|디스크 읽기 바이트/초|초당 바이트 수|평균|샘플 기간에 대 한 읽기 작업으로 인 한 평균 디스크 처리량입니다.|차원 없음|
|DiskReadLatency|예|디스크 읽기 대기 시간|밀리초|평균|총 읽기 대기 시간입니다. 장치 및 커널 읽기 대기 시간의 합계입니다.|차원 없음|
|DiskReadOperations|예|디스크 읽기 작업|개수|합계|이전 샘플 기간의 IO 읽기 작업 수입니다. 이러한 작업에는 가변 크기를 사용할 수 있습니다.|차원 없음|
|DiskWriteBytesPerSecond|예|디스크 쓰기 바이트/초|초당 바이트 수|평균|샘플 기간에 대 한 쓰기 작업으로 인 한 평균 디스크 처리량입니다.|차원 없음|
|DiskWriteLatency|예|디스크 쓰기 대기 시간|밀리초|평균|총 쓰기 대기 시간입니다. 장치 및 커널 쓰기 대기 시간의 합계입니다.|차원 없음|
|DiskWriteOperations|예|디스크 쓰기 작업|개수|합계|이전 샘플 기간의 IO 쓰기 작업 수입니다. 이러한 작업에는 가변 크기를 사용할 수 있습니다.|차원 없음|
|MemoryActive|예|메모리 활성|바이트|평균|지난 작은 시간 동안 VM에서 사용 하는 메모리의 양입니다. VM이 현재 필요로 하는 메모리의 양 ("true")입니다. 사용 되지 않는 추가 메모리는 게스트의 성능에 영향을 주지 않고 스왑할 수도 있고 ballooned 수 있습니다.|차원 없음|
|MemoryGranted|예|부여된 메모리|바이트|평균|호스트에서 VM에 부여 된 메모리의 양입니다. 메모리는 한 번에 도달할 때까지 호스트에 부여 되지 않으며, VMkernel에 메모리가 필요한 경우 부여 된 메모리를 교환 하거나 ballooned 수 있습니다.|차원 없음|
|MemoryUsed|예|사용된 메모리|바이트|평균|VM에서 사용 중인 컴퓨터 메모리의 양입니다.|차원 없음|
|네트워크 인|예|네트워크 인|바이트|합계|수신 되는 트래픽에 대 한 총 네트워크 처리량입니다.|차원 없음|
|네트워크 아웃|예|네트워크 아웃|바이트|합계|전송 된 트래픽에 대 한 총 네트워크 처리량입니다.|차원 없음|
|NetworkInBytesPerSecond|예|네트워크 입력 바이트/초|초당 바이트 수|평균|수신 되는 트래픽에 대 한 평균 네트워크 처리량입니다.|차원 없음|
|NetworkOutBytesPerSecond|예|네트워크 출력 바이트/초|초당 바이트 수|평균|전송 된 트래픽에 대 한 평균 네트워크 처리량입니다.|차원 없음|
|백분율 CPU|예|백분율 CPU|백분율|평균|CPU 사용률입니다. 이 값은 시스템의 모든 프로세서 코어를 나타내는 100%로 보고 됩니다. 예를 들어 4 코어 시스템의 50%를 사용 하는 양방향 VM은 2 개의 코어를 완전히 사용 합니다.|차원 없음|
|PercentageCpuReady|예|CPU 준비 백분율|밀리초|합계|준비 시간은 CPU를 과거 업데이트 간격으로 사용할 수 있을 때까지 기다리는 데 걸리는 시간입니다.|차원 없음|


## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|ActiveRequests|예|활성 요청|개수|합계|활성 요청|인스턴스|
|AverageResponseTime|예|평균 응답 시간|초|평균|평균 응답 시간|인스턴스|
|BytesReceived|예|데이터 입력|바이트|합계|데이터 입력|인스턴스|
|BytesSent|예|데이터 출력|바이트|합계|데이터 출력|인스턴스|
|CpuPercentage|예|CPU 비율|백분율|평균|CPU 비율|인스턴스|
|DiskQueueLength|예|디스크 큐 길이|개수|평균|디스크 큐 길이|인스턴스|
|Http101|예|Http 101|개수|합계|Http 101|인스턴스|
|Http2xx|예|Http 2xx|개수|합계|Http 2xx|인스턴스|
|Http3xx|예|Http 3xx|개수|합계|Http 3xx|인스턴스|
|Http401|예|Http 401|개수|합계|Http 401|인스턴스|
|Http403|예|Http 403|개수|합계|Http 403|인스턴스|
|Http404|예|Http 404|개수|합계|Http 404|인스턴스|
|Http406|예|Http 406|개수|합계|Http 406|인스턴스|
|Http4xx|예|Http 4xx|개수|합계|Http 4xx|인스턴스|
|Http5xx|예|Http 서버 오류|개수|합계|Http 서버 오류|인스턴스|
|HttpQueueLength|예|Http 큐 길이|개수|평균|Http 큐 길이|인스턴스|
|LargeAppServicePlanInstances|예|대형 App Service 계획 작업자|개수|평균|대형 App Service 계획 작업자|차원 없음|
|MediumAppServicePlanInstances|예|중형 App Service 계획 작업자|개수|평균|중형 App Service 계획 작업자|차원 없음|
|MemoryPercentage|예|메모리 비율|백분율|평균|메모리 비율|인스턴스|
|요청|예|요청|개수|합계|요청|인스턴스|
|SmallAppServicePlanInstances|예|소형 App Service 계획 작업자|개수|평균|소형 App Service 계획 작업자|차원 없음|
|TotalFrontEnds|예|총 프런트 엔드|개수|평균|총 프런트 엔드|차원 없음|


## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|CpuPercentage|예|CPU 비율|백분율|평균|CPU 비율|인스턴스|
|MemoryPercentage|예|메모리 비율|백분율|평균|메모리 비율|인스턴스|
|WorkersAvailable|예|사용 가능한 작업자|개수|평균|사용 가능한 작업자|차원 없음|
|WorkersTotal|예|총 작업자|개수|평균|총 작업자|차원 없음|
|WorkersUsed|예|사용된 작업자|개수|평균|사용된 작업자|차원 없음|


## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|BytesReceived|예|데이터 입력|바이트|합계|데이터 입력|인스턴스|
|BytesSent|예|데이터 출력|바이트|합계|데이터 출력|인스턴스|
|CpuPercentage|예|CPU 비율|백분율|평균|CPU 비율|인스턴스|
|DiskQueueLength|예|디스크 큐 길이|개수|평균|디스크 큐 길이|인스턴스|
|HttpQueueLength|예|Http 큐 길이|개수|평균|Http 큐 길이|인스턴스|
|MemoryPercentage|예|메모리 비율|백분율|평균|메모리 비율|인스턴스|
|SocketInboundAll|예|SocketInboundAll|개수|평균|SocketInboundAll|인스턴스|
|SocketLoopback|예|SocketLoopback|개수|평균|SocketLoopback|인스턴스|
|SocketOutboundAll|예|SocketOutboundAll|개수|평균|SocketOutboundAll|인스턴스|
|SocketOutboundEstablished|예|SocketOutboundEstablished|개수|평균|SocketOutboundEstablished|인스턴스|
|SocketOutboundTimeWait|예|SocketOutboundTimeWait|개수|평균|SocketOutboundTimeWait|인스턴스|
|TcpCloseWait|예|TCP 닫기 대기|개수|평균|TCP 닫기 대기|인스턴스|
|TcpClosing|예|TCP 닫는 중|개수|평균|TCP 닫는 중|인스턴스|
|TcpEstablished|예|TCP 설정됨|개수|평균|TCP 설정됨|인스턴스|
|TcpFinWait1|예|TCP FIN 대기 1|개수|평균|TCP FIN 대기 1|인스턴스|
|TcpFinWait2|예|TCP FIN 대기 2|개수|평균|TCP FIN 대기 2|인스턴스|
|TcpLastAck|예|TCP 마지막 ACK|개수|평균|TCP 마지막 ACK|인스턴스|
|TcpSynReceived|예|TCP SYN 받음|개수|평균|TCP SYN 받음|인스턴스|
|TcpSynSent|예|TCP SYN 보냄|개수|평균|TCP SYN 보냄|인스턴스|
|TcpTimeWait|예|TCP 시간 대기|개수|평균|TCP 시간 대기|인스턴스|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites(Functions 제외) 

> [!NOTE]
> **파일 시스템 사용량** 은 전역적으로 롤아웃 되는 새 메트릭입니다. 비공개 미리 보기에 대해 허용 목록 않은 경우에는 데이터가 필요 하지 않습니다.

> [!IMPORTANT]
> 메트릭 집계와의 혼동을 피하기 위해 **평균 응답 시간은** 사용 되지 않습니다. **응답 시간** 을 대체 항목으로 사용 합니다.

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|AppConnections|예|Connections|개수|평균|Connections|인스턴스|
|AverageMemoryWorkingSet|예|평균 메모리 작업 집합|바이트|평균|평균 메모리 작업 집합|인스턴스|
|AverageResponseTime|예|평균 응답 시간 **(사용 되지 않음)**|초|평균|평균 응답 시간|인스턴스|
|BytesReceived|예|데이터 입력|바이트|합계|데이터 입력|인스턴스|
|BytesSent|예|데이터 출력|바이트|합계|데이터 출력|인스턴스|
|CpuTime|예|CPU 시간|초|합계|CPU 시간|인스턴스|
|CurrentAssemblies|예|현재 어셈블리|개수|평균|현재 어셈블리|인스턴스|
|FileSystemUsage|예|파일 시스템 사용량|바이트|평균|파일 시스템 사용량|차원 없음|
|Gen0Collections|예|Gen 0 가비지 수집|개수|합계|Gen 0 가비지 수집|인스턴스|
|Gen1Collections|예|Gen 1 가비지 수집|개수|합계|Gen 1 가비지 수집|인스턴스|
|Gen2Collections|예|Gen 2 가비지 수집|개수|합계|Gen 2 가비지 수집|인스턴스|
|핸들|예|핸들 개수|개수|평균|핸들 개수|인스턴스|
|HealthCheckStatus|예|상태 검사 상태|개수|평균|상태 검사 상태|인스턴스|
|Http101|예|Http 101|개수|합계|Http 101|인스턴스|
|Http2xx|예|Http 2xx|개수|합계|Http 2xx|인스턴스|
|Http3xx|Http 3xx|개수|합계|Http 3xx|인스턴스|
|Http401|예|Http 401|개수|합계|Http 401|인스턴스|
|Http403|예|Http 403|개수|합계|Http 403|인스턴스|
|Http404|예|Http 404|개수|합계|Http 404|인스턴스|
|Http406|예|Http 406|개수|합계|Http 406|인스턴스|
|Http4xx|예|Http 4xx|개수|합계|Http 4xx|인스턴스|
|Http5xx|예|Http 서버 오류|개수|합계|Http 서버 오류|인스턴스|
|HttpResponseTime|예|응답 시간|초|평균|응답 시간|인스턴스|
|IoOtherBytesPerSecond|예|초당 IO 기타 바이트 수|초당 바이트 수|합계|초당 IO 기타 바이트 수|인스턴스|
|IoOtherOperationsPerSecond|예|초당 IO 기타 작업 수|초당 바이트 수|합계|초당 IO 기타 작업 수|인스턴스|
|IoReadBytesPerSecond|예|초당 IO 읽기 바이트 수|초당 바이트 수|합계|초당 IO 읽기 바이트 수|인스턴스|
|IoReadOperationsPerSecond|예|초당 IO 읽기 작업 수|초당 바이트 수|합계|초당 IO 읽기 작업 수|인스턴스|
|IoWriteBytesPerSecond|예|초당 IO 쓰기 바이트 수|초당 바이트 수|합계|초당 IO 쓰기 바이트 수|인스턴스|
|IoWriteOperationsPerSecond|예|초당 IO 쓰기 작업 수|초당 바이트 수|합계|초당 IO 쓰기 작업 수|인스턴스|
|MemoryWorkingSet|예|메모리 작업 집합|바이트|평균|메모리 작업 집합|인스턴스|
|PrivateBytes|예|프라이빗 바이트|바이트|평균|프라이빗 바이트|인스턴스|
|요청|예|요청|개수|합계|요청|인스턴스|
|RequestsInApplicationQueue|예|애플리케이션 큐의 요청 수|개수|평균|애플리케이션 큐의 요청 수|인스턴스|
|스레드|예|스레드 개수|개수|평균|스레드 개수|인스턴스|
|TotalAppDomains|예|총 앱 도메인|개수|평균|총 앱 도메인|인스턴스|
|TotalAppDomainsUnloaded|예|언로드된 총 앱 도메인|개수|평균|언로드된 총 앱 도메인|인스턴스|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites(functions)

> [!NOTE]
> **파일 시스템 사용량** 은 전역적으로 롤아웃 되는 새 메트릭입니다. 비공개 미리 보기에 대해 허용 목록 않은 경우에는 데이터가 필요 하지 않습니다.

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|AverageMemoryWorkingSet|예|평균 메모리 작업 집합|바이트|평균|평균 메모리 작업 집합|인스턴스|
|BytesReceived|예|데이터 입력|바이트|합계|데이터 입력|인스턴스|
|BytesSent|예|데이터 출력|바이트|합계|데이터 출력|인스턴스|
|CurrentAssemblies|예|현재 어셈블리|개수|평균|현재 어셈블리|인스턴스|
|FileSystemUsage|예|파일 시스템 사용량|바이트|평균|파일 시스템 사용량|차원 없음|
|FunctionExecutionCount|예|함수 실행 횟수|개수|합계|함수 실행 횟수|인스턴스|
|FunctionExecutionUnits|예|함수 실행 단위|개수|합계|[함수 실행 단위](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|인스턴스|
|Gen0Collections|예|Gen 0 가비지 수집|개수|합계|Gen 0 가비지 수집|인스턴스|
|Gen1Collections|예|Gen 1 가비지 수집|개수|합계|Gen 1 가비지 수집|인스턴스|
|Gen2Collections|예|Gen 2 가비지 수집|개수|합계|Gen 2 가비지 수집|인스턴스|
|HealthCheckStatus|예|상태 검사 상태|개수|평균|상태 검사 상태|인스턴스|
|Http5xx|예|Http 서버 오류|개수|합계|Http 서버 오류|인스턴스|
|IoOtherBytesPerSecond|예|초당 IO 기타 바이트 수|초당 바이트 수|합계|초당 IO 기타 바이트 수|인스턴스|
|IoOtherOperationsPerSecond|예|초당 IO 기타 작업 수|초당 바이트 수|합계|초당 IO 기타 작업 수|인스턴스|
|IoReadBytesPerSecond|예|초당 IO 읽기 바이트 수|초당 바이트 수|합계|초당 IO 읽기 바이트 수|인스턴스|
|IoReadOperationsPerSecond|예|초당 IO 읽기 작업 수|초당 바이트 수|합계|초당 IO 읽기 작업 수|인스턴스|
|IoWriteBytesPerSecond|예|초당 IO 쓰기 바이트 수|초당 바이트 수|합계|초당 IO 쓰기 바이트 수|인스턴스|
|IoWriteOperationsPerSecond|예|초당 IO 쓰기 작업 수|초당 바이트 수|합계|초당 IO 쓰기 작업 수|인스턴스|
|MemoryWorkingSet|예|메모리 작업 집합|바이트|평균|메모리 작업 집합|인스턴스|
|PrivateBytes|예|프라이빗 바이트|바이트|평균|프라이빗 바이트|인스턴스|
|RequestsInApplicationQueue|예|애플리케이션 큐의 요청 수|개수|평균|애플리케이션 큐의 요청 수|인스턴스|
|TotalAppDomains|예|총 앱 도메인|개수|평균|총 앱 도메인|인스턴스|
|TotalAppDomainsUnloaded|예|언로드된 총 앱 도메인|개수|평균|언로드된 총 앱 도메인|인스턴스|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|메트릭|진단 설정을 통해 내보내기 가능?|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|---|
|AppConnections|예|Connections|개수|평균|Connections|인스턴스|
|AverageMemoryWorkingSet|예|평균 메모리 작업 집합|바이트|평균|평균 메모리 작업 집합|인스턴스|
|AverageResponseTime|예|평균 응답 시간|초|평균|평균 응답 시간|인스턴스|
|BytesReceived|예|데이터 입력|바이트|합계|데이터 입력|인스턴스|
|BytesSent|예|데이터 출력|바이트|합계|데이터 출력|인스턴스|
|CpuTime|예|CPU 시간|초|합계|CPU 시간|인스턴스|
|CurrentAssemblies|예|현재 어셈블리|개수|평균|현재 어셈블리|인스턴스|
|FileSystemUsage|예|파일 시스템 사용량|바이트|평균|파일 시스템 사용량|차원 없음|
|FunctionExecutionCount|예|함수 실행 횟수|개수|합계|함수 실행 횟수|인스턴스|
|FunctionExecutionUnits|예|함수 실행 단위|개수|합계|함수 실행 단위|인스턴스|
|Gen0Collections|예|Gen 0 가비지 수집|개수|합계|Gen 0 가비지 수집|인스턴스|
|Gen1Collections|예|Gen 1 가비지 수집|개수|합계|Gen 1 가비지 수집|인스턴스|
|Gen2Collections|예|Gen 2 가비지 수집|개수|합계|Gen 2 가비지 수집|인스턴스|
|핸들|예|핸들 개수|개수|평균|핸들 개수|인스턴스|
|HealthCheckStatus|예|상태 검사 상태|개수|평균|상태 검사 상태|인스턴스|
|Http101|예|Http 101|개수|합계|Http 101|인스턴스|
|Http2xx|예|Http 2xx|개수|합계|Http 2xx|인스턴스|
|Http3xx|예|Http 3xx|개수|합계|Http 3xx|인스턴스|
|Http401|예|Http 401|개수|합계|Http 401|인스턴스|
|Http403|예|Http 403|개수|합계|Http 403|인스턴스|
|Http404|예|Http 404|개수|합계|Http 404|인스턴스|
|Http406|예|Http 406|개수|합계|Http 406|인스턴스|
|Http4xx|예|Http 4xx|개수|합계|Http 4xx|인스턴스|
|Http5xx|예|Http 서버 오류|개수|합계|Http 서버 오류|인스턴스|
|HttpResponseTime|예|응답 시간|초|평균|응답 시간|인스턴스|
|IoOtherBytesPerSecond|예|초당 IO 기타 바이트 수|초당 바이트 수|합계|초당 IO 기타 바이트 수|인스턴스|
|IoOtherOperationsPerSecond|예|초당 IO 기타 작업 수|초당 바이트 수|합계|초당 IO 기타 작업 수|인스턴스|
|IoReadBytesPerSecond|예|초당 IO 읽기 바이트 수|초당 바이트 수|합계|초당 IO 읽기 바이트 수|인스턴스|
|IoReadOperationsPerSecond|예|초당 IO 읽기 작업 수|초당 바이트 수|합계|초당 IO 읽기 작업 수|인스턴스|
|IoWriteBytesPerSecond|예|초당 IO 쓰기 바이트 수|초당 바이트 수|합계|초당 IO 쓰기 바이트 수|인스턴스|
|IoWriteOperationsPerSecond|예|초당 IO 쓰기 작업 수|초당 바이트 수|합계|초당 IO 쓰기 작업 수|인스턴스|
|MemoryWorkingSet|예|메모리 작업 집합|바이트|평균|메모리 작업 집합|인스턴스|
|PrivateBytes|예|프라이빗 바이트|바이트|평균|프라이빗 바이트|인스턴스|
|요청|예|요청|개수|합계|요청|인스턴스|
|RequestsInApplicationQueue|예|애플리케이션 큐의 요청 수|개수|평균|애플리케이션 큐의 요청 수|인스턴스|
|스레드|예|스레드 개수|개수|평균|스레드 개수|인스턴스|
|TotalAppDomains|예|총 앱 도메인|개수|평균|총 앱 도메인|인스턴스|
|TotalAppDomainsUnloaded|예|언로드된 총 앱 도메인|개수|평균|언로드된 총 앱 도메인|인스턴스|


## <a name="next-steps"></a>다음 단계
* [Azure Monitor의 메트릭에 대해 읽기](data-platform.md)
* [메트릭에 대한 경고 만들기](alerts-overview.md)
* [스토리지, 이벤트 허브 또는 Log Analytics에 메트릭 내보내기](platform-logs-overview.md)
