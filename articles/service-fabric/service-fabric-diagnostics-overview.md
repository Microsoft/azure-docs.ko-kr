---
title: Azure Service Fabric 모니터링 및 진단 개요 | Microsoft Docs
description: Azure Service Fabric 클러스터, 애플리케이션 및 서비스에 대한 모니터링 및 진단에 대해 알아봅니다.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/17/2019
ms.author: srrengar
ms.openlocfilehash: a6c32058c68adbfd11a4cede6332b42076bea015
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60952086"
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Azure Service Fabric 모니터링 및 진단

이 문서는 Azure Service Fabric의 모니터링 및 진단에 대한 개요를 제공합니다. 모니터링 및 진단은 모든 클라우드 환경에서 워크로드를 개발, 테스트 및 배포하는 데 중요합니다. 예를 들어, 애플리케이션이 사용되는 방식, Service Fabric 플랫폼이 수행한 작업, 성능 카운터를 통한 리소스 사용률 및 클러스터의 전반적인 상태를 추적할 수 있습니다. 이 정보를 사용하여 문제를 진단하고 수정할 수 있으며 나중에 문제가 발생하지 않도록 방지할 수 있습니다. 다음 일부 섹션에서는 프로덕션 작업에 대해 고려해야 하는 Service Fabric 모니터링의 각 영역에 대해 간단히 설명합니다. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="application-monitoring"></a>애플리케이션 모니터링
애플리케이션 모니터링은 애플리케이션의 기능 및 구성 요소가 사용되는 방식을 추적합니다. 애플리케이션을 모니터링하여 사용자에게 영향을 주는 문제가 발견되었는지 확인하려고 합니다. 애플리케이션 모니터링은 애플리케이션의 비즈니스 논리마다 고유하므로, 그 책임은 애플리케이션 및 해당 서비스를 개발하는 사용자에게 있습니다. 애플리케이션 모니터링은 다음 시나리오에서 유용할 수 있습니다.
* 내 애플리케이션에서 발생하는 트래픽 양은 얼마나 되나요? - 사용자 수요를 충족하거나 애플리케이션의 잠재적 병목 상태를 해결하기 위해 서비스를 확장해야 하나요?
* 서비스 간 호출이 성공적이며 추적되나요?
* 내 애플리케이션 사용자는 어떤 작업을 수행하나요? - 원격 분석을 수집하면 향후 기능 개발과 애플리케이션 오류에 대한 더 나은 진단이 가능해집니다.
* 내 애플리케이션에서 처리되지 않은 예외를 throw하나요? 
* 내 컨테이너 내에서 실행되는 서비스에서 어떤 상황이 발생하나요?

애플리케이션 모니터링은 애플리케이션 컨텍스트 내에서 진행되므로 개발자가 원하는 도구 및 프레임워크를 사용할 수 있다는 장점이 있습니다. [Application Insights를 사용하여 이벤트 분석](service-fabric-diagnostics-event-analysis-appinsights.md)에서 Azure Monitor - Application Insights를 사용한 애플리케이션 모니터링을 위한 Azure 솔루션에 대해 자세히 알아볼 수 있습니다.
[.NET 애플리케이션용으로 설정](service-fabric-tutorial-monitoring-aspnet.md)하는 방법을 제공하는 자습서도 있습니다. 이 자습서는 적절한 도구를 설치하는 방법, 애플리케이션의 사용자 지정 원격 분석 쓰기 예제 및 Azure Portal에서 응용 프로그램 진단 및 원격 분석 보기 방법을 설명합니다. 


## <a name="platform-cluster-monitoring"></a>플랫폼(클러스터) 모니터링
사용자는 코드를 직접 작성하기 때문에 해당 애플리케이션에서 제공되는 원격 분석을 제어하지만, Service Fabric 플랫폼에서 제공되는 진단의 경우는 어떤가요? Service Fabric의 목표 중 하나는 애플리케이션을 하드웨어 오류에 대해 복원력 있게 유지하는 것입니다. 이 목표는 플랫폼의 시스템 서비스가 인프라 문제를 감지하고 워크로드를 클러스터의 다른 노드로 빠르게 장애 조치(failover)할 수 있기 때문에 실현이 가능합니다. 그러나 이러한 특정 상황에서 시스템 서비스 자체에 문제가 있다면 어떻게 될까요? 또는 작업을 배포 또는 이동할 때 서비스 배치 규칙에 위배되면 어떻게 될까요? Service Fabric은 이러한 경우와 기타 경우에 대한 진단을 제공하여 사용자가 클러스터에서 진행되는 작업에 대해 알 수 있도록 합니다. 클러스터 모니터링에 대한 몇 가지 샘플 시나리오에는 다음이 포함됩니다.

