---
title: '자습서: Azure Active Directory로 자동 사용자 프로비전을 위한 Concur 구성 | Microsoft Docs'
description: Azure Active Directory와 Concur 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 678b7571d54c8938c163801a584ad813c6e5963f
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551169"
---
# <a name="tutorial-configure-concur-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비전을 위한 Concur 구성

이 자습서의 목적은 사용자 계정을 Azure AD에서 Concur로 자동으로 프로비전 및 프로비전 해제하도록 Concur 및 Azure AD에서 수행해야 하는 단계를 설명하는 것입니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

*   Azure Active Directory 테넌트.
*   Concur Single Sign-On이 설정된 구독
*   팀 관리자 권한이 있는 Concur의 사용자 계정

## <a name="assigning-users-to-concur"></a>Concur에 사용자 할당

Azure Active Directory는 "할당"이라는 개념을 사용하여 어떤 사용자가 선택한 앱에 대한 액세스를 받아야 하는지를 판단합니다. 자동 사용자 계정 프로비전의 컨텍스트에서는 Azure AD의 애플리케이션에 “할당된” 사용자 및 그룹만 동기화됩니다.

프로비전 서비스를 구성하고 사용하도록 설정하기 전에 Concur 앱에 대한 액세스가 필요한 사용자를 대표하는 Azure AD의 사용자 및/또는 그룹을 결정해야 합니다. 결정했으면 다음 지시에 따라 이러한 사용자를 Concur 앱에 할당할 수 있습니다.

