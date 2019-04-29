---
title: 모델에 대한 하이퍼 매개 변수 튜닝
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning 서비스를 사용하여 딥 러닝/기계 학습 모델에 대한 하이퍼 매개 변수를 효율적으로 조정합니다. 매개 변수 검색 공간을 정의하고, 최적화할 기본 메트릭을 지정하여 성능이 불량한 실행을 최적화하고 초기 종료하는 방법을 알아봅니다.
ms.author: swatig
author: swatig007
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 48f714a505bc79f0556a829206821aef986ad5d0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60818065"
---
# <a name="tune-hyperparameters-for-your-model-with-azure-machine-learning-service"></a>Machine Learning Service를 사용하여 모델에 대한 하이퍼 매개 변수 튜닝

Azure Machine Learning 서비스를 사용하여 모델에 대한 하이퍼 매개 변수를 효율적으로 조정합니다.  하이퍼 매개 변수 조정에는 다음 단계가 포함됩니다.

* 매개 변수 검색 공간 정의
* 최적화할 기본 메트릭 지정  
* 성능이 불량한 실행에 대한 초기 종료 조건 지정
* 하이퍼 매개 변수 튜닝에 대한 리소스 할당
* 위의 구성을 사용하여 실험 시작
* 교육 실행 시각화
* 모델에 대해 성능이 뛰어난 구성 선택

## <a name="what-are-hyperparameters"></a>하이퍼 매개 변수란?

하이퍼 매개 변수는 학습 프로세스 자체를 관리하는 모델을 학습하기 위해 선택한 조정 가능한 매개 변수입니다. 예를 들어 모델을 학습하기 전에 심층 신경망을 학습하려면 네트워크에서 숨겨진 계층 수 및 각 계층의 노드 수를 결정해야 합니다. 이러한 값은 일반적으로 학습 프로세스 중 일관되게 유지됩니다.

딥 러닝/기계 학습 시나리오에서 모델 성능은 선택한 하이퍼 매개 변수 값에 크게 의존합니다. 하이퍼 매개 변수 탐색의 목표는 최적의 성능을 제공하는 구성을 찾기 위해 다양한 하이퍼 매개 변수 구성을 검색하는 것입니다. 일반적으로 하이퍼 매개 변수 탐색 프로세스는 검색 공간이 방대하고 각 구성을 평가하는 데 많은 비용이 들 수 있으므로 매우 수동적입니다.

Azure Machine Learning을 통해 효율적인 방식으로 하이퍼 매개 변수 탐색을 자동화하여 상당한 시간과 리소스를 절약할 수 있습니다. 하이퍼 매개 변수 값의 범위 및 실행될 최대 학습 수를 지정합니다. 그러면 시스템은 다른 매개 변수 구성을 사용하여 여러 동시 실행을 자동으로 시작하고, 선택한 메트릭으로 측정했을 때 최적의 성능을 제공하는 구성을 찾습니다. 성능이 낮은 학습 실행은 자동으로 초기 종료되어 계산 리소스의 낭비가 줄어듭니다. 이러한 리소스는 다른 하이퍼 매개 변수 구성을 탐색하는 데 대신 사용됩니다.


## <a name="define-search-space"></a>검색 공간 정의

각 하이퍼 매개 변수에 대해 정의된 값의 범위를 탐색하여 하이퍼 매개 변수를 자동으로 조정합니다.

### <a name="types-of-hyperparameters"></a>하이퍼 매개 변수 형식

각 하이퍼 매개 변수는 불연속 또는 연속일 수 있습니다.

#### <a name="discrete-hyperparameters"></a>개별 하이퍼 매개 변수 

개별 하이퍼 매개 변수는 불연속 값 중 `choice`로 지정됩니다. `choice`는 다음과 같을 수 있습니다.

* 하나 이상의 쉼표로 구분된 값
* `range` 개체
* 임의의 `list` 개체


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

