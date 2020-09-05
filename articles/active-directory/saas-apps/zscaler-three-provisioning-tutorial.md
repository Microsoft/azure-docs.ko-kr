---
title: '자습서: Azure Active Directory를 사용하여 Zscaler Three에 대한 자동 사용자 프로비저닝 구성 | Microsoft Docs'
description: 이 자습서에서는 Zscaler Three에 대한 사용자 계정을 자동으로 프로비저닝하거나 이를 해제하도록 Azure Active Directory를 구성하는 방법을 알아봅니다.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: b7cb55b5d14208128c894a007d13f4e73fc54b3b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545809"
---
# <a name="tutorial-configure-zscaler-three-for-automatic-user-provisioning"></a>자습서: Zscaler Three에 대한 자동 사용자 프로비저닝 구성

이 자습서에서는 Zscaler Three에 대한 사용자 및/또는 그룹을 자동으로 프로비저닝하거나 이를 해제하도록 Azure AD(Azure Active Directory)를 구성하는 방법에 대해 알아봅니다.

> [!NOTE]
> 이 자습서에서는 Azure AD 사용자 프로비저닝 서비스에 구축된 커넥터에 대해 설명합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션에 대한 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../active-directory-saas-app-provisioning.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에서 설명하는 단계를 수행하려면 다음이 필요합니다.

* Azure AD 테넌트.
* Zscaler Three 테넌트
* 관리자 권한이 있는 Zscaler Three의 사용자 계정

> [!NOTE]
> Azure AD 프로비저닝 통합에는 엔터프라이즈 계정에서 사용할 수 있는 Zscaler ZSCloud SCIM API가 사용됩니다.

## <a name="adding-zscaler-three-from-the-gallery"></a>갤러리에서 Zscaler Three 추가

Azure AD를 사용하여 사용자를 자동으로 프로비저닝하도록 Zscaler Three를 구성하려면 먼저 Zscaler Three를 Azure AD 애플리케이션 갤러리에서 관리형 SaaS 애플리케이션 목록에 추가해야 합니다.

