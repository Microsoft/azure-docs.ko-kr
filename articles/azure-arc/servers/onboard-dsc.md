---
title: Windows PowerShell DSC를 사용 하 여 연결 된 컴퓨터 에이전트 설치
description: 이 문서에서는 Windows PowerShell DSC를 사용 하 여 Azure Arc 사용 서버 (미리 보기)를 사용 하 여 Azure에 컴퓨터를 연결 하는 방법에 대해 알아봅니다.
ms.date: 03/12/2020
ms.topic: conceptual
ms.openlocfilehash: 25d6e435c261a83bf81c15d5dd445a936d48a08b
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88213075"
---
# <a name="how-to-install-the-connected-machine-agent-using-windows-powershell-dsc"></a>Windows PowerShell DSC를 사용 하 여 연결 된 컴퓨터 에이전트를 설치 하는 방법

[Windows PowerShell DSC (필요한 상태 구성](/powershell/scripting/dsc/getting-started/winGettingStarted?view=powershell-7) )를 사용 하 여 windows 컴퓨터에 대 한 소프트웨어 설치 및 구성을 자동화할 수 있습니다. 이 문서에서는 DSC를 사용 하 여 하이브리드 Windows 컴퓨터에 Azure Arc 사용 서버 (미리 보기)에 연결 된 컴퓨터 에이전트를 설치 하는 방법을 설명 합니다.

## <a name="requirements"></a>요구 사항

- Windows PowerShell 버전 4.0 이상

