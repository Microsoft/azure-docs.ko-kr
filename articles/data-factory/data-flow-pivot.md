---
title: Azure Data Factory Mapping Data Flow 피벗 변환
description: Azure 데이터 팩터리 매핑 데이터 흐름 피벗 변환을 사용 하 여 열을 행의 데이터를 피벗 합니다.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: e16cac281b77f3ca93d9ef358ae806203bc8b663
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61348508"
---
# <a name="azure-data-factory-pivot-transformation"></a>Azure 데이터 팩터리 피벗 변환
[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

ADF Data Flow의 피벗을 하나 이상의 그룹화 열의 고유한 행 값이 개별 열로 변환되는 집계로 사용합니다. 기본적으로, 행 값을 새 열로 피벗할 수 있습니다(데이터를 메타데이터로 변환).

![피벗 옵션](media/data-flow/pivot1.png "피벗 1")

## <a name="group-by"></a>그룹화 방법

![피벗 옵션](media/data-flow/pivot2.png "피벗 2")

먼저 피벗 집계에 그룹화 기준으로 사용할 열을 설정합니다. 여기에서 열 목록 옆에 +기호를 사용하여 둘 이상의 열을 설정할 수 있습니다.

## <a name="pivot-key"></a>피벗 키

![피벗 옵션](media/data-flow/pivot3.png "피벗 3")

피벗 키는 ADF가 행에서 열로 피벗하는 열입니다. 기본적으로 이 필드에 대한 데이터 세트의 각 고유 값은 열로 피벗됩니다. 그러나 열 값으로 피벗하려는 데이터 세트의 값을 선택적으로 입력할 수 있습니다. 이 생성 되는 새 열을 결정 하는 열입니다.

## <a name="pivoted-columns"></a>피벗된 열

![피벗 옵션](media/data-flow/pivot4.png "피벗 4")

마지막으로, 피벗된 값에 사용할 집계와 변환의 새 출력 프로젝션에 열을 표시하는 방법을 선택합니다.

(선택 사항) 행 값에서 검색된 각각의 새 열 이름에 추가할 접두사, 중간 및 접미사를 사용한 이름 지정 패턴을 설정할 수 있습니다.

예를 들어 “Sales”를 “Region”으로 피벗하는 경우 단순히 각 판매 값의 새 열 값(예: "25", "50", "1000" 등)을 얻게 됩니다. 그러나 접두사 값 "Sales-"로 설정 하면 각 열 값에 추가 됩니다 "Sales-" 값의 시작.

![피벗 옵션](media/data-flow/pivot5.png "피벗 5")

열 정렬을 “기본”으로 설정하면 피벗된 모든 열이 집계된 값과 함께 그룹화됩니다. 열 정렬을 “횡적”으로 설정하면 열과 값이 교대로 지정됩니다.

### <a name="aggregation"></a>집계

피벗 값에 사용하려는 집계를 설정하려면 피벗 열 창 아래쪽의 필드를 클릭합니다. 집계 식을 작성하고 집계된 새 값에 대해 설명이 포함된 별칭 이름을 제공할 수 있는 ADF Data Flow 식 작성기를 시작합니다.

ADF Data Flow 식 언어를 사용하여 식 작성기의 피벗 열 변환을 설명합니다. https://aka.ms/dataflowexpressions

## <a name="pivot-metadata"></a>피벗 메타 데이터

피벗 변환에는 동적 프로그램 들어오는 데이터를 기반으로 하는 새 열 이름을 생성 됩니다. 피벗 키 각각 새 열 이름에 대 한 값을 생성합니다. 그런 다음 개별 값을 지정 하지 않으며 피벗 키에서 각 고유 값의 동적 열 이름을 만들 경우 검사에서 메타 데이터를 UI에 표시 되지 않습니다 고 싱크 변환에 없는 열 전파 됩니다. 피벗 키 값을 설정 하는 경우 다음 ADF에 새 열 이름을 확인할 수 있습니다 하 고 해당 열 이름 됩니다는 검사에 사용할 수 있습니다 매핑 싱크 합니다.

### <a name="landing-new-columns-in-sink"></a>새 열을 싱크에서 방문

피벗의 동적 열 이름으로도 있습니다 여전히 싱크할 수에 새 열 이름 및 값을 대상 저장소로. "스키마 드리프트 허용"로 설정에서 싱크 설정에서. 새 동적 이름을 열 메타 데이터를에서 표시 되지 않습니다 하지만 스키마 드리프트 옵션을 사용 하면 데이터를 두려면 합니다.

### <a name="view-metadata-in-design-mode"></a>메타 데이터 디자인 모드에서 보기

검사에서 메타 데이터로 새 열 이름을 보려면 하려는 싱크 변환에 명시적으로 전파 열을 표시 하려는 경우 다음 피벗 키 탭에서 명시적 값을 설정 합니다.

### <a name="how-to-rejoin-original-fields"></a>원본 필드를 다시 조인하는 방법
피벗 변환은 집계, 그룹화 및 피벗 작업에 사용되는 열만 프로젝션합니다. 흐름의 이전 단계에서 다른 열을 포함 하려는 경우 이전 단계에서 새 분기를 사용 하 고 자체 조인 패턴을 사용 하 여 원래 메타 데이터를 사용 하 여 흐름을 연결 합니다.

## <a name="next-steps"></a>다음 단계

시도 된 [피벗 해제 변환](data-flow-unpivot.md) 행 값으로 열 값을 설정 합니다. 
