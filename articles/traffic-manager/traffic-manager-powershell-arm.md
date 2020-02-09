---
title: Azure에서 PowerShell을 사용하여 Traffic Manager 관리
description: 이 학습 경로를 사용 하 여 Traffic Manager에 대 한 Azure PowerShell 사용을 시작 하세요.
services: traffic-manager
documentationcenter: na
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: rohink
ms.openlocfilehash: 7886764a69eefa68be071a801bea65ae995fbdc3
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938508"
---
# <a name="using-powershell-to-manage-traffic-manager"></a>PowerShell을 사용하여 Traffic Manager 관리

Azure의 서비스용 관리 인터페이스로는 기본적으로 Azure Resource Manager가 사용됩니다. Azure Resource Manager 기반 API 및 도구를 사용하여 Azure Traffic Manager 프로파일을 관리할 수 있습니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="resource-model"></a>리소스 모델

Azure Traffic Manager는 Traffic Manager 프로필을 호출하는 설정 모음을 사용하여 구성됩니다. 이 프로필에는 DNS 설정, 트래픽 라우팅 설정, 엔드포인트 모니터링 설정 및 트래픽이 라우팅되는 서비스 엔드포인트 목록이 포함됩니다.

각 Traffic Manager 프로필은 'TrafficManagerProfiles' 유형의 리소스로 표시됩니다. REST API 수준에서 각 프로필의 URI는 다음과 같습니다.

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## <a name="setting-up-azure-powershell"></a>Azure PowerShell 설정

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

이러한 지침은 Microsoft Azure PowerShell을 사용합니다. 다음 문서는 Azure PowerShell 설치 및 구성하는 방법을 설명합니다.

* [Azure PowerShell 설치 및 구성하는 방법](/powershell/azure/overview)

이 문서의 예제에서는 기존 리소스 그룹이 있다고 가정합니다. 다음 명령을 사용하여 리소스 그룹을 만들 수 있습니다.

```powershell
New-AzResourceGroup -Name MyRG -Location "West US"
```

> [!NOTE]
> Azure Resource Manager를 사용하려면 모든 리소스 그룹에 위치가 있어야 합니다. 이 위치는 해당 리소스 그룹에서 만들어진 리소스의 기본 위치로 사용됩니다. 하지만 Traffic Manager 프로필 리소스는 영역별이 아니라 전역이므로 리소스 그룹의 위치 선택이 Azure Traffic Manager에 영향을 주지 않습니다.

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager 프로필 만들기

Traffic Manager 프로필을 만들려면 `New-AzTrafficManagerProfile` cmdlet를 사용합니다.

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

다음 표는 매개 변수를 설명합니다.

| 매개 변수 | Description |
| --- | --- |
| 이름 |Traffic Manager 프로필 리소스의 ARM 리소스 이름입니다. 동일한 리소스 그룹의 프로필 이름은 고유해야 합니다. 이 이름은 DNS 쿼리에 사용되는 DNS 이름과 구분됩니다. |
| ResourceGroupName |프로필 리소스가 포함된 리소스 그룹의 이름. |
| TrafficRoutingMethod |DNS 쿼리에 대한 응답으로 반환되는 엔드포인트를 결정하는 데 사용되는 트래픽 라우팅 메서드를 지정합니다. 가능한 값은 '성능', '가중' 또는 '우선 순위'입니다. |
| RelativeDnsName |이 Traffic Manager 프로필을 통해 제공되는 DNS 이름의 호스트 이름 부분을 지정합니다. 이 값은 프로필의 FQDN(정규화된 도메인 이름)을 형성하여 Azure Traffic Manager가 사용하는 DNS 도메인 이름과 결합됩니다. 예를 들어 'contoso'의 값이 'contoso.trafficmanager.net.'이 되도록 설정합니다. |
| TTL |초 단위로 DNS TTL(Time-to-Live)을 지정합니다. 이 TTL은 로컬 DNS 확인자 및 DNS 클라이언트에게 이 Traffic Manager 프로필에 대한 DNS 응답을 캐시하는 데 걸린 시간을 알려줍니다. |
| MonitorProtocol |엔드포인트 상태를 모니터링 하는 데 사용할 프로토콜을 지정합니다. 가능한 값은 ‘HTTP’ 및 ‘HTTPS’입니다. |
| MonitorPort |엔드포인트 상태를 모니터링 하는 데 사용할 TCP 포트를 지정합니다. |
| MonitorPath |엔드포인트 상태를 조사하는 데 사용되는 엔드포인트 도메인 이름에 대한 상대 경로를 지정합니다. |

