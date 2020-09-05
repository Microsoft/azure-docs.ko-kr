---
title: 자습서 - Azure PowerShell을 사용하여 자동으로 확장 집합 크기 조정
description: Azure PowerShell을 사용하여 CPU 요구량이 늘거나 줄어듦에 따라 가상 머신 확장 집합의 크기를 자동으로 조정하는 방법을 알아봅니다.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 03/27/2018
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurepowershell
ms.openlocfilehash: 8ee124f866a5241620671ff84c24f3713f62efe1
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078472"
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-azure-powershell"></a>자습서: Azure PowerShell을 사용하여 가상 머신 확장 집합의 크기를 자동으로 조정

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

확장 집합을 만들 때 실행하려는 VM 인스턴스 수를 정의합니다. 애플리케이션 수요가 변경될 때는 VM 인스턴스 수를 자동으로 늘리거나 줄일 수 있습니다. 자동 크기 조정 기능을 사용하면 고객 수요에 따라 조정하거나 앱 수명 주기 동안 애플리케이션 성능 변화에 대응할 수 있습니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 확장 집합에 자동 크기 조정 사용
> * 자동 크기 조정 규칙 만들기 및 사용
> * VM 인스턴스 스트레스 테스트 및 자동 크기 조정 규칙 트리거
> * 요구량이 줄면 자동으로 다시 크기 조정

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

Azure Cloud Shell 현재 버전을 포함하여 Azure PowerShell 모듈 버전 6.8.1 이상에 영향을 주는 알려진 문제가 있습니다. 이 자습서는 Azure PowerShell 모듈 6.0.0-6.8.0 버전에서만 실행할 수 있습니다. `Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzureRmAccount`를 실행하여 Azure와 연결해야 합니다.


## <a name="create-a-scale-set"></a>확장 집합 만들기
자동 크기 조정 규칙을 보다 쉽게 만들려면 확장 집합에 대해 몇 개의 변수를 정의합니다. 다음 예제에서는 *myResourceGroup*이라는 리소스 그룹과 *East US* 지역에 있는 *myScaleSet*라는 확장 집합에 대한 변수를 정의합니다. 구독 ID는 [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription)을 사용하여 가져옵니다. 사용자 계정에 여러 구독이 연결되어 있으면 첫 번째 구독만 반환됩니다. 이름과 구독 ID를 다음과 같이 조정합니다.

```azurepowershell-interactive
$mySubscriptionId = (Get-AzureRmSubscription)[0].Id
$myResourceGroup = "myResourceGroup"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
```

이제 [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss)를 사용하여 가상 머신 확장 집합을 만듭니다. 트래픽을 개별 VM 인스턴스로 배포하기 위해 부하 분산 장치도 생성됩니다. 부하 분산 장치에는 80 TCP 포트에서 트래픽을 분산할 뿐만 아니라 3389 TCP 포트의 원격 데스크톱 트래픽 및 5985 TCP 포트의 PowerShell 원격을 허용하는 규칙이 포함되어 있습니다. 메시지가 표시되면 확장 집합에서 VM 인스턴스에 대해 원하는 관리 자격 증명을 제공합니다.

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName $myResourceGroup `
  -VMScaleSetName $myScaleSet `
  -Location $myLocation `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer"
```

확장 집합 리소스와 VM을 모두 만들고 구성하는 데 몇 분 정도 걸립니다.

## <a name="create-a-rule-to-autoscale-out"></a>자동 크기 확장 규칙 만들기
애플리케이션 수요가 증가하면 확장 집합의 VM 인스턴스 부하가 증가합니다. 증가된 로드가 단순한 요구가 아닌 일관된 요구인 경우 확장 집합의 VM 인스턴스 수를 늘리도록 자동 크기 조정 규칙을 구성할 수 있습니다. 이러한 VM 인스턴스를 만들고 애플리케이션을 배포하면 확장 집합이 부하 분산 장치를 통해 트래픽을 분산하기 시작합니다. 모니터링할 메트릭(예: CPU 또는 디스크), 애플리케이션 로드가 지정된 임계값을 충족해야 하는 기간, 확장 집합에 추가할 VM 인스턴스 수를 제어합니다.

평균 CPU 로드가 5분간 70%를 초과할 경우 [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule)을 사용하여 확장 집합의 VM 인스턴스 수를 늘리는 규칙을 만들어 보겠습니다. 규칙이 트리거되면 VM 인스턴스 수가 3만큼 늘어납니다.

이 규칙에 사용되는 매개 변수는 다음과 같습니다.

