---
title: '자습서: Azure Active Directory을 사용 하 여 자동 사용자 프로 비전을 위한 Contentful 구성 합니다.'
description: Azure Active Directory (Azure AD)에서 콘텐츠를 자동으로 프로 비전 및 프로 비전 해제 하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: zchia
manager: beatrizd
ms.assetid: 3b761984-a9a0-4519-b23e-563438978de5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2020
ms.author: zhchia
ms.openlocfilehash: c9d19624d90b1228b2a44caeff7d103af3172ed9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97516348"
---
# <a name="tutorial-configure-contentful-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로 비전을 위한 Contentful 구성 합니다.

이 문서에서는 자동 사용자 프로 비전을 구성 하기 위해 Azure AD (Azure Active Directory)에서 완료 해야 하는 단계를 설명 합니다. 구성 된 경우 Azure AD는 Azure AD 프로 비전 서비스를 사용 하 여 사용자와 그룹을 자동으로 프로 [비전 하 고](https://www.contentful.com/) 프로 비전 합니다. 이 서비스의 기능 및 작동 방법에 대한 중요한 내용과 FAQ는 [Azure Active Directory를 사용하여 SaaS 애플리케이션으로 사용자 프로비저닝 및 프로비저닝 해제 자동화](../app-provisioning/user-provisioning.md)를 참조하세요. 

## <a name="capabilities-supported"></a>지원되는 기능

> [!div class="checklist"]
> * 콘텐츠를 통해 사용자 만들기
> * 더 이상 액세스 하지 않아도 되는 콘텐츠를 사용 하 여 사용자 제거
> * Azure AD와 Contentful 간에 사용자 특성을 동기화 된 상태로 유지
> * 그룹 및 그룹 멤버 자격을 프로 비전 합니다.
> * Contentful에 대 [한 Single sign-on](contentful-tutorial.md) (권장)

## <a name="prerequisites"></a>필수 구성 요소

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* [Azure AD 테넌트](../develop/quickstart-create-new-tenant.md) 
* 프로 비전을 구성할 수 있는 [권한이](../roles/permissions-reference.md) 있는 Azure AD의 사용자 계정 (예: 응용 프로그램 관리자, 클라우드 응용 프로그램 관리자, 응용 프로그램 소유자 또는 전역 관리자). 
* SCIM (도메인 간 Id 관리) 프로 비전을 위해 시스템을 지 원하는 구독이 있는 Contentful 조직 계정입니다. 조직의 구독에 대 한 문의 사항이 있으면 [Contentful](mailto:support@contentful.com)에 문의 하세요.
 
## <a name="plan-your-provisioning-deployment"></a>프로비저닝 배포 계획

1. [프로비저닝 서비스의 작동 방식](../app-provisioning/user-provisioning.md)에 대해 알아봅니다.
2. [프로비저닝 범위](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에 있는 사용자를 결정합니다.
3. [AZURE AD와 contentful 간에 매핑할](../app-provisioning/customize-application-attributes.md)데이터를 결정 합니다. 

## <a name="configure-contentful-to-support-provisioning-with-azure-ad"></a>Azure AD로 프로 비전을 지원 하도록 Contentful 구성 합니다.

1. **서비스 사용자** 계정을 만듭니다. Azure에 대 한 모든 프로 비전 권한은이 계정을 통해 제공 됩니다. 이 계정에 대 한 조직 역할로 **소유자** 를 선택 하는 것이 좋습니다.

2. **서비스 사용자** 로 콘텐츠를 로그인 합니다.

3. 왼쪽 메뉴에서 **조직 설정**  >  **액세스 도구**  >  **사용자 프로 비전** 을 선택 합니다.

   ![액세스 도구 아래에 사용자 프로비저닝이 강조 표시 된 콘텐츠를 사용 하는 조직 설정 메뉴의 스크린샷](media/contentful-provisioning-tutorial/access.png)

4. **Scim URL** 을 복사 하 고 저장 합니다. 이 값은 Contentful 응용 프로그램의 **프로 비전** 탭의 Azure Portal에 입력 합니다.

5. **개인용 액세스 토큰 생성** 을 선택 합니다.

    ![url](media/contentful-provisioning-tutorial/generate.png)

6. 모달 창에서 개인용 액세스 토큰의 이름을 입력 한 다음 **생성** 을 선택 합니다.

7. SCIM URL 및 암호 토큰이 생성 됩니다. 이러한 값을 복사 하 여 저장 합니다. Azure Portal에서 Contentful 응용 프로그램의 **프로 비전** 탭에 이러한 값을 입력 합니다.

    ![C F P A T와 토큰 자리 표시자 이름이 강조 표시 된 개인용 액세스 토큰 창의 스크린샷](media/contentful-provisioning-tutorial/token.png)


콘텐츠 관리 콘솔에서 프로 비전을 구성 하는 동안 질문이 있으면 [Contentful](mailto:support@contentful.com)에 문의 하세요.

## <a name="add-contentful-from-the-azure-ad-application-gallery"></a>Azure AD 응용 프로그램 갤러리에서 Contentful 추가

콘텐츠를 대상으로 프로 비전을 관리 하려면 Azure AD 응용 프로그램 갤러리에서 콘텐츠를 추가 합니다. 이전에 Single Sign-On에 대 한 콘텐츠를 설정한 경우 동일한 응용 프로그램을 사용할 수 있습니다. 그러나 처음에 통합을 테스트 하려면 별도의 앱을 만드는 것이 좋습니다. [갤러리에서 응용 프로그램을 추가](../manage-apps/add-application-portal.md)하는 방법에 대해 알아봅니다. 

## <a name="define-who-will-be-in-scope-for-provisioning"></a>프로비저닝 범위에 있는 사용자 정의 

Azure AD 프로 비전 서비스를 사용 하 여 응용 프로그램에 대 한 할당 또는 사용자 또는 그룹의 특성을 기반으로 프로 비전 할 사용자의 범위를 지정할 수 있습니다. 

할당을 기준으로 앱에 프로 비전 되는 사용자의 범위를 선택 하는 경우 [응용 프로그램에 사용자 및 그룹을 할당](../manage-apps/assign-user-or-group-access-portal.md)하는 단계를 완료 합니다.

사용자 또는 그룹의 특성만을 기준으로 프로 비전 할 사용자의 범위를 선택 하는 경우 범위 지정 필터를 사용 하 여 [사용자 계정 프로 비전에 대 한 조건부 규칙을 정의](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)합니다. 

* 사용자 및 그룹을 Contentful에 할당 하는 경우 **기본 액세스** 외의 다른 역할을 선택 해야 합니다. 기본 액세스 역할이 있는 사용자는 프로 비전에서 제외 되 고 프로 비전 로그에 표시 되며 실제로는 부여 되지 않습니다. 응용 프로그램에서 사용할 수 있는 유일한 역할이 기본 액세스 역할인 경우에는 [응용 프로그램 매니페스트를 업데이트](../develop/howto-add-app-roles-in-azure-ad-apps.md) 하 여 역할을 더 추가할 수 있습니다. 
* 소규모로 시작합니다. 모든 사용자에게 롤아웃하려면 먼저 소수의 사용자 및 그룹 세트를 사용하여 테스트합니다. 프로 비전 범위가 할당 된 사용자 및 그룹으로 설정 된 경우 앱에 하나 또는 두 개의 사용자 또는 그룹을 할당 하 여 범위를 제어할 수 있습니다. 범위가 모든 사용자 및 그룹으로 설정 된 경우 [특성 기반 범위 지정 필터](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)를 지정할 수 있습니다. 

## <a name="configure-automatic-user-provisioning-to-contentful"></a>콘텐츠를 사용 하 여 자동 사용자 프로 비전 구성 

이 섹션에서는 azure ad의 사용자 또는 그룹 할당을 기반으로 테스트 앱에서 사용자 및 그룹을 만들고, 업데이트 하 고, 비활성화 하도록 Azure AD 프로 비전 서비스를 설정 하는 단계를 안내 합니다.

### <a name="configure-automatic-user-provisioning-for-contentful-in-azure-ad"></a>Azure AD에서 콘텐츠를 위한 자동 사용자 프로 비전 구성

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 응용 프로그램** 을 선택 하 고 **모든 응용 프로그램** 을 선택 합니다.

   ![모든 응용 프로그램이 강조 표시 된 상태로 Azure Portal의 엔터프라이즈 응용 프로그램 메뉴를 보여 주는 스크린샷](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Contentful** 을 선택합니다.

   ![응용 프로그램 목록에 반환 된 처음 20 개의 결과를 보여 주는 스크린샷](common/all-applications.png)

3. **프로비전** 탭을 선택합니다.

   ![왼쪽 메뉴의 관리 섹션에 강조 표시 된 프로 비전 탭의 스크린샷](common/provisioning.png)

4. **프로비전 모드** 를 **자동** 으로 설정합니다.

   ![자동 강조 표시 된 프로 비전 모드 옵션을 보여 주는 스크린샷](common/provisioning-automatic.png)

5. **관리자 자격 증명** 섹션에서 contentful 테 넌 트 URL 및 비밀 토큰을 입력 합니다. Azure AD가 Contentful에 연결할 수 있는지 확인 하려면 **연결 테스트** 를 선택 합니다. 연결에 실패 하면 Contentful 계정에 관리자 권한이 있는지를 다시 시도 하십시오.

   ![연결 테스트 단추가 강조 표시 된 테 넌 트 U R L 및 비밀 토큰 텍스트 상자를 보여 주는 스크린샷](common/provisioning-testconnection-tenanturltoken.png)

6. **알림 전자 메일** 에서 프로 비전 오류 알림을 받을 개인 또는 그룹의 전자 메일 주소를 입력 한 다음 **오류가 발생 하면 전자 메일 알림 보내기** 확인란을 선택 합니다.

   ![알림 전자 메일 텍스트 상자를 표시 하는 스크린샷](common/provisioning-notification-email.png)

7. **저장** 을 선택합니다.

8. **매핑** 섹션에서 **Azure Active Directory 사용자에 게 콘텐츠 동기화를** 선택 합니다.

9. **특성 매핑** 섹션에서 Azure AD에서 contentful으로 동기화 되는 사용자 특성을 검토 합니다. **일치** 속성으로 선택한 특성은 업데이트 작업의 사용자 계정을 일치 시키는 데 사용 됩니다. 일치 하는 [대상 특성](../app-provisioning/customize-application-attributes.md)을 변경 하도록 선택 하는 경우 콘텐츠를 사용 하는 API가 해당 특성에 따라 사용자 필터링을 지원 하는지 확인 해야 합니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

   |attribute|Type|필터링에 지원됨|
   |---|---|---|
   |userName|String|&check;|
   |name.givenName|String|
   |name.familyName|String|

10. **매핑** 섹션에서 **Azure Active Directory 그룹을 Contentful로 동기화를** 선택 합니다.

11. **특성 매핑** 섹션에서 Azure AD에서 contentful 그룹 특성을 검토 합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 contentful 그룹을 일치 시키는 데 사용 됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

    |attribute|Type|필터링에 지원됨|
    |---|---|---|
    |displayName|String|&check;|
    |members|참조|

12. 범위 지정 필터를 설정 하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에 설명 된 단계를 완료 합니다.

13. 콘텐츠를 위해 Azure AD 프로 비전 서비스를 사용 하도록 설정 하려면 **설정** 섹션에서 **프로 비전 상태** 에 대해 **켜기** 를 선택 합니다.

    ![프로 비전 상태를 설정 및 해제 하는 것을 보여 주는 스크린샷](common/provisioning-toggle-on.png)

14. 콘텐츠를 프로 비전 하려는 사용자 또는 그룹을 정의 하려면 **설정** 섹션의 **범위** 에서 관련 옵션을 선택 합니다.

    ![범위 창에서 선택할 수 있는 옵션을 보여 주는 스크린샷](common/provisioning-scope.png)

15. 프로비저닝할 준비가 되면 **저장** 을 선택합니다.

    ![저장 단추와 취소 단추를 보여 주는 스크린샷](common/provisioning-configuration-save.png)

이 작업은 **설정** 의 **범위** 에 정의 된 모든 사용자 및 그룹의 초기 동기화 주기를 시작 합니다. 초기 주기는 Azure AD 프로비저닝 서비스가 실행되는 동안 약 40분마다 발생하는 후속 주기보다 더 많은 시간이 걸립니다. 

## <a name="monitor-your-deployment"></a>배포 모니터링

프로 비전을 구성한 후 다음 리소스를 사용 하 여 배포를 모니터링할 수 있습니다.

* 성공적으로 프로 비전 된 사용자를 확인 하려면 [프로 비전 로그](../reports-monitoring/concept-provisioning-logs.md)를 확인 합니다.
* 프로 비전 주기 상태와 종료 방법의 상태를 확인 하려면 [진행률 표시줄](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)을 선택 합니다.
* 프로 비전 구성이 비정상 상태에 있는 것 같으면 응용 프로그램이 격리 됩니다. [격리 상태](../app-provisioning/application-provisioning-quarantine-status.md)에 대해 자세히 알아보세요.  

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).
* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)
