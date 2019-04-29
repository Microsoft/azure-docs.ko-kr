---
title: Azure Data Factory Mapping Data Flow 선택 변환
description: Azure Data Factory Mapping Data Flow 선택 변환
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: bc83b41067d587adce41658a2c4b3d68969750ba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61364484"
---
# <a name="azure-data-factory-mapping-data-flow-select-transformation"></a>Azure Data Factory Mapping Data Flow 선택 변환

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

열 선택도(열 수 줄이기) 또는 열과 스트림 이름의 별칭 지정에 이 변환을 사용합니다.

선택 변환을 사용하면 전체 스트림이나 해당 스트림의 열에 별칭을 지정하고, 다른 이름(별칭)을 할당한 다음, 데이터 흐름의 뒷부분에서 새 이름을 참조할 수 있습니다. 이 변환은 셀프 조인 시나리오에 유용합니다. ADF Data Flow에서 셀프 조인을 구현하는 방법은 스트림을 사용하고, “새 분기”로 분기한 다음, 바로 뒤에 “선택” 변환을 추가하는 것입니다. 이제 해당 스트림에 새 이름이 지정되며, 이 이름을 사용하여 원래 스트림에 다시 조인함으로써 셀프 조인을 만들 수 있습니다.

![셀프 조인](media/data-flow/selfjoin.png "셀프 조인")

위의 다이어그램에서 선택 변환은 맨 위에 있습니다. 이 변환은 원래 스트림의 별칭을 “OrigSourceBatting”으로 지정합니다. 아래 강조 표시된 조인 변환에서 이 선택 스트림 별칭 지정을 오른쪽 조인으로 사용하여 내부 조인의 왼쪽 및 오른쪽에서 동일한 키를 참조할 수 있음을 확인할 수 있습니다.

데이터 흐름에서 열을 선택 취소하는 방법으로 선택을 사용할 수도 있습니다. 예를 들어 싱크에 6개 열이 정의되어 있지만 변환할 특정 열 3개만 선택하고 싱크로 진행하는 경우 선택 변환을 사용하여 해당 3개만 선택할 수 있습니다.

> [!NOTE]
> 특정 열만 선택하려면 “모두 선택”을 꺼야 합니다.

옵션

“선택”의 기본 설정은 모든 수신 열을 포함하고 원래 이름을 유지하는 것입니다. 선택 변환의 이름을 설정하여 스트림의 별칭을 지정할 수 있습니다.

개별 열의 별칭을 지정하려면 “모두 선택”을 선택 취소하고 맨 아래에 있는 열 매핑을 사용합니다.

![선택 변환](media/data-flow/select001.png "선택 별칭")
