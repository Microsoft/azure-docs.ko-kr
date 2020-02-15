---
title: '의사 결정 포리스트 회귀: 모듈 참조'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 2 클래스 평균 퍼셉트론 모듈을 사용 하 여 평균 퍼셉트론 알고리즘을 기반으로 기계 학습 모델을 만드는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 352881d2fb4ddd8ce438f1bca713513b65f50f40
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153760"
---
# <a name="two-class-averaged-perceptron-module"></a>2 클래스 평균 퍼셉트론 모듈

이 문서에서는 Azure Machine Learning designer (미리 보기)의 모듈을 설명 합니다.

이 모듈을 사용 하 여 평균 퍼셉트론 알고리즘을 기반으로 기계 학습 모델을 만듭니다.  
  
이 분류 알고리즘은 감독 된 학습 방법 이며 레이블 열을 포함 하는 *태그가 지정 된 데이터 집합이*필요 합니다. 모델 [학습](./train-model.md)을 위한 입력으로 모델 및 태그가 지정 된 데이터 집합을 제공 하 여 모델을 학습할 수 있습니다. 그러면 학습 된 모델을 사용 하 여 새 입력 예제에 대 한 값을 예측할 수 있습니다.  

### <a name="about-averaged-perceptron-models"></a>평균 퍼셉트론 모델 정보

*평균 퍼셉트론 방법은* 신경망의 초기 및 단순 버전입니다. 이 방법에서 입력은 선형 함수를 기반으로 하는 여러 개의 가능한 출력으로 분류 된 후 기능 벡터에서 파생 된 가중치 집합과 결합 됩니다. 따라서 "퍼셉트론" 라는 이름이 사용 됩니다.

보다 단순한 퍼셉트론 모델은 선형으로 구분 가능한 패턴을 학습하는 데 적합한 반면 신경망, 특히 심층 신경망은 더 복잡한 클래스 경계를 모델링할 수 있습니다. 그러나 퍼셉트론은 속도가 더 빠르며 사례를 직렬로 처리하므로 연속 학습에서 사용할 수 있습니다.

## <a name="how-to-configure-two-class-averaged-perceptron"></a>2 클래스 평균 퍼셉트론를 구성 하는 방법

1.  **2 클래스 평균 퍼셉트론** 모듈을 파이프라인에 추가 합니다.  

2.  **강사 모드 만들기** 옵션을 설정 하 여 모델을 학습 하는 방법을 지정 합니다.  
  
    -   **단일 매개 변수**: 모델을 구성 하는 방법을 아는 경우 특정 값 집합을 인수로 제공 합니다.
  
3.  **학습 률**에 대해 *학습 률*값을 지정 합니다. 학습 률 값은 모델을 테스트 하 고 수정할 때마다 추계 그라데이션 하강에 사용 되는 단계의 크기를 제어 합니다.
  
     이 속도로 더 작게 설정 하면 로컬 정체 되기에서 중단 될 수 있는 위험이 있으므로 모델을 더 자주 테스트할 수 있습니다. 단계를 크게 만들면 더 빨리 수렴할 수 있지만 진짜 최소값을 초과할 위험이 있습니다.
  
4.  **최대 반복 횟수**에 대해 알고리즘에서 학습 데이터를 검사 하는 횟수를 입력 합니다.  
  
     일찍 중지하면 일반화가 향상되는 경우가 많습니다. 반복 횟수를 늘리면 맞춤이 향상되지만 과잉 맞춤될 위험이 있습니다.
  
5.  **난수 초기값**의 경우 초기값으로 사용할 정수 값을 입력 합니다. 실행 간에 파이프라인이 재현 가능성 보장 하려면 초기값을 사용 하는 것이 좋습니다.  
  
1.  학습 데이터 집합 및 학습 모듈 중 하나를 연결 합니다.
  
    -   담당자 **모드 만들기** 를 **단일 매개 변수로**설정한 경우 [모델 학습](train-model.md) 모듈을 사용 합니다.




## <a name="next-steps"></a>다음 단계

Azure Machine Learning [사용할 수 있는 모듈 집합](module-reference.md) 을 참조 하세요. 