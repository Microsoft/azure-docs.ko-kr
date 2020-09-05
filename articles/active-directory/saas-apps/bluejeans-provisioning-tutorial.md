---
title: '자습서: Azure Active Directory로 자동 사용자 프로비전을 위한 BlueJeans 구성 | Microsoft Docs'
description: 사용자 계정을 BlueJeans에 자동으로 프로비전 및 프로비전 해제하도록 Azure Active Directory를 구성하는 방법을 알아봅니다.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 0b90389f6ae88a073f3961cc2f0957d22e190fe0
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545457"
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비전을 위한 BlueJeans 구성

이 자습서에서는 사용자 및/또는 그룹을 BlueJeans로 자동으로 프로비전하고 프로비전을 해제하도록 Azure AD(Azure Active Directory)를 구성하기 위해 BlueJeans 및 Azure AD에서 수행할 단계를 보여 줍니다.

> [!NOTE]
> 이 자습서에서는 Azure AD 사용자 프로비저닝 서비스에 기반하여 구축된 커넥터에 대해 설명합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에서 설명한 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

* Azure AD 테넌트
* [My Company](https://www.BlueJeans.com/pricing)(내 회사) 플랜 또는 그 이상이 사용되도록 설정된 BlueJeans 테넌트
* 관리자 권한이 있는 BlueJeans의 사용자 계정

> [!NOTE]
> Azure AD 프로비전 통합에서는 [BlueJeans API](https://BlueJeans.github.io/developer)를 사용하며, 이 API는 BlueJeans 팀이 표준 플랜 이상에서 사용할 수 있습니다.

## <a name="adding-bluejeans-from-the-gallery"></a>갤러리에서 BlueJeans 추가

Azure AD를 사용하여 자동 사용자 프로비전을 위해 BlueJeans를 구성하기 전에 BlueJeans를 Azure AD 애플리케이션 갤러리에서 관리되는 SaaS 애플리케이션 목록으로 추가해야 합니다.

**Azure AD 애플리케이션 갤러리에서 BlueJeans를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 패널에서 **Azure Active Directory**를 선택 합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 응용 프로그램을 추가 하려면 창의 위쪽에 있는 **새 응용 프로그램** 단추를 선택 합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **BlueJeans**를 입력 하 고, 결과 패널에서 **BlueJeans** 를 선택한 다음, **추가** 단추를 선택 하 여 응용 프로그램을 추가 합니다.

    ![결과 목록의 BlueJeans](common/search-new-app.png)

## <a name="assigning-users-to-bluejeans"></a>BlueJeans에 사용자 할당

Azure Active Directory는 "할당"이라는 개념을 사용하여 어떤 사용자가 선택한 앱에 대한 액세스를 받아야 하는지를 판단합니다. 자동 사용자 프로비저닝의 컨텍스트에서 Azure AD의 애플리케이션에 "할당된" 사용자 및/또는 그룹만 동기화됩니다.

자동 사용자 프로비전을 구성하고 사용하도록 설정하기 전에 BlueJeans에 대한 액세스가 필요한 Azure AD의 사용자 및/또는 그룹을 결정해야 합니다. 일단 결정되면 다음 지침에 따라 이러한 사용자 및/또는 그룹을 BlueJeans에 할당할 수 있습니다.

* [엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-bluejeans"></a>BlueJeans에 사용자를 할당하기 위한 주요 팁

* 자동 사용자 프로비전 구성을 테스트하려면 단일 Azure AD 사용자를 BlueJeans에 할당하는 것이 좋습니다. 추가 사용자 및/또는 그룹은 나중에 할당할 수도 있습니다.

* 사용자를 BlueJeans에 할당할 때 할당 대화 상자에서 유효한 애플리케이션 특정 역할(사용 가능한 경우)을 선택해야 합니다. **기본 액세스** 역할이 있는 사용자는 프로비전에서 제외됩니다.

## <a name="configuring-automatic-user-provisioning-to-bluejeans"></a>BlueJeans에 대한 자동 사용자 프로비전 구성

이 섹션에서는 Azure AD의 사용자 및/또는 그룹 할당에 따라 BlueJeans에서 사용자 및/또는 그룹을 만들고, 업데이트하고, 사용 해제하도록 Azure AD 프로비전 서비스를 구성하는 단계를 설명합니다.

> [!TIP]
> [BlueJeans Single Sign-On 자습서](bluejeans-tutorial.md)에 제공된 지침에 따라 BlueJeans에 SAML 기반 Single Sign-On을 사용하도록 선택할 수도 있습니다. Single Sign-On과 자동 사용자 프로비저닝은 서로 보완적이지만, 별개로 구성할 수 있습니다.

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>Azure AD에서 BlueJeans에 대한 자동 사용자 프로비전을 구성하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com) 에 로그인 하 고 **엔터프라이즈 응용 프로그램**을 선택한 다음 **모든 응용 프로그램**을 선택 하 고 **BlueJeans**를 선택 합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **BlueJeans**를 선택합니다.

    ![애플리케이션 목록의 BlueJeans 링크](common/all-applications.png)

3. **프로비전** 탭을 선택합니다.

    ![BlueJeans 프로비저닝](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningTab.png)

4. **프로비전 모드**를 **자동**으로 설정합니다.

    ![BlueJeans 프로비저닝](./media/bluejeans-provisioning-tutorial/Bluejeans1.png)

5. **관리자 자격 증명** 섹션에 BlueJeans 계정의 **관리자 사용자 이름** 및 **관리자 암호**를 입력합니다. 이러한 값의 예는 다음과 같습니다.

   * **관리자 사용자 이름** 필드에 BlueJeans 테 넌 트의 관리자 계정에 대 한 사용자 이름을 채웁니다. 예: admin@contoso.com.

   * **관리자 암호** 필드에 관리자 사용자 이름에 해당하는 암호를 채웁니다.

6. 5단계에 표시된 필드를 채우면 **연결 테스트**를 클릭하여 Azure AD에서 BlueJeans에 연결할 수 있는지 확인합니다. 연결에 실패하면 BlueJeans 계정에 관리자 권한이 있는지 확인하고 다시 시도합니다.

    ![BlueJeans 프로비저닝](./media/bluejeans-provisioning-tutorial/BluejeansTestConnection.png)

7. **알림 메일** 필드에 프로비저닝 오류 알림을 받을 개인 또는 그룹의 메일 주소를 입력하고, **오류가 발생할 경우, 메일 알림 보내기** 확인란을 선택합니다.

    ![BlueJeans 프로비저닝](./media/bluejeans-provisioning-tutorial/BluejeansNotificationEmail.png)

8. **저장**을 클릭합니다.

9. **매핑** 섹션에서 **Azure Active Directory 사용자를 BlueJeans에 동기화**를 선택합니다.

    ![BlueJeans 프로비저닝](./media/bluejeans-provisioning-tutorial/BluejeansMapping.png)

10. **특성 매핑** 섹션에서는 Azure AD에서 BlueJeans로 동기화된 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 BlueJeans의 사용자 계정을 일치시키는 데 사용됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

    ![BlueJeans 프로비저닝](./media/bluejeans-provisioning-tutorial/BluejeansUserMappingAtrributes.png)

11. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

12. BlueJeans에 대한 Azure AD 프로비전 서비스를 사용하도록 설정하려면 **설정** 섹션에서 **프로비전 상태**를 **켜기**로 변경합니다.

    ![BlueJeans 프로비저닝](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningStatus.png)

13. **설정** 섹션의 **범위**에서 원하는 값을 선택하여 BlueJeans에 프로비전하려는 사용자 및/또는 그룹을 정의합니다.

    ![BlueJeans 프로비저닝](./media/bluejeans-provisioning-tutorial/UserGroupSelection.png)

14. 프로비전할 준비가 되면 **저장**을 클릭합니다.

    ![BlueJeans 프로비저닝](./media/bluejeans-provisioning-tutorial/SaveProvisioning.png)

이 작업은 **설정**의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화를 시작합니다. 초기 동기화는 Azure AD 프로비전 서비스가 실행되는 동안 약 40분마다 발생하는 후속 동기화보다 더 많은 시간이 걸립니다. **동기화 세부 정보** 섹션을 사용하여 진행 상태를 모니터링하고, BlueJeans에서 Azure AD 프로비전 서비스가 수행하는 모든 작업을 설명하는 프로비전 활동 보고서에 대한 링크를 따를 수 있습니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../app-provisioning/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="connector-limitations"></a>커넥터 제한 사항

* Bluejeans는 30자를 초과하는 사용자 이름을 허용하지 않습니다.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).

<!--Image references-->

[1]: ./media/bluejeans-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/bluejeans-tutorial/tutorial_general_03.png
