---
title: '자습서: Azure Active Directory로 자동 사용자 프로비저닝을 위한 MediusFlow 구성 | Microsoft Docs'
description: 사용자 계정을 Azure AD에서 MediusFlow로 자동으로 프로비저닝 및 프로비저닝 해제하는 방법을 알아봅니다.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 04/30/2020
ms.author: Zhchia
ms.openlocfilehash: e8a64b3f42e52363f70d536963594bb6083a0992
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548075"
---
# <a name="tutorial-configure-mediusflow-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비저닝을 위한 MediusFlow 구성

이 자습서에서는 자동 사용자 프로비저닝을 구성하기 위해 MediusFlow 및 Azure AD(Azure Active Directory)에서 수행해야 하는 단계를 설명합니다. 구성된 경우 Azure AD는 Azure AD 프로비저닝 서비스를 사용하여 사용자 및 그룹을 [MediusFlow](https://www.mediusflow.com/)로 자동으로 프로비저닝 및 프로비저닝 해제합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../manage-apps/user-provisioning.md)를 참조하세요. 


## <a name="capabilities-supported"></a>지원되는 기능
> [!div class="checklist"]
> * MediusFlow에서 사용자 만들기
> * MediusFlow에서 더 이상 액세스할 필요가 없는 사용자 제거
> * Azure AD와 MediusFlow 간에 사용자 특성을 동기화된 상태로 유지
> * MediusFlow에서 그룹 및 그룹 구성원 프로비저닝
> * MediusFlow에 대한 Single Sign-On(권장)

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* [Azure AD 테넌트](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* 프로비저닝을 구성할 [권한](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)이 있는 Azure AD의 사용자 계정(예: 애플리케이션 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 소유자 또는 전역 관리자). 
* 품질 보증 또는 프로덕션 테넌트가 포함된 활성 MediusFlow 구독
* MediusFlow 내에서 구성을 수행할 수 있는 관리자 액세스 권한이 있는 MediusFlow의 사용자 계정
* 사용자가 프로비저닝되어야 하는 MediusFlow 테넌트에 추가되는 회사

## <a name="step-1-plan-your-provisioning-deployment"></a>1단계. 프로비저닝 배포 계획
1. [프로비저닝 서비스의 작동 방식](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)에 대해 알아봅니다.
2. [프로비저닝 범위](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)에 있는 사용자를 결정합니다.
3. [Azure AD와 MediusFlow 간에 매핑](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)할 데이터를 결정합니다. 

## <a name="step-2-configure-mediusflow-to-support-provisioning-with-azure-ad"></a>2단계. Azure AD에서 프로비저닝을 지원하도록 MediusFlow 구성

### <a name="activate-the-microsoft-365-app-within-mediusflow"></a>MediusFlow 내에서 Microsoft 365 앱 활성화
우선 다음 단계를 수행하여 MediusFlow 내에서 Azure AD 로그인 및 Azure AD 구성 기능에 대한 액세스를 사용하도록 설정합니다.

#### <a name="user-login"></a>사용자 로그인
Microsoft 365/Azure AD에 대 한 로그인 흐름을 사용 하도록 설정 하려면 [this] (문서를 참조 https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-login-setup) 하세요.

#### <a name="user-transfer-configuration"></a>사용자 전송 구성
Azure AD에서 프로 비전 할 사용자의 구성 포털을 사용 하도록 설정 하려면 [이](
https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-sync-setup) 문서를 참조 하세요.

#### <a name="configure-user-provisioning"></a>사용자 프로비전 구성

1.  테넌트 ID를 제공하여 [MediusFlow 관리 콘솔](https://office365.cloudapp.mediusflow.com/)에 로그인합니다.

    ![Authenticate](./media/mediusflow-provisioning-tutorial/1-auth.png)

2. MediusFlow와의 연결을 확인합니다.

    ![확인](./media/mediusflow-provisioning-tutorial/2-verify-connection.png)

3. Azure AD 테넌트 ID를 제공합니다.

    ![테넌트 ID 제공](./media/mediusflow-provisioning-tutorial/3-provide-azuread-tenantid.png)

   이를 확인하는 방법은 [FAQ](https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#how-do-i-get-the-azure-tenant-id)를 참조하세요.

4. 구성을 저장합니다.

    ![Authenticate](./media/mediusflow-provisioning-tutorial/4-save-config.png)

5. 사용자 프로비저닝을 선택하고 **확인**을 클릭합니다.

    ![Authenticate](./media/mediusflow-provisioning-tutorial/5-select-user-provisioning.png)

6. **비밀 키 생성**을 클릭합니다. 이 값을 복사하고 저장합니다. 이 값은 Azure Portal에서 MediusFLow 애플리케이션의 **프로비저닝** 탭에 있는 **비밀 토큰** 필드에 입력됩니다.

    ![Authenticate](./media/mediusflow-provisioning-tutorial/6-create-secret-1.png)

7. **확인**을 클릭합니다.

    ![Authenticate](./media/mediusflow-provisioning-tutorial/7-confirm-secret.png)

8. MediusFlow에서 미리 정의 된 역할, 회사 및 기타 일반 구성 집합을 사용 하 여 가져온 사용자를 가져오려면 먼저 구성 해야 합니다. 먼저 **새 구성 추가**를 클릭하여 구성을 추가합니다.

    ![사용자 구성](./media/mediusflow-provisioning-tutorial/8-configure-user-configuration-1.png)

9. 사용자에 대한 기본 설정을 지정합니다. 이 보기에서는 기본 특성을 설정할 수 있습니다. 표준 설정이 괜찮으면 유효한 회사 이름만 지정하면 됩니다. 이러한 구성 설정은 Mediusflow에서 가져오므로 Mediusflow를 먼저 구성해야 합니다. 자세한 내용은 이 문서의 **필수 조건** 섹션을 참조하세요.

    ![사용자 구성](./media/mediusflow-provisioning-tutorial/9-configure-user-config-detail-1.png)

10. **저장**을 클릭하여 사용자 구성을 저장합니다.

    ![사용자 구성](./media/mediusflow-provisioning-tutorial/10-done-1.png)

11. 사용자 프로비저닝 링크를 가져오려면 **SCIM 링크 복사**를 클릭합니다. 이 값을 복사하여 저장합니다. 이 값은 Azure Portal의 MediusFLow 애플리케이션에서 **프로비저닝** 탭에 있는 **테넌트 URL** 필드에 입력됩니다.
 
    ![사용자 구성](./media/mediusflow-provisioning-tutorial/11-get-scim-link.png)

## <a name="step-3-add-mediusflow-from-the-azure-ad-application-gallery"></a>3단계. Azure AD 애플리케이션 갤러리에서 MediusFlow 추가

Azure AD 애플리케이션 갤러리에서 MediusFlow를 추가하여 MediusFlow로 프로비저닝 관리를 시작합니다. 이전에 SSO용 MediusFlow를 설정했다면 동일한 애플리케이션을 사용할 수 있습니다. 그러나 처음 통합을 테스트하는 경우 별도의 앱을 만드는 것이 좋습니다. [여기](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)를 클릭하여 갤러리에서 애플리케이션을 추가하는 방법에 대해 자세히 알아봅니다. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4단계. 프로비저닝 범위에 있는 사용자 정의 

Azure AD 프로비저닝 서비스를 사용하면 애플리케이션에 대한 할당 또는 사용자/그룹의 특성을 기반으로 프로비저닝되는 사용자의 범위를 지정할 수 있습니다. 할당을 기준으로 앱에 프로비저닝할 사용자의 범위를 선택하려면 다음 [단계](../manage-apps/assign-user-or-group-access-portal.md)를 사용하여 애플리케이션에 사용자 및 그룹을 할당할 수 있습니다. 사용자 또는 그룹의 특성만을 기준으로 프로비저닝할 사용자의 범위를 선택하려면 [여기](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 설명된 대로 범위 지정 필터를 사용할 수 있습니다. 

* MediusFlow에 사용자 및 그룹을 할당할 때 **기본 액세스** 이외의 역할을 선택해야 합니다. 기본 액세스 역할이 있는 사용자는 프로비저닝에서 제외되고 프로비저닝 로그에 실질적으로 권한을 부여받지 않은 것으로 표시됩니다. 애플리케이션에서 사용할 수 있는 유일한 역할이 기본 액세스 역할인 경우에는 [애플리케이션 매니페스트를 업데이트](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)하여 역할을 더 추가할 수 있습니다. 

* 소규모로 시작합니다. 모든 사용자에게 배포하기 전에 소수의 사용자 및 그룹 세트로 테스트합니다. 프로비저닝 범위가 할당된 사용자 및 그룹으로 설정된 경우 앱에 하나 또는 두 개의 사용자 또는 그룹을 할당하여 범위를 제어할 수 있습니다. 범위가 모든 사용자 및 그룹으로 설정된 경우 [특성 기반 범위 지정 필터](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)를 지정할 수 있습니다. 


## <a name="step-5-configure-automatic-user-provisioning-to-mediusflow"></a>5단계. MediusFlow에 대한 자동 사용자 프로비저닝 구성 

이 섹션에서는 Azure AD의 사용자 및/또는 그룹 할당에 따라 TestApp에서 사용자 및/또는 그룹을 만들고, 업데이트하고, 사용 해제하도록 Azure AD 프로비저닝 서비스를 구성하는 단계를 안내합니다.

### <a name="to-configure-automatic-user-provisioning-for-mediusflow-in-azure-ad"></a>Azure AD에서 MediusFlow에 대한 자동 사용자 프로비저닝을 구성하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 애플리케이션**, **모든 애플리케이션**을 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **MediusFlow**를 선택합니다.

    ![애플리케이션 목록의 MediusFlow 링크](common/all-applications.png)

3. **프로비전** 탭을 선택합니다.

    ![프로비저닝 탭](common/provisioning.png)

4. **프로비전 모드**를 **자동**으로 설정합니다.

    ![프로비저닝 탭](common/provisioning-automatic.png)

5. **관리자 자격 증명** 섹션에서 이전에 **테넌트 URL**에서 검색된 테넌트 URL 값을 입력합니다. **비밀 토큰**에 이전에 검색된 비밀 토큰 값을 입력합니다. **연결 테스트**를 클릭하여 Azure AD가 MediusFlow에 연결할 수 있는지 확인합니다. 연결이 실패하면 MediusFlow 계정에 관리자 권한이 있는지 확인한 후 다시 시도합니다.

      ![프로비전](./media/mediusflow-provisioning-tutorial/provisioning.png)

6. **알림 이메일** 필드에 프로비저닝 오류 알림을 받을 개인 또는 그룹의 이메일 주소를 입력하고, **오류가 발생할 경우 이메일 알림 보내기** 확인란을 선택합니다.

    ![알림 이메일](common/provisioning-notification-email.png)

7. **저장**을 선택합니다.

8. **매핑** 섹션에서 **Azure Active Directory 사용자를 MediusFlow에 동기화**를 선택합니다.

9. **특성 매핑** 섹션에서 Azure AD에서 MediusFlow로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 MediusFlow의 사용자 계정을 일치시키는 데 사용됩니다. [일치하는 대상 특성](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)을 변경하는 경우 MediusFlow API에서 해당 특성에 따라 사용자 필터링을 지원하는지 확인해야 합니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

   |attribute|Type|
   |---|---|
   |userName|String|
   |emails[type eq "work"].value|String|
   |name.displayName|String|
   |활성|부울|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |externalID|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|참조|


10. **매핑** 섹션에서 **Azure Active Directory 그룹을 MediusFlow에 동기화**를 선택합니다.

11. **특성 매핑** 섹션에서 Azure AD에서 MediusFlow로 동기화되는 그룹 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 MediusFlow의 그룹을 일치시키는 데 사용됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

      |attribute|Type|
      |---|---|
      |displayName|String|
      |externalID|String|
      |members|참조|

12. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

13. MediusFlow에 대한 Azure AD 프로비저닝 서비스를 사용하도록 설정하려면 **설정** 섹션에서 **프로비전 상태**를 **켜기**로 변경합니다.

    ![프로비전 상태 켜기로 전환](common/provisioning-toggle-on.png)

14. **설정** 섹션의 **범위**에서 원하는 값을 선택하여 MediusFlow에 프로비저닝하려는 사용자 및/또는 그룹을 정의합니다.

    ![프로비전 범위](common/provisioning-scope.png)

15. 프로비전할 준비가 되면 **저장**을 클릭합니다.

    ![프로비전 구성 저장](common/provisioning-configuration-save.png)

이 작업은 **설정**의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화 주기를 시작합니다. 초기 주기는 Azure AD 프로비저닝 서비스가 실행되는 동안 약 40분마다 발생하는 후속 주기보다 더 많은 시간이 걸립니다. 

## <a name="step-6-monitor-your-deployment"></a>6단계. 배포 모니터링
프로비저닝을 구성한 후에는 다음 리소스를 사용하여 배포를 모니터링합니다.

1. [프로비저닝 로그](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)를 사용하여 어떤 사용자가 성공적으로 프로비저닝되었는지 여부를 확인합니다.
2. [진행률 표시줄](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user)을 통해 프로비저닝 주기 상태와 완료 정도를 확인합니다.
3. 프로비저닝 구성이 비정상 상태로 보이면 애플리케이션이 격리됩니다. 격리 상태에 대한 자세한 내용은 [여기](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../manage-apps/check-status-user-account-provisioning.md).
