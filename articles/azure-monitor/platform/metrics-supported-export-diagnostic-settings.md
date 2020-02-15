---
title: 진단 설정을 통해 내보낼 수 있는 Azure Monitor 플랫폼 메트릭
description: Azure Monitor의 각 리소스 유형별로 사용 가능한 메트릭 목록.
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 02/10/2020
author: rboucher
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: fb11bf402ec671a46c191be0d8958c6a8a2c963d
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134955"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>진단 설정을 통해 내보낼 수 있는 Azure Monitor 플랫폼 메트릭

Azure Monitor는 기본적으로 구성 없이 [플랫폼 메트릭을](data-platform-metrics.md) 제공 합니다. 포털에서 차트를 작성 하거나, REST API를 통해 액세스 하거나, PowerShell 또는 CLI를 사용 하 여 쿼리 하는 등 플랫폼 메트릭과 상호 작용 하는 여러 가지 방법을 제공 합니다. Azure Monitor의 통합 된 메트릭 파이프라인에서 현재 사용할 수 있는 플랫폼 메트릭의 전체 목록은 [메트릭-지원](metrics-supported.md) 을 참조 하세요. 이러한 메트릭을 쿼리하고 액세스 하려면 [2018-01-01 api-version](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)을 사용 하세요. 레거시 API를 통해서 또는 포털에서 다른 메트릭을 제공할 수 있습니다.

