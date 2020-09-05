---
title: Azure Private Link
description: 프라이빗 엔드포인트 기능에 대한 개요입니다.
author: rohitnayakmsft
ms.author: rohitna
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.service: sql-database
ms.topic: overview
ms.custom: sqldbrb=1
ms.reviewer: vanto
ms.date: 03/09/2020
ms.openlocfilehash: 5fcc50670973c361d830e4e6bbf2ce26101b8582
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88213379"
---
# <a name="azure-private-link-for-azure-sql-database-and-azure-synapse-analytics"></a>Azure SQL Database 및 Azure Synapse Analytics에 대한 Azure Private Link
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Private Link를 사용하면 **프라이빗 엔드포인트**를 통해 Azure의 다양한 PaaS 서비스에 연결할 수 있습니다. Private Link 기능을 지원하는 PaaS 서비스 목록을 보려면 [Private Link 설명서](../../private-link/index.yml) 페이지로 이동하세요. 프라이빗 엔드포인트는 특정 [VNet](../../virtual-network/virtual-networks-overview.md) 및 서브넷 내의 개인 IP 주소입니다.

> [!IMPORTANT]
> 이 문서는 Azure SQL Database 및 Azure Synapse Analytics(이전의 Azure SQL Data Warehouse) 모두에 적용됩니다. 편의상 '데이터베이스'라는 용어는 Azure SQL Database 및 Azure Synapse Analytics의 데이터베이스를 모두 나타냅니다. 마찬가지로 '서버'에 대한 모든 참조는 Azure SQL Database 및 Azure Synapse Analytics를 호스트하는 [논리 SQL 서버](logical-servers.md)를 참조하는 것입니다. 이 문서는 **Azure SQL Managed Instance**에 적용되지 *않습니다*.

## <a name="data-exfiltration-prevention"></a>데이터 반출 방지

Azure SQL Database의 데이터 반출은 데이터베이스 관리자와 같은 권한 있는 사용자가 한 시스템에서 데이터를 추출하여 조직 외부의 다른 위치 또는 시스템으로 이동할 수 있는 경우입니다. 예를 들어 사용자는 데이터를 타사 소유의 스토리지 계정으로 이동합니다.

SQL Database의 데이터베이스에 연결하는 Azure 가상 머신 내에서 SSMS(SQL Server Management Studio)를 실행하는 사용자가 있는 시나리오를 고려해 보세요. 이 데이터베이스는 미국 서부 데이터 센터에 있습니다. 아래 예에서는 네트워크 액세스 제어를 사용하여 SQL Database에서 퍼블릭 엔드포인트를 통한 액세스를 제한하는 방법을 보여 줍니다.

1. [Azure 서비스 허용]을 **끄기**로 설정하여 퍼블릭 엔드포인트를 통해 SQL Database로의 모든 Azure 서비스 트래픽을 사용하지 않도록 설정합니다. 서버 및 데이터베이스 수준 방화벽 규칙에서 IP 주소가 허용되지 않는지 확인합니다. 자세한 내용은 [Azure SQL Database 및 Azure Synapse Analytics 네트워크 액세스 제어](network-access-controls-overview.md)를 참조하세요.
1. VM의 개인 IP 주소를 사용하는 SQL Database의 데이터베이스로의 트래픽만 허용합니다. 자세한 내용은 [서비스 엔드포인트](vnet-service-endpoint-rule-overview.md) 및 [가상 네트워크 방화벽 규칙](firewall-configure.md) 문서를 참조하세요.
1. Azure VM에서 다음과 같이 [NSG(네트워크 보안 그룹)](../../virtual-network/manage-network-security-group.md) 및 서비스 태그를 사용하여 나가는 연결의 범위를 좁힙니다.
    - 서비스 태그 = SQL.WestUs에 대한 트래픽을 허용하는 NSG 규칙을 지정합니다. 미국 서부에서는 SQL Database에만 연결할 수 있습니다.
    - 서비스 태그 = SQL에 대한 트래픽을 거부하는 NSG 규칙(**우선 순위가 높음**)을 지정합니다. 모든 지역에서 SQL Database에 대한 연결을 거부합니다.

