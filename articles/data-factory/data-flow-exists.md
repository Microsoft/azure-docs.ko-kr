---
title: Azure Data Factory Mapping Data Flow 있음 변환
description: Azure Data Factory Mapping Data Flow 있음 변환
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 6ce27ba699ae766ed4d2428f67d91379464bb9f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730987"
---
# <a name="azure-data-factory-mapping-data-flow-exists-transformation"></a>Azure Data Factory Mapping Data Flow 있음 변환

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

있음 변환은 데이터의 행을 중지하거나 통과 허용하는 행 필터링 변환입니다. 있음 변환은 ```SQL WHERE EXISTS``` 및 ```SQL WHERE NOT EXISTS```와 비슷합니다. 필터 변환 후 데이터 스트림의 결과 행에는 원본 1의 열 값이 원본 2에 있거나 원본 2에 없는 모든 행이 포함됩니다.

![있음 설정](media/data-flow/exsits.png "있음 1")

Data Flow가 스트림 1의 값을 스트림 2와 비교할 수 있도록 있음의 두 번째 원본을 선택합니다.

있음 또는 없음에 대해 값을 확인하려는 원본 1과 원본 2의 열을 선택합니다.

## <a name="multiple-exists-conditions"></a>여러 조건을 존재합니다

Exsits에 대 한 열 조건에서 각 행에 옆에 있는 것을 + 도달 행을 마우스로 가리킬 때 기호를 사용할 수 있습니다. 이렇게 하면 Exists 조건에 대 한 여러 행을 추가할 수 있습니다.

## <a name="next-steps"></a>다음 단계

