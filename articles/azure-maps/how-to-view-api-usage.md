---
title: Azure Maps API 사용량을 보는 방법 | Microsoft Docs
description: 포털에서 Azure Maps API 호출에 대한 메트릭을 보는 방법을 알아봅니다.
author: dsk-2015
ms.author: dkshir
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: d14088ed940ab83be29756a26f8612704bb9aebd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60770153"
---
# <a name="view-azure-maps-api-usage"></a>Azure Maps API 사용량 보기

이 문서에서는 [포털](https://portal.azure.com)에서 Azure Maps 계정에 대한 API 사용량 메트릭을 보는 방법에 대해 설명합니다. 메트릭은 사용자 지정 가능한 기간에 따라 편리한 그래프 형식으로 표시됩니다.

## <a name="view-metric-snapshot"></a>메트릭 스냅숏 보기

Maps 계정의 **개요** 페이지에서 몇 가지 일반적인 메트릭을 볼 수 있습니다. 현재 선택할 수 있는 기간 동안의 *총 요청 수*, *총 오류 수* 및 *가용성*이 표시됩니다.

![Azure Maps 메트릭 개요](media/how-to-view-api-usage/portal-overview.png)

특정 분석을 위해 이러한 그래프를 사용자 지정해야 하는 경우 다음 섹션으로 계속 진행하세요.

## <a name="view-detailed-metrics"></a>자세한 메트릭 보기

1. [포털](https://portal.azure.com)에서 Azure 구독에 로그인합니다.

2. 왼쪽에 있는 **모든 리소스** 메뉴 항목을 클릭하고, *Azure Maps 계정*으로 이동합니다.

3. Maps 계정이 열리면 왼쪽의 **메트릭** 메뉴를 클릭합니다.

4. **메트릭** 창에서 다음 중 하나를 선택합니다.

   1. **가용성** - 일정 기간 동안의 API 가용성에 대한 *평균*을 보여 줍니다.
   2. **사용량** - 계정에 대한 사용 *개수*를 보여 줍니다.

      ![Azure Maps 메트릭 창](media/how-to-view-api-usage/portal-metrics.png)

5. 다음으로, **지난 24시간(자동)** 을 클릭하여 *시간 범위*를 선택할 수 있습니다. 기본적으로 시간 범위는 24시간으로 설정됩니다. 클릭하면 선택 가능한 시간 범위가 모두 표시됩니다. *시간 단위*를 선택하고, 동일한 드롭다운에서 시간을 *로컬* 또는 *GMT*로 표시하도록 선택할 수 있습니다. **적용**을 클릭합니다.

    ![Azure Maps 메트릭 시간 범위](media/how-to-view-api-usage/time-range.png)

6. 메트릭이 추가되면 해당 메트릭과 관련된 속성 중에서 **필터 추가**를 수행한 다음, 그래프를 표시할 속성 값을 선택할 수 있습니다.

    ![Azure Maps 메트릭 필터](media/how-to-view-api-usage/filter.png)

7. 선택한 메트릭 속성에 따라 메트릭에 대한 **분할**을 적용할 수도 있습니다. 이렇게 하면 그래프가 해당 속성의 각 값에 대해 하나씩 여러 그래프로 분할될 수 있습니다. 다음 그림에서 각 그래프의 색은 그래프 아래쪽에 표시된 속성 값에 해당합니다.

    ![Azure Maps 메트릭 분할](media/how-to-view-api-usage/splitting.png)

8. 동일한 그래프에서 위쪽의 **메트릭 추가** 단추를 클릭하기만 하면 여러 메트릭을 관찰할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

사용량을 추적할 대상이 되는 Azure Maps API에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Maps REST API 설명서](https://docs.microsoft.com/rest/api/maps)에서 사용 중인 API에 대해 자세히 참조하세요.
