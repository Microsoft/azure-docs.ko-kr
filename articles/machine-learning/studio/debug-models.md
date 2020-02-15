---
title: 모듈 디버그
titleSuffix: ML Studio (classic) - Azure
description: Azure Machine Learning Studio (클래식)에서 모델 학습 및 모델 점수 매기기 모듈을 통해 생성 되는 오류를 디버깅 하는 방법입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/14/2017
ms.openlocfilehash: 6c672e57c0c1c0b62b79afbacec3f842770e3d61
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169241"
---
# <a name="debug-your-model-in-azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio에서 모델 디버그 (클래식)

모델을 실행할 때 다음 오류가 발생할 수 있습니다.

* [모델 학습][train-model] 모듈에서 오류가 발생 합니다. 
* [모델 점수 매기기][score-model] 모듈에서 잘못 된 결과 생성 

이 문서에서는 이러한 오류에 대한 잠재적 원인을 설명합니다.


## <a name="train-model-module-produces-an-error"></a>모델 학습 모듈에서 오류 발생

![image1](./media/debug-models/train_model-1.png)

[모델 학습][train-model] 모듈에는 두 가지 입력이 필요 합니다.

1. Azure Machine Learning Studio (클래식)에서 제공 하는 모델 컬렉션의 기계 학습 모델 유형입니다.
2. 예측할 변수를 지정하는 지정된 레이블 열이 있는 학습 데이터(다른 열은 기능으로 간주됨)

다음과 같은 경우 이 모듈에서 오류가 발생할 수 있습니다.

1. 레이블 열이 잘못 지정된 경우. 이는 둘 이상의 열을 레이블로 선택하거나 잘못된 열 인덱스를 선택한 경우에 발생할 수 있습니다. 예를 들어 두 번째 경우는 열이 25개뿐인 입력 데이터 세트에서 열 인덱스 30을 사용한 경우에 적용됩니다.

2. 데이터 세트에 기능 열이 없는 경우. 예를 들어 입력 데이터 세트에 열이 1개뿐이고 이 열이 레이블 열로 표시된 경우 모델을 빌드할 기능이 없습니다. 이 경우 [모델 학습][train-model] 모듈에서 오류를 생성 합니다.

3. 입력 데이터 세트(기능 또는 레이블)에 Infinity가 값으로 포함된 경우

## <a name="score-model-module-produces-incorrect-results"></a>모델 점수 매기기 모듈에서 잘못된 결과 생성

![image2](./media/debug-models/train_test-2.png)

감독 학습을 위한 일반적인 학습/테스트 실험에서 [데이터 분할][split] 모듈은 원래 데이터 집합을 두 부분으로 나눕니다. 한 부분은 모델을 학습 하는 데 사용 되 고 한 부분은 학습 된 모델이 얼마나 잘 수행 되는지 점수를 매기는 데 사용 됩니다. 그런 다음 학습된 모델은 테스트 데이터의 점수를 매기는 데 사용되며, 그 결과가 모델의 정확도를 확인하기 위해 평가됩니다.

[모델 점수 매기기][score-model] 모듈에는 두 가지 입력이 필요 합니다.

1. [모델 학습][train-model] 모듈의 학습 된 모델 출력입니다.
2. 모델 학습에 사용되는 데이터 세트와는 다른 점수 매기기 데이터 세트

실험에 성공한 경우에도 [모델 점수 매기기][score-model] 모듈에서 잘못 된 결과가 생성 될 수 있습니다. 다음과 같은 여러 시나리오에서 이러한 문제가 발생할 수 있습니다.

1. 지정 된 레이블이 범주이 고 데이터에서 회귀 모델을 학습 하는 경우 [모델 점수 매기기][score-model] 모듈에서 잘못 된 출력이 생성 됩니다. 회귀에는 연속 응답 변수가 필요하기 때문입니다. 이 경우 분류 모델을 사용하는 것이 보다 적합합니다. 

2. 마찬가지로 레이블 열에 부동 소수점 숫자가 있는 데이터 세트에 대해 분류 모델이 학습된 경우 바람직하지 않은 결과가 생성될 수 있습니다. 분류에는 범위가 유한하고 클래스 집합이 작은 값만 허용하는 불연속 응답 변수가 필요하기 때문입니다.

3. 점수 매기기 데이터 집합에 모델을 학습 하는 데 사용 되는 모든 기능이 포함 되어 있지 않으면 [점수 매기기 모델][score-model] 에서 오류를 생성 합니다.

4. 점수 매기기 데이터 집합의 행에 해당 기능에 대해 누락 된 값 이나 무한 값이 포함 된 경우 [모델 점수][score-model] 매기기에서 해당 행에 해당 하는 출력을 생성 하지 않습니다.

5. [점수 매기기 모델][score-model] 은 점수 매기기 데이터 집합의 모든 행에 대해 동일한 출력을 생성할 수 있습니다. 예를 들어 선택한 리프 노드당 최소 샘플 수가 사용 가능한 학습 예제 수보다 많은 경우 의사 결정 포리스트를 사용하여 분류할 때 이러한 상황이 발생할 수 있습니다.

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

