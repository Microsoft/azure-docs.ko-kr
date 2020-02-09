---
title: Azure NetApp Files 네트워크 계획에 대한 지침 | Microsoft Docs
description: Azure NetApp Files를 사용 하 여 효과적인 네트워크 아키텍처를 설계 하는 데 도움이 되는 지침을 설명 합니다.
services: azure-netapp-files
documentationcenter: ''
author: ram-kakani
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
ms.openlocfilehash: 8e6a1c3472c6b20b27cf181edbeeb96ab71eb58d
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242488"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>Azure NetApp Files 네트워크 계획 지침

네트워크 아키텍처 계획은 모든 응용 프로그램 인프라를 디자인 하는 핵심 요소입니다. 이 문서는 Azure NetApp Files의 다양 한 기능을 활용 하기 위해 워크 로드에 대한 효과적인 네트워크 아키텍처를 설계 하는 데 도움이 됩니다.

Azure NetApp Files 볼륨은 Azure Virtual Network 내에서 [위임 된 서브넷](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) 이라는 특수 용도의 서브넷에 포함 되도록 설계 되었습니다. 따라서 필요에 따라 Virtual Network 게이트웨이 (Express 경로 또는 VPN Gateway)를 통해 VNet에서 직접, 동일한 지역의 피어 링 Vnet에서 또는 온-프레미스에서 볼륨에 액세스할 수 있습니다. 서브넷은 Azure NetApp Files 전용 이며 다른 Azure 서비스 또는 인터넷에 대한 연결이 없습니다.

## <a name="considerations"></a>고려 사항  

Azure NetApp Files 네트워크를 계획 하는 경우 몇 가지 고려 사항을 이해 해야 합니다.

### <a name="constraints"></a>제약 조건

아래 기능은 현재 Azure NetApp Files에 대해 지원 되지 않습니다. 

* 위임 된 서브넷에 적용 된 NSGs (네트워크 보안 그룹)
* 주소 접두사가 Azure NetApp files 서브넷 인 UDRs (사용자 정의 경로)
* Azure NetApp Files 인터페이스의 Azure 정책 (예: 사용자 지정 명명 정책)
* Azure NetApp Files 트래픽에 대한 부하 분산 장치

Azure NetApp Files에는 다음과 같은 네트워크 제한이 적용 됩니다.

* Azure NetApp Files (피어 링 Vnet 포함)를 사용 하 여 VNet에서 사용 중인 Ip 수는 1000를 초과할 수 없습니다. 고객 규모 수요를 충족 하기 위해이 제한을 증가 시키기 위해 노력 하 고 있습니다. 중간에 더 많은 Ip를 요구 하는 경우 사용 사례 및 필요한 한도를 포함 하 여 지원 팀에 문의 하세요.
* 각 Azure Virtual Network(VNet)에서 하나의 서브넷만 Azure NetApp Files에 위임할 수 있습니다.


### <a name="supported-network-topologies"></a>지원 되는 네트워크 토폴로지

다음 표에서는 Azure NetApp Files에서 지 원하는 네트워크 토폴로지를 설명 합니다.  지원 되지 않는 토폴로지에 대한 해결 방법도 설명 합니다. 

|    토폴로지    |    지원 됨    |     해결 방법    |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------|
|    로컬 VNet의 볼륨에 연결    |    yes    |         |
|    피어 링 VNet (동일한 지역)의 볼륨에 연결    |    yes    |         |
|    피어 링 VNet (교차 지역 또는 전역 피어 링)의 볼륨에 연결    |    아닙니다.    |    없음    |
|    Express 경로 게이트웨이를 통해 볼륨에 연결    |    yes    |         |
|    Express 경로 게이트웨이 및 게이트웨이 전송으로 VNet 피어 링을 통해 스포크 VNet의 볼륨에서 온-프레미스로의 연결    |    yes    |        |
|    VPN gateway를 통해 온-프레미스에서 스포크 VNet의 볼륨으로의 연결    |    yes    |         |
|    온-프레미스에서 VPN gateway를 통해 스포크 VNet의 볼륨에 대한 연결 및 게이트웨이 전송으로 VNet 피어 링    |    yes    |         |


