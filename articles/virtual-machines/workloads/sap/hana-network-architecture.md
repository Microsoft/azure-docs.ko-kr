---
title: Azure(대규모 인스턴스)에서 SAP HANA 네트워크 아키텍처 | Microsoft Docs
description: Azure(대규모 인스턴스)에서 SAP HANA를 배포하는 방법에 대한 네트워크 아키텍처입니다.
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
ms.date: 09/04/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 724a91b6ba0be030a2281bce366e4378892df59b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60835794"
---
# <a name="sap-hana-large-instances-network-architecture"></a>SAP HANA(대규모 인스턴스) 네트워크 아키텍처

Azure 네트워크 서비스의 아키텍처는 SAP 애플리케이션을 HANA 대규모 인스턴스에 성공적으로 배포하는 핵심 구성 요소입니다. 일반적으로 Azure(큰 인스턴스)에서 SAP HANA 배포에는 다양한 데이터베이스 크기, CPU 리소스 사용량 및 메모리 사용률이 있는 서로 다른 여러 SAP 솔루션을 갖춘 더 큰 SAP 환경이 포함됩니다. 이러한 모든 SAP 시스템이 SAP HANA를 기반으로 하는 것은 아닙니다. SAP 자산은 아마도 다음을 사용하는 하이브리드일 수 있습니다.

