---
title: Azure Site Recovery 서비스를 사용하여 Azure VM을 위한 재해 복구와 Azure ExpressRoute 통합 | Microsoft Docs
description: Azure Site Recovery 및 Azure ExpressRoute를 사용하여 Azure VM을 위한 재해 복구를 설정하는 방법 설명
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 90388d570d027aea3c897f7306a1714fd7e847b3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60772418"
---
# <a name="integrate-azure-expressroute-with-disaster-recovery-for-azure-vms"></a>Azure VM을 위한 재해 복구와 Azure ExpressRoute 통합


이 문서에서는 Azure VM을 위한 재해 복구를 보조 Azure 하위 지역으로 설정할 때 Azure ExpressRoute를 [Azure Site Recovery](site-recovery-overview.md)와 통합하는 방법을 설명합니다.

Site Recovery를 사용하면 Azure VM 데이터를 Azure에 복제하여 Azure VM의 재해를 복구할 수 있습니다.

- Azure VM이 [Azure 관리 디스크](../virtual-machines/windows/managed-disks-overview.md)인 경우 VM 데이터는 보조 하위 지역의 복제된 관리 디스크에 복제됩니다.
- Azure VM이 관리 디스크를 사용하지 않는 경우 VM 데이터는 Azure 저장소 계정에 복제됩니다.
- 복제 엔드포인트는 공용이지만 Azure VM에 대한 복제 트래픽은 인터넷을 벗어나지 않습니다.

ExpressRoute를 사용하면 연결 공급자가 지원하는 개인 연결을 통해 온-프레미스 네트워크를 Microsoft Azure 클라우드로 확장할 수 있습니다. ExpressRoute를 구성한 경우 다음과 같이 Site Recovery와 통합합니다.

- **Azure 지역 간에 복제하는 동안**: Azure VM 재해 복구에 대한 복제 트래픽이 Azure 내에만 있으며 ExpressRoute는 복제에 필요하지 않거나 사용되지 않습니다. 그러나 온-프레미스 사이트에서 기본 Azure 사이트의 Azure VM에 연결하는 경우 해당 Azure VM에 대해 재해 복구를 설정할 때 주의해야 할 여러 가지 문제가 있습니다.
- **Azure 지역 간 장애 조치(failover)**: 중단이 발생한 경우 Azure VM을 기본에서 보조 Azure 하위 지역으로 장애 조치합니다. 보조 하위 지역으로 장애 조치 후 ExpressRoute를 사용하여 보조 하위 지역의 Azure VM에 액세스하려면 여러 단계를 실행해야 합니다.


## <a name="before-you-begin"></a>시작하기 전에

시작하기 전에 다음 개념을 이해해야 합니다.

