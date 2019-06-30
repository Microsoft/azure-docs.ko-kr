---
title: Azure AD에서 SaaS 앱 사용자를 자동으로 프로비저닝 | Microsoft Docs
description: Azure AD를 사용하여 여러 타사 SaaS 애플리케이션에서 사용자 계정을 자동으로 프로비저닝, 프로비저닝 해제, 지속적으로 업데이트하는 방법을 소개합니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/12/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a58d2b235757faf760539f514ea349e33e12b41
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310013"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제

## <a name="what-is-automated-user-provisioning-for-saas-apps"></a>SaaS 앱을 위한 자동 사용자 프로비저닝이란?
Azure Active Directory (Azure AD)를 사용 하면 생성, 유지 관리 및 클라우드에서 사용자 id의 제거를 자동화할 수 있습니다 ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) Dropbox, Salesforce, ServiceNow 등과 같은 응용 프로그램입니다.

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

**이 기능이 있습니다.**

- 새로운 사람이 팀 또는 조직에 가입할 때 적절한 시스템에서 새 계정을 자동으로 만듭니다.
- 사용자가 팀 또는 조직을 떠날 때 적절한 시스템에서 계정을 자동으로 비활성화합니다.
- 디렉터리 또는 인사 관리 시스템의 변경 내용에 따라 앱 및 시스템의 ID가 최신 상태로 유지되도록 합니다.
- 그룹과 같이 사용자가 아닌 개체를 지원하는 애플리케이션에 해당 개체를 프로비전합니다.

**자동된 사용자 프로 비전에이 기능이 포함 됩니다.**

- 원본 및 대상 시스템 간에 기존 ID를 일치시킬 수 있는 기능
- 소스 시스템에서 대상 시스템으로 이동되어야 하는 사용자 데이터를 정의하는 사용자 지정 가능한 특성 매핑입니다.
- 프로비전닝 오류를 전자 메일로 받을 수 있는 선택적 기능.
- 모니터링 및 문제 해결에 도움이 되는 보고 및 활동 로그.

## <a name="why-use-automated-provisioning"></a>자동 프로비전을 사용하는 이유는 무엇입니까?

이 기능을 사용하게 되는 일반적인 동기는 다음과 같습니다.

- 수동 프로비저닝 절차에서 빚어지는 비용, 비효율성, 사람의 실수를 방지합니다.
- 사용자 지정 개발된 프로비저닝 솔루션과 스크립트를 호스트하고 유지하는 데 관련된 비용을 방지합니다.
- 즉시 조직을 떠날 때 주요 SaaS 앱에서 사용자의 id를 제거 하 여 조직의 보호 합니다.
- 특정 SaaS 응용 프로그램 또는 시스템에 많은 수의 사용자를 쉽게 가져오고 있습니다.
- 단일 정책 집합을 프로 비전 되는 앱에 로그인 할 수 있는 사용자를 확인 하는 데 있습니다.

## <a name="how-does-automatic-provisioning-work"></a>자동 프로비전은 어떻게 작동합니까?
    
합니다 **Azure AD 프로 비전 서비스** 각 응용 프로그램 공급 업체에서 제공 하는 사용자 관리 API 끝점에 연결 하 여 SaaS 앱 및 다른 시스템에 사용자를 프로 비전 합니다. 이러한 사용자 관리 API 엔드포인트를 사용하면 Azure AD에서 프로그래밍 방식으로 사용자를 만들고, 업데이트하고, 제거할 수 있습니다. 선택한 응용 프로그램의 경우 프로 비전 서비스를 만들고 수도 있습니다, 업데이트, 그룹 및 역할과 같은 추가 id 관련 개체를 제거 합니다. 

![프로비전](./media/user-provisioning/provisioning0.PNG)
*그림 1: Azure AD Provisioning Service*

![아웃바운드 프로비전](./media/user-provisioning/provisioning1.PNG)
*그림 2: Azure AD에서 인기 있는 SaaS 애플리케이션으로의 “아웃바운드” 사용자 프로비저닝 워크플로*

