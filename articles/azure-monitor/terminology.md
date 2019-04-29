---
title: Azure Monitor 용어 업데이트 | Microsoft Docs
description: Azure 모니터링 서비스의 최근 용어 변경을 설명합니다.
author: bwren
manager: carmonm
editor: tysonn
services: azure-monitor
documentationcenter: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2019
ms.author: bwren
ms.openlocfilehash: 8f645f7d569546a8362d0149806a2b4636567fd0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61086751"
---
# <a name="azure-monitor-naming-and-terminology-changes"></a>Azure Monitor 이름 지정 및 용어 변경
Azure 고객을 위한 모니터링 간소화를 위해 다양한 서비스가 통합되면서 Azure Monitor가 최근 상당히 변화되었습니다. 이 문서에서는 Azure Monitor 설명서의 이름 및 용어 변경 내용을 설명합니다.

## <a name="february-2019---log-analytics-terminology"></a>2019년 2월 - Log Analytics 용어
Azure Monitor에 다양한 서비스를 통합한 후 다음 단계로, Azure Monitor 서비스와 다른 구성 요소를 잘 설명하기 위해 설명서의 용어가 수정되었습니다. 

### <a name="log-analytics"></a>Log Analytics
Azure Monitor 로그 데이터는 여전히 Log Analytics 작업 영역에 저장되며 동일한 Log Analytics 서비스에 의해 수집되고 분석됩니다. 다만, 여러 위치에서 _Log Analytics_라는 용어를 _Azure Monitor_ 로그로 변경하고 있습니다. 이 용어에는 Azure Monitor의 역할이 보다 잘 반영되고 [Azure Monitor에서 더 나은 일관성 메트릭](platform/data-platform-metrics.md)을 제공합니다.

이제 로그 분석(_log analytics_)이라는 용어는 쿼리를 작성하여 실행하고 로그 데이터를 분석하는 데 사용되는 Azure Portal의 페이지에 주로 적용됩니다. 이것은 [메트릭 탐색기](platform/metrics-charts.md)와 기능적으로 동일하며 메트릭 데이터를 분석하는 데 사용되는 Azure Portal의 페이지입니다.

### <a name="log-analytics-workspaces"></a>Log Analytics 작업 영역
Azure Monitor에서 로그 데이터를 보유하는 [작업 영역](platform/manage-access.md)은 여전히 Log Analytics 작업 영역이라고 합니다. Azure Portal의 **Log Analytics** 메뉴는 **Log Analytics 작업 영역**으로 이름이 바뀌었으며, 여기서 [새 작업 영역을 만들고](learn/quick-create-workspace.md) 데이터 원본을 구성합니다. **Azure Monitor**에서 로그 및 기타 모니터링 데이터를 분석하고 **Log Analytics 작업 영역**에서 작업 영역을 구성합니다.

### <a name="management-solutions"></a>관리 솔루션
[관리 솔루션](insights/solutions.md)은 기능을 더 잘 설명할 수 있도록 _모니터링 솔루션_으로 이름이 변경되었습니다.


## <a name="august-2018---consolidation-of-monitoring-services-into-azure-monitor"></a>2018년 8월 - Azure Monitor로 모니터링 서비스 통합
Azure 리소스와 하이브리드 환경을 모니터링하는 단일 통합 환경을 제공하기 위해 Log Analytics와 Application Insights를 Azure Monitor로 통합했습니다. 해당 서비스에서 기능이 제거되지 않았으므로, 어떤 기능도 유실 또는 손상되지 않은 상태에서 항상 완료했던 동일한 시나리오를 수행할 수 있습니다.

각 서비스의 문서는 Azure Monitor용 단일 콘텐츠 집합으로 통합되었습니다. 따라서 여러 콘텐츠 집합을 참조할 필요 없이 한 위치에서 특정 모니터링 시나리오의 모든 콘텐츠를 찾을 수 있습니다. 통합 서비스가 진행됨에 따라 콘텐츠가 더 많이 통합됩니다.

에이전트 및 보기와 같은 Log Analytics의 일부로 간주되었던 다른 기능도 Azure Monitor의 기능으로 재배치되었습니다. Azure Portal에서 기능이 잠재적으로 향상되는 것 이외에 이 기능은 변경되지 않았습니다.


## <a name="april-2018---retirement-of-operations-management-suite-brand"></a>2018년 4월 - Operations Management Suite 브랜드 사용 중지
OMS(Operations Management Suite)는 라이센스 부여에 사용되는 다음 Azure 관리 서비스를 번들한 것입니다.

- Application Insights
- Azure Automation
- Azure Backup
- Log Analytics
- Site Recovery

[해당 서비스에 대한 새 가격 책정이 도입](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/)되었으며 OMS 번들은 더 이상 새 고객에게 제공되지 않습니다. 위에서 설명한 Azure Monitor로의 통합을 제외하고 OMS의 일부인 서비스는 변경되지 않았습니다. 




## <a name="next-steps"></a>다음 단계

- 여러 다른 구성 요소와 기능을 설명하는 [Azure Monitor 개요](overview.md)를 읽어 보세요.
- [OMS 포털 상태 전환](../log-analytics/log-analytics-oms-portal-transition.md)에 관해 알아보세요.