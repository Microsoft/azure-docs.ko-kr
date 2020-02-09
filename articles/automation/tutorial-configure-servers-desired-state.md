---
title: 원하는 상태로 서버 구성 및 Azure Automation을 사용하여 드리프트 관리
description: 자습서 - Azure Automation 상태 구성으로 서버 구성 관리
services: automation
ms.subservice: dsc
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: 9e2f04f59a56be6c516eb90de45fdf7327673086
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75416581"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>원하는 상태로 서버 구성 및 드리프트 관리

Azure Automation 상태 구성을 사용하면 서버 구성을 지정하고 시간이 지남에 따라 해당 서버가 지정된 상태에 있는지 확인할 수 있습니다.

> [!div class="checklist"]
> - Azure Automation DSC에서 관리되는 VM 등록
> - Azure Automation에 구성 업로드
> - 노드 구성으로 구성 컴파일
> - 관리되는 노드에 노드 구성 할당
> - 관리되는 노드에 대한 준수 상태 확인

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음 항목이 필요합니다.

- Azure Automation 계정. Azure Automation 실행 계정 만들기에 대한 지침은 [Azure 실행 계정](automation-sec-configure-azure-runas-account.md)을 참조하세요.
- Windows Server 2008 R2 이상을 실행하는 Azure Resource Manager VM(클래식 아님). VM 만들기에 대한 지침은 [Azure 포털에서 첫 번째 Windows 가상 머신 만들기](../virtual-machines/virtual-machines-windows-hero-tutorial.md)
- Azure PowerShell 모듈 버전 3.6 이상 - `Get-Module -ListAvailable AzureRM`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/azurerm/install-azurerm-ps)를 참조하세요.
- DSC(필요한 상태 구성)와 익숙함. DSC에 대한 자세한 내용은 [Windows PowerShell 필요한 상태 구성 개요](/powershell/scripting/dsc/overview/overview)를 참조하세요.

## <a name="log-in-to-azure"></a>Azure에 로그인

`Connect-AzureRmAccount` 명령으로 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

```powershell
Connect-AzureRmAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Azure Automation에 구성 만들기 및 업로드

이 자습서에서는 IIS가 VM에 설치되도록 하는 간단한 DSC 구성을 사용합니다.

DSC 구성에 대한 자세한 내용은 [DSC 구성](/powershell/scripting/dsc/configurations/configurations)을 참조하세요.

텍스트 편집기에서 다음을 입력하고 로컬에서 `TestConfig.ps1`로 저장합니다.

```powershell
configuration TestConfig {
   Node WebServer {
      WindowsFeature IIS {
         Ensure               = 'Present'
         Name                 = 'Web-Server'
         IncludeAllSubFeature = $true
      }
   }
}
```

> [!NOTE]
> DSC 리소스를 제공 하는 여러 모듈을 가져와야 하는 고급 시나리오에서는 각 모듈이 구성에서 고유한 `Import-DscResource` 줄을 사용 하도록 해야 합니다.

`Import-AzureRmAutomationDscConfiguration` cmdlet을 호출하여 구성을 Automation 계정에 업로드합니다.

```powershell
 Import-AzureRmAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>노드 구성으로 구성 컴파일

DSC 구성을 노드에 할당하려면 먼저 노드 구성으로 컴파일해야 합니다.

구성 컴파일에 대한 자세한 내용은 [DSC 구성](/powershell/scripting/dsc/configurations/configurations)을 참조하세요.

`Start-AzureRmAutomationDscCompilationJob` cmdlet을 호출하여 `TestConfig` 구성을 노드 구성으로 컴파일합니다.

```powershell
Start-AzureRmAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

그러면 Automation 계정에 `TestConfig.WebServer`라는 노드 구성이 만들어집니다.

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>상태 구성에서 관리하려면 VM 등록

Azure Automation 상태 구성을 사용하여 Azure VM(클래식 및 Resource Manager), 온-프레미스 VM, Linux 머신, AWS VM 및 온-프레미스 물리적 머신을 관리할 수 있습니다. 이 항목에서는 Azure Resource Manager VM만 등록하는 방법에 대해 설명합니다. 다른 유형의 머신 등록에 대한 정보는 [Azure Automation 상태 구성을 통한 관리용 머신 온보드](automation-dsc-onboarding.md)를 참조하세요.

`Register-AzureRmAutomationDscNode` cmdlet을 호출하여 Azure Automation 상태 구성에 VM을 등록합니다.

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

이렇게 하면 지정된 VM을 상태 구성의 관리되는 노드로 등록할 수 있습니다.

### <a name="specify-configuration-mode-settings"></a>구성 모드 설정 지정

VM을 관리되는 노드로 등록할 때 구성의 속성도 지정할 수 있습니다. 예를 들어 **ConfigurationMode** 속성의 값으로 `ApplyOnly`를 지정하여 컴퓨터의 상태를 한 번만 적용하도록 지정할 수 있습니다(즉 DSC에서 초기 검사 후 구성을 적용하지 않음).

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

또한 **ConfigurationModeFrequencyMins** 속성을 사용하여 DSC에서 구성 상태를 확인하는 빈도를 지정할 수 있습니다.

```powershell
# Run a DSC check every 60 minutes
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

