---
title: '자습서: Cerner central Central에 대 한 사용자 프로 비전-Azure AD'
description: 사용자를 Cerner Central의 명단으로 자동으로 프로비전하도록 Azure Active Directory를 구성하는 방법을 알아봅니다.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.openlocfilehash: 58b991f5b229d924bc933ff34987db24bc895e10
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88529868"
---
# <a name="tutorial-configure-cerner-central-for-automatic-user-provisioning"></a>자습서: 동 사용자 프로비전을 위한 Cerner Central 구성

이 자습서의 목적은 사용자 계정을 Azure AD에서 Cerner Central의 사용자 명단으로 자동으로 프로비전 및 프로비전 해제하도록 Cerner Central 및 Azure AD에서 수행해야 하는 단계를 설명하는 것입니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

* Azure Active Directory 테넌트
* Cerner Central 테넌트

> [!NOTE]
> Azure Active Directory는 [SCIM](http://www.simplecloud.info/) 프로토콜을 사용하여 Cerner Central과 통합됩니다.

## <a name="assigning-users-to-cerner-central"></a>Cerner Central에 사용자 지정

Azure Active Directory는 "할당"이라는 개념을 사용하여 어떤 사용자가 선택한 앱에 대한 액세스를 받아야 하는지를 판단합니다. 자동 사용자 계정 프로비전의 컨텍스트에서는 Azure AD의 애플리케이션에 "할당된" 사용자 및 그룹만 동기화됩니다. 

프로비전 서비스를 구성하고 사용하도록 설정하기 전에 Cerner Central에 대한 액세스가 필요한 사용자를 대표하는 Azure AD의 사용자 및/또는 그룹을 결정해야 합니다. 결정했으면 다음 지시에 따라 이러한 사용자를 Cerner Central에 할당할 수 있습니다.

[엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cerner-central"></a>Cerner Central에 사용자 할당을 위한 주요 팁

* 프로비전 구성을 테스트하기 위해 단일 Azure AD 사용자를 Cerner Central에 할당하는 것이 좋습니다. 추가 사용자 및/또는 그룹은 나중에 할당할 수도 있습니다.

* 초기 테스트가 한 명의 사용자에 대해 완료되면 Cerner Central은 Cerner의 사용자 명단으로 프로비전되도록 모든 Cerner 솔루션(Cerner Central뿐 아니라)에 액세스하려는 사용자의 전체 목록을 할당하는 것을 권장합니다.  다른 Cerner 솔루션은 사용자 명단에서 사용자의 이 목록을 활용합니다.

* 사용자를 Cerner Central에 할당할 때 할당 대화 상자에서 **사용자** 역할을 선택해야 합니다. "기본 액세스" 역할이 있는 사용자는 프로비전에서 제외됩니다.

## <a name="configuring-user-provisioning-to-cerner-central"></a>Cerner Central에 사용자 프로비전 구성

이 섹션에서는 사용자의 Azure AD를 Cerner의 SCIM 사용자 계정 프로비전 API를 사용하여 Cerner Central의 사용자 명단에 연결하고, Azure AD의 사용자 및 그룹 할당을 기반으로 Cerner Central에서 할당된 사용자 계정을 만들고, 업데이트하고 비활성화하도록 프로비전 서비스를 구성하는 방법을 안내합니다.

> [!TIP]
> [Azure Portal](https://portal.azure.com)에 제공 된 지침에 따라 cerner central Central에 대해 SAML 기반 Single sign-on을 사용 하도록 선택할 수도 있습니다. Single Sign-On은 자동 프로비전과 별개로 구성할 수 있습니다. 하지만 이 두 가지 기능은 서로 보완적입니다. 자세한 내용은 [Cerner Central Single Sign-On 자습서](cernercentral-tutorial.md)를 참조하세요.

### <a name="to-configure-automatic-user-account-provisioning-to-cerner-central-in-azure-ad"></a>Azure AD에서 Cerner Central에 자동 사용자 계정 프로비전을 구성하려면 다음을 수행합니다.

Cerner Central으로 사용자 계정을 프로비전하기 위해 Cerner에서 Cerner Central 시스템 계정을 요청하고 Azure AD에서 Cerner의 SCIM 엔드포인트에 연결하는 데 사용할 수 있는 OAuth 전달자 토큰을 생성해야 합니다. 또한 프로덕션에 배포하기 전에 Cerner 샌드박스 환경에서 통합을 수행하는 것이 좋습니다.

1. 첫 번째 단계는 Cerner 및 Azure AD 통합을 관리하는 사용자에게 CernerCare 계정이 있는지 확인합니다. 이 계정은 지침을 완료하는 데 필요한 설명서에 액세스할 때 필요합니다. 필요한 경우 아래 URL을 사용하여 해당하는 각 환경에서 CernerCare 계정을 만들 수 있습니다.

   * 샌드박스: https://sandboxcernercare.com/accounts/create

   * 프로덕션: https://cernercare.com/accounts/create  

2. 다음으로 Azure AD에 대해 시스템 계정을 만들어야 합니다. 아래 지침에 따라 샌드박스 및 프로덕션 환경에 대한 시스템 계정을 요청하세요.

   * 지침: https://wiki.ucern.com/display/CernerCentral/Requesting+A+System+Account

   * 샌드박스: https://sandboxcernercentral.com/system-accounts/

   * 프로덕션: https://cernercentral.com/system-accounts/

3. 다음으로 각 시스템 계정에 대해 OAuth 전달자 토큰을 생성합니다. 이렇게 하려면 아래의 지침을 따르세요.

   * 지침: https://wiki.ucern.com/display/public/reference/Accessing+Cerner%27s+Web+Services+Using+A+System+Account+Bearer+Token

   * 샌드박스: https://sandboxcernercentral.com/system-accounts/

   * 프로덕션: https://cernercentral.com/system-accounts/

4. 마지막으로, 구성을 완료하려면 Cerner에서 샌드박스와 프로덕션 환경에 대한 사용자 명단 영역 ID를 획득해야 합니다. 이를 획득하는 방법에 대한 정보는 https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+SCIM을 참조하세요. 

5. 이제 Cerner로 사용자 계정을 프로비전하도록 Azure AD를 구성할 수 있습니다. [Azure Portal](https://portal.azure.com)에 로그인하고 **Azure Active Directory &gt; 엔터프라이즈 앱 &gt; 모든 애플리케이션** 섹션으로 이동합니다.

6. Single Sign-On에 대한 Cerner Central을 이미 구성한 경우 검색 필드를 사용하여 Cerner Central의 인스턴스를 검색합니다. 그렇지 않은 경우 **추가**를 선택하고 애플리케이션 갤러리에서 **Cerner Central**을 검색합니다. 검색 결과에서 Cerner Central을 선택하고 애플리케이션의 목록에 추가합니다.

7. Cerner Central의 인스턴스를 선택한 다음, **프로비전** 탭을 선택합니다.

8. **프로비전 모드**를 **자동**으로 설정합니다.

   ![Cerner Central 프로비저닝](./media/cernercentral-provisioning-tutorial/Cerner.PNG)

9. **관리자 자격 증명** 아래에서 다음 필드를 입력합니다.

   * **테넌트 URL** 필드에 아래 형식으로 URL을 입력합니다. 여기서 "User-Roster-Realm-ID"를 4단계에서 획득한 영역 ID로 바꿉니다.

    > 샌드박스: https://user-roster-api.sandboxcernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 
    > 
    > 프로덕션: https://user-roster-api.cernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 

   * **비밀 토큰** 필드에 3단계에서 생성한 OAuth 전달자 토큰을 입력하고 **연결 테스트**를 클릭합니다.

   * 포털의 오른쪽 맨 위에 성공 알림이 표시됩니다.

1. 프로비전 오류 알림을 받을 개인 또는 그룹의 전자 메일 주소를 **알림 전자 메일** 필드에 입력하고 아래 확인란을 선택합니다.

1. **저장**을 클릭합니다.

1. **특성 매핑** 섹션에서 Azure AD에서 Cerner Central로 동기화될 사용자 및 그룹 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 Cerner Central에서 사용자 계정 또는 그룹을 일치시키는 데 사용됩니다. 저장 단추를 선택하여 변경 내용을 커밋합니다.

1. Cerner Central에 대한 Azure AD 프로비전 서비스를 사용하도록 설정하려면 **설정** 섹션에서 **프로비전 상태**를 **켜기**로 변경합니다.

1. **저장**을 클릭합니다.

사용자 및 그룹 섹션에서 Cerner Central에 할당된 모든 사용자 및/또는 그룹의 초기 동기화가 시작됩니다. 초기 동기화는 Azure AD 프로비전 서비스가 실행되는 동안 약 40분마다 발생하는 후속 동기화보다 더 많은 시간이 걸립니다. **동기화 세부 정보** 섹션을 사용하여 진행 상태를 모니터링하고 Cerner Central 앱의 프로비저닝 서비스에서 수행하는 모든 작업을 설명하는 프로비저닝 활동 로그에 연결된 링크를 따를 수 있습니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../app-provisioning/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [Cerner Central: Azure AD를 사용하여 ID 데이터 게시](https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+Azure+AD)
* [자습서: Azure Active Directory를 사용한 Single Sign-On에 대한 Cerner Central 구성](cernercentral-tutorial.md)
* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [로그를 검토 하 고 프로 비전 활동에 대 한 보고서를 가져오는 방법에 대해 알아봅니다](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting).
