---
title: '자습서: Azure Active Directory를 사용하여 자동 사용자 프로비저닝을 위한 Cornerstone OnDemand 구성 | Microsoft Docs'
description: 자동으로 프로 비전 하 고 사용자 계정을 Cornerstone OnDemand로 프로 비전 해제 하도록 Azure Active Directory를 구성 하는 방법에 알아봅니다.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85ddcf3aff7d15c946230cedb0da190bca6aeab7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127500"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비저닝을 위한 Cornerstone OnDemand 구성

이 자습서에서는 자동으로 프로 비전 하 고 사용자 또는 그룹을 Cornerstone OnDemand 프로 비전 해제 하도록 Azure AD를 구성 하려면 Cornerstone OnDemand 및 Azure Active Directory (Azure AD)에서 수행 하는 단계를 보여 줍니다.

> [!NOTE]
> 이 자습서는 Azure AD 사용자 프로 비전 서비스를 기반으로 빌드되는 커넥터를 설명 합니다. 이 서비스의 용도, 작동 방법 및 자주 묻는 질문에 대 한 내용은 참조 하세요 [사용자 프로 비전 및 Azure Active Directory를 사용 하 여 소프트웨어-as a service (SaaS) 응용 프로그램에 프로 비전 해제 자동화](../manage-apps/user-provisioning.md)합니다.

## <a name="prerequisites"></a>필수 조건

이 자습서에 설명 된 시나리오에서는 있다고 가정 합니다.

* Azure AD 테넌트.
* Cornerstone OnDemand 테 넌 트입니다.
* 관리자 권한이 있는 Cornerstone OnDemand의 사용자 계정.

> [!NOTE]
> Azure AD 프로 비전 통합에 의존 합니다 [Cornerstone OnDemand 웹 서비스](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_-_Summary_of_Web_Services_v20151106.pdf)합니다. 이 서비스는 Cornerstone OnDemand 팀에 사용할 수 있습니다.

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>Azure Marketplace에서 Cornerstone OnDemand를 추가 합니다.

Azure AD를 사용 하 여 프로 비전 하는 자동 사용자를 Cornerstone OnDemand를 구성 하기 전에 관리 되는 SaaS 응용 프로그램의 목록에 Marketplace에서 Cornerstone OnDemand를 추가 합니다.

Marketplace에서 Cornerstone OnDemand를 추가 하려면 다음이 단계를 수행 합니다.

