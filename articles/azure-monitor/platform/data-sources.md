---
title: Azure Monitor의 데이터 원본 | Microsoft Docs
description: Azure 리소스 및 리소스에서 실행되는 애플리케이션의 성능과 상태를 모니터링할 수 있는 데이터를 설명합니다.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/19/2019
ms.openlocfilehash: 8f17a3ffbbee2bc702be1c2c690a1d6c85d792cb
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751047"
---
# <a name="sources-of-monitoring-data-for-azure-monitor"></a>Azure Monitor에 대 한 모니터링 데이터의 원본
Azure Monitor는 [로그](data-platform-logs.md) 및 [메트릭을](data-platform-metrics.md)포함 하는 [일반적인 모니터링 데이터 플랫폼](data-platform.md) 을 기반으로 합니다. 이 플랫폼에 데이터를 수집 하면 Azure Monitor의 공통 도구 집합을 사용 하 여 여러 리소스의 데이터를 함께 분석할 수 있습니다. 모니터링 데이터는 특정 시나리오를 지원 하기 위해 다른 위치로 전송 될 수도 있으며, 일부 리소스는 로그 나 메트릭에 수집 되기 전에 다른 위치에 기록 될 수 있습니다.

이 문서에서는 Azure 리소스에서 만든 모니터링 데이터 외에도 Azure Monitor에 의해 수집 되는 다양 한 모니터링 데이터 원본을 설명 합니다. 이 데이터를 다른 위치로 수집 하는 데 필요한 구성에 대 한 자세한 정보를 제공 하는 링크가 제공 됩니다.

## <a name="application-tiers"></a>애플리케이션 계층

Azure 응용 프로그램에서 모니터링 데이터의 소스는 계층으로 구성 될 수 있으며, 가장 높은 계층은 응용 프로그램 자체와 Azure platform의 구성 요소가 되는 하위 계층으로 구성 될 수 있습니다. 각 계층의 데이터에 액세스 하는 방법은 다양 합니다. 응용 프로그램 계층은 아래 표에 요약 되어 있으며 각 계층의 모니터링 데이터 원본은 다음 섹션에 나와 있습니다. 각 데이터 위치에 대 한 설명 및 데이터에 액세스 하는 방법은 [Azure에서 데이터 위치 모니터링](data-locations.md) 을 참조 하세요.


![계층 모니터링](../media/overview/overview.png)


### <a name="azure"></a>Azure
다음 표에서는 Azure와 관련 된 응용 프로그램 계층을 간략하게 설명 합니다. 아래 섹션의 각 항목에 대 한 자세한 내용은 링크를 참조 하세요.