cmdlet는 Azure에서 Traffic Manager 프로필 만들고 해당 프로필 개체를 PowerShell에 반환합니다. 이 시점에 프로필에 엔드포인트가 없습니다. Traffic Manager 프로필에 엔드포인트를 추가하는 방법에 대한 자세한 내용은 Traffic Manager 엔드포인트 추가를 참조하세요.

## <a name="get-a-traffic-manager-profile"></a>Traffic Manager 프로필 가져오기

기존 Traffic Manager 프로필 개체를 검색하려면 `Get-AzTrafficManagerProfle` cmdlet를 사용합니다.

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
```

이 cmdlet은 Traffic Manager 프로필 개체를 반환합니다.

## <a name="update-a-traffic-manager-profile"></a>Traffic Manager 프로필 업데이트

Traffic Manager 프로필을 수정하려면 3 단계 프로세스를 따릅니다.

1. `Get-AzTrafficManagerProfile`을 사용하여 프로필을 검색하거나 `New-AzTrafficManagerProfile`에서 반환된 프로필을 사용합니다.
2. 프로필을 수정합니다. 엔드포인트를 추가 및 제거하거나 엔드포인트 또는 프로필 매개 변수를 변경할 수 있습니다. 이러한 변경은 오프라인 작업입니다. 프로필을 나타내는 메모리에 있는 로컬 개체만 변경합니다.
3. `Set-AzTrafficManagerProfile` cmdlet을 사용하여 변경 내용을 커밋합니다.

프로필의 RelativeDnsName을 제외하고 모든 프로필 속성을 변경할 수 있습니다. RelativeDnsName를 변경하려면 프로필 및 새 이름이 있는 새 프로필을 삭제해야 합니다.

다음 예제는 프로필의 TTL을 변경하는 방법을 보여 줍니다.

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
$TmProfile.Ttl = 300
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

Traffic Manager 엔드포인트에는 세 가지 종류가 있습니다.

1. **Azure 엔드포인트** 는 Azure에서 호스팅되는 서비스입니다.
2. **외부 엔드포인트**는 Azure 외부에서 호스트되는 서비스입니다.
3. **중첩 엔드포인트**는 Traffic Manager 프로필의 중첩된 계층을 구성하는 데 사용됩니다. 중첩 엔드포인트는 복잡한 애플리케이션에 대한 고급 트래픽 라우팅 구성을 사용하도록 설정합니다.

세 가지 경우 모두, 엔드포인트는 두 가지 방법으로 추가될 수 있습니다.

1. 앞에서 설명한 3 단계 프로세스를 사용합니다. 이 방법의 장점은 여러 엔드포인트 변경이 단일 업데이트에서 이뤄진다는 점입니다.
2. New-AzTrafficManagerEndpoint cmdlet 사용. 이 cmdlet은 엔드포인트를 단일 작업의 기존 Traffic Manager 프로필에 추가합니다.

## <a name="adding-azure-endpoints"></a>Azure 엔드포인트 추가

Azure 엔드포인트는 Azure에서 호스팅되는 서비스를 나타냅니다. 두 가지 유형의 Azure 엔드포인트가 지원됩니다.

1. Azure App Service
2. Azure PublicIpAddress 리소스(부하 분산 장치 또는 가상 머신 NIC에 연결할 수 있음). publicIpAddress에는 Traffic Manager에서 사용되도록 지정된 DNS 이름이 있어야 합니다.

각 경우에 다음이 해당됩니다.

* 서비스는 `Add-AzTrafficManagerEndpointConfig` 또는 `New-AzTrafficManagerEndpoint`의 'targetResourceId' 매개 변수를 사용하여 지정합니다.
* 'Target' 및 'EndpointLocation'은 TargetResourceId에 포함됩니다.
* '가중치' 지정은 선택 사항입니다. 가중치는 '가중' 트래픽 라우팅 메서드를 사용하도록 프로필을 구성한 경우에만 사용됩니다. 그렇지 않으면 무시됩니다. 지정된 경우, 이 값은 1과 1000 사이의 숫자여야 합니다. 기본값은 '1'입니다.
* '우선 순위' 지정은 선택 사항입니다. 우선 순위는 '우선 순위' 트래픽 라우팅 메서드를 사용하도록 프로필을 구성한 경우에만 사용됩니다. 그렇지 않으면 무시됩니다. 유효한 값은 낮은 숫자가 더 높은 우선 순위를 나타내도록 한 1부터 1000까지의 숫자입니다. 한 엔드포인트에 대해 지정한 경우 모든 엔드포인트에 대해 지정되어야 합니다. 생략한 경우 '1'부터 시작하는 기본값이 엔드포인트가 나열된 순서대로 적용됩니다.

### <a name="example-1-adding-app-service-endpoints-using-add-aztrafficmanagerendpointconfig"></a>예제 1: `Add-AzTrafficManagerEndpointConfig`를 사용 하 여 App Service 엔드포인트 추가

이 예제에서는 Traffic Manager 프로필을 만들고 `Add-AzTrafficManagerEndpointConfig` cmdlet을 사용하여 두 개의 App Service 엔드포인트를 추가합니다.

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$webapp1 = Get-AzWebApp -Name webapp1
Add-AzTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $TmProfile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
$webapp2 = Get-AzWebApp -Name webapp2
Add-AzTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $TmProfile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```
### <a name="example-2-adding-a-publicipaddress-endpoint-using-new-aztrafficmanagerendpoint"></a>예시 2: `New-AzTrafficManagerEndpoint`을 사용하여 publicIpAddress 엔드포인트 추가

