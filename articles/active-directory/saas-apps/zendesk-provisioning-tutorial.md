---
title: '자습서: Azure Active Directory를 사용한 자동 사용자 프로비저닝을 위한 Zendesk 구성 | Microsoft Docs'
description: 자동으로 프로 비전 하 고 사용자 계정을 Zendesk로 프로 비전 해제 하도록 Azure Active Directory를 구성 하는 방법에 알아봅니다.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: 01d5e4d5-d856-42c4-a504-96fa554baf66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant
ms.collection: M365-identity-device-management
ms.openlocfilehash: f559d2c2398998ba590419758de559f21d9b65f5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114668"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비저닝에 대한 Zendesk 구성

이 자습서에서는 자동으로 프로 비전 하 고 사용자 및 그룹을 Zendesk로 프로 비전 해제 하도록 Azure AD를 구성 하려면 Zendesk 및 Azure Active Directory (Azure AD)에서 수행 하는 단계를 보여 줍니다.

> [!NOTE]
> 이 자습서는 Azure AD 사용자 프로 비전 서비스를 기반으로 빌드되는 커넥터를 설명 합니다. 이 서비스의 용도, 작동 방법 및 자주 묻는 질문에 대 한 내용은 참조 하세요 [사용자 프로 비전 및 Azure Active Directory를 사용 하 여 소프트웨어-as a service (SaaS) 응용 프로그램에 프로 비전 해제 자동화](../manage-apps/user-provisioning.md)합니다.

## <a name="prerequisites"></a>필수 조건

이 자습서에 설명 된 시나리오에서는 있다고 가정 합니다.

