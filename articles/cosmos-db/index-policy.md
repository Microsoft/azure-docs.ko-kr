---
title: Azure Cosmos DB 인덱싱 정책
description: 구성 하 고 인덱싱 정책을 자동 인덱싱 및 Azure Cosmos DB의 성능 향상에 대 한 기본값을 변경 하는 방법에 알아봅니다.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: thweiss
ms.openlocfilehash: 67bc3076be91ade140b39b7dd8037299902546a9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61046321"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Azure Cosmos DB의 인덱싱 정책

Azure Cosmos DB에서는 모든 컨테이너에는 컨테이너의 항목을 인덱싱해야 하는 방법을 결정 하는 인덱싱 정책을 있습니다. 기본 인덱싱 정책에 대 한를 새로 만든 컨테이너 인덱스 문자열 또는 숫자에 대해 범위 인덱스를 적용 하는 모든 항목의 모든 속성 및 공간 인덱스의 모든 GeoJSON 개체에 대 한 Point 형식입니다. 이 옵션을 사용 하면 인덱싱 및 인덱스 관리를 사전에 대 한 염려 없이 높은 쿼리 성능을 얻을 수 있습니다.

상황에 따라 요구 사항에 맞게 이러한 자동 동작을 재정의 하는 것이 좋습니다. 설정 하 여 컨테이너의 인덱싱 정책을 사용자 지정할 수 있습니다 해당 *인덱싱 모드*를 포함 하거나 제외 하 고 *속성 경로*합니다.

## <a name="indexing-mode"></a>인덱싱 모드

Azure Cosmos DB는 두 가지 인덱싱 모드를 지원합니다.

- **일관성**: 컨테이너의 인덱싱 정책을 일관성으로 설정 된 경우 인덱스 만들기, 업데이트 또는 항목을 삭제 하는 대로 동기적으로 업데이트 됩니다. 즉, 읽기 쿼리의 일관성 되도록 합니다 [계정에 대해 구성 된 일관성](consistency-levels.md)합니다.

- **없음**: 컨테이너의 인덱싱 정책을 None으로 설정 하는 경우 해당 컨테이너에서 효과적으로 비활성화 됩니다 인덱싱. 이 컨테이너 보조 인덱스 없이 순수 키-값 저장소로 사용 되는 경우 일반적으로 사용 됩니다. 대량 삽입 작업 속도 유용할 수 있습니다.

## <a name="including-and-excluding-property-paths"></a>포함 및 제외 속성 경로

사용자 지정 인덱싱 정책을 명시적으로 포함 하거나 인덱싱에서 제외할는 속성 경로 지정할 수 있습니다. 인덱싱된 경로의 수를 최적화 하 여 저장소 컨테이너에서 사용을 줄일 수 있으며 쓰기 작업의 대기 시간을 개선할 수 있습니다. 이러한 경로 다음 정의 된 [인덱싱 개요 섹션에서 설명 하는 방법을](index-overview.md#from-trees-to-property-paths) 다음 내용을 추가 하 여:

- 스칼라 값 (문자열 또는 숫자)으로 이어지는 경로 끝나는 `/?`
- 배열에서 요소를 통해 함께 사항은 합니다 `/[]` 표기법 (of `/0`, `/1` 등.)
- `/*` 와일드 카드를 사용 하 여 노드 아래의 모든 요소를 연결할 수 있습니다

동일한 예를 다시 수행 합니다.

    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 }
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }

- 합니다 `headquarters`의 `employees` 경로가 `/headquarters/employees/?`
- 합니다 `locations`' `country` 경로가 `/locations/[]/country/?`
- 아래에 있는 모든 항목에 대 한 경로 `headquarters` 는 `/headquarters/*`

경로 명시적으로 인덱싱 정책에 포함 하는 경우 또한 해당 경로 각 인덱스 형식에이 인덱스에 적용 됩니다. 데이터 형식에 대 한 인덱스 유형을 적용할 정의할 수 있습니다.

| 인덱스 유형 | 허용 되는 대상 데이터 형식 |
| --- | --- |
| 범위 | 문자열 또는 숫자 |
| 공간 | 점, LineString 또는 다각형 |

예를 들어 우리가 포함 될 수 있습니다는 `/headquarters/employees/?` 경로 지정 및는 `Range` 둘 다에 대 한 인덱스를 해당 경로에 적용 해야 `String` 및 `Number` 값.