이 예제에서는 공용 IP 주소 리소스가 Traffic Manager 프로필에 추가됩니다. 공용 IP 주소는 구성된 DNS 이름이 있어야 하고 VM의 NIC 또는 부하 분산 장치에 바인딩될 수 있습니다.

```powershell
$ip = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
New-AzTrafficManagerEndpoint -Name MyIpEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="adding-external-endpoints"></a>외부 엔드포인트 추가

Traffic Manager는 외부 엔드포인트를 사용하여 Azure 외부에서 호스팅되는 서비스에 트래픽을 보냅니다. Azure 엔드포인트와 마찬가지로 외부 엔드포인트는 `Add-AzTrafficManagerEndpointConfig` 뒤 이어 `Set-AzTrafficManagerProfile` 또는 `New-AzTrafficManagerEndpoint`을 사용하여 추가할 수 있습니다.

외부 엔드포인트를 지정하는 경우:

* 'Target' 매개 변수를 사용하여 엔드포인트 도메인 이름을 지정해야 합니다.
* 'Performance' 트래픽 라우팅 메서드를 사용하는 경우 'EndpointLocation'이 필수입니다. 그렇지 않은 경우 선택적입니다. 값은 [올바른 Azure 지역 이름](https://azure.microsoft.com/regions/)이어야 합니다.
* 'Weight' 및 'Priority'는 선택 사항입니다.

### <a name="example-1-adding-external-endpoints-using-add-aztrafficmanagerendpointconfig-and-set-aztrafficmanagerprofile"></a>예시 1: `Add-AzTrafficManagerEndpointConfig`과 `Set-AzTrafficManagerProfile`를 사용하여 외부 엔드포인트 추가

이 예제에서는 Traffic Manager 프로필을 만들고 두 개의 외부 엔드포인트를 추가하고 변경 내용을 커밋합니다.

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzTrafficManagerEndpointConfig -EndpointName eu-endpoint -TrafficManagerProfile $TmProfile -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointLocation "North Europe" -EndpointStatus Enabled
Add-AzTrafficManagerEndpointConfig -EndpointName us-endpoint -TrafficManagerProfile $TmProfile -Type ExternalEndpoints -Target app-us.contoso.com -EndpointLocation "Central US" -EndpointStatus Enabled
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

### <a name="example-2-adding-external-endpoints-using-new-aztrafficmanagerendpoint"></a>예시 2: `New-AzTrafficManagerEndpoint`을 사용하여 외부 엔드포인트 추가

이 예제에서는 기존 프로필에 외부 엔드포인트를 추가합니다. 프로필은 프로필 및 리소스 그룹 이름을 사용하여 지정합니다.

```powershell
New-AzTrafficManagerEndpoint -Name eu-endpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
```

## <a name="adding-nested-endpoints"></a>'중첩' 엔드포인트 추가

각 Traffic Manager 프로필은 단일 트래픽 라우팅 방법을 지정합니다. 그러나 단일 Traffic Manager 프로필에서 제공하는 것보다 더 정교한 트래픽 라우팅을 요구하는 시나리오가 있습니다. 둘 이상의 트래픽 라우팅 메서드의 장점을 결합하기 위해 Traffic Manager 프로필을 중첩할 수 있습니다. 중첩된 프로필을 사용하여 더 크고 복잡한 애플리케이션 배포를 지원하기 위해 기본 Traffic Manager 동작을 재정의할 수 있습니다. 자세한 예시는 [중첩 Traffic Manager 프로필](traffic-manager-nested-profiles.md)을 참조하세요.

중첩 엔드포인트는 특정 엔드포인트 유형인 'NestedEndpoints'를 사용하여 부모 프로필에서 구성됩니다. 중첩된 외부 엔드포인트를 지정하는 경우: 지정하는 경우:

* 'targetResourceId' 매개 변수를 사용하여 엔드포인트를 지정해야 합니다.
* 'Performance' 트래픽 라우팅 메서드를 사용하는 경우 'EndpointLocation'이 필수입니다. 그렇지 않은 경우 선택적입니다. 값은 [올바른 Azure 지역 이름](https://azure.microsoft.com/regions/)이어야 합니다.
* Azure 엔드포인트에 대해 'Weight' 및 'Priority'는 선택 사항입니다.
* 'MinChildEndpoints' 매개 변수는 선택적입니다. 기본값은 '1'입니다. 사용할 수 있는 엔드포인트 수가 이 임계값 아래로 떨어지는 경우 부모 프로필은 자식 프로필의 '성능이 저하되었다'고 간주하고 트래픽을 부모 프로필에 있는 다른 엔드포인트로 전환합니다.

### <a name="example-1-adding-nested-endpoints-using-add-aztrafficmanagerendpointconfig-and-set-aztrafficmanagerprofile"></a>예시 1: `Add-AzTrafficManagerEndpointConfig`과 `Set-AzTrafficManagerProfile`를 사용하여 중첩 엔드포인트 추가

이 예제에서는 새 Traffic Manager 자식 및 부모 프로필을 만들고 자식을 부모의 중첩 엔드포인트로 추가하고 변경 내용을 커밋합니다.

```powershell
$child = New-AzTrafficManagerProfile -Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$parent = New-AzTrafficManagerProfile -Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint -TrafficManagerProfile $parent -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
Set-AzTrafficManagerProfile -TrafficManagerProfile $parent
```

이 예제에서는 간단한 설명을 위해 자식 또는 부모 프로필에 다른 엔드포인트를 추가하지 않았습니다.

### <a name="example-2-adding-nested-endpoints-using-new-aztrafficmanagerendpoint"></a>예시 2: `New-AzTrafficManagerEndpoint`을 사용하여 중첩 엔드포인트 추가

이 예제에서는 기존 부모 프로필에 기존 자식 프로필을 중첩된 엔드포인트로 추가합니다. 프로필은 프로필 및 리소스 그룹 이름을 사용하여 지정합니다.

```powershell
$child = Get-AzTrafficManagerEndpoint -Name child -ResourceGroupName MyRG
New-AzTrafficManagerEndpoint -Name child-endpoint -ProfileName parent -ResourceGroupName MyRG -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
```

## <a name="adding-endpoints-from-another-subscription"></a>다른 구독에서 엔드포인트 추가

Traffic Manager는 다른 구독의 엔드포인트에서 사용할 수 있습니다. Traffic Manager에 필요한 입력을 검색하려면 추가하려는 엔드포인트로 구독을 전환해야 합니다. 그런 다음 Traffic Manager 프로필을 사용 하 여 구독으로 전환 하 고 엔드포인트을 추가 해야 합니다. 아래 예제에서는 공용 IP 주소로 이 작업을 수행하는 방법을 보여줍니다.

```powershell
Set-AzContext -SubscriptionId $EndpointSubscription
$ip = Get-AzPublicIpAddress -Name $IpAddressName -ResourceGroupName $EndpointRG

