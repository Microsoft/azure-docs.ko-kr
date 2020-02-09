---
title: 시계열 변칙 검색 & 예측-Azure 데이터 탐색기
description: Azure 데이터 탐색기를 사용 하 여 변칙 검색 및 예측에 대한 시계열 데이터를 분석 하는 방법을 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: 4a7463d6149a921a4a29b43eaebb78a01543323a
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985828"
---
# <a name="anomaly-detection-and-forecasting-in-azure-data-explorer"></a>Azure 데이터 탐색기의 변칙 검색 및 예측

Azure 데이터 탐색기는 클라우드 서비스 또는 IoT 장치에서 원격 분석 데이터를 지속적으로 수집 합니다. 이 데이터는 서비스 상태 모니터링, 물리적 프로덕션 프로세스, 사용 추세 및 부하 예측 등의 다양 한 통찰력을 위해 분석 됩니다. 분석은 선택 된 메트릭의 시계열에서 수행 되어 일반적인 일반 기준선 패턴을 기준으로 메트릭의 편차 패턴을 찾습니다. Azure 데이터 탐색기에는 여러 시계열의 생성, 조작 및 분석을 위한 기본 지원이 포함 되어 있습니다. 몇 초 안에 수천 개의 시계열을 만들고 분석할 수 있으며 거의 실시간으로 모니터링 하는 솔루션 및 워크플로를 사용할 수 있습니다.

이 문서에서는 Azure 데이터 탐색기 time series 변칙 검색 및 예측 기능을 자세히 설명 합니다. 적용 가능한 시계열 함수는 원래의 시계열이 계절, 추세 및 잔여 구성 요소로 분해 되는 강력한 잘 알려진 분해 모델을 기반으로 합니다. 이상 값은 잔여 구성 요소에서 이상 값으로 검색 되며, 예측은 계절 및 추세 구성 요소를 추정 하 여 수행 됩니다. Azure 데이터 탐색기 구현은 자동 계절성 검색, 강력한 이상 분석 및 벡터화 구현을 통해 기본 분해 모델을 크게 향상 시켜 몇 초만에 수천 개의 시계열을 처리할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

시계열 기능 개요는 [Azure 데이터 탐색기에서 시계열 분석](/azure/data-explorer/time-series-analysis) 을 참조 하세요.

## <a name="time-series-decomposition-model"></a>시계열 분해 모델

시계열 예측에 대한 Azure 데이터 탐색기 네이티브 구현 및 변칙 검색은 잘 알려진 분해 모델을 사용 합니다. 이 모델은 주기적으로 매니페스트 되어야 하는 시계열의 시계열 및 추세 동작 (예: 서비스 트래픽, 구성 요소 하트 비트 및 IoT 정기적 측정)에 적용 되어 향후 메트릭 값을 예측 하 고 비정상적인 항목을 검색 합니다. 이 회귀 프로세스의 가정은 이전에 알려진 계절 및 추세 동작을 제외 하 고 시계열은 무작위로 분산 된다는 것입니다. 그런 다음 계절 및 추세 구성 요소에서 앞으로 명명 된 기준선의 메트릭 값을 예측 하 고 나머지 부분은 무시할 수 있습니다. 잔여 부분만 사용 하 여 이상 값 분석을 기반으로 비정상 값을 검색할 수도 있습니다.
분해 모델을 만들려면 [`series_decompose()`](/azure/kusto/query/series-decomposefunction)함수를 사용 합니다. `series_decompose()` 함수는 일련의 시계열을 사용 하 고 각 시계열을 계절, 추세, 잔여 및 기준 구성 요소로 자동으로 분해 합니다. 

예를 들어 다음 쿼리를 사용 하 여 내부 웹 서비스의 트래픽을 분해할 수 있습니다.

