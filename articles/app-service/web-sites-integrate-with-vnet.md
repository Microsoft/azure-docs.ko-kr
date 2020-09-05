---
title: Azure Virtual Network에 앱 통합
description: Azure 가상 네트워크로 Azure App Service와 앱 통합
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 08/05/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 399689f3f7d07a6e77128037be6b7439e7bf5184
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88960023"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Azure 가상 네트워크에 앱 통합

이 문서에서는 Azure App Service VNet 통합 기능 및 앱으로 [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) 설정 방법을 설명합니다. [Azure VNet(Virtual Network)][VNETOverview]으로 다양한 Azure 리소스를 인터넷이 아닌 라우팅 가능한 네트워크에 배치할 수 있습니다. VNet 통합 기능을 사용 하면 앱에서 VNet을 통해 또는의 리소스에 액세스할 수 있습니다. VNet 통합을 사용 하면 앱을 개인적으로 액세스할 수 없습니다.

Azure App Service VNet 통합 기능에는 두 가지 변형이 있습니다.

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>VNet 통합 사용

1. App Service 포털에서 **네트워킹**으로 이동합니다. **VNet 통합**에서 **구성하려면 클릭**을 선택하세요.

1. **VNet 추가**를 선택합니다.

   ![VNET 통합 선택][1]

1. 드롭다운 목록에는 동일한 지역에서 사용자가 구독하는 모든 Azure Resource Manager 가상 네트워크가 포함됩니다. 그 아래에는 다른 모든 지역의 Resource Manager 가상 네트워크가 나열됩니다. 통합하려는 VNet를 선택하세요.

   ![VNet을 선택][2]

   * VNet이 동일한 지역에 있는 경우 서브넷을 새로 만들거나 기존의 빈 서브넷을 선택하세요.
   * 다른 지역에서 VNet을 선택하려면 사이트 간 사용이 프로비전된 VNet 게이트웨이가 필요합니다.
   * 클래식 VNet과 통합하려면 **Virtual Network** 드롭다운 목록을 선택하는 대신 **여기를 클릭 하여 클래식 VNet**을 선택하세요. 원하는 클래식 가상 네트워크를 선택하세요. 대상 VNet에는 지점 및 사이트 간 사용으로 프로비전된 Virtual Network 게이트웨이가 이미 있어야 합니다.

    ![클래식 VNet을 선택][3]

통합하는 동안에 앱이 다시 시작됩니다. 통합이 완료되면 통합된 VNet에 대한 세부 정보가 표시됩니다.

## <a name="regional-vnet-integration"></a>지역 VNet 통합

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>지역 VNet 통합 작동 원리

App Service의 앱은 작업자 역할에서 호스팅 됩니다. 기본 및 더 높은 가격 책정 요금제는 동일한 작업자에서 실행되는 다른 고객의 작업이 없는 전용 호스팅 계획입니다. 지역 VNet 통합은 위임된 서브넷의 주소를 사용하여 가상 인터페이스를 탑재하는 방식으로 작동합니다. 보낸 사람 주소는 VNet에 있기 때문에 VNet의 VM과 마찬가지로 VNet을 통해 액세스하거나, 모든 항목에 액세스할 수 있습니다. 네트워킹 구현은 VNet에서 VM 실행하기와 다릅니다. 이러한 이유로 일부 네트워킹 기능은 현재 이 기능을 사용할 수 없습니다.

![지역 VNet 통합 작동 원리][5]

지역 VNet 통합을 사용하면 앱에서 평소와 동일한 채널을 통해 인터넷으로 아웃바운드 호출을 수행합니다. 앱 속성 포털에 나열된 아웃바운드 주소는 앱에서 계속 사용하는 주소입니다. 앱 변경 내용은 서비스 엔드포인트 보안 서비스 호출 또는 RFC 1918 주소의 이동입니다. WEBSITE_VNET_ROUTE_ALL을 1로 설정 하면 모든 아웃바운드 트래픽이 VNet으로 전송될 수 있습니다.

이 기능은 작업자당 가상 인터페이스 하나만 지원합니다. 작업자당 가상 인터페이스 하나는 App Service 계획당 한 지역 내 VNet 통합을 의미합니다. 동일한 App Service 계획의 모든 앱은 동일한 VNet 통합을 사용할 수 있습니다. 추가로 VNet에 연결할 앱이 필요한 경우 다른 App Service 계획을 만들어야 합니다. 여기에 사용되는 가상 인터페이스는 고객이 직접 액세스할 수 있는 리소스가 아닙니다.

이 기술이 작동하는 방법의 특성상 VNet 통합에 사용되는 트래픽은 Azure Network Watcher 또는 NSG 플로 로그에 표시되지 않습니다.

