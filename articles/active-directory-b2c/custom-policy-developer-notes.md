---
title: 사용자 지정 정책에 대한 개발자 정보
titleSuffix: Azure AD B2C
description: 사용자 지정 정책으로 Azure AD B2C를 구성 및 유지 관리하는 개발자를 위한 정보
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/19/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 23e443a542ed22906abb237e402834b707e623a9
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88852047"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C의 사용자 지정 정책에 대한 개발자 정보

이제 Azure Active Directory B2C의 사용자 지정 정책 구성이 일반 제공됩니다. 이 구성 방법은 복잡한 ID 솔루션을 구축하는 고급 ID 개발자를 대상으로 합니다. 사용자 지정 정책을 통해 Azure AD B2C 테넌트에서 Identity Experience Framework의 기능을 사용할 수 있습니다.
사용자 지정 정책을 사용하는 고급 ID 개발자는 연습을 완료하고 참조 문서를 확인하는 데 시간을 어느 정도 투자해야 합니다.

현재 사용 가능한 대부분의 사용자 지정 정책 옵션이 일반 공급되며, 소프트웨어 수명 주기의 다른 단계에 있는 기술 프로필 유형 및 콘텐츠 정의 API와 같은 기본 기능이 있습니다. 더 많은 기능이 제공됩니다. 아래 표에서는 가용성 수준을 보다 세분화된 수준으로 지정합니다.

## <a name="features-that-are-generally-available"></a>일반 공급된 기능

- 사용자 지정 정책을 사용하여 사용자 지정 인증 사용자 경험을 작성 및 업로드합니다.
    - 클레임 공급자 간의 교환으로 사용자 경험을 단계별로 설명합니다.
    - 사용자 경험에서 조건부 분기를 정의합니다.
- 사용자 지정 인증 사용자 경험에서 REST API 사용 서비스를 상호 운용합니다.
- OpenIDConnect 프로토콜과 호환되는 ID 공급자와 페더레이션합니다.
- SAML 2.0 프로토콜을 준수하는 ID 공급자와 페더레이션합니다.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>사용자 지정 정책 기능 집합 개발자의 책임

수동 정책 구성은 Azure AD B2C의 기본 플랫폼에 대해 낮은 수준의 액세스 권한을 부여하여 고유한 보안 프레임워크를 생성합니다. 다각적인 사용자 지정 ID 공급자, 트러스트 관계, 외부 서비스와의 통합 및 단계별 워크플로를 사용하려면 설계 및 구성에 대한 체계적인 접근 방식이 필요합니다.

사용자 지정 정책 기능 집합을 사용하는 개발자는 다음 지침을 준수해야 합니다.

- 사용자 지정 정책 및 키/비밀 관리의 구성 언어에 익숙해집니다. 자세한 내용은 [TrustFrameworkPolicy](trustframeworkpolicy.md)를 참조하세요.
- 시나리오 및 사용자 지정 통합을 직접 소유합니다. 작업을 문서화하고 라이브 사이트 조직에 알립니다.
- 체계적인 시나리오 테스트를 수행합니다.
- 최소 1개의 개발/테스트 환경과 1개의 프로덕션 환경을 구축하여 소프트웨어 개발 및 스테이징 모범 사례를 준수합니다.
- 사용자와 통합된 ID 공급자 및 서비스에 대한 새로운 개발 정보를 바로 입수합니다. 예를 들어 기밀 변경 내용과 예정되었거나 갑작스럽게 진행되는 서비스 변경 내용을 추적합니다.
- 활성 모니터링을 설정하고 프로덕션 환경의 응답성을 모니터링합니다. Application Insights와 통합하는 방법에 대한 자세한 내용은 [Azure Active Directory B2C: 로그 수집](analytics-with-application-insights.md)을 참조하세요.
- Azure 구독에서 연락처 전자 메일 주소를 최신 상태로 유지하고 Microsoft 라이브 사이트 팀 전자 메일에 즉시 응답합니다.
- Microsoft 라이브 사이트 팀에서 권고할 때 시기 적절하게 조치를 취합니다.

## <a name="terms-for-features-in-public-preview"></a>공개 미리 보기 기능에 대한 사용 조건

- 공용 미리 보기 기능은 평가 목적으로 사용하는 것이 좋습니다.
- SLA(서비스 수준 약정)는 공용 미리 보기 기능에 적용되지 않습니다.
- 공개 미리 보기 기능에 대한 지원 요청은 정식 지원 채널을 통해 제출할 수 있습니다.

## <a name="features-by-stage-and-known-issues"></a>단계 및 알려진 문제별 기능

사용자 지정 정책/Identity Experience Framework 기능은 지속적으로 빠르게 개발되고 있습니다. 다음 표는 기능 및 구성 요소 가용성 색인입니다.


### <a name="protocols-and-authorization-flows"></a>프로토콜 및 인증 흐름

| 기능 | 개발 | 미리 보기 | GA | 메모 |
|-------- | :-----------: | :-------: | :--: | ----- |
| [OAuth2 인증 코드](authorization-code-flow.md) |  |  | X |  |
| PKCE를 사용하는 OAuth2 인증 코드 |  |  | X | 모바일 애플리케이션 전용  |
| [OAuth2 암시적 흐름](implicit-flow-single-page-application.md) |  |  | X |  |
| [OAuth2 리소스 소유자 암호 자격 증명](ropc-custom.md) |  | X |  |  |
| [OIDC Connect](openid-connect.md) |  |  | X |  |
| [SAML2](connect-with-saml-service-providers.md)  |  |  |X  | POST 바인딩 및 리디렉션 바인딩 |
| OAuth1 |  |  |  | 지원되지 않습니다. |
| WSFED | X |  |  |  |

