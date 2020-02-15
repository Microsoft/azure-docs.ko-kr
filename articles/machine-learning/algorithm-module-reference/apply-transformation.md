---
title: '변형 적용: 모듈 참조'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 변환 적용 모듈을 사용 하 여 이전에 계산 된 변환을 기반으로 입력 데이터 집합을 수정 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 443b021b266a202775e94e44acac3a91a2b70617
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137574"
---
# <a name="apply-transformation-module"></a>변환 모듈 적용

이 문서에서는 Azure Machine Learning designer (미리 보기)의 모듈을 설명 합니다.

이 모듈을 사용 하 여 이전에 계산 된 변환을 기반으로 입력 데이터 집합을 수정 합니다.  
  
예를 들어 **데이터 정규화** 모듈을 사용 하 여 z 점수를 사용 하 여 학습 데이터를 정규화 한 경우 점수 매기기 단계 중에 학습에 대해 계산 된 z 점수 값을 사용 하는 것이 좋습니다. Azure Machine Learning에서는 정규화 방법을 변환으로 저장 한 다음 **변환 적용** 을 사용 하 여 점수 매기기 전에 입력 데이터에 z 점수를 적용할 수 있습니다.
  
Azure Machine Learning는 다양 한 종류의 사용자 지정 변환을 만들고 적용 하는 기능을 지원 합니다. 예를 들어 다음에 대 한 변환을 저장 한 다음 다시 사용할 수 있습니다.  
  
- 누락 된 **데이터 정리** 를 사용 하 여 누락 값 제거 또는 바꾸기
- 데이터 정규화, **데이터 표준화** 사용
  

## <a name="how-to-use-apply-transformation"></a>변형 적용 사용 방법  
  
1. 파이프라인에 **변환 적용** 모듈을 추가 합니다. **모델 점수 매기기 & 평가** 범주에서이 모듈을 찾을 수 있습니다. 
  
2. 입력으로 사용할 기존 변환을 찾습니다. 이전에 저장 한 변환은 왼쪽 모듈 트리의 **데이터 집합** 범주에 있는 **내 데이터 집합** 그룹에서 찾을 수 있습니다.  
  
   
  
3. 변형할 데이터 집합을 연결 합니다. 데이터 집합은 변환의 첫 번째 스키마 (열 수, 열 이름, 데이터 형식)가 정확히 일치 해야 합니다.  
  
4. 변환을 정의할 때 모든 사용자 지정이 수행 되기 때문에 다른 매개 변수를 설정 하지 않아도 됩니다.  
  
5. 새 데이터 집합에 변환을 적용 하려면 파이프라인을 실행 합니다.  

## <a name="next-steps"></a>다음 단계

Azure Machine Learning [사용할 수 있는 모듈 집합](module-reference.md) 을 참조 하세요. 