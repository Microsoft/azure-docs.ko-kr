---
title: 자습서-Azure AD Domain Services |에서 포리스트 트러스트 만들기 Microsoft Docs
description: Azure Portal에서 온-프레미스 AD DS 도메인에 단방향 아웃 바운드 포리스트를 만드는 방법에 대해 알아봅니다 Azure AD Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: iainfou
ms.openlocfilehash: 3637a11724c1f0bab049077c5abbd817e168bd44
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76931226"
---
# <a name="tutorial-create-an-outbound-forest-trust-to-an-on-premises-domain-in-azure-active-directory-domain-services-preview"></a>자습서: Azure Active Directory Domain Services (미리 보기)에서 온-프레미스 도메인에 아웃 바운드 포리스트 트러스트 만들기

암호 해시를 동기화 할 수 없는 환경 또는 스마트 카드를 사용 하 여 독점적으로 로그인 하는 사용자가 자신의 암호를 알지 못하는 환경에서는 Azure Active Directory Domain Services (AD DS)에서 리소스 포리스트를 사용할 수 있습니다. 리소스 포리스트는 Azure AD DS에서 하나 이상의 온-프레미스 AD DS 환경으로의 단방향 아웃 바운드 트러스트를 사용 합니다. 이 트러스트 관계를 통해 사용자, 응용 프로그램 및 컴퓨터는 Azure AD DS 관리 되는 도메인의 온-프레미스 도메인에 대해 인증할 수 있습니다. Azure AD DS 리소스 포리스트는 현재 미리 보기로 제공 됩니다.

