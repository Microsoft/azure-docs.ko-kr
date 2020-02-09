---
title: '자습서: Azure Active Directory을 사용 하 여 자동 사용자 프로 비전을 위한 Smartsheet 구성 Microsoft Docs'
description: 사용자 계정을 Smartsheet에 자동으로 프로 비전 및 프로 비전 해제 하도록 Azure Active Directory를 구성 하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: 9fbdf8a1c4b1881fc6dfd9d7b95a4103761e9ce7
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063206"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로 비전을 위한 Smartsheet 구성

이 자습서에서는 사용자 및/또는 그룹을 Smartsheet로 자동으로 프로 비전 및 프로 비전 해제 하도록 Azure AD를 구성 하기 위해 Smartsheet 및 Azure Active Directory (Azure AD)에서 수행 하는 단계를 보여 줍니다.

> [!NOTE]
> 이 자습서에서는 Azure AD 사용자 프로비저닝 서비스에 기반하여 구축된 커넥터에 대해 설명합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)를 참조하세요.
>
> 이 커넥터는 현재 공개 미리 보기로 있습니다. 미리 보기 기능의 Microsoft Azure 일반 사용 약관에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 조건](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* Azure AD 테넌트
* [Smartsheet 테넌트](https://www.smartsheet.com/pricing)
* 시스템 관리자 권한이 있는 Smartsheet Enterprise 또는 Enterprise 프리미어 계획의 사용자 계정

## <a name="assign-users-to-smartsheet"></a>Smartsheet에 사용자 할당

Azure Active Directory는 *할당* 이라는 개념을 사용 하 여 선택한 앱에 대 한 액세스 권한을 받아야 하는 사용자를 결정 합니다. 자동 사용자 프로 비전의 컨텍스트에서는 Azure AD의 응용 프로그램에 할당 된 사용자 및/또는 그룹만 동기화 됩니다.

자동 사용자 프로비저닝을 구성 하 고 사용 하도록 설정 하기 전에 Azure AD의 사용자 및/또는 그룹에 Smartsheet에 대 한 액세스가 필요한 지 결정 해야 합니다. 일단 결정 되 면 다음 지침에 따라 이러한 사용자 및/또는 그룹을 Smartsheet에 할당할 수 있습니다.

* [엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-smartsheet"></a>Smartsheet에 사용자를 할당 하기 위한 주요 팁

* 자동 사용자 프로 비전 구성을 테스트 하려면 단일 Azure AD 사용자를 Smartsheet에 할당 하는 것이 좋습니다. 추가 사용자 및/또는 그룹은 나중에 할당할 수도 있습니다.

* 사용자를 Smartsheet에 할당할 때 할당 대화 상자에서 유효한 응용 프로그램별 역할 (사용 가능한 경우)을 선택 해야 합니다. **기본 액세스** 역할이 있는 사용자는 프로비전에서 제외됩니다.

* Smartsheet와 Azure AD 간의 사용자 역할 할당에서 패리티를 보장 하려면 전체 Smartsheet 사용자 목록에 채워진 동일한 역할 할당을 활용 하는 것이 좋습니다. Smartsheet에서이 사용자 목록을 검색 하려면 **계정 관리자 > 사용자 관리 > 기타 작업 > 사용자 목록 다운로드 (csv)** 로 이동 합니다.

* 앱의 특정 기능에 액세스 하려면 Smartsheet에 사용자에 게 여러 역할이 있어야 합니다. Smartsheet의 사용자 유형 및 권한에 대 한 자세한 내용은 [사용자 유형 및 권한](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions)을 참조 하세요.

*  사용자가 Smartsheet에 여러 역할을 할당 하는 경우 사용자가 Smartsheet 개체에 영구적으로 액세스할 수 없는 시나리오를 방지 하기 위해 이러한 역할 할당이 Azure AD에 복제 되었는지 확인 **해야 합니다** . Smartsheet의 각 고유 역할은 Azure AD의 다른 그룹에 할당 되어야 **합니다** . 그런 다음 원하는 역할에 해당 하는 각 그룹에 사용자를 추가 **해야** 합니다. 

## <a name="set-up-smartsheet-for-provisioning"></a>프로 비전을 위한 Smartsheet 설정

Azure AD를 사용 하 여 자동 사용자 프로 비전을 위해 Smartsheet를 구성 하기 전에 Smartsheet에서 SCIM 프로 비전을 사용 하도록 설정 해야 합니다.

1. **[Smartsheet 포털](https://app.smartsheet.com/b/home)** 에서 **SysAdmin** 으로 로그인 하 고 **계정 관리자**로 이동 합니다.

    ![Smartsheet 계정 관리자](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. **사용자 자동 프로 비전 > 편집 > 보안 제어**로 이동 합니다.

    ![Smartsheet 보안 컨트롤](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Azure AD에서 Smartsheet로 프로 비전 하려는 사용자의 전자 메일 도메인을 추가 하 고 유효성을 검사 합니다. **사용 안** 함을 선택 하 여 모든 프로 비전 작업이 azure ad 에서만 시작 되도록 하 고 smartsheet 사용자 목록이 azure ad 할당과 동기화 되도록 합니다.

    ![Smartsheet 사용자 프로 비전](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. 유효성 검사가 완료 되 면 도메인을 활성화 해야 합니다. 

    ![Smartsheet 도메인 활성화](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. **앱 및 통합**으로 이동 하 여 Azure AD로 자동 사용자 프로 비전을 구성 하는 데 필요한 **비밀 토큰** 을 생성 합니다.

    ![Smartsheet 설치](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. **API 액세스**를 선택 합니다. **새 액세스 토큰 생성**을 클릭 합니다.

    ![Smartsheet 설치](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. API 액세스 토큰의 이름을 정의 합니다. **확인**을 클릭합니다.

    ![Smartsheet 설치](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. API 액세스 토큰을 복사 하 고이를 볼 수 있는 유일한 시간으로 저장 합니다. Azure AD의 **비밀 토큰** 필드에 필요 합니다.

    ![Smartsheet 토큰](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="add-smartsheet-from-the-gallery"></a>갤러리에서 Smartsheet 추가

Azure AD를 사용 하 여 자동 사용자 프로비저닝을 위한 Smartsheet를 구성 하려면 Azure AD 응용 프로그램 갤러리의 Smartsheet를 관리 되는 SaaS 응용 프로그램 목록에 추가 해야 합니다.

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 패널에서 **Azure Active Directory**를 선택 합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 응용 프로그램을 추가 하려면 창의 위쪽에 있는 **새 응용 프로그램** 단추를 선택 합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **Smartsheet**를 입력 하 고 결과 패널에서 **smartsheet** 를 선택 합니다. 

    ![결과 목록의 smartsheet](common/search-new-app.png)

5. Smartsheet의 로그인 페이지로 리디렉션하는 **smartsheet에 등록** 단추를 선택 합니다. 

    ![Smartsheet OIDC Add](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-add.png)

6. Smartsheet는 OpenIDConnect 앱 이므로 Microsoft 회사 계정을 사용 하 여 Smartsheet에 로그인 하도록 선택 합니다.

    ![Smartsheet OIDC 로그인](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-login.png)

7. 인증에 성공 하면 동의 페이지에 대 한 동의 확인 프롬프트를 수락 합니다. 그러면 응용 프로그램이 테넌트에 자동으로 추가 되 고 Smartsheet 계정으로 리디렉션됩니다.

    ![Smartsheet OIDc 동의](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-consent.png)

## <a name="configure-automatic-user-provisioning-to-smartsheet"></a>Smartsheet에 자동 사용자 프로 비전 구성 

이 섹션에서는 azure ad의 사용자 및/또는 그룹 할당에 따라 Smartsheet에서 사용자 및/또는 그룹을 만들고, 업데이트 하 고, 비활성화 하도록 Azure AD 프로 비전 서비스를 구성 하는 단계를 안내 합니다.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Azure AD에서 Smartsheet에 대해 자동 사용자 프로 비전을 구성 하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 응용 프로그램**을 선택한 다음 **모든 응용 프로그램**을 선택 합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 응용 프로그램 목록에서 **Smartsheet**를 선택 합니다.

    ![응용 프로그램 목록의 Smartsheet 링크](common/all-applications.png)

3. **프로비전** 탭을 선택합니다.

    ![프로 비전 탭](common/provisioning.png)

4. **프로비전 모드**를 **자동**으로 설정합니다.

    ![프로 비전 탭](common/provisioning-automatic.png)

5. **관리자 자격 증명** 섹션 아래에 있는 **테넌트 URL**에 `https://scim.smartsheet.com/v2/`을 입력 합니다. **비밀 토큰**의 smartsheet에서 이전에 검색 하 고 저장 한 값을 입력 합니다. **연결 테스트** 를 클릭 하 여 Azure AD가 smartsheet에 연결할 수 있는지 확인 합니다. 연결에 실패 하는 경우 Smartsheet 계정에 SysAdmin 권한이 있는지 확인 한 후 다시 시도 하십시오.

    ![토큰](common/provisioning-testconnection-tenanturltoken.png)

6. **알림 메일** 필드에 프로비저닝 오류 알림을 받을 개인 또는 그룹의 메일 주소를 입력하고, **오류가 발생할 경우, 메일 알림 보내기** 확인란을 선택합니다.

    ![알림 이메일](common/provisioning-notification-email.png)

7. **저장**을 클릭합니다.

8. **매핑** 섹션에서 **사용자 Azure Active Directory 사용자를 Smartsheet에 동기화를**선택 합니다.

    ![Smartsheet 사용자 매핑](media/smartsheet-provisioning-tutorial/smartsheet-user-mappings.png)

9. **특성 매핑** 섹션에서 Azure AD에서 smartsheet로 동기화 되는 사용자 특성을 검토 합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 smartsheet의 사용자 계정을 일치 시키는 데 사용 됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

    ![Smartsheet 사용자 특성](media/smartsheet-provisioning-tutorial/smartsheet-user-attributes.png)

10. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

11. Smartsheet에 대해 Azure AD 프로 비전 서비스를 사용 하도록 설정 하려면 **설정** 섹션에서 **프로 비전 상태** 를 **켜기** 로 변경 합니다.

    ![프로비전 상태 켜기로 전환](common/provisioning-toggle-on.png)

12. **설정** 섹션의 **범위** 에서 원하는 값을 선택 하 여 smartsheet에 프로 비전 하려는 사용자 및/또는 그룹을 정의 합니다.

    ![프로비전 범위](common/provisioning-scope.png)

13. 프로비전할 준비가 되면 **저장**을 클릭합니다.

    ![프로비전 구성 저장](common/provisioning-configuration-save.png)

이 작업은 **설정**의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화를 시작합니다. 초기 동기화는 Azure AD 프로비전 서비스가 실행되는 동안 약 40분마다 발생하는 후속 동기화보다 더 많은 시간이 걸립니다. **동기화 세부 정보** 섹션을 사용 하 여 진행률을 모니터링 하 고 프로 비전 활동 보고서에 대 한 링크를 따라 Smartsheet의 Azure AD 프로 비전 서비스에서 수행 하는 모든 작업을 설명 합니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../app-provisioning/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="connector-limitations"></a>커넥터 제한 사항

* Smartsheet는 일시 삭제를 지원 하지 않습니다. 사용자의 **활성** 특성이 False로 설정 된 경우 smartsheet는 사용자를 영구적으로 삭제 합니다.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).
