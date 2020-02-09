---
title: '자습서: Azure Active Directory을 사용 하 여 자동 사용자 프로 비전을 위한 PureCloud 구성 Microsoft Docs'
description: 사용자 계정을 Azure AD에서 PureCloud로 자동으로 프로 비전 및 프로 비전 해제 하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 5f04b88b-117e-40da-a15c-e3732b240d5d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2020
ms.author: Zhchia
ms.openlocfilehash: b0b5147faf82fedb6dc3c2eea54dcff1b9343f7a
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77087480"
---
# <a name="tutorial-configure-purecloud-by-genesys-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로 비전을 위해 PureCloud를 사용 하 여 구성

이 자습서에서는 자동 사용자 프로 비전을 구성 하기 위해 PureCloud (Azure AD)를 사용 하 Azure Active Directory 여에서 수행 해야 하는 단계를 설명 합니다. 구성 된 경우 azure AD는 Azure AD 프로 비전 서비스를 사용 하 여 사용자 및 그룹을 [PureCloud](https://www.genesys.com) 에 자동으로 프로 비전 하 고 프로 비전 해제 합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../manage-apps/user-provisioning.md)를 참조하세요. 


## <a name="capabilities-supported"></a>지원 되는 기능
> [!div class="checklist"]
> * PureCloud에서 사용자 만들기
> * 더 이상 액세스할 필요가 없는 경우 PureCloud에서 사용자를 제거 합니다.
> * PureCloud를 통해 Azure AD와 간에 사용자 특성을 동기화 된 상태로 유지
> * PureCloud에서 그룹 및 그룹 멤버 자격 프로 비전
> * PureCloud에 대 [한 Single sign-on](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial) (권장)

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* [Azure AD 테넌트](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* 프로 비전을 구성할 수 있는 [권한이](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 있는 Azure AD의 사용자 계정 (예: 응용 프로그램 관리자, 클라우드 응용 프로그램 관리자, 응용 프로그램 소유자 또는 전역 관리자). 
* PureCloud [조직](https://help.mypurecloud.com/?p=81984)입니다.
* Oauth 클라이언트를 만들 수 있는 [권한이](https://help.mypurecloud.com/?p=24360) 있는 사용자입니다.

## <a name="step-1-plan-your-provisioning-deployment"></a>1단계. 프로 비전 배포 계획
1. [프로 비전 서비스의 작동 방식](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)에 대해 알아봅니다.
2. [프로 비전 범위](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)에 있는 사용자를 결정 합니다.
3. [PureCloud을 통해 AZURE AD와 간에 매핑할](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)데이터를 결정 합니다. 

## <a name="step-2-configure-purecloud-by-genesys-to-support-provisioning-with-azure-ad"></a>2단계. Azure AD를 사용 하 여 프로 비전을 지원 하도록 PureCloud 구성

1. PureCloud 조직에 구성 된 [Oauth 클라이언트](https://help.mypurecloud.com/?p=188023) 를 만듭니다.
2. [Oauth 클라이언트를 사용 하 여](https://developer.mypurecloud.com/api/rest/authorization/use-client-credentials.html)토큰을 생성 합니다.
3. PureCloud 내에서 그룹 멤버 자격을 자동으로 프로 비전 하려는 경우에는 Azure AD에서 그룹에 대해 동일한 이름을 사용 하 여 PureCloud에 [그룹을 만들어야](https://help.mypurecloud.com/?p=52397) 합니다.

## <a name="step-3-add-purecloud-by-genesys-from-the-azure-ad-application-gallery"></a>3단계. Azure AD 응용 프로그램 갤러리에서 PureCloud를 추가 하 여 추가

Azure AD 응용 프로그램 갤러리에서 PureCloud를 추가 하 여 PureCloud로 프로 비전 관리를 시작 합니다. 이전에 SSO에 대해 PureCloud를 설정 하 여 설치한 경우 동일한 응용 프로그램을 사용할 수 있습니다. 그러나 처음에 통합을 테스트할 때 별도의 앱을 만드는 것이 좋습니다. [여기](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)에서 갤러리에서 응용 프로그램을 추가 하는 방법에 대해 자세히 알아보세요. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4단계. 프로 비전 범위에 있는 사용자를 정의 합니다. 

Azure AD 프로 비전 서비스를 사용 하면 응용 프로그램에 대 한 할당 또는 사용자/그룹의 특성을 기반으로 프로 비전 되는 사용자의 범위를 지정할 수 있습니다. 할당에 따라 앱에 프로 비전 할 사용자의 범위를 선택 하는 경우 다음 [단계](../manage-apps/assign-user-or-group-access-portal.md) 를 사용 하 여 응용 프로그램에 사용자 및 그룹을 할당할 수 있습니다. 사용자 또는 그룹의 특성만을 기준으로 프로 비전 할 사용자의 범위를 선택 하는 경우 [여기](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)에 설명 된 대로 범위 지정 필터를 사용할 수 있습니다. 

* PureCloud에 사용자 및 그룹을 할당 하는 경우 **기본 액세스**이외의 다른 역할을 선택 해야 합니다. 기본 액세스 역할이 있는 사용자는 프로 비전에서 제외 되 고 프로 비전 로그에 효과적으로 부여 되지 않은 것으로 표시 됩니다. 응용 프로그램에서 사용할 수 있는 유일한 역할이 기본 액세스 역할인 경우에는 [응용 프로그램 매니페스트를 업데이트](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) 하 여 역할을 더 추가할 수 있습니다. 

* 작은 크기를 시작 합니다. 모든 사용자에 게 배포 하기 전에 소수의 사용자 및 그룹 집합을 사용 하 여 테스트 합니다. 프로 비전 범위가 할당 된 사용자 및 그룹으로 설정 된 경우 앱에 하나 또는 두 개의 사용자 또는 그룹을 할당 하 여이를 제어할 수 있습니다. 범위가 모든 사용자 및 그룹으로 설정 된 경우 [특성 기반 범위 지정 필터](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)를 지정할 수 있습니다. 


## <a name="step-5-configure-automatic-user-provisioning-to-purecloud-by-genesys"></a>5단계. PureCloud에 자동 사용자 프로 비전 구성 

이 섹션에서는 azure ad의 사용자 및/또는 그룹 할당에 따라 TestApp의 사용자 및/또는 그룹을 만들고, 업데이트 하 고, 비활성화 하도록 Azure AD 프로 비전 서비스를 구성 하는 단계를 안내 합니다.

### <a name="to-configure-automatic-user-provisioning-for-purecloud-by-genesys-in-azure-ad"></a>Azure AD에서 PureCloud에 대 한 자동 사용자 프로 비전을 구성 하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 응용 프로그램**을 선택한 다음 **모든 응용 프로그램**을 선택 합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **PureCloud by Genesys**를 선택합니다.

    ![애플리케이션 목록의 PureCloud by Genesys 링크](common/all-applications.png)

3. **프로비전** 탭을 선택합니다.

    ![프로 비전 탭](common/provisioning.png)

4. **프로비전 모드**를 **자동**으로 설정합니다.

    ![프로 비전 탭](common/provisioning-automatic.png)

5. **관리자 자격 증명** 섹션 아래에서 PureCloud by를 입력 하 여 **테넌트 Url** 및 **비밀 토큰** 필드에 Oauth 토큰을 입력 합니다. **연결 테스트** 를 클릭 하 여 Azure AD가 PureCloud에 연결할 수 있는지 확인 합니다. 연결에 실패 하면 Genesys 계정에 관리자 권한이 있는지 확인 하 고 다시 시도 하세요.

    ![프로비전](./media/purecloud-by-genesys-provisioning-tutorial/provisioning.png)

6. **알림 전자 메일** 필드에 프로 비전 오류 알림을 받을 개인 또는 그룹의 전자 메일 주소를 입력 하 고 **오류가 발생 하면 전자 메일 알림 보내기** 확인란을 선택 합니다.

    ![알림 이메일](common/provisioning-notification-email.png)

7. **저장**을 선택합니다.

8. **매핑** 섹션에서 **Azure Active Directory 사용자를 PureCloud에 동기화를**선택 합니다.

9. **특성 매핑** 섹션에서 Azure AD에서 PureCloud로 동기화 되는 사용자 특성을 검토 합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 PureCloud의 사용자 계정을 일치 시키는 데 사용 됩니다. 일치 하는 [대상 특성](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)을 변경 하도록 선택 하는 경우 PureCloud API에서 해당 특성에 따라 사용자 필터링을 지원 하는지 확인 해야 합니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

     |attribute|Type|
     |---|---|
     |userName|String|
     |활성|부울|
     |displayName|String|
     |emails[type eq "work"].value|String|
     |title|String|
     |phoneNumbers[type eq "mobile"].value|String|
     |phoneNumbers[type eq "work"].value|String|
     |urn: ietf: params: scim: 스키마: 확장: enterprise: 2.0: User: 부서|String|
     |urn: ietf: params: scim: 스키마: 확장: enterprise: 2.0: User: manager|참조|

10. **매핑** 섹션에서 **Azure Active Directory 그룹을 PureCloud에 동기화를**선택 합니다.

11. **특성 매핑** 섹션에서 Azure AD에서 PureCloud로 동기화 되는 그룹 특성을 검토 합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 PureCloud의 그룹을 일치 시키는 데 사용 됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다. PureCloud by Genesys는 그룹 만들기 또는 삭제를 지원 하지 않으며 그룹 업데이트만 지원 합니다.

      |attribute|Type|
      |---|---|
      |displayName|String|
      |externalId|String|
      |members|참조|

12. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

13. PureCloud에서 Azure AD 프로 비전 서비스를 사용 하도록 설정 하려면 **설정** 섹션에서 **프로 비전 상태** 를 **켜기** 로 변경 합니다.

    ![프로비전 상태 켜기로 전환](common/provisioning-toggle-on.png)

14. **설정** 섹션의 **범위** 에서 원하는 값을 선택 하 여 PureCloud에 프로 비전 하려는 사용자 및/또는 그룹을 정의 합니다.

    ![프로비전 범위](common/provisioning-scope.png)

15. 프로비전할 준비가 되면 **저장**을 클릭합니다.

    ![프로비전 구성 저장](common/provisioning-configuration-save.png)

이 작업은 **설정** 섹션의 **범위** 에 정의 된 모든 사용자 및 그룹의 초기 동기화 주기를 시작 합니다. Azure AD 프로 비전 서비스가 실행 되는 동안에는 대략 40 분 마다 발생 하는 이후 주기 보다 수행 하는 데 걸리는 시간이 길어집니다. 

## <a name="step-6-monitor-your-deployment"></a>6단계. 배포 모니터링
프로 비전을 구성한 후에는 다음 리소스를 사용 하 여 배포를 모니터링 합니다.

* [프로 비전 로그](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) 를 사용 하 여 성공적으로 프로 비전 된 사용자를 확인 합니다.
* [진행률 표시줄](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) 을 확인 하 여 프로 비전 주기의 상태와 완료 방법의 상태를 확인 합니다.
* 프로 비전 구성이 비정상 상태에 있는 것 같으면 응용 프로그램이 격리 됩니다. [여기](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)에서 격리 상태에 대해 자세히 알아보세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../manage-apps/check-status-user-account-provisioning.md).
