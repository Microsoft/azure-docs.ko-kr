---
title: Azure Site Recovery를 사용하여 Azure VM 장애 조치(failover) 중에 IP 주소 유지 | Microsoft Docs
description: Azure Site Recovery를 사용하여 보조 지역으로 재해 복구를 수행하기 위해 Azure VM을 장애 조치(failover)할 때 IP 주소를 보존하는 방법을 설명합니다.
ms.service: site-recovery
ms.date: 4/9/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 618d60417aa6b582eaef94bf75dcf16c74750f83
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61277304"
---
# <a name="retain-ip-addresses-during-failover"></a>장애 조치(failover) 중에 IP 주소 유지

[Azure Site Recovery](site-recovery-overview.md)는 VM을 다른 Azure 지역에 복제하고, 중단이 발생하면 장애 조치(failover)하고, 상황이 정상으로 돌아오면 주 지역으로 장애 복구하는 재해 복구를 Azure VM에 사용할 수 있습니다.

장애 조치(failover) 중에 대상 지역의 IP 주소를 원본 지역과 동일하게 유지할 수 있습니다.

- 기본적으로 Azure VM에 재해 복구를 사용하도록 설정하면 Site Recovery는 원본 리소스 설정에 따라 대상 리소스를 만듭니다. 고정 IP 주소를 사용하여 구성된 Azure VM의 경우 Site Recovery는 대상 VM에 동일한 IP 주소(사용 중이 아닌 경우)를 프로비전하려고 시도합니다. Site Recovery가 주소 지정을 처리하는 방법에 대한 전체 설명을 보려면 [이 문서를 검토](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms)하세요.
- 간단한 애플리케이션인 경우 기본 구성으로 충분합니다. 좀 더 복잡한 앱인 경우 장애 조치(failover) 후에 연결이 예상대로 작동하도록 추가 리소스를 프로비전 해야 할 수도 있습니다.


이 문서에서는 좀 더 복잡한 예제 시나리오에서 IP 주소를 유지하는 몇 가지 예를 제공합니다. 다음은 그 예입니다.

- 모든 리소스가 Azure에서 실행되는 회사의 장애 조치(failover)
- 하이브리드 배포를 사용하고 리소스가 온-프레미스와 Azure에서 실행되는 회사의 장애 조치(failover)

## <a name="resources-in-azure-full-failover"></a>Azure 리소스: 전체 장애 조치(failover)

A사의 모든 앱은 Azure에서 실행됩니다.

### <a name="before-failover"></a>장애 조치(failover) 전

다음은 장애 조치(failover) 전의 아키텍처입니다.

- A사는 원본 Azure 지역과 대상 Azure 지역의 네트워크 및 서브넷이 동일합니다.
- 회사에서는 RTO(복구 시간 목표)를 줄이기 위해 SQL Server Always On, 도메인 컨트롤러 등에 복제본 노드를 사용합니다. 이러한 복제본 노드는 대상 지역의 다른 VNet에 있으므로 원본 지역과 대상 지역 간에 VPN 사이트 간 연결을 설정할 수 있습니다. 원본과 대상에서 동일한 IP 주소 공간이 사용되는 경우에는 이것이 불가능합니다.  
- 다음은 장애 조치(failover) 전의 네트워크 아키텍처입니다.
    - 주 지역은 Azure 동아시아
        - 동아시아에는 주소 공간이 10.1.0.0/16인 VNet(**원본 VNet**)이 있습니다.
        - 동아시아의 워크로드는 VNet의 세 개 서브넷에 분산됩니다.
            - **서브넷 1**: 10.1.1.0/24
            - **서브넷 2**: 10.1.2.0/24,
            - **서브넷 3**: 10.1.3.0/24
    - 보조(대상) 지역은 Azure 동남아시아
        - 동남 아시아에는 **원본 VNet**과 동일한 복구 VNet(**Recovery VNet**)이 있습니다.
        - 동남 아시아에는 주소 공간이 10.2.0.0/16인 추가 VNet(**Azure VNet**)이 있습니다.
        - **Azure VNet**에는 주소 공간이 10.2.4.0/24인 서브넷(**Subnet 4**)이 포함됩니다.
        - SQL Server Always On에 대 한 복제본 노드, 도메인 컨트롤러 등에 위치한 **Subnet 4**합니다.
    - **원본 VNet** 및 **Azure VNet**은 VPN 사이트 간 연결을 통해 연결됩니다.
    - **Recovery VNet**은 다른 가상 네트워크와 연결되어 있지 않습니다.
    - **A사**는 복제된 항목의 대상 IP 주소를 할당/확인합니다. 대상 IP는 각 VM의 원본 IP와 같습니다.

