---
title: Azure Private Link 서비스와 Azure Service Bus 통합
description: Azure Private Link Service와 Azure Service Bus를 통합하는 방법을 알아봅니다.
author: spelluru
ms.author: spelluru
ms.date: 06/23/2020
ms.topic: article
ms.openlocfilehash: 4f3b67794d1a7f3935c79c70f18b8bd4a1e0d7ef
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88716625"
---
# <a name="allow-access-to-azure-service-bus-namespaces-via-private-endpoints"></a>전용 끝점을 통해 Azure Service Bus 네임 스페이스에 대 한 액세스 허용

Azure Private Link Service를 사용하면 가상 네트워크의 **프라이빗 엔드포인트**를 통해 Azure 서비스(예: Azure Service Bus, Azure Storage 및 Azure Cosmos DB)와 Azure 호스팅 고객/파트너 서비스에 액세스할 수 있습니다.

프라이빗 엔드포인트는 Azure Private Link가 제공하는, 서비스에 비공개로 안전하게 연결하는 네트워크 인터페이스입니다. 프라이빗 엔드포인트는 VNet의 개인 IP 주소를 사용하여 서비스를 VNet으로 효과적으로 가져옵니다. 서비스에 대한 모든 트래픽은 프라이빗 엔드포인트를 통해 라우팅할 수 있으므로 게이트웨이, NAT 디바이스, ExpressRoute 또는 VPN 연결 또는 공용 IP 주소가 필요하지 않습니다. 가상 네트워크와 서비스 간의 트래픽은 Microsoft 백본 네트워크를 통해 이동하여 공용 인터넷에서 노출을 제거합니다. Azure 리소스의 인스턴스에 연결하여 액세스 제어에서 가장 높은 수준의 세분성을 제공할 수 있습니다.

자세한 내용은 [Azure Private Link란?](../private-link/private-link-overview.md)을 참조하세요.

>[!WARNING]
> 프라이빗 엔드포인트를 구현하면 다른 Azure 서비스가 Service Bus와 상호 작용하는 것을 방지할 수 있습니다.
>
> 신뢰할 수 있는 Microsoft 서비스는 Virtual Networks를 사용할 때 지원되지 않습니다.
>
> Virtual Networks가 작동하지 않는 일반적인 Azure 시나리오(목록은 전체 목록이 **아님**) -
> - Azure Event Grid와 통합
> - Azure IoT Hub 경로
> - Azure IoT Device Explorer
>
> 아래 Microsoft 서비스는 가상 네트워크에 있어야 합니다.
> - Azure App Service
> - Azure 기능

> [!IMPORTANT]
> 이 기능은 Azure Service Bus의 **프리미엄** 계층에서 지원됩니다. 프리미엄 계층에 대한 자세한 내용은 [Service Bus 프리미엄 및 표준 메시징 계층](service-bus-premium-messaging.md) 문서를 참조하세요.


## <a name="add-a-private-endpoint-using-azure-portal"></a>Azure Portal을 사용하여 프라이빗 엔드포인트 추가

### <a name="prerequisites"></a>사전 요구 사항

Service Bus 네임스페이스를 Azure Private Link와 통합하려면 다음 엔터티 또는 사용 권한이 필요합니다.

- Service Bus 네임스페이스
- Azure 가상 네트워크
- 가상 네트워크의 서브넷 **기본** 서브넷을 사용할 수 있습니다. 
- Service Bus 네임스페이스 및 가상 네트워크 모두에 대한 소유자 또는 기여자 권한입니다.

프라이빗 엔드포인트와 가상 네트워크는 동일한 지역에 있어야 합니다. 포털을 사용하여 프라이빗 엔드포인트에 대한 영역을 선택하면 해당 지역에 있는 가상 네트워크만 자동으로 필터링됩니다. Service Bus 네임스페이스는 다른 지역에 있을 수 있습니다. 또한 프라이빗 엔드포인트는 가상 네트워크에서 개인 IP 주소를 사용합니다.

### <a name="steps"></a>단계

