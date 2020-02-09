---
title: ADAL에서 MSAL 마이그레이션 가이드 (MSAL4j) | Microsoft
titleSuffix: Microsoft identity platform
description: ADAL (Azure Active Directory Authentication Library) Java 앱을 MSAL (Microsoft Authentication Library)로 마이그레이션하는 방법에 대해 알아봅니다.
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: Java
ms.workload: identity
ms.date: 11/04/2019
ms.author: sagonzal
ms.reviewer: nacanuma, twhitney
ms.custom: aaddev
ms.openlocfilehash: 2d85a3a5d876d731655bb30f7d37a6f42fa5c220
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76696727"
---
# <a name="adal-to-msal-migration-guide-for-java"></a>Java 용 ADAL-MSAL 마이그레이션 가이드

이 문서에서는 MSAL (Microsoft Authentication Library)을 사용 하도록 ADAL (Azure Active Directory Authentication Library)을 사용 하는 앱을 마이그레이션하기 위해 변경 해야 하는 사항을 강조 합니다.

Java 용 Microsoft Authentication Library (MSAL4J) 및 Azure AD 인증 Library for Java (ADAL4J)는 모두 azure ad 엔터티를 인증 하 고 Azure AD에서 토큰을 요청 하는 데 사용 됩니다. 지금까지 대부분의 개발자는 ADAL (Azure AD 인증 라이브러리)을 사용 하 여 토큰을 요청 하 여 Azure AD id (회사 및 학교 계정)를 인증 하기 위해 개발자 플랫폼용 Azure AD (v1.0)로 작업 했습니다.

MSAL은 다음과 같은 이점을 제공 합니다.

- 최신 Microsoft id 플랫폼 엔드포인트을 사용 하기 때문에 azure ad id, Microsoft 계정, 소셜 및 로컬 계정과 같은 광범위 한 Microsoft id 집합을 Azure AD Business to Consumer (B2C)를 통해 인증할 수 있습니다.
- 사용자는 최상의 single sign-on 환경을 이용할 수 있습니다.
- 응용 프로그램에서 증분 동의를 설정할 수 있으며 조건부 액세스를 지 원하는 것이 더 쉽습니다.

Java 용 MSAL은 Microsoft id 플랫폼에서 사용 하는 것이 좋습니다. ADAL4J에는 새로운 기능이 구현 되지 않습니다. 앞으로 진행 되는 모든 작업은 MSAL을 개선 하는 데 중점을 두었습니다.

## <a name="differences"></a>차이점

개발자 용 Azure AD () 엔드포인트 (및 ADAL4J)을 사용 하 여 작업 하는 경우 v2.0 ( [Microsoft identity platform) 엔드포인트에 대 한 다른 사항을](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison)읽을 수 있습니다.

## <a name="scopes-not-resources"></a>리소스가 아닌 범위

