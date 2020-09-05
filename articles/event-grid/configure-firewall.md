---
title: Azure Event Grid 토픽 또는 도메인에 대 한 IP 방화벽 구성
description: 이 문서에서는 Event Grid 토픽 또는 도메인에 대 한 방화벽 설정을 구성 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: df6098df4817ee6c47378704c25d07433d6b9480
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88509421"
---
# <a name="configure-ip-firewall-for-azure-event-grid-topics-or-domains"></a>Azure Event Grid 토픽 또는 도메인에 대 한 IP 방화벽 구성 
기본적으로 요청에 유효한 인증 및 권한 부여가 제공 되는 한, 항목 및 도메인은 인터넷에서 액세스할 수 있습니다. IP 방화벽을 사용하면 [CIDR(Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 표기법으로 IPv4 주소 또는 IPv4 주소 범위 세트로만 제한할 수 있습니다. 다른 IP 주소에서 시작 된 게시자는 거부 되며 403 (사용할 수 없음) 응답을 받게 됩니다. Event Grid에서 지 원하는 네트워크 보안 기능에 대 한 자세한 내용은 [Event Grid 네트워크 보안](network-security.md)을 참조 하세요.

이 문서에서는 Azure Event Grid 토픽 또는 도메인에 대 한 IP 방화벽 설정을 구성 하는 방법을 설명 합니다.

## <a name="use-azure-portal"></a>Azure Portal 사용
이 섹션에서는 Azure Portal를 사용 하 여 인바운드 IP 방화벽 규칙을 만드는 방법을 보여 줍니다. 이 섹션에 표시 된 단계는 항목에 대 한 것입니다. 비슷한 단계를 사용 하 여 **도메인**에 대 한 인바운드 IP 규칙을 만들 수 있습니다. 

1. [Azure Portal](https://portal.azure.com)에서 event grid 토픽 또는 도메인으로 이동 하 고 **네트워킹** 탭으로 전환 합니다.
2. 인터넷을 포함 한 모든 네트워크에서 리소스에 액세스할 수 있도록 **공용 네트워크** 를 선택 합니다. 

    IP 기반 방화벽 규칙을 사용 하 여 트래픽을 제한할 수 있습니다. 클래스 없는 CIDR (도메인 간 라우팅) 표기법에서 단일 IPv4 주소 또는 IP 주소의 범위를 지정 합니다. 

    ![공용 네트워크 페이지](./media/configure-firewall/public-networks-page.png)
3. 개인 끝점 연결만이 리소스에 액세스할 수 있도록 하려면 **전용 끝점을 선택 합니다** . 이 페이지의 **개인 끝점 연결** 탭을 사용 하 여 연결을 관리할 수 있습니다. 

    ![공용 네트워크 페이지](./media/configure-firewall/private-endpoints-page.png)
4. 도구 모음에서 **저장**을 선택합니다. 



## <a name="use-azure-cli"></a>Azure CLI 사용
이 섹션에서는 Azure CLI 명령을 사용 하 여 인바운드 IP 규칙을 사용 하 여 항목을 만드는 방법을 보여 줍니다. 이 섹션에 표시 된 단계는 항목에 대 한 것입니다. 비슷한 단계를 사용 하 여 **도메인**에 대 한 인바운드 IP 규칙을 만들 수 있습니다. 


### <a name="prerequisites"></a>사전 요구 사항
다음 명령을 실행 하 여 CLI의 Azure Event Grid 확장을 업데이트 합니다. 

```azurecli-interactive
az extension update -n eventgrid
```

확장이 설치 되지 않은 경우 다음 명령을 실행 하 여 설치 합니다. 

```azurecli-interactive
az extension add -n eventgrid
```

### <a name="enable-or-disable-public-network-access"></a>공용 네트워크 액세스 사용 또는 사용 안 함
기본적으로 토픽 및 도메인에 대해 공용 네트워크 액세스를 사용할 수 있습니다. 명시적으로 사용 하도록 설정 하거나 사용 하지 않도록 설정할 수도 있습니다. 인바운드 IP 방화벽 규칙을 구성 하 여 트래픽을 제한할 수 있습니다. 

#### <a name="enable-public-network-access-while-creating-a-topic"></a>토픽을 만드는 동안 공용 네트워크 액세스 사용

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access enabled
```


#### <a name="disable-public-network-access-while-creating-a-topic"></a>토픽을 만드는 동안 공용 네트워크 액세스 사용 안 함

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access disabled
```

> [!NOTE]
> 토픽 또는 도메인에 대해 공용 네트워크 액세스를 사용 하지 않도록 설정 하면 공용 인터넷을 통한 트래픽이 허용 되지 않습니다. 개인 끝점 연결만 이러한 리소스에 액세스할 수 있습니다. 


#### <a name="enable-public-network-access-for-an-existing-topic"></a>기존 항목에 대 한 공용 네트워크 액세스 사용

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled 
```

#### <a name="disable-public-network-access-for-an-existing-topic"></a>기존 항목에 대 한 공용 네트워크 액세스 사용 안 함 

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access disabled
```

### <a name="create-a-topic-with-single-inbound-ip-rule"></a>단일 인바운드 ip 규칙을 사용 하 여 토픽 만들기
다음 샘플 CLI 명령은 인바운드 IP 규칙을 사용 하 여 event grid 토픽을 만듭니다. 

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR or CIDR MASK> allow 
```

### <a name="create-a-topic-with-multiple-inbound-ip-rules"></a>여러 인바운드 ip 규칙을 사용 하 여 토픽 만들기

다음 샘플 CLI 명령은 한 단계로 event grid 토픽 두 인바운드 IP 규칙을 만듭니다. 

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR 1 or CIDR MASK 1> allow \
    --inbound-ip-rules <IP ADDR 2 or CIDR MASK 2> allow
```

### <a name="update-an-existing-topic-to-add-inbound-ip-rules"></a>기존 항목을 업데이트 하 여 인바운드 IP 규칙 추가
이 예에서는 먼저 event grid 토픽을 만든 다음 개별 명령에 항목에 대 한 인바운드 IP 규칙을 추가 합니다. 또한 두 번째 명령에 설정 된 인바운드 IP 규칙도 업데이트 합니다. 

```azurecli-interactive

# create the event grid topic first
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location

# add inbound IP rules to an existing topic
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR or CIDR MASK> allow

# later, update topic with additional ip rules
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR 1 or CIDR MASK 1> allow \
    --inbound-ip-rules <IP ADDR 2 or CIDR MASK 2> allow
```

### <a name="remove-an-inbound-ip-rule"></a>인바운드 IP 규칙 제거
다음 명령은 설정을 업데이트 하는 동안 첫 번째 규칙만 지정 하 여 이전 단계에서 만든 두 번째 규칙을 제거 합니다. 

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR 1 or CIDR MASK 1> allow
```


## <a name="use-powershell"></a>PowerShell 사용
이 섹션에서는 Azure PowerShell 명령을 사용 하 여 인바운드 IP 방화벽 규칙을 사용 하 여 Azure Event Grid 항목을 만드는 방법을 보여 줍니다. 이 섹션에 표시 된 단계는 항목에 대 한 것입니다. 비슷한 단계를 사용 하 여 **도메인**에 대 한 인바운드 IP 규칙을 만들 수 있습니다. 

### <a name="prerequisites"></a>사전 요구 사항
[방법: 포털을 사용 하 여 리소스에 액세스할 수 있는 AZURE AD 응용 프로그램 및 서비스 주체 만들기](../active-directory/develop/howto-create-service-principal-portal.md) 에 대 한 지침을 수행 하 여 Azure Active Directory 응용 프로그램을 만들고 다음 값을 적어둡니다.

- 디렉터리(테넌트) ID
- 응용 프로그램 (클라이언트) ID
- 응용 프로그램 (클라이언트) 암호

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>REST API 호출에 대 한 토큰 및 헤더 준비 
REST API 호출, 권한 부여 및 기타 헤더 정보와 함께 사용할 인증 토큰을 가져오려면 다음 필수 구성 요소 명령을 실행 합니다. 

```azurepowershell-interactive
# replace <CLIENT ID> and <CLIENT SECRET>
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get the authentication token. Replace <TENANT ID>
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token `
    -Body $body `
    -ContentType 'application/x-www-form-urlencoded'

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
$Headers.Add("Content-Type","application/json")
```

### <a name="enable-public-network-access-for-an-existing-topic"></a>기존 항목에 대 한 공용 네트워크 액세스 사용
기본적으로 토픽 및 도메인에 대해 공용 네트워크 액세스를 사용할 수 있습니다. 인바운드 IP 방화벽 규칙을 구성 하 여 트래픽을 제한할 수 있습니다. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="enabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="disable-public-network-access-for-an-existing-topic"></a>기존 항목에 대 한 공용 네트워크 액세스 사용 안 함
토픽 또는 도메인에 대해 공용 네트워크 액세스를 사용 하지 않도록 설정 하면 공용 인터넷을 통한 트래픽이 허용 되지 않습니다. 개인 끝점 연결만 이러한 리소스에 액세스할 수 있습니다. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="create-an-event-grid-topic-with-inbound-rules-in-one-step"></a>한 단계에서 인바운드 규칙을 사용 하 여 event grid 토픽 만들기

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that inbound IP rules are included. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDR or CIDR MASK>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# create the event grid topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5
```


### <a name="create-event-grid-topic-first-and-then-add-inbound-ip-rules"></a>먼저 event grid 토픽을 만든 다음 인바운드 ip 규칙을 추가 합니다.

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that no inbound IP rules are specified. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled";}} | ConvertTo-Json -Depth 5

# create the Event Grid topic
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body`

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

# prepare the body for REST PUT method. Notice that it includes inbound IP rules now. This feature is available in both basic and premium tiers.
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDR or CIDR MASK>";"action"="allow"}, @{"ipmask"="<IP ADDR or CIDR MASK>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# update the topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body

# verify that the topic was updated
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AzURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

```

## <a name="next-steps"></a>다음 단계

* 이벤트 배달 모니터링에 대한 정보는 [Event Grid 메시지 배달 모니터링](monitor-event-delivery.md)을 참조하세요.
* 인증 키에 대한 자세한 내용은 [Event Grid 보안 및 인증](security-authentication.md)을 참조하세요.
* Azure Event Grid 구독을 만드는 방법에 대한 자세한 내용은 [Event Grid 구독 스키마](subscription-creation-schema.md)를 참조하세요.
* 네트워크 연결 문제를 해결 하려면 [네트워크 연결 문제 해결](troubleshoot-network-connectivity.md) 을 참조 하세요.
