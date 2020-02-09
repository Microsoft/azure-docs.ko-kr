---
title: Azure AD 클라우드 온-프레미스 워크 로드-Azure에 대 한 관리 제어
description: 이 항목에서는 온-프레미스 워크 로드에 대 한 제어 하는 클라우드 관리를 설명 합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 510a5562740260eb2946ded074a5c37804c55375
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67109513"
---
# <a name="how-azure-ad-delivers-cloud-governed-management-for-on-premises-workloads"></a>클라우드 관리 관리 온-프레미스 워크 로드에 대 한 Azure AD에서 제공 하는 방법

Azure Active Directory (Azure AD)는 포괄적인 id 수백만 개 id, 액세스 관리 및 보안의 모든 측면에 걸쳐 있는 조직에서 사용 하는 서비스 (IDaaS) 솔루션으로 합니다. Azure AD는 십억 개 사용자 id를 보유 하 고 사용자가 로그인 하 고 안전 하 게 모두에 액세스할 수 있습니다.

* Microsoft Office 365, Azure portal 및 수천 개의 다른 소프트웨어-as a Service (SaaS) 응용 프로그램 등의 외부 리소스.
* 조직의 회사 네트워크에 해당 조직에서 개발 하는 모든 클라우드 응용 프로그램과 함께 인트라넷 응용 프로그램과 같은 내부 리소스입니다.

'순수 클라우드,' 또는 '하이브리드'으로 있는 경우 배포 온-프레미스 워크 로드 하는 경우 조직에서 Azure AD를 사용할 수 있습니다. Azure AD의 하이브리드 배포에는 해당 IT 자산을 클라우드로 마이그레이션 또는 새 클라우드 서비스와 함께 기존 온-프레미스 인프라 통합을 계속 하려면 조직에 대 한 전략의 일부일 수 있습니다.

지금까지 '하이브리드' 조직으로 연장 기존 온-프레미스 인프라에는 Azure AD 살펴보았습니다. 이러한 배포에서 온-프레미스 identity 거 버 넌 스 관리, Windows Server Active Directory 또는 다른 내부 디렉터리 시스템 제어 지점을 이며 사용자 및 그룹은 Azure AD와 같은 클라우드 디렉터리에 이러한 시스템에서 동기화 됩니다. 이러한 id를 클라우드에 되 면 이러한 사용할 수 있습니다에 Office 365, Azure 및 다른 응용 프로그램입니다.

