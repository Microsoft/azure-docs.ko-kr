---
title: Azure Monitor 메트릭 차트 문제 해결
description: 만들기, 사용자 지정 또는 해석 메트릭 차트를 사용 하 여 문제 해결
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 73ef5cc00b5154dbdbc92911d17740c7d13038ec
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341974"
---
# <a name="troubleshooting-metrics-charts"></a>메트릭 차트를 문제 해결

만들기, 사용자 지정 또는 Azure 메트릭 탐색기의 차트를 해석 하는 문제를 실행 하는 경우이 문서를 사용 합니다. 메트릭을 처음 접하는 경우에 대해 알아봅니다 [메트릭 탐색기를 사용 하 여 시작](metrics-getting-started.md) 하 고 [메트릭 탐색기의 고급 기능](metrics-charts.md)합니다. 확인할 수도 있습니다 [예제](metric-chart-samples.md) 구성 된 메트릭 차트입니다.

## <a name="cant-find-your-resource-to-select-it"></a>선택 하 게 리소스를 찾을 수 없습니다.

클릭 합니다 **리소스를 선택** 단추 하지만 리소스 선택 대화 상자에서 리소스를 참조 하지 않습니다.

**해결 방법:** 메트릭 탐색기를 사용 하면 사용 가능한 리소스를 나열 하기 전에 구독 및 리소스 그룹을 선택 해야 합니다. 찾을 수 없으면 리소스:

1. 올바른 구독을 선택 했는지 확인 합니다 **구독** 드롭다운 합니다. 구독 나열 되지 않은 경우 클릭 합니다 **디렉터리 + 구독 설정** 를 리소스를 사용 하 여 구독을 추가 합니다.

1. 올바른 리소스 그룹을 선택 했는지 확인 합니다.
    > [!WARNING]
    > 메트릭 탐색기를 처음 열면 최상의 성능을 위해 합니다 **리소스 그룹** 드롭다운에 미리 선택된 된 리소스 그룹이 없습니다. 모든 리소스를 볼 수에 하나 이상의 그룹을 선택 해야 합니다.

## <a name="chart-shows-no-data"></a>차트 데이터를 보여 줍니다.

경우에 따라 차트 올바른 리소스 및 메트릭을 선택한 후 데이터를 표시할 수 있습니다. 이 동작은 일부의 원인에 의해 발생할 수 있습니다.

### <a name="microsoftinsights-resource-provider-isnt-registered-for-your-subscription"></a>Microsoft.Insights 리소스 공급자로 구독 등록 되지 않은

메트릭 탐색 필요 *Microsoft.Insights* 리소스 공급자 구독에 등록 합니다. 대부분의 경우에서이 자동으로 등록 (즉, 경고 규칙을 구성, 모든 리소스에 대 한 진단 설정을 사용자 지정 하거나 후 자동 크기 조정 규칙 구성). Microsoft.Insights 리소스 공급자로 등록 되지 않은 경우 수동으로 등록 해야 뒷부분에 나오는 단계에 따라 [Azure 리소스 공급자 및 형식](../../azure-resource-manager/resource-manager-supported-services.md)합니다.

**해결 방법:** 오픈 **구독**를 **리소스 공급자** 탭을 확인 하는 *Microsoft.Insights* 구독에 등록 됩니다.

### <a name="you-dont-have-sufficient-access-rights-to-your-resource"></a>리소스에 액세스 권한이 없습니다

