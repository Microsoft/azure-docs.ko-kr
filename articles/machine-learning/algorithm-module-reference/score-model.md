---
title: '모델 점수 매기기: 모듈 참조'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning의 모델 점수 매기기 모듈을 사용 하 여 학습 된 분류 또는 회귀 모델을 사용 하 여 예측을 생성 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: c06d1268abe8afdeb03668131c3c61cfbafa44cd
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77138167"
---
# <a name="score-model-module"></a>모델 점수 매기기 모듈

이 문서에서는 Azure Machine Learning designer (미리 보기)의 모듈을 설명 합니다.

이 모듈을 사용 하 여 학습 된 분류 또는 회귀 모델을 사용 하 여 예측을 생성 합니다.

## <a name="how-to-use"></a>사용 방법

1. **모델 점수 매기기** 모듈을 파이프라인에 추가 합니다.

2. 학습 된 모델 및 새 입력 데이터를 포함 하는 데이터 집합을 연결 합니다. 

    데이터는 사용 중인 학습 된 모델의 형식과 호환 되는 형식 이어야 합니다. 입력 데이터 집합의 스키마도 일반적으로 모델을 학습 하는 데 사용 되는 데이터의 스키마와 일치 해야 합니다.

3. 파이프라인을 실행합니다.

## <a name="results"></a>결과

[모델 점수 매기기](./score-model.md)를 사용 하 여 점수 집합을 생성 한 후:

+ 모델 정확도 (성능)를 평가 하는 데 사용 되는 메트릭 집합을 생성 합니다.  점수가 매겨진 데이터 집합을 연결 하 여 [모델을 평가할](./evaluate-model.md)수 있습니다. 
+ 모듈을 마우스 오른쪽 단추로 클릭 하 고 **시각화** 를 선택 하 여 결과 샘플을 표시 합니다.
+ 결과를 데이터 집합에 저장 합니다.

점수 또는 예측 값은 모델 및 입력 데이터에 따라 다양 한 형식으로 될 수 있습니다.

- 분류 모델의 경우 [점수 매기기 모델](./score-model.md) 은 클래스의 예측 값 뿐만 아니라 예측 값의 확률을 출력 합니다.
- 회귀 모델의 경우 [모델 점수 매기기](./score-model.md) 는 예측 된 숫자 값만 생성 합니다.


## <a name="publish-scores-as-a-web-service"></a>점수를 웹 서비스로 게시

점수 매기기의 일반적인 용도는 예측 웹 서비스의 일부로 출력을 반환 하는 것입니다. 자세한 내용은 Azure Machine Learning 파이프라인을 기반으로 웹 서비스를 만드는 방법에 대 한 자습서를 참조 하세요.

## <a name="next-steps"></a>다음 단계

Azure Machine Learning [사용할 수 있는 모듈 집합](module-reference.md) 을 참조 하세요. 