- [AzureConnectedMachineDsc](https://www.powershellgallery.com/packages/AzureConnectedMachineDsc/1.0.1.0) DSC 모듈

- 컴퓨터를 비 대화형으로 Azure Arc 사용 서버 (미리 보기)에 연결 하는 서비스 주체입니다. Arc 사용 서버 (미리 보기)에 대 한 서비스 주체를 아직 만들지 않은 경우 [대규모로 온 보 딩에 대 한 서비스 주체 만들기](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) 섹션의 단계를 따릅니다.

## <a name="install-the-connectedmachine-dsc-module"></a>ConnectedMachine DSC 모듈 설치

1. 모듈을 수동으로 설치 하려면 소스 코드를 다운로드 하 고 프로젝트 디렉터리의 콘텐츠를에 압축을 풉니다 `$env:ProgramFiles\WindowsPowerShell\Modules folder` . 또는 다음 명령을 실행 하 여 PowerShellGet (PowerShell 5.0)을 사용 하 여 PowerShell 갤러리에서 설치 합니다.

    ```powershell
    Find-Module -Name AzureConnectedMachineDsc -Repository PSGallery | Install-Module
    ```

2. 설치를 확인 하려면 다음 명령을 실행 하 고 Azure 연결 된 컴퓨터 DSC 리소스가 사용 가능한 지 확인 합니다.

    ```powershell
    Get-DscResource -Module AzureConnectedMachineDsc
    ```

   출력에 다음과 유사한 항목이 표시 됩니다.

   ![연결 된 컴퓨터 DSC 모듈 설치 확인 예](./media/onboard-dsc/confirm-module-installation.png)

## <a name="install-the-agent-and-connect-to-azure"></a>에이전트를 설치하고 Azure에 연결

이 모듈의 리소스는 Azure 연결 된 컴퓨터 에이전트 구성을 관리 하도록 설계 되었습니다. 또한 폴더에 있는 PowerShell 스크립트도 포함 되어 `AzureConnectedMachineAgent.ps1` `AzureConnectedMachineDsc\examples` 있습니다. 커뮤니티 리소스를 사용 하 여 다운로드 및 설치를 자동화 하 고 Azure Arc와의 연결을 설정 합니다. 이 스크립트는 [Azure Portal에서 Azure에 하이브리드 컴퓨터 연결](onboard-portal.md) 문서에 설명 된 유사한 단계를 수행 합니다.

컴퓨터가 프록시 서버를 통해 서비스와 통신 해야 하는 경우 에이전트를 설치한 후 [여기](manage-agent.md#update-or-remove-proxy-settings)에 설명 된 명령을 실행 해야 합니다. 그러면 `https_proxy` 프록시 서버 시스템 환경 변수가 설정됩니다. 명령을 수동으로 실행 하는 대신 [ComputeManagementDsc](https://www.powershellgallery.com/packages/ComputerManagementDsc/6.0.0.0) 모듈을 사용 하 여 DSC에서이 단계를 수행할 수 있습니다.

>[!NOTE]
>DSC를 실행할 수 있도록 허용 하려면 localhost 구성을 실행 하는 경우에도 PowerShell 원격 명령을 받도록 Windows를 구성 해야 합니다. 환경을 올바르게 구성하려면 관리자 권한 PowerShell 터미널에서 `Set-WsManQuickConfig -Force`를 실행하면 됩니다.
>

Cmdlet을 사용 하 여 구성 문서 (MOF 파일)를 컴퓨터에 적용할 수 있습니다 `Start-DscConfiguration` .

다음은 사용할 PowerShell 스크립트에 전달 하는 매개 변수입니다.

- `TenantId`: Azure AD의 전용 인스턴스를 나타내는 고유 식별자 (GUID)입니다.

- `SubscriptionId`: 컴퓨터를 만들려는 Azure 구독의 구독 ID (GUID)입니다.

- `ResourceGroup`: 연결 된 컴퓨터를 포함할 리소스 그룹 이름입니다.

- `Location`: [지원 되는 Azure 지역](overview.md#supported-regions)을 참조 하세요. 이 위치는 리소스 그룹의 위치와 같을 수도 있고 다를 수도 있습니다.

- `Tags`: 연결 된 컴퓨터 리소스에 적용 해야 하는 태그의 문자열 배열입니다.

- `Credential`: [서비스 주체](onboard-service-principal.md)를 사용 하 여 대규모로 컴퓨터를 등록 하는 데 사용 되는 **ApplicationId** 및 **암호** 를 사용 하는 PowerShell 자격 증명 개체입니다. 

1. PowerShell 콘솔에서 파일을 저장 한 폴더로 이동 `.ps1` 합니다.

2. 다음 PowerShell 명령을 실행하여 MOF 문서를 컴파일합니다(DSC 구성 컴파일에 대한 자세한 내용은 [DSC 구성](/powershell/scripting/dsc/configurations/configurations?view=powershell-7) 참조).

    ```powershell
    .\`AzureConnectedMachineAgent.ps1 -TenantId <TenantId GUID> -SubscriptionId <SubscriptionId GUID> -ResourceGroup '<ResourceGroupName>' -Location '<LocationName>' -Tags '<Tag>' -Credential <psCredential>
    ```

3. 그러면 `localhost.mof file` 라는 새 폴더에이 만들어집니다 `C:\dsc` .

에이전트를 설치 하 고 Azure Arc 사용 서버 (미리 보기)에 연결 하도록 구성한 후 Azure Portal으로 이동 하 여 서버가 성공적으로 연결 되었는지 확인 합니다. [Azure Portal](https://aka.ms/hybridmachineportal)에서 해당 머신을 확인합니다.

## <a name="adding-to-existing-configurations"></a>기존 구성에 추가

이 리소스는 기존 DSC 구성에 추가 하 여 컴퓨터에 대 한 종단 간 구성을 나타낼 수 있습니다. 예를 들어이 리소스를 보안 운영 체제 설정을 설정 하는 구성에 추가할 수 있습니다.

PowerShell 갤러리의 [CompsiteResource](https://www.powershellgallery.com/packages/compositeresource/0.4.0) 모듈을 사용 하 여 구성 결합을 더욱 간소화 하는 예제 구성의 [복합 리소스](/powershell/scripting/dsc/resources/authoringResourceComposite?view=powershell-7) 를 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Policy](../../governance/policy/overview.md)를 사용하여 머신을 관리하는 방법을 알아봅니다(예: VM [게스트 구성](../../governance/policy/concepts/guest-configuration.md), 머신이 예상되는 Log Analytics 작업 영역에 보고되는지 확인, [VM을 사용한 Azure Monitor](../../azure-monitor/insights/vminsights-enable-policy.md)로 모니터링 등).

- [Log Analytics 에이전트](../../azure-monitor/platform/log-analytics-agent.md)에 대해 자세히 알아보세요. 머신에서 실행되는 OS 및 워크로드를 사전에 모니터링하거나, 자동화 Runbook 또는 업데이트 관리 같은 솔루션을 사용하여 관리하거나, [Azure Security Center](../../security-center/security-center-intro.md) 같은 다른 Azure 서비스를 사용하려는 경우에는 Windows 및 Linux용 Log Analytics 에이전트가 필요합니다.