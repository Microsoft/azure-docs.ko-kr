---
title: Azure 가상 네트워크 서비스 엔드포인트
titlesuffix: Azure Virtual Network
description: 서비스 엔드포인트를 사용하여 가상 네트워크에서 Azure 리소스에 대한 직접 액세스를 사용하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: sumeetmittal
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2019
ms.author: sumi
ms.custom: ''
ms.openlocfilehash: 72c2c90f7a71bd9bf251adb492168fa5d2baa60a
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74378688"
---
# <a name="virtual-network-service-endpoints"></a>Virtual Network 서비스 엔드포인트

VNet (Virtual Network) 서비스 엔드포인트은 가상 네트워크 개인 주소 공간을 확장 합니다. 또한 엔드포인트은 직접 연결을 통해 VNet의 id를 Azure 서비스에 확장 합니다. 엔드포인트를 사용하면 가상 네트워크에 대해 중요한 Azure 서비스 리소스를 보호할 수 있습니다. VNet에서 Azure 서비스에 대한 트래픽은 Microsoft Azure 백본 네트워크에 항상 유지됩니다.

이 기능은 다음과 같은 Azure 서비스 및 지역에서 사용할 수 있습니다. *\** 리소스는 괄호 안에 있습니다. 서비스에 대 한 서비스 엔드포인트을 구성 하는 동안 서브넷 쪽에서이 리소스를 사용 하도록 설정 합니다.

**일반 공급**

- **[Azure Storage](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)** (*Microsoft 저장소*): 모든 Azure 지역에서 일반 공급 됩니다.
- **[Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft .sql*): 모든 Azure 지역에서 일반 공급 됩니다.
- **[Azure SQL Data Warehouse](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft .sql*): 모든 Azure 지역에서 일반 공급 됩니다.
- **[Azure Database for PostgreSQL server](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft .sql*): 데이터베이스 서비스를 사용할 수 있는 Azure 지역에서 일반적으로 사용할 수 있습니다.
- **[Azure Database for MySQL server](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft .sql*): 데이터베이스 서비스를 사용할 수 있는 Azure 지역에서 일반적으로 사용할 수 있습니다.
- **[Azure Database for MariaDB](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)** (*Microsoft .sql*): 데이터베이스 서비스를 사용할 수 있는 Azure 지역에서 일반적으로 사용할 수 있습니다.
- **[Azure Cosmos DB](../cosmos-db/vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*microsoft.azurecosmosdb*): 모든 Azure 지역에서 일반 공급 됩니다.
- **[Azure Key Vault](../key-vault/key-vault-overview-vnet-service-endpoints.md)** (*Microsoft. keyvault*): 모든 Azure 지역에서 일반 공급 됩니다.
- **[Azure Service Bus](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*ServiceBus*): 모든 Azure 지역에서 일반 공급 됩니다.
- **[Azure Event Hubs](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft EventHub*): 모든 azure 지역에서 일반 공급 됩니다.
- **[Azure Data Lake Store Gen 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*AzureActiveDirectory*): ADLS Gen1를 사용할 수 있는 모든 Azure 지역에서 일반 공급 됩니다.
- **[Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)** : 일반적으로 App Service를 사용할 수 있는 모든 Azure 지역에서 사용할 수 있습니다.

**공개 미리 보기**

- **[Azure Container Registry](../container-registry/container-registry-vnet.md)** (*microsoft.containerregistry*): Azure Container Registry를 사용할 수 있는 모든 Azure 지역에서 미리 보기를 사용할 수 있습니다.