| 매개 변수               | 설명                                                                                                         | 값          |
|-------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
| *-MetricName*           | 확장 집합 작업을 모니터링하고 적용하기 위한 성능 메트릭입니다.                                                   | 백분율 CPU |
| *-TimeGrain*            | 분석을 위해 메트릭이 수집되는 빈도입니다.                                                                   | 1분       |
| *-MetricStatistic*      | 분석을 위해 수집된 메트릭을 집계하는 방법을 정의합니다.                                                | 평균        |
| *-TimeWindow*           | 메트릭과 임계값을 비교하기 전에 모니터링하는 기간입니다.                                   | 5분      |
| *-Operator*             | 메트릭 데이터를 임계값과 비교하는 데 사용되는 연산자입니다.                                                     | 다음보다 큼   |
| *-Threshold*            | 자동 크기 조정 규칙이 작업을 트리거하도록 하는 값입니다.                                                      | 70%            |
| *-ScaleActionDirection* | 규칙이 적용될 때 확장 집합이 확장 또는 축소되어야 하는지를 정의합니다.                                             | 증가       |
| *-ScaleActionScaleType* | VM 인스턴스 수를 특정 값으로 변경해야 함을 나타냅니다.                                    | 변경 수   |
| *-ScaleActionValue*     | 규칙이 트리거되면 VM 인스턴스의 백분율을 변경해야 합니다.                                            | 3              |
| *-ScaleActionCooldown*  | 자동 크기 조정 작업이 적용될 시간을 주기 위해 규칙을 다시 적용하기 전에 대기할 시간입니다. | 5분      |

다음 예제에서는 이 확장 규칙이 적용되는 *myRuleScaleOut*이라는 개체를 생성합니다. *-MetricResourceId*는 구독 ID, 리소스 그룹 이름 및 확장 집합 이름에 대해 이전에 정의된 변수를 사용합니다.

```azurepowershell-interactive
$myRuleScaleOut = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -TimeGrain 00:01:00 `
  -MetricStatistic "Average" `
  -TimeWindow 00:05:00 `
  -Operator "GreaterThan" `
  -Threshold 70 `
  -ScaleActionDirection "Increase" `
  -ScaleActionScaleType "ChangeCount" `
  -ScaleActionValue 3 `
  -ScaleActionCooldown 00:05:00
```


## <a name="create-a-rule-to-autoscale-in"></a>자동 크기 축소 규칙 만들기
저녁이나 주말에는 애플리케이션 수요가 줄어들 수 있습니다. 이 감소된 로드가 일정 기간 동안 일관성 있게 유지될 경우 확장 집합의 VM 인스턴스 수를 줄이도록 자동 크기 조정 규칙을 구성할 수 있습니다. 이 규모 감축 작업은 현재 수요를 충족하는 데 필요한 수의 인스턴스만 실행하므로 확장 집합의 실행 비용을 줄입니다.

평균 CPU 로드가 5분 동안 30% 미만일 경우 [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule)을 사용하여 확장 집합의 VM 인스턴스 수를 줄이는 다른 규칙을 만듭니다. 규칙이 트리거되면 VM 인스턴스 수가 하나씩 감소합니다. 다음 예제에서는 이 축소 규칙이 적용되는 *myRuleScaleDown*이라는 개체를 생성합니다. *-MetricResourceId*는 구독 ID, 리소스 그룹 이름 및 확장 집합 이름에 대해 이전에 정의된 변수를 사용합니다.

```azurepowershell-interactive
$myRuleScaleIn = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -Operator "LessThan" `
  -MetricStatistic "Average" `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection "Decrease" `
  -ScaleActionScaleType "ChangeCount" `
  -ScaleActionValue 1
```


## <a name="define-an-autoscale-profile"></a>자동 크기 조정 프로필 정의
자동 크기 조정 규칙을 확장 집합과 연결하려면 프로필을 만듭니다. 자동 크기 조정 프로필은 기본, 최소, 최대 확장 집합 용량을 정의하고 자동 크기 조정 규칙을 연결합니다. [New-AzureRmAutoscaleProfile](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleProfile)을 사용하여 자동 크기 조정 프로필을 만듭니다. 다음 예제에서는 기본 및 최소 용량으로 VM 인스턴스 *2*개, 최대 용량으로 *10*개를 설정합니다. 그런 다음, 이전 단계에서 만든 규모 확장 및 규모 감축 규칙이 연결됩니다.

```azurepowershell-interactive
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rule $myRuleScaleOut,$myRuleScaleIn `
  -Name "autoprofile"
