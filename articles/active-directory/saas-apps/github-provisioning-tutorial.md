---
title: '자습서: GitHub에 대 한 사용자 프로 비전-Azure AD'
description: 사용자 계정을 GitHub에 자동으로 프로비전하고 프로비전 해제하도록 Azure Active Directory를 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.openlocfilehash: 7408d7609cbceb4ac39298680b6d3854a2d71306
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589217"
---
# <a name="tutorial-configure-github-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비전을 위한 GitHub 구성

이 자습서의 목적은 사용자 계정을 Azure AD에서 GitHub로 자동으로 프로비전하고 프로비전 해제하기 위해 GitHub 및 Azure AD에서 수행해야 하는 단계를 보여주는 것입니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

* Azure Active Directory 테넌트
* [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise)에서 만든 GitHub 조직. 여기에는 [GitHub Enterprise 요금제](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)가 필요합니다.
* 조직에 대 한 관리자 권한이 있는 GitHub의 사용자 계정
* [여기](https://help.github.com/en/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization) 에 설명 된 대로 조직에 대 한 OAuth 액세스가 제공 되었는지 확인 합니다.
* 단일 조직에 대 한 SCIM 프로 비전은 조직 수준에서 SSO를 사용 하도록 설정한 경우에만 지원 됩니다.

> [!NOTE]
> Azure AD 프로 비전 통합은 github 엔터프라이즈 [청구 계획](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)의 Github [enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise) 고객이 사용할 수 있는 [github scim API](https://developer.github.com/v3/scim/)를 사용 합니다.

## <a name="assigning-users-to-github"></a>GitHub에 사용자 할당

Azure Active Directory는 "할당"이라는 개념을 사용하여 어떤 사용자가 선택한 앱에 대한 액세스를 받아야 하는지를 판단합니다. 자동 사용자 계정 프로비전의 컨텍스트에서는 Azure AD의 애플리케이션에 “할당된” 사용자 및 그룹만 동기화됩니다. 

프로비전 서비스를 구성하고 사용하도록 설정하기 전에 Azure AD에서 GitHub 앱에 액세스해야 하는 사용자를 나타내는 사용자 및/또는 그룹을 결정해야 합니다. 일단 결정되면 다음 지침에 따라 해당 사용자를 GitHub 앱에 할당할 수 있습니다.

[엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-github"></a>GitHub에 사용자를 할당하기 위한 주요 팁

* 프로비전 구성을 테스트하기 위해 단일 Azure AD 사용자를 GitHub에 할당하는 것이 좋습니다. 추가 사용자 및/또는 그룹은 나중에 할당할 수도 있습니다.

* GitHub에 사용자를 할당할 때 할당 대화 상자에서 **사용자** 역할이나 다른 유효한 애플리케이션 관련 역할(사용 가능한 경우)을 선택해야 합니다. **기본 액세스** 역할은 프로비전에 적합하지 않으므로 이러한 사용자는 건너뜁니다.

## <a name="configuring-user-provisioning-to-github"></a>GitHub에 사용자 프로비전 구성

이 섹션에서는 Azure AD를 GitHub의 사용자 계정 프로비전 API에 연결하고 Azure AD의 사용자 및 그룹 할당을 기반으로 GitHub에서 할당된 사용자 계정을 만들고, 업데이트하고 비활성화하도록 프로비전 서비스를 구성하는 과정을 안내합니다.

> [!TIP]
> [Azure Portal](https://portal.azure.com)에 제공된 지침에 따라 GitHub에 대해 SAML 기반 Single Sign-On을 사용하도록 선택할 수도 있습니다. Single Sign-On은 자동 프로비전과 별개로 구성할 수 있습니다. 하지만 이 두 가지 기능은 서로 보완적입니다.

### <a name="configure-automatic-user-account-provisioning-to-github-in-azure-ad"></a>Azure AD에서 GitHub에 자동 사용자 계정 프로비전 구성

1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory &gt; 엔터프라이즈 앱 &gt; 모든 애플리케이션** 섹션으로 이동합니다.

2. GitHub에 Single Sign-On을 이미 구성한 경우 검색 필드를 사용하여 GitHub 인스턴스를 검색합니다. 그렇지 않은 경우 **추가**를 선택하고 애플리케이션 갤러리에서 **GitHub**를 검색합니다. 검색 결과에서 GitHub를 선택하여 애플리케이션 목록에 추가합니다.

3. GitHub 인스턴스를 선택한 다음 **프로비전** 탭을 선택합니다.

4. **프로비전 모드**를 **자동**으로 설정합니다.

    ![GitHub 프로비전](./media/github-provisioning-tutorial/GitHub1.png)

5. **관리자 자격 증명** 섹션에서 **권한 부여**를 클릭합니다. 이 작업은 새 브라우저 창에 GitHub 권한 부여 대화 상자를 엽니다. 액세스 권한을 부여 하도록 승인 되었는지 확인 해야 합니다. [여기](https://help.github.com/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization)에 설명 된 지침을 따르세요.

6. 새 창에서 관리자 계정을 사용하여 GitHub에 로그인합니다. 표시된 권한 부여 대화 상자에서 프로비전을 사용하도록 설정하려는 GitHub 팀을 선택한 다음 **권한 부여**를 선택합니다. 완료되면 Azure Portal로 돌아가서 프로비전 구성을 완료합니다.

    ![권한 부여 대화 상자](./media/github-provisioning-tutorial/GitHub2.png)

7. Azure Portal에서 **테넌트 URL**을 입력하고 **연결 테스트**를 클릭하여 Azure AD가 GitHub 앱에 연결할 수 있는지 확인합니다. 연결이 실패하면 GitHub 계정에 관리자 권한이 있는지 및 **테넌트 URl**가 제대로 입력되었는지 확인한 다음, "권한 부여" 단계를 다시 시도합니다. (**테넌트 URL**은 `https://api.github.com/scim/v2/organizations/<Organization_name>` 규칙에 따라 구성할 수 있습니다. 조직은 GitHub 계정으로 **설정** > **조직**에서 찾을 수 있습니다.)

    ![권한 부여 대화 상자](./media/github-provisioning-tutorial/GitHub3.png)

8. 프로비전 오류 알림을 받을 개인 또는 그룹의 메일 주소를 **알림 메일** 필드에 입력하고 “오류가 발생할 경우 메일 알림 보내기” 확인란을 선택합니다.

9. **저장**을 클릭합니다.

10. 매핑 섹션에서 **Synchronize Azure Active Directory Users to GitHub**(Azure Active Directory 사용자를 GitHub에 동기화)를 선택합니다.

11. **특성 매핑** 섹션에서 Azure AD에서 GitHub로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 GitHub의 사용자 계정을 일치시키는 데 사용됩니다. 저장 단추를 선택하여 변경 내용을 커밋합니다.

12. GitHub에 대한 Azure AD 프로비전 서비스를 사용하도록 설정하려면 **설정** 섹션에서 **프로비전 상태**를 **켜기**로 변경합니다.

13. **저장**을 클릭합니다.

사용자 및 그룹 섹션의 GitHub에 할당된 모든 사용자 및/또는 그룹의 초기 동기화가 시작됩니다. 초기 동기화는 서비스가 실행되는 동안 약 40분마다 발생하는 후속 동기화보다 더 많은 시간이 걸립니다. **동기화 세부 정보** 섹션을 사용하여 진행 상태를 모니터링하고 프로비저닝 서비스에서 수행하는 모든 작업을 설명하는 프로비저닝 활동 로그에 연결된 링크를 따를 수 있습니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../app-provisioning/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).
