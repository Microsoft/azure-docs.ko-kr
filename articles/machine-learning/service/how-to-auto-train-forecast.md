---
title: 시계열 예측된 모델을 자동-학습
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning 서비스를 사용 하 여 시계열 자동화 된 machine learning을 사용 하 여 예측 회귀 모델을 학습 하는 방법에 알아봅니다.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 03/19/2019
ms.openlocfilehash: c4f94dd2730dd302951b4476a292b006041b7ee8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60820031"
---
# <a name="auto-train-a-time-series-forecast-model"></a>시계열 예측된 모델을 자동-학습

이 문서에서는 자동화 된 기계 학습 Azure Machine Learning 서비스에 사용 하 여 시계열 예측 회귀 모델을 학습 하는 방법을 알아봅니다. 예측 모델 구성 자동화 된 machine learning을 사용 하 여 표준 회귀 모델을 설정 하려면 유사 하지만 시계열 데이터를 사용 하 여 작업에 대 한 특정 구성 옵션 및 전처리 단계 존재 합니다. 표시 하는 다음 예제에서는 하려면:

* 시계열 모델링에 대 한 데이터 준비
* 특정 시계열 매개 변수에서 구성 된 [ `AutoMLConfig` ](/python/api/azureml-train-automl/azureml.train.automl.automlconfig) 개체
* 시계열 데이터를 사용 하 여 예측을 실행 합니다.

## <a name="prerequisites"></a>필수 조건

* Azure Machine Learning 서비스 작업 영역. 참조 된 작업 영역을 만들려면 [Azure Machine Learning 서비스 작업 영역 만들기](setup-create-workspace.md)합니다.
* 이 문서에는 자동화 된 기계 학습 실험을 설정 하는 기본 지식이 있다고 가정 합니다. 수행 합니다 [자습서](tutorial-auto-train-models.md) 또는 [방법](how-to-configure-auto-train.md) 기본 자동화 된 기계 학습 실험 디자인 패턴을 확인 하려면.

## <a name="preparing-data"></a>데이터 준비

가장 중요 한 차이점을 예측 하는 회귀 작업 유형 및 회귀 간에 자동화 된 machine learning 내에서 작업 종류 유효한 시계열을 나타내는 데이터의는 기능을 포함 합니다. 일반 시계열 잘 정의 되 고 일관 된 빈도 있고 지속적인 시간 범위를 모든 샘플 시점의 값입니다. 파일의 스냅숏을 고려 `sample.csv`합니다.

    day_datetime,store,sales_quantity,week_of_year
    9/3/2018,A,2000,36
    9/3/2018,B,600,36
    9/4/2018,A,2300,36
    9/4/2018,B,550,36
    9/5/2018,A,2100,36
    9/5/2018,B,650,36
    9/6/2018,A,2400,36
    9/6/2018,B,700,36
    9/7/2018,A,2450,36
    9/7/2018,B,650,36

이 데이터 집합은 두 개의 다른 저장소 A와 b에 있는 회사에 대 한 일일 판매 데이터는 간단한 예제는 하나의 기능 `week_of_year` 를 모델에서 매주 계절성을 검색 하도록 허용 됩니다. 필드 `day_datetime` 일별 빈도 및 필드를 사용 하 여 정리 시계열을 나타내는 `sales_quantity` 예측을 실행 하기 위한 대상 열입니다. Pandas dataframe에 데이터를 읽은 다음 사용 합니다 `to_datetime` 시계열이 되도록 함수를 `datetime` 형식입니다.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

이 경우 데이터를 아직 정렬 시간 필드를 기준으로 오름차순 `day_datetime`합니다. 그러나 실험을 설정할 때에 원하는 시간 열이 올바른 시계열 빌드를 오름차순으로 정렬 확인 합니다. 학습 및 테스트 데이터 집합을 데이터의 명확한 분할을 확인 하 고 1,000 개의 레코드를 포함 하는 데이터를 가정 합니다. 그런 다음 대상 필드를 구분 `sales_quantity` 예측 학습 및 테스트를 만들려면 다음을 설정 합니다.

```python
X_train = data.iloc[:950]
X_test = data.iloc[-50:]

y_train = X_train.pop("sales_quantity").values
y_test = X_test.pop("sales_quantity").values
```

> [!NOTE]
> 미래 가치를 예측 하는 것에 대 한 모델을 학습할 때 의도 한 시야에 대 한 예측을 실행 하는 경우 학습에 사용 되는 기능을 사용할 수 모두를 확인 합니다. 예를 들어, 한 수요 예측을 만들 때 현재 주가 대 한 기능을 포함 하 여 증가할 수 있습니다 대규모 학습 정확도입니다. 그러나 긴 기간을 사용 하 여 예측 하려는 경우 향후 시계열 지점에 해당 주식 값을 정확 하 게 예측할 수 있습니다 및 모델 정확도 저하 될 수 있습니다.

## <a name="configure-experiment"></a>실험 구성

작업 예측에 대 한 자동화 된 기계 학습에서는 시계열 데이터에만 적용 되는 전처리, 예측 단계를 사용 합니다. 전처리 단계 실행 됩니다.

* 시계열 빈도 (예: 매시간, 매일, 매주) 샘플 없는 연속 시리즈를 확인 하는 시점에 대 한 새 레코드를 만들 감지 합니다.
* 기능 열 (중앙값 열 값 사용) (정방향 채우기)을 통해 대상에 누락 값 대체
* 다른 계열에서 고정 된 효과 수 있도록 세분화 기반 기능 만들기
* 계절성 패턴을 학습 하는 데 도움이 되는 시간 기반 기능 만들기
* 숫자 수량으로 범주 변수 인코딩

