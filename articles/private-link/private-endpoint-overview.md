---
title: Azure 개인 엔드포인트 이란?
description: Azure 개인 엔드포인트에 대 한 자세한 정보
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: dd73f42aaa0d0bd1884892143d96446935a401a5
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048433"
---
# <a name="what-is-azure-private-endpoint"></a>Azure 개인 엔드포인트 이란?

Azure 프라이빗 엔드포인트는 Azure Private Link가 제공하는, 서비스에 비공개로 안전하게 연결하는 네트워크 인터페이스입니다. 개인 엔드포인트은 VNet의 개인 IP 주소를 사용 하 여 서비스를 VNet에 효과적으로 제공 합니다. 서비스는 Azure Storage, Azure Cosmos DB, SQL 등의 Azure 서비스 이거나 자신의 [개인 링크 서비스](private-link-service-overview.md)일 수 있습니다.
  
## <a name="private-endpoint-properties"></a>개인 엔드포인트 속성 
 개인 엔드포인트은 다음 속성을 지정 합니다. 


|속성  |설명 |
|---------|---------|
|이름    |    리소스 그룹 내의 고유한 이름입니다.      |
|서브넷    |  가상 네트워크에서 개인 IP 주소를 배포 하 고 할당 하는 서브넷입니다. 서브넷 요구 사항은이 문서의 제한 사항 섹션을 참조 하세요.         |
|개인 링크 리소스    |   사용 가능한 형식 목록에서 리소스 ID 또는 별칭을 사용 하 여 연결할 개인 링크 리소스입니다. 이 리소스로 전송 되는 모든 트래픽에 대해 고유한 네트워크 식별자가 생성 됩니다.       |
|대상 하위 리소스   |      연결할 하위 리소스입니다. 각 개인 링크 리소스 종류에는 기본 설정에 따라 선택할 수 있는 다양 한 옵션이 있습니다.    |
|연결 승인 방법    |  자동 또는 수동. RBAC (역할 기반 액세스 제어) 권한에 따라 개인 엔드포인트이 자동으로 승인 될 수 있습니다. RBAC 없이 개인 링크 리소스에 연결 하려고 하면 수동 메서드를 사용 하 여 리소스 소유자가 연결을 승인할 수 있도록 허용 합니다.        |
|요청 메시지     |  요청 된 연결이 수동으로 승인 되도록 메시지를 지정할 수 있습니다. 이 메시지는 특정 요청을 식별 하는 데 사용할 수 있습니다.        |
|연결 상태   |   개인 엔드포인트이 활성 상태 인지 여부를 지정 하는 읽기 전용 속성입니다. 승인 된 상태의 개인 엔드포인트만 트래픽을 보내는 데 사용할 수 있습니다. 사용 가능한 추가 상태: <br>**승인**-: 연결이 자동 또는 수동으로 승인 되었고 사용할 준비가 되었습니다.</br><br>-**보류**중: 연결이 수동으로 만들어졌으며 개인 링크 리소스 소유자가 승인을 보류 하 고 있습니다.</br><br>-**거부 됨**: 개인 링크 리소스 소유자가 연결을 거부 했습니다.</br><br>-연결 **끊김**: 개인 링크 리소스 소유자가 연결을 제거 했습니다. 개인 엔드포인트은 정보를 제공 하 고 정리를 위해 삭제 해야 합니다. </br>|