이 설정이 완료되면 Azure VM에서 미국 서부 지역의 SQL Database의 데이터베이스에만 연결할 수 있습니다. 그러나 연결은 단일 SQL Database의 단일 데이터베이스로 제한되지 않습니다. VM은 구독에 속하지 않은 데이터베이스를 포함하여 미국 서부 지역의 모든 데이터베이스에 계속 연결할 수 있습니다. 위의 시나리오에서 데이터 반출의 범위를 특정 지역으로 좁혔지만 완전히 제거하지는 않았습니다.

Private Link를 사용하면 이제 고객이 NSG와 같은 네트워크 액세스 제어를 설정하여 프라이빗 엔드포인트에 대한 액세스를 제한할 수 있습니다. 그러면 개별 Azure PaaS 리소스가 특정 프라이빗 엔드포인트에 매핑됩니다. 악의적인 참가자는 매핑된 PaaS 리소스(예: SQL Database의 데이터베이스)에만 액세스할 수 있으며 다른 리소스에는 액세스할 수 없습니다. 

## <a name="on-premises-connectivity-over-private-peering"></a>프라이빗 피어링을 통한 온-프레미스 연결

고객이 온-프레미스 머신에서 퍼블릭 엔드포인트에 연결하는 경우 [서버 수준 방화벽 규칙](firewall-create-server-level-portal-quickstart.md)을 사용하여 IP 주소를 IP 기반 방화벽에 추가해야 합니다. 이 모델은 개발 또는 테스트 워크로드를 위해 개별 머신에 액세스할 수 있도록 하는 데 효과적이지만 프로덕션 환경에서는 관리하기가 어렵습니다.

Private Link를 사용하면 고객이 [ExpressRoute](../../expressroute/expressroute-introduction.md), 프라이빗 피어링 또는 VPN 터널링을 사용하여 프라이빗 엔드포인트에 대한 프레미스 간 액세스를 사용하도록 설정할 수 있습니다. 그런 다음, 고객이 퍼블릭 엔드포인트를 통한 모든 액세스를 사용하지 않도록 설정하고, IP 기반 방화벽을 사용하여 IP 주소를 허용하지 않을 수 있습니다.

## <a name="how-to-set-up-private-link-for-azure-sql-database"></a>Azure SQL Database용 Private Link를 설정하는 방법 

### <a name="creation-process"></a>만들기 프로세스
프라이빗 엔드포인트는 Azure Portal, PowerShell 또는 Azure CLI를 사용하여 만들 수 있습니다.
- [포털](../../private-link/create-private-endpoint-portal.md)
- [PowerShell](../../private-link/create-private-endpoint-powershell.md)
- [CLI](../../private-link/create-private-endpoint-cli.md)

### <a name="approval-process"></a>승인 프로세스
네트워크 관리자가 PE(프라이빗 엔드포인트)를 만들면 SQL 관리자가 SQL Database에 대한 PEC(프라이빗 엔드포인트 연결)를 관리할 수 있습니다.

1. 아래 스크린샷에 표시된 단계에 따라 Azure Portal의 서버 리소스로 이동합니다.

    - (1) 왼쪽 창에서 프라이빗 엔드포인트 연결 선택
    - (2) 모든 PEC(프라이빗 엔드포인트 연결)의 목록 표시
    - (3) 해당 PE(프라이빗 엔드포인트) 만들어짐 ![모든 PEC의 스크린샷][3]

1. 목록에서 개별 PEC를 선택합니다.
![선택한 PEC의 스크린샷][6]

1. SQL 관리자가 PEC를 승인 또는 거부하도록 선택하고 필요에 따라 짧은 텍스트 응답을 추가할 수 있습니다.
![PEC 승인의 스크린샷][4]

1. 승인되거나 거부되면 목록에 응답 텍스트와 함께 적절한 상태가 반영됩니다.
![승인 후 모든 PEC의 스크린샷][5]

## <a name="use-cases-of-private-link-for-azure-sql-database"></a>Azure SQL Database용 Private Link 사용 사례 

