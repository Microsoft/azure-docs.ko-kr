---
title: Azure Automation의 내 첫 번째 PowerShell 워크플로 Runbook
description: PowerShell 워크플로를 사용하여 간단한 텍스트 Runbook의 생성, 테스트, 게시 과정을 안내하는 자습서입니다.
keywords: Powershell 워크플로, Powershell 워크플로 예제, 워크플로 Powershell
services: automation
ms.subservice: process-automation
ms.date: 09/24/2018
ms.topic: conceptual
ms.openlocfilehash: cdaadcfa3108ca847443e100bc624c2458c34115
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75365957"
---
# <a name="my-first-powershell-workflow-runbook"></a>내 첫 번째 PowerShell 워크플로 Runbook

> [!div class="op_single_selector"]
> * [그래픽](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [PowerShell 워크플로](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

이 자습서는 Azure Automation에서 [PowerShell 워크플로 Runbook](automation-runbook-types.md#powershell-workflow-runbooks)을 만드는 과정을 안내합니다. Runbook 작업의 상태를 추적하는 방법을 설명하면서 테스트하고 게시하는 간단한 Runbook으로 시작합니다. 그런 다음 실제로 Azure 리소스를 관리하도록 Runbook을 수정합니다. 이 경우에 Azure Virtual Machine을 시작합니다. 마지막으로 Runbook 매개 변수를 추가하여 Runbook을 더 강력하게 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* 동작합니다. 구독이 아직 없는 경우 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 등록할 수 있습니다.
* [Automation 계정](automation-offering-get-started.md) .  이 계정은 가상 머신을 시작하고 중지할 수 있는 권한이 있어야 합니다.
* Azure 가상 머신. 프로덕션 VM이 되지 않도록이 컴퓨터를 중지 하 고 시작 합니다.

## <a name="step-1---create-new-runbook"></a>1 단계-새 runbook 만들기

먼저 *헬로 월드*라는 텍스트를 출력하는 간단한 Runbook을 만듭니다.

1. Azure Portal에서 Automation 계정을 엽니다.

   Automation 계정 페이지는 이 계정의 리소스 간략히 보기를 제공합니다. 사용자에게는 이미 일부 자산이 있어야 합니다. 대부분의 해당 자산은 새 Automation 계정에 자동적으로 포함되어 있는 모듈입니다. 또한 사용자는 [필수 구성 요소](#prerequisites)에서 언급된 자격 증명 자산이 있어야 합니다.

1. **프로세스 자동화** 아래에서 **Runbook**을 클릭하여 Runbook 목록을 엽니다.
1. **+ Runbook 추가** 단추를 클릭하고 **새 Runbook 만들기**를 클릭하여 새 Runbook을 만듭니다.
1. Runbook 이름을 *MyFirstRunbook-Workflow*로 지정합니다.
1. 이 경우 [PowerShell 워크플로 Runbook](automation-runbook-types.md#powershell-workflow-runbooks)을 만들어 **Runbook 형식**에 대해 **Powershell 워크플로**를 선택합니다.
1. **만들기** 를 클릭하여 Runbook을 만들고 그래픽 편집기를 엽니다.

## <a name="step-2---add-code-to-the-runbook"></a>2단계 - Runbook에 코드 추가

runbook에 직접 코드를 입력하거나 라이브러리 컨트롤에서 cmdlet, Runbook 및 자산을 선택한 후 관련 매개 변수와 함께 Runbook에 추가되도록 할 수 있습니다. 이 연습에서는 Runbook에 직접 입력합니다.

1. 현재 Runbook은 필요한 *workflow* 키워드, Runbook 이름, 전체 워크플로를 묶는 중괄호만으로 비어 있습니다.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

1. 중괄호 안에 *Write-Output "Hello World."* 라고 입력합니다.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

1. **저장**을 클릭하여 Runbook을 저장합니다.

## <a name="step-3---test-the-runbook"></a>3 단계 - runbook 테스트

프로덕션 환경에서 사용할 수 있도록 Runbook을 게시하기 전에 제대로 작동하는지 확인하기 위해 테스트합니다. Runbook을 테스트할 때 **초안** 버전을 실행하고 해당 출력을 대화형으로 봅니다.

1. **테스트 창** 을 클릭하여 테스트 창을 엽니다.
1. **시작** 을 클릭하여 테스트를 시작합니다. 이 옵션은 사용 가능한 유일한 옵션입니다.
1. 이 창에서 [runbook 작업](automation-runbook-execution.md) 이 생성되고 상태를 보여줍니다.

   작업 상태는 클라우드의 runbook worker을 사용할 수 있을 때까지 기다리는 중임을 나타내는 *큐에 대기* 상태로 시작 됩니다. 작업자가 작업을 요구한 경우 *시작 중*으로 바뀐 다음 Runbook이 실제로 실행되기 시작하면 *실행 중*으로 바뀝니다.

1. Runbook 작업이 완료되면 해당 출력이 표시됩니다. 이 예제에서는 *Hello World*가 표시됩니다.

   ![Hello World](media/automation-first-runbook-textual/test-output-hello-world.png)

1. 캔버스로 돌아가려면 테스트 창을 닫습니다.

## <a name="step-4---publish-and-start-the-runbook"></a>4 단계 - runbook 게시 및 시작

방금 만든 Runbook은 아직 초안 모드입니다. 프로덕션 환경에서 실행 하려면 먼저 게시 해야 합니다. Runbook을 게시하면 초안 버전으로 기존의 게시된 버전을 덮어씁니다. 이 예제에서는 Runbook을 방금 만들었으므로 게시된 버전이 아직 없습니다.

1. **게시**를 클릭하여 Runbook을 게시한 다음 확인 메시지가 표시되면 **예**를 클릭합니다.
1. 이제 **Runbooks** 창에서 Runbook을 보기 위해 왼쪽으로 스크롤하면 **작성 상태**가 **게시됨**으로 표시됩니다.
1. 오른쪽으로 다시 스크롤하면 **MyFirstRunbook-Workflow**창이 표시됩니다.
   위쪽에 표시되는 옵션을 사용하여 Runbook을 시작하거나, 미래의 특정 시간에 시작하도록 예약하거나, [webhook](automation-webhooks.md) 을 생성하여 HTTP 호출을 통해 시작할 수 있습니다.
1. Runbook을 시작하기만 하면 되므로 **시작**을 클릭한 다음, 메시지가 표시되면 **예**를 클릭합니다.

   ![Runbook 시작](media/automation-first-runbook-textual/automation-runbook-controls-start.png)

1. 만든 Runbook 작업에 대한 작업 창이 열립니다. 이 창을 닫을 수는 있지만, 여기서는 작업 진행 상황을 볼 수 있도록 열어 둡니다.
1. Runbook을 테스트할 때와 동일한 작업 상태가 **작업 요약**에 표시됩니다.

   ![작업 요약](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)

1. Runbook 상태가 *완료됨*으로 표시되면 **출력**을 클릭합니다. 출력 창이 열리고 *헬로 월드*가 표시됩니다.

   ![작업 요약](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)

1. 출력 창을 닫습니다.
1. **모든 로그** 를 클릭하여 Runbook 작업에 대한 스트림 창을 엽니다. 출력 스트림에 *Hello World* 만 표시 되어야 하지만 runbook에서 기록 하는 경우이 보기에는 자세한 정보 표시, 오류와 같은 runbook 작업에 대한 다른 스트림이 표시 될 수 있습니다.

   ![작업 요약](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)

1. MyFirstRunbook 페이지로 돌아가려면 스트림 페이지와 작업 페이지를 닫습니다.
1. **작업** 을 클릭 하 여이 runbook에 대한 작업 페이지를 엽니다. 이 페이지에는 Runbook에서 만든 모든 작업이 나열됩니다. 작업을 한 번만 실행했으므로 하나의 작업만 표시됩니다.

   ![작업](media/automation-first-runbook-textual/runbook-control-job-tile.png)

1. 이 작업을 클릭 하 여 runbook을 시작할 때 표시 된 것과 동일한 작업 페이지를 열 수 있습니다. 이 작업을 통해 예전으로 돌아가 특정 Runbook에 대해 생성된 모든 작업의 세부 정보를 볼 수 있습니다.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>5 단계-Azure 리소스를 관리 인증 추가

지금까지 Runbook을 테스트 하고 게시했지만, 딱히 유용하지는 않습니다. Azure 리소스를 관리하려고 합니다. [전제 조건](#prerequisites)에서 참조 하는 자격 증명을 사용 하 여 인증 하지 않은 경우에는이 작업을 수행할 수 없습니다. 이렇게 하려면 **AzAccount** cmdlet을 사용 합니다.

1. MyFirstRunbook-Workflow 창에서 **편집**을 클릭하여 텍스트 편집기를 엽니다.
2. **Write-Output** 줄은 더 이상 필요하지 않으므로 계속 진행하여 삭제합니다.
3. 중괄호 사이의 빈 줄에 커서를 놓습니다.
4. Automation 실행 계정으로 인증을 처리할 다음 코드를 입력하거나 복사하여 붙여 넣습니다.

   ```powershell-interactive
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

   > [!IMPORTANT]
   > **AzAccount** 및 **AzAccount** 는 이제 **AzAccount**에 대한 별칭입니다. **AzAccount** cmdlet이 존재 하지 않는 경우 **AzAccount** 또는 **AzAccount**를 사용 하거나 Automation 계정의 모듈을 최신 버전으로 [업데이트할](automation-update-azure-modules.md) 수 있습니다.

> [!NOTE]
> 새 Automation 계정을 만든 경우에도 [모듈을 업데이트](automation-update-azure-modules.md)해야 할 수 있습니다.

1. Runbook을 테스트할 수 있도록 **테스트 창**을 클릭합니다.
1. **시작** 을 클릭하여 테스트를 시작합니다. 일단 완료되면 다음과 비슷한 출력을 수신하여 계정의 기본 정보를 표시해야 합니다. 이 작업은 자격 증명이 유효한 지 확인 합니다.

   ![Authenticate](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>6단계 - 가상 머신을 시작하기 위한 코드 추가

이제 Runbook에서 Azure 구독을 인증하므로 리소스를 관리할 수 있습니다. 가상 머신을 시작하는 명령을 추가합니다. Azure 구독에서 가상 머신을 선택할 수 있으며, 지금은 runbook에서 해당 이름을 하드 코딩 합니다. 여러 구독에서 리소스를 관리 하는 경우에는 [AzContext](/powershell/module/az.accounts/get-azcontext)와 함께 **-AzContext** 매개 변수를 사용 해야 합니다.

1. *AzAccount*후에 시작할 가상 컴퓨터의 이름 및 리소스 그룹 이름을 제공 하는 *new-azvm-name ' VMName '-ResourceGroupName ' NameofResourceGroup '* 를 입력 합니다.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName' -AzContext $AzureContext
   }
   ```

1. Runbook을 저장한 다음, 테스트할 수 있도록 **테스트 창**을 클릭합니다.
1. **시작** 을 클릭하여 테스트를 시작합니다. 완료되면, 가상 머신이 시작되었다는 것을 확인합니다.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>7 단계 - runbook에 입력 매개 변수를 추가 합니다.

Runbook은 현재 Runbook에 하드 코딩된 가상 머신을 시작하지만, Runbook이 시작될 때 가상 머신을 지정할 수 있으면 더 유용합니다. Runbook에 입력 매개 변수를 추가하여 해당 기능을 제공합니다.

1. *VMName* 및 *ResourceGroupName* 에 대한 매개 변수를 runbook에 추가 하 고 아래 예제와 같이 **new-azvm** cmdlet에 이러한 변수를 사용 합니다.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```

2. Runbook을 저장하고 테스트 창을 엽니다. 이제 테스트에 있는 두 개의 입력 변수에 값을 제공할 수 있습니다.
3. 창을 닫습니다.
4. **게시** 를 클릭하여 Runbook의 새 버전을 게시합니다.
5. 이전 단계에서 실행시킨 가상 머신을 중지합니다.
6. **시작** 을 클릭하여 runbook을 시작합니다. 시작하려는 가상 머신의 **VMName** 및 **ResourceGroupName**을 입력합니다.

   ![Runbook 시작](media/automation-first-runbook-textual/automation-pass-params.png)

7. Runbook이 완료되면 가상 머신이 시작되었다는 것을 확인합니다.

## <a name="next-steps"></a>다음 단계

* 언어 참조 및 학습 모듈을 비롯 한 PowerShell에 대한 자세한 내용은 [Powershell 문서](https://docs.microsoft.com/powershell/scripting/overview)를 참조 하세요.
* 그래픽 Runbook을 시작하려면 [내 첫 번째 그래픽 Runbook](automation-first-runbook-graphical.md)
* PowerShell Runbook을 시작하려면 [내 첫 번째 PowerShell Runbook](automation-first-runbook-textual-powershell.md)
* Runbook 형식, 해당 장점 및 제한 사항에 대해 자세히 알아보려면 [Azure Automation Runbook 형식](automation-runbook-types.md)
* PowerShell 스크립트 지원 기능에 대한 자세한 내용은 [Azure Automation에서 네이티브 PowerShell 스크립트 지원](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
