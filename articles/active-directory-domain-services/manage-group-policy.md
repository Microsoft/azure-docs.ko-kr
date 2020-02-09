---
title: Azure AD Domain Services |에서 그룹 정책 만들기 및 관리 Microsoft Docs
description: 기본 제공 Gpo (그룹 정책 개체)를 편집 하 고 관리 되는 Azure Active Directory Domain Services 도메인에서 사용자 지정 정책을 만드는 방법에 대해 알아봅니다.
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: iainfou
ms.openlocfilehash: 32020341c7ac3bf33082ba365f053a613dcdffc2
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74705091"
---
# <a name="administer-group-policy-in-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services 관리 되는 도메인의 그룹 정책 관리

Azure Active Directory Domain Services (Azure AD DS)의 사용자 및 컴퓨터 개체에 대한 설정은 Gpo (그룹 정책 개체)를 사용 하 여 관리 되는 경우가 많습니다. Azure AD DS에는 *Aaddc 사용자* 및 *Aaddc 컴퓨터* 컨테이너에 대한 기본 제공 gpo가 포함 됩니다. 이러한 기본 제공 Gpo를 사용자 지정 하 여 사용자 환경에 필요한 대로 그룹 정책를 구성할 수 있습니다. Azure *AD DC administrators* 그룹의 구성원은 azure AD DS 도메인에서 그룹 정책 관리 권한을 가지 며 사용자 지정 Gpo 및 ou (조직 구성 단위)를 만들 수도 있습니다. 그룹 정책 정의 및 작동 방법에 대한 자세한 내용은 [그룹 정책 개요][group-policy-overview]를 참조 하세요.

하이브리드 환경에서 온-프레미스 AD DS 환경에 구성 된 그룹 정책은 Azure AD DS와 동기화 되지 않습니다. Azure AD DS에서 사용자 또는 컴퓨터에 대한 구성 설정을 정의 하려면 기본 Gpo 중 하나를 편집 하거나 사용자 지정 GPO를 만듭니다.

이 문서에서는 그룹 정책 관리 도구를 설치한 다음 기본 제공 Gpo를 편집 하 고 사용자 지정 Gpo를 만드는 방법을 보여 줍니다.

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

