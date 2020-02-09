---
title: Azure Automation 상태 구성을 통한 관리를 위한 머신 온보드
description: Azure Automation 상태 구성을 사용 하 여 관리할 컴퓨터를 설정 하는 방법
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: 89e86a6702be7314b99975cac90818252eb07df7
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/06/2020
ms.locfileid: "77046225"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>Azure Automation 상태 구성을 통한 관리를 위한 머신 온보드

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>Azure Automation 상태 구성으로 머신을 관리하는 이유는?

Azure Automation 상태 구성은 모든 클라우드 또는 온-프레미스 데이터 센터에서 DSC (Desired State Configuration) 노드에 대한 구성 관리 서비스입니다.
안전한 중앙 위치에서 수천 대의 컴퓨터를 빠르고 간편하게 확장할 수 있습니다.
컴퓨터를 쉽게 온보드하고, 컴퓨터에 선언적 구성을 할당하고, 사용자가 지정한 필요 상태에 대한 각 컴퓨터의 규정 준수를 나타내는 보고서를 확인할 수 있습니다.
Azure Automation 상태 구성 서비스는 PowerShell 스크립트에 Azure Automation runbook이 무엇 인지를 DSC에 제공 합니다.
즉, Azure Automation에서 PowerShell 스크립트 관리를 지원하는 동일한 방법으로 DSC 구성 관리를 지원합니다.
Azure Automation 상태 구성을 사용하는 이점에 대해 자세히 알려면 [Azure Automation 상태 구성 개요](automation-dsc-overview.md)를 참조하세요.

Azure Automation 상태 구성을 다양한 머신의 관리에 사용할 수 있습니다.

- Azure 가상 컴퓨터
- Azure 가상 머신(기본)
- 온-프레미스에 있는 실제/가상 Windows 컴퓨터 (AWS EC2 인스턴스 포함) 또는 Azure 이외의 클라우드
- 온-프레미스, Azure 또는 Azure 이외의 클라우드에 있는 실제/가상 Linux 컴퓨터

또한 클라우드에서 머신 구성을 관리할 수 없는 경우 Azure Automation 상태 구성은 보고서 전용 엔드포인트로 사용될 수 있습니다.
이렇게 하면 DSC를 통해 구성 (푸시)을 설정 하 고 Azure Automation 보고 세부 정보를 볼 수 있습니다.

