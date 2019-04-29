---
title: System Center Configuration Manager를 사용하여 Azure로 VMware VM 및 물리적 서버의 재해 복구를 위한 Azure Site Recovery 모바일 서비스 설치 자동화 | Microsoft Docs
description: 이 문서는 Site Recovery를 사용하여 VMware VM 및 물리적 서버를 Azure로 재해 복구하기 위해 System Center Configuration Manager를 사용하여 모바일 서비스 설치를 자동화하는 데 도움이 됩니다.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/14/2019
ms.author: ramamill
ms.openlocfilehash: 35c317c4b73e9a22e3b0d6192abcfc2a596066b8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60598270"
---
# <a name="automate-mobility-service-installation-with-system-center-configuration-manager"></a>System Center Configuration Manager를 사용하여 모바일 서비스 설치 자동화

모바일 서비스는 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 Azure에 복제하려는 VMware VM 및 물리적 서버에 설치됩니다.

이 문서에서는 System Center Configuration Manager를 사용하여 VMware VM에서 Azure Site Recovery 모바일 서비스를 배포하는 방법의 예제를 제공합니다. Configuration Manager 같은 소프트웨어 배포 도구를 사용하면 다음과 같은 장점이 있습니다.

* 소프트웨어 업데이트에 대한 계획된 유지 관리 시간에 새로 설치 및 업그레이드 예약
* 동시에 수백 대의 서버에 대규모로 배포

이 문서에서는 System Center Configuration Manager 2012 R2를 사용하여 배포 작업을 설명합니다. **9.9.4510.1** 버전 이상의 모바일 서비스를 사용하는 것으로 가정합니다.

또는 사용 하 여 모바일 서비스 설치를 자동화할 수 있습니다 [Azure Automation DSC](vmware-azure-mobility-deploy-automation-dsc.md)합니다.

## <a name="prerequisites"></a>필수 조건

1. Configuration Manager와 같은 소프트웨어 배포 도구가 사용자 환경에 이미 배포되어 있어야 합니다.
2. Site Recovery를 사용하여 보호하려는 모든 **Windows 서버**와 모든 **Linux 서버**에 각각 하나씩, 총 두 개의 [디바이스 컬렉션](https://technet.microsoft.com/library/gg682169.aspx)을 만들어야 합니다.
3. Recovery Services 자격 증명 모음에 이미 구성 서버가 등록되어 있어야 합니다.
4. Configuration Manager 컴퓨터가 액세스할 수 있는 보안 네트워크 파일 공유(SMB 공유)가 있어야 합니다.

## <a name="deploy-on-windows-machines"></a>Windows 컴퓨터에 배포
> [!NOTE]
> 이 문서에서는 구성 서버의 IP 주소가 192.168.3.121이고 보안 네트워크 파일 공유가 \\\ContosoSecureFS\MobilityServiceInstallers인 것으로 가정합니다.

### <a name="prepare-for-deployment"></a>배포 준비
1. 네트워크 공유에 폴더를 만들고 이름을 **MobSvcWindows**로 지정합니다.
2. 구성 서버에 로그인하고 관리자 권한 명령 프롬프트를 엽니다.
3. 다음 명령을 실행하여 암호 파일을 생성합니다.

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. 네트워크 공유에서 **MobSvc.passphrase** 파일을 **MobSvcWindows** 폴더로 복사합니다.
5. 다음 명령을 실행하여 구성 서버의 설치 관리자 리포지토리를 찾아봅니다.

   `cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

6. 네트워크 공유에서 **Microsoft-ASR\_UA\_*version*\_Windows\_GA\_*date*\_Release.exe**를 **MobSvcWindows** 폴더로 복사합니다.
7. 다음 코드를 복사하여 **MobSvcWindows** 폴더에 **install.bat**으로 저장합니다.

   > [!NOTE]
   > 이 스크립트의 [CSIP] 자리 표시자를 구성 서버 IP 주소의 실제 값으로 바꿉니다.

```DOS
Time /t >> C:\Temp\logfile.log
REM ==================================================
REM ==== Clean up the folders ========================
RMDIR /S /q %temp%\MobSvc
MKDIR %Temp%\MobSvc
MKDIR C:\Temp
REM ==================================================

REM ==== Copy new files ==============================
COPY M*.* %Temp%\MobSvc
CD %Temp%\MobSvc
REN Micro*.exe MobSvcInstaller.exe
REM ==================================================

REM ==== Extract the installer =======================
MobSvcInstaller.exe /q /x:%Temp%\MobSvc\Extracted
REM ==== Wait 10s for extraction to complete =========
TIMEOUT /t 10
REM =================================================

REM ==== Perform installation =======================
REM =================================================

CD %Temp%\MobSvc\Extracted
whoami >> C:\Temp\logfile.log
SET PRODKEY=HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall
REG QUERY %PRODKEY%\{275197FC-14FD-4560-A5EB-38217F80CBD1}
IF NOT %ERRORLEVEL% EQU 0 (
    echo "Product is not installed. Goto INSTALL." >> C:\Temp\logfile.log
    GOTO :INSTALL
) ELSE (
    echo "Product is installed." >> C:\Temp\logfile.log

    echo "Checking for Post-install action status." >> C:\Temp\logfile.log
    GOTO :POSTINSTALLCHECK
)

:POSTINSTALLCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "PostInstallActions" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Post-install actions succeeded. Checking for Configuration status." >> C:\Temp\logfile.log
        GOTO :CONFIGURATIONCHECK
    ) ELSE (
        echo "Post-install actions didn't succeed. Goto INSTALL." >> C:\Temp\logfile.log
        GOTO :INSTALL
    )

:CONFIGURATIONCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "AgentConfigurationStatus" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded. Goto UPGRADE." >> C:\Temp\logfile.log
        GOTO :UPGRADE
    ) ELSE (
        echo "Configuration didn't succeed. Goto CONFIGURE." >> C:\Temp\logfile.log
        GOTO :CONFIGURE
    )


:INSTALL
    echo "Perform installation." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Role MS /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Installation has succeeded." >> C:\Temp\logfile.log
        (GOTO :CONFIGURE)
    ) ELSE (
        echo "Installation has failed." >> C:\Temp\logfile.log
        GOTO :ENDSCRIPT
    )

:CONFIGURE
    echo "Perform configuration." >> C:\Temp\logfile.log
    cd "C:\Program Files (x86)\Microsoft Azure Site Recovery\agent"
    UnifiedAgentConfigurator.exe  /CSEndPoint "[CSIP]" /PassphraseFilePath %Temp%\MobSvc\MobSvc.passphrase
    IF %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Configuration has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:UPGRADE
    echo "Perform upgrade." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Upgrade has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Upgrade has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:ENDSCRIPT
    echo "End of script." >> C:\Temp\logfile.log


```

### <a name="create-a-package"></a>패키지 만들기

1. Configuration Manager 콘솔에 로그인합니다.
2. **소프트웨어 라이브러리** > **응용 프로그램 관리** > **패키지**로 이동합니다.
3. **패키지**를 마우스 오른쪽 단추로 클릭하고 **패키지 만들기**를 선택합니다.
4. 이름, 설명, 제조업체, 언어, 버전에 대한 값을 제공합니다.
5. **이 패키지는 원본 파일을 포함합니다** 확인란을 선택합니다.
6. **찾아보기**를 클릭하고 설치 관리자가 저장된 네트워크 공유를 선택합니다(\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcWindows).

   ![패키지 및 프로그램 만들기 마법사의 스크린샷](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

7. **만들려는 프로그램 유형 선택** 페이지에서 **표준 프로그램**을 선택하고 **다음**을 클릭합니다.

   ![패키지 및 프로그램 만들기 마법사의 스크린샷](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. **이 표준 프로그램에 대한 정보 지정** 페이지에서 다음 입력을 제공하고 **다음**을 클릭합니다. (다른 입력은 해당 기본값을 사용할 수 있습니다.)

   | **매개 변수 이름** | **값** |
   |--|--|
   | 이름 | Microsoft Azure Mobility Service(Windows) 설치 |
   | 명령 줄 | install.bat |
   | 프로그램을 실행할 수 있습니다. | 사용자 로그온 여부 |

   ![패키지 및 프로그램 만들기 마법사의 스크린샷](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

9. 다음 페이지에서 대상 운영 체제를 선택합니다. 모바일 서비스는 Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2에만 설치할 수 있습니다.

   ![패키지 및 프로그램 만들기 마법사의 스크린샷](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-page2.png)

10. 마법사를 완료하려면 **다음**을 두 번 클릭합니다.


> [!NOTE]
> 스크립트는 모바일 서비스 에이전트의 새로 설치 및 이미 설치된 에이전트의 업데이트를 모두 지원합니다.

### <a name="deploy-the-package"></a>패키지 배포
1. Configuration Manager 콘솔에서 패키지를 마우스 오른쪽 단추로 클릭하고 **콘텐츠 배포**를 선택합니다.
   ![Configuration Manager 콘솔의 스크린샷](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)
2. 패키지를 복사할 위치에 **[배포 지점](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** 을 선택합니다.
3. 마법사를 완료합니다. 그러면 패키지가 지정된 배포 지점에 복제를 시작합니다.
4. 패키지 배포 작업이 완료되면 패키지를 마우스 오른쪽 단추로 클릭하고 **배포**를 선택합니다.
   ![Configuration Manager 콘솔의 스크린샷](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)
5. 필수 구성 요소 섹션에서 만든 Widows Server 디바이스 컬렉션을 배포의 대상 컬렉션으로 선택합니다.

   ![소프트웨어 배포 마법사의 스크린샷](./media/vmware-azure-mobility-install-configuration-mgr/sccm-select-target-collection.png)

6. **콘텐츠 대상 지정** 페이지에서 **배포 지점**을 선택합니다.
7. **이 소프트웨어를 배포하는 방법을 제어하는 설정 지정** 페이지에서 목적이 **필수**인지 확인합니다.

   ![소프트웨어 배포 마법사의 스크린샷](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. **이 배포에 대한 일정 지정** 페이지에서 일정을 지정합니다. 자세한 내용은 [패키지 일정 예약](https://technet.microsoft.com/library/gg682178.aspx)을 참조하세요.
9. **배포 지점** 페이지에서 데이터 센터 요구 사항에 따라 속성을 구성합니다. 그런 다음 마법사를 완료합니다.

> [!TIP]
> 불필요한 재부팅을 방지하려면 매월 유지 관리 시간 또는 소프트웨어 업데이트 시간에 패키지 설치를 예약합니다.

Configuration Manager 콘솔을 사용하여 배포 진행률을 모니터링할 수 있습니다. **모니터링** > **배포** > *[패키지 이름]* 으로 이동합니다.

  ![배포를 모니터링하는 Configuration Manager 옵션의 스크린샷](./media/vmware-azure-mobility-install-configuration-mgr/report.PNG)

## <a name="deploy-on-linux-machines"></a>Linux 컴퓨터에 배포
> [!NOTE]
> 이 문서에서는 구성 서버의 IP 주소가 192.168.3.121이고 보안 네트워크 파일 공유가 \\\ContosoSecureFS\MobilityServiceInstallers인 것으로 가정합니다.

### <a name="prepare-for-deployment"></a>배포 준비
1. 네트워크 공유에 폴더를 만들고 이름을 **MobSvcLinux**로 지정합니다.
2. 구성 서버에 로그인하고 관리자 권한 명령 프롬프트를 엽니다.
3. 다음 명령을 실행하여 암호 파일을 생성합니다.

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. 네트워크 공유에서 **MobSvc.passphrase** 파일을 **MobSvcLinux** 폴더로 복사합니다.
5. 다음 명령을 실행하여 구성 서버에서 설치 관리자 리포지토리를 찾아봅니다.

   `cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

