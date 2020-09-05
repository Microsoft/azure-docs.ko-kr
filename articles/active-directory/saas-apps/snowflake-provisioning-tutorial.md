---
title: '자습서: Azure Active Directory을 사용 하 여 자동 사용자 프로 비전을 위한 눈송이 구성 Microsoft Docs'
description: 사용자 계정을 눈송이에 자동으로 프로 비전 및 프로 비전 해제 하도록 Azure Active Directory를 구성 하는 방법에 대해 알아봅니다.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 7fbf7743eb1c037a364e1004fc3be30db6777bb0
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546645"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로 비전을 위한 눈송이 구성

이 자습서에서는 사용자 및/또는 그룹을 [눈송이](https://www.Snowflake.com/pricing/)로 자동으로 프로 비전 및 프로 비전 해제 하도록 azure ad를 구성 하기 위해 눈송이 및 Azure Active Directory (azure ad)에서 수행 하는 단계를 보여 줍니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../manage-apps/user-provisioning.md)를 참조하세요. 


> [!NOTE]
> 이 커넥터는 현재 공개 미리 보기로 있습니다. 미리 보기 기능의 Microsoft Azure 일반 사용 약관에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 조건](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="capabilities-supported"></a>지원되는 기능
> [!div class="checklist"]
> * 눈송이에서 사용자 만들기
> * 더 이상 액세스 하지 않아도 되는 사용자를 눈송이에서 제거
> * Azure AD와 눈송이 간에 사용자 특성을 동기화 상태로 유지
> * 눈송이에서 그룹 및 그룹 멤버 자격 프로 비전
> * 눈송이에 대 [한 Single sign-on](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial) (권장)

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* [AZURE AD 테 넌 트](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* 프로비저닝을 구성할 [권한](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)이 있는 Azure AD의 사용자 계정(예: 애플리케이션 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 소유자 또는 전역 관리자).
* [눈송이 테 넌 트](https://www.Snowflake.com/pricing/).
* 관리 권한이 있는 눈송이의 사용자 계정

## <a name="step-1-plan-your-provisioning-deployment"></a>1단계. 프로비저닝 배포 계획
1. [프로비저닝 서비스의 작동 방식](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)에 대해 알아봅니다.
2. [프로비저닝 범위](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)에 있는 사용자를 결정합니다.
3. [AZURE AD와 눈송이 간에 매핑할](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)데이터를 결정 합니다. 

## <a name="step-2-configure-snowflake-to-support-provisioning-with-azure-ad"></a>2단계. Azure AD로 프로 비전을 지원 하도록 눈송이 구성

Azure AD를 사용 하 여 자동 사용자 프로 비전을 위해 눈송이를 구성 하기 전에 눈송이에서 SCIM 프로 비전을 사용 하도록 설정 해야 합니다.

1. 눈송이 관리 콘솔에 로그인 합니다. 강조 표시 된 워크시트에서 아래에 표시 된 쿼리를 입력 하 고 **실행**을 클릭 합니다.

    ![눈송이 관리 콘솔](media/Snowflake-provisioning-tutorial/image00.png)

2.  눈송이 테 넌 트에 대해 SCIM 액세스 토큰이 생성 됩니다. 검색 하려면 아래에 강조 표시 된 링크를 클릭 합니다.

    ![눈송이 SCIM 추가](media/Snowflake-provisioning-tutorial/image01.png)

3. 생성 된 토큰 값을 복사 하 고 **완료**를 클릭 합니다. 이 값은 Azure Portal의 눈송이 응용 프로그램의 프로 비전 탭에 있는 **비밀 토큰** 필드에 입력 됩니다.

    ![눈송이 SCIM 추가](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="step-3-add-snowflake-from-the-azure-ad-application-gallery"></a>3단계. Azure AD 응용 프로그램 갤러리에서 눈송이 추가

Azure AD 응용 프로그램 갤러리에서 눈송이를 추가 하 여 눈송이로의 프로 비전 관리를 시작 합니다. 이전에 SSO에 대 한 눈송이를 설정 했다면 동일한 응용 프로그램을 사용할 수 있습니다. 그러나 처음 통합을 테스트하는 경우 별도의 앱을 만드는 것이 좋습니다. [여기](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)를 클릭하여 갤러리에서 애플리케이션을 추가하는 방법에 대해 자세히 알아봅니다. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4단계. 프로비저닝 범위에 있는 사용자 정의 

Azure AD 프로비저닝 서비스를 사용하면 애플리케이션에 대한 할당 또는 사용자/그룹의 특성을 기반으로 프로비저닝되는 사용자의 범위를 지정할 수 있습니다. 할당을 기준으로 앱에 프로비저닝할 사용자의 범위를 선택하려면 다음 [단계](../manage-apps/assign-user-or-group-access-portal.md)를 사용하여 애플리케이션에 사용자 및 그룹을 할당할 수 있습니다. 사용자 또는 그룹의 특성만을 기준으로 프로비저닝할 사용자의 범위를 선택하려면 [여기](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 설명된 대로 범위 지정 필터를 사용할 수 있습니다. 

* 사용자 및 그룹을 눈송이에 할당할 때 **기본 액세스**이외의 다른 역할을 선택 해야 합니다. 기본 액세스 역할이 있는 사용자는 프로비저닝에서 제외되고 프로비저닝 로그에 실질적으로 권한을 부여받지 않은 것으로 표시됩니다. 애플리케이션에서 사용할 수 있는 유일한 역할이 기본 액세스 역할인 경우에는 [애플리케이션 매니페스트를 업데이트](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)하여 역할을 더 추가할 수 있습니다. 

* 소규모로 시작합니다. 모든 사용자에게 배포하기 전에 소수의 사용자 및 그룹 집합으로 테스트합니다. 할당된 사용자 및 그룹으로 프로비저닝 범위가 설정된 경우 앱에 하나 또는 두 개의 사용자 또는 그룹을 할당하여 범위를 제어할 수 있습니다. 모든 사용자 및 그룹으로 범위가 설정된 경우 [특성 기반 범위 지정 필터](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)를 지정할 수 있습니다. 


## <a name="step-5-configure-automatic-user-provisioning-to-snowflake"></a>5단계. 눈송이에 자동 사용자 프로 비전 구성 

이 섹션에서는 azure ad의 사용자 및/또는 그룹 할당에 따라 눈송이에서 사용자 및/또는 그룹을 만들고, 업데이트 하 고, 사용 해제 하도록 Azure AD 프로 비전 서비스를 구성 하는 단계를 안내 합니다.

### <a name="to-configure-automatic-user-provisioning-for-snowflake-in-azure-ad"></a>Azure AD에서 눈송이에 대해 자동 사용자 프로 비전을 구성 하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 애플리케이션**, **모든 애플리케이션**을 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Snowflake**를 선택합니다.

    ![애플리케이션 목록의 Snowflake 링크](common/all-applications.png)

3. **프로비전** 탭을 선택합니다.

    ![프로비저닝 탭](common/provisioning.png)

4. **프로비전 모드**를 **자동**으로 설정합니다.

    ![프로비저닝 탭](common/provisioning-automatic.png)

5. 관리자 자격 증명 섹션 아래에서 먼저 **테 넌 트 url** 및 **비밀 토큰** 필드에서 검색 된 **Scim 2.0 기준 url 및 인증 토큰** 값을 입력 합니다. **연결 테스트** 를 클릭 하 여 Azure AD가 눈송이에 연결할 수 있는지 확인 합니다. 연결에 실패 하면 눈송이 계정에 관리자 권한이 있는지 확인 하 고 다시 시도 합니다.

    ![테넌트 URL + 토큰](common/provisioning-testconnection-tenanturltoken.png)

7. **알림 메일** 필드에 프로비저닝 오류 알림을 받을 개인 또는 그룹의 메일 주소를 입력하고, **오류가 발생할 경우, 메일 알림 보내기** 확인란을 선택합니다.

    ![알림 이메일](common/provisioning-notification-email.png)

8. **저장**을 클릭합니다.

9. **매핑** 섹션에서 **Azure Active Directory 사용자를 눈송이에 동기화를**선택 합니다.

10. **특성 매핑** 섹션에서 Azure AD에서 눈송이로 동기화 되는 사용자 특성을 검토 합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 눈송이의 사용자 계정을 일치 시키는 데 사용 됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

   |attribute|Type|
   |---|---|
   |활성|부울|
   |displayName|String|
   |emails[type eq "work"].value|String|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   |urn: ietf: params: scim: 스키마: 확장: enterprise: 2.0: User: defaultRole|String|
   |urn: ietf: params: scim: 스키마: 확장: enterprise: 2.0: 사용자: defaultWarehouse|String|

11. **매핑** 섹션에서 **눈송이에 Azure Active Directory 그룹 동기화를**선택 합니다.

12. **특성 매핑** 섹션에서 Azure AD에서 눈송이로 동기화 되는 그룹 특성을 검토 합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 눈송이의 그룹을 일치 시키는 데 사용 됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

      |attribute|Type|
      |---|---|
      |displayName|String|
      |members|참조|

13. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

14. 눈송이에 대해 Azure AD 프로 비전 서비스를 사용 하도록 **설정 하려면 설정** 섹션에서 **프로 비전 상태** 를 **켜기** 로 변경 합니다.

    ![프로비전 상태 켜기로 전환](common/provisioning-toggle-on.png)

15. **설정** 섹션의 **범위** 에서 원하는 값을 선택 하 여 눈송이에 프로 비전 하려는 사용자 및/또는 그룹을 정의 합니다. 이 옵션을 사용할 수 없는 경우 관리자 자격 증명에서 필수 필드를 구성 하 고 **저장** 을 클릭 한 후 페이지를 새로 고칩니다. 

    ![프로비전 범위](common/provisioning-scope.png)

16. 프로비전할 준비가 되면 **저장**을 클릭합니다.

    ![프로비전 구성 저장](common/provisioning-configuration-save.png)

    이 작업은 **설정**의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화를 시작합니다. 초기 동기화는 Azure AD 프로비전 서비스가 실행되는 동안 약 40분마다 발생하는 후속 동기화보다 더 많은 시간이 걸립니다.

## <a name="step-6-monitor-your-deployment"></a>6단계. 배포 모니터링
프로비저닝을 구성한 후에는 다음 리소스를 사용하여 배포를 모니터링합니다.

1. [프로비저닝 로그](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)를 사용하여 어떤 사용자가 성공적으로 프로비저닝되었는지 확인합니다.
2. [진행률 표시줄](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user)을 통해 프로비저닝 주기 상태와 완료 정도를 확인합니다.
3. 프로비저닝 구성이 비정상 상태로 보이면 애플리케이션이 격리됩니다. 격리 상태에 대한 자세한 내용은 [여기](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)를 참조하세요.  

## <a name="connector-limitations"></a>커넥터 제한 사항

* 눈송이 생성 SCIM 토큰은 6 개월 후에 만료 됩니다. 프로 비전 동기화가 계속 작동 하도록 허용 하기 위해 만료 되기 전에 새로 고쳐야 합니다. 

## <a name="change-log"></a>로그 변경

* 07/21/2020-활성 특성을 통해 모든 사용자에 대해 일시 삭제를 사용 합니다.

## <a name="additional-resources"></a>추가 자료

* [엔터프라이즈 앱에 대 한 사용자 계정 프로 비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계
* [로그를 검토 하 고 프로 비전 활동에 대 한 보고서를 가져오는 방법에 대해 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).
