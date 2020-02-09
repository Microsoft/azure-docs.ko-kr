---
title: Azure 서비스에 대한 가상 네트워크
titlesuffix: Azure Virtual Network
description: 가상 네트워크에 리소스를 배포하는 이점을 알아봅니다. 가상 네트워크의 리소스는 트래픽이 인터넷을 트래버스하지 않고 서로 간에, 그리고 온-프레미스 리소스와 통신할 수 있습니다.
services: virtual-network
documentationcenter: na
author: malopMSFT
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: malop
ms.reviewer: kumud
ms.openlocfilehash: 24bcc7e698527cd39958c53b48a0b36404c36bb4
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048837"
---
# <a name="virtual-network-integration-for-azure-services"></a>Azure 서비스에 대한 가상 네트워크 통합

Azure 서비스를 Azure 가상 네트워크에 통합하면 가상 네트워크의 가상 머신 또는 컴퓨팅 리소스에서 서비스에 대한 비공개 액세스가 가능합니다.
다음 옵션을 사용하여 가상 네트워크의 Azure 서비스를 통합할 수 있습니다.
- 서비스의 전용 인스턴스를 가상 네트워크에 배포합니다. 이렇게 하면 가상 네트워크 내에서 그리고 온-프레미스에서 서비스에 비공개적으로 액세스할 수 있습니다.
- [개인 링크](../private-link/private-link-overview.md) 를 사용 하 여 가상 네트워크 및 온-프레미스 네트워크에서 서비스의 개인용 특정 인스턴스에 액세스할 수 있습니다.

서비스 [엔드포인트](virtual-network-service-endpoints-overview.md)을 통해 가상 네트워크를 서비스로 확장 하 여 공용 엔드포인트을 사용 하 여 서비스에 액세스할 수도 있습니다. 서비스 엔드포인트을 사용 하면 가상 네트워크에 대 한 서비스 리소스의 보안을 유지할 수 있습니다.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>가상 네트워크에 Azure 서비스 배포

[가상 네트워크](virtual-networks-overview.md)에 전용 Azure 서비스를 배포하는 경우 개인 IP 주소를 통해 개인적으로 서비스 리소스와 통신할 수 있습니다.

![가상 네트워크에 배포된 서비스](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

가상 네트워크 내에 서비스를 배포하면 다음과 같은 기능이 제공됩니다.

- 가상 네트워크 내의 리소스는 개인 IP 주소를 통해 개인적으로 서로 통신할 수 있습니다. 가상 네트워크의 가상 머신에서 실행 중인 SQL Server와 HDInsight 간에 데이터를 전송하는 예제입니다.
- 온-프레미스 리소스는 [사이트 간 VPN(VPN Gateway)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) 또는 [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 통해 개인 IP 주소를 사용하여 가상 네트워크의 리소스에 액세스할 수 있습니다.
- 가상 네트워크를 [피어링](virtual-network-peering-overview.md)하여 가상 네트워크의 리소스가 개인 IP 주소로 서로 간에 통신하도록 할 수 있습니다.
- 가상 네트워크의 서비스 인스턴스는 일반적으로 Azure 서비스를 통해 완전히 관리 됩니다. 여기에는 리소스의 상태를 모니터링 하 고 부하를 통해 크기를 조정 하는 작업이 포함 됩니다.
- 서비스 인스턴스는 가상 네트워크의 서브넷에 배포됩니다. 서브넷에 대 한 인바운드 및 아웃 바운드 네트워크 액세스는 서비스에서 제공 하는 지침에 따라 [네트워크 보안 그룹](security-overview.md#network-security-groups)을 통해 열어야 합니다.
- 또한 특정 서비스는 배포 되는 서브넷에 제한을 적용 하 여 정책 응용 프로그램을 제한 하 고 동일한 서브넷 내에서 Vm 및 서비스 리소스를 라우트 또는 결합 합니다. 시간이 지남에 따라 변경 될 수 있으므로 특정 제한 사항에 대 한 각 서비스를 확인 합니다. 이러한 서비스의 예는 Azure NetApp Files, 전용 HSM, Azure Container Instances, App Service입니다. 
- 경우에 따라 서비스에서는 서브넷이 특정 서비스를 호스트할 수 있다는 명시적 식별자로써 [위임된 서브넷](virtual-network-manage-subnet.md#add-a-subnet)을 요구할 수 있습니다. 위임 하는 경우 서비스는 위임 된 서브넷에 서비스별 리소스를 만들 수 있는 명시적 권한을 부여 합니다.
- [위임 된 서브넷을 사용 하는 가상 네트워크](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/get#get-virtual-network-with-a-delegated-subnet)에 대 한 REST API 응답의 예를 참조 하세요. 위임 된 서브넷 모델을 사용 하는 서비스의 포괄적인 목록은 [사용 가능한 위임](https://docs.microsoft.com/rest/api/virtualnetwork/availabledelegations/list) API를 통해 얻을 수 있습니다.

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>가상 네트워크에 배포할 수 있는 서비스

|범주|Service| 전용 ¹ 서브넷
|-|-|-|
| 컴퓨팅 | 가상 머신: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[가상 머신 확장 집합](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[클라우드 서비스](https://msdn.microsoft.com/library/azure/jj156091): 가상 네트워크(클래식)만 해당<br/> [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| 아니요 <br/> 아니요 <br/> 아니요 <br/> 없음 ²
| 네트워크 | [Application Gateway - WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[네트워크 가상 어플라이언스](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn) | 예 <br/> 예 <br/> 예 <br/> 아니요
|데이터|[RedisCache](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure SQL Database Managed Instance](../sql-database/sql-database-managed-instance-connectivity-architecture.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| 예 <br/> 예 <br/> 
|분석 | [Azure HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |없음 ² <br/> 없음 ² <br/> 
| Identity | [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |아니요 <br/>
| 컨테이너 | [AKS(Azure Kubernetes Service)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[ACI(Azure Container Instance)](https://www.aka.ms/acivnet)<br/>Azure Virtual Network CNI [플러그 인](https://github.com/Azure/acs-engine)을 사용하는 [Azure Container Service 엔진](https://github.com/Azure/acs-engine/tree/master/examples/vnet)|없음 ²<br/> 예 <br/><br/> 아니요
| 웹 | [API Management](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[App Service Environment](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|예 <br/> 예 <br/> 예
| 호스트형 | [Azure 전용 HSM](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|예 <br/> 예 <br/>
| | |

¹ ' 전용 '은 서비스 특정 리소스만이 서브넷에 배포할 수 있으며 고객 VM/VMSSs와 함께 사용할 수 없음을 의미 합니다. <br/> ² 서비스에 적용 되는 필수 요구 사항은 아니지만 전용 서브넷에 이러한 서비스를 포함 하는 것이 가장 좋은 방법입니다.
