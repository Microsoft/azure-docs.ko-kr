---
title: Azure Stream Analytics의 데이터 기반 디버깅
description: 이 문서에서는 Azure Portal의 작업 다이어그램과 메트릭을 사용하여 Azure Stream Analytics 작업의 문제를 해결하는 방법에 대해 설명합니다.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/01/2017
ms.openlocfilehash: 4a6d359b27b9a2e52d71ed5f8547041645147605
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61479969"
---
# <a name="data-driven-debugging-by-using-the-job-diagram"></a>작업 다이어그램을 사용하여 데이터 기반 디버그

Azure Portal의 **모니터링** 블레이드에 있는 작업 다이어그램은 작업 파이프라인을 시각화하는 데 도움이 될 수 있습니다. 입력, 출력 및 쿼리 단계를 보여 줍니다. 작업 다이어그램을 사용하여 각 단계의 메트릭을 검사하면 문제를 해결할 때 문제의 원인을 더 빠르게 격리할 수 있습니다.

## <a name="using-the-job-diagram"></a>작업 다이어그램 사용

Azure Portal에서 Stream Analytics 작업 동안에 **지원 + 문제 해결**에서 **작업 다이어그램**을 선택합니다.

![메트릭이 있는 작업 다이어그램 - 위치](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-1.png)

쿼리 편집 창에서 각 쿼리 단계를 선택하여 해당하는 섹션을 확인합니다. 단계에 해당하는 메트릭 차트가 페이지 아래쪽 창에 표시됩니다.

![메트릭이 있는 작업 다이어그램 - 기본 작업](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-2.png)

Azure Event Hubs 입력의 파티션을 보려면 **. . .** 을 선택합니다. 상황에 맞는 메뉴가 나타납니다. 또한 입력된 합병을 볼 수 있습니다.

![메트릭이 있는 작업 다이어그램 - 파티션 확장](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-3.png)

단일 파티션에만 해당하는 메트릭 차트를 보려면 파티션 노드를 선택합니다. 메트릭이 페이지의 맨 아래에 표시됩니다.

![메트릭이 있는 작업 다이어그램 - 더 많은 메트릭](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-4.png)

병합에 대한 메트릭 차트를 보려면 병합 노드를 선택합니다. 다음 차트는 이벤트가 삭제되거나 조정되지 않았음을 보여 줍니다.

![메트릭이 있는 작업 다이어그램 - 그리드](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-5.png)

메트릭 값과 시간에 대한 세부 정보를 보려면 차트를 가리킵니다.

![메트릭이 있는 작업 다이어그램 - 커서 올리기](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-6.png)

## <a name="troubleshoot-by-using-metrics"></a>메트릭을 사용하여 문제 해결

**QueryLastProcessedTime** 메트릭은 특정 단계가 데이터를 수신하는 시간을 나타냅니다. 토폴로지를 찾아 출력 프로세서부터 거꾸로 작업하여 어떤 단계가 데이터를 수신하지 않는지 확인할 수 있습니다. 단계가 데이터를 수신하지 않은 경우 바로 전 쿼리 단계로 이동합니다. 이전 쿼리 단계에 시간 창이 있는지, 그리고 데이터가 출력되기에 충분한 시간이 경과하였는지를 확인합니다. (시간 창은 시간으로 스냅됨)
 
이전 쿼리 단계가 입력 프로세서인 경우 입력 메트릭을 사용하여 다음과 같은 대상 질문에 응답합니다. 이는 입력 소스에서 데이터를 가져오는 작업인지 판단하는 데 유용합니다. 쿼리가 분할된 경우 각 파티션을 검사합니다.
 
### <a name="how-much-data-is-being-read"></a>얼마나 많은 데이터를 읽습니까?

*   **InputEventsSourcesTotal**은 읽는 데이터 단위 수입니다. 예를 들어 Blob의 수입니다.
*   **InputEventsTotal**은 읽는 이벤트의 수입니다. 이 메트릭은 각 파티션에 사용할 수 있습니다.
*   **InputEventsInBytesTotal**은 읽는 바이트 수입니다.
*   **InputEventsLastArrivalTime**은 수신된 모든 이벤트의 큐에 넣은 시간과 함께 업데이트됩니다.
 
### <a name="is-time-moving-forward-if-actual-events-are-read-punctuation-might-not-be-issued"></a>시간은 앞으로 진행됩니까? 실제 이벤트를 읽는 경우 문장 부호가 발생하지 않을 수 있습니다.

*   **InputEventsLastPunctuationTime**은 시간이 앞으로 진행하도록 문장 부호가 발생한 경우를 나타냅니다. 문장 부호가 발생하지 않는 경우 데이터 흐름을 차단할 수 있습니다.
 
### <a name="are-there-any-errors-in-the-input"></a>입력에 오류가 있습니까?

*   **InputEventsEventDataNullTotal**은 null 데이터가 있는 이벤트의 개수입니다.
*   **InputEventsSerializerErrorsTotal**은 올바르게 deserialize할 수 없는 이벤트의 개수입니다.
*   **InputEventsDegradedTotal**은 deserialization이 아닌 다른 문제가 있는 이벤트의 개수입니다.
 
### <a name="are-events-being-dropped-or-adjusted"></a>이벤트 삭제되거나 조정됩니까?

*   **InputEventsEarlyTotal**은 상위 워터마크 전의 애플리케이션 타임스탬프가 있는 이벤트 수입니다.
*   **InputEventsLateTotal**은 상위 워터마크 후의 애플리케이션 타임스탬프가 있는 이벤트 수입니다.
*   **InputEventsDroppedBeforeApplicationStartTimeTotal**은 작업 시작 시간 전에 삭제된 숫자 이벤트입니다.
 
### <a name="are-we-falling-behind-in-reading-data"></a>데이터를 읽을 때 뒤쳐지고 있습니까?

*   **총 백로그된 입력 이벤트 수**는 Event Hubs 및 Azure IoT Hub 입력에 대해 얼마나 많은 메시지를 읽어야 하는지 알려줍니다. 이 숫자가 0보다 크면 작업이 데이터가 들어오는 속도만큼 빠르게 데이터를 처리할 수 없다는 의미입니다. 이 경우 스트리밍 단위 수를 늘리거나 작업을 병렬 처리할 수 있도록 해야 합니다. 이에 대한 자세한 내용은 [쿼리 병렬화 페이지](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization)를 참조하세요. 


## <a name="get-help"></a>도움말 보기
추가 지원이 필요한 경우 [Azure Stream Analytics 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)을 참조하세요. 

## <a name="next-steps"></a>다음 단계
* [Stream Analytics 소개](stream-analytics-introduction.md)
* [Stream Analytics 시작](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics 작업 크기 조정](stream-analytics-scale-jobs.md)
* [Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)