클라이언트는 동일한 가상 네트워크에서 프라이빗 엔드포인트에 연결할 수 있고, 동일한 지역의 가상 네트워크를 피어링 하거나, 가상 네트워크를 통해 지역간 가상 네트워크 연결을 통해 연결할 수 있습니다. 또한 클라이언트는 ExpressRoute, 프라이빗 피어링 또는 VPN 터널링을 사용하여 온-프레미스에서 연결할 수 있습니다. 다음은 일반적인 사용 사례를 보여 주는 간소화된 다이어그램입니다.

 ![연결 옵션 다이어그램][1]

## <a name="test-connectivity-to-sql-database-from-an-azure-vm-in-same-virtual-network"></a>동일한 가상 네트워크의 Azure VM에서 SQL Database로의 연결 테스트

이 시나리오에서는 Windows Server 2016을 실행하는 Azure VM(Virtual Machine)을 만들었다고 가정합니다. 

1. [원격 데스크톱(RDP) 세션을 시작하고, 가상 머신에 연결합니다](../../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine). 
1. 그러면 다음 도구를 사용하여 VM에서 프라이빗 엔드포인트를 통해 SQL Database에 연결하는지 확인하기 위해 몇 가지 기본적인 연결 확인을 수행할 수 있습니다.
    1. 텔넷
    1. Psping
    1. Nmap
    1. SSMS(SQL Server Management Studio)

### <a name="check-connectivity-using-telnet"></a>텔넷을 사용하여 연결 확인

[텔넷 클라이언트](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754293%28v%3dws.10%29)는 연결을 테스트하는 데 사용할 수 있는 Windows 기능입니다. Windows OS 버전에 따라 이 기능을 명시적으로 사용하도록 설정해야 할 수도 있습니다. 

텔넷을 설치한 후 명령 프롬프트 창을 엽니다. 텔넷 명령을 실행하고, SQL Database의 데이터베이스 IP 주소와 프라이빗 엔드포인트를 지정합니다.

```
>telnet 10.1.1.5 1433
```

텔넷이 성공적으로 연결되면 아래 이미지와 같은 빈 화면이 명령 창에 표시됩니다.

 ![텔넷 다이어그램][2]

### <a name="check-connectivity-using-psping"></a>Psping을 사용하여 연결 확인

[Psping](/sysinternals/downloads/psping)은 다음과 같이 사용하여 PEC(프라이빗 엔드포인트 연결)에서 1433 포트의 연결을 수신 대기하는지 확인할 수 있습니다.

논리 SQL 서버 및 포트 1433에 대한 FQDN을 제공하여 psping을 다음과 같이 실행합니다.

```
>psping.exe mysqldbsrvr.database.windows.net:1433

PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
Copyright (C) 2012-2016 Mark Russinovich
Sysinternals - www.sysinternals.com

TCP connect to 10.6.1.4:1433:
5 iterations (warmup 1) ping test:
Connecting to 10.6.1.4:1433 (warmup): from 10.6.0.4:49953: 2.83ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49954: 1.26ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49955: 1.98ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49956: 1.43ms
Connecting to 10.6.1.4:1433: from 10.6.0.4:49958: 2.28ms
```

출력에서 Psping을 통해 PEC와 연결된 개인 IP 주소를 ping할 수 있음을 보여 줍니다.

### <a name="check-connectivity-using-nmap"></a>Nmap을 사용하여 연결 확인

Nmap(네트워크 매퍼)은 네트워크 검색 및 보안 감사에 사용되는 무료 오픈 소스 도구입니다. 자세한 내용과 다운로드 링크는 https://nmap.org 를 방문하세요. 이 도구를 사용하여 프라이빗 엔드포인트에서 1433 포트의 연결을 수신 대기하고 있는지 확인할 수 있습니다.

프라이빗 엔드포인트를 호스팅하는 서브넷의 주소 범위를 제공하여 Nmap을 다음과 같이 실행합니다.

```
>nmap -n -sP 10.1.1.0/24
...
...
Nmap scan report for 10.1.1.5
Host is up (0.00s latency).
Nmap done: 256 IP addresses (1 host up) scanned in 207.00 seconds
```

결과에서 프라이빗 엔드포인트의 IP 주소에 해당하는 하나의 IP 주소가 사용되고 있음을 보여 줍니다.

