---
title: '자습서: Azure Active Directory로 자동 사용자 프로비저닝을 위한 Tableau Online 구성 | Microsoft Docs'
description: 자동으로 프로 비전 하 고 Tableau Online 사용자 계정을 프로 비전 해제 하도록 Azure Active Directory를 구성 하는 방법에 알아봅니다.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 0be9c435-f9a1-484d-8059-e578d5797d8e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-wingf-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2dbebfa5fa7d9b255cc685696bfe8b3f61d5cf6b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123757"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비저닝을 위한 Tableau Online 구성

이 자습서에서는 Tableau Online와 Azure Active Directory (Azure AD) Azure AD를 구성 하려면 자동으로 프로 비전 하 고 Tableau Online에 사용자 및 그룹 프로 비전 해제를 수행 하는 단계를 보여 줍니다.

> [!NOTE]
> 이 자습서는 Azure AD 사용자 프로 비전 서비스를 기반으로 빌드되는 커넥터를 설명 합니다. 이 서비스의 용도, 작동 방법 및 자주 묻는 질문에 대 한 내용은 참조 하세요 [사용자 프로 비전 및 Azure Active Directory를 사용 하 여 소프트웨어-as a service (SaaS) 응용 프로그램에 프로 비전 해제 자동화](../manage-apps/user-provisioning.md)합니다.

## <a name="prerequisites"></a>필수 조건

이 자습서에 설명 된 시나리오에서는 있다고 가정 합니다.

