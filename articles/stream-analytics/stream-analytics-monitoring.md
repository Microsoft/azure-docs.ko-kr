---
title: Azure Stream Analytics에서 작업 모니터링 이해
description: 이 문서에서는 Azure Portal에서 Azure Stream Analytics 작업을 모니터링하는 방법을 설명합니다.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 6/21/2018
ms.custom: seodec18
ms.openlocfilehash: 54ff94cad3034e3679e57e5596ca13f3430cbbb3
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67340569"
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Stream Analytics 작업 모니터링 및 쿼리를 모니터링하는 방법 이해

## <a name="introduction-the-monitor-page"></a>소개: 모니터 페이지
Azure 포털 화면에는 성능 메트릭을 모니터링 하 고 쿼리 및 작업 성능 문제 해결에 사용할 수 있는 키입니다. 이러한 메트릭을 보려면 메트릭을 보려고 하는 Stream Analytics 작업으로 이동하고 개요 페이지에서 **모니터링** 섹션을 봅니다.  

![Stream Analytics 작업 모니터링 링크](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

다음과 같은 창이 표시됩니다.

![Stream Analytics 작업 모니터링 대시보드](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Stream Analytics에 사용 가능한 메트릭
| 메트릭                 | 정의                               |
| ---------------------- | ---------------------------------------- |
| 백로그된 입력 이벤트       | 백로그된 입력 이벤트의 수입니다. 이 메트릭의 값이 0이 아니면 작업이 수신 이벤트 수를 적시에 처리할 수 없음을 의미합니다. 이 값이 천천히 증가하거나 일관되게 0이 아닌 경우 작업을 확장해야 합니다. [스트리밍 단위 이해 및 조정](stream-analytics-streaming-unit-consumption.md)에서 자세히 알아볼 수 있습니다. |
| 데이터 변환 오류 | 예상 출력 스키마로 변환할 수 없는 출력 이벤트의 수입니다. 이 시나리오에서 발생하는 이벤트를 삭제하기 위해 오류 정책을 ‘삭제’로 변경할 수 있습니다. |
| 초기 입력 이벤트       | 애플리케이션 타임스탬프가 도착 시간보다 5분 넘게 이전인 이벤트입니다. |
| 실패한 기능 요청 | 실패한 Azure Machine Learning 함수 호출(있는 경우) 수입니다. |
| 함수 이벤트        | Azure Machine Learning 함수(있는 경우)에 전송된 이벤트 수입니다. |
| 기능 요청      | Azure Machine Learning 함수(있는 경우)에 대한 호출 수입니다. |
| 입력 역직렬화 오류       | 역직렬화할 수 없는 입력 이벤트 수입니다.  |
| 입력 이벤트 바이트      | Stream Analytics 작업이 받은 데이터의 양(바이트)입니다. 입력 소스로 전송되는 이벤트의 유효성을 검사하는 데 사용할 수 있습니다. |
| 입력 이벤트           | 입력 이벤트에서 역직렬화된 레코드 수입니다. 이 개수는 deserialization 오류가 발생 하는 들어오는 이벤트를 포함 하지 않습니다. |
| 수신된 입력 원본       | 작업에서 받은 메시지의 수입니다. 이벤트 허브에 대 한 메시지는 단일 EventData 됩니다. Blob에 대 한 메시지는 단일 blob을 됩니다. 입력 원본 deserialization을 수행 하기 전에 계산 된 note 하십시오. Deserialization 오류가 있는 경우에 입력된 소스 입력된 이벤트 보다 클 수 있습니다. 그렇지 않은 경우 각 메시지에는 여러 이벤트가 포함 될 수 있으므로 입력 이벤트 보다 작거나 수 있습니다. |
| 늦은 입력 이벤트      | 구성된 지연 도착 허용 시간보다 늦게 도착한 이벤트입니다. [Azure Stream Analytics 이벤트 순서 고려 사항](stream-analytics-out-of-order-and-late-events.md)에 대해 자세히 알아봅니다. |
| 순서 비지정 이벤트    | 이벤트 순서 지정 정책에 기반하여 조정된 타임스탬프를 받거나 삭제된 순서가 정해지지 않은 수신 이벤트의 수입니다. 잘못된 순서 허용 시간 설정의 구성에 의해 영향을 받을 수 있습니다. |
| 출력 이벤트          | Stream Analytics 작업이 출력 대상에 보낸 데이터의 양입니다(이벤트 수). |
| 런타임 오류         | (이벤트를 수집 하거나 결과 출력 하는 동안 발견 된 오류 제외) 하는 쿼리 처리와 관련 된 오류의 총 수 |
| SU % 사용률       | 작업의 크기 조정 탭에서 작업에 할당한 스트리밍 단위의 사용률입니다. 이 표시가 80% 이상에 도달하면 이벤트 처리가 지연되거나 진행을 중단할 가능성이 큽니다. |
| 워터마크 지연       | 작업에 있는 모든 출력의 모든 파티션에서 발생하는 최대 워터마크 지연입니다. |

이러한 메트릭을 사용하여 [Stream Analytics 작업의 성능을 모니터링](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor)할 수 있습니다. 

## <a name="customizing-monitoring-in-the-azure-portal"></a>Azure Portal에서 모니터링 사용자 지정하기
차트 편집 설정에서 차트 유형, 표시되는 메트릭 및 시간 범위를 조정할 수 있습니다. 자세한 내용은 [모니터링을 사용자 지정하는 방법](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)을 참조하세요.

  ![Stream Analytics 쿼리 모니터 시간 그래프](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>최신 출력
사용자 작업을 모니터링하기 위한 또 다른 흥미로운 데이터 요소는 개요 페이지에 표시되는 마지막 출력 시간입니다.
이 시간은 작업의 최신 출력에 대한 애플리케이션 시간(즉, 이벤트 데이터의 타임스탬프를 사용한 시간)입니다.

## <a name="get-help"></a>도움말 보기
추가 지원이 필요할 경우 [Azure Stream Analytics 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure  Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)