Azure에서 메트릭에 액세스 하 여 제어 됩니다 [역할 기반 액세스 제어 (RBAC)](../../role-based-access-control/overview.md)합니다. 멤버 여야 합니다 [판독기 모니터링](../../role-based-access-control/built-in-roles.md#monitoring-reader)를 [참가자 모니터링](../../role-based-access-control/built-in-roles.md#monitoring-contributor), 또는 [참가자](../../role-based-access-control/built-in-roles.md#contributor) 모든 리소스에 대 한 메트릭을 탐색 하 합니다.

**해결 방법:** 메트릭을 탐색 하는 리소스에 대 한 충분 한 권한이 있는지 확인 합니다.

### <a name="your-resource-didnt-emit-metrics-during-the-selected-time-range"></a>리소스는 선택한 시간 범위 동안 메트릭을 내보내며 하지

일부 리소스는 해당 메트릭을 내보냅니다 지속적으로 하지 않습니다. 예를 들어, Azure는 중지 된 가상 컴퓨터에 대 한 메트릭을 수집 하지 않습니다. 일부 조건이 발생 하는 경우에 다른 리소스 메트릭의 내보낼 수 있습니다. 예를 들어, 트랜잭션 처리 시간을 보여 주는 메트릭이 하나 이상의 트랜잭션이 필요 합니다. 선택한 시간 범위에 트랜잭션이 없을 경우 차트 자연스럽 게 비어 있게 됩니다. 또한 Azure의 메트릭 대부분 1 분 마다 수집 되는 동안 몇 가지 덜 빈번 하 게 수집 됩니다. 메트릭 탐색 하려고 하는 방법에 대 한 자세한 내용을 보려면 메트릭 설명서를 참조 합니다.

**해결 방법:** 더 다양 한 차트의 시간을 변경 합니다. "지난 30 일"에서 시작할 수는 더 큰 시간 단위를 사용 하 여 (또는 "자동 시간 세분성" 옵션을 사용).

### <a name="you-picked-a-time-range-greater-than-30-days"></a>30 일 보다 큰 시간 범위를 선택 하면

[Azure에서 대부분의 메트릭이 93 일 동안 저장 됩니다](data-platform-metrics.md#retention-of-metrics)합니다. 그러나 개 이하의 30 일만 단일 차트에 데이터에 쿼리할 수 있습니다. 이 제한에 적용 되지 않습니다 [로그 기반 메트릭](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics)합니다.

**해결 방법:** 빈 차트를 보거나 차트에 메트릭 데이터의 일부분만 표시 하는 경우 간의 시작-끝-일과 시간 선택기의 차이 30 일 간격을 초과 하지 않는지 확인 합니다.

### <a name="all-metric-values-were-outside-of-the-locked-y-axis-range"></a>잠긴된 y 축 범위를 벗어난 된 모든 메트릭 값

하 여 [경계의 차트 y 축 잠금](metrics-charts.md#lock-boundaries-of-chart-y-axis), 차트 표시 영역 차트 선 표시 의도 하지 않게 가능 합니다. 예를 들어 y 축 0%에서 50% 사이의 범위에 잠겨 있고 메트릭을 100%의 상수 값, 줄은 항상 빈 상태로 표시 하는 차트를 만드는 표시 영역 바깥쪽 렌더링 됩니다.

**해결 방법:** 차트의 y 축 경계 메트릭 값의 범위를 벗어난 잠기지 않습니다 있는지 확인 합니다. Y 축 경계 잠겨 경우 일시적으로 다시 설정 값 없는 차트 범위를 벗어나는 메트릭을 확인 하는 것이 좋습니다. Y 축 범위 잠금을 사용 하 여 차트에 대 한 자동 세분성을 사용 하 여 권장 되지 않습니다 **합계**를 **min**, 및 **최대** 집계와 해당 값 변경 되므로 브라우저 창의 크기를 조정 하거나 다른 한 화면 해상도에서 이동 하 여 세분성입니다. 전환 세분성 차트 빈의 표시 영역을 남을 수 있습니다.

### <a name="you-are-looking-at-a-guest-os-metric-but-didnt-enable-azure-diagnostic-extension"></a>게스트 OS 메트릭 사용 한다고 해도 Azure 진단 확장을 사용 하지 않도록

컬렉션 **게스트 OS** 메트릭을 Azure 진단 확장을 구성 하거나 사용 하 여 사용 하도록 설정 해야 합니다 **진단 설정** 리소스에 대 한 패널입니다.

**해결 방법:** Azure 진단 확장을 사용할 수 있지만 사용자 메트릭을 볼 수 없는 경우에 설명 된 단계를 따릅니다 [Azure 진단 확장 문제 해결 가이드](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal)합니다. 에 대 한 문제 해결 단계를 참조 하세요. [게스트 OS 네임 스페이스 및 메트릭을 선택할 수 없습니다.](metrics-troubleshoot.md#cannot-pick-guest-os-namespace-and-metrics)

## <a name="error-retrieving-data-message-on-dashboard"></a>대시보드에서 "데이터 검색 중 오류 발생" 메시지

이 문제는 나중에 사용 되지 않으며 Azure에서 제거 하는 메트릭을 사용 하 여 대시보드를 만들 때 발생할 수 있습니다. 대/소문자 인지를 확인 하려면 엽니다는 **메트릭** 리소스 및 메트릭 선택기의 사용 가능한 메트릭 확인 탭. 메트릭이 표시 되지 않으면 Azure에서 메트릭을 제거 되었습니다. 일반적으로 메트릭은 사용 되지 경우 리소스 상태와 유사한 큐브 뷰를 사용 하 여 제공 하는 보다 잘 새 메트릭을 않습니다.

**해결 방법:** 대시보드에서 차트에 대 한 대체는 메트릭을 선택 하 여 실패 한 타일을 업데이트 합니다. 할 수 있습니다 [Azure 서비스에 대 한 사용 가능한 메트릭 목록을 검토](metrics-supported.md)합니다.

## <a name="chart-shows-dashed-line"></a>차트가 파선

Azure 메트릭 차트를는 누락 된 값 (라고도: "null 값") 알려진된 시간 두 조직 간의 데이터 요소 파선 스타일을 사용 합니다. 예를 들어 시간 선택기 "1minute" 시간 단위를 선택 하지만 07:26, 07시 27분에 보고 된 메트릭에 07시 29분-07:30 (두 번째 및 세 번째 데이터 요소 사이의 간격을 분 참고), 다음 07 분 27 초 및 07시 29분 파선으로 연결 하 고 실선 연결 다른 모든 데이터 요소입니다. 메트릭을 사용 하는 경우 0 파선 삭제 **개수** 하 고 **합계** 집계 합니다. 에 대 한는 **avg**, **min** 또는 **최대** 집계 파선은 두 개의 가장 가까운 알려진된 데이터 요소를 연결 합니다. 또한 차트의 맨 오른쪽 또는 맨 왼쪽 쪽에 데이터가 누락 되어 파선 누락 된 데이터 요소의 방향으로 확장 합니다.
  ![metric image](./media/metrics-troubleshoot/missing-data-point-line-chart.png)

**해결 방법:** 이 동작은 의도된 것입니다. 누락 된 데이터 요소를 식별 하는 데 유용 합니다. 꺾은선형 차트 고밀도 메트릭의 추세를 시각화에 대 한 뛰어난 선택 되었지만 시간 세분화를 사용 하 여 값 corelating 하는 것이 중요 한 경우에 특히 스파스 값을 사용 하 여 메트릭에 대 한 해석 하기 어려울 수 있습니다. 파선 쉽게 이러한 차트의 읽기용 하지만 차트 여전히 명확 하지 않습니다, 다른 차트 종류를 사용 하 여 메트릭을 볼 합니다. 예를 들어, 동일한 메트릭에 대해 분산 된 점도 차트를 명확 하 게 표시 하 여 각 시간 조직 값이 있는 경우 점을 시각화 하 고 데이터를 건너뛰어 지점 모두 값이 누락 된 경우에: ![메트릭 이미지](./media/metrics-troubleshoot/missing-data-point-scatter-chart.png)

   > [!NOTE]
   > 마우스 포인터의 위치에서 데이터 요소를 강조 표시 하 여 시간 세분성을 평가 메트릭을 대 한 꺾은선형 차트를 계속 하려는 경우 도움이 될 수 있습니다 차트 위로 마우스를 이동 합니다.

## <a name="chart-shows-unexpected-drop-in-values"></a>차트 값에 예기치 않은 삭제를 보여 줍니다.

대부분의 경우 메트릭 값의 인식된 드롭다운 이해 하 여 차트에 표시 되는 데이터의 경우 마지막 메트릭 데이터 포인트에 보내거나 받은 Azure에서 아직 처리 하지 않은 때문에 차트에서 가장 최근의 분을 표시 하는 경우 개수 또는 합계 떨어지는 결과가 잘못 될 수 있습니다. 서비스에 따라 몇 분 범위 내에서 처리 메트릭이 대기 시간 수 있습니다. 지난 몇 분 값의 드롭다운을 1 또는 5 분 단위로 사용 하 여 최근 시간 범위를 보여 주는 차트를 더 분명해 집니다. ![메트릭 이미지](./media/metrics-troubleshoot/drop-in-values.png)

**해결 방법:** 이 동작은 의도된 것입니다. 경우에 데이터를 수신 하는 즉시 데이터를 보여 주는 유용한 것을 믿습니다 *부분* 하거나 *불완전 한*합니다. 이렇게 중요 한 결론을 더 빨리를 만들고 즉시 조사를 시작할 수 있습니다. 예를 들어, 실패 한 횟수를 보여 주는 메트릭에 대해 X 부분 값을 표시 알려 된 최소 X 주어진된 1 분에 실패 합니다. 문제를 즉시 조사를 시작 하지 않고 수 있습니다이 분 중요 하지 않을에서 발생 한 오류의 정확한 개수를 확인 하기 위해 기다립니다. 차트는 데이터의 전체 집합 수신 되지만 해당 시점에도 표시 될 최신 분에서 새 불완전 한 데이터 요소에 업데이트 됩니다.

## <a name="cannot-pick-guest-os-namespace-and-metrics"></a>게스트 OS 네임 스페이스 및 메트릭을 선택할 수 없습니다.

가상 머신과 가상 머신 확장 집합에 두 가지 범주의 메트릭 **가상 컴퓨터 호스트** Azure 호스팅 환경에서 수집 되는 메트릭의 및 **게스트 OS** 에서 수집 되는 메트릭의 [모니터링 에이전트](agents-overview.md) 가상 머신에서 실행 합니다. 모니터링 에이전트를 사용 하 여 설치한 [Azure 진단 확장](diagnostics-extension-overview.md)합니다.

기본적으로 게스트 OS 메트릭에서 선택 된 Azure Storage 계정에 저장 합니다 **진단 설정** 리소스의 탭 합니다. 게스트 OS 메트릭 수집 되지 않거나 메트릭 탐색기에 액세스할 수 없으며만 표시 됩니다는 **가상 컴퓨터 호스트** 메트릭 네임 스페이스:

![메트릭 이미지](./media/metrics-troubleshoot/cannot-pick-guest-os-namespace.png)

**해결 방법:** 찾을 수 없으면 **게스트 OS** 네임 스페이스 및 메트릭 탐색기에서 메트릭:

1. 확인 [Azure 진단 확장](diagnostics-extension-overview.md) 을 활성화 하 고 메트릭을 수집 하도록 구성 합니다.
    > [!WARNING]
    > 사용할 수 없습니다 [Log Analytics 에이전트](agents-overview.md#log-analytics-agent) (Microsoft Monitoring Agent 또는 "MMA"이 라고도 함) 보내도록 **게스트 OS** 저장소 계정에 있습니다.

1. 저장소 계정의 방화벽에 의해 보호 되지 있는지 확인 합니다.

1. 사용 된 [Azure storage 탐색기](https://azure.microsoft.com/features/storage-explorer/) 메트릭은 저장소 계정으로 흐르는 지 유효성을 검사 하 합니다. 메트릭이 수집 되지 않습니다, 경우에 따라 합니다 [Azure 진단 확장 문제 해결 가이드](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal)합니다.

## <a name="next-steps"></a>다음 단계

* [메트릭 탐색기를 사용 하 여 시작 하는 방법에 대 한 설명](metrics-getting-started.md)
* [메트릭 탐색기의 고급 기능에 대해 알아보기](metrics-charts.md)
* [Azure 서비스에 대 한 사용 가능한 메트릭 목록을 보려면](metrics-supported.md)
* [구성 된 차트의 예를 참조 하세요.](metric-chart-samples.md)
