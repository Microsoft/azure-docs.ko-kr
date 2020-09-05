---
title: '자습서: Azure VM 확장 집합 만들기 및 관리 – Azure PowerShell'
description: Azure PowerShell을 사용하여 인스턴스를 시작하고 중지하는 방법, 확장 집합 용량을 변경하는 방법 등의 몇 가지 일반적인 관리 작업과 함께 가상 머신 확장 집합을 만드는 방법을 알아봅니다.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 05/18/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: 1ab52c197ee57351a53919291f1c2e41108396ec
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078540"
---
# <a name="tutorial-create-and-manage-a-virtual-machine-scale-set-with-azure-powershell"></a>자습서: Azure PowerShell을 사용하여 가상 머신 확장 집합 만들기 및 관리

가상 머신 확장 집합을 사용하면 동일한 자동 크기 조정 가상 머신 집합을 배포하고 관리할 수 있습니다. 가상 머신 확장 집합의 수명 주기 동안 하나 이상의 관리 작업을 실행해야 합니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 가상 머신 확장 집합 만들기 및 연결
> * VM 이미지 선택 및 사용
> * 특정 VM 인스턴스 크기 보기 및 사용
> * 수동으로 확장 집합 크기 조정
> * 일반적인 확장 집합 관리 작업 수행

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]



## <a name="create-a-resource-group"></a>리소스 그룹 만들기
Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 가상 머신 확장 집합보다 먼저 리소스 그룹을 만들어야 합니다. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 명령을 사용하여 리소스 그룹을 만듭니다. 이 예제에서는 *EastUS* 지역에 *myResourceGroup*이라는 리소스 그룹을 만듭니다. 

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName "myResourceGroup" -Location "EastUS"
```
리소스 그룹 이름은 이 자습서에서 확장 집합 만들거나 수정할 때 지정됩니다.


## <a name="create-a-scale-set"></a>확장 집합 만들기
먼저 [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential?view=powershell-5.1)을 사용하여 VM 인스턴스에 대한 관리자 사용자 이름과 암호를 설정합니다.

```azurepowershell-interactive
$cred = Get-Credential
```

이제 [New-AzVmss](/powershell/module/az.compute/new-azvmss)를 사용하여 가상 머신 확장 집합을 만듭니다. 트래픽을 개별 VM 인스턴스로 배포하기 위해 부하 분산 장치도 생성됩니다. 부하 분산 장치는 TCP 포트 80에서 트래픽을 분산할 뿐만 아니라 TCP 포트 3389의 원격 데스크톱 트래픽 및 TCP 포트 5985의 PowerShell 원격을 허용하는 규칙을 포함합니다.

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -Location "EastUS" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -Credential $cred
```

확장 집합 리소스와 VM 인스턴스를 모두 만들고 구성하는 데 몇 분 정도 걸립니다.

> [!IMPORTANT]
> 확장 집합에 연결할 수 없는 경우 *[-SecurityGroupName "mySecurityGroup"](/powershell/module/az.compute/new-azvmss)* 매개 변수를 추가하여 네트워크 보안 그룹을 만들어야 할 수도 있습니다.


## <a name="view-the-vm-instances-in-a-scale-set"></a>확장 집합의 VM 인스턴스 보기
확장 집합의 VM 인스턴스 목록을 보려면 다음과 같이 [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm)을 사용합니다.

```azurepowershell-interactive
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

다음 예제 출력에서는 확장 집합의 두 VM 인스턴스를 보여 줍니다.

```powershell
ResourceGroupName         Name Location             Sku InstanceID ProvisioningState
-----------------         ---- --------             --- ---------- -----------------
MYRESOURCEGROUP   myScaleSet_0   eastus Standard_DS1_v2          0         Succeeded
MYRESOURCEGROUP   myScaleSet_1   eastus Standard_DS1_v2          1         Succeeded
```

특정 VM 인스턴스에 대한 추가 정보를 보려면 `-InstanceId` 매개 변수를 [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm)에 추가합니다. 다음 예제에서는 *1* VM 인스턴스에 대한 정보가 표시됩니다.

```azurepowershell-interactive
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


