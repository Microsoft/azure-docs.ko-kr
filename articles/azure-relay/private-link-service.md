---
title: Azure Private Link 서비스와 Azure Relay 통합
description: Azure Private Link Service와 Azure Relay를 통합하는 방법을 알아봅니다.
ms.date: 06/23/2020
ms.topic: article
ms.openlocfilehash: e5c35f9333378a5f0b87956e8a916491d51e3cb3
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719430"
---
# <a name="integrate-azure-relay-with-azure-private-link-preview"></a>Azure Private Link(미리 보기)와 Azure Relay 통합
Azure **Private Link Service**를 사용하면 가상 네트워크의 프라이빗 엔드포인트를 통해 Azure 서비스(예: Azure Relay, Azure Service Bus, Azure Event Hubs, Azure Storage 및 Azure Cosmos DB)와 Azure 호스팅 고객/파트너 서비스에 액세스할 수 있습니다. 자세한 내용은 [Azure Private Link(미리 보기)란?](../private-link/private-link-overview.md)을 참조하세요.

**프라이빗 엔드포인트**는 가상 네트워크에서 실행 중인 워크로드가 **프라이빗 링크 리소스**가 있는 서비스(예: 릴레이 네임스페이스)에 비공개로 안전하게 연결할 수 있도록 하는 네트워크 인터페이스입니다. 프라이빗 엔드포인트는 VNet의 개인 IP 주소를 사용하여 서비스를 VNet으로 효과적으로 가져옵니다. 서비스에 대한 모든 트래픽은 프라이빗 엔드포인트를 통해 라우팅할 수 있으므로 게이트웨이, NAT 디바이스, ExpressRoute, VPN 연결 또는 공용 IP 주소가 필요하지 않습니다. 가상 네트워크와 서비스 간의 트래픽은 Microsoft 백본 네트워크를 통해 이동하여 공용 인터넷에서 노출을 제거합니다. 특정 Azure Relay 네임스페이스에 대한 연결을 허용하여 액세스 제어 수준을 세분화할 수 있습니다. 


> [!IMPORTANT]
> 이 기능은 현재 **미리 보기**로 제공됩니다. 
>
> 현재 발신자 클라이언트에서 프라이빗 링크 연결을 지원합니다. 


## <a name="add-a-private-endpoint-using-azure-portal"></a>Azure Portal을 사용하여 프라이빗 엔드포인트 추가

### <a name="prerequisites"></a>사전 요구 사항
Azure Relay 네임스페이스를 Azure Private Link(미리 보기)와 통합하려면 다음 엔터티 또는 사용 권한이 필요합니다.

- Azure Relay 네임스페이스
- Azure 가상 네트워크
- 가상 네트워크의 서브넷
- 가상 네트워크에 대한 소유자 또는 기여자 권한

프라이빗 엔드포인트와 가상 네트워크는 동일한 지역에 있어야 합니다. 포털을 사용하여 프라이빗 엔드포인트에 대한 영역을 선택하면 해당 지역에 있는 가상 네트워크만 자동으로 필터링됩니다. 네임스페이스는 다른 지역에 있을 수 있습니다.

프라이빗 엔드포인트는 가상 네트워크에서 개인 IP 주소를 사용합니다.

### <a name="steps"></a>단계
새 Azure Relay 네임스페이스 및 엔터티를 만드는 방법에 대한 단계별 지침은 [Azure Portal을 사용하여 Azure Relay 네임스페이스 만들기](relay-create-namespace-portal.md)를 참조하세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
2. 검색 창에서 **릴레이**를 입력합니다.
3. 목록에서 프라이빗 엔드포인트를 추가하려는 **네임스페이스**를 선택합니다.
4. **설정** 아래에서 **네트워킹** 탭을 선택합니다.
5. 페이지 위쪽에서 **프라이빗 엔드포인트 연결(미리 보기)** 탭을 선택합니다.
6. 페이지 위쪽에서 **+ 프라이빗 엔드포인트** 단추를 선택합니다.

    ![프라이빗 엔드포인트 추가 단추](./media/private-link-service/add-private-endpoint-button.png)
7. **기본** 페이지에서 다음 단계를 수행합니다. 
    1. 프라이빗 엔드포인트를 만들려는 **Azure 구독**을 선택합니다. 
    2. 프라이빗 엔드포인트 리소스에 대한 **리소스 그룹**을 선택합니다.
    3. 프라이빗 엔드포인트에 대한 **이름**을 입력합니다. 
    5. 프라이빗 엔드포인트에 대한 **지역**을 선택합니다. 프라이빗 엔드포인트는 가상 네트워크와 동일한 영역에 있어야 하지만 연결할 Azure Relay 네임스페이스와 다른 영역에 있을 수 있습니다. 
    6. 완료되면 **다음: 리소스 >** 단추를 페이지 아래쪽에서 선택합니다.

        ![프라이빗 엔드포인트 만들기 - 기본 페이지](./media/private-link-service/create-private-endpoint-basics-page.png)
