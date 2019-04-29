---
title: Azure Data Factory 데이터 흐름 매핑 검사 및 데이터 미리 보기
description: Azure Data Factory 데이터 흐름 매핑에는 데이터 흐름 메타데이터(검사) 및 디버그 모드(데이터 미리 보기)일 경우 데이터 미리 보기를 보여주는 창이 있습니다.
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 47cde50e0874f0f73523925b6d1b2f8ee4abaea9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61284109"
---
# <a name="azure-data-factory-mapping-data-flow-transformation-inspect-tab"></a>Azure Data Factory 데이터 흐름 매핑 변환 검사 탭

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![검사 창](media/data-flow/agg3.png "검사 창")

검사 창에는 변환 중인 데이터 스트림의 메타데이터에 대한 보기를 제공합니다. 열 수, 변경된 열, 추가된 열, 데이터 형식, 열 순서 및 열 참조를 확인할 수 있습니다. “검사”는 메타데이터의 읽기 전용 보기입니다. 검사 창에서 메타데이터를 확인하기 위해 디버그 모드를 사용하도록 설정할 필요가 없습니다.

변환을 통해 데이터의 셰이프를 변경함에 따라 메타데이터가 흐름을 변경하는 것을 검사 창을 통해 확인할 수 있습니다. 원본 변환에 정의된 스키마가 없는 경우 메타데이터는 검사 창에 표시되지 않습니다. 메타데이터가 부족한 것은 스키마 드리프트 시나리오에서 일반적입니다.

![데이터 미리 보기](media/data-flow/datapreview.png "데이터 미리 보기")

데이터 미리 보기는 데이터가 변환되는 동안 데이터의 읽기 전용 보기를 제공하는 창입니다. 변환의 출력 및 데이터 흐름의 유효성을 검사하는 식을 볼 수 있습니다. 데이터 미리 보기를 보려면 디버그 모드를 켜야 합니다. 데이터 미리 보기 그리드의 열을 클릭하면 후속 패널이 오른쪽에 표시됩니다. 팝 아웃 패널에는 사용자가 선택하는 각 열에 대한 프로필 통계가 표시됩니다.

![데이터 미리 보기 차트](media/data-flow/chart.png "데이터 미리 보기 차트")

