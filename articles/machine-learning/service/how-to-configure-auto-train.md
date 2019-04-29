---
title: 자동화된 ML 실험 만들기
titleSuffix: Azure Machine Learning service
description: 자동화된 기계 학습은 사용자를 위한 알고리즘을 선택하고 배포할 준비가 된 모델을 생성합니다. 자동화된 기계 학습 실험을 구성하는 데 사용할 수 있는 옵션에 대해 알아봅니다.
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 04/08/2019
ms.custom: seodec18
ms.openlocfilehash: 33d8e18dcec98710443623c03651aa568aa37009
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60819949"
---
# <a name="configure-automated-machine-learning-experiments"></a>자동화된 Machine Learning 실험 구성

자동화된 Machine Learning은 사용자를 위한 알고리즘과 하이퍼 매개 변수를 선택하고 배포할 준비가 된 모델을 생성합니다. 자동화된 Machine Learning 실험을 구성하는 데 사용할 수 있는 옵션에 대해 알아봅니다. 이 가이드에서는 다양한 구성 설정을 정의하는 방법에 대해 알아봅니다.

자동화된 Machine Learning 실험 예제를 보려면 [자습서: 자동화된 Machine Learning을 사용하여 분류 모델 학습](tutorial-auto-train-models.md) 또는 [클라우드의 자동화된 Machine Learning을 사용하여 모델 학습](how-to-auto-train-remote.md)을 참조하세요.

자동화된 기계 학습에서 사용할 수 있는 구성 옵션은 다음과 같습니다.

* 실험 유형 선택: 분류, 회귀, 시계열 예측
* 데이터 원본, 형식 및 데이터 가져오기
* 컴퓨팅 대상 선택: 로컬 또는 원격
* 자동화된 Machine Learning 실험 설정
* 자동화된 Machine Learning 실험 실행
* 모델 메트릭 탐색
* 모델 등록 및 배포

## <a name="select-your-experiment-type"></a>실험 유형 선택
실험을 시작하기 전에 해결하려는 기계 학습 문제의 종류를 결정해야 합니다. 자동화된 Machine Learning은 작업 유형으로 분류, 회귀 및 예측을 지원합니다.

자동화된 Machine Learning은 자동화 및 튜닝 프로세스 중에 다음 알고리즘을 지원합니다. 사용자는 알고리즘을 지정할 필요가 없습니다. DNN 알고리즘은 학습 중 사용 가능한 이지만 자동화 된 ML DNN 모델을 작성 하지 않습니다.

