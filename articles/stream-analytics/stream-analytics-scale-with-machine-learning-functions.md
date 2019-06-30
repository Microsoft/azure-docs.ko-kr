---
title: Azure Stream Analytics에서 Machine Learning 함수 크기 조정
description: 이 아티클에서는 분할 및 스트림 단위를 구성하여 Microsoft Azure Machine Learning 함수를 사용하는 Stream Analytics 작업의 크기를 조정하는 방법을 설명합니다.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 5da09d705246ffd5002a1a21daab2266525f579e
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2019
ms.locfileid: "67357506"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-functions"></a>Azure Machine Learning Studio 기능을 사용 하 여 Stream Analytics 작업 크기 조정

이 문서에서는 Azure Machine Learning 함수를 사용 하는 Azure Stream Analytics 작업을 효율적으로 확장 하는 방법을 설명 합니다. Stream Analytics 작업의 크기를 조정하는 일반적인 방법은 [작업 크기 조정](stream-analytics-scale-jobs.md)을 참조하세요.

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>Stream Analytics의 Azure Machine Learning 함수란 무엇입니까?

Stream Analytics의 Machine Learning 함수는 Stream Analytics 쿼리 언어에 일반 함수 호출처럼 사용할 수 있습니다. 그러나 내부적으로 이러한 함수 호출은 실제로 Azure Machine Learning 웹 서비스 요청을 됩니다.

동일한 웹 서비스 API 호출에서 여러 행을 "일괄 처리" 하 여 Machine Learning 웹 서비스 요청 처리량을 개선할 수 있습니다. 이 그룹화는 미니 일괄 처리 라고 합니다. 자세한 내용은 [Azure Machine Learning Studio 웹 서비스](../machine-learning/studio/consume-web-services.md)합니다. Stream Analytics에서 Azure Machine Learning Studio에 대 한 지원은 미리 보기로 제공 됩니다.

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Machine Learning 함수를 사용하여 Stream Analytics 작업 구성

Stream Analytics 작업에서 사용 되는 Machine Learning 함수를 구성 하는 두 개의 매개 변수를 가지 있습니다.

* Machine Learning 함수 호출의 일괄 처리 크기입니다.
* Su (스트리밍 단위) Stream Analytics 작업에 대 한 프로 비전 된 횟수입니다.

Su에 대 한 적절 한 값을 확인 하려면 Stream Analytics 작업의 대기 시간 또는 각 SU의 처리량을 최적화 하려는 지 여부를 결정 합니다. Su는 항상 올바른 분할 된 Stream Analytics 쿼리의 처리량을 증가 시키려면 작업에 추가할 수 있습니다. 추가 Su는 작업 실행 비용이 증가 수행 합니다.

대기 시간을 결정 *내결함성* 하 여 Stream Analytics 작업에 대 한 합니다. 일괄 처리 크기를 늘리면 Azure Machine Learning 서비스 요청의 대기 시간 및 Stream Analytics 작업의 대기 시간이 늘어납니다.

일괄 처리 크기를 늘리면 Stream Analytics 작업에서 처리할 **더 많은 이벤트가** 사용 하 여는 **만큼** Machine learning 웹 서비스 요청 합니다. Machine Learning 웹 서비스 대기 시간이 증가 했음을 나타내며 일반적으로 일괄 처리 크기에 비례해입니다. 

