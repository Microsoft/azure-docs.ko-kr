---
title: Azure AD Domain Services에 Ubuntu VM 참여 | Microsoft Docs
description: Ubuntu Linux 가상 머신을 구성 하 고 Azure AD Domain Services 관리 되는 도메인에 가입 하는 방법을 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/22/2020
ms.author: iainfou
ms.openlocfilehash: 1cf1a97ed6350174511d61d924f893bb209736c2
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712584"
---
# <a name="join-an-ubuntu-linux-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services 관리 되는 도메인에 Ubuntu Linux 가상 컴퓨터 연결

사용자가 단일 자격 증명 집합을 사용 하 여 Azure에서 Vm (가상 머신)에 로그인 할 수 있도록 Vm을 Azure Active Directory Domain Services (AD DS) 관리 되는 도메인에 조인할 수 있습니다. Azure AD DS 관리 되는 도메인에 VM을 가입 하는 경우 도메인의 사용자 계정 및 자격 증명을 사용 하 여 서버에 로그인 하 고 서버를 관리할 수 있습니다. VM의 파일 또는 서비스에 대한 액세스를 제어할 수 있도록 Azure AD DS 관리 되는 도메인의 그룹 멤버 자격도 적용 됩니다.

이 문서에서는 Azure AD DS 관리 되는 도메인에 Ubuntu Linux VM을 조인 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 첫 번째 자습서에서 [Azure Active Directory Domain Services 인스턴스를 만들고 구성합니다][create-azure-ad-ds-instance].
* Azure AD 테넌트의 *Azure AD DC Administrators* 그룹에 속한 멤버인 사용자 계정

## <a name="create-and-connect-to-an-ubuntu-linux-vm"></a>Ubuntu Linux VM 만들기 및 연결

