---
title: '클러스터링 모델 학습: 모듈 참조'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 클러스터링 모델 학습 모듈을 사용 하 여 클러스터링 모델을 학습 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: 7053fab28b4a231c92f31e344cf09ffef3a6b146
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152111"
---
# <a name="train-clustering-model"></a>클러스터링 모델 학습

이 문서에서는 Azure Machine Learning designer (미리 보기)의 모듈을 설명 합니다.

이 모듈을 사용 하 여 클러스터링 모델을 학습 합니다.

이 모듈은 이미 [K-](k-means-clustering.md) 를 사용 하 여 클러스터링 모듈을 사용 하 여 구성한 학습 되지 않은 클러스터링 모델을 사용 하 고 레이블이 지정 되거나 레이블이 지정 되지 않은 데이터 집합을 사용 하 여 모델을 학습 합니다. 이 모듈은 예측에 사용할 수 있는 학습 된 모델 및 학습 데이터의 각 사례에 대 한 클러스터 할당 집합을 만듭니다.

> [!NOTE]
> 기계 학습 모델 학습을 위한 일반 모듈인 [모델 학습](train-model.md) 모듈을 사용 하 여 클러스터링 모델을 학습 시킬 수 없습니다. [모델 학습](train-model.md) 은 감독 된 학습 알고리즘 에서만 작동 하기 때문입니다. K-를 사용 하는 경우 및 다른 클러스터링 알고리즘은 자율 learning을 허용 합니다. 즉, 알고리즘은 레이블이 없는 데이터에서 학습할 수 있습니다.  
  
## <a name="how-to-use-train-clustering-model"></a>클러스터링 학습 모델을 사용 하는 방법  

1.  디자이너에서 파이프라인에 **클러스터링 모델 학습** 모듈을 추가 합니다. **학습** 범주에서 모듈 **Machine Learning**모듈 아래에 있는 모듈을 찾을 수 있습니다.  
  
2. [K 의미](k-means-clustering.md) 클러스터링 모듈 또는 호환 클러스터링 모델을 만들고 클러스터링 모델의 매개 변수를 설정 하는 다른 사용자 지정 모듈을 추가 합니다.  
    
3.  학습 **클러스터링 모델**의 오른쪽 입력에 학습 데이터 집합을 연결 합니다.
  
5.  **열 집합**에서 클러스터를 작성 하는 데 사용할 데이터 집합의 열을 선택 합니다. 올바른 기능을 제공 하는 열을 선택 해야 합니다. 예를 들어 Id 또는 고유한 값이 있는 다른 열을 사용 하거나 모든 값이 같은 열을 사용 하지 마십시오.

    레이블을 사용할 수 있는 경우 해당 레이블을 기능으로 사용 하거나 그대로 둘 수 있습니다.  
  
6. 새 클러스터 레이블과 함께 학습 데이터를 출력 하려면 옵션을 선택 하 고, **추가를 확인 하거나 결과에 대해서만 선택 취소**합니다.

    이 옵션의 선택을 취소 하면 클러스터 할당만 출력 됩니다. 

7. 파이프라인을 실행 하거나 **클러스터링 모델 학습** 모듈을 클릭 하 고 **선택 된 실행**을 선택 합니다.  
  
### <a name="results"></a>결과

학습을 완료 한 후:

+ 학습 된 모델의 스냅숏을 저장 하려면 **모델 학습** 모듈의 오른쪽 패널에서 **출력** 탭을 선택 합니다. **데이터 집합 등록** 아이콘을 선택 하 여 모델을 재사용 가능한 모듈로 저장 합니다.

+ 모델에서 점수를 생성 하려면 [클러스터에 데이터 할당](assign-data-to-clusters.md)을 사용 합니다.

## <a name="next-steps"></a>다음 단계

Azure Machine Learning [사용할 수 있는 모듈 집합](module-reference.md) 을 참조 하세요. 