---
title: Azure ExpressRoute의 네트워크 구성 세부 정보 - App Service
description: ExpressRoute 회로에 연결된 가상 네트워크에 있는 PowerApps용 App Service Environment의 네트워크 구성 세부 정보입니다.
services: app-service
documentationcenter: ''
author: stefsch
manager: nirma
editor: ''
ms.assetid: 34b49178-2595-4d32-9b41-110c96dde6bf
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: e0fa87facec73efdfff1a9908dcba92838215425
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130673"
---
# <a name="network-configuration-details-for-app-service-environment-for-powerapps-with-azure-expressroute"></a>Azure ExpressRoute를 사용하는 PowerApps용 App Service Environment의 네트워크 구성 세부 정보

고객은 [Azure ExpressRoute][ExpressRoute] 회로를 가상 네트워크 인프라에 연결하여 온-프레미스 네트워크를 Azure로 확장합니다. App Service Environment는 [가상 네트워크][virtualnetwork] 인프라의 서브넷에서 만들어집니다. App Service Environment에서 실행되는 앱은 ExpressRoute 연결을 통해서만 액세스할 수 있는 백 엔드 리소스의 보안 연결을 설정합니다.  

다음 시나리오에서 App Service Environment를 만들 수 있습니다.
- Azure Resource Manager 가상 네트워크.
- 클래식 배포 모델 가상 네트워크.
- 공용 주소 범위 또는 RFC1918 주소 공간(즉, 개인 주소)을 사용하는 가상 네트워크. 

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="required-network-connectivity"></a>필요한 네트워크 연결

App Service Environment에는 ExpressRoute에 연결된 가상 네트워크에서 초기에 충족되지 않을 수 있는 네트워크 연결 요구 사항이 있습니다.

App Service Environment에서는 다음 네트워크 연결 설정이 제대로 작동해야 합니다.

* 포트 80 및 포트 443에서 전 세계 Azure Storage 엔드포인트에 아웃바운드 네트워크 연결. 이러한 엔드포인트는 App Service Environment와 동일한 Azure 지역과 다른 Azure 지역에도 있습니다. 다음 DNS 도메인에서 Azure Storage 엔드포인트는 다음을 확인합니다. table.core.windows.net, blob.core.windows.net, queue.core.windows.net 및 file.core.windows.net.  

* 포트 445에서 Azure 파일 서비스에 대한 아웃바운드 연결.

* App Service Environment와 동일한 지역에 있는 Azure SQL Database 엔드포인트에 아웃바운드 네트워크 연결. SQL Database 엔드포인트는 포트 1433, 11000~11999 및 14000~14999에 대한 개방형 액세스가 필요한 database.windows.net 도메인에서 확인됩니다. SQL Database V12 포트 사용에 대한 자세한 내용은 [ADO.NET 4.5에 대한 1433 이외 포트](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md)를 참조하세요.

* Azure 관리 평면 엔드포인트에 아웃바운드 네트워크 연결(Azure 클래식 배포 모델 및 Azure Resource Manager 엔드포인트 모두). 이러한 엔드포인트 연결에는 management.core.windows.net 및 management.azure.com 도메인이 포함됩니다. 

* ocsp.msocsp.com, mscrl.microsoft.com 및 crl.microsoft.com 도메인에 아웃바운드 네트워크 연결. SSL 기능을 지원하려면 이러한 도메인에 연결해야 합니다.

* 가상 네트워크의 DNS 구성은 이 문서에 언급된 모든 엔드포인트 및 도메인을 확인할 수 있어야 합니다. 엔드포인트를 확인할 수 없으면 App Service Environment가 만들어지지 않습니다. 기존의 모든 App Service Environment가 비정상으로 표시됩니다.

* DNS 서버와의 통신을 위해서는 포트 53에서 아웃바운드 액세스가 필요합니다.

* 사용자 지정 DNS 서버가 VPN 게이트웨이의 반대쪽 끝에 있는 경우 App Service Environment를 포함하는 서브넷에서 DNS 서버에 연결할 수 있어야 합니다. 

* 아웃바운드 네트워크 경로는 내부 회사 프록시를 통과할 수 없고 온-프레미스에 강제로 터널링될 수 없습니다. 이러한 작업은 App Service Environment에서 아웃바운드 네트워크 트래픽의 유효 NAT 주소를 변경합니다. App Service Environment 아웃바운드 네트워크 트래픽의 NAT 주소를 변경하면 많은 엔드포인트에 연결 오류가 발생합니다. App Service Environment가 만들어지지 않습니다. 기존의 모든 App Service Environment가 비정상으로 표시됩니다.

* App Service Environment에 필요한 포트에 대한 인바운드 네트워크 액세스가 허용되어야 합니다. 자세한 내용은 [App Service Environment에 대한 인바운드 트래픽을 제어하는 방법][requiredports]을 참조하세요.

