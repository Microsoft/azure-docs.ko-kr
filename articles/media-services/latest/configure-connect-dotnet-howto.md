---
title: .NET-Azure Media Services v3 API에 연결
description: .NET으로 Media Services v3 API에 연결 하는 방법에 알아봅니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2019
ms.author: juliako
ms.openlocfilehash: 8f8a1434af768180e34afcaacd6e92ab402ad8cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736142"
---
# <a name="connect-to-media-services-v3-api---net"></a>.NET으로 Media Services v3 API에 연결

이 아티클에서 Azure Media Services v3.NET SDK에 연결 하는 서비스 보안 주체 로그인 메서드를 사용 하 여 합니다.

## <a name="prerequisites"></a>필수 조건

- [Media Services 계정 만들기](create-account-cli-how-to.md) Media Services 계정 이름과 리소스 그룹 이름을 기억해 두어야
- .NET 개발에 사용 하려는 하는 도구를 설치 합니다. 이 문서의 단계를 사용 하는 방법을 보여 줍니다 [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/)합니다. Visual Studio Code를 사용 하 여 참조 하세요 [사용 C# ](https://code.visualstudio.com/docs/languages/csharp)합니다. 또는 다른 코드 편집기를 사용할 수 있습니다.

## <a name="create-a-console-application"></a>콘솔 애플리케이션 만들기

1. Visual Studio를 시작합니다. 
1. **파일** 메뉴에서 클릭 **새로 만들기** > **프로젝트**합니다. 
1. 만들기는 **.NET Core** 콘솔 응용 프로그램입니다.

이 항목에서는 샘플 앱을 대상으로 `netcoreapp2.0`입니다. 코드에서는 '비동기 기본'부터 사용할 수 있는 C# 7.1 합니다. 이 참조 하세요 [블로그](https://blogs.msdn.microsoft.com/benwilli/2017/12/08/async-main-is-available-but-hidden/) 대 한 자세한 내용은 합니다.

## <a name="add-required-nuget-packages"></a>필요한 NuGet 패키지 추가

1. Visual Studio에서 선택 **도구가** > **NuGet 패키지 관리자** > **NuGet 관리자 콘솔**합니다.
2. 에 **패키지 관리자 콘솔** 창을 사용 하 여 `Install-Package` 다음 NuGet 패키지를 추가 하는 명령입니다. 예: `Install-Package Microsoft.Azure.Management.Media`.

|패키지|설명|
|---|---|
|`Microsoft.Azure.Management.Media`|Azure Media Services SDK. <br/>확인 하려면 최신 Azure Media Services 패키지를 사용 하 고 있는지 검사 하십시오 [Microsoft.Azure.Management.Media](https://www.nuget.org/packages/Microsoft.Azure.Management.Media)합니다.|
|`Microsoft.Rest.ClientRuntime.Azure.Authentication`|NET 용 Azure SDK에 대 한 ADAL 인증 라이브러리|
|`Microsoft.Extensions.Configuration.EnvironmentVariables`|환경 변수 및 로컬 JSON 파일에서 구성 값 읽기|
|`Microsoft.Extensions.Configuration.Json`|환경 변수 및 로컬 JSON 파일에서 구성 값 읽기
|`WindowsAzure.Storage`|Storage SDK|

## <a name="create-and-configure-the-app-settings-file"></a>만들기 및 앱 설정 파일을 구성 합니다.

### <a name="create-appsettingsjson"></a>Appsettings.json 만들기

1. 이동 go **일반적인** > **텍스트 파일**합니다.
1. "Appsettings.json" 이름을 지정 합니다.
1. "변경 된 내용만 복사".json 파일의 "출력 디렉터리로 복사" 속성을 설정 (되도록 응용 프로그램 게시 하는 경우에 액세스할 수)입니다.

### <a name="set-values-in-appsettingsjson"></a>Appsettings.json의 값 설정

실행 합니다 `az ams account sp create` 에 설명 된 대로 명령을 [Api에 액세스](access-api-cli-how-to.md)합니다. 이 명령은 "appsettings.json"에 복사 해야 하는 json을 반환 합니다.
 
## <a name="add-configuration-file"></a>구성 파일 추가

편의 위해 "appsettings.json"의 값을 읽는 역할을 하는 구성 파일을 추가 합니다.

1. 프로젝트에 새.cs 클래스를 추가 합니다. 이름을 `ConfigWrapper`로 지정합니다. 
1. 이 파일에 다음 코드를 붙여 넣습니다 (이 예제에서는 네임 스페이스를 있다고 가정은 `ConsoleApp1`).

```csharp
using System;

using Microsoft.Extensions.Configuration;

namespace ConsoleApp1
{
    public class ConfigWrapper
    {
        private readonly IConfiguration _config;

        public ConfigWrapper(IConfiguration config)
        {
            _config = config;
        }

        public string SubscriptionId
        {
            get { return _config["SubscriptionId"]; }
        }

        public string ResourceGroup
        {
            get { return _config["ResourceGroup"]; }
        }

        public string AccountName
        {
            get { return _config["AccountName"]; }
        }

        public string AadTenantId
        {
            get { return _config["AadTenantId"]; }
        }

        public string AadClientId
        {
            get { return _config["AadClientId"]; }
        }

        public string AadSecret
        {
            get { return _config["AadSecret"]; }
        }

        public Uri ArmAadAudience
        {
            get { return new Uri(_config["ArmAadAudience"]); }
        }

        public Uri AadEndpoint
        {
            get { return new Uri(_config["AadEndpoint"]); }
        }

        public Uri ArmEndpoint
        {
            get { return new Uri(_config["ArmEndpoint"]); }
        }

        public string Region
        {
            get { return _config["Region"]; }
        }
    }
}
```

## <a name="connect-to-the-net-client"></a>.NET 클라이언트에 연결

.NET으로 Media Services API를 사용하려면 **AzureMediaServicesClient** 개체를 만들어야 합니다. 개체를 만들려면 Azure AD를 사용하여 클라이언트가 Azure에 연결하는 데 필요한 자격 증명을 제공해야 합니다. 아래 코드 GetCredentialsAsync 함수는 로컬 구성 파일에 제공 된 자격 증명에 따라 ServiceClientCredentials 개체를 만듭니다.

1. `Program.cs`를 엽니다.
1. 다음 코드를 붙여 넣습니다.

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;

using Microsoft.Azure.Management.Media;
using Microsoft.Azure.Management.Media.Models;
using Microsoft.Extensions.Configuration;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;

namespace ConsoleApp1
{
    class Program
    {
        public static async Task Main(string[] args)
        {
            
            ConfigWrapper config = new ConfigWrapper(new ConfigurationBuilder()
                .SetBasePath(Directory.GetCurrentDirectory())
                .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
                .AddEnvironmentVariables()
                .Build());

            try
            {
                IAzureMediaServicesClient client = await CreateMediaServicesClientAsync(config);
                Console.WriteLine("connected");
            }
            catch (Exception exception)
            {
                if (exception.Source.Contains("ActiveDirectory"))
                {
                    Console.Error.WriteLine("TIP: Make sure that you have filled out the appsettings.json file before running this sample.");
                }

                Console.Error.WriteLine($"{exception.Message}");

                ApiErrorException apiException = exception.GetBaseException() as ApiErrorException;
                if (apiException != null)
                {
                    Console.Error.WriteLine(
                        $"ERROR: API call failed with error code '{apiException.Body.Error.Code}' and message '{apiException.Body.Error.Message}'.");
                }
            }

            Console.WriteLine("Press Enter to continue.");
            Console.ReadLine();
        }
 
        private static async Task<ServiceClientCredentials> GetCredentialsAsync(ConfigWrapper config)
        {
            // Use ApplicationTokenProvider.LoginSilentWithCertificateAsync or UserTokenProvider.LoginSilentAsync to get a token using service principal with certificate
            //// ClientAssertionCertificate
            //// ApplicationTokenProvider.LoginSilentWithCertificateAsync

            // Use ApplicationTokenProvider.LoginSilentAsync to get a token using a service principal with symetric key
            ClientCredential clientCredential = new ClientCredential(config.AadClientId, config.AadSecret);
            return await ApplicationTokenProvider.LoginSilentAsync(config.AadTenantId, clientCredential, ActiveDirectoryServiceSettings.Azure);
        }

        private static async Task<IAzureMediaServicesClient> CreateMediaServicesClientAsync(ConfigWrapper config)
        {
            var credentials = await GetCredentialsAsync(config);

            return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
            {
                SubscriptionId = config.SubscriptionId,
            };
        }

    }
}
```

## <a name="next-steps"></a>다음 단계

- [자습서: 비디오 업로드, 인코딩 및 스트리밍 - .NET](stream-files-tutorial-with-api.md) 
- [자습서: Media Services v3으로 라이브 스트리밍 - .NET](stream-live-tutorial-with-api.md)
- [자습서: Media Services v3으로 비디오 분석 - .NET](analyze-videos-tutorial-with-api.md)
- [로컬 파일에서 작업 입력 만들기 - .NET](job-input-from-local-file-how-to.md)
- [HTTPS URL에서 작업 입력 만들기 - .NET](job-input-from-http-how-to.md)
- [사용자 지정 변환으로 인코딩 - .NET](customize-encoder-presets-how-to.md)
- [AES-128 동적 암호화 및 키 전달 서비스 사용 - .NET](protect-with-aes128.md)
- [DRM 동적 암호화 및 라이선스 배달 서비스 사용 - .NET](protect-with-drm.md)
- [기존 정책에서 서명 키 가져오기 - .NET](get-content-key-policy-dotnet-howto.md)
- [Media Services로 필터 만들기 - .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Media Services v3 및 Azure Functions v2의 고급 비디오 주문형 예제](https://aka.ms/ams3functions)

## <a name="see-also"></a>참고 항목

[.NET 참조](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)
