---
title: Azure Monitor에서 지원되는 리소스 종류별 메트릭
description: Azure Monitor의 각 리소스 유형별로 사용 가능한 메트릭 목록.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 12/18/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: e67cd8c85d9ba85e32ef7aece402f97ab24243e5
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963447"
---
# <a name="supported-metrics-with-azure-monitor"></a>Azure Monitor에서 지원되는 메트릭

Azure Monitor에서는 포털에서의 차트 작성, REST API를 통한 액세스, PowerShell이나 CLI를 통한 쿼리 등, 메트릭과 상호 작용하는 몇 가지 방법을 제공합니다. 다음은 현재 Azure Monitor의 메트릭 파이프라인을 통해 사용할 수 있는 모든 메트릭의 전체 목록입니다. 레거시 API를 통해서 또는 포털에서 다른 메트릭을 제공할 수 있습니다. 아래 목록에는 통합 Azure Monitor 메트릭 파이프라인을 통해 사용할 수 있는 메트릭만 포함됩니다. 메트릭은 네임 스페이스로 구성 됩니다. 서비스 및 해당 서비스에 속하는 네임 스페이스 목록은 [Azure 서비스에 대 한 리소스 공급자](../../azure-resource-manager/management/azure-services-resource-providers.md)를 참조 하세요. 이러한 메트릭에 대 한 쿼리 및 액세스를 프로그래밍 방식으로 하려면 [2018-01-01 api 버전](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) 을 사용 하세요.

> [!NOTE]
> 진단 설정을 통한 다차원 메트릭 보내기는 현재 지원되지 않습니다. 차원이 있는 메트릭은 차원 값 전체에서 집계된 플랫 단일 차원 메트릭으로 내보내집니다.
>
> *예*: Event Hub의 '들어오는 메시지' 메트릭은 큐 수준별로 탐색하고 차트화할 수 있습니다. 하지만 진단 설정을 통해 내보내면 메트릭은 Event Hub의 모든 큐에서 모두 수신되는 메시지로 표시됩니다.
>
> 진단 설정을 통해 내보낼 수 있는 플랫폼 메트릭 목록은 [이 문서](metrics-supported-export-diagnostic-settings.md)를 참조 하세요.



## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|qpu_metric|QPU|카운트|평균|QPU. 범위는 S1의 경우 0-100, S2의 경우 0-200, S4의 경우 0-400임|ServerResourceType|
|memory_metric|메모리|바이트|평균|메모리. 범위는 S1의 경우 0-25GB, S2의 경우 0-50GB, S4의 경우 0-100GB임|ServerResourceType|
|private_bytes_metric|프라이빗 바이트|바이트|평균|전용 바이트.|ServerResourceType|
|virtual_bytes_metric|가상 바이트|바이트|평균|가상 바이트.|ServerResourceType|
|TotalConnectionRequests|총 연결 요청 수|카운트|평균|도착한 총 연결 요청 수입니다.|ServerResourceType|
|SuccessfullConnectionsPerSec|초당 성공한 연결 수|초당 개수|평균|성공적으로 완료된 연결 비율입니다.|ServerResourceType|
|TotalConnectionFailures|총 연결 실패 수|카운트|평균|실패한 총 연결 시도 수입니다.|ServerResourceType|
|CurrentUserSessions|현재 사용자 세션 수|카운트|평균|현재 설정된 사용자 세션 수입니다.|ServerResourceType|
|QueryPoolBusyThreads|쿼리 풀의 사용 중인 스레드|카운트|평균|쿼리 스레드 풀의 사용 중인 스레드 수입니다.|ServerResourceType|
|CommandPoolJobQueueLength|명령 풀의 작업 큐 길이|카운트|평균|명령 스레드 풀의 큐에 있는 작업 수입니다.|ServerResourceType|
|ProcessingPoolJobQueueLength|처리 풀의 작업 큐 길이|카운트|평균|처리 스레드 풀의 큐에 있는 비-I/O 작업 수입니다.|ServerResourceType|
|CurrentConnections|연결: 현재 연결|카운트|평균|현재 설정된 클라이언트 연결 수입니다.|ServerResourceType|
|CleanerCurrentPrice|메모리: 클리너 현재 가격|카운트|평균|현재 메모리 가격, $/바이트/시간, 1000으로 일반화됩니다.|ServerResourceType|
|CleanerMemoryShrinkable|메모리: 클리너 메모리 축소 가능|바이트|평균|메모리 양, 바이트 단위, 백그라운드 클리너에 의해 제거되는 대상입니다.|ServerResourceType|
|CleanerMemoryNonshrinkable|메모리: 클리너 메모리 축소 불가능|바이트|평균|메모리 양, 바이트 단위, 백그라운드 클리너에 의해 제거되는 대상이 아닙니다.|ServerResourceType|
|MemoryUsage|메모리: 메모리 사용량|바이트|평균|클리너 메모리 가격을 계산하는 데 사용되는 서버 프로세스의 메모리 사용량입니다. 메모리 매핑된 데이터 크기를 더한 카운터 Process\PrivateBytes와 동일하며 xVelocity 엔진 메모리 제한을 초과하여 xVelocity 메모리 내 분석 엔진(VertiPaq)에서 매핑하거나 할당하는 메모리를 무시합니다.|ServerResourceType|
|MemoryLimitHard|메모리: 메모리 제한 하드|바이트|평균|하드 메모리 제한, 구성 파일 원본입니다.|ServerResourceType|
|MemoryLimitHigh|메모리: 메모리 제한 상한|바이트|평균|상한 메모리 제한, 구성 파일 원본입니다.|ServerResourceType|
|MemoryLimitLow|메모리: 메모리 제한 하한|바이트|평균|하한 메모리 제한, 구성 파일 원본입니다.|ServerResourceType|
|MemoryLimitVertiPaq|메모리: 메모리 제한 VertiPaq|바이트|평균|메모리 내 제한, 구성 파일 원본입니다.|ServerResourceType|
|할당량|메모리: 할당량|바이트|평균|현재 메모리 할당량, 바이트 단위입니다. 메모리 할당량은 메모리 부여 또는 메모리 예약이라고도 합니다.|ServerResourceType|
|QuotaBlocked|메모리: 차단된 할당량|카운트|평균|다른 메모리 할당량이 해제될 때까지 차단되는 할당량 요청의 현재 수입니다.|ServerResourceType|
|VertiPaqNonpaged|메모리: 페이징되지 않은 VertiPaq|바이트|평균|메모리 내 엔진에서 사용하기 위해 설정된 작동 중에 잠긴 메모리 바이트입니다.|ServerResourceType|
|VertiPaqPaged|메모리: 페이징된 VertiPaq|바이트|평균|메모리 내 데이터에 사용 중인 페이징된 메모리 바이트입니다.|ServerResourceType|
|RowsReadPerSec|처리: 초당 읽은 행|초당 개수|평균|모든 관계형 데이터베이스에서 읽은 행의 비율입니다.|ServerResourceType|
|RowsConvertedPerSec|처리: 초당 변환된 행|초당 개수|평균|처리하는 동안 변환된 행의 비율입니다.|ServerResourceType|
|RowsWrittenPerSec|처리: 초당 작성된 행|초당 개수|평균|처리하는 동안 작성된 행의 비율입니다.|ServerResourceType|
|CommandPoolBusyThreads|스레드: 명령 풀 사용 중인 스레드|카운트|평균|명령 스레드 풀의 사용 중인 스레드 수입니다.|ServerResourceType|
|CommandPoolIdleThreads|스레드: 명령 풀 유휴 상태 스레드|카운트|평균|명령 스레드 풀의 유휴 상태 스레드 수입니다.|ServerResourceType|
|LongParsingBusyThreads|스레드: 긴 구문 분석 사용 중인 스레드|카운트|평균|긴 구문 분석 스레드 풀에서 사용 중인 스레드 수입니다.|ServerResourceType|
|LongParsingIdleThreads|스레드: 긴 구문 분석 유휴 상태 스레드|카운트|평균|긴 구문 분석 스레드 풀에서 유휴 상태 스레드 수입니다.|ServerResourceType|
|LongParsingJobQueueLength|스레드: 긴 구문 분석 작업 큐 길이|카운트|평균|긴 구문 분석 스레드 풀의 큐에 있는 작업 수입니다.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|스레드: 처리 풀 사용 중인 I/O 작업 스레드|카운트|평균|처리 스레드 풀에서 I/O 작업을 실행 중인 스레드 수입니다.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|스레드: 처리 풀 사용 중인 비-I/O 스레드|카운트|평균|처리 스레드 풀에서 비-I/O 작업을 실행 중인 스레드 수입니다.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|스레드: 처리 풀 I/O 작업 큐 길이|카운트|평균|처리 스레드 풀의 큐에 있는 I/O 작업 수입니다.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|스레드: 처리 풀 유휴 상태 I/O 작업 스레드|카운트|평균|처리 스레드 풀에서 I/O 작업의 유휴 상태 스레드 수입니다.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|스레드: 처리 풀 유휴 상태 비-I/O 스레드|카운트|평균|비-I/O 작업 전용인 처리 스레드 풀에서 유휴 상태 스레드 수입니다.|ServerResourceType|
|QueryPoolIdleThreads|스레드: 쿼리 풀 유휴 상태 스레드|카운트|평균|처리 스레드 풀에서 I/O 작업의 유휴 상태 스레드 수입니다.|ServerResourceType|
|QueryPoolJobQueueLength|스레드: 쿼리 풀 작업 큐 길이|카운트|평균|쿼리 스레드 풀의 큐에 있는 작업 수입니다.|ServerResourceType|
|ShortParsingBusyThreads|스레드: 짧은 구문 분석 사용 중인 스레드|카운트|평균|짧은 구문 분석 스레드 풀에서 사용 중인 스레드 수입니다.|ServerResourceType|
|ShortParsingIdleThreads|스레드: 짧은 구문 분석 유휴 상태 스레드|카운트|평균|짧은 구문 분석 스레드 풀에서 유휴 상태 스레드 수입니다.|ServerResourceType|
|ShortParsingJobQueueLength|스레드: 짧은 구문 분석 작업 큐 길이|카운트|평균|짧은 구문 분석 스레드 풀의 큐에 있는 작업 수입니다.|ServerResourceType|
|memory_thrashing_metric|메모리 쓰래싱|백분율|평균|평균 메모리 쓰래싱입니다.|ServerResourceType|
|mashup_engine_qpu_metric|M 엔진 QPU|카운트|평균|매시업 엔진 프로세스별 QPU 사용량|ServerResourceType|
|mashup_engine_memory_metric|M 엔진 메모리|바이트|평균|매시업 엔진 프로세스별 메모리 사용량|ServerResourceType|
|mashup_engine_private_bytes_metric|M 엔진 전용 바이트|바이트|평균|매시업 엔진 프로세스의 전용 바이트 사용량입니다.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|M 엔진 가상 바이트|바이트|평균|매시업 엔진 프로세스의 가상 바이트 사용량입니다.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|TotalRequests|총 게이트웨이 요청 (사용 되지 않음)|카운트|합계|게이트웨이 요청 수-대신 GatewayResponseCodeCategory 차원을 사용 하 여 다중 차원 요청 메트릭을 사용 합니다.|위치, 호스트 이름|
|SuccessfulRequests|성공적인 게이트웨이 요청 (사용 되지 않음)|카운트|합계|성공한 게이트웨이 요청 수-대신 GatewayResponseCodeCategory 차원을 사용 하 여 다중 차원 요청 메트릭을 사용 합니다.|위치, 호스트 이름|
|UnauthorizedRequests|권한 없는 게이트웨이 요청 (사용 되지 않음)|카운트|합계|권한 없는 게이트웨이 요청 수-대신 GatewayResponseCodeCategory 차원을 사용 하 여 다중 차원 요청 메트릭을 사용 합니다.|위치, 호스트 이름|
|FailedRequests|실패 한 게이트웨이 요청 (사용 되지 않음)|카운트|합계|게이트웨이 요청의 실패 횟수-대신 GatewayResponseCodeCategory 차원을 사용 하 여 다중 차원 요청 메트릭을 사용 합니다.|위치, 호스트 이름|
|OtherRequests|기타 게이트웨이 요청 (사용 되지 않음)|카운트|합계|다른 게이트웨이 요청 수-대신 GatewayResponseCodeCategory 차원을 사용 하 여 다중 차원 요청 메트릭을 사용 합니다.|위치, 호스트 이름|
|기간|게이트웨이 요청의 전체 기간|밀리초|평균|게이트웨이 요청의 전체 기간(밀리초)|위치, 호스트 이름|
|BackendDuration|백 엔드 요청 기간|밀리초|평균|백 엔드 요청 기간 (밀리초)|위치, 호스트 이름|
|용량 중심|용량 중심|백분율|평균|ApiManagement 서비스에 대한 사용률 메트릭|위치|
|EventHubTotalEvents|총 EventHub 이벤트|카운트|합계|EventHub로 전송 된 이벤트 수|위치|
|EventHubSuccessfulEvents|성공한 EventHub 이벤트|카운트|합계|성공한 EventHub 이벤트 수|위치|
|EventHubTotalFailedEvents|실패 한 EventHub 이벤트|카운트|합계|실패 한 EventHub 이벤트 수|위치|
|EventHubRejectedEvents|거부 된 EventHub 이벤트|카운트|합계|거부 된 EventHub 이벤트 수 (잘못 된 구성 또는 권한 없음)|위치|
|EventHubThrottledEvents|제한 된 EventHub 이벤트|카운트|합계|제한 된 EventHub 이벤트 수|위치|
|EventHubTimedoutEvents|시간 초과 EventHub 이벤트|카운트|합계|시간 초과 된 EventHub 이벤트 수|위치|
|EventHubDroppedEvents|삭제 된 EventHub 이벤트|카운트|합계|큐 크기 제한에 도달 하 여 건너뛴 이벤트 수|위치|
|EventHubTotalBytesSent|EventHub 이벤트 크기|바이트|합계|EventHub 이벤트의 총 크기 (바이트)|위치|
|요청|요청|카운트|합계|여러 차원이 포함 된 게이트웨이 요청 메트릭|Location, Hostname, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|HttpIncomingRequestCount|HttpIncomingRequestCount|카운트|카운트|들어오는 http 요청의 총 수입니다.|없음|
|FailedHttpRequestCount|FailedHttpRequestCount|카운트|카운트|Http 요청이 실패 했습니다.|없음|
|HttpIncomingRequestDuration|HttpIncomingRequestDuration|카운트|평균|Http 요청에 대 한 대기 시간입니다.|없음|


## <a name="microsoftappplatformspring"></a>Microsoft AppPlatform/스프링

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|SystemCpuUsagePercentage|시스템 CPU 사용량 (%)|백분율|평균|전체 시스템의 최근 cpu 사용량|AppName, Pod|
|AppCpuUsagePercentage|앱 CPU 사용량 비율|백분율|평균|앱 JVM CPU 사용량 비율|AppName, Pod|
|AppMemoryCommitted|할당 된 앱 메모리|바이트|평균|JVM에 할당 된 메모리 (바이트)|AppName, Pod|
|AppMemoryUsed|사용 되는 앱 메모리|바이트|평균|사용 되는 앱 메모리 (바이트)|AppName, Pod|
|AppMemoryMax|최대 앱 메모리|바이트|최대|메모리 관리에 사용할 수 있는 최대 메모리 양 (바이트)입니다.|AppName, Pod|
|MaxOldGenMemoryPoolBytes|사용 가능한 최대 이전 생성 데이터 크기|바이트|평균|이전 세대 메모리 풀의 최대 크기|AppName, Pod|
|OldGenMemoryPoolBytes|이전 세대 데이터 크기|바이트|평균|전체 GC 후의 이전 세대 메모리 풀 크기|AppName, Pod|
|OldGenPromotedBytes|이전 세대 데이터 크기로 수준 올리기|바이트|최대|Gc 이전 세대 메모리 풀의 크기에서 GC 이후로의 양의 증가량 수|AppName, Pod|
|YoungGenPromotedBytes|젊은 생성 데이터 크기로 수준 올리기|바이트|최대|1 GC 후의 젊은 생성 메모리 풀 크기가 다음 보다 이전으로 증가 하는 경우 증가|AppName, Pod|
|GCPauseTotalCount|GC 일시 중지 횟수|카운트|합계|GC 일시 중지 횟수|AppName, Pod|
|GCPauseTotalTime|GC 일시 중지 총 시간|밀리초|합계|GC 일시 중지 총 시간|AppName, Pod|
|TomcatSentBytes|총 보낸 바이트 Tomcat|바이트|합계|총 보낸 바이트 Tomcat|AppName, Pod|
|TomcatReceivedBytes|총 수신 바이트 Tomcat|바이트|합계|총 수신 바이트 Tomcat|AppName, Pod|
|TomcatRequestTotalTime|Tomcat 요청 총 시간|밀리초|합계|Tomcat 요청 총 시간|AppName, Pod|
|TomcatRequestTotalCount|Tomcat 요청 총 수|카운트|합계|Tomcat 요청 총 수|AppName, Pod|
|TomcatResponseAvgTime|Tomcat 요청 평균 시간|밀리초|평균|Tomcat 요청 평균 시간|AppName, Pod|
|TomcatRequestMaxTime|Tomcat 요청 최대 시간|밀리초|최대|Tomcat 요청 최대 시간|AppName, Pod|
|TomcatErrorCount|Tomcat 전역 오류|카운트|합계|Tomcat 전역 오류|AppName, Pod|
|TomcatSessionActiveMaxCount|Tomcat 세션 최대 활성 수|카운트|합계|Tomcat 세션 최대 활성 수|AppName, Pod|
|TomcatSessionAliveMaxTime|Tomcat 세션 최대 활성 시간|밀리초|최대|Tomcat 세션 최대 활성 시간|AppName, Pod|
|TomcatSessionActiveCurrentCount|Tomcat 세션 연결 수|카운트|합계|Tomcat 세션 연결 수|AppName, Pod|
|TomcatSessionCreatedCount|Tomcat 세션 생성 횟수|카운트|합계|Tomcat 세션 생성 횟수|AppName, Pod|
|TomcatSessionExpiredCount|Tomcat 세션 만료 수|카운트|합계|Tomcat 세션 만료 수|AppName, Pod|
|TomcatSessionRejectedCount|Tomcat 세션 거부 횟수|카운트|합계|Tomcat 세션 거부 횟수|AppName, Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|TotalJob|총 작업 수|카운트|합계|총 작업 수|Runbook, 상태|
|TotalUpdateDeploymentRuns|총 업데이트 배포 실행|카운트|합계|총 소프트웨어 업데이트 배포 실행|SoftwareUpdateConfigurationName, 상태|
|TotalUpdateDeploymentMachineRuns|총 업데이트 배포 컴퓨터 실행|카운트|합계|소프트웨어 업데이트 배포 실행 시 총 소프트웨어 업데이트 배포 컴퓨터 실행|SoftwareUpdateConfigurationName, Status, TargetComputer, SoftwareUpdateConfigurationRunId|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|CoreCount|전용된 코어 수|카운트|합계|배치 계정의 총 전용 코어 수|없음|
|TotalNodeCount|전용된 노드 수|카운트|합계|배치 계정의 총 전용 노드 수|없음|
|LowPriorityCoreCount|LowPriority 코어 수|카운트|합계|배치 계정의 우선 순위가 낮은 총 코어 수|없음|
|TotalLowPriorityNodeCount|우선 순위가 낮은 노드 수|카운트|합계|배치 계정의 우선 순위가 낮은 총 노드 수|없음|
|CreatingNodeCount|노드 수 만들기|카운트|합계|만든 노드 수|없음|
|StartingNodeCount|시작 노드 수|카운트|합계|시작 노드 수|없음|
|WaitingForStartTaskNodeCount|작업 시작 대기 노드 수|카운트|합계|시작 작업 완료를 기다리는 노드 수|없음|
|StartTaskFailedNodeCount|시작 작업 실패 노드 수|카운트|합계|시작 작업이 실패한 노드 수|없음|
|IdleNodeCount|유휴 상태인 노드 수|카운트|합계|유휴 노드 수|없음|
|OfflineNodeCount|오프라인 노드 수|카운트|합계|오프라인 노드 수|없음|
|RebootingNodeCount|재부팅 노드 수|카운트|합계|다시 부팅하는 노드의 수|없음|
|ReimagingNodeCount|이미지로 다시 설치하는 노드 수|카운트|합계|이미지로 다시 설치하는 노드의 수|없음|
|RunningNodeCount|실행 노드 수|카운트|합계|실행 중인 노드의 수|없음|
|LeavingPoolNodeCount|나가는 풀 노드 수|카운트|합계|풀을 나가는 노드 수|없음|
|UnusableNodeCount|사용 불가 노드 수|카운트|합계|사용할 수 없는 노드 수|없음|
|PreemptedNodeCount|선점된 노드 수|카운트|합계|선점된 노드 수|없음|
|TaskStartEvent|작업 시작 이벤트|카운트|합계|시작된 총 작업 수|없음|
|TaskCompleteEvent|작업 완료 이벤트|카운트|합계|완료된 총 작업 수|없음|
|TaskFailEvent|작업 실패 이벤트|카운트|합계|실패한 상태로 완료된 총 작업 수|없음|
|PoolCreateEvent|풀 만들기 이벤트|카운트|합계|만든 총 풀 수|없음|
|PoolResizeStartEvent|풀 크기 조정 시작 이벤트|카운트|합계|시작된 총 풀 크기 조정 작업 수|없음|
|PoolResizeCompleteEvent|풀 크기 조정 완료 이벤트|카운트|합계|완료된 총 풀 크기 조정 수|없음|
|PoolDeleteStartEvent|풀 삭제 시작 이벤트|카운트|합계|시작된 총 풀 삭제 수|없음|
|PoolDeleteCompleteEvent|풀 삭제 완료 이벤트|카운트|합계|완료된 총 풀 삭제 수|없음|
|JobDeleteCompleteEvent|작업 삭제 완료 이벤트|카운트|합계|정상적으로 삭제된 총 작업 수입니다.|없음|
|JobDeleteStartEvent|작업 삭제 시작 이벤트|카운트|합계|삭제되도록 요청된 총 작업 수입니다.|없음|
|JobDisableCompleteEvent|작업 비활성화 완료 이벤트|카운트|합계|정상적으로 사용하지 않도록 설정된 총 작업 수입니다.|없음|
|JobDisableStartEvent|작업 비활성화 시작 이벤트|카운트|합계|비활성화되도록 요청된 총 작업 수입니다.|없음|
|JobStartEvent|작업 시작 이벤트|카운트|합계|정상적으로 시작된 총 작업 수입니다.|없음|
|JobTerminateCompleteEvent|작업 종료 완료 이벤트|카운트|합계|정상적으로 종료된 총 작업 수입니다.|없음|
|JobTerminateStartEvent|작업 종료 시작 이벤트|카운트|합계|종료되도록 요청된 총 작업 수입니다.|없음|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft BatchAI/workspace

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|제출 된 작업|제출 된 작업|카운트|합계|제출 된 작업 수|시나리오, ClusterName|
|작업 완료 됨|작업 완료 됨|카운트|합계|완료 된 작업 수|시나리오, ClusterName, ResultType|
|총 노드|총 노드|카운트|평균|총 노드 수|시나리오, ClusterName|
|활성 노드|활성 노드|카운트|평균|실행 중인 노드의 수|시나리오, ClusterName|
|유휴 노드|유휴 노드|카운트|평균|유휴 노드 수|시나리오, ClusterName|
|사용할 수 없는 노드|사용할 수 없는 노드|카운트|평균|사용할 수 없는 노드 수|시나리오, ClusterName|
|선점 노드|선점 노드|카운트|평균|선점된 노드 수|시나리오, ClusterName|
|노드 종료|노드 종료|카운트|평균|종료 노드 수|시나리오, ClusterName|
|총 코어|총 코어|카운트|평균|총 코어 수|시나리오, ClusterName|
|활성 코어|활성 코어|카운트|평균|활성 코어 수|시나리오, ClusterName|
|유휴 코어|유휴 코어|카운트|평균|유휴 코어 수|시나리오, ClusterName|
|사용할 수 없는 코어|사용할 수 없는 코어|카운트|평균|사용할 수 없는 코어 수|시나리오, ClusterName|
|선점 코어|선점 코어|카운트|평균|선점 된 코어 수|시나리오, ClusterName|
|코어 탈퇴|코어 탈퇴|카운트|평균|종료 된 코어 수|시나리오, ClusterName|
|할당량 사용률 비율|할당량 사용률 비율|카운트|평균|사용한 할당량 백분율|시나리오, ClusterName, VmFamilyName, VmPriority|

## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|CpuUsagePercentageInDouble|CPU 사용량 비율|백분율|최대|CPU 사용량 비율|노드|
|MemoryUsage|메모리 사용량|바이트|평균|메모리 사용량|노드|
|MemoryLimit|메모리 제한|바이트|평균|메모리 제한|노드|
|MemoryUsagePercentageInDouble|메모리 사용 비율|백분율|평균|메모리 사용 비율|노드|
|StorageUsage|저장소 사용량|바이트|평균|저장소 사용량|노드|
|IOReadBytes|IO 읽기 바이트|바이트|합계|IO 읽기 바이트|노드|
|IOWriteBytes|IO 쓰기 바이트|바이트|합계|IO 쓰기 바이트|노드|
|ConnectionAccepted 됨|수락 된 연결|카운트|합계|수락 된 연결|노드|
|ConnectionHandled|처리 한 연결|카운트|합계|처리 한 연결|노드|
|ConnectionActive|활성 연결 수|카운트|평균|활성 연결 수|노드|
|RequestHandled|처리 한 요청|카운트|합계|처리 한 요청|노드|
|ProcessedBlocks|처리 된 블록|카운트|합계|처리 된 블록|노드|
|ProcessedTransactions|처리 된 트랜잭션|카운트|합계|처리 된 트랜잭션|노드|
|PendingTransactions|보류 중인 트랜잭션|카운트|평균|보류 중인 트랜잭션|노드|
|QueuedTransactions|대기 중인 트랜잭션|카운트|평균|대기 중인 트랜잭션|노드|



## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|connectedclients|연결된 클라이언트|카운트|최대||ShardId|
|totalcommandsprocessed|총 작업|카운트|합계||ShardId|
|cachehits|캐시 적중|카운트|합계||ShardId|
|cachemisses|캐시 누락|카운트|합계||ShardId|
|cachemissrate|캐시 누락 률|백분율|cachemissrate||ShardId|
|getcommands|가져오기|카운트|합계||ShardId|
|setcommands|집합|카운트|합계||ShardId|
|operationsPerSecond|초당 작업|카운트|최대||ShardId|
|evictedkeys|제거된 키|카운트|합계||ShardId|
|totalkeys|전체 키|카운트|최대||ShardId|
|expiredkeys|만료된 키|카운트|합계||ShardId|
|usedmemory|사용된 메모리|바이트|최대||ShardId|
|usedmemorypercentage|사용된 메모리 비율|백분율|최대||ShardId|
|usedmemoryRss|사용된 메모리 RSS|바이트|최대||ShardId|
|serverLoad|서버 부하|백분율|최대||ShardId|
|cacheWrite|캐시 쓰기|초당 바이트 수|최대||ShardId|
|cacheRead|캐시 읽기|초당 바이트 수|최대||ShardId|
|percentProcessorTime|CPU|백분율|최대||ShardId|
|cacheLatency|캐시 대기 시간 마이크로초(미리 보기)|카운트|평균||ShardId|
|오류|오류|카운트|최대||ShardId, ErrorType|
|connectedclients0|연결된 클라이언트(분할 0)|카운트|최대||없음|
|totalcommandsprocessed0|총 작업(분할 0)|카운트|합계||없음|
|cachehits0|캐시 적중(분할 0)|카운트|합계||없음|
|cachemisses0|캐시 누락(분할 0)|카운트|합계||없음|
|getcommands0|가져오기(분할 0)|카운트|합계||없음|
|setcommands0|설정(분할 0)|카운트|합계||없음|
|operationsPerSecond0|초당 작업(분할 0)|카운트|최대||없음|
|evictedkeys0|제거된 키(분할 0)|카운트|합계||없음|
|totalkeys0|총 키(분할 0)|카운트|최대||없음|
|expiredkeys0|만료된 키(분할 0)|카운트|합계||없음|
|usedmemory0|사용된 메모리(분할 0)|바이트|최대||없음|
|usedmemoryRss0|사용된 메모리 RSS(분할 0)|바이트|최대||없음|
|serverLoad0|서버 부하(분할 0)|백분율|최대||없음|
|cacheWrite0|캐시 쓰기(분할 0)|초당 바이트 수|최대||없음|
|cacheRead0|캐시 읽기(분할 0)|초당 바이트 수|최대||없음|
|percentProcessorTime0|CPU(분할 0)|백분율|최대||없음|
|connectedclients1|연결된 클라이언트(분할 1)|카운트|최대||없음|
|totalcommandsprocessed1|총 작업(분할 1)|카운트|합계||없음|
|cachehits1|캐시 적중(분할 1)|카운트|합계||없음|
|cachemisses1|캐시 누락(분할 1)|카운트|합계||없음|
|getcommands1|가져오기(분할 1)|카운트|합계||없음|
|setcommands1|집합(분할 1)|카운트|합계||없음|
|operationsPerSecond1|초당 작업(분할 1)|카운트|최대||없음|
|evictedkeys1|제거된 키(분할 1)|카운트|합계||없음|
|totalkeys1|총 키(분할 1)|카운트|최대||없음|
|expiredkeys1|만료된 키(분할 1)|카운트|합계||없음|
|usedmemory1|사용된 메모리(분할 1)|바이트|최대||없음|
|usedmemoryRss1|사용된 메모리 RSS(분할 1)|바이트|최대||없음|
|serverLoad1|서버 부하(분할 1)|백분율|최대||없음|
|cacheWrite1|캐시 쓰기(분할 1)|초당 바이트 수|최대||없음|
|cacheRead1|캐시 읽기(분할 1)|초당 바이트 수|최대||없음|
|percentProcessorTime1|CPU(분할 1)|백분율|최대||없음|
|connectedclients2|연결된 클라이언트(분할 2)|카운트|최대||없음|
|totalcommandsprocessed2|총 작업(분할 2)|카운트|합계||없음|
|cachehits2|캐시 적중(분할 2)|카운트|합계||없음|
|cachemisses2|캐시 누락(분할 2)|카운트|합계||없음|
|getcommands2|가져오기(분할 2)|카운트|합계||없음|
|setcommands2|설정(분할 2)|카운트|합계||없음|
|operationsPerSecond2|초당 작업(분할 2)|카운트|최대||없음|
|evictedkeys2|제거된 키(분할 2)|카운트|합계||없음|
|totalkeys2|총 키(분할 2)|카운트|최대||없음|
|expiredkeys2|만료된 키(분할 2)|카운트|합계||없음|
|usedmemory2|사용된 메모리(분할 2)|바이트|최대||없음|
|usedmemoryRss2|사용된 메모리 RSS(분할 2)|바이트|최대||없음|
|serverLoad2|서버 부하(분할 2)|백분율|최대||없음|
|cacheWrite2|캐시 쓰기(분할 2)|초당 바이트 수|최대||없음|
|cacheRead2|캐시 읽기(분할 2)|초당 바이트 수|최대||없음|
|percentProcessorTime2|CPU(분할 2)|백분율|최대||없음|
|connectedclients3|연결된 클라이언트(분할 3)|카운트|최대||없음|
|totalcommandsprocessed3|총 작업(분할 3)|카운트|합계||없음|
|cachehits3|캐시 적중(분할 3)|카운트|합계||없음|
|cachemisses3|캐시 누락(분할 3)|카운트|합계||없음|
|getcommands3|가져오기(분할 3)|카운트|합계||없음|
|setcommands3|설정(분할 3)|카운트|합계||없음|
|operationsPerSecond3|초당 작업(분할 3)|카운트|최대||없음|
|evictedkeys3|제거된 키(분할 3)|카운트|합계||없음|
|totalkeys3|총 키(분할 3)|카운트|최대||없음|
|expiredkeys3|만료된 키(분할 3)|카운트|합계||없음|
|usedmemory3|사용된 메모리(분할 3)|바이트|최대||없음|
|usedmemoryRss3|사용된 메모리 RSS(분할 3)|바이트|최대||없음|
|serverLoad3|서버 부하(분할 3)|백분율|최대||없음|
|cacheWrite3|캐시 쓰기(분할 3)|초당 바이트 수|최대||없음|
|cacheRead3|캐시 읽기(분할 3)|초당 바이트 수|최대||없음|
|percentProcessorTime3|CPU(분할 3)|백분율|최대||없음|
|connectedclients4|연결된 클라이언트(분할 4)|카운트|최대||없음|
|totalcommandsprocessed4|총 작업(분할 4)|카운트|합계||없음|
|cachehits4|캐시 적중(분할 4)|카운트|합계||없음|
|cachemisses4|캐시 누락(분할 4)|카운트|합계||없음|
|getcommands4|가져오기(분할 4)|카운트|합계||없음|
|setcommands4|설정(분할 4)|카운트|합계||없음|
|operationsPerSecond4|초당 작업(분할 4)|카운트|최대||없음|
|evictedkeys4|제거된 키(분할 4)|카운트|합계||없음|
|totalkeys4|총 키(분할 4)|카운트|최대||없음|
|expiredkeys4|만료된 키(분할 4)|카운트|합계||없음|
|usedmemory4|사용된 메모리(분할 4)|바이트|최대||없음|
|usedmemoryRss4|사용된 메모리 RSS(분할 4)|바이트|최대||없음|
|serverLoad4|서버 부하(분할 4)|백분율|최대||없음|
|cacheWrite4|캐시 쓰기(분할 4)|초당 바이트 수|최대||없음|
|cacheRead4|캐시 읽기(분할 4)|초당 바이트 수|최대||없음|
|percentProcessorTime4|CPU(분할 4)|백분율|최대||없음|
|connectedclients5|연결된 클라이언트(분할 5)|카운트|최대||없음|
|totalcommandsprocessed5|총 작업(분할 5)|카운트|합계||없음|
|cachehits5|캐시 적중(분할 5)|카운트|합계||없음|
|cachemisses5|캐시 누락(분할 5)|카운트|합계||없음|
|getcommands5|가져오기(분할 5)|카운트|합계||없음|
|setcommands5|설정(분할 5)|카운트|합계||없음|
|operationsPerSecond5|초당 작업(분할 5)|카운트|최대||없음|
|evictedkeys5|제거된 키(분할 5)|카운트|합계||없음|
|totalkeys5|총 키(분할 5)|카운트|최대||없음|
|expiredkeys5|만료된 키(분할 5)|카운트|합계||없음|
|usedmemory5|사용된 메모리(분할 5)|바이트|최대||없음|
|usedmemoryRss5|사용된 메모리 RSS(분할 5)|바이트|최대||없음|
|serverLoad5|서버 부하(분할 5)|백분율|최대||없음|
|cacheWrite5|캐시 쓰기(분할 5)|초당 바이트 수|최대||없음|
|cacheRead5|캐시 읽기(분할 5)|초당 바이트 수|최대||없음|
|percentProcessorTime5|CPU(분할 5)|백분율|최대||없음|
|connectedclients6|연결된 클라이언트(분할 6)|카운트|최대||없음|
|totalcommandsprocessed6|총 작업(분할 6)|카운트|합계||없음|
|cachehits6|캐시 적중(분할 6)|카운트|합계||없음|
|cachemisses6|캐시 누락(분할 6)|카운트|합계||없음|
|getcommands6|가져오기(분할 6)|카운트|합계||없음|
|setcommands6|설정(분할 6)|카운트|합계||없음|
|operationsPerSecond6|초당 작업(분할 6)|카운트|최대||없음|
|evictedkeys6|제거된 키(분할 6)|카운트|합계||없음|
|totalkeys6|총 키(분할 6)|카운트|최대||없음|
|expiredkeys6|만료된 키(분할 6)|카운트|합계||없음|
|usedmemory6|사용된 메모리(분할 6)|바이트|최대||없음|
|usedmemoryRss6|사용된 메모리 RSS(분할 6)|바이트|최대||없음|
|serverLoad6|서버 부하(분할 6)|백분율|최대||없음|
|cacheWrite6|캐시 쓰기(분할 6)|초당 바이트 수|최대||없음|
|cacheRead6|캐시 읽기(분할 6)|초당 바이트 수|최대||없음|
|percentProcessorTime6|CPU(분할 6)|백분율|최대||없음|
|connectedclients7|연결된 클라이언트(분할 7)|카운트|최대||없음|
|totalcommandsprocessed7|총 작업(분할 7)|카운트|합계||없음|
|cachehits7|캐시 적중(분할 7)|카운트|합계||없음|
|cachemisses7|캐시 누락(분할 7)|카운트|합계||없음|
|getcommands7|가져오기(분할 7)|카운트|합계||없음|
|setcommands7|설정(분할 7)|카운트|합계||없음|
|operationsPerSecond7|초당 작업(분할 7)|카운트|최대||없음|
|evictedkeys7|제거된 키(분할 7)|카운트|합계||없음|
|totalkeys7|총 키(분할 7)|카운트|최대||없음|
|expiredkeys7|만료된 키(분할 7)|카운트|합계||없음|
|usedmemory7|사용된 메모리(분할 7)|바이트|최대||없음|
|usedmemoryRss7|사용된 메모리 RSS(분할 7)|바이트|최대||없음|
|serverLoad7|서버 부하(분할 7)|백분율|최대||없음|
|cacheWrite7|캐시 쓰기(분할 7)|초당 바이트 수|최대||없음|
|cacheRead7|캐시 읽기(분할 7)|초당 바이트 수|최대||없음|
|percentProcessorTime7|CPU(분할 7)|백분율|최대||없음|
|connectedclients8|연결된 클라이언트(분할 8)|카운트|최대||없음|
|totalcommandsprocessed8|총 작업(분할 8)|카운트|합계||없음|
|cachehits8|캐시 적중(분할 8)|카운트|합계||없음|
|cachemisses8|캐시 누락(분할 8)|카운트|합계||없음|
|getcommands8|가져오기(분할 8)|카운트|합계||없음|
|setcommands8|설정(분할 8)|카운트|합계||없음|
|operationsPerSecond8|초당 작업(분할 8)|카운트|최대||없음|
|evictedkeys8|제거된 키(분할 8)|카운트|합계||없음|
|totalkeys8|총 키(분할 8)|카운트|최대||없음|
|expiredkeys8|만료된 키(분할 8)|카운트|합계||없음|
|usedmemory8|사용된 메모리(분할 8)|바이트|최대||없음|
|usedmemoryRss8|사용된 메모리 RSS(분할 8)|바이트|최대||없음|
|serverLoad8|서버 부하(분할 8)|백분율|최대||없음|
|cacheWrite8|캐시 쓰기(분할 8)|초당 바이트 수|최대||없음|
|cacheRead8|캐시 읽기(분할 8)|초당 바이트 수|최대||없음|
|percentProcessorTime8|CPU(분할 8)|백분율|최대||없음|
|connectedclients9|연결된 클라이언트(분할 9)|카운트|최대||없음|
|totalcommandsprocessed9|총 작업(분할 9)|카운트|합계||없음|
|cachehits9|캐시 적중(분할 9)|카운트|합계||없음|
|cachemisses9|캐시 누락(분할 9)|카운트|합계||없음|
|getcommands9|가져오기(분할 9)|카운트|합계||없음|
|setcommands9|설정(분할 9)|카운트|합계||없음|
|operationsPerSecond9|초당 작업(분할 9)|카운트|최대||없음|
|evictedkeys9|제거된 키(분할 9)|카운트|합계||없음|
|totalkeys9|총 키(분할 9)|카운트|최대||없음|
|expiredkeys9|만료된 키(분할 9)|카운트|합계||없음|
|usedmemory9|사용된 메모리(분할 9)|바이트|최대||없음|
|usedmemoryRss9|사용된 메모리 RSS(분할 9)|바이트|최대||없음|
|serverLoad9|서버 부하(분할 9)|백분율|최대||없음|
|cacheWrite9|캐시 쓰기(분할 9)|초당 바이트 수|최대||없음|
|cacheRead9|캐시 읽기(분할 9)|초당 바이트 수|최대||없음|
|percentProcessorTime9|CPU(분할 9)|백분율|최대||없음|



## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft Cdn/cdnwebapplicationfirewallpolicies

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|웹 애플리케이션 방화벽 요청 수|카운트|합계|웹 애플리케이션 방화벽에서 처리된 클라이언트 요청 수|PolicyName, RuleName, Action|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|백분율 CPU|백분율 CPU|백분율|평균|현재 Virtual Machine에서 사용 중인 할당된 컴퓨팅 단위의 백분율.|없음|
|네트워크 인|네트워크 인|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 수신한(들어오는 트래픽) 바이트 수.|없음|
|네트워크 아웃|네트워크 아웃|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 내보낸(나가는 트래픽) 바이트 수.|없음|
|디스크 읽기 바이트/초|디스크 읽기|초당 바이트 수|평균|모니터링 기간 동안 디스크에서 읽은 평균 바이트.|없음|
|디스크 쓰기 바이트/초|디스크 쓰기|초당 바이트 수|평균|모니터링 기간 동안 디스크에 쓴 평균 바이트.|없음|
|디스크 읽기 작업/초|디스크 읽기 작업/초|초당 개수|평균|디스크 읽기 IOPS.|없음|
|디스크 쓰기 작업/초|디스크 쓰기 작업/초|초당 개수|평균|디스크 쓰기 IOPS.|없음|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|백분율 CPU|백분율 CPU|백분율|평균|현재 Virtual Machine에서 사용 중인 할당된 컴퓨팅 단위의 백분율.|RoleInstanceId|
|네트워크 인|네트워크 인|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 수신한(들어오는 트래픽) 바이트 수.|RoleInstanceId|
|네트워크 아웃|네트워크 아웃|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 내보낸(나가는 트래픽) 바이트 수.|RoleInstanceId|
|디스크 읽기 바이트/초|디스크 읽기|초당 바이트 수|평균|모니터링 기간 동안 디스크에서 읽은 평균 바이트.|RoleInstanceId|
|디스크 쓰기 바이트/초|디스크 쓰기|초당 바이트 수|평균|모니터링 기간 동안 디스크에 쓴 평균 바이트.|RoleInstanceId|
|디스크 읽기 작업/초|디스크 읽기 작업/초|초당 개수|평균|디스크 읽기 IOPS.|RoleInstanceId|
|디스크 쓰기 작업/초|디스크 쓰기 작업/초|초당 개수|평균|디스크 쓰기 IOPS.|RoleInstanceId|



## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAccounts

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|UsedCapacity|사용된 용량|바이트|평균|계정 사용 용량|없음|
|트랜잭션|트랜잭션|카운트|합계|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|ResponseType, GeoType, ApiName, Authentication|
|수신|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|GeoType, ApiName, 인증|
|송신|송신|바이트|합계|송신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|GeoType, ApiName, 인증|
|SuccessServerLatency|성공 서버 대기 시간|밀리초|평균|성공한 요청을 처리 하기 위해 Azure Storage에서 사용 하는 대기 시간 (밀리초)입니다. 이 값은 SuccessE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|GeoType, ApiName, 인증|
|SuccessE2ELatency|성공 E2E 대기 시간|밀리초|평균|저장소 서비스 또는 지정 된 API 작업에 대해 수행 된 성공한 요청의 종단 간 대기 시간 (밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|GeoType, ApiName, 인증|
|가용성|가용성|백분율|평균|스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|GeoType, ApiName, 인증|

## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>ClassicStorage/storageAccounts/blobServices

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|BlobCapacity|Blob 용량|바이트|평균|스토리지 계정의 Blob service가 사용하는 스토리지의 양(바이트)입니다.|BlobType, 계층|
|BlobCount|Blob 수|카운트|평균|스토리지 계정의 Blob service에 있는 Blob 수입니다.|BlobType, 계층|
|ContainerCount|Blob 컨테이너 수|카운트|평균|스토리지 계정의 Blob service에 있는 컨테이너 수입니다.|없음|
|IndexCapacity|인덱스 용량|바이트|평균|ADLS Gen2 (계층적) 인덱스에서 사용 하는 저장소의 양 (바이트)입니다.|없음|
|트랜잭션|트랜잭션|카운트|합계|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|ResponseType, GeoType, ApiName, Authentication|
|수신|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|GeoType, ApiName, 인증|
|송신|송신|바이트|합계|송신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|GeoType, ApiName, 인증|
|SuccessServerLatency|성공 서버 대기 시간|밀리초|평균|성공한 요청을 처리 하기 위해 Azure Storage에서 사용 하는 대기 시간 (밀리초)입니다. 이 값은 SuccessE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|GeoType, ApiName, 인증|
|SuccessE2ELatency|성공 E2E 대기 시간|밀리초|평균|저장소 서비스 또는 지정 된 API 작업에 대해 수행 된 성공한 요청의 종단 간 대기 시간 (밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|GeoType, ApiName, 인증|
|가용성|가용성|백분율|평균|스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|GeoType, ApiName, 인증|

## <a name="microsoftclassicstoragestorageaccountstableservices"></a>ClassicStorage/storageAccounts/tableServices

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|TableCapacity|테이블 용량|바이트|평균|스토리지 계정의 Table service가 사용하는 스토리지의 양(바이트)입니다.|없음|
|TableCount|테이블 수|카운트|평균|스토리지 계정의 Table service에 있는 테이블 수입니다.|없음|
|TableEntityCount|테이블 엔터티 수|카운트|평균|스토리지 계정의 Table service에 있는 테이블 엔터티 수입니다.|없음|
|트랜잭션|트랜잭션|카운트|합계|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|ResponseType, GeoType, ApiName, Authentication|
|수신|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|GeoType, ApiName, 인증|
|송신|송신|바이트|합계|송신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|GeoType, ApiName, 인증|
|SuccessServerLatency|성공 서버 대기 시간|밀리초|평균|성공한 요청을 처리 하기 위해 Azure Storage에서 사용 하는 대기 시간 (밀리초)입니다. 이 값은 SuccessE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|GeoType, ApiName, 인증|
|SuccessE2ELatency|성공 E2E 대기 시간|밀리초|평균|저장소 서비스 또는 지정 된 API 작업에 대해 수행 된 성공한 요청의 종단 간 대기 시간 (밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|GeoType, ApiName, 인증|
|가용성|가용성|백분율|평균|스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|GeoType, ApiName, 인증|

## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>ClassicStorage/storageAccounts/fileServices

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|FileCapacity|파일 용량|바이트|평균|스토리지 계정의 파일 서비스가 사용하는 스토리지의 양(바이트)입니다.|FileShare|
|FileCount|파일 수|카운트|평균|스토리지 계정의 파일 서비스에 있는 파일 수입니다.|FileShare|
|FileShareCount|파일 공유 수|카운트|평균|스토리지 계정의 파일 서비스에 있는 파일 공유 수입니다.|없음|
|FileShareSnapshotCount|파일 공유 스냅숏 수|카운트|평균|저장소 계정의 파일 서비스에서 공유에 있는 스냅숏의 수입니다.|FileShare|
|FileShareSnapshotSize|파일 공유 스냅숏 크기|바이트|평균|저장소 계정의 파일 서비스에서 스냅숏에서 사용 되는 저장소의 크기 (바이트)입니다.|FileShare|
|FileShareQuota|파일 공유 할당량 크기|바이트|평균|Azure Files 서비스에서 사용할 수 있는 저장소 크기의 상한 (바이트)입니다.|FileShare|
|트랜잭션|트랜잭션|카운트|합계|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|ResponseType, GeoType, ApiName, Authentication, 파일 공유|
|수신|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|GeoType, ApiName, Authentication, 파일 공유|
|송신|송신|바이트|합계|송신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|GeoType, ApiName, Authentication, 파일 공유|
|SuccessServerLatency|성공 서버 대기 시간|밀리초|평균|성공한 요청을 처리 하기 위해 Azure Storage에서 사용 하는 대기 시간 (밀리초)입니다. 이 값은 SuccessE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|GeoType, ApiName, Authentication, 파일 공유|
|SuccessE2ELatency|성공 E2E 대기 시간|밀리초|평균|저장소 서비스 또는 지정 된 API 작업에 대해 수행 된 성공한 요청의 종단 간 대기 시간 (밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|GeoType, ApiName, Authentication, 파일 공유|
|가용성|가용성|백분율|평균|스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|GeoType, ApiName, Authentication, 파일 공유|

## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>ClassicStorage/storageAccounts/queueServices

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|QueueCapacity|큐 용량|바이트|평균|스토리지 계정의 큐 서비스가 사용하는 스토리지의 양(바이트)입니다.|없음|
|QueueCount|큐 수|카운트|평균|스토리지 계정의 큐 서비스에 있는 큐 수입니다.|없음|
|QueueMessageCount|큐 메시지 수|카운트|평균|스토리지 계정의 큐 서비스에 있는 대략적인 큐 메시지 수입니다.|없음|
|트랜잭션|트랜잭션|카운트|합계|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|ResponseType, GeoType, ApiName, Authentication|
|수신|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|GeoType, ApiName, 인증|
|송신|송신|바이트|합계|송신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|GeoType, ApiName, 인증|
|SuccessServerLatency|성공 서버 대기 시간|밀리초|평균|성공한 요청을 처리 하기 위해 Azure Storage에서 사용 하는 대기 시간 (밀리초)입니다. 이 값은 SuccessE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|GeoType, ApiName, 인증|
|SuccessE2ELatency|성공 E2E 대기 시간|밀리초|평균|저장소 서비스 또는 지정 된 API 작업에 대해 수행 된 성공한 요청의 종단 간 대기 시간 (밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|GeoType, ApiName, 인증|
|가용성|가용성|백분율|평균|스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|GeoType, ApiName, 인증|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|TotalCalls|총 호출|카운트|합계|총 호출 수.|ApiName, OperationName, 지역|
|SuccessfulCalls|성공한 호출|카운트|합계|성공한 호출 수입니다.|ApiName, OperationName, 지역|
|TotalErrors|총 오류|카운트|합계|오류 응답(HTTP 응답 코드 4xx 또는 5xx)이 있는 총 호출 수입니다.|ApiName, OperationName, 지역|
|BlockedCalls|차단된 호출|카운트|합계|요금 또는 할당량 한도를 초과한 호출 수입니다.|ApiName, OperationName, 지역|
|ServerErrors|서버 오류|카운트|합계|서비스 내부 오류(HTTP 응답 코드 5xx)가 있는 호출 수입니다.|ApiName, OperationName, 지역|
|ClientErrors|클라이언트 오류|카운트|합계|클라이언트 쪽 오류(HTTP 응답 코드 4xx)가 있는 호출 수입니다.|ApiName, OperationName, 지역|
|DataIn|데이터 입력|바이트|합계|들어오는 데이터 크기(바이트)입니다.|ApiName, OperationName, 지역|
|DataOut|데이터 출력|바이트|합계|나가는 데이터 크기(바이트)입니다.|ApiName, OperationName, 지역|
|대기 시간|대기 시간|밀리초|평균|대기 시간(밀리초)입니다.|ApiName, OperationName, 지역|
|CharactersTranslated|변환된 문자|카운트|합계|들어오는 텍스트 요청에 있는 문자의 총 수입니다.|ApiName, OperationName, 지역|
|CharactersTrained|학습 된 문자|카운트|합계|학습 된 총 문자 수입니다.|ApiName, OperationName, 지역|
|SpeechSessionDuration|음성 세션 기간|초|합계|음성 세션의 총 기간(초)입니다.|ApiName, OperationName, 지역|
|TotalTransactions|총 트랜잭션|카운트|합계|총 트랜잭션 수|없음|
|TotalTokenCalls|총 토큰 호출|카운트|합계|총 토큰 호출 수입니다.|ApiName, OperationName, 지역|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|백분율 CPU|백분율 CPU|백분율|평균|현재 Virtual Machine에서 사용 중인 할당된 컴퓨팅 단위의 백분율|없음|
|네트워크 인|청구 가능 네트워크 (사용 되지 않음)|바이트|합계|가상 머신에서 모든 네트워크 인터페이스에서 받은 청구 가능 바이트 수 (들어오는 트래픽) (사용 되지 않음)|없음|
|네트워크 아웃|네트워크 아웃 청구 가능 (사용 되지 않음)|바이트|합계|가상 컴퓨터의 모든 네트워크 인터페이스에서 청구 가능한 바이트 수 (나가는 트래픽) (사용 되지 않음)|없음|
|디스크 읽기 바이트|디스크 읽기 바이트|바이트|합계|모니터링 기간 동안 디스크에서 읽은 바이트 수|없음|
|디스크 쓰기 바이트|디스크 쓰기 바이트|바이트|합계|모니터링 기간 동안 디스크에 쓴 바이트 수|없음|
|디스크 읽기 작업/초|디스크 읽기 작업/초|초당 개수|평균|디스크 읽기 IOPS|없음|
|디스크 쓰기 작업/초|디스크 쓰기 작업/초|초당 개수|평균|디스크 쓰기 IOPS|없음|
|남은 CPU 크레딧|남은 CPU 크레딧|카운트|평균|버스트에 사용할 수 있는 총 크레딧 수|없음|
|사용된 CPU 크레딧|사용된 CPU 크레딧|카운트|평균|Virtual Machine에서 사용하는 총 크레딧 수|없음|
|디스크당 읽기 바이트/초|데이터 디스크 읽기 바이트/초 (사용 되지 않음)|초당 개수|평균|모니터링 기간에 단일 디스크에서 읽은 바이트/초|SlotId|
|디스크당 쓰기 바이트/초|데이터 디스크 쓰기 바이트/초 (사용 되지 않음)|초당 개수|평균|모니터링 기간 동안 단일 디스크에 쓴 바이트/초|SlotId|
|디스크당 읽기 작업/초|데이터 디스크 읽기 작업/초 (사용 되지 않음)|초당 개수|평균|모니터링 기간에 단일 디스크에서 IOPS 읽기|SlotId|
|디스크당 쓰기 작업/초|데이터 디스크 쓰기 작업/초 (사용 되지 않음)|초당 개수|평균|모니터링 기간 동안 단일 디스크의 쓰기 IOPS|SlotId|
|디스크당 QD|데이터 디스크 QD (사용 되지 않음)|카운트|평균|데이터 디스크 큐 깊이(또는 큐 길이)|SlotId|
|디스크당 OS 읽기 바이트/초|OS Disk Read Bytes/Sec (사용 되지 않음)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 읽은 바이트/초|없음|
|디스크당 OS 쓰기 바이트/초|OS Disk Write Bytes/Sec (사용 되지 않음)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에 쓴 바이트/초|없음|
|디스크당 OS 읽기 작업/초|OS 디스크 읽기 작업/초 (사용 되지 않음)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 IOPS 읽기|없음|
|디스크당 OS 쓰기 작업/초|OS 디스크 쓰기 작업/초 (사용 되지 않음)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 IOPS 쓰기|없음|
|디스크당 OS QD|OS 디스크 QD (사용 되지 않음)|카운트|평균|OS 디스크 큐 깊이(또는 큐 길이)|없음|
|Data Disk Read Bytes/sec|데이터 디스크 읽기 바이트/초(미리 보기)|초당 개수|평균|모니터링 기간에 단일 디스크에서 읽은 바이트/초|LUN|
|Data Disk Write Bytes/sec|데이터 디스크 쓰기 바이트/초(미리 보기)|초당 개수|평균|모니터링 기간 동안 단일 디스크에 쓴 바이트/초|LUN|
|데이터 디스크 읽기 작업/초|데이터 디스크 읽기 작업/초(미리 보기)|초당 개수|평균|모니터링 기간에 단일 디스크에서 IOPS 읽기|LUN|
|데이터 디스크 쓰기 작업/초|데이터 디스크 쓰기 작업/초(미리 보기)|초당 개수|평균|모니터링 기간 동안 단일 디스크의 쓰기 IOPS|LUN|
|데이터 디스크 큐 크기|데이터 디스크 큐 깊이 (미리 보기)|카운트|평균|데이터 디스크 큐 깊이(또는 큐 길이)|LUN|
|OS Disk Read Bytes/sec|OS 디스크 읽기 바이트/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 읽은 바이트/초|없음|
|OS 디스크 쓰기 바이트/초|OS 디스크 쓰기 바이트/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에 쓴 바이트/초|없음|
|OS 디스크 읽기 작업/초|OS 디스크 읽기 작업/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 IOPS 읽기|없음|
|OS 디스크 쓰기 작업/초|OS 디스크 쓰기 작업/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 IOPS 쓰기|없음|
|OS 디스크 큐 크기|OS 디스크 큐 깊이 (미리 보기)|카운트|평균|OS 디스크 큐 깊이(또는 큐 길이)|없음|
|인바운드 흐름|인바운드 흐름|카운트|평균|인바운드 흐름은 인바운드 방향의 현재 흐름 수 (VM으로 이동 하는 트래픽)입니다.|없음|
|아웃 바운드 흐름|아웃 바운드 흐름|카운트|평균|아웃 바운드 흐름은 아웃 바운드 방향의 현재 흐름 수 (VM에서 나가는 트래픽)입니다.|없음|
|인바운드 흐름 최대 생성 빈도|인바운드 흐름 최대 생성 빈도 (미리 보기)|초당 개수|평균|인바운드 흐름의 최대 생성 률 (VM으로 이동 하는 트래픽)|없음|
|아웃 바운드 흐름 최대 생성 빈도|아웃 바운드 흐름 최대 생성 빈도 (미리 보기)|초당 개수|평균|아웃 바운드 흐름의 최대 생성 률 (VM에서 나가는 트래픽)|없음|
|프리미엄 데이터 디스크 캐시 읽기 적중|프리미엄 데이터 디스크 캐시 읽기 적중 (미리 보기)|백분율|평균|프리미엄 데이터 디스크 캐시 읽기 적중|LUN|
|프리미엄 데이터 디스크 캐시 읽기 누락|프리미엄 데이터 디스크 캐시 읽기 누락 (미리 보기)|백분율|평균|프리미엄 데이터 디스크 캐시 읽기 누락|LUN|
|프리미엄 OS 디스크 캐시 읽기 적중|프리미엄 OS 디스크 캐시 읽기 적중 (미리 보기)|백분율|평균|프리미엄 OS 디스크 캐시 읽기 적중|없음|
|프리미엄 OS 디스크 캐시 읽기 누락|프리미엄 OS 디스크 캐시 읽기 누락 (미리 보기)|백분율|평균|프리미엄 OS 디스크 캐시 읽기 누락|없음|
|총 네트워크|총 네트워크|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 수신한(들어오는 트래픽) 바이트 수|없음|
|총 네트워크|총 네트워크|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 내보낸(나가는 트래픽) 바이트 수|없음|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|백분율 CPU|백분율 CPU|백분율|평균|현재 Virtual Machine에서 사용 중인 할당된 컴퓨팅 단위의 백분율|VMName|
|네트워크 인|청구 가능 네트워크 (사용 되지 않음)|바이트|합계|가상 머신에서 모든 네트워크 인터페이스에서 받은 청구 가능 바이트 수 (들어오는 트래픽) (사용 되지 않음)|VMName|
|네트워크 아웃|네트워크 아웃 청구 가능 (사용 되지 않음)|바이트|합계|가상 컴퓨터의 모든 네트워크 인터페이스에서 청구 가능한 바이트 수 (나가는 트래픽) (사용 되지 않음)|VMName|
|디스크 읽기 바이트|디스크 읽기 바이트|바이트|합계|모니터링 기간 동안 디스크에서 읽은 바이트 수|VMName|
|디스크 쓰기 바이트|디스크 쓰기 바이트|바이트|합계|모니터링 기간 동안 디스크에 쓴 바이트 수|VMName|
|디스크 읽기 작업/초|디스크 읽기 작업/초|초당 개수|평균|디스크 읽기 IOPS|VMName|
|디스크 쓰기 작업/초|디스크 쓰기 작업/초|초당 개수|평균|디스크 쓰기 IOPS|VMName|
|남은 CPU 크레딧|남은 CPU 크레딧|카운트|평균|버스트에 사용할 수 있는 총 크레딧 수|없음|
|사용된 CPU 크레딧|사용된 CPU 크레딧|카운트|평균|Virtual Machine에서 사용하는 총 크레딧 수|없음|
|디스크당 읽기 바이트/초|데이터 디스크 읽기 바이트/초 (사용 되지 않음)|초당 개수|평균|모니터링 기간에 단일 디스크에서 읽은 바이트/초|SlotId|
|디스크당 쓰기 바이트/초|데이터 디스크 쓰기 바이트/초 (사용 되지 않음)|초당 개수|평균|모니터링 기간 동안 단일 디스크에 쓴 바이트/초|SlotId|
|디스크당 읽기 작업/초|데이터 디스크 읽기 작업/초 (사용 되지 않음)|초당 개수|평균|모니터링 기간에 단일 디스크에서 IOPS 읽기|SlotId|
|디스크당 쓰기 작업/초|데이터 디스크 쓰기 작업/초 (사용 되지 않음)|초당 개수|평균|모니터링 기간 동안 단일 디스크의 쓰기 IOPS|SlotId|
|디스크당 QD|데이터 디스크 QD (사용 되지 않음)|카운트|평균|데이터 디스크 큐 깊이(또는 큐 길이)|SlotId|
|디스크당 OS 읽기 바이트/초|OS Disk Read Bytes/Sec (사용 되지 않음)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 읽은 바이트/초|없음|
|디스크당 OS 쓰기 바이트/초|OS Disk Write Bytes/Sec (사용 되지 않음)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에 쓴 바이트/초|없음|
|디스크당 OS 읽기 작업/초|OS 디스크 읽기 작업/초 (사용 되지 않음)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 IOPS 읽기|없음|
|디스크당 OS 쓰기 작업/초|OS 디스크 쓰기 작업/초 (사용 되지 않음)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 IOPS 쓰기|없음|
|디스크당 OS QD|OS 디스크 QD (사용 되지 않음)|카운트|평균|OS 디스크 큐 깊이(또는 큐 길이)|없음|
|Data Disk Read Bytes/sec|데이터 디스크 읽기 바이트/초(미리 보기)|초당 개수|평균|모니터링 기간에 단일 디스크에서 읽은 바이트/초|LUN, VMName|
|Data Disk Write Bytes/sec|데이터 디스크 쓰기 바이트/초(미리 보기)|초당 개수|평균|모니터링 기간 동안 단일 디스크에 쓴 바이트/초|LUN, VMName|
|데이터 디스크 읽기 작업/초|데이터 디스크 읽기 작업/초(미리 보기)|초당 개수|평균|모니터링 기간에 단일 디스크에서 IOPS 읽기|LUN, VMName|
|데이터 디스크 쓰기 작업/초|데이터 디스크 쓰기 작업/초(미리 보기)|초당 개수|평균|모니터링 기간 동안 단일 디스크의 쓰기 IOPS|LUN, VMName|
|데이터 디스크 큐 크기|데이터 디스크 큐 깊이 (미리 보기)|카운트|평균|데이터 디스크 큐 깊이(또는 큐 길이)|LUN, VMName|
|OS Disk Read Bytes/sec|OS 디스크 읽기 바이트/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 읽은 바이트/초|VMName|
|OS 디스크 쓰기 바이트/초|OS 디스크 쓰기 바이트/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에 쓴 바이트/초|VMName|
|OS 디스크 읽기 작업/초|OS 디스크 읽기 작업/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 IOPS 읽기|VMName|
|OS 디스크 쓰기 작업/초|OS 디스크 쓰기 작업/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 IOPS 쓰기|VMName|
|OS 디스크 큐 크기|OS 디스크 큐 깊이 (미리 보기)|카운트|평균|OS 디스크 큐 깊이(또는 큐 길이)|VMName|
|인바운드 흐름|인바운드 흐름|카운트|평균|인바운드 흐름은 인바운드 방향의 현재 흐름 수 (VM으로 이동 하는 트래픽)입니다.|VMName|
|아웃 바운드 흐름|아웃 바운드 흐름|카운트|평균|아웃 바운드 흐름은 아웃 바운드 방향의 현재 흐름 수 (VM에서 나가는 트래픽)입니다.|VMName|
|인바운드 흐름 최대 생성 빈도|인바운드 흐름 최대 생성 빈도 (미리 보기)|초당 개수|평균|인바운드 흐름의 최대 생성 률 (VM으로 이동 하는 트래픽)|VMName|
|아웃 바운드 흐름 최대 생성 빈도|아웃 바운드 흐름 최대 생성 빈도 (미리 보기)|초당 개수|평균|아웃 바운드 흐름의 최대 생성 률 (VM에서 나가는 트래픽)|VMName|
|프리미엄 데이터 디스크 캐시 읽기 적중|프리미엄 데이터 디스크 캐시 읽기 적중 (미리 보기)|백분율|평균|프리미엄 데이터 디스크 캐시 읽기 적중|LUN, VMName|
|프리미엄 데이터 디스크 캐시 읽기 누락|프리미엄 데이터 디스크 캐시 읽기 누락 (미리 보기)|백분율|평균|프리미엄 데이터 디스크 캐시 읽기 누락|LUN, VMName|
|프리미엄 OS 디스크 캐시 읽기 적중|프리미엄 OS 디스크 캐시 읽기 적중 (미리 보기)|백분율|평균|프리미엄 OS 디스크 캐시 읽기 적중|VMName|
|프리미엄 OS 디스크 캐시 읽기 누락|프리미엄 OS 디스크 캐시 읽기 누락 (미리 보기)|백분율|평균|프리미엄 OS 디스크 캐시 읽기 누락|VMName|
|총 네트워크|총 네트워크|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 수신한(들어오는 트래픽) 바이트 수|VMName|
|총 네트워크|총 네트워크|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 내보낸(나가는 트래픽) 바이트 수|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|백분율 CPU|백분율 CPU|백분율|평균|현재 Virtual Machine에서 사용 중인 할당된 컴퓨팅 단위의 백분율|없음|
|네트워크 인|청구 가능 네트워크 (사용 되지 않음)|바이트|합계|가상 머신에서 모든 네트워크 인터페이스에서 받은 청구 가능 바이트 수 (들어오는 트래픽) (사용 되지 않음)|없음|
|네트워크 아웃|네트워크 아웃 청구 가능 (사용 되지 않음)|바이트|합계|가상 컴퓨터의 모든 네트워크 인터페이스에서 청구 가능한 바이트 수 (나가는 트래픽) (사용 되지 않음)|없음|
|디스크 읽기 바이트|디스크 읽기 바이트|바이트|합계|모니터링 기간 동안 디스크에서 읽은 바이트 수|없음|
|디스크 쓰기 바이트|디스크 쓰기 바이트|바이트|합계|모니터링 기간 동안 디스크에 쓴 바이트 수|없음|
|디스크 읽기 작업/초|디스크 읽기 작업/초|초당 개수|평균|디스크 읽기 IOPS|없음|
|디스크 쓰기 작업/초|디스크 쓰기 작업/초|초당 개수|평균|디스크 쓰기 IOPS|없음|
|남은 CPU 크레딧|남은 CPU 크레딧|카운트|평균|버스트에 사용할 수 있는 총 크레딧 수|없음|
|사용된 CPU 크레딧|사용된 CPU 크레딧|카운트|평균|Virtual Machine에서 사용하는 총 크레딧 수|없음|
|디스크당 읽기 바이트/초|데이터 디스크 읽기 바이트/초 (사용 되지 않음)|초당 개수|평균|모니터링 기간에 단일 디스크에서 읽은 바이트/초|SlotId|
|디스크당 쓰기 바이트/초|데이터 디스크 쓰기 바이트/초 (사용 되지 않음)|초당 개수|평균|모니터링 기간 동안 단일 디스크에 쓴 바이트/초|SlotId|
|디스크당 읽기 작업/초|데이터 디스크 읽기 작업/초 (사용 되지 않음)|초당 개수|평균|모니터링 기간에 단일 디스크에서 IOPS 읽기|SlotId|
|디스크당 쓰기 작업/초|데이터 디스크 쓰기 작업/초 (사용 되지 않음)|초당 개수|평균|모니터링 기간 동안 단일 디스크의 쓰기 IOPS|SlotId|
|디스크당 QD|데이터 디스크 QD (사용 되지 않음)|카운트|평균|데이터 디스크 큐 깊이(또는 큐 길이)|SlotId|
|디스크당 OS 읽기 바이트/초|OS Disk Read Bytes/Sec (사용 되지 않음)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 읽은 바이트/초|없음|
|디스크당 OS 쓰기 바이트/초|OS Disk Write Bytes/Sec (사용 되지 않음)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에 쓴 바이트/초|없음|
|디스크당 OS 읽기 작업/초|OS 디스크 읽기 작업/초 (사용 되지 않음)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 IOPS 읽기|없음|
|디스크당 OS 쓰기 작업/초|OS 디스크 쓰기 작업/초 (사용 되지 않음)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 IOPS 쓰기|없음|
|디스크당 OS QD|OS 디스크 QD (사용 되지 않음)|카운트|평균|OS 디스크 큐 깊이(또는 큐 길이)|없음|
|Data Disk Read Bytes/sec|데이터 디스크 읽기 바이트/초(미리 보기)|초당 개수|평균|모니터링 기간에 단일 디스크에서 읽은 바이트/초|LUN|
|Data Disk Write Bytes/sec|데이터 디스크 쓰기 바이트/초(미리 보기)|초당 개수|평균|모니터링 기간 동안 단일 디스크에 쓴 바이트/초|LUN|
|데이터 디스크 읽기 작업/초|데이터 디스크 읽기 작업/초(미리 보기)|초당 개수|평균|모니터링 기간에 단일 디스크에서 IOPS 읽기|LUN|
|데이터 디스크 쓰기 작업/초|데이터 디스크 쓰기 작업/초(미리 보기)|초당 개수|평균|모니터링 기간 동안 단일 디스크의 쓰기 IOPS|LUN|
|데이터 디스크 큐 크기|데이터 디스크 큐 깊이 (미리 보기)|카운트|평균|데이터 디스크 큐 깊이(또는 큐 길이)|LUN|
|OS Disk Read Bytes/sec|OS 디스크 읽기 바이트/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 읽은 바이트/초|없음|
|OS 디스크 쓰기 바이트/초|OS 디스크 쓰기 바이트/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에 쓴 바이트/초|없음|
|OS 디스크 읽기 작업/초|OS 디스크 읽기 작업/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 IOPS 읽기|없음|
|OS 디스크 쓰기 작업/초|OS 디스크 쓰기 작업/초(미리 보기)|초당 개수|평균|OS 디스크의 모니터링 기간에 단일 디스크에서 IOPS 쓰기|없음|
|OS 디스크 큐 크기|OS 디스크 큐 깊이 (미리 보기)|카운트|평균|OS 디스크 큐 깊이(또는 큐 길이)|없음|
|인바운드 흐름|인바운드 흐름|카운트|평균|인바운드 흐름은 인바운드 방향의 현재 흐름 수 (VM으로 이동 하는 트래픽)입니다.|없음|
|아웃 바운드 흐름|아웃 바운드 흐름|카운트|평균|아웃 바운드 흐름은 아웃 바운드 방향의 현재 흐름 수 (VM에서 나가는 트래픽)입니다.|없음|
|인바운드 흐름 최대 생성 빈도|인바운드 흐름 최대 생성 빈도 (미리 보기)|초당 개수|평균|인바운드 흐름의 최대 생성 률 (VM으로 이동 하는 트래픽)|없음|
|아웃 바운드 흐름 최대 생성 빈도|아웃 바운드 흐름 최대 생성 빈도 (미리 보기)|초당 개수|평균|아웃 바운드 흐름의 최대 생성 률 (VM에서 나가는 트래픽)|없음|
|프리미엄 데이터 디스크 캐시 읽기 적중|프리미엄 데이터 디스크 캐시 읽기 적중 (미리 보기)|백분율|평균|프리미엄 데이터 디스크 캐시 읽기 적중|LUN|
|프리미엄 데이터 디스크 캐시 읽기 누락|프리미엄 데이터 디스크 캐시 읽기 누락 (미리 보기)|백분율|평균|프리미엄 데이터 디스크 캐시 읽기 누락|LUN|
|프리미엄 OS 디스크 캐시 읽기 적중|프리미엄 OS 디스크 캐시 읽기 적중 (미리 보기)|백분율|평균|프리미엄 OS 디스크 캐시 읽기 적중|없음|
|프리미엄 OS 디스크 캐시 읽기 누락|프리미엄 OS 디스크 캐시 읽기 누락 (미리 보기)|백분율|평균|프리미엄 OS 디스크 캐시 읽기 누락|없음|
|총 네트워크|총 네트워크|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 수신한(들어오는 트래픽) 바이트 수|없음|
|총 네트워크|총 네트워크|바이트|합계|Virtual Machine이 모든 네트워크 인터페이스에서 내보낸(나가는 트래픽) 바이트 수|없음|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|CpuUsage|CPU 사용량|카운트|평균|모든 코어의 CPU 사용량(밀리코어)|containerName|
|MemoryUsage|메모리 사용량|바이트|평균|총 메모리 사용량(바이트)|containerName|
|NetworkBytesReceivedPerSecond|초당 수신된 네트워크 바이트|바이트|평균|초당 수신된 네트워크 바이트입니다.|없음|
|NetworkBytesTransmittedPerSecond|초당 전송된 네트워크 바이트|바이트|평균|초당 전송된 네트워크 바이트입니다.|없음|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|TotalPullCount|총 풀 수|카운트|평균|총 이미지 가져오기 수|없음|
|SuccessfulPullCount|성공적인 끌어오기 수|카운트|평균|성공한 이미지 끌어오기 수|없음|
|TotalPushCount|총 푸시 수|카운트|평균|총 이미지 푸시 수|없음|
|SuccessfulPushCount|성공한 푸시 수|카운트|평균|성공한 이미지 푸시 수|없음|
|RunDuration|실행 지속 시간|밀리초|합계|실행 지속 시간 (밀리초)|없음|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|관리 클러스터에서 사용 가능한 cpu 코어의 총 수|카운트|평균|관리 클러스터에서 사용 가능한 cpu 코어의 총 수|없음|
|kube_node_status_allocatable_memory_bytes|관리 클러스터에서 사용 가능한 총 메모리 양|바이트|평균|관리 클러스터에서 사용 가능한 총 메모리 양|없음|
|kube_pod_status_ready|준비 상태인 Pod 수|카운트|평균|준비 상태인 Pod 수|네임 스페이스, pod|
|kube_node_status_condition|다양한 노드 조건에 대한 상태|카운트|평균|다양한 노드 조건에 대한 상태|조건, 상태, status2, 노드|
|kube_pod_status_phase|단계별 Pod 수|카운트|평균|단계별 Pod 수|단계, 네임 스페이스, pod|



## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft CustomProviders/resourceproviders

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|SuccessfullRequests|성공한 요청|카운트|합계|사용자 지정 공급자가 수행한 성공한 요청|HttpMethod, CallPath, StatusCode|
|FailedRequests|실패한 요청|카운트|합계|사용자 지정 리소스 공급자에 대해 사용 가능한 로그를 가져옵니다.|HttpMethod, CallPath, StatusCode|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|NICReadThroughput|읽기 처리량 (네트워크)|초당 바이트 수|평균|게이트웨이의 모든 볼륨에 대 한 보고 기간의 장치에서 네트워크 인터페이스의 읽기 처리량입니다.|InstanceName|
|NICWriteThroughput|쓰기 처리량 (네트워크)|초당 바이트 수|평균|게이트웨이의 모든 볼륨에 대 한 보고 기간의 장치에서 네트워크 인터페이스의 쓰기 처리량입니다.|InstanceName|
|CloudReadThroughputPerShare|클라우드 다운로드 처리량 (공유)|초당 바이트 수|평균|보고 기간 동안 공유에서 Azure로의 다운로드 처리량입니다.|공유|
|CloudUploadThroughputPerShare|클라우드 업로드 처리량 (공유)|초당 바이트 수|평균|보고 기간 동안 공유에서 Azure로의 업로드 처리량입니다.|공유|
|BytesUploadedToCloudPerShare|업로드 된 클라우드 바이트 (공유)|바이트|평균|보고 기간 동안 공유에서 Azure로 업로드 되는 총 바이트 수입니다.|공유|
|총 용량이|총 용량|바이트|평균|총 용량|없음|
|AvailableCapacity|사용 가능한 용량|바이트|평균|보고 기간 동안 사용 가능한 용량 (바이트)입니다.|없음|
|CloudUploadThroughput|클라우드 업로드 처리량|초당 바이트 수|평균|보고 기간 동안 Azure에 대 한 클라우드 업로드 처리량입니다.|없음|
|CloudReadThroughput|클라우드 다운로드 처리량|초당 바이트 수|평균|보고 기간 동안 Azure에 대 한 클라우드 다운로드 처리량입니다.|없음|
|BytesUploadedToCloud|업로드 된 클라우드 바이트 (장치)|바이트|평균|보고 기간 동안 장치에서 Azure로 업로드 되는 총 바이트 수입니다.|없음|
|HyperVVirtualProcessorUtilization|Edge 계산-CPU 비율|백분율|평균|CPU 사용률 (%)|InstanceName|
|HyperVMemoryUtilization|Edge 계산-메모리 사용량|백분율|평균|사용 중인 RAM의 양|InstanceName|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|FailedRuns|실패한 실행|카운트|합계||pipelineName, activityName|
|SuccessfulRuns|성공한 실행|카운트|합계||pipelineName, activityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|PipelineFailedRuns|실패한 파이프라인 실행 메트릭|카운트|합계||FailureType, 이름|
|PipelineSucceededRuns|성공한 파이프라인 실행 메트릭|카운트|합계||FailureType, 이름|
|PipelineCancelledRuns|취소 한 파이프라인 실행 메트릭|카운트|합계||FailureType, 이름|
|ActivityFailedRuns|실패한 활동 실행 메트릭|카운트|합계||ActivityType, PipelineName, FailureType, Name|
|ActivitySucceededRuns|성공한 활동 실행 메트릭|카운트|합계||ActivityType, PipelineName, FailureType, Name|
|ActivityCancelledRuns|취소 한 작업에서 메트릭 실행|카운트|합계||ActivityType, PipelineName, FailureType, Name|
|TriggerFailedRuns|실패한 트리거 실행 메트릭|카운트|합계||이름, FailureType|
|TriggerSucceededRuns|성공한 트리거 실행 메트릭|카운트|합계||이름, FailureType|
|TriggerCancelledRuns|취소 한 트리거 실행 메트릭|카운트|합계||이름, FailureType|
|IntegrationRuntimeCpuPercentage|통합 런타임 CPU 사용률|백분율|평균||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableMemory|통합 런타임 사용 가능한 메모리|바이트|평균||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAverageTaskPickupDelay|Integration runtime 큐 기간|초|평균||IntegrationRuntimeName|
|IntegrationRuntimeQueueLength|Integration runtime 큐 길이|카운트|평균||IntegrationRuntimeName|
|IntegrationRuntimeAvailableNodeNumber|Integration runtime 사용 가능한 노드 수|카운트|평균||IntegrationRuntimeName|
|MaxAllowedResourceCount|허용 되는 최대 엔터티 수|카운트|최대||없음|
|MaxAllowedFactorySizeInGbUnits|최대 허용 팩터리 크기 (GB 단위)|카운트|최대||없음|
|ResourceCount|총 엔터티 수|카운트|최대||없음|
|FactorySizeInGbUnits|총 팩터리 크기 (GB 단위)|카운트|최대||없음|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|JobEndedSuccess|성공한 작업|카운트|합계|성공한 작업의 수입니다.|없음|
|JobEndedFailure|실패한 작업|카운트|합계|실패한 작업 수입니다.|없음|
|JobEndedCancelled|취소된 작업|카운트|합계|취소된 작업 수입니다.|없음|
|JobAUEndedSuccess|성공한 AU 시간|초|합계|성공한 작업에 대한 총 AU 시간입니다.|없음|
|JobAUEndedFailure|실패한 AU 시간|초|합계|실패한 작업에 대한 총 AU 시간입니다.|없음|
|JobAUEndedCancelled|취소된 AU 시간|초|합계|취소된 작업에 대한 총 AU 시간입니다.|없음|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|TotalStorage|총 스토리지|바이트|최대|계정에 저장된 총 데이터 양.|없음|
|DataWritten|기록된 데이터|바이트|합계|계정에 기록된 총 데이터 양.|없음|
|DataRead|데이터 읽기|바이트|합계|계정에서 읽어 온 총 데이터 양.|없음|
|WriteRequests|쓰기 요청|카운트|합계|계정에 데이터 쓰기 요청 수.|없음|
|ReadRequests|읽기 요청|카운트|합계|계정에 데이터 읽기 요청 수.|없음|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|cpu_percent|CPU 백분율|백분율|평균|CPU 백분율|없음|
|memory_percent|메모리 백분율|백분율|평균|메모리 백분율|없음|
|io_consumption_percent|IO 백분율|백분율|평균|IO 백분율|없음|
|storage_percent|스토리지 비율|백분율|평균|스토리지 비율|없음|
|storage_used|사용된 스토리지|바이트|평균|사용된 스토리지|없음|
|storage_limit|스토리지 제한|바이트|최대|스토리지 제한|없음|
|serverlog_storage_percent|서버 로그 스토리지 비율|백분율|평균|서버 로그 스토리지 비율|없음|
|serverlog_storage_percent|사용된 서버 로그 스토리지|바이트|평균|사용된 서버 로그 스토리지|없음|
|serverlog_storage_limit|서버 로그 스토리지 제한|바이트|평균|서버 로그 스토리지 제한|없음|
|active_connections|활성 연결 수|카운트|평균|활성 연결 수|없음|
|connections_failed|실패한 연결|카운트|합계|실패한 연결|없음|
|seconds_behind_master|복제 지연 시간(초)|카운트|최대|복제 지연 시간(초)|없음|
|backup_storage_used|사용 되는 백업 저장소|바이트|평균|사용 되는 백업 저장소|없음|
|network_bytes_egress|네트워크 아웃|바이트|합계|활성 연결에서 네트워크 출력|없음|
|network_bytes_ingress|네트워크 인|바이트|합계|활성 연결에서 네트워크 입력|없음|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|cpu_percent|CPU 백분율|백분율|평균|CPU 백분율|없음|
|memory_percent|메모리 백분율|백분율|평균|메모리 백분율|없음|
|io_consumption_percent|IO 백분율|백분율|평균|IO 백분율|없음|
|storage_percent|스토리지 비율|백분율|평균|스토리지 비율|없음|
|storage_used|사용된 스토리지|바이트|평균|사용된 스토리지|없음|
|storage_limit|스토리지 제한|바이트|최대|스토리지 제한|없음|
|serverlog_storage_percent|서버 로그 스토리지 비율|백분율|평균|서버 로그 스토리지 비율|없음|
|serverlog_storage_percent|사용된 서버 로그 스토리지|바이트|평균|사용된 서버 로그 스토리지|없음|
|serverlog_storage_limit|서버 로그 스토리지 제한|바이트|최대|서버 로그 스토리지 제한|없음|
|active_connections|활성 연결 수|카운트|평균|활성 연결 수|없음|
|connections_failed|실패한 연결|카운트|합계|실패한 연결|없음|
|seconds_behind_master|복제 지연 시간(초)|카운트|최대|복제 지연 시간(초)|없음|
|backup_storage_used|사용 되는 백업 저장소|바이트|평균|사용 되는 백업 저장소|없음|
|network_bytes_egress|네트워크 아웃|바이트|합계|활성 연결에서 네트워크 출력|없음|
|network_bytes_ingress|네트워크 인|바이트|합계|활성 연결에서 네트워크 입력|없음|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|cpu_percent|CPU 백분율|백분율|평균|CPU 백분율|없음|
|memory_percent|메모리 백분율|백분율|평균|메모리 백분율|없음|
|io_consumption_percent|IO 백분율|백분율|평균|IO 백분율|없음|
|storage_percent|스토리지 비율|백분율|평균|스토리지 비율|없음|
|storage_used|사용된 스토리지|바이트|평균|사용된 스토리지|없음|
|storage_limit|스토리지 제한|바이트|최대|스토리지 제한|없음|
|serverlog_storage_percent|서버 로그 스토리지 비율|백분율|평균|서버 로그 스토리지 비율|없음|
|serverlog_storage_percent|사용된 서버 로그 스토리지|바이트|평균|사용된 서버 로그 스토리지|없음|
|serverlog_storage_limit|서버 로그 스토리지 제한|바이트|최대|서버 로그 스토리지 제한|없음|
|active_connections|활성 연결 수|카운트|평균|활성 연결 수|없음|
|connections_failed|실패한 연결|카운트|합계|실패한 연결|없음|
|backup_storage_used|사용 되는 백업 저장소|바이트|평균|사용 되는 백업 저장소|없음|
|network_bytes_egress|네트워크 아웃|바이트|합계|활성 연결에서 네트워크 출력|없음|
|network_bytes_ingress|네트워크 인|바이트|합계|활성 연결에서 네트워크 입력|없음|
|pg_replica_log_delay_in_seconds|복제본 지연 시간|초|최대|복제본 지연 (초)|없음|
|pg_replica_log_delay_in_bytes|복제본 간 최대 지연 시간|바이트|최대|가장 지연 복제본의 지연 (바이트)|없음|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|cpu_percent|CPU 백분율|백분율|평균|CPU 백분율|없음|
|memory_percent|메모리 백분율|백분율|평균|메모리 백분율|없음|
|사서함당|IOPS|카운트|평균|초당 IO 작업 수|없음|
|storage_percent|스토리지 비율|백분율|평균|스토리지 비율|없음|
|storage_used|사용된 스토리지|바이트|평균|사용된 스토리지|없음|
|active_connections|활성 연결 수|카운트|평균|활성 연결 수|없음|
|network_bytes_egress|네트워크 아웃|바이트|합계|활성 연결에서 네트워크 출력|없음|
|network_bytes_ingress|네트워크 인|바이트|합계|활성 연결에서 네트워크 입력|없음|





## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|원격 분석 메시지 보내기 시도|카운트|합계|IoT Hub로 보내려 한 디바이스-클라우드 원격 분석 메시지 수|없음|
|d2c.telemetry.ingress.success|보낸 원격 분석 메시지|카운트|합계|IoT Hub로 보내기 성공한 디바이스-클라우드 원격 분석 메시지 수|없음|
|c2d.commands.egress.complete.success|C2D 메시지 배달이 완료 됨|카운트|합계|장치에서 성공적으로 완료 한 클라우드-장치 메시지 배달 수|없음|
|c2d.commands.egress.abandon.success|C2D 메시지 중단 됨|카운트|합계|장치에서 중단 한 클라우드-장치 메시지 수|없음|
|c2d.commands.egress.reject.success|거부 된 C2D 메시지|카운트|합계|장치에서 거부 한 클라우드-장치 메시지 수|없음|
|C2DMessagesExpired|C2D 메시지 만료 됨 (미리 보기)|카운트|합계|만료 된 클라우드-장치 메시지 수|없음|
|devices.totalDevices|총 디바이스(사용되지 않음)|카운트|합계|IoT 허브에 등록된 디바이스 수|없음|
|devices.connectedDevices.allProtocol|연결된 디바이스(사용되지 않음) |카운트|합계|IoT 허브에 연결된 디바이스 수|없음|
|d2c.telemetry.egress.success|라우팅: 배달된 원격 분석 메시지|카운트|합계|IoT Hub 라우팅을 사용하여 모든 엔드포인트에 메시지가 성공적으로 배달된 횟수입니다. 메시지가 여러 엔드포인트로 라우팅되는 경우 이 값은 각 성공적인 배달에 대해 하나씩 증가합니다. 메시지가 동일한 엔드포인트로 여러 번 배달되는 경우 이 값은 각 성공적인 배달에 대해 하나씩 증가합니다.|없음|
|d2c.telemetry.egress.dropped|라우팅: 삭제된 원격 분석 메시지 |카운트|합계|데드 엔드포인트로 인해 IoT Hub 라우팅에서 메시지가 삭제된 횟수입니다. 삭제된 메시지는 배달되지 않으므로 이 값은 대체 경로에 배달된 메시지를 계산에 넣지 않습니다.|없음|
|d2c.telemetry.egress.orphaned|라우팅: 분리된 원격 분석 메시지 |카운트|합계|메시지가 라우팅 규칙(대체 규칙 포함)과 일치하지 않았으므로 IoT Hub 라우팅에 의해 분리된 횟수입니다. |없음|
|d2c.telemetry.egress.invalid|라우팅: 원격 분석 메시지 호환되지 않음|카운트|합계|IoT Hub 라우팅에서 엔드포인트와의 비호환성으로 인해 메시지를 배달하지 못한 횟수입니다. 이 값은 재시도를 포함하지 않습니다.|없음|
|d2c.telemetry.egress.fallback|라우팅: 대체에 배달된 메시지|카운트|합계|IoT Hub 라우팅에서 대체 경로와 연결된 엔드포인트에 메시지를 배달한 횟수입니다.|없음|
|d2c.endpoints.egress.eventHubs|라우팅: 이벤트 허브에 배달된 메시지|카운트|합계|IoT Hub 라우팅에서 이벤트 허브 엔드포인트에 메시지를 성공적으로 배달한 횟수입니다.|없음|
|d2c.endpoints.latency.eventHubs|라우팅: 이벤트 허브에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 이벤트 허브 엔드포인트에 대한 메시지 수신 간의 평균 대기 시간(밀리초)입니다.|없음|
|d2c.endpoints.egress.serviceBusQueues|라우팅: Service Bus 큐에 배달된 메시지|카운트|합계|IoT Hub 라우팅에서 Service Bus 큐 엔드포인트에 메시지를 성공적으로 배달한 횟수입니다.|없음|
|d2c.endpoints.latency.serviceBusQueues|라우팅: Service Bus 큐에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 Service Bus 큐 엔드포인트에 대한 원격 분석 메시지 수신 간의 평균 대기 시간(밀리초)입니다.|없음|
|d2c.endpoints.egress.serviceBusTopics|라우팅: Service Bus 토픽에 배달된 메시지|카운트|합계|IoT Hub 라우팅에서 Service Bus 토픽 엔드포인트에 메시지를 성공적으로 배달한 횟수입니다.|없음|
|d2c.endpoints.latency.serviceBusTopics|라우팅: Service Bus 토픽에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 Service Bus 토픽 엔드포인트에 대한 원격 분석 메시지 수신 간의 평균 대기 시간(밀리초)입니다.|없음|
|d2c.endpoints.egress.builtIn.events|라우팅: 메시지/이벤트에 배달된 메시지|카운트|합계|IoT Hub 라우팅에서 기본 제공 엔드포인트(메시지/이벤트)에 메시지를 성공적으로 배달한 횟수입니다.|없음|
|d2c.endpoints.latency.builtIn.events|라우팅: 메시지/이벤트에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 기본 제공 엔드포인트(메시지.이벤트)에 대한 원격 분석 메시지 수신 간의 평균 대기 시간(밀리초)입니다.|없음|
|d2c.endpoints.egress.storage|라우팅: 스토리지에 배달된 메시지|카운트|합계|IoT Hub 라우팅에서 스토리지 엔드포인트에 메시지를 성공적으로 배달한 횟수입니다.|없음|
|d2c.endpoints.latency.storage|라우팅: 스토리지에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 스토리지 엔드포인트에 대한 원격 분석 메시지 수신 간의 평균 대기 시간(밀리초)입니다.|없음|
|d2c.endpoints.egress.storage.bytes|라우팅: 스토리지에 배달된 데이터|바이트|합계|IoT Hub 라우팅에서 스토리지 엔드포인트에 배달된 데이터 양입니다(바이트).|없음|
|d2c.endpoints.egress.storage.blobs|라우팅: 스토리지에 배달된 Blob|카운트|합계|IoT Hub 라우팅에서 스토리지 엔드포인트에 Blob을 배달한 횟수입니다.|없음|
|EventGridDeliveries|Event Grid 배달 (미리 보기)|카운트|합계|Event Grid에 게시 된 IoT Hub 이벤트 수입니다. 성공 및 실패 한 요청의 수에 대해 결과 차원을 사용 합니다. EventType dimension 이벤트 유형 (https://aka.ms/ioteventgrid) 을 표시 합니다.|ResourceId, Result, EventType|
|EventGridLatency|Event Grid 대기 시간 (미리 보기)|밀리초|평균|Event Grid에 이벤트가 게시 될 때 Iot Hub 이벤트가 생성 된 시간에 대 한 평균 대기 시간 (밀리초)입니다. 이 숫자는 모든 이벤트 유형 사이의 평균입니다. 특정 유형의 이벤트에 대 한 대기 시간을 확인 하려면 EventType 차원을 사용 합니다.|ResourceId, EventType|
|d2c.twin.read.success|디바이스에서의 성공한 쌍 읽기|카운트|합계|성공한 모든 디바이스 시작 쌍 읽기 수입니다.|없음|
|d2c.twin.read.failure|디바이스에서의 실패한 쌍 읽기|카운트|합계|실패한 모든 디바이스 시작 쌍 읽기 수입니다.|없음|
|d2c.twin.read.size|디바이스에서의 쌍 읽기 응답 크기|바이트|평균|성공한 모든 디바이스 시작 쌍 읽기 수의 평균, 최소값 및 최대값입니다.|없음|
|d2c.twin.update.success|디바이스에서의 성공한 쌍 업데이트|카운트|합계|성공한 모든 디바이스 시작 쌍 업데이트 수입니다.|없음|
|d2c.twin.update.failure|디바이스에서의 실패한 쌍 업데이트|카운트|합계|실패한 모든 디바이스 시작 쌍 업데이트 수입니다.|없음|
|d2c.twin.update.size|디바이스에서의 쌍 업데이트 크기|바이트|평균|성공한 모든 디바이스 시작 쌍 업데이트 수의 평균, 최소 및 최대 크기입니다.|없음|
|c2d.methods.success|성공한 직접 메서드 호출|카운트|합계|성공한 모든 직접 메서드 호출의 수입니다.|없음|
|c2d.methods.failure|실패한 직접 메서드 호출|카운트|합계|실패한 모든 직접 메서드 호출의 수입니다.|없음|
|c2d.methods.requestSize|직접 메서드 호출의 요청 크기|바이트|평균|성공한 모든 직접 메서드 요청의 평균, 최소값, 최대값입니다.|없음|
|c2d.methods.responseSize|직접 메서드 호출의 응답 크기|바이트|평균|성공한 모든 직접 메서드 응답의 평균, 최소값, 최대값입니다.|없음|
|c2d.twin.read.success|백 엔드에서의 성공한 쌍 읽기|카운트|합계|성공한 모든 백 엔드 시작 쌍 읽기 수입니다.|없음|
|c2d.twin.read.failure|백 엔드에서의 실패한 쌍 읽기|카운트|합계|실패한 모든 백 엔드 시작 쌍 읽기 수입니다.|없음|
|c2d.twin.read.size|백 엔드에서의 쌍 읽기 응답 크기|바이트|평균|성공한 모든 백 엔드 시작 쌍 읽기 수의 평균, 최소값 및 최대값입니다.|없음|
|c2d.twin.update.success|백 엔드에서의 성공한 쌍 업데이트|카운트|합계|성공한 모든 백 엔드 시작 쌍 업데이트 수입니다.|없음|
|c2d.twin.update.failure|백 엔드에서의 실패한 쌍 업데이트|카운트|합계|실패한 모든 백 엔드 시작 쌍 업데이트 수입니다.|없음|
|c2d.twin.update.size|백 엔드에서의 쌍 업데이트 크기|바이트|평균|성공한 모든 백 엔드 시작 쌍 업데이트 수의 평균, 최소 및 최대 크기입니다.|없음|
|twinQueries.success|성공한 쌍 쿼리|카운트|합계|성공한 모든 쌍 쿼리의 수입니다.|없음|
|twinQueries.failure|실패한 쌍 쿼리|카운트|합계|실패한 모든 쌍 쿼리의 수입니다.|없음|
|twinQueries.resultSize|쌍 쿼리 결과 크기|바이트|평균|성공한 모든 쌍 쿼리 결과 크기의 평균, 최소값 및 최대값입니다.|없음|
|jobs.createTwinUpdateJob.success|쌍 업데이트 작업에 대한 성공한 만들기|카운트|합계|쌍 업데이트 작업에 대한 성공한 모든 만들기의 수입니다.|없음|
|jobs.createTwinUpdateJob.failure|쌍 업데이트 작업에 대한 실패한 만들기|카운트|합계|쌍 업데이트 작업에 대한 실패한 모든 만들기의 수입니다.|없음|
|jobs.createDirectMethodJob.success|메서드 호출 작업에 대한 성공한 만들기|카운트|합계|직접 메서드 호출 작업에 대한 성공한 모든 만들기의 수입니다.|없음|
|jobs.createDirectMethodJob.failure|실패한 메서드 호출 작업 만들기|카운트|합계|직접 메서드 호출 작업에 대한 실패한 모든 만들기의 수입니다.|없음|
|jobs.listJobs.success|목록 작업에 대한 성공한 호출|카운트|합계|목록 작업에 대한 성공한 모든 호출 수입니다.|없음|
|jobs.listJobs.failure|목록 작업에 대한 실패한 호출|카운트|합계|목록 작업에 대한 실패한 모든 호출 수입니다.|없음|
|jobs.cancelJob.success|성공한 작업 취소|카운트|합계|작업 취소에 대한 성공한 모든 호출 수입니다.|없음|
|jobs.cancelJob.failure|실패한 작업 취소|카운트|합계|작업 취소에 대한 실패한 모든 호출 수입니다.|없음|
|jobs.queryJobs.success|성공한 작업 쿼리|카운트|합계|쿼리 작업에 대한 성공한 모든 호출 수입니다.|없음|
|jobs.queryJobs.failure|실패한 작업 쿼리|카운트|합계|쿼리 작업에 대한 실패한 모든 호출 수입니다.|없음|
|jobs.completed|완료된 작업|카운트|합계|완료된 모든 작업의 수입니다.|없음|
|jobs.failed|실패한 작업|카운트|합계|실패한 모든 작업의 수입니다.|없음|
|d2c.telemetry.ingress.sendThrottle|제한 오류 수|카운트|합계|디바이스 처리량 제한으로 인한 제한 오류 수|없음|
|dailyMessageQuotaUsed|사용된 전체 메시지 수|카운트|평균|현재 사용되는 전체 메시지 수|없음|
|deviceDataUsage|총 장치 데이터 사용량|바이트|합계|IotHub에 연결된 모든 디바이스에서 전송된 바이트|없음|
|deviceDataUsageV2|총 디바이스 데이터 사용량(미리 보기)|바이트|합계|IotHub에 연결된 모든 디바이스에서 전송된 바이트|없음|
|totalDeviceCount|총 디바이스(미리 보기)|카운트|평균|IoT 허브에 등록된 디바이스 수|없음|
|connectedDeviceCount|연결된 디바이스(미리 보기)|카운트|평균|IoT 허브에 연결된 디바이스 수|없음|
|구성|구성 메트릭|카운트|합계|구성 작업에 대한 메트릭|없음|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|RegistrationAttempts|등록 시도|카운트|합계|디바이스 등록 시도 수|ProvisioningServiceName, IotHubName, 상태|
|DeviceAssignments|할당된 디바이스|카운트|합계|IoT Hub에 할당된 디바이스 수|ProvisioningServiceName, IotHubName|
|AttestationAttempts|증명 시도|카운트|합계|디바이스 증명 시도 수|ProvisioningServiceName, 상태, 프로토콜|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|AddRegion|추가 된 지역|카운트|카운트|추가 된 지역|지역|
|AvailableStorage|사용 가능한 스토리지|바이트|합계|5 분 세분성에서 보고 된 총 사용 가능한 저장소|CollectionName, DatabaseName, Region|
|CassandraConnectionClosures|Cassandra 연결 차단|카운트|합계|1 분 세분성으로 보고 된 Cassandra 연결의 수입니다.|APIType, 지역, ClosureReason|
|CassandraRequestCharges|Cassandra 요청 요금|카운트|합계|Cassandra 요청에 사용 된 RUs|APIType, DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|Cassandra 요청|카운트|카운트|Cassandra 요청 수|APIType, DatabaseName, CollectionName, Region, OperationType, ResourceType, ErrorCode|
|CreateAccount|계정이 만들어짐|카운트|카운트|계정이 만들어짐|없음|
|DataUsage|데이터 사용량|바이트|합계|5 분 세분성으로 보고 된 총 데이터 사용량|CollectionName, DatabaseName, Region|
|DeleteAccount|계정이 삭제 됨|카운트|카운트|계정이 삭제 됨|없음|
|DocumentCount|문서 수|카운트|합계|5 분 세분성으로 보고 된 총 문서 수|CollectionName, DatabaseName, Region|
|DocumentQuota|문서 할당량|바이트|합계|5 분 세분성에 보고 된 총 저장소 할당량|CollectionName, DatabaseName, Region|
|IndexUsage|인덱스 사용량|바이트|합계|5 분 세분성에 보고 된 총 인덱스 사용|CollectionName, DatabaseName, Region|
|MetadataRequests|메타데이터 요청|카운트|카운트|메타데이터 요청 수. Cosmos DB는 컬렉션, 데이터베이스 등과 해당 구성을 무료로 열거할 수 있는 각 계정에 대한 시스템 메타데이터 컬렉션을 유지 관리합니다.|DatabaseName, CollectionName, Region, StatusCode, Role|
|MongoRequestCharge|Mongo 요청 요금|카운트|합계|사용된 Mongo 요청 단위|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|MongoRequests|Mongo 요청|카운트|카운트|생성된 Mongo 요청 수|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|MongoRequestsCount|Mongo 요청 율|초당 개수|평균|초당 mongo 요청 수|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|MongoRequestsDelete|Mongo 삭제 요청 빈도|초당 개수|평균|초당 mongo 삭제 요청 수|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|MongoRequestsInsert|Mongo 삽입 요청 빈도|초당 개수|평균|초당 mongo 삽입 수|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|MongoRequestsQuery|Mongo 쿼리 요청 빈도|초당 개수|평균|Mongo 쿼리 요청 수/초|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|MongoRequestsUpdate|Mongo 업데이트 요청 률|초당 개수|평균|Mongo 업데이트 요청 수/초|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|ProvisionedThroughput|프로비전된 처리량|카운트|최대|프로비전된 처리량|DatabaseName, CollectionName|
|지역 장애 조치 (failover)|지역 장애 조치|카운트|카운트|지역 장애 조치|없음|
|RemoveRegion|지역이 제거 됨|카운트|카운트|지역이 제거 됨|지역|
|ReplicationLatency|P 99 복제 대기 시간|밀리초|평균|지역 사용 계정에 대한 원본 및 대상 지역의 P99 복제 대기 시간|SourceRegion, TargetRegion|
|ServiceAvailability|서비스 가용성|백분율|평균|1 시간, 일 또는 월 세분성의 계정 요청 가용성|없음|
|TotalRequestUnits|총 요청 단위|카운트|합계|사용된 요청 단위|DatabaseName, CollectionName, Region, StatusCode, OperationType|
|TotalRequests|총 요청 수|카운트|카운트|요청 수|DatabaseName, CollectionName, Region, StatusCode, OperationType|
|UpdateAccountKeys|계정 키가 업데이트 됨|카운트|카운트|계정 키가 업데이트 됨|KeyType|
|UpdateAccountNetworkSettings|계정 네트워크 설정 업데이트 됨|카운트|카운트|계정 네트워크 설정 업데이트 됨|없음|
|UpdateAccountReplicationSettings|계정 복제 설정 업데이트 됨|카운트|카운트|계정 복제 설정 업데이트 됨|없음|



## <a name="microsoftenterpriseknowledgegraphservices"></a>EnterpriseKnowledgeGraph/서비스

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|TransactionCount|트랜잭션 수|카운트|카운트|총 트랜잭션 수|TransactionCount|
|SuccessCount|성공 수|카운트|카운트|성공한 트랜잭션 수|SuccessCount|
|FailureCount|실패 수|카운트|카운트|실패 한 트랜잭션 수|FailureCount|
|SuccessLatency|성공 대기 시간|밀리초|평균|성공한 트랜잭션 대기 시간|SuccessCount|

## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|PublishSuccessCount|게시된 이벤트|카운트|합계|이 토픽에 게시된 총 이벤트|주제|
|PublishFailCount|실패 한 이벤트 게시|카운트|합계|이 토픽에 게시하지 못한 총 이벤트|토픽, ErrorType, 오류|
|PublishSuccessLatencyInMs|게시 성공 대기 시간|밀리초|합계|게시 성공 대기 시간 (밀리초)|없음|
|MatchedEventCount|일치하는 이벤트|카운트|합계|이 이벤트 구독에 일치하는 총 이벤트|토픽, EventSubscriptionName, DomainEventSubscriptionName|
|DeliveryAttemptFailCount|배달 실패 이벤트|카운트|합계|이 이벤트 구독에 배달하지 못한 총 이벤트|토픽, EventSubscriptionName, DomainEventSubscriptionName, Error, ErrorType|
|DeliverySuccessCount|배달된 이벤트|카운트|합계|이 이벤트 구독에 배달된 총 이벤트|토픽, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|대상 처리 기간|밀리초|평균|대상 처리 기간(밀리초)|토픽, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|삭제된 이벤트|카운트|합계|이 이벤트 구독에 일치하는 총 삭제된 이벤트|토픽, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|DeadLetteredCount|배달 못한 편지 이벤트|카운트|합계|이 이벤트 구독에 일치하는 총 배달 못한 이벤트|토픽, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|PublishSuccessCount|게시된 이벤트|카운트|합계|이 토픽에 게시된 총 이벤트|없음|
|PublishFailCount|실패 한 이벤트 게시|카운트|합계|이 토픽에 게시하지 못한 총 이벤트|ErrorType, 오류|
|UnmatchedEventCount|일치하지 않는 이벤트|카운트|합계|이 토픽에 대한 이벤트 구독과 일치하지 않는 총 이벤트|없음|
|PublishSuccessLatencyInMs|게시 성공 대기 시간|밀리초|합계|게시 성공 대기 시간 (밀리초)|없음|
|MatchedEventCount|일치하는 이벤트|카운트|합계|이 이벤트 구독에 일치하는 총 이벤트|EventSubscriptionName|
|DeliveryAttemptFailCount|배달 실패 이벤트|카운트|합계|이 이벤트 구독에 배달하지 못한 총 이벤트|오류, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|배달된 이벤트|카운트|합계|이 이벤트 구독에 배달된 총 이벤트|EventSubscriptionName|
|DestinationProcessingDurationInMs|대상 처리 기간|밀리초|평균|대상 처리 기간(밀리초)|EventSubscriptionName|
|DroppedEventCount|삭제된 이벤트|카운트|합계|이 이벤트 구독에 일치하는 총 삭제된 이벤트|DropReason, EventSubscriptionName|
|DeadLetteredCount|배달 못한 편지 이벤트|카운트|합계|이 이벤트 구독에 일치하는 총 배달 못한 이벤트|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|MatchedEventCount|일치하는 이벤트|카운트|합계|이 이벤트 구독에 일치하는 총 이벤트|없음|
|DeliveryAttemptFailCount|배달 실패 이벤트|카운트|합계|이 이벤트 구독에 배달하지 못한 총 이벤트|오류, ErrorType|
|DeliverySuccessCount|배달된 이벤트|카운트|합계|이 이벤트 구독에 배달된 총 이벤트|없음|
|DestinationProcessingDurationInMs|대상 처리 기간|밀리초|평균|대상 처리 기간(밀리초)|없음|
|DroppedEventCount|삭제된 이벤트|카운트|합계|이 이벤트 구독에 일치하는 총 삭제된 이벤트|DropReason|
|DeadLetteredCount|배달 못한 편지 이벤트|카운트|합계|이 이벤트 구독에 일치하는 총 배달 못한 이벤트|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|PublishSuccessCount|게시된 이벤트|카운트|합계|이 토픽에 게시된 총 이벤트|없음|
|PublishFailCount|실패 한 이벤트 게시|카운트|합계|이 토픽에 게시하지 못한 총 이벤트|ErrorType, 오류|
|UnmatchedEventCount|일치하지 않는 이벤트|카운트|합계|이 토픽에 대한 이벤트 구독과 일치하지 않는 총 이벤트|없음|
|PublishSuccessLatencyInMs|게시 성공 대기 시간|밀리초|합계|게시 성공 대기 시간 (밀리초)|없음|



## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|SuccessfulRequests|성공한 요청|카운트|합계|Microsoft.EventHub에 대한 성공한 요청.|EntityName, OperationResult|
|ServerErrors|서버 오류.|카운트|합계|Microsoft.EventHub에 대한 서버 오류.|EntityName, OperationResult|
|UserErrors|사용자 오류.|카운트|합계|Microsoft.EventHub에 대한 사용자 오류.|EntityName, OperationResult|
|QuotaExceededErrors|할당량 초과 오류.|카운트|합계|Microsoft.EventHub에 대한 할당량 초과 오류.|EntityName, OperationResult|
|ThrottledRequests|제한된 요청.|카운트|합계|Microsoft.EventHub에 대한 제한된 요청.|EntityName, OperationResult|
|IncomingRequests|들어오는 요청|카운트|합계|Microsoft.EventHub에 대한 들어오는 요청.|EntityName|
|IncomingMessages|들어오는 메시지|카운트|합계|Microsoft.EventHub에 대한 들어오는 메시지.|EntityName|
|OutgoingMessages|보내는 메시지|카운트|합계|Microsoft.EventHub에 대한 보내는 메시지.|EntityName|
|IncomingBytes|들어오는 바이트|바이트|합계|Microsoft.EventHub에 대한 들어오는 바이트.|EntityName|
|OutgoingBytes|보내는 바이트|바이트|합계|Microsoft.EventHub에 대한 보내는 바이트.|EntityName|
|ActiveConnections|ActiveConnections|카운트|평균|Microsoft.EventHub에 대한 총 활성 연결.|없음|
|ConnectionsOpened|열린 연결.|카운트|평균|Microsoft.EventHub에 대한 열린 연결.|EntityName|
|ConnectionsClosed|끊어진 연결.|카운트|평균|Microsoft.EventHub에 대한 끊어진 연결.|EntityName|
|CaptureBacklog|캡처 백로그.|카운트|합계|Microsoft.EventHub에 대한 캡처 백로그.|EntityName|
|CapturedMessages|캡처된 메시지.|카운트|합계|Microsoft.EventHub에 대한 캡처된 메시지.|EntityName|
|CapturedBytes|캡처된 바이트.|바이트|합계|Microsoft.EventHub에 대한 캡처된 바이트.|EntityName|
|크기|크기|바이트|평균|EventHub 크기(바이트)|EntityName|
|INREQS|들어오는 요청 (사용 되지 않음)|카운트|합계|네임 스페이스에 대 한 총 들어오는 전송 요청 (사용 되지 않음)|없음|
|SUCCREQ|성공한 요청 (사용 되지 않음)|카운트|합계|네임 스페이스에 대 한 총 성공한 요청 (사용 되지 않음)|없음|
|FAILREQ|실패 한 요청 (사용 되지 않음)|카운트|합계|네임 스페이스에 대해 실패 한 총 요청 (사용 되지 않음)|없음|
|SVRBSY|서버 작업 중 오류 (사용 되지 않음)|카운트|합계|네임 스페이스에 대 한 총 서버 사용 중 오류 (사용 되지 않음)|없음|
|INTERR|내부 서버 오류 (사용 되지 않음)|카운트|합계|네임 스페이스에 대 한 총 내부 서버 오류 (사용 되지 않음)|없음|
|MISCERR|기타 오류 (사용 되지 않음)|카운트|합계|네임 스페이스에 대해 실패 한 총 요청 (사용 되지 않음)|없음|
|INMSGS|들어오는 메시지 (사용 되지 않음) (사용 되지 않음)|카운트|합계|네임스페이스에 들어오는 총 메시지 수 이 메트릭은 사용되지 않습니다. 대신 들어오는 메시지 메트릭을 사용 하세요 (사용 되지 않음).|없음|
|EHINMSGS|들어오는 메시지(사용되지 않음)|카운트|합계|네임 스페이스에 대 한 총 수신 메시지 (사용 되지 않음)|없음|
|OUTMSGS|보내는 메시지 (사용 되지 않음) (사용 되지 않음)|카운트|합계|네임스페이스에 보내는 총 메시지 수 이 메트릭은 사용되지 않습니다. 대신 보내는 메시지 메트릭을 사용 하세요 (사용 되지 않음).|없음|
|EHOUTMSGS|보내는 메시지(사용되지 않음)|카운트|합계|네임 스페이스에 대 한 총 보내는 메시지 (사용 되지 않음)|없음|
|EHINMBS|들어오는 바이트 (사용 되지 않음) (사용 되지 않음)|바이트|합계|네임스페이스에 들어오는 Event Hub 메시지 처리량 이 메트릭은 사용되지 않습니다. 대신 들어오는 바이트 메트릭을 사용 하세요 (사용 되지 않음).|없음|
|EHINBYTES|들어오는 바이트(사용되지 않음)|바이트|합계|네임 스페이스에 대 한 이벤트 허브 들어오는 메시지 처리량 (사용 되지 않음)|없음|
|EHOUTMBS|보내는 바이트 (사용 되지 않음) (사용 되지 않음)|바이트|합계|네임스페이스에 보내는 Event Hub 메시지 처리량 이 메트릭은 사용되지 않습니다. 대신 나가는 바이트 메트릭을 사용 하십시오 (사용 되지 않음).|없음|
|EHOUTBYTES|보내는 바이트(사용되지 않음)|바이트|합계|네임 스페이스에 대 한 이벤트 허브 나가는 메시지 처리량 (사용 되지 않음)|없음|
|EHABL|백로그 메시지 보관 (사용 되지 않음)|카운트|합계|네임 스페이스에 대 한 백로그의 이벤트 허브 보관 메시지 (사용 되지 않음)|없음|
|EHAMSGS|보관 메시지 (사용 되지 않음)|카운트|합계|네임 스페이스에서 이벤트 허브 보관 된 메시지 (사용 되지 않음)|없음|
|EHAMBS|보관 메시지 처리량 (사용 되지 않음)|바이트|합계|네임 스페이스에서 이벤트 허브 보관 된 메시지 처리량 (사용 되지 않음)|없음|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|SuccessfulRequests|성공한 요청|카운트|합계|Microsoft.EventHub에 대한 성공한 요청.|OperationResult|
|ServerErrors|서버 오류.|카운트|합계|Microsoft.EventHub에 대한 서버 오류.|OperationResult|
|UserErrors|사용자 오류.|카운트|합계|Microsoft.EventHub에 대한 사용자 오류.|OperationResult|
|QuotaExceededErrors|할당량 초과 오류.|카운트|합계|Microsoft.EventHub에 대한 할당량 초과 오류.|OperationResult|
|ThrottledRequests|제한된 요청.|카운트|합계|Microsoft.EventHub에 대한 제한된 요청.|OperationResult|
|IncomingRequests|들어오는 요청|카운트|합계|Microsoft.EventHub에 대한 들어오는 요청.|없음|
|IncomingMessages|들어오는 메시지|카운트|합계|Microsoft.EventHub에 대한 들어오는 메시지.|없음|
|OutgoingMessages|보내는 메시지|카운트|합계|Microsoft.EventHub에 대한 보내는 메시지.|없음|
|IncomingBytes|들어오는 바이트|바이트|합계|Microsoft.EventHub에 대한 들어오는 바이트.|없음|
|OutgoingBytes|보내는 바이트|바이트|합계|Microsoft.EventHub에 대한 보내는 바이트.|없음|
|ActiveConnections|ActiveConnections|카운트|평균|Microsoft.EventHub에 대한 총 활성 연결.|없음|
|ConnectionsOpened|열린 연결.|카운트|평균|Microsoft.EventHub에 대한 열린 연결.|없음|
|ConnectionsClosed|끊어진 연결.|카운트|평균|Microsoft.EventHub에 대한 끊어진 연결.|없음|
|CaptureBacklog|캡처 백로그.|카운트|합계|Microsoft.EventHub에 대한 캡처 백로그.|없음|
|CapturedMessages|캡처된 메시지.|카운트|합계|Microsoft.EventHub에 대한 캡처된 메시지.|없음|
|CapturedBytes|캡처된 바이트.|바이트|합계|Microsoft.EventHub에 대한 캡처된 바이트.|없음|
|CPU|CPU|백분율|최대|이벤트 허브 클러스터에 대한 CPU 사용률(백분율)|역할|
|AvailableMemory|사용 가능한 메모리|백분율|최대|총 메모리의 백분율로 나타낸 이벤트 허브 클러스터에 사용할 수 있는 메모리입니다.|역할|


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|GatewayRequests|게이트웨이 요청|카운트|합계|게이트웨이 요청 수|HttpStatus|
|CategorizedGatewayRequests|분류된 게이트웨이 요청|카운트|합계|범주별 게이트웨이 요청 수(1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|NumActiveWorkers|활성 작업자 수|카운트|최대|활성 작업자 수|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|ObservedMetricValue|관찰된 메트릭 값|카운트|평균|실행될 때 자동 크기 조정에서 계산된 값|MetricTriggerSource|
|MetricThreshold|메트릭 임계값|카운트|평균|자동 크기 조정이 실행되었을 때 구성된 자동 크기 조정 임계값입니다.|MetricTriggerRule|
|ObservedCapacity|관찰된 용량|카운트|평균|실행될 때 자동 크기 조정을 위해 보고된 용량입니다.|없음|
|ScaleActionsInitiated|시작된 크기 조정 작업|카운트|합계|크기 조정 작업의 방향입니다.|ScaleDirection|




## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|가용성|백분율|평균|성공적으로 완료 된 가용성 테스트 비율|availabilityResult/name, availabilityResult/location|
|availabilityResults/count|가용성 테스트|카운트|카운트|가용성 테스트 수|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|availabilityResults/duration|가용성 테스트 기간|밀리초|평균|가용성 테스트 기간|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|browserTimings/networkDuration|페이지 로드 네트워크 연결 시간|밀리초|평균|사용자 요청과 네트워크 연결 사이의 시간입니다. DNS 조회 및 전송 연결을 포함합니다.|없음|
|browserTimings/processingDuration|클라이언트 처리 시간|밀리초|평균|DOM을 로드할 때부터 문서의 마지막 바이트를 받는 사이의 시간입니다. 비동기 요청은 계속 처리 중일 수 있습니다.|없음|
|browserTimings/receiveDuration|응답 수신 시간|밀리초|평균|첫 번째 및 마지막 바이트 사이 또는 연결 끊기까지의 시간입니다.|없음|
|browserTimings/sendDuration|요청 전송 시간|밀리초|평균|네트워크 연결과 첫 번째 바이트 받기 사이의 시간입니다.|없음|
|browserTimings/totalDuration|브라우저 페이지 로드 시간|밀리초|평균|사용자가 요청한 때부터 DOM, 스타일시트, 스크립트 및 이미지가 로드될 때까지 소요된 시간입니다.|없음|
|dependencies/count|종속성 호출|카운트|카운트|외부 리소스에 대해 애플리케이션이 만든 호출 수입니다.|종속성/유형, 종속성/performanceBucket, 종속성/성공, 종속성/대상, 작업/가상, 클라우드/roleInstance, 클라우드/roleName|
|dependencies/duration|종속성 기간|밀리초|평균|외부 리소스에 대한 서버 애플리케이션의 호출 기간입니다.|종속성/유형, 종속성/performanceBucket, 종속성/성공, 종속성/대상, 작업/가상, 클라우드/roleInstance, 클라우드/roleName|
|dependencies/failed|종속성 호출 오류|카운트|카운트|외부 리소스에 대해 애플리케이션이 만든 실패한 종속성 호출 수입니다.|종속성/유형, 종속성/performanceBucket, 종속성/성공, 종속성/대상, 작업/가상, 클라우드/roleInstance, 클라우드/roleName|
|pageViews/count|페이지 보기|카운트|카운트|페이지 보기 수입니다.|작업/가상, 클라우드/roleName|
|pageViews/duration|페이지 보기 로드 시간|밀리초|평균|페이지 보기 로드 시간|작업/가상, 클라우드/roleName|
|performanceCounters/requestExecutionTime|HTTP 요청 실행 시간|밀리초|평균|가장 최근 요청의 실행 시간입니다.|cloud/roleInstance|
|performanceCounters/requestsInQueue|애플리케이션 큐의 HTTP 요청|카운트|평균|애플리케이션 요청 큐의 길이입니다.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|HTTP 요청 속도|초당 개수|평균|ASP.NET에서 애플리케이션에 전송된 모든 요청의 속도(초)입니다.|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|예외 속도|초당 개수|평균|.NET 예외 및 .NET 예외로 변환된 관리되지 않는 예외를 포함하여 Windows에 보고된 처리된 예외 및 처리되지 않은 예외 수입니다.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|프로세스 IO 속도|초당 바이트 수|평균|파일, 네트워크 및 디바이스에서 읽고 쓴 초당 총 바이트 수입니다.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|CPU 프로세스|백분율|평균|모든 프로세스 스레드가 명령을 실행하기 위해 프로세서를 사용한 경과된 시간의 백분율입니다. 0~100 사이로 달라질 수 있습니다. 이 메트릭은 w3wp 프로세스만의 성능을 나타냅니다.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|프로세서 시간|백분율|평균|프로세서가 비 유휴 스레드에 소요한 시간의 비율입니다.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|사용 가능한 메모리|바이트|평균|프로세스에 할당하거나 시스템에서 사용할 수 있는 실제 메모리입니다.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|프로세스 프라이빗 바이트|바이트|평균|모니터링되는 애플리케이션의 프로세스에 독점적으로 할당된 메모리입니다.|cloud/roleInstance|
|requests/duration|서버 응답 시간|밀리초|평균|HTTP 요청을 받은 후 응답 전송을 완료한 때까지의 시간입니다.|요청/performanceBucket, 요청/resultCode, 작업/가상, 클라우드/roleInstance, 요청/성공, 클라우드/roleName|
|requests/count|서버 요청|카운트|카운트|완료된 HTTP 요청 수입니다.|요청/performanceBucket, 요청/resultCode, 작업/가상, 클라우드/roleInstance, 요청/성공, 클라우드/roleName|
|requests/failed|실패한 요청|카운트|카운트|실패한 것으로 표시된 HTTP 요청 수입니다. 대부분의 경우 응답 코드가 >= 400이고 401과 같지 않은 요청입니다.|요청/performanceBucket, 요청/resultCode, 요청/성공, 작업/가상, 클라우드/roleInstance, 클라우드/roleName|
|requests/rate|서버 요청 빈도|초당 개수|평균|초당 서버 요청 수|요청/performanceBucket, 요청/resultCode, 작업/가상, 클라우드/roleInstance, 요청/성공, 클라우드/roleName|
|exceptions/count|예외|카운트|카운트|모든 Catch되지 않은 예외의 결합된 수입니다.|클라우드/roleName, 클라우드/roleInstance, 클라이언트/유형|
|exceptions/browser|브라우저 예외|카운트|카운트|브라우저에서 발생한 확인할 수 없는 예외의 개수입니다.|클라이언트/isServer, 클라우드/roleName|
|exceptions/server|서버 예외|카운트|카운트|서버 애플리케이션에서 발생된 확인할 수 없는 예외의 수입니다.|클라이언트/isServer, 클라우드/roleName, 클라우드/roleInstance|
|traces/count|Traces|카운트|카운트|문서 개수 추적|추적/severityLevel, 작업/가상, 클라우드/roleName, 클라우드/roleInstance|



## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|ServiceApiHit|Service API 총 방문 횟수|카운트|카운트|Service API의 총 방문 횟수|ActivityType, ActivityName|
|ServiceApiLatency|전체 Service API 대기 시간|밀리초|평균|Service API 요청의 전체 대기 시간|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Service API 총 결과|카운트|카운트|Service API의 총 결과 수|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|SaturationShoebox|전체 자격 증명 모음 채도|백분율|평균|사용 되는 자격 증명 모음 용량|ActivityType, ActivityName, TransactionType|
|가용성|전체 자격 증명 모음 가용성|백분율|평균|자격 증명 모음 요청 가용성|ActivityType, ActivityName, StatusCode, StatusCodeClass|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|CacheUtilization|캐시 사용률|백분율|평균|클러스터 범위의 사용률 수준|없음|
|QueryDuration|쿼리 기간|밀리초|평균|쿼리 기간(초)|QueryStatus|
|IngestionUtilization|수집 사용률|백분율|평균|클러스터에서 사용되는 수집 슬롯의 비율|없음|
|KeepAlive|연결 유지|카운트|평균|온전성 검사는 쿼리에 대한 클러스터 응답을 나타냅니다.|없음|
|IngestionVolumeInMB|수집 볼륨 (MB)|카운트|합계|클러스터에 수집된 데이터의 전체 볼륨(MB)|데이터베이스|
|IngestionLatencyInSeconds|수집 대기 시간 (초)|초|평균|원본(예: EventHub에 있는 메시지)에서 클러스터로 수집 시간(초)|없음|
|EventsProcessedForEventHubs|처리 된 이벤트 (이벤트/IoT 허브의 경우)|카운트|합계|수집 때 클러스터에서 처리 한 이벤트 수/IoT Hub|EventStatus|
|IngestionResult|수집 결과|카운트|카운트|수집 작업 수|IngestionResultDetails|
|CPU|CPU|백분율|평균|CPU 사용률 수준|없음|
|ContinuousExportNumOfRecordsExported|연속 내보내기 – 내보낸 레코드의 개수|카운트|합계|내보내기 작업 중에 작성 된 모든 저장소 아티팩트에 대해 발생 된 내보낸 레코드 수입니다.|없음|
|ExportUtilization|사용률 내보내기|백분율|최대|사용률 내보내기|없음|
|ContinuousExportPendingCount|연속 내보내기 보류 중 수|카운트|최대|실행 준비가 완료 된 보류 중인 연속 내보내기 작업의 수입니다.|없음|
|ContinuousExportMaxLatenessMinutes|연속 내보내기 최대 지연 시간 (분)|카운트|최대|모든 연속 내보내기 보류 중 및 실행 준비가 완료 된 최대 지연 시간 (분)입니다.|없음|
|ContinuousExportResult|연속 내보내기 결과|카운트|카운트|연속 내보내기가 성공 또는 실패 여부를 나타냅니다.|ContinuousExportName, 결과, 데이터베이스|
|StreamingIngestDuration|스트리밍 수집 기간|밀리초|평균|스트리밍 수집 기간 (밀리초)|없음|
|StreamingIngestDataRate|스트리밍 수집 데이터 전송률|카운트|평균|스트리밍 수집 데이터 전송률 (초당 MB)|없음|
|SteamingIngestRequestRate|스트리밍 수집 요청 빈도|카운트|RateRequestsPerSecond|스트리밍 수집 요청 률 (초당 요청 수)|없음|
|StreamingIngestResults|스트리밍 수집 결과|카운트|평균|스트리밍 수집 결과|결과|
|TotalNumberOfConcurrentQueries|총 동시 쿼리 수|카운트|합계|총 동시 쿼리 수|없음|
|TotalNumberOfThrottledQueries|제한 된 쿼리의 총 수|카운트|합계|제한 된 쿼리의 총 수|없음|
|TotalNumberOfThrottledCommands|제한 된 명령의 총 수|카운트|합계|제한 된 명령의 총 수|CommandType|
|TotalNumberOfExtents|총 익스텐트 수|카운트|합계|총 데이터 익스텐트 수|없음|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|RunsStarted|실행 시작됨|카운트|합계|실행 시작된 워크플로 수|없음|
|RunsCompleted|실행 완료됨|카운트|합계|실행 완료된 워크플로 수|없음|
|RunsSucceeded|실행 성공함|카운트|합계|실행 성공한 워크플로 수|없음|
|RunsFailed|실행 실패함|카운트|합계|실행 실패한 워크플로 수|없음|
|RunsCancelled|실행 취소됨|카운트|합계|실행 취소된 워크플로 수|없음|
|RunLatency|실행 대기 시간|초|평균|완료된 워크플로 실행 대기 시간|없음|
|RunSuccessLatency|실행 성공 대기 시간|초|평균|성공한 워크플로 실행 대기 시간|없음|
|RunThrottledEvents|실행 제한 이벤트|카운트|합계|워크플로 작업 또는 트리거 제한 이벤트 수|없음|
|RunStartThrottledEvents|시작 제한 이벤트 실행|카운트|합계|워크플로 실행 시작 제한 이벤트의 수입니다.|없음|
|RunFailurePercentage|실행 오류 비율|백분율|합계|실행 실패한 워크플로 비율.|없음|
|ActionsStarted|작업 시작됨 |카운트|합계|시작된 워크플로 작업 수|없음|
|ActionsCompleted|작업 완료됨 |카운트|합계|완료된 워크플로 작업 수|없음|
|ActionsSucceeded|작업 성공함 |카운트|합계|성공한 워크플로 작업 수|없음|
|ActionsFailed|작업 실패함 |카운트|합계|실패한 워크플로 작업 수|없음|
|ActionsSkipped|작업 생략됨 |카운트|합계|생략한 워크플로 작업 수|없음|
|ActionLatency|작업 대기 시간 |초|평균|완료된 워크플로 작업 대기 시간|없음|
|ActionSuccessLatency|작업 성공 대기 시간 |초|평균|성공한 워크플로 작업 대기 시간|없음|
|ActionThrottledEvents|작업 제한 이벤트|카운트|합계|워크플로 작업 제한 이벤트 수|없음|
|TriggersStarted|트리거 시작됨 |카운트|합계|시작된 워크플로 트리거 수|없음|
|TriggersCompleted|트리거 완료됨 |카운트|합계|완료된 워크플로 트리거 수|없음|
|TriggersSucceeded|트리거 성공함 |카운트|합계|성공한 워크플로 트리거 수|없음|
|TriggersFailed|트리거 실패함 |카운트|합계|실패한 워크플로 트리거 수|없음|
|TriggersSkipped|트리거 생략함|카운트|합계|생략한 워크플로 트리거 수|없음|
|TriggersFired|트리거 실행됨 |카운트|합계|실행한 워크플로 트리거 수|없음|
|TriggerLatency|트리거 대기 시간 |초|평균|완료된 워크플로 트리거 대기 시간|없음|
|TriggerFireLatency|트리거 실행 대기 시간 |초|평균|실행한 워크플로 트리거 대기 시간|없음|
|TriggerSuccessLatency|트리거 성공 대기 시간 |초|평균|성공한 워크플로 트리거 대기 시간|없음|
|TriggerThrottledEvents|트리거 제한 이벤트|카운트|합계|워크플로 트리거 제한 이벤트 수|없음|
|BillableActionExecutions|청구 가능한 작업 실행|카운트|합계|청구되는 워크플로 작업 실행 수.|없음|
|BillableTriggerExecutions|청구 가능한 트리거 실행|카운트|합계|청구되는 워크플로 트리거 실행 수.|없음|
|TotalBillableExecutions|총 청구 가능 실행|카운트|합계|청구되는 워크플로 실행 수.|없음|
|BillingUsageNativeOperation|기본 작업 실행에 대한 청구 사용량|카운트|합계|청구되는 기본 작업 실행 수입니다.|없음|
|BillingUsageStandardConnector|표준 커넥터 실행에 대한 청구 사용량|카운트|합계|청구되는 표준 커넥터 실행 수입니다.|없음|
|BillingUsageStorageConsumption|스토리지 소비 실행에 대한 청구 사용량|카운트|합계|청구되는 스토리지 소비 실행 수입니다.|없음|
|BillingUsageNativeOperation|기본 작업 실행에 대한 청구 사용량|카운트|합계|청구되는 기본 작업 실행 수입니다.|없음|
|BillingUsageStandardConnector|표준 커넥터 실행에 대한 청구 사용량|카운트|합계|청구되는 표준 커넥터 실행 수입니다.|없음|
|BillingUsageStorageConsumption|스토리지 소비 실행에 대한 청구 사용량|카운트|합계|청구되는 스토리지 소비 실행 수입니다.|없음|



## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|RunsStarted|실행 시작됨|카운트|합계|실행 시작된 워크플로 수|없음|
|RunsCompleted|실행 완료됨|카운트|합계|실행 완료된 워크플로 수|없음|
|RunsSucceeded|실행 성공함|카운트|합계|실행 성공한 워크플로 수|없음|
|RunsFailed|실행 실패함|카운트|합계|실행 실패한 워크플로 수|없음|
|RunsCancelled|실행 취소됨|카운트|합계|실행 취소된 워크플로 수|없음|
|RunLatency|실행 대기 시간|초|평균|완료된 워크플로 실행 대기 시간|없음|
|RunSuccessLatency|실행 성공 대기 시간|초|평균|성공한 워크플로 실행 대기 시간|없음|
|RunThrottledEvents|실행 제한 이벤트|카운트|합계|워크플로 작업 또는 트리거 제한 이벤트 수|없음|
|RunStartThrottledEvents|시작 제한 이벤트 실행|카운트|합계|워크플로 실행 시작 제한 이벤트의 수입니다.|없음|
|RunFailurePercentage|실행 오류 비율|백분율|합계|실행 실패한 워크플로 비율.|없음|
|ActionsStarted|작업 시작됨 |카운트|합계|시작된 워크플로 작업 수|없음|
|ActionsCompleted|작업 완료됨 |카운트|합계|완료된 워크플로 작업 수|없음|
|ActionsSucceeded|작업 성공함 |카운트|합계|성공한 워크플로 작업 수|없음|
|ActionsFailed|작업 실패함 |카운트|합계|실패한 워크플로 작업 수|없음|
|ActionsSkipped|작업 생략됨 |카운트|합계|생략한 워크플로 작업 수|없음|
|ActionLatency|작업 대기 시간 |초|평균|완료된 워크플로 작업 대기 시간|없음|
|ActionSuccessLatency|작업 성공 대기 시간 |초|평균|성공한 워크플로 작업 대기 시간|없음|
|ActionThrottledEvents|작업 제한 이벤트|카운트|합계|워크플로 작업 제한 이벤트 수|없음|
|TriggersStarted|트리거 시작됨 |카운트|합계|시작된 워크플로 트리거 수|없음|
|TriggersCompleted|트리거 완료됨 |카운트|합계|완료된 워크플로 트리거 수|없음|
|TriggersSucceeded|트리거 성공함 |카운트|합계|성공한 워크플로 트리거 수|없음|
|TriggersFailed|트리거 실패함 |카운트|합계|실패한 워크플로 트리거 수|없음|
|TriggersSkipped|트리거 생략함|카운트|합계|생략한 워크플로 트리거 수|없음|
|TriggersFired|트리거 실행됨 |카운트|합계|실행한 워크플로 트리거 수|없음|
|TriggerLatency|트리거 대기 시간 |초|평균|완료된 워크플로 트리거 대기 시간|없음|
|TriggerFireLatency|트리거 실행 대기 시간 |초|평균|실행한 워크플로 트리거 대기 시간|없음|
|TriggerSuccessLatency|트리거 성공 대기 시간 |초|평균|성공한 워크플로 트리거 대기 시간|없음|
|TriggerThrottledEvents|트리거 제한 이벤트|카운트|합계|워크플로 트리거 제한 이벤트 수|없음|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|통합 서비스 환경에 대 한 워크플로 프로세서 사용|백분율|평균|Integration service environment에 대 한 워크플로 프로세서 사용량입니다.|없음|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|통합 서비스 환경에 대 한 워크플로 메모리 사용|백분율|평균|통합 서비스 환경에 대 한 워크플로 메모리 사용량입니다.|없음|
|IntegrationServiceEnvironmentConnectorProcessorUsage|통합 서비스 환경에 대 한 커넥터 프로세서 사용량|백분율|평균|Integration service environment의 커넥터 프로세서 사용량입니다.|없음|
|IntegrationServiceEnvironmentConnectorMemoryUsage|통합 서비스 환경에 대 한 커넥터 메모리 사용량|백분율|평균|Integration service environment의 커넥터 메모리 사용량입니다.|없음|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|완료 된 실행|완료 된 실행|카운트|합계|이 작업 영역에 대해 성공적으로 완료 된 실행 수|Scenario, RunType, PublishedPipelineId, PipelineStepType Etype,|
|실행 시작|실행 시작|카운트|합계|이 작업 영역에 대해 시작 된 실행 수|Scenario, RunType, PublishedPipelineId, PipelineStepType Etype,|
|실패한 실행|실패한 실행|카운트|합계|이 작업 영역에 대해 실패 한 실행 수|Scenario, RunType, PublishedPipelineId, PipelineStepType Etype,|
|모델 등록 성공|모델 등록 성공|카운트|합계|이 작업 영역에서 성공한 모델 등록 수|시나리오|
|모델 레지스터가 실패 했습니다.|모델 레지스터가 실패 했습니다.|카운트|합계|이 작업 영역에서 실패 한 모델 등록 수|시나리오, StatusCode|
|모델 배포 시작 됨|모델 배포 시작 됨|카운트|합계|이 작업 영역에서 시작 된 모델 배포 수|시나리오|
|모델 배포 성공|모델 배포 성공|카운트|합계|이 작업 영역에서 성공한 모델 배포 수|시나리오|
|모델 배포 실패|모델 배포 실패|카운트|합계|이 작업 영역에서 실패 한 모델 배포 수|시나리오, StatusCode|
|총 노드|총 노드|카운트|평균|총 노드 수입니다. 이 합계에는 활성 노드, 유휴 노드, 사용할 수 없는 노드, 선점 된 노드, 노드가 남아 있습니다.|시나리오, ClusterName|
|활성 노드|활성 노드|카운트|평균|활성 노드 수입니다. 작업을 적극적으로 실행 중인 노드입니다.|시나리오, ClusterName|
|유휴 노드|유휴 노드|카운트|평균|유휴 노드의 수입니다. 유휴 노드는 작업을 실행 하지 않지만 사용 가능한 경우 새 작업을 허용할 수 있는 노드입니다.|시나리오, ClusterName|
|사용할 수 없는 노드|사용할 수 없는 노드|카운트|평균|사용할 수 없는 노드 수입니다. 확인할 수 없는 노드는 확인할 수 없는 문제 때문에 작동 하지 않습니다. Azure는 이러한 노드를 재활용 합니다.|시나리오, ClusterName|
|선점 노드|선점 노드|카운트|평균|선점 된 노드 수입니다. 이러한 노드는 사용 가능한 노드 풀에서 멀리 떨어져 있는 낮은 우선 순위 노드입니다.|시나리오, ClusterName|
|노드 종료|노드 종료|카운트|평균|종료 노드 수입니다. 노드가 종료 되 면 작업 처리가 완료 되 고 유휴 상태로 전환 됩니다.|시나리오, ClusterName|
|총 코어|총 코어|카운트|평균|총 코어 수|시나리오, ClusterName|
|활성 코어|활성 코어|카운트|평균|활성 코어 수|시나리오, ClusterName|
|유휴 코어|유휴 코어|카운트|평균|유휴 코어 수|시나리오, ClusterName|
|사용할 수 없는 코어|사용할 수 없는 코어|카운트|평균|사용할 수 없는 코어 수|시나리오, ClusterName|
|선점 코어|선점 코어|카운트|평균|선점 된 코어 수|시나리오, ClusterName|
|코어 탈퇴|코어 탈퇴|카운트|평균|종료 된 코어 수|시나리오, ClusterName|
|할당량 사용률 비율|할당량 사용률 비율|카운트|평균|사용한 할당량 백분율|시나리오, ClusterName, VmFamilyName, VmPriority|
|CpuUtilization|CpuUtilization|카운트|평균|CPU(미리 보기)|시나리오, runId, NodeId, CreatedTime|
|GpuUtilization|GpuUtilization|카운트|평균|GPU (미리 보기)|시나리오, runId, NodeId, CreatedTime, DeviceId|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|사용량|사용량|카운트|카운트|API 호출 수|ApiCategory, ApiName, ResultType, ResponseCode|
|가용성|가용성|백분율|평균|Api의 가용성|ApiCategory, ApiName|

## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft. Media/windowsazure.mediaservices/streamingEndpoints

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|송신|송신|바이트|합계|송신 데이터의 양 (바이트)입니다.|OutputFormat|
|SuccessE2ELatency|성공 종단 간 대기 시간|밀리초|평균|성공한 요청의 평균 대기 시간 (밀리초)입니다.|OutputFormat|
|요청|요청|카운트|합계|스트리밍 엔드포인트에 대 한 요청입니다.|OutputFormat, HttpStatusCode, ErrorCode|


## <a name="microsoftmediamediaservices"></a>Microsoft. Media/windowsazure.mediaservices

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|AssetQuota|자산 할당량|카운트|평균|현재 미디어 서비스 계정에 대해 허용 되는 자산 수|없음|
|AssetCount|자산 수|카운트|평균|현재 미디어 서비스 계정에 이미 생성 된 자산 수|없음|
|AssetQuotaUsedPercentage|사용 되는 자산 할당량 백분율|백분율|평균|현재 미디어 서비스 계정에서 사용 되는 자산 비율|없음|
|ContentKeyPolicyQuota|콘텐츠 키 정책 할당량|카운트|평균|현재 미디어 서비스 계정에 대해 허용 되는 콘텐츠 키 정책 수|없음|
|ContentKeyPolicyCount|콘텐츠 키 정책 수|카운트|평균|현재 미디어 서비스 계정에 이미 생성 된 콘텐츠 키 정책 수|없음|
|ContentKeyPolicyQuotaUsedPercentage|콘텐츠 키 정책 할당량 사용 백분율|백분율|평균|현재 미디어 서비스 계정에서 사용 되는 콘텐츠 키 정책 비율|없음|
|StreamingPolicyQuota|스트리밍 정책 할당량|카운트|평균|현재 미디어 서비스 계정에 대해 허용 되는 스트리밍 정책 수|없음|
|StreamingPolicyCount|스트리밍 정책 수|카운트|평균|현재 미디어 서비스 계정에 이미 생성 된 스트리밍 정책 수|없음|
|StreamingPolicyQuotaUsedPercentage|스트리밍 정책 할당량 사용 백분율|백분율|평균|현재 미디어 서비스 계정에서 사용 되는 스트리밍 정책 비율|없음|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft NetApp/netAppAccounts/capacityPools/볼륨

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|AverageReadLatency|평균 읽기 대기 시간|밀리초|평균|작업당 평균 읽기 대기 시간(밀리초)|없음|
|AverageWriteLatency|평균 쓰기 대기 시간|밀리초|평균|작업당 평균 쓰기 대기 시간(밀리초)|없음|
|VolumeLogicalSize|볼륨 논리적 크기|바이트|평균|볼륨의 논리적 크기(사용되는 바이트)|없음|
|VolumeSnapshotSize|볼륨 스냅샷 크기|바이트|평균|볼륨의 모든 스냅샷 크기|없음|
|ReadIops|읽기 IOPS|초당 개수|평균|초당 읽기 입력/출력 작업|없음|
|WriteIops|쓰기 IOPS|초당 개수|평균|초당 쓰기 입력/출력 작업|없음|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|VolumePoolAllocatedUsed|할당된 사용되는 볼륨 풀|바이트|평균|풀의 사용되는 할당된 크기|없음|
|VolumePoolTotalLogicalSize|볼륨 풀 총 논리적 크기|바이트|평균|풀에 속한 모든 볼륨의 논리적 크기의 합계|없음|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|BytesSentRate|보낸 바이트|바이트|합계|네트워크 인터페이스가 보낸 바이트 수|없음|
|BytesReceivedRate|수신된 바이트|바이트|합계|네트워크 인터페이스가 수신한 바이트 수|없음|
|PacketsSentRate|보낸 패킷|카운트|합계|네트워크 인터페이스가 보낸 패킷 수|없음|
|PacketsReceivedRate|수신된 패킷|카운트|합계|네트워크 인터페이스가 수신한 패킷 수|없음|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|VipAvailability|데이터 경로 가용성|카운트|평균|기간당 평균 Load Balancer 데이터 경로 가용성|FrontendIPAddress, FrontendPort|
|DipAvailability|상태 프로브 상태|카운트|평균|기간당 평균 Load Balancer 상태 프로브 상태|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|바이트 수|카운트|합계|기간 내에 전송된 총 바이트 수|FrontendIPAddress, FrontendPort, Direction|
|PacketCount|패킷 수|카운트|합계|기간 내에 전송된 총 패킷 수|FrontendIPAddress, FrontendPort, Direction|
|SYNCount|SYN 수|카운트|합계|기간 내에 전송된 총 SYN 패킷 수|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|SNAT 연결 수|카운트|합계|기간 내에 만들어진 새 SNAT 연결의 총 수|FrontendIPAddress, BackendIPAddress, ConnectionState|
|AllocatedSnatPorts|할당된 SNAT 포트(미리 보기)|카운트|합계|기간 내에 할당된 총 SNAT 포트 수|FrontendIPAddress, BackendIPAddress, ProtocolType, IsAwaitingRemoval|
|UsedSnatPorts|사용된 SNAT 포트(미리 보기)|카운트|합계|기간 내에 사용된 총 SNAT 포트 수|FrontendIPAddress, BackendIPAddress, ProtocolType, IsAwaitingRemoval|


## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|QueryVolume|쿼리 볼륨|카운트|합계|DNS 영역에 대해 제공된 쿼리 수|없음|
|RecordSetCount|레코드 집합 수|카운트|최대|DNS 영역의 레코드 집합 수|없음|
|RecordSetCapacityUtilization|레코드 집합 용량 사용률|백분율|최대|DNS 영역에서 사용된 레코드 집합 용량 백분율|없음|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|PacketsInDDoS|인바운드 패킷 DDoS|초당 개수|최대|인바운드 패킷 DDoS|없음|
|PacketsDroppedDDoS|인바운드 패킷이 삭제된 DDoS|초당 개수|최대|인바운드 패킷이 삭제된 DDoS|없음|
|PacketsForwardedDDoS|인바운드 패킷이 전달된 DDoS|초당 개수|최대|인바운드 패킷이 전달된 DDoS|없음|
|TCPPacketsInDDoS|인바운드 TCP 패킷 DDoS|초당 개수|최대|인바운드 TCP 패킷 DDoS|없음|
|TCPPacketsDroppedDDoS|인바운드 TCP 패킷이 삭제된 DDoS|초당 개수|최대|인바운드 TCP 패킷이 삭제된 DDoS|없음|
|TCPPacketsForwardedDDoS|인바운드 TCP 패킷이 전달된 DDoS|초당 개수|최대|인바운드 TCP 패킷이 전달된 DDoS|없음|
|UDPPacketsInDDoS|인바운드 UDP 패킷 DDoS|초당 개수|최대|인바운드 UDP 패킷 DDoS|없음|
|UDPPacketsDroppedDDoS|인바운드 UDP 패킷이 삭제된 DDoS|초당 개수|최대|인바운드 UDP 패킷이 삭제된 DDoS|없음|
|UDPPacketsForwardedDDoS|인바운드 UDP 패킷이 전달된 DDoS|초당 개수|최대|인바운드 UDP 패킷이 전달된 DDoS|없음|
|BytesInDDoS|인바운드 바이트 DDoS|초당 바이트 수|최대|인바운드 바이트 DDoS|없음|
|BytesDroppedDDoS|인바운드 바이트가 삭제된 DDoS|초당 바이트 수|최대|인바운드 바이트가 삭제된 DDoS|없음|
|BytesForwardedDDoS|인바운드 바이트가 전달된 DDoS|초당 바이트 수|최대|인바운드 바이트가 전달된 DDoS|없음|
|TCPBytesInDDoS|인바운드 TCP 바이트 DDoS|초당 바이트 수|최대|인바운드 TCP 바이트 DDoS|없음|
|TCPBytesDroppedDDoS|인바운드 TCP 바이트가 삭제된 DDoS|초당 바이트 수|최대|인바운드 TCP 바이트가 삭제된 DDoS|없음|
|TCPBytesForwardedDDoS|인바운드 TCP 바이트가 전달된 DDoS|초당 바이트 수|최대|인바운드 TCP 바이트가 전달된 DDoS|없음|
|UDPBytesInDDoS|인바운드 UDP 바이트 DDoS|초당 바이트 수|최대|인바운드 UDP 바이트 DDoS|없음|
|UDPBytesDroppedDDoS|인바운드 UDP 바이트가 삭제된 DDoS|초당 바이트 수|최대|인바운드 UDP 바이트가 삭제된 DDoS|없음|
|UDPBytesForwardedDDoS|인바운드 UDP 바이트가 전달된 DDoS|초당 바이트 수|최대|인바운드 UDP 바이트가 전달된 DDoS|없음|
|IfUnderDDoSAttack|DDoS 공격 진행 여부|카운트|최대|DDoS 공격 진행 여부|없음|
|DDoSTriggerTCPPackets|DDoS 완화를 트리거하는 인바운드 TCP 패킷|초당 개수|최대|DDoS 완화를 트리거하는 인바운드 TCP 패킷|없음|
|DDoSTriggerUDPPackets|DDoS 완화를 트리거하는 인바운드 UDP 패킷|초당 개수|최대|DDoS 완화를 트리거하는 인바운드 UDP 패킷|없음|
|DDoSTriggerSYNPackets|DDoS 완화를 트리거하는 인바운드 SYN 패킷|초당 개수|최대|DDoS 완화를 트리거하는 인바운드 SYN 패킷|없음|
|VipAvailability|데이터 경로 가용성|카운트|평균|기간당 평균 IP 주소 가용성|Port|
|ByteCount|바이트 수|카운트|합계|기간 내에 전송된 총 바이트 수|포트, 방향|
|PacketCount|패킷 수|카운트|합계|기간 내에 전송된 총 패킷 수|포트, 방향|
|SynCount|SYN 수|카운트|합계|기간 내에 전송된 총 SYN 패킷 수|포트, 방향|



## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|VM에 대 한 Ping 왕복 시간|밀리초|평균|대상 VM에 전송 된 Ping에 대 한 왕복 시간|SourceCustomerAddress,DestinationCustomerAddress|
|PingMeshProbesFailedPercent|VM에 대 한 Ping 실패|백분율|평균|대상 VM의 총 전송 된 Ping에 대 한 실패 한 Ping 수의 백분율|SourceCustomerAddress,DestinationCustomerAddress|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|ApplicationRuleHit|응용 프로그램 규칙 적중 횟수|카운트|합계|응용 프로그램 규칙이 적중 된 횟수|상태, 이유, 프로토콜|
|NetworkRuleHit|네트워크 규칙 적중 횟수|카운트|합계|네트워크 규칙이 적중 된 횟수|상태, 이유, 프로토콜|
|FirewallHealth|방화벽 성능 상태|백분율|평균|방화벽 성능 상태|상태, 이유|
|DataProcessed 됨|처리된 데이터|바이트|합계|방화벽에 의해 처리 된 총 데이터 양|없음|
|SNATPortUtilization|SNAT 포트 사용률|백분율|평균|SNAT 포트 사용률|없음|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|처리량|처리량|초당 바이트 수|평균|Application Gateway에서 제공하는 초당 바이트 수|없음|
|UnhealthyHostCount|비정상 호스트 수|카운트|평균|비정상 백 엔드 호스트 수|BackendSettingsPool|
|HealthyHostCount|정상 호스트 수|카운트|평균|정상 백 엔드 호스트 수|BackendSettingsPool|
|TotalRequests|총 요청 수|카운트|합계|Application Gateway가 제공하는 성공한 요청 수|BackendSettingsPool|
|AvgRequestCountPerHealthyHost|정상 호스트 당 분당 요청 수|카운트|평균|풀의 정상 백 엔드 호스트 당 분당 평균 요청 수|BackendSettingsPool|
|FailedRequests|실패한 요청|카운트|합계|Application Gateway가 제공하는 실패한 요청 수|BackendSettingsPool|
|ResponseStatus|응답 상태|카운트|합계|Application Gateway에서 반환된 HTTP 응답 상태|HttpStatusGroup|
|CurrentConnections|현재 연결|카운트|합계|Application Gateway와 설정된 현재 연결 수|없음|
|CpuUtilization|CPU 사용률|백분율|평균|Application Gateway의 현재 CPU 사용률|없음|
|CapacityUnits|현재 용량 단위|카운트|평균|소비 된 용량 단위|없음|
|이상|현재 계산 단위|카운트|평균|소비 된 계산 단위|없음|
|BackendResponseStatus|백 엔드 응답 상태|카운트|합계|백 엔드 멤버에 의해 생성 된 HTTP 응답 코드의 수입니다. Application Gateway에서 생성 된 응답 코드는 여기에 포함 되지 않습니다.|BackendServer, BackendPool, BackendHttpSetting, HttpStatusGroup|
|TlsProtocol|클라이언트 TLS 프로토콜|카운트|합계|Application Gateway와의 연결을 설정한 클라이언트에서 시작한 tls 및 비 TLS 요청 수입니다. TLS 프로토콜 배포를 보려면 차원 TLS 프로토콜을 기준으로 필터링 합니다.|수신기, TlsProtocol|
|BytesSent|보낸 바이트|바이트|합계|Application Gateway 클라이언트에 보낸 총 바이트 수입니다.|수신기|
|BytesReceived|수신된 바이트|바이트|합계|클라이언트에서 Application Gateway 받은 총 바이트 수입니다.|수신기|
|ClientRtt|클라이언트 RTT|밀리초|평균|클라이언트와 Application Gateway 사이의 평균 왕복 시간입니다. 이 메트릭은 연결을 설정 하 고 승인을 반환 하는 데 걸리는 시간을 나타냅니다.|수신기|
|ApplicationGatewayTotalTime|Application Gateway 총 시간|밀리초|평균|요청을 처리 하는 데 걸리는 평균 시간 및 응답을 보내는 데 걸리는 시간입니다. 이는 Application Gateway에서 HTTP 요청의 첫 번째 바이트를 수신 하는 시점부터 응답 전송 작업이 완료 되는 시간 까지의 평균 간격으로 계산 됩니다. 일반적으로 처리 시간, 요청 및 응답 패킷이 네트워크를 통해 이동 하는 시간 및 백 엔드 서버에서 응답 하는 데 걸린 시간을 Application Gateway 포함 하는 것이 중요 합니다.|수신기|
|BackendConnectTime|백 엔드 연결 시간|밀리초|평균|백 엔드 서버와의 연결을 설정 하는 데 걸린 시간|Listener, BackendServer, BackendPool, BackendHttpSetting|
|BackendFirstByteResponseTime|백 엔드 첫 번째 바이트 응답 시간|밀리초|평균|백 엔드 서버에 대 한 연결을 설정 하 고 응답 헤더의 첫 번째 바이트를 수신 하는 시작 사이의 시간 간격 (백 엔드 서버의 처리 시간에 가까운)|Listener, BackendServer, BackendPool, BackendHttpSetting|
|BackendLastByteResponseTime|백 엔드 마지막 바이트 응답 시간|밀리초|평균|백 엔드 서버에 대 한 연결을 설정 하 고 응답 본문의 마지막 바이트를 받는 시작 사이의 시간 간격입니다.|Listener, BackendServer, BackendPool, BackendHttpSetting|
|MatchedCount|웹 응용 프로그램 방화벽 총 규칙 배포|카운트|합계|들어오는 트래픽에 대 한 웹 응용 프로그램 방화벽 총 규칙 배포|RuleGroup, RuleId|
|BlockedCount|웹 응용 프로그램 방화벽 차단 된 요청 규칙 배포|카운트|합계|웹 응용 프로그램 방화벽 차단 된 요청 규칙 배포|RuleGroup, RuleId|
|BlockedReqCount|웹 응용 프로그램 방화벽 차단 된 요청 수|카운트|합계|웹 응용 프로그램 방화벽 차단 된 요청 수|없음|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|AverageBandwidth|게이트웨이 S2S 대역폭|초당 바이트 수|평균|초당 게이트웨이의 평균 사이트 간 대역폭(바이트)|없음|
|P2SBandwidth|게이트웨이 P2S 대역폭|초당 바이트 수|평균|초당 게이트웨이의 평균 지점 및 사이트 간 대역폭(바이트)|없음|
|P2SConnectionCount|P2S 연결 수|카운트|최대|게이트웨이의 지점 및 사이트 간 연결 수|프로토콜|
|TunnelAverageBandwidth|터널 대역폭|초당 바이트 수|평균|초당 터널의 평균 대역폭(바이트)|ConnectionName, RemoteIP|
|TunnelEgressBytes|터널 송신 바이트|바이트|합계|터널의 송신 바이트|ConnectionName, RemoteIP|
|TunnelIngressBytes|터널 수신 바이트|바이트|합계|터널의 수신 바이트|ConnectionName, RemoteIP|
|TunnelEgressPackets|터널 송신 패킷|카운트|합계|터널의 송신 패킷 수|ConnectionName, RemoteIP|
|TunnelIngressPackets|터널 수신 패킷|카운트|합계|터널의 수신 패킷 수|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|터널 송신 TS 불일치 패킷 삭제|카운트|합계|터널의 트래픽 선택기 불일치에서 나가는 패킷 삭제 수|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|터널 수신 TS 불일치 패킷 삭제|카운트|합계|터널의 트래픽 선택기 불일치에서 들어오는 패킷 삭제 수|ConnectionName, RemoteIP|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft. Network/expressRoutePorts

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|RxLightLevel|RxLightLevel|카운트|평균|DBm 광원 수준 (dBm)|링크, 레인|
|TxLightLevel|TxLightLevel|카운트|평균|Tx 라이트 수준 (dBm)|링크, 레인|
|AdminState|AdminState|카운트|평균|포트의 관리 상태|링크|
|LineProtocol|LineProtocol|카운트|평균|포트의 선 프로토콜 상태|링크|
|PortBitsInPerSecond|BitsInPerSecond|초당 개수|평균|초당 Azure 수신 비트|링크|
|PortBitsOutPerSecond|BitsOutPerSecond|초당 개수|평균|초당 Azure 송신 비트|링크|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|초당 개수|평균|초당 Azure 수신 비트|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|초당 개수|평균|초당 Azure 송신 비트|PeeringType|
|GlobalReachBitsInPerSecond|GlobalReachBitsInPerSecond|초당 개수|평균|초당 Azure 수신 비트|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|GlobalReachBitsOutPerSecond|초당 개수|평균|초당 Azure 송신 비트|PeeredCircuitSKey|
|BgpAvailability|Bgp 가용성|백분율|평균|모든 피어에 대 한 MSEE의 BGP 가용성|PeeringType, 피어|
|ArpAvailability|Arp 가용성|백분율|평균|M의 ARP 가용성 모든 피어를 확인 합니다.|PeeringType, 피어|
|QosDropBitsInPerSecond|DroppedInBitsPerSecond|초당 개수|평균|초당 삭제 된 데이터의 수신 비트 수|없음|
|QosDropBitsOutPerSecond|DroppedOutBitsPerSecond|초당 개수|평균|초당 삭제 된 데이터의 송신 비트 수|없음|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|초당 개수|평균|초당 Azure 수신 비트|없음|
|BitsOutPerSecond|BitsOutPerSecond|초당 개수|평균|초당 Azure 송신 비트|없음|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|초당 개수|평균|초당 Azure 수신 비트|없음|
|BitsOutPerSecond|BitsOutPerSecond|초당 개수|평균|초당 Azure 송신 비트|없음|

## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft. Network/expressRouteGateways

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|BitsInPerSecond|초당 개수|평균|초당 Azure 수신 비트|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|BitsOutPerSecond|초당 개수|평균|초당 Azure 송신 비트|ConnectionName|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|QpsByEndpoint|반환된 엔드포인트별 쿼리|카운트|합계|Traffic Manager 엔드포인트가 주어진 시간 내에 반환된 횟수|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|엔드포인트별 엔드포인트 상태|카운트|최대|엔드포인트의 프로브 상태가 "사용"이면 1이고 그렇지 않으면 0입니다.|EndpointName|



## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|ProbesFailedPercent|실패한 프로브 %|백분율|평균|실패한 연결 모니터링 프로브 %|없음|
|AverageRoundtripMs|평균 왕복 시간 (밀리초)|밀리초|평균|원본과 대상 간에 전송된 연결 모니터링 프로브의 평균 네트워크 왕복 시간(ms)|없음|
|ChecksFailedPercent|실패 한 비율 검사 (미리 보기)|백분율|평균|%의 연결 모니터링 검사가 실패 했습니다.|SourceAddress, SourceName, Sourceresourceid 여야, SourceType, Protocol, DestinationAddress, Destinationaddress, Destinationaddress, DestinationType, Destinationaddress, TestGroupName, Testgroupname|
|RoundTripTimeMs|왕복 시간 (ms) (미리 보기)|밀리초|평균|연결 모니터링 검사의 왕복 시간 (밀리초)|SourceAddress, SourceName, Sourceresourceid 여야, SourceType, Protocol, DestinationAddress, Destinationaddress, Destinationaddress, DestinationType, Destinationaddress, TestGroupName, Testgroupname|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|RequestCount|요청 수|카운트|합계|HTTP/S 프록시에서 제공하는 클라이언트 요청 수|HttpStatus, HttpStatusGroup, ClientRegion, Clientregion|
|RequestSize|요청 크기|바이트|합계|클라이언트에서 HTTP/S 프록시로 요청으로 전송된 바이트 수|HttpStatus, HttpStatusGroup, ClientRegion, Clientregion|
|ResponseSize|응답 크기|바이트|합계|HTTP/S 프록시에서 클라이언트로 응답으로 전송된 바이트 수|HttpStatus, HttpStatusGroup, ClientRegion, Clientregion|
|BillableResponseSize|청구 가능한 응답 크기|바이트|합계|HTTP/S 프록시에서 클라이언트에 응답으로 보낸 청구 가능 바이트 (요청당 최소 2KB) 수입니다.|HttpStatus, HttpStatusGroup, ClientRegion, Clientregion|
|BackendRequestCount|백 엔드 요청 수|카운트|합계|HTTP/S 프록시에서 백 엔드로 전송된 요청 수|HttpStatus, HttpStatusGroup, 백 엔드|
|BackendRequestLatency|백 엔드 요청 대기 시간|밀리초|평균|HTTP/S 프록시에서 백 엔드의 마지막 응답 바이트를 받을 때까지 HTTP/S 프록시에서 백 엔드로 요청이 전송될 때 계산된 시간|백 엔드|
|TotalLatency|총 대기 시간|밀리초|평균|클라이언트가 HTTP/S 프록시의 마지막 응답 바이트를 승인할 때까지 클라이언트 요청이 HTTP/S 프록시에서 수신될 때 계산된 시간|HttpStatus, HttpStatusGroup, ClientRegion, Clientregion|
|BackendHealthPercentage|백 엔드 상태 비율|백분율|평균|HTTP/S 프록시에서 백 엔드로 성공한 상태 프로브의 비율|백 엔드, BackendPool|
|WebApplicationFirewallRequestCount|웹 애플리케이션 방화벽 요청 수|카운트|합계|웹 애플리케이션 방화벽에서 처리된 클라이언트 요청 수|PolicyName, RuleName, Action|


## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|registration.all|등록 작업|카운트|합계|성공한 모든 등록 작업(만들기 업데이트 쿼리 및 삭제)입니다. |없음|
|registration.create|등록 만들기 작업|카운트|합계|성공한 모든 등록 만들기의 수입니다.|없음|
|registration.update|등록 업데이트 작업|카운트|합계|성공한 모든 등록 업데이트의 수입니다.|없음|
|registration.get|등록 읽기 작업|카운트|합계|성공한 모든 등록 쿼리의 수입니다.|없음|
|registration.delete|등록 삭제 작업|카운트|합계|성공한 모든 등록 삭제의 수입니다.|없음|
|incoming|들어오는 메시지|카운트|합계|성공한 모든 API 호출 전송의 수입니다. |없음|
|incoming.scheduled|전송된 예약된 푸시 알림|카운트|합계|취소된 예약된 푸시 알림|없음|
|incoming.scheduled.cancel|취소된 예약된 푸시 알림|카운트|합계|취소된 예약된 푸시 알림|없음|
|scheduled.pending|보류 중인 예약된 알림|카운트|합계|보류 중인 예약된 알림|없음|
|installation.all|설치 관리 작업|카운트|합계|설치 관리 작업|없음|
|installation.get|설치 작업 가져오기|카운트|합계|설치 작업 가져오기|없음|
|installation.upsert|설치 작업 만들기 또는 업데이트|카운트|합계|설치 작업 만들기 또는 업데이트|없음|
|installation.patch|설치 작업 패치|카운트|합계|설치 작업 패치|없음|
|installation.delete|설치 작업 삭제|카운트|합계|설치 작업 삭제|없음|
|outgoing.allpns.success|성공적인 알림|카운트|합계|성공한 모든 알림의 수입니다.|없음|
|outgoing.allpns.invalidpayload|페이로드 오류|카운트|합계|PNS가 잘못된 페이로드 오류를 반환하기 때문에 실패한 푸시의 수입니다.|없음|
|outgoing.allpns.pnserror|외부 알림 시스템 오류|카운트|합계|PNS와 통신하는 데 문제(인증 문제 제외)가 있기 때문에 실패한 푸시의 수입니다.|없음|
|outgoing.allpns.channelerror|채널 오류|카운트|합계|채널이 잘못되었거나 제한 또는 만료된 올바른 앱과 연결되지 않았으므로 실패 한 푸시의 수입니다.|없음|
|outgoing.allpns.badorexpiredchannel|잘못되거나 만료된 채널 오류|카운트|합계|등록의 channel/token/registrationId가 만료되었거나 올바르지 않기 때문에 실패한 푸시의 수입니다.|없음|
|outgoing.wns.success|WNS 성공적인 알림|카운트|합계|성공한 모든 알림의 수입니다.|없음|
|outgoing.wns.invalidcredentials|WNS 권한 부여 오류(잘못된 자격 증명)|카운트|합계|PNS가 제공된 자격 증명을 수락하지 않았거나 자격 증명이 차단되어 실패한 푸시의 수입니다. (Windows Live는 자격 증명을 인식하지 못함)|없음|
|outgoing.wns.badchannel|WNS 잘못된 채널 오류|카운트|합계|등록의 ChannelURI가 인식되지 않아 실패한 푸시의 수입니다(WNS 상태: 404 찾을 수 없음).|없음|
|outgoing.wns.expiredchannel|WNS 만료된 채널 오류|카운트|합계|ChannelURI가 만료되어 실패한 푸시의 수입니다(WNS 상태: 410 없음).|없음|
|outgoing.wns.throttled|WNS 제한된 알림|카운트|합계|WNS가 이 앱을 제한하기 때문에 실패한 푸시의 수입니다(WNS 상태: 406 승인 금지).|없음|
|outgoing.wns.tokenproviderunreachable|WNS 권한 부여 오류(연결할 수 없음)|카운트|합계|Windows Live에 연결할 수 없습니다.|없음|
|outgoing.wns.invalidtoken|WNS 권한 부여 오류(잘못된 토큰)|카운트|합계|WNS에 제공한 토큰이 잘못되었습니다(WNS 상태: 401 권한 없음).|없음|
|outgoing.wns.wrongtoken|WNS 권한 부여 오류(잘못된 토큰)|카운트|합계|WNS에 제공된 토큰은 유효하지만 다른 애플리케이션에 대해서는 유효하지 않습니다(WNS 상태: 403 사용 권한 없음). 등록의 ChannelURI가 다른 앱에 연결된 경우 이 문제가 발생할 수 있습니다. 클라이언트 앱은 자격 증명이 알림 허브에 있는 동일한 앱과 연결되어 있는지 확인합니다.|없음|
|outgoing.wns.invalidnotificationformat|WNS 잘못된 알림 형식|카운트|합계|알림의 형식이 잘못되었습니다(WNS 상태: 400). WNS가 잘못된 모든 페이로드를 거부하지는 않습니다.|없음|
|outgoing.wns.invalidnotificationsize|WNS 잘못된 알림 크기 오류|카운트|합계|알림 페이로드가 너무 큽니다(WNS 상태: 413).|없음|
|outgoing.wns.channelthrottled|WNS 채널 제한|카운트|합계|등록의 ChannelURI가 제한되어 알림이 삭제되었습니다(WNS 응답 헤더: X-WNS-NotificationStatus:channelThrottled).|없음|
|outgoing.wns.channeldisconnected|WNS 채널 연결 끊김|카운트|합계|등록의 ChannelURI가 제한되어 알림이 삭제되었습니다(WNS 응답 헤더: X-WNS-DeviceConnectionStatus: disconnected).|없음|
|outgoing.wns.dropped|WNS 삭제된 알림|카운트|합계|등록의 ChannelURI가 제한되어 알림이 삭제되었습니다(X-WNS-NotificationStatus: dropped but not X-WNS-DeviceConnectionStatus: disconnected).|없음|
|outgoing.wns.pnserror|WNS 오류|카운트|합계|WNS와의 통신 오류로 인해 알림이 배달되지 않습니다.|없음|
|outgoing.wns.authenticationerror|WNS 인증 오류|카운트|합계|Windows Live와의 통신 오류(잘못된 자격 증명 또는 잘못된 토큰)로 인해 알림이 배달되지 않습니다.|없음|
|outgoing.apns.success|APNS 성공적인 알림|카운트|합계|성공한 모든 알림의 수입니다.|없음|
|outgoing.apns.invalidcredentials|APNS 권한 부여 오류|카운트|합계|PNS가 제공된 자격 증명을 수락하지 않았거나 자격 증명이 차단되어 실패한 푸시의 수입니다.|없음|
|outgoing.apns.badchannel|APNS 잘못된 채널 오류|카운트|합계|토큰이 잘못되어 실패한 푸시의 수입니다(APNS 상태 코드: 8).|없음|
|outgoing.apns.expiredchannel|APNS 만료된 채널 오류|카운트|합계|APNS 피드백 채널에서 무효화된 토큰의 수입니다.|없음|
|outgoing.apns.invalidnotificationsize|APNS 잘못된 알림 크기 오류|카운트|합계|페이로드가 너무 커서 실패한 푸시의 수입니다(APNS 상태 코드: 7).|없음|
|outgoing.apns.pnserror|APNS 오류|카운트|합계|APNS와의 통신 오류로 인해 실패한 푸시의 수입니다.|없음|
|outgoing.gcm.success|GCM 성공적인 알림|카운트|합계|성공한 모든 알림의 수입니다.|없음|
|outgoing.gcm.invalidcredentials|GCM 권한 부여 오류(잘못된 자격 증명)|카운트|합계|PNS가 제공된 자격 증명을 수락하지 않았거나 자격 증명이 차단되어 실패한 푸시의 수입니다.|없음|
|outgoing.gcm.badchannel|GCM 잘못된 채널 오류|카운트|합계|등록의 registrationId가 인식되지 않기 때문에 실패한 푸시의 수입니다(GCM 결과: 잘못된 등록).|없음|
|outgoing.gcm.expiredchannel|GCM 만료된 채널 오류|카운트|합계|등록의 registrationId가 만료되었기 때문에 실패한 푸시의 수입니다(GCM 결과: NotRegistered).|없음|
|outgoing.gcm.throttled|GCM 제한된 알림|카운트|합계|GCM이 이 앱을 제한하기 때문에 실패한 푸시의 수(GCM 상태 코드: 501-599 또는 결과: Unavailable).|없음|
|outgoing.gcm.invalidnotificationformat|GCM 잘못된 알림 형식|카운트|합계|페이로드 형식이 올바르지 않기 때문에 실패한 푸시의 수입니다(GCM 결과: InvalidDataKey 또는 InvalidTtl).|없음|
|outgoing.gcm.invalidnotificationsize|GCM 잘못된 알림 크기 오류|카운트|합계|페이로드가 너무 커서 실패한 푸시의 수입니다(GCM 결과: MessageTooBig).|없음|
|outgoing.gcm.wrongchannel|GCM 잘못된 채널 오류|카운트|합계|등록의 registrationId가 현재 앱과 연결되지 않기 때문에 실패한 푸시의 수입니다(GCM 결과: InvalidPackageName).|없음|
|outgoing.gcm.pnserror|GCM 오류|카운트|합계|GCM과의 통신 오류로 인해 실패한 푸시의 수입니다.|없음|
|outgoing.gcm.authenticationerror|GCM 인증 오류|카운트|합계|PNS가 제공된 자격 증명을 수락하지 않았거나, 자격 증명이 차단되었거나, 앱에서 SenderId가 올바르게 구성되지 않았기 때문에 실패한 푸시의 수입니다(GCM 결과: MismatchedSenderId).|없음|
|outgoing.mpns.success|MPNS 성공적인 알림|카운트|합계|성공한 모든 알림의 수입니다.|없음|
|outgoing.mpns.invalidcredentials|MPNS 잘못된 자격 증명|카운트|합계|PNS가 제공된 자격 증명을 수락하지 않았거나 자격 증명이 차단되어 실패한 푸시의 수입니다.|없음|
|outgoing.mpns.badchannel|MPNS 잘못된 채널 오류|카운트|합계|등록의 ChannelURI가 인식되지 않아 실패한 푸시의 수입니다(MPNS 상태: 404 찾을 수 없음).|없음|
|outgoing.mpns.throttled|MPNS 제한된 알림|카운트|합계|MPNS가 이 앱을 제한하기 때문에 실패한 푸시의 수입니다(WNS MPNS: 406 승인 금지).|없음|
|outgoing.mpns.invalidnotificationformat|MPNS 잘못된 알림 형식|카운트|합계|알림의 페이로드가 너무 커서 실패한 푸시의 수입니다.|없음|
|outgoing.mpns.channeldisconnected|MPNS 채널 연결 끊김|카운트|합계|등록의 ChannelURI 연결이 끊어졌으므로 실패한 푸시의 수입니다(MPNS 상태: 412 찾을 수 없음).|없음|
|outgoing.mpns.dropped|MPNS 삭제된 알림|카운트|합계|MPNS에서 삭제된 푸시의 수입니다(MPNS 응답 헤더: X-NotificationStatus: QueueFull 또는 Suppressed).|없음|
|outgoing.mpns.pnserror|MPNS 오류|카운트|합계|MPNS와의 통신 오류로 인해 실패한 푸시의 수입니다.|없음|
|outgoing.mpns.authenticationerror|MPNS 인증 오류|카운트|합계|PNS가 제공된 자격 증명을 수락하지 않았거나 자격 증명이 차단되어 실패한 푸시의 수입니다.|없음|
|notificationhub.pushes|모든 나가는 알림 수|카운트|합계|알림 허브의 모든 나가는 알림 수|없음|
|incoming.all.requests|들어오는 모든 요청|카운트|합계|알림 허브에 대해 들어오는 전체 요청|없음|
|incoming.all.failedrequests|들어오는 모든 실패한 요청|카운트|합계|알림 허브에 대해 들어오는 실패한 전체 요청|없음|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|Average_% Free Inodes|% 사용 가능한 Inodes|카운트|평균|Average_% Free Inodes|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Space|% 사용 가능한 공간|카운트|평균|Average_% Free Space|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Inodes|% 사용된 Inodes|카운트|평균|Average_% Used Inodes|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Space|% 사용된 공간|카운트|평균|Average_% Used Space|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Read Bytes/sec|디스크 읽기 바이트/초|카운트|평균|Average_Disk Read Bytes/sec|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Reads/sec|디스크 읽기/초|카운트|평균|Average_Disk Reads/sec|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|디스크 전송/초|카운트|평균|Average_Disk Transfers/sec|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Write Bytes/sec|디스크 쓰기 바이트/초|카운트|평균|Average_Disk Write Bytes/sec|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Writes/sec|디스크 쓰기/초|카운트|평균|Average_Disk Writes/sec|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|사용 가능한 메가바이트|카운트|평균|Average_Free Megabytes|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical Disk Bytes/sec|논리 디스크 바이트/초|카운트|평균|Average_Logical Disk Bytes/sec|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Available Memory|% 사용 가능한 메모리|카운트|평균|Average_% Available Memory|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Available Swap Space|% 사용 가능한 스왑 공간|카운트|평균|Average_% Available Swap Space|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Memory|% 사용된 메모리|카운트|평균|Average_% Used Memory|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Swap Space|% 사용된 스왑 공간|카운트|평균|Average_% Used Swap Space|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Memory|사용 가능한 MB 메모리|카운트|평균|Average_Available MBytes Memory|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Swap|사용 가능한 MB 스왑|카운트|평균|Average_Available MBytes Swap|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Reads/sec|페이지 읽기/초|카운트|평균|Average_Page Reads/sec|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Writes/sec|페이지 쓰기/초|카운트|평균|Average_Page Writes/sec|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/sec|페이지/초|카운트|평균|Average_Pages/sec|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used MBytes Swap Space|사용된 MB 스왑 공간|카운트|평균|Average_Used MBytes Swap Space|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory MBytes|사용된 메모리 MB|카운트|평균|Average_Used Memory MBytes|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Transmitted|전송된 총 바이트|카운트|평균|Average_Total Bytes Transmitted|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Received|받은 총 바이트|카운트|평균|Average_Total Bytes Received|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes|총 바이트|카운트|평균|Average_Total Bytes|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Transmitted|전송된 총 패킷|카운트|평균|Average_Total Packets Transmitted|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Received|받은 총 패킷|카운트|평균|Average_Total Packets Received|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Rx Errors|총 Rx 오류|카운트|평균|Average_Total Rx Errors|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Tx Errors|총 Tx 오류|카운트|평균|Average_Total Tx Errors|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Collisions|총 충돌|카운트|평균|Average_Total Collisions|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. 디스크 초/읽기|Avg. Disk sec/Read|카운트|평균|Average_Avg. 디스크 초/읽기|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. 디스크 초/전송|Avg. Disk sec/Transfer|카운트|평균|Average_Avg. 디스크 초/전송|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. 디스크 초/쓰기|Avg. Disk sec/Write|카운트|평균|Average_Avg. 디스크 초/쓰기|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical Disk Bytes/sec|물리적 디스크 바이트/초|카운트|평균|Average_Physical Disk Bytes/sec|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct Privileged Time|Pct 권한이 부여된 시간|카운트|평균|Average_Pct Privileged Time|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct User Time|Pct 사용자 시간|카운트|평균|Average_Pct User Time|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory kBytes|사용된 메모리 KB|카운트|평균|Average_Used Memory kBytes|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual Shared Memory|가상 공유 메모리|카운트|평균|Average_Virtual Shared Memory|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% DPC Time|% DPC 시간|카운트|평균|Average_% DPC Time|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Idle Time|% 유휴 시간|카운트|평균|Average_% Idle Time|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Interrupt Time|% 인터럽트 시간|카운트|평균|Average_% Interrupt Time|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% IO Wait Time|% IO 대기 시간|카운트|평균|Average_% IO Wait Time|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Nice Time|% Nice 시간|카운트|평균|Average_% Nice Time|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Privileged Time|% 권한이 부여된 시간|카운트|평균|Average_% Privileged Time|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Processor Time|% Processor Time|카운트|평균|Average_% Processor Time|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% User Time|% 사용자 시간|카운트|평균|Average_% User Time|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Physical Memory|사용 가능한 실제 메모리|카운트|평균|Average_Free Physical Memory|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Space in Paging Files|페이징 파일에 사용 가능한 공간|카운트|평균|Average_Free Space in Paging Files|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Virtual Memory|사용 가능한 가상 메모리|카운트|평균|Average_Free Virtual Memory|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|프로세스|카운트|평균|Average_Processes|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size Stored In Paging Files|페이징 파일에 저장된 크기|카운트|평균|Average_Size Stored In Paging Files|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|작동 시간|카운트|평균|Average_Uptime|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|사용자|카운트|평균|Average_Users|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. 디스크 초/읽기|Avg. Disk sec/Read|카운트|평균|Average_Avg. 디스크 초/읽기|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. 디스크 초/쓰기|Avg. Disk sec/Write|카운트|평균|Average_Avg. 디스크 초/쓰기|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current Disk Queue Length|Current Disk Queue Length|카운트|평균|Average_Current Disk Queue Length|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Reads/sec|디스크 읽기/초|카운트|평균|Average_Disk Reads/sec|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|디스크 전송/초|카운트|평균|Average_Disk Transfers/sec|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Writes/sec|디스크 쓰기/초|카운트|평균|Average_Disk Writes/sec|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|사용 가능한 메가바이트|카운트|평균|Average_Free Megabytes|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Space|% 사용 가능한 공간|카운트|평균|Average_% Free Space|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes|Available MBytes|카운트|평균|Average_Available MBytes|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Committed Bytes In Use|% Committed Bytes In Use|카운트|평균|Average_% Committed Bytes In Use|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Received/sec|Bytes Received/sec|카운트|평균|Average_Bytes Received/sec|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Sent/sec|Bytes Sent/sec|카운트|평균|Average_Bytes Sent/sec|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Total/sec|Bytes Total/sec|카운트|평균|Average_Bytes Total/sec|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Processor Time|% Processor Time|카운트|평균|Average_% Processor Time|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor Queue Length|프로세서 큐 길이|카운트|평균|Average_Processor Queue Length|컴퓨터, ObjectName, InstanceName, CounterPath, SourceSystem|
|Heartbeat|Heartbeat|카운트|합계|Heartbeat|컴퓨터, OSType, 버전, SourceComputerId|
|업데이트|업데이트|카운트|평균|업데이트|컴퓨터, 제품, 분류, UpdateState, 선택 사항, 승인 됨|
|이벤트|이벤트|카운트|평균|이벤트|원본, 이벤트 로그, 컴퓨터, EventCategory, Eventcategory, EventLevelName, EventID|

## <a name="microsoftpeeringpeeringservices"></a>Microsoft 피어 링/peeringServices

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|PrefixLatency|접두사 대기 시간|밀리초|평균|중앙값 전위 대기 시간|PrefixName|

## <a name="microsoftpeeringpeerings"></a>Microsoft 피어 링/피어 링

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|SessionAvailabilityV4|세션 가용성 V4|백분율|평균|V4 세션의 가용성|ConnectionId|
|SessionAvailabilityV6|세션 가용성 V6|백분율|평균|V6 세션의 가용성|ConnectionId|
|IngressTrafficRate|수신 트래픽 율|BitsPerSecond|평균|수신 트래픽 전송률 (비트/초)|ConnectionId|
|EgressTrafficRate|송신 트래픽 요금|BitsPerSecond|평균|초당 송신 트래픽 전송률 (비트)|ConnectionId|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|QueryDuration|쿼리 기간|밀리초|평균|마지막 간격의 DAX 쿼리 기간|차원 없음|
|QueryPoolJobQueueLength|스레드: 쿼리 풀 작업 큐 길이|카운트|평균|쿼리 스레드 풀의 큐에 있는 작업 수입니다.|차원 없음|
|qpu_high_utilization_metric|QPU 높은 사용률|카운트|합계|지난 1분 간의 QPU 높은 사용률로, QPU 사용률이 높으면 1, 그렇지 않으면 0|차원 없음|
|memory_metric|메모리|바이트|평균|메모리. A1은 0-3GB, A2는 0-5GB, A3는 0-10GB, A4는 0-25GB, A5는 0-50GB, A6는 0-100GB 범위|차원 없음|
|memory_thrashing_metric|메모리 쓰래싱|백분율|평균|평균 메모리 쓰래싱입니다.|차원 없음|



## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|카운트|합계|Microsoft.Relay의 성공적인 ListenerConnections.|EntityName, OperationResult|
|ListenerConnections-ClientError|ListenerConnections-ClientError|카운트|합계|Microsoft.Relay의 총 ListenerConnections에서의 ClientError.|EntityName, OperationResult|
|ListenerConnections-ServerError|ListenerConnections-ServerError|카운트|합계|Microsoft.Relay의 ListenerConnections에서의 ServerError.|EntityName, OperationResult|
|SenderConnections-Success|SenderConnections-Success|카운트|합계|Microsoft.Relay의 성공적인 SenderConnections.|EntityName, OperationResult|
|SenderConnections-ClientError|SenderConnections-ClientError|카운트|합계|Microsoft.Relay의 SenderConnections에서 ClientError.|EntityName, OperationResult|
|SenderConnections-ServerError|SenderConnections-ServerError|카운트|합계|Microsoft.Relay의 SenderConnections에서의 ServerError.|EntityName, OperationResult|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|카운트|합계|Microsoft.Relay의 총 ListenerConnections.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|카운트|합계|Microsoft.Relay의 총 SenderConnections 요청.|EntityName|
|ActiveConnections|ActiveConnections|카운트|합계|Microsoft.Relay의 총 ActiveConnections.|EntityName|
|ActiveListeners|ActiveListeners|카운트|합계|Microsoft.Relay의 총 ActiveListeners.|EntityName|
|BytesTransferred|BytesTransferred|카운트|합계|Microsoft.Relay의 총 BytesTransferred.|EntityName|
|ListenerDisconnects|ListenerDisconnects|카운트|합계|Microsoft.Relay의 총 ListenerDisconnects.|EntityName|
|SenderDisconnects|SenderDisconnects|카운트|합계|Microsoft.Relay의 총 SenderDisconnects.|EntityName|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|SearchLatency|검색 대기 시간|초|평균|검색 서비스에 대한 평균 검색 대기 시간|없음|
|SearchQueriesPerSecond|초당 검색 쿼리 수|초당 개수|평균|Search 서비스에 대한 초당 검색 쿼리|없음|
|ThrottledSearchQueriesPercentage|제한된 검색 쿼리 백분율|백분율|평균|검색 서비스에 대해 제한된 검색 쿼리의 백분율|없음|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|SuccessfulRequests|성공한 요청|카운트|합계|네임스페이스에 대한 총 성공한 요청|EntityName, OperationResult|
|ServerErrors|서버 오류.|카운트|합계|Microsoft.ServiceBus에 대한 서버 오류.|EntityName, OperationResult|
|UserErrors|사용자 오류.|카운트|합계|Microsoft.ServiceBus에 대한 사용자 오류.|EntityName, OperationResult|
|ThrottledRequests|제한된 요청.|카운트|합계|Microsoft.ServiceBus에 대한 제한된 요청.|EntityName, OperationResult|
|IncomingRequests|들어오는 요청|카운트|합계|Microsoft.ServiceBus에 대한 들어오는 요청.|EntityName|
|IncomingMessages|들어오는 메시지|카운트|합계|Microsoft.ServiceBus에 대한 들어오는 메시지.|EntityName|
|OutgoingMessages|보내는 메시지|카운트|합계|Microsoft.ServiceBus에 대한 보내는 메시지.|EntityName|
|ActiveConnections|ActiveConnections|카운트|합계|Microsoft.ServiceBus에 대한 총 활성 연결.|없음|
|ConnectionsOpened|열린 연결.|카운트|평균|Microsoft.ServiceBus에 대한 열린 연결.|EntityName|
|ConnectionsClosed|끊어진 연결.|카운트|평균|Microsoft.ServiceBus에 대한 끊어진 연결.|EntityName|
|크기|크기|바이트|평균|큐/토픽 크기(바이트)|EntityName|
|메시지|큐/토픽에 있는 메시지 수|카운트|평균|큐/토픽에 있는 메시지 수|EntityName|
|ActiveMessages|큐/토픽에 있는 활성 메시지 수|카운트|평균|큐/토픽에 있는 활성 메시지 수|EntityName|
|DeadletteredMessages|큐/토픽에서 배달 못 한 메시지의 수입니다.|카운트|평균|큐/토픽에서 배달 못 한 메시지의 수입니다.|EntityName|
|ScheduledMessages|큐/토픽에 있는 예약 된 메시지 수입니다.|카운트|평균|큐/토픽에 있는 예약 된 메시지 수입니다.|EntityName|
|NamespaceCpuUsage|CPU|백분율|최대|Service bus 프리미엄 네임 스페이스 CPU 사용량 메트릭|복제본|
|NamespaceMemoryUsage|메모리 사용량|백분율|최대|서비스 버스 프리미엄 네임 스페이스 메모리 사용량 메트릭입니다.|복제본|
|CPUXNS|CPU (사용 되지 않음)|백분율|최대|Service bus 프리미엄 네임 스페이스 CPU 사용량 메트릭 이 메트릭은 depricated입니다. 대신 CPU 메트릭 (NamespaceCpuUsage)을 사용 하세요.|복제본|
|WSXNS|메모리 사용 (사용 되지 않음)|백분율|최대|서비스 버스 프리미엄 네임 스페이스 메모리 사용량 메트릭입니다. 이 메트릭은 사용되지 않습니다. 대신 Memory Usage (NamespaceMemoryUsage) 메트릭을 사용 하세요.|복제본|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|카운트|평균|이 컨테이너에 할당 된 Cpu (밀리초 코어)|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|바이트|평균|이 컨테이너에 할당 된 메모리 (MB)|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualCpu|ActualCpu|카운트|평균|밀리초 코어의 실제 CPU 사용량|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|ActualMemory|바이트|평균|실제 메모리 사용량 (MB)|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|CpuUtilization|CpuUtilization|백분율|평균|이 컨테이너의 CPU 사용률 (%)은 AllocatedCpu의 백분율입니다.|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|백분율|평균|이 컨테이너의 CPU 사용률 (%)은 AllocatedCpu의 백분율입니다.|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|카운트|평균|Service Fabric 메시 응용 프로그램의 상태|ApplicationName, 상태|
|ServiceStatus|ServiceStatus|카운트|평균|Service Fabric 메시 응용 프로그램의 서비스 상태|ApplicationName, Status, ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|카운트|평균|Service Fabric 메시 응용 프로그램의 서비스 복제본 상태|ApplicationName, Status, ServiceName, ServiceReplicaName|
|ContainerStatus|ContainerStatus|카운트|평균|Service Fabric 메시 응용 프로그램의 컨테이너 상태|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, Status|
|RestartCount|RestartCount|카운트|평균|Service Fabric 메시 응용 프로그램에서 컨테이너의 다시 시작 횟수|ApplicationName, Status, ServiceName, ServiceReplicaName, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|ConnectionCount|연결 수|카운트|최대|사용자 연결의 양.|엔드포인트|
|MessageCount|메시지 수|카운트|합계|총 메시지 양|없음|
|InboundTraffic|인바운드 트래픽|바이트|합계|서비스의 인바운드 트래픽|없음|
|OutboundTraffic|아웃바운드 트래픽|바이트|합계|서비스의 아웃바운드 트래픽|없음|
|UserErrors|사용자 오류|백분율|최대|사용자 오류의 비율|없음|
|SystemErrors|시스템 오류|백분율|최대|시스템 오류의 비율|없음|





## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|cpu_percent|CPU 비율|백분율|평균|CPU 비율|없음|
|physical_data_read_percent|데이터 IO 비율|백분율|평균|데이터 IO 비율|없음|
|log_write_percent|로그 IO 비율|백분율|평균|로그 IO 백분율입니다. 데이터 웨어하우스에는 적용 되지 않습니다.|없음|
|dtu_consumption_percent|DTU 비율|백분율|평균|DTU 백분율. DTU 기반 데이터베이스에 적용 됩니다.|없음|
|스토리지|사용 되는 데이터 공간|바이트|최대|사용 되는 데이터 공간. 데이터 웨어하우스에는 적용 되지 않습니다.|없음|
|connection_successful|성공적인 연결|카운트|합계|성공적인 연결|없음|
|connection_failed|실패한 연결|카운트|합계|실패한 연결|없음|
|blocked_by_firewall|방화벽에 의해 차단|카운트|합계|방화벽에 의해 차단|없음|
|교착 상태|교착 상태|카운트|합계|상태가. 데이터 웨어하우스에는 적용 되지 않습니다.|없음|
|storage_percent|사용 되는 데이터 공간 (%)|백분율|최대|사용 되는 데이터 공간 (%)입니다. 데이터 웨어하우스 또는 대규모 데이터베이스에는 적용 되지 않습니다.|없음|
|xtp_storage_percent|메모리 내 OLTP 스토리지 백분율|백분율|평균|메모리 내 OLTP 저장소 백분율입니다. 데이터 웨어하우스에는 적용 되지 않습니다.|없음|
|workers_percent|작업자 백분율|백분율|평균|작업자 비율. 데이터 웨어하우스에는 적용 되지 않습니다.|없음|
|sessions_percent|세션 백분율|백분율|평균|세션 백분율. 데이터 웨어하우스에는 적용 되지 않습니다.|없음|
|dtu_limit|DTU 제한|카운트|평균|DTU 한도입니다. DTU 기반 데이터베이스에 적용 됩니다.|없음|
|dtu_used|DTU 사용됨|카운트|평균|DTU를 사용 했습니다. DTU 기반 데이터베이스에 적용 됩니다.|없음|
|cpu_limit|CPU 제한|카운트|평균|CPU 제한. VCore 기반 데이터베이스에 적용 됩니다.|없음|
|cpu_used|사용 되는 CPU|카운트|평균|사용 되는 CPU입니다. VCore 기반 데이터베이스에 적용 됩니다.|없음|
|dwu_limit|DWU 제한|카운트|최대|DWU 제한. 데이터 웨어하우스에만 적용 됩니다.|없음|
|dwu_consumption_percent|DWU 백분율|백분율|최대|DWU 백분율. 데이터 웨어하우스에만 적용 됩니다.|없음|
|dwu_used|DWU 사용됨|카운트|최대|DWU를 사용 했습니다. 데이터 웨어하우스에만 적용 됩니다.|없음|
|cache_hit_percent|캐시 적중 비율|백분율|최대|캐시 적중 비율입니다. 데이터 웨어하우스에만 적용 됩니다.|없음|
|cache_used_percent|캐시 사용 비율|백분율|최대|캐시 사용 백분율입니다. 데이터 웨어하우스에만 적용 됩니다.|없음|
|sqlserver_process_core_percent|SQL Server 프로세스 코어 백분율|백분율|최대|운영 체제에 따라 측정 되는 SQL Server 프로세스의 CPU 사용량 백분율입니다. 현재 서버 리스 데이터베이스에 대해서만 사용할 수 있습니다.|없음|
|sqlserver_process_memory_percent|SQL Server 프로세스 메모리 비율|백분율|최대|운영 체제에 따라 측정 되는 SQL Server 프로세스의 메모리 사용량 백분율입니다. 현재 서버 리스 데이터베이스에 대해서만 사용할 수 있습니다.|없음|
|tempdb_data_size|Tempdb 데이터 파일 크기 (Kb)|카운트|최대|Tempdb 데이터 파일 크기 (Kb)입니다. 데이터 웨어하우스에는 적용 되지 않습니다.|없음|
|tempdb_log_size|Tempdb 로그 파일 크기 (Kb)|카운트|최대|Tempdb 로그 파일 크기 (Kb)입니다. 데이터 웨어하우스에는 적용 되지 않습니다.|없음|
|tempdb_log_used_percent|Tempdb 백분율 로그가 사용 됨|백분율|최대|Tempdb 백분율 로그가 사용 되었습니다. 데이터 웨어하우스에는 적용 되지 않습니다.|없음|
|local_tempdb_usage_percent|로컬 tempdb 백분율|백분율|평균|로컬 tempdb 비율입니다. 데이터 웨어하우스에만 적용 됩니다.|없음|
|app_cpu_billed|청구 되는 앱 CPU|카운트|합계|청구 되는 앱 CPU입니다. 서버를 사용 하지 않는 데이터베이스에 적용 됩니다.|없음|
|app_cpu_percent|앱 CPU 비율|백분율|평균|앱 CPU 비율입니다. 서버를 사용 하지 않는 데이터베이스에 적용 됩니다.|없음|
|app_memory_percent|앱 메모리 비율|백분율|평균|앱 메모리 비율입니다. 서버를 사용 하지 않는 데이터베이스에 적용 됩니다.|없음|
|allocated_data_storage|할당 된 데이터 공간|바이트|평균|할당 된 데이터 저장소입니다. 데이터 웨어하우스에는 적용 되지 않습니다.|없음|
|memory_usage_percent|메모리 비율|백분율|최대|메모리 비율입니다. 데이터 웨어하우스에만 적용 됩니다.|없음|
|full_backup_size_bytes|전체 백업 저장소 크기|바이트|최대|누적 전체 백업 저장소 크기입니다. VCore 기반 데이터베이스에 적용 됩니다. Hyperscale 데이터베이스에는 적용 되지 않습니다.|없음|
|diff_backup_size_bytes|차등 백업 저장소 크기|바이트|최대|누적 차등 백업 저장소 크기입니다. VCore 기반 데이터베이스에 적용 됩니다. Hyperscale 데이터베이스에는 적용 되지 않습니다.|없음|
|log_backup_size_bytes|로그 백업 저장소 크기|바이트|최대|누적 로그 백업 저장소 크기입니다. VCore 기반 데이터베이스에 적용 됩니다. Hyperscale 데이터베이스에는 적용 되지 않습니다.|없음|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|cpu_percent|CPU 비율|백분율|평균|CPU 비율|없음|
|database_cpu_percent|CPU 비율|백분율|평균|CPU 비율|DatabaseResourceId|
|physical_data_read_percent|데이터 IO 비율|백분율|평균|데이터 IO 비율|없음|
|database_physical_data_read_percent|데이터 IO 비율|백분율|평균|데이터 IO 비율|DatabaseResourceId|
|log_write_percent|로그 IO 비율|백분율|평균|로그 IO 비율|없음|
|database_log_write_percent|로그 IO 비율|백분율|평균|로그 IO 비율|DatabaseResourceId|
|dtu_consumption_percent|DTU 비율|백분율|평균|DTU 백분율. DTU 기반 탄력적 풀에 적용 됩니다.|없음|
|database_dtu_consumption_percent|DTU 비율|백분율|평균|DTU 비율|DatabaseResourceId|
|storage_percent|사용 되는 데이터 공간 (%)|백분율|평균|사용 되는 데이터 공간 (%)|없음|
|workers_percent|작업자 백분율|백분율|평균|작업자 백분율|없음|
|database_workers_percent|작업자 백분율|백분율|평균|작업자 백분율|DatabaseResourceId|
|sessions_percent|세션 백분율|백분율|평균|세션 백분율|없음|
|database_sessions_percent|세션 백분율|백분율|평균|세션 백분율|DatabaseResourceId|
|eDTU_limit|eDTU 제한|카운트|평균|eDTU 제한. DTU 기반 탄력적 풀에 적용 됩니다.|없음|
|storage_limit|데이터 최대 크기|바이트|평균|데이터 최대 크기|없음|
|eDTU_used|eDTU 사용|카운트|평균|eDTU를 사용 했습니다. DTU 기반 탄력적 풀에 적용 됩니다.|없음|
|database_eDTU_used|eDTU 사용|카운트|평균|eDTU 사용|DatabaseResourceId|
|storage_used|사용 되는 데이터 공간|바이트|평균|사용 되는 데이터 공간|없음|
|database_storage_used|사용 되는 데이터 공간|바이트|평균|사용 되는 데이터 공간|DatabaseResourceId|
|xtp_storage_percent|메모리 내 OLTP 스토리지 백분율|백분율|평균|메모리 내 OLTP 스토리지 백분율|없음|
|cpu_limit|CPU 제한|카운트|평균|CPU 제한. VCore 기반 탄력적 풀에 적용 됩니다.|없음|
|database_cpu_limit|CPU 제한|카운트|평균|CPU 제한|DatabaseResourceId|
|cpu_used|사용 되는 CPU|카운트|평균|사용 되는 CPU입니다. VCore 기반 탄력적 풀에 적용 됩니다.|없음|
|database_cpu_used|사용 되는 CPU|카운트|평균|사용 되는 CPU|DatabaseResourceId|
|sqlserver_process_core_percent|SQL Server 프로세스 코어 백분율|백분율|최대|SQL DB 프로세스의 백분율로 나타낸 CPU 사용량입니다. 탄력적 풀에 적용 됩니다.|없음|
|sqlserver_process_memory_percent|SQL Server 프로세스 메모리 비율|백분율|최대|SQL DB 프로세스의 백분율로 나타낸 메모리 사용량입니다. 탄력적 풀에 적용 됩니다.|없음|
|tempdb_data_size|Tempdb 데이터 파일 크기 (Kb)|카운트|최대|Tempdb 데이터 파일 크기 (Kb)|없음|
|tempdb_log_size|Tempdb 로그 파일 크기 (Kb)|카운트|최대|Tempdb 로그 파일 크기 (Kb)|없음|
|tempdb_log_used_percent|Tempdb 백분율 로그가 사용 됨|백분율|최대|Tempdb 백분율 로그가 사용 됨|없음|
|allocated_data_storage|할당 된 데이터 공간|바이트|평균|할당 된 데이터 공간|없음|
|database_allocated_data_storage|할당 된 데이터 공간|바이트|평균|할당 된 데이터 공간|DatabaseResourceId|
|allocated_data_storage_percent|할당 된 데이터 공간 비율|백분율|최대|할당 된 데이터 공간 비율|없음|

## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|dtu_consumption_percent|DTU 비율|백분율|평균|DTU 비율|ElasticPoolResourceId|
|database_dtu_consumption_percent|DTU 비율|백분율|평균|DTU 비율|DatabaseResourceId,ElasticPoolResourceId|
|storage_used|사용 되는 데이터 공간|바이트|평균|사용 되는 데이터 공간|ElasticPoolResourceId|
|database_storage_used|사용 되는 데이터 공간|바이트|평균|사용 되는 데이터 공간|DatabaseResourceId,ElasticPoolResourceId|
|dtu_used|DTU 사용됨|카운트|평균|DTU 사용됨|DatabaseResourceId|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|virtual_core_count|가상 코어 수|카운트|평균|가상 코어 수|없음|
|avg_cpu_percent|평균 CPU 비율|백분율|평균|평균 CPU 비율|없음|
|reserved_storage_mb|예약된 스토리지 공간|카운트|평균|예약된 스토리지 공간|없음|
|storage_space_used_mb|사용된 스토리지 공간|카운트|평균|사용된 스토리지 공간|없음|
|io_requests|IO 요청 수|카운트|평균|IO 요청 수|없음|
|io_bytes_read|읽은 IO 바이트|바이트|평균|읽은 IO 바이트|없음|
|io_bytes_written|기록된 IO 바이트|바이트|평균|기록된 IO 바이트|없음|



## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|UsedCapacity|사용된 용량|바이트|평균|계정 사용 용량|없음|
|트랜잭션|트랜잭션|카운트|합계|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|ResponseType, GeoType, ApiName, Authentication|
|수신|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|GeoType, ApiName, 인증|
|송신|송신|바이트|합계|송신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|GeoType, ApiName, 인증|
|SuccessServerLatency|성공 서버 대기 시간|밀리초|평균|Azure Storage에서 성공적인 요청을 처리하는 데 사용한 평균 대기 시간(밀리초)입니다. 이 값은 AverageE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|GeoType, ApiName, 인증|
|SuccessE2ELatency|성공 E2E 대기 시간|밀리초|평균|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 성공적인 요청의 평균 엔드투엔드 대기 시간(밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|GeoType, ApiName, 인증|
|가용성|가용성|백분율|평균|스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|GeoType, ApiName, 인증|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|BlobCapacity|Blob 용량|바이트|평균|스토리지 계정의 Blob service가 사용하는 스토리지의 양(바이트)입니다.|BlobType, 계층|
|BlobCount|Blob 수|카운트|평균|스토리지 계정의 Blob service에 있는 Blob 수입니다.|BlobType, 계층|
|ContainerCount|Blob 컨테이너 수|카운트|평균|스토리지 계정의 Blob service에 있는 컨테이너 수입니다.|없음|
|IndexCapacity|인덱스 용량|바이트|평균|ADLS Gen2 (계층적) 인덱스에서 사용 하는 저장소의 양 (바이트)입니다.|없음|
|트랜잭션|트랜잭션|카운트|합계|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|ResponseType, GeoType, ApiName, Authentication|
|수신|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|GeoType, ApiName, 인증|
|송신|송신|바이트|합계|송신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|GeoType, ApiName, 인증|
|SuccessServerLatency|성공 서버 대기 시간|밀리초|평균|Azure Storage에서 성공적인 요청을 처리하는 데 사용한 평균 대기 시간(밀리초)입니다. 이 값은 AverageE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|GeoType, ApiName, 인증|
|SuccessE2ELatency|성공 E2E 대기 시간|밀리초|평균|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 성공적인 요청의 평균 엔드투엔드 대기 시간(밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|GeoType, ApiName, 인증|
|가용성|가용성|백분율|평균|스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|GeoType, ApiName, 인증|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|TableCapacity|테이블 용량|바이트|평균|스토리지 계정의 Table service가 사용하는 스토리지의 양(바이트)입니다.|없음|
|TableCount|테이블 수|카운트|평균|스토리지 계정의 Table service에 있는 테이블 수입니다.|없음|
|TableEntityCount|테이블 엔터티 수|카운트|평균|스토리지 계정의 Table service에 있는 테이블 엔터티 수입니다.|없음|
|트랜잭션|트랜잭션|카운트|합계|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|ResponseType, GeoType, ApiName, Authentication|
|수신|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|GeoType, ApiName, 인증|
|송신|송신|바이트|합계|송신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|GeoType, ApiName, 인증|
|SuccessServerLatency|성공 서버 대기 시간|밀리초|평균|Azure Storage에서 성공적인 요청을 처리하는 데 사용한 평균 대기 시간(밀리초)입니다. 이 값은 AverageE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|GeoType, ApiName, 인증|
|SuccessE2ELatency|성공 E2E 대기 시간|밀리초|평균|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 성공적인 요청의 평균 엔드투엔드 대기 시간(밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|GeoType, ApiName, 인증|
|가용성|가용성|백분율|평균|스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|GeoType, ApiName, 인증|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|FileCapacity|파일 용량|바이트|평균|스토리지 계정의 파일 서비스가 사용하는 스토리지의 양(바이트)입니다.|FileShare|
|FileCount|파일 수|카운트|평균|스토리지 계정의 파일 서비스에 있는 파일 수입니다.|FileShare|
|FileShareCount|파일 공유 수|카운트|평균|스토리지 계정의 파일 서비스에 있는 파일 공유 수입니다.|없음|
|FileShareSnapshotCount|파일 공유 스냅숏 수|카운트|평균|저장소 계정의 파일 서비스에서 공유에 있는 스냅숏의 수입니다.|FileShare|
|FileShareSnapshotSize|파일 공유 스냅숏 크기|바이트|평균|저장소 계정의 파일 서비스에서 스냅숏에서 사용 되는 저장소의 크기 (바이트)입니다.|FileShare|
|FileShareQuota|파일 공유 할당량 크기|바이트|평균|Azure Files 서비스에서 사용할 수 있는 저장소 크기의 상한 (바이트)입니다.|FileShare|
|트랜잭션|트랜잭션|카운트|합계|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|ResponseType, GeoType, ApiName, Authentication, 파일 공유|
|수신|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|GeoType, ApiName, Authentication, 파일 공유|
|송신|송신|바이트|합계|송신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|GeoType, ApiName, Authentication, 파일 공유|
|SuccessServerLatency|성공 서버 대기 시간|밀리초|평균|Azure Storage에서 성공적인 요청을 처리하는 데 사용한 평균 대기 시간(밀리초)입니다. 이 값은 AverageE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|GeoType, ApiName, Authentication, 파일 공유|
|SuccessE2ELatency|성공 E2E 대기 시간|밀리초|평균|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 성공적인 요청의 평균 엔드투엔드 대기 시간(밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|GeoType, ApiName, Authentication, 파일 공유|
|가용성|가용성|백분율|평균|스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|GeoType, ApiName, Authentication, 파일 공유|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|QueueCapacity|큐 용량|바이트|평균|스토리지 계정의 큐 서비스가 사용하는 스토리지의 양(바이트)입니다.|없음|
|QueueCount|큐 수|카운트|평균|스토리지 계정의 큐 서비스에 있는 큐 수입니다.|없음|
|QueueMessageCount|큐 메시지 수|카운트|평균|스토리지 계정의 큐 서비스에 있는 대략적인 큐 메시지 수입니다.|없음|
|트랜잭션|트랜잭션|카운트|합계|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. 다른 종류의 응답 수에 ResponseType 차원을 사용합니다.|ResponseType, GeoType, ApiName, Authentication|
|수신|수신|바이트|합계|수신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다.|GeoType, ApiName, 인증|
|송신|송신|바이트|합계|송신 데이터 양(바이트)입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다.|GeoType, ApiName, 인증|
|SuccessServerLatency|성공 서버 대기 시간|밀리초|평균|Azure Storage에서 성공적인 요청을 처리하는 데 사용한 평균 대기 시간(밀리초)입니다. 이 값은 AverageE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다.|GeoType, ApiName, 인증|
|SuccessE2ELatency|성공 E2E 대기 시간|밀리초|평균|스토리지 서비스 또는 지정된 API 작업에 대해 제기된 성공적인 요청의 평균 엔드투엔드 대기 시간(밀리초)입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다.|GeoType, ApiName, 인증|
|가용성|가용성|백분율|평균|스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 TotalBillableRequests 값을 적용 가능한 요청 수로 나누어서 계산합니다(예기치 않은 오류를 발생시킨 요청 포함). 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다.|GeoType, ApiName, 인증|





## <a name="microsoftstoragecachecaches"></a>StorageCache/캐시

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|Cliops Ops|총 클라이언트 IOPS|카운트|평균|캐시에서 처리 한 클라이언트 파일 작업의 률입니다.|없음|
|ClientLatency|평균 클라이언트 대기 시간|밀리초|평균|저장소 캐시에 대 한 클라이언트 파일 작업의 평균 대기 시간입니다.|없음|
|ClientReadIOPS|클라이언트 읽기 IOPS|초당 개수|평균|초당 클라이언트 읽기 작업 수입니다.|없음|
|ClientReadThroughput|평균 캐시 읽기 처리량|초당 바이트 수|평균|클라이언트 읽기 데이터 전송 률입니다.|없음|
|ClientWriteIOPS|클라이언트 쓰기 IOPS|초당 개수|평균|초당 클라이언트 쓰기 작업 수입니다.|없음|
|ClientWriteThroughput|평균 캐시 쓰기 처리량|초당 바이트 수|평균|클라이언트 쓰기 데이터 전송 률입니다.|없음|
|ClientMetadataReadIOPS|클라이언트 메타 데이터 읽기 IOPS|초당 개수|평균|영구적 상태를 수정 하지 않는 데이터 읽기를 제외 하 고 캐시로 전송 된 클라이언트 파일 작업의 률입니다.|없음|
|ClientMetadataWriteIOPS|클라이언트 메타 데이터 쓰기 IOPS|초당 개수|평균|영구적 상태를 수정 하는 데이터 쓰기를 제외 하 고 캐시로 전송 된 클라이언트 파일 작업의 율입니다.|없음|
|ClientLockIOPS|클라이언트 잠금 IOPS|초당 개수|평균|초당 클라이언트 파일 잠금 작업 수입니다.|없음|
|StorageTargetHealth|저장소 대상 상태|카운트|평균|캐시와 저장소 대상 간의 연결 테스트에 대 한 부울 결과입니다.|없음|
|작동 시간|작동 시간|카운트|평균|캐시와 모니터링 시스템 간의 연결 테스트에 대 한 부울 결과입니다.|없음|
|StorageTargetIOPS|총 StorageTarget IOPS|카운트|평균|캐시가 특정 StorageTarget 보내는 모든 파일 작업의 수입니다.|StorageTarget|
|StorageTargetWriteIOPS|StorageTarget 쓰기 IOPS|카운트|평균|캐시가 특정 StorageTarget 전송 하는 파일 쓰기 작업의 수입니다.|StorageTarget|
|StorageTargetAsyncWriteThroughput|StorageTarget 비동기 쓰기 처리량|초당 바이트 수|평균|캐시에서 특정 StorageTarget에 비동기적으로 데이터를 쓰는 요율입니다. 클라이언트를 차단 하지 않는 oplocks입니다.|StorageTarget|
|StorageTargetSyncWriteThroughput|StorageTarget 동기 쓰기 처리량|초당 바이트 수|평균|캐시에서 특정 StorageTarget 데이터를 동기적으로 쓰는 율입니다. 클라이언트를 차단 하는 쓰기입니다.|StorageTarget|
|StorageTargetTotalWriteThroughput|StorageTarget 총 쓰기 처리량|초당 바이트 수|평균|캐시가 특정 StorageTarget 데이터를 기록 하는 총 비율입니다.|StorageTarget|
|StorageTargetLatency|StorageTarget 대기 시간|밀리초|평균|캐시가 partricular StorageTarget로 보내는 모든 파일 작업의 평균 왕복 대기 시간입니다.|StorageTarget|
|StorageTargetMetadataReadIOPS|StorageTarget Metadata 읽기 IOPS|초당 개수|평균|캐시에서 특정 StorageTarget 보내는 읽기 작업을 제외 하 고 영구 상태를 수정 하지 않는 파일 작업의 요율입니다.|StorageTarget|
|StorageTargetMetadataWriteIOPS|StorageTarget Metadata Write IOPS|초당 개수|평균|캐시에서 특정 StorageTarget 전송 하는 영구 상태를 수정 하 고 쓰기 작업을 제외 하는 파일 작업의 률입니다.|StorageTarget|
|StorageTargetReadIOPS|StorageTarget 읽기 IOPS|초당 개수|평균|캐시가 특정 StorageTarget 전송 하는 파일 읽기 작업의 률입니다.|StorageTarget|
|StorageTargetReadAheadThroughput|StorageTarget 미리 읽기 처리량|초당 바이트 수|평균|캐시 opportunisticly StorageTarget에서 데이터를 읽는 등급입니다.|StorageTarget|
|StorageTargetFillThroughput|StorageTarget 채우기 처리량|초당 바이트 수|평균|캐시가 StorageTarget에서 데이터를 읽어 캐시 누락을 처리 하는 속도로,|StorageTarget|
|StorageTargetTotalReadThroughput|StorageTarget 총 읽기 처리량|초당 바이트 수|평균|캐시가 특정 StorageTarget에서 데이터를 읽는 총 비율입니다.|StorageTarget|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|ServerSyncSessionResult|동기화 세션 결과|카운트|평균|서버 엔드포인트이 클라우드 엔드포인트과 동기화 세션을 성공적으로 완료할 때마다 1 값을 기록 하는 메트릭입니다.|SyncGroupName, ServerEndpointName, Syncgroupname|
|StorageSyncSyncSessionAppliedFilesCount|동기화 된 파일|카운트|합계|동기화 된 파일 수|SyncGroupName, ServerEndpointName, Syncgroupname|
|StorageSyncSyncSessionPerItemErrorsCount|동기화 상태가 아닌 파일|카운트|합계|동기화 하지 못한 파일 수|SyncGroupName, ServerEndpointName, Syncgroupname|
|StorageSyncBatchTransferredFileBytes|동기화되는 바이트 수|바이트|합계|동기화 세션에 대해 전송 된 총 파일 크기|SyncGroupName, ServerEndpointName, Syncgroupname|
|StorageSyncServerHeartbeat|서버 온라인 상태|카운트|최대|Resigtered 서버가 클라우드 엔드포인트과 하트 비트를 성공적으로 기록할 때마다 값 1을 기록 하는 메트릭입니다.|ServerName|
|StorageSyncRecallIOTotalSizeBytes|클라우드 계층화 회수|바이트|합계|서버에서 회수 한 데이터의 총 크기|ServerName|
|StorageSyncRecalledTotalNetworkBytes|클라우드 계층화 회수 크기|바이트|합계|회수되는 데이터 크기|SyncGroupName, ServerName|
|StorageSyncRecallThroughputBytesPerSecond|클라우드 계층화 회수 처리량|초당 바이트 수|평균|데이터 회수 처리량 크기|SyncGroupName, ServerName|
|StorageSyncRecalledNetworkBytesByApplication|응용 프로그램별 클라우드 계층화 회수 크기|바이트|합계|응용 프로그램에서 회수 한 데이터 크기|SyncGroupName, ServerName, ApplicationName|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>microsoft.storagesync/storageSyncServices/syncGroups

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|SyncGroupSyncSessionAppliedFilesCount|동기화 된 파일|카운트|합계|동기화 된 파일 수|SyncGroupName, ServerEndpointName, Syncgroupname|
|SyncGroupSyncSessionPerItemErrorsCount|동기화 상태가 아닌 파일|카운트|합계|동기화 하지 못한 파일 수|SyncGroupName, ServerEndpointName, Syncgroupname|
|SyncGroupBatchTransferredFileBytes|동기화되는 바이트 수|바이트|합계|동기화 세션에 대해 전송 된 총 파일 크기|SyncGroupName, ServerEndpointName, Syncgroupname|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|ServerEndpointSyncSessionAppliedFilesCount|동기화 된 파일|카운트|합계|동기화 된 파일 수|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|동기화 상태가 아닌 파일|카운트|합계|동기화 하지 못한 파일 수|ServerEndpointName, SyncDirection|
|ServerEndpointBatchTransferredFileBytes|동기화되는 바이트 수|바이트|합계|동기화 세션에 대해 전송 된 총 파일 크기|ServerEndpointName, SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>microsoft.storagesync/storageSyncServices/registeredServers

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|ServerHeartbeat 비트|서버 온라인 상태|카운트|최대|Resigtered 서버가 클라우드 엔드포인트과 하트 비트를 성공적으로 기록할 때마다 값 1을 기록 하는 메트릭입니다.|ServerResourceId, ServerName|
|ServerRecallIOTotalSizeBytes|클라우드 계층화 회수|바이트|합계|서버에서 회수 한 데이터의 총 크기|ServerResourceId, ServerName|



## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|ResourceUtilization|SU % 사용률|백분율|최대|SU % 사용률|LogicalName, PartitionId|
|InputEvents|입력 이벤트|카운트|합계|입력 이벤트|LogicalName, PartitionId|
|InputEventBytes|입력 이벤트 바이트|바이트|합계|입력 이벤트 바이트|LogicalName, PartitionId|
|LateInputEvents|늦은 입력 이벤트|카운트|합계|늦은 입력 이벤트|LogicalName, PartitionId|
|OutputEvents|출력 이벤트|카운트|합계|출력 이벤트|LogicalName, PartitionId|
|ConversionErrors|데이터 변환 오류|카운트|합계|데이터 변환 오류|LogicalName, PartitionId|
|오류|런타임 오류|카운트|합계|런타임 오류|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|잘못된 이벤트|카운트|합계|잘못된 이벤트|LogicalName, PartitionId|
|AMLCalloutRequests|기능 요청|카운트|합계|기능 요청|LogicalName, PartitionId|
|AMLCalloutFailedRequests|실패한 기능 요청|카운트|합계|실패한 기능 요청|LogicalName, PartitionId|
|AMLCalloutInputEvents|함수 이벤트|카운트|합계|함수 이벤트|LogicalName, PartitionId|
|DeserializationError|입력 역직렬화 오류|카운트|합계|입력 역직렬화 오류|LogicalName, PartitionId|
|EarlyInputEvents|초기 입력 이벤트|카운트|합계|초기 입력 이벤트|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|워터마크 지연|초|최대|워터마크 지연|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|백로그된 입력 이벤트|카운트|최대|백로그된 입력 이벤트|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|수신된 입력 원본|카운트|합계|수신된 입력 원본|LogicalName, PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|IngressReceivedMessages|수신된 메시지|카운트|합계|모든 이벤트 허브 또는 IoT 허브 이벤트 원본에서 읽은 메시지 수입니다.|없음|
|IngressReceivedInvalidMessages|수신된 잘못된 메시지|카운트|합계|모든 이벤트 허브 또는 IoT 허브 이벤트 원본에서 읽은 잘못된 메시지 수입니다.|없음|
|IngressReceivedBytes|수신된 바이트|바이트|합계|모든 이벤트 원본에서 읽은 바이트 수입니다.|없음|
|IngressStoredBytes|저장된 수신 바이트|바이트|합계|성공적으로 처리되어 쿼리에 사용할 수 있는 총 이벤트 크기|없음|
|IngressStoredEvents|저장된 수신 이벤트|카운트|합계|성공적으로 처리되어 쿼리에 사용할 수 있는 총 평면화된 이벤트 크기|없음|
|IngressReceivedMessagesTimeLag|수신된 메시지 시간 지연|초|최대|메시지가 이벤트 원본의 큐에 대기되는 시간과 수신 처리되는 시간 간의 차이입니다.|없음|
|IngressReceivedMessagesCountLag|수신된 메시지 수 지연|카운트|평균|이벤트 원본 파티션에서 마지막으로 큐에 넣은 메시지의 시퀀스 번호와 수신 중 처리 되는 메시지의 시퀀스 번호 간의 차이|없음|
|WarmStorageMaxProperties|웜 저장소 최대 속성|카운트|최대|S1/S2 SKU에 대해 환경에서 허용 하는 최대 속성 수 및 PAYG SKU에 대 한 웜 저장소에서 허용 하는 최대 속성 수|없음|
|WarmStorageUsedProperties|웜 저장소 사용 속성 |카운트|최대|S1/S2 SKU에 대해 환경에서 사용 하는 속성 수 및 PAYG SKU에 대 한 웜 저장소에서 사용 하는 속성 수|없음|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|IngressReceivedMessages|수신된 메시지|카운트|합계|이벤트 원본에서 읽은 메시지 수|없음|
|IngressReceivedInvalidMessages|수신된 잘못된 메시지|카운트|합계|이벤트 원본에서 읽은 잘못된 메시지 수|없음|
|IngressReceivedBytes|수신된 바이트|바이트|합계|이벤트 원본에서 읽은 바이트 수|없음|
|IngressStoredBytes|저장된 수신 바이트|바이트|합계|성공적으로 처리되어 쿼리에 사용할 수 있는 총 이벤트 크기|없음|
|IngressStoredEvents|저장된 수신 이벤트|카운트|합계|성공적으로 처리되어 쿼리에 사용할 수 있는 총 평면화된 이벤트 크기|없음|
|IngressReceivedMessagesTimeLag|수신된 메시지 시간 지연|초|최대|메시지가 이벤트 원본의 큐에 대기되는 시간과 수신 처리되는 시간 간의 차이입니다.|없음|
|IngressReceivedMessagesCountLag|수신된 메시지 수 지연|카운트|평균|이벤트 원본 파티션에서 마지막으로 큐에 넣은 메시지의 시퀀스 번호와 수신 중 처리 되는 메시지의 시퀀스 번호 간의 차이|없음|
|WarmStorageMaxProperties|웜 저장소 최대 속성|카운트|최대|S1/S2 SKU에 대해 환경에서 허용 하는 최대 속성 수 및 PAYG SKU에 대 한 웜 저장소에서 허용 하는 최대 속성 수|없음|
|WarmStorageUsedProperties|웜 저장소 사용 속성 |카운트|최대|S1/S2 SKU에 대해 환경에서 사용 하는 속성 수 및 PAYG SKU에 대 한 웜 저장소에서 사용 하는 속성 수|없음|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|디스크 읽기 바이트/초|초당 바이트 수|평균|샘플 기간에 대 한 읽기 작업으로 인 한 평균 디스크 처리량입니다.|없음|
|DiskWriteBytesPerSecond|디스크 쓰기 바이트/초|초당 바이트 수|평균|샘플 기간에 대 한 쓰기 작업으로 인 한 평균 디스크 처리량입니다.|없음|
|디스크 읽기 바이트|디스크 읽기 바이트|바이트|합계|샘플 기간 동안 읽기 작업으로 인 한 총 디스크 처리량입니다.|없음|
|디스크 쓰기 바이트|디스크 쓰기 바이트|바이트|합계|샘플 기간에 대 한 쓰기 작업으로 인 한 총 디스크 처리량입니다.|없음|
|DiskReadOperations|디스크 읽기 작업|카운트|합계|이전 샘플 기간의 IO 읽기 작업 수입니다. 이러한 작업에는 가변 크기를 사용할 수 있습니다.|없음|
|DiskWriteOperations|디스크 쓰기 작업|카운트|합계|이전 샘플 기간의 IO 쓰기 작업 수입니다. 이러한 작업에는 가변 크기를 사용할 수 있습니다.|없음|
|디스크 읽기 작업/초|디스크 읽기 작업/초|초당 개수|평균|이전 샘플 기간의 평균 IO 읽기 작업 수입니다. 이러한 작업에는 가변 크기를 사용할 수 있습니다.|없음|
|디스크 쓰기 작업/초|디스크 쓰기 작업/초|초당 개수|평균|이전 샘플 기간의 평균 IO 쓰기 작업 수입니다. 이러한 작업에는 가변 크기를 사용할 수 있습니다.|없음|
|DiskReadLatency|디스크 읽기 대기 시간|밀리초|평균|총 읽기 대기 시간입니다. 장치 및 커널 읽기 대기 시간의 합계입니다.|없음|
|DiskWriteLatency|디스크 쓰기 대기 시간|밀리초|평균|총 쓰기 대기 시간입니다. 장치 및 커널 쓰기 대기 시간의 합계입니다.|없음|
|NetworkInBytesPerSecond|네트워크 (바이트/초)|초당 바이트 수|평균|수신 되는 트래픽에 대 한 평균 네트워크 처리량입니다.|없음|
|NetworkOutBytesPerSecond|네트워크 출력 바이트/초|초당 바이트 수|평균|전송 된 트래픽에 대 한 평균 네트워크 처리량입니다.|없음|
|네트워크 인|네트워크 인|바이트|합계|수신 되는 트래픽에 대 한 총 네트워크 처리량입니다.|없음|
|네트워크 아웃|네트워크 아웃|바이트|합계|전송 된 트래픽에 대 한 총 네트워크 처리량입니다.|없음|
|MemoryUsed|사용 되는 메모리|바이트|평균|VM에서 사용 중인 컴퓨터 메모리의 양입니다.|없음|
|MemoryGranted 됨|부여 된 메모리|바이트|평균|호스트에서 VM에 부여 된 메모리의 양입니다. 메모리는 한 번에 도달할 때까지 호스트에 부여 되지 않으며, VMkernel에 메모리가 필요한 경우 부여 된 메모리를 교환 하거나 ballooned 수 있습니다.|없음|
|MemoryActive|메모리 활성|바이트|평균|지난 작은 시간 동안 VM에서 사용 하는 메모리의 양입니다. VM이 현재 필요로 하는 메모리의 양 ("true")입니다. 사용 되지 않는 추가 메모리는 게스트의 성능에 영향을 주지 않고 스왑할 수도 있고 ballooned 수 있습니다.|없음|
|백분율 CPU|백분율 CPU|백분율|평균|CPU 사용률입니다. 이 값은 시스템의 모든 프로세서 코어를 나타내는 100%로 보고 됩니다. 예를 들어 4 코어 시스템의 50%를 사용 하는 양방향 VM은 2 개의 코어를 완전히 사용 합니다.|없음|
|PercentageCpuReady|CPU 준비 비율|밀리초|합계|준비 시간은 CPU를 과거 업데이트 간격으로 사용할 수 있을 때까지 기다리는 데 걸리는 시간입니다.|없음|










## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|CpuPercentage|CPU 비율|백분율|평균|CPU 비율|인스턴스|
|MemoryPercentage|메모리 비율|백분율|평균|메모리 비율|인스턴스|
|DiskQueueLength|디스크 큐 길이|카운트|평균|디스크 큐 길이|인스턴스|
|HttpQueueLength|Http 큐 길이|카운트|평균|Http 큐 길이|인스턴스|
|BytesReceived|데이터 입력|바이트|합계|데이터 입력|인스턴스|
|BytesSent|데이터 출력|바이트|합계|데이터 출력|인스턴스|
|TcpSynSent|TCP Syn 전송|카운트|평균|TCP Syn 전송|인스턴스|
|TcpSynReceived|TCP Syn Received|카운트|평균|TCP Syn Received|인스턴스|
|TcpEstablished|TCP 설정|카운트|평균|TCP 설정|인스턴스|
|TcpFinWait1|TCP Fin 대기 1|카운트|평균|TCP Fin 대기 1|인스턴스|
|TcpFinWait2|TCP Fin 대기 2|카운트|평균|TCP Fin 대기 2|인스턴스|
|TcpClosing|TCP 닫기|카운트|평균|TCP 닫기|인스턴스|
|TcpCloseWait|TCP 닫기 대기|카운트|평균|TCP 닫기 대기|인스턴스|
|TcpLastAck|TCP 마지막 Ack|카운트|평균|TCP 마지막 Ack|인스턴스|
|TcpTimeWait|TCP 시간 대기|카운트|평균|TCP 시간 대기|인스턴스|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites(Functions 제외)

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|CpuTime|CPU 시간|초|합계|CPU 시간|인스턴스|
|요청|요청|카운트|합계|요청|인스턴스|
|BytesReceived|데이터 입력|바이트|합계|데이터 입력|인스턴스|
|BytesSent|데이터 출력|바이트|합계|데이터 출력|인스턴스|
|Http101|Http 101|카운트|합계|Http 101|인스턴스|
|Http2xx|Http 2xx|카운트|합계|Http 2xx|인스턴스|
|Http3xx|Http 3xx|카운트|합계|Http 3xx|인스턴스|
|Http401|Http 401|카운트|합계|Http 401|인스턴스|
|Http403|Http 403|카운트|합계|Http 403|인스턴스|
|Http404|Http 404|카운트|합계|Http 404|인스턴스|
|Http406|Http 406|카운트|합계|Http 406|인스턴스|
|Http4xx|Http 4xx|카운트|합계|Http 4xx|인스턴스|
|Http5xx|Http 서버 오류|카운트|합계|Http 서버 오류|인스턴스|
|MemoryWorkingSet|메모리 작업 집합|바이트|평균|메모리 작업 집합|인스턴스|
|AverageMemoryWorkingSet|평균 메모리 작업 집합|바이트|평균|평균 메모리 작업 집합|인스턴스|
|AverageResponseTime|평균 응답 시간|초|평균|평균 응답 시간|인스턴스|
|AppConnections|연결|카운트|평균|연결|인스턴스|
|핸들|핸들 개수|카운트|평균|핸들 개수|인스턴스|
|스레드|스레드 개수|카운트|평균|스레드 개수|인스턴스|
|PrivateBytes|프라이빗 바이트|바이트|평균|프라이빗 바이트|인스턴스|
|IoReadBytesPerSecond|초당 IO 읽기 바이트 수|초당 바이트 수|합계|초당 IO 읽기 바이트 수|인스턴스|
|IoWriteBytesPerSecond|초당 IO 쓰기 바이트 수|초당 바이트 수|합계|초당 IO 쓰기 바이트 수|인스턴스|
|IoOtherBytesPerSecond|초당 IO 기타 바이트 수|초당 바이트 수|합계|초당 IO 기타 바이트 수|인스턴스|
|IoReadOperationsPerSecond|초당 IO 읽기 작업 수|초당 바이트 수|합계|초당 IO 읽기 작업 수|인스턴스|
|IoWriteOperationsPerSecond|초당 IO 쓰기 작업 수|초당 바이트 수|합계|초당 IO 쓰기 작업 수|인스턴스|
|IoOtherOperationsPerSecond|초당 IO 기타 작업 수|초당 바이트 수|합계|초당 IO 기타 작업 수|인스턴스|
|RequestsInApplicationQueue|애플리케이션 큐의 요청 수|카운트|평균|애플리케이션 큐의 요청 수|인스턴스|
|CurrentAssemblies|현재 어셈블리|카운트|평균|현재 어셈블리|인스턴스|
|TotalAppDomains|총 앱 도메인|카운트|평균|총 앱 도메인|인스턴스|
|TotalAppDomainsUnloaded|언로드된 총 앱 도메인|카운트|평균|언로드된 총 앱 도메인|인스턴스|
|Gen0Collections|Gen 0 가비지 수집|카운트|합계|Gen 0 가비지 수집|인스턴스|
|Gen1Collections|Gen 1 가비지 수집|카운트|합계|Gen 1 가비지 수집|인스턴스|
|Gen2Collections|Gen 2 가비지 수집|카운트|합계|Gen 2 가비지 수집|인스턴스|
|HealthCheckStatus|상태 검사 상태|카운트|평균|상태 검사 상태|인스턴스|
|FileSystemUsage|파일 시스템 사용량|바이트|평균|파일 시스템 사용량|없음|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites(functions)

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|BytesReceived|데이터 입력|바이트|합계|데이터 입력|인스턴스|
|BytesSent|데이터 출력|바이트|합계|데이터 출력|인스턴스|
|Http5xx|Http 서버 오류|카운트|합계|Http 서버 오류|인스턴스|
|MemoryWorkingSet|메모리 작업 집합|바이트|평균|메모리 작업 집합|인스턴스|
|AverageMemoryWorkingSet|평균 메모리 작업 집합|바이트|평균|평균 메모리 작업 집합|인스턴스|
|FunctionExecutionUnits|함수 실행 단위|M b/밀리초|합계|[함수 실행 단위](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|인스턴스|
|FunctionExecutionCount|함수 실행 횟수|카운트|합계|함수 실행 횟수|인스턴스|
|PrivateBytes|프라이빗 바이트|바이트|평균|프라이빗 바이트|인스턴스|
|IoReadBytesPerSecond|초당 IO 읽기 바이트 수|초당 바이트 수|합계|초당 IO 읽기 바이트 수|인스턴스|
|IoWriteBytesPerSecond|초당 IO 쓰기 바이트 수|초당 바이트 수|합계|초당 IO 쓰기 바이트 수|인스턴스|
|IoOtherBytesPerSecond|초당 IO 기타 바이트 수|초당 바이트 수|합계|초당 IO 기타 바이트 수|인스턴스|
|IoReadOperationsPerSecond|초당 IO 읽기 작업 수|초당 바이트 수|합계|초당 IO 읽기 작업 수|인스턴스|
|IoWriteOperationsPerSecond|초당 IO 쓰기 작업 수|초당 바이트 수|합계|초당 IO 쓰기 작업 수|인스턴스|
|IoOtherOperationsPerSecond|초당 IO 기타 작업 수|초당 바이트 수|합계|초당 IO 기타 작업 수|인스턴스|
|RequestsInApplicationQueue|애플리케이션 큐의 요청 수|카운트|평균|애플리케이션 큐의 요청 수|인스턴스|
|CurrentAssemblies|현재 어셈블리|카운트|평균|현재 어셈블리|인스턴스|
|TotalAppDomains|총 앱 도메인|카운트|평균|총 앱 도메인|인스턴스|
|TotalAppDomainsUnloaded|언로드된 총 앱 도메인|카운트|평균|언로드된 총 앱 도메인|인스턴스|
|Gen0Collections|Gen 0 가비지 수집|카운트|합계|Gen 0 가비지 수집|인스턴스|
|Gen1Collections|Gen 1 가비지 수집|카운트|합계|Gen 1 가비지 수집|인스턴스|
|Gen2Collections|Gen 2 가비지 수집|카운트|합계|Gen 2 가비지 수집|인스턴스|
|HealthCheckStatus|상태 검사 상태|카운트|평균|상태 검사 상태|인스턴스|
|FileSystemUsage|파일 시스템 사용량|바이트|평균|파일 시스템 사용량|없음|


## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|CpuTime|CPU 시간|초|합계|CPU 시간|인스턴스|
|요청|요청|카운트|합계|요청|인스턴스|
|BytesReceived|데이터 입력|바이트|합계|데이터 입력|인스턴스|
|BytesSent|데이터 출력|바이트|합계|데이터 출력|인스턴스|
|Http101|Http 101|카운트|합계|Http 101|인스턴스|
|Http2xx|Http 2xx|카운트|합계|Http 2xx|인스턴스|
|Http3xx|Http 3xx|카운트|합계|Http 3xx|인스턴스|
|Http401|Http 401|카운트|합계|Http 401|인스턴스|
|Http403|Http 403|카운트|합계|Http 403|인스턴스|
|Http404|Http 404|카운트|합계|Http 404|인스턴스|
|Http406|Http 406|카운트|합계|Http 406|인스턴스|
|Http4xx|Http 4xx|카운트|합계|Http 4xx|인스턴스|
|Http5xx|Http 서버 오류|카운트|합계|Http 서버 오류|인스턴스|
|MemoryWorkingSet|메모리 작업 집합|바이트|평균|메모리 작업 집합|인스턴스|
|AverageMemoryWorkingSet|평균 메모리 작업 집합|바이트|평균|평균 메모리 작업 집합|인스턴스|
|AverageResponseTime|평균 응답 시간|초|평균|평균 응답 시간|인스턴스|
|HttpResponseTime|응답 시간|초|평균|응답 시간|인스턴스|
|FunctionExecutionUnits|함수 실행 단위|카운트|합계|함수 실행 단위|인스턴스|
|FunctionExecutionCount|함수 실행 횟수|카운트|합계|함수 실행 횟수|인스턴스|
|AppConnections|연결|카운트|평균|연결|인스턴스|
|핸들|핸들 개수|카운트|평균|핸들 개수|인스턴스|
|스레드|스레드 개수|카운트|평균|스레드 개수|인스턴스|
|PrivateBytes|프라이빗 바이트|바이트|평균|프라이빗 바이트|인스턴스|
|IoReadBytesPerSecond|초당 IO 읽기 바이트 수|초당 바이트 수|합계|초당 IO 읽기 바이트 수|인스턴스|
|IoWriteBytesPerSecond|초당 IO 쓰기 바이트 수|초당 바이트 수|합계|초당 IO 쓰기 바이트 수|인스턴스|
|IoOtherBytesPerSecond|초당 IO 기타 바이트 수|초당 바이트 수|합계|초당 IO 기타 바이트 수|인스턴스|
|IoReadOperationsPerSecond|초당 IO 읽기 작업 수|초당 바이트 수|합계|초당 IO 읽기 작업 수|인스턴스|
|IoWriteOperationsPerSecond|초당 IO 쓰기 작업 수|초당 바이트 수|합계|초당 IO 쓰기 작업 수|인스턴스|
|IoOtherOperationsPerSecond|초당 IO 기타 작업 수|초당 바이트 수|합계|초당 IO 기타 작업 수|인스턴스|
|RequestsInApplicationQueue|애플리케이션 큐의 요청 수|카운트|평균|애플리케이션 큐의 요청 수|인스턴스|
|CurrentAssemblies|현재 어셈블리|카운트|평균|현재 어셈블리|인스턴스|
|TotalAppDomains|총 앱 도메인|카운트|평균|총 앱 도메인|인스턴스|
|TotalAppDomainsUnloaded|언로드된 총 앱 도메인|카운트|평균|언로드된 총 앱 도메인|인스턴스|
|Gen0Collections|Gen 0 가비지 수집|카운트|합계|Gen 0 가비지 수집|인스턴스|
|Gen1Collections|Gen 1 가비지 수집|카운트|합계|Gen 1 가비지 수집|인스턴스|
|Gen2Collections|Gen 2 가비지 수집|카운트|합계|Gen 2 가비지 수집|인스턴스|
|HealthCheckStatus|상태 검사 상태|카운트|평균|상태 검사 상태|인스턴스|
|FileSystemUsage|파일 시스템 사용량|바이트|평균|파일 시스템 사용량|없음|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|요청|요청|카운트|합계|요청|인스턴스|
|BytesReceived|데이터 입력|바이트|합계|데이터 입력|인스턴스|
|BytesSent|데이터 출력|바이트|합계|데이터 출력|인스턴스|
|Http101|Http 101|카운트|합계|Http 101|인스턴스|
|Http2xx|Http 2xx|카운트|합계|Http 2xx|인스턴스|
|Http3xx|Http 3xx|카운트|합계|Http 3xx|인스턴스|
|Http401|Http 401|카운트|합계|Http 401|인스턴스|
|Http403|Http 403|카운트|합계|Http 403|인스턴스|
|Http404|Http 404|카운트|합계|Http 404|인스턴스|
|Http406|Http 406|카운트|합계|Http 406|인스턴스|
|Http4xx|Http 4xx|카운트|합계|Http 4xx|인스턴스|
|Http5xx|Http 서버 오류|카운트|합계|Http 서버 오류|인스턴스|
|AverageResponseTime|평균 응답 시간|초|평균|평균 응답 시간|인스턴스|
|CpuPercentage|CPU 비율|백분율|평균|CPU 비율|인스턴스|
|MemoryPercentage|메모리 비율|백분율|평균|메모리 비율|인스턴스|
|DiskQueueLength|디스크 큐 길이|카운트|평균|디스크 큐 길이|인스턴스|
|HttpQueueLength|Http 큐 길이|카운트|평균|Http 큐 길이|인스턴스|
|ActiveRequests|활성 요청|카운트|합계|활성 요청|인스턴스|
|TotalFrontEnds|총 프런트 엔드|카운트|평균|총 프런트 엔드|없음|
|SmallAppServicePlanInstances|소형 App Service 계획 작업자|카운트|평균|소형 App Service 계획 작업자|없음|
|MediumAppServicePlanInstances|중형 App Service 계획 작업자|카운트|평균|중형 App Service 계획 작업자|없음|
|LargeAppServicePlanInstances|대형 App Service 계획 작업자|카운트|평균|대형 App Service 계획 작업자|없음|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|메트릭|메트릭 표시 이름|단위|집계 형식|Description|차원|
|---|---|---|---|---|---|
|WorkersTotal|총 작업자|카운트|평균|총 작업자|없음|
|WorkersAvailable|사용 가능한 작업자|카운트|평균|사용 가능한 작업자|없음|
|WorkersUsed|사용된 작업자|카운트|평균|사용된 작업자|없음|
|CpuPercentage|CPU 비율|백분율|평균|CPU 비율|인스턴스|
|MemoryPercentage|메모리 비율|백분율|평균|메모리 비율|인스턴스|
## <a name="next-steps"></a>다음 단계
* [Azure Monitor의 메트릭에 대해 읽기](data-platform.md)
* [메트릭에 대한 경고 만들기](alerts-overview.md)
* [스토리지, 이벤트 허브 또는 Log Analytics에 메트릭 내보내기](platform-logs-overview.md)
