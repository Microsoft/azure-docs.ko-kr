---
title: Azure Automation에서 Runbook 출력 및 메시지
description: Azure Automation의 runbook에서 출력 및 오류 메시지를 만들고 검색하는 방법을 설명합니다.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 12/04/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1cbf91af4e91f41fff30a7edfa869d07a21b881e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61226966"
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Azure Automation에서 Runbook 출력 및 메시지
대부분의 Azure Automation Runbook은 일종의 출력 양식을 갖습니다. 이 출력은 사용자에 대한 오류 메시지 또는 다른 Runbook과 함께 사용하려는 복합 개체일 수 있습니다. Windows PowerShell은 [여러 스트림](/powershell/module/microsoft.powershell.core/about/about_redirection) 제공하여 스크립트 또는 워크플로에서 출력을 보냅니다. Azure Automation은 이러한 스트림에서 각각 다르게 작동합니다. 각 Runbook을 만들 때 사용하는 방법에 대한 모범 사례를 따라야 합니다.

다음 테이블에서는 게시된 Runbook 및 [Runbook을 테스트](automation-testing-runbook.md)할 경우 Azure Portal의 각 스트림 및 해당 동작을 간략히 설명합니다. 각 스트림에 대한 자세한 내용은 뒷부분의 섹션에 제공됩니다.

| Stream | 설명 | 게시됨 | 테스트 |
|:--- |:--- |:--- |:--- |
| 출력 |다른 runbook에서 사용할 수 있도록 의도된 개체입니다. |작업 기록에 기록합니다. |테스트 출력 창에 표시합니다. |
| Warning |사용자를 위한 경고 메시지입니다. |작업 기록에 기록합니다. |테스트 출력 창에 표시합니다. |
| 오류 |사용자를 위한 오류 메시지입니다. 예외와 달리 runbook는 기본적으로 오류 메시지 이후에 계속합니다. |작업 기록에 기록합니다. |테스트 출력 창에 표시합니다. |
| 자세한 정보 표시 |일반 또는 디버깅 정보를 제공하는 메시지입니다. |자세한 정보 표시 로깅이 Runbook에 켜져 있는 경우 작업 기록에 기록합니다. |$VerbosePreference runbook가 계속으로 설정된 경우 테스트 출력 창에 표시합니다. |
| 진행 |runbook의 각 작업 전과 후에 레코드를 자동으로 생성했습니다. 자체 진행률 레코드는 대화형 사용자를 위한 것이기 때문에 Runbook이 만들려고 하지 않아야 합니다. |진행률 로깅이 Runbook에 켜져 있는 경우 작업 기록에 기록합니다. |테스트 출력 창에 표시하지 않습니다. |
| 디버그 |대화형 사용자를 위한 메시지입니다. Runbook에서 사용하지 않아야 합니다. |작업 기록에 기록하지 않습니다. |테스트 출력 창에 기록하지 않습니다. |

