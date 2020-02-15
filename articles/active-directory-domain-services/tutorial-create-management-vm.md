---
title: 자습서 - Azure Active Directory Domain Services에 대한 관리 VM 만들기 | Microsoft Docs
description: 이 자습서에서는 Azure Active Directory Domain Services 인스턴스를 관리하는 데 사용하는 Windows 가상 머신을 만들고 구성하는 방법을 알아봅니다.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: iainfou
ms.openlocfilehash: 73402420bdfee7fecbd7901deefe7f4314a76d51
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76931594"
---
# <a name="tutorial-create-a-management-vm-to-configure-and-administer-an-azure-active-directory-domain-services-managed-domain"></a>자습서: 관리 VM을 만들어 Azure Active Directory Domain Services 관리되는 도메인 구성 및 관리

Azure AD DS(Active Directory Domain Services)는 Windows Server Active Directory와 완전히 호환되는 도메인 조인, 그룹 정책, LDAP 및 Kerberos/NTLM 인증과 같은 관리되는 도메인 서비스를 제공합니다. 이 관리되는 도메인은 온-프레미스 Active Directory Domain Services 도메인과 동일한 RSAT(원격 서버 관리 도구)를 사용하여 관리됩니다. Azure AD DS는 관리형 서비스이므로 RDP(원격 데스크톱 프로토콜)를 사용하여 도메인 컨트롤러에 연결하는 것과 같이 수행할 수 없는 몇 가지 관리 작업이 있습니다.

이 자습서에서는 Azure에서 Windows Server VM을 만들고, Azure AD DS 관리되는 도메인을 관리하는 데 필요한 도구를 설치하는 방법을 보여 줍니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure AD DS 관리되는 도메인에서 사용 가능한 관리 작업 이해
> * Windows Server VM에 Active Directory 관리 도구 설치
> * Active Directory 관리 센터를 사용하여 일반 작업 수행

Azure 구독이 없는 경우 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 첫 번째 자습서를 참조하여 [Azure Active Directory Domain Services 인스턴스를 만들고 구성합니다][create-azure-ad-ds-instance].
* Azure AD DS 관리되는 도메인에 조인된 Windows Server VM
    * 필요한 경우 이전 자습서를 참조하여 [Windows Server VM을 만들어 관리되는 도메인에 조인시킵니다][create-join-windows-vm].
* Azure AD 테넌트의 *Azure AD DC Administrators* 그룹에 속한 멤버인 사용자 계정

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