Service Fabric은 구입 즉시 포괄적인 이벤트 집합을 제공합니다. 이러한 [Service Fabric 이벤트](service-fabric-diagnostics-events.md)는 EventStore 또는 작동 채널(플랫폼에서 노출되는 이벤트 채널)을 통해 액세스할 수 있습니다. 

* Service Fabric 이벤트 채널 - Windows에서 Service Fabric 이벤트는 작동 채널과 데이터 및 메시지 채널 사이에서 선택하는 데 사용되는 관련 `logLevelKeywordFilters` 집합을 사용하여 단일 ETW 공급자가 제공합니다. 이러한 방식으로 나가는 Service Fabric 이벤트를 격리하여 필요에 따라 필터링합니다. Linux에서는 Service Fabric 이벤트가 LTTng를 통해 수신되어 단일 Storage 테이블에 배치되며, 필요에 따라 어디서나 필터링할 수 있습니다. 이러한 채널에는 클러스터 상태 이해에 도움이 되는 구조화된 조정 이벤트가 포함되어 있습니다. 진단은 클러스터 생성 시 기본적으로 사용이 가능하며, 나중에 쿼리할 수 있도록 이러한 채널의 이벤트가 전송되는 Azure Storage 테이블이 만들어집니다. 

* EventStore - EventStore는 REST API를 통해 그리고 Service Fabric Explorer 에서 사용할 수 있는 Service Fabric 플랫폼 이벤트를 제공하는 플랫폼에서 제공하는 기능입니다. 이벤트 시간을 기반으로 각 엔터티(예: 노드, 서비스, 애플리케이션 및 쿼리)에 대해 클러스터에서 진행 중인 작업에 대한 스냅숏 보기를 볼 수 있습니다. [EventStore 개요](service-fabric-diagnostics-eventstore.md)에서 EventStore에 대한 자세한 내용을 볼 수도 있습니다.    

![EventStore](media/service-fabric-diagnostics-overview/eventstore.png)

제공되는 진단은 기본적으로 포괄적인 이벤트 세트 형식으로 되어 있습니다. 이러한 [Service Fabric 이벤트](service-fabric-diagnostics-events.md)는 노드, 애플리케이션, 서비스, 파티션 등의 다른 엔터티에 대해 플랫폼에서 수행되는 작업을 보여 줍니다. 위의 마지막 시나리오에서 노드가 작동 중단되면 플랫폼은 `NodeDown` 이벤트를 발생하며, 선택한 모니터링 도구를 통해 즉시 알림을 받을 수 있습니다. 다른 일반적인 예로 장애 조치(failover) 동안의 `ApplicationUpgradeRollbackStarted` 또는 `PartitionReconfigured`가 있습니다. **동일한 이벤트를 Windows 및 Linux 클러스터 둘 다에서 사용할 수 있습니다.**

이벤트가 Windows 및 Linux 둘 다의 표준 채널을 통해 전송되며, 이러한 채널을 지원하는 모니터링 도구에서 읽을 수 있습니다. Azure Monitor 방법은 Azure Monitor 로그입니다. 에 대 한 자세한 자유롭게 우리의 [Azure Monitor 통합 로그](service-fabric-diagnostics-event-analysis-oms.md) 클러스터 및 몇 가지 샘플 쿼리는 경고를 만들 수 있습니다에 대 한 사용자 지정 작업 대시보드를 포함 하는 합니다. [플랫폼 수준 이벤트 및 로그 생성](service-fabric-diagnostics-event-generation-infra.md)에서 더 많은 클러스터 모니터링 개념을 확인할 수 있습니다.

