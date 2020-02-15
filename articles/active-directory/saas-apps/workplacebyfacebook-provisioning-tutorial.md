---
title: '자습서: Azure Active Directory로 자동 사용자 프로비전을 위한 Workplace by Facebook 구성 | Microsoft Docs'
description: Azure Active Directory와 Workplace by Facebook 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7d8a7881c00427023e5f174461b3d8b24d83444
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121454"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>자습서: 사용자 프로비전을 위한 Workplace by Facebook 구성

이 자습서에서는 자동 사용자 프로 비전을 구성 하기 위해 Facebook 및 Azure Active Directory (Azure AD)로 작업 공간에서 수행 해야 하는 단계를 설명 합니다. 구성 된 경우 azure AD는 Azure AD 프로 비전 서비스를 사용 하 여 사용자 및 그룹을 [Facebook에서 작업 공간](https://work.workplace.com/) 으로 자동으로 프로 비전 하 고 프로 비전 해제 합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../manage-apps/user-provisioning.md)를 참조하세요.

## <a name="migrating-to-the-new-workplace-by-facebook-application"></a>Facebook 응용 프로그램으로 새 작업 공간으로 마이그레이션
Facebook을 사용 하 여 작업 공간에 기존에 통합 한 경우에는 아래에서 변경 된 내용에 대 한 섹션을 참조 하세요. Facebook에서 작업 공간을 처음 설정 하는 경우이 섹션을 건너뛰고 지원 되는 기능으로 이동할 수 있습니다. 

#### <a name="whats-changing"></a>변경 내용은?
* Azure AD 측의 변경: 작업 공간에서 사용자를 프로 비전 하는 권한 부여 방법이 오래 지속 된 비밀 토큰입니다. 곧 권한 부여 방법이 OAuth 권한 부여로 변경 된 것을 볼 수 있습니다. 
* 작업 공간 측 변경: 이전에 Azure AD 앱은 Facebook의 작업 공간에서 사용자 지정 통합 이었습니다. 이제 회사 통합 디렉터리에서 타사 응용 프로그램으로 Azure AD가 표시 됩니다. 

 

#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>기존 사용자 지정 통합을 새 응용 프로그램으로 마이그레이션하려면 어떻게 해야 하나요?
유효한 토큰과 함께 기존 작업 공간을 통합 하는 경우에 **는 아무 조치도 필요 하지**않습니다. 매주 새 응용 프로그램으로 고객을 자동으로 마이그레이션합니다. 이 작업은 백그라운드에서 완전히 수행 됩니다. 기다릴 수 없고 새 응용 프로그램으로 수동으로 이동 하려면 갤러리에서 작업 공간의 새 인스턴스를 추가 하 고 프로 비전을 다시 구성할 수 있습니다. 모든 새 작업 공간 인스턴스는 새 응용 프로그램 버전을 자동으로 사용 합니다. 

 
작업 공간 통합이 격리에 있는 경우 사용자를 마이그레이션하기 위해 올바른 토큰을 다시 제공 해야 합니다. 관리자 자격 증명 섹션은 회색으로 표시 되지만 다음을 추가할 수 있습니다. **Microsoft_AAD_IAM_userProvisioningEnableCredentialsOverride = true**)를 URL에 다시 저장 하 여 자격 증명을 다시 저장 합니다. 

https://portal.azure.com/?Microsoft_AAD_IAM_userProvisioningEnableCredentialsOverride=true

#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>응용 프로그램이 마이그레이션 되었는지 어떻게 알 수 있나요? 
응용 프로그램이 마이그레이션되면 변경 내용에 대 한 권한 부여 섹션의 배너가 제거 되 고 비밀 토큰 필드가 파란색 권한 부여 단추로 바뀝니다. 

#### <a name="the-admin-credentials-section-is-greyed-out-on-my-application-and-i-cant-save-why"></a>응용 프로그램에서 관리자 자격 증명 섹션이 회색으로 표시 되 고 저장할 수 없습니다. 그 이유는
기존 작업 공간 고객에 대 한 관리자 자격 증명 섹션을 잠 궜 습니다. 새 작업 공간 응용 프로그램으로 테 넌 트가 마이그레이션된 경우에는 관리자 자격 증명 섹션을 다시 업데이트할 수 있습니다. 기다릴 수 없으면 위의 URL을 사용 하 여 응용 프로그램을 편집할 수 있습니다. 

 
#### <a name="when-will-these-changes-happen"></a>이러한 변경 내용은 언제 발생 하나요?
작업 공간의 모든 새 인스턴스는 이미 새 통합/권한 부여 방법을 사용 하 고 있습니다. 기존 통합은 2 월에 점차적으로 마이그레이션됩니다. 해당 월의 끝까지 모든 테 넌 트에 대해 마이그레이션이 완료 됩니다. 

