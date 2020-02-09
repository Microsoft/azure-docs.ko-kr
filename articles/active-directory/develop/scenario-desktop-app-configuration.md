---
title: 웹 Api를 호출 하는 데스크톱 앱 구성-Microsoft identity platform | Microsoft
description: 웹 Api를 호출 하는 데스크톱 앱의 코드를 구성 하는 방법을 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: b2a5a66f2801804b354dd8945ea7d8eb565e82cb
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702218"
---
# <a name="desktop-app-that-calls-web-apis-code-configuration"></a>웹 Api를 호출 하는 데스크톱 앱: 코드 구성

이제 응용 프로그램을 만들었으므로 응용 프로그램 좌표를 사용 하 여 코드를 구성 하는 방법을 배웁니다.

## <a name="microsoft-authentication-libraries"></a>Microsoft 인증 라이브러리

다음 MSALs (Microsoft 인증 라이브러리)는 데스크톱 응용 프로그램을 지원 합니다.

  Microsoft 인증 라이브러리 | Description
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Linux, Windows 및 macOS와 같은 여러 플랫폼에서 데스크톱 응용 프로그램을 빌드할 수 있도록 지원 합니다.
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | 에서는 여러 플랫폼에서 데스크톱 응용 프로그램을 빌드할 수 있습니다.
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | 에서는 여러 플랫폼에서 데스크톱 응용 프로그램을 빌드할 수 있습니다.
  ![MSAL iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL iOS | MacOS 에서만 실행 되는 데스크톱 응용 프로그램을 지원 합니다.

## <a name="public-client-application"></a>공용 클라이언트 응용 프로그램

코드 관점에서 볼 때 데스크톱 응용 프로그램은 공용 클라이언트 응용 프로그램입니다. 이 구성은 대화형 인증을 사용 하는지 여부에 따라 약간 다릅니다.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

MSAL.NET `IPublicClientApplication`를 빌드하고 조작 해야 합니다.

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>코드 전용

다음 코드는 공용 클라이언트 응용 프로그램을 인스턴스화하고 회사 또는 학교 계정 또는 개인 Microsoft 계정를 사용 하 여 Microsoft Azure 공용 클라우드의 사용자에 게 로그인 합니다.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

앞서 살펴본 것 처럼 대화형 인증 또는 장치 코드 흐름을 사용 하려는 경우 `.WithRedirectUri` 한정자를 사용 합니다.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="use-configuration-files"></a>구성 파일 사용

다음 코드는 구성 개체에서 프로그래밍 방식으로 채우거 나 구성 파일에서 읽을 수 있는 공용 클라이언트 응용 프로그램을 인스턴스화합니다.

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>보다 정교한 구성

여러 한정자를 추가 하 여 응용 프로그램 빌드를 정교 하 게 만들 수 있습니다. 예를 들어, 응용 프로그램이 여기에 표시 된 미국 정부와 같이 국가별 클라우드의 다중 테넌트 응용 프로그램을 원하는 경우 다음을 작성할 수 있습니다.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET에는 Active Directory Federation Services 2019에 대 한 한정자도 포함 되어 있습니다.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

마지막으로, Azure Active Directory (Azure AD) B2C 테넌트에 대 한 토큰을 획득 하려는 경우 다음 코드 조각과 같이 테넌트를 지정 합니다.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>자세히 알아보기

MSAL.NET 데스크톱 응용 프로그램을 구성 하는 방법에 대 한 자세한 내용은 다음과 같습니다.

- `PublicClientApplicationBuilder`에서 사용할 수 있는 모든 한정자 목록은 참조 설명서 [Publicclientapplicationbuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)를 참조 하세요.
- `PublicClientApplicationOptions`에서 제공 하는 모든 옵션에 대 한 설명은 참조 설명서의 [Publicclientapplicationoptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions) 를 참조 하세요.

### <a name="complete-example-with-configuration-options"></a>구성 옵션을 사용 하는 전체 예제

다음 `appsettings.json` 구성 파일을 포함 하는 .NET Core 콘솔 응용 프로그램을 가정해 보겠습니다.

```JSon
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

을 사용 하 여이 파일에서 읽을 수 있는 코드가 거의 없습니다. NET 제공 구성 프레임 워크:

```csharp
public class SampleConfiguration
{
 /// <summary>
 /// Authentication options
 /// </summary>
 public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

 /// <summary>
 /// Base URL for Microsoft Graph (it varies depending on whether the application runs
 /// in Microsoft Azure public clouds or national or sovereign clouds)
 /// </summary>
 public string MicrosoftGraphBaseEndpoint { get; set; }

 /// <summary>
 /// Reads the configuration from a JSON file
 /// </summary>
 /// <param name="path">Path to the configuration json file</param>
 /// <returns>SampleConfiguration as read from the json file</returns>
 public static SampleConfiguration ReadFromJsonFile(string path)
 {
  // .NET configuration
  IConfigurationRoot Configuration;
  var builder = new ConfigurationBuilder()
                    .SetBasePath(Directory.GetCurrentDirectory())
                    .AddJsonFile(path);
  Configuration = builder.Build();

  // Read the auth and graph endpoint configuration
  SampleConfiguration config = new SampleConfiguration()
  {
   PublicClientApplicationOptions = new PublicClientApplicationOptions()
  };
  Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
  config.MicrosoftGraphBaseEndpoint =
  Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
  return config;
 }
}
```

이제 응용 프로그램을 만들려면 다음 코드를 작성 합니다.

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

`.Build()` 메서드를 호출 하기 전에 앞에서 볼 수 있는 것 처럼 `.WithXXX` 메서드를 호출 하 여 구성을 재정의할 수 있습니다.

# <a name="javatabjava"></a>[Java](#tab/java)

다음은 샘플을 구성 하기 위해 MSAL Java 개발 샘플에서 사용 되는 클래스입니다. [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java).

```Java
PublicClientApplication app = PublicClientApplication.builder(TestData.PUBLIC_CLIENT_ID)
        .authority(TestData.AUTHORITY_COMMON)
        .build();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="macostabmacos"></a>[MacOS](#tab/macOS)

다음 코드는 공용 클라이언트 응용 프로그램을 인스턴스화하고 회사 또는 학교 계정 또는 개인 Microsoft 계정를 사용 하 여 Microsoft Azure 공용 클라우드의 사용자에 게 로그인 합니다.

### <a name="quick-configuration"></a>빠른 구성

Objective-C:

```objc
NSError *msalError = nil;

MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Swift:
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

### <a name="more-elaborated-configuration"></a>보다 정교한 구성

여러 한정자를 추가 하 여 응용 프로그램 빌드를 정교 하 게 만들 수 있습니다. 예를 들어, 응용 프로그램이 여기에 표시 된 미국 정부와 같이 국가별 클라우드의 다중 테넌트 응용 프로그램을 원하는 경우 다음을 작성할 수 있습니다.

Objective-C:

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];

MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];

NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

Swift:

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```
---

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [데스크톱 앱에 대 한 토큰 획득](scenario-desktop-acquire-token.md)
