---
title: Azure 개인 끝점 DNS 구성
description: Azure 개인 끝점 DNS 구성 알아보기
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: allensu
ms.openlocfilehash: 5657741a1496084b55d2f76aef12c5e84c274feb
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88918131"
---
# <a name="azure-private-endpoint-dns-configuration"></a>Azure 개인 끝점 DNS 구성


FQDN (정규화 된 도메인 이름)을 사용 하 여 연결 문자열의 일부로 개인 링크 리소스에 연결 하는 경우 할당 된 개인 IP 주소를 확인 하도록 DNS 설정을 올바르게 구성 하는 것이 중요 합니다. 기존 Microsoft Azure 서비스에는 공용 끝점을 통해 연결할 때 사용할 DNS 구성이 이미 있을 수 있습니다. 이 구성은 프라이빗 엔드포인트를 사용하여 연결하도록 재정의해야 합니다. 
 
개인 끝점과 연결 된 네트워크 인터페이스에는 특정 개인 링크 리소스에 대해 할당 된 FQDN 및 개인 IP 주소를 포함 하 여 DNS를 구성 하는 데 필요한 전체 정보 집합이 포함 되어 있습니다. 
 
다음 옵션을 사용하여 프라이빗 엔드포인트에 대한 DNS 설정을 구성할 수 있습니다. 
- **호스트 파일 (테스트에만 권장 됨)을 사용**합니다. 가상 머신의 호스트 파일을 사용하여 DNS를 재정의할 수 있습니다.  
- **프라이빗 DNS 영역 사용**. [개인 dns 영역](../dns/private-dns-privatednszone.md) 을 사용 하 여 특정 개인 끝점에 대 한 DNS 확인을 재정의할 수 있습니다. 프라이빗 DNS 영역을 가상 네트워크에 연결하여 특정 도메인을 확인할 수 있습니다.
- **DNS 전달자를 사용 합니다 (선택 사항)**. DNS 전달자를 사용 하 여 특정 개인 링크 리소스에 대 한 DNS 확인을 재정의할 수 있습니다. [DNS 서버](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)가 가상 네트워크에서 호스트되는 경우 프라이빗 DNS 영역을 사용하여 모든 프라이빗 링크 리소스에 대한 구성을 간소화하는 DNS 전달 규칙을 만들 수 있습니다.
 
> [!IMPORTANT]
> 는 공용 끝점을 확인 하는 데 적극적으로 사용 되는 영역을 재정의 하지 않는 것이 좋습니다. DNS를 공용 DNS로 전달하지 않으면 리소스에 대한 연결을 올바르게 확인할 수 없습니다. 문제를 방지하려면 서로 다른 도메인 이름을 만들거나 아래 각 서비스에 대해 제안된 이름을 따릅니다. 

## <a name="azure-services-dns-zone-configuration"></a>Azure 서비스 DNS 영역 구성
Azure 서비스는 공용 DNS 서비스에서 CNAME (정식 이름 DNS 레코드)을 만들어 확인을 제안 된 개인 도메인 이름으로 리디렉션합니다. 프라이빗 엔드포인트의 프라이빗 IP 주소를 사용하여 확인 사항을 재정의할 수 있습니다. 
 
애플리케이션에서 연결 URL을 변경할 필요가 없습니다. 공용 DNS 서비스를 사용 하 여 확인 하려고 하면 DNS 서버가 개인 끝점으로 확인 됩니다. 이 프로세스는 기존 응용 프로그램에 영향을 주지 않습니다. 

> [!IMPORTANT]
> 지정 된 형식에 대해 개인 DNS 영역을 이미 사용 하 고 있는 개인 네트워크에서는 개인 끝점 연결이 없는 경우에만 공용 리소스에 연결할 수 있습니다. 그렇지 않으면 DNS 확인 시퀀스를 완료 하기 위해 개인 DNS 영역에 해당 DNS 구성이 필요 합니다. 

Azure 서비스의 경우 다음 표에 설명된 대로 권장되는 영역 이름을 사용합니다.

