---
title: 자습서 - Azure AD Domain Services에서 포리스트 트러스트 만들기 | Microsoft Docs
description: Azure AD Domain Services를 위해 Azure Portal에서 온-프레미스 AD DS 도메인에 대한 단방향 아웃바운드 포리스트를 만드는 방법을 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 24928ec4117b321cfec7177fdad40f2a3ab7a1f4
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88722724"
---
# <a name="tutorial-create-an-outbound-forest-trust-to-an-on-premises-domain-in-azure-active-directory-domain-services"></a>자습서: Azure Active Directory Domain Services에서 온-프레미스 도메인에 대한 아웃바운드 포리스트 트러스트 만들기

암호 해시를 동기화할 수 없거나 스마트 카드를 사용하여 독점적으로 로그인하는 사용자가 자신의 암호를 모르는 환경에서는 Azure AD DS(Active Directory Domain Services)에서 리소스 포리스트를 사용할 수 있습니다. 리소스 포리스트는 Azure AD DS에서 하나 이상의 온-프레미스 AD DS 환경으로의 단방향 아웃바운드 트러스트를 사용합니다. 이 트러스트 관계를 통해 사용자, 애플리케이션 및 컴퓨터에서 Azure AD DS 관리형 도메인의 온-프레미스 도메인에 대해 인증할 수 있습니다. 리소스 포리스트에서 온-프레미스 암호 해시는 절대 동기화되지 않습니다.

