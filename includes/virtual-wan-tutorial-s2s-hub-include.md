---
title: 포함 파일
description: 포함 파일
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 05/26/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ab341181db71a8df5dde27311e9169f9477c70f8
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110578287"
---
1. 사용자가 만든 가상 WAN을 찾습니다. 가상 WAN 페이지의 **연결** 섹션에서 **Hubs** 를 선택합니다.
2. **Hubs** 페이지에서 **+새 Hub** 를 선택하여 **가상 허브 만들기** 페이지를 엽니다.

   :::image type="content" source="./media/virtual-wan-tutorial-hub-include/basics.png" alt-text="스크린샷은 기본 탭이 선택된 가상 허브 만들기 창을 보여줍니다." border="false":::
3. **가상 허브 만들기** 페이지의 **기본 사항** 탭에서 다음 필드를 완료합니다.

   * **지역**(이전에는 위치라고 했음)
   * **이름**
   * **Hub 프라이빗 주소 공간** - 허브를 만들기 위한 최소 주소 공간은 /24입니다. /25 ~/32 범위의 항목을 사용하는 경우 생성 중에 오류가 발생합니다. 가상 허브의 서비스에 대한 서브넷 주소 공간을 명시적으로 계획할 필요가 없습니다. Azure Virtual WAN은 관리형 서비스이므로 다양한 게이트웨이/서비스(예: VPN 게이트웨이, ExpressRoute 게이트웨이, 사용자 VPN 지점 및 사이트 간 게이트웨이, 방화벽, 라우팅 등)에 대한 가상 허브에 적절한 서브넷을 만듭니다.
4. 완료되면 **다음: 사이트 간**

   :::image type="content" source="./media/virtual-wan-tutorial-hub-include/site-to-site.png" alt-text="스크린샷은 사이트 간 선택된 가상 허브 만들기 창을 보여줍니다." border="false":::

5. **사이트 간** 탭에서 다음 필드를 완료합니다.

   * **예** 를 선택하여 사이트 간 VPN을 만듭니다.
   * AS 번호 필드는 편집할 수 없습니다.
   * 드롭다운에서 **게이트웨이 배율 단위** 값을 선택합니다. 배율 단위를 사용하여 사이트를 연결할 가상 허브에서 만들 VPN Gateway의 집계 처리량을 선택할 수 있습니다. 1 배율 단위 = 500Mbps를 선택하는 경우 중복성을 위해 두 인스턴스가 생성되고 각각은 최대 500Mbps의 처리량을 갖게 됩니다. 예를 들어, 각각 10Mbps를 수행하는 5개의 분기가 있는 경우 헤드 끝에서 50Mbps로 집계되어야 합니다. 허브에 대한 분기 수를 지원하는 데 필요한 용량을 평가한 후 Azure VPN Gateway의 집계 용량 계획을 수립해야 합니다.
6. **검토 + 만들기** 를 선택하여 유효한지 확인합니다.
7. **만들기** 를 선택하여 허브를 만듭니다. 30분 후에 **새로 고침** 을 선택하여 **허브** 페이지에서 허브를 봅니다. **리소스로 이동** 을 선택하여 리소스로 이동합니다.
