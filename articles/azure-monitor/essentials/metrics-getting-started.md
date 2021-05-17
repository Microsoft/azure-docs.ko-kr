---
title: Azure 메트릭 탐색기 시작
description: Azure 메트릭 탐색기를 사용하여 처음으로 메트릭 차트를 만드는 방법을 알아봅니다.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: vitalyg
ms.openlocfilehash: df745e7612dbd5b5bb9029b89d7f74974270c2d1
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105962716"
---
# <a name="getting-started-with-azure-metrics-explorer"></a>Azure 메트릭 탐색기 시작

## <a name="where-do-i-start"></a>어디에서 시작할 수 있나요?
Azure Monitor 메트릭 탐색기는 Microsoft Azure Portal의 구성 요소이며 차트를 그리고, 추세의 상관 관계를 시각적으로 지정하고, 메트릭 값에서 급증 및 하락을 조사할 수 있습니다. 메트릭 탐색기를 사용하여 리소스의 상태 및 사용률을 조사합니다. 다음 순서로 시작합니다.

1. [리소스 및 메트릭을 선택](#create-your-first-metric-chart) 하면 기본 차트가 표시됩니다. 그런 다음 조사와 관련된 [시간 범위를 선택](#select-a-time-range)합니다.

1. [차원 필터 및 분할을 적용](#apply-dimension-filters-and-splitting)해보세요. 필터 및 분할을 사용하면 전체 메트릭 값에 기여하는 메트릭의 세그먼트를 분석하고 가능한 이상 값을 식별할 수 있습니다.

1. [고급 설정](#advanced-chart-settings)을 사용하여 대시보드에 고정하기 전에 차트를 사용자 지정할 수 있습니다. 메트릭 값이 임계값보다 크거나 떨어지면 알림을 받도록 [경고를 구성](../alerts/alerts-metric-overview.md)합니다.

## <a name="create-your-first-metric-chart"></a>첫 번째 메트릭 차트 만들기

메트릭 차트를 만들려면 리소스, 리소스 그룹, 구독 또는 Azure Monitor 보기에서 **메트릭** 탭을 열고 다음 단계를 따릅니다.

1. "범위 선택" 단추를 클릭하여 리소스 범위 선택기를 엽니다. 이렇게 하면 메트릭을 보려는 리소스를 선택할 수 있습니다. 리소스의 메뉴에서 메트릭 탐색기를 연 경우 범위가 이미 채워져 있어야 합니다. 여러 리소스에서 메트릭을 보는 방법을 알아보려면 [이 문서를 참조](./metrics-dynamic-scope.md)하세요.
    > ![리소스 선택](./media/metrics-getting-started/scope-picker.png)

2. 일부 리소스의 경우 네임스페이스를 선택해야 합니다. 네임스페이스는 메트릭을 쉽게 찾을 수 있도록 구성하는 방법일 뿐입니다. 예를 들어 스토리지 계정에는 파일, 테이블, Blob 및 큐 메트릭을 저장하기 위한 별도의 네임스페이스가 있습니다. 여러 리소스 유형 중 대부분은 네임스페이스가 하나만 있습니다.

3. 사용 가능한 메트릭 목록에서 메트릭을 선택합니다.

    > ![메트릭 선택](./media/metrics-getting-started/metrics-dropdown.png)

4. 필요에 따라 [메트릭 집계를 변경](../essentials/metrics-charts.md#aggregation)할 수 있습니다. 예를 들어 메트릭에 대한 최소값, 최대값 또는 평균 값을 차트에 표시할 수 있습니다.

> [!TIP]
> 동일한 차트에 여러 메트릭을 표시하려면 **메트릭 추가** 단추를 사용하여 이 단계를 반복합니다. 한 뷰에서 여러 차트를 보려면 상단의 **차트 추가** 단추를 선택합니다.

## <a name="select-a-time-range"></a>시간 범위 선택

> [!WARNING]
> [Azure의 메트릭은 대부분 93일 동안 저장됩니다](../essentials/data-platform-metrics.md#retention-of-metrics). 그러나 단일 차트에서는 30일 이하의 데이터만 쿼리할 수 있습니다. 차트를 [이동](metrics-charts.md#pan)하여 전체 보존을 볼 수 있습니다. 30일 제한은 [로그 기반 메트릭](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics)에는 적용되지 않습니다.

기본적으로 차트는 최근 24시간 동안의 메트릭 데이터를 표시합니다. **시간 선택기** 패널을 사용하여 시간 범위를 변경하거나, 차트를 확대 또는 축소합니다. 

![시간 범위 변경 패널](./media/metrics-getting-started/time.png)

> [!TIP]
> **시간 브러시** 를 사용하여 차트의 흥미로운 영역(급증 또는 급락)을 조사할 수 있습니다. 영역의 시작 부분에 마우스 포인터를 놓고 마우스 왼쪽 단추를 누른 채 영역의 반대쪽으로 끈 후 단추를 놓습니다. 그러면 해당 시간 범위에서 차트가 확대됩니다. 

## <a name="apply-dimension-filters-and-splitting"></a>차원 필터 및 분할 적용

[필터링](../essentials/metrics-charts.md#filters) 및 [분할](../essentials/metrics-charts.md#apply-splitting)은 차원이 있는 메트릭에 대한 강력한 진단 도구입니다. 이러한 기능은 다양한 메트릭 세그먼트("차원 값")가 메트릭의 전체 값에 미치는 영향을 보여주고 가능한 이상 값을 식별할 수 있도록 합니다.

- **필터링** 을 사용하여 차트에 포함할 차원 값을 선택할 수 있습니다. 예를 들어 *서버 응답 시간* 메트릭을 차트에 표시할 때 성공한 요청을 표시할 수 있습니다. *요청 성공* 차원에 필터를 적용해야 합니다. 

- **분할** 은 차원의 각 값에 대한 별도의 줄을 차트에 표시할 것인지 아니면 값을 한 줄로 집계할 것인지 여부를 제어합니다. 예를 들어 모든 서버 인스턴스의 평균 응답 시간을 한 줄로 표시하거나 서버마다 별도의 줄을 표시할 수 있습니다. 별도의 줄을 보려면 *서버 인스턴스* 차원에 분할을 적용해야 합니다.

필터링 및 분할이 적용된 차트는 [차트 예제](../essentials/metric-chart-samples.md)를 참조하세요. 이 문서에서는 차트를 구성하는 데 사용된 단계를 보여줍니다.

## <a name="share-your-metric-chart"></a>메트릭 차트 공유
현재 메트릭 차트를 공유하는 방법에는 두 가지가 있습니다. 다음은 Excel 및 링크를 통해 메트릭 차트의 정보를 공유하는 방법에 대한 지침입니다.
 
### <a name="download-to-excel"></a>Excel로 다운로드
"공유"를 클릭하고 "Excel로 다운로드"를 선택합니다. 다운로드가 즉시 시작됩니다.

![Excel을 통해 메트릭 차트를 공유하는 방법을 보여주는 스크린샷](./media/metrics-getting-started/share-excel.png)

### <a name="share-a-link"></a>링크 공유
"공유"를 클릭하고 "링크 복사"를 선택합니다. 링크가 복사되었다는 알림이 표시됩니다.

![링크를 통해 메트릭 차트를 공유하는 방법을 보여주는 스크린샷](./media/metrics-getting-started/share-link.png)


## <a name="advanced-chart-settings"></a>고급 차트 설정

차트 스타일 및 제목을 사용자 지정하고 고급 차트 설정을 수정할 수 있습니다. 사용자 지정을 마쳤으면 대시보드에 고정하여 작업을 저장합니다. 메트릭 경고를 구성할 수도 있습니다. Azure Monitor 메트릭 탐색기의 이러한 기능과 기타 고급 기능에 대한 자세한 내용은 [제품 설명서](../essentials/metrics-charts.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [메트릭 탐색기의 고급 기능에 대한 자세한 정보](../essentials/metrics-charts.md)
* [메트릭 탐색기에서 여러 리소스 보기](./metrics-dynamic-scope.md)
* [메트릭 탐색기 문제 해결](metrics-troubleshoot.md)
* [Azure 서비스에 사용 가능한 메트릭 목록 보기](./metrics-supported.md)
* [구성된 차트 예제 보기](../essentials/metric-chart-samples.md)
