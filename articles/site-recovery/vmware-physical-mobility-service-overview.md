---
title: Azure Site Recovery를 사용 하 여 VMware Vm 및 물리적 서버의 재해 복구에 대 한 모바일 서비스 정보 Microsoft Docs
description: Azure Site Recovery 서비스를 사용 하 여 Azure에 대 한 VMware Vm 및 물리적 서버 재해 복구에 대 한 모바일 서비스 에이전트에 대해 알아봅니다.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: how-to
ms.date: 04/10/2020
ms.author: ramamill
ms.openlocfilehash: 19d6933497b469148687dc9c5012513ab7d0314b
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89047210"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>VMware VM 및 물리적 서버의 재해 복구를 위한 Mobility 서비스

[Azure Site Recovery](site-recovery-overview.md)를 사용 하 여 VMware vm (가상 머신) 및 물리적 서버에 대 한 재해 복구를 설정 하는 경우 각 온-프레미스 VMware VM 및 물리적 서버에 Site Recovery 모바일 서비스를 설치 합니다. Mobility 서비스는 머신에 기록된 데이터를 캡처하고 이를 Site Recovery 프로세스 서버에 전달합니다. 모바일 서비스는 다음 방법을 사용 하 여 배포할 수 있는 모바일 서비스 에이전트 소프트웨어에 의해 설치 됩니다.

