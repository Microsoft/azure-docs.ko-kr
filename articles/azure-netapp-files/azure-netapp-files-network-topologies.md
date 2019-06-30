---
title: Azure NetApp 파일에 대 한 지침 계획 네트워크 | Microsoft Docs
description: Azure NetApp 파일을 사용 하 여 유효 네트워크 아키텍처를 설계 하는 데 도움이 되는 지침을 설명 합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: b-juche
ms.openlocfilehash: 207fb003eb1fdaafe4f43f7cd41dd4b7662eddf9
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331971"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>Azure NetApp Files 네트워크 계획 지침

네트워크 아키텍처 계획은 모든 응용 프로그램 인프라 디자인의 핵심 요소입니다. 이 문서에서는 Azure NetApp 파일의 다양 한 기능을 활용 하려면 워크 로드에 대 한 유효 네트워크 아키텍처를 디자인할 수 있습니다.

Azure NetApp 파일 볼륨 이라는 특수 한 용도의 서브넷에 포함 되어야 하도록 설계 되었습니다 [서브넷을 위임](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) Azure Virtual Network 내에서. 따라서 필요에 따라 가상 네트워크 게이트웨이 (ExpressRoute 또는 VPN Gateway) 볼륨 VNet에서 직접, 동일한 지역에서 피어 링 된 Vnet 또는 온-프레미스에서 액세스할 수 있습니다. Azure NetApp 파일에 전용 서브넷 및 다른 Azure 서비스 또는 인터넷에 대 한 연결이 없습니다.

## <a name="considerations"></a>고려 사항  

NetApp 파일 Azure 네트워크에 대 한 계획 하는 경우에 몇 가지 고려 사항을 이해 해야 합니다.

### <a name="constraints"></a>제약 조건

아래 기능은 Azure NetApp 파일에 대 한 현재 지원 되지 않습니다. 

* 서브넷에 네트워크 보안 그룹 (Nsg)
* 다음 홉 Azure NetApp 파일 서브넷을 사용 하 여 사용자 정의 경로 (Udr)
* Azure NetApp 파일 인터페이스에서 azure 정책 (예: 사용자 지정 명명 정책)
* Azure NetApp 파일 트래픽에 대 한 부하 분산 장치

Azure NetApp 파일에 다음과 같은 네트워크 제한 사항이 적용 됩니다.

* VNet 피어 링 된 Vnet 등 Azure NetApp 파일에서 사용 하 여 Ip 수가 1000을 초과할 수 없습니다.
* 각 Azure Virtual Network(VNet)에서 하나의 서브넷만 Azure NetApp Files에 위임할 수 있습니다.


### <a name="supported-network-topologies"></a>지원 되는 네트워크 토폴로지

다음 표에서 Azure NetApp Files에서 지원 되는 네트워크 토폴로지를 설명 합니다.  지원 되지 않는 토폴로지에 대 한 해결 방법을 설명합니다. 

|    토폴로지    |    지원    |     해결 방법    |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------|
|    볼륨을 로컬 VNet에 연결    |    예    |         |
|    볼륨 (동일한 지역) 피어 링된 된 VNet에 연결    |    예    |         |
|    볼륨 (지역 또는 전역 피어 링) 간 피어 링된 된 VNet에 연결    |    아닙니다.    |    없음    |
|    ExpressRoute 게이트웨이 통해 볼륨에 대 한 연결    |    예    |         |
|    ExpressRoute 게이트웨이 및 VNet 게이트웨이 전송을 사용 하 여 피어 링을 통해 스포크 VNet에에서 있는 볼륨에서 온-프레미스 연결    |    아닙니다.    |    허브 VNet (게이트웨이 사용 하 여 Azure VNet)에 위임 된 서브넷 만들기    |
|    VPN 게이트웨이 통해 스포크 VNet에에서 있는 볼륨에서 온-프레미스 연결    |    예    |         |
|    VPN gateway와 VNet 게이트웨이 전송을 사용 하 여 피어 링을 통해 스포크 VNet에에서 있는 볼륨에서 온-프레미스 연결    |    예    |         |