Set-AzContext -SubscriptionId $trafficmanagerSubscription
New-AzTrafficManagerEndpoint -Name $EndpointName -ProfileName $ProfileName -ResourceGroupName $TrafficManagerRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="update-a-traffic-manager-endpoint"></a>Traffic Manager 엔드포인트 업데이트

기존 Traffic Manager 엔드포인트를 업데이트하는 방법은 두 가지입니다.

1. `Get-AzTrafficManagerProfile`을 사용하여 Traffic Manager 프로필을 가져오고 프로필 내의 엔드포인트 속성을 업데이트하고 `Set-AzTrafficManagerProfile`를 사용하여 변경 내용을 커밋합니다. 이 메서드는 한 번에 엔드포인트를 둘 이상 업데이트할 수 있다는 장점이 있습니다.
2. `Get-AzTrafficManagerEndpoint`을 사용하여 Traffic Manager 엔드포인트 가져오고 엔드포인트 속성을 업데이트하고 `Set-AzTrafficManagerEndpoint`를 사용하여 변경 내용을 커밋합니다. 이 메서드는 프로필에서 엔드포인트 배열로 인덱싱하지 않아도 되므로 훨씬 간단합니다.

### <a name="example-1-updating-endpoints-using-get-aztrafficmanagerprofile-and-set-aztrafficmanagerprofile"></a>예시 1: `Get-AzTrafficManagerProfile`과 `Set-AzTrafficManagerProfile`를 사용하여 엔드포인트 업데이트

