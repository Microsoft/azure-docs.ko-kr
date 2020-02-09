---
title: Azure Active Directory 인증 관리 작업 참조 가이드
description: 이 작업 참조 가이드에서는 인증 관리를 보호 하기 위해 수행 해야 하는 검사 및 작업에 대해 설명 합니다.
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 934fe8271796ed6196c9e50a0eddd5d7de3d8432
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76511895"
---
# <a name="azure-active-directory-authentication-management-operations-reference-guide"></a>Azure Active Directory 인증 관리 작업 참조 가이드

[AZURE AD 작업 참조 가이드](active-directory-ops-guide-intro.md) 의이 섹션에서는 자격 증명을 보호 하 고 관리 하며, 인증 환경을 정의 하 고, 할당을 위임 하 고, 사용을 측정 하 고, 엔터프라이즈 보안 상태에 따라 액세스 정책을 정의 하기 위해 수행 해야 하는 검사 및 작업에 대해 설명 합니다.

> [!NOTE]
> 이러한 권장 사항은 게시 날짜를 따라 최신 이지만 시간이 지남에 따라 변경 될 수 있습니다. 조직에서는 Microsoft 제품 및 서비스가 시간이 지남에 따라 발전 함에 따라 id 사례를 지속적으로 평가 해야 합니다.

## <a name="key-operational-processes"></a>주요 운영 프로세스

### <a name="assign-owners-to-key-tasks"></a>키 작업에 소유자 할당

Azure Active Directory를 관리 하려면 롤아웃 프로젝트에 포함 되지 않을 수 있는 주요 운영 작업 및 프로세스를 지속적으로 실행 해야 합니다. 환경 최적화를 위해 이러한 작업을 설정 하는 것도 중요 합니다. 핵심 작업과 권장 소유자는 다음과 같습니다.

| Task | 소유자 |
| :- | :- |
| Azure AD에서 SSO (Single Sign-On) 구성의 수명 주기 관리 | IAM 운영 팀 |
| Azure AD 응용 프로그램에 대 한 조건부 액세스 정책 디자인 | InfoSec 아키텍처 팀 |
| SIEM 시스템의 보관 로그인 활동 | InfoSec 운영 팀 |
| SIEM 시스템에서 위험 이벤트 보관 | InfoSec 운영 팀 |
| 보안 보고서 심사 및 조사 | InfoSec 운영 팀 |
| 위험 이벤트 심사 및 조사 | InfoSec 운영 팀 |
| Azure AD ID 보호에서 위험 및 취약성 보고서에 대해 플래그가 지정 된 사용자를 심사 하 고 조사 합니다. | InfoSec 운영 팀 |

