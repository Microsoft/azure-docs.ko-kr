---
title: Azure Portal에서 Azure에 하이브리드 컴퓨터 연결
description: 이 문서에서는 Azure Portal에서 서버 (미리 보기) 용 Azure Arc를 사용 하 여 에이전트를 설치 하 고 Azure에 컴퓨터를 연결 하는 방법에 대해 알아봅니다.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 02/12/2020
ms.topic: conceptual
ms.openlocfilehash: 12fc29cf12fba6325af3197e727d94b3073ef2ff
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192312"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>Azure Portal에서 Azure에 하이브리드 컴퓨터 연결

일련의 단계를 수동으로 수행 하 여 환경에서 하나 또는 소수의 Windows 또는 Linux 컴퓨터에 대해 서버 (미리 보기)에 대해 Azure Arc를 사용 하도록 설정할 수 있습니다. 또는 제공 된 템플릿 스크립트를 실행 하 여 자동화 된 방법을 사용할 수 있습니다. 이 스크립트는 두 에이전트의 다운로드 및 설치를 자동화 합니다.

이 방법을 사용 하려면 에이전트를 설치 하 고 구성 하기 위해 컴퓨터에 대 한 관리자 권한이 있어야 합니다. Linux에서 루트 계정을 사용 하 여 Windows에서 사용자는 로컬 Administrators 그룹의 구성원입니다.