기존 네임스페이스가 이미 있는 경우 다음 단계를 수행하여 프라이빗 엔드포인트를 만들 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
2. 검색 표시줄에 **Service Bus**를 입력합니다.
3. 목록에서 프라이빗 엔드포인트를 추가하려는 **네임스페이스**를 선택합니다.
2. 왼쪽 메뉴의 **설정**에서 **네트워킹** 옵션을 선택 합니다. 

    > [!NOTE]
    > **프리미엄** 네임 스페이스에 대 한 **네트워킹** 탭만 표시 됩니다.  
    
    기본적으로 **선택한 네트워크** 옵션이 선택 되어 있습니다. 이 페이지에 하나 이상의 IP 방화벽 규칙 또는 가상 네트워크를 추가 하지 않으면 공용 인터넷을 통해 네임 스페이스에 액세스할 수 있습니다 (액세스 키 사용).

    :::image type="content" source="./media/service-bus-ip-filtering/default-networking-page.png" alt-text="네트워킹 페이지-기본값" lightbox="./media/service-bus-ip-filtering/default-networking-page.png":::
    
    **모든 네트워크** 옵션을 선택 하면 Service Bus 네임 스페이스가 액세스 키를 사용 하 여 모든 IP 주소의 연결을 허용 합니다. 이러한 기본 설정은 0.0.0.0/0 IP 주소 범위를 수락하는 규칙과 같습니다. 

    ![방화벽 - 모든 네트워크 옵션 선택됨](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
5. 개인 끝점을 통해 네임 스페이스에 대 한 액세스를 허용 하려면 페이지 맨 위에 있는 **개인 끝점 연결** 탭을 선택 합니다.
6. 페이지 위쪽에서 **+ 프라이빗 엔드포인트** 단추를 선택합니다.

    ![프라이빗 엔드포인트 추가 단추](./media/private-link-service/private-link-service-3.png)
7. **기본** 페이지에서 다음 단계를 수행합니다. 
    1. 프라이빗 엔드포인트를 만들려는 **Azure 구독**을 선택합니다. 
    2. 프라이빗 엔드포인트 리소스에 대한 **리소스 그룹**을 선택합니다.
    3. 프라이빗 엔드포인트에 대한 **이름**을 입력합니다. 
    5. 프라이빗 엔드포인트에 대한 **지역**을 선택합니다. 프라이빗 엔드포인트는 가상 네트워크와 동일한 영역에 있어야 하지만 연결할 프라이빗 링크 리소스와 다른 영역에 있을 수 있습니다. 
    6. 완료되면 **다음: 리소스 >** 단추(페이지 맨 아래)를 선택합니다.

        ![프라이빗 엔드포인트 만들기 - 기본 페이지](./media/private-link-service/create-private-endpoint-basics-page.png)
8. **리소스** 페이지에서 다음 단계를 수행합니다.
    1. 연결 방법으로 **내 디렉터리에서 Azure 리소스에 연결**을 선택하는 경우 다음 단계를 수행합니다.   
        1. **Service Bus 네임스페이스**가 존재하는 **Azure 구독**을 선택합니다. 
        2. **리소스 종류**의 경우 **리소스 종류**에 대한 **Microsoft.ServiceBus/namespaces**를 선택합니다.
        3. **리소스**의 경우 드롭다운 목록에서 Service Bus 네임스페이스를 선택합니다. 
        4. **대상 하위 리소스**가 **namespace**로 설정되어 있는지 확인합니다.
        5. 완료되면 **다음: 구성 >** 단추를 페이지 아래쪽에서 선택합니다. 
        
            ![프라이빗 엔드포인트 만들기 - 리소스 페이지](./media/private-link-service/create-private-endpoint-resource-page.png)
    2. **리소스 ID 또는 별칭으로 Azure 리소스에 연결**을 선택하는 경우 다음 단계를 수행합니다.
        1. **리소스 ID** 또는 **별칭**을 입력합니다. 다른 사람과 공유한 리소스 ID 또는 별칭일 수 있습니다. 리소스 ID를 가져오는 가장 쉬운 방법은 Azure Portal에서 Service Bus 네임스페이스로 이동하여 `/subscriptions/`부터 URI의 일부를 복사하는 것입니다. 예를 보려면 다음 이미지를 참조하세요. 
        2. **대상 하위 리소스**에 **namespace**를 입력합니다. 프라이빗 엔드포인트에서 액세스할 수 있는 하위 리소스의 형식입니다. 
        3. (선택 사항) **요청 메시지**를 입력합니다. 리소스 소유자는 프라이빗 엔드포인트 연결을 관리하는 동안 이 메시지를 확인합니다. 
        4. 그런 다음, **다음: 구성 >** 단추(페이지 맨 아래)를 선택합니다. 

            ![프라이빗 엔드포인트 만들기 - 리소스 ID를 사용하여 연결](./media/private-link-service/connect-resource-id.png)
9. **구성** 페이지에서 프라이빗 엔드포인트를 배포하려는 가상 네트워크의 서브넷을 선택합니다. 
    1. **가상 네트워크**를 선택합니다. 드롭다운 목록에 현재 선택한 구독 및 위치의 가상 네트워크만 나열됩니다. 
    2. 선택한 가상 네트워크의 **서브넷**을 선택합니다. 
    3. 완료되면 **다음: 태그 >** 단추를 페이지의 아래쪽에서 선택합니다. 

        ![프라이빗 엔드포인트 만들기 - 구성 페이지](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. **태그** 페이지에서 프라이빗 엔드포인트 리소스와 연결하려는 태그(이름 및 값)를 만듭니다. 그런 다음 페이지 아래쪽에서 **검토 + 만들기** 단추를 선택합니다. 
11. **검토 + 만들기**에서 모든 설정을 검토하고 **만들기**를 선택하여 프라이빗 엔드포인트를 만듭니다.
    
    ![프라이빗 엔드포인트 만들기 - 검토 및 만들기 페이지](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. 프라이빗 엔드포인트가 만들어졌는지 확인합니다. 리소스 소유자이고 **연결 방법**에 대한 **내 디렉터리 옵션에서 Azure 리소스에 연결**을 선택한 경우 엔드포인트 연결은 **자동 승인**이어야 합니다. **보류 중** 상태인 경우 [Azure Portal을 사용하여 프라이빗 엔드포인트 관리](#manage-private-endpoints-using-azure-portal) 섹션을 참조하세요.

    ![만든 프라이빗 엔드포인트](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>PowerShell을 사용하여 프라이빗 엔드포인트 추가
다음 예에서는 Azure PowerShell을 사용하여 Service Bus 네임스페이스에 대한 프라이빗 엔드포인트 연결을 만드는 방법을 보여 줍니다.

프라이빗 엔드포인트와 가상 네트워크는 동일한 지역에 있어야 합니다. Service Bus 네임스페이스는 다른 지역에 있을 수 있습니다. 또한 프라이빗 엔드포인트는 가상 네트워크에서 개인 IP 주소를 사용합니다.

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

# create premium service bus namespace
$namespaceResource = New-AzResource -Location $namespaceLocation -ResourceName $namespaceName -ResourceGroupName $rgName -Sku @{name = "Premium"; capacity = 1} -Properties @{} -ResourceType "Microsoft.ServiceBus/namespaces" -

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

프라이빗 엔드포인트를 만들 때 연결이 승인되어야 합니다. 프라이빗 엔드포인트를 만드는 리소스가 디렉터리에 있는 경우 충분한 사용 권한이 있다면 연결 요청을 승인할 수 있습니다. 다른 디렉터리의 Azure 리소스에 연결하는 경우 해당 리소스의 소유자가 연결 요청을 승인할 때까지 기다려야 합니다.

다음 네 가지 프로비저닝 상태가 있습니다.

| 서비스 작업 | 서비스 소비자 프라이빗 엔드포인트 상태 | Description |
|--|--|--|
| None | Pending | 연결이 수동으로 만들어지고, Private Link 리소스 소유자의 승인이 보류 중입니다. |
| 승인 | 승인됨 | 연결이 자동 또는 수동으로 승인되었으며, 사용할 준비가 되었습니다. |
| 거부 | 거부됨 | Private Link 리소스 소유자가 연결을 거부했습니다. |
| 제거 | 연결 끊김 | Private Link 리소스 소유자가 연결을 제거했습니다. 프라이빗 엔드포인트는 정보를 제공하므로 정리를 위해 삭제해야 합니다. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>프라이빗 엔드포인트 연결 승인, 거부 또는 제거

1. Azure Portal에 로그인합니다.
1. 검색 표시줄에 **Service Bus**를 입력합니다.
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

    ![연결 상태 - 승인됨](./media/private-link-service/connection-status-approved.png)

### <a name="reject-a-private-endpoint-connection"></a>프라이빗 엔드포인트 연결 거부

1. 이전에 승인된 기존 연결이든 아니면 보류 중인 요청이든지 거부하려는 프라이빗 엔드포인트 연결이 있는 경우 엔드포인트 연결을 선택하고 **거부** 단추를 클릭합니다.

    ![거부 단추](./media/private-link-service/private-endpoint-reject.png)
2. **연결 거부** 페이지에서 선택적으로 주석을 추가하고 **예**를 선택합니다. **아니요**를 선택하면 아무 작업도 수행되지 않습니다. 

    ![연결 거부 페이지](./media/private-link-service/reject-connection-page.png)
3. 목록에서 연결의 상태가 **거부됨**으로 변경되어 있어야 합니다. 

    ![엔드포인트가 거부됨](./media/private-link-service/endpoint-rejected.png)


### <a name="remove-a-private-endpoint-connection"></a>프라이빗 엔드포인트 연결 제거

1. 프라이빗 엔드포인트 연결을 제거하려면 목록에서 해당 연결을 선택하고 도구 모음에서 **제거**를 선택합니다. 

    ![제거 단추](./media/private-link-service/remove-endpoint.png)
2. **연결 삭제** 페이지에서 **예**를 선택하여 프라이빗 엔드포인트의 삭제를 확인합니다. **아니요**를 선택하면 아무 작업도 수행되지 않습니다. 

    ![연결 삭제 페이지](./media/private-link-service/delete-connection-page.png)
3. 상태가 **연결 끊김**으로 변경되어 있어야 합니다. 그러면 엔드포인트가 목록에서 사라지게 됩니다. 

## <a name="validate-that-the-private-link-connection-works"></a>프라이빗 링크 연결이 작동하는지 확인

개인 끝점의 가상 네트워크 내에 있는 리소스가 개인 IP 주소를 통해 Service Bus 네임 스페이스에 연결 되어 있고 올바른 개인 DNS 영역 통합이 있는지 확인 해야 합니다.

먼저 [Azure Portal에서 Windows 가상 머신 만들기](../virtual-machines/windows/quick-create-portal.md)의 단계에 따라 가상 머신을 만듭니다.

**네트워킹** 탭에서 다음을 수행합니다. 

1. **가상 네트워크**와 **서브넷**을 지정합니다. 프라이빗 엔드포인트를 배포한 가상 네트워크를 선택해야 합니다.
2. **공용 IP** 리소스를 지정합니다.
3. **NIC 네트워크 보안 그룹**은 **없음**을 선택합니다.
4. **부하 분산**은 **아니요**를 선택합니다.

VM에 연결하고 명령줄을 열어 다음 명령을 실행합니다.

```console
nslookup <service-bus-namespace-name>.servicebus.windows.net
```

결과가 다음과 같이 표시됩니다. 

```console
Non-authoritative answer:
Name:    <service-bus-namespace-name>.privatelink.servicebus.windows.net
Address:  10.0.0.4 (private IP address associated with the private endpoint)
Aliases:  <service-bus-namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>제한 사항 및 디자인 고려 사항

**가격 책정**: 가격 책정 정보는 [Azure Private Link 가격 책정](https://azure.microsoft.com/pricing/details/private-link/)을 참조하세요.

**제한 사항**:  이 기능은 모든 Azure 퍼블릭 지역에서 사용할 수 있습니다.

**Service Bus 네임스페이스당 최대 프라이빗 엔드포인트 수**: 120.

자세한 내용은 [Azure Private Link 서비스: 제한 사항](../private-link/private-link-service-overview.md#limitations)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Private Link](../private-link/private-link-service-overview.md)에 대해 자세히 알아봅니다.
- [Azure Service Bus](service-bus-messaging-overview.md)에 대해 자세히 알아보세요.
