---
title: Azure Storage로 데이터 반출 제한 - Azure PowerShell
description: 이 문서에서는 Azure PowerShell을 사용하여 가상 네트워크 서비스 엔드포인트 정책을 통해 Azure Storage 리소스에 대한 가상 네트워크 데이터 반출을 제한하는 방법에 관해 알아봅니다.
services: virtual-network
documentationcenter: virtual-network
author: RDhillon
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: 3ff0ee79413640c09132b8399a3b0c07dd1783cc
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063698"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-azure-powershell"></a>Azure PowerShell을 사용하여 가상 네트워크 서비스 엔드포인트 정책으로 Azure Storage 계정에 대한 데이터 반출 관리

가상 네트워크 서비스 엔드포인트 정책을 사용하면 서비스 엔드포인트를 통해 가상 네트워크 내에서 Azure Storage 계정에 액세스 제어를 적용할 수 있습니다. 이는 워크로드를 보호하고 허용되는 스토리지 계정과 데이터 반출이 허용되는 위치를 관리하기 위해 매우 중요합니다.
이 문서에서는 다음 방법을 설명합니다.

* 가상 네트워크를 생성합니다.
* Azure Storage에 서브넷을 추가하고 서비스 엔드포인트를 사용하도록 설정합니다.
* 두 개의 Azure Storage 계정을 만들고 위에서 만든 서브넷에서 해당 계정에 대한 네트워크 액세스를 허용합니다.
* 스토리지 계정 중 하나에 대해서만 액세스를 허용하는 서비스 엔드포인트 정책을 만듭니다.
* 서브넷에 VM(가상 머신)을 배포합니다.
* 서브넷에서 허용된 스토리지 계정에 대한 액세스를 확인합니다.
* 서브넷에서 허용되지 않는 스토리지 계정에 대한 액세스가 거부되었는지 확인합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure 구독이 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우, 이 문서에는 Azure PowerShell 모듈 버전 1.0.0 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

가상 네트워크를 만들기 전에 가상 네트워크에 대한 리소스 그룹과 이 아티클에서 만든 다른 모든 리소스를 만들어야 합니다. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *myResourceGroup* 이라는 리소스 그룹을 만듭니다. 

```azurepowershell-interactive
New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS
```

[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)를 사용하여 가상 네트워크를 만듭니다. 다음 예제는 주소 접두사 *10.0.0.0/16* 을 사용하는 *myVirtualNetwork* 라는 가상 네트워크를 만듭니다.

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

## <a name="enable-a-service-endpoint"></a>서비스 엔드포인트 사용

가상 네트워크에 서브넷을 만듭니다. 이 예제에서는 *Private* 라는 서브넷이 *Microsoft.Storage* 에 대한 서비스 엔드포인트로 생성됩니다. 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-for-the-subnet"></a>서브넷에 대한 네트워크 액세스 제한

[New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)를 사용하여 네트워크 보안 그룹 보안 규칙을 만듭니다. 다음 규칙을 사용하면 Azure Storage 서비스에 지정된 공용 IP 주소에 대한 아웃바운드 액세스가 허용됩니다. 

```azurepowershell-interactive
$rule1 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-Storage-All `
  -Access Allow `
  -DestinationAddressPrefix Storage `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 100 -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

다음 규칙은 모든 공용 IP 주소에 대한 액세스를 거부합니다. 우선 순위가 더 높은 이전 규칙이 이 규칙을 재정의하여 Azure Storage의 공용 IP 주소에 대한 액세스를 허용합니다.

```azurepowershell-interactive
$rule2 = New-AzNetworkSecurityRuleConfig `
  -Name Deny-Internet-All `
  -Access Deny `
  -DestinationAddressPrefix Internet `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 110 -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

다음 규칙을 사용하면 어디에서나 서브넷에 대한 RDP(원격 데스크톱 프로토콜) 트래픽 인바운드가 허용됩니다. 서브넷에 대한 원격 데스크톱 연결이 허용되므로 이후 단계에서 리소스에 대한 네트워크 액세스를 확인할 수 있습니다.

```azurepowershell-interactive
$rule3 = New-AzNetworkSecurityRuleConfig `
  -Name Allow-RDP-All `
  -Access Allow `
  -DestinationAddressPrefix VirtualNetwork `
  -DestinationPortRange 3389 `
  -Direction Inbound `
  -Priority 120 `
  -Protocol * `
  -SourceAddressPrefix * `
  -SourcePortRange *
```

