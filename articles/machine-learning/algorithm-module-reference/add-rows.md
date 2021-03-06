---
title: '행 추가: 모듈 참조'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 디자이너에서 행 추가 모듈을 사용 하 여 두 데이터 집합을 연결 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 2c2a74ee88e7161ab8dd1701ef4d105df67c0938
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93421943"
---
# <a name="add-rows-module"></a>행 추가 모듈

이 문서에서는 Azure Machine Learning 디자이너의 모듈을 설명 합니다.

이 모듈을 사용 하 여 두 데이터 집합을 연결 합니다. 연결에서 두 번째 데이터 집합의 행은 첫 번째 데이터 집합의 끝에 추가 됩니다.  
  
행 연결은 다음과 같은 시나리오에서 유용합니다.  
  
+ 일련의 평가 통계를 생성했으며 보다 쉬운 보고를 위해 이러한 통계를 테이블 하나로 결합하려는 경우  
  
+ 여러 데이터 집합에서 작업한 후 해당 데이터 집합을 결합하여 최종 데이터 집합을 만들려는 경우  

## <a name="how-to-use-add-rows"></a>행 추가를 사용 하는 방법  

두 데이터 집합의 행을 연결 하려면 행에 정확히 동일한 스키마가 있어야 합니다. 즉, 열에 동일한 수의 열과 동일한 데이터 형식이 있습니다.

1.  **행 추가** 모듈을 파이프라인으로 끌어 **데이터 변환** 아래에서 찾을 수 있습니다.

2. 두 입력 포트에 데이터 집합을 연결합니다. 추가할 데이터 집합이 두 번째(오른쪽) 포트에 연결되어야 합니다. 
  
3.  파이프라인을 제출합니다. 출력 데이터 집합의 행 수는 두 입력 데이터 집합의 행 합계와 같아야 합니다.

    **행 추가** 모듈의 두 입력에 동일한 데이터 집합을 추가 하는 경우 데이터 집합이 중복 됩니다. 

## <a name="next-steps"></a>다음 단계

Azure Machine Learning에서 [사용 가능한 모듈 세트](module-reference.md)를 참조하세요. 