## <a name="virtual-network-for-azure-netapp-files-volumes"></a>Azure NetApp Files 볼륨에 대한 가상 네트워크

이 섹션에서는 가상 네트워크를 계획 하는 데 도움이 되는 개념을 설명 합니다.

### <a name="azure-virtual-networks"></a>Azure 가상 네트워크

Azure NetApp Files 볼륨을 프로 비전 하기 전에 Azure VNet (가상 네트워크)을 만들거나 구독에 이미 있는 VNet (가상 네트워크)을 사용 해야 합니다. VNet은 볼륨의 네트워크 경계를 정의 합니다.  가상 네트워크를 만드는 방법에 대한 자세한 내용은 [Azure Virtual Network 설명서](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)를 참조 하세요.

### <a name="subnets"></a>서브넷

서브넷은 가상 네트워크를 Azure 리소스에서 사용할 수 있는 별도의 주소 공간으로 분할 합니다.  Azure NetApp Files 볼륨은 [위임 된 서브넷](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)이라고 하는 특수 한 용도의 서브넷에 포함 되어 있습니다. 

서브넷 위임은 Azure NetApp Files 서비스에 대한 명시적 권한을 부여 하 여 서브넷에 서비스별 리소스를 만듭니다.  서비스를 배포 하는 데 고유한 식별자를 사용 합니다. 이 경우 Azure NetApp Files에 연결할 수 있도록 네트워크 인터페이스가 생성 됩니다.

새 VNet을 사용 하는 경우 서브넷을 [Azure NetApp Files에 위임](azure-netapp-files-delegate-subnet.md)의 지침에 따라 Azure NetApp Files 서브넷을 만들고 서브넷을 위임할 수 있습니다. 다른 서비스에 아직 위임 되지 않은 기존의 빈 서브넷을 위임할 수도 있습니다.

VNet이 다른 VNet과 피어 링 경우 VNet 주소 공간을 확장할 수 없습니다. 이러한 이유로 VNet 주소 공간 내에 새 위임 된 서브넷을 만들어야 합니다. 주소 공간을 확장 해야 하는 경우 주소 공간을 확장 하기 전에 VNet 피어 링을 삭제 해야 합니다.

### <a name="udrs-and-nsgs"></a>UDRs 및 NSGs

UDRs (사용자 정의 경로) 및 NSGs (네트워크 보안 그룹)는 Azure NetApp Files에 대한 위임 된 서브넷에서 지원 되지 않습니다.

이에 대한 해결 방법으로, 위임 된 서브넷 Azure NetApp Files에서 들어오고 나가는 트래픽을 허용 하거나 거부 하는 다른 서브넷에 NSGs를 적용할 수 있습니다.  

## <a name="azure-native-environments"></a>Azure 기본 환경

다음 다이어그램은 Azure 기본 환경을 보여 줍니다.

