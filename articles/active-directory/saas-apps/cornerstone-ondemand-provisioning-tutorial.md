---
title: '자습서: Azure Active Directory를 사용하여 자동 사용자 프로비저닝을 위한 Cornerstone OnDemand 구성 | Microsoft Docs'
description: 사용자 계정을 Cornerstone OnDemand로 자동으로 프로비저닝 및 프로비저닝 해제하도록 Azure Active Directory를 구성하는 방법을 알아봅니다.
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
ms.openlocfilehash: 4200bc9879aba5e18282123be03576cc617a5e14
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549231"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비저닝을 위한 Cornerstone OnDemand 구성

이 자습서에서는 사용자 및/또는 그룹을 Cornerstone OnDemand로 자동으로 프로비저닝 및 프로비저닝 해제하도록 Azure AD(Azure Active Directory)를 구성하기 위해 Cornerstone OnDemand 및 Azure AD(Azure Active Directory)에서 수행하는 단계를 보여 줍니다.

> [!NOTE]
> 이 자습서에서는 Azure AD 사용자 프로비저닝 서비스를 기반으로 구축된 커넥터에 대해 설명합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 정보는 [Azure Active Directory를 사용하여 SaaS(Software as a Service) 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에서 설명한 시나리오에서는 사용자에게 다음 항목이 있다고 가정합니다.

* Azure AD 테넌트.
* Cornerstone OnDemand 테넌트
* 관리자 권한이 있는 Cornerstone OnDemand의 사용자 계정

> [!NOTE]
> Azure AD 프로비저닝 통합은 [Cornerstone OnDemand 웹 서비스](https://www.cornerstoneondemand.com/)에 의존합니다. Cornerstone OnDemand 팀은 이 서비스를 사용할 수 있습니다.

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>Azure Marketplace에서 Cornerstone OnDemand 추가

Azure AD를 사용하여 Cornerstone OnDemand에 자동 사용자 프로비저닝을 구성하기 전에 Marketplace의 Cornerstone OnDemand를 관리형 SaaS 애플리케이션 목록에 추가하세요.

Marketplace의 Cornerstone OnDemand를 추가하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 탐색 창에서 **Azure Active Directory**를 선택합니다.

    ![Azure Active Directory 아이콘](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션**을 선택합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **Cornerstone OnDemand**를 입력하고 결과 패널에서 **Cornerstone OnDemand**를 선택합니다. 애플리케이션을 추가하려면 **추가**를 선택합니다.

    ![결과 목록의 Cornerstone OnDemand](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>Cornerstone OnDemand에 사용자 할당

Azure Active Directory는 *할당*이라는 개념을 사용하여 어떤 사용자가 선택된 앱에 대한 액세스 권한을 부여받아야 하는지 판단합니다. 자동 사용자 프로비저닝의 컨텍스트에서 Azure AD의 애플리케이션에 할당된 사용자 또는 그룹만 동기화됩니다.

자동 사용자 프로비저닝을 구성하고 사용하도록 설정하려면 먼저 Cornerstone OnDemand에 액세스해야 하는 Azure AD의 사용자 또는 그룹을 결정하세요. 이러한 사용자 또는 그룹을 Cornerstone OnDemand에 할당하려면 [엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)의 지침을 따르세요.

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Cornerstone OnDemand에 사용자를 할당하기 위한 주요 팁

* 자동 사용자 프로비저닝 구성을 테스트하려면 단일 Azure AD 사용자를 Cornerstone OnDemand에 할당하는 것이 좋습니다. 추가 사용자 또는 그룹을 나중에 할당할 수 있습니다.

* 사용자를 Cornerstone OnDemand에 할당할 때 할당 대화 상자에서 유효한 애플리케이션 관련 역할을 선택합니다(사용 가능한 경우). **기본 액세스** 역할이 있는 사용자는 프로비전에서 제외됩니다.

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>Cornerstone OnDemand에 자동 사용자 프로비저닝 구성

이 섹션에서는 Azure AD 프로비저닝 서비스를 구성하는 단계를 안내합니다. 이는 Azure AD의 사용자 또는 그룹 할당을 기반으로 Cornerstone OnDemand에서 사용자 또는 그룹을 만들고, 업데이트하고, 사용하지 않도록 설정하는 데 사용됩니다.

Azure AD에서 Cornerstone OnDemand에 대한 자동 사용자 프로비저닝을 구성하려면 다음 단계를 따르세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 애플리케이션** > **모든 애플리케이션** > **Cornerstone OnDemand**를 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Cornerstone OnDemand**를 선택합니다.

    ![애플리케이션 목록의 Cornerstone OnDemand 링크](common/all-applications.png)

3. **프로비전** 탭을 선택합니다.

    ![Cornerstone OnDemand 프로비전](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. **프로비전 모드**를 **자동**으로 설정합니다.

    ![Cornerstone OnDemand 프로비저닝 모드](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. **관리자 자격 증명** 섹션 아래에서 Cornerstone OnDemand 계정의 관리자 사용자 이름, 관리자 암호 및 도메인을 입력합니다.

    * **관리자 사용자 이름** 상자에 Cornerstone OnDemand 테넌트의 관리자 계정에 대한 도메인 또는 사용자 이름을 채웁니다. 예를 들면 contoso\admin입니다.

    * **관리자 암호** 상자에 관리자 사용자 이름에 해당하는 암호를 채웁니다.

    * **도메인** 상자에 Cornerstone OnDemand 테넌트의 웹 서비스 URL을 채웁니다. 예를 들어 서비스가 `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`에 있으면 Contoso의 도메인은 `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`입니다. 웹 서비스 URL을 검색하는 방법에 대한 자세한 내용은 [이 PDF](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf)를 참조하세요.

6. 5단계에 표시된 상자를 채운 후 **연결 테스트**를 선택하여 Azure AD가 Cornerstone OnDemand에 연결할 수 있는지 확인합니다. 연결이 실패하면 Cornerstone OnDemand 계정에 관리자 권한이 있는지 확인하고 다시 시도합니다.

    ![Cornerstone OnDemand 테스트 연결](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. **알림 이메일** 상자에서 프로비저닝 오류 알림을 받을 사람 또는 그룹의 이메일 주소를 입력합니다. **오류가 발생할 경우 이메일 알림 보내기** 확인란을 선택합니다.

    ![Cornerstone OnDemand 알림 이메일](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. **저장**을 선택합니다.

9. **매핑** 섹션 아래에서 **Azure Active Directory 사용자를 Cornerstone OnDemand에 동기화**를 선택합니다.

    ![Cornerstone OnDemand 동기화](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. **특성 매핑** 섹션에서 Azure AD에서 Cornerstone OnDemand로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택된 특성은 업데이트 작업을 위해 Cornerstone OnDemand의 사용자 계정을 일치시키는 데 사용됩니다. 변경 내용을 저장하려면 **저장**을 선택합니다.

    ![Cornerstone OnDemand 특성 매핑](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)의 지침을 따르세요.

12. Cornerstone OnDemand에 Azure AD 프로비저닝 서비스를 사용하도록 설정하려면 **설정** 섹션에서 **프로비저닝 상태**를 **켜기**로 변경합니다.

    ![Cornerstone OnDemand 프로비저닝 상태](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. Cornerstone OnDemand에 프로비저닝할 사용자 또는 그룹을 정의합니다. **설정** 섹션의 **범위**에 원하는 값을 선택합니다.

    ![Cornerstone OnDemand 범위](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. 프로비저닝할 준비가 되면 **저장**을 선택합니다.

    ![Cornerstone OnDemand 저장](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

이 작업은 **설정**의 **범위** 섹션에 정의된 모든 사용자 또는 그룹의 초기 동기화를 시작합니다. 초기 동기화는 이후 동기화보다 더 오랜 시간이 걸립니다. Azure AD 프로비저닝 서비스가 실행되는 동안 약 40분 간격으로 발생합니다. 

**동기화 세부 정보** 섹션을 사용하여 진행 상태를 모니터링하고 링크를 통해 프로비저닝 활동 보고서를 확인할 수 있습니다. 이 보고서는 Cornerstone OnDemand에서 Azure AD 프로비저닝 서비스가 수행하는 모든 작업을 설명합니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../app-provisioning/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="connector-limitations"></a>커넥터 제한 사항

Cornerstone OnDemand **위치** 특성은 Cornerstone OnDemand 포털에서 역할에 해당하는 값을 예상합니다. 유효한 **위치** 값 목록을 보려면 Cornerstone OnDemand 포털에서 **사용자 레코드 편집 > 조직 구조 > 위치**로 이동합니다.

![Cornerstone OnDemand 프로비저닝 사용자 레코드 편집](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![Cornerstone OnDemand 프로비저닝 위치](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![Cornerstone OnDemand 프로비저닝 위치 목록](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
