---
title: 데이터 흐름 조회 변환 매핑
description: 데이터 흐름 조회 변환 Azure Data Factory 매핑
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/03/2019
ms.openlocfilehash: 5cc54c95759ba1490f498305f05cc49a4411686d
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930324"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>데이터 흐름 조회 변환 Azure Data Factory 매핑

조회를 사용하여 다른 원본의 참조 데이터를 Data Flow에 추가합니다. 조회 변환에는 참조 테이블을 가리키고 키 필드가 일치하는 정의된 원본이 필요합니다.

![조회 변환](media/data-flow/lookup1.png "조회")

수신 스트림 필드와 참조 원본 필드 간의 일치에 사용할 키 필드를 선택합니다. Data Flow 디자인 캔버스에서 조회의 오른쪽 항목으로 사용할 새 원본을 먼저 만들어야 합니다.

일치 항목이 발견되면 참조 원본의 결과 행과 열이 데이터 흐름에 추가됩니다. Data Flow의 끝에서 싱크에 포함할 관심 필드를 선택할 수 있습니다. 또는 조회 후에 선택 변환을 사용 하 여 필드 목록을 정리 하 고 유지 하려는 두 스트림의 필드만 유지 합니다.

조회 변환은 왼쪽 우선 외부 조인과 동등한 기능을 수행 합니다. 따라서 왼쪽 소스의 모든 행이 오른쪽의 일치 항목과 결합 된 것을 볼 수 있습니다. 조회에 일치 하는 값이 여러 개 있거나 조회 식을 사용자 지정 하려는 경우 조인 변환으로 전환 하 고 크로스 조인을 사용 하는 것이 좋습니다. 이렇게 하면 실행 시 발생 가능한 카티션 곱을 피할 수 있습니다.

## <a name="match--no-match"></a>일치/일치 하지 않음

조회 변환 후에는 결과를 사용 하 여 각 일치 행의 결과를 검사할 수 있습니다. `isMatch()` 식 함수를 사용 하 여 조회로 인해 행이 일치 하는지 여부에 따라 논리를 추가로 선택할 수 있습니다.

![조회 패턴](media/data-flow/lookup111.png "조회 패턴")

조회 변환을 사용한 후 ```isMatch()``` 함수에 조건부 분할 변환 분할을 추가할 수 있습니다. 위의 예에서는 일치 하는 행이 위쪽 스트림을 통과 하 고 일치 하지 않는 행이 ```NoMatch``` 스트림을 통해 흐릅니다.

## <a name="first-or-last-value"></a>첫 번째 또는 마지막 값

조회와 일치 하는 항목이 여러 개 있는 경우 첫 번째 또는 마지막 일치 항목 중 하나를 선택 하 여 일치 하는 여러 행을 줄일 수 있습니다. 조회 후 집계 변환을 사용 하 여이 작업을 수행할 수 있습니다.

이 경우 ```PickFirst``` 라는 집계 변환을 사용 하 여 조회 일치 항목에서 첫 번째 값을 선택 합니다.

![조회 집계](media/data-flow/lookup333.png "조회 집계")

![먼저 조회](media/data-flow/lookup444.png "먼저 조회")

## <a name="optimizations"></a>최적화

Data Factory에서 데이터 흐름은 스케일 아웃 Spark 환경에서 실행 됩니다. 데이터 집합을 작업자 노드 메모리 공간에 맞출 수 있는 경우 조회 성능을 최적화할 수 있습니다.

![브로드캐스트 조인](media/data-flow/broadcast.png "브로드캐스트 참가")

### <a name="broadcast-join"></a>브로드캐스트 조인

왼쪽 및/또는 오른쪽 브로드캐스트 조인을 선택 하 여 ADF를 요청 하 여 조회 관계의 양쪽에 있는 전체 데이터 집합을 메모리에 푸시합니다. 작은 데이터 집합의 경우 조회 성능을 크게 향상 시킬 수 있습니다.

### <a name="data-partitioning"></a>데이터 분할

조회 변환의 최적화 탭에서 "분할 설정"을 선택 하 여 데이터 분할을 지정할 수도 있습니다 .이를 통해 작업자 당 메모리에 더 적합 한 데이터 집합을 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [조인](data-flow-join.md) 및 [존재](data-flow-exists.md) 변환은 ADF 매핑 데이터 흐름에서 유사한 작업을 수행 합니다. 다음으로 이러한 변환을 살펴보세요.
* ```isMatch()```와 [조건부 분할](data-flow-conditional-split.md) 을 사용 하 여 일치 하는 값 및 일치 하지 않는 값에 대한 행 분할