### <a name="identify-providers-federation"></a>공급자 페더레이션 식별 

| 기능 | 개발 | 미리 보기 | GA | 메모 |
|-------- | :-----------: | :-------: | :--: | ----- |
| [OpenID Connect](openid-connect-technical-profile.md) |  |  | X | 예: Google+  |
| [OAuth2](oauth2-technical-profile.md) |  |  | X | 예: Facebook  |
| [OAuth1](oauth1-technical-profile.md) |  | X |  | 예: Twitter |
| [SAML2](saml-identity-provider-technical-profile.md) |  |   | X | 예: Salesforce, ADFS |
| WSFED| X |  |  |  |


### <a name="rest-api-integration"></a>REST API 통합

| 기능 | 개발 | 미리 보기 | GA | 메모 |
|-------- | :-----------: | :-------: | :--: | ----- |
| [기본 인증을 사용하는 REST API](secure-rest-api.md#http-basic-authentication) |  |  | X |  |
| [클라이언트 인증서 인증을 사용하는 REST API](secure-rest-api.md#https-client-certificate-authentication) |  |  | X |  |
| [OAuth2 전달자 인증을 사용하는 REST API](secure-rest-api.md#oauth2-bearer-authentication) |  | X |  |  |

### <a name="component-support"></a>구성 요소 지원

| 기능 | 개발 | 미리 보기 | GA | 메모 |
| ------- | :-----------: | :-------: | :--: | ----- |
| [Phone 요소 인증](phone-factor-technical-profile.md) |  |  | X |  |
| [Azure MFA 인증](multi-factor-auth-technical-profile.md) |  | X |  |  |
| [일회용 암호](one-time-password-technical-profile.md) |  | X |  |  |
| 로컬 디렉터리로서의 [Azure Active Directory](active-directory-technical-profile.md) |  |  | X |  |
| 이메일 확인을 위한 Azure 이메일 하위 시스템 |  |  | X |  |
| [타사 이메일 서비스 공급자](custom-email-mailjet.md) |  |X  |  |  |
| [다중 언어 지원](localization.md)|  |  | X |  |
| [조건자 유효성 검사](predicates.md) |  |  | X | 예: 암호 복잡성 |
| [표시 컨트롤](display-controls.md) |  |X  |  |  |


### <a name="page-layout-versions"></a>페이지 레이아웃 버전

| 기능 | 개발 | 미리 보기 | GA | 메모 |
| ------- | :-----------: | :-------: | :--: | ----- |
| [2.0.0](page-layout.md) |  | X |  |  |
| [1.2.0](page-layout.md) |  | X |  |  |
| [1.1.0](page-layout.md) |  |  | X |  |
| [1.0.0](page-layout.md) |  |  | X |  |
| [JavaScript 지원](javascript-samples.md) |  | X |  |  |

### <a name="app-ief-integration"></a>App-IEF 통합

| 기능 | 개발 | 미리 보기 | GA | 메모 |
| ------- | :-----------: | :-------: | :--: | ----- |
| 쿼리 문자열 매개 변수 `domain_hint` |  |  | X | 클레임으로 사용 가능, IDP로 전달될 수 있음 |
| 쿼리 문자열 매개 변수 `login_hint` |  |  | X | 클레임으로 사용 가능, IDP로 전달될 수 있음 |
| `client_assertion`을 통해 사용자 경험에 JSON 삽입 | X |  |  | 지원 중단 예정 |
| 사용자 경험에 `id_token_hint`로 JSON 삽입 |  | X |  | JSON을 전달하는 진행 방법 |
| [애플리케이션에 ID 공급자 토큰 전달](idp-pass-through-custom.md) |  | X |  | 예: Facebook에서 앱으로 전달 |

### <a name="session-management"></a>세션 관리

| 기능 | 개발 | 미리 보기 | GA | 메모 |
| ------- | :-----------: | :-------: | :--: | ----- |
| [기본 SSO 세션 공급자](custom-policy-reference-sso.md#defaultssosessionprovider) |  |  | X |  |
| [외부 로그인 세션 공급자](custom-policy-reference-sso.md#externalloginssosessionprovider) |  |  | X |  |
| [SAML SSO 세션 공급자](custom-policy-reference-sso.md#samlssosessionprovider) |  |  | X |  |
| [OAuthSSOSessionProvider](custom-policy-reference-sso.md#oauthssosessionprovider)  |  | X |  |  |
| [Single Sign-Out](session-overview.md#sign-out)  |  | X |  |  |

### <a name="security"></a>보안

| 기능 | 개발 | 미리 보기 | GA | 메모 |
|-------- | :-----------: | :-------: | :--: | ----- |
| 정책 키 - 생성, 수동, 업로드 |  |  | X |  |
| 정책 키 - RSA/Cert, 비밀 |  |  | X |  |


### <a name="developer-interface"></a>개발자 인터페이스

| 기능 | 개발 | 미리 보기 | GA | 메모 |
| ------- | :-----------: | :-------: | :--: | ----- |
| Azure Portal-IEF UX |  |  | X |  |
| 정책 업로드 |  |  | X |  |
| [Application Insights 사용자 경험 로그](troubleshoot-with-application-insights.md) |  | X |  | 개발 중 문제를 해결하는 데 사용됨  |
| [Application Insights 이벤트 로그](application-insights-technical-profile.md) |  | X |  | 프로덕션에서 사용자 흐름을 모니터링하는 데 사용됨 |


## <a name="next-steps"></a>다음 단계

- Azure AD B2C에 사용할 수 있는 [Microsoft Graph 작업](microsoft-graph-operations.md) 확인
- [사용자 지정 정책 및 사용자 흐름과의 차이점](custom-policy-overview.md)에 대해 자세히 알아보기
