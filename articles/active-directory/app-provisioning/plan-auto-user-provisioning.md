---
title: Azure Active Directory에 대 한 자동 사용자 프로 비전 배포 계획
description: 자동 사용자 프로비저닝을 계획 하 고 실행 하기 위한 지침
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 10/17/2019
ms.author: kenwith
ms.reviewer: arvindha, celested
ms.openlocfilehash: 24de1b2cfc5e0804039923f23b6a5cd9f6c6fb80
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88235707"
---
# <a name="plan-an-automatic-user-provisioning-deployment"></a>자동 사용자 프로비저닝 배포 계획

많은 조직에서는 최종 사용자 생산성을 위해 ServiceNow, Zscaler 및 여유 시간과 같은 SaaS (software as a service) 응용 프로그램을 사용 합니다. 지금까지 IT 직원은 CSV 파일 업로드 또는 사용자 지정 스크립트를 사용 하 여 각 SaaS 응용 프로그램에서 사용자 id를 안전 하 게 관리 하는 등의 수동 프로 비전 방법에 의존 했습니다. 이러한 프로세스는 오류가 발생 하기 쉬우며 안전 하지 않으며 관리가 어렵습니다.

Azure Active Directory (Azure AD) 자동 사용자 프로 비전은 비즈니스 규칙에 따라 SaaS 응용 프로그램에서 사용자 id의 생성, 유지 관리 및 제거를 안전 하 게 자동화 하 여이 프로세스를 간소화 합니다. 이러한 자동화를 통해 클라우드 기반 솔루션에 대 한 종속성을 확장할 때 클라우드 전용 및 하이브리드 환경에서 id 관리 시스템을 효과적으로 확장할 수 있습니다.

기능을 더 잘 이해 하려면 [Azure Active Directory를 사용 하 여 SaaS 응용 프로그램에 사용자 프로 비전 및 프로 비전](../app-provisioning/user-provisioning.md) 해제를 참조 하세요.

## <a name="learn"></a>배우기

사용자 프로 비전은 지속적인 id 거 버 넌 스에 대 한 토대를 만들고 신뢰할 수 있는 id 데이터를 사용 하는 비즈니스 프로세스의 품질을 향상 시킵니다.

### <a name="key-benefits"></a>주요 이점

자동 사용자 프로 비전을 사용 하는 경우의 주요 이점은 다음과 같습니다.

* **생산성 향상**. 단일 사용자 프로비저닝 관리 인터페이스를 사용 하 여 SaaS 응용 프로그램에서 사용자 id를 관리할 수 있습니다. 이 인터페이스에는 단일 프로 비전 정책 집합이 있습니다.

* **위험을 관리**합니다. 역할 및/또는 액세스를 정의 하는 직원 상태 또는 그룹 멤버 자격에 따라 변경 내용을 자동화 하 여 보안을 강화할 수 있습니다.

* **주소 규정 준수 및 거 버 넌 스**. Azure AD는 모든 사용자 프로 비전 요청에 대해 기본 감사 로그를 지원 합니다. 요청은 원본 및 대상 시스템에서 실행 됩니다. 이를 통해 단일 화면에서 응용 프로그램에 액세스할 수 있는 사용자를 추적할 수 있습니다.

* **비용 절감**. 자동 사용자 프로 비전은 수동 프로 비전과 관련 된 비효율성 및 인간 오류를 방지 하 여 비용을 절감 합니다. 사용자 지정 개발 된 사용자 프로 비전 솔루션, 스크립트 및 감사 로그의 필요성을 줄여 줍니다.

### <a name="licensing"></a>라이선스

