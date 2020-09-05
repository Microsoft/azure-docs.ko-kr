---
title: AKS 비즈니스 연속성 및 재해 복구에 대 한 모범 사례
description: Azure Kubernetes 서비스 (AKS)에서 고가용성 및 재해 복구 준비를 제공 하 여 응용 프로그램에 대 한 최대 가동 시간을 제공 하는 클러스터 운영자의 모범 사례를 알아봅니다.
services: container-service
author: lastcoolnameleft
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: thfalgou
ms.custom: fasttrack-edit
ms.openlocfilehash: 110a25fca0b0e764650665635dbe545de7a350cd
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88653999"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Services)의 비즈니스 연속성 및 재해 복구 모범 사례

AKS(Azure Kubernetes Services)에서 클러스터를 관리할 때 애플리케이션 가동 시간이 중요합니다. 기본적으로 AKS는 [VMSS (가상 머신 확장 집합)](../virtual-machine-scale-sets/overview.md)에서 여러 노드를 사용 하 여 고가용성을 제공 합니다. 그러나 이러한 여러 노드는 지역 장애 로부터 시스템을 보호 하지 않습니다. 가동 시간을 최대화 하려면 비즈니스 연속성을 유지 하 고 재해 복구를 준비 하기 위해 미리 계획을 세워야 합니다.

이 문서에서는 AKS에서 비즈니스 연속성 및 재해 복구를 계획 하는 방법에 중점을 둔 문서입니다. 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * 여러 지역에서 AKS 클러스터를 계획 합니다.
> * Azure Traffic Manager을 사용 하 여 여러 클러스터에서 트래픽을 라우팅합니다.
> * 컨테이너 이미지 레지스트리에 대해 지역에서 복제를 사용 합니다.
> * 여러 클러스터에서 응용 프로그램 상태를 계획 합니다.
> * 여러 지역에 걸쳐 저장소를 복제 합니다.

## <a name="plan-for-multiregion-deployment"></a>다중 지역 배포 계획

**모범 사례**: 여러 AKS 클러스터를 배포 하는 경우 AKS을 사용할 수 있는 지역을 선택 하 고 쌍을 이루는 지역을 사용 합니다.

AKS 클러스터는 단일 Azure 지역에 배포됩니다. 지역 장애 로부터 시스템을 보호 하려면 여러 지역의 여러 AKS 클러스터에 응용 프로그램을 배포 합니다. AKS 클러스터를 배포할 위치를 계획 하는 경우 다음을 고려 하십시오.