> [!NOTE]
> [Azure AD DS의 도메인 컨트롤러에 대한 액세스 권한이 없기](faqs.md#can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop)때문에 관리 되는 도메인에서 그룹 정책 관리 템플릿에 대한 중앙 저장소를 만들고 사용할 수 없습니다. [Sysvol은 온-프레미스 Azure AD Connect 동기화에 포함 되지](synchronization.md#what-isnt-synchronized-to-azure-ad-ds)않으므로 azure AD를 통해 온-프레미스 중앙 저장소를 만들고 azure AD DS와 동기화 할 수 없습니다.

## <a name="install-group-policy-management-tools"></a>그룹 정책 관리 도구 설치

그룹 정책 개체 (Gpo)를 만들고 구성 하려면 그룹 정책 관리 도구를 설치 해야 합니다. 이러한 도구는 Windows Server의 기능으로 설치할 수 있습니다. Windows 클라이언트에 관리 도구를 설치 하는 방법에 대한 자세한 내용은 install [원격 서버 관리 도구 (RSAT)][install-rsat]를 참조 하십시오.

1. 관리 VM에 로그인 합니다. Azure Portal를 사용 하 여 연결 하는 방법에 대한 단계는 [Windows SERVER VM에 연결][connect-windows-server-vm]을 참조 하세요.
1. VM에 로그인하면 **서버 관리자**가 기본적으로 열립니다. 그렇지 않은 경우 **시작** 메뉴에서 **서버 관리자**를 선택합니다.
1. **서버 관리자** 창의 *대시보드* 창에서 **역할 및 기능 추가**를 선택합니다.
1. *역할 및 기능 추가 마법사*의 **시작하기 전에** 페이지에서 **다음**을 선택합니다.
1. *설치 유형*에서 **역할 기반 또는 기능 기반 설치** 옵션을 선택한 상태로 두고, **다음**을 선택합니다.
1. **서버 선택** 페이지에서 서버 풀의 현재 VM (예: *myvm.aadds.contoso.com*)을 선택 하 고 **다음**을 선택 합니다.
1. **서버 역할** 페이지에서 **다음**을 클릭합니다.
1. **기능** 페이지에서 **그룹 정책 관리** 기능을 선택합니다.

    ![기능 페이지에서 ' 그룹 정책 관리 '를 설치 합니다.](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)

1. **확인** 페이지에서 **설치**를 선택합니다. 그룹 정책 관리 도구를 설치 하는 데 1 ~ 2 시간이 걸릴 수 있습니다.
1. 기능 설치가 완료되면 **닫기**를 선택하여 **역할 및 기능 추가** 마법사를 종료합니다.

## <a name="open-the-group-policy-management-console-and-edit-an-object"></a>그룹 정책 관리 콘솔를 열고 개체를 편집 합니다.

Azure AD DS 관리 되는 도메인의 사용자 및 컴퓨터에 대한 기본 Gpo (그룹 정책 개체)가 존재 합니다. 이전 섹션에서 설치 된 그룹 정책 관리 기능을 사용 하 여 기존 GPO를 보고 편집 해 보겠습니다. 다음 섹션에서는 사용자 지정 GPO를 만듭니다.

> [!NOTE]
> Azure AD DS 관리 되는 도메인에서 그룹 정책을 관리 하려면 *AAD DC Administrators* 그룹의 구성원 인 사용자 계정에 로그인 해야 합니다.

1. 시작 화면에서 **관리 도구**를 선택 합니다. 이전 섹션에 설치 된 **그룹 정책 관리** 를 비롯 하 여 사용 가능한 관리 도구 목록이 표시 됩니다.
1. 그룹 정책 관리 콘솔 (GPMC)를 열려면 **그룹 정책 관리**를 선택 합니다.

    ![그룹 정책 관리 콘솔를 열어 그룹 정책 개체를 편집할 수 있습니다.](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

Azure AD DS 관리 되는 도메인에는 두 개의 기본 제공 Gpo (그룹 정책 개체)가 있습니다. *Aaddc 컴퓨터* 컨테이너와 *Aaddc 사용자* 컨테이너에 대한 하나는 1입니다. 이러한 Gpo를 사용자 지정 하 여 Azure AD DS 관리 되는 도메인 내에서 필요에 따라 그룹 정책을 구성할 수 있습니다.

1. **그룹 정책 관리** 콘솔에서 **포리스트: aadds.contoso.com** 노드를 확장 합니다. 그런 다음 **도메인** 노드를 확장 합니다.

    *Aaddc 컴퓨터* 와 *Aaddc 사용자*에 대한 두 개의 기본 제공 컨테이너가 있습니다. 이러한 각 컨테이너에는 기본 GPO가 적용 됩니다.

    ![기본 제공 Gpo가 기본 ' AADDC 컴퓨터 ' 및 ' AADDC 사용자 ' 컨테이너에 적용 됨](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)

1. 이러한 기본 제공 Gpo를 사용자 지정 하 여 Azure AD DS 관리 되는 도메인에서 특정 그룹 정책을 구성할 수 있습니다. *Aaddc 컴퓨터 gpo*와 같은 gpo 중 하나를 마우스 오른쪽 단추로 선택 하 고 **편집 ...** 을 선택 합니다.

    ![기본 제공 Gpo 중 하나를 ' 편집 ' 하는 옵션을 선택 합니다.](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)

1. *계정 정책과*같이 GPO를 사용자 지정할 수 있는 그룹 정책 관리 편집기 도구가 열립니다.

    ![필요에 따라 설정을 구성 하도록 GPO 사용자 지정](./media/active-directory-domain-services-admin-guide/gp-editor.png)

    완료 되 면 **파일 > 저장** 을 선택 하 여 정책을 저장 합니다. 컴퓨터는 기본적으로 90 분 마다 그룹 정책 새로 고침을 수행 하 고 변경 내용을 적용 합니다.

## <a name="create-a-custom-group-policy-object"></a>사용자 지정 그룹 정책 개체 만들기

유사한 정책 설정을 그룹화 하려면 모든 필수 설정을 단일 기본 GPO에 적용 하는 대신 Gpo를 추가로 만드는 경우가 많습니다. Azure AD DS을 사용 하 여 사용자 고유의 사용자 지정 그룹 정책 개체를 만들거나 가져와서 사용자 지정 OU에 연결할 수 있습니다. 먼저 사용자 지정 OU를 만들어야 하는 경우 [Azure AD DS 관리 되는 도메인에서 사용자 지정 ou 만들기](create-ou.md)를 참조 하세요.

1. **그룹 정책 관리** 콘솔에서 *mycustomou*와 같은 사용자 지정 ou (조직 구성 단위)를 선택 합니다. OU를 마우스 오른쪽 단추로 선택 하 고 **이 도메인에서 GPO를 만들어 여기에 연결 ...을**선택 합니다.

    ![그룹 정책 관리 콘솔에서 사용자 지정 GPO 만들기](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)

1. 새 GPO의 이름 (예: *내 사용자 지정 gpo*)을 지정 하 고 **확인**을 선택 합니다. 필요에 따라 기존 GPO 및 정책 옵션 집합에서이 사용자 지정 GPO를 기반으로 할 수 있습니다.

    ![새 사용자 지정 GPO의 이름 지정](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)

1. 사용자 지정 GPO가 만들어지고 사용자 지정 OU에 연결 됩니다. 이제 정책 설정을 구성 하려면 사용자 지정 GPO를 마우스 오른쪽 단추로 선택 하 고 **편집 ...** 을 선택 합니다.

    ![사용자 지정 GPO를 ' 편집 ' 하는 옵션을 선택 합니다.](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)

1. GPO를 사용자 지정할 수 있도록 **그룹 정책 관리 편집기** 열립니다.

    ![필요에 따라 설정을 구성 하도록 GPO 사용자 지정](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)

    완료 되 면 **파일 > 저장** 을 선택 하 여 정책을 저장 합니다. 컴퓨터는 기본적으로 90 분 마다 그룹 정책 새로 고침을 수행 하 고 변경 내용을 적용 합니다.

## <a name="next-steps"></a>다음 단계

그룹 정책 관리 콘솔를 사용 하 여 구성할 수 있는 사용 가능한 그룹 정책 설정에 대한 자세한 내용은 [그룹 정책 기본 설정 항목 작업][group-policy-console]을 참조 하세요.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[group-policy-overview]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831791(v=ws.11)
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
[group-policy-console]: /previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789194(v=ws.11)
