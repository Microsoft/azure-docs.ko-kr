---
title: 재해 복구 및 마이그레이션을 위해 Azure Site Recovery를 통해 Azure ExpressRoute를 사용하는 방법 설명 | Microsoft Docs
description: 재해 복구 및 마이그레이션을 위해 Azure Site Recovery 서비스를 통해 Azure ExpressRoute를 사용하는 방법을 설명합니다.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/18/2019
ms.author: mayg
ms.openlocfilehash: bf4cce8a224db81b8db7fae6a69b8b578bb3d47a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60772322"
---
# <a name="azure-expressroute-with-azure-site-recovery"></a>Azure Site Recovery를 사용한 Azure ExpressRoute

Microsoft Azure ExpressRoute를 사용하면 연결 공급자에서 쉽게 처리된 개인 연결을 통해 온-프레미스 네트워크를 Microsoft 클라우드로 확장할 수 있습니다. ExpressRoute를 사용하면 Microsoft Azure, Office 365 및 Dynamics 365와 같은 Microsoft 클라우드 서비스에 대한 연결을 설정할 수 있습니다.

이 문서에서는 재해 복구 및 마이그레이션에 대해 Azure Site Recovery를 사용한 Azure ExpressRoute를 사용하는 방법을 설명합니다.

## <a name="expressroute-circuits"></a>ExpressRoute 회로

ExpressRoute 회로는 연결 공급자를 통한 온-프레미스 인프라와 Microsoft 클라우드 서비스 간의 논리적 연결을 나타냅니다. 여러 ExpressRoute 회로를 주문할 수 있습니다. 각 회로는 동일하거나 다른 지역에 있을 수 있으며 서로 다른 연결 공급자를 통해 프레미스에 연결할 수 있습니다. [여기](../expressroute/expressroute-circuit-peerings.md)에서 ExpressRoute 회로에 대해 자세히 알아봅니다.

## <a name="expressroute-routing-domains"></a>ExpressRoute 라우팅 도메인

