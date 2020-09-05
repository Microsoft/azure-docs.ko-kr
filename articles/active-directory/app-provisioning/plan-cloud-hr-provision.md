---
title: 사용자 프로 비전을 Azure Active Directory 클라우드 HR 응용 프로그램 계획
description: 이 문서에서는 Azure Active Directory와 함께 Workday 및 SuccessFactors와 같은 클라우드 HR 시스템을 통합 하는 배포 프로세스를 설명 합니다. Azure AD를 클라우드 HR 시스템과 통합 하면 완전 한 id 수명 주기 관리 시스템이 생성 됩니다.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: kenwith
ms.reviewer: arvindha, celested
ms.openlocfilehash: b96b679e967fd898f072b4b1ae195e3dd1061c04
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88235698"
---
# <a name="plan-cloud-hr-application-to-azure-active-directory-user-provisioning"></a>사용자 프로 비전을 Azure Active Directory 클라우드 HR 응용 프로그램 계획

지금까지 IT 직원은 직원을 만들고 업데이트 하 고 삭제 하는 수동 방법에 의존 했습니다. 이러한 메서드는 CSV 파일 업로드 또는 사용자 지정 스크립트를 사용 하 여 직원 데이터를 동기화 하는 등의 방법을 사용 했습니다. 이러한 프로 비전 프로세스는 오류가 발생 하기 쉬우며 안전 하지 않으며 관리가 어렵습니다.

직원, 공급 업체 또는 불확정 작업자의 id 수명 주기를 관리 하기 위해 [AZURE AD (Azure Active Directory) 사용자 프로 비전 서비스](../app-provisioning/user-provisioning.md) 는 클라우드 기반 HR (인적 자원) 응용 프로그램과의 통합을 제공 합니다. 응용 프로그램의 예로는 Workday 또는 SuccessFactors이 있습니다.

Azure AD는이 통합을 사용 하 여 다음과 같은 클라우드 HR 응용 프로그램 (앱) 워크플로를 사용 하도록 설정 합니다.

- **Active Directory에 사용자 프로 비전:** 클라우드 HR 앱에서 선택한 사용자 집합을 하나 이상의 Active Directory 도메인에 프로 비전 합니다.
- **클라우드 전용 사용자를 AZURE AD에 프로 비전:** Active Directory 사용 되지 않는 시나리오에서는 클라우드 HR 앱에서 Azure AD로 직접 사용자를 프로 비전 합니다.
- **클라우드 HR 앱에 다시 씁니다.** Azure AD의 이메일 주소 및 사용자 이름 특성을 클라우드 HR 앱에 다시 씁니다.

