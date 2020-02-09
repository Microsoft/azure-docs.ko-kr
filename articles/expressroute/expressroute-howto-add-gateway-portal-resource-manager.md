---
title: 'Azure Express 경로: VNet에 게이트웨이 추가: 포털'
description: 이 문서에서는 Azure Portal를 사용 하 여 이미 생성 된 Express 경로에 대한 리소스 관리자 VNet에 가상 네트워크 게이트웨이를 추가 하는 과정을 안내 합니다.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 87b656f0ef999b3b15a89476f5cba4c4fcfc2b1e
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037391"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>Azure Portal을 사용하여 ExpressRoute에 대한 가상 네트워크 게이트웨이 구성
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [클래식 - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [비디오 - Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

이 문서에서는 기존 VNet에 대한 가상 네트워크 게이트웨이를 추가하는 단계를 안내합니다. 이 문서에서는 기존 VNet에 대한 가상 네트워크(VNet) 게이트웨이를 추가, 제거하고 크기를 조정하는 단계를 안내합니다. 이 구성에서 수행하는 단계는 Resource Manager 배포 모델을 사용하여 만든 VNet(ExpressRoute 구성에서 사용됨)에만 해당합니다. 가상 네트워크 게이트웨이 및 ExpressRoute의 게이트웨이 구성 설정에 대한 자세한 내용은 [ExpressRoute에 대한 가상 네트워크 게이트웨이 정보](expressroute-about-virtual-network-gateways.md)를 참조하세요. 


## <a name="before-beginning"></a>시작하기 전에

이 작업의 단계는 다음 구성 참조 목록의 값을 기반으로 VNet을 사용합니다. 예제 단계에서 이 목록을 사용합니다. 목록을 복사하여 참조로 사용하고 값을 사용자 값으로 바꿀 수 있습니다.

**구성 참조 목록**

* Virtual Network 이름 = "TestVNet"
* Virtual Network 주소 공간: 192.168.0.0/16
* 서브넷 이름= “FrontEnd” 
    * 서브넷 주소 공간 = “192.168.1.0/24”
* 리소스 그룹: "TestRG"
* 위치 = “미국 동부”
* 게이트웨이 서브넷 이름: "GatewaySubnet" 게이트웨이 서브넷의 이름을 항상 *GatewaySubnet*으로 지정해야 합니다.
    * 게이트웨이 서브넷 주소 공간 = "192.168.200.0/26"
* 게이트웨이 이름 = “ERGW”
* 게이트웨이 공용 IP 이름 = “MyERGWVIP”
* 게이트웨이 유형 = “ExpressRoute” Express 경로 구성에 이 유형이 필요합니다.

구성을 시작하기 전에 이러한 단계의 [비디오](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)를 시청하십시오.

## <a name="create-the-gateway-subnet"></a>게이트웨이 서브넷 만들기

1. [포털](https://portal.azure.com)에서 가상 네트워크 게이트웨이를 만들려는 Resource Manager 가상 네트워크로 이동합니다.
2. VNet 블레이드의 **설정** 섹션에서 **서브넷**을 클릭하여 서브넷 블레이드를 확장합니다.
3. **서브넷** 블레이드에서 **+ 게이트웨이 서브넷**을 클릭하여 **서브넷 추가** 블레이드를 엽니다. 
   
    ![게이트웨이 서브넷 추가](./media/expressroute-howto-add-gateway-portal-resource-manager/addgwsubnet.png "게이트웨이 서브넷 추가")


4. 서브넷의 **이름**에 'GatewaySubnet' 값이 자동으로 채워집니다. Azure가 서브넷을 게이트웨이 서브넷으로 인식하기 위해 이 값이 필요합니다. 자동으로 채워진 **주소 범위** 값을 구성 요구 사항과 일치하도록 조정합니다. /27 이상의 게이트웨이 서브넷을 만드는 것이 좋습니다(/26, /25, 등). 그런 다음 **확인**을 클릭하여 값을 저장하고 게이트웨이 서브넷을 만듭니다.

    ![서브넷 추가](./media/expressroute-howto-add-gateway-portal-resource-manager/addsubnetgw.png "서브넷 추가")

## <a name="create-the-virtual-network-gateway"></a>가상 네트워크 게이트웨이 만들기

1. 포털의 왼쪽에서 **+** 를 클릭하고 검색에서 'Virtual Network 게이트웨이'를 입력합니다. 검색 결과에서 **가상 네트워크 게이트웨이**를 찾아서 항목을 클릭합니다. **가상 네트워크 게이트웨이** 블레이드의 아래쪽에서 **만들기**를 클릭합니다. 그러면 **가상 네트워크 게이트웨이 만들기** 블레이드가 열립니다.
2. **가상 네트워크 게이트웨이 만들기** 블레이드에서 가상 네트워크 게이트웨이의 값을 채웁니다.

    ![가상 네트워크 게이트웨이 블레이드 필드 만들기](./media/expressroute-howto-add-gateway-portal-resource-manager/gw.png "가상 네트워크 게이트웨이 블레이드 필드 만들기")
3. **이름**: 게이트웨이 이름을 지정합니다. 이는 게이트웨이 서브넷 이름 지정과 동일하지는 않습니다. 만드는 게이트웨이 개체의 이름입니다.
4. **게이트웨이 유형**: **ExpressRoute**를 선택합니다.
5. **SKU**: 드롭다운에서 게이트웨이 SKU를 선택합니다.
6. **위치**: 가상 네트워크가 있는 위치를 가리키도록 **위치** 필드를 조정합니다. 위치에서 가상 네트워크가 있는 지역을 가리키고 있지 않으면 가상 네트워크가 [가상 네트워크 선택] 드롭다운에 나타나지 않습니다.
7. 이 게이트웨이를 추가할 가상 네트워크를 선택합니다. **가상 네트워크**를 클릭하여 **가상 네트워크 선택** 블레이드를 엽니다. VNet을 선택합니다. VNet이 보이지 않으면 **위치** 필드가 가상 네트워크가 있는 지역을 가리키고 있는지 확인합니다.
9. 공용 IP 주소를 선택합니다. **공용 IP 주소**를 클릭하여 **공용 IP 주소 선택** 블레이드를 엽니다. **+새로 만들기**를 클릭하여 **공용 IP 주소 만들기** 블레이드를 엽니다. 공용 IP 주소의 이름을 입력합니다. 이 블레이드는 공용 IP 주소를 동적으로 할당할 공용 IP 주소 개체를 만듭니다. **확인**을 클릭하여 이 블레이드에 변경 내용을 저장합니다.
10. **구독**: 올바른 구독을 선택하는지 확인합니다.
11. **리소스 그룹**: 이 설정은 선택한 Virtual Network에 의해 결정됩니다.
12. 앞의 설정을 지정한 후에는 **위치**를 조정하지 않습니다.
13. 설정을 확인합니다. 게이트웨이를 대시보드에 표시하려면 블레이드 아래쪽에서 **대시보드에 고정**을 선택할 수 있습니다.
14. **만들기** 를 클릭하여 게이트웨이 만들기를 시작합니다. 설정이 검증되었으며 게이트웨이가 배포됩니다. 가상 네트워크 게이트웨이 만들기는 완료되는 데 최대 45분까지 소요됩니다.

## <a name="next-steps"></a>다음 단계
VNet 게이트웨이를 만든 후 VNet을 ExpressRoute 회로에 연결할 수 있습니다. [Virtual Network를 ExpressRoute 회로에 연결](expressroute-howto-linkvnet-portal-resource-manager.md)을 참조하세요.
