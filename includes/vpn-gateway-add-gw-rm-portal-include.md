---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/27/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1894f38a92fe839dcf44cc3265aa1ca25c05d6f6
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2021
ms.locfileid: "111564681"
---
1. [Azure Portal](https://portal.azure.com)에서 **리소스, 서비스 및 문서 검색(G+/)** 에 **가상 네트워크 게이트웨이** 를 입력합니다. 검색 결과에서 **가상 네트워크 게이트웨이** 를 찾아서 항목을 선택합니다.

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-include/search.png" alt-text="검색 필드" lightbox="./media/vpn-gateway-add-gw-rm-portal-include/search-expand.png":::

1. **가상 네트워크 게이트웨이** 페이지에서 **+ 추가** 를 선택합니다. 그러면 **가상 네트워크 게이트웨이 만들기** 페이지가 열립니다.

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-include/add.png" alt-text="가상 네트워크 게이트웨이 페이지":::
1. **기본** 탭에서 가상 네트워크 게이트웨이 값을 입력합니다.

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-include/gateway.png" alt-text="게이트웨이 필드":::

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-include/gateway-vnet.png" alt-text="추가 게이트웨이 필드":::

   * **구독**: 드롭다운 목록에서 사용하려는 구독을 선택합니다.
   * **리소스 그룹**: 이 페이지에서 가상 네트워크를 선택하면 이 설정이 자동으로 채워집니다.

   **인스턴스 세부 정보**

   * **이름**: 게이트웨이 이름을 지정합니다. 게이트웨이 이름 지정은 게이트웨이 서브넷 이름 지정과 동일하지 않습니다. 만드는 게이트웨이 개체의 이름입니다.
   * **지역**: 리소스를 만들려는 지역을 선택합니다. 게이트웨이에 대한 지역은 가상 네트워크와 동일해야 합니다.
   * **게이트웨이 유형**: **VPN** 을 선택합니다. VPN 게이트웨이는 가상 네트워크 게이트웨이 유형 **VPN** 을 사용합니다.
   * **VPN 유형**: 구성에 대해 지정된 VPN 유형을 선택합니다. 대부분의 구성에는 경로 기반 VPN 유형이 필요합니다.
   * **SKU**: 드롭다운에서 게이트웨이 SKU를 선택합니다. 드롭다운에 나열되는 SKU는 선택하는 VPN 유형에 따라 달라집니다. 사용하려는 기능을 지원하는 SKU를 선택해야 합니다. 게이트웨이 SKU에 대한 자세한 내용은 [게이트웨이 SKU](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku)를 참조하세요.
   * **생성**: VPN Gateway 생성에 대한 자세한 내용은 [게이트웨이 SKU](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku)를 참조하세요.
   * **가상 네트워크**: 드롭다운에서 이 게이트웨이를 추가하려는 가상 네트워크를 선택합니다.
   * **게이트웨이 서브넷 주소 범위**: 이 필드는 VNet에 게이트웨이 서브넷이 없는 경우에만 표시됩니다. 가능한 경우 범위(/26, /25 등)를 /27 이상으로 설정합니다. /28보다 작은 범위는 만들지 않는 것이 좋습니다. 게이트웨이 서브넷이 이미 있는 경우 가상 네트워크로 이동하여 GatewaySubnet 세부 정보를 볼 수 있습니다. **서브넷** 을 클릭하여 범위를 확인합니다. 범위를 변경하려면 GatewaySubnet을 삭제하고 다시 만들 수 있습니다.

   **공용 IP 주소**

   이 설정은 VPN Gateway에 연결되는 공용 IP 주소 개체를 지정합니다. VPN Gateway가 생성될 때 공용 IP 주소가 이 개체에 동적으로 할당됩니다. 게이트웨이가 삭제되고 다시 만들어지는 경우에만 공용 IP 주소가 변경됩니다. VPN Gateway의 크기 조정, 다시 설정 또는 기타 내부 유지 관리/업그레이드 시에는 변경되지 않습니다.

     * **공용 IP 주소**: **새로 만들기** 를 선택한 상태로 둡니다.
     * **공용 IP 주소 이름**: 텍스트 상자에서 공용 IP 주소 인스턴스의 이름을 입력합니다.
     * **할당**: VPN 게이트웨이는 '동적'만 지원합니다.
     * **active-active 모드를 사용하도록 설정**: 활성-활성 게이트웨이 구성을 만드는 경우에만 **활성-활성 모드 활성화** 를 선택합니다. 그렇지 않은 경우 이 설정을 **사용 안 함** 상태로 둡니다.
     * 구성에서 이 설정이 명시적으로 필요한 경우를 제외하고 **BGP 구성** 을 **사용 안 함** 상태로 둡니다. 이 설정이 필요한 경우 기본 ASN은 65515이며 변경 가능합니다.
1. **검토 + 만들기** 를 선택하여 유효성 검사를 실행합니다.
1. 유효성 검사를 통과하면 **만들기** 를 선택하여 VPN 게이트웨이를 배포합니다.

게이트웨이에서 완전히 만들고 배포하는 데 최대 45분이 걸릴 수 있습니다. 배포 상태는 게이트웨이에 대한 [개요] 페이지에서 확인할 수 있습니다. 게이트웨이를 만든 후 포털에서 가상 네트워크를 살펴보면 게이트웨이에 할당된 IP 주소를 볼 수 있습니다. 게이트웨이가 연결된 디바이스로 표시됩니다.