* Azure AD 테넌트.
* 사용 하 여 Zendesk 테 넌 트를 [Enterprise](https://www.zendesk.com/product/pricing/) 계획 또는 이상을 사용 하도록 설정 합니다.
* 관리자 권한이 있는 Zendesk의 사용자 계정.

> [!NOTE]
> Azure AD 프로 비전 통합에 의존 합니다 [Zendesk Rest API](https://developer.zendesk.com/rest_api/docs/core/introduction)합니다. 이 API는 Zendesk 팀 엔터프라이즈 계획에 이상을 사용할 수 있습니다.

## <a name="add-zendesk-from-the-azure-marketplace"></a>Azure Marketplace에서 Zendesk를 추가 합니다.

Azure AD를 사용 하 여 프로 비전 하는 자동 사용자를 Zendesk를 구성 하기 전에 관리 되는 SaaS 응용 프로그램의 목록에 Azure Marketplace에서 Zendesk를 추가 합니다.

Marketplace에서 Zendesk를 추가 하려면 다음이 단계를 수행 합니다.

1. 에 [Azure portal](https://portal.azure.com), 왼쪽 탐색 창에서 선택 **Azure Active Directory**합니다.

    ![Azure Active Directory 아이콘](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션**을 선택합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에서 입력 **Zendesk** 선택한 **Zendesk** 결과 패널에서 합니다. 응용 프로그램을 추가 하려면 선택 **추가**합니다.

    ![결과 목록의 Zendesk](common/search-new-app.png)

## <a name="assign-users-to-zendesk"></a>Zendesk에 사용자 할당

이라는 개념을 사용 하 여 azure Active Directory *할당* 어떤 사용자가 선택한 앱에 대 한 액세스를 받아야 하는지를 판단 합니다. 자동 사용자 프로 비전의 컨텍스트에서 사용자 또는 Azure AD에서 응용 프로그램에 할당 된 그룹 동기화 됩니다.

구성 및 자동 사용자 프로 비전을 사용 하도록 설정 하기 전에 Zendesk에 대 한 액세스를 필요한 사용자 또는 Azure AD의 그룹을 결정 합니다. 이 사용자 또는 그룹을 Zendesk에 할당 하려면의 지침을 따릅니다 [엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)합니다.

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Zendesk에 사용자를 할당하기 위한 주요 팁

* 현재 Zendesk 역할 자동 및 동적으로 채워지는 Azure portal UI에서. 사용자에 게 Zendesk 역할을 할당 하기 전에 Zendesk 테 넌 트에서 최신 역할을 검색 하려면 Zendesk에 대 한 초기 동기화가 완료 되었는지 확인 합니다.

* 단일 할당 하는 것이 좋습니다 Azure AD 사용자를 Zendesk에 초기 자동 사용자 프로 비전 구성을 테스트 합니다. 할당할 수 있습니다 추가 사용자 또는 그룹 나중에 성공한 후 테스트 합니다.

* Zendesk에 사용자를 할당 하면 선택 유효한 응용 프로그램별 역할, 사용 가능한 경우 할당 대화 상자에서 합니다. **기본 액세스** 역할이 있는 사용자는 프로비전에서 제외됩니다.

## <a name="configure-automatic-user-provisioning-to-zendesk"></a>Zendesk에 자동 사용자 프로비저닝 구성 

이 섹션에서는 Azure AD 프로 비전 서비스를 구성 하는 단계를 안내 합니다. 만들기, 업데이트 및 Azure AD의 사용자 또는 그룹 할당에 따라 Zendesk의 사용자 또는 그룹을 사용 하지 않도록 설정 하려면 사용 합니다.

> [!TIP]
> SAML 기반 single sign Zendesk에 사용할 수 있습니다. 지침에 따라 합니다 [Zendesk single sign-on 자습서](zendesk-tutorial.md)합니다. Single sign on 구성할 수 있습니다 자동 사용자 프로 비전과 별개로 있지만 이러한 두 기능은 서로 보완적입니다.

### <a name="configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Azure AD에서 Zendesk에 대 한 자동 사용자 프로 비전 구성

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 선택 **엔터프라이즈 응용 프로그램** > **모든 응용 프로그램** > **Zendesk**합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Zendesk**를 선택합니다.

    ![응용 프로그램 목록의 Zendesk 링크](common/all-applications.png)

3. **프로비전** 탭을 선택합니다.

    ![Zendesk 프로비전](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. **프로비전 모드**를 **자동**으로 설정합니다.

    ![Zendesk 프로 비전 모드](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. 아래는 **관리자 자격 증명** 섹션에서 관리자 사용자 이름, 보안 토큰 및 Zendesk 계정의 도메인을 입력 합니다. 이러한 값의 예는 다음과 같습니다.

   * 에 **관리자 사용자 이름** 상자에서 Zendesk 테 넌 트에서 관리자 계정의 사용자 이름을 입력 합니다. 예는 admin@contoso.com입니다.

   * 에 **비밀 토큰** 상자에서 6 단계에에서 설명 된 대로 비밀 토큰을 입력 합니다.

   * 에 **도메인** 상자에서 Zendesk 테 넌 트의 하위 도메인을 입력 합니다. 예를 들어, 테 넌 트 URL 사용 하 여 계정의 `https://my-tenant.zendesk.com`, 하위 도메인이 **내 테 넌 트**합니다.

6. Zendesk 계정에 대 한 보안 토큰에 위치한 **Admin** > **API** > **설정**합니다. 했는지 **액세스 토큰** 로 설정 되어 **Enabled**.

    ![Zendesk 관리자 설정](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Zendesk 비밀 토큰](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. 5 단계에에서 표시 된 상자에 입력 한 후 선택 **연결 테스트** 되도록 Azure AD가 Zendesk에 연결 합니다. 연결에 실패 하면 Zendesk 계정에 관리자 권한이 있는지 확인 하 고 다시 시도.

    ![Zendesk 테스트 연결](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. 에 **알림 전자 메일** 상자에 사용자의 전자 메일 주소를 입력 하거나 프로 비전 오류 알림의 받을 그룹입니다. 선택 된 **오류가 발생 하는 경우 전자 메일 알림 보내기** 확인란 합니다.

    ![Zendesk 알림 전자 메일](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. **저장**을 선택합니다.

10. **매핑** 섹션에서 **Synchronize Azure Active Directory Users to Zendesk**(Azure Active Directory 사용자를 Zendesk에 동기화)를 선택합니다.

    ![Zendesk 사용자 동기화](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. Azure AD에서 zendesk에 동기화 되는 사용자 특성을 검토 합니다 **특성 매핑** 섹션입니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 Zendesk의 사용자 계정을 일치시키는 데 사용됩니다. 모든 변경 내용을 저장 하려면 선택 **저장할**합니다.

    ![Zendesk 일치 하는 사용자 특성](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. 아래는 **매핑을** 섹션에서 **동기화 할 Azure Active Directory 그룹을 Zendesk에**입니다.

    ![Zendesk 그룹 동기화](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. Azure AD에서 zendesk에 동기화 되는 그룹 특성을 검토 합니다 **특성 매핑** 섹션입니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 Zendesk의 그룹을 일치시키는 데 사용됩니다. 모든 변경 내용을 저장 하려면 선택 **저장할**합니다.

    ![Zendesk 일치 하는 그룹 특성](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. 범위 지정 필터를 구성 하려면의 지침에 따라 합니다 [범위 지정 필터 자습서](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)합니다.

15. Azure AD에서 Zendesk에 대 한 서비스를 프로 비전을 사용 하도록 설정 합니다 **설정** 섹션, 변경 **프로 비전 상태** 를 **에서**.

    ![Zendesk 프로 비전 상태](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Zendesk에 프로 비전 할 사용자 또는 그룹을 정의 합니다. 에 **설정을** 섹션에서 원하는 값을 선택 합니다 **범위**합니다.

    ![Zendesk 범위](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. 프로 비전 할 준비가 선택 **저장할**합니다.

    ![Zendesk 저장](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

이 작업에는 모든 사용자의 초기 동기화가 시작 됩니다. 또는에 정의 된 그룹 **범위** 에 **설정** 섹션입니다. 초기 동기화 이후 동기화 보다 더 오래 걸립니다. Azure AD 프로 비전 서비스를 실행 하기만 약 40 분 마다 발생 합니다. 

사용할 수는 **동기화 세부 정보** 진행률을 모니터링 하 고 프로 비전 작업 보고서에 링크를 따라 섹션입니다. 보고서에 프로 비전 서비스 Zendesk에서 Azure AD에서 수행 되는 모든 작업에 설명 합니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../manage-apps/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="connector-limitations"></a>커넥터 제한 사항

* Zendesk 사용자에 대 한 그룹의 사용을 지원 **에이전트** 역할만 합니다. 자세한 내용은 참조는 [Zendesk 설명서](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups)합니다.

* 사용자 지정 역할을 사용자 또는 그룹에 할당할도 프로 비전 서비스는 Azure AD 자동 사용자 기본 역할 할당 **에이전트**합니다. 에이전트 에서만 사용자 지정 역할을 할당할 수 있습니다. 자세한 내용은 참조는 [Zendesk API 설명서](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests)합니다. 

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../manage-apps/check-status-user-account-provisioning.md).

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
