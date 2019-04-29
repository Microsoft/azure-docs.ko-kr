---
title: Azure Analysis Services 서버 메트릭 모니터링 | Microsoft Docs
description: Azure Portal에서 Analysis Services 서버 메트릭을 모니터링하는 방법에 대해 알아봅니다.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: cdffa8e138062a91bd1876ac6e44728c47d9cdd7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61065066"
---
# <a name="monitor-server-metrics"></a>서버 메트릭 모니터링

Analysis Services에서는 서버의 성능 및 상태를 모니터링하는 메트릭을 제공합니다. 예를 들어 메모리와 CPU 사용량, 클라이언트 연결 수 및 쿼리 리소스 소비를 모니터링합니다. Analysis Services에서는 대부분의 다른 Azure 서비스와 동일한 모니터링 프레임워크를 사용합니다. 자세한 내용은 [Microsoft Azure에서 메트릭](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)을 참조하세요.

심층 진단을 수행하고, 성능을 추적하고, 리소스 그룹이나 구독의 여러 서비스 리소스에서 추세를 식별하려면 [Azure Monitor](https://azure.microsoft.com/services/monitor/)를 사용합니다. Azure Monitor(서비스)는 요금이 청구될 수 있는 서비스입니다.


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>Analysis Services 서버에 대한 메트릭을 모니터링하려면

1. Azure Portal에서 **메트릭**을 선택합니다.

    ![Azure Portal에서 모니터링](./media/analysis-services-monitor/aas-monitor-portal.png)

2. **사용 가능한 메트릭**에서 차트에 포함할 메트릭을 선택합니다. 

    ![모니터링 차트](./media/analysis-services-monitor/aas-monitor-chart.png)

<a id="#server-metrics"></a>

## <a name="server-metrics"></a>서버 메트릭

모니터링 시나리오에 가장 적합한 메트릭을 확인하기 위해 이 테이블을 사용합니다. 동일한 단위의 메트릭만이 같은 차트에 표시될 수 있습니다.

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|
|---|---|---|---|---|
|CommandPoolJobQueueLength|명령 풀의 작업 큐 길이|카운트|평균|명령 스레드 풀의 큐에 있는 작업 수입니다.|
|CurrentConnections|연결: 현재 연결|카운트|평균|현재 설정된 클라이언트 연결 수입니다.|
|CurrentUserSessions|현재 사용자 세션 수|카운트|평균|현재 설정된 사용자 세션 수입니다.|
|mashup_engine_memory_metric|M 엔진 메모리|바이트|평균|매시업 엔진 프로세스별 메모리 사용량|
|mashup_engine_qpu_metric|M 엔진 QPU|카운트|평균|매시업 엔진 프로세스별 QPU 사용량|
|memory_metric|메모리|바이트|평균|메모리. 범위는 S1의 경우 0-25GB, S2의 경우 0-50GB, S4의 경우 0-100GB임|
|memory_thrashing_metric|메모리 쓰래싱|백분율|평균|평균 메모리 쓰래싱입니다.|
|CleanerCurrentPrice|메모리: 클리너 현재 가격|카운트|평균|현재 메모리 가격, $/바이트/시간, 1000으로 일반화됩니다.|
|CleanerMemoryNonshrinkable|메모리: 클리너 메모리 축소 불가능|바이트|평균|메모리 양, 바이트 단위, 백그라운드 클리너에 의해 제거되는 대상이 아닙니다.|
|CleanerMemoryShrinkable|메모리: 클리너 메모리 축소 가능|바이트|평균|메모리 양, 바이트 단위, 백그라운드 클리너에 의해 제거되는 대상입니다.|
|MemoryLimitHard|메모리: 메모리 제한 하드|바이트|평균|하드 메모리 제한, 구성 파일 원본입니다.|
|MemoryLimitHigh|메모리: 메모리 제한 상한|바이트|평균|상한 메모리 제한, 구성 파일 원본입니다.|
|MemoryLimitLow|메모리: 메모리 제한 하한|바이트|평균|하한 메모리 제한, 구성 파일 원본입니다.|
|MemoryLimitVertiPaq|메모리: 메모리 제한 VertiPaq|바이트|평균|메모리 내 제한, 구성 파일 원본입니다.|
|MemoryUsage|메모리: 메모리 사용량|바이트|평균|클리너 메모리 가격을 계산하는 데 사용되는 서버 프로세스의 메모리 사용량입니다. 메모리 매핑된 데이터 크기를 더한 카운터 Process\PrivateBytes와 동일하며 엔진 메모리 제한을 초과하여 메모리 내 분석 엔진(VertiPaq)에서 매핑하거나 할당하는 메모리를 무시합니다.|
|할당량|메모리: 할당량|바이트|평균|현재 메모리 할당량, 바이트 단위입니다. 메모리 할당량은 메모리 부여 또는 메모리 예약이라고도 합니다.|
|QuotaBlocked|메모리: 차단된 할당량|카운트|평균|다른 메모리 할당량이 해제될 때까지 차단되는 할당량 요청의 현재 수입니다.|
|VertiPaqNonpaged|메모리: 페이징되지 않은 VertiPaq|바이트|평균|메모리 내 엔진에서 사용하기 위해 설정된 작동 중에 잠긴 메모리 바이트입니다.|
|VertiPaqPaged|메모리: 페이징된 VertiPaq|바이트|평균|메모리 내 데이터에 사용 중인 페이징된 메모리 바이트입니다.|
|ProcessingPoolJobQueueLength|처리 풀의 작업 큐 길이|카운트|평균|처리 스레드 풀의 큐에 있는 비-I/O 작업 수입니다.|
|RowsConvertedPerSec|처리: 초당 변환된 행|초당 개수|평균|처리하는 동안 변환된 행의 비율입니다.|
|RowsReadPerSec|처리: 초당 읽은 행|초당 개수|평균|모든 관계형 데이터베이스에서 읽은 행의 비율입니다.|
|RowsWrittenPerSec|처리: 초당 작성된 행|초당 개수|평균|처리하는 동안 작성된 행의 비율입니다.|
|qpu_metric|QPU|카운트|평균|QPU. 범위는 S1의 경우 0-100, S2의 경우 0-200, S4의 경우 0-400임|
|QueryPoolBusyThreads|쿼리 풀의 사용 중인 스레드|카운트|평균|쿼리 스레드 풀의 사용 중인 스레드 수입니다.|
|SuccessfullConnectionsPerSec|초당 성공한 연결 수|초당 개수|평균|성공적으로 완료된 연결 비율입니다.|
|CommandPoolBusyThreads|스레드: 명령 풀 사용 중인 스레드|카운트|평균|명령 스레드 풀의 사용 중인 스레드 수입니다.|
|CommandPoolIdleThreads|스레드: 명령 풀 유휴 상태 스레드|카운트|평균|명령 스레드 풀의 유휴 상태 스레드 수입니다.|
|LongParsingBusyThreads|스레드: 긴 구문 분석 사용 중인 스레드|카운트|평균|긴 구문 분석 스레드 풀에서 사용 중인 스레드 수입니다.|
|LongParsingIdleThreads|스레드: 긴 구문 분석 유휴 상태 스레드|카운트|평균|긴 구문 분석 스레드 풀에서 유휴 상태 스레드 수입니다.|
|LongParsingJobQueueLength|스레드: 긴 구문 분석 작업 큐 길이|카운트|평균|긴 구문 분석 스레드 풀의 큐에 있는 작업 수입니다.|
|ProcessingPoolIOJobQueueLength|스레드: 처리 중인 풀 I/O 작업 큐 길이|카운트|평균|처리 스레드 풀의 큐에 있는 I/O 작업 수입니다.|
|ProcessingPoolBusyIOJobThreads|스레드: 처리 풀 사용 중인 I/O 작업 스레드|카운트|평균|처리 스레드 풀에서 I/O 작업을 실행 중인 스레드 수입니다.|
|ProcessingPoolBusyNonIOThreads|스레드: 처리 풀 사용 중인 비-I/O 스레드|카운트|평균|처리 스레드 풀에서 비-I/O 작업을 실행 중인 스레드 수입니다.|
|ProcessingPoolIdleIOJobThreads|스레드: 처리 풀 유휴 상태 I/O 작업 스레드|카운트|평균|처리 스레드 풀에서 I/O 작업의 유휴 상태 스레드 수입니다.|
|ProcessingPoolIdleNonIOThreads|스레드: 처리 풀 유휴 상태 비-I/O 스레드|카운트|평균|비-I/O 작업 전용인 처리 스레드 풀에서 유휴 상태 스레드 수입니다.|
|QueryPoolIdleThreads|스레드: 쿼리 풀 유휴 상태 스레드|카운트|평균|처리 스레드 풀에서 I/O 작업의 유휴 상태 스레드 수입니다.|
|QueryPoolJobQueueLength|스레드: 쿼리 풀 작업 큐 길이|카운트|평균|쿼리 스레드 풀의 큐에 있는 작업 수입니다.|
|ShortParsingBusyThreads|스레드: 짧은 구문 분석 사용 중인 스레드|카운트|평균|짧은 구문 분석 스레드 풀에서 사용 중인 스레드 수입니다.|
|ShortParsingIdleThreads|스레드: 짧은 구문 분석 유휴 상태 스레드|카운트|평균|짧은 구문 분석 스레드 풀에서 유휴 상태 스레드 수입니다.|
|ShortParsingJobQueueLength|스레드: 짧은 구문 분석 작업 큐 길이|카운트|평균|짧은 구문 분석 스레드 풀의 큐에 있는 작업 수입니다.|
|TotalConnectionFailures|총 연결 실패 수|카운트|평균|실패한 총 연결 시도 수입니다.|
|TotalConnectionRequests|총 연결 요청 수|카운트|평균|도착한 총 |

## <a name="next-steps"></a>다음 단계
[Microsoft Azure의 모니터링](../monitoring-and-diagnostics/monitoring-overview.md)   
[Microsoft Azure의 메트릭](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)   
[Azure Monitor REST API의 메트릭](/rest/api/monitor/metrics)