DNS 요구 사항을 충족하려면 가상 네트워크에 유효한 DNS 인프라를 구성하고 유지 관리해야 합니다. App Service Environment를 만든 후에 DNS 구성이 변경되면 개발자는 App Service Environment를 강제로 적용하여 새 DNS 구성을 선택할 수 있습니다. [Azure Portal][NewPortal]의 App Service Environment 관리에서 **다시 시작** 아이콘을 사용하여 롤링 환경 다시 부팅을 트리거할 수 있습니다. 다시 부팅되면 환경이 새 DNS 구성을 선택합니다.

인바운드 네트워크 액세스 요구 사항을 충족하려면 App Service Environment 서브넷에서 [NSG(네트워크 보안 그룹)][NetworkSecurityGroups]를 구성합니다. NSG는 [App Service Environment에 대한 인바운드 트래픽을 제어][requiredports]하는 데 필요한 액세스를 허용합니다.

## <a name="outbound-network-connectivity"></a>아웃바운드 네트워크 연결

기본적으로, 새로 생성된 ExpressRoute 회로는 아웃바운드 인터넷 연결을 허용하는 기본 경로를 보급합니다. App Service Environment는 이 구성을 사용하여 다른 Azure 엔드포인트에 연결할 수 있습니다.

일반적인 고객 구성은 아웃바운드 인터넷 트래픽을 온-프레미스에서 강제로 흐르게 하는 고유한 기본 경로(0.0.0.0/0)를 정의하는 것입니다. 이 트래픽 흐름은 App Service Environment를 계속 중단합니다. 아웃바운드 트래픽은 온-프레미스에서 차단되거나 다양한 Azure 엔드포인트에서 더 이상 작동하지 않는 인식할 수 없는 주소 세트로 NAT됩니다.

해결 방법은 하나 이상의 사용자 정의 경로(UDR)를 App Service Environment를 포함하는 서브넷에 정의하는 것입니다. UDR이 정의한 서브넷 특정 경로는 기본 경로 대신 적용됩니다.

가능한 경우 다음 구성을 사용합니다.

* ExpressRoute 구성은 0.0.0.0/0을 보급합니다. 기본적으로 구성은 온-프레미스의 모든 아웃바운드 트래픽을 강제로 터널링합니다.
* App Service Environment가 포함된 서브넷에 적용된 UDR는 인터넷의 다음 홉 유형을 사용하여 0.0.0.0/0을 정의합니다. 이 구성 예제는 이 문서의 뒷부분에서 설명합니다.

이 구성의 결합 효과는 서브넷 수준 UDR이 ExpressRoute 강제 터널링보다 우선 적용된다는 것입니다. App Service Environment의 아웃바운드 인터넷 액세스가 보장됩니다.

> [!IMPORTANT]
> UDR에 정의된 경로는 ExpressRoute 구성을 통해 보급된 경로보다 우선 적용되도록 구체적이어야 합니다. 다음 섹션에 설명된 예제에서는 광범위한 0.0.0.0/0 주소 범위를 사용합니다. 이 범위는 더 구체적인 주소 범위를 사용하는 경로 보급으로 의도치 않게 재정의될 수 있습니다.
> 
> 공용 피어링 경로에서 개인 피어링 경로로 경로의 교차 보급을 수행하는 ExpressRoute 구성에서는 App Service Environment가 지원되지 않습니다. 구성된 공용 피어링이 있는 ExpressRoute 구성은 다양한 Microsoft Azure IP 주소 범위 세트에 대해 Microsoft에서 경로 보급을 받습니다. 개인 피어링 경로에서 주소 범위를 교차 보급하는 경우 App Service Environment 서브넷의 모든 아웃바운드 네트워크 패킷이 고객의 온-프레미스 네트워크 인프라로 강제 터널링됩니다. 이 네트워크 흐름은 현재 App Service Environment에서 지원되지 않습니다. 이 문제를 해결하려면 공용 피어링 경로에서 개인 피어링 경로로 교차 보급 경로를 중지합니다.
> 
> 

사용자 정의 경로에 대한 배경 정보는 [가상 네트워크 트래픽 라우팅][UDROverview]을 참조하세요.  

사용자 정의 경로를 만들고 구성하는 방법을 알아보려면 [PowerShell을 사용하여 경로 테이블로 네트워크 트래픽 라우팅][UDRHowTo]을 참조하세요.

## <a name="udr-configuration"></a>UDR 구성

이 섹션은 App Service Environment에 대한 예제 UDR 구성을 보여 줍니다.

### <a name="prerequisites"></a>필수 조건

