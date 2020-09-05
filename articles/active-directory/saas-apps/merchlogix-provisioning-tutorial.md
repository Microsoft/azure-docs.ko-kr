---
title: '자습서: Azure Active Directory을 사용 하 여 자동 사용자 프로 비전을 위한 MerchLogix 구성 | Microsoft Docs'
description: 사용자 계정을 MerchLogix로 자동으로 프로 비전 및 프로 비전 해제 하도록 Azure Active Directory를 구성 하는 방법에 대해 알아봅니다.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 03/27/2019
ms.author: zhchia
ms.openlocfilehash: 6fe256660dc73aa411cf06e2b56ce9ef26934e2f
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548003"
---
# <a name="tutorial-configure-merchlogix-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로 비전을 위한 MerchLogix 구성

이 자습서에서는 사용자 및/또는 그룹을 MerchLogix로 자동으로 프로 비전 및 프로 비전 해제 하도록 Azure AD를 구성 하기 위해 MerchLogix and Azure Active Directory (Azure AD)에서 수행 하는 단계를 보여 줍니다.

> [!NOTE]
> 이 자습서에서는 Azure AD 사용자 프로비저닝 서비스에 기반하여 구축된 커넥터에 대해 설명합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* Azure AD 테넌트
* MerchLogix 테 넌 트
* 사용자 프로 비전에 필요한 SCIM 끝점 URL 및 암호 토큰을 제공할 수 있는 MerchLogix의 기술 담당자

## <a name="adding-merchlogix-from-the-gallery"></a>갤러리에서 MerchLogix 추가

Azure AD를 사용 하 여 자동 사용자 프로 비전을 위한 MerchLogix를 구성 하기 전에 Azure AD 응용 프로그램 갤러리의 MerchLogix를 관리 되는 SaaS 응용 프로그램 목록에 추가 해야 합니다.

**Azure AD 응용 프로그램 갤러리에서 MerchLogix를 추가 하려면 다음 단계를 수행 합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 패널에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**  >  **모든 응용 프로그램**으로 이동 합니다.

    ![엔터프라이즈 애플리케이션 섹션][2]

3. MerchLogix를 추가 하려면 대화 상자 위쪽에서 **새 응용 프로그램** 단추를 클릭 합니다.

    ![새 애플리케이션 단추][3]

4. 검색 상자에 **MerchLogix**를 입력 합니다.

5. 결과 패널에서 **MerchLogix**를 선택 하 고 **추가** 단추를 클릭 하 여 SaaS 응용 프로그램 목록에 MerchLogix를 추가 합니다.

    ![MerchLogix 프로 비전][4]

## <a name="assigning-users-to-merchlogix"></a>MerchLogix에 사용자 할당

Azure Active Directory는 "할당"이라는 개념을 사용하여 어떤 사용자가 선택한 앱에 대한 액세스를 받아야 하는지를 판단합니다. 자동 사용자 프로비저닝의 컨텍스트에서 Azure AD의 애플리케이션에 "할당된" 사용자 및/또는 그룹만 동기화됩니다. 

자동 사용자 프로비저닝을 구성 하 고 사용 하도록 설정 하기 전에 MerchLogix에 액세스 해야 하는 Azure AD의 사용자 및/또는 그룹을 결정 해야 합니다. 일단 결정 되 면 다음 지침에 따라 이러한 사용자 및/또는 그룹을 MerchLogix에 할당할 수 있습니다.

