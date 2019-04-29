---
title: 네트워킹 고려 사항 - Azure Dedicated HSM | Microsoft Docs
description: Azure Dedicated HSM 배포에 적용되는 네트워킹 고려 사항의 개요
services: dedicated-hsm
author: barclayn
manager: barbkess
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: barclayn
ms.openlocfilehash: 042ecabe38969a6a26c27622b8c3d25193b3e7c2
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62118037"
---
# <a name="azure-dedicated-hsm-networking"></a>Azure Dedicated HSM 네트워킹

Azure Dedicated HSM에는 보안 수준이 높은 네트워킹 환경이 필요합니다. 분산형 애플리케이션을 사용하거나 고가용성 시나리오를 위해 Azure 클라우드에서 고객의 IT 환경(온-프레미스)으로 되돌아가는 경우는 true입니다. 이는 Azure 네트워킹을 통해 가능하며 반드시 해결해야 할 4개의 고유 영역이 있습니다.

- Azure의 VNet(Virtual Network) 내에서 HSM 디바이스 만들기
- 온-프레미스를 클라우드 기반 리소스에 연결하여 HSM 디바이스 구성 및 관리
- 가상 네트워크를 만들고 연결하여 애플리케이션 리소스 및 HSM 디바이스 간 상호 연결
- 지역 간 가상 네트워크를 연결하여 상호 통신 및 고가용성 시나리오를 사용 하도록 설정

## <a name="virtual-network-for-your-dedicated-hsms"></a>전용 HSM에 대한 가상 네트워크

전용 HSM은 가상 네트워크에 통합되며 Azure에서 고객 고유의 개인 네트워크에 배치됩니다. 이렇게 하면 가상 머신의 디바이스 또는 가상 네트워크의 컴퓨팅 리소스에 액세스할 수 있습니다.  
Azure 서비스를 가상 네트워크에 통합하는 방법 및 제공되는 기능에 대한 내용은 [Azure 서비스에 대한 가상 네트워크](../virtual-network/virtual-network-for-azure-services.md) 설명서를 참조하세요.

### <a name="virtual-networks"></a>가상 네트워크

전용 HSM 디바이스를 프로비전하기 전에 먼저 고객은 Azure에서 Virtual Network를 만들거나 고객 구독에 이미 있는 Virtual Network를 사용해야 합니다. 가상 네트워크는 전용 HSM 디바이스에 대한 보안 경계를 정의합니다. 가상 네트워크를 만드는 방법에 대한 자세한 내용은 [가상 네트워크 설명서](../virtual-network/virtual-networks-overview.md)를 참조하세요.

### <a name="subnets"></a>서브넷

서브넷은 가상 네트워크를 서브넷에 배치하는 Azure 리소스가 사용할 수 있는 개별 주소 공간으로 구분합니다. 전용 HSM은 가상 네트워크의 서브넷에 배포됩니다. 고객의 서브넷에 배포되는 각 전용 HSM 디바이스는 이 서브넷에서 개인 IP 주소를 수신합니다. HSM 디바이스가 배포되는 서브넷은 서비스: Microsoft.HardwareSecurityModules/dedicatedHSMs에 명시적으로 위임되어야 합니다. 그러면 서브넷에 배포를 위해 HSM 서비스에 특정 권한이 부여됩니다. 전용 HSM에 위임하면 서브넷에 특정 정책 제한을 적용합니다. NSG(네트워크 보안 그룹) 및 UDR(사용자 정의 경로)은 현재 위임된 서브넷에서 지원되지 않습니다. 결과적으로 전용 HSM에 위임되는 서브넷은 HSM 리소스를 배포하는 데만 사용될 수 있습니다. 따라서 다른 고객의 모든 리소스를 서브넷에 배포하지 못합니다.


### <a name="expressroute-gateway"></a>ExpressRoute 게이트웨이

현재 아키텍처의 요구 사항은 HSM 디바이스를 Azure에 통합할 수 있도록 해당 HSM 디바이스를 배치해야 하는 고객 서브넷에서 ER 게이트웨이를 구성하는 것입니다. 이 ER 게이트웨이는 온-프레미스 위치를 Azure의 고객 HSM 디바이스에 연결하는 데 활용할 수 없습니다.

## <a name="connecting-your-on-premises-it-to-azure"></a>온-프레미스 IT를 Azure에 연결

