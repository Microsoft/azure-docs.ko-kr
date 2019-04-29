---
title: Azure Data Factory Mapping Data Flow 피벗 해제 변환
description: Azure Data Factory Mapping Data Flow 피벗 해제 변환
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 160ff8bbcb8ce5a4f1f32245cc366281640c5919
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61348319"
---
# <a name="azure-data-factory-unpivot-transformation"></a>Azure Data Factory를 피벗 해제 변환

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

ADF Mapping Data Flow의 피벗 해제는 단일 레코드에 있는 여러 열의 값을 단일 열의 값이 동일한 여러 레코드로 확장하여 비정규화된 데이터 세트를 보다 정규화된 버전으로 전환하는 방법으로 사용합니다.

![피벗 해제 변환](media/data-flow/unpivot1.png "피벗 해제 옵션 1")

## <a name="ungroup-by"></a>그룹 해제 기준

![피벗 해제 변환](media/data-flow/unpivot5.png " 피벗 해제 옵션 2")

먼저 피벗 집계에 그룹화 기준으로 사용할 열을 설정합니다. 열 목록 옆에 있는 + 기호를 사용하여 그룹 해제할 열을 하나 이상 설정합니다.

## <a name="unpivot-key"></a>피벗 해제 키

![피벗 해제 변환](media/data-flow/unpivot6.png " 피벗 해제 옵션 3")

피벗 키는 ADF가 행에서 열로 피벗하는 열입니다. 기본적으로 이 필드에 대한 데이터 세트의 각 고유 값은 열로 피벗됩니다. 그러나 열 값으로 피벗하려는 데이터 세트의 값을 선택적으로 입력할 수 있습니다.

## <a name="unpivoted-columns"></a>피벗 해제된 열

![피벗 해제 변환](media/data-flow//unpivot7.png "피벗 해제 옵션 4")

마지막으로, 피벗된 값에 사용할 집계와 변환의 새 출력 프로젝션에 열을 표시하는 방법을 선택합니다.

(선택 사항) 행 값에서 검색된 각 새 열 이름에 추가할 접두사, 중간 및 접미사를 사용한 이름 지정 패턴을 설정할 수 있습니다.

예를 들어 “Sales”를 “Region”으로 피벗하는 경우 단순히 각 판매 값에서 새 열 값이 제공됩니다. 예를 들면 다음과 같습니다. “25”, “50”, “1000”, ... 그러나 접두사 값으로 “Sales”를 설정하면 “Sales”가 값에 접두사로 추가됩니다.

<img src="media/data-flow/unpivot3.png" width="400">

열 정렬을 “기본”으로 설정하면 피벗된 모든 열이 집계된 값과 함께 그룹화됩니다. 열 정렬을 “횡적”으로 설정하면 열과 값이 교대로 지정됩니다.

![피벗 해제 변환](media/data-flow//unpivot7.png "피벗 해제 옵션 5")

피벗 해제된 최종 데이터 결과 집합은 이제 개별 행 값으로 피벗 해제된 열 합계를 보여 줍니다.

## <a name="next-steps"></a>다음 단계

사용 된 [피벗 변환](data-flow-pivot.md) 피벗 행을 열 수 있습니다.
