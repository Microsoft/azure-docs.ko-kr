---
title: 네트워크 액세스 컨트롤
description: 액세스를 관리 하 고 단일 또는 풀링된 데이터베이스를 구성 하기 위한 Azure SQL Database 및 데이터 웨어하우스의 네트워크 액세스 제어 개요입니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto
ms.date: 08/05/2019
ms.openlocfilehash: 16ba90aab52c00f77af590f854217cd989df53b3
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251909"
---
# <a name="azure-sql-database-and-data-warehouse-network-access-controls"></a>Azure SQL Database 및 데이터 웨어하우스 네트워크 액세스 제어

> [!NOTE]
> 이 문서는 Azure SQL 서버 및 Azure SQL 서버에서 생성된 SQL Database와 SQL Data Warehouse 데이터베이스에 적용됩니다. 간단히 하기 위해 SQL Database는 SQL Database와 SQL Data Warehouse를 참조할 때 사용됩니다.

> [!IMPORTANT]
> 이 문서는 *Azure SQL Database Managed Instance*에 적용되지 **않습니다**. 네트워킹 구성에 대 한 자세한 내용은 [Managed Instance에 연결](sql-database-managed-instance-connect-app.md) 을 참조 하세요.

[Azure Portal에서](sql-database-single-database-get-started.md)새 Azure SQL Server를 만들 때 결과는 *yourservername.database.windows.net*형식의 공용 끝점입니다. 설계상 공용 끝점에 대 한 모든 액세스는 거부 됩니다. 그런 다음, 다음 네트워크 액세스 제어를 사용 하 여 공용 끝점을 통해 SQl Database에 대 한 액세스를 선택적으로 허용할 수 있습니다.
- Azure 서비스 허용: 사용으로 설정 되 면 azure 경계 내의 다른 리소스 (예: Azure Virtual Machine)는에 액세스할 수 있습니다 SQL Database

- IP 방화벽 규칙:-이 기능을 사용 하 여 온-프레미스 컴퓨터 등 특정 IP 주소에서의 연결을 명시적으로 허용 합니다.

- Virtual Network 방화벽 규칙:-이 기능을 사용 하 여 Azure 경계 내에서 특정 Virtual Network의 트래픽을 허용 합니다.

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--SQL-Database-Connectivity-Explained/player?WT.mc_id=dataexposed-c9-niner]

## <a name="allow-azure-services"></a>Azure 서비스 허용 
[Azure Portal에서](sql-database-single-database-get-started.md)새 Azure SQL Server를 만드는 동안이 설정은 선택 하지 않은 상태로 유지 됩니다.

 ![새 서버 만들기의 스크린샷][1]

다음과 같이 Azure SQL Server를 만든 후 방화벽 창을 통해이 설정을 변경할 수도 있습니다.
  
 ![서버 방화벽 관리 스크린샷][2]

Azure SQL Server **에서** 로 설정 하면 azure 경계 내의 모든 리소스에서 통신할 수 있으며,이는 구독의 일부가 될 수도 있고 그렇지 않을 수도 있습니다.

대부분의 경우에는 대부분의 고객이 원하는 것 보다는 **ON** 설정이 더 허용 됩니다. 이 설정을 **OFF** 로 설정 하 고 더 제한적인 IP 방화벽 규칙 또는 Virtual Network 방화벽 규칙으로 대체 하는 것이 좋습니다. 이렇게 하면 VNet의 일부가 아닌 Azure의 Vm에서 실행 되는 다음 기능에 영향을 주므로 Azure IP 주소를 통해 Sql Database에 연결 합니다.

