---
title: Azure Data Factory Mapping Data Flow 최적화 탭
description: 최적화 탭 파티션 설정을 사용하여 Azure Data Factory Mapping Data Flow 최적화
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 3802a8475d8a39a2f275dbc7fcf21ce69892a117
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61262291"
---
# <a name="mapping-data-flow-transformation-optimize-tab"></a>Mapping Data Flow 변환 최적화 탭

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

각 Data Flow 변환에는 “최적화” 탭이 있습니다. 최적화 탭에는 데이터 흐름의 분할 구성표를 구성하기 위한 선택적 설정이 포함되어 있습니다.

<img src="media/data-flow/opt001.png" width="800">

기본 설정은 “현재 분할 사용”입니다. 현재 분할에 따라 Azure Data Factory에서 Azure Databricks의 Spark에서 실행 중인 데이터 흐름에 고유한 분할 구성표를 사용합니다. 일반적으로 이 방법을 사용하는 것이 좋습니다.

그러나 분할을 조정하려는 경우가 있습니다. 예를 들어 Lake의 단일 파일에 변환을 출력하려는 경우 싱크 변환의 분할에 대해 최적화 탭의 “단일 파티션”을 선택합니다.

데이터 변환에 사용되는 분할 구성표를 제어하려는 또 다른 경우는 성능과 관련이 있습니다. 데이터 분할을 조정하면 전반적인 데이터 흐름 성능에 긍정적인 영향과 부정적인 영향을 모두 줄 수 있는 컴퓨팅 노드 간의 데이터 분산 및 데이터 국부성 최적화를 어느 정도 제어할 수 있습니다.

변환의 분할을 변경하려는 경우 최적화 탭을 클릭하고 “분할 설정” 라디오 단추를 선택하면 됩니다. 그러면 일련의 분할 옵션이 표시됩니다. 구현할 최상의 분할 방법은 데이터 볼륨, 후보 키, null 값 및 카디널리티에 따라 다릅니다. 기본 분할로 시작한 후 다른 분할 옵션을 시도하는 것이 좋습니다. 파이프라인의 디버그 실행을 사용하여 테스트한 다음, 모니터링 보기에서 파티션 사용량과 각 변환 그룹에 사용된 시간을 볼 수 있습니다.

<img src="media/data-flow/opt002.png" width="600">

### <a name="round-robin"></a>라운드 로빈

라운드 로빈은 파티션 간에 데이터를 자동으로 균등 분산하는 간단한 파티션입니다. 견고한 스마트 분할 전략을 구현하기 위한 적절한 키 후보가 없는 경우 라운드 로빈을 사용합니다. 물리적 파티션 수를 설정할 수 있습니다.

### <a name="hash"></a>해시

Azure Data Factory는 유사한 값을 가진 행이 동일한 파티션에 속하도록 균일한 파티션을 생성하기 위해 열 해시를 생성합니다. 해시 옵션을 사용하는 경우 가능한 파티션 기울이기를 테스트합니다. 물리적 파티션 수를 설정할 수 있습니다.

### <a name="dynamic-range"></a>동적 범위

동적 범위는 제공한 열 또는 식을 기반으로 하여 Spark 동적 범위를 사용합니다. 물리적 파티션 수를 설정할 수 있습니다. 

### <a name="fixed-range"></a>고정 범위

분할된 데이터 열의 값에 대해 고정 범위를 제공하는 식을 작성해야 합니다. 파티션 기울이기를 방지하기 위해 이 옵션을 사용하기 전에 데이터를 잘 파악해야 합니다. 식에 대해 입력한 값이 파티션 함수의 일부로 사용됩니다. 물리적 파티션 수를 설정할 수 있습니다.

### <a name="key"></a>키

데이터의 카디널리티를 잘 알고 있는 경우 키 분할은 좋은 파티션 전략이 될 수 있습니다. 키 분할은 열의 각 고유 값에 대한 파티션을 만듭니다. 숫자가 데이터의 고유 값을 기반으로 하기 때문에 파티션 수를 설정할 수 없습니다.
