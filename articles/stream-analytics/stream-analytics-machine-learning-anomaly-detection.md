---
title: Azure Stream Analytics의 변칙 검색
description: 이 아티클에서는 Azure Stream Analytics 및 Azure Machine Learning을 함께 사용하여 변칙을 검색하는 방법을 설명합니다.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 88c0aea851bcf70206b5f68d7865c487441905f6
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329909"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Azure Stream Analytics의 변칙 검색

클라우드와 Azure IoT Edge 둘 다에서 사용할 수 있는 Azure Stream Analytics는 가장 일반적으로 발생하는 두 가지 변칙(임시 및 영구적)을 모니터링하는 데 사용할 수 있는 기계 학습 기반의 기본 제공 변칙 검색 기능을 제공합니다. **AnomalyDetection_SpikeAndDip** 및 **AnomalyDetection_ChangePoint** 함수를 사용하여 Stream Analytics 작업에서 직접 변칙 검색을 수행할 수 있습니다.

기계 학습 모델은 균일하게 샘플링된 시계열을 가정합니다. 시계열이 균일하지 않으면 변칙 검색을 호출하기 전에 연속 창을 사용하여 집계 단계를 삽입할 수 있습니다.

Machine learning 작업 지금은 계절성 추세 또는 다중 variate 상관 관계를 지원 하지 않습니다.

## <a name="model-accuracy-and-performance"></a>모델 정확도 및 성능

일반적으로 모델 정확도는 슬라이딩 윈도우의 데이터가 많을수록 향상됩니다. 지정된 슬라이딩 윈도우의 데이터는 해당 시간 프레임 동안 정상 범위의 일부로 처리됩니다. 모델은 슬라이딩 윈도우의 이벤트 기록만 고려하여 현재 이벤트가 비정상인지 확인합니다. 슬라이딩 윈도우가 이동하면 이전 값이 모델 학습에서 제거됩니다.

함수는 지금까지 확인한 내용을 기반으로 해서 특정 표준을 설정하여 작동합니다. 신뢰 수준 내에서 설정된 표준과 비교하여 이상값을 식별합니다. 변칙이 발생할 경우 인식할 수 있도록 정상 동작에 대한 모델 학습에 필요한 최소 이벤트 수를 기준으로 윈도우 크기를 설정해야 합니다.

지난 이벤트의 더 큰 값을 비교 하기 때문에 기록 크기를 사용 하 여 모델의 응답 시간이 증가 합니다. 성능 향상을 위해 필요한 개수의 이벤트만 포함하는 것이 좋습니다.

시계열의 간격은 모델이 특정 시점에 이벤트를 수신하지 않아서 발생하는 결과일 수 있습니다. 이 경우 대체 논리를 사용 하 여 Stream Analytics에 의해 처리 됩니다. 동일한 슬라이딩 윈도우의 기간뿐만 아니라 기록 크기는 이벤트가 도착하는 예상 평균 속도를 계산하는 데 사용됩니다.

## <a name="spike-and-dip"></a>급증 및 급감

시계열 이벤트 스트림의 일시적인 변칙을 급증 및 급감이라고 합니다. 급증 및 급감은 기계 학습 기반 연산자인 [AnomalyDetection_SpikeAndDip](https://docs.microsoft.com/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
)을 사용하여 모니터할 수 있습니다.

![급증 및 급감 변칙의 예](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

동일한 슬라이딩 윈도우에서 두 번째 급증이 첫 번째 급증보다 작을 경우 더 작은 급증에 대해 계산된 점수가 지정된 신뢰 수준 내에서 첫 번째 급증의 점수와 비교하여 유의미하지 않을 수 있습니다. 이러한 변칙을 검색 하는 모델의 신뢰도 줄이면 시도할 수 있습니다. 그러나 너무 많은 경고를 받기 시작하면 더 높은 신뢰 구간을 사용할 수 있습니다.

다음 예제 쿼리는 2분 슬라이딩 윈도우에서 초당 1개 이벤트의 균일한 입력 속도와 120개 이벤트의 기록을 가정합니다. 최종 SELECT 문은 신뢰 수준 95%로 점수 및 변칙 상태를 추출하고 출력합니다.

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_SpikeAndDip(CAST(temperature AS float), 95, 120, 'spikesanddips')
            OVER(LIMIT DURATION(second, 120)) AS SpikeAndDipScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'Score') AS float) AS
    SpikeAndDipScore,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'IsAnomaly') AS bigint) AS
    IsSpikeAndDipAnomaly
INTO output
FROM AnomalyDetectionStep
```

## <a name="change-point"></a>변경 지점

시계열 이벤트 스트림의 영구적 변칙은 수준 변경 및 추세와 같은 이벤트 스트림의 값 분산 변경입니다. Stream Analytics에서 이러한 변칙은 Machine Learning 기반의 [AnomalyDetection_ChangePoint](https://docs.microsoft.com/stream-analytics-query/anomalydetection-changepoint-azure-stream-analytics) 연산자를 사용하여 검색됩니다.

영구적 변경은 급증 및 급감보다 훨씬 오래 지속되며 치명적인 이벤트를 나타낼 수 있습니다. 일반적으로 영구적 변경은 표시되지 않지만 **AnomalyDetection_ChangePoint** 연산자를 사용하여 검색할 수 있습니다.

다음 이미지는 수준 변경의 예입니다.

![수준 변경 변칙의 예](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-level-change.png)

다음 이미지는 추세 변경의 예입니다.

![추세 변경 변칙의 예](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-trend-change.png)

다음 예제 쿼리는 20분 슬라이딩 윈도우에서 초당 1개 이벤트의 균일한 입력 속도와 1200개 이벤트의 기록 크기를 가정합니다. 최종 SELECT 문은 신뢰 수준 80%로 점수 및 변칙 상태를 추출하고 출력합니다.

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_ChangePoint(CAST(temperature AS float), 80, 1200) 
        OVER(LIMIT DURATION(minute, 20)) AS ChangePointScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(ChangePointScores, 'Score') AS float) AS
    ChangePointScore,
    CAST(GetRecordPropertyValue(ChangePointScores, 'IsAnomaly') AS bigint) AS
    IsChangePointAnomaly
INTO output
FROM AnomalyDetectionStep

```

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>Machine learning을 사용 하 여 Azure Stream Analytics의 변칙 검색

다음 비디오는 Azure Stream Analytics에서 machine learning 함수를 사용 하 여 실시간으로에서 변칙을 검색 하는 방법을 보여 줍니다. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Anomaly-detection-using-machine-learning-in-Azure-Stream-Analytics/player]

## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure  Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.ASpx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)

