---
title: Azure Automation 상태 구성에서 구성 컴파일
description: 이 문서에서는 Azure Automation에 대한 DSC(필요한 상태 구성) 구성을 컴파일하는 방법을 설명합니다.
services: automation
ms.subservice: dsc
ms.date: 09/10/2018
ms.topic: conceptual
ms.openlocfilehash: d7f22e5042f301d7c16573318b6ddd1585f1e350
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75770002"
---
# <a name="compiling-dsc-configurations-in-azure-automation-state-configuration"></a>Azure Automation 상태 구성에서 DSC 구성 컴파일

Azure Automation 상태 구성을 사용 하는 두 가지 방법, 즉 Azure와 Windows PowerShell에서 DSC (필요한 상태 구성) 구성을 컴파일할 수 있습니다. 다음 테이블에서는 각각의 특징을 기반으로 어떤 방법을 언제 사용할지 결정하도록 합니다.

- Azure 상태 구성 컴파일 서비스
  - 대화형 사용자 인터페이스를 사용 하는 초보자 방법
   - 작업 상태를 쉽게 추적

- Windows PowerShell
  - 로컬 워크스테이션 또는 빌드 서비스의 Windows PowerShell에서 호출
  - 개발 테스트 파이프라인과 통합
  - 복잡 한 매개 변수 값 제공
  - 규모에 맞게 노드 및 비 노드 데이터 작업
  - 뛰어난 성능 향상

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>Azure 상태 구성에서 DSC 구성 컴파일

### <a name="portal"></a>포털