관리되는 노드에 대한 구성 속성 설정에 대한 자세한 내용은 [Register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode)를 참조하세요.

DSC 구성 설정에 대한 자세한 내용은 [로컬 구성 관리자 구성](/powershell/scripting/dsc/managing-nodes/metaConfig)을 참조하세요.

## <a name="assign-a-node-configuration-to-a-managed-node"></a>관리되는 노드에 노드 구성 할당

이제 구성하려는 VM에 컴파일된 노드 구성을 할당할 수 있습니다.

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

그러면 등록된 DSC 노드(`DscVm`)에 노드 구성(`TestConfig.WebServer`)이 할당됩니다.
기본적으로 DSC 노드에서는 30분마다 노드 구성이 준수되는지 확인합니다.
준수 확인 간격을 변경하는 방법에 대한 자세한 내용은 [로컬 구성 관리자 구성](/powershell/scripting/dsc/managing-nodes/metaConfig)을 참조하세요.

## <a name="working-with-partial-configurations"></a>부분 구성 작업

Azure Automation 상태 구성은 [부분 구성](/powershell/scripting/dsc/pull-server/partialconfigs)의 사용을 지원 합니다.
이 시나리오에서 DSC는 여러 구성을 독립적으로 관리 하도록 구성 되 고 각 구성은 Azure Automation에서 검색 됩니다.
그러나 automation 계정 마다 하나의 구성만 노드에 할당할 수 있습니다.
즉, 노드에 대한 두 가지 구성을 사용 하는 경우 두 개의 automation 계정이 필요 합니다.

끌어오기 서비스에서 부분 구성을 등록 하는 방법에 대한 자세한 내용은 [부분 구성](https://docs.microsoft.com/powershell/scripting/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode)설명서를 참조 하십시오.

팀에서 구성을 사용 하 여 공동으로 서버를 공동 관리 하는 방법에 대한 자세한 내용은 [CI/CD 파이프라인에서 DSC의 역할 이해](/powershell/scripting/dsc/overview/authoringadvanced)를 참조 하세요.

## <a name="check-the-compliance-status-of-a-managed-node"></a>관리되는 노드에 대한 준수 상태 확인

`Get-AzureRmAutomationDscNodeReport` cmdlet을 호출하여 관리되는 노드의 준수 상태에 대한 보고서를 얻을 수 있습니다.

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzureRmAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="removing-nodes-from-service"></a>서비스에서 노드 제거

Azure Automation 상태 구성에 노드를 추가 하는 경우 로컬 Configuration Manager의 설정은 서비스에 등록 하 고, 끌어오기 구성 및 필요한 모듈을 구성 하 여 컴퓨터를 구성 합니다.
서비스에서 노드를 제거 하도록 선택 하는 경우 Azure Portal 또는 Az cmdlet 중 하나를 사용 하 여이 작업을 수행할 수 있습니다.

> [!NOTE]
> 서비스에서 노드의 등록을 취소 하면 노드가 서비스에 더 이상 연결 되지 않도록 로컬 Configuration Manager 설정만 설정 합니다.
> 현재 노드에 적용 되는 구성에는 영향을 주지 않습니다.
> 현재 구성을 제거 하려면 [PowerShell](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument?view=powershell-5.1) 을 사용 하거나 로컬 구성 파일을 삭제 합니다 (Linux 노드의 유일한 옵션).

### <a name="azure-portal"></a>Azure Portal

Azure Automation에서 목차의 **상태 구성 (DSC)** 을 클릭 합니다.
그런 다음 **노드** 를 클릭 하 여 서비스에 등록 된 노드 목록을 표시 합니다.
제거 하려는 노드의 이름을 클릭 합니다.
열리는 노드 보기에서 **등록 취소**를 클릭 합니다.

### <a name="powershell"></a>PowerShell

PowerShell을 사용 하 여 Azure Automation 상태 구성 서비스에서 노드를 등록 취소 하려면 cmdlet [AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-2.0.0)에 대한 설명서를 따르세요.

## <a name="next-steps"></a>다음 단계

- 시작하려면 [Azure Automation 상태 구성 시작하기](automation-dsc-getting-started.md)를 참조하세요.
- 노드를 온보드하는 방법에 대한 자세한 내용은 [Azure Automation 상태 구성을 통해 머신을 관리하기 위한 머신 온보딩](automation-dsc-onboarding.md)을 참조하세요.
- DSC 구성을 대상 노드에 할당할 수 있도록 DSC 구성을 컴파일하는 방법에 대해 알아보려면 [Azure Automation 상태 구성에서 구성 컴파일](automation-dsc-compile.md)을 참조하세요.
- PowerShell cmdlet 참조는 [Azure Automation 상태 구성 cmdlet](/powershell/module/azurerm.automation/#automation)을 참조하세요.
- 가격 책정 정보는 [Azure Automation 상태 구성 가격 책정](https://azure.microsoft.com/pricing/details/automation/)을 참조하세요.
- 지속적인 배포 파이프라인에서 Azure Automation 상태 구성을 사용하는 예제는 [Azure Automation 상태 구성 및 Chocolatey를 사용한 지속적인 배포](automation-dsc-cd-chocolatey.md)를 참조하세요.
