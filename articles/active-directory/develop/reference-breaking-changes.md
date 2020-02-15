---
title: Azure Active Directory 새로운 변경 참조 | Microsoft Docs
description: 애플리케이션에 영향을 줄 수 있는 Azure AD 프로토콜의 변경 내용을 알아봅니다.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 68517c83-1279-4cc7-a7c1-c7ccc3dbe146
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 1/24/2020
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: c6ed72e5c94191411572c6ab67533141e2fe47d6
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77185812"
---
# <a name="whats-new-for-authentication"></a>인증의 새로운 기능? 

>이 페이지의 업데이트에 대한 알림을 받습니다. RSS 피드 리더에 [이 URL](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us)을 추가하면 됩니다.

인증 시스템에서는 보안 및 표준 준수를 개선하기 위해 지속적으로 변경하고 기능을 추가합니다. 최신 개발 정보를 확인할 수 있도록 이 문서에서는 다음과 같은 세부 정보를 제공합니다.

- 최신 기능
- 알려진 문제
- 프로토콜 변경 내용
- 사용되지 않는 기능

> [!TIP] 
> 이 페이지는 정기적으로 업데이트되므로 자주 방문해 주세요. 별도로 명시되지 않는 한, 이 변경 내용은 새로 등록된 애플리케이션에만 적용됩니다.  

## <a name="upcoming-changes"></a>예정된 변경

지금은 예약이 없습니다.  또는 프로덕션에 적용 되는 변경 내용에 대해서는 아래를 참조 하세요. 

## <a name="february-2020"></a>2020년 2월 

### <a name="empty-fragments-will-be-appended-to-every-http-redirect-from-the-login-endpoint"></a>로그인 끝점의 모든 HTTP 리디렉션에 빈 조각이 추가 됩니다. 

**개시 날짜**: 2020 년 2 월 8 일

**영향을 받는 엔드포인트**: v1.0 및 v2.0 모두

**영향을 받는 프로토콜**: Response_type = 쿼리를 사용 하는 OAuth 및 OIDC 흐름-경우에 따라 [권한 부여 코드 흐름](v2-oauth2-auth-code-flow.md) 및 [암시적 흐름이](v2-oauth2-implicit-grant-flow.md)포함 됩니다. 

HTTP 리디렉션을 통해 login.microsoftonline.com에서 응용 프로그램으로 인증 응답을 보내면 서비스는 회신 URL에 빈 조각을 추가 합니다.  이렇게 하면 브라우저가 인증 요청에서 기존 조각을 모두 초기화 하 여 공격에 대 한 클래스를 방지할 수 있습니다.  이 동작에 대 한 종속성이 있는 앱이 없습니다. 


## <a name="august-2019"></a>2019년 8월

### <a name="post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored"></a>게시 양식 의미 체계가 보다 엄격 하 게 적용 되며 따옴표는 무시 됩니다.

**개시 날짜**: 2019 년 9 월 2 일

**영향을 받는 엔드포인트**: v1.0 및 v2.0 모두

