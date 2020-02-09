---
title: RHEL VM을 Azure AD Domain Services에 조인 | Microsoft Docs
description: Red Hat Enterprise Linux 가상 머신을 구성 하 고 Azure AD Domain Services 관리 되는 도메인에 가입 하는 방법을 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: iainfou
ms.openlocfilehash: 93cb200751c1c107ae844ffd274d83dd997293de
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712589"
---
# <a name="join-a-red-hat-enterprise-linux-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services 관리 되는 도메인에 Red Hat Enterprise Linux 가상 컴퓨터 연결

사용자가 단일 자격 증명 집합을 사용 하 여 Azure에서 Vm (가상 머신)에 로그인 할 수 있도록 Vm을 Azure Active Directory Domain Services (AD DS) 관리 되는 도메인에 조인할 수 있습니다. Azure AD DS 관리 되는 도메인에 VM을 가입 하는 경우 도메인의 사용자 계정 및 자격 증명을 사용 하 여 서버에 로그인 하 고 서버를 관리할 수 있습니다. VM의 파일 또는 서비스에 대한 액세스를 제어할 수 있도록 Azure AD DS 관리 되는 도메인의 그룹 멤버 자격도 적용 됩니다.

이 문서에서는 Azure AD DS 관리 되는 도메인에 Red Hat Enterprise Linux (RHEL) VM을 조인 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 첫 번째 자습서에서 [Azure Active Directory Domain Services 인스턴스를 만들고 구성합니다][create-azure-ad-ds-instance].
* Azure AD 테넌트의 *Azure AD DC Administrators* 그룹에 속한 멤버인 사용자 계정

## <a name="create-and-connect-to-a-rhel-linux-vm"></a>RHEL Linux VM 만들기 및 연결

