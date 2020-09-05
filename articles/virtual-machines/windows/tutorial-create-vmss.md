---
title: '자습서: Windows 가상 머신 확장 집합 만들기'
description: Azure PowerShell을 사용하여 가상 머신 확장 집합을 사용하는 Windows VM에서 고가용성 애플리케이션을 만들고 배포하는 방법을 알아봅니다.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: windows
ms.date: 11/30/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: b3853ddc71d1a9be26b2492764a9b341446e0eeb
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078744"
---
# <a name="tutorial-create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-windows-with-azure-powershell"></a>자습서: 가상 머신 확장 집합을 만들고 Azure PowerShell을 통해 Windows에 고가용성 앱 배포
가상 머신 확장 집합을 사용하면 동일한 자동 크기 조정 가상 머신 집합을 배포하고 관리할 수 있습니다. 확장 집합의 VM 수를 수동으로 조정할 수 있습니다. CPU와 같은 리소스 사용량, 메모리 요구량 또는 네트워크 트래픽을 기반으로 자동으로 크기를 조정하는 규칙을 정의할 수도 있습니다. 이 자습서에서는 Azure에서 가상 머신 확장 집합을 배포하고 다음에 대해 자세히 알아봅니다.

> [!div class="checklist"]
> * 사용자 지정 스크립트 확장을 사용하여 크기를 조정하는 IIS 사이트를 정의
> * 확장 집합에 대한 부하 분산 장치 만들기
> * 가상 머신 확장 집합 만들기
> * 확장 집합의 인스턴스 수 증가 또는 감소
> * 자동 크기 조정 규칙 만들기

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요**를 선택하기만 하면 됩니다. 또한 [https://shell.azure.com/powershell](https://shell.azure.com/powershell)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 시작할 수도 있습니다. **복사**를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, Enter 키를 눌러 실행합니다.

## <a name="scale-set-overview"></a>확장 집합 개요
가상 머신 확장 집합을 사용하면 동일한 자동 크기 조정 가상 머신 집합을 배포하고 관리할 수 있습니다. 확장 집합의 VM은 하나 이상의 *배치 그룹*에서 논리 장애 도메인 및 업데이트 도메인에 분산됩니다. 배치 그룹은 비슷하게 구성된 VM의 그룹으로 [가용성 집합](tutorial-availability-sets.md)과 비슷합니다.

VM은 필요에 따라 확장 집합에 생성됩니다. 사용자는 확장 집합에서 VM이 추가되거나 제거되는 방법 및 시기를 제어하는 자동 크기 조정 규칙을 정의합니다. 이러한 규칙은 메트릭(예: CPU 부하, 메모리 사용량 또는 네트워크 트래픽)을 기반으로 트리거할 수 있습니다.

확장 집합은 Azure 플랫폼 이미지를 사용하는 경우 최대 1,000개의 VM을 지원합니다. 중요한 설치 또는 VM 사용자 지정이 필요한 워크로드의 경우 [사용자 지정 VM 이미지를 만들 수 있습니다](tutorial-custom-images.md). 사용자 지정 이미지를 사용하는 경우 확장 집합에 최대 600대의 VM을 만들 수 있습니다.


## <a name="create-a-scale-set"></a>확장 집합 만들기
[New-AzVmss](/powershell/module/az.compute/new-azvmss)를 사용하여 가상 머신 확장 집합을 만듭니다. 다음 예제에서는 *Windows Server 2016 Datacenter* 플랫폼 이미지를 사용하는 *myScaleSet*이라는 확장 집합을 만듭니다. 가상 네트워크, 공용 IP 주소 및 부하 분산 장치에 대한 Azure 네트워크 리소스가 자동으로 만들어집니다. 메시지가 표시되면 확장 집합에서 VM 인스턴스에 대해 사용자 고유의 관리 자격 증명을 설정할 수 있습니다.

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic"
```

확장 집합 리소스와 VM을 모두 만들고 구성하는 데 몇 분 정도 걸립니다.


## <a name="deploy-sample-application"></a>샘플 애플리케이션 배포
확장 집합을 테스트하려면 기본 웹 애플리케이션을 설치합니다. Azure 사용자 지정 스크립트 확장을 사용하여 VM 인스턴스에 IIS를 설치하는 스크립트를 다운로드하고 실행합니다. 이 확장은 배포 후 구성, 소프트웨어 설치 또는 기타 구성/관리 작업에 유용합니다. 자세한 내용은 [사용자 지정 스크립트 확장 개요](../extensions/custom-script-windows.md)를 참조하세요.

사용자 지정 스크립트 확장을 사용하여 기본 IIS 웹 서버 설치 다음과 같이 IIS를 설치하는 사용자 지정 스크립트 확장을 적용합니다.

```azurepowershell-interactive
# Define the script for your Custom Script Extension to run
$publicSettings = @{
    "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Get information about the scale set
$vmss = Get-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"

# Use Custom Script Extension to install IIS and configure basic website
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

## <a name="allow-traffic-to-application"></a>애플리케이션에 트래픽 허용

기본 웹 애플리케이션에 대한 액세스를 허용하려면 [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) 및 [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)을 사용하여 네트워크 보안 그룹을 만듭니다. 자세한 내용은 [Azure 가상 머신 확장 집합에 대한 네트워킹](../../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md)을 참조하세요.

```azurepowershell-interactive
# Get information about the scale set
$vmss = Get-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"

#Create a rule to allow traffic over port 80
$nsgFrontendRule = New-AzNetworkSecurityRuleConfig `
  -Name myFrontendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 200 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow

#Create a network security group and associate it with the rule
$nsgFrontend = New-AzNetworkSecurityGroup `
  -ResourceGroupName  "myResourceGroupScaleSet" `
  -Location EastUS `
  -Name myFrontendNSG `
  -SecurityRules $nsgFrontendRule

$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName  "myResourceGroupScaleSet" `
  -Name myVnet

$frontendSubnet = $vnet.Subnets[0]

$frontendSubnetConfig = Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $vnet `
  -Name mySubnet `
  -AddressPrefix $frontendSubnet.AddressPrefix `
  -NetworkSecurityGroup $nsgFrontend

Set-AzVirtualNetwork -VirtualNetwork $vnet

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Name "myScaleSet" `
  -VirtualMachineScaleSet $vmss
```

## <a name="test-your-scale-set"></a>확장 집합 테스트
작동 중인 확장 집합을 확인하려면 [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress)를 사용하여 부하 분산 장치의 공용 IP 주소를 가져옵니다. 다음 예제에서는 확장 집합의 일부로 만든 *myPublicIP*의 IP 주소를 표시합니다.

```azurepowershell-interactive
Get-AzPublicIPAddress `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Name "myPublicIPAddress" | select IpAddress
```

웹 브라우저에 공용 IP 주소를 입력합니다. 웹앱이 표시되고 부하 분산 장치가 트래픽을 분산한 VM의 호스트 이름이 표시됩니다.

![실행 중인 IIS 사이트](./media/tutorial-create-vmss/running-iis-site.png)

확장 집합의 실제 동작을 확인하려면 웹 브라우저에서 새로 고침을 실행하여 부하 분산 장치가 앱이 실행되는 모든 VM으로 트래픽을 분산시키는 것을 확인합니다.


## <a name="management-tasks"></a>관리 작업
확장 집합의 수명 주기 내내 하나 이상의 관리 작업을 실행해야 합니다. 또한 다양한 수명 주기 작업을 자동화하는 스크립트를 만들어야 하는 경우가 있습니다. Azure PowerShell은 이러한 작업을 수행할 수 있는 빠른 방법을 제공합니다. 다음은 몇 가지 일반적인 작업입니다.

### <a name="view-vms-in-a-scale-set"></a>확장 집합의 VM 보기
확장 집합의 VM 인스턴스 목록을 보려면 다음과 같이 [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm)을 사용합니다.

```azurepowershell-interactive
Get-AzVmssVM `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"
```

다음 예제 출력에서는 확장 집합의 두 VM 인스턴스를 보여 줍니다.

```powershell
ResourceGroupName                 Name Location             Sku InstanceID ProvisioningState
-----------------                 ---- --------             --- ---------- -----------------
MYRESOURCEGROUPSCALESET   myScaleSet_0   eastus Standard_DS1_v2          0         Succeeded
MYRESOURCEGROUPSCALESET   myScaleSet_1   eastus Standard_DS1_v2          1         Succeeded
```

특정 VM 인스턴스에 대한 추가 정보를 보려면 `-InstanceId` 매개 변수를 [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm)에 추가합니다. 다음 예제에서는 *1* VM 인스턴스에 대한 정보가 표시됩니다.

```azurepowershell-interactive
Get-AzVmssVM `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet" `
  -InstanceId "1"
```


### <a name="increase-or-decrease-vm-instances"></a>VM 인스턴스 증가 또는 감소
현재 확장 집합의 인스턴스 수를 보려면 [Get-AzVmss](/powershell/module/az.compute/get-azvmss)를 사용하여 *sku.capacity*를 쿼리합니다.

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet" | `
  Select -ExpandProperty Sku
```

그런 다음, [Update-AzVmss](/powershell/module/az.compute/update-azvmss)를 사용하여 확장 집합의 가상 머신 수를 수동으로 늘리거나 줄일 수 있습니다. 다음 예제는 확장 집합의 VM 수를 *3*으로 설정합니다.

```azurepowershell-interactive
# Get current scale set
$scaleset = Get-AzVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$scaleset.sku.capacity = 3
Update-AzVmss -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $scaleset
```

확장 집합에 지정된 인스턴스 수를 업데이트하는 데 몇 분 정도 걸립니다.


### <a name="configure-autoscale-rules"></a>자동 크기 조정 규칙 구성
확장 집합에서 인스턴스 수를 수동으로 확장하는 대신 자동 크기 조정 규칙을 정의합니다. 이러한 규칙은 확장 집합의 인스턴스를 모니터링하고 사용자가 정의한 메트릭 및 임계값에 따라 적절하게 대응합니다. 평균 CPU 부하가 5분 넘게 60%를 초과하면 다음 예제에서는 인스턴스 수를 하나 늘립니다. 평균 CPU 부하가 5분 넘게 30% 미만이면 인스턴스 수를 하나 줄입니다.

```azurepowershell-interactive
# Define your scale set information
$mySubscriptionId = (Get-AzSubscription)[0].Id
$myResourceGroup = "myResourceGroupScaleSet"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
$myScaleSetId = (Get-AzVmss -ResourceGroupName $myResourceGroup -VMScaleSetName $myScaleSet).Id 

# Create a scale up rule to increase the number instances after 60% average CPU usage exceeded for a 5-minute period
$myRuleScaleUp = New-AzAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId $myScaleSetId `
  -Operator GreaterThan `
  -MetricStatistic Average `
  -Threshold 60 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Increase `
  -ScaleActionValue 1

# Create a scale down rule to decrease the number of instances after 30% average CPU usage over a 5-minute period
$myRuleScaleDown = New-AzAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId $myScaleSetId `
  -Operator LessThan `
  -MetricStatistic Average `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Decrease `
  -ScaleActionValue 1

# Create a scale profile with your scale up and scale down rules
$myScaleProfile = New-AzAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rule $myRuleScaleUp,$myRuleScaleDown `
  -Name "autoprofile"

# Apply the autoscale rules
Add-AzAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId $myScaleSetId `
  -AutoscaleProfile $myScaleProfile
```

자동 크기 조정을 사용하는 자세한 내용은 [자동 크기 조정 모범 사례](/azure/architecture/best-practices/auto-scaling)를 참조하세요.


## <a name="next-steps"></a>다음 단계
이 자습서에서는 가상 머신 확장 집합을 만들었습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 사용자 지정 스크립트 확장을 사용하여 크기를 조정하는 IIS 사이트를 정의
> * 확장 집합에 대한 부하 분산 장치 만들기
> * 가상 머신 확장 집합 만들기
> * 확장 집합의 인스턴스 수 증가 또는 감소
> * 자동 크기 조정 규칙 만들기

Virtual Machines의 부하 분산 개념에 대해 자세히 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [Virtual Machines 부하 분산](tutorial-load-balancer.md)
