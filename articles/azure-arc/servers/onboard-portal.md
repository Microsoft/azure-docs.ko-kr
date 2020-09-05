---
title: Azure Portal에서 Azure에 하이브리드 머신 연결
description: 이 문서에서는 Azure Portal에서 Azure Arc 사용 서버 (미리 보기)를 사용 하 여 에이전트를 설치 하 고 Azure에 컴퓨터를 연결 하는 방법에 대해 알아봅니다.
ms.date: 08/07/2020
ms.topic: conceptual
ms.custom: references_regions
ms.openlocfilehash: 23415bc648ae31b9073adb71d6f066a28c144c9d
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88213504"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>Azure Portal에서 Azure에 하이브리드 머신 연결

일련의 단계를 수동으로 수행 하 여 환경에서 하나 또는 소수의 Windows 또는 Linux 컴퓨터에 대해 Azure Arc 사용 서버 (미리 보기)를 사용 하도록 설정할 수 있습니다. 또는 제공된 템플릿 스크립트를 실행하여 자동화된 방법을 사용할 수 있습니다. 이 스크립트는 두 에이전트의 다운로드 및 설치를 자동화합니다.

이 방법을 사용하려면 머신에 대한 관리자 권한으로 에이전트를 설치하고 구성할 수 있어야 합니다. Linux에서는 루트 계정을 사용하여 수행하고, Windows에서는 로컬 관리자 그룹의 멤버로 수행해야 합니다.