**영향을 받는 프로토콜**: 어디서 나 게시를 사용 합니다 ([클라이언트 자격 증명](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow), [인증 코드 상환](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow), [ropc](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc), [obo](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)및 [새로 고침 토큰 상환](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-auth-code-flow#refresh-the-access-token)).

9/2 주부터 POST 메서드를 사용 하는 인증 요청은 보다 엄격한 HTTP 표준을 사용 하 여 유효성이 검사 됩니다.  특히 공백과 큰따옴표 (")는 요청 양식 값에서 더 이상 제거 되지 않습니다. 이러한 변경으로 인해 기존 클라이언트는 중단 되지 않으며, Azure AD로 전송 되는 요청은 매번 안정적으로 처리 됩니다. 나중에 (위 참조) 요청 내에서 중복 된 매개 변수를 거부 하 고 BOM을 무시할 계획입니다. 

예:

현재 `?e=    "f"&g=h` `?e=f&g=h`와 동일 하 게 구문 분석 되므로 `f` == `e`.  이와 같이 변경 하면 `e` == `    "f"` 하 여이를 구문 분석할 수 있습니다 .이는 유효한 인수가 될 가능성이 낮으므로 요청이 실패 합니다. 


## <a name="july-2019"></a>2019년 7월

### <a name="app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant"></a>단일 테 넌 트 응용 프로그램에 대 한 앱 전용 토큰은 클라이언트 앱이 리소스 테 넌 트에 있는 경우에만 실행 됩니다.

**개시 날짜**: 2019 년 7 월 26 일

**영향을 받는 엔드포인트**: [V1.0](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow) 및 [v2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow) 모두

**영향을 받는 프로토콜**: [클라이언트 자격 증명 (앱 전용 토큰)](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)

보안 변경은 응용 프로그램 전용 토큰 (클라이언트 자격 증명 부여를 통해 권한 부여를 통해)이 발급 되는 방식을 변경 하는 7 월 26 일에 실시간으로 발생 합니다. 이전에는 응용 프로그램에서 테 넌 트의 현재 상태와 상관 없이 해당 응용 프로그램에 대 한 동의한 다른 앱을 호출 하는 토큰을 가져올 수 있었습니다.  이 동작은 리소스 (웹 Api 라고도 함)가 단일 테 넌 트 (기본값)로 설정 되도록 업데이트 되었으므로 클라이언트 응용 프로그램이 리소스 테 넌 트 내에 있어야 합니다.  클라이언트와 API 간의 기존 동의는 여전히 필요 하지 않으며, 앱이 자체 권한 부여 검사를 수행 하 여 `roles` 클레임이 있고 API에 필요한 값을 포함 하 고 있는지 확인 해야 합니다.

이 시나리오에 대 한 오류 메시지는 현재 다음과 같습니다. 

`The service principal named <appName> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`

이 문제를 해결 하려면 관리자 동의 환경을 사용 하 여 테 넌 트에서 클라이언트 응용 프로그램 서비스 주체를 만들거나 수동으로 만듭니다.  이 요구 사항은 테 넌 트가 테 넌 트 내에서 작동할 수 있는 응용 프로그램 권한을 부여 했는지 확인 합니다.  

#### <a name="example-request"></a>요청 예

이 예제에서 `https://login.microsoftonline.com/contoso.com/oauth2/authorize?resource=https://gateway.contoso.com/api&response_type=token&client_id=14c88eee-b3e2-4bb0-9233-f5e3053b3a28&...` 리소스 테 넌 트 (기관)는 contoso.com 리소스 앱은 Contoso 테 넌 트에 대해 `gateway.contoso.com/api` 라는 단일 테 넌 트 앱 이며 클라이언트 앱은 `14c88eee-b3e2-4bb0-9233-f5e3053b3a28`됩니다.  클라이언트 앱이 Contoso.com 내에 서비스 주체를 포함 하는 경우이 요청을 계속할 수 있습니다.  그러나 그렇지 않으면 위의 오류가 발생 하 여 요청이 실패 합니다.  

그러나 Contoso 게이트웨이 앱이 다중 테 넌 트 응용 프로그램이 면 Contoso.com 내에서 서비스 사용자가 있는 클라이언트 앱에 관계 없이 요청이 계속 됩니다.  

### <a name="redirect-uris-can-now-contain-query-string-parameters"></a>이제 리디렉션 Uri는 쿼리 문자열 매개 변수를 포함할 수 있습니다.

**개시 날짜**: 2019년 7월 22일

**영향을 받는 엔드포인트**: v1.0 및 v2.0 모두

**영향을 받는 프로토콜**: 모든 흐름

이제 Azure AD 응용 프로그램은 [RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)에 따라 정적 쿼리 매개 변수 (예: OAuth 2.0 요청에 대 한 https://contoso.com/oauth2?idp=microsoft) )로 리디렉션 (회신) uri를 등록 하 고 사용할 수 있습니다.  동적 리디렉션 Uri는 보안 위험을 나타내므로 사용할 수 없으며 인증 요청에 대 한 상태 정보를 유지 하는 데 사용할 수 없습니다. 즉, `state` 매개 변수를 사용 합니다.

정적 쿼리 매개 변수는 리디렉션 URI의 다른 부분과 마찬가지로 리디렉션 uri에 대 한 문자열 일치가 적용 됩니다. URI로 디코딩된 redirect_uri와 일치 하는 문자열이 등록 되지 않은 경우 요청이 거부 됩니다.  앱 등록에서 URI를 찾은 경우에는 정적 쿼리 매개 변수를 포함 하 여 전체 문자열이 사용자를 리디렉션하는 데 사용 됩니다. 

이번에는 (7 월 2019 끝) Azure Portal의 앱 등록 UX에서 여전히 쿼리 매개 변수를 차단 합니다.  그러나 응용 프로그램 매니페스트를 수동으로 편집 하 여 쿼리 매개 변수를 추가 하 고 앱에서 테스트할 수 있습니다.  


## <a name="march-2019"></a>2019년 3월

### <a name="looping-clients-will-be-interrupted"></a>반복 클라이언트가 중단 됩니다.

**개시 날짜**: 3 월 25 일, 2019

**영향을 받는 엔드포인트**: v1.0 및 v2.0 모두

**영향을 받는 프로토콜**: 모든 흐름

클라이언트 응용 프로그램은 경우에 따라 짧은 시간 동안 수백 개의 동일한 로그인 요청을 실행 하 여 잘못 동작 하는 경우도 있습니다.  이러한 요청은 성공 하거나 성공 하지 않을 수도 있지만, 모든 사용자에 대 한 대기 시간이 IDP 모든 사용자의 대기 시간이 늘어나고 IDP의 가용성이 감소 합니다.  이러한 응용 프로그램은 정상적인 사용 범위를 벗어나 작동 하 고 올바르게 동작 하도록 업데이트 되어야 합니다.  

중복 요청을 여러 번 실행 하는 클라이언트는 `invalid_grant` 오류 `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request`전송 됩니다. 

대부분의 클라이언트는이 오류를 방지 하기 위해 동작을 변경할 필요가 없습니다.  잘못 구성 된 클라이언트 (토큰 캐싱을 사용 하지 않는 클라이언트 또는 프롬프트 루프를 포함 하는 클라이언트만 이미)는이 오류의 영향을 받습니다.  클라이언트는 다음 요소에 대해 로컬로 (쿠키를 통해) 인스턴스당 추적 됩니다.

* 사용자 힌트 (있는 경우)

* 요청 되는 범위 또는 리소스

* 클라이언트 ID

* 리디렉션 URI

* 응답 유형 및 모드

짧은 시간 (5 분) 내에 여러 요청 (15 +)을 만드는 앱은 루핑 임을 설명 하는 `invalid_grant` 오류를 받게 됩니다.  요청 되는 토큰은 충분히 수명이 긴 수명 (기본적으로 10 분, 최소 60 분) 이므로이 기간 동안 반복 되는 요청은 필요 하지 않습니다.  

모든 앱은 토큰을 자동으로 요청 하는 대신 대화형 프롬프트를 표시 하 여 `invalid_grant`를 처리 해야 합니다.  클라이언트는이 오류를 방지 하기 위해 수신 하는 토큰을 올바르게 캐싱하지 확인 해야 합니다.


## <a name="october-2018"></a>2018년 10월

### <a name="authorization-codes-can-no-longer-be-reused"></a>권한 부여 코드는 더 이상 재사용할 수 없습니다.

**개시 날짜**: 2018년 11월 15일

**영향을 받는 엔드포인트**: v1.0 및 v2.0 모두

**영향을 받는 프로토콜**: [코드 흐름](v2-oauth2-auth-code-flow.md)

2018년 11월 15일부터 Azure AD는 앱에서 이전에 사용된 인증 코드를 더 이상 수락하지 않습니다. 이 보안 변경은 Azure AD를 OAuth 사양에 맞추는 데 도움이 되며 v1 및 v2 엔드포인트 모두에 적용됩니다.

앱에서 여러 리소스에 대한 토큰을 얻기 위해 인증 코드를 재사용하는 경우에는 코드를 사용하여 새로 고침 토큰을 얻은 다음, 이 새로 고침 토큰을 사용하여 다른 리소스에 대한 추가 토큰을 얻는 것이 좋습니다. 인증 코드는 한 번만 사용할 수 있지만 새로 고침 토큰은 여러 리소스에서 여러 번 사용할 수 있습니다. OAuth 코드 흐름 중에 인증 코드를 다시 사용하려고 하는 새 앱에서는 invalid_grant 오류가 발생합니다.

새로 고침 토큰에 대한 자세한 내용은 [액세스 토큰 새로 고침](v2-oauth2-auth-code-flow.md#refresh-the-access-token)을 참조하세요.  ADAL 또는 MSAL을 사용하는 경우 이 작업은 라이브러리를 통해 처리됩니다. 'AcquireTokenByAuthorizationCodeAsync'의 두 번째 인스턴스를 'AcquireTokenSilentAsync'로 바꿉니다. 

## <a name="may-2018"></a>2018년 5월

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>ID 토큰은 OBO 흐름에 사용할 수 없습니다.

**날짜**: 2018년 5월 1일

**영향을 받는 엔드포인트**: v1.0 및 v2.0 모두

**영향을 받는 프로토콜**: 암시적 흐름 및 [대리](v2-oauth2-on-behalf-of-flow.md) 흐름

2018년 5월 1일 이 OBO 흐름에서 새 애플리케이션의 어설션으로 id_token을 사용할 수 없습니다. API를 보호하려면 클라이언트와 동일한 애플리케이션의 중간 계층 사이에서도 액세스 토큰을 대신 사용해야 합니다. 2018년 5월 1일 전에 등록된 앱은 계속 작동하고 id_token을 액세스 토큰으로 교환할 수 있지만 이 패턴은 모범 사례로 간주되지 않습니다.

이 변경 문제를 해결하려면 다음을 수행할 수 있습니다.

1. 하나 이상의 범위가 있는 애플리케이션의 Web API를 만듭니다. 이 명시적 진입점으로 인해 더 세분화된 제어 및 보안이 가능합니다.
1. 앱 매니페스트, [Azure Portal](https://portal.azure.com) 또는 [앱 등록 포털](https://apps.dev.microsoft.com)에서 앱이 암시적 흐름을 통해 액세스 토큰을 발급할 수 있는지 확인합니다. 이 작업은 `oauth2AllowImplicitFlow` 키를 통해 제어됩니다.
1. 클라이언트 애플리케이션에서 `response_type=id_token`을 통해 id_token을 요청할 때 위에서 만든 Web API의 액세스 토큰(`response_type=token`)도 요청합니다. 따라서 v2.0 엔드포인트를 사용하는 경우 `scope` 매개 변수는 `api://GUID/SCOPE`와 비슷하게 표시되어야 합니다. V1.0 엔드포인트에서 `resource` 매개 변수는 Web API의 앱 URI여야 합니다.
1. Id_token 대신 중간 계층으로 이 액세스 토큰을 전달합니다.  
