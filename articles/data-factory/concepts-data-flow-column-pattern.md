---
title: Azure Data Factory 매핑 데이터 흐름의 열 패턴
description: Azure Data Factory 매핑 데이터 흐름에서 열 패턴을 사용 하 여 일반화 된 데이터 변환 패턴 만들기
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/21/2019
ms.openlocfilehash: 0c9a3c2ef05f4a11933ca7fc81c7c0f87a612293
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72789873"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>매핑 데이터 흐름에서 열 패턴 사용

여러 매핑 데이터 흐름 변환을 사용 하면 하드 코드 된 열 이름 대신 패턴을 기반으로 템플릿 열을 참조할 수 있습니다. 이러한 일치를 *열 패턴*이라고 합니다. 정확한 필드 이름을 요구 하는 대신 이름, 데이터 형식, 스트림 또는 위치에 따라 열을 일치 시키는 패턴을 정의할 수 있습니다. 열 패턴이 유용한 두 가지 시나리오는 다음과 같습니다.

* 들어오는 원본 필드가 텍스트 파일 또는 NoSQL 데이터베이스에서 열을 변경 하는 경우와 같이 자주 변경 되는 경우 이 시나리오를 [스키마 드리프트](concepts-data-flow-schema-drift.md)라고 합니다.
* 많은 열 그룹에서 일반적인 작업을 수행 하려는 경우 예를 들어 열 이름에 ' total '이 포함 된 모든 열을 double로 캐스팅 하려고 합니다.

열 패턴은 현재 파생 열, 집계, 선택 및 싱크 변환에서 사용할 수 있습니다.

## <a name="column-patterns-in-derived-column-and-aggregate"></a>파생 열 및 집계의 열 패턴

집계 변환의 파생 열 또는 집계 탭에 열 패턴을 추가 하려면 기존 열의 오른쪽에 있는 더하기 아이콘을 클릭 합니다. **열 패턴 추가**를 선택 합니다. 

![열 패턴](media/data-flow/columnpattern.png "열 패턴")

[식 작성기](concepts-data-flow-expression-builder.md) 를 사용 하 여 일치 조건에 입력 합니다. 열의 `name`, `type`, `stream`및 `position`를 기반으로 하는 열과 일치 하는 부울 식을 만듭니다. 이 패턴은 조건이 true를 반환 하는 열, 데이터베이스가 드리프트 또는 defined 열에 영향을 줍니다.

일치 조건 아래의 두 식 상자는 영향을 받는 열의 새 이름과 값을 지정 합니다. `$$`를 사용 하 여 일치 하는 필드의 기존 값을 참조 합니다. 왼쪽 식 상자는 이름을 정의 하 고 오른쪽 식 상자는 값을 정의 합니다.

![열 패턴](media/data-flow/columnpattern2.png "열 패턴")

위의 열 패턴은 double 형식의 모든 열을 일치 시키고 일치 하는 항목 마다 하나의 집계 열을 만듭니다. 새 열의 이름은 일치 하는 열 이름이 ' _total '과 연결 되어 있습니다. 새 열의 값은 반올림 되어 기존 double 값의 집계 된 합계입니다.

일치 조건이 올바른지 확인 하기 위해 **검사** 탭에서 정의 된 열의 출력 스키마 유효성을 검사 하거나 **데이터 미리 보기** 탭에서 데이터의 스냅숏을 가져올 수 있습니다. 

![열 패턴](media/data-flow/columnpattern3.png "열 패턴")

## <a name="rule-based-mapping-in-select-and-sink"></a>Select 및 sink의 규칙 기반 매핑

원본에서 열을 매핑하고 변환을 선택 하는 경우 고정 매핑 또는 규칙 기반 매핑을 추가할 수 있습니다. 데이터의 스키마를 알고 있고 원본 데이터 집합의 특정 열이 항상 특정 정적 이름과 일치 하도록 하려면 고정 매핑을 사용 합니다. 유연한 스키마를 사용 하 여 작업 하는 경우 규칙 기반 매핑을 사용 하 여 `name`, `type`, `stream`및 열 `position`에 따라 패턴 일치를 작성 합니다. 고정 및 규칙 기반 매핑의 모든 조합을 사용할 수 있습니다. 

규칙 기반 매핑을 추가 하려면 **매핑 추가** 를 클릭 하 고 **규칙 기반 매핑을**선택 합니다.

![규칙 기반 매핑](media/data-flow/rule2.png "규칙 기반 매핑")

왼쪽 식 상자에 부울 일치 조건을 입력 합니다. 오른쪽 식 상자에서 일치 하는 열을 매핑할 대상을 지정 합니다. `$$`를 사용 하 여 일치 하는 필드의 기존 이름을 참조 합니다.

아래쪽 펼침 단추를 클릭 하면 regex 매핑 조건을 지정할 수 있습니다.

규칙 기반 매핑 옆의 안경 아이콘을 클릭 하 여 일치 하는 정의 된 열과 매핑되는 항목을 확인 합니다.

![규칙 기반 매핑](media/data-flow/rule1.png "규칙 기반 매핑")

위의 예에서는 두 개의 규칙 기반 매핑이 만들어집니다. 첫 번째는 ' movie '로 이름이 지정 되지 않은 모든 열을 사용 하 여 기존 값에 매핑합니다. 두 번째 규칙은 regex를 사용 하 여 ' movie '로 시작 하는 모든 열을 일치 시키고 ' movieId ' 열에 매핑합니다.

규칙에 따라 여러 개의 동일한 매핑이 발생 하는 경우 중복 된 **입력 건너뛰기** 또는 **중복 출력 건너뛰기** 를 사용 하도록 설정 하 여 중복을 방지 합니다.

## <a name="pattern-matching-expression-values"></a>패턴 일치 식 값입니다.

* `$$`는 런타임에 각 일치 항목의 이름이 나 값으로 변환 됩니다.
* `name`은 들어오는 각 열의 이름을 나타냅니다.
* `type`은 들어오는 각 열의 데이터 형식을 나타냅니다.
* `stream`는 각 스트림 또는 흐름의 변환과 연결 된 이름을 나타냅니다.
* `position`은 데이터 흐름에서 열의 서 수 위치입니다.

## <a name="next-steps"></a>다음 단계
* 데이터 변환에 대한 매핑 데이터 흐름 [식 언어](data-flow-expression-functions.md) 에 대한 자세한 정보
* [싱크 변환](data-flow-sink.md) 에서 열 패턴을 사용 하 고 규칙 기반 매핑을 사용 하 여 [변환 선택](data-flow-select.md)
