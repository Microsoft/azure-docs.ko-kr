---
title: 공용 클라이언트 앱 인스턴스화 (MSAL.NET) | Microsoft
titleSuffix: Microsoft identity platform
description: .NET 용 Microsoft 인증 라이브러리 (MSAL.NET)를 사용 하 여 구성 옵션으로 공용 클라이언트 응용 프로그램을 인스턴스화하는 방법에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 349ec8c99bc9e6768e5ec2ded346893da95c1636
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88166147"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>MSAL.NET를 사용 하 여 구성 옵션을 사용 하 여 공용 클라이언트 응용 프로그램 인스턴스화

이 문서에서는 .NET 용 Microsoft Authentication Library (MSAL.NET)를 사용 하 여 [공용 클라이언트 응용 프로그램](msal-client-applications.md) 을 인스턴스화하는 방법을 설명 합니다.  응용 프로그램은 설정 파일에 정의 된 구성 옵션을 사용 하 여 인스턴스화됩니다.

응용 프로그램을 초기화 하려면 먼저 앱이 Microsoft id 플랫폼과 통합 될 수 있도록 해당 응용 프로그램을 [등록](quickstart-register-app.md) 해야 합니다. 등록 후에는 다음 정보가 필요할 수 있습니다 (Azure Portal 참조).

- 클라이언트 ID (GUID를 나타내는 문자열)
- 응용 프로그램에 대 한 id 공급자 URL (인스턴스 이름) 및 로그인 대상이 됩니다. 이러한 두 매개 변수를 통칭 하 여 기관 이라고 합니다.
- 조직 전용 lob (단일 테 넌 트 응용 프로그램) 응용 프로그램을 작성 하는 경우 테 넌 트 ID입니다.
- 웹 앱 및 공용 클라이언트 앱의 경우 (특히 앱에서 broker를 사용 해야 하는 경우) id 공급자가 응용 프로그램에 보안 토큰을 다시 연결 하는 redirectUri도 설정 해야 합니다.


.NET Core 콘솔 응용 프로그램의 구성 파일에는 다음 *appsettings.js* 있을 수 있습니다.

```json
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

다음 코드는 .NET 구성 프레임 워크를 사용 하 여이 파일을 읽습니다.

```csharp
public class SampleConfiguration
{
    /// <summary>
    /// Authentication options
    /// </summary>
    public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

    /// <summary>
    /// Base URL for Microsoft Graph (it varies depending on whether the application is ran
    /// in Microsoft Azure public clouds or national / sovereign clouds
    /// </summary>
    public string MicrosoftGraphBaseEndpoint { get; set; }

    /// <summary>
    /// Reads the configuration from a json file
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

        // Read the auth and graph endpoint config
        SampleConfiguration config = new SampleConfiguration()
        {
            PublicClientApplicationOptions = new PublicClientApplicationOptions()
        };
        Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
        config.MicrosoftGraphBaseEndpoint = Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
        return config;
    }
}
```

다음 코드는 설정 파일의 구성을 사용 하 여 응용 프로그램을 만듭니다.

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