### <a name="check-connectivity-using-sql-server-management-studio-ssms"></a>SSMS(SQL Server Management Studio)를 사용하여 연결 확인
> [!NOTE]
> 클라이언트의 연결 문자열에 있는 서버의 **FQDN(정규화된 도메인 이름)** 을 사용합니다(`<server>.database.windows.net`). IP 주소에 직접 또는 개인 링크 FQDN(`<server>.privatelink.database.windows.net`)을 사용하여 수행된 모든 로그인 시도가 실패합니다. 프라이빗 엔드포인트는 트래픽을 지역의 SQL 게이트웨이로 라우팅하고 로그인이 성공하려면 올바른 FQDN을 지정해야 하기 때문에 이 동작은 의도된 것입니다.

[SSMS를 사용하여 SQL Database에 연결](connect-query-ssms.md)하려면 여기의 단계를 따르세요. SSMS를 사용하여 SQL Database에 연결되면 다음 쿼리를 실행하여 Azure VM의 개인 IP 주소에서 연결하고 있는지 확인합니다.

````
select client_net_address from sys.dm_exec_connections 
where session_id=@@SPID
````

## <a name="limitations"></a>제한 사항 
프라이빗 엔드포인트에 대한 연결은 **프록시**만 [연결 정책](connectivity-architecture.md#connection-policy)으로 지원합니다.


## <a name="connecting-from-an-azure-vm-in-peered-virtual-network"></a>피어링된 가상 네트워크의 Azure VM에서 연결 

피어링된 가상 네트워크의 Azure VM에서 SQL Database로의 연결을 설정하도록 [가상 네트워크 피어링](../../virtual-network/tutorial-connect-virtual-networks-powershell.md)을 구성합니다.

## <a name="connecting-from-an-azure-vm-in-virtual-network-to-virtual-network-environment"></a>가상 네트워크의 Azure VM에서 가상 네트워크 환경으로 연결

[가상 네트워크를 가상 네트워크 VPN 게이트웨이 연결](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)을 구성하여 다른 지역 또는 구독의 Azure VM에서 SQL Database 데이터베이스에 대한 연결을 설정합니다.

## <a name="connecting-from-an-on-premises-environment-over-vpn"></a>VPN을 통해 온-프레미스 환경에서 연결

온-프레미스 환경에서 SQL Database 데이터베이스로의 연결을 설정하려면 다음 옵션 중 하나를 선택하고 구현합니다.
- [지점 및 사이트 간 연결](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
- [사이트 간 VPN 연결](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
- [ExpressRoute 회로](../../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)


## <a name="connecting-from-azure-synapse-analytics-to-azure-storage-using-polybase-and-the-copy-statement"></a>Polybase 및 COPY 문을 사용하여 Azure Synapse Analytics에서 Azure Storage로 연결

PolyBase 및 COPY 문은 일반적으로 Azure Storage 계정에서 Azure Synapse Analytics로 데이터를 로드하는 데 사용됩니다. 데이터를 로드하는 Azure Storage 계정에서 프라이빗 엔드포인트, 서비스 엔드포인트 또는 IP 기반 방화벽을 통해 가상 네트워크 서브넷 세트에만 액세스하도록 제한하는 경우 PolyBase 및 COPY 문에서 계정으로의 연결이 끊어집니다. 가상 네트워크로 보안이 유지되는 Azure Storage에 연결하는 Azure Synapse Analytics를 통해 가져오기 및 내보내기 시나리오를 사용하도록 설정하는 경우 [여기](vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)에서 제공하는 단계를 따르세요. 

## <a name="next-steps"></a>다음 단계

- Azure SQL Database 보안 개요는 [데이터베이스 보안 설정](security-overview.md)을 참조하세요.
- Azure SQL Database 연결에 대한 개요는 [Azure SQL 연결 아키텍처](connectivity-architecture.md)를 참조하세요.

<!--Image references-->
[1]: media/quickstart-create-single-database/pe-connect-overview.png
[2]: media/quickstart-create-single-database/telnet-result.png
[3]: media/quickstart-create-single-database/pec-list-before.png
[4]: media/quickstart-create-single-database/pec-approve.png
[5]: media/quickstart-create-single-database/pec-list-after.png
[6]: media/quickstart-create-single-database/pec-select.png
