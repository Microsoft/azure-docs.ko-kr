---
title: VMware VM 및 물리적 서버를 Azure로 재해 복구하기 위해 푸시 설치를 통해 Mobility Service를 설치하도록 원본 머신 준비 | Microsoft Docs
description: Azure Site Recovery 서비스를 사용하여 VMware VM 및 물리적 서버를 Azure로 재해 복구하기 위해 푸시 설치를 통해 모바일 에이전트를 설치하도록 서버를 준비하는 방법을 알아봅니다.
services: site-recovery
author: Sharmistha-Rai
manager: gaggupta
ms.service: site-recovery
ms.topic: conceptual
ms.author: sharrai
ms.date: 05/27/2021
ms.openlocfilehash: c48fe1f4c6d871506235d5bea743226bc1184fee
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110576916"
---
# <a name="prepare-source-machine-for-push-installation-of-mobility-agent"></a>모바일 에이전트를 푸시 설치하도록 원본 머신 준비

[Azure Site Recovery](site-recovery-overview.md)를 사용하여 VMware VM 및 물리적 서버에 대한 재해 복구를 설정할 경우 각 온-프레미스 VMware VM 및 물리적 서버에 [Site Recovery Mobility 서비스](vmware-physical-mobility-service-overview.md)를 설치합니다.  Mobility 서비스는 머신에 기록된 데이터를 캡처하고 이를 Site Recovery 프로세스 서버에 전달합니다.

## <a name="install-on-windows-machine"></a>Windows 머신에 설치

보호하려는 각 Windows 머신에서 다음을 수행합니다.

1. 머신과 프로세스 서버 간에 네트워크가 연결되어 있는지 확인합니다. 별도의 프로세스 서버를 설정하지 않은 경우에는 기본적으로 구성 서버에서 실행 중입니다.
1. 프로세스 서버가 컴퓨터에 액세스하는 데 사용할 수 있는 계정을 작성합니다. 계정에는 관리자 권한(로컬 또는 도메인)이 있어야 합니다. 강제 설치 및 에이전트 업데이트의 경우에만 이 계정을 사용합니다.
2. 도메인 계정을 사용하지 않는 경우 다음과 같이 로컬 컴퓨터에서 원격 사용자 액세스 제어를 사용하지 않도록 설정합니다.
    - HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System 레지스트리 키에서 새 DWORD: **LocalAccountTokenFilterPolicy** 를 추가합니다. 값을 **1** 로 설정합니다.
    -  명령 프롬프트에서 이렇게 하려면 다음 명령을 실행합니다.
    
       ```
       REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1 /f
       ```

3. 보호하려는 머신의 Windows 방화벽에서 **방화벽을 통해 앱 또는 기능 허용** 을 선택합니다. **파일 및 프린터 공유** 와 **WMI(Windows Management Instrumentation)** 를 사용하도록 설정합니다. 도메인에 속하는 컴퓨터의 경우 GPO(그룹 정책 개체)를 사용하여 방화벽 설정을 구성할 수 있습니다.

   ![방화벽 설정](./media/vmware-azure-install-mobility-service/mobility1.png)

4. CSPSConfigtool에서 만든 계정을 추가합니다. 이 작업을 수행하려면 구성 서버에 로그인합니다.
5. **cspsconfigtool.exe** 를 엽니다. 바탕 화면에 바로 가기로 제공되며 %ProgramData%\ASR\home\svsystems\bin 폴더에서도 사용할 수 있습니다.
6. **계정 관리** 탭에서 **계정 추가** 를 선택합니다.
7. 만든 계정을 추가합니다.
8. 컴퓨터에서 복제를 사용할 때 사용할 자격 증명을 입력합니다.

## <a name="install-on-linux-machine"></a>Linux 머신에 설치

보호하려는 각 Linux 머신에서 다음을 수행합니다.

1. Linux 머신과 프로세스 서버 간에 네트워크가 연결되어 있는지 확인합니다.
2. 프로세스 서버가 컴퓨터에 액세스하는 데 사용할 수 있는 계정을 작성합니다. 계정은 원본 Linux 서버의 **루트** 사용자여야 합니다. 푸시 설치 및 업데이트의 경우에만 이 계정을 사용합니다.
3. 원본 Linux 서버의 /etc/hosts 파일에 로컬 호스트 이름을 모든 네트워크 어댑터와 연결된 IP 주소에 매핑하는 항목이 있는지 확인합니다.
4. 복제하려는 컴퓨터에 최신 openssh, openssh-server 및 openssl 패키지를 설치합니다.
5. SSH(보안 셸)가 22 포트에서 사용되고 실행 중인지 확인합니다.
4. sshd_config 파일에서 SFTP 하위 시스템 및 암호 인증을 사용하도록 설정합니다. 이 작업을 수행하려면 **루트** 로 로그인합니다.
5. **/etc/ssh/sshd_config** 파일에서 **PasswordAuthentication** 으로 시작하는 줄을 찾습니다.
6. 줄의 주석 처리를 제거하고 값을 **예** 로 변경합니다.
7. **Subsystem** 으로 시작하는 줄을 찾아서 주석 처리를 제거합니다.

      ![Linux](./media/vmware-azure-install-mobility-service/mobility2.png)

8. **sshd** 서비스를 다시 시작합니다.
9. CSPSConfigtool에서 만든 계정을 추가합니다. 이 작업을 수행하려면 구성 서버에 로그인합니다.
10. **cspsconfigtool.exe** 를 엽니다. 바탕 화면에서 바로 가기로 사용할 수 있으며, %ProgramData%\home\svsystems\bin 폴더에 있습니다.
11. **계정 관리** 탭에서 **계정 추가** 를 선택합니다.
12. 만든 계정을 추가합니다.
13. 컴퓨터에서 복제를 사용할 때 사용할 자격 증명을 입력합니다.
1. SUSE Linux Enterprise Server 11 SP3, RHEL 5, CentOS 5 또는 Debian 7 머신을 업데이트하거나 보호하기 위한 추가 단계입니다. [구성 서버에서 최신 버전을 사용할 수 있는지 확인](vmware-physical-mobility-service-overview.md#download-latest-mobility-agent-installer-for-suse-11-sp3-rhel-5-debian-7-server)합니다.

## <a name="anti-virus-on-replicated-machines"></a>복제된 머신에서 바이러스 백신

복제하려는 머신에 활성 바이러스 백신 소프트웨어가 실행 중이면 바이러스 백신 작업에서 Mobility 서비스 설치 폴더를 제외해야 합니다(*C:\ProgramData\ASR\agent*). 이렇게 하면 복제가 예상대로 작동합니다.

## <a name="next-steps"></a>다음 단계

Mobility Service를 설치한 후 Azure Portal에서 **+복제** 를 선택하여 이러한 VM 보호를 시작합니다. [VMware VM](vmware-azure-enable-replication.md) 및 [물리적 서버](physical-azure-disaster-recovery.md#enable-replication)에 대해 복제를 사용하도록 설정하는 방법을 자세히 알아봅니다.


