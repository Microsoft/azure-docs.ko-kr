---
title: '자습서: Azure Active Directory로 자동 사용자 프로비전을 위한 G Suite 구성 | Microsoft Docs'
description: 사용자 계정을 Azure AD에서 G Suite로 자동으로 프로비전 및 프로비전 해제하는 방법을 알아봅니다.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/06/2020
ms.author: Zhchia
ms.openlocfilehash: 27e34a66bb6dfa642b84bd8997b2b02c4981788e
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551557"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비전을 위한 G Suite 구성

이 자습서에서는 자동 사용자 프로 비전을 구성 하기 위해 G Suite와 Azure Active Directory (Azure AD)에서 수행 해야 하는 단계를 설명 합니다. 구성 된 경우 azure AD는 Azure AD 프로 비전 서비스를 사용 하 여 사용자 및 그룹을 [G Suite](https://gsuite.google.com/) 에 자동으로 프로 비전 하 고 프로 비전 해제 합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../manage-apps/user-provisioning.md)를 참조하세요. 

> [!NOTE]
> 이 자습서에서는 Azure AD 사용자 프로비저닝 서비스에 기반하여 구축된 커넥터에 대해 설명합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)를 참조하세요.

> [!NOTE]
> G Suite 커넥터는 최근 2019 년 10 월에 업데이트 되었습니다. G Suite 커넥터에 대 한 변경 내용은 다음과 같습니다.
>
> * 추가 G Suite 사용자 및 그룹 특성에 대 한 지원이 추가 되었습니다.
> * G Suite 대상 특성 이름이 [여기](https://developers.google.com/admin-sdk/directory)에 정의 된 것과 일치 하도록 업데이트 되었습니다.
> * 기본 특성 매핑을 업데이트 했습니다.

## <a name="capabilities-supported"></a>지원되는 기능
> [!div class="checklist"]
> * G Suite에서 사용자 만들기
> * 더 이상 액세스 하지 않아도 되는 G Suite의 사용자 제거
> * Azure AD와 G Suite 간에 사용자 특성을 동기화 상태로 유지
> * G Suite에서 그룹 및 그룹 멤버 자격 프로 비전
> * G Suite에 대 [한 Single sign-on](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-tutorial) (권장)

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* [Azure AD 테넌트](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* 프로비저닝을 구성할 [권한](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)이 있는 Azure AD의 사용자 계정(예: 애플리케이션 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 소유자 또는 전역 관리자). 
* [G Suite 테 넌 트](https://gsuite.google.com/pricing.html)
* 관리자 권한이 있는 G Suite의 사용자 계정

## <a name="step-1-plan-your-provisioning-deployment"></a>1단계. 프로비저닝 배포 계획
1. [프로비저닝 서비스의 작동 방식](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)에 대해 알아봅니다.
2. [프로비저닝 범위](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)에 있는 사용자를 결정합니다.
3. [AZURE AD와 G Suite 간에 매핑할](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)데이터를 결정 합니다. 

## <a name="step-2-configure-g-suite-to-support-provisioning-with-azure-ad"></a>2단계. Azure AD를 사용 하 여 프로 비전을 지원 하도록 G Suite 구성

Azure AD를 사용 하 여 자동 사용자 프로 비전을 위한 G Suite를 구성 하기 전에 G Suite에서 SCIM 프로 비전을 사용 하도록 설정 해야 합니다.

1. 관리자 계정으로 [G Suite 관리 콘솔](https://admin.google.com/) 에 로그인 한 다음 **보안**을 선택 합니다. 링크가 보이지 않으면 화면 아래쪽에 있는 **기타 컨트롤** 메뉴에 숨겨져 있을 수 있습니다.

    ![G Suite 보안](./media/google-apps-provisioning-tutorial/gapps-security.png)

2. **보안** 페이지에서 **API 참조**를 선택합니다.

    ![G Suite API](./media/google-apps-provisioning-tutorial/gapps-api.png)

3. **API 액세스 사용**을 선택합니다.

    ![G Suite API 사용](./media/google-apps-provisioning-tutorial/gapps-api-enabled.png)

    > [!IMPORTANT]
   > G Suite에 프로 비전 하려는 모든 사용자에 대해 Azure AD의 사용자 이름이 사용자 지정 도메인에 연결 되어야 **합니다** . 예를 들면 bob@contoso.onmicrosoft.com과 같은 사용자 이름은 G Suite에서 허용되지 않습니다. 반면에 bob@contoso.com은 허용됩니다. [여기](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)에 설명 된 지침에 따라 기존 사용자의 도메인을 변경할 수 있습니다.

4. Azure AD를 사용 하 여 원하는 사용자 지정 도메인을 추가 하 고 확인 한 후에는 G Suite를 사용 하 여 다시 확인 해야 합니다. G Suite의 도메인을 확인 하려면 다음 단계를 참조 하세요.

    a. [G Suite 관리 콘솔](https://admin.google.com/)에서 **도메인**을 선택 합니다.

    ![G Suite 도메인](./media/google-apps-provisioning-tutorial/gapps-domains.png)

    b. **Add a domain or a domain alias(도메인 또는 도메인 별칭 추가)** 를 선택합니다.

    ![G Suite 도메인 추가](./media/google-apps-provisioning-tutorial/gapps-add-domain.png)

    다. **다른 도메인 추가**를 선택하고 추가하려는 도메인 이름을 입력합니다.

    ![G Suite 다른 추가](./media/google-apps-provisioning-tutorial/gapps-add-another.png)

    d. **Continue and verify domain ownership(계속해서 도메인 소유권 확인)** 을 선택합니다. 그런 다음 도메인 이름을 소유하고 있는지 확인하는 단계를 따릅니다. Google을 사용 하 여 도메인을 확인 하는 방법에 대 한 포괄적인 지침은 [사이트 소유권 확인](https://support.google.com/webmasters/answer/35179)을 참조 하세요.

    e. G Suite에 추가 하려는 추가 도메인에 대해 위의 단계를 반복 합니다.

5. 다음으로 G Suite에서 사용자 프로 비전을 관리 하는 데 사용할 관리자 계정을 결정 합니다. **관리자 역할**로 이동 합니다.

    ![G Suite 관리자](./media/google-apps-provisioning-tutorial/gapps-admin.png)

6. 해당 계정의 **관리자 역할** 에 대해 해당 역할에 대 한 **권한을** 편집 합니다. 이 계정을 프로비전에 사용할 수 있도록 모든 **관리자 API 권한**이 사용하도록 설정되어 있는지 확인합니다.

    ![G Suite 관리자 권한](./media/google-apps-provisioning-tutorial/gapps-admin-privileges.png)

## <a name="step-3-add-g-suite-from-the-azure-ad-application-gallery"></a>3단계. Azure AD 응용 프로그램 갤러리에서 G Suite 추가

Azure AD 응용 프로그램 갤러리에서 G Suite를 추가 하 여 G Suite에 대 한 프로 비전 관리를 시작 합니다. 이전에 SSO 용 G Suite를 설정 했다면 동일한 응용 프로그램을 사용할 수 있습니다. 그러나 처음 통합을 테스트하는 경우 별도의 앱을 만드는 것이 좋습니다. [여기](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)를 클릭하여 갤러리에서 애플리케이션을 추가하는 방법에 대해 자세히 알아봅니다. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4단계. 프로비저닝 범위에 있는 사용자 정의 

Azure AD 프로비저닝 서비스를 사용하면 애플리케이션에 대한 할당 또는 사용자/그룹의 특성을 기반으로 프로비저닝되는 사용자의 범위를 지정할 수 있습니다. 할당을 기준으로 앱에 프로비저닝할 사용자의 범위를 선택하려면 다음 [단계](../manage-apps/assign-user-or-group-access-portal.md)를 사용하여 애플리케이션에 사용자 및 그룹을 할당할 수 있습니다. 사용자 또는 그룹의 특성만을 기준으로 프로비저닝할 사용자의 범위를 선택하려면 [여기](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 설명된 대로 범위 지정 필터를 사용할 수 있습니다. 

* G Suite에 사용자 및 그룹을 할당 하는 경우 **기본 액세스**외의 다른 역할을 선택 해야 합니다. 기본 액세스 역할이 있는 사용자는 프로비저닝에서 제외되고 프로비저닝 로그에 실질적으로 권한을 부여받지 않은 것으로 표시됩니다. 애플리케이션에서 사용할 수 있는 유일한 역할이 기본 액세스 역할인 경우에는 [애플리케이션 매니페스트를 업데이트](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)하여 역할을 더 추가할 수 있습니다. 

* 소규모로 시작합니다. 모든 사용자에게 배포하기 전에 소수의 사용자 및 그룹 집합으로 테스트합니다. 할당된 사용자 및 그룹으로 프로비저닝 범위가 설정된 경우 앱에 하나 또는 두 개의 사용자 또는 그룹을 할당하여 범위를 제어할 수 있습니다. 모든 사용자 및 그룹으로 범위가 설정된 경우 [특성 기반 범위 지정 필터](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)를 지정할 수 있습니다. 


## <a name="step-5-configure-automatic-user-provisioning-to-g-suite"></a>5단계. G Suite에 대 한 자동 사용자 프로 비전 구성 

이 섹션에서는 Azure AD의 사용자 및/또는 그룹 할당에 따라 TestApp에서 사용자 및/또는 그룹을 만들고, 업데이트하고, 사용 해제하도록 Azure AD 프로비저닝 서비스를 구성하는 단계를 안내합니다.

> [!NOTE]
> G Suite의 디렉터리 API 끝점에 대 한 자세한 내용은 [디렉터리 api](https://developers.google.com/admin-sdk/directory)를 참조 하세요.

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>Azure AD에서 G Suite에 대 한 자동 사용자 프로 비전을 구성 하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 애플리케이션**, **모든 애플리케이션**을 차례로 선택합니다. 사용자가 portal.azure.com에 로그인 해야 하며 aad.portal.azure.com를 사용할 수 없습니다.

    ![엔터프라이즈 애플리케이션 블레이드](./media/google-apps-provisioning-tutorial/enterprise-applications.png)

    ![모든 애플리케이션 블레이드](./media/google-apps-provisioning-tutorial/all-applications.png)

2. 애플리케이션 목록에서 **G Suite**를 선택합니다.

    ![애플리케이션 목록의 G Suite 링크](common/all-applications.png)

3. **프로비전** 탭을 선택합니다. **시작**을 클릭합니다.

    ![프로비저닝 탭](common/provisioning.png)

      ![시작 블레이드](./media/google-apps-provisioning-tutorial/get-started.png)

4. **프로비전 모드**를 **자동**으로 설정합니다.

    ![프로비저닝 탭](common/provisioning-automatic.png)

5. **관리자 자격 증명** 섹션 아래에서 **권한 부여**를 클릭 합니다. 새 브라우저 창에서 Google 권한 부여 대화 상자로 리디렉션됩니다.

      ![G Suite 권한 부여](./media/google-apps-provisioning-tutorial/authorize-1.png)

6. Azure AD 사용 권한을 부여 하 여 G Suite 테 넌 트를 변경할 것인지 확인 합니다. **수락**을 선택합니다.

     ![G Suite 테 넌 트 인증](./media/google-apps-provisioning-tutorial/gapps-auth.png)

7. Azure Portal에서 **연결 테스트** 를 클릭 하 여 Azure AD가 G Suite에 연결할 수 있는지 확인 합니다. 연결에 실패 하면 G Suite 계정에 관리자 권한이 있는지 확인 하 고 다시 시도 하세요. 그런 다음 **권한 부여** 단계를 다시 시도합니다.

6. **알림 이메일** 필드에 프로비저닝 오류 알림을 받을 개인 또는 그룹의 메일 주소를 입력하고, **오류가 발생할 경우 메일 알림 보내기** 확인란을 선택합니다.

    ![알림 이메일](common/provisioning-notification-email.png)

7. **저장**을 선택합니다.

8. **매핑** 섹션에서 **Azure Active Directory 사용자 프로비전**을 선택합니다.

9. **특성 매핑** 섹션에서 Azure AD에서 G Suite로 동기화 되는 사용자 특성을 검토 합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 G Suite의 사용자 계정을 일치 시키는 데 사용 됩니다. 일치 하는 [대상 특성](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)을 변경 하도록 선택 하는 경우 G Suite API가 해당 특성에 따라 사용자 필터링을 지원 하는지 확인 해야 합니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

   |attribute|Type|
   |---|---|
   |primaryEmail|String|
   |관계. [type eq "manager"]. 값|String|
   |name.familyName|String|
   |name.givenName|String|
   |일시 중단됨|String|
   |externalIds. [type eq "custom"]. value|String|
   |externalIds. [type eq "조직"]. 값|String|
   |주소로. [eq "work"]. country를 입력 합니다.|String|
   |주소로. [type eq "work"]. streetAddress|String|
   |주소로. [type eq "work"]. region|String|
   |주소로. [type eq "work"]. 집약성|String|
   |주소로. [type eq "work"]. postalCode|String|
   |전자. [type eq "work"]. address|String|
   |조직. [eq "work"] 학과를 입력 합니다.|String|
   |조직. [type eq "work"]. title|String|
   |phoneNumbers. [eq "work"] 값을 입력 합니다.|String|
   |phoneNumbers. [type eq "mobile"]. value|String|
   |phoneNumbers. [eq "work_fax"]. 값을 입력 합니다.|String|
   |전자. [type eq "work"]. address|String|
   |조직. [eq "work"] 학과를 입력 합니다.|String|
   |조직. [type eq "work"]. title|String|
   |phoneNumbers. [eq "work"] 값을 입력 합니다.|String|
   |phoneNumbers. [type eq "mobile"]. value|String|
   |phoneNumbers. [eq "work_fax"]. 값을 입력 합니다.|String|
   |주소로. [eq "home"]. country를 입력 합니다.|String|
   |주소로. [eq "home"] 형식의 서식 지정|String|
   |주소로. [type eq "home"]. 집약성|String|
   |주소로. [type eq "home"]. postalCode|String|
   |주소로. [eq "home"]. region을 입력 합니다.|String|
   |주소로. [eq "home"]. streetAddress을 입력 합니다.|String|
   |주소로. [type eq "other"]. country|String|
   |주소로. [type eq "other"]. 서식 지정|String|
   |주소로. [type eq "other"]. 집약성|String|
   |주소로. [type eq "other"]. postalCode|String|
   |주소로. [type eq "other"]. region|String|
   |주소로. [type eq "other"]. streetAddress|String|
   |주소로. [type eq "work"]. 서식 지정|String|
   |changePasswordAtNextLogin|String|
   |전자. [eq "home"]. address를 입력 합니다.|String|
   |전자. [type eq "other"]. address|String|
   |externalIds. [eq "account"]. 값을 입력 합니다.|String|
   |externalIds. [type eq "custom"]. customType|String|
   |externalIds. [type eq "customer"]. value|String|
   |externalIds. [eq "login_id"]. 값을 입력 합니다.|String|
   |externalIds. [type eq "network"]. value|String|
   |성별 형식|String|
   |GeneratedImmutableId|String|
   |ID|String|
   |받을. [type eq "home"]. protocol|String|
   |받을. [type eq "other"]. 프로토콜|String|
   |받을. [type eq "work"]. protocol|String|
   |includeInGlobalAddressList|String|
   |ipWhitelisted|String|
   |조직. [eq "school"]. costCenter을 입력 합니다.|String|
   |조직. [eq "school"] 학과를 입력 합니다.|String|
   |조직. [eq "school"]. domain을 입력 합니다.|String|
   |조직. [type eq "school"]. fullTimeEquivalent|String|
   |조직. [type eq "school"]. location|String|
   |조직. [eq "school"]. name을 입력 합니다.|String|
   |조직. [type eq "school"]. 기호|String|
   |조직. [eq "school"]. title을 입력 합니다.|String|
   |조직. [type eq "work"]. costCenter|String|
   |조직. [type eq "work"]. domain|String|
   |조직. [type eq "work"]. fullTimeEquivalent|String|
   |조직. [type eq "work"]. location|String|
   |조직. [eq "work"]. name을 입력 합니다.|String|
   |조직. [type eq "work"]. 기호|String|
   |OrgUnitPath|String|
   |phoneNumbers. [eq "home"] 값을 입력 합니다.|String|
   |phoneNumbers. [type eq "other"]. 값|String|
   |사이트. [eq "home"] 값을 입력 합니다.|String|
   |사이트. [type eq "other"]. 값|String|
   |사이트. [eq "work"] 값을 입력 합니다.|String|
   

10. **매핑** 섹션 아래에서 **프로 비전 Azure Active Directory 그룹**을 선택 합니다.

11. **특성 매핑** 섹션에서 Azure AD에서 G Suite로 동기화 되는 그룹 특성을 검토 합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 G Suite의 그룹을 일치 시키는 데 사용 됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

      |attribute|Type|
      |---|---|
      |이메일|String|
      |멤버|String|
      |name|String|
      |description|String|

12. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

13. G Suite에 대 한 Azure AD 프로 비전 서비스를 사용 하도록 **설정 하려면 설정** 섹션에서 **프로 비전 상태** 를 **켜기** 로 변경 합니다.

    ![프로비전 상태 켜기로 전환](common/provisioning-toggle-on.png)

14. **설정** 섹션의 **범위** 에서 원하는 값을 선택 하 여 G Suite에 프로 비전 하려는 사용자 및/또는 그룹을 정의 합니다.

    ![프로비전 범위](common/provisioning-scope.png)

15. 프로비전할 준비가 되면 **저장**을 클릭합니다.

    ![프로비전 구성 저장](common/provisioning-configuration-save.png)

이 작업은 **설정**의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화 주기를 시작합니다. 초기 주기는 Azure AD 프로비저닝 서비스가 실행되는 동안 약 40분마다 발생하는 후속 주기보다 더 많은 시간이 걸립니다.

> [!NOTE]
> 사용자에 게 Azure AD 사용자의 메일 주소를 사용 하는 기존 개인/소비자 계정이 이미 있는 경우 디렉터리 동기화를 수행 하기 전에 Google Transfer Tool을 사용 하 여 해결할 수 있는 몇 가지 문제가 발생할 수 있습니다.

## <a name="step-6-monitor-your-deployment"></a>6단계. 배포 모니터링
프로비저닝을 구성한 후에는 다음 리소스를 사용하여 배포를 모니터링합니다.

1. [프로비저닝 로그](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)를 사용하여 어떤 사용자가 성공적으로 프로비저닝되었는지 확인합니다.
2. [진행률 표시줄](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user)을 통해 프로비저닝 주기 상태와 완료 정도를 확인합니다.
3. 프로비저닝 구성이 비정상 상태로 보이면 애플리케이션이 격리됩니다. 격리 상태에 대한 자세한 내용은 [여기](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../manage-apps/check-status-user-account-provisioning.md).
