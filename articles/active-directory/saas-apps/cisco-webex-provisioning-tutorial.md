---
title: '자습서: Azure Active Directory를 사용한 자동 사용자 프로비저닝을 위한 Cisco Webex 구성 | Microsoft Docs'
description: 사용자 계정을 Cisco Webex에 자동으로 프로비전 및 프로비전 해제하도록 Azure Active Directory를 구성하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: 0075783c049e7f48645f768026dd9d5ec0ead821
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77058519"
---
# <a name="tutorial-configure-cisco-webex-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비저닝을 위한 Cisco Webex 구성

이 자습서에서는 사용자를 Cisco Webex에 자동으로 프로비전 및 프로비전 해제하도록 Azure AD(Azure Active Directory)를 구성하기 위해 Cisco Webex 및 Azure AD에서 수행하는 단계를 보여줍니다.

> [!NOTE]
> 이 자습서에서는 Azure AD 사용자 프로비저닝 서비스에 기반하여 구축된 커넥터에 대해 설명합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)를 참조하세요.
>
> 이 커넥터는 현재 미리 보기로 제공됩니다. 미리 보기 기능의 Microsoft Azure 일반 사용 약관에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 조건](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* Azure AD 테넌트.
* [Cisco Webex 테넌트](https://www.webex.com/pricing/index.html)
* 관리자 권한이 있는 Cisco Webex의 사용자 계정

## <a name="adding-cisco-webex-from-the-gallery"></a>갤러리에서 Cisco Webex 추가

Azure AD를 사용하여 사용자를 자동으로 프로비전하도록 Cisco Webex를 구성하기 전에, 먼저 Cisco Webex를 Azure AD 애플리케이션 갤러리에서 관리되는 SaaS 애플리케이션 목록에 추가해야 합니다.

**Azure AD 애플리케이션 갤러리에서 Cisco Webex를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에서 **Cisco Webex**를 입력하고, 결과 패널에서 **Cisco Webex**를 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

    ![결과 목록의 Cisco Webex](common/search-new-app.png)

## <a name="assigning-users-to-cisco-webex"></a>Cisco Webex에 사용자 할당

Azure Active Directory는 "할당"이라는 개념을 사용하여 어떤 사용자가 선택한 앱에 대한 액세스를 받아야 하는지를 판단합니다. 자동 사용자 프로비저닝의 컨텍스트에서 Azure AD의 애플리케이션에 "할당된" 사용자 및/또는 그룹만 동기화됩니다.

자동 사용자 프로비저닝을 구성하고 사용하도록 설정하기 전에, 먼저 Cisco Webex에 액세스해야 하는 Azure AD의 사용자를 결정해야 합니다. 사용자가 결정되면 다음 지침에 따라 해당 사용자를 Cisco Webex에 할당할 수 있습니다.

* [엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-webex"></a>Cisco Webex에 사용자를 할당하기 위한 주요 팁

* 단일 Azure AD 사용자를 Cisco Webex에 할당하여 자동 사용자 프로비저닝 구성을 테스트하는 것이 좋습니다. 추가 사용자를 나중에 할당할 수 있습니다.

* 사용자를 Cisco Webex에 할당할 때 할당 대화 상자에서 유효한 애플리케이션별 역할(사용 가능한 경우)을 선택해야 합니다. **기본 액세스** 역할이 있는 사용자는 프로비전에서 제외됩니다.

## <a name="configuring-automatic-user-provisioning-to-cisco-webex"></a>Cisco Webex에 자동 사용자 프로비저닝 구성

이 섹션에서는 Azure AD의 사용자 할당에 따라 Cisco Webex에서 사용자를 만들고, 업데이트하고, 사용 해제하도록 Azure AD 프로비전 서비스를 구성하는 단계를 안내합니다.

### <a name="to-configure-automatic-user-provisioning-for-cisco-webex-in-azure-ad"></a>Azure AD에서 Cisco Webex에 대한 자동 사용자 프로비저닝을 구성하려면

1. [Azure Portal](https://portal.azure.com)에 로그인하고 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **Cisco Webex**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Cisco Webex**를 선택합니다.

    ![애플리케이션 목록의 Cisco Webex 링크](common/all-applications.png)

3. **프로비전** 탭을 선택합니다.

    ![Cisco Webex 프로비전](common/provisioning.png)

4. **프로비전 모드**를 **자동**으로 설정합니다.

    ![Cisco Webex 프로비전](common/provisioning-automatic.png)

5. **관리자 자격 증명** 섹션 아래에서 Cisco Webex 계정의 **테넌트 URL** 및 **비밀 토큰**을 입력합니다.

    ![Cisco Webex 프로비전](./media/cisco-webex-provisioning-tutorial/secrettoken1.png)

6.  **테넌트 URL** 필드에 `https://api.ciscoweb.com/v1/scim/[OrgId]` 형식으로 값을 입력합니다. `[OrgId]`를 얻으려면 [Cisco Webex 컨트롤 허브](https://admin.webex.com/login)에 로그인합니다. 왼쪽 아래에서 조직 이름을 클릭하고 **조직 ID**의 값을 복사합니다. 

    * **비밀 토큰**의 값을 얻으려면 이 [URL](https://idbroker.webex.com/idb/saml2/jsp/doSSO.jsp?type=login&goto=https%3A%2F%2Fidbroker.webex.com%2Fidb%2Foauth2%2Fv1%2Fauthorize%3Fresponse_type%3Dtoken%26client_id%3DC4ca14fe00b0e51efb414ebd45aa88c1858c3bfb949b2405dba10b0ca4bc37402%26redirect_uri%3Dhttp%253A%252F%252Flocalhost%253A3000%252Fauth%252Fcode%26scope%3Dspark%253Apeople_read%2520spark%253Apeople_write%2520Identity%253ASCIM%26state%3Dthis-should-be-a-random-string-for-security-purpose)로 이동합니다. 표시되는 webex 로그인 페이지에서 조직의 전체 Cisco Webex 관리자 계정으로 로그인합니다. 사이트에 연결할 수 없다는 오류 페이지가 표시되지만, 이는 정상적인 동작입니다.

        ![Cisco Webex 프로비전](./media/cisco-webex-provisioning-tutorial/test.png)
 
    * 아래에 강조 표시된 대로 URL에서 생성된 전달자 토큰의 값을 복사합니다. 이 토큰은 365일 동안 유효합니다.
        
        ![Cisco Webex 프로비전](./media/cisco-webex-provisioning-tutorial/test1.png)

7. 5단계에 표시된 필드를 채우면 **연결 테스트**를 클릭하여 Azure AD에서 Clarizen에 연결할 수 있는지 확인합니다. 연결이 실패하면 Cisco Webex 계정에 관리자 권한이 있는지 확인하고 다시 시도합니다.

    ![테넌트 URL + 토큰](common/provisioning-testconnection-tenanturltoken.png)
   
8. **알림 메일** 필드에 프로비저닝 오류 알림을 받을 개인 또는 그룹의 메일 주소를 입력하고, **오류가 발생할 경우, 메일 알림 보내기** 확인란을 선택합니다.

    ![알림 이메일](common/provisioning-notification-email.png)

9. **저장**을 클릭합니다.

10. **매핑** 섹션에서 **Azure Active Directory 사용자를 Cisco Webex에 동기화**를 선택합니다.

    ![Cisco Webex 프로비전](./media/cisco-webex-provisioning-tutorial/usermapping.png)

11. **특성 매핑** 섹션에서 Azure AD에서 Cisco Webex로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 Cisco Webex의 사용자 계정을 일치시키는 데 사용됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

    ![Cisco Webex 프로비전](./media/cisco-webex-provisioning-tutorial/usermappingattributes.png)

12. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

13. Cisco Webex에 Azure AD 프로비전 서비스를 사용하도록 설정하려면 **설정** 섹션에서 **프로비전 상태**를 **켜기**로 변경합니다.

    ![프로비전 상태 켜기로 전환](common/provisioning-toggle-on.png)

14. **설정**의 **범위** 섹션에서 원하는 값을 선택하여 Cisco Webex에 프로비전하려는 사용자 및/또는 그룹을 정의합니다.

    ![프로비전 범위](common/provisioning-scope.png)

15. 프로비전할 준비가 되면 **저장**을 클릭합니다.

    ![프로비전 구성 저장](common/provisioning-configuration-save.png)

이 작업은 **설정**의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화를 시작합니다. 초기 동기화는 Azure AD 프로비전 서비스가 실행되는 동안 약 40분마다 발생하는 후속 동기화보다 더 많은 시간이 걸립니다. **동기화 세부 정보** 섹션을 사용하여 진행 상황을 모니터링하고, Cisco Webex의 Azure AD 프로비전 서비스에서 수행한 모든 작업을 설명하는 프로비전 활동 보고서에 대한 링크를 따를 수 있습니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../app-provisioning/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="connector-limitations"></a>커넥터 제한 사항

* Cisco Webex는 현재 Cisco의 EFT(초기 필드 테스트) 단계에 있습니다. 자세한 내용은 [Cisco 지원 팀](https://www.webex.co.in/support/support-overview.html)에 문의하세요. 
* Cisco Webex 구성에 대한 자세한 내용은 [여기](https://help.webex.com/en-us/aumpbz/Synchronize-Azure-Active-Directory-Users-into-cisco-webex-Control-Hub)서 Cisco 설명서를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).