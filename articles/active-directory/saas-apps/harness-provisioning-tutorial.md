---
title: '자습서: Azure Active Directory을 사용 하 여 자동 사용자 프로 비전을 위한 도구 구성 Microsoft Docs'
description: 사용자 계정을 자동으로 프로 비전 및 프로 비전 해제 하도록 Azure Active Directory를 구성 하는 방법에 대해 알아봅니다.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: 56a865de8cb1be079f4935ef2a8f840f10589b26
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88550013"
---
# <a name="tutorial-configure-harness-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로 비전을 위한 도구 구성

이 문서에서는 사용자 또는 그룹을 자동으로 프로 비전 및 프로 비전 해제 하도록 Azure AD (Azure Active Directory)를 구성 하는 방법에 대해 알아봅니다.

> [!NOTE]
> 이 문서에서는 Azure AD 사용자 프로 비전 서비스 위에 구축 된 커넥터에 대해 설명 합니다. 이 서비스에 대 한 중요 정보 및 질문과 대답은 [Azure Active Directory를 사용 하 여 SaaS 응용 프로그램에 대 한 사용자 프로 비전 및 프로 비전](../app-provisioning/user-provisioning.md)해제를 참조 하세요.
>
> 이 커넥터는 현재 미리 보기로 제공되고 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>전제 조건

이 문서에 설명 된 시나리오에서는 사용자에 게 이미 다음과 같은 필수 구성 요소가 있다고 가정 합니다.