## <a name="gateway-required-vnet-integration"></a>필수 게이트웨이 VNet 통합

필수 게이트웨이 VNet 통합은 다른 지역의 VNet 또는 클래식 가상 네트워크 관련 연결을 지원합니다. 필수 게이트웨이 VNet 통합

* 앱은 한 번에 1개의 VNet에만 연결 가능합니다.
* VNet App Service 계획에 최대 5개까지 통합 가능합니다.
* App Service 계획에서 사용할 수 있는 총 개수에 영향을 주지 않고 App Service 계획의 여러 앱에서 동일한 VNet을 사용할 수 있습니다. 동일한 App Service 계획에서 동일한 VNet을 사용하는 앱이 6개 있는 경우 사용되는 VNet은 하나로 계산됩니다.
* 게이트웨이의 SLA에 따라 99.9% SLA를 지원합니다.
* 앱에서 VNet이 구성된 DNS를 사용할 수 있도록 합니다.
* 앱에 연결하려면 먼저 SSTP 지점 및 사이트 간 VPN이 구성된 Virtual Network 경로 기반 게이트웨이가 필요합니다.

필수 게이트웨이 VNet 통합을 사용할 수 없습니다.

* Azure ExpressRoute와 연결된 VNet 사용.
* Linux 앱에서
* 서비스 엔드포인트의 보안 리소스에 액세스합니다.
* ExpressRoute와 지점 및 사이트 간 또는 사이트 간 VPN을 모두 지원하는 동시 존재 게이트웨이.

### <a name="set-up-a-gateway-in-your-azure-virtual-network"></a>Azure 가상 네트워크에서 게이트웨이 설정 ###

게이트웨이를 만들려면,

1. VNet에 [게이트웨이 서브넷을 만듭니다][creategatewaysubnet].  

1. [VPN 게이트웨이를 만듭니다][creategateway]. 경로 기반 VPN 종류를 선택합니다.

1. [지점 및 사이트 간 주소를 설정합니다][setp2saddresses]. 게이트웨이가 기본 SKU에 없으면 지점 및 사이트 간 구성에서 IKEV2를 사용하지 않도록 설정해야 하며 SSTP를 선택해야 합니다. 지점 및 사이트 간 주소 공간은 RFC 1918 주소 블록 10.0.0.0/8, 172.16.0.0/12 및 192.168.0.0/16에 있어야 합니다.

App Service VNet 통합에 사용할 게이트웨이를 만드는 경우 인증서를 업로드할 필요가 없습니다. 게이트웨이를 만드는 데 30분이 걸릴 수 있습니다. 게이트웨이가 프로비전될 때까지는 VNet과 앱을 통합할 수 없습니다.

### <a name="how-gateway-required-vnet-integration-works"></a>필수 게이트웨이와 VNet 통합 작동 원리

필수 게이트웨이 VNet 통합은 지점 및 사이트 간 VPN 기술을 기반으로 합니다. 지점 및 사이트 간 VPN은 앱을 호스트 하는 가상 머신의 네트워크 액세스를 제한합니다. 앱은 하이브리드 연결 또는 VNet 통합을 통해서만 트래픽을 인터넷으로 보내도록 제한됩니다. 앱이 포털을 사용하여 필수 게이트웨이 VNet 통합을 사용하도록 구성된 경우 사용자를 대신해 복잡한 협상을 관리하고 게이트웨이 및 애플리케이션 쪽에서 인증서를 만들고 할당합니다. 따라서 앱을 호스트 하는 데 사용되는 작업자는 선택한 VNet의 가상 네트워크 게이트웨이에 직접 연결할 수 있습니다.

![필수 게이트웨이와 VNet 통합 작동 원리][6]

### <a name="access-on-premises-resources"></a>온-프레미스 리소스에 액세스

앱은 사이트 간 연결이 있는 VNet과 통합되어 온-프레미스 리소스에 액세스할 수 있습니다. 필수 게이트웨이 VNet 통합을 사용하는 경우 지점 및 사이트 간 주소 블록을 사용하여 온-프레미스 VPN gateway 경로를 업데이트합니다. 사이트 간 VPN을 처음 설정하는 경우 이를 구성하는 데 사용되는 스크립트에서 경로를 올바르게 설정해야 합니다. 사이트 간 VPN을 만든 후에 지점 및 사이트 간 VPN을 추가하는 경우 해당 경로를 수동으로 업데이트해야 합니다. 이 작업을 수행하는 방법 관련 세부 정보는 게이트웨이마다 다르며 여기에 설명되어 있지 않습니다. 또한 사이트 간 VPN 연결로 구성된 BGP는 사용할 수 없습니다.

