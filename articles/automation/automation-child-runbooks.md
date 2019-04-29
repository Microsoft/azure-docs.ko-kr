---
title: Azure Automation의 자식 runbook
description: 다른 Runbook에서 Azure Automation의 Runbook을 시작하고 서로 정보를 공유하는 다양한 방법을 설명합니다.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 01/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 84f17b76f03c01d0b1441a50b9bcbddc1dfe2ef3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61081581"
---
# <a name="child-runbooks-in-azure-automation"></a>Azure Automation의 자식 runbook

다른 runbook 옆에 있는 불연속 함수를 사용하여 다시 사용할 수 있는 모듈식 runbook을 작성하는 Azure Automation의 모범 사례입니다. 부모 runbook은 하나 이상의 자식 runbook를 자주 호출하여 필요한 기능을 수행합니다. 두 가지 방법으로 자식 runbook을 호출하고 각각 서로 다른 차이점을 이해하여 다양한 시나리오에 가장 적합하게 결정하도록 합니다.

## <a name="invoking-a-child-runbook-using-inline-execution"></a>인라인 실행을 사용하여 자식 runbook 호출

다른 runbook에서 runbook 인라인을 호출하려면 runbook의 이름을 사용하고 활동 또는 cmdlet을 사용하는 것처럼 정확하게 해당 매개 변수에 대한 값을 제공합니다.  같은 Automation 계정에서 모든 runbook이 이런 방식으로 사용할 다른 모든 사용자에게 제공됩니다. 부모 runbook은 다음 줄으로 이동하기 전에 자식 runbook이 완료하기를 기다리고 어떤 출력도 직접 부모에게 반환됩니다.

runbook 인라인을 호출하면 동일한 작업에서 부모 runbook으로 실행됩니다. 실행된 자식 runbook의 작업 기록에는 표시가 없습니다. 자식 runbook에서 모든 예외 및 출력 스트림을 부모와 연결합니다. 자식 Runbook에서 throw된 예외 및 해당 스트림 출력 중 하나가 부모 작업과 연결되므로 이 동작으로 인해 더 적은 작업이 진행되고 보다 쉽게 추적하고 문제를 해결할 수 있습니다.

runbook이 게시되면 호출하는 모든 자식 runbook은 이미 게시되어야 합니다. runbook이 컴파일될 때 Azure Automation이 모든 자식 runbook과 연결을 빌드하기 때문입니다. 그렇지 않은 경우 부모 runbook은 올바르게 게시되도록 표시하지만 시작되면 예외를 생성합니다. 이 경우 자식 runbook을 제대로 참조하기 위해 부모 runbook을 다시 게시할 수 있습니다. 자식 runbook 중 하나가 변경되면 연결이 이미 만들어지기 때문에 부모 runbook을 다시 게시할 필요가 없습니다.