## <a name="output-stream"></a>출력 스트림
출력 스트림이 올바르게 실행되면 스크립트 또는 워크플로에 의해 생성된 개체의 출력을 위한 것입니다. Azure Automation에서 이 스트림은 주로 [현재 runbook을 호출하는 부모 runbook](automation-child-runbooks.md)에서 사용할 수 있도록 의도한 개체에 사용됩니다. 부모 runbook에서 [runbook 인라인을 호출](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution)하는 경우 출력 스트림에서 부모 runbook에 데이터를 반환합니다. Runbook이 다른 Runbook에서 호출되지 않는다는 사실을 알고 있는 경우 출력 스트림을 사용하여 다시 사용자에게 일반 정보를 전달합니다. 그러나 모범 사례로 일반적으로 [자세한 정보 표시 스트림](#verbose-stream) 을 사용하여 사용자에게 일반 정보를 전달해야 합니다.

[쓰기 출력](https://technet.microsoft.com/library/hh849921.aspx) 을 사용하거나 runbook에서 자체 줄에 개체를 배치하여 출력 스트림에 데이터를 쓸 수 있습니다.

```powershell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="output-from-a-function"></a>함수에서 출력
Runbook에 포함된 함수에 출력 스트림을 작성하는 경우 출력은 Runbook에 다시 전달됩니다. runbook이 해당 출력을 변수에 할당하면 출력 스트림에 기록되지 않습니다. 함수 내에서 다른 스트림에 기록하면 Runbook의 해당하는 스트림에 기록됩니다.

다음과 같은 샘플 Runbook을 고려해 보세요.

```powershell
Workflow Test-Runbook
{
  Write-Verbose "Verbose outside of function" -Verbose
  Write-Output "Output outside of function"
  $functionOutput = Test-Function
  $functionOutput

  Function Test-Function
  {
    Write-Verbose "Verbose inside of function" -Verbose
    Write-Output "Output inside of function"
  }
}
```

Runbook 작업에 대한 출력 스트림은 다음과 같습니다.

```output
Output inside of function
Output outside of function
```

Runbook 작업에 대한 자세한 정보 표시 스트림은 다음과 같습니다.

```output
Verbose outside of function
Verbose inside of function
```

Runbook을 게시하고 시작하기 전에 자세한 정보 표시 스트림 출력을 얻기 위해 Runbook 설정에서 자세한 정보 표시 로깅을 켜야 합니다.

### <a name="declaring-output-data-type"></a>출력 데이터 형식 선언
워크플로는 [OutputType 특성](https://technet.microsoft.com/library/hh847785.aspx)을 사용하여 해당 출력의 데이터 형식을 지정할 수 있습니다. 이 특성은 런타임 시 영향을 주지 않지만 runbook의 예상된 출력에서 디자인 타임에 runbook 작성자에게 표시를 제공합니다. Runbook용 도구 집합이 계속 진화하기 때문에 디자인 타임에 출력 데이터 형식을 선언하는 일이 더욱 중요해지고 있습니다. 결과적으로 만든 Runbook에서 이 선언을 포함하는 것이 모범 사례입니다.

다음은 예제 출력 형식의 목록입니다.

* System.String
* System.Int32
* System.Collections.Hashtable
* Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine

다음 샘플 runbook은 문자열 개체를 출력하고 해당 출력 형식의 선언을 포함합니다. Runbook이 특정 유형의 배열을 출력하면 형식의 배열과 달리 형식을 지정해야 합니다.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

그래픽 또는 그래픽 PowerShell 워크플로 Runbook에서 출력 형식을 선언하려면 **입력 및 출력** 메뉴 옵션을 선택하고 출력 형식 이름을 입력할 수 있습니다. 부모 Runbook에서 참조하는 경우 쉽게 식별할 수 있도록 전체 .NET 클래스 이름을 사용하는 것이 좋습니다. Runbook의 데이터 버스에 대한 해당 클래스의 모든 속성을 노출하고 Runbook의 다른 활동에 대한 값으로 조건부 논리, 로깅 및 참조에 사용하는 경우 유연성 향상을 제공합니다.<br> ![Runbook 입력 및 출력 옵션](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

다음 예제에서는 이 기능을 보여 주기 위한 두 개의 그래픽 Runbook이 있습니다. 모듈식 Runbook 디자인 모델을 적용하는 경우 실행 계정을 사용하여 Azure로 인증을 관리하는 *인증 Runbook 템플릿*으로 제공하는 하나의 Runbook이 있습니다. 이 경우에 일반적으로 제공된 시나리오를 자동화하는 핵심 논리를 수행하는 두 번째 Runbook은 *인증 Runbook 템플릿* 을 실행하고 **테스트** 출력 창에 결과를 표시합니다. 정상적인 상황에서 자식 Runbook의 출력을 활용하는 리소스에 대해 이 Runbook이 작업을 수행하도록 합니다.

**AuthenticateTo-Azure** Runbook의 기본 논리는 다음과 같습니다.<br> ![Runbook 템플릿 예제 인증](media/automation-runbook-output-and-messages/runbook-authentication-template.png)할 경우 Azure 관리 포털에서 각 스트림 및 동작에 대한 간략한 설명을 제공합니다.  

인증 프로필 속성을 반환하는 출력 형식 *Microsoft.Azure.Commands.Profile.Models.PSAzureContext*를 포함합니다.<br> ![Runbook 출력 형식 예제](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png) 

이 Runbook은 단순하지만 여기에서 호출할 하나의 구성 항목이 있습니다. 마지막 작업은 **Write-Output** cmdlet을 실행하고 해당 cmdlet에 필요한 **Inputobject** 매개 변수에 대해 PowerShell 식을 사용하여 $_ 변수에 대한 프로필 데이터를 기록합니다.  

이 예제에서 *Test-ChildOutputType*이라는 두 번째 Runbook의 경우 단순히 두 개의 작업을 가집니다.<br> ![예제 자식 출력 형식 Runbook](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png) 

첫 번째 작업은 **AuthenticateTo-Azure** Runbook을 호출하고 두 번째 작업은 **작업 출력**의 **데이터 원본**으로 **Write-Verbose** cmdlet을 실행하며 **AuthenticateTo-Azure** Runbook의 컨텍스트 출력을 지정하는 **필드 경로**에 대한 값은 **Context.Subscription.SubscriptionName**입니다.<br> ![Write-Verbose cmdlet 매개 변수 데이터 원본](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)    

결과 출력은 구독의 이름입니다.<br> ![Test-ChildOutputType Runbook 결과](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

출력 형식 제어의 동작에 대한 참고입니다. 입력 및 출력 속성 블레이드의 출력 형식 필드에 값을 입력하는 경우 제어에서 항목을 인식하려면 입력한 후 제어의 바깥쪽을 클릭해야 합니다.  

## <a name="message-streams"></a>메시지 스트림
출력 스트림과 달리 메시지 스트림은 사용자에게 정보를 전달하기 위한 것입니다. 다른 종류의 정보에 대한 여러 메시지 스트림이 있으며 각각 Azure Automation에서 다르게 처리됩니다.

### <a name="warning-and-error-streams"></a>경고 및 오류 스트림
경고 및 오류 스트림은 Runbook에서 발생하는 문제를 로그하기 위한 것입니다. Runbook을 실행하는 경우 작업 기록에 기록되며 Runbook을 테스트할 경우 Azure Portal에서 테스트 출력 창에 포함됩니다. 기본적으로 runbook은 경고 또는 오류 이후에 계속 실행됩니다. 메시지를 만들기 전에 runbook의 [기본 설정 변수](#preference-variables) 를 설정하여 runbook이 경고 또는 오류로 일시 중단해야 할지를 지정할 수 있습니다. 예를 들어 runbook이 예외와 마찬가지로 오류로 인해 일시 중단이 발생하려면 **$ErrorActionPreference** 를 중지로 설정합니다.

[Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) 또는 [Write-Error](https://technet.microsoft.com/library/hh849962.aspx) cmdlet를 사용하여 경고 또는 오류 메시지를 만듭니다. 또한 활동은 이러한 스트림에 쓸 수 있습니다.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="verbose-stream"></a>자세한 정보 표시 스트림
자세한 정보 표시 메시지 스트림은Runbook 작업에 대한 일반 정보입니다. [디버그 스트림](#debug-stream) 을 runbook에서 사용할 수 없기 때문에 디버그 정보에 자세한 정보 표시 메시지를 사용해야 합니다. 기본적으로 게시된 Runbook에서 자세한 정보 표시 메시지는 작업 기록에 저장되지 않습니다. 자세한 정보 표시 메시지를 저장하기 위해 Azure Portal에 있는 Runbook의 구성 탭에서 게시된 Runbook을 상세 레코드 기록으로 구성합니다. 대부분의 경우 성능상의 이유로 runbook에 대한 자세한 정보 표시 레코드를 기록하지 않는 기본 설정을 유지해야 합니다. 옵션을 켜서 문제를 해결하거나 runbook 디버그합니다.

[Runbook을 테스트](automation-testing-runbook.md)할 경우 Runbook이 자세한 정보 표시 레코드를 기록하도록 구성되면 자세한 정보 표시 메시지는 표시되지 않습니다. [runbook을 테스트](automation-testing-runbook.md)하는 동안 자세한 정보 표시 메시지를 표시하려면 $VerbosePreference 변수를 계속으로 설정해야 합니다. 해당 변수를 설정하면, 자세한 정보 표시 메시지가 Azure Portal의 테스트 출력 창에 표시됩니다.

[Write-Verbose](https://technet.microsoft.com/library/hh849951.aspx) cmdlet를 사용하여 자세한 정보 표시 메시지를 만듭니다.

```powershell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

### <a name="debug-stream"></a>디버그 스트림
디버그 스트림은 대화형 사용자로 사용하기 위한 것이며 runbook에서 사용하지 않아야 합니다.

## <a name="progress-records"></a>진행률 레코드
Runbook을 구성하여(Azure 포털의 runbook 구성 탭에서) 진행률 레코드를 기록하는 경우 각 작업을 실행하는 전과 후에 작업 기록에 레코드를 기록합니다. 대부분의 경우 성능을 최대화하기 위해 runbook에 대한 진행률 레코드를 기록하지 않는 기본 설정을 유지해야 합니다. 옵션을 켜서 문제를 해결하거나 runbook 디버그합니다. runbook을 테스트할 경우 runbook이 진행률 레코드를 기록하도록 구성되면 진행률 메시지는 표시되지 않습니다.

[Write-progress](https://technet.microsoft.com/library/hh849902.aspx) cmdlet은 대화형 사용자로 사용하기 위한 것이기 때문에 Runbook에 유효하지 않습니다.

## <a name="preference-variables"></a>기본 설정 변수
Windows PowerShell은 [기본 설정 변수](https://technet.microsoft.com/library/hh847796.aspx) 를 사용하여 다른 출력 스트림에 전송된 데이터에 대응하는 방법을 결정합니다. Runbook에서 이러한 변수를 설정하여 다른 스트림으로 전송된 데이터에 응답하는 방식을 제어할 수 있습니다.

다음 테이블은 유효 및 기본값으로 runbook에서 사용할 수 있는 기본 설정 변수를 나열 합니다. 이 테이블은 runbook에서 유효한 값만을 포함합니다. 추가 값은 Azure Automation 외부의 Windows PowerShell에서 사용되는 경우 기본 설정 변수로 유효합니다.

| 변수 | 기본값 | 유효한 값 |
|:--- |:--- |:--- |
| WarningPreference |계속 |중지<br>계속<br>SilentlyContinue |
| ErrorActionPreference |계속 |중지<br>계속<br>SilentlyContinue |
| VerbosePreference |SilentlyContinue |중지<br>계속<br>SilentlyContinue |

다음 테이블은 runbook에서 유효한 기본 설정 변수 값에 대한 동작을 나열합니다.

| 값 | 동작 |
|:--- |:--- |
| 계속 |메시지를 기록하고 runbook 실행을 계속합니다. |
| SilentlyContinue |메시지를 기록하지 않고 Runbook을 계속 실행합니다. 이 값은 메시지를 무시하는 효과가 있습니다. |
| 중지 |메시지를 기록하고 runbook을 일시 중단합니다. |

## <a name="runbook-output"></a>Runbook 출력 및 메시지 검색
### <a name="azure-portal"></a>Azure portal
Runbook의 작업 탭의 Azure 포털에서 Runbook 작업의 세부 정보를 볼 수 있습니다. 작업 요약에는 작업 및 발생하는 모든 예외에 대한 일반 정보 외에도 입력 매개 변수 및 [출력 스트림](#output-stream)이 표시합니다. 세부 정보 표시 및 진행률 레코드를 기록하도록 Runbook을 구성하면 [자세한 정보 표시 스트림](#verbose-stream) 및 [진행률 레코드](#progress-records) 외에도 [출력 스트림](#output-stream) 및 [경고 및 오류 스트림](#warning-and-error-streams)의 메시지가 기록에 포함됩니다.

### <a name="windows-powershell"></a>Windows PowerShell
Windows PowerShell에서 [Get AzureAutomationJobOutput](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjoboutput) cmdlet을 사용하여 runbook에서 출력 및 메시지를 검색할 수 있습니다. cmdlet은 작업의 ID를 필요로 하고 반환하는 스트림을 지정하는 스트림을 호출한 매개 변수가 있습니다. **Any**를 지정하여 작업에 모든 스트림을 반환할 수 있습니다.

다음 예제는 샘플 runbook를 시작한 다음 완료되기를 기다립니다. 완료되면 해당 출력 스트림을 작업에서 수집합니다.

```powershell
$job = Start-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

$doLoop = $true
While ($doLoop) {
  $job = Get-AzureRmAutomationJob -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" -Id $job.JobId
  $status = $job.Status
  $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Output

# For more detailed job output, pipe the output of Get-AzureRmAutomationJobOutput to Get-AzureRmAutomationJobOutputRecord
Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Any | Get-AzureRmAutomationJobOutputRecord
``` 

### <a name="graphical-authoring"></a>그래픽 작성
그래픽 Runbook의 경우 추가 로깅은 작업 수준 추적의 형식으로 사용할 수 있습니다. 추적에는 두 가지 수준이 있습니다. 기본 및 자세히 추적 기본 추적에서는 Runbook의 각 작업의 시작 및 종료 시간과 작업 다시 시도와 관련된 정보를 볼 수 있습니다. 작업의 시도 횟수 및 시작 시간을 예로 들 수 있습니다. 자세히 추적에서는 기본 추적 외에도 각 작업에 대한 입력 및 출력 데이터를 얻습니다. 현재 추적 레코드는 자세한 정보 표시 스트림을 사용하여 기록되므로 추적을 사용하도록 설정할 경우 자세한 정보 로깅을 사용하도록 설정해야 합니다. 추적이 활성화된 그래픽 Runbook의 경우 진행률 레코드를 기록할 필요가 없습니다. 기본 추적은 동일한 용도를 제공하며 정보가 더욱 자세합니다.

![그래픽 작성 작업 스트림 보기](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

그래픽 Runbook에 대한 자세한 정보 로깅 및 추적을 사용하도록 설정하면 프로덕션 작업 스트림 보기에서 훨씬 더 많은 정보를 사용할 수 있음을 앞의 스크린샷에서 볼 수 있습니다. 이러한 추가 정보는 Runbook을 사용하는 프로덕션 문제 해결에 필요할 수 있으므로 그 목적으로만 사용하고 일반적으로는 사용하지 않습니다. 추적 레코드는 특히 많을 수 있습니다. 그래픽 Runbook 추적을 사용하면 기본 추적 또는 자세히 추적을 구성했는지에 따라 작업당 2~4개의 레코드를 얻을 수 있습니다. 문제 해결에 대한 Runbook의 진행 상황을 추적하는 데 이 정보가 필요하지 않은 한 추적을 끈 상태로 유지하고 싶어 할 수 있습니다.

**작업 수준 추적을 사용하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 Automation 계정을 엽니다.
2. **프로세스 자동화**에서 **Runbook**을 선택하여 Runbook 목록을 엽니다.
3. Runbook 페이지의 Runbook 목록에서 그래픽 Runbook을 선택하도록 클릭합니다.
4. **설정**에서 **로깅 및 추적**을 클릭합니다.
5. 로깅 및 추적 페이지의 상세 레코드 기록에서 자세한 정보 로깅을 사용하도록 설정하려면 **켜기**를 클릭하고 작업 수준 추적에서 필요한 추적 수준에 따라 추적 수준을 **기본** 또는 **자세히**로 변경합니다.<br>
   
   ![그래픽 작성 로깅 및 추적 페이지](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="microsoft-azure-monitor-logs"></a>Microsoft Azure Monitor 로그
Automation에서는 Log Analytics 작업 영역으로 Runbook 작업 상태 및 작업 스트림을 보낼 수 있습니다. Azure Monitor 로그를 사용 하면 다음과 같은 작업을 수행할 수 있습니다,

* Automation 작업에 대한 통찰력 확보 
* Runbook 작업 상태(예: 실패 또는 일시 중단)를 기반으로 전자 메일 또는 경고 트리거 
* 작업 스트림에서 고급 쿼리 작성 
* Automation 계정 간에 작업 상호 연결 
* 시간별 작업 기록 시각화    

Azure Monitor 로그 수집, 상호 연결 하 고 작업 데이터를 사용 하 여 통합을 구성 하는 방법에 대 한 자세한 내용은 참조 하세요. [Automation에서 Azure Monitor 로그로 작업 상태 및 작업 스트림 전달](automation-manage-send-joblogs-log-analytics.md)합니다.

## <a name="next-steps"></a>다음 단계
* Runbook 실행, Runbook 작업 모니터링 방법 및 기타 기술 세부 정보를 알아보려면 [Runbook 작업 추적](automation-runbook-execution.md)
* 자식 Runbook을 디자인하고 사용하는 방법을 이해하려면 [Azure Automation의 자식 Runbook](automation-child-runbooks.md)


