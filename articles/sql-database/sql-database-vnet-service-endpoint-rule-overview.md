---
title: 단일 및 풀링된 데이터베이스에 대 한 VNet 엔드포인트 및 규칙
description: 서브넷을 Virtual Network 서비스 엔드포인트로 표시합니다. 그런 다음 해당 엔드포인트를 가상 네트워크 규칙으로 Azure SQL Database의 ACL에 추가합니다. 그러면 SQL Database가 해당 서브넷에 있는 모든 가상 머신과 다른 노드에서 보낸 통신을 수락합니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto, genemi
ms.date: 11/14/2019
ms.openlocfilehash: 0562d609231d69d95f1d2b5b838663b704f8f2f3
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75972709"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-database-servers"></a>데이터베이스 서버에 대한 가상 네트워크 서비스 엔드포인트 및 규칙 사용

*가상 네트워크 규칙*은 Azure [SQL Database](sql-database-technical-overview.md)의 단일 데이터베이스 및 탄력적 풀 또는 [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)의 데이터베이스에 대한 데이터베이스 서버가 가상 네트워크의 특정 서브넷에서 보낸 통신을 수락할지 여부를 제어하는 하나의 방화벽 보안 기능입니다. 이 문서에서는 경우에 따라 가상 네트워크 규칙 기능이 Azure SQL Database 및 SQL Data Warehouse에 대한 통신을 안전하게 허용하기 위한 가장 좋은 옵션인 이유를 설명합니다.

> [!IMPORTANT]
> 이 문서는 Azure SQL 서버 및 Azure SQL 서버에서 생성된 SQL Database와 SQL Data Warehouse 데이터베이스에 적용됩니다. 간단히 하기 위해 SQL Database는 SQL Database와 SQL Data Warehouse를 참조할 때 사용됩니다. Azure SQL Database의 **관리되는 인스턴스** 배포는 서비스 엔드포인트와 연결되지 않으므로 이 문서는 해당 배포에 적용되지 *않습니다*.

가상 네트워크 규칙을 만들려면 먼저 참조할 규칙의 [가상 네트워크 서비스 엔드포인트][vm-virtual-network-service-endpoints-overview-649d] 이 있어야 합니다.

## <a name="how-to-create-a-virtual-network-rule"></a>가상 네트워크 규칙을 만드는 방법