## <a name="list-connection-information"></a>연결 정보 나열
트래픽을 개별 VM 인스턴스로 라우팅하는 부하 분산 장치에 공용 IP 주소가 할당됩니다. NAT(Network Address Translation) 규칙은 기본적으로 지정된 포트의 각 VM에 원격 연결 트래픽을 전달하는 Azure 부하 분산 장치에 추가됩니다. 확장 집합의 VM 인스턴스에 연결하려면 할당된 공용 IP 주소와 포트 번호에 대한 원격 연결을 만듭니다.

확장 집합의 VM 인스턴스에 연결할 NAT 포트를 나열하려면, 먼저 [Get-AzLoadBalancer](/powershell/module/az.network/Get-AzLoadBalancer)를 사용하여 부하 분산 장치 개체를 가져옵니다. 그런 다음, [Get-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/Get-AzLoadBalancerInboundNatRuleConfig)를 사용하여 인바운드 NAT 규칙을 봅니다.


```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort
```

다음 예제 출력에서는 NAT 규칙에서 트래픽을 전달하는 인스턴스 이름, 부하 분산 장치의 공용 IP 주소 및 포트 번호를 보여 줍니다.

```powershell
Name             Protocol FrontendPort BackendPort
----             -------- ------------ -----------
myScaleSet3389.0 Tcp             50001        3389
myScaleSet5985.0 Tcp             51001        5985
myScaleSet3389.1 Tcp             50002        3389
myScaleSet5985.1 Tcp             51002        5985
```

규칙의 *이름*은 이전 [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) 명령에 표시된 VM 인스턴스의 이름과 일치합니다. 예를 들어 *0* VM 인스턴스에 연결하려면 *myScaleSet3389.0*을 사용하고 *50001* 포트에 연결합니다. *1* VM 인스턴스에 연결하려면 *myScaleSet3389.1*의 값을 사용하고 *50002* 포트에 연결합니다. PowerShell 원격을 사용하려면 *5985* *TCP* 포트에 대한 적절한 VM 인스턴스 규칙에 연결합니다.

[Get-AzPublicIpAddress](/powershell/module/az.network/Get-AzPublicIpAddress)를 사용하여 부하 분산 장치의 공용 IP 주소를 봅니다.


```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" -Name "myPublicIPAddress" | Select IpAddress
```

예제 출력:

```powershell
IpAddress
---------
52.168.121.216
```

첫 번째 VM 인스턴스에 대한 원격 연결을 만듭니다. 앞의 명령과 같이 필요한 VM 인스턴스의 공용 IP 주소와 포트 번호를 지정합니다. 메시지가 표시되면 확장 집합을 만들 때 사용한 자격 증명을 입력합니다(샘플 명령의 경우 기본적으로 *azureuser* 및 *P\@ssw0rd!* 임). Azure Cloud Shell을 사용하는 경우 로컬 PowerShell 프롬프트 또는 원격 데스크톱 클라이언트에서 이 단계를 수행합니다. 다음 예제에서는 *1* VM 인스턴스에 연결합니다.

```powershell
mstsc /v 52.168.121.216:50001
```

VM 인스턴스에 로그인한 후 필요에 따라 일부 구성 변경을 수동으로 수행할 수 있습니다. 지금은 원격 연결을 닫습니다.


## <a name="understand-vm-instance-images"></a>VM 인스턴스 이미지 이해
Azure Marketplace에는 VM 인스턴스를 만드는 데 사용할 수 있는 많은 VM 이미지가 포함되어 있습니다. 사용 가능한 게시자 목록을 보려면 [Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) 명령을 사용합니다.

```azurepowershell-interactive
Get-AzVMImagePublisher -Location "EastUS"
```

지정된 게시자에 대한 이미지 목록을 보려면 [Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku)를 사용합니다. 이미지 목록은 `-PublisherName` 또는 `-Offer`로 필터링할 수도 있습니다. 다음 예제에서는 게시자 이름이 *MicrosoftWindowsServer*이고 *WindowsServer*와 일치하는 제품이 있는 모든 이미지에 대한 목록이 필터링됩니다.