![인바운드 프로비전](./media/user-provisioning/provisioning2.PNG)
*그림 3: 인기 있는 HCM(Human Capital Management) 애플리케이션에서 Azure Active Directory 및 Windows Server Active Directory로의 “인바운드”사용자 프로비저닝 워크플로*


## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Azure AD 자동 사용자 프로비전에서 사용할 수 있는 애플리케이션과 시스템은 무엇입니까?

Azure AD 기능 많은 인기 있는 SaaS 앱 및 인사 관리 시스템에 대 한 지원 및 SCIM 2.0 표준의 특정 부분을 구현 하는 앱에 대 한 일반 지원에 미리 통합 합니다.

### <a name="pre-integrated-applications"></a>사전 통합된 애플리케이션

Azure AD가 미리 통합된 프로비저닝 커넥터를 지원하는 모든 애플리케이션 목록을 보려면 [사용자 프로비저닝에 대한 애플리케이션 자습서 목록](../saas-apps/tutorial-list.md)을 참조하세요.

Azure AD 엔지니어링 팀에 문의하여 추가 애플리케이션에 대한 프로비전 지원을 요청하려면 [Azure Active Directory 피드백 포럼](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests/filters/new?category_id=172035)을 통해 메시지를 제출하세요.

> [!NOTE]
> 애플리케이션에서 자동화된 사용자 프로비저닝을 지원하려면, 먼저 외부 프로그램에서 사용자 만들기, 유지 관리 및 제거를 자동화하는 데 필요한 사용자 관리 API를 제공해야 합니다. 따라서 모든 SaaS 앱이 이 기능과 호환되지는 않습니다. 사용자 관리 Api를 지 원하는 앱에 대 한 Azure AD 엔지니어링 팀의 경우 다음 이러한 앱에 프로 비전 커넥터를 빌드할 수 및이 작업은 현재와 잠재 고객의 요구 사항에 따라 우선 순위가 지정 됩니다. 

### <a name="connecting-applications-that-support-scim-20"></a>SCIM 2.0을 지원하는 애플리케이션 연결

SCIM 2.0 기반 사용자 관리 API를 구현하는 애플리케이션에 일반적으로 연결하는 방법에 대한 자세한 내용은 [SCIM을 사용하여 사용자 및 그룹을 Azure Active Directory에서 애플리케이션으로 자동으로 프로비전](use-scim-to-provision-users-and-groups.md)을 참조하세요.

    
## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>애플리케이션에 자동 프로비전을 설정하려면 어떻게 합니까?

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

선택한 응용 프로그램에 대 한 서비스를 프로 비전 하는 Azure AD를 구성 하는 Azure Active Directory 포털을 사용 합니다.