**\[** [**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WQ3WrDMAyF7/sUukvCnDXJGIOVPEULuwxqoixm/gm2+jf28JObFjbYrmyho3M+yRCD1a5jaGFAJtaW8qaqX8qqLqvnYrMySYHnvxRNWT1B07xW1U03JFEzbVYDWd9Z/KAuUtAUm9UXpLJcSnAH2+LxPZe3AO9gJ6ZbRjvDGLy9EbG/BUemOXnvLxD1AOJ1mijQtWhbyHbbOgOA9RogkqGeAaXn3g1BooVb6OiDNHpD6CjAUccDGv2JrL0TSzozuQHyPYqHdqRkDKN3aBRwkJaCQJIoQ4VsuXh2A/Xezj5SWkVBWSvI0vSoOSsWpLtEpyDwY4KTW8nnJ5ws+2+eAhSyOxjkd+HDVVcIfHplp2TYTxgYTpqnnDUbarM32gPO86PY4jjqfmGw3vGkftNlCi5xNprbWW5kYvENQQnqDh8CAAA=) **\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (baseline, seasonal, trend, residual) = series_decompose(num, -1, 'linefit')  //  decomposition of a set of time series to seasonal, trend, residual, and baseline (seasonal+trend)
| render timechart with(title='Web app. traffic of a month, decomposition', ysplit=panels)
```

![시계열 분해](media/anomaly-detection/series-decompose-timechart.png)

* 원본 시계열의 레이블은 **num** (빨간색)으로 지정 됩니다. 
* 이 프로세스는 [`series_periods_detect()`](/azure/kusto/query/series-periods-detectfunction) 함수를 사용 하 여 계절성를 자동으로 검색 하 고 **계절** 패턴 (자주색)을 추출 합니다.
* 계절 패턴은 원래 시계열에서 빼서 선형 회귀는 함수 [`series_fit_line()`](/azure/kusto/query/series-fit-linefunction) 를 사용 하 여 실행 되어 **추세** 구성 요소 (연한 파란색)를 찾습니다.
* 함수는 추세를 빼고 **나머지는 나머지 구성 요소** (녹색)입니다.
* 마지막으로 함수는 계절 및 추세 구성 요소를 추가 하 여 **기준선** 을 생성 합니다 (파란색).

## <a name="time-series-anomaly-detection"></a>시계열 변칙 검색

[`series_decompose_anomalies()`](/azure/kusto/query/series-decompose-anomaliesfunction) 함수는 시계열 집합에서 비정상 요소를 찾습니다. 이 함수는 `series_decompose()`를 호출 하 여 분해 모델을 만든 다음 나머지 구성 요소에서 [`series_outliers()`](/azure/kusto/query/series-outliersfunction) 를 실행 합니다. `series_outliers()`은 Kekey의 fence 테스트를 사용 하 여 잔여 구성 요소의 변칙 점수를 계산 합니다. 1\.5 이상인 1.5 이상 점수는 각각 가벼운 이상 증가 또는 거부를 의미 합니다. 3\.0 이상인 이상 점수 또는-3.0은 강력한 비정상을 의미 합니다. 

다음 쿼리를 사용 하면 내부 웹 서비스 트래픽에서 변칙을 검색할 수 있습니다.

**\[** [**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WR3W7CMAyF73mKI25KpRbaTmjSUJ8CpF1WoXVptPxUifmb9vBLoGO7GFeR7ePv2I4ihpamYdToBBNLTYuqKF/zosyLdbqZqagQl/8UVV68oKreimLSdVFUDZtZR9o2WnxQ48lJ8tXsCzHM7yHMUdfidFiEN4U12AXoloUe0Turp4nYTsaeaYzs/RVedgis80CObkFdI9ltywTAagV4UtQyRKiZgyLEaTGZ9taFQqtIGHI4SX8USn4KltYEJF2YTIeFMFaHPPkMvrWOMuxFoEpDaVjujmo6aq0erafmIY+7ZCiX6wx5mSGJHb3kJA1sF8jB8q69toNwjLPkYfGTseqoja//eLNkRXXyTnuIcVyCneh72cL2YQdtDQ8ZHvIkDcsfPWH+3AvPvObx0FMXD/RLhfDYW9VhtNKwj/8U69M1b2S//AbRUQMWQQIAAA==) **\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (anomalies, score, baseline) = series_decompose_anomalies(num, 1.5, -1, 'linefit')
| render anomalychart with(anomalycolumns=anomalies, title='Web app. traffic of a month, anomalies') //use "| render anomalychart with anomalycolumns=anomalies" to render the anomalies as bold points on the series charts.
```

![시계열 변칙 검색](media/anomaly-detection/series-anomaly-detection.png)

* 원본 시계열 (빨간색)입니다. 
* 기준선 (계절 + 추세) 구성 요소 (파란색)입니다.
* 원본 시계열 위쪽의 비정상 시점 (자주색)입니다. 비정상 지점은 예상 되는 기준선 값을 크게 벗어난 것입니다.

## <a name="time-series-forecasting"></a>시계열 예측

함수 [`series_decompose_forecast()`](/azure/kusto/query/series-decompose-forecastfunction) 는 시계열 집합의 미래 값을 예측 합니다. 이 함수는 `series_decompose()`를 호출 하 여 분해 모델을 작성 한 다음 각 시계열에 대해 기준선 구성 요소를 미래로 추정 합니다.

다음 쿼리를 사용 하면 다음 주 웹 서비스 트래픽을 예측할 수 있습니다.

**\[** [**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA22QzW6DMBCE73mKuQFqKISqitSIW98gkXpEDl5iK9hG9uanUR++dqE99YRGO8x845EYRtuO0UIKJtaG8qbebMt6U9avxW41Joe4/+doyvoFTfNW14tPJlOjZqGc1w9n263crSQZ1xlxpi6Q1xSa1ReSLGcJezGtuJ7y+C3gLA6xZM/CTBi8MwshuxnkaUlGYJpS5/ETQUvEzJsiTz+ibZEd9psMQFUBgUbqGSLe7GkkpBVYygfn46EfSVjyuOpwEaN+CNbOxki6M1mZTNSLkAbOv3WSemcmF6j7vSX8dcTUlvOFsZJcFDHFx4wYnmp7JTzjplnlrHmkNvugI8Q0PYO9GAbdww0RyDjLav1XHLnBimAjEG5E5zQ7vRP284x36hOOTtxZ8Q3The8P2QEAAA==) **\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week by Time Series Decomposition')
```

![시계열 예측](media/anomaly-detection/series-forecasting.png)

* 원래 메트릭 (빨간색)입니다. 이후 값이 누락 되 고 기본적으로 0으로 설정 됩니다.
* 기본 구성 요소 (파란색)를 추정 하 여 다음 주의 값을 예측 합니다.

## <a name="scalability"></a>확장성

Azure 데이터 탐색기 쿼리 언어 구문을 사용 하면 단일 호출을 통해 여러 시계열을 처리할 수 있습니다. 고유 하 게 최적화 된 구현에서는 거의 실시간 시나리오에서 수천 개의 카운터를 모니터링 하는 경우 효과적인 변칙 검색 및 예측에 매우 중요 한 빠른 성능을 사용할 수 있습니다.

다음 쿼리에서는 세 시계열을 동시에 처리 하는 방법을 보여 줍니다.

**\[** [**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA21Qy26DMBC85yvmFlChcUirSI34ikTqETl4KVawjfDmqX587UCaHuqLtePxPLYjhtG2YpRQkom1oaQQy3Uulrl4TzezLjLk5T9GkYsViuJDiImnIqlox6F1g745W67VZqbIuMrIA1WeBk2+mH0jjvk4wh5NKU9fSbhTOItdMNmyND2awZkpIbsxyMukDM/UR8/9FV6rIEkXJqvgmsYTl7X0lISHspzvtqt5hjdxPxkeYBHA4gGKFMBiAUilIAfWja617CY1NG4ASX/FSfuj7PRNsg4ZXANz7Fj3HSGuBmOjZ5hYbcSqIBwbZpNk+iQFcQpx4/omrqLamd55qh5v41d22nIybWChOI0qQ9Cg4e5ftyE6zprbhDV3VM4/aQ/Z96/gQTahU4wsYZzlNvs11vYL3BJsCIQz0eHed/W30jz9AUEBI0ktAgAA) **\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid
| extend offset=case(sid=='TS3', 4000000, sid=='TS2', 2000000, 0)   //  add artificial offset for easy visualization of multiple time series
| extend num=series_add(num, offset)
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week for 3 time series')
```

![시계열 확장성](media/anomaly-detection/series-scalability.png)

## <a name="summary"></a>요약

이 문서에서는 시계열 변칙 검색 및 예측에 대한 기본 Azure 데이터 탐색기 함수에 대해 자세히 설명 합니다. 각 원본 시계열은 비정상 및/또는 예측을 검색 하기 위한 계절, 추세 및 잔여 구성 요소로 분해 됩니다. 이러한 기능은 거의 실시간 모니터링 시나리오 (예: 오류 검색, 예측 유지 관리, 수요 및 부하 예측)에 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure 데이터 탐색기의 [기계 학습 기능](/azure/data-explorer/machine-learning-clustering) 에 대해 알아봅니다.