```


## <a name="apply-autoscale-profile-to-a-scale-set"></a>확장 집합에 자동 크기 조정 프로필 적용
마지막 단계는 확장 집합에 자동 크기 조정 프로필을 적용하는 것입니다. 그러면 확장 집합이 애플리케이션 요구량에 따라 자동으로 확장되거나 축소될 수 있습니다. [Add-AzureRmAutoscaleSetting](/powershell/module/AzureRM.Insights/Add-AzureRmAutoscaleSetting)을 사용하여 자동 크기 조정 프로필을 다음과 같이 적용합니다.

```azurepowershell-interactive
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -AutoscaleProfile $myScaleProfile
```


## <a name="generate-cpu-load-on-scale-set"></a>확장 집합에 CPU 로드 생성
자동 크기 조정 규칙을 테스트하려면 확장 집합의 VM 인스턴스에 약간의 CPU 로드를 생성합니다. 시뮬레이션된 CPU 로드로 인해 자동 크기 조정 규칙이 확장되고 VM 인스턴스 수가 늘어납니다. 시뮬레이션된 CPU 로드가 감소하면 자동 크기 조정 규칙이 축소되고 VM 인스턴스 수가 줄어듭니다.

확장 집합의 VM 인스턴스에 연결할 NAT 포트를 나열하려면, 먼저 [Get-AzureRmLoadBalancer](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancer)를 사용하여 부하 분산 장치 개체를 가져옵니다. 그런 다음, [Get-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancerInboundNatRuleConfig)를 사용하여 인바운드 NAT 규칙을 봅니다.

```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzureRmLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzureRmLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort
```

다음 예제 출력에서는 NAT 규칙에서 트래픽을 전달하는 인스턴스 이름, 부하 분산 장치의 공용 IP 주소 및 포트 번호를 보여 줍니다.

```powershell
Name        Protocol FrontendPort BackendPort
----        -------- ------------ -----------
myRDPRule.0 Tcp             50001        3389
myRDPRule.1 Tcp             50002        3389
```

규칙의 *이름*은 이전 [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) 명령에 표시된 VM 인스턴스의 이름과 일치합니다. 예를 들어 *0* VM 인스턴스에 연결하려면 *myRDPRule.0*을 사용하고 *50001* 포트에 연결합니다. *1* VM 인스턴스에 연결하려면 *myRDPRule.1*의 값을 사용하고 *50002* 포트에 연결합니다.

[Get-AzureRmPublicIpAddress](/powershell/module/AzureRM.Network/Get-AzureRmPublicIpAddress)를 사용하여 부하 분산 장치의 공용 IP 주소를 봅니다.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" -Name myPublicIP | Select IpAddress
```

예제 출력:

```powershell
IpAddress
---------
52.168.121.216
```

첫 번째 VM 인스턴스에 대한 원격 연결을 만듭니다. 앞의 명령과 같이 필요한 VM 인스턴스의 고유한 공용 IP 주소와 포트 번호를 지정합니다. 메시지가 표시되면 확장 집합을 만들 때 사용한 자격 증명을 입력합니다(샘플 명령의 경우 기본적으로 *azureuser* 및 *P\@ssw0rd!* 임). Azure Cloud Shell을 사용하는 경우 로컬 PowerShell 프롬프트 또는 원격 데스크톱 클라이언트에서 이 단계를 수행합니다. 다음 예제에서는 *0* VM 인스턴스에 연결합니다.

```powershell
mstsc /v 52.168.121.216:50001
```

로그인한 후 작업 표시줄에서 Internet Explorer를 엽니다.

- **확인**을 선택하여 *권장되는 보안, 개인 정보 및 호환성 설정을 사용*하도록 요구하는 메시지를 수락합니다.
- 주소 표시줄에 *http://download.sysinternals.com/files/CPUSTRES.zip* 을 입력합니다.
- Internet Explorer 보안 강화 구성을 사용하도록 설정하면 *http://download.sysinternals.com* 도메인을 신뢰할 수 있는 사이트 목록에 **추가**하도록 선택합니다.
- 파일 다운로드를 묻는 메시지가 표시되면 **열기**를 선택한 다음, *CPUSTRES.EXE* 도구를 선택하고 **실행**합니다.

약간의 CPU 로드를 생성하려면 **활성** 스레드에 대한 두 개의 확인란을 선택합니다. 두 스레드에 대한 **활동** 드롭다운 메뉴에서 *최대*를 선택합니다. [작업 관리자]를 열어 VM의 CPU 로드가 100%인지 확인할 수 있습니다.

