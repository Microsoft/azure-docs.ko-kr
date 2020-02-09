---
title: 작업 보고서를 사용 하 여 AD FS 앱을 Azure Active Directory로 이동 | Microsoft Docs '
description: Active Directory Federation Services (AD FS) 응용 프로그램 작업 보고서를 사용 하면 AD FS에서 Azure Active Directory (Azure AD)로 신속 하 게 응용 프로그램을 마이그레이션할 수 있습니다. AD FS에 대 한이 마이그레이션 도구는 Azure AD와의 호환성을 식별 하 고 마이그레이션 지침을 제공 합니다.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/14/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 333e440fdd5f5062dda45fb12a83543c63e66c04
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978026"
---
# <a name="use-the-ad-fs-application-activity-report-preview-to-migrate-applications-to-azure-ad"></a>AD FS 응용 프로그램 작업 보고서 (미리 보기)를 사용 하 여 응용 프로그램을 Azure AD로 마이그레이션합니다.

많은 조직에서 Active Directory Federation Services (AD FS)를 사용 하 여 클라우드 응용 프로그램에 Single Sign-On을 제공 합니다. 인증을 위해 AD FS 응용 프로그램을 Azure AD로 이동 하는 데 상당한 이점이 있습니다. 특히 비용 관리, 위험 관리, 생산성, 규정 준수 및 거 버 넌 스를 기준으로 합니다. 그러나 Azure AD와 호환 되는 응용 프로그램을 이해 하 고 특정 마이그레이션 단계를 식별 하는 것은 시간이 오래 걸릴 수 있습니다.

Azure Portal의 AD FS 응용 프로그램 작업 보고서 (미리 보기)를 사용 하면 Azure AD로 마이그레이션할 수 있는 응용 프로그램을 신속 하 게 식별할 수 있습니다. Azure AD와의 호환성을 위해 모든 AD FS 응용 프로그램을 평가 하 고, 문제를 확인 하 고, 개별 응용 프로그램을 마이그레이션하기 위한 지침을 제공 합니다. AD FS 응용 프로그램 활동 보고서를 사용 하 여 다음을 수행할 수 있습니다.

* **AD FS 응용 프로그램을 검색 하 고 마이그레이션 범위를 확인 합니다.** AD FS 응용 프로그램 작업 보고서는 조직에 있는 모든 AD FS 응용 프로그램을 나열 하 고 Azure AD로의 마이그레이션 준비를 나타냅니다.
* **마이그레이션을 위해 응용 프로그램의 우선 순위를 지정 합니다.** 응용 프로그램 마이그레이션의 중요도를 결정 하는 데 도움이 되도록 지난 1, 7 또는 30 일 동안 응용 프로그램에 로그인 한 고유 사용자 수를 가져옵니다.
* **마이그레이션 테스트를 실행 하 고 문제를 해결 합니다.** 보고 서비스는 테스트를 자동으로 실행 하 여 응용 프로그램을 마이그레이션할 준비가 되었는지 여부를 확인 합니다. 결과는 AD FS 응용 프로그램 작업 보고서에 마이그레이션 상태로 표시 됩니다. 잠재적인 마이그레이션 문제가 식별 되 면 문제를 해결 하는 방법에 대 한 구체적인 지침을 얻을 수 있습니다.

전역 관리자, 보고서 읽기 권한자, 보안 읽기 권한자, 응용 프로그램 관리자 또는 클라우드 응용 프로그램 관리자와 같은 관리 역할이 할당 된 사용자는 AD FS 응용 프로그램 작업 데이터를 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* 조직에서 응용 프로그램에 액세스 하려면 현재 AD FS를 사용 해야 합니다.
* Azure AD 테넌트에서 Azure AD Connect Health를 사용 하도록 설정 해야 합니다.
   * [Azure AD Connect Health에 대 한 자세한 정보](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)
   * [Azure AD Connect Health 설정 시작](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install)

