---
title: Azure Data Factory 데이터 흐름 정렬 변환 매핑
description: Azure Data Factory 데이터 정렬 변환 매핑
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: d0482d1081c16dc89e7371c4c33de9b2bb4e4c2e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61348389"
---
# <a name="azure-data-factory-data-flow-sort-transformations"></a>Azure Data Factory 데이터 흐름 정렬 변환

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![정렬 설정](media/data-flow/sort.png "정렬")

정렬 변환을 사용하면 현재 데이터 스트림의 수신 행을 정렬할 수 있습니다. 이후에 정렬 변환의 발신 행은 설정된 순서 지정 규칙을 따릅니다. 각 필드 옆의 화살표 표시기를 사용하여 개별 열을 선택하고 ASC 또는 DEC로 정렬할 수 있습니다. 정렬을 적용하기 전에 열을 수정해야 하는 경우 “계산 열”을 클릭하여 식 편집기를 시작합니다. 그러면 단순히 정렬에 열을 적용하는 대신 정렬 작업에 대한 식을 작성할 수 있는 기회가 제공됩니다.

## <a name="case-insensitive"></a>대/소문자 구분하지 않음
문자열 또는 텍스트 필드를 정렬할 때 대/소문자를 무시하려는 경우 “대/소문자 구분 안 함”을 켤 수 있습니다.

“파티션 내에서만 정렬”은 Spark 데이터 분할을 활용합니다. 각 파티션 내에서만 수신 데이터를 정렬하면 Data Flow에서 전체 데이터 스트림을 정렬하는 대신 분할된 데이터를 정렬할 수 있습니다.

정렬 변환의 각 정렬 조건을 재배열할 수 있습니다. 따라서 정렬 우선 순위의 위로 열을 이동해야 하는 경우 마우스로 해당 행을 잡아 정렬 목록에서 위나 아래로 이동합니다.

분할이 정렬에 미치는 영향

ADF Data Flow는 데이터가 여러 노드 및 파티션에 분산된 빅 데이터 Spark 클러스터에서 실행됩니다. 데이터를 동일한 순서로 유지하기 위해 정렬 변환을 사용하는 경우 데이터 흐름을 설계할 때 이 점에 유의하는 것이 중요합니다. 후속 변환에서 데이터를 다시 분할하는 경우 데이터가 다시 섞여 정렬이 손실될 수 있습니다.

## <a name="next-steps"></a>다음 단계

정렬 후 사용 하려는 [집계 변환](data-flow-aggregate.md)
