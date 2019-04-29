---
title: Site Recovery에서 복구 계획에 Azure Automation Runbook 추가 | Microsoft Docs
description: Azure Site Recovery를 통한 재해 복구를 위해 Azure Automation을 사용하여 복구 계획을 확장하는 방법에 대해 알아봅니다.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 26c3466080cb356ca3610d42eaaf5ee4975d3731
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61471951"
---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>복구 계획에 Azure Automation Runbook 추가
이 문서에서는 Azure Site Recovery를 Azure Automation에 통합하여 복구 계획을 확장하는 방법을 설명합니다. 복구 계획으로 Site Recovery로 보호되는 VM의 복구를 오케스트레이션할 수 있습니다. 복구 계획은 보조 클라우드로 복제 및 Azure로의 복제 모두에서 작동합니다. 복구 계획을 통해 복구를 **일관적으로 정확**하고, **반복 가능**하며, **자동화**되도록 할 수도 있습니다. VM을 Azure로 장애 조치(failover)하는 경우 Azure Automation과 통합하면 복구 계획이 확장됩니다. 이를 통해 강력한 자동화 작업을 제공하는 Runbook을 실행할 수 있습니다.

Azure Automation을 처음 접하는 경우 [등록](https://azure.microsoft.com/services/automation/)하여 [샘플 스크립트를 다운로드](https://azure.microsoft.com/documentation/scripts/)할 수 있습니다. [복구 계획](./site-recovery-create-recovery-plans.md)을 사용하여 Azure에 복구를 오케스트레이션하는 방법에 대한 자세한 내용은 [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/)를 참조하세요.

이 문서에서는 Azure Automation Runbook을 복구 계획에 어떻게 통합할 수 있는지 설명합니다. 이전에는 수동 개입이 필요했던 기본 작업을 자동화하는 예제를 사용합니다. 또한 다단계 복구를 단일 클릭 복구 작업으로 변환하는 방법도 설명합니다.

## <a name="customize-the-recovery-plan"></a>복구 계획 사용자 지정
1. **Site Recovery** 복구 계획 리소스 블레이드로 이동합니다. 이 예제의 경우 복구 계획에 두 개의 VM이 복구용으로 추가되어 있습니다. Runbook 추가를 시작하려면 **사용자 지정** 탭을 클릭합니다.

    ![[사용자 지정] 단추 클릭](media/site-recovery-runbook-automation-new/essentials-rp.png)


2. **그룹 1: 시작**을 마우스 오른쪽 단추로 클릭한 후 **사후 작업 추가**를 선택합니다.

    ![그룹 1: 시작을 마우스 오른쪽 단추로 클릭하고 사후 작업 추가](media/site-recovery-runbook-automation-new/customize-rp.png)

3. **스크립트 선택**을 클릭합니다.

4. **업데이트 작업** 블레이드에서 스크립트 이름을 **Hello World**로 지정합니다.

    ![업데이트 작업 블레이드](media/site-recovery-runbook-automation-new/update-rp.png)

5. Automation 계정 이름을 입력합니다.
    >[!NOTE]
    > Automation 계정은 모든 Azure 지역에 있을 수 있습니다. Automation 계정은 Azure Site Recovery 자격 증명 모음과 동일한 구독에 있어야 합니다.

6. Automation 계정에서 Runbook을 선택합니다. 이 Runbook은 첫 번째 그룹을 복구한 후에 복구 계획을 실행하는 동안 실행할 스크립트입니다.

7. 스크립트를 저장하려면 **확인**을 클릭합니다. 스크립트가 **그룹 1: 사후 단계**에 추가됩니다.

    ![사후 작업 그룹 1: 시작](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="considerations-for-adding-a-script"></a>스크립트를 추가하기 위한 고려 사항

* **단계 삭제** 또는 **스크립트 업데이트**에 대한 옵션에서 스크립트를 마우스 오른쪽 단추로 클릭합니다.
* 스크립트는 온-프레미스 컴퓨터에서 Azure로 장애 조치하는 동안 Azure에서 실행할 수 있습니다. 또한 Azure에서 온-프레미스 컴퓨터로 장애 복구(failback)하는 동안 종료하기 전에 주 사이트 스크립트로 Azure에서 실행할 수도 있습니다.
* 스크립트가 실행되면 복구 계획 컨텍스트가 삽입됩니다. 다음 예제는 컨텍스트 변수를 보여줍니다.

    ```
            {"RecoveryPlanName":"hrweb-recovery",

            "FailoverType":"Test",

            "FailoverDirection":"PrimaryToSecondary",

            "GroupId":"1",

            "VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":

                    { "SubscriptionId":"7a1111111-c1d6-49c5-8c5d-111ce8dd183",

                    "ResourceGroupName":"ContosoRG",

                    "CloudServiceName":"pod02hrweb-Chicago-test",

                    "RoleName":"Fabrikam-Hrweb-frontend-test",

                    "RecoveryPointId":"TimeStamp"}

                    }

            }
    ```

    다음 표에는 컨텍스트의 각 변수에 대한 이름과 설명이 나열되어 있습니다.

    | **변수 이름** | **설명** |
    | --- | --- |
    | RecoveryPlanName |실행되는 계획의 이름입니다. 이 변수를 사용하면 복구 계획 이름에 따라 서로 다른 작업을 수행할 수 있습니다. 또한 스크립트 다시 사용할 수 있습니다. |
    | FailoverType |장애 조치(failover)가 테스트, 계획됨 또는 계획되지 않음인지 지정합니다. |
    | FailoverDirection |복구가 주 사이트 쪽으로 이루어지는지 보조 사이트 쪽으로 이루어지는지 지정합니다. |
    | GroupID |계획이 실행 중일 때 복구 계획에서 그룹 번호를 식별합니다. |
    | VmMap |그룹의 모든 VM 배열입니다. |
    | VMMap key |각 VM에 대한 고유 키(GUID)입니다. 해당되는 경우 VM의 Azure VMM(Virtual Machine Manager) ID와 동일합니다. |
    | SubscriptionId |VM이 만들어진 Azure 구독 ID입니다. |
    | RoleName |복구 중인 Azure VM의 이름입니다. |
    | CloudServiceName |VM이 만들어진 Azure 클라우드 서비스 이름입니다. |
    | ResourceGroupName|VM이 만들어진 Azure 리소스 그룹 이름입니다. |
    | RecoveryPointId|VM이 복구될 때의 타임스탬프입니다. |

* Automation 계정에 다음 모듈이 있는지 확인합니다.
    * AzureRM.profile
    * AzureRM.Resources
    * AzureRM.Automation
    * AzureRM.Network
    * AzureRM.Compute

모든 모듈은 호환되는 버전이어야 합니다. 모든 모듈이 호환되는지 확인하는 간단한 방법은 모든 모듈에 대한 최신 버전을 사용하는 것입니다.

### <a name="access-all-vms-of-the-vmmap-in-a-loop"></a>루프에서 VMMap의 모든 VM 액세스
다음 코드를 사용하여 Microsoft VMMap의 모든 VM을 반복합니다.

```
$VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
$vmMap = $RecoveryPlanContext.VmMap
 foreach($VMID in $VMinfo)
 {
     $VM = $vmMap.$VMID                
             if( !(($VM -eq $Null) -Or ($VM.ResourceGroupName -eq $Null) -Or ($VM.RoleName -eq $Null))) {
         #this check is to ensure that we skip when some data is not available else it will fail
 Write-output "Resource group name ", $VM.ResourceGroupName
 Write-output "Rolename " = $VM.RoleName
     }
 }

```

> [!NOTE]
> 스크립트가 부팅 그룹에 대한 사전 작업인 경우 리소스 그룹 이름과 역할 이름 값이 비어 있습니다. 해당 그룹의 VM이 성공적으로 장애 조치되는 경우에만 값이 채워집니다. 이 스크립트는 부팅 그룹의 사후 작업입니다.

## <a name="use-the-same-automation-runbook-in-multiple-recovery-plans"></a>여러 복구 계획에 동일한 Automation Runbook 사용

단일 스크립트는 외부 변수를 사용하여 여러 복구 계획에서 사용할 수 있습니다. [Azure Automation 변수](../automation/automation-variables.md)를 사용하여 복구 계획 실행을 위해 전달할 수 있는 매개 변수를 저장할 수 있습니다. 복구 계획 이름을 변수에 대한 접두사로 추가하여 각 복구 계획에 대한 개별 변수를 만들 수 있습니다. 그런 다음 매개 변수로 변수를 사용합니다. 스크립트를 변경하지 않고 매개 변수를 변경할 수 있으며 스크립트가 작동하는 방식도 변경할 수 있습니다.

### <a name="use-a-simple-string-variable-in-a-runbook-script"></a>Runbook 스크립트에서 단순 문자열 변수 사용

이 예제에서 스크립트는 NSG(네트워크 보안 그룹)의 입력을 받아서 복구 계획의 VM에 적용합니다.

스크립트에서 실행 중인 복구 계획을 검색할 수 있도록 복구 계획 컨텍스트를 사용합니다.

```
workflow AddPublicIPAndNSG {
    param (
          [parameter(Mandatory=$false)]
          [Object]$RecoveryPlanContext
    )

    $RPName = $RecoveryPlanContext.RecoveryPlanName
```

기존 NSG를 적용하려면 NSG 이름과 NSG 리소스 그룹 이름을 알아야 합니다. 복구 계획 스크립트에 대한 입력으로 이러한 변수를 사용합니다. 이렇게 하려면 Automation 계정 자산에서 두 개의 변수를 만듭니다. 변수 이름에 대한 접두사로 매개 변수를 생성 중인 복구 계획의 이름을 추가합니다.

1. NSG 이름을 저장할 변수를 만듭니다. 복구 계획의 이름을 사용하여 변수 이름에 접두사를 추가합니다.

    ![NSG 이름 변수 만들기](media/site-recovery-runbook-automation-new/var1.png)

2. NSG의 리소스 그룹 이름을 저장할 변수를 만듭니다. 복구 계획의 이름을 사용하여 변수 이름에 접두사를 추가합니다.

    ![NSG 리소스 그룹 이름 만들기](media/site-recovery-runbook-automation-new/var2.png)


3.  스크립트에서 다음 참조 코드를 사용하여 변수 값을 가져옵니다.

    ```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
    ```

4.  Runbook에서 변수를 사용하여 장애 조치된 VM의 네트워크 인터페이스에 NSG를 적용합니다.

    ```
    InlineScript {
    if (($Using:NSGname -ne $Null) -And ($Using:NSGRGname -ne $Null)) {
            $NSG = Get-AzureRmNetworkSecurityGroup -Name $Using:NSGname -ResourceGroupName $Using:NSGRGname
            Write-output $NSG.Id
            #Apply the NSG to a network interface
            #$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
            #Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
            #  -AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $NSG
        }
    }
    ```

각 복구 계획마다 개별 변수를 만들어 스크립트를 다시 사용할 수 있도록 합니다. 복구 계획 이름을 사용하여 접두사를 추가합니다. 이 시나리오에 대한 종단 간 전체 스크립트는 [Site Recovery 복구 계획의 테스트 장애 조치 중에 VM에 공용 IP 및 NSG 추가](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee)를 참조하세요.


### <a name="use-a-complex-variable-to-store-more-information"></a>복합 변수를 사용하여 자세한 정보 저장

단일 스크립트에서 특정 VM의 공용 IP를 설정하는 시나리오를 고려해 보세요. 다른 시나리오에서는 서로 다른 VM(모든 VM 아님)에는 다른 NSG를 적용하려고 합니다. 모든 복구 계획에 다시 사용할 수 있는 스크립트를 만들 수 있습니다. 각 복구 계획에는 다양한 수의 VM이 포함될 수 있습니다. 예를 들어 SharePoint 복구에는 두 개의 프런트 엔드가 있습니다. 기본 LOB(기간 업무) 애플리케이션에는 하나의 프런트 엔드만 있습니다. 각 복구 계획에 별도의 변수를 만들 수 없습니다.

다음 예제에서는 Azure Automation 계정 자산에서 새 기술을 사용하고 [복합 변수](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureautomationvariable)를 만듭니다. 여러 값을 지정하여 이 작업을 수행합니다. 다음 단계를 완료하려면 Azure PowerShell을 사용해야 합니다.

1. PowerShell에서 Azure 구독에 로그인합니다.

    ```
    Connect-AzureRmAccount
    $sub = Get-AzureRmSubscription -Name <SubscriptionName>
    $sub | Select-AzureRmSubscription
    ```

2. 매개 변수를 저장하려면 복구 계획의 이름을 사용하여 복합 변수를 만듭니다.

    ```
    $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

3. 이 복합 변수에서 **VMDetails**는 보호된 VM의 VM ID입니다. Azure Portal에서 VM ID를 가져오려면 VM 속성을 봅니다. 다음 스크린샷은 두 VM의 세부 정보를 저장하는 변수를 보여 줍니다.

    ![VM ID를 GUID로 사용](media/site-recovery-runbook-automation-new/vmguid.png)

4. Runbook에서 이 변수를 사용합니다. 표시된 VM GUID가 복구 계획 컨텍스트에 있으면 VM에서 NSG를 적용합니다.

    ```
    $VMDetailsObj = (Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName).ToObject([hashtable])
    ```

4. Runbook에서 복구 계획 컨텍스트 VM을 반복합니다. **$VMDetailsObj**에서 VM의 존재 여부를 확인합니다. 존재하는 경우 변수 속성에 액세스하여 NSG를 적용합니다.

    ```
        $VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
        $vmMap = $RecoveryPlanContext.VmMap

        foreach($VMID in $VMinfo) {
            $VMDetails = $VMDetailsObj[$VMID].ToObject([hashtable]);
            Write-output $VMDetails
            if ($VMDetails -ne $Null) { #If the VM exists in the context, this will not be Null
                $VM = $vmMap.$VMID
                # Access the properties of the variable
                $NSGname = $VMDetails.NSGName
                $NSGRGname = $VMDetails.NSGResourceGroupName

                # Add code to apply the NSG properties to the VM
            }
        }
    ```

다른 복구 계획에 대해 동일한 스크립트를 사용할 수 있습니다. 다른 변수에서 복구 계획에 해당하는 값을 저장하여 서로 다른 매개 변수를 입력합니다.

## <a name="sample-scripts"></a>샘플 스크립트

Automation 계정에 샘플 스크립트를 배포하려면 **Azure에 배포** 단추를 클릭합니다.

[![Azure에 배포](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

다른 예제를 보려면 다음 비디오를 시청하세요. Azure로 2계층 WordPress 애플리케이션을 복구하는 방법을 보여줍니다.


> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]


## <a name="additional-resources"></a>추가 리소스
* [Azure Automation 서비스 실행 계정](../automation/automation-create-runas-account.md)
* [Azure Automation 개요](https://msdn.microsoft.com/library/azure/dn643629.aspx "Azure Automation 개요")
* [Azure Automation 샘플 스크립트](https://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=User&f\[0\].Value=SC%20Automation%20Product%20Team&f\[0\].Text=SC%20Automation%20Product%20Team "Azure Automation 샘플 스크립트")

## <a name="next-steps"></a>다음 단계
장애 조치를 실행하는 방법에 대해 [자세히 알아보세요](site-recovery-failover.md).
