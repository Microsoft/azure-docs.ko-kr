---
title: Azure Automation에서 텍스트 Runbook 편집
description: 이 문서에서는 텍스트 편집기를 사용하여 Azure Automation에서 PowerShell 및 PowerShell 워크플로 Runbook을 작업하기 위한 여러 절차를 제공합니다.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9c684e9d1bf6cec12024cedfb5360d10e400e139
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61071096"
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Azure Automation에서 텍스트 Runbook 편집

Azure Automation에서 텍스트 편집기를 사용하여 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) 및 [PowerShell 워크플로 Runbook](automation-runbook-types.md#powershell-workflow-runbooks)을 편집할 수 있습니다. 텍스트 편집기에는 Intellisense와 색 구분 같은 타 코드 편집기의 일반적인 기능과, Runbook에 일반적인 리소스를 액세스하는 데 도움이 되는 추가적인 특수 기능이 있습니다. 이 문서에서는 이 편집기를 사용하여 다양한 기능을 수행하기 위한 상세 단계를 제공합니다.

텍스트 편집기에는 cmdlet, 자산 및 자식 Runbook에 대한 코드를 Runbook에 삽입하는 기능이 포함되어 있습니다. 코드를 직접 입력하지 않고 사용 가능한 리스소 목록에서 선택하여 적합한 코드를 Runbook에 에 삽입할 수 있습니다.

Azure Automation의 각 Runbook에는 초안과 게시 등 두 버전이 있습니다. Runbook의 초안 버전을 편집한 다음 실행할 수 있도록 게시합니다. 게시된 버전은 편집할 수 없습니다. 자세한 내용은 [Runbook 게시](manage-runbooks.md#publish-a-runbook)를 참조하세요.

[그래픽 Runbook](automation-runbook-types.md#graphical-runbooks) 작업은 [Azure Automation에서 그래픽 작성](automation-graphical-authoring-intro.md)을 참조하세요.

## <a name="to-edit-a-runbook-with-the-azure-portal"></a>Azure 포털을 사용하여 Runbook을 편집하려면

다음 절차를 사용하여 텍스트 편집기에서 편집할 Runbook을 엽니다.

1. Azure 포털에서 자동화 계정을 선택합니다.
2. **프로세스 자동화**에서 **Runbook**을 선택하여 Runbook 목록을 엽니다.
3. 편집하려는 Runbook을 선택한 다음, **편집** 단추를 클릭합니다.
4. Runbook을 편집합니다.
5. 편집이 완료되면 **저장** 을 클릭합니다.
6. Runbook의 최신 초안 버전을 게시하려면 **게시** 를 클릭합니다.

### <a name="to-insert-a-cmdlet-into-a-runbook"></a>Runbook에 cmdlet을 삽입하려면

1. 텍스트 편집기의 캔버스에서 cmdlet을 배치하려면 위치에 커서를 놓습니다.
2. 라이브러리 컨트롤에서 **Cmdlet** 노드를 확장합니다.
3. 사용하려는 cmdlet을 포함하는 모듈을 확장합니다.
4. 삽입할 cmdlet을 마우스 오른쪽 단추로 클릭하고 **캔버스에 추가**를 선택합니다. Cmdlet에 둘 이상의 매개 변수를 설정한 경우 기본 설정이 추가됩니다. cmdlet를 확장하여 다른 매개 변수 설정을 선택할 수 있습니다.
5. Cmdlet의 코드가 전체 매개 변수 목록과 함께 삽입됩니다.
6. 필요한 매개 변수에 대해 괄호 <>로 둘러 쌓인 데이터 형식 대신에 적합한 값을 입력합니다. 필요가 없는 매개 변수는 제거합니다.

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>Runbook에 자식 Runbook에 대한 코드를 삽입하려면

1. 텍스트 편집기의 캔버스에서 [자식 Runbook](automation-child-runbooks.md)을 배치하려는 위치에 커서를 놓습니다.
2. 라이브러리 컨트롤에서 **Runbook** 노드를 확장합니다.
3. 삽입할 Runbook을 마우스 오른쪽 단추로 클릭하고 **캔버스에 추가**를 선택합니다.
4. 자식 Runbook의 코드가 Runbook 매개 변수의 자리 표시자와 함께 삽입됩니다.
5. 자리 표시자를 각 매개 변수에 적합한 값으로 바꿉니다.

### <a name="to-insert-an-asset-into-a-runbook"></a>Runbook에 자산을 삽입하려면

1. 텍스트 편집기의 캔버스에서 코드를 배치하려는 위치에 커서를 놓습니다.
2. 라이브러리 컨트롤에서 **자산** 노드를 확장합니다.
3. 원하는 자산 유형의 노드를 확장합니다.
4. 삽입할 자산을 마우스 오른쪽 단추로 클릭하고 **캔버스에 추가**를 선택합니다. [변수 자산](automation-variables.md)의 경우 변수를 가져올지 또는 설정할지 여부에 따라 **"변수 가져오기"를 캔버스에 추가** 또는 **"변수 설정"을 캔버스에 추가** 중에서 선택합니다.
5. 자산에 대한 코드가 Runbook에 삽입됩니다.

## <a name="to-edit-an-azure-automation-runbook-using-windows-powershell"></a>Windows PowerShell을 사용하여 Azure Automation Runbook을 편집하려면

Windows PowerShell을 사용하여 Runbook을 편집하려면 원하는 편집기를 사용하고 `.ps1` 파일에 저장합니다. [Export-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Export-AzureRmAutomationRunbook) cmdlet을 사용하여 Runbook의 콘텐츠를 가져온 다음, [Import-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/import-azurermautomationrunbook) cmdlet을 사용하여 기존 초안 Runbook을 수정한 Runbook으로 바꿀 수 있습니다.

### <a name="to-retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Windows PowerShell을 사용하여 Runbook의 콘텐츠를 검색하려면

다음 예제 명령에서는 Runbook에 대한 스크립트를 검색하고 스크립트 파일에 저장하는 방법을 보여줍니다. 이 예에서는 초안 버전이 검색됩니다. Runbook의 게시 버전을 검색할 수도 있습니다. 단, 이 버전을 변경할 수는 없습니다.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzureRmAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="to-change-the-contents-of-a-runbook-using-windows-powershell"></a>Windows PowerShell을 사용하여 Runbook의 콘텐츠를 변경하려면

다음 샘플 명령에서는 스크립트 파일의 콘텐츠로 기존 Runbook 콘텐츠를 대체하는 방법을 보여줍니다. 이 내용은 [Windows PowerShell을 사용하여 스크립트 파일에서 Runbook을 가져오려면](manage-runbooks.md#import-a-runbook)에서 설명한 것과 동일한 샘플 절차입니다.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzureRmAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzureRmAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="related-articles"></a>관련 문서

* [Azure Automation에서 Runbook 관리](manage-runbooks.md)
* [PowerShell 워크플로 학습](automation-powershell-workflow.md)
* [Azure Automation에서 그래픽 작성](automation-graphical-authoring-intro.md)
* [Certificates](automation-certificates.md)
* [연결](automation-connections.md)
* [자격 증명](automation-credentials.md)
* [일정](automation-schedules.md)
* [변수](automation-variables.md)

