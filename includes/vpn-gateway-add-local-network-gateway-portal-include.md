---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9648f4c16e8d266bbdd504d4a7599b67a5c846b4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73523347"
---
1. [Azure Portal](https://portal.azure.com) 메뉴에서 **리소스 만들기**를 선택 합니다. 

   ![Azure Portal에서 리소스 만들기](./media/vpn-gateway-add-local-network-gateway-portal-include/azure-portal-create-resource.png)
2. **Marketplace 검색** 필드에서 **로컬 네트워크 게이트웨이**를 입력 한 다음 **enter** 키를 눌러 검색 합니다. 그러면 결과 목록이 표시됩니다. **로컬 네트워크 게이트웨이**를 클릭한 다음, **만들기** 단추를 클릭하여 **로컬 네트워크 게이트웨이 만들기** 페이지를 엽니다.

   ![로컬 네트워크 게이트웨이 만들기](./media/vpn-gateway-add-local-network-gateway-portal-include/create-local-network-gateway.png "로컬 네트워크 게이트웨이 만들기")

3. **로컬 네트워크 게이트웨이 만들기 블레이드**에서 로컬 네트워크 게이트웨이의 값을 지정합니다.

   - **이름:** 로컬 네트워크 게이트웨이 개체의 이름을 지정합니다.
   - **IP 주소:** Azure를 연결하려는 VPN 디바이스의 퍼블릭 IP 주소입니다. 유효한 퍼블릭 IP 주소를 지정합니다. IP 주소가 없는 경우에는 예제에 표시된 값을 사용할 수 있지만 이후에 설정에서 VPN 디바이스의 퍼블릭 IP 주소로 표시 IP 주소를 바꿔야 합니다. 그렇지 않으면 Azure를 연결할 수 없습니다.
   - **IP 주소 범위** 는 이 로컬 네트워크가 나타내는 네트워크에 대한 주소 범위를 가리킵니다. IP 범위를 여러 개 추가할 수 있습니다. 여기에서 지정한 IP 범위가 연결하려는 다른 네트워크의 IP 범위와 겹치지 않는지 확인합니다. Azure는 지정한 주소 범위를 온-프레미스 VPN 디바이스 IP 주소에 라우팅합니다. *온-프레미스 사이트에 연결하려는 경우 예제에 표시된 값이 아니라 자체 값을 여기에 사용합니다*.
   - **BGP 설정 구성:** BGP를 구성할 때에만 사용합니다. 그렇지 않으면 이것을 선택하지 마십시오.
   - **구독:** 올바른 구독이 표시되어 있는지 확인합니다.
   - **리소스 그룹:** 사용하려는 리소스 그룹을 선택합니다. 새 리소스 그룹을 만들거나 이미 만든 리소스 그룹을 선택할 수 있습니다.
   - **위치:** 위치는 다른 설정의 **리전**과 동일 합니다. 이 개체를 만들 위치를 선택합니다. VNet이 있는 동일한 위치를 선택하는 것이 좋지만 그렇게 하지 않아도 됩니다.

4. 값을 지정하는 작업을 완료하면 페이지 맨 아래에서 **만들기** 단추를 클릭하여 로컬 네트워크 게이트웨이를 만듭니다.
