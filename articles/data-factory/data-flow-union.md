---
title: Azure Data Factory Mapping Data Flow 새 분기 변환
description: Azure Data Factory Mapping Data Flow 새 분기 변환
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: af2225d749283c7124f89d5a7cd735b2f6bfd121
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61348282"
---
# <a name="mapping-data-flow-union-transformation"></a>매핑 데이터 흐름에 대 한 공용 구조체 변환

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

공용 구조체는 여러 데이터 스트림을 하나로 결합하고 해당 스트림의 SQL 공용 구조체를 공용 구조체 변환의 새 출력으로 사용합니다. 각 입력 스트림에서 스키마의 모든 조인 키가 필요 없이 데이터 흐름 내에서 결합할 수 됩니다.

데이터 흐름에서 원본 데이터 뿐만 아니라 기존 변환에서 스트림을 포함 하 여 구성 된 각 행에 옆에 있는 "+" 아이콘을 선택 하 여 n-설정 테이블에서 스트림 수를 결합할 수 있습니다.

![공용 구조체 변환](media/data-flow/union.png "공용 구조체")

이 경우 (이 예제에서는 세 개의 서로 다른 소스 파일)의 여러 원본에서 서로 다른 메타 데이터를 결합 하 고 단일 스트림으로 결합할 수 있습니다.

![공용 구조체 변환 개요](media/data-flow/union111.png "공용 구조체 1")

이렇게 하려면 추가 하려는 모든 원본을 포함 하 여 공용 구조체 설정에 추가 행을 추가 합니다. 공통 조회 나 조인 키에 대 한 필요 하지 않습니다.

![공용 구조체 변환 설정을](media/data-flow/unionsettings.png "통합 설정")

공용 구조체 후 선택 변환으로 설정한 경우 겹치는 필드나 headerless 원본의 이름이 지정 되지 않은 필드의 이름을 바꿀 수 됩니다. 이 예제에서는 서로 다른 세 원본의 132 총 열과 결합 메타 데이터를 보려면 "검사"를 클릭 하십시오.

![공용 구조체 변환 최종](media/data-flow/union333.png "Union 3")

## <a name="name-and-position"></a>이름 및 위치

"이름으로 union"을 선택 하면 새 연결 된 메타 데이터 스키마를 사용 하 여 각 소스의 해당 열에 각 열 값이 삭제 됩니다.

"위치별 union"을 선택 하면 각 열 값 각 해당 원본에는 새 결합 된 데이터 스트림의 각 원본에서 데이터를 동일한 스트림에 추가 됩니다 여기서 결과에서 원래 위치에 삭제 됩니다.

![Union 출력](media/data-flow/unionoutput.png "Union 출력")

## <a name="next-steps"></a>다음 단계

비슷한 변환을 포함 하 여 탐색 [조인](data-flow-join.md) 하 고 [Exists](data-flow-exists.md)합니다.
