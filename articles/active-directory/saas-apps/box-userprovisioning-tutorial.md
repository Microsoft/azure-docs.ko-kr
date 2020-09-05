---
title: '자습서: Azure Active Directory로 자동 사용자 프로비전을 위한 Box 구성 | Microsoft Docs'
description: Azure Active Directory와 Box 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/20/2020
ms.author: jeedes
ms.openlocfilehash: 76c0f4f021646b4cbf1a09559e085176353a4a3c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88525615"
---
# <a name="tutorial-configure-box-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비전을 위한 Box 구성

이 자습서의 목적은 사용자 계정을 Azure AD에서 Box로 자동으로 프로비전 및 프로비전 해제하도록 Box 및 Azure AD에서 수행해야 하는 단계를 설명하는 것입니다.

> [!NOTE]
> 이 자습서에서는 Azure AD 사용자 프로비저닝 서비스에 기반하여 구축된 커넥터에 대해 설명합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

Box와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 테넌트
- Box Business 계획 이상

> [!NOTE]
> 이 자습서의 단계를 테스트 하는 경우 프로덕션 환경을 사용 *하지* 않는 것이 좋습니다.

> [!NOTE]
> 먼저 Box 응용 프로그램에서 앱을 사용 하도록 설정 해야 합니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 따릅니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="assigning-users-to-box"></a>Box에 사용자 할당 

Azure Active Directory는 "할당"이라는 개념을 사용하여 어떤 사용자가 선택한 앱에 대한 액세스를 받아야 하는지를 판단합니다. 자동 사용자 계정 프로비전의 컨텍스트에서는 Azure AD의 애플리케이션에 “할당된” 사용자 및 그룹만 동기화됩니다.

프로비전 서비스를 구성하고 사용하도록 설정하기 전에 Box 앱에 대한 액세스가 필요한 사용자를 대표하는 Azure AD의 사용자 및/또는 그룹을 결정해야 합니다. 결정했으면 다음 지시에 따라 이러한 사용자를 Box 앱에 할당할 수 있습니다.