8. **리소스** 페이지에서 다음 단계를 수행합니다.
    1. 연결 방법의 경우 **내 디렉터리에서 Azure 리소스에 연결**을 선택하고, 네임스페이스에 대한 소유자 또는 기여자 액세스 권한이 있으며, 해당 네임스페이스가 프라이빗 엔드포인트와 동일한 디렉터리에 있으면 다음 단계를 수행합니다. 
        1. **Azure Relay 네임스페이스**가 존재하는 **Azure 구독**을 선택합니다. 
        2. **리소스 종류**의 경우 **리소스 종류**에 대한 **Microsoft.Relay/namespaces**를 선택합니다.
        3. **리소스**의 경우 드롭다운 목록에서 Relay 네임스페이스를 선택합니다. 
        4. **대상 하위 리소스**가 **namespace**로 설정되어 있는지 확인합니다.
        5. 완료되면 **다음: 구성 >** 단추를 페이지 아래쪽에서 선택합니다. 
        
            ![프라이빗 엔드포인트 만들기 - 리소스 페이지](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. 네임스페이스가 프라이빗 엔드포인트와 동일한 디렉터리에 있지 않아서 **리소스 ID 또는 별칭으로 Azure 리소스에 연결**을 선택하는 경우 다음 단계를 수행합니다.
        1. **리소스 ID** 또는 **별칭**을 입력합니다. 다른 사람과 공유한 리소스 ID 또는 별칭일 수 있습니다. 리소스 ID를 가져오는 가장 쉬운 방법은 Azure Portal에서 Azure Relay 네임스페이스로 이동하여 `/subscriptions/`부터 URI의 일부를 복사하는 것입니다. 다음 예를 참조하세요. `/subscriptions/000000000-0000-0000-0000-000000000000000/resourceGroups/myresourcegroup/providers/Microsoft.Relay/namespaces/myrelaynamespace.` 
        2. **대상 하위 리소스**에 **namespace**를 입력합니다. 프라이빗 엔드포인트에서 액세스할 수 있는 하위 리소스의 형식입니다.
        3. (선택 사항) **요청 메시지**를 입력합니다. 리소스 소유자는 프라이빗 엔드포인트 연결을 관리하는 동안 이 메시지를 확인합니다.
        4. 그런 다음, **다음: 구성 >** 단추를 페이지 아래쪽에서 선택합니다.

            ![프라이빗 엔드포인트 만들기 - 리소스 ID를 사용하여 연결](./media/private-link-service/connect-resource-id.png)
9. **구성** 페이지에서 프라이빗 엔드포인트를 배포하려는 가상 네트워크의 서브넷을 선택합니다. 
    1. **가상 네트워크**를 선택합니다. 드롭다운 목록에 현재 선택한 구독 및 위치의 가상 네트워크만 나열됩니다. 
    2. 선택한 가상 네트워크의 **서브넷**을 선택합니다. 
    3. 프라이빗 DNS 영역과 프라이빗 엔드포인트를 통합하려는 경우 **프라이빗 DNS 영역과 통합**을 사용하도록 설정합니다. 
    
        프라이빗 엔드포인트와 비공개로 연결하려면 DNS 레코드가 필요합니다. 프라이빗 엔드포인트를 **프라이빗 DNS 영역**과 통합하는 것이 좋습니다. 자체 DNS 서버를 활용하거나 가상 머신의 호스트 파일을 사용하여 DNS 레코드를 만들 수도 있습니다. 자세한 내용은 [Azure 프라이빗 엔드포인트 DNS 구성](../private-link/private-endpoint-dns.md)을 참조하세요. 이 예제에서는 **프라이빗 DNS 영역과 통합** 옵션을 선택하고 프라이빗 DNS 영역을 만듭니다. 
    3. 완료되면 **다음: 태그 >** 단추를 페이지의 아래쪽에서 선택합니다. 

        ![프라이빗 엔드포인트 만들기 - 구성 페이지](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. **태그** 페이지에서 프라이빗 엔드포인트 및 프라이빗 DNS 영역(옵션을 활성화한 경우)과 연결하려는 태그(이름 및 값)를 만듭니다. 그런 다음, **검토 + 만들기** 단추를 페이지 아래쪽에서 선택합니다. 
11. **검토 + 만들기**에서 모든 설정을 검토하고 **만들기**를 선택하여 프라이빗 엔드포인트를 만듭니다.
    
    ![프라이빗 엔드포인트 만들기 - 검토 및 만들기 페이지](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. **프라이빗 엔드포인트** 페이지에서 프라이빗 엔드포인트 연결의 상태를 볼 수 있습니다. 릴레이 네임스페이스의 소유자이거나 이에 대한 액세스를 관리하고 **내 디렉터리에서 Azure 리소스에 연결**을 **연결 방법**에서 선택한 경우 엔드포인트 연결은 **자동 승인**되어야 합니다. **보류 중**인 경우 [Azure Portal을 사용하여 프라이빗 엔드포인트 관리](#manage-private-endpoints-using-azure-portal) 섹션을 참조하세요.

    ![프라이빗 엔드포인트 페이지](./media/private-link-service/private-endpoint-page.png)
13. **네임스페이스**의 **네트워킹** 페이지로 다시 이동하여 **프라이빗 엔드포인트 연결(미리 보기)** 탭으로 전환합니다. 생성된 프라이빗 엔드포인트가 표시됩니다. 

    ![생성된 프라이빗 엔드포인트](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>PowerShell을 사용하여 프라이빗 엔드포인트 추가
다음 예제에서는 Azure PowerShell을 사용하여 Azure Relay에 대한 프라이빗 엔드포인트 연결을 만드는 방법을 보여 줍니다.

프라이빗 엔드포인트와 가상 네트워크는 동일한 지역에 있어야 합니다. Azure Relay 네임스페이스는 다른 지역에 있을 수 있습니다. 또한 프라이빗 엔드포인트는 가상 네트워크에서 개인 IP 주소를 사용합니다.

```azurepowershell-interactive

$rgName = "<RESOURCE GROUP NAME>"
$vnetlocation = "<VNET LOCATION>"
$vnetName = "<VIRTUAL NETWORK NAME>"
$subnetName = "<SUBNET NAME>"
$namespaceLocation = "<NAMESPACE LOCATION>"
$namespaceName = "<NAMESPACE NAME>"
$peConnectionName = "<PRIVATE ENDPOINT CONNECTION NAME>"

# create resource group
az group create -l $vnetLocation -n $rgName

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName $rgName `
                    -Location $vnetlocation `
                    -Name $vnetName `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name $subnetName `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork

# create a relay namespace
$namespaceResource = New-AzResource -Location $namespaceLocation -ResourceName $namespaceName -ResourceGroupName $rgName -Properties @{} -ResourceType "Microsoft.Relay/namespaces" 

# create a private link service connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name $peConnectionName `
                                -PrivateLinkServiceId $namespaceResource.ResourceId `
                                -GroupId "namespace"

# get subnet object that you will use in the next step                                
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $rgName -Name $vnetName
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                                | Where-Object  {$_.Name -eq $subnetName}  
   
# now, create private endpoint   
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName  `
                                -Name $vnetName   `
                                -Location $vnetlocation `
                                -Subnet  $subnet   `
                                -PrivateLinkServiceConnection $privateEndpointConnection

(Get-AzResource -ResourceId $namespaceResource.ResourceId -ExpandProperties).Properties


```

## <a name="manage-private-endpoints-using-azure-portal"></a>Azure Portal을 사용하여 프라이빗 엔드포인트 관리

프라이빗 엔드포인트를 만들 때 연결이 승인되어야 합니다. 프라이빗 엔드포인트를 만드는 리소스(릴레이 네임스페이스)가 사용자 디렉터리에 있는 경우 릴레이 네임스페이스에 대한 관리 권한이 있다면 연결 요청을 승인할 수 있습니다. 관리 액세스 권한이 없는 릴레이 네임스페이스에 연결하는 경우 해당 리소스의 소유자가 연결 요청을 승인할 때까지 기다려야 합니다.

다음 네 가지 프로비저닝 상태가 있습니다.

| 서비스 작업 | 서비스 소비자 프라이빗 엔드포인트 상태 | Description |
|--|--|--|
| None | Pending | 연결이 수동으로 만들어지고, Azure Relay 네임스페이스 소유자의 승인이 보류 중입니다. |
| 승인 | 승인됨 | 연결이 자동 또는 수동으로 승인되었으며, 사용할 준비가 되었습니다. |
| 거부 | 거부됨 | Azure Relay 네임스페이스 소유자가 연결을 거부했습니다. |
| 제거 | 연결 끊김 | Azure Relay 네임스페이스 소유자가 연결을 제거했습니다. 프라이빗 엔드포인트는 정보를 제공하므로 정리를 위해 삭제해야 합니다. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>프라이빗 엔드포인트 연결 승인, 거부 또는 제거

1. Azure Portal에 로그인합니다.
1. 검색 창에서 **릴레이**를 입력합니다.
1. 관리하려는 **네임스페이스**를 선택합니다.
1. **네트워킹** 탭을 선택합니다.
5. 승인, 거부 또는 제거하려는 작업에 따라 아래의 해당 섹션으로 이동합니다. 

### <a name="approve-a-private-endpoint-connection"></a>프라이빗 엔드포인트 연결 승인

1. 보류 중인 연결이 있으면 프로비저닝 상태가 **보류 중**인 연결이 나열됩니다. 
2. 승인하려는 **프라이빗 엔드포인트**를 선택합니다.
3. **승인** 단추를 선택합니다.

    ![프라이빗 엔드포인트 승인](./media/private-link-service/private-endpoint-approve.png)
4. **연결 승인** 페이지에서 선택적으로 **주석**을 추가하고 **예**를 선택합니다. **아니요**를 선택하면 아무 작업도 수행되지 않습니다. 

    ![연결 승인 페이지](./media/private-link-service/approve-connection-page.png)
5. 목록에서 연결의 상태가 **승인됨**으로 변경되어 있어야 합니다.

### <a name="reject-a-private-endpoint-connection"></a>프라이빗 엔드포인트 연결 거부

1. 이전에 승인된 기존 연결이든 아니면 보류 중인 요청이든지 거부하려는 프라이빗 엔드포인트 연결이 있는 경우 엔드포인트 연결을 선택하고 **거부** 단추를 클릭합니다.

    ![거부 단추](./media/private-link-service/private-endpoint-reject.png)
2. **연결 거부** 페이지에서 선택적으로 주석을 추가하고 **예**를 선택합니다. **아니요**를 선택하면 아무 작업도 수행되지 않습니다. 

    ![연결 거부 페이지](./media/private-link-service/reject-connection-page.png)
3. 목록에서 연결의 상태가 **거절됨**으로 변경되어 있어야 합니다.


### <a name="remove-a-private-endpoint-connection"></a>프라이빗 엔드포인트 연결 제거

1. 프라이빗 엔드포인트 연결을 제거하려면 목록에서 해당 연결을 선택하고 도구 모음에서 **제거**를 선택합니다. 

    ![제거 단추](./media/private-link-service/remove-endpoint.png)
2. **연결 삭제** 페이지에서 **예**를 선택하여 프라이빗 엔드포인트의 삭제를 확인합니다. **아니요**를 선택하면 아무 작업도 수행되지 않습니다. 

    ![연결 삭제 페이지](./media/private-link-service/delete-connection-page.png)
3. 상태가 **연결 끊김**으로 변경되어 있어야 합니다. 그러면 엔드포인트가 목록에서 사라지게 됩니다. 

## <a name="validate-that-the-private-link-connection-works"></a>프라이빗 링크 연결이 작동하는지 확인
개인 IP 주소를 통해 개인 끝점의 가상 네트워크 내에 있는 리소스가 Azure Relay 네임 스페이스에 연결 되어 있는지 확인 해야 합니다.

먼저 [Azure Portal에서 Windows 가상 머신 만들기](../virtual-machines/windows/quick-create-portal.md)의 단계에 따라 가상 머신을 만듭니다.

**네트워킹** 탭에서 다음을 수행합니다. 

1. **가상 네트워크**와 **서브넷**을 지정합니다. 프라이빗 엔드포인트를 배포한 가상 네트워크를 선택해야 합니다.
2. **공용 IP** 리소스를 지정합니다.
3. **NIC 네트워크 보안 그룹**은 **없음**을 선택합니다.
4. **부하 분산**은 **아니요**를 선택합니다.

VM에 연결하고 명령줄을 열어 다음 명령을 실행합니다.

```console
nslookup <your-relay-namespace-name>.servicebus.windows.net
```

결과가 다음과 같이 표시됩니다. 

```console
Non-authoritative answer:
Name:    <namespace-name>.privatelink.servicebus.windows.net
Address:  10.0.0.4 (private IP address associated with the private endpoint)
Aliases:  <namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>제한 사항 및 디자인 고려 사항

### <a name="design-considerations"></a>디자인 고려 사항
- Azure Relay에 대한 프라이빗 엔드포인트는 **공개 미리 보기**로 제공됩니다. 
- 가격 책정 정보는 [Azure Private Link(미리 보기) 가격](https://azure.microsoft.com/pricing/details/private-link/)을 참조하세요.

### <a name="limitations"></a>제한 사항 
- Azure Relay 네임스페이스당 최대 프라이빗 엔드포인트 수: 64.
- 구독당 프라이빗 엔드포인트가 포함된 최대 Azure Relay 네임스페이스 수: 64.
- NSG(네트워크 보안 그룹) 규칙 및 사용자 정의 경로는 프라이빗 엔드포인트에 적용되지 않습니다. 자세한 내용은 [Azure Private Link 서비스: 제한 사항](../private-link/private-link-service-overview.md#limitations)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Private Link(미리 보기)](../private-link/private-link-service-overview.md)에 대해 자세히 알아봅니다.
- [Azure Relay](relay-what-is-it.md)에 대해 자세히 알아봅니다.
