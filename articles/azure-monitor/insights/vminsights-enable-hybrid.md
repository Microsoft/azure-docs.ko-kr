---
title: 하이브리드 환경에 대한 Azure Monitor 사용
description: 이 문서에서는 하나 이상의 가상 머신을 포함 하는 하이브리드 클라우드 환경에 대 한 VM용 Azure Monitor를 사용 하도록 설정 하는 방법을 설명 합니다.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: d994df4d56f4958784256ff9cd92ce1e6f3b3e50
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88642166"
---
# <a name="enable-azure-monitor-for-vms-for-a-hybrid-virtual-machine"></a>하이브리드 가상 컴퓨터에 대 한 VM용 Azure Monitor 사용
이 문서에서는 온-프레미스 및 기타 클라우드 환경을 포함 하 여 Azure 외부에서 가상 머신에 대 한 VM용 Azure Monitor를 사용 하도록 설정 하는 방법을 설명 합니다.

> [!IMPORTANT]
> 하이브리드 Vm을 사용 하도록 설정 하는 권장 방법은 먼저 [서버에 대해 Azure Arc](../../azure-arc/servers/overview.md) 를 사용 하도록 설정 하 여 azure vm과 유사한 프로세스를 사용 하 VM용 Azure Monitor vm을 사용할 수 있도록 하는 것입니다. 이 문서에서는 Azure Arc를 사용 하지 않도록 선택 하는 경우 하이브리드 Vm을 등록 하는 방법을 설명 합니다.

## <a name="prerequisites"></a>사전 요구 사항

