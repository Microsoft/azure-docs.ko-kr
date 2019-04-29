---
title: Azure Stream Analytics에서 스트리밍 단위 이해 및 조정
description: 이 문서에서는 Azure Stream Analytics에서 성능에 영향을 주는 스트리밍 단위 설정 및 다른 요소에 대해 설명합니다.
services: stream-analytics
author: JSeb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/12/2018
ms.openlocfilehash: 5f85f0a6b1869571a8db29586e5fe113e0f47433
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60761672"
---
# <a name="understand-and-adjust-streaming-units"></a>스트리밍 단위 이해 및 조정

SU(스트리밍 단위)는 작업을 실행하도록 할당된 컴퓨팅 리소스를 나타냅니다. SU 수가 클수록 작업에 더 많은 CPU 및 메모리 리소스가 할당됩니다. 이러한 용량을 통해 쿼리 논리에 중점을 두고 Stream Analytics 작업을 적시에 실행하도록 하드웨어를 관리해야 할 필요성을 요약할 수 있습니다.

Azure Stream Analytics 작업은 대기 시간이 짧은 스트리밍 처리를 위해 모든 처리를 메모리 안에서 수행합니다. 메모리가 부족하면 스트리밍 작업이 실패합니다. 결과적으로, 프로덕션 작업의 경우 스트리밍 작업의 리소스 사용을 모니터링하고 작업을 중단 없이 실행하기에 충분한 리소스가 할당되도록 확인해야 합니다.

0%에서 100% 범위의 SU % 사용률 메트릭은 워크로드의 메모리 사용량을 설명합니다. 최소 사용되는 스트리밍 작업의 경우 이 메트릭은 보통 10~20% 범위입니다. SU % 사용률이 낮고 입력 이벤트가 백로그되면, 워크로드에 더 많은 계산 리소스가 필요할 수 있으므로 SU 수를 늘려야 합니다. 경우에 따른 사용량 급증을 대비하여 SU 메트릭을 80% 이하로 유지하는 것이 가장 좋습니다. 리소스 소진을 방지하기 위해 80% SU 사용률 메트릭에 대한 경고를 설정하는 것이 좋습니다. 자세한 내용은 [자습서: Azure Stream Analytics 작업에 대한 경고 설정](stream-analytics-set-up-alerts.md)

## <a name="configure-stream-analytics-streaming-units-sus"></a>Stream Analytics 스트리밍 단위(SU) 구성
1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. 리소스 목록에서 확장할 Stream Analytics 작업을 찾은 후 엽니다. 

3. 작업 페이지의 **구성** 제목 아래 **크기 조정**을 선택합니다. 

    ![Azure Portal Stream Analytics 작업 구성][img.stream.analytics.preview.portal.settings.scale]
    
4. 슬라이더를 사용하여 작업에 대한 SU를 설정합니다. 특정 SU 설정으로 제한되는 것을 확인합니다. 

## <a name="monitor-job-performance"></a>작업 성능 모니터링
Azure Portal을 사용하여 작업 처리량을 추적할 수 있습니다.

![Azure Stream Analytics 모니터링 작업][img.stream.analytics.monitor.job]

워크로드의 예상 처리량을 계산합니다. 처리량이 예상보다 더 작은 경우 입력 파티션을 조정하고, 쿼리를 조정하고, 작업에 SU를 추가합니다.

## <a name="how-many-sus-are-required-for-a-job"></a>작업에 필요한 SU 수는?

특정 작업에 필요한 SU 수 선택은 입력에 대한 파티션 구성 및 작업에 정의된 쿼리에 따라 달라집니다. **비율 크기 조정** 블레이드를 사용하면 올바른 SU 수를 설정할 수 있습니다. 필요한 것보다 많은 SU를 할당하는 것이 좋습니다. Stream Analytics 처리 엔진은 메모리를 추가로 할당하는 비용으로 대기 시간과 처리량을 최적화합니다.

