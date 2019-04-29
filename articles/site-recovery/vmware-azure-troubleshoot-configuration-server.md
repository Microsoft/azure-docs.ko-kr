---
title: Azure Site Recovery를 사용하여 VMware VM 및 물리적 서버를 Azure로 재해 복구하는 동안 구성 서버 문제 해결 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery를 사용하여 VMware VM 및 물리적 서버를 Azure로 재해 복구하는 구성 서버 배포에 대한 문제 해결 정보를 제공합니다.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/13/2019
ms.author: ramamill
ms.openlocfilehash: f08d7bb2087ef4f30b325b3796a13e387ccdea22
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60725570"
---
# <a name="troubleshoot-configuration-server-issues"></a>구성 서버 문제 해결

이 문서는 [Azure Site Recovery](site-recovery-overview.md) 구성 서버를 배포 및 관리할 때 문제를 해결하는 데 도움이 됩니다. 구성 서버는 관리 서버로 작동합니다. Site Recovery에서는 구성 서버를 사용하여 온-프레미스 VMware VM 및 물리적 서버의 재해 복구를 Azure로 설정합니다. 다음 섹션에서는 새 구성 서버를 추가할 때 및 구성 서버를 관리할 때 발생할 수 있는 가장 일반적인 오류에 대해 설명합니다.

## <a name="registration-failures"></a>등록 오류

원본 머신은 모바일 에이전트를 설치할 때 구성 서버에 등록됩니다. 이 단계를 수행하는 동안 오류가 발생하면 다음 지침에 따라 디버그할 수 있습니다.

1. C:\ProgramData\ASR\home\svsystems\var\configurator_register_host_static_info.log 파일을 엽니다. (ProgramData 폴더는 숨겨진 폴더일 수 있습니다. ProgramData 폴더가 보이지 않으면 파일 탐색기의 **보기** 탭에 있는 **표시/숨기기** 섹션에서 **숨긴 항목** 확인란을 선택하십시오.) 오류는 여러 가지 문제로 인해 발생할 수 있습니다.

2. **No Valid IP Address found** 문자열을 검색합니다. 문자열이 검색되면:
   1. 요청한 호스트 ID가 원본 머신의 호스트 ID와 같은지 확인합니다.
   2. 실제 NIC에 할당된 IP 주소가 원본 머신에 하나 이상 있는지 확인합니다. 에이전트가 구성 서버에 성공적으로 등록되려면, 실제 NIC에 할당된 유효한 IPv4 주소가 원본 머신에 하나 이상 있어야 합니다.
   3. 원본 머신에서 다음 명령 중 하나를 실행하여 원본 머신의 모든 IP 주소를 가져옵니다.
      - Windows의 경우: `> ipconfig /all`
      - Linux의 경우: `# ifconfig -a`

3. **No Valid IP Address found** 문자열이 검색되지 않으면 **Reason=>NULL** 문자열을 검색합니다. 이 오류는 원본 머신이 빈 호스트를 사용하여 구성 서버에 등록하는 경우 발생합니다. 문자열이 검색되면:
    - 문제를 해결한 후에는 [구성 서버에 원본 머신 등록](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)의 지침에 따라 등록을 수동으로 다시 시도합니다.

4. **Reason=>NULL** 문자열이 검색되지 않으면, 원본 머신에서 C:\ProgramData\ASRSetupLogs\UploadedLogs\ASRUnifiedAgentInstaller.log 파일을 엽니다. (ProgramData 폴더는 숨겨진 폴더일 수 있습니다. ProgramData 폴더가 보이지 않으면 파일 탐색기의 **보기** 탭에 있는 **표시/숨기기** 섹션에서 **숨긴 항목** 확인란을 선택하십시오.) 오류는 여러 가지 문제로 인해 발생할 수 있습니다. 

5. **post request: (7) - Couldn't connect to server** 문자열을 검색합니다. 문자열이 검색되면:
    1. 원본 머신과 구성 서버 간의 네트워크 문제를 해결합니다. ping, traceroute과 같은 네트워크 도구 또는 웹 브라우저를 사용하여 원본 머신에서 구성 서버에 연결할 수 있는지 확인합니다. 원본 머신이 포트 443을 통해 구성 서버에 연결할 수 있는지 확인합니다.
    2. 원본 머신의 방화벽 규칙이 원본 머신과 구성 서버 간의 연결을 차단하는지 검사합니다. 연결 문제가 있으면 네트워크 관리자와 협력하여 차단을 해제하십시오.
    3. [Site Recovery 폴더에 나열된 폴더가 바이러스 백신 프로그램](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)에서 제외되고 바이러스 백신 소프트웨어에서 제외되도록 해야 합니다.
    4. 네트워크 문제가 해결되면 [구성 서버에 원본 머신 등록](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)의 지침에 따라 등록을 다시 시도합니다.

