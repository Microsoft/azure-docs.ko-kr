---
title: 자동화된 ML 실험 만들기
titleSuffix: Azure Machine Learning
description: 자동화된 기계 학습은 사용자를 위한 알고리즘을 선택하고 배포할 준비가 된 모델을 생성합니다. 자동화된 기계 학습 실험을 구성하는 데 사용할 수 있는 옵션에 대해 알아봅니다.
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: b7f837c56214d2d01d0f119e0107a095bcfd782b
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198772"
---
# <a name="configure-automated-ml-experiments-in-python"></a>Python에서 자동화 된 ML 실험 구성
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 가이드에서는 [AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)를 사용 하 여 자동화 된 machine learning 실험의 다양 한 구성 설정을 정의 하는 방법에 대해 알아봅니다. 자동화된 Machine Learning은 사용자를 위한 알고리즘과 하이퍼 매개 변수를 선택하고 배포할 준비가 된 모델을 생성합니다. 자동화된 Machine Learning 실험을 구성하는 데 사용할 수 있는 옵션에 대해 알아봅니다.

자동화 된 기계 학습 실험의 예를 보려면 [자습서: 자동화 된 machine learning을 사용 하 여 분류 모델 학습](tutorial-auto-train-models.md) 또는 [클라우드에서 자동화 된 machine learning을 사용 하 여 모델 학습](how-to-auto-train-remote.md)을 참조 하세요.

자동화된 기계 학습에서 사용할 수 있는 구성 옵션은 다음과 같습니다.

* 실험 유형 선택: 분류, 회귀 또는 시계열 예측
* 데이터 원본, 형식 및 데이터 가져오기
* 컴퓨팅 대상 선택: 로컬 또는 원격
* 자동화된 Machine Learning 실험 설정
* 자동화된 Machine Learning 실험 실행
* 모델 메트릭 탐색
* 모델 등록 및 배포

코드를 사용 하지 않으려는 경우 [Azure Machine Learning studio에서 자동화 된 기계 학습 실험을 만들](how-to-create-portal-experiments.md)수도 있습니다.

## <a name="select-your-experiment-type"></a>실험 유형 선택

실험을 시작하기 전에 해결하려는 기계 학습 문제의 종류를 결정해야 합니다. 자동화 된 machine learning은 분류, 회귀 및 예측의 작업 유형을 지원 합니다. [작업 유형에](how-to-define-task-type.md)대 한 자세한 정보.

자동화된 Machine Learning은 자동화 및 튜닝 프로세스 중에 다음 알고리즘을 지원합니다. 사용자는 알고리즘을 지정할 필요가 없습니다.