* [Azure 다운로드 페이지][AzureDownloads]에서 Azure PowerShell을 설치합니다. 2015년 6월 이후 날짜의 다운로드를 선택합니다. **명령줄 도구** > **Windows PowerShell**에서 **설치**를 선택하여 최신 PowerShell cmdlet을 설치합니다.

* App Service Environment에서만 사용할 고유 서브넷을 만듭니다. 고유 서브넷을 사용하면 서브넷에 적용된 UDR이 App Service Environment의 아웃바운드 트래픽만 엽니다.

> [!IMPORTANT]
> App Service Environment는 구성 단계를 완료한 후에 배포해야 합니다. 이러한 단계에서 App Service Environment를 배포하기 전에 네트워크 연결을 사용할 수 있는지 확인합니다.

### <a name="step-1-create-a-route-table"></a>1단계: 경로 테이블 만들기

다음 코드 조각에 표시된 대로 미국 서부 Azure 지역에서 **DirectInternetRouteTable**이라는 경로 테이블을 만듭니다.

`New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest`

### <a name="step-2-create-routes-in-the-table"></a>2단계: 테이블에서 경로 만들기

경로 테이블에 경로를 추가하여 아웃바운드 인터넷 액세스를 사용하도록 설정합니다.  

인터넷에 대한 아웃바운드 액세스를 구성합니다. 이 코드 조각에 표시된 대로 0.0.0.0/0에 대한 경로를 정의합니다.

`Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet`

0.0.0.0/0은 광범위한 주소 범위입니다. 이 범위는 ExpressRoute에서 보급하는 더 구체적인 주소 범위로 재정의됩니다. 0.0.0.0/0 경로가 있는 UDR은 0.0.0.0/0만 보급하는 ExpressRoute 구성과 함께 사용해야 합니다. 

대신 Azure에서 사용 중인 포괄적인 최신 CIDR 범위 목록을 다운로드합니다. 모든 Azure IP 주소 범위에 대한 XML 파일은 [Microsoft 다운로드 센터][DownloadCenterAddressRanges]에서 사용할 수 있습니다.  

> [!NOTE]
>
> Azure IP 주소 범위는 시간이 지나면서 변경됩니다. 사용자 정의 경로는 동기화를 유지하기 위해 주기적인 수동 업데이트가 필요합니다.
>
> 단일 UDR의 기본 상한은 100개 경로입니다. 100개 경로 한도 내에 맞게 Azure IP 주소 범위를 “요약”해야 합니다. UDR 정의 경로는 ExpressRoute 연결에서 보급하는 경로보다 더 구체적이어야 합니다.
> 

### <a name="step-3-associate-the-table-to-the-subnet"></a>3단계: 서브넷에 테이블 연결

App Service Environment를 배포하려는 경우 서브넷에 경로 테이블을 연결합니다. 이 명령은 App Service Environment를 포함하는 **ASESubnet** 서브넷에 **DirectInternetRouteTable** 테이블을 연결합니다.

`Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'`

### <a name="step-4-test-and-confirm-the-route"></a>4단계: 경로 테스트 및 확인

경로 테이블이 서브넷에 바인딩된 후에 경로를 테스트하고 확인합니다.

서브넷에 가상 머신을 배포하고 다음 조건을 확인합니다.

* 이 문서에 설명된 Azure 및 Azure 이외 엔드포인트에 대한 아웃바운드 트래픽은 ExpressRoute 회로로 이동하지 **않습니다**. 서브넷의 아웃바운드 트래픽이 온-프레미스에서 강제로 터널링되는 경우 항상 App Service Environment 만들기에 실패합니다.
* 이 문서에 설명된 엔드포인트에 대한 DNS 조회가 모두 제대로 확인됩니다. 

구성 단계를 완료하고 경로를 확인한 후 가상 머신을 삭제합니다. App Service Environment가 생성될 때 서브넷은 “비어” 있어야 합니다.

이제 App Service Environment를 배포할 준비가 되었습니다.

## <a name="next-steps"></a>다음 단계

PowerApps용 App Service Environment를 시작하려면 [App Service Environment 소개][IntroToAppServiceEnvironment]를 참조하세요.

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/services/virtual-network/ 
[ExpressRoute]: https://azure.microsoft.com/services/expressroute/ 
[requiredports]: app-service-app-service-environment-control-inbound-traffic.md 
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/ 
[UDROverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/ 
<!-- Old link -- [UDRHowTo]: https://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/ -->

[UDRHowTo]: https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell 
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md 
[AzureDownloads]: https://azure.microsoft.com/downloads/ 
[DownloadCenterAddressRanges]: https://www.microsoft.com/download/details.aspx?id=41653 
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/ 
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md 
[NewPortal]:  https://portal.azure.com 


<!-- IMAGES -->
