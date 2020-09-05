---
title: Azure AD Domain Services에 대 한 Azure AD 응용 프로그램 프록시 배포 | Microsoft Docs
description: Azure Active Directory Domain Services 관리 되는 도메인에서 Azure Active Directory 응용 프로그램 프록시를 배포 및 구성 하 여 원격 작업자를 위한 내부 응용 프로그램에 대 한 보안 액세스를 제공 하는 방법을 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: iainfou
ms.openlocfilehash: f02f8c0c5aabc48e7b9966898028485a4687f413
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723081"
---
# <a name="deploy-azure-ad-application-proxy-for-secure-access-to-internal-applications-in-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services 관리 되는 도메인의 내부 응용 프로그램에 안전 하 게 액세스할 수 있도록 Azure AD 응용 프로그램 프록시 배포

Azure AD Domain Services (Azure AD DS)를 사용 하 여 온-프레미스에서 실행 되는 레거시 응용 프로그램을 Azure로 리프트 앤 시프트 할 수 있습니다. 그러면 AD (Azure Active Directory) 응용 프로그램 프록시를 통해 Azure AD DS 관리 되는 도메인의 내부 응용 프로그램 부분을 안전 하 게 게시 하 여 원격 작업자를 지원할 수 있으므로 인터넷을 통해 액세스할 수 있습니다.

Azure AD 응용 프로그램 프록시를 처음 사용 하 고 자세히 알아보려면 [내부 응용 프로그램에 보안 원격 액세스를 제공 하는 방법](../active-directory/manage-apps/application-proxy.md)을 참조 하세요.

이 문서에서는 관리 되는 도메인의 응용 프로그램에 대 한 보안 액세스를 제공 하기 위해 Azure AD 응용 프로그램 프록시 커넥터를 만들고 구성 하는 방법을 보여 줍니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
    * **Azure AD Premium 라이선스** 는 Azure AD 응용 프로그램 프록시를 사용 하는 데 필요 합니다.
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 [Azure Active Directory Domain Services 관리형 도메인을 만들고 구성합니다][create-azure-ad-ds-instance].

## <a name="create-a-domain-joined-windows-vm"></a>도메인에 가입 된 Windows VM 만들기

환경에서 실행 중인 응용 프로그램으로 트래픽을 라우팅하려면 Azure AD 응용 프로그램 프록시 커넥터 구성 요소를 설치 합니다. 이 Azure AD 응용 프로그램 프록시 커넥터는 관리 되는 도메인에 가입 된 Windows Server VM (가상 머신)에 설치 되어야 합니다. 일부 응용 프로그램의 경우 커넥터가 설치 된 여러 서버를 배포할 수 있습니다. 이 배포 옵션을 선택하면 가용성이 높아지고 더 많은 인증 부하를 처리하는 데 도움이 됩니다.

Azure AD 응용 프로그램 프록시 커넥터를 실행 하는 VM은 관리 되는 도메인과 동일 하거나 피어 링 가상 네트워크에 있어야 합니다. 그런 다음 응용 프로그램 프록시를 사용 하 여 게시 하는 응용 프로그램을 호스트 하는 Vm도 동일한 Azure 가상 네트워크에 배포 해야 합니다.

Azure AD 응용 프로그램 프록시 커넥터용 VM을 만들려면 다음 단계를 완료 합니다.

1. [사용자 지정 OU를 만듭니다](create-ou.md). 이 사용자 지정 OU를 관리할 수 있는 권한을 관리되는 도메인 내의 사용자에게 위임할 수 있습니다. Azure AD 응용 프로그램 프록시 및 응용 프로그램을 실행 하는 Vm은 기본 *AAD DC 컴퓨터* ou가 아닌 사용자 지정 OU의 일부 여야 합니다.
1. Azure AD 응용 프로그램 프록시 커넥터를 실행 하는 가상 컴퓨터와 응용 프로그램을 실행 하는 가상 컴퓨터를 관리 되는 도메인에 [도메인 가입][create-join-windows-vm]합니다. 이전 단계에서 사용자 지정 OU에 이러한 컴퓨터 계정을 만듭니다.

