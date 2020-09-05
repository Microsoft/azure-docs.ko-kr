---
title: C#을 사용하여 GA 환경에서 참조 데이터 관리 - Azure Time Series Insights | Microsoft Docs
description: C#에서 작성한 사용자 지정 애플리케이션을 만들어 GA 환경에 대한 참조 데이터를 관리하는 방법을 알아봅니다.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/12/2020
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 2d14d45a6cf7147514cf4fdfc2a3ede470c12835
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89017637"
---
# <a name="manage-reference-data-for-an-azure-time-series-insights-gen-1-environment-using-c-sharp"></a>C #을 사용 하 여 Azure Time Series Insights Gen 1 환경에 대 한 참조 데이터 관리

이 문서에서는 c #, [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)및 Azure Active Directory를 결합 하 여 Azure Time Series Insights Gen 1 [참조 데이터 관리 api](https://docs.microsoft.com/rest/api/time-series-insights/gen1-reference-data-api)에 대 한 프로그래밍 방식 api 요청을 만드는 방법을 보여 줍니다.

> [!TIP]
> [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen1-sample/csharp-tsi-gen1-sample)에서 GA C# 코드 샘플을 봅니다.

## <a name="summary"></a>요약

아래 샘플 코드에서는 다음과 같은 기능을 보여 줍니다.

* [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) **PublicClientApplication**을 사용하여 액세스 토큰 획득
* Gen 1 [참조 데이터 관리 API](https://docs.microsoft.com/rest/api/time-series-insights/gen1-reference-data-api)에 대 한 순차적 생성, 읽기, 업데이트 및 삭제 작업입니다.
* [일반적인 오류 코드](https://docs.microsoft.com/rest/api/time-series-insights/gen1-reference-data-api#validation-and-error-handling)를 포함하는 일반적인 응답 코드

    Reference Data Management API는 각 항목을 개별적으로 처리하며 한 항목의 오류 때문에 다른 항목이 완료되지 못하는 것은 아닙니다. 예를 들어, 요청에 100개 항목이 있고 한 항목에 오류가 있는 경우 99개 항목이 기록되고 하나는 거부됩니다.

## <a name="prerequisites-and-setup"></a>필수 조건 및 설정

샘플 코드를 컴파일 및 실행하기 전에 다음 단계를 완료합니다.

1. [Gen 1 Azure Time Series Insights 환경을 프로 비전](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started
) 합니다.

1. 환경 내에서 [참조 데이터 세트를 만듭니다](time-series-insights-add-reference-data-set.md). 다음 참조 데이터 구성표를 사용합니다.

   | 키 이름 | Type |
   | --- | --- |
   | uuid | String |

1. [인증 및 권한 부여](time-series-insights-authentication-and-authorization.md)에 설명된 대로 Azure Active Directory에 대한 Azure Time Series Insights 환경을 구성합니다. `http://localhost:8080/`을 **리디렉션 URI**로 사용합니다.

1. 필요한 프로젝트 종속성을 설치합니다.

1. 각 **#PLACEHOLDER#** 를 적절 한 환경 식별자로 바꿔서 아래 샘플 코드를 편집합니다.

1. 프로젝트의 루트 디렉터리 내에서 `dotnet run`을 실행합니다. 메시지가 표시되면 사용자 프로필을 사용하여 Azure에 로그인합니다.

## <a name="project-dependencies"></a>프로젝트 종속성

최신 버전의 Visual Studio와 **NETCore.app**을 사용하는 것이 좋습니다.

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) - 버전 16.4.2+
* [NETCore.app](https://www.nuget.org/packages/Microsoft.NETCore.App/2.2.8) - 버전 2.2.8

샘플 코드에는 다음과 같은 두 가지 필수 종속성이 있습니다.

* MSAL.NET [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) -  4.7.1 패키지
* [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json) - 12.0.3 패키지

[NuGet 2.12+](https://www.nuget.org/)를 사용하여 패키지를 추가합니다.

* `dotnet add package Newtonsoft.Json --version 12.0.3`
* `dotnet add package Microsoft.Identity.Client --version 4.7.1`

또는

1. `csharp-tsi-msal-ga-sample.csproj` 파일을 선언합니다.

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

* Gen 1 [참조 데이터 관리 API](https://docs.microsoft.com/rest/api/time-series-insights/gen1-reference-data-api) 참조 설명서를 읽습니다.
