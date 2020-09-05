---
title: MSAL.NET client 응용 프로그램 초기화 | Microsoft
titleSuffix: Microsoft identity platform
description: .NET 용 Microsoft 인증 라이브러리 (MSAL.NET)를 사용 하 여 공용 클라이언트 및 기밀 클라이언트 응용 프로그램을 초기화 하는 방법에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/12/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 30f9f1998ee133c2546c9f4de7a99c51feb8740f
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88166198"
---
# <a name="initialize-client-applications-using-msalnet"></a>MSAL.NET를 사용 하 여 클라이언트 응용 프로그램 초기화
이 문서에서는 .NET 용 Microsoft Authentication Library (MSAL.NET)를 사용 하 여 공용 클라이언트 및 기밀 클라이언트 응용 프로그램을 초기화 하는 방법을 설명 합니다.  클라이언트 응용 프로그램 유형 및 응용 프로그램 구성 옵션에 대 한 자세한 내용은 [개요](msal-client-applications.md)를 참조 하세요.

MSAL.NET 3.x를 사용 하는 경우 응용 프로그램 작성기를 사용 하 여 응용 프로그램을 인스턴스화하는 것이 `PublicClientApplicationBuilder` 좋습니다. `ConfidentialClientApplicationBuilder` 코드 또는 구성 파일에서 응용 프로그램을 구성 하는 강력한 메커니즘을 제공 하거나 두 방법을 모두 혼합 하 여 제공 합니다.

## <a name="prerequisites"></a>사전 요구 사항
응용 프로그램을 초기화 하려면 먼저 앱이 Microsoft id 플랫폼과 통합 될 수 있도록 해당 응용 프로그램을 [등록](quickstart-register-app.md) 해야 합니다.  등록 후에는 다음 정보가 필요할 수 있습니다 (Azure Portal 참조).

- 클라이언트 ID (GUID를 나타내는 문자열)
- 응용 프로그램에 대 한 id 공급자 URL (인스턴스 이름) 및 로그인 대상이 됩니다. 이러한 두 매개 변수를 통칭 하 여 기관 이라고 합니다.
- 조직 전용 lob (단일 테 넌 트 응용 프로그램) 응용 프로그램을 작성 하는 경우 테 넌 트 ID입니다.
- 기밀 클라이언트 앱 인 경우 응용 프로그램 암호 (클라이언트 암호 문자열) 또는 인증서 (X509Certificate2 형식)입니다.
- 웹 앱 및 공용 클라이언트 앱의 경우 (특히 앱에서 broker를 사용 해야 하는 경우) id 공급자가 응용 프로그램에 보안 토큰을 다시 연결 하는 redirectUri도 설정 해야 합니다.

## <a name="ways-to-initialize-applications"></a>응용 프로그램 초기화 방법
클라이언트 응용 프로그램을 인스턴스화하는 방법에는 여러 가지가 있습니다.

### <a name="initializing-a-public-client-application-from-code"></a>코드에서 공용 클라이언트 응용 프로그램 초기화

다음 코드는 공용 클라이언트 응용 프로그램, Microsoft Azure 공용 클라우드의 로그인 사용자, 회사 및 학교 계정 또는 개인 Microsoft 계정을 인스턴스화합니다.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-code"></a>코드에서 기밀 클라이언트 응용 프로그램 초기화

동일한 방식으로 다음 코드는 `https://myapp.azurewebsites.net` Microsoft Azure 공용 클라우드의 사용자에서 작업 및 학교 계정 또는 개인 Microsoft 계정으로 토큰을 처리 하는 기밀 응용 프로그램 (에 있는 웹 앱)을 인스턴스화합니다. 응용 프로그램은 클라이언트 암호를 공유 하 여 id 공급자로 식별 됩니다.

```csharp
string redirectUri = "https://myapp.azurewebsites.net";
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithClientSecret(clientSecret)
    .WithRedirectUri(redirectUri )
    .Build();
```

프로덕션 환경에서는 클라이언트 암호를 사용 하는 대신 Azure AD 인증서를 사용 하 여 공유 하는 것이 좋습니다. 그러면 코드가 다음과 같이 됩니다.

```csharp
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithCertificate(certificate)
    .WithRedirectUri(redirectUri )
    .Build();
```

### <a name="initializing-a-public-client-application-from-configuration-options"></a>구성 옵션에서 공용 클라이언트 응용 프로그램 초기화

다음 코드는 구성 개체에서 공용 클라이언트 응용 프로그램을 인스턴스화하고 구성 파일에서 프로그래밍 방식으로 채우거 나 읽을 수 있습니다.

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-configuration-options"></a>구성 옵션에서 기밀 클라이언트 응용 프로그램 초기화

기밀 클라이언트 응용 프로그램에는 동일한 종류의 패턴이 적용 됩니다. `.WithXXX`한정자 (여기서는 인증서)를 사용 하 여 다른 매개 변수를 추가할 수도 있습니다.

```csharp
ConfidentialClientApplicationOptions options = GetOptions(); // your own method
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(options)
    .WithCertificate(certificate)
    .Build();
```

## <a name="builder-modifiers"></a>작성기 한정자

