---
title: '아키텍처: 전역 전송 네트워크 아키텍처'
titleSuffix: Azure Virtual WAN
description: 가상 WAN의 글로벌 전송 네트워크 아키텍처에 대해 알아보기
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 02/06/2020
ms.author: cherylmc
ms.openlocfilehash: 17d0e678008c76da32f20562aa795e83e49c80e4
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064974"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>글로벌 전송 네트워크 아키텍처 및 가상 WAN

최신 기업은 클라우드 및 온-프레미스에서 하이퍼 분산 응용 프로그램, 데이터 및 사용자 간에 다양 한 연결이 필요 합니다. 기업에서 글로벌 전송 네트워크 아키텍처를 채택 하 여 클라우드 중심의 최신 글로벌 엔터프라이즈 IT 공간을 통합 하 고, 연결 하 고, 제어할 수 있습니다.

글로벌 전송 네트워크 아키텍처는 클라우드 호스 티 드 네트워크 ' 허브 '를 사용 하 여 여러 종류의 ' 스포크 '에 분산 될 수 있는 엔드포인트 간의 전이적 연결을 지 원하는 클래식 허브 및 스포크 연결 모델을 기반으로 합니다.

이 모델에서 스포크는 다음과 같을 수 있습니다.
* 가상 네트워크 (Vnet)
* 실제 분기 사이트
* 원격 사용자
* 인터넷

![허브 및 스포크](./media/virtual-wan-global-transit-network-architecture/figure1.png)

**그림 1: 글로벌 전송 허브 및 스포크 네트워크**

그림 1에서는 지리적으로 분산 된 사용자, 물리적 사이트 및 Vnet 클라우드에서 호스트 되는 네트워킹 허브를 통해 상호 연결 된 글로벌 전송 네트워크의 논리적 보기를 보여 줍니다. 이 아키텍처는 네트워킹 엔드포인트 간에 논리적 1 홉 전송 연결을 사용 하도록 설정 합니다.

## <a name="globalnetworktransit"></a>가상 WAN을 사용 하는 글로벌 전송 네트워크

Azure 가상 WAN은 Microsoft에서 관리 하는 클라우드 네트워킹 서비스입니다. 이 서비스가 구성 된 모든 네트워킹 구성 요소는 Microsoft에서 호스트 되 고 관리 됩니다. 가상 WAN에 대 한 자세한 내용은 [가상 Wan 개요](virtual-wan-about.md) 문서를 참조 하세요.

Azure 가상 WAN은 Vnet, 분기 사이트, SaaS 및 PaaS 응용 프로그램, 사용자 등의 전 세계적으로 분산 된 클라우드 작업 집합 간에 다양 한 연결을 가능 하 게 하 여 글로벌 전송 네트워크 아키텍처를 허용 합니다.

![Azure 가상 WAN](./media/virtual-wan-global-transit-network-architecture/figure2.png)

**그림 2: 글로벌 전송 네트워크 및 가상 WAN**

Azure 가상 WAN 아키텍처에서 가상 WAN 허브는 Azure 지역에 프로 비전 되어 분기, Vnet 및 원격 사용자를 연결 하도록 선택할 수 있습니다. 물리적 분기 사이트는 프리미엄 Express 경로 또는 사이트 간 Vpn에 의해 허브에 연결 되 고 Vnet는 VNet 연결로 허브에 연결 되며 원격 사용자는 사용자 VPN (지점 및 사이트 간 Vpn)을 사용 하 여 허브에 직접 연결할 수 있습니다. 또한 가상 WAN은 한 지역의 VNet을 다른 지역의 가상 WAN 허브에 연결할 수 있는 지역 간 VNet 연결을 지원 합니다.

스포크 (분기, Vnet, 사용자) 수가 가장 많은 지역에서 단일 가상 WAN 허브를 만든 다음 다른 지역의 스포크를 허브에 연결 하 여 가상 WAN을 설정할 수 있습니다. 이는 엔터프라이즈 공간이 대부분의 원격 스포크를 가진 한 지역에 있는 경우 좋은 옵션입니다.  
  
## <a name="hubtohub"></a>허브 간 연결