### <a name="includeexclude-strategy"></a>전략 포함/제외 합니다.

모든 인덱싱 정책에 루트 경로 포함 하도록 `/*` 를 포함 또는 제외 된 경로 합니다.

- 인덱싱할 필요가 없는 경로 선택적으로 제외할 루트 경로 포함 합니다. 이 권장 되는 방법으로 Azure Cosmos DB를 사전에 모델을 추가할 수 있는 새 속성을 인덱싱할 수 있습니다.
- 선택적으로 인덱싱해야 하는 경로 포함 하려면 루트 경로 제외 합니다.

참조 [이 섹션에서는](how-to-manage-indexing-policy.md#indexing-policy-examples) 인덱싱 정책 예제에 대 한 합니다.

## <a name="modifying-the-indexing-policy"></a>인덱싱 정책 수정

컨테이너의 인덱싱 정책을 언제 든 지 업데이트할 수 있습니다 [Azure portal 또는 지원 되는 Sdk 중 하나를 사용 하 여](how-to-manage-indexing-policy.md)입니다. 인덱싱 정책에 대 한 업데이트 트리거 (따라서 추가 저장소 공간이 없습니다 작업 중에 사용 됨) 온라인 상태이 고 현재 위치에서 수행 되는 새 쿼리와 이전 인덱스에서 변환 합니다. 이전 정책의 인덱스 쓰기 가용성 또는 컨테이너에서 프로 비전 된 처리량에 영향을 주지 않고 새 정책에 효율적으로 변환 됩니다. 인덱스 변환 비동기 작업의 경우 이며 완료 하는 데 걸리는 시간 프로 비전된 된 처리량, 항목의 수와 크기에 따라 달라 집니다. 

> [!NOTE]
> 진행 중인 인덱싱을 다시 수행 하는 동안 쿼리는 일치 하는 모든 결과 반환 하지 않을 수 및 모든 오류를 반환 하지 않아도 됩니다. 즉, 인덱스 변환이 완료 될 때까지 쿼리 결과가 일관 되지 않을 수 있습니다. 인덱스 변환의 진행률을 추적할 수 있기 [Sdk 중 하나를 사용 하 여](how-to-manage-indexing-policy.md)입니다.

새 인덱싱 정책의 모드는 Consistent로 설정 됩니다, 경우에 인덱스 변환이 진행 중인 동안 없는 다른 인덱싱 정책 변경을 적용할 수 있습니다. None (인덱스를 삭제는 즉시)을 인덱싱 정책의 모드를 설정 하 여 실행 중인 인덱스 변환을 취소할 수 있습니다.

## <a name="indexing-policies-and-ttl"></a>인덱싱 정책 및 TTL

합니다 [Time to Live (TTL) 기능](time-to-live.md) 인덱싱을 설정 된 컨테이너에서 활성화 되도록 해야 합니다. 이는 다음을 의미합니다.

- 인덱싱 모드를 None으로 설정 되어 있는 컨테이너에서 TTL을 활성화할 수 없는
- TTL가 활성화 하는 컨테이너에서 None으로 인덱싱 모드를 설정 하는 것이 불가능 합니다.

여기서 속성 경로가 없는 인덱싱할 하지만 TTL이 필요한 시나리오에서 사용 하 여 인덱싱 정책을 사용할 수 있습니다.

- 일관성을 설정 하는 인덱싱 모드 및
- 포함된 경로 및
- `/*` 경로로 제외 합니다.

## <a name="obsolete-attributes"></a>사용 되지 않는 특성

인덱싱 정책을 사용 하 여 작업을 하는 경우 이제 사용 되지 않는 다음 특성을 발생할 수 있습니다.

- `automatic` 부울 인덱싱 정책의 루트에서 정의 됩니다. 이제 무시 되 고 설정할 수 있는 `true`사용 중인 도구가 필요한 경우.
- `precision` 숫자로 포함된 경로 대 한 인덱스 수준에서 정의 됩니다. 이제 무시 되 고 설정할 수 있는 `-1`사용 중인 도구가 필요한 경우.
- `hash` 이제 범위 종류도 대체 되는 인덱스 종류입니다.

## <a name="next-steps"></a>다음 단계

다음 문서에서 인덱싱에 대해 자세히 알아보세요.

- [인덱싱 개요](index-overview.md)
- [인덱싱 정책을 관리하는 방법](how-to-manage-indexing-policy.md)
