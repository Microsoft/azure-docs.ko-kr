---
title: Azure Private Link 서비스와 Azure Event Hubs 통합
description: Azure Private Link Service와 Azure Event Hubs를 통합하는 방법을 알아봅니다.
ms.date: 07/29/2020
ms.topic: article
ms.openlocfilehash: 817ec7b9256829ace61a0d1dad98f1f34683c95e
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88716794"
---
# <a name="allow-access-to-azure-event-hubs-namespaces-via-private-endpoints"></a>개인 끝점을 통해 Azure Event Hubs 네임 스페이스에 대 한 액세스 허용 
Azure Private Link Service를 사용하면 가상 네트워크의 **프라이빗 엔드포인트**를 통해 Azure 서비스(예: Azure Event Hubs, Azure Storage 및 Azure Cosmos DB)와 Azure 호스팅 고객/파트너 서비스에 액세스할 수 있습니다.

프라이빗 엔드포인트는 Azure Private Link가 제공하는, 서비스에 비공개로 안전하게 연결하는 네트워크 인터페이스입니다. 개인 끝점은 가상 네트워크의 개인 IP 주소를 사용 하 여 서비스를 가상 네트워크에 효과적으로 제공 합니다. 서비스에 대한 모든 트래픽은 프라이빗 엔드포인트를 통해 라우팅할 수 있으므로 게이트웨이, NAT 디바이스, ExpressRoute 또는 VPN 연결 또는 공용 IP 주소가 필요하지 않습니다. 가상 네트워크와 서비스 간의 트래픽은 Microsoft 백본 네트워크를 통해 이동하여 공용 인터넷에서 노출을 제거합니다. Azure 리소스의 인스턴스에 연결하여 액세스 제어에서 가장 높은 수준의 세분성을 제공할 수 있습니다.

자세한 내용은 [Azure Private Link란?](../private-link/private-link-overview.md)을 참조하세요.

