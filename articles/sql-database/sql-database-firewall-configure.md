---
title: IP 방화벽 규칙
description: SQL database 또는 SQL Data Warehouse 방화벽에 대 한 서버 수준 IP 방화벽 규칙을 구성 합니다. 단일 또는 풀링된 데이터베이스에 대 한 액세스를 관리 하 고 데이터베이스 수준 IP 방화벽 규칙을 구성 합니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 9db6b5ff517a1b0d67e59591ee634dfad685527b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75461458"
---
# <a name="azure-sql-database-and-azure-sql-data-warehouse-ip-firewall-rules"></a>IP 방화벽 규칙 Azure SQL Database 및 Azure SQL Data Warehouse

> [!NOTE]
> 이 문서는 azure sql 서버에 적용 되며, Azure SQL server의 Azure SQL Database 및 Azure SQL Data Warehouse 데이터베이스에 적용 됩니다. 간단히 하기 위해 *SQL Database* 를 사용 하 여 SQL Database 및 SQL Data Warehouse를 모두 참조 합니다.

> [!IMPORTANT]
> 이 문서는 *Azure SQL Database Managed Instance*에 적용되지 *않습니다*. 네트워크 구성에 대 한 자세한 내용은 [Azure SQL Database Managed Instance에 응용 프로그램 연결](sql-database-managed-instance-connect-app.md)을 참조 하세요.

예를 들어 이름이 *mysqlserver*인 새 Azure SQL server를 만들 때 SQL Database 방화벽은 서버에 대 한 공용 엔드포인트 ( *mysqlserver.database.windows.net*에서 액세스할 수 있음)에 대 한 모든 액세스를 차단 합니다.

> [!IMPORTANT]
> SQL Data Warehouse는 서버 수준 IP 방화벽 규칙만 지원 합니다. 데이터베이스 수준 IP 방화벽 규칙은 지원 하지 않습니다.

## <a name="how-the-firewall-works"></a>방화벽 작동 방법
다음 다이어그램과 같이 인터넷 및 Azure의 연결 시도는 SQL server 또는 SQL database에 연결 하기 전에 방화벽을 통과 해야 합니다.

   ![방화벽 구성 다이어그램][1]

### <a name="server-level-ip-firewall-rules"></a>서버 수준 IP 방화벽 규칙

  클라이언트는 이 규칙에 따라 전체 Azure SQL Server, 즉 동일한 SQL Database 서버 내의 모든 데이터베이스에 액세스할 수 있습니다. 규칙은 *master* 데이터베이스에 저장 됩니다. Azure SQL Server에 대해 최대 128의 서버 수준 IP 방화벽 규칙을 사용할 수 있습니다.
  
  Azure Portal, PowerShell 또는 Transact-sql 문을 사용 하 여 서버 수준 IP 방화벽 규칙을 구성할 수 있습니다.
  - 포털 또는 PowerShell을 사용 하려면 구독 소유자 또는 구독 참가자 여야 합니다.
  - Transact-sql을 사용 하려면 서버 수준 보안 주체 로그인 또는 Azure Active Directory 관리자로 SQL Database 인스턴스에 연결 해야 합니다. 먼저 Azure 수준 사용 권한이 있는 사용자가 서버 수준 IP 방화벽 규칙을 만들어야 합니다.

