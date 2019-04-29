---
title: Azure Monitor 리소스 그룹 인사이트 | Microsoft Docs
description: Azure Monitor를 사용하여 리소스 그룹 수준에서 분산 애플리케이션 및 서비스의 상태 및 성능 이해
services: azure-monitor
author: NumberByColors
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.author: daviste
ms.openlocfilehash: d5c07e0d4aca8bda42ea9f78a1475ea7bb5861f0
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119424"
---
# <a name="monitor-resource-groups-with-azure-monitor-preview"></a>Azure Monitor(미리 보기)를 사용하여 리소스 그룹 모니터링

최신 애플리케이션은 서비스를 전달하기 위해 여러 개별 부분이 함께 작동하므로 매우 복잡하고 고도로 분산된 경우가 많습니다. 이러한 복잡성을 인식하고 있으므로 Azure Monitor는 리소스 그룹에 대한 모니터링 인사이트를 제공합니다. 이를 통해 리소스 그룹&mdash;및 애플리케이션의&mdash;상태 및 성능과 관련된 컨텍스트를 전체적으로 제공하면서 개별 리소스에 발생하는 문제를 쉽게 심사하고 전달할 수 있습니다.

## <a name="access-insights-for-resource-groups"></a>리소스 그룹에 대한 액세스 인사이트

1. 왼쪽 탐색 모음에서 **리소스 그룹**을 선택합니다.
2. 탐색하려는 리소스 그룹 중 하나를 선택합니다. (많은 수의 리소스 그룹이 있는 경우 구독별로 필터링하는 것이 경우에 따라 유용할 수 있습니다.)
3. 리소스 그룹에 대한 인사이트에 액세스하려면 모든 리소스 그룹의 왼쪽 메뉴에서 **인사이트**를 클릭합니다.

![리소스 그룹 인사이트 개요 페이지 스크린샷](./media/resource-group-insights/0001-overview.png)

## <a name="resources-with-active-alerts-and-health-issues"></a>활성 경고 및 상태 문제가 있는 리소스

개요 페이지에는 발생한 경고 횟수와 여전히 활성 상태인 경고 수가 각 리소스의 현재 Azure Resource Health와 함께 표시됩니다. 이러한 정보는 문제가 발생한 리소스를 빠르게 찾아내는 데 도움이 될 수 있습니다. 경고는 코드의 문제를 감지하고, 인프라 구성 방법을 파악하는 데 도움이 됩니다. Azure Resource Health는 개별 애플리케이션에 국한되지 않은 Azure 플랫폼 자체의 문제를 드러냅니다.

![Azure Resource Health 창 스크린샷](./media/resource-group-insights/0002-overview.png)

### <a name="azure-resource-health"></a>Azure Resource Health

Azure Resource Health를 표시하려면 표 위에 있는 **Azure Resource Health 표시** 확인란을 선택합니다. 이 열은 기본적으로는 페이지를 빠르게 로드하기 위해 숨겨집니다.

![리소스 상태 그래프가 추가된 스크린샷](./media/resource-group-insights/0003-overview.png)

기본적으로 리소스는 앱 계층 및 리소스 종류별로 그룹화됩니다. **앱 계층**은 리소스 그룹 인사이트 개요 페이지의 컨텍스트 내에서만 존재하는 리소스 종류의 간단한 분류 입니다. 애플리케이션 코드, 계산 인프라, 네트워킹, 스토리지 + 데이터베이스와 관련된 리소스 종류가 있습니다. 관리 도구에는 자체 앱 계층이 있으며 다른 모든 리소스는 **기타** 앱 계층으로 분류됩니다. 이러한 그룹화는 애플리케이션에서 정상 및 비정상 상태의 하위 시스템을 일목요연하게 확인하는 데 도움이 될 수 있습니다.

## <a name="diagnose-issues-in-your-resource-group"></a>리소스 그룹의 문제 진단

리소스 그룹 인사이트 페이지는 문제를 진단하는 데 도움을 주기 위해 몇 가지 다른 도구를 범위별로 제공합니다.

   |         |          |
   | ---------------- |:-----|
   | [**경고**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)      |  경고를 보고, 만들고, 관리합니다. |
   | [**메트릭**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) | 메트릭 기반 데이터를 시각화하고 탐색합니다.    |
   | [**활동 로그**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) | Azure에서 발생한 구독 수준 이벤트입니다.  |
   | [**애플리케이션 맵**](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) | 분산 애플리케이션 토폴로지를 탐색하여 성능 병목 상태 또는 실패 핫스폿을 찾습니다. |

## <a name="failures-and-performance"></a>오류 및 성능

애플리케이션이 느리게 실행되고 있다는 사실을 알게 되거나 사용자가 오류를 보고하면 어떻게 하나요? 모든 리소스를 탐색하면서 문제를 격리하는 데는 시간이 많이 걸립니다.