- 온-프레미스에 배포된 SAP 시스템 이러한 시스템은 해당 크기로 인해 현재 Azure에서 호스팅할 수 없습니다. 예를 들어 SQL Server에서 데이터베이스로 실행되고 VM이 제공할 수 있는 것보다 더 많은 CPU 또는 메모리 리소스가 필요한 프로덕션 SAP ERP 시스템이 있습니다.
- 온-프레미스에 배포된 SAP HANA 기반 SAP 시스템.
- VM에 배포된 SAP 시스템. 이러한 시스템은 리소스 사용량 및 메모리 요구량에 따라 Azure에서 VM에 성공적으로 배포할 수 있는 SAP NetWeaver 기반 애플리케이션에 대한 배포, 테스트, 샌드박스 또는 프로덕션 인스턴스가 될 수 있습니다. 또한 SQL Server와 같은 데이터베이스를 기반으로 할 수도 있습니다. 자세한 내용은 참조 하세요. [SAP Support Note #1928533 – Azure의 SAP 응용 프로그램: 지원 되는 제품 및 Azure VM 유형](https://launchpad.support.sap.com/#/notes/1928533/E)합니다. 그리고 이러한 시스템은 SAP HANA와 같은 데이터베이스를 기반으로 할 수 있습니다. 자세한 내용은 [SAP HANA 인증 IaaS 플랫폼](https://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html)을 참조하세요.
- Azure 대규모 인스턴스 스탬프에서 SAP HANA on Azure(대규모 인스턴스)를 활용하는 Azure에서 VM에 배포된 SAP 애플리케이션 서버.

네 가지 이상의 배포 시나리오가 있는 하이브리드 SAP 자산이 일반적입니다. Azure에서 실행되는 완전한 SAP 자산에 대한 고객 사례도 많이 있습니다. VM이 더욱 강력해짐에 따라 Azure에서 자신의 모든 SAP 솔루션을 이동하는 고객의 수가 늘고 있습니다.

Azure에 배포된 SAP 시스템의 컨텍스트에서 Azure 네트워킹은 복잡하지 않습니다. 기준이 되는 원칙은 다음과 같습니다.

- Azure 가상 네트워크는 온-프레미스 네트워크에 연결되는 ExpressRoute 회로에 연결되어야 합니다.
- 온-프레미스를 Azure에 연결하는 ExpressRoute 회로에는 대개 1Gbps 이상의 대역폭이 있어야 합니다. 이 최소 대역폭을 통해 온-프레미스 시스템과 VM에서 실행되는 시스템 간의 데이터 전송에 적합한 대역폭이 허용됩니다. 또한 온-프레미스 사용자의 Azure 시스템에 연결할 수 있는 적절한 대역폭도 허용됩니다.
- Azure에서 모든 SAP 시스템이 서로 통신할 수 있도록 Azure 가상 네트워크에 설정되어야 합니다.
- 온-프레미스에서 호스팅되는 Active Directory 및 DNS는 ExpressRoute를 통해 온-프레미스에서 Azure로 확장됩니다.


> [!NOTE] 
> 요금 청구 관점에서 볼 때, 하나의 Azure 구독은 특정 Azure 지역의 대규모 인스턴스 스탬프에 있는 하나의 테넌트에만 연결할 수 있습니다. 반대로, 하나의 대규모 인스턴스 스탬프 테넌트는 하나의 Azure 구독에만 연결할 수 있습니다. 이 요구 사항은 Azure에서 청구 가능한 다른 개체와 일치합니다.

서로 다른 여러 Azure 지역에 SAP HANA on Azure(대규모 인스턴스)를 배포하면 별도의 테넌트가 대규모 인스턴스 스탬프에 배포됩니다. 이러한 인스턴스가 동일한 SAP 자산에 포함되면 둘 다 동일한 Azure 구독에서 실행할 수 있습니다. 

> [!IMPORTANT] 
> SAP HANA on Azure(대규모 인스턴스)에서는 Azure Resource Manager 배포만 지원됩니다.

 

## <a name="additional-virtual-network-information"></a>추가 가상 네트워크 정보

가상 네트워크를 ExpressRoute에 연결하려면 Azure 게이트웨이를 만들어야 합니다. 자세한 내용은 [ExpressRoute에 대한 가상 네트워크 게이트웨이 정보](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요. 

Azure 게이트웨이는 Azure 외부의 인프라 또는 Azure 대규모 인스턴스 스탬프에 ExpressRoute와 함께 사용할 수 있습니다. 또한 Azure 게이트웨이를 사용하여 가상 네트워크 간에 연결할 수도 있습니다. 자세한 내용은 [PowerShell을 사용하여 Resource Manager에 대한 네트워크 간 연결 구성](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요. 이러한 연결이 서로 다른 Microsoft 엔터프라이즈 에지 라우터에서 오는 경우 Azure 게이트웨이를 최대 4개의 서로 다른 ExpressRoute 연결에 연결할 수 있습니다. 자세한 내용은 [SAP HANA on Azure(대규모 인스턴스) 인프라 및 연결](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요. 

> [!NOTE] 
> 두 사용 사례에 대해 Azure 게이트웨이에서 제공하는 처리량은 차이가 있습니다. 자세한 내용은 [VPN Gateway 정보](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요. 가상 네트워크 게이트웨이로 달성할 수 있는 최대 처리량은 ExpressRoute 연결을 사용할 경우 10Gbps입니다. 가상 네트워크에 있는 VM과 온-프레미스 시스템 간에 파일을 복사하는 경우(단일 복사 스트림으로), 다양한 게이트웨이 SKU의 전체 처리량을 달성할 수 없습니다. 가상 네트워크 게이트웨이의 전체 대역폭을 활용하려면 다중 스트림을 사용합니다. 또는 단일 파일의 병렬 스트림에서 다른 파일을 복사해야 합니다.


## <a name="networking-architecture-for-hana-large-instance"></a>HANA 대규모 인스턴스에 대한 네트워킹 아키텍처
HANA 대규모 인스턴스에 대한 네트워킹 아키텍처는 다음 네 가지 부분으로 구분할 수 있습니다.

- Azure에 대한 온-프레미스 네트워킹 및 ExpressRoute 연결. 이 부분은 고객의 도메인이며 ExpressRoute 통해 Azure에 연결됩니다. 다음 그림의 오른쪽 아래 부분을 참조하세요.
- Azure 네트워크 서비스(앞에서 설명한 대로 게이트웨이가 있는 가상 네트워크 포함). 이 부분은 애플리케이션 요구 사항, 보안 및 규정 준수 요구 사항에 적합한 디자인을 찾아야 하는 영역입니다. HANA 대규모 인스턴스를 사용하는지 여부는 선택할 가상 네트워크와 Azure 게이트웨이 SKU의 수 측면에서 고려해야 할 또 다른 요소입니다. 그림의 오른쪽 위 부분을 참조하세요.
- Azure에 ExpressRoute 기술을 통해 HANA 대규모 인스턴스 연결. 이 부분은 Microsoft에서 배포하고 처리합니다. HANA 대규모 인스턴스에 자산을 배포한 후 ExpressRoute 회로를 가상 네트워크에 연결하기 위한 일부 IP 주소 범위를 제공하기만 하면 됩니다. 자세한 내용은 [SAP HANA on Azure(대규모 인스턴스) 인프라 및 연결](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요. 
- 대부분 투명한 HANA 대규모 인스턴스의 네트워킹

![SAP HANA on Azure(대규모 인스턴스) 및 온-프레미스에 연결된 가상 네트워크](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

HANA 대규모 인스턴스를 사용하기 때문에 온-프레미스 자산에 있는 사용자가 ExpressRoute를 통해 Azure에 연결해야 한다는 요구 사항은 변경되지 않습니다. HANA 대규모 인스턴스 장치에서 호스팅되는 HANA 인스턴스에 연결하는 애플리케이션 계층을 호스팅하는 VM을 실행하는 하나 이상의 가상 네트워크가 있어야 한다는 요구 사항도 변경되지 않습니다. 

Azure에서 SAP 배포의 차이점은 다음과 같습니다.

- 고객 테넌트의 HANA 대규모 인스턴스 장치는 또 다른 ExpressRoute 회로 통해 가상 네트워크에 연결됩니다. 로드 조건을 분리하기 위해 온-프레미스와 가상 네트워크 ExpressRoute 간의 링크 및 가상 네트워크와 HANA 대규모 인스턴스 간의 링크는 동일한 라우터를 공유하지 않습니다.
- SAP 애플리케이션 계층과 HANA 대규모 인스턴스 간의 워크로드 프로필은 SAP HANA에서 애플리케이션 계층으로의 데이터 전송(결과 집합)과 같이 많은 작은 요청과 버스트와는 다른 특성을 가지고 있습니다.
- SAP 애플리케이션 아키텍처는 온-프레미스와 Azure 간에 데이터가 교환되는 일반적인 시나리오보다 네트워크 대기 시간에 더 민감합니다.
- 가상 네트워크 게이트웨이에는 둘 이상의 ExpressRoute 연결이 있습니다. 두 연결은 모두 가상 네트워크 게이트웨이의 들어오는 데이터에 대한 최대 대역폭을 공유합니다.

VM과 HANA 대규모 인스턴스 장치 간에 발생하는 네트워크 대기 시간은 일반적인 VM 간 네트워크 왕복 대기 시간보다 길 수 있습니다. Azure 지역에 따라 측정 된 값을 초과할 수에서 평균 미달로 분류 된 0.7ms 왕복 대기 시간 [SAP Note #1100926-FAQ: 네트워크 성능](https://launchpad.support.sap.com/#/notes/1100926/E)합니다. Azure VM 및 HANA 대규모 인스턴스 단위 간의 네트워크 왕복 대기 시간을 측정하는 Azure 지역 및 도구에 따라 측정된 대기 시간은 최대 약 2밀리초일 수 있습니다. 그럼에도 불구하고 고객은 SAP HANA 기반 프로덕션 SAP 애플리케이션을 SAP HANA 대규모 인스턴스에 성공적으로 배포합니다. Azure HANA 대규모 인스턴스에서 자신의 비즈니스 프로세스를 철저하게 테스트해야 합니다.
 
VM과 HANA 대규모 인스턴스 간에 결정적인 네트워크 대기 시간을 제공하려면 가상 네트워크 게이트웨이 SKU를 선택해야 합니다. 온-프레미스와 VM 간의 트래픽 패턴과는 달리, VM과 HANA 대규모 인스턴스 간의 트래픽 패턴은 작지만 높은 버스트의 요청 및 데이터 볼륨을 전송하도록 개발할 수 있습니다. 이러한 버스트를 잘 처리하려면 UltraPerformance 게이트웨이 SKU를 사용하는 것이 좋습니다. HANA 대규모 인스턴스 유형 II 클래스 SKU의 경우, 반드시 UltraPerformance 게이트웨이 SKU를 가상 네트워크 게이트웨이로 사용해야 합니다.

> [!IMPORTANT] 
> SAP 애플리케이션과 데이터베이스 계층 간의 전체 네트워크 트래픽을 고려할 때, SAP HANA on Azure(대규모 인스턴스) 연결에는 가상 네트워크용 HighPerformance 또는 UltraPerformance 게이트웨이 SKU만 지원됩니다. HANA 대규모 인스턴스 유형 II 클래스 SKU의 경우, UltraPerformance 게이트웨이 SKU만 가상 네트워크 게이트웨이로 지원됩니다.



## <a name="single-sap-system"></a>단일 SAP 시스템

앞에서 보여 준 온-프레미스 인프라는 ExpressRoute를 통해 Azure에 연결됩니다. ExpressRoute 회로는 엔터프라이즈 에지 라우터에 연결됩니다. 자세한 내용은 [ExpressRoute 기술 개요](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요. 경로가 설정되면 해당 경로가 Azure 백본에 연결되고 모든 Azure 지역에 액세스할 수 있습니다.

> [!NOTE] 
> Azure에서 SAP 자산을 실행하려면 SAP 자산에서 Azure 지역과 가장 가까운 엔터프라이즈 에지 라우터에 연결합니다. Azure 대규모 인스턴스 스탬프는 Azure IaaS 및 대규모 인스턴스 스탬프에서 VM 간의 네트워크 대기 시간을 최소화하기 위해 전용 엔터프라이즈 에지 라우터 디바이스를 통해 연결됩니다.

SAP 애플리케이션 인스턴스를 호스팅하는 VM에 대한 가상 네트워크 게이트웨이는 ExpressRoute 회로에 연결됩니다. 동일한 가상 네트워크는 대규모 인스턴스 스탬프에 전용으로 연결하기 위해 별도의 엔터프라이즈 에지 라우터에 연결됩니다.

이 시스템은 단일 SAP 시스템의 간단한 예입니다. SAP 애플리케이션 계층은 Azure에서 호스팅됩니다. SAP HANA 데이터베이스는 SAP HANA on Azure(대규모 인스턴스)에서 실행됩니다. 2Gbps 또는 10Gbps 처리량의 가상 네트워크 게이트웨이 대역폭에서 병목 현상이 나타나지 않는다고 가정합니다.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>여러 SAP 시스템 또는 큰 SAP 시스템

SAP HANA on Azure(대규모 인스턴스)에 연결하기 위해 여러 SAP 시스템 또는 대형 SAP 시스템을 배포하는 경우, 가상 네트워크 게이트웨이의 처리량으로 인해 병목 현상이 발생할 수 있습니다. 이 경우 애플리케이션 계층을 여러 가상 네트워크로 분할합니다. 또한 다음과 같은 경우 HANA 대규모 인스턴스에 연결하는 특수 가상 네트워크를 만들 수도 있습니다.

- HANA 대규모 인스턴스의 HANA 인스턴스에서 NFS 공유를 호스팅하는 Azure의 VM으로 백업을 직접 수행합니다.
- 큰 백업 또는 HANA 큰 인스턴스 단위의 기타 파일을 Azure에서 관리하는 디스크 공간으로 복사합니다.

별도의 가상 네트워크를 사용하여 저장소를 관리하는 VM을 호스팅합니다. 이렇게 하면 SAP 애플리케이션 계층을 실행하는 VM을 제공하는 가상 네트워크 게이트웨이에서 큰 파일이나 데이터를 HANA 대규모 인스턴스로부터 Azure에 전송하지 못하도록 방지할 수 있습니다. 

확장성 있는 네트워크 아키텍처를 위해서는 다음과 같이 합니다.

- 더 큰 단일 SAP 애플리케이션 계층에 대해 여러 가상 네트워크를 활용합니다.
- 동일한 가상 네트워크 하에서 별도의 서브넷에 있는 이러한 SAP 시스템을 결합하는 것과 비교하여 배포된 각 SAP 시스템에 대해 별도의 가상 네트워크를 하나씩 배포합니다.

  Azure(큰 인스턴스)에서 SAP HANA에 대한 보다 확장성 있는 네트워킹 아키텍처

![여러 가상 네트워크를 통해 SAP 애플리케이션 계층 배포](./media/hana-overview-architecture/image4-networking-architecture.png)

이 그림에서는 여러 가상 네트워크를 통해 배포된 SAP 애플리케이션 계층 또는 구성 요소를 보여 줍니다. 이 구성은 이러한 가상 네트워크에서 호스팅되는 애플리케이션 간의 통신 중에 발생한 피할 수 없는 대기 시간 오버헤드를 유발했습니다. 기본적으로 서로 다른 가상 네트워크에 있는 VM 간의 네트워크 트래픽은 이 구성에서 엔터프라이즈 에지 라우터를 통해 라우팅됩니다. 두 가상 네트워크 간의 통신 대기 시간을 최적화하고 줄이려면 동일한 지역 내에서 가상 네트워크를 피어링합니다. 이 방법은 해당 가상 네트워크가 다른 구독에 있는 경우에도 작동합니다. 가상 네트워크 피어링을 사용하면 서로 다른 두 가상 네트워크의 VM 간 통신에서 Azure 네트워크 백본을 통해 서로 직접 통신할 수 있습니다. 대기 시간은 VM이 동일한 가상 네트워크에 있는 것처럼 표시됩니다. Azure 가상 네트워크 게이트웨이를 통해 연결된 IP 주소 범위를 처리하는 트래픽은 가상 네트워크의 개별 가상 네트워크 게이트웨이를 통해 라우팅됩니다. 

가상 네트워크 피어링에 대한 자세한 내용은 [가상 네트워크 피어링](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)을 참조하세요.


## <a name="routing-in-azure"></a>Azure에서 라우팅

SAP HANA on Azure(대규모 인스턴스)의 네트워크 라우팅에 대해 고려해야 할 세 가지 중요 사항은 다음과 같습니다.

* SAP HANA on Azure(대규모 인스턴스)는 VM 및 전용 ExpressRoute 연결을 통해서만 액세스할 수 있으며, 온-프레미스에서 직접 액세스 할 수 없습니다. Microsoft에서 제공하는 것처럼 온-프레미스에서 HANA 대규모 인스턴스 장치에 직접적으로 즉시 액세스할 수 없습니다. 전이적 라우팅 제한은 SAP HANA 대규모 인스턴스에 사용되는 현재 Azure 네트워크 아키텍처로 인해 발생합니다. 온-프레미스에서 실행되는 SAP 솔루션 관리자와 같이 직접 액세스가 필요한 관리 클라이언트 및 애플리케이션 일부는 SAP HANA 데이터베이스에 연결할 수 없습니다.

* 재해 복구를 위해 두 개의 다른 Azure 지역에 배포된 HANA 대규모 인스턴스 장치가 있는 경우 동일한 일시적 라우팅 제한이 적용됩니다. 즉, 한 지역(예: 미국 서부)에 있는 HANA 대규모 인스턴스 장치의 IP 주소는 다른 지역(예: 미국 동부)에 배포된 HANA 대규모 인스턴스 장치로 라우팅되지 않습니다. 이 제한은 지역 간에 또는 HANA 대규모 인스턴스 장치를 가상 네트워크에 연결하는 ExpressRoute 회로 연결 간의 Azure 네트워크 피어링을 사용하는 것과는 독립적입니다. 그래픽 표현은 "여러 지역에서 HANA 대규모 인스턴스 장치 사용" 섹션의 그림을 참조하세요. 배포된 아키텍처와 함께 제공되는 이 제한은 HANA 시스템 복제를 재해 복구 기능으로 즉시 사용할 수 없도록 금지합니다.

* SAP HANA on Azure(대규모 인스턴스) 장치에는 사용자가 제출한 서버 IP 풀 주소 범위에서 할당된 IP 주소가 있습니다. 자세한 내용은 [SAP HANA on Azure(대규모 인스턴스) 인프라 및 연결](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요. 이 IP 주소는 가상 네트워크를 HANA on Azure(대규모 인스턴스)에 연결하는 Azure 구독 및 ExpressRoute를 통해 액세스할 수 있습니다. 해당 서버 IP 풀 주소 범위 이외의 할당된 IP 주소는 하드웨어 장치에 직접 할당됩니다. 이 솔루션을 처음 배포하는 경우와 같이 더 이상 NAT를 통해 *할당되지 않습니다*. 

> [!NOTE]
> 처음 두 가지 목록 항목에서 설명한 대로 일시적인 라우팅에 대한 제한을 해결하려면 라우팅에 추가 구성 요소를 사용합니다. 제한을 해결하는 데 사용할 수 있는 구성 요소는 다음과 같습니다.
> 
> * 데이터를 라우팅하는 역방향 프록시를 사용합니다. 예를 들어 F5 BIG-IP, NGINX(Traffic Manager 포함)가 가상 방화벽/트래픽 라우팅 솔루션으로 Azure에 배포되어 있습니다.
> * Linux VM에서 [IPTables 규칙](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ)을 사용하여 온-프레미스 위치와 HANA 대규모 인스턴스 장치 간 또는 다른 지역의 HANA 대규모 인스턴스 장치 간에 라우팅을 사용하도록 설정합니다.
> 
> Microsoft에서는 타사 네트워크 어플라이언스 또는 IPTables와 관련된 사용자 지정 솔루션에 대한 구현 및 지원을 제공하지 않습니다. 지원은 사용되는 구성 요소의 공급업체 또는 통합자에서 제공해야 합니다. 

## <a name="internet-connectivity-of-hana-large-instance"></a>HANA 대규모 인스턴스의 인터넷 연결
HANA 대규모 인스턴스는 인터넷에 직접 *연결되지 않습니다*. 예를 들어 이 제한으로 인해 OS 공급업체에 OS 이미지를 직접 등록하는 기능이 제한될 수 있습니다. 로컬 SUSE Linux Enterprise Server 구독 관리 도구 서버 또는 Red Hat Enterprise Linux 구독 관리자를 사용해야 할 수도 있습니다.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>VM 및 HANA 대규모 인스턴스 간 데이터 암호화
HANA 대규모 인스턴스 및 VM 간에 전송되는 데이터는 암호화되지 않습니다. 그러나 HANA DBMS 쪽과 JDBC/ODBC 기반 애플리케이션 간의 교환을 위해서만 트래픽 암호화를 사용할 수 있습니다. 자세한 내용은 [이 SAP 설명서](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false)를 참조하세요.

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>여러 지역에서 HANA 대규모 인스턴스 장치 사용

재해 복구 용도 외에도, 여러 Azure 지역에 SAP HANA on Azure(대규모 인스턴스)를 배포해야 하는 이유가 있을 수 있습니다. 지역의 다른 가상 네트워크에 배포된 각 VM에서 HANA 대규모 인스턴스에 액세스하려는 경우가 있습니다. 다른 HANA 대규모 인스턴스 장치에 할당된 IP 주소는 해당 게이트웨이를 통해 직접 인스턴스에 연결된 가상 네트워크를 넘어서 전파되지 않습니다. 따라서 가상 네트워크 디자인이 약간 변경되었습니다. 가상 네트워크 게이트웨이는 다른 엔터프라이즈 에지 라우터에서 서로 다른 4개의 ExpressRoute 회로를 처리할 수 있습니다. 대규모 인스턴스 스탬프 중 하나에 연결된 각 가상 네트워크는 다른 Azure 지역의 대규모 인스턴스 스탬프에 연결할 수 있습니다.

![다른 Azure 지역의 Azure 대규모 인스턴스 스탬프에 연결된 가상 네트워크](./media/hana-overview-architecture/image8-multiple-regions.png)

이 그림에서는 두 지역에 있는 서로 다른 가상 네트워크가 두 Azure 지역의 SAP HANA on Azure(대규모 인스턴스)에 연결하는 데 사용되는 서로 다른 두 개의 ExpressRoute 회로에 연결된 방식을 보여 줍니다. 새로 소개된 연결은 빨간색 사각형 선입니다. 가상 네트워크 중에서 이러한 연결을 사용하면 해당 가상 네트워크 중 하나에서 실행되는 VM이 두 지역에 배포된 서로 다른 각각의 HANA 대규모 인스턴스 장치에 액세스할 수 있습니다. 그림에서 보여 주듯이 온-프레미스에서 두 개의 Azure 지역에 연결하는 두 개의 ExpressRoute 연결이 있다고 가정합니다. 이러한 정렬은 재해 복구 용도를 위해 권장됩니다.

> [!IMPORTANT] 
> 여러 개의 ExpressRoute 회로를 사용한 경우 트래픽을 적절하게 라우팅할 수 있도록 선행 AS 경로 추가 및 로컬 기본 BGP 설정을 사용해야 합니다.

**다음 단계**
- [SAP HANA(대규모 인스턴스) 저장소 아키텍처](hana-storage-architecture.md) 참조