![Azure AD DS에서 온-프레미스 AD DS로의 포리스트 트러스트에 대한 다이어그램](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure AD DS 연결을 지원하도록 온-프레미스 AD DS 환경에서 DNS 구성
> * 온-프레미스 AD DS 환경에서 단방향 인바운드 포리스트 트러스트 만들기
> * Azure AD DS에서 단방향 아웃바운드 포리스트 트러스트 만들기
> * 인증 및 리소스 액세스에 대한 트러스트 관계 테스트 및 유효성 검사

Azure 구독이 없는 경우 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* 리소스 포리스트를 사용하여 만들고 Azure AD 테넌트에 구성한 Azure Active Directory Domain Services 관리형 도메인
    * 필요한 경우 [Azure Active Directory Domain Services 관리형 도메인을 만들고 구성][create-azure-ad-ds-instance-advanced]합니다.
    
    > [!IMPORTANT]
    > *리소스* 포리스트를 사용하여 관리되는 도메인을 만들어야 합니다. 기본 옵션은 *사용자* 포리스트를 만듭니다. 리소스 포리스트만 온-프레미스 AD DS 환경에 대한 트러스트를 만들 수 있습니다.
    >
    > 또한 관리되는 도메인에 대해 적어도 *Enterprise* SKU를 사용해야 합니다. 필요한 경우 [관리되는 도메인의 SKU를 변경][howto-change-sku]하세요.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

이 자습서에서는 Azure Portal을 사용하여 Azure AD DS에서 아웃바운드 포리스트 트러스트를 만들고 구성합니다. 시작하려면 먼저 [Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="networking-considerations"></a>네트워킹 고려 사항

Azure AD DS 리소스 포리스트를 호스팅하는 가상 네트워크에는 온-프레미스 Active Directory에 대한 네트워크 연결이 필요합니다. 또한 애플리케이션 및 서비스에는 Azure AD DS 리소스 포리스트를 호스팅하는 가상 네트워크에 대한 네트워크 연결이 필요합니다. Azure AD DS 리소스 포리스트에 대한 네트워크 연결은 항상 켜져 있고 안정적이어야 합니다. 그렇지 않으면 사용자가 리소스를 인증하거나 액세스하지 못할 수 있습니다.

Azure AD DS에서 포리스트 트러스트를 구성하려면 먼저 Azure와 온-프레미스 환경 간의 네트워킹에서 다음 요구 사항을 충족하는지 확인해야 합니다.

* 개인 IP 주소를 사용합니다. 동적 IP 주소가 할당된 DHCP는 사용하지 마세요.
* 가상 네트워크 피어링 및 라우팅에서 Azure와 온-프레미스 간에 성공적으로 통신할 수 있도록 IP 주소 공간이 겹치지 않도록 합니다.
* Azure 가상 네트워크에는 [Azure S2S(사이트 간) VPN][vpn-gateway] 또는 [ExpressRoute][expressroute] 연결을 구성하기 위해 게이트웨이 서브넷이 필요합니다.
* 시나리오를 지원하는 데 충분한 IP 주소를 사용하는 서브넷을 만듭니다.
* 자체 서브넷이 Azure AD DS에 있어야 합니다. 이 가상 네트워크 서브넷을 애플리케이션 VM 및 서비스와 공유하지 마세요.
* 피어링된 가상 네트워크는 전이적이지 않습니다.
    * 온-프레미스 AD DS 환경에 대한 Azure AD DS 리소스 포리스트 트러스트를 사용하려는 모든 가상 네트워크 간에 Azure 가상 네트워크 피어링을 만들어야 합니다.
* 온-프레미스 Active Directory 포리스트에 대한 지속적인 네트워크 연결을 제공합니다. 주문형 연결은 사용하지 마세요.
* Azure AD DS 리소스 포리스트 이름과 온-프레미스 Active Directory 포리스트 이름 간에 지속적인 이름 확인(DNS)이 있어야 합니다.

## <a name="configure-dns-in-the-on-premises-domain"></a>온-프레미스 도메인에서 DNS 구성

온-프레미스 환경에서 관리되는 도메인을 올바르게 확인하려면 전달자를 기존 DNS 서버에 추가해야 할 수 있습니다. 관리되는 도메인과 통신하도록 온-프레미스 환경을 구성하지 않은 경우 온-프레미스 AD DS 도메인의 관리 워크스테이션에서 다음 단계를 완료합니다.

1. **시작 | 관리 도구 | DNS**를 차례로 선택합니다.
1. 마우스 오른쪽 단추로 DNS 서버(예: *myAD01*)를 선택한 다음, **속성**을 선택합니다.
1. **전달자**를 선택한 다음, **편집**을 선택하여 추가 전달자를 추가합니다.
1. 관리되는 도메인의 IP 주소(예: *10.0.2.4* 및 *10.0.2.5*)를 추가합니다.

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>온-프레미스 도메인에서 인바운드 포리스트 트러스트 만들기

온-프레미스 AD DS 도메인에는 관리되는 도메인에 대해 들어오는 포리스트 트러스트가 필요합니다. 이 트러스트는 온-프레미스 AD DS 도메인에서 수동으로 만들어야 하며, Azure Portal에서는 만들 수 없습니다.

온-프레미스 AD DS 도메인에서 인바운드 트러스트를 구성하려면 온-프레미스 AD DS 도메인의 관리 워크스테이션에서 다음 단계를 완료합니다.

1. **시작 | 관리 도구 | Active Directory 도메인 및 트러스트**를 차례로 선택합니다.
1. 마우스 오른쪽 단추로 도메인(예: *onprem.contoso.com*)을 선택한 다음, **속성**을 선택합니다.
1. **트러스트** 탭, **새 트러스트**를 차례로 선택합니다.
1. Azure AD DS 도메인 이름에 이름(예: *aaddscontoso.com*)을 입력하고, **다음**을 선택합니다.
1. **포리스트 트러스트**를 만드는 옵션, **단방향: 들어오는 트러스트**를 만드는 옵션을 차례로 선택합니다.
1. **이 도메인만**에 대한 트러스트를 만들도록 선택합니다. 다음 단계에서는 Azure Portal에서 관리되는 도메인에 대한 트러스트를 만듭니다.
1. **전체 포리스트 인증**을 사용하도록 선택한 다음, 트러스트 암호를 입력하고 확인합니다. 다음 섹션의 Azure Portal에서도 동일한 암호가 입력됩니다.
1. 기본 옵션을 사용하여 다음 몇 개의 창을 단계별로 실행한 다음, **아니요, 보내는 트러스트를 확인하지 않습니다.** 옵션을 선택합니다.
1. **마침**을 선택합니다.

## <a name="create-outbound-forest-trust-in-azure-ad-ds"></a>Azure AD DS에서 아웃바운드 포리스트 트러스트 만들기

관리되는 도메인을 확인하도록 온-프레미스 AD DS 도메인이 구성되고 인바운드 포리스트 트러스트가 만들어지면 이제 아웃바운드 포리스트 트러스트를 만듭니다. 이 아웃바운드 포리스트 트러스트는 온-프레미스 AD DS 도메인과 관리되는 도메인 간의 트러스트 관계를 완료합니다.

Azure Portal에서 관리되는 도메인에 대한 아웃바운드 트러스트를 만들려면 다음 단계를 완료합니다.

1. Azure Portal에서 **Azure AD Domain Services**를 검색하여 선택한 다음, 관리되는 도메인(예: *aaddscontoso.com*)을 선택합니다.
1. 관리되는 도메인의 왼쪽 메뉴에서 **트러스트**를 선택한 다음, 트러스트 **+ 추가**를 선택합니다.

   > [!NOTE]
   > **트러스트** 메뉴 옵션이 표시되지 않으면 **속성** 아래에서 *포리스트 유형*을 확인합니다. *리소스* 포리스트만 트러스트를 만들 수 있습니다. 포리스트 유형이 *사용자*인 경우 트러스트를 만들 수 없습니다. 현재 관리되는 도메인의 포리스트 유형을 변경할 수 있는 방법이 없습니다. 관리되는 도메인을 삭제하고 리소스 포리스트로 다시 만들어야 합니다.

1. 트러스트를 식별할 수 있는 표시 이름을 입력한 다음, 트러스트된 온-프레미스 포리스트 DNS 이름(예: *onprem.contoso.com*)을 입력합니다.
1. 이전 섹션에서 온-프레미스 AD DS 도메인에 대한 인바운드 포리스트 트러스트를 구성할 때 사용한 것과 동일한 트러스트 암호를 제공합니다.
1. 온-프레미스 AD DS 도메인에 대해 둘 이상의 DNS 서버(예: *10.1.1.4* 및 *10.1.1.5*)를 제공합니다.
1. 준비가 되면 아웃바운드 포리스트 트러스트를 **저장**합니다.

    ![Azure Portal에서 아웃바운드 포리스트 트러스트 만들기](./media/tutorial-create-forest-trust/portal-create-outbound-trust.png)

## <a name="validate-resource-authentication"></a>리소스 인증 유효성 검사

다음과 같은 일반적인 시나리오를 통해 포리스트 트러스트에서 사용자 및 리소스에 대한 액세스를 올바르게 인증하는지 확인할 수 있습니다.

* [Azure AD DS 리소스 포리스트의 온-프레미스 사용자 인증](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [온-프레미스 사용자를 사용하여 Azure AD DS 리소스 포리스트의 리소스에 액세스](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [파일 및 프린터 공유 사용](#enable-file-and-printer-sharing)
    * [보안 그룹 만들기 및 멤버 추가](#create-a-security-group-and-add-members)
    * [포리스트 간 액세스를 위한 파일 공유 만들기](#create-a-file-share-for-cross-forest-access)
    * [리소스에 대한 포리스트 간 인증 유효성 검사](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>Azure AD DS 리소스 포리스트의 온-프레미스 사용자 인증

Windows Server 가상 머신이 관리되는 도메인에 가입되어 있어야 합니다. 이 가상 머신을 사용하여 온-프레미스 사용자가 가상 머신에서 인증할 수 있는지 테스트합니다. 필요한 경우 [Windows VM을 만들어서 관리되는 도메인에 가입][join-windows-vm]합니다.

1. [Azure Bastion](../bastion/bastion-overview.md) 및 Azure AD DS 관리자 자격 증명을 사용하여 Azure AD DS 리소스 포리스트에 조인된 Windows Server VM에 연결합니다.
1. 명령 프롬프트를 열고, `whoami` 명령을 사용하여 현재 인증된 사용자의 고유 이름을 표시합니다.

    ```console
    whoami /fqdn
    ```

1. `runas` 명령을 사용하여 온-프레미스 도메인에서 사용자로 인증합니다. 다음 명령에서 `userUpn@trusteddomain.com`을 트러스트된 온-프레미스 도메인의 사용자 UPN으로 바꿉니다. 이 명령은 사용자의 암호를 묻는 메시지를 표시합니다.

    ```console
    Runas /u:userUpn@trusteddomain.com cmd.exe
    ```

1. 인증에 성공하면 새 명령 프롬프트가 열립니다. 새 명령 프롬프트의 제목에 `running as userUpn@trusteddomain.com`이 포함되어 있습니다.
1. 새 명령 프롬프트에서 `whoami /fqdn`을 사용하여 온-프레미스 Active Directory에서 인증된 사용자의 고유 이름을 확인합니다.

### <a name="access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user"></a>온-프레미스 사용자를 사용하여 Azure AD DS 리소스 포리스트의 리소스에 액세스

Azure AD DS 리소스 포리스트에 조인된 Windows Server VM을 사용하면, 온-프레미스 도메인의 컴퓨터에서 온-프레미스 도메인의 사용자를 사용하여 인증할 때 해당 사용자가 리소스 포리스트에 호스팅되는 리소스에 액세스할 수 있는 시나리오를 테스트할 수 있습니다. 다음 예에서는 다양한 일반적인 시나리오를 만들고 테스트하는 방법을 보여 줍니다.

#### <a name="enable-file-and-printer-sharing"></a>파일 및 프린터 공유 사용

1. [Azure Bastion](../bastion/bastion-overview.md) 및 Azure AD DS 관리자 자격 증명을 사용하여 Azure AD DS 리소스 포리스트에 조인된 Windows Server VM에 연결합니다.

1. **Windows 설정**을 연 다음, **네트워크 및 공유 센터**를 검색하여 선택합니다.
1. **고급 공유 설정 변경** 옵션을 선택합니다.
1. **도메인 프로필** 아래에서 **파일 및 프린터 공유 켜기**, **변경 내용 저장**을 차례로 선택합니다.
1. **네트워크 및 공유 센터**를 닫습니다.

#### <a name="create-a-security-group-and-add-members"></a>보안 그룹 만들기 및 멤버 추가

1. **Active Directory 사용자 및 컴퓨터**를 엽니다.
1. 마우스 오른쪽 단추로 도메인 이름을 선택하고, **새로 만들기**를 선택한 다음, **조직 구성 단위**를 선택합니다.
1. 이름 상자에서 *LocalObjects*를 입력한 다음, **확인**을 선택합니다.
1. 탐색 창에서 **LocalObjects**를 선택하고 마우스 오른쪽 단추로 클릭합니다. **새로 만들기**, **그룹**을 차례로 선택합니다.
1. **그룹 이름** 상자에서 *FileServerAccess*를 입력합니다. **그룹 범위**에 대해 **도메인 로컬**, **확인**을 차례로 선택합니다.
1. 내용 창에서 **FileServerAccess**를 두 번 클릭합니다. **멤버**, **추가**, **위치**를 차례로 선택합니다.
1. **위치** 보기에서 온-프레미스 Active Directory를 선택한 다음, **확인**을 선택합니다.
1. **선택할 개체 이름 입력** 상자에서 *도메인 사용자*를 입력합니다. **이름 확인**을 선택하고, 온-프레미스 Active Directory에 대한 자격 증명을 제공한 다음, **확인**을 선택합니다.

    > [!NOTE]
    > 단방향 트러스트 관계이므로 자격 증명을 제공해야 합니다. 즉, Azure AD DS 관리형 도메인의 사용자가 신뢰할 수 있는(온-프레미스) 도메인에서 리소스에 액세스하거나 사용자 또는 그룹을 검색할 수 없습니다.

1. 온-프레미스 Active Directory의 **도메인 사용자** 그룹은 **FileServerAccess** 그룹의 멤버여야 합니다. **확인**을 선택하여 해당 그룹을 저장하고 창을 닫습니다.

#### <a name="create-a-file-share-for-cross-forest-access"></a>포리스트 간 액세스를 위한 파일 공유 만들기

1. Azure AD DS 리소스 포리스트에 조인된 Windows Server VM에서 폴더를 만들고, 이름(예: *CrossForestShare*)을 제공합니다.
1. 마우스 오른쪽 단추로 폴더를 선택하고, **속성**을 선택합니다.
1. **보안** 탭, **편집**을 차례로 선택합니다.
1. *CrossForestShare 권한* 대화 상자에서 **추가**를 선택합니다.
1. **선택할 개체 이름 입력**에서 *FileServerAccess*를 입력한 다음, **확인**을 선택합니다.
1. **그룹 또는 사용자 이름** 목록에서 *FileServerAccess*를 선택합니다. **FileServerAccess 권한** 목록에서 **수정** 및 **쓰기** 권한에 대해 *허용*을 선택한 다음, **확인**을 선택합니다.
1. **공유** 탭, **고급 공유...** 를 차례로 선택합니다.
1. **이 폴더를 공유함**을 선택한 다음, **공유 이름**에서 기억하기 쉬운 파일 공유 이름(예: *CrossForestShare*)을 입력합니다.
1. **권한**을 선택합니다. **모든 사용자 권한** 목록에서 **변경** 권한에 대해 **허용**을 선택합니다.
1. **확인**을 두 번 선택한 다음, **닫기**를 선택합니다.

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>리소스에 대한 포리스트 간 인증 유효성 검사

1. 온-프레미스 Active Directory의 사용자 계정을 사용하여 온-프레미스 Active Directory에 조인된 Windows 컴퓨터에 로그인합니다.
1. **Windows 탐색기**에서 정규화된 호스트 이름과 공유를 사용하여 만든 공유(예: `\\fs1.aaddscontoso.com\CrossforestShare`)에 연결합니다.
1. 쓰기 권한의 유효성을 검사하려면 마우스 오른쪽 단추로 폴더를 선택하고, **새로 만들기**를 선택한 다음, **텍스트 문서**를 선택합니다. **새 텍스트 문서**라는 기본 이름을 사용합니다.

    쓰기 권한이 올바르게 설정되었으면 새 텍스트 문서가 만들어집니다. 그러면 다음 단계에서 해당 파일을 열고, 편집하고, 삭제합니다.
1. 읽기 권한의 유효성을 검사하려면 **새 텍스트 문서**를 엽니다.
1. 수정 권한의 유효성을 검사하려면 텍스트를 파일에 추가하고 **메모장**을 닫습니다. 변경 내용을 저장하라는 메시지가 표시되면 **저장**을 선택합니다.
1. 삭제 권한의 유효성을 검사하려면 마우스 오른쪽 단추로 **새 텍스트 문서**를 선택하고 **삭제**를 선택합니다. **예**를 선택하여 파일 삭제를 확인합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * Azure AD DS 연결을 지원하도록 온-프레미스 AD DS 환경에서 DNS 구성
> * 온-프레미스 AD DS 환경에서 단방향 인바운드 포리스트 트러스트 만들기
> * Azure AD DS에서 단방향 아웃바운드 포리스트 트러스트 만들기
> * 인증 및 리소스 액세스에 대한 트러스트 관계 테스트 및 유효성 검사

Azure AD DS의 포리스트 유형에 대한 자세한 개념 정보는 [리소스 포리스트란?][concepts-forest] 및 [Azure AD DS에서 포리스트 트러스트가 작동하는 방법][concepts-trust]을 참조하세요.

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
[howto-change-sku]: change-sku.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[expressroute]: ../expressroute/expressroute-introduction.md
[join-windows-vm]: join-windows-vm.md