[New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)을 사용하여 네트워크 보안 그룹을 만듭니다. 다음 예제에서는 *myNsgPrivate* 라는 네트워크 보안 그룹을 만듭니다.

```azurepowershell-interactive
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myNsgPrivate `
  -SecurityRules $rule1,$rule2,$rule3
```

[Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)를 사용하여 네트워크 보안 그룹을 ‘프라이빗’ 서브넷에 연결한 다음 가상 네트워크에 서브넷 구성을 작성합니다. 다음 예제에서는 *myNsgPrivate* 네트워크 보안 그룹을 *프라이빗* 서브넷에 연결합니다.

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="restrict-network-access-to-azure-storage-accounts"></a>Azure Storage 계정에 대한 네트워크 액세스 제한

서비스 엔드포인트에 사용할 수 있는 Azure 서비스를 통해 만든 리소스에 대한 네트워크 액세스를 제한하는 데 필요한 단계는 서비스에 따라 다릅니다. 각 서비스에 대한 특정 단계는 개별 서비스의 설명서를 참조하세요. 이 문서의 나머지 부분에는 Azure Storage 계정에 대한 네트워크 액세스를 제한하는 단계가 예제로 포함되어 있습니다.

### <a name="create-two-storage-accounts"></a>두 개의 스토리지 계정 만들기

[New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount)를 사용하여 Azure Storage 계정을 만듭니다.

```azurepowershell-interactive
$storageAcctName1 = 'allowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName1 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

스토리지 계정이 만들어진 후 [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey)를 사용하여 스토리지 계정의 키를 변수로 검색합니다.

```azurepowershell-interactive
$storageAcctKey1 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName1).Value[0]
```

이 키는 이후 단계에서 파일 공유를 만드는 데 사용됩니다. `$storageAcctKey`를 입력하고 값을 적어 둡니다. 이후 단계에서 파일 공유를 VM의 드라이브에 매핑할 때도 이 값을 수동으로 입력해야 합니다.

이제 위의 단계를 반복하여 두 번째 스토리지 계정을 만듭니다.

```azurepowershell-interactive
$storageAcctName2 = 'notallowedaccount'

New-AzStorageAccount `
  -Location EastUS `
  -Name $storageAcctName2 `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

또한 나중에 파일 공유를 만드는 데 사용할 수 있도록 이 계정에서 스토리지 계정 키를 검색합니다.

```azurepowershell-interactive
$storageAcctKey2 = (Get-AzStorageAccountKey -ResourceGroupName myResourceGroup -AccountName $storageAcctName2).Value[0]
```

### <a name="create-a-file-share-in-each-of-the-storage-account"></a>각 스토리지 계정에 파일 공유 만들기

[New-AzStorageContext](/powershell/module/az.storage/new-AzStoragecontext)를 사용하여 스토리지 계정 및 키에 대한 컨텍스트를 만듭니다. 이 컨텍스트는 스토리지 계정 이름 및 계정 키를 캡슐화합니다.

```azurepowershell-interactive
$storageContext1 = New-AzStorageContext $storageAcctName1 $storageAcctKey1

$storageContext2 = New-AzStorageContext $storageAcctName2 $storageAcctKey2
```

[New-AzStorageShare](/powershell/module/az.storage/new-azstorageshare)를 사용하여 파일 공유를 만듭니다.

```azurepowershell-interactive
$share1 = New-AzStorageShare my-file-share -Context $storageContext1

$share2 = New-AzStorageShare my-file-share -Context $storageContext2
```

### <a name="deny-all-network-access-to-a-storage-accounts"></a>스토리지 계정에 대한 모든 네트워크 액세스 거부

기본적으로 스토리지 계정은 네트워크에 있는 클라이언트의 네트워크 연결을 허용합니다. 선택한 네트워크에 대한 액세스를 제한하려면 [Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset)을 사용하여 기본 작업을 ‘거부’로 변경합니다. 네트워크 액세스가 거부되면 네트워크에서 스토리지 계정에 액세스할 수 없습니다.