**성능** 및 **오류** 탭은 많은 일반적인 리소스 종류에 대한 성능 및 오류 진단 보기를 함께 표시하여 이러한 과정을 간소화합니다.

대부분의 리소스 종류에서는 Azure Monitor 통합 문서 템플릿 갤러리가 열립니다. 만든 각 통합 문서를 사용자 지정하고 저장하고 팀과 공유할 수 있으며 향후 비슷한 문제를 진단하는 데 재사용할 수 있습니다.

### <a name="investigate-failures"></a>오류 조사

오류 탭을 테스트하려면 왼쪽 메뉴의 **조사**에서 **오류**를 선택합니다.

선택을 수행하면 왼쪽 메뉴 모음이 변경되면서 새 옵션을 표시합니다.

![오류 개요 창 스크린샷](./media/resource-group-insights/00004-failures.png)

App Service를 선택하면 Azure Monitor 통합 문서 템플릿 갤러리가 제공됩니다.

![애플리케이션 통합 문서 갤러리 스크린샷](./media/resource-group-insights/0005-failure-insights-workbook.png)

오류 인사이트를 위한 템플릿을 선택하면 통합 문서가 열립니다.

![오류 보고서 스크린샷](./media/resource-group-insights/0006-failure-visual.png)

아무 행이나 선택할 수 있습니다. 그러면 선택 항목이 그래픽 세부 정보 보기에 표시됩니다.

![오류 세부 정보 스크린샷](./media/resource-group-insights/0007-failure-details.png)

통합 문서는 사용자 지정 보고서 및 시각화를 쉽게 사용할 수 있는 형식으로 만드는 까다로운 작업을 추상화합니다. 일부 사용자는 미리 작성된 매개 변수를 조정하기만 하면 될 수 있지만 통합 문서는 완전히 사용자 지정할 수 있습니다.

이러한 통합 문서가 내부적으로 작동하는 방식을 이해하려면 위쪽 메뉴 모음에서 **편집**을 선택합니다.

![추가 편집 옵션 스크린샷](./media/resource-group-insights/0008-failure-edit.png)

통합 문서의 다양한 요소 옆에 여러 **편집** 상자가 표시됩니다. 작업 표 아래의 **편집**을 선택합니다.

![편집 상자 스크린샷](./media/resource-group-insights/0009-failure-edit-graph.png)

그러면 표 시각화를 제공하는 기본 로그 쿼리가 표시됩니다.

 ![로그 쿼리 창 스크린샷](./media/resource-group-insights/0010-failure-edit-query.png)

쿼리를 직접 수정할 수 있습니다. 또는 쿼리를 참조하여 매개 변수화된 사용자 지정 통합 문서를 디자인할 때 활용할 수 있습니다.

### <a name="investigate-performance"></a>성능 조사

성능은 자체적인 통합 문서 갤러리를 제공합니다. App Service의 경우 미리 작성된 애플리케이션 성능 통합 문서가 다음 보기를 제공합니다.

 ![성능 보기 스크린샷](./media/resource-group-insights/0011-performance.png)

이 경우 편집을 선택하면 이러한 시각화 집합이 Azure Monitor 메트릭을 기준으로 한다는 것을 알 수 있습니다.

 ![Azure 메트릭을 사용한 성능 보기 스크린샷](./media/resource-group-insights/0012-performance-metrics.png)

## <a name="troubleshooting"></a>문제 해결

### <a name="enabling-access-to-alerts"></a>경고 액세스 사용

Azure Monitor for Resource Groups에서 경고를 보려면 이 구독에 대한 소유자 또는 기여자 역할이 할당된 누군가가 구독에 속한 리소스 그룹에 대해 Azure Monitor for Resource Groups를 열어야 합니다. 이렇게 하면 읽기 권한이 있는 모든 사람이 Azure Monitor for Resource Groups에서 구독의 모든 리소스 그룹에 대한 경고를 볼 수 있습니다. 소유자 또는 기여자 역할이 할당된 사용자는 몇 분 후 이 페이지를 새로 고치세요.

Azure Monitor for Resource Groups는 Azure Monitor 경고 관리 시스템을 사용하여 경고 상태를 검색합니다. 경고 관리는 기본적으로 일부 리소스 그룹 및 구독에 대해서만 구성되며, 소유자 또는 기여자 역할이 할당된 사용자만 설정할 수 있습니다. 다음 방법으로 설정할 수 있습니다.
* 구독 내 아무 리소스 그룹에 대해 Azure Monitor for Resource Groups를 엽니다.
* 또는 구독으로 이동하고 **리소스 공급자**를 클릭한 다음, **Alerts.Management 등록**을 클릭합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Monitor 통합 문서](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)
- [Azure Resource Health](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [Azure Monitor 경고](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)
