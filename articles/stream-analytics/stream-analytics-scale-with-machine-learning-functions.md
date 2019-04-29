---
title: Azure Stream Analytics에서 Machine Learning 함수 크기 조정
description: 이 아티클에서는 분할 및 스트림 단위를 구성하여 Microsoft Azure Machine Learning 함수를 사용하는 Stream Analytics 작업의 크기를 조정하는 방법을 설명합니다.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: f11034a4970e3fb95333310af82a6b2a2551f1eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61479150"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-functions"></a>Azure Machine Learning 함수를 사용하여 Stream Analytics 작업의 크기 조정
Stream Analytics 작업을 설정하고 이를 통해 몇 가지 샘플 데이터를 실행하는 작업은 간단합니다. 더 큰 데이터 볼륨으로 같은 작업을 실행해야 할 때 어떻게 해야 할까요? 크기를 조정할 수 있도록 Stream Analytics 작업을 구성하는 방법을 이해해야 합니다. 이 문서에서는 Machine Learning 함수를 사용하여 Stream Analytics 작업의 크기를 조정하는 방법을 집중적으로 다루겠습니다. Stream Analytics 작업의 크기를 조정하는 일반적인 방법은 [작업 크기 조정](stream-analytics-scale-jobs.md)을 참조하세요.

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>Stream Analytics의 Azure Machine Learning 함수란 무엇입니까?
Stream Analytics의 Machine Learning 함수는 Stream Analytics 쿼리 언어에 일반 함수 호출처럼 사용할 수 있습니다. 그러나 내부를 들여다보면, 함수 호출이 실제로는 Azure Machine Learning 웹 서비스 요청입니다. Machine Learning 웹 서비스는 같은 웹 서비스 API 호출의 여러 행을 “일괄로 처리”하여 전체적인 처리량을 개선할 수 있으며, 이러한 일괄 처리를 미니 배치라고 부릅니다. 자세한 내용은 [Azure Machine Learning 웹 서비스](../machine-learning/studio/consume-web-services.md)합니다.

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Machine Learning 함수를 사용하여 Stream Analytics 작업 구성
Stream Analytics 작업에 대한 Machine Learning 함수를 구성할 때 두 가지 매개 변수를 고려해야 합니다. 하나는 Machine Learning 함수 호출의 배치 크기이고, 다른 하나는 Stream Analytics 작업에 프로비전된 SU(스트리밍 단위)입니다. SU의 적절한 값을 결정하려면 먼저 대기 시간과 처리량, 즉, Stream Analytics 작업의 대기 시간과 각 SU의 처리량을 결정해야 합니다. SU를 추가하면 작업 실행 비용이 증가하기는 하지만 언제나 SU를 작업에 추가하여 적절하게 분할된 Stream Analytics 쿼리의 처리량을 높일 수 있습니다.