```azurepowershell-interactive
Update-AzStorageAccountNetworkRuleSet `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName1 `
  -DefaultAction Deny

Update-AzStorageAccountNetworkRuleSet  `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName2 `
  -DefaultAction Deny
```

### <a name="enable-network-access-only-from-the-vnet-subnet"></a>VNet 서브넷에서만 네트워크 액세스 사용

[Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)를 사용하여 만들어진 가상 네트워크를 검색한 다음 [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig)를 사용하여 프라이빗 서브넷 개체를 변수로 검색합니다.

```azurepowershell-interactive
$privateSubnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Name myVirtualNetwork `
  | Get-AzVirtualNetworkSubnetConfig -Name Private
```

[Add-AzStorageAccountNetworkRule](/powershell/module/az.network/add-aznetworksecurityruleconfig)을 사용하여 ‘프라이빗’ 서브넷에서 스토리지 계정에 대한 네트워크 액세스를 허용합니다.

```azurepowershell-interactive
Add-AzStorageAccountNetworkRule `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName1 `
  -VirtualNetworkResourceId $privateSubnet.Id

Add-AzStorageAccountNetworkRule `
  -ResourceGroupName myresourcegroup `
  -Name $storageAcctName2 `
  -VirtualNetworkResourceId $privateSubnet.Id
```

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>유효한 스토리지 계정에 대한 액세스를 허용하는 정책 적용

가상 네트워크의 사용자가 안전하고 허용된 Azure Storage 계정에만 액세스할 수 있도록 정의에서 허용된 스토리지 계정 목록을 사용하여 서비스 엔드포인트 정책을 만들 수 있습니다. 그런 다음 이 정책이 서비스 엔드포인트를 통해 스토리지에 연결된 가상 네트워크 서브넷에 적용됩니다.

### <a name="create-a-service-endpoint-policy"></a>서비스 엔드포인트 정책 만들기

이 섹션에서는 서비스 엔드포인트를 통한 액세스에 허용된 리소스 목록을 사용하여 정책 정의를 만듭니다.

첫 번째(허용된) 스토리지 계정의 리소스 ID 검색 

```azurepowershell-interactive
$resourceId = (Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name $storageAcctName1).id
```

위의 리소스를 허용하는 정책 정의 만들기

```azurepowershell-interactive
$policyDefinition = New-AzServiceEndpointPolicyDefinition -Name mypolicydefinition `
  -Description "Service Endpoint Policy Definition" `
  -Service "Microsoft.Storage" `
  -ServiceResource $resourceId
```

위에서 만든 정책 정의를 사용하여 서비스 엔드포인트 정책 만들기

```azurepowershell-interactive
$sepolicy = New-AzServiceEndpointPolicy -ResourceGroupName myresourcegroup `
  -Name mysepolicy -Location EastUS
  -ServiceEndpointPolicyDefinition $policyDefinition
```

### <a name="associate-the-service-endpoint-policy-to-the-virtual-network-subnet"></a>서비스 엔드포인트 정책을 가상 네트워크 서브넷에 연결

서비스 엔드포인트 정책을 만든 후에는 Azure Storage의 서비스 엔드포인트 구성을 통해 대상 서브넷에 연결합니다.

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.0.0/24 `
  -NetworkSecurityGroup $nsg `
  -ServiceEndpoint Microsoft.Storage `
  -ServiceEndpointPolicy $sepolicy

$virtualNetwork | Set-AzVirtualNetwork
```
## <a name="validate-access-restriction-to-azure-storage-accounts"></a>Azure Storage 계정에 대한 액세스 제한 유효성 검사

### <a name="deploy-the-virtual-machine"></a>가상 머신 배포

스토리지 계정에 대한 네트워크 액세스를 테스트하려면 서브넷에 VM을 배포합니다.

[New-AzVM](/powershell/module/az.compute/new-azvm)을 사용하여 ‘프라이빗’ 서브넷에 가상 머신을 만듭니다. 다음 명령을 실행하면 자격 증명을 묻는 메시지가 표시됩니다. 입력하는 값은 VM에 대한 사용자 이름과 암호로 구성됩니다. `-AsJob` 옵션은 백그라운드에서 VM을 만들므로 다음 단계를 계속 진행할 수 있습니다.