이 사례에서 `batch_size`는 [16, 32, 64, 128] 값 중 하나를 사용하고, `number_of_hidden_layers`는 [1, 2, 3, 4] 값 중 하나를 사용합니다.

고급 불연속 하이퍼 매개 변수는 배포를 사용하여 지정될 수도 있습니다. 다음 배포가 지원됩니다.

* `quniform(low, high, q)` - 라운드와 같은 값 반환(uniform(low, high) / q) * q
* `qloguniform(low, high, q)` - 라운드와 같은 값 반환(exp(uniform(low, high)) / q) * q
* `qnormal(mu, sigma, q)` - 라운드와 같은 값 반환(normal(mu, sigma) / q) * q
* `qlognormal(mu, sigma, q)` - 라운드와 같은 값 반환(exp(normal(mu, sigma)) / q) * q

#### <a name="continuous-hyperparameters"></a>연속 하이퍼 매개 변수 

연속 하이퍼 매개 변수는 연속 값의 범위에 대해 배포로 지정됩니다. 지원되는 배포는 다음을 포함합니다.

* `uniform(low, high)` - low와 high 간에 균일하게 배포되는 값 반환
* `loguniform(low, high)` - 반환 값의 로그가 균일하게 배포되도록 exp(uniform(low, high))에 따라 그려진 값 반환
* `normal(mu, sigma)` - 평균 mu 및 표준 편차 sigma를 사용하여 일반적으로 배포되는 실제 값 반환
* `lognormal(mu, sigma)` - 반환 값의 로그가 균일하게 배포되도록 exp(normal(mu, sigma))에 따라 그려진 값 반환

매개 변수 공간 정의의 예제는 다음과 같습니다.

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

이 코드는 `learning_rate` 및 `keep_probability`라는 두 개의 매개 변수를 사용하여 검색 공간을 정의합니다. `learning_rate`는 평균 값 10과 표준 편차 3이 있는 정규 분포를 갖게 됩니다. `keep_probability`는 최솟값 0.05 및 최댓값 0.1이 있는 균일한 분포를 갖게 됩니다.

### <a name="sampling-the-hyperparameter-space"></a>하이퍼 매개 변수 공간 샘플링

하이퍼 매개 변수 공간 정의를 통해 사용하도록 매개 변수 샘플링 방법을 지정할 수도 있습니다. Azure Machine Learning 서비스는 무작위 샘플링, 그리드 샘플링 및 Bayesian 샘플링을 지원합니다.

#### <a name="random-sampling"></a>무작위 샘플링

무작위 샘플링에서 하이퍼 매개 변수 값은 정의된 검색 공간에서 임의로 선택됩니다. 무작위 샘플링은 불연속 및 연속 하이퍼 매개 변수를 포함하는 검색 공간을 허용합니다.

```Python
from azureml.train.hyperdrive import RandomParameterSampling
param_sampling = RandomParameterSampling( {
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

#### <a name="grid-sampling"></a>그리드 샘플링

그리드 샘플링은 정의된 검색 공간에서 사용할 수 있는 모든 값을 통해 간단한 그리드 검색을 수행합니다. `choice`를 사용하여 지정된 하이퍼 매개 변수를 통해서만 사용할 수 있습니다. 예를 들어 다음 공간에는 총 6개의 샘플이 있습니다.

```Python
from azureml.train.hyperdrive import GridParameterSampling
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>Bayesian 샘플링

Bayesian 샘플링은 Bayesian 최적화 알고리즘을 기반으로 하며 다음에 샘플링할 하이퍼 매개 변수 값에 대한 지능적인 선택을 합니다. 새 샘플이 보고된 기본 메트릭을 개선하기 위해 이전 샘플이 수행한 방법에 따라 이 샘플을 선택합니다.

Bayesian 샘플링을 사용할 때 동시 실행 수는 조정 프로세스의 효율성에 영향을 줍니다. 병렬 처리 수준이 작으면 이전에 완료된 실행에서 활용하는 실행 수가 증가하므로 일반적으로 동시 실행 수가 적으면 더 나은 샘플링 수렴이 가능합니다.

