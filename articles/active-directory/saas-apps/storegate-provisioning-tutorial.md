---
title: '자습서: Azure Active Directory을 사용 하 여 자동 사용자 프로 비전을 위한 자동 사용자 게이트 구성 Microsoft Docs'
description: 사용자 계정을 자동으로 프로 비전 및 프로 비전 해제 하도록 Azure Active Directory를 구성 하는 방법에 대해 알아봅니다.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 10/15/2019
ms.author: Zhchia
ms.openlocfilehash: c889b2c7b11c5e649045e34cdac9e50a3a242b6a
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88524456"
---
# <a name="tutorial-configure-storegate-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로 비전을 위한 자동 사용자 게이트 구성

이 자습서에서는 사용자 및/또는 그룹을 자동으로 프로 비전 하 고 프로 비전 할 수 있도록 Azure AD를 구성 하 여 사용자 및/또는 그룹을 자동으로 프로 비전 및 프로 비전 해제 하도록 Azure AD (Azure Active Directory)에서 수행 하는 단계를 보여 줍니다.

> [!NOTE]
> 이 자습서에서는 Azure AD 사용자 프로비저닝 서비스에 기반하여 구축된 커넥터에 대해 설명합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)를 참조하세요.
>
> 이 커넥터는 현재 공개 미리 보기로 있습니다. 미리 보기 기능의 Microsoft Azure 일반 사용 약관에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 조건](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* Azure AD 테넌트
* [트 게이트 테 넌 트](https://www.storegate.com)
* 관리자 권한이 있는 사용자 계정에 대 한 사용자 계정입니다.

## <a name="assign-users-to-storegate"></a>사용자를 사용자에 게 할당 합니다.

Azure Active Directory는 할당이라는 개념을 사용하여 어떤 사용자가 선택된 앱에 대한 액세스 권한을 부여받아야 하는지 판단합니다. 자동 사용자 프로 비전의 컨텍스트에서는 Azure AD의 응용 프로그램에 할당 된 사용자 및/또는 그룹만 동기화 됩니다.

자동 사용자 프로비저닝을 구성 하 고 사용 하도록 설정 하기 전에 Azure AD의 사용자 및/또는 그룹에 대 한 액세스 권한이 필요한 사용자 및/또는 그룹을 결정 해야 합니다. 일단 결정 되 면 다음 지침에 따라 이러한 사용자 및/또는 그룹을 할당할 수 있습니다.

* [엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-storegate"></a>사용자를 사용자에 게 할당 하기 위한 주요 팁

* 자동 사용자 프로 비전 구성을 테스트 하기 위해 단일 Azure AD 사용자를 자동 사용자 게이트에 할당 하는 것이 좋습니다. 추가 사용자 및/또는 그룹은 나중에 할당할 수도 있습니다.

* 사용자를 할당 하는 데 사용자를 할당 하는 경우 할당 대화 상자에서 유효한 응용 프로그램별 역할 (사용 가능한 경우)을 선택 해야 합니다. **기본 액세스** 역할이 있는 사용자는 프로비전에서 제외됩니다.

## <a name="set-up-storegate-for-provisioning"></a>프로 비전을 위한 작업 모음 설정

Azure AD를 사용 하 여 자동 사용자 프로 비전을 위한 자동 사용자 게이트를 구성 하기 전에, 사용자 게이트 Gate에서 일부 프로 비전 정보를 검색 해야 합니다.

1. 사용자 컴퓨터 [게이트 관리 콘솔](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) 에 로그인 하 고 오른쪽 위 모서리의 사용자 아이콘을 클릭 하 고 **계정 설정**을 선택 하 여 설정으로 이동 합니다.

    ![을 (를) 추가 SCIM 추가](media/storegate-provisioning-tutorial/admin.png)

2. 설정 내에서 **팀 > 설정** 으로 이동 하 고 **Single sign-on** 섹션에서 토글 스위치가 켜져 있는지 확인 합니다.

    ![에 어 게이트 설정](media/storegate-provisioning-tutorial/team.png)

    ![가 나 게이트 설정/해제 단추](media/storegate-provisioning-tutorial/sso.png)

3. **테 넌 트 URL** 및 **토큰**을 복사 합니다. 이러한 값은 Azure Portal에 있는 사용자 용 사용자 게이트 응용 프로그램의 프로 비전 탭에 각각 **테 넌 트 URL** 및 **암호 토큰** 필드에 입력 됩니다. 

    ![을 (를) gate 토큰 만들기](media/storegate-provisioning-tutorial/token.png)

## <a name="add-storegate-from-the-gallery"></a>갤러리에서 나이 게이트 추가

Azure AD를 사용한 자동 사용자 프로 비전을 위한 자동 사용자 게이트를 구성 하려면 Azure AD 응용 프로그램 갤러리의 자동 관리 게이트를 관리 되는 SaaS 응용 프로그램 목록에 추가 해야 합니다.

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 패널에서 **Azure Active Directory**를 선택 합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 응용 프로그램을 추가 하려면 창의 위쪽에 있는 **새 응용 프로그램** 단추를 선택 합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에서 창 고를 **입력 하 고 결과**패널에서 창 고 **를 선택 합니다** . 

    ![결과 목록에 있는 나이 게이트](common/search-new-app.png)

5. 사용자 게이트 성문 로그인 페이지로 리디렉션하는 사용자 **용 사용자 게이트에 등록** 단추를 선택 합니다. 

    ![을 (를) gate 추가](media/storegate-provisioning-tutorial/signup.png)

6.  사용자 컴퓨터 [게이트 관리 콘솔](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) 에 로그인 하 고 오른쪽 위 모서리의 사용자 아이콘을 클릭 하 고 **계정 설정**을 선택 하 여 설정으로 이동 합니다.

    ![나 게이트 로그인](media/storegate-provisioning-tutorial/admin.png)

7. 설정 내에서 **팀 > 설정** 으로 이동 하 고 Single sign-on 섹션에서 토글 스위치를 클릭 하면 동의 흐름이 시작 됩니다. **활성화**를 클릭 합니다.

    ![팀 단체 팀](media/storegate-provisioning-tutorial/team.png)

    ![을 (를) 게이트 sso](media/storegate-provisioning-tutorial/sso.png)

    ![가 나 게이트 활성화](media/storegate-provisioning-tutorial/activate.png)

8. 사용자 게이트 Gate는 OpenIDConnect 앱 이므로 Microsoft 회사 계정을 사용 하 여 동료에 게 로그인 하도록 선택 합니다.

    ![을 (를) 게이트 OIDC 로그인](media/storegate-provisioning-tutorial/login.png)

9. 인증에 성공 하면 동의 페이지에 대 한 동의 확인 프롬프트를 수락 합니다. 그러면 응용 프로그램이 테 넌 트에 자동으로 추가 되 고 사용자가 사용자 게이트 게이트 계정으로 리디렉션됩니다.

    ![%에 게이트 OIDc 동의](media/storegate-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-storegate"></a>자동 사용자 프로 비전 구성 

이 섹션에서는 azure ad의 사용자 및/또는 그룹 할당을 기반으로 하는 사용자 및/또는 그룹을 만들고, 업데이트 하 고, 사용 하지 않도록 Azure AD 프로 비전 서비스를 구성 하는 단계를 안내 합니다.

> [!NOTE]
> 대화 기능 게이트의 SCIM 끝점에 대 한 자세한 내용은 [다음](https://en-support.storegate.com/article/step-by-step-instruction-how-to-enable-azure-provisioning-to-your-storegate-team-account/)을 참조 하세요.

### <a name="to-configure-automatic-user-provisioning-for-storegate-in-azure-ad"></a>Azure AD에서 자동 사용자 게이트에 대 한 자동 사용자 프로 비전을 구성 하려면

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 애플리케이션**, **모든 애플리케이션**을 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 응용 프로그램 목록에서 그룹 **게이트**를 선택 합니다.

    ![응용 프로그램 목록의 [응용 프로그램] 링크](common/all-applications.png)

3. **프로비전** 탭을 선택합니다.

    ![프로비저닝 탭](common/provisioning.png)

4. **프로비전 모드**를 **자동**으로 설정합니다.

    ![프로비저닝 탭](common/provisioning-automatic.png)

5. **관리자 자격 증명** 섹션 아래에 `https://dialpad.com/scim` 있는 **테 넌 트 URL**에 입력 합니다. **비밀 토큰**의 저장 된 사용자 게이트에서 이전에 검색 하 고 저장 한 값을 입력 합니다. **연결 테스트** 를 클릭 하 여 Azure AD가가 나 게이트에 연결할 수 있는지 확인 합니다. 연결에 실패 하면 사용자의 사용자 게이트 계정에 관리자 권한이 있는지 확인 하 고 다시 시도 하세요.

    ![테넌트 URL + 토큰](common/provisioning-testconnection-tenanturltoken.png)

6. **알림 메일** 필드에 프로비저닝 오류 알림을 받을 개인 또는 그룹의 메일 주소를 입력하고, **오류가 발생할 경우, 메일 알림 보내기** 확인란을 선택합니다.

    ![알림 이메일](common/provisioning-notification-email.png)

7. **저장**을 클릭합니다.

8. **매핑** 섹션 아래에서 **사용자 Azure Active Directory 사용자에 게 동기화를**선택 합니다.

    ![사용자 매핑 사용자 매핑](media/storegate-provisioning-tutorial/usermappings.png)

9. **특성 매핑** 섹션에서 Azure AD에서 대상 사용자 게이트로 동기화 되는 사용자 특성을 검토 합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 사용자 이름 게이트에서 사용자 계정을 일치 시키는 데 사용 됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

    ![사용자 게이트 게이트 사용자 특성](media/storegate-provisioning-tutorial/userattributes.png)

10. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

11. Azure AD 프로 비전 서비스를 사용 하도록 설정 하려면 **설정** 섹션에서 **프로 비전 상태** 를 **켜기** 로 변경 합니다.

    ![프로비전 상태 켜기로 전환](common/provisioning-toggle-on.png)

12. **설정** 섹션의 **범위** 에서 원하는 값을 선택 하 여 프로 비전 하려는 사용자 및/또는 그룹을 정의 합니다.

    ![프로비전 범위](common/provisioning-scope.png)

13. 프로비전할 준비가 되면 **저장**을 클릭합니다.

    ![프로비전 구성 저장](common/provisioning-configuration-save.png)

이 작업은 **설정**의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화를 시작합니다. 초기 동기화는 Azure AD 프로비전 서비스가 실행되는 동안 약 40분마다 발생하는 후속 동기화보다 더 많은 시간이 걸립니다. **동기화 세부 정보** 섹션을 사용 하 여 진행률을 모니터링 하 고 프로 비전 활동 보고서에 대 한 링크를 팔 로우 하 여 Azure AD 프로 비전 서비스에서 수행 하는 모든 작업을이에 대해 설명 합니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../app-provisioning/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).
