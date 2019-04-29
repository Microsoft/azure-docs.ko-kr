---
title: '여러 VPN 게이트웨이 사이트 간 연결을 VNet에 추가: Azure Portal: Resource Manager| Microsoft Docs'
description: 기존 연결이 있는 VPN Gateway에 다중 사이트 S2S 연결 추가
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: f3e8b165-f20a-42ab-afbb-bf60974bb4b1
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: cherylmc
ms.openlocfilehash: 4b9f007e00d0912687b723bd4f7e747da893948d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60760461"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>기존 VPN 게이트웨이 연결이 있는 VNet에 사이트 간 연결 추가

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell(클래식)](vpn-gateway-multi-site.md)
>
> 

이 문서는 Azure Portal을 사용하여 기존 연결이 있는 VPN 게이트웨이에 S2S(사이트 간) 연결을 추가하는 데 도움이 됩니다. 이러한 유형의 연결을 "다중 사이트" 구성이라고 합니다. S2S 연결, 사이트 간 연결 또는 VNet 간 연결이 이미 있는 VNet에 S2S 연결을 추가할 수 있습니다. 연결을 추가하는 데 몇 가지 제한이 있습니다. 구성을 시작하기 전에 확인하려면 이 문서의 [시작 하기 전에](#before) 섹션을 확인합니다. 

이 문서는 RouteBased VPN 게이트웨이가 있는 리소스 관리자 VNet에 적용됩니다. 이러한 단계가 ExpressRoute/사이트 간 공존 연결 구성에는 적용되지 않습니다. 공존 연결에 대한 자세한 내용은 [xpress 경로/S2S 공존 연결](../expressroute/expressroute-howto-coexist-resource-manager.md)을 참조하세요.

### <a name="deployment-models-and-methods"></a>배포 모델 및 메서드
[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

새 문서 및 추가 도구를 이 구성에 사용할 수 있게 되었으므로 다음 표를 업데이트합니다. 문서를 사용할 수 있는 경우 표에서 직접 링크를 제공합니다.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="before"></a>시작하기 전에
다음 사항을 확인합니다.

* ExpressRoute/S2S 공존 연결을 만들지 않습니다.
* 기존 연결로 Resource Manager 배포 모델을 사용하여 만든 가상 네트워크가 있습니다.
* VNet용 가상 네트워크 게이트웨이는 RouteBased입니다. PolicyBased VPN 게이트웨이가 있다면 가상 네트워크 게이트웨이를 삭제하고 새 VPN 게이트웨이를 RouteBased로 만들어야 합니다.
* 어떠한 주소 범위도 이 Vnet이 연결하고 있는 Vnet에 대해 겹치지 않습니다.
* 호환되는 VPN 디바이스 및 그것을 구성할 수 있는 사람이 있습니다. [VPN 디바이스 정보](vpn-gateway-about-vpn-devices.md)를 참조하세요. VPN 디바이스를 구성하는 방법과 온-프레미스 네트워크 구성에 있는 IP 주소 범위에 익숙하지 않은 경우 세부 정보를 제공할 수 있는 다른 사람의 도움을 받아야 합니다.
* VPN 디바이스에 대한 외부 연결 공용 IP 주소가 있습니다. 이 IP 주소는 NAT 뒤에 배치할 수 없습니다.

## <a name="part1"></a>1부 - 연결 구성
1. 브라우저에서 [Azure Portal](https://portal.azure.com) 로 이동하고 필요한 경우 Azure 계정으로 로그인합니다.
2. **모든 리소스**를 클릭하고 리소스 목록에서 **가상 네트워크 게이트웨이**를 찾아 클릭합니다.
3. **가상 네트워크 게이트웨이** 페이지에서 **연결**을 클릭합니다.
   
    ![연결 페이지](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connectionsblade.png "연결 페이지")<br>
4. **연결** 페이지에서 **+추가**를 클릭합니다.
   
    ![연결 추가 버튼](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addbutton.png "연결 추가 버튼")<br>
5. **연결 추가** 페이지에서 다음 필드에 정보를 입력합니다.
   
   * **이름:** 연결을 만들고자 하는 사이트에 부여하고자 하는 이름입니다.
   * **연결 형식:** **사이트 간(IPSec)** 을 선택합니다.
     
     ![연결 추가 페이지](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addconnectionblade.png "연결 추가 페이지")<br>

## <a name="part2"></a>2부 - 로컬 네트워크 게이트웨이 추가
1. **로컬 네트워크 게이트웨이**를 클릭하고 ***로컬 네트워크 게이트웨이를 선택 합니다***. **로컬 네트워크 게이트웨이 선택** 페이지가 열립니다.
   
    ![로컬 네트워크 게이트웨이 선택](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/chooselng.png "로컬 네트워크 게이트웨이 선택")<br>
2. **새로 만들기**를 클릭하여 **로컬 네트워크 게이트웨이 만들기** 페이지를 엽니다.
   
    ![로컬 네트워크 게이트웨이 만들기 페이지](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/createlngblade.png "로컬 네트워크 게이트웨이 만들기")<br>
3. **로컬 네트워크 게이트웨이 만들기** 페이지에서 다음 필드에 정보를 입력합니다.
   
   * **이름:** 로컬 네트워크 게이트웨이 리소스에 부여하고자 하는 이름입니다.
   * **IP 주소:** 연결하려는 사이트에 있는 VPN 디바이스의 공용 IP 주소입니다.
   * **주소 공간:** 새 로컬 네트워크 사이트로 라우팅하려는 주소 공간입니다.
4. **로컬 네트워크 게이트웨이 만들기** 페이지에서 **확인**을 클릭하여 변경 내용을 저장합니다.

## <a name="part3"></a>3부 - 공유 키 추가 및 연결 만들기
1. **연결 추가** 페이지에서 연결을 만드는 데 사용할 공유 키를 추가합니다. 사용자가 VPN 디바이스에서 공유 키를 가져오거나, 여기서 공유 키를 만든 후 VPN 디바이스에서 동일한 공유 키를 사용하도록 구성할 수 있습니다. 중요한 것은 키가 정확히 동일하다는 것입니다.
   
    ![공유 키](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/sharedkey.png "Shared key")<br>
2. 페이지의 맨 아래에서 **확인**을 클릭하여 연결을 만듭니다.

## <a name="part4"></a>4부 - VPN 연결 확인


[!INCLUDE [vpn-gateway-verify-connection-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="next-steps"></a>다음 단계

연결이 완료되면 가상 네트워크에 가상 머신을 추가할 수 있습니다. 자세한 내용은 [가상 머신 학습 경로](/learn/paths/deploy-a-website-with-azure-virtual-machines/) 를 참조하세요.
