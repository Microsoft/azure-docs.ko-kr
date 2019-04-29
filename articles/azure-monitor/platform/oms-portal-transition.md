---
title: Azure로 이동하는 OMS 포털 | Microsoft Docs
description: OMS 포털은 Azure Portal로 이동하면서 모든 기능이 종료되고 있습니다. 이 문서는 이 전환에 대해 자세한 정보를 제공합니다.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: bwren
ms.openlocfilehash: c4950d03449f2b293a87ab88f1ea3f49eee29557
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62102019"
---
# <a name="oms-portal-moving-to-azure"></a>Azure로 이동하는 OMS 포털

> [!NOTE]
> 이 문서는 별도로 명시된 곳을 제외하고 Azure 공용 클라우드 및 정부 클라우드 모두에 적용됩니다.

Azure Portal은 모든 Azure 서비스의 허브이며, 리소스 고정을 위한 대시보드, 리소스를 찾기 위한 지능형 검색, 리소스 관리를 위한 태그 지정 같은 기능을 통해 다양한 관리 환경을 제공합니다. 모니터링 및 관리 워크플로를 간소화하고 통합하기 위해 Azure Portal에 OMS 포털 기능을 추가하기 시작했습니다. OMS 포털의 모든 기능은 이제 Azure Portal의 일부입니다. 사실, 트래픽 분석 같은 새 기능 중 일부는 Azure Portal에서만 사용할 수 있습니다. Azure Portal 등을 사용하여 OMS 포털에서 하던 모든 것을 수행할 수 있습니다. 아직 그렇게 하지 않은 경우 지금 Azure Portal의 사용을 시작하는 것이 좋습니다!

Azure 상용 클라우드 및 Azure 미국 정부 클라우드에 대한 **OMS 포털이 2019년 1월 15일 공식적으로 만료**되며, OMS 포털은 **2019년 3월 30일에 공식적으로 만료됩니다.** Azure Portal로 이동하게 돼 기쁘며 이 전환이 쉬울 것이라 예상합니다. 그러나 변경은 어려우며 중단될 수 있음을 이해합니다. 모든 질문, 피드백 또는 문제를 보낼 **LAUpgradeFeedback\@microsoft.com**합니다. 이 문서의 나머지 부분에서는 주요 시나리오 및 이 전환에 대한 로드맵을 살펴봅니다.

## <a name="what-is-changing"></a>변경되는 내용 
다음 변경 사항은 OMS 포털의 사용 중단으로 공지되었습니다. 각 변경에 대한 자세한 설명은 아래 섹션에 자세히 나와 있습니다.

