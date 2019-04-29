---
title: Azure Database for MariaDB에서 MariaDB 방화벽 규칙 만들기 및 관리
description: Azure Portal을 사용한 Azure Database for MariaDB 방화벽 규칙 만들기 및 관리
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 04/09/2019
ms.openlocfilehash: e9ab243692f5a4a1ec7de25774f5bad867698fc3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60746401"
---
# <a name="create-and-manage-azure-database-for-mariadb-firewall-rules-by-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Database for MariaDB 방화벽 규칙 만들기 및 관리
서버 수준 방화벽 규칙은 지정된 된 IP 주소 또는 IP 주소에서 MariaDB 서버에 대 한 Azure Database에 대 한 액세스 관리를 사용할 수 있습니다.

Virtual Network (VNet) 규칙은 서버에 대 한 액세스를 보호 하려면 데도 사용할 수 있습니다. 에 대해 자세히 알아보세요 [만들기 및 관리 가상 네트워크 서비스 끝점 및 Azure portal을 사용 하 여 규칙](howto-manage-vnet-portal.md)합니다.

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Azure Portal에서 서버 수준 방화벽 규칙 만들기

1. MariaDB 서버 페이지의 설정 제목에서 **연결 보안**을 클릭하여 Azure Database for MariaDB에 대한 연결 보안 페이지를 엽니다.

   ![Azure Portal - 보안 연결 클릭](./media/howto-manage-firewall-portal/1-connection-security.png)

2. 도구 모음에서 **내 IP 추가**를 클릭합니다. 이렇게 하면 Azure 시스템에서 감지한 컴퓨터의 공용 IP 주소를 사용하는 방화벽 규칙이 자동으로 만들어집니다.

   ![Azure Portal - 내 IP 추가 클릭](./media/howto-manage-firewall-portal/2-add-my-ip.png)

3. 구성을 저장하기 전에 사용자의 IP 주소를 확인합니다. 상황에 따라 Azure Portal에서 관찰하는 IP 주소는 인터넷 및 Azure 서버에 액세스할 때 사용된 IP 주소와 다릅니다. 따라서, 규칙 함수를 예상대로 만들기 위해 시작 IP 및 끝 IP를 변경해야 할 수도 있습니다.

   검색 엔진 또는 다른 온라인 도구를 사용하여 사용자 고유의 IP 주소를 확인합니다. 예를 들어 "내 IP 주소는 무엇입니까"를 검색합니다.

4. 추가 주소 범위를 추가합니다. Azure Database for MariaDB 방화벽 규칙에서 단일 IP 주소 또는 주소 범위를 지정할 수 있습니다. 하나의 단일 IP 주소로 규칙을 제한하려는 경우 시작 IP 및 끝 IP 필드에 동일한 주소를 입력합니다. 방화벽을 열면 관리자, 사용자 및 응용 프로그램에서 유효한 자격 증명이 있는 MariaDB 서버의 데이터베이스에 액세스할 수 있습니다.

   ![Azure Portal - 방화벽 규칙](./media/howto-manage-firewall-portal/4-specify-addresses.png)

5. 도구 모음에서 **저장**을 클릭하여 이 서버 수준 방화벽 규칙을 저장합니다. 방화벽 규칙에 대한 업데이트가 성공적으로 수행되었는지 확인될 때까지 기다립니다.

   ![Azure Portal - 저장 클릭](./media/howto-manage-firewall-portal/5-save-firewall-rule.png)

## <a name="connecting-from-azure"></a>Azure에서 연결
Azure의 응용 프로그램에서 Azure Database for MariaDB 서버에 연결할 수 있게 하려면 Azure 연결을 사용하도록 설정해야 합니다. 예를 들어 Azure Web Apps 애플리케이션 또는 Azure VM에서 실행되는 애플리케이션을 호스팅하거나 Azure Data Factory 데이터 관리 게이트웨이에서 연결하는 경우가 있습니다. 이러한 연결을 사용하도록 설정하려면 리소스가 방화벽 규칙에 대해 동일한 VNet(Virtual Network) 또는 리소스 그룹에 있을 필요가 없습니다. Azure의 애플리케이션이 데이터베이스 서버로 연결을 시도할 때, 방화벽은 Azure 연결이 허용되는지 확인합니다. 이러한 유형의 연결을 사용하도록 설정하는 몇 가지 방법이 있습니다. 0.0.0.0으로 된 시작 및 끝 주소가 포함된 방화벽 설정은 연결이 허용됨을 나타냅니다. 또는 포털의 **연결 보안** 창에서 **Azure 서비스 방문 허용** 옵션을 **설정**으로 지정하고 **저장**을 클릭합니다. 연결 시도가 허용되지 않으면 해당 요청이 Azure Database for MariaDB 서버에 도달하지 않습니다.

> [!IMPORTANT]
> 이 옵션은 다른 고객 구독에서의 연결을 포함하여 Azure에서의 모든 연결을 허용하도록 방화벽을 구성합니다. 이 옵션을 선택할 때 로그인 및 사용자 권한이 부여된 사용자만으로 액세스를 제한하는지 확인합니다.
> 

## <a name="manage-existing-firewall-rules-in-the-azure-portal"></a>Azure Portal에서 기존 방화벽 규칙을 관리합니다.
방화벽 규칙을 관리하는 단계를 반복합니다.
* 현재 컴퓨터를 추가하려면 **+ 내 IP 추가**를 클릭합니다. **저장**을 클릭하여 변경 내용을 저장합니다.
* 추가 IP 주소를 추가하려면 **규칙 이름**, **시작 IP** 및 **끝 IP**를 입력합니다. **저장**을 클릭하여 변경 내용을 저장합니다.
* 기존 규칙을 수정하려면 규칙의 필드 중 하나를 클릭한 후 수정합니다. **저장**을 클릭하여 변경 내용을 저장합니다.
* 기존 규칙을 삭제하려면 줄임표[...]를 클릭하고 **삭제**를 클릭합니다. **저장**을 클릭하여 변경 내용을 저장합니다.

## <a name="next-steps"></a>다음 단계
 - 마찬가지로를 읽을 수 있습니다 [만들기 및 Azure CLI를 사용 하 여 MariaDB 방화벽 규칙에 대 한 Azure Database를 관리](howto-manage-firewall-cli.md)합니다.
 - 서버에 대 한 액세스 보안을 강화할 [만들기 및 관리 가상 네트워크 서비스 끝점 및 Azure portal을 사용 하 여 규칙](howto-manage-vnet-portal.md)합니다.