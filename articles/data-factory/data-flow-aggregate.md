---
title: Azure Data Factory Mapping Data Flow 집계 변환
description: Azure Data Factory Data Flow 집계 변환
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 7b488b243c0520befb6b5470598f460b5a759fed
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61467380"
---
# <a name="azure-data-factory-mapping-data-flow-aggregate-transformation"></a>Azure Data Factory Mapping Data Flow 집계 변환

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

집계 변환에서는 데이터 스트림의 열 집계를 정의합니다. 식 작성기에서 여러 유형의 집계(SUM, MIN, MAX, COUNT 등)를 정의하고 선택적 group-by 필드를 사용한 이러한 집계를 포함하는 새 필드를 출력에 만들 수 있습니다.

![집계 변환 옵션](media/data-flow/agg.png "집계 1")

## <a name="group-by"></a>그룹화 기준
(선택 사항) 집계에 대한 Group-by 절을 선택하고 기존 열의 이름이나 새 이름을 사용합니다. “열 추가”를 사용하여 group-by 절을 더 추가하고 열 이름 옆에 있는 텍스트 상자를 클릭하여 식 작성기를 시작한 다음, 기존 열, 열 조합 또는 식을 그룹화에 대해 선택합니다.

## <a name="the-aggregate-column-tab"></a>집계 열 탭 
(필수) 집계 열 탭을 선택하여 집계 식을 작성합니다. 기존 열을 선택하여 값을 집계로 덮어쓰거나, 집계에 사용할 새 필드를 새 이름으로 만들 수 있습니다. 집계에 사용할 식은 열 이름 선택기 옆에 있는 오른쪽 상자에 입력됩니다. 해당 텍스트 상자를 클릭하면 식 작성기가 열립니다.

![집계 변환 옵션](media/data-flow/agg2.png "집계")

## <a name="data-preview-in-expression-builder"></a>식 작성기의 데이터 미리 보기

디버그 모드의 식 작성기에서는 집계 함수로 데이터 미리 보기를 생성할 수 없습니다. 집계 변환에 대한 데이터 미리 보기를 보려면 식 작성기를 닫고 데이터 흐름 디자이너의 데이터 프로필을 확인합니다.
