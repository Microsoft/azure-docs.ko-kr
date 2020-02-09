---
title: 데이터 흐름 피벗 변환 매핑
description: Azure Data Factory 매핑 데이터 흐름 피벗 변환을 사용 하 여 행에서 열로 데이터 피벗
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: 8f23b5e61e1aee83172a12466fac8d5b5003fea8
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930298"
---
# <a name="azure-data-factory-pivot-transformation"></a>Azure 데이터 팩터리 피벗 변환


ADF Data Flow의 피벗을 하나 이상의 그룹화 열의 고유한 행 값이 개별 열로 변환되는 집계로 사용합니다. 기본적으로, 행 값을 새 열로 피벗할 수 있습니다(데이터를 메타데이터로 변환).

![피벗 옵션](media/data-flow/pivot1.png "피벗 1")

## <a name="group-by"></a>Group By

![피벗 옵션](media/data-flow/pivot2.png "피벗 2")

먼저 피벗 집계에 그룹화 기준으로 사용할 열을 설정합니다. 여기에서 열 목록 옆에 +기호를 사용하여 둘 이상의 열을 설정할 수 있습니다.

## <a name="pivot-key"></a>피벗 키

![피벗 옵션](media/data-flow/pivot3.png "피벗 3")

피벗 키는 ADF가 행에서 열로 피벗하는 열입니다. 기본적으로 이 필드에 대한 데이터 세트의 각 고유 값은 열로 피벗됩니다. 그러나 열 값으로 피벗하려는 데이터 세트의 값을 선택적으로 입력할 수 있습니다. 생성 될 새 열을 결정 하는 열입니다.

## <a name="pivoted-columns"></a>피벗 열

![피벗 옵션](media/data-flow/pivot4.png "피벗 4")

마지막으로, 피벗된 값에 사용할 집계와 변환의 새 출력 프로젝션에 열을 표시하는 방법을 선택합니다.

(선택 사항) 행 값에서 검색된 각 새 열 이름에 추가할 접두사, 중간 및 접미사를 사용한 이름 지정 패턴을 설정할 수 있습니다.

예를 들어 "Region"으로 "Sales"를 피벗 하면 각 판매 값의 새 열 값 (예: "25", "50", "1000" 등)이 발생 합니다. 그러나 접두사 값 "Sales-"를 설정 하면 각 열 값이 값의 시작 부분에 "Sales-"를 추가 합니다.

![피벗 옵션](media/data-flow/pivot5.png "피벗 5")

열 정렬을 “기본”으로 설정하면 피벗된 모든 열이 집계된 값과 함께 그룹화됩니다. 열 정렬을 “횡적”으로 설정하면 열과 값이 교대로 지정됩니다.

### <a name="aggregation"></a>집계

피벗 값에 사용하려는 집계를 설정하려면 피벗 열 창 아래쪽의 필드를 클릭합니다. 집계 식을 작성하고 집계된 새 값에 대해 설명이 포함된 별칭 이름을 제공할 수 있는 ADF Data Flow 식 작성기를 시작합니다.

ADF Data Flow 식 언어를 사용하여 식 작성기의 피벗 열 변환을 설명합니다. https://aka.ms/dataflowexpressions

## <a name="pivot-metadata"></a>피벗 메타 데이터

피벗 변환을 사용 하면 들어오는 데이터를 기반으로 동적으로 새 열 이름이 생성 됩니다. 피벗 키는 각각의 새 열 이름에 대한 값을 생성 합니다. 개별 값을 지정 하지 않고 피벗 키의 각 고유 값에 대해 동적 열 이름을 만들려는 경우 UI는 검사 중인 메타 데이터를 표시 하지 않으며 싱크 변환에 대한 열 전파가 없습니다. 피벗 키에 대한 값을 설정 하면 ADF는 새 열 이름을 확인할 수 있으며 이러한 열 이름은 검사 및 싱크 매핑에서 사용할 수 있습니다.

### <a name="generate-a-new-model-from-dynamic-columns"></a>동적 열에서 새 모델 생성

피벗은 행 값에 따라 새 열 이름을 동적으로 생성 합니다. 이러한 새 열을 나중에 데이터 흐름에서 참조할 수 있는 메타 데이터로 전환할 수 있습니다. 이렇게 하려면 데이터 미리 보기 탭을 클릭 합니다. 피벗 변환에 의해 생성 된 모든 새 열은 테이블 헤더에 "데이터베이스가 드리프트" 아이콘이 표시 됩니다. "Map 데이터베이스가 드리프트" 단추를 클릭 하 여 이러한 새 열을 데이터 흐름의 모델에 포함 되도록 설정 합니다.

![열 피벗](media/data-flow/newpivot1.png "데이터베이스가 드리프트 Pivot 열 매핑")

### <a name="landing-new-columns-in-sink"></a>싱크에 새 열 방문

피벗에서 동적 열 이름을 사용 하는 경우에도 새 열 이름과 값을 대상 저장소로 싱크할 수 있습니다. 싱크 설정에서 "Schema 드리프트 허용"을 on으로 설정 하면 됩니다. 열 메타 데이터에는 새 동적 이름이 표시 되지 않지만 스키마 드리프트 옵션을 사용 하면 데이터를 연결할 수 있습니다.

### <a name="view-metadata-in-design-mode"></a>디자인 모드에서 메타 데이터 보기

검사 시 새 열 이름을 메타 데이터로 표시 하 고 열이 명시적으로 싱크 변환에 전파 되는 것을 확인 하려는 경우 피벗 키 탭에서 명시적 값을 설정 합니다.

### <a name="how-to-rejoin-original-fields"></a>원본 필드를 다시 조인하는 방법
피벗 변환은 집계, 그룹화 및 피벗 작업에 사용되는 열만 프로젝션합니다. 흐름에 이전 단계의 다른 열을 포함 하려는 경우 이전 단계의 새 분기를 사용 하 고 자체 조인 패턴을 사용 하 여 흐름을 원래 메타 데이터와 연결 합니다.

## <a name="next-steps"></a>다음 단계

피벗 해제 [변환을](data-flow-unpivot.md) 시도 하 여 열 값을 행 값으로 설정 합니다. 
