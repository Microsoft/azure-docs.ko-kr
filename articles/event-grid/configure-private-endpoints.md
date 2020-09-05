---
title: Azure Event Grid 토픽 또는 도메인에 대 한 개인 끝점 구성
description: 이 문서에서는 Azure Event Grid 토픽 또는 도메인에 대 한 개인 끝점을 구성 하는 방법을 설명 합니다.
ms.topic: how-to
ms.date: 07/07/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: fa67ba8dbe8106c0311bafec07a1510ca0c25c3f
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88508841"
---
# <a name="configure-private-endpoints-for-azure-event-grid-topics-or-domains"></a>Azure Event Grid 토픽 또는 도메인에 대 한 개인 끝점 구성
[개인 끝점](../private-link/private-endpoint-overview.md) 을 사용 하 여 공용 인터넷을 통하지 않고 [개인 링크](../private-link/private-link-overview.md) 를 통해 안전 하 게 항목 및 도메인에 대 한 가상 네트워크에서 직접 이벤트를 수신 하도록 허용할 수 있습니다. 개인 끝점은 토픽 또는 도메인에 대 한 VNet 주소 공간의 IP 주소를 사용 합니다. 개념에 대 한 자세한 내용은 [네트워크 보안](network-security.md)을 참조 하세요.

이 문서에서는 토픽 또는 도메인에 대 한 개인 끝점을 구성 하는 방법을 설명 합니다.

## <a name="use-azure-portal"></a>Azure Portal 사용 
이 섹션에서는 Azure Portal를 사용 하 여 토픽 또는 도메인에 대 한 개인 끝점을 만드는 방법을 보여 줍니다.

> [!NOTE]
> 이 섹션에 표시 된 단계는 대부분 항목에 해당 합니다. 비슷한 단계를 사용 하 여 **도메인**에 대 한 개인 끝점을 만들 수 있습니다. 

