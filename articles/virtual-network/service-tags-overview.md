---
title: Azure 서비스 태그 개요
titlesuffix: Azure Virtual Network
description: 서비스 태그에 대해 알아봅니다. 서비스 태그는 보안 규칙 생성의 복잡성을 최소화 하는 데 도움이 됩니다.
services: virtual-network
documentationcenter: na
author: jispar
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/22/2019
ms.author: jispar
ms.reviewer: kumud
ms.openlocfilehash: 270b3f0fe7c654d2bf059784e872b7dbe97a6068
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190484"
---
# <a name="virtual-network-service-tags"></a>가상 네트워크 서비스 태그 
<a name="network-service-tags"></a>

서비스 태그는 지정 된 Azure 서비스에서 IP 주소 접두사 그룹을 나타냅니다. Microsoft는 서비스 태그가 있는 주소 접두사를 관리 하 고, 주소가 변경 되 면 서비스 태그를 자동으로 업데이트 하 여 네트워크 보안 규칙에 대 한 빈번한 업데이트의 복잡성을 최소화 합니다. 

서비스 태그를 사용 하 여 [네트워크 보안 그룹](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) 또는 [Azure 방화벽](https://docs.microsoft.com/azure/firewall/service-tags)에서 네트워크 액세스 제어를 정의할 수 있습니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용 합니다. 규칙의 적절 한 *원본* 또는 *대상* 필드에 서비스 태그 이름 (예: **microsoft.apimanagement**)을 지정 하 여 해당 서비스에 대 한 트래픽을 허용 하거나 거부할 수 있습니다. 

공용 끝점이 있는 Azure 서비스에 액세스 하는 동안 서비스 태그를 사용 하 여 네트워크 격리를 수행 하 고 일반 인터넷에서 Azure 리소스를 보호할 수 있습니다. 인바운드/아웃 바운드 네트워크 보안 그룹 규칙을 만들어 **인터넷** 에서 트래픽을 거부 하 고 **azurecloud** 또는 특정 Azure 서비스의 다른 [사용 가능한 서비스 태그로](#available-service-tags) 들어오고 나가는 트래픽을 허용 합니다. 

## <a name="available-service-tags"></a>사용 가능한 서비스 태그
다음 표에는 [네트워크 보안 그룹](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) 규칙에서 사용할 수 있는 모든 서비스 태그가 나와 있습니다.

열은 태그의 여부를 나타냅니다.

- 는 인바운드 또는 아웃 바운드 트래픽을 포함 하는 규칙에 적합 합니다.
- [지역](https://azure.microsoft.com/regions) 범위를 지원 합니다.
- [Azure 방화벽](https://docs.microsoft.com/azure/firewall/service-tags) 규칙에서 사용할 수 있습니다.

기본적으로 서비스 태그는 전체 클라우드의 범위를 반영 합니다. 일부 서비스 태그는 해당 IP 범위를 지정 된 지역으로 제한 하 여 더 세부적인 제어를 허용 합니다. 예를 들어 서비스 태그 **저장소** 는 전체 클라우드의 Azure Storage를 나타내지만 WestUS는 WestUS 지역의 저장소 IP 주소 범위로만 범위를 좁힙니다 **.** 다음 표에서는 각 서비스 태그가 이러한 지역 범위를 지원 하는지 여부를 나타냅니다.  

| 태그 | 목적 | 인바운드 또는 아웃 바운드를 사용할 수 있나요? | 지역별 일 수 있나요? | Azure 방화벽과 함께 사용할 수 있나요? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ApiManagement** | Azure API Management 전용 배포에 대 한 관리 트래픽 <br/><br/>*참고:* 이 태그는 지역별 제어 평면에 대 한 Azure API Management 서비스 끝점을 나타냅니다. 이렇게 하면 고객이 API Management 서비스에 구성 된 Api, 작업, 정책, NamedValues에 대해 관리 작업을 수행할 수 있습니다.  | 인바운드 | yes | yes |
| **ApplicationInsightsAvailability** | Application Insights 가용성. | 인바운드 | 예 | 예 |
| **AppService**    | Azure App Service 이 태그는 웹 앱 프런트 엔드에 대 한 아웃 바운드 보안 규칙에 권장 됩니다. | 아웃바운드 | yes | yes |
| **AppServiceManagement** | App Service Environment 전용 배포에 대 한 관리 트래픽 | 모두 | 예 | yes |
| **AzureActiveDirectory** | Azure Active Directory. | 아웃바운드 | 예 | yes |
| **AzureActiveDirectoryDomainServices** | Azure Active Directory Domain Services 전용 배포에 대 한 관리 트래픽 | 모두 | 예 | yes |
| **AzureAdvancedThreatProtection** | Azure Advanced Threat Protection. | 아웃바운드 | 예 | 예 |
| **AzureBackup** |Azure Backup.<br/><br/>*참고:* 이 태그는 **Storage** 및 **AzureActiveDirectory** 태그에 종속 됩니다. | 아웃바운드 | 예 | yes |
| **AzureBotService** | Azure Bot Service. | 아웃바운드 | 예 | 예 |
| **AzureCloud** | 모든 [데이터 센터 공용 IP 주소](https://www.microsoft.com/download/details.aspx?id=56519)입니다. | 아웃바운드 | yes | yes |
| **AzureCognitiveSearch** | Azure Cognitive Search (기술와 함께 인덱서를 사용 하는 경우). | 모두 | 예 | 예 |
| **AzureConnectors** | 프로브/백 엔드 연결용 커넥터를 Azure Logic Apps 합니다. | 인바운드 | yes | yes |
| **AzureContainerRegistry** | Azure Container Registry. | 아웃바운드 | yes | yes |
| **Microsoft.azurecosmosdb** | Azure Cosmos DB. | 아웃바운드 | yes | yes |
| **AzureDatabricks** | Azure Databricks. | 모두 | 예 | 예 |
| **AzureDataExplorerManagement** | Azure 데이터 탐색기 관리. | 인바운드 | 예 | 예 |
| **AzureDataLake** | Azure Data Lake. | 아웃바운드 | 예 | yes |
| **AzureEventGrid** | Azure Event Grid. <br/><br/>*참고:* 이 태그는 미국 남부 중부, 미국 동부, 미국 동부 2, 미국 서 부 2 및 미국 중부 전용의 Azure Event Grid 끝점을 포함 합니다. | 모두 | 예 | 예 |
| **AzureFrontDoor** | Azure 전면 도어. | 모두 | 예 | 예 |
| **AzureInformationProtection** | Azure Information Protection.<br/><br/>*참고:* 이 태그는 **AzureActiveDirectory** 및 **AzureFrontDoor** 태그에 종속 됩니다. 다음 Ip (이 종속성이 곧 제거 될 예정)를 허용 목록. 13.107.6.181 & 13.107.9.181. | 아웃바운드 | 예 | 예 |
| **AzureIoTHub** | Azure IoT Hub. | 아웃바운드 | 예 | 예 |
| **AzureKeyVault** | Azure Key Vault.<br/><br/>*참고:* 이 태그는 **AzureActiveDirectory** 태그에 종속 됩니다. | 아웃바운드 | yes | yes |
| **AzureLoadBalancer** | Azure 인프라 부하 분산 장치. 태그는 Azure 상태 검색이 시작 되는 [호스트의 가상 IP 주소](security-overview.md#azure-platform-considerations) (168.63.129.16)로 변환 됩니다. 여기에는 Azure Load Balancer 리소스에 대 한 트래픽이 포함 되지 않습니다. Azure Load Balancer 사용 하지 않는 경우이 규칙을 재정의할 수 있습니다. | 모두 | 예 | 예 |
| **AzureMachineLearning** | Azure Machine Learning입니다. | 모두 | 예 | yes |
| **AzureMonitor** | Log Analytics, Application Insights, AzMon 및 사용자 지정 메트릭 (4Gb 끝점)을 지정 합니다.<br/><br/>*참고:* Log Analytics의 경우이 태그는 **저장소** 태그에 종속 됩니다. | 아웃바운드 | 예 | yes |
| **AzurePlatformDNS** | 기본 인프라 (기본) DNS 서비스입니다.<br/><br>이 태그를 사용 하 여 기본 DNS를 사용 하지 않도록 설정할 수 있습니다. 이 태그를 사용 하는 경우 주의 해야 합니다. [Azure 플랫폼 고려 사항을](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)참조 하는 것이 좋습니다. 또한이 태그를 사용 하기 전에 테스트를 수행 하는 것이 좋습니다. | 아웃바운드 | 예 | 예 |
| **AzurePlatformIMDS** | Azure Instance Metadata Service (IMDS)-기본 인프라 서비스입니다.<br/><br/>이 태그를 사용 하 여 기본 IMDS를 사용 하지 않도록 설정할 수 있습니다. 이 태그를 사용 하는 경우 주의 해야 합니다. [Azure 플랫폼 고려 사항을](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)참조 하는 것이 좋습니다. 또한이 태그를 사용 하기 전에 테스트를 수행 하는 것이 좋습니다. | 아웃바운드 | 예 | 예 |
| **AzurePlatformLKM** | Windows 라이선스 또는 키 관리 서비스입니다.<br/><br/>이 태그를 사용 하 여 라이선스에 대 한 기본값을 사용 하지 않도록 설정할 수 있습니다. 이 태그를 사용 하는 경우 주의 해야 합니다. [Azure 플랫폼 고려 사항을](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)참조 하는 것이 좋습니다.  또한이 태그를 사용 하기 전에 테스트를 수행 하는 것이 좋습니다. | 아웃바운드 | 예 | 예 |
| **AzureResourceManager** | Azure Resource Manager. | 아웃바운드 | 예 | 예 |
| **AzureSiteRecovery** | Azure Site Recovery.<br/><br/>*참고:* 이 태그는 **Storage**, **AzureActiveDirectory**및 **EventHub** 태그에 종속 됩니다. | 아웃바운드 | 예 | 예 |
| **AzureTrafficManager** | Azure Traffic Manager 프로브 IP 주소.<br/><br/>Traffic Manager 프로브 IP 주소에 대 한 자세한 내용은 [Azure TRAFFIC MANAGER FAQ](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs)를 참조 하세요. | 인바운드 | 예 | yes |  
| **BatchNodeManagement** | Azure Batch 전용 배포에 대 한 관리 트래픽 | 모두 | 예 | yes |
| **CognitiveServicesManagement** | Azure Cognitive Services에 대 한 트래픽 주소 범위입니다. | 아웃바운드 | 예 | 예 |
| **Dynamics365ForMarketingEmail** | Dynamics 365 마케팅 메일 서비스의 주소 범위입니다. | 아웃바운드 | yes | 예 |
| **ElasticAFD** | 탄력적 Azure Front 도어. | 모두 | 예 | 예 |
| **EventHub** | Azure Event Hubs. | 아웃바운드 | yes | yes |
| **게이트웨이 관리자** | Azure VPN Gateway 및 Application Gateway 전용 배포에 대 한 관리 트래픽 | 인바운드 | 예 | 예 |
| **GuestAndHybridManagement** | Azure Automation 및 게스트 구성. | 아웃바운드 | 예 | yes |
| **HDInsight** | Azure HDInsight. | 인바운드 | yes | 예 |
| **인터넷** | 가상 네트워크 외부에 있고 공용 인터넷에서 연결할 수 있는 IP 주소 공간입니다.<br/><br/>주소 범위에는 [Azure 소유의 공용 IP 주소 공간이](https://www.microsoft.com/download/details.aspx?id=41653)포함 됩니다. | 모두 | 예 | 예 |
| **MicrosoftCloudAppSecurity** | Microsoft Cloud App Security. | 아웃바운드 | 예 | 예 |
| **MicrosoftContainerRegistry** | Microsoft 컨테이너 이미지용 컨테이너 레지스트리 <br/><br/>*참고:* 다음 IP를 허용 목록 하세요 (이 종속성은 곧 제거 될 예정). 204.79.197.219. | 아웃바운드 | yes | yes |
| **Service Bus** | 프리미엄 서비스 계층을 사용 하는 Azure Service Bus 트래픽 | 아웃바운드 | yes | yes |
| **ServiceFabric** | Azure Service Fabric.<br/><br/>*참고:* 이 태그는 지역별 컨트롤 평면에 대 한 Service Fabric 서비스 끝점을 나타냅니다. 이를 통해 고객은 자신의 VNET에서 Service Fabric 클러스터에 대 한 관리 작업을 수행할 수 있습니다 (예: https://westus.servicefabric.azure.com) | 모두 | 예 | 예 |
| **Sql** | Azure SQL Database, Azure Database for MySQL, Azure Database for PostgreSQL 및 Azure SQL Data Warehouse.<br/><br/>*참고:* 이 태그는 서비스의 특정 인스턴스가 아니라 서비스를 나타냅니다. 예를 들어 태그는 특정 SQL 데이터베이스 또는 서버가 아닌 Azure SQL Database 서비스를 나타냅니다. | 아웃바운드 | yes | yes |
| **SqlManagement** | SQL 전용 배포에 대 한 관리 트래픽 | 모두 | 예 | yes |
| **스토리지** | Azure Storage. <br/><br/>*참고:* 이 태그는 서비스의 특정 인스턴스가 아니라 서비스를 나타냅니다. 예를 들어 태그는 특정 Azure Storage 계정이 아닌 Azure Storage 서비스를 나타냅니다. | 아웃바운드 | yes | yes |
| **VirtualNetwork** | 가상 네트워크 주소 공간 (가상 네트워크에 대해 정의 된 모든 IP 주소 범위), 연결 된 모든 온-프레미스 주소 공간, [피어 링](virtual-network-peering-overview.md) 가상 네트워크, [가상 네트워크 게이트웨이에](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json)연결 된 가상 네트워크, [호스트의 가상 IP 주소](security-overview.md#azure-platform-considerations)및 [사용자 정의 경로](virtual-networks-udr-overview.md)에 사용 되는 주소 접두사입니다. 이 태그에는 기본 경로가 포함 될 수도 있습니다. | 모두 | 예 | 예 |

>[!NOTE]
>클래식 배포 모델에서 (Azure Resource Manager 하기 전에) 이전 표에 나열 된 태그의 하위 집합이 지원 됩니다. 이러한 태그의 철자가 다릅니다.
>
>| 클래식 맞춤법 | 해당 리소스 관리자 태그 |
>|---|---|
>| AZURE_LOADBALANCER | AzureLoadBalancer |
>| 인터넷 | 인터넷 |
>| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Azure 서비스의 서비스 태그는 사용 중인 특정 클라우드의 주소 접두사를 나타냅니다. 예를 들어 Azure 공용 클라우드의 **Sql** 태그 값에 해당 하는 기본 IP 범위는 azure 중국 클라우드의 기본 범위와 다릅니다.

> [!NOTE]
> Azure Storage 또는 Azure SQL Database와 같은 서비스에 [가상 네트워크 서비스 엔드포인트](virtual-network-service-endpoints-overview.md)를 구현하는 경우 Azure는 서비스의 가상 네트워크 서브넷에 [경로](virtual-networks-udr-overview.md#optional-default-routes)를 추가합니다. 경로의 주소 접두사는 해당 하는 서비스 태그와 동일한 주소 접두사 또는 CIDR 범위입니다.

## <a name="service-tags-on-premises"></a>온-프레미스 서비스 태그  
온-프레미스 방화벽 구성의 일부로 포함할 현재 서비스 태그 및 범위 정보를 얻을 수 있습니다. 이 정보는 각 서비스 태그에 해당 하는 IP 범위의 현재 지정 시간 목록입니다. 다음 섹션에 설명 된 대로 프로그래밍 방식으로 또는 JSON 파일 다운로드를 통해 정보를 가져올 수 있습니다.

### <a name="use-the-service-tag-discovery-api-public-preview"></a>서비스 태그 검색 API (공개 미리 보기) 사용
IP 주소 범위 세부 정보와 함께 서비스 태그의 현재 목록을 프로그래밍 방식으로 검색할 수 있습니다.

- [REST (영문)](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [Azure CLI](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> 공개 미리 보기로 제공 되는 동안 검색 API는 JSON 다운로드에서 반환 된 정보 보다 최신 정보가 아닌 정보를 반환할 수 있습니다. (다음 섹션을 참조하십시오.)


### <a name="discover-service-tags-by-using-downloadable-json-files"></a>다운로드 가능한 JSON 파일을 사용 하 여 서비스 태그 검색 
현재 서비스 태그 목록이 포함 된 JSON 파일을 IP 주소 범위 정보와 함께 다운로드할 수 있습니다. 이러한 목록은 매주 업데이트 및 게시 됩니다. 각 클라우드의 위치는 다음과 같습니다.

- [Azure 공용](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure 미국 정부](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure 중국](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure 독일](https://www.microsoft.com/download/details.aspx?id=57064)   

> [!NOTE]
>이 정보의 하위 집합은 [Azure 공용](https://www.microsoft.com/download/details.aspx?id=41653), [Azure 중국](https://www.microsoft.com/download/details.aspx?id=42064)및 [azure 독일](https://www.microsoft.com/download/details.aspx?id=54770)용 XML 파일에 게시 되었습니다. 이러한 XML 다운로드는 2020 년 6 월 30 일부 터 더 이상 사용 되지 않으며 해당 날짜 이후에는 더 이상 사용할 수 없습니다. 이전 섹션에서 설명한 대로 검색 API 또는 JSON 파일 다운로드를 사용 하 여로 마이그레이션해야 합니다.

### <a name="tips"></a>팁 
- JSON 파일에서 증가 된 *changeNumber* 값을 확인 하 여 한 게시에서 다음 게시로 업데이트를 검색할 수 있습니다. 각 하위 섹션 (예: **WestUS**)에는 변경 사항이 발생할 때마다 증가 하는 고유한 *changeNumber* 가 있습니다. 하위 섹션 중 하나가 변경 되 면 파일 *changeNumber* 의 최상위 수준이 증가 합니다.
- 서비스 태그 정보를 구문 분석 하는 방법에 대 한 예제는 (예: WestUS의 저장소에 대 한 모든 주소 범위 가져오기) [서비스 태그 검색 API PowerShell](https://aka.ms/discoveryapi_powershell) 설명서를 참조 하세요.

## <a name="next-steps"></a>다음 단계
- [네트워크 보안 그룹을 만드는](tutorial-filter-network-traffic.md)방법에 대해 알아봅니다.
