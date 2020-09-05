---
title: Azure Monitor 설명서의 새로운 기능
description: 매월 업데이트되는 Azure Monitor 설명서에 대한 중요 업데이트입니다.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 07/08/2020
ms.openlocfilehash: 10d4cb8160d0bf9111903b193574e9e820ded221
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87827744"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Azure Monitor 설명서의 새로운 기능이란?

이 문서에는 새롭거나 크게 업데이트된 Azure Monitor 문서가 나열되어 있습니다. 지난 달의 문서 업데이트가 포함되도록 매월 첫째 주에 새로 고쳐집니다.

## <a name="july-2020"></a>2020년 7월

### <a name="general"></a>일반
- [Azure Monitor 배포](deploy-scale.md) - VM용 Azure Monitor 온보딩 콘텐츠의 재구성
- [Azure Private Link를 사용하여 네트워크를 Azure Monitor에 안전하게 연결](platform/private-link-security.md) - 제한에 대한 섹션이 추가됨

### <a name="alerts"></a>경고
- [Azure Monitor 경고에 대한 작업 규칙](platform/alerts-action-rules.md) - CLI 프로세스가 추가됨
- [Azure Portal에서 작업 그룹 만들기 및 관리](platform/action-groups.md) - UI의 변경 내용을 반영하도록 업데이트됨
- [Azure Monitor Log Analytics에 저장된 쿼리](log-query/saved-queries.md) - 새 문서
- [Azure Monitor의 로그 경고 문제 해결](platform/alerts-troubleshoot-log.md) - 경고 규칙 할당량에 섹션이 추가됨
- [Azure 메트릭 경고 문제 해결](platform/alerts-troubleshoot-metric.md) - 아직 내보내지 않은 사용자 지정 메트릭의 경고 규칙에 대한 섹션이 추가됨
- [Azure Monitor 메트릭 경고의 작동 방식 이해](platform/alerts-metric-overview.md) - 집계 세분성 선택을 위한 권장 사항이 추가됨

### <a name="application-insights"></a>Application Insights
- [Azure 웹앱 확장에 대한 릴리스 정보 - Application Insights](app/web-app-extension-release-notes.md) - 새 문서
- [Application Insights 리소스에 대한 Resource Manager 템플릿 샘플](samples/resource-manager-app-resource.md) - 새 문서
- [Azure Application Insights Profiler 문제 해결](app/profiler-troubleshooting.md) - Azure App Service에서 ASP.NET Core 앱에 대해 프로파일러를 실행하는 버그에 대한 참고 사항이 추가됨 

### <a name="containers"></a>컨테이너
- [컨테이너의 Azure Monitor의 로그 경고](insights/container-insights-log-alerts.md) - 새 문서
- [컨테이너의 Azure Monitor의 메트릭 경고](insights/container-insights-metric-alerts.md) - 새 문서

### <a name="logs"></a>로그
- [Azure Monitor 고객 관리형 키](platform/customer-managed-keys.md) - 오류 메시지와 쿼리를 위한 CMK 구성 섹션이 추가됨
- [Azure Monitor HTTP 데이터 수집기 API](platform/data-collector-api.md) - Python 3 샘플이 추가됨
- [Azure Monitor에서 로그 쿼리 최적화](log-query/query-optimization.md) - 하위 쿼리를 사용할 때 여러 데이터 검색 방지에 대한 섹션이 추가됨
- [자습서: Log Analytics 쿼리 시작](log-query/get-started-portal.md) - 비디오가 추가됨

### <a name="platform-logs"></a>플랫폼 로그
- [플랫폼 로그 및 메트릭을 다른 대상으로 전송하는 진단 설정 만들기](platform/diagnostic-settings.md) - 비디오가 추가됨
- [Azure Monitor에 대한 Resource Manager 템플릿 샘플](samples/resource-manager-samples.md) - 로그 대상 유형을 사용하는 ARM 샘플이 추가됨 

### <a name="solutions"></a>솔루션
- [Azure Monitor의 모니터링 솔루션](insights/solutions.md) - CLI 프로세스가 추가됨
- [Azure에서 Office 365 관리 솔루션](insights/solution-office-365.md) - 사용 중지 날짜가 변경됨

### <a name="virtual-machines"></a>가상 머신

VM용 Azure Monitor 콘텐츠의 재구성에서 새로운 문서 및 업데이트된 문서

- [VM용 Azure Monitor란?](insights/vminsights-overview.md)
- [VM용 Azure Monitor에 대해 Log Analytics 작업 영역 구성](insights/vminsights-configure-workspace.md)
- [Azure Monitor에 Linux 컴퓨터 연결](platform/agent-linux.md)
- [하이브리드 환경에 대한 Azure Monitor 사용](insights/vminsights-enable-hybrid.md)
- [Azure Portal에서 단일 가상 머신 또는 가상 머신 확장 집합에 대한 Azure Monitor 사용](insights/vminsights-enable-portal.md)
- [Azure Policy를 사용하여 VM용 Azure Monitor를 사용하도록 설정](insights/vminsights-enable-at-scale-policy.md)
- [VM용 Azure Monitor 사용 개요](insights/vminsights-enable-overview.md)
- [PowerShell을 사용하여 VM용 Azure Monitor 사용](insights/vminsights-enable-powershell.md)
- [Resource Manager 템플릿을 사용하여 VM용 Azure Monitor 사용](insights/vminsights-enable-resource-manager.md)
- [PowerShell 또는 템플릿을 사용하여 VM용 Azure Monitor 사용](insights/vminsights-enable-at-scale-powershell.md)


### <a name="visualizations"></a>시각화
- [Log Analytics 대시보드 시각화 업그레이드](log-query/dashboard-upgrade.md) - 새로 고침 빈도가 업데이트됨
- [Azure Monitor의 데이터 시각화](visualizations.md) - 비디오가 추가됨


## <a name="june-2020"></a>2020년 6월

### <a name="general"></a>일반
- [Azure Monitor 배포](deploy-scale.md) - 새 문서.
- [Azure Monitor 고객 관리형 키](platform/customer-managed-keys.md) - 업데이트된 billingtype 속성. PowerShell 명령이 추가되었습니다.

### <a name="agents"></a>에이전트
- [Log Analytics 에이전트 개요](platform/log-analytics-agent.md) - Python 2 요구 사항이 추가되었습니다.

