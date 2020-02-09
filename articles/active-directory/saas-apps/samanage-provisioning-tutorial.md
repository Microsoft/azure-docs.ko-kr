---
title: '자습서: Azure Active Directory로 자동 사용자 프로비전을 위한 Samanage 구성 | Microsoft Docs'
description: 사용자 계정을 Samanage로 자동으로 프로 비전 및 프로 비전 해제 하도록 Azure Active Directory를 구성 하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 62d0392f-37d4-436e-9aff-22f4e5b83623
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 988efc2087b3b30e6073bd7f6e2cf08f91fd397c
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77060493"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비전을 위한 Samanage 구성

이 자습서에서는 Samanage and Azure Active Directory (Azure AD)에서 수행 하 여 사용자 및 그룹을 Samanage로 자동으로 프로 비전 및 프로 비전 해제 하도록 Azure AD를 구성 하는 단계를 보여 줍니다.

> [!NOTE]
> 이 자습서에서는 Azure AD 사용자 프로 비전 서비스 위에 구축 된 커넥터에 대해 설명 합니다. 이 서비스의 역할, 작동 방법 및 질문과 대답에 대 한 자세한 내용은 [Azure Active Directory를 사용 하 여 SaaS (software as a service) 응용 프로그램에 사용자 프로 비전 및 프로 비전](../app-provisioning/user-provisioning.md)해제를 참조 하세요.

## <a name="prerequisites"></a>필수 조건

이 자습서에 설명 된 시나리오에서는 다음과 같은 것으로 가정 합니다.

