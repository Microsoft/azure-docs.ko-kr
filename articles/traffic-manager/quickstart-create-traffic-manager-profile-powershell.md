---
title: '빠른 시작: 고가용성 애플리케이션을 위한 프로필 만들기 - Azure PowerShell - Azure Traffic Manager'
description: 이 빠른 시작 문서에서는 고가용성 웹 애플리케이션을 빌드하기 위한 Traffic Manager 프로필을 만드는 방법을 설명합니다.
services: traffic-manager
author: duongau
mnager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/25/2020
ms.author: duau
ms.openlocfilehash: f3ecdfb03a6e6d1aab355edf7c370b29240e0543
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88929519"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-powershell"></a>빠른 시작: Azure PowerShell을 사용하여 고가용성 웹 애플리케이션을 위한 Traffic Manager 프로필 만들기

이 빠른 시작에서는 웹 애플리케이션에 고가용성을 제공하는 Traffic Manager 프로필을 만드는 방법에 대해 설명합니다.

여기서는 웹 애플리케이션의 두 인스턴스를 만듭니다. 각각 다른 Azure 지역에서 실행됩니다. [엔드포인트 우선 순위](traffic-manager-routing-methods.md#priority-traffic-routing-method)에 따라 Traffic Manager 프로필을 만듭니다. 프로필은 웹 애플리케이션을 실행하는 주 사이트로 사용자 트래픽을 보냅니다. Traffic Manager는 웹 애플리케이션을 지속적으로 모니터링합니다. 주 사이트를 사용할 수 없는 경우 백업 사이트에 자동 장애 조치를 제공합니다.

Azure 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 지금 만드세요.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 문서에는 Azure PowerShell 모듈 버전 5.4.1 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기
[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 리소스 그룹을 만듭니다.

```azurepowershell-interactive


# Variables
$Location1="WestUS"

# Create a Resource Group
New-AzResourceGroup -Name MyResourceGroup -Location $Location1
```

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager 프로필 만들기

[New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile)을 사용하여 엔드포인트 우선 순위에 따라 사용자 트래픽을 보내는 Traffic Manager 프로필을 만듭니다.

```azurepowershell-interactive

# Generates a random value
$Random=(New-Guid).ToString().Substring(0,8)
$mytrafficmanagerprofile="mytrafficmanagerprofile$Random"

New-AzTrafficManagerProfile `
-Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup `
-TrafficRoutingMethod Priority `
-MonitorPath '/' `
-MonitorProtocol "HTTP" `
-RelativeDnsName $mytrafficmanagerprofile `
-Ttl 30 `
-MonitorPort 80
```

## <a name="create-web-apps"></a>웹앱 만들기

이 빠른 시작에는 두 개의 서로 다른 Azure 지역(*미국 서부* 및 *미국 동부*)에 배포된 두 개의 웹 애플리케이션 인스턴스가 필요합니다. 각각은 Traffic Manager에 대한 기본 및 장애 조치 엔드포인트의 역할을 합니다.

### <a name="create-web-app-service-plans"></a>웹 App Service 계획 만들기
서로 다른 두 Azure 지역에 배포할 두 웹 애플리케이션 인스턴스에 대해 [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan)을 사용하여 웹 App Service 계획을 만듭니다.

```azurepowershell-interactive

# Variables
$App1Name="AppServiceTM1$Random"
$App2Name="AppServiceTM2$Random"
$Location1="WestUS"
$Location2="EastUS"

# Create an App service plan
New-AzAppservicePlan -Name "$App1Name-Plan" -ResourceGroupName MyResourceGroup -Location $Location1 -Tier Standard
New-AzAppservicePlan -Name "$App2Name-Plan" -ResourceGroupName MyResourceGroup -Location $Location2 -Tier Standard

```
### <a name="create-a-web-app-in-the-app-service-plan"></a>App Service 계획에서 웹앱 만들기
*미국 서부* 및 *미국 동부* Azure 지역의 App Service 계획에서 [New-AzWebApp](/powershell/module/az.websites/new-azwebapp)을 사용하여 두 웹 애플리케이션 인스턴스를 만듭니다.

```azurepowershell-interactive
$App1ResourceId=(New-AzWebApp -Name $App1Name -ResourceGroupName MyResourceGroup -Location $Location1 -AppServicePlan "$App1Name-Plan").Id
$App2ResourceId=(New-AzWebApp -Name $App2Name -ResourceGroupName MyResourceGroup -Location $Location2 -AppServicePlan "$App2Name-Plan").Id

```

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager 엔드포인트 추가
다음과 같이 [New-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint)를 사용하여 두 웹앱을 Traffic Manager 프로필에 Traffic Manager 엔드포인트로 추가합니다.
- *미국 서부* Azure 지역에 있는 웹앱을 모든 사용자 트래픽을 라우팅할 기본 엔드포인트로서 추가합니다. 
- *미국 동부* Azure 지역에 있는 웹앱을 장애 조치(failover) 엔드포인트로서 추가합니다. 기본 엔드포인트를 사용할 수 없으면 트래픽이 자동으로 장애 조치 엔드포인트로 라우팅됩니다.

```azurepowershell-interactive
New-AzTrafficManagerEndpoint -Name "$App1Name-$Location1" `
-ResourceGroupName MyResourceGroup `
-ProfileName "$mytrafficmanagerprofile" `
-Type AzureEndpoints `
-TargetResourceId $App1ResourceId `
-EndpointStatus "Enabled"

New-AzTrafficManagerEndpoint -Name "$App2Name-$Location2" `
-ResourceGroupName MyResourceGroup `
-ProfileName "$mytrafficmanagerprofile" `
-Type AzureEndpoints `
-TargetResourceId $App2ResourceId `
-EndpointStatus "Enabled"
```

## <a name="test-traffic-manager-profile"></a>Traffic Manager 프로필 테스트

이 섹션에서는 Traffic Manager 프로필의 도메인 이름을 확인합니다. 또한 기본 엔드포인트를 사용할 수 없도록 구성합니다. 마지막으로 웹앱을 계속 사용할 수 있는지 확인합니다. 이는 Traffic Manager에서 트래픽을 장애 조치 엔드포인트로 보내기 때문입니다.

### <a name="determine-the-dns-name"></a>DNS 이름 확인

[Get-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile)을 사용하여 Traffic Manager 프로필의 DNS 이름을 확인합니다.

```azurepowershell-interactive
Get-AzTrafficManagerProfile -Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup
```

**RelativeDnsName** 값을 복사합니다. Traffic Manager 프로필의 DNS 이름은 *http://<* relativednsname *>.trafficmanager.net*입니다. 

### <a name="view-traffic-manager-in-action"></a>실행 중인 Traffic Manager 보기
1. 웹 브라우저에서 Traffic Manager 프로필의 DNS 이름(*http://<* relativednsname *>.trafficmanager.net*)을 입력하여 웹앱의 기본 웹 사이트를 표시합니다.

    > [!NOTE]
    > 이 빠른 시작 시나리오에서는 모든 요청이 기본 엔드포인트로 라우팅됩니다. **우선 순위 1**로 설정됩니다.
2. 작동 중인 Traffic Manager 장애 조치(failover)를 보려면 [Disable-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/disable-aztrafficmanagerendpoint)를 사용하여 기본 사이트를 사용하지 않도록 설정합니다.

   ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name $App1Name-$Location1 `
    -Type AzureEndpoints `
    -ProfileName $mytrafficmanagerprofile `
    -ResourceGroupName MyResourceGroup `
    -Force
   ```
3. Traffic Manager 프로필의 DNS 이름(*http://<* relativednsname *>.trafficmanager.net*)을 복사하여 새 웹 브라우저 세션에서 웹 사이트를 표시합니다.
4. 웹앱을 계속 사용할 수 있는지 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

완료되면 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)을 사용하여 리소스 그룹, 웹 애플리케이션 및 모든 관련 리소스를 삭제합니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 웹 애플리케이션에 고가용성을 제공하는 Traffic Manager 프로필을 만들었습니다. 트래픽 라우팅에 대해 자세히 알아보려면 Traffic Manager 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Traffic Manager 자습서](tutorial-traffic-manager-improve-website-response.md)