[엔터프라이즈 앱에 사용자 또는 그룹 할당](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-concur"></a>Concur에 사용자 할당을 위한 주요 팁

*   프로비전 구성을 테스트하기 위해 단일 Azure AD 사용자를 Concur에 할당하는 것이 좋습니다. 추가 사용자 및/또는 그룹은 나중에 할당할 수도 있습니다.

*   Concur에 사용자를 할당할 때 유효한 사용자 역할을 선택해야 합니다. "기본 액세스" 역할은 프로비전에 작동하지 않습니다.

## <a name="enable-user-provisioning"></a>사용자 프로비저닝 사용

이 섹션에서는 사용자의 Azure AD를 Concur의 사용자 계정 프로비전 API에 연결하고, Azure AD의 사용자 및 그룹 할당을 기반으로 Concur에서 할당된 사용자 계정을 만들고, 업데이트하고 비활성화하도록 프로비전 서비스를 구성하는 방법을 안내합니다.

> [!Tip] 
> [Azure Portal](https://portal.azure.com)에 제공된 지침에 따라 Concur에 대해 SAML 기반 Single Sign-On을 사용하도록 선택할 수도 있습니다. Single Sign-On은 자동 프로비전과 별개로 구성할 수 있습니다. 하지만 이 두 가지 기능은 서로 보완적입니다.

### <a name="to-configure-user-account-provisioning"></a>사용자 계정 프로비전을 구성하려면

이 섹션은 Concur에 Active Directory 사용자 계정을 프로비전할 수 있도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

Expense Service에서 앱을 사용하도록 설정하려면 웹 서비스 관리자 프로필을 적절히 설치하고 사용해야 합니다. T&E 관리 기능으로 사용하는 기존 관리자 프로필에 WS 관리자 역할을 추가하지 마세요.

Concur 컨설턴트 또는 클라이언트 관리자는 고유한 웹 서비스 관리자 프로필을 만들어야 하고 클라이언트 관리자는 웹 서비스 관리자 기능에 해당 프로필을 사용해야 합니다(예: 앱 사용). 이러한 프로필은 클라이언트 관리자의 일별 T&E 관리자 프로필에서 별도로 유지되어야 합니다.(T&E 관리자 프로필에 할당된 WSAdmin 역할이 없어야 함)

앱을 사용하도록 설정하는 데 사용되는 프로필을 만드는 경우 사용자 프로필 필드에 클라이언트 관리자의 이름을 입력합니다. 이렇게 하면 프로필에 소유권이 할당됩니다. 하나 이상의 프로필이 만들어지면 클라이언트는 이 프로필로 로그인하여 [웹 서비스] 메뉴 내의 파트너 앱에 대해 “*사용*” 단추를 클릭해야 합니다.

다음과 같은 이유로 이 작업은 일반 T&E 관리에 사용하는 프로필을 사용하지 말아야 합니다.

* 앱을 사용하도록 설정한 후 표시되는 대화 창에서 클라이언트가 "*예*"를 클릭해야 합니다. 클릭함으로써 클라이언트는 파트너 애플리케이션이 데이터에 액세스하도록 승인하므로 사용자나 파트너가 해당 예 단추를 클릭할 수 없습니다.

* T&E 관리자 프로필을 사용하여 앱을 사용하도록 설정한 클라이언트 관리자가 퇴사하면(비활성화된 프로필이 발생함) 앱이 다른 WS 관리자 프로필로 설정될 때까지 해당 프로필을 사용하도록 설정된 앱은 작동하지 않습니다. 이 때문에 고유한 WS 관리자 프로필을 만들도록 합니다.

* 관리자가 퇴사하면 프로필이 비활성화되지 않아도 되기 때문에 활성화된 앱에 영향을 주지 않으면서도 원하는 경우 WS 관리자 프로필로 연결된 이름을 대체 관리자로 변경할 수 있습니다.

**사용자 프로비전을 구성하려면 다음 단계를 수행합니다.**

1. **Concur** 테넌트에 로그온합니다.

2. **관리** 메뉴에서 **웹 서비스**를 선택합니다.
   
    ![Concur 테넌트](./media/concur-provisioning-tutorial/IC721729.png "Concur 테넌트")

3. **웹 서비스** 창의 왼쪽에서 **파트너 응용 프로그램 사용**을 선택합니다.
   
    ![파트너 애플리케이션 사용](./media/concur-provisioning-tutorial/ic721730.png "파트너 애플리케이션 사용")

4. **응용 프로그램 사용** 목록에서 **Azure Active Directory**를 선택하고 **사용**을 클릭합니다.
   
    ![Microsoft Azure Active Directory](./media/concur-provisioning-tutorial/ic721731.png "Microsoft Azure Active Directory")

5. **예**를 클릭하여 **작업 확인** 대화 상자를 닫습니다.
   
    ![동작 확인](./media/concur-provisioning-tutorial/ic721732.png "동작 확인")

6. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory > Enterprise Apps > 모든 응용 프로그램** 섹션으로 이동 합니다.

7. Single Sign-On에 대한 Concur를 이미 구성한 경우 검색 필드를 사용하여 Concur의 인스턴스를 검색합니다. 그렇지 않은 경우 **추가**를 선택하고 애플리케이션 갤러리에서 **Concur**를 검색합니다. 검색 결과에서 Concur를 선택하고 애플리케이션의 목록에 추가합니다.

8. Concur의 인스턴스를 선택한 다음 **프로비전** 탭을 선택합니다.

9. **프로비전 모드**를 **자동**으로 설정합니다. 
 
    ![프로비전](./media/concur-provisioning-tutorial/provisioning.png)

10. **관리자 자격 증명** 섹션에서 Concur 관리자의 **사용자 이름** 및 **암호**를 입력합니다.

11. Azure Portal에서 **연결 테스트**를 클릭하여 Azure AD가 Concur 앱에 연결되었는지 확인합니다. 연결에 실패하면 Concur 계정에 팀 관리자 권한이 있는지 확인합니다.

12. 프로비전 오류 알림을 받을 개인 또는 그룹의 이메일 주소를 **알림 메일** 필드에 입력하고 확인란을 선택합니다.

13. **저장**을 클릭합니다.

14. 매핑 섹션에서 **Azure Active Directory 사용자를 Concur에 동기화**를 선택합니다.

15. **특성 매핑** 섹션에서 Azure AD에서 Concur로 동기화할 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 Concur의 사용자 계정을 일치시키는 데 사용됩니다. 저장 단추를 선택하여 변경 내용을 커밋합니다.

16. Concur에 대한 Azure AD 프로비전 서비스를 사용하도록 설정하려면 **설정** 섹션에서 **프로비전 상태**를 **켜기**로 변경합니다.

17. **저장**을 클릭합니다.

이제 테스트 계정을 만들 수 있습니다. 이제 최대 20분 동안 기다린 후 계정이 Concur에 동기화되었는지 확인합니다.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](tutorial-list.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)
* [Single Sign-on 구성](concur-tutorial.md)

