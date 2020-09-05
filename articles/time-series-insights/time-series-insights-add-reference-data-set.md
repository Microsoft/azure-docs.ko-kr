---
title: 환경에 참조 데이터 집합을 추가 하는 방법-Azure Time Series Insights | Microsoft Docs
description: 이 문서에서는 Azure Time Series Insights 환경에서 참조 데이터 집합을 추가하여 데이터를 확장하는 방법을 설명합니다.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/12/2020
ms.custom: seodec18
ms.openlocfilehash: 57e99dfd0cec4ee396cc90173f1f22715c7ba760
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88168187"
---
# <a name="create-a-reference-data-set-for-your-azure-time-series-insights-gen1-environment-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Azure Time Series Insights Gen1 환경에 대 한 참조 데이터 집합 만들기

이 문서에서는 Azure Time Series Insights 환경에 참조 데이터 집합을 추가하는 방법을 설명합니다. 참조 데이터는 값을 확장하기 위해 원본 데이터에 연결하는 데 유용합니다.

참조 데이터 집합은 이벤트 원본의 이벤트로 확장된 항목의 컬렉션입니다. Azure Time Series Insights 수신 엔진은 이벤트 원본의 각 이벤트를 참조 데이터 집합의 해당 데이터 행과 조인 합니다. 이렇게 보강된 이벤트는 쿼리에 사용할 수 있습니다. 이 조인은 참조 데이터 집합에서 정의된 기본 키 열을 기준으로 합니다.

참조 데이터는 소급되어 조인되지 않습니다. 따라서 데이터를 구성 하 고 업로드 한 후에는 현재 및 이후의 수신 데이터만 일치 하 고 참조 날짜 집합에 조인 합니다.

## <a name="video"></a>비디오

### <a name="learn-about-time-series-insights-reference-data-modelbr"></a>시계열 정보에 대 한 참조 데이터 모델에 대해 알아봅니다.</br>