- Azure Portal에서만 새 [작업 영역](#new-workspaces)을 만들 수 있습니다.
- 새 경고 관리 환경이 [경고 관리 솔루션을 대체](#changes-to-alerts)하게 됩니다.
- [사용자 액세스 관리](#user-access-and-role-migration)는 이제 Azure 역할 기반 액세스 제어를 사용하여 Azure Portal에서 수행됩니다.
- [Application Insights 커넥터](#application-insights-connector-and-solution)는 동일한 기능이 작업 영역 간 쿼리를 통해 활성화되므로 더 이상 필요치 않습니다.
- [OMS 모바일 앱](#oms-mobile-app)은 사용되지 않습니다. 
- [NSG 솔루션](#azure-network-security-group-analytics)은 트래픽 분석 솔루션을 통해 사용 가능한 향상된 기능으로 대체되고 있습니다.
- System Center Operations Manager에서 Log Analytics로의 새로운 연결에는 [업데이트된 관리 팩](#system-center-operations-manager)이 필요합니다.
- [업데이트 관리](../../automation/automation-update-management.md)에 대한 변경 내용의 세부 정보는 [OMS 업데이트 배포를 Azure로 마이그레이션](../../automation/migrate-oms-update-deployments.md)을 참조하세요.


## <a name="what-should-i-do-now"></a>이제 어떻게 해야 하나요?
대부분의 기능이 계속해서 모든 마이그레이션을 수행하지 않고 작동하므로 다음 작업을 수행해야 합니다.

- [사용자 권한을 Azure Portal로 마이그레이션](#user-access-and-role-migration)해야 합니다.
- 업데이트 관리 솔루션 전환에 대한 세부 정보는 [OMS 업데이트 배포를 Azure로 마이그레이션](../../automation/migrate-oms-update-deployments.md)을 참조하세요.

Azure Portal로 전환하는 방법에 대한 자세한 내용은 [Log Analytics 사용자를 위한 OMS 포털에서 Azure Portal로 전환에 대한 일반적인 질문](oms-portal-faq.md)을 참조하세요. 모든 피드백, 질문 또는 문제를 보낼 **LAUpgradeFeedback\@microsoft.com**합니다.

## <a name="user-access-and-role-migration"></a>사용자 액세스 및 역할 마이그레이션
Azure Portal 액세스 관리는 OMS 포털에서 액세스 관리보다 더욱 풍부하고 더욱 강력합니다. Log Analytics에서 액세스 관리에 대한 자세한 내용은 [작업 영역 관리](manage-access.md#manage-accounts-and-users)를 참조하세요.

> [!NOTE]
> 이 문서의 이전 버전은 사용 권한이 OMS 포털에서 Azure Portal로 자동으로 변환되는 것을 명시했습니다. 이 자동 대화는 더 이상 계획에 없으며, 스스로 변환을 수행해야 합니다.

Azure Portal에 이미 적절한 액세스 권한이 있을 수 있으며 이 경우 변경할 필요가 없습니다. 적절한 액세스 권한이 없을 수 있는 두 가지의 사례가 있습니다. 이 경우 관리자는 권한을 할당해야 합니다.

- OMS 포털에서 읽기 전용 사용자 권한이 있지만 Azure Portal에서 아무런 권한이 없습니다. 
- OMS 포털에서 기여자 사용자 권한이 있지만 Azure Portal에서 독자 권한만을 갖습니다.
 
이러한 두 경우에 관리자는 다음 테이블에서 적절한 역할을 수동으로 할당해야 합니다. 리소스 그룹 또는 구독 수준에서 이 역할을 할당하는 것이 좋습니다.  더 많은 규범적 지침이 두 경우 모두에 대해 곧 제공됩니다.

| OMS 포털 사용 권한 | Azure 역할 |
|:---|:---|
| ReadOnly | Log Analytics 독자 |
| 참가자 | Log Analytics 참가자 |
| 관리자 | 소유자 | 
 

## <a name="new-workspaces"></a>새 작업 영역
OMS 포털을 사용하여 더 이상 새 작업 영역을 만들 수 없습니다. Azure Portal에서 새 작업 영역을 만들려면 [Azure Portal에서 Log Analytics 작업 영역 만들기](../learn/quick-create-workspace.md)의 지침을 따릅니다.

## <a name="changes-to-alerts"></a>경고에 대한 변경

### <a name="alert-extension"></a>경고 확장  

> [!NOTE]
> 경고는 이제 공용 클라우드에 대한 Azure Portal로 완전히 확장되었습니다. OMS 포털에서 기존 경고 규칙을 볼 수 있지만 Azure Portal에서는 관리만 할 수 있습니다. Azure Portal로 경고의 확장은 Azure 정부 클라우드에 대해 2019년 2월에 시작합니다.

경고는 [Azure Portal로 확장](alerts-extend.md)되었습니다. 완료되면 경고에 대한 관리 작업은 Azure Portal에서만 사용할 수 있습니다. 기존 경고는 계속 OMS 포털에 나열됩니다. Log Analytics 경고 REST API 또는 Log Analytics 경고 리소스 템플릿을 사용하여 프로그래밍 방식으로 경고에 액세스하는 경우 API 호출, Azure Resource Manager 템플릿 및 PowerShell 명령에서 작업 대신 작업 그룹을 사용해야 합니다.

### <a name="alert-management-solution"></a>경고 관리 솔루션
이전 알림의 변경으로 [경고 관리 솔루션](alert-management-solution.md)은 계속 사용할 수 있으며 Azure Portal에서 완전히 지원됩니다. Azure Marketplace에서 솔루션을 계속 설치할 수 있습니다.

경고 관리 솔루션을 계속해서 사용할 수 있는 동안 [Azure Monitor의 통합된 경고 인터페이스](alerts-overview.md)를 사용하여 Azure의 모든 경고를 시각화하고 관리하는 것이 좋습니다. 이 새로운 환경은 Log Analytics에서 로그 경고를 포함하여 Azure 내의 여러 원본에서 경고를 고유하게 집계합니다. Azure Monitor의 통합된 경고 인터페이스를 사용하는 경우 경고 관리 솔루션을 사용하려면 System Center Operation Manager에서 Azure로 경고를 통합해야 합니다. Azure Monitor의 통합된 경고 인터페이스에서 경고의 배포를 확인하고, 스마트 그룹을 통해 관련 경고의 자동 그룹화를 활용하고, 풍부하게 필터를 적용하는 동안 여러 구독에서 경고를 볼 수 있습니다. 경고 관리의 향후 개선 사항은 주로 이 새로운 환경에서 사용할 수 있습니다. 

경고 관리 솔루션(경고 유형별 레코드)에 의해 수집된 데이터는 솔루션이 작업 영역에 대해 설치되는 한 Log Analytics에 계속 포함됩니다. 

## <a name="oms-mobile-app"></a>OMS 모바일 앱
OMS 모바일 앱은 OMS 포털과 함께 종료됩니다. OMS 모바일 앱 대신에 IT 인프라, 대시보드 및 저장된 쿼리에 대한 정보에 액세스하려면 모바일 디바이스의 브라우저에서 직접 Azure Portal에 액세스할 수 있습니다. 경고를 받으려면 [Azure 동작 그룹](action-groups.md)을 구성하여 음성 통화 또는 SMS 형식으로 알림을 받아야 합니다.

## <a name="application-insights-connector-and-solution"></a>Application Insights 커넥터 및 솔루션
[Application Insights 커넥터](app-insights-connector.md)는 Application Insights 데이터를 Log Analytics 작업 영역으로 포함할 수 있는 방법을 제공합니다. 이 데이터 중복은 인프라 및 애플리케이션 데이터에서 시각화를 사용하는 데 필요했습니다. 2019년 3월부터 지원 예정인 Application Insights 확장 데이터 보존 기능과 [리소스 간 쿼리](../log-query/cross-workspace-query.md) 수행 기능, 그리고 [여러 Azure Monitor Application Insights 리소스를 확인](../log-query/unify-app-resource-data.md)하는 기능을 사용하면 Application Insights 리소스에서 데이터를 복제하여 Log Analytics로 전송하지 않아도 됩니다. 또한 커넥터는 리소스 간 쿼리에서 향상된 유연성을 제공하여 애플리케이션 속성의 하위 집합을 Log Analytics에 전송합니다.  

따라서 Application Insights 커넥터는 2019년 3월 30일의 OMS 포털 사용 중단과 함께 Azure Marketplace에서 사용 중단 및 제거되는 반면 기존 커넥터는 2019년 6월 30일까지 계속 작동합니다. OMS 포털 사용 중단으로 포털에서 기존 연결을 구성하고 제거할 방법이 없습니다. 2019년 1월에 사용할 수 있는 REST API를 사용하여 지원되며 [Azure 업데이트](https://azure.microsoft.com/updates/)에 알림이 게시됩니다. 

## <a name="azure-network-security-group-analytics"></a>Azure 네트워크 보안 그룹 분석
[Azure 네트워크 보안 그룹 분석 솔루션](../insights/azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-azure-monitor)은 최근에 시작된 [트래픽 분석](https://azure.microsoft.com/blog/traffic-analytics-in-preview/)으로 대체되어 클라우드 네트워크에서 사용자 및 애플리케이션 작업에 대한 가시성을 제공합니다. 트래픽 분석은 조직의 네트워크 활동을 감사하고, 애플리케이션 및 데이터를 보호하고, 워크로드 성능을 최적화하고, 준수 상태를 유지하는 데 도움이 됩니다. 

이 솔루션은 NSG 흐름 로그를 분석하고 다음에 대한 인사이트를 제공합니다.

- 트래픽은 Azure와 인터넷, 공용 클라우드 지역, VNET 및 서브넷 사이의 네트워크를 통한 흐릅니다.
- 스니퍼나 전용 흐름 수집 어플라이언스가 필요 없는 네트워크상의 애플리케이션 및 프로토콜입니다.
- 상위 토커, 대화량이 많은 애플리케이션, 클라우드의 VM 대화, 트래픽 핫스폿.
- VNET, 중요한 비즈니스 서비스와 애플리케이션 간의 상호 관계 트래픽의 원본 및 대상입니다.
- 악성 트래픽, 인터넷에 열려 있는 포트, 인터넷 액세스를 시도하는 애플리케이션 또는 VM을 포함한 보안입니다.
- 과도한 프로비전 또는 미달 사용이라는 문제를 제거하는 데 도움이 되는 용량 사용률입니다.

Log Analytics에 NSG 로그를 보내려면 진단 설정에 계속 의존할 수 있으므로 기존 저장된 검색, 경고 및 대시보드는 계속 작동하게 됩니다. 이미 솔루션을 설치한 고객은 추가 공지가 있을 때까지 솔루션을 계속 사용할 수 있습니다. 9월 5일부터 네트워크 보안 그룹 분석 솔루션은 마켓플레이스에서 제거되고 커뮤니티를 통해 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Operationalinsights)으로 사용할 수 있게 됩니다.

## <a name="system-center-operations-manager"></a>System Center Operations Manager
[Log Analytics에 Operations Manager 관리 그룹을 연결](om-agents.md)한 경우 변경 없이 계속 작동합니다. 그러나 새 연결의 경우에는 [Operations Management Suite를 구성하기 위한 Microsoft System Center Operations Manager 관리 팩](https://blogs.technet.microsoft.com/momteam/2018/07/25/microsoft-system-center-operations-manager-management-pack-to-configure-operations-management-suite/)의 지침을 따릅니다.

## <a name="next-steps"></a>다음 단계
- OMS 포털에서 Azure Portal로 이동에 대한 지침은 [Log Analytics 사용자를 위한 OMS 포털에서 Azure Portal로 전환에 대한 일반적인 질문](oms-portal-faq.md)을 참조하세요.
