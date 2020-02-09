---
title: '자습서: Azure Active Directory로 자동 사용자 프로비전을 위한 G Suite 구성 | Microsoft Docs'
description: 사용자 계정을 Azure AD에서 G Suite로 자동으로 프로비전 및 프로비전 해제하는 방법을 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/06/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 969a2fb5444ae8ece2aa302c04a5bbb85dcca917
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77057722"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비전을 위한 G Suite 구성

이 자습서에서는 사용자 및/또는 그룹을 G Suite로 자동으로 프로 비전 및 프로 비전 해제 하도록 Azure AD를 구성 하기 위해 G Suite 및 Azure Active Directory (Azure AD)에서 수행 하는 단계를 설명 합니다.

> [!NOTE]
> 이 자습서에서는 Azure AD 사용자 프로비저닝 서비스에 기반하여 구축된 커넥터에 대해 설명합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)를 참조하세요.

> [!NOTE]
> G Suite 커넥터는 최근 2019 년 10 월에 업데이트 되었습니다. G Suite 커넥터에 대 한 변경 내용은 다음과 같습니다.
> - 추가 G Suite 사용자 및 그룹 특성에 대 한 지원이 추가 되었습니다. 
> - G Suite 대상 특성 이름이 [여기](https://developers.google.com/admin-sdk/directory)에 정의 된 것과 일치 하도록 업데이트 되었습니다.
> - 기본 특성 매핑을 업데이트 했습니다.

## <a name="prerequisites"></a>필수 조건

G Suite와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 테넌트
- [G Suite 테넌트](https://gsuite.google.com/pricing.html)
- 관리자 권한이 있는 G Suite의 사용자 계정

## <a name="assign-users-to-g-suite"></a>G Suite에 사용자 할당

Azure Active Directory는 할당 이라는 개념을 사용 하 여 선택한 앱에 대 한 액세스 권한을 받아야 하는 사용자를 결정 합니다. 자동 사용자 프로 비전의 컨텍스트에서는 Azure AD의 응용 프로그램에 할당 된 사용자 및/또는 그룹만 동기화 됩니다.

자동 사용자 프로비저닝을 구성 하 고 사용 하도록 설정 하기 전에 G Suite에 대 한 액세스 권한이 필요한 Azure AD의 사용자 및/또는 그룹을 결정 해야 합니다. 일단 결정 되 면 다음 지침에 따라 이러한 사용자 및/또는 그룹을 G Suite에 할당할 수 있습니다.

* [엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-g-suite"></a>G Suite에 사용자를 할당 하기 위한 주요 팁

* 자동 사용자 프로 비전 구성을 테스트 하려면 단일 Azure AD 사용자를 G Suite에 할당 하는 것이 좋습니다. 추가 사용자 및/또는 그룹은 나중에 할당할 수도 있습니다.

* 사용자를 G Suite에 할당할 때 할당 대화 상자에서 유효한 응용 프로그램별 역할 (사용 가능한 경우)을 선택 해야 합니다. **기본 액세스** 역할이 있는 사용자는 프로비전에서 제외됩니다.

## <a name="setup-g-suite-for-provisioning"></a>프로 비전을 위한 G 모음 설정

Azure AD를 사용 하 여 자동 사용자 프로 비전을 위한 G Suite를 구성 하기 전에 G Suite에서 SCIM 프로 비전을 사용 하도록 설정 해야 합니다.

1. 관리자 계정으로 [G Suite 관리 콘솔](https://admin.google.com/) 에 로그인 한 다음 **보안**을 선택 합니다. 링크가 보이지 않으면 화면 아래쪽에 있는 **기타 컨트롤** 메뉴에 숨겨져 있을 수 있습니다.

    ![[보안]을 선택합니다.][10]

1. **보안** 페이지에서 **API 참조**를 선택합니다.

    ![[API 참조]를 선택합니다.][15]

1. **API 액세스 사용**을 선택합니다.

    ![[API 참조]를 선택합니다.][16]

   > [!IMPORTANT]
   > G Suite에 프로 비전 하려는 모든 사용자에 대해 Azure AD의 사용자 이름이 사용자 지정 도메인에 연결 되어야 **합니다** . 예를 들면 bob@contoso.onmicrosoft.com과 같은 사용자 이름은 G Suite에서 허용되지 않습니다. 반면에 bob@contoso.com은 허용됩니다. [여기](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)에 설명 된 지침에 따라 기존 사용자의 도메인을 변경할 수 있습니다.

1. Azure AD를 사용 하 여 원하는 사용자 지정 도메인을 추가 하 고 확인 한 후에는 G Suite를 사용 하 여 다시 확인 해야 합니다. G Suite의 도메인을 확인 하려면 다음 단계를 참조 하세요.

    a. [G Suite 관리 콘솔](https://admin.google.com/)에서 **도메인**을 선택 합니다.

    ![도메인 선택][20]

    b. **Add a domain or a domain alias(도메인 또는 도메인 별칭 추가)** 를 선택합니다.

    ![새 도메인 추가][21]

    c. **다른 도메인 추가**를 선택하고 추가하려는 도메인 이름을 입력합니다.

    ![사용자의 도메인 이름을 입력합니다.][22]

    . **Continue and verify domain ownership(계속해서 도메인 소유권 확인)** 을 선택합니다. 그런 다음 도메인 이름을 소유하고 있는지 확인하는 단계를 따릅니다. Google을 사용 하 여 도메인을 확인 하는 방법에 대 한 포괄적인 지침은 [사이트 소유권 확인](https://support.google.com/webmasters/answer/35179)을 참조 하세요.

    e. G Suite에 추가 하려는 추가 도메인에 대해 위의 단계를 반복 합니다.

1. 다음으로 G Suite에서 사용자 프로 비전을 관리 하는 데 사용할 관리자 계정을 결정 합니다. **관리자 역할**로 이동 합니다.

    ![Google Apps 선택][26]

1. 해당 계정의 **관리자 역할** 에 대해 해당 역할에 대 한 **권한을** 편집 합니다. 이 계정을 프로비전에 사용할 수 있도록 모든 **관리자 API 권한**이 사용하도록 설정되어 있는지 확인합니다.

    ![Google Apps 선택][27]

## <a name="add-g-suite-from-the-gallery"></a>갤러리에서 G Suite 추가

Azure AD를 사용 하 여 자동 사용자 프로 비전을 위한 G Suite를 구성 하려면 Azure AD 응용 프로그램 갤러리의 G Suite를 관리 되는 SaaS 응용 프로그램 목록에 추가 해야 합니다. 

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 패널에서 **Azure Active Directory**를 선택 합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

1. 새 응용 프로그램을 추가 하려면 창의 위쪽에 있는 **새 응용 프로그램** 단추를 선택 합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

1. 검색 상자에 **g suite**를 입력 하 고 결과 패널에서 **g suite** 를 선택한 후 **추가** 단추를 클릭 하 여 응용 프로그램을 추가 합니다.

    ![결과 목록의 G Suite](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-g-suite"></a>G Suite에 대 한 자동 사용자 프로 비전 구성 

이 섹션에서는 azure ad의 사용자 및/또는 그룹 할당을 기반으로 G Suite에서 사용자 및/또는 그룹을 만들고, 업데이트 하 고, 비활성화 하도록 Azure AD 프로 비전 서비스를 구성 하는 단계를 안내 합니다.

> [!TIP]
> G [Suite single sign-on 자습서](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-tutorial)에 제공 된 지침에 따라 g SUITE에 SAML 기반 Single Sign-On를 사용 하도록 선택할 수도 있습니다. Single Sign-On과 자동 사용자 프로비저닝은 서로 보완적이지만, 별개로 구성할 수 있습니다.

> [!NOTE]
> G Suite의 디렉터리 API 엔드포인트에 대 한 자세한 내용은 [디렉터리 api](https://developers.google.com/admin-sdk/directory)를 참조 하세요.

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>Azure AD에서 G Suite에 대 한 자동 사용자 프로 비전을 구성 하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 응용 프로그램**을 선택한 다음 **모든 응용 프로그램**을 선택 합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

1. 애플리케이션 목록에서 **G Suite**를 선택합니다.

    ![애플리케이션 목록의 G Suite 링크](common/all-applications.png)

1. **프로비전** 탭을 선택합니다.

    ![프로 비전 탭](common/provisioning.png)

1. **프로비전 모드**를 **자동**으로 설정합니다.

    ![프로 비전 탭](common/provisioning-automatic.png)

1. **관리자 자격 증명** 섹션에서 **권한 부여**를 선택합니다. 그러면 새 브라우저 창에서 Google 권한 부여 대화 상자가 열립니다.

    ![G Suite 권한 부여](media/google-apps-provisioning-tutorial/authorize.png)

1. Azure AD 사용 권한을 부여 하 여 G Suite 테넌트를 변경할 것인지 확인 합니다. **수락**을 선택합니다.

    ![사용 권한을 확인합니다.][28]

1. Azure Portal에서 **연결 테스트**를 선택하여 Azure AD가 앱에 연결할 수 있는지 확인합니다. 연결에 실패하면 G Suite 계정에 팀 관리자 권한이 있는지 확인합니다. 그런 다음 **권한 부여** 단계를 다시 시도합니다.

1. **알림 메일** 필드에 프로비저닝 오류 알림을 받을 개인 또는 그룹의 메일 주소를 입력하고, **오류가 발생할 경우, 메일 알림 보내기** 확인란을 선택합니다.

    ![알림 이메일](common/provisioning-notification-email.png)

1. **저장**을 클릭합니다.

1. **매핑** 섹션에서 **Azure Active Directory 사용자를 G Suite에 동기화를**선택 합니다.

    ![G Suite 사용자 매핑](media/google-apps-provisioning-tutorial/usermappings.png)

1. **특성 매핑** 섹션에서 Azure AD에서 G Suite로 동기화 되는 사용자 특성을 검토 합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 G Suite의 사용자 계정을 일치 시키는 데 사용 됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

    ![G Suite 사용자 특성](media/google-apps-provisioning-tutorial/userattributes.png)

1. **매핑** 섹션에서 **G Suite에 Azure Active Directory 그룹 동기화를**선택 합니다.

    ![G Suite 그룹 매핑](media/google-apps-provisioning-tutorial/groupmappings.png)

1. **특성 매핑** 섹션에서 Azure AD에서 G Suite로 동기화 되는 그룹 특성을 검토 합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 G Suite의 그룹을 일치 시키는 데 사용 됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다. UI는 Azure AD와 G Suite 간의 기본 특성 매핑 집합을 표시 합니다. 새 매핑 추가를 클릭 하 여 조직 단위와 같은 추가 특성을 추가 하도록 선택할 수 있습니다.

    ![G Suite 그룹 특성](media/google-apps-provisioning-tutorial/groupattributes.png)

1. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

1. G Suite에 대 한 Azure AD 프로 비전 서비스를 사용 하도록 **설정 하려면 설정** 섹션에서 **프로 비전 상태** 를 **켜기** 로 변경 합니다.

    ![프로비전 상태 켜기로 전환](common/provisioning-toggle-on.png)

1. **설정** 섹션의 **범위** 에서 원하는 값을 선택 하 여 G Suite에 프로 비전 하려는 사용자 및/또는 그룹을 정의 합니다.

    ![프로비전 범위](common/provisioning-scope.png)

1. 프로비전할 준비가 되면 **저장**을 클릭합니다.

    ![프로비전 구성 저장](common/provisioning-configuration-save.png)

이 작업은 **설정**의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화를 시작합니다. 초기 동기화는 Azure AD 프로비전 서비스가 실행되는 동안 약 40분마다 발생하는 후속 동기화보다 더 많은 시간이 걸립니다. **동기화 세부 정보** 섹션을 사용 하 여 진행률을 모니터링 하 고 프로 비전 활동 보고서에 대 한 링크를 팔 로우 하 여 G Suite의 Azure AD 프로 비전 서비스에서 수행 하는 모든 작업을 설명 합니다.

> [!NOTE]
> 사용자에 게 Azure AD 사용자의 메일 주소를 사용 하는 기존 개인/소비자 계정이 이미 있는 경우 디렉터리 동기화를 수행 하기 전에 Google Transfer Tool을 사용 하 여 해결할 수 있는 몇 가지 문제가 발생할 수 있습니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../app-provisioning/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="common-issues"></a>일반적인 문제
* 권한 부여 오류는 GSuite의 관리자에 대 한 연결을 설정 하는 데 사용 되는 계정이 아닌 경우에 발생할 수 있습니다. 액세스 권한을 부여 하는 데 사용 되는 계정에 사용자가 프로 비전 해야 하는 **모든 도메인** 에 대 한 관리자 권한이 있는지 확인 합니다. 
* Azure AD는 응용 프로그램에 액세스할 수 없도록 GSuite의 사용자 비활성화를 지원 하지만 GSuite의 사용자는 삭제 하지 않습니다.

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).

<!--Image references-->

[10]: ./media/google-apps-provisioning-tutorial/gapps-security.png
[15]: ./media/google-apps-provisioning-tutorial/gapps-api.png
[16]: ./media/google-apps-provisioning-tutorial/gapps-api-enabled.png
[20]: ./media/google-apps-provisioning-tutorial/gapps-domains.png
[21]: ./media/google-apps-provisioning-tutorial/gapps-add-domain.png
[22]: ./media/google-apps-provisioning-tutorial/gapps-add-another.png
[24]: ./media/google-apps-provisioning-tutorial/gapps-provisioning.png
[25]: ./media/google-apps-provisioning-tutorial/gapps-provisioning-auth.png
[26]: ./media/google-apps-provisioning-tutorial/gapps-admin.png
[27]: ./media/google-apps-provisioning-tutorial/gapps-admin-privileges.png
[28]: ./media/google-apps-provisioning-tutorial/gapps-auth.png