가상 네트워크 규칙을 만들기만 할 경우 [이 문서의 뒷부분](#anchor-how-to-by-using-firewall-portal-59j)에 있는 단계와 설명으로 바로 건너뛸 수 있습니다.

<!--<a name="anch-details-about-vnet-rules-38q"/> -->

## <a name="details-about-virtual-network-rules"></a>가상 네트워크 규칙에 대한 세부 정보

이 섹션에서는 가상 네트워크 규칙에 대한 여러 가지 세부 정보를 설명합니다.

### <a name="only-one-geographic-region"></a>단 하나의 지리적 지역

각 Virtual Network 서비스 엔드포인트는 하나의 Azure 지역에만 적용됩니다. 엔드포인트를 사용하여 다른 지역이 서브넷에서 보낸 통신을 수락하도록 할 수 없습니다.

가상 네트워크 규칙은 기본 엔드포인트가 적용되는 지역으로 제한됩니다.

### <a name="server-level-not-database-level"></a>데이터베이스 수준이 아님, 서버 수준

각 가상 네트워크 규칙은 서버에 있는 하나의 특정 데이터베이스가 아니라 전체 Azure SQL Database 서버에 적용됩니다. 즉, 가상 네트워크 규칙은 데이터베이스 수준이 아니라 서버 수준에서 적용됩니다.

- 이와 달리 IP 규칙은 각 수준에서 적용될 수 있습니다.

### <a name="security-administration-roles"></a>보안 관리 역할

Virtual Network 서비스 엔드포인트 관리에는 보안 역할 분리가 있습니다. 다음과 같은 각 역할의 작업이 필요합니다.

- **네트워크 관리자:** 엔드포인트을 &nbsp; 설정 합니다.
- **데이터베이스 관리자:** ACL (액세스 제어 목록)을 업데이트 하 여 지정 된 서브넷을 SQL Database 서버에 추가 &nbsp; 합니다.

*RBAC 대체:*

네트워크 관리자 및 데이터베이스 관리자 역할에는 가상 네트워크 규칙을 관리하는 데 필요한 것보다 많은 기능이 포함됩니다. 해당 기능의 하위 집합만 필요합니다.

Azure에서 [RBAC (역할 기반 액세스 제어)][rbac-what-is-813s] 를 사용 하 여 필요한 기능 하위 집합만 포함 하는 단일 사용자 지정 역할을 만들 수 있습니다. 네트워크 관리자 또는 데이터베이스 관리자를 포함 하는 대신 사용자 지정 역할을 사용할 수 있습니다. 사용자를 사용자 지정 역할에 추가 하 고 다른 두 개의 주요 관리자 역할에 사용자를 추가 하는 경우 보안 노출의 노출 영역이 낮습니다.

> [!NOTE]
> 일부 경우에 Azure SQL Database 및 VNet 서브넷은 서로 다른 구독에 있습니다. 이러한 경우에는 다음과 같은 구성을 확인해야 합니다.
> - 두 구독은 모두 동일한 Azure Active Directory 테넌트에 있어야 합니다.
> - 서비스 엔드포인트를 사용하도록 설정하고 지정된 서버에 VNet 서브넷을 추가하는 등의 작업을 시작하는 데 필요한 권한이 사용자에게 있습니다.
> - 두 구독 모두 Microsoft.Sql 공급자에 등록되어야 합니다.

## <a name="limitations"></a>제한 사항

Azure SQL Database의 경우 가상 네트워크 규칙 기능에는 다음과 같은 제한이 있습니다.

- SQL Database에 대한 방화벽에서 각 가상 네트워크 규칙은 서브넷을 참조합니다. 이렇게 참조된 모든 서브넷은 SQL Database와 동일한 지리적 위치에서 호스팅되어야 합니다.

- 각 Azure SQL Database 서버에는 특정 가상 네트워크에 대해 최대 128개 ACL 항목이 포함될 수 있습니다.

- 가상 네트워크 규칙은 Azure Resource Manager 가상 네트워크에만 적용 됩니다. [클래식 배포 모델][arm-deployment-model-568f] 네트워크는 그렇지 않습니다.

- 가상 네트워크 서비스 엔드포인트를 Azure SQL Database로 설정하면 MySQL 및 PostgreSQL Azure 서비스에 대한 엔드포인트도 활성화됩니다. 그러나 엔드포인트를 실행한 상태에서 엔드포인트를 MySQL 또는 PostgreSQL 인스턴스에 연결하려는 시도는 실패합니다.
  - 기본적인 이유는 MySQL 및 PostgreSQL에서 가상 네트워크 규칙이 구성되지 않을 가능성이 높다는 것입니다. Azure Database for MySQL 및 PostgreSQL의 가상 네트워크 규칙을 구성해야 연결이 성공적으로 수행됩니다.

- 방화벽에서 IP 주소 범위는 다음 네트워킹 항목에 적용되지만 가상 네트워크 규칙에는 적용되지 않습니다.
  - [S2S (사이트 간) VPN (가상 사설망)][vpn-gateway-indexmd-608y]
  - [Express][expressroute-indexmd-744v] 경로를 통해 온-프레미스

### <a name="considerations-when-using-service-endpoints"></a>서비스 엔드포인트 사용 시 고려할 사항

Azure SQL Database에 대해 서비스 엔드포인트를 사용하는 경우 다음 고려 사항을 검토합니다.

- **Azure SQL Database 공용 IP에 대한 아웃 바운드가 필요함**: 연결을 허용하려면 Azure SQL Database IP에 대해 NSG(네트워크 보안 그룹)를 열어야 합니다. Azure SQL Database에 대해 NSG [서비스 태그](../virtual-network/security-overview.md#service-tags)를 사용하면 됩니다.

### <a name="expressroute"></a>Express Route

공용 피어링 또는 Microsoft 피어링을 위해 온-프레미스에서 [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 사용하는 경우 사용되는 NAT IP 주소를 식별해야 합니다. 공용 피어링의 경우 기본적으로 각 ExpressRoute 회로는 트래픽이 Microsoft Azure 네트워크 백본으로 들어갈 때 Azure 서비스 트래픽에 적용되는 두 개의 NAT IP 주소를 사용합니다. Microsoft 피어링의 경우 사용되는 NAT IP 주소는 고객이 제공하거나 서비스 공급자가 제공합니다. 서비스 리소스에 대한 액세스를 허용하려면 리소스 IP 방화벽 설정에서 이러한 공용 IP 주소를 허용해야 합니다. ExpressRoute 회로 IP 주소를 찾으려면 Azure Portal을 통해 [ExpressRoute에서 지원 티켓을 엽니다](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). [ExpressRoute 공용 및 Microsoft 피어링을 위한 NAT](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)에 대해 자세히 알아보세요.
  
회로에서 Azure SQL Database로의 통신을 허용하려면 NAT의 공용 IP 주소에 대한 IP 네트워크 규칙을 만들어야 합니다.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-using-vnet-service-endpoints-with-azure-storage"></a>Azure Storage에서 VNet 서비스 엔드포인트 사용의 영향

Azure Storage는 사용자가 Azure Storage 계정에 대한 연결성을 제한하도록 허용하는 동일한 기능을 구현했습니다. Azure SQL Server에서 사용 중인 Azure Storage 계정에서 이 기능을 사용하도록 선택한 경우 문제가 발생할 수 있습니다. 다음은 이로 인해 영향을 받는 Azure SQL Database 및 Azure SQL Data Warehouse 기능의 목록 및 논의 내용입니다.

### <a name="azure-sql-data-warehouse-polybase"></a>Azure SQL Data Warehouse PolyBase

PolyBase는 대개 Azure Storage 계정에서 Azure SQL Data Warehouse로 데이터를 로드하는 데 사용됩니다. 데이터를 로드하는 Azure Storage 계정이 액세스를 VNet 서브넷 집합으로만 제한하는 경우 PolyBase에서 계정으로의 연결은 중단됩니다. Azure SQL Data Warehouse가 VNet으로 보호되는 Azure Storage에 연결되는 PolyBase 가져오기 및 내보내기 시나리오를 사용하도록 설정하는 경우 아래에 설명된 단계를 따르세요.

#### <a name="prerequisites"></a>필수 조건

- [이 가이드](https://docs.microsoft.com/powershell/azure/install-az-ps)를 사용하여 Azure PowerShell을 설치합니다.
- 범용 v1 또는 Blob Storage 계정이 있는 경우 먼저 이 [가이드](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade)를 사용하여 범용 v2로 업그레이드해야 합니다.
- Azure Storage 계정 **방화벽 및 가상 네트워크** 설정 메뉴에서 **신뢰할 수 있는 Microsoft 서비스가 이 스토리지 계정에 액세스하도록 허용합니다.** 를 설정해야 합니다. 자세한 내용은 이 [가이드](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)를 참조하세요.

> [!IMPORTANT]
> Azure SQL Database, Azure Resource Manager PowerShell 모듈은 계속 지원하지만 모든 향후 개발은 Az.Sql 모듈에 대해 진행됩니다. AzureRM 모듈은 12 월 2020 일까 때까지 버그 수정을 계속 받습니다.  Az 모듈과 AzureRm 모듈에서 명령의 인수는 실질적으로 동일합니다. 호환성에 대 한 자세한 내용은 [새 Azure PowerShell Az Module 소개](/powershell/azure/new-azureps-module-az)를 참조 하세요.

#### <a name="steps"></a>단계

1. PowerShell에서 AAD (Azure Active Directory)를 사용 하 여 Azure SQL Data Warehouse 인스턴스를 호스트 하는 **Azure SQL Server를 등록** 합니다.

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

1. 이 [가이드](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)를 사용하여 **범용 v2 스토리지 계정**을 만듭니다.

   > [!NOTE]
   > - 범용 v1 또는 Blob Storage 계정이 있는 경우 먼저 이 [가이드](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade)를 사용하여 **v2로 업그레이드**해야 합니다.
   > - Azure Data Lake Storage Gen2의 알려진 문제에 대해서는 이 [가이드](https://docs.microsoft.com/azure/storage/data-lake-storage/known-issues)를 참조하세요.
    
1. 스토리지 계정 아래의 **액세스 제어(IAM)** 로 이동하고 **역할 할당 추가**를 클릭합니다. 1 단계에서와 같이 AAD (Azure Active Directory에 등록 한 Azure SQL Data Warehouse를 호스팅하는 Azure SQL Server에 **저장소 Blob 데이터 참가자** RBAC 역할을 할당 합니다.

   > [!NOTE]
   > 소유자 권한이 있는 멤버만 이 단계를 수행할 수 있습니다. Azure 리소스에 대한 다양한 기본 제공 역할을 보려면 이 [가이드](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)를 참조하세요.
  
1. **Azure Storage 계정에 대한 Polybase 연결:**

   1. 이전에 생성하지 않은 경우 데이터베이스 **[마스터 키](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql)** 를 만듭니다.

       ```sql
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```

   1. **IDENTITY = '관리 서비스 ID'** 인 데이터베이스 범위 자격 증명을 만듭니다.

       ```sql
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```

       > [!NOTE]
       > - 이 메커니즘은 내부적으로 [관리 ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)를 사용하므로 Azure Storage 액세스 키로 SECRET을 지정할 필요가 없습니다.
       > - PolyBase 연결을 VNet에서 보호되는 Azure Storage 계정에 사용하려면 IDENTITY 이름이 **'관리 서비스 ID'** 여야 합니다.

   1. PolyBase를 사용 하 여 범용 v2 저장소 계정에 연결 하기 위한 `abfss://` 체계를 사용 하 여 외부 데이터 원본 만들기:

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```

       > [!NOTE]
       > - 범용 v1 또는 Blob Storage 계정과 연결된 외부 테이블이 이미 있는 경우 먼저 해당 외부 테이블을 삭제한 다음, 해당 외부 데이터 원본을 삭제해야 합니다. 그런 다음 위와 같이 범용 v2 저장소 계정에 연결 하는 `abfss://` 체계를 사용 하 여 외부 데이터 원본을 만들고이 새 외부 데이터 원본을 사용 하 여 모든 외부 테이블을 다시 만듭니다. [스크립트 생성 및 게시 마법사](https://docs.microsoft.com/sql/ssms/scripting/generate-and-publish-scripts-wizard)를 사용하여 모든 외부 테이블용 create-scripts를 쉽게 생성할 수 있습니다.
       > - `abfss://` 체계에 대 한 자세한 내용은이 [가이드](https://docs.microsoft.com/azure/storage/data-lake-storage/introduction-abfs-uri)를 참조 하세요.
       > - CREATE EXTERNAL DATA SOURCE에 대한 자세한 내용은 이 [가이드](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql)를 참조하세요.

   1. [외부 테이블](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql)을 사용하여 평소와 같이 쿼리합니다.

### <a name="azure-sql-database-blob-auditing"></a>Azure SQL Database Blob 감사

Blob 감사는 사용자 고유의 스토리지 계정에 감사 로그를 푸시합니다. 이 저장소 계정에서 VNet 서비스 엔드포인트 기능을 사용하는 경우 Azure SQL Database에서 스토리지 계정으로의 연결이 끊어집니다.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>VNet 서비스 엔드포인트를 켜지 않고 서버에 VNet 방화벽 규칙 추가

이 기능을 강화하기 오래 전에는 VNet 서비스 엔드포인트를 켜야 방화벽에 라이브 VNet 규칙을 구현할 수 있었습니다. 엔드포인트는 Azure SQL Database에 지정된 VNet 서브넷과 관련이 있었습니다. 그러나 이제 2018년 1월부터는 **IgnoreMissingVNetServiceEndpoint** 플래그를 설정하여 이 요구 사항을 우회할 수 있습니다.

단순히 방화벽 규칙을 설정하는 것은 서버 보안에 도움이 되지 않습니다. 보안이 효력을 나타내려면 VNet 서비스 엔드포인트도 켜야 합니다. 서비스 엔드포인트를 켜면 전환을 끈 상태에서 켠 상태로 완료할 때까지 VNet 서브넷에서 가동 중지 시간이 발생합니다. 이는 특히 대규모 VNet의 컨텍스트에 적용됩니다. **IgnoreMissingVNetServiceEndpoint** 플래그를 사용하여 전환 시 가동 중지 시간을 줄이거나 없앨 수 있습니다.

PowerShell을 사용하여 **IgnoreMissingVNetServiceEndpoint** 플래그를 설정할 수 있습니다. 자세한 내용은 [PowerShell을 Virtual Network service 엔드포인트 및 Azure SQL Database 규칙 만들기][sql-db-vnet-service-endpoint-rule-powershell-md-52d]를 참조 하세요.

## <a name="errors-40914-and-40615"></a>오류 40914 및 40615

연결 오류 40914는 Azure Porrtal의 방화벽 창에서 지정한 대로 *가상 네트워크 규칙*과 관련이 있습니다. 40615 오류는 비슷하지만 방화벽의 *IP 주소 규칙*과 관련이 있다는 점은 다릅니다.

### <a name="error-40914"></a>오류 40914

*메시지 텍스트*: 로그인에서 요청한 ' *[server-name]* ' 서버를 열 수 없습니다. 클라이언트가 서버에 액세스할 수 없습니다.

*오류 설명:* 클라이언트가 가상 네트워크 서버 엔드포인트가 있는 서브넷에 있습니다. 그러나 Azure SQL Database 서버에는 SQL Database와의 통신 권한을 서브넷에 부여하는 가상 네트워크 규칙이 없습니다.

*오류 해결:* Azure Portal의 방화벽 창에서 가상 네트워크 규칙 제어를 사용하여 서브넷에 대한 [가상 네트워크 규칙을 추가](#anchor-how-to-by-using-firewall-portal-59j)합니다.

### <a name="error-40615"></a>오류 40615

*메시지 텍스트:* 로그인에서 요청된 서버 '{0}'을(를) 열 수 없습니다. IP 주소가 ‘{1}’인 클라이언트는 서버에 액세스할 수 없습니다.

*오류 설명:* 클라이언트가 Azure SQL Database 서버에 연결할 권한이 없는 IP 주소에서 연결을 시도합니다. 서버 방화벽에 클라이언트가 주어진 IP 주소로부터 SQL Database로 통신하도록 허용하는 IP 주소 규칙이 없습니다.

*오류 해결:* 클라이언트의 IP 주소를 IP 규칙으로 입력합니다. Azure Portal의 방화벽 창을 사용하여 수행합니다.

<a name="anchor-how-to-by-using-firewall-portal-59j" />

## <a name="portal-can-create-a-virtual-network-rule"></a>포털은 가상 네트워크 규칙을 만들 수 있습니다.

이 섹션에서는 [Azure Portal][http-azure-portal-link-ref-477t] 사용 하 여 Azure SQL Database에서 *가상 네트워크 규칙* 을 만드는 방법을 설명 합니다. 이 규칙은 *Virtual Network 서비스 엔드포인트*로 태그가 지정된 특정 서브넷에서 보낸 통신을 수락하도록 SQL Database에 지시합니다.

> [!NOTE]
> Azure SQL Database 서버의 VNet 방화벽 규칙에 서비스 엔드포인트를 추가하려면 먼저 서브넷에 대해 서비스 엔드포인트가 설정되어 있는지 확인하세요.
>
> 서브넷에 대해 서비스 엔드포인트가 설정되어 있지 않으면 포털에서 설정하라는 메시지가 표시됩니다. 규칙을 추가한 동일한 블레이드에서 **사용** 단추를 클릭합니다.

## <a name="powershell-alternative"></a>PowerShell 대체

또한 스크립트는 PowerShell cmdlet **AzSqlServerVirtualNetworkRule** 또는 [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create)를 사용 하 여 가상 네트워크 규칙을 만들 수 있습니다. 관심이 [있는 경우 PowerShell을 참조 하 여 Azure SQL Database에 대 한 Virtual Network 서비스 엔드포인트 및 규칙을 만듭니다][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="rest-api-alternative"></a>REST API 대체

내부적으로 SQL VNet 작업을 위한 PowerShell cmdlet은 REST API를 호출합니다. REST API를 직접 호출할 수 있습니다.

- [Virtual Network 규칙: 작업][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>필수 조건

Azure SQL Database에 관련된 특정 Virtual Network 서비스 엔드포인트 *형식 이름*으로 태그가 지정된 서브넷이 있어야 합니다.

- 관련 엔드포인트 형식 이름은 **Microsoft.Sql**입니다.
- 서브넷에 형식 이름으로 태그를 지정 하지 않을 수 있는 경우 [서브넷이 엔드포인트 인지 확인][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]을 참조 하세요.

<a name="a-portal-steps-for-vnet-rule-200" />

## <a name="azure-portal-steps"></a>Azure Portal 단계

1. [Azure Portal][http-azure-portal-link-ref-477t]에 로그인합니다.

2. **SQL 서버**를 검색 하 고 선택한 다음 서버를 선택 합니다. **보안**에서 **방화벽 및 가상 네트워크**를 선택 합니다.

3. **Azure 서비스에 대한 액세스 허용** 컨트롤을 [끄기]로 설정합니다.

    > [!IMPORTANT]
    > 제어를 설정 된 상태로 두면 Azure SQL Database 서버는 azure 경계 내의 모든 서브넷 (즉, Azure 데이터 센터에 대해 정의 된 범위 내에 있는 것으로 인식 되는 IP 주소 중 하나에서 시작)의 통신을 수락 합니다. 제어 집합을 ON으로 유지하면 보안 관점에서 과도하게 액세스할 수도 있습니다. Microsoft Azure Virtual Network 서비스 엔드포인트 기능을 SQL Database의 가상 네트워크 규칙 기능과 함께 사용하여 보안 노출 영역을 줄일 수 있습니다.

4. **가상 네트워크** 섹션에서 **+ 기존 항목 추가** 컨트롤을 클릭합니다.

    ![[기존 항목 추가]를 클릭합니다(SQL 규칙으로서 서브넷 엔드포인트).][image-portal-firewall-vnet-add-existing-10-png]

5. 새 **만들기/업데이트** 창에서 Azure 리소스 이름으로 컨트롤을 채웁니다.

    > [!TIP]
    > 서브넷에 대한 정확한 **주소 접두사**를 포함해야 합니다. Portal에서 값을 찾을 수 있습니다.
    > 모든 **리소스** &gt; 모든 **형식** &gt; **가상 네트워크**로 이동 합니다. 필터에 가상 네트워크가 표시됩니다. 사용 중인 가상 네트워크를 클릭하고 **서브넷**을 클릭합니다. **주소 범위** 열에 필요한 주소 접두사가 있습니다.

    ![새 규칙에 대한 필드를 입력합니다.][image-portal-firewall-create-update-vnet-rule-20-png]

6. 창 아래쪽에 있는 **확인** 단추를 클릭합니다.

7. 방화벽 창에서 결과 가상 네트워크 규칙을 확인합니다.

    ![방화벽 창에서 새 규칙을 확인합니다.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> 다음 상태는 규칙에 적용됩니다.
> - **Ready:** 시작한 작업이 성공했음을 나타냅니다.
> - **Failed:** 시작한 작업이 실패했음을 나타냅니다.
> - **Deleted:** 삭제 작업에만 적용되고 규칙이 삭제되었으며 더 이상 적용되지 않음을 나타냅니다.
> - **InProgress:** 작업이 진행 중임을 나타냅니다. 작업이 이 상태인 동안에는 이전 규칙이 적용됩니다.

<a name="anchor-how-to-links-60h" />

## <a name="related-articles"></a>관련 문서

- [Azure 가상 네트워크 서비스 엔드포인트][vm-virtual-network-service-endpoints-overview-649d]
- [서버 수준 및 데이터베이스 수준 방화벽 규칙 Azure SQL Database][sql-db-firewall-rules-config-715d]

Azure SQL Database에 대한 가상 네트워크 규칙 기능은 2017년 9월 말에 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [PowerShell을 사용 하 여 가상 네트워크 서비스 엔드포인트을 만든 다음 Azure SQL Database에 대 한 가상 네트워크 규칙을 만듭니다.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Virtual Network 규칙:][rest-api-virtual-network-rules-operations-862r] REST api를 사용 하는 작업

<!-- Link references, to images. -->
[image-portal-firewall-vnet-add-existing-10-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png
[image-portal-firewall-create-update-vnet-rule-20-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png
[image-portal-firewall-vnet-result-rule-30-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png

<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../azure-resource-manager/management/deployment-models.md
[expressroute-indexmd-744v]: ../expressroute/index.yml
[rbac-what-is-813s]:../role-based-access-control/overview.md
[sql-db-firewall-rules-config-715d]: sql-database-firewall-configure.md
[sql-db-vnet-service-endpoint-rule-powershell-md-52d]: sql-database-vnet-service-endpoint-rule-powershell.md
[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]: sql-database-vnet-service-endpoint-rule-powershell.md#a-verify-subnet-is-endpoint-ps-100
[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[vm-virtual-network-service-endpoints-overview-649d]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

<!-- Link references, to text, Outside this GitHub repo (HTTP). -->
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
[rest-api-virtual-network-rules-operations-862r]: https://docs.microsoft.com/rest/api/sql/virtualnetworkrules

<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON
- Azure CLI
- ARM templates
-->
