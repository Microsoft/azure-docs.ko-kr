---
title: '자습서: Azure Active Directory로 자동 사용자 프로비전을 위한 Salesforce Sandbox 구성 | Microsoft Docs'
description: Azure Active Directory와 Salesforce Sandbox 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 4666768d7e975e976601810bbbfadb1685fa91ff
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543526"
---
# <a name="tutorial-configure-salesforce-sandbox-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비전을 위한 Salesforce Sandbox 구성

이 자습서의 목적은 사용자 계정을 Azure AD에서 Salesforce Sandbox으로 자동 프로비전 및 프로비전 해제하도록 Salesforce Sandbox 및 Azure AD에서 수행해야 하는 단계를 설명하는 것입니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

*   Azure Active Directory 테넌트.
*   Salesforce Sandbox for Work 또는 Salesforce Sandbox for Education에 대한 유효한 테넌트가 있어야 합니다. 어느 서비스에나 평가판 계정을 사용할 수 있습니다.
*   팀 관리자 권한이 있는 Salesforce Sandbox의 사용자 계정.

## <a name="assigning-users-to-salesforce-sandbox"></a>Salesforce Sandbox에 사용자 할당

Azure Active Directory는 "할당"이라는 개념을 사용하여 어떤 사용자가 선택한 앱에 대한 액세스를 받아야 하는지를 판단합니다. 자동 사용자 계정 프로비전의 컨텍스트에서는 Azure AD의 애플리케이션에 "할당된" 사용자 및 그룹만 동기화됩니다.

프로비전 서비스를 구성하고 사용하도록 설정하기 전에 Salesforce Sandbox 앱에 대한 액세스가 필요한 Azure AD의 사용자 또는 그룹을 결정해야 합니다. 결정했으면 [엔터프라이즈 앱에 사용자 또는 그룹 할당](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)의 지침에 따라 해당 사용자를 Salesforce Sandbox 앱에 할당할 수 있습니다.

### <a name="important-tips-for-assigning-users-to-salesforce-sandbox"></a>Salesforce Sandbox에 사용자를 할당하기 위한 주요 팁

* 프로비전 구성을 테스트하기 위해 단일 Azure AD 사용자를 Salesforce Sandbox에 할당하는 것이 좋습니다. 추가 사용자 및/또는 그룹은 나중에 할당할 수도 있습니다.

* Salesforce Sandbox에 사용자를 할당할 때 유효한 사용자 역할을 선택해야 합니다. "기본 액세스" 역할은 프로비전에 작동하지 않습니다.

> [!NOTE]
> 이 앱은 프로비전 프로세스에서 Salesforce Sandbox의 사용자 지정 역할을 가져오며, 고객이 사용자를 할당할 때 선택할 수 있습니다.

## <a name="enable-automated-user-provisioning"></a>자동 사용자 프로비전 사용

이 섹션에서는 사용자의 Azure AD를 Salesforce Sandbox의 사용자 계정 프로비전 API에 연결하고, Azure AD의 사용자 및 그룹 할당을 기반으로 Salesforce Sandbox에서 할당된 사용자 계정을 만들고 업데이트하고 비활성화하도록 프로비전 서비스를 구성하는 방법을 안내합니다.