이 예제에서는 기존 프로필 내에서 두 개의 엔드포인트에 대한 우선 순위를 수정합니다.

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG
$TmProfile.Endpoints[0].Priority = 2
$TmProfile.Endpoints[1].Priority = 1
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

### <a name="example-2-updating-an-endpoint-using-get-aztrafficmanagerendpoint-and-set-aztrafficmanagerendpoint"></a>예시 2: `Get-AzTrafficManagerEndpoint`과 `Set-AzTrafficManagerEndpoint`를 사용하여 엔드포인트 업데이트

이 예제에서는 기존 프로필에 있는 단일 엔드포인트의 가중치를 수정합니다.

```powershell
$endpoint = Get-AzTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
$endpoint.Weight = 20
Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint
```

## <a name="enabling-and-disabling-endpoints-and-profiles"></a>엔드포인트 및 프로필 활성화 및 비활성화

Traffic Manager는 전체 프로필의 활성화 및 비활성화를 허용할 뿐 아니라 개별 엔드포인트의 활성화 및 비활성화를 허용합니다.
엔드포인트 또는 프로필 리소스 가져오기/업데이트/설정으로 변경할 수 있습니다. 이러한 일반적인 작업을 간소화하기 위해 전용 cmdlet을 통해 지원됩니다.

### <a name="example-1-enabling-and-disabling-a-traffic-manager-profile"></a>예제 1: Traffic Manager 프로필 활성화 및 비활성화

