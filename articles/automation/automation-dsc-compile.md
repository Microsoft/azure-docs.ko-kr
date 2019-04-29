---
title: Azure Automation 상태 구성에서 구성 컴파일
description: 이 문서에서는 Azure Automation에 대한 DSC(필요한 상태 구성) 구성을 컴파일하는 방법을 설명합니다.
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 09/10/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f2c6f45081b78d330033570ff322f90cd06e50dd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61074192"
---
# <a name="compiling-dsc-configurations-in-azure-automation-state-configuration"></a>Azure Automation 상태 구성에서 DSC 구성 컴파일

Azure Automation 상태 구성을 사용하여 Azure Portal에서 및 Windows PowerShell을 사용하는 두 가지 방법으로 필요한 상태 구성(DSC) 구성을 컴파일할 수 있습니다. 다음 테이블에서는 각각의 특징을 기반으로 어떤 방법을 언제 사용할지 결정하도록 합니다.

**Azure Portal**

- 대화형 사용자 인터페이스를 사용하는 간단한 방법
- 단순한 매개 변수 값을 제공하는 양식
- 작업 상태를 쉽게 추적
- Azure 로그온을 사용하여 액세스 인증

**Windows PowerShell**

- Windows PowerShell cmdlet을 사용하여 명령줄에서 호출
- 여러 단계로 구성된 자동화된 솔루션에 포함할 수 있음
- 단순한 매개 변수 값 및 복잡한 매개 변수 값 제공
- 작업 상태 추적
- 클라이언트에서 PowerShell cmdlet을 지원해야 함
- ConfigurationData 전달
- 자격 증명을 사용하는 구성을 컴파일

컴파일 방법이 결정되면 다음 프로시저를 사용하여 컴파일을 시작합니다.

## <a name="compiling-a-dsc-configuration-with-the-azure-portal"></a>Azure 포털을 사용하여 DSC 구성 컴파일