1. 에 [Azure portal](https://portal.azure.com), 왼쪽 탐색 창에서 선택 **Azure Active Directory**합니다.

    ![Azure Active Directory 아이콘](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션**을 선택합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에서 입력 **Cornerstone OnDemand** 선택한 **Cornerstone OnDemand** 결과 패널에서 합니다. 응용 프로그램을 추가 하려면 선택 **추가**합니다.

    ![결과 목록의 Cornerstone OnDemand](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>Cornerstone OnDemand에 사용자 할당

이라는 개념을 사용 하 여 azure Active Directory *할당* 어떤 사용자가 선택한 앱에 대 한 액세스를 받아야 하는지를 판단 합니다. 자동 사용자 프로 비전의 컨텍스트에서 사용자 또는 Azure AD에서 응용 프로그램에 할당 된 그룹 동기화 됩니다.

구성 및 자동 사용자 프로 비전을 사용 하도록 설정 하기 전에 Cornerstone OnDemand에 대 한 액세스를 필요한 사용자 또는 Azure AD의 그룹을 결정 합니다. 이 사용자 또는 그룹을 Cornerstone OnDemand에 할당 하려면의 지침을 따릅니다 [엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)합니다.

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Cornerstone OnDemand에 사용자를 할당하기 위한 주요 팁

* 단일 할당 하는 것이 좋습니다 Azure AD 사용자를 Cornerstone OnDemand에 자동 사용자 프로 비전 구성을 테스트 합니다. 추가 사용자 또는 그룹을 나중에 할당할 수 있습니다.

* Cornerstone OnDemand에 사용자를 할당 하면 선택 유효한 응용 프로그램별 역할, 사용 가능한 경우 할당 대화 상자에서 합니다. **기본 액세스** 역할이 있는 사용자는 프로비전에서 제외됩니다.

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>Cornerstone OnDemand에 자동 사용자 프로비저닝 구성

이 섹션에서는 Azure AD 프로 비전 서비스를 구성 하는 단계를 안내 합니다. 만들기, 업데이트 및 Azure AD의 사용자 또는 그룹 할당에 따라 Cornerstone OnDemand의 사용자 또는 그룹을 사용 하지 않도록 설정 하려면 사용 합니다.

Azure AD에서 Cornerstone OnDemand에 자동 사용자 프로 비전을 구성 하려면 다음이 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 선택 **엔터프라이즈 응용 프로그램** > **모든 응용 프로그램** > **Cornerstone OnDemand**합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Cornerstone OnDemand**를 선택합니다.

    ![응용 프로그램 목록에서 Cornerstone OnDemand 링크](common/all-applications.png)

3. **프로비전** 탭을 선택합니다.

    ![Cornerstone OnDemand 프로비전](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. **프로비전 모드**를 **자동**으로 설정합니다.

    ![Cornerstone OnDemand 프로 비전 모드](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. 아래는 **관리자 자격 증명** 섹션에서 관리자 사용자 이름, 관리자 암호 및 Cornerstone OnDemand 계정의 도메인을 입력 합니다.

    * 에 **관리자 사용자 이름** 상자에서 도메인 또는 Cornerstone OnDemand 테 넌 트에서 관리자 계정의 사용자 이름을 입력 합니다. 예 contoso\admin입니다.

    * 에 **관리자 암호** 상자에서 관리자 사용자 이름에 해당 하는 암호를 입력 합니다.

    * 에 **도메인** 상자에서 Cornerstone OnDemand 테 넌 트의 웹 서비스 URL을 입력 합니다. 예를 들어, 서비스에 위치한 `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx`, 도메인은 Contoso에 대 한 `https://ws-contoso.csod.com/feed30/clientdataservice.asmx`합니다. 웹 서비스 URL을 검색 하는 방법에 대 한 자세한 내용은 참조 하세요. [이 pdf](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf)합니다.

6. 5 단계에에서 표시 된 상자에 입력 한 후 선택 **연결 테스트** 되도록 Azure AD가 Cornerstone OnDemand에 연결할 수 있습니다. 연결에 실패 하면 Cornerstone OnDemand 계정에 관리자 권한이 있는지 확인 하 고 다시 시도.

    ![Cornerstone OnDemand 테스트 연결](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. 에 **알림 전자 메일** 상자에 사용자의 전자 메일 주소를 입력 하거나 프로 비전 오류 알림의 받을 그룹입니다. 선택 된 **오류가 발생 하는 경우 전자 메일 알림 보내기** 확인란 합니다.

    ![Cornerstone OnDemand에 대 한 알림 전자 메일](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. **저장**을 선택합니다.

9. **매핑** 섹션 아래에서 **Azure Active Directory 사용자를 Cornerstone OnDemand에 동기화**를 선택합니다.

    ![Cornerstone OnDemand 동기화](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. Azure AD에서 Cornerstone OnDemand로 동기화 되는 사용자 특성을 검토 합니다 **특성 매핑** 섹션입니다. **일치** 속성으로 선택된 특성은 업데이트 작업을 위해 Cornerstone OnDemand의 사용자 계정을 일치시키는 데 사용됩니다. 모든 변경 내용을 저장 하려면 선택 **저장할**합니다.

    ![Cornerstone OnDemand 특성 매핑](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. 범위 지정 필터를 구성 하려면의 지침에 따라 합니다 [범위 지정 필터 자습서](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)합니다.

12. Azure AD에서 Cornerstone OnDemand에 대 한 서비스를 프로 비전을 사용 하도록 설정 합니다 **설정** 섹션, 변경 **프로 비전 상태** 를 **에서**.

    ![Cornerstone OnDemand 프로 비전 상태](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. Cornerstone OnDemand에 프로 비전 할 사용자 또는 그룹을 정의 합니다. 에 **설정을** 섹션에서 원하는 값을 선택 합니다 **범위**합니다.

    ![Cornerstone OnDemand 범위](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. 프로 비전 할 준비가 선택 **저장할**합니다.

    ![Cornerstone OnDemand 저장](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

이 작업에는 모든 사용자의 초기 동기화가 시작 됩니다. 또는에 정의 된 그룹 **범위** 에 **설정** 섹션입니다. 초기 동기화 이후 동기화 보다 더 오래 걸립니다. Azure AD 프로 비전 서비스를 실행 하기만 약 40 분 마다 발생 합니다. 

사용할 수는 **동기화 세부 정보** 진행률을 모니터링 하 고 프로 비전 작업 보고서에 링크를 따라 섹션입니다. 보고서에 프로 비전 서비스 Cornerstone OnDemand에서 Azure AD에서 수행 되는 모든 작업에 설명 합니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../manage-apps/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="connector-limitations"></a>커넥터 제한 사항

Cornerstone OnDemand **위치** 특성은 Cornerstone OnDemand 포털에서 역할에 해당하는 값을 예상합니다. 유효한 목록을 가져오려면 **위치** 값으로 이동 **사용자 레코드 편집 > 조직 구조 > 위치** Cornerstone OnDemand 포털에서 합니다.

![사용자 레코드 편집 cornerstone OnDemand 프로 비전](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![Cornerstone OnDemand 프로 비전 위치](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![Cornerstone OnDemand 프로 비전 위치 목록](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../manage-apps/check-status-user-account-provisioning.md).

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
