---
title: Azure Data Factory Mapping Data Flow 시각적 모니터링
description: Azure Data Factory 데이터 흐름을 시각적으로 모니터링하는 방법
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 90aa6261aebb9d1f7da89c101854bad8061dd6ff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61269048"
---
# <a name="monitor-data-flows"></a>데이터 흐름 모니터링

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

데이터 흐름 빌드 및 디버그를 완료한 후에는 파이프라인의 컨텍스트 내에서 일정에 따라 실행되도록 데이터 흐름을 예약하는 것이 좋습니다. 트리거를 사용하여 Azure Data Factory에서 파이프라인을 예약할 수 있습니다. 또는 Azure Data Factory 파이프라인 작성기에서 지금 트리거 옵션을 사용하여 단일 실행을 수행하여 파이프라인 컨텍스트 내에서 데이터 흐름을 테스트할 수 있습니다.

파이프라인을 실행할 때 Data Flow 작업을 비롯하여 파이프라인에 포함된 모든 작업과 파이프라인을 모니터링할 수 있습니다. 왼쪽 Azure Data Factory UI 패널에서 모니터 아이콘을 클릭합니다. 아래와 비슷한 화면이 표시됩니다. 강조 표시된 아이콘을 사용하면 Data Flow 작업을 비롯한 파이프라인의 작업을 드릴할 수 있습니다.

<img src="media/data-flow/mon001.png" width="800">

실행 시간 및 상태를 포함하여 이 수준의 통계도 표시됩니다. 작업 수준의 실행 ID는 파이프라인 수준의 실행 ID와 다릅니다. 이전 수준의 실행 ID는 파이프라인의 실행 ID입니다. 안경을 클릭하면 데이터 흐름 실행에 대한 세부 정보가 제공됩니다.

<img src="media/data-flow/mon002.png" width="800">

그래픽 노드 모니터링 보기에 있는 경우 데이터 흐름 그래프의 간소화된 보기 전용 버전이 표시됩니다.

<img src="media/data-flow/mon003.png" width="800">

## <a name="view-data-flow-execution-plans"></a>데이터 흐름 실행 플랜 보기

데이터 흐름이 Databricks에서 실행되는 경우 Azure Data Factory는 전체 데이터 흐름에 따라 최적 코드 경로를 결정합니다. 또한 실행 경로는 여러 스케일 아웃 노드 및 데이터 파티션에서 발생할 수 있습니다. 따라서 모니터링 그래프는 변환의 실행 경로를 고려하여 흐름의 디자인을 나타냅니다. 개별 노드를 클릭하면 클러스터에서 함께 실행된 코드를 나타내는 “그룹화”가 표시됩니다. 표시되는 타이밍 및 개수는 디자인의 개별 단계와는 반대로 해당 그룹을 나타냅니다.

<img src="media/data-flow/mon004.png" width="800"> 

* 모니터링 창에서 열린 공간을 클릭하면 맨 아래 창의 통계에 변환 계보에 대한 싱크 데이터가 발생한 변환과 각 싱크의 타이밍 및 행 개수가 표시됩니다.

* 개별 변환을 선택하면 파티션 통계, 열 개수, 왜곡도(파티션 간에 데이터가 균일하게 분산된 정도) 및 첨도(데이터 급증 정도)를 보여 주는 추가 피드백이 오른쪽 패널에 수신됩니다.

* 노드 보기에서 싱크를 클릭하면 열 계보가 표시됩니다. 데이터 흐름 전체의 열이 누적되어 싱크에 배치되는 세 가지 방법이 있습니다. 아래에 이 계정과 키의 예제가 나와 있습니다.

  * 계산됨: 조건부 처리를 위해 또는 데이터 흐름의 식 내에서 열을 사용하지만 싱크에 배치하지 않습니다.
  * 파생됨: 열이 흐름에서 생성된 새 열입니다. 즉, 열이 원본에 없습니다.
  * 매핑됨: 열이 원본에서 시작되고 싱크 필드에 매핑됩니다.
  
## <a name="monitor-icons"></a>모니터 아이콘

이 아이콘은 변환 데이터가 클러스터에 이미 캐시되어 타이밍 및 실행 경로에서 고려되었음을 의미합니다.

<img src="media/data-flow/mon005.png" width="800"> 

변환에 녹색 원 아이콘도 표시됩니다. 이 아이콘은 데이터가 이동되는 싱크 수를 나타냅니다.