VNet으로 온-프레미스 리소스에 연결하기 위해 지역 VNet 통합 기능 추가 구성은 필요하지 않습니다. ExpressRoute 또는 사이트 간 VPN을 사용하여 VNet을 온-프레미스 리소스에 연결만 하면 됩니다.

> [!NOTE]
> 필수 게이트웨이 VNet 통합 기능은 ExpressRoute 게이트웨이가 있는 VNet과 앱을 통합하지 않습니다. ExpressRoute 게이트웨이가 [동시 존재 모드][VPNERCoex]로 구성되어 있더라도 VNet 통합은 작동하지 않습니다. ExpressRoute 연결을 통해 리소스에 액세스해야 하는 경우에는 VNet에서 실행되는 [App Service Environment][ASE]를 사용할 수 있습니다.
> 
> 

### <a name="peering"></a>피어링

지역 VNet 통합에서 피어링을 사용하는 경우 추가 구성을 수행할 필요가 없습니다.

피어링과 함께 필수 게이트웨이 VNet 통합을 사용하는 경우 몇 가지 추가 항목을 구성해야 합니다. 앱에서 작동하도록 피어링을 구성하려면,

1. 앱이 연결되는 VNet에 피어링 연결을 추가합니다. 피어링 연결을 추가할 때 **가상 네트워크 액세스 허용**을 사용하도록 설정하고, **전달된 트래픽 허용** 및 **게이트 전송 허용**을 선택하세요.
1. 사용자가 연결한 VNet에 피어링되는 VNet으로 피어링 연결을 추가하세요. 대상 VNet에 피어링 연결을 추가할 때 **가상 네트워크 액세스 허용**을 사용하도록 설정하고, **전달된 트래픽 허용** 및 **원격 게이트웨이 허용**을 선택하세요.
1. 포털에서 **App Service 계획** > **네트워킹** > **VNet 통합** UI로 이동합니다. 앱이 연결되는 VNet을 선택합니다. 라우팅 섹션에서 앱이 연결된 VNet과 피어링되는 VNet의 주소 범위를 추가하세요.

## <a name="manage-vnet-integration"></a>VNet 통합 관리

앱 수준에서 VNet에 연결하거나 연결을 끊습니다. 여러 앱의 VNet 통합에 영향을 줄 수 있는 작업은 App Service 계획 수준입니다. 앱 >**네트워킹** > **VNet 통합** 포털에서 VNet 관련 세부 정보를 볼 수 있습니다. **App Service 계획** > **네트워킹** > **VNet 통합** 포털의 App Service 계획 수준에서 유사한 정보를 볼 수 있습니다.

VNet 통합 인스턴스의 앱 보기에서 할 수 있는 유일한 작업은 현재 연결된 VNet과 앱의 연결을 해제하는 것입니다. VNet에서 앱의 연결을 끊으려면 **연결 끊기**를 선택합니다. VNet과의 연결을 끊으면 앱이 다시 시작됩니다. 연결을 끊더라도 VNet은 변경되지 않습니다. 서브넷 또는 게이트웨이가 제거되지 않습니다. 그다음 VNet을 삭제하려면, 먼저 VNet에서 앱의 연결을 끊고 게이트웨이 등의 리소스를 삭제하세요.

App Service 계획 VNet 통합 UI에는 App Service 계획의 앱에서 사용하는 모든 VNet 통합이 표시됩니다. 각 VNet 관련 세부 정보를 보려면 관심 있는 VNet을 선택하세요. 필수 게이트웨이 VNet 통합에 대해 여기에서 수행할 수 있는 작업은 두 가지가 있습니다.

* **네트워크 동기화** 네트워크 동기화 작업은 게이트웨이 종속 VNet 통합 기능에만 사용합니다. 네트워크 동기화 작업을 수행하면 인증서와 네트워크 정보가 동기화됩니다. VNet의 DNS를 추가하거나 변경하면 네트워크 동기화 작업을 수행해야 합니다. 이 작업은 해당 VNet이 사용하는 모든 앱을 다시 시작합니다. 다른 구독에 속하는 앱과 vnet을 사용하는 경우 해당 작업은 작동하지 않습니다.
* **경로 추가** 경로를 추가하면 아웃바운드 트래픽이 VNet에 추가됩니다.

