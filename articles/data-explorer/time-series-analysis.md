---
title: Azure 데이터 탐색기를 사용 하 여 시계열 데이터 분석
description: Azure 데이터 탐색기를 사용 하 여 클라우드의 시계열 데이터를 분석 하는 방법에 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/07/2019
ms.openlocfilehash: 8492f736e64366802b3601f9b5fc8bd1d9b6ea79
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60827438"
---
# <a name="time-series-analysis-in-azure-data-explorer"></a>Azure 데이터 탐색기에서 시계열 분석

ADX(Azure Data Explorer)는 클라우드 서비스 또는 IoT 디바이스에서 원격 분석 데이터의 수집을 지속적으로 수행합니다. 서비스 상태 모니터링, 실제 프로덕션 프로세스 및 사용량 추세와 같은 다양한 인사이트를 위해 이 데이터를 분석할 수 있습니다. 일반적인 기준 패턴과 비교해 패턴의 편차를 발견하려면 선택한 메트릭의 시계열에서 분석을 수행합니다.
ADX에는 여러 시계열의 생성, 조작 및 분석에 대한 기본 지원이 포함됩니다. 이 항목에서는 거의 실시간 모니터링 솔루션 및 워크플로를 사용하도록 설정함으로써 ADX를 사용하여 **몇 초만에 수천 개의 시계열**을 만들고 분석하는 방법을 알아봅니다.

## <a name="time-series-creation"></a>시계열 만들기

이 섹션에서는 `make-series` 연산자를 사용하여 기본 시계열의 대규모 집합을 간단하고 직관적으로 만들고, 필요할 경우 누락된 값을 입력합니다.
시계열 분석의 첫 번째 단계는 원본 원격 분석 테이블을 시계열 집합으로 분할 및 변환하는 것입니다. 테이블에는 일반적으로 타임 스탬프 열, 상황에 맞는 차원 및 선택적 메트릭이 포함됩니다. 차원을 사용하여 데이터를 분할합니다. 기본 시간 간격에 파티션당 수천 개의 시계열을 만드는 것이 목표입니다.

입력 테이블 *demo_make_series1*에는 임의 웹 서비스 트래픽의 60만 개 레코드가 포함됩니다. 다음 명령을 사용하여 10개의 레코드를 샘플링합니다.

```kusto
demo_make_series1 | take 10 
```

결과 테이블에는 타임 스탬프 열, 세 가지 상황에 맞는 차원 열 및 메트릭 없음이 포함됩니다.

|   |   |   |   |   |
| --- | --- | --- | --- | --- |
|   | TimeStamp | BrowserVer | OsVer | 국가 |
|   | 2016-08-25 09:12:35.4020000 | Chrome 51.0 | Windows 7 | 영국 |
|   | 2016-08-25 09:12:41.1120000 | Chrome 52.0 | 윈도우 10 |   |
|   | 2016-08-25 09:12:46.2300000 | Chrome 52.0 | Windows 7 | 영국 |
|   | 2016-08-25 09:12:46.5100000 | Chrome 52.0 | 윈도우 10 | 영국 |
|   | 2016-08-25 09:12:46.5570000 | Chrome 52.0 | 윈도우 10 | 리투아니아 |
|   | 2016-08-25 09:12:47.0470000 | Chrome 52.0 | Windows 8.1 | 인도 |
|   | 2016-08-25 09:12:51.3600000 | Chrome 52.0 | 윈도우 10 | 영국 |
|   | 2016-08-25 09:12:51.6930000 | Chrome 52.0 | Windows 7 | 네덜란드 |
|   | 2016-08-25 09:12:56.4240000 | Chrome 52.0 | 윈도우 10 | 영국 |
|   | 2016-08-25 09:13:08.7230000 | Chrome 52.0 | 윈도우 10 | 인도 |