| 개인 링크 리소스 유형/하위 리소스 |사설 DNS 영역 이름 | 공용 DNS 영역 전달자 |
|---|---|---|---|
| Azure Automation/(Microsoft Automation/automationAccounts)/Webhook, DSCAndHybridWorker | privatelink.azure-automation.net | azure-automation.net |
| Azure SQL Database (Microsoft .Sql/servers)/SQL Server | privatelink.database.windows.net | database.windows.net |
| Azure Synapse Analytics (Microsoft .Sql/servers)/SQL Server  | privatelink.database.windows.net | database.windows.net |
| 저장소 계정 (Microsoft Storage/storageAccounts)/Blob (blob, blob_secondary) | privatelink.blob.core.windows.net | blob.core.windows.net |
| 저장소 계정 (Microsoft Storage/storageAccounts)/테이블 (테이블, table_secondary) | privatelink.table.core.windows.net | table.core.windows.net |
| 저장소 계정 (Microsoft Storage/storageAccounts)/Queue (queue, queue_secondary) | privatelink.queue.core.windows.net | queue.core.windows.net |
| 저장소 계정 (Microsoft Storage/storageAccounts)/File (파일, file_secondary) | privatelink.file.core.windows.net | file.core.windows.net |
| 저장소 계정 (Microsoft Storage/storageAccounts)/웹 (웹, web_secondary) | privatelink.web.core.windows.net | web.core.windows.net |
| Azure Data Lake 파일 시스템 Gen2 (Microsoft Storage/storageAccounts)/Data Lake 파일 시스템 Gen2 (dfs, dfs_secondary) | privatelink.dfs.core.windows.net | dfs.core.windows.net |
| Azure Cosmos DB(Microsoft.AzureCosmosDB/databaseAccounts) / SQL | privatelink.documents.azure.com | documents.azure.com |
| Azure Cosmos DB(Microsoft.AzureCosmosDB/databaseAccounts) / MongoDB | privatelink.mongo.cosmos.azure.com | mongo.cosmos.azure.com |
| Azure Cosmos DB(Microsoft.AzureCosmosDB/databaseAccounts) / Cassandra | privatelink.cassandra.cosmos.azure.com | cassandra.cosmos.azure.com |
| Azure Cosmos DB(Microsoft.AzureCosmosDB/databaseAccounts) / Gremlin | privatelink.gremlin.cosmos.azure.com | gremlin.cosmos.azure.com |
| Azure Cosmos DB(Microsoft.AzureCosmosDB/databaseAccounts) / Table | privatelink.table.cosmos.azure.com | table.cosmos.azure.com |
| Azure Database for PostgreSQL - 단일 서버(Microsoft.DBforPostgreSQL/servers) / postgresqlServer | privatelink.postgres.database.azure.com | postgres.database.azure.com |
| Azure Database for MySQL(Microsoft.DBforMySQL/servers) / mysqlServer | privatelink.mysql.database.azure.com | mysql.database.azure.com |
| Azure Database for MariaDB(Microsoft.DBforMariaDB/servers) / mariadbServer | privatelink.mariadb.database.azure.com | mariadb.database.azure.com |
| Azure Key Vault(Microsoft.KeyVault/vaults) / 자격 증명 모음 | privatelink.vaultcore.azure.net | vault.azure.net <br> vaultcore.azure.net |
| Azure Kubernetes Service-Kubernetes API (ContainerService/managedClusters)/관리 | privatelink.{region}.azmk8s.io | {region}.azmk8s.io |
| Azure Search(Microsoft.Search/searchServices)/searchService | privatelink.search.windows.net | search.windows.net |
| Azure Container Registry(Microsoft.ContainerRegistry/registries) / 레지스트리 | privatelink.azurecr.io | azurecr.io |
| Azure App Configuration(Microsoft.AppConfiguration/configurationStores) / configurationStore | privatelink.azconfig.io | azconfig.io |
| Azure Backup(Microsoft.RecoveryServices/vaults) / 자격 증명 모음 | privatelink.{region}.backup.windowsazure.com | {region}.backup.windowsazure.com |
| Azure Event Hubs (Microsoft EventHub/네임 스페이스)/네임 스페이스 | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Service Bus(Microsoft.ServiceBus/namespaces) / 네임스페이스 | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure IoT Hub (Microsoft Devices/IotHubs)/iotHub | privatelink.azure-devices.net | azure-devices.net |
| Azure Relay(Microsoft.Relay/namespaces) / 네임스페이스 | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Event Grid(Microsoft.EventGrid/topics) / 토픽 | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure Event Grid(Microsoft.EventGrid/domains) / 도메인 | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure Web Apps (Microsoft 웹/사이트)/사이트 | privatelink.azurewebsites.net | azurewebsites.net |
| Azure Machine Learning (MachineLearningServices/작업 영역)/작업 영역 | privatelink.api.azureml.ms | api.azureml.ms |
| IoT Hub (Microsoft Devices/IotHubs)/IotHub | privatelink.azure-devices.net | azure-devices.net |
| SignalR (SignalRService/SignalR)/signalR | privatelink.service.signalr.net | service.signalr.net |
| Azure Monitor (Microsoft Insights/privateLinkScopes)/azuremonitor | privatelink.monitor.azure.com<br/> privatelink.oms.opinsights.azure.com <br/> privatelink.ods.opinsights.azure.com <br/> privatelink.agentsvc.azure-automation.com | monitor.azure.com<br/> oms.opinsights.azure.com<br/> ods.opinsights.azure.com<br/> agentsvc.azure-automation.com |
| Cognitive Services (Cognitiveservices account/accounts)/계정 | privatelink.cognitiveservices.azure.com  | cognitiveservices.azure.com  |
| Azure File Sync (Microsoft.storagesync/storageSyncServices)/afs |  privatelink.afs.azure.net  |  afs.azure.net  |

 
## <a name="dns-configuration-scenarios"></a>DNS 구성 시나리오

