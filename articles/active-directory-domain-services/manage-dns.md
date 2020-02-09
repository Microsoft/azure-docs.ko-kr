---
title: Azure AD Domain Services에 대한 DNS 관리 | Microsoft Docs
description: DNS 서버 도구를 설치 하 여 Azure Active Directory Domain Services 관리 되는 도메인에 대한 DNS를 관리 하는 방법을 알아봅니다.
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: iainfou
ms.openlocfilehash: f47f112991a4fe2e5b245920db98e5ae7617161a
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74704940"
---
# <a name="administer-dns-in-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services 관리 되는 도메인에서 DNS 관리

Azure Active Directory Domain Services (Azure AD DS)에서 키 구성 요소는 DNS (도메인 이름 확인)입니다. Azure AD DS에는 관리 되는 도메인에 대한 이름 확인을 제공 하는 DNS 서버가 포함 되어 있습니다. 이 DNS 서버에는 기본 제공 DNS 레코드와 서비스를 실행 하는 데 사용할 수 있는 주요 구성 요소에 대한 업데이트가 포함 되어 있습니다.

사용자 고유의 응용 프로그램 및 서비스를 실행 하는 경우 도메인에 가입 되지 않은 컴퓨터에 대한 DNS 레코드를 만들고, 부하 분산 장치에 대한 가상 IP 주소를 구성 하거나, 외부 DNS 전달자를 설정 해야 할 수 있습니다. *AAD DC 관리자* 그룹에 속한 사용자에 게는 Azure AD DS 관리 되는 도메인에 대한 dns 관리 권한이 부여 되 고 사용자 지정 dns 레코드를 만들고 편집할 수 있습니다.

하이브리드 환경에서 온-프레미스 AD DS 환경에 구성 된 DNS 영역 및 레코드는 Azure AD DS와 동기화 되지 않습니다. 사용자 고유의 DNS 항목을 정의 하 고 사용 하려면 Azure AD DS DNS 서버에 레코드를 만들거나 사용자 환경의 기존 DNS 서버를 가리키는 조건부 전달자를 사용 합니다.

이 문서에서는 dns 서버 도구를 설치한 다음 DNS 콘솔을 사용 하 여 Azure AD DS에서 레코드를 관리 하는 방법을 보여 줍니다.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료 하려면 다음 리소스와 권한이 필요 합니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant] [Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 자습서를 완료 하 여 [Azure Active Directory Domain Services 인스턴스를 만들고 구성][create-azure-ad-ds-instance]합니다.
* Azure AD DS 관리 되는 도메인에 가입 된 Windows Server 관리 VM입니다.
    * 필요한 경우 자습서를 완료 하 여 [Windows SERVER VM을 만들고 관리 되는 도메인에 가입 시킵니다][create-join-windows-vm].
* Azure AD 테넌트의 *Azure AD DC Administrators* 그룹에 속한 멤버인 사용자 계정

## <a name="install-dns-server-tools"></a>DNS 서버 도구 설치

Azure AD DS에서 DNS 레코드를 만들고 수정 하려면 DNS 서버 도구를 설치 해야 합니다. 이러한 도구는 Windows Server의 기능으로 설치할 수 있습니다. Windows 클라이언트에 관리 도구를 설치 하는 방법에 대한 자세한 내용은 install [원격 서버 관리 도구 (RSAT)][install-rsat]를 참조 하십시오.

1. 관리 VM에 로그인 합니다. Azure Portal를 사용 하 여 연결 하는 방법에 대한 단계는 [Windows SERVER VM에 연결][connect-windows-server-vm]을 참조 하세요.
1. VM에 로그인할 때 **서버 관리자**가 기본적으로 열리지 않는 경우 **시작** 메뉴를 선택한 다음, **서버 관리자**를 선택합니다.
1. **서버 관리자** 창의 *대시보드* 창에서 **역할 및 기능 추가**를 선택합니다.
1. *역할 및 기능 추가 마법사*의 **시작하기 전에** 페이지에서 **다음**을 선택합니다.
1. *설치 유형*에서 **역할 기반 또는 기능 기반 설치** 옵션을 선택한 상태로 두고, **다음**을 선택합니다.
1. **서버 선택** 페이지에서 서버 풀의 현재 VM (예: *myvm.aadds.contoso.com*)을 선택 하 고 **다음**을 선택 합니다.
1. **서버 역할** 페이지에서 **다음**을 클릭합니다.
1. **기능** 페이지에서 **원격 서버 관리 도구** 노드, **역할 관리 도구** 노드를 차례로 펼칩니다. 역할 관리 도구 목록에서 **DNS 서버 도구** 기능을 선택합니다.

    ![사용 가능한 역할 관리 도구 목록에서 DNS 서버 도구를 설치 하도록 선택 합니다.](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)

1. **확인** 페이지에서 **설치**를 선택합니다. 그룹 정책 관리 도구를 설치 하는 데 1 ~ 2 시간이 걸릴 수 있습니다.
1. 기능 설치가 완료되면 **닫기**를 선택하여 **역할 및 기능 추가** 마법사를 종료합니다.

## <a name="open-the-dns-management-console-to-administer-dns"></a>Dns 관리 콘솔을 열어 DNS 관리

DNS 서버 도구를 설치 하면 Azure AD DS 관리 되는 도메인에서 DNS 레코드를 관리할 수 있습니다.

> [!NOTE]
> Azure AD DS 관리 되는 도메인에서 DNS를 관리 하려면 *AAD DC Administrators* 그룹의 구성원 인 사용자 계정에 로그인 해야 합니다.

1. 시작 화면에서 **관리 도구**를 선택 합니다. 이전 섹션에 설치 된 **DNS** 를 비롯 하 여 사용 가능한 관리 도구 목록이 표시 됩니다. Dns **를 선택 하 여 Dns** 관리 콘솔을 시작 합니다.
1. **Dns 서버에 연결** 대화 상자에서 **다음 컴퓨터**를 선택 하 고 관리 되는 도메인의 DNS 도메인 이름 (예: *aadds.contoso.com*)을 입력 합니다.

    ![DNS 콘솔에서 Azure AD DS 관리 되는 도메인에 연결](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)

1. DNS 콘솔은 지정 된 Azure AD DS 관리 되는 도메인에 연결 합니다. 필요에 따라 **전방 조회 영역** 또는 **역방향 조회 영역** 을 확장 하 여 필요한 DNS 항목을 만들거나 기존 레코드를 편집 합니다.

    ![DNS 콘솔 - 도메인 관리](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)

> [!WARNING]
> DNS 서버 도구를 사용 하 여 레코드를 관리 하는 경우 Azure AD DS에서 사용 하는 기본 제공 DNS 레코드를 삭제 하거나 수정 하지 않도록 해야 합니다. 기본 제공 DNS 레코드는 도메인 DNS 레코드, 이름 서버 레코드 및 DC 위치에 사용되는 기타 레코드를 포함합니다. 이러한 레코드를 수정하는 경우 가상 네트워크에서 도메인 서비스가 중단됩니다.

## <a name="next-steps"></a>다음 단계

DNS 관리에 대한 자세한 내용은 [Technet의 DNS 도구 문서](https://technet.microsoft.com/library/cc753579.aspx)를 참조하세요.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
