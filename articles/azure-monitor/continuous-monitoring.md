---
title: Azure Monitor를 사용하여 연속 모니터링 | Microsoft Docs
description: Azure Monitor를 사용하여 전체 워크플로에서 연속 모니터링을 사용하도록 설정하는 특정 단계를 설명합니다.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/12/2018
ms.openlocfilehash: 7dc8001d74ea026625d7496fb84053b4e9109dce
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75406147"
---
# <a name="continuous-monitoring-with-azure-monitor"></a>Azure Monitor를 사용하여 연속 모니터링

연속 모니터링은 DevOps 및 IT 운영 수명 주기의 각 단계에서 모니터링을 통합하는 데 필요한 프로세스 및 기술을 나타냅니다. 개발에서 프로덕션으로 이동할 때 애플리케이션 및 인프라의 상태, 성능 및 안정성을 지속적으로 확인하는 데 도움이 됩니다. 연속 모니터링은 사용자에게 지속적인 가치를 제공하기 위해 소프트웨어를 더 빠르고 더 안정적으로 개발 및 배달하도록 도와주는 CI/CD(연속 통합 및 지속적인 배포) 개념을 기반으로 합니다.

[Azure Monitor](overview.md)는 클라우드 및 온-프레미스에서 애플리케이션과 인프라에 전체 스택 식별 가능성을 제공하는 Azure의 통합 모니터링 솔루션입니다. 개발 및 테스트 중에 [Visual Studio 및 Visual Studio Code](https://visualstudio.microsoft.com/)에서 원활하게 작동하고 배포 및 운영 중에 릴리스 관리 및 작업 항목 관리를 위해 [Azure DevOps](/azure/devops/user-guide/index)와 통합됩니다. 기존 IT 프로세스 내에서 문제 및 인시던트를 추적하는 데 도움이 되도록 선택한 ITSM 및 SIEM 도구 간에도 통합됩니다.

이 문서에서는 Azure Monitor를 사용하여 전체 워크플로에서 연속 모니터링을 사용하도록 설정하는 특정 단계를 설명합니다. 다양한 기능 구현에 대한 세부 정보를 제공하는 다른 문서의 링크가 포함됩니다.


## <a name="enable-monitoring-for-all-your-applications"></a>모든 애플리케이션에 모니터링 사용
전체 환경에서 식별 가능성을 얻으려면 모든 웹 애플리케이션 및 서비스에서 모니터링을 사용하도록 설정해야 합니다. 이렇게 하면 모든 구성 요소에서 엔드투엔드 트랜잭션 및 연결을 쉽게 시각화할 수 있습니다.

- [Azure DevOps Projects](../devops-project/overview.md)는 기존 코드와 Git 리포지토리가 포함된 간소화된 환경을 제공하거나 샘플 애플리케이션 중 하나를 선택하여 Azure에 CI(연속 통합) 및 CD(지속적인 업데이트) 파이프라인을 만듭니다.
- [DevOps 릴리스 파이프라인의 연속 모니터링](../azure-monitor/app/continuous-monitoring.md)을 사용하면 모니터링 데이터를 기반으로 배포를 제어 또는 롤백할 수 있습니다.
- [상태 모니터](../azure-monitor/app/monitor-performance-live-website-now.md)를 사용하면 코드를 수정하거나 다시 배포하지 않고도 Azure Application Insights를 사용하여 Windows에서 라이브 .NET 앱을 계측할 수 있습니다.
- 애플리케이션의 코드에 액세스할 수 있는 경우 [.NET](../azure-monitor/learn/quick-monitor-portal.md), [Java](../azure-monitor/app/java-get-started.md), [Node.js](../azure-monitor/learn/nodejs-quick-start.md) 또는 [기타 프로그래밍 언어](../azure-monitor/app/platforms.md)용 Azure Monitor Application Insights SDK를 설치하여 [Application Insights](../azure-monitor/app/app-insights-overview.md)를 통해 전체 모니터링을 사용하도록 설정합니다. 이렇게 하면 애플리케이션 및 비즈니스에 관련된 사용자 지정 이벤트, 메트릭 또는 페이지 보기를 지정할 수 있습니다.



## <a name="enable-monitoring-for-your-entire-infrastructure"></a>전체 인프라에 모니터링 사용
애플리케이션은 기본 인프라 만큼만 안정적입니다. 전체 인프라에서 모니터링을 사용하도록 설정하면 전체 식별 가능성을 확보하고 문제 발생 시 잠재적인 근본 원인을 더 쉽게 발견할 수 있습니다. Azure Monitor를 사용하여 VM, 컨테이너, 스토리지 및 네트워크 등의 리소스를 비롯하여 전체 하이브리드 인프라의 상태 및 성능을 추적할 수 있습니다.

- 구성 없이 대부분의 Azure 리소스에서 [플랫폼 메트릭, 활동 로그 및 진단 로그](platform/data-sources.md)를 자동으로 가져옵니다.
- [VM용 Azure Monitor](insights/vminsights-overview.md)를 사용하여 더 세부적인 VM 모니터링을 사용하도록 설정합니다.
-  [컨테이너용 Azure Monitor](insights/container-insights-overview.md)를 사용하여 더 세부적인 AKS 클러스터 모니터링을 사용하도록 설정합니다.
- 환경에 있는 다양한 애플리케이션 및 서비스를 위한 [모니터링 솔루션](insights/solutions-inventory.md)을 추가합니다.


[코드로서의 인프라](/azure/devops/learn/what-is-infrastructure-as-code)는 DevOps 팀이 원본 코드에 사용하는 것과 동일한 버전 관리를 사용하여 설명 모델에서 인프라를 관리하는 기능입니다. 이 기능을 통해 환경에 안정성과 확장성이 추가되고 애플리케이션을 관리하는 데 사용되는 유사한 프로세스를 활용할 수 있습니다.

-  [Resource Manager 템플릿](platform/template-workspace-configuration.md)을 사용하여 모니터링을 사용하도록 설정하고 대규모 리소스 세트에 대한 경고를 구성합니다.
- [Azure Policy](../governance/policy/overview.md)를 사용하여 리소스에 다양한 규칙을 적용합니다. 이렇게 하면 해당 리소스는 회사 표준 및 서비스 수준 계약을 계속 준수합니다. 


##  <a name="combine-resources-in-azure-resource-groups"></a>Azure 리소스 그룹의 리소스 결합
현재 Azure의 일반적인 애플리케이션에는 VM 및 App Services나 Cloud Services, AKS 클러스터 또는 Service Fabric에 호스트된 마이크로서비스와 같은 다양한 리소스가 포함됩니다. 이러한 애플리케이션은 Event Hubs, Storage, SQL 및 Service Bus와 같은 종속성을 자주 사용합니다.

- Azure 리소스 그룹의 리소스를 결합 하 여 다양 한 응용 프로그램을 구성 하는 모든 리소스에 대한 완전 한 가시성을 얻을 수 있습니다. [리소스 그룹용 Azure Monitor](../azure-monitor/insights/resource-group-insights.md)는 전체 스택 애플리케이션의 상태 및 성능을 추적하고 조사 또는 디버깅을 위해 개별 구성 요소로 드릴다운할 수 있는 간단한 방법을 제공합니다.

## <a name="ensure-quality-through-continuous-deployment"></a>지속적인 배포를 통해 품질 보장
연속 통합/지속적인 배포를 사용하면 자동화된 테스트의 결과에 따라 코드 변경 내용을 애플리케이션에 자동으로 통합하고 배포할 수 있습니다. 배포 프로세스를 간소화하고 프로덕션으로 이동하기 전에 변경 내용의 품질을 보장합니다.


- [Azure Pipelines](/azure/devops/pipelines)를 사용하여 지속적인 배포를 구현하고 CI/CD 테스트를 기반으로 코드 커밋에서 프로덕션으로 전체 프로세스를 자동화합니다.
- [품질 게이트](/azure/devops/pipelines/release/approvals/gates)를 사용하여 모니터링을 사전 배포 또는 사후 배포에 통합합니다. 이렇게 하면 애플리케이션이 개발 환경에서 프로덕션 환경으로 이동하고 인프라 환경 또는 규모의 차이가 KPI에 부정적인 영향을 주지 않으므로 핵심 상태/성과 지표(KPI)가 충족됩니다.
- 개발, 테스트, 카나리아 및 Prod와 같은 서로 다른 배포 환경 간에 [별도의 모니터링 인스턴스를 유지](../azure-monitor/app/separate-resources.md) 합니다. 이렇게 하면 수집 된 데이터가 연결 된 응용 프로그램 및 인프라에서 관련 됩니다. 환경 간에 데이터를 상호 연결해야 하면 [메트릭 탐색기에서 다중 리소스 차트](../azure-monitor/platform/metrics-charts.md)를 사용하거나 [Azure Monitor에서 리소스 간 쿼리](log-query/cross-workspace-query.md)를 만들 수 있습니다.


## <a name="create-actionable-alerts-with-actions"></a>작업을 사용하여 실행 가능한 경고 만들기
모니터링의 중요한 측면은 관리자에게 현재 및 예상 문제를 적극적으로 알리는 것입니다. 

- 로그 및 메트릭을 기반으로 [Azure Monitor에서 경고](../azure-monitor/platform/alerts-overview.md)를 만들어 예측 가능한 오류 상태를 식별합니다. 실제 위험 조건을 나타내고 가양성을 줄이고자 하는 모든 경고를 실행 가능하도록 설정해야 합니다. [동적 임계값](platform/alerts-dynamic-thresholds.md)을 사용하여 고유한 정적 임계값을 정의하는 대신 메트릭 데이터에서 기준선을 자동으로 계산합니다. 
- 관리자에게 알리는 가장 효과적인 수단을 사용하도록 경고 작업을 정의합니다. 사용 가능한 [알림 작업](platform/action-groups.md#create-an-action-group-by-using-the-azure-portal)은 SMS, 메일, 푸시 알림 또는 음성 통화입니다.
- 고급 작업을 사용하여 [웹후크](platform/activity-log-alerts-webhook.md)를 통해 [ITSM 도구에 연결](platform/itsmc-overview.md)하거나 기타 경고 관리 시스템에 연결합니다.
- 웹후크를 사용하여 경고에서 시작될 수 있는 [Azure Automation Runbook](../automation/automation-webhooks.md) 또는 [Logic Apps](/connectors/custom-connectors/create-webhook-trigger)와 함께 경고에서 식별된 상황도 수정합니다. 
- [자동 크기 조정](../azure-monitor/learn/tutorial-autoscale-performance-schedule.md)을 사용하여 수집된 메트릭을 기반으로 컴퓨팅 리소스를 동적으로 늘리고 줄입니다.

## <a name="prepare-dashboards-and-workbooks"></a>대시보드 및 통합 문서 준비
개발 및 운영이 동일한 원격 분석 및 도구에 액세스할 수 있도록 하면 전체 환경에서 패턴을 보고 MTTD(평균 검색 시간) 및 MTTR(평균 복원 시간)을 최소화할 수 있습니다.

- 조직의 다양한 역할에 대한 공통 메트릭 및 로그를 기반으로 [사용자 지정 대시보드](../azure-monitor/learn/tutorial-app-dashboards.md)를 준비합니다. 대시보드는 모든 Azure 리소스의 데이터를 결합할 수 있습니다.
- 개발 및 운영 간에 정보 공유를 보장하기 위해 [통합 문서](../azure-monitor/app/usage-workbooks.md)를 준비합니다. 이러한 통합 문서는 메트릭 차트 및 로그 쿼리를 사용하여 동적 보고서로 준비하거나 기본적인 문제를 처리하는 고객 지원 또는 운영에 도움이 되도록 개발자가 마련한 문제 해결 가이드로도 준비할 수 있습니다.

## <a name="continuously-optimize"></a>지속적으로 최적화
 모니터링은 KPI 및 사용자 동작 메트릭을 지속적으로 추적하고 반복 계획을 통해 최대한 최적화하도록 권장하는 널리 사용되는 빌드-측정-학습 철학의 기본 측면 중 하나입니다. Azure Monitor를 사용하여 비즈니스에 관련된 메트릭 및 로그를 수집하고 필요에 따라 다음 배포에 새 데이터 요소를 추가할 수 있습니다.

- Application Insights의 도구를 사용하여 [최종 사용자 동작 및 참여를 추적](../azure-monitor/learn/tutorial-users.md)합니다.
- [영향 분석](../azure-monitor/app/usage-impact.md)을 사용하여 중요 KPI에 집중하기 위해 초점을 맞출 영역의 우선 순위를 지정할 수 있습니다.


## <a name="next-steps"></a>다음 단계

- [Azure Monitor](overview.md)의 차이 구성 요소에 대해 알아봅니다.
- 릴리스 파이프라인에 [연속 모니터링을 추가](../azure-monitor/app/continuous-monitoring.md)합니다.
