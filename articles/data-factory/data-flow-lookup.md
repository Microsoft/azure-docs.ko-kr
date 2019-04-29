---
title: Azure Data Factory Mapping Data Flow 조회 변환
description: Azure Data Factory Mapping Data Flow 조회 변환
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: ef473ea5f88b9108894787785fe1e9083fab1b0a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61348178"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Azure Data Factory Mapping Data Flow 조회 변환

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

조회를 사용하여 다른 원본의 참조 데이터를 Data Flow에 추가합니다. 조회 변환에는 참조 테이블을 가리키고 키 필드가 일치하는 정의된 원본이 필요합니다.

![조회 변환](media/data-flow/lookup1.png "조회")

수신 스트림 필드와 참조 원본 필드 간의 일치에 사용할 키 필드를 선택합니다. Data Flow 디자인 캔버스에서 조회의 오른쪽 항목으로 사용할 새 원본을 먼저 만들어야 합니다.

일치 항목이 발견되면 참조 원본의 결과 행과 열이 데이터 흐름에 추가됩니다. Data Flow의 끝에서 싱크에 포함할 관심 필드를 선택할 수 있습니다.

## <a name="optimizations"></a>최적화

Data Factory에서 스케일 아웃 Spark 환경에서 실행 데이터 흐름입니다. 데이터 집합은 작업자 노드 메모리 공간에 맞출 수 있습니다, 조회 성능을 최적화할 수 있습니다.

![조인 브로드캐스트](media/data-flow/broadcast.png "브로드캐스트 조인")

### <a name="broadcast-join"></a>브로드캐스트 조인

왼쪽 선택 및/또는 오른쪽에 있는 브로드캐스트 조인 메모리를 조회 관계의 양쪽에서 전체 데이터 집합을 푸시하는 ADF를 요청 하려면

### <a name="data-partitioning"></a>데이터 분할

"설정 분할" 최적화 탭에서 조회 변환의 작업자 당 메모리에 더 잘 맞출 수 있는 데이터 집합을 선택 하 여 데이터의 분할을 지정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[조인](data-flow-join.md) 하 고 [Exists](data-flow-exists.md) 변환 ADF 매핑 데이터 흐름에서 유사한 작업을 수행 합니다. 이러한 변환 살펴보고 다음에 수행 합니다.
