---
title: '자습서: Azure Active Directory을 사용 하 여 자동 사용자 프로 비전을 위한 Foodee 구성 Microsoft Docs'
description: 사용자 계정을 Foodee로 자동으로 프로 비전 및 프로 비전 해제 하도록 Azure Active Directory를 구성 하는 방법에 대해 알아봅니다.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: 40549fa8880fb4dc4d29046b0037621610f0b130
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544186"
---
# <a name="tutorial-configure-foodee-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로 비전을 위한 Foodee 구성

이 문서에서는 Foodee 및 Azure AD에서 Azure Active Directory (Azure AD)를 구성 하 여 사용자 또는 그룹을 Foodee로 자동으로 프로 비전 하거나 프로 비전 해제 하는 방법을 보여 줍니다.

> [!NOTE]
> 이 문서에서는 Azure AD 사용자 프로 비전 서비스 위에 구축 된 커넥터에 대해 설명 합니다. 이 서비스의 기능 및 작동 원리를 알아보고, 질문과 대답을 보려면 [Azure Active Directory 사용 하 여 SaaS 응용 프로그램에 대 한 사용자 프로 비전 및 프로 비전](../app-provisioning/user-provisioning.md)해제를 참조 하세요.
>
> 이 커넥터는 현재 미리 보기로 제공되고 있습니다. 미리 보기 기능에 대 한 Azure 사용 약관 기능에 대 한 자세한 내용은 [Microsoft Azure 미리 보기의 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에서는 다음과 같은 필수 구성 요소를 충족 한다고 가정 합니다.

* Azure AD 테넌트
* [Foodee 테 넌 트](https://www.food.ee/about/)
* 관리자 권한이 있는 Foodee의 사용자 계정

## <a name="assign-users-to-foodee"></a>Foodee에 사용자 할당 

Azure AD는 *할당* 이라는 개념을 사용 하 여 선택한 앱에 대 한 액세스 권한을 받아야 하는 사용자를 결정 합니다. 자동 사용자 프로 비전의 컨텍스트에서는 Azure AD의 응용 프로그램에 할당 된 사용자 또는 그룹만 동기화 됩니다.

자동 사용자 프로비저닝을 구성 하 고 사용 하도록 설정 하기 전에 Foodee에 액세스 해야 하는 Azure AD의 사용자 또는 그룹을 결정 해야 합니다. 이렇게 결정 한 후에 [는 엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)의 지침에 따라 이러한 사용자 또는 그룹을 Foodee에 할당할 수 있습니다.

## <a name="important-tips-for-assigning-users-to-foodee"></a>Foodee에 사용자를 할당 하기 위한 주요 팁 

사용자를 할당 하는 경우 다음 팁을 염두에 두어야 합니다.

* 자동 사용자 프로 비전의 구성을 테스트 하기 위해 단일 Azure AD 사용자를 Foodee에 할당 하는 것이 좋습니다. 추가 사용자 또는 그룹을 나중에 할당할 수 있습니다.

* Foodee에 사용자를 할당 하는 경우 **할당** 창에서 유효한 응용 프로그램별 역할 (사용 가능한 경우)을 선택 합니다. *기본 액세스* 역할이 있는 사용자는 프로 비전에서 제외 됩니다.

## <a name="set-up-foodee-for-provisioning"></a>프로 비전을 위한 Foodee 설정

Azure AD를 사용 하 여 자동 사용자 프로 비전을 위해 Foodee를 구성 하기 전에 Foodee에서 SCIM (도메인 간 Id 관리) 프로 비전을 위해 시스템을 사용 하도록 설정 해야 합니다.

1. [Foodee](https://www.food.ee/login/)에 로그인 한 다음 테 넌 트 ID를 선택 합니다.

    ![Foodee](media/Foodee-provisioning-tutorial/tenant.png)

1. **Enterprise portal**에서 **Single Sign On**을 선택 합니다.

    ![Foodee Enterprise Portal 왼쪽 창 메뉴](media/Foodee-provisioning-tutorial/scim.png)

1. 나중에 사용 하기 위해 **API 토큰** 상자에서 값을 복사 합니다. Azure Portal에서 Foodee 응용 프로그램의 **프로 비전** 탭에 있는 **비밀 토큰** 상자에 입력 합니다.

    ![Foodee](media/Foodee-provisioning-tutorial/token.png)

## <a name="add-foodee-from-the-gallery"></a>갤러리에서 Foodee 추가

Azure AD를 사용 하 여 자동 사용자 프로 비전을 위한 Foodee를 구성 하려면 Azure AD 응용 프로그램 갤러리의 Foodee를 관리 되는 SaaS 응용 프로그램 목록에 추가 해야 합니다.

Azure AD 응용 프로그램 갤러리에서 Foodee를 추가 하려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 창에서 **Azure Active Directory**를 선택 합니다.

    ![Azure Active Directory 명령](common/select-azuread.png)

1. **엔터프라이즈 응용 프로그램**  >  **모든 응용 프로그램**을 선택 합니다.

    ![엔터프라이즈 애플리케이션 창](common/enterprise-applications.png)

1. 새 응용 프로그램을 추가 하려면 창 위쪽에서 **새 응용 프로그램** 을 선택 합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

1. 검색 상자에 **Foodee**를 입력 하 고, 결과 창에서 **Foodee** 를 선택한 다음, **추가** 를 선택 하 여 응용 프로그램을 추가 합니다.

    ![결과 목록의 Foodee](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-foodee"></a>Foodee에 자동 사용자 프로 비전 구성 

이 섹션에서는 azure ad의 사용자 또는 그룹 할당을 기반으로 Foodee에서 사용자 또는 그룹을 만들고, 업데이트 하 고, 비활성화 하도록 Azure AD 프로 비전 서비스를 구성 합니다.

> [!TIP]
> [Foodee Single Sign-On 자습서](Foodee-tutorial.md)의 지침에 따라 Foodee에 대해 SAML 기반 Single Sign-On를 사용 하도록 설정할 수도 있습니다. 자동 사용자 프로 비전에 관계 없이 Single Sign-On를 구성할 수 있습니다. 하지만이 두 가지 기능은 서로 보완적입니다.

다음을 수행 하 여 Azure AD에서 Foodee에 대 한 자동 사용자 프로 비전을 구성 합니다.

1. [Azure Portal](https://portal.azure.com)에서 **엔터프라이즈 응용 프로그램**  >  **모든 응용 프로그램**을 선택 합니다.

    ![Enterprise 애플리케이션 창](common/enterprise-applications.png)

1. **응용 프로그램** 목록에서 **Foodee**를 선택 합니다.

    ![응용 프로그램 목록의 Foodee 링크](common/all-applications.png)

1. **프로비전** 탭을 선택합니다.

    ![프로비저닝 탭](common/provisioning.png)

1. **프로 비전 모드** 드롭다운 목록에서 **자동**을 선택 합니다.

    ![프로비저닝 탭](common/provisioning-automatic.png)

1. **관리자 자격 증명**아래에서 다음을 수행 합니다.

   a. **테 넌 트 URL** 상자에 이전에 검색 한 **https: \/ /concierge.food.ee/scim/v2** 값을 입력 합니다.

   b. **비밀 토큰** 상자에 이전에 검색 한 **API 토큰** 값을 입력 합니다.
   
   다. Azure AD가 Foodee에 연결할 수 있는지 확인 하려면 **연결 테스트**를 선택 합니다. 연결에 실패 하면 Foodee 계정에 관리자 권한이 있는지 확인 한 후 다시 시도 하십시오.

    ![연결 테스트 링크](common/provisioning-testconnection-tenanturltoken.png)

1. **알림 전자 메일** 상자에 프로 비전 오류 알림을 받을 개인 또는 그룹의 전자 메일 주소를 입력 한 다음 **오류가 발생 하면 전자 메일 알림 보내기** 확인란을 선택 합니다.

    ![알림 전자 메일 입력란](common/provisioning-notification-email.png)

1. **저장**을 선택합니다.

1. **매핑**아래에서 **Foodee에 사용자 Azure Active Directory 동기화를**선택 합니다.

    ![Foodee 사용자 매핑](media/Foodee-provisioning-tutorial/usermapping.png)

1. **특성 매핑**아래에서 Azure AD에서 Foodee로 동기화 되는 사용자 특성을 검토 합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 Foodee의 *사용자 계정을* 일치 시키는 데 사용 됩니다. 

    ![Foodee 사용자 매핑](media/Foodee-provisioning-tutorial/userattribute.png)

1. 변경 내용을 커밋하려면 **저장**을 선택 합니다.
1. **매핑**아래에서 **Foodee에 Azure Active Directory 그룹 동기화를**선택 합니다.

    ![Foodee 사용자 매핑](media/Foodee-provisioning-tutorial/groupmapping.png)

1. **특성 매핑**아래에서 Azure AD에서 Foodee로 동기화 되는 사용자 특성을 검토 합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 Foodee의 *그룹 계정을* 일치 시키는 데 사용 됩니다.

    ![Foodee 사용자 매핑](media/Foodee-provisioning-tutorial/groupattribute.png)

1. 변경 내용을 커밋하려면 **저장**을 선택 합니다.
1. 범위 지정 필터를 구성 합니다. 방법에 대 한 자세한 내용은 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)의 지침을 참조 하세요.

1. Foodee에 대 한 Azure AD 프로 비전 서비스를 사용 하도록 설정 하려면 **설정** 섹션에서 **프로 비전 상태** 를 **켜기**로 변경 합니다.

    ![프로 비전 상태 전환](common/provisioning-toggle-on.png)

1. **설정**의 **범위** 드롭다운 목록에서 Foodee에 프로 비전 하려는 사용자 또는 그룹을 정의 합니다.

    ![프로 비전 범위 드롭다운 목록](common/provisioning-scope.png)

1. 프로비저닝할 준비가 되면 **저장**을 선택합니다.

    ![프로 비전 구성 저장 단추](common/provisioning-configuration-save.png)

이전 작업은 **범위** 드롭다운 목록에서 정의한 사용자 또는 그룹의 초기 동기화를 시작 합니다. 초기 동기화는 후속 동기화 보다 수행 하는 데 더 많은 시간이 걸립니다. 자세한 내용은 [사용자를 프로 비전 하](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)는 데 얼마나 걸립니까?를 참조 하세요.

**현재 상태** 섹션을 사용 하 여 진행률을 모니터링 하 고 프로 비전 활동 보고서에 대 한 링크를 따라갈 수 있습니다. 이 보고서는 Foodee의 Azure AD 프로 비전 서비스에서 수행 하는 모든 작업을 설명 합니다. 자세한 내용은 [사용자 프로비저닝 상태 확인](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)을 참조하세요. Azure AD 프로 비전 로그를 읽으려면 [자동 사용자 계정 프로 비전에 대 한 보고](../app-provisioning/check-status-user-account-provisioning.md)를 참조 하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).