개인 엔드포인트에 대 한 몇 가지 주요 정보는 다음과 같습니다. 
- 개인 엔드포인트을 사용 하면 동일한 VNet, 지역적으로 피어 링 Vnet, 글로벌 피어 링 Vnet 및 온-프레미스에서 [VPN](https://azure.microsoft.com/services/vpn-gateway/) 또는 [Express 경로](https://azure.microsoft.com/services/expressroute/) 를 사용 하는 소비자와 개인 링크를 통해 제공 되는 서비스 간의 연결이 가능해 집니다.
 
- 개인 엔드포인트을 만들 때 리소스 수명 주기에 대 한 네트워크 인터페이스도 생성 됩니다. 인터페이스에는 개인 링크 서비스에 매핑되는 서브넷의 개인 IP 주소가 할당 됩니다.
 
- 개인 엔드포인트은 가상 네트워크와 동일한 지역에 배포 되어야 합니다. 
 
- 개인 링크 리소스는 가상 네트워크 및 개인 엔드포인트이 아닌 다른 지역에 배포할 수 있습니다.
 
- 동일한 개인 링크 리소스를 사용 하 여 여러 개인 엔드포인트을 만들 수 있습니다. 공용 DNS 서버 구성을 사용 하는 단일 네트워크의 경우에는 지정 된 개인 링크 리소스에 대 한 단일 개인 엔드포인트을 사용 하 여 중복 항목이 나 DNS 확인에서 충돌을 방지 하는 것이 좋습니다. 
 
- 동일한 가상 네트워크 내에서 동일 하거나 다른 서브넷에 여러 개인 엔드포인트을 만들 수 있습니다. 구독에서 만들 수 있는 개인 엔드포인트 수에는 제한이 있습니다. 자세한 내용은 [Azure 제한](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)을 참조 하세요.


 
## <a name="private-link-resource"></a>개인 링크 리소스 
개인 링크 리소스는 지정 된 개인 엔드포인트의 대상 대상입니다. 다음은 사용 가능한 개인 링크 리소스 종류 목록입니다. 
 
|개인 링크 리소스 이름  |리소스 유형   |하위  |
|---------|---------|---------|
|**개인 링크 서비스** (사용자 고유의 서비스)   |  Microsoft. Network/privateLinkServices       | empty |
|**Azure SQL Database** | Microsoft.Sql/servers    |  Sql Server (sqlServer)        |
|**Azure Synapse 분석** | Microsoft.Sql/servers    |  Sql Server (sqlServer)        |
|**Azure Storage**  | Microsoft.Storage/storageAccounts    |  Blob (blob, blob_secondary)<BR> 테이블 (테이블, table_secondary)<BR> 큐 (큐, queue_secondary)<BR> 파일 (파일, file_secondary)<BR> 웹 (웹, web_secondary)        |
|**Azure Data Lake Storage Gen2**  | Microsoft.Storage/storageAccounts    |  Blob (blob, blob_secondary)<BR> Data Lake 파일 시스템 Gen2 (dfs, dfs_secondary)       |
|**Azure Cosmos DB** | Microsoft.azurecosmosdb/databaseAccounts | Sql, MongoDB, Cassandra, Gremlin, Table|
|**Azure Database for PostgreSQL-단일 서버** | Microsoft.DBforPostgreSQL/servers   | postgresqlServer |
|**Azure Database for MySQL** | Microsoft.DBforMySQL/servers    | mysqlServer |
|**Azure Database for MariaDB** | Microsoft.DBforMariaDB/servers    | mariadbServer |
|**Azure Key Vault** | Microsoft.KeyVault/vaults    | 자격 증명 모음 |
 
## <a name="network-security-of-private-endpoints"></a>개인 엔드포인트의 네트워크 보안 
Azure 서비스에 대 한 개인 엔드포인트을 사용 하는 경우 특정 개인 링크 리소스에 대 한 트래픽이 보호 됩니다. 플랫폼은 액세스 제어를 수행 하 여 지정 된 개인 링크 리소스에만 도달 하는 네트워크 연결의 유효성을 검사 합니다. 동일한 Azure 서비스 내에서 추가 리소스에 액세스 하려면 추가 개인 엔드포인트이 필요 합니다. 
 
지원 되는 Azure 서비스에 연결 하기 위해 공용 엔드포인트에 액세스 하지 못하도록 작업을 완전히 잠글 수 있습니다. 이 컨트롤은 같은 Azure 서비스에서 호스트 되는 다른 리소스에 대 한 액세스를 차단 하는 기본 제공 반출 보호를 제공 하 여 리소스에 대 한 추가 네트워크 보안 계층을 제공 합니다. 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>승인 워크플로를 사용 하 여 개인 링크 리소스에 대 한 액세스 
다음 연결 승인 방법을 사용 하 여 개인 링크 리소스에 연결할 수 있습니다.
- 사용자가 소유 하거나 특정 개인 링크 리소스에 대 한 권한이 있는 경우 **자동으로** 승인 됩니다. 필요한 권한은 다음 형식의 개인 링크 리소스 형식을 기반으로 합니다. Microsoft.\<공급자 >/< resource_type >/privateEndpointConnectionApproval/action
- 필요한 권한이 없고 액세스를 요청 하는 경우 **수동** 요청 승인 워크플로가 시작 됩니다. 프라이빗 엔드포인트 및 후속 프라이빗 엔드포인트 연결은 "보류 중" 상태로 만들어집니다. Private Link 리소스 소유자가 연결을 승인해야 합니다. 승인 된 후에는 다음 승인 워크플로 다이어그램에 표시 된 것 처럼 개인 엔드포인트에서 트래픽을 정상적으로 전송할 수 있습니다.  

![워크플로 승인](media/private-endpoint-overview/private-link-paas-workflow.png)
 
개인 링크 리소스 소유자는 개인 엔드포인트 연결에 대해 다음 작업을 수행할 수 있습니다. 
- 모든 개인 엔드포인트 연결 정보를 검토 합니다. 
- 개인 엔드포인트 연결을 승인 합니다. 해당 개인 엔드포인트은 개인 링크 리소스에 트래픽을 보내도록 설정 됩니다. 
- 개인 엔드포인트 연결을 거부 합니다. 해당 개인 엔드포인트은 상태를 반영 하도록 업데이트 됩니다.
- 모든 상태에서 개인 엔드포인트 연결을 삭제 합니다. 해당 개인 엔드포인트은 작업을 반영 하기 위해 끊어진 상태로 업데이트 됩니다. 개인 엔드포인트 소유자는이 시점에서 리소스를 삭제할 수만 있습니다. 
 
> [!NOTE]
> 승인 된 상태의 개인 엔드포인트만 지정 된 개인 링크 리소스에 트래픽을 보낼 수 있습니다. 

### <a name="connecting-using-alias"></a>별칭을 사용 하 여 연결
별칭은 서비스 소유자가 표준 부하 분산 장치 뒤에 개인 링크 서비스를 만들 때 생성 되는 고유한 모니커입니다. 서비스 소유자는이 별칭을 소비자와 오프 라인으로 공유할 수 있습니다. 소비자는 리소스 URI 또는 별칭을 사용 하 여 개인 링크 서비스에 대 한 연결을 요청할 수 있습니다. 별칭을 사용 하 여 연결 하려면 수동 연결 승인 방법을 사용 하 여 개인 엔드포인트을 만들어야 합니다. 수동 연결 승인 방법을 사용 하려면 개인 엔드포인트 만들기 흐름에서 수동 요청 매개 변수를 true로 설정 합니다. 자세한 내용은 [AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint?view=azps-2.6.0) 및 [az network private-endpoint create](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) 를 참조 하세요. 

## <a name="dns-configuration"></a>DNS 구성 
FQDN (정규화 된 도메인 이름)을 사용 하 여 연결 문자열의 일부로 개인 링크 리소스에 연결 하는 경우 할당 된 개인 IP 주소를 확인 하도록 DNS 설정을 올바르게 구성 하는 것이 중요 합니다. 기존 Azure 서비스에는 공용 엔드포인트을 통해 연결할 때 사용할 DNS 구성이 이미 있을 수 있습니다. 개인 엔드포인트을 사용 하 여 연결 하려면이를 재정의 해야 합니다. 
 
개인 엔드포인트과 연결 된 네트워크 인터페이스에는 지정 된 개인 링크 리소스에 대해 할당 된 FQDN 및 개인 IP 주소를 포함 하 여 DNS를 구성 하는 데 필요한 전체 정보 집합이 포함 되어 있습니다. 
 
다음 옵션을 사용 하 여 개인 엔드포인트에 대 한 DNS 설정을 구성할 수 있습니다. 
- **호스트 파일 (테스트에만 권장 됨)을 사용**합니다. 가상 컴퓨터의 호스트 파일을 사용 하 여 DNS를 재정의할 수 있습니다.  
- **개인 DNS 영역을 사용**합니다. 개인 DNS 영역을 사용 하 여 지정 된 개인 엔드포인트에 대 한 DNS 확인을 재정의할 수 있습니다. 개인 DNS 영역을 가상 네트워크에 연결 하 여 특정 도메인을 확인할 수 있습니다.
- **사용자 지정 DNS 서버를 사용**합니다. 자체 DNS 서버를 사용 하 여 지정 된 개인 링크 리소스에 대 한 DNS 확인을 재정의할 수 있습니다. [Dns 서버가](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) 가상 네트워크에서 호스트 되는 경우 개인 dns 영역을 사용 하 여 모든 개인 링크 리소스에 대 한 구성을 간소화 하는 dns 전달 규칙을 만들 수 있습니다.
 
> [!IMPORTANT]
> 공용 엔드포인트을 확인 하는 데 적극적으로 사용 되는 영역은 재정의 하지 않는 것이 좋습니다. 공용 DNS로 DNS를 전달 하지 않고 리소스에 대 한 연결을 올바르게 확인할 수 없습니다. 문제를 방지 하려면 다른 도메인 이름을 만들거나 아래 각 서비스에 대 한 제안 된 이름을 따르세요. 
 
Azure 서비스의 경우 다음 표에 설명 된 대로 권장 된 영역 이름을 사용 합니다.

|개인 링크 리소스 유형   |Subresource  |영역 이름  |
|---------|---------|---------|
|SQL DB/DW (Microsoft .Sql/servers)    |  Sql Server (sqlServer)        |   privatelink.database.windows.net       |
|저장소 계정 (Microsoft. Storage/storageAccounts)    |  Blob (blob, blob_secondary)        |    privatelink.blob.core.windows.net      |
|저장소 계정 (Microsoft. Storage/storageAccounts)    |    테이블 (테이블, table_secondary)      |   privatelink.table.core.windows.net       |
|저장소 계정 (Microsoft. Storage/storageAccounts)    |    큐 (큐, queue_secondary)     |   privatelink.queue.core.windows.net       |
|저장소 계정 (Microsoft. Storage/storageAccounts)   |    파일 (파일, file_secondary)      |    privatelink.file.core.windows.net      |
|저장소 계정 (Microsoft. Storage/storageAccounts)     |  웹 (웹, web_secondary)        |    privatelink.web.core.windows.net      |
|Data Lake 파일 시스템 Gen2 (Microsoft Storage/storageAccounts)  |  Data Lake 파일 시스템 Gen2 (dfs, dfs_secondary)        |     privatelink.dfs.core.windows.net     |
|Azure Cosmos DB (Microsoft.azurecosmosdb/databaseAccounts)|SQL |privatelink.documents.azure.com|
|Azure Cosmos DB (Microsoft.azurecosmosdb/databaseAccounts)|MongoDB |privatelink.mongo.cosmos.azure.com|
|Azure Cosmos DB (Microsoft.azurecosmosdb/databaseAccounts)|Cassandra|privatelink.cassandra.cosmos.azure.com|
|Azure Cosmos DB (Microsoft.azurecosmosdb/databaseAccounts)|Gremlin |privatelink.gremlin.cosmos.azure.com|
|Azure Cosmos DB (Microsoft.azurecosmosdb/databaseAccounts)|Table|privatelink.table.cosmos.azure.com|
|Azure Database for PostgreSQL-단일 서버 (DBforPostgreSQL/servers)|postgresqlServer|privatelink.postgres.database.azure.com|
|Azure Database for MySQL (Microsoft DBforMySQL/servers)|mysqlServer|privatelink.mysql.database.azure.com|
|Azure Database for MariaDB (DBforMariaDB/servers)|mariadbServer|privatelink.mariadb.database.azure.com|
|Azure Key Vault (Microsoft. KeyVault/자격 증명 모음)|자격 증명 모음|privatelink.vaultcore.azure.net|
 
Azure는 공용 DNS에서 정식 이름 DNS 레코드 (CNAME)를 만들어 해결 방법을 제안 된 도메인 이름으로 리디렉션합니다. 개인 엔드포인트의 개인 IP 주소를 사용 하 여 해상도를 재정의할 수 있습니다. 
 
응용 프로그램에서 연결 URL을 변경할 필요가 없습니다. 공용 DNS를 사용 하 여 확인 하려고 하면 DNS 서버가 개인 엔드포인트으로 확인 됩니다. 이 프로세스는 응용 프로그램에 영향을 주지 않습니다. 
 
## <a name="limitations"></a>제한 사항
 
다음 표에서는 개인 엔드포인트을 사용 하는 경우의 알려진 제한 사항 목록을 제공 합니다. 


|제한 사항 |설명 |완화 방법  |
|---------|---------|---------|
|NSG (네트워크 보안 그룹) 규칙 및 사용자 정의 경로는 개인 엔드포인트에 적용 되지 않습니다.    |NSG는 개인 엔드포인트에서 지원 되지 않습니다. 개인 엔드포인트을 포함 하는 서브넷에 NSG가 연결 되어 있을 수 있지만 규칙은 개인 엔드포인트에서 처리 하는 트래픽에 적용 되지 않습니다. 서브넷에 개인 엔드포인트을 배포 하려면 [네트워크 정책 적용을 사용 하지 않도록 설정](disable-private-endpoint-network-policy.md) 해야 합니다. NSG는 동일한 서브넷에서 호스트 되는 다른 워크 로드에도 적용 됩니다. 모든 클라이언트 서브넷의 경로는/32 접두사를 사용 하 고 기본 라우팅 동작을 변경 하려면 비슷한 UDR이 필요 합니다.  | 원본 클라이언트의 아웃 바운드 트래픽에 대 한 NSG 규칙을 사용 하 여 트래픽을 제어 합니다. /32 접두사를 사용 하 여 개별 경로를 배포 하 여 개인 엔드포인트 경로를 재정의 합니다. 아웃 바운드 연결에 대 한 NSG 흐름 로그 및 모니터링 정보는 계속 지원 되며 사용할 수 있습니다.        |


## <a name="next-steps"></a>다음 단계
- [포털을 사용하여 SQL Database Server용 프라이빗 엔드포인트 만들기](create-private-endpoint-portal.md)
- [PowerShell을 사용하여 SQL Database Server용 프라이빗 엔드포인트 만들기](create-private-endpoint-powershell.md)
- [CLI를 사용하여 SQL Database Server용 프라이빗 엔드포인트 만들기](create-private-endpoint-cli.md)
- [포털을 사용하여 스토리지 계정용 프라이빗 엔드포인트 만들기](create-private-endpoint-storage-portal.md)
- [포털을 사용하여 Azure Cosmos 계정용 프라이빗 엔드포인트 만들기](../cosmos-db/how-to-configure-private-endpoints.md)
- [Azure PowerShell를 사용하여 고유의 Private Link 서비스 만들기](create-private-link-service-powershell.md)
- [포털을 사용 하 여 Azure Database for PostgreSQL 단일 서버에 대 한 사용자 고유의 개인 링크 만들기](../postgresql/howto-configure-privatelink-portal.md)
- [CLI를 사용 하 여 Azure Database for PostgreSQL 단일 서버에 대 한 사용자 고유의 개인 링크 만들기](../postgresql/howto-configure-privatelink-cli.md)
- [포털을 사용 하 여 Azure Database for MySQL에 대 한 사용자 고유의 개인 링크 만들기](../mysql/howto-configure-privatelink-portal.md)
- [CLI를 사용 하 여 Azure Database for MySQL에 대 한 사용자 고유의 개인 링크 만들기](../mysql/howto-configure-privatelink-cli.md)
- [포털을 사용 하 여 Azure Database for MariaDB에 대 한 사용자 고유의 개인 링크 만들기](../mariadb/howto-configure-privatelink-portal.md)
- [CLI를 사용 하 여 Azure Database for MariaDB에 대 한 사용자 고유의 개인 링크 만들기](../mariadb/howto-configure-privatelink-cli.md)
