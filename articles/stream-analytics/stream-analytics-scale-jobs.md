---
title: Azure Stream Analytics 작업에서 강화 및 확장
description: 이 아티클에서는 입력 데이터를 분할하고, 쿼리를 조정하고, 작업 스트리밍 단위를 설정하여 Stream Analytics 작업의 크기를 조정하는 방법을 설명합니다.
services: stream-analytics
author: JSeb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/22/2017
ms.openlocfilehash: f4307da2e74846507cafb9f767a6ccae855e42a2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60816806"
---
# <a name="scale-an-azure-stream-analytics-job-to-increase-throughput"></a>처리량을 높이도록 Azure Stream Analytics 작업 크기 조정
이 문서에서는 Streaming Analytics 작업에 대한 처리량을 증가시키기 위해 Stream Analytics 쿼리를 조정하는 방법을 보여 줍니다. 다음 가이드를 사용하여 더 높은 부하를 처리하고 더 많은 시스템 리소스(예: 추가 대역폭, 추가 CPU 리소스, 추가 메모리)를 활용하도록 작업 크기를 조정할 수 있습니다.
전제 조건으로 다음 문서를 읽을 필요가 있습니다.
-   [스트리밍 단위 이해 및 조정](stream-analytics-streaming-unit-consumption.md)
-   [병렬 처리 가능한 작업 만들기](stream-analytics-parallelization.md)

## <a name="case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions"></a>사례 1 - 쿼리가 기본적으로 여러 입력 파티션 간에 완전히 병렬 처리 가능한 경우
쿼리가 기본적으로 여러 입력 파티션 간에 완전히 병렬 처리가 가능하면 다음 단계를 따를 수 있습니다.
1.  **PARTITION BY** 키워드를 사용하여 쉬운 병렬 처리가 되도록 쿼리를 작성합니다. [이 페이지](stream-analytics-parallelization.md)의 쉬운 병렬 처리(Embarrassingly parallel) 작업 섹션에서 자세한 내용을 참조하세요.
2.  쿼리에 사용되는 출력 형식에 따라, 일부 출력은 병렬 처리할 수 없거나, 쉬운 병렬 처리를 위해 추가 구성이 필요할 수 있습니다. 예를 들어 SQL, SQL DW 및 PowerBI 출력은 병렬 처리가 가능하지 않습니다. 출력은 출력 싱크로 전송되기 전에 항상 병합됩니다. Blob, Tables, ADLS, Service Bus 및 Azure Function은 자동으로 병렬 처리됩니다. CosmosDB 및 이벤트 허브에서는 PartitionKey 구성이 **PARTITION BY** 필드(일반적으로 PartitionId)와 일치하도록 설정되어야 합니다. 이벤트 허브의 경우 모든 입/출력의 파티션 수가 일치하도록 하여 파티션 간에 교차가 이루어지지 않도록 특히 주의해야 합니다. 
3.  **6개 SU**(단일 컴퓨팅 노드의 전체 용량)로 쿼리를 실행하여 달성 가능한 최대 처리량을 측정하고, **GROUP BY**를 사용하는 경우 작업이 처리할 수 있는 그룹(카디널리티) 수를 측정합니다. 시스템 리소스 제한에 도달하는 작업의 일반적인 증상은 다음과 같습니다.
    - SU % 사용률 메트릭이 80%를 초과합니다. 이것은 메모리 사용량이 높음을 나타냅니다. 이 메트릭의 증가에 영향을 주는 요인은 [여기](stream-analytics-streaming-unit-consumption.md)에 설명되어 있습니다. 
    -   출력 타임스탬프가 벽 시계 시간보다 느립니다. 쿼리 논리에 따라 출력 타임스탬프와 벽 시계 시간 간에는 논리 오프셋이 있을 수 있습니다. 그러나 이들은 거의 같은 속도로 진행됩니다. 출력 타임스탬프가 점점 더 느려지면 시스템이 과부하되었다는 것을 의미합니다. 이것은 다운스트림 출력 싱크 제한이나 높은 CPU 사용률의 결과일 수 있습니다. 현재는 CPU 사용률 메트릭을 제공하지 않으므로 둘 간을 구분하는 것이 어려울 수 있습니다.
        - 이 문제가 싱크 제한으로 발생한 경우 출력 파티션의 수를 늘리거나(또한 입력 파티션의 수를 늘려 작업을 완전히 병렬 처리가 가능하도록 유지) 싱크의 리소스 양(예: CosmosDB에 대한 요청 단위 수)을 늘려야 할 수 있습니다.
    - 작업 다이어그램에는 각 입력에 대한 파티션 기준 백로그 이벤트 메트릭이 있습니다. 백로그 이벤트 메트릭이 계속 증가하는 경우에도 시스템 리소스가 제한되었다는 것을 의미할 수 있습니다(출력 싱크 제한 또는 높은 CPU 사용률이 원인).
