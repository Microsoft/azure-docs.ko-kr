---
title: Azure SQL Database Managed Instance 연결 애플리케이션 | Microsoft Docs
description: 이 아티클에서는 Azure SQL Database Managed Instance에 애플리케이션을 연결하는 방법을 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 11/09/2018
ms.openlocfilehash: ed9fbdd3e999cfd262ecbcf05a843c19cc969ed1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60701307"
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>애플리케이션을 Azure SQL Database Managed Instance에 연결

현재 애플리케이션을 호스트하는 방법과 위치를 결정하는 여러 가지 옵션이 있습니다.

Azure App Service 또는 Azure VNet(가상 네트워크) 통합 옵션 중 일부(예: Azure App Service 환경, Virtual Machine, Virtual Machine Scale Set)를 사용하여 클라우드에서 애플리케이션을 호스팅할 수 있습니다. 하이브리드 클라우드 접근 방법을 사용하고 애플리케이션을 온-프레미스에 유지할 수도 있습니다.

어떤 방법을 선택하든지 Managed Instance에 연결할 수 있습니다.  

![고가용성](./media/sql-database-managed-instance/application-deployment-topologies.png)

## <a name="connect-an-application-inside-the-same-vnet"></a>동일한 VNet 내에서 애플리케이션 연결

이 시나리오가 가장 간단합니다. VNet 내의 가상 머신은 다른 서브넷 내에 있더라도 서로 직접 연결할 수 있습니다. 즉, Azure 애플리케이션 환경 또는 Virtual Machine 내 애플리케이션을 연결하려면 연결 문자열을 적절하게 설정하기만 하면 됩니다.  

## <a name="connect-an-application-inside-a-different-vnet"></a>다른 VNet 내에서 애플리케이션 연결

이 시나리오에서는 Managed Instance가 고유한 VNet의 개인 IP 주소를 포함하기 때문에 조금 복잡합니다. 연결하려면 Managed Instance를 배포할 때 애플리케이션이 VNet에 액세스해야 합니다. 따라서 먼저 애플리케이션과 Managed Instance VNet이 연결되어야 합니다. 이 시나리오가 작동하기 위해 VNet이 동일한 구독에 있을 필요는 없습니다.

VNet을 연결하는 두 가지 옵션이 있습니다.

- [Azure Virtual Network 피어링](../virtual-network/virtual-network-peering-overview.md)
- VNet 간 VPN 게이트웨이([Azure Portal](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md))

피어링이 Microsoft 백본 네트워크를 사용하기 때문에 피어링 옵션은 적합한 기능입니다. 따라서 연결성 관점에서 피어링된 VNet 및 동일한 VNet에 있는 가상 머신 간에 대기 시간이 눈에 띄는 차이점은 없습니다. VNet 피어링은 동일한 지역의 네트워크로 제한됩니다.  

> [!IMPORTANT]
> Managed Instance에 대한 VNet 피어링 시나리오는 [전역 가상 네트워크 피어 링 제약 조건](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)으로 인해 동일한 지역의 네트워크로 제한됩니다.

## <a name="connect-an-on-premises-application"></a>온-프레미스 애플리케이션 연결

Managed Instance는 개인 IP 주소를 통해서만 액세스할 수 있습니다. 온-프레미스에서 액세스하기 위해 애플리케이션과 Managed Instance VNet 간에 사이트 간 연결을 만들어야 합니다.

온-프레미스를 Azure VNet에 연결하는 두 개의 옵션이 있습니다.

- 사이트 간 VPN 연결([Azure Portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md))
- [ExpressRoute](../expressroute/expressroute-introduction.md) 연결  

온-프레미스와 Azure 연결을 성공적으로 설정하고 Managed Instance에 연결을 설정할 수 없는 경우 리디렉션을 위해 방화벽에서 11000~12000 포트 범위뿐만 아니라 SQL 포트 1433의 아웃바운드 연결이 열려 있는지 확인합니다.