다음 두 가지 방법 중 하나로 플랫폼 메트릭을 Azure monitor 파이프라인에서 다른 위치로 내보낼 수 있습니다.
1. [진단 설정을](diagnostic-settings.md) 사용 하 여 Log Analytics, Event Hubs 또는 Azure Storage으로 보냅니다.
2. 메트릭 사용 [REST API](https://docs.microsoft.com/rest/api/monitor/metrics/list)

Azure Monitor 백 엔드에서 복잡 하지 않기 때문에 모든 메트릭이 진단 설정을 사용 하 여 내보낼 수 있는 것은 아닙니다. 아래 표는 진단 설정을 사용 하 여 내보낼 수 있으며 내보낼 수 없는 목록입니다.

## <a name="change-to-behavior-for-nulls-and-zero-values"></a>Null 및 0 값에 대 한 동작 변경 
 
진단 설정을 통해 내보낼 수 있는 플랫폼 메트릭에는 Azure Monitor에서 ' 0 s '를 ' n u l l '로 해석할 수 있는 몇 가지 메트릭이 있습니다. 이로 인해 실제 ' 0 s ' (리소스에 의해 내보내지는)와 해석 된 ' 0 s ' (Null) 사이에서 혼란이 발생 했습니다. **2020 4 월 1** 일부 터 진단 설정을 통해 내보낸 플랫폼 메트릭은 기본 리소스에서 실제로 내보내지 않는 한 ' 0 s '를 더 이상 내보내지 않습니다. 참고로

1.  리소스 그룹 또는 특정 리소스를 삭제 하는 경우 영향을 받는 리소스의 메트릭 데이터는 더 이상 진단 설정 내보내기 대상으로 전송 되지 않습니다. 즉, Event Hubs, 저장소 계정 및 Log Analytics 작업 영역에 더 이상 표시 되지 않습니다.
2.  이러한 향상은 모든 공용 및 사설 클라우드에서 사용할 수 있습니다.
3.  이러한 변경은 다음 환경의 동작에 영향을 주지 않습니다. 
-   진단 설정을 통해 내보낸 플랫폼 리소스 로그
-   메트릭 탐색기의 메트릭 차트
-   플랫폼 메트릭에 대 한 경고
 
## <a name="metrics-exportable-table"></a>메트릭 내보내기 가능 테이블 

테이블에는 다음과 같은 열이 있습니다. 
- 진단 설정을 통해 내보낼 수 있나요? 
- ResourceType 
- 메트릭 
- MetricDisplayName
- 단위 
- AggregationType


> [!NOTE]
> 아래 표에는 아래쪽에 가로 스크롤 막대가 있을 수 있습니다. 누락 된 정보가 있다고 생각 되는 경우 스크롤 막대가 왼쪽에 표시 되는지 확인 합니다.  


진단 설정을 통해 내보낼 수 있나요? | ResourceType | 메트릭 | MetricDisplayName | 단위 | AggregationType
|----|-----|------|----|----|-----|
예 | Microsoft.AnalysisServices/servers | CleanerCurrentPrice | 메모리: 클리너 현재 가격 | 개수 | 평균
예 | Microsoft.AnalysisServices/servers | CleanerMemoryNonshrinkable | 메모리: 클리너 메모리 축소 불가능 | 바이트 | 평균
예 | Microsoft.AnalysisServices/servers | CleanerMemoryShrinkable | 메모리: 클리너 메모리 축소 가능 | 바이트 | 평균
예 | Microsoft.AnalysisServices/servers | CommandPoolBusyThreads | 스레드: 명령 풀 사용 중인 스레드 | 개수 | 평균
예 | Microsoft.AnalysisServices/servers | CommandPoolIdleThreads | 스레드: 명령 풀 유휴 상태 스레드 | 개수 | 평균
예 | Microsoft.AnalysisServices/servers | CommandPoolJobQueueLength | 명령 풀의 작업 큐 길이 | 개수 | 평균
예 | Microsoft.AnalysisServices/servers | CurrentConnections | 연결: 현재 연결 | 개수 | 평균
예 | Microsoft.AnalysisServices/servers | CurrentUserSessions | 현재 사용자 세션 수 | 개수 | 평균
예 | Microsoft.AnalysisServices/servers | LongParsingBusyThreads | 스레드: 긴 구문 분석 사용 중인 스레드 | 개수 | 평균
예 | Microsoft.AnalysisServices/servers | LongParsingIdleThreads | 스레드: 긴 구문 분석 유휴 상태 스레드 | 개수 | 평균
예 | Microsoft.AnalysisServices/servers | LongParsingJobQueueLength | 스레드: 긴 구문 분석 작업 큐 길이 | 개수 | 평균
예 | Microsoft.AnalysisServices/servers | mashup_engine_memory_metric | M 엔진 메모리 | 바이트 | 평균
예 | Microsoft.AnalysisServices/servers | mashup_engine_private_bytes_metric | M 엔진 전용 바이트 | 바이트 | 평균
예 | Microsoft.AnalysisServices/servers | mashup_engine_qpu_metric | M 엔진 QPU | 개수 | 평균
예 | Microsoft.AnalysisServices/servers | mashup_engine_virtual_bytes_metric | M 엔진 가상 바이트 | 바이트 | 평균
예 | Microsoft.AnalysisServices/servers | memory_metric | 메모리 | 바이트 | 평균
예 | Microsoft.AnalysisServices/servers | memory_thrashing_metric | 메모리 쓰래싱 | % | 평균
예 | Microsoft.AnalysisServices/servers | MemoryLimitHard | 메모리: 메모리 제한 하드 | 바이트 | 평균
예 | Microsoft.AnalysisServices/servers | MemoryLimitHigh | 메모리: 메모리 제한 상한 | 바이트 | 평균
예 | Microsoft.AnalysisServices/servers | MemoryLimitLow | 메모리: 메모리 제한 하한 | 바이트 | 평균
예 | Microsoft.AnalysisServices/servers | MemoryLimitVertiPaq | 메모리: 메모리 제한 VertiPaq | 바이트 | 평균
예 | Microsoft.AnalysisServices/servers | MemoryUsage | 메모리: 메모리 사용량 | 바이트 | 평균
예 | Microsoft.AnalysisServices/servers | private_bytes_metric | 전용 바이트 | 바이트 | 평균
예 | Microsoft.AnalysisServices/servers | ProcessingPoolBusyIOJobThreads | 스레드: 처리 풀 사용 중인 I/O 작업 스레드 | 개수 | 평균
예 | Microsoft.AnalysisServices/servers | ProcessingPoolBusyNonIOThreads | 스레드: 처리 풀 사용 중인 비-I/O 스레드 | 개수 | 평균
예 | Microsoft.AnalysisServices/servers | ProcessingPoolIdleIOJobThreads | 스레드: 처리 풀 유휴 상태 I/O 작업 스레드 | 개수 | 평균
예 | Microsoft.AnalysisServices/servers | ProcessingPoolIdleNonIOThreads | 스레드: 처리 풀 유휴 상태 비-I/O 스레드 | 개수 | 평균
예 | Microsoft.AnalysisServices/servers | ProcessingPoolIOJobQueueLength | 스레드: 처리 풀 I/O 작업 큐 길이 | 개수 | 평균
예 | Microsoft.AnalysisServices/servers | ProcessingPoolJobQueueLength | 처리 풀의 작업 큐 길이 | 개수 | 평균
예 | Microsoft.AnalysisServices/servers | qpu_metric | QPU | 개수 | 평균
예 | Microsoft.AnalysisServices/servers | QueryPoolBusyThreads | 쿼리 풀의 사용 중인 스레드 | 개수 | 평균
예 | Microsoft.AnalysisServices/servers | QueryPoolIdleThreads | 스레드: 쿼리 풀 유휴 상태 스레드 | 개수 | 평균
예 | Microsoft.AnalysisServices/servers | QueryPoolJobQueueLength | 스레드: 쿼리 풀 작업 큐 길이 | 개수 | 평균
예 | Microsoft.AnalysisServices/servers | 할당량 | 메모리: 할당량 | 바이트 | 평균
예 | Microsoft.AnalysisServices/servers | QuotaBlocked | 메모리: 차단된 할당량 | 개수 | 평균
예 | Microsoft.AnalysisServices/servers | RowsConvertedPerSec | 처리: 초당 변환된 행 | 초당 개수 | 평균
예 | Microsoft.AnalysisServices/servers | RowsReadPerSec | 처리: 초당 읽은 행 | 초당 개수 | 평균
예 | Microsoft.AnalysisServices/servers | RowsWrittenPerSec | 처리: 초당 작성된 행 | 초당 개수 | 평균
예 | Microsoft.AnalysisServices/servers | ShortParsingBusyThreads | 스레드: 짧은 구문 분석 사용 중인 스레드 | 개수 | 평균
예 | Microsoft.AnalysisServices/servers | ShortParsingIdleThreads | 스레드: 짧은 구문 분석 유휴 상태 스레드 | 개수 | 평균
예 | Microsoft.AnalysisServices/servers | ShortParsingJobQueueLength | 스레드: 짧은 구문 분석 작업 큐 길이 | 개수 | 평균
예 | Microsoft.AnalysisServices/servers | SuccessfullConnectionsPerSec | 초당 성공한 연결 수 | 초당 개수 | 평균
예 | Microsoft.AnalysisServices/servers | TotalConnectionFailures | 총 연결 실패 수 | 개수 | 평균
예 | Microsoft.AnalysisServices/servers | TotalConnectionRequests | 총 연결 요청 수 | 개수 | 평균
예 | Microsoft.AnalysisServices/servers | VertiPaqNonpaged | 메모리: 페이징되지 않은 VertiPaq | 바이트 | 평균
예 | Microsoft.AnalysisServices/servers | VertiPaqPaged | 메모리: 페이징된 VertiPaq | 바이트 | 평균
예 | Microsoft.AnalysisServices/servers | virtual_bytes_metric | Virtual Bytes | 바이트 | 평균
예 | Microsoft.ApiManagement/service | BackendDuration | 백 엔드 요청 기간 | 밀리초 | 평균
예 | Microsoft.ApiManagement/service | 용량 | 용량 | % | 평균
예 | Microsoft.ApiManagement/service | 지속 시간 | 게이트웨이 요청의 전체 기간 | 밀리초 | 평균
예 | Microsoft.ApiManagement/service | EventHubDroppedEvents | 삭제 된 EventHub 이벤트 | 개수 | 합계
예 | Microsoft.ApiManagement/service | EventHubRejectedEvents | 거부 된 EventHub 이벤트 | 개수 | 합계
예 | Microsoft.ApiManagement/service | EventHubSuccessfulEvents | 성공한 EventHub 이벤트 | 개수 | 합계
예 | Microsoft.ApiManagement/service | EventHubThrottledEvents | 제한 된 EventHub 이벤트 | 개수 | 합계
예 | Microsoft.ApiManagement/service | EventHubTimedoutEvents | 시간 초과 EventHub 이벤트 | 개수 | 합계
예 | Microsoft.ApiManagement/service | EventHubTotalBytesSent | EventHub 이벤트 크기 | 바이트 | 합계
예 | Microsoft.ApiManagement/service | EventHubTotalEvents | 총 EventHub 이벤트 | 개수 | 합계
예 | Microsoft.ApiManagement/service | EventHubTotalFailedEvents | 실패 한 EventHub 이벤트 | 개수 | 합계
예 | Microsoft.ApiManagement/service | FailedRequests | 실패 한 게이트웨이 요청 (사용 되지 않음) | 개수 | 합계
예 | Microsoft.ApiManagement/service | OtherRequests | 기타 게이트웨이 요청 (사용 되지 않음) | 개수 | 합계
예 | Microsoft.ApiManagement/service | 요청 | 요청 | 개수 | 합계
예 | Microsoft.ApiManagement/service | SuccessfulRequests | 성공적인 게이트웨이 요청 (사용 되지 않음) | 개수 | 합계
예 | Microsoft.ApiManagement/service | TotalRequests | 총 게이트웨이 요청 (사용 되지 않음) | 개수 | 합계
예 | Microsoft.ApiManagement/service | UnauthorizedRequests | 권한 없는 게이트웨이 요청 (사용 되지 않음) | 개수 | 합계
예 | Microsoft AppPlatform/스프링 | AppCpuUsagePercentage | 앱 CPU 사용량 비율 | % | 평균
예 | Microsoft AppPlatform/스프링 | AppMemoryCommitted | 할당 된 앱 메모리 | 바이트 | 평균
예 | Microsoft AppPlatform/스프링 | AppMemoryMax | 최대 앱 메모리 | 바이트 | 최대
예 | Microsoft AppPlatform/스프링 | AppMemoryUsed | 사용 되는 앱 메모리 | 바이트 | 평균
예 | Microsoft AppPlatform/스프링 | GCPauseTotalCount | GC 일시 중지 횟수 | 개수 | 합계
예 | Microsoft AppPlatform/스프링 | GCPauseTotalTime | GC 일시 중지 총 시간 | 밀리초 | 합계
예 | Microsoft AppPlatform/스프링 | MaxOldGenMemoryPoolBytes | 사용 가능한 최대 이전 생성 데이터 크기 | 바이트 | 평균
예 | Microsoft AppPlatform/스프링 | OldGenMemoryPoolBytes | 이전 세대 데이터 크기 | 바이트 | 평균
예 | Microsoft AppPlatform/스프링 | OldGenPromotedBytes | 이전 세대 데이터 크기로 수준 올리기 | 바이트 | 최대
예 | Microsoft AppPlatform/스프링 | SystemCpuUsagePercentage | 시스템 CPU 사용량 (%) | % | 평균
예 | Microsoft AppPlatform/스프링 | TomcatErrorCount | Tomcat 전역 오류 | 개수 | 합계
예 | Microsoft AppPlatform/스프링 | TomcatReceivedBytes | 총 수신 바이트 Tomcat | 바이트 | 합계
예 | Microsoft AppPlatform/스프링 | TomcatRequestMaxTime | Tomcat 요청 최대 시간 | 밀리초 | 최대
예 | Microsoft AppPlatform/스프링 | TomcatRequestTotalCount | Tomcat 요청 총 수 | 개수 | 합계
예 | Microsoft AppPlatform/스프링 | TomcatRequestTotalTime | Tomcat 요청 총 시간 | 밀리초 | 합계
예 | Microsoft AppPlatform/스프링 | TomcatResponseAvgTime | Tomcat 요청 평균 시간 | 밀리초 | 평균
예 | Microsoft AppPlatform/스프링 | TomcatSentBytes | 총 보낸 바이트 Tomcat | 바이트 | 합계
예 | Microsoft AppPlatform/스프링 | TomcatSessionActiveCurrentCount | Tomcat 세션 연결 수 | 개수 | 합계
예 | Microsoft AppPlatform/스프링 | TomcatSessionActiveMaxCount | Tomcat 세션 최대 활성 수 | 개수 | 합계
예 | Microsoft AppPlatform/스프링 | TomcatSessionAliveMaxTime | Tomcat 세션 최대 활성 시간 | 밀리초 | 최대
예 | Microsoft AppPlatform/스프링 | TomcatSessionCreatedCount | Tomcat 세션 생성 횟수 | 개수 | 합계
예 | Microsoft AppPlatform/스프링 | TomcatSessionExpiredCount | Tomcat 세션 만료 수 | 개수 | 합계
예 | Microsoft AppPlatform/스프링 | TomcatSessionRejectedCount | Tomcat 세션 거부 횟수 | 개수 | 합계
예 | Microsoft AppPlatform/스프링 | YoungGenPromotedBytes | 젊은 생성 데이터 크기로 수준 올리기 | 바이트 | 최대
예 | Microsoft.Automation/automationAccounts | TotalJob | 총 작업 수 | 개수 | 합계
예 | Microsoft.Automation/automationAccounts | TotalUpdateDeploymentMachineRuns | 총 업데이트 배포 컴퓨터 실행 | 개수 | 합계
예 | Microsoft.Automation/automationAccounts | TotalUpdateDeploymentRuns | 총 업데이트 배포 실행 | 개수 | 합계
아니요 | Microsoft.Batch/batchAccounts | CoreCount | 전용된 코어 수 | 개수 | 합계
아니요 | Microsoft.Batch/batchAccounts | CreatingNodeCount | 노드 수 만들기 | 개수 | 합계
아니요 | Microsoft.Batch/batchAccounts | IdleNodeCount | 유휴 상태인 노드 수 | 개수 | 합계
예 | Microsoft.Batch/batchAccounts | JobDeleteCompleteEvent | 작업 삭제 완료 이벤트 | 개수 | 합계
예 | Microsoft.Batch/batchAccounts | JobDeleteStartEvent | 작업 삭제 시작 이벤트 | 개수 | 합계
예 | Microsoft.Batch/batchAccounts | JobDisableCompleteEvent | 작업 비활성화 완료 이벤트 | 개수 | 합계
예 | Microsoft.Batch/batchAccounts | JobDisableStartEvent | 작업 비활성화 시작 이벤트 | 개수 | 합계
예 | Microsoft.Batch/batchAccounts | JobStartEvent | 작업 시작 이벤트 | 개수 | 합계
예 | Microsoft.Batch/batchAccounts | JobTerminateCompleteEvent | 작업 종료 완료 이벤트 | 개수 | 합계
예 | Microsoft.Batch/batchAccounts | JobTerminateStartEvent | 작업 종료 시작 이벤트 | 개수 | 합계
아니요 | Microsoft.Batch/batchAccounts | LeavingPoolNodeCount | 나가는 풀 노드 수 | 개수 | 합계
아니요 | Microsoft.Batch/batchAccounts | LowPriorityCoreCount | LowPriority 코어 수 | 개수 | 합계
아니요 | Microsoft.Batch/batchAccounts | OfflineNodeCount | 오프라인 노드 수 | 개수 | 합계
예 | Microsoft.Batch/batchAccounts | PoolCreateEvent | 풀 만들기 이벤트 | 개수 | 합계
예 | Microsoft.Batch/batchAccounts | PoolDeleteCompleteEvent | 풀 삭제 완료 이벤트 | 개수 | 합계
예 | Microsoft.Batch/batchAccounts | PoolDeleteStartEvent | 풀 삭제 시작 이벤트 | 개수 | 합계
예 | Microsoft.Batch/batchAccounts | PoolResizeCompleteEvent | 풀 크기 조정 완료 이벤트 | 개수 | 합계
예 | Microsoft.Batch/batchAccounts | PoolResizeStartEvent | 풀 크기 조정 시작 이벤트 | 개수 | 합계
아니요 | Microsoft.Batch/batchAccounts | PreemptedNodeCount | 선점된 노드 수 | 개수 | 합계
아니요 | Microsoft.Batch/batchAccounts | RebootingNodeCount | 재부팅 노드 수 | 개수 | 합계
아니요 | Microsoft.Batch/batchAccounts | ReimagingNodeCount | 이미지로 다시 설치하는 노드 수 | 개수 | 합계
아니요 | Microsoft.Batch/batchAccounts | RunningNodeCount | 실행 노드 수 | 개수 | 합계
아니요 | Microsoft.Batch/batchAccounts | StartingNodeCount | 시작 노드 수 | 개수 | 합계
아니요 | Microsoft.Batch/batchAccounts | StartTaskFailedNodeCount | 시작 작업 실패 노드 수 | 개수 | 합계
예 | Microsoft.Batch/batchAccounts | TaskCompleteEvent | 작업 완료 이벤트 | 개수 | 합계
예 | Microsoft.Batch/batchAccounts | TaskFailEvent | 작업 실패 이벤트 | 개수 | 합계
예 | Microsoft.Batch/batchAccounts | TaskStartEvent | 작업 시작 이벤트 | 개수 | 합계
아니요 | Microsoft.Batch/batchAccounts | TotalLowPriorityNodeCount | 우선 순위가 낮은 노드 수 | 개수 | 합계
아니요 | Microsoft.Batch/batchAccounts | TotalNodeCount | 전용된 노드 수 | 개수 | 합계
아니요 | Microsoft.Batch/batchAccounts | UnusableNodeCount | 사용 불가 노드 수 | 개수 | 합계
아니요 | Microsoft.Batch/batchAccounts | WaitingForStartTaskNodeCount | 작업 시작 대기 노드 수 | 개수 | 합계
예 | Microsoft BatchAI/workspace | 활성 코어 | 활성 코어 | 개수 | 평균
예 | Microsoft BatchAI/workspace | 활성 노드 | 활성 노드 | 개수 | 평균
예 | Microsoft BatchAI/workspace | 유휴 코어 | 유휴 코어 | 개수 | 평균
예 | Microsoft BatchAI/workspace | 유휴 노드 | 유휴 노드 | 개수 | 평균
예 | Microsoft BatchAI/workspace | 작업 완료 됨 | 작업 완료 됨 | 개수 | 합계
예 | Microsoft BatchAI/workspace | 제출 된 작업 | 제출 된 작업 | 개수 | 합계
예 | Microsoft BatchAI/workspace | 코어 탈퇴 | 코어 탈퇴 | 개수 | 평균
예 | Microsoft BatchAI/workspace | 노드 종료 | 노드 종료 | 개수 | 평균
예 | Microsoft BatchAI/workspace | 선점 코어 | 선점 코어 | 개수 | 평균
예 | Microsoft BatchAI/workspace | 선점 노드 | 선점 노드 | 개수 | 평균
예 | Microsoft BatchAI/workspace | 할당량 사용률 비율 | 할당량 사용률 비율 | 개수 | 평균
예 | Microsoft BatchAI/workspace | 총 코어 | 총 코어 | 개수 | 평균
예 | Microsoft BatchAI/workspace | 총 노드 | 총 노드 | 개수 | 평균
예 | Microsoft BatchAI/workspace | 사용할 수 없는 코어 | 사용할 수 없는 코어 | 개수 | 평균
예 | Microsoft BatchAI/workspace | 사용할 수 없는 노드 | 사용할 수 없는 노드 | 개수 | 평균
예 | Microsoft.Blockchain/blockchainMembers | ConnectionAccepted 됨 | 수락 된 연결 | 개수 | 합계
예 | Microsoft.Blockchain/blockchainMembers | ConnectionActive | 활성 연결 수 | 개수 | 평균
예 | Microsoft.Blockchain/blockchainMembers | ConnectionHandled | 처리 한 연결 | 개수 | 합계
예 | Microsoft.Blockchain/blockchainMembers | CpuUsagePercentageInDouble | CPU 사용량 비율 | % | 최대
예 | Microsoft.Blockchain/blockchainMembers | IOReadBytes | IO 읽기 바이트 | 바이트 | 합계
예 | Microsoft.Blockchain/blockchainMembers | IOWriteBytes | IO 쓰기 바이트 | 바이트 | 합계
예 | Microsoft.Blockchain/blockchainMembers | MemoryLimit | 메모리 제한 | 바이트 | 평균
예 | Microsoft.Blockchain/blockchainMembers | MemoryUsage | 메모리 사용량 | 바이트 | 평균
예 | Microsoft.Blockchain/blockchainMembers | MemoryUsagePercentageInDouble | 메모리 사용 비율 | % | 평균
예 | Microsoft.Blockchain/blockchainMembers | PendingTransactions | 보류 중인 트랜잭션 | 개수 | 평균
예 | Microsoft.Blockchain/blockchainMembers | ProcessedBlocks | 처리 된 블록 | 개수 | 합계
예 | Microsoft.Blockchain/blockchainMembers | ProcessedTransactions | 처리 된 트랜잭션 | 개수 | 합계
예 | Microsoft.Blockchain/blockchainMembers | QueuedTransactions | 대기 중인 트랜잭션 | 개수 | 평균
예 | Microsoft.Blockchain/blockchainMembers | RequestHandled | 처리 한 요청 | 개수 | 합계
예 | Microsoft.Blockchain/blockchainMembers | StorageUsage | 저장소 사용량 | 바이트 | 평균
예 | Microsoft.Cache/redis | cachehits | 캐시 적중 | 개수 | 합계
예 | Microsoft.Cache/redis | cachehits0 | 캐시 적중(분할 0) | 개수 | 합계
예 | Microsoft.Cache/redis | cachehits1 | 캐시 적중(분할 1) | 개수 | 합계
예 | Microsoft.Cache/redis | cachehits2 | 캐시 적중(분할 2) | 개수 | 합계
예 | Microsoft.Cache/redis | cachehits3 | 캐시 적중(분할 3) | 개수 | 합계
예 | Microsoft.Cache/redis | cachehits4 | 캐시 적중(분할 4) | 개수 | 합계
예 | Microsoft.Cache/redis | cachehits5 | 캐시 적중(분할 5) | 개수 | 합계
예 | Microsoft.Cache/redis | cachehits6 | 캐시 적중(분할 6) | 개수 | 합계
예 | Microsoft.Cache/redis | cachehits7 | 캐시 적중(분할 7) | 개수 | 합계
예 | Microsoft.Cache/redis | cachehits8 | 캐시 적중(분할 8) | 개수 | 합계
예 | Microsoft.Cache/redis | cachehits9 | 캐시 적중(분할 9) | 개수 | 합계
예 | Microsoft.Cache/redis | cacheLatency | 캐시 대기 시간 마이크로초(미리 보기) | 개수 | 평균
예 | Microsoft.Cache/redis | cachemisses | 캐시 누락 | 개수 | 합계
예 | Microsoft.Cache/redis | cachemisses0 | 캐시 누락(분할 0) | 개수 | 합계
예 | Microsoft.Cache/redis | cachemisses1 | 캐시 누락(분할 1) | 개수 | 합계
예 | Microsoft.Cache/redis | cachemisses2 | 캐시 누락(분할 2) | 개수 | 합계
예 | Microsoft.Cache/redis | cachemisses3 | 캐시 누락(분할 3) | 개수 | 합계
예 | Microsoft.Cache/redis | cachemisses4 | 캐시 누락(분할 4) | 개수 | 합계
예 | Microsoft.Cache/redis | cachemisses5 | 캐시 누락(분할 5) | 개수 | 합계
예 | Microsoft.Cache/redis | cachemisses6 | 캐시 누락(분할 6) | 개수 | 합계
예 | Microsoft.Cache/redis | cachemisses7 | 캐시 누락(분할 7) | 개수 | 합계
예 | Microsoft.Cache/redis | cachemisses8 | 캐시 누락(분할 8) | 개수 | 합계
예 | Microsoft.Cache/redis | cachemisses9 | 캐시 누락(분할 9) | 개수 | 합계
예 | Microsoft.Cache/redis | cacheRead | 캐시 읽기 | 초당 바이트 수 | 최대
예 | Microsoft.Cache/redis | cacheRead0 | 캐시 읽기(분할 0) | 초당 바이트 수 | 최대
예 | Microsoft.Cache/redis | cacheRead1 | 캐시 읽기(분할 1) | 초당 바이트 수 | 최대
예 | Microsoft.Cache/redis | cacheRead2 | 캐시 읽기(분할 2) | 초당 바이트 수 | 최대
예 | Microsoft.Cache/redis | cacheRead3 | 캐시 읽기(분할 3) | 초당 바이트 수 | 최대
예 | Microsoft.Cache/redis | cacheRead4 | 캐시 읽기(분할 4) | 초당 바이트 수 | 최대
예 | Microsoft.Cache/redis | cacheRead5 | 캐시 읽기(분할 5) | 초당 바이트 수 | 최대
예 | Microsoft.Cache/redis | cacheRead6 | 캐시 읽기(분할 6) | 초당 바이트 수 | 최대
예 | Microsoft.Cache/redis | cacheRead7 | 캐시 읽기(분할 7) | 초당 바이트 수 | 최대
예 | Microsoft.Cache/redis | cacheRead8 | 캐시 읽기(분할 8) | 초당 바이트 수 | 최대
예 | Microsoft.Cache/redis | cacheRead9 | 캐시 읽기(분할 9) | 초당 바이트 수 | 최대
예 | Microsoft.Cache/redis | cacheWrite | 캐시 쓰기 | 초당 바이트 수 | 최대
예 | Microsoft.Cache/redis | cacheWrite0 | 캐시 쓰기(분할 0) | 초당 바이트 수 | 최대
예 | Microsoft.Cache/redis | cacheWrite1 | 캐시 쓰기(분할 1) | 초당 바이트 수 | 최대
예 | Microsoft.Cache/redis | cacheWrite2 | 캐시 쓰기(분할 2) | 초당 바이트 수 | 최대
예 | Microsoft.Cache/redis | cacheWrite3 | 캐시 쓰기(분할 3) | 초당 바이트 수 | 최대
예 | Microsoft.Cache/redis | cacheWrite4 | 캐시 쓰기(분할 4) | 초당 바이트 수 | 최대
예 | Microsoft.Cache/redis | cacheWrite5 | 캐시 쓰기(분할 5) | 초당 바이트 수 | 최대
예 | Microsoft.Cache/redis | cacheWrite6 | 캐시 쓰기(분할 6) | 초당 바이트 수 | 최대
예 | Microsoft.Cache/redis | cacheWrite7 | 캐시 쓰기(분할 7) | 초당 바이트 수 | 최대
예 | Microsoft.Cache/redis | cacheWrite8 | 캐시 쓰기(분할 8) | 초당 바이트 수 | 최대
예 | Microsoft.Cache/redis | cacheWrite9 | 캐시 쓰기(분할 9) | 초당 바이트 수 | 최대
예 | Microsoft.Cache/redis | connectedclients | Connected Clients | 개수 | 최대
예 | Microsoft.Cache/redis | connectedclients0 | 연결된 클라이언트(분할 0) | 개수 | 최대
예 | Microsoft.Cache/redis | connectedclients1 | 연결된 클라이언트(분할 1) | 개수 | 최대
예 | Microsoft.Cache/redis | connectedclients2 | 연결된 클라이언트(분할 2) | 개수 | 최대
예 | Microsoft.Cache/redis | connectedclients3 | 연결된 클라이언트(분할 3) | 개수 | 최대
예 | Microsoft.Cache/redis | connectedclients4 | 연결된 클라이언트(분할 4) | 개수 | 최대
예 | Microsoft.Cache/redis | connectedclients5 | 연결된 클라이언트(분할 5) | 개수 | 최대
예 | Microsoft.Cache/redis | connectedclients6 | 연결된 클라이언트(분할 6) | 개수 | 최대
예 | Microsoft.Cache/redis | connectedclients7 | 연결된 클라이언트(분할 7) | 개수 | 최대
예 | Microsoft.Cache/redis | connectedclients8 | 연결된 클라이언트(분할 8) | 개수 | 최대
예 | Microsoft.Cache/redis | connectedclients9 | 연결된 클라이언트(분할 9) | 개수 | 최대
예 | Microsoft.Cache/redis | 오류 | 오류 | 개수 | 최대
예 | Microsoft.Cache/redis | evictedkeys | 제거된 키 | 개수 | 합계
예 | Microsoft.Cache/redis | evictedkeys0 | 제거된 키(분할 0) | 개수 | 합계
예 | Microsoft.Cache/redis | evictedkeys1 | 제거된 키(분할 1) | 개수 | 합계
예 | Microsoft.Cache/redis | evictedkeys2 | 제거된 키(분할 2) | 개수 | 합계
예 | Microsoft.Cache/redis | evictedkeys3 | 제거된 키(분할 3) | 개수 | 합계
예 | Microsoft.Cache/redis | evictedkeys4 | 제거된 키(분할 4) | 개수 | 합계
예 | Microsoft.Cache/redis | evictedkeys5 | 제거된 키(분할 5) | 개수 | 합계
예 | Microsoft.Cache/redis | evictedkeys6 | 제거된 키(분할 6) | 개수 | 합계
예 | Microsoft.Cache/redis | evictedkeys7 | 제거된 키(분할 7) | 개수 | 합계
예 | Microsoft.Cache/redis | evictedkeys8 | 제거된 키(분할 8) | 개수 | 합계
예 | Microsoft.Cache/redis | evictedkeys9 | 제거된 키(분할 9) | 개수 | 합계
예 | Microsoft.Cache/redis | expiredkeys | 만료된 키 | 개수 | 합계
예 | Microsoft.Cache/redis | expiredkeys0 | 만료된 키(분할 0) | 개수 | 합계
예 | Microsoft.Cache/redis | expiredkeys1 | 만료된 키(분할 1) | 개수 | 합계
예 | Microsoft.Cache/redis | expiredkeys2 | 만료된 키(분할 2) | 개수 | 합계
예 | Microsoft.Cache/redis | expiredkeys3 | 만료된 키(분할 3) | 개수 | 합계
예 | Microsoft.Cache/redis | expiredkeys4 | 만료된 키(분할 4) | 개수 | 합계
예 | Microsoft.Cache/redis | expiredkeys5 | 만료된 키(분할 5) | 개수 | 합계
예 | Microsoft.Cache/redis | expiredkeys6 | 만료된 키(분할 6) | 개수 | 합계
예 | Microsoft.Cache/redis | expiredkeys7 | 만료된 키(분할 7) | 개수 | 합계
예 | Microsoft.Cache/redis | expiredkeys8 | 만료된 키(분할 8) | 개수 | 합계
예 | Microsoft.Cache/redis | expiredkeys9 | 만료된 키(분할 9) | 개수 | 합계
예 | Microsoft.Cache/redis | getcommands | 가져옵니다. | 개수 | 합계
예 | Microsoft.Cache/redis | getcommands0 | 가져오기(분할 0) | 개수 | 합계
예 | Microsoft.Cache/redis | getcommands1 | 가져오기(분할 1) | 개수 | 합계
예 | Microsoft.Cache/redis | getcommands2 | 가져오기(분할 2) | 개수 | 합계
예 | Microsoft.Cache/redis | getcommands3 | 가져오기(분할 3) | 개수 | 합계
예 | Microsoft.Cache/redis | getcommands4 | 가져오기(분할 4) | 개수 | 합계
예 | Microsoft.Cache/redis | getcommands5 | 가져오기(분할 5) | 개수 | 합계
예 | Microsoft.Cache/redis | getcommands6 | 가져오기(분할 6) | 개수 | 합계
예 | Microsoft.Cache/redis | getcommands7 | 가져오기(분할 7) | 개수 | 합계
예 | Microsoft.Cache/redis | getcommands8 | 가져오기(분할 8) | 개수 | 합계
예 | Microsoft.Cache/redis | getcommands9 | 가져오기(분할 9) | 개수 | 합계
예 | Microsoft.Cache/redis | operationsPerSecond | 초당 작업 | 개수 | 최대
예 | Microsoft.Cache/redis | operationsPerSecond0 | 초당 작업(분할 0) | 개수 | 최대
예 | Microsoft.Cache/redis | operationsPerSecond1 | 초당 작업(분할 1) | 개수 | 최대
예 | Microsoft.Cache/redis | operationsPerSecond2 | 초당 작업(분할 2) | 개수 | 최대
예 | Microsoft.Cache/redis | operationsPerSecond3 | 초당 작업(분할 3) | 개수 | 최대
예 | Microsoft.Cache/redis | operationsPerSecond4 | 초당 작업(분할 4) | 개수 | 최대
예 | Microsoft.Cache/redis | operationsPerSecond5 | 초당 작업(분할 5) | 개수 | 최대
예 | Microsoft.Cache/redis | operationsPerSecond6 | 초당 작업(분할 6) | 개수 | 최대
예 | Microsoft.Cache/redis | operationsPerSecond7 | 초당 작업(분할 7) | 개수 | 최대
예 | Microsoft.Cache/redis | operationsPerSecond8 | 초당 작업(분할 8) | 개수 | 최대
예 | Microsoft.Cache/redis | operationsPerSecond9 | 초당 작업(분할 9) | 개수 | 최대
예 | Microsoft.Cache/redis | percentProcessorTime | CPU | % | 최대
예 | Microsoft.Cache/redis | percentProcessorTime0 | CPU(분할 0) | % | 최대
예 | Microsoft.Cache/redis | percentProcessorTime1 | CPU(분할 1) | % | 최대
예 | Microsoft.Cache/redis | percentProcessorTime2 | CPU(분할 2) | % | 최대
예 | Microsoft.Cache/redis | percentProcessorTime3 | CPU(분할 3) | % | 최대
예 | Microsoft.Cache/redis | percentProcessorTime4 | CPU(분할 4) | % | 최대
예 | Microsoft.Cache/redis | percentProcessorTime5 | CPU(분할 5) | % | 최대
예 | Microsoft.Cache/redis | percentProcessorTime6 | CPU(분할 6) | % | 최대
예 | Microsoft.Cache/redis | percentProcessorTime7 | CPU(분할 7) | % | 최대
예 | Microsoft.Cache/redis | percentProcessorTime8 | CPU(분할 8) | % | 최대
예 | Microsoft.Cache/redis | percentProcessorTime9 | CPU(분할 9) | % | 최대
예 | Microsoft.Cache/redis | serverLoad | 서버 부하 | % | 최대
예 | Microsoft.Cache/redis | serverLoad0 | 서버 부하(분할 0) | % | 최대
예 | Microsoft.Cache/redis | serverLoad1 | 서버 부하(분할 1) | % | 최대
예 | Microsoft.Cache/redis | serverLoad2 | 서버 부하(분할 2) | % | 최대
예 | Microsoft.Cache/redis | serverLoad3 | 서버 부하(분할 3) | % | 최대
예 | Microsoft.Cache/redis | serverLoad4 | 서버 부하(분할 4) | % | 최대
예 | Microsoft.Cache/redis | serverLoad5 | 서버 부하(분할 5) | % | 최대
예 | Microsoft.Cache/redis | serverLoad6 | 서버 부하(분할 6) | % | 최대
예 | Microsoft.Cache/redis | serverLoad7 | 서버 부하(분할 7) | % | 최대
예 | Microsoft.Cache/redis | serverLoad8 | 서버 부하(분할 8) | % | 최대
예 | Microsoft.Cache/redis | serverLoad9 | 서버 부하(분할 9) | % | 최대
예 | Microsoft.Cache/redis | setcommands | 집합 | 개수 | 합계
예 | Microsoft.Cache/redis | setcommands0 | 설정(분할 0) | 개수 | 합계
예 | Microsoft.Cache/redis | setcommands1 | 집합(분할 1) | 개수 | 합계
예 | Microsoft.Cache/redis | setcommands2 | 설정(분할 2) | 개수 | 합계
예 | Microsoft.Cache/redis | setcommands3 | 설정(분할 3) | 개수 | 합계
예 | Microsoft.Cache/redis | setcommands4 | 설정(분할 4) | 개수 | 합계
예 | Microsoft.Cache/redis | setcommands5 | 설정(분할 5) | 개수 | 합계
예 | Microsoft.Cache/redis | setcommands6 | 설정(분할 6) | 개수 | 합계
예 | Microsoft.Cache/redis | setcommands7 | 설정(분할 7) | 개수 | 합계
예 | Microsoft.Cache/redis | setcommands8 | 설정(분할 8) | 개수 | 합계
예 | Microsoft.Cache/redis | setcommands9 | 설정(분할 9) | 개수 | 합계
예 | Microsoft.Cache/redis | totalcommandsprocessed | 총 작업 | 개수 | 합계
예 | Microsoft.Cache/redis | totalcommandsprocessed0 | 총 작업(분할 0) | 개수 | 합계
예 | Microsoft.Cache/redis | totalcommandsprocessed1 | 총 작업(분할 1) | 개수 | 합계
예 | Microsoft.Cache/redis | totalcommandsprocessed2 | 총 작업(분할 2) | 개수 | 합계
예 | Microsoft.Cache/redis | totalcommandsprocessed3 | 총 작업(분할 3) | 개수 | 합계
예 | Microsoft.Cache/redis | totalcommandsprocessed4 | 총 작업(분할 4) | 개수 | 합계
예 | Microsoft.Cache/redis | totalcommandsprocessed5 | 총 작업(분할 5) | 개수 | 합계
예 | Microsoft.Cache/redis | totalcommandsprocessed6 | 총 작업(분할 6) | 개수 | 합계
예 | Microsoft.Cache/redis | totalcommandsprocessed7 | 총 작업(분할 7) | 개수 | 합계
예 | Microsoft.Cache/redis | totalcommandsprocessed8 | 총 작업(분할 8) | 개수 | 합계
예 | Microsoft.Cache/redis | totalcommandsprocessed9 | 총 작업(분할 9) | 개수 | 합계
예 | Microsoft.Cache/redis | totalkeys | 전체 키 | 개수 | 최대
예 | Microsoft.Cache/redis | totalkeys0 | 총 키(분할 0) | 개수 | 최대
예 | Microsoft.Cache/redis | totalkeys1 | 총 키(분할 1) | 개수 | 최대
예 | Microsoft.Cache/redis | totalkeys2 | 총 키(분할 2) | 개수 | 최대
예 | Microsoft.Cache/redis | totalkeys3 | 총 키(분할 3) | 개수 | 최대
예 | Microsoft.Cache/redis | totalkeys4 | 총 키(분할 4) | 개수 | 최대
예 | Microsoft.Cache/redis | totalkeys5 | 총 키(분할 5) | 개수 | 최대
예 | Microsoft.Cache/redis | totalkeys6 | 총 키(분할 6) | 개수 | 최대
예 | Microsoft.Cache/redis | totalkeys7 | 총 키(분할 7) | 개수 | 최대
예 | Microsoft.Cache/redis | totalkeys8 | 총 키(분할 8) | 개수 | 최대
예 | Microsoft.Cache/redis | totalkeys9 | 총 키(분할 9) | 개수 | 최대
예 | Microsoft.Cache/redis | usedmemory | Used Memory | 바이트 | 최대
예 | Microsoft.Cache/redis | usedmemory0 | 사용된 메모리(분할 0) | 바이트 | 최대
예 | Microsoft.Cache/redis | usedmemory1 | 사용된 메모리(분할 1) | 바이트 | 최대
예 | Microsoft.Cache/redis | usedmemory2 | 사용된 메모리(분할 2) | 바이트 | 최대
예 | Microsoft.Cache/redis | usedmemory3 | 사용된 메모리(분할 3) | 바이트 | 최대
예 | Microsoft.Cache/redis | usedmemory4 | 사용된 메모리(분할 4) | 바이트 | 최대
예 | Microsoft.Cache/redis | usedmemory5 | 사용된 메모리(분할 5) | 바이트 | 최대
예 | Microsoft.Cache/redis | usedmemory6 | 사용된 메모리(분할 6) | 바이트 | 최대
예 | Microsoft.Cache/redis | usedmemory7 | 사용된 메모리(분할 7) | 바이트 | 최대
예 | Microsoft.Cache/redis | usedmemory8 | 사용된 메모리(분할 8) | 바이트 | 최대
예 | Microsoft.Cache/redis | usedmemory9 | 사용된 메모리(분할 9) | 바이트 | 최대
예 | Microsoft.Cache/redis | usedmemorypercentage | 사용된 메모리 비율 | % | 최대
예 | Microsoft.Cache/redis | usedmemoryRss | 사용된 메모리 RSS | 바이트 | 최대
예 | Microsoft.Cache/redis | usedmemoryRss0 | 사용된 메모리 RSS(분할 0) | 바이트 | 최대
예 | Microsoft.Cache/redis | usedmemoryRss1 | 사용된 메모리 RSS(분할 1) | 바이트 | 최대
예 | Microsoft.Cache/redis | usedmemoryRss2 | 사용된 메모리 RSS(분할 2) | 바이트 | 최대
예 | Microsoft.Cache/redis | usedmemoryRss3 | 사용된 메모리 RSS(분할 3) | 바이트 | 최대
예 | Microsoft.Cache/redis | usedmemoryRss4 | 사용된 메모리 RSS(분할 4) | 바이트 | 최대
예 | Microsoft.Cache/redis | usedmemoryRss5 | 사용된 메모리 RSS(분할 5) | 바이트 | 최대
예 | Microsoft.Cache/redis | usedmemoryRss6 | 사용된 메모리 RSS(분할 6) | 바이트 | 최대
예 | Microsoft.Cache/redis | usedmemoryRss7 | 사용된 메모리 RSS(분할 7) | 바이트 | 최대
예 | Microsoft.Cache/redis | usedmemoryRss8 | 사용된 메모리 RSS(분할 8) | 바이트 | 최대
예 | Microsoft.Cache/redis | usedmemoryRss9 | 사용된 메모리 RSS(분할 9) | 바이트 | 최대
아니요 | Microsoft.ClassicCompute/domainNames/slots/roles | 디스크 읽기 바이트/초 | 디스크 읽기 | 초당 바이트 수 | 평균
예 | Microsoft.ClassicCompute/domainNames/slots/roles | 디스크 읽기 작업/초 | 디스크 읽기 작업/초 | 초당 개수 | 평균
아니요 | Microsoft.ClassicCompute/domainNames/slots/roles | 디스크 쓰기 바이트/초 | 디스크 쓰기 | 초당 바이트 수 | 평균
예 | Microsoft.ClassicCompute/domainNames/slots/roles | 디스크 쓰기 작업/초 | 디스크 쓰기 작업/초 | 초당 개수 | 평균
예 | Microsoft.ClassicCompute/domainNames/slots/roles | 네트워크 인 | 네트워크 인 | 바이트 | 합계
예 | Microsoft.ClassicCompute/domainNames/slots/roles | 네트워크 아웃 | 네트워크 아웃 | 바이트 | 합계
예 | Microsoft.ClassicCompute/domainNames/slots/roles | 백분율 CPU | 백분율 CPU | % | 평균
아니요 | Microsoft.ClassicCompute/virtualMachines | 디스크 읽기 바이트/초 | 디스크 읽기 | 초당 바이트 수 | 평균
예 | Microsoft.ClassicCompute/virtualMachines | 디스크 읽기 작업/초 | 디스크 읽기 작업/초 | 초당 개수 | 평균
아니요 | Microsoft.ClassicCompute/virtualMachines | 디스크 쓰기 바이트/초 | 디스크 쓰기 | 초당 바이트 수 | 평균
예 | Microsoft.ClassicCompute/virtualMachines | 디스크 쓰기 작업/초 | 디스크 쓰기 작업/초 | 초당 개수 | 평균
예 | Microsoft.ClassicCompute/virtualMachines | 네트워크 인 | 네트워크 인 | 바이트 | 합계
예 | Microsoft.ClassicCompute/virtualMachines | 네트워크 아웃 | 네트워크 아웃 | 바이트 | 합계
예 | Microsoft.ClassicCompute/virtualMachines | 백분율 CPU | 백분율 CPU | % | 평균
예 | Microsoft.ClassicStorage/storageAccounts | 가용성 | 가용성 | % | 평균
예 | Microsoft.ClassicStorage/storageAccounts | 송신 | 송신 | 바이트 | 합계
예 | Microsoft.ClassicStorage/storageAccounts | 수신 | 수신 | 바이트 | 합계
예 | Microsoft.ClassicStorage/storageAccounts | SuccessE2ELatency | 성공 E2E 대기 시간 | 밀리초 | 평균
예 | Microsoft.ClassicStorage/storageAccounts | SuccessServerLatency | 성공 서버 대기 시간 | 밀리초 | 평균
예 | Microsoft.ClassicStorage/storageAccounts | 트랜잭션 | 트랜잭션 | 개수 | 합계
아니요 | Microsoft.ClassicStorage/storageAccounts | UsedCapacity | 사용된 용량 | 바이트 | 평균
예 | ClassicStorage/storageAccounts/blobServices | 가용성 | 가용성 | % | 평균
아니요 | ClassicStorage/storageAccounts/blobServices | BlobCapacity | Blob 용량 | 바이트 | 평균
아니요 | ClassicStorage/storageAccounts/blobServices | BlobCount | Blob 수 | 개수 | 평균
예 | ClassicStorage/storageAccounts/blobServices | ContainerCount | Blob 컨테이너 수 | 개수 | 평균
예 | ClassicStorage/storageAccounts/blobServices | 송신 | 송신 | 바이트 | 합계
아니요 | ClassicStorage/storageAccounts/blobServices | IndexCapacity | 인덱스 용량 | 바이트 | 평균
예 | ClassicStorage/storageAccounts/blobServices | 수신 | 수신 | 바이트 | 합계
예 | ClassicStorage/storageAccounts/blobServices | SuccessE2ELatency | 성공 E2E 대기 시간 | 밀리초 | 평균
예 | ClassicStorage/storageAccounts/blobServices | SuccessServerLatency | 성공 서버 대기 시간 | 밀리초 | 평균
예 | ClassicStorage/storageAccounts/blobServices | 트랜잭션 | 트랜잭션 | 개수 | 합계
예 | ClassicStorage/storageAccounts/fileServices | 가용성 | 가용성 | % | 평균
예 | ClassicStorage/storageAccounts/fileServices | 송신 | 송신 | 바이트 | 합계
아니요 | ClassicStorage/storageAccounts/fileServices | FileCapacity | 파일 용량 | 바이트 | 평균
아니요 | ClassicStorage/storageAccounts/fileServices | FileCount | 파일 수 | 개수 | 평균
아니요 | ClassicStorage/storageAccounts/fileServices | FileShareCount | 파일 공유 수 | 개수 | 평균
아니요 | ClassicStorage/storageAccounts/fileServices | FileShareQuota | 파일 공유 할당량 크기 | 바이트 | 평균
아니요 | ClassicStorage/storageAccounts/fileServices | FileShareSnapshotCount | 파일 공유 스냅숏 수 | 개수 | 평균
아니요 | ClassicStorage/storageAccounts/fileServices | FileShareSnapshotSize | 파일 공유 스냅숏 크기 | 바이트 | 평균
예 | ClassicStorage/storageAccounts/fileServices | 수신 | 수신 | 바이트 | 합계
예 | ClassicStorage/storageAccounts/fileServices | SuccessE2ELatency | 성공 E2E 대기 시간 | 밀리초 | 평균
예 | ClassicStorage/storageAccounts/fileServices | SuccessServerLatency | 성공 서버 대기 시간 | 밀리초 | 평균
예 | ClassicStorage/storageAccounts/fileServices | 트랜잭션 | 트랜잭션 | 개수 | 합계
예 | ClassicStorage/storageAccounts/queueServices | 가용성 | 가용성 | % | 평균
예 | ClassicStorage/storageAccounts/queueServices | 송신 | 송신 | 바이트 | 합계
예 | ClassicStorage/storageAccounts/queueServices | 수신 | 수신 | 바이트 | 합계
예 | ClassicStorage/storageAccounts/queueServices | QueueCapacity | 큐 용량 | 바이트 | 평균
예 | ClassicStorage/storageAccounts/queueServices | QueueCount | 큐 수 | 개수 | 평균
예 | ClassicStorage/storageAccounts/queueServices | QueueMessageCount | 큐 메시지 수 | 개수 | 평균
예 | ClassicStorage/storageAccounts/queueServices | SuccessE2ELatency | 성공 E2E 대기 시간 | 밀리초 | 평균
예 | ClassicStorage/storageAccounts/queueServices | SuccessServerLatency | 성공 서버 대기 시간 | 밀리초 | 평균
예 | ClassicStorage/storageAccounts/queueServices | 트랜잭션 | 트랜잭션 | 개수 | 합계
예 | ClassicStorage/storageAccounts/tableServices | 가용성 | 가용성 | % | 평균
예 | ClassicStorage/storageAccounts/tableServices | 송신 | 송신 | 바이트 | 합계
예 | ClassicStorage/storageAccounts/tableServices | 수신 | 수신 | 바이트 | 합계
예 | ClassicStorage/storageAccounts/tableServices | SuccessE2ELatency | 성공 E2E 대기 시간 | 밀리초 | 평균
예 | ClassicStorage/storageAccounts/tableServices | SuccessServerLatency | 성공 서버 대기 시간 | 밀리초 | 평균
예 | ClassicStorage/storageAccounts/tableServices | TableCapacity | 테이블 용량 | 바이트 | 평균
예 | ClassicStorage/storageAccounts/tableServices | TableCount | 테이블 수 | 개수 | 평균
예 | ClassicStorage/storageAccounts/tableServices | TableEntityCount | 테이블 엔터티 수 | 개수 | 평균
예 | ClassicStorage/storageAccounts/tableServices | 트랜잭션 | 트랜잭션 | 개수 | 합계
예 | Microsoft.CognitiveServices/accounts | BlockedCalls | 차단된 호출 | 개수 | 합계
예 | Microsoft.CognitiveServices/accounts | CharactersTrained | 학습 된 문자 | 개수 | 합계
예 | Microsoft.CognitiveServices/accounts | CharactersTranslated | 변환된 문자 | 개수 | 합계
예 | Microsoft.CognitiveServices/accounts | ClientErrors | 클라이언트 오류 | 개수 | 합계
예 | Microsoft.CognitiveServices/accounts | DataIn | 데이터 입력 | 바이트 | 합계
예 | Microsoft.CognitiveServices/accounts | DataOut | 데이터 출력 | 바이트 | 합계
예 | Microsoft.CognitiveServices/accounts | 대기 시간 | 대기 시간 | 밀리초 | 평균
예 | Microsoft.CognitiveServices/accounts | ServerErrors | 서버 오류 | 개수 | 합계
예 | Microsoft.CognitiveServices/accounts | SpeechSessionDuration | 음성 세션 기간 | 초 | 합계
예 | Microsoft.CognitiveServices/accounts | SuccessfulCalls | 성공한 호출 | 개수 | 합계
예 | Microsoft.CognitiveServices/accounts | TotalCalls | 총 호출 | 개수 | 합계
예 | Microsoft.CognitiveServices/accounts | TotalErrors | Total Errors | 개수 | 합계
예 | Microsoft.CognitiveServices/accounts | TotalTokenCalls | 총 토큰 호출 | 개수 | 합계
예 | Microsoft.CognitiveServices/accounts | TotalTransactions | 총 트랜잭션 | 개수 | 합계
예 | Microsoft.Compute/virtualMachines | 사용된 CPU 크레딧 | 사용된 CPU 크레딧 | 개수 | 평균
예 | Microsoft.Compute/virtualMachines | 남은 CPU 크레딧 | 남은 CPU 크레딧 | 개수 | 평균
예 | Microsoft.Compute/virtualMachines | 데이터 디스크 큐 크기 | 데이터 디스크 큐 깊이 (미리 보기) | 개수 | 평균
예 | Microsoft.Compute/virtualMachines | Data Disk Read Bytes/sec | 데이터 디스크 읽기 바이트/초(미리 보기) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachines | 데이터 디스크 읽기 작업/초 | 데이터 디스크 읽기 작업/초(미리 보기) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachines | Data Disk Write Bytes/sec | 데이터 디스크 쓰기 바이트/초(미리 보기) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachines | 데이터 디스크 쓰기 작업/초 | 데이터 디스크 쓰기 작업/초(미리 보기) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachines | 디스크 읽기 바이트 | 디스크 읽기 바이트 | 바이트 | 합계
예 | Microsoft.Compute/virtualMachines | 디스크 읽기 작업/초 | 디스크 읽기 작업/초 | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachines | 디스크 쓰기 바이트 | 디스크 쓰기 바이트 | 바이트 | 합계
예 | Microsoft.Compute/virtualMachines | 디스크 쓰기 작업/초 | 디스크 쓰기 작업/초 | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachines | 인바운드 흐름 | 인바운드 흐름 | 개수 | 평균
예 | Microsoft.Compute/virtualMachines | 인바운드 흐름 최대 생성 빈도 | 인바운드 흐름 최대 생성 빈도 (미리 보기) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachines | 네트워크 인 | 청구 가능 네트워크 (사용 되지 않음) | 바이트 | 합계
예 | Microsoft.Compute/virtualMachines | 총 네트워크 | 총 네트워크 | 바이트 | 합계
예 | Microsoft.Compute/virtualMachines | 네트워크 아웃 | 네트워크 아웃 청구 가능 (사용 되지 않음) | 바이트 | 합계
예 | Microsoft.Compute/virtualMachines | 총 네트워크 | 총 네트워크 | 바이트 | 합계
예 | Microsoft.Compute/virtualMachines | OS 디스크 큐 크기 | OS 디스크 큐 깊이 (미리 보기) | 개수 | 평균
예 | Microsoft.Compute/virtualMachines | OS Disk Read Bytes/sec | OS 디스크 읽기 바이트/초(미리 보기) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachines | OS 디스크 읽기 작업/초 | OS 디스크 읽기 작업/초(미리 보기) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachines | OS 디스크 쓰기 바이트/초 | OS 디스크 쓰기 바이트/초(미리 보기) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachines | OS 디스크 쓰기 작업/초 | OS 디스크 쓰기 작업/초(미리 보기) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachines | 디스크당 OS QD | OS 디스크 QD (사용 되지 않음) | 개수 | 평균
예 | Microsoft.Compute/virtualMachines | 디스크당 OS 읽기 바이트/초 | OS Disk Read Bytes/Sec (사용 되지 않음) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachines | 디스크당 OS 읽기 작업/초 | OS 디스크 읽기 작업/초 (사용 되지 않음) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachines | 디스크당 OS 쓰기 바이트/초 | OS Disk Write Bytes/Sec (사용 되지 않음) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachines | 디스크당 OS 쓰기 작업/초 | OS 디스크 쓰기 작업/초 (사용 되지 않음) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachines | 아웃 바운드 흐름 | 아웃 바운드 흐름 | 개수 | 평균
예 | Microsoft.Compute/virtualMachines | 아웃 바운드 흐름 최대 생성 빈도 | 아웃 바운드 흐름 최대 생성 빈도 (미리 보기) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachines | 디스크당 QD | 데이터 디스크 QD (사용 되지 않음) | 개수 | 평균
예 | Microsoft.Compute/virtualMachines | 디스크당 읽기 바이트/초 | 데이터 디스크 읽기 바이트/초 (사용 되지 않음) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachines | 디스크당 읽기 작업/초 | 데이터 디스크 읽기 작업/초 (사용 되지 않음) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachines | 디스크당 쓰기 바이트/초 | 데이터 디스크 쓰기 바이트/초 (사용 되지 않음) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachines | 디스크당 쓰기 작업/초 | 데이터 디스크 쓰기 작업/초 (사용 되지 않음) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachines | 백분율 CPU | 백분율 CPU | % | 평균
예 | Microsoft.Compute/virtualMachines | 프리미엄 데이터 디스크 캐시 읽기 적중 | 프리미엄 데이터 디스크 캐시 읽기 적중 (미리 보기) | % | 평균
예 | Microsoft.Compute/virtualMachines | 프리미엄 데이터 디스크 캐시 읽기 누락 | 프리미엄 데이터 디스크 캐시 읽기 누락 (미리 보기) | % | 평균
예 | Microsoft.Compute/virtualMachines | 프리미엄 OS 디스크 캐시 읽기 적중 | 프리미엄 OS 디스크 캐시 읽기 적중 (미리 보기) | % | 평균
예 | Microsoft.Compute/virtualMachines | 프리미엄 OS 디스크 캐시 읽기 누락 | 프리미엄 OS 디스크 캐시 읽기 누락 (미리 보기) | % | 평균
예 | Microsoft.Compute/virtualMachineScaleSets | 사용된 CPU 크레딧 | 사용된 CPU 크레딧 | 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets | 남은 CPU 크레딧 | 남은 CPU 크레딧 | 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets | 데이터 디스크 큐 크기 | 데이터 디스크 큐 깊이 (미리 보기) | 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets | Data Disk Read Bytes/sec | 데이터 디스크 읽기 바이트/초(미리 보기) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets | 데이터 디스크 읽기 작업/초 | 데이터 디스크 읽기 작업/초(미리 보기) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets | Data Disk Write Bytes/sec | 데이터 디스크 쓰기 바이트/초(미리 보기) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets | 데이터 디스크 쓰기 작업/초 | 데이터 디스크 쓰기 작업/초(미리 보기) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets | 디스크 읽기 바이트 | 디스크 읽기 바이트 | 바이트 | 합계
예 | Microsoft.Compute/virtualMachineScaleSets | 디스크 읽기 작업/초 | 디스크 읽기 작업/초 | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets | 디스크 쓰기 바이트 | 디스크 쓰기 바이트 | 바이트 | 합계
예 | Microsoft.Compute/virtualMachineScaleSets | 디스크 쓰기 작업/초 | 디스크 쓰기 작업/초 | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets | 인바운드 흐름 | 인바운드 흐름 | 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets | 인바운드 흐름 최대 생성 빈도 | 인바운드 흐름 최대 생성 빈도 (미리 보기) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets | 네트워크 인 | 청구 가능 네트워크 (사용 되지 않음) | 바이트 | 합계
예 | Microsoft.Compute/virtualMachineScaleSets | 총 네트워크 | 총 네트워크 | 바이트 | 합계
예 | Microsoft.Compute/virtualMachineScaleSets | 네트워크 아웃 | 네트워크 아웃 청구 가능 (사용 되지 않음) | 바이트 | 합계
예 | Microsoft.Compute/virtualMachineScaleSets | 총 네트워크 | 총 네트워크 | 바이트 | 합계
예 | Microsoft.Compute/virtualMachineScaleSets | OS 디스크 큐 크기 | OS 디스크 큐 깊이 (미리 보기) | 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets | OS Disk Read Bytes/sec | OS 디스크 읽기 바이트/초(미리 보기) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets | OS 디스크 읽기 작업/초 | OS 디스크 읽기 작업/초(미리 보기) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets | OS 디스크 쓰기 바이트/초 | OS 디스크 쓰기 바이트/초(미리 보기) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets | OS 디스크 쓰기 작업/초 | OS 디스크 쓰기 작업/초(미리 보기) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets | 디스크당 OS QD | OS 디스크 QD (사용 되지 않음) | 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets | 디스크당 OS 읽기 바이트/초 | OS Disk Read Bytes/Sec (사용 되지 않음) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets | 디스크당 OS 읽기 작업/초 | OS 디스크 읽기 작업/초 (사용 되지 않음) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets | 디스크당 OS 쓰기 바이트/초 | OS Disk Write Bytes/Sec (사용 되지 않음) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets | 디스크당 OS 쓰기 작업/초 | OS 디스크 쓰기 작업/초 (사용 되지 않음) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets | 아웃 바운드 흐름 | 아웃 바운드 흐름 | 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets | 아웃 바운드 흐름 최대 생성 빈도 | 아웃 바운드 흐름 최대 생성 빈도 (미리 보기) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets | 디스크당 QD | 데이터 디스크 QD (사용 되지 않음) | 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets | 디스크당 읽기 바이트/초 | 데이터 디스크 읽기 바이트/초 (사용 되지 않음) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets | 디스크당 읽기 작업/초 | 데이터 디스크 읽기 작업/초 (사용 되지 않음) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets | 디스크당 쓰기 바이트/초 | 데이터 디스크 쓰기 바이트/초 (사용 되지 않음) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets | 디스크당 쓰기 작업/초 | 데이터 디스크 쓰기 작업/초 (사용 되지 않음) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets | 백분율 CPU | 백분율 CPU | % | 평균
예 | Microsoft.Compute/virtualMachineScaleSets | 프리미엄 데이터 디스크 캐시 읽기 적중 | 프리미엄 데이터 디스크 캐시 읽기 적중 (미리 보기) | % | 평균
예 | Microsoft.Compute/virtualMachineScaleSets | 프리미엄 데이터 디스크 캐시 읽기 누락 | 프리미엄 데이터 디스크 캐시 읽기 누락 (미리 보기) | % | 평균
예 | Microsoft.Compute/virtualMachineScaleSets | 프리미엄 OS 디스크 캐시 읽기 적중 | 프리미엄 OS 디스크 캐시 읽기 적중 (미리 보기) | % | 평균
예 | Microsoft.Compute/virtualMachineScaleSets | 프리미엄 OS 디스크 캐시 읽기 누락 | 프리미엄 OS 디스크 캐시 읽기 누락 (미리 보기) | % | 평균
예 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 사용된 CPU 크레딧 | 사용된 CPU 크레딧 | 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 남은 CPU 크레딧 | 남은 CPU 크레딧 | 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 데이터 디스크 큐 크기 | 데이터 디스크 큐 깊이 (미리 보기) | 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Data Disk Read Bytes/sec | 데이터 디스크 읽기 바이트/초(미리 보기) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 데이터 디스크 읽기 작업/초 | 데이터 디스크 읽기 작업/초(미리 보기) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Data Disk Write Bytes/sec | 데이터 디스크 쓰기 바이트/초(미리 보기) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 데이터 디스크 쓰기 작업/초 | 데이터 디스크 쓰기 작업/초(미리 보기) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 디스크 읽기 바이트 | 디스크 읽기 바이트 | 바이트 | 합계
예 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 디스크 읽기 작업/초 | 디스크 읽기 작업/초 | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 디스크 쓰기 바이트 | 디스크 쓰기 바이트 | 바이트 | 합계
예 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 디스크 쓰기 작업/초 | 디스크 쓰기 작업/초 | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 인바운드 흐름 | 인바운드 흐름 | 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 인바운드 흐름 최대 생성 빈도 | 인바운드 흐름 최대 생성 빈도 (미리 보기) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 네트워크 인 | 청구 가능 네트워크 (사용 되지 않음) | 바이트 | 합계
예 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 총 네트워크 | 총 네트워크 | 바이트 | 합계
예 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 네트워크 아웃 | 네트워크 아웃 청구 가능 (사용 되지 않음) | 바이트 | 합계
예 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 총 네트워크 | 총 네트워크 | 바이트 | 합계
예 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | OS 디스크 큐 크기 | OS 디스크 큐 깊이 (미리 보기) | 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | OS Disk Read Bytes/sec | OS 디스크 읽기 바이트/초(미리 보기) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | OS 디스크 읽기 작업/초 | OS 디스크 읽기 작업/초(미리 보기) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | OS 디스크 쓰기 바이트/초 | OS 디스크 쓰기 바이트/초(미리 보기) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | OS 디스크 쓰기 작업/초 | OS 디스크 쓰기 작업/초(미리 보기) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 디스크당 OS QD | OS 디스크 QD (사용 되지 않음) | 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 디스크당 OS 읽기 바이트/초 | OS Disk Read Bytes/Sec (사용 되지 않음) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 디스크당 OS 읽기 작업/초 | OS 디스크 읽기 작업/초 (사용 되지 않음) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 디스크당 OS 쓰기 바이트/초 | OS Disk Write Bytes/Sec (사용 되지 않음) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 디스크당 OS 쓰기 작업/초 | OS 디스크 쓰기 작업/초 (사용 되지 않음) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 아웃 바운드 흐름 | 아웃 바운드 흐름 | 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 아웃 바운드 흐름 최대 생성 빈도 | 아웃 바운드 흐름 최대 생성 빈도 (미리 보기) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 디스크당 QD | 데이터 디스크 QD (사용 되지 않음) | 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 디스크당 읽기 바이트/초 | 데이터 디스크 읽기 바이트/초 (사용 되지 않음) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 디스크당 읽기 작업/초 | 데이터 디스크 읽기 작업/초 (사용 되지 않음) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 디스크당 쓰기 바이트/초 | 데이터 디스크 쓰기 바이트/초 (사용 되지 않음) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 디스크당 쓰기 작업/초 | 데이터 디스크 쓰기 작업/초 (사용 되지 않음) | 초당 개수 | 평균
예 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 백분율 CPU | 백분율 CPU | % | 평균
예 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 프리미엄 데이터 디스크 캐시 읽기 적중 | 프리미엄 데이터 디스크 캐시 읽기 적중 (미리 보기) | % | 평균
예 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 프리미엄 데이터 디스크 캐시 읽기 누락 | 프리미엄 데이터 디스크 캐시 읽기 누락 (미리 보기) | % | 평균
예 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 프리미엄 OS 디스크 캐시 읽기 적중 | 프리미엄 OS 디스크 캐시 읽기 적중 (미리 보기) | % | 평균
예 | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | 프리미엄 OS 디스크 캐시 읽기 누락 | 프리미엄 OS 디스크 캐시 읽기 누락 (미리 보기) | % | 평균
예 | Microsoft.ContainerInstance/containerGroups | CpuUsage | CPU 사용량 | 개수 | 평균
예 | Microsoft.ContainerInstance/containerGroups | MemoryUsage | 메모리 사용량 | 바이트 | 평균
예 | Microsoft.ContainerInstance/containerGroups | NetworkBytesReceivedPerSecond | 초당 수신된 네트워크 바이트 | 바이트 | 평균
예 | Microsoft.ContainerInstance/containerGroups | NetworkBytesTransmittedPerSecond | 초당 전송된 네트워크 바이트 | 바이트 | 평균
예 | Microsoft.ContainerRegistry/registries | RunDuration | 실행 지속 시간 | 밀리초 | 합계
예 | Microsoft.ContainerRegistry/registries | SuccessfulPullCount | 성공적인 끌어오기 수 | 개수 | 평균
예 | Microsoft.ContainerRegistry/registries | SuccessfulPushCount | 성공한 푸시 수 | 개수 | 평균
예 | Microsoft.ContainerRegistry/registries | TotalPullCount | 총 풀 수 | 개수 | 평균
예 | Microsoft.ContainerRegistry/registries | TotalPushCount | 총 푸시 수 | 개수 | 평균
아니요 | Microsoft.ContainerService/managedClusters | kube_node_status_allocatable_cpu_cores | 관리 클러스터에서 사용 가능한 cpu 코어의 총 수 | 개수 | 평균
아니요 | Microsoft.ContainerService/managedClusters | kube_node_status_allocatable_memory_bytes | 관리 클러스터에서 사용 가능한 총 메모리 양 | 바이트 | 평균
아니요 | Microsoft.ContainerService/managedClusters | kube_node_status_condition | 다양한 노드 조건에 대한 상태 | 개수 | 평균
아니요 | Microsoft.ContainerService/managedClusters | kube_pod_status_phase | 단계별 Pod 수 | 개수 | 평균
아니요 | Microsoft.ContainerService/managedClusters | kube_pod_status_ready | 준비 상태인 Pod 수 | 개수 | 평균
예 | Microsoft.DataBoxEdge/dataBoxEdgeDevices | AvailableCapacity | 사용 가능한 용량 | 바이트 | 평균
예 | Microsoft.DataBoxEdge/dataBoxEdgeDevices | BytesUploadedToCloud | 업로드 된 클라우드 바이트 (장치) | 바이트 | 평균
예 | Microsoft.DataBoxEdge/dataBoxEdgeDevices | BytesUploadedToCloudPerShare | 업로드 된 클라우드 바이트 (공유) | 바이트 | 평균
예 | Microsoft.DataBoxEdge/dataBoxEdgeDevices | CloudReadThroughput | 클라우드 다운로드 처리량 | 초당 바이트 수 | 평균
예 | Microsoft.DataBoxEdge/dataBoxEdgeDevices | CloudReadThroughputPerShare | 클라우드 다운로드 처리량 (공유) | 초당 바이트 수 | 평균
예 | Microsoft.DataBoxEdge/dataBoxEdgeDevices | CloudUploadThroughput | 클라우드 업로드 처리량 | 초당 바이트 수 | 평균
예 | Microsoft.DataBoxEdge/dataBoxEdgeDevices | CloudUploadThroughputPerShare | 클라우드 업로드 처리량 (공유) | 초당 바이트 수 | 평균
예 | Microsoft.DataBoxEdge/dataBoxEdgeDevices | HyperVMemoryUtilization | Edge 계산-메모리 사용량 | % | 평균
예 | Microsoft.DataBoxEdge/dataBoxEdgeDevices | HyperVVirtualProcessorUtilization | Edge 계산-CPU 비율 | % | 평균
예 | Microsoft.DataBoxEdge/dataBoxEdgeDevices | NICReadThroughput | 읽기 처리량 (네트워크) | 초당 바이트 수 | 평균
예 | Microsoft.DataBoxEdge/dataBoxEdgeDevices | NICWriteThroughput | 쓰기 처리량 (네트워크) | 초당 바이트 수 | 평균
예 | Microsoft.DataBoxEdge/dataBoxEdgeDevices | 총 용량이 | 총 용량 | 바이트 | 평균
예 | Microsoft.DataFactory/datafactories | FailedRuns | 실패한 실행 | 개수 | 합계
예 | Microsoft.DataFactory/datafactories | SuccessfulRuns | 성공한 실행 | 개수 | 합계
예 | Microsoft.DataFactory/factories | ActivityCancelledRuns | 취소 한 작업에서 메트릭 실행 | 개수 | 합계
예 | Microsoft.DataFactory/factories | ActivityFailedRuns | 실패한 활동 실행 메트릭 | 개수 | 합계
예 | Microsoft.DataFactory/factories | ActivitySucceededRuns | 성공한 활동 실행 메트릭 | 개수 | 합계
예 | Microsoft.DataFactory/factories | FactorySizeInGbUnits | 총 팩터리 크기 (GB 단위) | 개수 | 최대
예 | Microsoft.DataFactory/factories | IntegrationRuntimeAvailableMemory | 통합 런타임 사용 가능한 메모리 | 바이트 | 평균
예 | Microsoft.DataFactory/factories | IntegrationRuntimeAverageTaskPickupDelay | Integration runtime 큐 기간 | 초 | 평균
예 | Microsoft.DataFactory/factories | IntegrationRuntimeCpuPercentage | 통합 런타임 CPU 사용률 | % | 평균
예 | Microsoft.DataFactory/factories | IntegrationRuntimeQueueLength | Integration runtime 큐 길이 | 개수 | 평균
예 | Microsoft.DataFactory/factories | MaxAllowedFactorySizeInGbUnits | 최대 허용 팩터리 크기 (GB 단위) | 개수 | 최대
예 | Microsoft.DataFactory/factories | MaxAllowedResourceCount | 허용 되는 최대 엔터티 수 | 개수 | 최대
예 | Microsoft.DataFactory/factories | PipelineCancelledRuns | 취소 한 파이프라인 실행 메트릭 | 개수 | 합계
예 | Microsoft.DataFactory/factories | PipelineFailedRuns | 실패한 파이프라인 실행 메트릭 | 개수 | 합계
예 | Microsoft.DataFactory/factories | PipelineSucceededRuns | 성공한 파이프라인 실행 메트릭 | 개수 | 합계
예 | Microsoft.DataFactory/factories | ResourceCount | 총 엔터티 수 | 개수 | 최대
예 | Microsoft.DataFactory/factories | TriggerCancelledRuns | 취소 한 트리거 실행 메트릭 | 개수 | 합계
예 | Microsoft.DataFactory/factories | TriggerFailedRuns | 실패한 트리거 실행 메트릭 | 개수 | 합계
예 | Microsoft.DataFactory/factories | TriggerSucceededRuns | 성공한 트리거 실행 메트릭 | 개수 | 합계
예 | Microsoft.DataLakeAnalytics/accounts | JobAUEndedCancelled | 취소된 AU 시간 | 초 | 합계
예 | Microsoft.DataLakeAnalytics/accounts | JobAUEndedFailure | 실패한 AU 시간 | 초 | 합계
예 | Microsoft.DataLakeAnalytics/accounts | JobAUEndedSuccess | 성공한 AU 시간 | 초 | 합계
예 | Microsoft.DataLakeAnalytics/accounts | JobEndedCancelled | 취소된 작업 | 개수 | 합계
예 | Microsoft.DataLakeAnalytics/accounts | JobEndedFailure | 실패한 작업 | 개수 | 합계
예 | Microsoft.DataLakeAnalytics/accounts | JobEndedSuccess | 성공한 작업 | 개수 | 합계
예 | Microsoft.DataLakeStore/accounts | DataRead | 데이터 읽기 | 바이트 | 합계
예 | Microsoft.DataLakeStore/accounts | DataWritten | 기록된 데이터 | 바이트 | 합계
예 | Microsoft.DataLakeStore/accounts | ReadRequests | 읽기 요청 | 개수 | 합계
예 | Microsoft.DataLakeStore/accounts | TotalStorage | 총 스토리지 | 바이트 | 최대
예 | Microsoft.DataLakeStore/accounts | WriteRequests | 쓰기 요청 | 개수 | 합계
예 | Microsoft.DBforMariaDB/servers | active_connections | 활성 연결 수 | 개수 | 평균
예 | Microsoft.DBforMariaDB/servers | backup_storage_used | 사용 되는 백업 저장소 | 바이트 | 평균
예 | Microsoft.DBforMariaDB/servers | connections_failed | 실패한 연결 | 개수 | 합계
예 | Microsoft.DBforMariaDB/servers | cpu_percent | CPU 백분율 | % | 평균
예 | Microsoft.DBforMariaDB/servers | io_consumption_percent | IO 백분율 | % | 평균
예 | Microsoft.DBforMariaDB/servers | memory_percent | 메모리 백분율 | % | 평균
예 | Microsoft.DBforMariaDB/servers | network_bytes_egress | 네트워크 아웃 | 바이트 | 합계
예 | Microsoft.DBforMariaDB/servers | network_bytes_ingress | 네트워크 인 | 바이트 | 합계
예 | Microsoft.DBforMariaDB/servers | seconds_behind_master | 복제 지연 시간(초) | 개수 | 최대
예 | Microsoft.DBforMariaDB/servers | serverlog_storage_limit | 서버 로그 스토리지 제한 | 바이트 | 평균
예 | Microsoft.DBforMariaDB/servers | serverlog_storage_percent | 서버 로그 스토리지 비율 | % | 평균
예 | Microsoft.DBforMariaDB/servers | serverlog_storage_percent | 사용된 서버 로그 스토리지 | 바이트 | 평균
예 | Microsoft.DBforMariaDB/servers | storage_limit | 스토리지 제한 | 바이트 | 최대
예 | Microsoft.DBforMariaDB/servers | storage_percent | 스토리지 비율 | % | 평균
예 | Microsoft.DBforMariaDB/servers | storage_used | 스토리지 사용됨 | 바이트 | 평균
예 | Microsoft.DBforMySQL/servers | active_connections | 활성 연결 수 | 개수 | 평균
예 | Microsoft.DBforMySQL/servers | backup_storage_used | 사용 되는 백업 저장소 | 바이트 | 평균
예 | Microsoft.DBforMySQL/servers | connections_failed | 실패한 연결 | 개수 | 합계
예 | Microsoft.DBforMySQL/servers | cpu_percent | CPU 백분율 | % | 평균
예 | Microsoft.DBforMySQL/servers | io_consumption_percent | IO 백분율 | % | 평균
예 | Microsoft.DBforMySQL/servers | memory_percent | 메모리 백분율 | % | 평균
예 | Microsoft.DBforMySQL/servers | network_bytes_egress | 네트워크 아웃 | 바이트 | 합계
예 | Microsoft.DBforMySQL/servers | network_bytes_ingress | 네트워크 인 | 바이트 | 합계
예 | Microsoft.DBforMySQL/servers | seconds_behind_master | 복제 지연 시간(초) | 개수 | 최대
예 | Microsoft.DBforMySQL/servers | serverlog_storage_limit | 서버 로그 스토리지 제한 | 바이트 | 최대
예 | Microsoft.DBforMySQL/servers | serverlog_storage_percent | 서버 로그 스토리지 비율 | % | 평균
예 | Microsoft.DBforMySQL/servers | serverlog_storage_percent | 사용된 서버 로그 스토리지 | 바이트 | 평균
예 | Microsoft.DBforMySQL/servers | storage_limit | 스토리지 제한 | 바이트 | 최대
예 | Microsoft.DBforMySQL/servers | storage_percent | 스토리지 비율 | % | 평균
예 | Microsoft.DBforMySQL/servers | storage_used | 스토리지 사용됨 | 바이트 | 평균
예 | Microsoft.DBforPostgreSQL/servers | active_connections | 활성 연결 수 | 개수 | 평균
예 | Microsoft.DBforPostgreSQL/servers | backup_storage_used | 사용 되는 백업 저장소 | 바이트 | 평균
예 | Microsoft.DBforPostgreSQL/servers | connections_failed | 실패한 연결 | 개수 | 합계
예 | Microsoft.DBforPostgreSQL/servers | cpu_percent | CPU 백분율 | % | 평균
예 | Microsoft.DBforPostgreSQL/servers | io_consumption_percent | IO 백분율 | % | 평균
예 | Microsoft.DBforPostgreSQL/servers | memory_percent | 메모리 백분율 | % | 평균
예 | Microsoft.DBforPostgreSQL/servers | network_bytes_egress | 네트워크 아웃 | 바이트 | 합계
예 | Microsoft.DBforPostgreSQL/servers | network_bytes_ingress | 네트워크 인 | 바이트 | 합계
예 | Microsoft.DBforPostgreSQL/servers | pg_replica_log_delay_in_bytes | 복제본 간 최대 지연 시간 | 바이트 | 최대
예 | Microsoft.DBforPostgreSQL/servers | pg_replica_log_delay_in_seconds | 복제본 지연 시간 | 초 | 최대
예 | Microsoft.DBforPostgreSQL/servers | serverlog_storage_limit | 서버 로그 스토리지 제한 | 바이트 | 최대
예 | Microsoft.DBforPostgreSQL/servers | serverlog_storage_percent | 서버 로그 스토리지 비율 | % | 평균
예 | Microsoft.DBforPostgreSQL/servers | serverlog_storage_percent | 사용된 서버 로그 스토리지 | 바이트 | 평균
예 | Microsoft.DBforPostgreSQL/servers | storage_limit | 스토리지 제한 | 바이트 | 최대
예 | Microsoft.DBforPostgreSQL/servers | storage_percent | 스토리지 비율 | % | 평균
예 | Microsoft.DBforPostgreSQL/servers | storage_used | 스토리지 사용됨 | 바이트 | 평균
예 | Microsoft.DBforPostgreSQL/serversv2 | active_connections | 활성 연결 수 | 개수 | 평균
예 | Microsoft.DBforPostgreSQL/serversv2 | cpu_percent | CPU 백분율 | % | 평균
예 | Microsoft.DBforPostgreSQL/serversv2 | 사서함당 | IOPS | 개수 | 평균
예 | Microsoft.DBforPostgreSQL/serversv2 | memory_percent | 메모리 백분율 | % | 평균
예 | Microsoft.DBforPostgreSQL/serversv2 | network_bytes_egress | 네트워크 아웃 | 바이트 | 합계
예 | Microsoft.DBforPostgreSQL/serversv2 | network_bytes_ingress | 네트워크 인 | 바이트 | 합계
예 | Microsoft.DBforPostgreSQL/serversv2 | storage_percent | 스토리지 비율 | % | 평균
예 | Microsoft.DBforPostgreSQL/serversv2 | storage_used | 스토리지 사용됨 | 바이트 | 평균
예 | Microsoft. 장치/계정 | digitaltwins | 디지털 Twins 노드 원격 분석 자리 표시자 | 개수 | 합계
예 | Microsoft.Devices/IotHubs | c2d.commands.egress.abandon.success | C2D 메시지 중단 됨 | 개수 | 합계
예 | Microsoft.Devices/IotHubs | c2d.commands.egress.complete.success | C2D 메시지 배달이 완료 됨 | 개수 | 합계
예 | Microsoft.Devices/IotHubs | c2d.commands.egress.reject.success | 거부 된 C2D 메시지 | 개수 | 합계
예 | Microsoft.Devices/IotHubs | c2d.methods.failure | 실패한 직접 메서드 호출 | 개수 | 합계
예 | Microsoft.Devices/IotHubs | c2d.methods.requestSize | 직접 메서드 호출의 요청 크기 | 바이트 | 평균
예 | Microsoft.Devices/IotHubs | c2d.methods.responseSize | 직접 메서드 호출의 응답 크기 | 바이트 | 평균
예 | Microsoft.Devices/IotHubs | c2d.methods.success | 성공한 직접 메서드 호출 | 개수 | 합계
예 | Microsoft.Devices/IotHubs | c2d.twin.read.failure | 백 엔드에서의 실패한 쌍 읽기 | 개수 | 합계
예 | Microsoft.Devices/IotHubs | c2d.twin.read.size | 백 엔드에서의 쌍 읽기 응답 크기 | 바이트 | 평균
예 | Microsoft.Devices/IotHubs | c2d.twin.read.success | 백 엔드에서의 성공한 쌍 읽기 | 개수 | 합계
예 | Microsoft.Devices/IotHubs | c2d.twin.update.failure | 백 엔드에서의 실패한 쌍 업데이트 | 개수 | 합계
예 | Microsoft.Devices/IotHubs | c2d.twin.update.size | 백 엔드에서의 쌍 업데이트 크기 | 바이트 | 평균
예 | Microsoft.Devices/IotHubs | c2d.twin.update.success | 백 엔드에서의 성공한 쌍 업데이트 | 개수 | 합계
예 | Microsoft.Devices/IotHubs | C2DMessagesExpired | C2D 메시지 만료 됨 (미리 보기) | 개수 | 합계
예 | Microsoft.Devices/IotHubs | 구성 | 구성 메트릭 | 개수 | 합계
아니요 | Microsoft.Devices/IotHubs | connectedDeviceCount | 연결된 디바이스(미리 보기) | 개수 | 평균
예 | Microsoft.Devices/IotHubs | d2c.endpoints.egress.builtIn.events | 라우팅: 메시지/이벤트에 배달된 메시지 | 개수 | 합계
예 | Microsoft.Devices/IotHubs | d2c.endpoints.egress.eventHubs | 라우팅: 이벤트 허브에 배달된 메시지 | 개수 | 합계
예 | Microsoft.Devices/IotHubs | d2c.endpoints.egress.serviceBusQueues | 라우팅: Service Bus 큐에 배달된 메시지 | 개수 | 합계
예 | Microsoft.Devices/IotHubs | d2c.endpoints.egress.serviceBusTopics | 라우팅: Service Bus 토픽에 배달된 메시지 | 개수 | 합계
예 | Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage | 라우팅: 스토리지에 배달된 메시지 | 개수 | 합계
예 | Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage.blobs | 라우팅: 스토리지에 배달된 Blob | 개수 | 합계
예 | Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage.bytes | 라우팅: 스토리지에 배달된 데이터 | 바이트 | 합계
예 | Microsoft.Devices/IotHubs | d2c.endpoints.latency.builtIn.events | 라우팅: 메시지/이벤트에 대한 메시지 대기 시간 | 밀리초 | 평균
예 | Microsoft.Devices/IotHubs | d2c.endpoints.latency.eventHubs | 라우팅: 이벤트 허브에 대한 메시지 대기 시간 | 밀리초 | 평균
예 | Microsoft.Devices/IotHubs | d2c.endpoints.latency.serviceBusQueues | 라우팅: Service Bus 큐에 대한 메시지 대기 시간 | 밀리초 | 평균
예 | Microsoft.Devices/IotHubs | d2c.endpoints.latency.serviceBusTopics | 라우팅: Service Bus 토픽에 대한 메시지 대기 시간 | 밀리초 | 평균
예 | Microsoft.Devices/IotHubs | d2c.endpoints.latency.storage | 라우팅: 스토리지에 대한 메시지 대기 시간 | 밀리초 | 평균
예 | Microsoft.Devices/IotHubs | d2c.telemetry.egress.dropped | 라우팅: 삭제된 원격 분석 메시지  | 개수 | 합계
예 | Microsoft.Devices/IotHubs | d2c.telemetry.egress.fallback | 라우팅: 대체에 배달된 메시지 | 개수 | 합계
예 | Microsoft.Devices/IotHubs | d2c.telemetry.egress.invalid | 라우팅: 원격 분석 메시지 호환되지 않음 | 개수 | 합계
예 | Microsoft.Devices/IotHubs | d2c.telemetry.egress.orphaned | 라우팅: 분리된 원격 분석 메시지  | 개수 | 합계
예 | Microsoft.Devices/IotHubs | d2c.telemetry.egress.success | 라우팅: 배달된 원격 분석 메시지 | 개수 | 합계
예 | Microsoft.Devices/IotHubs | d2c.telemetry.ingress.allProtocol | 원격 분석 메시지 보내기 시도 | 개수 | 합계
예 | Microsoft.Devices/IotHubs | d2c.telemetry.ingress.sendThrottle | 제한 오류 수 | 개수 | 합계
예 | Microsoft.Devices/IotHubs | d2c.telemetry.ingress.success | 보낸 원격 분석 메시지 | 개수 | 합계
예 | Microsoft.Devices/IotHubs | d2c.twin.read.failure | 디바이스에서의 실패한 쌍 읽기 | 개수 | 합계
예 | Microsoft.Devices/IotHubs | d2c.twin.read.size | 디바이스에서의 쌍 읽기 응답 크기 | 바이트 | 평균
예 | Microsoft.Devices/IotHubs | d2c.twin.read.success | 디바이스에서의 성공한 쌍 읽기 | 개수 | 합계
예 | Microsoft.Devices/IotHubs | d2c.twin.update.failure | 디바이스에서의 실패한 쌍 업데이트 | 개수 | 합계
예 | Microsoft.Devices/IotHubs | d2c.twin.update.size | 디바이스에서의 쌍 업데이트 크기 | 바이트 | 평균
예 | Microsoft.Devices/IotHubs | d2c.twin.update.success | 디바이스에서의 성공한 쌍 업데이트 | 개수 | 합계
예 | Microsoft.Devices/IotHubs | dailyMessageQuotaUsed | 사용된 전체 메시지 수 | 개수 | 평균
예 | Microsoft.Devices/IotHubs | deviceDataUsage | 총 장치 데이터 사용량 | 바이트 | 합계
예 | Microsoft.Devices/IotHubs | deviceDataUsageV2 | 총 디바이스 데이터 사용량(미리 보기) | 바이트 | 합계
예 | Microsoft.Devices/IotHubs | devices.connectedDevices.allProtocol | 연결된 디바이스(사용되지 않음)  | 개수 | 합계
예 | Microsoft.Devices/IotHubs | devices.totalDevices | 총 디바이스(사용되지 않음) | 개수 | 합계
예 | Microsoft.Devices/IotHubs | EventGridDeliveries | Event Grid 배달 (미리 보기) | 개수 | 합계
예 | Microsoft.Devices/IotHubs | EventGridLatency | Event Grid 대기 시간 (미리 보기) | 밀리초 | 평균
예 | Microsoft.Devices/IotHubs | jobs.cancelJob.failure | 실패한 작업 취소 | 개수 | 합계
예 | Microsoft.Devices/IotHubs | jobs.cancelJob.success | 성공한 작업 취소 | 개수 | 합계
예 | Microsoft.Devices/IotHubs | jobs.completed | 완료된 작업 | 개수 | 합계
예 | Microsoft.Devices/IotHubs | jobs.createDirectMethodJob.failure | 실패한 메서드 호출 작업 만들기 | 개수 | 합계
예 | Microsoft.Devices/IotHubs | jobs.createDirectMethodJob.success | 메서드 호출 작업에 대한 성공한 만들기 | 개수 | 합계
예 | Microsoft.Devices/IotHubs | jobs.createTwinUpdateJob.failure | 쌍 업데이트 작업에 대한 실패한 만들기 | 개수 | 합계
예 | Microsoft.Devices/IotHubs | jobs.createTwinUpdateJob.success | 쌍 업데이트 작업에 대한 성공한 만들기 | 개수 | 합계
예 | Microsoft.Devices/IotHubs | jobs.failed | 실패한 작업 | 개수 | 합계
예 | Microsoft.Devices/IotHubs | jobs.listJobs.failure | 목록 작업에 대한 실패한 호출 | 개수 | 합계
예 | Microsoft.Devices/IotHubs | jobs.listJobs.success | 목록 작업에 대한 성공한 호출 | 개수 | 합계
예 | Microsoft.Devices/IotHubs | jobs.queryJobs.failure | 실패한 작업 쿼리 | 개수 | 합계
예 | Microsoft.Devices/IotHubs | jobs.queryJobs.success | 성공한 작업 쿼리 | 개수 | 합계
아니요 | Microsoft.Devices/IotHubs | totalDeviceCount | 총 디바이스(미리 보기) | 개수 | 평균
예 | Microsoft.Devices/IotHubs | twinQueries.failure | 실패한 쌍 쿼리 | 개수 | 합계
예 | Microsoft.Devices/IotHubs | twinQueries.resultSize | 쌍 쿼리 결과 크기 | 바이트 | 평균
예 | Microsoft.Devices/IotHubs | twinQueries.success | 성공한 쌍 쿼리 | 개수 | 합계
예 | Microsoft.Devices/provisioningServices | AttestationAttempts | 증명 시도 | 개수 | 합계
예 | Microsoft.Devices/provisioningServices | DeviceAssignments | 할당된 디바이스 | 개수 | 합계
예 | Microsoft.Devices/provisioningServices | RegistrationAttempts | 등록 시도 | 개수 | 합계
아니요 | Microsoft.DocumentDB/databaseAccounts | AvailableStorage | 사용 가능한 스토리지 | 바이트 | 합계
아니요 | Microsoft.DocumentDB/databaseAccounts | CassandraConnectionClosures | Cassandra 연결 차단 | 개수 | 합계
아니요 | Microsoft.DocumentDB/databaseAccounts | CassandraRequestCharges | Cassandra 요청 요금 | 개수 | 합계
아니요 | Microsoft.DocumentDB/databaseAccounts | CassandraRequests | Cassandra 요청 | 개수 | 개수
아니요 | Microsoft.DocumentDB/databaseAccounts | DataUsage | 데이터 사용량 | 바이트 | 합계
예 | Microsoft.DocumentDB/databaseAccounts | DeleteVirtualNetwork | DeleteVirtualNetwork | 개수 | 개수
아니요 | Microsoft.DocumentDB/databaseAccounts | DocumentCount | 문서 수 | 개수 | 합계
아니요 | Microsoft.DocumentDB/databaseAccounts | DocumentQuota | 문서 할당량 | 바이트 | 합계
아니요 | Microsoft.DocumentDB/databaseAccounts | IndexUsage | 인덱스 사용량 | 바이트 | 합계
아니요 | Microsoft.DocumentDB/databaseAccounts | MetadataRequests | 메타데이터 요청 | 개수 | 개수
예 | Microsoft.DocumentDB/databaseAccounts | MongoRequestCharge | Mongo 요청 요금 | 개수 | 합계
예 | Microsoft.DocumentDB/databaseAccounts | MongoRequests | Mongo 요청 | 개수 | 개수
아니요 | Microsoft.DocumentDB/databaseAccounts | MongoRequestsCount | Mongo 요청 율 | 초당 개수 | 평균
아니요 | Microsoft.DocumentDB/databaseAccounts | MongoRequestsDelete | Mongo 삭제 요청 빈도 | 초당 개수 | 평균
아니요 | Microsoft.DocumentDB/databaseAccounts | MongoRequestsInsert | Mongo 삽입 요청 빈도 | 초당 개수 | 평균
아니요 | Microsoft.DocumentDB/databaseAccounts | MongoRequestsQuery | Mongo 쿼리 요청 빈도 | 초당 개수 | 평균
아니요 | Microsoft.DocumentDB/databaseAccounts | MongoRequestsUpdate | Mongo 업데이트 요청 률 | 초당 개수 | 평균
아니요 | Microsoft.DocumentDB/databaseAccounts | ProvisionedThroughput | 프로비전된 처리량 | 개수 | 최대
예 | Microsoft.DocumentDB/databaseAccounts | ReplicationLatency | P 99 복제 대기 시간 | 밀리초 | 평균
아니요 | Microsoft.DocumentDB/databaseAccounts | ServiceAvailability | 서비스 가용성 | % | 평균
예 | Microsoft.DocumentDB/databaseAccounts | TotalRequests | 총 요청 수 | 개수 | 개수
예 | Microsoft.DocumentDB/databaseAccounts | TotalRequestUnits | 총 요청 단위 | 개수 | 합계
아니요 | EnterpriseKnowledgeGraph/서비스 | FailureCount | 실패 수 | 개수 | 개수
아니요 | EnterpriseKnowledgeGraph/서비스 | SuccessCount | 성공 수 | 개수 | 개수
아니요 | EnterpriseKnowledgeGraph/서비스 | SuccessLatency | 성공 대기 시간 | 밀리초 | 평균
아니요 | EnterpriseKnowledgeGraph/서비스 | TransactionCount | 트랜잭션 수 | 개수 | 개수
예 | Microsoft.EventGrid/domains | DeadLetteredCount | 배달 못한 편지 이벤트 | 개수 | 합계
아니요 | Microsoft.EventGrid/domains | DeliveryAttemptFailCount | 배달 실패 이벤트 | 개수 | 합계
예 | Microsoft.EventGrid/domains | DeliverySuccessCount | 배달된 이벤트 | 개수 | 합계
아니요 | Microsoft.EventGrid/domains | DestinationProcessingDurationInMs | 대상 처리 기간 | 밀리초 | 평균
예 | Microsoft.EventGrid/domains | DroppedEventCount | 삭제된 이벤트 | 개수 | 합계
예 | Microsoft.EventGrid/domains | MatchedEventCount | 일치하는 이벤트 | 개수 | 합계
예 | Microsoft.EventGrid/domains | PublishFailCount | 실패 한 이벤트 게시 | 개수 | 합계
예 | Microsoft.EventGrid/domains | PublishSuccessCount | 게시된 이벤트 | 개수 | 합계
예 | Microsoft.EventGrid/domains | PublishSuccessLatencyInMs | 게시 성공 대기 시간 | 개수 | 합계
예 | Microsoft.EventGrid/eventSubscriptions | DeadLetteredCount | 배달 못한 편지 이벤트 | 개수 | 합계
아니요 | Microsoft.EventGrid/eventSubscriptions | DeliveryAttemptFailCount | 배달 실패 이벤트 | 개수 | 합계
예 | Microsoft.EventGrid/eventSubscriptions | DeliverySuccessCount | 배달된 이벤트 | 개수 | 합계
아니요 | Microsoft.EventGrid/eventSubscriptions | DestinationProcessingDurationInMs | 대상 처리 기간 | 밀리초 | 평균
예 | Microsoft.EventGrid/eventSubscriptions | DroppedEventCount | 삭제된 이벤트 | 개수 | 합계
예 | Microsoft.EventGrid/eventSubscriptions | MatchedEventCount | 일치하는 이벤트 | 개수 | 합계
예 | Microsoft.EventGrid/extensionTopics | PublishFailCount | 실패 한 이벤트 게시 | 개수 | 합계
예 | Microsoft.EventGrid/extensionTopics | PublishSuccessCount | 게시된 이벤트 | 개수 | 합계
예 | Microsoft.EventGrid/extensionTopics | PublishSuccessLatencyInMs | 게시 성공 대기 시간 | 개수 | 합계
예 | Microsoft.EventGrid/extensionTopics | UnmatchedEventCount | 일치하지 않는 이벤트 | 개수 | 합계
예 | Microsoft.EventGrid/topics | PublishFailCount | 실패 한 이벤트 게시 | 개수 | 합계
예 | Microsoft.EventGrid/topics | PublishSuccessCount | 게시된 이벤트 | 개수 | 합계
예 | Microsoft.EventGrid/topics | PublishSuccessLatencyInMs | 게시 성공 대기 시간 | 개수 | 합계
예 | Microsoft.EventGrid/topics | UnmatchedEventCount | 일치하지 않는 이벤트 | 개수 | 합계
아니요 | Microsoft.EventHub/clusters | ActiveConnections | ActiveConnections | 개수 | 평균
아니요 | Microsoft.EventHub/clusters | AvailableMemory | 사용 가능한 메모리 | % | 최대
아니요 | Microsoft.EventHub/clusters | CaptureBacklog | 캡처 백로그. | 개수 | 합계
아니요 | Microsoft.EventHub/clusters | CapturedBytes | 캡처된 바이트. | 바이트 | 합계
아니요 | Microsoft.EventHub/clusters | CapturedMessages | 캡처된 메시지. | 개수 | 합계
아니요 | Microsoft.EventHub/clusters | ConnectionsClosed | 끊어진 연결. | 개수 | 평균
아니요 | Microsoft.EventHub/clusters | ConnectionsOpened | 열린 연결. | 개수 | 평균
아니요 | Microsoft.EventHub/clusters | CPU | CPU | % | 최대
예 | Microsoft.EventHub/clusters | IncomingBytes | 들어오는 바이트 | 바이트 | 합계
예 | Microsoft.EventHub/clusters | IncomingMessages | 들어오는 메시지 | 개수 | 합계
예 | Microsoft.EventHub/clusters | IncomingRequests | 들어오는 요청 | 개수 | 합계
예 | Microsoft.EventHub/clusters | OutgoingBytes | 보내는 바이트 | 바이트 | 합계
예 | Microsoft.EventHub/clusters | OutgoingMessages | 보내는 메시지 | 개수 | 합계
아니요 | Microsoft.EventHub/clusters | QuotaExceededErrors | 할당량 초과 오류. | 개수 | 합계
아니요 | Microsoft.EventHub/clusters | ServerErrors | 서버 오류. | 개수 | 합계
아니요 | Microsoft.EventHub/clusters | SuccessfulRequests | 성공한 요청 | 개수 | 합계
아니요 | Microsoft.EventHub/clusters | ThrottledRequests | 제한된 요청. | 개수 | 합계
아니요 | Microsoft.EventHub/clusters | UserErrors | 사용자 오류. | 개수 | 합계
아니요 | Microsoft.EventHub/namespaces | ActiveConnections | ActiveConnections | 개수 | 평균
아니요 | Microsoft.EventHub/namespaces | CaptureBacklog | 캡처 백로그. | 개수 | 합계
아니요 | Microsoft.EventHub/namespaces | CapturedBytes | 캡처된 바이트. | 바이트 | 합계
아니요 | Microsoft.EventHub/namespaces | CapturedMessages | 캡처된 메시지. | 개수 | 합계
아니요 | Microsoft.EventHub/namespaces | ConnectionsClosed | 끊어진 연결. | 개수 | 평균
아니요 | Microsoft.EventHub/namespaces | ConnectionsOpened | 열린 연결. | 개수 | 평균
예 | Microsoft.EventHub/namespaces | EHABL | 백로그 메시지 보관 (사용 되지 않음) | 개수 | 합계
예 | Microsoft.EventHub/namespaces | EHAMBS | 보관 메시지 처리량 (사용 되지 않음) | 바이트 | 합계
예 | Microsoft.EventHub/namespaces | EHAMSGS | 보관 메시지 (사용 되지 않음) | 개수 | 합계
예 | Microsoft.EventHub/namespaces | EHINBYTES | 들어오는 바이트(사용되지 않음) | 바이트 | 합계
예 | Microsoft.EventHub/namespaces | EHINMBS | 들어오는 바이트 (사용 되지 않음) (사용 되지 않음) | 바이트 | 합계
예 | Microsoft.EventHub/namespaces | EHINMSGS | 들어오는 메시지(사용되지 않음) | 개수 | 합계
예 | Microsoft.EventHub/namespaces | EHOUTBYTES | 보내는 바이트(사용되지 않음) | 바이트 | 합계
예 | Microsoft.EventHub/namespaces | EHOUTMBS | 보내는 바이트 (사용 되지 않음) (사용 되지 않음) | 바이트 | 합계
예 | Microsoft.EventHub/namespaces | EHOUTMSGS | 보내는 메시지(사용되지 않음) | 개수 | 합계
예 | Microsoft.EventHub/namespaces | FAILREQ | 실패 한 요청 (사용 되지 않음) | 개수 | 합계
예 | Microsoft.EventHub/namespaces | IncomingBytes | 들어오는 바이트 | 바이트 | 합계
예 | Microsoft.EventHub/namespaces | IncomingMessages | 들어오는 메시지 | 개수 | 합계
예 | Microsoft.EventHub/namespaces | IncomingRequests | 들어오는 요청 | 개수 | 합계
예 | Microsoft.EventHub/namespaces | INMSGS | 들어오는 메시지 (사용 되지 않음) (사용 되지 않음) | 개수 | 합계
예 | Microsoft.EventHub/namespaces | INREQS | 들어오는 요청 (사용 되지 않음) | 개수 | 합계
예 | Microsoft.EventHub/namespaces | INTERR | 내부 서버 오류 (사용 되지 않음) | 개수 | 합계
예 | Microsoft.EventHub/namespaces | MISCERR | 기타 오류 (사용 되지 않음) | 개수 | 합계
예 | Microsoft.EventHub/namespaces | OutgoingBytes | 보내는 바이트 | 바이트 | 합계
예 | Microsoft.EventHub/namespaces | OutgoingMessages | 보내는 메시지 | 개수 | 합계
예 | Microsoft.EventHub/namespaces | OUTMSGS | 보내는 메시지 (사용 되지 않음) (사용 되지 않음) | 개수 | 합계
아니요 | Microsoft.EventHub/namespaces | QuotaExceededErrors | 할당량 초과 오류. | 개수 | 합계
아니요 | Microsoft.EventHub/namespaces | ServerErrors | 서버 오류. | 개수 | 합계
아니요 | Microsoft.EventHub/namespaces | 크기 | 크기 | 바이트 | 평균
아니요 | Microsoft.EventHub/namespaces | SuccessfulRequests | 성공한 요청 | 개수 | 합계
예 | Microsoft.EventHub/namespaces | SUCCREQ | 성공한 요청 (사용 되지 않음) | 개수 | 합계
예 | Microsoft.EventHub/namespaces | SVRBSY | 서버 작업 중 오류 (사용 되지 않음) | 개수 | 합계
아니요 | Microsoft.EventHub/namespaces | ThrottledRequests | 제한된 요청. | 개수 | 합계
아니요 | Microsoft.EventHub/namespaces | UserErrors | 사용자 오류. | 개수 | 합계
예 | Microsoft.HDInsight/clusters | CategorizedGatewayRequests | 분류된 게이트웨이 요청 | 개수 | 합계
예 | Microsoft.HDInsight/clusters | GatewayRequests | 게이트웨이 요청 | 개수 | 합계
예 | Microsoft.HDInsight/clusters | NumActiveWorkers | 활성 작업자 수 | 개수 | 최대
예 | Microsoft.HDInsight/clusters | ScalingRequests | 크기 조정 요청 | 개수 | 최대
예 | Microsoft.Insights/AutoscaleSettings | MetricThreshold | 메트릭 임계값 | 개수 | 평균
예 | Microsoft.Insights/AutoscaleSettings | ObservedCapacity | 관찰된 용량 | 개수 | 평균
예 | Microsoft.Insights/AutoscaleSettings | ObservedMetricValue | 관찰된 메트릭 값 | 개수 | 평균
예 | Microsoft.Insights/AutoscaleSettings | ScaleActionsInitiated | 시작된 크기 조정 작업 | 개수 | 합계
예 | Microsoft.Insights/Components | availabilityResults/availabilityPercentage | 가용성 | % | 평균
아니요 | Microsoft.Insights/Components | availabilityResults/count | 가용성 테스트 | 개수 | 개수
예 | Microsoft.Insights/Components | availabilityResults/duration | 가용성 테스트 기간 | 밀리초 | 평균
예 | Microsoft.Insights/Components | browserTimings/networkDuration | 페이지 로드 네트워크 연결 시간 | 밀리초 | 평균
예 | Microsoft.Insights/Components | browserTimings/processingDuration | 클라이언트 처리 시간 | 밀리초 | 평균
예 | Microsoft.Insights/Components | browserTimings/receiveDuration | 응답 수신 시간 | 밀리초 | 평균
예 | Microsoft.Insights/Components | browserTimings/sendDuration | 요청 전송 시간 | 밀리초 | 평균
예 | Microsoft.Insights/Components | browserTimings/totalDuration | 브라우저 페이지 로드 시간 | 밀리초 | 평균
아니요 | Microsoft.Insights/Components | dependencies/count | 종속성 호출 | 개수 | 개수
예 | Microsoft.Insights/Components | dependencies/duration | 종속성 기간 | 밀리초 | 평균
아니요 | Microsoft.Insights/Components | dependencies/failed | 종속성 호출 오류 | 개수 | 개수
아니요 | Microsoft.Insights/Components | exceptions/browser | 브라우저 예외 | 개수 | 개수
예 | Microsoft.Insights/Components | exceptions/count | 예외 | 개수 | 개수
아니요 | Microsoft.Insights/Components | exceptions/server | 서버 예외 | 개수 | 개수
예 | Microsoft.Insights/Components | pageViews/count | 페이지 보기 | 개수 | 개수
예 | Microsoft.Insights/Components | pageViews/duration | 페이지 보기 로드 시간 | 밀리초 | 평균
예 | Microsoft.Insights/Components | performanceCounters/exceptionsPerSecond | 예외 속도 | 초당 개수 | 평균
예 | Microsoft.Insights/Components | performanceCounters/memoryAvailableBytes | 사용 가능한 메모리 | 바이트 | 평균
예 | Microsoft.Insights/Components | performanceCounters/processCpuPercentage | CPU 프로세스 | % | 평균
예 | Microsoft.Insights/Components | performanceCounters/processIOBytesPerSecond | 프로세스 IO 속도 | 초당 바이트 수 | 평균
예 | Microsoft.Insights/Components | performanceCounters/processorCpuPercentage | 프로세서 시간 | % | 평균
예 | Microsoft.Insights/Components | performanceCounters/processPrivateBytes | 프로세스 프라이빗 바이트 | 바이트 | 평균
예 | Microsoft.Insights/Components | performanceCounters/requestExecutionTime | HTTP 요청 실행 시간 | 밀리초 | 평균
예 | Microsoft.Insights/Components | performanceCounters/requestsInQueue | 애플리케이션 큐의 HTTP 요청 | 개수 | 평균
예 | Microsoft.Insights/Components | performanceCounters/requestsPerSecond | HTTP 요청 속도 | 초당 개수 | 평균
아니요 | Microsoft.Insights/Components | requests/count | 서버 요청 | 개수 | 개수
예 | Microsoft.Insights/Components | requests/duration | 서버 응답 시간 | 밀리초 | 평균
아니요 | Microsoft.Insights/Components | requests/failed | 실패한 요청 | 개수 | 개수
아니요 | Microsoft.Insights/Components | requests/rate | 서버 요청 빈도 | 초당 개수 | 평균
예 | Microsoft.Insights/Components | traces/count | Traces | 개수 | 개수
예 | Microsoft.KeyVault/vaults | ServiceApiHit | Service API 총 방문 횟수 | 개수 | 개수
예 | Microsoft.KeyVault/vaults | ServiceApiLatency | 전체 Service API 대기 시간 | 밀리초 | 평균
예 | Microsoft.KeyVault/vaults | ServiceApiResult | Service API 총 결과 | 개수 | 개수
예 | Microsoft.Kusto/Clusters | CacheUtilization | 캐시 사용률 | % | 평균
예 | Microsoft.Kusto/Clusters | ContinuousExportMaxLatenessMinutes | 연속 내보내기 최대 지연 시간 (분) | 개수 | 최대
예 | Microsoft.Kusto/Clusters | ContinuousExportNumOfRecordsExported | 연속 내보내기-내보낸 레코드의 개수 | 개수 | 합계
예 | Microsoft.Kusto/Clusters | ContinuousExportPendingCount | 연속 내보내기 보류 중 수 | 개수 | 최대
예 | Microsoft.Kusto/Clusters | ContinuousExportResult | 연속 내보내기 결과 | 개수 | 개수
예 | Microsoft.Kusto/Clusters | CPU | CPU | % | 평균
예 | Microsoft.Kusto/Clusters | EventsProcessedForEventHubs | 처리 된 이벤트 (이벤트/IoT 허브의 경우) | 개수 | 합계
예 | Microsoft.Kusto/Clusters | ExportUtilization | 사용률 내보내기 | % | 최대
예 | Microsoft.Kusto/Clusters | IngestionLatencyInSeconds | 수집 대기 시간 (초) | 초 | 평균
예 | Microsoft.Kusto/Clusters | IngestionResult | 수집 결과 | 개수 | 개수
예 | Microsoft.Kusto/Clusters | IngestionUtilization | 수집 사용률 | % | 평균
예 | Microsoft.Kusto/Clusters | IngestionVolumeInMB | 수집 볼륨 (MB) | 개수 | 합계
예 | Microsoft.Kusto/Clusters | KeepAlive | 연결 유지 | 개수 | 평균
예 | Microsoft.Kusto/Clusters | QueryDuration | 쿼리 기간 | 밀리초 | 평균
예 | Microsoft.Kusto/Clusters | SteamingIngestRequestRate | 스트리밍 수집 요청 빈도 | 개수 | RateRequestsPerSecond
예 | Microsoft.Kusto/Clusters | StreamingIngestDataRate | 스트리밍 수집 데이터 전송률 | 개수 | 평균
예 | Microsoft.Kusto/Clusters | StreamingIngestDuration | 스트리밍 수집 기간 | 밀리초 | 평균
예 | Microsoft.Kusto/Clusters | StreamingIngestResults | 스트리밍 수집 결과 | 개수 | 평균
예 | Microsoft.Logic/integrationServiceEnvironments | ActionLatency | 작업 대기 시간  | 초 | 평균
예 | Microsoft.Logic/integrationServiceEnvironments | ActionsCompleted | 작업 완료됨  | 개수 | 합계
예 | Microsoft.Logic/integrationServiceEnvironments | ActionsFailed | 작업 실패함  | 개수 | 합계
예 | Microsoft.Logic/integrationServiceEnvironments | ActionsSkipped | 작업 생략됨  | 개수 | 합계
예 | Microsoft.Logic/integrationServiceEnvironments | ActionsStarted | 작업 시작됨  | 개수 | 합계
예 | Microsoft.Logic/integrationServiceEnvironments | ActionsSucceeded | 작업 성공함  | 개수 | 합계
예 | Microsoft.Logic/integrationServiceEnvironments | ActionSuccessLatency | 작업 성공 대기 시간  | 초 | 평균
예 | Microsoft.Logic/integrationServiceEnvironments | ActionThrottledEvents | 작업 제한 이벤트 | 개수 | 합계
예 | Microsoft.Logic/integrationServiceEnvironments | IntegrationServiceEnvironmentConnectorMemoryUsage | 통합 서비스 환경에 대 한 커넥터 메모리 사용량 | % | 평균
예 | Microsoft.Logic/integrationServiceEnvironments | IntegrationServiceEnvironmentConnectorProcessorUsage | 통합 서비스 환경에 대 한 커넥터 프로세서 사용량 | % | 평균
예 | Microsoft.Logic/integrationServiceEnvironments | IntegrationServiceEnvironmentWorkflowMemoryUsage | 통합 서비스 환경에 대 한 워크플로 메모리 사용 | % | 평균
예 | Microsoft.Logic/integrationServiceEnvironments | IntegrationServiceEnvironmentWorkflowProcessorUsage | 통합 서비스 환경에 대 한 워크플로 프로세서 사용 | % | 평균
예 | Microsoft.Logic/integrationServiceEnvironments | RunFailurePercentage | 실행 오류 비율 | % | 합계
예 | Microsoft.Logic/integrationServiceEnvironments | RunLatency | 실행 대기 시간 | 초 | 평균
예 | Microsoft.Logic/integrationServiceEnvironments | RunsCancelled | 실행 취소 됨 | 개수 | 합계
예 | Microsoft.Logic/integrationServiceEnvironments | RunsCompleted | 실행 완료됨 | 개수 | 합계
예 | Microsoft.Logic/integrationServiceEnvironments | RunsFailed | 실행 실패함 | 개수 | 합계
예 | Microsoft.Logic/integrationServiceEnvironments | RunsStarted | 실행 시작됨 | 개수 | 합계
예 | Microsoft.Logic/integrationServiceEnvironments | RunsSucceeded | 실행 성공함 | 개수 | 합계
예 | Microsoft.Logic/integrationServiceEnvironments | RunStartThrottledEvents | 시작 제한 이벤트 실행 | 개수 | 합계
예 | Microsoft.Logic/integrationServiceEnvironments | RunSuccessLatency | 실행 성공 대기 시간 | 초 | 평균
예 | Microsoft.Logic/integrationServiceEnvironments | RunThrottledEvents | 실행 제한 이벤트 | 개수 | 합계
예 | Microsoft.Logic/integrationServiceEnvironments | TriggerFireLatency | 트리거 실행 대기 시간  | 초 | 평균
예 | Microsoft.Logic/integrationServiceEnvironments | TriggerLatency | 트리거 대기 시간  | 초 | 평균
예 | Microsoft.Logic/integrationServiceEnvironments | TriggersCompleted | 트리거 완료됨  | 개수 | 합계
예 | Microsoft.Logic/integrationServiceEnvironments | TriggersFailed | 트리거 실패함  | 개수 | 합계
예 | Microsoft.Logic/integrationServiceEnvironments | TriggersFired | 트리거 실행됨  | 개수 | 합계
예 | Microsoft.Logic/integrationServiceEnvironments | TriggersSkipped | 트리거 생략함 | 개수 | 합계
예 | Microsoft.Logic/integrationServiceEnvironments | TriggersStarted | 트리거 시작됨  | 개수 | 합계
예 | Microsoft.Logic/integrationServiceEnvironments | TriggersSucceeded | 트리거 성공함  | 개수 | 합계
예 | Microsoft.Logic/integrationServiceEnvironments | TriggerSuccessLatency | 트리거 성공 대기 시간  | 초 | 평균
예 | Microsoft.Logic/integrationServiceEnvironments | TriggerThrottledEvents | 트리거 제한 이벤트 | 개수 | 합계
예 | Microsoft.Logic/workflows | ActionLatency | 작업 대기 시간  | 초 | 평균
예 | Microsoft.Logic/workflows | ActionsCompleted | 작업 완료됨  | 개수 | 합계
예 | Microsoft.Logic/workflows | ActionsFailed | 작업 실패함  | 개수 | 합계
예 | Microsoft.Logic/workflows | ActionsSkipped | 작업 생략됨  | 개수 | 합계
예 | Microsoft.Logic/workflows | ActionsStarted | 작업 시작됨  | 개수 | 합계
예 | Microsoft.Logic/workflows | ActionsSucceeded | 작업 성공함  | 개수 | 합계
예 | Microsoft.Logic/workflows | ActionSuccessLatency | 작업 성공 대기 시간  | 초 | 평균
예 | Microsoft.Logic/workflows | ActionThrottledEvents | 작업 제한 이벤트 | 개수 | 합계
예 | Microsoft.Logic/workflows | BillableActionExecutions | 청구 가능한 작업 실행 | 개수 | 합계
예 | Microsoft.Logic/workflows | BillableTriggerExecutions | 청구 가능한 트리거 실행 | 개수 | 합계
예 | Microsoft.Logic/workflows | BillingUsageNativeOperation | 기본 작업 실행에 대한 청구 사용량 | 개수 | 합계
예 | Microsoft.Logic/workflows | BillingUsageNativeOperation | 기본 작업 실행에 대한 청구 사용량 | 개수 | 합계
예 | Microsoft.Logic/workflows | BillingUsageStandardConnector | 표준 커넥터 실행에 대한 청구 사용량 | 개수 | 합계
예 | Microsoft.Logic/workflows | BillingUsageStandardConnector | 표준 커넥터 실행에 대한 청구 사용량 | 개수 | 합계
예 | Microsoft.Logic/workflows | BillingUsageStorageConsumption | 스토리지 소비 실행에 대한 청구 사용량 | 개수 | 합계
예 | Microsoft.Logic/workflows | BillingUsageStorageConsumption | 스토리지 소비 실행에 대한 청구 사용량 | 개수 | 합계
예 | Microsoft.Logic/workflows | RunFailurePercentage | 실행 오류 비율 | % | 합계
예 | Microsoft.Logic/workflows | RunLatency | 실행 대기 시간 | 초 | 평균
예 | Microsoft.Logic/workflows | RunsCancelled | 실행 취소 됨 | 개수 | 합계
예 | Microsoft.Logic/workflows | RunsCompleted | 실행 완료됨 | 개수 | 합계
예 | Microsoft.Logic/workflows | RunsFailed | 실행 실패함 | 개수 | 합계
예 | Microsoft.Logic/workflows | RunsStarted | 실행 시작됨 | 개수 | 합계
예 | Microsoft.Logic/workflows | RunsSucceeded | 실행 성공함 | 개수 | 합계
예 | Microsoft.Logic/workflows | RunStartThrottledEvents | 시작 제한 이벤트 실행 | 개수 | 합계
예 | Microsoft.Logic/workflows | RunSuccessLatency | 실행 성공 대기 시간 | 초 | 평균
예 | Microsoft.Logic/workflows | RunThrottledEvents | 실행 제한 이벤트 | 개수 | 합계
예 | Microsoft.Logic/workflows | TotalBillableExecutions | 총 청구 가능 실행 | 개수 | 합계
예 | Microsoft.Logic/workflows | TriggerFireLatency | 트리거 실행 대기 시간  | 초 | 평균
예 | Microsoft.Logic/workflows | TriggerLatency | 트리거 대기 시간  | 초 | 평균
예 | Microsoft.Logic/workflows | TriggersCompleted | 트리거 완료됨  | 개수 | 합계
예 | Microsoft.Logic/workflows | TriggersFailed | 트리거 실패함  | 개수 | 합계
예 | Microsoft.Logic/workflows | TriggersFired | 트리거 실행됨  | 개수 | 합계
예 | Microsoft.Logic/workflows | TriggersSkipped | 트리거 생략함 | 개수 | 합계
예 | Microsoft.Logic/workflows | TriggersStarted | 트리거 시작됨  | 개수 | 합계
예 | Microsoft.Logic/workflows | TriggersSucceeded | 트리거 성공함  | 개수 | 합계
예 | Microsoft.Logic/workflows | TriggerSuccessLatency | 트리거 성공 대기 시간  | 초 | 평균
예 | Microsoft.Logic/workflows | TriggerThrottledEvents | 트리거 제한 이벤트 | 개수 | 합계
예 | Microsoft.MachineLearningServices/workspaces | 활성 코어 | 활성 코어 | 개수 | 평균
예 | Microsoft.MachineLearningServices/workspaces | 활성 노드 | 활성 노드 | 개수 | 평균
예 | Microsoft.MachineLearningServices/workspaces | 완료 된 실행 | 완료 된 실행 | 개수 | 합계
예 | Microsoft.MachineLearningServices/workspaces | 실패한 실행 | 실패한 실행 | 개수 | 합계
예 | Microsoft.MachineLearningServices/workspaces | 유휴 코어 | 유휴 코어 | 개수 | 평균
예 | Microsoft.MachineLearningServices/workspaces | 유휴 노드 | 유휴 노드 | 개수 | 평균
예 | Microsoft.MachineLearningServices/workspaces | 코어 탈퇴 | 코어 탈퇴 | 개수 | 평균
예 | Microsoft.MachineLearningServices/workspaces | 노드 종료 | 노드 종료 | 개수 | 평균
예 | Microsoft.MachineLearningServices/workspaces | 모델 배포 실패 | 모델 배포 실패 | 개수 | 합계
예 | Microsoft.MachineLearningServices/workspaces | 모델 배포 시작 됨 | 모델 배포 시작 됨 | 개수 | 합계
예 | Microsoft.MachineLearningServices/workspaces | 모델 배포 성공 | 모델 배포 성공 | 개수 | 합계
예 | Microsoft.MachineLearningServices/workspaces | 모델 레지스터가 실패 했습니다. | 모델 레지스터가 실패 했습니다. | 개수 | 합계
예 | Microsoft.MachineLearningServices/workspaces | 모델 등록 성공 | 모델 등록 성공 | 개수 | 합계
예 | Microsoft.MachineLearningServices/workspaces | 선점 코어 | 선점 코어 | 개수 | 평균
예 | Microsoft.MachineLearningServices/workspaces | 선점 노드 | 선점 노드 | 개수 | 평균
예 | Microsoft.MachineLearningServices/workspaces | 할당량 사용률 비율 | 할당량 사용률 비율 | 개수 | 평균
예 | Microsoft.MachineLearningServices/workspaces | 실행 시작 | 실행 시작 | 개수 | 합계
예 | Microsoft.MachineLearningServices/workspaces | 총 코어 | 총 코어 | 개수 | 평균
예 | Microsoft.MachineLearningServices/workspaces | 총 노드 | 총 노드 | 개수 | 평균
예 | Microsoft.MachineLearningServices/workspaces | 사용할 수 없는 코어 | 사용할 수 없는 코어 | 개수 | 평균
예 | Microsoft.MachineLearningServices/workspaces | 사용할 수 없는 노드 | 사용할 수 없는 노드 | 개수 | 평균
예 | Microsoft.Maps/accounts | 가용성 | 가용성 | % | 평균
아니요 | Microsoft.Maps/accounts | 사용법 | 사용법 | 개수 | 개수
예 | Microsoft. Media/windowsazure.mediaservices | AssetCount | 자산 수 | 개수 | 평균
예 | Microsoft. Media/windowsazure.mediaservices | AssetQuota | 자산 할당량 | 개수 | 평균
예 | Microsoft. Media/windowsazure.mediaservices | AssetQuotaUsedPercentage | 사용 되는 자산 할당량 백분율 | % | 평균
예 | Microsoft. Media/windowsazure.mediaservices | ContentKeyPolicyCount | 콘텐츠 키 정책 수 | 개수 | 평균
예 | Microsoft. Media/windowsazure.mediaservices | ContentKeyPolicyQuota | 콘텐츠 키 정책 할당량 | 개수 | 평균
예 | Microsoft. Media/windowsazure.mediaservices | ContentKeyPolicyQuotaUsedPercentage | 콘텐츠 키 정책 할당량 사용 백분율 | % | 평균
예 | Microsoft. Media/windowsazure.mediaservices | StreamingPolicyCount | 스트리밍 정책 수 | 개수 | 평균
예 | Microsoft. Media/windowsazure.mediaservices | StreamingPolicyQuota | 스트리밍 정책 할당량 | 개수 | 평균
예 | Microsoft. Media/windowsazure.mediaservices | StreamingPolicyQuotaUsedPercentage | 스트리밍 정책 할당량 사용 백분율 | % | 평균
예 | Microsoft. Media/windowsazure.mediaservices/streamingEndpoints | 송신 | 송신 | 바이트 | 합계
예 | Microsoft. Media/windowsazure.mediaservices/streamingEndpoints | 요청 | 요청 | 개수 | 합계
예 | Microsoft. Media/windowsazure.mediaservices/streamingEndpoints | SuccessE2ELatency | 성공 종단 간 대기 시간 | 밀리초 | 평균
예 | Microservices4Spring/appClusters | GCPauseTotalCount | GC 일시 중지 횟수 | 개수 | 합계
예 | Microservices4Spring/appClusters | GCPauseTotalTime | GC 일시 중지 총 시간 | 밀리초 | 합계
예 | Microservices4Spring/appClusters | MaxOldGenMemoryPoolBytes | 사용 가능한 최대 이전 생성 데이터 크기 | 바이트 | 평균
예 | Microservices4Spring/appClusters | OldGenMemoryPoolBytes | 이전 세대 데이터 크기 | 바이트 | 평균
예 | Microservices4Spring/appClusters | OldGenPromotedBytes | 이전 세대 데이터 크기로 수준 올리기 | 바이트 | 최대
예 | Microservices4Spring/appClusters | ServiceCpuUsagePercentage | 서비스 CPU 사용량 비율 | % | 평균
예 | Microservices4Spring/appClusters | ServiceMemoryCommitted | 할당 된 서비스 메모리 | 바이트 | 평균
예 | Microservices4Spring/appClusters | ServiceMemoryMax | 최대 서비스 메모리 | 바이트 | 최대
예 | Microservices4Spring/appClusters | ServiceMemoryUsed | 사용 되는 서비스 메모리 | 바이트 | 평균
예 | Microservices4Spring/appClusters | SystemCpuUsagePercentage | 시스템 CPU 사용량 (%) | % | 평균
예 | Microservices4Spring/appClusters | TomcatErrorCount | Tomcat 전역 오류 | 개수 | 합계
예 | Microservices4Spring/appClusters | TomcatReceivedBytes | 총 수신 바이트 Tomcat | 바이트 | 합계
예 | Microservices4Spring/appClusters | TomcatRequestMaxTime | Tomcat 요청 최대 시간 | 밀리초 | 최대
예 | Microservices4Spring/appClusters | TomcatRequestTotalCount | Tomcat 요청 총 수 | 개수 | 합계
예 | Microservices4Spring/appClusters | TomcatRequestTotalTime | Tomcat 요청 총 시간 | 밀리초 | 합계
예 | Microservices4Spring/appClusters | TomcatResponseAvgTime | Tomcat 요청 평균 시간 | 밀리초 | 평균
예 | Microservices4Spring/appClusters | TomcatSentBytes | 총 보낸 바이트 Tomcat | 바이트 | 합계
예 | Microservices4Spring/appClusters | TomcatSessionActiveCurrentCount | Tomcat 세션 연결 수 | 개수 | 합계
예 | Microservices4Spring/appClusters | TomcatSessionActiveMaxCount | Tomcat 세션 최대 활성 수 | 개수 | 합계
예 | Microservices4Spring/appClusters | TomcatSessionAliveMaxTime | Tomcat 세션 최대 활성 시간 | 밀리초 | 최대
예 | Microservices4Spring/appClusters | TomcatSessionCreatedCount | Tomcat 세션 생성 횟수 | 개수 | 합계
예 | Microservices4Spring/appClusters | TomcatSessionExpiredCount | Tomcat 세션 만료 수 | 개수 | 합계
예 | Microservices4Spring/appClusters | TomcatSessionRejectedCount | Tomcat 세션 거부 횟수 | 개수 | 합계
예 | Microservices4Spring/appClusters | YoungGenPromotedBytes | 젊은 생성 데이터 크기로 수준 올리기 | 바이트 | 최대
예 | Microsoft.NetApp/netAppAccounts/capacityPools | VolumePoolAllocatedUsed | 할당된 사용되는 볼륨 풀 | 바이트 | 평균
예 | Microsoft.NetApp/netAppAccounts/capacityPools | VolumePoolTotalLogicalSize | 볼륨 풀 총 논리적 크기 | 바이트 | 평균
예 | Microsoft NetApp/netAppAccounts/capacityPools/볼륨 | AverageReadLatency | 평균 읽기 대기 시간 | 밀리초 | 평균
예 | Microsoft NetApp/netAppAccounts/capacityPools/볼륨 | AverageWriteLatency | 평균 쓰기 대기 시간 | 밀리초 | 평균
예 | Microsoft NetApp/netAppAccounts/capacityPools/볼륨 | ReadIops | 읽기 IOPS | 초당 개수 | 평균
예 | Microsoft NetApp/netAppAccounts/capacityPools/볼륨 | VolumeLogicalSize | 볼륨 논리적 크기 | 바이트 | 평균
예 | Microsoft NetApp/netAppAccounts/capacityPools/볼륨 | VolumeSnapshotSize | 볼륨 스냅샷 크기 | 바이트 | 평균
예 | Microsoft NetApp/netAppAccounts/capacityPools/볼륨 | WriteIops | 쓰기 IOPS | 초당 개수 | 평균
아니요 | Microsoft.Network/applicationGateways | ApplicationGatewayTotalTime | Application Gateway 총 시간 | 밀리초 | 평균
아니요 | Microsoft.Network/applicationGateways | AvgRequestCountPerHealthyHost | 정상 호스트 당 분당 요청 수 | 개수 | 평균
아니요 | Microsoft.Network/applicationGateways | BackendConnectTime | 백 엔드 연결 시간 | 밀리초 | 평균
아니요 | Microsoft.Network/applicationGateways | BackendFirstByteResponseTime | 백 엔드 첫 번째 바이트 응답 시간 | 밀리초 | 평균
아니요 | Microsoft.Network/applicationGateways | BackendLastByteResponseTime | 백 엔드 마지막 바이트 응답 시간 | 밀리초 | 평균
예 | Microsoft.Network/applicationGateways | BackendResponseStatus | 백 엔드 응답 상태 | 개수 | 합계
예 | Microsoft.Network/applicationGateways | BlockedCount | 웹 응용 프로그램 방화벽 차단 된 요청 규칙 배포 | 개수 | 합계
예 | Microsoft.Network/applicationGateways | BlockedReqCount | 웹 응용 프로그램 방화벽 차단 된 요청 수 | 개수 | 합계
예 | Microsoft.Network/applicationGateways | BytesReceived | Bytes Received | 바이트 | 합계
예 | Microsoft.Network/applicationGateways | BytesSent | Bytes Sent | 바이트 | 합계
아니요 | Microsoft.Network/applicationGateways | CapacityUnits | 현재 용량 단위 | 개수 | 평균
아니요 | Microsoft.Network/applicationGateways | ClientRtt | 클라이언트 RTT | 밀리초 | 평균
아니요 | Microsoft.Network/applicationGateways | 이상 | 현재 계산 단위 | 개수 | 평균
예 | Microsoft.Network/applicationGateways | CurrentConnections | 현재 연결 | 개수 | 합계
예 | Microsoft.Network/applicationGateways | FailedRequests | 실패한 요청 | 개수 | 합계
예 | Microsoft.Network/applicationGateways | HealthyHostCount | 정상 호스트 수 | 개수 | 평균
예 | Microsoft.Network/applicationGateways | MatchedCount | 웹 응용 프로그램 방화벽 총 규칙 배포 | 개수 | 합계
예 | Microsoft.Network/applicationGateways | ResponseStatus | 응답 상태 | 개수 | 합계
아니요 | Microsoft.Network/applicationGateways | 처리량 | 처리량 | 초당 바이트 수 | 평균
예 | Microsoft.Network/applicationGateways | TlsProtocol | 클라이언트 TLS 프로토콜 | 개수 | 합계
예 | Microsoft.Network/applicationGateways | TotalRequests | 총 요청 수 | 개수 | 합계
예 | Microsoft.Network/applicationGateways | UnhealthyHostCount | 비정상 호스트 수 | 개수 | 평균
예 | Microsoft.Network/azurefirewalls | ApplicationRuleHit | 응용 프로그램 규칙 적중 횟수 | 개수 | 합계
예 | Microsoft.Network/azurefirewalls | DataProcessed 됨 | 처리된 데이터 | 바이트 | 합계
예 | Microsoft.Network/azurefirewalls | FirewallHealth | 방화벽 성능 상태 | % | 평균
예 | Microsoft.Network/azurefirewalls | NetworkRuleHit | 네트워크 규칙 적중 횟수 | 개수 | 합계
예 | Microsoft.Network/azurefirewalls | SNATPortUtilization | SNAT 포트 사용률 | % | 평균
예 | Microsoft.Network/connections | BitsInPerSecond | BitsInPerSecond | 초당 개수 | 평균
예 | Microsoft.Network/connections | BitsOutPerSecond | BitsOutPerSecond | 초당 개수 | 평균
예 | Microsoft.Network/dnszones | QueryVolume | 쿼리 볼륨 | 개수 | 합계
아니요 | Microsoft.Network/dnszones | RecordSetCapacityUtilization | 레코드 집합 용량 사용률 | % | 최대
예 | Microsoft.Network/dnszones | RecordSetCount | 레코드 집합 수 | 개수 | 최대
예 | Microsoft.Network/expressRouteCircuits | ArpAvailability | Arp 가용성 | % | 평균
예 | Microsoft.Network/expressRouteCircuits | BgpAvailability | Bgp 가용성 | % | 평균
아니요 | Microsoft.Network/expressRouteCircuits | BitsInPerSecond | BitsInPerSecond | 초당 개수 | 평균
아니요 | Microsoft.Network/expressRouteCircuits | BitsOutPerSecond | BitsOutPerSecond | 초당 개수 | 평균
아니요 | Microsoft.Network/expressRouteCircuits | GlobalReachBitsInPerSecond | GlobalReachBitsInPerSecond | 초당 개수 | 평균
아니요 | Microsoft.Network/expressRouteCircuits | GlobalReachBitsOutPerSecond | GlobalReachBitsOutPerSecond | 초당 개수 | 평균
아니요 | Microsoft.Network/expressRouteCircuits | QosDropBitsInPerSecond | DroppedInBitsPerSecond | 초당 개수 | 평균
아니요 | Microsoft.Network/expressRouteCircuits | QosDropBitsOutPerSecond | DroppedOutBitsPerSecond | 초당 개수 | 평균
예 | Microsoft.Network/expressRouteCircuits/peerings | BitsInPerSecond | BitsInPerSecond | 초당 개수 | 평균
예 | Microsoft.Network/expressRouteCircuits/peerings | BitsOutPerSecond | BitsOutPerSecond | 초당 개수 | 평균
아니요 | Microsoft. Network/expressRouteGateways | ErGatewayConnectionBitsInPerSecond | BitsInPerSecond | 초당 개수 | 평균
아니요 | Microsoft. Network/expressRouteGateways | ErGatewayConnectionBitsOutPerSecond | BitsOutPerSecond | 초당 개수 | 평균
예 | Microsoft. Network/expressRoutePorts | AdminState | AdminState | 개수 | 평균
예 | Microsoft. Network/expressRoutePorts | LineProtocol | LineProtocol | 개수 | 평균
예 | Microsoft. Network/expressRoutePorts | PortBitsInPerSecond | BitsInPerSecond | 초당 개수 | 평균
예 | Microsoft. Network/expressRoutePorts | PortBitsOutPerSecond | BitsOutPerSecond | 초당 개수 | 평균
예 | Microsoft. Network/expressRoutePorts | RxLightLevel | RxLightLevel | 개수 | 평균
예 | Microsoft. Network/expressRoutePorts | TxLightLevel | TxLightLevel | 개수 | 평균
예 | Microsoft.Network/frontdoors | BackendHealthPercentage | 백 엔드 상태 비율 | % | 평균
예 | Microsoft.Network/frontdoors | BackendRequestCount | 백 엔드 요청 수 | 개수 | 합계
예 | Microsoft.Network/frontdoors | BackendRequestLatency | 백 엔드 요청 대기 시간 | 밀리초 | 평균
예 | Microsoft.Network/frontdoors | BillableResponseSize | 청구 가능한 응답 크기 | 바이트 | 합계
예 | Microsoft.Network/frontdoors | RequestCount | 요청 수 | 개수 | 합계
예 | Microsoft.Network/frontdoors | RequestSize | 요청 크기 | 바이트 | 합계
예 | Microsoft.Network/frontdoors | ResponseSize | 응답 크기 | 바이트 | 합계
예 | Microsoft.Network/frontdoors | TotalLatency | 총 대기 시간 | 밀리초 | 평균
예 | Microsoft.Network/frontdoors | WebApplicationFirewallRequestCount | 웹 애플리케이션 방화벽 요청 수 | 개수 | 합계
아니요 | Microsoft.Network/loadBalancers | AllocatedSnatPorts | 할당된 SNAT 포트(미리 보기) | 개수 | 합계
예 | Microsoft.Network/loadBalancers | ByteCount | 바이트 수 | 개수 | 합계
예 | Microsoft.Network/loadBalancers | DipAvailability | 상태 프로브 상태 | 개수 | 평균
예 | Microsoft.Network/loadBalancers | PacketCount | 패킷 수 | 개수 | 합계
예 | Microsoft.Network/loadBalancers | SnatConnectionCount | SNAT 연결 수 | 개수 | 합계
예 | Microsoft.Network/loadBalancers | SYNCount | SYN 수 | 개수 | 합계
아니요 | Microsoft.Network/loadBalancers | UsedSnatPorts | 사용된 SNAT 포트(미리 보기) | 개수 | 합계
예 | Microsoft.Network/loadBalancers | VipAvailability | 데이터 경로 가용성 | 개수 | 평균
예 | Microsoft.Network/networkInterfaces | BytesReceivedRate | Bytes Received | 바이트 | 합계
예 | Microsoft.Network/networkInterfaces | BytesSentRate | Bytes Sent | 바이트 | 합계
예 | Microsoft.Network/networkInterfaces | PacketsReceivedRate | 수신된 패킷 | 개수 | 합계
예 | Microsoft.Network/networkInterfaces | PacketsSentRate | 보낸 패킷 | 개수 | 합계
예 | Microsoft.Network/networkWatchers/connectionMonitors | AverageRoundtripMs | 평균 왕복 시간 (밀리초) | 밀리초 | 평균
예 | Microsoft.Network/networkWatchers/connectionMonitors | ChecksFailedPercent | 실패 한 비율 검사 (미리 보기) | % | 평균
예 | Microsoft.Network/networkWatchers/connectionMonitors | ProbesFailedPercent | 실패한 프로브 % | % | 평균
예 | Microsoft.Network/networkWatchers/connectionMonitors | RoundTripTimeMs | 왕복 시간 (ms) (미리 보기) | 밀리초 | 평균
예 | Microsoft.Network/publicIPAddresses | ByteCount | 바이트 수 | 개수 | 합계
예 | Microsoft.Network/publicIPAddresses | BytesDroppedDDoS | 인바운드 바이트가 삭제된 DDoS | 초당 바이트 수 | 최대
예 | Microsoft.Network/publicIPAddresses | BytesForwardedDDoS | 인바운드 바이트가 전달된 DDoS | 초당 바이트 수 | 최대
예 | Microsoft.Network/publicIPAddresses | BytesInDDoS | 인바운드 바이트 DDoS | 초당 바이트 수 | 최대
예 | Microsoft.Network/publicIPAddresses | DDoSTriggerSYNPackets | DDoS 완화를 트리거하는 인바운드 SYN 패킷 | 초당 개수 | 최대
예 | Microsoft.Network/publicIPAddresses | DDoSTriggerTCPPackets | DDoS 완화를 트리거하는 인바운드 TCP 패킷 | 초당 개수 | 최대
예 | Microsoft.Network/publicIPAddresses | DDoSTriggerUDPPackets | DDoS 완화를 트리거하는 인바운드 UDP 패킷 | 초당 개수 | 최대
예 | Microsoft.Network/publicIPAddresses | IfUnderDDoSAttack | DDoS 공격 진행 여부 | 개수 | 최대
예 | Microsoft.Network/publicIPAddresses | PacketCount | 패킷 수 | 개수 | 합계
예 | Microsoft.Network/publicIPAddresses | PacketsDroppedDDoS | 인바운드 패킷이 삭제된 DDoS | 초당 개수 | 최대
예 | Microsoft.Network/publicIPAddresses | PacketsForwardedDDoS | 인바운드 패킷이 전달된 DDoS | 초당 개수 | 최대
예 | Microsoft.Network/publicIPAddresses | PacketsInDDoS | 인바운드 패킷 DDoS | 초당 개수 | 최대
예 | Microsoft.Network/publicIPAddresses | SynCount | SYN 수 | 개수 | 합계
예 | Microsoft.Network/publicIPAddresses | TCPBytesDroppedDDoS | 인바운드 TCP 바이트가 삭제된 DDoS | 초당 바이트 수 | 최대
예 | Microsoft.Network/publicIPAddresses | TCPBytesForwardedDDoS | 인바운드 TCP 바이트가 전달된 DDoS | 초당 바이트 수 | 최대
예 | Microsoft.Network/publicIPAddresses | TCPBytesInDDoS | 인바운드 TCP 바이트 DDoS | 초당 바이트 수 | 최대
예 | Microsoft.Network/publicIPAddresses | TCPPacketsDroppedDDoS | 인바운드 TCP 패킷이 삭제된 DDoS | 초당 개수 | 최대
예 | Microsoft.Network/publicIPAddresses | TCPPacketsForwardedDDoS | 인바운드 TCP 패킷이 전달된 DDoS | 초당 개수 | 최대
예 | Microsoft.Network/publicIPAddresses | TCPPacketsInDDoS | 인바운드 TCP 패킷 DDoS | 초당 개수 | 최대
예 | Microsoft.Network/publicIPAddresses | UDPBytesDroppedDDoS | 인바운드 UDP 바이트가 삭제된 DDoS | 초당 바이트 수 | 최대
예 | Microsoft.Network/publicIPAddresses | UDPBytesForwardedDDoS | 인바운드 UDP 바이트가 전달된 DDoS | 초당 바이트 수 | 최대
예 | Microsoft.Network/publicIPAddresses | UDPBytesInDDoS | 인바운드 UDP 바이트 DDoS | 초당 바이트 수 | 최대
예 | Microsoft.Network/publicIPAddresses | UDPPacketsDroppedDDoS | 인바운드 UDP 패킷이 삭제된 DDoS | 초당 개수 | 최대
예 | Microsoft.Network/publicIPAddresses | UDPPacketsForwardedDDoS | 인바운드 UDP 패킷이 전달된 DDoS | 초당 개수 | 최대
예 | Microsoft.Network/publicIPAddresses | UDPPacketsInDDoS | 인바운드 UDP 패킷 DDoS | 초당 개수 | 최대
예 | Microsoft.Network/publicIPAddresses | VipAvailability | 데이터 경로 가용성 | 개수 | 평균
예 | Microsoft.Network/trafficManagerProfiles | ProbeAgentCurrentEndpointStateByProfileResourceId | 엔드포인트별 엔드포인트 상태 | 개수 | 최대
예 | Microsoft.Network/trafficManagerProfiles | QpsByEndpoint | 반환된 엔드포인트별 쿼리 | 개수 | 합계
예 | Microsoft.Network/virtualNetworkGateways | AverageBandwidth | 게이트웨이 S2S 대역폭 | 초당 바이트 수 | 평균
예 | Microsoft.Network/virtualNetworkGateways | P2SBandwidth | 게이트웨이 P2S 대역폭 | 초당 바이트 수 | 평균
예 | Microsoft.Network/virtualNetworkGateways | P2SConnectionCount | P2S 연결 수 | 개수 | 최대
예 | Microsoft.Network/virtualNetworkGateways | TunnelAverageBandwidth | 터널 대역폭 | 초당 바이트 수 | 평균
예 | Microsoft.Network/virtualNetworkGateways | TunnelEgressBytes | 터널 송신 바이트 | 바이트 | 합계
예 | Microsoft.Network/virtualNetworkGateways | TunnelEgressPacketDropTSMismatch | 터널 송신 TS 불일치 패킷 삭제 | 개수 | 합계
예 | Microsoft.Network/virtualNetworkGateways | TunnelEgressPackets | 터널 송신 패킷 | 개수 | 합계
예 | Microsoft.Network/virtualNetworkGateways | TunnelIngressBytes | 터널 수신 바이트 | 바이트 | 합계
예 | Microsoft.Network/virtualNetworkGateways | TunnelIngressPacketDropTSMismatch | 터널 수신 TS 불일치 패킷 삭제 | 개수 | 합계
예 | Microsoft.Network/virtualNetworkGateways | TunnelIngressPackets | 터널 수신 패킷 | 개수 | 합계
예 | Microsoft.Network/virtualNetworks | PingMeshAverageRoundtripMs | VM에 대 한 Ping 왕복 시간 | 밀리초 | 평균
예 | Microsoft.Network/virtualNetworks | PingMeshProbesFailedPercent | VM에 대 한 Ping 실패 | % | 평균
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming | 들어오는 메시지 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.all.failedrequests | 들어오는 모든 실패한 요청 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.all.requests | 들어오는 모든 요청 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.scheduled | 전송된 예약된 푸시 알림 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | incoming.scheduled.cancel | 취소된 예약된 푸시 알림 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.all | 설치 관리 작업 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.delete | 설치 작업 삭제 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.get | 설치 작업 가져오기 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.patch | 설치 작업 패치 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.upsert | 설치 작업 만들기 또는 업데이트 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | notificationhub.pushes | 모든 나가는 알림 수 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.badorexpiredchannel | 잘못되거나 만료된 채널 오류 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.channelerror | 채널 오류 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.invalidpayload | 페이로드 오류 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.pnserror | 외부 알림 시스템 오류 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.success | 성공적인 알림 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.badchannel | APNS 잘못된 채널 오류 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.expiredchannel | APNS 만료된 채널 오류 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.invalidcredentials | APNS 권한 부여 오류 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.invalidnotificationsize | APNS 잘못된 알림 크기 오류 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.pnserror | APNS 오류 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.success | APNS 성공적인 알림 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.authenticationerror | GCM 인증 오류 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.badchannel | GCM 잘못된 채널 오류 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.expiredchannel | GCM 만료된 채널 오류 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidcredentials | GCM 권한 부여 오류(잘못된 자격 증명) | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidnotificationformat | GCM 잘못된 알림 형식 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidnotificationsize | GCM 잘못된 알림 크기 오류 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.pnserror | GCM 오류 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.success | GCM 성공적인 알림 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.throttled | GCM 제한된 알림 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.wrongchannel | GCM 잘못된 채널 오류 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.authenticationerror | MPNS 인증 오류 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.badchannel | MPNS 잘못된 채널 오류 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.channeldisconnected | MPNS 채널 연결 끊김 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.dropped | MPNS 삭제된 알림 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.invalidcredentials | MPNS 잘못된 자격 증명 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.invalidnotificationformat | MPNS 잘못된 알림 형식 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.pnserror | MPNS 오류 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.success | MPNS 성공적인 알림 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.throttled | MPNS 제한된 알림 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.authenticationerror | WNS 인증 오류 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.badchannel | WNS 잘못된 채널 오류 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.channeldisconnected | WNS 채널 연결 끊김 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.channelthrottled | WNS 채널 제한 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.dropped | WNS 삭제된 알림 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.expiredchannel | WNS 만료된 채널 오류 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidcredentials | WNS 권한 부여 오류(잘못된 자격 증명) | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidnotificationformat | WNS 잘못된 알림 형식 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidnotificationsize | WNS 잘못된 알림 크기 오류 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidtoken | WNS 권한 부여 오류(잘못된 토큰) | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.pnserror | WNS 오류 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.success | WNS 성공적인 알림 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.throttled | WNS 제한된 알림 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.tokenproviderunreachable | WNS 권한 부여 오류(연결할 수 없음) | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.wrongtoken | WNS 권한 부여 오류(잘못된 토큰) | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.all | 등록 작업 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.create | 등록 만들기 작업 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.delete | 등록 삭제 작업 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.get | 등록 읽기 작업 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registration.update | 등록 업데이트 작업 | 개수 | 합계
예 | Microsoft.NotificationHubs/Namespaces/NotificationHubs | scheduled.pending | 보류 중인 예약된 알림 | 개수 | 합계
예 | Microsoft.OperationalInsights/workspaces | Average_% Available Memory | % Available Memory | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_% Available Swap Space | % Available Swap Space | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_% Committed Bytes In Use | % Committed Bytes In Use | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_% DPC Time | % DPC Time | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_% Free Inodes | % Free Inodes | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_% Free Space | 사용 가능한 공간의 백분율(%) | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_% Free Space | 사용 가능한 공간의 백분율(%) | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_% Idle Time | % Idle Time | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_% Interrupt Time | % Interrupt Time | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_% IO Wait Time | % IO Wait Time | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_% Nice Time | % Nice Time | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_% Privileged Time | % Privileged Time | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_% Processor Time | % Processor Time | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_% Processor Time | % Processor Time | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_% Used Inodes | % Used Inodes | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_% Used Memory | % Used Memory | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_% Used Space | % Used Space | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_% Used Swap Space | % Used Swap Space | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_% User Time | % User Time | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Available MBytes | 사용 가능한 MB | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Available MBytes Memory | Available MBytes Memory | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Available MBytes Swap | Available MBytes Swap | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Avg. Disk sec/Read 성능 카운터를 수집합니다. | Avg. Disk sec/Read | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Avg. Disk sec/Read 성능 카운터를 수집합니다. | Avg. Disk sec/Read | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Avg. Disk sec/Transfer | Avg. Disk sec/Transfer | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Avg. Disk sec/Write | Avg. Disk sec/Write | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Avg. Disk sec/Write | Avg. Disk sec/Write | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Bytes Received/sec | Bytes Received/sec | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Bytes Sent/sec | Bytes Sent/sec | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Bytes Total/sec | Bytes Total/sec | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Current Disk Queue Length | 현재 디스크 큐 길이 | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Disk Read Bytes/sec | Disk Read Bytes/sec | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Disk Reads/sec | 디스크 읽기/초 | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Disk Reads/sec | 디스크 읽기/초 | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Disk Transfers/sec | Disk Transfers/sec | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Disk Transfers/sec | Disk Transfers/sec | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Disk Write Bytes/sec | Disk Write Bytes/sec | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Disk Writes/sec | Disk Writes/sec | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Disk Writes/sec | Disk Writes/sec | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Free Megabytes | Free Megabytes | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Free Megabytes | Free Megabytes | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Free Physical Memory | Free Physical Memory | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Free Space in Paging Files | Free Space in Paging Files | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Free Virtual Memory | Free Virtual Memory | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Logical Disk Bytes/sec | Logical Disk Bytes/sec | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Page Reads/sec | Page Reads/sec | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Page Writes/sec | Page Writes/sec | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Pages/sec | 페이지 수/초 | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Pct Privileged Time | Pct Privileged Time | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Pct User Time | Pct User Time | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Physical Disk Bytes/sec | Physical Disk Bytes/sec | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Processes | 프로세스 | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Processor Queue Length | Processor Queue Length | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Size Stored In Paging Files | Size Stored In Paging Files | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Total Bytes | 총 바이트 수 | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Total Bytes Received | 받은 총 바이트 | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Total Bytes Transmitted | 전송된 총 바이트 | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Total Collisions | 총 충돌 | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Total Packets Received | 받은 총 패킷 | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Total Packets Transmitted | 전송된 총 패킷 | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Total Rx Errors | 총 Rx 오류 | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Total Tx Errors | 총 Tx 오류 | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Uptime | 가동 시간 | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Used MBytes Swap Space | Used MBytes Swap Space | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Used Memory kBytes | 사용된 메모리 KB | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Used Memory MBytes | Used Memory MBytes | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Users | Users | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | Average_Virtual Shared Memory | Virtual Shared Memory | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | 이벤트 | 이벤트 | 개수 | 평균
예 | Microsoft.OperationalInsights/workspaces | 하트비트 | 하트비트 | 개수 | 합계
예 | Microsoft.OperationalInsights/workspaces | 업데이트 | 업데이트 | 개수 | 평균
예 | Microsoft.PowerBIDedicated/capacities | memory_metric | 메모리 | 바이트 | 평균
예 | Microsoft.PowerBIDedicated/capacities | memory_thrashing_metric | 메모리 스 래 싱 (데이터 집합) | % | 평균
예 | Microsoft.PowerBIDedicated/capacities | qpu_high_utilization_metric | QPU 높은 사용률 | 개수 | 합계
예 | Microsoft.PowerBIDedicated/capacities | QueryDuration | 쿼리 기간 (데이터 집합) | 밀리초 | 평균
예 | Microsoft.PowerBIDedicated/capacities | QueryPoolJobQueueLength | 쿼리 풀 작업 큐 길이 (데이터 집합) | 개수 | 평균
아니요 | Microsoft.Relay/namespaces | ActiveConnections | ActiveConnections | 개수 | 합계
아니요 | Microsoft.Relay/namespaces | ActiveListeners | ActiveListeners | 개수 | 합계
예 | Microsoft.Relay/namespaces | BytesTransferred | BytesTransferred | 개수 | 합계
아니요 | Microsoft.Relay/namespaces | ListenerConnections-ClientError | ListenerConnections-ClientError | 개수 | 합계
아니요 | Microsoft.Relay/namespaces | ListenerConnections-ServerError | ListenerConnections-ServerError | 개수 | 합계
아니요 | Microsoft.Relay/namespaces | ListenerConnections-Success | ListenerConnections-Success | 개수 | 합계
아니요 | Microsoft.Relay/namespaces | ListenerConnections-TotalRequests | ListenerConnections-TotalRequests | 개수 | 합계
아니요 | Microsoft.Relay/namespaces | ListenerDisconnects | ListenerDisconnects | 개수 | 합계
아니요 | Microsoft.Relay/namespaces | SenderConnections-ClientError | SenderConnections-ClientError | 개수 | 합계
아니요 | Microsoft.Relay/namespaces | SenderConnections-ServerError | SenderConnections-ServerError | 개수 | 합계
아니요 | Microsoft.Relay/namespaces | SenderConnections-Success | SenderConnections-Success | 개수 | 합계
아니요 | Microsoft.Relay/namespaces | SenderConnections-TotalRequests | SenderConnections-TotalRequests | 개수 | 합계
아니요 | Microsoft.Relay/namespaces | SenderDisconnects | SenderDisconnects | 개수 | 합계
예 | Microsoft.Search/searchServices | SearchLatency | 검색 대기 시간 | 초 | 평균
예 | Microsoft.Search/searchServices | SearchQueriesPerSecond | 초당 검색 쿼리 수 | 초당 개수 | 평균
예 | Microsoft.Search/searchServices | ThrottledSearchQueriesPercentage | 제한된 검색 쿼리 백분율 | % | 평균
아니요 | Microsoft.ServiceBus/namespaces | ActiveConnections | ActiveConnections | 개수 | 합계
아니요 | Microsoft.ServiceBus/namespaces | ActiveMessages | 큐/토픽에 있는 활성 메시지 수 | 개수 | 평균
아니요 | Microsoft.ServiceBus/namespaces | ConnectionsClosed | 끊어진 연결. | 개수 | 평균
아니요 | Microsoft.ServiceBus/namespaces | ConnectionsOpened | 열린 연결. | 개수 | 평균
아니요 | Microsoft.ServiceBus/namespaces | CPUXNS | CPU (사용 되지 않음) | % | 최대
아니요 | Microsoft.ServiceBus/namespaces | DeadletteredMessages | 큐/토픽에서 배달 못 한 메시지의 수입니다. | 개수 | 평균
예 | Microsoft.ServiceBus/namespaces | IncomingMessages | 들어오는 메시지 | 개수 | 합계
예 | Microsoft.ServiceBus/namespaces | IncomingRequests | 들어오는 요청 | 개수 | 합계
아니요 | Microsoft.ServiceBus/namespaces | Messages | 큐/토픽에 있는 메시지 수 | 개수 | 평균
아니요 | Microsoft.ServiceBus/namespaces | NamespaceCpuUsage | CPU | % | 최대
아니요 | Microsoft.ServiceBus/namespaces | NamespaceMemoryUsage | 메모리 사용량 | % | 최대
예 | Microsoft.ServiceBus/namespaces | OutgoingMessages | 보내는 메시지 | 개수 | 합계
아니요 | Microsoft.ServiceBus/namespaces | ScheduledMessages | 큐/토픽에 있는 예약 된 메시지 수입니다. | 개수 | 평균
아니요 | Microsoft.ServiceBus/namespaces | ServerErrors | 서버 오류. | 개수 | 합계
아니요 | Microsoft.ServiceBus/namespaces | 크기 | 크기 | 바이트 | 평균
아니요 | Microsoft.ServiceBus/namespaces | SuccessfulRequests | 성공한 요청 | 개수 | 합계
아니요 | Microsoft.ServiceBus/namespaces | ThrottledRequests | 제한된 요청. | 개수 | 합계
아니요 | Microsoft.ServiceBus/namespaces | UserErrors | 사용자 오류. | 개수 | 합계
아니요 | Microsoft.ServiceBus/namespaces | WSXNS | 메모리 사용 (사용 되지 않음) | % | 최대
아니요 | Microsoft.ServiceFabricMesh/applications | ActualCpu | ActualCpu | 개수 | 평균
아니요 | Microsoft.ServiceFabricMesh/applications | ActualMemory | ActualMemory | 바이트 | 평균
아니요 | Microsoft.ServiceFabricMesh/applications | AllocatedCpu | AllocatedCpu | 개수 | 평균
아니요 | Microsoft.ServiceFabricMesh/applications | AllocatedMemory | AllocatedMemory | 바이트 | 평균
아니요 | Microsoft.ServiceFabricMesh/applications | ApplicationStatus | ApplicationStatus | 개수 | 평균
아니요 | Microsoft.ServiceFabricMesh/applications | ContainerStatus | ContainerStatus | 개수 | 평균
아니요 | Microsoft.ServiceFabricMesh/applications | CpuUtilization | CpuUtilization | % | 평균
아니요 | Microsoft.ServiceFabricMesh/applications | MemoryUtilization | MemoryUtilization | % | 평균
아니요 | Microsoft.ServiceFabricMesh/applications | RestartCount | RestartCount | 개수 | 평균
아니요 | Microsoft.ServiceFabricMesh/applications | ServiceReplicaStatus | ServiceReplicaStatus | 개수 | 평균
아니요 | Microsoft.ServiceFabricMesh/applications | ServiceStatus | ServiceStatus | 개수 | 평균
예 | Microsoft.SignalRService/SignalR | ConnectionCount | 연결 수 | 개수 | 최대
예 | Microsoft.SignalRService/SignalR | InboundTraffic | 인바운드 트래픽 | 바이트 | 합계
예 | Microsoft.SignalRService/SignalR | MessageCount | 메시지 수 | 개수 | 합계
예 | Microsoft.SignalRService/SignalR | OutboundTraffic | 아웃바운드 트래픽 | 바이트 | 합계
예 | Microsoft.SignalRService/SignalR | SystemErrors | 시스템 오류 | % | 최대
예 | Microsoft.SignalRService/SignalR | UserErrors | 사용자 오류 | % | 최대
예 | Microsoft.Sql/managedInstances | avg_cpu_percent | 평균 CPU 비율 | % | 평균
예 | Microsoft.Sql/managedInstances | io_bytes_read | 읽은 IO 바이트 | 바이트 | 평균
예 | Microsoft.Sql/managedInstances | io_bytes_written | 기록된 IO 바이트 | 바이트 | 평균
예 | Microsoft.Sql/managedInstances | io_requests | IO 요청 수 | 개수 | 평균
예 | Microsoft.Sql/managedInstances | reserved_storage_mb | 예약된 스토리지 공간 | 개수 | 평균
예 | Microsoft.Sql/managedInstances | storage_space_used_mb | 사용된 스토리지 공간 | 개수 | 평균
예 | Microsoft.Sql/managedInstances | virtual_core_count | 가상 코어 수 | 개수 | 평균
아니요 | Microsoft.Sql/servers | database_dtu_consumption_percent | DTU 비율 | % | 평균
아니요 | Microsoft.Sql/servers | database_storage_used | 사용 되는 데이터 공간 | 바이트 | 평균
예 | Microsoft.Sql/servers | dtu_consumption_percent | DTU 비율 | % | 평균
예 | Microsoft.Sql/servers | dtu_used | DTU 사용됨 | 개수 | 평균
예 | Microsoft.Sql/servers | storage_used | 사용 되는 데이터 공간 | 바이트 | 평균
예 | Microsoft.Sql/servers/databases | allocated_data_storage | 할당 된 데이터 공간 | 바이트 | 평균
예 | Microsoft.Sql/servers/databases | app_cpu_billed | 청구 되는 앱 CPU | 개수 | 합계
예 | Microsoft.Sql/servers/databases | app_cpu_percent | 앱 CPU 비율 | % | 평균
예 | Microsoft.Sql/servers/databases | app_memory_percent | 앱 메모리 비율 | % | 평균
예 | Microsoft.Sql/servers/databases | blocked_by_firewall | 방화벽에 의해 차단 | 개수 | 합계
예 | Microsoft.Sql/servers/databases | cache_hit_percent | 캐시 적중 비율 | % | 최대
예 | Microsoft.Sql/servers/databases | cache_used_percent | 캐시 사용 비율 | % | 최대
예 | Microsoft.Sql/servers/databases | connection_failed | 실패한 연결 | 개수 | 합계
예 | Microsoft.Sql/servers/databases | connection_successful | 성공적인 연결 | 개수 | 합계
예 | Microsoft.Sql/servers/databases | cpu_limit | CPU 제한 | 개수 | 평균
예 | Microsoft.Sql/servers/databases | cpu_percent | CPU 비율 | % | 평균
예 | Microsoft.Sql/servers/databases | cpu_used | 사용 되는 CPU | 개수 | 평균
예 | Microsoft.Sql/servers/databases | 교착 상태 | 교착 상태 | 개수 | 합계
예 | Microsoft.Sql/servers/databases | dtu_consumption_percent | DTU 비율 | % | 평균
예 | Microsoft.Sql/servers/databases | dtu_limit | DTU 제한 | 개수 | 평균
예 | Microsoft.Sql/servers/databases | dtu_used | DTU 사용됨 | 개수 | 평균
예 | Microsoft.Sql/servers/databases | dwu_consumption_percent | DWU 백분율 | % | 최대
예 | Microsoft.Sql/servers/databases | dwu_limit | DWU 제한 | 개수 | 최대
예 | Microsoft.Sql/servers/databases | dwu_used | DWU 사용됨 | 개수 | 최대
예 | Microsoft.Sql/servers/databases | local_tempdb_usage_percent | 로컬 tempdb 백분율 | % | 평균
예 | Microsoft.Sql/servers/databases | log_write_percent | 로그 IO 비율 | % | 평균
예 | Microsoft.Sql/servers/databases | memory_usage_percent | 메모리 비율 | % | 최대
예 | Microsoft.Sql/servers/databases | physical_data_read_percent | 데이터 IO 비율 | % | 평균
예 | Microsoft.Sql/servers/databases | sessions_percent | 세션 백분율 | % | 평균
예 | Microsoft.Sql/servers/databases | 스토리지 | 사용 되는 데이터 공간 | 바이트 | 최대
예 | Microsoft.Sql/servers/databases | storage_percent | 사용 되는 데이터 공간 (%) | % | 최대
예 | Microsoft.Sql/servers/databases | tempdb_data_size | Tempdb 데이터 파일 크기 (Kb) | 개수 | 최대
예 | Microsoft.Sql/servers/databases | tempdb_log_size | Tempdb 로그 파일 크기 (Kb) | 개수 | 최대
예 | Microsoft.Sql/servers/databases | tempdb_log_used_percent | Tempdb 백분율 로그가 사용 됨 | % | 최대
예 | Microsoft.Sql/servers/databases | workers_percent | 작업자 백분율 | % | 평균
예 | Microsoft.Sql/servers/databases | xtp_storage_percent | 메모리 내 OLTP 스토리지 백분율 | % | 평균
예 | Microsoft.Sql/servers/elasticPools | allocated_data_storage | 할당 된 데이터 공간 | 바이트 | 평균
예 | Microsoft.Sql/servers/elasticPools | allocated_data_storage_percent | 할당 된 데이터 공간 비율 | % | 최대
예 | Microsoft.Sql/servers/elasticPools | cpu_limit | CPU 제한 | 개수 | 평균
예 | Microsoft.Sql/servers/elasticPools | cpu_percent | CPU 비율 | % | 평균
예 | Microsoft.Sql/servers/elasticPools | cpu_used | 사용 되는 CPU | 개수 | 평균
아니요 | Microsoft.Sql/servers/elasticPools | database_allocated_data_storage | 할당 된 데이터 공간 | 바이트 | 평균
아니요 | Microsoft.Sql/servers/elasticPools | database_cpu_limit | CPU 제한 | 개수 | 평균
아니요 | Microsoft.Sql/servers/elasticPools | database_cpu_percent | CPU 비율 | % | 평균
아니요 | Microsoft.Sql/servers/elasticPools | database_cpu_used | 사용 되는 CPU | 개수 | 평균
아니요 | Microsoft.Sql/servers/elasticPools | database_dtu_consumption_percent | DTU 비율 | % | 평균
아니요 | Microsoft.Sql/servers/elasticPools | database_eDTU_used | eDTU 사용 | 개수 | 평균
아니요 | Microsoft.Sql/servers/elasticPools | database_log_write_percent | 로그 IO 비율 | % | 평균
아니요 | Microsoft.Sql/servers/elasticPools | database_physical_data_read_percent | 데이터 IO 비율 | % | 평균
아니요 | Microsoft.Sql/servers/elasticPools | database_sessions_percent | 세션 백분율 | % | 평균
아니요 | Microsoft.Sql/servers/elasticPools | database_storage_used | 사용 되는 데이터 공간 | 바이트 | 평균
아니요 | Microsoft.Sql/servers/elasticPools | database_workers_percent | 작업자 백분율 | % | 평균
예 | Microsoft.Sql/servers/elasticPools | dtu_consumption_percent | DTU 비율 | % | 평균
예 | Microsoft.Sql/servers/elasticPools | eDTU_limit | eDTU 제한 | 개수 | 평균
예 | Microsoft.Sql/servers/elasticPools | eDTU_used | eDTU 사용 | 개수 | 평균
예 | Microsoft.Sql/servers/elasticPools | log_write_percent | 로그 IO 비율 | % | 평균
예 | Microsoft.Sql/servers/elasticPools | physical_data_read_percent | 데이터 IO 비율 | % | 평균
예 | Microsoft.Sql/servers/elasticPools | sessions_percent | 세션 백분율 | % | 평균
예 | Microsoft.Sql/servers/elasticPools | storage_limit | 데이터 최대 크기 | 바이트 | 평균
예 | Microsoft.Sql/servers/elasticPools | storage_percent | 사용 되는 데이터 공간 (%) | % | 평균
예 | Microsoft.Sql/servers/elasticPools | storage_used | 사용 되는 데이터 공간 | 바이트 | 평균
예 | Microsoft.Sql/servers/elasticPools | tempdb_data_size | Tempdb 데이터 파일 크기 (Kb) | 개수 | 최대
예 | Microsoft.Sql/servers/elasticPools | tempdb_log_size | Tempdb 로그 파일 크기 (Kb) | 개수 | 최대
예 | Microsoft.Sql/servers/elasticPools | tempdb_log_used_percent | Tempdb 백분율 로그가 사용 됨 | % | 최대
예 | Microsoft.Sql/servers/elasticPools | workers_percent | 작업자 백분율 | % | 평균
예 | Microsoft.Sql/servers/elasticPools | xtp_storage_percent | 메모리 내 OLTP 스토리지 백분율 | % | 평균
예 | Microsoft.Storage/storageAccounts | 가용성 | 가용성 | % | 평균
예 | Microsoft.Storage/storageAccounts | 송신 | 송신 | 바이트 | 합계
예 | Microsoft.Storage/storageAccounts | 수신 | 수신 | 바이트 | 합계
예 | Microsoft.Storage/storageAccounts | SuccessE2ELatency | 성공 E2E 대기 시간 | 밀리초 | 평균
예 | Microsoft.Storage/storageAccounts | SuccessServerLatency | 성공 서버 대기 시간 | 밀리초 | 평균
예 | Microsoft.Storage/storageAccounts | 트랜잭션 | 트랜잭션 | 개수 | 합계
아니요 | Microsoft.Storage/storageAccounts | UsedCapacity | 사용된 용량 | 바이트 | 평균
예 | Microsoft.Storage/storageAccounts/blobServices | 가용성 | 가용성 | % | 평균
아니요 | Microsoft.Storage/storageAccounts/blobServices | BlobCapacity | Blob 용량 | 바이트 | 평균
아니요 | Microsoft.Storage/storageAccounts/blobServices | BlobCount | Blob 수 | 개수 | 평균
예 | Microsoft.Storage/storageAccounts/blobServices | ContainerCount | Blob 컨테이너 수 | 개수 | 평균
예 | Microsoft.Storage/storageAccounts/blobServices | 송신 | 송신 | 바이트 | 합계
아니요 | Microsoft.Storage/storageAccounts/blobServices | IndexCapacity | 인덱스 용량 | 바이트 | 평균
예 | Microsoft.Storage/storageAccounts/blobServices | 수신 | 수신 | 바이트 | 합계
예 | Microsoft.Storage/storageAccounts/blobServices | SuccessE2ELatency | 성공 E2E 대기 시간 | 밀리초 | 평균
예 | Microsoft.Storage/storageAccounts/blobServices | SuccessServerLatency | 성공 서버 대기 시간 | 밀리초 | 평균
예 | Microsoft.Storage/storageAccounts/blobServices | 트랜잭션 | 트랜잭션 | 개수 | 합계
예 | Microsoft.Storage/storageAccounts/fileServices | 가용성 | 가용성 | % | 평균
예 | Microsoft.Storage/storageAccounts/fileServices | 송신 | 송신 | 바이트 | 합계
아니요 | Microsoft.Storage/storageAccounts/fileServices | FileCapacity | 파일 용량 | 바이트 | 평균
아니요 | Microsoft.Storage/storageAccounts/fileServices | FileCount | 파일 수 | 개수 | 평균
아니요 | Microsoft.Storage/storageAccounts/fileServices | FileShareCount | 파일 공유 수 | 개수 | 평균
아니요 | Microsoft.Storage/storageAccounts/fileServices | FileShareQuota | 파일 공유 할당량 크기 | 바이트 | 평균
아니요 | Microsoft.Storage/storageAccounts/fileServices | FileShareSnapshotCount | 파일 공유 스냅숏 수 | 개수 | 평균
아니요 | Microsoft.Storage/storageAccounts/fileServices | FileShareSnapshotSize | 파일 공유 스냅숏 크기 | 바이트 | 평균
예 | Microsoft.Storage/storageAccounts/fileServices | 수신 | 수신 | 바이트 | 합계
예 | Microsoft.Storage/storageAccounts/fileServices | SuccessE2ELatency | 성공 E2E 대기 시간 | 밀리초 | 평균
예 | Microsoft.Storage/storageAccounts/fileServices | SuccessServerLatency | 성공 서버 대기 시간 | 밀리초 | 평균
예 | Microsoft.Storage/storageAccounts/fileServices | 트랜잭션 | 트랜잭션 | 개수 | 합계
예 | Microsoft.Storage/storageAccounts/queueServices | 가용성 | 가용성 | % | 평균
예 | Microsoft.Storage/storageAccounts/queueServices | 송신 | 송신 | 바이트 | 합계
예 | Microsoft.Storage/storageAccounts/queueServices | 수신 | 수신 | 바이트 | 합계
예 | Microsoft.Storage/storageAccounts/queueServices | QueueCapacity | 큐 용량 | 바이트 | 평균
예 | Microsoft.Storage/storageAccounts/queueServices | QueueCount | 큐 수 | 개수 | 평균
예 | Microsoft.Storage/storageAccounts/queueServices | QueueMessageCount | 큐 메시지 수 | 개수 | 평균
예 | Microsoft.Storage/storageAccounts/queueServices | SuccessE2ELatency | 성공 E2E 대기 시간 | 밀리초 | 평균
예 | Microsoft.Storage/storageAccounts/queueServices | SuccessServerLatency | 성공 서버 대기 시간 | 밀리초 | 평균
예 | Microsoft.Storage/storageAccounts/queueServices | 트랜잭션 | 트랜잭션 | 개수 | 합계
예 | Microsoft.Storage/storageAccounts/tableServices | 가용성 | 가용성 | % | 평균
예 | Microsoft.Storage/storageAccounts/tableServices | 송신 | 송신 | 바이트 | 합계
예 | Microsoft.Storage/storageAccounts/tableServices | 수신 | 수신 | 바이트 | 합계
예 | Microsoft.Storage/storageAccounts/tableServices | SuccessE2ELatency | 성공 E2E 대기 시간 | 밀리초 | 평균
예 | Microsoft.Storage/storageAccounts/tableServices | SuccessServerLatency | 성공 서버 대기 시간 | 밀리초 | 평균
예 | Microsoft.Storage/storageAccounts/tableServices | TableCapacity | 테이블 용량 | 바이트 | 평균
예 | Microsoft.Storage/storageAccounts/tableServices | TableCount | 테이블 수 | 개수 | 평균
예 | Microsoft.Storage/storageAccounts/tableServices | TableEntityCount | 테이블 엔터티 수 | 개수 | 평균
예 | Microsoft.Storage/storageAccounts/tableServices | 트랜잭션 | 트랜잭션 | 개수 | 합계
예 | StorageCache/캐시 | Cliops Ops | 총 클라이언트 IOPS | 개수 | 평균
예 | StorageCache/캐시 | ClientLatency | 평균 클라이언트 대기 시간 | 밀리초 | 평균
예 | StorageCache/캐시 | ClientLockIOPS | 클라이언트 잠금 IOPS | 초당 개수 | 평균
예 | StorageCache/캐시 | ClientMetadataReadIOPS | 클라이언트 메타 데이터 읽기 IOPS | 초당 개수 | 평균
예 | StorageCache/캐시 | ClientMetadataWriteIOPS | 클라이언트 메타 데이터 쓰기 IOPS | 초당 개수 | 평균
예 | StorageCache/캐시 | ClientReadIOPS | 클라이언트 읽기 IOPS | 초당 개수 | 평균
예 | StorageCache/캐시 | ClientReadThroughput | 평균 캐시 읽기 처리량 | 초당 바이트 수 | 평균
예 | StorageCache/캐시 | ClientWriteIOPS | 클라이언트 쓰기 IOPS | 초당 개수 | 평균
예 | StorageCache/캐시 | ClientWriteThroughput | 평균 캐시 쓰기 처리량 | 초당 바이트 수 | 평균
예 | StorageCache/캐시 | StorageTargetAsyncWriteThroughput | StorageTarget 비동기 쓰기 처리량 | 초당 바이트 수 | 평균
예 | StorageCache/캐시 | StorageTargetFillThroughput | StorageTarget 채우기 처리량 | 초당 바이트 수 | 평균
예 | StorageCache/캐시 | StorageTargetHealth | 저장소 대상 상태 | 개수 | 평균
예 | StorageCache/캐시 | StorageTargetIOPS | 총 StorageTarget IOPS | 개수 | 평균
예 | StorageCache/캐시 | StorageTargetLatency | StorageTarget 대기 시간 | 밀리초 | 평균
예 | StorageCache/캐시 | StorageTargetMetadataReadIOPS | StorageTarget Metadata 읽기 IOPS | 초당 개수 | 평균
예 | StorageCache/캐시 | StorageTargetMetadataWriteIOPS | StorageTarget Metadata Write IOPS | 초당 개수 | 평균
예 | StorageCache/캐시 | StorageTargetReadAheadThroughput | StorageTarget 미리 읽기 처리량 | 초당 바이트 수 | 평균
예 | StorageCache/캐시 | StorageTargetReadIOPS | StorageTarget 읽기 IOPS | 초당 개수 | 평균
예 | StorageCache/캐시 | StorageTargetSyncWriteThroughput | StorageTarget 동기 쓰기 처리량 | 초당 바이트 수 | 평균
예 | StorageCache/캐시 | StorageTargetTotalReadThroughput | StorageTarget 총 읽기 처리량 | 초당 바이트 수 | 평균
예 | StorageCache/캐시 | StorageTargetTotalWriteThroughput | StorageTarget 총 쓰기 처리량 | 초당 바이트 수 | 평균
예 | StorageCache/캐시 | StorageTargetWriteIOPS | StorageTarget 쓰기 IOPS | 개수 | 평균
예 | StorageCache/캐시 | 가동 시간 | 가동 시간 | 개수 | 평균
예 | microsoft.storagesync/storageSyncServices | ServerSyncSessionResult | 동기화 세션 결과 | 개수 | 평균
예 | microsoft.storagesync/storageSyncServices | StorageSyncBatchTransferredFileBytes | 동기화되는 바이트 수 | 바이트 | 합계
예 | microsoft.storagesync/storageSyncServices | StorageSyncRecalledNetworkBytesByApplication | 응용 프로그램별 클라우드 계층화 회수 크기 | 바이트 | 합계
예 | microsoft.storagesync/storageSyncServices | StorageSyncRecalledTotalNetworkBytes | 클라우드 계층화 회수 크기 | 바이트 | 합계
예 | microsoft.storagesync/storageSyncServices | StorageSyncRecallIOTotalSizeBytes | 클라우드 계층화 회수 | 바이트 | 합계
예 | microsoft.storagesync/storageSyncServices | StorageSyncRecallThroughputBytesPerSecond | 클라우드 계층화 회수 처리량 | 초당 바이트 수 | 평균
예 | microsoft.storagesync/storageSyncServices | StorageSyncServerHeartbeat | 서버 온라인 상태 | 개수 | 최대
예 | microsoft.storagesync/storageSyncServices | StorageSyncSyncSessionAppliedFilesCount | 동기화 된 파일 | 개수 | 합계
예 | microsoft.storagesync/storageSyncServices | StorageSyncSyncSessionPerItemErrorsCount | 동기화 상태가 아닌 파일 | 개수 | 합계
예 | microsoft.storagesync/storageSyncServices/registeredServers | ServerHeartbeat 비트 | 서버 온라인 상태 | 개수 | 최대
예 | microsoft.storagesync/storageSyncServices/registeredServers | ServerRecallIOTotalSizeBytes | 클라우드 계층화 회수 | 바이트 | 합계
예 | microsoft.storagesync/storageSyncServices/syncGroups | SyncGroupBatchTransferredFileBytes | 동기화되는 바이트 수 | 바이트 | 합계
예 | microsoft.storagesync/storageSyncServices/syncGroups | SyncGroupSyncSessionAppliedFilesCount | 동기화 된 파일 | 개수 | 합계
예 | microsoft.storagesync/storageSyncServices/syncGroups | SyncGroupSyncSessionPerItemErrorsCount | 동기화 상태가 아닌 파일 | 개수 | 합계
예 | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints | ServerEndpointBatchTransferredFileBytes | 동기화되는 바이트 수 | 바이트 | 합계
예 | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints | ServerEndpointSyncSessionAppliedFilesCount | 동기화 된 파일 | 개수 | 합계
예 | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints | ServerEndpointSyncSessionPerItemErrorsCount | 동기화 상태가 아닌 파일 | 개수 | 합계
예 | Microsoft.StreamAnalytics/streamingjobs | AMLCalloutFailedRequests | 실패한 기능 요청 | 개수 | 합계
예 | Microsoft.StreamAnalytics/streamingjobs | AMLCalloutInputEvents | 함수 이벤트 | 개수 | 합계
예 | Microsoft.StreamAnalytics/streamingjobs | AMLCalloutRequests | 기능 요청 | 개수 | 합계
예 | Microsoft.StreamAnalytics/streamingjobs | ConversionErrors | 데이터 변환 오류 | 개수 | 합계
예 | Microsoft.StreamAnalytics/streamingjobs | DeserializationError | 입력 역직렬화 오류 | 개수 | 합계
예 | Microsoft.StreamAnalytics/streamingjobs | DroppedOrAdjustedEvents | 잘못된 이벤트 | 개수 | 합계
예 | Microsoft.StreamAnalytics/streamingjobs | EarlyInputEvents | 초기 입력 이벤트 | 개수 | 합계
예 | Microsoft.StreamAnalytics/streamingjobs | 오류 | 런타임 오류 | 개수 | 합계
예 | Microsoft.StreamAnalytics/streamingjobs | InputEventBytes | 입력 이벤트 바이트 | 바이트 | 합계
예 | Microsoft.StreamAnalytics/streamingjobs | InputEvents | 입력 이벤트 | 개수 | 합계
예 | Microsoft.StreamAnalytics/streamingjobs | InputEventsSourcesBacklogged | 백로그된 입력 이벤트 | 개수 | 최대
예 | Microsoft.StreamAnalytics/streamingjobs | InputEventsSourcesPerSecond | 수신된 입력 원본 | 개수 | 합계
예 | Microsoft.StreamAnalytics/streamingjobs | LateInputEvents | 늦은 입력 이벤트 | 개수 | 합계
예 | Microsoft.StreamAnalytics/streamingjobs | OutputEvents | 출력 이벤트 | 개수 | 합계
예 | Microsoft.StreamAnalytics/streamingjobs | OutputWatermarkDelaySeconds | 워터마크 지연 | 초 | 최대
예 | Microsoft.StreamAnalytics/streamingjobs | ResourceUtilization | SU % 사용률 | % | 최대
예 | Microsoft.VMwareCloudSimple/virtualMachines | 디스크 읽기 바이트 | 디스크 읽기 바이트 | 바이트 | 합계
예 | Microsoft.VMwareCloudSimple/virtualMachines | 디스크 읽기 작업/초 | 디스크 읽기 작업/초 | 초당 개수 | 평균
예 | Microsoft.VMwareCloudSimple/virtualMachines | 디스크 쓰기 바이트 | 디스크 쓰기 바이트 | 바이트 | 합계
예 | Microsoft.VMwareCloudSimple/virtualMachines | 디스크 쓰기 작업/초 | 디스크 쓰기 작업/초 | 초당 개수 | 평균
예 | Microsoft.VMwareCloudSimple/virtualMachines | DiskReadBytesPerSecond | 디스크 읽기 바이트/초 | 초당 바이트 수 | 평균
예 | Microsoft.VMwareCloudSimple/virtualMachines | DiskReadLatency | 디스크 읽기 대기 시간 | 밀리초 | 평균
예 | Microsoft.VMwareCloudSimple/virtualMachines | DiskReadOperations | 디스크 읽기 작업 | 개수 | 합계
예 | Microsoft.VMwareCloudSimple/virtualMachines | DiskWriteBytesPerSecond | 디스크 쓰기 바이트/초 | 초당 바이트 수 | 평균
예 | Microsoft.VMwareCloudSimple/virtualMachines | DiskWriteLatency | 디스크 쓰기 대기 시간 | 밀리초 | 평균
예 | Microsoft.VMwareCloudSimple/virtualMachines | DiskWriteOperations | 디스크 쓰기 작업 | 개수 | 합계
예 | Microsoft.VMwareCloudSimple/virtualMachines | MemoryActive | 메모리 활성 | 바이트 | 평균
예 | Microsoft.VMwareCloudSimple/virtualMachines | MemoryGranted 됨 | 부여 된 메모리 | 바이트 | 평균
예 | Microsoft.VMwareCloudSimple/virtualMachines | MemoryUsed | 사용 되는 메모리 | 바이트 | 평균
예 | Microsoft.VMwareCloudSimple/virtualMachines | 네트워크 인 | 네트워크 인 | 바이트 | 합계
예 | Microsoft.VMwareCloudSimple/virtualMachines | 네트워크 아웃 | 네트워크 아웃 | 바이트 | 합계
예 | Microsoft.VMwareCloudSimple/virtualMachines | NetworkInBytesPerSecond | 네트워크 (바이트/초) | 초당 바이트 수 | 평균
예 | Microsoft.VMwareCloudSimple/virtualMachines | NetworkOutBytesPerSecond | 네트워크 출력 바이트/초 | 초당 바이트 수 | 평균
예 | Microsoft.VMwareCloudSimple/virtualMachines | 백분율 CPU | 백분율 CPU | % | 평균
예 | Microsoft.VMwareCloudSimple/virtualMachines | PercentageCpuReady | CPU 준비 비율 | 밀리초 | 합계
예 | Microsoft.Web/hostingEnvironments/multiRolePools | ActiveRequests | 활성 요청 | 개수 | 합계
예 | Microsoft.Web/hostingEnvironments/multiRolePools | AverageResponseTime | 평균 응답 시간 | 초 | 평균
예 | Microsoft.Web/hostingEnvironments/multiRolePools | BytesReceived | 데이터 입력 | 바이트 | 합계
예 | Microsoft.Web/hostingEnvironments/multiRolePools | BytesSent | 데이터 출력 | 바이트 | 합계
예 | Microsoft.Web/hostingEnvironments/multiRolePools | CpuPercentage | CPU 비율 | % | 평균
예 | Microsoft.Web/hostingEnvironments/multiRolePools | DiskQueueLength | Disk Queue Length | 개수 | 평균
예 | Microsoft.Web/hostingEnvironments/multiRolePools | Http101 | Http 101 | 개수 | 합계
예 | Microsoft.Web/hostingEnvironments/multiRolePools | Http2xx | Http 2xx | 개수 | 합계
예 | Microsoft.Web/hostingEnvironments/multiRolePools | Http3xx | Http 3xx | 개수 | 합계
예 | Microsoft.Web/hostingEnvironments/multiRolePools | Http401 | Http 401 | 개수 | 합계
예 | Microsoft.Web/hostingEnvironments/multiRolePools | Http403 | Http 403 | 개수 | 합계
예 | Microsoft.Web/hostingEnvironments/multiRolePools | Http404 | Http 404 | 개수 | 합계
예 | Microsoft.Web/hostingEnvironments/multiRolePools | Http406 | Http 406 | 개수 | 합계
예 | Microsoft.Web/hostingEnvironments/multiRolePools | Http4xx | Http 4xx | 개수 | 합계
예 | Microsoft.Web/hostingEnvironments/multiRolePools | Http5xx | Http 서버 오류 | 개수 | 합계
예 | Microsoft.Web/hostingEnvironments/multiRolePools | HttpQueueLength | Http 큐 길이 | 개수 | 평균
예 | Microsoft.Web/hostingEnvironments/multiRolePools | LargeAppServicePlanInstances | 대형 App Service 계획 작업자 | 개수 | 평균
예 | Microsoft.Web/hostingEnvironments/multiRolePools | MediumAppServicePlanInstances | 중형 App Service 계획 작업자 | 개수 | 평균
예 | Microsoft.Web/hostingEnvironments/multiRolePools | MemoryPercentage | 메모리 비율 | % | 평균
예 | Microsoft.Web/hostingEnvironments/multiRolePools | 요청 | 요청 | 개수 | 합계
예 | Microsoft.Web/hostingEnvironments/multiRolePools | SmallAppServicePlanInstances | 소형 App Service 계획 작업자 | 개수 | 평균
예 | Microsoft.Web/hostingEnvironments/multiRolePools | TotalFrontEnds | 총 프런트 엔드 | 개수 | 평균
예 | Microsoft.Web/hostingEnvironments/workerPools | CpuPercentage | CPU 비율 | % | 평균
예 | Microsoft.Web/hostingEnvironments/workerPools | MemoryPercentage | 메모리 비율 | % | 평균
예 | Microsoft.Web/hostingEnvironments/workerPools | WorkersAvailable | 사용 가능한 작업자 | 개수 | 평균
예 | Microsoft.Web/hostingEnvironments/workerPools | WorkersTotal | 총 작업자 | 개수 | 평균
예 | Microsoft.Web/hostingEnvironments/workerPools | WorkersUsed | 사용된 작업자 | 개수 | 평균
예 | Microsoft.Web/serverfarms | BytesReceived | 데이터 입력 | 바이트 | 합계
예 | Microsoft.Web/serverfarms | BytesSent | 데이터 출력 | 바이트 | 합계
예 | Microsoft.Web/serverfarms | CpuPercentage | CPU 비율 | % | 평균
예 | Microsoft.Web/serverfarms | DiskQueueLength | Disk Queue Length | 개수 | 평균
예 | Microsoft.Web/serverfarms | HttpQueueLength | Http 큐 길이 | 개수 | 평균
예 | Microsoft.Web/serverfarms | MemoryPercentage | 메모리 비율 | % | 평균
예 | Microsoft.Web/serverfarms | TcpCloseWait | TCP 닫기 대기 | 개수 | 평균
예 | Microsoft.Web/serverfarms | TcpClosing | TCP 닫기 | 개수 | 평균
예 | Microsoft.Web/serverfarms | TcpEstablished | TCP 설정 | 개수 | 평균
예 | Microsoft.Web/serverfarms | TcpFinWait1 | TCP Fin 대기 1 | 개수 | 평균
예 | Microsoft.Web/serverfarms | TcpFinWait2 | TCP Fin 대기 2 | 개수 | 평균
예 | Microsoft.Web/serverfarms | TcpLastAck | TCP 마지막 Ack | 개수 | 평균
예 | Microsoft.Web/serverfarms | TcpSynReceived | TCP Syn Received | 개수 | 평균
예 | Microsoft.Web/serverfarms | TcpSynSent | TCP Syn 전송 | 개수 | 평균
예 | Microsoft.Web/serverfarms | TcpTimeWait | TCP 시간 대기 | 개수 | 평균
예 | Microsoft.Web/sites | AppConnections | 연결 | 개수 | 평균
예 | Microsoft.Web/sites | AverageMemoryWorkingSet | 평균 메모리 작업 집합 | 바이트 | 평균
예 | Microsoft.Web/sites | AverageResponseTime | 평균 응답 시간 | 초 | 평균
예 | Microsoft.Web/sites | BytesReceived | 데이터 입력 | 바이트 | 합계
예 | Microsoft.Web/sites | BytesSent | 데이터 출력 | 바이트 | 합계
예 | Microsoft.Web/sites | CpuTime | CPU 시간 | 초 | 합계
예 | Microsoft.Web/sites | CurrentAssemblies | Current Assemblies | 개수 | 평균
예 | Microsoft.Web/sites | FunctionExecutionCount | 함수 실행 횟수 | 개수 | 합계
예 | Microsoft.Web/sites | FunctionExecutionUnits | 함수 실행 단위 | 개수 | 합계
예 | Microsoft.Web/sites | Gen0Collections | Gen 0 가비지 수집 | 개수 | 합계
예 | Microsoft.Web/sites | Gen1Collections | Gen 1 가비지 수집 | 개수 | 합계
예 | Microsoft.Web/sites | Gen2Collections | Gen 2 가비지 수집 | 개수 | 합계
예 | Microsoft.Web/sites | 핸들 | 핸들 수 | 개수 | 평균
예 | Microsoft.Web/sites | HealthCheckStatus | 상태 검사 상태 | 개수 | 평균
예 | Microsoft.Web/sites | Http101 | Http 101 | 개수 | 합계
예 | Microsoft.Web/sites | Http2xx | Http 2xx | 개수 | 합계
예 | Microsoft.Web/sites | Http3xx | Http 3xx | 개수 | 합계
예 | Microsoft.Web/sites | Http401 | Http 401 | 개수 | 합계
예 | Microsoft.Web/sites | Http403 | Http 403 | 개수 | 합계
예 | Microsoft.Web/sites | Http404 | Http 404 | 개수 | 합계
예 | Microsoft.Web/sites | Http406 | Http 406 | 개수 | 합계
예 | Microsoft.Web/sites | Http4xx | Http 4xx | 개수 | 합계
예 | Microsoft.Web/sites | Http5xx | Http 서버 오류 | 개수 | 합계
예 | Microsoft.Web/sites | HttpResponseTime | 응답 시간 | 초 | 평균
예 | Microsoft.Web/sites | IoOtherBytesPerSecond | 초당 IO 기타 바이트 수 | 초당 바이트 수 | 합계
예 | Microsoft.Web/sites | IoOtherOperationsPerSecond | 초당 IO 기타 작업 수 | 초당 바이트 수 | 합계
예 | Microsoft.Web/sites | IoReadBytesPerSecond | 초당 IO 읽기 바이트 수 | 초당 바이트 수 | 합계
예 | Microsoft.Web/sites | IoReadOperationsPerSecond | 초당 IO 읽기 작업 수 | 초당 바이트 수 | 합계
예 | Microsoft.Web/sites | IoWriteBytesPerSecond | 초당 IO 쓰기 바이트 수 | 초당 바이트 수 | 합계
예 | Microsoft.Web/sites | IoWriteOperationsPerSecond | 초당 IO 쓰기 작업 수 | 초당 바이트 수 | 합계
예 | Microsoft.Web/sites | MemoryWorkingSet | 메모리 작업 집합 | 바이트 | 평균
예 | Microsoft.Web/sites | PrivateBytes | 전용 바이트 | 바이트 | 평균
예 | Microsoft.Web/sites | 요청 | 요청 | 개수 | 합계
예 | Microsoft.Web/sites | RequestsInApplicationQueue | 애플리케이션 큐의 요청 수 | 개수 | 평균
예 | Microsoft.Web/sites | 스레드 | 스레드 수 | 개수 | 평균
예 | Microsoft.Web/sites | TotalAppDomains | 총 앱 도메인 | 개수 | 평균
예 | Microsoft.Web/sites | TotalAppDomainsUnloaded | 언로드된 총 앱 도메인 | 개수 | 평균
예 | Microsoft.Web/sites/slots | AppConnections | 연결 | 개수 | 평균
예 | Microsoft.Web/sites/slots | AverageMemoryWorkingSet | 평균 메모리 작업 집합 | 바이트 | 평균
예 | Microsoft.Web/sites/slots | AverageResponseTime | 평균 응답 시간 | 초 | 평균
예 | Microsoft.Web/sites/slots | BytesReceived | 데이터 입력 | 바이트 | 합계
예 | Microsoft.Web/sites/slots | BytesSent | 데이터 출력 | 바이트 | 합계
예 | Microsoft.Web/sites/slots | CpuTime | CPU 시간 | 초 | 합계
예 | Microsoft.Web/sites/slots | CurrentAssemblies | Current Assemblies | 개수 | 평균
예 | Microsoft.Web/sites/slots | FunctionExecutionCount | 함수 실행 횟수 | 개수 | 합계
예 | Microsoft.Web/sites/slots | FunctionExecutionUnits | 함수 실행 단위 | 개수 | 합계
예 | Microsoft.Web/sites/slots | Gen0Collections | Gen 0 가비지 수집 | 개수 | 합계
예 | Microsoft.Web/sites/slots | Gen1Collections | Gen 1 가비지 수집 | 개수 | 합계
예 | Microsoft.Web/sites/slots | Gen2Collections | Gen 2 가비지 수집 | 개수 | 합계
예 | Microsoft.Web/sites/slots | 핸들 | 핸들 수 | 개수 | 평균
예 | Microsoft.Web/sites/slots | HealthCheckStatus | 상태 검사 상태 | 개수 | 평균
예 | Microsoft.Web/sites/slots | Http101 | Http 101 | 개수 | 합계
예 | Microsoft.Web/sites/slots | Http2xx | Http 2xx | 개수 | 합계
예 | Microsoft.Web/sites/slots | Http3xx | Http 3xx | 개수 | 합계
예 | Microsoft.Web/sites/slots | Http401 | Http 401 | 개수 | 합계
예 | Microsoft.Web/sites/slots | Http403 | Http 403 | 개수 | 합계
예 | Microsoft.Web/sites/slots | Http404 | Http 404 | 개수 | 합계
예 | Microsoft.Web/sites/slots | Http406 | Http 406 | 개수 | 합계
예 | Microsoft.Web/sites/slots | Http4xx | Http 4xx | 개수 | 합계
예 | Microsoft.Web/sites/slots | Http5xx | Http 서버 오류 | 개수 | 합계
예 | Microsoft.Web/sites/slots | HttpResponseTime | 응답 시간 | 초 | 평균
예 | Microsoft.Web/sites/slots | IoOtherBytesPerSecond | 초당 IO 기타 바이트 수 | 초당 바이트 수 | 합계
예 | Microsoft.Web/sites/slots | IoOtherOperationsPerSecond | 초당 IO 기타 작업 수 | 초당 바이트 수 | 합계
예 | Microsoft.Web/sites/slots | IoReadBytesPerSecond | 초당 IO 읽기 바이트 수 | 초당 바이트 수 | 합계
예 | Microsoft.Web/sites/slots | IoReadOperationsPerSecond | 초당 IO 읽기 작업 수 | 초당 바이트 수 | 합계
예 | Microsoft.Web/sites/slots | IoWriteBytesPerSecond | 초당 IO 쓰기 바이트 수 | 초당 바이트 수 | 합계
예 | Microsoft.Web/sites/slots | IoWriteOperationsPerSecond | 초당 IO 쓰기 작업 수 | 초당 바이트 수 | 합계
예 | Microsoft.Web/sites/slots | MemoryWorkingSet | 메모리 작업 집합 | 바이트 | 평균
예 | Microsoft.Web/sites/slots | PrivateBytes | 전용 바이트 | 바이트 | 평균
예 | Microsoft.Web/sites/slots | 요청 | 요청 | 개수 | 합계
예 | Microsoft.Web/sites/slots | RequestsInApplicationQueue | 애플리케이션 큐의 요청 수 | 개수 | 평균
예 | Microsoft.Web/sites/slots | 스레드 | 스레드 수 | 개수 | 평균
예 | Microsoft.Web/sites/slots | TotalAppDomains | 총 앱 도메인 | 개수 | 평균
예 | Microsoft.Web/sites/slots | TotalAppDomainsUnloaded | 언로드된 총 앱 도메인 | 개수 | 평균