- ExpressRoute [회로](../expressroute/expressroute-circuit-peerings.md)
- ExpressRoute [라우팅 도메인](../expressroute/expressroute-circuit-peerings.md#routingdomains)
- ExpressRoute [위치](../expressroute/expressroute-locations.md).
- Azure VM [복제 아키텍처](azure-to-azure-architecture.md)
- Azure VM을 위한 [복제를 설정](azure-to-azure-tutorial-enable-replication.md)하는 방법.
- Azure VM을 [장애 조치](azure-to-azure-tutorial-failover-failback.md)하는 방법.


## <a name="general-recommendations"></a>일반 권장 사항

모범 사례를 따르기 위해, 그리고 재해 복구에 효율적인 RTO(복구 시간 목표)를 설정하기 위해 Site Recovery를 ExpressRoute와 통합하도록 설정할 때 다음을 수행하는 것이 좋습니다.

- 보조 하위 지역으로 장애 조치하기 전에 네트워킹 구성 요소를 프로비전:
    - Azure VM에 복제를 사용할 수 있는 경우 Site Recovery는 원본 네트워크 설정을 기반으로 대상 Azure 하위 지역의 네트워크, 서브넷 및 게이트웨이와 같은 네트워킹 리소스를 자동으로 배포할 수 있습니다.
    - Site Recovery는 VNet 게이트웨이와 같은 네트워킹 리소스를 자동으로 설정할 수 없습니다.
    - 장애 조치 전에 이러한 추가 네트워킹 리소스를 프로비전하는 것이 좋습니다. 이 배포 때문에 약간의 가동 중지 시간이 발생하며, 배포 계획 중에 이 점을 고려하지 않으면 전체 복구 시간에 영향을 미칠 수 있습니다.
- 정기적인 재해 복구 반복 연습 실행:
    - 훈련은 데이터 손실이나 가동 중지 시간 없이 복제 전략의 유효성을 검사하며 프로덕션 환경에 영향을 미치지 않습니다. 이는 RTO에 부정적 영향을 미칠 수 있는 막바지 구성 문제를 피하는 데 도움이 됩니다.
    - 반복 훈련을 위해 테스트 장애 조치를 실행하는 경우 복제를 사용할 때 설정한 기본 네트워크 대신에 별도의 Azure VM 네트워크를 사용하는 것이 좋습니다.
- 단일 ExpressRoute 회로가 있는 경우 서로 다른 IP 주소 공간을 사용합니다.
    - 대상 가상 네트워크에 다른 IP 주소 공간을 사용하는 것이 좋습니다. 이렇게 하면 하위 지역 중단 중에 연결을 설정할 때 문제를 피할 수 있습니다.
    - 별도 주소 공간을 사용할 수 없는 경우 IP 주소가 다른 별도의 테스트 네트워크에서 재해 복구 반복 훈련 테스트 장애 조치를 실행해야 합니다. 동일한 ExpressRoute 회로에 IP 주소 공간이 겹치는 Vnet 두 개를 연결할 수 없습니다.

## <a name="replicate-azure-vms-when-using-expressroute"></a>ExpressRoute를 사용할 때 Azure VM 복제


기본 사이트의 Azure VM에 대해 복제를 설정하려 하는데 ExpressRoute를 통해 온-프레미스 사이트에서 이 VM에 연결하는 경우 다음을 수행해야 합니다.

1. 각 Azure VM에 대해 [복제를 사용](azure-to-azure-tutorial-enable-replication.md)합니다.
2. 필요에 따라 Site Recovery에서 네트워킹을 설정하도록 합니다.
    - 복제를 구성하고 사용하도록 설정하면 Site Recovery가 대상 Azure 하위 지역의 네트워크, 서브넷 및 게이트웨이 서브넷을 원본 지역의 요소에 맞게 설정합니다. 또한 Site Recovery는 원본 및 대상 가상 네트워크 간에 매핑합니다.
    - Site Recovery가 이 설정을 자동으로 수행하는 것을 원하지 않는 경우 복제를 사용하기 전에 대상 쪽 네트워크 자원을 만듭니다.
3. 다른 네트워킹 요소 만들기:
    - Site Recovery는 보조 하위 지역에 경로 테이블, VNet 게이트웨이, VNet 게이트웨이 연결, VNet 피어링 또는 다른 네트워킹 리소스 및 연결을 만들지 않습니다.
    - 기본 하위 지역에서 장애 조치를 실행하기 전에 언제든지 보조 하위 지역에 이러한 추가 네트워킹 요소를 만들어야 합니다.
    - [복구 계획](site-recovery-create-recovery-plans.md) 및 자동화 스크립트를 사용하여 이러한 네트워킹 리소스를 설정하고 연결할 수 있습니다.
1. 네트워크 트래픽의 흐름을 제어하기 위해 NVA(네트워크 가상 어플라이언스)를 배포한 경우 다음 사항에 유의하십시오.
    - Azure VM 복제를 위한 Azure의 기본 시스템 경로는 0.0.0.0/0입니다.
    - 또한 NVA 배포는 일반적으로 아웃바운드 인터넷 트래픽이 NVA를 통과하도록 강제하는 기본 경로(0.0.0.0/0)를 정의합니다. 기본 경로는 다른 특정 경로 구성을 찾을 수 없는 경우에 사용됩니다.
    - 이 경우 모든 복제 트래픽이 NVA를 통과하는 경우 NVA가 과부하될 수 있습니다.
    - 온-프레미스 배포에 모든 Azure VM 트래픽을 라우팅하기 위해 기본 경로를 사용하는 경우 동일한 제한이 적용됩니다.
    - 이 시나리오에서 복제 트래픽이 Azure 경계를 벗어나지 않도록 가상 네트워크에 Microsoft.Storage 서비스를 위한 [네트워크 서비스 엔드포인트](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage)를 만드는 것이 좋습니다.

## <a name="replication-example"></a>복제 예제

일반적으로 엔터프라이즈 배포에는 여러 Azure VNet에 분할된 워크로드가 있고 중앙 연결 허브는 인터넷 및 온-프레미스 배포에 대한 외부 연결을 설정합니다. 허브 및 스포크 토폴로지는 일반적으로 ExpressRoute와 함께 사용됩니다.

![장애 조치 전에 ExpressRoute를 포함한 Azure에 온-프레미스](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

- **하위 지역**. 앱은 Azure 동아시아 지역에 배포 됩니다.
- **스포크 VNet**. 앱은 두 개의 스포크 vNet에 배포됩니다.
    - **원본 vNet1**: 10.1.0.0/24.
    - **원본 vNet2**: 10.2.0.0/24.
    - 각 스포크 가상 네트워크는 **허브 vNet**에 연결됩니다.
- **허브 vNet**. 허브 vNet **원본 허브 vNet**: 10.10.10.0/24가 있습니다.
  - 이 허브 vNet은 게이트키퍼 역할을 합니다.
  - 서브넷 간 통신은 모두 이 허브를 통해 이루어집니다.
    - **허브 vNet 서브넷**합니다. 허브 vNet에는 두 개의 서브넷에 있습니다.
    - **NVA 서브넷**: 10.10.10.0/25. 이 서브넷은 NVA(10.10.10.10)를 포함합니다.
    - **게이트웨이 서브넷**: 10.10.10.128/25. 이 서브넷은 ExpressRoute 연결에 연결된 ExpressRoute 게이트웨이를 포함하며 개인 피어링 라우팅 도메인을 통해 온-프레미스를 라우팅합니다.
- 온-프레미스 데이터 센터에는 홍콩의 파트너 에지를 통해 ExpressRoute 회로에 연결됩니다.
- 모든 라우팅은 Azure 경로 테이블(UDR)을 통해 제어됩니다.
- vNet 간 또는 온-프레미스 데이터 센터에 대한 모든 아웃바운드 트래픽은 NVA를 통해 라우팅됩니다.

### <a name="hub-and-spoke-peering-settings"></a>허브 및 스포크 피어링 설정

#### <a name="spoke-to-hub"></a>스포크-허브

**방향** | **설정** | **State**
--- | --- | ---
스포크-허브 | 가상 네트워크 주소 허용 | Enabled
스포크-허브 | 전달된 트래픽 허용 | Enabled
스포크-허브 | 게이트웨이 전송 허용 | 사용 안 함
스포크-허브 | 게이트웨이 제거 사용 | Enabled

 ![허브 피어링 구성에 대한 스포크](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

#### <a name="hub-to-spoke"></a>허브-스포크

**방향** | **설정** | **State**
--- | --- | ---
허브-스포크 | 가상 네트워크 주소 허용 | Enabled
허브-스포크 | 전달된 트래픽 허용 | Enabled
허브-스포크 | 게이트웨이 전송 허용 | Enabled
허브-스포크 | 게이트웨이 제거 사용 | 사용 안 함

 ![스포크 피어링 구성에 대한 허브](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="example-steps"></a>예제 단계

이 예제에서 원본 네트워크에 Azure VM에 대해 복제를 사용하면 다음과 같은 동작이 일어납니다.

1. VM에 대해 [복제를 사용](azure-to-azure-tutorial-enable-replication.md)합니다.
2. Site Recovery는 대상 하위 지역에 복제본 vNet, 서브넷 및 게이트웨이 서브넷을 만듭니다.
3. Site Recovery는 원본 네트워크와 자신이 만드는 복제본 대상 네트워크 사이에 매핑을 만듭니다.
4. 가상 네트워크 게이트웨이, 가상 네트워크 게이트웨이 연결, 가상 네트워크 피어링 또는 다른 네트워킹 자원이나 연결을 수동으로 만듭니다.


## <a name="fail-over-azure-vms-when-using-expressroute"></a>ExpressRoute를 사용할 때 Azure VM 장애 조치

Site Recovery를 사용하여 대상 Azure 하위 지역에 Azure VM을 장애 조치한 후 ExpressRoute [개인 피어링](../expressroute/expressroute-circuit-peerings.md#privatepeering)을 사용하여 이들에 액세스할 수 있습니다.

- 새 연결을 사용하여 대상 vNet에 ExpressRoute를 연결해야 합니다. 기존 ExpressRoute 연결은 자동으로 전송되지 않습니다.
- 대상 vNet에 ExpressRoute 연결을 설정하는 방법은 ExpressRoute 토폴로지에 따라 달라집니다.


### <a name="access-with-two-circuits"></a>두 개의 회로를 사용하여 액세스

#### <a name="two-circuits-with-two-peering-locations"></a>두 개의 피어링 위치를 사용하는 두 개의 회로

이 구성은 ExpressRoute 회로를 하위 지역 재해로부터 보호하는 데 도움이 됩니다. 기본 피어링 위치가 다운되면 다른 위치에서 연결을 계속할 수 있습니다.

- 프로덕션 환경에 연결된 회로는 일반적으로 기본 회로입니다. 보조 회로는 일반적으로 낮은 대역폭을 가지고 있으며 재해가 발생한 경우 늘릴 수 있습니다.
- 장애 조치 후 보조 ExpressRoute 회로에서 대상 vNet로 연결을 설정할 수 있습니다. 또는 재해의 경우 전체 복구 시간을 줄이기 위해 연결을 설정하고 준비할 수 있습니다.
- 기본 및 대상 vNet 둘 다에 동시에 연결한 상태에서 온-프레미스 라우팅이 장애 조치 후 보조 회로 및 연결만 사용하도록 하십시오.
- 원본 및 대상 vNet이 장애 조치 후 새 IP 주소를 받거나 같은 주소를 유지할 수 있습니다. 두 경우 모두에 장애 조치하기 전에 보조 연결을 설정할 수 있습니다.


#### <a name="two-circuits-with-single-peering-location"></a>단일 피어링 위치를 사용하는 두 개의 회로

이 구성은 기본 ExpressRoute 회로의 고장으로부터 보호하는 데 도움이 되지만, 단일 ExpressRoute 피어링 위치가 다운되면 그렇지 않으며 두 회로에 영향을 모두 영향을 미칩니다.

- 온-프레미스 데이터 센터에서 기본 회로를 사용하는 경우 원본 vNet에, 그리고 보조 회로를 사용하는 경우 대상 vNet에 동시에 연결할 수 있습니다.
- 기본 및 대상에 동시에 연결한 상태에서 온-프레미스 라우팅이 장애 조치 후 보조 회로 및 연결만 사용하도록 하십시오.
-   회로가 동일한 피어링 위치에 생성되면 동일한 vNet에 두 회로를 모두 연결할 수 없습니다.

### <a name="access-with-a-single-circuit"></a>단일 회로를 사용하여 액세스

이 구성에서는 ExpressRoute 회로가 한 개만 있습니다. 회로에 중복 연결이 있더라도 한 회로가 다운된 경우 피어링 하위 지역이 다운되면 단일 경로 회로가 회복력을 제공하지 않습니다. 다음 사항에 유의하세요.

- [동일한 지리적 위치](azure-to-azure-support-matrix.md#region-support)의 Azure 하위 지역에 Azure VM을 복제할 수 있습니다. 대상 Azure 하위 지역이 원본과 동일한 위치에 있지 않은 경우 단일 ExpressRoute 회로를 사용한다면 ExpressRoute Premium을 사용하도록 설정해야 합니다. [ExpressRoute 위치](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) 및 [ExpressRoute 가격 책정](https://azure.microsoft.com/pricing/details/expressroute/)에 대해 알아보세요.
- 대상 하위 지역에 동일한 IP 주소 공간을 사용하는 경우 원본과 대상 vNet을 동시에 회로에 연결할 수 없습니다. 이 시나리오에서는    
    -  원본 쪽 연결을 끊은 다음, 대상 쪽 연결을 설정하세요. 이 연결 변경은 Site Recovery 복구 계획의 일부로 스크립트될 수 있습니다. 다음 사항에 유의하세요.
        - 지역 오류에서 주 지역에 액세스할 수 없으면 연결 끊기 작업에 실패할 수 있습니다. 이는 대상 하위 지역에 대한 연결 생성에 영향을 미칠 수 있습니다.
        - 대상 하위 지역에 연결을 만들고 나중에 기본 하위 지역이 복구되는 경우, 동일한 주소 공간에 연결하려는 두 개의 동시 연결 시도가 있으면 패킷 드롭을 경험할 수 있습니다.
        - 이 현상을 방지하려면 기본 연결을 즉시 종료합니다.
        - 기본 하위 지역으로 VM 장애 복구 후 기본 연결은 보조 연결을 끊은 후에 다시 설정할 수 있습니다.
-   대상 vNet에 서로 다른 주소 공간을 사용하는 경우, 동일한 ExpressRoute 회로에서 원본 및 대상 vNet에 동시에 연결할 수 있습니다.


## <a name="failover-example"></a>장애 조치 예제

이 예제에서는 다음과 같은 토폴로지를 사용합니다.

- 서로 다른 두 피어링 위치에 서로 다른 두 개의 ExpressRoute 피어링 회로.
- 장애 조치 후 Azure VM에 대해 사설 IP 주소 유지.
- 대상 복구 하위 지역은 Azure 동남아시아입니다.
- 보조 ExpressRoute 회로 연결은 싱가포르의 파트너 에지를 통해 설정됩니다.

장애 조치 후 동일한 IP 주소와 함께 단일 ExpressRoute 회로를 사용하는 단순한 토폴로지의 경우 [이 문서를 검토](site-recovery-retain-ip-azure-vm-failover.md#hybrid-resources-full-failover)합니다.

### <a name="example-steps"></a>예제 단계
이 예제에서 복구를 자동화하려면 다음을 수행해야 합니다.

1. 복제를 설정하는 단계를 따르세요.
2. 장애 조치 중 또는 후에 이 추가 단계를 사용하여 [Azure VM을 장애 조치](azure-to-azure-tutorial-failover-failback.md)합니다.

    a. 대상 하위 지역 허브 VNet에 Azure ExpressRoute 게이트웨이를 만듭니다. ExpressRoute 회로에 대상 허브 vNet을 연결해야 합니다.

    b. 대상 허브 vNet에서 대상 ExpressRoute 회로에 연결을 만듭니다.

    다. 대상 지역의 허브와 스포크 가상 네트워크 간에 VNet 피어링을 설정합니다. 대상 지역의 피어링 속성은 원본 지역에 있는 속성과 동일합니다.

    d. 허브 VNet의 UDR 및 두 개의 스포크 VNet을 설정합니다.

    - 동일한 IP 주소를 사용할 때 대상 쪽 UDR의 속성이 원본 쪽 속성과 동일합니다.
    - 다른 대상 IP 주소에서 UDR은 적절하게 수정해야 합니다.


위의 단계는 [복구 계획](site-recovery-create-recovery-plans.md)의 일환으로 스크립팅될 수 있습니다. 애플리케이션 연결 및 복구 시간 요구 사항에 따라 위의 단계는 장애 조치를 시작하기 전에 완료될 수도 있습니다.

#### <a name="after-recovery"></a>복구 후

VM을 복구하고 연결을 완료한 후 복구 환경은 다음과 같습니다.

![장애 조치 후 ExpressRoute를 포함한 Azure에 온-프레미스](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)



## <a name="next-steps"></a>다음 단계

[복구 계획](site-recovery-create-recovery-plans.md)을 사용하여 앱 장애 조치를 자동화하기에 대해 알아봅니다.