```azurepowershell-interactive
Get-AzVMImageSku -Location "EastUS" -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer"
```

다음 예제 출력에서는 사용 가능한 모든 Windows Server 이미지를 보여 줍니다.

```powershell
Skus                                  Offer         PublisherName          Location
----                                  -----         -------------          --------
2008-R2-SP1                           WindowsServer MicrosoftWindowsServer eastus
2008-R2-SP1-smalldisk                 WindowsServer MicrosoftWindowsServer eastus
2012-Datacenter                       WindowsServer MicrosoftWindowsServer eastus
2012-Datacenter-smalldisk             WindowsServer MicrosoftWindowsServer eastus
2012-R2-Datacenter                    WindowsServer MicrosoftWindowsServer eastus
2012-R2-Datacenter-smalldisk          WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter                       WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-Server-Core           WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-Server-Core-smalldisk WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-smalldisk             WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-with-Containers       WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-with-RDSH             WindowsServer MicrosoftWindowsServer eastus
2016-Nano-Server                      WindowsServer MicrosoftWindowsServer eastus
```

자습서의 시작 부분에서 확장 집합을 만들 때 VM 인스턴스에 대해 *Windows Server 2016 DataCenter*의 기본 VM 이미지가 제공되었습니다. [Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku)의 출력에 따라 다른 VM 이미지를 지정할 수 있습니다. 다음 예제에서는 *MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest*의 VM 이미지를 지정하는 `-ImageName` 매개 변수를 사용하여 확장 집합을 만듭니다. 모든 확장 집합 리소스와 VM 인스턴스를 만들고 구성하는 데 몇 분이 걸리기 때문에 다음 확장 집합을 배포할 필요가 없습니다.

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup2" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet2" `
  -VirtualNetworkName "myVnet2" `
  -SubnetName "mySubnet2" `
  -PublicIpAddressName "myPublicIPAddress2" `
  -LoadBalancerName "myLoadBalancer2" `
  -UpgradePolicyMode "Automatic" `
  -ImageName "MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest" `
  -Credential $cred
```

> [!IMPORTANT]
> *최신* 이미지 버전을 사용하는 것이 좋습니다. 배포 시 사용할 수 있는 최신 버전의 이미지를 사용하려면 '최신'을 지정합니다. '최신'을 사용하더라도 배포 시간이 지나면 새 버전을 사용할 수 있게 되는 경우에도 VM 이미지가 자동으로 업데이트되지 않습니다.

## <a name="understand-vm-instance-sizes"></a>VM 인스턴스 크기 이해
VM 인스턴스 크기 또는 *SKU*에 따라 VM 인스턴스에 사용할 수 있는 컴퓨팅 리소스(예: CPU, GPU, 메모리)의 양이 결정됩니다. 확장 집합의 VM 인스턴스 크기는 예상 작업에 맞게 적절히 조정되어야 합니다.

### <a name="vm-instance-sizes"></a>VM 인스턴스 크기
다음 표에서는 일반적인 VM 크기를 사용 사례로 분류하고 있습니다.

| Type                     | 일반적인 크기           |    Description       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [범용](../virtual-machines/sizes-general.md)         |Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0-7| CPU 대 메모리 비율이 적당합니다. 개발/테스트와 소규모에서 중간 정도의 애플리케이션 및 데이터 솔루션에 적합합니다.  |
| [컴퓨팅 최적화](../virtual-machines/sizes-compute.md)   | Fs, F             | CPU 대 메모리 비율이 높습니다. 트래픽이 중간 정도인 애플리케이션, 네트워크 어플라이언스 및 일괄 처리 프로세스에 적합합니다.        |
| [메모리에 최적화](../virtual-machines/sizes-memory.md)    | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | 메모리 대 코어 비율이 높습니다. 관계형 데이터베이스, 중대형 캐시 및 메모리 내 분석에 적합합니다.                 |
| [Storage에 최적화](../virtual-machines/sizes-storage.md)      | Ls                | 높은 디스크 처리량 및 IO 빅 데이터, SQL, NoSQL 데이터베이스에 적합합니다.                                                         |
| [GPU](../virtual-machines/sizes-gpu.md)          | NV, NC            | 대량의 그래픽 렌더링 및 비디오 편집에 적합한 전문 VM입니다.       |
| [고성능](../virtual-machines/sizes-hpc.md) | H, A8-11          | 당사의 가장 강력한 CPU VM으로, 필요한 경우 처리량이 높은 네트워크 인터페이스(RDMA)도 제공합니다. 

