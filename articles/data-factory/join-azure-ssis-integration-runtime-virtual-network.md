---
title: Azure-SSIS 통합 런타임을 Azure 가상 네트워크에 조인
description: Azure 가상 네트워크에 Azure SSIS 통합 런타임을 조인 하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/01/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: 7e8a1793a329a863c9df97ae5ddcbee6cef10e8e
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964346"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Azure-SSIS 통합 런타임을 Azure 가상 네트워크에 조인

Azure Data Factory에서 SSIS (SQL Server Integration Services)를 사용 하는 경우 다음과 같은 시나리오에서 azure SSIS IR (통합 런타임)을 Azure virtual network에 조인 해야 합니다.

- 자체 호스팅 IR을 프록시로 구성 하거나 관리 하지 않고 Azure-SSIS IR에서 실행 되는 SSIS 패키지에서 온-프레미스 데이터 저장소에 연결 하려고 합니다. 

- IP 방화벽 규칙/가상 네트워크 서비스 엔드포인트 또는 개인 엔드포인트이 포함 된 관리 되는 인스턴스를 사용 하 여 Azure SQL Database에서 SSISDB (SSIS 카탈로그 데이터베이스)를 호스트 하려고 합니다. 

- Azure-SSIS IR에서 실행 되는 SSIS 패키지의 가상 네트워크 서비스 엔드포인트을 사용 하 여 구성 된 Azure 리소스에 연결 하려고 합니다.

- Azure-SSIS IR에서 실행 되는 SSIS 패키지의 IP 방화벽 규칙을 사용 하 여 구성 된 데이터 저장소/리소스에 연결 하려고 합니다.

Data Factory를 사용 하면 클래식 배포 모델 또는 Azure Resource Manager 배포 모델을 통해 만든 가상 네트워크에 Azure-SSIS IR를 조인할 수 있습니다.

> [!IMPORTANT]
> 클래식 가상 네트워크는 더 이상 사용 되지 않으므로 Azure Resource Manager 가상 네트워크를 대신 사용 하세요.  이미 클래식 가상 네트워크를 사용 하는 경우 가능한 한 빨리 Azure Resource Manager virtual network로 전환 합니다.

[가상 네트워크에 가입 하기 위한 Azure SQL Server Integration Services (SSIS) Integration runtime (IR) 구성](tutorial-deploy-ssis-virtual-network.md) 자습서에서는 Azure Portal를 통해 최소 단계를 보여 줍니다. 이 문서는 자습서를 확장 하 고 모든 선택적 작업을 설명 합니다.

- 가상 네트워크 (클래식)를 사용 하는 경우
- Azure-SSIS IR에 대한 사용자 고유의 공용 IP 주소를 가져옵니다.
- 사용자 고유의 DNS (Domain Name System) 서버를 사용 하는 경우
- 서브넷에 NSG (네트워크 보안 그룹)를 사용 하는 경우
- Azure Express 경로 또는 UDR (사용자 정의 경로)를 사용 하는 경우.
- 사용자 지정 된 Azure-SSIS IR를 사용 하는 경우
- Azure Powershell 프로 비전을 사용 하는 경우.

## <a name="access-to-on-premises-data-stores"></a>온-프레미스 데이터 저장소 액세스

SSIS 패키지가 온-프레미스 데이터 저장소에 액세스 하는 경우 온-프레미스 네트워크에 연결 된 가상 네트워크에 Azure-SSIS IR를 조인할 수 있습니다. 또는 Azure-SSIS IR에 대한 프록시로 자체 호스팅 IR을 구성 하 고 관리할 수 있습니다. 자세한 내용은 [Azure-SSIS IR에 대한 프록시로 자체 호스팅 IR 구성](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)을 참조 하세요. 

Azure-SSIS IR 가상 네트워크에 가입 하는 경우 다음과 같은 중요 한 사항을 기억해 야 합니다. 

