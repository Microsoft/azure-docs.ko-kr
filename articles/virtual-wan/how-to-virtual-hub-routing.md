---
title: 가상 허브 라우팅을 구성하는 방법
titleSuffix: Azure Virtual WAN
description: 이 문서에서는 가상 허브 라우팅을 구성 하는 방법을 설명 합니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 07/07/2020
ms.author: cherylmc
ms.openlocfilehash: 00d79f8058d7d478f7fe97ba9677e47098b70e1b
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89145929"
---
# <a name="how-to-configure-virtual-hub-routing"></a>가상 허브 라우팅을 구성하는 방법

가상 허브는 사이트 간 VPN 게이트웨이, Express 경로 게이트웨이, 지점 및 사이트 간 게이트웨이 및 Azure 방화벽과 같은 여러 게이트웨이를 포함할 수 있습니다. 가상 허브의 라우팅 기능은 BGP (Border Gateway Protocol)를 사용 하는 게이트웨이와 전송 라우팅을 비롯 한 모든 라우팅을 관리 하는 라우터에서 제공 됩니다. 또한이 라우터는 가상 허브에 연결 하는 가상 네트워크 간의 전송 연결을 제공 하며 50 Gbps의 최대 집계 처리량을 지원할 수 있습니다. 이러한 라우팅 기능은 표준 가상 WAN 고객에 게 적용 됩니다.

자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조 하세요.

## <a name="create-a-route-table"></a><a name="create-table"></a>경로 테이블 만들기

1. Azure Portal에서 가상 허브로 이동 합니다.
2. **연결**아래에서 **라우팅**을 선택 합니다. 라우팅 페이지에 **기본** 및 **없음** 경로 테이블이 표시 됩니다.

   :::image type="content" source="./media/how-to-virtual-hub-routing/routing.png" alt-text="라우팅 페이지":::
3. **+ 경로 테이블 만들기** 를 선택 하 여 **경로 테이블 만들기** 페이지를 엽니다.
4. 경로 테이블 만들기 페이지 **기본 사항** 탭에서 다음 필드를 완료 합니다.

   :::image type="content" source="./media/how-to-virtual-hub-routing/basics.png" alt-text="기본 사항 탭":::

   * **이름**
   * **경로**
   * **경로 이름**
   * **대상 유형**
   * **대상 접두사**: 접두사를 집계할 수 있습니다. 예: VNet 1:10.1.0.0/24 및 VNet 2:10.1.1.0/24는 10.1.0.0/16으로 집계할 수 있습니다. **분기** 경로는 연결 된 모든 VPN 사이트, express 경로 회로 및 사용자 VPN 연결에 적용 됩니다.
   * **다음 홉**: 가상 네트워크 연결 또는 Azure 방화벽의 목록입니다.

     가상 네트워크 연결을 선택 하는 경우 **고정 경로 구성**이 표시 됩니다. 이는 선택적 구성 설정입니다. 자세한 내용은 [정적 경로 구성](about-virtual-hub-routing.md#static)을 참조 하세요.

      :::image type="content" source="./media/how-to-virtual-hub-routing/next-hop.png" alt-text="다음 홉":::

5. **레이블 탭을** 선택 하 여 레이블 이름을 구성 합니다. 레이블은 경로 테이블을 논리적으로 그룹화 하는 메커니즘을 제공 합니다.

    :::image type="content" source="./media/how-to-virtual-hub-routing/labels.png" alt-text="레이블 이름 구성":::

6. 연결 탭 **을 선택 하 여 연결** 을 경로 테이블에 연결 합니다.
**분기**, **가상 네트워크**및 연결의 **현재 설정이** 표시 됩니다.

    :::image type="content" source="./media/how-to-virtual-hub-routing/associations.png" alt-text="경로 테이블에 대 한 연결 연결":::

7. **전파** 탭을 선택 하 여 연결의 경로를 경로 테이블에 전파 합니다.

    :::image type="content" source="./media/how-to-virtual-hub-routing/propagations.png" alt-text="경로 전파":::

8. **만들기** 를 선택 하 여 경로 테이블을 만듭니다.

## <a name="to-edit-a-route-table"></a><a name="edit-table"></a>경로 테이블을 편집 하려면

Azure Portal에서 가상 허브의 경로 테이블을 찾습니다. 모든 정보를 편집할 경로 테이블을 선택 합니다.

## <a name="to-delete-a-route-table"></a><a name="delete-table"></a>경로 테이블을 삭제하려면

Azure Portal에서 가상 허브의 경로 테이블을 찾습니다. 기본 또는 없음 경로 테이블은 삭제할 수 없습니다. 그러나 모든 사용자 지정 경로 테이블을 삭제할 수 있습니다. **"..."** 를 클릭 한 다음 **삭제**를 선택 합니다.

## <a name="to-view-effective-routes"></a><a name="view-routes"></a>유효 경로를 보려면

Azure Portal에서 가상 허브의 경로 테이블을 찾습니다. **"..."** 를 클릭 하 고 **유효 경로** 를 선택 하 여 선택한 경로 테이블에 의해 학습 된 경로를 확인 합니다. 경로 테이블에 대 한 연결의 전파 된 경로는 경로 테이블의 **유효 경로** 에 자동으로 채워집니다. 자세한 내용은 [유효 경로 정보](effective-routes-virtual-hub.md)를 참조 하세요.

:::image type="content" source="./media/how-to-virtual-hub-routing/effective.png" alt-text="유효 경로 보기" lightbox="./media/how-to-virtual-hub-routing/effective-expand.png":::

## <a name="to-set-up-routing-configuration-for-a-virtual-network-connection"></a><a name="routing-configuration"></a>가상 네트워크 연결에 대 한 라우팅 구성을 설정 하려면

1. Azure Portal에서 가상 WAN으로 이동 하 고 **연결**에서 **Virtual Network 연결**을 선택 합니다.
1. **+ 연결 추가**를 선택 합니다.
1. 드롭다운에서 가상 네트워크를 선택 합니다.
1. 경로 테이블에 연결할 라우팅 구성을 설정 합니다. **경로 테이블 연결**의 드롭다운에서 경로 테이블을 선택 합니다.
1. 하나 이상의 경로 테이블에 전파 되도록 라우팅 구성을 설정 합니다. **경로 테이블에 전파**의 경우 드롭다운에서 선택 합니다.
1. **고정 경로의**경우 네트워크 가상 어플라이언스에 대 한 고정 경로 (해당 하는 경우)를 구성 합니다. 가상 WAN은 가상 네트워크 연결의 고정 경로에 대 한 다음 홉 IP 하나를 지원 합니다. 예를 들어 수신 및 송신 트래픽 흐름에 대 한 별도의 가상 어플라이언스를 보유 한 경우 별도의 Vnet에 가상 어플라이언스를 포함 하 고 Vnet를 가상 허브에 연결 하는 것이 가장 좋습니다.


:::image type="content" source="./media/how-to-virtual-hub-routing/routing-configuration.png" alt-text="라우팅 구성 설정" lightbox="./media/how-to-virtual-hub-routing/routing-configuration-expand.png":::

## <a name="next-steps"></a>다음 단계

가상 허브 라우팅에 대 한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조 하세요.
Virtual WAN에 대한 자세한 내용은 [FAQ](virtual-wan-faq.md)를 참조하세요.