6. **post request: (7) - Couldn't connect to server** 문자열이 검색되지 않으면 동일한 로그 파일에서 **request: (60) - Peer certificate cannot be authenticated with given CA certificates** 문자열을 찾아봅니다. 이 오류는 구성 서버 인증서가 만료되었거나 원본 머신이 TLS 1.0 이상 SSL 프로토콜을 지원하지 않기 때문에 발생할 수 있습니다. 또한 방화벽이 원본 머신과 구성 서버 간의 SSL 통신을 차단하면 발생할 수 있습니다. 문자열이 검색되면: 
    1. 해결을 위해, 원본 머신의 웹 브라우저를 사용하여 구성 서버 IP 주소에 연결합니다. https:\/\/<configuration server IP address\>:443/ URI를 사용합니다. 원본 머신이 포트 443을 통해 구성 서버에 연결할 수 있는지 확인합니다.
    2. 원본 머신이 구성 서버와 통신하기 위해 원본 머신에서 방화벽 규칙을 추가하거나 제거해야 하는지 확인합니다. 사용하는 방화벽 소프트웨어가 다양하기 때문에, 필요한 방화벽 구성을 모두 나열할 수는 없습니다. 연결 문제가 있으면 네트워크 관리자와 협력하여 차단을 해제하십시오.
    3. [Site Recovery 폴더에 나열된 폴더가 바이러스 백신 프로그램](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)에서 제외되고 바이러스 백신 소프트웨어에서 제외되도록 해야 합니다.  
    4. 문제를 해결한 후에 [구성 서버에 원본 머신 등록](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)의 지침에 따라 등록을 다시 시도합니다.

