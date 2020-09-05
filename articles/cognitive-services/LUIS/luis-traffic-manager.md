---
title: 끝점 할당량 늘리기-LUIS
titleSuffix: Azure Cognitive Services
description: Language Understanding(LUIS)은 단일 키의 할당량 이상으로 엔드포인트 요청 할당량을 늘리는 기능을 제공합니다. 이렇게 하려면 **게시** 페이지의 **리소스 및 키** 섹션에서 LUIS에 대해 더 많은 키를 만들고 LUIS 애플리케이션에 추가합니다.
author: diberry
manager: nitinme
ms.custom: seodec18, devx-track-javascript, devx-track-azurepowershell
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: 96d844d52fa554b7f9a467ae59d8ed4ccd2f6ee2
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079883"
---
# <a name="use-microsoft-azure-traffic-manager-to-manage-endpoint-quota-across-keys"></a>Microsoft Azure Traffic Manager를 사용하여 키 전체에서 엔드포인트 할당량 관리
Language Understanding(LUIS)은 단일 키의 할당량 이상으로 엔드포인트 요청 할당량을 늘리는 기능을 제공합니다. 이렇게 하려면 **게시** 페이지의 **리소스 및 키** 섹션에서 LUIS에 대해 더 많은 키를 만들고 LUIS 애플리케이션에 추가합니다.

클라이언트 애플리케이션은 키 전체에서 트래픽을 관리해야 합니다. LUIS는 이러한 작업을 수행하지 않습니다.