![Azure-기본 네트워킹 환경](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>로컬 VNet

기본 시나리오는 동일한 VNet의 VM (가상 머신)에서 Azure NetApp Files 볼륨을 만들거나 연결 하는 것입니다. 위의 다이어그램에서 VNet 2의 경우 볼륨 1은 위임 된 서브넷에서 만들어지고 기본 서브넷의 VM 1에 탑재 될 수 있습니다.

### <a name="vnet-peering"></a>VNet 피어링

서로 다른 리소스에 액세스 해야 하는 동일한 지역에 추가 Vnet이 있는 경우 [VNet 피어 링](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) 을 사용 하 여 vnet를 연결 하 여 Azure 인프라를 통해 보안 연결을 사용할 수 있습니다. 

위의 다이어그램에서 VNet 2와 VNet 3을 고려 합니다. Vm 1이 vm 2 또는 볼륨 2에 연결 되어야 하거나 vm 2가 VM 1 또는 볼륨 1에 연결 해야 하는 경우 VNet 2와 VNet 3 간에 VNet 피어 링을 사용 하도록 설정 해야 합니다. 

또한 vnet 1이 VNet 2를 사용 하 여 피어 링 하 고 VNet 2는 동일한 지역에서 VNet 3으로 피어 링는 시나리오를 고려해 야 합니다. Vnet 1의 리소스는 VNet 2의 리소스에 연결할 수 있지만 vnet 1 및 VNet 3이 피어 링 않는 한 VNet 3의 리소스에 연결할 수 없습니다. 

위의 다이어그램에서 VM 3은 볼륨 1에 연결할 수 있지만 VM 4는 볼륨 2에 연결할 수 없습니다.  그 이유는 스포크 Vnet 피어 링 없고 _전송 라우팅이 VNet 피어 링을 통해 지원 되지_않기 때문입니다.

## <a name="hybrid-environments"></a>하이브리드 환경

다음 다이어그램은 하이브리드 환경을 보여 줍니다. 

![하이브리드 네트워킹 환경](../media/azure-netapp-files/azure-netapp-files-network-hybrid-environment.png)

하이브리드 시나리오에서 온-프레미스 데이터 센터의 응용 프로그램은 Azure의 리소스에 액세스 해야 합니다.  데이터 센터를 Azure로 확장 하려는 경우 또는 Azure native services를 사용 하거나 재해 복구를 사용 하고자 하는 경우입니다. 사이트 간 VPN 또는 Express 경로를 통해 온-프레미스의 여러 리소스를 Azure의 리소스에 연결 하는 방법에 대한 자세한 내용은 [VPN Gateway 계획 옵션](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable) 을 참조 하세요.

하이브리드 허브-스포크 토폴로지에서 Azure의 허브 VNet은 온-프레미스 네트워크에 대한 연결의 중앙 지점 역할을 합니다. 스포크는 허브와 Vnet 피어 링, 워크 로드를 격리 하는 데 사용할 수 있습니다.

구성에 따라 허브 및 스포크의 리소스에 온-프레미스 리소스를 연결할 수 있습니다.

위에서 보여 주는 토폴로지에서 온-프레미스 네트워크는 Azure의 허브 VNet에 연결 되 고 허브 VNet과 동일한 지역 피어 링에 2 개의 스포크 Vnet 있습니다.  이 시나리오에서 Azure NetApp Files 볼륨에 대해 지원 되는 연결 옵션은 다음과 같습니다.

* 온-프레미스 리소스 VM 1과 VM 2는 사이트 간 VPN 또는 Express 경로 회로를 통해 허브의 볼륨 1에 연결할 수 있습니다. 
* 온-프레미스 리소스 VM 1과 VM 2는 사이트 간 VPN 및 지역 Vnet 피어 링을 통해 볼륨 2 또는 볼륨 3에 연결할 수 있습니다.
* 허브 VNet의 VM 3은 스포크 vnet 1의 볼륨 2와 스포크 VNet 2의 볼륨 3에 연결할 수 있습니다.
* 스포크 vnet 1 및 스포크 vnet 2의 vm 5에서 VM 4는 허브 VNet의 볼륨 1에 연결할 수 있습니다.

스포크 VNet 1의 VM 4는 스포크 VNet 2의 볼륨 3에 연결할 수 없습니다. 또한 스포크 VNet2의 VM 5는 스포크 VNet 1의 볼륨 2에 연결할 수 없습니다. 이는 스포크 Vnet 피어 링 없고 _전송 라우팅이 VNet 피어 링을 통해 지원 되지_않기 때문입니다.

## <a name="next-steps"></a>다음 단계

[Azure NetApp Files에 서브넷 위임](azure-netapp-files-delegate-subnet.md)