![ID 수명 주기](media/cloud-governed-management-for-on-premises//image1.png)

조직이 클라우드로 해당 응용 프로그램과 함께 IT 인프라의 자세히 나가면서 대부분 찾고자 하는 향상 된 보안 및 id 관리 서비스로 간소화 된 관리 기능입니다. Azure AD에서 클라우드 제공 IDaaS 기능에는 관리 관리 솔루션 및 조직에서 신속 하 게 채택 하 고 기존 온-프레미스에서 해당 id 관리의 더를 이동할 수 있도록 기능을 제공 하 여 클라우드 전환을 빠르게 진행 하세요. Azure ad에 기존 뿐만 아니라 새 응용 프로그램을 지원 하면서 시스템입니다.

이 문서는 IDaaS 하이브리드에 대 한 Microsoft의 전략에 간략하게 설명 하 고로 인해 조직 조직은 기존 응용 프로그램에 대 한 Azure AD를 사용 하는 방법을 설명 합니다.

## <a name="the-azure-ad-approach-to-cloud-governed-identity-management"></a>Azure AD 클라우드 관리 id 관리 방법

클라우드로 조직 전환으로 자세한 보안 및 자동화 및 자동 관리 insights에서 지원 되는 활동에 대 한 자세한 가시성-가 완벽 한 환경을 통해 컨트롤 수 있는 보증 해야 합니다. "**클라우드 관리를 제어**" 조직 관리 하 고 해당 사용자, 응용 프로그램, 그룹 및 클라우드에서 장치를 제어 하는 방법에 대해 설명 합니다.

이 최신 환경에서는 조직 규모에서 SaaS 응용 프로그램의 확산 됨에 따라 및 공동 작업 및 외부 id의 증가 역할 때문에 효과적으로 관리할 수 있으려면 필요 합니다. 클라우드의 새 위험 가로 조직을 더욱 빨리 대응할 수 해야-클라우드 사용자를 손상 하는 악의적인 행위자가 클라우드 및 온-프레미스 응용 프로그램에 영향을 줄 수를 의미 합니다.

특히 하이브리드 조직에서는 위임 하 고 자동화 하는 일을 할 수 해야 작업을 지금까지 IT 수동으로 않았습니다. 이러한 태스크를 자동화 하기 위해 필요한 Api 및 다양 한 id 관련 리소스 (사용자, 그룹, 응용 프로그램, 장치)의 수명 주기를 오케스트레이션 하는 프로세스 외부에서 더 많은 개인에 게 이러한 리소스의 일상적인 관리를 위임할 수 있습니다 있도록 IT 직원에 게를 핵심입니다. Azure AD 사용자 계정 관리를 통해 이러한 요구 사항을 해결 하 고 기본 인증 하지 않고도 사용자에 대 한 온-프레미스 id 인프라. 온-프레미스 인프라 구축 하지 해당 온-프레미스 디렉터리에서 발생 하지는 않지만 인 액세스 관리는 비즈니스 결과 달성 하는 데 중요 한 비즈니스 파트너와 같이 사용자의 새로운 커뮤니티 있는 조직에서는 이용할 수 있습니다.

또한 관리 거 버 넌 스---없이 완료 되지 않으며이 새로운 세계에서 거 버 넌 스는 추가 기능 보다는 id 시스템의 통합된 부분입니다. Id 관리는 조직 id를 관리 하 고 직원, 비즈니스 파트너 및 공급 업체 및 서비스 및 응용 프로그램 수명 주기를 액세스 하는 기능을 제공 합니다.

수를 쉽게 관리 관리를 클라우드로 전환 하는 조직이 id 관리를 통합 크기를 조정 하려면 IT 게스트를 사용 하 여 새로운 과제를 해결 하 고 심층적인 인 사이트 및 사용 하 여 고객 들 보다 자동화 제공 온-프레미스 인프라입니다. 이 새로운 세계에서 거 버 넌 스 조직은 투명도, 가시성 및 조직 내의 리소스에 액세스할 때 적절 한 제어 기능을 의미 합니다. Azure AD를 사용 하 여 보안 작업 및 감사 팀 장치의 어떤 조직, 해당 액세스를 사용 하 여 해당 사용자가 수행 하 고 조직에 있는지 여부 및 사용 하 여 적절 한 리소스에 누가---및 해야 하는 사용자-에 대 한 가시성 액세스할을 수 됩니다. 컨트롤을 제거 하거나 회사 또는 규정 정책에 따라 액세스를 제한 합니다.

새 관리 모델을 보다 쉽게 관리 하 고 해당 응용 프로그램에 대 한 액세스를 보호할 수는 그대로 SaaS와 기간 업무 (LOB) 응용 프로그램을 사용 하 여 조직 유용 합니다. Azure AD를 사용 하 여 응용 프로그램에 통합 하 여 조직이 사용 하 고 모두 클라우드에 대 한 액세스를 관리 하는 일을 할 수 점과 온-프레미스 identities 일관 되 게 합니다. 응용 프로그램 수명 주기 관리를 자동화할 더가 있으며 Azure AD는 온-프레미스 id 관리에 쉽게 달성할 수 없었던 응용 프로그램 사용에 대 한 풍부한 정보를 제공 합니다. Azure AD, Office 365 그룹 및 팀 셀프 서비스 기능을 통해 조직 수 쉽게 액세스 관리 및 공동 작업에 대 한 그룹을 만들 및 사용자 추가 또는 제거 클라우드 공동 작업 및 액세스 관리 요구 사항을를 합니다.

클라우드 관리 관리를 사용 하는 응용 프로그램에 따라 달라 집니다 및 해당 응용 프로그램을 Azure AD와 통합 됩니다 하는 방법에 대 한 오른쪽 Azure AD 기능을 선택 합니다. 다음 섹션에서는 AD 통합 응용 프로그램 및 페더레이션 프로토콜 (예를 들어, SAML, OAuth 또는 OpenID Connect)를 사용 하는 응용 프로그램에 대해 수행할 방법을 간략하게 설명 합니다.

## <a name="cloud-governed-management-for-ad-integrated-applications"></a>AD 통합 응용 프로그램에 대 한 제어 하는 클라우드 관리

Azure AD는 조직의 온-프레미스 Active Directory 통합 응용 프로그램 보안 원격 액세스 및 조건부 액세스를 통해 해당 응용 프로그램에 대 한 관리를 개선합니다. 또한 Azure AD 계정 수명 주기 관리 및 사용자의 기존 AD 계정의 경우 포함 하 여 자격 증명 관리도 제공 합니다.

* **안전한 원격 액세스 및 온-프레미스 응용 프로그램에 대 한 조건부 액세스**

대부분의 조직에서 클라우드에서 온-프레미스 AD 통합 웹 및 원격 데스크톱 기반 응용 프로그램에 대 한 액세스를 관리 하는 첫 번째 단계는 배포 하는 [응용 프로그램 프록시](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) 수 있도록 해당 응용 프로그램 앞에 보안 원격 액세스 합니다.

Azure AD에 Single Sign-On 되면 사용자는 외부 URL 또는 내부 애플리케이션 포털을 통해 클라우드와 온-프레미스 애플리케이션에 모두 액세스할 수 있습니다. 예를 들어, 응용 프로그램 프록시는 Tableau, Qlik, 기간 업무 (LOB) 응용 프로그램 등 앱 뿐만 아니라 원격 데스크톱, SharePoint, 원격 액세스 및 single sign on를 제공합니다. 또한 조건부 액세스 정책을 표시 포함 될 수 있습니다 합니다 [사용 약관](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) 하 고 [에 동의한 사용자를 확인](https://docs.microsoft.com/azure/active-directory/conditional-access/require-tou) 응용 프로그램에 액세스할 수 있을 때까지 합니다.

![앱 프록시 아키텍처](media/cloud-governed-management-for-on-premises/image2.png)

* **Active Directory 계정에 대 한 자동 수명 주기 관리**

Id 관리를 통해 간의 균형을 조정 하는 조직은 *생산성* ---얼마나 빨리 사용자가 액세스할 수 리소스를 필요한 조직에 가입할 때 같은? ---및 *보안* ---하는 방법에 대 한 액세스 변경 해야 해당 사용자의 고용 상태 변경 될 때와 같은 시간에 따른? ID 수명 주기 관리는 ID 거버넌스의 기반이며 대규모의 효과적인 거버넌스에는 애플리케이션에 대한 ID 수명 주기 관리 인프라를 현대화해야 합니다.

많은 조직에서 직원 id 수명 주기 인적 자원 관리 (HCM) 시스템에서 해당 사용자의 표현에 연결 됩니다. Workday HCM 시스템으로 사용 하 여 조직에서 Azure AD 확실히 AD의 사용자 계정은 [자동으로 프로 비전 하 고 Workday의 작업자에 대 한 프로 비전 해제](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)합니다. 계정 및 위험을 관리 하므로 잠재 고객 인터넷용의 자동화를 통해 향상 된 사용자의 생산성을 수행 하 여 응용 프로그램 액세스는 자동으로 업데이트 사용자 역할을 변경 하거나 조직을 떠날 때. Workday 기반 사용자 프로 비전 [배포 계획](https://aka.ms/WorkdayDeploymentPlan) Workday의 모범 사례 구현 함으로써 조직 5 단계 프로세스에서 Active Directory 사용자 프로 비전 솔루션을 안내 하는 단계별 가이드입니다.

Azure AD Premium에는 또한 SAP를 비롯 한 다른 온-프레미스 HCM 시스템에서 레코드를 가져올 수 있는 Microsoft Identity Manager, Oracle eBusiness, Oracle PeopleSoft 하 고 있습니다.

기업 간 공동 작업은 점점 더 조직 외부인에 게 액세스 권한을 부여 해야 합니다. [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/) 공동 작업을 사용 하면 안전 하 게 공유할 응용 프로그램과 서비스 게스트 사용자 및 외부 파트너를 사용 하 여 자신의 회사 데이터에 대 한 제어를 유지 하면서 조직 수 있습니다.

Azure AD 수 [게스트 사용자에 대 한 AD의 계정을 자동으로 만듭니다](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises) 필요에 따라 비즈니스 게스트 액세스에 사용 하도록 설정 하면 온-프레미스 AD 통합 응용 프로그램 다른 암호를 입력 하지 않고도 합니다. 조직에서는 설정할 수 있습니다 [게스트 사용자에 대 한 multi-factor authentication (MFA) 정책을](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)의MFA 응용 프로그램 프록시 인증 하는 동안 작업을 마쳤으면 확인 하도록 합니다. 또한 모든 [액세스 검토](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews) 는 클라우드 B2B 사용자가 온-프레미스 사용자에 게 적용에서 수행 됩니다. 예를 들어, 수명 주기 관리 정책을 통해 클라우드 사용자를 삭제 하는 경우 온-프레미스 사용자도 삭제 됩니다.

**Active Directory 계정에 대 한 관리 자격 증명** Azure AD의 셀프 서비스 암호 재설정을 사용 하면 사용자가 다시 인증 하 고 변경 된 암호를 사용 하 여 자신의 암호를 재설정 하는 데 암호를 잊어 버린 [에 기록 온-프레미스 Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)합니다. 암호 재설정 프로세스 온-프레미스 Active Directory 암호 정책을 이용할 수 있습니다. 사용자가 암호를 재설정 하는 경우 해당 디렉터리에 커밋하기 전에 온-프레미스 Active Directory 정책을 충족 하는지 확인 됩니다. 셀프 서비스 암호 재설정 [배포 계획](https://aka.ms/deploymentplans/sspr) 셀프 서비스 암호 재설정 웹 및 Windows 통합 환경을 통해 사용자에 게 롤아웃 하려면 모범 사례에 간략하게 설명 합니다.

![Azure AD SSPR 아키텍처](media/cloud-governed-management-for-on-premises/image3.png)

마지막으로, 사용자가 AD에서 자신의 암호를 변경 하도록 허용 하는 조직에서 AD 구성할 수 있습니다 조직은를 통해 Azure AD에서 사용 하는 동일한 암호 정책을 사용 하 여 [Azure AD 암호 보호 기능](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises), 현재 공개 미리 보기로 제공 됩니다.

조직을 클라우드로 AD 통합 응용 프로그램을 Azure에 응용 프로그램을 호스팅하는 운영 체제를 이동 하 여 이동할 준비가 되 면 [Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) -호환 되는 AD 도메인 서비스 (예: 도메인 가입을 제공 합니다. 그룹 정책, LDAP, Kerberos/NTLM 인증). Azure AD Domain Services는 조직의 기존 Azure AD 테넌트, 사용자가 회사 자격 증명을 사용 하 여 로그인 할 수 있도록와 통합 됩니다. 또한 기존 그룹과 사용자 계정을 사용할 수 있습니다 리소스에 대 한 액세스를 보호 하는 부드러운 ' 리프트 앤 시프트 ' Azure 인프라 서비스에 온-프레미스 리소스를 보장.

![Azure AD Domain Services](media/cloud-governed-management-for-on-premises/image4.png)

## <a name="cloud-governed-management-for-on-premises-federation-based-applications"></a>온-프레미스 페더레이션 기반 응용 프로그램에 대 한 제어 하는 클라우드 관리

더 안전한 액세스 및 페더레이션 관리를 위한 더 쉬운 관리 환경이 이미 온-프레미스 id 공급자를 사용 하는 조직에 대 한 사용 응용 프로그램을 Azure AD 이동 합니다. Azure AD 응용 프로그램을 Azure AD 조건부 액세스를 사용 하 여 Azure Multi-factor Authentication을 비롯 한 세부적인 응용 프로그램별 액세스 제어를 구성할 수 있습니다. Azure AD는 응용 프로그램별 토큰 서명 인증서 및 구성 가능한 인증서 만료 날짜를 비롯 한 더 많은 기능을 지원 합니다. 이러한 기능, 도구 및 지침 조직이 자신의 온-프레미스 id 공급자를 사용 중지에 사용 하도록 설정 합니다. Microsoft의 IT를 하나의 예를 들어 움직인 17,987 응용 프로그램 Microsoft의 내부 Active Directory Federation Services (AD FS)에서 Azure AD에 있습니다.

![Azure AD 발전](media/cloud-governed-management-for-on-premises/image5.png)

마이그레이션을 시작 하려면 Azure ad를 id 공급자로 페더레이션된 응용 프로그램 참조 https://aka.ms/migrateapps 에 대 한 링크를 포함 하는:

* 백서 [Azure Active Directory에 응용 프로그램 마이그레이션](https://aka.ms/migrateapps/whitepaper), 마이그레이션 혜택을 제공 하며 명확 하 게 설명 하는 4 단계에서 마이그레이션을 계획 하는 방법에 설명 합니다: 검색, 분류, 마이그레이션 및 지속적인 관리 합니다. 사용자 안내 됩니다 프로세스에 대 한 의견과 프로젝트가 사용이 쉬운 조각으로 분할 하는 방법입니다. 문서 전체에는 과정에 도움이 되는 중요한 리소스에 대한 링크가 있습니다.

* 솔루션 가이드 [마이그레이션 응용 프로그램 Active Directory Federation Services에서 Azure Active Directory에 인증](https://aka.ms/migrateapps/adfssolutionguide) 탐색 자세히 동일한 4 단계의 계획 하 고 응용 프로그램 마이그레이션 프로젝트를 실행 합니다. . 이 가이드에서는 Active Directory Federation Services (AD FS)에서 Azure AD에 응용 프로그램을 이동 하는 특정 목표에 해당 단계를 적용 하는 방법에 알아봅니다.

* 합니다 [Active Directory Federation Services 마이그레이션 준비 스크립트](https://aka.ms/migrateapps/adfstools) Azure AD로의 마이그레이션 위한 응용 프로그램의 준비 상태를 확인 하기 위해 기존 온-프레미스 Active Directory Federation Services (AD FS) 서버에서 실행할 수 있습니다.

## <a name="ongoing-access-management-across-cloud-and-on-premises-applications"></a>클라우드 및 온-프레미스 응용 프로그램에서 진행 중인 액세스 관리

조직에는 확장 가능한 액세스를 관리 하는 프로세스가 필요 합니다. 사용자는 액세스 권한을 누적 끝나는 외에 프로 비전 처음에 항목을 계속 합니다. 또한 기업이 개발 하 고 액세스 정책 및 제어를 지속적으로 적용 하도록 효율적으로 확장할 수 있으려면 필요 합니다.

IT 대리자는 일반적으로 비즈니스 의사 결정권자에 대한 승인 결정에 액세스합니다. 또한 IT에는 사용자가 스스로가 포함될 수 있습니다. 예를 들어 유럽에 있는 회사의 마케팅 애플리케이션에서 기밀 고객 데이터에 액세스하는 사용자는 회사의 정책을 알아야 합니다. 게스트 사용자도 인식 하지 못할 데이터는 해당 초대 된 조직에 대 한 처리 요구 사항입니다.

조직은와 같은 기술을 통해 액세스 수명 주기 프로세스를 자동화할 수 있습니다 [동적 그룹](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)이 고 사용자 프로 비전을 사용 하 여 결합 된 [SaaS 응용 프로그램](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list), 또는 [응용 프로그램 시스템을 사용 하 여 도메인 간 Id 관리를 위한 통합 된 (SCIM](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)) 표준입니다. 조직으로 제어할 수 있습니다는 [게스트 사용자가 온-프레미스 응용 프로그램에 액세스할](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises)합니다. 그런 다음, 정기 [Azure AD 액세스 검토](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)를 사용하여 이러한 액세스 권한을 정기적으로 검토할 수 있습니다.

## <a name="future-directions"></a>앞으로 방향

Microsoft의 전략은 하이브리드 환경에서 배포를 사용 하도록 설정 하는 합니다 **클라우드는 id에 대 한 제어 평면**, 온-프레미스 디렉터리 및 다른 온-프레미스 Active Directory 등의 다른 id 시스템과 응용 프로그램은 액세스 권한이 있는 사용자를 프로 비전에 대 한 대상입니다. 이 전략은 권한, id 및 해당 응용 프로그램 및 해당 의존 하는 워크 로드에 액세스 하도록 계속 됩니다. 이 최종 상태에 조직은 수 있을 것 최종 사용자 생산성을 클라우드에서 전적으로 합니다.

![Azure AD 아키텍처](media/cloud-governed-management-for-on-premises/image6.png)

## <a name="next-steps"></a>다음 단계

이 과정을 시작 하는 방법에 대 한 자세한 내용은에 있는 Azure AD 배포 계획을 참조 하세요. <https://aka.ms/deploymentplans> 합니다. Azure Active Directory (Azure AD) 기능을 배포 하는 방법에 대 한 종단 간 지침을 제공 합니다. 각 계획 고려 사항, 디자인 및 운영 절차가 일반적인 Azure AD 기능을 성공적으로 롤아웃하는 데 필요한 계획 비즈니스 가치를 설명 합니다. Microsoft는 지속적으로 새로운 기능을 Azure AD 사용 하 여 클라우드에서 관리 추가할 때 고객 배포 및 기타 피드백에서 학습 된 모범 사례를 사용 하 여 배포 계획을 업데이트 합니다.
