---
title: '클러스터에 데이터 할당: 모듈 참조'
titleSuffix: Azure Machine Learning
description: 클러스터링 모델의 점수를 매기는 Azure Machine Learning의 클러스터에 데이터 할당 모듈을 사용 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: eff480d6763ae4bd277e6781663c559cc7c9169e
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152451"
---
# <a name="module-assign-data-to-clusters"></a>모듈: 클러스터에 데이터 할당

이 문서에서는 Azure Machine Learning designer (미리 보기)의 *클러스터에 데이터 할당* 모듈을 사용 하는 방법을 설명 합니다. 이 모듈은 *K를 의미* 하는 클러스터링 알고리즘을 사용 하 여 학습 된 클러스터링 모델을 통해 예측을 생성 합니다.

클러스터에 데이터 할당 모듈은 각 새 데이터 요소에 대해 가능한 할당을 포함 하는 데이터 집합을 반환 합니다. 

## <a name="how-to-use-assign-data-to-clusters"></a>클러스터에 데이터 할당을 사용 하는 방법
  
1. Azure Machine Learning 디자이너에서 이전에 학습 된 클러스터링 모델을 찾습니다. 다음 방법 중 하나를 사용 하 여 클러스터링 모델을 만들고 학습할 수 있습니다.  
  
    - K를 구성 하 [는 클러스터링 알고리즘](k-means-clustering.md) 을 사용 하 여 클러스터링 알고리즘을 구성 하 고, 데이터 집합 및 클러스터링 모델 학습 모듈 (이 문서)을 사용 하 여 모델을 학습 합니다.  
  
    - 작업 영역의 **저장 된 모델** 그룹에서 기존의 학습 된 클러스터링 모델을 추가할 수도 있습니다.

2. **데이터를 클러스터에 할당**의 왼쪽 입력 포트에 학습 된 모델을 연결 합니다.  

3. 새 데이터 집합을 입력으로 연결 합니다. 

   이 데이터 집합에서 레이블은 선택 사항입니다. 일반적으로 클러스터링은 자율 learning 메서드입니다. 범주를 미리 알 수 없습니다. 그러나 입력 열은 클러스터링 모델을 학습 하는 데 사용 된 열과 동일 해야 합니다. 그렇지 않으면 오류가 발생 합니다.

    > [!TIP]
    > 클러스터 예측에서 디자이너에 기록 되는 열 수를 줄이려면 [데이터 집합에서 열 선택](select-columns-in-dataset.md)을 사용 하 고 열의 하위 집합을 선택 합니다. 
    
4. 결과 (클러스터 할당)를 표시 하는 열을 포함 하 여 전체 입력 데이터 집합을 결과에 포함 하려면 결과에 **만 추가 또는 선택 취소** 확인란을 선택 된 상태로 둡니다.
  
    이 확인란의 선택을 취소 하면 결과만 반환 됩니다. 이 옵션은 웹 서비스의 일부로 예측을 만들 때 유용할 수 있습니다.
  
5.  파이프라인을 실행합니다.  
  
### <a name="results"></a>결과

+  데이터 집합의 값을 보려면 모듈을 마우스 오른쪽 단추로 클릭 한 다음 **시각화**를 선택 합니다. 또는 모듈을 선택 하 고 오른쪽 패널의 **출력** 탭으로 전환 하 고 **포트 출력** 에서 히스토그램 아이콘을 클릭 하 여 결과를 시각화 합니다.

