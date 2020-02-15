---
title: 자동화 된 ML/AutoML 이란?
titleSuffix: Azure Machine Learning
description: 사용자가 알고리즘을 자동으로 선택 하 고 모델에서 모델을 생성 하 여 사용자가 모델에 가장 적합 한 알고리즘을 선택 하는 데 사용 하는 매개 변수 및 조건을 사용 하 여 시간을 절약할 수 있는 Azure Machine Learning 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cartacioS
ms.author: sacartac
ms.date: 11/04/2019
ms.openlocfilehash: f7a2e78ed2b1de770f7a60f1312e069dc1757cb6
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191200"
---
# <a name="what-is-automated-machine-learning"></a>자동화된 Machine Learning이란?

자동화 된 ML이 라고도 하는 자동화 된 기계 학습은 기계 학습 모델 개발의 시간이 많이 걸리는 반복적인 작업을 자동화 하는 프로세스입니다. 이를 통해 데이터 과학자, 분석가 및 개발자는 모델 품질을 유지 하면서도 확장성, 효율성 및 생산성이 높은 ML 모델을 빌드할 수 있습니다. 자동화 된 ML은 [Microsoft Research 부서의](https://arxiv.org/abs/1705.05355)혁신을 기반으로 합니다.

기존의 기계 학습 모델 개발은 리소스를 많이 사용 하므로 수십 개의 모델을 생성 하 고 비교 하는 데 상당한 도메인 지식과 시간이 필요 합니다. 지정한 대상 메트릭을 사용 하 여 모델을 학습 하 고 조정 하려면 자동화 된 ML을 적용 Azure Machine Learning 합니다. 그런 다음 서비스는 기능 선택과 쌍을 이루는 ML 알고리즘을 반복 하 여 각 반복에서 학습 점수가 있는 모델을 생성 합니다. 점수가 높을수록 데이터를 "맞추기" 하는 것이 더 효율적입니다.

자동화 된 기계 학습을 통해 프로덕션이 준비 된 ML 모델을 매우 쉽고 효율적으로 활용 하는 데 걸리는 시간을 단축할 수 있습니다.

## <a name="when-to-use-automated-ml"></a>자동화 된 ML을 사용 하는 경우

자동화 된 ML은 기계 학습 모델 개발 프로세스를 보편화 하 고, 문제에 대 한 종단 간 기계 학습 파이프라인을 식별 하기 위해 데이터 과학 전문 지식에 관계 없이 사용자의 역량을 강화 합니다.

산업 전반에 걸친 데이터 과학자, 분석가 및 개발자는 자동화 된 ML을 사용 하 여 다음을 수행할 수 있습니다.

+ 광범위 한 프로그래밍 지식 없이 기계 학습 솔루션 구현
+ 시간 및 리소스 절약
+ 데이터 과학 모범 사례 활용
+ Agile 문제 제공-해결

다음 표에서는 일반적인 자동화 된 ML 사용 사례를 보여 줍니다. 

분류| 시계열 예측 | 회귀
---|---|---
[사기 감지](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)|[판매 예측](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)|[CPU 성능 예측](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb)
|[마케팅 예측](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)|[수요 예측](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)|
|[뉴스 그룹 데이터 분류](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb)|[음료 생산 예측](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)|

## <a name="how-automated-ml-works"></a>자동화 된 ML 작동 방법

**Azure Machine Learning**를 사용 하 여 다음 단계를 통해 자동화 된 ML 학습 실험을 디자인 하 고 실행할 수 있습니다.

1. 해결 될 기계 학습 **문제 식별** : 분류, 예측 또는 회귀

1. **레이블이 지정 된 학습 데이터의 원본 및 형식 지정**: Numpy Array 또는 Pandas 데이터 프레임

1. **모델 학습을 위한 계산 목표**(예: [로컬 컴퓨터, Azure Machine Learning 계산, 원격 vm 또는 Azure Databricks](how-to-set-up-training-targets.md))를 구성 합니다.  [원격 리소스에](how-to-auto-train-remote.md)대 한 자동화 된 학습에 대해 알아봅니다.

1. 다른 모델에 대 한 반복 횟수, 하이퍼 매개 변수 설정, 고급 전처리/기능화 및 최상의 모델을 결정할 때 살펴볼 메트릭을 결정 하는 **자동화 된 machine learning 매개 변수를 구성 합니다** .  [Azure Machine Learning studio](https://ml.azure.com)또는 [SDK를 사용](how-to-configure-auto-train.md)하 여 자동 학습 실험의 설정을 구성할 수 있습니다. 

    [!INCLUDE [aml-applies-to-enterprise-sku](../../includes/aml-applies-to-enterprise-sku-inline.md)]

1. **학습 실행을 제출 합니다.**

  ![자동화 된 기계 학습](./media/concept-automated-ml/automl-concept-diagram2.png)

학습 하는 동안 Azure Machine Learning는 여러 알고리즘 및 매개 변수를 시도 하는 많은 수의 병렬 파이프라인을 만듭니다. 실험에 정의 된 종료 조건에 도달 하면 중지 됩니다.

실행 중에 수집 된 [메트릭을 포함](how-to-understand-automated-ml.md) 하는 로그 된 실행 정보를 검사할 수도 있습니다. 학습 실행은 모델 및 데이터 전처리를 포함 하는 Python 직렬화 된 개체 (`.pkl` 파일)를 생성 합니다.

모델 빌드를 자동화 하는 동안 생성 된 모델에 [중요 하거나 관련 된 기능을 배울](how-to-configure-auto-train.md#explain) 수도 있습니다.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>전처리

자동화 된 모든 기계 학습 실험에서 데이터는 기본 메서드를 사용 하 여 전처리 되 고 선택적으로 고급 전처리를 통해 전처리 됩니다.

> [!NOTE]
> 자동화된 기계 학습 사전 처리 단계(기능 정규화, 누락된 데이터 처리, 텍스트를 숫자로 변환 등)는 기본 모델의 일부가 됩니다. 예측에 모델을 사용하는 경우 학습 중에 적용되는 동일한 전처리 단계가 입력 데이터에 자동으로 적용됩니다.

### <a name="automatic-preprocessing-standard"></a>자동 전처리 (표준)

자동화 된 모든 기계 학습 실험에서 데이터는 알고리즘의 성능을 향상 시킬 수 있도록 자동으로 확장 되거나 정규화 됩니다.  모델 학습 중에는 다음 크기 조정 또는 정규화 기술 중 하나가 각 모델에 적용 됩니다.

|&nbsp;정규화&nbsp;&크기 조정| Description |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | 단위 분산의 평균 및 크기 조정을 제거 하 여 기능 표준화  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | 해당 열의 최소값과 최대값을 기준으로 각 기능의 크기를 조정 하 여 기능을 변환 합니다.  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |최대 절대값으로 각 기능 크기 조정 |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |변 위치 범위에의 한 Scaler 기능 |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |데이터를 하위 차원 공간으로 프로젝션 하기 위한 단일 값 분해를 사용 하 여 선형 차원 축소 |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |이 변환기는 잘린 단일 값 분해 (.SVD)를 통해 선형 차원 감소를 수행 합니다. PCA와 달리이 평가기는 단일 값 분해를 계산 하기 전에 데이터를 중심으로 하지 않습니다. 즉, scipy 행렬을 효율적으로 사용할 수 있습니다. |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | 0이 아닌 구성 요소를 하나 이상 포함 하는 각 샘플 (즉, 데이터 행렬의 각 행)은 다른 샘플과 독립적으로 재조정 하 여 일반적인 (l1 또는 l2)와 동일 하 게 구성 됩니다. |

### <a name="advanced-preprocessing-optional-featurization"></a>고급 전처리: 선택적 기능화

데이터 guardrails, 인코딩, 변환 등의 추가 고급 전처리 및 기능화도 사용할 수 있습니다. [기능화 포함 된 항목에 대해 자세히 알아보세요](how-to-create-portal-experiments.md#featurization). 다음을 사용 하 여이 설정 사용:

+ Azure Machine Learning studio: **추가 구성 보기** 섹션에서 [다음 단계를 수행 하](how-to-create-portal-experiments.md#create-and-run-experiment)여 **자동 기능화** 를 사용 하도록 설정 합니다.

+ Python SDK: [`AutoMLConfig` 클래스](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)에 대 한 `"feauturization": 'auto' / 'off' / 'FeaturizationConfig'`를 지정 합니다. 

## <a name="prevent-over-fitting"></a>과도 맞춤 방지

기계 학습의 과도 한 맞춤은 모델이 학습 데이터에 적합 한 경우에 발생 하며,이로 인해 보이지 않는 테스트 데이터를 정확 하 게 예측할 수 없습니다. 즉, 모델은 단순히 학습 데이터의 특정 패턴 및 노이즈를 memorized 실제 데이터에 대 한 예측을 만들기에 충분 하지 않습니다. 황당한 대부분의 경우 학습 중에 표시 되는 기능 값 조합이 항상 대상에 대해 정확히 동일한 출력을 생성 한다고 가정 합니다. 

과도 한 맞춤을 방지 하는 가장 좋은 방법은 다음과 같은 ML 모범 사례를 따르는 것입니다.

* 추가 학습 데이터 사용 및 통계 바이어스 제거
* 대상 누출 방지
* 보다 작은 기능 사용
* **정규화 및 하이퍼 매개 변수 최적화**
* **모델 복잡성 제한 사항**
* **교차 유효성 검사**

자동화 된 ML의 컨텍스트에서 위의 처음 세 개 항목은 **구현 하**는 모범 사례입니다. 마지막 3 개의 굵게 표시 된 항목은 오버 맞춤을 방지 하기 위해 기본적으로 자동화 된 ML을 구현 하는 **최선의 방법** 입니다. 자동화 된 ML 이외의 설정에서는 오버 맞춤 모델을 방지 하기 위해 6 가지 모범 사례를 모두 따르는 것이 좋습니다.

### <a name="best-practices-you-implement"></a>구현 모범 사례

**더 많은 데이터** 를 사용 하는 것은 과도 한 맞춤을 방지 하는 가장 간단 하 고 좋은 방법 이며, 추가 된 보너스로 일반적으로 정확도를 높입니다. 더 많은 데이터를 사용 하는 경우 모델이 정확한 패턴을 기억 하는 것이 더 어려워집니다. 더 많은 조건을 수용 하기에 더 유연 하 게 솔루션에 연결 해야 합니다. 또한 학습 데이터에 라이브 예측 데이터에 존재 하지 않는 격리 된 패턴이 포함 되지 않도록 **통계 바이어스**를 인식 하는 것이 중요 합니다. 이 시나리오는 학습 및 테스트 집합 간에 과도 하 게 맞지 않을 수 있기 때문에 해결 하기 어려울 수 있지만 라이브 테스트 데이터와 비교할 때 과도 한 맞춤이 있을 수 있습니다.

대상 누출은 학습/테스트 집합 간에 과도 한 맞춤이 표시 되지 않는 유사한 문제 이며, 대신 예측 시간에 표시 됩니다. 대상 누출은 일반적으로 예측 시간에 포함 되지 않는 데이터에 액세스 하 여 학습 중에 모델 "속이거나" 할 때 발생 합니다. 예를 들어 월요일에 어떤 주가 요금을 예측 하는 것이 문제가 되는 경우에는 목요일의 데이터를 실수로 포함 하는 데이터입니다 .이는 미래를 확인할 수 없기 때문에 모델이 예측 시간에 포함 되지 않습니다. 대상 누출은 쉬운 실수 이지만 문제에 대 한 비정상적으로 높은 정확도로 규정 되는 경우가 많습니다. 주식 가격을 예측 하 고 95% 정확도로 모델을 학습 하는 경우 기능을 대상으로 하는 누출 가능성이 있습니다.

기능을 제거 하면 특정 패턴을 기억 하는 데 너무 많은 필드가 포함 되지 않아 더 유연 하 게 만들 수 있으므로 과도 하 게 맞출 수도 있습니다. Quantitatively을 측정 하는 것은 어려울 수 있지만 기능을 제거 하 고 동일한 정확도를 유지할 수 있는 경우 모델을 더 유연 하 게 만들어 과도 하 게 연결 하는 위험을 줄일 수 있습니다.

### <a name="best-practices-automated-ml-implements"></a>모범 사례 자동화 된 ML 구현

정규화는 복잡 하 고 과도 하 게 조정 된 모델을 penalize 하는 비용 함수를 최소화 하는 프로세스입니다. 정규화 함수에는 여러 가지 형식이 있지만 일반적으로 모델 계수 크기, 분산 및 복잡성을 모두 penalize 합니다. 자동화 된 ML은 오버 맞춤을 제어 하는 다른 모델 하이퍼 매개 변수 설정을 사용 하 여 서로 다른 조합에서 L1 (올가미), L2 (볼록) 및 ElasticNet (L1 및 L2)를 동시에 사용 합니다. 간단히 말해, 자동화 된 ML은 모델의 규정 수준을 변경 하 고 최상의 결과를 선택 합니다.

또한 자동화 된 ML은 과도 한 맞춤을 방지 하기 위해 명시적 모델 복잡성 제한을 구현 합니다. 대부분의 경우이 구현은 개별 트리의 최대 깊이가 제한 되는 의사 결정 트리 또는 포리스트 알고리즘과 포리스트 또는 앙상블 기술에 사용 되는 총 트리 수가 제한 되어 있습니다.

CV (교차 유효성 검사)는 전체 학습 데이터의 여러 하위 집합을 가져오고 각 하위 집합에서 모델을 학습 하는 프로세스입니다. 모델은 "운이 많은" 것이 가능 하 고 한 하위 집합을 사용 하 여 정확도가 매우 우수 하지만 많은 하위 집합을 사용 하는 경우 모델은 매번 이러한 높은 정확도를 얻지 못합니다. CV를 수행할 때 유효성 검사 홀드 아웃 데이터 집합을 제공 하 고, CV 접기 (하위 집합 수)를 지정 하 고, 자동화 된 ML은 모델을 학습 하 고 하이퍼 매개 변수를 조정 하 여 유효성 검사 집합에서 오류를 최소화 합니다 하나의 CV 접기는 과도 하 게 일치 하지만 대부분의 기능을 사용 하면 최종 모델이 과도 하 게 일치 하는 확률을 줄일 수 있습니다. 모델을 한 번 학습 하는 대신 각 *n* 개의 cv 하위 집합에 대해 한 번씩 학습 하기 때문에 CV로 인해 학습 시간이 더 오래 걸릴 수 있으므로 비용이 더 많이 듭니다.

> [!NOTE]
> 교차 유효성 검사는 기본적으로 사용 되지 않습니다. 자동 ML 설정에서 구성 해야 합니다. 그러나 CV를 구성 하 고 유효성 검사 데이터 집합을 제공 하 고 나면 프로세스가 자동으로 수행 됩니다.

### <a name="identifying-over-fitting"></a>과도 맞춤 식별

다음 학습 된 모델 및 해당 학습 및 테스트 정확도을 고려 합니다.

| 모델 | 학습 정확도 | 테스트 정확도 |
|-------|----------------|---------------|
| A | 99.9% | 95% |
| b | 87% | 87% |
| C | 99.9% | 45% |

모델 **A**를 고려 하 여 보이지 않는 데이터의 테스트 정확도가 학습 정확도 보다 낮으면 모델이 과도 하 게 조정 되는 일반적인 오해 있습니다. 그러나 테스트 정확도는 항상 학습 정확도 보다 작아야 하 고, 과도 하 게 일치 하는 것과 적절 한 일치에 대 한 차이는 *얼마나* 정확 하지 않습니다. 

모델 **a** 와 **B**를 비교할 때 모델 **a** 는 테스트 정확도가 높기 때문에 더 나은 모델입니다. 테스트 정확도가 95%에서 약간 낮은 경우에는 오버 맞춤을 제안 하는 것이 중요 한 차이가 아닙니다. 학습 및 테스트 정확도는 서로 가까이 있으므로 모델 **B** 를 선택 하지 않는 것이 좋습니다.

모델 **C** 는 오버 맞춤의 명확한 사례를 나타냅니다. 학습 정확도는 매우 높고 테스트 정확도는 많지 않습니다. 이러한 구분은 주관적 이지만 문제 및 데이터에 대 한 지식 및 크고 많을는 허용 되는 오류에 대 한 정보를 제공 합니다. 

## <a name="time-series-forecasting"></a>시계열 예측

예측 빌드는 수익, 재고, 판매 또는 고객 수요에 관계 없이 모든 비즈니스의 필수적인 부분입니다. 자동화 된 ML을 사용 하 여 기법과 접근 방식을 결합 하 고 권장 되는 고품질 시간 계열 예측을 얻을 수 있습니다.

자동화 된 시간 계열 실험은 다중 변형 회귀 문제로 처리 됩니다. 이전 시간 계열 값은 다른 예측 변수와 함께 회귀 변수의 추가 차원이 되도록 "피벗" 됩니다. 클래식 시계열 메서드와 달리이 방법은 학습 중에 여러 상황별 변수와 해당 변수 간의 관계를 자연스럽 게 통합 하는 이점을 제공 합니다. 자동화 된 ML은 데이터 집합 및 예측 horizons의 모든 항목에 대해 종종 내부적으로 분기 된 단일 모델을 학습 합니다. 따라서 모델 매개 변수를 예측 하는 데 더 많은 데이터를 사용할 수 있으며 보이지 않는 시리즈에 대 한 일반화가 가능 합니다.

자세한 정보 및 [시계열 예측에 대 한 자동화 된 기계 학습](how-to-auto-train-forecast.md)예를 참조 하세요. 또는 다음을 비롯 한 고급 예측 구성의 자세한 코드 예제는 [에너지 수요 노트북](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb) 을 참조 하세요.

* 휴일 검색 및 기능화
* 시계열 및 DNN 학습자 (Auto-ARIMA, Prophet, ForecastTCN)
* 그룹화를 통해 많은 모델 지원
* 롤링 원본 교차 유효성 검사
* 구성 가능한 지연
* 창 롤링 집계 기능

## <a name="ensemble"></a>앙상블 모델

자동화 된 machine learning은 기본적으로 사용 되는 앙상블 모델을 지원 합니다. 앙상블 learning은 단일 모델 사용과 반대로 여러 모델을 결합 하 여 기계 학습 결과와 예측 성능을 향상 시킵니다. 앙상블 반복은 실행의 최종 반복으로 나타납니다. 자동화 된 machine learning은 모델 결합을 위해 투표 및 스택 앙상블 메서드를 모두 사용 합니다.

* **투표**: 예측 된 클래스 확률 (분류 작업의 경우) 또는 예측 된 회귀 목표 (회귀 작업의 경우)의 가중치가 적용 된 평균을 기반으로 예측 합니다.
* **스택**: 스택은 다른 유형의 모델을 결합 하 고 개별 모델의 출력을 기반으로 메타 모델을 학습 합니다. 현재 기본 메타 모델은 분류 작업의 경우 LogisticRegression이 고 회귀/예측 작업의 경우 ElasticNet입니다.

[Caruana 앙상블 선택 알고리즘](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) 은 정렬 된 앙상블 초기화를 사용 하 여 앙상블 내에서 사용할 모델을 결정 하는 데 사용 됩니다. 높은 수준에서이 알고리즘은 가장 적합 한 개별 점수가 포함 된 최대 5 개의 모델을 사용 하 여 앙상블를 초기화 하 고 이러한 모델이 가장 높은 점수의 5% 임계값을 초과 하 여 초기 앙상블 저하를 방지 하는지 확인 합니다. 그런 다음 각 앙상블 반복에 대해 새 모델이 기존 앙상블에 추가 되 고 결과 점수가 계산 됩니다. 새 모델에서 기존 앙상블 점수가 향상 되 면 새 모델을 포함 하도록 앙상블이 업데이트 됩니다.

자동화 된 machine learning에서 기본 앙상블 설정을 변경 [하는 방법을](how-to-configure-auto-train.md#ensemble) 참조 하세요.

## <a name="imbalance"></a>불균형 데이터

불균형 데이터는 기계 학습 분류 시나리오에 대 한 데이터에 일반적으로 있으며 각 클래스에서 불균형 비율을 포함 하는 데이터를 참조 합니다. 이러한 불균형으로 인해 모델의 정확도가 잘못 인식 될 수 있습니다. 입력 데이터는 하나의 클래스를 기준으로 하기 때문에 학습 된 모델이 해당 바이어스를 모방 하 게 됩니다. 

기계 학습 워크플로를 단순화 하는 목표의 일환으로, 자동화 된 ML은와 같은 불균형 데이터를 처리 하는 데 도움이 되는 기본 제공 기능을 제공 합니다. 

- **가중치 열**: 자동 ML은 가중치가 적용 된 열을 입력으로 지원 하 여 데이터의 행이 위쪽 또는 아래쪽으로 이동 하 여 클래스를 "중요" 하 게 만들 수 있습니다.

- 자동화 된 ML에서 사용 되는 알고리즘은 최대 20:1의 불균형을 적절히 처리할 수 있습니다. 즉, 가장 일반적인 클래스는 가장 일반적인 클래스 보다 데이터에서 20 배 더 많은 행을 가질 수 있습니다.

### <a name="identify-models-with-imbalanced-data"></a>불균형 데이터를 사용 하 여 모델 식별

분류 알고리즘은 일반적으로 정확도로 계산 되므로 모델의 정확도 점수를 확인 하는 것은 불균형 데이터의 영향을 받는 경우를 식별 하는 좋은 방법입니다. 특정 클래스에 대해 매우 높은 정확도 나 매우 낮은 정확도가 있나요?

또한 자동화 된 ML 실행은 다음 차트를 자동으로 생성 하며,이를 통해 모델 분류의 정확성을 이해 하 고 불균형 데이터의 영향을 받을 수 있는 모델을 식별할 수 있습니다.

차트| Description
---|---
[혼동 행렬](how-to-understand-automated-ml.md#confusion-matrix)| 데이터의 실제 레이블에 대해 올바르게 분류 된 레이블을 평가 합니다. 
[전체 자릿수-회수](how-to-understand-automated-ml.md#precision-recall-chart)| 데이터의 찾은 레이블 인스턴스 비율에 대해 올바른 레이블의 비율을 평가 합니다. 
[ROC 곡선](how-to-understand-automated-ml.md#roc)| 가양성 레이블의 비율에 대해 올바른 레이블의 비율을 평가 합니다.

### <a name="handle-imbalanced-data"></a>불균형 데이터 처리 

다음 기술에서는 자동화 된 ML 외부의 불균형 데이터를 처리 하는 추가 옵션을 설명 합니다. 

- 더 작은 클래스를 위로 샘플링 하거나 더 큰 클래스를 다운 샘플링 하 여 클래스 불균형에도 재샘플링 합니다. 이러한 방법에는를 처리 하 고 분석 하기 위한 전문 기술이 필요 합니다.

- 불균형 데이터에 대해 더 잘 처리 하는 성능 메트릭을 사용 합니다. 예를 들어 F1 점수는 전체 자릿수 및 회수의 가중치가 적용 된 평균입니다. 전체 자릿수는 분류자의 exactness을 측정 합니다. 낮은 정밀도는 분류자의 완전성을 측정 하는 동안 매우 많은 가양성--,를 나타냅니다. 낮은 회수는 매우 많은 가양성을 나타냅니다. 

## <a name="use-with-onnx-in-c-apps"></a>앱에서 C# onnx와 함께 사용

Azure Machine Learning를 사용 하면 자동화 된 ML을 사용 하 여 Python 모델을 작성 하 고이를 ONNX 형식으로 변환할 수 있습니다. ONNX 런타임은을 지원 C#하므로, 기록이 필요 하지 않거나 REST 끝점이 도입 하는 C# 네트워크 대기 시간을 제외 하 고 앱에서 자동으로 작성 된 모델을 사용할 수 있습니다. [이 Jupyter 노트북에서](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)이 흐름의 예를 사용해 보세요.

## <a name="automated-ml-across-microsoft"></a>Microsoft에서 자동 ML

자동화 된 ML은 다음과 같은 다른 Microsoft 솔루션 에서도 사용할 수 있습니다.

|통합|Description|
|------------|-----------|
|[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)|Visual Studio를 사용 하 여 .NET 앱에서 자동 모델 선택 및 학습 ML.NET 자동화 된 ML을 사용 하 여 Visual Studio Code 합니다.|
|[HDInsight](../hdinsight/spark/apache-spark-run-machine-learning-automl.md)|HDInsight 클러스터의 Spark에서 자동화 된 ML 학습 작업을 병렬로 확장 하세요.|
|[Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)|Power BI에서 직접 machine learning 모델을 호출 합니다.|
|[SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)|SQL Server 2019 빅 데이터 클러스터의 데이터에 대해 새 machine learning 모델을 만듭니다.|

## <a name="next-steps"></a>다음 단계

자동화 된 machine learning을 사용 하 여 모델을 작성 하는 방법 및 예제를 참조 하세요.

+ [자습서: Azure 자동화 된 Machine Learning를 사용 하 여 자동으로 회귀 모델 학습](tutorial-auto-train-models.md) 을 수행 합니다.

+ 자동 학습 실험의 설정 구성:
  + Azure Machine Learning studio에서 [다음 단계를 사용](how-to-create-portal-experiments.md)합니다.
  + Python SDK를 사용 하 여 [다음 단계를 수행](how-to-configure-auto-train.md)합니다.

+ 시계열 데이터를 사용 하 여 자동으로 학습 하는 방법에 대해 설명 하 고 [다음 단계를 사용](how-to-auto-train-forecast.md)합니다.

+ 자동화 된 [기계 학습에 대 한 Jupyter Notebook 샘플](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/) 사용해 보기