이 문서에서는 Azure [Traffic Manager][traffic-manager-marketing]를 사용하여 키 전체에서 트래픽을 관리하는 방법에 대해 설명합니다. 이미 학습하고 게시한 LUIS 앱이 있어야 합니다. 이러한 앱이 없는 경우, 미리 빌드된 도메인 [빠른 시작](luis-get-started-create-app.md)을 따르세요.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connect-to-powershell-in-the-azure-portal"></a>Azure Portal에서 PowerShell에 연결
[Azure][azure-portal] Portal에서 PowerShell 창을 엽니다. PowerShell 창의 아이콘은 맨 위 탐색 모음에 있는 **>_** 입니다. 포털에서 PowerShell을 사용하면 최신 PowerShell 버전이 다운로드되고 인증됩니다. 포털에서 PowerShell을 사용하려면 [Azure Storage](https://azure.microsoft.com/services/storage/) 계정이 필요합니다.

![Powershell 창이 열려 있는 Azure Portal 스크린샷](./media/traffic-manager/azure-portal-powershell.png)

다음 섹션에서는 [Traffic Manager PowerShell cmdlet](https://docs.microsoft.com/powershell/module/az.trafficmanager/#traffic_manager)을 사용합니다.

## <a name="create-azure-resource-group-with-powershell"></a>PowerShell을 사용하여 Azure 리소스 그룹 만들기
Azure 리소스를 만들기 전에 모든 리소스를 포함할 리소스 그룹을 만듭니다. 리소스 그룹 이름을 `luis-traffic-manager`로 지정하고 지역은 `West US`를 사용합니다. 리소스 그룹의 지역에는 그룹에 대한 메타데이터가 저장됩니다. 다른 지역에 있어도 리소스 속도가 느려지지는 않습니다.

**[AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)** cmdlet을 사용 하 여 리소스 그룹을 만듭니다.

```powerShell
New-AzResourceGroup -Name luis-traffic-manager -Location "West US"
```

## <a name="create-luis-keys-to-increase-total-endpoint-quota"></a>LUIS 키를 만들어 총 엔드포인트 할당량 늘리기
1. Azure Portal에서 두 개의 **Language Understanding** 키를 만듭니다. 하나는 `West US`에, 하나는 `East US`에 만듭니다. 이전 섹션에서 만든 기존 리소스 그룹 `luis-traffic-manager`를 사용합니다.

    ![luis-traffic-manager 리소스 그룹에 두 개의 LUIS 키가 있는 Azure Portal의 스크린샷](./media/traffic-manager/luis-keys.png)

2. [LUIS][LUIS] 웹 사이트의 **관리** 섹션에 있는 **Azure 리소스** 페이지에서 앱에 키를 할당 하 고 오른쪽 위에 있는 메뉴에서 **게시** 단추를 선택 하 여 앱을 다시 게시 합니다.

    **엔드포인트** 열의 예제 URL에서는 엔드포인트 키가 있는 GET 요청을 쿼리 매개 변수로 사용합니다. 새 키 두 개의 엔드포인트 URL을 복사합니다. 이 URL은 이 문서의 뒷부분에 나오는 Traffic Manager 구성의 일부로 사용됩니다.

## <a name="manage-luis-endpoint-requests-across-keys-with-traffic-manager"></a>Traffic Manager를 사용하여 키 전체에서 LUIS 엔드포인트 요청 관리
Traffic Manager는 엔드포인트에 대한 새 DNS 액세스 지점을 만듭니다. 이 지점은 게이트웨이나 프록시 역할을 하지 않고 DNS 수준에서만 엄격하게 사용됩니다. 이 예제에서는 DNS 레코드를 변경하지 않습니다. DNS 라이브러리를 사용하여 Traffic Manager와 통신하고 특정 요청에 올바른 엔드포인트를 가져옵니다. LUIS에 대한 ‘각’ 요청은__ 먼저 Traffic Manager 요청에서 사용할 LUIS 엔드포인트를 결정하도록 합니다.

### <a name="polling-uses-luis-endpoint"></a>폴링에서 LUIS 엔드포인트 사용
Traffic Manager는 엔드포인트를 정기적으로 폴링하여 엔드포인트를 계속 사용할 수 있도록 합니다. 폴링된 Traffic Manager URL은 GET 요청을 통해 액세스할 수 있어야 하며 200을 반환해야 합니다. **게시** 페이지의 엔드포인트 URL이 이 역할을 합니다. 엔드포인트 키마다 다른 경로와 쿼리 문자열 매개 변수가 있으므로 엔드포인트 키마다 다른 폴링 경로가 필요합니다. Traffic Manager가 폴링할 때마다 할당량 요청 비용이 듭니다. LUIS 엔드포인트의 쿼리 문자열 매개 변수 **q**는 LUIS로 전송된 발화입니다. 이 매개 변수는 발화를 전송하는 대신 Traffic Manager를 구성하는 동안 디버깅 기술로 Traffic Manager 폴링을 LUIS 엔드포인트 로그에 추가하는 데 사용됩니다.

각 LUIS 엔드포인트에는 고유한 경로가 필요하므로 고유한 Traffic Manager 프로필이 필요합니다. 프로필을 관리하려면[__‘중첩’ Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-nested-profiles) 아키텍처를 만듭니다. 하나의 부모 프로필은 자식 프로필을 가리키고 이러한 프로필의 트래픽을 관리합니다.

Traffic Manager를 구성한 다음에는 logging=false 쿼리 문자열 매개 변수를 사용하도록 경로를 변경하여 로그가 폴링으로 채워지지 않도록 해야 합니다.

## <a name="configure-traffic-manager-with-nested-profiles"></a>중첩 프로필로 Traffic Manager 구성
다음 섹션에서는 East LUIS 키와 West LUIS 키에 대해 하나씩 두 개의 자식 프로필을 만듭니다. 그런 다음, 부모 프로필을 만들고 이 부모 프로필에 두 개의 자식 프로필을 추가합니다.

### <a name="create-the-east-us-traffic-manager-profile-with-powershell"></a>PowerShell을 사용하여 미국 동부 Traffic Manager 프로필 만들기
미국 동부 Traffic Manager 프로필을 만들려면 프로필 만들기, 엔드포인트 추가, 엔드포인트 설정 등의 여러 단계를 수행합니다. Traffic Manager 프로필에는 많은 엔드포인트가 있을 수 있지만 각 엔드포인트에는 동일한 유효성 검사 경로가 있습니다. 지역 및 엔드포인트 키로 인해 east 및 west 구독에 대한 LUIS 엔드포인트 URL이 서로 다르기 때문에 각 LUIS 엔드포인트은 프로필에서 단일 엔드포인트이어야 합니다.

1. **[AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile)** cmdlet을 사용 하 여 프로필 만들기

    다음 cmdlet을 사용하여 프로필을 만듭니다. `appIdLuis` 및 `subscriptionKeyLuis`를 변경해야 합니다. subscriptionKey는 미국 동부 LUIS 키용입니다. LUIS 앱 ID 및 엔드포인트 키를 포함하여 경로가 올바르지 않으면 Traffic Manager에서 LUIS 엔드포인트을 요청할 수 없기 때문에 Traffic Manager 폴링은 `degraded` 상태가 됩니다. `q` 값이 `traffic-manager-east`인지 확인하여 이 값이 LUIS 엔드포인트 로그에 표시되도록 합니다.

    ```powerShell
    $eastprofile = New-AzTrafficManagerProfile -Name luis-profile-eastus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-eastus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appID>?subscription-key=<subscriptionKey>&q=traffic-manager-east"
    ```

    다음 표에서는 cmdlet의 각 변수에 대해 설명합니다.

    |구성 매개 변수|변수 이름 또는 값|목적|
    |--|--|--|
    |-Name|luis-profile-eastus|Azure Portal의 Traffic Manager 이름|
    |-ResourceGroupName|luis-traffic-manager|이전 섹션에서 만든 리소스 그룹 이름|
    |-TrafficRoutingMethod|성능|자세한 내용은 [Traffic Manager 라우팅 방법][routing-methods]을 참조하세요. 성능을 사용하는 경우, 사용자의 지역에서 Traffic Manager에 대한 URL 요청을 가져와야 합니다. 챗봇이나 다른 애플리케이션을 사용하는 경우, 챗봇이 Traffic Manager에 대한 호출에서 지역을 모방해야 합니다. |
    |-RelativeDnsName|luis-dns-eastus|서비스의 하위 도메인(luis-dns-eastus.trafficmanager.net)|
    |-Ttl|30|폴링 간격(30초)|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|LUIS에 대한 포트 및 프로토콜은 HTTPS/443입니다.|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-east`|`<appIdLuis>` 및 `<subscriptionKeyLuis>`를 사용자 고유의 값으로 바꿉니다.|

    성공한 요청에는 응답이 없습니다.

2. **[AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.trafficmanager/add-aztrafficmanagerendpointconfig)** cmdlet을 사용 하 여 미국 동부 끝점 추가

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-east-endpoint -TrafficManagerProfile $eastprofile -Type ExternalEndpoints -Target eastus.api.cognitive.microsoft.com -EndpointLocation "eastus" -EndpointStatus Enabled
    ```
    다음 표에서는 cmdlet의 각 변수에 대해 설명합니다.

    |구성 매개 변수|변수 이름 또는 값|목적|
    |--|--|--|
    |-EndpointName|luis-east-endpoint|프로필 아래에 표시되는 엔드포인트 이름|
    |-TrafficManagerProfile|$eastprofile|1단계에서 만든 프로필 개체 사용|
    |-Type|ExternalEndpoints|자세한 내용은 [Traffic Manager 엔드포인트][traffic-manager-endpoints]를 참조하세요. |
    |-Target|eastus.api.cognitive.microsoft.com|LUIS 엔드포인트의 도메인입니다.|
    |-EndpointLocation|“eastus”|엔드포인트의 지역|
    |-EndpointStatus|사용|엔드포인트를 만들 때 사용하도록 설정|

    성공한 응답은 다음과 같습니다.

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-eastus
    Name                             : luis-profile-eastus
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-eastus
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /luis/v2.0/apps/<luis-app-id>?subscription-key=f0517d185bcf467cba5147d6260bb868&q=traffic-manager-east
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {luis-east-endpoint}
    ```

3. **[AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerprofile)** cmdlet을 사용 하 여 미국 동부 끝점 설정

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $eastprofile
    ```

    성공한 응답은 2단계의 응답과 동일합니다.

### <a name="create-the-west-us-traffic-manager-profile-with-powershell"></a>PowerShell을 사용하여 미국 서부 Traffic Manager 프로필 만들기
미국 서부 Traffic Manager 프로필을 만들려면 프로필 만들기, 엔드포인트 추가, 엔드포인트 설정 등의 동일한 단계를 수행합니다.

1. **[AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)** cmdlet을 사용 하 여 프로필 만들기

    다음 cmdlet을 사용하여 프로필을 만듭니다. `appIdLuis` 및 `subscriptionKeyLuis`를 변경해야 합니다. subscriptionKey는 미국 동부 LUIS 키용입니다. LUIS 앱 ID 및 엔드포인트 키를 포함하여 경로가 올바르지 않으면 Traffic Manager에서 LUIS 엔드포인트을 요청할 수 없기 때문에 Traffic Manager 폴링은 `degraded` 상태가 됩니다. `q` 값이 `traffic-manager-west`인지 확인하여 이 값이 LUIS 엔드포인트 로그에 표시되도록 합니다.

    ```powerShell
    $westprofile = New-AzTrafficManagerProfile -Name luis-profile-westus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-westus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west"
    ```

    다음 표에서는 cmdlet의 각 변수에 대해 설명합니다.

    |구성 매개 변수|변수 이름 또는 값|목적|
    |--|--|--|
    |-Name|luis-profile-westus|Azure Portal의 Traffic Manager 이름|
    |-ResourceGroupName|luis-traffic-manager|이전 섹션에서 만든 리소스 그룹 이름|
    |-TrafficRoutingMethod|성능|자세한 내용은 [Traffic Manager 라우팅 방법][routing-methods]을 참조하세요. 성능을 사용하는 경우, 사용자의 지역에서 Traffic Manager에 대한 URL 요청을 가져와야 합니다. 챗봇이나 다른 애플리케이션을 사용하는 경우, 챗봇이 Traffic Manager에 대한 호출에서 지역을 모방해야 합니다. |
    |-RelativeDnsName|luis-dns-westus|서비스의 하위 도메인(luis-dns-westus.trafficmanager.net)|
    |-Ttl|30|폴링 간격(30초)|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|LUIS에 대한 포트 및 프로토콜은 HTTPS/443입니다.|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west`|`<appId>` 및 `<subscriptionKey>`를 사용자 고유의 값으로 바꿉니다. 이 엔드포인트 키는 동부 엔드포인트 키와 다릅니다.|

    성공한 요청에는 응답이 없습니다.

2. **[AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** cmdlet을 사용 하 여 미국 서 부 끝점 추가

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-west-endpoint -TrafficManagerProfile $westprofile -Type ExternalEndpoints -Target westus.api.cognitive.microsoft.com -EndpointLocation "westus" -EndpointStatus Enabled
    ```

    다음 표에서는 cmdlet의 각 변수에 대해 설명합니다.

    |구성 매개 변수|변수 이름 또는 값|목적|
    |--|--|--|
    |-EndpointName|luis-west-endpoint|프로필 아래에 표시되는 엔드포인트 이름|
    |-TrafficManagerProfile|$westprofile|1단계에서 만든 프로필 개체 사용|
    |-Type|ExternalEndpoints|자세한 내용은 [Traffic Manager 엔드포인트][traffic-manager-endpoints]를 참조하세요. |
    |-Target|westus.api.cognitive.microsoft.com|LUIS 엔드포인트의 도메인입니다.|
    |-EndpointLocation|“westus”|엔드포인트의 지역|
    |-EndpointStatus|사용|엔드포인트를 만들 때 사용하도록 설정|

    성공한 응답은 다음과 같습니다.

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-westus
    Name                             : luis-profile-westus
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-westus
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /luis/v2.0/apps/c3fc5d1e-5187-40cc-af0f-fbde328aa16b?subscription-key=e3605f07e3cc4bedb7e02698a54c19cc&q=traffic-manager-west
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {luis-west-endpoint}
    ```

3. **[AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)** cmdlet을 사용 하 여 미국 서 부 끝점 설정

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $westprofile
    ```

    성공한 응답은 2단계의 응답과 동일합니다.

### <a name="create-parent-traffic-manager-profile"></a>부모 Traffic Manager 프로필 만들기
부모 Traffic Manager 프로필을 만들고 두 개의 자식 Traffic Manager 프로필을 부모에 연결합니다.

1. **[AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)** cmdlet을 사용 하 여 부모 프로필 만들기

    ```powerShell
    $parentprofile = New-AzTrafficManagerProfile -Name luis-profile-parent -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-parent -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/"
    ```

    다음 표에서는 cmdlet의 각 변수에 대해 설명합니다.

    |구성 매개 변수|변수 이름 또는 값|목적|
    |--|--|--|
    |-Name|luis-profile-parent|Azure Portal의 Traffic Manager 이름|
    |-ResourceGroupName|luis-traffic-manager|이전 섹션에서 만든 리소스 그룹 이름|
    |-TrafficRoutingMethod|성능|자세한 내용은 [Traffic Manager 라우팅 방법][routing-methods]을 참조하세요. 성능을 사용하는 경우, 사용자의 지역에서 Traffic Manager에 대한 URL 요청을 가져와야 합니다. 챗봇이나 다른 애플리케이션을 사용하는 경우, 챗봇이 Traffic Manager에 대한 호출에서 지역을 모방해야 합니다. |
    |-RelativeDnsName|luis-dns-parent|서비스의 하위 도메인(luis-dns-parent.trafficmanager.net)|
    |-Ttl|30|폴링 간격(30초)|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|LUIS에 대한 포트 및 프로토콜은 HTTPS/443입니다.|
    |-MonitorPath|`/`|자식 엔드포인트 경로가 대신 사용되므로 이 경로는 중요하지 않습니다.|

    성공한 요청에는 응답이 없습니다.

2. **[AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** 및 **유형인** type을 사용 하 여 부모에 미국 동부 자식 프로필 추가

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-useast -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $eastprofile.Id -EndpointStatus Enabled -EndpointLocation "eastus" -MinChildEndpoints 1
    ```

    다음 표에서는 cmdlet의 각 변수에 대해 설명합니다.

    |구성 매개 변수|변수 이름 또는 값|목적|
    |--|--|--|
    |-EndpointName|child-endpoint-useast|East 프로필|
    |-TrafficManagerProfile|$parentprofile|이 엔드포인트를 할당할 프로필|
    |-Type|NestedEndpoints|자세한 내용은 [AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig)를 참조 하세요. |
    |-TargetResourceId|$eastprofile.Id|자식 프로필의 ID|
    |-EndpointStatus|사용|부모에 추가한 후 엔드포인트 상태|
    |-EndpointLocation|“eastus”|리소스의 [Azure 지역 이름](https://azure.microsoft.com/global-infrastructure/regions/)|
    |-MinChildEndpoints|1|최소 자식 엔드포인트 수|

    성공한 응답은 다음과 같으며 새 `child-endpoint-useast` 엔드포인트를 포함합니다.

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-parent
    Name                             : luis-profile-parent
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-parent
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {child-endpoint-useast}
    ```

3. **[AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** Cmdlet 및 **유형인** type을 사용 하 여 미국 서 부 자식 프로필을 부모에 추가

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-uswest -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $westprofile.Id -EndpointStatus Enabled -EndpointLocation "westus" -MinChildEndpoints 1
    ```

    다음 표에서는 cmdlet의 각 변수에 대해 설명합니다.

    |구성 매개 변수|변수 이름 또는 값|목적|
    |--|--|--|
    |-EndpointName|child-endpoint-uswest|West 프로필|
    |-TrafficManagerProfile|$parentprofile|이 엔드포인트를 할당할 프로필|
    |-Type|NestedEndpoints|자세한 내용은 [AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig)를 참조 하세요. |
    |-TargetResourceId|$westprofile.Id|자식 프로필의 ID|
    |-EndpointStatus|사용|부모에 추가한 후 엔드포인트 상태|
    |-EndpointLocation|“westus”|리소스의 [Azure 지역 이름](https://azure.microsoft.com/global-infrastructure/regions/)|
    |-MinChildEndpoints|1|최소 자식 엔드포인트 수|

    성공한 응답은 다음과 같으며 이전 `child-endpoint-useast` 엔드포인트 및 새 `child-endpoint-uswest` 엔드포인트를 모두 포함합니다.

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-parent
    Name                             : luis-profile-parent
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-parent
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {child-endpoint-useast, child-endpoint-uswest}
    ```

4. **[AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)** cmdlet을 사용 하 여 끝점 설정

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $parentprofile
    ```

    성공한 응답은 3단계의 응답과 동일합니다.

### <a name="powershell-variables"></a>PowerShell 변수
이전 섹션에서 세 개의 PowerShell 변수 `$eastprofile`, `$westprofile`, `$parentprofile`을 만들었습니다. 이러한 변수는 Traffic Manager 구성의 끝 부분에서 사용됩니다. 변수를 만들지 않거나 잊어버린 경우 또는 PowerShell 창 시간이 초과 되는 경우 PowerShell cmdlet **[AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile)** 을 사용 하 여 프로필을 다시 가져와 변수에 할당할 수 있습니다.

꺾쇠 괄호(`<>`)로 묶인 항목을 필요한 세 개의 프로필 각각에 올바른 값으로 바꾸세요.

```powerShell
$<variable-name> = Get-AzTrafficManagerProfile -Name <profile-name> -ResourceGroupName luis-traffic-manager
```

## <a name="verify-traffic-manager-works"></a>Traffic Manager 작동 확인
Traffic Manager 프로필이 작동하는지 확인하려면 프로필이 `Online` 상태여야 합니다. 이 상태는 엔드포인트의 폴링 경로를 기반으로 합니다.

### <a name="view-new-profiles-in-the-azure-portal"></a>Azure Portal에서 새 프로필 보기
`luis-traffic-manager` 리소스 그룹에서 리소스를 확인하여 세 개의 프로필이 모두 만들어졌는지 확인할 수 있습니다.

![Azure 리소스 그룹 luis-traffic-manager의 스크린샷](./media/traffic-manager/traffic-manager-profiles.png)

### <a name="verify-the-profile-status-is-online"></a>프로필 상태가 온라인인지 확인
Traffic Manager는 각 엔드포인트의 경로를 폴링하여 온라인 상태인지 확인합니다. 온라인 상태인 경우 자식 프로필의 상태가 `Online`입니다. 이 상태는 각 프로필의 **개요**에 표시됩니다.

![모니터 상태가 온라인임을 보여 주는 Azure Traffic Manager 프로필 개요의 스크린샷](./media/traffic-manager/profile-status-online.png)

### <a name="validate-traffic-manager-polling-works"></a>Traffic Manager 폴링 작업 유효성 검사
Traffic Manager 폴링 작업의 유효성을 검사하는 또 다른 방법은 LUIS 엔드포인트 로그를 사용하는 것입니다. [LUIS][LUIS] 웹 사이트 앱 목록 페이지에서 애플리케이션에 대한 엔드포인트 로그를 내보냅니다. Traffic Manager는 두 개의 엔드포인트에 대해 자주 폴링하므로 몇 분 안에도 로그에 항목이 생성될 수 있습니다. 쿼리가 `traffic-manager-`로 시작하는 항목을 찾아야 합니다.

```console
traffic-manager-west    6/7/2018 19:19  {"query":"traffic-manager-west","intents":[{"intent":"None","score":0.944767}],"entities":[]}
traffic-manager-east    6/7/2018 19:20  {"query":"traffic-manager-east","intents":[{"intent":"None","score":0.944767}],"entities":[]}
```

### <a name="validate-dns-response-from-traffic-manager-works"></a>Traffic Manager 작업에서 DNS 응답의 유효성 검사
DNS 응답에서 LUIS 엔드포인트를 반환하는지 확인하려면 DNS 클라이언트 라이브러리를 사용하여 Traffic Manager 부모 프로필을 요청합니다. 부모 프로필의 DNS 이름은 `luis-dns-parent.trafficmanager.net`입니다.

다음 Node.js 코드는 부모 프로필을 요청하고 LUIS 엔드포인트를 반환합니다.

```javascript
const dns = require('dns');

dns.resolveAny('luis-dns-parent.trafficmanager.net', (err, ret) => {
  console.log('ret', ret);
});
```

LUIS 엔드포인트가 있는 성공한 응답은 다음과 같습니다.

```json
[
    {
        value: 'westus.api.cognitive.microsoft.com',
        type: 'CNAME'
    }
]
```

## <a name="use-the-traffic-manager-parent-profile"></a>Traffic Manager 부모 프로필 사용
엔드포인트 간의 트래픽을 관리하려면 Traffic Manager DNS에 대한 호출을 삽입하여 LUIS 엔드포인트를 찾아야 합니다. 이 호출은 모든 LUIS 엔드포인트 요청에 대해 수행되고 LUIS 클라이언트 애플리케이션 사용자의 지리적 위치를 시뮬레이션해야 합니다. LUIS 클라이언트 애플리케이션과 엔드포인트 예측을 위한 LUIS 요청 간에 DNS 응답 코드를 추가합니다.

## <a name="resolving-a-degraded-state"></a>성능 저하 상태 문제 해결

Traffic Manager에 대해 [진단 로그](../../traffic-manager/traffic-manager-diagnostic-logs.md)를 사용하도록 설정하여 엔드포인트의 성능 상태가 저하된 이유를 확인합니다.

## <a name="clean-up"></a>정리
두 개의 LUIS 엔드포인트 키, 세 개의 Traffic Manager 프로필과 이러한 5개의 리소스가 포함된 리소스 그룹을 제거합니다. 이 작업은 Azure Portal에서 수행합니다. 리소스 목록에서 5개의 리소스를 삭제합니다. 그런 다음, 리소스 그룹을 삭제합니다.

## <a name="next-steps"></a>다음 단계

BotFramework 봇에 이 트래픽 관리 코드를 추가하는 방법을 이해하려면 BotFramework v4의 [middleware](https://docs.microsoft.com/azure/bot-service/bot-builder-create-middleware?view=azure-bot-service-4.0&tabs=csaddmiddleware%2Ccsetagoverwrite%2Ccsmiddlewareshortcircuit%2Ccsfallback%2Ccsactivityhandler)(미들웨어) 옵션을 검토하세요.

[traffic-manager-marketing]: https://azure.microsoft.com/services/traffic-manager/
[traffic-manager-docs]: https://docs.microsoft.com/azure/traffic-manager/
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[azure-portal]: https://portal.azure.com/
[azure-storage]: https://azure.microsoft.com/services/storage/
[routing-methods]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods
[traffic-manager-endpoints]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-endpoint-types
