---
title: '자습서: Azure Active Directory로 자동 사용자 프로비전을 위한 Slack 구성 | Microsoft Docs'
description: 사용자 계정을 Slack으로 자동으로 프로비전 및 프로비전 해제하도록 Azure Active Directory를 구성하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: daveba
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: asmalser-msft
ms.reviewer: asmalser
ms.collection: M365-identity-device-management
ms.openlocfilehash: b50bcada8cfc72c06804793850f1f28a288f5248
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60620214"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비전을 위한 Slack 구성

이 자습서의 목적은 사용자 계정을 Azure AD에서 Slack으로 자동으로 프로비전 및 프로비전 해제하도록 Slack 및 Azure AD에서 수행해야 하는 단계를 설명하는 것입니다.

## <a name="prerequisites"></a>필수 조건

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

* Azure Active Directory 테넌트
* 사용 가능한 [추가 플랜](https://aadsyncfabric.slack.com/pricing) 또는 개선을 사용한 Slack 테넌트
* 팀 관리자 권한이 있는 Slack의 사용자 계정

참고: Azure AD 프로비전 통합은 추가 플랜 이상에서 Slack 팀이 이용 가능한 [Slack SCIM API](https://api.slack.com/scim)에 의존합니다.

## <a name="assigning-users-to-slack"></a>Slack에 사용자 할당

Azure Active Directory는 "할당"이라는 개념을 사용하여 어떤 사용자가 선택한 앱에 대한 액세스를 받아야 하는지를 판단합니다. 자동 사용자 계정 프로비전의 컨텍스트에서는 Azure AD의 애플리케이션에 "할당된" 사용자 및 그룹만 동기화됩니다.

프로비전 서비스를 구성하고 사용하도록 설정하기 전에 Slack 앱에 대한 액세스가 필요한 사용자를 대표하는 Azure AD의 사용자 및/또는 그룹을 결정해야 합니다. 결정했으면 다음 지시에 따라 이러한 사용자를 Slack 앱에 할당할 수 있습니다.

[엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-slack"></a>Slack에 사용자 할당을 위한 주요 팁

* 프로비전 구성을 테스트하기 위해 단일 Azure AD 사용자를 Slack에 할당하는 것이 좋습니다. 추가 사용자 및/또는 그룹은 나중에 할당할 수도 있습니다.

* 사용자를 Slack에 할당할 때 할당 대화 상자에서 **사용자** 또는 "그룹" 역할을 선택해야 합니다. "기본 액세스" 역할은 프로비전에 작동하지 않습니다.

## <a name="configuring-user-provisioning-to-slack"></a>Slack에 사용자 프로비전 구성 

이 섹션에서는 사용자의 Azure AD를 Slack의 사용자 계정 프로비전 API에 연결하고, Azure AD의 사용자 및 그룹 할당을 기반으로 Slack에서 할당된 사용자 계정을 만들고, 업데이트하고 비활성화하도록 프로비전 서비스를 구성하는 방법을 안내합니다.

**팁:** [Azure Portal](https://portal.azure.com)에 제공된 지침에 따라 Slack에 SAML 기반 Single Sign-On을 사용하도록 설정할 수도 있습니다. Single Sign-On은 자동 프로비전과 별개로 구성할 수 있습니다. 하지만 이 두 가지 기능은 서로 보완적입니다.

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Azure AD에서 Slack에 자동 사용자 계정 프로비전을 구성하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory &gt; 엔터프라이즈 앱 &gt; 모든 애플리케이션** 섹션으로 이동합니다.

2. Single Sign-On에 대한 Slack을 이미 구성한 경우 검색 필드를 사용하여 Slack의 인스턴스를 검색합니다. 그렇지 않은 경우 **추가**를 선택하고 애플리케이션 갤러리에서 **Slack**을 검색합니다. 검색 결과에서 Slack을 선택하고 애플리케이션의 목록에 추가합니다.

3. Slack의 인스턴스를 선택한 다음, **프로비전** 탭을 선택합니다.

4. **프로비전 모드**를 **자동**으로 설정합니다.

   ![Slack 프로비전](./media/slack-provisioning-tutorial/Slack1.PNG)

5. **관리자 자격 증명** 섹션에서 **권한 부여**를 클릭합니다. 그러면 새 브라우저 창에서 Slack 권한 부여 대화 상자가 열립니다.

6. 새 창에서 팀 관리자 계정을 사용하여 Slack에 로그인합니다. 표시된 권한 부여 대화 상자에서 프로비전을 사용하도록 설정하려는 Slack 팀을 선택한 다음, **권한 부여**를 선택합니다. 완료되면 Azure Portal로 돌아가서 프로비전 구성을 완료합니다.

    ![권한 부여 대화 상자](./media/slack-provisioning-tutorial/Slack3.PNG)

7. Azure Portal에서 **연결 테스트**를 클릭하여 Azure AD가 Slack 앱에 연결되었는지 확인합니다. 연결에 실패하면 Slack 계정에 팀 관리자 권한이 있는지 확인하고 "권한 부여" 단계를 다시 시도합니다.

8. 프로비전 오류 알림을 받을 개인 또는 그룹의 전자 메일 주소를 **알림 전자 메일** 필드에 입력하고 아래 확인란을 선택합니다.

9. **저장**을 클릭합니다.

10. 매핑 섹션에서 **Azure Active Directory 사용자를 Slack에 동기화**를 선택합니다.

11. **특성 매핑** 섹션에서 Azure AD에서 Slack로 동기화할 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 Slack의 사용자 계정을 일치시키는 데 사용됩니다. 저장 단추를 선택하여 변경 내용을 커밋합니다.

12. Slack에 대한 Azure AD 프로비전 서비스를 사용하도록 설정하려면 **설정** 섹션에서 **프로비전 상태**를 **켜기**로 변경합니다.

13. **저장**을 클릭합니다.

사용자 및 그룹 섹션에서 Slack에 할당된 모든 사용자 및/또는 그룹의 초기 동기화가 시작됩니다. 초기 동기화는 서비스가 실행되는 동안 약 10분마다 발생하는 차후 동기화보다 수행하는 데 더 오래 걸립니다. **동기화 세부 정보** 섹션을 사용하여 진행 상태를 모니터링하고 Slack 앱에서 프로비전 서비스에서 수행하는 모든 작업을 설명하는 프로비전 작업 보고서에 연결된 링크를 이용할 수 있습니다.

## <a name="optional-configuring-group-object-provisioning-to-slack"></a>[옵션] Slack에 그룹 개체 프로비전 구성

필요에 따라 Azure AD에서 Slack으로 그룹 개체의 프로비전을 사용하도록 설정할 수 있습니다. 이는 실제 멤버 외에도 실제 그룹 개체가 Azure AD에서 Slack으로 복제된다는 점에서 "사용자 그룹 할당"과는 다릅니다. 예를 들어, Azure AD에서 “내 그룹”이라는 그룹이 있는 경우 Slack 내에 “내 그룹”이라는 동일한 그룹이 만들어집니다.

### <a name="to-enable-provisioning-of-group-objects"></a>그룹 개체 프로비전을 사용하도록 설정하려면 다음을 수행합니다.

1. 매핑 섹션에서 **Azure Active Directory 그룹을 Slack에 동기화**를 선택합니다.

2. 특성 매핑 블레이드에서 사용을 예로 설정합니다.

3. **특성 매핑** 섹션에서 Azure AD에서 Slack으로 동기화할 그룹 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 Slack의 그룹을 일치시키는 데 사용됩니다. 

4. **저장**을 클릭합니다.

이제 **사용자 및 그룹** 섹션의 Slack에 할당된 모든 그룹 개체는 Azure AD에서 Slack으로 완전하게 동기화됩니다. **동기화 세부 정보** 섹션을 사용하여 진행 상태를 모니터링하고 Slack 앱에서 프로비저닝 서비스에서 수행하는 모든 작업을 설명하는 프로비저닝 활동 로그에 연결된 링크를 이용할 수 있습니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../manage-apps/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="connector-limitations"></a>커넥터 제한 사항

* Slack의 **displayName** 특성을 구성할 때는 다음 동작에 유의하세요.

  * 값이 완전히 고유하지는 않습니다(예: 2명의 사용자가 동일한 표시 이름을 가질 수 있음).

  * 비영어 문자, 공백, 대/소문자를 지원합니다. 
  
  * 허용되는 문장 부호에는 마침표, 밑줄, 하이픈, 아포스트로피, 대괄호(예: **( [ { } ] )**) 및 구분 기호(예: **, / ;**)가 포함됩니다.
  
  * Slack의 작업 공간/조직에서 두 설정, 즉 **Profile syncing is enabled**(프로필 동기화 사용) 및 **Users cannot change their display name**(사용자가 표시 이름을 변경할 수 없음)이 구성된 경우에만 업데이트됩니다.
  
  * Slack의 **userName** 특성은 21자 미만이어야 하며 고유한 값을 가져야 합니다.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)
