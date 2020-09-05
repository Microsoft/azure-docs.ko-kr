---
title: Azure AD Domain Services에 SLE VM 조인 | Microsoft Docs
description: SUSE Linux Enterprise 가상 머신을 구성 하 고 관리 되는 Azure AD Domain Services 도메인에 가입 하는 방법을 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 08/12/2020
ms.author: iainfou
ms.openlocfilehash: 9f50be95e456802c6ad403acd6a2f539780e53a2
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2020
ms.locfileid: "88251184"
---
# <a name="join-a-suse-linux-enterprise-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>SUSE Linux Enterprise 가상 머신을 Azure Active Directory Domain Services 관리 되는 도메인에 가입

사용자가 단일 자격 증명 집합을 사용 하 여 Azure에서 Vm (가상 머신)에 로그인 할 수 있도록 Vm을 Azure Active Directory Domain Services (Azure AD DS) 관리 되는 도메인에 조인할 수 있습니다. Azure AD DS 관리 되는 도메인에 VM을 가입 하는 경우 도메인의 사용자 계정 및 자격 증명을 사용 하 여 서버에 로그인 하 고 서버를 관리할 수 있습니다. 관리 되는 도메인의 그룹 멤버 자격도 적용 되어 VM의 파일 또는 서비스에 대 한 액세스를 제어할 수 있습니다.

이 문서에서는 SUSE Linux Enterprise (SLE) VM을 관리 되는 도메인에 조인 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 첫 번째 자습서에서 [Azure Active Directory Domain Services 관리되는 도메인을 만들고 구성합니다][create-azure-ad-ds-instance].
* 관리되는 도메인의 일부인 사용자 계정

## <a name="create-and-connect-to-a-sle-linux-vm"></a>SLE Linux VM 만들기 및 연결