Azure AD는 응용 프로그램 갤러리 메뉴에 제공 된 템플릿을 사용 하 여 모든 응용 프로그램의 셀프 서비스 통합을 제공 합니다. 라이선스 요구 사항에 대 한 전체 목록은 [AZURE AD 라이선스 페이지](https://azure.microsoft.com/pricing/details/active-directory/)를 참조 하세요.

#### <a name="application-licensing"></a>응용 프로그램 라이선스

자동으로 프로 비전 하려는 응용 프로그램에 적합 한 라이선스가 필요 합니다. 응용 프로그램에 할당 된 사용자에 게 응용 프로그램 역할에 대 한 적절 한 라이선스가 있는지 여부를 응용 프로그램 소유자와 논의 합니다. Azure AD에서 역할에 따라 자동 프로 비전을 관리 하는 경우 Azure AD에서 할당 된 역할이 응용 프로그램 라이선스에 맞게 조정 되어야 합니다. 응용 프로그램에서 소유한 잘못 된 라이선스는 사용자의 프로 비전/업데이트 중에 오류가 발생할 수 있습니다.

### <a name="terms"></a>용어

이 문서에서는 다음과 같은 용어를 사용 합니다.

* CRUD 작업-사용자 계정에 수행 된 작업: 만들기, 읽기, 업데이트, 삭제

* SSO (Single sign-on)-사용자가 한 번 로그인 하 여 모든 SSO 사용 응용 프로그램에 액세스할 수 있는 기능입니다. 사용자 프로 비전 컨텍스트에서 SSO는 자동 사용자 프로 비전을 사용 하는 모든 시스템에 액세스할 수 있는 단일 계정이 있는 사용자의 결과입니다.

* 원본 시스템-Azure AD가 프로 비전 하는 사용자의 리포지토리입니다. Azure AD는 미리 통합 된 대부분의 프로 비전 커넥터에 대 한 원본 시스템입니다. 그러나 SAP, Workday, AWS 등의 클라우드 응용 프로그램에 대 한 몇 가지 예외가 있습니다. 예를 들어 [Workday에서 AD로의 사용자 프로 비전](../saas-apps/workday-inbound-tutorial.md)을 참조 하세요.

* 대상 시스템-Azure AD가 프로 비전 하는 사용자의 리포지토리입니다. 대상 시스템은 일반적으로 ServiceNow, Zscaler 및 여유 시간과 같은 SaaS 응용 프로그램입니다. 대상 시스템은 AD와 같은 온-프레미스 시스템 일 수도 있습니다.

* [SCIM (도메인 간 Id 관리)에 대 한 시스템](https://aka.ms/scimoverview) -사용자 프로 비전을 자동화 하는 데 사용할 수 있는 개방형 표준입니다. SCIM은 Microsoft와 같은 id 공급자와 사용자 id 정보를 요구 하는 Salesforce 또는 기타 SaaS 앱과 같은 서비스 공급자 간에 사용자 id 데이터를 전달 합니다.

### <a name="training-resources"></a>교육 리소스

| 리소스| 링크 및 설명 |
| - | - |
| 주문형 웹 세미나| [Azure AD를 사용 하 여 엔터프라이즈 응용 프로그램 관리](https://info.microsoft.com/CO-AZUREPLAT-WBNR-FY18-03Mar-06-ManageYourEnterpriseApplicationsOption1-MCW0004438_02OnDemandRegistration-ForminBody.html)<br>Azure AD를 사용 하 여 엔터프라이즈 SaaS 응용 프로그램에 대 한 SSO를 구현 하 고 액세스를 제어 하는 최선의 방법을 알아보세요. |
| 동영상| [활성 Azure 디렉터리의 사용자 프로비저닝 이란?](https://youtu.be/_ZjARPpI6NI) <br> [활성 Azure 디렉터리에 사용자 프로 비전을 배포 하는 방법](https://youtu.be/pKzyts6kfrw) <br> [Salesforce를 Azure AD와 통합: 사용자 프로 비전을 자동화 하는 방법](https://azure.microsoft.com/resources/videos/integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning/) |
| 온라인 과정| SkillUp Online:  [Id 관리](https://skillup.online/courses/course-v1:Microsoft+AZ-100.5+2018_T3/about) <br> Azure AD를 많은 SaaS 응용 프로그램과 통합 하 고 이러한 응용 프로그램에 대 한 사용자 액세스를 보호 하는 방법을 알아봅니다. |
| 책| [웹 응용 프로그램에 대 한 Azure Active Directory 최신 인증 (개발자 참조) 첫 번째 버전](https://www.amazon.com/Authentication-Directory-Applications-Developer-Reference/dp/0735696942/ref=sr_1_fkmr0_1?keywords=Azure+multifactor+authentication&qid=1550168894&s=gateway&sr=8-1-fkmr0)입니다.  <br> 이러한 새 환경에 대 한 Active Directory 인증 솔루션을 구축 하기 위한 신뢰할 수 있는 심층 소개 가이드입니다. |
| 자습서| [SaaS 앱을 AZURE AD와 통합 하는 방법에 대 한 자습서 목록을](../saas-apps/tutorial-list.md)참조 하세요. |
| FAQ| 자동화 된 사용자 프로 비전에 대 한 질문과 [대답](../app-provisioning/user-provisioning.md) |

### <a name="solution-architectures"></a>솔루션 아키텍처

Azure AD 프로 비전 서비스는 각 응용 프로그램 공급 업체에서 제공 하는 사용자 관리 API 끝점에 연결 하 여 SaaS 앱 및 기타 시스템에 사용자를 프로 비전 합니다. 이러한 사용자 관리 API 엔드포인트를 사용하면 Azure AD에서 프로그래밍 방식으로 사용자를 만들고, 업데이트하고, 제거할 수 있습니다.

#### <a name="automatic-user-provisioning-for-hybrid-enterprises"></a>하이브리드 기업을 위한 자동 사용자 프로 비전

이 예에서는 사용자 및 또는 그룹이 온-프레미스 디렉터리에 연결 된 HR 데이터베이스에 생성 됩니다. Azure AD 프로 비전 서비스는 대상 SaaS 응용 프로그램에 대 한 자동 사용자 프로 비전을 관리 합니다.

 ![사용자 프로 비전](./media/plan-auto-user-provisioning/hybridprovisioning.png)

**워크플로 설명:**

1. 사용자/그룹은 SAP와 같은 온-프레미스 HR 응용 프로그램/시스템에서 만들어집니다. 

1. **Azure AD Connect 에이전트** 는 로컬 Ad에서 Azure ad에 대 한 id (사용자 및 그룹)의 예약 된 동기화를 실행 합니다.

1. **AZURE AD 프로 비전 서비스** 는 원본 시스템 및 대상 시스템에 대 한 [초기 순환을](../app-provisioning/user-provisioning.md) 시작 합니다. 

1. **AZURE AD 프로 비전 서비스** 는 초기 주기 이후에 변경 된 모든 사용자 및 그룹에 대 한 원본 시스템을 쿼리하고 [증분 주기에](../app-provisioning/user-provisioning.md)변경 내용을 푸시합니다.

#### <a name="automatic-user-provisioning-for-cloud-only-enterprises"></a>클라우드 전용 엔터프라이즈에 대 한 자동 사용자 프로 비전

이 예제에서는 Azure AD에서 사용자 만들기를 수행 하 고 Azure AD 프로 비전 서비스는 대상 (SaaS) 응용 프로그램에 대 한 자동 사용자 프로 비전을 관리 합니다.

![그림 2](./media/plan-auto-user-provisioning/cloudprovisioning.png)

**워크플로 설명:**

1. 사용자/그룹은 Azure AD에서 생성 됩니다.

1. **AZURE AD 프로 비전 서비스** 는 원본 시스템 및 대상 시스템에 대 한 [초기 순환을](../app-provisioning/user-provisioning.md) 시작 합니다. 

1. **AZURE AD 프로 비전 서비스** 는 초기 주기 이후에 업데이트 된 모든 사용자 및 그룹에 대 한 원본 시스템을 쿼리하고 [증분 주기](../app-provisioning/user-provisioning.md)를 수행 합니다.

#### <a name="automatic-user-provisioning-for-cloud-hr-applications"></a>클라우드 HR 응용 프로그램에 대 한 자동 사용자 프로 비전 

이 예제에서는 사용자 및 또는 그룹이 Workday 및 SuccessFactors와 같은 클라우드 HR 응용 프로그램에 생성 됩니다. Azure AD 프로 비전 서비스 및 Azure AD Connect 프로 비전 에이전트는 클라우드 HR 앱 테 넌 트에서 사용자 데이터를 AD로 프로 비전 합니다. AD에서 계정이 업데이트 된 후에는 Azure AD Connect를 통해 Azure AD와 동기화 되며, 메일 주소 및 사용자 이름 특성을 클라우드 HR 앱 테 넌 트에 다시 쓸 수 있습니다.

![그림 2](./media/plan-auto-user-provisioning/workdayprovisioning.png)

1.  **Hr 팀** 은 cloud hr 앱 테 넌 트에서 트랜잭션을 수행 합니다.
2.  **AZURE ad 프로 비전 서비스** 는 클라우드 HR 앱 테 넌 트에서 예약 된 주기를 실행 하 고 AD와 동기화 하기 위해 처리 해야 하는 변경 내용을 식별 합니다.
3.  **AZURE ad 프로 비전 서비스** 는 ad 계정 만들기/업데이트/사용/사용 안 함 작업을 포함 하는 요청 페이로드를 사용 하 여 Azure AD Connect 프로 비전 에이전트를 호출 합니다.
4.  **Azure AD Connect 프로 비전 에이전트** 는 서비스 계정을 사용 하 여 AD 계정 데이터를 관리 합니다.
5.  **Azure AD Connect** 는 델타 동기화를 실행 하 여 AD에서 업데이트를 가져옵니다.
6.  **Ad** 업데이트는 Azure ad와 동기화 됩니다. 
7.  **AZURE ad 프로 비전 서비스** 는 azure ad에서 클라우드 HR 앱 테 넌 트로 메일 특성 및 사용자 이름을 쓰기 저장 합니다.

## <a name="plan-the-deployment-project"></a>배포 프로젝트 계획

사용자 환경에 사용자 프로 비전을 배포 하기 위한 전략을 결정 하려면 조직의 요구 사항을 고려해 야 합니다.

### <a name="engage-the-right-stakeholders"></a>올바른 관련자 참여

기술 프로젝트에 오류가 발생 하는 경우 일반적으로 영향, 결과 및 책임에 대 한 예상치가 일치 하지 않기 때문입니다. 이러한 문제를 방지 하려면 관련자와 해당 프로젝트 입력 및 accountabilities를 문서화 하 여 [적절 한 관련자](https://aka.ms/deploymentplans) 와 프로젝트의 관련자 역할을 잘 이해 하 고 있어야 합니다.

### <a name="plan-communications"></a>통신 계획

통신은 새 서비스의 성공에 대단히 중요합니다. 사용자의 경험이 어떻게 변화하고 언제 변할 것인지, 문제가 발생할 경우 지원을 받는 방법에 대해 사용자와 사전에 소통합니다.

### <a name="plan-a-pilot"></a>파일럿 계획

프로덕션의 모든 사용자로 크기를 조정 하기 전에 사용자 수가 작은 테스트 환경에서 자동 사용자 프로 비전의 초기 구성을 수행 하는 것이 좋습니다. 파일럿 실행에 대 한 [모범 사례](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) 를 참조 하세요.

#### <a name="best-practices-for-a-pilot"></a>파일럿에 대 한 모범 사례  

파일럿을 통해 모든 사용자에 게 기능을 배포 하기 전에 작은 그룹으로 테스트할 수 있습니다. 테스트의 일부로 조직 내의 각 사용 사례가 철저 하 게 테스트 되었는지 확인 합니다.

첫 번째 wave에서 테스트 하 고 피드백을 제공할 수 있는 IT, 유용성 및 기타 적절 한 사용자를 대상으로 합니다. 이 피드백을 사용 하 여 사용자에 게 보내는 통신 및 지침을 추가로 개발 하 고 지원 담당자에 게 표시 되는 문제 유형에 대 한 통찰력을 얻을 수 있습니다.

대상 그룹의 범위를 늘려 더 큰 사용자 그룹에 대 한 롤아웃을 확장 합니다. [동적 그룹 멤버 자격](../users-groups-roles/groups-dynamic-membership.md)을 통해 또는 대상 그룹에 사용자를 수동으로 추가 하 여이 작업을 수행할 수 있습니다.

## <a name="plan-application-connections-and-administration"></a>응용 프로그램 연결 및 관리 계획

Azure AD 포털을 사용 하 여 프로 비전을 지 원하는 모든 응용 프로그램을 보고 관리 합니다. [포털에서 응용 프로그램 찾기](../app-provisioning/configure-automatic-user-provisioning-portal.md)를 참조 하세요.

### <a name="determine-the-type-of-connector-to-use"></a>사용할 커넥터 유형 결정

자동 프로비전을 사용하도록 설정하고 구성하는 데 필요한 실제 단계는 애플리케이션에 따라 달라집니다. 자동으로 프로 비전 하려는 응용 프로그램이 [AZURE AD SaaS 앱 갤러리](../saas-apps/tutorial-list.md)에 나열 되는 경우 [앱 별 통합 자습서](../saas-apps/tutorial-list.md) 를 선택 하 여 사전 통합 된 사용자 프로 비전 커넥터를 구성 해야 합니다.

그렇지 않은 경우 다음 단계를 수행 합니다.

1. 사전 통합 된 사용자 프로 비전 커넥터에 대 한 [요청을 만듭니다](../azuread-dev/howto-app-gallery-listing.md) . Microsoft 팀은 SCIM을 지 원하는 경우 사용자와 응용 프로그램 개발자와 협력 하 여 응용 프로그램을 플랫폼에 등록 합니다.

1. 앱에 대 한 [Byoa SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md) 일반 사용자 프로 비전 지원을 사용 합니다. 이는 사전 통합 된 프로 비전 커넥터 없이 사용자를 앱에 프로 비전 하기 위해 Azure AD에 대 한 요구 사항입니다.

1. 응용 프로그램이 BYOA scim 커넥터를 사용할 수 있는 경우 [byoa scim 통합 자습서](../app-provisioning/use-scim-to-provision-users-and-groups.md) 를 참조 하 여 응용 프로그램에 대 한 BYOA scim 커넥터를 구성 합니다.

자세한 내용은 [AZURE AD 자동 사용자 프로 비전에 사용할 수 있는 응용 프로그램 및 시스템](../app-provisioning/user-provisioning.md) 을 참조 하세요.

### <a name="collect-information-to-authorize-application-access"></a>응용 프로그램 액세스 권한을 부여 하는 정보 수집

자동 사용자 프로 비전을 설정 하는 작업은 응용 프로그램 별로입니다. 각 응용 프로그램에 대해 대상 시스템의 사용자 관리 끝점에 연결 하려면 [관리자 자격 증명](../app-provisioning/configure-automatic-user-provisioning-portal.md) 을 제공 해야 합니다.

아래 이미지는 필수 관리자 자격 증명의 한 가지 버전을 보여 줍니다.

![사용자 계정 프로비저닝 설정을 관리하기 위한 프로비저닝 화면](./media/plan-auto-user-provisioning/userprovisioning-admincredentials.png)

일부 응용 프로그램에는 관리자 사용자 이름 및 암호가 필요 하지만 다른 응용 프로그램에는 전달자 토큰이 필요할 수 있습니다.

## <a name="plan-user-and-group-provisioning"></a>사용자 및 그룹 프로 비전 계획

엔터프라이즈 앱에 대 한 사용자 프로 비전을 사용 하도록 설정 하면 [Azure Portal](https://portal.azure.com/) 특성 매핑을 통해 해당 특성 값을 제어 합니다.

### <a name="determine-operations-for-each-saas-app"></a>각 SaaS 앱에 대 한 작업 확인

각 응용 프로그램에는 Azure AD의 특성에 매핑되어야 하는 고유한 사용자 또는 그룹 특성이 있을 수 있습니다. 응용 프로그램에는 CRUD 작업의 하위 집합만 사용할 수 있습니다.

각 응용 프로그램에 대해 다음 정보를 문서화 합니다.

* 대상 시스템에 대 한 사용자 및 그룹 개체에서 수행할 CRUD 프로 비전 작업입니다. 예를 들어 각 SaaS 앱 비즈니스 소유자는 가능한 모든 작업을 원하지 않을 수 있습니다.

* 원본 시스템에서 사용할 수 있는 특성

* 대상 시스템에서 사용할 수 있는 특성

* 시스템 간 특성 매핑

### <a name="choose-which-users-and-groups-to-provision"></a>프로 비전 할 사용자 및 그룹 선택

자동 사용자 프로 비전을 구현 하기 전에 응용 프로그램에 프로 비전 할 사용자 및 그룹을 결정 해야 합니다.

* [범위 지정 필터](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) 를 사용 하 여 응용 프로그램에 프로 비전 되는 사용자를 결정 하는 특성 기반 규칙을 정의 합니다.

* 그런 다음 추가 필터링을 위해 필요에 따라 [사용자 및 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md) 을 사용 합니다.

### <a name="define-user-and-group-attribute-mapping"></a>사용자 및 그룹 특성 매핑 정의

자동 사용자 프로 비전을 구현 하려면 응용 프로그램에 필요한 사용자 및 그룹 특성을 정의 해야 합니다. Azure AD 사용자 개체와 각 SaaS 응용 프로그램의 사용자 개체 사이에는 미리 구성 된 특성 및 [특성 매핑](../app-provisioning/configure-automatic-user-provisioning-portal.md) 집합이 있습니다. 모든 SaaS 앱에서 그룹 특성을 사용 하도록 설정 하는 것은 아닙니다.

Azure AD는 특성 간 매핑을 직접 지원 하거나 상수 값을 제공 하거나 [특성 매핑에 대 한 식을 작성](../app-provisioning/functions-for-customizing-application-data.md)하 여를 지원 합니다. 이러한 유연성을 통해 대상 시스템의 특성에서 채워지는 항목을 세밀 하 게 제어할 수 있습니다. [MICROSOFT GRAPH API](../app-provisioning/export-import-provisioning-configuration.md) 및 Graph 탐색기를 사용 하 여 사용자 프로 비전 특성 매핑 및 스키마를 JSON 파일로 내보내고 Azure AD로 다시 가져올 수 있습니다.

자세한 내용은 [Azure Active Directory에서 SaaS 응용 프로그램에 대 한 사용자 프로 비전 특성 매핑 사용자 지정](../app-provisioning/customize-application-attributes.md)을 참조 하세요.

### <a name="special-considerations-for-user-provisioning"></a>사용자 프로 비전에 대 한 특별 고려 사항

다음 사항을 고려 하 여 배포 후 문제를 줄이십시오.

* 원본 및 대상 응용 프로그램 간에 사용자/그룹 개체를 매핑하는 데 사용 되는 특성이 복원 가능한 지 확인 합니다. 특성이 변경 되 면 (예: 사용자가 회사의 다른 부분으로 이동) 사용자/그룹이 잘못 프로 비전 되는 것은 아닙니다.

* 응용 프로그램에는 사용자 프로비저닝이 제대로 작동 하기 위해 충족 해야 하는 특정 제한 및/또는 요구 사항이 있을 수 있습니다. 예를 들어, 여유 시간은 특정 특성에 대 한 값을 자릅니다. 각 응용 프로그램에 특정 한 [자동 사용자 프로 비전 자습서](../saas-apps/tutorial-list.md) 를 참조 하세요.

* 원본 시스템과 대상 시스템 간의 스키마 일관성을 확인 합니다. 일반적인 문제에는 UPN 또는 메일이 일치 하지 않는 등의 특성이 포함 됩니다. 예를 들어 Azure AD의 UPN은 *john_smith@contoso.com* 앱에서 및로 설정 되며,이는 *jsmith@contoso.com* 입니다. 자세한 내용은 [사용자 및 그룹 스키마 참조](../app-provisioning/use-scim-to-provision-users-and-groups.md)를 참조 하세요.

## <a name="plan-testing-and-security"></a>테스트 및 보안 계획

배포의 각 단계에서 결과가 예상 대로 테스트 되었으며 프로 비전 주기를 감사 하는지 확인 합니다.

### <a name="plan-testing"></a>테스트 계획

응용 프로그램에 대 한 자동 사용자 프로 비전을 구성한 후에는 테스트 사례를 실행 하 여이 솔루션이 조직의 요구 사항을 충족 하는지 확인 합니다.

| 시나리오| 예상 결과 |
| - | - |
| 사용자가 대상 시스템에 할당 된 그룹에 추가 됨 | 사용자 개체가 대상 시스템에 프로 비전 됩니다. <br>사용자가 대상 시스템에 로그인 하 여 원하는 작업을 수행할 수 있습니다. |
| 대상 시스템에 할당 된 그룹에서 사용자가 제거 되었습니다. | 사용자 개체가 대상 시스템에 프로 비전 해제.<br>사용자가 대상 시스템에 로그인 할 수 없습니다. |
| 사용자 정보는 어떤 방법으로도 Azure AD에서 업데이트 됩니다. | 업데이트 된 사용자 특성은 증분 주기 후 대상 시스템에 반영 됩니다. |
| 사용자가 범위를 벗어났습니다. | 사용자 개체가 사용 하지 않도록 설정 되거나 삭제 되었습니다. <br>참고:이 동작은 [Workday 프로 비전](skip-out-of-scope-deletions.md)에 대해 재정의 됩니다. |

### <a name="plan-security"></a>보안 계획

일반적으로 보안 검토는 배포의 일부로 필요 합니다. 보안 검토가 필요한 경우 identity as a service에 대 한 개요를 제공 하는 여러 Azure AD [백서](https://www.microsoft.com/download/details.aspx?id=36391) 를 참조 하세요.

### <a name="plan-rollback"></a>롤백 계획

자동 사용자 프로 비전 구현이 프로덕션 환경에서 원하는 대로 작동 하지 않을 경우 다음 롤백 단계를 통해 이전에 알려진 양호한 상태로 되돌리는 데 도움이 될 수 있습니다.

1. [프로 비전 요약 보고서](../app-provisioning/check-status-user-account-provisioning.md) 및 [프로 비전 로그](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview) 를 검토 하 여 영향을 받는 사용자 및/또는 그룹에 대해 잘못 된 작업이 발생 했는지 확인 합니다.

1. 프로 비전 감사 로그를 사용 하 여 영향을 받는 사용자 및/또는 그룹의 마지막으로 알려진 양호한 상태를 확인 합니다. 원본 시스템 (Azure AD 또는 AD)도 검토 합니다.

1. 응용 프로그램 소유자에 게 문의 하 여 마지막으로 알려진 양호한 상태 값을 사용 하 여 응용 프로그램에서 직접 영향을 받는 사용자 및/또는 그룹을 업데이트 합니다.

## <a name="deploy-automatic-user-provisioning-service"></a>자동 사용자 프로 비전 서비스 배포

솔루션 요구 사항에 부합 하는 단계를 선택 합니다.

### <a name="prepare-for-the-initial-cycle"></a>초기 주기 준비

Azure AD 프로 비전 서비스가 처음으로 실행 될 때 원본 시스템 및 대상 시스템에 대 한 초기 주기는 각 대상 시스템에 대 한 모든 사용자 개체의 스냅숏을 만듭니다.

응용 프로그램에 대해 자동 프로 비전을 사용 하도록 설정 하는 경우 초기 주기는 20 분에서 몇 시간까지 걸릴 수 있습니다. 기간은 Azure AD 디렉터리의 크기와 프로 비전 범위에 있는 사용자 수에 따라 달라 집니다.

프로 비전 서비스는 초기 주기 후에 두 시스템의 상태를 저장 하 여 후속 증분 주기의 성능을 향상 시킵니다.

### <a name="configure-automatic-user-provisioning"></a>자동 사용자 프로비저닝 구성

이를 지 원하는 응용 프로그램에 대 한 자동 사용자 계정 프로 비전 및 프로 비전 해제를 관리 하려면 [Azure Portal](https://portal.azure.com/) 을 사용 합니다. [응용 프로그램에 대 한 자동 프로 비전 설정 어떻게 할까요?](../app-provisioning/user-provisioning.md) 의 단계를 따르세요.

[Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)를 사용하여 Azure AD 사용자 프로비저닝 서비스를 구성 및 관리할 수도 있습니다.

## <a name="manage-automatic-user-provisioning"></a>자동 사용자 프로 비전 관리

이제을 (를) 배포 했으므로 솔루션을 관리 해야 합니다.

### <a name="monitor-user-provisioning-operation-health"></a>사용자 프로비저닝 작업 상태 모니터링

성공적인 [초기 주기](../app-provisioning/user-provisioning.md)후에 Azure AD 프로 비전 서비스는 다음 이벤트 중 하나가 발생할 때까지 각 응용 프로그램과 관련 된 간격으로 증분 업데이트를 무기한 실행 합니다.

* 서비스를 수동으로 중지 하 고 [Azure Portal](https://portal.azure.com/)를 사용 하거나 적절 한 [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) 명령을 사용 하 여 새로운 초기 순환이 트리거됩니다.

* 새 초기 주기는 특성 매핑 또는 범위 지정 필터의 변경에 의해 트리거됩니다.

* 프로 비전 프로세스는 높은 오류 속도로 인해 격리 되 고 자동으로 사용 하지 않도록 설정 될 때 4 주 넘게 격리 상태로 유지 됩니다.

이러한 이벤트 및 프로 비전 서비스에서 수행 하는 다른 모든 작업을 검토 하려면 Azure AD [프로 비전 로그](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)를 참조 하세요.

프로 비전 주기에서 프로 비전 작업의 진행 상태를 확인 하 고 모니터링 하는 시간을 이해 하려면 [사용자 프로 비전 상태를 확인](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)하면 됩니다.

### <a name="gain-insights-from-reports"></a>보고서에서 통찰력 얻기

Azure AD는 감사 로그 및 보고서를 통해 조직의 사용자 프로 비전 사용 및 운영 상태에 대 한 [추가 정보](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) 를 제공할 수 있습니다.

관리자는 프로 비전 요약 보고서를 확인 하 여 프로 비전 작업의 작동 상태를 모니터링 해야 합니다. 프로 비전 서비스에서 수행 하는 모든 작업은 Azure AD 감사 로그에 기록 됩니다. [자습서: 자동 사용자 계정 프로 비전에 대 한 보고를](../app-provisioning/check-status-user-account-provisioning.md)참조 하세요.

조직의 요구 사항을 충족 하는 흐름에서 이러한 보고서를 소유 하 고 사용 하는 것이 좋습니다. Azure AD는 30 일 동안 대부분의 감사 데이터를 유지 합니다.

### <a name="troubleshoot"></a>문제 해결

프로 비전 중에 발생할 수 있는 문제를 해결 하려면 다음 링크를 참조 하세요.

* [Azure AD 갤러리 애플리케이션에 대해 사용자 프로비전 구성 문제](../app-provisioning/application-provisioning-config-problem.md)

* [응용 프로그램에 프로 비전 하기 위해 온-프레미스 Active Directory에서 Azure AD로 특성 동기화](../app-provisioning/user-provisioning-sync-attributes-for-mapping.md)

* [사용자가 Azure Active Directory 갤러리 애플리케이션에 프로비전을 구성하는 동안 관리자 자격 증명을 저장하는 문제](../app-provisioning/application-provisioning-config-problem-storage-limit.md)

* [사용자가 Azure AD 갤러리 애플리케이션에 프로비전되지 않음](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

* [Azure AD 갤러리 애플리케이션에 잘못된 사용자 집합이 프로비전됨](../app-provisioning/application-provisioning-config-problem-wrong-users-provisioned.md)

### <a name="helpful-documentation"></a>유용한 설명서

* [특성 매핑에 대한 식 작성](../app-provisioning/functions-for-customizing-application-data.md)

* [Azure AD 동기화 API 개요](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)

* [범위를 벗어나는 사용자 계정 삭제 건너뛰기](skip-out-of-scope-deletions.md)

* [Azure AD Connect 프로 비전 에이전트: 버전 릴리스 기록](provisioning-agent-release-version-history.md)

#### <a name="resources"></a>리소스

* [제품 사용자 의견 제공](https://feedback.azure.com/forums/169401-azure-active-directory)

* [Azure AD의 새로운 기능을 최신 상태로 유지](https://azure.microsoft.com/updates/?product=active-directory)

* [스택 오버플로 Azure AD 포럼](https://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="next-steps"></a>다음 단계
* [자동 사용자 프로 비전 구성](../app-provisioning/configure-automatic-user-provisioning-portal.md)

* [Microsoft Graph API를 사용 하 여 프로 비전 구성 내보내기 또는 가져오기](../app-provisioning/export-import-provisioning-configuration.md)

* [Azure Active directory의 특성 매핑에 대 한 식 작성](../app-provisioning/functions-for-customizing-application-data.md)