| 계층 | Description | Collection 메서드 |
|:---|:---|:---|
| [Azure 테넌트](#azure-tenant) | Azure Active Directory 등, 테넌트 수준 Azure 서비스의 작업에 대한 데이터입니다. | 포털에서 AAD 데이터를 확인 하거나 테넌트 진단 설정을 사용 하 여 Azure Monitor 컬렉션을 구성 합니다. |
| [Azure 구독](#azure-subscription) | 리소스 관리자 및 Service Health와 같은 Azure 구독에서 리소스 간 서비스의 상태 및 관리와 관련 된 데이터입니다. | 포털에서 보거나 로그 프로필을 사용 하 여 Azure Monitor 컬렉션을 구성 합니다. |
| [Azure 리소스](#azure-resources) |  각 Azure 리소스의 작업 및 성능에 대 한 데이터입니다. | 자동으로 수집 된 메트릭, 메트릭 탐색기에서 보기<br>Azure Monitor에서 로그를 수집 하도록 진단 설정을 구성 합니다.<br>특정 리소스 종류에 대 한 자세한 모니터링에 대 한 모니터링 솔루션 및 정보를 제공 합니다. |

### <a name="azure-other-cloud-or-on-premises"></a>Azure, 기타 클라우드 또는 온-프레미스 
다음 표에서는 Azure, 다른 클라우드 또는 온-프레미스에 있을 수 있는 응용 프로그램 계층을 간략하게 설명 합니다. 아래 섹션의 각 항목에 대 한 자세한 내용은 링크를 참조 하세요.

| 계층 | Description | Collection 메서드 |
|:---|:---|:---|
| [운영 체제 (게스트)](#operating-system-guest) | 계산 리소스의 운영 체제에 대 한 데이터입니다. | Log Analytics 에이전트를 설치 하 여 클라이언트 데이터 원본을 Azure Monitor 및 종속성 에이전트로 수집 하 여 VM용 Azure Monitor를 지 원하는 종속성을 수집 합니다.<br>Azure virtual machines의 경우 Azure 진단 확장을 설치 하 여 Azure Monitor로 로그 및 메트릭을 수집 합니다. |
| [응용 프로그램 코드](#application-code) | 성능 추적, 응용 프로그램 로그 및 사용자 원격 분석을 포함 하 여 실제 응용 프로그램 및 코드의 성능 및 기능에 대 한 데이터입니다. | 코드를 계측 하 여 데이터를 Application Insights으로 수집 합니다. |
| [사용자 지정 원본](#custom-sources) | 외부 서비스 또는 기타 구성 요소 또는 장치의 데이터 | 모든 REST 클라이언트에서 Azure Monitor 로그 또는 메트릭 데이터를 수집 합니다. |

## <a name="azure-tenant"></a>Azure 테넌트
Azure 테넌트와 관련된 원격 분석은 Azure Active Directory와 같은 테넌트 전체 서비스에서 수집됩니다.

![Azure 테넌트 컬렉션](media/data-sources/tenant.png)

### <a name="azure-active-directory-audit-logs"></a>Azure Active Directory 감사 로그
[Azure Active Directory 보고](../../active-directory/reports-monitoring/overview-reports.md)는 로그인 활동 및 특정 테넌트 내의 변경 감사 내역에 대한 기록을 포함합니다. 

| 대상 | Description | 참조 |
|:---|:---|:---|
| Azure Monitor 로그 | 다른 모니터링 데이터를 사용 하 여 분석 하기 위해 Azure Monitor에서 수집 되도록 Azure AD 로그를 구성 합니다. | [Azure Monitor 로그와 Azure AD 로그 통합 (미리 보기)](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure Storage | Azure AD 로그를 보관을 위해 Azure Storage로 내보냅니다. | [자습서: azure storage 계정 (미리 보기)에 Azure AD 로그 보관](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| 이벤트 허브 | Event Hubs를 사용 하 여 Azure AD 로그를 다른 위치로 스트리밍합니다. | [자습서: Azure 이벤트 허브 (미리 보기)에 Azure Active Directory 로그를 스트리밍합니다](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md). |



## <a name="azure-subscription"></a>Azure 구독
Azure 구독의 상태 및 작업과 관련 된 원격 분석입니다.

![Azure 구독](media/data-sources/azure-subscription.png)

### <a name="azure-activity-log"></a>Azure 활동 로그 
Azure [활동 로그](platform-logs-overview.md) 에는 azure 구독의 리소스에 대 한 구성 변경에 대 한 기록과 함께 서비스 상태 레코드가 포함 됩니다. 활동 로그는 모든 Azure 리소스에 대해 사용할 수 있으며 해당 _외부_ 보기를 나타냅니다.

| 대상 | Description | 참조 |
|:---|:---|
| 활동 로그 | 활동 로그는 Azure Monitor 메뉴에서 보거나 활동 로그 경고를 만드는 데 사용할 수 있는 자체 데이터 저장소로 수집 됩니다. | [Azure Portal에서 활동 로그를 쿼리 합니다.](activity-log-view.md#azure-portal) |
| Azure Monitor 로그 | 활동 로그를 수집 하 여 다른 모니터링 데이터를 사용 하 여 분석 하도록 Azure Monitor 로그를 구성 합니다. | [Azure Monitor의 Log Analytics 작업 영역에서 Azure 활동 로그를 수집 하 고 분석 합니다.](activity-log-collect.md) |
| Azure Storage | 작업 로그를 보관을 위해 Azure Storage로 내보냅니다. | [활동 로그 보관](activity-log-export.md#archive-activity-log)  |
| Event Hubs | Event Hubs를 사용 하 여 다른 위치로 활동 로그 스트림 | [활동 로그를 이벤트 허브로 스트림](activity-log-export.md#stream-activity-log-to-event-hub)합니다. |

### <a name="azure-service-health"></a>Azure Service Health
[Azure Service Health](../../service-health/service-health-overview.md)는 애플리케이션 및 리소스가 의존하는 구독에서 Azure 서비스의 상태에 대한 정보를 제공합니다.

| 대상 | Description | 참조 |
|:---|:---|:---|
| 활동 로그<br>Azure Monitor 로그 | Service Health 레코드는 Azure 활동 로그에 저장 되므로 Azure Portal에서 보거나 활동 로그로 수행할 수 있는 다른 활동을 수행할 수 있습니다. | [Azure Portal을 사용하여 서비스 상태 알림 보기](service-notifications.md) |


## <a name="azure-resources"></a>Azure 리소스
메트릭 및 리소스 로그는 Azure 리소스의 _내부_ 작업에 대 한 정보를 제공 합니다. 이러한 기능은 대부분의 Azure 서비스에 사용할 수 있으며, 모니터링 솔루션과 통찰력은 특정 서비스에 대 한 추가 데이터를 수집 합니다.

![Azure 리소스 컬렉션](media/data-sources/azure-resources.png)


### <a name="platform-metrics"></a>플랫폼 메트릭 
대부분의 Azure 서비스는 성능 및 작업을 메트릭 데이터베이스에 직접 반영 하는 [플랫폼 메트릭을](data-platform-metrics.md) 전송 합니다. 특정 [메트릭은 리소스의 각 형식마다 다릅니다](metrics-supported.md). 

| 대상 | Description | 참조 |
|:---|:---|:---|
| Azure Monitor 메트릭 | 플랫폼 메트릭은 구성 없이 Azure Monitor 메트릭 데이터베이스에 기록 합니다. 메트릭 탐색기에서 플랫폼 메트릭에 액세스 합니다.  | [Azure 메트릭 탐색기 시작](metrics-getting-started.md)<br>[Azure Monitor에서 지원되는 메트릭](metrics-supported.md) |
| Azure Monitor 로그 | Log Analytics를 사용 하 여 추세 및 기타 분석을 위해 플랫폼 메트릭을 로그에 복사 합니다. | [Log Analytics에 대 한 Azure 진단 직접](resource-logs-collect-workspace.md) |
| Event Hubs | Event Hubs를 사용 하 여 다른 위치로 메트릭을 스트림 합니다. |[Azure 모니터링 데이터를 이벤트 허브로 스트리밍하여 외부 도구에서 사용](stream-monitoring-data-event-hubs.md) |

### <a name="resource-logs"></a>리소스 로그
[리소스 로그](platform-logs-overview.md) 는 Azure 리소스의 _내부_ 작업에 대 한 통찰력을 제공 합니다.  리소스 로그는 자동으로 만들어지지만 각 리소스에 대해 수집할 대상을 지정 하려면 진단 설정을 만들어야 합니다.

리소스 로그의 구성 요구 사항 및 내용은 리소스 유형에 따라 다르며, 일부 서비스에서는 아직 만들지 않습니다. 각 서비스에 대 한 자세한 내용과 자세한 구성 절차에 대 한 링크를 보려면 [Azure 리소스 로그에 대해 지원 되는 서비스, 스키마 및 범주](diagnostic-logs-schema.md) 를 참조 하세요. 서비스가이 문서에 나열 되지 않은 경우 해당 서비스는 현재 리소스 로그를 만들지 않습니다.

| 대상 | Description | 참조 |
|:---|:---|:---|
| Azure Monitor 로그 | 다른 수집 된 로그 데이터를 사용 하 여 분석을 위해 Azure Monitor 로그에 리소스 로그를 보냅니다. | [Azure Monitor의 Log Analytics 작업 영역에서 Azure 리소스 로그를 수집 합니다.](resource-logs-collect-storage.md) |
| Storage | 보관을 위해 Azure Storage로 리소스 로그를 보냅니다. | [Azure 리소스 로그 보관](resource-logs-collect-workspace.md) |
| Event Hubs | Event Hubs를 사용 하 여 리소스 로그를 다른 위치로 스트림 합니다. |[이벤트 허브로 Azure 리소스 로그 스트림](resource-logs-stream-event-hubs.md) |

## <a name="operating-system-guest"></a>운영 체제 (게스트)
Azure, 다른 클라우드 및 온-프레미스의 컴퓨팅 리소스에는 모니터링할 게스트 운영 체제가 있습니다. 하나 이상의 에이전트를 설치 하면 게스트의 원격 분석을 Azure Monitor으로 수집 하 여 Azure 서비스와 동일한 모니터링 도구를 사용 하 여 분석할 수 있습니다.

![Azure 컴퓨팅 리소스 컬렉션](media/data-sources/compute-resources.png)

### <a name="azure-diagnostic-extension"></a>Azure 진단 확장
Azure Virtual machines에 대해 Azure 진단 확장을 사용 하도록 설정 하면 azure 클라우드 서비스 (클래식) 웹 및 작업자 역할, Virtual Machines, 가상 컴퓨터를 포함 하 여 Azure 계산 리소스의 게스트 운영 체제에서 로그 및 메트릭을 수집할 수 있습니다. 크기 집합 및 Service Fabric

| 대상 | Description | 참조 |
|:---|:---|:---|
| Storage | 진단 확장을 사용 하도록 설정 하면 기본적으로 저장소 계정에 기록 됩니다. | [Azure Storage에서 진단 데이터 저장 및 보기](diagnostics-extension-to-storage.md) |
| Azure Monitor 메트릭 | 성능 카운터를 수집 하도록 진단 확장을 구성 하면 Azure Monitor 메트릭 데이터베이스에 기록 됩니다. | [Windows 가상 머신에 대 한 리소스 관리자 템플릿을 사용 하 여 Azure Monitor 메트릭 저장소에 게스트 OS 메트릭 보내기](collect-custom-metrics-guestos-resource-manager-vm.md) |
| Application Insights 로그 | 다른 응용 프로그램 데이터를 사용 하 여 분석할 응용 프로그램을 지 원하는 계산 리소스에서 로그 및 성능 카운터를 수집 합니다. | [클라우드 서비스, 가상 머신 또는 Service Fabric 진단 데이터를 Application Insights으로 보내기](diagnostics-extension-to-application-insights.md) |
| Event Hubs | Event Hubs를 사용 하 여 다른 위치로 데이터를 스트리밍하는 진단 확장을 구성 합니다.  | [Event Hubs를 사용 하 여 실행 부하 과다 경로의 데이터 스트리밍 Azure 진단](diagnostics-extension-stream-event-hubs.md) |

### <a name="log-analytics-agent"></a>Log Analytics 에이전트 
Windows 또는 Linux 가상 머신에 대 한 포괄적인 모니터링 및 관리를 위해 Log Analytics 에이전트를 설치 합니다. 가상 머신은 Azure, 다른 클라우드 또는 온-프레미스에서 실행될 수 있습니다.

| 대상 | Description | 참조 |
|:---|:---|:---|
| Azure Monitor 로그 | Log Analytics 에이전트는 직접 또는 System Center Operations Manager를 통해 Azure Monitor에 연결 하 여 사용자가 구성 하는 데이터 원본이 나 응용 프로그램에 대 한 추가 정보를 제공 하는 모니터링 솔루션에서 데이터를 수집할 수 있도록 합니다. 가상 머신에서 실행 중입니다. | [Azure Monitor의 에이전트 데이터 원본](agent-data-sources.md)<br>[Azure Monitor에 Operations Manager 연결](om-agents.md) |
| VM 스토리지 | VM용 Azure Monitor Log Analytics 에이전트를 사용 하 여 사용자 지정 위치에 heath 상태 정보를 저장 합니다. 자세한 내용은 다음 섹션을 참조하세요.  |


### <a name="azure-monitor-for-vms"></a>VM용 Azure Monitor 
[VM용 Azure Monitor](../insights/vminsights-overview.md) 은 서비스 상태 및 VM 상태를 비롯 하 여 핵심 Azure Monitor 기능을 제공 하는 기능을 제공 하는 가상 머신에 대 한 사용자 지정 모니터링 환경을 제공 합니다. 가상 컴퓨터 및 외부 프로세스 종속성에서 실행 되는 프로세스에 대 한 검색 된 데이터를 수집 하기 위해 Log Analytics 에이전트와 통합 되는 Windows 및 Linux 가상 컴퓨터에 대 한 Dependency Agent 필요 합니다.

| 대상 | Description | 참조 |
|:---|:---|:---|
| Azure Monitor 로그 | 에이전트의 프로세스 및 종속성에 대 한 데이터를 저장 합니다. | [VM용 Azure Monitor (미리 보기) 맵을 사용 하 여 응용 프로그램 구성 요소 이해](../insights/vminsights-maps.md) |
| VM 스토리지 | VM용 Azure Monitor Log Analytics 에이전트를 사용 하 여 사용자 지정 위치에 heath 상태 정보를 저장 합니다. 이는 [Azure Resource health REST API](/rest/api/resourcehealth/)외에도 Azure Portal VM용 Azure Monitor에만 사용할 수 있습니다. | [Azure virtual machines의 상태 이해](../insights/vminsights-health.md)<br>[Azure 리소스 상태 REST API](https://docs.microsoft.com/rest/api/resourcehealth/) |



## <a name="application-code"></a>응용 프로그램 코드
Azure Monitor의 자세한 응용 프로그램 모니터링은 다양 한 플랫폼에서 실행 되는 응용 프로그램에서 데이터를 수집 하는 [Application Insights](https://docs.microsoft.com/azure/application-insights/) 를 통해 수행 됩니다. 애플리케이션은 Azure, 다른 클라우드 또는 온-프레미스에서 실행될 수 있습니다.

![애플리케이션 데이터 수집](media/data-sources/applications.png)


### <a name="application-data"></a>애플리케이션 데이터
계측 패키지를 설치하여 애플리케이션에 대한 Application Insights를 사용하도록 설정하는 경우 애플리케이션의 성능 및 작업과 관련된 메트릭 및 로그를 수집합니다. Application Insights은 수집 하는 데이터를 다른 데이터 원본에서 사용 하는 것과 동일한 Azure Monitor 데이터 플랫폼에 저장 합니다. 여기에는이 데이터를 분석 하기 위한 광범위 한 도구가 포함 되어 있지만 메트릭 탐색기 및 Log Analytics 같은 도구를 사용 하 여 다른 원본의 데이터로 분석할 수도 있습니다.

| 대상 | Description | 참조 |
|:---|:---|:---|
| Azure Monitor 로그 | 페이지 보기, 응용 프로그램 요청, 예외 및 추적을 포함 하 여 응용 프로그램에 대 한 운영 데이터입니다. | [Azure Monitor에서 로그 데이터 분석](../log-query/log-query-overview.md) |
|                    | 응용 프로그램 맵 및 원격 분석 상관 관계를 지원 하기 위한 응용 프로그램 구성 요소 간의 종속성 정보 | [Application Insights의 원격 분석 상관 관계](../app/correlation.md) <br> [애플리케이션 맵](../app/app-map.md) |
|            | 공용 인터넷의 여러 위치에서 응용 프로그램의 가용성과 응답성을 테스트 하는 가용성 테스트의 결과입니다. | [웹 사이트의 가용성 및 응답성 모니터링](../app/monitor-web-app-availability.md) |
| Azure Monitor 메트릭 | Application Insights는 응용 프로그램에서 정의 하는 사용자 지정 메트릭 외에도 응용 프로그램의 성능과 작업을 설명 하는 메트릭을 수집 Azure Monitor 메트릭 데이터베이스에 수집 합니다. | [Azure Application Insights의 로그 기반 및 사전 집계 메트릭](../app/pre-aggregated-metrics-log-metrics.md)<br>[사용자 지정 이벤트 및 메트릭용 Application Insights API](../app/api-custom-events-metrics.md) |
| Azure Storage | 보관을 위해 Azure Storage에 응용 프로그램 데이터를 보냅니다. | [Application Insights에서 원격 분석 내보내기](../app/export-telemetry.md) |
|            | 가용성 테스트에 대 한 세부 정보는 Azure Storage에 저장 됩니다. Azure Portal에서 Application Insights를 사용 하 여 로컬 분석을 다운로드 합니다. 가용성 테스트의 결과는 Azure Monitor 로그에 저장 됩니다. | [웹 사이트의 가용성 및 응답성 모니터링](../app/monitor-web-app-availability.md) |
|            | 프로파일러 추적 데이터는 Azure Storage에 저장 됩니다. Azure Portal에서 Application Insights를 사용 하 여 로컬 분석을 다운로드 합니다.  | [Application Insights를 사용 하 여 Azure에서 프로덕션 응용 프로그램 프로 파일링](../app/profiler-overview.md) 
|            | 예외 하위 집합에 대해 캡처된 디버그 스냅숏 데이터는 Azure Storage에 저장 됩니다. Azure Portal에서 Application Insights를 사용 하 여 로컬 분석을 다운로드 합니다.  | [스냅숏의 작동 방식](../app/snapshot-debugger.md#how-snapshots-work) |

## <a name="monitoring-solutions-and-insights"></a>솔루션 및 통찰력 모니터링
[모니터링 솔루션](../insights/solutions.md) 및 [통찰력](../insights/insights-overview.md) 은 데이터를 수집 하 여 특정 서비스나 응용 프로그램의 작업에 대 한 추가 정보를 제공 합니다. 서로 다른 응용 프로그램 계층 및 여러 계층의 리소스를 처리할 수 있습니다.

### <a name="monitoring-solutions"></a>모니터링 솔루션

| 대상 | Description | 참조
|:---|:---|:---|
| Azure Monitor 로그 | 모니터링 솔루션은 데이터를 Azure Monitor 로그에 수집 합니다 .이 로그는 일반적으로 솔루션에 포함 되어 있는 쿼리 언어나 [뷰](view-designer.md) 를 사용 하 여 분석할 수 있습니다. | [Azure의 모니터링 솔루션에 대 한 데이터 수집 세부 정보](../insights/solutions-inventory.md) |


### <a name="azure-monitor-for-containers"></a>컨테이너용 Azure Monitor
[컨테이너에 대 한 Azure Monitor](../insights/container-insights-overview.md) 는 [AKS (Azure Kubernetes Service)](/azure/aks/)에 대 한 사용자 지정 모니터링 환경을 제공 합니다. 다음 표에서 설명 하는 해당 리소스에 대 한 추가 데이터를 수집 합니다.

| 대상 | Description | 참조 |
|:---|:---|:---|
| Azure Monitor 로그 | 인벤토리, 로그 및 이벤트를 포함 하 여 AKS에 대 한 모니터링 데이터를 저장 합니다. 메트릭 데이터는 포털에서 해당 분석 기능을 활용 하기 위해 로그에도 저장 됩니다. | [컨테이너용 Azure Monitor를 사용하여 AKS 클러스터 성능 이해](../insights/container-insights-analyze.md) |
| Azure Monitor 메트릭 | 메트릭 데이터는 시각화 및 경고를 구동 하기 위해 메트릭 데이터베이스에 저장 됩니다. | [메트릭 탐색기에서 컨테이너 메트릭 보기](../insights/container-insights-analyze.md#view-container-metrics-in-metrics-explorer) |
| Azure Kubernetes Service | 포털에서 AKS (Azure Kubernetes Service) 컨테이너 로그 (stdout/stderror), 이벤트 및 pod 메트릭에 대 한 직접 액세스를 제공 합니다. | [Kubernetes 로그, 이벤트 및 pod 메트릭을 실시간으로 보는 방법](../insights/container-insights-livedata-overview.md) |

### <a name="azure-monitor-for-vms"></a>VM용 Azure Monitor
[VM용 Azure Monitor](../insights/vminsights-overview.md) 는 가상 컴퓨터 모니터링을 위한 사용자 지정 된 환경을 제공 합니다. VM용 Azure Monitor에서 수집 된 데이터에 대 한 설명은 위의 [운영 체제 (게스트)](#operating-system-guest) 섹션에 포함 되어 있습니다.

## <a name="custom-sources"></a>사용자 지정 원본
애플리케이션의 표준 계층 외에 다른 데이터 원본과 함께 수집할 수 없는 원격 분석이 있는 다른 리소스를 모니터링해야 할 수도 있습니다. 이러한 리소스에 대해 Azure Monitor API를 사용 하 여 메트릭 또는 로그에이 데이터를 씁니다.

![사용자 지정 컬렉션](media/data-sources/custom.png)

| 대상 | 방법 | Description | 참조 |
|:---|:---|:---|:---|
| Azure Monitor 로그 | 데이터 수집기 API | 모든 REST 클라이언트에서 로그 데이터를 수집 하 고 Log Analytics 작업 영역에 저장 합니다. | [HTTP 데이터 수집기 API를 사용 하 여 Azure Monitor에 로그 데이터 전송 (공개 미리 보기)](data-collector-api.md) |
| Azure Monitor 메트릭 | 사용자 지정 메트릭 API | 모든 REST 클라이언트에서 메트릭 데이터를 수집 하 고 Azure Monitor 메트릭 데이터베이스에 저장 합니다. | [REST API를 사용 하 여 Azure 리소스에 대 한 사용자 지정 메트릭을 Azure Monitor 메트릭 저장소에 보냅니다.](metrics-store-custom-rest-api.md) |


## <a name="other-services"></a>기타 서비스
Azure의 다른 서비스는 Azure Monitor 데이터 플랫폼에 데이터를 씁니다. 이를 통해 Azure Monitor에서 수집한 데이터를 사용 하 여 이러한 서비스에 의해 수집 된 데이터를 분석 하 고 동일한 분석 및 시각화 도구를 활용할 수 있습니다.

| 서비스 | 대상 | Description | 참조 |
|:---|:---|:---|:---|
| [Azure Security Center](/azure/security-center/) | Azure Monitor 로그 | Azure Security Center은 수집 하는 보안 데이터를 Log Analytics 작업 영역에 저장 하 여 Azure Monitor에서 수집한 다른 로그 데이터로 분석할 수 있도록 합니다.  | [Azure Security Center에서 데이터 수집](../../security-center/security-center-enable-data-collection.md) |
| [Azure Sentinel](/azure/sentinel/) | Azure Monitor 로그 | Azure 센티널은 다른 데이터 원본에서 수집한 데이터를 Log Analytics 작업 영역에 저장 하 여 Azure Monitor에서 수집한 다른 로그 데이터를 사용 하 여 분석할 수 있도록 합니다.  | [데이터 원본 연결](/azure/sentinel/quickstart-onboard) |


## <a name="next-steps"></a>다음 단계

- [Azure Monitor에서 수집한 모니터링 데이터의 유형](data-platform.md)과 이 데이터를 보고 분석하는 방법에 대해 자세히 알아보세요.
- [Azure 리소스가 데이터를 저장](data-locations.md) 하는 다양 한 위치 및 액세스 하는 방법을 나열 합니다. 
