---
title: '자습서: Azure Active Directory을 사용 하 여 자동 사용자 프로 비전을 위한 Atlassian 클라우드 구성 Microsoft Docs'
description: 사용자 계정을 Atlassian 클라우드로 자동으로 프로 비전 및 프로 비전 해제 하도록 Azure Active Directory를 구성 하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/27/2019
ms.author: jeedes
ms.openlocfilehash: 7ddccef00cf1b5ad524c0e1eaa7aed52c0e55197
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77059347"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로 비전에 대해 Atlassian Cloud 구성

이 자습서에서는 사용자 및/또는 그룹을 Atlassian 클라우드로 자동으로 프로 비전 및 프로 비전 해제 하도록 Azure AD를 구성 하기 위해 Atlassian Cloud and Azure Active Directory (Azure AD)에서 수행 하는 단계를 설명 합니다.

> [!NOTE]
> 이 자습서에서는 Azure AD 사용자 프로비저닝 서비스에 기반하여 구축된 커넥터에 대해 설명합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* Azure AD 테넌트
* [Atlassian 클라우드 테넌트](https://www.atlassian.com/licensing/cloud)
* 관리자 권한이 있는 Atlassian Cloud의 사용자 계정

> [!NOTE]
> Azure AD 프로 비전 통합은 Atlassian 클라우드 팀이 사용할 수 있는 **Atlassian cloud SCIM API**를 사용 합니다.

## <a name="add-atlassian-cloud-from-the-gallery"></a>갤러리에서 Atlassian Cloud 추가

Azure AD를 사용 하 여 자동 사용자 프로 비전을 위해 Atlassian Cloud를 구성 하기 전에 Azure AD 응용 프로그램 갤러리의 Atlassian Cloud를 관리 되는 SaaS 응용 프로그램 목록에 추가 해야 합니다.

**Azure AD 응용 프로그램 갤러리에서 Atlassian Cloud를 추가 하려면 다음 단계를 수행 합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 패널에서 **Azure Active Directory**를 선택 합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 응용 프로그램을 추가 하려면 창의 위쪽에 있는 **새 응용 프로그램** 단추를 선택 합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **Atlassian cloud**를 입력 하 고, 결과 패널에서 **Atlassian cloud** 를 선택한 다음, **추가** 단추를 클릭 하 여 응용 프로그램을 추가 합니다.

    ![결과 목록의 Atlassian Cloud](common/search-new-app.png)

## <a name="assigning-users-to-atlassian-cloud"></a>Atlassian Cloud에 사용자 할당

Azure Active Directory는 *할당* 이라는 개념을 사용 하 여 선택한 앱에 대 한 액세스 권한을 받아야 하는 사용자를 결정 합니다. 자동 사용자 프로 비전의 컨텍스트에서는 Azure AD의 응용 프로그램에 할당 된 사용자 및/또는 그룹만 동기화 됩니다.

자동 사용자 프로비저닝을 구성 하 고 사용 하도록 설정 하기 전에 Atlassian Cloud에 대 한 액세스 권한이 필요한 Azure AD의 사용자 및/또는 그룹을 결정 해야 합니다. 일단 결정 되 면 다음 지침에 따라 이러한 사용자 및/또는 그룹을 Atlassian 클라우드에 할당할 수 있습니다.

* [엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-atlassian-cloud"></a>Atlassian Cloud에 사용자를 할당 하기 위한 주요 팁

* 자동 사용자 프로 비전 구성을 테스트 하기 위해 단일 Azure AD 사용자를 Atlassian Cloud에 할당 하는 것이 좋습니다. 추가 사용자 및/또는 그룹은 나중에 할당할 수도 있습니다.

* Atlassian Cloud에 사용자를 할당할 때 [할당] 대화 상자에서 유효한 응용 프로그램별 역할 (사용 가능한 경우)을 선택 해야 합니다. **기본 액세스** 역할이 있는 사용자는 프로비전에서 제외됩니다.

## <a name="configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Atlassian Cloud에 대 한 자동 사용자 프로 비전 구성 

이 섹션에서는 azure ad의 사용자 및/또는 그룹 할당을 기반으로 Atlassian 클라우드에서 사용자 및/또는 그룹을 만들고, 업데이트 하 고, 비활성화 하도록 Azure AD 프로 비전 서비스를 구성 하는 단계를 안내 합니다.

> [!TIP]
> [Atlassian cloud Single Sign-On 자습서](atlassian-cloud-tutorial.md)에 제공 된 지침에 따라 Atlassian 클라우드에 대해 SAML 기반 Single Sign-On를 사용 하도록 선택할 수도 있습니다. Single Sign-On과 자동 사용자 프로비저닝은 서로 보완적이지만, 별개로 구성할 수 있습니다.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Azure AD에서 Atlassian Cloud에 대 한 자동 사용자 프로 비전을 구성 하려면:

1. [Azure Portal](https://portal.azure.com) 에 로그인 하 고 **엔터프라이즈 응용 프로그램**을 선택한 다음 **모든 응용 프로그램**을 선택 하 고 **Atlassian Cloud**를 선택 합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Atlassian Cloud**를 선택합니다.

    ![애플리케이션 목록의 Atlassian Cloud 링크](common/all-applications.png)

3. **프로비전** 탭을 선택합니다.

    ![Atlassian 클라우드 프로 비전](./media/atlassian-cloud-provisioning-tutorial/provisioning-tab.png)

4. **프로비전 모드**를 **자동**으로 설정합니다.

    ![Atlassian 클라우드 프로 비전](./media/atlassian-cloud-provisioning-tutorial/credentials.png)

5. [Atlassian 조직 관리자](https://admin.atlassian.com) **> 조직 > 디렉터리를 선택**합니다.

    ![Atlassian 클라우드 프로 비전](./media/atlassian-cloud-provisioning-tutorial/select-directory.png)

6. **사용자 프로 비전** 을 클릭 하 고 **디렉터리 만들기**를 클릭 합니다. **디렉터리 기준 url** 및 **전달자 토큰** 을 **테넌트 url** 및 **암호 토큰** 필드 각각에 복사 합니다.

    ![Atlassian 클라우드 프로 비전](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![Atlassian 클라우드 프로 비전](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png) ![Atlassian 클라우드 프로 비전](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)

7. **관리자 자격 증명** 섹션 아래에서 Atlassian 클라우드의 계정에 대 한 **테넌트 URL** 및 **암호 토큰** 을 입력 합니다. 이러한 값의 예는 다음과 같습니다.

   * **테넌트 URL** 필드에서 6 단계에 설명 된 대로 Atlassian에서 수신 하는 특정 테넌트 엔드포인트를 채웁니다. 예: `https://api.atlassian.com/scim/directory/{directoryId}`.

   * 6단계에서 설명한 대로 **비밀 토큰** 필드에 비밀 토큰을 채웁니다.

8. 7 단계에 표시 된 필드를 채우면 **연결 테스트** 를 클릭 하 여 Azure AD가 Atlassian 클라우드에 연결할 수 있는지 확인 합니다. 연결에 실패 하면 Atlassian 클라우드 계정에 관리자 권한이 있는지 확인 하 고 다시 시도 하세요.

    ![Atlassian 클라우드 프로 비전](./media/atlassian-cloud-provisioning-tutorial/test-connection.png)

9. **알림 메일** 필드에 프로비저닝 오류 알림을 받을 개인 또는 그룹의 메일 주소를 입력하고, **오류가 발생할 경우, 메일 알림 보내기** 확인란을 선택합니다.

    ![Atlassian 클라우드 프로 비전](./media/atlassian-cloud-provisioning-tutorial/notification.png)

10. **저장**을 클릭합니다.

11. **매핑** 섹션 아래에서 **Atlassian Cloud에 사용자 Azure Active Directory 동기화를**선택 합니다.

    ![Atlassian 클라우드 프로 비전](./media/atlassian-cloud-provisioning-tutorial/provision-users.png)

12. **특성 매핑** 섹션에서 Azure AD에서 Atlassian Cloud로 동기화 되는 사용자 특성을 검토 합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 Atlassian 클라우드의 사용자 계정을 일치 시키는 데 사용 됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

    ![Atlassian 클라우드 프로 비전](./media/atlassian-cloud-provisioning-tutorial/user-mapping.png)

13. **매핑** 섹션 아래에서 **Atlassian Cloud에 Azure Active Directory 그룹 동기화를**선택 합니다.

    ![Atlassian 클라우드 프로 비전](./media/atlassian-cloud-provisioning-tutorial/provision-groups.png)

14. **특성 매핑** 섹션에서 Azure AD에서 Atlassian Cloud로 동기화 되는 그룹 특성을 검토 합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 Atlassian 클라우드의 그룹을 일치 시키는 데 사용 됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

    ![Atlassian 클라우드 프로 비전](./media/atlassian-cloud-provisioning-tutorial/group-mapping.png)

15. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

16. Atlassian Cloud에 대 한 Azure AD 프로 비전 서비스를 사용 하도록 **설정 하려면 설정** 섹션에서 **프로 비전 상태** 를 **켜기** 로 변경 합니다.

    ![Atlassian 클라우드 프로 비전](./media/atlassian-cloud-provisioning-tutorial/provisioning-on.png)

17. **설정** 섹션의 **범위** 에서 원하는 값을 선택 하 여 Atlassian Cloud에 프로 비전 하려는 사용자 및/또는 그룹을 정의 합니다.

    ![Atlassian 클라우드 프로 비전](./media/atlassian-cloud-provisioning-tutorial/provisioning-options.png)

18. 프로비전할 준비가 되면 **저장**을 클릭합니다.

    ![Atlassian 클라우드 프로 비전](./media/atlassian-cloud-provisioning-tutorial/save.png)

이 작업은 **설정**의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화를 시작합니다. 초기 동기화는 Azure AD 프로비전 서비스가 실행되는 동안 약 40분마다 발생하는 후속 동기화보다 더 많은 시간이 걸립니다. **동기화 세부 정보** 섹션을 사용 하 여 진행률을 모니터링 하 고 프로 비전 활동 보고서에 대 한 링크를 따라 Atlassian 클라우드의 Azure AD 프로 비전 서비스에서 수행 하는 모든 작업을 설명 합니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../app-provisioning/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="connector-limitations"></a>커넥터 제한 사항

* Atlassian Cloud를 사용 하면 [확인 된 도메인](https://confluence.atlassian.com/cloud/organization-administration-938859734.html)에서만 사용자를 프로 비전 할 수 있습니다.
* Atlassian Cloud는 현재 그룹 이름 바꾸기를 지원 하지 않습니다. 즉, Azure AD에서 그룹의 displayName에 대 한 모든 변경 내용이 업데이트 되 고 Atlassian 클라우드에 반영 되지 않습니다.
* Azure AD의 **메일** 사용자 특성 값은 사용자에 게 Microsoft Exchange 사서함이 있는 경우에만 채워집니다. 사용자가 없는 경우 Atlassian 클라우드의 **전자 메일** 특성에 다른 desired 특성을 매핑하는 것이 좋습니다.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png