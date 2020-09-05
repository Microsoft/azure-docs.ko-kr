---
title: 응용 프로그램을 SQL Managed Instance에 연결
titleSuffix: Azure SQL Managed Instance
description: 이 문서에서는 Azure SQL Managed Instance에 응용 프로그램을 연결 하는 방법을 설명 합니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab, vanto
ms.date: 11/09/2018
ms.openlocfilehash: a04f4879bbd06c2fa6c1af921d7adafdef9417d6
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88871448"
---
# <a name="connect-your-application-to-azure-sql-managed-instance"></a>애플리케이션을 Azure SQL Managed Instance에 연결
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

현재 애플리케이션을 호스트하는 방법과 위치를 결정하는 여러 가지 옵션이 있습니다.

Azure App Service 또는 Azure App Service Environment, Azure Virtual Machines 및 가상 머신 확장 집합과 같은 Azure의 가상 네트워크 통합 옵션 중 일부를 사용 하 여 클라우드에서 응용 프로그램을 호스트 하도록 선택할 수 있습니다. 하이브리드 클라우드 접근 방법을 사용하고 애플리케이션을 온-프레미스에 유지할 수도 있습니다.

어떤 옵션을 선택 하든지 Azure SQL Managed Instance에 연결할 수 있습니다. 

![고가용성](./media/connect-application-instance/application-deployment-topologies.png)

이 문서는 다양한 애플리케이션 시나리오에서 애플리케이션을 Azure SQL Managed Instance에 연결하는 방법을 설명합니다. 

## <a name="connect-inside-the-same-vnet"></a>동일한 VNet 내에서 연결

SQL Managed Instance와 동일한 가상 네트워크 내에 응용 프로그램을 연결 하는 것이 가장 간단한 시나리오입니다. 가상 네트워크 내의 가상 머신은 서로 다른 서브넷에 있는 경우에도 직접 연결할 수 있습니다. 즉, App Service Environment 또는 가상 머신에 응용 프로그램을 연결 해야 하는 것은 연결 문자열을 적절 하 게 설정 하는 것입니다.  

## <a name="connect-inside-a-different-vnet"></a>다른 VNet 내에서 연결

Sql Managed Instance에서 다른 가상 네트워크에 있는 경우 응용 프로그램을 연결 하는 것은 SQL Managed Instance 자체 가상 네트워크에 개인 IP 주소를 포함 하기 때문에 좀 더 복잡 합니다. 연결 하려면 응용 프로그램에 SQL Managed Instance 배포 된 가상 네트워크에 대 한 액세스 권한이 있어야 합니다. 따라서 응용 프로그램과 SQL Managed Instance 가상 네트워크 간에 연결을 설정 해야 합니다. 이 시나리오가 작동 하려면 가상 네트워크가 동일한 구독에 있지 않아도 됩니다.

가상 네트워크를 연결 하는 두 가지 옵션이 있습니다.