1. [Azure Portal](https://portal.azure.com) 에 로그인 하 고 토픽 또는 도메인으로 이동 합니다.
2. 항목 페이지의 **네트워킹** 탭으로 전환 합니다. 도구 모음에서 **+ 개인 끝점** 을 선택 합니다.

    ![개인 끝점 추가](./media/configure-private-endpoints/add-button.png)
2. **기본 사항** 페이지에서 다음 단계를 수행 합니다. 
    1. 개인 끝점을 만들 **Azure 구독** 을 선택 합니다. 
    2. 개인 끝점에 대 한 **Azure 리소스 그룹** 을 선택 합니다. 
    3. 끝점의 **이름을** 입력 합니다. 
    4. 끝점의 **지역을** 선택 합니다. 개인 끝점은 가상 네트워크와 동일한 지역에 있어야 하지만 개인 링크 리소스 (이 예제에서는 event grid 토픽)의 다른 지역에 있을 수 있습니다. 
    5. 그런 다음 페이지 아래쪽에서 **다음: 리소스 >** 단추를 선택 합니다. 

      ![개인 끝점-기본 페이지](./media/configure-private-endpoints/basics-page.png)
3. **리소스** 페이지에서 다음 단계를 수행합니다. 
    1. 연결 방법의 경우 **내 디렉터리에서 Azure 리소스에 연결**을 선택 하는 경우 다음 단계를 수행 합니다. 이 예제에서는 디렉터리의 Azure 리소스에 연결 하는 방법을 보여 줍니다. 
        1. **토픽/도메인이** 존재 하는 **Azure 구독** 을 선택 합니다. 
        1. **리소스 유형**에서 **리소스 종류**에 대 한 **microsoft Eventgrid/토픽** 또는 **microsoft eventgrid/도메인** 을 선택 합니다.
        2. **리소스**의 경우 드롭다운 목록에서 토픽/도메인을 선택 합니다. 
        3. 선택한 리소스 종류에 따라 **대상 하위 리소스가** **토픽** 또는 **도메인** 으로 설정 되어 있는지 확인 합니다.    
        4. 완료되면 **다음: 구성 >** 단추를 페이지 아래쪽에서 선택합니다. 

            ![개인 끝점-리소스 페이지](./media/configure-private-endpoints/resource-page.png)
    2. **리소스 ID 또는 별칭을 사용 하 여 리소스에 연결**을 선택 하는 경우 다음 단계를 수행 합니다.
        1. 리소스의 ID를 입력 합니다. 예: `/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>`  
        2. **리소스**에 대해 **토픽** 또는 **도메인**을 입력 합니다. 
        3. 필드 요청 메시지를 추가 합니다. 
        4. 완료되면 **다음: 구성 >** 단추를 페이지 아래쪽에서 선택합니다. 

            ![개인 끝점-리소스 페이지](./media/configure-private-endpoints/connect-azure-resource-id.png)
4. **구성** 페이지에서 프라이빗 엔드포인트를 배포하려는 가상 네트워크의 서브넷을 선택합니다. 
    1. **가상 네트워크**를 선택합니다. 드롭다운 목록에 현재 선택한 구독 및 위치의 가상 네트워크만 나열됩니다. 
    2. 선택한 가상 네트워크의 **서브넷**을 선택합니다. 
    3. 완료되면 **다음: 태그 >** 단추를 페이지의 아래쪽에서 선택합니다. 

    ![개인 끝점-구성 페이지](./media/configure-private-endpoints/configuration-page.png)
5. **태그** 페이지에서 프라이빗 엔드포인트 리소스와 연결하려는 태그(이름 및 값)를 만듭니다. 그런 다음 페이지 아래쪽에서 **검토 + 만들기** 단추를 선택합니다. 
6. **검토 + 만들기**에서 모든 설정을 검토하고 **만들기**를 선택하여 프라이빗 엔드포인트를 만듭니다. 

    ![개인 끝점-검토 & 만들기 페이지](./media/configure-private-endpoints/review-create-page.png)
    

### <a name="manage-private-link-connection"></a>프라이빗 링크 연결 관리

프라이빗 엔드포인트를 만들 때 연결이 승인되어야 합니다. 프라이빗 엔드포인트를 만드는 리소스가 디렉터리에 있는 경우 충분한 사용 권한이 있다면 연결 요청을 승인할 수 있습니다. 다른 디렉터리의 Azure 리소스에 연결하는 경우 해당 리소스의 소유자가 연결 요청을 승인할 때까지 기다려야 합니다.

다음 네 가지 프로비저닝 상태가 있습니다.

| 서비스 작업 | 서비스 소비자 프라이빗 엔드포인트 상태 | Description |
|--|--|--|
| None | Pending | 연결이 수동으로 만들어지며 개인 링크 리소스 소유자의 승인이 보류 중입니다. |
| 승인 | 승인됨 | 연결이 자동 또는 수동으로 승인되었으며, 사용할 준비가 되었습니다. |
| 거부 | 거부됨 | Private Link 리소스 소유자가 연결을 거부했습니다. |
| 제거 | 연결 끊김 | Private Link 리소스 소유자가 연결을 제거했습니다. 프라이빗 엔드포인트는 정보를 제공하므로 정리를 위해 삭제해야 합니다. |
 
###  <a name="how-to-manage-a-private-endpoint-connection"></a>개인 끝점 연결을 관리 하는 방법
다음 섹션에서는 개인 끝점 연결을 승인 하거나 거부 하는 방법을 보여 줍니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 검색 표시줄에 **Event Grid 토픽** 또는 **Event Grid 도메인**을 입력 합니다.
1. 관리 하려는 **토픽** 또는 **도메인** 을 선택 합니다.
1. **네트워킹** 탭을 선택합니다.
1. 보류 중인 연결이 있는 경우 프로 비전 상태에 **보류 중** 으로 표시 된 연결이 표시 됩니다. 

### <a name="to-approve-a-private-endpoint"></a>개인 끝점을 승인 하려면
보류 중 상태인 개인 끝점을 승인할 수 있습니다. 승인 하려면 다음 단계를 수행 합니다. 

> [!NOTE]
> 이 섹션에 표시 된 단계는 대부분 항목에 해당 합니다. 비슷한 단계를 사용 하 여 **도메인**에 대 한 개인 끝점을 승인할 수 있습니다. 

1. 승인 하려는 **개인 끝점** 을 선택 하 고 도구 모음에서 **승인** 을 선택 합니다.

    ![개인 끝점-보류 상태](./media/configure-private-endpoints/pending.png)
1. **연결 승인** 대화 상자에서 설명 (선택 사항)을 입력 하 고 **예**를 선택 합니다. 

    ![개인 끝점-승인](./media/configure-private-endpoints/approve.png)
1. **승인**됨으로 끝점의 상태가 표시 되는지 확인 합니다. 

    ![개인 끝점-승인 된 상태](./media/configure-private-endpoints/approved-status.png)

### <a name="to-reject-a-private-endpoint"></a>개인 끝점을 거부 하려면
보류 중 상태 또는 승인 됨 상태의 개인 끝점을 거부할 수 있습니다. 거부 하려면 다음 단계를 수행 합니다. 

> [!NOTE]
> 이 섹션에 표시 된 단계는 항목에 대 한 것입니다. 비슷한 단계를 사용 하 여 **도메인**에 대 한 개인 끝점을 거부할 수 있습니다. 

1. 거부할 **개인 끝점** 을 선택 하 고 도구 모음에서 **거부** 를 선택 합니다.

    ![개인 끝점-거부](./media/configure-private-endpoints/reject-button.png)
1. **연결 거부** 대화 상자에서 설명 (선택 사항)을 입력 하 고 **예**를 선택 합니다. 

    ![개인 끝점-거부](./media/configure-private-endpoints/reject.png)
1. 끝점의 상태가 **거부**됨으로 표시 되는지 확인 합니다. 

    ![개인 끝점-거부 된 상태](./media/configure-private-endpoints/rejected-status.png)

    > [!NOTE]
    > Azure Portal 거부 되 면 개인 끝점을 승인할 수 없습니다. 


## <a name="use-azure-cli"></a>Azure CLI 사용
개인 끝점을 만들려면 다음 예제와 같이 [az network private-endpoint create](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) 메서드를 사용 합니다.

```azurecli-interactive
az network private-endpoint create \
    --resource-group <RESOURECE GROUP NAME> \
    --name <PRIVATE ENDPOINT NAME> \
    --vnet-name <VIRTUAL NETWORK NAME> \
    --subnet <SUBNET NAME> \
    --private-connection-resource-id "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME> \
    --connection-name <PRIVATE LINK SERVICE CONNECTION NAME> \
    --location <LOCATION> \
    --group-ids topic
```

예제에 사용 된 매개 변수에 대 한 설명은 [az network private endpoint create](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create)에 대 한 설명서를 참조 하세요. 이 예에서는 다음과 같은 몇 가지 사항을 주의 해야 합니다. 

- `private-connection-resource-id`에서 **토픽** 또는 **도메인**의 리소스 ID를 지정 합니다. 앞의 예제에서는 type: 토픽을 사용 합니다.
- 에 대해 `group-ids` 또는를 지정 `topic` `domain` 합니다. 위의 예에서는 `topic` 가 사용 됩니다. 

개인 끝점을 삭제 하려면 다음 예제와 같이 [az network private-endpoint delete](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-delete) 메서드를 사용 합니다.

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> 이 섹션에 표시 된 단계는 항목에 대 한 것입니다. 비슷한 단계를 사용 하 여 **도메인**에 대 한 개인 끝점을 만들 수 있습니다. 



### <a name="prerequisites"></a>사전 요구 사항
다음 명령을 실행 하 여 CLI의 Azure Event Grid 확장을 업데이트 합니다. 

```azurecli-interactive
az extension update -n eventgrid
```

확장이 설치 되지 않은 경우 다음 명령을 실행 하 여 설치 합니다. 

```azurecli-interactive
az extension add -n eventgrid
```

### <a name="create-a-private-endpoint"></a>프라이빗 엔드포인트 만들기
개인 끝점을 만들려면 다음 예제와 같이 [az network private-endpoint create](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) 메서드를 사용 합니다.

```azurecli-interactive
az network private-endpoint create \
    --resource-group <RESOURECE GROUP NAME> \
    --name <PRIVATE ENDPOINT NAME> \
    --vnet-name <VIRTUAL NETWORK NAME> \
    --subnet <SUBNET NAME> \
    --private-connection-resource-id "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME> \
    --connection-name <PRIVATE LINK SERVICE CONNECTION NAME> \
    --location <LOCATION> \
    --group-ids topic
```

예제에 사용 된 매개 변수에 대 한 설명은 [az network private endpoint create](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create)에 대 한 설명서를 참조 하세요. 이 예에서는 다음과 같은 몇 가지 사항을 주의 해야 합니다. 

- `private-connection-resource-id`에서 **토픽** 또는 **도메인**의 리소스 ID를 지정 합니다. 앞의 예제에서는 type: 토픽을 사용 합니다.
- 에 대해 `group-ids` 또는를 지정 `topic` `domain` 합니다. 위의 예에서는 `topic` 가 사용 됩니다. 

개인 끝점을 삭제 하려면 다음 예제와 같이 [az network private-endpoint delete](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-delete) 메서드를 사용 합니다.

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> 이 섹션에 표시 된 단계는 항목에 대 한 것입니다. 비슷한 단계를 사용 하 여 **도메인**에 대 한 개인 끝점을 만들 수 있습니다. 

#### <a name="sample-script"></a>샘플 스크립트
다음은 Azure 리소스를 만드는 샘플 스크립트입니다.

- Resource group
- 가상 네트워크
- 가상 네트워크의 서브넷
- Azure Event Grid 항목
- 항목의 개인 끝점

> [!NOTE]
> 이 섹션에 표시 된 단계는 항목에 대 한 것입니다. 비슷한 단계를 사용 하 여 도메인에 대 한 개인 끝점을 만들 수 있습니다.

```azurecli-interactive
subscriptionID="<AZURE SUBSCRIPTION ID>"
resourceGroupName="<RESOURCE GROUP NAME>"
location="<LOCATION>"
vNetName="<VIRTUAL NETWORK NAME>"
subNetName="<SUBNET NAME>"
topicName = "<TOPIC NAME>"
connectionName="<ENDPOINT CONNECTION NAME>"
endpointName=<ENDPOINT NAME>

# resource ID of the topic. replace <SUBSCRIPTION ID>, <RESOURCE GROUP NAME>, and <TOPIC NAME>
topicResourceID="/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME>"

# select subscription
az account set --subscription $subscriptionID

# create resource group
az group create --name $resourceGroupName --location $location

# create vnet 
az network vnet create \
    --resource-group $resourceGroupName \
    --name $vNetName \
    --address-prefix 10.0.0.0/16

# create subnet
az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --address-prefixes 10.0.0.0/24

# disable private endpoint network policies for the subnet
az network vnet subnet update \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --disable-private-endpoint-network-policies true

# create event grid topic. update <LOCATION>
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location

# verify that the topic was created.
az eventgrid topic show \
    --resource-group $resourceGroupName \
    --name $topicName

# create private endpoint for the topic you created
az network private-endpoint create 
    --resource-group $resourceGroupName \
    --name $endpointName \
    --vnet-name $vNetName \
    --subnet $subNetName \
    --private-connection-resource-id $topicResourceID \
    --connection-name $connectionName \
    --location $location \
    --group-ids topic

# get topic 
az eventgrid topic show \
    --resource-group $resourceGroupName \
    --name $topicName

```

### <a name="approve-a-private-endpoint"></a>개인 끝점 승인
다음 샘플 CLI 코드 조각은 개인 끝점 연결을 승인 하는 방법을 보여 줍니다. 

```azurecli-interactive
az eventgrid topic private-endpoint-connection approve \
    --resource-group $resourceGroupName \
    --topic-name $topicName \
    --name  $endpointName \
    --description "connection approved"
```


### <a name="reject-a-private-endpoint"></a>개인 끝점 거부
다음 샘플 CLI 코드 조각은 개인 끝점 연결을 거부 하는 방법을 보여 줍니다. 

```azurecli-interactive
az eventgrid topic private-endpoint-connection reject \
    --resource-group $resourceGroupName \
    --topic-name $topicName \
    --name $endpointName \
    --description "Connection rejected"
```

### <a name="disable-public-network-access"></a>공용 네트워크 액세스 사용 안 함
기본적으로 공용 네트워크 액세스는 Event Grid 토픽 또는 도메인에 대해 사용 하도록 설정 됩니다. 전용 엔드포인트를 통해 액세스를 허용 하려면 다음 명령을 실행 하 여 공용 네트워크 액세스를 사용 하지 않도록 설정 합니다.  

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access disabled
```


## <a name="use-powershell"></a>PowerShell 사용
이 섹션에서는 PowerShell을 사용 하 여 토픽 또는 도메인에 대 한 개인 끝점을 만드는 방법을 보여 줍니다. 

### <a name="prerequisite"></a>필수 조건
[방법: 포털을 사용 하 여 리소스에 액세스할 수 있는 AZURE AD 응용 프로그램 및 서비스 주체 만들기를 사용](../active-directory/develop/howto-create-service-principal-portal.md) 하 여 Azure Active Directory 응용 프로그램을 만들고 **디렉터리 (테 넌 트) id**, **응용 프로그램 (클라이언트) id**및 **응용 프로그램 (클라이언트) 암호**에 대 한 값을 적어 둡니다. 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>REST API 호출에 대 한 토큰 및 헤더 준비 
REST API 호출 및 권한 부여 및 기타 헤더 정보와 함께 사용할 인증 토큰을 가져오려면 다음 필수 구성 요소 명령을 실행 합니다. 

```azurepowershell-interactive
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get authentication token
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token  `
    -Body $body  `
    -ContentType 'application/x-www-form-urlencoded' 

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
```

### <a name="create-a-subnet-with-endpoint-network-policies-disabled"></a>끝점 네트워크 정책을 사용 하지 않도록 설정 하 여 서브넷 만들기

```azurepowershell-interactive

# create resource group
New-AzResourceGroup -ResourceGroupName <RESOURCE GROUP NAME>  -Location <LOCATION>

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName <RESOURCE GROUP NAME> `
                    -Location <LOCATION> `
                    -Name <VIRTUAL NETWORK NAME> `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name example-privatelinksubnet `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork
```

### <a name="create-an-event-grid-topic-with-a-private-endpoint"></a>개인 끝점을 사용 하 여 event grid 토픽 만들기

> [!NOTE]
> 이 섹션에 표시 된 단계는 항목에 대 한 것입니다. 비슷한 단계를 사용 하 여 **도메인**에 대 한 개인 끝점을 만들 수 있습니다. 


```azurepowershell-interactive
$body = @{"location"="<LOCATION>"; "properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json

# create topic
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $body

# verify that the topic was created
$topic=Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01"   `
    -Headers $Headers  

# create private link service connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name "<PRIVATE LINK SERVICE CONNECTION NAME>" `
                                -PrivateLinkServiceId $topic.Id `
                                -GroupId "topic"

# get virtual network info 
$virtualNetwork = Get-AzVirtualNetwork  `
                    -ResourceGroupName  <RESOURCE GROUP NAME> `
                    -Name <VIRTUAL NETWORK NAME>

# get subnet info
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                             | Where-Object  {$_.Name -eq <SUBNET NAME>}  

# now, you are ready to create a private endpoint 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName <RESOURCE GROUP NAME>  `
                                        -Name <PRIVATE ENDPOINT NAME>   `
                                        -Location <LOCATION> `
                                        -Subnet  $subnet   `
                                        -PrivateLinkServiceConnection $privateEndpointConnection

# verify that the endpoint was created
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections?api-version=2020-06-01"   `
    -Headers $Headers   `
    | ConvertTo-Json -Depth 5

```

끝점이 생성 되었는지 확인 하는 경우 다음과 유사한 결과가 표시 됩니다.

```json

{
  "value": [
    {
      "properties": {
        "privateEndpoint": {
          "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Network/privateEndpoints/<PRIVATE ENDPOINT NAME>"
        },
        "groupIds": [
          "topic"
        ],
        "privateLinkServiceConnectionState": {
          "status": "Approved",
          "description": "Auto-approved",
          "actionsRequired": "None"
        },
        "provisioningState": "Succeeded"
      },
      "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>",
      "name": "myConnection",
      "type": "Microsoft.EventGrid/topics/privateEndpointConnections"
    }
  ]
}
```

### <a name="approve-a-private-endpoint-connection"></a>프라이빗 엔드포인트 연결 승인
다음 샘플 PowerShell 코드 조각은 개인 끝점을 승인 하는 방법을 보여 줍니다. 

> [!NOTE]
> 이 섹션에 표시 된 단계는 항목에 대 한 것입니다. 비슷한 단계를 사용 하 여 **도메인**에 대 한 개인 끝점을 승인할 수 있습니다. 

```azurepowershell-interactive
$approvedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="approved";"description"="connection approved";"actionsRequired"="none"}}} | ConvertTo-Json

# approve endpoint connection
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AzuRE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $approvedBody

# confirm that the endpoint connection was approved
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers

```

### <a name="reject-a-private-endpoint-connection"></a>프라이빗 엔드포인트 연결 거부
다음 예제에서는 PowerShell을 사용 하 여 개인 끝점을 거부 하는 방법을 보여 줍니다. 이전 GET 명령의 결과에서 개인 끝점에 대 한 GUID를 가져올 수 있습니다. 

> [!NOTE]
> 이 섹션에 표시 된 단계는 항목에 대 한 것입니다. 비슷한 단계를 사용 하 여 **도메인**에 대 한 개인 끝점을 거부할 수 있습니다. 


```azurepowershell-interactive
$rejectedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="rejected";"description"="connection rejected";"actionsRequired"="none"}}} | ConvertTo-Json

# reject private endpoint
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $rejectedBody

# confirm that endpoint was rejected
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01" ` 
    -Headers $Headers
```

API를 통해 거부 된 후에도 연결을 승인할 수 있습니다. Azure Portal 사용 하는 경우 거부 된 끝점은 승인할 수 없습니다. 

## <a name="next-steps"></a>다음 단계
* IP 방화벽 설정을 구성 하는 방법에 대 한 자세한 내용은 [Azure Event Grid 토픽 또는 도메인에 대 한 ip 방화벽 구성](configure-firewall.md)을 참조 하세요.
* 네트워크 연결 문제를 해결 하려면 [네트워크 연결 문제 해결](troubleshoot-network-connectivity.md) 을 참조 하세요.