## <a name="connect-an-application-on-the-developers-box"></a>개발자 상자에서 애플리케이션 연결

Managed Instance는 개인 IP 주소를 통해서만 액세스할 수 있습니다. 따라서 개발자 상자에서 액세스하려면 먼저 개발자 상자와 Managed Instance VNet 간에 연결을 만들어야 합니다. 이렇게 하려면 네이티브 Azure 인증서 인증을 사용하여 VNet에 지점 및 사이트 간 연결을 구성합니다. 자세한 내용은 [온-프레미스 컴퓨터에서 Azure SQL Database Managed Instance로 연결 지점-사이트 간 연결 구성](sql-database-managed-instance-configure-p2s.md)을 참조하세요.

## <a name="connect-from-on-premises-with-vnet-peering"></a>VNet 피어링을 사용하여 온-프레미스에서 연결

고객에 의해 구현되는 또 다른 시나리오는 VPN Gateway가 한 호스팅 Managed Instance의 구독 및 별도 가상 네트워크에 설치된 경우입니다. 그러면 이 두 개의 가상 네트워크가 피어링됩니다. 그러한 구현 방식이 다음 샘플 아키텍처 다이어그램에 나와 있습니다.

![VNet 피어링](./media/sql-database-managed-instance-connect-app/vnet-peering.png)

기본 인프라를 설정하고 나면 VPN Gateway가 Managed Instance를 호스팅하는 가상 네트워크에서 IP 주소를 볼 수 있도록 일부 설정을 수정해야 합니다. 이렇게 하려면 **피어링 설정**에서 다음과 같이 매우 구체적으로 변경합니다.

1. VPN Gateway를 호스팅하는 VNet에서 **피어링**, Managed Instance 피어링된 VNet 연결로 차례로 이동한 다음, **게이트웨이 전송 허용**을 클릭합니다.
2. Managed Instance를 호스팅하는 VNet에서 **피어링**, VPN Gateway 피어링된 VNet 연결로 차례로 이동한 다음, **원격 게이트웨이 사용**을 클릭합니다.

## <a name="connect-an-azure-app-service-hosted-application"></a>Azure App Service 호스트 애플리케이션 연결

Managed Instance는 개인 IP 주소를 통해서만 액세스할 수 있습니다. 따라서 Azure App Service에서 액세스하려면 먼저 애플리케이션과 Managed Instance VNet 간에 연결을 만들어야 합니다. [Azure Virtual Network에 앱 통합](../app-service/web-sites-integrate-with-vnet.md)을 참조하세요.  