> [!VIDEO <https://www.youtube.com/embed/Z0NuWQUMv1o>]

## <a name="add-a-reference-data-set"></a>참조 데이터 집합 추가

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 기존 Azure Time Series Insights 환경을 찾습니다. Azure Portal의 왼쪽에 있는 메뉴에서 **모든 리소스**를 선택합니다. Azure Time Series Insights 환경을 선택 합니다.

1. **Overview** 페이지를 선택합니다. 페이지 맨 위 근처의 **Essentials** 섹션을 확장 하 여 **Time Series Insights 탐색기 URL** 을 찾고 링크를 엽니다.  

   [![Essentials 섹션 확장](media/add-reference-data-set/essentials.png)](media/add-reference-data-set/essentials.png#lightbox)

   Azure Time Series Insights 환경의 탐색기를 확인 합니다.

1. Azure Time Series Insights 탐색기에서 환경 선택기를 확장 합니다. 활성 환경을 선택합니다. 탐색기 페이지의 오른쪽 위에서 참조 데이터 아이콘을 선택 합니다.

   [![참조 데이터 추가](media/add-reference-data-set/tsi-select-environment-and-data-icons.png)](media/add-reference-data-set/tsi-select-environment-and-data-icons.png#lightbox)

1. **+ 데이터 집합 추가** 단추를 선택하여 새 데이터 집합 추가를 시작합니다.

   [![데이터 집합 추가](media/add-reference-data-set/tsi-add-a-reference-data-set.png)](media/add-reference-data-set/tsi-add-a-reference-data-set.png#lightbox)

1. **새 참조 데이터 집합** 페이지에서 데이터의 형식을 선택합니다.

   - 쉼표로 구분된 데이터에 대해 **CSV**를 선택합니다. 첫 번째 행은 머리글 행으로 처리됩니다.
   - JSON (JavaScript object notation) 형식의 데이터에 대해 **Json 배열을** 선택 합니다.

   [![데이터 형식을 선택합니다.](media/add-reference-data-set/tsi-select-data-upload-option.png)](media/add-reference-data-set/tsi-select-data-upload-option.png#lightbox)

1. 다음 두 가지 방법 중 하나를 사용하여 데이터를 제공합니다.

   - 데이터를 텍스트 편집기에 붙여 넣습니다. 그런 다음, **참조 데이터 구문 분석** 단추를 선택합니다.
   - **파일 선택** 단추를 선택하여 로컬 텍스트 파일의 데이터를 추가합니다.

   예를 들어 CSV 데이터 붙여넣기: [ ![ 붙여넣은 csv 데이터](media/add-reference-data-set/select-csv-and-enter-data.png)](media/add-reference-data-set/select-csv-and-enter-data.png#lightbox)

   예를 들어 JSON 배열 데이터 붙여넣기: [ ![ json 데이터 붙여넣기](media/add-reference-data-set/select-json-option-and-enter-data.png)](media/add-reference-data-set/select-json-option-and-enter-data.png#lightbox)

   데이터 값을 구문 분석하는 동안 오류가 발생하면 페이지 하단에 빨간색으로 오류가 표시됩니다(예: `CSV parsing error, no rows extracted`).

1. 데이터가 구문 분석되면 데이터 그리드에 데이터를 나타내는 열 및 행이 표시됩니다. 데이터 그리드가 정확한지 검토합니다.

   [![참조 데이터 검토](media/add-reference-data-set/review-displayed-data-grid.png)](media/add-reference-data-set/review-displayed-data-grid.png#lightbox)

1. 각 열을 검토 하 여 가정 된 데이터 형식을 이해 하 고 필요한 경우 데이터 형식을 변경 합니다.  **#** Double (숫자 데이터), T | 열 머리글에서 데이터 형식 기호를 선택 합니다 **. **부울의 경우 F, 문자열의 경우 **Abc**

   [![열 머리글에 대한 데이터 형식을 선택합니다.](media/add-reference-data-set/select-column-types.png)](media/add-reference-data-set/select-column-types.png#lightbox)

1. 필요한 경우 열 머리글의 이름을 바꿉니다. 해당 속성을 이벤트 원본에 조인하려면 키 열 이름이 필요합니다.

   > [!IMPORTANT]
   > 참조 데이터 키 열 이름이 들어오는 데이터의 이벤트 이름과 정확하게(대/소문자 포함) 일치하는지 확인합니다. 키가 아닌 열 이름은 해당 참조 데이터 값을 사용하여 들어오는 데이터를 확장하는 데 사용됩니다.

1. 필요에 따라 특정 행을 검토하려면 **행 필터링...** 필드에 값을 입력합니다. 필터는 데이터를 검토하는 데 유용하지만 데이터를 업로드할 때는 적용되지 않습니다.

1. 데이터 그리드 위의 **데이터 집합 이름** 필드에 입력하여 데이터 집합 이름을 지정합니다.

    [![데이터 집합 이름을 지정합니다.](media/add-reference-data-set/enter-reference-data-set-name.png)](media/add-reference-data-set/enter-reference-data-set-name.png#lightbox)

1. 데이터 그리드 위의 드롭다운을 선택하여 데이터 집합에 **기본 키** 열을 제공합니다.

    [![키 열을 선택합니다.](media/add-reference-data-set/select-primary-key-column.png)](media/add-reference-data-set/select-primary-key-column.png#lightbox)

    **(선택 사항)** **+** 보조 키 열을 복합 기본 키로 추가 하는 단추를 선택 합니다. 선택을 실행 취소해야 할 경우 드롭다운 목록에서 빈 값을 선택하여 보조 키를 제거합니다.

1. 데이터를 업로드하려면 **행 업로드** 단추를 선택합니다.

    [![행을 업로드 하 고 데이터를 확인 합니다.](media/add-reference-data-set/confirm-upload-reference-data.png)](media/add-reference-data-set/confirm-upload-reference-data.png#lightbox)

    이 페이지에서는 완료된 업로드를 확인하고 **데이터 세트가 업로드됨** 메시지를 표시합니다.

    > [!WARNING]
    > 참조 데이터 집합 사이에 공유 되는 열 또는 속성은 **중복 된 속성 이름** 업로드 오류를 표시 합니다. 오류가 발생 해도 참조 데이터 집합이 성공적으로 업로드 되는 것은 방지 되지 않습니다. 중복 된 속성 이름을 공유 하는 행을 결합 하 여 제거할 수 있습니다.

1. 필요에 따라 **행 추가**, **대량 행 가져오기**또는 **열 추가** 를 선택 하 여 참조 데이터 값을 추가 합니다.

    [![행을 추가 하거나 행을 대량으로 가져오거나 열을 추가 합니다.](media/add-reference-data-set/add-row-or-bulk-upload.png)](media/add-reference-data-set/add-row-or-bulk-upload.png#lightbox)

   > [!IMPORTANT]
   > 고유 키를 다른 행과 공유 하는 모든 행의 열은 해당 고유 키를 공유 하는 마지막으로 추가 된 행에 의해 재정의 됩니다.

   > [!NOTE]
   > 추가 된 행은 *사각형* 일 필요가 **없습니다** . 참조 데이터 집합의 다른 항목에 대 한 열이 더 적거나, 더 많거나, 달라질 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 프로그래밍 방식으로 [참조 데이터를 관리](time-series-insights-manage-reference-data-csharp.md)합니다.

- 전체 API 참조를 보려면 [참조 데이터 API](https://docs.microsoft.com/rest/api/time-series-insights/gen1-reference-data-api) 문서를 참조 하세요.