### <a name="health-monitoring"></a>상태 모니터링
Service Fabric 플랫폼에는 클러스터의 엔터티 상태에 대한 확장 가능한 상태 보고를 제공하는 상태 모델이 포함되어 있습니다. 각 노드, 애플리케이션, 서비스, 파티션, 복제본 또는 인스턴스에는 지속적으로 업데이트 가능한 상태가 있습니다. 시스템 상태는 “정상”, “경고” 또는 “오류”일 수 있습니다. Service Fabric 이벤트를 다양한 엔터티에 대해 클러스터가 수행하는 동사로, 상태를 각 엔터티에 대한 형용사로 간주할 수 있습니다. 특정 엔터티의 상태가 전환될 때마다 이벤트도 발생합니다. 이러한 방식으로 다른 이벤트와 마찬가지로 원하는 모니터링 도구에서 상태 이벤트에 대한 쿼리 및 경고를 설정할 수 있습니다. 

또한 사용자가 엔터티의 상태를 재정의하도록 할 수도 있습니다. 애플리케이션이 업그레이드를 거치게 되며, 유효성 검사 테스트가 실패하면 Health API를 통해 Service Fabric 상태에 애플리케이션이 더 이상 정상 상태가 아니라고 쓸 수 있습니다. 그러면 Service Fabric은 업그레이드를 자동으로 롤백합니다. 상태 모델에 대한 자세한 내용은 [Service Fabric 상태 모니터링 소개](service-fabric-health-introduction.md)를 참조하세요.

![SFX 상태 대시보드](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)