### <a name="gateway-required-vnet-integration-routing"></a>필수 게이트웨이 VNet 통합 라우팅
VNet에 정의된 경로는 트래픽을 앱에서 VNet으로 전달하는 데 사용됩니다. 아웃바운드 트래픽을 VNet에 추가로 보내야 하는 경우 해당 주소 블록을 여기에 추가하세요. 이 기능은 필수 게이트웨이 VNet 통합에서만 작동합니다. 지역 VNet 통합을 사용하는 방식으로 필수 게이트웨이 VNet 통합을 사용하는 경우 경로 테이블은 앱 트래픽에 영향을 주지 않습니다.

### <a name="gateway-required-vnet-integration-certificates"></a>필수 게이트웨이 VNet 통합 인증서
필수 게이트웨이 VNet 통합을 사용하면, 연결 보안을 위해 필요한 인증서 교환이 있습니다. 인증서와 함께 네트워크를 설명하는 DNS 구성, 경로 및 다른 비슷한 항목이 있습니다.

인증서 또는 네트워크 정보가 변경되면 **네트워크 동기화**를 선택하세요. **네트워크 동기화**를 클릭하면 앱과 VNet 연결이 잠시 중단됩니다. 앱이 다시 시작되지는 않지만, 연결 손실로 인해 사이트가 제대로 작동하지 않을 수 있습니다.

## <a name="pricing-details"></a>가격 정보
지역 VNet 통합 기능에는 App Service 계획 가격 책정 계층별 요금 외에 추가 요금이 부과되지 않습니다.

다음의 세 가지 가격이 필수 게이트웨이 VNet 통합 기능 사용과 관련된 가격입니다.

* **App Service 계획 가격 계층별 책정** 앱은 표준, 프리미엄, 또는 프리미엄 V2 App Service 계획에 속해야 합니다. 가격 책정에 대한 자세한 내용은 [App Service 가격 책정][ASPricing]을 참조하세요.
* **데이터 전송 비용** VNet이 동일한 데이터 센터에 있더라도 데이터 송신 요금이 있습니다. 이러한 요금은 [데이터 전송 가격 정보][DataPricing]에서 설명합니다.
* **VPN 게이트웨이 비용** 지점 및 사이트 간 VPN에 필요한 가상 네트워크 게이트웨이 관련 비용이 발생합니다. 자세한 내용은 [VPN 게이트웨이 가격 책정][VNETPricing]을 참조하세요.

## <a name="troubleshooting"></a>문제 해결

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automation

CLI 지원은 지역 VNet 통합에서 이용할 수 있습니다. 다음 명령에 액세스하여[Azure CLI 에이전트][installCLI]를 설치합니다.

```azurecli
az webapp vnet-integration --help

Group
    az webapp vnet-integration : Methods that list, add, and remove virtual network
    integrations from a webapp.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    add    : Add a regional virtual network integration to a webapp.
    list   : List the virtual network integrations on a webapp.
    remove : Remove a regional virtual network integration from webapp.

az appservice vnet-integration --help

Group
    az appservice vnet-integration : A method that lists the virtual network
    integrations used in an appservice plan.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    list : List the virtual network integrations used in an appservice plan.
```

지역 VNet 통합에 대 한 Powershell 지원도 사용할 수 있지만 서브넷 resourceID의 속성 배열을 사용 하 여 제네릭 리소스를 만들어야 합니다.

```azurepowershell
# Parameters
$sitename="myWebApp"
$resourcegroupname="myRG"
$VNetname="myVNet"
$location="myRegion"
$integrationsubnetname = "myIntegrationSubnet"
$subscriptionID = "aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee"

#Property array with the SubnetID
$properties = @{
      "subnetResourceId" = "/subscriptions/"+$subscriptionID+"/resourceGroups/"+$resourcegroupname+"/providers/Microsoft.Network/virtualNetworks/"+$VNetname+"/subnets/"+$integrationsubnetname;
      }
      
#Creation of the VNet integration
$resourceID = $sitename+"/VirtualNetwork"
New-AzResource -ResourceName $resourceID `
-Location $location  `
-ResourceGroupName $resourcegroupname `
-ResourceType Microsoft.Web/sites/networkConfig `
-PropertyObject $properties 

```


필수 게이트웨이 VNet 통합의 경우, PowerShell을 사용하여 Azure 가상 네트워크와 App Service를 통합할 수 있습니다. 실행 준비 스크립트의 경우, [Azure App Service에서 Azure Virtual Network에 앱 연결](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3)을 참조하세요.


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-classic.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-regionalworks.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-gwworks.png


<!--Links-->
[VNETOverview]: ../virtual-network/virtual-networks-overview.md
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: ../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[setp2saddresses]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#addresspool
[VNETRouteTables]: ../virtual-network/manage-route-table.md
[installCLI]: /cli/azure/install-azure-cli?view=azure-cli-latest%2f
[privateendpoints]: networking/private-endpoint.md