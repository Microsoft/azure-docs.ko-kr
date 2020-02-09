---
title: 시계열 데이터에서 변칙 탐지기 API를 사용 하는 방법
titleSuffix: Azure Cognitive Services
description: 일괄 처리 또는 스트리밍 데이터에서 데이터의 변칙을 검색 하는 방법에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: aahi
ms.openlocfilehash: ca93de71f64efaf21c78b37b9c9aee193d13b28d
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71840217"
---
# <a name="how-to-use-the-anomaly-detector-api-on-your-time-series-data"></a>방법: 시계열 데이터에서 변칙 탐지기 API 사용  

[변칙 탐지기 API](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) 는 두 가지 변칙 검색 방법을 제공 합니다. 시간 계열 전체의 일괄 처리로 비정상을 검색 하거나, 최신 데이터 요소의 변칙 상태를 검색 하 여 데이터를 생성할 수 있습니다. 검색 모델은 각 데이터 요소의 예상 값과 상위 및 낮은 변칙 검색 경계와 함께 이상 결과를 반환 합니다. 이러한 값을 사용 하 여 일반 값의 범위와 데이터의 이상 값을 시각화할 수 있습니다.

## <a name="anomaly-detection-modes"></a>변칙 검색 모드 

변칙 탐지기 API는 검색 모드 (일괄 처리 및 스트리밍)를 제공 합니다.

> [!NOTE]
> 다음 요청 Url은 구독에 적합 한 엔드포인트과 결합 되어야 합니다. 예: `https://<your-custom-subdomain>.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/entire/detect`


### <a name="batch-detection"></a>일괄 검색

지정 된 시간 범위 동안 데이터 요소 일괄 처리에서 변칙을 검색 하려면 시계열 데이터에 다음 요청 URI를 사용 합니다. 

`/timeseries/entire/detect`. 

시계열 데이터를 한 번에 전송 하면 API는 전체 계열을 사용 하 여 모델을 생성 하 고 각 데이터 요소를 분석 합니다.  

### <a name="streaming-detection"></a>스트리밍 검색

스트리밍 데이터에 대 한 변칙을 지속적으로 감지 하려면 최신 데이터 요소와 함께 다음 요청 URI를 사용 합니다. 

`/timeseries/last/detect'`. 

데이터를 생성할 때 새 데이터 요소를 전송 하 여 실시간으로 데이터를 모니터링할 수 있습니다. 전송 하는 데이터 요소를 사용 하 여 모델이 생성 되 고 API는 시계열의 최신 시점이 비정상 인지 여부를 확인 합니다.

## <a name="adjusting-lower-and-upper-anomaly-detection-boundaries"></a>하위 및 상위 변칙 검색 경계 조정

기본적으로 변칙 검색의 상한 및 하 한 경계는 `expectedValue`, `upperMargin` 및 `lowerMargin`를 사용 하 여 계산 됩니다. 다른 경계가 필요한 경우 `marginScale`을 `upperMargin` 또는 `lowerMargin`에 적용 하는 것이 좋습니다. 경계는 다음과 같이 계산 됩니다.

|상한을  |계산  |
|---------|---------|
|`upperBoundary` | `expectedValue + (100 - marginScale) * upperMargin`        |
|`lowerBoundary` | `expectedValue - (100 - marginScale) * lowerMargin`        |

다음 예에서는 서로 다른 구분에서 변칙 탐지기 API 결과를 보여 줍니다.

### <a name="example-with-sensitivity-at-99"></a>99의 민감도를 포함 하는 예제

![기본 민감도](../media/sensitivity_99.png)

### <a name="example-with-sensitivity-at-95"></a>95의 민감도를 포함 하는 예제

![99 민감도](../media/sensitivity_95.png)

### <a name="example-with-sensitivity-at-85"></a>85의 민감도를 포함 하는 예제

![85 민감도](../media/sensitivity_85.png)

## <a name="next-steps"></a>다음 단계

* [변칙 탐지기 API 란 무엇 인가요?](../overview.md)
* [빠른 시작: Anomaly Detector REST API를 사용하여 시계열 데이터에서 변칙 검색](../quickstarts/detect-data-anomalies-csharp.md)