서비스의 FQDN은 공용 IP 주소에 대해 자동으로 확인 됩니다. 개인 끝점의 개인 IP 주소로 확인 하려면 DNS 구성을 적절 하 게 변경 해야 합니다.

DNS는 개인 끝점 IP 주소를 성공적으로 확인 하 여 응용 프로그램이 제대로 작동 하도록 하는 중요 한 구성 요소입니다.

기본 설정에 따라 다음과 같은 시나리오를 DNS 확인 통합에서 사용할 수 있습니다.

- [사용자 지정 DNS 서버가 없는 가상 네트워크 워크로드](#virtual-network-workloads-without-custom-dns-server)
- [DNS 전달자를 사용하는 온-프레미스 워크로드](#on-premises-workloads-using-a-dns-forwarder)
- [DNS 전달자를 사용 하는 가상 네트워크 및 온-프레미스 워크 로드](#virtual-network-and-on-premises-workloads-using-a-dns-forwarder)


## <a name="virtual-network-workloads-without-custom-dns-server"></a>사용자 지정 DNS 서버가 없는 가상 네트워크 작업

이 구성은 사용자 지정 DNS 서버가 없는 가상 네트워크 작업에 적합 합니다. 이 시나리오에서 클라이언트는 Azure에서 제공 하는 DNS 서비스 [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md)에 대 한 개인 끝점 IP 주소를 쿼리 합니다. Azure DNS는 프라이빗 DNS 영역의 DNS 확인을 담당합니다.

> [!NOTE]
> 이 시나리오에서는 Azure SQL Database 권장 되는 개인 DNS 영역을 사용 합니다. 다른 서비스의 경우 다음 참조를 사용 하 여 모델을 조정할 수 있습니다. [Azure 서비스 DNS 영역 구성](#azure-services-dns-zone-configuration).

제대로 구성 하려면 다음 리소스가 필요 합니다.

- 클라이언트 가상 네트워크

- [유형 A 레코드](../dns/dns-zones-records.md#record-types) 를 사용 하 여 영역 [privatelink.database.windows.net](../dns/private-dns-privatednszone.md) 사설 DNS

- 개인 끝점 정보 (FQDN 레코드 이름 및 개인 IP 주소)

다음 스크린샷에서는 개인 DNS 영역을 사용 하는 가상 네트워크 작업의 DNS 확인 시퀀스를 보여 줍니다.

:::image type="content" source="media/private-endpoint-dns/single-vnet-azure-dns.png" alt-text="단일 가상 네트워크 및 Azure 제공 DNS":::

이 모델은 동일한 프라이빗 엔드포인트에 연결된 여러 피어링된 가상 네트워크로 확장될 수 있습니다. 모든 피어링된 가상 네트워크의 프라이빗 DNS 영역에 [새 가상 네트워크 링크를 추가](../dns/private-dns-virtual-network-links.md)하여 이 작업을 수행할 수 있습니다.

> [!IMPORTANT]
> 이 구성에는 단일 개인 DNS 영역이 필요 합니다. 서로 다른 가상 네트워크에 대해 동일한 이름으로 여러 영역을 만들려면 DNS 레코드를 병합 하는 작업을 수동으로 수행 해야 합니다.

> [!IMPORTANT]
> 다른 구독의 허브 및 스포크 모델에서 개인 끝점을 사용 하는 경우 허브에서 동일한 개인 DNS 영역을 다시 사용 합니다.

이 시나리오에서는 공통 개인 끝점을 공유 하는 스포크 네트워크를 사용 하는 [허브 및 스포크](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) 네트워킹 토폴로지가 있으며 모든 스포크 가상 네트워크는 동일한 개인 DNS 영역에 연결 됩니다. 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="Azure 제공 DNS를 사용하는 허브 및 스포크":::

## <a name="on-premises-workloads-using-a-dns-forwarder"></a>DNS 전달자를 사용하는 온-프레미스 워크로드

온-프레미스 작업에서 개인 IP 주소에 대 한 개인 끝점의 FQDN을 확인 하려면 DNS 전달자를 사용 하 여 azure에서 Azure 서비스 [공용 DNS 영역](#azure-services-dns-zone-configuration) 에 대 한 확인을 배포 해야 합니다.

다음 시나리오는 Azure에서 DNS 전달자를 사용 하는 온-프레미스 네트워크에 적합 합니다 .이는 azure에서 제공 하는 dns [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md)서버 수준 전달자를 통해 모든 dns 쿼리를 확인 하는 일을 담당 합니다. 

> [!NOTE]
> 이 시나리오에서는 Azure SQL Database 권장 되는 개인 DNS 영역을 사용 합니다.다른 서비스의 경우 다음 참조를 사용 하 여 모델을 조정할 수 있습니다. [Azure 서비스 DNS 영역 구성](#azure-services-dns-zone-configuration).

제대로 구성 하려면 다음 리소스가 필요 합니다.

- 온-프레미스 네트워크
- 가상 네트워크 [온-프레미스에 연결됨](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)
- Azure에 배포된 DNS 전달자 
-  [privatelink.database.windows.net](../dns/private-dns-privatednszone.md)    [유형 A 레코드](../dns/dns-zones-records.md#record-types) 를 사용 하는 사설 DNS 영역 privatelink.database.windows.net
- 개인 끝점 정보 (FQDN 레코드 이름 및 개인 IP 주소)

다음 다이어그램은 Azure에 배포 된 DNS 전달자를 사용 하는 온-프레미스 네트워크의 DNS 확인 시퀀스를 보여 줍니다. 여기서는 [가상 네트워크에 연결 된](../dns/private-dns-virtual-network-links.md)개인 DNS 영역에 의해 확인 됩니다.

:::image type="content" source="media/private-endpoint-dns/on-premises-using-azure-dns.png" alt-text="Azure DNS를 사용하는 온-프레미스":::

이 구성은 이미 DNS 솔루션이 있는 온-프레미스 네트워크에 대해 확장할 수 있습니다. 
온-프레미스 DNS 솔루션은 Azure에 배포 된 DNS 전달자를 참조 하는 [조건부 전달자](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) 를 통해 AZURE DNS에 DNS 트래픽을 전달 하도록 구성 해야 합니다.

> [!NOTE]
> 이 시나리오에서는 Azure SQL Database 권장 되는 개인 DNS 영역을 사용 합니다. 다른 서비스의 경우 다음 참조를 사용 하 여 모델을 조정할 수 있습니다. [Azure 서비스 DNS 영역 구성](#azure-services-dns-zone-configuration)

제대로 구성 하려면 다음 리소스가 필요 합니다.

- 사용자 지정 DNS 솔루션이 있는 온-프레미스 네트워크 
- 가상 네트워크 [온-프레미스에 연결됨](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)
- Azure에 배포된 DNS 전달자
-  [privatelink.database.windows.net](../dns/private-dns-privatednszone.md)     [유형 A 레코드](../dns/dns-zones-records.md#record-types) 를 사용 하는 사설 DNS 영역 privatelink.database.windows.net
- 개인 끝점 정보 (FQDN 레코드 이름 및 개인 IP 주소)

다음 다이어그램은 [가상 네트워크에 연결](../dns/private-dns-virtual-network-links.md)된 개인 dns 영역에서 확인 되는 AZURE에 dns 트래픽을 조건부로 전달 하는 온-프레미스 네트워크의 dns 확인 시퀀스를 보여 줍니다.

> [!IMPORTANT]
> 권장 되는 [공용 DNS 영역 전달자](#azure-services-dns-zone-configuration)에 대 한 조건부 전달이 이루어져야 합니다.예를 들면  `database.windows.net`    **privatelink**. database.windows.net 대신.

:::image type="content" source="media/private-endpoint-dns/on-premises-forwarding-to-azure.png" alt-text="Azure DNS로 온-프레미스 전달":::

## <a name="virtual-network-and-on-premises-workloads-using-a-dns-forwarder"></a>DNS 전달자를 사용 하는 가상 네트워크 및 온-프레미스 워크 로드

가상 및 온-프레미스 네트워크에서 개인 끝점에 액세스 해야 하는 워크 로드에 적합 한 일반적인 접근 방식은 공유 DNS 전달자를 사용 하 여 Azure에 배포 된 Azure 서비스 [공용 dns 영역](#azure-services-dns-zone-configuration) 을 확인 해야 합니다.

다음 시나리오는 Azure에 DNS 전달 자가 있는 온-프레미스 네트워크와 공유 허브 네트워크에 있는 개인 끝점에 대 한 액세스가 필요한 가상 네트워크에 적합 합니다.  

이 DNS 전달자는 Azure에서 제공 하는 DNS 서비스 [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md)서버 수준 전달자를 통해 모든 dns 쿼리를 확인 합니다.

> [!IMPORTANT]
> 이 구성에는 단일 개인 DNS 영역이 필요 합니다. 온-프레미스 및 [피어 링 가상 네트워크](../virtual-network/virtual-network-peering-overview.md) 에서 만든 모든 클라이언트 연결은 동일한 개인 DNS 영역도 사용 해야 합니다.

> [!NOTE]
> 이 시나리오에서는 Azure SQL Database 권장 되는 개인 DNS 영역을 사용 합니다. 다른 서비스의 경우 다음 참조를 사용 하 여 모델을 조정할 수 있습니다. [Azure 서비스 DNS 영역 구성](#azure-services-dns-zone-configuration).

제대로 구성 하려면 다음 리소스가 필요 합니다.

- 온-프레미스 네트워크
- 가상 네트워크 [온-프레미스에 연결됨](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)
- [피어 링 가상 네트워크](../virtual-network/virtual-network-peering-overview.md) 
- Azure에 배포된 DNS 전달자
-  [privatelink.database.windows.net](../dns/private-dns-privatednszone.md)     [유형 A 레코드](../dns/dns-zones-records.md#record-types) 를 사용 하는 사설 DNS 영역 privatelink.database.windows.net
- 개인 끝점 정보 (FQDN 레코드 이름 및 개인 IP 주소)

다음 다이어그램에서는 Azure에 배포 된 DNS 전달자를 사용 하는 온-프레미스 및 가상 네트워크의 DNS 확인 시퀀스를 보여 줍니다. 여기서는 [가상 네트워크에 연결 된](../dns/private-dns-virtual-network-links.md)개인 DNS 영역에 의해 확인 됩니다.

:::image type="content" source="media/private-endpoint-dns/hybrid-scenario.png" alt-text="하이브리드 시나리오":::

## <a name="next-steps"></a>다음 단계
- [개인 끝점에 대 한 자세한 정보](private-endpoint-overview.md)