클라우드 기반 리소스를 만드는 경우 일반적인 요구 사항은 온-프레미스 IT 리소스로 다시 비공개 연결하는 것입니다. 전용 HSM의 경우 이 요구 사항은 HSM 클라이언트 소프트웨어가 HSM 디바이스를 구성하고, 또한 분석을 위해 HSM에서 로그를 백업하고 풀링하는 것과 같은 작업이 대부분입니다. 옵션이 있으므로 여기서 주요 결정 포인트는 연결의 특성입니다.  가장 유연한 옵션은 사이트 간 VPN입니다. Azure 클라우드의 리소스(HSM 포함)와 보안 통신이 필요한 여러 온-프레미스 리소스가 있을 수 있기 때문입니다. VPN 디바이스 연결을 용이하게 하려면 고객 조직이 필요합니다. 단일 관리 워크스테이션 같은 단일 엔드포인트 온-프레미스만 있는 경우 지점 및 사이트 간 VPN 연결을 사용할 수 있습니다.
연결 옵션에 대한 자세한 내용은 [VPN Gateway 계획 옵션](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable)을 참조하세요.

> [!NOTE]
> 이때 ExpressRoute는 온-프레미스 리소스에 연결하는 옵션이 아닙니다. 위에서 설명한 대로 사용된 ExpressRoute 게이트웨이는 온-프레미스 인프라에 연결하기 위함이 아닙니다.

### <a name="point-to-site-vpn"></a>지점 및 사이트 간 VPN

지점 및 사이트 간 가상 사설망은 단일 엔드포인트 온-프레미스에 보안 연결할 수 있는 가장 간단한 형태입니다. 이 VPN은 Azure 기반 전용 HSM에 대한 단일 관리 워크스테이션만 보유하려는 경우 관련이 있을 수 있습니다.

### <a name="site-to-site-vpn"></a>사이트 간 VPN

사이트 간 가상 사설망을 사용하면 Azure 기반 전용 HSM 및 온-프레미스 IT 간 보안 통신이 가능합니다. 이 작업을 수행 하는 이유는 HSM의 온-프레미스 및 백업을 실행 하는 경우 둘 간의 연결을 필요로 하는 것에 대 한 백업 기능을 하는 것입니다.

## <a name="connecting-virtual-networks"></a>가상 네트워크 연결

전용 HSM에 대한 일반적인 배포 아키텍처는 단일 가상 네트워크 및 HSM 디바이스를 생성하고 프로비전하는 해당 서브넷에서 시작합니다. 동일한 지역 내에 추가 가상 네트워크 및 전용 HSM를 활용할 수 있는 애플리케이션 구성 요소에 대한 서브넷이 있을 수 있습니다. 이러한 네트워크에서 통신을 사용하도록 설정하려면 Virtual Network 피어링을 사용합니다.

### <a name="virtual-network-peering"></a>가상 네트워크 피어링

서로 다른 사용자의 리소스에 액세스해야 하는 지역 내에 여러 가상 네트워크가 있는 경우 가상 네트워크 간 보안 통신 채널을 만드는 데 Virtual Network 피어링을 사용할 수 있습니다.  가상 네트워크 피어링은 보안 통신을 제공할 뿐 아니라 Azure의 리소스 간 짧은 대기 시간 및 높은 대역폭 연결을 보장합니다.

![네트워크 피어링](media/networking/peering.png)

## <a name="connecting-across-azure-regions"></a>Azure 지역 간 연결

HSM 디바이스에는 소프트웨어 라이브러리를 통해 대체 HSM에 트래픽을 리디렉션할 수 있는 기능이 있습니다. 트래픽 리디렉션은 디바이스에 오류가 발생하거나 디바이스에 대한 액세스 권한을 손실하는 경우 유용합니다. 다른 지역에서 HSM을 배포하고 지역에 걸쳐 가상 네트워크 간 통신을 사용하도록 설정하여 지역 수준 오류 시나리오를 완화할 수 있습니다.

### <a name="cross-region-ha-using-vpn-gateway"></a>VPN 게이트웨이를 사용한 지역 간 HA

글로벌 분산형 애플리케이션 또는 고가용성 지역별 장애 조치(failover) 시나리오의 경우 지역에 걸쳐 가상 네트워크를 연결해야 합니다. Azure Dedicated HSM을 사용하면 두 가상 네트워크 간 보안 터널을 제공하는 VPN Gateway를 통해 고가용성을 구현할 수 있습니다. VPN Gateway를 사용한 Vnet 간 연결에 대한 자세한 내용 [VPN Gateway란?](../vpn-gateway/vpn-gateway-about-vpngateways.md#V2V) 문서를 참조하세요.

> [!NOTE]
> 글로벌 Vnet 피어링은 전용 HSM을 사용한 지역 간 연결 시나리오에서 사용할 수 없습니다. 대신 VPN 게이트웨이를 사용해야 합니다. 

![global-vnet](media/networking/global-vnet.png)

## <a name="next-steps"></a>다음 단계

- [질문과 대답](faq.md)
- [지원 가능성](supportability.md)
- [고가용성](high-availability.md)
- [물리적 보안](physical-security.md)
- [모니터링](monitoring.md)
- [배포 아키텍처](deployment-architecture.md)