[Azure Portal](https://portal.azure.com)의 왼쪽 창에서 **Azure Active Directory**를 선택합니다.

![Azure Active Directory 선택](common/select-azuread.png)

**엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.

![Enterprise 애플리케이션](common/enterprise-applications.png)

애플리케이션을 추가하려면 창의 위쪽에서 **새 애플리케이션**을 선택합니다.

![새 애플리케이션 선택](common/add-new-app.png)

검색 상자에서 **Zscaler Three**를 입력합니다. 결과에서 **Zscaler Three**를 선택한 다음, **추가**를 선택합니다.

![결과 목록](common/search-new-app.png)

## <a name="assign-users-to-zscaler-three"></a>Zscaler Three에 사용자 할당

Azure AD 사용자는 먼저 선택한 앱에 대한 액세스 권한을 할당받아야 해당 앱을 사용할 수 있습니다. 자동 사용자 프로비저닝의 컨텍스트에서 Azure AD의 애플리케이션에 할당된 사용자 또는 그룹만 동기화됩니다.

자동 사용자 프로비저닝을 구성하고 사용하도록 설정하려면 먼저 Zscaler Three에 액세스해야 하는 Azure AD의 사용자 및/또는 그룹을 결정해야 합니다. 사용자 및/또는 그룹이 결정되면 [엔터프라이즈 앱에 사용자 또는 그룹 할당](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)의 지침에 따라 이러한 사용자와 그룹을 Zscaler Three에 할당할 수 있습니다.

### <a name="important-tips-for-assigning-users-to-zscaler-three"></a>사용자를 Zscaler Three에 할당하기 위한 주요 팁

* 먼저 단일 Azure AD 사용자를 Zscaler Three에 할당하여 자동 사용자 프로비저닝 구성을 테스트하는 것이 좋습니다. 나중에 더 많은 사용자와 그룹을 할당할 수 있습니다.

* 사용자를 Zscaler Three에 할당할 때 할당 대화 상자에서 유효한 애플리케이션별 역할(사용 가능한 경우)을 선택해야 합니다. **기본 액세스** 역할이 있는 사용자는 프로비전에서 제외됩니다.

## <a name="set-up-automatic-user-provisioning"></a>자동 사용자 프로비저닝 설정

이 섹션에서는 Azure AD의 사용자 및 그룹 할당에 따라 Zscaler Three에서 사용자와 그룹을 만들고, 업데이트하고, 사용 해제하도록 Azure AD 프로비저닝 서비스를 구성하는 단계를 안내합니다.

> [!TIP]
> Zscaler Three에서 SAML 기반 Single Sign-On을 사용하도록 설정하려고 할 수도 있습니다. 이 경우 [Zscaler Three Single Sign-On 자습서](zscaler-three-tutorial.md)의 지침을 따르세요. Single Sign-On은 자동 사용자 프로비저닝과 별개로 구성할 수 있습니다. 하지만 이 두 가지 기능은 서로 보완적입니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고, **엔터프라이즈 애플리케이션** > **모든 애플리케이션** > **Zscaler Three**를 차례로 선택합니다.

    ![Enterprise 애플리케이션](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Zscaler Three**를 선택합니다.

    ![애플리케이션 목록](common/all-applications.png)

3. **프로비저닝** 탭을 선택합니다.

    ![Zscaler Three 프로비저닝](./media/zscaler-three-provisioning-tutorial/provisioning-tab.png)

4. **프로비저닝 모드**를 **자동**으로 설정합니다.

    ![프로비저닝 모드 설정](./media/zscaler-three-provisioning-tutorial/provisioning-credentials.png)

5. **관리자 자격 증명** 섹션에서 다음 단계에서 설명한 대로 Zscaler Three 계정의 **테넌트 URL** 및 **비밀 토큰**을 입력합니다.

6. **테넌트 URL** 및 **비밀 토큰**을 가져오려면 Zscaler Three 포털에서 **관리** > **인증 설정**으로 차례로 이동하여 **인증 유형** 아래에서 **SAML**을 선택합니다.

    ![Zscaler Three 인증 설정](./media/zscaler-three-provisioning-tutorial/secret-token-1.png)

    **SAML 구성**을 선택하여 **SAML 구성** 창을 엽니다.

    ![SAML 창 구성](./media/zscaler-three-provisioning-tutorial/secret-token-2.png)

    **SCIM 기반 프로비저닝 사용**을 선택하고, **기본 URL** 및 **전달자 토큰**을 복사한 다음, 설정을 저장합니다. Azure Portal에서 **기본 URL**을 **테넌트 URL** 상자에 붙여넣고, **전달자 토큰**을 **비밀 토큰** 상자에 붙여넣습니다.

7. **테넌트 URL** 및 **비밀 토큰** 상자에 값이 입력되면 **테스트 연결**을 선택하여 Azure AD에서 Zscaler Three에 연결할 수 있는지 확인합니다. 연결이 실패하면 Zscaler Three 계정에 관리자 권한이 있는지 확인하고 다시 시도합니다.

    ![연결 테스트](./media/zscaler-three-provisioning-tutorial/test-connection.png)

8. **알림 이메일** 상자에서 프로비저닝 오류 알림을 받을 사람 또는 그룹의 이메일 주소를 입력합니다. **오류가 발생할 경우 이메일 알림 보내기**를 선택합니다.

    ![알림 이메일 설정](./media/zscaler-three-provisioning-tutorial/notification.png)

9. **저장**을 선택합니다.

10. **매핑** 섹션에서 **Azure Active Directory 사용자를 Zscaler Three로 동기화**를 선택합니다.

    ![Azure AD 사용자 동기화](./media/zscaler-three-provisioning-tutorial/user-mappings.png)

11. **특성 매핑** 섹션에서 Azure AD에서 Zscaler Three로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 Zscaler Three의 사용자 계정을 일치시키는 데 사용됩니다. 변경 내용을 커밋하려면 **저장**을 선택합니다.

    ![특성 매핑](./media/zscaler-three-provisioning-tutorial/user-attribute-mappings.png)

12. **매핑** 섹션에서 **Azure Active Directory 그룹을 Zscaler Three로 동기화**를 선택합니다.

    ![Azure AD 그룹 동기화](./media/zscaler-three-provisioning-tutorial/group-mappings.png)

13. **특성 매핑** 섹션에서 Azure AD에서 Zscaler Three로 동기화되는 그룹 특성을 검토합니다. **일치** 속성으로 선택된 특성은 업데이트 작업을 위해 Zscaler Three의 그룹을 일치시키는 데 사용됩니다. 변경 내용을 커밋하려면 **저장**을 선택합니다.

    ![특성 매핑](./media/zscaler-three-provisioning-tutorial/group-attribute-mappings.png)

14. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](./../active-directory-saas-scoping-filters.md)의 지침을 참조하세요.

15. Zscaler Three에서 Azure AD 프로비저닝 서비스를 사용하도록 설정하려면 **설정** 섹션에서 **프로비저닝 상태**를 **켜기**로 변경합니다.

    ![프로비전 상태](./media/zscaler-three-provisioning-tutorial/provisioning-status.png)

16. **설정** 섹션의 **범위** 아래에서 원하는 값을 선택하여 Zscaler Three에 프로비저닝하려는 사용자 및/또는 그룹을 정의합니다.

    ![범위 값](./media/zscaler-three-provisioning-tutorial/scoping.png)

17. 프로비저닝할 준비가 되면 **저장**을 선택합니다.

    ![저장 선택](./media/zscaler-three-provisioning-tutorial/save-provisioning.png)

이 작업은 **설정** 섹션의 **범위** 아래에 정의된 모든 사용자 및 그룹의 초기 동기화를 시작합니다. Azure AD 프로비저닝 서비스가 실행되는 동안 초기 동기화는 약 40분마다 수행되는 후속 동기화보다 더 오래 걸립니다. **동기화 세부 정보** 섹션에서 진행률을 모니터링할 수 있습니다. 또한 Zscaler Three의 Azure AD 프로비저닝 서비스에서 수행되는 모든 작업을 설명하는 프로비저닝 활동 보고서에 대한 링크를 따를 수도 있습니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../active-directory-saas-provisioning-reporting.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../active-directory-saas-provisioning-reporting.md).

<!--Image references-->
[1]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-03.png