메트릭이 없으므로 다음과 같은 쿼리를 사용하여 OS로 분할된 트래픽 개수 자체를 나타내는 시계열 집합만 빌드할 수 있습니다.

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| render timechart 
```

- 다음과 같은 경우 [`make-series`](/azure/kusto/query/make-seriesoperator) 연산자를 사용하여 세 개의 시계열 집합을 만듭니다.
    - `num=count()`: 트래픽의 시계열
    - `range(min_t, max_t, 1h)`: 시계열은 시간 범위에서 1시간 bin 단위로 만들어짐(가장 오래되고 최신의 테이블 레코드의 타임 스탬프)
    - `default=0`: 기본 시계열을 만들려면 누락된 bin에 대한 채우기 메서드를 지정합니다. 또는 변경을 위해 [`series_fill_const()`](/azure/kusto/query/series-fill-constfunction), [`series_fill_forward()`](/azure/kusto/query/series-fill-forwardfunction), [`series_fill_backward()`](/azure/kusto/query/series-fill-backwardfunction) 및 [`series_fill_linear()`](/azure/kusto/query/series-fill-linearfunction) 사용
    - `byOsVer`: OS별 파티션
- 실제 시계열 데이터 구조는 각 시간 bin 단위당 집계된 값의 숫자형 배열입니다. 시각화를 위해 `render timechart`를 사용합니다.

위의 테이블에는 3개의 파티션이 있습니다. 다음과 같은 별도의 시계열을 만들 수 있습니다. 그래프에 표시된 대로 각 OS 버전에 대해 Windows 10(빨간색), Windows 7(파란색) 및 Windows 8.1(녹색)

![시계열 파티션](media/time-series-analysis/time-series-partition.png)

## <a name="time-series-analysis-functions"></a>시계열 분석 함수

이 섹션에서는 일반적인 시계열 처리 함수를 수행합니다.
시계열 집합을 만든 후 ADX는 증가하는 함수 목록을 지원하여 [시계열 설명서](/azure/kusto/query/machine-learning-and-tsa)에 있을 수 있는 함수를 처리하고 분석합니다. 시계열의 처리 및 분석을 위한 몇 가지 대표적인 함수에 대해 설명하겠습니다.

### <a name="filtering"></a>Filtering

필터링은 신호 처리의 일반적인 사례로서 시계열 처리 작업(예: 노이즈가 있는 신호 및 변경 내용 검색 평활화)에 유용합니다.
- 일반 필터링 함수에는 다음과 같은 두 가지가 있습니다.
    - [`series_fir()`](/azure/kusto/query/series-firfunction): FIR 필터를 적용합니다. 이동 평균의 간단한 계산 및 변경 내용 검색에 대한 시계열 차별화에 사용합니다.
    - [`series_iir()`](/azure/kusto/query/series-iirfunction): IIR 필터를 적용합니다. 지수 평활법 및 누적 합계에 사용합니다.
- `Extend` 5개 bin 크기의 새 이동 평균 계열(*ma_num*이라고 함)을 쿼리에 추가하여 설정한 시계열.

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| extend ma_num=series_fir(num, repeat(1, 5), true, true)
| render timechart
```

![시계열 필터링](media/time-series-analysis/time-series-filtering.png)

### <a name="regression-analysis"></a>회귀 분석

ADX는 분할된 선형 회귀 분석을 지원하여 시계열 추세를 예측합니다.
- [series_fit_line()](/azure/kusto/query/series-fit-linefunction)을 사용하여 일반적인 추세 검색을 위해 시계열에 적합한 선을 맞춥니다.
- [series_fit_2lines()](/azure/kusto/query/series-fit-2linesfunction)를 사용하여 모니터링 시나리오에 유용한 기준선을 기준으로 추세 변경 사항을 검색합니다.

`series_fit_line()`의 예제 및 시계열 쿼리의 `series_fit_2lines()` 함수.

```kusto
demo_series2
| extend series_fit_2lines(y), series_fit_line(y)
| render linechart with(xcolumn=x)
```

![시계열 회귀](media/time-series-analysis/time-series-regression.png)

- 파란색: 원본 시계열
- 녹색: 맞춤 줄
- 빨강: 두 개의 맞춤 줄

> [!NOTE]
> 함수는 정확하게 점프(수준 변경) 지점을 검색했습니다.

### <a name="seasonality-detection"></a>계절성 검색

많은 메트릭이 계절성(정기적인) 패턴을 따릅니다. 일반적으로 클라우드 서비스의 사용자 트래픽에는 영업일 중에 가장 높고 밤 및 주말에 가장 낮은 일별 및 주별 패턴이 포함됩니다. IoT 센서는 정기적으로 측정합니다. 압력, 온도, 습도 같은 실제 측정값은 계절별 동작도 보여줍니다.

다음 예제는 웹 서비스의 1개월 트래픽(2시간 bin 단위)에 계절성 검색을 적용합니다.

```kusto
demo_series3
| render timechart 
```

![시계열 계절성](media/time-series-analysis/time-series-seasonality.png)

- [series_periods_detect()](/azure/kusto/query/series-periods-detectfunction)를 사용하여 시계열의 기간을 자동으로 검색합니다. 
- 메트릭이 특정 고유 기간을 포함해야 하는 것을 알고 해당 기간이 있는지 확인하려는 경우 [series_periods_validate()](/azure/kusto/query/series-periods-validatefunction)를 사용합니다.

> [!NOTE]
> 특정 고유 마침표가 없으면 비정상임

