---
title: 집계 변환 매핑 데이터 흐름에서-Azure Data Factory | Microsoft Docs
description: 매핑 데이터 흐름 집계 변환 사용 하 여 Azure Data Factory에서 대규모로 데이터를 집계 하는 방법에 알아봅니다.
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 21135b26d4bc840b3fcb091e675e5e6bd24d8548
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312096"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>집계 변환에서 데이터 흐름 매핑 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

집계 변환에서는 데이터 스트림의 열 집계를 정의합니다. 식 작성기를 사용 하 여 다양 한 유형의 SUM, MIN, MAX 및 기존 또는 계산 열을 기준으로 그룹화 할 수 있습니다 수와 같은 집계를 정의할 수 있습니다.

## <a name="group-by"></a>Group By
기존 열을 선택 하거나 by 절에 집계에 대 한 그룹으로 사용할 새 계산된 열을 만듭니다. 기존 열을 사용 하려면 드롭다운 목록에서 원하는 열을 선택 합니다. 새 계산된 열을 만들려면 절 위에 놓고 '계산 열'를 클릭 합니다. 가 열립니다는 [데이터 흐름 식 작성기](concepts-data-flow-expression-builder.md)합니다. 계산된 열을 만든 후에 '이름으로 ' 필드에서 출력 열 이름을 입력 합니다. By 절 추가 그룹을 추가 하려는 경우 기존 절 마우스로 및 클릭 '+'.

![집계 변환 그룹 설정에 의해](media/data-flow/agg.png "설정에 따라 집계 변환 그룹")

> [!NOTE]
> group by 절에 집계 변환을 선택 사항입니다.

## <a name="aggregate-column"></a>집계 열 
집계 식을 작성 하기 위해 '집계' 탭을 선택 합니다. 기존 열을 선택 하 고 집계를 사용 하 여 값을 덮어쓰도록 하거나 새 이름으로 새 필드를 만듭니다. 집계 식 열 이름이 선택기 옆에 있는 오른쪽 상자에 입력 됩니다. 식을 편집 하려면 식 작성기를 열어 텍스트 상자를 클릭 합니다. 새 집계 열을 만들려면 '+'를 클릭 하 여 기존 식 위로 마우스를 가져가고 추가 집계를 추가 하거나 [열 패턴](concepts-data-flow-column-pattern.md)합니다.

![집계 변환 집계 설정을](media/data-flow/agg2.png "집계 변환 집계 설정")

> [!NOTE]
> 각 집계 식에 집계 함수가 하나 이상 있어야 합니다.

> [!NOTE]
> 디버그 모드에서 식 작성기는 집계 함수를 사용 하 여 데이터 미리 보기를 생성할 수 없습니다. 집계 변환에 대 한 데이터 미리 보기를 보려면 식 작성기를 닫고 ' 데이터 미리 보기 ' 탭을 통해 데이터를 확인 합니다.

## <a name="next-steps"></a>다음 단계

-기간에 따른 집계를 사용 하 여 정의 된 [창 변환](data-flow-window.md)