6. 네트워크 공유에서 다음 파일을 **MobSvcLinux** 폴더로 복사합니다.
   * Microsoft-ASR\_UA\*RHEL6-64*release.tar.gz
   * Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz
   * Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz
   * Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz
   * Microsoft-ASR\_UA\*OL6-64\*release.tar.gz
   * Microsoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz


7. 아래 코드를 복사하여 **MobSvcLinux** 폴더에 **install_linux.sh**로 저장합니다.
   > [!NOTE]
   > 이 스크립트의 [CSIP] 자리 표시자를 구성 서버 IP 주소의 실제 값으로 바꿉니다.

```Bash
#!/usr/bin/env bash

rm -rf /tmp/MobSvc
mkdir -p /tmp/MobSvc
INSTALL_DIR='/usr/local/ASR'
VX_VERSION_FILE='/usr/local/.vx_version'

echo "=============================" >> /tmp/MobSvc/sccm.log
echo `date` >> /tmp/MobSvc/sccm.log
echo "=============================" >> /tmp/MobSvc/sccm.log

if [ -f /etc/oracle-release ] && [ -f /etc/redhat-release ]; then
    if grep -q 'Oracle Linux Server release 6.*' /etc/oracle-release; then
        if uname -a | grep -q x86_64; then
            OS="OL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *OL6*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/redhat-release ]; then
    if grep -q 'Red Hat Enterprise Linux Server release 6.* (Santiago)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 6.* (Final)' /etc/redhat-release || \
        grep -q 'CentOS release 6.* (Final)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL6*.tar.gz /tmp/MobSvc
        fi
    elif grep -q 'Red Hat Enterprise Linux Server release 7.* (Maipo)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 7.* (Core)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL7-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL7*.tar.gz /tmp/MobSvc
                fi
    fi
elif [ -f /etc/SuSE-release ] && grep -q 'VERSION = 11' /etc/SuSE-release; then
    if grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 3' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP3-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP3*.tar.gz /tmp/MobSvc
        fi
    elif grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 4' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP4-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP4*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/lsb-release ] ; then
    if grep -q 'DISTRIB_RELEASE=14.04' /etc/lsb-release ; then
       if uname -a | grep -q x86_64; then
           OS="UBUNTU-14.04-64"
           echo $OS >> /tmp/MobSvc/sccm.log
           cp *UBUNTU-14*.tar.gz /tmp/MobSvc
       fi
    fi
else
    exit 1
fi

if [ -z "$OS" ]; then
    exit 1
fi

Install()
{
    echo "Perform Installation." >> /tmp/MobSvc/sccm.log
    ./install -q -d ${INSTALL_DIR} -r MS -v VmWare
    RET_VAL=$?
    echo "Installation Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Installation has succeeded. Proceed to configuration." >> /tmp/MobSvc/sccm.log
        Configure
    else
        echo "Installation has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Configure()
{
    echo "Perform configuration." >> /tmp/MobSvc/sccm.log
    ${INSTALL_DIR}/Vx/bin/UnifiedAgentConfigurator.sh -i [CSIP] -P MobSvc.passphrase
    RET_VAL=$?
    echo "Configuration Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Configuration has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Configuration has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Upgrade()
{
    echo "Perform Upgrade." >> /tmp/MobSvc/sccm.log
    ./install -q -v VmWare
    RET_VAL=$?
    echo "Upgrade Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Upgrade has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Upgrade has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

cp MobSvc.passphrase /tmp/MobSvc
cd /tmp/MobSvc

tar -zxvf *.tar.gz

if [ -e ${VX_VERSION_FILE} ]; then
    echo "${VX_VERSION_FILE} exists. Checking for configuration status." >> /tmp/MobSvc/sccm.log
    agent_configuration=$(grep ^AGENT_CONFIGURATION_STATUS "${VX_VERSION_FILE}" | cut -d"=" -f2 | tr -d " ")
    echo "agent_configuration=$agent_configuration" >> /tmp/MobSvc/sccm.log
     if [ "$agent_configuration" == "Succeeded" ]; then
        echo "Agent is already configured. Proceed to Upgrade." >> /tmp/MobSvc/sccm.log
        Upgrade
    else
        echo "Agent is not configured. Proceed to Configure." >> /tmp/MobSvc/sccm.log
        Configure
    fi
else
    Install
fi

cd /tmp

```

