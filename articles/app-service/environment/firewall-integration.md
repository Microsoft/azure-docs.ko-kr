---
title: App Service Environment 아웃바운드 트래픽 잠금 - Azure
description: Azure Firewall과 통합하여 아웃바운드 트래픽을 보호하는 방법을 설명합니다
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 6ae7037ad4cd532b6661a56e6e37a88df3eb54a2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60766533"
---
# <a name="locking-down-an-app-service-environment"></a>App Service Environment 잠금

ASE(App Service Environment)에는 제대로 작동하기 위해 액세스해야 하는 여러 가지 외부 종속성이 있습니다. ASE는 고객의 Azure VNet(Virtual Network)에 상주합니다. 고객은 ASE 종속성 트래픽을 허용해야 합니다. 이는 VNet의 모든 송신을 잠그려고 하는 고객에게는 문제가 됩니다.

ASE에는 여러 가지 인바운드 종속성이 있습니다. 인바운드 관리 트래픽은 방화벽 디바이스를 통해 보낼 수 없습니다. 이 트래픽에 대한 원본 주소는 알려져 있으며 [App Service Environment 관리 주소](https://docs.microsoft.com/azure/app-service/environment/management-addresses) 문서에 게시됩니다. 해당 정보를 사용하여 네트워크 보안 그룹 규칙을 만들어 인바운드 트래픽을 보호할 수 있습니다.

ASE 아웃바운드 종속성은 거의 전적으로 뒤에 고정 주소가 없는 FQDN을 사용하여 정의됩니다. 고정 주소가 없으면 NSG(네트워크 보안 그룹)를 사용하여 ASE의 아웃바운드 트래픽을 잠글 수 없습니다. 주소는 현재 확인에 기반한 규칙을 설정하고 NSG를 만드는 데 사용할 수 없을 만큼 자주 변경됩니다. 

아웃바운드 주소를 보호하는 솔루션은 도메인 이름에 따라 아웃바운드 트래픽을 제어할 수 있는 방화벽 디바이스를 사용하는 것입니다. Azure Firewall은 대상의 FQDN을 기반으로 아웃바운드 HTTP 및 HTTPS 트래픽을 제한할 수 있습니다.  

## <a name="configuring-azure-firewall-with-your-ase"></a>ASE를 사용하여 Azure Firewall 구성 

Azure Firewall을 사용하여 기존 ASE의 송신을 잠그는 단계는 다음과 같습니다.

1. ASE 서브넷에서 서비스 엔드포인트를 SQL, Storage 및 Event Hub에 사용하도록 설정합니다. 이렇게 하려면 네트워킹 포털 > 서브넷으로 이동하여 서비스 엔드포인트 드롭다운에서 Microsoft.EventHub, Microsoft.SQL 및 Microsoft.Storage를 선택합니다. 서비스 엔드포인트를 Azure SQL에 사용하도록 설정하면 앱에 있는 Azure SQL 종속성도 서비스 엔드포인트로 구성되어야 합니다. 

   ![서비스 엔드포인트 선택][2]
  
1. ASE가 있는 VNet에서 AzureFirewallSubnet이라는 서브넷을 만듭니다. [Azure Firewall 설명서](https://docs.microsoft.com/azure/firewall/)의 지침에 따라 Azure Firewall을 만듭니다.
1. [Azure Firewall UI] > [규칙] > [애플리케이션 규칙 컬렉션]에서 [애플리케이션 규칙 컬렉션 추가]를 선택합니다. 이름, 우선 순위를 제공하고 허용을 설정합니다. FQDN 태그 섹션에서 이름을 입력하고, 원본 주소를 *로 설정하며, App Service Environment FQDN 태그 및 Windows 업데이트를 선택합니다. 
   
   ![애플리케이션 규칙 추가][1]
   
1. [Azure Firewall UI] > [규칙] > [애플리케이션 규칙 컬렉션]에서 [네트워크 규칙 컬렉션 추가]를 선택합니다. 이름, 우선 순위를 제공하고 허용을 설정합니다. 규칙 섹션에서 이름을 입력하고, **모두**를 선택한 다음, 원본 및 대상 주소를 *로 설정하며, 포트를 123으로 설정합니다. 이 규칙은 시스템이 NTP를 사용하여 클록 동기화를 수행할 수 있게 합니다. 시스템 문제를 분류하기 위해 포트 12000과 동일한 방식으로 다른 규칙을 만듭니다.

   ![NTP 네트워크 규칙 추가][3]

1. 인터넷의 다음 홉을 사용하여 [App Service Environment 관리 주소]( https://docs.microsoft.com/azure/app-service/environment/management-addresses)의 관리 주소가 있는 경로 테이블을 만듭니다. 경로 테이블 항목은 비대칭 라우팅 문제를 방지하는 데 필요합니다. 인터넷의 다음 홉을 사용하여 IP 주소 종속성 아래에 명시된 IP 주소 종속성에 대한 경로를 추가합니다. Azure Firewall 사설 IP 주소인 다음 홉을 사용하여 0.0.0.0/0에 대한 경로 테이블에 Virtual Appliance 경로를 추가합니다 

   ![경로 테이블 만들기][4]
   
1. 만든 경로 테이블을 ASE 서브넷에 할당합니다.

#### <a name="deploying-your-ase-behind-a-firewall"></a>방화벽 뒤에 ASE 배포

방화벽 뒤에 ASE를 배치하는 단계는 ASE 서브넷을 만든 다음, 이전 단계를 수행해야 한다는 점을 제외하고는 Azure Firewall로 기존 ASE를 구성하는 것과 동일합니다. 기존 서브넷에 ASE를 만들려면 [Resource Manager 템플릿으로 ASE 만들기](https://docs.microsoft.com/azure/app-service/environment/create-from-template) 문서에 설명된 대로 Resource Manager 템플릿을 사용해야 합니다.

## <a name="application-traffic"></a>애플리케이션 트래픽 

위의 단계를 통해 ASE가 문제 없이 작동할 수 있습니다. 여전히 애플리케이션 요구 사항에 맞게 항목을 구성해야 합니다. Azure Firewall을 사용하여 구성된 ASE의 애플리케이션에는 두 가지 문제가 있습니다.  

- 애플리케이션 종속성을 Azure Firewall 또는 경로 테이블에 추가해야 합니다. 
- 비대칭 라우팅 문제를 방지하려면 애플리케이션 트래픽에 대한 경로를 만들어야 합니다.

애플리케이션에 종속성이 있으면 Azure Firewall에 추가해야 합니다. 다른 모든 항목에 대해 HTTP/HTTPS 트래픽 및 네트워크 규칙을 허용하는 애플리케이션 규칙을 만듭니다. 

애플리케이션 요청 트래픽이 제공되는 주소 범위를 알고 있는 경우 ASE 서브넷에 할당된 경로 테이블에 이를 추가할 수 있습니다. 주소 범위가 크거나 지정되지 않은 경우 Application Gateway와 같은 네트워크 어플라이언스를 사용하여 경로 테이블에 추가할 하나의 주소를 제공할 수 있습니다. ILB ASE를 사용하여 Application Gateway를 구성하는 방법에 대한 자세한 내용은 [ILB ASE와 Application Gateway 통합](https://docs.microsoft.com/azure/app-service/environment/integrate-with-application-gateway)을 참조하세요.

![Azure Firewall 연결 흐름 포함 ASE][5]

이러한 Application Gateway의 사용은 시스템을 구성하는 방법의 한 예일 뿐입니다. 이 경로를 따른 경우 ASE 서브넷 경로 테이블에 경로를 추가해야 Application Gateway로 전송된 회신 트래픽이 직접 이동하게 됩니다. 

## <a name="logging"></a>로깅 

Azure Firewall은 로그를 Azure Storage, Event Hub 또는 Azure Monitor 로그에 보낼 수 있습니다. 앱을 지원되는 모든 대상과 통합하려면 [Azure Firewall 포털] > [진단 로그]로 이동하여 원하는 대상에 대한 로그를 설정합니다. Azure Monitor 로그와 통합하면 Azure Firewall에 전송된 모든 트래픽에 대한 로깅을 볼 수 있습니다. 거부되는 트래픽을 보려면 Log Analytics 작업 영역 포털 > 로그를 열고 다음과 같은 쿼리를 입력합니다. 

    AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
 
Azure Firewall과 Azure Monitor 로그를 통합하면 모든 애플리케이션 종속성을 알지 못하는 상태에서 애플리케이션을 처음 작동할 때 매우 유용합니다. Azure Monitor 로그에 대한 자세한 내용은 [Azure Monitor에서 로그 데이터 분석](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)을 참조하세요.
 
## <a name="dependencies"></a>종속성

다음 정보는 Azure Firewall 이외의 방화벽 어플라이언스를 구성하는 경우에만 필요합니다. 

- 서비스 엔드포인트 지원 서비스는 서비스 엔드포인트로 구성되어야 합니다.
- IP 주소 종속성은 HTTP/S가 아닌 트래픽(TCP 및 UDP 모두)에 대한 것입니다.
- FQDN HTTP/HTTPS 엔드포인트는 방화벽 디바이스에 배치할 수 있습니다.
- 와일드카드 HTTP/HTTPS 엔드포인트는 몇 가지 한정자를 기반으로 하여 ASE에 따라 달라질 수 있는 종속성입니다. 
- Linux 종속성은 Linux 응용 프로그램을 ASE에 배포하는 경우에만 문제가 됩니다. Linux 응용 프로그램을 ASE에 배포하지 않는 경우 이러한 주소는 방화벽에 추가할 필요가 없습니다. 

#### <a name="service-endpoint-capable-dependencies"></a>서비스 엔드포인트 지원 종속성 

| 엔드포인트 |
|----------|
| Azure SQL |
| Azure Storage |
| Azure Event Hub |

#### <a name="ip-address-dependencies"></a>IP 주소 종속성

| 엔드포인트 | 세부 정보 |
|----------| ----- |
| \*:123 | NTP 클록 확인. 트래픽이 포트 123의 여러 엔드포인트에서 확인됩니다. |
| \*:12000 | 이 포트는 일부 시스템 모니터링에 사용됩니다. 차단된 경우 일부 문제는 분류가 어려울 수 있지만 ASE는 계속 작동합니다. |
| 40.77.24.27:80 | 모니터 및 경고 ASE 문제를 해결 하는 데 필요한 |
| 40.77.24.27:443 | 모니터 및 경고 ASE 문제를 해결 하는 데 필요한 |
| 13.90.249.229:80 | 모니터 및 경고 ASE 문제를 해결 하는 데 필요한 |
| 13.90.249.229:443 | 모니터 및 경고 ASE 문제를 해결 하는 데 필요한 |
| 104.45.230.69:80 | 모니터 및 경고 ASE 문제를 해결 하는 데 필요한 |
| 104.45.230.69:443 | 모니터 및 경고 ASE 문제를 해결 하는 데 필요한 |
| 13.82.184.151:80 | 모니터 및 경고 ASE 문제를 해결 하는 데 필요한 |
| 13.82.184.151:443 | 모니터 및 경고 ASE 문제를 해결 하는 데 필요한 |

Azure Firewall을 사용하면 FQDN 태그로 구성된 모든 항목을 자동으로 가져옵니다. 

#### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS 종속성 

| 엔드포인트 |
|----------|
|graph.windows.net:443 |
|login.live.com:443 |
|login.windows.com:443 |
|login.windows.net:443 |
|login.microsoftonline.com:443 |
|client.wns.windows.com:443 |
|definitionupdates.microsoft.com:443 |
|go.microsoft.com:80 |
|go.microsoft.com:443 |
|www.microsoft.com:80 |
|www.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|ocsp.msocsp.com:443 |
|mscrl.microsoft.com:443 |
|mscrl.microsoft.com:80 |
|crl.microsoft.com:443 |
|crl.microsoft.com:80 |
|www.thawte.com:443 |
|crl3.digicert.com:80 |
|ocsp.digicert.com:80 |
|csc3-2009-2.crl.verisign.com:80 |
|crl.verisign.com:80 |
|ocsp.verisign.com:80 |
|cacerts.digicert.com:80 |
|azperfcounters1.blob.core.windows.net:443 |
|azurewatsonanalysis-prod.core.windows.net:443 |
|global.metrics.nsatc.net:80 |
|global.metrics.nsatc.net:443 |
|az-prod.metrics.nsatc.net:443 |
|antares.metrics.nsatc.net:443 |
|azglobal-black.azglobal.metrics.nsatc.net:443 |
|azglobal-red.azglobal.metrics.nsatc.net:443 |
|antares-black.antares.metrics.nsatc.net:443 |
|antares-red.antares.metrics.nsatc.net:443 |
|maupdateaccount.blob.core.windows.net:443 |
|clientconfig.passport.net:443 |
|packages.microsoft.com:443 |
|schemas.microsoft.com:80 |
|schemas.microsoft.com:443 |
|management.core.windows.net:443 |
|management.core.windows.net:80 |
|management.azure.com:443 |
|www.msftconnecttest.com:80 |
|shavamanifestcdnprod1.azureedge.net:443 |
|validation-v2.sls.microsoft.com:443 |
|flighting.cp.wd.microsoft.com:443 |
|dmd.metaservices.microsoft.com:80 |
|admin.core.windows.net:443 |
|azureprofileruploads.blob.core.windows.net:443 |
|azureprofileruploads2.blob.core.windows.net:443 |
|azureprofileruploads3.blob.core.windows.net:443 |
|azureprofileruploads4.blob.core.windows.net:443 |
|azureprofileruploads5.blob.core.windows.net:443 |

#### <a name="wildcard-httphttps-dependencies"></a>Wildcard HTTP/HTTPS dependencies 

| 엔드포인트 |
|----------|
|gr-Prod-\*.cloudapp.net:443 |
| \*.management.azure.com:443 |
| \*.update.microsoft.com:443 |
| \*.windowsupdate.microsoft.com:443 |

#### <a name="linux-dependencies"></a>Linux 종속성 

| 엔드포인트 |
|----------|
|wawsinfraprodbay063.blob.core.windows.net:443 |
|registry-1.docker.io:443 |
|auth.docker.io:443 |
|production.cloudflare.docker.com:443 |
|download.docker.com:443 |
|us.archive.ubuntu.com:80 |
|download.mono-project.com:80 |
|packages.treasuredata.com:80|
|security.ubuntu.com:80 |

<!--Image references-->
[1]: ./media/firewall-integration/firewall-apprule.png
[2]: ./media/firewall-integration/firewall-serviceendpoints.png
[3]: ./media/firewall-integration/firewall-ntprule.png
[4]: ./media/firewall-integration/firewall-routetable.png
[5]: ./media/firewall-integration/firewall-topology.png