> [!NOTE]
> 이 배포 계획에서는 Azure AD 사용자 프로 비전을 사용 하 여 클라우드 HR 앱 워크플로를 배포 하는 방법을 보여 줍니다. SaaS (software as a service) 앱에 자동 사용자 프로 비전을 배포 하는 방법에 대 한 자세한 내용은 [자동 사용자 프로 비전 배포 계획](https://aka.ms/deploymentplans/provisioning)을 참조 하세요.

## <a name="enabled-hr-scenarios"></a>HR 시나리오 사용

Azure AD 사용자 프로 비전 서비스를 사용 하면 다음과 같은 HR 기반 id 수명 주기 관리 시나리오를 자동화할 수 있습니다.

- **새 직원 채용:** 클라우드 HR 앱에 새 직원을 추가 하면 사용자 계정이 자동으로 Active Directory 및 Azure AD에서 클라우드 HR 앱에 전자 메일 주소 및 사용자 이름 특성을 기록 하는 옵션을 사용 하 여 생성 됩니다.
- **직원 특성 및 프로필 업데이트:** 클라우드 HR 앱에서 이름, 제목, 관리자 등의 직원 레코드가 업데이트 되는 경우 해당 사용자 계정은 Active Directory 및 Azure AD에서 자동으로 업데이트 됩니다.
- **직원 종료:** 클라우드 HR 앱에서 직원이 종료 되 면 해당 사용자 계정은 Active Directory 및 Azure AD에서 자동으로 사용 하지 않도록 설정 됩니다.
- **직원 재 고용:** 클라우드 HR 앱에서 직원이 다시 고용 될 때 이전 계정은 자동으로 다시 활성화 되거나 Active Directory 및 Azure AD에 다시 프로 비전 될 수 있습니다.

## <a name="who-is-this-integration-best-suited-for"></a>이 통합의 가장 적합 한 사용자는 누구 인가요?

Azure AD 사용자 프로 비전과 클라우드 HR 앱 통합은 다음을 수행 하는 조직에 가장 적합 합니다.

- 클라우드 HR 사용자 프로 비전을 위한 미리 빌드된 클라우드 기반 솔루션이 필요 합니다.
- 클라우드 HR 앱에서 Active Directory 또는 Azure AD로 직접 사용자를 프로 비전 해야 합니다.
- 클라우드 HR 앱에서 얻은 데이터를 사용 하 여 사용자를 프로 비전 해야 합니다.
- 클라우드 HR 앱에서 검색 된 변경 정보만 기반으로 하나 이상의 Active Directory 포리스트, 도메인 및 Ou로 사용자를 동기화 할 수 있어야 합니다.
- 전자 메일에 Office 365를 사용 합니다.

## <a name="learn"></a>배우기

사용자 프로 비전은 지속적인 id 거 버 넌 스를 위한 토대를 만듭니다. 이를 통해 신뢰할 수 있는 id 데이터를 사용 하는 비즈니스 프로세스의 품질을 향상 시킬 수 있습니다.

### <a name="terms"></a>용어

이 문서에서는 다음과 같은 용어를 사용 합니다.

- **원본 시스템**: Azure AD가 프로 비전 하는 사용자의 리포지토리입니다. 예를 들어 Workday 또는 SuccessFactors와 같은 클라우드 HR 앱이 있습니다.
- **대상 시스템**: Azure AD가 프로 비전 하는 사용자의 리포지토리입니다. 예를 들면 Active Directory, Azure AD, Office 365 또는 기타 SaaS 앱이 있습니다.
- **Joiners-Leavers 프로세스**: 클라우드 HR 앱을 레코드 시스템으로 사용 하 여 새 고용, 전송 및 종료에 사용 되는 용어입니다. 이 프로세스는 서비스가 대상 시스템에 필요한 특성을 성공적으로 프로 비전 할 때 완료 됩니다.

### <a name="key-benefits"></a>주요 이점

HR 중심 IT 프로 비전의이 기능은 다음과 같은 중요 한 비즈니스 혜택을 제공 합니다.

- **생산성 향상:** 이제 사용자 계정 및 Office 365 라이선스 할당을 자동화 하 고 키 그룹에 대 한 액세스를 제공할 수 있습니다. 할당을 자동화 하면 작업 도구에 즉시 액세스할 수 있으며 생산성이 향상 됩니다.
- **위험 관리:** 클라우드 HR 앱에서 이동 하는 데이터를 사용 하 여 직원 상태 또는 그룹 멤버 자격에 따라 변경 내용을 자동화 하 여 보안을 강화할 수 있습니다. 변경을 자동화 하면 사용자가 조직을 전환 하거나 탈퇴할 때 키 앱에 대 한 사용자 id 및 액세스가 자동으로 업데이트 됩니다.
- **주소 규정 준수 및 거 버 넌 스:** Azure AD는 원본 및 대상 시스템 모두의 앱에서 수행 하는 사용자 프로 비전 요청에 대 한 기본 감사 로그를 지원 합니다. 감사를 통해 단일 화면에서 앱에 대 한 액세스 권한이 있는 사용자를 추적할 수 있습니다.
- **비용 관리:** 자동 프로 비전은 수동 프로 비전과 관련 된 비효율성 및 인간 오류를 방지 하 여 비용을 절감 합니다. 레거시 및 오래 된 플랫폼을 사용 하 여 시간에 따라 작성 된 사용자 지정 개발 된 사용자 프로 비전 솔루션에 대 한 필요성을 줄입니다.

### <a name="licensing"></a>라이선스

클라우드 HR 앱을 Azure AD 사용자 프로 비전 통합에 구성 하려면 Workday 또는 SuccessFactors와 같은 클라우드 HR 앱에 대 한 유효한 [Azure AD Premium 라이선스](https://azure.microsoft.com/pricing/details/active-directory/) 및 라이선스가 필요 합니다.

또한 클라우드 HR 앱에서 소스인 Active Directory 또는 Azure AD로 프로 비전 되는 모든 사용자에 대해 유효한 Azure AD Premium P1 이상의 구독 라이선스가 필요 합니다. 클라우드 HR 앱에서 소유 하 고 있는 라이선스 수가 잘못 된 경우 사용자 프로 비전 중에 오류가 발생할 수 있습니다.

### <a name="prerequisites"></a>필수 구성 요소

- Azure AD Connect 프로 비전 에이전트를 구성 하는 Azure AD [하이브리드 id 관리자](../users-groups-roles/directory-assign-admin-roles.md#hybrid-identity-administrator)  입니다.
- Azure Portal에서 프로 비전 앱을 구성 하는 Azure AD [응용 프로그램 관리자](../users-groups-roles/directory-assign-admin-roles.md#application-administrator) 역할
- 클라우드 HR 앱의 테스트 및 프로덕션 인스턴스입니다.
- 클라우드 HR 앱에서 시스템 통합 사용자를 만들고 테스트 목적으로 직원 데이터를 변경 하는 관리자 권한
- Active Directory에 대 한 사용자 프로 비전을 위해 Azure AD Connect 프로 비전 에이전트를 호스트 하려면 .NET 4.7.1 + runtime을 사용 하 여 Windows Server 2012 이상을 실행 하는 서버가 필요 합니다.
- Active Directory와 Azure AD 간에 사용자를 동기화 하는 [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) .

### <a name="training-resources"></a>학습 리소스

| **리소스** | **링크 및 설명** |
|:-|:-|
| 동영상 | [활성 Azure 디렉터리의 사용자 프로비저닝 이란?](https://youtu.be/_ZjARPpI6NI) |
| | [활성 Azure 디렉터리에 사용자 프로 비전을 배포 하는 방법](https://youtu.be/pKzyts6kfrw) |
| 자습서 | [Azure AD와 SaaS 앱을 통합 하는 방법에 대 한 자습서 목록](../saas-apps/tutorial-list.md) |
| | [자습서: 자동 사용자 프로비저닝을 위한 Workday 구성](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |
| FAQ | [자동화된 사용자 프로비전](../app-provisioning/user-provisioning.md#what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning) |
| | [Workday에서 Azure AD로 프로 비전](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |

### <a name="solution-architecture"></a>솔루션 아키텍처

다음 예제에서는 일반적인 하이브리드 환경에 대 한 종단 간 사용자 프로비저닝 솔루션 아키텍처를 설명 하 고 다음을 포함 합니다.

- **클라우드 HR 앱에서 Active Directory에 대 한 신뢰할 수 있는 HR 데이터 흐름입니다.** 이 흐름에서 HR 이벤트 (Joiners-Leavers 프로세스)는 클라우드 HR 앱 테 넌 트에서 시작 됩니다. Azure AD 프로 비전 서비스 및 Azure AD Connect 프로 비전 에이전트는 클라우드 HR 앱 테 넌 트의 사용자 데이터를 Active Directory으로 프로 비전 합니다. 이벤트에 따라 Active Directory에서 만들기, 업데이트, 사용 및 사용 안 함 작업을 수행할 수 있습니다.
- **Azure AD와 동기화 하 고 온-프레미스 Active Directory의 전자 메일 및 사용자 이름을 클라우드 HR 앱에 다시 씁니다.** Active Directory에서 계정이 업데이트 된 후에는 Azure AD Connect를 통해 Azure AD와 동기화 됩니다. 이메일 주소 및 사용자 이름 특성은 클라우드 HR 앱 테 넌 트에 다시 쓸 수 있습니다.

![워크플로 다이어그램](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img1.png)

#### <a name="description-of-workflow"></a>워크플로에 대한 설명

다이어그램에는 다음과 같은 주요 단계가 나와 있습니다.  

1. **Hr 팀** 은 cloud hr 앱 테 넌 트에서 트랜잭션을 수행 합니다.
2. **AZURE AD 프로 비전 서비스** 는 클라우드 HR 앱 테 넌 트에서 예약 된 주기를 실행 하 고 Active Directory와 동기화 하기 위해 처리 해야 하는 변경 내용을 식별 합니다.
3. **AZURE AD 프로 비전 서비스** 는 Active Directory 계정 만들기, 업데이트, 사용 및 사용 안 함 작업을 포함 하는 요청 페이로드를 사용 하 여 Azure AD Connect 프로 비전 에이전트를 호출 합니다.
4. **Azure AD Connect 프로 비전 에이전트** 는 서비스 계정을 사용 하 여 Active Directory 계정 데이터를 관리 합니다.
5. **Azure AD Connect** 델타 [동기화](../hybrid/how-to-connect-sync-whatis.md) 를 실행 하 여 Active Directory 업데이트를 가져옵니다.
6. **Active Directory** 업데이트는 Azure AD와 동기화 됩니다.
7. **AZURE ad 프로 비전 서비스** 쓰기는 azure ad에서 클라우드 HR 앱 테 넌 트로 메일 특성 및 사용자 이름을 백업 합니다.

## <a name="plan-the-deployment-project"></a>배포 프로젝트 계획

사용자 환경에서 이 배포에 대한 전략을 결정하는 동안 조직의 요구 사항을 고려합니다.

### <a name="engage-the-right-stakeholders"></a>올바른 관련자 참여

기술 프로젝트에 오류가 발생 하는 경우 일반적으로 영향, 결과 및 책임에 대 한 기대치가 일치 업데이트로 인해 합니다. 이러한 문제를 방지 하려면 [올바른 관련자에 게 관심이 있는지 확인](https://aka.ms/deploymentplans)합니다. 또한 프로젝트의 관련자 역할을 잘 이해 하 고 있어야 합니다. 관련자와 해당 프로젝트 입력 및 accountabilities를 문서화 합니다.

HR 조직의 담당자에 게 기존 HR 비즈니스 프로세스 및 작업자 id와 작업 데이터 처리 요구 사항에 대 한 입력을 제공할 수 있는 담당자를 포함 합니다.

### <a name="plan-communications"></a>통신 계획

통신은 새 서비스의 성공에 대단히 중요합니다. 사용자의 경험이 언제 어떻게 변경 되는 시기 및 방법에 대해 사전에 사용자와 의견을 교환 하세요. 문제가 발생 하는 경우 지원을 얻는 방법을 알려주세요.

### <a name="plan-a-pilot"></a>파일럿 계획

HR 비즈니스 프로세스와 id 워크플로를 클라우드 HR 앱에서 대상 시스템으로 통합 하려면 솔루션을 프로덕션 환경에 배포 하기 전에 상당한 양의 데이터 유효성 검사, 데이터 변환, 데이터 정리 및 종단 간 테스트가 필요 합니다.

프로덕션 환경에서 모든 사용자로 크기를 조정 하기 전에 [파일럿 환경](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) 에서 초기 구성을 실행 합니다.

## <a name="select-cloud-hr-provisioning-connector-apps"></a>클라우드 HR 프로 비전 커넥터 앱을 선택 합니다.

클라우드 HR 앱과 Active Directory 간에 Azure AD 프로 비전 워크플로를 용이 하 게 하려면 Azure AD 앱 갤러리에서 여러 프로 비전 커넥터 앱을 추가할 수 있습니다.

- **사용자 프로 비전 Active Directory 클라우드 hr 앱**:이 프로 비전 커넥터 앱은 클라우드 hr 앱에서 단일 Active Directory 도메인으로 사용자 계정 프로 비전을 용이 하 게 합니다. 도메인이 여러 개 있는 경우 프로비전해야 하는 각 Active Directory 도메인에 대해 Azure AD 앱 갤러리에서 이 앱의 하나의 인스턴스를 추가할 수 있습니다.
- **AZURE ad 사용자 프로 비전에 대 한 클라우드 hr 앱**: Azure AD Connect는 azure ad에 Active Directory 사용자를 동기화 하는 데 사용 되는 도구 이지만,이 프로 비전 커넥터 앱을 사용 하 여 클라우드 HR 앱에서 단일 azure ad 테 넌 트로 클라우드 전용 사용자를 쉽게 프로 비전 할 수 있습니다.
- **클라우드 hr 앱 쓰기-** 이 프로 비전 커넥터 앱은 사용자의 메일 주소를 Azure AD에서 클라우드 HR 앱으로 다시 쓰기를 용이 하 게 합니다.

예를 들어 다음 이미지는 Azure AD 앱 갤러리에서 사용할 수 있는 Workday 커넥터 앱을 나열 합니다.

![Azure Active Directory 포털 앱 갤러리](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img2.png)

### <a name="decision-flow-chart"></a>의사 결정 흐름 차트

다음 의사 결정 흐름 차트를 사용 하 여 시나리오와 관련 된 클라우드 HR 프로 비전 앱을 식별 합니다.

![의사 결정 흐름 차트](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img3.png)

## <a name="design-the-azure-ad-connect-provisioning-agent-deployment-topology"></a>Azure AD Connect 프로 비전 에이전트 배포 토폴로지 디자인

클라우드 HR 앱과 Active Directory 간의 프로 비전 통합에는 네 가지 구성 요소가 필요 합니다.

- 클라우드 HR 앱 테 넌 트
- 프로 비전 커넥터 앱
- 프로 비전 에이전트 Azure AD Connect
- Active Directory 도메인

Azure AD Connect 프로 비전 에이전트 배포 토폴로지는 클라우드 HR 앱 테 넌 트 수 Active Directory 및 통합 하려는 하위 도메인의 수에 따라 달라 집니다. Active Directory 도메인이 여러 개 있는 경우에는 Active Directory 도메인이 연속 되었는지 또는 [비연속](/windows-server/identity/ad-ds/plan/disjoint-namespace)인지에 따라 달라 집니다.

결정에 따라 배포 시나리오 중 하나를 선택 합니다.

- 단일 클라우드 HR 앱 테 넌 트-신뢰할 수 있는 포리스트에 있는 단일 또는 여러 Active Directory 하위 도메인 > 대상
- 단일 클라우드 HR 앱 테 넌 트-연결 되지 않은 Active Directory 포리스트에 여러 자식 도메인 > 대상으로 합니다.

### <a name="single-cloud-hr-app-tenant---target-single-or-multiple-active-directory-child-domains-in-a-trusted-forest"></a>단일 클라우드 HR 앱 테 넌 트-신뢰할 수 있는 포리스트에 있는 단일 또는 여러 Active Directory 하위 도메인 > 대상

다음 프로덕션 구성을 권장 합니다.

|요구 사항|권장|
|:-|:-|
|배포할 Azure AD Connect 프로 비전 에이전트 수|2 (고가용성 및 장애 조치 (failover))
|구성할 프로 비전 커넥터 앱 수|자식 도메인당 하나의 앱|
|Azure AD Connect 프로 비전 에이전트에 대 한 서버 호스트|도메인 컨트롤러 Active Directory 찾을 수 있는 배치할의 Windows 2012 R2 +</br>Azure AD Connect 서비스와 함께 사용할 수 있음|

![온-프레미스 에이전트로 이동](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img4.png)

### <a name="single-cloud-hr-app-tenant---target-multiple-child-domains-in-a-disjoint-active-directory-forest"></a>단일 클라우드 HR 앱 테 넌 트-연결 되지 않은 Active Directory 포리스트에 여러 자식 도메인 > 대상으로 합니다.

이 시나리오에는 클라우드 HR 앱에서 연결 되지 않은 Active Directory 포리스트의 도메인으로 사용자를 프로 비전 하는 작업이 포함 됩니다.

다음 프로덕션 구성을 권장 합니다.

|요구 사항|권장|
|:-|:-|
|온-프레미스로 배포할 Azure AD Connect 프로 비전 에이전트 수|비연속 Active Directory 포리스트 2 개|
|구성할 프로 비전 커넥터 앱 수|자식 도메인당 하나의 앱|
|Azure AD Connect 프로 비전 에이전트에 대 한 서버 호스트|도메인 컨트롤러 Active Directory 찾을 수 있는 배치할의 Windows 2012 R2 +</br>Azure AD Connect 서비스와 함께 사용할 수 있음|

![단일 클라우드 HR 앱 테 넌 트 Active Directory 포리스트 분리](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img5.png)

### <a name="azure-ad-connect-provisioning-agent-requirements"></a>Azure AD Connect 프로 비전 에이전트 요구 사항

사용자 프로 비전 솔루션 Active Directory 클라우드 HR 앱을 사용 하려면 Windows 2012 R2 이상을 실행 하는 서버에서 하나 이상의 Azure AD Connect 프로 비전 에이전트를 배포 해야 합니다. 서버에는 최소 4gb RAM 및 .NET 4.7.1 + runtime이 있어야 합니다. 호스트 서버에 대상 Active Directory 도메인에 대 한 네트워크 액세스 권한이 있는지 확인 합니다.

온-프레미스 환경을 준비 하기 위해 Azure AD Connect 프로 비전 에이전트 구성 마법사는 Azure AD 테 넌 트에 에이전트를 등록 하 고, [포트를 열어](../manage-apps/application-proxy-add-on-premises-application.md#open-ports) [url에](../manage-apps/application-proxy-add-on-premises-application.md#allow-access-to-urls)대 한 액세스를 허용 하 고, [아웃 바운드 HTTPS 프록시 구성을](../saas-apps/workday-inbound-tutorial.md#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)지원 합니다.

프로 비전 에이전트는 서비스 계정을 사용 하 여 Active Directory 도메인과 통신 합니다. 에이전트를 설치 하기 전에 다음 요구 사항을 충족 하는 Active Directory 사용자 및 컴퓨터에 서비스 계정을 만듭니다.

- 만료 되지 않는 암호
- 사용자 계정을 읽고, 만들고, 삭제 하 고, 관리 하기 위한 위임 된 제어 권한

프로 비전 요청을 처리 해야 하는 도메인 컨트롤러를 선택할 수 있습니다. 지리적으로 분산 된 도메인 컨트롤러가 여러 개 있는 경우 기본 설정 도메인 컨트롤러와 동일한 사이트에 프로 비전 에이전트를 설치 합니다. 이러한 위치를 지정 하면 종단 간 솔루션의 안정성과 성능이 향상 됩니다.

고가용성을 위해 둘 이상의 Azure AD Connect 프로 비전 에이전트를 배포할 수 있습니다. 에이전트를 등록 하 여 동일한 온-프레미스 Active Directory 도메인 집합을 처리 합니다.

## <a name="plan-scoping-filters-and-attribute-mapping"></a>범위 지정 필터 및 특성 매핑 계획

클라우드 HR 앱에서 Active Directory 또는 Azure AD로 프로 비전을 사용 하도록 설정 하면 Azure Portal는 특성 매핑을 통해 특성 값을 제어 합니다.

### <a name="define-scoping-filters"></a>범위 지정 필터 정의

[범위 지정 필터](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) 를 사용 하 여 클라우드 HR 앱에서 Active Directory 또는 Azure AD로 프로 비전 할 사용자를 결정 하는 특성 기반 규칙을 정의 합니다.

Joiners 프로세스를 시작 하는 경우 다음 요구 사항을 수집 합니다.

- 클라우드 HR 앱이 직원 및 불확정 작업자를 모두 보드에 가져오는 데 사용 되나요?
- Azure AD 사용자 프로 비전에 클라우드 HR 앱을 사용 하 여 직원과 불확정 작업자를 모두 관리 하려고 하나요?
- 클라우드 hr 앱 사용자의 하위 집합에 대해서만 Azure AD 사용자 프로 비전에 클라우드 HR 앱을 롤아웃할 계획 입니까? 예를 들어 직원 일 수도 있습니다.

요구 사항에 따라 특성 매핑을 구성할 때 **원본 개체 범위** 필드를 설정 하 여 클라우드 HR 앱의 사용자 집합을 Active Directory 프로 비전 범위에 속해야 하는 사용자 집합을 선택할 수 있습니다. 자세한 내용은 일반적으로 사용 되는 범위 지정 필터에 대 한 클라우드 HR 앱 자습서를 참조 하세요.

### <a name="determine-matching-attributes"></a>일치 하는 특성 확인

프로 비전을 사용 하면 원본 시스템과 대상 시스템 간에 기존 계정을 일치 시킬 수 있습니다. 클라우드 HR 앱을 Azure AD 프로 비전 서비스와 통합 하는 경우 클라우드 HR 앱에서 Active Directory 또는 Azure AD로 이동 해야 하는 사용자 데이터를 결정 하는 [특성 매핑을 구성할](../app-provisioning/configure-automatic-user-provisioning-portal.md#mappings) 수 있습니다.

Joiners 프로세스를 시작 하는 경우 다음 요구 사항을 수집 합니다.

- 이 클라우드 HR 앱에서 각 사용자를 식별 하는 데 사용 되는 고유 ID는 무엇 인가요?
- Id 수명 주기 관점에서 rehires을 어떻게 처리 하나요? 이전 직원 Id를 계속 고용 하나요?
- 향후에는 고용를 처리 하 고 사전에 Active Directory 계정을 만들어야 하나요?
- Id 수명 주기 관점에서 볼 때 직원의 불확정 변환을 어떻게 처리 하나요? 아니면 그렇지 않은 경우
- 변환 된 사용자가 이전 Active Directory 계정을 유지 하거나 새 계정을 얻을 수 있나요?

요구 사항에 따라 Azure AD는 상수 값을 제공 하거나 특성 [매핑에 대 한 식을 작성](../app-provisioning/functions-for-customizing-application-data.md)하 여 특성-특성 간 매핑을 직접 지원 합니다. 이러한 유연성을 통해 대상 앱 특성에서 채워지는 사항을 궁극적으로 제어할 수 있습니다. [MICROSOFT GRAPH API](../app-provisioning/export-import-provisioning-configuration.md) 및 Graph 탐색기를 사용 하 여 사용자 프로 비전 특성 매핑 및 스키마를 JSON 파일로 내보내고 Azure AD로 다시 가져올 수 있습니다.

기본적으로 고유한 직원 ID를 나타내는 cloud HR 앱의 특성은 *Active Directory의 고유한 특성에 매핑되* 는 일치 특성으로 사용 됩니다. 예를 들어 Workday 앱 시나리오에서 **workday** **WorkerID** 특성은 Active Directory **employeeID** 특성에 매핑됩니다.

일치 하는 특성을 여러 개 설정 하 고 일치 하는 우선 순위를 할당할 수 있습니다. 일치 하는 우선 순위에 따라 평가 됩니다. 일치 항목이 발견되는 즉시 더 이상 일치 특성을 평가하지 않습니다.

기존 특성 매핑을 변경 하거나 삭제 하는 등 [의 기본 특성 매핑을 사용자 지정할](../app-provisioning/customize-application-attributes.md#understanding-attribute-mapping-types)수도 있습니다. 비즈니스 요구 사항에 따라 새 특성 매핑을 만들 수도 있습니다. 자세한 내용은 매핑할 사용자 지정 특성 목록에 대 한 클라우드 HR 앱 자습서 (예: [Workday](../saas-apps/workday-inbound-tutorial.md#managing-your-configuration))를 참조 하세요.

### <a name="determine-user-account-status"></a>사용자 계정 상태 확인

기본적으로 프로 비전 커넥터 앱은 HR 사용자 프로필 상태를 Active Directory 또는 Azure AD의 사용자 계정 상태에 매핑하여 사용자 계정을 사용 하거나 사용 하지 않도록 설정할지 여부를 결정 합니다.

Joiners-Leavers 프로세스를 시작 하는 경우 다음 요구 사항을 수집 합니다.

| 프로세스 | 요구 사항 |
| - | - |
| **Joiners** | Id 수명 주기 관점에서 rehires을 어떻게 처리 하나요? 이전 직원 Id를 계속 고용 하나요? |
| | 향후에는 고용를 처리 하 고 사전에 Active Directory 계정을 만들어야 하나요? 이러한 계정은 사용 또는 사용 안 함 상태로 생성 되나요? |
| | Id 수명 주기 관점에서 볼 때 직원의 불확정 변환을 어떻게 처리 하나요? 아니면 그렇지 않은 경우 |
| | 변환 된 사용자가 이전 Active Directory 계정을 유지 하거나 새 계정을 받게 하나요? |
| **입사** | Active Directory의 직원과 불확정 작업자에 대해 서로 다르게 처리 되나요? |
| | 사용자 종료를 처리 하는 데 어떤 효과적인 날짜를 고려 하나요? |
| | Employee 및 불확정 작업자 변환은 기존 Active Directory 계정에 어떤 영향을 미칩니까? |
| | Active Directory에서 Rescind 작업을 처리 하려면 어떻게 해야 하나요? Rescind 작업은 작업을 처리 해야 합니다. 이후 날짜는 연결자 프로세스의 일부로 Active Directory에서 생성 됩니다. |

요구 사항에 따라, 데이터 요소 조합에 따라 Active Directory 계정이 활성화 또는 비활성화 되도록 [AZURE AD 식을](../app-provisioning/functions-for-customizing-application-data.md) 사용 하 여 매핑 논리를 사용자 지정할 수 있습니다.

### <a name="map-cloud-hr-app-to-active-directory-user-attributes"></a>클라우드 HR 앱을 Active Directory 사용자 특성에 매핑

각 클라우드 HR 앱은 기본 클라우드 HR 앱과 함께 Active Directory 매핑을 제공 합니다.

Joiners-Leavers 프로세스를 시작 하는 경우 다음 요구 사항을 수집 합니다.

| 프로세스 | 요구 사항 |
| - | - |
| **Joiners** | Active Directory 계정 생성 프로세스 수동, 자동화 또는 부분적으로 자동화 되었습니까? |
| | 클라우드 HR 앱에서 Active Directory로 사용자 지정 특성을 전파할 계획 입니까? |
| **이사** | 클라우드 HR 앱에서 운송업 (작업)이 발생 하는 경우 어떤 특성을 처리 하 시겠습니까? |
| | 사용자 업데이트 시 특정 특성 유효성 검사를 수행 하나요? 그렇다면 세부 정보를 제공 합니다. |
| **입사** | Active Directory의 직원과 불확정 작업자에 대해 서로 다르게 처리 되나요? |
| | 사용자 종료를 처리 하는 데 어떤 효과적인 날짜를 고려 하나요? |
| | Employee 및 불확정 작업자 변환은 기존 Active Directory 계정에 어떤 영향을 미칩니까? |

요구 사항에 따라 통합 목표에 맞게 매핑을 수정할 수 있습니다. 자세한 내용은 매핑할 사용자 지정 특성 목록에 대 한 특정 클라우드 HR 앱 자습서 (예: [Workday](../saas-apps/workday-inbound-tutorial.md#part-4-configure-attribute-mappings))를 참조 하세요.

### <a name="generate-a-unique-attribute-value"></a>고유한 특성 값 생성

Joiners 프로세스를 시작할 때 CN, samAccountName 및 UPN (unique 제약 조건이 있는)과 같은 특성을 설정 하는 경우 고유한 특성 값을 생성 해야 할 수 있습니다.

Azure AD 함수 [Selectuniquevalues](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) 는 각 규칙을 평가한 다음 대상 시스템에서 고유성이 생성 된 값을 확인 합니다. 예제를 보려면 [userPrincipalName (UPN) 특성에 대 한 고유 값 생성](../app-provisioning/functions-for-customizing-application-data.md#generate-unique-value-for-userprincipalname-upn-attribute)을 참조 하세요.

> [!NOTE]
> 이 함수는 현재 Workday에서 사용자 프로 비전을 Active Directory 하는 경우에만 지원 됩니다. 다른 프로 비전 앱과 함께 사용할 수 없습니다.

### <a name="configure-active-directory-ou-container-assignment"></a>Active Directory OU 컨테이너 할당 구성

비즈니스 단위, 위치 및 부서에 따라 컨테이너에 Active Directory 사용자 계정을 저장 하는 것이 일반적입니다. 운송업 프로세스를 시작 하 고 감독 기관과 이러한 조직이 변경 되는 경우 Active Directory에서 한 OU에서 다른 OU로 사용자를 이동 해야 할 수 있습니다.

[스위치 ()](../app-provisioning/functions-for-customizing-application-data.md#switch) 함수를 사용 하 여 OU 할당에 대 한 비즈니스 논리를 구성 하 고 Active Directory 특성 **parentDistinguishedName**에 매핑합니다.

예를 들어 HR 특성 **지방 자치 체**을 기반으로 OU에 사용자를 만들려는 경우 다음 식을 사용할 수 있습니다.

`
Switch([Municipality], "OU=Default,OU=Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")
`

이 식을 사용 하는 경우 지방 자치 체 값이 댈러스, 오스틴, 시애틀 또는 런던 이면 해당 OU에 사용자 계정이 생성 됩니다. 일치 하는 항목이 없으면 기본 OU에 계정이 만들어집니다.

## <a name="plan-for-password-delivery-of-new-user-accounts"></a>새 사용자 계정의 암호 전달 계획

Joiners 프로세스를 시작 하는 경우 새 사용자 계정의 임시 암호를 설정 하 고 제공 해야 합니다. Azure AD 사용자 프로 비전에 클라우드 HR을 사용 하면 하루에 사용자에 대 한 Azure AD SSPR ( [셀프 서비스 암호 재설정](../authentication/tutorial-enable-sspr.md) ) 기능을 롤아웃할 수 있습니다.

SSPR는 IT 관리자가 사용자가 암호를 재설정 하거나 계정의 잠금을 해제할 수 있도록 하는 간단한 방법입니다. 클라우드 HR 앱에서 **모바일 번호** 특성을 프로 비전 하 여 Active Directory 하 고 Azure AD와 동기화 할 수 있습니다. **모바일 번호** 특성이 Azure AD에 있는 후 사용자 계정에 대해 SSPR를 사용 하도록 설정할 수 있습니다. 그런 다음, 새 사용자는 인증을 위해 등록 되 고 확인 된 휴대폰 번호를 사용할 수 있습니다.

## <a name="plan-for-initial-cycle"></a>초기 주기 계획

Azure AD 프로 비전 서비스를 처음 실행 하는 경우 클라우드 HR 앱에 대 한 [초기 주기](../app-provisioning/how-provisioning-works.md#initial-cycle) 를 수행 하 여 cloud hr 앱에 모든 사용자 개체의 스냅숏을 만듭니다. 초기 주기에 소요 되는 시간은 원본 시스템에 있는 사용자 수에 직접적으로 종속 됩니다. 10만 명의 사용자가 있는 일부 클라우드 HR 앱 테 넌 트의 초기 주기는 오랜 시간이 걸릴 수 있습니다.

**대량 클라우드 HR 앱 테 넌 트 (>3만 사용자)의** 경우 점진적 단계에서 초기 주기를 실행 합니다. 다른 사용자 프로 비전 시나리오에 대해 Active Directory에 올바른 특성이 설정 되었는지 확인 한 후에만 증분 업데이트를 시작 합니다. 여기에서 순서를 따르세요.

1. [범위 지정 필터](#plan-scoping-filters-and-attribute-mapping)를 설정 하 여 제한 된 사용자 집합에 대해서만 초기 주기를 실행 합니다.
2. Active Directory 계정 프로 비전 및 첫 번째 실행에 대해 선택한 사용자에 대해 설정 된 특성 값을 확인 합니다. 결과가 예상과 일치 하는 경우 범위 지정 필터를 확장 하 여 더 많은 사용자를 점차적으로 포함 하 고 두 번째 실행에 대 한 결과를 확인 합니다.

테스트 사용자에 대 한 초기 주기의 결과가 만족 스 러 우면 [증분 업데이트](../app-provisioning/how-provisioning-works.md#incremental-cycles)를 시작 합니다.

## <a name="plan-testing-and-security"></a>테스트 및 보안 계획

사용자 프로 비전을 사용 하도록 설정 하 여 초기 파일럿에서 배포의 각 단계에서 결과가 예상 대로 테스트 되 고 프로 비전 주기를 감사 하는지 확인 합니다.

### <a name="plan-testing"></a>테스트 계획

클라우드 HR 앱을 Azure AD 사용자 프로 비전에 구성한 후 테스트 사례를 실행 하 여이 솔루션이 조직의 요구 사항을 충족 하는지 확인 합니다.

|시나리오|예상 결과|
|:-|:-|
|새 직원은 클라우드 HR 앱에서 고용 됩니다.| -사용자 계정이 Active Directory 프로 비전 됩니다.</br>-사용자는 Active Directory 도메인 앱에 로그인 하 여 원하는 작업을 수행할 수 있습니다.</br>-Azure AD Connect sync가 구성 된 경우 사용자 계정도 Azure AD에서 생성 됩니다.
|사용자가 클라우드 HR 앱에서 종료 됩니다.|-Active Directory에서 사용자 계정을 사용할 수 없습니다.</br>-사용자는 Active Directory로 보호 되는 엔터프라이즈 앱에 로그인 할 수 없습니다.
|Cloud HR 앱에서 사용자 감독 기관과 이러한 조직이 업데이트 됩니다.|특성 매핑에 따라 사용자 계정은 Active Directory에서 한 OU에서 다른 OU로 이동 합니다.|
|HR은 클라우드 HR 앱에서 사용자의 관리자를 업데이트 합니다.|Active Directory의 관리자 필드는 새 관리자의 이름을 반영 하 여 업데이트 됩니다.|
|직원을 새 역할로 다시 고용 합니다.|동작은 클라우드 HR 앱이 직원 Id를 생성 하도록 구성 된 방식에 따라 달라 집니다.</br>-이전 직원 ID를 다시 고용 하 여 다시 사용 하는 경우 커넥터는 사용자에 대 한 기존 Active Directory 계정을 사용 하도록 설정 합니다.</br>-다시 고용 하 여 새 직원 ID를 가져오는 경우 커넥터는 사용자에 대 한 새 Active Directory 계정을 만듭니다.|
|HR은 직원을 계약 작업자로 변환 하거나 그 반대로 변환 합니다.|새 가상 사용자에 대 한 새 Active Directory 계정이 만들어지고 이전 계정이 변환 개시 날짜에서 사용 하지 않도록 설정 됩니다.|

이전 결과를 사용 하 여 설정 된 타임 라인에 따라 자동 사용자 프로 비전 구현을 프로덕션으로 전환 하는 방법을 결정 합니다.

> [!TIP]
> 프로덕션 데이터를 사용 하 여 테스트 환경을 새로 고치면 개인 정보 보호 및 보안 표준을 준수 하기 위해 중요 한 개인 데이터를 제거 하거나 마스킹할 때 데이터 감소 및 데이터 스크러빙과 같은 기술을 사용 합니다. 

### <a name="plan-security"></a>보안 계획

새 서비스 배포의 일환으로 보안 검토를 요구 하는 것이 일반적입니다. 보안 검토를 수행 해야 하거나 수행 하지 않은 경우 id의 개요를 서비스로 제공 하는 여러 Azure AD [백서](https://www.microsoft.com/download/details.aspx?id=36391) 를 참조 하세요.

### <a name="plan-rollback"></a>롤백 계획

클라우드 HR 사용자 프로 비전 구현은 프로덕션 환경에서 원하는 대로 작동 하지 않을 수 있습니다. 이 경우 다음 롤백 단계를 수행 하면 이전에 알려진 양호한 상태로 되돌리는 데 도움이 될 수 있습니다.

1. [프로 비전 요약 보고서](../app-provisioning/check-status-user-account-provisioning.md#getting-provisioning-reports-from-the-azure-portal) 및 [프로 비전 로그](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview) 를 검토 하 여 영향을 받는 사용자 또는 그룹에 대해 잘못 된 작업이 수행 되었는지 확인 합니다. 프로 비전 요약 보고서 및 로그에 대 한 자세한 내용은 [클라우드 HR 앱 사용자 프로 비전 관리](#manage-your-configuration)를 참조 하세요.
2. 영향을 받는 사용자 또는 그룹의 마지막 정상 상태는 프로 비전 감사 로그를 통하거나 대상 시스템 (Azure AD 또는 Active Directory)을 검토 하 여 확인할 수 있습니다.
3. 앱 소유자와 협력 하 여 마지막으로 알려진 양호한 상태 값을 사용 하 여 앱에서 직접 영향을 받는 사용자 또는 그룹을 업데이트 합니다.

## <a name="deploy-the-cloud-hr-app"></a>클라우드 HR 앱 배포

솔루션 요구 사항에 부합 하는 클라우드 HR 앱을 선택 합니다.

**Workday**: workday에서 Active Directory 및 Azure AD로 작업자 프로필을 가져오려면 [자습서: 자동 사용자 프로 비전을 위한 Workday 구성](../saas-apps/workday-inbound-tutorial.md#planning-your-deployment)을 참조 하세요. 필요에 따라 전자 메일 주소, 사용자 이름 및 전화 번호를 Workday에 다시 쓸 수 있습니다.

**SAP SuccessFactors**: SuccessFactors에서 Active Directory 및 Azure AD로 작업자 프로필을 가져오려면 [자습서: 자동 사용자 프로 비전을 위한 SAP SuccessFactors 구성](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)을 참조 하세요. 필요에 따라 전자 메일 주소와 사용자 이름을 SuccessFactors에 다시 쓸 수 있습니다.

## <a name="manage-your-configuration"></a>구성 관리

Azure AD는 감사 로그 및 보고서를 통해 조직의 사용자 프로 비전 사용 및 운영 상태에 대 한 추가 정보를 제공할 수 있습니다.

### <a name="gain-insights-from-reports-and-logs"></a>보고서 및 로그에서 정보 얻기

[초기 주기가](../app-provisioning/how-provisioning-works.md#initial-cycle)성공적으로 완료 되 면 Azure AD 프로 비전 서비스는 다음 이벤트 중 하나가 발생할 때까지 각 앱에 대 한 자습서에 정의 된 간격으로 다시 백 엔드 증분 업데이트를 무기한으로 실행 합니다.

- 서비스가 수동으로 중지 되었습니다. [Azure Portal](https://portal.azure.com/) 또는 적절 한 [Microsoft Graph API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview) 명령을 사용 하 여 새로운 초기 순환이 트리거됩니다.
- 새 초기 주기는 특성 매핑 또는 범위 지정 필터의 변경에 업데이트로 인해 트리거됩니다.
- 높은 오류 발생률 때문에 프로 비전 프로세스가 격리 됩니다. 4 주 넘게 격리 상태로 유지 되며,이 경우 자동으로 사용 하지 않도록 설정 됩니다.

이러한 이벤트 및 프로 비전 서비스에서 수행 하는 다른 모든 작업을 검토 하려면 [로그를 검토 하 고 프로 비전 활동에 대 한 보고서를 가져오는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).

#### <a name="azure-monitor-logs"></a>Azure Monitor 로그

프로 비전 서비스에서 수행 하는 모든 작업은 Azure AD 감사 로그에 기록 됩니다. 추가 분석을 위해 Azure AD 감사 로그를 Azure Monitor 로그로 라우팅할 수 있습니다. Azure Monitor 로그 (Log Analytics 작업 영역이 라고도 함)를 사용 하 여 데이터를 쿼리하여 이벤트를 찾고, 추세를 분석 하 고, 다양 한 데이터 원본에서 상관 관계를 수행할 수 있습니다. 이 [비디오](https://youtu.be/MP5IaCTwkQg) 를 시청 하 여 실제 사용자 시나리오에서 Azure AD 로그에 Azure Monitor 로그를 사용 하는 이점을 알아보세요.

[AZURE AD 활동 로그에 대 한 log analytics 뷰](../reports-monitoring/howto-install-use-log-analytics-views.md) 를 설치 하 여 사용자 환경에서 이벤트를 프로 비전 하는 [미리 빌드된 보고서](https://github.com/AzureAD/Deployment-Plans/tree/master/Log%20Analytics%20Views) 에 액세스할 수 있습니다.

자세한 내용은 [Azure Monitor 로그를 사용 하 여 AZURE AD 활동 로그를 분석](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md)하는 방법을 참조 하세요.

### <a name="manage-personal-data"></a>개인 데이터 관리

Windows server에 설치 된 Azure AD Connect 프로 비전 에이전트는 클라우드 HR 앱에 따라 개인 데이터를 포함할 수 있는 Windows 이벤트 로그에 특성 매핑을 Active Directory 하 여 로그를 만듭니다. 사용자 개인 정보 취급 방침을 준수 하려면 이벤트 로그를 지우도록 Windows 예약 된 작업을 설정 하 고 데이터를 48 시간 이상 유지 하지 않도록 합니다.

Azure AD 프로 비전 서비스는 30 일 이상 데이터를 저장, 처리 또는 보관 하지 않으므로 보고서를 생성 하거나 분석을 수행 하거나 30 일이 지난 정보를 제공 하지 않습니다.

### <a name="troubleshoot"></a>문제 해결

프로 비전 중에 발생할 수 있는 문제를 해결 하려면 다음 문서를 참조 하세요.

- [Azure AD 갤러리 애플리케이션에 대해 사용자 프로비전 구성 문제](application-provisioning-config-problem.md)
- [응용 프로그램에 프로 비전 하기 위해 온-프레미스 Active Directory에서 Azure AD로 특성 동기화](user-provisioning-sync-attributes-for-mapping.md)
- [사용자가 Azure Active Directory 갤러리 애플리케이션에 프로비전을 구성하는 동안 관리자 자격 증명을 저장하는 문제](application-provisioning-config-problem-storage-limit.md)
- [사용자가 Azure AD 갤러리 애플리케이션에 프로비전되지 않음](application-provisioning-config-problem-no-users-provisioned.md)
- [Azure AD 갤러리 애플리케이션에 잘못된 사용자 집합이 프로비전됨](application-provisioning-config-problem-wrong-users-provisioned.md)
- [에이전트 문제 해결을 위한 Windows 이벤트 뷰어 설정](../saas-apps/workday-inbound-tutorial.md#setting-up-windows-event-viewer-for-agent-troubleshooting)
- [서비스 문제 해결을 위한 Azure Portal 감사 로그 설정](../saas-apps/workday-inbound-tutorial.md#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
- [AD 사용자 계정 생성 작업에 대한 로그 이해](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-ad-user-account-create-operations)
- [관리자 업데이트 작업에 대한 로그 이해](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-manager-update-operations)
- [일반적으로 발생하는 오류 해결](../saas-apps/workday-inbound-tutorial.md#resolving-commonly-encountered-errors)

### <a name="next-steps"></a>다음 단계

- [특성 매핑에 대한 식 작성](functions-for-customizing-application-data.md)
- [Azure AD 동기화 API 개요](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
- [범위를 벗어나는 사용자 계정 삭제 건너뛰기](skip-out-of-scope-deletions.md)
- [Azure AD Connect 프로 비전 에이전트: 버전 릴리스 기록](provisioning-agent-release-version-history.md)