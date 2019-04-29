---
title: Azure Data Factory Data Flow 조인 변환
description: Azure Data Factory Data Flow 조인 변환
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: 18f713198ef9aa45cb72a6718c0f7b086c019258
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61348555"
---
# <a name="mapping-data-flow-join-transformation"></a>데이터 흐름 조인 변환 매핑

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

조인을 사용하여 Data Flow에서 두 테이블의 데이터를 결합합니다. 왼쪽 관계가 될 변환을 클릭하고 도구 상자에서 조인 변환을 추가합니다. 조인 변환 내부에서 오른쪽 관계가 될 데이터 흐름의 다른 데이터 스트림을 선택합니다.

![조인 변환](media/data-flow/join.png "조인")

## <a name="join-types"></a>조인 유형

조인 유형을 선택 하는 것은 조인 변환에 대 한 필수입니다.

### <a name="inner-join"></a>내부 조인

내부 조인은 양쪽 테이블의 열 조건과 일치 하는 행만 전달 합니다.

### <a name="left-outer"></a>왼쪽 우선 외부

조인 조건에 맞지 않는 왼쪽 스트림의 모든 행을 통과하고, 내부 조인에서 반환된 모든 행 외에, 다른 테이블의 출력 열도 NULL로 설정됩니다.

### <a name="right-outer"></a>오른쪽 우선 외부

조인 조건에 맞지 않는 오른쪽 스트림의 모든 행을 통과하고, 내부 조인에서 반환된 모든 행 외에, 다른 테이블에 해당하는 출력 열도 NULL로 설정됩니다.

### <a name="full-outer"></a>완전 외부

완전 외부 모든 열을 생성 하 고 다른 테이블의 열에 대해 NULL 값을 사용 하 여 양쪽에서 행 없음.

### <a name="cross-join"></a>크로스 조인

식을 사용 하 여 두 스트림의 교차곱을 지정 합니다. 사용자 지정 조인 조건을 만들려면 다음과 같이 사용할 수 있습니다.

## <a name="specify-join-conditions"></a>조인 조건 지정

왼쪽 조인 조건은 조인 왼쪽에 연결된 데이터 스트림에서 가져옵니다. 오른쪽 조인 조건은 아래쪽의 조인에 연결된 두 번째 데이터 스트림으로, 다른 스트림에 대한 직접 커넥터이거나 다른 스트림에 대한 참조가 됩니다.

1개 이상의 (1..n) 조인 조건을 입력해야 합니다. 직접 참조하거나, 드롭다운 메뉴에서 선택하거나, 식에 해당하는 필드일 수 있습니다.

## <a name="join-performance-optimizations"></a>조인 성능 최적화

SSIS와 같은 도구의 병합 조인과 달리, ADF Data Flow의 조인은 필수 병합 조인 작업이 아닙니다. 따라서 조인 키를 먼저 정렬할 필요가 없습니다. 조인 작업은 Spark의 최적 조인 작업을 기준으로 Databricks를 사용하여 Spark에서 발생합니다. 브로드캐스트/맵 쪽 조인:

![조인 변환 최적화](media/data-flow/joinoptimize.png "조인 최적화")

데이터 세트가 Databricks 작업자 노드 메모리에 맞으면 조인 성능을 최적화할 수 있습니다. 조인 작업에서 데이터의 분할을 지정하여 작업자당 메모리에 더 잘 맞는 데이터 세트를 만들 수도 있습니다.

## <a name="self-join"></a>셀프 조인

변환 선택을 통해 기존 스트림에 별칭을 지정하여 ADF 데이터 흐름에서 셀프 조인 조건을 수행할 수 있습니다. 먼저 스트림에서 "새 분기"를 만든 후 선택을 추가하여 전체 원본 스트림에 별칭을 지정합니다.

![셀프 조인](media/data-flow/selfjoin.png "셀프 조인")

위의 다이어그램에서 선택 변환은 맨 위에 있습니다. 전반적으로 원래 스트림의 별칭을 “OrigSourceBatting”으로 지정합니다. 아래 강조 표시된 조인 변환에서 이 선택 스트림 별칭 지정을 오른쪽 조인으로 사용하여 내부 조인의 왼쪽 및 오른쪽에서 동일한 키를 참조할 수 있음을 확인할 수 있습니다.

## <a name="composite-and-custom-keys"></a>복합 및 사용자 지정 키

사용자 지정 및 복합 키 조인 변환 내에서 즉석에서 빌드할 수 있습니다. 각 관계 행 옆의 더하기 기호 (+)를 사용 하 여 추가 조인 열에 대 한 행을 추가 합니다. 또는 식 작성기는 즉석에서 조인 값에 대 한 새 키 값을 계산 합니다.

## <a name="next-steps"></a>다음 단계

데이터를 조인한 후 할 수 있습니다 [새 열을 만듭니다](data-flow-derived-column.md) 하 고 [대상 데이터 저장소로 데이터를 싱크](data-flow-sink.md)합니다.
