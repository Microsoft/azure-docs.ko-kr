---
title: SQL Fqdn을 사용 하 여 Azure 방화벽 응용 프로그램 규칙 구성
description: 이 문서에서는 Azure 방화벽 응용 프로그램 규칙에서 SQL Fqdn을 구성 하는 방법에 대해 알아봅니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 07/19/2019
ms.author: victorh
ms.openlocfilehash: 239998f29ac9a578174c5dba547bb24ba0755505
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68318179"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>SQL Fqdn을 사용 하 여 Azure 방화벽 응용 프로그램 규칙 구성

> [!IMPORTANT]
> SQL Fqdn을 사용 하는 Azure 방화벽 응용 프로그램 규칙은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이제 SQL Fqdn을 사용 하 여 Azure 방화벽 응용 프로그램 규칙을 구성할 수 있습니다. 이렇게 하면 가상 네트워크에서 지정 된 SQL server 인스턴스로만 액세스를 제한할 수 있습니다.

SQL Fqdn을 사용 하 여 트래픽을 필터링 할 수 있습니다.

- Vnet에서 Azure SQL Database 또는 Azure SQL Data Warehouse. 예: *Sql-server1.database.windows.net*에 대한 액세스만 허용 합니다.
- 온-프레미스에서 Azure SQL 관리 되는 인스턴스 또는 Vnet에서 실행 되는 SQL IaaS.
- 스포크-스포크에서 Azure SQL 관리 되는 인스턴스 또는 Vnet에서 실행 되는 SQL IaaS로.

공개 미리 보기 동안에는 SQL FQDN 필터링이 [프록시 모드](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy) 에서만 지원 됩니다 (포트 1433). 기본 리디렉션 모드에서 SQL을 사용 하는 경우 [네트워크 규칙](overview.md#network-traffic-filtering-rules)의 일부로 sql 서비스 태그를 사용 하 여 액세스를 필터링 할 수 있습니다.
SQL IaaS 트래픽에 기본 포트가 아닌 포트를 사용 하는 경우 방화벽 응용 프로그램 규칙에서 이러한 포트를 구성할 수 있습니다.

SQL Fqdn을 사용 하는 응용 프로그램 규칙은 현재 Azure Portal, Azure CLI, REST 및 템플릿을 통해 모든 지역에서 사용할 수 있습니다.

## <a name="configure-using-azure-cli"></a>Azure CLI를 사용 하 여 구성

1. Azure CLI를 [사용 하 여 Azure 방화벽](deploy-cli.md)을 배포 합니다.
2. Azure SQL Database, SQL Data Warehouse 또는 SQL Managed Instance 트래픽을 필터링 하는 경우 SQL 연결 모드가 **프록시**로 설정 되었는지 확인 합니다. SQL 연결 모드를 전환 하는 방법에 대한 자세한 내용은 [AZURE Sql 연결 아키텍처](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#change-azure-sql-database-connection-policy)를 참조 하세요. 

   > [!NOTE]
   > SQL *프록시* 모드를 사용할 경우 *리디렉션*에 비해 대기 시간이 길어질 수 있습니다. Azure 내에서 연결 하는 클라이언트에 대한 기본값 인 리디렉션 모드를 계속 사용 하려는 경우 방화벽 [네트워크 규칙](tutorial-firewall-deploy-portal.md#configure-a-network-rule)의 SQL [서비스 태그](service-tags.md) 를 사용 하 여 액세스를 필터링 할 수 있습니다.

3. Sql server에 대한 액세스를 허용 하도록 SQL FQDN을 사용 하 여 응용 프로그램 규칙 구성:

   ```azurecli
   az extension add -n azure-firewall

   az network firewall application-rule create \
   -g FWRG \
   -f azfirewall \
   -c FWAppRules \
   -n srule \
   --protocols mssql=1433 \
   --source-addresses 10.0.0.0/24 \
   --target-fqdns sql-serv1.database.windows.net
   ```

## <a name="configure-using-the-azure-portal"></a>Azure Portal 사용 하 여 구성
1. Azure CLI를 [사용 하 여 Azure 방화벽](deploy-cli.md)을 배포 합니다.
2. Azure SQL Database, SQL Data Warehouse 또는 SQL Managed Instance 트래픽을 필터링 하는 경우 SQL 연결 모드가 **프록시**로 설정 되었는지 확인 합니다. SQL 연결 모드를 전환 하는 방법에 대한 자세한 내용은 [AZURE Sql 연결 아키텍처](../sql-database/sql-database-connectivity-architecture.md#change-azure-sql-database-connection-policy)를 참조 하세요. 

   > [!NOTE]
   > SQL *프록시* 모드를 사용할 경우 *리디렉션*에 비해 대기 시간이 길어질 수 있습니다. Azure 내에서 연결 하는 클라이언트에 대한 기본값 인 리디렉션 모드를 계속 사용 하려는 경우 방화벽 [네트워크 규칙](tutorial-firewall-deploy-portal.md#configure-a-network-rule)의 SQL [서비스 태그](service-tags.md) 를 사용 하 여 액세스를 필터링 할 수 있습니다.
3. 적절 한 프로토콜, 포트 및 SQL FQDN을 사용 하 여 응용 프로그램 규칙을 추가 하 고 **저장**을 선택 합니다.
   ![SQL FQDN을 사용 하는 응용 프로그램 규칙](media/sql-fqdn-filtering/application-rule-sql.png)
4. 방화벽을 통해 트래픽을 필터링 하는 VNet의 가상 머신에서 SQL에 액세스 합니다. 
5. [Azure 방화벽 로그](log-analytics-samples.md) 에서 트래픽이 허용 되는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

SQL 프록시 및 리디렉션 모드에 대해 알아보려면 [AZURE sql database 연결 아키텍처](../sql-database/sql-database-connectivity-architecture.md)를 참조 하세요.