> [!IMPORTANT]
> 이 기능은 **표준** 계층과 **전용** 계층 모두에서 지원 됩니다. **기본** 계층에서는 지원 되지 않습니다.
>
> 프라이빗 엔드포인트를 사용하여 다른 Azure 서비스가 Event Hubs와 상호 작용하지 않도록 할 수 있습니다.  차단되는 요청에는 다른 Azure 서비스, Azure Portal, 로깅 및 메트릭 서비스 등이 포함됩니다. 
> 
> 다음은 개인 끝점을 사용 하는 경우 Event Hubs 리소스에 액세스할 수 없는 서비스의 일부입니다. 목록은 완전 **하지 않습니다** .
>
> - Azure Stream Analytics
> - Azure IoT Hub 경로
> - Azure IoT Device Explorer
> - Azure Event Grid
> - Azure Monitor (진단 설정)
>
> 단, 개인 끝점을 사용 하는 경우에도 신뢰할 수 있는 특정 서비스의 리소스 Event Hubs에 대 한 액세스를 허용할 수 있습니다. 신뢰할 수 있는 서비스 목록은 [신뢰할 수 있는 서비스](#trusted-microsoft-services)를 참조 하세요.

## <a name="add-a-private-endpoint-using-azure-portal"></a>Azure Portal을 사용하여 프라이빗 엔드포인트 추가

### <a name="prerequisites"></a>사전 요구 사항

Event Hubs 네임스페이스를 Azure Private Link와 통합하려면 다음 엔터티 또는 사용 권한이 필요합니다.

- Event Hubs 네임스페이스
- Azure 가상 네트워크
- 가상 네트워크의 서브넷 **기본** 서브넷을 사용할 수 있습니다. 
- 네임스페이스 및 가상 네트워크 모두에 대한 소유자 또는 기여자 권한

프라이빗 엔드포인트와 가상 네트워크는 동일한 지역에 있어야 합니다. 포털을 사용하여 프라이빗 엔드포인트에 대한 영역을 선택하면 해당 지역에 있는 가상 네트워크만 자동으로 필터링됩니다. 네임스페이스는 다른 지역에 있을 수 있습니다.

프라이빗 엔드포인트는 가상 네트워크에서 개인 IP 주소를 사용합니다.

### <a name="steps"></a>단계
Event Hubs 네임스페이스가 이미 있는 경우 다음 단계에 따라 Private Link 연결을 만들 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
2. 검색 표시줄에 **event hubs**를 입력합니다.
3. 목록에서 프라이빗 엔드포인트를 추가하려는 **네임스페이스**를 선택합니다.
4. 왼쪽 메뉴의 **설정** 에서 **네트워킹** 을 선택 합니다.

    > [!NOTE]
    > **표준** 또는 **전용** 네임 스페이스에 대 한 **네트워킹** 탭만 표시 됩니다. 

    :::image type="content" source="./media/private-link-service/selected-networks-page.png" alt-text="네트워크 탭-선택한 네트워크 옵션" lightbox="./media/private-link-service/selected-networks-page.png":::    

    > [!NOTE]
    > 기본적으로 **선택한 네트워크** 옵션이 선택 되어 있습니다. IP 방화벽 규칙을 지정 하지 않거나 가상 네트워크를 추가 하는 경우 공용 인터넷을 통해 네임 스페이스에 액세스할 수 있습니다. 
1. 페이지 위쪽에서 **프라이빗 엔드포인트 연결** 탭을 선택합니다. 
1. 페이지 위쪽에서 **+ 프라이빗 엔드포인트** 단추를 선택합니다.

    :::image type="content" source="./media/private-link-service/private-link-service-3.png" alt-text="네트워킹 페이지-개인 끝점 연결 탭-개인 끝점 추가 링크":::
7. **기본** 페이지에서 다음 단계를 수행합니다. 
    1. 프라이빗 엔드포인트를 만들려는 **Azure 구독**을 선택합니다. 
    2. 프라이빗 엔드포인트 리소스에 대한 **리소스 그룹**을 선택합니다.
    3. 프라이빗 엔드포인트에 대한 **이름**을 입력합니다. 
    5. 프라이빗 엔드포인트에 대한 **지역**을 선택합니다. 프라이빗 엔드포인트는 가상 네트워크와 동일한 영역에 있어야 하지만 연결할 프라이빗 링크 리소스와 다른 영역에 있을 수 있습니다. 
    6. 완료되면 **다음: 리소스 >** 단추(페이지 맨 아래)를 선택합니다.

        ![프라이빗 엔드포인트 만들기 - 기본 페이지](./media/private-link-service/create-private-endpoint-basics-page.png)
8. **리소스** 페이지에서 다음 단계를 수행합니다.
    1. 연결 방법으로 **내 디렉터리에서 Azure 리소스에 연결**을 선택하는 경우 다음 단계를 수행합니다. 
        1. **Event Hubs 네임스페이스**가 존재하는 **Azure 구독**을 선택합니다. 
        2. **리소스 종류**의 경우 **리소스 종류**에 대한 **Microsoft.EventHub/네임스페이스**를 선택합니다.
        3. **리소스**의 경우 드롭다운 목록에서 Event Hubs 네임스페이스를 선택합니다. 
        4. **대상 하위 리소스**가 **네임스페이스**로 설정되어 있는지 확인합니다.
        5. 완료되면 **다음: 구성 >** 단추를 페이지 아래쪽에서 선택합니다. 
        
            ![프라이빗 엔드포인트 만들기 - 리소스 페이지](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. **리소스 ID 또는 별칭으로 Azure 리소스에 연결**을 선택하는 경우 다음 단계를 수행합니다.
        1. **리소스 ID** 또는 **별칭**을 입력합니다. 다른 사람과 공유한 리소스 ID 또는 별칭일 수 있습니다. 리소스 ID를 가져오는 가장 쉬운 방법은 Azure Portal에서 Event Hubs 네임스페이스로 이동하여 `/subscriptions/`부터 URI의 일부를 복사하는 것입니다. 예를 보려면 다음 이미지를 참조하세요. 
        2. **대상 하위 리소스**에 **namespace**를 입력합니다. 프라이빗 엔드포인트에서 액세스할 수 있는 하위 리소스의 형식입니다.
        3. (선택 사항) **요청 메시지**를 입력합니다. 리소스 소유자는 프라이빗 엔드포인트 연결을 관리하는 동안 이 메시지를 확인합니다.
        4. 그런 다음, **다음: 구성 >** 단추를 페이지 아래쪽에서 선택합니다.

            ![프라이빗 엔드포인트 만들기 - 리소스 ID를 사용하여 연결](./media/private-link-service/connect-resource-id.png)
9. **구성** 페이지에서 프라이빗 엔드포인트를 배포하려는 가상 네트워크의 서브넷을 선택합니다. 
    1. **가상 네트워크**를 선택합니다. 드롭다운 목록에 현재 선택한 구독 및 위치의 가상 네트워크만 나열됩니다. 
    2. 선택한 가상 네트워크의 **서브넷**을 선택합니다. 
    3. 완료되면 **다음: 태그 >** 단추를 페이지의 아래쪽에서 선택합니다. 

        ![프라이빗 엔드포인트 만들기 - 구성 페이지](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. **태그** 페이지에서 프라이빗 엔드포인트 리소스와 연결하려는 태그(이름 및 값)를 만듭니다. 그런 다음 페이지 아래쪽에서 **검토 + 만들기** 단추를 선택합니다. 
11. **검토 + 만들기**에서 모든 설정을 검토하고 **만들기**를 선택하여 프라이빗 엔드포인트를 만듭니다.
    
    ![프라이빗 엔드포인트 만들기 - 검토 및 만들기 페이지](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. 만든 프라이빗 엔드포인트 연결이 엔드포인트 목록에 표시되는지 확인합니다. 이 예에서는 디렉터리의 Azure 리소스에 연결할 수 있고 충분한 사용 권한이 있으므로 프라이빗 엔드포인트가 자동으로 승인됩니다. 

    ![만든 프라이빗 엔드포인트](./media/private-link-service/private-endpoint-created.png)

[!INCLUDE [event-hubs-trusted-services](../../includes/event-hubs-trusted-services.md)]

트러스트 된 서비스에서 네임 스페이스에 액세스할 수 있도록 하려면 **네트워킹** 페이지에서 **방화벽 및 가상 네트워크** 탭으로 전환 하 고 **신뢰할 수 있는 Microsoft 서비스에서이 방화벽을 우회 하도록 허용 하 시겠습니까?** 에서 **예** 를 선택 합니다. 

## <a name="add-a-private-endpoint-using-powershell"></a>PowerShell을 사용하여 프라이빗 엔드포인트 추가
다음 예에서는 Azure PowerShell을 사용하여 프라이빗 엔드포인트 연결을 만드는 방법을 보여 줍니다. 자동으로 전용 클러스터를 만들지 않습니다. 이 [문서](event-hubs-dedicated-cluster-create-portal.md)의 단계에 따라 전용 Event Hubs 클러스터를 만듭니다. 

```azurepowershell-interactive
# create resource group

$rgName = "<RESOURCE GROUP NAME>"
$vnetlocation = "<VIRTUAL NETWORK LOCATION>"
$vnetName = "<VIRTUAL NETWORK NAME>"
$subnetName = "<SUBNET NAME>"
$namespaceLocation = "<NAMESPACE LOCATION>"
$namespaceName = "<NAMESPACE NAME>"
$peConnectionName = "<PRIVATE ENDPOINT CONNECTION NAME>"

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

# create an event hubs namespace in a dedicated cluster
$namespaceResource = New-AzResource -Location $namespaceLocation `
                                    -ResourceName $namespaceName `
                                    -ResourceGroupName $rgName `
                                    -Sku @{name = "Standard"; capacity = 1} `
                                    -Properties @{clusterArmId = "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/clusters/<EVENT HUBS CLUSTER NAME>"} `
                                    -ResourceType "Microsoft.EventHub/namespaces" -ApiVersion "2018-01-01-preview"

# create private endpoint connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name $peConnectionName `
                                -PrivateLinkServiceId $namespaceResource.ResourceId `
                                -GroupId "namespace"

# get subnet object that you will use later
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $rgName -Name $vnetName
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                                | Where-Object  {$_.Name -eq $subnetName}  
   
# create a private endpoint   
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName  `
                                -Name $vnetName   `
                                -Location $vnetlocation `
                                -Subnet  $subnet   `
                                -PrivateLinkServiceConnection $privateEndpointConnection

(Get-AzResource -ResourceId $namespaceResource.ResourceId -ExpandProperties).Properties


```

### <a name="configure-the-private-dns-zone"></a>프라이빗 DNS 영역 구성
Event Hubs 도메인에 대한 프라이빗 DNS 영역을 만들고, Virtual Network와의 연결 링크를 만듭니다.

```azurepowershell-interactive
$zone = New-AzPrivateDnsZone -ResourceGroupName $rgName `
                            -Name "privatelink.servicebus.windows.net" 
 
$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $rgName `
                                            -ZoneName "privatelink.servicebus.windows.net" `
                                            -Name "mylink" `
                                            -VirtualNetworkId $virtualNetwork.Id  
 
$networkInterface = Get-AzResource -ResourceId $privateEndpoint.NetworkInterfaces[0].Id -ApiVersion "2019-04-01" 
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
    foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
        Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
        $recordName = $fqdn.split('.',2)[0] 
        $dnsZone = $fqdn.split('.',2)[1] 
        New-AzPrivateDnsRecordSet -Name $recordName -RecordType A -ZoneName "privatelink.servicebus.windows.net"  `
                                -ResourceGroupName $rgName -Ttl 600 `
                                -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $ipconfig.properties.privateIPAddress)  
    } 
}
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
2. 검색 표시줄에 **event hubs**를 입력합니다.
3. 관리하려는 **네임스페이스**를 선택합니다.
4. **네트워킹** 탭을 선택합니다.
5. 승인, 거부 또는 제거하려는 작업에 따라 아래의 해당 섹션으로 이동합니다.

### <a name="approve-a-private-endpoint-connection"></a>프라이빗 엔드포인트 연결 승인
1. 보류 중인 연결이 있으면 프로비저닝 상태가 **보류 중**인 연결이 나열됩니다. 
2. 승인하려는 **프라이빗 엔드포인트**를 선택합니다.
3. **승인** 단추를 선택합니다.

    ![프라이빗 엔드포인트 승인](./media/private-link-service/approve-private-endpoint.png)
4. **연결 승인** 페이지에서 설명(선택 사항)을 추가하고 **예**를 선택합니다. **아니요**를 선택하면 아무 작업도 수행되지 않습니다. 
5. 목록에서 프라이빗 엔드포인트 연결의 상태가 **승인됨**으로 변경되어 있어야 합니다. 

### <a name="reject-a-private-endpoint-connection"></a>프라이빗 엔드포인트 연결 거부

1. 보류 중인 요청 또는 기존 연결인지 여부에 관계없이 거부하려는 프라이빗 엔드포인트 연결이 있으면 연결을 선택하고 **거부** 단추를 클릭합니다.

    ![전용 끝점 거부](./media/private-link-service/private-endpoint-reject-button.png)
2. **연결 거부** 페이지에서 설명(선택 사항)을 입력하고 **예**를 선택합니다. **아니요**를 선택하면 아무 작업도 수행되지 않습니다. 
3. 목록에서 프라이빗 엔드포인트 연결 상태가 **거부됨**으로 변경되어 있어야 합니다. 

### <a name="remove-a-private-endpoint-connection"></a>프라이빗 엔드포인트 연결 제거

1. 프라이빗 엔드포인트 연결을 제거하려면 목록에서 해당 연결을 선택하고 도구 모음에서 **제거**를 선택합니다.
2. **연결 삭제** 페이지에서 **예**를 선택하여 프라이빗 엔드포인트의 삭제를 확인합니다. **아니요**를 선택하면 아무 작업도 수행되지 않습니다.
3. 상태가 **연결 끊김**으로 변경되어 있어야 합니다. 그러면 엔드포인트가 목록에서 사라지게 됩니다.

## <a name="validate-that-the-private-link-connection-works"></a>프라이빗 링크 연결이 작동하는지 확인

개인 끝점의 가상 네트워크 내에 있는 리소스가 개인 IP 주소를 통해 Event Hubs 네임 스페이스에 연결 되어 있고 올바른 개인 DNS 영역 통합이 있는지 확인 해야 합니다.

먼저 [Azure Portal에서 Windows 가상 머신 만들기](../virtual-machines/windows/quick-create-portal.md)의 단계에 따라 가상 머신을 만듭니다.

**네트워킹** 탭에서 다음을 수행합니다. 

1. **가상 네트워크**와 **서브넷**을 지정합니다. 프라이빗 엔드포인트를 배포한 가상 네트워크를 선택해야 합니다.
2. **공용 IP** 리소스를 지정합니다.
3. **NIC 네트워크 보안 그룹**은 **없음**을 선택합니다.
4. **부하 분산**은 **아니요**를 선택합니다.

VM에 연결하고 명령줄을 열어 다음 명령을 실행합니다.

```console
nslookup <event-hubs-namespace-name>.servicebus.windows.net
```

결과가 다음과 같이 표시됩니다. 

```console
Non-authoritative answer:
Name:    <event-hubs-namespace-name>.privatelink.servicebus.windows.net
Address:  10.0.0.4 (private IP address associated with the private endpoint)
Aliases:  <event-hubs-namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>제한 사항 및 디자인 고려 사항

**가격 책정**: 가격 책정 정보는 [Azure Private Link 가격 책정](https://azure.microsoft.com/pricing/details/private-link/)을 참조하세요.

**제한 사항**:  이 기능은 모든 Azure 퍼블릭 지역에서 사용할 수 있습니다.

**Event Hubs 네임스페이스당 최대 프라이빗 엔드포인트 수**: 120.

자세한 내용은 [Azure Private Link 서비스: 제한 사항](../private-link/private-link-service-overview.md#limitations)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Private Link](../private-link/private-link-service-overview.md)에 대해 자세히 알아봅니다.
- [Azure Event Hubs](event-hubs-about.md)에 대해 자세히 알아봅니다.