1. Automation 계정에서 **상태 구성(DSC)** 을 클릭합니다.
1. **구성** 탭 및 컴파일할 구성 이름을 차례로 클릭합니다.
1. **컴파일**을 클릭합니다.
1. 구성에 매개 변수가 없는 경우 컴파일할지 확인하라는 메시지가 표시됩니다. 구성에 매개 변수가 있는 경우 **컴파일 구성** 블레이드를 열어 매개 변수 값을 제공할 수 있습니다. 매개 변수에 대한 자세한 내용은 다음 [**기본 매개 변수**](#basic-parameters) 섹션을 참조하세요.
1. **컴파일 작업** 페이지를 열어서 컴파일 작업의 상태 및 노드 구성(MOF 구성 문서)을 추적할 수 있습니다. 이렇게 하면 Azure Automation 상태 구성 끌어오기 서버에 배치됩니다.

### <a name="azure-powershell"></a>Azure PowerShell

Windows PowerShell을 사용하여 컴파일하기 시작하는 데 [`Start-AzAutomationDscCompilationJob`](/powershell/module/az.automation/start-azautomationdsccompilationjob) 를 사용할 수 있습니다. 다음 샘플 코드는 **SampleConfig**이라는 DSC 구성의 컴파일을 시작합니다.

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzAutomationDscCompilationJob` 는 해당 상태를 추적하는 데 사용할 수 있는 컴파일 작업 개체를 반환합니다. 그런 다음, 이 컴파일 작업 개체를 [`Get-AzAutomationDscCompilationJob`](/powershell/module/az.automation/get-azautomationdsccompilationjob)과 함께 사용하여
컴파일 작업 상태를 확인하고, [`Get-AzAutomationDscCompilationJobOutput`](/powershell/module/az.automation/get-azautomationdscconfiguration)을 사용하여
스트림(출력)을 볼 수 있습니다. 다음 샘플 코드는 **SampleConfig** 구성의 컴파일을 시작하고 완료될 때까지 대기한 다음 해당 스트림을 표시합니다.

```powershell
$CompilationJob = Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzAutomationDscCompilationJobOutput –Stream Any
```

###  <a name="basic-parameters"></a>기본 매개 변수

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

#### <a name="portal"></a>포털

포털에서 **컴파일**을 클릭한 후에 매개 변수 값을 입력할 수 있습니다.

![구성 컴파일 매개 변수](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

PowerShell은 키와 매개 변수 이름이 일치하고 매개 변수 값과 값이 같은 [hashtable](/powershell/module/microsoft.powershell.core/about/about_hash_tables) 에 매개 변수가 필요합니다.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

PSCredentials을 매개 변수로 전달하는 방법에 대한 정보는 아래의 [자격 증명 자산](#credential-assets)을 참조하세요.

### <a name="compiling-configurations-in-azure-automation-that-contain-composite-resources"></a>복합 리소스를 포함 하는 Azure Automation에서 구성 컴파일

**복합 리소스**를 사용하면 구성 내에서 중첩된 리소스로 DSC 구성을 사용할 수 있습니다. 이렇게 하면 단일 리소스에 여러 구성을 적용할 수 있습니다. **복합 리소스**에 대해 자세히 알아보려면 [복합 리소스: DSC 구성을 리소스로 사용](/powershell/scripting/dsc/resources/authoringresourcecomposite)을 참조하세요.

> [!NOTE]
> **복합 리소스** 를 포함 하는 구성이 올바르게 컴파일되도록 하려면 먼저 복합 리소스가 의존 하는 DSC 리소스를에서 Azure Automation로 먼저 가져와야 합니다.

DSC **복합 리소스** 추가는 Azure Automation에 PowerShell 모듈을 추가 하는 것과는 다릅니다.
이 프로세스에 대한 단계별 지침은 [Azure Automation의 모듈 관리](/azure/automation/shared-resources/modules)문서에 설명 되어 있습니다.

### <a name="managing-configurationdata-when-compiling-configuration-in-azure-automation"></a>Azure Automation에서 구성을 컴파일할 때 ConfigurationData 관리

**ConfigurationData** 를 사용하면 PowerShell DSC를 사용하는 동안 구조적 구성을 환경별 구성과 구분할 수 있습니다. [PowerShell DSC의 "위치"에서 "대상" 분리](https://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) 를 참조하여 **ConfigurationData**에 대해 자세히 알아봅니다.

> [!NOTE]
> Azure PowerShell를 사용 하 여 Azure Automation 상태 구성에서 컴파일할 때 **ConfigurationData** 를 사용할 수 있지만 Azure Portal는 사용할 수 없습니다.

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

Windows PowerShell을 사용 하 여 이전 DSC 구성을 컴파일할 수 있습니다. 다음 스크립트는 Azure Automation 상태 구성 끌어오기 서비스에 두 개의 노드 구성을 추가 합니다. **ConfigurationMyVM1** 및 **configurationMyVM3**:

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

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ConfigurationDataSample' -ConfigurationData $ConfigData
```

### <a name="working-with-assets-in-azure-automation-during-compilation"></a>컴파일하는 동안 Azure Automation의 자산 작업

자산 참조는 Azure Automation 상태 구성 및 Runbook에서 동일합니다. 자세한 내용은

- [인증서](automation-certificates.md)
- [연결](automation-connections.md)
- [자격 증명](automation-credentials.md)
- [변수](automation-variables.md)

#### <a name="credential-assets"></a>자격 증명 자산

Azure Automation에서 DSC 구성은 `Get-AutomationPSCredential` cmdlet를 사용하여 Automation 자격 증명 자산을 참조할 수 있습니다. 구성에 **PSCredential** 형식의 매개 변수가 있는 경우 자격 증명을 검색하는 cmdlet에 Azure Automation 자격 증명 자산의 문자열 이름을 전달하여 `Get-AutomationPSCredential` cmdlet을 사용할 수 있습니다. 그런 다음, 해당 개체를 **PSCredential** 개체가 필요한 매개 변수로 사용할 수 있습니다. 내부적으로 해당 이름을 가진 Azure Automation 자격 증명 자산은 검색되고 구성에 전달됩니다. 아래 예제에서는 작업 중인 이 항목을 보여줍니다.

자격 증명을 노드 구성(MOF 구성 문서)에서 안전하게 유지하려면 노드 구성 MOF 파일에 자격 증명을 암호화해야 합니다. 그러나 현재 PowerShell DSC가 노드 구성 MOF을 생성하는 동안 자격 증명을 일반 텍스트로 출력해도 되는지 알아야 합니다. PowerShell DSC은 Azure Automation이 컴파일 작업을 통해 생성된 후에 전체 MOF 파일을 암호화한다는 것을 모르기 때문입니다.

구성 데이터를 사용 하 여 생성 된 노드 구성 Mof에서 일반 텍스트로 자격 증명이 출력 되도록 PowerShell DSC에 지시할 수 있습니다. DSC 구성에 표시되고 자격 증명을 사용하는 각 노드 블록 이름의 경우 **ConfigurationData**를 통해 `PSDscAllowPlainTextPassword = $true`을 전달해야 합니다.

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

PowerShell로 이전 DSC 구성을 컴파일할 수 있습니다. 다음 PowerShell은 Azure Automation 상태 구성 끌어오기 서버에 **CredentialSample.MyVM1** 및 **CredentialSample.MyVM2**라는 두 개의 노드 구성을 추가합니다.

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

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'CredentialSample' -ConfigurationData $ConfigData
```

> [!NOTE]
> 컴파일이 완료되면 오류 메시지 **'Microsoft.PowerShell.Management' 스냅인을 이미 가져왔으므로 'Microsoft.PowerShell.Management' 모듈을 가져오지 못했습니다.** 가 표시될 수 있습니다. 이 경고는 무시해도 됩니다.

## <a name="compiling-configurations-in-windows-powershell-and-publishing-to-azure-automation"></a>Windows PowerShell에서 구성 컴파일 및 Azure Automation에 게시

Azure 외부에서 컴파일한 노드 구성(MOF)을 가져올 수도 있습니다.
여기에는 개발자 워크스테이션 또는 [Azure DevOps](https://dev.azure.com)와 같은 서비스에서 컴파일을 포함 합니다.
성능 및 안정성을 포함 하 여이 접근 방법에는 여러 가지 이점이 있습니다.
Windows PowerShell에서 컴파일하면 구성 콘텐츠를 서명 하는 옵션도 제공 됩니다.
서명된 노드 구성은 DSC 에이전트에 의해 관리되는 노드에서 로컬로 확인되어 인증된 출처에서 가져온 노드에 구성을 적용할 수 있습니다.

> [!NOTE]
> 노드 구성 파일은 Azure Automation으로 가져오기 위해 1MB보다 크지 않아야 합니다.

노드 구성에 서명하는 방법에 대한 자세한 내용은 [WMF 5.1의 향상된 기능 - 구성 및 모듈에 서명하는 방법](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations)을 참조하세요.

### <a name="compiling-a-configuration-in-windows-powershell"></a>Windows PowerShell에서 구성 컴파일

Windows PowerShell에서 DSC 구성을 컴파일하는 프로세스는 PowerShell DSC 설명서에서 [구성 작성, 컴파일 및 적용](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration)에 포함 되어 있습니다.
이는 개발자 워크스테이션 또는 [Azure DevOps](https://dev.azure.com)와 같은 빌드 서비스 내에서 실행할 수 있습니다.

그런 다음 구성을 컴파일하여 생성 된 MOF 파일을 Azure State 구성 서비스로 직접 가져올 수 있습니다.

### <a name="importing-a-node-configuration-in-the-azure-portal"></a>Azure Portal에서 노드 구성 가져오기

1. Automation 계정 페이지의 **구성 관리**에서 **상태 구성(DSC)** 을 클릭합니다.
1. **상태 구성(DSC)** 페이지에서 **구성** 탭을 클릭한 다음, **+ 추가**를 클릭합니다.
1. **가져오기** 페이지에서 **노드 구성 파일** 텍스트 상자 옆의 폴더 아이콘을 클릭하여 로컬 컴퓨터에서 노드 구성 파일(MOF)을 찾습니다.

   ![로컬 파일 찾기](./media/automation-dsc-compile/import-browse.png)

1. **구성 이름** 텍스트 상자에 이름을 입력합니다. 이 이름은 노드 구성이 컴파일된 구성 이름과 일치해야 합니다.
1. **확인**을 클릭합니다.

### <a name="importing-a-node-configuration-with-azure-powershell"></a>Azure PowerShell를 사용 하 여 노드 구성 가져오기

[AzAutomationDscNodeConfiguration](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) cmdlet을 사용 하 여 노드 구성을 automation 계정으로 가져올 수 있습니다.

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>다음 단계

- 시작하려면 [Azure Automation 상태 구성 시작하기](automation-dsc-getting-started.md)를 참조하세요.
- DSC 구성을 대상 노드에 할당할 수 있도록 DSC 구성을 컴파일하는 방법에 대해 알아보려면 [Azure Automation 상태 구성에서 구성 컴파일](automation-dsc-compile.md)을 참조하세요.
- PowerShell cmdlet 참조는 [Azure Automation 상태 구성 cmdlet](/powershell/module/az.automation)을 참조하세요.
- 가격 책정 정보는 [Azure Automation 상태 구성 가격 책정](https://azure.microsoft.com/pricing/details/automation/)을 참조하세요.
- 지속적인 배포 파이프라인에서 Azure Automation 상태 구성을 사용하는 예제는 [Azure Automation 상태 구성 및 Chocolatey를 사용한 지속적인 배포](automation-dsc-cd-chocolatey.md)를 참조하세요.