* [**AKS 지역 가용성**](./quotas-skus-regions.md#region-availability): 사용자에 게 가까운 지역을 선택 합니다. AKS는 계속 해 서 새 지역으로 확장 됩니다.
* [**Azure 쌍을 이루는 지역**](../best-practices-availability-paired-regions.md): 지리적 영역에 대해 서로 쌍을 이루는 두 지역을 선택 합니다. 쌍을 이루는 지역은 플랫폼 업데이트를 조정 하 고 필요한 경우 복구 작업의 우선 순위를 지정 합니다.
* **서비스 가용성**: 페어링된 지역이 핫/핫, 핫/웜 또는 핫/콜드 이어야 하는지 여부를 결정 합니다. 두 지역을 동시에 실행 하 고 한 지역이 트래픽 처리를 시작할 *준비가 되셨습니까* ? 또는 한 지역에서 트래픽 제공을 준비 하는 데 시간을 사용 하 시겠습니까?

AKS 지역 가용성 및 쌍을 이루는 지역은 공동 고려 사항입니다. 지역 재해 복구를 함께 관리하도록 설계된 쌍을 이루는 지역에 AKS 클러스터를 배포합니다. 예를 들어 AKS는 미국 동부 및 미국 서부에서 사용할 수 있습니다. 이러한 지역은 쌍을 이룹니다. AKS BC/DR 전략을 만들 때이 두 지역을 선택 합니다.

응용 프로그램을 배포 하는 경우 CI/CD 파이프라인에 다른 단계를 추가 하 여 이러한 여러 AKS 클러스터에 배포 합니다. 배포 파이프라인을 업데이트 하지 않는 경우 응용 프로그램은 지역 및 AKS 클러스터 중 하나에만 배포할 수 있습니다. 보조 지역으로 전송 되는 고객 트래픽은 최신 코드 업데이트를 받지 않습니다.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Azure Traffic Manager를 사용하여 트래픽 라우팅

**모범 사례**: Azure Traffic Manager는 고객에 게 가장 가까운 AKS 클러스터 및 응용 프로그램 인스턴스로 지시할 수 있습니다. 최상의 성능 및 중복성을 위해 AKS 클러스터로 이동 하기 전에 Traffic Manager를 통해 모든 응용 프로그램 트래픽을 보냅니다.

서로 다른 지역에 여러 AKS 클러스터가 있는 경우 Traffic Manager를 사용 하 여 각 클러스터에서 실행 되는 응용 프로그램에 대 한 트래픽 흐름 방식을 제어 합니다. [Azure Traffic Manager](../traffic-manager/index.yml)는 네트워크 트래픽을 여러 Azure 지역에 분산할 수 있는 DNS 기반 트래픽 부하 분산 장치입니다. Traffic Manager를 사용 하 여 클러스터 응답 시간 또는 지리에 따라 사용자를 라우팅합니다.

![Traffic Manager AKS](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

단일 AKS 클러스터가 있는 고객은 일반적으로 지정 된 응용 프로그램의 서비스 IP 또는 DNS 이름에 연결 합니다. Multicluster 배포에서 고객은 각 AKS 클러스터의 서비스를 가리키는 Traffic Manager DNS 이름에 연결 해야 합니다. Traffic Manager 끝점을 사용 하 여 이러한 서비스를 정의 합니다. 각 끝점은 *서비스 부하 분산 장치 IP*입니다. 이 구성을 사용 하 여 한 지역의 Traffic Manager 끝점에서 다른 지역의 끝점으로의 네트워크 트래픽을 보낼 수 있습니다.

![Traffic Manager를 통한 지리적 라우팅](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

Traffic Manager DNS 조회를 수행 하 고 사용자의 가장 적합 한 끝점을 반환 합니다. 중첩 된 프로필은 기본 위치에 우선 순위를 지정할 수 있습니다. 예를 들어 사용자는 일반적으로 가장 가까운 지역에 연결 해야 합니다. 해당 지역에 문제가 있는 경우 대신 Traffic Manager 사용자를 보조 지역으로 보냅니다. 이러한 접근 방식을 통해 고객은 가장 가까운 지리적 지역을 사용할 수 없는 경우에도 응용 프로그램 인스턴스에 연결할 수 있습니다.

끝점과 라우팅을 설정 하는 방법에 대 한 자세한 내용은 [Traffic Manager를 사용 하 여 지리적 트래픽 라우팅 방법 구성](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)을 참조 하세요.

### <a name="application-routing-with-azure-front-door-service"></a>Azure Front 도어 서비스를 사용 하 여 응용 프로그램 라우팅

[Azure Front 도어 서비스](../frontdoor/front-door-overview.md) 는 분할 TCP 기반 애니캐스트 프로토콜을 사용 하 여 최종 사용자가 가장 가까운 앞면 도어 POP (현재 위치)에 신속 하 게 연결할 수 있도록 합니다. Azure Front 도어 서비스의 추가 기능에는 TLS 종료, 사용자 지정 도메인, 웹 응용 프로그램 방화벽, URL 재작성 및 세션 선호도가 포함 됩니다. 애플리케이션 트래픽 요구 사항을 검토하여 어떤 솔루션이 가장 적합한지 알아보세요.

### <a name="interconnect-regions-with-global-virtual-network-peering"></a>글로벌 가상 네트워크 피어 링을 사용 하 여 지역 상호 연결

클러스터가 서로 통신 해야 하는 경우 가상 [네트워크 피어 링](../virtual-network/virtual-network-peering-overview.md)을 통해 두 가상 네트워크를 서로 연결할 수 있습니다. 이 기술은 서로 다른 지리적 지역에도 불구 하 고 Microsoft의 백본 네트워크에서 높은 대역폭을 제공 하 여 가상 네트워크를 서로 상호 연결할 수 있습니다.

AKS 클러스터가 실행 되는 가상 네트워크를 피어 링 하는 필수 구성 요소는 AKS 클러스터의 표준 Load Balancer를 사용 하 여 가상 네트워크 피어 링을 통해 Kubernetes 서비스에 연결할 수 있도록 하는 것입니다.

## <a name="enable-geo-replication-for-container-images"></a>컨테이너 이미지에 지역 복제 사용

**모범 사례**: 컨테이너 이미지를 Azure Container Registry에 저장 하 고 각 AKS 지역에 레지스트리를 지역 복제 합니다.

AKS에 애플리케이션을 배포하고 실행하려면 컨테이너 이미지를 저장하고 끌어오는 방법이 필요합니다. Container Registry는 AKS와 통합 되므로 컨테이너 이미지 또는 투구 차트를 안전 하 게 저장할 수 있습니다. Container Registry은 전 세계의 Azure 지역에 이미지를 자동으로 복제 하는 다중 마스터 지역에서 복제를 지원 합니다. 

성능 및 가용성을 향상 시키려면 Container Registry 지역에서 복제를 사용 하 여 AKS 클러스터가 있는 각 지역에 레지스트리를 만듭니다. 그런 다음 각 AKS 클러스터는 동일한 지역의 로컬 컨테이너 레지스트리에서 컨테이너 이미지를 끌어옵니다.

![컨테이너 이미지에 대 한 지역에서 복제 Container Registry](media/operator-best-practices-bc-dr/acr-geo-replication.png)

Container Registry 지역에서 복제를 사용 하 여 동일한 지역에서 이미지를 가져오는 경우 결과는 다음과 같습니다.

* **더 빠름**: 동일한 Azure 지역 내에서 고속의 대기 시간이 짧은 네트워크 연결에서 이미지를 끌어옵니다.
* **더 안정적**: 지역을 사용할 수 없는 경우 AKS 클러스터는 사용 가능한 컨테이너 레지스트리에서 이미지를 가져옵니다.
* **비용**절감: 데이터 센터 간에 네트워크 송신 비용이 없습니다.

지역에서 복제는 *프리미엄* SKU 컨테이너 레지스트리 기능입니다. 지역에서 복제를 구성 하는 방법에 대 한 자세한 내용은 [Container Registry 지역에서 복제](../container-registry/container-registry-geo-replication.md)를 참조 하세요.

## <a name="remove-service-state-from-inside-containers"></a>컨테이너 내부에서 서비스 상태 제거

**모범 사례**: 가능한 경우 컨테이너 내에 서비스 상태를 저장 하지 마세요. 대신, 다중 지역 복제를 지 원하는 Azure PaaS (platform as a service)를 사용 합니다.

*서비스 상태* 는 서비스가 작동 하는 데 필요한 메모리 내 또는 디스크 내 데이터를 나타냅니다. 상태의 예로는 서비스에서 읽고 쓰는 데이터 구조 및 멤버 변수를 들 수 있습니다. 서비스를 설계 하는 방법에 따라 상태에는 디스크에 저장 된 파일 또는 기타 리소스가 포함 될 수도 있습니다. 예를 들어 데이터베이스에서 데이터 및 트랜잭션 로그를 저장 하는 데 사용 하는 파일이 상태에 포함 될 수 있습니다.

상태는 상태를 조작 하는 코드를 사용 하 여 표면화 된 또는 공동 배치 수 있습니다. 일반적으로 네트워크를 통해 다른 컴퓨터에서 실행 되거나 동일한 시스템에서 프로세스를 실행 하는 데이터베이스 또는 다른 데이터 저장소를 사용 하 여 상태를 외부화 합니다.

컨테이너 및 마이크로 서비스는 내부에서 실행 되는 프로세스가 상태를 유지 하지 않을 때 대부분 복원 력이 높습니다. 응용 프로그램은 거의 항상 몇 가지 상태를 포함 하기 때문에 Azure Cosmos DB, Azure Database for PostgreSQL, Azure Database for MySQL 또는 Azure SQL Database와 같은 PaaS 솔루션을 사용 합니다.

이식 가능한 응용 프로그램을 빌드하려면 다음 지침을 참조 하세요.

* [12 단계 앱 방법론](https://12factor.net/)
* [여러 Azure 지역에서 웹 애플리케이션 실행](/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>스토리지 마이그레이션 계획 작성

**모범 사례**: Azure Storage 사용 하는 경우 주 지역에서 백업 지역으로 저장소를 마이그레이션하는 방법을 준비 하 고 테스트 합니다.

응용 프로그램은 데이터에 대 한 Azure Storage를 사용할 수 있습니다. 응용 프로그램은 서로 다른 지역의 여러 AKS 클러스터에 걸쳐 분산 되므로 저장소를 동기화 된 상태로 유지 해야 합니다. 저장소를 복제 하는 두 가지 일반적인 방법은 다음과 같습니다.

* 인프라 기반 비동기 복제
* 애플리케이션 기반 비동기 복제

### <a name="infrastructure-based-asynchronous-replication"></a>인프라 기반 비동기 복제

Pod를 삭제 한 후에도 응용 프로그램에 영구적 저장소가 필요할 수 있습니다. Kubernetes에서 영구적 볼륨을 사용 하 여 데이터 저장소를 유지할 수 있습니다. 영구적 볼륨은 노드 VM에 탑재 된 다음 pod에 노출 됩니다. Pod가 동일한 클러스터 내의 다른 노드로 이동 하는 경우에도 영구 볼륨이 pod를 따릅니다.

사용 하는 복제 전략은 저장소 솔루션에 따라 달라 집니다. [Gluster](https://docs.gluster.org/en/latest/Administrator%20Guide/Geo%20Replication/), [Ceph](https://docs.ceph.com/docs/master/cephfs/disaster-recovery/), [Rook](https://rook.io/docs/rook/v1.2/ceph-disaster-recovery.html)및 [portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps) 와 같은 일반적인 저장소 솔루션은 재해 복구 및 복제에 대 한 자체 지침을 제공 합니다.

일반적인 전략은 응용 프로그램이 데이터를 쓸 수 있는 일반적인 저장소 지점을 제공 하는 것입니다. 이 데이터는 여러 Azure 지역에 복제된 후 로컬로 액세스됩니다.

![인프라 기반 비동기 복제](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Azure Managed Disks를 사용 하는 경우 다음과 같이 복제 및 DR 솔루션을 선택할 수 있습니다.

* [Azure의 Velero](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)
* [Azure Backup](../backup/backup-overview.md)

### <a name="application-based-asynchronous-replication"></a>애플리케이션 기반 비동기 복제

Kubernetes는 현재 응용 프로그램 기반 비동기 복제에 대 한 기본 구현을 제공 하지 않습니다. 컨테이너와 Kubernetes는 느슨하게 결합 되기 때문에 기존의 응용 프로그램 또는 언어 접근 방식이 작동 해야 합니다. 일반적으로 응용 프로그램 자체는 저장소 요청을 복제 하며,이는 각 클러스터의 기본 데이터 저장소에 기록 됩니다.

![애플리케이션 기반 비동기 복제](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>다음 단계

이 문서에서는 AKS 클러스터에 대 한 비즈니스 연속성 및 재해 복구 고려 사항을 중점적으로 다룹니다. AKS의 클러스터 작업에 대 한 자세한 내용은 모범 사례에 대 한 다음 문서를 참조 하세요.

* [배포할지에 및 클러스터 격리][aks-best-practices-cluster-isolation]
* [기본 Kubernetes 스케줄러 기능][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