### <a name="find-available-vm-instance-sizes"></a>사용 가능한 VM 인스턴스 크기 찾기
특정 지역에서 사용할 수 있는 VM 인스턴스 크기의 목록을 보려면 [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize) 명령을 사용합니다. 

```azurepowershell-interactive
Get-AzVMSize -Location "EastUS"
```

출력은 압축된 다음 예제와 비슷하며, 각 VM 크기에 할당된 리소스를 보여 줍니다.

```powershell
Name                   NumberOfCores MemoryInMB MaxDataDiskCount OSDiskSizeInMB ResourceDiskSizeInMB
----                   ------------- ---------- ---------------- -------------- --------------------
Standard_DS1_v2                    1       3584                4        1047552                 7168
Standard_DS2_v2                    2       7168                8        1047552                14336
[...]
Standard_A0                        1        768                1        1047552                20480
Standard_A1                        1       1792                2        1047552                71680
[...]
Standard_F1                        1       2048                4        1047552                16384
Standard_F2                        2       4096                8        1047552                32768
[...]
Standard_NV6                       6      57344               24        1047552               389120
Standard_NV12                     12     114688               48        1047552               696320
```

자습서의 시작 부분에서 확장 집합을 만들 때 VM 인스턴스에 대해 *Standard_DS1_v2*의 기본 VM SKU가 제공되었습니다. [Get-AzVMSize](/powershell/module/az.compute/get-azvmsize)의 출력에 따라 다른 VM 인스턴스 크기를 지정할 수 있습니다. 다음 예제에서는 *Standard_F1*의 VM 인스턴스 크기를 지정하는 `-VmSize` 매개 변수를 사용하여 확장 집합을 만듭니다. 모든 확장 집합 리소스와 VM 인스턴스를 만들고 구성하는 데 몇 분이 걸리기 때문에 다음 확장 집합을 배포할 필요가 없습니다.

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName "myResourceGroup3" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet3" `
  -VirtualNetworkName "myVnet3" `
  -SubnetName "mySubnet3" `
  -PublicIpAddressName "myPublicIPAddress3" `
  -LoadBalancerName "myLoadBalancer3" `
  -UpgradePolicyMode "Automatic" `
  -VmSize "Standard_F1" `
  -Credential $cred
```


## <a name="change-the-capacity-of-a-scale-set"></a>확장 집합의 용량 변경
확장 집합을 만들 때 두 개의 VM 인스턴스를 요청했습니다. 확장 집합의 VM 인스턴스 수를 늘리거나 줄이려면 용량을 수동으로 변경할 수 있습니다. 확장 집합은 필요한 수의 VM 인스턴스를 만들거나 제거한 다음, 부하 분산 장치에서 트래픽을 분산하도록 구성합니다.

먼저 [Get-AzVmss](/powershell/module/az.compute/get-azvmss)를 포함하는 확장 집합 개체를 만들고 `sku.capacity`에 새 값을 지정합니다. 용량 변경 내용을 적용하려면 [Update-AzVmss](/powershell/module/az.compute/update-azvmss)를 사용합니다. 다음 예제에서는 확장 집합의 VM 인스턴스 수를 *3*으로 설정합니다.

```azurepowershell-interactive
# Get current scale set
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 3
Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss 
```

확장 집합의 용량을 업데이트하는 데 몇 분 정도가 걸립니다. 현재 확장 집합의 인스턴스 수를 보려면 [Get-AzVmss](/powershell/module/az.compute/get-azvmss)를 사용합니다.

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

다음 예제 출력에서는 현재 확장 집합의 용량이 *3*임을 보여 줍니다.

```powershell
Sku        :
  Name     : Standard_DS2
  Tier     : Standard
  Capacity : 3