이 자습서에서는 Azure Portal을 사용하여 관리 VM을 만들고 구성합니다. 시작하려면 먼저 [Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="available-administrative-tasks-in-azure-ad-ds"></a>Azure AD DS에서 사용 가능한 관리 작업

Azure AD DS는 사용자, 애플리케이션 및 서비스에서 사용할 관리되는 도메인을 제공합니다. 이 방법에서는 사용 가능한 관리 작업 중 일부와 관리되는 도메인 내에서 보유한 권한을 변경합니다. 이러한 작업과 권한은 일반 온-프레미스 Active Directory Domain Services 환경에서 경험하는 것과 다를 수 있습니다. 또한 원격 데스크톱을 사용하여 Azure AD DS 관리되는 도메인의 도메인 컨트롤러에 연결할 수도 없습니다.

### <a name="administrative-tasks-you-can-perform-on-an-azure-ad-ds-managed-domain"></a>Azure AD DS 관리되는 도메인에서 수행할 수 있는 관리 작업

*AAD DC Administrators* 그룹의 멤버에게는 다음 작업을 수행할 수 있는 Azure AD DS 관리되는 도메인에 대한 권한이 부여됩니다.

* 컴퓨터를 관리되는 도메인에 조인합니다.
* 관리되는 도메인의 *AADDC Computers* 및 *AADDC Users* 컨테이너에 대한 기본 제공 GPO(그룹 정책 개체)를 구성합니다.
* 관리되는 도메인에서 DNS 관리
* 관리되는 도메인에서 사용자 지정 OU(조직 구성 단위)를 만들고 관리합니다.
* 관리되는 도메인에 가입된 컴퓨터에 대한 관리 액세스 권한을 얻습니다.

### <a name="administrative-privileges-you-dont-have-on-an-azure-ad-ds-managed-domain"></a>Azure AD DS 관리되는 도메인에 없는 관리자 권한

Azure AD DS 관리되는 도메인이 잠겨 있으므로 도메인에서 특정 관리 작업을 수행할 수 있는 권한이 없습니다. 다음 예 중 일부는 수행할 수 없는 작업입니다.

* 관리되는 도메인의 스키마를 확장합니다.
* 원격 데스크톱을 사용하여 관리되는 도메인의 도메인 컨트롤러에 연결합니다.
* 도메인 컨트롤러를 관리되는 도메인에 추가합니다.
* 관리되는 도메인에 대한 *도메인 관리자* 또는 *엔터프라이즈 관리자* 권한이 없습니다.

## <a name="sign-in-to-the-windows-server-vm"></a>Windows Server VM에 로그인

이전 자습서에서는 Windows Server VM을 만들어 Azure AD DS 관리되는 도메인에 조인했습니다. 이 VM을 사용하여 관리 도구를 설치해 보겠습니다. 필요한 경우 [자습서의 단계에 따라 Windows Server VM을 만들고 관리되는 도메인에 조인합니다][create-join-windows-vm].

> [!NOTE]
> 이 자습서에서는 Azure AD DS 관리되는 도메인에 조인된 Windows Server VM을 사용합니다. 또한 관리되는 도메인에 조인된 Windows 10과 같은 Windows 클라이언트를 사용할 수도 있습니다.
>
> 관리 도구를 Windows 클라이언트에 설치하는 방법에 대한 자세한 내용은 [RSAT(원격 서버 관리 도구) 설치](https://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx)를 참조하세요.

시작하려면 다음과 같이 Windows Server VM에 연결합니다.

1. Azure Portal의 왼쪽에서 **리소스 그룹**을 선택합니다. VM을 만든 리소스 그룹(예: *myResourceGroup*)을 선택한 다음, VM(예: *myVM*)을 선택합니다.
1. VM의 **개요** 창에서 **연결**을 선택합니다.

    ![Azure Portal에서 Windows 가상 머신에 연결](./media/tutorial-create-management-vm/connect-vm.png)

    또한 Azure Portal에서 SSL을 통해서만 액세스할 수 있도록 [Azure Bastion 호스트(현재 미리 보기)를 만들어 사용][azure-bastion]할 수도 있습니다.

1. *RDP 파일 다운로드* 옵션을 선택합니다. 이 RDP 파일을 웹 브라우저에 저장합니다.
1. VM에 연결하려면 다운로드한 RDP 파일을 엽니다. 메시지가 표시되면 **연결**을 선택합니다.
1. *Azure AD DC Administrators* 그룹에 속한 사용자의 자격 증명(예:  *contoso\dee*)을 입력합니다.
1. 로그인 프로세스 중에 인증서 경고가 표시되면 **예** 또는 **계속**을 선택하여 연결합니다.

## <a name="install-active-directory-administrative-tools"></a>Active Directory 관리 도구 설치

Azure AD DS 관리되는 도메인은 온-프레미스 AD DS 환경과 동일한 관리 도구(예: ADAC(Active Directory 관리 센터) 또는 AD PowerShell)를 사용하여 관리됩니다. 이러한 도구는 RSAT(원격 서버 관리 도구) 기능의 일부로 Windows Server 및 클라이언트 컴퓨터에 설치할 수 있습니다. 그러면 *AAD DC Administrators* 그룹의 멤버가 관리되는 도메인에 조인된 컴퓨터에서 이러한 AD 관리 도구를 사용하여 Azure AD DS 관리되는 도메인을 원격으로 관리할 수 있습니다.

Active Directory 관리 도구를 도메인 조인 VM에 설치하려면 다음 단계를 수행합니다.

1. VM에 로그인할 때 **서버 관리자**가 기본적으로 열리지 않는 경우 **시작** 메뉴를 선택한 다음, **서버 관리자**를 선택합니다.
1. **서버 관리자** 창의 *대시보드* 창에서 **역할 및 기능 추가**를 선택합니다.
1. *역할 및 기능 추가 마법사*의 **시작하기 전에** 페이지에서 **다음**을 선택합니다.
1. *설치 유형*에서 **역할 기반 또는 기능 기반 설치** 옵션을 선택한 상태로 두고, **다음**을 선택합니다.
1. **서버 선택** 페이지의 서버 풀에서 현재 VM(예: *myvm.aadds.contoso.com*), **다음**을 차례로 선택합니다.
1. **서버 역할** 페이지에서 **다음**을 클릭합니다.
1. **기능** 페이지에서 **원격 서버 관리 도구** 노드, **역할 관리 도구** 노드를 차례로 펼칩니다.

    역할 관리 도구 목록에서 **AD DS 및 AD LDS 도구** 기능, **다음**을 차례로 선택합니다.

    ![기능 페이지에서 'AD DS 및 AD LDS 도구' 설치](./media/tutorial-create-management-vm/install-features.png)

1. **확인** 페이지에서 **설치**를 선택합니다. 관리 도구를 설치하는 데 1-2분 정도 걸릴 수 있습니다.
1. 기능 설치가 완료되면 **닫기**를 선택하여 **역할 및 기능 추가** 마법사를 종료합니다.

## <a name="use-active-directory-administrative-tools"></a>Active Directory 관리 도구 사용

관리 도구가 설치되었으면 이 도구를 사용하여 Azure AD DS 관리되는 도메인을 관리하는 방법을 알아보겠습니다. *AAD DC Administrators* 그룹의 멤버인 사용자 계정을 사용하여 VM에 로그인했는지 확인합니다.

1. **시작** 메뉴에서 **Windows 관리 도구**를 선택합니다. 이전 단계에서 설치한 AD 관리 도구가 나열됩니다.

    ![서버에 설치된 관리 도구 목록](./media/tutorial-create-management-vm/list-admin-tools.png)

1. **Active Directory 관리 센터**를 선택합니다.
1. Azure AD DS 관리형 도메인을 검색하려면 왼쪽 창에서 도메인 이름(예: *aadds.contoso.com*)을 선택합니다. *AADDC Computers* 및 *AADDC Users*라는 두 개의 컨테이너가 목록 위쪽에 있습니다.

    ![Azure AD DS 관리되는 도메인의 사용 가능한 컨테이너 부분 나열](./media/tutorial-create-management-vm/active-directory-administrative-center.png)

1. Azure AD DS 관리되는 도메인에 속한 사용자와 그룹을 확인하려면 **AADDC Users** 컨테이너를 선택합니다. Azure AD 테넌트의 사용자 계정과 그룹이 이 컨테이너에 나열됩니다.

    다음 출력 예에는 이름이 *Contoso Admin*인 사용자 계정과 *AAD DC Administrators*에 대한 그룹이 이 컨테이너에 표시됩니다.

    ![Active Directory 관리 센터에서 Azure AD DS 도메인 사용자 목록 보기](./media/tutorial-create-management-vm/list-azure-ad-users.png)

1. Azure AD DS 관리되는 도메인에 조인된 컴퓨터를 확인하려면 **AADDC Computers** 컨테이너를 선택합니다. 현재 가상 머신에 대한 항목(예: *myVM*)이 나열됩니다. Azure AD DS 관리되는 도메인에 조인된 모든 컴퓨터의 컴퓨터 계정은 이 *AADDC Computers* 컨테이너에 저장됩니다.

사용자 계정 암호 재설정 또는 그룹 멤버 자격 관리와 같은 일반적인 Active Directory 관리 센터 작업을 사용할 수 있습니다. 이러한 작업은 Azure AD DS 관리형 도메인에서 직접 만든 사용자 및 그룹에 대해서만 작동합니다. ID 정보는 Azure AD*에서* Azure AD DS로만 동기화됩니다. Azure AD DS에서 Azure AD로 다시 쓸 수는 없습니다. Azure AD에서 동기화된 사용자의 암호 또는 관리 그룹 멤버 자격을 변경하고 해당 변경 내용을 다시 동기화할 수 없습니다.

또한 관리 도구의 일부로 설치된 *Windows PowerShell용 Active Directory 모듈*을 사용하여 Azure AD DS 관리되는 도메인에서 일반적인 작업을 관리할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * Azure AD DS 관리되는 도메인에서 사용 가능한 관리 작업 이해
> * Windows Server VM에 Active Directory 관리 도구 설치
> * Active Directory 관리 센터를 사용하여 일반 작업 수행

Azure AD DS 관리되는 도메인과 안전하게 상호 작용하려면 LDAPS(보안 Lightweight Directory Access Protocol)를 사용하도록 설정합니다.

> [!div class="nextstepaction"]
> [관리되는 도메인에 대한 보안 LDAP 구성](tutorial-configure-ldaps.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
