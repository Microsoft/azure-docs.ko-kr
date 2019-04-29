---
title: '자습서: Azure Active Directory로 자동 사용자 프로 비전에 대 한 Zscaler One 구성 | Microsoft Docs'
description: 자동으로 프로 비전 하 고 Zscaler one 사용자 계정 프로 비전 해제 하도록 Azure Active Directory를 구성 하는 방법에 알아봅니다.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 72f6ba2b-73ed-420a-863a-aff672f26fa3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant-msft
ms.openlocfilehash: 5319b0ac06c4ddf1a7627a4e7fe0bfb2694f79f6
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114612"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로 비전을 위한 Zscaler One 구성

이 자습서에서는 Zscaler One 및 Azure Active Directory (Azure AD) Azure AD를 구성 하려면 자동으로 프로 비전 하 고 Zscaler One에 사용자 및 그룹 프로 비전 해제를 수행 하는 단계를 보여 줍니다.

> [!NOTE]
> 이 자습서는 Azure AD 사용자 프로 비전 서비스를 기반으로 빌드되는 커넥터를 설명 합니다. 이 서비스의 용도, 작동 방법 및 자주 묻는 질문에 대 한 내용은 참조 하세요 [사용자 프로 비전 및 Azure Active Directory를 사용 하 여 소프트웨어-as a service (SaaS) 응용 프로그램에 프로 비전 해제 자동화](../active-directory-saas-app-provisioning.md)합니다.
>
> 이 커넥터는 현재 미리 보기로 제공 됩니다. 일반적인 Microsoft Azure 사용 약관 미리 보기 기능에 대 한 자세한 내용은 참조 하세요. [Microsoft Azure 미리 보기에 대 한 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)합니다.

## <a name="prerequisites"></a>필수 조건

이 자습서에 설명 된 시나리오에서는 있다고 가정 합니다.

* Azure AD 테넌트.
* Zscaler One 테 넌 트입니다.
* 관리자 권한으로 Zscaler One에 사용자 계정.

> [!NOTE]
> Zscaler One SCIM API을 의존 하는 Azure AD 프로 비전 통합 합니다. 이 API는 Zscaler One 개발자 엔터프라이즈 패키지를 사용 하 여 계정에 대해 사용할 수 있습니다.

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>Azure Marketplace에서 Zscaler One 추가

Azure AD를 사용 하 여 프로 비전 하는 자동 사용자를 Zscaler One을 구성 하기 전에 추가할 Zscaler One Azure Marketplace에서 관리 되는 SaaS 응용 프로그램의 목록입니다.

에 Marketplace에서 Zscaler One 추가 하려면 다음이 단계를 수행 합니다.