일반적으로 **파티션 기준**을 사용하지 않는 쿼리에 대해 6 SU로 시작하는 것이 좋습니다. 그런 다음 대표적인 데이터 양을 전달하고 SU % 사용률 메트릭을 시험한 후 SU 수를 수정하는 평가판 및 오류 메서드를 사용하여 가장 적절한 부분을 판단합니다. Stream Analytics 작업에 사용될 수 있는 최대 스트리밍 단위 수는 작업에 대해 정의된 쿼리의 단계 수와 각 단계에 대한 파티션 수에 따라 결정됩니다. 이러한 제한에 대한 자세한 내용은 [여기](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#calculate-the-maximum-streaming-units-of-a-job)에서 확인할 수 있습니다.

적절한 SU 수를 선택하는 방법에 대한 자세한 내용은 다음 페이지를 참조하세요. [처리량을 높이기 위한 Azure Stream Analytics 작업 비율 크기 조정](stream-analytics-scale-jobs.md)

> [!Note]
> 특정 작업에 필요한 SU 수 선택은 입력에 대한 파티션 구성 및 작업에 정의된 쿼리에 따라 달라집니다. 작업에 대해 SU의 할당량까지 선택할 수 있습니다. 기본적으로 각 Azure 구독에는 특정 지역의 모든 분석 작업에 대해 최대 200개의 SU 할당량이 있습니다. 구독의 SU를 이 할당량을 초과하여 늘리려면 [Microsoft 지원](https://support.microsoft.com)에 문의하세요. 작업당 SU에 대한 유효한 값은 1, 3, 6이며 6 단위로 증가합니다.

## <a name="factors-that-increase-su-utilization"></a>SU% 사용률이 증가하는 요인 

temporal(시간 지향적인) 쿼리 요소는 Stream Analytics에서 제공하는 상태 저장 연산자의 핵심 집합입니다. Stream Analytics는 서비스 업그레이드 중 메모리 소비, 복원력 검사점 및 상태 복구를 관리하여 사용자 대신 내부적으로 이러한 작업의 상태를 관리합니다. Stream Analytics가 상태를 완벽하게 관리하더라도, 사용자가 고려해야 할 몇 가지 모범 사례 권장 사항이 있습니다.

복잡한 쿼리 논리를 사용하는 작업은 입력 이벤트를 지속적으로 수신하지 않더라도 높은 SU% 사용률을 나타낼 수 있습니다. 입력 및 출력 이벤트가 급격히 증가한 후에 이러한 현상이 발생할 수 있습니다. 쿼리가 복잡한 경우 작업은 메모리의 상태를 계속 유지할 수 있습니다.

SU% 사용률은 잠시 동안 갑자기 0으로 떨어졌다가 정상 수준으로 돌아올 수도 있습니다. 이러한 현상은 일시적인 오류 또는 시스템에서 시작한 업그레이드로 인해 발생합니다.

## <a name="stateful-query-logicin-temporal-elements"></a>temporal 요소의 상태 저장 쿼리 논리
Azure Stream Analytics 작업의 고유한 기능 중 하나는 기간 이동 집계, 임시 조인 및 임시 분석 함수 등과 같은 상태 저장 처리를 수행하는 것입니다. 이러한 연산자마다 상태 정보를 유지합니다. 이러한 쿼리 요소의 최대 시간 범위는 7일입니다. 

temporal 시간 범위 개념은 몇 가지 Stream Analytics 쿼리 요소에 나타납니다.
1. 기간 이동 집계: 연속, 도착 및 슬라이딩 시간 범위의 GROUP BY

2. temporal 조인: DATEDIFF 함수를 사용한 JOIN

3. temporal 분석 함수: LIMIT DURATION이 있는 ISFIRST, LAST 및 LAG

다음 요소는 Stream Analytics 작업에 사용되는 메모리(스트리밍 단위 메트릭의 일부)에 영향을 줍니다.

## <a name="windowed-aggregates"></a>기간 이동 집계
기간 이동 집계에 소비되는 메모리(상태 크기)가 시간 범위 크기에 항상 비례하지는 않습니다. 대신 소비되는 메모리는 데이터의 카디널리티 또는 각 시간 범위의 그룹 수에 비례합니다.


예를 들어 다음 쿼리에서 `clusterid`와 연결된 수는 쿼리의 카디널리티입니다. 

   ```sql
   SELECT count(*)
   FROM input 
   GROUP BY  clusterid, tumblingwindow (minutes, 5)
   ```

앞의 쿼리에서 카디널리티가 커서 발생한 문제를 개선하기 위해 `clusterid`로 분할된 Event Hubs에 이벤트를 보내고 아래 예제에서처럼 **PARTITION BY**를 사용하여 개별적으로 각 입력 파티션을 처리할 수 있습니다.

   ```sql
   SELECT count(*) 
   FROM input PARTITION BY PartitionId
   GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)
   ```

쿼리가 분할되면 여러 노드에 걸쳐 분산됩니다. 결과적으로, 각 노드로 들어오는 `clusterid` 값의 수가 감소하여 GROUP BY 연산자 카디널리티가 줄어듭니다. 

이벤트 허브 파티션은 단계를 줄이지 않아도 되도록 그룹 키로 분할되어야 합니다. 자세한 내용은 [Event Hubs 개요](../event-hubs/event-hubs-what-is-event-hubs.md)를 참조하세요. 

## <a name="temporal-joins"></a>temporal 조인
temporal 조인의 메모리 소비(상태 크기)는 조인의 temporal 위글 공간의 이벤트 수에 비례합니다. 즉 이벤트 입력 속도와 위글 공간 크기의 곱입니다. 즉, 조인에 의해 소비되는 메모리는 DateDiff 시간 범위에 평균 이벤트 속도를 곱한 값에 비례합니다.

조인에서 일치하지 않는 이벤트 수는 쿼리에 대한 메모리 사용률에 영향을 줍니다. 다음 쿼리는 클릭을 유도하는 광고 노출을 찾는 것입니다.

   ```sql
   SELECT clicks.id
   FROM clicks 
   INNER JOIN impressions ON impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.
   ```

이 예제에서 광고는 많은 데 사용자가 거의 클릭하지 않는 경우 모든 이벤트를 timewindow에 있도록 해야 합니다. 사용된 메모리는 창 크기와 이벤트 속도에 비례합니다. 

이를 조치하기 위해 조인 키로 분할된 이벤트 허브에 이벤트를 보내고 다음과 같이 시스템이 **PARTITION BY**를 사용하여 별도로 각각의 입력 파티션을 처리할 수 있게 쿼리를 확장합니다.

   ```sql
   SELECT clicks.id
   FROM clicks PARTITION BY PartitionId
   INNER JOIN impressions PARTITION BY PartitionId 
   ON impression.PartitionId = clicks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
   ```

쿼리가 분할되면 여러 노드에 걸쳐 분산됩니다. 결과적으로, 각 노드로 들어오는 이벤트 수가 감소하여 조인 창에 유지되는 상태 크기가 줄어듭니다. 

## <a name="temporal-analytic-functions"></a>temporal 분석 함수
temporal 분석 함수의 소비 메모리(상태 크기)는 이벤트 속도와 기간의 곱에 비례합니다. 분석 함수가 소비하는 메모리는 시간 범위의 크기에 비례하지 않고 각 시간 범위의 파티션 수에 비례합니다.

조치는 임시 조인과 유사합니다. **PARTITION BY**를 사용하여 쿼리를 확장할 수 있습니다. 

## <a name="out-of-order-buffer"></a>잘못된 버퍼 
이벤트 순서 지정 구성 창에서 잘못된 버퍼 크기를 구성할 수 있습니다. 이 버퍼는 창의 기간에 대한 입력을 유지하고 순서를 재지정하는 데 사용됩니다. 버퍼 크기는 이벤트 입력 비율과 잘못된 창 크기의 곱에 비례합니다. 기본 창 크기는 0입니다. 

순서가 잘못된 버퍼의 오버플로를 수정하려면 **PARTITION BY**를 사용하여 쿼리를 확장합니다. 쿼리가 분할되면 여러 노드에 걸쳐 분산됩니다. 결과적으로, 각 노드로 들어오는 이벤트 수가 감소하여 각각의 순서 재지정 버퍼의 이벤트 수가 줄어듭니다. 

## <a name="input-partition-count"></a>입력 분할 개수  
작업 입력의 각 입력 파티션에는 버퍼가 있습니다. 입력 파티션 수가 클수록 이 작업이 더 많은 리소스를 소비합니다. 각 스트리밍 단위에 대해 Azure Stream Analytics는 대략 1 MB/s의 입력을 처리할 수 있습니다. 따라서 Stream Analytics 스트리밍 단위 수를 Event Hubs의 파티션 수와 일치 시켜서 최적화할 수 있습니다. 

일반적으로 하나의 스트리밍 유닛으로 구성된 작업은 두 개의 파티션이 있는 Event Hub(Event Hub의 경우 최소)로 충분합니다. Event Hub에 더 많은 파티션이 있으면 Stream Analytics 작업이 더 많은 리소스를 소비하지만 Event Hub에서 제공한 추가적인 처리량을 반드시 사용하는 것은 아닙니다. 

스트리밍 단위가 6인 작업의 경우 Event Hub에서 4 또는 8개 파티션이 필요할 수 있습니다. 그러나 불필요한 파티션을 너무 많이 사용하면 과도한 리소스 사용이 발생하므로 피해야 합니다. 스트리밍 단위가 1인 Stream Analytics 작업에서 16개 이상의 파티션이 있는 Event Hub가 그러한 예입니다. 

## <a name="reference-data"></a>참조 데이터 
ASA의 참조 데이터는 빠른 조회를 위해 메모리에 로드됩니다. 현재 구현에서는 동일한 참조 데이터를 여러 번 조인하더라도 참조 데이터와의 각 조인 작업이 메모리에 참조 데이터의 사본을 유지합니다. **PARTITION BY**를 통한 쿼리의 경우 각 파티션은 참조 데이터의 사본을 유지하므로 파티션이 완전히 분리됩니다. 참조 데이터를 여러 파티션과 여러 차례 조인할 경우 승수 효과에 따라 메모리 사용이 갑자기 급증할 수 있습니다.  

### <a name="use-of-udf-functions"></a>UDF 함수 사용
UDF 함수를 추가하면 Azure Stream Analytics가 JavaScript 런타임을 메모리에 로드합니다. 이것이 SU%에 영향을 주게 됩니다.

## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics에서 병렬 처리 가능 쿼리 만들기](stream-analytics-parallelization.md)
* [처리량을 높이기 위한 Azure Stream Analytics 작업 비율 크기 조정](stream-analytics-scale-jobs.md)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   
