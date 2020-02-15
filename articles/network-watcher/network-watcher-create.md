---
title: Azure Network Watcher 인스턴스 만들기 | Microsoft Docs
description: Azure 지역에서 Network Watcher를 사용하도록 설정하는 방법을 알아봅니다.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 77812a3765a027152c957f6dbb7c9b3811a2278f
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191189"
---
# <a name="create-an-azure-network-watcher-instance"></a>Azure Network Watcher 인스턴스 만들기

Network Watcher는 Azure 내에서, Azure로, Azure로부터 네트워크 시나리오 수준 상태를 모니터링하고 진단할 수 있게 하는 지역 서비스입니다. 시나리오 수준 모니터링을 사용하면 종단 간 네트워크 수준 보기에서 문제를 진단할 수 있습니다. Network Watcher에서 제공하는 네트워크 진단 및 시각화 도구를 사용하면 Azure에서 네트워크를 파악하고, 진단하고, 정보를 얻을 수 있습니다. Network Watcher는 Network Watcher 리소스 만들기를 통해 사용하도록 설정됩니다. 이 리소스에서는 Network Watcher 기능을 활용하도록 허용합니다.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="network-watcher-is-automatically-enabled"></a>Network Watcher가 자동으로 사용하도록 설정됨
구독에서 가상 네트워크를 만들거나 업데이트하면 가상 네트워크 지역에서 Network Watcher가 자동으로 사용하도록 설정됩니다. Network Watcher가 자동으로 사용하도록 설정되어도 리소스에 영향을 주지 않으며 관련된 요금도 없습니다.

#### <a name="opt-out-of-network-watcher-automatic-enablement"></a>Network Watcher 자동 사용 옵트아웃
Network Watcher 자동 사용을 옵트아웃하려면 다음 명령을 실행하여 옵트아웃할 수 있습니다.

> [!WARNING]
> Network Watcher 자동 사용 옵트아웃은 영구적 변경입니다. 옵트아웃한 후에는 [고객 지원에 문의](https://azure.microsoft.com/support/options/)하지 않고는 옵트인할 수 없습니다.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName DisableNetworkWatcherAutocreation -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

```azurecli-interactive
az feature register --name DisableNetworkWatcherAutocreation --namespace Microsoft.Network
az provider register -n Microsoft.Network
```



## <a name="create-a-network-watcher-in-the-portal"></a>포털에서 Network Watcher 만들기

**모든 서비스** > **네트워킹** > **Network Watcher**로 이동합니다. Network Watcher에 대해 사용하도록 설정하려는 모든 구독을 선택할 수 있습니다. 이 작업은 사용할 수 있는 모든 지역에서 Network Watcher를 만듭니다.

![Network Watcher 만들기](./media/network-watcher-create/figure1.png)

포털을 사용하여 Network Watcher를 사용하도록 설정하면 Network Watcher 인스턴스의 이름이 *NetworkWatcher_region_name*으로 자동 설정됩니다. 여기서 *region_name*은 인스턴스가 설정된 Azure 지역에 해당됩니다. 예를 들어 미국 중서부에서 활성화된 Network Watcher의 이름은 *NetworkWatcher_westcentralus*입니다.

Network Watcher 인스턴스가 *NetworkWatcherRG*라는 리소스 그룹에 자동으로 생성됩니다. 리소스 그룹은 아직 존재하지 않는 경우 만들어집니다.

Network Watcher 인스턴스와 이 인스턴스가 배치된 리소스 그룹의 이름을 사용자 지정하려면 다음 섹션에 설명된 Powershell, Azure CLI, REST API 또는 ARMClient 메서드를 사용할 수 있습니다. 각 옵션에서 Network Watcher를 배치하려면 리소스 그룹이 있어야 합니다.  

## <a name="create-a-network-watcher-with-powershell"></a>PowerShell을 사용하여 Network Watcher 만들기

Network Watcher의 인스턴스를 만들려면 다음 예제를 실행합니다.

```powershell
New-AzNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-azure-cli"></a>Azure CLI를 사용하여 Network Watcher 만들기

Network Watcher의 인스턴스를 만들려면 다음 예제를 실행합니다.

```azurecli
az network watcher configure --resource-group NetworkWatcherRG --locations westcentralus --enabled
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>REST API를 사용하여 Network Watcher 만들기

PowerShell을 사용하여 REST API를 호출하는 데 ARMclient가 사용됩니다. ARMClient는 [Chocolatey의 ARMClient](https://chocolatey.org/packages/ARMClient)에서 chocolatey에 있습니다.

### <a name="log-in-with-armclient"></a>ARMClient에 로그인

```powerShell
armclient login
```

### <a name="create-the-network-watcher"></a>Network Watcher 만들기

```powershell
$subscriptionId = '<subscription id>'
$networkWatcherName = '<name of network watcher>'
$resourceGroupName = '<resource group name>'
$apiversion = "2016-09-01"
$requestBody = @"
{
'location': 'West Central US'
}
"@

armclient put "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}?api-version=${api-version}" $requestBody
```

## <a name="delete-a-network-watcher-in-the-portal"></a>포털에서 Network Watcher 삭제

**모든 서비스** > **네트워킹** > **Network Watcher**로 이동합니다.

아직 없는 경우 개요 탭을 선택 합니다. 드롭다운을 사용 하 여에서 네트워크 감시자를 사용 하지 않도록 설정할 구독을 선택 합니다.
화살표를 클릭 하 여 선택한 구독에 대 한 영역 목록을 확장 합니다. 지정 된 경우 오른쪽에 있는 3 개의 점을 사용 하 여 상황에 맞는 메뉴에 액세스 합니다.
"네트워크 감시자 사용 안 함"을 클릭 하 여 비활성화를 시작 합니다. 이 단계를 확인 하 라는 메시지가 표시 됩니다. 계속하려면 예를 클릭합니다.
포털에서는 모든 구독의 모든 지역에 대해 개별적으로이 작업을 수행 해야 합니다.


## <a name="delete-a-network-watcher-with-powershell"></a>PowerShell을 사용 하 여 Network Watcher 삭제

Network Watcher 인스턴스를 삭제 하려면 다음 예제를 실행 합니다.

```powershell
New-AzResourceGroup -Name NetworkWatcherRG -Location westcentralus
New-AzNetworkWatcher -Name NetworkWatcher_westcentralus -ResourceGroup NetworkWatcherRG -Location westcentralus
Remove-AzNetworkWatcher -Name NetworkWatcher_westcentralus -ResourceGroup NetworkWatcherRG
```

## <a name="next-steps"></a>다음 단계

이제 Network Watcher의 인스턴스가 있으니 제공되는 기능에 대해 알아봅니다.

* [토폴로지](network-watcher-topology-overview.md)
* [패킷 캡처](network-watcher-packet-capture-overview.md)
* [IP 흐름 확인](network-watcher-ip-flow-verify-overview.md)
* [다음 홉](network-watcher-next-hop-overview.md)
* [보안 그룹 보기](network-watcher-security-group-view-overview.md)
* [NSG 흐름 로깅](network-watcher-nsg-flow-logging-overview.md)
* [Virtual Network 게이트웨이 문제 해결](network-watcher-troubleshoot-overview.md)
