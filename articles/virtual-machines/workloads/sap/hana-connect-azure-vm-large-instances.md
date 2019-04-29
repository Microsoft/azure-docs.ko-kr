---
title: Azure(대규모 인스턴스)의 SAP HANA에 대한 가상 머신에서 연결 설정 | Microsoft Docs
description: Azure(대규모 인스턴스)에서 SAP HANA를 사용하기 위해 가상 머신의 연결 설정
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2628cafada47b2602b195c44d4b6f2e6b16012ef
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098813"
---
# <a name="connecting-azure-vms-to-hana-large-instances"></a>Azure VM을 HANA 큰 인스턴스에 연결

문서 [Azure의 SAP HANA(대규모 인스턴스)란?](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) Azure에서 SAP 애플리케이션 레이어를 사용하는 HANA 대규모 인스턴스의 최소 배포가 다음과 같이 나타난다는 점을 언급합니다.

![Azure(큰 인스턴스)에서 SAP HANA 및 온-프레미스로 연결된 Azure VNet](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

Azure Virtual Network 쪽을 자세히 살펴보면 다음에 대한 필요성을 인식하게 됩니다.

- SAP 애플리케이션 계층의 VM을 배포할 Azure Virtual Network의 정의입니다.
- VM이 실제로 배포되는 Azure Virtual Network의 기본 서브넷에 대한 정의입니다.
- 만들어진 Azure Virtual Network에는 하나 이상의 VM 서브넷과 하나의 Azure ExpressRoute 가상 네트워크 게이트웨이 서브넷이 있어야 합니다. 이러한 서브넷은 다음 섹션에서 지정하고 설명한 대로 IP 주소 범위가 할당되어야 합니다.

Azure Virtual Network의 HANA 대규모 인스턴스 생성에 대해 좀 더 자세히 살펴보겠습니다.

## <a name="create-the-azure-virtual-network-for-hana-large-instances"></a>HANA 대규모 인스턴스에 대한 Azure Virtual Network 만들기

>[!Note]
>HANA 대규모 인스턴스에 대한 Azure Virtual Network는 Azure Resource Manager 배포 모델을 사용하여 만들어야 합니다. 일반적으로 클래식 배포 모델로 알려진 이전 Azure 배포 모델은 HANA 대규모 인스턴스 솔루션에 지원되지 않습니다.

Azure Portal, PowerShell, Azure 템플릿 또는 Azure CLI를 사용하여 가상 네트워크를 만들 수 있습니다. 자세한 내용은 [Azure Portal을 사용하여 가상 네트워크 만들기](../../../virtual-network/manage-virtual-network.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#create-a-virtual-network)를 참조하세요. 다음 예제에서는 Azure Portal을 사용하여 만든 가상 네트워크를 살펴봅니다.

가상 네트워크의 정의가 서로 다른 IP 주소 범위로 표시되는 항목과 어떤 관련이 있는지 살펴보겠습니다. **주소 공간**에 대해 이야기할 때 Azure Virtual Network의 사용이 허용된 주소 공간을 의미합니다. 이 주소 공간은 가상 네트워크에서 BGP 경로 전파에 사용할 주소 범위이기도 합니다. 이 **주소 공간**은 여기에서 볼 수 있습니다.

![Azure Portal에 표시된 Azure Virtual Network의 주소 공간](./media/hana-overview-connectivity/image1-azure-vnet-address-space.png)

위 예제의 경우 10.16.0.0/16으로, Azure Virtual Network는 상당히 크고 넓은 IP 주소 범위를 사용했습니다. 따라서 Virtual Network 내의 후속 서브넷의 모든 IP 주소 범위가 주소 공간 내에 자체 범위를 포함할 수 있습니다. 일반적으로 Azure에서는 단일 가상 네트워크에 대해 이러한 대규모 주소 범위를 사용하는 것이 권장되지 않습니다. 하지만 Azure Virtual Network에 정의된 서브넷은 살펴보겠습니다.

![Azure Virtual Network 서브넷 및 해당 IP 주소 범위](./media/hana-overview-connectivity/image2b-vnet-subnets.png)

여기서는 첫 번째 VM 서브넷(여기서는 “default”라고 함)과 “GatewaySubnet”이라는 서브넷을 포함하는 가상 네트워크가 표시됩니다.

위의 두 그래픽에서 **가상 네트워크 주소 공간**은 **Azure VM 서브넷 IP 주소 범위**와 가상 네트워크 게이트웨이 서브넷 IP 주소 범위를 모두 포괄합니다.

**가상 네트워크 주소 공간**을 각 서브넷에서 사용하는 특정 범위로 제한할 수 있습니다. 또한 가상 네트워크의 **가상 네트워크 주소 공간**을 다음과 같이 여러 가지 특정 범위로 정의할 수 있습니다.

![두 공간이 있는 Azure Virtual Network 주소 공간](./media/hana-overview-connectivity/image3-azure-vnet-address-space_alternate.png)

이 경우 **가상 네트워크 주소 공간**에는 두 개의 공간이 정의되어 있습니다. 이 두 공간은 Azure VM 서브넷 IP 주소 범위 및 가상 네트워크 게이트웨이 서브넷 IP 주소 범위에 대해 정의된 IP 주소 범위와 동일합니다. 

이러한 테넌트 서브넷(VM 서브넷)에 원하는 모든 명명 표준을 사용할 수 있습니다. 그러나 Azure(대규모 인스턴스) ExpressRoute 회로 상의 SAP HANA에 연결하는 **각 가상 네트워크에 대한 하나 및 하나만의 게이트웨이 서브넷이 항상 있어야 합니다**. 또한 **이 게이트웨이 서브넷의 이름이 "GatewaySubnet"으로 지정되어야** ExpressRoute 게이트웨이가 제대로 배치될 수 있습니다.

> [!WARNING] 
> 게이트웨이 서브넷의 이름은 항상 “GatewaySubnet”이어야 합니다.

여러 VM 서브넷 및 연속되지 않은 주소 범위를 사용할 수 있습니다. 이러한 주소 범위는 가상 네트워크의 **가상 네트워크 주소 공간**에 포함되어야 합니다. 이는 집계된 형식일 수 있습니다. 또한 VM 서브넷 및 게이트웨이 서브넷의 정확한 범위 목록일 수도 있습니다.

다음은 HANA 대규모 인스턴스에 연결되는 Azure Virtual Network에 대한 중요한 사실을 요약한 것입니다.

- HANA 대규모 인스턴스의 초기 배포를 수행할 때 Microsoft에 **가상 네트워크 주소 공간**을 제출해야 합니다. 
- **가상 네트워크 주소 공간**은 Azure VM 및 가상 네트워크 게이트웨이의 서브넷 IP 주소 범위를 포괄하는 더 큰 범위일 수 있습니다.
- 또는 VM 서브넷 IP 주소 범위와 가상 네트워크 게이트웨이 IP 주소 범위의 다양한 IP 주소 범위를 포함하는 여러 범위를 제출할 수도 있습니다.
- 정의된 **가상 네트워크 주소 공간**은 BGP 라우팅 전파에 사용됩니다.
- 게이트웨이 서브넷의 이름은 **"GatewaySubnet"** 이어야 합니다.
- 주소 공간은 Azure에서 HANA 대규모 인스턴스 단위로 트래픽을 허용 또는 차단하기 위해 HANA 대규모 인스턴스 쪽의 필터로 사용됩니다. Azure 가상 네트워크의 BGP 라우팅 정보와 HANA 대규모 인스턴스 측 필터링을 위해 구성된 IP 주소 범위가 서로 일치해야 합니다. 일치하지 않으면 연결 문제가 발생할 수 있습니다.
- 뒷부분에 나오는 게이트웨이 서브넷에 대한 자세한 내용은 **가상 네트워크를 HANA 대규모 인스턴스 ExpressRoute에 연결** 섹션에서 자세히 설명합니다.



## <a name="different-ip-address-ranges-to-be-defined"></a>다양한 IP 주소 범위 정의 

HANA 대규모 인스턴스를 배포하는 데 필요한 IP 주소 범위에 대해서는 이미 소개했습니다. 하지만 중요한 IP 주소 범위가 몇 가지 더 있습니다. 일부 주소 범위를 좀 더 자세히 살펴보겠습니다. 다음 IP 주소를 모두 Microsoft에 제출해야 하는 것은 아닙니다. 그러나 초기 배포를 위한 요청을 보내기 전에 정의해야 합니다.

- **가상 네트워크 주소 공간**: **가상 네트워크 주소 공간**은 Azure 가상 네트워크에서 사용자가 자신의 주소 공간 매개 변수에 할당하는 IP 주소 범위입니다. 이 네트워크는 SAP HANA 대규모 인스턴스 환경과 연결됩니다.

  이 주소 공간 매개 변수는 다중 선 값으로 하는 것이 좋습니다. 이는 Azure VM 서브넷 범위와 Azure 게이트웨이 서브넷 범위로 구성되어야 합니다. 이 서브넷 범위는 이전 그래픽에서 볼 수 있었습니다. 이는 온-프레미스나 서버 IP 풀 또는 ER-P2P 주소 범위와 겹쳐서는 안 됩니다. 
 
이러한 IP 주소 범위는 어떻게 가져오나요? 

회사 네트워크 팀 또는 서비스 공급자는 네트워크 내에서 사용되지 않는 하나 이상의 IP 주소 범위를 제공해야 합니다. 예를 들어 Azure VM의 서브넷이 10.0.1.0/24이고 Azure 게이트웨이 서브넷의 서브넷이 10.0.2.0/28이라 가정하면,  Azure 가상 네트워크 주소 공간은 10.0.1.0/24와 10.0.2.0/28로 하는 것이 좋습니다. 

주소 공간 값을 집계할 수 있지만 서브넷 범위와 일치시키는 것이 좋습니다. 이렇게 하면 사용하지 않는 IP 주소 범위를 네트워크 내 규모가 더 큰 다른 주소 공간에서 실수로 다시 사용하는 문제를 방지할 수 있습니다. **가상 네트워크 주소 공간은 IP 주소 범위입니다. 이는 초기 배포 요청 시 Microsoft에 제출되어야 합니다**.

- **Azure VM 서브넷 IP 주소 범위:** 이 IP 주소 범위는 사용자가 Azure 가상 네트워크 서브넷 매개 변수에 할당하는 IP 주소 범위입니다. 이 매개 변수는 사용자의 Azure 가상 네트워크에 있으며 SAP HANA 대규모 인스턴스 환경과 연결됩니다. 이 IP 주소 범위는 IP 주소를 Azure VM에 할당하는 데 사용됩니다. 이 범위를 벗어난 IP 주소 범위가 SAP HANA 큰 인스턴스 환경에 연결되도록 허용됩니다. 필요한 경우 여러 Azure VM 서브넷을 사용할 수 있습니다. 각 Azure VM 서브넷에 대해 /24 CIDR 블록이 권장됩니다. 이 주소 범위는 Azure Virtual Network 주소 공간에 사용된 값에 포함되어야 합니다. 

이 IP 주소 범위는 어떻게 가져오나요? 

회사 네트워크 팀 또는 서비스 공급자는 네트워크 내에서 사용되지 않는 IP 주소 범위를 제공해야 합니다.

- **가상 네트워크 게이트웨이 서브넷 IP 주소 범위:** 사용하려는 기능에 따라 권장되는 규모는 다음과 같습니다.
   - Ultra-performance ExpressRoute 게이트웨이: /26 주소 블록 - SKU의 Type II 클래스에 필요함
   - High-performance ExpressRoute 가상 네트워크 게이트웨이(이하)를 사용하고 VPN 및 ExpressRoute의 동시 존재: /27 주소 블록
   - 기타 모든 상황: /28 주소 블록. 이 주소 범위는 “VNet 주소 공간” 값에 사용된 값에 포함되어야 합니다. 이 주소 범위는 Microsoft에 제출해야 하는 Azure Virtual Network 주소 공간 값에 사용된 값에 포함되어야 합니다. 이 IP 주소 범위는 어떻게 가져오나요? 회사 네트워크 팀 또는 서비스 공급자는 네트워크 내에서 현재 사용되지 않는 IP 주소 범위를 제공해야 합니다. 

- **ER-P2P 연결용 주소 범위:** 이 범위는 SAP HANA 대규모 인스턴스 ExpressRoute(ER) P2P 연결을 위한 IP 범위입니다. 이 IP 주소 범위는 /29 CIDR IP 주소 범위여야 합니다. 이 범위는 온-프레미스 또는 기타 Azure IP 주소 범위와 겹치면 안 됩니다. 이 IP 주소 범위는 ExpressRoute 가상 게이트웨이에서 SAP HANA 대규모 인스턴스 서버로 ER 연결을 설정하는 데 사용됩니다. 이 IP 주소 범위는 어떻게 가져오나요? 회사 네트워크 팀 또는 서비스 공급자는 네트워크 내에서 현재 사용되지 않는 IP 주소 범위를 제공해야 합니다. **이 범위는 IP 주소 범위입니다. 이는 초기 배포 요청 시 Microsoft에 제출되어야 합니다**.
  
- **서버 IP 풀 주소 범위:** 이 IP 주소 범위는 개별 IP 주소를 HANA 대규모 인스턴스 서버에 할당하는 데 사용됩니다. 권장되는 서브넷 크기는 /24 CIDR 블록입니다. 필요한 경우 64개 IP 주소만큼 더 작은 수도 있습니다. 이 범위에서 처음 30개 IP 주소는 Microsoft에서 사용하도록 예약됩니다. 범위의 크기를 선택할 때 이러한 사실을 고려해야 합니다. 이 범위는 온-프레미스 또는 기타 Azure IP 주소와 겹치면 안 됩니다. 이 IP 주소 범위는 어떻게 가져오나요? 회사 네트워크 팀 또는 서비스 공급자는 네트워크 내에서 현재 사용되지 않는 IP 주소 범위를 제공해야 합니다. 

  **이 범위는 초기 배포를 요청할 때 Microsoft에 제출해야 하는 IP 주소 범위입니다.**
 
앞서 설명한 IP 주소 범위를 정의하고 계획해야 합니다. 그러나 모든 IP 주소 범위를 Microsoft에 전송할 필요는 없습니다. Microsoft에 이름을 지정해야 하는 IP 주소 범위는 다음과 같습니다.

- Azure Virtual Network 주소 공간
- ER-P2P 연결에 대한 주소 범위
- 서버 IP 풀 주소 범위

HANA 대규모 인스턴스에 연결해야 하는 추가 가상 네트워크를 추가하려면 Microsoft에 추가하는 새 Azure Virtual Network 주소 공간을 제출해야 합니다. 

다음은 사용자가 구성하여 최종적으로 Microsoft에 제출해야 할 몇 가지 다양한 범위의 예입니다. Azure 가상 네트워크 주소 공간의 값은 첫 번째 예에서는 집계되지 않습니다. 그러나 그 값은 최초 Azure VM 서브넷 IP 주소 및 가상 네트워크 게이트웨이 서브넷 IP 주소의 범위를 통해 정의됩니다. 

Azure Virtual Network 주소 공간의 일부로 추가 VM 서브넷의 추가 IP 주소 범위를 구성하고 제출하면 Azure Virtual Network 내에서 여러 VM 서브넷을 사용할 수 있습니다.

![Azure(큰 인스턴스)에서 SAP HANA 최소 배포에 필요한 IP 주소 범위](./media/hana-overview-connectivity/image4b-ip-addres-ranges-necessary.png)

Microsoft에 제출하는 데이터를 집계할 수도 있습니다. 이 경우 Azure Virtual Network의 주소 공간은 하나의 공간만 포함합니다. 이전 예제의 IP 주소 범위를 사용하면 집계된 가상 네트워크 주소 공간은 다음 이미지와 같을 수 있습니다.

![Azure(큰 인스턴스)에서 SAP HANA 최소 배포에 필요한 IP 주소 범위의 두 번째 가능성](./media/hana-overview-connectivity/image5b-ip-addres-ranges-necessary-one-value.png)

예제에는 Azure 가상 네트워크의 주소 공간을 정의한 두 개의 작은 범위 대신 4096개의 IP 주소를 포괄하는 하나의 더 큰 범위가 있습니다. 주소 공간의 그러한 큰 정의는 다소 큰 범위를 사용하지 않고 남겨 둡니다. 가상 네트워크 주소 공간 값이 BGP 경로 전파에 사용되므로 온-프레미스 또는 네트워크의 다른 곳에서 사용하지 않는 범위를 사용하면 라우팅 문제가 발생할 수 있습니다. 

따라서 주소 공간을 사용된 실제 서브넷 주소 공간과 밀접하게 연계하는 것이 좋습니다. 가상 네트워크에서 가동 중단 시간이 발생하지 않고도 필요할 때 새 주소 공간 값을 나중에 언제든지 추가할 수 있습니다.
 
> [!IMPORTANT] 
> ER-P2P, 서버 IP 풀 및 Azure Virtual Network 주소 공간의 각 IP 주소 범위는 서로 또는 네트워크에서 사용되는 다른 범위와 겹치지 **않아야 합니다**. 각각은 고유해야 합니다. 앞에 나온 두 개의 그래픽과 같이 다른 범위의 서브넷일 수도 없습니다. 범위 간 겹침이 발생하면 Azure Virtual Network는 ExpressRoute 회로에 연결되지 않을 수 있습니다.

## <a name="next-steps-after-address-ranges-have-been-defined"></a>주소 범위를 정의한 후 다음 단계
IP 주소 범위를 정의한 후에는 다음 작업이 수행되어야 합니다.

1. Azure Virtual Network 주소 공간, ER-P2P 연결 및 서버 IP 풀 주소 범위에 대한 IP 주소 범위와 문서 시작 부분에 나열된 다른 데이터를 함께 제출합니다. 이때 가상 네트워크 및 VM 서브넷 만들기를 시작할 수도 있습니다. 
2. ExpressRoute 회로는 Microsoft가 Azure 구독과 HANA 대규모 인스턴스 스탬프 사이에 만듭니다.
3. 테넌트 네트워크는 Microsoft의 큰 인스턴스 스탬프에서 생성됩니다.
4. Microsoft는 HANA 대규모 인스턴스와 통신할 Azure Virtual Network 주소 공간의 IP 주소를 허용하도록 Azure(큰 인스턴스)의 SAP HANA에 네트워킹을 구성합니다.
5. Microsoft는 사용자가 구매한 Azure(대규모 인스턴스) SKU의 특정 SAP HANA에 따라 테넌트 네트워크의 컴퓨팅 단위를 지정합니다. 또한 스토리지를 할당 및 탑재하고 운영 체제(SUSE 또는 Red Hat Linux)를 설치합니다. 이러한 단위에 대한 IP 주소는 Microsoft에 제출한 서버 IP 풀 주소 범위를 벗어나는 주소를 지정합니다.

배포 프로세스의 끝부분에는 Microsoft에서 다음 데이터를 사용자에게 제공합니다.
- HANA 대규모 인스턴스에 Azure Virtual Network를 연결하는 ExpressRoute 회로에 Azure Virtual Network를 연결하는 데 필요한 정보:
     - 권한 부여 키
     - ExpressRoute PeerID
- ExpressRoute 회로 및 Azure Virtual Network를 설정한 후 HANA 대규모 인스턴스에 액세스하기 위한 데이터.

[Azure(대규모 인스턴스)의 SAP HANA 설정](https://azure.microsoft.com/resources/sap-hana-on-azure-large-instances-setup/) 문서에서 HANA 대규모 인스턴스를 연결하는 순서를 확인할 수 있습니다. 대다수의 다음 단계는 해당 문서의 배포 예에 표시됩니다. 

## <a name="next-steps"></a>다음 단계

- [가상 네트워크를 HANA 대규모 인스턴스 ExpressRoute에 연결](hana-connect-vnet-express-route.md)을 참조하세요.
