---
title: Azure Service Fabric 플랫폼 수준 모니터링
description: Azure Service Fabric 클러스터를 모니터링 및 진단하는 데 사용되는 플랫폼 수준 이벤트 및 로그를 알아봅니다.
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: f9104c390ab4115c626beb4759c6b6952d691ca9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626523"
---
# <a name="monitoring-the-cluster"></a>클러스터 모니터링

클러스터 수준에서 모니터링하여 하드웨어와 클러스터가 예상대로 작동하는지 확인해야 합니다. Service Fabric은 하드웨어 오류 시에도 애플리케이션을 계속 실행할 수 있지만, 애플리케이션이나 기본 인프라에서 오류가 발생하는지를 여전히 진단해야 합니다. 하드웨어 추가 또는 제거에 대한 결정 시 도움이 되도록 용량 계획을 개선하려면 클러스터를 모니터링해야 합니다.

Service Fabric은 EventStore 및 다양한 기본 제공 로그 채널을 통해 여러 구조적 플랫폼 이벤트를 [Service Fabric 이벤트](service-fabric-diagnostics-events.md)로 노출합니다. 

Windows에서 Service Fabric 이벤트는 작동 채널과 데이터 및 메시징 채널 사이에서 선택하는 데 사용되는 관련 `logLevelKeywordFilters` 세트가 있는 단일 ETW 공급자에서 사용할 수 있습니다. 이는 필요에 따라 필터링할 나가는 Service Fabric 이벤트를 격리하는 방식입니다.

* **작업** 제공될 노드에 대한 이벤트, 배포 중인 새 애플리케이션 또는 업그레이드 롤백 등을 포함하여 Service Fabric 및 클러스터에서 수행하는 상위 수준 작업입니다. [여기](service-fabric-diagnostics-event-generation-operational.md)에서 전체 이벤트 목록을 참조하세요.  

* **작동 - 상세**  
상태 보고서 및 부하 분산 결정

작업 채널은 ETW/Windows 이벤트 로그, [EventStore](service-fabric-diagnostics-eventstore.md)(Windows 클러스터의 경우 Windows에 있는 6.2 이상 버전에서 사용 가능)를 포함한 다양한 방법을 통해 액세스할 수 있습니다. EventStore는 엔터티 단위로(클러스터, 노드, 애플리케이션, 서비스, 파티션, 복제본 및 컨테이너를 포함한 엔터티) 클러스터의 이벤트에 대한 액세스를 제공하고 REST API 및 Service Fabric 클라이언트 라이브러리를 통해 이벤트를 노출합니다. EventStore를 사용하여 개발/테스트 클러스터를 모니터링하고, 특정 시점에 프로덕션 클러스터의 상태를 이해할 수 있습니다.

* **데이터 및 메시징**  
메시징(현재는 ReverseProxy만) 및 데이터 경로(신뢰할 수 있는 서비스 모델)에서 생성된 중요한 로그 및 이벤트

* **데이터 및 메시징 채널 - 상세**  
자세한 정보 채널에는 클러스터의 메시징 데이터에서 중요하지 않은 모든 로그가 포함됩니다(이 채널은 상당한 양의 이벤트 포함).

이들 외에 2개의 구조화된 EventSource 채널이 제공되고 지원을 위해 수집한 로그가 있습니다.

* [Reliable Services 이벤트](service-fabric-reliable-services-diagnostics.md)  
프로그래밍 모델 특정 이벤트

* [Reliable Actors 이벤트](service-fabric-reliable-actors-diagnostics.md)  
프로그래밍 모델 특정 이벤트 및 성능 카운터

* 지원 로그  
지원을 제공할 때만 사용되는 Service Fabric에서 생성된 시스템 로그

이러한 다양한 채널에서 권장되는 대부분의 플랫폼 수준 로깅을 처리합니다. 플랫폼 수준 로깅을 개선하려면 상태 모델을 더 잘 파악하는 데 투자하고 사용자 지정 상태 보고서를 추가해 보고, 사용자 지정 **성능 카운터** 를 추가하여 서비스와 애플리케이션이 클러스터에 미치는 영향을 실시간으로 파악하는 기능을 빌드하세요.

이러한 로그를 활용하려면 Azure Portal에서 클러스터를 만드는 동안 "진단"을 사용하도록 설정하는 것이 좋습니다. 진단을 켜면 클러스터가 배포될 때 Microsoft Azure Diagnostics에서는 작동, Reliable Services 및 Reliable Actors 채널을 인식하고 [Azure Diagnostics를 사용하여 이벤트 집계](service-fabric-diagnostics-event-aggregation-wad.md)에 추가로 설명된 대로 데이터를 저장할 수 있습니다.

