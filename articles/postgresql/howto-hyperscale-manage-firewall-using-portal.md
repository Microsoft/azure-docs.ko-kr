---
title: 방화벽 규칙 관리-Hyperscale (Citus)-Azure Database for PostgreSQL
description: Azure Portal를 사용 하 여 Citus (Azure Database for PostgreSQL-Hyperscale)에 대 한 방화벽 규칙 만들기 및 관리
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 660c395e6cff81b0abcac07e66385f80a538695f
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977542"
---
# <a name="manage-firewall-rules-for-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL-Hyperscale (Citus)에 대 한 방화벽 규칙 관리
서버 수준 방화벽 규칙은 지정 된 IP 주소 또는 IP 주소 범위에서 Citus (Hyperscale) 코디네이터 노드에 대 한 액세스를 관리 하는 데 사용할 수 있습니다.

## <a name="prerequisites"></a>Előfeltételek
이 방법 가이드를 단계별로 실행 하려면 다음이 필요 합니다.
- 서버 그룹은 [Azure Database for PostgreSQL – Hyperscale (Citus) 서버 그룹을 만듭니다](quickstart-create-hyperscale-portal.md).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Kiszolgálószintű tűzfalszabály létrehozása az Azure Portalon

> [!NOTE]
> 이러한 설정은 Citus (Azure Database for PostgreSQL-Hyperscale) 서버 그룹을 만드는 동안에도 액세스할 수 있습니다. **네트워킹** 탭에서 **공용 엔드포인트**을 클릭 합니다.
> ![Azure Portal-네트워킹 탭](./media/howto-hyperscale-manage-firewall-using-portal/0-create-public-access.png)

1. PostgreSQL 서버 그룹 페이지의 보안 제목 아래에서 **네트워킹** 을 클릭 하 여 방화벽 규칙을 엽니다.

   ![네트워킹 Azure Portal 클릭](./media/howto-hyperscale-manage-firewall-using-portal/1-connection-security.png)

2. 도구 모음 (아래 옵션)에서 또는 링크 (옵션 B)에서 **클라이언트 IP 추가**를 클릭 합니다. 어떤 방법으로 든 Azure 시스템에서 인식 하는 대로 컴퓨터의 공용 IP 주소를 사용 하 여 방화벽 규칙을 자동으로 만듭니다.

   ![Azure Portal 클라이언트 IP 추가를 클릭 합니다.](./media/howto-hyperscale-manage-firewall-using-portal/2-add-my-ip.png)

또는 옵션 B의 오른쪽에 있는 **+ 추가 0.0.0.0-255.255.255.255** 를 클릭 하면 IP 뿐만 아니라 전체 인터넷을 통해 코디네이터 노드의 포트 5432에 액세스할 수 있습니다. 이 경우 클라이언트는 클러스터를 사용 하기 위해 올바른 사용자 이름과 암호를 사용 하 여 로그인 해야 합니다. 그럼에도 불구 하 고, 짧은 기간 동안만 프로덕션 데이터베이스에 대해서만 전 세계에 액세스를 허용 하는 것이 좋습니다.

3. 구성을 저장 하기 전에 IP 주소를 확인 합니다. 경우에 따라 Azure Portal에서 관찰 되는 IP 주소는 인터넷 및 Azure 서버에 액세스할 때 사용 되는 IP 주소와 다릅니다. 따라서 규칙 함수를 예상 대로 만들려면 시작 IP와 끝 IP를 변경 해야 할 수 있습니다.
   검색 엔진 또는 기타 온라인 도구를 사용 하 여 사용자 고유의 IP 주소를 확인 합니다. 예를 들어 "내 IP 란?"를 검색 합니다.

   ![내 IP의 Bing 검색](./media/howto-hyperscale-manage-firewall-using-portal/3-what-is-my-ip.png)

4. 주소 범위를 추가 합니다. 방화벽 규칙에서 단일 IP 주소 또는 주소 범위를 지정할 수 있습니다. 규칙을 단일 IP 주소로 제한 하려는 경우 시작 IP 및 종료 IP의 필드에 동일한 주소를 입력 합니다. 방화벽을 열면 관리자, 사용자 및 응용 프로그램이 포트 5432의 코디네이터 노드에 액세스할 수 있습니다.

5. 도구 모음에서 **저장** 을 클릭 하 여이 서버 수준 방화벽 규칙을 저장 합니다. 방화벽 규칙에 대 한 업데이트가 성공적으로 수행 되었는지 확인 될 때까지 기다립니다.

## <a name="connecting-from-azure"></a>Csatlakozás az Azure-ból

Azure에서 호스트 되는 응용 프로그램 (예: azure Web Apps 응용 프로그램 또는 Azure VM에서 실행 되는 응용 프로그램)에 대 한 하이퍼 확장 데이터베이스 액세스 권한을 부여 하는 쉬운 방법이 있습니다. **네트워킹** 창에서 포털의 **Azure 서비스 및 리소스에서이 서버 그룹에 액세스 하도록 허용** 옵션을 **예** 로 설정 하 고 **저장**을 누릅니다.

> [!IMPORTANT]
> Ez a beállítás konfigurálja a tűzfalat arra, hogy engedélyezzen minden, az Azure felől érkező kapcsolatot, beleértve a más ügyfelek előfizetéseiből érkező kapcsolatokat is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Meglévő kiszolgálószintű tűzfalszabályok kezelése az Azure Portalon
방화벽 규칙을 관리 하는 단계를 반복 합니다.
* 현재 컴퓨터를 추가 하려면 단추를 클릭 하 여 **클라이언트 IP 추가**를 클릭 합니다. Kattintson a **Mentés** gombra a módosítások mentéséhez.
* További IP-címek hozzáadásához adja meg a Szabály neve, a Kezdő IP-cím és a Záró IP-cím értékét. Kattintson a **Mentés** gombra a módosítások mentéséhez.
* Meglévő szabály módosításához kattintson a szabály valamelyik mezőjére, és adja meg a módosításokat. Kattintson a **Mentés** gombra a módosítások mentéséhez.
* 기존 규칙을 삭제 하려면 줄임표 [...]를 클릭 하 고 **삭제** 를 클릭 하 여 규칙을 제거 합니다. Kattintson a **Mentés** gombra a módosítások mentéséhez.

## <a name="next-steps"></a>Következő lépések
- 연결 문제를 해결 하는 방법을 비롯 하 여 [방화벽 규칙의 개념](concepts-hyperscale-firewall-rules.md)에 대해 자세히 알아보세요.