`AutoMLConfig` 설정 및 데이터는 자동화 된 기계 학습 작업에 필요한 개체를 정의 합니다. 회귀 문제와 마찬가지로, 작업 유형, 데이터를 학습, 반복 횟수 및 교차 유효성 검사의 수와 같은 표준 학습 매개 변수 정의 합니다. 작업 예측에 대 한 실험에 영향을 주는 설정 해야 하는 추가 매개 변수가 있습니다. 다음 표에서 각 매개 변수 및 사용법을 설명합니다.

| 매개 변수 | 설명 | 필수 |
|-------|-------|-------|
|`time_column_name`|시계열을 빌드하고 해당 빈도 유추에 사용할 입력된 데이터에서 날짜/시간 열을 지정 하는 데 사용 합니다.|✓|
|`grain_column_names`|입력된 데이터의 개별 계열 그룹을 정의 하는 이름입니다. 수준이 정의 되어 있지 않으면 데이터 집합을 하나의 시계열 수로 간주 됩니다.||
|`max_horizon`|최대 원하는 예측된 기간 시계열 빈도 단위입니다.|✓|
|`target_lags`|*n* 정방향 지연 기간이 대상 모델을 학습 하기 전에 값입니다.||
|`target_rolling_window_size`|*n* 기록 기간 예측된 값을 생성 하는 데 < = 학습 집합 크기입니다. 생략 *n* 는 전체 학습 집합 크기입니다.||

사전 개체로 시계열 설정을 만듭니다. 설정 합니다 `time_column_name` 에 `day_datetime` 데이터 집합의 필드입니다. 정의 `grain_column_names` 되도록 매개 변수 **두 개의 별도 시계열 그룹** 데이터 저장소는 및 2. 마지막으로,에 대 한 설정에 대해 만들어진는 `max_horizon` 전체 테스트에 대 한 예측 하기 위해 50으로 설정 합니다. 예측된 기간을 사용 하 여 10 기간으로 설정 `target_rolling_window_size`, 및 대상 값을 사용 하 여 계속 해 서 2 시간 지연의 `target_lags` 매개 변수입니다.

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "grain_column_names": ["store"],
    "max_horizon": 50,
    "target_lags": 2,
    "target_rolling_window_size": 10
}
```

이제 표준을 만들 `AutoMLConfig` 개체를 지정 하는 `forecasting` 형식, 작업 및 실험을 제출 합니다. 모델에는 다음이 완료 되 면 가장 잘 실행된 반복을 검색 합니다.

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             iterations=10,
                             X=X_train,
                             y=y_train,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **time_series_settings)

ws = Workspace.from_config()
experiment = Experiment(ws, "forecasting_example")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```

> [!NOTE]
> 교차 유효성 검사 (CV) 프로시저에 대 한 자동화 된 기계 학습을 만드는 롤링 원점 유효성 검사 절차를 구현 하므로 시계열 데이터 정식 K 접기 교차 유효성 검사 전략의 기본 통계 가정을 위반할 수 있습니다. 시계열 데이터에 대 한 교차 유효성 검사 접기 합니다. 이 절차를 사용 하려면 지정 합니다 `n_cross_validations` 의 매개 변수는 `AutoMLConfig` 개체입니다. 유효성 검사 및 유효성 검사를 직접 사용 하 여 집합을 사용 하 여 무시할 수 있습니다 합니다 `X_valid` 고 `y_valid` 매개 변수입니다.

## <a name="forecasting-with-best-model"></a>최상의 모델을 사용 하 여 예측

테스트 데이터 집합에 대 한 값을 예측 하는 가장 좋은 모델 반복을 사용 합니다.

```python
y_predict = fitted_model.predict(X_test)
y_actual = y_test.flatten()
```

계산 RMSE (제곱 평균 오차) 간에 `y_test` 에 예측 된 값과 실제 값을 `y_pred`.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

이제 전체 모델 정확도 결정 되는 좀 더 현실적인 다음 단계를 알 수 없는 미래 값을 예측 모델을 사용 하도록 합니다. 테스트 집합으로 동일한 형식의 데이터 집합을 제공 `X_test` 하지만 이후 날짜와 결과 예측은 각 시계열의 단계에 대 한 예측된 값입니다. 2018 년 12 월 31에 대 한 데이터 집합의 마지막 시계열 레코드를 가정 합니다. 다음 날에 대 한 수요를 예측 (또는 예측을 위해 필요한 만큼 많은 기간 < = `max_horizon`), 단일 만들기 01/01/2019에 대 한 각 상점의 시리즈 레코드 시간입니다.

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

데이터 프레임에 이후이 데이터를 로드 한 다음 실행 하는 데 필요한 단계를 반복 `best_run.predict(X_test)` 미래 가치를 예측 합니다.

> [!NOTE]
> 보다 큰 기간 수에 대 한 값을 예측할 수 없습니다는 `max_horizon`합니다. 모델은 현재 마감일 외 이후 값을 예측 하려면 더 큰 기간을 사용 하 여 다시 학습 해야 합니다.

## <a name="next-steps"></a>다음 단계

* 수행 합니다 [자습서](tutorial-auto-train-models.md) 자동화 된 machine learning을 사용 하 여 실험을 만드는 방법을 알아봅니다.
* 보기는 [Python 용 Azure Machine Learning SDK](https://aka.ms/aml-sdk) 설명서를 참조 합니다.
