---
title: Chocolatey를 사용한 Azure Automation State Configuration 연속 배포
description: Azure Automation State Configuration, DSC 및 Chocolatey 패키지 관리자를 사용한 DevOps 연속 배포  전체 JSON Resource Manager 템플릿 및 PowerShell 소스가 있는 예
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 08/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: b53cb65ec99637dadb16ed9d97c495571be956d7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61073915"
---
# <a name="usage-example-continuous-deployment-to-virtual-machines-using-automation-state-configuration-and-chocolatey"></a>사용 예제: Automation State Configuration 및 Chocolatey를 사용하여 Virtual Machines에 연속 배포

DevOps 업계에는 연속 통합 파이프라인의 여러 시점에서 개발자를 지원하는 여러 도구가 있습니다. Azure Automation State Configuration이 DevOps 팀이 채택할 수 있는 새로운 옵션으로 추가되었습니다. 이 문서에서는 Windows 컴퓨터에 대 한 CD(연속 배포)를 보여줍니다. 이 방법을 간편하게 확장하여 역할(예: 웹 사이트)에서 필요한 만큼의 Windows 컴퓨터를 포함하고 추가적인 역할도 구성할 수 있습니다.

![IaaS VM에 대한 연속 배포](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>높은 수준

이 단계는 상당 부분 진행 중이지만 다행스럽게도 두 가지 기본 프로세스로 나눌 수 있습니다.

- 즉 코드를 작성하여 테스트한 다음 시스템의 주 버전과 부 버전에 대해 설치 패키지를 만들고 게시하는 것입니다.
- 패키지에서 코드를 설치 및 실행할 VM을 만들고 관리합니다.  

이 핵심 프로세스가 둘 다 배치되어 있으면 새 버전을 만들어 배포할 때 특정 VM에서 실행되는 패키지를 자동으로 업데이트하는 단계는 간단합니다.

## <a name="component-overview"></a>구성 요소 개요

[apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) 같은 패키지 관리자는 Linux 분야에서 잘 알려져 있지만 Windows 분야에서는 그렇지 않습니다.
[Chocolatey](https://chocolatey.org/)도 마찬가지입니다. Scott Hanselman의 [블로그](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx)에서 이 항목에 대해 잘 소개하고 있습니다. 간단히 말해 Chocolatey를 사용하면 패키지의 중앙 리포지토리에 있는 패키지를 명령줄을 사용하여 Winodws 시스템에 설치할 수 있습니다. 자체 리포지토리를 만들어 관리할 수 있고 Chocolatey가 사용자가 지정한 수 만큼의 리포지토리에서 패키지를 설치할 수 있습니다.

DSC(필요한 상태 구성)([개요](/powershell/dsc/overview))는 컴퓨터에 원하는 구성을 선언할 수 있는 PowerShell 도구입니다. 예를 들어, "Chocolatey와 IIS를 설치하고 포트 80을 개방하며 웹 사이트 설치의 1.0.0 버전을 원합니다."라고 말할 수 있습니다. DSC LCM(로컬 구성 관리자)이 이러한 구성을 구현합니다. DSC 풀 서버가 컴퓨터에 대한 구성 리포지토리를 담고 있습니다. 각 컴퓨터의 LCM이 주기적인 검사를 통해 자신의 구성이 저장된 구성과 일치하는지 확인합니다. 상태를 보고하거나, 컴퓨터를 저장된 구성과 일치하는 상태로 되돌리는 작업을 수행할 수 있습니다. 풀 서버에서 저장된 구성을 편집하여 컴퓨터나 컴퓨터 집합이 변경된 구성과 일치하게 만들 수 있습니다.

Azure Automation은 사용자가 일정 및 전역 변수와 같은 Runbook, 노드, 자산을 사용하여 다양한 작업을 자동화할 수 있도록 하는 Microsoft Azure의 관리되는 서비스입니다.
Azure Automation State Configuration은 PowerShell DSC 도구를 포함하도록 자동화 기능을 확장합니다. 여기서 알맞은 [개요](automation-dsc-overview.md)를 제공합니다.

DSC 리소스는 네트워크 관리, Active Directory 또는 SQL Server 등과 같은 특정 기능을 갖는 코드 모듈입니다. Chocolatey DSC 리소스는 NuGet 서버 액세스(서로 간), 패키지 다운로드, 패키지 설치 방법 등을 이해하고 있습니다. 여러 다른 DSC 리소스는 [PowerShell 갤러리](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title)에 있습니다.
이러한 모듈은 사용자가 구성에서 사용할 수 있게 Azure Automation State Configuration 풀 서버에 설치됩니다.

리소스 관리자 템플릿은 네트워크, 서브넷, 네트워크 보안, 라우팅, 부하 분산 장치, NIC, VM 등의 인프라를 생성하는 선언적인 방법을 제공합니다. 다음 [문서](../azure-resource-manager/resource-manager-deployment-model.md)에서는 Resource Manager 배포 모델(선언적)을 Azure 서비스 관리(ASM 또는 클래식) 배포 모델(명령적)을 비교하고 핵심 리소스 공급자, 계산, 스토리지 및 네트워크에 대해 설명합니다.

Resource Manager 템플릿의 핵심 기능은 프로비전되었을 때 VM에 VM 확장을 설치하는 것입니다. VM 확장에는 사용자 지정 스크립트 실행, 바이러스 백신 소프트웨어 설치, DSC 구성 스크립트 실행 등과 같은 특정 기능이 있습니다. VM 확장에는 여러 다른 형식이 있습니다.

## <a name="quick-trip-around-the-diagram"></a>간략히 다이어그램 둘러보기

맨 위쪽부터 코드를 작성하고 빌드 및 테스트한 다음 설치 패키지를 만듭니다.
Chocolatey는 MSI, MSU, ZIP 등과 같은 다양한 형태의 설치 패키지를 처리할 수 있습니다. 또한 Chocolatey의 기본 기능이 부족할 경우 PowerShell을 완전히 활용하여 실제 설치를 수행할 수 있습니다. 패키지를 위치 연결할 – 패키지 리포지토리에 넣습니다. 이 사용 예는 Auzre Blob Storage 계정의 공용 폴더를 사용하지만 어디에나 있을 수 있습니다. Chocolatey는 패키지 메타데이터의 관리를 위해 NuGet 서버 및 기타 제품과 기본적으로 연동됩니다. [이 문서](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) 에서는 이러한 옵션에 대해 설명합니다. 이 사용 예는 NuGet을 사용합니다. Nuspec은 패키지에 관한 메타데이터입니다. Nuspec은 NuPkg에 "컴파일"되어 NuGet 서버에 저장됩니다. 구성에서 이름으로 패키지를 요청하고 NuGet 서버를 참조할 경우 Chocolatey DSC 리소스(이제 VM에 있음)가 패키지를 포착하여 설치합니다. 특정 버전의 패키지를 요청할 수도 있습니다.

그림의 왼쪽 하단에는 Azure Resource Manager 템플릿이 있습니다. 이 사용 예에서는 VM 확장이 VM을 Azure Automation State Configuration 풀 서버(즉, 풀 서버)에 노드로 등록합니다. 구성은 풀 서버에 저장됩니다.
사실 일반 텍스트로 1번, MOF 파일로 컴파일 1번 등(이에 대해 알고 있는 상황), 두 번 저장됩니다. 포털에서 MOF는 "노드 구성"입니다(단순 "구성"과 반대). 노드와 관련한 아티팩트이므로 노드가 그 구성을 알고 있습니다. 아래의 세부 정보는 노드 구성을 노드에 할당하는 방법을 보여줍니다.

아마도 이미 윗 부분 또는 대부분을 수행하고 있을 것입니다. Nuspec를 생성, 컴파일 및 NuGet 서버에 저장하는 작업은 간단합니다. 이미 VM을 관리하고 있습니다. 연속 배포의 다음 단계로 이동하려면 풀 서버를 설정하고(1회), 노드를 풀 서버에 등록하고(1회), 여기에 구성을 만들어 저장해야 합니다(최초). 그러면 패키지가 업그레이드되어 리포지토리에 배포되며 풀 서버에서 구성 및 노드 구성을 새로 고칩니다.(필요에 따라 반복)

Resource Manager 템플릿으로 시작하지 않아도 괜찮습니다. VM을 풀 서버 및 나머지 모두에 등록하는 데 도움이 되도록 설계된PowerShell cmdlet이 있습니다. 자세한 내용은 문서: [Azure Automation 상태 구성을 통한 관리를 위한 머신 온보드](automation-dsc-onboarding.md)를 참조하세요.

## <a name="step-1-setting-up-the-pull-server-and-automation-account"></a>1단계: 풀 서버 및 자동화 계정 설정

인증된(`Connect-AzureRmAccount`) PowerShell 명령줄에서(풀 서버를 설정하는 데 몇 분 정도 소요될 수 있음):

```azurepowershell-interactive
New-AzureRmResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzureRmAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

Automation 계정을 미국 동부 2, 미국 중남부, 미국 버지니아 주 정부, 유럽 서부, 동남 아시아, 일본 동부, 인도 중부 및 오스트레일리아 남동부, 캐나다 중부, 유럽 북부 등의 지역(즉, 위치) 중 하나에 놓을 수 있습니다.

## <a name="step-2-vm-extension-tweaks-to-the-resource-manager-template"></a>2단계: Resource Manager 템플릿에 대한 VM 확장 조정

VM 등록에 대한 세부 정보(PowerShell DSC VM 확장 사용)는 이 [Azure 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver)에서 제공합니다.
이 단계는 새 VM을 상태 구성 노드 목록의 풀 서버에 등록합니다. 이 등록의 일부는 노드에 적용할 노드 구성을 지정하고 있습니다. 이 노드 구성은 끌어오기 서버에 있을 필요가 없으므로 4단계에서 이 작업을 처음으로 수행해도 됩니다. 하지만 여기 2단계에서 노드의 이름 및 구성의 이름을 결정해야 합니다. 이 사용 예에서 노드는 'isvbox'이고 구성은 'ISVBoxConfig'입니다. 따라서 (DeploymentTemplate.json 지정할) 노드 구성 이름은 'ISVBoxConfig.isvbox'입니다.

## <a name="step-3-adding-required-dsc-resources-to-the-pull-server"></a>3단계: 필요한 DSC 리소스를 풀 서버에 추가

Azure Automation 계정에 DSC 리소스를 설치하기 위해 PowerShell 갤러리가 계측됩니다.
원하는 리소스로 이동한 다음 "Azure Automation에 배포" 단추를 클릭합니다.

![PowerShell 갤러리 예](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Azure Portal에 최근에 추가된 또 다른 방법을 사용하면 새 모듈을 당겨오거나 기존 모듈을 업데이트할 수 있습니다. Automation 계정 리소스, 자산 타일 및 모듈 타일을 차례로 클릭합니다. 갤러리 찾아보기 아이콘을 사용하면 갤러리에서 모듈의 목록을 보고 세부 정보로 드릴다운하고 궁극적으로 Automation 계정으로 가져올 수 있습니다. 이는 모듈을 최신 상태로 유지할 수 있는 좋은 방법입니다. 그리고 가져오기 기능은 다른 모듈의 종속성을 확인하여 동기화에서 빠져 나가지 않도록 합니다.

또는 수동 방법이 있습니다. Windows 컴퓨터용 PowerShell 통합 모듈의 폴더 구조는 Azure Automation에서의 예상 폴더 구조와 다소 차이가 있습니다.
여기에는 약간의 사용자 조정 작업이 필요합니다. 어려운 작업도 아니고 리소스당 한 번만 수행합니다(향후 업그레이드하지 않으려는 경우). PowerShell 통합 모듈 제작에 대한 자세한 내용은 [Azure Automation에 대한 통합 모듈 제작](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/) 문서를 참조하세요.

- 다음과 같이 사용자 워크스테이션에 필요한 모듈을 설치합니다.
  - [Windows Management Framework, v5](https://aka.ms/wmf5latest) 설치(Windows 10에는 필요 없음)
  - `Install-Module –Name MODULE-NAME` <- PowerShell 갤러리에서 모듈 가져오기
- `c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME` 의 모듈 폴더를 임시 폴더에 복사
- 주 폴더에서 샘플 및 설명서 삭제
- 주 폴더를 압축하고 ZIP 파일 이름을 폴더와 정확히 같게 지정 
- ZIP 파일을 Azure Storage 계정의 Blob 스토리지와 같은 연결할 수 있는 HTTP 위치에 배치합니다.
- 이 PowerShell 실행:

  ```powershell
  New-AzureRmAutomationModule `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Name MODULE-NAME –ContentLink 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
  ```

포함된 예는 cChoco 및xNetworking에 대해 이 절차를 수행합니다. cChoco에 대한 특수 처리는 [참고 사항](#notes) 을 참조하세요.

## <a name="step-4-adding-the-node-configuration-to-the-pull-server"></a>4단계: 노드 구성을 풀 서버에 추가

구성을 풀 서버로 처음 가져와 컴파일하는 데는 별다른 사항이 없습니다. 동일한 구성에 대한 차후의 모든 가져오기/컴파일은 정확히 같습니다. 패키지를 업데이트하고 프러덕션에 푸시해야 할 때마다 구성 파일이 정확한지 확인한 후(새 패키지 버전 포함) 이 단계를 수행합니다. 구성 파일과 PowerShell을 다음과 같습니다.

ISVBoxConfig.ps1:

```powershell
Configuration ISVBoxConfig
{
    Import-DscResource -ModuleName cChoco
    Import-DscResource -ModuleName xNetworking

    Node 'isvbox' {

        cChocoInstaller installChoco
        {
            InstallDir = 'C:\choco'
        }

        WindowsFeature installIIS
        {
            Ensure = 'Present'
            Name   = 'Web-Server'
        }

        xFirewall WebFirewallRule
        {
            Direction    = 'Inbound'
            Name         = 'Web-Server-TCP-In'
            DisplayName  = 'Web Server (TCP-In)'
            Description  = 'IIS allow incoming web site traffic.'
            Enabled       = 'True'
            Action       = 'Allow'
            Protocol     = 'TCP'
            LocalPort    = '80'
            Ensure       = 'Present'
        }

        cChocoPackageInstaller trivialWeb
        {
            Name      = 'trivialweb'
            Version   = '1.0.0'
            Source    = 'MY-NUGET-V2-SERVER-ADDRESS'
            DependsOn = '[cChocoInstaller]installChoco','[WindowsFeature]installIIS'
        }
    }
}
```

New-ConfigurationScript.ps1:

```powershell
Import-AzureRmAutomationDscConfiguration `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 `
    -Published –Force

$jobData = Start-AzureRmAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -ConfigurationName ISVBoxConfig

$compilationJobId = $jobData.Id

Get-AzureRmAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Id $compilationJobId
```

이 단계에서는 끌어오기 서버에 위치하며 이름이 "ISVBoxConfig.isvbox"인 새 노드 구성이 생깁니다. 노드 구성 이름이 "configurationName.nodeName"으로 빌드됩니다.

## <a name="step-5-creating-and-maintaining-package-metadata"></a>5단계: 패키지 메타데이터 만들기 및 유지 관리

패키지 리포지토리에 넣는 각 패키지에 대해 이를 설명하는 nuspec이 필요합니다.
해당 nuspec은 NuGet 서버에서 컴파일 및 저장해야 합니다. 이 프로세스는 [여기](https://docs.nuget.org/create/creating-and-publishing-a-package)에서 설명합니다. MyGet.org를 NuGet 서버로 사용할 수 있습니다. 이 서비스를 판매하고 있지만 스타터 SKU는 무료입니다. NuGet.org에서 개인 패키지에 대 한 자체 NuGet 서버 설치 관련 지침을 찾을 수 있습니다.

## <a name="step-6-tying-it-all-together"></a>6단계: 모든 항목 요약

각 버전이 QA를 전달하고 배포를 승인할 때마다 패키지를 만들고 nuspec 및 nupkg를 NuGet 서버에 업데이트하며 배포합니다. 또한 구성(위의 4단계)은 새 버전 번호와 일치하도록 업데이트되어야 합니다. 끌어오기 서버로 전송되고 컴파일되어야 합니다.
해당 지점부터 업데이트를 끌어오고 설치하는 작업은 해당 구성에 종속되는 VM의 역할입니다. 이러한 각각의 업데이트는 하나 또는 두 줄의 PowerShell로 간단합니다. Azure DevOps의 경우 일부는 빌드에서 서로 연결될 수 있는 빌드 작업에 캡슐화됩니다. 이 [문서](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery)에 자세한 내용이 나와 있습니다. 이 [GitHub 리포지토리](https://github.com/Microsoft/vso-agent-tasks) 는 사용 가능한 다양한 빌드 작업을 자세히 설명합니다.

## <a name="notes"></a>메모

이 사용 예에서는 Azure 갤러리의 일반 Windows Server 2012 R2 이미지에서 VM으로 시작합니다. 아무 저장된 이미지에서나 시작하고 DSC 구성을 통해 조정을 진행할 수 있습니다.
그러나 이미지에 반영되는 구성을 변경하는 작업은 DSC를 사용하여 구성을 동적으로 업데이트하는 작업보다 훨씬 어렵습니다.

이 방법을 VM에 사용하기 위해 Resource Manager 템플릿이나 VM 확장을 사용할 필요는 없습니다. 또한 VM이 CD 관리를 위해 Azure에 있을 필요도 없습니다. 오직 Chocolatey을 설치하고 풀 서버 위치를 알 수 있게 VM에 LCM을 구성하기만 하면 됩니다.

물론, 프러덕션 상태인 VM에서 패키지를 업데이트할 때는 업데이트가 설치되는 동안 VM을 로테이션에서 배제해야 합니다. 이를 수행하는 방법은 크게 다릅니다. 예를 들어, Azure 부하 분산 장치 뒤에 있는 VM에서는 사용자 지정 프로브를 추가할 수 있습니다. VM을 업데이트하는 동안 프로브 엔드포인트가 400을 반환하게 합니다. 이 변경을 위해 필요한 조정은 구성 안에 있을 수 있습니다. 업데이트가 완료되면 조정에서 200을 반환하도록 복귀되기 때문입니다.

이 사용 예의 전체 원본은 GitHub의 [이 Visual Studio 프로젝트](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) 에 있습니다.

## <a name="related-articles"></a>관련 문서
* [Azure Automation DSC 개요](automation-dsc-overview.md)
* [Azure Automation DSC cmdlets](https://docs.microsoft.com/powershell/module/azurerm.automation#automation)
* [Azure Automation DSC를 통한 관리를 위한 컴퓨터 온보드](automation-dsc-onboarding.md)

## <a name="next-steps"></a>다음 단계

- 개요는 [Azure Automation 상태 구성](automation-dsc-overview.md)을 참조하세요.
- 시작하려면 [Azure Automation 상태 구성 시작하기](automation-dsc-getting-started.md)를 참조하세요.
- DSC 구성을 대상 노드에 할당할 수 있도록 DSC 구성을 컴파일하는 방법에 대해 알아보려면 [Azure Automation 상태 구성에서 구성 컴파일](automation-dsc-compile.md)을 참조하세요.
- PowerShell cmdlet 참조는 [Azure Automation 상태 구성 cmdlet](/powershell/module/azurerm.automation/#automation)을 참조하세요.
- 가격 책정 정보는 [Azure Automation 상태 구성 가격 책정](https://azure.microsoft.com/pricing/details/automation/)을 참조하세요.
- 지속적인 배포 파이프라인에서 Azure Automation 상태 구성을 사용하는 예제는 [Azure Automation 상태 구성 및 Chocolatey를 사용한 지속적인 배포](automation-dsc-cd-chocolatey.md)를 참조하세요.