Traffic Manager 프로필을 사용하도록 설정하려면 `Enable-AzTrafficManagerProfile` cmdlet를 사용합니다. 프로필은 프로필 개체를 사용하여 지정할 수 있습니다. 프로필 개체를 파이프라인을 통해 또는 '-TrafficManagerProfile' 매개 변수를 사용하여 전달할 수 있습니다. 이 예제에서는 프로필 개체 및 리소스 그룹 이름을 사용하여 프로필을 지정합니다.

```powershell
Enable-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Traffic Manager 프로필을 비활성화하려면:

```powershell
Disable-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Disable-AzTrafficManagerProfile cmdlet은 확인 프롬프트를 표시합니다. 이 프롬프트는 '-Force' 매개 변수를 사용하여 표시되지 않게 할 수 있습니다.

### <a name="example-2-enabling-and-disabling-a-traffic-manager-endpoint"></a>예제 2: Traffic Manager 엔드포인트 활성화 및 비활성화

Traffic Manager 엔드포인트를 활성화하려면 `Enable-AzTrafficManagerEndpoint`을 사용합니다. 엔드포인트를 지정하는 방법은 두 가지입니다

1. 파이프라인을 통해 전달되는 TrafficManagerEndpoint 개체를 사용하거나 '-TrafficManagerEndpoint' 매개 변수를 사용합니다.
2. 엔드포인트 이름, 엔드포인트 형식, 프로필 이름 및 리소스 그룹 이름 사용:

```powershell
Enable-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

마찬가지로 Traffic Manager 엔드포인트를 비활성화하려면:

```powershell
Disable-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force
```

`Disable-AzTrafficManagerProfile`과 마찬가지로, `Disable-AzTrafficManagerEndpoint` cmdlet는 확인 프롬프트를 표시합니다. 이 프롬프트는 '-Force' 매개 변수를 사용하여 표시되지 않게 할 수 있습니다.

## <a name="delete-a-traffic-manager-endpoint"></a>Traffic Manager 엔드포인트 삭제

개별 엔드포인트를 제거하려면 `Remove-AzTrafficManagerEndpoint` cmdlet을 사용합니다.

```powershell
Remove-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

이 cmdlet은 확인 프롬프트를 표시합니다. 이 프롬프트는 '-Force' 매개 변수를 사용하여 표시되지 않게 할 수 있습니다.

## <a name="delete-a-traffic-manager-profile"></a>Traffic Manager 프로필 삭제

Traffic Manager 프로필을 삭제하려면 프로필 이름 및 리소스 그룹 이름을 지정하는 `Remove-AzTrafficManagerProfile` cmdlet을 사용합니다.

```powershell
Remove-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG [-Force]
```

이 cmdlet은 확인 프롬프트를 표시합니다. 이 프롬프트는 '-Force' 매개 변수를 사용하여 표시되지 않게 할 수 있습니다.

삭제할 프로필은 프로필 개체를 사용하여 지정할 수도 있습니다.

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
Remove-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile [-Force]
```

이 순서는 파이프될 수도 있습니다.

```powershell
Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG | Remove-AzTrafficManagerProfile [-Force]
```

## <a name="next-steps"></a>다음 단계

[Traffic Manager 모니터링](traffic-manager-monitoring.md)

[Traffic Manager 성능 고려 사항](traffic-manager-performance-considerations.md)