## <a name="azure-service-fabric-health-and-load-reporting"></a>Azure Service Fabric 상태 및 로드 보고

Service Fabric에는 다음과 같은 문서에서 자세히 설명하는 자체의 상태 모델이 있습니다.

- [Service Fabric 상태 모니터링 소개](service-fabric-health-introduction.md)
- [서비스 상태 보고 및 확인](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [사용자 지정 서비스 패브릭 상태 보고서 추가](service-fabric-report-health.md)
- [서비스 패브릭 상태 보고서 보기](service-fabric-view-entities-aggregated-health.md)

상태 모니터링은 특히 애플리케이션 업그레이드 중에 서비스 운영의 여러 측면에서 매우 중요합니다. 서비스의 각 업그레이드 도메인이 업그레이드되면 배포가 다음 업그레이드 도메인으로 이동하기 전에 업그레이드 도메인이 상태 확인을 통과해야 합니다. 정상 상태에 도달할 수 없는 경우 배포가 롤백되어 애플리케이션이 알려진 정상 상태로 유지됩니다. 서비스를 롤백하기 전에 일부 고객이 영향을 받을 수 있지만, 대부분의 고객에게는 문제가 발생하지 않습니다. 또한 운영자가 조치를 취할 때까지 기다릴 필요 없이 비교적 빨리 해결됩니다. 코드에 통합된 상태 검사가 많을수록 배포 문제에 대한 서비스 복원력이 더욱 높아집니다.

서비스 상태의 또 다른 측면은 서비스의 메트릭을 보고하는 것입니다. 메트릭은 리소스 사용량을 조정하는 데 사용되므로 Service Fabric에서 중요합니다. 또한 메트릭은 시스템 상태를 나타내는 표시기일 수도 있습니다. 예를 들어 많은 서비스가 있는 애플리케이션을 가질 수 있으며, 각 인스턴스에서 RPS(초당 요청 수)를 보고합니다. 한 서비스에서 다른 서비스보다 많은 리소스를 사용하는 경우 Service Fabric에서 클러스터 주변의 서비스 인스턴스를 이동하여 리소스 사용률을 유지하려고 합니다. 리소스 사용률이 작동하는 방식에 대한 자세한 설명은 [메트릭을 사용하여 Service Fabric에서 리소스 부하 및 소비 관리](service-fabric-cluster-resource-manager-metrics.md)를 참조하세요.

또한 메트릭을 통해 서비스 수행 방식에 대한 통찰력을 얻을 수도 있습니다. 시간이 지남에 따라 메트릭을 사용하여 서비스가 예상된 매개 변수 내에서 작동하는지 확인할 수 있습니다. 예를 들어 추세에서 월요일 오전 9시의 평균 RPS가 1,000인 경우 RPS가 500 미만이거나 1,500 이상일 때 경고하는 상태 보고서를 설정할 수 있습니다. 모든 것이 완벽할 수도 있지만 고객이 매우 만족스럽게 경험하고 있는지도 확인할 만한 가치가 있습니다. 서비스에서 상태 검사를 위해 보고할 수 있지만 클러스터의 리소스 분산에는 영향을 미치지 않는 메트릭 집합을 정의할 수 있습니다. 이렇게 하려면 메트릭 가중치를 0으로 설정합니다. 가중치가 0인 모든 메트릭을 시작하고 메트릭의 가중치가 클러스터의 리소스 분산에 미치는 영향을 확실히 이해할 때까지 가중치를 높이지 않는 것이 좋습니다.

> [!TIP]
> 너무 높은 가중치가 적용된 메트릭은 사용하지 마세요. 분산을 조정하기 위해 서비스 인스턴스를 이동하는 이유를 이해하기 어려울 수 있습니다. 몇 가지 메트릭에는 많은 시간이 걸릴 수 있습니다.

애플리케이션의 상태 및 성능을 나타낼 수 있는 정보는 메트릭 및 상태 보고서의 후보가 됩니다. **CPU 성능 카운터는 노드가 활용되는 상태를 알려주지만 단일 노드에서 여러 서비스가 실행될 수 있기 때문에 특정 서비스의 상태가 정상인지 여부는 알려주지 않습니다.** 그러나 RPS, 처리된 항목 및 요청 대기 시간과 같은 메트릭은 모두 특정 서비스의 상태를 나타낼 수 있습니다.

## <a name="service-fabric-support-logs"></a>Service Fabric 지원 로그

Azure Service Fabric 클러스터에 대한 도움을 받기 위해 Microsoft 지원에 문의해야 하는 경우 지원 로그가 거의 항상 필요합니다. 클러스터가 Azure에서 호스팅되는 경우 클러스터를 만드는 과정의 일부로 이러한 로그를 자동으로 구성하여 수집합니다. 로그는 클러스터의 리소스 그룹에 있는 전용 스토리지 계정에 저장됩니다. 스토리지 계정에 고정된 이름은 없지만, 계정에는 *fabric* 으로 시작하는 이름의 Blob 컨테이너와 테이블이 표시됩니다. 독립 실행형 클러스터의 로그 수집 설정에 대한 자세한 내용은 [독립 실행형 Azure Service Fabric 클러스터 만들기 및 관리](service-fabric-cluster-creation-for-windows-server.md) 및 [독립 실행형 Windows 클러스터에 대한 구성 설정](service-fabric-cluster-manifest.md)을 참조하세요. 독립 실행형 Service Fabric 인스턴스의 경우 로그는 로컬 파일 공유로 보내야 합니다. 지원을 받으려면 이러한 로그가 **필요하지만** Microsoft 고객 지원 팀 외부의 사람이 사용할 수 있는 것은 아닙니다.

## <a name="measuring-performance"></a>성능 측정

클러스터 성능 측정을 통해 어떻게 부하를 처리하고 클러스터 크기 조정과 관련된 의사 결정을 주도할 수 있는지 이해할 수 있습니다([Azure에서](service-fabric-cluster-scale-in-out.md) 또는 [온-프레미스로](service-fabric-cluster-windows-server-add-remove-nodes.md) 클러스터 크기를 조정하는 방법에 대한 자세한 내용 참조). 성능 데이터는 나중에 로그를 분석할 때 사용자나 애플리케이션 및 서비스가 수행한 작업에 비교될 경우에도 유용합니다. 

Service Fabric을 사용할 경우 수집할 경우 수집할 성능 카운터에 대해서는 [Service Fabric의 성능 카운터](service-fabric-diagnostics-event-generation-perf.md)를 참조하세요.

클러스터에 대한 성능 데이터 수집을 설정하는 두 가지 일반적인 방법은 다음과 같습니다.

* **에이전트 사용**  
이는 머신에서 성능을 수집할 때 선호되는 방법이며, 일반적으로 에이전트에는 수집할 수 있는 가능한 성능 메트릭 목록이 있어서 수집하거나 변경할 메트릭을 선택하는 프로세스가 상대적으로 쉽기 때문입니다. 클러스터 VM 및 배포된 컨테이너의 성능 데이터를 선택할 수 있는 모니터링 에이전트 중 하나인 Log Analytics 에이전트에 대해 자세히 알아보려면 Service Fabric의 [Azure Monitor 로그 통합](service-fabric-diagnostics-event-analysis-oms.md) 및 [Log Analytics 에이전트 설정](../azure-monitor/agents/agent-windows.md)에서 Azure Monitor 로그를 제공하는 Azure Monitor와 관련된 문서를 참조하세요.

* **Azure Table Storage대한 성능 카운터**  
성능 메트릭은 이벤트와 동일한 테이블 스토리지에 보낼 수도 있습니다. 이렇게 하려면 Azure Diagnostics 구성을 변경하여 클러스터의 VM에서 적절한 성능 카운터를 선택하고, 컨테이너를 배포할 경우 Docker 통계를 선택해야 합니다. 성능 카운터 수집을 설정하려면 Service Fabric에서 [WAD의 성능 카운터](service-fabric-diagnostics-event-aggregation-wad.md)를 구성하는 방법을 참조하세요.

## <a name="next-steps"></a>다음 단계

* Service Fabric의 [Azure Monitor 로그 통합](service-fabric-diagnostics-event-analysis-oms.md)에 대해 알아본 후에 클러스터 진단을 수집하고 사용자 지정 쿼리와 경고를 만듭니다.
* Service Fabric의 기본 제공 진단 환경인 [EventStore](service-fabric-diagnostics-eventstore.md)에 대해 알아봅니다.
* Service Fabric에서 몇 가지 [일반적인 진단 시나리오](service-fabric-diagnostics-common-scenarios.md)를 살펴봅니다.