## <a name="virtual-network-for-azure-netapp-files-volumes"></a>NetApp Azure Files 볼륨에 대 한 가상 네트워크

이 섹션에서는 가상 네트워크 계획에 도움이 되는 개념을 설명 합니다.

### <a name="azure-virtual-networks"></a>Azure 가상 네트워크

NetApp Azure Files 볼륨을 프로 비전 하기 전에 Azure virtual network (VNet)를 만들거나 구독에 이미 존재 하는 하나를 사용 해야 합니다. 볼륨의 네트워크 경계를 정의 하는 VNet입니다.  가상 네트워크를 만드는 방법에 대 한 자세한 내용은 참조는 [Azure Virtual Network 설명서](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)합니다.

### <a name="subnets"></a>서브넷

서브넷에 Azure 리소스에서 사용할 수 있는 별도 주소 공간에 가상 네트워크를 분할 합니다.  이라는 특수 한 용도의 서브넷에 포함 되어 azure NetApp 파일 볼륨 [서브넷을 위임](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)합니다. 

서브넷 위임에는 서브넷에서 서비스 관련 리소스를 만드는 Azure NetApp 파일 서비스에 대 한 명시적 권한을 제공 합니다.  서비스 배포의 고유 식별자를 사용 합니다. 이 경우 네트워크 인터페이스를 Azure NetApp 파일에 대 한 연결을 사용 하도록 만들어집니다.

새 VNet을 사용 하는 경우 서브넷 만들기를 Azure NetApp 파일 지침에 따라 서브넷에 위임할 [NetApp Azure Files로 서브넷 대리자](azure-netapp-files-delegate-subnet.md)합니다. 또한 다른 서비스에 아직 위임 되지 않음 기존의 빈 서브넷을 위임할 수 있습니다.

VNet은 다른 VNet과 피어 링 하는 경우 VNet 주소 공간을 확장할 수 없습니다. 따라서 새 위임 된 서브넷의 VNet 주소 공간 내에 만들어야 해야 합니다. 주소 공간을 확장 해야 할 경우 VNet 주소 공간을 확장 하기 전에 피어 링을 삭제 해야 합니다.

### <a name="udrs-and-nsgs"></a>Udr 및 Nsg

사용자 정의 경로 (Udr) 및 네트워크 보안 그룹 (Nsg) Azure NetApp 파일에 대 한 위임 된 서브넷에 지원 되지 않습니다.

해결 방법으로 허용 하거나 위임 하는 Azure NetApp 파일 서브넷 간의 트래픽을 거부 하는 다른 서브넷에 Nsg를 적용할 수 있습니다.  

## <a name="azure-native-environments"></a>Azure 네이티브 환경

다음 다이어그램에서는 Azure 네이티브 환경을 보여 줍니다.