Azure에 기존 SLE Linux VM이 있는 경우 SSH를 사용 하 여 연결 하 고 다음 단계를 계속 진행 하 여 [vm 구성을 시작](#configure-the-hosts-file)합니다.

SLE Linux VM을 만들어야 하거나이 문서에서 사용할 테스트 VM을 만들려는 경우 다음 방법 중 하나를 사용할 수 있습니다.

* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

VM을 만들 때 VM이 관리 되는 도메인과 통신할 수 있는지 확인 하려면 가상 네트워크 설정에 주의를 기울여야 합니다.

* Azure AD Domain Services를 사용 하도록 설정한 것과 동일한 또는 피어 링 가상 네트워크에 VM을 배포 합니다.
* Azure AD Domain Services 관리 되는 도메인과 다른 서브넷에 VM을 배포 합니다.

VM을 배포한 후에는 SSH를 사용 하 여 VM에 연결 하는 단계를 수행 합니다.

## <a name="configure-the-hosts-file"></a>hosts 파일 구성

VM 호스트 이름이 관리 되는 도메인에 대해 올바르게 구성 되었는지 확인 하려면 */etc/hosts* 파일을 편집 하 고 호스트 이름을 설정 합니다.

```console
sudo vi /etc/hosts
```

*Hosts* 파일에서 *localhost* 주소를 업데이트 합니다. 다음 예제에서는

* *aaddscontoso.com* 는 관리 되는 도메인의 DNS 도메인 이름입니다.
* *q2gr* 는 관리 되는 도메인에 가입 하는 SLE VM의 호스트 이름입니다.

이러한 이름을 사용자 고유의 값으로 업데이트 합니다.

```console
127.0.0.1 linux-q2gr linux-q2gr.aaddscontoso.com
```

완료 되 면 편집기의 명령을 사용 하 여 *hosts* 파일을 저장 하 고 종료 `:wq` 합니다.

## <a name="join-vm-to-the-managed-domain-using-sssd"></a>SSSD를 사용 하 여 관리 되는 도메인에 VM 연결

**Sssd** 및 Yast의 *사용자 로그온 관리* 모듈을 사용 하 여 관리 되는 도메인에 가입 하려면 다음 단계를 완료 합니다.

1. *사용자 로그온 관리* yast 모듈을 설치 합니다.

    ```bash
    sudo zypper install yast2-auth-client
    ```

1. YaST를 엽니다.

1. 나중에 DNS 자동 검색을 성공적으로 사용 하려면 관리 되는 도메인 IP 주소 ( *Active Directory 서버*)를 클라이언트의 이름 서버로 구성 합니다.

    YaST에서 **시스템 > 네트워크 설정**을 선택 합니다.

1. *Hostname/DNS* 탭을 선택한 다음, 텍스트 상자 *이름 서버 1*에 관리 되는 도메인의 IP 주소를 입력 합니다. 이러한 IP 주소는 관리 되는 도메인에 대 한 Azure Portal의 *속성* 창에 표시 됩니다 (예: *10.0.2.4* 및 *10.0.2.5*).

    관리 되는 도메인 IP 주소를 추가한 다음 **확인**을 선택 합니다.

1. Yast 주 창에서 *Network Services*  >  *사용자 로그온 관리*를 선택 합니다.

    다음 예제 스크린샷에 표시 된 것 처럼 모듈에는 컴퓨터의 여러 네트워크 속성 및 현재 사용 중인 인증 방법을 보여 주는 개요가 열립니다.

    ![YaST의 사용자 로그인 관리 창에 대 한 예제 스크린샷](./media/join-suse-linux-vm/overview-window.png)

    편집을 시작 하려면 **설정 변경**을 선택 합니다.

VM을 관리 되는 도메인에 가입 하려면 다음 단계를 완료 합니다.

1. 대화 상자에서 **도메인 추가**를 선택 합니다.

1. *Aaddscontoso.com*와 같은 올바른 *도메인 이름을*지정 하 고, id 데이터 및 인증에 사용할 서비스를 지정 합니다. 두 가지 모두에 대해 *Microsoft Active Directory* 를 선택 합니다.

    *도메인 사용* 옵션이 선택 되어 있는지 확인 합니다.

1. 준비되면 **확인**을 선택합니다.

1. 다음 대화 상자에서 기본 설정을 적용 한 다음 **확인**을 선택 합니다.

1. VM은 필요에 따라 추가 소프트웨어를 설치 하 고 관리 되는 도메인을 사용할 수 있는지 확인 합니다.

    모든 항목이 올바르면 VM이 관리 되는 도메인을 검색 했지만 *아직 등록 하지*않았음을 나타내는 다음 예제 대화 상자가 표시 됩니다.

    ![YaST의 Active Directory 등록 창의 예제 스크린샷](./media/join-suse-linux-vm/enroll-window.png)

1. 대화 상자에서 관리 되는 도메인의 일부인 사용자의 사용자 *이름* 및 *암호* 를 지정 합니다. 필요한 경우 [AZURE AD의 그룹에 사용자 계정을 추가](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md)합니다.

    현재 도메인이 Samba에 대해 사용 하도록 설정 되었는지 확인 하려면 *이 AD에서 작업 하도록 samba 구성 덮어쓰기*를 활성화 합니다.

1. 등록 하려면 **확인**을 선택 합니다.

1. 성공적으로 등록 되었음을 확인 하는 메시지가 표시 됩니다. 완료 하려면 **확인**을 선택 합니다.

VM이 관리 되는 도메인에 등록 된 후 다음 예제 스크린샷에 표시 된 것 처럼 *도메인 사용자 로그온 관리*를 사용 하 여 클라이언트를 구성 합니다.

![YaST에서 도메인 사용자 로그온 창의 예 스크린샷](./media/join-suse-linux-vm/manage-domain-user-logon-window.png)

1. 관리 되는 도메인에서 제공 하는 데이터를 사용 하 여 로그인 할 수 있도록 하려면 *도메인 사용자 로그온 허용*확인란을 선택 합니다.

1. 필요에 따라 *도메인 데이터 원본 사용*에서 사용자 환경에 필요한 추가 데이터 원본을 선택 합니다. 이러한 옵션에는 **sudo** 를 사용할 수 있는 사용자 또는 사용할 수 있는 네트워크 드라이브가 포함 됩니다.

1. 관리 되는 도메인의 사용자가 VM에서 홈 디렉터리를 갖도록 허용 하려면 *홈 디렉터리 만들기*확인란을 선택 합니다.

1. 세로 막대에서 **서비스 옵션 › Name 스위치**를 선택 하 고 *확장 옵션*을 선택 합니다. 해당 창에서 *fallback_homedir* 또는 *override_homedir*을 선택한 다음 **추가**를 선택 합니다.

1. 홈 디렉터리 위치에 대 한 값을 지정 합니다. 홈 디렉터리가 */home/USER_NAME*형식을 따르도록 하려면 */home/%u*를 사용 합니다. 가능한 변수에 대 한 자세한 내용은 sssd.. s q s 페이지 ( `man 5 sssd.conf` ), *override_homedir*섹션을 참조 하십시오.

1. **확인**을 선택합니다.

1. 변경 내용을 저장 하려면 **확인**을 선택 합니다. 이제 표시 된 값이 올바른지 확인 합니다. 대화 상자를 나가려면 **취소**를 선택 합니다.

1. Sssd 및 Winbind을 동시에 실행 하려는 경우 (예: SSSD를 통해 조인 하는 경우, Samba 파일 서버를 실행 하는 경우) Samba 옵션 *kerberos 메서드* 는 smb의 *암호 및 keytab* 로 설정 해야 합니다. Sssd 옵션 *ad_update_samba_machine_account_password* 는 sssd에서 *true* 로 설정 해야 합니다. 이러한 옵션은 시스템 keytab 동기화 되지 않도록 방지 합니다.

## <a name="join-vm-to-the-managed-domain-using-winbind"></a>Winbind를 사용 하 여 VM을 관리 되는 도메인에 가입

**Winbind** 및 yast의 *Windows 도메인 멤버 자격* 모듈을 사용 하 여 관리 되는 도메인에 가입 하려면 다음 단계를 완료 합니다.

1. YaST에서 **Network Services > Windows 도메인 멤버 자격**을 선택 합니다.

1. *Windows 도메인 멤버 자격* 화면의 *도메인 또는 작업 그룹* 에서 가입할 도메인을 입력 합니다. 관리 되는 도메인 이름 (예: *aaddscontoso.com*)을 입력 합니다.

    ![YaST의 Windows 도메인 멤버 자격 창에 대 한 예제 스크린샷](./media/join-suse-linux-vm/samba-client-window.png)

1. Linux 인증용 SMB 원본을 사용 하려면 *Linux 인증에 Smb 정보 사용*옵션을 선택 합니다.

1. VM에서 관리 되는 도메인 사용자에 대 한 로컬 홈 디렉터리를 자동으로 만들려면 *로그인 시 홈 디렉터리 만들기*옵션을 선택 합니다.

1. 관리 되는 도메인을 일시적으로 사용할 수 없는 경우에도 도메인 사용자가 로그인 할 수 있도록 하려면 *오프 라인 인증* 에 대 한 옵션을 선택 합니다.

1. Samba 사용자 및 그룹에 대 한 UID 및 GID 범위를 변경 하려면 *전문가 설정*을 선택 합니다.

1. *Ntp 구성*을 선택 하 여 관리 되는 도메인에 대 한 ntp 시간 동기화를 구성 합니다. 관리 되는 도메인의 IP 주소를 입력 합니다. 이러한 IP 주소는 관리 되는 도메인에 대 한 Azure Portal의 *속성* 창에 표시 됩니다 (예: *10.0.2.4* 및 *10.0.2.5*).

1. **확인** 을 선택 하 고 메시지가 표시 되 면 도메인 가입을 확인 합니다.

1. 관리 되는 도메인의 관리자에 대 한 암호를 입력 하 고 **확인을**선택 합니다.

    ![SLE VM을 관리 되는 도메인에 연결 하는 경우 인증 대화 상자 프롬프트의 예제 스크린샷](./media/join-suse-linux-vm/domain-join-authentication-prompt.png)

관리 되는 도메인에 가입한 후에는 데스크톱 또는 콘솔의 디스플레이 관리자를 사용 하 여 워크스테이션에서 해당 도메인에 로그인 할 수 있습니다.

## <a name="allow-password-authentication-for-ssh"></a>SSH에 대 한 암호 인증 허용

기본적으로 사용자는 SSH 공개 키 기반 인증을 사용 하 여 VM에만 로그인 할 수 있습니다. 암호 기반 인증에 실패 합니다. VM을 관리 되는 도메인에 가입 하는 경우 해당 도메인 계정은 암호 기반 인증을 사용 해야 합니다. 다음과 같이 암호 기반 인증을 허용 하도록 SSH 구성을 업데이트 합니다.

1. 편집기를 사용 하 여 *sshd_conf* 파일을 엽니다.

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. *Passwordauthentication* 의 줄을 *예*로 업데이트 합니다.

    ```console
    PasswordAuthentication yes
    ```

    완료 되 면 편집기의 명령을 사용 하 여 *sshd_conf* 파일을 저장 하 고 종료 `:wq` 합니다.

1. 변경 내용을 적용 하 고 사용자가 암호를 사용 하 여 로그인 하도록 하려면 SSH 서비스를 다시 시작 합니다.

    ```console
    sudo systemctl restart sshd
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>'AAD DC Administrators' 그룹 sudo 권한 부여

*AAD DC 관리자* 그룹 구성원에 게 SLE VM에 대 한 관리 권한을 부여 하려면 */etc/sudoers*에 항목을 추가 합니다. 추가 된 후에는 *AAD DC 관리자* 그룹의 구성원이 `sudo` SLE VM에서 명령을 사용할 수 있습니다.

1. 편집할 *sudoers* 파일을 엽니다.

    ```console
    sudo visudo
    ```

1. */Etc/sudoers* 파일의 끝에 다음 항목을 추가 합니다. *AAD DC Administrators* 그룹은 이름에 공백을 포함 하므로 그룹 이름에 백슬래시 이스케이프 문자를 포함 합니다. *Aaddscontoso.com*와 같은 고유한 도메인 이름을 추가 합니다.

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@aaddscontoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    완료 되 면 편집기의 명령을 사용 하 여 편집기를 저장 하 고 종료 `:wq` 합니다.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>도메인 계정을 사용 하 여 VM에 로그인 합니다.

VM이 관리 되는 도메인에 성공적으로 가입 되었는지 확인 하려면 도메인 사용자 계정을 사용 하 여 새 SSH 연결을 시작 합니다. 홈 디렉터리가 만들어지고 도메인의 그룹 구성원이 적용 되었는지 확인 합니다.

1. 콘솔에서 새 SSH 연결을 만듭니다. 명령을 사용 하 여 관리 되는 도메인에 속하는 도메인 계정을 사용 하 고 (예:) `ssh -l` `contosoadmin@aaddscontoso.com` *LINUX-Q2GR.AADDSCONTOSO.COM*와 같은 VM의 주소를 입력 합니다. Azure Cloud Shell 사용 하는 경우 내부 DNS 이름이 아닌 VM의 공용 IP 주소를 사용 합니다.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com linux-q2gr.aaddscontoso.com
    ```

2. VM에 성공적으로 연결한 경우 홈 디렉터리가 올바르게 초기화 되었는지 확인 합니다.

    ```console
    pwd
    ```

    사용자 계정과 일치 하는 고유한 디렉터리를 사용 하 여 */home* 디렉터리에 있어야 합니다.

3. 이제 그룹 멤버 자격이 올바르게 확인 되는지 확인 합니다.

    ```console
    id
    ```

    관리 되는 도메인에서 그룹 멤버 자격을 확인 해야 합니다.

4. *AAD DC 관리자* 그룹의 구성원으로 VM에 로그인 한 경우 명령을 올바르게 사용할 수 있는지 확인 합니다 `sudo` .

    ```console
    sudo zypper update
    ```

## <a name="next-steps"></a>다음 단계

VM을 관리 되는 도메인에 연결 하거나 도메인 계정으로 로그인 하는 데 문제가 있는 경우 [도메인 가입 문제 해결](join-windows-vm.md#troubleshoot-domain-join-issues)을 참조 하세요.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