엔터프라이즈 클라우드 공간은 여러 클라우드 지역에 걸쳐 있을 수 있으며, 물리적 사이트 및 사용자에 게 가장 가까운 지역에서 클라우드에 액세스 하는 것이 최적 (대기 시간)입니다. 글로벌 전송 네트워크 아키텍처의 핵심 원리 중 하나는 모든 클라우드 및 온-프레미스 네트워크 엔드포인트 간에 지역 간 연결을 사용 하도록 설정 하는 것입니다. 즉, 한 지역의 클라우드에 연결 된 분기의 트래픽은 [Azure 글로벌 네트워크](https://azure.microsoft.com/global-infrastructure/global-network/)에서 사용 하도록 설정 된 허브 간 연결을 사용 하 여 다른 지역에 있는 다른 분기 또는 VNet에 도달할 수 있습니다.

![지역 간](./media/virtual-wan-global-transit-network-architecture/figure3.png)

**그림 3: 가상 WAN 교차 지역 연결**

단일 가상 WAN에서 여러 허브를 사용 하도록 설정 하는 경우 허브에서 허브로의 링크를 통해 자동으로 상호 연결 되므로 여러 지역에 걸쳐 분산 된 분기와 Vnet 간에 전역 연결을 사용할 수 있습니다. 

또한 동일한 가상 WAN의 일부인 허브는 서로 다른 지역 액세스 및 보안 정책에 연결 될 수 있습니다. 자세한 내용은이 문서의 뒷부분에 나오는 [보안 및 정책 제어](#security) 를 참조 하세요.

## <a name="anytoany"></a>임의 연결

글로벌 전송 네트워크 아키텍처는 가상 WAN 허브를 통해 임의 연결을 사용 하도록 설정 합니다. 이 아키텍처는 빌드 및 유지 관리가 더 복잡 한 스포크 간 전체 메시 또는 부분 메시 연결의 필요성을 제거 하거나 감소 시킵니다. 또한 허브 및 스포크 네트워크의 라우팅 제어를 구성 하 고 유지 관리 하는 것이 더 쉽습니다.

모든 연결 (전역 아키텍처의 컨텍스트에서)은 전 세계에 분산 된 사용자, 분기, 데이터 센터, Vnet 및 응용 프로그램이 "전송" 허브를 통해 서로 연결할 수 있도록 합니다. Azure 가상 WAN은 글로벌 전송 시스템 역할을 합니다.

![임의 대상](./media/virtual-wan-global-transit-network-architecture/figure4.png)

**그림 4: 가상 WAN 트래픽 경로**

Azure 가상 WAN은 다음과 같은 글로벌 전송 연결 경로를 지원 합니다. 괄호 안의 문자는 그림 4에 매핑됩니다.

* VNet (a)에 분기
* 분기 분기 (b)
  * Express 경로 Global Reach 및 가상 WAN
* 원격 사용자-VNet (c)
* 원격 사용자-분기 (d)
* VNet 간 (e)
* 허브-분기 간 (f)
* 허브 간-허브-VNet (g)
* VNet 간-허브-VNet (h)

### <a name="branch-to-vnet-a-and-branch-to-vnet-cross-region-g"></a>브랜치 to VNet (a) 및 분기 간 (g)

VNet은 Azure 가상 WAN에서 지 원하는 기본 경로입니다. 이 경로를 사용 하 여 Azure Vnet에 배포 된 Azure IAAS enterprise 워크 로드에 분기를 연결할 수 있습니다. 분기는 Express 경로 또는 사이트 간 VPN을 통해 가상 WAN에 연결 될 수 있습니다. 전송은 트래픽은 VNet 연결을 통해 가상 WAN 허브에 연결 되는 Vnet에 연결 됩니다. 가상 WAN은 게이트웨이를 분기 사이트로 자동으로 전송할 수 있으므로 가상 WAN에 대 한 명시적 [게이트웨이 전송은](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) 필요 하지 않습니다. 가상 wan에 SD WAN CPE를 연결 하는 방법에 대 한 [가상 Wan 파트너](virtual-wan-configure-automation-providers.md) 문서를 참조 하세요.

### <a name="expressroute-global-reach-and-virtual-wan"></a>Express 경로 Global Reach 및 가상 WAN

Express 경로는 온-프레미스 네트워크를 Microsoft 클라우드에 연결 하는 개인적이 고 복원 력 있는 방법입니다. 가상 WAN은 Express 경로 회로 연결을 지원 합니다. Express 경로를 사용 하 여 가상 WAN에 분기 사이트를 연결 하려면 1) 프리미엄 회로 2) 회로가 Global Reach 사용 가능한 위치에 있어야 합니다.

Express 경로 Global Reach는 Express 경로에 대 한 추가 기능입니다. Global Reach를 통해 Express 경로 회로를 함께 연결 하 여 온-프레미스 네트워크 간에 개인 네트워크를 만들 수 있습니다. Express 경로를 사용 하 여 Azure 가상 WAN에 연결 된 분기에는 Express 경로 Global Reach 서로 통신 해야 합니다.

이 모델에서 Express 경로를 사용 하 여 가상 WAN 허브에 연결 된 각 분기에서 VNet 간 경로를 사용 하 여 Vnet에 연결할 수 있습니다. Express 경로 Global Reach는 Azure WAN을 통해 더 최적의 경로를 사용할 수 있으므로 분기 간 트래픽은 허브를 전송 하지 않습니다.

### <a name="branch-to-branch-b-and-branch-to-branch-cross-region-f"></a>분기 간 (b) 및 분기 간 분기 (f)

분기는 Express 경로 회로 및/또는 사이트 간 VPN 연결을 사용 하 여 Azure 가상 WAN 허브에 연결할 수 있습니다. 분기와 가장 가까운 지역에 있는 가상 WAN 허브에 분기를 연결할 수 있습니다.