[엔터프라이즈 앱에 사용자 또는 그룹 할당](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

## <a name="assign-users-and-groups"></a>사용자 및 그룹 할당
Azure Portal의 **Box > 사용자 및 그룹** 탭에서 Box에 대한 액세스 권한을 부여해야 하는 사용자 및 그룹을 지정할 수 있습니다. 사용자 또는 그룹을 할당하면 다음과 같은 상황이 발생합니다.

* Azure AD에서 할당된 사용자에게 Box에 인증하도록 허용합니다(직접 할당 또는 그룹 멤버 자격을 통해). 사용자가 할당되지 않은 경우에는 Azure AD에서 Box에 로그인하도록 허용하지 않으며 Azure AD 로그인 페이지에서 오류를 반환합니다.
* Box의 앱 타일이 사용자의 [애플리케이션 시작 관리자](../manage-apps/end-user-experiences.md)에 추가됩니다.
* 자동 프로비전이 설정된 경우 할당된 사용자 및/또는 그룹이 자동으로 프로비전되도록 프로비전 큐에 추가됩니다.
  
  * 사용자 개체만 프로비전되도록 구성된 경우 직접 할당된 모든 사용자가 프로비전 큐에 배치되며, 할당된 그룹의 멤버인 모든 사용자가 프로비전 큐에 배치됩니다. 
  * 그룹 개체가 프로비전되도록 구성된 경우 할당된 모든 그룹 개체 및 해당 그룹의 멤버인 모든 사용자가 Box에 프로비전됩니다. 그룹 및 사용자 멤버 자격은 Box에 기록될 때 유지됩니다.

**특성 > Single Sign-On** 탭을 사용하여 SAML 기반 인증 중에 Box에 표시되는 사용자 특성(또는 클레임)을 구성하고, **특성 > 프로비전** 탭을 사용하여 프로비전 작업 중에 사용자 및 그룹 특성이 Azure AD에서 Box로 이동하는 방식을 구성할 수 있습니다.

### <a name="important-tips-for-assigning-users-to-box"></a>Box에 사용자를 할당하기 위한 주요 팁 

*   프로비전 구성을 테스트하기 위해 단일 Azure AD 사용자를 Box에 할당하는 것이 좋습니다. 추가 사용자 및/또는 그룹은 나중에 할당할 수도 있습니다.

*   Box에 사용자를 할당할 때 유효한 사용자 역할을 선택해야 합니다. "기본 액세스" 역할은 프로비전에 작동하지 않습니다.

## <a name="enable-automated-user-provisioning"></a>자동 사용자 프로비전 사용

이 섹션에서는 사용자의 Azure AD를 Box의 사용자 계정 프로비전 API에 연결하고, Azure AD의 사용자 및 그룹 할당을 기반으로 Box에서 할당된 사용자 계정을 만들고, 업데이트하고 비활성화하도록 프로비전 서비스를 구성하는 방법을 안내합니다.

자동 프로비전이 설정된 경우 할당된 사용자 및/또는 그룹이 자동으로 프로비전되도록 프로비전 큐에 추가됩니다.
    
 * 사용자 개체만 프로비전되도록 구성된 경우 직접 할당된 사용자가 프로비전 큐에 배치되며, 할당된 그룹의 멤버인 모든 사용자가 프로비전 큐에 배치됩니다. 
    
 * 그룹 개체가 프로비전되도록 구성된 경우 할당된 모든 그룹 개체 및 해당 그룹의 멤버인 모든 사용자가 Box에 프로비전됩니다. 그룹 및 사용자 멤버 자격은 Box에 기록될 때 유지됩니다.

> [!TIP] 
> [Azure Portal](https://portal.azure.com)에 제공된 지침에 따라 Box에 대해 SAML 기반 Single Sign-On을 사용하도록 선택할 수도 있습니다. Single Sign-On은 자동 프로비전과 별개로 구성할 수 있습니다. 하지만 이 두 가지 기능은 서로 보완적입니다.

### <a name="to-configure-automatic-user-account-provisioning"></a>자동 사용자 계정 프로비전을 구성하려면

이 섹션은 Box에 Active Directory 사용자 계정을 프로비전할 수 있도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory > Enterprise Apps > 모든 응용 프로그램** 섹션으로 이동 합니다.

2. Single Sign-On에 대한 Box를 이미 구성한 경우 검색 필드를 사용하여 Box의 인스턴스를 검색합니다. 그렇지 않은 경우 **추가**를 선택하고 애플리케이션 갤러리에서 **Box**를 검색합니다. 검색 결과에서 Box를 선택하고 애플리케이션의 목록에 추가합니다.

3. Box의 인스턴스를 선택한 다음, **프로비전** 탭을 선택합니다.

4. **프로비전 모드**를 **자동**으로 설정합니다. 

    ![프로비전](./media/box-userprovisioning-tutorial/provisioning.png)

5. **관리자 자격 증명** 섹션 아래에서 **권한 부여**를 클릭하여 새 브라우저 창에서 Box 로그인 대화 상자를 엽니다.

6. **Box에 대한 액세스 권한 부여를 위한 로그인** 페이지에서 필요한 자격 증명을 제공한 다음 **권한 부여**를 클릭합니다. 
   
    ![자동 사용자 프로비전 사용](./media/box-userprovisioning-tutorial/IC769546.png "자동 사용자 프로비전 사용")

7. **Box에 액세스 허용**을 클릭하여 이 작업에 권한을 부여하고 Azure Portal로 돌아옵니다. 
   
    ![자동 사용자 프로비전 사용](./media/box-userprovisioning-tutorial/IC769549.png "자동 사용자 프로비전 사용")

8. Azure Portal에서 **연결 테스트**를 클릭하여 Azure AD가 Box 앱에 연결되었는지 확인합니다. 연결에 실패하면 Box 계정에 팀 관리자 권한이 있는지 확인하고 **"권한 부여"** 단계를 다시 시도합니다.

9. 프로비전 오류 알림을 받을 개인 또는 그룹의 이메일 주소를 **알림 메일** 필드에 입력하고 확인란을 선택합니다.

10. **저장**을 클릭합니다.

11. [매핑] 섹션에서 **Azure Active Directory 사용자를 Box에 동기화**를 선택합니다.

12. **특성 매핑** 섹션에서 Azure AD에서 Box로 동기화할 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 Box의 사용자 계정을 일치시키는 데 사용됩니다. 저장 단추를 선택하여 변경 내용을 커밋합니다.

13. Box에 대한 Azure AD 프로비전 서비스를 사용하도록 설정하려면 설정 섹션에서 **프로비전 상태**를 **켜기**로 변경합니다.

14. **저장**을 클릭합니다.

[사용자 및 그룹] 섹션에서 Box에 할당된 모든 사용자 및/또는 그룹의 초기 동기화가 시작됩니다. 초기 동기화는 서비스가 실행되는 동안 약 40분마다 발생하는 후속 동기화보다 더 많은 시간이 걸립니다. **동기화 세부 정보** 섹션을 사용하여 진행 상태를 모니터링하고 Box 앱의 프로비저닝 서비스에서 수행하는 모든 작업을 설명하는 프로비저닝 활동 로그에 연결된 링크를 따를 수 있습니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../app-provisioning/check-status-user-account-provisioning.md)를 참조하세요.

Box 테넌트에서 동기화된 사용자가 **관리 콘솔**의 **관리되는 사용자** 아래에 나열됩니다.

![통합 상태](./media/box-userprovisioning-tutorial/IC769556.png "통합 상태")


## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](tutorial-list.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)
* [Single Sign-on 구성](box-tutorial.md)
