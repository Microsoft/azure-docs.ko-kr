---
title: DHCP를 만들고 관리 하는 방법
description: 이 문서에서는 Azure VMware 솔루션에서 DHCP를 관리 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 3fc3de228179925afdf1b7c1015c577fd9c4c924
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752166"
---
# <a name="how-to-create-and-manage-dhcp-in-azure-vmware-solution"></a>Azure VMWare 솔루션에서 DHCP를 만들고 관리 하는 방법

NSX-T는 사설 클라우드에 대해 DHCP를 구성 하는 기능을 제공 합니다. NSX-T를 사용 하 여 DHCP 서버를 호스트 하려는 경우 [dhcp 서버 만들기](#create-dhcp-server)를 참조 하세요. 그렇지 않고 네트워크에 타사 외부 DHCP 서버가 있고 해당 DHCP 서버에 요청을 릴레이 하려는 경우 [dhcp 릴레이 서비스 만들기](#create-dhcp-relay-service)를 참조 하세요.

## <a name="create-dhcp-server"></a>DHCP 서버 만들기

NSX에서 DHCP 서버를 구성 하려면 다음 단계를 사용 합니다.

NSX manager에서 **네트워킹** 탭으로 이동 하 고 **IP 관리**아래에서 **DHCP** 를 선택 합니다. **서버 추가** 단추를 선택 합니다. 그런 다음 서버 이름과 서버 IP 주소를 제공 합니다. 완료 되 면 **저장**을 선택 합니다.

:::image type="content" source="./media/manage-dhcp/dhcp-server-settings.png" alt-text="DHCP 서버 추가" border="true":::

### <a name="connect-dhcp-server-to-the-tier-1-gateway"></a>계층 1 게이트웨이에 DHCP 서버를 연결 합니다.

1. **계층 1 게이트웨이**를 선택 하 고, 게이트웨이를 선택 하 고, **편집** 을 선택 합니다.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway.png" alt-text="사용할 게이트웨이를 선택 합니다." border="true":::

1. **IP 할당 집합 없음** 을 선택 하 여 서브넷을 추가 합니다.

   :::image type="content" source="./media/manage-dhcp/add-subnet.png" alt-text="서브넷 추가" border="true":::

1. 다음 화면의 **유형** 드롭다운에서 **DHCP 로컬 서버** 를 선택 합니다. **Dhcp 서버**에 대해 **기본 dhcp** 를 선택 하 고 **저장**을 선택 합니다.

   :::image type="content" source="./media/manage-dhcp/set-ip-address-management.png" alt-text="dhcp 서버에 대 한 옵션 선택" border="true":::

1. **계층 1 게이트웨이** 창에서 **저장**을 선택 합니다. 다음 화면에 **저장 된 변경 내용이**표시 되 면 **편집 닫기** 를 선택 하 여 마칩니다.

### <a name="add-a-network-segment"></a>네트워크 세그먼트 추가

DHCP 서버를 만들었으면 네트워크 세그먼트를 추가 해야 합니다.

1. NSX에서 **네트워킹** 탭을 선택 하 고 **연결**아래에서 **세그먼트** 를 선택 합니다. **세그먼트 추가**를 선택 합니다. 세그먼트 이름을, 계층-1 게이트웨이에 연결 합니다. 그런 다음 **서브넷 설정** 을 선택 하 여 새 서브넷을 구성 합니다. 

   :::image type="content" source="./media/manage-dhcp/add-segment.png" alt-text="새 네트워크 세그먼트 추가" border="true":::

1. **서브넷 설정** 창에서 **서브넷 추가**를 선택 합니다. 게이트웨이 IP 주소와 DHCP 범위를 입력 하 고 **추가** 를 선택한 다음 **적용** 을 선택 합니다.

   :::image type="content" source="./media/manage-dhcp/add-subnet-segment.png" alt-text="네트워크 세그먼트 추가" border="true":::

1. 완료 되 면 **저장** 을 선택 하 여 네트워크 세그먼트 추가를 완료 합니다.

   :::image type="content" source="./media/manage-dhcp/segments-complete.png" alt-text="세그먼트 완료" border="true":::

## <a name="create-dhcp-relay-service"></a>DHCP 릴레이 서비스 만들기

1. NXT-T 창에서 **네트워킹** 탭을 선택 하 고 **IP 관리**에서 **DHCP**를 선택 합니다. **서버 추가**를 선택 합니다. **서버 유형에** 대해 DHCP 릴레이를 선택 하 고 릴레이 서버에 대 한 서버 이름 및 IP 주소를 입력 합니다. **저장**을 선택하여 변경 내용을 저장합니다.

   :::image type="content" source="./media/manage-dhcp/create-dhcp-relay.png" alt-text="dhcp 릴레이 서버 만들기" border="true":::

1. **연결**아래에서 **계층 1 게이트웨이를** 선택 합니다. 계층 1 게이트웨이에서 세로 줄임표를 선택 하 고 **편집**을 선택 합니다.

   :::image type="content" source="./media/manage-dhcp/edit-tier-1-gateway-relay.png" alt-text="계층 1 게이트웨이 편집" border="true":::

1. Ip **할당 설정 안 함** 을 선택 하 여 ip 주소 할당을 정의 합니다.

   :::image type="content" source="./media/manage-dhcp/edit-ip-address-allocation.png" alt-text="ip 주소 할당 편집" border="true":::

1. 대화 상자에서 **유형**에 대해 **DHCP Relay 서버**를 선택 합니다. **Dhcp 릴레이** 드롭다운에서 dhcp 릴레이 서버를 선택 합니다. 완료 되 면 **저장** 을 선택 합니다.

   :::image type="content" source="./media/manage-dhcp/set-ip-address-management-relay.png" alt-text="ip 주소 관리 설정" border="true":::

## <a name="specify-a-dhcp-range-ip-on-segment"></a>세그먼트에서 DHCP 범위 IP 지정

> [!NOTE]
> 이 구성은 DHCP 클라이언트 세그먼트에서 DHCP 릴레이 기능을 인식 하는 데 필요 합니다. 

1. **연결**에서 **세그먼트**를 선택 합니다. 세로 줄임표를 선택 하 고 **편집**을 선택 합니다. 대신 새 세그먼트를 추가 하려는 경우 **세그먼트 추가** 를 선택 하 여 새 세그먼트를 만들 수 있습니다.

   :::image type="content" source="./media/manage-dhcp/edit-segments.png" alt-text="네트워크 서브넷 편집" border="true":::

1. 세그먼트에 대 한 세부 정보를 추가 합니다.  **서브넷 아래에서** 값을 선택 하거나 서브넷을 **설정** 하 여 서브넷을 추가 하거나 수정 합니다.

   :::image type="content" source="./media/manage-dhcp/network-segments.png" alt-text="네트워크 세그먼트" border="true":::

1. 세로 줄임표를 선택 하 고 **편집**을 선택 합니다. 새 서브넷을 만들어야 하는 경우 **서브넷 추가** 를 선택 하 여 게이트웨이를 만들고 DHCP 범위를 구성 합니다. IP 풀의 범위를 제공 하 고 **적용**을 선택한 다음, **저장** 을 선택 합니다.

   :::image type="content" source="./media/manage-dhcp/edit-subnet.png" alt-text="서브넷 편집" border="true":::

1. 이제 DHCP 서버 풀이 세그먼트에 할당 됩니다.

   :::image type="content" source="./media/manage-dhcp/assigned-to-segment.png" alt-text="세그먼트에 할당 된 DHCP 서버 풀" border="true":::