### <a name="alerts"></a>경고
- [대상 리소스가 다른 Azure 지역으로 이동할 때 경고 규칙 또는 작업 규칙을 업데이트하는 방법](platform/alerts-resource-move.md) - 새 문서.
- [Azure 메트릭 경고 문제 해결](platform/alerts-troubleshoot-metric.md) - 새 문서.
- [Azure Monitor에서 로그 경고 문제 해결](platform/alerts-troubleshoot-metric.md) - 새 문서.
  
### <a name="application-insights"></a>Application Insights
- [JavaScript 웹앱용 Azure Application Insights](app/javascript.md) - JavaScript SDK 섹션으로 업데이트합니다. 로드 오류를 보고하도록 코드 조각을 업데이트했습니다.
- [프로필 및 스냅샷 디버거에 대한 BYOS(사용자 고유의 스토리지 가져오기) 구성](app/profiler-bring-your-own-storage.md) - 새 문서.
- [OpenCensus Python을 사용하여 Azure Application Insights에서 들어오는 요청 추적](app/opencensus-python-request.md) - OpenCensus에 대한 로깅 및 구성을 업데이트했습니다.
- [Azure Application Insights를 사용하여 라이브 ASP.NET 웹앱 모니터링](app/monitor-performance-live-website-now.md) - 상태 모니터 v1의 사용 중단 날짜가 업데이트되었습니다.
- [Azure Application Insights를 사용하여 Node.js 서비스 모니터링](app/nodejs.md) - 이전 버전 및 SDK 구성에서 마이그레이션을 비롯한 여러 업데이트
- [Azure Monitor(미리 보기)를 사용하여 Python 애플리케이션 모니터링](app/opencensus-python.md) - Azure Monitor 내보내기 구성에 대한 섹션을 추가했습니다.
- [코드 변경 없이 앱 모니터링 - Azure Monitor Application Insights에 대한 자동 계측](app/codeless-overview.md) - 새 문서.
- [JavaScript 웹 애플리케이션에 대한 SDK 로드 실패 문제 해결](app/javascript-sdk-load-failure.md) - 새 문서.

### <a name="containers"></a>컨테이너
- [하이브리드 Kubernetes 클러스터 모니터링을 중지하는 방법](insights/container-insights-optout-hybrid.md) - Arc 지원 Kubernetes에 대한 섹션을 추가했습니다.
- [컨테이너에 대한 Azure Monitor를 사용하여 Azure Arc 지원 Kubernetes 클러스터 구성](insights/container-insights-enable-arc-enabled-clusters.md) - 새 문서.
- [컨테이너에 대한 Azure Monitor를 사용하여 Azure Red Hat OpenShift v4.x 구성](insights/container-insights-azure-redhat4-setup.md) - 필수 구성 요소가 업데이트되었습니다.
- [컨테이너 Live Data에 대한 Azure Monitor 설정(미리 보기)](insights/container-insights-livedata-setup.md) - Azure 미국 정부에서 사용할 수 없는 기능에 대한 정보가 제거되었습니다.

### <a name="insights"></a>자세한 정보
- [FAQ - Azure의 네트워크 성능 모니터 솔루션](insights/network-performance-monitor-faq.md) - ExpressRoute 모니터에 대한 FAQ가 추가되었습니다.

### <a name="logs"></a>로그
- [Azure Log Analytics 작업 영역 삭제 및 복구](platform/delete-workspace.md) - PowerShell 명령이 추가되었습니다. 문제 해결이 업데이트되었습니다.
- [Azure Monitor에서 Log Analytics 작업 영역 관리](platform/manage-access.md) - RBAC 섹션에 허용되지 않는 테이블에 대한 예제가 추가되었습니다.
- [Azure Monitor 로그의 사용량 및 비용 관리](platform/manage-cost-storage.md) - 데이터 크기 계산에 대한 추가 세부 정보. 데이터 볼륨 경고 구성을 업데이트했습니다. Azure Sentinel에서 수집한 보안 데이터에 대한 세부 정보. 데이터 캡에 대한 설명.
- [Azure Logic Apps와 Power Automate로 Azure Monitor 로그 사용](platform/logicapp-flow-connector.md) - 커넥터 제한이 추가되었습니다.

### <a name="metrics"></a>메트릭
- [리소스 종류별 Azure Monitor 지원 메트릭](platform/metrics-supported.md) - SQL Server 메트릭이 업데이트되었습니다.


### <a name="platform-logs"></a>플랫폼 로그

- [진단 설정에 대한 Resource Manager 템플릿 샘플](samples/resource-manager-diagnostic-settings.md) - 활동 로그 진단 설정 수정.
- [Azure Portal을 사용하여 Log Analytics 작업 영역에 Azure 활동 로그 보내기](learn/quick-collect-activity-log-portal.md) - 새 문서.
- [Azure Resource Manager 템플릿을 사용하여 Log Analytics 작업 영역에 Azure 활동 로그 보내기](learn/quick-collect-activity-log-arm.md) - 새 문서.

플랫폼 로그 콘텐츠 재구성 및 통합의 새로운 문서 및 업데이트된 문서