### <a name="database-level-ip-firewall-rules"></a>데이터베이스 수준 IP 방화벽 규칙

  클라이언트는 이 규칙에 따라 동일한 SQL Database 서버 내의 특정(보안) 데이터베이스에 액세스할 수 있습니다. 각 데이터베이스 ( *master* 데이터베이스 포함)에 대 한 규칙을 만들어 개별 데이터베이스에 저장 합니다.
  
  첫 번째 서버 수준 방화벽을 구성한 후에만 Transact-sql 문을 사용 하 여 master 및 사용자 데이터베이스에 대 한 데이터베이스 수준 IP 방화벽 규칙을 만들고 관리할 수 있습니다.
  
  서버 수준 IP 방화벽 규칙의 범위를 벗어나는 데이터베이스 수준 IP 방화벽 규칙의 IP 주소 범위를 지정 하면 데이터베이스 수준 범위에서 IP 주소가 있는 클라이언트만 데이터베이스에 액세스할 수 있습니다.
  
  데이터베이스에 대해 최대 128개의 데이터베이스 수준 IP 방화벽 규칙을 가질 수 있습니다. 데이터베이스 수준 IP 방화벽 규칙을 구성 하는 방법에 대 한 자세한 내용은이 문서의 뒷부분에 나오는 예제를 참조 하 고 [sp_set_database_firewall_rule (Azure SQL Database)](https://msdn.microsoft.com/library/dn270010.aspx)를 참조 하세요.

### <a name="recommendations-for-how-to-set-firewall-rules"></a>방화벽 규칙을 설정 하는 방법에 대 한 권장 사항

가능 하면 항상 데이터베이스 수준 IP 방화벽 규칙을 사용 하는 것이 좋습니다. 이 방법을 사용 하면 보안을 강화 하 고 데이터베이스의 이식성을 높일 수 있습니다. 관리자에 대해 서버 수준 IP 방화벽 규칙을 사용 합니다. 동일한 액세스 요구 사항을 가진 데이터베이스가 많고 각 데이터베이스를 개별적으로 구성 하지 않으려는 경우에도이를 사용 합니다.

> [!NOTE]
> 비즈니스 연속성의 맥락에서 휴대용 데이터베이스에 대한 자세한 내용은 [재해 복구를 위한 인증 요구 사항](sql-database-geo-replication-security-config.md)을 참조하세요.

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>서버 수준 및 데이터베이스 수준 IP 방화벽 규칙 비교

*한 데이터베이스의 사용자를 다른 데이터베이스에서 완전히 격리 해야 하나요?*

그렇다면 *데이터베이스*수준 IP 방화벽 규칙을 사용 하 여 액세스 권한을 부여 합니다. 이 방법은 모든 데이터베이스에 대 한 방화벽을 통한 액세스를 허용 하는 서버 수준 IP 방화벽 규칙을 사용 하지 않습니다. 그러면 방어 수준이 줄어듭니다.

*IP 주소에서 사용자가 모든 데이터베이스에 액세스 해야 하나요?*

그렇다면 *서버*수준 ip 방화벽 규칙을 사용 하 여 ip 방화벽 규칙을 구성 해야 하는 횟수를 줄입니다.

*IP 방화벽 규칙을 구성 하는 개인 이나 팀은 Azure Portal, PowerShell 또는 REST API을 통해서만 액세스할 수 있나요?*

이 경우 서버 수준 IP 방화벽 규칙을 사용 해야 합니다. 데이터베이스 수준 IP 방화벽 규칙은 Transact-sql을 통해서만 구성할 수 있습니다.  

*IP 방화벽 규칙을 구성 하는 개인 이나 팀이 데이터베이스 수준에서 높은 수준의 사용 권한을 갖지 못하도록 허용 되나요?*

그렇다면 서버 수준 IP 방화벽 규칙을 사용 합니다. Transact-sql을 통해 데이터베이스 수준 IP 방화벽 규칙을 구성 하려면 데이터베이스 수준에서 최소 *제어 데이터베이스* 권한이 있어야 합니다.  

*IP 방화벽 규칙을 구성 하거나 감사 하는 개인 이나 팀이 수천 개의 데이터베이스에 대 한 IP 방화벽 규칙을 중앙에서 관리 하나요?*

이 시나리오에서 모범 사례는 요구 사항 및 환경에 따라 결정 됩니다. 서버 수준 IP 방화벽 규칙이 구성하기 더 쉬울 수 있지만 스크립팅은 데이터베이스 수준에서 규칙을 구성할 수 있습니다. 서버 수준 ip 방화벽 규칙을 사용 하는 경우에도 데이터베이스에 대 한 *CONTROL* 권한이 있는 사용자가 데이터베이스 수준 ip 방화벽 규칙을 만들 수 있는지 확인 하기 위해 데이터베이스 수준 ip 방화벽 규칙을 감사 해야 할 수 있습니다.

*서버 수준 및 데이터베이스 수준 IP 방화벽 규칙을 함께 사용할 수 있나요?*

예. 관리자와 같은 일부 사용자에 게는 서버 수준 IP 방화벽 규칙이 필요할 수 있습니다. 데이터베이스 애플리케이션 사용자와 같은 경우는 데이터베이스 수준 IP 방화벽 규칙이 필요할 수 있습니다.

### <a name="connections-from-the-internet"></a>인터넷에서 연결

컴퓨터에서 인터넷을 통해 데이터베이스 서버에 연결 하려고 하면 먼저 방화벽은 연결에서 요청 하는 데이터베이스에 대 한 데이터베이스 수준 IP 방화벽 규칙에 대해 요청의 원래 IP 주소를 확인 합니다.

- 주소가 데이터베이스 수준 IP 방화벽 규칙에 지정 된 범위 내에 있는 경우 해당 규칙을 포함 하는 SQL database에 대 한 연결이 부여 됩니다.
- 주소가 데이터베이스 수준 IP 방화벽 규칙의 범위 내에 없는 경우 방화벽은 서버 수준 IP 방화벽 규칙을 확인 합니다. 주소가 서버 수준 IP 방화벽 규칙의 범위 내에 있는 경우 연결이 허용 됩니다. 서버 수준 IP 방화벽 규칙은 Azure SQL Server에 있는 모든 SQL Database에 적용됩니다.  
- 주소가 데이터베이스 수준 또는 서버 수준 IP 방화벽 규칙의 범위에 포함 되지 않은 경우 연결 요청이 실패 합니다.

> [!NOTE]
> 로컬 컴퓨터에서 SQL Database에 액세스 하려면 네트워크와 로컬 컴퓨터의 방화벽이 TCP 포트 1433에서 나가는 통신을 허용 하는지 확인 합니다.

### <a name="connections-from-inside-azure"></a>Azure 내부에서 연결

Azure 내에서 호스트 되는 응용 프로그램이 SQL server에 연결할 수 있도록 하려면 Azure 연결을 사용 하도록 설정 해야 합니다. Azure의 응용 프로그램이 데이터베이스 서버에 연결 하려고 하면 방화벽은 Azure 연결이 허용 되는지 확인 합니다. 시작 및 끝 IP 주소가 *0.0.0.0* 과 같은 방화벽 설정은 Azure 연결이 허용 됨을 나타냅니다. 방화벽 규칙을 설정 하 고 방화벽 **및 가상 네트워크** 설정에서 **이 서버에 액세스할 수 있는 azure 서비스 및 리소스** 를 **전환 하는** 것을 비롯 하 여 azure Portal 블레이드에서 직접 설정할 수 있습니다. 연결이 허용 되지 않으면 요청이 SQL Database 서버에 도달 하지 않습니다.

> [!IMPORTANT]
> 이 옵션은 다른 고객의 구독에서 연결을 포함 하 여 Azure의 모든 연결을 허용 하도록 방화벽을 구성 합니다. 이 옵션을 선택 하는 경우 로그인 및 사용자 권한이 권한 있는 사용자만 액세스할 수 있도록 제한 해야 합니다.

## <a name="create-and-manage-ip-firewall-rules"></a>IP 방화벽 규칙 만들기 및 관리

[Azure Portal](https://portal.azure.com/) 를 사용 하거나 [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.sql), [Azure CLI](https://docs.microsoft.com/cli/azure/sql/server/firewall-rule)또는 Azure [REST API](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate)를 사용 하 여 프로그래밍 방식으로 첫 번째 서버 수준 방화벽 설정을 만듭니다. 이러한 메서드 또는 Transact-sql을 사용 하 여 추가 서버 수준 IP 방화벽 규칙을 만들고 관리 합니다.

> [!IMPORTANT]
> Transact-sql을 사용 하 여 데이터베이스 수준 IP 방화벽 규칙을 만들고 관리할 수 있습니다.

성능 향상을 위해 서버 수준 IP 방화벽 규칙이 데이터베이스 수준에서 일시적으로 캐시됩니다. 캐시를 새로 고치려면 [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx)를 참조하세요.

> [!TIP]
> [SQL Database 감사](sql-database-auditing.md)를 사용하여 서버 수준 및 데이터베이스 수준의 방화벽 변경 내용을 감사할 수 있습니다.

### <a name="use-the-azure-portal-to-manage-server-level-ip-firewall-rules"></a>Azure Portal를 사용 하 여 서버 수준 IP 방화벽 규칙 관리

Azure Portal에서 서버 수준 IP 방화벽 규칙을 설정 하려면 Azure SQL database 또는 SQL Database 서버에 대 한 개요 페이지로 이동 합니다.

> [!TIP]
> 자습서는 [Azure Portal을 사용하여 DB 만들기](sql-database-single-database-get-started.md)를 참조하세요.

#### <a name="from-the-database-overview-page"></a>데이터베이스 개요 페이지에서

1. 데이터베이스 개요 페이지에서 서버 수준 IP 방화벽 규칙을 설정 하려면 다음 그림과 같이 도구 모음에서 **서버 방화벽 설정** 을 선택 합니다. 

    ![서버 IP 방화벽 규칙](./media/sql-database-get-started-portal/sql-database-server-set-firewall-rule.png)

    SQL Database 서버에 대한 **방화벽 설정** 페이지가 열립니다.

2. 도구 모음에서 **클라이언트 Ip 추가** 를 선택 하 여 사용 중인 컴퓨터의 IP 주소를 추가 하 고 **저장**을 선택 합니다. 현재 IP 주소에 대한 서버 수준 IP 방화벽 규칙이 생성됩니다.

    ![서버 수준 IP 방화벽 규칙 설정](./media/sql-database-get-started-portal/sql-database-server-firewall-settings.png)

#### <a name="from-the-server-overview-page"></a>서버 개요 페이지에서

서버에 대 한 개요 페이지가 열립니다. 정규화 된 서버 이름 (예: *mynewserver20170403.database.windows.net*)을 표시 하 고 추가 구성을 위한 옵션을 제공 합니다.

1. 이 페이지에서 서버 수준 규칙을 설정 하려면 왼쪽의 **설정** 메뉴에서 **방화벽** 을 선택 합니다.

2. 도구 모음에서 **클라이언트 Ip 추가** 를 선택 하 여 사용 중인 컴퓨터의 IP 주소를 추가 하 고 **저장**을 선택 합니다. 현재 IP 주소에 대한 서버 수준 IP 방화벽 규칙이 생성됩니다.

### <a name="use-transact-sql-to-manage-ip-firewall-rules"></a>Transact-sql을 사용 하 여 IP 방화벽 규칙 관리

| 카탈로그 뷰 또는 저장 프로시저 | 수준 | Description |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |서버 |현재 서버 수준 IP 방화벽 규칙을 표시합니다. |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |서버 |서버 수준 IP 방화벽 규칙을 생성 및 업데이트합니다. |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |서버 |서버 수준 IP 방화벽 규칙을 제거합니다. |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |데이터베이스 |현재 데이터베이스 수준 IP 방화벽 규칙을 표시합니다. |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |데이터베이스 |데이터베이스 수준 IP 방화벽 규칙을 생성 및 업데이트합니다. |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |데이터베이스 |데이터베이스 수준 IP 방화벽 규칙을 제거합니다. |

다음 예에서는 기존 규칙을 검토 하 고, *Contoso*서버에서 ip 주소 범위를 사용 하도록 설정 하 고, ip 방화벽 규칙을 삭제 합니다.

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

다음으로 서버 수준 IP 방화벽 규칙을 추가합니다.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

서버 수준 IP 방화벽 규칙을 삭제 하려면 *sp_delete_firewall_rule* 저장 프로시저를 실행 합니다. 다음 예에서는 *에서는 contosofirewallrule*규칙을 삭제 합니다.

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

### <a name="use-powershell-to-manage-server-level-ip-firewall-rules"></a>PowerShell을 사용 하 여 서버 수준 IP 방화벽 규칙 관리 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager 모듈은 Azure SQL Database에서 계속 지원 되지만 이제 Az. Sql 모듈에 대 한 모든 개발이 지원 됩니다. 이러한 cmdlet에 대한 내용은 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)을 참조합니다. Az 및 AzureRm 모듈의 명령에 대 한 인수는 실질적으로 동일 합니다.

| Cmdlet | 수준 | Description |
| --- | --- | --- |
| [Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |서버 |현재 서버 수준 방화벽 규칙 반환 |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |서버 |새 서버 수준 방화벽 규칙 만들기 |
| [Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |서버 |기존 서버 수준 방화벽 규칙 속성 업데이트 |
| [Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |서버 |서버 수준 방화벽 규칙 제거 |

다음 예제에서는 PowerShell을 사용 하 여 서버 수준 IP 방화벽 규칙을 설정 합니다.

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "ContosoIPRange" -StartIpAddress "192.168.1.0" -EndIpAddress "192.168.1.255"
```
> [!TIP]
> $Servername에 대해 정규화 된 DNS 이름이 아닌 서버 이름을 지정 합니다. 예를 들어 **mysqldbserver.database.windows.net** 대신 **mysqldbserver** 를 지정 합니다.

> [!TIP]
> 빠른 시작의 컨텍스트에서 PowerShell 예제는 PowerShell을 사용 하 여 [Db-library 만들기](sql-database-powershell-samples.md) 및 [단일 데이터베이스 만들기 및 SQL DATABASE 서버 수준 IP 방화벽 규칙 구성](scripts/sql-database-create-and-configure-database-powershell.md)을 참조 하세요.

### <a name="use-cli-to-manage-server-level-ip-firewall-rules"></a>CLI를 사용 하 여 서버 수준 IP 방화벽 규칙 관리

| Cmdlet | 수준 | Description |
| --- | --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|서버|서버 IP 방화벽 규칙을 만듭니다.|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|서버|서버의 IP 방화벽 규칙을 나열합니다.|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|서버|IP 방화벽 규칙의 세부 정보를 표시 합니다.|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|서버|IP 방화벽 규칙을 업데이트 합니다.|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|서버|IP 방화벽 규칙을 삭제 합니다.|

다음 예제에서는 CLI를 사용 하 여 서버 수준 IP 방화벽 규칙을 설정 합니다.

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n ContosoIPRange --start-ip-address 192.168.1.0 --end-ip-address 192.168.1.255
```
> [!TIP]
> $Servername에 대해 정규화 된 DNS 이름이 아닌 서버 이름을 지정 합니다. 예를 들어 **mysqldbserver.database.windows.net** 대신 **mysqldbserver** 를 지정 합니다.

> [!TIP]
> 빠른 시작의 컨텍스트에서 CLI 예제를 보려면 [DB Azure CLI 만들기](sql-database-cli-samples.md) 및 [단일 데이터베이스 만들기를 참조 하 고 Azure CLI를 사용 하 여 SQL Database IP 방화벽 규칙을 구성](scripts/sql-database-create-and-configure-database-cli.md)합니다.

### <a name="use-a-rest-api-to-manage-server-level-ip-firewall-rules"></a>REST API를 사용 하 여 서버 수준 IP 방화벽 규칙 관리

| API | 수준 | Description |
| --- | --- | --- |
| [방화벽 규칙 나열](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver) |서버 |현재 서버 수준 IP 방화벽 규칙을 표시합니다. |
| [방화벽 규칙 만들기 또는 업데이트](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) |서버 |서버 수준 IP 방화벽 규칙을 생성 및 업데이트합니다. |
| [방화벽 규칙 삭제](https://docs.microsoft.com/rest/api/sql/firewallrules/delete) |서버 |서버 수준 IP 방화벽 규칙을 제거합니다. |
| [방화벽 규칙 가져오기](https://docs.microsoft.com/rest/api/sql/firewallrules/get) | 서버 | 서버 수준 IP 방화벽 규칙을 가져옵니다. |

## <a name="troubleshoot-the-database-firewall"></a>데이터베이스 방화벽 문제 해결

SQL Database 서비스에 대 한 액세스가 예상과 다르게 작동 하는 경우 다음 사항을 고려 하세요.

- **로컬 방화벽 구성:**

  컴퓨터에서 SQL Database에 액세스할 수 있으려면 먼저 컴퓨터에서 TCP 포트 1433에 대 한 방화벽 예외를 만들어야 할 수 있습니다. Azure 클라우드 경계 내에서 연결을 만들려면 추가 포트를 열어야 할 수도 있습니다. 자세한 내용은 [ADO.NET 4.5 및 SQL Database에 대해 1433를 초과](sql-database-develop-direct-route-ports-adonet-v12.md)하는 포트의 "SQL Database: 외부 vs 내부" 섹션을 참조 하세요.

- **네트워크 주소 변환:**

  NAT (network address translation)로 인해 컴퓨터에서 SQL Database에 연결 하는 데 사용 하는 IP 주소가 컴퓨터의 IP 구성 설정에 있는 IP 주소와 다를 수 있습니다. 컴퓨터에서 Azure에 연결 하는 데 사용 하는 IP 주소를 보려면 다음을 수행 합니다.
    1. 포털에 로그인합니다.
    1. 데이터베이스를 호스트 하는 서버에서 **구성** 탭으로 이동 합니다.
    1. **현재 클라이언트 Ip 주소** 는 **허용 된 ip 주소** 섹션에 표시 됩니다. **허용 된 IP 주소** 에 대해 **추가** 를 선택 하 여이 컴퓨터에서 서버에 액세스할 수 있도록 합니다.

- **허용 목록에 대 한 변경 내용이 아직 적용 되지 않았습니다.**

  SQL Database 방화벽 구성에 대 한 변경 내용이 적용 되려면 최대 5 분 정도 걸릴 수 있습니다.

- **로그인에 권한이 없거나 잘못 된 암호가 사용 되었습니다.**

  로그인에 SQL Database 서버에 대 한 권한이 없거나 암호가 올바르지 않은 경우 서버에 대 한 연결이 거부 됩니다. 방화벽 설정을 만들면 클라이언트에서 서버에 연결할 수 있는 *기회가* 제공 됩니다. 클라이언트는 여전히 필요한 보안 자격 증명을 제공 해야 합니다. 로그인 준비에 대 한 자세한 내용은 [SQL Database 및 SQL Data Warehouse에 대 한 데이터베이스 액세스 제어 및 권한 부여](sql-database-manage-logins.md)를 참조 하세요.

- **동적 IP 주소:**

  동적 IP 주소 지정을 사용 하는 인터넷 연결이 있고 방화벽을 통과 하는 데 문제가 있는 경우 다음 해결 방법 중 하나를 시도해 보세요.
  
  - 인터넷 서비스 공급자에 게 SQL Database 서버에 액세스 하는 클라이언트 컴퓨터에 할당 된 IP 주소 범위를 요청 합니다. Ip 주소 범위를 IP 방화벽 규칙으로 추가 합니다.
  - 클라이언트 컴퓨터에 대 한 고정 IP 주소 지정을 대신 가져옵니다. Ip 주소를 IP 방화벽 규칙으로 추가 합니다.

## <a name="next-steps"></a>다음 단계

- 회사 네트워크 환경에서 Azure 데이터 센터에서 사용 하는 계산 IP 주소 범위 (SQL 범위 포함)에서 인바운드 통신을 허용 하는지 확인 합니다. 이러한 IP 주소를 허용 목록에 추가 해야 할 수도 있습니다. [Microsoft Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)를 참조 하세요.  
- 서버 수준 IP 방화벽 규칙을 만드는 방법에 대 한 빠른 시작은 [AZURE SQL 데이터베이스 만들기](sql-database-single-database-get-started.md)를 참조 하세요.
- 오픈 소스 또는 타사 응용 프로그램에서 Azure SQL database에 연결 하는 데 도움이 필요한 경우 [클라이언트 빠른 시작 코드 샘플](https://msdn.microsoft.com/library/azure/ee336282.aspx)을 참조 하 여 SQL Database 합니다.
- 열어야 할 수 있는 추가 포트에 대 한 자세한 내용은 [ADO.NET 4.5 및 SQL Database에 대해 1433를 초과](sql-database-develop-direct-route-ports-adonet-v12.md) 하는 포트의 "SQL Database: 외부 vs 내부" 섹션을 참조 하세요.
- Azure SQL Database 보안에 대 한 개요는 [데이터베이스 보안](sql-database-security-overview.md)설정을 참조 하세요.

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