*   Azure AD 테넌트.
*   A [Tableau Online 테 넌 트](https://www.tableau.com/)합니다.
*   관리자 권한으로 Tableau Online에서 사용자 계정.

> [!NOTE]
> Azure AD 프로 비전 통합에 의존 합니다 [Tableau Online Rest API](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm)합니다. 이 API는 Tableau Online 개발자에 게 사용할 수 있습니다.

## <a name="add-tableau-online-from-the-azure-marketplace"></a>Azure Marketplace에서 Tableau Online 추가
Azure AD를 사용 하 여 프로 비전 하는 자동 사용자를 Tableau Online을 구성 하기 전에 추가할 Tableau Online Azure Marketplace에서 관리 되는 SaaS 응용 프로그램의 목록입니다.

에 Marketplace에서 Tableau Online 추가 하려면 다음이 단계를 수행 합니다.

1. 에 [Azure portal](https://portal.azure.com), 왼쪽 탐색 창에서 선택 **Azure Active Directory**합니다.

    ![Azure Active Directory 아이콘](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션**을 선택합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에서 입력 **Tableau Online** 선택한 **Tableau Online** 결과 패널에서 합니다. 응용 프로그램을 추가 하려면 선택 **추가**합니다.

    ![결과 목록의 Tableau Online](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>Tableau Online에 사용자 할당

이라는 개념을 사용 하 여 azure Active Directory *할당* 어떤 사용자가 선택한 앱에 대 한 액세스를 받아야 하는지를 판단 합니다. 자동 사용자 프로 비전의 컨텍스트에서 사용자 또는 Azure AD에서 응용 프로그램에 할당 된 그룹 동기화 됩니다.

구성 및 자동 사용자 프로 비전을 사용 하도록 설정 하기 전에 필요한 Tableau Online에 대 한 액세스는 사용자 또는 Azure AD의 그룹을 결정 합니다. 이 사용자 또는 그룹을 Tableau Online에 할당 하려면의 지침을 따릅니다 [엔터프라이즈 앱에 사용자 또는 그룹 할당](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)합니다.

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Tableau Online에 사용자를 할당하기 위한 주요 팁

*   단일 할당 하는 것이 좋습니다 Azure AD 사용자를 Tableau Online에 자동 사용자 프로 비전 구성을 테스트 합니다. 추가 사용자 또는 그룹을 나중에 할당할 수 있습니다.

*   Tableau Online에 사용자를 할당 하면 선택 유효한 응용 프로그램별 역할, 사용 가능한 경우 할당 대화 상자에서 합니다. **기본 액세스** 역할이 있는 사용자는 프로비전에서 제외됩니다.

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>Tableau Online에 자동 사용자 프로비저닝 구성

이 섹션에서는 Azure AD 프로 비전 서비스를 구성 하는 단계를 안내 합니다. 만들기, 업데이트 및 Azure AD의 사용자 또는 그룹 할당에 따라 Tableau Online에서 사용자 또는 그룹을 사용 하지 않도록 설정 하려면 사용 합니다.

> [!TIP]
> SAML 기반 single sign Tableau Online에 대 한 사용할 수 있습니다. 지침에 따라 합니다 [Tableau Online single sign-on 자습서](tableauonline-tutorial.md)합니다. Single sign on 구성할 수 있습니다 자동 사용자 프로 비전과 별개로 있지만 이러한 두 기능은 서로 보완적입니다.

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Azure AD에서 Tableau Online에 대 한 자동 사용자 프로 비전 구성

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 선택 **엔터프라이즈 응용 프로그램** > **모든 응용 프로그램** > **Tableau Online**합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Tableau Online**을 선택합니다.

    ![응용 프로그램 목록에서 Tableau Online 링크](common/all-applications.png)

3. **프로비전** 탭을 선택합니다.

    ![Tableau Online 프로비전](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. **프로비전 모드**를 **자동**으로 설정합니다.

    ![Tableau Online 프로 비전 모드](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. 아래는 **관리자 자격 증명** 섹션, 도메인, 관리자 사용자 이름, 관리자 암호 및 Tableau Online 계정의 콘텐츠 URL을 입력 합니다.

   * 에 **도메인** 상자에서 6 단계에 따라 하위 도메인을 입력 합니다.

   * 에 **관리자 사용자 이름** 상자에서 Clarizen 테 넌 트에서 관리자 계정의 사용자 이름을 입력 합니다. 예는 admin@contoso.com입니다.

   * 에 **관리자 암호** 상자에서 관리자 사용자 이름에 해당 하는 관리자 계정의 암호를 입력 합니다.

   * 에 **콘텐츠 URL** 상자에서 6 단계에 따라 하위 도메인을 입력 합니다.

6. Tableau Online에 대 한 관리 계정에 로그인 한 후에 값을 가져올 수 있습니다 **도메인** 하 고 **콘텐츠 URL** 관리 페이지의 url에서입니다.

    * Tableau Online 계정에 대한 **도메인**을 URL의 이 부분에서 복사할 수 있습니다.

        ![Tableau Online 도메인](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * 합니다 **콘텐츠 URL** 에 Tableau Online에 대 한 계정에서에서 복사할 수 있습니다이 섹션에서는 합니다. 계정 설정 하는 동안 정의 된 값입니다. 이 예제에서 값은 “contoso”입니다.

        ![Tableau Online 콘텐츠 URL](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > 프로그램 **도메인** 여기에 표시 된 것과에서 다를 수 있습니다.

7. 5 단계에에서 표시 된 상자에 입력 한 후 선택 **연결 테스트** 되도록 Azure AD는 Tableau Online에 연결할 수 있습니다. 연결에 실패 하면 Tableau Online 계정에 관리자 권한이 있는지 확인 하 고 다시 시도.

    ![Tableau Online 테스트 연결](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. 에 **알림 전자 메일** 상자에 사용자의 전자 메일 주소를 입력 하거나 프로 비전 오류 알림의 받을 그룹입니다. 선택 된 **오류가 발생 하는 경우 전자 메일 알림 보내기** 확인란 합니다.

    ![Tableau Online 알림 전자 메일](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. **저장**을 선택합니다.

10. **매핑** 섹션에서 **Azure Active Directory 사용자를 Tableau Online에 동기화**를 선택합니다.

    ![Tableau Online 사용자 동기화](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. Tableau online에서 Azure AD에서 동기화 되는 사용자 특성을 검토 합니다 **특성 매핑** 섹션입니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 Tableau Online의 사용자 계정을 일치시키는 데 사용됩니다. 모든 변경 내용을 저장 하려면 선택 **저장할**합니다.

    ![Tableau Online 일치 하는 사용자 특성](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

12. **매핑** 섹션에서 **Azure Active Directory 그룹을 Tableau에 동기화**를 선택합니다.

    ![Tableau Online 그룹 동기화](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. Tableau online에서 Azure AD에서 동기화 되는 그룹 특성을 검토 합니다 **특성 매핑** 섹션입니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 Tableau Online의 사용자 계정을 일치시키는 데 사용됩니다. 모든 변경 내용을 저장 하려면 선택 **저장할**합니다.

    ![Tableau Online 일치 하는 그룹 특성](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. 범위 지정 필터를 구성 하려면의 지침에 따라 합니다 [범위 지정 필터 자습서](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)합니다.

15. Tableau Online에 대 한 서비스를 프로 비전 하는 Azure AD를 사용 하도록 설정 합니다 **설정** 섹션, 변경 **프로 비전 상태** 에 **에서**.

    ![Tableau Online 프로 비전 상태](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. Tableau Online에 프로 비전 할 사용자 또는 그룹을 정의 합니다. 에 **설정을** 섹션에서 원하는 값을 선택 합니다 **범위**합니다.

    ![Tableau Online 범위](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. 프로 비전 할 준비가 선택 **저장할**합니다.

    ![Tableau Online 저장](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

이 작업에는 모든 사용자의 초기 동기화가 시작 됩니다. 또는에 정의 된 그룹 **범위** 에 **설정** 섹션입니다. 초기 동기화 이후 동기화 보다 더 오래 걸립니다. Azure AD 프로 비전 서비스를 실행 하기만 약 40 분 마다 발생 합니다. 

사용할 수는 **동기화 세부 정보** 진행률을 모니터링 하 고 프로 비전 작업 보고서에 링크를 따라 섹션입니다. 보고서에 프로 비전 서비스 Tableau Online에서 Azure AD에서 수행 되는 모든 작업에 설명 합니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../manage-apps/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../manage-apps/check-status-user-account-provisioning.md).

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