이 옵션을 통해 기업은 Azure 백본을 활용 하 여 분기를 연결할 수 있습니다. 그러나이 기능을 사용할 수 있는 경우에도 Azure 가상 WAN을 통해 분기를 연결 하는 이점 및 사설 WAN 사용의 이점을 비교 해야 합니다.  

### <a name="remote-user-to-vnet-c"></a>원격 사용자-VNet (c)

원격 사용자 클라이언트에서 가상 WAN으로 지점 및 사이트 간 연결을 사용 하 여 Azure에 대 한 직접 보안 원격 액세스를 사용 하도록 설정할 수 있습니다. 엔터프라이즈 원격 사용자는 더 이상 회사 VPN을 사용 하 여 클라우드로 hairpin 필요가 없습니다.

### <a name="remote-user-to-branch-d"></a>원격 사용자-분기 (d)

원격 사용자-분기 경로를 사용 하면 Azure에 대 한 지점 및 사이트 간 연결을 사용 하는 원격 사용자가 클라우드를 통해 전송을 하 여 온-프레미스 워크 로드 및 응용 프로그램에 액세스할 수 있습니다. 이 경로를 통해 원격 사용자는 Azure 및 온-프레미스에 배포 된 워크 로드에 유연 하 게 액세스할 수 있습니다. 기업은 Azure Virtual WAN에서 중앙 클라우드 기반 보안 원격 액세스 서비스를 사용 하도록 설정할 수 있습니다.

### <a name="vnet-to-vnet-transit-e-and-vnet-to-vnet-cross-region-h"></a>VNet 간 전송 (e) 및 VNet 간 (h)

VNet 간 전송 기능을 사용 하면 여러 Vnet에서 구현 된 다중 계층 응용 프로그램을 상호 연결 하기 위해 Vnet에서 서로 연결할 수 있습니다. 필요에 따라 VNet 피어 링을 통해 Vnet를 서로 연결할 수 있으며,이는 VWAN 허브를 통해 전송 하지 않아도 되는 일부 시나리오에 적합 합니다.

## <a name="security"></a>보안 및 정책 제어

Azure 가상 WAN 허브는 하이브리드 네트워크에서 모든 네트워킹 엔드포인트을 상호 연결 하 고 잠재적으로 모든 전송 네트워크 트래픽을 표시 합니다. 클라우드 기반 보안, 액세스 및 정책 제어를 사용 하기 위해 VWAN 허브 내에 Azure 방화벽을 배포 하 여 가상 WAN 허브를 보안 가상 허브로 변환할 수 있습니다. Azure 방화벽 관리자에서 가상 WAN 허브의 Azure 방화벽 오케스트레이션을 수행할 수 있습니다.

[Azure 방화벽 관리자](https://go.microsoft.com/fwlink/?linkid=2107683) 는 글로벌 전송 네트워크에 대 한 보안을 관리 하 고 확장 하는 기능을 제공 합니다. Azure 방화벽 관리자는 Azure 방화벽과 함께 타사를 통해 라우팅, 글로벌 정책 관리, 고급 인터넷 보안 서비스를 중앙에서 관리 하는 기능을 제공 합니다.

![Azure 방화벽으로 보호 된 가상 허브](./media/virtual-wan-global-transit-network-architecture/figure5.png)

**그림 5: Azure 방화벽으로 보호 된 가상 허브**

가상 WAN에 대 한 Azure 방화벽은 다음과 같은 글로벌 보안 전송 연결 경로를 지원 합니다. 괄호 안의 문자는 그림 5에 매핑됩니다.

* VNet 간 보안 전송 (e)
* VNet 간 또는 타사 보안 서비스 (i)
* 지점 및 인터넷 간 또는 타사 보안 서비스 (j)

### <a name="vnet-to-vnet-secured-transit-e"></a>VNet 간 보안 전송 (e)

VNet 간 보안 전송에서는 Vnet가 가상 WAN 허브의 Azure 방화벽을 통해 서로 연결할 수 있습니다.

### <a name="vnet-to-internet-or-third-party-security-service-i"></a>VNet 간 또는 타사 보안 서비스 (i)

VNet 간 또는 타사 보안 전송에서는 Vnet가 가상 WAN 허브의 Azure 방화벽을 통해 인터넷 또는 지원 되는 타사 보안 서비스에 연결할 수 있습니다.

### <a name="branch-to-internet-or-third-party-security-service-j"></a>지점 및 인터넷 간 또는 타사 보안 서비스 (j)
분기 간 또는 타사 보안 전송에서는 가상 WAN 허브의 Azure 방화벽을 통해 인터넷 또는 지원 되는 타사 보안 서비스에 연결할 수 있습니다.

## <a name="next-steps"></a>다음 단계

가상 WAN을 사용 하 여 연결을 만들고 VWAN 허브에 Azure 방화벽을 배포 합니다.

* [가상 WAN을 사용 하 여 사이트 간 연결](virtual-wan-site-to-site-portal.md)
* [가상 WAN을 사용한 Express 경로 연결](virtual-wan-expressroute-portal.md)
* [VWAN에서 Azure FW를 배포 하는 azure 방화벽 관리자](https://go.microsoft.com/fwlink/?linkid=2107683)
