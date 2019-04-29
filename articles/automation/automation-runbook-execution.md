---
title: Azure Automation에서 Runbook 실행
description: Azure Automation의 Runbook이 처리되는 방법에 대한 자세한 내용을 설명합니다.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 38dd4d13aa45b69fc846ef9b6b2e1b56f56de573
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60738194"
---
# <a name="runbook-execution-in-azure-automation"></a>Azure Automation에서 Runbook 실행

Azure Automation에서 Runbook을 시작하면 작업이 생성됩니다. 작업은 Runbook의 단일 실행 인스턴스입니다. 각 작업을 실행하기 위해 Azure Automation 작업자가 할당됩니다. 작업자가 많은 Azure 계정에서 공유되지만 여러 Automation 계정의 작업은 서로 격리됩니다. 사용자는 작업에 대한 요청을 처리할 작업자를 제어할 수 없습니다. 단일 Runbook에서 동시에 많은 작업을 실행할 수 있습니다. 동일한 Automation 계정의 작업 실행 환경은 다시 사용할 수 있습니다. 더 많은 작업을 동시에 실행할 수록 동일한 샌드박스에 더 자주 디스패치될 수 있습니다. 동일한 샌드박스에서 실행하는 작업은 서로 영향을 줄 수 있습니다. 한 가지 예는 `Disconnect-AzureRMAccount` cmdlet을 실행하는 것입니다. 이 cmdlet을 실행하면 공유 샌드박스 프로세스의 각 Runbook 작업의 연결을 끊습니다. Azure Portal에서 Runbook 목록을 확인하면 각 Runbook에 대해 시작된 모든 작업의 상태가 나열됩니다. 각 Runbook에 대한 작업 목록을 확인하여 각 작업의 상태를 추적할 수 있습니다. 작업 로그는 최대 30일 동안 저장됩니다. 다양한 작업 상태에 대한 설명은 [작업 상태](#job-statuses)를 참조하세요.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

다음 다이어그램에 대 한 runbook 작업의 수명 주기를 보여 줍니다 [PowerShell runbook](automation-runbook-types.md#powershell-runbooks)를 [그래픽 runbook](automation-runbook-types.md#graphical-runbooks) 하 고 [PowerShell 워크플로 runbook](automation-runbook-types.md#powershell-workflow-runbooks)합니다.

![작업 상태 - PowerShell 워크플로](./media/automation-runbook-execution/job-statuses.png)

Azure 구독에 연결하면 작업에서 Azure 리소스에 액세스할 수 있습니다. 단, 공용 클라우드에서 액세스할 수 있는 데이터 센터의 리소스에만 액세스할 수 있습니다.

## <a name="where-to-run-your-runbooks"></a>Runbook을 실행하는 위치

Azure Automation의 Runbook은 Azure의 샌드박스 또는 [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)에서 실행할 수 있습니다. 샌드박스는 여러 작업에서 사용할 수 있는 Azure의 공유 환경입니다. 동일한 샌드박스를 사용하는 작업에는 샌드박스의 리소스 제한이 적용됩니다. Hybrid Runbook Worker 역할을 호스팅하는 컴퓨터에서 직접 및 해당 로컬 리소스를 관리 하 여 환경에서 리소스에 대 한 runbook을 실행할 수 있습니다. Runbook은 Azure Automation에 저장 및 관리된 후 하나 이상의 할당된 컴퓨터에 전달됩니다. 대부분의 runbook은 Azure 샌드박스에서에서 쉽게 실행할 수 있습니다. Azure 샌드박스를 통해 하이브리드 Runbook을 선택하여 Runbook을 실행하도록 추천되는 특정 시나리오가 있습니다. 몇 가지 예제 시나리오의 목록은 다음 표를 참조하세요.

|Task|최선의 선택|메모|
|---|---|---|
|Azure 리소스와 통합|Azure 샌드박스|Azure에서 호스팅되며, 인증이 더 간단합니다. Azure VM에서 Hybrid Runbook Worker를 사용하는 경우 [Azure 리소스에 대한 관리 ID](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources)를 사용할 수 있습니다.|
|Azure 리소스를 관리하는 최적의 성능|Azure 샌드박스|스크립트 있으며 대기 시간이 짧습니다 동일한 환경에서 실행 됩니다.|
|운영 비용 최소화|Azure 샌드박스|컴퓨팅 오버헤드가 없고 VM이 필요하지 않습니다.|
|장기 실행 스크립트|Hybrid Runbook Worker|Azure 샌드박스는 [리소스에 제한](../azure-subscription-service-limits.md#automation-limits)이 있습니다.|
|로컬 서비스와 상호 작용|Hybrid Runbook Worker|호스트 머신에 직접 액세스할 수 있습니다.|
|타사 소프트웨어 및 실행 파일 필요|Hybrid Runbook Worker|OS를 관리하고 소프트웨어를 설치할 수 있습니다.|
|Runbook으로 파일 또는 폴더 모니터링|Hybrid Runbook Worker|Hybrid Runbook Worker에서 [감시자 작업](automation-watchers-tutorial.md)을 사용합니다.|
|리소스 사용량이 많은 스크립트|Hybrid Runbook Worker| Azure 샌드박스는 [리소스에 제한](../azure-subscription-service-limits.md#automation-limits)이 있습니다.|
|특정 요구 사항이 있는 모듈 사용| Hybrid Runbook Worker|일부 사례:</br> **WinSCP** - winscp.exe에 대한 종속성 </br> **IISAdministration** - IIS를 사용하도록 설정해야 함|
|설치 관리자가 필요한 모듈 설치|Hybrid Runbook Worker|샌드박스 용 모듈 copiable 있어야 합니다.|
|4.7.2와 다른 .NET 프레임워크가 필요한 Runbook 또는 모듈 사용|Hybrid Runbook Worker|Automation 샌드박스에는 NET Framework 4.7.2가 있으며, 업그레이드할 수 있는 방법이 없습니다.|
|권한 상승 해야 하는 스크립트|Hybrid Runbook Worker|샌드박스 권한 상승을 허용 하지 않습니다. 이 해결 하기 위해 Hybrid Runbook Worker를 사용 하 고 UAC를 사용 하 여 해제할 수 있습니다 `Invoke-Command` 경우 권한 상승이 필요한 있는 명령을 실행|
|WMI에 대 한 액세스를 필요로 하는 스크립트|Hybrid Runbook Worker|샌드박스의 클라우드에서 실행 중인 작업 [WMI에 대 한 액세스 권한이 없습니다](#device-and-application-characteristics)|

## <a name="runbook-behavior"></a>Runbook 동작

Runbook은 내부에 정의된 논리에 따라 실행됩니다. Runbook이 중단되면 해당 Runbook이 처음부터 다시 시작됩니다. 일시적인 문제가 있는 경우 이 동작을 수행하려면 다시 시작을 지원하는 방식으로 Runbook을 작성해야 합니다.

Azure에서 실행 하는 Runbook에서 시작 하는 PowerShell 작업 샌드박스 전체 언어 모드에서 실행 되지 않을 수 있습니다. PowerShell 언어 모드에 대 한 자세한 내용은 참조 하세요 [PowerShell 언어 모드](/powershell/module/microsoft.powershell.core/about/about_language_modes)합니다. Azure Automation에서 작업과 상호 작용 하는 방법에 대 한 자세한 내용은 참조 하세요. [PowerShell 사용 하 여 작업 상태를 검색 합니다.](#retrieving-job-status-using-powershell)

### <a name="creating-resources"></a>리소스 만들기

스크립트에서 리소스를 만드는 경우 리소스를 다시 만들기 전에 해당 리소스가 이미 있는지 확인해야 합니다. 기본 예제는 다음 예제와 같습니다.

```powershell
$vmName = "WindowsVM1"
$resourceGroupName = "myResourceGroup"
$myCred = Get-AutomationPSCredential "MyCredential"
$vmExists = Get-AzureRmResource -Name $vmName -ResourceGroupName $resourceGroupName

if(!$vmExists)
    {
    Write-Output "VM $vmName does not exists, creating"
    New-AzureRmVM -Name $vmName -ResourceGroupName $resourceGroupName -Credential $myCred
    }
else
    {
    Write-Output "VM $vmName already exists, skipping"
    }
```

### <a name="time-dependant-scripts"></a>시간 종속 스크립트

Runbook을 작성할 때는 신중하게 고려해야 합니다. 앞에서 설명한 대로 Runbook은 강력하고 Runbook이 다시 시작되거나 실패할 수 있는 일시적인 오류를 처리할 수 있는 방식으로 작성되어야 합니다. Runbook이 실패 하면 다시 시도 됩니다. Runbook이 실행 되는 시간 제약 조건 내에서 일반적으로, 확인, 시작 같은 작업을 수행 하려면 runbook에서 구현 하는 실행 시간이 종료 하거나 확장 하는 논리는 특정 시간 동안에 실행 됩니다.

### <a name="tracking-progress"></a>진행률 추적

Runbook을 모듈식으로 작성하는 것이 좋습니다. 즉, Runbook의 논리를 쉽게 다시 사용하고 다시 시작할 수 있도록 구조화합니다. Runbook의 진행 상황을 추적 하는 것은 문제가 있는 경우 runbook의 논리 올바르게 실행 되도록 하려면 좋은 방법입니다. Runbook에서 진행률을 추적할 수 있는 몇 가지 방법은 스토리지 계정, 데이터베이스 또는 공유 파일과 같은 외부 원본을 사용하는 것입니다. 외부적으로 상태를 추적 하 여 만들 수 있습니다 논리 첫 번째 검사 하도록 runbook의 runbook 수행한 마지막 작업의 상태. 그런 다음 결과 건너뛰거나 기반으로 하거나 runbook의 특정 작업을 계속 합니다.

### <a name="prevent-concurrent-jobs"></a>동시 작업 방지

여러 작업을 동시에 실행하는 경우 일부 Runbook에서는 이상하게 작동할 수 있습니다. 이 경우 이미 실행 중인 작업이 Runbook에 있는지 확인하는 논리를 구현해야 합니다. 이 동작을 수행하는 방법에 대한 기본 예제는 다음 예제와 같습니다.

```powershell
# Authenticate to Azure
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $connection.SubscriptionID

# Check for already running or new runbooks
$runbookName = "<RunbookName>"
$rgName = "<ResourceGroupName>"
$aaName = "<AutomationAccountName>"
$jobs = Get-AzureRmAutomationJob -ResourceGroupName $rgName -AutomationAccountName $aaName -RunbookName $runbookName -AzureRmContext $AzureContext

# If then check to see if it is already running
$runningCount = ($jobs | ? {$_.Status -eq "Running"}).count

If (($jobs.status -contains "Running" -And $runningCount -gt 1 ) -Or ($jobs.Status -eq "New")) {
    # Exit code
    Write-Output "Runbook is already running"
    Exit 1
} else {
    # Insert Your code here
}
```

### <a name="working-with-multiple-subscriptions"></a>여러 구독 작업

Runbook에 사용 하 여 필요한 여러 구독을 처리 하는 runbook을 작성할 때 합니다 [Disable-azurermcontextautosave](/powershell/module/azurerm.profile/disable-azurermcontextautosave) 수 있는 다른 runbook에서 인증 컨텍스트 검색 되지 않으면 되도록 cmdlet 동일한 샌드박스에서 실행 합니다. 다음 사용 해야 합니다 `-AzureRmContext` 매개 변수를 프로그램 `AzureRM` cmdlet 적절 한 컨텍스트를 전달 합니다.

```powershell
# Ensures you do not inherit an AzureRMContext in your runbook
Disable-AzureRmContextAutosave –Scope Process

$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal `
-Tenant $Conn.TenantID `
-ApplicationID $Conn.ApplicationID `
-CertificateThumbprint $Conn.CertificateThumbprint

$context = Get-AzureRmContext

$ChildRunbookName = 'ChildRunbookDemo'
$AutomationAccountName = 'myAutomationAccount'
$ResourceGroupName = 'myResourceGroup'

Start-AzureRmAutomationRunbook `
    -ResourceGroupName $ResourceGroupName `
    -AutomationAccountName $AutomationAccountName `
    -Name $ChildRunbookName `
    -DefaultProfile $context
```

### <a name="handling-exceptions"></a>예외 처리

스크립트를 작성할 때 반드시 예외 및 잠재적인 일시적 오류를 처리할 수 있어야 합니다. 다음은 예외 또는 runbook 사용 하 여 일시적인 문제를 처리 하는 몇 가지 다른 방법입니다.

#### <a name="erroractionpreference"></a>$ErrorActionPreference

합니다 [$ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) 기본 설정 변수 PowerShell 비종료 오류에 반응 하는 방법을 결정 합니다. 종료 오류 영향을 받지 않는 `$ErrorActionPreference`, 항상 종료 합니다. 사용 하 여 `$ErrorActionPreference`를 정상적으로 종료 되지 않는 오류와 같은 `PathNotFound` 에서 `Get-ChildItem` cmdlet은 runbook이 완료를 중지 합니다. 다음 예제에서는 사용 하 여 `$ErrorActionPreference`입니다. 최종 `Write-Output` 줄은 스크립트에서 중지 하므로 실행 되지 않습니다.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-Childitem -path nofile.txt
Write-Output "This message will not show"
```

#### <a name="try-catch-finally"></a>Try Catch Finally

[Try Catch](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) PowerShell 스크립트에서 종료 오류를 처리할 수 있도록 하는 데 사용 됩니다. Try Catch를 사용 하 여 특정 예외 또는 일반 예외를 catch 할 수 있습니다. Catch 문이 오류를 추적 하는 데 사용 하 하거나 오류를 처리 하려고 하는 데 사용 해야 합니다. 다음 예제에서는 존재 하지 않는 파일을 다운로드 하려고 시도 합니다. Catch는 `System.Net.WebException` 마지막 값이 반환 하는 다른 예외가 발생 한 경우 예외입니다.

```powershell-interactive
try
{
   $wc = new-object System.Net.WebClient
   $wc.DownloadFile("http://www.contoso.com/MyDoc.doc")
}
catch [System.Net.WebException]
{
    "Unable to download MyDoc.doc from http://www.contoso.com."
}
catch
{
    "An error occurred that could not be resolved."
}
```

#### <a name="throw"></a>Throw

[Throw](/powershell/module/microsoft.powershell.core/about/about_throw) 종료 오류를 생성 데 사용할 수 있습니다. 이 runbook의 고유한 논리를 정의할 때 유용할 수 있습니다. 스크립트 실행을 중단 해야 하는 특정 기준을 충족 될 경우, 사용할 수 있습니다 `throw` 스크립트를 중지 합니다. 다음 예제에서는 컴퓨터를 함수 매개 변수를 사용 하 여 필요한 `throw`합니다.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

### <a name="using-executables-or-calling-processes"></a>실행 파일 또는 호출 프로세스 사용

Azure 샌드박스에서 실행 되는 Runbook (예:.exe 또는 subprocess.call) 호출 프로세스를 지원 하지 않습니다. 모든 기본 Api에 액세스할 수 있는 컨테이너에서 실행 되는 공유 프로세스는 다음과 같이 Azure 샌드박스에서 때문입니다. 타사 소프트웨어 또는 하위 프로세스의 호출이 필요한 시나리오의 경우 [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)에서 Runbook을 실행하는 것이 좋습니다.

### <a name="device-and-application-characteristics"></a>장치 및 응용 프로그램 특성

Azure 샌드박스에서 실행 되는 Runbook 작업에는 장치 또는 응용 프로그램 특성에 대 한 액세스가 없습니다. Windows에서 성능 메트릭 쿼리를 사용 하는 가장 일반적인 API는 WMI. 이러한 공통 메트릭 중 일부는 메모리 및 CPU 사용량입니다. 그러나에 상관 없이 어떤 API를 사용 합니다. 클라우드에서 실행 중인 작업 없는의 웹 기반 관리 WBEM (Enterprise)에 모델 CIM (Common Information) 작성 되는 Microsoft 구현에 대 한 액세스는 장치 및 응용 프로그램 특징을 정의 하는 것에 대 한 업계 표준입니다.

## <a name="job-statuses"></a>작업 상태

다음 표에서는 작업의 가능한 여러 상태를 설명합니다. PowerShell에는 두 가지 유형의 오류, 즉 종료되는 오류와 종료되지 않는 오류가 있습니다. 종료되는 오류는 발생하는 경우 Runbook 상태를 **실패**로 설정합니다. 종료되지 않는 오류를 사용하면 오류 발생 후에도 스크립트가 계속 진행될 수 있습니다. 종료되지 않는 오류의 예는 존재하지 않는 경로로 `Get-ChildItem` cmdlet을 사용하는 것입니다. PowerShell은 경로가 없다는 것을 확인하고 오류가 throw한 후 다음 폴더로 계속 진행됩니다. 이 오류는 Runbook 상태를 **실패**로 설정하지 않으며 **완료**로 표시될 수 있습니다. 종료되지 않는 오류에서 강제로 Runbook을 중지하려면 cmdlet에서 `-ErrorAction Stop`을 사용할 수 있습니다.

| 상태 | 설명 |
|:--- |:--- |
| Completed |작업이 완료되었습니다. |
| 실패 |[그래픽 및 PowerShell 워크플로 Runbook](automation-runbook-types.md)의 경우 Runbook을 컴파일하지 못했습니다. [PowerShell 스크립트 Runbook](automation-runbook-types.md)의 경우 Runbook을 시작하지 못했거나 작업에서 예외가 발생했습니다. |
| Failed, waiting for resources |작업이 [공평 분배](#fair-share) 한도에 세 번 도달했기 때문에 실패했고 매번 동일한 검사점 또는 Runbook의 처음부터 시작되었습니다. |
| Queued |작업이 시작될 수 있도록 Automation 작업자의 리소스가 사용 가능한 상태가 되기를 기다리고 있습니다. |
| 시작 중 |작업이 작업자에게 지정되었으며 시스템이 작업을 시작하고 있습니다. |
| Resuming |시스템이 일시 중단된 후 작업을 재개하는 중입니다. |
| 실행 중 |작업이 실행 중입니다. |
| Running, waiting for resources |작업이 [공평 분배](#fair-share) 한도에 도달했기 때문에 언로드되었습니다. 잠시 후 마지막 검사점에서 작업이 다시 시작됩니다. |
| 중지됨 |작업이 완료되기 전에 사용자에 의해 중지되었습니다. |
| 중지 중 |시스템이 작업을 중지하는 중입니다. |
| 일시 중단 |작업이 Runbook의 사용자, 시스템 또는 명령에 의해 일시 중단되었습니다. Runbook에 검사점이 없으면 Runbook의 처음부터 시작됩니다. 검사점이 있으면 다시 시작되고 마지막 검사점에서 재개될 수 있습니다. 예외가 발생하는 경우에만 시스템에서 Runbook을 일시 중단합니다. 기본적으로 ErrorActionPreference는 **Continue**로 설정되며, 이는 오류 발생 시 작업이 계속 실행된다는 의미입니다. 이 기본 설정 변수가 **Stop** 으로 설정된 경우 오류 발생 시 작업이 일시 중단됩니다. [그래픽 및 PowerShell 워크플로 Runbook](automation-runbook-types.md) 에만 적용됩니다. |
| Suspending |시스템이 사용자의 요청에 따라 작업을 일시 중단하려고 합니다. Runbook의 다음 검사점에 도달해야만 Runbook을 일시 중단할 수 있습니다. 이미 마지막 검사점을 지난 경우 완료되어야만 일시 중단할 수 있습니다. [그래픽 및 PowerShell 워크플로 Runbook](automation-runbook-types.md) 에만 적용됩니다. |

## <a name="viewing-job-status-from-the-azure-portal"></a>Azure Portal에서 작업 상태 보기

모든 Runbook 작업의 요약 상태를 보거나 Azure Portal에서 특정 Runbook 작업의 세부 정보로 드릴할 수 있습니다. 또한 Runbook의 작업 상태와 작업 스트림을 전달하기 위해 Log Analytics 작업 영역과 통합하도록 구성할 수도 있습니다. Azure Monitor 로그를 통합 하는 방법에 대 한 자세한 내용은 참조 하세요. [Automation에서 Azure Monitor 로그로 작업 상태 및 작업 스트림 전달](automation-manage-send-joblogs-log-analytics.md)합니다.

### <a name="automation-runbook-jobs-summary"></a>Automation runbook 작업 요약

선택한 Automation 계정 오른쪽의 **작업 통계** 타일 아래에서 모든 Runbook 작업에 대한 요약을 확인할 수 있습니다.

![작업 통계 타일](./media/automation-runbook-execution/automation-account-job-status-summary.png)

이 타일은 실행된 모든 작업의 개수 및 작업 상태를 그래픽으로 표시합니다.

타일을 클릭하면 실행된 모든 작업의 요약 목록이 포함된 **작업** 페이지가 표시됩니다. 이 페이지는 상태, 시작 시간 및 완료 시간을 표시합니다.

![Automation 계정 작업 페이지](./media/automation-runbook-execution/automation-account-jobs-status-blade.png)

**작업 필터링**을 선택하여 작업 목록을 필터링할 수 있습니다. 특정 Runbook이나 작업 상태를 필터링하거나 드롭다운 목록에서 검색할 시간 범위를 필터링할 수 있습니다.

![작업 상태 필터링](./media/automation-runbook-execution/automation-account-jobs-filter.png)

또는 Automation 계정의 **Runbook** 페이지에서 Runbook을 선택하여 특정 Runbook에 대한 작업 요약 세부 정보를 확인한 다음, **작업** 타일을 선택할 수 있습니다. 이 작업을 수행하면 **작업** 페이지가 표시되며, 여기서 작업 레코드를 클릭하여 세부 정보와 출력을 확인할 수 있습니다.

![Automation 계정 작업 페이지](./media/automation-runbook-execution/automation-runbook-job-summary-blade.png)

### <a name="job-summary"></a>작업 요약

특정 Runbook에 대해 만들어진 모든 작업 및 해당 작업의 최신 상태가 나와 있는 목록을 볼 수 있습니다. 이 목록에서 작업 상태 및 날짜 범위를 필터링하면 작업의 마지막 변경 사항을 볼 수 있습니다. 세부 정보와 출력을 표시하려면 작업의 이름을 클릭합니다. 작업의 세부 보기에는 해당 작업에 제공된 Runbook 매개 변수의 값이 포함됩니다.

다음 단계를 수행하여 Runbook의 작업을 볼 수 있습니다.

1. Azure Portal에서 **Automation**을 선택한 다음 Automation 계정의 이름을 선택합니다.
2. 허브에서 **Runbook**을 선택하고 **Runbook** 페이지의 목록에서 Runbook을 선택합니다.
3. 선택한 Runbook에 대한 페이지에서 **작업** 타일을 클릭합니다.
4. 목록에 있는 작업 중 하나를 클릭하면 Runbook 작업 세부 정보 페이지에서 세부 정보와 출력을 확인할 수 있습니다.

## <a name="retrieving-job-status-using-powershell"></a>PowerShell을 사용 하 여 작업 상태 검색

[Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob)을 사용하여 Runbook에 대해 생성된 작업 및 특정 작업을 검색할 수 있습니다. PowerShell을 사용 하 여 runbook을 시작 하는 경우 [Start-azurermautomationrunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook), 결과 작업이 반환 됩니다. 작업의 출력을 얻으려면 [Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput)을 사용합니다.

다음 명령 예제는 샘플 Runbook에 대한 마지막 작업을 검색하고 작업의 상태, Runbook 매개 변수에 제공된 값, 작업의 출력을 표시합니다.

```azurepowershell-interactive
$job = (Get-AzureRmAutomationJob –AutomationAccountName "MyAutomationAccount" `
–RunbookName "Test-Runbook" -ResourceGroupName "ResourceGroup01" | sort LastModifiedDate –desc)[0]
$job.Status
$job.JobParameters
Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAcct" -Id $job.JobId –Stream Output
```

다음 예제는 특정 작업에 대한 출력을 검색하고, 각 레코드를 반환합니다. 레코드 중 하나에 대한 예외가 발생한 경우 값 대신 예외가 쓰여집니다. 이 동작은 출력하는 동안 예외가 일반적으로 기록되지 않을 수 있는 추가 정보를 제공하므로 유용합니다.

```azurepowershell-interactive
$output = Get-AzureRmAutomationJobOutput -AutomationAccountName <AutomationAccountName> -Id <jobID> -ResourceGroupName <ResourceGroupName> -Stream "Any"
foreach($item in $output)
{
    $fullRecord = Get-AzureRmAutomationJobOutputRecord -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -JobId <jobID> -Id $item.StreamRecordId
    if ($fullRecord.Type -eq "Error")
    {
        $fullRecord.Value.Exception
    }
    else
    {
    $fullRecord.Value
    }
}
```

## <a name="get-details-from-activity-log"></a>활동 로그에서 세부 정보 가져오기

Runbook을 시작한 사용자 또는 계정과 같은 기타 세부 정보는 Automation 계정의 활동 로그에서 검색할 수 있습니다. 다음 PowerShell 예에서는 문제의 Runbook을 실행하는 마지막 사용자를 제공합니다.

```powershell-interactive
$SubID = "00000000-0000-0000-0000-000000000000"
$AutomationResourceGroupName = "MyResourceGroup"
$AutomationAccountName = "MyAutomationAccount"
$RunbookName = "MyRunbook"
$StartTime = (Get-Date).AddDays(-1)
$JobActivityLogs = Get-AzureRmLog -ResourceGroupName $AutomationResourceGroupName -StartTime $StartTime `
                                | Where-Object {$_.Authorization.Action -eq "Microsoft.Automation/automationAccounts/jobs/write"}

$JobInfo = @{}
foreach ($log in $JobActivityLogs)
{
    # Get job resource
    $JobResource = Get-AzureRmResource -ResourceId $log.ResourceId

    if ($JobInfo[$log.SubmissionTimestamp] -eq $null -and $JobResource.Properties.runbook.name -eq $RunbookName)
    { 
        # Get runbook
        $Runbook = Get-AzureRmAutomationJob -ResourceGroupName $AutomationResourceGroupName -AutomationAccountName $AutomationAccountName `
                                            -Id $JobResource.Properties.jobId | ? {$_.RunbookName -eq $RunbookName}

        # Add job information to hash table
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName,$Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | sort key -Descending | Select-Object -First 1
```

## <a name="fair-share"></a>공평 분배

클라우드의 모든 runbook 간에 리소스를 공유 하려면 Azure Automation는 일시적으로 언로드합니다 하 하거나 3 시간 이상 동안 실행 된 모든 작업을 중지 합니다. [PowerShell 기반 Runbook](automation-runbook-types.md#powershell-runbooks) 및 [Python Runbook](automation-runbook-types.md#python-runbooks)의 작업은 중지된 후 다시 시작되지 않으며 작업 상태는 중지됨으로 표시됩니다.

장기 실행 작업의 경우 [Hybrid Runbook Worker](automation-hrw-run-runbooks.md#job-behavior)를 사용하는 것이 좋습니다. Hybrid Runbook Worker는 공평 분배로 제한되지 않으며, Runbook을 실행할 수 있는 기간에 대한 제한이 없습니다. 다른 작업 [제한](../azure-subscription-service-limits.md#automation-limits)은 Azure 샌드박스 및 Hybrid Runbook Worker에 모두 적용됩니다. Hybrid Runbook Worker는 3 시간 공평 분배 제한에 따라 제한 되지, 하는 동안 예기치 않은 로컬 인프라 문제에서 다시 시작 동작을 지원 하기 위해 실행할 runbook은 개발 합니다.

또 다른 옵션은 자식 Runbook을 사용하여 Runbook을 최적화하는 것입니다. Runbook이 여러 데이터베이스의 데이터베이스 작업과 같이 여러 리소스에서 동일한 함수를 반복하는 경우, 해당 함수를 [자식 Runbook](automation-child-runbooks.md)으로 이동하고 [Start-AzureRMAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) cmdlet을 사용하여 이를 호출할 수 있습니다. 이러한 자식 Runbook은 각각 개별 프로세스에서 병렬로 실행됩니다. 이 동작은 부모 Runbook이 완료되는 총 기간을 감소시킵니다. 사용할 수는 [Get-azurermautomationjob](/powershell/module/azurerm.automation/Get-AzureRmAutomationJob) 작업이 자식 runbook이 완료 된 후 수행 하는 경우 각 자식에 대 한 작업 상태를 확인 하도록 runbook의 cmdlet.

## <a name="next-steps"></a>다음 단계

* Azure Automation에서 runbook을 시작하는 데 사용할 수 있는 여러 가지 방법에 대해 자세히 알아보려면 [Azure Automation에서 Runbook 시작](automation-starting-a-runbook.md)을 참조하세요.