>[!Tip]
>[Azure Portal](https://portal.azure.com)에 제공된 지침에 따라 Salesforce Sandbox에 SAML 기반 Single Sign-On을 사용하도록 선택할 수도 있습니다. Single Sign-On은 자동 프로비전과 별개로 구성할 수 있습니다. 하지만 이 두 가지 기능은 서로 보완적입니다.

### <a name="configure-automatic-user-account-provisioning"></a>자동 사용자 계정 프로비전 구성

이 섹션은 Salesforce Sandbox에 Active Directory 사용자 계정을 사용자 프로비저닝할 수 있도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory > Enterprise Apps > 모든 응용 프로그램** 섹션으로 이동 합니다.

1. 이미 Salesforce Sandbox에 Single Sign-On을 구성한 경우 검색 필드를 사용하여 Salesforce Sandbox의 인스턴스를 검색합니다. 그렇지 않은 경우 **추가**를 선택하고 애플리케이션 갤러리에서 **Salesforce Sandbox**를 검색합니다. 검색 결과에서 Salesforce Sandbox를 선택하고 애플리케이션 목록에 추가합니다.

1. Salesforce Sandbox의 인스턴스를 선택한 다음 **프로비전** 탭을 선택합니다.

1. **프로비전 모드**를 **자동**으로 설정합니다.

    ![프로비전](./media/salesforce-sandbox-provisioning-tutorial/provisioning.png)

1. **관리자 자격 증명** 섹션에서 다음 구성 설정을 제공합니다.
   
    a. **관리 사용자 이름** 텍스트 상자에 Salesforce.com의 **시스템 관리자** 프로필이 할당된 Salesforce Sandbox 계정 이름을 입력합니다.
   
    b. **관리자 암호** 텍스트 상자에 이 계정의 암호를 입력합니다.

1. Salesforce Sandbox 보안 토큰을 얻으려면 새 탭을 열고 동일한 Salesforce Sandbox 관리자 계정에 로그인합니다. 페이지의 오른쪽 위 모서리에 있는 사용자 이름을 클릭하고 **설정**을 클릭합니다.

     ![자동 사용자 프로비전 사용](./media/salesforce-sandbox-provisioning-tutorial/sf-my-settings.png "자동 사용자 프로비전 사용")

1. 왼쪽 탐색 패널에서 **내 개인 정보**를 클릭하여 관련 섹션을 확장하고 **내 보안 토큰 재설정**을 클릭합니다.
  
    ![자동 사용자 프로비전 사용](./media/salesforce-sandbox-provisioning-tutorial/sf-personal-reset.png "자동 사용자 프로비전 사용")

1. **보안 토큰 재설정** 페이지에서 **보안 토큰 재설정** 단추를 클릭합니다.

    ![자동 사용자 프로비전 사용](./media/salesforce-sandbox-provisioning-tutorial/sf-reset-token.png "자동 사용자 프로비전 사용")

1. 이 관리자 계정과 연결된 전자 메일 받은 편지함을 확인합니다. Salesforce Sandbox.com에서 새 보안 토큰이 포함된 이메일을 찾습니다.

1. 토큰을 복사하고 Azure AD 창으로 이동하여 **비밀 토큰** 필드에 붙여넣습니다.

1. Azure Portal에서 **연결 테스트**를 클릭하여 Azure AD가 Salesforce Sandbox 앱에 연결할 수 있는지 확인합니다.

1. 프로비전 오류 알림을 받을 개인 또는 그룹의 메일 주소를 **알림 메일** 필드에 입력하고 확인란을 선택합니다.

1. **저장**을 클릭합니다.  
    
1.  매핑 섹션에서 **Azure Active Directory 사용자를 Salesforce Sandbox에 동기화**를 선택합니다.

1. **특성 매핑** 섹션에서 Azure AD에서 Salesforce Sandbox로 동기화할 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 Salesforce Sandbox의 사용자 계정을 일치시키는 데 사용됩니다. 저장 단추를 선택하여 변경 내용을 커밋합니다.

1. Salesforce Sandbox에 대한 Azure AD 프로비전 서비스를 사용하도록 설정하려면 설정 섹션에서 **프로비전 상태**를 **켜기**로 변경합니다.

1. **저장**을 클릭합니다.

사용자 및 그룹 섹션에서 Salesforce Sandbox에 할당된 모든 사용자 및/또는 그룹의 초기 동기화가 시작됩니다. 초기 동기화는 서비스가 실행되는 동안 약 40분마다 발생하는 후속 동기화보다 더 많은 시간이 걸립니다. **동기화 세부 정보** 섹션을 사용하여 진행 상태를 모니터링하고 Salesforce Sandbox 앱의 프로비저닝 서비스에서 수행하는 모든 작업을 설명하는 프로비저닝 활동 로그에 연결된 링크를 따를 수 있습니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../app-provisioning/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](tutorial-list.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)
* [Single Sign-on 구성](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-sandbox-tutorial)