특정된 상황에서 Machine Learning 웹 서비스에 대 한 가장 경제적인 배치 크기를 고려 하는 것이 반드시 합니다. 웹 서비스 요청에 대 한 기본 일괄 처리 크기는 1000입니다. 사용 하 여이 기본 크기를 변경할 수 있습니다 합니다 [Stream Analytics REST API](https://docs.microsoft.com/previous-versions/azure/mt653706(v=azure.100) "Stream Analytics REST API") 나 [Stream Analytics 용 PowerShell 클라이언트](stream-analytics-monitor-and-manage-jobs-use-powershell.md)합니다.

일괄 처리 크기를 결정 한 후 Su (스트리밍 단위)를 함수 해야 하는 이벤트의 수에 따라 수를 설정할 수 있습니다 초당 처리 합니다. 스트리밍 단위에 대한 자세한 내용은 [Stream Analytics 크기 조정 작업](stream-analytics-scale-jobs.md)을 참조하세요.

6su 마다 Machine Learning 웹 서비스에 대 한 동시 연결을 20 개를 가져옵니다. 그러나 1 개 SU 작업 및 3 개 SU 작업 20 개의 동시 연결을 가져옵니다.  

응용 프로그램에서 초당 200,000 이벤트를 생성 하는 경우 일괄 처리 크기는 1000을 웹 서비스 대기 시간은 200ms입니다. 이 속도 모든 연결 요청을 수행할 수 5 Machine Learning 웹 서비스에 초당 의미 합니다. 연결이 20 개이면 Stream Analytics 작업을가에서 200ms 동안 20,000 개 이벤트 및 10만 개 이벤트를 1 초 동안에서 처리할 수 있습니다.

초당 200,000 이벤트를 처리 하려면 Stream Analytics 작업에는 12 개가 제공 되는 동시 연결 40 해야 합니다. 다음 다이어그램은 Stream Analytics 작업에서 Machine Learning 웹 서비스 엔드포인트로 전송되는 요청을 보여줍니다. 6SU마다 Machine Learning 웹 서비스에 대한 최대 동시 연결 20개가 있습니다.

![Machine Learning 함수 2 작업 예제를 사용한 Stream Analytics 크기 조정](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Machine Learning 함수 2 작업 예제를 사용한 Stream Analytics 크기 조정")

일반적으로 배치 크기가 ***B***이고, 배치 크기 B의 웹 서비스 대기 시간이 ***L***밀리초이면 SU ***N***개가 포함된 Stream Analytics 작업의 처리량은 다음과 같습니다.

![Machine Learning 함수 수식을 사용한 Stream Analytics 크기 조정](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Machine Learning 함수 수식을 사용한 Stream Analytics 크기 조정")

Machine Learning 웹 서비스의 '최대 동시 호출'도 구성할 수 있습니다. 것이 좋습니다 최대값 (현재는 200)이 매개이 변수를 설정 합니다.

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

트 윗 당 10,000 개의 트 윗의 속도로 감정 분석은 두 번째 Stream Analytics 작업을 만드는 데 필요한 구성을 살펴보겠습니다.

1SU를 사용 하 여이 Stream Analytics 작업 처리할 수 트래픽을? 작업은 기본 배치 크기인 1000 사용 하 여 입력을 사용 하 여 유지할 수 있습니다. (기본 일괄 처리 크기로 1000)을 사용한 정서 분석 Machine Learning 웹 서비스의 기본 대기 시간이 1 초 보다 더 이상 만듭니다.

Stream Analytics 작업의 **전체적인** 또는 엔드투엔드 대기 시간은 일반적으로 몇 초입니다. 이 Stream Analytics 작업, *특히* Machine Learning 함수 호출에 대해 좀 더 자세히 살펴보겠습니다. 10,000 개의 이벤트가의 처리량을 일괄 처리 크기로 1000 사용 하 여 웹 서비스를 약 10 개의 요청 소요 됩니다. 1SU를 사용하더라도 이 입력 트래픽을 처리하기에 충분한 동시 연결이 있습니다.

입력 이벤트 속도가 100배 증가하면 Stream Analytics 작업은 초당 1,000,000 트윗을 처리해야 합니다. 크기 증가를 위해 다음 두 가지 옵션을 사용할 수 있습니다.

1. 일괄 처리 크기를 늘립니다.
2. 동시에 이벤트를 처리 하려면 입력된 스트림을 분할 합니다.

첫 번째 옵션을 사용하면 작업 **대기 시간**이 증가합니다.

두 번째 옵션을 사용 하 여 더 많은 동시 Machine Learning 웹 서비스 요청을 할 더 많은 Su를 프로 비전 해야 합니다. 이 더 많은 Su 작업 증가 **비용**합니다.

각 일괄 처리 크기에 대 한 크기 조정 사용 대기 시간 측정값에 살펴보겠습니다.

| 대기 시간 | Batch 크기 |
| --- | --- |
| 200 밀리초 | 1000 이벤트 일괄 처리 또는 아래 |
| 250ms | 5,000 이벤트 배치 |
| 300 밀리초 | 10,000 이벤트 배치 |
| 500ms | 25,000 이벤트 배치 |

1. 첫 번째 옵션을 사용하면(더 많은 SU를 프로비전하지 **않음**) 일괄 처리 크기가 **25,000**으로 늘어날 수 있습니다. 이러한 방식으로 일괄 처리 크기를 늘리면 (호출당 500 밀리초의 대기 시간)으로 Machine Learning 웹 서비스에 대 한 동시 연결 20 개를 사용 하 여 1,000,000 이벤트를 처리 하는 데 작업을 수 있습니다. Machine Learning 웹 서비스 요청에 대한 감정 함수 요청으로 인해 Stream Analytics 작업의 대기 시간이 **200ms**에서 **500ms**로 증가합니다. 그러나 일괄 처리 크기 **없습니다** Machine Learning 웹 서비스 여야 요청의 페이로드 크기가 4MB 무한 또는 더 작은 크기로 늘릴 수 및 서비스 요청 제한 시간 후 작업의 100 초가 지나면 웹입니다.
1. 두 번째 옵션을 사용할 경우 배치 크기는 1000으로 유지되고 웹 서비스 대기 시간이 200ms이므로, 웹 서비스에 대한 동시 연결 20개마다 1000 * 20 * 5 이벤트 = 초당 100,000개 이벤트를 처리할 수 있습니다. 따라서 초당 1,000,000 이벤트를 처리하려면 작업에 60SU가 필요합니다. 첫 번째 옵션과 비교하면, Stream Analytics 작업에서 웹 서비스 배치 요청을 더 많이 만들며, 그로 인해 비용도 함께 증가합니다.

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

일반적으로 Machine Learning 함수에 대해 설정하는 일괄 처리 크기를 각 Stream Analytics 작업의 “끌어오기”에서 반환하는 이벤트 수로 나누어서 딱 떨어지는 경우는 별로 없습니다. 이와 같은 경우 Machine Learning 웹 서비스가 “부분” 배치를 통해 호출됩니다. 부분 일괄 처리를 사용 하 여 끌어오기에서 병합 이벤트에 추가 작업 대기 시간 오버 헤드 발생을 방지 합니다.

## <a name="new-function-related-monitoring-metrics"></a>새 함수 관련 모니터링 메트릭
Stream Analytics 작업의 모니터링 영역에 함수 관련 메트릭 세 개가 추가되었습니다. 이들은 **FUNCTION REQUESTS**, **FUNCTION EVENTS** 및 **FAILED FUNCTION REQUESTS**아래 그림에 나와 있는 것 처럼 합니다.

![Machine Learning 함수 메트릭을 사용한 Stream Analytics 크기 조정](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Machine Learning 함수 메트릭을 사용한 Stream Analytics 크기 조정")

함수는 아래와 같이 정의됩니다.

**FUNCTION REQUESTS**: 함수 요청의 수

**FUNCTION EVENTS**: 함수 요청의 이벤트 수

**FAILED FUNCTION REQUESTS**: 실패한 함수 요청의 수

## <a name="key-takeaways"></a>핵심 내용

Machine Learning 함수를 사용 하 여 Stream Analytics 작업 크기를 조정 하려면 다음 요소를 고려 합니다.

1. 입력된 이벤트 속도입니다.
2. 실행 중인 Stream Analytics 작업 (및 따라서 Machine Learning 웹 서비스 요청의 일괄 처리 크기)에 대 한 허용된 대기 시간입니다.
3. 프로 비전 된 Stream Analytics Su 및 Machine Learning 웹 서비스 요청 (추가 함수 관련 비용)의 수입니다.

이 예에서는 완전하게 분할된 Stream Analytics 쿼리가 사용되었습니다. 보다 복잡한 쿼리가 필요한 경우 [Azure Stream Analytics 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)에서 Stream Analytics 팀에게 추가 도움을 받을 수 있습니다.

## <a name="next-steps"></a>다음 단계
Stream Analytics에 대한 자세한 내용은 다음 항목을 참조하세요.

* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure  Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)