1. Automation 계정에서 **상태 구성(DSC)** 을 클릭합니다.
1. **구성** 탭 및 컴파일할 구성 이름을 차례로 클릭합니다.
1. **컴파일**을 클릭합니다.
1. 구성에 매개 변수가 없는 경우 컴파일할지 확인하라는 메시지가 표시됩니다. 구성에 매개 변수가 있는 경우 **컴파일 구성** 블레이드를 열어 매개 변수 값을 제공할 수 있습니다. 매개 변수에 대한 자세한 내용은 다음 [**기본 매개 변수**](#basic-parameters) 섹션을 참조하세요.
1. **컴파일 작업** 페이지를 열어서 컴파일 작업의 상태 및 노드 구성(MOF 구성 문서)을 추적할 수 있습니다. 이렇게 하면 Azure Automation 상태 구성 끌어오기 서버에 배치됩니다.

## <a name="compiling-a-dsc-configuration-with-windows-powershell"></a>Windows PowerShell을 사용하여 DSC 구성을 컴파일

Windows PowerShell을 사용하여 컴파일하기 시작하는 데 [`Start-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/start-azurermautomationdsccompilationjob) 를 사용할 수 있습니다. 다음 샘플 코드는 **SampleConfig**이라는 DSC 구성의 컴파일을 시작합니다.

```powershell
Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzureRmAutomationDscCompilationJob` 는 해당 상태를 추적하는 데 사용할 수 있는 컴파일 작업 개체를 반환합니다. 그런 다음, 이 컴파일 작업 개체를 [`Get-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjob)과 함께 사용하여
컴파일 작업 상태를 확인하고, [`Get-AzureRmAutomationDscCompilationJobOutput`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjoboutput)을 사용하여
스트림(출력)을 볼 수 있습니다. 다음 샘플 코드는 **SampleConfig** 구성의 컴파일을 시작하고 완료될 때까지 대기한 다음 해당 스트림을 표시합니다.

```powershell
$CompilationJob = Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($CompilationJob.EndTime –eq $null -and $CompilationJob.Exception –eq $null)
{
    $CompilationJob = $CompilationJob | Get-AzureRmAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzureRmAutomationDscCompilationJobOutput –Stream Any
```

## <a name="basic-parameters"></a>기본 매개 변수

매개 변수 형식 및 속성을 포함하는 DSC 구성의 매개 변수 선언은 Azure Automation runbook과 동일하게 작동합니다. [Azure Automation에서 runbook 시작](automation-starting-a-runbook.md) 을 참조하여 runbook 매개 변수에 대한 자세한 내용을 알아봅니다.

다음 예제에서는 **FeatureName** 및 **IsPresent**라는 두 개의 매개 변수를 사용하여 **ParametersExample.sample** 노드 구에서 속성의 값을 결정하며 이는 컴파일하는 동안 생성됩니다.

```powershell
Configuration ParametersExample
{
    param(
        [Parameter(Mandatory=$true)]
        [string] $FeatureName,

        [Parameter(Mandatory=$true)]
        [boolean] $IsPresent
    )

    $EnsureString = 'Present'
    if($IsPresent -eq $false)
    {
        $EnsureString = 'Absent'
    }

    Node 'sample'
    {
        WindowsFeature ($FeatureName + 'Feature')
        {
            Ensure = $EnsureString
            Name   = $FeatureName
        }
    }
}
```

Azure Automation 상태 구성 포털에서 또는 Azure PowerShell로 기본 매개 변수를 사용하는 DSC 구성을 컴파일할 수 있습니다.

### <a name="portal"></a>포털

포털에서 **컴파일**을 클릭한 후에 매개 변수 값을 입력할 수 있습니다.

![구성 컴파일 매개 변수](./media/automation-dsc-compile/DSC_compiling_1.png)

### <a name="powershell"></a>PowerShell

PowerShell은 키와 매개 변수 이름이 일치하고 매개 변수 값과 값이 같은 [hashtable](/powershell/module/microsoft.powershell.core/about/about_hash_tables) 에 매개 변수가 필요합니다.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

PSCredentials을 매개 변수로 전달하는 방법에 대한 정보는 아래의 [자격 증명 자산](#credential-assets)을 참조하세요.

## <a name="composite-resources"></a>복합 리소스

**복합 리소스**를 사용하면 구성 내에서 중첩된 리소스로 DSC 구성을 사용할 수 있습니다. 이렇게 하면 단일 리소스에 여러 구성을 적용할 수 있습니다. [복합 리소스: DSC 구성을 리소스로 사용](/powershell/dsc/authoringresourcecomposite)에서 **복합 리소스**에 대해 자세히 알아보세요.

> [!NOTE]
> **복합 리소스**가 올바르게 컴파일되도록 하려면 먼저 복합 리소스가 의존하는 DSC 리소스가 Azure Automation 계정 모듈 리포지토리에 처음 설치되었거나 제대로 가져 오지 않았는지 확인해야 합니다.

DSC **복합 리소스**를 추가하려면 리소스 모듈을 보관 파일(*.zip)에 추가해야 합니다. Azure Automation 계정의 모듈 리포지토리로 이동합니다. 그런 다음 '모듈 추가' 단추를 클릭합니다.

![모듈 추가](./media/automation-dsc-compile/add_module.png)

보관 파일이 있는 디렉터리로 이동합니다. 보관 파일을 선택하고 확인을 클릭합니다.

![모듈 선택](./media/automation-dsc-compile/select_dscresource.png)

모듈 디렉터리로 다시 이동되어, 압축을 풀고 Azure Automation에 등록하는 동안 **복합 리소스**의 상태를 모니터링할 수 있습니다.

![복합 리소스 가져오기](./media/automation-dsc-compile/register_composite_resource.png)

모듈이 등록된 후에는 모듈을 클릭하여 **복합 리소스**를 구성에 사용할 수 있는지 확인할 수 있습니다.

![복합 리소스 유효성 검사](./media/automation-dsc-compile/validate_composite_resource.png)

그런 다음 **복합 리소스**를 다음과 같이 구성에 호출할 수 있습니다.

```powershell
Node ($AllNodes.Where{$_.Role -eq 'WebServer'}).NodeName
{
    DomainConfig myCompositeConfig
    {
        DomainName = $DomainName
        Admincreds = $Admincreds
    }

    PSWAWebServer InstallPSWAWebServer
    {
        DependsOn = '[DomainConfig]myCompositeConfig'
    }
}
```

## <a name="configurationdata"></a>ConfigurationData

**ConfigurationData** 를 사용하면 PowerShell DSC를 사용하는 동안 구조적 구성을 환경별 구성과 구분할 수 있습니다. [PowerShell DSC의 "위치"에서 "대상" 분리](https://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) 를 참조하여 **ConfigurationData**에 대해 자세히 알아봅니다.

> [!NOTE]
> Azure PowerShell을 사용하여 Azure Portal이 아닌 Azure Automation 상태 구성에서 컴파일하는 경우 **ConfigurationData**를 사용할 수 있습니다.

다음 예제 DSC 구성은 **$ConfigurationData** 및 **$AllNodes** 키워드를 통해 **ConfigurationData**를 사용합니다. 또한 다음과 같이 예를 들어 [**xWebAdministration** 모듈](https://www.powershellgallery.com/packages/xWebAdministration/)이 필요합니다.

```powershell
Configuration ConfigurationDataSample
{
    Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite

    Write-Verbose $ConfigurationData.NonNodeData.SomeMessage

    Node $AllNodes.Where{$_.Role -eq 'WebServer'}.NodeName
    {
        xWebsite Site
        {
            Name         = $Node.SiteName
            PhysicalPath = $Node.SiteContents
            Ensure       = 'Present'
        }
    }
}
```

PowerShell로 이전 DSC 구성을 컴파일할 수 있습니다. 다음 PowerShell은 Azure Automation 상태 구성 끌어오기 서버에 다음 두 개의 노드 구성을 추가합니다. **ConfigurationDataSample.MyVM1** 및 **ConfigurationDataSample.MyVM3**.

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = 'MyVM1'
            Role = 'WebServer'
        },
        @{
            NodeName = 'MyVM2'
            Role = 'SQLServer'
        },
        @{
            NodeName = 'MyVM3'
            Role = 'WebServer'
        }
    )

    NonNodeData = @{
        SomeMessage = 'I love Azure Automation State Configuration and DSC!'
    }
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ConfigurationDataSample' -ConfigurationData $ConfigData
```

## <a name="assets"></a>자산

자산 참조는 Azure Automation 상태 구성 및 Runbook에서 동일합니다. 자세한 내용은 다음을 참조하세요.

- [인증서](automation-certificates.md)
- [연결](automation-connections.md)
- [자격 증명](automation-credentials.md)
- [변수](automation-variables.md)

### <a name="credential-assets"></a>자격 증명 자산

Azure Automation에서 DSC 구성은 `Get-AutomationPSCredential` cmdlet를 사용하여 Automation 자격 증명 자산을 참조할 수 있습니다. 구성에 **PSCredential** 형식의 매개 변수가 있는 경우 자격 증명을 검색하는 cmdlet에 Azure Automation 자격 증명 자산의 문자열 이름을 전달하여 `Get-AutomationPSCredential` cmdlet을 사용할 수 있습니다. 그런 다음, 해당 개체를 **PSCredential** 개체가 필요한 매개 변수로 사용할 수 있습니다. 내부적으로 해당 이름을 가진 Azure Automation 자격 증명 자산은 검색되고 구성에 전달됩니다. 아래 예제에서는 작업 중인 이 항목을 보여줍니다.

자격 증명을 노드 구성(MOF 구성 문서)에서 안전하게 유지하려면 노드 구성 MOF 파일에 자격 증명을 암호화해야 합니다. 그러나 현재 PowerShell DSC가 노드 구성 MOF을 생성하는 동안 자격 증명을 일반 텍스트로 출력해도 되는지 알아야 합니다. PowerShell DSC은 Azure Automation이 컴파일 작업을 통해 생성된 후에 전체 MOF 파일을 암호화한다는 것을 모르기 때문입니다.

PowerShell DSC가 [**ConfigurationData**](#configurationdata)을 클릭합니다. DSC 구성에 표시되고 자격 증명을 사용하는 각 노드 블록 이름의 경우 **ConfigurationData**를 통해 `PSDscAllowPlainTextPassword = $true`을 전달해야 합니다.

다음 예제에서는 Automation 자격 증명 자산을 사용하는 DSC 구성을 보여줍니다.

```powershell
Configuration CredentialSample
{
    Import-DscResource -ModuleName PSDesiredStateConfiguration
    $Cred = Get-AutomationPSCredential 'SomeCredentialAsset'

    Node $AllNodes.NodeName
    {
        File ExampleFile
        {
            SourcePath      = '\\Server\share\path\file.ext'
            DestinationPath = 'C:\destinationPath'
            Credential      = $Cred
        }
    }
}
```

PowerShell로 이전 DSC 구성을 컴파일할 수 있습니다. 다음 PowerShell은 Azure Automation 상태 구성 끌어오기 서버에 다음 두 개의 노드 구성을 추가합니다. **CredentialSample.MyVM1** 및 **CredentialSample.MyVM2**.

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = '*'
            PSDscAllowPlainTextPassword = $True
        },
        @{
            NodeName = 'MyVM1'
        },
        @{
            NodeName = 'MyVM2'
        }
    )
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'CredentialSample' -ConfigurationData $ConfigData
```

> [!NOTE]
> 컴파일이 완료되면 다음과 같은 오류 메시지가 나타날 수 있습니다. **'Microsoft.PowerShell.Management' 스냅인을 이미 가져왔으므로 'Microsoft.PowerShell.Management' 모듈을 가져오지 못했습니다.** 이 경고는 무시해도 됩니다.

## <a name="partial-configuration"></a>부분 구성

Azure Automation 상태 구성의 사용을 지원 [부분 구성을](https://docs.microsoft.com/en-us/powershell/dsc/pull-server/partialconfigs)합니다.
이 시나리오에서는 DSC 구성을 여러를 독립적으로 관리 하도록 구성 됩니다 하 고 각 구성은 Azure Automation에서 retreieved 합니다.
그러나 하나의 구성만 automation 계정당 노드에 할당할 수 있습니다.
즉, 노드에 대 한 두 가지 구성을 사용 하는 경우 두 개의 automation 계정이 필요 합니다.
팀이 작동 하는 방식에 대 한 자세한 내용은 공동으로 서버를 관리 하려면 함께 구성을 코드로 사용 하 여 참조 하세요 [CI/CD 파이프라인의 역할 이해 DSC](https://docs.microsoft.com/en-us/powershell/dsc/overview/authoringadvanced)합니다.

## <a name="importing-node-configurations"></a>노드 구성 가져오기

Azure 외부에서 컴파일한 노드 구성(MOF)을 가져올 수도 있습니다. 이 경우 장점 중 하나는 노드 구성을 서명할 수 있다는 것입니다. 서명된 노드 구성은 DSC 에이전트에 의해 관리되는 노드에서 로컬로 확인되어 인증된 출처에서 가져온 노드에 구성을 적용할 수 있습니다.

> [!NOTE]
> 서명된 구성을 Azure Automation 계정으로 가져오기를 사용할 수 있지만 Azure Automation에서는 현재 서명된 구성의 컴파일을 지원하지 않습니다.

> [!NOTE]
> 노드 구성 파일은 Azure Automation으로 가져오기 위해 1MB보다 크지 않아야 합니다.

노드 구성에 서명하는 방법에 대한 자세한 내용은 [WMF 5.1의 향상된 기능 - 구성 및 모듈에 서명하는 방법](/powershell/wmf/5.1/dsc-improvements#dsc-module-and-configuration-signing-validations)을 참조하세요.

### <a name="importing-a-node-configuration-in-the-azure-portal"></a>Azure Portal에서 노드 구성 가져오기

1. Automation 계정 페이지의 **구성 관리**에서 **상태 구성(DSC)** 을 클릭합니다.
1. **상태 구성(DSC)** 페이지에서 **구성** 탭을 클릭한 다음, **+ 추가**를 클릭합니다.
1. **가져오기** 페이지에서 **노드 구성 파일** 텍스트 상자 옆의 폴더 아이콘을 클릭하여 로컬 컴퓨터에서 노드 구성 파일(MOF)을 찾습니다.

   ![로컬 파일 찾기](./media/automation-dsc-compile/import-browse.png)

1. **구성 이름** 텍스트 상자에 이름을 입력합니다. 이 이름은 노드 구성이 컴파일된 구성 이름과 일치해야 합니다.
1. **확인**을 클릭합니다.

### <a name="importing-a-node-configuration-with-powershell"></a>PowerShell을 사용하여 노드 구성 가져오기

[Import-AzureRmAutomationDscNodeConfiguration](/powershell/module/azurerm.automation/import-azurermautomationdscnodeconfiguration) cmdlet을 사용하여 노드 구성을 Automation 계정으로 가져올 수 있습니다.

```powershell
Import-AzureRmAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>다음 단계

- 시작하려면 [Azure Automation 상태 구성 시작하기](automation-dsc-getting-started.md)를 참조하세요.
- DSC 구성을 대상 노드에 할당할 수 있도록 DSC 구성을 컴파일하는 방법에 대해 알아보려면 [Azure Automation 상태 구성에서 구성 컴파일](automation-dsc-compile.md)을 참조하세요.
- PowerShell cmdlet 참조는 [Azure Automation 상태 구성 cmdlet](/powershell/module/azurerm.automation/#automation)을 참조하세요.
- 가격 책정 정보는 [Azure Automation 상태 구성 가격 책정](https://azure.microsoft.com/pricing/details/automation/)을 참조하세요.
- 지속적인 배포 파이프라인에서 Azure Automation 상태 구성을 사용하는 예제는 [Azure Automation 상태 구성 및 Chocolatey를 사용한 지속적인 배포](automation-dsc-cd-chocolatey.md)를 참조하세요.
