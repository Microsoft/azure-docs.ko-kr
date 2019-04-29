---
title: Azure Site Recovery에서 네트워크 보안 그룹 사용 | Microsoft Docs
description: 재해 복구 및 마이그레이션을 위해 Azure Site Recovery에서 네트워크 보안 그룹을 사용하는 방법을 설명합니다.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 0c06283080a4ee51f863714e4c515672299b420d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60773034"
---
# <a name="network-security-groups-with-azure-site-recovery"></a>Azure Site Recovery에서 네트워크 보안 그룹 사용

네트워크 보안 그룹은 네트워크 트래픽을 가상 네트워크의 리소스로 제한하는 데 사용됩니다. [NSG(네트워크 보안 그룹)](../virtual-network/security-overview.md#network-security-groups)에는 원본 또는 대상 IP 주소, 포트 및 프로토콜에 따라 인바운드 또는 아웃바운드 네트워크 트래픽을 허용하거나 거부하는 보안 규칙 목록이 포함되어 있습니다.

Resource Manager 배포 모델에서 NSG는 서브넷 또는 개별 네트워크 인터페이스에 연결할 수 있습니다. NSG를 서브넷에 연결하면 규칙이 서브넷에 연결된 모든 리소스에 적용됩니다. 이미 연결된 NSG가 있는 서브넷 내의 개별 네트워크 인터페이스에 NSG를 연결하여 트래픽을 추가로 제한할 수 있습니다.

이 문서에서는 Azure Site Recovery에서 네트워크 보안 그룹을 사용하는 방법에 대해 설명합니다.

## <a name="using-network-security-groups"></a>네트워크 보안 그룹 사용

개별 서브넷에는 0 또는 1개의 연결된 NSG가 있을 수 있습니다. 개별 네트워크 인터페이스에도 0 또는 1개의 연결된 NSG가 있을 수 있습니다. 따라서 NSG를 먼저 서브넷에 연결한 다음, 다른 NSG를 VM의 네트워크 인터페이스에 연결하여 가상 머신에 대한 이중 트래픽 제한을 효과적으로 사용할 수 있습니다. 이 경우에 대한 NSG 규칙의 적용은 트래픽의 방향과 적용되는 보안 규칙의 우선 순위에 따라 달라집니다.

다음과 같이 하나의 가상 머신을 사용하는 간단한 예를 살펴보겠습니다.
-   가상 머신이 **Contoso 서브넷**에 배치되어 있습니다.
-   **Contoso 서브넷**이 **서브넷 NSG**와 연결되어 있습니다.
-   VM 네트워크 인터페이스가 **VM NSG**와 추가로 연결되어 있습니다.

![Site Recovery와 NSG](./media/concepts-network-security-group-with-site-recovery/site-recovery-with-network-security-group.png)

이 예의 경우 인바운드 트래픽에서는 서브넷 NSG가 먼저 평가됩니다. 서브넷 NSG를 통해 허용되는 모든 트래픽이 VM NSG에서 평가됩니다. 아웃바운드 트래픽의 경우 반대 방향으로 적용되어 VM NSG가 먼저 평가됩니다. VM NSG를 통해 허용되는 모든 트래픽이 NSG 서브넷에서 평가됩니다.

이렇게 하면 세부적인 보안 규칙을 적용할 수 있습니다. 예를 들어 서브넷에 속한 일부 애플리케이션 VM(예: 프런트 엔드 VM)에 대한 인바운드 인터넷 액세스는 허용하고, 다른 VM(예: 데이터베이스 및 다른 백 엔드 VM)에 대한 인바운드 인터넷 액세스는 제한할 수 있습니다. 이 경우 서브넷 NSG에 더 완화된 규칙을 적용하여 인터넷 트래픽을 허용하고, VM NSG에서 액세스를 거부하여 특정 VM에 대한 액세스를 제한할 수 있습니다. 아웃바운드 트래픽에도 동일하게 적용할 수 있습니다.

이러한 NSG 구성을 설정하는 경우 올바른 우선 순위가 [보안 규칙](../virtual-network/security-overview.md#security-rules)에 적용되는지 확인합니다. 낮은 번호의 우선 순위가 더 높기 때문에 규칙은 낮은 번호가 높은 번호보다 먼저 처리되는 우선 순위 순서로 처리됩니다. 트래픽이 규칙과 일치하면 처리가 중지됩니다. 따라서 우선 순위가 높은 규칙과 동일한 특성을 가진 우선 순위가 낮은 규칙(높은 번호)은 처리되지 않습니다.

네트워크 인터페이스와 서브넷에 네트워크 보안 그룹이 적용되는 시점을 항상 알 수는 없습니다. 네트워크 인터페이스에 대한 [효과적인 보안 규칙](../virtual-network/virtual-network-network-interface.md#view-effective-security-rules)을 확인하여 네트워크 인터페이스에 적용되는 집계 규칙을 확인할 수 있습니다. 또한 [Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md)에서 [IP 흐름 확인](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) 기능을 사용하여 네트워크 인터페이스 간에 통신이 허용되는지 여부를 결정할 수도 있습니다. 이 도구에서는 통신이 허용되는지 여부 및 어떤 네트워크 보안 규칙이 트래픽을 허용하거나 거부하는지를 알려줍니다.

## <a name="on-premises-to-azure-replication-with-nsg"></a>NSG를 사용한 온-프레미스에서 Azure로의 복제

Azure Site Recovery를 사용하면 온-프레미스 [Hyper-V 가상 머신](hyper-v-azure-architecture.md), [VMware 가상 머신](vmware-azure-architecture.md) 및 [물리적 서버](physical-azure-architecture.md)에 대해 Azure로 마이그레이션 및 재해 복구를 할 수 있습니다. 온-프레미스와 Azure 간 모든 시나리오의 경우 복제 데이터는 Azure Storage 계정에 전송돼 저장됩니다. 복제 동안 모든 가상 머신 요금을 지불하지 않습니다. Azure에 장애 조치를 실행하면 Site Recovery에서 Azure IaaS 가상 머신을 자동으로 만듭니다.

Azure로 장애 조치한 후에 VM을 만들면 NSG를 사용하여 네트워크 트래픽을 가상 네트워크 및 VM으로 제한할 수 있습니다. Site Recovery는 장애 조치 작업의 일부로 NSG를 만들지 않습니다. 장애 조치(failover)를 시작하기 전에 필요한 Azure NSG를 만드는 것이 좋습니다. 그런 다음, Site Recovery의 강력한 [복구 계획](site-recovery-create-recovery-plans.md)에서 자동화 스크립트를 사용하여 장애 조치 중에 자동으로 장애 조치된 VM에 NSG를 연결할 수 있습니다.

예를 들어 장애 조치 후 VM 구성이 위에서 설명한 [시나리오 예](concepts-network-security-group-with-site-recovery.md#using-network-security-groups)와 비슷한 경우 다음과 같이 수행할 수 있습니다:
-   대상 Azure 지역에서 DR 계획의 일부로 **Contoso VNet** 및 **Contoso 서브넷**을 만들 수 있습니다.
-   동일한 DR 계획의 일부로 **서브넷 NSG** 및 **VM NSG**를 모두 만들고 구성할 수도 있습니다.
-   이미 NSG와 서브넷을 모두 사용할 수 있으므로 **서브넷 NSG**를 **Contoso 서브넷**과 즉시 연결할 수 있습니다.
-   **VM NSG**는 복구 계획을 사용하여 장애 조치 중에 VM과 연결할 수 있습니다.

NSG를 만들고 구성한 경우 [테스트 장애 조치](site-recovery-test-failover-to-azure.md)를 실행하여 스크립팅된 NSG 연결 및 장애 조치 후 VM 연결을 확인하는 것이 좋습니다.

## <a name="azure-to-azure-replication-with-nsg"></a>NSG를 사용한 Azure 간 복제

Azure Site Recovery를 사용하면 [Azure 가상 머신](azure-to-azure-architecture.md)의 재해 복구가 가능합니다. Azure VM에 대한 복제를 사용하도록 설정하는 경우 Site Recovery는 대상 지역에 복제본 가상 네트워크(서브넷, 게이트웨이 서브넷 포함)를 만들고, 원본과 대상 가상 네트워크 간에 필요한 매핑을 만들 수 있습니다. 또한 대상 쪽 네트워크와 서브넷을 미리 만들 수 있고, 복제를 사용하도록 설정하는 동안 동일한 대상 쪽 네트워크와 서브넷을 사용할 수도 있습니다. Site Recovery는 [장애 조치](azure-to-azure-tutorial-failover-failback.md) 이전에 VM을 대상 Azure 지역에 만들지 않습니다.

Azure VM 복제의 경우 원본 Azure 지역의 NSG 규칙이 복제 트래픽에 대한 [아웃바운드 연결](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges)을 허용하는지 확인합니다. 또한 이 [NSG 구성 예제](azure-to-azure-about-networking.md#example-nsg-configuration)를 통해 이러한 필수 규칙을 테스트하고 확인할 수도 있습니다.

Site Recovery는 장애 조치 작업의 일부로 NSG를 만들거나 복제하지 않습니다. 장애 조치(failover)를 시작하기 전에 대상 Azure 지역에 필요한 NSG를 만드는 것이 좋습니다. 그런 다음, Site Recovery의 강력한 [복구 계획](site-recovery-create-recovery-plans.md)에서 자동화 스크립트를 사용하여 장애 조치 중에 자동으로 장애 조치된 VM에 NSG를 연결할 수 있습니다.

앞에서 설명한 [시나리오 예](concepts-network-security-group-with-site-recovery.md#using-network-security-groups)를 고려하면 다음과 같습니다.
-   VM에 대한 복제를 사용하도록 설정하면 Site Recovery는 대상 Azure 지역에 **Contoso VNet** 및 **Contoso 서브넷**의 복제본을 만들 수 있습니다.
-   대상 지역에 필요한 추가 규칙이 허용되도록 원하는 **서브넷 NSG** 및 **VM NSG** 복제본을 만들 수 있습니다(예: 각각 **대상 서브넷 NSG** 및 **대상 VM NSG**로 명명).
-   그러면 이미 NSG와 서브넷을 모두 사용할 수 있으므로 **대상 서브넷 NSG**를 대상 지역 서브넷과 즉시 연결할 수 있습니다.
-   **대상 VM NSG**는 복구 계획을 사용하여 장애 조치 중에 VM과 연결할 수 있습니다.

NSG를 만들고 구성한 경우 [테스트 장애 조치](azure-to-azure-tutorial-dr-drill.md)를 실행하여 스크립팅된 NSG 연결 및 장애 조치 후 VM 연결을 확인하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계
-   [네트워크 보안 그룹](../virtual-network/security-overview.md#network-security-groups)에 대해 자세히 알아보세요.
-   NSG [보안 규칙](../virtual-network/security-overview.md#security-rules)에 대해 자세히 알아보세요.
-   NSG에 대한 [효과적인 보안 규칙](../virtual-network/diagnose-network-traffic-filter-problem.md)에 대해 자세히 알아보세요.
-   애플리케이션 장애 조치(failover)를 자동화하는 [복구 계획](site-recovery-create-recovery-plans.md)에 대해 자세히 알아봅니다.