1. 엽니다는  **[Azure Active Directory 포털](https://aad.portal.azure.com)** 합니다.

1. 선택 **엔터프라이즈 응용 프로그램** 왼쪽된 창에서. 모든 구성 된 앱 목록에는 표시 됩니다.

1. 선택할 **+ 새 응용 프로그램** 는 응용 프로그램을 추가 합니다. 시나리오에 따라 다음 중 하나를 추가 합니다.

   - 합니다 **고유한 응용 프로그램 추가** 옵션은 사용자 지정 개발 된 SCIM 통합을 지원 합니다.

   - 모든 응용 프로그램을 **갤러리에서 추가** > **주요 응용 프로그램** 섹션 자동 프로비저닝을 지원 합니다. 추가 애플리케이션을 보려면 [사용자 프로비전에 대한 애플리케이션 자습서 목록](../saas-apps/tutorial-list.md)을 참조하세요.

1. 세부 정보를 제공 하 고 선택 **추가**합니다. 새 앱을 엔터프라이즈 응용 프로그램 목록에 추가 되 고 해당 응용 프로그램 관리 화면으로 열립니다.

1. 선택 **프로 비전** 앱에 대 한 설정을 프로 비전 하는 사용자 계정을 관리할 수 있습니다.

   ![설정](./media/user-provisioning/provisioning_settings0.PNG)

1. 자동 옵션을 선택 합니다 **프로 비전 모드** 관리자 자격 증명, 시작 및 중지, 매핑 및 동기화에 대 한 설정을 지정할 수 있습니다.

   - 확장 **관리자 자격 증명** Azure AD 응용 프로그램의 사용자 관리 API에 연결 하는 데 필요한 자격 증명을 입력 합니다. 이 섹션에서는 또한 자격 증명 실패 하거나 프로비저닝 작업이 들어가는 경우 전자 메일 알림을 사용 하도록 설정할 수 있습니다 [격리](#quarantine)합니다.

   - 확장 **매핑을** Azure AD 사이 흐르는 사용자 특성 보기 및 편집 하 고 사용자 계정을 프로 비전 되거나 업데이트 되 면 대상 응용 프로그램. 대상 응용 프로그램에서 지원할 경우이 섹션에서는 필요에 따라 그룹 및 사용자 계정을 프로 비전을 구성할 수 있습니다. 오른쪽으로 확인 하 고 사용자 특성을 사용자 지정할 수 있는 매핑 편집기를 열고 테이블의 매핑을 선택 합니다.
   
     **범위 지정 필터** 사용자 프로 비전 서비스에 지시 하 고 원본 시스템에서 그룹을 프로 비전 또는 대상 시스템에 프로 비전을 해제 해야 합니다. 에 **특성 매핑** 창 **원본 개체 범위** 특정 특성 값에 필터를 적용 합니다. 예를 들어 "Department" 특성이 "Sales"인 사용자만 프로비전 범위에 속해야 한다고 지정할 수 있습니다. 자세한 내용은 [범위 지정 필터 사용](define-conditional-rules-for-provisioning-user-accounts.md)을 참조하세요.
    
     자세한 내용은 [특성 매핑 사용자 지정](customize-application-attributes.md)을 참조하세요.

   - **설정** 현재 실행 여부를 포함 하 여 응용 프로그램에 대 한 프로 비전 서비스의 작업을 제어 합니다. 합니다 **범위** 메뉴를 사용 하면 Azure AD 디렉터리의 모든 사용자를 프로 비전 해야 하는 경우 또는 할당 된 사용자 및 그룹 프로 비전 범위에 있어야 하는지 여부를 지정 합니다. 사용자 및 그룹 "할당"에 대한 자세한 내용은 [Azure Active Directory에서 엔터프라이즈 앱에 사용자 또는 그룹 할당](assign-user-or-group-access-portal.md)을 참조하세요.

앱 관리 화면에서 선택 **감사 로그** 보려면 모든 작업의 레코드 실행 Azure AD에서 서비스를 프로 비전 합니다. 자세한 내용은 [프로비전 보고 가이드](check-status-user-account-provisioning.md)를 참조하세요.

![설정](./media/user-provisioning/audit_logs.PNG)

> [!NOTE]
> [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)를 사용하여 Azure AD 사용자 프로비저닝 서비스를 구성 및 관리할 수도 있습니다.


## <a name="what-happens-during-provisioning"></a>프로비전하는 동안 어떻게 됩니까?

Azure AD가 소스 시스템인 경우 프로비저닝 서비스는 [Azure AD Graph API의 차등 쿼리 기능](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query)을 사용하여 사용자와 그룹을 모니터링합니다. 프로비저닝 서비스는 소스 시스템 및 대상 시스템에 대해 초기 동기화를 실행한 다음, 주기적 증분 동기화를 실행합니다. 

### <a name="initial-sync"></a>초기 동기화

프로 비전 서비스를 시작할 때 실행 하는 첫 번째 동기화가 됩니다.

1. [특성 매핑](customize-application-attributes.md)에 정의된 모든 특성을 검색하여 소스 시스템에서 모든 사용자와 그룹을 쿼리합니다.
2. 구성된 [할당](assign-user-or-group-access-portal.md) 또는 [특성 기반 범위 지정 필터](define-conditional-rules-for-provisioning-user-accounts.md)를 사용하여 반환된 사용자 및 그룹을 필터링합니다.
3. 사용자가 할당 하는 경우 또는 서비스 프로 비전에 대 한 범위에서 지정 된 일치 하는 사용자에 대 한 대상 시스템을 쿼리하여 [일치 특성](customize-application-attributes.md#understanding-attribute-mapping-properties)합니다. 예제: 원본 시스템의 userPrincipal 이름이 일치하는 특성이고 대상 시스템의 userName에 매핑되는 경우, 프로비전 서비스는 대상 시스템에서 원본 시스템의 userPrincipal 이름 값과 일치하는 userNames를 쿼리합니다.
4. 일치 하는 사용자 대상 시스템에 없으면 원본 시스템에서 반환 하는 특성을 사용 하 여 생성 됩니다. 사용자 계정을 만든 후 프로 비전 서비스를 검색 하 고 해당 사용자의 모든 이후 작업을 실행 하는 데 사용 되는 새 사용자에 대 한 대상 시스템의 ID를 캐시 합니다.
5. 일치 하는 사용자가 있으면 소스 시스템에서 제공 하는 특성을 사용 하 여 업데이트 됩니다. 사용자 계정에 일치 한 후 프로 비전 서비스를 검색 하 고 해당 사용자의 모든 이후 작업을 실행 하는 데 사용 되는 새 사용자에 대 한 대상 시스템의 ID를 캐시 합니다.
6. 특성 매핑 "참조" 특성이 들어 있는 경우 서비스를 만들고 참조 된 개체를 연결할 대상 시스템에 대 한 추가 업데이트를 수행 합니다. 예를 들어 대상 시스템에 있는 한 사용자의 “Manager” 특성이 대상 시스템에서 생성된 다른 사용자에 연결되어 있을 수 있습니다.
7. 나중에 증분 동기화의 시작점을 제공 하는 초기 동기화 후에 워터 마크를 저장 합니다.

사용자 프로비저닝 뿐 아니라 그룹 및 해당 멤버 프로비저닝도 ServiceNow, G Suite 및 Box 지원과 같은 일부 응용 프로그램입니다. 그룹 프로 비전을 활성화 한 경우 이러한 경우에는 [매핑을](customize-application-attributes.md), 프로 비전 서비스 사용자 및 그룹을 동기화 하 고 다음 나중에 동기화 그룹 멤버 자격. 

### <a name="incremental-syncs"></a>증분 동기화

초기 동기화 후 다른 모든 동기화 작업이 수행 됩니다.

1. 소스 시스템에서 마지막 워터마크가 저장된 이후 업데이트된 사용자 및 그룹을 쿼리합니다.
2. 구성된 [할당](assign-user-or-group-access-portal.md) 또는 [특성 기반 범위 지정 필터](define-conditional-rules-for-provisioning-user-accounts.md)를 사용하여 반환된 사용자 및 그룹을 필터링합니다.
3. 사용자가 할당 하는 경우 또는 서비스 프로 비전에 대 한 범위에서 지정 된 일치 하는 사용자에 대 한 대상 시스템을 쿼리하여 [일치 특성](customize-application-attributes.md#understanding-attribute-mapping-properties)합니다.
4. 일치 하는 사용자 대상 시스템에 없으면 원본 시스템에서 반환 하는 특성을 사용 하 여 생성 됩니다. 사용자 계정을 만든 후 프로 비전 서비스를 검색 하 고 해당 사용자의 모든 이후 작업을 실행 하는 데 사용 되는 새 사용자에 대 한 대상 시스템의 ID를 캐시 합니다.
5. 일치 하는 사용자가 있으면 소스 시스템에서 제공 하는 특성을 사용 하 여 업데이트 됩니다. 일치 하는 새로 할당 된 계정의 경우 프로 비전 서비스를 검색 하 고 해당 사용자의 모든 이후 작업을 실행 하는 데 사용 되는 새 사용자에 대 한 대상 시스템의 ID를 캐시 합니다.
6. 특성 매핑 "참조" 특성이 들어 있는 경우 서비스를 만들고 참조 된 개체를 연결할 대상 시스템에 대 한 추가 업데이트를 수행 합니다. 예를 들어 대상 시스템에 있는 한 사용자의 “Manager” 특성이 대상 시스템에서 생성된 다른 사용자에 연결되어 있을 수 있습니다.
7. 이전에 프로비저닝 범위에 있던 사용자가 범위에서 제거되면(할당 취소되는 경우 포함), 서비스는 업데이트를 통해 대상 시스템에서 사용자를 해제합니다.
8. 이전에 프로비저닝 범위에 있던 사용자가 소스 시스템에서 해제 또는 일시 삭제되면 서비스는 업데이트를 통해 대상 시스템에서 사용자를 해제합니다.
9. 이전에 프로비저닝 범위에 있던 사용자가 소스 시스템에서 영구 삭제되면 서비스는 대상 시스템에서 사용자를 삭제합니다. Azure AD에서 사용자는 일시 삭제 된 면 하드 삭제 된 30 일.
10. 증분 동기화는 나중에 증분 동기화의 시작점을 제공 하는 끝에 새 워터 마크를 저장 합니다.

>[!NOTE]
> 선택적으로 해제할 수 있습니다는 **만들기**, **업데이트**, 또는 **삭제** 를 사용 하 여 작업을 **대상 개체 작업** 확인란을 [매핑을](customize-application-attributes.md) 섹션입니다. 업데이트 중에 사용자를 해제하는 논리도 “accountEnabled”와 같은 필드의 특성 매핑을 통해 제어됩니다.

프로 비전 서비스에 정의 된 간격 연속 증분 동기화를 무기한으로 실행을 계속 합니다 [각 응용 프로그램에 관련 된 자습서](../saas-apps/tutorial-list.md)다음 이벤트 중 하나가 발생할 때까지:

- 서비스가 Azure Portal을 사용하여 수동으로 중지되거나 적절한 Graph API 명령을 사용하여 중지됩니다. 
- Azure Portal의 **상태 지우기 및 다시 시작** 옵션을 사용하거나 적절한 Graph API 명령을 사용하여 새 초기 동기화가 트리거됩니다. 이 그러면 모든 저장 된 워터 마크가 지우고 모든 소스 개체가 다시 평가 됩니다.
- 특성 매핑 또는 범위 지정 필터가 변경 되어 새 초기 동기화가 트리거됩니다. 이 동작은 또한 모든 저장 된 워터 마크가 지우면 모든 소스 개체가 다시 평가 됩니다.
- 프로 비전 프로세스 격리 (아래 참조)에 높은 오류 비율로 인해 들어가고 4 주 넘게 격리 상태로 유지 됩니다. 이 이벤트가 발생하면 자동으로 서비스를 사용할 수 없게 됩니다.

### <a name="errors-and-retries"></a>오류 및 다시 시도

개별 사용자는 추가, 업데이트 또는 대상 시스템에서 오류로 인해 대상 시스템에서 삭제 될 수 없습니다, 하는 경우 작업을 다음 동기화 주기에서 시도 됩니다. 사용자가 계속 실패하면 감소된 빈도로 다시 시도되기 시작하여, 점차 하루에 한 번만 시도되는 빈도까지 축소됩니다. 오류를 해결 하려면 관리자가 확인 해야 합니다 [감사 로그](check-status-user-account-provisioning.md) "프로세스 에스크로"에 대 한 루트를 확인 하는 이벤트 발생 및 적절 한 조치를 취합니다. 일반적인 오류는 다음과 같습니다.

- 대상 시스템에서 필요한 특성이 사용자의 소스 시스템에 채워져 있지 않음
- 대상 시스템에서 unique 제약 조건이 되는 원본 시스템의 특성 값과 동일한 값을 가진 사용자가 다른 사용자 레코드에

이러한 오류는 소스 시스템에서 영향을 받는 사용자의 특성 값을 조정하거나 충돌이 발생하지 않도록 특성 매핑을 조정하여 해결할 수 있습니다.   

### <a name="quarantine"></a>격리

전체 또는 대부분을 호출 하는 대상 시스템에 대해 일관 되 게 (예: 잘못 된 관리자 자격 증명) 오류로 인해 실패 하는 경우 프로비저닝 작업이 "격리" 상태로 전환 됩니다. 이 상태에서 표시 되는 [프로 비전 요약 보고서](check-status-user-account-provisioning.md) 및 Azure portal에서 전자 메일 알림이 구성 된 경우 전자 메일을 통해. 

격리 중에 증분 동기화 빈도는 하루에 한 번까지 점차 감소됩니다. 

고정은 잘못 된 오류가 모두 다음 동기화 주기가 시작 된 후 프로비저닝 작업이 격리에서 제거 됩니다. 프로비저닝 작업이 4주 넘게 격리 상태로 유지되면 프로비저닝 작업을 사용할 수 없게 됩니다.


## <a name="how-long-will-it-take-to-provision-users"></a>사용자를 프로비전하는 데 걸리는 시간은 어느 정도인가요?

성능 프로 비전 작업의 실행 여부는 초기 프로 비전 주기는 증분 주기에 따라 달라 집니다. 프로 비전 시간 사용에 대 한 세부 정보 및 프로 비전 서비스의 상태를 모니터링 하는 방법에 대 한 참조 [사용자 프로 비전의 상태를 확인할](application-provisioning-when-will-provisioning-finish-specific-user.md)합니다. 

## <a name="how-can-i-tell-if-users-are-being-provisioned-properly"></a>사용자가 적절히 프로비전되는지 어떻게 확인할 수 있나요?

Azure AD에서 사용자 프로 비전 서비스에서 실행 되는 모든 작업 기록 로그를 감사 합니다. 여기에 모든 읽기 및 쓰기 작업 원본 및 대상 시스템 및 사용자 데이터를 읽거나 각 작업 중에 기록 하려고 합니다.

Azure portal에서 감사 로그를 읽는 방법에 대 한 자세한 내용은 참조는 [프로 비전 보고 가이드](check-status-user-account-provisioning.md)합니다.


## <a name="how-do-i-troubleshoot-issues-with-user-provisioning"></a>사용자 프로비저닝 문제를 어떻게 해결하나요?

자동 사용자 프로비전 문제를 해결하는 방법에 대한 시나리오 기반 지침은 [애플리케이션에 사용자를 구성 및 프로비전하는 문제](application-provisioning-config-problem.md)를 참조하세요.


## <a name="what-are-the-best-practices-for-rolling-out-automatic-user-provisioning"></a>자동 사용자 프로비저닝을 롤아웃하기 위한 모범 사례는 무엇인가요?

> [!VIDEO https://www.youtube.com/embed/MAy8s5WSe3A]

애플리케이션에 대한 아웃바운드 사용자 프로비저닝에 대한 단계별 배포 계획 예제는 [사용자 프로비저닝에 대한 ID 배포 가이드](https://aka.ms/userprovisioningdeploymentplan)를 참조하세요.

## <a name="more-frequently-asked-questions"></a>기타 질문과 대답

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-b2b-users-in-azure-ad"></a>SaaS 앱에 대한 자동 사용자 프로비저닝이 Azure AD의 B2B 사용자에게 작동하나요?

예, SaaS 응용 프로그램에 Azure AD에서 서비스를 프로 비전 B2B (또는 게스트) 사용자 프로 비전 하는 Azure AD 사용자를 사용 하는 것이 같습니다.

그러나 Azure AD를 사용 하 여 SaaS 응용 프로그램에 로그인 하는 B2B 사용자에 대 한 SaaS 응용 프로그램의 SAML 기반 single sign on 기능을 특정 방식에서으로 구성 있어야 합니다. B2B 사용자의 로그인을 지원하도록 SaaS 애플리케이션을 구성하는 방법에 대한 자세한 내용은 [B2B 협업을 위한 SaaS 앱 구성]( https://docs.microsoft.com/azure/active-directory/b2b/configure-saas-apps)을 참조하세요.

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-dynamic-groups-in-azure-ad"></a>SaaS 앱에 대한 자동 사용자 프로비저닝이 Azure AD의 동적 그룹에 작동하나요?

예. "동기화만 할당 된 사용자 및 그룹" 하도록 프로 비전 서비스는 Azure AD 사용자 프로 비전 할 수 또는 멤버인 있는지 여부에 따라 SaaS 응용 프로그램에서 프로 비전 해제 사용자를 [동적 그룹](../users-groups-roles/groups-create-rule.md)합니다. 또한 동적 그룹은 “모든 사용자 및 그룹 동기화” 옵션을 사용할 때도 작동합니다.

그러나 동적 그룹의 사용은 Azure AD에서 SaaS 애플리케이션으로의 엔드투엔드 사용자 프로비저닝에 대한 전체 성능에 영향을 줄 수 있습니다. 동적 그룹을 사용 하는 경우 이러한 제한 사항 및 권장 사항을 염두에서에 둡니다.

- 동적 그룹의 사용자가 SaaS 애플리케이션에서 프로비전 또는 프로비전 해제되는 속도는 동적 그룹이 멤버 자격 변경을 평가하는 속도에 따라 좌우됩니다. 동적 그룹의 처리 상태를 확인하는 방법에 대한 내용은 [멤버 자격 규칙에 대한 처리 상태 확인](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)을 참조하세요.

- 동적 그룹을 사용 하는 경우 사용자 프로 비전 및 프로 비전 해제 이벤트에서 멤버 자격 결과의 손실을 의미를 염두에 프로 비전 해제를 사용 하 여 규칙을 신중 하 게 고려 되어야 합니다.

### <a name="does-automatic-user-provisioning-to-saas-apps-work-with-nested-groups-in-azure-ad"></a>SaaS 앱에 대한 자동 사용자 프로비저닝이 Azure AD의 중첩 그룹에 작동하나요?

아니요. "동기화만 할당 된 사용자 및 그룹"에 구성 되 면 Azure AD 사용자 프로 비전 서비스 읽기 또는 중첩 된 그룹에 있는 사용자를 프로 비전 할 수 없습니다. 것만 읽고 명시적으로 할당된 된 그룹의 직접 구성원 인 사용자를 프로 비전 할 수 있습니다.

이것은 Single Sign-On에도 영향을 미치는 “애플리케이션에 대한 그룹 기반 할당”의 제한 사항으로, [그룹을 사용하여 SaaS 애플리케이션에 대한 액세스 관리](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-saasapps )에 설명되어 있습니다.

이 문제를 해결 하면 명시적으로 할당 해야 (또는 기타 수단 [범위](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)) 프로 비전 해야 하는 사용자를 포함 하는 그룹입니다.

### <a name="is-provisioning-between-azure-ad-and-a-target-application-using-an-encrypted-channel"></a>Azure AD 간의 프로 비전 하 고 암호화 된 채널을 사용 하 여 대상 응용 프로그램을?

예. 서버 대상에 대 한 HTTPS SSL 암호화를 사용 했습니다. 

## <a name="related-articles"></a>관련 문서

- [SaaS App을 통합하는 방법에 대한 자습서 목록](../saas-apps/tutorial-list.md)
- [사용자 프로비저닝에 대한 특성 매핑 사용자 지정](customize-application-attributes.md)
- [특성 매핑에 대한 식 작성](functions-for-customizing-application-data.md)
- [사용자 프로 비전에 대 한 필터 범위 지정](define-conditional-rules-for-provisioning-user-accounts.md)
- [SCIM를 사용하여 Azure Active Directory으로부터 애플리케이션에 사용자 및 그룹의 자동 프로비전 사용](use-scim-to-provision-users-and-groups.md)
- [Azure AD 동기화 API 개요](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