ADAL4J는 리소스에 대 한 토큰을 획득 하는 반면, Java 용 MSAL은 범위 토큰을 획득 합니다. Java 클래스에 대 한 다양 한 MSAL에는 범위 매개 변수가 필요 합니다. 이 매개 변수는 필요한 사용 권한 및 요청 된 리소스를 선언 하는 문자열 목록입니다. 예제 범위를 보려면 [Microsoft Graph의 범위](https://docs.microsoft.com/graph/permissions-reference) 를 참조 하세요.

## <a name="core-classes"></a>핵심 클래스

ADAL4J에서 `AuthenticationContext` 클래스는 권한을 통해 STS (보안 토큰 서비스) 또는 권한 부여 서버에 대 한 연결을 나타냅니다. 그러나 Java 용 MSAL은 클라이언트 응용 프로그램을 중심으로 설계 되었습니다. 클라이언트 응용 프로그램을 나타내는 `PublicClientApplication`와 `ConfidentialClientApplication`의 두 가지 별도 클래스를 제공 합니다.  후자 `ConfidentialClientApplication`는 디먼 앱에 대 한 응용 프로그램 식별자와 같은 비밀을 안전 하 게 유지 하기 위해 디자인 된 응용 프로그램을 나타냅니다.

다음 표에서는 ADAL4J 함수가 Java 함수의 새 MSAL에 매핑되는 방법을 보여 줍니다.

| ADAL4J 메서드| MSAL4J 메서드|
|------|-------|
|acquireToken (문자열 리소스, ClientCredential 자격 증명, AuthenticationCallback 콜백) | acquireToken (ClientCredentialParameters)|
|acquireToken (문자열 리소스, ClientAssertion assertion, AuthenticationCallback 콜백)|acquireToken (ClientCredentialParameters)|
|acquireToken (문자열 리소스, AsymmetricKeyCredential credential, AuthenticationCallback 콜백)|acquireToken (ClientCredentialParameters)|
|acquireToken (문자열 리소스, 문자열 clientId, 문자열 사용자 이름, 문자열 암호, AuthenticationCallback 콜백)| acquireToken (UsernamePasswordParameters)|
|acquireToken (문자열 리소스, 문자열 clientId, 문자열 사용자 이름, 문자열 암호 = null, AuthenticationCallback 콜백)|acquireToken (IntegratedWindowsAuthenticationParameters)|
|acquireToken (문자열 리소스, UserAssertion userAssertion, ClientCredential 자격 증명, AuthenticationCallback 콜백)| acquireToken (OnBehalfOfParameters)|
|acquireTokenByAuthorizationCode () | acquireToken (AuthorizationCodeParameters) |
| acquireDeviceCode () 및 acquireTokenByDeviceCode ()| acquireToken (DeviceCodeParameters)|
|acquireTokenByRefreshToken()| acquireTokenSilently(SilentParameters)|

## <a name="iaccount-instead-of-iuser"></a>IUser 대신 IAccount

조작한 사용자를 ADAL4J 합니다. 사용자는 단일 사람이 나 소프트웨어 에이전트를 나타내므로 Microsoft id 시스템에 하나 이상의 계정을 포함할 수 있습니다. 예를 들어 사용자에 게 여러 Azure AD, Azure AD B2C 또는 Microsoft 개인 계정이 있을 수 있습니다.

Java 용 MSAL은 `IAccount` 인터페이스를 통해 계정의 개념을 정의 합니다. 이는 ADAL4J의 주요 변경 내용 이지만 동일한 사용자가 여러 계정을 가질 수 있다는 사실과 다른 Azure AD 디렉터리에 있을 수 있으므로이는 좋은 방법입니다. Java 용 MSAL은 홈 계정 정보를 제공 하기 때문에 게스트 시나리오에서 더 나은 정보를 제공 합니다.

## <a name="cache-persistence"></a>캐시 지속성

ADAL4J는 토큰 캐시를 지원 하지 않습니다.
Java 용 MSAL은 가능 하면 만료 된 토큰을 자동으로 새로 고쳐 토큰 수명 관리를 간소화 하 고 가능한 경우 사용자에 게 자격 증명을 제공 하는 불필요 한 프롬프트를 방지 하기 위해 [토큰 캐시](msal-acquire-cache-tokens.md) 를 추가 합니다.

## <a name="common-authority"></a>Common Authority

V1.0에서 `https://login.microsoftonline.com/common` 기관을 사용 하는 경우 사용자는 모든 조직에 대해 AAD (Azure Active Directory) 계정으로 로그인 할 수 있습니다.

V2.0에서 `https://login.microsoftonline.com/common` 기관을 사용 하는 경우 사용자는 AAD 조직 또는 Microsoft MSA (개인 계정)를 사용 하 여 로그인 할 수 있습니다. Java 용 MSAL에서 AAD 계정에 대 한 로그인을 제한 하려는 경우 `https://login.microsoftonline.com/organizations` 권한 (ADAL4J와 동일한 동작)을 사용 해야 합니다. 권한을 지정 하려면 `PublicClientApplication` 클래스를 만들 때 [Publicclientapplication. Builder](https://javadoc.io/doc/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.Builder.html) 메서드에서 `authority` 매개 변수를 설정 합니다.

## <a name="v10-and-v20-tokens"></a>v1.0 및 v2.0 토큰

v1.0 엔드포인트(ADAL에서 사용)는 v1.0 토큰만 내보냅니다.

V2.0 엔드포인트 (MSAL에서 사용)은 v1.0 및 v2.0 토큰을 내보낼 수 있습니다. Web API의 애플리케이션 매니페스트 속성을 사용하면 개발자가 허용할 토큰 버전을 선택할 수 있습니다. [응용 프로그램 매니페스트](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest) 참조 설명서의 `accessTokenAcceptedVersion`을 참조 하세요.

V1.0 및 v2.0 토큰에 대 한 자세한 내용은 [Azure Active Directory 액세스 토큰](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)을 참조 하세요.

## <a name="adal-to-msal-migration"></a>ADAL에서 MSAL로 마이그레이션

ADAL4J에서 새로 고침 토큰이 노출 되었으며 개발자가이를 캐시할 수 있습니다. 그런 다음 사용자가 더 이상 연결 되어 있지 않을 때 사용자를 대신 하 여 대시보드를 새로 고치는 장기 실행 서비스를 구현 하는 등의 솔루션을 사용 하도록 `AcquireTokenByRefreshToken()`를 사용 합니다.

Java 용 MSAL은 보안상의 이유로 새로 고침 토큰을 노출 하지 않습니다. 대신, MSAL에서 토큰 새로 고침을 처리 합니다.

Java 용 MSAL에는 ADAL4j을 사용 하 여 얻은 새로 고침 토큰을 ClientApplication: [acquireToken (RefreshTokenParameters)](https://javadoc.io/static/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.html#acquireToken-com.microsoft.aad.msal4j.RefreshTokenParameters-)로 마이그레이션할 수 있는 API가 있습니다. 이 메서드를 사용하면 이전에 사용한 새로 고침 토큰을 원하는 범위(리소스)와 함께 제공할 수 있습니다. 새로 고침 토큰은 새 토큰에 대해 교환 되 고 응용 프로그램에서 사용 하기 위해 캐시 됩니다.

다음 코드 조각에서는 기밀 클라이언트 응용 프로그램의 일부 마이그레이션 코드를 보여 줍니다.

```java
String rt = GetCachedRefreshTokenForSIgnedInUser(); // Get refresh token from where you have them stored
Set<String> scopes = Collections.singleton("SCOPE_FOR_REFRESH_TOKEN");

RefreshTokenParameters parameters = RefreshTokenParameters.builder(scopes, rt).build();

PublicClientApplication app = PublicClientApplication.builder(CLIENT_ID) // ClientId for your application
                .authority(AUTHORITY)  //plug in your authority
                .build();

IAuthenticationResult result = app.acquireToken(parameters);
```

`IAuthenticationResult`는 액세스 토큰 및 ID 토큰을 반환 하는 반면 새 새로 고침 토큰은 캐시에 저장 됩니다. 응용 프로그램에는 이제 IAccount 포함 됩니다.

```java
Set<IAccount> accounts =  app.getAccounts().join();
```

현재 캐시에 있는 토큰을 사용 하려면 다음을 호출 합니다.

```java
SilentParameters parameters = SilentParameters.builder(scope, accounts.iterator().next()).build(); 
IAuthenticationResult result = app.acquireToken(parameters);
```