![Azure 네이티브 네트워킹 환경](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>로컬 VNet

만들거나 동일한 VNet의 가상 컴퓨터 (VM)에서 Azure NetApp Files 볼륨에 연결 하는 기본 시나리오가입니다. 위의 다이어그램에서 VNet 2에 대 한 볼륨 1 위임 된 서브넷에서 생성 되 고 기본 서브넷에 VM 1에서 탑재할 수 있습니다.

### <a name="vnet-peering"></a>VNet 피어링

사용 하 여 Vnet을 연결할 수 있습니다 다른 사용자의 리소스에 액세스 해야 하는 동일한 지역에 추가 Vnet에 있는 경우 [VNet 피어 링](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) Azure 인프라를 통해 보안 연결을 사용 하도록 설정 합니다. 

위의 다이어그램에서 VNet 2 및 VNet 3을 고려 합니다. VM 1 VM 2 및 볼륨 2에 연결 해야 하는 경우 또는 VM 2 VM 1 또는 볼륨 1에 연결 해야 하는 경우 해야 VNet 2 및 3 VNet 간에 VNet 피어 링을 사용 하도록 설정 합니다. 

또한 VNet 1은 VNet 2를 사용 하 여 피어 링 및 VNet 2 동일한 지역에 3 VNet 피어 링 되는 시나리오를 고려 합니다. VNet 1 리소스 vnet2에는 리소스에 연결할 수 있지만 않으면 VNet 1 및 3 VNet 피어 링 된 VNet 3의 리소스에 연결할 수 없습니다. 

위의 다이어그램에서 VM 3 볼륨 1에 연결할 수 있지만 VM 4가 볼륨 2에 연결할 수 없습니다.  이 원인은 스포크 Vnet 피어 링 되지 된 경우 및 _VNet 피어 링을 통한 전송 라우팅이 지원 되지 않습니다_합니다.

## <a name="hybrid-environments"></a>하이브리드 환경

다음 다이어그램에서는 하이브리드 환경을 보여 줍니다. 

![하이브리드 네트워킹 환경](../media/azure-netapp-files/azure-netapp-files-network-hybrid-environment.png)

하이브리드 시나리오에서 온-프레미스 데이터 센터에서 응용 프로그램에는 Azure의 리소스에 액세스를 해야합니다.  이 경우에 데이터 센터를 Azure로 확장 하려는 네이티브 Azure 서비스를 사용 하려면이 든 또는 재해 복구에 대 한 합니다. 참조 [VPN Gateway 계획 옵션](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable) 사이트 간 VPN 또는 ExpressRoute를 통해 Azure 리소스에 여러 온-프레미스 리소스를 연결 하는 방법에 대 한 합니다.

하이브리드 허브-스포크 토폴로지에서 허브 VNet에서 Azure에 온-프레미스 네트워크에 대 한 연결의 중심으로 작동합니다. 스포크는 허브와 피어 링 Vnet 및 워크 로드 격리를 사용할 수 있습니다.

구성에 따라 합니다. 허브 및 스포크에서 리소스를 온-프레미스에서 리소스를 연결할 수 있습니다.

위에서 설명한 토폴로지를에서 온-프레미스 네트워크는 허브 VNet에서 Azure에 연결 되며 허브 VNet 사용 하 여 2 개의 스포크는 동일한 지역에 Vnet 피어 링 합니다.  이 시나리오에서는 Azure NetApp 파일 볼륨에 대 한 지원 되는 연결 옵션은 다음과 같습니다.

* VM 1 및 2 VM 온-프레미스 리소스는 사이트 간 VPN 또는 Expressroute를 통해 허브의 볼륨 1에 연결할 수 있습니다. 
* VM 1 및 2 VM 온-프레미스 리소스는 사이트 간 VPN 및 지역 Vnet 피어 링을 통해 볼륨 2 또는 3 권에 연결할 수 있습니다.
* VM 3 허브의 VNet 스포크 2 VNet에서에서 스포크 VNet 1에서에서 2 볼륨 및 볼륨 3 연결할 수 있습니다.
* 스포크 1 VNet에서에서 VM 4 및 스포크 2 VNet에서에서 VM 5는 허브 VNet의에서 볼륨 1에 연결할 수 있습니다.

스포크 1 VNet에서에서 VM 4 볼륨 3 스포크 2 VNet에에서 연결할 수 없습니다. 또한 스포크에서 VM 5 VNet2에 연결할 수 없습니다 볼륨 2 스포크 VNet 1에서. 스포크 Vnet 피어 링 하지는 때문에 경우 및 _VNet 피어 링을 통한 전송 라우팅이 지원 되지 않습니다_합니다.

## <a name="next-steps"></a>다음 단계

[Azure NetApp Files에 서브넷 위임](azure-netapp-files-delegate-subnet.md)
