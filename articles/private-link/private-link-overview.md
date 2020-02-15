---
title: Azure Private Link란?
description: 'Azure Private Link를 사용하여 가상 네트워크의 프라이빗 엔드포인트를 통해 Azure PaaS Services(예: Azure Storage 및 SQL Database)와 Azure 호스팅 고객/파트너 서비스에 액세스하는 방법을 알아봅니다.'
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: overview
ms.date: 01/09/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: 810f2b376e9983743617dae77d74d596a7fbe663
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77084188"
---
# <a name="what-is-azure-private-link-preview"></a>Azure Private Link란? (미리 보기)
Azure Private Link를 사용하면 가상 네트워크의 [프라이빗 엔드포인트](private-endpoint-overview.md)를 통해 Azure PaaS Services(예: Azure Storage, Azure Cosmos DB 및 SQL Database)와 Azure 호스팅 고객/파트너 서비스에 액세스할 수 있습니다. 가상 네트워크와 서비스 간의 트래픽은 Microsoft 백본 네트워크를 통해 이동하여 공용 인터넷에서 노출을 제거합니다. VNet(가상 네트워크)에서 자체 [Private Link Service](private-link-service-overview.md)를 만들어 고객에게 개인적으로 제공할 수도 있습니다. Azure Private Link를 사용하는 설정 및 사용 환경은 Azure PaaS, 고객 소유/공유 파트너 서비스에서 일관적입니다.