```


## <a name="common-management-tasks"></a>일반적인 관리 작업
이제 확장 집합을 만들고, 연결 정보를 나열하고, VM 인스턴스에 연결할 수 있습니다. VM 인스턴스에 대해 다른 OS 이미지를 사용하거나, 다른 VM 크기를 선택하거나, 인스턴스 수를 수동으로 조정하는 방법을 알아보았습니다. 일상적인 관리의 일환으로, 확장 집합에서 VM 인스턴스를 중지, 시작 또는 다시 시작해야 할 수 있습니다.

### <a name="stop-and-deallocate-vm-instances-in-a-scale-set"></a>확장 집합에서 VM 인스턴스 중지 및 할당 취소
확장 집합에서 하나 이상의 VM을 중지하려면 [Stop-AzVmss](/powershell/module/az.compute/stop-azvmss)를 사용합니다. `-InstanceId` 매개 변수를 사용하면 하나 이상의 가상 컴퓨터를 중지하도록 지정할 수 있습니다. 인스턴스 ID를 지정하지 않으면 확장 집합에서 모든 VM이 중지됩니다. 다음 예제에서는 *1* 인스턴스를 중지합니다.

```azurepowershell-interactive
Stop-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```

기본적으로 중지된 VM은 할당을 취소하고 컴퓨팅 요금을 부과하지 않습니다. VM이 중지될 때 프로비전된 상태로 유지하려는 경우 `-StayProvisioned` 매개 변수를 위의 명령에 추가합니다. 프로비전된 상태로 유지하는 중지된 VM은 기본 컴퓨팅 요금을 부과합니다.

### <a name="start-vm-instances-in-a-scale-set"></a>확장 집합에서 VM 인스턴스 시작
확장 집합에서 하나 이상의 VM을 시작하려면 [Start-AzVmss](/powershell/module/az.compute/start-azvmss)를 사용합니다. `-InstanceId` 매개 변수를 사용하면 하나 이상의 가상 컴퓨터를 시작하도록 지정할 수 있습니다. 인스턴스 ID를 지정하지 않으면 확장 집합에서 모든 VM이 시작됩니다. 다음 예제에서는 *1* 인스턴스를 시작합니다.

```azurepowershell-interactive
Start-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```

### <a name="restart-vm-instances-in-a-scale-set"></a>확장 집합에서 VM 인스턴스 다시 시작
확장 집합에서 하나 이상의 VM을 다시 시작하려면 [Retart-AzVmss](/powershell/module/az.compute/restart-azvmss)를 사용합니다. `-InstanceId` 매개 변수를 사용하면 하나 이상의 가상 컴퓨터를 다시 시작하도록 지정할 수 있습니다. 인스턴스 ID를 지정하지 않으면 확장 집합에서 모든 VM이 다시 시작됩니다. 다음 예제에서는 *1* 인스턴스를 다시 시작합니다.

```azurepowershell-interactive
Restart-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


## <a name="clean-up-resources"></a>리소스 정리
리소스 그룹을 삭제하면 VM 인스턴스, 가상 네트워크 및 디스크와 같이 포함된 리소스도 모두 삭제됩니다. `-Force` 매개 변수는 작업을 수행하는 추가 프롬프트 없이 리소스를 삭제할 것인지 확인합니다. `-AsJob` 매개 변수는 작업이 완료될 때까지 대기하지 않고 프롬프트로 제어를 반환합니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure PowerShell을 사용하여 기본 확장 집합 만들기 및 관리 작업 일부를 수행하는 방법을 알아보았습니다.

> [!div class="checklist"]
> * 가상 머신 확장 집합 만들기 및 연결
> * VM 이미지 선택 및 사용
> * 특정 VM 크기 보기 및 사용
> * 수동으로 확장 집합 크기 조정
> * 일반적인 확장 집합 관리 작업 수행

확장 집합 디스크에 대해 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [확장 집합으로 데이터 디스크 사용](tutorial-use-disks-powershell.md)
