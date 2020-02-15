---
title: '다중 클래스 로지스틱 회귀: 모듈 참조'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 다중 클래스 로지스틱 회귀 모듈을 사용 하 여 여러 값을 예측 하는 데 사용할 수 있는 로지스틱 회귀 모델을 만드는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: a697c1e43ccd486f8c98399ea9065902bd247f7d
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152145"
---
# <a name="multiclass-logistic-regression-module"></a>다중 클래스 로지스틱 회귀 모듈

이 문서에서는 Azure Machine Learning designer (미리 보기)의 모듈을 설명 합니다.

이 모듈을 사용 하 여 여러 값을 예측 하는 데 사용할 수 있는 로지스틱 회귀 모델을 만듭니다.

로지스틱 회귀를 사용 하는 분류는 감독 학습 방법 이므로 레이블이 지정 된 데이터 집합이 필요 합니다. 모델 [학습](./train-model.md)을 통해 모델과 레이블이 지정 된 데이터 집합을 모듈에 대 한 입력으로 제공 하 여 모델을 학습 합니다. 그러면 학습 된 모델을 사용 하 여 새 입력 예제에 대 한 값을 예측할 수 있습니다.

또한 Azure Machine Learning는 이진 또는이 분 변수의 분류에 적합 한 [2 클래스 로지스틱 회귀](./two-class-logistic-regression.md) 모듈을 제공 합니다.

## <a name="about-multiclass-logistic-regression"></a>다중 클래스 로지스틱 회귀 정보

로지스틱 회귀는 결과의 확률을 예측 하는 데 사용 되는 통계의 잘 알려진 방법 이며 분류 작업에 많이 사용 됩니다. 알고리즘은 데이터를 로지스틱 함수에 맞추는 방법으로 이벤트 발생 확률을 예측 합니다. 

다중 클래스 로지스틱 회귀에서는 분류자를 사용 하 여 여러 결과를 예측할 수 있습니다.

## <a name="configure-a-multiclass-logistic-regression"></a>다중 클래스 로지스틱 회귀 구성

1. **다중 클래스 로지스틱 회귀** 모듈을 파이프라인에 추가 합니다.

2. **강사 모드 만들기** 옵션을 설정 하 여 모델을 학습 하는 방법을 지정 합니다.

    + **단일 매개 변수**: 모델을 구성 하는 방법을 알고 있으며 특정 값 집합을 인수로 제공 하는 경우이 옵션을 사용 합니다.

    + **매개 변수 범위**: 최적의 매개 변수를 잘 모르는 상태에서 매개 변수 스윕을 사용 하려는 경우이 옵션을 사용 합니다.

3. **최적화 허용 오차**에 대 한 임계값을 지정 합니다. 반복 간의 개선 수준이 임계값보다 낮으면 알고리즘이 중지되며 현재 모델이 반환됩니다.

4. **L1 정규화 weight**, **l2 정규화 가중치**: 정규화 매개 변수 L1 및 L2에 사용할 값을 입력 합니다. 둘 다에 0이 아닌 값을 사용하는 것이 좋습니다.

    정규화는 극단적인 계수 값을 사용 하 여 과잉 맞춤 by 페널티 모델을 방지 하는 방법입니다. 정규화는 계수 값과 연결 된 페널티를 가설의 오류에 추가 하 여 작동 합니다. 극단적인 계수 값을 포함 하는 정확한 모델은 더 페널티가 수 있지만 보다 보수적인 값을 가진 보다 정확한 모델은 페널티가 줄어듭니다.

     L1 및 L2 정규화가 미치는 영향과 사용 방법은 여러 가지가 있습니다. L1은 스파스 모델에 적용할 수 있으며 이는 고차원 데이터로 작업할 때 유용합니다. 한편, L2 정규화는 스파스가 아닌 데이터에 사용하는 것이 좋습니다.  이 알고리즘은 L1 및 L2 정규화 값의 선형 조합을 지원 합니다. 즉, `x = L1` 및 `y = L2``ax + by = c`는 정규화 용어의 선형 범위를 정의 합니다.

     [탄력적 네트워크 정규화](https://wikipedia.org/wiki/Elastic_net_regularization)와 같은 로지스틱 회귀 모델에 대해 L1 및 L2 용어의 여러 선형 조합이 고안 되었습니다.

6. **난수 초기값**: 결과를 반복 해 서 실행 하려면 알고리즘의 초기값으로 사용할 정수 값을 입력 합니다. 그렇지 않으면 시스템 클록 값이 초기값으로 사용 되므로 동일한 파이프라인을 실행할 때 약간 다른 결과를 생성할 수 있습니다.

8. 레이블이 지정 된 데이터 집합 및 학습 모듈 중 하나를 연결 합니다.

    + 담당자 **모드 만들기** 를 **단일 매개 변수로**설정한 경우 [모델 학습](./train-model.md) 모듈을 사용 합니다.

9. 파이프라인을 실행합니다.



## <a name="next-steps"></a>다음 단계

Azure Machine Learning [사용할 수 있는 모듈 집합](module-reference.md) 을 참조 하세요. 