시작하려면 먼저 [사전 요구 사항](agent-overview.md#prerequisites)을 검토하고 구독 및 리소스에서 요구 사항을 충족하는지 확인해야 합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Azure Portal에서 설치 스크립트 생성

다운로드 및 설치를 자동화하고 Azure Arc와의 연결을 설정하는 스크립트는 Azure Portal에서 사용할 수 있습니다. 프로세스를 완료하려면 다음을 수행합니다.

1. 브라우저에서 [Azure Portal](https://aka.ms/hybridmachineportal)로 이동합니다.

1. **머신 - Azure Arc** 페이지의 왼쪽 위에서 **추가**를 선택하거나 가운데 창의 아래쪽에서 **머신 만들기 - Azure Arc** 옵션을 선택합니다.

1. **메서드 선택** 페이지에서 **대화형 스크립트를 사용하여 머신 추가** 타일, **스크립트 생성**을 차례로 선택합니다.

1. **스크립트 생성** 페이지에서 머신을 Azure 내에서 관리하려는 구독 및 리소스 그룹을 선택합니다. 머신 메타데이터를 저장할 Azure 위치를 선택합니다.

    >[!NOTE]
    >Azure Arc 사용 서버 (미리 보기)는 다음 영역만 지원 합니다.
    >- EastUS
    >- WestUS2
    >- WestEurope
    >- SoutheastAsia
    >
    >개요 문서의 [여기](overview.md#supported-regions)에서 지역 선택 시 추가 고려 사항을 검토하세요.

1. **스크립트 생성** 페이지의 **운영 체제** 드롭다운 목록에서 스크립트를 실행할 운영 체제를 선택합니다.

1. 머신에서 프록시 서버를 통해 통신하여 인터넷에 연결하는 경우 **다음: 프록시 서버**를 선택합니다.

1. **프록시 서버** 탭에서 머신에서 프록시 서버와 통신하는 데 사용할 프록시 서버 IP 주소 또는 이름 및 포트 번호를 지정합니다. 해당 값을 `http://<proxyURL>:<proxyport>` 형식으로 입력합니다.

1. **검토 + 생성**을 선택합니다.

1. **검토 + 생성** 탭에서 요약 정보를 검토한 다음, **다운로드**을 선택합니다. 그래도 변경해야 하는 경우 **이전**을 선택합니다.

## <a name="install-and-validate-the-agent-on-windows"></a>Windows에서 에이전트 설치 및 유효성 검사

### <a name="install-manually"></a>수동 설치

*AzureConnectedMachineAgent.msi* Windows Installer 패키지를 실행하여 Connected Machine 에이전트를 수동으로 설치할 수 있습니다. Microsoft 다운로드 센터에서 최신 버전의 [Windows 에이전트 Windows Installer 패키지](https://aka.ms/AzureConnectedMachineAgent)를 다운로드할 수 있습니다.

>[!NOTE]
>* 에이전트를 설치하거나 제거하려면 *관리자* 권한이 있어야 합니다.
>* 먼저 설치 관리자 패키지를 다운로드하여 대상 서버의 폴더 또는 공유 네트워크 폴더에 복사해야 합니다. 옵션 없이 설치 관리자 패키지를 실행하면 에이전트를 대화형으로 설치하기 위해 수행할 수 있는 설치 마법사가 시작됩니다.

컴퓨터가 프록시 서버를 통해 서비스와 통신 해야 하는 경우 에이전트를 설치한 후 다음 단계에서 설명 하는 명령을 실행 해야 합니다. 이 명령은 프록시 서버 시스템 환경 변수를 설정 합니다 `https_proxy` .

Windows Installer 패키지에 대한 명령줄 옵션에 익숙하지 않은 경우 [Msiexec 표준 명령줄](/windows/win32/msi/standard-installer-command-line-options) 및 [Msiexec 명령줄 옵션](/windows/win32/msi/command-line-options)을 검토하세요.

예를 들어 `/?` 매개 변수를 사용하여 설치 프로그램을 실행하고 도움말 및 빠른 참조 옵션을 검토합니다.

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /?
```

1. 에이전트를 자동으로 설치하고 이미 존재하는 `C:\Support\Logs` 폴더에 설치 로그 파일을 만들려면 다음 명령을 실행합니다.

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentsetup.log"
    ```

    설치가 완료된 후 에이전트가 시작되지 않으면 자세한 오류 정보를 로그에서 확인합니다. 로그 디렉터리는 *%Programfiles%\AzureConnectedMachineAgentAgent\logs*입니다.

2. 컴퓨터가 프록시 서버를 통해 통신 해야 하는 경우 프록시 서버 환경 변수를 설정 하려면 다음 명령을 실행 합니다.

    ```powershell
    [Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
    $env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
    # For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
    Restart-Service -Name himds
    ```

    >[!NOTE]
    >이 미리 보기에서는 에이전트에서 프록시 인증 설정을 지원하지 않습니다.
    >

3. 에이전트를 설치한 후 다음 명령을 실행하여 Azure Arc 서비스와 통신하도록 구성해야 합니다.

    ```dos
    "%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID"
    ```

### <a name="install-with-the-scripted-method"></a>스크립팅된 메서드를 사용하여 설치

1. 서버에 로그인합니다.

1. 관리자 권한 PowerShell 명령 프롬프트를 엽니다.

    >[!NOTE]
    >이 스크립트는 64 비트 버전의 Windows PowerShell에서 실행 되는 것만 지원 합니다.
    >

1. 스크립트를 복사한 폴더 또는 공유로 변경하고, `./OnboardingScript.ps1` 스크립트를 실행하여 서버에서 실행합니다.

설치가 완료된 후 에이전트가 시작되지 않으면 자세한 오류 정보를 로그에서 확인합니다. 로그 디렉터리는 *%Programfiles%\AzureConnectedMachineAgentAgent\logs*입니다.

## <a name="install-and-validate-the-agent-on-linux"></a>Linux에서 에이전트 설치 및 유효성 검사

Linux용 Connected Machine 에이전트는 Microsoft [패키지 리포지토리](https://packages.microsoft.com/)에서 호스팅되는 배포(.RPM 또는 .DEB)에 대한 기본 설정 패키지 형식으로 제공됩니다. [`Install_linux_azcmagent.sh` 셸 스크립트 번들](https://aka.ms/azcmagent)에서 수행하는 작업은 다음과 같습니다.

- packages.microsoft.com에서 에이전트 패키지를 다운로드하도록 호스트 머신을 구성합니다.
- 하이브리드 리소스 공급자 패키지를 설치합니다.
- Azure Arc에 컴퓨터를 등록 합니다.

필요에 따라 `--proxy "{proxy-url}:{proxy-port}"` 매개 변수를 포함하여 에이전트를 프록시 정보로 구성할 수 있습니다.

또한 이 스크립트에는 지원되는 배포와 지원되지 않는 배포를 식별하는 논리가 포함되어 있으며 설치를 수행하는 데 필요한 권한을 확인합니다.

다음 예제에서는 에이전트를 다운로드하여 설치합니다.

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent.
bash ~/Install_linux_azcmagent.sh
```

프록시 서버를 통해 통신하도록 에이전트를 구성하는 `--proxy` 매개 변수를 포함하여 에이전트를 다운로드하고 설치하려면 다음 명령을 실행합니다.

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

## <a name="verify-the-connection-with-azure-arc"></a>Azure Arc 연결 확인

에이전트를 설치 하 고 Azure Arc 사용 서버 (미리 보기)에 연결 하도록 구성한 후 Azure Portal으로 이동 하 여 서버가 성공적으로 연결 되었는지 확인 합니다. [Azure Portal](https://aka.ms/hybridmachineportal)에서 머신을 확인합니다.

![성공적인 서버 연결](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>다음 단계

- [Azure Policy](../../governance/policy/overview.md)를 사용하여 머신을 관리하는 방법을 알아봅니다(예: VM [게스트 구성](../../governance/policy/concepts/guest-configuration.md), 머신이 예상되는 Log Analytics 작업 영역에 보고되는지 확인, [VM을 사용한 Azure Monitor](../../azure-monitor/insights/vminsights-enable-policy.md)로 모니터링 등).

- [Log Analytics 에이전트](../../azure-monitor/platform/log-analytics-agent.md)에 대해 자세히 알아보세요. 머신에서 실행되는 OS 및 워크로드를 사전에 모니터링하거나, 자동화 Runbook 또는 업데이트 관리 같은 솔루션을 사용하여 관리하거나, [Azure Security Center](../../security-center/security-center-intro.md) 같은 다른 Azure 서비스를 사용하려는 경우에는 Windows 및 Linux용 Log Analytics 에이전트가 필요합니다.