응용 프로그램 빌더를 사용 하는 코드 조각에서는 여러 `.With` 메서드를 한정자로 적용할 수 있습니다 (예: `.WithCertificate` 및 `.WithRedirectUri` ). 

### <a name="modifiers-common-to-public-and-confidential-client-applications"></a>공용 및 기밀 클라이언트 응용 프로그램에 공통 되는 한정자

공용 클라이언트나 기밀 클라이언트 응용 프로그램 작성기에서 설정할 수 있는 한정자는 다음과 같습니다.

|한정자 | Description|
|--------- | --------- |
|`.WithAuthority()`7 재정의 | Azure 클라우드, 대상 그룹, 테 넌 트 (테 넌 트 ID 또는 도메인 이름)를 선택 하거나 직접 인증 기관 URI를 제공 하 여 응용 프로그램 기본 기관을 Azure AD 기관에 설정 합니다.|
|`.WithAdfsAuthority(string)` | 응용 프로그램 기본 기관을 ADFS 기관으로 설정 합니다.|
|`.WithB2CAuthority(string)` | 응용 프로그램 기본 기관을 Azure AD B2C 기관으로 설정 합니다.|
|`.WithClientId(string)` | 클라이언트 ID를 재정의 합니다.|
|`.WithComponent(string)` | 원격 분석을 위해 MSAL.NET를 사용 하 여 라이브러리의 이름을 설정 합니다. |
|`.WithDebugLoggingCallback()` | 호출 되는 경우 응용 프로그램은 `Debug.Write` 단순히 디버깅 추적을 사용 하도록 설정 합니다. 자세한 내용은 [로깅](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging)을 참조하세요.|
|`.WithExtraQueryParameters(IDictionary<string,string> eqp)` | 모든 인증 요청에서 전송 될 응용 프로그램 수준 추가 쿼리 매개 변수를 설정 합니다. 이는 동일한를 사용 하 여 각 토큰 획득 방법 수준에서 재정의할 수 `.WithExtraQueryParameters pattern` 있습니다.|
|`.WithHttpClientFactory(IMsalHttpClientFactory httpClientFactory)` | HTTP 프록시를 구성 하는 등의 고급 시나리오를 사용 하도록 설정 하거나 MSAL이 특정 HttpClient (ASP.NET Core web apps/Api)를 사용 하도록 강제 합니다.|
|`.WithLogging()` | 호출 되는 경우 응용 프로그램은 디버깅 추적과 함께 콜백을 호출 합니다. 자세한 내용은 [로깅](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging)을 참조하세요.|
|`.WithRedirectUri(string redirectUri)` | 기본 리디렉션 URI를 재정의 합니다. 공용 클라이언트 응용 프로그램의 경우 broker를 포함 하는 시나리오에 유용 합니다.|
|`.WithTelemetry(TelemetryCallback telemetryCallback)` | 원격 분석을 보내는 데 사용 되는 대리자를 설정 합니다.|
|`.WithTenantId(string tenantId)` | 테 넌 트 ID 또는 테 넌 트 설명을 재정의 합니다.|

### <a name="modifiers-specific-to-xamarinios-applications"></a>Xamarin.ios 응용 프로그램에만 적용 되는 한정자

Xamarin.ios에서 공용 클라이언트 응용 프로그램 작성기에 대해 설정할 수 있는 한정자는 다음과 같습니다.

|한정자 | Description|
|--------- | --------- |
|`.WithIosKeychainSecurityGroup()` | **Xamarin.ios만**해당: io 키 체인 보안 그룹 (캐시 지 속성)을 설정 합니다.|

### <a name="modifiers-specific-to-confidential-client-applications"></a>기밀 클라이언트 응용 프로그램에만 적용 되는 한정자

기밀 클라이언트 응용 프로그램 빌더에 대해 설정할 수 있는 한정자는 다음과 같습니다.

|한정자 | Description|
|--------- | --------- |
|`.WithCertificate(X509Certificate2 certificate)` | Azure AD를 사용 하 여 응용 프로그램을 식별 하는 인증서를 설정 합니다.|
|`.WithClientSecret(string clientSecret)` | Azure AD를 사용 하 여 응용 프로그램을 식별 하는 클라이언트 암호 (앱 암호)를 설정 합니다.|

이러한 한정자는 함께 사용할 수 없습니다. 둘 다 제공 하는 경우 MSAL은 의미 있는 예외를 throw 합니다.

### <a name="example-of-usage-of-modifiers"></a>한정자 사용 예제

응용 프로그램이 조직에만 사용 되는 lob (기간 업무) 응용 프로그램 이라고 가정 합니다.  그러면 다음을 작성할 수 있습니다.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzurePublic, tenantId)
        .Build();
```

흥미로운 부분은 이제 국가별 클라우드의 프로그래밍이 간소화 되었습니다. 응용 프로그램이 국가별 클라우드의 다중 테 넌 트 응용 프로그램이 되도록 하려면 다음과 같이 작성할 수 있습니다.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment, AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

ADFS에 대 한 재정의도 있습니다 (ADFS 2019은 현재 지원 되지 않음).
```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

마지막으로 Azure AD B2C 개발자 인 경우 다음과 같이 테 넌 트를 지정할 수 있습니다.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```