따라서 Stream Analytics 작업을 실행할 때 대기 시간 *허용 오차*를 결정하는 것이 중요합니다. Azure Machine Learning 서비스 요청의 실행 대기 시간이 길어지면 당연히 배치 크기가 커지고, 결과적으로 Stream Analytics 작업의 대기 시간이 더욱 길어집니다. 반면, 배치 크기를 늘리면 Stream Analytics 작업이 *같은 수*의 Machine Learning 웹 서비스 요청으로 더 많은 이벤트*를 처리할 수 있습니다. Machine Learning 웹 서비스의 대기 시간이 배치 크기에 비례해서 거의 선형적으로 증가하는 경우가 종종 있기 때문에 주어진 상황에서 Machine Learning 웹 서비스에 가장 경제적인 배치 크기를 고려해야 합니다. 웹 서비스 요청의 기본 배치 크기는 1000이며 [Stream Analytics REST API](https://msdn.microsoft.com/library/mt653706.aspx "Stream Analytics REST API") 또는 [Stream Analytics용 PowerShell 클라이언트](stream-analytics-monitor-and-manage-jobs-use-powershell.md "Stream Analytics용 PowerShell 클라이언트")를 사용하여 수정할 수 있습니다.

배치 크기가 결정되면 함수가 초당 처리할 이벤트 수에 따라 SU(스트리밍 단위)의 수를 결정할 수 있습니다. 스트리밍 단위에 대한 자세한 내용은 [Stream Analytics 크기 조정 작업](stream-analytics-scale-jobs.md)을 참조하세요.

1SU 작업과 3SU 작업도 20개의 동시 연결을 갖는다는 점을 제외하면, 일반적으로 6SU마다 Machine Learning 웹 서비스에 대한 20개의 동시 연결이 있습니다.  예를 들어 입력 데이터 속도가 초당 200,000 이벤트이고 배치 크기를 기본값인 1000으로 두면 1000 이벤트 미니 배치가 포함된 웹 서비스 대기 시간은 200ms입니다. 즉, 모든 연결에서 Machine Learning 웹 서비스에 초당 5개의 요청을 만들 수 있습니다. 연결이 20개이면 Stream Analytics 작업에서 200ms 동안 20,000개 이벤트를 처리할 수 있으므로 초당 100,000개 이벤트를 처리할 수 있습니다. 그러므로 초당 200,000 이벤트를 처리하려면 Stream Analytics 작업에 동시 연결 40개가 필요하고, 12SU가 필요하다는 결론이 나옵니다. 다음 다이어그램은 Stream Analytics 작업에서 Machine Learning 웹 서비스 엔드포인트로 전송되는 요청을 보여줍니다. 6SU마다 Machine Learning 웹 서비스에 대한 최대 동시 연결 20개가 있습니다.

![Machine Learning 함수 2 작업 예제를 사용한 Stream Analytics 크기 조정](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Machine Learning 함수 2 작업 예제를 사용한 Stream Analytics 크기 조정")

일반적으로 배치 크기가 ***B***이고, 배치 크기 B의 웹 서비스 대기 시간이 ***L***밀리초이면 SU ***N***개가 포함된 Stream Analytics 작업의 처리량은 다음과 같습니다.

![Machine Learning 함수 수식을 사용한 Stream Analytics 크기 조정](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Machine Learning 함수 수식을 사용한 Stream Analytics 크기 조정")

Machine Learning 웹 서비스 쪽의 '최대 동시 호출'도 고려해야 합니다. 이 값을 최대 값(현재는 200)으로 설정하는 것이 좋습니다.

이 설정에 대한 자세한 내용은 [Machine Learning 웹 서비스와 관련된 크기 조정 문서](../machine-learning/studio/scaling-webservice.md)를 참조하세요.

## <a name="example--sentiment-analysis"></a>예 – 정서 분석
다음 예제는 [Stream Analytics Machine Learning 통합 자습서](stream-analytics-machine-learning-integration-tutorial.md)에 설명된 대로 정서 분석 Machine Learning 함수를 사용하는 Stream Analytics 작업을 포함하고 있습니다.

다음 예제에 표시된 대로 완전히 분할된 간단한 쿼리 다음에 **sentiment** 함수가 옵니다.

```SQL
    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery
```
처리량이 초당 10,000 트윗인 Stream Analytics 작업을 만들어서 트윗(이벤트) 정서 분석을 수행해야 하는 시나리오를 가정해 보겠습니다. 1SU를 사용하면 이 Stream Analytics 작업에서 트래픽을 처리할 수 있을까요? 기본 배치 크기인 1000을 사용하면 작업에서 입력을 처리할 수 있습니다. 뿐만 아니라 추가된 Machine Learning 함수가 생성하는 대기 시간이 정서 분석 Machine Learning 웹 서비스(배치 크기는 기본값인 1000)의 일반적인 기본 대기 시간인 1초를 초과하면 안 됩니다. Stream Analytics 작업의 **전체적인** 또는 종단 간 대기 시간은 일반적으로 몇 초입니다. 이 Stream Analytics 작업, *특히* Machine Learning 함수 호출에 대해 좀 더 자세히 살펴보겠습니다. 배치 크기가 1000이면 10,000 이벤트를 처리하기 위해 웹 서버로 약 10개의 요청을 보내야 합니다. 1SU를 사용하더라도 이 입력 트래픽을 처리하기에 충분한 동시 연결이 있습니다.

입력 이벤트 속도가 100배 증가하면 Stream Analytics 작업은 초당 1,000,000 트윗을 처리해야 합니다. 크기 증가를 위해 다음 두 가지 옵션을 사용할 수 있습니다.

1. 배치 크기를 늘립니다.
2. 입력 스트림을 분할하여 이벤트를 병렬로 처리합니다.

첫 번째 옵션을 사용하면 작업 **대기 시간**이 증가합니다.

두 번째 옵션을 사용하면 더 많은 SU를 프로비전해야 하고, 따라서 더 많은 동시 Machine Learning 웹 서비스 요청을 생성해야 합니다. 즉, 작업 **비용** 이 증가합니다.

정서 분석 Machine Learning 웹 서비스의 대기 시간이 1000개 미만 이벤트 배치에서는 200ms, 5,000개 이벤트 배치에서는 250ms, 10,000개 이벤트 배치에서는 300ms, 또는 25,000개 이벤트 배치에서는 500ms라고 가정하겠습니다.

1. 첫 번째 옵션을 사용하면(더 많은 SU를 프로비전하지 **않음**) 일괄 처리 크기가 **25,000**으로 늘어날 수 있습니다. 이렇게 하면 작업에서 Machine Learning 웹 서비스에 대한 동시 연결 20개(호출당 대기 시간 500ms)를 사용하여 1,000,000개 이벤트를 처리할 수 있습니다. Machine Learning 웹 서비스 요청에 대한 감정 함수 요청으로 인해 Stream Analytics 작업의 대기 시간이 **200ms**에서 **500ms**로 증가합니다. 그러나 배치 크기를 무한정 늘릴 수는 **없습니다**. Machine Learning 웹 서비스는 작업 후 100초가 지나면 웹 서비스 요청 시간이 초과되므로 요청의 페이로드 크기가 4MB 이하여야 하기 때문입니다.
2. 두 번째 옵션을 사용할 경우 배치 크기는 1000으로 유지되고 웹 서비스 대기 시간이 200ms이므로, 웹 서비스에 대한 동시 연결 20개마다 1000 * 20 * 5 이벤트 = 초당 100,000개 이벤트를 처리할 수 있습니다. 따라서 초당 1,000,000 이벤트를 처리하려면 작업에 60SU가 필요합니다. 첫 번째 옵션과 비교하면, Stream Analytics 작업에서 웹 서비스 배치 요청을 더 많이 만들며, 그로 인해 비용도 함께 증가합니다.

아래는 SU 및 배치 크기(초당 이벤트 수)에 따른 Stream Analytics 작업의 처리량을 보여 주는 테이블입니다.

| 배치 크기(ML 대기 시간) | 500(200ms) | 1,000(200ms) | 5,000(250ms) | 10,000(300ms) | 25,000(500ms) |
| --- | --- | --- | --- | --- | --- |
| **1SU** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **3SU** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **6SU** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **12SU** |5,000 |10000 |40,000 |60,000 |100,000 |
| **18SU** |7,500 |15,000 |60,000 |90,000 |150,000 |
| **24SU** |10000 |20,000 |80,000 |120,000 |200,000 |
| **…** |… |… |… |… |… |
| **60SU** |25,000 |50,000 |200,000 |300,000 |500,000 |

이제 Stream Analytics 작업에서 Machine Learning 함수가 어떻게 작동하는지 충분히 이해하셨을 것입니다. 또한 Stream Analytics 작업이 데이터 원본에서 데이터를 “끌어오며” 각 “끌어오기”는 Stream Analytics 작업에서 처리할 이벤트 일괄 처리를 반환한다는 내용도 이해하셨을 것입니다. 이 끌어오기 모델이 Machine Learning 웹 서비스 요청에 어떤 영향을 미칠까요?

일반적으로 Machine Learning 함수에 대해 설정하는 일괄 처리 크기를 각 Stream Analytics 작업의 “끌어오기”에서 반환하는 이벤트 수로 나누어서 딱 떨어지는 경우는 별로 없습니다. 이와 같은 경우 Machine Learning 웹 서비스가 “부분” 배치를 통해 호출됩니다. 이는 끌어오기 간에 이벤트를 병합할 때 추가 작업 대기 시간 오버헤드를 유발하지 않기 위한 조치입니다.

## <a name="new-function-related-monitoring-metrics"></a>새 함수 관련 모니터링 메트릭
Stream Analytics 작업의 모니터링 영역에 함수 관련 메트릭 세 개가 추가되었습니다. 이 세 개 함수는 아래 그림에 나와 있는 것처럼 FUNCTION REQUESTS, FUNCTION EVENTS 및 FAILED FUNCTION REQUESTS입니다.

![Machine Learning 함수 메트릭을 사용한 Stream Analytics 크기 조정](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Machine Learning 함수 메트릭을 사용한 Stream Analytics 크기 조정")

함수는 아래와 같이 정의됩니다.

**FUNCTION REQUESTS**: 함수 요청의 수

**FUNCTION EVENTS**: 함수 요청의 이벤트 수

**FAILED FUNCTION REQUESTS**: 실패한 함수 요청의 수

## <a name="key-takeaways"></a>핵심 내용
핵심을 요약하자면, Machine Learning 함수를 사용하여 Stream Analytics 작업의 크기를 조정하려면 다음 사항을 고려해야 합니다.

1. 입력 이벤트 속도
2. 실행 중인 Stream Analytics 작업에 허용되는 대기 시간(따라서 Machine Learning 웹 서비스 요청의 배치 크기)
3. 프로비전된 Stream Analytics SU 및 Machine Learning 웹 서비스 요청 수(추가 함수 관련 비용)

이 예에서는 완전하게 분할된 Stream Analytics 쿼리가 사용되었습니다. 보다 복잡한 쿼리가 필요한 경우 [Azure Stream Analytics 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)에서 Stream Analytics 팀에게 추가 도움을 받을 수 있습니다.

## <a name="next-steps"></a>다음 단계
Stream Analytics에 대한 자세한 내용은 다음 항목을 참조하세요.

* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure  Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)
