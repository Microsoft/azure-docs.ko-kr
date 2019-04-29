---
title: Azure Portal을 사용한 Azure Database for MariaDB VNet 서비스 엔드포인트와 규칙 만들기 및 관리 | Microsoft Docs
description: Azure Portal을 사용한 Azure Database for MariaDB VNet 서비스 엔드포인트와 규칙 만들기 및 관리
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 6644b6ae3a9482a1bd3f840a814d3bb6361517fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60745919"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Database for MariaDB VNet 서비스 엔드포인트와 VNet 규칙 만들기 및 관리

VNet(Virtual Network) 서비스 엔드포인트 및 규칙이 Virtual Network의 개인 주소 공간을 Azure Database for MariaDB 서버로 확장합니다. 제한을 포함하여 Azure Database for MariaDB VNet 서비스 엔드포인트에 대한 개요는 [Azure Database for MariaDB 서버 VNet 서비스 엔드포인트](concepts-data-access-security-vnet.md)를 참조하세요. VNet 서비스 엔드포인트는 Azure Database for MariaDB에 대해 지원되는 모든 지역에서 사용할 수 있습니다.

> [!NOTE]
> VNet 서비스 엔드포인트는 범용 및 메모리 최적화 서버에 대해서만 지원됩니다.

## <a name="create-a-vnet-rule-and-enable-service-endpoints"></a>VNet 규칙을 만들고 서비스 엔드포인트를 사용하도록 설정

1. MariaDB 서버 페이지의 설정 제목에서 **연결 보안**을 클릭하여 Azure Database for MariaDB의 연결 보안 창을 엽니다.

2. Azure 서비스 컨트롤에 액세스 허용으로 설정 되어 있는지 확인 하십시오 **OFF**합니다.

> [!Important]
> ON으로 설정한 경우 Azure MariaDB 데이터베이스 서버는 모든 서브넷 으로부터의 통신을 허용 합니다. 제어 집합을 ON으로 유지하면 보안 관점에서 과도하게 액세스할 수도 있습니다. 함께 협력 하 여 가상 네트워크 규칙 기능을 사용 하 여 Azure database for MariaDB, Microsoft Azure Virtual Network 서비스 엔드포인트 기능, 보안 영역을 줄일 수 있습니다.

3. 다음으로, **+ 기존 가상 네트워크 추가**를 클릭합니다. 기존 VNet이 없는 경우 **+ 새 가상 네트워크 만들기**를 클릭할 수 있습니다. [빠른 시작: Azure Portal을 사용하여 가상 네트워크 만들기](../virtual-network/quick-create-portal.md)

   ![Azure Portal - 보안 연결 클릭](./media/howto-manage-vnet-portal/1-connection-security.png)

4. VNet 규칙 이름을 입력하고 구독, 가상 네트워크 및 서브넷 이름을 선택한 다음, **사용**을 클릭합니다. **Microsoft.SQL** 서비스 태그를 사용하여 서브넷에서 VNet 서비스 엔드포인트를 자동으로 사용하도록 설정할 수 있습니다.

   ![Azure Portal - VNet 구성](./media/howto-manage-vnet-portal/2-configure-vnet.png)

   계정에는 가상 네트워크 및 서비스 엔드포인트를 만드는 데 필요한 사용 권한이 있어야 합니다.

   가상 네트워크에 대한 쓰기 액세스 권한이 있는 사용자는 가상 네트워크에서 독립적으로 서비스 엔드포인트를 구성할 수 있습니다.
    
   VNet에 대한 Azure 서비스 리소스를 보호하려면 사용자는 추가되는 서브넷의 "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/"에 대한 사용 권한을 갖고 있어야 합니다. 이 권한은 기본적으로 기본 제공 서비스 관리자 역할에 포함되고 사용자 지정 역할을 만들어서 수정될 수 있습니다.
    
   [기본 제공 역할](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) 및 [사용자 지정 역할](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles)에 특정 권한 할당에 대해 자세히 알아보세요.
    
   VNet 및 Azure 서비스 리소스가 동일한 구독이나 다른 구독에 있을 수 있습니다. VNet 및 Azure 서비스 리소스가 서로 다른 구독에 있는 경우 리소스가 동일한 AD(Active Directory) 테넌트에 있어야 합니다.

   > [!IMPORTANT]
   > 서비스 엔드포인트를 구성하기 전에 서비스 엔드포인트 및 고려 사항에 대한 이 문서를 읽어보는 것이 매우 좋습니다. **Virtual Network 서비스 엔드포인트:** [Virtual Network 서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)는 속성 값에 하나 이상의 정식 Azure 서비스 유형 이름이 포함된 서브넷입니다. VNet 서비스 엔드포인트는 SQL Database라는 Azure 서비스를 나타내는 서비스 형식 이름 **Microsoft.Sql**을 사용합니다. 이 서비스 태그는 Azure SQL Database, Azure Database for MariaDB, PostgreSQL 및 MySQL 서비스에도 적용됩니다. **Microsoft.Sql** 서비스 태그를 VNet 서비스 엔드포인트에 적용하는 경우 Azure SQL Database, Azure Database for PostgreSQL, Azure Database for MariaDB 및 Azure Database for MySQL 서버를 포함하는 모든 Azure Database 서비스의 서비스 엔드포인트 트래픽을 서브넷에서 구성합니다.
   > 

5. 활성화되고 **확인**을 클릭하면 VNet 서비스 엔드포인트가 VNet 규칙에 따라 사용하도록 설정되는 것을 확인할 수 있습니다.

   ![사용하도록 설정된 VNet 서비스 엔드포인트 및 만든 VNet 규칙](./media/howto-manage-vnet-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>다음 단계
- [Azure Database for MariaDB에서 SSL을 구성하는 방법](howto-configure-ssl.md)에 대해 자세히 알아보기
- 마찬가지로 [Azure CLI를 사용하여 Azure Database for MariaDB에 대한 VNET 규칙을 만들고 VNet 서비스 엔드포인트를 사용](howto-manage-vnet-cli.md)하도록 스크립팅할 수 있습니다.