### <a name="watchdogs"></a>Watchdog
일반적으로 Watchdog는 전체 서비스의 상태와 부하를 감시하고, 엔드포인트에 Ping을 수행하고, 클러스터에 있는 모든 항목의 상태를 보고할 수 있는 별도의 서비스입니다. 이렇게 하면 단일 서비스 보기를 기반으로 하여 감지되지 않는 오류를 방지할 수 있습니다. Watchdog은 사용자 상호 작용 없이 교정 작업을 수행하는 코드(예: 특정 시간 간격으로 저장소의 로그 파일 정리)를 호스트하는 데 적합합니다. [여기](https://github.com/Azure-Samples/service-fabric-watchdog-service)에서 샘플 watchdog 서비스 구현을 찾을 수 있습니다.

## <a name="infrastructure-performance-monitoring"></a>인프라(성능) 모니터링
지금까지 애플리케이션 및 플랫폼의 진단에 대해 알아보았습니다. 하드웨어가 예상대로 작동하는지를 어떻게 알 수 있을까요? 기본 인프라 모니터링은 클러스터 상태 및 리소스 사용률을 이해하는 데 중요한 부분입니다. 시스템 성능 측정은 작업에 따라 주관적일 수 있는 다양한 요인에 따라 달라집니다. 이러한 요소는 일반적으로 성능 카운터를 통해 측정됩니다. 이러한 성능 카운터는 운영 체제, .NET Framework 또는 Service Fabric 플랫폼 자체를 포함하는 다양한 원본에서 가져올 수 있습니다. 성능 카운터를 유용하게 사용할 수 있는 몇 가지 시나리오는 다음과 같습니다.

* 하드웨어를 효율적으로 활용하고 있나요? 하드웨어를 90% CPU 또는 10% CPU 중 어디에서 사용하고 싶나요? 이 시나리오는 클러스터의 크기를 조정하거나 애플리케이션 프로세스를 최적화하는 경우에 도움이 됩니다.
* 인프라 문제를 사전에 예측할 수 있나요? - 문제에 앞서 성능이 갑자기 변경(저하)되는 경우가 많으므로 네트워크 I/O, CPU 사용률 등의 성능 카운터를 사용하여 문제를 사전에 예측하고 진단할 수 있습니다.

인프라 수준에서 수집해야 하는 성능 카운터 목록은 [성능 메트릭](service-fabric-diagnostics-event-generation-perf.md)에서 확인할 수 있습니다. 

또한 Service Fabric Reliable Services 및 Actors 프로그래밍 모델에 대 한 성능 카운터 집합을 제공 합니다. 두 모델 중 하나를 사용하는 경우, 이러한 성능 카운터는 행위자가 올바르게 스핀 업/스핀 다운하고 있는지 또는 Reliable Services 요청이 빠르게 처리되고 있는지 확인하는 데 유용한 정보를 제공할 수 있습니다. 자세한 내용은 [Reliable Service Remoting 모니터링](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) 및 [Reliable Actors 성능 모니터링](service-fabric-reliable-actors-diagnostics.md#performance-counters)을 참조하세요. 

이러한 수집 하도록 Azure Monitor 방법은 플랫폼 수준 모니터링와 마찬가지로 Azure Monitor 로그입니다. 사용 해야 합니다 [Log Analytics 에이전트](service-fabric-diagnostics-oms-agent.md) 적절 한 성능 카운터를 수집 하 여 Azure Monitor 로그에서이 확인 합니다.

## <a name="recommended-setup"></a>권장 설정
각 모니터링 영역 및 예제 시나리오를 완료했으므로 다음에 제공되는 Azure 모니터링 도구 및 위의 모든 영역을 모니터링하는 데 필요한 설정 요약을 확인하세요. 

* [Application Insights](service-fabric-tutorial-monitoring-aspnet.md)를 사용하는 애플리케이션 모니터링
* 사용 하 여 클러스터 모니터링 [진단 에이전트](service-fabric-diagnostics-event-aggregation-wad.md) 고 [Azure Monitor 로그](service-fabric-diagnostics-oms-setup.md)
* 사용한 인프라 모니터링 [Azure Monitor 로그](service-fabric-diagnostics-oms-agent.md)

또한 [여기](service-fabric-diagnostics-oms-setup.md#deploy-azure-monitor-logs-with-azure-resource-manager)에 있는 샘플 ARM 템플릿을 사용 및 수정하여 필요한 모든 리소스 및 에이전트의 배포를 자동화할 수도 있습니다. 

## <a name="other-logging-solutions"></a>다른 로깅 솔루션

두 솔루션 좋습니다, 있지만 [Azure Monitor 로그](service-fabric-diagnostics-event-analysis-oms.md) 하 고 [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) 많은 이벤트가 ETW 공급자를 통해 작성 되 고는 Service Fabric과 통합에서 빌드한 다른 로깅 솔루션으로 확장할 수 있습니다. [Elastic Stack](https://www.elastic.co/products)(특히 오프라인 환경에서 클러스터를 실행하려는 경우), [Dynatrace](https://www.dynatrace.com/) 또는 기타 원하는 플랫폼도 살펴봐야 합니다. [여기](service-fabric-diagnostics-partners.md)에서 사용할 수 있는 통합된 파트너 목록이 있습니다.

플랫폼을 선택할 때는 사용자 인터페이스, 쿼리 기능, 사용자 지정 시각화 및 사용 가능한 대시보드, 모니터링 환경 개선을 위해 제공하는 추가 도구의 편의성을 고려해야 합니다. 

## <a name="next-steps"></a>다음 단계

* 애플리케이션 계측을 시작하려면 [애플리케이션 수준 이벤트 및 로그 생성](service-fabric-diagnostics-event-generation-app.md)을 참조하세요.
* [Service Fabric에서 ASP.NET Core 애플리케이션 모니터링 및 진단](service-fabric-tutorial-monitoring-aspnet.md)에서 애플리케이션용 Application Insights를 설정하는 단계를 진행합니다.
* Service Fabric이 제공하는 플랫폼 및 이벤트 모니터링에 대한 자세한 내용은 [플랫폼 수준 이벤트 및 로그 생성](service-fabric-diagnostics-event-generation-infra.md)을 참조하세요.
* Service Fabric을 사용 하 여 Azure Monitor 로그 통합을 구성 [클러스터에 대 한 Azure Monitor 로그 설정](service-fabric-diagnostics-oms-setup.md)
* 컨테이너를 모니터링 하는 것에 대 한 Azure Monitor 로그를 설정 하는 방법 알아보기- [컨테이너 모니터링 및 진단에 대 한 Windows Azure Service Fabric에서](service-fabric-tutorial-monitoring-wincontainers.md)합니다.
* [일반적인 시나리오 진단](service-fabric-diagnostics-common-scenarios.md)에서 Service Fabric을 사용하여 예제 진단 문제 및 솔루션을 참조하세요.
* [Service Fabric 진단 파트너](service-fabric-diagnostics-partners.md)에서 Service Fabric과 통합되는 기타 진단 제품을 확인하세요.
* Azure 리소스에 대한 일반적인 모니터링 권장 사항을 알아봅니다. [모범 사례 - 모니터링 및 진단](https://docs.microsoft.com/azure/architecture/best-practices/monitoring) 