> [!IMPORTANT]
> 이 공개 미리 보기는 Service Level Agreement(서비스 수준 약정)없이 제공되므로 프로덕션 워크로드에 사용하지 말아야 합니다. 특정 기능은 지원되지 않을 수 있거나, 기능이 제한될 수 있거나 모든 Azure 위치에서 사용하지는 못할 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. 알려진 제한은 [프라이빗 엔드포인트](private-endpoint-overview.md#limitations) 및 [Private Link Service](private-link-service-overview.md#limitations)를 참조하세요.


![프라이빗 엔드포인트 개요](media/private-link-overview/private-endpoint.png)

## <a name="key-benefits"></a>주요 이점
Azure Private Link는 다음과 같은 이점이 있습니다.  
- **Azure 플랫폼에서 서비스에 비공개로 액세스**: 원본 또는 대상의 공용 IP 주소가 없어도 Azure에서 비공개로 실행되는 서비스에 가상 네트워크를 연결할 수 있습니다. 서비스 공급자는 자체 가상 네트워크에서 비공개로 서비스를 렌더링할 수 있으며, 소비자는 로컬 가상 네트워크에서 이러한 서비스에 비공개로 액세스할 수 있습니다. Private Link 플랫폼은 Azure 백본 네트워크를 통해 소비자와 서비스 간의 연결을 처리합니다. 
 
- **온-프레미스 및 피어링된 네트워크** 온-프레미스에서 ExpressRoute 프라이빗 피어링/VPN 터널(온-프레미스에서) 및 피어링된 가상 네트워크를 통해 프라이빗 엔드포인트를 사용하여 Azure에서 실행되는 서비스에 액세스할 수 있습니다. 공용 피어링을 설정하거나 인터넷을 통과하여 서비스에 연결할 필요가 없습니다. 이 기능은 워크로드를 Azure로 안전하게 마이그레이션하는 방법을 제공합니다.
 
- **데이터 반출 방지**:  Azure Private Link를 사용하면 VNet의 프라이빗 엔드포인트가 전체 서비스가 아닌 고객 PaaS 리소스의 특정 인스턴스에 매핑됩니다. 프라이빗 엔드포인트를 사용하면 소비자는 특정 리소스에만 연결할 수 있고 서비스의 다른 리소스에는 연결할 수 없습니다. 이 기본 메커니즘은 데이터 반출 위험을 방지합니다. 
 
- **글로벌 환경**: 다른 Azure 지역에서 실행되는 서비스에 비공개로 연결할 수 있습니다. 즉, A 지역에 있는 소비자의 가상 네트워크가 지역 B의 Private Link 뒤에 있는 서비스에 연결할 수 있습니다.  
 
- **사용자 고유의 서비스로 확장**: 동일한 환경과 기능을 활용하여 사용자 고유의 서비스를 Azure의 소비자에게 비공개로 렌더링할 수 있습니다. 서비스를 표준 Load Balancer 뒤에 배치하면 Private Link에 사용할 수 있습니다. 그러면 소비자는 자체 VNet에서 프라이빗 엔드포인트를 사용하여 서비스에 직접 연결할 수 있습니다. 간단한 승인 호출 흐름을 사용하여 이러한 연결 요청을 관리할 수 있습니다. Azure Private Link는 다른 Active Directory 테넌트에 속한 소비자 및 서비스에 대해서도 작동합니다. 

## <a name="availability"></a>가용성 
 다음 표에는 Private Link 서비스 및 이러한 서비스를 사용할 수 있는 지역이 나열되어 있습니다. 

|시나리오  |지원되는 서비스  |사용 가능한 지역 | 상태  |
|:---------|:-------------------|:-----------------|:--------|
|고객 소유 서비스용 Private Link|표준 Load Balancer 뒤에 배치된 Private Link 서비스 | 모든 공용 지역  | 미리 보기  |
|Azure PaaS 서비스용 Private Link   | Azure Storage        |  모든 공용 지역      | 미리 보기 <br/> [자세히 알아보기](/azure/storage/common/storage-private-endpoints).  |
|  | Azure Data Lake Storage Gen2        |  모든 공용 지역      | 미리 보기 <br/> [자세히 알아보기](/azure/storage/common/storage-private-endpoints).  |
|  |  Azure SQL Database         | 모든 공용 지역      |   미리 보기      |
||Azure SQL Data Warehouse| 모든 공용 지역 |미리 보기|
||Azure Cosmos DB| 미국 중서부, 미국 서부, 미국 중북부 |미리 보기|
|  |  Azure Database for PostgreSQL - 단일 서버         | 모든 공용 지역      |   미리 보기      |
|  |  Azure Database for MySQL         | 모든 공용 지역      |   미리 보기      |
|  |  Azure Database for MariaDB         | 모든 공용 지역      |   미리 보기      |
|  |  Azure Key Vault         | 모든 공용 지역      |   미리 보기      |

최신 알림은 [Azure Virtual Network 업데이트 페이지](https://azure.microsoft.com/updates/?product=virtual-network)를 확인하세요.

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

Azure Private Link가 Azure Monitor와 통합되기 때문에 스토리지 계정에 로그를 보관하고, 이벤트를 Event Hub로 스트리밍하거나 Azure Monitor 로그에 보낼 수 있습니다. Azure Monitor에서 다음 정보에 액세스할 수 있습니다. 
- **프라이빗 엔드포인트**: 프라이빗 엔드포인트에서 처리한 데이터(수신/송신)
 
- **Private Link 서비스**:
    - Private Link 서비스에서 처리한 데이터(수신/송신)
    - NAT 포트 가용성  
 
## <a name="pricing"></a>가격 책정   
가격 책정에 대한 자세한 내용은 [Azure Private Link 가격 책정](https://azure.microsoft.com/pricing/details/private-link/)을 참조하세요.
 
## <a name="faqs"></a>FAQ  
FAQ는 [Azure Private Link FAQ](private-link-faq.md)를 참조하세요.
 
## <a name="limits"></a>제한  
제한은 [Azure Private Link 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits)을 참조하세요.

## <a name="next-steps"></a>다음 단계
- [포털을 사용하여 SQL Database Server용 프라이빗 엔드포인트 만들기](create-private-endpoint-portal.md)
- [PowerShell을 사용하여 SQL Database Server용 프라이빗 엔드포인트 만들기](create-private-endpoint-powershell.md)
- [CLI를 사용하여 SQL Database Server용 프라이빗 엔드포인트 만들기](create-private-endpoint-cli.md)
- [포털을 사용하여 스토리지 계정용 프라이빗 엔드포인트 만들기](create-private-endpoint-storage-portal.md)
- [포털을 사용하여 Azure Cosmos 계정용 프라이빗 엔드포인트 만들기](../cosmos-db/how-to-configure-private-endpoints.md)
- [Azure PowerShell를 사용하여 고유의 Private Link 서비스 만들기](create-private-link-service-powershell.md)


 
