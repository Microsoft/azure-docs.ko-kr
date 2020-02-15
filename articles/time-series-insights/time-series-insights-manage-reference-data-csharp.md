---
title: -Azure Time Series Insights를 사용 하 여 C# GA 환경에서 참조 데이터 관리 Microsoft Docs
description: 에서 C#작성 한 사용자 지정 응용 프로그램을 만들어 GA 환경에 대 한 참조 데이터를 관리 하는 방법을 알아봅니다.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/31/2020
ms.custom: seodec18
ms.openlocfilehash: cf5f89197798f95dced5bfd8817f1df050297048
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76962002"
---
# <a name="manage-ga-reference-data-for-an-azure-time-series-insights-environment-using-c"></a>을 사용 하 여 Azure Time Series Insights 환경의 GA 참조 데이터 관리C#

이 문서에서는 Azure Time Series Insights GA C# [참조 데이터 관리 api](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api)에 대 한 프로그래밍 방식 api 요청을 결합, [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)및 Azure Active Directory 하는 방법을 보여 줍니다.

> [!TIP]
> https://github.com/Azure-Samples/Azure-Time-Series-Insights 에서 C# GA 코드 샘플을 봅니다.

## <a name="summary"></a>요약

아래 샘플 코드에서는 다음과 같은 기능을 보여 줍니다.