분류 | 회귀 | 시계열 예측
|-- |-- |--
[Logistic Regression](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Decision Tree](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Decision Tree](https://scikit-learn.org/stable/modules/tree.html#regression)|[Decision Tree](https://scikit-learn.org/stable/modules/tree.html#regression)
[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Linear SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[지원 벡터 분류 (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[SGD(Stochastic Gradient Descent)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[SGD(Stochastic Gradient Descent)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[DNN 분류자](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN 회귀 변수](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN 회귀 변수](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN 선형 분류자](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[선형 회귀 변수](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[선형 회귀 변수](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|[고속 선형 회귀 변수](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.fastlinearregressor?view=nimbusml-py-latest)|[자동 ARIMA](https://www.alkaline-ml.com/pmdarima/modules/generated/pmdarima.arima.auto_arima.html#pmdarima.arima.auto_arima)
[SGD(Stochastic Gradient Descent)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|[온라인 그라데이션 하강 회귀 변수](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.onlinegradientdescentregressor?view=nimbusml-py-latest)|[Prophet](https://facebook.github.io/prophet/docs/quick_start.html)
|[평균 퍼셉트론 분류자](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.averagedperceptronbinaryclassifier?view=nimbusml-py-latest)||ForecastTCN
|[선형 SVM 분류자](https://docs.microsoft.com/python/api/nimbusml/nimbusml.linear_model.linearsvmbinaryclassifier?view=nimbusml-py-latest)||

`AutoMLConfig` 생성자에서 `task` 매개 변수를 사용 하 여 실험 유형을 지정 합니다.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task = "classification")
```

## <a name="data-source-and-format"></a>데이터 원본 및 형식

자동화된 Machine Learning은 로컬 데스크톱 또는 Azure Blob Storage와 같은 클라우드의 데이터를 지원합니다. **Pandas 데이터 프레임** 또는 **Azure Machine Learning TabularDataset**데이터를 읽을 수 있습니다.  [데이터 집합에 대해 자세히 알아보세요](how-to-create-register-datasets.md).

데이터 학습을 위한 요구 사항:
- 데이터는 테이블 형식 이어야 합니다.
- 예측할 값, 대상 열은 데이터에 있어야 합니다.

다음 코드 예제에서는 이러한 형식으로 데이터를 저장 하는 방법을 보여 줍니다.

* TabularDataset
  ```python
  from azureml.core.dataset import Dataset
  from azureml.opendatasets import Diabetes
  
  tabular_dataset = Diabetes.get_tabular_dataset()
  train_dataset, test_dataset = tabular_dataset.random_split(percentage=0.1, seed=42)
  label = "Y"
  ```

* pandas 데이터 프레임

    ```python
    import pandas as pd
    from sklearn.model_selection import train_test_split

    df = pd.read_csv("your-local-file.csv")
    train_data, test_data = train_test_split(df, test_size=0.1, random_state=42)
    label = "label-col-name"
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>원격 컴퓨팅에서 실험을 실행하기 위한 데이터 가져오기

원격 실행의 경우 원격 계산에서 학습 데이터에 액세스할 수 있어야 합니다. SDK의 클래스 [`Datasets`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py) 는 다음과 같은 기능을 제공 합니다.

* 정적 파일 또는 URL 원본에서 작업 영역으로 데이터를 쉽게 전송
* 클라우드 계산 리소스에서 실행 되는 경우 학습 스크립트에서 데이터를 사용할 수 있도록 설정

`Dataset` 클래스를 사용 하 여 계산 대상에 데이터를 탑재 하는 방법에 대 한 예제는 [방법을](how-to-train-with-datasets.md#option-2--mount-files-to-a-remote-compute-target) 참조 하세요.

## <a name="train-and-validation-data"></a>데이터 학습 및 유효성 검사

별도의 학습 및 유효성 검사 집합을 `AutoMLConfig` 생성자에서 직접 지정할 수 있습니다.

### <a name="k-folds-cross-validation"></a>K 접기 교차 유효성 검사

`n_cross_validations` 설정을 사용하여 교차 유효성 검사의 수를 지정합니다. 학습 데이터 집합은 무작위로 동일한 크기의 `n_cross_validations` 접기로 분할됩니다. 각 교차 유효성 검사 라운드 중에 접기 중 하나는 나머지 접기에 대해 학습된 모델의 유효성 검사에 사용됩니다. 이 프로세스는 각 접기가 유효성 검사 집합으로 한 번 사용될 때까지 `n_cross_validations` 라운드 동안 반복됩니다. 모든 `n_cross_validations` 라운드에 걸친 평균 점수가 보고되고 해당 모델이 전체 학습 데이터 세트에 대해 다시 학습됩니다.

### <a name="monte-carlo-cross-validation-repeated-random-sub-sampling"></a>Monte 몬테카를로 교차 유효성 검사 (반복 무작위 하위 샘플링)

`validation_size`를 사용하여 유효성 검사에 사용해야 하는 학습 데이터 세트의 비율을 지정하고, `n_cross_validations`를 사용하여 교차 유효성 검사의 수를 지정합니다. 각 교차 유효성 검사 라운드 중에 나머지 데이터에 대해 학습된 모델의 유효성 검사를 위해 `validation_size` 크기의 하위 집합이 무작위로 선택됩니다. 마지막으로, 모든 `n_cross_validations` 라운드에 걸친 평균 점수가 보고되고 해당 모델이 전체 학습 데이터 집합에 대해 다시 학습됩니다. Monte 몬테카를로는 시계열 예측에 대해 지원 되지 않습니다.

### <a name="custom-validation-dataset"></a>사용자 지정 유효성 검사 데이터 세트

임의 분할이 허용 되지 않는 경우 사용자 지정 유효성 검사 데이터 집합 사용 (일반적으로 시계열 데이터 또는 불균형 데이터) 사용자 고유의 유효성 검사 데이터 세트를 지정할 수 있습니다. 모델은 무작위 데이터 세트 대신 지정된 유효성 검사 데이터 세트에 대해 평가됩니다.

## <a name="compute-to-run-experiment"></a>실험 실행 컴퓨팅

다음으로, 모델을 학습할 위치를 결정합니다. 자동화된 Machine Learning 실험은 다음 컴퓨팅 옵션에서 실행할 수 있습니다.
*   로컬 데스크톱 또는 랩톱과 같은 로컬 머신 - 일반적으로 데이터 세트가 작고 아직 탐색 단계에 있는 경우입니다.
*   클라우드의 원격 머신 - [Azure Machine Learning Managed Compute](concept-compute-target.md#amlcompute)는 Azure Virtual Machines 클러스터에서 Machine Learning 모델을 학습할 수 있도록 하는 관리형 서비스입니다.

    로컬 및 원격 계산 대상이 있는 노트북의 예제는이 [GitHub 사이트](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) 를 참조 하세요.

*   Azure 구독의 Azure Databricks 클러스터 여기에서 자세한 내용을 확인할 수 있습니다. [자동화 된 ML에 대 한 Azure Databricks 클러스터 설정](how-to-configure-environment.md#azure-databricks)

    Azure Databricks 있는 노트북의 예제는이 [GitHub 사이트](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-databricks/automl) 를 참조 하세요.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>실험 설정 구성

자동화된 Machine Learning 실험을 구성하는 데 사용할 수 있는 옵션에 대해 알아봅니다. 이러한 매개 변수는 `AutoMLConfig` 개체를 인스턴스화하여 설정됩니다. 매개 변수의 전체 목록은 [AutoMLConfig 클래스](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)를 참조하세요.

일부 사례:

1.  실험 시간 제한 시간 (분)이 30 분으로 설정 되 고 교차 유효성 검사를 2로 설정 하는 기본 메트릭으로 # 가중치를 사용 하는 분류 실험

    ```python
    automl_classifier=AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        blacklist_models='XGBoostClassifier',
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=2)
    ```
2.  다음은 5 번의 유효성 검사를 통해 60 분 후에 종료 되는 회귀 실험 집합의 예입니다.

    ```python
    automl_regressor = AutoMLConfig(
        task='regression',
        experiment_timeout_minutes=60,
        whitelist_models='kNN regressor'
        primary_metric='r2_score',
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5)
    ```

세 가지 다른 `task` 매개 변수 값 (세 번째 작업 유형은 `forecasting`이며 `regression` 작업과 유사한 알고리즘 풀 사용)은 적용할 모델 목록을 결정 합니다. `whitelist` 또는 `blacklist` 매개 변수를 사용 하 여 포함 하거나 제외할 사용 가능한 모델로 반복을 수정 합니다. 지원 되는 모델 목록은 ([분류](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.classification), [예측](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.forecasting)및 [회귀](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels.regression))의 [supportedmodels 클래스](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.constants.supportedmodels) 에서 찾을 수 있습니다.

자동 ML의 유효성 검사를 수행 하려면 시험 시간 제한 오류를 방지 하기 위해 `experiment_timeout_minutes`를 최소 시간 제한 (15 분)으로 설정 해야 합니다.

### <a name="primary-metric"></a>기본 메트릭
기본 메트릭은 최적화를 위해 모델 학습 중에 사용할 메트릭을 결정 합니다. 선택할 수 있는 메트릭은 선택한 작업 유형에 따라 결정 되며, 다음 표에서는 각 작업 유형에 대 한 유효한 기본 메트릭을 보여 줍니다.

|분류 | 회귀 | 시계열 예측
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

[자동화 된 기계 학습 결과 이해](how-to-understand-automated-ml.md)에서 이러한 메트릭의 특정 정의에 대해 알아봅니다.

### <a name="data-featurization"></a>데이터 기능화

자동화 된 모든 기계 학습 실험에서 데이터의 [크기를 자동으로 조정 하 고 표준화](concept-automated-ml.md#preprocess) 하 여 다양 한 규모의 기능에 영향을 주는 *특정* 알고리즘을 지원 합니다.  그러나 누락 값 대체, 인코딩 및 변환과 같은 추가 기능화를 사용 하도록 설정할 수도 있습니다. [기능화 포함 된 항목에 대해 자세히 알아보세요](how-to-create-portal-experiments.md#featurization).

실험을 구성할 때 고급 설정 `featurization`을 사용 하도록 설정할 수 있습니다. 다음 표에서는 [`AutoMLConfig` 클래스](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py)에서 기능화에 대해 허용 되는 설정을 보여 줍니다.

|기능화 구성 | Description |
| ------------- | ------------- |
|`"featurization":`&nbsp;`'FeaturizationConfig'`| 사용자 지정 된 기능화 단계를 사용 해야 함을 나타냅니다. [기능화를 사용자 지정 하는 방법을 알아봅니다](how-to-configure-auto-train.md#customize-feature-engineering).|
|`"featurization": 'off'`| 기능화 단계를 자동으로 수행 하지 않음을 나타냅니다.|
|`"featurization": 'auto'`| 전처리의 일부로 [데이터 guardrails 및 기능화 단계가](how-to-create-portal-experiments.md#advanced-featurization-options) 자동으로 수행 됨을 나타냅니다.|

> [!NOTE]
> 자동화 된 machine learning 기능화 단계 (기능 정규화, 누락 된 데이터 처리, 텍스트를 숫자로 변환 등)는 기본 모델의 일부가 됩니다. 예측에 모델을 사용 하는 경우 학습 중에 적용 되는 것과 동일한 기능화 단계가 입력 데이터에 자동으로 적용 됩니다.

### <a name="time-series-forecasting"></a>시계열 예측
시계열 `forecasting` 태스크에는 구성 개체의 추가 매개 변수가 필요 합니다.

1. `time_column_name`: 유효한 시계열을 포함 하는 학습 데이터의 열 이름을 정의 하는 필수 매개 변수입니다.
1. `max_horizon`: 학습 데이터의 주기를 기준으로 예측 하려는 시간을 정의 합니다. 예를 들어 일일 시간 조직 학습 데이터가 있는 경우 모델을 학습 하는 데 사용할 일 수를 정의 합니다.
1. `grain_column_names`: 학습 데이터의 개별 시계열 데이터를 포함 하는 열의 이름을 정의 합니다. 예를 들어 매장에서 특정 브랜드의 판매를 예측 하는 경우 매장 및 브랜드 열을 그레인 열로 정의 합니다. 각 수준/그룹화에 대해 별도의 시간 계열 및 예측이 생성 됩니다. 

아래 사용 되는 설정의 예제는 [샘플 노트북](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)을 참조 하세요.

```python
# Setting Store and Brand as grains for training.
grain_column_names = ['Store', 'Brand']
nseries = data.groupby(grain_column_names).ngroups

# View the number of time series data with defined grains
print('Data contains {0} individual time-series.'.format(nseries))
```

```python
time_series_settings = {
    'time_column_name': time_column_name,
    'grain_column_names': grain_column_names,
    'drop_column_names': ['logQuantity'],
    'max_horizon': n_test_periods
}

automl_config = AutoMLConfig(task = 'forecasting',
                             debug_log='automl_oj_sales_errors.log',
                             primary_metric='normalized_root_mean_squared_error',
                             experiment_timeout_minutes=20,
                             training_data=train_data,
                             label_column_name=label,
                             n_cross_validations=5,
                             path=project_folder,
                             verbosity=logging.INFO,
                             **time_series_settings)
```

### <a name="ensemble"></a>앙상블 구성

앙상블 모델은 기본적으로 사용 하도록 설정 되어 있으며 자동화 된 machine learning 실행에서 최종 실행 반복으로 나타납니다. 현재 지원 되는 앙상블 메서드는 투표 및 누적입니다. 투표는 가중치 평균을 사용 하 여 소프트 투표로 구현 되 고, 스택 구현은 두 개의 계층 구현을 사용 합니다. 여기서 첫 번째 계층은 투표 앙상블와 동일한 모델을 사용 하 고 두 번째 계층 모델은 다음의 최적 조합을 찾는 데 사용 됩니다. 첫 번째 계층의 모델입니다. ONNX 모델을 사용 **하거나** explainability를 사용 하는 경우에는 누적이 사용 하지 않도록 설정 되며 투표만 사용 됩니다.

기본 stack 앙상블 동작을 변경 하기 위해 `AutoMLConfig` 개체에서 `kwargs`로 제공 될 수 있는 여러 기본 인수가 있습니다.

* `stack_meta_learner_type`: 학습자은 개별 유형이 다른 모델의 출력에 대해 학습 된 모델입니다. 기본 메타 학습자는 분류 작업 (또는 교차 유효성 검사를 사용 하는 경우 `LogisticRegressionCV`) `ElasticNet` 및 회귀/예측 작업 (교차 유효성 검사를 사용 하는 경우 `ElasticNetCV`)에 대해 `LogisticRegression` 됩니다. 이 매개 변수는 `LogisticRegression`, `LogisticRegressionCV`, `LightGBMClassifier`, `ElasticNet`, `ElasticNetCV`, `LightGBMRegressor`또는 `LinearRegression`문자열 중 하나일 수 있습니다.
* `stack_meta_learner_train_percentage`: 학습자를 학습 하기 위해 예약할 학습 집합 (학습 및 유효성 검사 유형 선택)의 비율을 지정 합니다. 기본값은 `0.2`입니다.
* `stack_meta_learner_kwargs`: meta 학습자의 이니셜라이저에 전달할 선택적 매개 변수입니다. 이러한 매개 변수 및 매개 변수 형식은 해당 모델 생성자에서 매개 변수 및 매개 변수 형식을 미러링 하 고 모델 생성자에 전달 됩니다.

다음 코드는 `AutoMLConfig` 개체에서 사용자 지정 앙상블 동작을 지정 하는 예를 보여 줍니다.

```python
ensemble_settings = {
    "stack_meta_learner_type": "LogisticRegressionCV",
    "stack_meta_learner_train_percentage": 0.3,
    "stack_meta_learner_kwargs": {
        "refit": True,
        "fit_intercept": False,
        "class_weight": "balanced",
        "multi_class": "auto",
        "n_jobs": -1
    }
}

automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=train_data,
        label_column_name=label,
        n_cross_validations=5,
        **ensemble_settings
        )
```

앙상블 교육은 기본적으로 사용 하도록 설정 되어 있지만 `enable_voting_ensemble` 및 `enable_stack_ensemble` 부울 매개 변수를 사용 하 여 사용 하지 않도록 설정할 수 있습니다.

```python
automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        experiment_timeout_minutes=30,
        training_data=data_train,
        label_column_name=label,
        n_cross_validations=5,
        enable_voting_ensemble=False,
        enable_stack_ensemble=False
        )
```

## <a name="run-experiment"></a>실험 실행

자동화 된 ML의 경우 실험을 실행 하는 데 사용 되는 `Workspace`의 명명 된 개체인 `Experiment` 개체를 만듭니다.

```python
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

# Choose a name for the experiment and specify the project folder.
experiment_name = 'automl-classification'
project_folder = './sample_projects/automl-classification'

experiment = Experiment(ws, experiment_name)
```

실행하려는 실험을 제출하고 모델을 생성합니다. `AutoMLConfig`를 `submit` 메서드에 전달하여 모델을 생성합니다.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>새 머신에 먼저 종속성이 설치됩니다.  출력이 표시되는 데 최대 10분이 걸릴 수 있습니다.
>`show_output`을 `True`로 설정하면 출력이 콘솔에 표시됩니다.

### <a name="exit-criteria"></a>종료 조건
실험을 종료 하기 위해 정의할 수 있는 몇 가지 옵션이 있습니다.
1. 조건 없음: 종료 매개 변수를 정의 하지 않으면 기본 메트릭에 대 한 추가 진행률이 표시 되지 않을 때까지 실험을 계속 진행 합니다.
1. 시간이 지난 후 종료: 설정에 `experiment_timeout_minutes`를 사용 하면 실험을 계속 실행 하는 데 걸리는 시간 (분)을 정의할 수 있습니다.
1. 점수를 받은 후 종료: 기본 메트릭 점수에 도달한 후 `experiment_exit_score`를 사용 하 여 실험을 완료 합니다.

### <a name="explore-model-metrics"></a>모델 메트릭 탐색

노트에서 학습 결과를 보거나 인라인으로 볼 수 있습니다. 자세한 내용은 [모델 추적 및 평가](how-to-track-experiments.md#view-run-details)를 참조하세요.

## <a name="understand-automated-ml-models"></a>자동화 된 ML 모델 이해

자동화 된 ML을 사용 하 여 생성 된 모든 모델에는 다음 단계가 포함 됩니다.
+ 자동화 된 기능 엔지니어링 (`"featurization": 'auto'`)
+ 하이퍼 매개 변수 값을 사용 하 여 크기 조정/정규화 및 알고리즘

자동 ML의 fitted_model 출력에서이 정보를 가져오도록 투명 하 게 만듭니다.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```

### <a name="automated-feature-engineering"></a>자동화 된 기능 엔지니어링

`"featurization": 'auto'`때 발생 하는 전처리 및 [자동화 된 기능 엔지니어링](concept-automated-ml.md#preprocess) 목록을 참조 하세요.

다음 예를 살펴보세요.
+ 입력 기능에는 (숫자), B (숫자), C (숫자), D (DateTime)의 네 가지가 있습니다.
+ 숫자 기능 C는 모든 고유 값을 포함 하는 ID 열 이므로 삭제 됩니다.
+ 숫자 기능 A와 B에는 누락 된 값이 있으므로 평균의 귀속 됩니다.
+ DateTime 기능 D는 11 가지 다른 엔지니어링 된 기능에 기능화 됩니다.

적합 한 모델의 첫 번째 단계에서이 2 개의 Api를 사용 하 여 더 자세히 이해 합니다.  [이 샘플 노트북](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand)을 참조 하세요.

+ API 1: `get_engineered_feature_names()`은 엔지니어링 된 기능 이름 목록을 반환 합니다.

  Usage:
  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

  ```
  Output: ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

  이 목록에는 모든 엔지니어링 된 기능 이름이 포함 됩니다.

  >[!Note]
  >작업 = ' 예측 '에 ' timeseriestransformer '를 사용 하 고, ' 회귀 ' 또는 ' 분류 ' 작업에 ' datatransformer '를 사용 합니다.

+ API 2: `get_featurization_summary()` 모든 입력 기능에 대 한 기능화 요약을 반환 합니다.

  Usage:
  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

  >[!Note]
  >작업 = ' 예측 '에 ' timeseriestransformer '를 사용 하 고, ' 회귀 ' 또는 ' 분류 ' 작업에 ' datatransformer '를 사용 합니다.

  출력:
  ```
  [{'RawFeatureName': 'A',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'B',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'C',
    'TypeDetected': 'Numeric',
    'Dropped': 'Yes',
    'EngineeredFeatureCount': 0,
    'Tranformations': []},
   {'RawFeatureName': 'D',
    'TypeDetected': 'DateTime',
    'Dropped': 'No',
    'EngineeredFeatureCount': 11,
    'Tranformations': ['DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime']}]
  ```

   위치:

   |출력|정의|
   |----|--------|
   |RawFeatureName|제공 된 데이터 집합의 입력 기능/열 이름입니다.|
   |TypeDetected|입력 기능의 데이터 형식이 검색 되었습니다.|
   |Dropped|입력 기능을 삭제 하거나 사용 했는지 여부를 나타냅니다.|
   |EngineeringFeatureCount|자동화 된 기능 엔지니어링 변환을 통해 생성 된 기능의 수입니다.|
   |변환|엔지니어링 된 기능을 생성 하기 위해 입력 기능에 적용 되는 변환 목록입니다.|
   
### <a name="customize-feature-engineering"></a>기능 엔지니어링 사용자 지정
기능 엔지니어링을 사용자 지정 하려면 `"featurization": FeaturizationConfig`를 지정 합니다.

지원 되는 사용자 지정은 다음과 같습니다.

|사용자 지정|정의|
|--|--|
|열 용도 업데이트|지정 된 열에 대 한 기능 유형을 재정의 합니다.|
|변환기 매개 변수 업데이트 |지정 된 변환기에 대 한 매개 변수를 업데이트 합니다. 현재는 (평균, 가장 자주 & 중앙값) 및 HashOneHotEncoder을 지원 합니다.|
|열 삭제 |기능화에서 삭제할 열입니다.|
|블록 변환기| 기능화 프로세스에 사용할 변환기를 차단 합니다.|

API 호출을 사용 하 여 FeaturizationConfig 개체를 만듭니다.
```python
featurization_config = FeaturizationConfig()
featurization_config.blocked_transformers = ['LabelEncoder']
featurization_config.drop_columns = ['aspiration', 'stroke']
featurization_config.add_column_purpose('engine-size', 'Numeric')
featurization_config.add_column_purpose('body-style', 'CategoricalHash')
#default strategy mean, add transformer param for for 3 columns
featurization_config.add_transformer_params('Imputer', ['engine-size'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['city-mpg'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['bore'], {"strategy": "most_frequent"})
featurization_config.add_transformer_params('HashOneHotEncoder', [], {"number_of_bits": 3})
```

### <a name="scalingnormalization-and-algorithm-with-hyperparameter-values"></a>하이퍼 매개 변수 값을 사용 하는 크기 조정/정규화 및 알고리즘:

파이프라인에 대 한 크기 조정/정규화 및 알고리즘/하이퍼 매개 변수 값을 이해 하려면 fitted_model을 사용 합니다. [크기 조정/정규화에 대해 자세히 알아보세요](concept-automated-ml.md#preprocess). 샘플 출력은 다음과 같습니다.

```
[('RobustScaler', RobustScaler(copy=True, quantile_range=[10, 90], with_centering=True, with_scaling=True)), ('LogisticRegression', LogisticRegression(C=0.18420699693267145, class_weight='balanced', dual=False, fit_intercept=True, intercept_scaling=1, max_iter=100, multi_class='multinomial', n_jobs=1, penalty='l2', random_state=None, solver='newton-cg', tol=0.0001, verbose=0, warm_start=False))
```

자세한 내용을 보려면 [이 샘플 노트북](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification/auto-ml-classification.ipynb)에 표시 된이 도우미 함수를 사용 합니다.

```python
from pprint import pprint


def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(
                e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0] + ' - ')
        else:
            pprint(step[1].get_params())
            print()


print_model(fitted_model)
```

다음 샘플 출력은 특정 알고리즘을 사용 하는 파이프라인에 대 한 것입니다 (이 경우 RobustScalar과 함께 LogisticRegression).

```
RobustScaler
{'copy': True,
'quantile_range': [10, 90],
'with_centering': True,
'with_scaling': True}

LogisticRegression
{'C': 0.18420699693267145,
'class_weight': 'balanced',
'dual': False,
'fit_intercept': True,
'intercept_scaling': 1,
'max_iter': 100,
'multi_class': 'multinomial',
'n_jobs': 1,
'penalty': 'l2',
'random_state': None,
'solver': 'newton-cg',
'tol': 0.0001,
'verbose': 0,
'warm_start': False}
```

### <a name="predict-class-probability"></a>예측 클래스 확률

자동화 된 ML을 사용 하 여 생성 된 모델에는 해당 오픈 소스 클래스에서 기능을 미러링 하는 래퍼 개체가 있습니다. 자동 ML에서 반환 하는 대부분의 분류 모델 래퍼 개체는 기능 (X 값)의 배열 형식 또는 스파스 행렬 데이터 샘플을 허용 하는 `predict_proba()` 함수를 구현 하 고 각 샘플의 n 차원 배열 및 해당 클래스 확률을 반환 합니다.

위에서 설명한 것과 동일한 호출을 사용 하 여 가장 적합 한 실행 및 적합 한 모델을 검색 한 경우 모델 유형에 따라 적절 한 형식으로 `X_test` 샘플을 제공 하 여 적절 한 형식으로 `predict_proba()`를 직접 호출할 수 있습니다.

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

기본 모델이 `predict_proba()` 함수를 지원 하지 않거나 형식이 잘못 된 경우 모델 클래스 관련 예외가 throw 됩니다. 다른 모델 형식에 대해이 함수를 구현 하는 방법에 대 한 예제는 [RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba) 및 [xgboost](https://xgboost.readthedocs.io/en/latest/python/python_api.html) 참조 문서를 참조 하세요.

<a name="explain"></a>

## <a name="model-interpretability"></a>모델 해석력

모델 interpretability를 사용 하면 모델이 예측을 수행한 이유와 기본 기능 중요도 값을 이해할 수 있습니다. SDK에는 로컬 및 배포 된 모델에 대 한 학습 및 유추 시간에 모델 interpretability 기능을 사용 하기 위한 다양 한 패키지가 포함 되어 있습니다.

자동화 된 기계 학습 실험 내에서 특히 interpretability 기능을 사용 하도록 설정 하는 방법에 대 한 코드 샘플은 [방법을](how-to-machine-learning-interpretability-automl.md) 참조 하세요.

자동화 된 machine learning 외부에서 SDK의 다른 영역에 모델 설명과 기능 중요도를 사용 하도록 설정 하는 방법에 대 한 일반적인 내용은 interpretability의 [개념](how-to-machine-learning-interpretability.md) 문서를 참조 하세요.

## <a name="next-steps"></a>다음 단계

[모델 배포 방법 및 위치](how-to-deploy-and-where.md)에 대해 자세히 알아봅니다.

[자동화 된 machine learning을 사용 하 여 회귀 모델을 학습](tutorial-auto-train-models.md) 하는 방법 또는 [원격 리소스에서 자동화 된 machine learning을 사용 하 여 학습](how-to-auto-train-remote.md)하는 방법에 대해 자세히 알아보세요.