- [Log Analytics 작업 영역을 만들고 구성](vminsights-configure-workspace.md)합니다.
- 지원 되는 [운영 체제](vminsights-enable-overview.md#supported-operating-systems) 를 참조 하 여 활성화 하는 가상 머신 또는 가상 머신 확장 집합의 운영 체제가 지원 되는지 확인 합니다. 


## <a name="overview"></a>개요
Azure 외부의 가상 컴퓨터에는 Azure Vm에 사용 되는 것과 동일한 Log Analytics 에이전트 및 종속성 에이전트가 필요 합니다. 그러나 VM 확장을 사용 하 여 에이전트를 설치할 수 없으므로 게스트 운영 체제에 수동으로 설치 하거나 다른 방법을 통해 설치 해야 합니다. 

Log Analytics 에이전트 배포에 대 한 자세한 내용은 [Azure Monitor에 Windows 컴퓨터 연결](../platform/agent-windows.md) 또는 [Linux 컴퓨터를 Azure Monitor에](../platform/agent-linux.md) 연결을 참조 하세요. 종속성 에이전트에 대 한 세부 정보는이 문서에 나와 있습니다. 

## <a name="firewall-requirements"></a>방화벽 요구 사항
Log Analytics 에이전트에 대 한 방화벽 요구 사항은 [Log Analytics 에이전트 개요](../platform/log-analytics-agent.md#network-requirements)에 제공 됩니다. VM용 Azure Monitor 맵 Dependency Agent는 데이터 자체를 전송하지 않으며 방화벽 또는 포트를 변경하지 않아도 됩니다. 사용자가 네트워크의 컴퓨터에서 인터넷에 연결할 수 있도록 허용 하지 않는 경우에는 항상 Log Analytics 에이전트가 직접 또는 [Operations Management Suite 게이트웨이](../../azure-monitor/platform/gateway.md) 를 통해 Azure Monitor 서비스에 맵 데이터를 전송 합니다.


## <a name="dependency-agent"></a>종속성 에이전트

>[!NOTE]
>이 섹션에서 설명 하는 다음 정보는 [서비스 맵 솔루션](service-map.md)에도 적용 됩니다.  

다음 위치에서 종속성 에이전트를 다운로드할 수 있습니다.

| 파일 | OS | 버전 | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.10.5.10940 | C27A56D0BE9CF162DF73292DFBB2083F5FF749F2B80FCAD2545BC8B14B64A8D7  |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.10.5.10940 | 71B4E1DA5116E61E03317C49C6702B5069F01A0C9A7CB860F6ACFAF5C198740E |


## <a name="install-the-dependency-agent-on-windows"></a>Windows에 종속성 에이전트 설치

`InstallDependencyAgent-Windows.exe`를 실행하면 Dependency Agent를 수동으로 Windows 컴퓨터에 설치할 수 있습니다. 옵션 없이 이 실행 파일을 실행하면 설치 마법사가 시작되고 에이전트를 대화형으로 설치할 수 있습니다. 에이전트를 설치 하거나 제거 하려면 게스트 OS에 대 한 *관리자* 권한이 필요 합니다.

다음 표에는 명령줄에서 에이전트 설정을 통해 지원되는 매개 변수가 강조 표시되어 있습니다.

| 매개 변수 | Description |
|:--|:--|
| /? | 명령줄 옵션의 목록을 반환합니다. |
| /S | 사용자 상호 작용 없이 자동 설치를 수행합니다. |

예를 들어 매개 변수를 사용 하 여 설치 프로그램을 실행 하려면 `/?` **InstallDependencyAgent-Windows.exe/?** 를 입력 합니다.

Windows Dependency Agent에 대한 파일은 기본적으로 *C:\Program Files\Microsoft Dependency Agent*에 설치됩니다. 설치가 완료 된 후 종속성 에이전트를 시작 하지 못한 경우 자세한 오류 정보는 로그를 확인 하십시오. 로그 디렉터리는 *%Programfiles%\Microsoft Dependency Agent\logs*입니다.

### <a name="powershell-script"></a>PowerShell 스크립트
다음 샘플 PowerShell 스크립트를 사용 하 여 에이전트를 다운로드 하 고 설치 합니다.

```powershell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```


## <a name="install-the-dependency-agent-on-linux"></a>Linux에 Dependency Agent 설치

Dependency Agent는 셀프 추출 이진이 포함된 셸 스크립트인 *InstallDependencyAgent-Linux64.bin*을 사용하여 Linux 서버에 설치됩니다. `sh`를 사용하여 파일을 실행하거나 파일 자체에 실행 권한을 추가할 수 있습니다.

>[!NOTE]
> 에이전트를 설치 또는 구성하려면 루트 액세스가 필요합니다.
>

| 매개 변수 | Description |
|:--|:--|
| -help | 명령줄 옵션 목록을 가져옵니다. |
| -S | 사용자 프롬프트 없이 자동 설치를 수행합니다. |
| --check | 권한 및 운영 체제를 확인하지만 에이전트는 설치하지 않습니다. |

예를 들어 매개 변수를 사용 하 여 설치 프로그램을 실행 하려면 `-help` **installdependencyagent-linux64.bin-help**를 입력 합니다. 명령을 실행 하 여 루트로 Linux 종속성 에이전트를 설치 합니다 `sh InstallDependencyAgent-Linux64.bin` .

Dependency Agent를 시작하지 못하는 경우 로그에서 자세한 오류 정보를 확인합니다. Linux 에이전트에서 로그 디렉터리는 */var/opt/microsoft/dependency-agent/log*입니다.

Dependency Agent에 대한 파일은 다음 디렉터리에 있습니다.

| Files | 위치 |
|:--|:--|
| 코어 파일 | /opt/microsoft/dependency-agent |
| 로그 파일 | /var/opt/microsoft/dependency-agent/log |
| 구성 파일 | /etc/opt/microsoft/dependency-agent/config |
| 서비스 실행 파일 | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| 이진 스토리지 파일 | /var/opt/microsoft/dependency-agent/storage |

### <a name="shell-script"></a>셸 스크립트 
다음 샘플 셸 스크립트를 사용 하 여 에이전트를 다운로드 하 고 설치 합니다.

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```

## <a name="desired-state-configuration"></a>Desired State Configuration

DSC(필요한 상태 구성)를 통해 Dependency Agent를 배포하려면 xPSDesiredStateConfiguration 모듈을 다음과 같은 코드와 함께 사용할 수 있습니다.

```powershell
configuration VMInsights {

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    $DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

    Node localhost
    {
        # Download and install the Dependency agent
        xRemoteFile DAPackage
        {
            Uri = "https://aka.ms/dependencyagentwindows"
            DestinationPath = $DAPackageLocalPath
        }

        xPackage DA
        {
            Ensure="Present"
            Name = "Dependency Agent"
            Path = $DAPackageLocalPath
            Arguments = '/S'
            ProductId = ""
            InstalledCheckRegKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"
            InstalledCheckRegValueName = "DisplayName"
            InstalledCheckRegValueData = "Dependency Agent"
            DependsOn = "[xRemoteFile]DAPackage"
        }
    }
}
```



## <a name="troubleshooting"></a>문제 해결

### <a name="vm-doesnt-appear-on-the-map"></a>VM이 맵에 표시 되지 않습니다.

종속성 에이전트가 성공적으로 설치 되었지만 맵에 컴퓨터가 표시 되지 않으면 다음 단계를 수행 하 여 문제를 진단 합니다.

1. 종속성 에이전트가 성공적으로 설치되었나요? 서비스가 설치되어 실행 중인지 확인하여 성공적인 설치 여부를 검사할 수 있습니다.

    **Windows**: "Microsoft 종속성 에이전트" 라는 서비스를 찾습니다.

    **Linux**: 실행 중인 프로세스 "microsoft-종속성-에이전트"를 찾습니다.

2. [Log Analytics의 무료 가격 책정 계층](./solutions.md)에 있나요? 무료 요금제에서는 최대 5 개의 고유 컴퓨터를 사용할 수 있습니다. 이전 5 개에서 더 이상 데이터를 전송 하지 않는 경우에도 모든 후속 컴퓨터가 맵에 표시 되지 않습니다.

3. 컴퓨터에서 로그 및 성능 데이터를 Azure Monitor 로그에 보내고 있나요? 컴퓨터에 대해 다음 쿼리를 수행 합니다.

    ```Kusto
    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

    하나 이상의 결과를 반환 했습니까? 최근 데이터인가요? 이 경우 Log Analytics 에이전트가 올바르게 작동 하 고 서비스와 통신 하 고 있는 것입니다. 그렇지 않으면 [Windows용 Log Analytics 에이전트 문제 해결](../platform/agent-windows-troubleshoot.md) 또는 [Linux용 Log Analytics 에이전트 문제 해결](../platform/agent-linux-troubleshoot.md)을 참조하여 서버의 에이전트를 확인합니다.

#### <a name="computer-appears-on-the-map-but-has-no-processes"></a>컴퓨터는 맵에 나타나지만 프로세스가 없습니다.

맵에 서버가 표시 되지만 프로세스 또는 연결 데이터가 없는 경우 종속성 에이전트가 설치 되어 실행 중이지만 커널 드라이버가 로드 되지 않았음을 나타냅니다.

C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log 파일(Windows) 또는 /var/opt/microsoft/dependency-agent/log/service.log 파일(Linux)을 확인합니다. 파일의 마지막 줄에 커널이 로드되지 않은 이유가 표시되어야 합니다. 예를 들어, 커널을 업데이트 한 경우 Linux에서 커널이 지원되지 않을 수 있습니다.


## <a name="next-steps"></a>다음 단계

이제 가상 머신에 대 한 모니터링을 사용 하도록 설정 했으므로이 정보는 VM용 Azure Monitor 분석에 사용할 수 있습니다.

- 검색된 애플리케이션 종속성을 보려면 [VM용 Azure Monitor 맵 보기](vminsights-maps.md)를 참조하세요.

- VM의 성능에 대 한 병목 및 전반적인 사용률을 식별 하려면 [AZURE vm 성능 보기](vminsights-performance.md)를 참조 하세요.