```azurepowershell-interactive
New-AzVm -ResourceGroupName myresourcegroup `
  -Location "East US" `
  -VirtualNetworkName myVirtualNetwork `
  -SubnetName Private `
  -Name "myVMPrivate" -AsJob
```

다음 예제 출력과 유사한 출력이 반환됩니다.

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

### <a name="confirm-access-to-the-allowed-storage-account"></a>‘허용된’ 스토리지 계정에 대한 액세스를 확인합니다.

[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress)를 사용하여 VM의 공용 IP 주소를 반환합니다. 다음 예제에서는 *myVmPrivate* VM의 공용 IP 주소를 반환합니다.

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

다음 명령에서 `<publicIpAddress>`을 이전 명령에서 반환된 공용 IP 주소로 바꾸고 다음 명령을 입력합니다.

```powershell
mstsc /v:<publicIpAddress>
```

원격 데스크톱 프로토콜(.rdp) 파일이 만들어지고 컴퓨터에 다운로드됩니다. 다운로드한 rdp 파일을 엽니다. 메시지가 표시되면 **연결** 을 선택합니다. VM을 만들 때 지정한 사용자 이름과 암호를 입력합니다. **추가 선택 사항**, **다른 계정 사용** 을 차례로 선택하여 VM을 만들 때 입력한 자격 증명을 지정해야 할 수도 있습니다. **확인** 을 선택합니다. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. 경고 메시지가 표시되면 **예** 또는 **계속** 을 선택하여 연결을 계속합니다.

*myVmPrivate* VM에서 PowerShell을 사용하여 Azure 파일 공유를 허용된 스토리지 계정에서 Z 드라이브에 매핑합니다. 

```powershell
$acctKey = ConvertTo-SecureString -String $storageAcctKey1 -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList ("Azure\allowedaccount"), $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\allowedaccount.file.core.windows.net\my-file-share" -Credential $credential
```

PowerShell에서 다음 예제 출력과 비슷한 출력을 반환합니다.

```powershell
Name           Used (GB)     Free (GB) Provider      Root
----           ---------     --------- --------      ----
Z                                      FileSystem    \\allowedaccount.file.core.windows.net\my-f...
```

Azure 파일 공유가 Z 드라이브에 매핑되었습니다.

*myVmPrivate* VM에 대한 원격 데스크톱 세션을 닫습니다.

### <a name="confirm-access-is-denied-to-non-allowed-storage-account"></a>‘허용되지 않은’ 스토리지 계정에 대한 액세스가 거부되는지 확인

동일한 *myVmPrivate* VM에서 Azure 파일 공유를 드라이브 X에 매핑하려고 합니다. 

```powershell
$acctKey = ConvertTo-SecureString -String $storageAcctKey1 -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\notallowedaccount", $acctKey
New-PSDrive -Name X -PSProvider FileSystem -Root "\\notallowedaccount.file.core.windows.net\my-file-share" -Credential $credential
```

공유에 대한 액세스가 거부되고 `New-PSDrive : Access is denied` 오류가 수신됩니다. 스토리지 계정 *notallowedaccount* 가 서비스 엔드포인트 정책의 허용된 리소스 목록에 없기 때문에 액세스가 거부되었습니다. 

*myVmPublic* VM에 대한 원격 데스크톱 세션을 닫습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)을 사용하여 리소스 그룹 및 해당 그룹에 포함된 모든 리소스를 제거할 수 있습니다.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure 가상 네트워크 서비스 엔드포인트를 통해 Azure Storage에 서비스 엔드포인트 정책을 적용했습니다. Azure Storage 계정을 만들고 가상 네트워크 서브넷에서 특정 스토리지 계정(즉, 다른 스토리지 계정 거부)에 대해서만 네트워크 액세스를 제한했습니다. 서비스 엔드포인트 정책에 관한 자세한 내용은 [서비스 엔드포인트 정책 개요](virtual-network-service-endpoint-policies-overview.md)를 참조하세요.