* [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) **publicclientapplication**을 사용 하 여 액세스 토큰을 획득 합니다.
* GA [참조 데이터 관리 API](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api)에 대 한 순차적 생성, 읽기, 업데이트 및 삭제 작업입니다.
* 일반적인 [오류 코드](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api#validation-and-error-handling)를 비롯 한 일반적인 응답 코드입니다.
    
    참조 데이터 관리 API는 각 항목을 개별적으로 처리 하 고 하나의 항목을 포함 하는 오류는 다른 항목을 성공적으로 완료 하는 것을 방지 하지 않습니다. 예를 들어 요청에 100 항목이 있고 한 항목에 오류가 있는 경우 99 항목이 기록 되 고 하나는 거부 됩니다.

## <a name="prerequisites-and-setup"></a>필수 구성 요소 및 설치

샘플 코드를 컴파일 및 실행하기 전에 다음 단계를 완료합니다.

1. [GA Azure Time Series Insights 환경을 프로 비전](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started
) 합니다.

1. 환경 내에서 [참조 데이터 집합을 만듭니다](time-series-insights-add-reference-data-set.md) . 다음 참조 데이터 구성표를 사용 합니다.

   | 키 이름 | 유형 |
   | --- | --- |
   | uuid | String | 

1. [인증 및 권한 부여](time-series-insights-authentication-and-authorization.md)에 설명 된 대로 Azure Active Directory에 대 한 Azure Time Series Insights 환경을 구성 합니다. `http://localhost:8080/`를 **리디렉션 URI**로 사용 합니다.

1. 필요한 프로젝트 종속성을 설치 합니다.

1. 각 **#PLACEHOLDER #** 을 적절 한 환경 식별자로 바꿔서 아래 샘플 코드를 편집 합니다.

1. 프로젝트의 루트 디렉터리 내에서 `dotnet run`를 실행 합니다. 메시지가 표시 되 면 사용자 프로필을 사용 하 여 Azure에 로그인 합니다. 

## <a name="project-dependencies"></a>프로젝트 종속성

최신 버전의 Visual Studio 및 Netcore를 사용 하는 것이 좋습니다 **. 앱**:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) -버전 16.4.2 +
* [Netcore. 앱](https://www.nuget.org/packages/Microsoft.NETCore.App/2.2.8) -버전 2.2.8

샘플 코드에는 두 가지 필수 종속성이 있습니다.

* MSAL.NET [-](https://www.nuget.org/packages/Microsoft.Identity.Client/) 4.7.1 패키지를 확인 합니다.
* [Newtonsoft.json](https://www.nuget.org/packages/Newtonsoft.Json) -12.0.3 package.

[NuGet 2.12 +](https://www.nuget.org/)를 사용 하 여 패키지를 추가 합니다.

* `dotnet add package Newtonsoft.Json --version 12.0.3`
* `dotnet add package Microsoft.Identity.Client --version 4.7.1`

또는

1. `csharp-tsi-msal-ga-sample.csproj` 파일을 선언 합니다.

    ```XML
    <Project Sdk="Microsoft.NET.Sdk">
      <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.1</TargetFramework>
        <LangVersion>latest</LangVersion>
        <RootNamespace>csharp-tsi-msal-ga-sample</RootNamespace>
        <RunWorkingDirectory>$(MSBuildThisFileDirectory)</RunWorkingDirectory>
      </PropertyGroup>
      <ItemGroup>
        <PackageReference Include="Microsoft.Identity.Client" Version="4.7.1.0" Culture="neutral" PublicKeyToken="0a613f4dd989e8ae" />
        <PackageReference Include="Newtonsoft.Json" Version="12.0.3" />
      </ItemGroup>
    </Project>
    ```
1. 그런 다음, `dotnet restore`을 실행합니다.

## <a name="c-sample-code"></a>C# 샘플 코드

```csharp
// Copyright (c) Microsoft Corporation.  All rights reserved.

namespace CsharpTsiMsalGaSample
{
    using Microsoft.Identity.Client;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;

    public static class Program
    {
        /**
         * Review the product documentation for detailed configuration steps or skip ahead and configure your environment settings.
         * 
         * https://docs.microsoft.com/azure/time-series-insights/time-series-insights-authentication-and-authorization
         */

        // Azure Time Series Insights environment configuration
        internal static string EnvironmentFqdn = "#PLACEHOLDER#.env.timeseries.azure.com";
        internal static string EnvironmentReferenceDataSetName = "#PLACEHOLDER#";

        // Azure Active Directory application configuration
        internal static string AadClientApplicationId = "#PLACEHOLDER#";
        internal static string[] AadScopes = new string[] { "https://api.timeseries.azure.com//user_impersonation" };
        internal static string AadRedirectUri = "http://localhost:8080/";
        internal static string AadTenantName = "#PLACEHOLDER#";
        internal static string AadAuthenticationAuthority = "https://login.microsoftonline.com/" + AadTenantName + ".onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/";

        private static async Task<string> AcquireAccessTokenAsync()
        {
            if (AadClientApplicationId == "#PLACEHOLDER#" || AadScopes.Length == 0 || AadRedirectUri == "#PLACEHOLDER#" || AadTenantName.StartsWith("#PLACEHOLDER#"))
            {
                throw new Exception($"Use the link {"https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started"} to update the values of 'AadClientApplicationId', 'AadScopes', 'AadRedirectUri', and 'AadAuthenticationAuthority'.");
            }

            /**
             * MSAL.NET configuration. Review the product documentation for more information about MSAL.NET authentication options.
             * 
             * https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/
             */

            IPublicClientApplication app = PublicClientApplicationBuilder
                .Create(AadClientApplicationId)
                .WithRedirectUri(AadRedirectUri)
                .WithAuthority(AadAuthenticationAuthority)
                .Build();

            AuthenticationResult result = await app
                .AcquireTokenInteractive(AadScopes)
                .ExecuteAsync();

            Console.WriteLine("MSAL Authentication Token Acquired: {0}", result.AccessToken);
            Console.WriteLine("");
            return result.AccessToken;
        }

        // System.Net.HttpClient helper to wrap HTTP POST made to the GA Reference Data API
        private static async Task<HttpResponseMessage> AsyncHttpPostRequestHelper(HttpClient httpClient, string input)
        {
             if (EnvironmentFqdn.StartsWith("#PLACEHOLDER#") || EnvironmentReferenceDataSetName == "#PLACEHOLDER#")
             {
                throw new Exception($"Use the link {"https://docs.microsoft.com/azure/time-series-insights/time-series-insights-authentication-and-authorization"} to update the values of 'EnvironmentFqdn' and 'EnvironmentReferenceDataSetName'.");
             }

             Console.WriteLine("HTTP JSON Request Body: {0}", input);
             Console.WriteLine("");
             HttpContent requestBody = new StringContent(input, Encoding.UTF8, "application/json");

             Uri uri = new UriBuilder("https", EnvironmentFqdn)
             {
                Path = $"referencedatasets/{EnvironmentReferenceDataSetName}/$batch",
                Query = "api-version=2016-12-12"
             }.Uri;
                
             Console.WriteLine("Making HTTP POST to URI: {0}", uri);
             Console.WriteLine("");

             HttpResponseMessage response = await httpClient.PostAsync(uri, requestBody);
             if (response.IsSuccessStatusCode)
             {
                var jsonString = await response.Content.ReadAsStringAsync();
                var jsonStringTransferObject = JsonConvert.DeserializeObject<object>(jsonString);
                Console.WriteLine("HTTP JSON Response Body: {0}", jsonStringTransferObject);
                Console.WriteLine("");
                return response;
             }

             return null;
        }

        private static async Task TsiMsalGaSample()
        {
            Console.WriteLine("Beginning demo...");
            Console.WriteLine("");
            Console.WriteLine("The following samples assume a single Key Name (uuid) with String type...");
            Console.WriteLine("");

            string accessToken = await AcquireAccessTokenAsync();
            var httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Add("Authorization", "Bearer " + accessToken);

            {   
                // CREATE reference data
                Console.WriteLine("CREATE reference data example...");
                Console.WriteLine("");
                string createInput = @"
                    {
                        ""put"": [
                            {
                                ""uuid"": ""Fan1""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""color"": ""White"",
                                ""floor"": 2
                            },
                            {
                                ""uuid"": ""Fan3"",
                                ""color"": ""Red"",
                                ""maxSpeed"": 5
                            }
                        ]
                    }";


                var createResponse = await AsyncHttpPostRequestHelper(httpClient, createInput);

                // READ reference data
                Console.WriteLine("READ reference data example...");
                Console.WriteLine("");
                string readInput = @"
                    {
                        ""get"": [
                            {
                                ""uuid"": ""Fan1""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""color"": ""White"",
                                ""desc"": ""example""
                            },
                            {
                                ""uuid"": ""Fan3"",
                                ""color"": ""Red"",
                                ""maxSpeed"": 5
                            }
                        ]
                    }";

                var readResponse = await AsyncHttpPostRequestHelper(httpClient, readInput);

                // UPDATE reference data
                Console.WriteLine("UPDATE reference data example...");
                Console.WriteLine("");
                string updateInput = @"
                    {
                        ""patch"": [
                            {
                                ""uuid"": ""Fan1"",
                                ""color"": ""Red""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""color"": ""Orange""
                            },
                            {
                                ""uuid"": ""Fan3"",
                                ""desc"": ""Example""
                            }
                        ]
                    }";

                var inputResponse = await AsyncHttpPostRequestHelper(httpClient, updateInput);

                // DELETE reference data
                Console.WriteLine("DELETE reference data example...");
                Console.WriteLine("");
                string deleteInput = @"
                    {
                        ""delete"": [
                            {
                                ""uuid"": ""Fan1""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""color"": ""Orange""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""desc"": ""Blue""
                            }
                        ]
                    }";

                var deleteResponse = await AsyncHttpPostRequestHelper(httpClient, deleteInput);
            }
        }

        internal static void Main(string[] args)
        {
            Task.Run(async () => await TsiMsalGaSample()).Wait();
        }
    }
}
```

## <a name="next-steps"></a>다음 단계

- GA [참조 데이터 관리 API](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api) 참조 설명서를 참조 하세요.