1. 에 [Azure portal](https://portal.azure.com), 왼쪽 탐색 창에서 선택 **Azure Active Directory**합니다.

    ![Azure Active Directory 아이콘](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션**을 선택합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에서 입력 **Zscaler One** 선택한 **Zscaler One** 결과 패널에서 합니다. 응용 프로그램을 추가 하려면 선택 **추가**합니다.

    ![결과 목록의 Zscaler One](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>Zscaler One에 사용자 할당

이라는 개념을 사용 하 여 azure Active Directory *할당* 어떤 사용자가 선택한 앱에 대 한 액세스를 받아야 하는지를 판단 합니다. 자동 사용자 프로 비전의 컨텍스트에서 사용자 또는 Azure AD에서 응용 프로그램에 할당 된 그룹 동기화 됩니다.

구성 및 자동 사용자 프로 비전을 사용 하도록 설정 하기 전에 필요한 Zscaler One에 액세스할 사용자 또는 Azure AD의 그룹을 결정 합니다. 이 사용자 또는 그룹을 Zscaler One에 할당 하려면의 지침을 따릅니다 [엔터프라이즈 앱에 사용자 또는 그룹 할당](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)합니다.

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>Zscaler One에 사용자를 할당 하기 위한 주요 팁

* 단일 할당 하는 것이 좋습니다 Azure AD 사용자를 Zscaler One에 자동 사용자 프로 비전 구성을 테스트 합니다. 추가 사용자 또는 그룹을 나중에 할당할 수 있습니다.

* Zscaler One에 사용자를 할당 하면 선택 유효한 응용 프로그램별 역할, 사용 가능한 경우 할당 대화 상자에서 합니다. **기본 액세스** 역할이 있는 사용자는 프로비전에서 제외됩니다.

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>Zscaler One에 자동 사용자 프로비저닝 구성

이 섹션에서는 Azure AD 프로 비전 서비스를 구성 하는 단계를 안내 합니다. 만들기, 업데이트 및 Azure AD의 사용자 또는 그룹 할당에 따라 Zscaler One에서 사용자 또는 그룹을 사용 하지 않도록 설정 하려면 사용 합니다.

> [!TIP]
> SAML 기반 single sign Zscaler One에 사용할 수 있습니다. 지침에 따라 합니다 [Zscaler One single sign-on 자습서](zscaler-One-tutorial.md)합니다. Single sign on 구성할 수 있습니다 자동 사용자 프로 비전과 별개로 있지만 이러한 두 기능은 서로 보완적입니다.

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>Azure AD에서 Zscaler One에 자동 사용자 프로 비전 구성

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 선택 **엔터프라이즈 응용 프로그램** > **모든 응용 프로그램** > **Zscaler One**합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Zscaler One**을 선택합니다.

    ![응용 프로그램 목록의 Zscaler One 링크](common/all-applications.png)

3. **프로비전** 탭을 선택합니다.

    ![Zscaler One에 프로 비전](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. **프로비전 모드**를 **자동**으로 설정합니다.

    ![Zscaler 하나의 프로 비전 모드](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. 아래는 **관리자 자격 증명** 섹션을 채웁니다 합니다 **테 넌 트 URL** 및 **비밀 토큰** 6 단계에에서 설명 된 대로 상자에 Zscaler One에 대 한 설정 사용 하 여 계정.

6. 테 넌 트 URL과 비밀 토큰을 받으려면 **관리** > **인증 설정을** Zscaler One 포털 UI에서에서 합니다. 아래 **인증 유형**를 선택 **SAML**합니다.

    ![Zscaler One 인증 설정](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. 선택 **SAML 구성** 열려는 합니다 **SAML 구성** 옵션입니다.

    ![Zscaler One SAML 구성](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. 선택 **Enable SCIM-Based 프로 비전** 설정을 가져오려면 **기준 URL** 하 고 **전달자 토큰**합니다. 다음 설정을 저장 합니다. 복사 합니다 **기준 URL** 설정을 **테 넌 트 URL** Azure portal에서 합니다. 복사 합니다 **전달자 토큰** 설정을 **비밀 토큰** Azure portal에서 합니다.

7. 5 단계에에서 표시 된 상자에 입력 한 후 선택 **연결 테스트** 되도록 Azure AD가 Zscaler One에 연결할 수 있습니다. 연결에 실패 하면, Zscaler One 사용자 계정에 관리자 권한이 있는지 확인 하 고 다시 시도.

    ![Zscaler 테스트 연결](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. 에 **알림 전자 메일** 상자에 사용자의 전자 메일 주소를 입력 하거나 프로 비전 오류 알림의 받을 그룹입니다. 선택 된 **오류가 발생 하는 경우 전자 메일 알림 보내기** 확인란 합니다.

    ![Zscaler One 알림 전자 메일](./media/zscaler-one-provisioning-tutorial/notification.png)

9. **저장**을 선택합니다.

10. 아래는 **매핑을** 섹션에서 **동기화 할 Azure Active Directory 사용자를 Zscaler One**합니다.

    ![Zscaler One 사용자 동기화](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. Zscaler one에서 Azure AD에서 동기화 되는 사용자 특성을 검토 합니다 **특성 매핑** 섹션입니다. 으로 선택한 특성 **일치** 속성은 Zscaler One의 업데이트 작업에 대 한 사용자 계정을 일치 하는 데 사용 됩니다. 모든 변경 내용을 저장 하려면 선택 **저장할**합니다.

    ![일치 하는 Zscaler One 사용자 특성](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. 아래는 **매핑을** 섹션에서 **Azure Active Directory 그룹 동기화 Zscaler One에**입니다.

    ![Zscaler One 그룹 동기화](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. Zscaler one에서 Azure AD에서 동기화 되는 그룹 특성을 검토 합니다 **특성 매핑** 섹션입니다. 으로 선택한 특성 **일치** 속성 업데이트 작업을 위해 Zscaler One의 그룹을 일치 시키는 데 사용 됩니다. 모든 변경 내용을 저장 하려면 선택 **저장할**합니다.

    ![Zscaler One 일치 하는 그룹 특성](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. 범위 지정 필터를 구성 하려면의 지침에 따라 합니다 [범위 지정 필터 자습서](./../active-directory-saas-scoping-filters.md)합니다.

15. Zscaler One에 대 한 서비스를 프로 비전 하는 Azure AD를 사용 하도록 설정 합니다 **설정** 섹션, 변경 **프로 비전 상태** 에 **에서**.

    ![Zscaler 하나의 프로 비전 상태](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. Zscaler One에 프로 비전 할 사용자 또는 그룹을 정의 합니다. 에 **설정을** 섹션에서 원하는 값을 선택 합니다 **범위**합니다.

    ![Zscaler One 범위](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. 프로 비전 할 준비가 선택 **저장할**합니다.

    ![Zscaler 하나 저장](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

이 작업에는 모든 사용자의 초기 동기화가 시작 됩니다. 또는에 정의 된 그룹 **범위** 에 **설정** 섹션입니다. 초기 동기화 이후 동기화 보다 더 오래 걸립니다. Azure AD 프로 비전 서비스를 실행 하기만 약 40 분 마다 발생 합니다. 

사용할 수는 **동기화 세부 정보** 진행률을 모니터링 하 고 프로 비전 작업 보고서에 링크를 따라 섹션입니다. 보고서에 프로 비전 서비스 Zscaler One에서 Azure AD에서 수행 되는 모든 작업에 설명 합니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../active-directory-saas-provisioning-reporting.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../active-directory-saas-provisioning-reporting.md).

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png