![CPU 스트레스 유틸리티에서 VM 인스턴스에 로드를 생성합니다.](media/tutorial-autoscale-powershell/cpu-stress-load.PNG)

원격 데스크톱 연결 세션을 열어 둔 채 다른 원격 데스크톱 연결 세션을 엽니다. 이전 [Get-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancerInboundNatRuleConfig) cmdlet에서 나열된 포트 번호를 사용하여 두 번째 VM 인스턴스에 연결합니다.

```powershell
mstsc /v 52.168.121.216:50002
```

두 번째 VM 인스턴스에 로그인한 후 이전 단계를 반복하여 *CPUSTRES.EXE*를 다운로드하고 실행합니다. 다시 한 번, 두 개의 **활성** 스레드를 시작하고 활동을 *최대*로 설정합니다.

**CPU 스트레스** 도구가 계속 실행되도록 하려면 두 원격 데스크톱 연결 세션을 모두 열어 둡니다.


## <a name="monitor-the-active-autoscale-rules"></a>활성 자동 크기 조정 규칙 모니터링
확장 집합의 VM 인스턴스 수를 모니터링하려면 **while**을 사용합니다. 각 VM 인스턴스의 **CPUStress**에서 생성된 CPU 로드에 응답하여 자동 크기 조정 확장 집합에서 규모 확장 프로세스를 시작하는 데 5분이 걸립니다.

```azurepowershell-interactive
while (1) {Get-AzureRmVmssVM `
    -ResourceGroupName $myResourceGroup `
    -VMScaleSetName $myScaleSet; sleep 10}
```

CPU 임계값이 충족되면 자동 크기 조정 규칙에서 확장 집합의 VM 인스턴스 수를 늘립니다. 다음 출력에서는 확장 집합의 크기가 자동으로 확장함에 따라 생성된 세 개의 VM을 보여 줍니다.

```powershell
ResourceGroupName         Name Location          Sku Capacity InstanceID ProvisioningState
-----------------         ---- --------          --- -------- ---------- -----------------
MYRESOURCEGROUP   myScaleSet_2   eastus Standard_DS2                   2         Succeeded
MYRESOURCEGROUP   myScaleSet_3   eastus Standard_DS2                   3         Succeeded
MYRESOURCEGROUP   myScaleSet_4   eastus Standard_DS2                   4          Creating
MYRESOURCEGROUP   myScaleSet_5   eastus Standard_DS2                   5          Creating
MYRESOURCEGROUP   myScaleSet_6   eastus Standard_DS2                   6          Creating
```

각 VM 인스턴스에 대한 원격 데스크톱 연결 세션에서 **CPU 스트레스** 도구를 닫습니다. 확장 집합에 걸친 평균 CPU 로드가 정상으로 돌아갑니다. 또 다른 5분이 지나면 자동 크기 조정 규칙에서 VM 인스턴스 수를 축소합니다. 규모 감축 작업에서 가장 높은 ID가 있는 VM 인스턴스를 먼저 제거합니다. 확장 집합에서 가용성 집합 또는 가용성 영역을 사용하는 경우 규모 감축 작업은 해당 VM 인스턴스 간에 균등하게 분산됩니다. 다음 예제 출력에서는 확장 집합의 자동 크기를 확장하면서 삭제된 하나의 VM 인스턴스를 보여 줍니다.

```powershell
MYRESOURCEGROUP   myScaleSet_6   eastus Standard_DS2                   6          Deleting
```

`Ctrl-c`를 사용하여 *while*을 종료합니다. 확장 집합은 5분마다 계속 축소되며, 최소 인스턴스 수인 2에 도달할 때까지 하나의 VM 인스턴스를 제거합니다.


## <a name="clean-up-resources"></a>리소스 정리
확장 집합 및 추가 리소스를 제거하려면 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup)을 사용하여 리소스 그룹 및 모든 해당 리소스를 삭제합니다. `-Force` 매개 변수는 작업을 수행하는 추가 프롬프트 없이 리소스를 삭제할 것인지 확인합니다. `-AsJob` 매개 변수는 작업이 완료될 때까지 대기하지 않고 프롬프트로 제어를 반환합니다.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure PowerShell을 사용하여 다음과 같이 확장 집합을 자동으로 확장하거나 축소하는 방법을 알아보았습니다.

> [!div class="checklist"]
> * 확장 집합에 자동 크기 조정 사용
> * 자동 크기 조정 규칙 만들기 및 사용
> * VM 인스턴스 스트레스 테스트 및 자동 크기 조정 규칙 트리거
> * 요구량이 줄면 자동으로 다시 크기 조정