![전체 장애 조치(failover) 전의 Azure 리소스](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>장애 조치(failover) 후

원본 지역에서 중단이 발생하면 A사는 모든 리소스를 대상 지역으로 장애 조치(failover)할 수 있습니다.

- A사는 장애 조치(failover) 전에 이미 사용되고 있던 대상 IP 주소를 사용하여 장애 조치(failover)를 오케스트레이션하고, 장애 조치(failover) 후 자동으로 **Recovery VNet**과 **Azure VNet** 간에 연결을 설정할 수 있습니다. 다음 다이어그램은 이 내용을 그림으로 표현한 것입니다.
- 앱 요구 사항에 따라 장애 조치(failover) 전, 도중(중간 단계로) 또는 후에 대상 영역의 두 VNet(**Recovery VNet** 및 **Azure VNet**) 간 연결을 설정할 수 있습니다.
  - 회사에서는 [복구 계획](site-recovery-create-recovery-plans.md)을 사용하여 언제 연결을 설정할 것인지 지정할 수 있습니다.
  - 회사에서는 VNet 피어링 또는 사이트 간 VPN을 사용하여 VNet을 연결할 수 있습니다.
      - VNet 피어링은 VPN 게이트웨이를 사용하지 않으며 여러 제약 조건이 있습니다.
      - VNet 피어링 [가격 책정](https://azure.microsoft.com/pricing/details/virtual-network)은 VNet-VNet VPN Gateway [가격 책정](https://azure.microsoft.com/pricing/details/vpn-gateway)과 다른 방식으로 계산됩니다. 장애 조치(failover)의 경우 예측할 수 없는 네트워크 문제를 최소화화기 위해 일반적으로 연결 형식을 비롯한 여러 연결 방법을 원본 네트워크와 동일하게 사용하는 것이 좋습니다.

    ![Azure 리소스 전체 장애 조치(failover)](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover2.png)



## <a name="resources-in-azure-isolated-app-failover"></a>Azure 리소스: 격리된 앱 장애 조치(failover)

앱 수준에서 장애 조치(failover)가 필요할 수 있습니다. 예를 들어 전용 서브넷의 특정 앱 또는 앱 계층을 장애 조치(failover)할 수 있습니다.

- 이 시나리오에서는 IP 주소를 유지할 수 있지만, 연결 불일치 가능성이 증가하므로 일반적으로 권장하지 않습니다. 또한 동일한 Azure VNet 내부의 다른 서브넷과 연결이 끊어집니다.
- 서브넷 수준 앱 장애 조치(failover)를 수행하는 보다 나은 방법은 다른 대상 IP 주소를 장애 조치(failover)에 사용하거나(원본 VNet의 다른 서브넷에 연결해야 하는 경우) 원본 지역의 전용 VNet에 있는 각 앱을 격리하는 것입니다. 후자의 방식을 사용하면 원본 지역에서 네트워크 간 연결을 설정하고, 대상 지역으로 장애 조치(failover)할 때 동일한 동작을 에뮬레이트할 수 있습니다.  

이 예제에서 A사는 전용 VNet의 원본 지역에 앱을 배치하고, 이러한 VNet 간에 연결을 설정합니다. 이 디자인을 사용하면 A사는 격리된 앱을 장애 조치(failover)하고 대상 네트워크의 원본 사설 IP 주소를 유지할 수 있습니다.

### <a name="before-failover"></a>장애 조치(failover) 전

다음은 장애 조치(failover) 전의 아키텍처입니다.

- 애플리케이션 VM이 Azure 동아시아 주 지역에 호스트됩니다.
    - **App1** VM은 VNet **원본 VNet 1**: 10.1.0.0/16에 있습니다.
    - **App2** VM은 VNet **원본 VNet 2**: 10.2.0.0/16에 있습니다.
    - **원본 VNet 1**에는 두 개의 서브넷이 있습니다.
    - **원본 VNet 2**에는 두 개의 서브넷이 있습니다.
- 보조(대상) 지역은 Azure 동남 아시아입니다. 동남 아시아에는 **Source VNet 1** 및 **Source VNet 2**와 동일한 복구 VNet(**Recovery VNet 1** 및 **Recovery VNet 2**)이 있습니다.
        - **Recovery VNet 1** 및 **Recovery VNet 2**에는 각각 **Source VNet 1** 및 **Source VNet 2**의 서브넷과 일치하는 두 개의 서브넷이 있습니다. 동남 아시아에는 주소 공간이 10.3.0.0/16인 추가 VNet(**Azure VNet**)이 있습니다.
        - **Azure VNet**에는 주소 공간이 10.3.4.0/24인 서브넷(**Subnet 4**)이 포함됩니다.
        SQL Server Always On에 대 한 복제본 노드, 도메인 컨트롤러 등에 위치한 **Subnet 4**합니다.
- 여러 개의 사이트 간 VPN 연결이 있습니다. 
    - **Source VNet 1** 및 **Azure VNet**
    - **Source VNet 2** 및 **Azure VNet**
    - **Source VNet 1** 및 **Source VNet 2**는 VPN 사이트 간 연결을 통해 연결됩니다.
- **Recovery VNet 1** 및 **Recovery VNet 2**는 다른 VNet과 연결되지 않습니다.
- **A사**는 RTO를 줄이기 위해 **Recovery VNet 1** 및 **Recovery VNet 2**에 VPN 게이트웨이를 구성합니다.  
- **Recovery VNet1**과 **Recovery VNet2**는 다른 가상 네트워크와 연결되어 있지 않습니다.
- RTO(복구 시간 목표)를 감소시키기 위해 장애 조치 전에 **Recovery VNet1**과 **Recovery VNet2**에 VPN 게이트웨이가 구성됩니다.

    ![앱 장애 조치(failover) 전의 Azure 리소스](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover2.png)

### <a name="after-failover"></a>장애 조치(failover) 후

단일 앱(이 예에서는 **Source VNet 2)에 영향을 주는 중단 또는 문제 발생 시, A사는 영향을 받는 앱을 다음과 같이 복구할 수 있습니다.


- **Source VNet1**과 **Source VNet2** 및 **Source VNet2**와 **Azure VNet** 사이의 VPN 연결을 해제합니다.
- **Source VNet1**과 **Recovery VNet2** 및 **Recovery VNet2**와 **Azure VNet** 사이에 VPN 연결을 설정합니다.
- **Source VNet2**의 VM을 **Recovery VNet2**로 장애 조치(failover)합니다.

![Azure 리소스 앱 장애 조치(failover)](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover2.png)


- 더 많은 애플리케이션과 네트워크 연결을 포함하도록 이 예제를 확장할 수 있습니다. 권장 사항은 원본에서 대상으로 장애 조치(failover)할 때 가능하면 유사한 연결 모델을 따르는 것입니다.
- VPN 게이트웨이는 공용 IP 주소와 게이트웨이 홉을 사용하여 연결을 설정합니다. 공용 IP를 사용하지 않으려는 경우 또는 추가 홉을 사용하지 않으려는 경우 [Azure VNet 피어링](../virtual-network/virtual-network-peering-overview.md)을 사용하여 [지원되는 Azure 지역](../virtual-network/virtual-network-manage-peering.md#cross-region)에서 가상 네트워크를 피어링하면 됩니다.

## <a name="hybrid-resources-full-failover"></a>하이브리드 리소스: 전체 장애 조치(failover)

이 시나리오의 **B사**는 하이브리드 비즈니스를 실행하고 있고, 애플리케이션 인프라의 일부가 Azure에서 실행 중이고 나머지는 온-프레미스에서 실행 중입니다. 

### <a name="before-failover"></a>장애 조치(failover) 전

다음은 장애 조치(failover) 전 네트워크 아키텍처의 모습입니다.

- 애플리케이션 VM이 Azure 동아시아에 호스트됩니다.
- 동아시아에는 주소 공간이 10.1.0.0/16인 VNet(**원본 VNet**)이 있습니다.
  - 동아시아의 워크로드는 **Source VNet**의 세 개 서브넷에 분산됩니다.
    - **서브넷 1**: 10.1.1.0/24
    - **서브넷 2**: 10.1.2.0/24,
    - **서브넷 3**: 10.1.3.0/24 주소 공간이 10.1.0.0/16인 Azure 가상 네트워크를 활용합니다. 이 가상 네트워크의 이름은 **Source VNet**입니다.
      - 보조(대상) 지역은 Azure 동남아시아:
  - 동남 아시아에는 **원본 VNet**과 동일한 복구 VNet(**Recovery VNet**)이 있습니다.
- 동아시아의 VM은 Azure ExpressRoute 또는 사이트 간 VPN을 통해 온-프레미스 데이터 센터에 연결됩니다.
- RTO를 줄이기 위해 B사는 장애 조치(failover) 전에 Azure 동남 아시아의 Recovery VNet에 게이트웨이를 프로비전합니다.
- B사는 복제된 VM의 대상 IP 주소를 할당/확인합니다. 대상 IP 주소는 각 VM의 원본 IP 주소와 같습니다.


![장애 조치(failover) 전 온-프레미스에서 Azure 연결](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>장애 조치(failover) 후


원본 지역에서 중단이 발생하면 B사는 모든 리소스를 대상 지역으로 장애 조치(failover)할 수 있습니다.

- B사는 장애 조치(failover) 전에 이미 사용되고 있던 대상 IP 주소를 사용하여 장애 조치(failover)를 오케스트레이션하고, 장애 조치(failover) 후 자동으로 **Recovery VNet**과 **Azure VNet** 간에 연결을 설정할 수 있습니다.
- 앱 요구 사항에 따라 장애 조치(failover) 전, 도중(중간 단계로) 또는 후에 대상 영역의 두 VNet(**Recovery VNet** 및 **Azure VNet**) 간 연결을 설정할 수 있습니다. 회사에서는 [복구 계획](site-recovery-create-recovery-plans.md)을 사용하여 언제 연결을 설정할 것인지 지정할 수 있습니다.
- Azure 동남 아시아와 온-프레미스 데이터 센터 간에 연결을 설정하기 전에 Azure 동아시아와 온-프레미스 데이터 센터 간의 원래 연결을 끊어야 합니다.
- 온-프레미스 라우팅은 대상 지역 및 게이트웨이 사후 장애 조치(failover)를 가리키도록 다시 구성됩니다.

![장애 조치(failover) 후 온-프레미스에서 Azure 연결](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover2.png)

## <a name="hybrid-resources-isolated-app-failover"></a>하이브리드 리소스: 격리된 앱 장애 조치(failover)

B사는 격리된 앱을 서브넷 수준에서 장애 조치(failover)할 수 없습니다. 원본 및 복구 VNets의 주소 공간이 동일하고 원래 원본과 온-프레미스의 연결이 활성 상태이기 때문입니다.

 - 앱 복원력을 높이기 위해 B사는 각 앱을 전용 Azure VNet에 배치해야 합니다.
 - 각 앱을 별도의 VNet에 배치하면 B사는 격리된 앱을 장애 조치(failover)하고, 원본 연결을 대상 지역으로 라우팅할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[복구 계획](site-recovery-create-recovery-plans.md)에 대해 알아봅니다.