인라인으로 호출되는 자식 runbook의 매개 변수는 복잡한 개체를 비롯한 어떤 데이터 형식도 될 수 있습니다. Azure Portal 또는 Start-AzureRmAutomationRunbook cmdlet을 사용하여 runbook을 시작할 때와 달리 [JSON 직렬화](start-runbooks.md#runbook-parameters)는 수행되지 않습니다.

### <a name="runbook-types"></a>Runbook 형식

서로를 호출할 수 있는 형식:

* [PowerShell runbook](automation-runbook-types.md#powershell-runbooks) 및 [Graphical runbook](automation-runbook-types.md#graphical-runbooks)은 인라인으로 서로를 호출할 수 있습니다(둘 다 PowerShell 기반임).
* [PowerShell 워크플로 Runbook](automation-runbook-types.md#powershell-workflow-runbooks) 및 그래픽 PowerShell 워크플로 Runbook은 인라인으로 서로를 호출할 수 있습니다(둘 다 PowerShell 워크플로 기반임).
* PowerShell 형식과 PowerShell 워크플로 형식은 인라인으로 서로를 호출할 수 없으므로 Start-AzureRmAutomationRunbook을 사용해야 합니다.

게시 순서가 중요한 경우:

* Runbook의 게시 순서는 PowerShell 워크플로 및 그래픽 PowerShell 워크플로 Runbook에서만 중요합니다.

인라인 실행을 사용하여 그래픽 또는 PowerShell 워크플로 자식 Runbook을 호출하는 경우 Runbook의 이름을 사용합니다.  PowerShell 자식 Runbook을 호출할 때 스크립트가 로컬 디렉터리에 위치하는 것을 지정하도록 *.\\*로 해당 이름을 시작해야 합니다.

### <a name="example"></a>예

다음 예제는 세 매개 변수인 복잡한 개체, 정수 및 부울 값을 허용하는 테스트 자식 runbook을 시작합니다. 자식 runbook의 출력을 변수에 할당합니다.  이 경우 자식 Runbook은 PowerShell 워크플로 Runbook입니다.

```azurepowershell-interactive
$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

다음은 PowerShell Runbook을 자식으로 사용하는 동일한 예제입니다.

```azurepowershell-interactive
$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-cmdlet"></a>cmdlet을 사용하여 자식 runbook 시작

> [!IMPORTANT]
> `Start-AzureRmAutomationRunbook` cmdlet과 `-Wait` 스위치를 함께 사용하여 자식 runbook을 호출하며 자식 runbook의 결과가 개체이면 오류가 발생할 수 있습니다. 이 오류를 해결하려면 [개체 출력을 포함하는 자식 runbook](troubleshoot/runbooks.md#child-runbook-object)을 참조하여 결과를 폴링하도록 논리를 구현하고 [Get-AzureRmAutomationJobOutputRecord](/powershell/module/azurerm.automation/get-azurermautomationjoboutputrecord)를 사용하는 방법을 알아보세요.

[Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) cmdlet을 사용하여 [Windows PowerShell에서 Runbook 시작](start-runbooks.md#start-a-runbook-with-powershell)에서 설명한 대로 Runbook을 시작할 수 있습니다. 이 cmdlet에 사용할 두 가지 모드가 있습니다.  한 모드에서는 cmdlet이 자식 runbook에 대한 자식 작업이 만들어지면 작업 ID를 반환합니다.  **-wait** 매개 변수를 지정하여 사용하도록 설정할 수 있는 다른 모드에서 cmdlet은 자식 작업이 완료될 때까지 대기하고 자식 Runbook의 출력을 반환합니다.

cmdlet으로 시작된 자식 runbook에서 작업은 부모 runbook의 별도 작업에서 실행됩니다. 이 경우 Runbook 인라인을 호출하는 것보다 더 많은 작업이 진행되므로 추적하기가 더 어려울 수 있습니다. 부모는 각각이 완료되기를 기다리지 않고 비동기식으로 여러 자식 runbook을 시작할 수 있습니다. 인라인에서 자식 Runbook을 호출하는 동일한 종류의 병렬 실행에 대해 부모 Runbook은 [parallel 키워드](automation-powershell-workflow.md#parallel-processing)를 사용해야 합니다.

자식 Runbook의 출력은 타이밍으로 인해 부모 Runbook에 안정적으로 반환되지 않습니다. 또한 $VerbosePreference, $WarningPreference와 같은 특정 변수 및 다른 변수는 자식 Runbook으로 전파되지 않을 수 있습니다. 이러한 문제를 방지하기 위해 `-Wait` 스위치와 함께 `Start-AzureRmAutomationRunbook` cmdlet을 사용하여 별도 Automation 작업으로 자식 Runbook을 시작할 수 있습니다. 이는 자식 Runbook이 완료될 때까지 부모 Runbook을 차단합니다.

기다리는 동안 부모 Runbook을 차단하지 않으려는 경우 `-Wait` 스위치 없이 `Start-AzureRmAutomationRunbook` cmdlet을 사용하여 자식 Runbook을 시작할 수 있습니다. 그런 다음, `Get-AzureRmAutomationJob`을 사용하여 작업 완료를 기다리고, `Get-AzureRmAutomationJobOutput` 및 `Get-AzureRmAutomationJobOutputRecord`를 사용하여 결과를 검색해야 합니다.

[Runbook 매개 변수](start-runbooks.md#runbook-parameters)에서 설명한 대로 cmdlet을 사용하여 시작된 자식 Runbook에 대한 매개 변수는 해시 테이블로 제공됩니다. 단순한 데이터 형식만 사용할 수 있습니다. runbook에 복잡한 데이터 형식을 가진 매개 변수가 있는 경우 인라인으로 호출해야 합니다.

자식 Runbook을 별도 작업으로 시작할 때 구독 컨텍스트가 손실될 수 있습니다. 자식 Runbook은 특정 Azure 구독에 대해 Azure RM cmdlet을 실행하려면 부모 Runbook과는 별도로 이 구독에서 인증을 받아야 합니다.

동일한 Automation 계정 내의 작업이 여러 구독을 사용하는 경우 하나의 작업에서 구독을 선택하면 다른 작업에 대해서도 현재 선택한 구독 컨텍스트가 변경될 수 있습니다. 이 문제를 방지하려면 각 runbook 맨 처음에 `Disable-AzureRmContextAutosave –Scope Processsave`를 사용합니다. 이 작업을 수행하면 컨텍스트가 해당 runbook 실행에만 저장됩니다.

### <a name="example"></a>예

다음 예제는 매개 변수로 자식 runbook를 시작한 다음 Start-AzureRmAutomationRunbook -wait 매개 변수를 사용하여 완료되기를 기다립니다. 완료되면 자식 runbook에서 해당 출력을 수집합니다. `Start-AzureRmAutomationRunbook`을 사용하려면 Azure 구독에 인증해야 합니다.

```azurepowershell-interactive
# Ensures you do not inherit an AzureRMContext in your runbook
Disable-AzureRmContextAutosave –Scope Process

# Connect to Azure with RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzureRmAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzureRMContext $AzureContext `
    –Parameters $params –wait
```

## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>자식 runbook을 호출하기 위한 방법 비교

다음 테이블은 다른 runbook에서 runbook을 호출하기 위한 두 메서드 간의 차이점을 요약합니다.

|  | 인라인 | Cmdlet |
|:--- |:--- |:--- |
| 작업 |자식 runbook은 부모와 동일한 작업을 실행합니다. |자식 runbook에 대한 별도 작업을 만듭니다. |
| 실행 |계속하기 전에 부모 runbook은 자식 runbook이 완료되기를 기다립니다. |자식 Runbook이 시작된 후에 즉시 부모 Runbook이 계속되거나 *또는* 자식 작업이 완료될 때까지 부모 Runbook이 대기합니다. |
| 출력 |부모 runbook은 자식 runbook에서 출력을 직접 가져올 수 있습니다. |부모 Runbook은 자식 Runbook 작업에서 출력을 검색하거나 *또는* 자식 Runbook에서 출력을 직접 가져올 수 있습니다. |
| 매개 변수 |자식 runbook 매개 변수 값은 별도로 지정되며 모든 데이터 형식을 사용할 수 있습니다. |자식 runbook 매개 변수 값은 단일 해시 테이블로 결합해야 합니다. 이 해시 테이블은 JSON 직렬화를 사용하는 단순, 배열 및 개체 데이터 형식만 포함할 수 있습니다. |
| Automation 계정 |부모 runbook은 같은 자동화 계정에서 자식 runbook을 사용할 수 있습니다. |부모 runbook은 연결된 경우 동일한 Azure 구독 및 심지어 다른 구독의 자동화 계정에서 자식 runbook을 사용할 수 있습니다. |
| 게시 |부모 runbook을 게시하기 전에 자식 runbook을 게시해야 합니다. |부모 runbook을 시작하기 전 언제든 자식 runbook을 게시해야 합니다. |

## <a name="next-steps"></a>다음 단계

* [Azure Automation에서 Runbook 시작](start-runbooks.md)
* [Azure Automation에서 Runbook 출력 및 메시지](automation-runbook-output-and-messages.md)

