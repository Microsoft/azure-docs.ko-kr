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
ms.date: 08/15/2018
ms.author: sumeet.mittal
ms.custom: ''
ms.openlocfilehash: 73621c3bbab7f0c49feacab29e1e5de1792b80e4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61032586"
---
# <a name="virtual-network-service-endpoints"></a>Virtual Network 서비스 엔드포인트

VNet(Virtual Network) 서비스 엔드포인트는 직접 연결을 통해 가상 네트워크 개인 주소 공간 및 Azure 서비스에 대한 VNet의 ID를 확장합니다. 엔드포인트를 사용하면 가상 네트워크에 대해 중요한 Azure 서비스 리소스를 보호할 수 있습니다. VNet에서 Azure 서비스에 대한 트래픽은 Microsoft Azure 백본 네트워크에 항상 유지됩니다.

이 기능은 다음과 같은 Azure 서비스 및 지역에서 제공됩니다.

**일반 공급**

- **[Azure Storage](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)**: 모든 Azure 지역에서 일반 공급됩니다.
- **[Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)**: 모든 Azure 지역에서 일반 공급됩니다.
- **[Azure SQL Data Warehouse](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)**: 모든 Azure 지역에서 일반 공급됩니다.
- **[Azure Database for PostgreSQL 서버](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)**: 일반적으로 데이터베이스 서비스를 사용할 수 있는 Azure 지역에 제공됩니다.
- **[Azure Database for MySQL 서버](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)**: 일반적으로 데이터베이스 서비스를 사용할 수 있는 Azure 지역에 제공됩니다.
- **[Azure Database for MariaDB](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)**: 일반적으로 데이터베이스 서비스를 사용할 수 있는 Azure 지역에 제공됩니다.
- **[Azure Cosmos DB](../cosmos-db/vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)**: 모든 Azure 지역에서 일반 공급됩니다.
- **[Azure Key Vault](../key-vault/key-vault-overview-vnet-service-endpoints.md)**: 모든 Azure 지역에서 일반 공급됩니다.
- **[Azure Service Bus](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)**: 모든 Azure 지역에서 일반 공급됩니다.
- **[Azure Event Hubs](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)**: 모든 Azure 지역에서 일반 공급됩니다.
- **[Azure Data Lake Store Gen 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)**: ADLS Gen1을 사용할 수 있는 모든 Azure 지역에서 일반 공급됩니다.

**공개 미리 보기**

- **[Azure Container Registry](../container-registry/container-registry-vnet.md)**: Azure Container Registry를 사용할 수 있는 모든 Azure 지역에서 사용할 수 있는 미리 봅니다.

