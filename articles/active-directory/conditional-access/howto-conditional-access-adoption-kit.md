---
title: 조건부 액세스 도입 키트-Azure Active Directory
description: 리소스에 대 한 액세스에 대 한 Azure AD 조건부 액세스를 채택합니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cc4ff5fb528760be8c910f3da7d5691a6aae0d8
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2019
ms.locfileid: "67387574"
---
#  <a name="adopting-azure-ad-conditional-access"></a>Azure AD 조건부 액세스를 채택합니다.

사용자는 모바일 우선, 클라우드 우선 세계에서 아무 곳 이나 다른 종류의 장치 및 앱을 사용 하 여 조직의 리소스에 액세스할 수 있습니다. 결과적으로, 리소스에 액세스할 수 있는 사용자에 초점을 두는 더 이상 충분 한 합니다. 액세스할 수 있고 사용자가 어떤 장치를 되 고 등을 식별 하는 제어할 수 있습니다.

이 제어를 제공 **Azure AD (Active Directory) 조건부 액세스** Multi-factor Authentication (MFA)와 같은 응용 프로그램에 대 한 액세스에 대 한 모든 사용자는 조건을 지정할 수 있습니다. 어떻게 권한이 있는 사용자 (클라우드 앱에 대 한 액세스 권한이 부여 된 사용자)를 제어 조건부 액세스 정책을 사용 하 여 특정 조건에서 클라우드 앱에 액세스 합니다. 가리킵니다 [Azure Active Directory의 조건부 액세스를 란](overview.md#conditional-access-policies) 자세한 내용은 합니다.

## <a name="key-benefits"></a>주요 이점

Azure AD 조건부 액세스를 사용 하는 주요 이점은 다음과 같습니다.

* **생산성 향상:** 조건부 액세스 (CA) 정책 MFA를 사용 하 여, 차단 액세스 권한이 하 라는 메시지가 나타나면 사용자는 지점 대상 수 또는 신뢰할 수 있는 장치를 사용 해야 합니다. 예를 들어, 회사 네트워크 외부 응용 프로그램에만 사용자가 MFA 요구 하는 등 정책을 설정할 수 있습니다. MFA 요청을 줄이고 유지 사용자 있는지 mfa에 로그인 할 때마다 보다 더 생산적입니다. 또한 Azure AD 조건부 액세스 정책을 사용자 단위로 지정할 수 있습니다 및 앱 별 정책을 만듭니다.
* **위험을 관리 합니다.** 조건부 액세스 정책을 사용 하도록 설정 하면 클라우드 규모 id 보호, 위험 기반 액세스 제어 기능 및 네이티브 다단계 인증 지원을 제공 합니다. Id 보호를 사용 하 여 조건부 액세스를 결합 하 여 응용 프로그램에 대 한 액세스를 차단 하거나 제어 된 체크 인 경우를 정의할 수 있습니다.
* **규정을 준수 및 거 버 넌 스:** 액세스 요청 및 승인 된 응용 프로그램에 대 한 감사 및 전체 응용 프로그램 사용 현황 이해 쉽습니다 Azure AD를 사용 하 여 수행한 모든 응용 프로그램 액세스 요청에 대 한 기본 감사 로그를 지원 하기 때문에. 감사 요청자 id, 요청한 날짜, 비즈니스 근거, 승인 상태 및 승인자 identity 포함 되어 있습니다. 이 데이터는 보안 인시던트 및 선택한 이벤트 모니터링 SIEM () 시스템에이 데이터 가져오기를 수 있도록 해 주는 API에서 사용할 수도 있습니다.
* **비용을 관리 합니다.** 액세스 정책은 Azure AD로 이동 사용자 지정에 대 한 의존도 줄일 수 또는 온-프레미스 솔루션 Active Directory 페더레이션 서비스 (ADFS)을 같은 해당 인프라를 실행 하는 비용을 절감 하는 조건부 액세스에 대 한 합니다.

## <a name="customer-case-studies"></a>고객 사례 연구

검색 방법을 대부분의 조직에서는 Azure AD 조건부 액세스를 사용 하 여 정의 하 고 조건에 따라 클라우드 앱에 액세스 하는 자동화 된 액세스 제어 결정을 구현 합니다. 다음 항목 추천된 사례 이러한 고객 요구 사항이 충족 하는 방법을 보여 줍니다.

* [**Wipro** 고객 참여를 개선 하기 위해 Microsoft 클라우드 보안 도구를 사용 하 여 모바일 생산성을 높이입니다.](https://customers.microsoft.com/story/wipro-professional-services-enterprise-mobility-security) Azure AD의 조건부 액세스 정책을 자체 회사 데이터에 대 한 제어를 유지 하면서 자신의 자격 증명---를 사용할 수 있는 신뢰할 수 있는 외부 엔터티---를 사용 하 여 문서, 리소스 및 응용 프로그램을 공유할 회사 사용 하도록 설정 합니다.
* [**Accenture** Microsoft Cloud App security를 사용 하 여 클라우드로 이동 함으로써 보호](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security) Accenture 평가 합니다 [조건부 액세스 앱 제어](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad) 활성 Azure를 사용 하는 Cloud App Security의 기능 디렉터리 특정 조건에 따라 응용 프로그램 액세스를 제어 하려면 조건부 액세스. LePenske 라는이 기능이 유용할 수 있습니다, 예를 들어 다운로드를 금지 하는 동안 읽기 전용 파일 액세스를 사용 하도록 설정 합니다.
* [**Aramex** 배달 제한-글로벌 물류 및 운송 회사 id 및 액세스 관리 솔루션을 사용 하 여 클라우드에 연결 된 office 만듭니다](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)합니다. 보안 액세스를 보장 Aramex의 원격 직원을 사용 하 여 특히 어려웠습니다. 회사는 이러한 원격 직원이 네트워크 외부에서 SaaS 응용 프로그램에 액세스할 수 있도록 하려면 조건부 액세스 적용 이제 않습니다. 조건부 액세스 규칙에 적절 한 액세스 권한이 있는 사용자만 제공 되는 Multi-factor Authentication 적용 여부를 결정 됩니다.

Azure AD 조건부 액세스에서 고객 및 파트너 환경에 대 한 자세한 내용은 다음을 방문- [놀라운 작업 사용자는 Azure를 사용 하 여 수행 하는 참조](https://azure.microsoft.com/case-studies/?service=active-directory)합니다.

## <a name="announcements"></a>알림

Azure AD는 지속적인 향상되고 있습니다. 가장 최근 개발 작업을 사용 하 여 최신 참조 [Azure Active Directory의 새로운 기능?](../fundamentals/whats-new.md)

기술 커뮤니티 및 Microsoft Id 부서에서 최근 블로그:

* 2018 년 9 월 24 일 [Azure Databricks에서 Azure Active Directory 조건부 액세스](https://azure.microsoft.com/updates/azure-active-directory-conditional-access-in-azure-databricks/)
* 2018 년 9 월 21 일 [사용자 지정 컨트롤은 공개 미리 보기로 제공 되는 Azure AD 조건부 액세스](https://azure.microsoft.com/updates/azure-ad-conditional-access-custom-controls-are-in-public-preview/)
* 2018 년 9 월 21 일 [Microsoft Cloud App Security를 사용 하 여 제한 된 액세스에 대 한 Azure AD 조건부 액세스 지원 출시 되었습니다.](https://azure.microsoft.com/updates/azure-ad-conditional-access-support-for-limited-access-with-microsoft-cloud-app-security-is-now-available/)
* 2018 년 9 월 21 일 [Azure AD 조건부 액세스: 관리 되는 미리 보기에서 이제 iOS/Android 플랫폼에 대 한 브라우저 지원](https://azure.microsoft.com/updates/azure-ad-conditional-access-managed-browser-support-for-ios-android-platforms-now-in-preview/)
* 2018 년 9 월 21 일 [국가 코드에 대 한 Azure AD 조건부 액세스는 공개 미리 보기](https://azure.microsoft.com/updates/azure-ad-conditional-access-for-country-codes-is-in-public-preview/)
* 2018 년 9 월 21 일 [Azure AD 사용 약관 사용 가능](https://azure.microsoft.com/updates/azure-ad-terms-of-use-now-available/)

## <a name="learning-resources"></a>학습 리소스

Azure AD 조건부 액세스 기능 개요를 보려면 아래 링크를 따르세요.

* 에 대해 알아봅니다 "[Azure Active Directory의 조건부 액세스를 란?](overview.md)"
* 알고 있는 "[Azure Active Directory 조건부 액세스의 조건 이란?](conditions.md)"
* 알고 있는 "[Azure Active Directory 조건부 액세스의 위치 조건 이란?](location-condition.md)"
* 알고 있는 "[Azure Active Directory 조건부 액세스의 제어 액세스 이란?](controls.md)"
* 찾을 "[도구 란 무엇을 하는 경우 Azure Active Directory 조건부 액세스?"](what-if-tool.md)
* 에 따라 [Azure Active Directory의 조건부 액세스 모범 사례](best-practices.md)

또한 Azure Active Directory와 통합 되는 모든 서비스에 대 한 액세스를 보호 하는 지침은 다음 링크를 참조 하세요.

* [초기 보호 (미리 보기) 이란?](baseline-protection.md) 초기 보호를 사용할 수 있게 이상 Azure Active Directory 환경에서 사용 하도록 설정 하는 보안 수준입니다.
* [Id 및 장치 액세스 구성](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)합니다. 권장 되는 환경 및 조건부 액세스 정책 및 관련된 기능에 대해 지정 된 집합을 포함 한 구성을 구현 하 여 Enterprise Mobility + Security 제품을 통해 클라우드 서비스에 대 한 보안 액세스를 구성 하는 방법을 설명 합니다.
* [Azure Active Directory 조건부 액세스 설정 참조](technical-reference.md)합니다. 에 대해 알아봅니다.
   * 앱 조건부 액세스 사용
   * 조건부 액세스를 사용 하 여 어떤 서비스가 활성화 되어 있습니까?
* [보안 사용자 액세스를 위한 Azure Active Directory 조건부 액세스를 사용 하도록 설정](https://www.youtube.com/watch?v=eLAYBwjCGoA)합니다. 조건부 액세스 다른 Enterprise Mobility Suite의 워크 로드에서 역할을 담당 하는 방법에 대해 알아보려면이 동영상을 시청 하세요.

### <a name="training-videos"></a>교육 비디오

**Enterprise Mobility + Security의 조건부 액세스**
   > [!VIDEO https://www.youtube.com/embed/A7IrxAH87wc]

**장치 기반 조건부 액세스**
   > [!VIDEO https://www.youtube.com/embed/AdM0zYB-3WQ]

**보안 사용자 액세스를 위한 Azure Active Directory 조건부 액세스 사용**
   > [!VIDEO https://www.youtube.com/embed/eLAYBwjCGoA]

### <a name="online-courses"></a>온라인 과정

다음 조건부 액세스 코스 및 기타 정보를 참조 [Pluralsight.com](https://www.pluralsight.com/):

* Pluralsight.com: [Microsoft Azure에서 Id 관리를 디자인](https://www.pluralsight.com/courses/microsoft-azure-identity-management-design)
   * "이이 과정에서는 Azure AD 사용 하 여 id 관리 솔루션을 디자인 하는 알아야 할 핵심 항목에는 합니다." Azure AD 조건부 액세스 "를 사용 하 여 역할 및 Azure AD 사용 하 여 액세스 제어"에 대해서는 모듈입니다.

* Pluralsight.com: [Microsoft Azure에 대 한 디자인 인증](https://www.pluralsight.com/courses/microsoft-azure-authentication-design)
   * "이이 과정에는 모든 클라우드 인증 요구 사항을 해결 하기 위해 Azure AD를 사용 하는 방법을 설명 합니다." Azure AD 조건부 액세스는 "인증 요구 사항에 대 한 다양 한 시나리오" 모듈에 적용 됩니다.

* Pluralsight.com: [Microsoft Azure에 대 한 권한 부여 디자인](https://www.pluralsight.com/courses/microsoft-azure-authorization-design)
   * "이이 과정에서는 Azure 및 Azure를 사용 하 여 사용할 수 있는 권한 부여 옵션 AD." Azure AD 조건부 액세스는 "Azure Resource Manager 및 Azure AD를 사용 하 여 권한 부여" 모듈에 적용 됩니다.

### <a name="books"></a>온라인 설명서

* O'Reilly- [Second Edition-Azure 솔루션을 구현 합니다.](https://www.oreilly.com/library/view/implementing-azure-solutions/9781789343045/b7ead3db-eb1c-4ace-897e-86ee25ea86be.xhtml)
   * "Azure 서비스를 사용 하 여 즉시 가동 및 실행 하 고 조직에서이 구현 하는 방법을 알아봅니다. Azure AD 조건부 액세스는 다룬 [배포 및 Azure Active Directory 동기화](https://learning.oreilly.com/library/view/implementing-azure-solutions/9781789343045/02ca8bba-08cf-4691-a7d0-1b96e286e7ea.xhtml). "

* Wiley- [Mastering Microsoft Azure 인프라 서비스](https://www.wiley.com/Mastering+Microsoft+Azure+Infrastructure+Services-p-9781119003298)
   * "Here is 이해, 평가, 배포 및 Microsoft Azure를 활용 하는 환경을 유지 관리 하는 데 필요한 모든 것."

## <a name="white-papers"></a>백서

* 2018 년 12 월 18 일 게시 [Azure Active Directory를 사용 하 여 탄력적 액세스 제어 관리 전략 만들기](../authentication/concept-resilient-controls.md)
   * 이 문서에 예기치 않은 중단 중 잠금의 위험을 줄이기 위해 복원 력을 채택 조직의 전략에 대 한 지침을 제공 합니다.

* 2018 년 9 월 18 일 게시 [마이그레이션에 대 한 리소스를 Azure Active Directory 응용 프로그램](../manage-apps/migration-resources.md)
   * 이 백서는 Azure Active Directory (Azure AD)에 응용 프로그램 액세스 및 인증을 마이그레이션할 수 있도록 리소스의 목록을 포함 합니다.

* 2018 년 7 월 12 일 게시 [Azure Security 및 Compliance Blueprint: PaaS 웹 응용 프로그램 영국 공식 워크 로드에 대 한 호스팅](../../security/blueprints/ukofficial-paaswa-overview.md)
   * Azure Blueprint는 승인 또는 규정 준수 요구 사항이 있는 시나리오에 대한 솔루션을 제공하기 위해 클라우드 기반 아키텍처를 배포하는 지침 문서와 자동화 템플릿으로 구성되어 있습니다.

## <a name="guidance-for-it-administrators"></a>IT 관리자에 대 한 지침

에 로그인 합니다 [Azure portal](https://portal.azure.com/) 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자입니다. 참조 [Azure Active Directory에서 관리자 역할 권한.](../users-groups-roles/directory-assign-admin-roles.md)

IT 관리자를 사용 하 여 [Azure AD 조건부 액세스](overview.md) 사용자가 Azure Multi-factor Authentication을 사용 하 여 인증에 필요한 로그인 신뢰할 수 있는 네트워크 또는 신뢰할 수 있는 장치입니다.

시작 하는 데 유용한 링크는 다음과 같습니다.

* [Azure Active Directory의 조건부 액세스 모범 사례](best-practices.md)
* [조건부 액세스 정책에서 제외 된 사용자 관리를 사용 하 여 Azure AD 액세스 검토](../governance/conditional-access-exclusion.md)
* [방법: Azure Active Directory에서 조건부 액세스 배포 계획](plan-conditional-access.md)
* [빠른 시작: Azure Active Directory 조건부 액세스를 사용 하 여 특정 앱에 대 한 mfa](app-based-mfa.md)
* [빠른 시작: 클라우드 앱에 액세스하기 전에 사용 약관에 동의하도록 요구](require-tou.md)
* [빠른 시작: Azure Active Directory 조건부 액세스를 사용 하 여 세션 위험이 감지 되 면 액세스 차단](app-sign-in-risk.md)
* [Azure AD 조건부 액세스 Faq](faqs.md)
   * 추가 질문이 있으면 볼 수도 있습니다는 [MSDN 포럼](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&sort=relevancedesc&brandIgnore=True&searchTerm=password+reset+azure)합니다.
   * 문제에 대 한 답을 찾을 수 없는 경우 지원 팀 항상 사용할 주십시오. 사용 하 여 [Contact Microsoft support](../authentication/active-directory-passwords-troubleshoot.md#contact-microsoft-support)합니다.

### <a name="tutorials"></a>자습서

* [**빠른 시작: Azure Active Directory 조건부 액세스를 사용 하 여 특정 앱에 대 한 mfa**](app-based-mfa.md)
   * 이 빠른 시작에는 사용자 환경에서 선택한 클라우드 앱에 대 한 다단계 인증을 요구 하는 Azure AD 조건부 액세스 정책을 구성 하는 방법을 보여 줍니다.

* [**빠른 시작: 클라우드 앱에 액세스 하기 전에 허용할 사용 약관 필요**](require-tou.md)
   * 이 빠른 시작에는 사용자 환경에서 선택한 클라우드 앱에 대 한 허용할 ToU는 Azure AD 조건부 액세스 정책을 구성 하는 방법을 보여 줍니다.

* [**빠른 시작: Azure Active Directory 조건부 액세스를 사용 하 여 세션 위험이 감지 되 면 액세스 차단**](app-sign-in-risk.md)
   * 이 빠른 시작에 구성 된 로그인 위험 수준 감지 하는 경우 로그인을 차단 하는 조건부 액세스 정책을 구성 하는 방법을 보여 줍니다.

* [자습서: **Azure portal에서 다단계 인증이 필요한 클래식 정책 마이그레이션**](policy-migration-mfa.md)
   * 이 자습서에는 클라우드 앱에 대 한 multi-factor authentication (MFA)을 필요한 클래식 정책을 마이그레이션하는 방법을 보여 줍니다.

## <a name="end-user-readiness-and-communication"></a>최종 사용자 준비 상태 및 통신

조건부 액세스는 최종 사용자 환경에 영향을 줄 수 있는 다른 Azure AD 기능을 사용 합니다. 예를 들어, 사용자에 대 한 강력한 인증을 사용 하도록 설정 하려면 Azure multi-factor authentication을 사용할 수 있습니다. 이 경우 Azure mfa 최종 사용자가 템플릿을 사용 합니다.

## <a name="next-steps"></a>다음 단계

* 사용 하 여 배포를 시작 합니다 [조건부 액세스 배포 계획 설명서](plan-conditional-access.md)합니다.