Azure에 기존 Ubuntu Linux VM이 있는 경우 SSH를 사용 하 여 연결 하 고 다음 단계를 계속 진행 하 여 [vm 구성을 시작](#configure-the-hosts-file)합니다.

Ubuntu Linux VM을 만들거나이 문서에서 사용할 테스트 VM을 만들려는 경우 다음 방법 중 하나를 사용할 수 있습니다.

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
* *ubuntu* 는 관리 되는 도메인에 가입 하는 ubuntu VM의 호스트 이름입니다.

이러한 이름을 사용자 고유의 값으로 업데이트 합니다.

```console
127.0.0.1 ubuntu.aadds.contoso.com ubuntu
```

완료 되 면 편집기의 `:wq` 명령을 사용 하 여 *hosts* 파일을 저장 하 고 종료 합니다.

## <a name="install-required-packages"></a>필요한 패키지를 설치합니다.

Vm을 Azure AD DS 관리 되는 도메인에 가입 하려면 VM에 몇 가지 추가 패키지가 필요 합니다. 이러한 패키지를 설치 및 구성 하려면 `apt-get`를 사용 하 여 도메인 가입 도구를 업데이트 하 고 설치 합니다.

Kerberos 설치 중에 *krb5.conf* 패키지는 모두 대문자로 영역 이름을 입력 하 라는 메시지를 표시 합니다. 예를 들어 Azure AD DS 관리 되는 도메인의 이름이 *aadds.contoso.com*인 경우 *aadds를 입력 합니다.* 영역으로 CONTOSO.COM. 설치는 */etc/krb5.conf* 구성 파일의 `[realm]` 및 `[domain_realm]` 섹션을 작성 합니다. 영역을 모두 대문자로 지정 해야 합니다.

```console
sudo apt-get update
sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
```

## <a name="configure-network-time-protocol-ntp"></a>NTP (Network Time Protocol) 구성

도메인 통신이 제대로 작동 하려면 Ubuntu VM의 날짜 및 시간이 Azure AD DS 관리 되는 도메인과 동기화 되어야 합니다. Azure AD DS 관리 되는 도메인의 NTP 호스트 이름을 */etc/ntp.conf* 파일에 추가 합니다.

1. 편집기를 사용 하 여 *ntp* 파일을 엽니다.

    ```console
    sudo vi /etc/ntp.conf
    ```

1. *Ntp* 파일에서 Azure AD DS 관리 되는 도메인의 DNS 이름을 추가할 줄을 만듭니다. 다음 예제에서는 *aadds.contoso.com* 에 대한 항목을 추가 합니다. 사용자 고유의 DNS 이름 사용:

    ```console
    server aadds.contoso.com
    ```

    완료 되 면 편집기의 `:wq` 명령을 사용 하 여 *ntp* 파일을 저장 하 고 종료 합니다.

1. VM이 Azure AD DS 관리 되는 도메인과 동기화 되었는지 확인 하려면 다음 단계를 수행 해야 합니다.

    * NTP 서버 중지
    * 관리 되는 도메인에서 날짜 및 시간 업데이트
    * NTP 서비스를 시작 합니다.

    다음 명령을 실행 하 여 이러한 단계를 완료 합니다. `ntpdate` 명령을 사용 하 여 사용자 고유의 DNS 이름을 사용 합니다.

    ```console
    sudo systemctl stop ntp
    sudo ntpdate aadds.contoso.com
    sudo systemctl start ntp
    ```

## <a name="join-vm-to-the-managed-domain"></a>VM을 관리 되는 도메인에 가입

필요한 패키지가 VM에 설치 되어 있고 NTP가 구성 되었으므로 Azure AD DS 관리 되는 도메인에 VM을 가입 시킵니다.

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
    sudo realm join --verbose AADDS.CONTOSO.COM -U 'contosoadmin@AADDS.CONTOSO.COM' --install=/
    ```

VM을 Azure AD DS 관리 되는 도메인에 가입 하는 데 몇 분 정도 걸립니다. 다음 예제 출력에서는 VM이 Azure AD DS 관리 되는 도메인에 성공적으로 가입 되었음을 보여 줍니다.

```output
Successfully enrolled machine in realm
```

VM이 도메인 가입 프로세스를 성공적으로 완료할 수 없는 경우 VM의 네트워크 보안 그룹이 TCP + UDP 포트 464의 아웃 바운드 Kerberos 트래픽을 Azure AD DS 관리 되는 도메인에 대한 가상 네트워크 서브넷으로 허용 하는지 확인 합니다.

## <a name="update-the-sssd-configuration"></a>SSSD 구성 업데이트

이전 단계에서 설치 된 패키지 중 하나는 SSSD (시스템 보안 서비스 디먼) 용입니다. 사용자가 도메인 자격 증명을 사용 하 여 VM에 로그인 하려고 하면 SSSD는 인증 공급자에 게 요청을 릴레이 합니다. 이 시나리오에서 SSSD는 Azure AD DS를 사용 하 여 요청을 인증 합니다.

1. 편집기를 사용 하 여 *sssd* 파일을 엽니다.

    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

1. 다음과 같이 *use_fully_qualified_names* 에 대한 줄을 주석으로 처리 합니다.

    ```console
    # use_fully_qualified_names = True
    ```

    완료 되 면 편집기의 `:wq` 명령을 사용 하 여 *sssd* 파일을 저장 하 고 종료 합니다.

1. 변경 내용을 적용 하려면 SSSD 서비스를 다시 시작 합니다.

    ```console
    sudo service sssd restart
    ```

## <a name="configure-user-account-and-group-settings"></a>사용자 계정 및 그룹 설정 구성

Azure AD DS 관리 되는 도메인에 가입 되 고 인증을 위해 구성 된 VM을 사용 하 여 몇 가지 사용자 구성 옵션을 완료할 수 있습니다. 이러한 구성 변경에는 암호 기반 인증 허용, 도메인 사용자가 처음으로 로그인 할 때 로컬 VM에서 홈 디렉터리 자동 생성이 포함 됩니다.

### <a name="allow-password-authentication-for-ssh"></a>SSH에 대한 암호 인증 허용

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

1. 변경 내용을 적용 하 고 사용자가 암호를 사용 하 여 로그인 하도록 하려면 SSH 서비스를 다시 시작 합니다.

    ```console
    sudo systemctl restart ssh
    ```

### <a name="configure-automatic-home-directory-creation"></a>자동 홈 디렉터리 생성 구성

사용자가 처음으로 로그인 할 때 홈 디렉터리의 자동 생성을 사용 하도록 설정 하려면 다음 단계를 완료 합니다.

1. 편집기에서 */etc/pam.d/common-session* 파일을 엽니다.

    ```console
    sudo vi /etc/pam.d/common-session
    ```

1. 이 파일에서 줄 `session optional pam_sss.so`아래에 다음 줄을 추가 합니다.

    ```console
    session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
    ```

    완료 되 면 편집기의 `:wq` 명령을 사용 하 여 *공용 세션* 파일을 저장 하 고 종료 합니다.

### <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>'AAD DC Administrators' 그룹 sudo 권한 부여

*AAD DC 관리자* 그룹 구성원에 게 Ubuntu VM에 대한 관리 권한을 부여 하려면 */etc/sudoers*에 항목을 추가 합니다. 일단 추가 되 면 *AAD DC 관리자* 그룹의 구성원이 Ubuntu VM에서 `sudo` 명령을 사용할 수 있습니다.

1. 편집할 *sudoers* 파일을 엽니다.

    ```console
    sudo visudo
    ```

1. */Etc/sudoers* 파일의 끝에 다음 항목을 추가 합니다.

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

    완료 되 면 `Ctrl-X` 명령을 사용 하 여 편집기를 저장 하 고 종료 합니다.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>도메인 계정을 사용 하 여 VM에 로그인 합니다.

VM이 Azure AD DS 관리 되는 도메인에 성공적으로 가입 되었는지 확인 하려면 도메인 사용자 계정을 사용 하 여 새 SSH 연결을 시작 합니다. 홈 디렉터리가 만들어지고 도메인의 그룹 구성원이 적용 되었는지 확인 합니다.

1. 콘솔에서 새 SSH 연결을 만듭니다. `contosoadmin@aadds.contoso.com`와 같은 `ssh -l` 명령을 사용 하 여 관리 되는 도메인에 속하는 도메인 계정을 사용한 다음 VM의 주소 (예: *ubuntu.aadds.contoso.com*)를 입력 합니다. Azure Cloud Shell 사용 하는 경우 내부 DNS 이름이 아닌 VM의 공용 IP 주소를 사용 합니다.

    ```console
    ssh -l contosoadmin@AADDS.CONTOSO.com ubuntu.aadds.contoso.com
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
    sudo apt-get update
    ```

## <a name="next-steps"></a>다음 단계

VM을 Azure AD DS 관리 되는 도메인에 연결 하거나 도메인 계정으로 로그인 하는 데 문제가 있는 경우 [도메인 가입 문제 해결](join-windows-vm.md#troubleshoot-domain-join-issues)을 참조 하세요.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
