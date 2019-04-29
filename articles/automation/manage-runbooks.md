---
title: Azure Automation에서 Runbook 관리
description: 이 문서에서는 Azure Automation에서 Runbook을 관리하는 방법을 설명합니다.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5bb52e0547ed9bc18d67370ffb9db35942212aab
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61300280"
---
# <a name="manage-runbooks-in-azure-automation"></a>Azure Automation에서 Runbook 관리

[새로 만들거나](#create-a-runbook) 파일 또는[Runbook 갤러리](automation-runbook-gallery.md)에서 기존 Runbook을 가져와 Azure Automation에 Runbook을 추가할 수 있습니다. 이 문서에서는 파일로부터 Runbook을 만들고 가져오는 것과 관련한 정보를 제공합니다.  [Azure Automation에 대한 Runbook 및 모듈 갤러리](automation-runbook-gallery.md)에서 커뮤니티 Runbook과 모듈 액세스에 대한 모든 상세 정보를 확인할 수 있습니다.

## <a name="create-a-runbook"></a>Runbook 만들기

Azure Portal 또는 Windows PowerShell 중 하나를 사용하여 Azure Automation에서 새 Runbook을 만들 수 있습니다. Runbook를 만든 후에는 [PowerShell 워크플로 학습](automation-powershell-workflow.md) 및 [Azure Automation에서 그래픽 제작](automation-graphical-authoring-intro.md)의 정보를 사용하여 편집할 수 있습니다.

### <a name="create-a-runbook-in-the-azure-portal"></a>Azure Portal에서 Runbook 만들기

1. Azure Portal에서 Automation 계정을 엽니다.
2. Hub에서 **Runbook**을 클릭하여 Runbook 목록을 엽니다.
3. **Runbook 추가** 단추를 클릭하고 **새 Runbook 만들기**를 클릭합니다.
4. Runbook의 **이름** 을 입력하고 [유형](automation-runbook-types.md)을 선택합니다. Runbook 이름은 문자로 시작해야 하며 문자, 숫자, 언더바, 대시 등이 포함될 수 있습니다.
5. **만들기** 를 클릭하여 Runbook을 만들고 편집기를 엽니다.

### <a name="create-a-runbook-with-powershell"></a>PowerShell을 사용하여 Runbook 만들기

[New-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/new-azurermautomationrunbook) cmdlet을 사용하여 빈 [PowerShell 워크플로 Runbook](automation-runbook-types.md#powershell-workflow-runbooks)을 만들 수 있습니다. **Type** 매개 변수를 사용하여 네 가지 Runbook 유형 중 하나를 지정합니다.

다음 명령 예제에서는 새로운 빈 Runbook을 만드는 방법을 보여줍니다.

```azurepowershell-interactive
New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Runbook 가져오기

PowerShell 스크립트나 PowerShell 워크플로(.ps1 확장명), 내보낸 그래픽 Runbook(.graphrunbook) 또는 Python 2 스크립트(.py extension)를 Azure Automation에 가져와서 새 Runbook을 만들 수 있습니다.  다음 사항을 고려하여 가져오기 동안 만들어지는 [Runbook 유형](automation-runbook-types.md)을 지정해야 합니다.

* `.graphrunbook` 파일은 새 [그래픽 Runbook](automation-runbook-types.md#graphical-runbooks)으로만 가져올 수 있으며, 그래픽 Runbook은 `.graphrunbook` 파일을 통해서만 만들 수 있습니다.
* PowerShell 워크플로를 포함하는 `.ps1` 파일은 [PowerShell 워크플로 Runbook](automation-runbook-types.md#powershell-workflow-runbooks)으로만 가져올 수 있습니다.  파일에 여러 PowerShell 워크플로 있으면 가져오기가 실패합니다. 각 워크플로를 자체 파일에 저장하 고 각각 개별적으로 가져와야 합니다.
* 워크플로를 포함하지 않는 `.ps1` 파일은 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) 또는 [PowerShell 워크플로 Runbook](automation-runbook-types.md#powershell-workflow-runbooks)으로 가져올 수 있습니다.  PowerShell 워크플로 Runbook에 가져오면 워크플로로 변환되며 Runbook에 적용된 변경 내용을 명시하는 메모가 포함됩니다.

### <a name="to-import-a-runbook-from-a-file-with-the-azure-portal"></a>Azure Portal을 사용하여 파일에서 Runbook을 가져오려면

Azure Automation에 스크립트 파일을 가져오려면 다음 절차를 사용할 수 있습니다.  

> [!NOTE]
> 이 포털을 사용하는 PowerShell 워크플로 Runbook에만 .ps1 파일을 가져올 수 있습니다.

1. Azure Portal에서 Automation 계정을 엽니다.
2. Hub에서 **Runbook**을 클릭하여 Runbook 목록을 엽니다.
3. **Runbook 추가** 단추를 클릭하고 **가져오기**를 클릭합니다.
4. **Runbook 파일** 을 클릭하여 가져올 파일을 선택합니다.
5. **이름** 필드가 활성화된 경우 변경이 가능합니다.  Runbook 이름은 문자로 시작해야 하며 문자, 숫자, 언더바, 대시 등이 포함될 수 있습니다.
6. [Runbook 형식](automation-runbook-types.md)이 자동으로 선택되지만 해당 제한을 고려한 후에 형식을 변경할 수 있습니다. 
7. 새 Runbook이 Automation 계정의 Runbook 목록에 표시됩니다.
8. 실행에 앞서 [Runbook을 게시](#publish-a-runbook) 해야 합니다.

> [!NOTE]
> 그래픽 Runbook 또는 그래픽 PowerShell 워크플로 Runbook을 가져온 후 필요에 따라 다른 형식으로 변환할 수도 있습니다. 텍스트 Runbook으로는 변환할 수 없습니다.

### <a name="to-import-a-runbook-from-a-script-file-with-windows-powershell"></a>Windows PowerShell을 사용하여 스크립트 파일에서 Runbook을 가져오려면

[Import-AzureRMAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/import-azurermautomationrunbook) cmdlet을 사용하여 스크립트 파일을 PowerShell 워크플로 Runbook 초안으로 가져올 수 있습니다. 해당 Runbook이 이미 있는 경우 *-Force* 매개 변수를 사용하지 않으면 가져오기에 실패합니다.

다음 명령 예제는 Runbook에 스크립트 파일을 가져오는 방법을 보여 줍니다.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzureRMAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="test-a-runbook"></a>Runbook 테스트

Runbook을 테스트할 때 [초안 버전](#publish-a-runbook) 이 실행되며 해당 Runbook에서 수행하는 모든 작업이 완료됩니다. 작업 기록은 만들어지지 않지만 [출력](automation-runbook-output-and-messages.md#output-stream)과 [경고 및 오류](automation-runbook-output-and-messages.md#message-streams) 스트림은 테스트 출력 창에 표시됩니다. [자세한 정보 스트림](automation-runbook-output-and-messages.md#message-streams)에 대한 메시지는 [$VerbosePreference 변수](automation-runbook-output-and-messages.md#preference-variables)가 Continue로 설정되는 경우에만 출력 창에 표시됩니다.

초안 버전을 실행하더라도 Runbook은 정상적으로 실행되며, 해당 환경의 리소스에 대해 모든 작업을 수행합니다. 이러한 이유로 비프로덕션 리소스에서만 Runbook을 테스트해야 합니다.

각 [Runbook 유형](automation-runbook-types.md)을 테스트하는 절차는 동일하며, Azure Portal에서 테스트할 때 텍스트 편집기와 그래픽 편집기 간에 차이가 없습니다.  

1. [텍스트 편집기](automation-edit-textual-runbook.md) 또는 [그래픽 편집기](automation-graphical-authoring-intro.md)에서 Runbook의 초안 버전을 엽니다.
1. **테스트** 단추를 클릭하여 테스트 페이지를 엽니다.
1. Runbook에 매개 변수가 있는 경우 왼쪽 창에 나열되며, 여기서 테스트에 사용할 값을 제공할 수 있습니다.
1. [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)에서 테스트를 실행하려는 경우는 **실행 설정**을 **Hybrid Worker**로 변경하고 대상 그룹의 이름을 선택합니다.  그렇지 않은 경우 기본 **Azure** 를 유지하여 클라우드에서 테스트를 실행합니다.
1. **시작** 단추를 클릭하여 테스트를 시작합니다.
1. Runbook이 [PowerShell 워크플로](automation-runbook-types.md#powershell-workflow-runbooks) 또는 [그래픽](automation-runbook-types.md#graphical-runbooks)인 경우 출력 창 아래의 단추를 사용하여 테스트 중에 중지하거나 일시 중단할 수 있습니다. Runbook을 일시 중단하는 경우 일시 중단하기 전에 현재 작업을 완료합니다. Runbook이 일시 중단되면 중지하거나 다시 시작할 수 있습니다.
1. 출력 창에서 Runbook의 출력을 검사합니다.

## <a name="publish-a-runbook"></a>Runbook 게시

새 Runbook을 만들거나 가져올 때는 게시해야 실행할 수 있습니다.  Automation의 각 Runbook에는 초안 버전과 게시된 버전이 있습니다. 게시된 버전만 실행할 수 있으며 초안 버전만 편집할 수 있습니다. 초안 버전을 변경해도 게시된 버전은 영향을 받지 않습니다. 초안 버전을 사용할 수 있게 되면 이를 게시합니다. 그러면 초안 버전이 게시된 버전을 덮어씁니다.

### <a name="azure-portal"></a>Azure portal

1. Azure Portal에서 Runbook을 엽니다.
2. **Edit** 단추를 클릭합니다.
3. **게시** 단추를 클릭한 다음 확인 메시지에서 **예**를 클릭합니다.

### <a name="powershell"></a>PowerShell

[Publish-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/publish-azurermautomationrunbook) cmdlet을 사용하여 Windows PowerShell에서 Runbook을 게시할 수 있습니다. 다음 명령 예제에서는 샘플 Runbook을 게시하는 방법을 보여줍니다.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="next-steps"></a>다음 단계

* Runbook 및 PowerShell 모듈 갤러리를 활용하는 방법을 알아보려면 [Azure Automation에 대한 Runbook 및 모듈 갤러리](automation-runbook-gallery.md)
* 텍스트 편집기를 사용하여 PowerShell 및 PowerShell 워크플로 Runbook을 편집하는 방법을 알아보려면 [Azure Automation에서 텍스트 Runbook 편집](automation-edit-textual-runbook.md)
* 그래픽 Runbook 작성에 대한 자세한 내용은 [Azure Automation에서 그래픽 제작](automation-graphical-authoring-intro.md)