- [스토리지 계정에 Azure 리소스 로그 보관](./platform/resource-logs.md#send-to-azure-storage)
- [Azure 활동 로그 이벤트 스키마](platform/activity-log-schema.md)
- [Azure 활동 로그](platform/activity-log.md)
- [Azure Monitor CLI 샘플](samples/cli-samples.md)
- [Azure Monitor PowerShell 샘플](samples/powershell-samples.md)
- [Azure Monitoring REST API 연습](platform/rest-api-walkthrough.md)
- [Azure 리소스 로그 지원 서비스 및 스키마](./platform/resource-logs-schema.md)
- [Azure 리소스 로그](platform/resource-logs.md)
- [Azure Monitor에서 Azure 활동 로그 수집 및 분석](./platform/activity-log.md)
- [Log Analytics 작업 영역에서 Azure 리소스 로그 수집](./platform/resource-logs.md#send-to-log-analytics-workspace)
- [플랫폼 로그 및 메트릭을 다른 대상으로 전송하는 진단 설정 만들기](platform/diagnostic-settings.md)
- [Azure 활동 로그 내보내기](./platform/activity-log.md#legacy-collection-methods)
- [Azure 플랫폼 로그 개요](platform/platform-logs-overview.md)
- [이벤트 허브로 Azure 플랫폼 로그 스트림](./platform/resource-logs.md#send-to-azure-event-hubs)
- [Azure Monitor에서 Azure 활동 로그 이벤트 보기](./platform/activity-log.md#view-the-activity-log)

### <a name="virtual-machines"></a>가상 머신
- [Azure Portal에서 VM용 Azure Monitor 사용](./insights/vminsights-enable-portal.md) - Azure Arc를 포함하도록 업데이트되었습니다.
- [VM용 Azure Monitor 사용 개요](insights/vminsights-enable-overview.md) - Azure Arc를 포함하도록 업데이트되었습니다.
- [VM용 Azure Monitor란?](insights/vminsights-overview.md) - Azure Arc를 포함하도록 업데이트되었습니다.


### <a name="visualizations"></a>시각화
- [Azure Monitor 통합 문서 데이터 원본](platform/workbooks-data-sources.md) - 경고 및 사용자 지정 엔드포인트 섹션이 추가되었습니다.
- [Azure Monitor 통합 문서 기반 인사이트 문제 해결](insights/troubleshoot-workbooks.md) - 새 문서.
- [Log Analytics 대시보드 시각화 업그레이드](log-query/dashboard-upgrade.md) - 새 문서.



## <a name="may-2020"></a>2020년 5월

### <a name="general"></a>일반

- [Azure Monitor FAQ](faq.md) - 메트릭에 대한 섹션이 추가됨.
- [Azure Monitor 고객 관리형 키](platform/customer-managed-keys.md) - 일반 공급을 위한 다양한 변경 내용.
- [Azure Monitor에 대한 기본 제공 정책 정의](samples/policy-samples.md) - 새 문서
- [로그 수집을 위한 고객 소유 스토리지 계정](platform/private-storage.md) - 새 문서
- [Azure Monitor 로그 사용량 및 비용 관리](platform/manage-cost-storage.md) - 클러스터에 대한 비례 요금 청구가 추가됨.
- [Azure Private Link를 사용하여 네트워크를 Azure Monitor에 안전하게 연결](platform/private-link-security.md) - 새 문서


#### <a name="new-resource-manager-template-samples"></a>새 Resource Manager 템플릿 샘플 
- [Azure Monitor에 대한 Resource Manager 템플릿 샘플](samples/resource-manager-samples.md)
- [작업 그룹에 대한 Resource Manager 템플릿 샘플](samples/resource-manager-action-groups.md)
- [에이전트에 대한 Resource Manager 템플릿 샘플](samples/resource-manager-agent.md)
- [컨테이너용 Azure Monitor에 대한 Resource Manager 템플릿 샘플](samples/resource-manager-container-insights.md)
- [VM용 Azure Monitor에 대한 Resource Manager 템플릿 샘플](samples/resource-manager-vminsights.md)
- [진단 설정에 대한 Resource Manager 템플릿 샘플](samples/resource-manager-diagnostic-settings.md)
- [Log Analytics 작업 영역에 대한 Resource Manager 템플릿 샘플](samples/resource-manager-workspace.md)
- [로그 쿼리에 대한 Resource Manager 템플릿 샘플](samples/resource-manager-log-queries.md)
- [로그 쿼리 경고 규칙에 대한 Resource Manager 템플릿 샘플](samples/resource-manager-alerts-log.md)
- [메트릭 경고 규칙에 대한 Resource Manager 템플릿 샘플](samples/resource-manager-alerts-metric.md)
- [통합 문서에 대한 Resource Manager 템플릿 샘플](samples/resource-manager-workbooks.md)

### <a name="agents"></a>에이전트
- [WAD (Windows Azure 진단 확장) 설치 및 구성](platform/diagnostics-extension-windows-install.md) - 진단 구성에 대한 세부 정보가 추가됨.
- [Log Analytics 에이전트 개요](platform/log-analytics-agent.md) - 지원되는 Linux 버전이 추가됨.

### <a name="application-insights"></a>Application Insights

- [Application Insights를 사용하여 Azure Functions에서 실행되는 애플리케이션 모니터링 - Azure Monitor](app/monitor-functions.md) - 새 문서
- [Azure Application Insights를 사용하여 node.js 서비스를 모니터링](app/nodejs.md) - 이전 버전의 마이그레이션에 대한 새로운 섹션을 비롯한 일반 업데이트
- [Application Insights 및 Log Analytics에서 사용하는 IP 주소](app/ip-addresses.md) - 웹 후크 및 미국 정부에 대한 IP 주소가 추가됨.
- [Application Insights를 사용하여 AKS(Azure Kubernetes Service)에서 애플리케이션 모니터링](app/kubernetes-codeless.md) - 새 문서
- [데이터 없은 문제 해결 - .NET에 대한 Application Insights](app/asp-net-troubleshoot-no-data.md) - dotnet 추적을 사용하여 로그 수집에 추가된 섹션
- [Azure Monitor에서 애플리케이션 변경 분석을 사용하여 웹-앱 문제 발견](app/change-analysis.md) - 변경 분석 기능에서 다수 업데이트

#### <a name="new-and-updated-articles-for-preview-of-workspace-based-applications"></a>작업 영역 기반 애플리케이션의 미리 보기에 대한 새 문서 및 업데이트된 문서
- [Azure Monitor Application Insights 작업 영역 기반 리소스 스키마](app/apm-tables.md)
- [새 Azure Monitor Application Insights 작업 영역 기반 리소스 만들기](app/create-workspace-resource.md)
- [Azure Monitor 로그 쿼리의 앱() 식](log-query/app-expression.md)
- [Azure Monitor Log Analytics의 로그 쿼리 범위](log-query/scope.md)
- [Azure Monitor로 리소스 쿼리](log-query/cross-workspace-query.md)
- [Azure Monitor 로그 레코드의 표준 속성](platform/log-standard-properties.md)
- [Azure Monitor 로그의 구조](log-query/logs-structure.md)





### <a name="containers"></a>컨테이너
- [컨테이너에 대한 Azure Monitor 사용 방법](insights/container-insights-onboard.md) - 업데이트된 방화벽 구성 테이블
- [메트릭용 컨테이너에 대한 Azure Monitor 업데이트 방법](insights/container-insights-update-metrics.md) - 관리되는 ID를 사용하여 메트릭을 수집하도록 업데이트
- [컨테이너의 Azure Monitor에 대한 모니터링 비용](insights/container-insights-cost.md) - 새 문서
- [컨테이너 라이브 데이터 (미리 보기)에 대한 Azure Monitor 설정](insights/container-insights-livedata-setup.md) - 새 클러스터 역할 바인딩에 대한 지원

### <a name="insights"></a>자세한 정보
- [Azure Cache for Redis용 Azure Monitor(미리 보기)](insights/redis-cache-insights-overview.md) - 새 문서
- [Azure Monitor for Key Vault(미리 보기)를 사용하여 Key Vault 모니터링](./insights/key-vault-insights-overview.md) - 새 문서

### <a name="logs"></a>로그
- [PowerShell을 사용하여 Log Analytics 설정 & 구성](platform/powershell-workspace-configuration.md) - 문제 해결 섹션을 추가
- [Azure Portal에서 Log Analytics 작업 영역 생성](learn/quick-create-workspace.md) - 문제 해결 섹션을 추가
- [Azure CLI를 사용하여 Log Analytics 작업 영역을 생성](learn/quick-create-workspace-cli.md) - 문제 해결 섹션을 추가
- [Azure Log Analytics 작업 영역 삭제 및 복구](platform/delete-workspace.md) - 삭제된 작업 영역 복구에 대한 정보가 업데이트
- [Azure Monitor 로그 쿼리 함수](log-query/functions.md) - 다른 함수를 포함하지 않는 함수에 대한 메모를 제거
- [Azure Monitor 로그의 구조](log-query/logs-structure.md)-Application Insights 테이블에 대한 속성 설명이 명시됨
- [Azure Logic Apps와 Power Automate로 Azure Monitor 로그를 사용](platform/logicapp-flow-connector.md) - 한도 섹션을 추가함
- [PowerShell을 사용하여 Log Analytics 작업 영역 생성 및 구성](platform/powershell-workspace-configuration.md) - 문제 해결 섹션을 추가함


### <a name="metrics"></a>메트릭
- [리소스 유형별로 Azure Monitor 지원 메트릭](platform/metrics-supported.md) - 게스트 메트릭 및 메트릭 라우팅을 설명함. 

### <a name="solutions"></a>솔루션
- [Azure Monitor로 Active Directory 환경 최적화](insights/ad-assessment.md) - 지원된 버전에 Windows Server 2019를 추가함.
- [Azure Monitor로 SQL Server 환경 최적화](insights/sql-assessment.md) - SQL Server의 지원 버전을 추가함.


### <a name="virtual-machines"></a>가상 머신
- [VM용 Azure Monitor 사용 개요](insights/vminsights-enable-overview.md) - Ubuntu 서버의 지버전원 을 추가함. Log Analytics 작업 영역에 대해 지원되는 지역 추가
- [VM용 Azure Monitor로 성능을 차트로 만드는 방법](insights/vminsights-performance.md) - 사용할 수 없는 메트릭에 대한 제한 사항 섹션을 추가함.

### <a name="visualizations"></a>시각화
- [Azure Monitor 통합 문서 및 Azure Resource Manager 템플릿](platform/workbooks-automate.md) - 통합 문서 템플릿을 배포하기 위한 Resource Manager 템플릿을 추가함.
- [Azure Monitor 통합 문서 그룹](platform/workbooks-groups.md) - 새 문서
- [Azure Monitor 통합 문서-JSONPath를 사용하여 JSON 데이터 변환](platform/workbooks-jsonpath.md) - 새 문서


## <a name="april-2020"></a>2020년 4월

### <a name="general"></a>일반

- [Azure Monitor 고객 관리형 키](platform/customer-managed-keys.md) - 비동기 작업에 대한 섹션이 추가됨.
- [Azure Monitor에서 Log Analytics 작업 영역 관리](platform/manage-access.md) - 사용자 지정 로그 섹션이 업데이트됨.

### <a name="alerts"></a>경고

- [Azure Monitor 경고에 대한 작업 규칙](platform/alerts-action-rules.md) - 비디오가 추가됨.
- [Azure의 경고 및 알림 모니터링 개요](platform/alerts-overview.md) - 비디오가 추가됨.

### <a name="application-insights"></a>Application Insights

- [Azure Application Insights의 애플리케이션 맵](app/app-map.md) - Java 에이전트에 대한 클라우드 역할 이름 구성이 추가됨.
- [Azure Application Insights .NET 에이전트 API 참조](app/status-monitor-v2-api-reference.md) - API 참조가 통합됨.
- [Application Insights 및 Log Analytics에서 사용되는 IP 주소](app/ip-addresses.md) - App Insights 및 Log Analytics API, 작업 그룹 웹후크, Azure US Government에 대한 IP 주소가 업데이트됨.
- [어디서나 Java 애플리케이션 모니터링](app/java-standalone-config.md) - 새 문서
- [모든 환경에서 Java 애플리케이션 모니터링](app/java-in-process-agent.md) - 새 문서
- [모든 환경에서 실행되는 Java 애플리케이션 모니터링](app/java-standalone-arguments.md) - 새 문서
- [온-프레미스에서 실행되는 Java 애플리케이션 모니터링](app/java-on-premises.md) - 새 문서
- [Visual Studio에서 Application Insights 제거](app/remove-application-insights.md) - 새 문서
- [Azure Application Insights의 원격 분석 샘플링](app/sampling.md) - Python에서 고정 요율 샘플로 수정함.

### <a name="containers"></a>컨테이너

- [컨테이너에 대한 Azure Monitor를 사용하여 Azure Red Hat OpenShift v4.x 구성](insights/container-insights-azure-redhat4-setup.md) - 새 문서
- [ServiceNow 동기화 문제를 수동으로 해결하는 방법](platform/itsmc-resync-servicenow.md) - 새 문서
- [Azure 및 Red Hat OpenShift v4 클러스터 모니터링을 중지하는 방법](insights/container-insights-optout-openshift-v4.md) - 새 문서
- [Azure Red Hat OpenShift v3 클러스터 모니터링을 중지하는 방법](insights/container-insights-optout-openshift-v3.md) - 새 문서
- [하이브리드 Kubernetes 클러스터 모니터링을 중지하는 방법](insights/container-insights-optout-hybrid.md) - 새 문서

### <a name="insights"></a>자세한 정보

- [Key Vault용 Azure Monitor를 사용하여 Azure Key Vault 모니터링(미리 보기)](insights/key-vault-insights-overview.md) - 새 문서

### <a name="logs"></a>로그

- [Azure Monitor 서비스 제한](service-limits.md) - 사용자 쿼리 제한이 추가됨.
- [Azure Monitor 로그 사용량 및 비용 관리](platform/manage-cost-storage.md) - 로그 클러스터에 대한 요금 청구가 추가됨. 레거시 노드당 가격 책정 계층을 사용하는 고객이 GB당 또는 용량 예약 계층으로 이동할지 여부를 결정할 수 있도록 Kusto 쿼리가 추가되었습니다.

### <a name="metrics"></a>메트릭

- [Azure 메트릭 탐색기의 고급 기능](platform/metrics-charts.md) - 집계 섹션이 추가됨.

### <a name="workbooks"></a>통합 문서

- [Azure Monitor 통합 문서 및 Azure Resource Manager 템플릿](platform/workbooks-automate.md) - 통합 문서 템플릿을 배포하기 위한 Resource Manager 템플릿이 추가됩니다.

## <a name="march-2020"></a>2020년 3월

### <a name="general"></a>일반

- [Azure Monitor 개요](overview.md) - Azure Monitor 개요 비디오가 추가되었습니다.
- [Azure Monitor 고객 관리형 키 구성](platform/customer-managed-keys.md) - 일반 업데이트입니다.
- [Azure Monitor 데이터 참조](/azure/azure-monitor/reference/) - 새 사이트입니다.

### <a name="alerts"></a>경고

- [Azure Monitor에서 활동 로그 경고 만들기, 보기 및 관리](platform/alerts-activity-log.md) - Resource Manager 템플릿에 대한 추가 설명입니다.
- [Azure Monitor 메트릭 경고의 작동 방식 이해](platform/alerts-metric-overview.md) - 정부 지원을 위해 업데이트되었습니다.
- [Azure Monitor 경고 및 알림 문제 해결](platform/alerts-troubleshoot.md) - 새 문서

### <a name="application-insights"></a>Application Insights

- [PowerShell을 사용하여 Azure Application Insights 자동화](app/powershell.md) - 추가된 ARMClient 예제입니다.
- [Application Insights에서 원격 분석 연속 내보내기](app/export-telemetry.md)- 내보내기 구조의 세부 정보가 포함된 표를 추가합니다.
- [Azure App Service에서 .NET 앱용 스냅샷 디버거 사용](app/snapshot-debugger-appservice.md) - 추가된 Resource Manager 템플릿 예제입니다.
- [Azure Application Insights의 사용량 및 비용 관리](app/pricing.md) - 데이터 제한 경고에 대한 추가 정보입니다.
- [Azure Monitor(미리 보기)로 Python 애플리케이션 모니터링](app/opencensus-python.md) - 추가된 표준 메트릭입니다.
- [JavaScript 애플리케이션에 대한 소스 맵 지원 - Azure Monitor Application Insights](app/source-map-support.md) - 새 문서입니다.

### <a name="containers"></a>컨테이너

- [Azure Monitor FAQ](faq.md) - 컨테이너용 Azure Monitor에 대한 업데이트입니다.
- [컨테이너용 Azure Monitor를 사용하여 GPU 모니터링 구성](insights/container-insights-gpu-monitoring.md) - 새 문서

### <a name="insights"></a>자세한 정보

- [Azure의 Office 365 관리 솔루션](insights/solution-office-365.md) - 업데이트된 사용 중단 날짜

### <a name="logs"></a>로그

- [Azure Monitor에서 로그 쿼리 최적화](log-query/query-optimization.md) - XML 및 JSON 구문 분석을 위해 추가된 CPU 조건입니다.
- [Azure Log Analytics 작업 영역 삭제 및 복구](platform/delete-workspace.md) - 추가된 문제 해결 방법입니다.
- [Azure Logic Apps 및 Power Automate와 함께 Azure Monitor 로그 사용](platform/logicapp-flow-connector.md) - 새 Azure Monitor 커넥터에 대한 내용이 업데이트되었습니다.

### <a name="metrics"></a>메트릭

- [Azure Portal에서 디스크 메트릭 사용 중단](platform/portal-disk-metrics-deprecation.md)- 새 문서입니다.
- [자습서 - Azure Monitor에서 메트릭 차트 만들기](learn/tutorial-metrics-explorer.md) - 추가된 비디오입니다.

### <a name="platform-logs"></a>플랫폼 로그

- [Azure Monitor에서 Azure 활동 로그 수집 및 분석](./platform/activity-log.md) - 진단 설정을 사용하여 활동 로그를 수집하는 방법을 보다 정확하게 설명하기 위해 다시 작성 되었습니다.

### <a name="virtual-machines"></a>가상 머신

- [Azure Monitor를 사용하여 Azure 가상 머신 모니터링](insights/monitor-vm-azure.md) - 새 문서입니다.
- [빠른 시작: Azure Monitor를 사용하여 Azure 가상 머신 모니터링](learn/quick-monitor-azure-vm.md) - VM용 Azure Monitor를 추가하기 위해 업데이트되었습니다.
- [VM용 Azure Monitor의 경고](insights/vminsights-alerts.md) - 새 문서
- [VM용 Azure Monitor 사용 개요](insights/vminsights-enable-overview.md) - 업데이트된 에이전트 다운로드 링크입니다.

VM용 Azure Monitor의 일반 공급에 대한 일반 업데이트

- [VM용 Azure Monitor란?](insights/vminsights-overview.md)
- [VM용 Azure Monitor(GA) 질문과 대답](insights/vminsights-ga-release-faq.md) 
- [Azure Policy를 사용하여 VM용 Azure Monitor를 사용하도록 설정](./insights/vminsights-enable-policy.md) 
- [VM용 Azure Monitor를 사용하여 성능을 차트로 표시하는 방법](insights/vminsights-performance.md)
- [VM용 Azure Monitor에서 로그를 쿼리하는 방법](insights/vminsights-log-search.md)
- [VM용 Azure Monitor를 사용하여 앱 종속성 보기](insights/vminsights-maps.md) 

### <a name="visualizations"></a>시각화

- [Azure Monitor에서 데이터 시각화](visualizations.md) - 뷰 디자이너의 계획된 사용 중단 소식을 알려주도록 업데이트되었습니다.

## <a name="february-2020"></a>2020년 2월

### <a name="agents"></a>에이전트

진단 확장 콘텐츠 재작성의 일부로 여러 개 업데이트

- [Azure 모니터링 에이전트 개요](platform/agents-overview.md) - 각 에이전트의 고유한 기능을 보다 명확하게 설명하기 위해 재구성된 테이블
- [Azure Diagnostics 확장 개요](platform/diagnostics-extension-overview.md) - 다시 작성을 완료합니다.
- [Azure Monitor에서 IIS에 대해 Blob 스토리지 및 이벤트에 대해 테이블 스토리지 사용](platform/diagnostics-extension-logs.md) - 업데이트 및 명확성을 위한 일반 재작성
- [Windows Azure 진단 확장(WAD) 설치 및 구성](platform/diagnostics-extension-windows-install.md) - 새 문서 
- [Windows 진단 확장 스키마](platform/diagnostics-extension-schema-windows.md) - 재구성됨
- [Microsoft Azure 진단 확장에서 Azure Event Hubs로 데이터 전송](platform/diagnostics-extension-stream-event-hubs.md) - 완전히 다시 작성되고 업데이트됨
- [Azure Storage에서 진단 데이터 저장 및 보기](../cloud-services/diagnostics-extension-to-storage.md) - 완전히 다시 작성되고 업데이트됨
- [Windows용 Log Analytics 가상 머신 확장](../virtual-machines/extensions/oms-windows.md) - Log Analytics 에이전트와의 관계를 보다 명확하게 구분합니다.
- [Linux용 Azure Monitor 가상 머신 확장](../virtual-machines/extensions/oms-linux.md) - Log Analytics 에이전트와의 관계를 보다 명확하게 구분합니다.

### <a name="application-insights"></a>Application Insights

- [Azure Application Insights의 연결 문자열](app/sdk-connection-string.md) - 새 문서

### <a name="insights-and-solutions"></a>인사이트 및 솔루션

#### <a name="azure-monitor-for-containers"></a>컨테이너용 Azure Monitor

- [Azure Kubernetes Service와 Azure Active Directory 통합](../aks/azure-ad-integration-cli.md) - RBAC 지원 클러스터를 지원하여 컨테이너에 대한 Azure Monitor를 지원하도록 클라이언트 애플리케이션을 만들기 위한 메모를 추가했습니다.

#### <a name="azure-monitor-for-vms"></a>VM용 Azure Monitor

- [VM용 Azure Monitor(GA) 질문과 대답](insights/vminsights-ga-release-faq.md) - 성능 데이터를 저장하는 방법에 대한 변경 내용

#### <a name="office-365"></a>Office 365

- [Azure의 Office 365 관리 솔루션](insights/solution-office-365.md) - 업데이트된 사용 중단 날짜


### <a name="logs"></a>로그

- [Azure Monitor에서 로그 쿼리 최적화](log-query/query-optimization.md) - 새 문서
- [Azure Monitor 로그에 대한 사용량 및 비용 관리](platform/manage-cost-storage.md) - 사용량을 이해하는 데 도움이 되는 향상된 샘플 쿼리

### <a name="metrics"></a>메트릭

- [진단 설정을 통해 내보낼 수 있는 Azure Monitor 플랫폼 메트릭](platform/metrics-supported-export-diagnostic-settings.md) - Null 및 0 값에 대한 동작 변경 내용에 추가된 섹션

### <a name="visualizations"></a>시각화

뷰 디자이너에서 통합 문서로 전환 가이드에 대한 여러 가지 새 문서

- [Azure Monitor 뷰 디자이너에서 통합 문서로 전환 가이드](platform/view-designer-conversion-overview.md) - 새 문서
- [Azure Monitor 뷰 디자이너에서 통합 문서로 전환 옵션](platform/view-designer-conversion-options.md) - 새 문서
- [Azure Monitor 뷰 디자이너에서 통합 문서로 타일 전환](platform/view-designer-conversion-tiles.md) - 새 문서
- [Azure Monitor 뷰 디자이너에서 통합 문서로 전환 요약 및 액세스](platform/view-designer-conversion-access.md) - 새 문서
- [Azure Monitor 뷰 디자이너에서 통합 문서로 전환 일반 작업](platform/view-designer-conversion-tasks.md) - 새 문서
- [Azure Monitor 뷰 디자이너에서 통합 문서로 전환 예제](platform/view-designer-conversion-examples.md) - 새 문서

## <a name="january-2020"></a>2020년 1월

### <a name="general"></a>일반

- [Azure Monitor에서 모니터링하는 항목](monitor-reference.md) - 새 문서

### <a name="agents"></a>에이전트

- [Azure Log Analytics 에이전트를 사용하여 로그 데이터 수집](platform/log-analytics-agent.md) - 네트워크 방화벽 요구 사항 표가 업데이트되었습니다.

### <a name="alerts"></a>경고

- [Azure Portal에서 작업 그룹 만들기 및 관리](platform/action-groups.md) - 더 이상 필요하지 않은 v2 기능에 대한 설정이 제거되었습니다.
- [Resource Manager 템플릿을 사용하여 메트릭 경고 만들기](platform/alerts-metric-create-templates.md) - *ignoreDataBefore* 매개 변수에 대한 예제가 추가되었습니다.  다중 조건 규칙에 대한 제약 조건이 추가되었습니다.
- [Log Analytics 경고 REST API 사용](platform/api-alerts.md) - JSON 예제가 수정되었습니다.

### <a name="application-insights"></a>Application Insights

- [Application Insights 및 Log Analytics에서 사용하는 IP 주소](app/ip-addresses.md) - 가용성 테스트 섹션에서 Azure 네트워크 보안 그룹을 사용하여 트래픽을 허용하는 인바운드 포트 규칙을 추가하는 방법을 포함하도록 업데이트되었습니다.
- [Azure Application Insights Profiler 관련 문제 해결](app/profiler-troubleshooting.md) - 일반적인 문제 해결이 업데이트되었습니다.
- [Azure Application Insights의 원격 분석 샘플링](app/sampling.md) - 고객 의견에 따라 가독성을 향상시킬 수 있도록 업데이트 및 재구성되었습니다.

### <a name="data-security"></a>데이터 보안

- [Azure Monitor 고객 관리형 키 구성](platform/customer-managed-keys.md) - 새 문서

### <a name="insights-and-solutions"></a>인사이트 및 솔루션

#### <a name="azure-monitor-for-containers"></a>컨테이너용 Azure Monitor

- [컨테이너용 Azure Monitor 에이전트 데이터 수집 구성](insights/container-insights-agent-config.md) - Azure Red Hat OpenShift에서 에이전트를 업그레이드하는 방법에 대한 세부 정보 및 에이전트를 업그레이드 방법을 구분하기 위한 추가 정보가 추가되었습니다.
- [컨테이너용 Azure Monitor 성능 경고 만들기](insights/container-insights-alerts.md) - 작업 영역 컨텍스트 경고를 사용하여 작업 영역에 저장된 성능 데이터에 대한 경고를 만드는 방법에 대한 정보가 수정되고 단계가 업데이트되었습니다.
- [컨테이너용 Azure Monitor를 사용하여 Kubernetes 모니터링](insights/container-insights-analyze.md) - Windows Kubernetes 클러스터 지원에 대한 개요 문서와 분석 문서가 모두 업데이트되었습니다.
- [컨테이너용 Azure Monitor를 사용하여 Azure Red Hat OpenShift 클러스터 구성](insights/container-insights-azure-redhat-setup.md) - Azure Red Hat OpenShift에서 에이전트를 업그레이드하는 방법에 대한 세부 정보 및 에이전트를 업그레이드 방법을 구분하기 위한 추가 정보가 추가되었습니다.
- [컨테이너용 Azure Monitor를 사용하여 하이브리드 Kubernetes 클러스터 구성](insights/container-insights-hybrid-setup.md) - Kubelet의 cAdvisor를 사용하여 secure port:10250에 대한 추가 지원을 반영하도록 업데이트되었습니다.
- [컨테이너용 Azure Monitor 에이전트를 관리하는 방법](insights/container-insights-manage-agent.md) - 다른 유형의 Kubernetes 클러스터에 비해 Azure Red Hat OpenShift를 사용한 메트릭 스크랩의 동작 및 구성과 관련된 세부 정보가 업데이트되었습니다.
- [컨테이너용 Azure Monitor Prometheus 통합 구성](insights/container-insights-prometheus-integration.md) - 다른 유형의 Kubernetes 클러스터와 비교하여 Azure Red Hat OpenShift를 사용한 메트릭 스크래핑의 동작 및 구성과 관련된 세부 정보가 업데이트되었습니다.
- [메트릭에 맞게 컨테이너용 Azure Monitor를 업데이트하는 방법](insights/container-insights-update-metrics.md) - 다른 유형의 Kubernetes 클러스터에 비해 Azure Red Hat OpenShift를 사용한 메트릭 스크랩의 동작 및 구성과 관련된 세부 정보가 업데이트되었습니다.

#### <a name="azure-monitor-for-vms"></a>VM용 Azure Monitor

- [VM용 Azure Monitor(GA) 질문과 대답](insights/vminsights-ga-release-faq.md) - 작업 영역과 에이전트를 새 버전으로 업그레이드하는 방법에 대한 정보가 추가되었습니다.

#### <a name="office-365"></a>Office 365

- [Azure의 Office 365 관리 솔루션](insights/solution-office-365.md) - Azure Sentinel에서 Office 365 솔루션으로 마이그레이션하는 방법에 대한 세부 정보와 FAQ가 추가되었습니다. 온보딩 섹션이 제거되었습니다.

### <a name="logs"></a>로그

- [Azure Monitor에서 Log Analytics 작업 영역 관리](platform/manage-access.md) - '작업 없음'으로 업데이트되었습니다.
- [Azure Monitor 로그의 사용량 및 비용 관리](platform/manage-cost-storage.md) - 가격 책정 모델 섹션에서 데이터 볼륨 계산에 대한 설명이 추가되었습니다.
- [Azure Resource Manager 템플릿을 사용하여 Log Analytics 작업 영역 만들기 및 구성](platform/template-workspace-configuration.md) - 템플릿에서 새 가격 책정 계층을 사용하도록 업데이트되었습니다.

### <a name="platform-logs"></a>플랫폼 로그

- [진단 설정을 사용하여 Azure 활동 로그 수집 - Azure Monitor](./platform/activity-log.md) - 변경된 속성에 대한 정보가 추가되었습니다.
- [Azure 활동 로그 내보내기](./platform/activity-log.md#legacy-collection-methods) - 변경된 UI에 맞도록 업데이트되었습니다. 

## <a name="december-2019"></a>2019년 12월

### <a name="agents"></a>에이전트

- [Azure Monitor에 Linux 컴퓨터 연결](platform/agent-linux.md) - 새 문서

### <a name="alerts"></a>경고

- [Resource Manager 템플릿을 사용하여 메트릭 경고 만들기](platform/alerts-metric-create-templates.md) - 사용자 지정 메트릭에 대한 예제가 추가되었습니다.
- [Azure Monitor에서 동적 임계값을 사용하는 경고 만들기](platform/alerts-dynamic-thresholds.md) - 동적 임계값 차트를 해석하는 방법에 대한 섹션이 추가되었습니다.
- [Azure의 경고 및 알림 모니터링 개요](platform/alerts-overview.md) - 리소스 그래프 쿼리가 업데이트되었습니다.
- [Azure Monitor에서 메트릭 경고에 지원되는 리소스](platform/alerts-metric-near-real-time.md) - 지원되는 메트릭 및 차원이 업데이트되었습니다.
- [레거시 Log Analytics 경고 API에서 새 Azure 경고 API로 전환](platform/alerts-log-api-switch.md) - 수정된 경고 이름에 대한 참고 사항이 추가되었습니다.
- [Azure Monitor 메트릭 경고의 작동 방식 이해](platform/alerts-metric-overview.md) - 규모에 맞게 모니터링에 지원되는 리소스 종류가 추가되었습니다.

### <a name="application-insights"></a>Application Insights

- [Worker Service 앱(비 HTTP 앱)용 Application Insights](app/worker-service.md) - 기본 로깅 수준이 C# 코드에 추가되었습니다. 패키지 참조 버전이 업데이트되었습니다.
- [ApplicationInsights.config 참조](app/configuration-with-applicationinsights-config.md) - 샘플 코드가 업데이트되었습니다.
- [PowerShell을 사용하여 Azure Application Insights 자동화](app/powershell.md) - Resource Manager 템플릿이 업데이트되었습니다.
- [Azure Monitor Application Insights NuGet 패키지](app/nuget.md) - 패키지 버전이 업데이트되었습니다.
- [새 Azure Application Insights 리소스 만들기](app/create-new-resource.md) - 전역적으로 고유한 이름에 대한 참고 사항이 추가되었습니다.
- [라이브 메트릭 스트림을 사용하여 진단 - Azure Application Insights](app/live-stream.md) - ASP.NET Core SDK 버전 요구 사항이 업데이트되었습니다.
- [Application Insights의 이벤트 카운터](app/eventcounters.md) - customMetrics에 대한 범주와 테이블이 업데이트되었습니다.
- [Application Insights에서 Java 추적 로그 검색](app/java-trace-logs.md) - Java 에이전트 로깅 임계값에 대한 구성이 추가되었습니다.
- [Application Insights 및 Log Analytics에서 사용하는 IP 주소](app/ip-addresses.md) - 라이브 메트릭 스트림에 대한 IP 주소가 업데이트되었습니다.
- [Azure App Service 성능 모니터링](app/azure-web-apps.md) - ASP.NET Core 3.0에 대한 지원이 추가되었습니다. 
- [Azure Monitor를 사용하여 Python 애플리케이션 모니터링(미리 보기)](app/opencensus-python.md) - OpenCensus Python 스키마를 Azure .Monitor 스키마에 매핑하는 방법에 대한 설명이 추가되었습니다.
- [Azure Application Insights 릴리스 정보](app/release-notes.md) - 이전 릴리스에 대한 참고 사항이 추가되었습니다.
- [Azure Application Insights의 원격 분석 채널](app/telemetry-channels.md) - 연결이 끊겨 연장된 기간 동안 삭제된 데이터에 대한 기간이 업데이트되었습니다.
- [Azure Application Insights의 원격 분석 샘플링](app/sampling.md) - 사용자 지정 TelemetryInitializer에 대한 코드 조각이 수정되었습니다.
- [Java 웹 프로젝트의 Application Insights 문제 해결](app/java-troubleshoot.md) - JDK 9에서 종속성 컬렉션을 지원하지 않는 방법에 대한 명령문이 제거되었습니다.

### <a name="insights-and-solutions"></a>인사이트 및 솔루션

- [컨테이너용 Azure Monitor 질문과 대답](./faq.md) - 이미지 및 이름 필드에 질문이 추가되었습니다.
- [Azure Monitor의 Azure SQL 분석 솔루션](insights/azure-sql.md) - Managed Instance 지원을 기다리는 데이터베이스가 업데이트되었습니다.
- [컨테이너용 Azure Monitor 에이전트 데이터 수집 구성](insights/container-insights-agent-config.md) - enrich_container_logs에 대한 설정이 추가되었습니다.
- [컨테이너용 Azure Monitor를 사용하여 하이브리드 Kubernetes 클러스터 구성](insights/container-insights-hybrid-setup.md) - 문제 해결 섹션이 추가되었습니다.
- [Azure Monitor를 사용하여 Active Directory 복제 상태 모니터링](insights/ad-replication-status.md) - .NET Framework 필수 구성 요소가 업데이트되었습니다.
- [Azure의 네트워크 성능 모니터 솔루션](insights/network-performance-monitor.md) - 지원되는 지역이 추가되었습니다.
- [Azure Monitor를 사용하여 Active Directory 환경 최적화](insights/ad-assessment.md) - .NET Framework 필수 구성 요소가 업데이트되었습니다.
- [Azure Monitor를 사용하여 SQL Server 환경 최적화](insights/sql-assessment.md) - .NET Framework 필수 구성 요소가 업데이트되었습니다.
- [Azure Log Analytics를 사용하여 System Center Operations Manager 환경 최적화](insights/scom-assessment.md) - .NET Framework 필수 구성 요소가 업데이트되었습니다.
- [Azure Log Analytics의 IT Service Management Connector에 지원되는 연결](platform/itsmc-connections.md) - 필수 구성 요소 클라이언트 ID 및 클라이언트 암호에 New York이 추가되었습니다.

### <a name="logs"></a>로그

- [Azure Log Analytics 작업 영역 삭제 및 복구](platform/delete-workspace.md) - PowerShell 메서드가 추가되었습니다.
- [Azure Monitor 로그 배포 디자인](platform/design-logs-deployment.md) - 작업 영역에 대한 수집 비율이 증가되었습니다.

### <a name="metrics"></a>메트릭

- [진단 설정을 통해 내보낼 수 있는 Azure Monitor 플랫폼 메트릭](platform/metrics-supported-export-diagnostic-settings.md) - 새 문서

### <a name="platform-logs"></a>플랫폼 로그

진단 설정을 사용하여 활동 로그를 구성하는 새로운 기능을 기반으로 하는 플랫폼 로그의 콘텐츠 구조 변경의 일환으로 여러 문서가 업데이트되었습니다.

- [스토리지 계정에 Azure 리소스 로그 보관](./platform/resource-logs.md#send-to-azure-storage)
- [Azure 활동 로그 이벤트 스키마](platform/activity-log-schema.md)
- [Azure Monitor 서비스 제한](service-limits.md)
- [Log Analytics 작업 영역에서 Azure 활동 로그 수집 및 분석](./platform/activity-log.md)
- [진단 설정을 사용하여 Azure 활동 로그 수집(미리 보기) - Azure Monitor](./platform/activity-log.md)
- [Azure 테넌트 전체에서 Log Analytics 작업 영역으로 Azure 활동 로그 수집](platform/activity-log-collect-tenants.md)
- [Log Analytics 작업 영역에서 Azure 리소스 로그 수집](./platform/resource-logs.md#send-to-log-analytics-workspace)
- [Resource Manager 템플릿을 사용하여 Azure에서 진단 설정 만들기](platform/diagnostic-settings-template.md)
- [Azure에서 로그 및 메트릭을 수집하는 진단 설정 만들기](platform/diagnostic-settings.md)
- [Azure 활동 로그 내보내기](./platform/activity-log.md#legacy-collection-methods)
- [Azure 플랫폼 로그 개요](platform/platform-logs-overview.md)
- [이벤트 허브로 Azure 모니터링 데이터 스트림](platform/stream-monitoring-data-event-hubs.md)
- [이벤트 허브로 Azure 플랫폼 로그 스트림](./platform/resource-logs.md#send-to-azure-event-hubs)

### <a name="quickstarts-and-tutorials"></a>빠른 시작 및 자습서

- [Azure Monitor에서 메트릭 차트 만들기](learn/tutorial-metrics-explorer.md) - 새 문서
- [Azure 리소스에서 리소스 로그를 수집하고 Azure Monitor를 사용하여 분석](learn/tutorial-resource-logs.md) - 새 문서
- [Azure Monitor를 사용하여 Azure 리소스 모니터링](learn/quick-monitor-azure-resource.md) - 새 문서
   
## <a name="next-steps"></a>다음 단계

- Azure Monitor 설명서에 기여하려면 [Docs 기여자 가이드](/contribute/)를 참조하세요.