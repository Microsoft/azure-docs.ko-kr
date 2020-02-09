---
title: 강력한 identity foundation에 대 한 4 단계-Azure AD
description: 이 항목에서는 고객이 강력한 id 기반을 구축 하기 위해 수행할 수 있는 하이브리드 id 4 단계에 대해 설명 합니다.
services: active-directory
author: martincoetzer
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2019
ms.subservice: hybrid
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3eb98f543e17981be0d5b9ab08fa4e146659b47
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74206790"
---
# <a name="four-steps-to-a-strong-identity-foundation-with-azure-active-directory"></a>Azure Active Directory를 사용 하는 강력한 identity foundation에 대 한 4 단계

앱 및 데이터에 대 한 액세스를 관리 하는 것은 응용 프로그램을 클라우드로 빠르게 이동 하기 때문에 경계 네트워크 및 방화벽과 같은 기존의 네트워크 보안 경계 전략에 더 이상 의존 하지 않습니다. 이제 조직에서 id 솔루션을 신뢰 하 여 조직의 앱 및 데이터에 대 한 액세스 권한이 있는 사용자를 제어 해야 합니다. 조직에서 직원 들이 자신의 장치를 회사로 가져와서 인터넷에 연결할 수 있는 어디에서 나 장치를 사용할 수 있도록 합니다. 이러한 장치를 준수 하 고 안전 하 게 유지 하는 것은 조직에서 구현 하도록 선택한 id 솔루션에서 중요 한 고려 사항이 됩니다. 오늘날의 디지털 작업 공간에서 id는 클라우드로 이동 하는 모든 조직의 [기본 제어 평면](https://www.microsoft.com/security/technology/identity-access-management?rtc=1) 입니다.

조직에서는 Azure Active Directory (Azure AD) 하이브리드 id 솔루션을 채택 하 여 자동화, 위임, 셀프 서비스 및 Single Sign-On 기능을 통해 생산성을 해제 하는 프리미엄 기능에 액세스할 수 있습니다. 이를 통해 작업자는 자신의 작업을 수행 해야 하는 모든 위치에서 회사 리소스에 액세스할 수 있으며, 적절 한 사용자가 안전한 생산성을 설정 하는 적절 한 리소스에 적절 하 게 액세스할 수 있도록 하 여 IT 팀이 해당 액세스를 제어할 수 있습니다.

학습을 기반으로 하는이 모범 사례 검사 목록은 조직에서 *강력한* id 기반을 구축 하기 위한 권장 작업을 신속 하 게 배포 하는 데 도움이 됩니다.

* 앱에 쉽게 연결
* 모든 사용자에 대 한 id를 자동으로 설정
* 안전 하 게 사용자의 역량 강화
* 통찰력 운영

## <a name="step-1---connect-to-apps-easily"></a>1 단계-앱에 쉽게 연결

앱을 Azure AD와 연결 하면 SSO (Single Sign-On)를 사용 하도록 설정 하 고 사용자 프로 비전을 수행 하 여 최종 사용자 생산성과 보안을 향상 시킬 수 있습니다. Azure AD의 단일 위치에서 앱을 관리 하 여 관리 오버 헤드를 최소화 하 고 보안 및 규정 준수 정책을 위한 단일 제어 지점을 달성할 수 있습니다.

이 섹션에서는 앱에 대 한 사용자 액세스를 관리 하 고, 내부 앱에 대 한 보안 원격 액세스를 설정 하 고, 앱을 Azure AD로 마이그레이션하는 이점에 대 한 옵션을 설명 합니다.

### <a name="make-apps-available-to-your-users-seamlessly"></a>사용자가 앱을 원활 하 게 사용할 수 있도록 설정

관리자는 Azure AD를 사용 하 여 [Azure Portal](https://portal.azure.com/)의 엔터프라이즈 응용 프로그램 갤러리에 [응용 프로그램을 추가할](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal) 수 있습니다. 엔터프라이즈 응용 프로그램 갤러리에 응용 프로그램을 추가 하면 id 공급자로 Azure AD를 사용 하도록 응용 프로그램을 쉽게 구성할 수 있습니다. 또한 조건부 액세스 정책을 사용 하 여 응용 프로그램에 대 한 사용자 액세스를 관리 하 고 응용 프로그램에 대 한 SSO (Single Sign-On)를 구성 하 여 사용자가 암호를 반복적으로 입력 하 고 온-프레미스에 자동으로 로그인 할 필요가 없도록 할 수 있습니다. 클라우드 기반 응용 프로그램.

응용 프로그램이 Azure AD 갤러리에 추가 되 면 사용자는 할당 된 앱을 보고 필요에 따라 다른 앱을 검색 및 요청할 수 있습니다. Azure AD는 사용자가 앱에 액세스할 수 있는 [여러 가지 방법을](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences) 제공 합니다.

* 액세스 패널/내 앱
* Office 365 앱 시작 관리자
* 페더레이션된 앱에 직접 로그온
* 직접 로그온 링크

앱에 대 한 사용자 액세스에 대 한 자세한 내용은이 문서에서 **3 단계--사용자에** 게 권한 부여를 참조 하세요.

### <a name="migrate-apps-from-active-directory-federation-services-to-azure-ad"></a>Active Directory Federation Services에서 Azure AD로 앱 마이그레이션

Active Directory Federation Services (ADFS)에서 Azure AD로 Single Sign-On 구성을 마이그레이션하면 보안의 추가 기능, 보다 일관 된 관리 효율성 및 공동 작업을 수행할 수 있습니다. 최적의 결과를 얻으려면 앱을 AD FS에서 Azure AD로 마이그레이션하는 것이 좋습니다. 응용 프로그램 인증 및 권한 부여를 Azure AD로 가져오면 다음과 같은 이점이 제공 됩니다.

* 비용 관리
* 위험 관리
* 생산성 향상
* 규정 준수 및 거 버 넌 스

자세히 알아보려면 [Azure Active Directory로 응용 프로그램 마이그레이션](https://aka.ms/migrateapps/whitepaper) 백서를 참조 하세요.

### <a name="enable-secure-remote-access-to-apps"></a>앱에 대 한 보안 원격 액세스 사용

[Azure AD 응용 프로그램 프록시](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy) 는 조직이 안전한 방식으로 내부 앱에 액세스 해야 하는 원격 사용자를 위해 온-프레미스 앱을 클라우드에 게시할 수 있는 간단한 솔루션을 제공 합니다. Azure AD에 Single Sign-On 후 사용자는 외부 Url 또는 내부 응용 프로그램 포털을 통해 클라우드 및 온-프레미스 응용 프로그램 모두에 액세스할 수 있습니다.

Azure AD 응용 프로그램 프록시는 다음과 같은 이점을 제공 합니다.

* Azure AD를 온-프레미스 리소스로 확장
  * 클라우드 규모 보안 및 보호
  * 쉽게 사용할 수 있는 조건부 액세스 및 Multi-Factor Authentication 같은 기능
* 경계 네트워크에 VPN 및 기존의 역방향 프록시 솔루션과 같은 구성 요소가 없습니다.
* 필요한 인바운드 연결 없음
* 클라우드 및 온-프레미스에서 장치, 리소스 및 앱에 대해 SSO (Single sign-on)
* 언제 어디서 나 최종 사용자의 생산성을 높일 수 있습니다.

### <a name="discover-shadow-it-with-microsoft-cloud-app-security"></a>Microsoft Cloud App Security를 사용 하 여 섀도 IT 검색

오늘날 기업에서 IT 부서는 사용자가 작업을 수행 하는 데 사용 하는 모든 클라우드 응용 프로그램을 인식 하지 못하는 경우가 많습니다. IT 관리자에 게 직원 들이 사용 하는 것으로 의심 되는 클라우드 앱의 수를 묻는 메시지가 표시 되 면 평균적으로 30 또는 40입니다. 실제로 평균은 조직의 직원이 사용 하는 1000 개 이상의 개별 앱입니다. 직원 중 80%는 아직 검토 하지 않은 사용 권한 앱을 사용 하 고 보안 및 규정 준수 정책을 준수 하지 않을 수 있습니다.

Mcas ( [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) )를 사용 하면 사용자가 SSO 및 조건부 액세스와 같은 기능을 활용할 수 있도록 사용 권한 부여 하 고 엔터프라이즈 응용 프로그램 갤러리에 추가할 수 있는 사용자에 게 널리 사용 되는 유용한 앱을 식별할 수 있습니다.

<em>"**Cloud App Security** Accenture를 보호 하는 데 도움이 되는 기본 보안 정책을 지 원하는 방식으로 사용자가 클라우드 및 SaaS 응용 프로그램을 제대로 사용 하 고 있는지 확인 하는 데 도움이 됩니다."</em> --- [John Blasi, Director 관리, 정보 보안, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

섀도 IT를 검색 하는 것 외에도 MCAS는 앱의 위험 수준을 결정 하 고, 회사 데이터에 대 한 무단 액세스, 가능한 데이터 유출 및 기타 보안 위험을 응용 프로그램에 내재 할 수 있습니다.

## <a name="step-2---establish-one-identity-for-every-user-automatically"></a>2 단계-모든 사용자에 대 한 id를 자동으로 설정

Azure AD 하이브리드 id 솔루션에서 온-프레미스 및 클라우드 기반 디렉터리를 함께 사용 하면 기존 온-프레미스의 기존 id를 클라우드에서 프로 비전 하 여 기존 온-프레미스 Active Directory 투자를 재사용할 수 있습니다. 이 솔루션은 온-프레미스 id를 Azure AD와 동기화 하는 반면, 기존 거 버 넌 스 솔루션을 사용 하 여 온-프레미스 Active Directory을 id의 주요 원인으로 유지 합니다. Microsoft의 Azure AD 하이브리드 id 솔루션은 온-프레미스 및 클라우드 기반 기능을 제공 하 여 위치에 관계 없이 모든 리소스에 대 한 인증 및 권한 부여에 대 한 공통 사용자 id를 만듭니다.

온-프레미스 디렉터리를 Azure AD와 통합 하면 사용자가 생산성을 높일 수 있으며, 클라우드 및 온-프레미스 리소스 모두에 액세스할 수 있는 공통 id를 제공 하 여 사용자가 앱과 서비스에서 여러 계정을 사용할 수 없습니다. 여러 계정을 사용 하는 것은 최종 사용자와 동일 합니다. 최종 사용자 관점에서 여러 계정이 있는 경우 여러 암호를 기억할 필요가 있습니다. 이를 방지 하기 위해 많은 사용자는 각 계정에 대해 동일한 암호를 다시 사용 합니다 .이는 보안 관점에서 잘못 된 것입니다. IT 관점에서 다시 사용 하는 경우에는 최종 사용자 불만에 따라 더 많은 암호 재설정 및 기술 지원팀 비용이 자주 발생 합니다.

Azure AD Connect은 온-프레미스 id를 Azure AD에 동기화 하는 데 사용 되는 도구입니다 .이 도구를 사용 하 여 클라우드 응용 프로그램에 액세스할 수 있습니다. Id가 Azure AD에 있으면 Salesforce 또는 Concur와 같은 SaaS 응용 프로그램에 프로 비전 할 수 있습니다.

이 섹션에서는 고가용성, 클라우드에 대 한 최신 인증을 제공 하 고 온-프레미스 공간을 줄이는 데 필요한 권장 사항을 나열 합니다.

> [!NOTE]
> Azure AD Connect에 대해 자세히 알아보려면 [Azure AD Connect 동기화 란?](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) 을 참조 하세요.

### <a name="set-up-a-staging-server-for-azure-ad-connect-and-keep-it-up-to-date"></a>Azure AD Connect 준비 서버를 설정 하 고 최신 상태로 유지

Azure AD Connect는 프로 비전 프로세스에서 주요 역할을 수행 합니다. 어떤 이유로 든 동기화 서버가 오프 라인 상태가 되 면 온-프레미스에 대 한 변경 내용이 클라우드에서 업데이트 되지 않고 사용자에 대 한 액세스 문제가 발생 합니다. 동기화 서버가 오프 라인 상태가 된 후 관리자가 신속 하 게 동기화를 다시 시작할 수 있도록 하는 장애 조치 전략을 정의 하는 것이 중요 합니다.

기본 Azure AD Connect 서버가 오프 라인 상태가 되는 이벤트의 고가용성을 제공 하려면 Azure AD Connect에 대 한 별도의 [준비 서버](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-staging-server) 를 배포 하는 것이 좋습니다. 서버를 배포 하면 관리자가 간단한 구성 스위치를 사용 하 여 스테이징 서버를 프로덕션으로 "승격할" 수 있습니다. 또한 준비 모드에서 구성 된 대기 서버를 사용 하면 새 구성 변경을 테스트 및 배포 하 고, 기존 구성 변경을 서비스 해제할 경우 새 서버를 도입할 수 있습니다.

> [!TIP]
> Azure AD Connect는 정기적으로 업데이트 됩니다. 따라서 새 버전에서 제공 하는 성능 향상, 버그 수정 및 새로운 기능을 활용 하기 위해 준비 서버를 최신 상태로 유지 하는 것이 좋습니다.

### <a name="enable-cloud-authentication"></a>클라우드 인증 사용

온-프레미스 Active Directory 있는 조직은 Azure AD Connect를 사용 하 여 해당 디렉터리를 Azure AD로 확장 하 고 적절 한 인증 방법을 구성 해야 합니다. 조직에 대 한 [올바른 인증 방법을 선택](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn) 하는 것은 앱을 클라우드로 이동 하는 첫 번째 단계입니다. 모든 클라우드 데이터 및 리소스에 대 한 액세스를 제어 하기 때문에 중요 한 구성 요소입니다.

Azure AD에서 온-프레미스 디렉터리 개체에 대 한 클라우드 인증을 사용 하는 가장 간단 하 고 권장 되는 방법은 phs ( [암호 해시 동기화](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) )를 사용 하도록 설정 하는 것입니다. 또는 일부 조직에서는 pta ( [통과 인증](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) )를 사용 하도록 고려할 수 있습니다.

PHS 또는 PTA를 선택 하는지 여부에 상관 없이, [원활한 Single sign-on](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso) 을 사용 하도록 설정 하 여 사용자가 회사 네트워크에서 Windows 7 및 8 장치를 사용 하는 경우 앱에 사용자 이름과 암호를 지속적으로 입력 하지 않고도 클라우드 앱에 액세스할 수 있도록 해야 합니다. Single Sign-On 하지 않으면 사용자는 응용 프로그램 관련 암호를 기억할 수 있으며 각 응용 프로그램에 로그인 해야 합니다. 마찬가지로, IT 직원은 Office 365, Box, Salesforce 등의 각 응용 프로그램에 대 한 사용자 계정을 만들고 업데이트 해야 합니다. 사용자는 암호를 기억할 뿐만 아니라 각 응용 프로그램에 로그인 하는 데 걸리는 시간을 소비 해야 합니다. 전체 기업에 표준화 된 Single Sign-On 메커니즘을 제공 하는 것은 최상의 사용자 환경, 위험 감소, 보고 기능 및 거 버 넌 스에 매우 중요 합니다.

이미 AD FS 또는 다른 온-프레미스 인증 공급자를 사용 하는 조직의 경우 id 공급자로 Azure AD로 이동 하면 복잡성을 줄이고 가용성을 향상 시킬 수 있습니다. 페더레이션을 사용 하는 특정 사용 사례가 있는 경우를 제외 하 고는 페더레이션 인증에서 PHS와 원활한 SSO 또는 PTA와 원활한 SSO로 마이그레이션하는 것이 좋습니다. 사용자 환경을 개선 했습니다. 자세한 내용은 [페더레이션에서 Azure Active Directory에 대 한 암호 해시 동기화로 마이그레이션](https://docs.microsoft.com/azure/active-directory/hybrid/plan-migrate-adfs-password-hash-sync)을 참조 하세요.

### <a name="enable-automatic-deprovisioning-of-accounts"></a>계정 자동 프로 비전 해제 사용

응용 프로그램에 대 한 자동화 된 프로 비전 및 프로 비전 해제를 사용 하도록 설정 하는 것은 여러 시스템에서 id의 수명 주기를 관리 하는 Azure AD는 ServiceNow 및 Salesforce와 같은 널리 사용 되는 다양 한 SaaS 응용 프로그램에 대 한 사용자 계정의 [자동화 된 정책 기반 프로 비전 및 프로 비전](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal) 해제를 지원 하 고 [scim 2.0 프로토콜](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)을 구현 합니다. 사용자 지정 코드 또는 CSV 파일의 수동 업로드를 필요로 하는 기존 프로 비전 솔루션과 달리, 프로 비전 서비스는 클라우드에서 호스트 되 고 Azure Portal를 사용 하 여 설정 및 관리할 수 있는 사전 통합 된 커넥터 기능을 제공 합니다. 자동 프로 비전 해제의 주요 혜택은 조직에서 나갈 때 키 SaaS 앱에서 사용자 id를 즉시 제거 하 여 조직의 보안을 유지 하는 데 도움이 된다는 것입니다.

자동 사용자 계정 프로비전 및 작동 방식에 대한 자세한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션에 사용자 프로비전 및 프로비전 해제 자동화](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)를 참조하세요.

## <a name="step-3---empower-your-users-securely"></a>3 단계-안전 하 게 사용자에 게 권한 부여

오늘날의 디지털 작업 공간에서는 보안과 균형을 유지 하는 것이 중요 합니다. 그러나 최종 사용자는 생산성을 높이고 클라우드 앱에 액세스 하는 보안 조치를 다시 푸시하는 경우가 많습니다. 이를 해결 하기 위해 Azure AD는 관리 오버 헤드를 최소화 하면서 사용자가 생산성을 유지할 수 있도록 하는 셀프 서비스 기능을 제공 합니다.

이 섹션에서는 유의 남은 시간 동안 사용자에 게 역량을 부여 하 여 조직에서의 마찰을 제거 하기 위한 권장 사항을 나열 합니다.

### <a name="enable-self-service-password-reset-for-all-users"></a>모든 사용자에 대해 셀프 서비스 암호 재설정 사용

Azure의 SSPR ( [셀프 서비스 암호 재설정](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) )는 IT 관리자가 관리자 개입 없이 암호나 계정을 재설정 하 고 잠금을 해제할 수 있는 간단한 방법을 제공 합니다. 이 시스템에는 오용 또는 남용에 대해 경고하는 알림과 함께 사용자가 언제 시스템에 액세스하는지 추적하는 구체적인 보고서가 포함되어 있습니다.

기본적으로 Azure AD는 암호 재설정을 수행할 때 계정을 잠금 해제합니다. 그러나 [온-프레미스에서 Azure AD Connect 통합](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#on-premises-integration)을 사용 하도록 설정 하는 경우 사용자가 암호를 다시 설정 하지 않고도 계정의 잠금을 해제할 수 있도록 두 작업을 구분 하는 옵션도 있습니다.

### <a name="ensure-all-users-are-registered-for-mfa-and-sspr"></a>모든 사용자가 MFA 및 SSPR에 등록 되었는지 확인

Azure는 사용자와 조직에서 사용할 수 있는 보고서를 제공 하 여 사용자가 MFA 및 SSPR에 등록 되도록 합니다. 등록 하지 않은 사용자는 프로세스에 대해 교육을 받아야 할 수 있습니다.

MFA [로그인 보고서](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting) 에는 mfa 사용에 대 한 정보가 포함 되어 있으며, 조직에서 mfa가 작동 하는 방식에 대 한 통찰력을 제공 합니다. Azure AD에 대 한 로그인 활동에 대 한 액세스 권한 (및 감사 및 위험 검색)은 문제 해결, 사용 분석 및 법률 조사를 위해 중요 합니다.

마찬가지로, [셀프 서비스 암호 관리 보고서](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting) 를 사용 하 여 SSPR에 대해 등록 하거나 등록 하지 않은 사용자를 확인할 수 있습니다.

### <a name="self-service-app-management"></a>셀프 서비스 앱 관리

사용자가 액세스 패널에서 응용 프로그램을 자체 검색할 수 있으려면 먼저 사용자가 직접 검색 하 고 액세스를 요청할 수 있도록 하려는 모든 응용 프로그램에 대 한 [셀프 서비스 응용 프로그램 액세스](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-manage-self-service-access) 를 사용 하도록 설정 해야 합니다. 셀프 서비스 응용 프로그램 액세스는 사용자가 응용 프로그램을 직접 검색할 수 있도록 하 고 필요에 따라 비즈니스 그룹이 해당 응용 프로그램에 대 한 액세스를 승인할 수 있도록 하는 좋은 방법입니다. 비즈니스 그룹이 액세스 패널에서 바로 [암호 Single Sign-on 응용 프로그램](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery#configure-password-sso-for-an-azure-ad-gallery-app) 에 대해 해당 사용자에 게 할당 된 자격 증명을 관리 하도록 허용할 수 있습니다.

### <a name="self-service-group-management"></a>셀프 서비스 그룹 관리

그룹을 사용할 때 응용 프로그램에 사용자를 할당 하는 것이 가장 효율적입니다.

* 동적 그룹 멤버 자격을 사용한 특성 기반
* 앱 소유자에 게 위임

Azure AD는 보안 그룹 및 Office 365 그룹을 사용하여 리소스에 대한 액세스를 관리하는 기능을 제공합니다. 이러한 그룹은 멤버 자격 요청을 승인 또는 거부 하 고 그룹 멤버 자격에 대 한 제어를 위임할 수 있는 그룹 소유자가 관리할 수 있습니다. [셀프 서비스 그룹 관리](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management)라고 하는이 기능은 관리자를 사용 하 여 요청을 처리 하지 않고도 그룹을 만들고 관리할 수 있도록 관리 역할이 할당 되지 않은 그룹 소유자에 게 시간을 절약 합니다.

## <a name="step-4---operationalize-your-insights"></a>4 단계-통찰력 운영

보안 관련 이벤트 및 관련 경고의 감사 및 로깅은 사용자가 생산성을 유지 하 고 조직의 보안을 유지 하기 위한 효율적인 전략의 필수 구성 요소입니다. 보안 로그 및 보고서를 통해 다음과 같은 질문에 대답할 수 있습니다.

* 지불 하 고 있는 항목을 사용 하 고 있나요?
* 내 테넌트에 의심 스러운 상황이 나 악의적인 문제가 있나요?
* 보안 인시던트에 영향을 받는 사람은 누구 인가요?

보안 로그 및 보고서는 의심스러운 활동에 대한 전자 기록을 제공하며, 네트워크의 외부 침투와 내부 공격의 시도 또는 성공을 나타낼 수 있는 패턴을 검색할 수 있도록 도움을 줍니다. 감사를 사용 하 여 사용자 활동을 모니터링 하 고, 규정 준수를 문서화 하 고, 법정 분석을 수행할 수 있습니다. 경고는 보안 이벤트의 알림을 제공합니다.

### <a name="assign-least-privileged-admin-roles-for-operations"></a>작업에 대해 최소 권한 있는 관리자 역할 할당

작업에 대 한 접근 방식에 대해 생각해 볼 때 고려해 야 할 몇 가지 관리가 있습니다. 첫 번째 수준은 전역 관리자에 대 한 관리 부담을 부과 합니다. 항상 전역 관리자 역할을 사용 하는 것은 소규모 회사에 적합할 수 있습니다. 그러나 지원 센터 담당자 및 관리자가 특정 작업을 담당 하는 대규모 조직의 경우 전역 관리자 역할을 할당 하는 것은 이러한 개인에 게 위와 그 이상의 작업을 관리할 수 있는 기능을 제공 하기 때문에 보안상 위험할 수 있습니다. 이러한 작업을 수행할 수 있어야 합니다.

이 경우 다음 수준의 관리를 고려해 야 합니다. Azure AD를 사용 하면 권한이 낮은 역할의 작업을 관리할 수 있는 "제한 된 관리자"로 최종 사용자를 지정할 수 있습니다. 예를 들어 읽기 전용 액세스를 통해 보안 관련 기능을 관리할 수 있도록 지원 센터 담당자에 게 [보안 읽기 권한자](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#security-reader) 역할을 할당할 수 있습니다. 또는 [인증 관리자](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#authentication-administrator) 역할을 개인에 게 할당 하 여 암호를 사용 하지 않는 자격 증명을 다시 설정 하거나 Azure Service Health를 읽고 구성 하는 기능을 제공 하는 것이 좋습니다.

자세히 알아보려면 [Azure Active Directory의 관리자 역할 권한](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)을 참조 하세요.

### <a name="monitor-hybrid-components-azure-ad-connect-sync-ad-fs-using-azure-ad-connect-health"></a>Azure AD Connect Health를 사용 하 여 하이브리드 구성 요소 (Azure AD Connect 동기화, AD FS) 모니터링

Azure AD Connect 및 AD FS는 잠재적으로 수명 주기 관리 및 인증을 중단 하 고 궁극적으로 중단 될 수 있는 중요 한 구성 요소입니다. 따라서 이러한 구성 요소에 대 한 모니터링 및 보고를 위해 Azure AD Connect Health를 배포 해야 합니다.

자세히 알아보려면 [Azure AD Connect Health를 사용 하 여 AD FS 읽기 모니터](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)로 이동 하세요.

### <a name="use-azure-monitor-to-collect-data-logs-for-analytics"></a>Azure Monitor를 사용 하 여 분석을 위한 데이터 로그 수집

[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) 는 심층 통찰력, 고급 분석 및 스마트 기계 학습을 제공 하는 모든 Azure AD 로그에 대 한 통합 모니터링 포털입니다. Azure Monitor를 사용 하 여 포털 내에서 메트릭과 로그를 사용 하 고 Api를 통해 리소스의 상태와 성능을 보다 자세히 파악할 수 있습니다. 이를 통해 포털 내에서 단일 창을 투명 하 게 사용할 수 있으며 기존 타사 SIEM 시스템을 지 원하는 Api 및 데이터 내보내기 옵션을 통해 다양 한 제품 통합을 사용할 수 있습니다. 또한 Azure Monitor는 알림을 얻거나 리소스에 영향을 주는 문제에 대 한 자동화 된 작업을 수행 하도록 경고 규칙을 구성 하는 기능을 제공 합니다.

![Azure Monitor](./media/four-steps/image1.png)

### <a name="create-custom-dashboards-for-your-leadership-and-your-day-to-day"></a>리더십 및 일상 업무에 대 한 사용자 지정 대시보드 만들기

SIEM 솔루션이 없는 조직은 Azure AD 용 [Power BI 콘텐츠 팩](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-power-bi-content-pack) 을 다운로드할 수 있습니다. Power BI 콘텐츠 팩에는 사용자가 Azure AD 기능을 채택 하 고 사용 하는 방법을 이해 하는 데 도움이 되는 미리 작성 된 보고서가 포함 되어 있습니다 .이를 통해 디렉터리 내의 모든 활동에 대 한 정보를 얻을 수 있습니다 [사용자 고유의 사용자 지정 대시보드](https://docs.microsoft.com/power-bi/service-dashboards) 를 만들고 리더십 팀과 공유 하 여 일상적인 활동을 보고할 수도 있습니다. 대시보드는 비즈니스를 모니터링 하 고 가장 중요 한 메트릭을 한 눈에 볼 수 있는 훌륭한 방법입니다. 대시보드의 시각화는 하나의 기본 데이터 집합 또는 여러 원본 보고서에서 비롯 될 수 있습니다. 대시보드는 온-프레미스 및 클라우드 데이터를 결합 하 여 데이터의 위치에 관계 없이 통합 된 보기를 제공 합니다.

![사용자 지정 대시보드 Power BI](./media/four-steps/image2.png)

### <a name="understand-your-support-call-drivers"></a>지원 전화 드라이버 이해

이 문서에 설명 된 대로 하이브리드 id 솔루션을 구현 하는 경우에는 궁극적으로 지원 호출이 감소 하는 것을 알 수 있습니다. 잊어버린 암호 및 계정 잠금과 같은 일반적인 문제는 Azure의 셀프 서비스 암호 재설정을 구현 하 여 완화 되지만 셀프 서비스 응용 프로그램 액세스를 사용 하도록 설정 하면 사용자가 신뢰 없이 응용 프로그램에 대 한 액세스를 직접 검색 하 고 요청할 수 있습니다. IT 직원에 게 문의 하세요.

지원 호출의 감소를 관찰 하지 않으면 지원 통화 드라이버를 분석 하 여 SSPR 또는 셀프 서비스 응용 프로그램 액세스가 올바르게 구성 되었는지 또는 체계적으로 수행할 수 있는 다른 새로운 문제가 있는지 확인 하는 것이 좋습니다. 보고.

*"우리의 디지털 변환 과정에서는 효과적인 에코 시스템에 대해 미국, 파트너 및 클라우드 서비스 공급자 간의 원활한 보안 통합을 용이 하 게 하기 위해 신뢰할 수 있는 id 및 액세스 관리 공급자가 필요 했습니다. Azure AD는 위험을 감지 하 고이에 대응할 수 있도록 하는 필요한 기능 및 가시성을 제공 하는 가장 좋은 옵션입니다. "* --- [Yazan Almasri, Aramex, 글로벌 정보 보안 책임자,](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)

### <a name="monitor-your-usage-of-apps-to-drive-insights"></a>앱 사용을 모니터링 하 여 insights를 추진 합니다.

섀도 IT를 검색 하는 것 외에도 [Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) 를 사용 하 여 조직 전체의 앱 사용을 모니터링 하는 것은 클라우드 응용 프로그램의 약속을 최대한 활용 하기 위해 이동 하는 데 도움이 될 수 있습니다. 이를 통해 작업에 대 한 가시성을 개선 하 고 클라우드 응용 프로그램 전반에 걸쳐 중요 한 데이터의 보호를 향상 시켜 자산을 제어할 수 있습니다. MCAS를 사용 하 여 조직의 앱 사용 현황을 모니터링 하면 다음 질문에 대 한 답을 확인할 수 있습니다.

* 비 사용 권한 앱은 데이터를 저장 하는 데 사용 하는 직원은 무엇 인가요?
* 중요 한 데이터가 클라우드에 저장 되는 위치와 시기는 어디 인가요?
* 클라우드에서 중요 한 데이터에 액세스 하는 사람은 누구 인가요?

*"Cloud App Security를 사용 하면 변칙을 신속 하 게 파악 하 고 조치를 취할 수 있습니다."* --- [Eric LePenske, 수석 관리자, 정보 보안, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

## <a name="summary"></a>요약

하이브리드 Id 솔루션을 구현 하는 데는 많은 측면이 있지만이 4 단계 검사 목록은 사용자가 생산성을 높이고 안전 하 게 사용할 수 있도록 id 인프라를 신속 하 게 수행 하는 데 도움이 됩니다.

* 앱에 쉽게 연결
* 모든 사용자에 대 한 id를 자동으로 설정
* 안전 하 게 사용자의 역량 강화
* 통찰력 운영

이 문서는 조직에서 강력한 id 기반을 설정 하는 데 유용한 로드맵입니다.

## <a name="identity-checklist"></a>Id 검사 목록

조직에서 더 견고한 identity foundation으로의 전환을 시작 하는 경우 참조를 위해 다음 검사 목록을 출력 하는 것이 좋습니다.

### <a name="today"></a>오늘

|등록했나요?|항목|
|:-|:-|
||그룹에 대 한 파일럿 SSPR (셀프 서비스 암호 재설정)|
||Azure AD Connect Health를 사용 하 여 하이브리드 구성 요소 모니터링|
||작업에 최소한의 권한 있는 관리자 역할 할당|
||Microsoft Cloud App Security를 사용 하 여 섀도 IT 검색|
||Azure Monitor를 사용 하 여 분석을 위한 데이터 로그 수집|

### <a name="next-two-weeks"></a>다음 2 주

|등록했나요?|항목|
|:-|:-|
||사용자가 앱을 사용할 수 있도록 설정|
||선택한 SaaS 앱에 대 한 Azure AD 프로 비전 파일럿|
||Azure AD Connect 준비 서버를 설정 하 고 최신 상태로 유지|
||ADFS에서 Azure AD로 앱 마이그레이션 시작|
||리더십 및 일상 업무에 대 한 사용자 지정 대시보드 만들기|

### <a name="next-month"></a>다음 달

|등록했나요?|항목|
|:-|:-|
||앱 사용을 모니터링 하 여 insights를 추진 합니다.|
||앱에 대 한 보안 원격 액세스 파일럿|
||모든 사용자가 MFA 및 SSPR에 등록 되었는지 확인|
||클라우드 인증 사용|

### <a name="next-three-months"></a>다음 3 개월

|등록했나요?|항목|
|:-|:-|
||셀프 서비스 앱 관리 사용|
||셀프 서비스 그룹 관리 사용|
||앱 사용을 모니터링 하 여 insights를 추진 합니다.|
||지원 전화 드라이버 이해|

## <a name="next-steps"></a>다음 단계

Azure Active Directory 기능을 사용 하 여 보안 상태를 높일 수 있는 방법 및 5 단계 검사 목록- [id 인프라를 보호 하는 5 단계](https://aka.ms/securitysteps)를 알아보세요.

조직이 기존 온-프레미스 시스템에서 Azure AD로의 id 관리를 신속 하 게 채택 하 고 이동할 수 있도록 하는 솔루션 및 기능을 제공 하 여 클라우드 관리 관리로의 전환을 가속화 하는 방법에 대해 알아봅니다. [AZURE Ad는 온-프레미스 워크 로드에 대 한 클라우드 관리 관리](https://aka.ms/cloudgoverned)기능을 제공 합니다.