* Azure AD 테넌트
* [하네스 테 넌 트](https://harness.io/pricing/)
* *관리자* 권한이 있는 도구의 사용자 계정

## <a name="assign-users-to-harness"></a>사용자를 도구에 할당

Azure Active Directory는 *할당*이라는 개념을 사용하여 어떤 사용자가 선택된 앱에 대한 액세스 권한을 부여받아야 하는지 판단합니다. 자동 사용자 프로 비전의 컨텍스트에서는 Azure AD의 응용 프로그램에 할당 된 사용자 또는 그룹만 동기화 됩니다.

자동 사용자 프로비저닝을 구성 하 고 사용 하도록 설정 하기 전에 Azure AD에서 도구에 액세스 해야 하는 사용자 또는 그룹을 결정 합니다. 그런 다음 [엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)의 지침에 따라 이러한 사용자 또는 그룹을 도구에 할당할 수 있습니다.

## <a name="important-tips-for-assigning-users-to-harness"></a>사용자를 도구에 할당 하기 위한 주요 팁

* 자동 사용자 프로 비전 구성을 테스트 하기 위해 단일 Azure AD 사용자를 도구에 할당 하는 것이 좋습니다. 추가 사용자 또는 그룹은 나중에 할당할 수 있습니다.

* 사용자를 도구에 할당할 때 **할당** 대화 상자에서 유효한 응용 프로그램별 역할 (사용 가능한 경우)을 선택 해야 합니다. *기본 액세스* 역할이 있는 사용자는 프로비전에서 제외됩니다.

## <a name="set-up-harness-for-provisioning"></a>프로 비전을 위한 도구 설정

1. [하네스 관리 콘솔](https://app.harness.io/#/login)에 로그인 한 다음 **지속적인 보안**  >  **액세스 관리**로 이동 합니다.

    ![도구 관리 콘솔](media/harness-provisioning-tutorial/admin.png)

1. **API 키**를 선택 합니다.

    ![도구 API 키 링크](media/harness-provisioning-tutorial/apikeys.png)

1. **API 키 추가**를 선택 합니다. 

    ![도구 API 키 링크 추가](media/harness-provisioning-tutorial/addkey.png)

1. **Api 키 추가** 창에서 다음을 수행 합니다.

    ![도구 Api 키 창 추가](media/harness-provisioning-tutorial/title.png)
   
   a. **이름** 상자에 키의 이름을 입력 합니다.  
   b. 다음 **에서 상속 된 권한** 드롭다운 목록에서 옵션을 선택 합니다. 
   
1. **제출**을 선택합니다.

1. 이 자습서에서 나중에 사용 하기 위해 **키** 를 복사 합니다.

    ![하네스 토큰 만들기](media/harness-provisioning-tutorial/token.png)

## <a name="add-harness-from-the-gallery"></a>갤러리에서 도구 추가

Azure AD를 사용한 자동 사용자 프로 비전을 위한 도구를 구성 하기 전에 Azure AD 응용 프로그램 갤러리의 도구를 관리 되는 SaaS 응용 프로그램 목록에 추가 해야 합니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 창에서 **Azure Active Directory**를 선택 합니다.

    !["Azure Active Directory" 단추](common/select-azuread.png)

1. **엔터프라이즈 응용 프로그램**  >  **모든 응용 프로그램**을 선택 합니다.

    !["모든 애플리케이션" 링크](common/enterprise-applications.png)

1. 새 응용 프로그램을 추가 하려면 창의 위쪽에 있는 **새 응용 프로그램** 단추를 선택 합니다.

    !["새 애플리케이션" 단추](common/add-new-app.png)

1. 검색 상자에 **도구**를 입력 하 고 결과 목록에서 **도구** 를 선택한 다음 **추가** 단추를 선택 하 여 응용 프로그램을 추가 합니다.

    ![결과 목록의 도구](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-harness"></a>자동 사용자 프로 비전을 사용 하도록 구성 

이 섹션에서는 azure ad의 사용자 또는 그룹 할당을 기반으로 하 여 사용자 또는 그룹을 만들고, 업데이트 하 고, 비활성화 하도록 Azure AD 프로 비전 서비스를 구성 하는 단계를 안내 합니다.

> [!TIP]
> [도구 Single Sign-On 자습서](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial)의 지침에 따라 도구에 SAML 기반 Single Sign-On를 사용 하도록 선택할 수도 있습니다. 자동 사용자 프로 비전에 관계 없이 Single Sign-On를 구성할 수 있습니다. 하지만 이러한 두 가지 기능은 서로 보완적입니다.

> [!NOTE]
> SCIM 끝점에 대 한 자세한 내용은 도구 [API 키](https://docs.harness.io/article/smloyragsm-api-keys) 문서를 참조 하세요.

Azure AD에서 도구에 대 한 자동 사용자 프로비저닝을 구성 하려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에서 **엔터프라이즈 응용 프로그램**  >  **모든 응용 프로그램**을 선택 합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

1. 애플리케이션 목록에서 **Harness**를 선택합니다.

    ![응용 프로그램 목록의 도구 링크](common/all-applications.png)

1. **프로 비전**을 선택 합니다.

    ![프로 비전 단추](common/provisioning.png)

1. **프로 비전 모드** 드롭다운 목록에서 **자동**을 선택 합니다.

    !["프로 비전 모드" 드롭다운 목록](common/provisioning-automatic.png)

1. **관리자 자격 증명**아래에서 다음을 수행 합니다.

    ![테넌트 URL + 토큰](common/provisioning-testconnection-tenanturltoken.png)
 
   a. **테 넌 트 URL** 상자에을 입력 **`https://app.harness.io/gateway/api/scim/account/XCPzWkCIQ46ypIu2DeT7yw`** 합니다.  
   b. **비밀 토큰** 상자에 "프로 비전을 위한 도구 설정" 섹션의 6 단계에서 저장 한 Scim 인증 토큰 값을 입력 합니다.  
   다. **연결 테스트** 를 선택 하 여 Azure AD가 하네스에 연결할 수 있는지 확인 합니다. 연결에 실패 하면 사용자의 하네스 계정에 *관리자* 권한이 있는지 확인 한 후 다시 시도 하십시오.

1. **알림 전자 메일** 상자에 프로 비전 오류 알림을 받을 개인 또는 그룹의 전자 메일 주소를 입력 한 다음 **오류가 발생 하면 전자 메일 알림 보내기** 확인란을 선택 합니다.

    !["알림 전자 메일" 상자](common/provisioning-notification-email.png)

1. **저장**을 선택합니다.

1. **매핑**아래에서 **사용자에 게 Azure Active Directory 사용자에 게 동기화를**선택 합니다.

    !["사용자에 게 Azure Active Directory 동기화" 링크를 활용 합니다.](media/harness-provisioning-tutorial/usermappings.png)

1. **특성 매핑**아래에서 Azure AD에서 하네스로 동기화 되는 사용자 특성을 검토 합니다. *일치* 로 선택한 특성은 업데이트 작업을 위해 도구의 사용자 계정을 일치 시키는 데 사용 됩니다. 변경 내용을 커밋하려면 **저장**을 선택합니다.

    ![하네스 사용자 "특성 매핑" 창](media/harness-provisioning-tutorial/userattributes.png)

1. **매핑**에서 **도구에 Azure Active Directory 그룹 동기화를**선택 합니다.

    !["도구에 Azure Active Directory 그룹 동기화" 링크](media/harness-provisioning-tutorial/groupmappings.png)

1. **특성 매핑**아래에서 Azure AD에서 하네스로 동기화 되는 그룹 특성을 검토 합니다. *일치* 속성으로 선택한 특성은 업데이트 작업을 위한 도구 그룹을 일치 시키는 데 사용 됩니다. 변경 내용을 커밋하려면 **저장**을 선택합니다.

    ![도구 그룹 "특성 매핑" 창](media/harness-provisioning-tutorial/groupattributes.png)

1. 범위 지정 필터를 구성 하려면 [범위 지정 필터를 사용한 특성 기반 응용 프로그램 프로 비전](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)을 참조 하세요.

1. **설정**아래에서 도구에 대 한 Azure AD 프로 비전 서비스를 사용 하도록 설정 하려면 **프로 비전 상태** 스위치를 **켜기**로 전환 합니다.

    ![프로 비전 상태 스위치를 "설정"으로 전환](common/provisioning-toggle-on.png)

1. **설정**의 **범위** 드롭다운 목록에서 프로 비전 하는 사용자 또는 그룹을 하네스로 동기화 할 방법을 선택 합니다.

    ![프로비전 범위](common/provisioning-scope.png)

1. 프로비저닝할 준비가 되면 **저장**을 선택합니다.

    ![프로 비전 저장 단추](common/provisioning-configuration-save.png)

이 작업을 수행 하면 프로 비전 하는 사용자 또는 그룹의 초기 동기화가 시작 됩니다. 초기 동기화는 나중에 수행 하는 데 더 많은 시간이 걸립니다. 동기화는 Azure AD 프로 비전 서비스가 실행 되는 동안 약 40 분 마다 발생 합니다. 진행률을 모니터링 하려면 **동기화 세부 정보** 섹션으로 이동 합니다. 프로 비전 활동 보고서에 대 한 링크를 팔 로우 하 여 Azure AD 프로 비전 서비스에서 수행 하는 모든 작업을 제어할 수도 있습니다.

Azure AD 프로 비전 로그를 읽는 방법에 대 한 자세한 내용은 [자동 사용자 계정 프로 비전에 대 한 보고서](../app-provisioning/check-status-user-account-provisioning.md)를 참조 하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).