```kusto
demo_series3
| project (periods, scores) = series_periods_detect(num, 0., 14d/2h, 2) //to detect the periods in the time series
| mv-expand periods, scores
| extend days=2h*todouble(periods)/1d
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | 기간 | 점수 | days |
|   | 84 | 0.820622786055595 | 7 |
|   | 12 | 0.764601405803502 | 1 |

함수는 일별 및 주별 계절성을 검색합니다. 주말은 평일과 다르므로 일별 점수는 주별 점수보다 낮습니다.

### <a name="element-wise-functions"></a>요소별 함수

시계열에서 산술 및 논리 작업을 수행할 수 있습니다. [series_subtract()](/azure/kusto/query/series-subtractfunction)를 사용하여 잔여 시계열 즉, 원본 원시 메트릭과 평활된 메트릭 간의 차이를 계산하고, 잔여 신호의 변칙을 검색할 수 있습니다.

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| extend ma_num=series_fir(num, repeat(1, 5), true, true)
| extend residual_num=series_subtract(num, ma_num) //to calculate residual time series
| where OsVer == "Windows 10"   // filter on Win 10 to visualize a cleaner chart 
| render timechart
```

![시계열 작업](media/time-series-analysis/time-series-operations.png)

- 파란색: 원본 시계열
- 빨간색: 평활 시계열
- 녹색: 잔여 시계열

## <a name="time-series-workflow-at-scale"></a>대규모의 시계열 워크플로

아래 예제에서는 이러한 함수가 변칙 검색을 위해 몇 초만에 수천 개의 시계열에서 대규모로 실행되는 방법을 보여줍니다. 4일 동안 DB 서비스의 읽기 개수 메트릭의 몇 가지 샘플 원격 분석 레코드를 확인하려면 다음 쿼리를 실행합니다.

```kusto
demo_many_series1
| take 4 
```

|   |   |   |   |   |   |
| --- | --- | --- | --- | --- | --- |
|   | TIMESTAMP | Loc | anonOp | DB | DataRead |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | 5117853934049630089 | 262 | 0 |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | 5117853934049630089 | 241 | 0 |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | -865998331941149874 | 262 | 279862 |
|   | 2016-09-11 21:00:00.0000000 | Loc 9 | 371921734563783410 | 255 | 0 |

간단한 통계:

```kusto
demo_many_series1
| summarize num=count(), min_t=min(TIMESTAMP), max_t=max(TIMESTAMP) 
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | num | min\_t | max\_t |
|   | 2177472 | 2016-09-08 00:00:00.0000000 | 2016-09-11 23:00:00.0000000 |

읽기 메트릭의 1시간 bin 단위로 시계열(총 4 일 * 24시간 = 96포인트)을 빌드하면 일반 패턴 변동이 발생합니다.

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h)
| render timechart with(ymin=0) 
```

![대규모의 시계열](media/time-series-analysis/time-series-at-scale.png)

단일 일반 시계열은 비정상적인 패턴이 있을 수 있는 수천 개의 서로 다른 인스턴스에서 집계되므로 위의 동작은 오해의 여지가 있습니다. 따라서 인스턴스당 시계열을 만듭니다. 인스턴스는 Loc(위치), anonOp(작업) 및 DB(특정 머신)에서 정의됩니다.

만들 수 있는 시계열 수는 어떻게 되나요?

```kusto
demo_many_series1
| summarize by Loc, Op, DB
| count
```

|   |   |
| --- | --- |
|   | 카운트 |
|   | 18339 |

이제 읽기 개수 메트릭의 18339 시계열 세트를 만들려고 합니다. `by` 절을 make-series 문에 추가하고, 선형 회귀를 적용하고, 가장 중요한 감소 추세를 포함한 상위 두 개의 시계열을 선택합니다.

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h) by Loc, Op, DB
| extend (rsquare, slope) = series_fit_line(reads)
| top 2 by slope asc 
| render timechart with(title='Service Traffic Outage for 2 instances (out of 18339)')
```

![상위 두 개의 시계열](media/time-series-analysis/time-series-top-2.png)

인스턴스를 표시합니다.

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h) by Loc, Op, DB
| extend (rsquare, slope) = series_fit_line(reads)
| top 2 by slope asc
| project Loc, Op, DB, slope 
```

|   |   |   |   |   |
| --- | --- | --- | --- | --- |
|   | Loc | Op | DB | slope |
|   | Loc 15 | 37 | 1151 | -102743.910227889 |
|   | Loc 13 | 37 | 1249 | -86303.2334644601 |

2분 미만의 시간에 ADX는 20,000개에 가까운 시계열을 분석하고 읽기 개수가 갑자기 줄어든 비정상적인 시계열 2개를 검색했습니다.

ADX 빠른 성능과 결합된 이러한 고급 기능은 시계열 분석을 위해 고유하고 강력한 솔루션을 제공합니다.