7. Linux에서 <INSTALLATION_DIR\>/etc/drscout.conf에 있는 플랫폼 값이 손상된 경우에는 등록에 실패합니다. 이 문제를 식별하려면 /var/log/ua_install.log 파일을 엽니다. **Aborting configuration as VM_PLATFORM value is either null or it is not VmWare/Azure** 문자열을 검색합니다. 플랫폼은 **VmWare**나 **Azure**로 설정되어야 합니다. drscout.conf 파일이 손상된 경우에는 [모바일 에이전트를 제거](vmware-physical-manage-mobility-service.md#uninstall-mobility-service)한 다음, 모바일 에이전트를 다시 설치하는 것이 좋습니다. 제거에 실패 하는 경우 다음 단계를 완료 합니다:는 합니다. Installation_Directory/uninstall.sh 파일을 열고 **StopServices** 함수에 대한 호출을 주석으로 처리합니다.
    b. Installation_Directory/Vx/bin/uninstall.sh 파일을 열고 **stop_services** 함수에 대한 호출을 주석으로 처리합니다.
    다. Installation_Directory/Fx/uninstall.sh 파일을 열고 Fx 서비스를 중지하려고 시도하는 전체 섹션을 주석으로 처리합니다.
    d. 모바일 에이전트를 [제거](vmware-physical-manage-mobility-service.md#uninstall-mobility-service)합니다. 제거가 완료되면 시스템을 다시 부팅한 다음, 모바일 에이전트를 다시 설치해봅니다.

## <a name="installation-failure-failed-to-load-accounts"></a>설치 실패: 계정을 로드하지 못함

이 오류는 모바일 에이전트를 설치하고 구성 서버에 등록하는 동안 서비스가 전송 연결에서 데이터를 읽을 수 없을 때 발생합니다. 이 문제를 해결하려면 원본 머신에 TLS 1.0이 활성화되어 있는지 확인합니다.

## <a name="vcenter-discovery-failures"></a>vCenter 검색 실패

vCenter 검색 실패를 해결하려면 vCenter Server를 바이패스 목록 프록시 설정에 추가합니다. 

- [여기](https://aka.ms/PsExec)에서 PsExec 도구를 다운로드하여 시스템 사용자 콘텐츠에 액세스합니다.
- 명령줄 psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"를 실행하여 시스템 사용자 콘텐츠에서 Internet Explorer를 엽니다.
- IE에서 프록시 설정을 추가하고 tmanssvc 서비스를 다시 시작합니다.
- DRA 프록시 설정을 구성하려면 cd C:\Program Files\Microsoft Azure Site Recovery Provider를 실행합니다.
- 다음으로, DRCONFIGURATOR.EXE /configure /AddBypassUrls를 실행합니다[[구성 서버 배포](vmware-azure-deploy-configuration-server.md#configure-settings)의 **vCenter Server/vSphere ESXi 서버 구성** 단계 동안 제공되는 vCenter Server의 IP 주소/FQDN 추가].

## <a name="change-the-ip-address-of-the-configuration-server"></a>구성 서버의 IP 주소 변경

구성 서버의 IP 주소는 변경하지 않는 것이 좋습니다. 구성 서버에 할당된 모든 IP 주소는 고정 IP 주소여야 합니다. DHCP IP 주소는 사용하지 마십시오.

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008: SAML 토큰이 잘못되었습니다.

이 오류를 방지하려면, 시스템 시계의 시간이 현지 시간과 15분 넘게 차이가 나지 않도록 해야 합니다. 설치 관리자를 다시 실행하여 등록을 완료합니다.

## <a name="failed-to-create-a-certificate"></a>인증서 만들기 실패

Site Recovery를 인증하는 데 필요한 인증서를 만들 수 없습니다. 로컬 관리자로 설치 프로그램을 실행하고 있는지 확인한 후에 설치 프로그램을 다시 실행합니다.

## <a name="failure-to-activate-windows-license-from-server-standard-evaluation-to-server-standard"></a>Windows Server standard Server Standard 평가판 라이선스를 정품 인증에 실패

1. OVF 통해 구성 서버 배포의 일환으로,은 180 일 동안 유효 하는 평가판 라이선스가 사용 됩니다. 만료되기 전에 이 라이선스를 활성화해야 합니다. 그렇지 않은 경우 구성 서버는 자주 종료 고 따라서 복제 작업에 방해가 될 수 있습니다.
2. Windows 라이선스를 활성화할 수 없는 경우 [Windows 지원 팀](https://aka.ms/Windows_Support)에 문의하여 문제를 해결하세요.

## <a name="register-source-machine-with-configuration-server"></a>구성 서버에 원본 머신 등록

### <a name="if-the-source-machine-runs-windows"></a>원본 머신에서 Windows를 실행하는 경우

원본 머신에서 다음 명령을 실행합니다.

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <configuration server IP address> /PassphraseFilePath <passphrase file path>
```

설정 | 세부 정보
--- | ---
사용 현황 | UnifiedAgentConfigurator.exe  /CSEndPoint <구성 서버 IP 주소\> /PassphraseFilePath <암호 파일 경로\>
에이전트 구성 로그 | %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log 아래에 있습니다.
/CSEndPoint | 필수 매개 변수입니다. 구성 서버의 IP 주소를 지정합니다. 유효한 IP 주소를 사용합니다.
/PassphraseFilePath |  필수. 암호의 위치입니다. 유효한 UNC 또는 로컬 파일 경로를 사용합니다.

### <a name="if-the-source-machine-runs-linux"></a>원본 머신에서 Linux를 실행하는 경우

원본 머신에서 다음 명령을 실행합니다.

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <configuration server IP address> -P /var/passphrase.txt
  ```

설정 | 세부 정보
--- | ---
사용 현황 | cd /usr/local/ASR/Vx/bin<br /><br /> UnifiedAgentConfigurator.sh -i <구성 서버 IP 주소\> -P <암호 파일 경로\>
-i | 필수 매개 변수입니다. 구성 서버의 IP 주소를 지정합니다. 유효한 IP 주소를 사용합니다.
-P |  필수. 암호가 저장되는 파일의 전체 파일 경로입니다. 유효한 폴더를 사용합니다.

## <a name="unable-to-configure-the-configuration-server"></a>구성 서버를 구성할 수 없음

가상 머신에서 구성 서버 이외의 애플리케이션을 설치하는 경우 마스터 대상을 구성하지 못할 수 있습니다. 

구성 서버는 단일 용도의 서버여야 하며, 공유 서버로 사용하는 것은 지원되지 않습니다. 

자세한 내용은 [구성 서버 배포](vmware-azure-deploy-configuration-server.md#faq)에서 구성 FAQ를 참조하세요. 

## <a name="remove-the-stale-entries-for-protected-items-from-the-configuration-server-database"></a>구성 서버 데이터베이스에서 보호된 항목에 대한 오래된 항목 제거 

구성 서버에서 보호된 부실 컴퓨터를 제거하려면 다음 단계를 사용합니다. 
 
1. 부실 항목의 원본 컴퓨터 및 IP 주소를 확인하려면 

    1. 관리자 모드에서 MYSQL 명령줄을 엽니다. 
    2. 다음 명령을 실행합니다. 
   
        ```
        mysql> use svsdb1;
        mysql> select id as hostid, name, ipaddress, ostype as operatingsystem, from_unixtime(lasthostupdatetime) as heartbeat from hosts where name!='InMageProfiler'\G;
        ```

        이 경우 해당 IP 주소 및 마지막 하트비트와 함께 등록된 컴퓨터의 목록이 반환됩니다. 부실 복제 쌍이 있는 호스트를 찾습니다.

2. 관리자 권한 명령 프롬프트를 열고 C:\ProgramData\ASR\home\svsystems\bin으로 이동합니다. 
4. 구성 서버에서 등록된 호스트 세부 정보와 부실 항목 정보를 제거하려면 부실 항목의 원본 컴퓨터와 IP 주소를 사용하여 다음 명령을 실행합니다. 
   
    `Syntax: Unregister-ASRComponent.pl -IPAddress <IP_ADDRESS_OF_MACHINE_TO_UNREGISTER> -Component <Source/ PS / MT>`
 
    "온-프레미스 VM01"의 소스 서버 항목을 10.0.0.4의 ip 주소를 가진 경우 다음 명령을 대신 사용 합니다.
 
    `perl Unregister-ASRComponent.pl -IPAddress 10.0.0.4 -Component Source`
 
5. 원본 컴퓨터에서 다음 서비스를 다시 시작하여 구성 서버에 다시 등록합니다. 
 
    - InMage Scout Application 서비스
    - InMage Scout VX Agent – Sentinel/Outpost

## <a name="upgrade-fails-when-the-services-fail-to-stop"></a>서비스 중지 실패 시 업그레이드가 실패함

특정 서비스가 중지되지 않으면 구성 서버 업그레이드가 실패합니다. 

문제를 식별하려면 구성 서버의 C:\ProgramData\ASRSetupLogs\CX_TP_InstallLogFile로 이동합니다. 다음 오류가 있으면 아래 단계를 사용하여 문제를 해결합니다. 

    2018-06-28 14:28:12.943   Successfully copied php.ini to C:\Temp from C:\thirdparty\php5nts
    2018-06-28 14:28:12.943   svagents service status - SERVICE_RUNNING
    2018-06-28 14:28:12.944   Stopping svagents service.
    2018-06-28 14:31:32.949   Unable to stop svagents service.
    2018-06-28 14:31:32.949   Stopping svagents service.
    2018-06-28 14:34:52.960   Unable to stop svagents service.
    2018-06-28 14:34:52.960   Stopping svagents service.
    2018-06-28 14:38:12.971   Unable to stop svagents service.
    2018-06-28 14:38:12.971   Rolling back the install changes.
    2018-06-28 14:38:12.971   Upgrade has failed.

이 문제를 해결하려면

다음 서비스를 수동으로 중지합니다.

- cxprocessserver
- InMage Scout VX Agent – Sentinel/Outpost 
- Microsoft Azure Recovery Services 에이전트 
- Microsoft Azure Site Recovery 서비스 
- tmansvc
  
구성 서버를 업데이트하려면 [통합 설치](service-updates-how-to.md#links-to-currently-supported-update-rollups)를 다시 실행합니다.

## <a name="azure-active-directory-application-creation-failure"></a>Azure Active Directory 애플리케이션 생성 실패

AAD(Azure Active Directory)에서 [OVA(Open Virtualization Application)](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template
) 템플릿을 사용하여 애플리케이션을 만들 수 있는 권한이 부족합니다.

이 문제를 해결하려면 Azure Portal에 로그인하고 다음 중 하나를 수행합니다.

- AAD에서 애플리케이션 개발자 역할을 요청합니다. 애플리케이션 개발자 역할에 대한 자세한 내용은 [Azure Active Directory의 관리자 역할 권한](../active-directory/users-groups-roles/directory-assign-admin-roles.md)을 참조하세요.
- AAD에서 **사용자가 애플리케이션을 만들 수 있음** 플래그를 *true*로 설정했는지 확인합니다. 자세한 내용은 [방법: 포털을 사용하여 리소스에 액세스할 수 있는 Azure AD 애플리케이션 및 서비스 주체 만들기](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)

## <a name="process-servermaster-target-are-unable-to-communicate-with-the-configuration-server"></a>프로세스 서버/마스터 대상이 구성 서버와 통신할 수 없음 

PS(프로세스 서버) 및 MT(마스터 대상) 모듈이 CS(구성 서버)와 통신할 수 없으며 Azure Portal에서 해당 상태가 연결되지 않음으로 표시됩니다.

일반적으로 이 문제는 포트 443의 오류 때문입니다. 다음 단계에 따라 포트의 차단을 해제하고 CS와의 통신을 다시 사용하도록 설정합니다.

**마스터 대상 에이전트에서 MARS 에이전트를 호출하는지 확인**

마스터 대상 에이전트가 구성 서버 IP의 TCP 세션을 만들 수 있는지 확인하려면 마스터 대상 에이전트 로그에서 다음과 유사한 추적을 찾습니다.

TCP \<CS 여기 IP를 사용 하 여 대체 IP >: 52739 \<여기 CS IP를 사용 하 여 대체 IP >: 443 SYN_SENT 

TCP    192.168.1.40:52739     192.168.1.40:443      SYN_SENT  // 여기서 IP를 CS IP로 교체

MT 에이전트 로그에 다음과 유사한 추적이 있으면 MT 에이전트가 포트 443에 대해 오류를 보고하는 것입니다.

    #~> (11-20-2018 20:31:51):   ERROR  2508 8408 313 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
    #~> (11-20-2018 20:31:54):   ERROR  2508 8408 314 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
 
이 오류는 다른 애플리케이션에서도 포트 443을 사용하고 있거나 방화벽 설정으로 인해 해당 포트가 차단될 경우에 발생할 수 있습니다.

이 문제를 해결하려면

- 방화벽이 포트 443을 차단하지 않는지 확인합니다.
- 해당 포트를 사용하는 다른 애플리케이션으로 인해 포트에 연결할 수 없으면 앱을 중지한 후 제거합니다.
  - 앱을 중지할 수 없으면 새로운 정리 CS를 설정합니다.
- 구성 서버를 다시 시작합니다.
- IIS 서비스를 다시 시작합니다.

### <a name="configuration-server-is-not-connected-due-to-incorrect-uuid-entries"></a>잘못된 UUID 항목으로 인해 구성 서버가 연결되지 않음

이 오류는 데이터베이스에 여러 CS(구성 서버) 인스턴스 UUID 항목이 있을 때 발생할 수 있습니다. 이 문제는 구성 서버 VM을 복제하는 경우에 자주 발생합니다.

이 문제를 해결하려면

1. vCenter에서 부실/이전 CS VM을 제거합니다. 자세한 내용은 [서버 제거 및 보호 사용 안 함](site-recovery-manage-registration-and-protection.md)을 참조하세요.
2. 구성 서버 VM에 로그인하고 MySQL svsdb1 데이터베이스에 연결합니다. 
3. 다음 쿼리를 실행합니다.

    > [!IMPORTANT]
    >
    > 더 이상 가상 머신을 보호하는 데 사용되지 않는 구성 서버의 부실 항목 또는 복제된 구성 서버의 UUID 세부 정보를 입력하는지 확인합니다. 잘못된 UUID를 입력하면 기존에 보호된 모든 항목의 정보가 손실됩니다.
   
    ```
        MySQL> use svsdb1;
        MySQL> delete from infrastructurevms where infrastructurevmid='<Stale CS VM UUID>';
        MySQL> commit; 
    ```
4. 포털 페이지를 새로 고칩니다.

## <a name="an-infinite-sign-in-loop-occurs-when-entering-your-credentials"></a>자격 증명을 입력하면 무한 로그인 루프가 발생함

구성 서버 OVF에서 올바른 로그인 이름과 암호를 입력한 후 Azure 로그인이 올바른 자격 증명을 계속 묻습니다.

이 문제는 시스템 시간이 올바르지 않을 때 발생할 수 있습니다.

이 문제를 해결하려면

컴퓨터에서 올바른 시간을 설정하고 로그인을 다시 시도하세요. 
 