Bayesian 샘플링은 검색 공간을 통해 `choice` 및 `uniform` 배포만을 지원합니다. 

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

> [!NOTE]
> Bayesian 샘플링은 초기 종료 정책을 지원하지 않습니다([초기 종료 정책 지정](#specify-early-termination-policy) 참조). Bayesian 매개 변수 샘플링을 사용할 때 `early_termination_policy = None`을 설정하거나 `early_termination_policy` 매개 변수를 해제해 둡니다.

<a name='specify-primary-metric-to-optimize'/>

## <a name="specify-primary-metric"></a>기본 메트릭 지정

하이퍼 매개 변수 실험을 최적화할 기본 메트릭을 지정합니다. 각 교육 실행은 기본 메트릭에 대해 평가됩니다. 성능이 불량한 실행(기본 메트릭이 초기 종료 정책에서 설정된 기준을 충족하지 않음)은 종료됩니다. 기본 메트릭 이름 외에도 기본 메트릭을 최대화하거나 최소화할지 최적화의 목표를 지정합니다.

* `primary_metric_name`: 최적화할 기본 메트릭의 이름입니다. 기본 메트릭 이름은 학습 스크립트에서 기록된 메트릭의 이름과 정확히 일치해야 합니다. [하이퍼 매개 변수 튜닝에 대한 메트릭 기록](#log-metrics-for-hyperparameter-tuning)을 참조하세요.
* `primary_metric_goal`: `PrimaryMetricGoal.MAXIMIZE` 또는 `PrimaryMetricGoal.MINIMIZE`일 수 있으며 실행을 평가할 때 기본 메트릭을 최대화할지 또는 최소화할지 여부를 결정합니다. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

"정확도"를 최대화하도록 실행을 최적화합니다.  학습 스크립트에서 이 값을 기록해야 합니다.

<a name='log-metrics-for-hyperparameter-tuning'/>

### <a name="log-metrics-for-hyperparameter-tuning"></a>하이퍼 매개 변수 튜닝에 대한 메트릭 기록

모델의 학습 스크립트는 모델을 학습하는 동안 관련 메트릭을 기록해야 합니다. 하이퍼 매개 변수 조정을 구성하면 실행 성능을 평가하는 데 사용할 기본 메트릭을 지정합니다. [최적화할 기본 메트릭 지정](#specify-primary-metric-to-optimize)을 참조하세요.  학습 스크립트에서 하이퍼 매개 변수 조정 프로세스에 사용할 수 있도록 이 메트릭을 기록해야 합니다.

다음 샘플 코드 조각을 사용하여 학습 스크립트에서 이 메트릭을 기록합니다.

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

학습 스크립트는 `val_accuracy`을 계산하고 "정확도"를 기록합니다. 이것이 기본 메트릭으로 사용됩니다. 메트릭이 기록될 때마다 하이퍼 매개 변수 조정 서비스에서 수신됩니다. 이 메트릭을 보고하는 빈도를 결정하는 모델 개발자의 몫입니다.

<a name='specify-early-termination-policy'/>

## <a name="specify-early-termination-policy"></a>초기 종료 정책 지정

초기 종료 정책을 사용하여 자동으로 성능이 불량한 실행을 종료합니다. 이렇게 종료하여 리소스의 낭비를 줄이고 대신 다른 매개 변수 구성을 탐색하는 데 이러한 리소스를 사용합니다.

초기 종료 정책을 사용할 때 정책이 적용되는 경우를 제어하는 다음 매개 변수를 구성할 수 있습니다.

* `evaluation_interval`: 정책 적용에 대한 빈도입니다. 학습 스크립트에서 기본 메트릭을 기록할 때마다 한 번의 간격으로 계산됩니다. 따라서 1의 `evaluation_interval`은 학습 스크립트에서 기본 메트릭을 보고할 때마다 정책을 적용합니다. 2의 `evaluation_interval`은 학습 스크립트에서 기본 메트릭을 보고할 때마다 하나 걸러 한 번씩 정책을 적용합니다. 지정하지 않으면 `evaluation_interval`은 기본적으로 1로 설정됩니다.
* `delay_evaluation`: 지정된 간격 동안 첫 번째 정책 평가를 지연합니다. 학습 실행의 이른 종료를 방지하도록 간격의 초기 최소 수에 대해 모든 구성이 실행되도록 허용하는 선택적 매개 변수입니다. 지정된 경우 정책은 delay_evaluation보다 크거나 같은 evaluation_interval의 모든 배수마다 적용됩니다.

Azure Machine Learning 서비스는 다음 초기 종료 정책을 지원합니다.

### <a name="bandit-policy"></a>산적 정책

산적은 slack 요소/slack 양 및 평가 간격을 기반으로 하는 종료 정책입니다. 이 정책은 기본 메트릭이 성능이 훌륭한 학습 실행에 대해 지정된 slack 요소/slack 양 내에 있지 않은 모든 실행을 초기 종료합니다. 다음 구성 매개 변수를 사용합니다.

* `slack_factor` 또는 `slack_amount`: 성능이 훌륭한 학습 실행에 대해 허용된 slack입니다. `slack_factor`는 비율로 허용되는 slack을 지정합니다. `slack_amount`는 비율 대신에 절대 양으로 허용되는 slack을 지정합니다.

    예를 들어 간격 10에 적용되는 산적 정책을 사용하는 것이 좋습니다. 간격 10에서 성능이 훌륭한 실행이 기본 메트릭을 최대화하는 목표로 기본 메트릭 0.8을 보고했다고 가정합니다. 정책이 0.2의 `slack_factor`로 지정된 경우 간격 10에서 최상의 메트릭이 0.66(0.8/(1+`slack_factor`))보다 작은 모든 학습 실행이 종료됩니다. 대신 정책이 0.2의 `slack_amount`로 지정된 경우 간격 10에서 최상의 메트릭이 0.6(0.8 - `slack_amount`)보다 작은 모든 학습 실행이 종료됩니다.
* `evaluation_interval`: 정책 적용에 대한 빈도입니다(선택적 매개 변수).
* `delay_evaluation`: 지정된 간격 동안 첫 번째 정책 평가를 지연합니다(선택적 매개 변수).


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

이 예제에서는 평가 간격 5에서 시작하여 메트릭이 보고될 때 모든 간격에서 초기 종료 정책이 적용됩니다. 최상의 메트릭이 성능이 훌륭한 실행의 (1/(1+0.1) 또는 91%보다 작은 모든 실행이 종료됩니다.

### <a name="median-stopping-policy"></a>중앙값 중지 정책

중앙값 중지는 해당 실행에서 보고되는 기본 메트릭의 실행 평균을 기반으로 하는 초기 종료 정책입니다. 이 정책은 모든 학습 실행에서 실행 평균을 계산하고 성능이 실행 평균의 중앙값보다 좋지 않은 실행을 종료합니다. 이 정책은 다음 구성 매개 변수를 사용합니다.
* `evaluation_interval`: 정책 적용에 대한 빈도입니다(선택적 매개 변수).
* `delay_evaluation`: 지정된 간격 동안 첫 번째 정책 평가를 지연합니다(선택적 매개 변수).


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

이 예제에서는 평가 간격 5에서 시작하여 모든 간격에서 초기 종료 정책이 적용됩니다. 실행의 최적 기본 메트릭이 모든 학습 실행의 간격 1:5에서 실행 평균의 중앙값보다 좋지 않은 경우 실행은 간격 5에서 종료됩니다.

### <a name="truncation-selection-policy"></a>잘림 선택 영역 정책

잘림 선택 영역은 각 평가 간격에서 성능이 가장 낮은 실행의 지정된 백분율을 취소합니다. 실행은 기본 메트릭의 해당 성능에 따라 비교되고 가장 낮은 X%가 종료됩니다. 다음 구성 매개 변수를 사용합니다.

* `truncation_percentage`: 각 평가 간격에서 종료할 성능이 가장 낮은 실행의 백분율입니다. 1에서 99 사이의 정수 값을 지정합니다.
* `evaluation_interval`: 정책 적용에 대한 빈도입니다(선택적 매개 변수).
* `delay_evaluation`: 지정된 간격 동안 첫 번째 정책 평가를 지연합니다(선택적 매개 변수).


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

이 예제에서는 평가 간격 5에서 시작하여 모든 간격에서 초기 종료 정책이 적용됩니다. 간격 5에서 해당 성능이 간격 5의 모든 실행에서 가장 낮은 20%의 성능에 있는 경우 실행은 간격 5에서 종료됩니다.

### <a name="no-termination-policy"></a>종료 정책 없음

모든 학습 실행을 완료될 때까지 실행하려는 경우 정책을 None으로 설정합니다. 초기 종료 정책이 적용되지 않습니다.

```Python
policy=None
```

### <a name="default-policy"></a>기본 정책

정책을 지정하지 않은 하이퍼 매개 변수 조정 서비스는 모든 학습이 완료될 때까지 실행되도록 합니다.

>[!NOTE] 
>가능성이 높은 작업을 종료하지 않고 비용 절감을 제공하는 보수적인 정책을 원하는 경우 `evaluation_interval` 1 및 `delay_evaluation` 5의 중앙값 중지 정책을 사용할 수 있습니다. 이는 일반적인 설정이며, 기본 메트릭에서 손실 없이 약 25%-35% 절감을 제공할 수 있습니다(계산 데이터에 따라).

## <a name="allocate-resources"></a>리소스 할당

총 교육 실행의 최대 수를 지정하여 하이퍼 매개 변수 조정 실험의 리소스 예산을 제어합니다.  선택적으로 하이퍼 매개 변수 조정 실험의 최대 기간을 지정합니다.

* `max_total_runs`: 생성될 학습 실행 최대 총 수입니다. 상한 - 예를 들어, 하이퍼 매개 변수 공간이 한정되어 있고 샘플이 더 적은 경우더 적은 실행이 있을 수 있습니다. 값은 1에서 1000 사이의 숫자여야 합니다.
* `max_duration_minutes`: 하이퍼 매개 변수 튜닝 실험의 최대 지속 시간(분)입니다. 매개 변수는 선택적이며, 있는 경우 이 기간 후 실행될 수 있는 모든 실행은 자동으로 취소됩니다.

>[!NOTE] 
>`max_total_runs` 및 `max_duration_minutes`가 모두 지정된 경우 이러한 두 임계값 중 첫 번째 임계값에 도달하면 하이퍼 매개 변수 조정 실험은 종료됩니다.

또한 하이퍼 매개 변수 조정 검색 중 동시에 실행할 학습 실행의 최대 수를 지정합니다.

* `max_concurrent_runs`: 지정된 순간에 동시에 실행될 실행의 최대 수입니다. 지정되지 않는 경우 모든 `max_total_runs`는 병렬로 시작됩니다. 지정된 경우 1과 100 사이의 숫자여야 합니다.

>[!NOTE] 
>동시 실행 수는 지정된 계산 대상에서 사용할 수 있는 리소스에서 제어됩니다. 따라서 계산 대상이 원하는 동시성에 대한 사용 가능한 리소스를 갖도록 해야 합니다.

하이퍼 매개 변수 조정에 대한 리소스 할당:

```Python
max_total_runs=20,
max_concurrent_runs=4
```

이 코드는 한 번에 4개의 구성을 실행하여 최대 20개의 실행을 사용하도록 하이퍼 매개 변수 조정 실험을 구성합니다.

## <a name="configure-experiment"></a>실험 구성

위의 섹션에서 정의된 하이퍼 매개 변수 검색 공간, 초기 종료 정책, 기본 메트릭 및 리소스 할당을 사용하여 하이퍼 매개 변수 조정 실험을 구성합니다. 또한 샘플링된 하이퍼 매개 변수를 사용하여 호출될 `estimator`를 제공합니다. `estimator`는 실행하는 학습 스크립트, 작업당 리소스(단일 또는 다중 gpu) 및 사용할 계산 대상을 설명합니다. 하이퍼 매개 변수 조정 실험에 대한 동시성이 사용 가능한 리소스에서 제어되므로 `estimator`에서 지정된 계산 대상에 원하는 동시성에 대한 충분한 리소스가 있어야 합니다. 추정에 대한 자세한 내용은 [모델을 학습하는 방법](how-to-train-ml-models.md)을 참조하세요.

하이퍼 매개 변수 조정 실험 구성

```Python
from azureml.train.hyperdrive import HyperDriveRunConfig
hyperdrive_run_config = HyperDriveRunConfig(estimator=estimator,
                          hyperparameter_sampling=param_sampling, 
                          policy=early_termination_policy,
                          primary_metric_name="accuracy", 
                          primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                          max_total_runs=100,
                          max_concurrent_runs=4)
```

## <a name="submit-experiment"></a>실험 제출

하이퍼 매개 변수 조정 구성을 정의하면 다음과 같이 실험을 제출합니다.

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hyperdrive_run_config)
```

`experiment_name`은 하이퍼 매개 변수 조정 실험에 할당하려는 이름이고, `workspace`는 실험을 만들려는 작업 영역입니다(실험에 대한 자세한 내용은 [Azure Machine Learning 서비스 작동 방법](concept-azure-machine-learning-architecture.md) 참조).

## <a name="visualize-experiment"></a>실험 시각화

Azure Machine Learning SDK는 학습 실행의 진행률을 시각화하는 Notebook 위젯을 제공합니다. 다음 코드 조각은 Jupyter Notebook에서 모든 하이퍼 매개 변수 조정 실행을 시각화합니다.

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

이 코드는 각 하이퍼 매개 변수 구성의 학습 실행에 대한 세부 정보가 있는 표를 표시합니다.

![하이퍼 매개 변수 튜닝 테이블](media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

학습 진행률에 따라 각 실행의 성능을 시각화할 수도 있습니다. 

![하이퍼 매개 변수 튜닝 플롯](media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

또한 병렬 좌표 플롯을 사용하여 개별 하이퍼 매개 변수의 성능과 값 간의 상관 관계를 시각적으로 식별할 수 있습니다. 

![하이퍼 매개 변수 튜닝 병렬 좌표](media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)

Azure 웹 포털에서도 모든 하이퍼 매개 변수 조정 실행을 시각화할 수 있습니다. 웹 포털에서 실험을 보는 방법에 대한 자세한 내용은 [실험을 추적하는 방법](how-to-track-experiments.md#view-the-experiment-in-the-web-portal)을 참조하세요.

![하이퍼 매개 변수 튜닝 포털](media/how-to-tune-hyperparameters/HyperparameterTuningPortal.png)

## <a name="find-the-best-model"></a>최적 모델 찾기

모든 하이퍼 매개 변수 조정 실행이 완료되면 성능이 훌륭한 구성 및 해당 하이퍼 매개 변수 값을 식별할 수 있습니다.

```Python
best_run = hyperdrive_run.get_best_run_by_primary_metric()
best_run_metrics = best_run.get_metrics()
parameter_values = best_run.get_details()['runDefinition']['Arguments']

print('Best Run Id: ', best_run.id)
print('\n Accuracy:', best_run_metrics['accuracy'])
print('\n learning rate:',parameter_values[3])
print('\n keep probability:',parameter_values[5])
print('\n batch size:',parameter_values[7])
```

## <a name="sample-notebook"></a>샘플 노트북
이러한 Notebook을 참조하세요.
* [how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch) 
* [how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>다음 단계
* [실험 추적](how-to-track-experiments.md)
* [학습된 모델 배포](how-to-deploy-and-where.md)