> [!NOTE]
> 상태 구성을 사용한 Azure VM 관리는 설치된 가상 머신 DSC 확장이 2.70보다 큰 경우 추가 비용 없이 포함됩니다. 자세한 내용은 [**Automation 가격 책정 페이지**](https://azure.microsoft.com/pricing/details/automation/)를 참조 하세요.

다음 섹션에서는 Azure Automation 상태 구성에 대해 각 머신 형식을 온보드하는 방법을 간략히 설명합니다.

> [!NOTE]
>Linux 노드에 DSC를 배포 하면 `/tmp` 폴더 및 **Nxautomation** 과 같은 모듈이 사용 됩니다 .이를 적절 한 위치에 설치 하기 전에 확인을 위해 임시로 다운로드 됩니다. 모듈이 제대로 설치 되도록 하려면 Linux 용 Log Analytics 에이전트에 `/tmp` 폴더에 대한 읽기/쓰기 권한이 있어야 합니다. Linux 용 Log Analytics 에이전트는 `omsagent` 사용자로 실행 됩니다. 
>
>`omsagent` 사용자에 게 쓰기 권한을 부여 하려면 다음 명령을 실행 합니다. `setfacl -m u:omsagent:rwx /tmp`
>

## <a name="azure-virtual-machines"></a>Azure 가상 컴퓨터

Azure Automation 상태 구성을 사용하면 Azure Portal, Azure Resource Manager 템플릿 또는 PowerShell을 사용하는 구성 관리를 위해 Azure 가상 머신을 간편하게 온보드할 수 있습니다. 내부적으로, VM에 대해 관리자가 원격으로 작업할 필요 없이 Azure VM 필요 상태 구성 확장은 VM을 Azure Automation 상태 구성에 등록합니다.
Azure VM DSC(Desired State Configuration) 확장은 비동기적으로 실행되므로 진행 상황을 추적하거나 문제를 해결하는 단계는 뒤에 나오는 [**Azure 가상 머신 온보드 문제 해결**](#troubleshooting-azure-virtual-machine-onboarding) 섹션에서 제공됩니다.

### <a name="azure-portal"></a>Azure 포털

[Azure Portal](https://portal.azure.com/)에서 가상 머신을 온보드할 Azure Automation 계정으로 이동합니다. 상태 구성 페이지 및 **노드** 탭에서 **+ 추가**를 클릭합니다.

등록하려면 Azure 가상 머신을 선택합니다.

컴퓨터에 필요한 상태 확장이 설치된 PowerShell이 없고 전원 상태가 실행 중인 경우 **연결**을 클릭합니다.

**등록**에서 사용 사례에 필요한 [PowerShell DSC 로컬 Configuration Manager 값](/powershell/scripting/dsc/managing-nodes/metaConfig)과 선택적으로 VM에 할당할 노드 구성을 입력합니다.

![온보딩](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿

Azure 가상 머신은 Azure Resource Manager 템플릿을 통해 Azure Automation 상태 구성에 배포 및 온보드할 수 있습니다. 기존 VM을 Azure Automation 상태 구성으로 전환 하는 예제 템플릿은 [필요한 상태에서 관리 되는 서버 구성 서비스](https://azure.microsoft.com/resources/templates/101-automation-configuration/) 를 참조 하세요.
가상 머신 확장 집합을 관리 하는 경우 [Azure Automation에서 관리 하는 템플릿 가상 머신 확장 집합 구성](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/)예제를 참조 하세요.

### <a name="powershell"></a>PowerShell

AzAutomationDscNode cmdlet은 PowerShell을 사용 하 여 Azure에서 가상 컴퓨터를 [등록](/powershell/module/az.automation/register-azautomationdscnode) 하는 데 사용할 수 있습니다.
그러나이는 현재 Windows를 실행 하는 컴퓨터에 대해서만 구현 됩니다 .이 cmdlet은 Windows 확장만 트리거합니다.

### <a name="registering-virtual-machines-across-azure-subscriptions"></a>Azure 구독을 통해 가상 머신 등록

다른 Azure 구독에서 가상 컴퓨터를 등록 하는 가장 좋은 방법은 Azure Resource Manager 배포 템플릿에서 DSC 확장을 사용 하는 것입니다.
예는 [Azure Resource Manager 템플릿을 사용 하 여 필요한 상태 구성 확장](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template)에서 제공 됩니다.
템플릿에서 매개 변수로 사용할 등록 키와 등록 URL을 찾으려면 다음 [**보안 등록**](#secure-registration) 섹션을 참조 하세요.

## <a name="physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances"></a>온-프레미스에 있는 실제/가상 Windows 컴퓨터 (AWS EC2 인스턴스 포함) 또는 Azure 이외의 클라우드

온-프레미스 또는 다른 클라우드 환경에서 실행 되는 Windows 서버는 [Azure에 대한 아웃 바운드 액세스](automation-dsc-overview.md#network-planning)권한이 있는 한 Azure Automation 상태 구성으로 등록 될 수도 있습니다.

1. 최신 버전의 [WMF 5](https://aka.ms/wmf5latest)가 Azure Automation 상태 구성에 온보드하려는 머신에 설치되었는지 확인합니다.
1. 뒤에 나오는 [**DSC 메타 구성 생성**](#generating-dsc-metaconfigurations) 섹션의 지침에 따라 필요한 DSC 메타 구성이 포함된 폴더를 생성합니다.
1. 등록할 컴퓨터에 PowerShell DSC 메타 구성을 원격으로 적용합니다. **이 명령이 실행되는 컴퓨터에는 최신 버전의 [WMF 5](https://aka.ms/wmf5latest) 가 설치되어 있어야 합니다.**

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. PowerShell DSC 메타 구성을 원격으로 적용할 수 없는 경우 2단계의 메타 구성 폴더를 등록할 각 컴퓨터에 복사합니다. 그런 다음 등록할 각 컴퓨터에서 **Set-DscLocalConfigurationManager** 를 로컬로 호출합니다.
1. Azure Portal 또는 cmdlet을 사용 하 여 등록할 컴퓨터가 Azure Automation 계정에 등록 된 상태 구성 노드로 나타나는지 확인 합니다.

## <a name="physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>온-프레미스 또는 Azure 이외의 클라우드에서 온-프레미스의 물리적/가상 Linux 컴퓨터

온-프레미스 또는 다른 클라우드 환경에서 실행 되는 Linux 서버는 [Azure에 대한 아웃 바운드 액세스](automation-dsc-overview.md#network-planning)권한이 있는 한 Azure Automation 상태 구성으로 등록 될 수도 있습니다.

1. 최신 버전의 [Linux용 PowerShell 필요한 상태 구성](https://github.com/Microsoft/PowerShell-DSC-for-Linux)이 Azure Automation 상태 구성에 온보드하려는 머신에 설치되어 있는지 확인합니다.
2. [PowerShell DSC 로컬 구성 관리자 기본값](/powershell/scripting/dsc/managing-nodes/metaConfig4)이 사용자 사용 사례와 일치하는 경우 Azure Automation 상태 구성에서 끌어오고 보고하는 **모든** 머신을 온보드하려 합니다.

   - Azure Automation 상태 구성에 온보드할 각 Linux 머신에서 `Register.py`를 사용하여 PowerShell DSC 로컬 구성 관리자 기본값을 통해 온보드합니다.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Automation 계정에 대한 등록 키와 등록 URL을 찾으려면 뒤에 나오는 [**등록 보호**](#secure-registration) 섹션을 참조하세요.

     PowerShell DSC 로컬 Configuration Manager 기본값이 사용 사례와 일치 **하지** 않거나 Azure Automation 상태 구성에만 보고 하도록 컴퓨터를 등록 하려는 경우에는 3-6 단계를 수행 합니다. 그렇지 않으면 6단계로 직접 이동합니다.

3. 뒤에 나오는 [**DSC 메타 구성 생성**](#generating-dsc-metaconfigurations) 섹션의 지침에 따라 필요한 DSC 메타 구성이 포함된 폴더를 생성합니다.
4. 온보드할 컴퓨터에 PowerShell DSC 메타 구성을 원격으로 적용합니다.

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

이 명령이 실행되는 컴퓨터에는 최신 버전의 [WMF 5](https://aka.ms/wmf5latest) 가 설치되어 있어야 합니다.

1. PowerShell DSC 메타 구성을를 원격으로 적용할 수 없는 경우 5 단계의 폴더에서 해당 컴퓨터에 해당 하는 메타 구성을 Linux 컴퓨터에 복사 합니다. 그런 다음, Azure Automation 상태 구성에 온보드하려는 각 Linux 머신에서 로컬로 `SetDscLocalConfigurationManager.py`를 호출합니다.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

2. Azure Portal 또는 cmdlet를 사용하여 Azure Automation 계정에서 등록된 DSC 노드로 온보드할 컴퓨터가 표시되는지 확인합니다.

## <a name="generating-dsc-metaconfigurations"></a>DSC 메타 구성 생성

일반적으로 모든 컴퓨터를 Azure Automation 상태 구성으로 등록 하려면 dsc 에이전트가 Azure Automation 상태 구성에서 끌어오거나 보고 하도록 지시 하는 [dsc 메타](/powershell/scripting/dsc/managing-nodes/metaConfig) 구성을 생성할 수 있습니다. Azure Automation 상태 구성에 대한 DSC 메타 구성은 PowerShell DSC 구성 또는 Azure Automation PowerShell cmdlet을 사용하여 생성될 수 있습니다.

> [!NOTE]
> DSC 메타 구성은 관리를 위해 Automation 계정에 컴퓨터를 등록하는 데 필요한 암호를 포함합니다. 사용한 후에 만들거나 삭제한 DSC 메타 구성을 제대로 보호해야 합니다.

### <a name="using-a-dsc-configuration"></a>DSC 구성 사용

1. 로컬 환경의 머신에서 관리자 권한으로 VSCode(또는 선호하는 편집기)를 엽니다. 이 컴퓨터에는 최신 버전의 [WMF 5](https://aka.ms/wmf5latest) 가 설치되어 있어야 합니다.
1. 다음 스크립트를 로컬로 복사합니다. 이 스크립트는 메타 구성을 만들기 위한 PowerShell DSC 구성 및 메타 구성 생성을 시작하는 명령을 포함합니다.

> [!NOTE]
> 상태 구성 노드 구성 이름은 포털에서 대/소문자를 구분합니다. 대/소문자가 일치하지 않는 경우 노드는 **노드** 탭에 표시되지 않습니다.

   ```powershell
   # The DSC configuration that will generate metaconfigurations
   [DscLocalConfigurationManager()]
   Configuration DscMetaConfigs
   {
        param
        (
            [Parameter(Mandatory=$True)]
            [String]$RegistrationUrl,

            [Parameter(Mandatory=$True)]
            [String]$RegistrationKey,

            [Parameter(Mandatory=$True)]
            [String[]]$ComputerName,

            [Int]$RefreshFrequencyMins = 30,

            [Int]$ConfigurationModeFrequencyMins = 15,

            [String]$ConfigurationMode = 'ApplyAndMonitor',

            [String]$NodeConfigurationName,

            [Boolean]$RebootNodeIfNeeded= $False,

            [String]$ActionAfterReboot = 'ContinueConfiguration',

            [Boolean]$AllowModuleOverwrite = $False,

            [Boolean]$ReportOnly
        )

        if(!$NodeConfigurationName -or $NodeConfigurationName -eq '')
        {
            $ConfigurationNames = $null
        }
        else
        {
            $ConfigurationNames = @($NodeConfigurationName)
        }

        if($ReportOnly)
        {
            $RefreshMode = 'PUSH'
        }
        else
        {
            $RefreshMode = 'PULL'
        }

        Node $ComputerName
        {
            Settings
            {
                RefreshFrequencyMins           = $RefreshFrequencyMins
                RefreshMode                    = $RefreshMode
                ConfigurationMode              = $ConfigurationMode
                AllowModuleOverwrite           = $AllowModuleOverwrite
                RebootNodeIfNeeded             = $RebootNodeIfNeeded
                ActionAfterReboot              = $ActionAfterReboot
                ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins
            }

            if(!$ReportOnly)
            {
            ConfigurationRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl          = $RegistrationUrl
                    RegistrationKey    = $RegistrationKey
                    ConfigurationNames = $ConfigurationNames
                }

                ResourceRepositoryWeb AzureAutomationStateConfiguration
                {
                ServerUrl       = $RegistrationUrl
                RegistrationKey = $RegistrationKey
                }
            }

            ReportServerWeb AzureAutomationStateConfiguration
            {
                ServerUrl       = $RegistrationUrl
                RegistrationKey = $RegistrationKey
            }
        }
   }

    # Create the metaconfigurations
    # NOTE: DSC Node Configuration names are case sensitive in the portal.
    # TODO: edit the below as needed for your use case
   $Params = @{
        RegistrationUrl = '<fill me in>';
        RegistrationKey = '<fill me in>';
        ComputerName = @('<some VM to onboard>', '<some other VM to onboard>');
        NodeConfigurationName = 'SimpleConfig.webserver';
        RefreshFrequencyMins = 30;
        ConfigurationModeFrequencyMins = 15;
        RebootNodeIfNeeded = $False;
        AllowModuleOverwrite = $False;
        ConfigurationMode = 'ApplyAndMonitor';
        ActionAfterReboot = 'ContinueConfiguration';
        ReportOnly = $False;  # Set to $True to have machines only report to AA DSC but not pull from it
   }

   # Use PowerShell splatting to pass parameters to the DSC configuration being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   DscMetaConfigs @Params
   ```

1. 등록할 컴퓨터의 이름 뿐만 아니라 Automation 계정에 대한 등록 키 및 URL을 입력합니다. 모든 다른 매개 변수는 선택 사항입니다. Automation 계정에 대한 등록 키와 등록 URL을 찾으려면 뒤에 나오는 [**등록 보호**](#secure-registration) 섹션을 참조하세요.
1. 머신이 Azure Automation 상태 구성에 DSC 상태 정보를 보고하지만 구성 또는 PowerShell 모듈을 끌어오지 않도록 하려면 **ReportOnly** 매개 변수를 true로 설정합니다.
1. 스크립트를 실행합니다. 이제 작업 디렉터리에 **DscMetaConfigs**라는 폴더가 있어야 하며 이는 등록할 컴퓨터에 대한 PowerShell DSC 메타 구성을 포함합니다(관리자로).

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="using-the-azure-automation-cmdlets"></a>Azure Automation cmdlet 사용

PowerShell DSC 로컬 구성 관리자 기본값이 사용자 사용 사례와 일치하고 Azure Automation 상태 구성에서 끌어오고 보고하는 모든 머신을 등록하려는 경우 Azure Automation cmdlet는 필요한 DSC 메타 구성을 생성하는 단순화된 방법을 제공합니다.

1. 로컬 환경의 머신에서 관리자 권한으로 PowerShell 콘솔이나 VSCode를 엽니다.
2. `Connect-AzAccount`를 사용하여 Azure Resource Manager에 연결
3. 노드를 등록하려는 Automation 계정에서 등록하려 컴퓨터에 대한 PowerShell DSC 메타 구성을 다운로드합니다.

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation Account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation Account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the meta configuration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. 이제 ***DscMetaConfigs***라는 폴더가 있어야 하며 이는 등록할 컴퓨터에 대한 PowerShell DSC 메타 구성을 포함합니다(관리자로).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="secure-registration"></a>등록 보호

DSC 노드에서 PowerShell DSC 끌어오기 또는 보고 서버(Azure Automation 상태 구성 포함)를 인증할 수 있도록 하는 WMF 5 DSC 등록 프로토콜을 통해 Azure Automation 계정에 머신을 안전하게 등록할 수 있습니다. 노드는 **등록 URL**에 있는 서버에 등록되며 **등록 키**를 통해 인증됩니다. 등록하는 동안 DSC 노드와 DSC 풀/보고 서버가 이 노드에 대해, 서버 게시-등록에 대한 인증에 사용할 고유 인증서를 협상합니다. 이 프로세스는 노드가 손상되어 악의적 동작을 수행하는 등, 온보드된 노드가 다른 노드를 가장하는 것을 방지합니다. 등록 후 해당 등록 키는 다시 인증에 사용되지 않으며 노드에서 삭제됩니다.

Azure Portal의 **계정 설정** 아래에 있는 **키**에서 상태 구성 등록 프로토콜에 필요한 정보를 얻을 수 있습니다. Automation 계정의 **Essentials** 패널에서 키 아이콘을 클릭하여 이 블레이드를 엽니다.

![Azure Automation 키 및 URL](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- 등록 URL은 키 관리 블레이드의 URL 필드입니다.
- 등록 키는 키 관리 블레이드에서 주기본 액세스 키 또는 보조 액세스 키입니다. 둘 중 하나를 사용할 수 있습니다.

이전 키를 사용한 향후 노드 등록 차단을 위해 Automation 계정의 기본 액세스 키와 보조 액세스 키는 언제든 (**키 관리** 페이지에서) 다시 생성하여 보안을 강화할 수 있습니다.

## <a name="certificate-expiration-and-re-registration"></a>인증서 만료 및 다시 등록

Azure Automation 상태 구성에서 DSC 노드로 컴퓨터를 등록 한 후 나중에 해당 노드를 다시 등록 해야 하는 여러 가지 이유가 있습니다.

- Windows Server 2019 이전의 Windows Server 버전에서는 각 노드가 1 년 후에 만료 되는 인증에 대해 고유한 인증서를 자동으로 협상 합니다. 현재 PowerShell DSC 등록 프로토콜은 만료가 임박 하면 인증서를 자동으로 갱신할 수 없으므로 1 년 후에 노드를 다시 등록 해야 합니다. 다시 등록 하기 전에 각 노드에서 Windows Management Framework 5.0 RTM을 실행 하 고 있는지 확인 합니다. 노드의 인증 인증서가 만료 되 고 노드가 다시 등록 되지 않으면 노드가 Azure Automation와 통신할 수 없으며 ' 응답 없음 '으로 표시 됩니다. 다시 등록 하면 인증서 만료 시간에서 90 일을 뺀 후 또는 인증서 만료 시간 이후에 발생 한 모든 시점에서 새 인증서를 생성 하 고 사용 하 게 됩니다.  이 문제에 대한 해결 방법은 Windows Server 2019 이상에 포함 되어 있습니다.
- ConfigurationMode와 같은 노드의 초기 등록 중에 설정된 [PowerShell DSC 로컬 구성 관리자 값](/powershell/scripting/dsc/managing-nodes/metaConfig4)을 변경하려면 현재 이러한 DSC 에이전트 값은 다시 등록을 통해서만 변경할 수 있습니다. 한 가지 예외는 노드에 할당된 노드 구성입니다. 이는 직접 Azure Automation DSC에서 변경될 수 있습니다.

다시 등록은이 문서에 설명 된 온 보 딩 방법 중 하나를 사용 하 여 처음에 노드를 등록 한 것과 같은 방식으로 수행할 수 있습니다. 다시 등록 하기 전에 Azure Automation 상태 구성에서 노드를 등록 취소할 필요가 없습니다.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Azure 가상 머신 온보드 문제 해결

Azure Automation 상태 구성을 사용하면 구성 관리를 위해 간편하게 Azure Windows VM을 온보드할 수 있습니다. 내부적으로 Azure VM 필요 상태 구성 확장은 VM을 Azure Automation 상태 구성에 등록하는 데 사용됩니다. Azure VM 필요 상태 구성 확장은 비동기적으로 실행되므로 그 진행 상황 추적과 실행 문제 해결이 중요할 수 있습니다.

> [!NOTE]
> Azure VM 필요 상태 구성 확장을 사용하는 Azure Automation 상태 구성에 Azure Windows VM을 온보드하는 모든 방법에서 노드가 Azure Automation에 등록된 것으로 표시하는 데는 최대 1시간이 소요됩니다. 이것은 Azure Automation 상태 구성에 VM을 온보드하는 데 필요한 Azure VM DSC 확장에 의한 VM의 Windows 관리 프레임워크 5.0의 설치 때문에 꼭 필요한 일입니다.

Azure VM DSC(Desired State Configuration) 확장의 상태를 확인하거나 문제를 해결하려면, Azure Portal에서 등록될 VM으로 이동한 다음, **설정** 아래에서 **확장**을 클릭합니다. 그런 다음, 운영 체제에 따라 **DSC** 또는 **DSCForLinux**를 클릭합니다. 자세한 내용을 보려면 **자세한 상태 보기**를 클릭합니다.

문제 해결에 대한 자세한 내용은 [Azure Automation 필요한 상태 구성 (DSC)의 문제 해결](./troubleshoot/desired-state-configuration.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- 시작하려면 [Azure Automation 상태 구성 시작하기](automation-dsc-getting-started.md)를 참조하세요.
- DSC 구성을 대상 노드에 할당할 수 있도록 DSC 구성을 컴파일하는 방법에 대해 알아보려면 [Azure Automation 상태 구성에서 구성 컴파일](automation-dsc-compile.md)을 참조하세요.
- PowerShell cmdlet 참조는 [Azure Automation 상태 구성 cmdlet](/powershell/module/az.automation#automation)을 참조하세요.
- 가격 책정 정보는 [Azure Automation 상태 구성 가격 책정](https://azure.microsoft.com/pricing/details/automation/)을 참조하세요.
- 지속적인 배포 파이프라인에서 Azure Automation 상태 구성을 사용하는 예제는 [Azure Automation 상태 구성 및 Chocolatey를 사용한 지속적인 배포](automation-dsc-cd-chocolatey.md)를 참조하세요.
