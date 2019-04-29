---
title: Azure Data Factory Mapping Data Flow 스키마 드리프트
description: 스키마 드리프트를 사용하여 Azure Data Factory에서 복원력 있는 데이터 흐름 빌드
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: aadab68185347dc0a12e0802f675efe13ecea545
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61262162"
---
# <a name="mapping-data-flow-schema-drift"></a>Mapping Data Flow 스키마 드리프트

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

스키마 드리프트 개념은 원본이 메타데이터를 자주 변경하는 경우입니다. 필드, 열, 형식 등이 즉석에서 추가, 제거 또는 변경될 수 있습니다. 스키마 드리프트를 처리하지 않으면 데이터 흐름이 업스트림 데이터 원본의 변경에 취약해집니다. 수신 열 및 필드가 변경되는 경우 일반적인 ETL 패턴은 해당 원본 이름에 연결되는 경향이 있으므로 실패합니다.

스키마 드리프트로부터 보호하려면 데이터 엔지니어가 다음 작업을 수행할 수 있게 해주는 Data Flow 도구의 기능이 필요합니다.

* 변경 가능한 필드 이름, 데이터 형식, 값 및 크기가 있는 원본 정의
* 하드 코드된 필드 및 값 대신 데이터 패턴을 사용할 수 있는 변환 매개 변수 정의
* 명명된 필드를 사용하는 대신 수신 필드와 일치하는 패턴을 이해하는 식 정의

## <a name="how-to-implement-schema-drift"></a>스키마 드리프트를 구현 하는 방법

* 원본 변환에서 “스키마 드리프트 허용” 선택

<img src="media/data-flow/schemadrift001.png" width="400">

* 이 옵션을 선택하면 Data Flow가 실행될 때마다 원본에서 모든 수신 필드를 읽고 전체 흐름을 거쳐 싱크로 전달합니다.

* 새 필드가 모두 선택되고 대상에 배치되도록 “자동 매핑”을 사용하여 싱크 변환의 새 필드를 모두 매핑해야 합니다.

<img src="media/data-flow/automap.png" width="400">

* 간단한 원본 -> 싱크(즉, 복사) 매핑을 사용하면 해당 시나리오에서 새 필드가 도입될 때 모든 기능이 제대로 작동합니다.

* 해당 워크플로에 스키마 드리프트를 처리하는 변환을 추가하려면 패턴 일치를 사용하여 이름, 형식 및 값을 기준으로 열을 일치시킬 수 있습니다.

* “스키마 드리프트”를 이해하는 변환을 만들려는 경우 파생 열 또는 집계 변환에서 “열 패턴 추가”를 클릭합니다.

<img src="media/data-flow/columnpattern.png" width="400">

> [!NOTE]
> 흐름 전체의 스키마 드리프트를 허용하도록 데이터 흐름의 아키텍처를 결정해야 합니다. 이렇게 하면 원본의 스키마 변경으로부터 보호할 수 있습니다. 그러나 데이터 흐름 전체에서 열과 형식의 컴파일 시 바인딩이 손실됩니다. Azure Data Factory는 스키마 드리프트 흐름을 런타임에 바인딩 흐름으로 처리하므로 변환을 빌드할 때 흐름 전체의 스키마 보기에 열 이름이 제공되지 않습니다.

<img src="media/data-flow/taxidrift1.png" width="400">

Taxi Demo 샘플 데이터 흐름에서 TripFare 원본을 사용하는 맨 아래 데이터 흐름에 샘플 스키마 드리프트가 있습니다. 집계 변환의 집계 필드에는 “열 패턴” 디자인을 사용하고 있습니다. 특정 열 이름을 지정하거나 위치를 기준으로 열을 찾는 대신 데이터가 변경될 수 있으며 실행 간에 동일한 순서로 나타나지 않을 수 있다고 가정합니다.

이 Azure Data Factory Data Flow 스키마 드리프트 처리 예제에서는 ‘double’ 형식의 열을 검사하는 집계를 빌드했으며 데이터 도메인에 각 여행의 가격이 포함되어 있음을 알고 있습니다. 그런 다음, 열 배치 위치와 열 이름 지정과 관계없이 원본의 모든 double 필드에서 집계 수학 계산을 수행할 수 있습니다.

Azure Data Factory Data Flow 구문은 $$를 사용하여 일치 패턴에서 일치된 각 열을 나타냅니다. 복합 문자열 검색과 정규식 함수를 사용하여 열 이름을 일치시킬 수도 있습니다. 이 경우 열의 ‘double’ 형식과 일치하는 각 항목에 따라 새 집계 필드 이름을 만들고 일치된 각 이름에 ```_total``` 텍스트를 추가합니다. 

```concat($$, '_total')```

그런 다음, 일치된 각 열의 값을 반올림하고 합계를 계산합니다.

```round(sum ($$))```

Azure Data Factory Data Flow 샘플 “Taxi Demo”를 사용하여 이 작업을 테스트할 수 있습니다. 결과를 대화형으로 확인할 수 있도록 Data Flow 디자인 화면의 맨 위에 있는 디버그 토글을 사용하여 디버그 세션을 켭니다.

<img src="media/data-flow/taxidrift2.png" width="800">

## <a name="access-new-columns-downstream"></a>새 열 다운스트림 액세스

열 패턴을 사용 하 여 새 열을 생성 하는 경우에 "byName" 식 함수를 사용 하 여 데이터 흐름 변환의 뒷부분에 나오는 이러한 새 열을 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

에 [데이터 흐름 식 언어](data-flow-expression-functions.md) 열 패턴 및 스키마 드리프트 "byName" 및 "byPosition"를 포함 하 여 추가 기능을 찾을 수 있습니다.