최신 알림은 [Azure Virtual Network 업데이트](https://azure.microsoft.com/updates/?product=virtual-network) 페이지를 확인하세요.

## <a name="key-benefits"></a>주요 이점

서비스 엔드포인트는 다음과 같은 이점을 제공합니다.

- **Azure 서비스 리소스의 보안 향상**: VNet 개인 주소 공간은 중복될 수 있으므로 VNet에서 발생한 트래픽을 고유하게 식별하는 데 사용할 수 없습니다. 서비스 엔드포인트는 VNet ID를 서비스로 확장하여 Azure 서비스 리소스를 가상 네트워크에 안전하게 저장할 수 있는 기능을 제공합니다. 서비스 엔드포인트를 가상 네트워크에 사용할 수 있게 되면 리소스에 가상 네트워크 규칙을 추가하여 가상 네트워크에 대한 Azure 서비스 리소스를 보호할 수 있습니다. 그러면 리소스에 대한 공용 인터넷 액세스를 완전히 제거하고 가상 네트워크의 트래픽만 허용하여 보안이 향상됩니다.
- **Virtual Network의 Azure 서비스 트래픽에 대한 최적의 라우팅**: 현재 온-프레미스 및/또는 가상 어플라이언스를 통해 인터넷 트래픽을 강제하는 가상 네트워크의 경로(강제 터널링이라고 함)는 Azure 서비스 트래픽이 인터넷 트래픽과 동일한 경로를 사용하도록 강제할 수도 있습니다. 서비스 엔드포인트는 Azure 트래픽에 대한 최적의 라우팅을 제공합니다. 

  엔드포인트는 가상 네트워크의 서비스 트래픽을 직접 Microsoft Azure 백본 네트워크의 서비스로 항상 이동시킵니다. 트래픽을 Azure 백본 네트워크에 유지하면 서비스 트래픽에 영향을 주지 않고 강제 터널링을 통해 가상 네트워크의 아웃바운드 인터넷 트래픽을 계속 감사하고 모니터링할 수 있습니다. [사용자 정의 경로 및 강제 터널링](virtual-networks-udr-overview.md)에 대해 알아봅니다.
- **관리 오버헤드를 덜 사용하여 간단히 설정**: IP 방화벽을 통해 Azure 리소스를 보호하기 위해 가상 네트워크에서 예약된 공용 IP 주소가 더 이상 필요하지 않습니다. 서비스 엔드포인트를 설정하는 데 NAT 또는 게이트웨이 디바이스가 필요하지 않습니다. 서브넷을 간단히 클릭하여 서비스 엔드포인트를 구성할 수 있습니다. 엔드포인트를 유지하기 위한 추가 오버헤드가 없습니다.

## <a name="limitations"></a>제한 사항

- 이 기능은 Azure Resource Manager 배포 모델을 통해 배포된 가상 네트워크에만 사용할 수 있습니다.
- 엔드포인트는 Azure 가상 네트워크에서 구성된 서브넷에서 활성화됩니다. 프레미스에서 Azure 서비스로의 트래픽에 엔드포인트를 사용할 수 없습니다. 자세한 내용은 [온-프레미스에서 Azure 서비스 액세스 보호](#securing-azure-services-to-virtual-networks)를 참조하세요.
- Azure SQL의 경우 서비스 엔드포인트는 가상 네트워크의 지역 내에서 Azure 서비스 트래픽에만 적용됩니다. Azure Storage의 경우 RA-GRS 및 GRS 트래픽을 지원하기 위해 가상 네트워크가 배포된 쌍을 이루는 지역을 포함하도록 엔드포인트가 확장됩니다. [Azure 쌍을 이루는 지역](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions)에 대해 자세히 알아보세요.
- ADLS Gen 1의 경우 VNet 통합 기능은 동일한 지역 내의 가상 네트워크에서만 사용할 수 있습니다.

## <a name="securing-azure-services-to-virtual-networks"></a>Virtual Network에 대한 Azure 서비스 보호

- 가상 네트워크 서비스 엔드포인트는 Azure 서비스에 가상 네트워크의 ID를 제공합니다. 서비스 엔드포인트를 가상 네트워크에 사용할 수 있게 되면 리소스에 가상 네트워크 규칙을 추가하여 가상 네트워크에 대한 Azure 서비스 리소스를 보호할 수 있습니다.
- 현재 가상 네트워크의 Azure 서비스 트래픽은 공용 IP 주소를 원본 IP 주소로 사용합니다. 서비스 엔드포인트에서 서비스 트래픽은 가상 네트워크의 Azure 서비스에 액세스할 때 가상 네트워크 개인 주소를 원본 IP 주소로 사용하도록 전환됩니다. 이 스위치를 사용하면 IP 방화벽에서 사용되는 예약된 공용 IP 주소가 필요 없이 서비스에 액세스할 수 있습니다.

>[!NOTE]
> 서비스 엔드포인트를 사용하면 서비스 트래픽에 대한 서브넷의 가상 머신 원본 IP 주소가 공용 IPv4 주소에서 개인 IPv4 주소로 전환됩니다. Azure 공용 IP 주소를 사용하는 기존 Azure 서비스 방화벽 규칙은 더 이상 이 스위치에 작동하지 않습니다. 서비스 엔드포인트를 설정하기 전에 Azure 서비스 방화벽 규칙에서 이 스위치를 허용해야 합니다. 서비스 엔드포인트를 구성하는 동안 이 서브넷의 서비스 트래픽이 일시적으로 중단될 수도 있습니다. 
 
- __온-프레미스에서 Azure 서비스 액세스 보안 유지__:

  기본적으로 가상 네트워크에 대해 보호된 Azure 서비스 리소스는 온-프레미스 네트워크에서 연결할 수 없습니다. 온-프레미스의 트래픽을 허용하려는 경우 온-프레미스 또는 ExpressRoute의 공용 IP 주소(일반적으로 NAT)도 허용해야 합니다. Azure 서비스 리소스에 대한 IP 방화벽 구성을 통해 해당 IP 주소를 추가할 수 있습니다.

  ExpressRoute: 공용 피어링 또는 Microsoft 피어링을 위해 온-프레미스에서 [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 사용하는 경우 사용되는 NAT IP 주소를 식별해야 합니다. 공용 피어링의 경우 기본적으로 각 ExpressRoute 회로는 트래픽이 Microsoft Azure 네트워크 백본으로 들어갈 때 Azure 서비스 트래픽에 적용되는 두 개의 NAT IP 주소를 사용합니다. Microsoft 피어링의 경우 사용되는 NAT IP 주소는 고객이 제공하거나 서비스 공급자가 제공합니다. 서비스 리소스에 대한 액세스를 허용하려면 리소스 IP 방화벽 설정에서 이러한 공용 IP 주소를 허용해야 합니다. 공용 피어링 ExpressRoute 회로 IP 주소를 찾으려면 Azure Portal을 통해 [ExpressRoute에서 지원 티켓을 엽니다](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). [ExpressRoute 공용 및 Microsoft 피어링을 위한 NAT](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)에 대해 자세히 알아보세요.

![Virtual Network에 대한 Azure 서비스 보호](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>구성

- 서비스 엔드포인트는 가상 네트워크의 서브넷에 구성됩니다. 엔드포인트는 해당 서브넷 내에서 실행되는 모든 컴퓨팅 인스턴스를 사용합니다.
- 서브넷에 지원되는 모든 Azure 서비스(예: Azure Storage, Azure SQL Database)에 여러 개의 서비스 엔드포인트를 구성할 수 있습니다.
- Azure SQL Database의 경우 가상 네트워크는 Azure 서비스 리소스와 동일한 지역에 있어야 합니다. GRS 및 RA-GRS Azure Storage 계정을 사용하는 경우 기본 계정은 가상 네트워크와 동일한 지역에 있어야 합니다. 그 외의 서비스는 Azure 서비스 리소스를 모든 지역의 가상 네트워크에 안전하게 보호할 수 있습니다. 
- 엔드포인트가 구성된 가상 네트워크는 Azure 서비스 리소스와 동일하거나 다른 구독에 구성될 수 있습니다. 엔드포인트를 설정하고 Azure 서비스를 보호하는 데 필요한 사용 권한에 대한 자세한 내용은 [프로비전](#provisioning)을 참조하세요.
- 지원되는 서비스의 경우 서비스 엔드포인트를 사용하여 가상 네트워크에 대한 기존 또는 새로운 리소스를 보호할 수 있습니다.

### <a name="considerations"></a>고려 사항

- 서비스 엔드포인트를 사용하도록 설정한 후에 서브넷에 있는 가상 머신의 원본 IP 주소는 해당 서브넷의 서비스와 통신할 때 공용 IPv4 주소가 아닌 개인 IPv4 주소를 사용하도록 전환됩니다. 이 전환 중에 서비스에 대한 기존의 모든 오픈 TCP 연결이 닫힙니다. 서브넷의 서비스에 서비스 엔드포인트를 사용하거나 사용하지 않도록 설정하는 경우 중요한 작업이 실행되지 않아야 합니다. 또한 IP 주소를 전환한 후에 응용 프로그램이 Azure 서비스에 자동으로 연결될 수 있어야 합니다.

  IP 주소 전환은 가상 네트워크의 서비스 트래픽에만 영향을 줍니다. 가상 머신에 할당된 공용 IPv4 주소 간에 주소가 지정된 다른 모든 트래픽에는 영향이 없습니다. Azure 서비스의 경우 Azure 공용 IP 주소를 사용하는 기존 방화벽 규칙이 있는 경우 이러한 규칙은 가상 네트워크 개인 주소로 전환하는 동시에 작동이 중지됩니다.
- 서비스 엔드포인트에서 Azure 서비스의 DNS 항목은 현재 상태로 유지되고 Azure 서비스에 할당된 공용 IP 주소로 계속 사용됩니다.

- 서비스 엔드포인트의 NSG(네트워크 보안 그룹):
  - 기본적으로 NSG는 아웃바운드 인터넷 트래픽을 허용하고 따라서 VNet에서 Azure 서비스로의 트래픽을 허용합니다. 서비스 엔드포인트에서 계속 그대로 작동합니다. 
  - 모든 아웃바운드 인터넷 트래픽을 거부하고 특정 Azure 서비스에 대한 트래픽만 허용하려는 경우 NSG에서 [서비스 태그](security-overview.md#service-tags)를 사용하여 수행할 수 있습니다. NSG 규칙에서 대상으로 지원되는 Azure 서비스를 지정할 수 있습니다. 또한 각 태그의 기반이 되는 IP 주소의 유지 관리는 Azure에서 제공됩니다. 자세한 내용은 [NSG의 Azure 서비스 태그](security-overview.md#service-tags)를 참조하세요. 

### <a name="scenarios"></a>시나리오

- **피어링되거나 연결된 여러 가상 네트워크**: 하나의 가상 네트워크 또는 여러 가상 네트워크의 여러 서브넷에 대한 Azure 서비스를 보호하려면 각 서브넷에서 서비스 엔드포인트를 독립적으로 활성화하고 모든 서브넷에 대한 Azure 서비스 리소스를 보호할 수 있습니다.
- **가상 네트워크에서 Azure 서비스로의 아웃바운드 트래픽 필터링**: 가상 네트워크에서 Azure 서비스로 대상이 지정된 트래픽을 검사하거나 필터링하려는 경우 가상 네트워크 내에 네트워크 가상 어플라이언스를 배포할 수 있습니다. 네트워크 가상 어플라이언스를 배포한 서브넷에 서비스 엔드포인트를 적용하고 이 서브넷에 대한 Azure 서비스 리소스만을 보호할 수 있습니다. 네트워크 가상 어플라이언스 필터링을 사용하여 가상 네트워크에서 특정 Azure 리소스로의 Azure 서비스 액세스만을 제한하도록 하려는 경우 이 시나리오가 유용할 수 있습니다. 자세한 내용은 [네트워크 가상 어플라이언스에서 송신](/azure/architecture/reference-architectures/dmz/nva-ha)을 참조하세요.
- **가상 네트워크에 직접 배포된 서비스에 대한 Azure 리소스 보호**: 가상 네트워크에서 특정 서브넷에 다양한 Azure 서비스를 직접 배포할 수 있습니다. 관리되는 서비스 서브넷에서 서비스 엔드포인트를 설정하여 [관리되는 서비스](virtual-network-for-azure-services.md) 서브넷에 대한 Azure 서비스 리소스를 보호할 수 있습니다.
- **Azure 가상 머신의 디스크 트래픽**: 가상 머신 디스크 트래픽(탑재 및 탑재 해제 포함, diskIO), 관리형/비관리형 디스크의 경우 Azure Storage에 대한 서비스 엔드포인트 라우팅 변경 내용에 영향을 받지 않습니다. 서비스 엔드포인트 및 [Azure Storage 네트워크 규칙](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)을 통해 REST 액세스를 네트워크를 선택하도록 페이지 Blob으로 제한할 수 있습니다. 

### <a name="logging-and-troubleshooting"></a>로깅 및 문제 해결

서비스 엔드포인트가 특정 서비스에 구성되면 서비스 엔드포인트 경로가 다음 항목에 적용되는지 유효성을 검사합니다. 
 
- 서비스 진단에서 모든 서비스 요청의 원본 IP 주소 유효성을 검사합니다. 서비스 엔드포인트에서 모든 새로운 요청은 요청의 원본 IP 주소를 가상 네트워크 개인 IP 주소로 표시하고 가상 네트워크에서 요청한 클라이언트에 할당됩니다. 엔드포인트가 없는 경우 주소는 Azure 공용 IP 주소입니다.
- 서브넷의 모든 네트워크 인터페이스에서 유효 경로를 볼 수 있습니다. 서비스에 대한 경로:
  - 각 서비스의 주소를 지정하는 구체적인 기본 경로를 표시합니다.
  - *VirtualNetworkServiceEndpoint*의 nextHopType이 있습니다.
  - 강제 터널링 경로에 비해 서비스에 대한 직접 연결이 더 효과적임을 나타냅니다.

>[!NOTE]
> 서비스 엔드포인트 경로는 Azure 서비스의 주소 접두사에 대한 BGP 또는 UDR 경로를 재정의합니다. [유효 경로 관련 문제 해결](diagnose-network-routing-problem.md)에 대해 자세히 알아봅니다.

## <a name="provisioning"></a>프로비전

가상 네트워크에 대한 쓰기 액세스 권한이 있는 사용자는 가상 네트워크에서 독립적으로 서비스 엔드포인트를 구성할 수 있습니다. VNet에 대한 Azure 서비스 리소스를 보호하려면 사용자는 추가되는 서브넷의 *Microsoft.Network/JoinServicetoaSubnet*에 대한 사용 권한이 있어야 합니다. 이 권한은 기본적으로 기본 제공 서비스 관리자 역할에 포함되고 사용자 지정 역할을 만들어서 수정될 수 있습니다.

[기본 제공 역할](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 및 [사용자 지정 역할](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)에 특정 권한 할당에 대해 자세히 알아보세요.

가상 네트워크 및 Azure 서비스 리소스가 동일한 구독이나 다른 구독에 있을 수 있습니다. 가상 네트워크 및 Azure 서비스 리소스가 다른 구독에 있는 경우 동일한 AD(Active Directory) 테넌트 아래에 있어야 합니다. 

## <a name="pricing-and-limits"></a>가격 책정 및 제한

서비스 엔드포인트를 사용하는 추가 비용이 없습니다. Azure 서비스(Azure Storage, Azure SQL Database 등)의 현재 가격 책정 모델은 현재 상태로 적용됩니다.

가상 네트워크에서 서비스 엔드포인트의 총합에 제한이 없습니다.

Azure 저장소 계정 등의 특정 Azure 서비스는 리소스 보안에 사용 되는 서브넷의 수에 제한을 적용할 수 있습니다. 자세한 내용은 [다음 단계](#next-steps)에 있는 다양한 서비스에 대한 설명서를 참조하세요.

## <a name="virtual-network-service-endpoint-policies"></a>Virtual Network 서비스 엔드포인트 정책 

Virtual Network 서비스 엔드포인트 정책을 사용하면 가상 네트워크 트래픽을 Azure 서비스로 필터링하여 특정 Azure 서비스 리소스만 서비스 엔드포인트를 통해 허용할 수 있습니다. 서비스 엔드포인트 정책은 Azure 서비스의 가상 네트워크 트래픽에 대한 세부적인 액세스 제어를 제공합니다. 자세한 정보: [Virtual Network 서비스 엔드포인트 정책](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)

## <a name="faqs"></a>FAQ

FAQ는 [Virtual Network 서비스 엔드포인트 FAQ](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#virtual-network-service-endpoints)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [가상 네트워크 서비스 엔드포인트를 구성](tutorial-restrict-network-access-to-resources.md)하는 방법에 대한 자세한 내용
- [가상 네트워크에 대한 Azure Storage 계정을 보호](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)하는 방법에 대한 자세한 내용
- [가상 네트워크에 대한 Azure SQL Database 계정을 보호](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)하는 방법에 대한 자세한 내용
- [Azure SQL Data Warehouse를 가상 네트워크에서 안전하게 보호](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)하는 방법에 대한 자세한 내용
- [가상 네트워크의 Azure 서비스 통합](virtual-network-for-azure-services.md)에 대한 자세한 내용
- [Virtual Network 서비스 엔드포인트 정책](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)에 대한 자세한 내용
-  빠른 시작: VNet의 서브넷에 서비스 엔드포인트를 설정하고 해당 서브넷에 Azure Storage 계정을 보호하기 위한 [Azure Resource Manager 템플릿](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration).