Azure에 기존 RHEL Linux VM이 있는 경우 SSH를 사용 하 여 연결 하 고 다음 단계를 계속 진행 하 여 [vm 구성을 시작](#configure-the-hosts-file)합니다.

RHEL Linux VM을 만들거나이 문서에서 사용할 테스트 VM을 만들려는 경우 다음 방법 중 하나를 사용할 수 있습니다.

* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

VM을 만들 때 VM이 Azure AD DS 관리 되는 도메인과 통신할 수 있는지 확인 하려면 가상 네트워크 설정에 주의를 기울여야 합니다.

* Azure AD Domain Services를 사용 하도록 설정한 것과 동일한 또는 피어 링 가상 네트워크에 VM을 배포 합니다.
* Azure AD Domain Services 인스턴스와 다른 서브넷에 VM을 배포 합니다.

VM을 배포한 후에는 SSH를 사용 하 여 VM에 연결 하는 단계를 수행 합니다.

## <a name="configure-the-hosts-file"></a>hosts 파일 구성

VM 호스트 이름이 관리 되는 도메인에 대해 올바르게 구성 되었는지 확인 하려면 */etc/hosts* 파일을 편집 하 고 호스트 이름을 설정 합니다.

```console
sudo vi /etc/hosts
```

*Hosts* 파일에서 *localhost* 주소를 업데이트 합니다. 다음 예제에서,

* *aadds.contoso.com* 는 Azure AD DS 관리 되는 도메인의 DNS 도메인 이름입니다.
* *rhel* 는 관리 되는 도메인에 가입 하는 rhel VM의 호스트 이름입니다.

이러한 이름을 사용자 고유의 값으로 업데이트 합니다.

```console
127.0.0.1 rhel rhel.aadds.contoso.com
```

완료 되 면 편집기의 `:wq` 명령을 사용 하 여 *hosts* 파일을 저장 하 고 종료 합니다.

## <a name="install-required-packages"></a>필요한 패키지를 설치합니다.

Vm을 Azure AD DS 관리 되는 도메인에 가입 하려면 VM에 몇 가지 추가 패키지가 필요 합니다. 이러한 패키지를 설치 하 고 구성 하려면 `yum`를 사용 하 여 도메인 가입 도구를 업데이트 하 고 설치 합니다. RHEL 및 RHEL .x 간에는 몇 가지 차이점이 있으므로이 문서의 나머지 섹션에서는 배포판 버전에 적절 한 명령을 사용 합니다.

**RHEL 7**

```console
sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```

**RHEL 6**

```console
sudo yum install adcli sssd authconfig krb5-workstation
```

## <a name="join-vm-to-the-managed-domain"></a>VM을 관리 되는 도메인에 가입

필요한 패키지가 VM에 설치 되었으므로 Azure AD DS 관리 되는 도메인에 VM을 가입 시킵니다. RHEL 배포판 버전에 대한 적절 한 단계를 다시 사용 합니다.

### <a name="rhel-7"></a>RHEL 7

1. `realm discover` 명령을 사용 하 여 Azure AD DS 관리 되는 도메인을 검색 합니다. 다음 예제에서는 영역 Aadds를 검색 합니다 *. CONTOSO.COM*. Azure AD DS 관리 되는 도메인 이름을 모두 대문자로 지정 합니다.

    ```console
    sudo realm discover AADDS.CONTOSO.COM
    ```

   `realm discover` 명령이 Azure AD DS 관리 되는 도메인을 찾을 수 없는 경우 다음 문제 해결 단계를 검토 합니다.

    * VM에서 도메인에 연결할 수 있는지 확인 합니다. `ping aadds.contoso.com`를 시도 하 여 긍정 회신이 반환 되는지 확인 합니다.
    * VM이 Azure AD DS 관리 되는 도메인을 사용할 수 있는 동일한 또는 피어 링 가상 네트워크에 배포 되었는지 확인 합니다.
    * 가상 네트워크에 대한 DNS 서버 설정이 Azure AD DS 관리 되는 도메인의 도메인 컨트롤러를 가리키도록 업데이트 되었는지 확인 합니다.

1. 이제 `kinit` 명령을 사용 하 여 Kerberos를 초기화 합니다. *AAD DC 관리자* 그룹에 속한 사용자를 지정 합니다. 필요한 경우 [AZURE AD의 그룹에 사용자 계정을 추가](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md)합니다.

    Azure AD DS 관리 되는 도메인 이름을 모두 대문자로 입력 해야 합니다. 다음 예에서는 `contosoadmin@aadds.contoso.com` 라는 계정이 Kerberos를 초기화 하는 데 사용 됩니다. *AAD DC 관리자* 그룹의 구성원 인 사용자 계정을 입력 합니다.

    ```console
    kinit contosoadmin@AADDS.CONTOSO.COM
    ```

1. 마지막으로 `realm join` 명령을 사용 하 여 Azure AD DS 관리 되는 도메인에 컴퓨터를 가입 시킵니다. 이전 `kinit` 명령에서 지정한 *AAD DC Administrators* 그룹의 구성원 인 동일한 사용자 계정 (예: `contosoadmin@AADDS.CONTOSO.COM`)을 사용 합니다.

    ```console
    sudo realm join --verbose AADDS.CONTOSO.COM -U 'contosoadmin@AADDS.CONTOSO.COM'
    ```

VM을 Azure AD DS 관리 되는 도메인에 가입 하는 데 몇 분 정도 걸립니다. 다음 예제 출력에서는 VM이 Azure AD DS 관리 되는 도메인에 성공적으로 가입 되었음을 보여 줍니다.

```output
Successfully enrolled machine in realm
```

### <a name="rhel-6"></a>RHEL 6

1. `adcli info` 명령을 사용 하 여 Azure AD DS 관리 되는 도메인을 검색 합니다. 다음 예제에서는 영역 Addds를 검색 합니다 *. CONTOSO.COM*. Azure AD DS 관리 되는 도메인 이름을 모두 대문자로 지정 합니다.

    ```console
    sudo adcli info aadds.contoso.com
    ```

   `adcli info` 명령이 Azure AD DS 관리 되는 도메인을 찾을 수 없는 경우 다음 문제 해결 단계를 검토 합니다.

    * VM에서 도메인에 연결할 수 있는지 확인 합니다. `ping aadds.contoso.com`를 시도 하 여 긍정 회신이 반환 되는지 확인 합니다.
    * VM이 Azure AD DS 관리 되는 도메인을 사용할 수 있는 동일한 또는 피어 링 가상 네트워크에 배포 되었는지 확인 합니다.
    * 가상 네트워크에 대한 DNS 서버 설정이 Azure AD DS 관리 되는 도메인의 도메인 컨트롤러를 가리키도록 업데이트 되었는지 확인 합니다.

1. 먼저 `adcli join` 명령을 사용 하 여 도메인에 가입 합니다 .이 명령은 컴퓨터를 인증 하는 keytab도 만듭니다. *AAD DC Administrators* 그룹의 구성원 인 사용자 계정을 사용 합니다.

    ```console
    sudo adcli join aadds.contoso.com -U contosoadmin
    ```

1. 이제 `/ect/krb5.conf`를 구성 하 고 `/etc/sssd/sssd.conf` 파일을 만들어 `aadds.contoso.com` Active Directory 도메인을 사용 합니다.
   `AADDS.CONTOSO.COM` 사용자의 도메인 이름으로 대체 되었는지 확인 합니다.

    편집기를 사용 하 여 `/ect/krb5.conf` 파일을 엽니다.

    ```console
    sudo vi /etc/krb5.conf
    ```

    다음 샘플과 일치 하도록 `krb5.conf` 파일을 업데이트 합니다.

    ```console
    [logging]
     default = FILE:/var/log/krb5libs.log
     kdc = FILE:/var/log/krb5kdc.log
     admin_server = FILE:/var/log/kadmind.log
    
    [libdefaults]
     default_realm = AADDS.CONTOSO.COM
     dns_lookup_realm = true
     dns_lookup_kdc = true
     ticket_lifetime = 24h
     renew_lifetime = 7d
     forwardable = true
    
    [realms]
     AADDS.CONTOSO.COM = {
     kdc = AADDS.CONTOSO.COM
     admin_server = AADDS.CONTOSO.COM
     }
    
    [domain_realm]
     .CONTOSO.COM = AADDS.CONTOSO.COM
     CONTOSO.COM = AADDS.CONTOSO.COM
    ```
    
   `/etc/sssd/sssd.conf` 파일을 만듭니다.
    
    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

    다음 샘플과 일치 하도록 `sssd.conf` 파일을 업데이트 합니다.

    ```console
    [sssd]
     services = nss, pam, ssh, autofs
     config_file_version = 2
     domains = AADDS.CONTOSO.COM
    
    [domain/AADDS.CONTOSO.COM]
    
     id_provider = ad
    ```

1. `/etc/sssd/sssd.conf` 권한이 600이 고 루트 사용자가 소유 하 고 있는지 확인 합니다.

    ```console
    sudo chmod 600 /etc/sssd/sssd.conf
    sudo chown root:root /etc/sssd/sssd.conf
    ```

1. `authconfig`를 사용 하 여 AD Linux 통합에 대해 VM에 지시 합니다.

    ```console
    sudo authconfig --enablesssd --enablesssdauth --update
    ```

1. Sssd 서비스를 시작 하 고 사용 하도록 설정 합니다.

    ```console
    sudo service sssd start
    sudo chkconfig sssd on
    ```

VM이 도메인 가입 프로세스를 성공적으로 완료할 수 없는 경우 VM의 네트워크 보안 그룹이 TCP + UDP 포트 464의 아웃 바운드 Kerberos 트래픽을 Azure AD DS 관리 되는 도메인에 대한 가상 네트워크 서브넷으로 허용 하는지 확인 합니다.

이제 `getent`를 사용 하 여 사용자 AD 정보를 쿼리할 수 있는지 확인 합니다.

```console
sudo getent passwd contosoadmin
```

## <a name="allow-password-authentication-for-ssh"></a>SSH에 대한 암호 인증 허용

기본적으로 사용자는 SSH 공개 키 기반 인증을 사용 하 여 VM에만 로그인 할 수 있습니다. 암호 기반 인증에 실패 합니다. VM을 Azure AD DS 관리 되는 도메인에 가입 하는 경우 해당 도메인 계정은 암호 기반 인증을 사용 해야 합니다. 다음과 같이 암호 기반 인증을 허용 하도록 SSH 구성을 업데이트 합니다.

1. 편집기를 사용 하 여 *sshd_conf* 파일을 엽니다.

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. *Passwordauthentication* 의 줄을 *예*로 업데이트 합니다.

    ```console
    PasswordAuthentication yes
    ```

    완료 되 면 편집기의 `:wq` 명령을 사용 하 여 *sshd_conf* 파일을 저장 하 고 종료 합니다.

1. 변경 내용을 적용 하 고 사용자가 암호를 사용 하 여 로그인 하도록 하려면 RHEL 배포판 버전에 대한 SSH 서비스를 다시 시작 합니다.

   **RHEL 7**

    ```console
    sudo systemctl restart sshd
    ```

   **RHEL 6**

    ```console
    sudo service sshd restart
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>'AAD DC Administrators' 그룹 sudo 권한 부여

*AAD DC 관리자* 의 구성원에 게 RHEL VM에 대한 관리 권한을 부여 하려면 */etc/sudoers*에 항목을 추가 합니다. 추가 된 후에는 *AAD DC Administrators* 그룹의 구성원이 RHEL VM에서 `sudo` 명령을 사용할 수 있습니다.

1. 편집할 *sudoers* 파일을 엽니다.

    ```console
    sudo visudo
    ```

1. */Etc/sudoers* 파일의 끝에 다음 항목을 추가 합니다. *AAD DC Administrators* 그룹은 이름에 공백을 포함 하므로 그룹 이름에 백슬래시 이스케이프 문자를 포함 합니다. *Aadds.contoso.com*와 같은 고유한 도메인 이름을 추가 합니다.

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@aadds.contoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    완료 되 면 편집기의 `:wq` 명령을 사용 하 여 편집기를 저장 하 고 종료 합니다.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>도메인 계정을 사용 하 여 VM에 로그인 합니다.

VM이 Azure AD DS 관리 되는 도메인에 성공적으로 가입 되었는지 확인 하려면 도메인 사용자 계정을 사용 하 여 새 SSH 연결을 시작 합니다. 홈 디렉터리가 만들어지고 도메인의 그룹 구성원이 적용 되었는지 확인 합니다.

1. 콘솔에서 새 SSH 연결을 만듭니다. `contosoadmin@aadds.contoso.com`와 같은 `ssh -l` 명령을 사용 하 여 관리 되는 도메인에 속하는 도메인 계정을 사용한 다음 VM의 주소 (예: *rhel.aadds.contoso.com*)를 입력 합니다. Azure Cloud Shell 사용 하는 경우 내부 DNS 이름이 아닌 VM의 공용 IP 주소를 사용 합니다.

    ```console
    ssh -l contosoadmin@AADDS.CONTOSO.com rhel.contoso.com
    ```

1. VM에 성공적으로 연결한 경우 홈 디렉터리가 올바르게 초기화 되었는지 확인 합니다.

    ```console
    pwd
    ```

    사용자 계정과 일치 하는 고유한 디렉터리를 사용 하 여 */home* 디렉터리에 있어야 합니다.

1. 이제 그룹 멤버 자격이 올바르게 확인 되는지 확인 합니다.

    ```console
    id
    ```

    Azure AD DS 관리 되는 도메인에서 그룹 멤버 자격을 확인 해야 합니다.

1. *AAD DC 관리자* 그룹의 구성원으로 VM에 로그인 한 경우 `sudo` 명령을 올바르게 사용할 수 있는지 확인 합니다.

    ```console
    sudo yum update
    ```

## <a name="next-steps"></a>다음 단계

VM을 Azure AD DS 관리 되는 도메인에 연결 하거나 도메인 계정으로 로그인 하는 데 문제가 있는 경우 [도메인 가입 문제 해결](join-windows-vm.md#troubleshoot-domain-join-issues)을 참조 하세요.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