- 가상 네트워크가 온-프레미스 네트워크에 연결 되어 있지 않으면 먼저 Azure-SSIS IR 가입할 [Azure Resource Manager 가상 네트워크](../virtual-network/quick-create-portal.md#create-a-virtual-network) 를 만듭니다. 그런 다음 해당 가상 네트워크에서 온-프레미스 네트워크로 사이트 간 [VPN 게이트웨이 연결](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) 또는 [express](../expressroute/expressroute-howto-linkvnet-classic.md) 경로 연결을 구성 합니다. 

- Azure Resource Manager 가상 네트워크가 Azure-SSIS IR와 동일한 위치에 있는 온-프레미스 네트워크에 이미 연결 되어 있는 경우 해당 가상 네트워크에 IR을 조인할 수 있습니다. 

- 클래식 가상 네트워크가 Azure-SSIS IR의 다른 위치에 있는 온-프레미스 네트워크에 이미 연결 되어 있는 경우 Azure-SSIS IR 가입할 [Azure Resource Manager 가상 네트워크](../virtual-network/quick-create-portal.md#create-a-virtual-network) 를 만들 수 있습니다. 그런 다음, [클래식-Azure Resource Manager 가상 네트워크](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) 연결을 구성합니다. 
 
- Azure Resource Manager 가상 네트워크가 Azure-SSIS IR의 다른 위치에 있는 온-프레미스 네트워크에 이미 연결 되어 있는 경우 먼저 Azure-SSIS IR 가입할 [Azure Resource Manager 가상 네트워크](../virtual-network/quick-create-portal.md#create-a-virtual-network) 를 만들 수 있습니다. 그런 다음 Azure Resource Manager Azure Resource Manager 가상 네트워크 연결을 구성 합니다. 

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>SQL Database에서 SSIS 카탈로그 호스팅

가상 네트워크 서비스 엔드포인트을 사용 하 여 Azure SQL Database에서 SSIS 카탈로그를 호스트 하는 경우 Azure-SSIS IR를 동일한 가상 네트워크 및 서브넷에 연결 해야 합니다.

개인 엔드포인트을 사용 하 여 관리 되는 인스턴스에서 SSIS 카탈로그를 호스트 하는 경우 Azure-SSIS IR를 관리 되는 인스턴스와 다른 서브넷에 있는 동일한 가상 네트워크에 조인 해야 합니다. Azure-SSIS IR를 관리 되는 인스턴스가 아닌 다른 가상 네트워크에 가입 하려면 가상 네트워크 피어 링 (동일한 지역으로 제한 됨) 또는 가상 네트워크에서 가상 네트워크로의 연결을 권장 합니다. 자세한 내용은 [응용 프로그램을 Azure SQL Database 관리 되는 인스턴스에 연결](../sql-database/sql-database-managed-instance-connect-app.md)을 참조 하세요.

## <a name="access-to-azure-services"></a>Azure 서비스에 대한 액세스

SSIS 패키지가 [가상 네트워크 서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md) 을 지 원하는 Azure 리소스에 액세스 하 고 Azure-SSIS IR에서 해당 리소스에 대한 액세스를 보호 하려는 경우 가상 네트워크 서비스 엔드포인트에 대해 구성 된 가상 네트워크 서브넷에 Azure-SSIS IR를 조인한 다음, 동일한 서브넷에서 액세스할 수 있도록 관련 Azure 리소스에 가상 네트워크 규칙을 추가할 수 있습니다.

## <a name="access-to-data-sources-protected-by-ip-firewall-rule"></a>IP 방화벽 규칙으로 보호 되는 데이터 원본에 대한 액세스

SSIS 패키지가 특정 고정 공용 IP 주소만 허용 하는 데이터 저장소/리소스에 액세스 하 고 Azure-SSIS IR에서 해당 리소스에 대한 액세스를 보호 하려는 경우 가상 네트워크에 연결 하는 동안 Azure-SSIS IR에 대한 사용자 고유의 [공용 ip 주소](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address) 를 가져와서 해당 ip 주소에서의 액세스를 허용 하는 관련 리소스에 ip 방화벽 규칙을 추가할 수 있습니다.

모든 경우에 가상 네트워크는 Azure Resource Manager 배포 모델을 통해서만 배포할 수 있습니다.

다음 섹션에 자세한 내용이 제공됩니다. 

## <a name="virtual-network-configuration"></a>가상 네트워크 구성

다음 요구 사항을 충족 하도록 가상 네트워크를 설정 합니다. 

- `Microsoft.Batch`이 Azure-SSIS IR를 호스팅하는 가상 네트워크 서브넷의 구독에서 등록 된 공급자 인지 확인 합니다. 클래식 가상 네트워크를 사용 하는 경우 해당 가상 네트워크에 대한 클래식 가상 머신 참가자 역할에도 `MicrosoftAzureBatch` 가입 합니다. 

- 필요한 권한이 있는지 확인하세요. 자세한 내용은 [설정 사용 권한](#perms)을 참조 하세요.

- Azure-SSIS IR을 호스팅할 적절한 서브넷을 선택합니다. 자세한 내용은 [서브넷 선택](#subnet)을 참조 하세요. 

- Azure-SSIS IR에 대한 사용자 고유의 공용 IP 주소를 가져오는 경우 [고정 공용 ip 주소 선택](#publicIP) 을 참조 하세요.

- 가상 네트워크에서 자체 DNS (Domain Name System) 서버를 사용 하는 경우 [dns 서버 설정](#dns_server)을 참조 하세요. 

- 서브넷에서 NSG (네트워크 보안 그룹)를 사용 하는 경우 [Nsg 설정](#nsg)을 참조 하세요. 

- Azure Express 경로 또는 UDR (사용자 정의 경로)을 사용 하는 경우 [Azure express 경로 또는 Udr 사용](#route)을 참조 하세요. 

- 가상 네트워크의 리소스 그룹 (또는 사용자 고유의 공용 IP 주소를 가져오는 경우 공용 IP 주소의 리소스 그룹)이 특정 Azure 네트워크 리소스를 만들고 삭제할 수 있는지 확인 합니다. 자세한 내용은 [리소스 그룹 설정](#resource-group)을 참조 하세요. 

- [Azure-SSIS IR에 대한 사용자 지정 설정](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)에 설명 된 대로 Azure-SSIS IR를 사용자 지정 하는 경우 Azure-SSIS IR 노드가 미리 정의 된 172.16.0.0에서 172.31.255.255으로 개인 IP 주소를 가져옵니다. 따라서 가상 또는 온-프레미스 네트워크의 개인 IP 주소 범위가이 범위와 충돌 하지 않는지 확인 합니다.

이 다이어그램은 Azure-SSIS IR에 필요한 연결을 보여 줍니다.

![Azure-SSIS IR](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="perms"></a>권한 설정

Azure-SSIS IR를 만드는 사용자에 게는 다음 사용 권한이 있어야 합니다.

- SSIS IR를 Azure Resource Manager 가상 네트워크에 연결하는 경우 다음 두 가지 옵션이 있습니다.

  - 기본 제공 네트워크 참가자 역할을 사용 합니다. 이 역할에는 범위가 필요 이상으로 큰 _Microsoft.Network/\*_ 권한이 제공됩니다.

  - 필요한 _Microsoft.Network/virtualNetworks/\*/join/action_ 권한만 포함하는 사용자 지정 역할을 만듭니다. 또한 Azure Resource Manager 가상 네트워크에 연결 하는 동안 Azure-SSIS IR에 대한 공용 IP 주소를 가져오려면 역할에 _Microsoft. network/publicIPAddresses/*/join/action_ 권한도 포함 해야 합니다.

- SSIS IR을 클래식 가상 네트워크에 가입 하는 경우 기본 제공 클래식 가상 머신 참가자 역할을 사용 하는 것이 좋습니다. 그렇지 않은 경우 가상 네트워크 연결 권한이 포함된 사용자 지정 역할을 정의해야 합니다.

### <a name="subnet"></a> 서브넷 선택

서브넷을 선택 하면 다음을 수행 합니다. 

- Azure-SSIS IR을 배포 하려면 게이트웨이 서브넷을 선택 하지 마세요. 가상 네트워크 게이트웨이 전용입니다. 

- 선택한 서브넷에 Azure-SSIS IR 사용할 수 있는 충분 한 주소 공간이 있는지 확인 합니다. IR 노드 번호의 두 배 이상에 대해 사용 가능한 IP 주소를 유지 합니다. Azure는 각 서브넷 내의 일부 IP 주소를 예약합니다. 이러한 주소는 사용할 수 없습니다. 서브넷의 첫 번째 및 마지막 IP 주소는 프로토콜 규칙을 위해 예약 되 고 3 개 이상의 주소가 Azure 서비스에 사용 됩니다. 자세한 내용은 [이러한 서브넷 내에서 IP 주소를 사용하는데 제한 사항이 있습니까?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

- 다른 Azure 서비스 (예: SQL Database 관리 되는 인스턴스, App Service 등)에서 독점적으로 사용 되는 서브넷을 사용 하지 마세요. 

### <a name="publicIP"></a>고정 공용 IP 주소를 선택 합니다.

가상 네트워크에 가입 하는 동안 Azure-SSIS IR에 대한 고정 공용 IP 주소를 가져오려면 다음과 같은 요구 사항을 충족 하는지 확인 합니다.

- 다른 Azure 리소스와 아직 연결 되지 않은 사용 하지 않는 항목은 정확히 두 개 제공 되어야 합니다. Azure-SSIS IR를 정기적으로 업그레이드 하는 경우 추가 사용 됩니다.

- 둘 다 표준 유형의 정적 이어야 합니다. 자세한 내용은 [공용 IP 주소의 sku](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#sku) 를 참조 하세요.

- 둘 다 DNS 이름이 있어야 합니다. DNS 이름을 제공 하지 않은 경우에는 Azure Portal에서 DNS 이름을 제공 하지 않아도 됩니다.

![Azure-SSIS IR](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

- 그리고 가상 네트워크는 동일한 구독 및 동일한 지역에 있어야 합니다.

### <a name="dns_server"></a>DNS 서버 설정 
개인 호스트 이름을 확인 하기 위해 Azure-SSIS IR에 연결 된 가상 네트워크에서 자체 DNS 서버를 사용 해야 하는 경우 글로벌 Azure 호스트 이름 (예: `<your storage account>.blob.core.windows.net`Azure Storage blob)도 확인할 수 있는지 확인 합니다. 

권장 되는 방법 중 하나는 다음과 같습니다. 

-   Azure DNS에 요청을 전달 하도록 사용자 지정 DNS를 구성 합니다. 사용자의 DNS 서버에서 Azure 재귀적 확인자 (168.63.129.16)의 IP 주소에 확인 되지 않은 DNS 레코드를 전달할 수 있습니다. 

자세한 내용은 [자체 DNS 서버를 사용 하는 이름 확인](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)을 참조 하세요. 

> [!NOTE]
> 개인 호스트 이름에 대한 FQDN (정규화 된 도메인 이름)을 사용 하세요 (예: `<your_private_server>`대신 `<your_private_server>.contoso.com` 사용). Azure-SSIS IR 사용자의 DNS 접미사를 자동으로 추가 하지 않습니다.

### <a name="nsg"></a>NSG 설정
Azure-SSIS IR에서 사용 하는 서브넷에 대한 NSG를 구현 해야 하는 경우 다음 포트를 통해 인바운드 및 아웃 바운드 트래픽을 허용 합니다. 

-   **Azure-SSIS IR의 인바운드 요구 사항**

| Direction | 전송 프로토콜 | 원본 | 원본 포트 범위 | 대상 | Destination port range | 의견 |
|---|---|---|---|---|---|---|
| 인바운드 | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (IR을 리소스 관리자 가상 네트워크에 연결 하는 경우) <br/><br/>10100, 20100, 30100(IR을 클래식 가상 네트워크에 조인하는 경우)| Data Factory 서비스는 이러한 포트를 사용 하 여 가상 네트워크에 있는 Azure-SSIS IR의 노드와 통신 합니다. <br/><br/> 서브넷 수준 NSG를 만들지 여부에 관계 없이는 Azure-SSIS IR를 호스트 하는 가상 컴퓨터에 연결 된 Nic (네트워크 인터페이스 카드) 수준에서 NSG를 항상 구성 Data Factory 합니다. 지정된 포트에서 Data Factory IP 주소의 인바운드 트래픽만 해당 NIC 수준 NSG에서 허용됩니다. 서브넷 수준에서 인터넷 트래픽에 대해 이러한 포트를 여는 경우에도 IP 주소가 Data Factory 되지 않은 IP 주소의 트래픽은 NIC 수준에서 차단 됩니다. |
| 인바운드 | TCP | CorpNetSaw | * | VirtualNetwork | 3389 | 필드 이 규칙은 Microsoft 서포터 고객이 고급 문제 해결을 위해 열도록 요청 하 고 문제 해결 후 바로 종료할 수 있는 경우에만 필요 합니다. **CorpNetSaw** service 태그는 원격 데스크톱을 사용 하기 위해 Microsoft 회사 네트워크의 보안 액세스 워크스테이션만 허용 합니다. 이 서비스 태그는 포털에서 선택할 수 없으며 Azure PowerShell 또는 Azure CLI 통해서만 사용할 수 있습니다. <br/><br/> NIC 수준 NSG에서 포트 3389는 기본적으로 열려 있으며,이를 통해 서브넷 수준 NSG에서 포트 3389을 제어할 수 있습니다. 또한 Azure-SSIS IR는 보호를 위해 각 IR 노드의 windows 방화벽 규칙에서 포트 3389 아웃 바운드를 기본적으로 허용 하지 않습니다. |
||||||||

-   **Azure-SSIS IR의 아웃 바운드 요구 사항**

| Direction | 전송 프로토콜 | 원본 | 원본 포트 범위 | 대상 | Destination port range | 의견 |
|---|---|---|---|---|---|---|
| 아웃바운드 | TCP | VirtualNetwork | * | AzureCloud | 443 | 가상 네트워크의 Azure-SSIS IR 노드는이 포트를 사용 하 여 Azure Storage 및 Azure Event Hubs와 같은 Azure 서비스에 액세스 합니다. |
| 아웃바운드 | TCP | VirtualNetwork | * | 인터넷 | 80 | 필드 가상 네트워크의 Azure-SSIS IR 노드는이 포트를 사용 하 여 인터넷에서 인증서 해지 목록을 다운로드 합니다. 이 트래픽을 차단 하면 IR을 시작 하 고 인증서 해지 목록을 확인 하는 기능이 손실 될 때 성능 다운 그레이드가 발생할 수 있습니다. 대상의 범위를 특정 Fqdn으로 세분화 하려면 **Azure express 경로 또는 UDR 섹션 사용** 을 참조 하세요.|
| 아웃바운드 | TCP | VirtualNetwork | * | Sql | 1433, 11000-11999 | 필드 이 규칙은 가상 네트워크의 Azure-SSIS IR 노드가 SQL Database 서버에서 호스팅하는 SSISDB에 액세스할 때만 필요 합니다. SQL Database 서버 연결 정책이 **리디렉션**대신 **프록시** 로 설정 된 경우에는 포트 1433만 필요 합니다. <br/><br/> 이 아웃 바운드 보안 규칙은 개인 엔드포인트을 사용 하 여 구성 된 가상 네트워크 또는 Azure 데이터베이스 서버에서 관리 되는 인스턴스에 의해 호스팅되는 SSISDB에 적용 되지 않습니다. |
| 아웃바운드 | TCP | VirtualNetwork | * | VirtualNetwork | 1433, 11000-11999 | 필드 이 규칙은 가상 네트워크의 Azure-SSIS IR 노드가 개인 엔드포인트을 사용 하 여 구성 된 가상 네트워크 또는 Azure 데이터베이스 서버의 관리 되는 인스턴스에서 호스팅되는 SSISDB에 액세스 하는 경우에만 필요 합니다. SQL Database 서버 연결 정책이 **리디렉션**대신 **프록시** 로 설정 된 경우에는 포트 1433만 필요 합니다. |
| 아웃바운드 | TCP | VirtualNetwork | * | Storage | 445 | 필드 이 규칙은 Azure Files에 저장 된 SSIS 패키지를 실행 하려는 경우에만 필요 합니다. |
||||||||

### <a name="route"></a>Azure Express 경로 또는 UDR 사용
Azure-SSIS IR의 아웃 바운드 트래픽을 검사 하려는 경우 [Azure express](https://azure.microsoft.com/services/expressroute/) 경로 force 터널링 (BGP 경로, 0.0.0.0/0을 가상 네트워크에 보급) 또는 Nva (네트워크 가상 어플라이언스)를 통해 [udrs](../virtual-network/virtual-networks-udr-overview.md) [를 통해 온](https://docs.microsoft.com/azure/firewall/) -프레미스 방화벽 어플라이언스로 Azure-SSIS IR 시작 된 트래픽을 라우팅할 수 있습니다. 

![Azure-SSIS IR에 대한 NVA 시나리오](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir-nva.png)

전체 시나리오를 작동 하 게 하려면 다음 작업을 수행 해야 합니다.
   -   Azure Batch management services와 Azure-SSIS IR 간의 인바운드 트래픽은 방화벽 어플라이언스를 통해 라우팅할 수 없습니다.
   -   방화벽 어플라이언스는 Azure-SSIS IR에 필요한 아웃 바운드 트래픽을 허용 해야 합니다.

Azure Batch management services와 Azure-SSIS IR 간의 인바운드 트래픽은 방화벽 어플라이언스로 라우팅할 수 없습니다. 그렇지 않으면 비대칭 라우팅 문제로 인해 트래픽이 중단 됩니다. 트래픽이 들어오는 것과 같은 방식으로 다시 응답할 수 있도록 인바운드 트래픽에 대한 경로를 정의 해야 합니다. 다음 홉 유형이 **인터넷**인 Azure Batch 관리 서비스와 Azure-SSIS IR 간에 트래픽을 라우팅하는 특정 udrs를 정의할 수 있습니다.

예를 들어 Azure-SSIS IR `UK South`에 있는 경우 Azure 방화벽을 통해 아웃 바운드 트래픽을 검사 하려는 경우 서비스 태그 [ip 범위 다운로드 링크](https://www.microsoft.com/download/details.aspx?id=56519) 또는 [서비스 태그 검색 API](https://aka.ms/discoveryapi)를 통해 서비스 태그 `BatchNodeManagement.UKSouth`의 ip 범위 목록을 먼저 가져옵니다. 그런 다음, 다음 홉 유형이 **Virtual 어플라이언스**인 0.0.0.0/0 경로를 사용 하 여 다음 홉 유형으로 **인터넷** 으로 다음 홉 유형 경로를 적용 합니다.

![Azure Batch UDR 설정](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)

> [!NOTE]
> 이 방법에는 추가 유지 관리 비용이 발생 합니다. 정기적으로 IP 범위를 확인 하 고 Azure-SSIS IR 중단을 방지 하기 위해 UDR에 새 IP 범위를 추가 합니다. 매월 IP 범위를 확인 하는 것이 좋습니다. 새 IP가 서비스 태그에 표시 되 면 IP는 다른 달에 적용 됩니다. 

방화벽 어플라이언스에서 아웃 바운드 트래픽을 허용 하려면 NSG 아웃 바운드 규칙의 요구 사항과 동일한 포트에 대한 아웃 바운드를 허용 해야 합니다.
-   Azure Cloud services를 대상으로 하는 포트 443.

    Azure 방화벽을 사용 하는 경우 AzureCloud 서비스 태그를 사용 하 여 네트워크 규칙을 지정할 수 있습니다. 그렇지 않으면 방화벽 어플라이언스에서 대상을 모두로 허용할 수 있습니다.

-   대상이 CRL 다운로드 사이트인 포트 80입니다.

    CRL (인증서 해지 목록)으로 사용 되는 Fqdn을 사용 하 여 Azure-SSIS IR 관리 목적으로 인증서의 사이트를 다운로드 하도록 허용 해야 합니다.
    -  crl.microsoft.com:80
    -  mscrl.microsoft.com:80
    -  crl3.digicert.com:80
    -  crl4.digicert.com:80
    -  ocsp.digicert.com:80
    -  cacerts.digicert.com:80
    
    다른 CRL을 포함 하는 인증서를 사용 하는 경우 해당 인증서를 포함 하는 것이 좋습니다. [인증서 해지 목록](https://social.technet.microsoft.com/wiki/contents/articles/2303.understanding-access-to-microsoft-certificate-revocation-list.aspx)에 대한 자세한 내용은이 정보를 참조 하세요.

    이 트래픽을 허용 하지 않으면 시작 Azure-SSIS IR 때 성능 다운 그레이드가 발생할 수 있으며, 인증서 사용에 대한 인증서 해지 목록을 확인 하는 기능이 손실 될 수 있습니다 .이는 보안 관점에서 권장 되지 않습니다.

-   포트 1433, 11000-11999 (대상이 Azure SQL 인 경우) (가상 네트워크의 Azure-SSIS IR 노드가 SQL Database 서버에서 호스팅하는 SSISDB에 액세스 하는 경우에만 필요)

    Azure 방화벽을 사용 하는 경우 Azure SQL 서비스 태그를 사용 하 여 네트워크 규칙을 지정할 수 있습니다. 그렇지 않으면 방화벽 어플라이언스에서 특정 Azure sql url로 대상을 허용할 수 있습니다.

-   Azure Files에 저장 된 SSIS 패키지를 실행 하는 경우에만 필요한 Azure Storage를 대상으로 하는 포트 445.

    Azure 방화벽을 사용 하는 경우 저장소 서비스 태그를 사용 하 여 네트워크 규칙을 지정할 수 있습니다. 그렇지 않으면 방화벽 어플라이언스에서 특정 Azure 파일 저장소 url로 대상을 허용할 수 있습니다.

> [!NOTE]
> Azure SQL 및 저장소의 경우 서브넷에서 Virtual Network 서비스 엔드포인트을 구성 하는 경우 동일한 지역 또는 쌍을 이루는 지역에 있는 동일한 지역 \ Azure Storage의 Azure-SSIS IR와 Azure SQL 간의 트래픽이 Microsoft Azure 백본 네트워크로 직접 라우팅됩니다. 방화벽 어플라이언스 대신

Azure-SSIS IR의 아웃 바운드 트래픽을 검사 하는 기능이 필요 하지 않은 경우에는 단순히 경로를 적용 하 여 다음 홉 유형 **인터넷**으로 모든 트래픽을 강제로 수행할 수 있습니다.

-   Azure Express 경로 시나리오에서는 Azure-SSIS IR를 호스팅하는 서브넷에서 다음 홉 유형이 **인터넷** 인 0.0.0.0/0 경로를 적용할 수 있습니다. 
-   NVA 시나리오에서는 Azure-SSIS IR을 호스트 하는 서브넷에 적용 된 기존 0.0.0.0/0 경로를 **가상 어플라이언스** 에서 **인터넷**으로의 다음 홉 유형에 서 수정할 수 있습니다.

![경로 추가](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

> [!NOTE]
> 다음 홉 유형이 있는 경로 지정 **인터넷** 은 모든 트래픽이 인터넷을 통해 이동 하는 것을 의미 하지 않습니다. Azure의 서비스 중 하나에 대한 대상 주소인 경우, Azure는 트래픽을 인터넷으로 라우팅하는 대신 Azure의 백본 네트워크를 통해 서비스로 직접 라우팅합니다.

### <a name="resource-group"></a>리소스 그룹 설정

Azure-SSIS IR은 가상 네트워크와 동일한 리소스 그룹에 특정 네트워크 리소스를 만들어야 합니다. 이러한 리소스는 다음과 같습니다.
- 이름 *\<Guid >-azurebatch-cloudserviceloadbalancer*를 사용 하는 Azure 부하 분산 장치
- 이름이\<Guid > 인 Azure 공용 IP 주소 *-azurebatch-cloudservicepublicip*.
- 이름이 *\<Guid >-azurebatch-cloudservicenetworksecuritygroup*인 네트워크 작업 보안 그룹입니다. 

> [!NOTE]
> 이제 Azure-SSIS IR에 대한 고정 공용 IP 주소를 가져올 수 있습니다. 이 시나리오에서는 가상 네트워크 대신 고정 공용 IP 주소와 동일한 리소스 그룹에 Azure 부하 분산 장치 및 네트워크 보안 그룹을 만듭니다.

이러한 리소스는 Azure-SSIS IR 시작 될 때 생성 됩니다. Azure-SSIS IR 중지 될 때 삭제 됩니다. Azure-SSIS IR에 대한 고정 공용 IP 주소를 가져오는 경우 Azure-SSIS IR 중지 될 때 삭제 되지 않습니다. Azure-SSIS IR 중단 되지 않도록 차단 하려면 다른 리소스에서 이러한 네트워크 리소스를 다시 사용 하지 마세요. 

가상 네트워크/고정 공용 IP 주소가 속한 리소스 그룹/구독에 대한 리소스 잠금이 없는지 확인 합니다. 읽기 전용/삭제 잠금을 구성 하는 경우 Azure-SSIS IR를 시작 하 고 중지 하면 실패 하거나 응답 하지 않습니다.

가상 네트워크/고정 공용 IP 주소가 속한 리소스 그룹/구독에서 다음 리소스를 만들지 못하도록 하는 Azure 정책이 없는지 확인 합니다. 
- Microsoft.Network/LoadBalancers 
- Microsoft.Network/NetworkSecurityGroups 
- Microsoft.Network/PublicIPAddresses 

### <a name="faq"></a> FAQ

- 인바운드 연결에 대해 내 Azure-SSIS IR에서 노출 되는 공용 IP 주소를 보호 하려면 어떻게 해야 하나요? 공용 IP 주소를 제거할 수 있나요?
 
  현재 Azure-SSIS IR 가상 네트워크에 연결 되 면 공용 IP 주소가 자동으로 생성 됩니다. Azure Batch 관리 서비스만 인바운드-Azure-SSIS IR에 연결 하도록 허용 하는 NIC 수준 NSG가 있습니다. 인바운드 보호를 위해 서브넷 수준 NSG를 지정할 수도 있습니다.

  공용 IP 주소를 노출 하지 않으려면 사용자의 시나리오에 적용 되는 경우 가상 네트워크에 Azure-SSIS IR를 조인 하는 대신 [자체 호스팅 IR을 Azure-SSIS IR에 대한 프록시로 구성](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) 하는 것이 좋습니다.
 
- 내 데이터 원본에 대한 방화벽의 허용 목록에 내 Azure-SSIS IR의 공용 IP 주소를 추가할 수 있나요?

  이제 Azure-SSIS IR에 대한 고정 공용 IP 주소를 가져올 수 있습니다. 이 경우 데이터 원본에 대한 방화벽의 허용 목록에 IP 주소를 추가할 수 있습니다. 시나리오에 따라 Azure-SSIS IR에서 데이터 액세스를 보호 하는 다른 옵션을 고려할 수도 있습니다.

  - 온-프레미스 네트워크에 가상 네트워크를 연결 하 고 Azure-SSIS IR를 가상 네트워크 서브넷에 조인한 후 데이터 원본이 온-프레미스에 있는 경우 해당 서브넷의 개인 IP 주소 범위를 데이터 원본에 대한 방화벽의 허용 목록에 추가할 수 있습니다. .
  - 데이터 원본이 virtual network 서비스 엔드포인트을 지 원하는 Azure 서비스인 경우 가상 네트워크 서브넷에서 가상 네트워크 서비스 엔드포인트을 구성 하 고 해당 서브넷에 Azure-SSIS IR을 조인할 수 있습니다. 그런 다음 해당 서브넷을 사용 하 여 가상 네트워크 규칙을 데이터 원본에 대한 방화벽에 추가할 수 있습니다.
  - 데이터 원본이 비 Azure 클라우드 서비스인 경우 UDR을 사용 하 여 고정 공용 IP 주소를 통해 Azure-SSIS IR에서 NVA/Azure 방화벽으로 아웃 바운드 트래픽을 라우팅할 수 있습니다. 그런 다음 NVA/Azure 방화벽의 고정 공용 IP 주소를 데이터 원본에 대한 방화벽의 허용 목록에 추가할 수 있습니다.
  - 위의 옵션 중 요구 사항을 충족 하는 옵션이 없는 경우 [자체 호스팅 IR을 Azure-SSIS IR 프록시로 구성 하는](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)것이 좋습니다. 그런 다음 자체 호스팅 IR을 호스트 하는 컴퓨터의 고정 공용 IP 주소를 데이터 원본에 대한 방화벽의 허용 목록에 추가할 수 있습니다.

- Azure-SSIS IR에 대한 자체 작업을 수행 하려는 경우 두 개의 고정 공용 주소를 제공 해야 하는 이유는 무엇 인가요?

  Azure-SSIS IR은 정기적으로 자동으로 업데이트 됩니다. 업그레이드 하는 동안 새 노드가 만들어지고 이전 노드가 삭제 됩니다. 그러나 가동 중지 시간을 방지 하기 위해 새 노드는 새 노드가 준비 될 때까지 삭제 되지 않습니다. 따라서 이전 노드에 사용 되는 첫 번째 고정 공용 IP 주소를 즉시 해제할 수 없으며 새 노드를 만드는 두 번째 고정 공용 IP 주소가 필요 합니다.

- Azure-SSIS IR에 대한 고정 공용 IP 주소를 사용 했지만 여전히 내 데이터 원본에 액세스할 수 없는 이유는 무엇입니까?

  - 두 개의 고정 공용 IP 주소가 데이터 원본에 대한 방화벽의 허용 목록에 추가 되어 있는지 확인 합니다. Azure-SSIS IR 업그레이드 될 때마다 자신의 고정 공용 IP 주소는 사용자가 가져온 두 주소 간에 전환 됩니다. 둘 중 하나만 허용 목록에 추가 하는 경우 업그레이드 후 Azure-SSIS IR에 대한 데이터 액세스가 중단 됩니다.
  - 데이터 원본이 Azure 서비스인 경우 가상 네트워크 서비스 엔드포인트을 사용 하 여 구성 했는지 확인 하세요. 해당 하는 경우 데이터 원본에 대한 Azure-SSIS IR의 트래픽이 Azure 서비스에서 관리 하는 개인 IP 주소를 사용 하도록 전환 되 고 사용자 고유의 고정 공용 IP 주소를 데이터 원본에 대한 방화벽의 허용 목록에 추가 하는 것은 적용 되지 않습니다.

## <a name="azure-portal-data-factory-ui"></a>Azure Portal(데이터 팩터리 UI)

이 섹션에서는 Azure Portal 및 Data Factory UI를 사용 하 여 기존 Azure-SSIS IR를 가상 네트워크 (클래식 또는 Azure Resource Manager)에 조인 하는 방법을 보여 줍니다. 

Azure-SSIS IR 가상 네트워크에 연결 하기 전에 가상 네트워크를 올바르게 구성 해야 합니다. 가상 네트워크의 유형 (클래식 또는 Azure Resource Manager)에 적용 되는 섹션의 단계를 따릅니다. 그런 다음 세 번째 섹션의 단계에 따라 가상 네트워크에 Azure-SSIS IR을 연결 합니다. 

### <a name="configure-an-azure-resource-manager-virtual-network"></a>Azure Resource Manager 가상 네트워크 구성

포털을 사용 하 여 Azure-SSIS IR에 연결 하기 전에 Azure Resource Manager 가상 네트워크를 구성 합니다.

1. Microsoft Edge 또는 Google Chrome을 시작합니다. 현재 이러한 웹 브라우저만 Data Factory UI를 지원 합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 

1. **추가 서비스**를 선택합니다. **가상 네트워크**를 필터링하여 선택합니다. 

1. 목록에서 가상 네트워크를 필터링하고 선택합니다. 

1. **가상 네트워크** 페이지에서 **속성**을 선택합니다. 

1. **리소스 ID**에 대한 복사 단추를 선택하여 가상 네트워크에 대한 리소스 ID를 클립보드에 복사합니다. 클립 보드의 ID를 OneNote 또는 파일에 저장합니다. 

1. 왼쪽 메뉴에서 **서브넷**을 선택 합니다. 사용 가능한 주소 수가 Azure-SSIS IR의 노드 보다 큰지 확인 합니다. 

1. 가상 네트워크가 있는 Azure 구독에 Azure Batch 공급자가 등록되었는지 확인합니다. 또는 Azure Batch 공급자를 등록 합니다. 구독에 Azure Batch 계정이 이미 있는 경우에는 Azure Batch에 대한 구독이 등록 됩니다. (Data Factory 포털에 Azure-SSIS IR을 만들면 Azure Batch 공급자가 자동으로 등록됩니다.) 

   1. Azure Portal의 왼쪽 메뉴에서 **구독**을 선택 합니다. 

   1. 구독을 선택합니다. 

   1. 왼쪽에서 **리소스 공급자**를 선택 하 고, **Microsoft Batch** 가 등록 된 공급자 인지 확인 합니다. 

   !["등록됨" 상태 확인](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   목록에서 **Microsoft.Batch**가 보이지 않으면 지금 등록할 수 있도록 구독에서 [빈 Azure Batch 계정을 만듭니다](../batch/batch-account-create-portal.md). 나중에 삭제할 수 있습니다. 

### <a name="configure-a-classic-virtual-network"></a>클래식 가상 네트워크 구성

Azure-SSIS IR에 연결 하기 전에 포털을 사용 하 여 클래식 가상 네트워크를 구성 합니다. 

1. Microsoft Edge 또는 Google Chrome을 시작합니다. 현재 이러한 웹 브라우저만 Data Factory UI를 지원 합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 

1. **추가 서비스**를 선택합니다. **가상 네트워크(클래식)** 를 필터링하여 선택합니다. 

1. 목록에서 가상 네트워크를 필터링하고 선택합니다. 

1. **가상 네트워크(클래식)** 페이지에서 **속성**을 선택합니다. 

   ![클래식 가상 네트워크 리소스 ID](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. **RESOURCE ID**에 대한 복사 단추를 선택하여 클래식 네트워크의 리소스 ID를 클립보드에 복사합니다. 클립 보드의 ID를 OneNote 또는 파일에 저장합니다. 

1. 왼쪽 메뉴에서 **서브넷**을 선택 합니다. 사용 가능한 주소 수가 Azure-SSIS IR의 노드 보다 큰지 확인 합니다. 

   ![가상 네트워크에서 사용 가능한 주소 수](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. **MicrosoftAzureBatch**를 가상 네트워크의 **클래식 가상 머신 참가자** 역할에 조인합니다. 

   1. 왼쪽 메뉴에서 **액세스 제어 (IAM)** 를 선택 하 고 **역할 할당** 탭을 선택 합니다. 

       !["액세스 제어" 및 "추가" 단추](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

   1. **역할 할당 추가**를 선택합니다.

   1. **역할 할당 추가** 페이지에서 **역할**에 대해 **클래식 가상 머신 참가자**를 선택 합니다. **선택** 상자에 **ddbf3205-c6bd-46ae-8127-60eb93363864 붙여넣고**를 붙여넣고 검색 결과 목록에서 **Microsoft Azure Batch** 을 선택 합니다. 

       !["역할 할당 추가" 페이지의 검색 결과](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

   1. **저장** 을 선택 하 여 설정을 저장 하 고 페이지를 닫습니다. 

       ![액세스 설정 저장](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

   1. 참가자 목록에 **Microsoft Azure Batch**가 보이는지 확인합니다. 

       ![Azure Batch 액세스 확인](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. 가상 네트워크가 있는 Azure 구독에 Azure Batch 공급자가 등록되었는지 확인합니다. 또는 Azure Batch 공급자를 등록 합니다. 구독에 Azure Batch 계정이 이미 있는 경우에는 Azure Batch에 대한 구독이 등록 됩니다. (Data Factory 포털에 Azure-SSIS IR을 만들면 Azure Batch 공급자가 자동으로 등록됩니다.) 

   1. Azure Portal의 왼쪽 메뉴에서 **구독**을 선택 합니다. 

   1. 구독을 선택합니다. 

   1. 왼쪽에서 **리소스 공급자**를 선택 하 고, **Microsoft Batch** 가 등록 된 공급자 인지 확인 합니다. 

   !["등록됨" 상태 확인](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   목록에서 **Microsoft.Batch**가 보이지 않으면 지금 등록할 수 있도록 구독에서 [빈 Azure Batch 계정을 만듭니다](../batch/batch-account-create-portal.md). 나중에 삭제할 수 있습니다. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Azure-SSIS IR을 가상 네트워크에 조인

Azure Resource Manager 가상 네트워크 또는 클래식 가상 네트워크를 구성한 후 Azure-SSIS IR 가상 네트워크에 조인할 수 있습니다.

1. Microsoft Edge 또는 Google Chrome을 시작합니다. 현재 이러한 웹 브라우저만 Data Factory UI를 지원 합니다. 

1. [Azure Portal](https://portal.azure.com)의 왼쪽 메뉴에서 **데이터 팩터리**를 선택 합니다. 메뉴에 **데이터 팩터리** 가 표시 되지 않으면 **추가 서비스**를 선택한 다음 **인텔리전스 + 분석** 섹션에서 **데이터 팩터리**를 선택 합니다. 

   ![데이터 팩터리 목록](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. 목록에서 Azure-SSIS IR를 사용 하 여 데이터 팩터리를 선택 합니다. 데이터 팩터리의 홈 페이지가 표시됩니다. **작성자 & 모니터** 타일을 선택 합니다. 별도의 탭에 Data Factory UI가 표시됩니다. 

   ![데이터 팩터리 홈페이지](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. 데이터 팩터리 UI에서 **편집** 탭으로 전환하고 **연결**을 선택한 다음, **통합 런타임** 탭으로 전환합니다. 

   !["통합 런타임" 탭](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Azure-SSIS IR 실행 중인 경우 **통합 런타임** 목록의 **작업** 열에서 Azure-SSIS IR의 **중지** 단추를 선택 합니다. 중지할 때까지 Azure-SSIS IR을 편집할 수 없습니다. 

   ![IR 중지](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. **통합 런타임** 목록의 **작업** 열에서 Azure-SSIS IR에 대한 **편집** 단추를 선택 합니다. 

   ![통합 런타임 편집](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. Integration runtime 설정 패널에서 **다음** 단추를 선택 하 여 **일반 설정** 및 **SQL 설정** 섹션을 진행 합니다. 

1. **고급 설정** 섹션에서 다음을 수행 합니다. 

   1. **참가할 VNet 선택 Azure-SSIS Integration Runtime 선택 하 고 ADF가 특정 네트워크 리소스를 만들도록 허용 하 고 선택적으로 고정 공용 IP 주소를 가져옵니다** . 확인란을 선택 합니다. 

   1. **구독**에 대해서는 가상 네트워크가 있는 Azure 구독을 선택합니다.

   1. **위치**에는 통합 런타임의 동일한 위치가 선택됩니다.

   1. **유형**에 대해 가상 네트워크의 유형 (클래식 또는 Azure Resource Manager)을 선택 합니다. 클래식 가상 네트워크는 곧 사용 되지 않으므로 Azure Resource Manager 가상 네트워크를 선택 하는 것이 좋습니다.

   1. **VNet 이름**에서 가상 네트워크의 이름을 선택합니다. 가상 네트워크 서비스 엔드포인트을 사용 하는 Azure SQL Database 서버 또는 SSISDB를 호스트 하는 개인 엔드포인트이 포함 된 관리 되는 인스턴스를 사용 하는 것과 동일 해야 합니다. 또는 온-프레미스 네트워크에 연결 된 것과 동일 해야 합니다. 그렇지 않은 경우에는 Azure-SSIS IR에 대한 고정 공용 IP 주소를 만들 수 있는 가상 네트워크가 될 수 있습니다.

   1. **서브넷 이름**에서 가상 네트워크의 서브넷 이름을 선택합니다. SSISDB를 호스트 하는 가상 네트워크 서비스 엔드포인트을 사용 하 여 Azure SQL Database 서버에 사용 하는 것과 동일 해야 합니다. 또는 SSISDB를 호스트 하기 위해 개인 엔드포인트을 사용 하 여 관리 되는 인스턴스에 사용 되는 서브넷과 다른 서브넷 이어야 합니다. 그렇지 않은 경우에는 Azure-SSIS IR에 대한 고정 공용 IP 주소를 가져오는 서브넷이 될 수 있습니다.

   1. **Azure-SSIS Integration Runtime에 대한 고정 공용 ip 주소 가져오기** 확인란을 선택 하 여 Azure-SSIS IR에 대한 고정 공용 ip 주소를 만들지 여부를 선택 하 여 데이터 원본에 대한 방화벽에서 허용할 수 있습니다.

      확인란을 선택 하는 경우 다음 단계를 완료 합니다.

      1. **첫 번째 고정 공용 ip 주소**에 대해 Azure-SSIS IR [요구 사항을 충족](#publicIP) 하는 첫 번째 고정 공용 ip 주소를 선택 합니다. 가 없으면 **새 링크 만들기** 를 클릭 하 여 Azure Portal에서 고정 공용 IP 주소를 만든 다음 새로 고침 단추를 클릭 하 여 선택할 수 있습니다.
      
      1. **두 번째 고정 공용 ip 주소**에 대해 Azure-SSIS IR [요구 사항을 충족](#publicIP) 하는 두 번째 고정 공용 ip 주소를 선택 합니다. 가 없으면 **새 링크 만들기** 를 클릭 하 여 Azure Portal에서 고정 공용 IP 주소를 만든 다음 새로 고침 단추를 클릭 하 여 선택할 수 있습니다.

   1. **VNet 유효성 검사**를 선택 합니다. 유효성 검사에 성공 하면 **계속**을 선택 합니다. 

   ![가상 네트워크 관련 고급 설정](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. **요약** 섹션에서 Azure-SSIS IR에 대한 모든 설정을 검토 합니다. 그런 다음 **업데이트**를 선택 합니다.

1. Azure-SSIS IR의 **작업** 열에서 **시작** 단추를 선택 하 여 Azure-SSIS IR를 시작 합니다. 가상 네트워크에 연결 하는 Azure-SSIS IR를 시작 하는 데 약 20 ~ 30 분이 소요 됩니다. 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="define-the-variables"></a>변수를 정의합니다.

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"
```

### <a name="configure-a-virtual-network"></a>가상 네트워크 구성

Azure-SSIS IR를 가상 네트워크에 연결 하려면 먼저 가상 네트워크를 구성 해야 합니다. 가상 네트워크에 가입 하기 위해 Azure-SSIS IR에 대한 가상 네트워크 권한 및 설정을 자동으로 구성 하려면 다음 스크립트를 추가 합니다.

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Azure-SSIS IR을 만들어 가상 네트워크에 조인

Azure-SSIS IR을 만드는 동시에 가상 네트워크에 조인할 수 있습니다. 전체 스크립트 및 지침은 [Azure-SSIS IR 만들기](create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime)를 참조 하세요.

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>기존 Azure-SSIS IR을 가상 네트워크에 조인

[Azure-SSIS IR 만들기](create-azure-ssis-integration-runtime.md) 문서에서는 Azure-SSIS IR를 만들어 동일한 스크립트의 가상 네트워크에 조인 하는 방법을 보여 줍니다. Azure-SSIS IR 이미 있는 경우 다음 단계에 따라 가상 네트워크에 가입 합니다. 
1. Azure-SSIS IR을 중지합니다. 
1. 가상 네트워크에 조인하도록 Azure-SSIS IR을 구성합니다. 
1. Azure-SSIS IR을 시작합니다. 

### <a name="stop-the-azure-ssis-ir"></a>Azure-SSIS IR 중지

가상 네트워크에 연결 하려면 먼저 Azure-SSIS IR를 중지 해야 합니다. 이 명령은 모든 노드를 해제하고 청구를 중지합니다.

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Azure-SSIS IR이 조인하기 위한 가상 네트워크 설정을 구성합니다.

Azure SSIS가 가입할 가상 네트워크에 대한 설정을 구성 하려면 다음 스크립트를 사용 합니다. 

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="configure-the-azure-ssis-ir"></a>Azure-SSIS IR 구성

Azure-SSIS IR를 가상 네트워크에 연결 하려면 `Set-AzDataFactoryV2IntegrationRuntime` 명령을 실행 합니다. 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -VnetId $VnetId `
    -Subnet $SubnetName

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}
```

### <a name="start-the-azure-ssis-ir"></a>Azure-SSIS IR 시작

Azure-SSIS IR를 시작 하려면 다음 명령을 실행 합니다. 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

이 명령을 완료하는 데 20~30분이 걸립니다.

## <a name="next-steps"></a>다음 단계

Azure-SSIS IR에 대한 자세한 내용은 다음 문서를 참조 하세요. 
- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). 이 문서에서는 Azure-SSIS IR를 포함 하 여 IRs에 대한 일반적인 개념 정보를 제공 합니다. 
- [자습서: SSIS 패키지를 Azure에 배포](tutorial-create-azure-ssis-runtime-portal.md)합니다. 이 자습서에서는 Azure-SSIS IR을 만드는 단계별 지침을 제공 합니다. Azure SQL Database를 사용하여 SSIS 카탈로그를 호스트합니다. 
- [Azure-SSIS IR를 만듭니다](create-azure-ssis-integration-runtime.md). 이 문서는 자습서를 확장 합니다. 가상 네트워크의 가상 네트워크 서비스 엔드포인트 또는 관리 되는 인스턴스와 함께 Azure SQL Database를 사용 하 여 SSIS 카탈로그를 호스트 하는 방법에 대한 지침을 제공 합니다. Azure-SSIS IR를 가상 네트워크에 연결 하는 방법을 보여 줍니다. 
- [Azure-SSIS IR 모니터링](monitor-integration-runtime.md#azure-ssis-integration-runtime). 이 문서에서는 Azure-SSIS IR에 대한 정보를 가져오는 방법을 보여 줍니다. 반환 된 정보에 대한 상태 설명을 제공 합니다. 
- [Azure-SSIS IR 관리](manage-azure-ssis-integration-runtime.md). 이 문서에서는 Azure-SSIS IR를 중지, 시작 또는 삭제 하는 방법을 보여 줍니다. 또한 노드를 추가하여 Azure-SSIS IR을 규모 확장하는 방법을 보여줍니다.