* Azure AD 테넌트.
* 전문 패키지가 포함 된 [Samanage 테넌트](https://www.samanage.com/pricing/) .
* 관리자 권한이 있는 Samanage의 사용자 계정

> [!NOTE]
> Azure AD 프로 비전 통합은 [Samanage REST API](https://www.samanage.com/api/)에 의존 합니다. 이 API는 전문 패키지를 사용 하 여 Samanage 개발자가 사용할 수 있습니다.

## <a name="add-samanage-from-the-azure-marketplace"></a>Azure Marketplace에서 Samanage 추가

Azure AD를 사용 하 여 자동 사용자 프로 비전을 위해 Samanage를 구성 하기 전에 Azure Marketplace의 Samanage를 관리 되는 SaaS 응용 프로그램 목록에 추가 합니다.

Marketplace에서 Samanage를 추가 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 탐색 창에서 **Azure Active Directory**를 선택 합니다.

    ![Azure Active Directory 아이콘](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션**을 선택합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **Samanage** 를 입력 하 고 결과 패널에서 **Samanage** 를 선택 합니다. 응용 프로그램을 추가 하려면 **추가**를 선택 합니다.

    ![결과 목록의 Samanage](common/search-new-app.png)

## <a name="assign-users-to-samanage"></a>Samanage에 사용자 할당

Azure Active Directory는 *할당* 이라는 개념을 사용 하 여 선택한 앱에 대 한 액세스 권한을 받아야 하는 사용자를 결정 합니다. 자동 사용자 프로 비전의 컨텍스트에서는 Azure AD의 응용 프로그램에 할당 된 사용자 또는 그룹만 동기화 됩니다.

자동 사용자 프로비저닝을 구성 하 고 사용 하도록 설정 하기 전에 Samanage에 액세스 해야 하는 Azure AD의 사용자 또는 그룹을 결정 합니다. 이러한 사용자 또는 그룹을 Samanage에 할당 하려면 [엔터프라이즈 앱에 사용자 또는 그룹 할당](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)의 지침을 따르세요.

### <a name="important-tips-for-assigning-users-to-samanage"></a>Samanage에 사용자를 할당하기 위한 주요 팁

*    오늘날 Samanage 역할은 Azure Portal UI에서 자동으로 자동으로 채워집니다. Samanage 역할을 사용자에 게 할당 하기 전에 Samanage에 대해 초기 동기화가 완료 되었는지 확인 하 여 Samanage 테넌트의 최신 역할을 검색 합니다.

*    초기 자동 사용자 프로 비전 구성을 테스트 하기 위해 단일 Azure AD 사용자를 Samanage에 할당 하는 것이 좋습니다. 테스트에 성공한 후 나중에 추가 사용자 및 그룹을 할당할 수 있습니다.

*    사용자를 Samanage에 할당할 때 할당 대화 상자에서 유효한 응용 프로그램별 역할 (사용 가능한 경우)을 선택 합니다. **기본 액세스** 역할이 있는 사용자는 프로비전에서 제외됩니다.

## <a name="configure-automatic-user-provisioning-to-samanage"></a>Samanage에 자동 사용자 프로 비전 구성

이 섹션에서는 Azure AD 프로 비전 서비스를 구성 하는 단계를 안내 합니다. Azure AD의 사용자 또는 그룹 할당을 기반으로 Samanage에서 사용자 또는 그룹을 만들고, 업데이트 하 고, 사용 하지 않도록 설정 하는 데 사용 합니다.

> [!TIP]
> Samanage에 대해 SAML 기반 Single Sign-On를 사용 하도록 설정할 수도 있습니다. [Samanage Single Sign-On 자습서](samanage-tutorial.md)의 지침을 따르세요. Single sign-on은 자동 사용자 프로 비전과 독립적으로 구성할 수 있습니다. 하지만이 두 가지 기능은 서로 보완적입니다.

### <a name="configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Azure AD에서 Samanage에 대 한 자동 사용자 프로 비전 구성

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **모든 응용** 프로그램 > **엔터프라이즈 응용 프로그램** > **Samanage**을 선택 합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Samanage**를 선택합니다.

    ![응용 프로그램 목록의 Samanage 링크](common/all-applications.png)

3. **프로비전** 탭을 선택합니다.

    ![Samanage 프로비전](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. **프로비전 모드**를 **자동**으로 설정합니다.

    ![프로 비전 탭](common/provisioning-automatic.png)

5. **관리자 자격 증명** 섹션 아래에서 Samanage **테넌트 URL** 및 **암호 토큰**을 입력 합니다. **연결 테스트** 를 클릭 하 여 Azure AD가 Samanage에 연결할 수 있는지 확인 합니다. 연결에 실패하면 Samanage 계정에 관리자 권한이 있는지 확인하고 다시 시도합니다.

    ![연결 테스트 Samanage](./media/samanage-provisioning-tutorial/provisioning.png)

6. **알림 전자 메일** 상자에 프로 비전 오류 알림을 받을 개인 또는 그룹의 전자 메일 주소를 입력 합니다. **오류가 발생 하면 전자 메일 알림 보내기** 확인란을 선택 합니다.

    ![Samanage 알림 전자 메일](./media/samanage-provisioning-tutorial/EmailNotification.png)

7. **저장**을 선택합니다.

8. **매핑** 섹션에서 **Azure Active Directory 사용자를 Samanage에 동기화**를 선택합니다.

    ![Samanage 사용자 동기화](./media/samanage-provisioning-tutorial/UserMappings.png)

9. **특성 매핑** 섹션에서 Azure AD에서 Samanage로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 Samanage의 사용자 계정을 일치시키는 데 사용됩니다. 변경 내용을 저장 하려면 **저장**을 선택 합니다.

    ![Samanage 일치 사용자 특성](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

10. 그룹 매핑을 사용하려면 **매핑** 섹션에서 **Azure Active Directory 그룹을 Samanage에 동기화**를 선택합니다.

    ![Samanage 그룹 동기화](./media/samanage-provisioning-tutorial/GroupMappings.png)

11. **사용**을 **예**로 설정하여 그룹을 동기화합니다. **특성 매핑** 섹션에서 Azure AD에서 Samanage로 동기화되는 그룹 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 Samanage의 사용자 계정을 일치시키는 데 사용됩니다. 변경 내용을 저장 하려면 **저장**을 선택 합니다.

    ![Samanage 일치 그룹 특성](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. 범위 지정 필터를 구성 하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)의 지침을 따르세요.

13. Samanage에 대 한 Azure AD 프로 비전 서비스를 사용 하도록 설정 하려면 **설정** 섹션에서 **프로 비전 상태** 를 **켜기**로 변경 합니다.

    ![Samanage 프로 비전 상태](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

14. Samanage에 프로 비전 할 사용자 또는 그룹을 정의 합니다. **설정** 섹션의 **범위**에서 원하는 값을 선택 합니다. **모든 사용자 및 그룹 동기화** 옵션을 선택 하는 경우 다음 "커넥터 제한 사항" 섹션에 설명 된 제한 사항을 고려 합니다.

    ![Samanage 범위](./media/samanage-provisioning-tutorial/ScopeSync.png)

15. 프로 비전 할 준비가 되 면 **저장**을 선택 합니다.

    ![Samanage 저장](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


이 작업을 수행 하면 **설정** 섹션의 **범위** 에 정의 된 모든 사용자 또는 그룹의 초기 동기화가 시작 됩니다. 초기 동기화는 이후 동기화 보다 수행 하는 데 더 많은 시간이 걸립니다. Azure AD 프로 비전 서비스가 실행 되는 동안 약 40 분 마다 발생 합니다. 

**동기화 세부 정보** 섹션을 사용 하 여 진행률을 모니터링 하 고 프로 비전 활동 보고서에 대 한 링크를 따라갈 수 있습니다. 이 보고서는 Samanage의 Azure AD 프로 비전 서비스에서 수행 하는 모든 작업을 설명 합니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../app-provisioning/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="connector-limitations"></a>커넥터 제한 사항

**모든 사용자 및 그룹 동기화** 옵션을 선택 하 고 Samanage **roles** 특성에 대 한 값을 구성 하는 경우 **null (옵션)** 상자의 값은 다음 형식으로 표시 되어야 합니다.

- {"displayName": "role"}. 여기서 role은 원하는 기본값입니다.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
