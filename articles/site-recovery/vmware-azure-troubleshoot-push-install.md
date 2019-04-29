---
title: 재해 복구에 대한 복제를 사용하도록 설정할 때 Mobility Service 푸시 설치 오류 문제 해결 | Microsoft Docs
description: 재해 복구에 대한 복제를 사용하도록 설정할 때 Mobility Service 설치 오류 문제 해결
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 02/27/2019
ms.openlocfilehash: 0278332105f2102fc82122c5a74db6326f011e81
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564680"
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>Mobility Service 푸시 설치 문제 해결

모바일 서비스 설치는 복제를 사용하도록 설정하는 동안 주요한 단계입니다. 이 단계의 성공은 필수 구성 요소 충족 및 지원되는 구성 작업에 달려 있습니다. 모바일 서비스를 설치하는 동안 접하는 가장 일반적인 문제의 원인은 다음과 같습니다.

* [자격 증명/권한 오류](#credentials-check-errorid-95107--95108)
* [로그인 실패](#login-failures-errorid-95519-95520-95521-95522)
* [연결 오류](#connectivity-failure-errorid-95117--97118)
* [파일 및 프린터 공유 오류](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [WMI 실패](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [지원되지 않는 운영 체제](#unsupported-operating-systems)
* [지원되지 않는 부트 구성](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [VSS 설치 오류](#vss-installation-failures)
* [디바이스 UUID 대신 GRUB 구성에 디바이스 이름 사용](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [LVM 볼륨](#lvm-support-from-920-version)
* [다시 부팅 경고](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

복제를 활성화하면 Azure Site Recovery가 가상 머신에 모바일 서비스 에이전트 설치를 푸시하려고 합니다. 이러한 시도의 일환으로 구성 서버가 가상 머신에 연결하여 에이전트를 복사하려고 합니다. 설치에 성공하려면 아래에 있는 단계별 문제 해결 지침을 따르십시오.

## <a name="credentials-check-errorid-95107--95108"></a>자격 증명 확인(ErrorID: 95107 및 95108)

* 복제 활성화 중에 선택한 사용자 계정이 **유효하고 정확한지** 확인합니다.
* 푸시 설치를 수행하려면 Azure Site Recovery에 **관리자 권한**이 있는 **ROOT** 계정 또는 사용자 계정이 필요합니다. 해당 계정이 없을 경우 원본 머신에서 푸시 설치가 차단됩니다.
  * Windows(**오류 95107**)의 경우 원본 머신에서 사용자 계정에 로컬 도메인에 대한 관리 액세스 권한이 있는지 확인합니다.
  * 도메인 계정을 사용하지 않는 경우 로컬 컴퓨터에서 원격 사용자 액세스 제어를 사용하지 않도록 설정해야 합니다.
    * 원격 사용자 액세스 제어를 사용하지 않도록 설정하려면 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System 레지스트리 키 아래에서 새로운 DWORD: LocalAccountTokenFilterPolicy를 추가합니다. 이 값을 1로 설정합니다. 이 단계를 실행하려면 명령 프롬프트에서 다음 명령을 실행합니다.

         `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
  * Linux(**오류 95108**)의 경우 모바일 에이전트를 성공적으로 설치하려면 루트 계정을 선택해야 합니다. 또한 SFTP 서비스가 실행되어야 합니다. sshd_config 파일에서 SFTP 하위 시스템 및 암호 인증을 사용하도록 설정하려면
    1. 루트로 로그인합니다.
    2. /etc/ssh/sshd_config 파일로 이동하여 PasswordAuthentication으로 시작하는 줄을 찾습니다.
    3. 줄의 주석 처리를 제거하고 값을 yes로 변경합니다.
    4. Subsystem으로 시작하는 줄을 찾아서 주석 처리를 제거합니다.
    5. sshd 서비스를 다시 시작합니다.

선택한 사용자 계정의 자격 증명을 수정하려면 [여기](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)에 제공된 지침을 따릅니다.

## <a name="insufficient-privileges-failure-errorid-95517"></a>권한 부족 오류(ErrorID: 95517)

모바일 에이전트를 설치하도록 선택한 사용자에게 관리자 권한이 없는 경우, 구성 서버/스케일 아웃 프로세스 서버에서 모바일 에이전트 소프트웨어를 원본 머신에 복사할 수 없습니다. 따라서 이 오류는 액세스 거부됨 오류의 결과입니다. 사용자 계정에 관리자 권한이 있는지 확인합니다.

선택한 사용자 계정의 자격 증명을 수정하려면 [여기](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)에 제공된 지침을 따릅니다.

## <a name="insufficient-privileges-failure-errorid-95518"></a>권한 부족 오류(ErrorID: 95518)

원본 컴퓨터에 로그인 하는 동안 오류가 발생 하면 도메인 트러스트 관계 설정 주 도메인 및 워크스테이션 간의 95518 오류 ID를 사용 하 여 모바일 에이전트 설치가 실패 합니다. 따라서 모바일 에이전트를 설치 하는 데 사용 하는 사용자 계정에 원본 컴퓨터의 주 도메인을 통해 로그인 하려면 관리자 권한이 있는지 확인 합니다.

선택한 사용자 계정의 자격 증명을 수정하려면 [여기](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)에 제공된 지침을 따릅니다.

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>로그인 실패(ErrorID: 95519, 95520, 95521, 95522)

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>사용자 계정의 자격 증명이 사용하지 않도록 설정됨(ErrorID: 95519)

복제를 사용하도록 설정하는 동안 선택한 사용자 계정을 사용할 수 없습니다. 사용자 계정을 사용하려면 [여기](https://aka.ms/enable_login_user)서 문서를 참조하거나, *username* 텍스트를 실제 사용자 이름으로 대체하여 다음 명령을 실행합니다.
`net user 'username' /active:yes`

### <a name="credentials-locked-out-due-to-multiple-failed-login-attempts-errorid-95520"></a>로그인 시도에 여러 번 실패하여 자격 증명이 잠김(ErrorID: 95520)

머신에 대한 액세스 시도가 여러 번 실패하면 사용자 계정이 잠깁니다. 실패 원인은 다음과 같을 수 있습니다.

* 구성 설정 중에 제공한 자격 증명이 잘못되었습니다. 또는
* 복제를 사용하도록 설정하는 동안 선택한 사용자 계정이 잘못되었습니다.

따라서 [여기](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)에 제공된 지침에 따라 선택한 자격 증명을 수정하고 잠시 후에 작업을 다시 시도합니다.

### <a name="logon-servers-are-not-available-on-the-source-machine-errorid-95521"></a>원본 머신에서 로그온 서버를 사용할 수 없음(ErrorID: 95521)

이 오류는 원본 머신에서 로그온 서버를 사용할 수 없는 경우에 발생합니다. 로그온 서버를 사용할 수 없는 경우 로그인 요청이 실패하여 모바일 에이전트를 설치할 수 없습니다. 로그인에 성공하려면 원본 머신에서 로그온 서버를 사용할 수 있는지 확인하고 로그온 서비스를 시작합니다. 자세한 지침은 KB [139410](https://support.microsoft.com/en-in/help/139410/err-msg-there-are-currently-no-logon-servers-available) 오류 메시지: 현재 아니요 로그온 서버 사용할 수 있습니다.

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>원본 머신에서 로그온 서비스가 실행되지 않음(ErrorID: 95522)

로그인 서비스가 원본 머신에서 실행되고 있지 않아 로그인 요청이 실패했습니다. 따라서 모바일 에이전트를 설치할 수 없습니다. 오류를 해결하려면 성공적인 로그인을 위해 원본 머신에서 로그온 서비스가 실행되고 있는지 확인합니다. 로그온 서비스를 시작하려면 명령 프롬프트에서 “net start Logon” 명령을 실행하거나 작업 관리자에서 “NetLogon” 서비스를 시작합니다.

## <a name="connectivity-failure-errorid-95117--97118"></a>**연결 실패(ErrorID: 95117 및 97118)**

구성 서버/스케일 아웃 프로세스 서버가 모바일 에이전트를 설치하기 위해 원본 VM에 연결하려고 시도합니다. 이 오류는 네트워크 연결 문제로 인해 원본 머신에 도달할 수 없는 경우에 발생합니다. 오류를 해결하려면 다음을 수행합니다.

* 구성 서버에서 원본 머신에 ping을 수행할 수 있는지 확인합니다. 복제를 활성화하는 동안 확장 프로세스 서버를 선택한 경우 프로세스 서버에서 원본 머신에 ping을 수행할 수 있어야 합니다.
  * 원본 서버 머신 명령줄에서 텔넷을 사용하여 아래와 같이 https 포트(135)로 구성 서버/확장 프로세스 서버에 ping을 수행하여 네트워크 연결 문제나 방화벽 포트 차단 문제가 있는지 확인합니다.

     `telnet <CS/ scale-out PS IP address> <135>`
* 추가로 **Linux VM**에서도 시작합니다.
  * 최신 openssh, openssh-server 및 openssl 패키지가 설치되어 있는지 확인합니다.
  * SSH(Secure Shell)를 사용할 수 있고 22 포트에서 실행 중인지 확인합니다.
  * SFTP 서비스가 실행되어야 합니다. sshd_config 파일에서 SFTP 하위 시스템 및 암호 인증을 사용하도록 설정하려면,
    * 루트로 로그인합니다.
    * /etc/ssh/sshd_config 파일로 이동하여 PasswordAuthentication으로 시작하는 줄을 찾습니다.
    * 줄의 주석 처리를 제거하고 값을 yes로 변경합니다.
    * Subsystem으로 시작하는 줄을 찾아서 주석 처리를 제거합니다.
    * sshd 서비스를 다시 시작합니다.
* 일정 기간 후에 적절한 응답이 없거나 연결된 호스트가 응답하지 못하여 기존 연결에 오류가 발생한 경우 연결 시도가 실패할 수 있습니다.
* 연결/네트워크/도메인 관련 문제일 수 있습니다. DNS 이름 확인 문제 또는 TCP 포트 고갈 문제가 원인일 수 있습니다. 도메인에 이러한 알려진 문제가 있는지 확인합니다.

## <a name="connectivity-failure-errorid-95523"></a>연결 실패(ErrorID: 95523)

이 오류는 원본 머신이 상주하는 네트워크를 찾을 수 없거나, 삭제되었거나, 더 이상 사용할 수 없는 경우에 발생합니다. 오류를 해결하는 유일한 방법은 네트워크가 존재하는지 확인하는 것입니다.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>파일 및 프린터 공유 서비스 확인(ErrorID: 95105 및 95106)

연결을 모니터링한 후에는 가상 머신에 파일 및 프린터 공유 서비스가 활성화되어 있는지 확인합니다. 모바일 에이전트를 원본 머신에 복사하려면 이러한 설정이 필요합니다.

**Windows 2008 R2 이전 버전**의 경우,

* Windows 방화벽을 통해 파일 및 인쇄 공유가 가능하도록 설정하려면,
  * 제어판 -> 시스템 및 보안 -> Windows 방화벽으로 이동하여 왼쪽 창에서 고급 설정을 클릭하고 콘솔 트리에서 인바운드 규칙을 클릭합니다.
  * 파일 및 프린터 공유(NB-Session-In) 및 파일 및 프린터 공유(SMB-In) 규칙을 찾습니다. 각 규칙을 마우스 오른쪽 단추로 클릭한 다음, **규칙 사용**을 클릭합니다.
* 그룹 정책을 사용하여 파일 공유가 가능하도록 하려면,
  * 시작으로 이동하여 gpmc.msc를 입력하고 검색합니다.
  * 탐색 창에서 로컬 컴퓨터 정책, 사용자 구성, 관리 템플릿, Windows 구성 요소 및 네트워크 공유 폴더를 엽니다.
  * 세부 정보 창에서 **사용자 프로필 내의 파일 공유 안 함**을 두 번 클릭합니다. 그룹 정책 설정을 사용하지 않도록 설정하고 사용자가 파일을 공유할 수 있도록 하려면 사용 안 함을 클릭합니다. 확인을 클릭하여 변경 내용을 저장합니다. 자세한 내용은 참조 하세요 [그룹 정책을 사용 하 여 파일 공유를 사용할지](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10))합니다.

에 대 한 **이상 버전**에 제공 된 지침에 따라 [VMware Vm 및 물리적 서버의 재해 복구를 위해 모바일 서비스 설치](vmware-azure-install-mobility-service.md) 파일 및 프린터 공유를 사용 하도록 설정 합니다.

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>WMI(Windows Management Instrumentation) 구성 확인(오류 코드: 95103)

파일 및 프린터 서비스를 확인한 후에는 방화벽을 통해 개인, 공용 및 도메인 프로필에 대한 WMI 서비스를 사용하도록 설정합니다. 이 설정은 원본 머신에서 원격 실행을 완료하는 데 필요합니다. 사용하도록 설정하려면 다음을 수행합니다.

* 제어판으로 이동하여 보안을 클릭한 다음, Windows 방화벽을 클릭합니다.
* 설정 변경을 클릭한 다음, 예외 탭을 클릭합니다.
* 예외 창에서 WMI(Windows Management Instrumentation)에 대한 확인란을 선택하여 방화벽을 통해 WMI 트래픽을 사용하도록 설정합니다. 

명령 프롬프트에서 방화벽을 통해 WMI 트래픽을 사용하도록 설정할 수도 있습니다. `netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes` 명령을 사용합니다.
기타 WMI 문제 해결 문서는 다음 문서에서 찾을 수 있습니다.

* [기본 WMI 테스트](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [WMI 문제 해결](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [WMI 스크립트 및 WMI 서비스 관련 문제 해결](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="unsupported-operating-systems"></a>지원되지 않는 운영 체제

실패에 대한 또 다른 가장 일반적인 이유는 지원되지 않는 운영 체제일 수 있습니다. 모바일 서비스를 성공적으로 설치하려면 지원되는 운영 체제/커널 버전에 설치해야 합니다. 개인 패치를 사용하지 마세요.
Azure Site Recovery에서 어떤 운영 체제 및 커널 버전이 지원되는지 알아보려면 [지원 매트릭스 문서](vmware-physical-azure-support-matrix.md#replicated-machines)를 참조하세요.

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>지원되지 않는 부팅 디스크 구성(ErrorID: 95309, 95310, 95311)

### <a name="boot-and-system-partitions--volumes-are-not-the-same-disk-errorid-95309"></a>부트 및 시스템 파티션/볼륨이 동일한 디스크가 아님(ErrorID: 95309)

9.20 이전 버전에서는 서로 다른 디스크의 부트 및 시스템 파티션/볼륨이 지원되지 않는 구성이었습니다. [9.20 버전](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)부터 이 구성이 지원됩니다. 이 지원을 활용하려면 최신 버전을 사용합니다.

### <a name="the-boot-disk-is-not-available-errorid-95310"></a>부팅 디스크를 사용할 수 없음(ErrorID: 95310)

부트 디스크가 없는 가상 머신은 보호할 수 없습니다. 이는 장애 조치 작업 중에 가상 머신의 원활한 복구를 보장하기 위한 것입니다. 부트 디스크가 없으면 장애 조치 후 머신을 부팅하지 못합니다. 가상 머신에 부트 디스크가 있는지 확인하고 작업을 다시 시도하세요. 또한 동일한 머신에 부트 디스크가 여러 개 있는 것도 지원되지 않습니다.

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>원본 머신에 여러 개의 부팅 디스크가 있음(ErrorID: 95311)

부트 디스크가 여러 개인 가상 머신은 [지원되는 구성](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)이 아닙니다.

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>여러 디스크에 있는 시스템 파티션(ErrorID: 95313)

9.20 이전 버전에서는 여러 디스크에 배치된 루트 파티션 또는 볼륨이 지원되지 않는 구성이었습니다. [9.20 버전](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)부터 이 구성이 지원됩니다. 이 지원을 활용하려면 최신 버전을 사용합니다.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>UUID 대신 디바이스 이름이 GRUB 구성에 언급되어 보호를 사용하도록 설정하지 못함(ErrorID: 95320)

**가능한 원인:** </br>
GRUB 구성 파일("/boot/grub/menu.lst", "/boot/grub/grub.cfg", "/boot/grub2/grub.cfg" 또는 "/etc/default/grub")에는 **root** 및 **resume** 매개 변수의 값이 UUID가 아닌 실제 디바이스 이름으로 포함되어 있을 수 있습니다. Site Recovery에서는 UUID를 사용해야 합니다. 디바이스 이름은 VM을 다시 부팅하면 변경될 수 있는데, 장애 조치(failover) 시에 VM 이름이 달라지면 문제가 발생하기 때문입니다. 예를 들면 다음과 같습니다. </br>


- 아래에는 이러한 오류의 원인이 되는 GRUB 파일 **/boot/grub2/grub.cfg**에서 발췌한 줄이 나와 있습니다. <br>
  *linux   /boot/vmlinuz-3.12.49-11-default **root=/dev/sda2**  ${extra_cmdline} **resume=/dev/sda1** splash=silent quiet showopts*


- 아래에는 GRUB 파일 **/boot/grub/menu.lst**에서 발췌한 줄이 나와 있습니다.
  *kernel /boot/vmlinuz-3.0.101-63-default **root=/dev/sda2** **resume=/dev/sda1** splash=silent crashkernel=256M-:128M showopts vga=0x314*

위에서 굵게 표시된 문자열을 살펴보면 GRUB의 “root” 및 “resume” 매개 변수에 대해 UUID 대신 실제 디바이스 이름이 사용된 것을 확인할 수 있습니다.
 
**해결 방법:**<br>
디바이스 이름을 해당 UUID로 바꿔야 합니다.<br>


1. 명령을 실행 하 여 장치의 UUID를 찾으려면 "blkid \<장치 이름 >"입니다. 예를 들면 다음과 같습니다.<br>
   ```
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2 
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3" 
   ```

2. 이제 장치 이름으로 같은 형식으로 해당 UUID 바꿉니다 "루트 UUID = =\<UUID >"입니다. 예를 들어, 루트에 대 한 UUID를 사용 하 여 장치 이름을 대체 하 고 파일에 위에서 언급 한 매개 변수를 다시 시작 하는 경우 "/ boot/grub2/grub.cfg", "/ boot/grub2/grub.cfg" 또는 "/ 등/기본/grub: 파일의 줄을 다음과 같이 보입니다. <br>
   *kernel /boot/vmlinuz-3.0.101-63-default **root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4** **resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b** splash=silent crashkernel=256M-:128M showopts vga=0x314*
3. 보호 다시 시작

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>모바일 서비스 설치가 완료되고 재부팅하라는 경고가 표시됨(ErrorID: 95265 및 95266)

Site Recovery 모바일 서비스에는 여러 구성 요소가 있으며, 그중에서 하나를 필터 드라이버라고 합니다. 필터 드라이버는 시스템 재부팅 시에만 시스템 메모리에 로드됩니다. 따라서 시스템 재부팅 시 새 필터 드라이버가 로드될 때만 필터 드라이버를 수정할 수 있습니다.

이는 경고이며, 기존 복제는 새 에이전트 업데이트 후에도 작동한다는 것에 **유의**하세요. 작업에서 이전 필터 드라이버 유지를 다시 부팅 하지 하는 경우 새 필터 드라이버의 이점을 활용 해야 할 때마다 다시 부팅 하도록 선택할 수 있습니다. 따라서 재부팅하지 않고 업데이트한 후에는 필터 드라이버를 제외한 **모바일 서비스의 기타 개선 사항과 수정 사항의 혜택이 실현**됩니다. 즉,이 좋지만, 업그레이드가 수행 될 때마다 다시 부팅 하는 필수적이 지 않습니다. 경우에 대 한 다시 부팅 필수 정보를 설정 합니다 [모바일 에이전트를 업그레이드 한 후 원본 컴퓨터의 다시 부팅 ](https://aka.ms/v2a_asr_reboot) 섹션에서는 Azure Site Recovery에서 서비스 업데이트에서.

> [!TIP]
>유지 관리 기간에 업그레이드 일정에서 모범 사례에 대 한 참조를 [최신 OS/커널 버전에 대 한 지원을](https://aka.ms/v2a_asr_upgrade_practice) Azure Site Recovery에서 서비스 업데이트에서.

## <a name="lvm-support-from-920-version"></a>9.20 버전의 LVM 지원

9.20 이전 버전에서는 데이터 디스크에 대해서만 LVM이 지원되었습니다. /boot는 디스크 파티션에 있어야 하며 LVM 볼륨이 아니어야 합니다.

[9.20 버전](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)부터 [LVM의 OS 디스크](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)가 지원됩니다. 이 지원을 활용하려면 최신 버전을 사용합니다.

## <a name="insufficient-space-errorid-95524"></a>공간 부족(ErrorID: 95524)

모바일 에이전트를 원본 머신에 복사하는 경우 최소 100MB의 여유 공간이 필요합니다. 따라서 원본 머신에 필요한 여유 공간이 있는지 확인하고 작업을 다시 시도합니다.

## <a name="vss-installation-failures"></a>VSS 설치 오류

VSS 설치는 모바일 에이전트 설치의 일부입니다. 이 서비스는 애플리케이션 일치 복구 지점 생성 프로세스에 사용됩니다. VSS 설치 중 오류는 여러 이유로 인해 발생할 수 있습니다. 정확한 오류를 식별하려면 **c:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log**를 참조하세요. 몇 가지 일반적인 오류 및 해결 단계는 다음 섹션에서 강조 표시됩니다.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>VSS 오류 -2147023170 [0x800706BE] - 종료 코드 511

이 문제는 바이러스 백신 소프트웨어가 Azure Site Recovery 서비스의 작업을 차단 하는 경우에 주로 표시 됩니다. 이 문제를 해결하려면:

1. [여기](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)에서 언급된 모든 폴더를 제외합니다.
2. 바이러스 백신 공급자가 게시한 지침을 따라 Windows에서 DLL의 등록을 차단 해제합니다.

### <a name="vss-error-7-0x7---exit-code-511"></a>VSS 오류 7 [0x7] - 종료 코드 511

이는 런타임 오류이며 VSS 설치를 위한 메모리 부족으로 인해 발생합니다. 이 작업의 성공적인 완료를 위해 디스크 공간을 늘려야 합니다.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>VSS 오류 -2147023824 [0x80070430] - 종료 코드 517

이 오류는 Azure Site Recovery VSS 공급자 서비스가 [삭제를 위해 표시된](https://msdn.microsoft.com/en-us/library/ms838153.aspx) 경우에 발생합니다. 다음 명령줄을 실행하여 원본 머신에서 수동으로 VSS를 설치해 보세요.

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>VSS 오류 -2147023841 [0x8007041F] - 종료 코드 512

이 오류는 Azure Site Recovery VSS 공급자 서비스 데이터베이스가 [잠긴](https://msdn.microsoft.com/en-us/library/ms833798.aspx) 경우에 발생합니다. 다음 명령줄을 실행하여 원본 머신에서 수동으로 VSS를 설치해 보세요.

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-exit-code-806"></a>VSS 종료 코드 806

이 오류는 설치에 사용되는 사용자 계정에 CSScript 명령을 실행할 수 있는 사용 권한이 없는 경우에 발생합니다. 사용자 계정에 스크립트를 실행하기 위해 필요한 권한을 제공하고 작업을 다시 시도합니다.

### <a name="other-vss-errors"></a>기타 VSS 오류

다음 명령줄을 실행하여 원본 머신에서 수동으로 VSS 공급자 서비스를 설치해 보세요.

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`



## <a name="vss-error---0x8004e00f"></a>VSS 오류-0x8004E00F

이 오류는 일반적으로 DCOM 및 DCOM 위험 상태에서는 문제로 인해 모바일 에이전트를 설치 하는 동안 발생 합니다.

오류의 원인을 확인 하려면 다음 절차를 따르십시오.

**설치 로그를 검사 합니다.**

1. C:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log에 있는 설치 로그를 엽니다.
2. 다음 오류가 있으면이이 문제를 나타냅니다.

    기존 응용 프로그램의 등록을 취소 하는 중...  카탈로그 개체를 만들고 응용 프로그램의 컬렉션을 가져옵니다. 

    오류:

    - 오류 코드: [0x8004E00F]-2147164145
    - 종료 코드: 802

이 문제를 해결하려면

연락처를 [Microsoft Windows 플랫폼 팀](https://aka.ms/Windows_Support) DCOM 문제 해결 도움을 가져오려고 합니다.

DCOM 문제가 해결 되 면 다음 명령을 사용 하 여 수동으로 Azure Site Recovery VSS 공급자를 다시 설치 합니다.
 
**C:\Program 파일 (x86) \Microsoft Azure Site Recovery\agent > "C:\Program 파일 (x86) \Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd**
  
응용 프로그램 일관성 재해 복구 요구 사항에 대 한 중요 하지 않은 경우에 VSS 공급자 설치를 무시할 수 있습니다. 

Azure Site Recovery VSS 공급자 설치를 무시 하 수동으로 설치 된 후에 Azure Site Recovery VSS 공급자를 설치 합니다.

1. 모바일 서비스를 설치 합니다. 
   > [!Note]
   > 
   > '설치 후 구성' 단계에서 설치가 실패 합니다. 
2. VSS 설치 하지 않으려면:
   1. 에 있는 Azure Site Recovery 모바일 서비스 설치 디렉터리를 엽니다.
   
      C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
   2. Azure Site Recovery VSS 공급자 설치 스크립트를 수정 **nMageVSSProvider_Install** 하 고 **InMageVSSProvider_Uninstall.cmd** 다음 줄을 추가 하 여 항상 성공 하려면:
    
      ```     
      rem @echo off
      setlocal
      exit /B 0
      ```

3. 모바일 에이전트 설치를 수동으로 다시 실행 합니다. 
4. 설치가 성공 하 고 다음 단계로 이동 되 면 **구성**에서 추가한 줄을 제거 합니다.
5. VSS 공급자를 설치 하려면 관리자 권한으로 명령 프롬프트를 열고 다음 명령을 실행 합니다.
   
    **C:\Program 파일 (x86) \Microsoft Azure Site Recovery\agent >.\InMageVSSProvider_Install.cmd**

9. Windows 서비스에서 서비스로 ASR VSS 공급자 설치 되어 있는지 확인 하 고 ASR VSS 공급자가 표시 되는지 확인 하려면 구성 요소 서비스 MMC를 엽니다.
10. VSS 공급자를 설치 하는 경우 실패 CX CAPI2에서 권한 오류를 해결 하기를 사용 하 여 작업을 계속 합니다.

## <a name="vss-provider-installation-fails-due-to-the-cluster-service-being-enabled-on-non-cluster-machine"></a>비클러스터 컴퓨터에 사용 되는 클러스터 서비스가 VSS 공급자 설치 실패

이 문제로 인해 Azure Site Recovery Mobility Agent 설치에 VSS 공급자 설치를 방지 하는 COM +를 사용 하 여 문제로 인해 ASAzure 사이트 복구 VSS 공급자 설치 단계 중에 실패 합니다.
 
### <a name="to-identify-the-issue"></a>문제를 식별 하려면

C:\ProgramData\ASRSetupLogs\UploadedLogs에서 구성 서버에 있는 로그에\<날짜-시간 > UA_InstallLogFile.log, 다음 예외를 찾을 수 있습니다.

COM +에서 Microsoft Distributed Transaction Coordinator와 통신할 수 없습니다 (HRESULT의 예외: 0x8004E00F)

이 문제를 해결하려면

1.  이 컴퓨터를 클러스터가 아닌 컴퓨터 인지 하 고 클러스터 구성 요소를 사용 되는 확인 합니다.
3.  구성 요소를 사용 하지 않는 경우 컴퓨터에서 클러스터 구성 요소를 제거 합니다.

## <a name="drivers-are-missing-on-the-source-server"></a>드라이버가 원본 서버에서 누락 되었습니다.

모바일 에이전트 설치가 실패할 경우 C:\ProgramData\ASRSetupLogs 일부 컨트롤 집합에 누락 된 경우 일부 필요한 드라이버를 확인 하려면 로그를 확인 합니다.
 
이 문제를 해결하려면
  
1. Regedit.msc 같은 레지스트리 편집기를 사용 하 여 레지스트리를 엽니다.
2. HKEY_LOCAL_MACHINE\SYSTEM 노드를 엽니다.
3. 시스템 노드에서 찾을 컨트롤 집합입니다.
4. 각 컨트롤 집합을 열고 다음 Windows 드라이버가 설치 되어 있는지 확인 합니다.

   - Atapi
   - Vmbus
   - Storflt
   - Storvsc
   - intelide
 
모든 누락 된 드라이버를 다시 설치 합니다.

## <a name="next-steps"></a>다음 단계

VMware VM에 대한 재해 복구를 설정하는 [방법을 알아봅니다](vmware-azure-tutorial.md).
