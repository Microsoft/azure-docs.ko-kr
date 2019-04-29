---
title: Azure Site Recovery를 통해 장애 조치(failover) 후 공용 IP 주소 사용 | Microsoft Docs
description: 재해 복구 및 마이그레이션을 위해 Azure Site Recovery 및 Azure Traffic Manager를 사용하여 공용 IP 주소를 설정하는 방법 설명
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 1f20818f0b899eede9fff05d71e98c8bffb94b0a
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62101955"
---
# <a name="set-up-public-ip-addresses-after-failover"></a>장애 조치(failover) 후 공용 IP 주소 설정

공용 IP 주소를 통해 인터넷 리소스가 Azure 리소스에 대한 인바운드와 통신할 수 있습니다. 또한 공용 IP 주소를 사용하면 Azure 리소스가 리소스에 할당된 IP 주소에 인터넷 및 공용 Azure 서비스에 대한 아웃바운드와 통신할 수 있습니다.
- Azure Virtual Machines (VM), Azure Application Gateways, Azure Load Balancer, Azure VPN Gateways 등 인터넷에서 리소스로 인바운드 통신. VM에 공용 IP 주소가 할당되어 있지 않은 경우라도 VM이 부하 분산 장치 백엔드 풀의 일부이고 부하 분산 장치에 공용 IP 주소가 할당되어 있는 한, 인터넷에서 VM 등의 일부 리소스와 계속 통신할 수 있습니다.
- 예측 가능한 IP 주소를 사용하는 인터넷으로의 아웃바운드 연결. 예를 들어 공용 IP 주소가 할당되지 않은 가상 머신도 인터넷으로의 아웃바운드 통신을 수행할 수는 있지만, 이 가상 머신의 주소는 기본적으로 Azure에서 예측할 수 없는 공용 주소로 변환한 네트워크 주소입니다. 공용 IP 주소를 리소스에 할당하면 아웃바운드 연결에 사용되는 IP 주소를 확인할 수 있습니다. 이 주소는 예측은 가능하지만 선택한 할당 방법에 따라 변경될 수 있습니다. 자세한 내용은 [공용 IP 주소 만들기](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address)를 참조하세요. Azure 리소스의 아웃바운드 연결에 대한 자세한 내용은 [아웃바운드 연결 이해](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 참조하세요.

Azure Resource Manager에서 공용 IP 주소는 자체 속성이 있는 리소스입니다. 공용 IP 주소 리소스를 연결할 수 있는 리소스는 다음과 같습니다.

* 가상 머신 네트워크 인터페이스
* 인터넷 연결 부하 분산 장치
* VPN 게이트웨이
* 애플리케이션 게이트웨이

이 문서에서는 Site Recovery를 통해 공용 IP 주소를 사용하는 방법을 설명합니다.

## <a name="public-ip-address-assignment-using-recovery-plan"></a>복구 계획을 사용하여 공용 IP 주소 할당

프로덕션 애플리케이션의 공용 IP 주소는 **장애 조치(failover) 시 유지할 수 없습니다**. 장애 조치(failover) 프로세스의 일부로 포함된 워크로드에는 대상 지역에서 사용 가능한 Azure 공용 IP 리소스를 할당해야 합니다. 이 단계는 수동으로 수행할 수도 있고 복구 계획을 통해 자동으로 수행할 수도 있습니다. 복구 계획은 복구 그룹으로 컴퓨터를 수집합니다. 그러므로 체계적인 복구 프로세스를 정의할 수 있습니다. 복구 계획을 사용하여 순서를 적용하고 Azure 또는 스크립트로 장애 조치(failover)에 대한 Azure Automation Runbook을 사용하여 각 단계에 필요한 작업을 자동화할 수 있습니다.

설정 방법은 다음과 같습니다.
- [복구 계획](../site-recovery/site-recovery-create-recovery-plans.md#create-a-recovery-plan)을 만들고 필요에 따라 워크로드를 계획에 그룹화합니다.
- [Azure Automation Runbook](../site-recovery/site-recovery-runbook-automation.md#customize-the-recovery-plan) 스크립트를 사용해 장애 조치(failover)된 VM에 공용 IP 주소를 연결하는 단계를 추가하여 계획을 사용자 지정합니다.

 
## <a name="public-endpoint-switching-with-dns-level-routing"></a>DNS 수준 라우팅을 통한 공용 엔드포인트 전환

Azure Traffic Manager에서는 엔드포인트 간의 DNS 수준 라우팅이 가능하며, DR 시나리오에서는 [RTO를 단축](../site-recovery/concepts-traffic-manager-with-site-recovery.md#recovery-time-objective-rto-considerations)할 수 있습니다. 

다음 항목에서 Traffic Manager를 사용하는 장애 조치(failover) 시나리오에 대해 자세히 알아보세요.
1. [온-프레미스와 Azure 장애 조치](../site-recovery/concepts-traffic-manager-with-site-recovery.md#on-premises-to-azure-failover) Traffic Manager를 사용 하 여 
2. Traffic Manager를 사용한 [Azure 간의 장애 조치(failover)](../site-recovery/concepts-traffic-manager-with-site-recovery.md#azure-to-azure-failover) 

설정 방법은 다음과 같습니다.
- [Traffic Manager 프로필](../traffic-manager/traffic-manager-create-profile.md)을 만듭니다.
- **우선 순위** 라우팅 방법을 사용하여 엔드포인트 두 개를 만듭니다. 그중 하나는 원본용 **기본** 엔드포인트이고 다른 하나는 Azure용 **장애 조치(failover)** 엔드포인트입니다. **기본** 엔드포인트는 우선 순위 1에 할당되고 **장애 조치(failover)** 엔드포인트는 우선 순위 2에 할당됩니다.
- **기본** 엔드포인트는 원본 환경의 위치(Azure 내부 또는 외부)에 따라 [Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints)일 수도 있고 [외부](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints)일 수도 있습니다.
- **장애 조치(failover)** 엔드포인트는 **Azure** 엔드포인트로 생성됩니다. 이 엔드포인트는 재해 이벤트에서 Traffic Manager가 사용하는 외부 연결 엔드포인트이므로 **정적 공용 IP 주소**를 사용합니다.

## <a name="next-steps"></a>다음 단계
- [Azure Site Recovery와 함께 Traffic Manager 사용](../site-recovery/concepts-traffic-manager-with-site-recovery.md) 문서를 자세히 살펴봅니다.
- Traffic Manager [라우팅 메서드](../traffic-manager/traffic-manager-routing-methods.md)에 대해 자세히 알아봅니다.
- 애플리케이션 장애 조치(failover)를 자동화하는 [복구 계획](site-recovery-create-recovery-plans.md)에 대해 자세히 알아봅니다.