### <a name="create-a-package"></a>패키지 만들기

1. Configuration Manager 콘솔에 로그인합니다.
2. **소프트웨어 라이브러리** > **응용 프로그램 관리** > **패키지**로 이동합니다.
3. **패키지**를 마우스 오른쪽 단추로 클릭하고 **패키지 만들기**를 선택합니다.
4. 이름, 설명, 제조업체, 언어, 버전에 대한 값을 제공합니다.
5. **이 패키지는 원본 파일을 포함합니다** 확인란을 선택합니다.
6. **찾아보기**를 클릭하고 설치 관리자가 저장된 네트워크 공유를 선택합니다(\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcLinux).

   ![패키지 및 프로그램 만들기 마법사의 스크린샷](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package-linux.png)

7. **만들려는 프로그램 유형 선택** 페이지에서 **표준 프로그램**을 선택하고 **다음**을 클릭합니다.

   ![패키지 및 프로그램 만들기 마법사의 스크린샷](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. **이 표준 프로그램에 대한 정보 지정** 페이지에서 다음 입력을 제공하고 **다음**을 클릭합니다. (다른 입력은 해당 기본값을 사용할 수 있습니다.)

    | **매개 변수 이름** | **값** |
   |--|--|
   | 이름 | Microsoft Azure Mobility Service(Linux) 설치 |
   | 명령 줄 | ./install_linux.sh |
   | 프로그램을 실행할 수 있습니다. | 사용자 로그온 여부 |

   ![패키지 및 프로그램 만들기 마법사의 스크린샷](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-linux.png)

9. 다음 페이지에서 **모든 플랫폼에서 이 프로그램 실행**을 선택합니다.
   ![패키지 및 프로그램 만들기 마법사의 스크린샷](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-page2-linux.png)

10. 마법사를 완료하려면 **다음**을 두 번 클릭합니다.

> [!NOTE]
> 스크립트는 모바일 서비스 에이전트의 새로 설치 및 이미 설치된 에이전트의 업데이트를 모두 지원합니다.

### <a name="deploy-the-package"></a>패키지 배포
1. Configuration Manager 콘솔에서 패키지를 마우스 오른쪽 단추로 클릭하고 **콘텐츠 배포**를 선택합니다.
   ![Configuration Manager 콘솔의 스크린샷](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)
2. 패키지를 복사할 위치에 **[배포 지점](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** 을 선택합니다.
3. 마법사를 완료합니다. 그러면 패키지가 지정된 배포 지점에 복제를 시작합니다.
4. 패키지 배포 작업이 완료되면 패키지를 마우스 오른쪽 단추로 클릭하고 **배포**를 선택합니다.
   ![Configuration Manager 콘솔의 스크린샷](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)
5. 전제 조건 섹션에서 만든 Linux Server 디바이스 컬렉션을 배포의 대상 컬렉션으로 선택합니다.

   ![소프트웨어 배포 마법사의 스크린샷](./media/vmware-azure-mobility-install-configuration-mgr/sccm-select-target-collection-linux.png)

6. **콘텐츠 대상 지정** 페이지에서 **배포 지점**을 선택합니다.
7. **이 소프트웨어를 배포하는 방법을 제어하는 설정 지정** 페이지에서 목적이 **필수**인지 확인합니다.

   ![소프트웨어 배포 마법사의 스크린샷](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. **이 배포에 대한 일정 지정** 페이지에서 일정을 지정합니다. 자세한 내용은 [패키지 일정 예약](https://technet.microsoft.com/library/gg682178.aspx)을 참조하세요.
9. **배포 지점** 페이지에서 데이터 센터 요구 사항에 따라 속성을 구성합니다. 그런 다음 마법사를 완료합니다.

사용자가 구성한 일정에 따라 Linux Server 디바이스 컬렉션에 모바일 서비스가 설치됩니다.


## <a name="uninstall-the-mobility-service"></a>모바일 서비스 설치 제거
모바일 서비스를 제거하는 Configuration Manager 패키지를 만들 수 있습니다. 이렇게 하려면 다음 스크립트를 사용합니다.

```
Time /t >> C:\logfile.log
REM ==================================================
REM ==== Check if Mob Svc is already installed =======
REM ==== If not installed no operation required ========
REM ==== Else run uninstall command =====================
REM ==== {275197FC-14FD-4560-A5EB-38217F80CBD1} is ====
REM ==== guid for Mob Svc Installer ====================
whoami >> C:\logfile.log
NET START | FIND "InMage Scout Application Service"
IF  %ERRORLEVEL% EQU 1 (GOTO :INSTALL) ELSE GOTO :UNINSTALL
:NOOPERATION
                echo "No Operation Required." >> c:\logfile.log
                GOTO :ENDSCRIPT
:UNINSTALL
                echo "Uninstall" >> C:\logfile.log
                MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
:ENDSCRIPT

```

## <a name="next-steps"></a>다음 단계
이제 가상 머신의 [보호를 활성화](vmware-azure-enable-replication.md)할 준비가 되었습니다.
