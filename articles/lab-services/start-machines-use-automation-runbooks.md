---
title: Azure DevTest Labs에서 Automation runbook을 사용 하 여 컴퓨터를 시작 합니다. | Microsoft Docs
description: Azure Automation runbook을 사용 하 여 Azure DevTest Labs의 랩에서 가상 머신을 시작 하는 방법에 알아봅니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: spelluru
ms.openlocfilehash: 8d3885ba25e479316f97ecbb0681a1680650fc09
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61083621"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>Azure Automation runbook을 사용 하 여 순서 대로 랩에서 가상 머신을 시작합니다
합니다 [autostart](devtest-lab-set-lab-policy.md#set-autostart) DevTest Labs의 기능을 사용 하면 지정된 된 시간에 자동으로 시작 되도록 Vm을 구성할 수 있습니다. 그러나이 기능은 특정 순서 대로 시작 하려면 컴퓨터를 지원 하지 않습니다. 이 유형의 자동화도 유용 하다는 몇 가지 시나리오가 있습니다.  하나의 시나리오 랩 내에서 Jumpbox VM 해야 하는 다른 Vm과 하기 전에 먼저 시작할 Jumpbox를 다른 Vm에 대 한 액세스 지점으로 사용 되는 것입니다.  이 문서에서는 스크립트를 실행 하는 PowerShell runbook 사용 하 여 Azure Automation 계정을 설정 하는 방법을 보여 줍니다. 스크립트를 변경 하지 않고도 시작 순서를 제어할 수 있도록 랩의 Vm에 태그를 사용 하는 스크립트입니다.

## <a name="setup"></a>설정
이 예제에서는 랩의 Vm 해야 태그가 **StartupOrder** 적절 한 값을 사용 하 여 추가 (0,1,2, 등.). -1로 시작 될 필요가 있는 모든 컴퓨터를 지정 합니다.

## <a name="create-an-azure-automation-account"></a>Azure Automation 계정 만들기
지침에 따라 Azure Automation 계정 만들기 [이 문서에서는](../automation/automation-create-standalone-account.md)합니다. 선택 된 **실행 계정** 계정을 만들 때 옵션입니다. Automation 계정이 만들어지면 엽니다는 **모듈** 페이지에서 선택한 **Azure 모듈 업데이트** 메뉴 모음에서. 기본 모듈은 이전 하지 않고 스크립트를 업데이트 하는 여러 버전 작동 하지 않을 수 있습니다.

## <a name="add-a-runbook"></a>Runbook 추가
이제 runbook을 automation 계정에 추가할 선택할 **Runbook** 왼쪽된 메뉴에서. 선택 **runbook 추가** 메뉴에서을 지침에 따라 [PowerShell runbook 만들기](../automation/automation-first-runbook-textual-powershell.md)합니다.

## <a name="powershell-script"></a>PowerShell 스크립트
다음 스크립트는 구독 이름, 랩 이름을 매개 변수로 사용 합니다. 스크립트의 흐름 랩에서 모든 Vm을 가져오고 다음 VM 이름 및 시작 순서 목록을 만드는 데 필요한 태그 정보를 구문 분석 하는 것입니다. 스크립트를 순서 대로 Vm을 안내 하 고 Vm을 시작 합니다. 특정 주문 번호에 여러 Vm이 없으면 PowerShell 작업을 사용 하 여 비동기적으로 시작 됩니다. 집합 시작 값 (10), 마지막 태그 되지 않은 해당 Vm에 대 한 이러한 시작 됩니다 마지막으로, 기본적으로.  랩 VM을 자동으로 시작할 수 합니다 11 태그 값을 설정 하 고 무시 됩니다.

```powershell
#Requires -Version 3.0
#Requires -Module AzureRM.Resources

param
(
    [Parameter(Mandatory=$false, HelpMessage="Name of the subscription that has the lab")]
    [string] $SubscriptionName,

    [Parameter(Mandatory=$false, HelpMessage="Lab name")]
    [string] $LabName
)

# Connect and add the appropriate subscription
$Conn = Get-AutomationConnection -Name AzureRunAsConnection

Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationId -Subscription $SubscriptionName -CertificateThumbprint $Conn.CertificateThumbprint

# Find the lab
$dtLab = Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $LabName

# Get the VMs
$dtlAllVms = New-Object System.Collections.ArrayList
$AllVMs = Get-AzResource -ResourceId "$($dtLab.ResourceId)/virtualmachines" -ApiVersion 2016-05-15

# Get the StartupOrder tag, if missing set to be run last (10)
ForEach ($vm in $AllVMs) {
    if ($vm.Tags) {
        if ($vm.Tags['StartupOrder']) {
            $startupValue = $vm.Tags['StartupOrder']
        } else {
            $startupValue = 10
        }
        } else {
            $startupValue = 10
        }
        $dtlAllVms.Add(@{$vm.Name = $startupValue}) > $null
}

# Setup for the async multiple vm start

# Save profile
$profilePath = Join-Path $env:Temp "profile.json"
If (Test-Path $profilePath){
    Remove-Item $profilePath
}
Save-AzContext -Path $profilePath

# Job to start VMs asynch
$startVMBlock = {
    Param($devTestLab,$vmToStart,$profilePath)
    Import-AzContext -Path ($profilePath)
    Invoke-AzResourceAction `
        -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vmToStart" `
        -Action Start `
        -Force
    Write-Output "Started: $vmToStart"
}

$current = 0
# Start in order from 0 to 10

While ($current -le 10) {
# Get the VMs in the current stage
    $tobeStarted = $null
    $tobeStarted = $dtlAllVms | Where-Object { $_.Values -eq $current}
    if ($tobeStarted.Count -eq 1) {
        # Run sync – jobs not necessary for a single VM
        $returnStatus = Invoke-AzResourceAction `
                -ResourceId "$($dtLab.ResourceId)/virtualmachines/$($tobeStarted.Keys)" `
                -Action Start `
                -Force
        Write-Output "$($tobeStarted.Keys) status: $($returnStatus.status)"
    } elseif ($tobeStarted.Count -gt 1) {
        # Start multiple VMs async
        $jobs = @()
        Write-Output "Start Jobs start: $(Get-Date)"
        
        # Jobs
        $jobs += Start-Job -ScriptBlock $startVMBlock -ArgumentList $dtLab, $($singlevm.Keys), $profilePath
        Write-Output "Start Jobs end: $(Get-Date)"
    }

    # Get results from all jobs
    if($jobs.Count -ne 0) {
        Write-Output "Receive Jobs start: $(Get-Date)"
        foreach ($job in $jobs){
            $jobResult = Receive-Job -Job $job -Wait | Write-Output
        }
        Remove-Job -Job $jobs -Force
    }
    else
    {
        Write-Output "Information: No jobs available"
    }
}
```

## <a name="create-a-schedule"></a>일정 만들기
이 스크립트를 매일 실행 하도록 [일정을 만들려면](../automation/shared-resources/schedules.md#creating-a-schedule) automation 계정에 있습니다. 일정을 만든 후 [runbook에 연결할](../automation/shared-resources/schedules.md#linking-a-schedule-to-a-runbook)합니다. 

대규모 상황에서 여러 labs 사용 하 여 여러 구독이 있는 다른 랩에 대 한 파일에 매개 변수 정보를 저장 하 고 개별 매개 변수 대신 스크립트 파일을 전달 합니다. 스크립트를 수정 해야 하지만 동일 코어 실행 됩니다. 이 샘플에서는 Azure Automation에서 PowerShell 스크립트를 실행 하는 동안 빌드/릴리스 파이프라인에서 작업을 사용 하 여 같은 다른 옵션도 있습니다.

## <a name="next-steps"></a>다음 단계
Azure Automation에 자세히 알아보려면 다음 문서를 참조 하세요. [Azure Automation에 대 한 소개](../automation/automation-intro.md)합니다.
