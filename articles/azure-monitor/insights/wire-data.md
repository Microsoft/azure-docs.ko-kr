---
title: Azure Monitor에서 실시간 데이터 솔루션 | Microsoft Docs
description: Wire Data는 Log Analytics 에이전트를 사용한 컴퓨터의 통합 네트워크 및 성능 데이터입니다. 네트워크 데이터는 데이터를 상호 연결할 수 있도록 로그 데이터와 결합됩니다.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: fc3d7127-0baa-4772-858a-5ba995d1519b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/03/2018
ms.author: magoedte
ms.openlocfilehash: d295a5a7eae2bdc7983e7271aa11bce1840b92dd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61387579"
---
# <a name="wire-data-20-preview-solution-in-azure-monitor"></a>Azure Monitor에서 실시간 데이터 2.0 (미리 보기) 솔루션

![Wire Data 기호](media/wire-data/wire-data2-symbol.png)

Wire Data는 사용자 환경의 Operations Manager에서 모니터링되는 데이터를 포함하여 Log Analytics 에이전트를 통해 Windows 연결 및 Linux 연결 컴퓨터에서 수집되는 통합 네트워크 및 성능 데이터입니다. 네트워크 데이터는 데이터를 상호 연결할 수 있도록 다른 로그 데이터와 결합됩니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Log Analytics 에이전트 외에 Wire Data 솔루션은 IT 인프라에서 컴퓨터에 설치하는 Microsoft 종속성 에이전트를 사용합니다. 종속성 에이전트는 사용된 다양한 프로토콜 및 포트를 포함하여 [OSI 모델](https://en.wikipedia.org/wiki/OSI_model)에서 네트워크 수준 2-3에 해당하는 컴퓨터와 주고 받는 네트워크 데이터를 모니터링합니다. 데이터는 Azure Monitor에 전송 다음 에이전트를 사용 합니다.  

>[!NOTE]
>서비스 맵에서 이미 배포 하거나 서비스 맵을 고려 하는 경우 또는 [Vm에 대 한 Azure Monitor](../../azure-monitor/insights/vminsights-overview.md), 새 연결 메트릭 데이터 집합이 수집 하 고 실시간 데이터를 비교할 수 있는 정보를 제공 하는 Azure Monitor에 저장 합니다.

기본적으로 Azure Monitor는 CPU, 메모리, 디스크 및 네트워크 성능 데이터를 지정할 수 있는 다른 성능 카운터 뿐만 아니라 Windows 및 Linux에 기본 제공 된 카운터에 대 한 데이터를 기록 합니다. 컴퓨터에 사용되는 서브넷 및 애플리케이션 수준 프로토콜을 포함하여 네트워크 및 기타 데이터 수집이 에이전트별로 실시간으로 이루어집니다.  실시간 데이터는 TCP 전송 계층으로 내려가지 않고 애플리케이션 수준에서 네트워크 데이터를 확인합니다.  솔루션은 개별 ACK 및 SYN을 확인하지 않습니다.  핸드셰이크가 완료되면 라이브 연결로 간주되고 연결됨으로 표시됩니다. 해당 연결은 양쪽에서 소켓이 열려 있고 데이터를 앞뒤로 전달할 수 있음을 동의할 경우 실시간 상태를 유지합니다.  어느 쪽에서 연결을 닫으면 Disconnected로 표시 됩니다.  따라서 성공적으로 완료된 패킷의 대역폭만 계산하며 재전송된 패킷 또는 실패한 패킷 수는 보고하지 않습니다.

[Cisco의 NetFlow 프로토콜](https://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html)과 함께 [sFlow](http://www.sflow.org/) 또는 다른 소프트웨어를 사용한 경우 실시간 데이터에서 볼 통계 및 데이터는 익숙하게 느껴집니다.

기본 제공 로그 검색 쿼리 유형 중 일부는 다음과 같습니다.

- 실시간 데이터를 제공하는 에이전트
- 실시간 데이터를 제공하는 에이전트의 IP 주소
- IP 주소에서 아웃바운드 통신
- 애플리케이션 프로토콜에서 보낸 바이트 수
- 애플리케이션 서비스에서 보낸 바이트 수
- 서로 다른 프로토콜에서 받은 바이트 수
- IP 버전을 통해 보내고 받은 총 바이트 수
- 안정적으로 측정된 평균 연결 대기 시간
- 네트워크 트래픽을 시작 또는 수신한 컴퓨터 프로세스
- 프로세스에 대한 네트워크 트래픽의 양

실시간 데이터를 사용하여 검색하는 경우 상위 에이전트 및 상위 프로토콜에 대한 정보를 보기 위해 데이터를 필터링 및 그룹화할 수 있습니다. 또는 특정 컴퓨터(IP 주소/MAC 주소)가 언제, 얼마나 오래 통신하며 기본적으로 얼마나 많은 데이터를 전송하는지 볼 수 있으며 검색 기반의 네트워크 트래픽에 대한 메타데이터를 확인할 수 있습니다.

그러나 메타데이터를 보는 것이므로 자세한 문제 해결에 반드시 유용한 것은 아닙니다. Azure Monitor에서 실시간 데이터 네트워크 데이터를 전체 캡처한 아닙니다.  심도 있는 패킷 수준 문제 해결에는 적절하지 않습니다. 다른 수집 방법에 비해 에이전트를 사용 하는 장점은 어플라이언스를 설치, 네트워크 스위치를 다시 구성 하거나 복잡 한 구성을 수행 하려면 없는 한다는 점입니다. 실시간 데이터는 단순히 에이전트 기반이며 컴퓨터에 에이전트를 설치하고 자체 네트워크 트래픽을 모니터링합니다. 또 다른 장점은 사용자가 패브릭 계층을 소유하지 않는 클라우드 공급자, 호스팅 서비스 공급자 또는 Microsoft Azure에서 실행 중인 워크로드를 모니터링하는 경우입니다.

## <a name="connected-sources"></a>연결된 소스

Wire Data는 Microsoft 종속성 에이전트에서 해당 데이터를 가져옵니다. 종속성 에이전트는 Azure Monitor에 대 한 연결에 대 한 Log Analytics 에이전트에 따라 달라 집니다. 즉, 서버에 먼저 Log Analytics 에이전트를 설치하고 Dependency Agent로 구성해야 합니다. 다음 표는 Wire Data 솔루션이 지원하는 연결된 원본을 설명합니다.

| **연결된 원본** | **지원됨** | **설명** |
| --- | --- | --- |
| Windows 에이전트 | 예 | Wire Data는 Windows 에이전트 컴퓨터에서 데이터를 분석하고 수집합니다. <br><br> [Windows용 Log Analytics 에이전트](../../azure-monitor/platform/agent-windows.md) 외에도 Windows 에이전트에는 Microsoft Dependency Agent가 필요합니다. 운영 체제 버전의 전체 목록은 [지원되는 운영 체제](../../azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems)를 참조하세요. |
| Linux 에이전트 | 예 | Wire Data는 Linux 에이전트 컴퓨터에서 데이터를 분석하고 수집합니다.<br><br> [Linux용 Log Analytics 에이전트](../../azure-monitor/learn/quick-collect-linux-computer.md) 외에도 Linux 에이전트에는 Microsoft Dependency Agent가 필요합니다. 운영 체제 버전의 전체 목록은 [지원되는 운영 체제](../../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems)를 참조하세요. |
| System Center Operations Manager 관리 그룹 | 예 | Wire Data는 연결된 [System Center Operations Manager 관리 그룹](../../azure-monitor/platform/om-agents.md)의 Windows 및 Linux 에이전트에서 데이터를 분석하고 수집합니다. <br><br> Azure Monitor로 System Center Operations Manager 에이전트 컴퓨터에서 직접 연결은 필요 합니다. |
| Azure Storage 계정 | 아닙니다. | Wire Data는 에이전트 컴퓨터에서 데이터를 수집하므로 Azure Storage에서 수집할 데이터는 없습니다. |

Windows에서의 Microsoft 모니터링 에이전트 (MMA) 수집 하 고 데이터를 전송 합니다. System Center Operations Manager와 Azure Monitor에서 사용 됩니다. 에이전트는 컨텍스트에 따라 System Center Operations Manager 에이전트, Log Analytics 에이전트, MMA 또는 직접 에이전트라고 합니다. System Center Operations Manager 및 Azure Monitor는 MMA의 약간 다른 버전을 제공합니다. 이러한 버전은 각각 보고할 System Center Operations Manager, Azure Monitor 또는 둘 다에 있습니다.

Linux에서는 Linux 용 Log Analytics 에이전트를 수집 하 고 Azure Monitor에 데이터를 보냅니다. System Center Operations Manager 관리 그룹을 통해 Azure Monitor에 연결 하는 서버 또는 Azure Monitor에 직접 연결 된 에이전트를 사용 하 여 서버에서 Wire Data를 사용할 수 있습니다.

종속성 에이전트는 데이터 자체를 전송하지 않으며 방화벽 또는 포트를 변경하지 않아도 됩니다. Wire Data의 데이터는 직접 또는 Log Analytics 게이트웨이 통해 Azure Monitor에 Log Analytics 에이전트에서 항상 전송 됩니다.

![에이전트 다이어그램](./media/wire-data/agents.png)

Azure Monitor에 연결 된 관리 그룹을 사용 하 여 System Center Operations Manager 사용자 인 경우

- 추가 구성 없이 필요한 경우 System Center Operations Manager 에이전트 Azure Monitor에 연결 하려면 인터넷에 액세스할 수 있습니다.
- Log Analytics 게이트웨이 System Center Operations Manager 에이전트가 인터넷을 통해 Azure Monitor를 액세스할 수 없는 경우 System Center Operations Manager와 작동 하도록 구성 해야 합니다.

Windows 또는 Linux 컴퓨터는 서비스에 직접 연결할 수 없으면, Log Analytics 게이트웨이 사용 하 여 Azure Monitor에 연결 하도록 Log Analytics 에이전트를 구성 해야 합니다. [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=52666)에서 Log Analytics 게이트웨이를 다운로드할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

- [Insight and Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite-pricing) 솔루션 제품이 필요합니다.
- 이전 버전의 Wire Data 솔루션을 사용하는 경우 먼저 이전 버전을 제거해야 합니다. 그러나 원래 Wire Data 솔루션을 통해 캡처된 모든 데이터는 Wire Data 2.0 및 로그 검색에서 계속 사용할 수 있습니다.
- 종속성 에이전트를 설치 또는 제거하려면 관리자 권한이 필요합니다.
- 종속성 에이전트는 64비트 운영 체제의 컴퓨터에 설치되어야 합니다.

### <a name="operating-systems"></a>운영 체제

다음 섹션에서는 종속성 에이전트에 대해 지원되는 운영 체제를 나열합니다. Wire Data는 모든 운영 체제의 32비트 아키텍처를 지원하지 않습니다.

#### <a name="windows-server"></a>Windows Server

- Windows Server 2019
- Windows Server 2016 1803
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

#### <a name="windows-desktop"></a>Windows 데스크톱

- Windows 10 1803
- 윈도우 10
- Windows 8.1
- Windows 8
- Windows 7

#### <a name="supported-linux-operating-systems"></a>지원되는 Linux 운영 체제
다음 섹션에서는 Linux에서 종속성 에이전트에 대 한 지원 되는 운영 체제를 나열합니다.  

- 기본 및 SMP Linux 커널 릴리스만 지원됩니다.
- PAE 및 Xen과 같은 비표준 커널 릴리스는 Linux 배포판에 대해 지원되지 않습니다. 예를 들어 "2.6.16.21-0.8-xen"의 릴리스 문자열이 있는 시스템은 지원되지 않습니다.
- 표준 커널의 재컴파일을 포함한 사용자 지정 커널은 지원되지 않습니다.

##### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| OS 버전 | 커널 버전 |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

##### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| OS 버전 | 커널 버전 |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

##### <a name="centosplus"></a>CentOSPlus
| OS 버전 | 커널 버전 |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

##### <a name="ubuntu-server"></a>Ubuntu Server

| OS 버전 | 커널 버전 |
|:--|:--|
| Ubuntu 18.04 | kernel 4.15.\*<br>4.18* |
| Ubuntu 16.04.3 | kernel 4.15.* |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

##### <a name="suse-linux-11-enterprise-server"></a>SUSE Linux 11 Enterprise Server

| OS 버전 | 커널 버전
|:--|:--|
| 11 SP4 | 3.0.* |

##### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| OS 버전 | 커널 버전
|:--|:--|
| 12 SP2 | 4.4.* |
| 12 SP3 | 4.4.* |

### <a name="dependency-agent-downloads"></a>종속성 에이전트 다운로드

| 파일 | OS | 버전 | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.4 | A111B92AB6CF28EB68B696C60FE51F980BFDFF78C36A900575E17083972989E0 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.4 | AB58F3DB8B1C3DEE7512690E5A65F1DFC41B43831543B5C040FCCE8390F2282C |



## <a name="configuration"></a>구성

다음 단계를 수행하여 작업 영역에 대해 Wire Data 솔루션을 구성합니다.

1. Activity Log Analytics 솔루션을 사용 하도록 설정 합니다 [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WireData2OMS?tab=Overview) 하거나에 설명 된 프로세스를 사용 하 여 [솔루션 갤러리에서 솔루션을 모니터링 하는 추가](../../azure-monitor/insights/solutions.md)합니다.
2. 데이터를 가져오려는 각 컴퓨터에 종속성 에이전트를 설치합니다. 종속성 에이전트는 모든 컴퓨터에서 에이전트를 필요로 하지 않도록 바로 인접한 연결을 모니터링할 수 있습니다.

> [!NOTE]
> 새 작업 영역에 이전 버전의 Wire Data 솔루션을 추가할 수 없습니다. Wire Data 솔루션이 사용하도록 설정된 경우 계속해서 사용할 수 있습니다. 그러나 Wire Data 2.0을 사용하려면 먼저 원래 버전을 제거해야 합니다.
> 
 
### <a name="install-the-dependency-agent-on-windows"></a>Windows에 종속성 에이전트 설치

에이전트를 설치 또는 제거하려면 관리자 권한이 필요합니다.

종속성 에이전트는 InstallDependencyAgent-Windows.exe를 통해 Windows를 실행하는 컴퓨터에 설치됩니다. 옵션 없이 이 실행 파일을 실행하면 마법사가 시작되고 대화형으로 설치를 수행할 수 있습니다.

Windows를 실행하는 각 컴퓨터에서 종속성 에이전트를 설치하려면 다음 단계를 따르세요.

1. [사용자 환경에서 호스트되는 Windows 컴퓨터에서 데이터 수집](../../azure-monitor/platform/agent-windows.md)의 단계에 따라 Log Analytics 에이전트를 설치합니다.
2. 이전 섹션의 링크를 사용하여 Windows 종속성 에이전트를 다운로드한 후 다음 명령을 사용하여 실행합니다. `InstallDependencyAgent-Windows.exe`
3. 마법사에 따라 에이전트를 설치합니다.
4. 종속성 에이전트를 시작하지 못하는 경우 로그에서 자세한 오류 정보를 확인합니다. Windows 에이전트에서 로그 디렉터리는 %Programfiles%\Microsoft Dependency Agent\logs입니다.

#### <a name="windows-command-line"></a>Windows 명령줄

다음 표의 옵션을 사용하여 명령줄에서 설치합니다. 설치 플래그 목록을 보려면 /?를 사용하여 설치 관리자를 실행하십시오. 플래그를 사용하여 설치 관리자를 실행합니다.

InstallDependencyAgent-Windows.exe /?

| **플래그** | **설명** |
| --- | --- |
| <code>/?</code> | 명령줄 옵션 목록을 가져옵니다. |
| <code>/S</code> | 사용자 프롬프트 없이 자동 설치를 수행합니다. |

Windows 종속성 에이전트에 대한 파일은 기본적으로 C:\Program Files\Microsoft Dependency Agent에 있습니다.

### <a name="install-the-dependency-agent-on-linux"></a>Linux에 종속성 에이전트 설치

에이전트를 설치 또는 구성하려면 루트 액세스가 필요합니다.

종속성 에이전트는 자동 압축 해제 이진이 포함된 셸 스크립트인 InstallDependencyAgent-Linux64.bin을 통해 Linux 컴퓨터에 설치됩니다. _sh_를 사용하여 파일을 실행하거나 파일 자체에 실행 권한을 추가할 수 있습니다.

각 Linux 컴퓨터에서 종속성 에이전트를 설치하려면 다음 단계를 따르세요.

1. [사용자 환경에서 호스트되는 Linux 컴퓨터에서 데이터 수집](../../azure-monitor/learn/quick-collect-linux-computer.md#obtain-workspace-id-and-key)의 단계에 따라 Log Analytics 에이전트를 설치합니다.
2. 이전 섹션의 링크를 사용하여 Linux 종속성 에이전트를 다운로드한 후 다음 명령을 사용하여 루트로 설치합니다. sh InstallDependencyAgent-Linux64.bin
3. 종속성 에이전트를 시작하지 못하는 경우 로그에서 자세한 오류 정보를 확인합니다. Linux 에이전트에서 로그 디렉터리는 /var/opt/microsoft/dependency-agent/log입니다.

설치 플래그 목록을 보려면 다음과 같이 `-help` 플래그로 설치 프로그램을 실행합니다.

```
InstallDependencyAgent-Linux64.bin -help
```

| **플래그** | **설명** |
| --- | --- |
| <code>-help</code> | 명령줄 옵션 목록을 가져옵니다. |
| <code>-s</code> | 사용자 프롬프트 없이 자동 설치를 수행합니다. |
| <code>--check</code> | 권한 및 운영 체제를 확인하지만 에이전트는 설치하지 않습니다. |

종속성 에이전트에 대한 파일은 다음 디렉터리에 있습니다.

| **파일** | **위치**: |
| --- | --- |
| 코어 파일 | /opt/microsoft/dependency-agent |
| 로그 파일 | /var/opt/microsoft/dependency-agent/log |
| 구성 파일 | /etc/opt/microsoft/dependency-agent/config |
| 서비스 실행 파일 | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br><br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| 이진 저장소 파일 | /var/opt/microsoft/dependency-agent/storage |

### <a name="installation-script-examples"></a>설치 스크립트 예제

한 번에 많은 서버에 종속 에이전트를 쉽게 배포하려면 스크립트를 사용하는 것이 좋습니다. 다음 스크립트 예제를 사용하여 Windows 또는 Linux에서 종속성 에이전트를 다운로드하고 설치할 수 있습니다.

#### <a name="powershell-script-for-windows"></a>Windows용 PowerShell 스크립트

```powershell

Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S

```

#### <a name="shell-script-for-linux"></a>Linux용 셸 스크립트

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
```

```
sh InstallDependencyAgent-Linux64.bin -s
```

### <a name="desired-state-configuration"></a>필요한 상태 구성

필요한 상태 구성을 통해 종속성 에이전트를 배포하려면 xPSDesiredStateConfiguration 모듈 및 다음과 같은 코드를 사용할 수 있습니다.

```powershell
Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"



Node $NodeName

{

    # Download and install the Dependency Agent

    xRemoteFile DAPackage

    {

        Uri = "https://aka.ms/dependencyagentwindows"

        DestinationPath = $DAPackageLocalPath

        DependsOn = "[Package]OI"

    }

    xPackage DA

    {

        Ensure = "Present"

        Name = "Dependency Agent"

        Path = $DAPackageLocalPath

        Arguments = '/S'

        ProductId = ""

        InstalledCheckRegKey = "HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"

        InstalledCheckRegValueName = "DisplayName"

        InstalledCheckRegValueData = "Dependency Agent"

    }

}

```

### <a name="uninstall-the-dependency-agent"></a>종속성 에이전트 제거

다음 섹션을 사용하여 종속성 에이전트를 제거할 수 있도록 돕습니다.

#### <a name="uninstall-the-dependency-agent-on-windows"></a>Windows에서 종속성 에이전트 제거

관리자는 제어판을 통해 Windows용 종속성 에이전트를 제거할 수 있습니다.

관리자는 %Programfiles%\Microsoft Dependency Agent\Uninstall.exe를 실행하여 종속성 에이전트를 제거할 수도 있습니다.

#### <a name="uninstall-the-dependency-agent-on-linux"></a>Linux의 종속성 에이전트 제거

Linux에서 종속성 에이전트를 완전히 제거하려면 에이전트 자체와 에이전트와 함께 자동으로 설치된 커넥터를 제거해야 합니다. 다음 단일 명령을 사용하여 둘 다 제거할 수 있습니다.

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="management-packs"></a>관리 팩

Log Analytics 작업 영역에서 Wire Data가 활성화되면 해당 작업 영역의 모든 Windows 서버에 300KB 관리 팩이 전송됩니다. [연결된 관리 그룹](../platform/om-agents.md)에서 System Center Operations Manager 에이전트를 사용하는 경우 Dependency Monitor 관리 팩은 System Center Operations Manager에서 배포됩니다. 에이전트는 직접 연결 하는 경우 Azure Monitor는 관리 팩을 제공 합니다.

관리 팩 이름은 Microsoft.IntelligencePacks.ApplicationDependencyMonitor입니다. 이것은 %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs에 기록됩니다. 관리 팩에 사용된 데이터 원본은 %Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll입니다.

## <a name="using-the-solution"></a>솔루션 사용

다음 정보를 사용하여 솔루션을 설치하고 구성합니다.

- 실시간 데이터 솔루션은 Windows Server 2012 R2, Windows 8.1 이상의 운영 체제를 실행하는 컴퓨터에서 데이터를 획득합니다.
- 실시간 데이터를 획득하려는 컴퓨터에는 Microsoft .NET Framework 4.0 이상이 필요합니다.
- Wire Data 솔루션에서 설명한 프로세스를 사용 하 여 Log Analytics 작업 영역 추가 [솔루션 갤러리에서 솔루션을 모니터링 하는 추가](solutions.md)합니다. 추가 구성은 필요 없습니다.
- 특정 솔루션에 대한 실시간 데이터를 보려면 작업 영역에 솔루션이 이미 추가되어 있어야 합니다.

에이전트를 설치한 후 솔루션을 설치하면 Wire Data 2.0 타일이 작업 영역에 나타납니다.

![Wire Data 타일](./media/wire-data/wire-data-tile.png)

## <a name="using-the-wire-data-20-solution"></a>Wire Data 2.0 솔루션 사용

Azure Portal의 사용자 Log Analytics 작업 영역에 대한 **개요** 페이지에서 **Wire Data 2.0** 타일을 클릭하여 Wire Data 대시보드를 엽니다. 대시보드에는 다음 테이블의 블레이드가 포함되어 있습니다. 각 블레이드에는 지정된 범위 및 시간 범위에 대한 해당 블레이드의 기준과 일치하는 항목이 최대 10개까지 나열됩니다. 블레이드 맨 아래에서 **모두 보기**를 클릭하거나 블레이드 헤더를 클릭하여 모든 레코드를 반환하는 로그 검색을 실행할 수 있습니다.

| **블레이드** | **설명** |
| --- | --- |
| 네트워크 트래픽을 캡처하는 에이전트 | 네트워크 트래픽을 캡처하는 에이전트의 수를 표시하고 트래픽을 캡처하는 상위 10개의 컴퓨터를 나열합니다. <code>WireData \| summarize sum(TotalBytes) by Computer \| take 500000</code>에 대한 로그 검색을 실행할 번호를 클릭합니다. 목록에서 컴퓨터를 클릭하여 캡처된 바이트의 총 수를 반환하는 로그 검색을 실행합니다. |
| 로컬 서브넷 | 에이전트가 검색한 로컬 서브넷의 수를 보여 줍니다.  각각을 통해 전송된 바이트의 수와 함께 모든 서브넷을 나열하는 <code>WireData \| summarize sum(TotalBytes) by LocalSubnet</code>에 대한 로그 검색을 실행할 번호를 클릭합니다. 목록에서 서브넷을 클릭하여 서브넷을 통해 전송된 바이트의 총 수를 반환하는 로그 검색을 실행합니다. |
| 애플리케이션 수준 프로토콜 | 에이전트에서 검색된 것으로 사용 중인 애플리케이션 수준 프로토콜의 수를 보여 줍니다. <code>WireData \| summarize sum(TotalBytes) by ApplicationProtocol</code>에 대한 로그 검색을 실행할 번호를 클릭합니다. 프로토콜을 클릭하여 프로토콜을 사용하여 전송된 바이트의 총 수를 반환하는 로그 검색을 실행합니다. |

![Wire Data 대시보드](./media/wire-data/wire-data-dash.png)

**네트워크 트래픽을 캡처하는 에이전트** 블레이드를 사용하여 컴퓨터에서 사용되는 네트워크 대역폭 양을 확인할 수 있습니다. 이 블레이드는 사용자 환경에서 _chattiest_ 컴퓨터를 쉽게 찾을 수 있도록 도와 줍니다. 이러한 컴퓨터는 오버로드되거나 비정상적으로 작동하거나 보통 때보다 더 많은 네트워크 리소스를 사용할 수 있습니다.

![로그 검색 예제](./media/wire-data/log-search-example01.png)

마찬가지로 **로컬 서브넷** 블레이드를 사용하여 서브넷을 통해 이동하는 네트워크 트래픽의 양을 확인할 수 있습니다. 사용자는 종종 애플리케이션의 중요한 영역 주위 서브넷을 정의합니다. 이 블레이드는 해당 영역에 대한 뷰를 제공합니다.

![로그 검색 예제](./media/wire-data/log-search-example02.png)

**애플리케이션 수준 프로토콜** 블레이드는 사용 중인 프로토콜을 아는 데 도움이 되므로 유용합니다. 예를 들어 네트워크 환경에서 SSH가 사용되지 않는 것을 예상할 수 있습니다. 블레이드에서 사용할 수 있는 정보 보기는 사용자의 예상을 신속하게 확인하거나 예상이 틀렸음을 증명할 수 있습니다.

![로그 검색 예제](./media/wire-data/log-search-example03.png)

프로토콜 트래픽이 시간에 따라 증가하는지 감소하는지 여부를 아는 데 유용합니다. 예를 들어 애플리케이션에 의해 전송되고 있는 데이터 양이 증가하는 경우 이를 알고 있어야 하거나 주목할 만한 것일 수 있습니다.

## <a name="input-data"></a>데이터 입력

실시간 데이터 기능은 설정한 에이전트를 사용하여 네트워크 트래픽에 대한 메타데이터를 수집합니다. 각 에이전트는 약 15초마다 데이터를 보냅니다.

## <a name="output-data"></a>출력 데이터

각 형식의 입력 데이터에 대해 종류가 _WireData_인 레코드가 만들어집니다. WireData 레코드는 다음 테이블에 표시된 속성을 가집니다.

| 자산 | 설명 |
|---|---|
| Computer | 데이터가 수집된 컴퓨터 이름 |
| TimeGenerated | 레코드 시간 |
| LocalIP | 로컬 컴퓨터의 IP 주소 |
| SessionState | 연결 또는 연결 끊김 |
| ReceivedBytes | 받은 바이트의 양 |
| ProtocolName | 사용되는 네트워크 프로토콜의 이름 |
| IPVersion | IP 버전 |
| Direction | 인바운드 또는 아웃바운드 |
| MaliciousIP | 알려진 악의적인 원본의 IP 주소 |
| Severity | 의심되는 맬웨어 심각도 |
| RemoteIPCountry | 원격 IP 주소의 국가 |
| ManagementGroupName | Operations Manager 관리 그룹의 이름 |
| SourceSystem | 데이터가 수집된 원본 |
| SessionStartTime | 세션의 시작 시간 |
| SessionEndTime | 세션의 종료 시간 |
| LocalSubnet | 데이터가 수집된 서브넷 |
| LocalPortNumber | 로컬 포트 번호 |
| RemoteIP | 원격 컴퓨터에서 사용하는 원격 IP 주소 |
| RemotePortNumber | 원격 IP 주소에서 사용하는 포트 번호 |
| SessionID | 두 개의 IP 주소 간의 통신 세션을 식별하는 고유 값 |
| SentBytes | 보낸 바이트 수 |
| TotalBytes | 세션 중에 보낸 총 바이트 수 |
| ApplicationProtocol | 사용되는 네트워크 프로토콜의 유형   |
| ProcessID | Windows 프로세스 ID |
| ProcessName | 프로세스의 경로 및 파일 이름 |
| RemoteIPLongitude | IP 경도 값 |
| RemoteIPLatitude | IP 위도 값 |


## <a name="next-steps"></a>다음 단계

- [로그를 검색](../../azure-monitor/log-query/log-query-overview.md)하여 자세한 실시간 데이터 검색 레코드를 볼 수 있습니다.