* [엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-merchlogix"></a>MerchLogix에 사용자를 할당 하기 위한 주요 팁

* 최초 자동 사용자 프로 비전 구성을 테스트 하기 위해 단일 Azure AD 사용자를 MerchLogix에 할당 하는 것이 좋습니다. 테스트가 성공적으로 완료되면 추가 사용자 및/또는 그룹이 나중에 할당될 수 있습니다.

* 사용자를 MerchLogix에 할당할 때 할당 대화 상자에서 유효한 응용 프로그램별 역할 (사용 가능한 경우)을 선택 해야 합니다. **기본 액세스** 역할이 있는 사용자는 프로비전에서 제외됩니다.

## <a name="configuring-automatic-user-provisioning-to-merchlogix"></a>MerchLogix에 자동 사용자 프로 비전 구성 

이 섹션에서는 azure ad의 사용자 및/또는 그룹 할당에 따라 MerchLogix에서 사용자 및/또는 그룹을 만들고, 업데이트 하 고, 사용 해제 하도록 Azure AD 프로 비전 서비스를 구성 하는 단계를 안내 합니다.

> [!TIP]
> [MerchLogix Single Sign-On 자습서](merchlogix-tutorial.md)에 제공 된 지침에 따라 MerchLogix에 대해 SAML 기반 Single Sign-On를 사용 하도록 선택할 수도 있습니다. Single Sign-On과 자동 사용자 프로비저닝은 서로 보완적이지만, 별개로 구성할 수 있습니다.

### <a name="to-configure-automatic-user-provisioning-for-merchlogix-in-azure-ad"></a>Azure AD에서 MerchLogix에 대 한 자동 사용자 프로 비전을 구성 하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인하고, **Azure Active Directory &gt; 엔터프라이즈 애플리케이션 &gt; 모든 애플리케이션**으로 차례로 이동합니다.

2. SaaS 응용 프로그램 목록에서 MerchLogix을 선택 합니다.

3. **프로비전** 탭을 선택합니다.

4. **프로비전 모드**를 **자동**으로 설정합니다.

    ![MerchLogix 프로 비전](./media/merchlogix-provisioning-tutorial/Merchlogix1.png)

5. **관리자 자격 증명** 섹션에서 다음을 수행 합니다.

    * **테 넌 트 URL** 필드에 MerchLogix 기술 담당자가 제공한 scim 끝점 url을 입력 합니다.

    * **비밀 토큰** 필드에 MerchLogix 기술 담당자가 제공한 비밀 토큰을 입력 합니다.

6. 5 단계에 표시 된 필드를 채우면 **연결 테스트** 를 클릭 하 여 Azure AD가 MerchLogix에 연결할 수 있는지 확인 합니다. 연결에 실패 하면 MerchLogix 계정에 관리자 권한이 있는지 확인 하 고 다시 시도 합니다.

7. **알림 메일** 필드에 프로비저닝 오류 알림을 받을 개인 또는 그룹의 메일 주소를 입력하고, **오류가 발생할 경우, 메일 알림 보내기** 확인란을 선택합니다.

8. **저장**을 클릭합니다.

9. **매핑** 섹션 아래에서 **MerchLogix에 사용자 Azure Active Directory 동기화를**선택 합니다.

10. **특성 매핑** 섹션에서 Azure AD에서 MerchLogix로 동기화 되는 사용자 특성을 검토 합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 MerchLogix의 사용자 계정을 일치 시키는 데 사용 됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

11. **매핑** 섹션 아래에서 **MerchLogix에 Azure Active Directory 그룹 동기화를**선택 합니다.

12. **특성 매핑** 섹션에서 Azure AD에서 MerchLogix로 동기화 되는 그룹 특성을 검토 합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 MerchLogix의 그룹을 일치 시키는 데 사용 됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

13. MerchLogix에 대 한 Azure AD 프로 비전 서비스를 사용 하도록 **설정 하려면 설정** 섹션에서 **프로 비전 상태** 를 **켜기** 로 변경 합니다.

14. 프로비전할 준비가 되면 **저장**을 클릭합니다.

이 작업은 **설정**의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화를 시작합니다. 초기 동기화는 Azure AD 프로비전 서비스가 실행되는 동안 약 40분마다 발생하는 후속 동기화보다 더 많은 시간이 걸립니다. **동기화 세부 정보** 섹션을 사용 하 여 진행률을 모니터링 하 고 프로 비전 활동 보고서에 대 한 링크를 따라 MerchLogix의 Azure AD 프로 비전 서비스에서 수행 하는 모든 작업을 설명 합니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../app-provisioning/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).

<!--Image references-->
[1]: common/select-azuread.png
[2]: common/enterprise-applications.png
[3]: common/add-new-app.png
[4]: common/search-new-app.png