![Azure AD DS에서 온-프레미스 AD DS로의 포리스트 트러스트 다이어그램](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure AD DS 연결을 지원 하도록 온-프레미스 AD DS 환경에서 DNS 구성
> * 온-프레미스 AD DS 환경에서 단방향 인바운드 포리스트 트러스트 만들기
> * Azure AD DS에서 단방향 아웃 바운드 포리스트 트러스트 만들기
> * 인증 및 리소스 액세스에 대한 트러스트 관계 테스트 및 유효성 검사

Azure 구독이 없는 경우 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* 리소스 포리스트를 사용 하 여 만들고 Azure AD 테넌트에서 구성 된 Azure Active Directory Domain Services 관리 되는 도메인입니다.
    * 필요한 경우 [Azure Active Directory Domain Services 인스턴스를 만들고 구성합니다][create-azure-ad-ds-instance-advanced].
    
    > [!IMPORTANT]
    > *리소스* 포리스트를 사용 하 여 Azure AD DS 관리 되는 도메인을 만들어야 합니다. 기본 옵션은 *사용자* 포리스트를 만듭니다. 리소스 포리스트만 온-프레미스 AD DS 환경에 대한 트러스트를 만들 수 있습니다. 또한 관리 되는 도메인에 *Enterprise* SKU를 최소한으로 사용 해야 합니다. 필요한 경우 [Azure AD DS 관리 되는 도메인에 대한 SKU를 변경][howto-change-sku]합니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

이 자습서에서는 Azure Portal를 사용 하 여 Azure AD DS에서 아웃 바운드 포리스트 트러스트를 만들고 구성 합니다. 시작하려면 먼저 [Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="networking-considerations"></a>네트워킹 고려 사항

Azure AD DS 리소스 포리스트를 호스트 하는 가상 네트워크에는 온-프레미스 Active Directory에 대한 네트워크 연결이 필요 합니다. 응용 프로그램 및 서비스에는 Azure AD DS 리소스 포리스트를 호스트 하는 가상 네트워크에 대한 네트워크 연결도 필요 합니다. Azure AD DS 리소스 포리스트에 대한 네트워크 연결은 always on 및 안정 되어야 합니다. 그렇지 않으면 사용자가 리소스에 대한 인증 또는 액세스에 실패할 수 있습니다.

Azure AD DS에서 포리스트 트러스트를 구성 하기 전에 Azure 및 온-프레미스 환경 간의 네트워킹에서 다음 요구 사항을 충족 하는지 확인 합니다.

* 개인 IP 주소를 사용 합니다. 동적 IP 주소 할당에는 DHCP를 사용 하지 마세요.
* 가상 네트워크 피어 링 및 라우팅이 Azure와 온-프레미스 간에 성공적으로 통신할 수 있도록 하기 위해 IP 주소 공간이 겹치지 않도록 합니다.
* Azure 가상 네트워크에서 S2S (사이트 간) VPN 또는 Express 경로 연결을 구성 하려면 게이트웨이 서브넷이 필요 합니다.
* 시나리오를 지원할 수 있는 충분 한 IP 주소를 사용 하 여 서브넷을 만듭니다.
* Azure AD DS에 자체 서브넷이 있는지 확인 하 고이 가상 네트워크 서브넷을 응용 프로그램 Vm 및 서비스와 공유 하지 마세요.
* 피어 링 가상 네트워크는 전이적이 지 않습니다.
    * Azure AD DS 리소스 포리스트 트러스트를 사용 하려는 모든 가상 네트워크에서 온-프레미스 AD DS 환경으로 azure 가상 네트워크 피어 링을 만들어야 합니다.
* 온-프레미스 Active Directory 포리스트에 지속적으로 네트워크 연결을 제공 합니다. 요청 시 연결을 사용 하지 않습니다.
* Azure AD DS 리소스 포리스트 이름과 온-프레미스 Active Directory 포리스트 이름 사이에 DNS (연속 이름 확인)가 있는지 확인 합니다.

## <a name="configure-dns-in-the-on-premises-domain"></a>온-프레미스 도메인에서 DNS 구성

온-프레미스 환경에서 Azure AD DS 관리 되는 도메인을 올바르게 확인 하려면 기존 DNS 서버에 전달자를 추가 해야 할 수 있습니다. Azure AD DS 관리 되는 도메인과 통신 하도록 온-프레미스 환경을 구성 하지 않은 경우 온-프레미스 AD DS 도메인에 대한 관리 워크스테이션에서 다음 단계를 완료 합니다.

1. 시작을 선택 합니다.  **관리 도구 | DNS**
1. DNS 서버 (예: *myAD01*)를 마우스 오른쪽 단추로 선택 하 고 **속성** 을 선택 합니다.
1. 전달자를 선택한 다음 **편집** **을 선택 하**여 추가 전달자를 추가 합니다.
1. *10.0.1.4* 및 *10.0.1.5*와 같은 Azure AD DS 관리 되는 도메인의 IP 주소를 추가 합니다.

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>온-프레미스 도메인에서 인바운드 포리스트 트러스트 만들기

온-프레미스 AD DS 도메인에는 Azure AD DS 관리 되는 도메인에 대한 들어오는 포리스트 트러스트가 필요 합니다. 이 트러스트는 온-프레미스 AD DS 도메인에서 수동으로 만들어야 하며 Azure Portal에서 만들 수 없습니다.

온-프레미스 AD DS 도메인에서 인바운드 트러스트를 구성 하려면 온-프레미스 AD DS 도메인에 대한 관리 워크스테이션에서 다음 단계를 완료 합니다.

1. 시작을 선택 합니다.  **관리 도구 | Active Directory 도메인 및 트러스트**
1. 도메인 (예: *onprem.contoso.com*)을 마우스 오른쪽 단추로 선택 하 고 **속성** 을 선택 합니다.
1. **트러스트** 탭, **새 트러스트** 를 차례로 선택 합니다.

   > [!NOTE]
   > **트러스트** 메뉴 옵션이 표시 되지 않으면 *포리스트 유형의* **속성** 에서를 선택 합니다. *리소스* 포리스트만 트러스트를 만들 수 있습니다. 포리스트 유형이 *사용자*인 경우 트러스트를 만들 수 없습니다. 현재 Azure AD DS 관리 되는 도메인의 포리스트 유형을 변경할 수 있는 방법은 없습니다. 리소스 포리스트로 관리 되는 도메인을 삭제 하 고 다시 만들어야 합니다.

1. Azure AD DS 도메인 이름에 이름 (예: *aadds.contoso.com*)을 입력 하 고 **다음** 을 선택 합니다.
1. **포리스트 트러스트**를 만드는 옵션을 선택한 다음 **단방향 (받는** 트러스트)을 만듭니다.
1. **이 도메인**에 대해서만 트러스트를 만들도록 선택 합니다. 다음 단계에서는 Azure AD DS 관리 되는 도메인에 대한 Azure Portal에서 트러스트를 만듭니다.
1. **포리스트 수준 인증**을 사용 하도록 선택한 다음, 트러스트 암호를 입력 하 고 확인 합니다. 다음 섹션의 Azure Portal에도 동일한 암호를 입력 합니다.
1. 기본 옵션을 사용 하 여 다음 몇 가지 창을 단계별로 실행 한 다음 아니요에 대한 옵션 **을 선택 하 여 보내는 트러스트를 확인 하지**않습니다.
1. **마침**을 선택합니다.

## <a name="create-outbound-forest-trust-in-azure-ad-ds"></a>Azure AD DS에서 아웃 바운드 포리스트 트러스트 만들기

온-프레미스 AD DS 도메인에서 Azure AD DS 관리 되는 도메인 및 만든 인바운드 포리스트 트러스트를 확인 하도록 구성 하면에서 아웃 바운드 포리스트 트러스트를 만들었습니다. 아웃 바운드 포리스트 트러스트는 온-프레미스 AD DS 도메인과 Azure AD DS 관리 되는 도메인 간의 트러스트 관계를 완료 합니다.

Azure Portal에서 Azure AD DS 관리 되는 도메인에 대한 아웃 바운드 트러스트를 만들려면 다음 단계를 완료 합니다.

1. Azure Portal에서 **Azure AD Domain Services**를 검색 하 고 선택한 다음 관리 되는 도메인 (예: *aadds.contoso.com* )을 선택 합니다.
1. Azure AD DS 관리 되는 도메인의 왼쪽에 있는 메뉴에서 **트러스트**를 선택 하 고 **+ 트러스트 추가** 를 선택 합니다.
1. 트러스트를 식별 하는 표시 이름, 온-프레미스의 신뢰할 수 있는 포리스트 DNS 이름 (예: *onprem.contoso.com* )을 입력 합니다.
1. 이전 섹션에서 온-프레미스 AD DS 도메인에 대한 인바운드 포리스트 트러스트를 구성할 때 사용한 것과 동일한 트러스트 암호를 제공 합니다.
1. *10.0.2.4* 및 *10.0.2.5* 와 같이 온-프레미스 AD DS 도메인에 대해 둘 이상의 DNS 서버를 제공 합니다.
1. 준비가 되 면 아웃 바운드 포리스트 트러스트를 **저장** 합니다.

    [Azure Portal에서 아웃 바운드 포리스트 트러스트 만들기](./media/create-forest-trust/portal-create-outbound-trust.png)

## <a name="validate-resource-authentication"></a>리소스 인증 유효성 검사

다음과 같은 일반적인 시나리오를 통해 포리스트 트러스트가 사용자를 올바르게 인증 하 고 리소스에 액세스할 수 있는지 확인할 수 있습니다.

* [Azure AD DS 리소스 포리스트에서 온-프레미스 사용자 인증](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [온-프레미스 사용자를 사용 하 여 Azure AD DS 리소스 포리스트의 리소스에 액세스](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [파일 및 프린터 공유 사용](#enable-file-and-printer-sharing)
    * [보안 그룹 만들기 및 멤버 추가](#create-a-security-group-and-add-members)
    * [포리스트 간 액세스를 위한 파일 공유 만들기](#create-a-file-share-for-cross-forest-access)
    * [리소스에 대한 크로스 포리스트 인증의 유효성 검사](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>Azure AD DS 리소스 포리스트에서 온-프레미스 사용자 인증

Azure AD DS 리소스 도메인에 가입 된 Windows Server 가상 컴퓨터가 있어야 합니다. 이 가상 머신을 사용 하 여 온-프레미스 사용자가 가상 머신에서 인증할 수 있는지 테스트 합니다.

1. 원격 데스크톱 및 Azure AD DS 관리자 자격 증명을 사용 하 여 Azure AD DS 리소스 포리스트에 가입 된 Windows Server VM에 연결 합니다. 네트워크 수준 인증 (NLA) 오류가 발생 하는 경우 사용 된 사용자 계정이 도메인 사용자 계정이 아닙니다 .를 확인 합니다.

    > [!NOTE]
    > Azure AD Domain Services에 연결 된 Vm에 안전 하 게 연결 하려면 지원 되는 Azure 지역에서 [Azure 방호 호스트 서비스](https://docs.microsoft.com/azure/bastion/bastion-overview) 를 사용할 수 있습니다.

1. 명령 프롬프트를 열고 `whoami` 명령을 사용 하 여 현재 인증 된 사용자의 고유 이름을 표시 합니다.

    ```console
    whoami /fqdn
    ```

1. `runas` 명령을 사용 하 여 온-프레미스 도메인에서 사용자로 인증 합니다. 다음 명령에서 `userUpn@trusteddomain.com`를 신뢰할 수 있는 온-프레미스 도메인의 사용자 UPN으로 바꿉니다. 명령에서 사용자의 암호를 묻는 메시지를 표시 합니다.

    ```console
    Runas /u:userUpn@trusteddomain.com cmd.exe
    ```

1. 인증에 성공 하면 새 명령 프롬프트가 열립니다. 새 명령 프롬프트의 제목에는 `running as userUpn@trusteddomain.com`포함 됩니다.
1. 새 명령 프롬프트에서 `whoami /fqdn`를 사용 하 여 온-프레미스 Active Directory에서 인증 된 사용자의 고유 이름을 확인 합니다.

### <a name="access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user"></a>온-프레미스 사용자를 사용 하 여 Azure AD DS 리소스 포리스트의 리소스에 액세스

Azure AD DS 리소스 포리스트에 연결 된 Windows Server VM을 사용 하 여 온-프레미스 도메인의 사용자와 온-프레미스 도메인의 컴퓨터에서 인증 하는 경우 사용자가 리소스 포리스트에서 호스트 되는 리소스에 액세스할 수 있는 시나리오를 테스트할 수 있습니다. 다음 예에서는 다양 한 일반적인 시나리오를 만들고 테스트 하는 방법을 보여 줍니다.

#### <a name="enable-file-and-printer-sharing"></a>파일 및 프린터 공유 사용

1. 원격 데스크톱 및 Azure AD DS 관리자 자격 증명을 사용 하 여 Azure AD DS 리소스 포리스트에 가입 된 Windows Server VM에 연결 합니다. 네트워크 수준 인증 (NLA) 오류가 발생 하는 경우 사용 된 사용자 계정이 도메인 사용자 계정이 아닙니다 .를 확인 합니다.

    > [!NOTE]
    > Azure AD Domain Services에 연결 된 Vm에 안전 하 게 연결 하려면 지원 되는 Azure 지역에서 [Azure 방호 호스트 서비스](https://docs.microsoft.com/azure/bastion/bastion-overview) 를 사용할 수 있습니다.

1. **Windows 설정을**열고 **네트워크 및 공유 센터**를 검색 하 여 선택 합니다.
1. **고급 공유 설정 변경** 에 대한 옵션을 선택 합니다.
1. **도메인 프로필**에서 **파일 및 프린터 공유 켜기** 를 선택 하 고 **변경 내용 저장**을 선택 합니다.
1. **네트워크 및 공유 센터**를 닫습니다.

#### <a name="create-a-security-group-and-add-members"></a>보안 그룹 만들기 및 멤버 추가

1. **Active Directory 사용자 및 컴퓨터**를 엽니다.
1. 도메인 이름을 마우스 오른쪽 단추로 선택 하 고 **새로 만들기**를 선택한 다음 **조직 구성 단위**를 선택 합니다.
1. 이름 상자에 *Localobjects*를 입력 한 다음 **확인**을 선택 합니다.
1. 탐색 창에서 **Localobjects** 를 선택 하 고 마우스 오른쪽 단추로 클릭 합니다. **새로 만들기** , **그룹**을 차례로 선택 합니다.
1. **그룹 이름** 상자에 *FileServerAccess* 을 입력 합니다. **그룹 범위**에서 **도메인 로컬**을 선택 하 고 **확인**을 선택 합니다.
1. 내용 창에서 **FileServerAccess**를 두 번 클릭 합니다. **멤버**를 선택 하 고 **추가**를 선택한 다음 **위치**를 선택 합니다.
1. **위치** 보기에서 온-프레미스 Active Directory를 선택 하 고 **확인**을 선택 합니다.
1. **선택할 개체 이름을 입력 하십시오** . 상자에 *도메인 사용자* 를 입력 합니다. **이름 확인**을 선택 하 고 온-프레미스 Active Directory에 대한 자격 증명을 제공한 다음 **확인**을 선택 합니다.

    > [!NOTE]
    > 트러스트 관계가 단방향 이기 때문에 자격 증명을 제공 해야 합니다. 즉, Azure AD DS의 사용자는 리소스에 액세스 하거나 신뢰할 수 있는 (온-프레미스) 도메인에서 사용자 또는 그룹을 검색할 수 없습니다.

1. 온-프레미스 Active Directory의 **Domain Users** 그룹은 **FileServerAccess** 그룹의 구성원 이어야 합니다. **확인** 을 선택 하 여 그룹을 저장 하 고 창을 닫습니다.

#### <a name="create-a-file-share-for-cross-forest-access"></a>포리스트 간 액세스를 위한 파일 공유 만들기

1. Azure AD DS 리소스 포리스트에 연결 된 Windows Server VM에서 폴더를 만들고 *CrossForestShare*와 같은 이름을 제공 합니다.
1. 폴더를 마우스 오른쪽 단추로 선택 하 고 **속성**을 선택 합니다.
1. **보안** 탭을 선택한 다음 **편집**을 선택 합니다.
1. *CrossForestShare에 대한 사용 권한* 대화 상자에서 **추가**를 선택 합니다.
1. **선택할 개체 이름을 입력**하십시오 .에 *FileServerAccess* 을 입력 한 다음 **확인**을 선택 합니다.
1. **그룹 또는 사용자 이름** 목록에서 *FileServerAccess* 를 선택 합니다. **FileServerAccess에 대한 사용 권한** 목록에서 **수정** 및 **쓰기** 권한에 대해 *허용* 을 선택 하 고 **확인**을 선택 합니다.
1. **공유** 탭을 선택 하 고 **고급 공유 ...** 를 선택 합니다.
1. **이 폴더 공유**를 선택한 다음 **공유 이름** 에 파일 공유에 대한 기억 하기 쉬운 이름 (예: *CrossForestShare*)을 입력 합니다.
1. **권한**을 선택 합니다. **모든 사람에 대한 사용 권한** 목록에서 **변경** 권한에 대해 **허용** 을 선택 합니다.
1. **확인** 을 두 번 선택 하 고 **닫기**를 선택 합니다.

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>리소스에 대한 크로스 포리스트 인증의 유효성 검사

1. 온-프레미스 Active Directory의 사용자 계정을 사용 하 여 온-프레미스 Active Directory에 가입 된 Windows 컴퓨터에 로그인 합니다.
1. **Windows 탐색기**를 사용 하 여 정규화 된 호스트 이름 및 공유 (예: `\\fs1.aadds.contoso.com\CrossforestShare`)를 사용 하 여 만든 공유에 연결 합니다.
1. 쓰기 권한의 유효성을 검사 하려면 폴더에서 마우스 오른쪽 단추를 선택 하 고 **새로 만들기**를 선택한 다음 **텍스트 문서**를 선택 합니다. 기본 이름 **새 텍스트 문서**를 사용 합니다.

    쓰기 권한이 올바르게 설정 된 경우 새 텍스트 문서가 만들어집니다. 다음 단계에서는 파일을 적절 하 게 열고, 편집 하 고, 삭제 합니다.
1. 읽기 권한의 유효성을 검사 하려면 **새 텍스트 문서**를 엽니다.
1. 수정 권한의 유효성을 검사 하려면 파일에 텍스트를 추가 하 고 **메모장**을 닫습니다. 변경 내용을 저장 하 라는 메시지가 표시 되 면 **저장**을 선택 합니다.
1. 삭제 권한의 유효성을 검사 하려면 **새 텍스트 문서** 를 마우스 오른쪽 단추로 선택 하 고 **삭제**를 선택 합니다. **예** 를 선택 하 여 파일 삭제를 확인 합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * Azure AD DS 연결을 지원 하도록 온-프레미스 AD DS 환경에서 DNS 구성
> * 온-프레미스 AD DS 환경에서 단방향 인바운드 포리스트 트러스트 만들기
> * Azure AD DS에서 단방향 아웃 바운드 포리스트 트러스트 만들기
> * 인증 및 리소스 액세스에 대한 트러스트 관계 테스트 및 유효성 검사

Azure AD DS의 포리스트 형식에 대한 자세한 개념 정보는 [리소스 포리스트 란 무엇 인가요?][concepts-forest] [azure AD DS에서 포리스트 트러스트는 어떻게 작동 하나요?][concepts-trust] 를 참조 하세요.

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
[howto-change-sku]: change-sku.md