ExpressRoute 회로에는 연결된 여러 라우팅 도메인이 있습니다.
-   [Azure 개인 피어링](../expressroute/expressroute-circuit-peerings.md#privatepeering) - Azure 컴퓨팅 서비스, 즉 가상 머신 (IaaS) 및 가상 네트워크 내에 배포된 클라우드 서비스(PaaS)는 개인 피어링 도메인을 통해 연결될 수 있습니다. 개인 피어링 도메인은 Microsoft Azure로의 핵심 네트워크의 신뢰할 수 있는 확장으로 간주됩니다.
-   [Azure 공용 피어링](../expressroute/expressroute-circuit-peerings.md#publicpeering) - Azure Storage, SQL Databases 및 Websites와 같은 서비스는 공용 IP 주소에 제공됩니다. 공용 피어링 라우팅 도메인을 통해 공용 IP 주소(클라우드 서비스의 VIP 포함)에서 호스팅되는 서비스에 개인적으로 연결할 수 있습니다. 새 만들기에 대해 공용 피어링을 사용하지 않으며 Azure PaaS 서비스에 대해 Microsoft 피어링을 대신 사용해야 합니다.
-   [Microsoft 피어링](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) - Microsoft 피어링을 통해 Microsoft 온라인 서비스(Office 365, Dynamics 365 및 Azure PaaS 서비스)에 대해 연결됩니다. Microsoft 피어링은 Azure PaaS 서비스에 연결하는 데 권장하는 라우팅 도메인입니다.

[여기](../expressroute/expressroute-circuit-peerings.md#peeringcompare)에서 ExpressRoute 라우팅 도메인을 비교하고 자세히 알아봅니다.

## <a name="on-premises-to-azure-replication-with-expressroute"></a>ExpressRoute를 사용한 온-프레미스와 Azure 간 복제

Azure Site Recovery를 사용하면 온-프레미스 [Hyper-V 가상 머신](hyper-v-azure-architecture.md), [VMware 가상 머신](vmware-azure-architecture.md) 및 [물리적 서버](physical-azure-architecture.md)에 대해 Azure로 마이그레이션 및 재해 복구를 할 수 있습니다. 온-프레미스와 Azure 간 모든 시나리오의 경우 복제 데이터는 Azure Storage 계정에 전송돼 저장됩니다. 복제 동안 모든 가상 머신 요금을 지불하지 않습니다. Azure에 장애 조치를 실행하면 Site Recovery에서 Azure IaaS 가상 머신을 자동으로 만듭니다.

Site Recovery는 공용 엔드포인트를 통해 Azure Storage 계정에 데이터를 복제합니다. Site Recovery 복제에 대 한 ExpressRoute를 사용 하려면 사용할 수 있습니다 [공용 피어 링](../expressroute/expressroute-circuit-peerings.md#publicpeering) (새로 만들기에 사용 되지 않음) 또는 [Microsoft 피어 링](../expressroute/expressroute-circuit-peerings.md#microsoftpeering)합니다. Microsoft 피어링은 복제에 권장되는 라우팅 도메인입니다. [네트워킹 요구 사항](vmware-azure-configuration-server-requirements.md#network-requirements)이 복제 기준을 충족하는지도 확인합니다. 가상 머신 또는 서버가 Azure 가상 네트워크로 장애 조치된 후 [개인 피어링](../expressroute/expressroute-circuit-peerings.md#privatepeering)을 사용하여 해당 가상 머신 또는 서버에 액세스할 수 있습니다. 개인 피어링에는 복제가 지원되지 않습니다.

온-프레미스 프록시를 사용 하 고 복제 트래픽에 ExpressRoute를 사용 하려는 경우 구성 서버 및 프로세스 서버에서 프록시 무시 목록 구성 해야 합니다. 다음 단계를 따르세요.

- PsExec 도구를 다운로드 [여기](https://aka.ms/PsExec) 시스템 사용자 컨텍스트를 액세스할 수 있습니다.
- 다음 명령줄 psexec-s를 실행 하 여 시스템 사용자 컨텍스트에서 Internet Explorer를 열고-i "%programfiles%\Internet Explorer\iexplore.exe"
- IE에서 프록시 설정 추가
- 바이패스 목록에는 Azure storage URL을 추가 *. blob.core.windows.net

이렇게 하면 통신 프록시를 통해 이동할 수 있지만 ExpressRoute를 통해 복제 트래픽만 전달 되도록 합니다.

결합된 시나리오는 다음 다이어그램, ![ExpressRoute를 포함한 Azure에 온-프레미스](./media/concepts-expressroute-with-site-recovery/site-recovery-with-expressroute.png)에 표시됩니다.

## <a name="azure-to-azure-replication-with-expressroute"></a>ExpressRoute를 사용한 Azure와 Azure 간 복제

Azure Site Recovery를 사용하면 [Azure 가상 머신](azure-to-azure-architecture.md)의 재해 복구가 가능합니다. Azure 가상 머신이 [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md)를 사용하는지 여부에 따라 복제 데이터가 대상 Azure 지역의 Azure Storage 계정 또는 복제본 Managed Disks로 전송됩니다. 복제 엔드포인트가 공용이라고 해도 기본적으로 Azure VM 복제에 대한 복제 트래픽은 원본 가상 네트워크가 존재하는 Azure 지역과 무관하게 인터넷을 통과하지 않습니다. [사용자 지정 경로](../virtual-network/virtual-networks-udr-overview.md#custom-routes)를 사용하여 Azure 기본 시스템 경로의 0.0.0.0/0 주소 접두사를 재정의하고 VM 트래픽을 온-프레미스 NVA(네트워크 가상 어플라이언스)로 우회시킬 수 있지만 Site Recovery 복제에는 이 구성이 권장되지 않습니다. 사용자 지정 경로를 사용하는 경우 복제 트래픽이 Azure 경계를 나가지 않도록 "Storage"에 대한 가상 네트워크에서 [가상 네트워크 서비스 엔드포인트를 생성](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage)해야 합니다.

Azure VM 재해 복구의 경우 기본적으로 ExpressRoute는 복제에 필요하지 않습니다. 가상 머신이 대상 Azure 지역으로 장애 조치된 후 [개인 피어링](../expressroute/expressroute-circuit-peerings.md#privatepeering)을 사용하여 해당 가상 머신에 액세스할 수 있습니다.

온-프레미스 데이터센터에서 원본 지역의 Azure VM에 연결하기 위해 이미 ExpressRoute를 사용하고 있는 경우 장애 조치 대상 지역에서 ExpressRoute 연결을 다시 설정하기 위한 계획을 세울 수 있습니다. 새 가상 네트워크 연결을 통해 대상 지역에 연결하거나 별도 ExpressRoute 회로 및 재해 복구용 연결을 활용하려면 동일한 ExpressRoute 회로를 사용할 수 있습니다. 여러 가능한 시나리오가 [여기](azure-vm-disaster-recovery-with-expressroute.md#fail-over-azure-vms-when-using-expressroute)에 설명돼 있습니다.

[여기](../site-recovery/azure-to-azure-support-matrix.md#region-support)에 설명된 대로 동일한 지리적 클러스터 내에서 Azure 가상 머신을 Azure 지역에 복제할 수 있습니다. 선택한 대상 Azure 지역이 원본과 동일한 지리적 지역 내에 없는 경우 ExpressRoute 프리미엄을 사용해야 할 수 있습니다. 자세한 내용은 [ExpressRoute 위치](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) 및 [ExpressRoute 가격 책정](https://azure.microsoft.com/pricing/details/expressroute/)을 확인하세요.

## <a name="next-steps"></a>다음 단계
- [ExpressRoute 회로](../expressroute/expressroute-circuit-peerings.md)에 대해 자세히 알아봅니다.
- [ExpressRoute 라우팅 도메인](../expressroute/expressroute-circuit-peerings.md#peeringcompare)에 대해 자세히 알아봅니다.
- [ExpressRoute 위치](../expressroute/expressroute-locations.md)에 대해 자세히 알아봅니다.
- 재해 복구에 자세히 알아보려면 [ExpressRoute 사용 하 여 Azure virtual machines](azure-vm-disaster-recovery-with-expressroute.md)합니다.