## <a name="discover-ad-fs-applications-that-can-be-migrated"></a>마이그레이션할 수 있는 AD FS 응용 프로그램 검색 

AD FS 응용 프로그램 작업 보고서는 Azure AD **사용량 & insights** 보고의 Azure Portal에서 사용할 수 있습니다. AD FS 응용 프로그램 작업 보고서는 각 AD FS 응용 프로그램을 분석 하 여 있는 그대로 마이그레이션할 수 있는지 또는 추가 검토가 필요한 지를 결정 합니다. 

1. AD FS 응용 프로그램 활동 데이터 (전역 관리자, 보고서 읽기 권한자, 보안 읽기 권한자, 응용 프로그램 관리자 또는 클라우드 응용 프로그램 관리자)에 대 한 액세스 권한이 있는 관리자 역할을 사용 하 여 [Azure Portal](https://portal.azure.com) 에 로그인 합니다.

2. **Azure Active Directory**를 선택 하 고 **엔터프라이즈 응용 프로그램**을 선택 합니다.

3. **활동**에서 **사용 & Insights (미리 보기)** 를 선택 하 고 **AD FS 응용 프로그램 활동** 을 선택 하 여 조직의 모든 AD FS 응용 프로그램 목록을 엽니다.

   ![응용 프로그램 작업 AD FS](media/migrate-adfs-application-activity/adfs-application-activity.png)

4. 응용 프로그램 AD FS 작업 목록의 각 응용 프로그램에 대해 **마이그레이션 상태**를 확인 합니다.

   * **마이그레이션할 준비가** 됨은 AD FS 응용 프로그램 구성이 Azure AD에서 완전히 지원 되며 그대로 마이그레이션할 수 있음을 의미 합니다.

   * **검토 필요** 는 응용 프로그램의 설정 중 일부를 Azure AD로 마이그레이션할 수 있지만 그대로 마이그레이션할 수 없는 설정을 검토 해야 한다는 것을 의미 합니다.

   * **필요한 추가 단계** 는 Azure AD가 응용 프로그램의 설정 중 일부를 지원 하지 않으므로 현재 상태에서 응용 프로그램을 마이그레이션할 수 없음을 의미 합니다.

## <a name="evaluate-the-readiness-of-an-application-for-migration"></a>응용 프로그램의 마이그레이션 준비 상태 평가 

1. AD FS 응용 프로그램 작업 목록에서 마이그레이션 **상태** 열의 상태를 클릭 하 여 마이그레이션 세부 정보를 엽니다. 잠재적인 마이그레이션 문제와 함께 전달 된 구성 테스트에 대 한 요약이 표시 됩니다.

   ![마이그레이션 세부 정보](media/migrate-adfs-application-activity/migration-details.png)

2. 추가 마이그레이션 규칙 세부 정보를 열려면 메시지를 클릭 합니다. 테스트 된 속성의 전체 목록은 아래 [AD FS 응용 프로그램 구성 테스트](#ad-fs-application-configuration-tests) 표를 참조 하세요.

   ![마이그레이션 규칙 세부 정보](media/migrate-adfs-application-activity/migration-rule-details.png)

### <a name="ad-fs-application-configuration-tests"></a>응용 프로그램 구성 테스트 AD FS

다음 표에는 AD FS 응용 프로그램에서 수행 되는 모든 구성 테스트가 나열 되어 있습니다.

|결과  |성공/경고/실패  |Description  |
|---------|---------|---------|
|ADFSRPAdditionalAuthenticationRules <br> AdditionalAuthentication에 대해 마이그레이션할 수 있고 규칙이 하나 이상 검색 되지 않았습니다.       | 통과/경고          | 신뢰 당사자에 게 MFA (multi-factor authentication)를 요구 하는 규칙이 있습니다. Azure AD로 이동 하려면 이러한 규칙을 조건부 액세스 정책으로 변환 합니다. 온-프레미스 MFA를 사용 하는 경우 Azure MFA로 이동 하는 것이 좋습니다. [조건부 액세스에 대해 자세히 알아보세요](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).        |
|ADFSRPAdditionalWSFedEndpoint <br> 신뢰 당사자가 AdditionalWSFedEndpoint를 true로 설정 했습니다.       | 성공/실패          | AD FS 신뢰 당사자는 여러 개의 WS 급지됨 어설션 엔드포인트을 허용 합니다. 현재 Azure AD는 하나만 지원 합니다. 이 결과가 마이그레이션을 차단 하는 시나리오를 [사용](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695621-allow-multiple-ws-fed-assertion-endpoints)하는 경우 알려주세요.     |
|ADFSRPAllowedAuthenticationClassReferences <br> 신뢰 당사자가 AllowedAuthenticationClassReferences를 설정 했습니다.       | 성공/실패          | AD FS에서이 설정을 사용 하면 응용 프로그램이 특정 인증 유형만 허용 하도록 구성 되어 있는지 여부를 지정할 수 있습니다. 조건부 액세스를 사용 하 여이 기능을 구현 하는 것이 좋습니다.  이 결과가 마이그레이션을 차단 하는 시나리오를 [사용](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695672-allow-in-azure-ad-to-specify-certain-authentication)하는 경우 알려주세요.  [조건부 액세스에 대해 자세히 알아보세요](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).          |
|ADFSRPAlwaysRequireAuthentication <br> AlwaysRequireAuthenticationCheckResult      | 성공/실패          | AD FS에서이 설정을 사용 하면 응용 프로그램이 SSO 쿠키를 무시 하도록 구성 되었는지 여부를 지정 하 고 **항상 인증 메시지를 표시**하도록 구성할 수 있습니다. Azure AD에서 조건부 액세스 정책을 사용 하 여 인증 세션을 관리 하 여 유사한 동작을 달성할 수 있습니다. [조건부 액세스를 사용 하 여 인증 세션 관리를 구성 하는 방법에 대해 자세히 알아보세요](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime).          |
|테스트-ADFSRPAutoUpdateEnabled <br> 신뢰 당사자가 AutoUpdateEnabled를 true로 설정 했습니다.       | 통과/경고          | AD FS에서이 설정을 사용 하면 AD FS 페더레이션 메타 데이터 내의 변경 내용에 따라 응용 프로그램을 자동으로 업데이트 하도록 구성할 지 여부를 지정할 수 있습니다. Azure AD는 현재이를 지원 하지 않지만 응용 프로그램을 Azure AD로 마이그레이션하지 못하도록 차단 해서는 안 됩니다.           |
|ADFSRPClaimsProviderName <br> 신뢰 당사자가 여러 ClaimsProviders를 사용 하도록 설정 됨       | 성공/실패          | 이 AD FS의이 설정은 신뢰 당사자가 클레임을 수락 하는 id 공급자를 호출 합니다. Azure AD에서는 Azure AD B2B를 사용 하 여 외부 공동 작업을 사용 하도록 설정할 수 있습니다. [AZURE AD B2B에 대해 자세히 알아보세요](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b).          |
|ADFSRPDelegationAuthorizationRules      | 성공/실패          | 응용 프로그램에 정의 된 사용자 지정 위임 권한 부여 규칙이 있습니다. 이는 Azure AD에서 Openid connect Connect 및 OAuth 2.0과 같은 최신 인증 프로토콜을 사용 하 여 지 원하는 WS-TRUST 개념입니다. [Microsoft Id 플랫폼에 대해 자세히 알아보세요](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc).          |
|ADFSRPImpersonationAuthorizationRules       | 통과/경고          | 응용 프로그램에 사용자 지정 가장 권한 부여 규칙이 정의 되어 있습니다. 이는 Azure AD에서 Openid connect Connect 및 OAuth 2.0과 같은 최신 인증 프로토콜을 사용 하 여 지 원하는 WS-TRUST 개념입니다. [Microsoft Id 플랫폼에 대해 자세히 알아보세요](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc).          |
|ADFSRPIssuanceAuthorizationRules <br> IssuanceAuthorization에 대 한 마이그레이션할 수 있고 규칙이 하나 이상 검색 되지 않았습니다.       | 통과/경고          | 응용 프로그램에 AD FS에 정의 된 사용자 지정 발급 권한 부여 규칙이 있습니다. Azure AD는 Azure AD 조건부 액세스와 함께이 기능을 지원 합니다. [조건부 액세스에 대해 자세히 알아보세요](https://docs.microsoft.com/azure/active-directory/conditional-access/overview). <br> 응용 프로그램에 할당 된 사용자 또는 그룹을 기준으로 응용 프로그램에 대 한 액세스를 제한할 수도 있습니다. [응용 프로그램에 액세스 하기 위해 사용자 및 그룹을 할당 하는 방법에 대해 자세히 알아보세요](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups).            |
|ADFSRPIssuanceTransformRules <br> IssuanceTransform에 대 한 마이그레이션할 수 있고 규칙이 하나 이상 검색 되지 않았습니다.       | 통과/경고          | 응용 프로그램에 AD FS에 정의 된 사용자 지정 발급 변환 규칙이 있습니다. Azure AD는 토큰에서 발급 된 클레임 사용자 지정을 지원 합니다. 자세한 내용은 [엔터프라이즈 응용 프로그램에 대 한 SAML 토큰에서 발급 된 클레임 사용자 지정](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)을 참조 하세요.           |
|ADFSRPMonitoringEnabled <br> 신뢰 당사자가 MonitoringEnabled를 true로 설정 했습니다.       | 통과/경고          | AD FS에서이 설정을 사용 하면 AD FS 페더레이션 메타 데이터 내의 변경 내용에 따라 응용 프로그램을 자동으로 업데이트 하도록 구성할 지 여부를 지정할 수 있습니다. Azure AD는 현재이를 지원 하지 않지만 응용 프로그램을 Azure AD로 마이그레이션하지 못하도록 차단 해서는 안 됩니다.           |
|ADFSRPNotBeforeSkew <br> NotBeforeSkewCheckResult      | 통과/경고          | AD FS를 사용 하면 SAML 토큰의 NotBefore 및 NotOnOrAfter 시간을 기준으로 시간을 기울일 수 있습니다. Azure AD에서는 기본적으로이를 자동으로 처리 합니다.          |
|ADFSRPRequestMFAFromClaimsProviders <br> 신뢰 당사자가 RequestMFAFromClaimsProviders를 true로 설정 했습니다.       | 통과/경고          | AD FS의이 설정은 사용자가 다른 클레임 공급자에서 가져온 경우 MFA에 대 한 동작을 결정 합니다. Azure AD에서는 Azure AD B2B를 사용 하 여 외부 공동 작업을 사용 하도록 설정할 수 있습니다. 그런 다음 조건부 액세스 정책을 적용 하 여 게스트 액세스를 보호할 수 있습니다. [AZURE AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b) 및 [조건부 액세스](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)에 대해 자세히 알아보세요.          |
|ADFSRPSignedSamlRequestsRequired <br> 신뢰 당사자가 SignedSamlRequestsRequired를 true로 설정 했습니다.       | 성공/실패          | 응용 프로그램은 SAML 요청에서 서명을 확인 하기 위해 AD FS 구성 됩니다. Azure AD는 서명 된 SAML 요청을 수락 합니다. 그러나 서명을 확인 하지 않습니다. Azure AD에는 악의적인 호출 로부터 보호 하는 여러 방법이 있습니다. 예를 들어 Azure AD는 응용 프로그램에 구성 된 회신 Url을 사용 하 여 SAML 요청의 유효성을 검사 합니다. Azure AD는 응용 프로그램에 대해 구성 된 회신 Url에만 토큰을 보냅니다. 이 결과가 마이그레이션을 차단 하는 시나리오를 [사용](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/13394589-saml-signature)하는 경우 알려주세요.          |
|ADFSRPTokenLifetime <br> TokenLifetimeCheckResult        | 통과/경고         | 응용 프로그램은 사용자 지정 토큰 수명에 대해 구성 됩니다. AD FS 기본값은 1 시간입니다. Azure AD는 조건부 액세스를 사용 하 여이 기능을 지원 합니다. 자세히 알아보려면 [조건부 액세스를 사용 하 여 인증 세션 관리 구성](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime)을 참조 하세요.          |
|신뢰 당사자가 클레임을 암호화 하도록 설정 되어 있습니다. Azure AD에서 지원 됩니다.       | 합격          | Azure AD를 사용 하 여 응용 프로그램에 전송 된 토큰을 암호화할 수 있습니다. 자세히 알아보려면 [AZURE AD SAML 토큰 암호화 구성](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)을 참조 하세요.          |
|EncryptedNameIdRequiredCheckResult      | 성공/실패          | 응용 프로그램은 SAML 토큰의 nameID 클레임을 암호화 하도록 구성 됩니다. Azure AD를 사용 하면 응용 프로그램에 전송 된 전체 토큰을 암호화할 수 있습니다. 특정 클레임의 암호화는 아직 지원 되지 않습니다. 자세히 알아보려면 [AZURE AD SAML 토큰 암호화 구성](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)을 참조 하세요.         |

## <a name="check-the-results-of-claim-rule-tests"></a>클레임 규칙 테스트의 결과를 확인 합니다.

AD FS에서 응용 프로그램에 대 한 클레임 규칙을 구성한 경우이 환경은 모든 클레임 규칙에 대 한 세부적인 분석을 제공 합니다. Azure AD로 이동할 수 있는 클레임 규칙과 추가 검토가 필요한 클레임 규칙을 확인할 수 있습니다.

1. AD FS 응용 프로그램 작업 목록에서 마이그레이션 **상태** 열의 상태를 클릭 하 여 마이그레이션 세부 정보를 엽니다. 잠재적인 마이그레이션 문제와 함께 전달 된 구성 테스트에 대 한 요약이 표시 됩니다.

2. **마이그레이션 규칙 세부 정보** 페이지에서 결과를 확장 하 여 잠재적인 마이그레이션 문제에 대 한 세부 정보를 표시 하 고 추가 지침을 가져옵니다. 테스트 된 모든 클레임 규칙의 자세한 목록은 아래의 [클레임 규칙 테스트 결과 확인](#check-the-results-of-claim-rule-tests) 표를 참조 하세요.

   아래 예제에서는 IssuanceTransform 규칙에 대 한 마이그레이션 규칙 정보를 보여 줍니다. 응용 프로그램을 Azure AD로 마이그레이션하기 전에 검토 하 고 해결 해야 하는 클레임의 특정 부분을 나열 합니다.

   ![마이그레이션 규칙 세부 정보 추가 지침](media/migrate-adfs-application-activity/migration-rule-details-guidance.png)

### <a name="claim-rule-tests"></a>클레임 규칙 테스트

다음 표에는 AD FS 응용 프로그램에서 수행 되는 모든 클레임 규칙 테스트가 나열 되어 있습니다.

|속성  |Description  |
|---------|---------|
|UNSUPPORTED_CONDITION_PARAMETER      | 조건문은 정규식을 사용 하 여 클레임이 특정 패턴과 일치 하는지 여부를 평가 합니다.  Azure AD에서 유사한 기능을 얻기 위해 IfEmpty (), StartWith (), Contains () 등의 미리 정의 된 변환을 사용할 수 있습니다. 자세한 내용은 [엔터프라이즈 응용 프로그램에 대 한 SAML 토큰에서 발급 된 클레임 사용자 지정](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)을 참조 하세요.          |
|UNSUPPORTED_CONDITION_CLASS      | Condition 문에는 발급 문을 실행 하기 전에 평가 해야 하는 여러 조건이 있습니다. Azure AD는 클레임의 변환 함수를 사용 하 여 여러 클레임 값을 평가할 수 있는이 기능을 지원할 수 있습니다.  자세한 내용은 [엔터프라이즈 응용 프로그램에 대 한 SAML 토큰에서 발급 된 클레임 사용자 지정](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)을 참조 하세요.          |
|UNSUPPORTED_RULE_TYPE      | 클레임 규칙을 인식할 수 없습니다. Azure AD에서 클레임을 구성 하는 방법에 대 한 자세한 내용은 [엔터프라이즈 응용 프로그램에 대 한 SAML 토큰에서 발급 된 클레임 사용자 지정](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)을 참조 하세요.          |
|CONDITION_MATCHES_UNSUPPORTED_ISSUER      | Condition 문이 Azure AD에서 지원 되지 않는 발급자를 사용 합니다. 현재 Azure AD는 Active Directory 또는 Azure AD와 다른 저장소에서의 원본 클레임을 따르지 않습니다. 응용 프로그램을 Azure AD로 마이그레이션하는 것을 차단 하는 경우 [microsoft](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire)에 알려주세요.         |
|UNSUPPORTED_CONDITION_FUNCTION      | 조건 문은 집계 함수를 사용 하 여 일치 하는 항목 수에 관계 없이 단일 클레임을 발급 하거나 추가 합니다.  Azure AD에서는 사용자의 특성을 평가 하 여 IfEmpty (), StartWith (), Contains () 등의 함수를 사용 하 여 클레임에 사용할 값을 결정할 수 있습니다. 자세한 내용은 [엔터프라이즈 응용 프로그램에 대 한 SAML 토큰에서 발급 된 클레임 사용자 지정](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)을 참조 하세요.          |
|RESTRICTED_CLAIM_ISSUED      | Condition 문은 Azure AD에서 제한 된 클레임을 사용 합니다. 제한 된 클레임을 발급할 수 있지만 원본을 수정 하거나 변환을 적용할 수 없습니다. 자세한 내용은 [AZURE AD의 특정 앱에 대 한 토큰에서 내보낸 클레임 사용자 지정](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping)을 참조 하세요.          |
|EXTERNAL_ATTRIBUTE_STORE      | 발급 문에서 Active Directory 다른 특성 저장소를 사용 합니다. 현재 Azure AD는 Active Directory 또는 Azure AD와 다른 저장소에서의 원본 클레임을 따르지 않습니다. 이로 인해 응용 프로그램을 Azure AD로 마이그레이션하는 것을 [차단 하는](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire)경우에는 알려주세요.          |
|UNSUPPORTED_ISSUANCE_CLASS      | 발급 문은 ADD를 사용 하 여 들어오는 클레임 집합에 클레임을 추가 합니다. Azure AD에서이는 여러 클레임 변환으로 구성 될 수 있습니다.  자세한 내용은 [엔터프라이즈 응용 프로그램에 대 한 SAML 토큰에서 발급 된 클레임 사용자 지정](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping)을 참조 하세요.         |
|UNSUPPORTED_ISSUANCE_TRANSFORMATION      | 발급 문은 정규식을 사용 하 여 내보낼 클레임 값을 변환 합니다. Azure AD에서 유사한 기능을 얻기 위해 Extract (), Trim (), ToLower 등의 미리 정의 된 변환을 사용할 수 있습니다. 자세한 내용은 [엔터프라이즈 응용 프로그램에 대 한 SAML 토큰에서 발급 된 클레임 사용자 지정](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)을 참조 하세요.          |


## <a name="next-steps"></a>다음 단계

- [비디오: AD FS 활동 보고서를 사용 하 여 응용 프로그램을 마이그레이션하는 방법](https://www.youtube.com/watch?v=OThlTA239lU)
- [Azure Active Directory로 애플리케이션 관리](what-is-application-management.md)
- [앱에 대한 액세스 관리](what-is-access-management.md)
- [Azure AD Connect 페더레이션](../hybrid/how-to-connect-fed-whatis.md)