4.  6개 SU 작업이 도달할 수 있는 제한을 확인한 후에는, SU를 더 추가하면서 작업의 처리 용량을 선형으로 추론할 수 있습니다(특정 파티션의 “작업량을 높이는” 데이터 기울이기가 없다는 가정 하).

> [!NOTE]
> 적절한 스트리밍 단위 수 선택: Stream Analytics는 각 6개의 SU가 추가될 때마다 처리 노드를 만들기 때문에 노드 수를 입력 파티션 수의 제수로 사용하여 파티션을 노드 간에 균일하게 분산하는 것이 가장 좋습니다.
> 예를 들어 6개 SU 작업이 4MB/s 처리 속도를 달성할 수 있다고 측정했고, 입력 파티션 수는 4개입니다. 약 8MB/s의 처리 속도를 얻기 위해서는 12개 SU, 16MB/s의 처리 속도를 얻기 위해서는 24개 SU의 작업을 선택하여 실행할 수 있습니다. 그런 후 입력 속도의 함수로서, 작업에 대한 SU 수를 특정 값으로 늘려야 하는 시기를 결정할 수 있습니다.


## <a name="case-2---if-your-query-is-not-embarrassingly-parallel"></a>사례 2 - 쿼리가 병렬 처리하기 쉽지 않은 경우
쿼리가 병렬 처리하기 쉽지 않은 경우, 다음 단계를 따릅니다.
1.  먼저 **PARTITION BY** 없이 쿼리를 시작하여 분할에 따른 복잡성을 피하고, [사례 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions)과 같이 최대 부하를 측정하기 위해 6개 SU로 쿼리를 실행합니다.
2.  처리량 측면에서 예상되는 부하를 달성하는 경우 완료된 것입니다. 또는 3개 SU 및 1개 SU에서 실행되는 동일한 작업을 측정하도록 선택하여 시나리오에 적합한 최소 SU 수를 알아낼 수 있습니다.
3.  원하는 처리량을 얻을 수 없고 쿼리가 아직 여러 단계로 구성되지 않은 경우 가능하면 쿼리를 여러 단계로 분리하고, 쿼리의 각 단계에 대해 최대 6개 SU를 할당합니다. 예를 들어 3개 단계가 있는 경우 “규모” 옵션에서 18개의 SU를 할당합니다.
4.  이러한 작업을 실행하는 경우, Stream Analytics는 전용 6개 SU 리소가 있는 자체 노드에 각 단계를 배치합니다. 
5.  부하 목표에 도달하지 못한 경우 입력에 가까운 단계부터 시작해서 **PARTITION BY**를 사용할 수 있습니다. 자연스럽게 분할 가능하지 않을 수 있는 **GROUP BY** 연산자의 경우, 로컬/전역 집계 패턴을 사용하여 분할된 **GROUP BY**를 수행한 후 분할되지 않은 **GROUP BY**를 수행할 수 있습니다. 예를 들어 3분 간격으로 각 요금 창구를 통과하는 차량 수를 계산하려고 하며 데이터 볼륨이 6개 SU에 의해 처리될 수 있는 수준을 초과한다고 가정합니다.

