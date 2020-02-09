---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/02/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 19b8a73835e8ac5ecaac7b42793140325964d17c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73523344"
---
Azure Portal을 사용하여 Resource Manager 배포 모델에서 VNet을 만들려면 아래 단계를 따릅니다. 이러한 단계를 자습서로 사용 하는 경우 **예제 값** 을 사용 합니다. 이러한 단계를 자습서로 사용하지 않는 경우 고유한 값으로 바꿔야 합니다. 가상 네트워크 작업에 대한 자세한 내용은 [Virtual Network 개요](../articles/virtual-network/virtual-networks-overview.md)를 참조하세요.

>[!NOTE]
>이 VNet을 온-프레미스 위치에 연결하려면 온-프레미스 네트워크 관리자와 협의하여 이 가상 네트워크에 특별히 사용할 수 있는 IP 주소 범위를 만들어야 합니다. VPN 연결의 양쪽 모두에 중복 주소 범위가 있는 경우 트래픽이 예상대로 라우팅되지 않습니다. 또한 이 VNet을 다른 VNet에 연결하려면 주소 공간이 다른 VNet과 겹치면 안됩니다. 이에 따라 네트워크 구성을 적절히 계획하도록 주의해야 합니다.
>

1. [Azure Portal](https://portal.azure.com) 메뉴에서 **리소스 만들기**를 선택 합니다. 

   ![Azure Portal에서 리소스 만들기](./media/vpn-gateway-create-virtual-network-portal-include/azure-portal-create-resource.png)
2. **마켓플레이스 검색** 필드에 ‘Virtual Network’를 입력합니다. 반환된 목록에서 **Virtual Network**를 찾아서 클릭하여 **Virtual Network** 페이지를 엽니다.
3. **만들기**를 클릭합니다. 그러면 **가상 네트워크 만들기** 페이지가 열립니다.
4. **가상 네트워크 만들기** 페이지에서 VNet 설정을 구성합니다. 필드를 채울 때 필드에 입력한 문자가 유효하면 빨간색 느낌표가 녹색 확인 표시가 됩니다. 다음 값을 사용합니다.

   - **이름**: VNet1
   - **주소 공간**: 10.1.0.0/16
   - **구독**: 나열된 구독이 사용하려는 구독인지 확인합니다. 드롭다운을 사용하여 구독을 변경할 수 있습니다.
   - **리소스 그룹**: TestRG1 (새 그룹을 만들려면 **새로 만들기** 클릭)
   - **위치**: 미국 동부
   - **서브넷**: Frontend
   - **주소 범위**: 10.1.0.0/24

   ![가상 네트워크 만들기 페이지](./media/vpn-gateway-create-virtual-network-portal-include/create-virtual-network1.png)
5. DDoS를 기본으로, 서비스 엔드포인트을 사용 안 함으로 설정 하 고 방화벽을 사용 안 함으로 유지 합니다.
6. **만들기**를 클릭하여 VNet을 만듭니다.