## <a name="download-the-azure-ad-application-proxy-connector"></a>Azure AD 응용 프로그램 프록시 커넥터 다운로드

Azure AD 응용 프로그램 프록시 커넥터를 다운로드 하려면 다음 단계를 수행 합니다. 다운로드 한 설치 파일은 다음 섹션에서 앱 프록시 VM에 복사 됩니다.

1. Azure AD에서 *엔터프라이즈 관리자* 권한이 있는 사용자 계정으로 [Azure Portal](https://portal.azure.com) 에 로그인 합니다.
1. 포털의 맨 위에서 **Azure Active Directory** 를 검색 하 고 선택한 다음 **엔터프라이즈 응용 프로그램**을 선택 합니다.
1. 왼쪽의 메뉴에서 **응용 프로그램 프록시** 를 선택 합니다. 첫 번째 커넥터를 만들고 앱 프록시를 사용 하도록 설정 하려면 커넥터를 **다운로드**하기 위한 링크를 선택 합니다.
1. 다운로드 페이지에서 사용 조건 및 개인 정보 보호 계약에 동의한 다음 사용 **약관 & 다운로드**를 선택 합니다.

    ![Azure AD 앱 프록시 커넥터 다운로드](./media/app-proxy/download-app-proxy-connector.png)

## <a name="install-and-register-the-azure-ad-application-proxy-connector"></a>Azure AD 응용 프로그램 프록시 커넥터 설치 및 등록

Azure AD 응용 프로그램 프록시 커넥터로 VM을 사용할 준비가 되 면 이제 Azure Portal에서 다운로드 한 설치 파일을 복사 하 여 실행 합니다.

1. Azure AD 응용 프로그램 프록시 connector 설치 파일을 VM에 복사 합니다.
1. *AADApplicationProxyConnectorInstaller.exe*와 같은 설치 파일을 실행 합니다. 소프트웨어 사용 조건에 동의합니다.
1. 설치 하는 동안 Azure AD 디렉터리의 응용 프로그램 프록시를 사용 하 여 커넥터를 등록 하 라는 메시지가 표시 됩니다.
   * Azure AD 디렉터리의 전역 관리자에 대 한 자격 증명을 제공 합니다. Azure AD 전역 관리자 자격 증명은 포털의 Azure 자격 증명과 다를 수 있습니다.

        > [!NOTE]
        > 커넥터를 등록 하는 데 사용 되는 전역 관리자 계정은 응용 프로그램 프록시 서비스를 사용 하도록 설정 하는 동일한 디렉터리에 속해야 합니다.
        >
        > 예를 들어 Azure AD 도메인이 *contoso.com*인 경우 전역 관리자는 `admin@contoso.com` 또는 해당 도메인의 다른 유효한 별칭 이어야 합니다.

   * 커넥터를 설치 하는 VM에 대해 Internet Explorer 보안 강화 구성이 설정 된 경우 등록 화면이 차단 될 수 있습니다. 액세스를 허용 하려면 오류 메시지의 지침에 따라 설치 하는 동안 Internet Explorer 보안 강화를 해제 합니다.
   * 커넥터 등록이 실패 하는 경우 [응용 프로그램 프록시 문제 해결](../active-directory/manage-apps/application-proxy-troubleshoot.md)을 참조 하세요.
1. 설정이 끝나면 아웃 바운드 프록시를 사용 하는 환경에 대 한 노트가 표시 됩니다. 아웃 바운드 프록시를 통해 작동 하도록 Azure AD 응용 프로그램 프록시 connector를 구성 하려면 제공 된 스크립트 (예:)를 실행 `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1` 합니다.
1. 다음 예제와 같이 Azure Portal의 응용 프로그램 프록시 페이지에 새 커넥터가 *활성*상태로 나열 됩니다.

    ![Azure Portal에서 활성으로 표시 된 새 Azure AD 응용 프로그램 프록시 커넥터](./media/app-proxy/connected-app-proxy.png)

> [!NOTE]
> Azure AD 응용 프로그램 프록시를 통해 인증 하는 응용 프로그램에 고가용성을 제공 하기 위해 여러 Vm에 커넥터를 설치할 수 있습니다. 이전 섹션에 나열 된 것과 동일한 단계를 반복 하 여 관리 되는 도메인에 가입 된 다른 서버에 커넥터를 설치 합니다.

## <a name="enable-resource-based-kerberos-constrained-delegation"></a>리소스 기반 Kerberos 제한 위임 사용

IWA (Windows 통합 인증)를 사용 하 여 응용 프로그램에 Single Sign-On를 사용 하려는 경우 Azure AD 응용 프로그램 프록시 커넥터에 사용자를 가장할 수 있는 권한을 부여 하 고 토큰을 대신 보내고 받을 수 있습니다. 이러한 권한을 부여 하려면 커넥터에 대 한 KCD (Kerberos 제한 위임)를 구성 하 여 관리 되는 도메인의 리소스에 액세스 합니다. 관리 되는 도메인에서 도메인 관리자 권한이 없기 때문에 관리 되는 도메인에서는 기존 계정 수준 KCD를 구성할 수 없습니다. 대신 리소스 기반 KCD를 사용 합니다.

자세한 내용은 [Azure Active Directory Domain Services에서 Kerberos 제한 위임 (KCD) 구성](deploy-kcd.md)을 참조 하세요.

> [!NOTE]
> 다음 PowerShell cmdlet을 실행 하려면 Azure AD 테 넌 트에서 *AZURE AD DC administrators* 그룹의 구성원 인 사용자 계정에 로그인 해야 합니다.
>
> 앱 프록시 커넥터 VM 및 응용 프로그램 Vm의 컴퓨터 계정은 리소스 기반 KCD를 구성할 수 있는 권한이 있는 사용자 지정 OU에 있어야 합니다. 기본 제공 *AAD DC 컴퓨터* 컨테이너의 컴퓨터 계정에 대해서는 리소스 기반 kcd를 구성할 수 없습니다.

[Get ADComputer][Get-ADComputer] 를 사용 하 여 Azure AD 응용 프로그램 프록시 커넥터가 설치 된 컴퓨터에 대 한 설정을 검색 합니다. 도메인에 가입 된 관리 VM에서 *AZURE AD DC administrators* 그룹의 구성원 인 사용자 계정으로 로그인 한 후 다음 cmdlet을 실행 합니다.

다음 예에서는 *appproxy.aaddscontoso.com*라는 컴퓨터 계정에 대 한 정보를 가져옵니다. 이전 단계에서 구성 된 Azure AD 응용 프로그램 프록시 VM에 대 한 고유한 컴퓨터 이름을 제공 합니다.

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity appproxy.aaddscontoso.com
```

Azure AD 응용 프로그램 프록시 앱을 실행 하는 각 응용 프로그램 서버에 대해 [집합-ADComputer][Set-ADComputer] PowerShell cmdlet을 사용 하 여 리소스 기반 kcd를 구성 합니다. 다음 예제에서는 Azure AD 응용 프로그램 프록시 커넥터에 *appserver.aaddscontoso.com* 컴퓨터를 사용할 수 있는 권한이 부여 됩니다.

```powershell
Set-ADComputer appserver.aaddscontoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

여러 Azure AD 응용 프로그램 프록시 커넥터를 배포 하는 경우 각 커넥터 인스턴스에 대해 리소스 기반 KCD를 구성 해야 합니다.

## <a name="next-steps"></a>다음 단계

Azure AD DS와 통합 된 Azure AD 응용 프로그램 프록시를 사용 하 여 사용자가 액세스할 수 있도록 응용 프로그램을 게시 합니다. 자세한 내용은 [Azure AD 응용 프로그램 프록시를 사용 하 여 응용 프로그램 게시](../active-directory/manage-apps/application-proxy-add-on-premises-application.md)를 참조 하세요.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[Get-ADComputer]: /powershell/module/addsadministration/get-adcomputer
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer