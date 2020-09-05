---
title: '자습서: Azure Active Directory을 사용 하 여 자동 사용자 프로 비전을 위한 Wrike 구성 | Microsoft Docs'
description: 사용자 계정을 Wrike로 자동으로 프로 비전 및 프로 비전 해제 하도록 Azure Active Directory를 구성 하는 방법에 대해 알아봅니다.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 08/26/2019
ms.author: Zhchia
ms.openlocfilehash: e3ec25504decca0747c8a853bb88ab3e7a10966a
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546324"
---
# <a name="tutorial-configure-wrike-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로 비전을 위한 Wrike 구성

이 자습서에서는 사용자 또는 그룹을 Wrike로 자동으로 프로 비전 및 프로 비전 해제 하도록 azure AD를 구성 하기 위해 Wrike and Azure Active Directory (Azure AD)에서 수행 하는 단계를 보여 줍니다.

> [!NOTE]
> 이 자습서에서는 Azure AD 사용자 프로 비전 서비스 위에 구축 된 커넥터에 대해 설명 합니다. 이 서비스에서 수행 하는 작업, 작동 방법 및 질문과 대답에 대 한 자세한 내용은 [Azure Active Directory를 사용 하 여 SaaS (software as a service) 응용 프로그램에 사용자 프로비저닝 자동화 및 프로 비전](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)해제를 참조 하세요.
>
> 이 커넥터는 현재 공개 미리 보기로 있습니다. 미리 보기 기능에 대 한 일반 Microsoft Azure 사용 약관에 대 한 자세한 내용은 [Microsoft Azure 미리 보기에 대 한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* Azure AD 테넌트
* [Wrike 테 넌 트](https://www.wrike.com/price/)
* 관리자 권한이 있는 Wrike의 사용자 계정

## <a name="assign-users-to-wrike"></a>Wrike에 사용자 할당
Azure Active Directory는 *할당*이라는 개념을 사용하여 어떤 사용자가 선택된 앱에 대한 액세스 권한을 부여받아야 하는지 판단합니다. 자동 사용자 프로비저닝의 컨텍스트에서 Azure AD의 애플리케이션에 할당된 사용자 또는 그룹만 동기화됩니다.

자동 사용자 프로비저닝을 구성 하 고 사용 하도록 설정 하기 전에 Wrike에 액세스 해야 하는 Azure AD의 사용자 또는 그룹을 결정 합니다. 그런 다음 여기에 설명 된 지침에 따라 이러한 사용자 또는 그룹을 Wrike에 할당 합니다.

* [엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-wrike"></a>Wrike에 사용자를 할당 하기 위한 주요 팁

* 자동 사용자 프로 비전 구성을 테스트 하려면 단일 Azure AD 사용자를 Wrike에 할당 하는 것이 좋습니다. 추가 사용자 또는 그룹은 나중에 할당할 수 있습니다.

* 사용자를 Wrike에 할당할 때 할당 대화 상자에서 유효한 응용 프로그램별 역할 (사용 가능한 경우)을 선택 해야 합니다. 기본 액세스 역할이 있는 사용자는 프로비전에서 제외됩니다.

## <a name="set-up-wrike-for-provisioning"></a>프로 비전을 위한 Wrike 설정

Azure AD를 사용 하 여 자동 사용자 프로 비전을 위해 Wrike를 구성 하기 전에 Wrike에서 SCIM (도메인 간 Id 관리) 프로 비전을 위해 시스템을 사용 하도록 설정 해야 합니다.

1. [Wrike 관리 콘솔](https://www.Wrike.com/login/)에 로그인 합니다. 테 넌 트 ID로 이동 합니다. **앱 & 통합**을 선택 합니다.

    ![앱 & 통합](media/Wrike-provisioning-tutorial/admin.png)

2.  **AZURE AD** 로 이동 하 여 선택 합니다.

    ![Azure AD](media/Wrike-provisioning-tutorial/Capture01.png)

3.  SCIM을 선택 합니다. **기본 URL**을 복사 합니다.

    ![기준 URL](media/Wrike-provisioning-tutorial/Wrike-tenanturl.png)

4. **API**  >  **Azure scim**을 선택 합니다.

    ![Azure SCIM](media/Wrike-provisioning-tutorial/Wrike-add-scim.png)

5.  팝업이 열립니다. 계정을 만들기 위해 이전에 만든 것과 동일한 암호를 입력 합니다.

    ![Wrike 토큰 만들기](media/Wrike-provisioning-tutorial/password.png)

6.  **비밀 토큰**을 복사 하 여 Azure AD에 붙여넣습니다. **저장** 을 선택 하 여 Wrike에서 프로 비전 설정을 마칩니다.

    ![영구 액세스 토큰](media/Wrike-provisioning-tutorial/Wrike-create-token.png)


## <a name="add-wrike-from-the-gallery"></a>갤러리에서 Wrike 추가

Azure AD를 사용 하 여 자동 사용자 프로 비전을 위해 Wrike를 구성 하기 전에 Azure AD 응용 프로그램 갤러리의 Wrike를 관리 되는 SaaS 응용 프로그램 목록에 추가 합니다.

Azure AD 응용 프로그램 갤러리에서 Wrike를 추가 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 탐색 창에서 **Azure Active Directory**를 선택 합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 응용 프로그램을 추가 하려면 창의 위쪽에 있는 **새 응용 프로그램** 단추를 선택 합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **Wrike**를 입력 하 고, 결과 패널에서 **Wrike** 를 선택한 다음, **추가** 를 선택 하 여 응용 프로그램을 추가 합니다.

    ![결과 목록의 Wrike](common/search-new-app.png)


## <a name="configure-automatic-user-provisioning-to-wrike"></a>Wrike에 자동 사용자 프로 비전 구성 

이 섹션에서는 azure ad의 사용자 또는 그룹 할당을 기반으로 Wrike에서 사용자 또는 그룹을 만들고, 업데이트 하 고, 비활성화 하도록 Azure AD 프로 비전 서비스를 구성 하는 단계를 안내 합니다.

> [!TIP]
> Wrike에 대 한 SAML 기반 Single Sign-On를 사용 하도록 설정 하려면 [Wrike Single Sign-On 자습서](wrike-tutorial.md)의 지침을 따르세요. Single sign-on은 자동 사용자 프로 비전과 독립적으로 구성할 수 있습니다. 하지만이 두 가지 기능은 서로 보완적입니다.

### <a name="configure-automatic-user-provisioning-for-wrike-in-azure-ad"></a>Azure AD에서 Wrike에 대 한 자동 사용자 프로 비전 구성

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 응용 프로그램**  >  **모든 응용 프로그램**을 선택 합니다.

    ![모든 애플리케이션](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Wrike**를 선택합니다.

    ![애플리케이션 목록의 Wrike 링크](common/all-applications.png)

3. **프로비전** 탭을 선택합니다.

    ![프로비저닝 탭](common/provisioning.png)

4. **프로비전 모드**를 **자동**으로 설정합니다.

    ![프로 비전 모드를 자동으로 설정](common/provisioning-automatic.png)

5. 관리자 자격 증명 섹션 아래에서 **테 넌 트 url** 및 **암호 토큰**에서 각각 검색 된 **기본 URL** 및 **영구 액세스 토큰** 값을 입력 합니다. **연결 테스트** 를 선택 하 여 Azure AD가 Wrike에 연결할 수 있는지 확인 합니다. 연결에 실패 하면 Wrike 계정에 관리자 권한이 있는지 확인 하 고 다시 시도 합니다.

    ![테 넌 트 URL + 토큰](common/provisioning-testconnection-tenanturltoken.png)

7. **알림 전자 메일** 상자에 프로 비전 오류 알림을 받을 개인 또는 그룹의 전자 메일 주소를 입력 합니다. **오류가 발생할 경우 이메일 알림 보내기** 확인란을 선택합니다.

    ![전자 메일 알림](common/provisioning-notification-email.png)

8. **저장**을 선택합니다.

9. **매핑** 섹션 아래에서 **Wrike에 사용자 Azure Active Directory 동기화를**선택 합니다.

    ![Wrike 사용자 매핑](media/Wrike-provisioning-tutorial/Wrike-user-mappings.png)

10. **특성 매핑** 섹션에서 Azure AD에서 Wrike로 동기화 되는 사용자 특성을 검토 합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 Wrike의 사용자 계정을 일치 시키는 데 사용 됩니다. 변경 내용을 커밋하려면 **저장**을 선택합니다.

    ![Wrike 사용자 특성](media/Wrike-provisioning-tutorial/Wrike-user-attributes.png)

11. 범위 지정 필터를 구성 하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)의 지침을 따르세요.

12. Wrike에 대 한 Azure AD 프로 비전 서비스를 사용 하도록 **설정 하려면 설정** 섹션에서 **프로 비전 상태** 를 **켜기** 로 변경 합니다.

    ![프로 비전 상태 전환 설정](common/provisioning-toggle-on.png)

13. **설정** 섹션의 **범위** 에서 원하는 값을 선택 하 여 Wrike에 프로 비전 할 사용자 또는 그룹을 정의 합니다.

    ![프로비전 범위](common/provisioning-scope.png)

14. 프로비저닝할 준비가 되면 **저장**을 선택합니다.

    ![프로 비전 구성 저장 중](common/provisioning-configuration-save.png)

이 작업은 **설정**의 **범위** 섹션에 정의된 모든 사용자 또는 그룹의 초기 동기화를 시작합니다. 초기 동기화는 후속 동기화 보다 수행 하는 데 더 많은 시간이 걸립니다. 사용자 또는 그룹이 프로 비전 하는 데 걸리는 시간에 대 한 자세한 내용은 [사용자를 프로 비전 하](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)는 데 얼마나 걸립니까?를 참조 하세요.

**현재 상태** 섹션을 사용 하 여 진행률을 모니터링 하 고 프로 비전 활동 보고서에 대 한 링크를 따라 Wrike의 Azure AD 프로 비전 서비스에서 수행 하는 모든 작업을 설명 합니다. 자세한 내용은 [사용자 프로비저닝 상태 확인](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)을 참조하세요. Azure AD 프로 비전 로그를 읽으려면 [자동 사용자 계정 프로 비전에 대 한 보고](../app-provisioning/check-status-user-account-provisioning.md)를 참조 하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).