시작 하기 전에 [필수 구성 요소](overview.md#prerequisites) 를 검토 하 고 구독과 리소스가 요구 사항을 충족 하는지 확인 해야 합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Azure Portal에서 설치 스크립트를 생성 합니다.

다운로드 및 설치를 자동화 하 고 Azure Arc와의 연결을 설정 하는 스크립트는 Azure Portal에서 사용할 수 있습니다. 프로세스를 완료 하려면 다음을 수행 합니다.

1. 브라우저에서 [Azure Portal](https://aka.ms/hybridmachineportal)로 이동 합니다.

1. **컴퓨터-Azure arc** 페이지에서 왼쪽 위에 있는 **추가**를 선택 하거나 가운데 창 맨 아래에 있는 **컴퓨터 만들기-Azure arc** 옵션을 선택 합니다. 

1. **방법 선택** 페이지에서 **대화형 스크립트를 사용 하 여 컴퓨터 추가** 타일을 선택한 다음 **스크립트 생성**을 선택 합니다.

1. **스크립트 생성** 페이지에서 Azure 내에서 컴퓨터를 관리 하려는 구독 및 리소스 그룹을 선택 합니다. 컴퓨터 메타 데이터를 저장할 Azure 위치를 선택 합니다.

    >[!NOTE]
    >서버 (미리 보기) 용 Azure Arc는 다음 영역만 지원 합니다.
    >- WestUS2
    >- WestEurope
    >- WestAsia
    >
    >개요 문서 [에서 영역을](overview.md#supported-regions) 선택할 때 추가 고려 사항을 검토 합니다.

1. **스크립트 생성** 페이지의 **운영 체제** 드롭다운 목록에서 스크립트가 실행 될 운영 체제를 선택 합니다.

1. 컴퓨터가 인터넷에 연결 하기 위해 프록시 서버를 통해 통신 하는 경우 **다음: 프록시 서버**를 선택 합니다. 
1. **프록시 서버 탭에서** 프록시 서버 IP 주소 또는 컴퓨터에서 프록시 서버와 통신 하는 데 사용할 이름 및 포트 번호를 지정 합니다. 해당 값을 `http://<proxyURL>:<proxyport>` 형식으로 입력합니다. 
1. **검토 + 생성**을 선택 합니다.

1. **검토 + 생성** 탭에서 요약 정보를 검토 한 다음 **다운로드**를 선택 합니다. 변경 해야 하는 경우 **이전**을 선택 합니다.

## <a name="install-and-validate-the-agent-on-windows"></a>Windows에서 에이전트 설치 및 유효성 검사

### <a name="install-manually"></a>수동 설치
Windows Installer 패키지 *AzureConnectedMachineAgent*를 실행 하 여 연결 된 컴퓨터 에이전트를 수동으로 설치할 수 있습니다. 

> [!NOTE]
> * 에이전트를 설치 하거나 제거 하려면 *관리자* 권한이 있어야 합니다.
> * 먼저 설치 관리자 패키지를 대상 서버의 폴더 또는 공유 네트워크 폴더에 다운로드 하 여 복사 해야 합니다. 옵션 없이 설치 관리자 패키지를 실행 하는 경우 설치 마법사가 시작 되 고,이 마법사를 사용 하 여 대화형으로 에이전트를 설치할 수 있습니다.

컴퓨터가 프록시 서버를 통해 서비스와 통신 해야 하는 경우 에이전트를 설치한 후이 문서의 뒷부분에서 설명 하는 명령을 실행 해야 합니다. 그러면 프록시 서버 시스템 환경 변수가 `https_proxy`설정 됩니다.

다음 표에는 명령줄에서 에이전트 설정을 통해 지원되는 매개 변수가 강조 표시되어 있습니다.

| 매개 변수 | Description |
|:--|:--|
| /? | 명령줄 옵션의 목록을 반환합니다. |
| /S | 사용자 상호 작용 없이 자동 설치를 수행합니다. |

예를 들어 `/?` 매개 변수를 사용 하 여 설치 프로그램을 실행 하려면 `msiexec.exe /i AzureConnectedMachineAgent.msi /?`을 입력 합니다.

연결 된 컴퓨터 에이전트에 대 한 파일은 기본적으로 *C:\Program Files\AzureConnectedMachineAgent* 에 설치 됩니다. 설치가 완료 된 후에 에이전트를 시작 하지 못하면 로그에서 자세한 오류 정보를 확인 하십시오. 로그 디렉터리는 *%Programfiles%\AzureConnectedMachineAgentAgent\logs*입니다.

### <a name="install-with-the-scripted-method"></a>스크립팅된 메서드를 사용 하 여 설치

1. 서버에 로그인 합니다.

1. 관리자 권한 PowerShell 명령 프롬프트를 엽니다.

1. 스크립트를 복사한 폴더 또는 공유로 변경 하 고 `./OnboardingScript.ps1` 스크립트를 실행 하 여 서버에서 실행 합니다.

### <a name="configure-the-agent-proxy-setting"></a>에이전트 프록시 설정 구성

프록시 서버 환경 변수를 설정 하려면 다음 명령을 실행 합니다.

```powershell
# If a proxy server is needed, execute these commands with the proxy URL and port.
[Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
Restart-Service -Name himds
```

>[!NOTE]
>에이전트는이 미리 보기에서 프록시 인증 설정을 지원 하지 않습니다.
>

### <a name="configure-agent-communication"></a>에이전트 통신 구성

에이전트를 설치한 후 다음 명령을 실행 하 여 Azure Arc 서비스와 통신 하도록 에이전트를 구성 해야 합니다.

`%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="install-and-validate-the-agent-on-linux"></a>Linux에서 에이전트 설치 및 유효성 검사

Linux 용 연결 된 컴퓨터 에이전트는 배포에 대 한 기본 패키지 형식으로 제공 됩니다. RPM 또는입니다. DEB)를 Microsoft [패키지 리포지토리에](https://packages.microsoft.com/)호스팅합니다. [셸 스크립트 번들 `Install_linux_azcmagent.sh`](https://aka.ms/azcmagent) 다음 작업을 수행 합니다.

- Packages.microsoft.com에서 에이전트 패키지를 다운로드 하도록 호스트 컴퓨터를 구성 합니다.
- 하이브리드 리소스 공급자 패키지를 설치합니다.

필요에 따라 `--proxy "{proxy-url}:{proxy-port}"` 매개 변수를 포함 하 여 프록시 정보를 사용 하 여 에이전트를 구성할 수 있습니다.

스크립트에는 지원 되거나 지원 되지 않는 배포를 식별 하는 논리도 포함 되며, 설치를 수행 하는 데 필요한 사용 권한을 확인 합니다. 

다음 예에서는 에이전트를 다운로드 하 여 설치 합니다.

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh
```

에이전트를 다운로드 하 고 설치 하려면 프록시 서버를 통해 통신 하도록 에이전트를 구성 하는 `--proxy` 매개 변수를 포함 하 여 다음 명령을 실행 합니다.

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

### <a name="configure-the-agent-communication"></a>에이전트 통신 구성

에이전트를 설치한 후 다음 명령을 실행 하 여 Azure Arc 서비스와 통신 하도록 구성 합니다.

`/opt/azcmagent/bin/azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="verify-the-connection-with-azure-arc"></a>Azure Arc와의 연결 확인

에이전트를 설치 하 고 서버에 대 한 Azure Arc (미리 보기)에 연결 하도록 구성한 후 Azure Portal으로 이동 하 여 서버가 성공적으로 연결 되었는지 확인 합니다. [Azure Portal](https://aka.ms/hybridmachineportal)에서 컴퓨터를 확인 합니다.

![성공적인 서버 연결](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="clean-up"></a>정리

서버 (미리 보기)에 대 한 Azure Arc에서 컴퓨터의 연결을 끊으려면 다음을 수행 합니다.

1. [Azure Portal](https://aka.ms/hybridmachineportal)로 이동 하 여 서버 (미리 보기)에 대 한 Azure Arc를 엽니다.

1. 목록에서 컴퓨터를 선택 하 고 줄임표 ( **...** )를 선택한 다음 **삭제**를 선택 합니다.

1. 컴퓨터에서 Windows 에이전트를 제거 하려면 다음을 수행 합니다.

    a. 관리자 권한이 있는 계정으로 컴퓨터에 로그인 합니다.  
    b. **제어판**에서 **프로그램 및 기능**을 선택 합니다.  
    다. **프로그램 및 기능**에서 **Azure 연결 된 컴퓨터 에이전트**를 선택 하 고 **제거**를 선택한 다음 **예**를 선택 합니다.  

    >[!NOTE]
    > **AzureConnectedMachineAgent** 설치 관리자 패키지를 두 번 클릭 하 여 에이전트 설치 마법사를 실행할 수도 있습니다.

    에이전트의 제거를 스크립팅 하려는 경우 다음 예제를 사용 하면 제품 코드를 검색 하 고 Msiexec.exe 명령줄-`msiexec /x {Product Code}`를 사용 하 여 에이전트를 제거할 수 있습니다. 이렇게 하려면 다음을 수행합니다.  
    
    a. 레지스트리 편집기를 엽니다.  
    b. 레지스트리 키 `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall`아래에서 제품 코드 GUID를 찾아 복사 합니다.  
    다. 그런 다음 Msiexec를 사용 하 여 에이전트를 제거할 수 있습니다.

    다음 예에서는 에이전트를 제거 하는 방법을 보여 줍니다.

    ```powershell
    Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
    Get-ItemProperty | `
    Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
    ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
    ```

1. Linux 에이전트를 제거 하려면 다음 명령을 실행 합니다.

      ```bash
      sudo apt purge hybridagent
      ```

## <a name="next-steps"></a>다음 단계

- [Azure Policy](../../governance/policy/overview.md)를 사용 하 여 컴퓨터를 관리 하는 방법, 컴퓨터에서 예상 Log Analytics 작업 영역에 보고 하는지 [확인,](../../governance/policy/concepts/guest-configuration.md)vm을 사용 하 여 [Azure Monitor](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)모니터링 사용 등의 작업을 수행 하는 방법을 알아봅니다.

- [Log Analytics 에이전트](../../azure-monitor/platform/log-analytics-agent.md)에 대해 자세히 알아보세요. 컴퓨터에서 실행 되는 OS 및 워크 로드를 사전에 모니터링 하거나, 자동화 runbook 또는 업데이트 관리와 같은 솔루션을 사용 하 여 관리 하거나, [Azure Security Center](../../security-center/security-center-intro.md)같은 다른 Azure 서비스를 사용 하려는 경우 Windows 및 Linux 용 Log Analytics 에이전트가 필요 합니다.