- [Azure VNet 피어 링](../../virtual-network/virtual-network-peering-overview.md)
- VNet 간 VPN 게이트웨이([Azure Portal](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [PowerShell](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [Azure CLI](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md))

피어 링은 Microsoft 백본 네트워크를 사용 하기 때문에 더 좋습니다. 따라서 연결 관점에서 피어 링 가상 네트워크와 동일한 가상 네트워크에 있는 가상 컴퓨터 간의 대기 시간 차이가 크게 차이가 없습니다. 가상 네트워크 피어 링은 동일한 지역의 네트워크로 제한 됩니다.  

> [!IMPORTANT]
> SQL Managed Instance에 대 한 가상 네트워크 피어 링 시나리오는 [글로벌 가상 네트워크 피어 링의 제약 조건](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)으로 인해 동일한 지역의 네트워크로 제한 됩니다. 자세한 내용은 [Azure Virtual network faq](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) (질문과 대답) 문서의 관련 단원을 참조 하세요. 

## <a name="connect-from-on-premises"></a>온-프레미스에서 연결 

온-프레미스 응용 프로그램을 SQL Managed Instance에 연결할 수도 있습니다. SQL Managed Instance은 개인 IP 주소를 통해서만 액세스할 수 있습니다. 온-프레미스에서 액세스 하기 위해 응용 프로그램과 SQL Managed Instance 가상 네트워크 간에 사이트 간 연결을 설정 해야 합니다.

온-프레미스를 Azure 가상 네트워크에 연결 하는 방법에는 두 가지 옵션이 있습니다.

- 사이트 간 VPN 연결 ([Azure Portal](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), [PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [Azure CLI](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md))
- [Azure express](../../expressroute/expressroute-introduction.md) 경로 연결  

온-프레미스에서 Azure로 연결을 성공적으로 설정 하 고 SQL Managed Instance에 연결할 수 없는 경우 방화벽에 SQL 포트 1433에 대 한 열려 있는 아웃 바운드 연결이 있는지 확인 하 고 리디렉션 포트의 11000-11999 범위를 확인 합니다.

## <a name="connect-the-developer-box"></a>개발자 상자 연결

또한 개발자 상자를 SQL Managed Instance에 연결할 수 있습니다. SQL Managed Instance은 개인 IP 주소를 통해서만 액세스할 수 있으므로 개발자 상자에서 액세스 하려면 먼저 개발자 상자와 SQL Managed Instance 가상 네트워크 간에 연결을 설정 해야 합니다. 이렇게 하려면 네이티브 Azure 인증서 인증을 사용 하 여 가상 네트워크에 지점 및 사이트 간 연결을 구성 합니다. 자세한 내용은  [온-프레미스 컴퓨터에서 AZURE SQL Managed Instance 연결 하도록 지점 및 사이트 간 연결 구성](point-to-site-p2s-configure.md)을 참조 하세요.

## <a name="connect-with-vnet-peering"></a>VNet 피어 링을 사용 하 여 연결

고객에 의해 구현 되는 다른 시나리오는 SQL Managed Instance를 호스트 하는 가상 네트워크와 별도의 가상 네트워크에 VPN gateway가 설치 되는 경우입니다. 그러면 이 두 개의 가상 네트워크가 피어링됩니다. 그러한 구현 방식이 다음 샘플 아키텍처 다이어그램에 나와 있습니다.

![가상 네트워크 피어링](./media/connect-application-instance/vnet-peering.png)

기본 인프라를 설정한 후에는 VPN gateway가 SQL Managed Instance를 호스트 하는 가상 네트워크의 IP 주소를 볼 수 있도록 일부 설정을 수정 해야 합니다. 이렇게 하려면 **피어링 설정**에서 다음과 같이 매우 구체적으로 변경합니다.

1. VPN gateway를 호스트 하는 가상 네트워크에서 **피어 링**으로 이동 하 여 SQL Managed Instance에 대 한 피어 링 가상 네트워크 연결로 이동한 다음 **게이트웨이 전송 허용**을 클릭 합니다.
2. SQL Managed Instance를 호스트 하는 가상 네트워크에서 **피어 링**으로 이동 하 여 VPN gateway에 대 한 피어 링 가상 네트워크 연결로 이동한 다음 **원격 게이트웨이 사용**을 클릭 합니다.

## <a name="connect-azure-app-service"></a>연결 Azure App Service 

Azure App Service에서 호스트 되는 응용 프로그램을 연결할 수도 있습니다. SQL Managed Instance은 개인 IP 주소를 통해서만 액세스할 수 있으므로 Azure App Service에서 액세스 하려면 먼저 응용 프로그램과 SQL Managed Instance 가상 네트워크 간에 연결을 설정 해야 합니다. [Azure 가상 네트워크와 앱 통합](../../app-service/web-sites-integrate-with-vnet.md)을 참조 하세요.  

문제 해결에 대해서는 [가상 네트워크 및 응용 프로그램 문제 해결](../../app-service/web-sites-integrate-with-vnet.md#troubleshooting)을 참조 하세요. 연결을 설정할 수 없는 경우 [네트워킹 구성을 동기화](azure-app-sync-network-configuration.md)해 보세요.

SQL Managed Instance에 Azure App Service를 연결 하는 특수 한 경우는 Azure App Service를 네트워크 피어 링 SQL Managed Instance 가상 네트워크에 통합 하는 경우입니다. 해당 경우에는 다음 구성을 설정해야 합니다.

- SQL Managed Instance 가상 네트워크에는 게이트웨이가 없어야 합니다.  
- SQL Managed Instance 가상 네트워크에 옵션이 설정 되어 있어야 합니다. `Use remote gateways`
- 피어 링 가상 네트워크에는 옵션이 설정 되어 있어야 합니다. `Allow gateway transit`

이 시나리오는 다음 다이어그램에서 설명합니다.

![통합 앱 피어링](./media/connect-application-instance/integrated-app-peering.png)

>[!NOTE]
>가상 네트워크 통합 기능은 Express 경로 게이트웨이가 있는 가상 네트워크와 앱을 통합 하지 않습니다. Express 경로 게이트웨이가 공존 모드로 구성 된 경우에도 가상 네트워크 통합이 작동 하지 않습니다. Express 경로 연결을 통해 리소스에 액세스 해야 하는 경우 가상 네트워크에서 실행 되는 App Service Environment를 사용할 수 있습니다.

## <a name="troubleshooting-connectivity-issues"></a>연결 문제 해결

연결 문제를 해결하려면 다음을 검토합니다.

- 동일한 가상 네트워크 내에서 다른 서브넷에 있는 Azure 가상 머신에서 SQL Managed Instance에 연결할 수 없는 경우 액세스를 차단할 수 있는 VM 서브넷에 네트워크 보안 그룹이 설정 되어 있는지 확인 합니다. 또한 Azure 경계 내에서 리디렉션을 통해 연결 하는 데 필요 하므로 11000-11999 범위의 포트 뿐만 아니라 SQL 포트 1433에 대 한 아웃 바운드 연결을 엽니다.
- 가상 네트워크와 연결 된 경로 테이블에 대해 BGP 전파가 **사용** 으로 설정 되어 있는지 확인 합니다.
- P2S VPN을 사용할 경우 Azure Portal에서 구성을 확인하여 **수신/송신** 숫자가 보이는지 확인합니다. 0이 아닌 숫자는 Azure가 온-프레미스에서 트래픽을 라우팅하는 것을 나타냅니다.

   ![수신/송신 숫자](./media/connect-application-instance/ingress-egress-numbers.png)

- 클라이언트 컴퓨터 (VPN 클라이언트를 실행 하는)에 액세스 해야 하는 모든 가상 네트워크에 대 한 경로 항목이 있는지 확인 합니다. 경로는 `%AppData%\ Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt`에 저장됩니다.

   ![route.txt](./media/connect-application-instance/route-txt.png)

   이 이미지에 표시 된 것 처럼, 관련 된 각 가상 네트워크에 대 한 두 항목과 포털에서 구성 된 VPN 끝점에 대 한 세 번째 항목이 있습니다.

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

- 가상 네트워크 피어 링을 사용 하는 경우 [게이트웨이 전송 허용 및 원격 게이트웨이 사용](#connect-from-on-premises)에 대 한 지침을 따랐는지 확인 합니다.

- 가상 네트워크 피어 링을 사용 하 여 Azure App Service 호스팅된 응용 프로그램을 연결 하 고 SQL Managed Instance 가상 네트워크에 공용 IP 주소 범위가 있는 경우 호스팅된 응용 프로그램 설정에서 아웃 바운드 트래픽을 공용 IP 네트워크로 라우팅할 수 있도록 해야 합니다. [지역 가상 네트워크 통합](../../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)의 지침을 따릅니다.

## <a name="required-versions-of-drivers-and-tools"></a>드라이버 및 도구의 필요한 버전

다음 최소 버전의 도구 및 드라이버는 SQL Managed Instance에 연결 하려는 경우에 권장 됩니다.

| 드라이버/도구 | 버전 |
| --- | --- |
|.NET Framework | 4.6.1(또는 .NET Core) |
|ODBC 드라이버| v17 |
|PHP 드라이버| 5.2.0 |
|JDBC 드라이버| 6.4.0 |
|Node.js 드라이버| 2.1.1 |
|OLEDB 드라이버| 18.0.2.0 |
|SSMS| 18.0 [이상](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) |
|[SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) 이상 |

## <a name="next-steps"></a>다음 단계

- SQL Managed Instance에 대 한 자세한 내용은 [sql Managed Instance?](sql-managed-instance-paas-overview.md)을 참조 하십시오.
- 새 관리 되는 인스턴스를 만드는 방법을 보여 주는 자습서는 [관리 되는 인스턴스 만들기](instance-create-quickstart.md)를 참조 하세요.