문제 해결은 [VNet 및 애플리케이션 문제 해결](../app-service/web-sites-integrate-with-vnet.md#troubleshooting)을 참조하세요. 연결을 설정할 수 없는 경우 [네트워킹 구성 동기화](sql-database-managed-instance-sync-network-configuration.md)를 시도하세요.

특수한 경우에 Azure App Service를 Managed Instance에 연결합니다. 즉, Managed Instance VNet에 피어링된 네트워크에 Azure App Service를 통합하는 경우입니다. 해당 경우에는 다음 구성을 설정해야 합니다.

- Managed Instance VNet에는 게이트웨이가 없어야 합니다.  
- Managed Instance VNet에는 원격 게이트웨이 사용 옵션을 설정해야 합니다.
- 피어링된 VNet에는 게이트웨이 전송 허용 옵션을 설정해야 합니다.

이 시나리오는 다음 다이어그램에서 설명합니다.

![통합 앱 피어링](./media/sql-database-managed-instance/integrated-app-peering.png)

>[!NOTE]
>VNet 통합 기능은 ExpressRoute 게이트웨이가 있는 VNet에 앱을 통합하지 않습니다. ExpressRoute 게이트웨이가 공존 모드로 구성되어 있어도 VNet 통합은 작동하지 않습니다. ExpressRoute 연결을 통해 리소스에 액세스해야 하는 경우에는 VNet에서 실행되는 App Service Environment를 사용할 수 있습니다.

## <a name="troubleshooting-connectivity-issues"></a>연결 문제 해결

연결 문제를 해결하려면 다음을 검토합니다.

- VNet은 동일하지만 서브넷이 다른 Azure 가상 머신에서 Managed Instance에 연결할 수 없는 경우 액세스를 차단할 수도 있는 NSG(네트워크 보안 그룹)를 VM 서브넷으로 설정했는지 확인합니다. 또한 11000-12000 범위의 포트와 마찬가지로 SQL 포트 1433에서 아웃바운드 연결을 개방해야 합니다. Azure 경계 내 리디렉션을 통한 연결에 필요하기 때문입니다.
- VNet과 연결된 라우트 테이블에 대해 BGP 전파를 **사용**으로 설정해야 합니다.
- P2S VPN을 사용할 경우 Azure Portal에서 구성을 확인하여 **수신/송신** 숫자가 보이는지 확인합니다. 0이 아닌 숫자는 Azure가 온-프레미스에서 트래픽을 라우팅하는 것을 나타냅니다.

   ![수신/송신 숫자](./media/sql-database-managed-instance-connect-app/ingress-egress-numbers.png)

- 클라이언트 머신(VPN 클라이언트를 사용 중인)에 사용자가 액세스해야 하는 모든 VNet에 대한 경로 항목이 있는지 확인합니다. 경로는 `%AppData%\ Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt`에 저장됩니다.

   ![route.txt](./media/sql-database-managed-instance-connect-app/route-txt.png)

   이 이미지와 같이 관련된 각 VNet에 대한 항목이 두 개, 포털에서 구성된 VPN 엔드포인트에 대한 항목이 세 개 있습니다.

   경로를 확인하는 또 다른 방법은 다음 명령을 사용하는 것입니다. 출력에는 다양한 서브넷에 대한 경로가 표시됩니다.

   ```cmd
   C:\ >route print -4
   ===========================================================================
   Interface List
   14...54 ee 75 67 6b 39 ......Intel(R) Ethernet Connection (3) I218-LM
   57...........................rndatavnet
   18...94 65 9c 7d e5 ce ......Intel(R) Dual Band Wireless-AC 7265
   1...........................Software Loopback Interface 1
   Adapter===========================================================================

   IPv4 Route Table
   ===========================================================================
   Active Routes:
   Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0       10.83.72.1     10.83.74.112     35
         10.0.0.0    255.255.255.0         On-link       172.26.34.2     43
         10.4.0.0    255.255.255.0         On-link       172.26.34.2     43
   ===========================================================================
   Persistent Routes:
   None
   ```

- VNet 피어링을 사용하는 경우 [게이트웨이 전송 허용 및 원격 게이트웨이 사용](#connect-from-on-premises-with-vnet-peering) 설정을 위한 지침을 따랐는지 확인합니다.

## <a name="required-versions-of-drivers-and-tools"></a>드라이버 및 도구의 필요한 버전

Managed Instance에 연결하려면 다음과 같은 버전 이상의 도구와 드라이버를 사용하는 것이 좋습니다.

| 드라이버/도구 | Version |
| --- | --- |
|.NET Framework | 4.6.1(또는 .NET Core) |
|ODBC 드라이버| v17 |
|PHP 드라이버| 5.2.0 |
|JDBC 드라이버| 6.4.0 |
|Node.js 드라이버| 2.1.1 |
|OLEDB 드라이버| 18.0.2.0 |
|SSMS| 17.8.1 [이상](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) |

## <a name="next-steps"></a>다음 단계

- Managed Instance에 대한 자세한 내용은 [Managed Instance란?](sql-database-managed-instance.md)을 참조하세요.
- Managed Instance를 새로 만드는 방법을 보여 주는 자습서에 대해서는 [Managed Instance 만들기](sql-database-managed-instance-get-started.md)를 참조하세요.