쿼리:

 ```SQL
 WITH Step1 AS (
 SELECT COUNT(*) AS Count, TollBoothId, PartitionId
 FROM Input1 Partition By PartitionId
 GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
 )
 SELECT SUM(Count) AS Count, TollBoothId
 FROM Step1
 GROUP BY TumblingWindow(minute, 3), TollBoothId
 ```
위의 쿼리에서는 파티션당 요금 창구별 차량 수를 계산한 다음, 모든 파티션의 차량 수를 합합니다.

일단 분할되면, 단계의 각 파티션에 대해 최대 6개의 SU를 할당합니다. 그러면 6개의 SU를 갖는 각 파티션이 최대값이 되므로 각 파티션은 자체 처리 노드에 배치될 수 있습니다.

> [!Note]
> 쿼리를 분할할 수 없는 경우 다단계 쿼리에 SU를 더 추가한다고 해서 항상 처리량이 개선될 수 있는 것은 아닙니다. 성능을 높이는 한 가지 방법은 위의 5단계에서 설명한 것과 같이 로컬/전역 집계 패턴을 사용하여 초기 단계에서 볼륨을 줄이는 것입니다.

## <a name="case-3---you-are-running-lots-of-independent-queries-in-a-job"></a>사례 3 - 한 작업에서 많은 양의 독립 쿼리를 실행하는 경우
한 작업에서 여러 테넌트의 데이터를 처리하는 것이 더 비용 효율적인 특정 ISV 사용 사례의 경우, 각 테넌트에 대해 별도의 입/출력을 사용하면 다수의(예: 20개) 독립 쿼리가 한 작업에서 수행될 수 있습니다. 여기서는 이러한 각 하위 쿼리 부하가 비교적 작다고 가정합니다. 이 경우 다음 단계를 수행할 수 있습니다.
1.  여기서는 쿼리에서 **PARTITION BY**를 사용하지 않도록 합니다.
2.  이벤트 허브를 사용하는 경우 입력 파티션 수를 가능한 가장 낮은 값인 2로 줄입니다.
3.  6개의 SU를 사용하여 쿼리를 실행합니다. 각 하위 쿼리에 예상된 부하가 있으면, 작업이 시스템 리소스 제한에 도달할 때까지 이러한 하위 쿼리를 최대한 많이 추가합니다. 이 경우의 증상에 대해서는 [사례 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions)을 참조하세요.
4.  위에서 측정한 하위 쿼리 제한에 도달하면 하위 쿼리를 새 작업에 추가하기 시작합니다. 독립적인 쿼리 수에 대한 함수로서, 실행할 작업의 수는 비교적 선형이며 부하 기울이기가 없다고 가정합니다. 그런 후 제공하려는 테넌트 수에 대한 함수로서, 실행해야 하는 6개 SU 작업의 수를 예측할 수 있습니다.
5.  이러한 쿼리와 함께 참조 데이터 조인을 사용할 경우 동일한 참조 데이터와 조인하기 전에 입력을 함께 통합합니다. 그런 다음, 필요한 경우 이벤트를 분할합니다. 그렇지 않은 경우, 각 참조 데이터 조인은 참조 데이터의 복사본을 메모리에 유지하여 메모리 사용량이 불필요하게 증가할 수 있습니다.

> [!Note] 
> 각 작업에 몇 개의 테넌트를 배치해야 할까요?
> 이 쿼리 패턴에는 종종 많은 수의 하위 쿼리가 있으며 매우 크고 복잡한 토폴로지가 생성됩니다. 작업의 컨트롤러는 이러한 대량의 토폴로지를 처리하지 못할 수 있습니다. 원칙적으로, 1개 SU 작업의 경우 40개의 테넌트, 3개 SU 및 6개 SU 작업의 경우 60개의 테넌트 미만으로 유지하는 것이 좋습니다. 컨트롤러의 용량을 초과하는 경우 작업이 제대로 시작되지 않습니다.



## <a name="get-help"></a>도움말 보기
추가 지원이 필요한 경우 [Azure Stream Analytics 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

<!--Link references-->

[microsoft.support]: https://support.microsoft.com
[azure.event.hubs.developer.guide]: https://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301