분류 | 회귀 | 시계열 예측
|-- |-- |--
[Logistic Regression](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Elastic Net](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Gradient Boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Decision Tree](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Decision Tree](https://scikit-learn.org/stable/modules/tree.html#regression)|[Decision Tree](https://scikit-learn.org/stable/modules/tree.html#regression)
[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K Nearest Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[Linear SVC](https://scikit-learn.org/stable/modules/svm.html#classification)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[LARS Lasso](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[C-SVC(Support Vector Classification)](https://scikit-learn.org/stable/modules/svm.html#classification)|[SGD(Stochastic Gradient Descent)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[SGD(Stochastic Gradient Descent)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Extremely Randomized Trees](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[DNN 분류자](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN 회귀 변수](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN 회귀 변수](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[선형 분류자 DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[선형 회귀 변수](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[선형 회귀 변수](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Naive Bayes](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)|
[SGD(Stochastic Gradient Descent)](https://scikit-learn.org/stable/modules/sgd.html#sgd)|


## <a name="data-source-and-format"></a>데이터 원본 및 형식
자동화된 Machine Learning은 로컬 데스크톱 또는 Azure Blob Storage와 같은 클라우드의 데이터를 지원합니다. 데이터는 scikit-learn 지원 데이터 형식으로 읽을 수 있습니다. 데이터를
* Numpy 배열 X(기능) 및 y(대상 변수 또는 레이블이라고도 함)로 읽을 수 있습니다.
* pandas 데이터 프레임

예제:

*   numpy 배열

    ```python
    digits = datasets.load_digits()
    X_digits = digits.data
    y_digits = digits.target
    ```

*   pandas 데이터 프레임

    ```python
    import pandas as pd
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"')
    # get integer labels
    df = df.drop(["Label"], axis=1)
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    ```

## <a name="fetch-data-for-running-experiment-on-remote-compute"></a>원격 계산에서 실험을 실행하기 위한 데이터 가져오기

원격 계산을 사용하여 실험을 실행하는 경우 데이터 가져오기는 별도의 `get_data()` Python 스크립트로 래핑되어야 합니다. 이 스크립트는 자동화된 Machine Learning 실험이 실행되는 원격 계산에서 실행됩니다. `get_data`는 각 반복마다 연결을 통해 데이터를 가져올 필요가 없습니다. `get_data`가 없으면 원격 계산에서 실행할 때 실험이 실패합니다.

`get_data`의 예제는 다음과 같습니다.

```python
%%writefile $project_folder/get_data.py
import pandas as pd
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import LabelEncoder
def get_data(): # Burning man 2016 data
    df = pd.read_csv("https://automldemods.blob.core.windows.net/datasets/PlayaEvents2016,_1.6MB,_3.4k-rows.cleaned.2.tsv", delimiter="\t", quotechar='"')
    # get integer labels
    le = LabelEncoder()
    le.fit(df["Label"].values)
    y = le.transform(df["Label"].values)
    df = df.drop(["Label"], axis=1)
    df_train, _, y_train, _ = train_test_split(df, y, test_size=0.1, random_state=42)
    return { "X" : df, "y" : y }
```

`AutoMLConfig` 개체에서 `data_script` 매개 변수를 지정하고 아래와 비슷한 `get_data` 스크립트 파일에 대한 경로를 제공합니다.

```python
automl_config = AutoMLConfig(****, data_script=project_folder + "/get_data.py", **** )
```

`get_data` 스크립트는 다음을 반환할 수 있습니다.

키 | Type | 상호 배타적 관계    | 설명
---|---|---|---
X | pandas 데이터 프레임 또는 numpy 배열 | data_train, label, columns |  학습할 모든 기능입니다.
y | pandas 데이터 프레임 또는 numpy 배열 |   label   | 학습할 데이터에 레이블을 지정합니다. 분류의 경우 정수 배열이어야 합니다.
X_valid | pandas 데이터 프레임 또는 numpy 배열   | data_train, label | _선택 사항_ 유효성을 검사할 모든 기능입니다. 지정하지 않으면 X가 학습과 유효성 검사 간에 분할됩니다.
y_valid |   pandas 데이터 프레임 또는 numpy 배열 | data_train, label | _선택 사항_ 유효성을 검사할 레이블 데이터입니다. 지정하지 않으면 y가 학습과 유효성 검사 간에 분할됩니다.
sample_weight | pandas 데이터 프레임 또는 numpy 배열 |   data_train, label, columns| _선택 사항_ 각 샘플에 대한 가중치입니다. 데이터 요소에 대해 서로 다른 가중치를 할당하려는 경우에 사용합니다.
sample_weight_valid | pandas 데이터 프레임 또는 numpy 배열 | data_train, label, columns |    _선택 사항_ 각 유효성 검사 샘플에 대한 가중치입니다. 지정하지 않으면 sample_weight가 학습과 유효성 검사 간에 분할됩니다.
data_train |    pandas 데이터 프레임 |  X, y, X_valid, y_valid |    학습할 모든 데이터(기능 + 레이블)입니다.
label | 문자열  | X, y, X_valid, y_valid |  레이블을 나타내는 data_train의 열입니다.
열 | 문자열 배열  ||  _선택 사항_ 기능에 사용할 열의 허용 목록입니다.
cv_splits_indices   | 정수 배열 ||  _선택 사항_ 교차 유효성 검사를 위해 데이터를 분할할 인덱스 목록입니다.

### <a name="load-and-prepare-data-using-dataprep-sdk"></a>DataPrep SDK를 사용하여 데이터 로드 및 준비
자동화된 Machine Learning 실험은 dataprep SDK를 사용하여 데이터 로드 및 변환을 지원합니다. SDK를 사용하면 다음 기능을 사용할 수 있습니다.

>* 구문 분석 매개 변수 유추(인코딩, 구분 기호, 헤더)를 사용하여 다양한 파일 형식에서 로드
>* 파일 로드 동안 유추를 사용한 형식 변환
>* MS SQL Server 및 Azure Data Lake Storage에 대한 연결 지원
>* 식을 사용하여 열 추가
>* 누락 값 입력
>* 예제별 열 파생
>* Filtering
>* 사용자 지정 Python 변환

data prep sdk에 대해 알아보려면 [모델링을 위한 데이터 준비 방법 문서](how-to-load-data.md)를 참조하세요.
다음은 data prep sdk를 사용하여 데이터를 로드하는 예제입니다.
```python
# The data referenced here was pulled from `sklearn.datasets.load_digits()`.
simple_example_data_root = 'https://dprepdata.blob.core.windows.net/automl-notebook-data/'
X = dprep.auto_read_file(simple_example_data_root + 'X.csv').skip(1)  # Remove the header row.
# You can use `auto_read_file` which intelligently figures out delimiters and datatypes of a file.

# Here we read a comma delimited file and convert all columns to integers.
y = dprep.read_csv(simple_example_data_root + 'y.csv').to_long(dprep.ColumnSelector(term='.*', use_regex = True))
```

## <a name="train-and-validation-data"></a>데이터 학습 및 유효성 검사

get_data()를 통하거나 `AutoMLConfig` 메서드에서 직접 별도의 학습 및 유효성 검사 집합을 지정할 수 있습니다.

## <a name="cross-validation-split-options"></a>교차 유효성 검사 분할 옵션

### <a name="k-folds-cross-validation"></a>K 접기 교차 유효성 검사

`n_cross_validations` 설정을 사용하여 교차 유효성 검사의 수를 지정합니다. 학습 데이터 집합은 무작위로 동일한 크기의 `n_cross_validations` 접기로 분할됩니다. 각 교차 유효성 검사 라운드 중에 접기 중 하나는 나머지 접기에 대해 학습된 모델의 유효성 검사에 사용됩니다. 이 프로세스는 각 접기가 유효성 검사 집합으로 한 번 사용될 때까지 `n_cross_validations` 라운드 동안 반복됩니다. 모든 `n_cross_validations` 라운드에 걸친 평균 점수가 보고되고 해당 모델이 전체 학습 데이터 세트에 대해 다시 학습됩니다. 

### <a name="monte-carlo-cross-validation-aka-repeated-random-sub-sampling"></a>몬테카를로 교차 유효성 검사(반복된 무작위 하위 샘플링)

`validation_size`를 사용하여 유효성 검사에 사용해야 하는 학습 데이터 세트의 비율을 지정하고, `n_cross_validations`를 사용하여 교차 유효성 검사의 수를 지정합니다. 각 교차 유효성 검사 라운드 중에 나머지 데이터에 대해 학습된 모델의 유효성 검사를 위해 `validation_size` 크기의 하위 집합이 무작위로 선택됩니다. 마지막으로, 모든 `n_cross_validations` 라운드에 걸친 평균 점수가 보고되고 해당 모델이 전체 학습 데이터 집합에 대해 다시 학습됩니다. 몬테카를로 시계열 예측에 대 한 지원 되지 않습니다.

### <a name="custom-validation-dataset"></a>사용자 지정 유효성 검사 데이터 세트

임의 분할에 적합 하지 않은 경우 사용자 지정 유효성 검사 데이터 집합, 일반적으로 시계열 데이터 또는 불균형 데이터를 사용 합니다. 사용자 고유의 유효성 검사 데이터 세트를 지정할 수 있습니다. 모델은 무작위 데이터 세트 대신 지정된 유효성 검사 데이터 세트에 대해 평가됩니다.

## <a name="compute-to-run-experiment"></a>실험 실행 계산

다음으로, 모델을 학습할 위치를 결정합니다. 자동화된 Machine Learning 실험은 다음 컴퓨팅 옵션에서 실행할 수 있습니다.
*   로컬 데스크톱 또는 랩톱과 같은 로컬 머신 - 일반적으로 데이터 세트가 작고 아직 탐색 단계에 있는 경우입니다.
*   클라우드의 원격 머신 - [Azure Machine Learning Managed Compute](concept-azure-machine-learning-architecture.md#managed-and-unmanaged-compute-targets)는 Azure Virtual Machines 클러스터에서 Machine Learning 모델을 학습할 수 있도록 하는 관리형 서비스입니다.

로컬 및 원격 컴퓨팅 대상이 있는 예제 노트에 대해서는 [GitHub 사이트](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)를 참조하세요.

<a name='configure-experiment'></a>

## <a name="configure-your-experiment-settings"></a>실험 설정 구성

자동화된 Machine Learning 실험을 구성하는 데 사용할 수 있는 옵션에 대해 알아봅니다. 이러한 매개 변수는 `AutoMLConfig` 개체를 인스턴스화하여 설정됩니다. 매개 변수의 전체 목록은 [AutoMLConfig 클래스](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py)를 참조하세요.  

일부 사례:

1.  반복당 최대 시간이 12,000초인 기본 메트릭으로 가중된 AUC를 사용하는 분류 실험이며, 이 실험은 50회 반복 및 2회 교차 유효성 검사 접기 후에 종료됩니다.

    ```python
    automl_classifier = AutoMLConfig(
        task='classification',
        primary_metric='AUC_weighted',
        max_time_sec=12000,
        iterations=50,
        blacklist_models='XGBoostClassifier',
        X=X,
        y=y,
        n_cross_validations=2)
    ```
2.  다음은 회귀 실험이 100회 반복된 후 종료되도록 설정한 예제이며, 각 반복이 5회 교차 유효성 검사 접기를 통해 최대 600초까지 지속됩니다.

    ```python
    automl_regressor = AutoMLConfig(
        task='regression',
        max_time_sec=600,
        iterations=100,
        whitelist_models='kNN regressor'
        primary_metric='r2_score',
        X=X,
        y=y,
        n_cross_validations=5)
    ```

세 가지 `task` 매개 변수 값에 적용할 알고리즘의 목록을 확인 합니다.  `whitelist` 또는 `blacklist` 매개 변수를 사용하여 사용 가능한 알고리즘 반복을 포함 또는 제외하도록 추가로 수정합니다. 지원 되는 모델 목록에서 찾을 수 있습니다 [SupportedAlgorithms 클래스](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.constants.supportedalgorithms?view=azure-ml-py)합니다.

## <a name="primary-metric"></a>기본 메트릭
기본 메트릭; 와 같이 위의 예에서는 최적화에 대 한 모델을 학습 하는 동안 사용할 메트릭을 결정 합니다. 기본 메트릭은 선택할 수 있습니다 선택 하면 작업 형식에 의해 결정 됩니다. 사용 가능한 메트릭 목록은 다음과 같습니다.

|분류 | 회귀 | 시계열 예측
|-- |-- |--
|accuracy| spearman_correlation | spearman_correlation
|AUC_weighted | normalized_root_mean_squared_error | normalized_root_mean_squared_error
|average_precision_score_weighted | r2_score | r2_score
|norm_macro_recall | normalized_mean_absolute_error | normalized_mean_absolute_error
|precision_score_weighted |

## <a name="data-pre-processing-and-featurization"></a>데이터 전처리 및 기능화

`preprocess=True`를 사용하는 경우 다음 데이터 전처리 단계가 자동으로 수행됩니다.
1.  높은 카디널리티 또는 분산 없는 기능 삭제
    * 학습 및 유효성 검사 세트에서 얻은 유용한 정보가 없는 기능을 삭제합니다. 여기에는 값이 모두 누락된 기능, 모든 행에서 값이 같은 기능 또는 카디널리티(예: 해시, ID 또는 GUID)가 매우 높은 기능이 포함됩니다.
1.  값 대체 없음
    *   숫자 기능의 경우 누락된 값을 열 값 평균으로 대체합니다.
    *   범주 기능의 경우 누락된 값을 가장 자주 사용되는 값으로 대체합니다.
1.  추가 기능 생성
    * DateTime 기능: 연도, 월, 일, 요일, 연간 일자, 분기, 연간 주, 시간, 분, 초
    * 텍스트 기능: 단어 유니그램, 바이그램 및 트라이그램을 기준으로 하는 조건 빈도, 개수 벡터화
1.  변환 및 인코딩
    * 매우 적은 수의 고유 값을 갖는 숫자 기능이 범주 기능으로 변환됩니다.
    * 범주 기능의 카디널리티에 따라, 레이블 인코딩 또는 (해싱) 원 핫 인코딩을 수행합니다.

## <a name="ensemble-models"></a>앙상블 모델
달리 단일 모델을 사용 하 여 여러 모델을 결합 함으로써 여 machine learning 결과 예측 성능을 개선 하는 앙상블 학습 합니다. 사용 하 여 machine learning 자동화 하는 경우 사용 하 여 앙상블 모델을 학습 시킬 수는 [정렬 된 앙상블 초기화를 사용 하 여 Caruana 앙상블 선택 알고리즘](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf)합니다. 앙상블 반복 실행의 마지막 반복으로 표시 됩니다.

## <a name="run-experiment"></a>실험 실행

실행하려는 실험을 제출하고 모델을 생성합니다. `AutoMLConfig`를 `submit` 메서드에 전달하여 모델을 생성합니다.

```python
run = experiment.submit(automl_config, show_output=True)
```

>[!NOTE]
>새 머신에 먼저 종속성이 설치됩니다.  출력이 표시되는 데 최대 10분이 걸릴 수 있습니다.
>`show_output`을 `True`로 설정하면 출력이 콘솔에 표시됩니다.

## <a name="exit-criteria"></a>종료 기준 
있는 몇 가지 옵션을 정의할 수 있습니다 실험을 완료 합니다.
1. 조건 없는-정의 하지 않는 경우 기본 메트릭이에서 더 이상 진행이 만들어질 때까지 계속 실험 하는 매개 변수를 종료 합니다. 
1. 수의 반복-실행 하는 실험에 대 한 반복 횟수를 정의 합니다. 선택적 있습니다 iteration_timeout_minutes 각 반복 당 분의 시간 제한을 정의를 추가 합니다.
1. 시간-실험 하 여 계속 실행 기간 (분) 정의할 수 있습니다 설정에서 사용 하 여 experiment_timeout_minutes 후 종료 합니다.
1. Experiment_exit_score 기본 메트릭을 기준으로 점수에 도달한 후에 experiement 완료 하도록 선택할 수 있습니다를 사용 하 여 점수-도달한 후 종료 합니다.

## <a name="explore-model-metrics"></a>모델 메트릭 탐색
노트북을 사용하고 있으면 위젯 또는 인라인에서 결과를 볼 수 있습니다. 자세한 내용은 [모델 추적 및 평가](how-to-track-experiments.md#view-run-details)를 참조하세요.


### <a name="classification-metrics"></a>분류 메트릭
다음 메트릭은 분류 작업에 대한 각 반복에 저장됩니다.

|메트릭|설명|계산|추가 매개 변수
--|--|--|--|
AUC_Macro| AUC는 Receiver Operating Characteristic Curve 아래의 영역입니다. Macro(매크로)는 각 클래스에 대한 AUC의 산술 평균입니다.  | [계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="macro"|
AUC_Micro| AUC는 Receiver Operating Characteristic Curve 아래의 영역입니다. Micro(마이크로)는 각 클래스의 참 긍정과 거짓 긍정을 결합하여 전역적으로 컴퓨팅됩니다.| [계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="micro"|
AUC_Weighted  | AUC는 Receiver Operating Characteristic Curve 아래의 영역입니다. Weighted(가중치)는 각 클래스의 true 인스턴스 수를 가중치로 적용하여 계산한 각 클래스 점수의 산술 평균입니다.| [계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|average="weighted"
accuracy|Accuracy(정확도)는 true 레이블과 정확히 일치하는 예측된 레이블의 백분율입니다. |[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |없음|
average_precision_score_macro|Average precision(평균 정밀도)은 정밀도-재현율 곡선을 이전 임계값에서의 재현율 증가를 가중치로 사용하여 계산한 각 임계값에서 도달된 정밀도의 가중 평균으로 요약합니다. Macro는 각 클래스 평균 정밀도 점수의 산술 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="macro"|
average_precision_score_micro|Average precision(평균 정밀도)은 정밀도-재현율 곡선을 이전 임계값에서의 재현율 증가를 가중치로 사용하여 계산한 각 임계값에서 도달된 정밀도의 가중 평균으로 요약합니다. Micro(마이크로)는 각 차단에서의 참 긍정과 거짓 긍정을 결합하여 전역적으로 계산됩니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="micro"|
average_precision_score_weighted|Average precision(평균 정밀도)은 정밀도-재현율 곡선을 이전 임계값에서의 재현율 증가를 가중치로 사용하여 계산한 각 임계값에서 도달된 정밀도의 가중 평균으로 요약합니다. Weighted(가중치)는 각 클래스의 true 인스턴스 수를 가중치로 적용하여 계산한 각 클래스에 대한 평균 정밀도 점수의 산술 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="weighted"|
balanced_accuracy|Balanced accuracy(균형 정확도)는 각 클래스 재현율의 산술 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
f1_score_macro|F1 score(F1 점수)는 정밀도 및 재현율의 조화 평균입니다. Macro(매크로)는 각 클래스에 대한 F1 점수의 산술 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="macro"|
f1_score_micro|F1 score(F1 점수)는 정밀도 및 재현율의 조화 평균입니다. Micro(마이크로)는 총 참 긍정, 거짓 부정 및 거짓 긍정을 계산하여 전역적으로 계산됩니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="micro"|
f1_score_weighted|F1 score(F1 점수)는 정밀도 및 재현율의 조화 평균입니다. Weighted(가중치)는 각 클래스에 대한 F1 점수의 클래스 빈도별 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="weighted"|
log_loss|이 함수는 (다항) 로지스틱 회귀와 그 확장(예: 신경망)에 사용되는 손실 함수로, 지정된 확률적 분류자 예측에 대한 true 레이블의 음수 로그 유사도로 정의됩니다. {0,1}에서 true 레이블 yt와 예상 확률 yp를 갖는 단일 샘플에서 yt = 1인 경우 로그 손실은 -log P(yt&#124;yp) = -(yt log(yp) + (1 - yt) log(1 - yp))입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|없음|
norm_macro_recall|Normalized Macro Recall(정규화된 매크로 재현율)은 임의 성능 점수가 0이고 완벽한 성능 점수가 1이 되도록 정규화된 매크로 재현율입니다. 이 값은 norm_macro_recall := (recall_score_macro - R)/(1 - R)로 얻습니다. 여기서 R은 임의 예측에 대한 recall_score_macro의 예상 값입니다[예: 이진 분류의 경우 R=0.5, C-클래스 분류 문제의 경우 R=(1/C)].|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average = "macro", (recall_score_macro - R)/(1 - R)이 됩니다. 여기서 R은 임의 예측에 대한 recall_score_macro의 예상 값입니다[예: 이진 분류의 경우 R=0.5, C-클래스 분류 문제의 경우 R=(1/C)].|
precision_score_macro|Precision(정밀도)은 실제로 해당 클래스에 있는 특정 클래스로 레이블이 지정된 요소의 백분율입니다. Macro(매크로)는 각 클래스에 대한 정밀도의 산술 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="macro"|
precision_score_micro|Precision(정밀도)은 실제로 해당 클래스에 있는 특정 클래스로 레이블이 지정된 요소의 백분율입니다. Micro(마이크로)는 총 참 긍정 및 거짓 긍정을 계산하여 전역적으로 계산됩니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="micro"|
precision_score_weighted|Precision(정밀도)은 실제로 해당 클래스에 있는 특정 클래스로 레이블이 지정된 요소의 백분율입니다. Weighted(가중치)는 각 클래스의 true 인스턴스 수를 가중치로 적용하여 계산한 각 클래스 정밀도의 산술 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="weighted"|
recall_score_macro|Recall(재현율)은 특정 클래스에서 올바르게 레이블이 지정된 요소의 백분율입니다. Macro(매크로)는 각 클래스에 대한 재현율의 산술 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
recall_score_micro|Recall(재현율)은 특정 클래스에서 올바르게 레이블이 지정된 요소의 백분율입니다. Micro(마이크로)는 총 참 긍정 및 거짓 부정을 계산하여 전역적으로 계산됩니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="micro"|
recall_score_weighted|Recall(재현율)은 특정 클래스에서 올바르게 레이블이 지정된 요소의 백분율입니다. Weighted(가중치)는 각 클래스의 true 인스턴스 수를 가중치로 적용하여 계산한 각 클래스 재현율의 산술 평균입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="weighted"|
weighted_accuracy|Weighted accuracy(가중 정확도)는 각 예제에 제공된 가중치가 해당 예제의 true 클래스에 있는 true 인스턴스의 비율과 같아지는 정확도입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight는 대상의 각 요소에 대한 해당 클래스의 비율과 동일한 벡터입니다.|

### <a name="regression-and-time-series-forecasting-metrics"></a>회귀 및 시간 시계열 메트릭 예측
다음 메트릭은 회귀 또는 예측 작업에 대한 각 반복에 저장됩니다.

|메트릭|설명|계산|추가 매개 변수
--|--|--|--|
explained_variance|Explained variance(설명된 분산)는 수학 모델에서 지정된 데이터 세트의 편차가 고려되는 비율입니다. 오차 분산에 대한 원래 데이터의 분산 감소율입니다. 오차의 평균이 0이면 explained variance와 같습니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|없음|
r2_score|R2는 평균을 출력하는 기준선 모델과 비교한 제곱 오차의 결정 계수 또는 환원율입니다. 오차의 평균이 0이면 explained variance와 같습니다.|[계산](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|없음|
spearman_correlation|Spearman correlation(Spearman 상관 관계)은 두 데이터 세트 간 관계의 비모수 단조성 측정입니다. Pearson 상관 관계와 달리 Spearman 상관 관계는 두 데이터 세트가 모두 정규적으로 분포된다고 가정하지 않습니다. 다른 상관 계수처럼, 이 방식은 -1과 +1 사이에서 달라지며 0은 상관 관계가 없음을 의미합니다. - 1 또는 +1의 상관 관계는 정확히 단조성 관계를 의미합니다. 양의 상관 관계는 x가 증가할 때 y도 증가함을 의미합니다. 음의 상관 관계는 x가 증가할 때 y는 감소함을 의미합니다.|[계산](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|없음|
mean_absolute_error|Mean absolute error(평균 절대 오차)는 목표와 예측 간 차이의 예상 절대값입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|없음|
normalized_mean_absolute_error|Normalized mean absolute error(정규화된 평균 절대 오차)는 평균 절대 오차를 데이터 범위로 나눈 것입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|데이터 범위로 나누기|
median_absolute_error|Median absolute error(중앙값 절대 오차)는 목표와 예측 간 모든 절대 차이의 중앙값입니다. 이 손실은 이상값보다 강력합니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|없음|
normalized_median_absolute_error|Normalized median absolute error(정규화된 중앙값 절대 오차)는 중앙값 절대 오차를 데이터 범위로 나눈 것입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|데이터 범위로 나누기|
root_mean_squared_error|Root mean squared error(제곱 평균 오차)는 목표와 예측 간 예상 제곱 차이의 제곱근입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|없음|
normalized_root_mean_squared_error|Normalized root mean squared error(정규화된 제곱 평균 오차)는 제곱 평균 오차를 데이터 범위로 나눈 것입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|데이터 범위로 나누기|
root_mean_squared_log_error|Root mean squared log error(제곱 평균 로그 오차)는 예상 제곱 로그 오차의 제곱근입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|없음|
normalized_root_mean_squared_log_error|Noramlized Root mean squared log error(정규화된 제곱 평균 로그 오차)는 제곱 평균 로그 오차를 데이터 범위로 나눈 것입니다.|[계산](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|데이터 범위로 나누기|

## <a name="explain-the-model"></a>모델 설명

자동화된 Machine Learning 기능은 일반 공급되지만 **모델 설명 기능은 아직 공용 미리 보기로 제공됩니다.**

자동화된 Machine Learning을 사용하면 기능 중요도를 이해할 수 있습니다.  학습 프로세스 동안 모델의 전역 기능 중요도를 얻을 수 있습니다.  분류 시나리오의 경우 클래스 수준 기능 중요도를 얻을 수도 있습니다.  기능 중요도를 얻으려면 유효성 검증 데이터 세트(X_valid)를 제공해야 합니다.

기능 중요도를 생성하는 방법에는 다음 두 가지가 있습니다.

*   실험이 완료되면 모든 반복에 대해 `explain_model` 메서드를 사용할 수 있습니다.

    ```python
    from azureml.train.automl.automlexplainer import explain_model

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
        explain_model(fitted_model, X_train, X_test)

    #Overall feature importance
    print(overall_imp)
    print(overall_summary)

    #Class-level feature importance
    print(per_class_imp)
    print(per_class_summary)
    ```

*   모든 반복의 기능 중요도를 보려면 AutoMLConfig에서 `model_explainability` 플래그를 `True`로 설정하세요.

    ```python
    automl_config = AutoMLConfig(task = 'classification',
                                 debug_log = 'automl_errors.log',
                                 primary_metric = 'AUC_weighted',
                                 max_time_sec = 12000,
                                 iterations = 10,
                                 verbosity = logging.INFO,
                                 X = X_train,
                                 y = y_train,
                                 X_valid = X_test,
                                 y_valid = y_test,
                                 model_explainability=True,
                                 path=project_folder)
    ```

    완료되면 retrieve_model_explanation 메서드를 사용하여 특정 반복의 기능 중요도를 검색할 수 있습니다.

    ```python
    from azureml.train.automl.automlexplainer import retrieve_model_explanation

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
        retrieve_model_explanation(best_run)

    #Overall feature importance
    print(overall_imp)
    print(overall_summary)

    #Class-level feature importance
    print(per_class_imp)
    print(per_class_summary)
    ```

Azure Portal의 작업 영역에서 기능 중요도 차트를 시각화할 수 있습니다. 이 차트는 노트에서 Jupyter 위젯을 사용할 때도 표시됩니다. 차트에 대한 자세한 내용은 [샘플 Azure Machine Learning Service Notebook 문서](samples-notebooks.md)를 참조하세요.

```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```
![기능 중요도 그래프](./media/how-to-configure-auto-train/feature-importance.png)

## <a name="next-steps"></a>다음 단계

[모델 배포 방법 및 위치](how-to-deploy-and-where.md)에 대해 자세히 알아봅니다.

에 대해 자세히 알아보세요 [자동화 된 machine learning 사용 하 여 회귀 모델을 학습 하는 방법을](tutorial-auto-train-models.md) 하거나 [사용 하 여 학습 하는 방법에는 machine learning 원격 리소스에 자동화 된](how-to-auto-train-remote.md)합니다.