최신 알림은 [Azure Virtual Network 업데이트](https://azure.microsoft.com/updates/?product=virtual-network) 페이지를 확인하세요.

## <a name="key-benefits"></a>주요 이점

서비스 엔드포인트는 다음과 같은 이점을 제공합니다.

- **Azure 서비스 리소스의 보안 향상**: VNet 개인 주소 공간이 겹칠 수 있습니다. 중복 된 공백은 VNet에서 시작 되는 트래픽을 고유 하 게 식별 하는 데 사용할 수 없습니다. 서비스 엔드포인트은 VNet id를 서비스로 확장 하 여 가상 네트워크에 대 한 Azure 서비스 리소스를 보호 하는 기능을 제공 합니다. 가상 네트워크에서 서비스 엔드포인트을 사용 하도록 설정 하면 가상 네트워크 규칙을 추가 하 여 가상 네트워크에 대 한 Azure 서비스 리소스를 보호할 수 있습니다. 규칙 추가는 리소스에 대 한 공용 인터넷 액세스를 완전히 제거 하 고 가상 네트워크의 트래픽만 허용 하 여 향상 된 보안을 제공 합니다.
- **가상 네트워크에서 azure 서비스 트래픽에 대 한 최적의 라우팅**: 현재 온-프레미스 및/또는 가상 어플라이언스에 인터넷 트래픽을 강제 하는 가상 네트워크의 모든 경로는 Azure 서비스 트래픽이 인터넷 트래픽과 동일한 경로를 사용 하도록 합니다. 서비스 엔드포인트는 Azure 트래픽에 대한 최적의 라우팅을 제공합니다. 

  엔드포인트는 가상 네트워크의 서비스 트래픽을 직접 Microsoft Azure 백본 네트워크의 서비스로 항상 이동시킵니다. 트래픽을 Azure 백본 네트워크에 유지하면 서비스 트래픽에 영향을 주지 않고 강제 터널링을 통해 가상 네트워크의 아웃바운드 인터넷 트래픽을 계속 감사하고 모니터링할 수 있습니다. 사용자 정의 경로 및 강제 터널링에 대 한 자세한 내용은 [Azure 가상 네트워크 트래픽 라우팅](virtual-networks-udr-overview.md)을 참조 하세요.
- **관리 오버 헤드를 덜 사용하여 간단히 설정**: IP 방화벽을 통해 Azure 리소스를 보호하기 위해 가상 네트워크에서 예약된 공용 IP 주소가 더 이상 필요하지 않습니다. 서비스 엔드포인트을 설정 하는 데 필요한 NAT (Network Address Translation) 또는 게이트웨이 장치가 없습니다. 서브넷을 간단히 클릭 하 여 서비스 엔드포인트을 구성할 수 있습니다. 엔드포인트을 유지 하기 위한 추가 오버 헤드가 없습니다.

## <a name="limitations"></a>제한 사항

- 이 기능은 Azure Resource Manager 배포 모델을 통해 배포된 가상 네트워크에만 사용할 수 있습니다.
- 엔드포인트는 Azure 가상 네트워크에서 구성된 서브넷에서 활성화됩니다. 온-프레미스에서 Azure 서비스로의 트래픽에 엔드포인트을 사용할 수 없습니다. 자세한 내용은 [온-프레미스에서 Azure 서비스 액세스 보호](#secure-azure-services-to-virtual-networks) 를 참조 하세요.
- Azure SQL의 경우 서비스 엔드포인트는 가상 네트워크의 지역 내에서 Azure 서비스 트래픽에만 적용됩니다. Azure Storage의 경우 엔드포인트은 가상 네트워크를 배포 하 여 GRS (읽기 액세스 지역 중복 저장소) 및 GRS (지역 중복 저장소) 트래픽을 지원 하기 위해 쌍을 이루는 지역을 포함 하도록 확장 됩니다. 자세한 내용은 [Azure 쌍을 이루는 지역](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions)을 참조하세요.
- Azure Data Lake Storage (ADLS) Gen 1의 경우 동일한 지역 내의 가상 네트워크에 대해서만 VNet 통합 기능을 사용할 수 있습니다. 또한 ADLS Gen1의 가상 네트워크 통합은 가상 네트워크와 Azure Active Directory (Azure AD) 간의 가상 네트워크 서비스 엔드포인트 보안을 사용 하 여 액세스 토큰에 추가 보안 클레임을 생성 합니다. 그런 다음, 이러한 클레임을 사용하여 Data Lake Storage Gen1 계정에 대해 가상 네트워크를 인증하고 액세스를 허용합니다. 서비스 엔드포인트 지원 서비스에 나열 된 *AzureActiveDirectory* 태그는 서비스 엔드포인트을 ADLS Gen 1에 지 원하는 데만 사용 됩니다. Azure AD는 서비스 엔드포인트을 기본적으로 지원 하지 않습니다. Azure Data Lake Store Gen 1 VNet 통합에 대 한 자세한 내용은 [Azure Data Lake Storage Gen1의 네트워크 보안](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조 하세요.

## <a name="secure-azure-services-to-virtual-networks"></a>가상 네트워크에 대 한 Azure 서비스 보안 유지

- 가상 네트워크 서비스 엔드포인트는 Azure 서비스에 가상 네트워크의 ID를 제공합니다. 가상 네트워크에서 서비스 엔드포인트을 사용 하도록 설정 하면 가상 네트워크 규칙을 추가 하 여 가상 네트워크에 대 한 Azure 서비스 리소스를 보호할 수 있습니다.
- 현재 가상 네트워크의 Azure 서비스 트래픽은 공용 IP 주소를 원본 IP 주소로 사용합니다. 서비스 엔드포인트에서 서비스 트래픽은 가상 네트워크의 Azure 서비스에 액세스할 때 가상 네트워크 프라이빗 주소를 원본 IP 주소로 사용하도록 전환됩니다. 이 스위치를 사용하면 IP 방화벽에서 사용되는 예약된 공용 IP 주소가 필요 없이 서비스에 액세스할 수 있습니다.

   >[!NOTE]
   > 서비스 엔드포인트를 사용하면 서비스 트래픽에 대한 서브넷의 가상 머신 원본 IP 주소가 공용 IPv4 주소에서 프라이빗 IPv4 주소로 전환됩니다. Azure 공용 IP 주소를 사용하는 기존 Azure 서비스 방화벽 규칙은 더 이상 이 스위치에 작동하지 않습니다. 서비스 엔드포인트를 설정하기 전에 Azure 서비스 방화벽 규칙에서 이 스위치를 허용해야 합니다. 서비스 엔드포인트를 구성하는 동안 이 서브넷의 서비스 트래픽이 일시적으로 중단될 수도 있습니다. 
 
## <a name="secure-azure-service-access-from-on-premises"></a>온-프레미스에서 Azure 서비스 액세스 보안

  기본적으로 가상 네트워크에 대해 보호 된 Azure 서비스 리소스는 온-프레미스 네트워크에서 연결할 수 없습니다. 온-프레미스의 트래픽을 허용하려는 경우 온-프레미스 또는 ExpressRoute의 공용 IP 주소(일반적으로 NAT)도 허용해야 합니다. Azure 서비스 리소스에 대 한 IP 방화벽 구성을 통해 이러한 IP 주소를 추가할 수 있습니다.

  Express 경로: 공용 피어 링 또는 온-프레미스의 Microsoft 피어 링에 대해 [express](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 경로를 사용 하는 경우 사용 중인 NAT IP 주소를 식별 해야 합니다. 공용 피어 링의 경우 각 Express 경로 회로는 트래픽이 Microsoft Azure 네트워크 백본으로 들어갈 때 Azure 서비스 트래픽에 적용 되는 두 개의 NAT IP 주소를 기본적으로 사용 합니다. Microsoft 피어 링의 경우 NAT IP 주소는 서비스 공급자가 제공 하거나 제공 하는 고객입니다. 서비스 리소스에 대 한 액세스를 허용 하려면 리소스 IP 방화벽 설정에서 이러한 공용 IP 주소를 허용 해야 합니다. 공용 피어 링 (Express 경로) 회로 IP 주소를 찾으려면 Azure Portal를 통해 Express 경로를 [사용 하 여 지원 티켓을 엽니다](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) . Express 경로 공용 및 Microsoft 피어 링의 NAT에 대 한 자세한 내용은 [express 경로 nat 요구 사항](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)을 참조 하세요.

![Virtual Network에 대한 Azure 서비스 보호](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>구성

- 가상 네트워크의 서브넷에서 서비스 엔드포인트을 구성 합니다. 엔드포인트는 해당 서브넷 내에서 실행되는 모든 컴퓨팅 인스턴스를 사용합니다.
- 모든 지원 되는 Azure 서비스 (예: 서브넷에 대해 지원 되는 모든 Azure 서비스 Azure Storage 또는 Azure SQL Database)에 대해 여러 서비스 엔드포인트을 구성할 수 있습니다.
- Azure SQL Database의 경우 가상 네트워크는 Azure 서비스 리소스와 동일한 지역에 있어야 합니다. GRS 및 RA-GRS Azure Storage 계정을 사용하는 경우 기본 계정은 가상 네트워크와 동일한 지역에 있어야 합니다. 다른 모든 서비스의 경우 모든 지역에서 가상 네트워크에 대 한 Azure 서비스 리소스를 보호할 수 있습니다. 
- 엔드포인트가 구성된 가상 네트워크는 Azure 서비스 리소스와 동일하거나 다른 구독에 구성될 수 있습니다. 엔드포인트를 설정하고 Azure 서비스를 보호하는 데 필요한 사용 권한에 대한 자세한 내용은 [프로비전](#provisioning)을 참조하세요.
- 지원되는 서비스의 경우 서비스 엔드포인트를 사용하여 가상 네트워크에 대한 기존 또는 새로운 리소스를 보호할 수 있습니다.

### <a name="considerations"></a>고려 사항

- 서비스 엔드포인트을 사용 하도록 설정한 후에는 서브넷 스위치에 있는 가상 머신의 원본 IP 주소입니다. 원본 IP 주소는 해당 서브넷의 서비스와 통신할 때 공용 IPv4 주소를 사용 하 여 개인 IPv4 주소를 사용 하는 것으로 전환 됩니다. 이 전환 중에 서비스에 대한 기존의 모든 오픈 TCP 연결이 닫힙니다. 서브넷의 서비스에 서비스 엔드포인트를 사용하거나 사용하지 않도록 설정하는 경우 중요한 작업이 실행되지 않아야 합니다. 또한 IP 주소를 전환한 후에 애플리케이션이 Azure 서비스에 자동으로 연결될 수 있어야 합니다.

  IP 주소 전환은 가상 네트워크의 서비스 트래픽에만 영향을 줍니다. 가상 컴퓨터에 할당 된 공용 IPv4 주소로 주소를 지정 하는 다른 트래픽에는 영향을 주지 않습니다. Azure 서비스의 경우 Azure 공용 IP 주소를 사용하는 기존 방화벽 규칙이 있는 경우 이러한 규칙은 가상 네트워크 프라이빗 주소로 전환하는 동시에 작동이 중지됩니다.
- 서비스 엔드포인트을 사용 하 여 Azure 서비스에 대 한 DNS 항목은 현재 상태로 유지 되 고 Azure 서비스에 할당 된 공용 IP 주소로 계속 확인 됩니다.

- 서비스 엔드포인트의 NSG(네트워크 보안 그룹):
  - 기본적으로 NSGs는 아웃 바운드 인터넷 트래픽을 허용 하 고 VNet에서 Azure 서비스로의 트래픽만 허용 합니다. 이 트래픽은 계속 해 서 서비스 엔드포인트을 그대로 사용 합니다. 
  - 모든 아웃 바운드 인터넷 트래픽을 거부 하 고 특정 Azure 서비스에 대 한 트래픽만 허용 하려면 NSGs의 [서비스 태그](security-overview.md#service-tags) 를 사용 하면 됩니다. NSG 규칙에서 지원 되는 Azure 서비스를 대상으로 지정할 수 있으며, Azure는 각 태그의 IP 주소에 대 한 유지 관리도 제공 합니다. 자세한 내용은 [NSG의 Azure 서비스 태그](security-overview.md#service-tags)를 참조하세요. 

### <a name="scenarios"></a>시나리오

- **피어링되거나 연결된 여러 가상 네트워크**: 하나의 가상 네트워크 또는 여러 가상 네트워크의 여러 서브넷에 대한 Azure 서비스를 보호하려면 각 서브넷에서 서비스 엔드포인트를 독립적으로 활성화하고 모든 서브넷에 대한 Azure 서비스 리소스를 보호할 수 있습니다.
- **가상 네트워크에서 azure 서비스로 아웃 바운드 트래픽 필터링**: 가상 네트워크에서 azure 서비스로 전송 되는 트래픽을 검사 하거나 필터링 하려는 경우 가상 네트워크 내에 네트워크 가상 어플라이언스를 배포할 수 있습니다. 네트워크 가상 어플라이언스를 배포한 서브넷에 서비스 엔드포인트를 적용하고 이 서브넷에 대한 Azure 서비스 리소스만을 보호할 수 있습니다. 이 시나리오는 네트워크 가상 어플라이언스 필터링을 사용 하 여 가상 네트워크에서 특정 Azure 리소스로의 Azure 서비스 액세스만 제한 하려는 경우에 유용할 수 있습니다. 자세한 내용은 [네트워크 가상 어플라이언스에서 송신](/azure/architecture/reference-architectures/dmz/nva-ha)을 참조하세요.
- **가상 네트워크에 직접 배포 된 서비스에 대 한 azure 리소스 보안**: 다양 한 azure 서비스를 가상 네트워크의 특정 서브넷에 직접 배포할 수 있습니다. 관리되는 서비스 서브넷에서 서비스 엔드포인트를 설정하여 [관리되는 서비스](virtual-network-for-azure-services.md) 서브넷에 대한 Azure 서비스 리소스를 보호할 수 있습니다.
- **Azure 가상 컴퓨터의 디스크 트래픽**: 관리 되는 디스크 및 관리 되지 않는 디스크에 대 한 가상 컴퓨터 디스크 트래픽은 Azure Storage의 서비스 엔드포인트 라우팅 변경의 영향을 받지 않습니다. 이 트래픽에는 탑재 및 탑재 해제 뿐만 아니라 diskIO도 포함 됩니다. 서비스 엔드포인트을 통해 네트워크를 선택 하 고 [네트워크 규칙을 Azure Storage](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)하 여 페이지 blob에 대 한 REST 액세스를 제한할 수 있습니다. 

### <a name="logging-and-troubleshooting"></a>로깅 및 문제 해결

특정 서비스에 대 한 서비스 엔드포인트을 구성한 후에는 서비스 엔드포인트 경로가 다음에 의해 적용 되는지 확인 합니다. 
 
- 서비스 진단에서 모든 서비스 요청의 원본 IP 주소 유효성을 검사합니다. 서비스 엔드포인트에서 모든 새로운 요청은 요청의 원본 IP 주소를 가상 네트워크 개인 IP 주소로 표시하고 가상 네트워크에서 요청한 클라이언트에 할당됩니다. 엔드포인트가 없는 경우 주소는 Azure 공용 IP 주소입니다.
- 서브넷의 모든 네트워크 인터페이스에서 유효 경로를 볼 수 있습니다. 서비스에 대한 경로:
  - 각 서비스의 주소를 지정하는 구체적인 기본 경로를 표시합니다.
  - *VirtualNetworkServiceEndpoint*의 nextHopType이 있습니다.
  - 강제 터널링 경로에 비해 서비스에 대 한 직접 연결이 적용 됨을 나타냅니다.

>[!NOTE]
> 서비스 엔드포인트 경로는 Azure 서비스의 주소 접두사에 대한 BGP 또는 UDR 경로를 재정의합니다. 자세한 내용은 [유효 경로를 사용 하 여 문제 해결](diagnose-network-routing-problem.md)을 참조 하세요.

## <a name="provisioning"></a>프로비전

가상 네트워크에서 가상 네트워크에 대 한 쓰기 권한이 있는 사용자가 독립적으로 서비스 엔드포인트을 구성할 수 있습니다. VNet에 대 한 Azure 서비스 리소스를 보호 하려면 사용자는 추가 된 서브넷에 대해 *Microsoft. Network/virtualNetworks/서브넷/joinViaServiceEndpoint/action* 에 대 한 사용 권한이 있어야 합니다. 기본 제공 서비스 관리자 역할은 기본적으로이 권한을 포함 합니다. 사용자 지정 역할을 만들어 사용 권한을 수정할 수 있습니다.

기본 제공 역할에 대 한 자세한 내용은 [Azure 리소스에 대 한 기본 제공 역할](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조 하세요. 사용자 지정 역할에 특정 사용 권한을 할당 하는 방법에 대 한 자세한 내용은 [Azure 리소스에 대 한 사용자 지정 역할](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 참조 하세요.

가상 네트워크 및 Azure 서비스 리소스가 동일한 구독이나 다른 구독에 있을 수 있습니다. 가상 네트워크 및 Azure 서비스 리소스가 다른 구독에 있는 경우 동일한 AD(Active Directory) 테넌트 아래에 있어야 합니다. 

## <a name="pricing-and-limits"></a>가격 책정 및 제한

서비스 엔드포인트 사용에 대 한 추가 비용은 없습니다. 현재 Azure 서비스 (Azure Storage, Azure SQL Database 등)에 대 한 가격 책정 모델은 현재 상태로 적용 됩니다.

가상 네트워크의 총 서비스 엔드포인트 수에는 제한이 없습니다.

Azure Storage 계정과 같은 특정 Azure 서비스는 리소스를 보호 하는 데 사용 되는 서브넷의 수에 제한을 적용할 수 있습니다. 자세한 내용은 [다음 단계](#next-steps) 섹션에서 다양 한 서비스에 대 한 설명서를 참조 하세요.

## <a name="vnet-service-endpoint-policies"></a>VNet 서비스 엔드포인트 정책 

VNet 서비스 엔드포인트 정책을 통해 Azure 서비스에 대 한 가상 네트워크 트래픽을 필터링 할 수 있습니다. 이 필터는 서비스 엔드포인트에 대 한 특정 Azure 서비스 리소스만 허용 합니다. 서비스 엔드포인트 정책은 Azure 서비스의 가상 네트워크 트래픽에 대한 세부적인 액세스 제어를 제공합니다. 자세한 내용은 [Virtual Network 서비스 엔드포인트 정책](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)을 참조 하세요.

## <a name="faqs"></a>FAQ

Faq는 [Virtual Network 서비스 엔드포인트 faq](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#virtual-network-service-endpoints)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [가상 네트워크 서비스 엔드포인트 구성](tutorial-restrict-network-access-to-resources.md)
- [가상 네트워크에 대 한 Azure Storage 계정 보안](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [가상 네트워크에 대 한 Azure SQL Database 보안](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [가상 네트워크에 대 한 Azure SQL Data Warehouse 보안](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [가상 네트워크의 Azure 서비스 통합](virtual-network-for-azure-services.md)
- [Virtual Network 서비스 엔드포인트 정책](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)
- [Azure Resource Manager 템플릿](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration)