## <a name="capabilities-supported"></a>지원 되는 기능
> [!div class="checklist"]
> * Facebook으로 작업 공간에서 사용자 만들기
> * 더 이상 액세스 하지 않아도 되는 Facebook의 작업 공간 사용자 제거
> * 사용자 특성을 Azure AD와 Facebook의 작업 공간 간에 동기화 된 상태로 유지
> * Facebook 별 작업 공간에 [Single sign-on](https://docs.microsoft.com/azure/active-directory/saas-apps/workplacebyfacebook-tutorial) (권장)

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* [Azure AD 테 넌 트](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* 프로 비전을 구성할 수 있는 [권한이](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 있는 Azure AD의 사용자 계정 (예: 응용 프로그램 관리자, 클라우드 응용 프로그램 관리자, 응용 프로그램 소유자 또는 전역 관리자)
* Workplace by Facebook Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="step-1-plan-your-provisioning-deployment"></a>1단계. 프로 비전 배포 계획
1. [프로 비전 서비스의 작동 방식](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)에 대해 알아봅니다.
2. [프로 비전 범위](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)에 있는 사용자를 결정 합니다.
3. [AZURE AD와 Facebook의 작업 공간 간에 매핑할](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)데이터를 결정 합니다.

## <a name="step-2-configure-workplace-by-facebook-to-support-provisioning-with-azure-ad"></a>2단계. Azure AD를 사용 하 여 프로 비전을 지원 하도록 Facebook의 작업 공간 구성

프로비전 서비스를 구성하고 사용하도록 설정하기 전에 Workplace by Facebook 앱에 액세스해야 하는 사용자를 나타내는 Azure AD의 사용자 및/또는 그룹을 결정해야 합니다. 결정했으면 다음 지시에 따라 이러한 사용자를 Workplace by Facebook 앱에 할당할 수 있습니다.

*   단일 Azure AD 사용자를 Workplace by Facebook에 할당하여 프로비전 구성을 테스트하는 것이 좋습니다. 추가 사용자 및/또는 그룹은 나중에 할당할 수도 있습니다.

*   Workplace by Facebook에 사용자를 할당할 때 유효한 사용자 역할을 선택해야 합니다. "기본 액세스" 역할은 프로비전에 작동하지 않습니다.

## <a name="step-3-add-workplace-by-facebook-from-the-azure-ad-application-gallery"></a>3단계. Azure AD 응용 프로그램 갤러리에서 Facebook으로 작업 공간 추가

Azure AD 응용 프로그램 갤러리에서 Facebook으로 작업 공간을 추가 하 여 Facebook에서 작업 공간으로 프로 비전 관리를 시작 합니다. 이전에 SSO에 대 한 Facebook의 작업 공간을 설치한 경우 동일한 응용 프로그램을 사용할 수 있습니다. 그러나 처음에 통합을 테스트할 때 별도의 앱을 만드는 것이 좋습니다. [여기](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)에서 갤러리에서 응용 프로그램을 추가 하는 방법에 대해 자세히 알아보세요.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4단계. 프로 비전 범위에 있는 사용자를 정의 합니다. 

Azure AD 프로 비전 서비스를 사용 하면 응용 프로그램에 대 한 할당 또는 사용자/그룹의 특성을 기반으로 프로 비전 되는 사용자의 범위를 지정할 수 있습니다. 할당에 따라 앱에 프로 비전 할 사용자의 범위를 선택 하는 경우 다음 [단계](../manage-apps/assign-user-or-group-access-portal.md) 를 사용 하 여 응용 프로그램에 사용자 및 그룹을 할당할 수 있습니다. 사용자 또는 그룹의 특성만을 기준으로 프로 비전 할 사용자의 범위를 선택 하는 경우 [여기](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)에 설명 된 대로 범위 지정 필터를 사용할 수 있습니다. 

* Facebook에서 작업 공간에 사용자 및 그룹을 할당 하는 경우 **기본 액세스**외의 다른 역할을 선택 해야 합니다. 기본 액세스 역할이 있는 사용자는 프로 비전에서 제외 되 고 프로 비전 로그에 효과적으로 부여 되지 않은 것으로 표시 됩니다. 응용 프로그램에서 사용할 수 있는 유일한 역할이 기본 액세스 역할인 경우에는 [응용 프로그램 매니페스트를 업데이트](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) 하 여 역할을 더 추가할 수 있습니다. 

* 작은 크기를 시작 합니다. 모든 사용자에 게 배포 하기 전에 소수의 사용자 및 그룹 집합을 사용 하 여 테스트 합니다. 프로 비전 범위가 할당 된 사용자 및 그룹으로 설정 된 경우 앱에 하나 또는 두 개의 사용자 또는 그룹을 할당 하 여이를 제어할 수 있습니다. 범위가 모든 사용자 및 그룹으로 설정 된 경우 [특성 기반 범위 지정 필터](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)를 지정할 수 있습니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 응용 프로그램**을 선택한 다음 **모든 응용 프로그램**을 선택 합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Workplace by Facebook**을 선택합니다.

    ![애플리케이션 목록의 Workplace by Facebook 링크](common/all-applications.png)

3. **프로비전** 탭을 선택합니다.

    ![프로 비전 탭](common/provisioning.png)

4. **프로비전 모드**를 **자동**으로 설정합니다.

    ![프로 비전 탭](common/provisioning-automatic.png)

5. **관리자 자격 증명** 섹션 아래에서 **권한 부여**를 클릭 합니다. Facebook의 권한 부여 페이지에서 작업 공간으로 리디렉션됩니다. Facebook 사용자 이름으로 작업 공간을 입력 하 고 **계속** 단추를 클릭 합니다. **연결 테스트** 를 클릭 하 여 Azure AD가 Facebook의 작업 공간에 연결할 수 있는지 확인 합니다. 연결에 실패 하면 Facebook 계정으로 작업 공간에 관리자 권한이 있는지 확인 하 고 다시 시도 하세요.

    ![프로비전](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

    ![권한 부여](./media/workplacebyfacebook-provisioning-tutorial/workplacelogin.png)

6. **알림 전자 메일** 필드에 프로 비전 오류 알림을 받을 개인 또는 그룹의 전자 메일 주소를 입력 하 고 **오류가 발생 하면 전자 메일 알림 보내기** 확인란을 선택 합니다.

    ![알림 이메일](common/provisioning-notification-email.png)

7. **저장**을 선택합니다.

8. **매핑** 섹션 아래에서 **사용자에 게 작업 공간에 사용자 Azure Active Directory 동기화를**선택 합니다.

9. **특성 매핑** 섹션에서 Azure AD에서 Facebook으로 작업 공간으로 동기화 되는 사용자 특성을 검토 합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 Workplace by Facebook의 사용자 계정을 일치시키는 데 사용됩니다. 일치 하는 [대상 특성](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)을 변경 하도록 선택 하는 경우 Facebook API의 작업 공간에서 해당 특성을 기반으로 하는 사용자 필터링을 지원 하는지 확인 해야 합니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

   |attribute|Type|
   |---|---|
   |userName|String|
   |displayName|String|
   |활성|부울|
   |title|부울|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |이름 형식|String|
   |addresses [type eq "work"]. 서식 지정|String|
   |addresses[type eq "work"].streetAddress|String|
   |addresses [type eq "work"]. 집약성|String|
   |addresses [type eq "work"]. region|String|
   |addresses [type eq "work"]. country|String|
   |addresses[type eq "work"].postalCode|String|
   |addresses [type eq "other"]. 서식 지정|String|
   |phoneNumbers[type eq "work"].value|String|
   |phoneNumbers[type eq "mobile"].value|String|
   |phoneNumbers[type eq "fax"].value|String|
   |externalId|String|
   |preferredLanguage|String|
   |urn: ietf: params: scim: 스키마: 확장: enterprise: 2.0: User: manager|String|
   |urn: ietf: params: scim: 스키마: 확장: enterprise: 2.0: User: 부서|String|

10. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

11. Facebook에서 작업 공간에 대 한 Azure AD 프로 비전 서비스를 사용 하도록 설정 하려면 **설정** 섹션에서 **프로 비전 상태** 를 **켜기** 로 변경 합니다.

    ![프로비전 상태 켜기로 전환](common/provisioning-toggle-on.png)

12. **설정** 섹션의 **범위** 에서 원하는 값을 선택 하 여 Facebook에서 작업 공간에 프로 비전 하려는 사용자 및/또는 그룹을 정의 합니다.

    ![프로비전 범위](common/provisioning-scope.png)

13. 프로비전할 준비가 되면 **저장**을 클릭합니다.

    ![프로비전 구성 저장](common/provisioning-configuration-save.png)

이 작업은 **설정** 섹션의 **범위** 에 정의 된 모든 사용자 및 그룹의 초기 동기화 주기를 시작 합니다. Azure AD 프로 비전 서비스가 실행 되는 동안에는 대략 40 분 마다 발생 하는 이후 주기 보다 수행 하는 데 걸리는 시간이 길어집니다. 

## <a name="step-6-monitor-your-deployment"></a>6단계. 배포 모니터링
프로 비전을 구성한 후에는 다음 리소스를 사용 하 여 배포를 모니터링 합니다.

1. [프로 비전 로그](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) 를 사용 하 여 성공적으로 프로 비전 된 사용자를 확인 합니다.
2. [진행률 표시줄](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) 을 확인 하 여 프로 비전 주기의 상태와 완료 방법의 상태를 확인 합니다.
3. 프로 비전 구성이 비정상 상태에 있는 것 같으면 응용 프로그램이 격리 됩니다. [여기](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)에서 격리 상태에 대해 자세히 알아보세요.

## <a name="troubleshooting-tips"></a>문제 해결 팁
*  사용자가 성공적으로 생성 되지 않은 것을 확인 하 고 코드가 "1789003" 인 감사 로그 이벤트가 있는 경우 사용자가 확인 되지 않은 도메인에서 온 것임을 의미 합니다.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../manage-apps/check-status-user-account-provisioning.md).