### <a name="import-export-service"></a>가져오기/내보내기 서비스
가져오기/내보내기 서비스를 사용 **하면 Azure 서비스에서 서버를** OFF로 설정 하 여 액세스할 수 있습니다. 그러나 [AZURE VM에서 sqlpackage를 수동으로 실행 하거나](https://docs.microsoft.com/azure/sql-database/import-export-from-vm) DACFx API를 사용 하 여 코드에서 직접 내보내기를 수행 하 여 문제를 해결할 수 있습니다.

### <a name="data-sync"></a>데이터 동기화
**Azure 서비스가 서버에 액세스할 수 있도록 허용** 하는 데이터 동기화 기능을 사용 하려면 **허브** 데이터베이스를 호스트 하는 지역에 대 한 **Sql 서비스 태그** 에서 [IP 주소를 추가](sql-database-server-level-firewall-rule.md) 하는 개별 방화벽 규칙 항목을 만들어야 합니다.
**허브** 와 **구성원** 데이터베이스를 모두 호스팅하는 논리 서버에 이러한 서버 수준 방화벽 규칙을 추가 합니다 (다른 지역에 있을 수 있음).

다음 PowerShell 스크립트를 사용 하 여 미국 서 부 지역에 대 한 Sql 서비스 태그에 해당 하는 IP 주소를 생성 합니다.
```powershell
PS C:\>  $serviceTags = Get-AzNetworkServiceTag -Location eastus2
PS C:\>  $sql = $serviceTags.Values | Where-Object { $_.Name -eq "Sql.WestUS" }
PS C:\> $sql.Properties.AddressPrefixes.Count
70
PS C:\> $sql.Properties.AddressPrefixes
13.86.216.0/25
13.86.216.128/26
13.86.216.192/27
13.86.217.0/25
13.86.217.128/26
13.86.217.192/27
```

> [!TIP]
> AzNetworkServiceTag은 Location 매개 변수를 지정 하는 경우에도 Sql 서비스 태그의 전역 범위를 반환 합니다. 동기화 그룹에서 사용 하는 허브 데이터베이스를 호스트 하는 지역으로 필터링 해야 합니다.

PowerShell 스크립트의 출력은 클래스를 사용 하지 않는 CIDR (도메인 간 라우팅) 표기법으로 되어 있으므로 Get-IPrangeStartEnd를 사용 하 여 시작 및 끝 IP 주소 형식으로 변환 해야 [합니다.](https://gallery.technet.microsoft.com/scriptcenter/Start-and-End-IP-addresses-bcccc3a9) 예를 들면 다음과 같습니다.
```powershell
PS C:\> Get-IPrangeStartEnd -ip 52.229.17.93 -cidr 26                                                                   
start        end
-----        ---
52.229.17.64 52.229.17.127
```

다음 추가 단계를 수행 하 여 모든 IP 주소를 CIDR에서 Start 및 End IP 주소 형식으로 변환 합니다.

```powershell
PS C:\>foreach( $i in $sql.Properties.AddressPrefixes) {$ip,$cidr= $i.split('/') ; Get-IPrangeStartEnd -ip $ip -cidr $cidr;}                                                                                                                
start          end
-----          ---
13.86.216.0    13.86.216.127
13.86.216.128  13.86.216.191
13.86.216.192  13.86.216.223
```
이제 이러한 규칙을 별도의 방화벽 규칙으로 추가한 다음 **Azure 서비스에서 서버에 액세스 하도록 허용** 을 OFF로 설정할 수 있습니다.


## <a name="ip-firewall-rules"></a>IP 방화벽 규칙
Ip 기반 방화벽은 클라이언트 컴퓨터의 [ip 주소](sql-database-server-level-firewall-rule.md) 를 명시적으로 추가할 때까지 데이터베이스 서버에 대 한 모든 액세스를 차단 하는 Azure SQL Server의 기능입니다.


## <a name="virtual-network-firewall-rules"></a>Virtual Network 방화벽 규칙

IP 규칙 외에도 Azure SQL Server 방화벽을 사용 하 여 *가상 네트워크 규칙*을 정의할 수 있습니다.  
자세한 내용은 [Azure SQL Database Virtual Network 서비스 끝점 및 규칙](sql-database-vnet-service-endpoint-rule-overview.md) 을 참조 하거나이 비디오를 시청 하세요.

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Data-Exposed--Demo--Vnet-Firewall-Rules-for-SQL-Database/player?WT.mc_id=dataexposed-c9-niner]

 ### <a name="azure-networking-terminology"></a>Azure 네트워킹 용어  
Virtual Network 방화벽 규칙을 탐색 하면서 다음과 같은 Azure 네트워킹 용어를 알고 있어야 합니다.

**가상 네트워크:** Azure 구독과 연결 된 가상 네트워크를 사용할 수 있습니다. 

**서브넷:** 가상 네트워크에 **서브넷**이 포함됩니다. 소유한 Azure VM(가상 머신)은 서브넷에 할당됩니다. 하나의 서브넷에 여러 VM 또는 다른 컴퓨팅 노드가 포함될 수 있습니다. 액세스를 허용하도록 보안을 구성해야 가상 네트워크 외부의 컴퓨팅 노드가 가상 네트워크에 액세스할 수 있습니다.

**Virtual Network 서비스 엔드포인트:** [Virtual Network 서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)는 속성 값에 하나 이상의 정식 Azure 서비스 유형 이름이 포함된 서브넷입니다. 이 문서에서는 SQL Database라는 Azure 서비스를 나타내는 **Microsoft.Sql**의 형식 이름을 살펴봅니다.

**가상 네트워크 규칙:** SQL Database 서버에 대한 가상 네트워크 규칙은 SQL Database 서버의 ACL(액세스 제어 목록)에 나열된 서브넷입니다. SQL Database에 대한 ACL에 나열되려면 서브넷에 **Microsoft.Sql** 형식 이름이 있어야 합니다. 가상 네트워크 규칙은 서브넷에 있는 모든 노드에서 보낸 통신을 수락하도록 SQL Database 서버에 지시합니다.


## <a name="ip-vs-virtual-network-firewall-rules"></a>IP와 Virtual Network 방화벽 규칙 비교

Azure SQL Server 방화벽을 사용 하 여 SQL Database에 대 한 통신을 허용 하는 IP 주소 범위를 지정할 수 있습니다. 이 방법은 Azure 프라이빗 네트워크 외부에 있는 안정적인 IP 주소에 적합합니다. 그러나 Azure 개인 네트워크 내의 가상 머신 (Vm)는 *동적* IP 주소로 구성 됩니다. 동적 IP 주소는 VM이 다시 시작 될 때 변경 될 수 있으며, 그런 다음 IP 기반 방화벽 규칙을 무효화 합니다. 프로덕션 환경의 방화벽 규칙에서는 동적 IP 주소를 지정하면 안 됩니다.

VM에 대 한 *고정* IP 주소를 가져오면 이러한 제한을 해결할 수 있습니다. 자세한 내용은 Azure Portal를 [사용 하 여 가상 컴퓨터에 대 한 개인 IP 주소 구성](../virtual-network/virtual-networks-static-private-ip-arm-pportal.md)을 참조 하세요. 그러나 정적 IP 방법은 관리가 어려워질 수 있고 대규모로 이루어질 경우 비용이 많이 듭니다. 

가상 네트워크 규칙은 Vm이 포함 된 특정 서브넷의 액세스를 설정 하 고 관리 하는 보다 쉬운 방법입니다.

> [!NOTE]
> 아직 서브넷에 SQL Database가 있을 수 없습니다. Azure SQL Database 서버가 가상 네트워크에 있는 서브넷의 노드인 경우 가상 네트워크 내의 모든 노드가 SQL Database와 통신할 수 있습니다. 이 경우 VM은 가상 네트워크 규칙이나 IP 규칙이 없어도 SQL Database와 통신할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 서버 수준 IP 방화벽 규칙을 만드는 방법에 대 한 빠른 시작은 [AZURE SQL 데이터베이스 만들기](sql-database-single-database-get-started.md)를 참조 하세요.

- 서버 수준 Vnet 방화벽 규칙을 만드는 방법에 대 한 빠른 시작은 [Virtual Network 서비스 끝점 및 Azure SQL Database에 대 한 규칙](sql-database-vnet-service-endpoint-rule-overview.md)을 참조 하세요.

- 오픈 소스 또는 타사 응용 프로그램에서 Azure SQL database에 연결 하는 데 도움이 필요한 경우 [클라이언트 빠른 시작 코드 샘플](https://msdn.microsoft.com/library/azure/ee336282.aspx)을 참조 하 여 SQL Database 합니다.

- 열어야 할 수 있는 추가 포트에 대한 자세한 내용은 **ADO.NET 4.5와 SQL Database에 대한 1433 이외 포트**의 [SQL Database: 내부 및 외부](sql-database-develop-direct-route-ports-adonet-v12.md) 섹션을 참조하세요.

- Azure SQL Database 연결에 대 한 개요는 [AZURE SQL 연결 아키텍처](sql-database-connectivity-architecture.md) 를 참조 하세요.

- Azure SQL Database 보안 개요는 [데이터베이스 보안 설정](sql-database-security-overview.md)을 참조하세요.

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/new-server2.png
[2]: ./media/sql-database-get-started-portal/manage-server-firewall.png
