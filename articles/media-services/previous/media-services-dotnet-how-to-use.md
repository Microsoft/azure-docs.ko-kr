---
title: .NET을 사용하여 Media Services 개발을 위한 컴퓨터를 설정하는 방법
description: Media Services .NET SDK를 사용하는 Media Services에 대한 일반적인 필수 구성 요소에 대해 알아봅니다. 또한 Visual Studio 앱을 만드는 방법에 대해서도 알아봅니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: ec2804c7-c656-4fbf-b3e4-3f0f78599a7f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 47db5ba826b94422672dd46b191556da43b70b02
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61465000"
---
# <a name="media-services-development-with-net"></a>.NET을 사용한 Media Services 개발 
[!INCLUDE [media-services-selector-setup](../../../includes/media-services-selector-setup.md)]

이 문서에서는 .NET을 사용하여 Media Services 애플리케이션 개발을 시작하는 방법을 설명합니다.

**Azure Media Services .NET SDK** 라이브러리를 사용하면 .NET을 사용하여 Media Services를 프로그래밍 할 수 있습니다. .NET을 사용한 개발을 더욱 쉽게 도와주는 **Azure Media Services .NET SDK 확장** 라이브러리가 제공 됩니다. 이 라이브러리는 .NET 코드를 단순화하는 일련의 확장 방법 및 도우미 함수를 포함합니다. 두 라이브러리 모두 **NuGet** 및 **GitHub**를 통해 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
* 신규 또는 기존 Azure 구독의 Media Services 계정. [Media Services 계정을 만드는 방법](media-services-portal-create-account.md) 문서를 참조하세요.
* 운영 체제: Windows 10, Windows 7, Windows 2008 R2 또는 Windows 8.
* .NET Framework 4.5 이상
* 있습니다.

## <a name="create-and-configure-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기 및 구성
이 섹션에서는 Visual Studio에서 프로젝트를 생성하고 Media Services 개발에 대해 설정하는 방법을 설명합니다.  이 경우에 프로젝트는 C# Windows 콘솔 애플리케이션이지만 여기에 설명된 설정 방법은 Media Services 애플리케이션에 생성할 수 있는 다른 프로젝트 유형에도 그대로 적용됩니다(예: Windows Forms 애플리케이션 또는 ASP.NET 웹 애플리케이션).

이 섹션에서는 Media Services .NET SDK 확장 추가를 위한 **NuGet** 을 사용하는 방법과 기타 종속된 라이브러리를 보여 줍니다.

또는 GitHub에서 최신 Media Services .NET SDK 비트를 가져오고([github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) 또는 [github.com/Azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)) 솔루션을 빌드하고 클라이언트 프로젝트에 대한 참조를 추가합니다. 필요한 종속성은 모두 자동으로 다운로드되고 추출됩니다.

1. Visual Studio를 사용하여 새 C# 콘솔 애플리케이션을 만듭니다. **이름**, **위치** 및 **솔루션 이름**을 입력하고 확인을 클릭합니다.
2. 솔루션을 빌드하십시오.
3. **NuGet**을 사용하여 **Azure Media Services .NET SDK Extensions**(**windowsazure.mediaservices.extensions**)를 설치한 후 추가합니다. 이 패키지를 설치하면 **Media Services .NET SDK** 도 설치되고 다른 모든 필수 종속성이 추가됩니다.
   
    최신 버전의 NuGet이 설치 되어있는지 확인하십시오. 자세한 내용 및 설치 지침은 [NuGet](https://nuget.codeplex.com/)을 참조하세요.

    1. 솔루션 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.

    2. NuGet 패키지 관리 대화 상자가 나타납니다.

    3. 온라인 갤러리에서 Azure Media Services 확장을 검색하여 **Azure Media Services .NET SDK 확장**(**windowsazure.mediaservices.extensions**)을 선택한 다음 **설치**단추를 클릭합니다.
   
    4. 프로젝트가 수정되고 Media Services .NET SDK 확장, Media Services .NET SDK 및 기타 종속 어셈블리에 대한 참조가 추가됩니다.
4. 클리너 개발 환경의 수준을 올릴 NuGet 패키지 복원을 사용하도록 설정하는 것이 좋습니다. 자세한 내용은 [NuGet 패키지 복원"](https://docs.nuget.org/consume/package-restore)을 참조하세요.
5. **System.Configuration** 어셈블리에 참조를 추가합니다. 이 어셈블리는 구성 파일(예: App.config)에 액세스하는 데 사용되는 System.Configuration.**ConfigurationManager** 클래스를 포함합니다.
   
    1. 참조 관리 대화 상자를 사용하여 참조를 추가하려면 솔루션 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭합니다. **추가**를 클릭한 다음 **참조...** 를 클릭합니다.
   
    2. 참조 관리 대화 상자가 나타납니다.
    3. .NET Framework 어셈블리에서 System.Configuration 어셈블리를 찾아 선택하고 **확인**을 누릅니다.
6. App.config 파일을 열고 파일에 **appSettings** 섹션을 추가합니다. Media Services API에 연결하는 데 필요한 값을 설정합니다. 자세한 내용은 [Azure AD 인증을 사용하여 Azure Media Services API 액세스](media-services-use-aad-auth-to-access-ams-api.md)를 참조하세요. 

    **서비스 사용자** 인증 방법을 사용하여 연결하는 데 필요한 값을 설정합니다.

        ```csharp
                <configuration>
                ...
                    <appSettings>
                        <add key="AMSAADTenantDomain" value="tenant"/>
                        <add key="AMSRESTAPIEndpoint" value="endpoint"/>
                        <add key="AMSClientId" value="id"/>
                        <add key="AMSClientSecret" value="secret"/>
                    </appSettings>
                </configuration>
        ```

7. 프로젝트에 **System.Configuration** 참조를 추가합니다.
8. 다음 코드를 사용하여 Program.cs 파일의 앞부분에 있는 기존 **using** 문을 덮어씁니다.

    ```csharp      
            using System;
            using System.Configuration;
            using System.IO;
            using Microsoft.WindowsAzure.MediaServices.Client;
            using System.Threading;
            using System.Collections.Generic;
            using System.Linq;
    ```

    이제 Media Services 애플리케이션 개발을 시작할 준비가 되었습니다.    

## <a name="example"></a>예

다음은 AMS API에 연결하고 사용 가능한 모든 미디어 프로세서를 나열하는 간단한 예제입니다.

```csharp
        class Program
        {
            // Read values from the App.config file.

            private static readonly string _AADTenantDomain =
                ConfigurationManager.AppSettings["AMSAADTenantDomain"];
            private static readonly string _RESTAPIEndpoint =
                ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
            private static readonly string _AMSClientId =
                ConfigurationManager.AppSettings["AMSClientId"];
            private static readonly string _AMSClientSecret =
                ConfigurationManager.AppSettings["AMSClientSecret"];
        
            private static CloudMediaContext _context = null;
            static void Main(string[] args)
            {
                AzureAdTokenCredentials tokenCredentials = 
                    new AzureAdTokenCredentials(_AADTenantDomain,
                        new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                        AzureEnvironments.AzureCloudEnvironment);

                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

                _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
        
                // List all available Media Processors
                foreach (var mp in _context.MediaProcessors)
                {
                    Console.WriteLine(mp.Name);
                }
        
            }
 ```

## <a name="next-steps"></a>다음 단계

이제 [AMS API에 연결하고](media-services-use-aad-auth-to-access-ams-api.md) [개발](media-services-dotnet-get-started.md)을 시작할 수 있습니다.


## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