- [강제 설치](#push-installation): Azure Portal를 통해 보호를 사용 하도록 설정 하면 Site Recovery에서 모바일 서비스를 서버에 설치 합니다.
- 수동 설치: [UI (사용자 인터페이스)](#install-the-mobility-service-using-ui) 또는 [명령 프롬프트](#install-the-mobility-service-using-command-prompt)를 사용 하 여 각 컴퓨터에 모바일 서비스를 수동으로 설치할 수 있습니다.
- [자동 배포](vmware-azure-mobility-install-configuration-mgr.md): Configuration Manager와 같은 소프트웨어 배포 도구를 사용 하 여 모바일 서비스 설치를 자동화할 수 있습니다.

> [!NOTE]
> 모바일 서비스는 VMware Vm 또는 물리적 컴퓨터에 대 한 원본 컴퓨터에서 약 6%-10%의 메모리를 사용 합니다.

## <a name="antivirus-on-replicated-machines"></a>복제 된 컴퓨터의 바이러스 백신

복제 하려는 컴퓨터에서 바이러스 백신 소프트웨어를 실행 중인 경우에는 _C:\ProgramData\ASR\agent_ 에서 모바일 서비스의 설치 폴더를 제외 합니다. 이를 제외 하면 복제가 예상 대로 작동 합니다.

## <a name="push-installation"></a>강제 설치

강제 설치는 [복제를 사용 하도록 설정](vmware-azure-enable-replication.md#enable-replication)하기 위해 Azure Portal에서 실행 되는 작업의 필수적인 부분입니다. 보호 하려는 Vm 집합을 선택 하 고 복제를 사용 하도록 설정 하 고 나면 구성 서버에서 모바일 서비스 에이전트를 서버에 푸시하고, 에이전트를 설치 하 고, 구성 서버에서 에이전트의 등록을 완료 합니다.

### <a name="prerequisites"></a>사전 요구 사항

- 모든 푸시 설치 [필수 구성 요소가](vmware-azure-install-mobility-service.md) 충족 되는지 확인 합니다.
- 모든 서버 구성이 [Azure에 대 한 VMware vm 및 물리적 서버 재해 복구를 위한 지원 매트릭스](vmware-physical-azure-support-matrix.md)의 조건을 충족 하는지 확인 합니다.
- [9.36 버전](https://support.microsoft.com/help/4578241/) 이상, SUSE Linux Enterprise Server 11 s p 4의 경우 [구성 서버 및 스케일 아웃 프로세스 서버에서](#download-latest-mobility-agent-installer-for-suse-11-sp3-server) 최신 설치 관리자를 사용할 수 있는지 확인 합니다.

푸시 설치 워크플로는 다음 섹션에 설명 되어 있습니다.

### <a name="mobility-service-agent-version-923-and-higher"></a>모바일 서비스 에이전트 버전 9.23 이상

버전 9.23에 대 한 자세한 내용은 [Azure Site Recovery 용 업데이트 롤업 35](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery)을 참조 하십시오.

모바일 서비스를 강제 설치 하는 동안 다음 단계가 수행 됩니다.

1. 에이전트가 원본 컴퓨터에 푸시됩니다. 여러 환경 오류로 인해 에이전트를 원본 컴퓨터에 복사 하지 못할 수 있습니다. 푸시 설치 오류 문제를 해결 하려면 [지침](vmware-azure-troubleshoot-push-install.md) 을 참조 하세요.
1. 에이전트가 서버에 성공적으로 복사 된 후 서버에서 필수 구성 요소 확인이 수행 됩니다.
   - 모든 필수 구성 요소가 충족 되 면 설치가 시작 됩니다.
   - 하나 이상의 [필수 구성 요소가](vmware-physical-azure-support-matrix.md) 충족 되지 않으면 설치가 실패 합니다.
1. 에이전트 설치의 일부로 Azure Site Recovery 용 VSS (볼륨 섀도 복사본 서비스) 공급자가 설치 됩니다. VSS 공급자는 응용 프로그램 일치 복구 지점이 생성 되는 데 사용 됩니다. VSS 공급자 설치에 실패 하는 경우이 단계를 건너뛰고 에이전트 설치를 계속 합니다.
1. 에이전트 설치에 성공 했지만 VSS 공급자 설치에 실패 한 경우 작업 상태는 **경고**로 표시 됩니다. 크래시 일치 복구 지점 생성에는 영향을 주지 않습니다.

    - 응용 프로그램 일치 복구 지점이 생성 하려면 Site Recovery VSS 공급자의 수동 설치를 완료 하기 위한 [지침](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine) 을 참조 하세요.
    - 응용 프로그램 일치 복구 지점이 생성 되지 않도록 하려면 [복제 정책을 수정](vmware-azure-set-up-replication.md#create-a-policy) 하 여 응용 프로그램 일치 복구 지점이 해제 되도록 합니다.

### <a name="mobility-service-agent-version-922-and-below"></a>모바일 서비스 에이전트 버전 9.22이 하

1. 에이전트가 원본 컴퓨터에 푸시됩니다. 여러 환경 오류로 인해 에이전트를 원본 컴퓨터에 복사 하지 못할 수 있습니다. 푸시 설치 오류 문제를 해결 하려면 [지침](vmware-azure-troubleshoot-push-install.md) 을 참조 하세요.
1. 에이전트가 서버에 성공적으로 복사 된 후 서버에서 필수 구성 요소 확인이 수행 됩니다.
   - 모든 필수 구성 요소가 충족 되 면 설치가 시작 됩니다.
   - 하나 이상의 [필수 구성 요소가](vmware-physical-azure-support-matrix.md) 충족 되지 않으면 설치가 실패 합니다.

1. 에이전트 설치의 일부로 Azure Site Recovery 용 VSS (볼륨 섀도 복사본 서비스) 공급자가 설치 됩니다. VSS 공급자는 응용 프로그램 일치 복구 지점이 생성 되는 데 사용 됩니다.
   - VSS 공급자 설치에 실패 하는 경우 에이전트가 설치 되지 않습니다. 에이전트 설치 오류를 방지 하려면 [버전 9.23](https://support.microsoft.com/help/4494485/update-rollup-35-for-azure-site-recovery) 이상을 사용 하 여 크래시 일치 복구 지점이 생성 되 고 VSS 공급자의 수동 설치를 수행 합니다.

## <a name="install-the-mobility-service-using-ui"></a>UI를 사용 하 여 모바일 서비스 설치

### <a name="prerequisites"></a>사전 요구 사항

- 모든 서버 구성이 [Azure에 대 한 VMware vm 및 물리적 서버 재해 복구를 위한 지원 매트릭스](vmware-physical-azure-support-matrix.md)의 조건을 충족 하는지 확인 합니다.
- 서버의 운영 체제에 대 한 [설치 관리자를 찾습니다](#locate-installer-files) .

>[!IMPORTANT]
> 한 Azure 지역에서 다른 Azure 지역으로 Azure IaaS (Infrastructure as a Service) VM을 복제 하는 경우 UI 설치 방법을 사용 하지 마세요. [명령 프롬프트](#install-the-mobility-service-using-command-prompt) 설치를 사용 합니다.

1. 머신에 설치 파일을 복사하고 실행합니다.
1. **설치 옵션**에서 **Mobility 서비스 설치**를 선택합니다.
1. 설치 위치를 선택 하 고 **설치**를 선택 합니다.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility1.png" alt-text="모바일 서비스 설치 옵션 페이지.":::

1. **설치 진행률**에서 설치를 모니터링합니다. 설치가 완료되면 **구성으로 계속 진행**을 선택하여 Mobility 서비스를 구성 서버에 등록합니다.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility3.png" alt-text="모바일 서비스 등록 페이지.":::

1. **구성 서버 세부 정보**에서 구성 된 IP 주소 및 암호를 지정 합니다.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility4.png" alt-text="모바일 서비스 등록 페이지.":::

1. **등록**을 선택하여 등록을 완료합니다.

    :::image type="content" source="./media/vmware-physical-mobility-service-install-manual/mobility5.png" alt-text="모바일 서비스 등록 마지막 페이지입니다.":::

## <a name="install-the-mobility-service-using-command-prompt"></a>명령 프롬프트를 사용 하 여 모바일 서비스 설치

### <a name="prerequisites"></a>사전 요구 사항

- 모든 서버 구성이 [Azure에 대 한 VMware vm 및 물리적 서버 재해 복구를 위한 지원 매트릭스](vmware-physical-azure-support-matrix.md)의 조건을 충족 하는지 확인 합니다.
- 서버의 운영 체제에 대 한 [설치 관리자를 찾습니다](#locate-installer-files) .

### <a name="windows-machine"></a>Windows 컴퓨터

- 명령 프롬프트에서 다음 명령을 실행 하 여 보호 하려는 서버의 로컬 폴더 (예: _C:\Temp_)에 설치 관리자를 복사 합니다. 설치 관리자의 파일 이름을 실제 파일 이름으로 바꿉니다.

  ```cmd
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  ```

- 이 명령을 실행 하 여 에이전트를 설치 합니다.

  ```cmd
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```

- 다음 명령을 실행 하 여 에이전트를 구성 서버에 등록 합니다.

  ```cmd
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="installation-settings"></a>설치 설정

Setting | 세부 정보
--- | ---
구문 | `UnifiedAgent.exe /Role \<MS/MT> /InstallLocation \<Install Location> /Platform "VmWare" /Silent`
설치 로그 | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log`
`/Role` | 필수 설치 매개 변수입니다. MS(Mobility 서비스) 또는 MT(마스터 대상) 설치 여부를 지정합니다.
`/InstallLocation`| 선택적 매개 변수. Mobility 서비스 설치 위치(모든 폴더)를 지정합니다.
`/Platform` | 필수. 모바일 서비스가 설치 되는 플랫폼을 지정 합니다. <br/> Vmware v m/물리적 서버용 **vmware** <br/> Azure Vm 용 **azure** .<br/><br/> Azure Vm을 물리적 컴퓨터로 처리 하는 경우 **VMware**를 지정 합니다.
`/Silent`| 선택 사항입니다. 자동 모드에서의 설치 관리자 실행 여부를 지정합니다.

#### <a name="registration-settings"></a>등록 설정

Setting | 세부 정보
--- | ---
구문 | `UnifiedAgentConfigurator.exe  /CSEndPoint \<CSIP> /PassphraseFilePath \<PassphraseFilePath>`
에이전트 구성 로그 | `%ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log`
`/CSEndPoint` | 필수 매개 변수입니다. `<CSIP>` 구성 서버의 IP 주소를 지정 합니다. 유효한 IP 주소를 사용합니다.
`/PassphraseFilePath` |  필수. 암호의 위치입니다. 유효한 UNC 또는 로컬 파일 경로를 사용합니다.

### <a name="linux-machine"></a>Linux 컴퓨터

1. 터미널 세션에서 보호 하려는 서버에 있는 _/tmp_ 와 같은 로컬 폴더에 설치 관리자를 복사 합니다. 설치 관리자의 파일 이름을 Linux 배포의 실제 파일 이름으로 바꾸고 명령을 실행 합니다.

   ```shell
   cd /tmp ;
   tar -xvf Microsoft-ASR_UA_version_LinuxVersion_GA_date_release.tar.gz
   ```

2. 다음과 같이 설치합니다.

   ```shell
   sudo ./install -d <Install Location> -r MS -v VmWare -q
   ```

3. 설치가 완료 되 면 모바일 서비스를 구성 서버에 등록 해야 합니다. 다음 명령을 실행 하 여 모바일 서비스를 구성 서버에 등록 합니다.

   ```shell
   /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
   ```

#### <a name="installation-settings"></a>설치 설정

Setting | 세부 정보
--- | ---
구문 | `./install -d \<Install Location> -r \<MS/MT> -v VmWare -q`
`-r` | 필수 설치 매개 변수입니다. MS(Mobility 서비스) 또는 MT(마스터 대상) 설치 여부를 지정합니다.
`-d` | 선택적 매개 변수. 모바일 서비스 설치 위치를 지정 합니다 `/usr/local/ASR` .
`-v` | 필수. 모바일 서비스가 설치 되는 플랫폼을 지정 합니다. <br/> Vmware v m/물리적 서버용 **vmware** <br/> Azure Vm 용 **azure** .
`-q` | 선택 사항입니다. 자동 모드에서의 설치 관리자 실행 여부를 지정합니다.

#### <a name="registration-settings"></a>등록 설정

Setting | 세부 정보
--- | ---
구문 | `cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP> -P \<PassphraseFilePath>`
`-i` | 필수 매개 변수입니다. `<CSIP>` 구성 서버의 IP 주소를 지정 합니다. 유효한 IP 주소를 사용합니다.
`-P` |  필수. 암호가 저장되는 파일의 전체 파일 경로입니다. 유효한 폴더를 사용합니다.

## <a name="azure-virtual-machine-agent"></a>Azure Virtual Machine 에이전트

- **Windows VM**: Mobility 서비스 버전 9.7.0.0부터 [Azure VM 에이전트](../virtual-machines/extensions/features-windows.md#azure-vm-agent)를 Mobility 서비스 설치 관리자가 설치합니다. 이렇게 하면 컴퓨터가 Azure로 장애 조치 (failover) 되 면 Azure VM은 VM 확장을 사용 하기 위한 에이전트 설치 필수 구성 요소를 충족 합니다.
- **Linux VM**: 장애 조치(failover) 후 [WALinuxAgent](../virtual-machines/extensions/update-linux-agent.md)가 Azure VM에 수동으로 설치되어야 합니다.

## <a name="locate-installer-files"></a>설치 관리자 파일 찾기

구성 서버에서 _%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository_폴더로 이동 합니다. 운영 체제에 따라 필요한 설치 관리자를 확인 합니다. 다음 표에는 각 VMware VM 및 물리적 서버 운영 체제에 대 한 설치 관리자 파일이 요약 되어 있습니다. 시작 하기 전에 [지원 되는 운영 체제](vmware-physical-azure-support-matrix.md#replicated-machines)를 검토할 수 있습니다.

> [!NOTE]
> 파일 이름에는 다음 표에 나와 있는 구문을 사용 하 여 _버전_ 및 _날짜_ 를 실제 값에 대 한 자리 표시자로 사용 합니다. 실제 파일 이름은 다음 예제와 유사 하 게 표시 됩니다.
> - `Microsoft-ASR_UA_9.30.0.0_Windows_GA_22Oct2019_release.exe`
> - `Microsoft-ASR_UA_9.30.0.0_UBUNTU-16.04-64_GA_22Oct2019_release.tar.gz`

설치 관리자 파일 | 운영 체제(64비트만 해당)
--- | ---
`Microsoft-ASR_UA_version_Windows_GA_date_release.exe` | Windows Server 2016 </br> Windows Server 2012 R2 </br> Windows Server 2012 </br> Windows Server 2008 R2 SP1
`Microsoft-ASR_UA_version_RHEL6-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux (RHEL) 6 </br> CentOS 6
`Microsoft-ASR_UA_version_RHEL7-64_GA_date_release.tar.gz` | Red Hat Enterprise Linux(RHEL) 7 </br> CentOS 7
`Microsoft-ASR_UA_version_SLES12-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 12 SP1 </br> SP2 및 SP3을 포함 합니다.
[다운로드 하 여이 폴더에 수동으로 저장](#download-latest-mobility-agent-installer-for-suse-11-sp3-server)합니다. | SUSE Linux Enterprise Server 11 SP3
`Microsoft-ASR_UA_version_SLES11-SP4-64_GA_date_release.tar.gz` | SUSE Linux Enterprise Server 11 SP4
`Microsoft-ASR_UA_version_OL6-64_GA_date_release.tar.gz` | Oracle Enterprise Linux 6.4 </br> Oracle Enterprise Linux 6.5
`Microsoft-ASR_UA_version_UBUNTU-14.04-64_GA_date_release.tar.gz` | Ubuntu Linux 14.04
`Microsoft-ASR_UA_version_UBUNTU-16.04-64_GA_date_release.tar.gz` | Ubuntu Linux 16.04 LTS 서버
`Microsoft-ASR_UA_version_DEBIAN7-64_GA_date_release.tar.gz` | Debian 7
`Microsoft-ASR_UA_version_DEBIAN8-64_GA_date_release.tar.gz` | Debian 8

### <a name="download-latest-mobility-agent-installer-for-suse-11-sp3-server"></a>SUSE 11 SP3 서버용 최신 모바일 에이전트 설치 관리자 다운로드

[9.36 버전](https://support.microsoft.com/help/4578241/) 이상에서 **SUSE Linux Enterprise Server 11 SP3 컴퓨터를 업데이트 하거나 보호 하기 위한 필수 구성 요소입니다** .

1. 최신 모바일 에이전트 설치 관리자가 Microsoft 다운로드 센터에서 다운로드 되 고 구성 서버 및 모든 규모 확장 프로세스 서버에서 푸시 설치 관리자 리포지토리에 배치 되었는지 확인 합니다.
2. SUSE Linux Enterprise Server 11 SP3 에이전트 설치 관리자를 [다운로드](https://download.microsoft.com/download/0/3/4/0341b388-1ff5-4ead-b197-7cf6d2bb3e40/Microsoft-ASR_UA_9.36.0.0_SLES11-SP3-64_GA_06Aug2020_release.tar.gz) 합니다.
3. 구성 서버로 이동 하 고 다음 경로에 SUSE Linux Enterprise Server 11 SP3 에이전트 설치 관리자를 복사 합니다.
    1. INSTALL_DIR \home\svsystems\pushinstallsvc\repository 아래
    1.  INSTALL_DIR \home\svsystems\admin\web\sw 폴더
4. 이제 연결 된 확장 프로세스 서버로 이동 하 여 세 번째 단계에서 언급 한 두 경로에 설치 관리자를 복사 & 합니다.
5. **예**를 들어 설치 경로가 C:\Program files (x86) \Microsoft Azure Site Recovery 경우 위에 언급 된 디렉터리는
    1. C:\Program Files (x86) \Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository
    1. C:\Program Files (x86) \Microsoft Azure Site Recovery\home\svsystems\admin\web\sw path

## <a name="next-steps"></a>다음 단계

[Mobility 서비스에 대한 푸시 설치를 설정합니다](vmware-azure-install-mobility-service.md).