> [!NOTE]
> Azure AD ID 보호에 Azure AD Premium P2 라이선스가 필요 합니다. 요구 사항에 맞는 적절 한 라이선스를 찾으려면 [Azure AD Free 및 Azure AD Premium 버전의 일반 기능 비교](https://azure.microsoft.com/pricing/details/active-directory/)를 참조 하세요.

목록을 검토할 때 소유자가 없는 작업의 소유자를 할당 하거나 위의 권장 사항에 맞지 않는 소유자가 있는 작업에 대 한 소유권을 조정 해야 할 수 있습니다.

#### <a name="owner-recommended-reading"></a>소유자 권장 읽기

- [Azure Active Directory에서 관리자 역할 할당](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Azure에서 거버넌스](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="credentials-management"></a>자격 증명 관리

### <a name="password-policies"></a>암호 정책

안전 하 게 암호를 관리 하는 것은 id 및 액세스 관리의 가장 중요 한 부분 중 하나 이며 종종 공격의 가장 큰 목표 중 하나입니다. Azure AD는 공격이 성공 하지 못하도록 하는 데 도움이 되는 몇 가지 기능을 지원 합니다.

아래 표를 사용 하 여 해결 해야 하는 문제를 완화 하기 위한 권장 솔루션을 찾을 수 있습니다.

| 문제 | 권장 |
| :- | :- |
| 약한 암호 로부터 보호 하는 메커니즘이 없습니다. | Azure AD [SSPR (셀프 서비스 암호 재설정)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks) 및 [암호 보호](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises) 를 사용 하도록 설정 |
| 누출 암호를 검색 하는 메커니즘이 없습니다. | 정보를 얻기 위해 phs ( [암호 해시 동기화](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) ) 사용 |
| AD FS를 사용 하 여 관리 되는 인증으로 이동할 수 없음 | [AD FS 엑스트라넷 스마트 잠금](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) 및/또는 [Azure AD 스마트 잠금](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout) 사용 |
| 암호 정책에서는 길이, 다중 문자 집합 또는 만료와 같은 복잡성 기반 규칙을 사용 합니다. | [Microsoft 권장 사례](https://aka.ms/passwordguidance) 를 고려 하 고 암호 관리에 대 한 접근 방식을 전환 하 고 [Azure AD 암호 보호](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad)를 배포 합니다. |
| 사용자가 MFA (multi-factor authentication)를 사용 하도록 등록 되지 않음 | 암호와 함께 사용자의 id를 확인 하는 메커니즘으로 사용할 수 있도록 [모든 사용자의 보안 정보를 등록](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-mfa-policy) 합니다. |
| 사용자 위험에 따라 암호를 해지 하지 않습니다. | SSPR를 사용 하 여 누출 자격 증명에 대 한 암호 변경을 강제로 적용 하도록 Azure AD [Id 보호 사용자 위험 정책](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy) 배포 |
| 식별 된 IP 주소에서 들어오는 잘못 된 행위자 로부터 악성 인증을 보호 하는 스마트 잠금 메커니즘이 없습니다. | 암호 해시 동기화 또는 pta ( [통과 인증](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) )를 사용 하 여 클라우드 관리 인증 배포 |

#### <a name="password-policies-recommended-reading"></a>암호 정책 권장 읽기

- [Azure AD 및 AD FS 모범 사례: 암호 스프레이 공격 으로부터 방어-Enterprise Mobility + Security](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

### <a name="enable-self-service-password-reset-and-password-protection"></a>셀프 서비스 암호 재설정 및 암호 보호 사용

암호를 변경 하거나 다시 설정 해야 하는 사용자는 볼륨의 가장 큰 출처와 지원 센터 통화 비용 중 하나입니다. 비용 외에도 사용자 위험을 완화 하기 위한 도구로 암호를 변경 하는 것은 조직의 보안 상태를 개선 하는 기본 단계입니다.

최소한 Azure AD SSPR ( [셀프 서비스 암호 재설정](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks) ) 및 온-프레미스 [암호 보호](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-deploy) 를 배포 하 여 다음을 수행 하는 것이 좋습니다.

- Deflect 지원 센터에 전화를 겁니다.
- 임시 암호 사용을 대체 합니다.
- 온-프레미스 솔루션을 사용 하는 기존 셀프 서비스 암호 관리 솔루션을 대체 합니다.
- 조직에서 [약한 암호를 제거](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad) 합니다.

> [!NOTE]
> Azure AD Premium P2 구독이 있는 조직의 경우 SSPR를 배포 하 고 [Id 보호 사용자 위험 정책의](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)일부로 사용 하는 것이 좋습니다.

### <a name="strong-credential-management"></a>강력한 자격 증명 관리

암호 자체는 잘못 된 행위자가 사용자 환경에 액세스 하지 못하도록 하는 데 충분히 안전 하지 않습니다. 최소한 MFA (multi-factor authentication)에 대해 권한 있는 계정을 사용 하는 모든 사용자를 사용 하도록 설정 해야 합니다. 이상적으로는 결합 된 [등록](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined) 을 사용 하도록 설정 하 고 모든 사용자가 [결합 된 등록 환경을](https://docs.microsoft.com/azure/active-directory/user-help/user-help-security-info-overview)사용 하 여 MFA 및 SSPR에 등록 해야 합니다. 결국 예상치 못한 상황으로 인해 잠금의 위험을 줄이기 위해 [복원 력을 제공](https://docs.microsoft.com/azure/active-directory/authentication/concept-resilient-controls) 하는 전략을 채택 하는 것이 좋습니다.

![결합 된 사용자 환경 흐름](./media/active-directory-ops-guide/active-directory-ops-img4.png)

### <a name="on-premises-outage-authentication-resiliency"></a>온-프레미스 중단 인증 복원 력

간단한 자격 증명 검색 기능 외에도 Azure AD의 PHS (암호 해시 동기화) 및 Azure MFA를 사용 하면 [NotPetya](https://www.microsoft.com/security/blog/2018/02/05/overview-of-petya-a-rapid-cyberattack/)와 같은 사이버 공격로 인 한 온-프레미스 중단에도 불구 하 고 사용자가 SaaS 응용 프로그램 및 Office 365에 액세스할 수 있습니다. 페더레이션을 사용 하는 동시에 페더레이션을 사용 하도록 설정할 수도 있습니다. PHS를 사용 하도록 설정 하면 페더레이션 서비스를 사용할 수 없을 때 인증을 대체 합니다.

온-프레미스 조직에 중단 복원 력이 없거나 Azure AD와 통합 되지 않은 전략이 있는 경우 Azure AD PHS를 배포 하 고 PHS를 포함 하는 재해 복구 계획을 정의 해야 합니다. Azure AD PHS를 사용 하도록 설정 하면 사용자가 온-프레미스 Active Directory을 사용할 수 없는 경우 Azure AD에 대해 인증할 수 있습니다.

![암호 해시 동기화 흐름](./media/active-directory-ops-guide/active-directory-ops-img5.png)

인증 옵션을 더 잘 이해 하려면 [Azure Active Directory 하이브리드 id 솔루션에 적합 한 인증 방법 선택](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)을 참조 하세요.

### <a name="programmatic-usage-of-credentials"></a>자격 증명의 프로그래밍 방식 사용

PowerShell 또는 Graph API를 사용 하는 응용 프로그램을 사용 하는 Azure AD 스크립트는 보안 인증이 필요 합니다. 자격 증명 관리가 잘못 되 면 이러한 스크립트 및 도구를 실행 하면 자격 증명 도난 위험이 증가 합니다. 하드 코드 된 암호 또는 암호 프롬프트를 사용 하는 스크립트나 응용 프로그램을 사용 하는 경우 먼저 구성 파일 또는 소스 코드에서 암호를 검토 한 다음 해당 종속성을 바꾸고 가능한 경우 Azure 관리 되는 Id, Windows 통합 인증 또는 [인증서](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-access-api-with-certificates) 를 사용 해야 합니다. 이전 솔루션을 사용할 수 없는 응용 프로그램의 경우 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)를 사용 하는 것이 좋습니다.

암호 자격 증명을 사용 하는 서비스 주체가 있고 이러한 암호 자격 증명이 스크립트나 응용 프로그램에 의해 보호 되는 방식을 잘 모르겠으면 응용 프로그램 소유자에 게 문의 하 여 사용 패턴을 보다 잘 이해할 수 있습니다.

또한 암호 자격 증명이 있는 서비스 사용자가 있는 경우 응용 프로그램 소유자에 게 문의 하 여 사용 패턴을 파악 하는 것이 좋습니다.

## <a name="authentication-experience"></a>인증 환경

### <a name="on-premises-authentication"></a>온-프레미스 인증

암호 해시 동기화 또는 통과 인증을 사용 하 여 IWA (Windows 통합 인증) 또는 원활한 SSO (Single Sign-on) 관리 인증을 사용 하는 페더레이션된 인증은 회사 네트워크 내부에서 온-프레미스 도메인 컨트롤러에 대 한 시야입니다. 피로 자격 증명 프롬프트를 최소화 하 고 사용자가 피싱 공격에 받기 쉽습니다 수 있는 위험을 줄입니다. 이미 PHS 또는 PTA를 사용 하 여 클라우드 관리 인증을 사용 하 고 있지만 사용자가 온-프레미스에서 인증할 때 여전히 암호를 입력 해야 하는 경우에는 [원활한 SSO](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)를 즉시 배포 해야 합니다. 반면에 현재 클라우드 관리 인증으로 마이그레이션할 계획으로 페더레이션 하는 경우 마이그레이션 프로젝트의 일부로 원활한 SSO를 구현 해야 합니다.

### <a name="device-trust-access-policies"></a>장치 신뢰 액세스 정책

디바이스는 조직의 사용자처럼 보호해야 하는 핵심 ID입니다. 디바이스의 ID를 사용하여 언제 어디서든 리소스를 보호할 수 있습니다. 장치를 인증 하 고 해당 신뢰 유형에 대해 계정을 인증 하면 다음과 같은 방식으로 보안 상태 및 사용 편리성을 향상 시킵니다.

- 장치를 신뢰할 수 있는 경우와 같이 원활한 방지
- 신뢰할 수 없는 장치에서 액세스 차단
- Windows 10 장치의 경우 [온-프레미스 리소스에 대 한 Single Sign-On를 원활 하 게](https://docs.microsoft.com/azure/active-directory/devices/azuread-join-sso)제공 합니다.

다음 방법 중 하나를 사용 하 여 장치 id를 가져오고 Azure AD에서 관리 하 여이 목표를 수행할 수 있습니다.

- 조직에서는 [Microsoft Intune](https://docs.microsoft.com/intune/what-is-intune) 를 사용 하 여 장치를 관리 하 고 규정 준수 정책을 적용 하며 장치 상태를 증명 하 고, 장치가 규격 인지 여부에 따라 조건부 액세스 정책을 설정할 수 있습니다. IOS 장치, Mac 데스크톱 (JAMF 통합을 통해), Windows 데스크톱 (Windows 10의 경우 기본적으로 모바일 장치 관리를 사용 하 고, Microsoft 엔드포인트 Configuration Manager로 공동 관리) 및 Android 모바일 장치를 관리할 수 Microsoft Intune.
- [하이브리드 AZURE AD 조인은](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-managed-domains) Active Directory 도메인에 가입 된 컴퓨터 장치를 사용 하는 환경에서 그룹 정책 또는 Microsoft 엔드포인트 Configuration Manager에 대 한 관리를 제공 합니다. 조직은 원활한 SSO를 사용 하 여 PHS 또는 PTA를 통해 관리 되는 환경을 배포할 수 있습니다. 장치를 Azure AD로 가져오면 클라우드 및 온-프레미스 리소스에 대해 SSO를 통해 사용자 생산성을 극대화 하 고, 동시에  [조건부 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) 를 사용 하 여 클라우드 및 온-프레미스 리소스에 안전 하 게 액세스할 수 있도록 합니다.

클라우드에 등록 되지 않은 도메인 가입 windows 장치 또는 클라우드에 등록 되어 있고 조건부 액세스 정책을 사용 하지 않는 도메인에 가입 된 windows 장치를 사용 하는 경우 등록 되지 않은 장치를 등록 하 고 두 경우 모두 조건부 액세스 정책에서 [컨트롤로 하이브리드 AZURE AD 조인을 사용](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) 합니다.

![하이브리드 장치를 요구 하는 조건부 액세스 정책에서 허용의 스크린샷](./media/active-directory-ops-guide/active-directory-ops-img6.png)

MDM 또는 Microsoft Intune를 사용 하 여 장치를 관리 하지만 조건부 액세스 정책에서 장치 컨트롤을 사용 하지 않는 경우, 장치를 해당 정책에서 컨트롤로 [규격으로 표시](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices#require-device-to-be-marked-as-compliant) 해야 합니다 .를 사용 하는 것이 좋습니다.

![장치 준수를 요구 하는 조건부 액세스 정책에 부여의 스크린샷](./media/active-directory-ops-guide/active-directory-ops-img7.png)

#### <a name="device-trust-access-policies-recommended-reading"></a>장치 신뢰 액세스 정책 권장 읽기

- [방법: 하이브리드 Azure Active Directory 조인 구현 계획](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan)
- [ID 및 디바이스 액세스 구성](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

### <a name="windows-hello-for-business"></a>비즈니스용 Windows Hello

Windows 10에서 [비즈니스용 Windows Hello](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification) 는 pc에서 강력한 2 단계 인증으로 암호를 바꿉니다. 비즈니스용 Windows Hello를 사용 하면 사용자에 게 보다 간소화 된 MFA 환경을 사용 하 여 암호에 대 한 종속성을 줄일 수 있습니다. Windows 10 장치를 롤아웃 하지 않았거나 일부만 배포 된 경우 Windows 10으로 업그레이드 하 고 모든 장치에서 [비즈니스용 Windows Hello를 사용 하도록 설정](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) 하는 것이 좋습니다.

암호 없는 인증에 대 한 자세한 내용은 [Azure Active Directory를 사용 하 여 암호가 없는 전 세계를](https://aka.ms/passwordlessdoc)참조 하세요.

## <a name="application-authentication-and-assignment"></a>응용 프로그램 인증 및 할당

### <a name="single-sign-on-for-apps"></a>앱에 대 한 Single sign-on

전체 기업에 표준화 된 Single Sign-On 메커니즘을 제공 하는 것은 최상의 사용자 환경, 위험 감소, 보고 기능 및 거 버 넌 스에 매우 중요 합니다. Azure AD에서 SSO를 지 원하는 응용 프로그램을 사용 하지만 현재 로컬 계정을 사용 하도록 구성 된 응용 프로그램을 사용 하는 경우 Azure AD에서 SSO를 사용 하도록 해당 응용 프로그램을 다시 구성 해야 합니다. 마찬가지로, Azure AD에서 SSO를 지 원하는 응용 프로그램을 사용 하지만 다른 Id 공급자를 사용 하는 경우에도 Azure AD에서 SSO를 사용 하도록 해당 응용 프로그램을 다시 구성 해야 합니다. 페더레이션 프로토콜을 지원 하지 않지만 폼 기반 인증을 지 원하는 응용 프로그램의 경우 Azure AD 응용 프로그램 프록시에서 [암호 보관](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-password-vaulting) 을 사용 하도록 응용 프로그램을 구성 하는 것이 좋습니다.

![AppProxy 암호 기반 로그온](./media/active-directory-ops-guide/active-directory-ops-img8.png)

> [!NOTE]
> 조직에서 관리 되지 않는 응용 프로그램을 검색 하는 메커니즘이 없는 경우 [Microsoft Cloud App Security](https://www.microsoft.com/enterprise-mobility-security/cloud-app-security)와 같은 cloud access security broker 솔루션 (casb)을 사용 하 여 검색 프로세스를 구현 하는 것이 좋습니다.

마지막으로 Azure AD 앱 갤러리가 있고 Azure AD에서 SSO를 지 원하는 응용 프로그램을 사용 하는 경우 [앱 갤러리에 응용 프로그램을 나열](https://docs.microsoft.com/azure/active-directory/develop/howto-app-gallery-listing)하는 것이 좋습니다.

#### <a name="single-sign-on-recommended-reading"></a>Single sign-on 권장 읽기

- [응용 프로그램 액세스 및 Single Sign-On Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

### <a name="migration-of-ad-fs-applications-to-azure-ad"></a>Azure AD로 AD FS 응용 프로그램 마이그레이션

[앱을 AD FS에서 AZURE AD로 마이그레이션하면](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-apps-to-azure) 보안, 보다 일관 된 관리 효율성 및 더 나은 공동 작업 환경에서 추가 기능을 사용할 수 있습니다. Azure AD에서 SSO를 지 원하는 AD FS에서 구성 된 응용 프로그램이 있는 경우 Azure AD에서 SSO를 사용 하도록 해당 응용 프로그램을 다시 구성 해야 합니다. Azure AD에서 지원 되지 않는 일반적인 구성을 사용 하 여 AD FS에서 구성 된 응용 프로그램을 사용 하는 경우 앱 소유자에 게 문의 하 여 특수 구성이 응용 프로그램의 절대 요구 사항 인지를 이해 해야 합니다. 필요 하지 않은 경우 Azure AD에서 SSO를 사용 하도록 응용 프로그램을 다시 구성 해야 합니다.

![기본 id 공급자로 Azure AD](./media/active-directory-ops-guide/active-directory-ops-img9.png)

> [!NOTE]
> [ADFS에 대 한 Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs) 는 잠재적으로 Azure AD로 마이그레이션할 수 있는 각 응용 프로그램에 대 한 구성 정보를 수집 하는 데 사용할 수 있습니다.

### <a name="assign-users-to-applications"></a>응용 프로그램에 사용자 할당

[응용 프로그램에 사용자를 할당](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups) 하는 것은 대규모의 유연성과 관리 기능을 허용 하므로 그룹을 사용 하 여 가장 잘 매핑됩니다. 그룹을 사용할 경우의 이점에는 [특성 기반 동적 그룹 멤버 자격](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership) 및 [앱 소유자에 대 한 위임이](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-accessmanagement-managing-group-owners)포함 됩니다. 따라서 그룹을 이미 사용 하 고 관리 하는 경우 다음 작업을 수행 하 여 규모에 따라 관리를 개선 하는 것이 좋습니다.

- 그룹 관리 및 관리를 응용 프로그램 소유자에 게 위임 합니다.
- 응용 프로그램에 대 한 셀프 서비스 액세스를 허용 합니다.
- 사용자 특성이 응용 프로그램에 대 한 액세스를 일관 되 게 결정할 수 있는 경우 동적 그룹을 정의 합니다.
- [AZURE AD 액세스 검토](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)를 사용 하 여 응용 프로그램 액세스에 사용 되는 그룹에 증명을 구현 합니다.

반면, 개별 사용자에 게 할당 된 응용 프로그램을 찾은 경우에는 해당 응용 프로그램에 대 한 [관리](https://docs.microsoft.com/azure/active-directory/governance/index) 를 구현 해야 합니다.

#### <a name="assign-users-to-applications-recommended-reading"></a>응용 프로그램에 사용자 할당 권장 읽기

- [Azure Active Directory에서 응용 프로그램에 사용자 및 그룹 할당](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups)
- [Azure Active Directory에서 앱 등록 권한 위임](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-delegate-app-roles)
- [Azure Active Directory 그룹에 대 한 동적 멤버 자격 규칙](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

## <a name="access-policies"></a>액세스 정책

### <a name="named-locations"></a>명명된 위치

Azure AD의 [명명 된 위치](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations) 를 사용 하 여 조직에서 신뢰할 수 있는 IP 주소 범위에 레이블을 지정할 수 있습니다. Azure AD는 명명된 위치를 사용하여 다음 작업을 수행합니다.

- 위험 이벤트의 가양성을 방지 합니다. 신뢰할 수 있는 네트워크 위치에서 로그인 하면 사용자의 로그인 위험이 줄어듭니다.
- [위치 기반 조건부 액세스](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)를 구성합니다.

![명명된 위치](./media/active-directory-ops-guide/active-directory-ops-img10.png)

우선 순위에 따라 아래 표를 사용 하 여 조직의 요구 사항에 가장 부합 하는 권장 솔루션을 찾습니다.

| **우선 순위** | **시나리오** | **권장** |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| 1 | PHS 또는 PTA를 사용 하 고 명명 된 위치가 정의 되지 않은 경우 | 위험 이벤트 검색을 향상 시키기 위해 명명 된 위치 정의 |
| 2 | 페더레이션 하 고 "insideCorporateNetwork" 클레임을 사용 하지 않고 명명 된 위치가 정의 되지 않은 경우 | 위험 이벤트 검색을 향상 시키기 위해 명명 된 위치 정의 |
| 3 | 조건부 액세스 정책에서 명명 된 위치를 사용 하지 않고 조건부 액세스 정책에 위험 또는 장치 컨트롤이 없는 경우 | 명명 된 위치를 포함 하도록 조건부 액세스 정책 구성 |
| 4 | 페더레이션 하 고 "insideCorporateNetwork" 클레임을 사용 하 고 명명 된 위치가 정의 되지 않은 경우 | 위험 이벤트 검색을 향상 시키기 위해 명명 된 위치 정의 |
| 5 | 명명 된 위치가 아닌 MFA를 사용 하 여 신뢰할 수 있는 IP 주소를 사용 하 고 신뢰할 수 있는 IP 주소를 표시 하는 경우 | 명명 된 위치를 정의 하 고 신뢰할 수 있는 것으로 표시 하 여 위험 이벤트 검색을 향상 시킵니다. |

### <a name="risk-based-access-policies"></a>위험 기반 액세스 정책

Azure AD는 모든 로그인 및 모든 사용자에 대 한 위험을 계산할 수 있습니다. 액세스 정책에서 위험을 조건으로 사용 하는 것은 더 나은 사용자 환경을 제공할 수 있습니다. 예를 들어 인증 프롬프트의 수를 줄이고 보안을 향상 시킬 수 있습니다. 예를 들어 필요한 경우에만 사용자에 게 메시지를 표시 하 고 응답 및 수정을 자동화할 수 있습니다.

![로그인 위험 정책](./media/active-directory-ops-guide/active-directory-ops-img11.png)

액세스 정책에서 위험 사용을 지 원하는 Azure AD Premium P2 라이선스를 이미 소유 하 고 있지만 사용 되지 않는 경우 보안 상태에 위험을 추가 하는 것이 좋습니다.

#### <a name="risk-based-access-policies-recommended-reading"></a>위험 기반 액세스 정책 권장 읽기

- [방법: 로그인 위험 정책 구성](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-sign-in-risk-policy)
- [방법: 사용자 위험 정책 구성](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)

### <a name="client-application-access-policies"></a>클라이언트 응용 프로그램 액세스 정책

MAM (Microsoft Intune 응용 프로그램 관리)은 저장소 암호화, PIN, 원격 저장소 정리 등의 데이터 보호 컨트롤을 호환 되는 클라이언트 모바일 응용 프로그램 (예: Outlook Mobile)에 푸시할 수 있는 기능을 제공 합니다. 또한 승인 된 앱 또는 호환 되는 앱에서 Exchange Online과 같은 클라우드 서비스에 대 한 [액세스를 제한](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) 하는 조건부 액세스 정책을 만들 수 있습니다.

직원이 Exchange Online 또는 SharePoint Online과 같은 회사 리소스에 액세스 하기 위해 Office mobile apps와 같은 MAM 지원 응용 프로그램을 설치 하 고 BYOD (사용자 고유의 장치를 가져오는)도 지 원하는 경우 관리 하려면 응용 프로그램 MAM 정책을 배포 하는 것이 좋습니다. 개인 소유의 장치에서 MDM 등록 없이 응용 프로그램을 구성 하 고, MAM 지원 클라이언트 에서만 액세스할 수 있도록 조건부 액세스 정책을 업데이트 합니다.

![조건부 액세스 권한 부여 컨트롤](./media/active-directory-ops-guide/active-directory-ops-img12.png)

직원이 회사 리소스에 대해 MAM 지원 응용 프로그램을 설치 하 고 Intune 관리 장치에 대 한 액세스가 제한 된 경우, 개인 장치에 대 한 응용 프로그램 구성을 관리 하기 위해 응용 프로그램 MAM 정책을 배포 하는 것을 고려해 야 합니다. MAM 지원 클라이언트 에서만 액세스할 수 있도록 조건부 액세스 정책을 업데이트 합니다.

### <a name="conditional-access-implementation"></a>조건부 액세스 구현

조건부 액세스는 조직의 보안 상태를 개선 하기 위한 필수 도구입니다. 따라서 다음 모범 사례를 따르는 것이 중요 합니다.

- 모든 SaaS 응용 프로그램에 하나 이상의 정책이 적용 되어 있는지 확인 합니다.
- 잠금 위험을 방지 하려면 **모든 앱** 필터를 **블록** 컨트롤과 결합 하지 마십시오.
- **모든 사용자** 를 필터로 사용 하지 않고 실수로 **게스트** 를 추가 합니다.
- **모든 "레거시" 정책을 Azure Portal로 마이그레이션**
- 사용자, 장치 및 응용 프로그램에 대 한 모든 조건 Catch
- 조건부 액세스 정책을 사용 하 여 **사용자 단위 mfa** 를 사용 하는 대신 [mfa 구현](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access)
- 여러 응용 프로그램에 적용할 수 있는 몇 가지 핵심 정책 집합이 있어야 합니다.
- 빈 예외 그룹을 정의 하 고 정책에 추가 하 여 예외 전략을 포함 합니다.
- MFA 컨트롤이 없는 [브레이크 유리](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure#break-glass-what-to-do-in-an-emergency) 계정 계획
- Office 365 클라이언트 응용 프로그램 (예: 팀, OneDrive for Business, Outlook 등)에서 일관 된 환경을 보장 합니다. Exchange Online, Sharepoint Online 등의 서비스에 대해 동일한 컨트롤 집합을 구현 하 여
- 정책에 대 한 할당은 개인이 아닌 그룹을 통해 구현 되어야 합니다.
- 정책에 사용 되는 예외 그룹을 정기적으로 검토 하 여 사용자가 보안 상태를 벗어나는 시간을 제한 합니다. Azure AD P2를 소유 하는 경우 액세스 검토를 사용 하 여 프로세스를 자동화할 수 있습니다.

#### <a name="conditional-access-recommended-reading"></a>조건부 액세스 권장 읽기

- [Azure Active Directory의 조건부 액세스에 대 한 모범 사례](https://docs.microsoft.com/azure/active-directory/conditional-access/best-practices)
- [ID 및 디바이스 액세스 구성](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Azure Active Directory 조건부 액세스 설정 참조](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference)
- [일반적인 조건부 액세스 정책](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common)

## <a name="access-surface-area"></a>액세스 노출 영역

### <a name="legacy-authentication"></a>레거시 인증

MFA와 같은 강력한 자격 증명은 레거시 인증 프로토콜을 사용 하 여 앱을 보호할 수 없으므로 악의적인 행위자가 선호 하는 공격 벡터를 만들 수 있습니다. 액세스 보안 상태를 향상 시키려면 레거시 인증을 잠그는 것이 중요 합니다.

레거시 인증은 다음과 같은 앱에서 사용 하는 인증 프로토콜을 지칭 하는 용어입니다.

- 최신 인증을 사용 하지 않는 이전 Office 클라이언트 (예: Office 2010 클라이언트)
- IMAP/SMTP/POP와 같은 메일 프로토콜을 사용 하는 클라이언트

공격자는 이러한 프로토콜을 매우 선호 합니다. 실제로 [암호 스프레이 공격의 거의 100%는](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984) 레거시 인증 프로토콜을 사용 합니다. 해커가 레거시 인증 프로토콜을 사용 하는 것은 multi-factor authentication 및 장치 인증과 같은 추가 보안 문제에 필요한 대화형 로그인을 지원 하지 않기 때문입니다.

레거시 인증이 환경에서 널리 사용 되는 경우 가능한 한 빨리 [최신 인증](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) 을 지 원하는 클라이언트로 레거시 클라이언트를 마이그레이션할 계획을 세워야 합니다. 동일한 토큰에서 기존 인증을 사용 하는 사용자가 이미 있지만 레거시 인증을 사용 하는 사용자가 있는 경우 다음 단계를 수행 하 여 레거시 인증 클라이언트를 잠가야 합니다.

1. [로그인 활동 보고서](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) 를 사용 하 여 레거시 인증 및 계획 수정을 계속 사용 하는 사용자를 식별 합니다.

   a. 영향을 받는 사용자에 게 최신 인증 지원 클라이언트를 업그레이드 합니다.
   
   b. 아래 단계에 따라 잠글 일정을 계획 합니다.
   
   다. 레거시 인증에 대 한 하드 종속성이 있는 레거시 응용 프로그램을 식별 합니다. 아래 3 단계를 참조 하세요.

2. 레거시 인증을 사용 하지 않는 사용자가 더 많은 노출을 방지 하기 위해 원본 (예: Exchange 사서함)에서 레거시 프로토콜을 사용 하지 않도록 설정 합니다.
3. 나머지 계정 (서비스 계정 등의 비 사용자 id)의 경우 조건부 액세스를 사용 하 여 인증 후 [레거시 프로토콜을 제한](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Conditional-Access-support-for-blocking-legacy-auth-is/ba-p/245417) 합니다.

#### <a name="legacy-authentication-recommended-reading"></a>레거시 인증 권장 읽기

- [Exchange Server의 사서함에 POP3 또는 IMAP4 액세스를 사용 하거나 사용 하지 않도록 설정](https://docs.microsoft.com/exchange/clients/pop3-and-imap4/configure-mailbox-access?view=exchserver-2019)

### <a name="consent-grants"></a>승인 허가

불법 승인 부여 공격에서 공격자는 연락처 정보, 전자 메일 또는 문서와 같은 데이터에 대 한 액세스를 요청 하는 Azure AD 등록 응용 프로그램을 만듭니다. 악의적인 웹 사이트를 방문 하는 경우 사용자는 피싱 공격을 통해 악의적인 응용 프로그램에 대 한 동의를 허용할 수 있습니다.

다음은 Microsoft 클라우드 서비스에 대해 확인할 수 있는 권한이 있는 앱 목록입니다.

- 앱 또는 위임 된 \*ReadWrite 권한
- 위임 된 권한이 있는 앱은 사용자 대신 전자 메일을 읽거나, 보내거나, 관리할 수 있습니다.
- 다음 사용 권한을 부여 받은 앱:

| 리소스 | 사용 권한 |
| :- | :- |
| Office 365 Exchange Online | 그런. AccessAsUser. 모두 |
| | EWS. AccessAsUser. 모두 |
| | 메일. 읽기 |
| Microsoft Graph | 메일. 읽기 |
| | Mail. 읽기. 공유 |
| | Mail. ReadWrite |

- 앱은 로그인 한 사용자의 전체 사용자 가장을 부여 합니다. 예:

|리소스 | 사용 권한 |
| :- | :- |
| Azure AD 그래프 | Directory.AccessAsUser.All |
| Microsoft Graph | Directory.AccessAsUser.All |
| Azure REST API | user_impersonation |

이 시나리오를 방지 하기 위해 [Office 365의 불법 승인 허용을 검색](https://docs.microsoft.com/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) 하 고 수정 하 여 필요한 것 보다 더 많은 권한이 부여 된 응용 프로그램 또는 응용 프로그램을 식별 하 고 수정 하는 것을 참조 해야 합니다. 그런 다음 [셀프 서비스를 완전히 제거](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-user-consent) 하 고 [거 버 넌 스 절차를 설정](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow)합니다. 마지막으로, 앱 사용 권한 정기 검토를 예약 하 고 필요 하지 않은 경우 제거 합니다.

#### <a name="consent-grants-recommended-reading"></a>동의 부여 권장 읽기

- [Microsoft Graph 사용 권한](https://docs.microsoft.com/graph/permissions-reference)

### <a name="user-and-group-settings"></a>사용자 및 그룹 설정

다음은 명시적인 비즈니스 요구 사항이 없는 경우 잠글 수 있는 사용자 및 그룹 설정입니다.

#### <a name="user-settings"></a>사용자 설정

- **외부 사용자** -외부 공동 작업은 팀, Power BI, Sharepoint Online 및 Azure Information Protection와 같은 서비스를 통해 엔터프라이즈에서 유기적으 발생할 수 있습니다. 사용자가 시작한 외부 공동 작업을 제어 하기 위한 명시적인 제약 조건이 있는 경우 [AZURE AD 자격 관리](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) 또는 지원 센터 등의 제어 된 작업을 사용 하 여 외부 사용자를 사용 하도록 설정 하는 것이 좋습니다. 서비스에 대 한 유기적 외부 공동 작업을 허용 하지 않으려면 [구성원이 외부 사용자를 완전히 초대](https://docs.microsoft.com/azure/active-directory/b2b/delegate-invitations)하지 못하도록 차단할 수 있습니다. 또는 외부 사용자 초대의 [특정 도메인을 허용 하거나 차단할](https://docs.microsoft.com/azure/active-directory/b2b/allow-deny-list) 수도 있습니다.
- **앱 등록** -앱 등록 사용 하도록 설정 하면 최종 사용자가 응용 프로그램 자체를 등록 하 고 해당 데이터에 대 한 액세스 권한을 부여할 수 있습니다. 앱 등록의 일반적인 예는 Outlook 플러그 인을 사용 하도록 설정 하는 사용자 또는 Alexa 및 Siri와 같은 음성 도우미가 전자 메일 및 일정을 읽거나 사용자를 대신 하 여 전자 메일을 보내는 것입니다. 고객이 앱 등록을 해제 하기로 결정 한 경우 관리자 계정으로 응용 프로그램을 등록 해야 하므로 예외 (비즈니스 요구 사항에 따라 필요한 앱 등록) 관리에 InfoSec 및 IAM 팀이 참여 해야 합니다. 그리고 대부분 프로세스를 운영 프로세스를 디자인 해야 할 가능성이 높습니다.
- **관리 포털** -조직에서 Azure Portal의 azure ad 블레이드를 잠가 관리자가 아닌 관리자가 Azure Portal에서 azure ad 관리에 액세스할 수 없어 혼동을 받을 수 있습니다. Azure AD 관리 포털의 사용자 설정으로 이동 하 여 액세스를 제한 합니다.

![관리 포털 제한 된 액세스](./media/active-directory-ops-guide/active-directory-ops-img13.png)

> [!NOTE]
> 비 관리자는 명령줄 및 기타 프로그래밍 인터페이스를 통해 Azure AD 관리 인터페이스에 계속 액세스할 수 있습니다.

#### <a name="group-settings"></a>그룹 설정

**셀프 서비스 그룹 관리/사용자는 보안 그룹/O365 그룹을 만들 수 있습니다.** 클라우드의 그룹에 대 한 현재 셀프 서비스 이니셔티브가 없는 경우 고객은이 기능을 사용할 준비가 될 때까지 해제 하도록 결정할 수 있습니다.

#### <a name="groups-recommended-reading"></a>그룹 권장 읽기

- [Azure Active Directory B2B 공동 작업은 무엇 인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
- [Azure Active Directory와 애플리케이션 통합](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)
- [Azure Active Directory에서 앱, 사용 권한 및 동의 합니다.](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)
- [그룹을 사용 하 여 Azure Active Directory 리소스에 대 한 액세스 관리](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)
- [Azure Active Directory에서 셀프 서비스 응용 프로그램 액세스 관리 설정](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)

### <a name="traffic-from-unexpected-locations"></a>예기치 않은 위치에서의 트래픽

공격자는 전 세계의 다양 한 부분에서 시작 됩니다. 위치를 조건으로 사용 하 여 조건부 액세스 정책을 사용 하 여이 위험을 관리 합니다. 조건부 액세스 정책의 [위치 조건을](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition) 사용 하 여에서 로그인 할 비즈니스 이유가 없는 위치에 대 한 액세스를 차단할 수 있습니다.

![새 명명 된 위치 만들기](./media/active-directory-ops-guide/active-directory-ops-img14.png)

사용할 수 있는 경우 SIEM (보안 정보 및 이벤트 관리) 솔루션을 사용 하 여 지역 간 액세스 패턴을 분석 하 고 찾습니다. SIEM 제품을 사용 하지 않거나 Azure AD에서 인증 정보를 수집 않는 경우 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) 를 사용 하 여 지역 간 액세스 패턴을 식별 하는 것이 좋습니다.

## <a name="access-usage"></a>액세스 사용

### <a name="azure-ad-logs-archived-and-integrated-with-incident-response-plans"></a>인시던트 대응 계획에 보관 및 통합 된 Azure AD 로그

로그인 활동에 대 한 액세스 권한이 있는 Azure AD에 대 한 감사 및 위험 이벤트는 문제 해결, 사용 분석 및 등록 분석에 중요 합니다. Azure AD는 보존 기간이 제한 된 REST Api를 통해 이러한 소스에 대 한 액세스를 제공 합니다. SIEM (보안 정보 및 이벤트 관리) 시스템 또는 이와 동등한 보관 기술은 감사 및 지원 가능성의 장기 저장소에 대 한 키입니다. Azure AD 로그의 장기 저장소를 사용 하도록 설정 하려면 기존 SIEM 솔루션에 해당 로그를 추가 하거나 [Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor)를 사용 해야 합니다. 인시던트 대응 계획 및 조사의 일부로 사용할 수 있는 보관 로그

#### <a name="logs-recommended-reading"></a>로그 권장 읽기

- [Azure Active Directory 감사 API 참조](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference)
- [Azure Active Directory 로그인 활동 보고서 API 참조](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-sign-in-activity-reference)
- [인증서와 함께 Azure AD Reporting API를 사용하여 데이터 가져오기](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-with-certificates)
- [Azure Active Directory Identity Protection에 대 한 Microsoft Graph](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)
- [Office 365 관리 활동 API 참조](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference)
- [Azure Active Directory Power BI 콘텐츠 팩을 사용 하는 방법](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

## <a name="summary"></a>요약

보안 Id 인프라에는 12 가지 측면이 있습니다. 이 목록을 통해 자격 증명을 보다 안전 하 게 관리 하 고, 인증 환경을 정의 하 고, 할당을 위임 하 고, 사용을 측정 하 고, 엔터프라이즈 보안 상태에 따라 액세스 정책을 정의할 수 있습니다.

- 키 작업에 소유자를 할당 합니다.
- 취약 하거나 누출 한 암호를 감지 하 고, 암호 관리 및 보호를 개선 하 고, 리소스에 대 한 사용자 액세스를 강화 하는 솔루션을 구현 합니다.
- 언제 든 지 모든 위치에서 리소스를 보호 하기 위해 장치 id를 관리 합니다.
- 암호 없는 인증을 구현 합니다.
- 조직 전체에서 표준화 된 Single Sign-On 메커니즘을 제공 합니다.
- 응용 프로그램을 AD FS에서 Azure AD로 마이그레이션하여 더 나은 보안과 일관 된 관리 효율성을 지원 합니다.
- 그룹을 사용 하 여 응용 프로그램에 사용자를 할당 하 여 더 유연 하 고 대규모로 관리할 수 있는 기능을 제공 합니다.
- 위험 기반 액세스 정책을 구성 합니다.
- 레거시 인증 프로토콜을 잠급니다.
- 불법 승인 부여를 검색 하 고 재구성 합니다.
- 사용자 및 그룹 설정을 잠급니다.
- 문제 해결, 사용 분석 및 법적 고 지 조사를 위해 Azure AD 로그의 장기 저장소를 사용 하도록 설정 합니다.

## <a name="next-steps"></a>다음 단계

[Id 거 버 넌 스 작업 검사 및 작업](active-directory-ops-guide-govern.md)을 시작 합니다.
