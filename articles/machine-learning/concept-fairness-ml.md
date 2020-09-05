---
title: 기계 학습 모델 (미리 보기)의 공평 완화
titleSuffix: Azure Machine Learning
description: 기계 학습 모델의 공정성에 대한 내용과 Fairlearn Python 패키지로 보다 공정한 모델을 빌드하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 08/05/2020
ms.openlocfilehash: 3f051d9fc1599c0877e1e8a58935d09d224ce22b
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689680"
---
# <a name="mitigate-fairness-in-machine-learning-models-preview"></a>기계 학습 모델 (미리 보기)의 공평 완화

기계 학습에서 공평 하 고 [Fairlearn](https://fairlearn.github.io/) 오픈 소스 Python 패키지가 기계 학습 모델의 공평 한 문제를 완화 하는 데 어떻게 도움이 될 수 있는지 알아보세요. 향상 된 문제를 이해 하 고 기계 학습 모델을 빌드할 때 공평 하 게 평가 하는 데 도움이 되지 않는 경우 불공정 거래법 결과를 생성 하는 모델을 작성할 수 있습니다.

Fairlearn 오픈 소스 패키지에 대 한 [사용자 가이드](https://fairlearn.github.io/user_guide/index.html) 의 다음 요약에서는이를 사용 하 여 빌드하는 AI 시스템의 공평을 평가 하는 방법을 설명 합니다.  Fairlearn 오픈 소스 패키지는 관찰 되는 모든 문제를 완화 하는 데 도움이 되는 옵션을 제공할 수도 있습니다.  Azure Machine Learning 학습 중 AI 시스템의 공평 한 평가를 사용 하려면 [방법](how-to-machine-learning-fairness-aml.md) 및 [샘플 노트북](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness) 을 참조 하세요.


## <a name="what-is-fairness-in-machine-learning-models"></a>기계 학습 모델에서 공평 한가요?

>[!NOTE]
> 공정성은 사회 기술적 과제입니다. 공평, 적법 절차 등 공정성의 여러 측면은 정량적 공정성 메트릭으로 캡처할 수 없습니다. 또한 여러 정성적 공정성 메트릭을 모두 동시에 만족할 수는 없습니다. Fairlearn 오픈 소스 패키지의 목표는 다른 영향 및 완화 전략을 평가할 수 있도록 하는 것입니다. 궁극적으로는 인공 지능 및 기계 학습 모델을 작성 하는 인적 사용자가 자신의 시나리오에 적합 한 장단점을 만듭니다.

AI와 기계 학습 시스템이 불공정한 동작을 보일 수 있습니다. 불공정한 동작을 정의하는 한 가지 방법은 사람들에게 미치는 피해 또는 영향입니다. AI 시스템은 여러 가지 유형의 피해를 입힐 수 있습니다. 자세히 알아보려면 [Neurips 2017 키 노트 By Kate Crawford](https://www.youtube.com/watch?v=fMym_BKWQzk) 를 참조 하세요.

다음 두 가지는 AI로 인한 대표적인 피해 유형입니다.

- 할당의 손상: AI 시스템은 특정 그룹에 대 한 기회, 리소스 또는 정보를 포함 합니다. 예를 들어 채용, 입학 및 대출 심사에서 모델이 다른 그룹보다 특정 그룹에서 유력 후보를 선택하는 경향을 보일 수 있습니다.

- 서비스 품질 피해: AI 시스템이 특정 그룹에 대해서는 다른 그룹에 하는 것처럼 작동하지 않습니다. 예를 들어 음성 인식 시스템이 여성에게는 남성에게 하듯이 작동하지 않을 수 있습니다.

AI 시스템의 불공정한 동작을 줄이려면 이러한 피해를 평가하고 완화해야 합니다.

## <a name="fairness-assessment-and-mitigation-with-fairlearn"></a>Fairlearn을 사용한 공정성 평가 및 완화

Fairlearn은 기계 학습 시스템 개발자가 시스템의 공정성을 평가하고 관찰된 공정성 문제를 완화할 수 있는 오픈 소스 Python 패키지입니다.

Fairlearn 오픈 소스 패키지에는 두 가지 구성 요소가 있습니다.

- 평가 대시보드: 모델의 예측이 다른 그룹에 어떤 영향을 주는지 평가하기 위한 Jupyter Notebook 위젯입니다. 또한 공정성 및 성능 메트릭을 사용하여 여러 모델을 비교할 수 있습니다.
- 완화 알고리즘: 이진 분류 및 회귀의 불공정을 완화하는 알고리즘 세트입니다.

두 구성 요소가 함께 작동하여 데이터 과학자 및 비즈니스 리더가 공정성과 성능 사이에서 적절히 타협하고, 요구 사항에 가장 적합한 완화 전략을 선택할 수 있도록 도와줍니다.

## <a name="assess-fairness-in-machine-learning-models"></a>기계 학습 모델에서 공평 하 게 평가

Fairlearn 오픈 소스 패키지에서 공평은 개념화가 위험에 노출 될 수 있는 개인 그룹을 요청 하는 **그룹**수준으로 알려진 방법입니다. 아집단이라고도 하는 관련 그룹은 **중요한 기능** 또는 중요한 특성을 통해 정의됩니다. 중요 한 기능은 Fairlearn의 벡터 또는 행렬로 평가기에 전달 됩니다  `sensitive_features` . 이 용어는 시스템 디자이너가 그룹 공정성을 평가할 때 이러한 기능을 중요하게 생각해야 한다는 의미입니다. 

이러한 기능에는 개인 데이터 때문에 개인 정보 취급 방침이 포함 되어 있는지 여부를 염두에 두는 것이 좋습니다. 하지만 "중요한"이라는 단어는 이러한 기능을 예측에 사용하면 안 된다는 의미가 아닙니다.

>[!NOTE]
> 공평 하 게 평가 하는 것은 전적으로 기술적인 연습이 아닙니다.  Fairlearn 오픈 소스 패키지는 모델의 공평을 평가 하는 데 도움이 될 수 있지만 평가를 수행 하지는 않습니다.  Fairlearn 오픈 소스 패키지는 양적 메트릭을 식별 하는 데 도움이 되는 양적 메트릭을 식별 하는 데 도움이 되지만 개발자는 질적 분석을 수행 하 여 자체 모델의 공평도를 평가 해야 합니다.  위에서 언급 한 중요 한 기능은 이러한 종류의 질적 분석의 예입니다.     

평가 단계에서 공정성은 차이 메트릭을 통해 정량화됩니다. **차이 메트릭**은 여러 그룹에서 모델의 동작을 평가하고 비율 또는 차이로 비교할 수 있습니다. Fairlearn 오픈 소스 패키지는 두 가지 차이가 메트릭 클래스를 지원 합니다.


- 모델 성능의 차이: 이 메트릭 세트는 여러 하위 그룹에서 선택한 성능 메트릭 값의 차이를 계산합니다. 일부 사례:

  - 정확도 차이
  - 오류율 차이
  - 정확도 차이
  - 재현율 차이
  - MAE 차이
  - 기타

- 선택 비율의 차이: 이 메트릭은 여러 하위 그룹 간의 선택 비율 차이를 포함합니다. 대출 승인율의 차이를 예로 들 수 있습니다. 선택 비율은 각 클래스에서 1로 분류된 데이터 요소의 비율(이진 분류) 또는 예측 값의 분포(회귀)를 의미합니다.

## <a name="mitigate-unfairness-in-machine-learning-models"></a>기계 학습 모델의 공평 완화

### <a name="parity-constraints"></a>패리티 제약 조건

Fairlearn 오픈 소스 패키지에는 다양 한 비 공평 완화 알고리즘이 포함 되어 있습니다. 이러한 알고리즘은 예측 요인의 동작에 대한 **패리티 제약 조건** 또는 조건이라는 제약 조건 세트를 지원합니다. 패리티 제약 조건을 사용하려면 중요한 기능이 정의하는 그룹(예: 여러 인종)에서 예측 요인 동작의 일부 측면을 비교할 수 있어야 합니다. Fairlearn 오픈 소스 패키지의 완화 알고리즘은 이러한 패리티 제약 조건을 사용 하 여 관찰 된 공평 문제를 완화 합니다.

>[!NOTE]
> 모델에서의 공평 함을 완화 하는 것은 중단을 줄이는 것을 의미 하지만이 기술 완화를 사용 하면이 문제를 완전히 해결할 수 없습니다.  Fairlearn 오픈 소스 패키지의 비 공평 완화 알고리즘은 기계 학습 모델에서의 공평을 줄이기 위해 제안 된 완화 전략을 제공할 수 있지만,이는 완전 하지 않게 해결할 수 있는 솔루션은 아닙니다.  각 특정 개발자의 기계 학습 모델에 대해 고려해 야 하는 다른 패리티 제약 조건 또는 조건이 있을 수 있습니다. Azure Machine Learning를 사용 하는 개발자는 시스템 학습 모델을 사용 하 고 배포 하는 데 충분 한 문제를 해결 해야 하는 경우 자신을 결정 해야 합니다.  

Fairlearn 오픈 소스 패키지는 다음과 같은 유형의 패리티 제약 조건을 지원 합니다. 

|패리티 제약 조건  | 목적  |기계 학습 작업  |
|---------|---------|---------|
|인구 통계 패리티     |  할당 피해 완화 | 이진 분류, 회귀 |
|균등 배당률  | 할당 및 서비스 품질 피해 진단 | 이진 분류        |
|같음 기회 | 할당 및 서비스 품질 피해 진단 | 이진 분류        |
|제한된 그룹 손실     |  서비스 품질 피해 완화 | 회귀 |



### <a name="mitigation-algorithms"></a>완화 알고리즘

Fairlearn 오픈 소스 패키지는 후 처리 및 감소 하는 완화 알고리즘을 제공 합니다.

- 감소: 이러한 알고리즘은 표준 블랙 박스 machine learning 평가기 (예: LightGBM 모델)를 사용 하 고 다시 가중치가 매겨진 학습 데이터 집합의 시퀀스를 사용 하 여 다시 학습 모델 집합을 생성 합니다. 예를 들어 특정 성별의 신청자에게 가중치를 더하거나 빼서 모델을 다시 학습시켜 성별 그룹 간의 차이를 줄일 수 있습니다. 그러면 사용자는 정확도(또는 다른 성능 메트릭)와 차이 간에 적절하게 절충하는 모델을 선택할 수 있으며, 일반적으로 절충은 비즈니스 규칙과 비용 계산을 기반으로 해야 합니다.  
- 후처리: 이 알고리즘은 기존 분류자와 중요한 기능을 입력으로 사용합니다. 그런 다음, 분류자의 예측을 변환하여 지정된 공정성 제약 조건을 적용합니다. 임계값 최적화의 가장 큰 장점은 모델을 다시 학습시킬 필요가 없기 때문에 간단하고 유연하다는 점입니다. 

| 알고리즘 | Description | 기계 학습 작업 | 중요한 기능 | 지원되는 패리티 제약 조건 | 알고리즘 유형 |
| --- | --- | --- | --- | --- | --- |
| `ExponentiatedGradient` | [공정 분류에 대한 축소 접근 방식](https://arxiv.org/abs/1803.02453)에 설명된 공정성 분류에 대한 블랙박스 접근 방식 | 이진 분류 | 범주 | [인구 통계 패리티](#parity-constraints), [균등 배당률](#parity-constraints) | 축소 |
| `GridSearch` | [공정 분류에 대한 축소 접근 방식](https://arxiv.org/abs/1803.02453)에 설명된 블랙박스 접근 방식| 이진 분류 | 이진 | [인구 통계 패리티](#parity-constraints), [균등 배당률](#parity-constraints) | 축소 |
| `GridSearch` | [공정 회귀: 정량적 정의 및 축소 기반 알고리즘](https://arxiv.org/abs/1905.12843)에 설명된 제한된 그룹 손실에 대한 알고리즘을 사용하여 공정 회귀의 그리드 검색 변형을 구현하는 블랙박스 접근 방식 | 회귀 | 이진 | [제한된 그룹 손실](#parity-constraints) | 축소 |
| `ThresholdOptimizer` | [감독 학습의 기회 균등](https://arxiv.org/abs/1610.02413) 논문을 기반으로 하는 후처리 알고리즘입니다. 이 기술은 기존 분류자와 중요한 기능을 입력으로 사용하고, 분류자의 예측을 모노톤으로 변환하여 지정된 패리티 제약 조건을 적용합니다. | 이진 분류 | 범주 | [인구 통계 패리티](#parity-constraints), [균등 배당률](#parity-constraints) | 후처리 |

## <a name="next-steps"></a>다음 단계

- Fairlearn의 [GitHub](https://github.com/fairlearn/fairlearn/), [사용자 가이드](https://fairlearn.github.io/user_guide/index.html), [예제](https://fairlearn.github.io/auto_examples/)및 [샘플 노트북](https://github.com/fairlearn/fairlearn/tree/master/notebooks)을 체크 아웃 하 여 다양 한 구성 요소를 사용 하는 방법을 알아봅니다.
- Azure Machine Learning에서 기계 학습 모델의 공평 한 평가를 사용 하도록 설정 하 [는 방법을](how-to-machine-learning-fairness-aml.md) 알아봅니다.
- Azure Machine Learning의 추가 평가 시나리오는 [샘플 노트북](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness) 을 참조 하세요. 
