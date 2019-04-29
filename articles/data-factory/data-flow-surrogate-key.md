---
title: Azure Data Factory Mapping Data Flow 서로게이트 키 변환
description: 변환을 사용 하 여 Azure Data Factory의 매핑 데이터 흐름 서로게이트 키 순차 키 값을 생성 하는 방법
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: eaa1c577f7e208400d3430222b006e0dbbd7956a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61350626"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>데이터 흐름 서로게이트 키 변환 매핑

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

서로게이트 키 변환을 사용하여 증가하는 비업무용 임의 키 값을 데이터 흐름 행 집합에 추가합니다. 이는 차원 테이블의 각 구성원이 Kimball DW 방법론의 일부인 비업무용 키인 고유 키를 가져야 하는 별모양 스키마 분석 데이터 모델에서 차원 테이블을 디자인할 때 유용합니다.

![서로게이트 키 변환](media/data-flow/surrogate.png "서로게이트 키 변환")

"키 열"은 새로운 서로게이트 키 열에 제공할 이름입니다.

"시작 값"은 증분 값의 시작점입니다.

## <a name="increment-keys-from-existing-sources"></a>기존 원본에서 키를 증가

원본에 존재 하는 값에서 시퀀스를 시작 하려는 경우에 서로게이트 키 변환 바로 다음 파생 열 변환을 사용 하 여 수 있으며 두 값을 함께 추가:

![SK 추가 최대](media/data-flow/sk006.png "서로게이트 키 변환 추가 최대")

이전 max 사용 하 여 키 값을 시드를 사용할 수 있는 두 가지 기술 가지 있습니다.

### <a name="database-sources"></a>데이터베이스 원본

원본 변환을 사용 하 여 원본에서 max ()를 선택 하려면 "Query" 옵션을 사용 합니다.

![중요 한 쿼리를 서로게이트](media/data-flow/sk002.png "서로게이트 키 변환 쿼리")

### <a name="file-sources"></a>파일 원본

파일에 이전 최대 값이 있는 경우에 집계 변환 함께 소스 변환에 사용 하 고 max () 식 함수를 사용 하 여 이전 최대 값을 검색할 수 있습니다.

![키 파일을 서로게이트](media/data-flow/sk008.png "서로게이트 키 파일")

두 경우 모두 이전 최대 값이 포함 된 소스와 함께 들어오는 새 데이터를 조인 해야 합니다.

![서로게이트 키 조인의](media/data-flow/sk004.png "서로게이트 키 조인")

## <a name="next-steps"></a>다음 단계

이러한 예에서 사용 된 [조인](data-flow-join.md) 및 [파생 열](data-flow-derived-column.md) 변환 합니다.
