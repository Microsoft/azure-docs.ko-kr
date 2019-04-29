---
title: Azure AD 인증을 사용하여 .NET으로 Azure Media Services API 액세스 | Microsoft Docs
description: 이 항목에서는 Azure AD(Azure Active Directory) 인증을 사용하여 .NET으로 AMS(Azure Media Services) API에 액세스하는 방법을 보여 줍니다.
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
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 15b986d4e7567be48c582e4a39b727ab110de2be
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61230950"
---
# <a name="use-azure-ad-authentication-to-access-azure-media-services-api-with-net"></a>Azure AD 인증을 사용하여 .NET으로 Azure Media Services API 액세스

windowsazure.mediaservices 4.0.0.4부터는 Azure Media Services에서 Azure AD(Azure Active Directory) 기반 인증을 지원합니다. 이 항목에서는 Azure AD 인증을 사용하여 Microsoft .NET으로 Azure Media Services API에 액세스하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

- Azure 계정. 자세한 내용은 [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요. 
- Media Services 계정. 자세한 내용은 [Azure Portal을 사용하여 Azure Media Services 계정 만들기](media-services-portal-create-account.md)를 참조하세요.
- 최신 [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) 패키지.
- [Azure AD 인증으로 Azure Media Services API 액세스 개요](media-services-use-aad-auth-to-access-ams-api.md) 항목 익히기. 

Azure Media Services와 함께 Azure AD 인증을 사용할 때 다음 두 가지 방법 중 하나로 인증할 수 있습니다.

- **사용자 인증**은 Azure Media Services 리소스와 상호 작용하는 데 앱을 사용하는 사용자를 인증합니다. 대화형 애플리케이션은 먼저 사용자에게 자격 증명을 묻는 메시지를 표시합니다. 예제는 권한 있는 사용자가 인코딩 작업 또는 라이브 스트리밍을 모니터링하기 위해 사용한 관리 콘솔 앱입니다. 
- **서비스 주체 인증**은 서비스를 인증합니다. 이 인증 방법을 일반적으로 사용하는 애플리케이션은 디먼 서비스, 중간 계층 서비스 또는 예약된 작업(예: 웹앱, 함수 앱, 논리 앱, API 또는 마이크로서비스)을 실행하는 앱입니다.

>[!IMPORTANT]
>현재 Azure Media Services는 Azure Access Control Service 인증 모델을 지원합니다. 그러나 Access Control 권한 부여는 2018년 6월 22일부로 더 이상 사용되지 않을 예정입니다. 가능한 빨리 Azure Active Directory 인증 모델로 마이그레이션하는 것이 좋습니다.

## <a name="get-an-azure-ad-access-token"></a>Azure AD 액세스 토큰 가져오기

Azure AD 인증으로 Azure Media Services API에 연결하려면 클라이언트 앱에서 Azure AD 액세스 토큰을 요청해야 합니다. Media Services .NET 클라이언트 SDK를 사용할 경우 Azure AD 액세스 토큰을 확보하는 방법에 대한 대부분의 세부 내용은 [AzureAdTokenProvider](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenProvider.cs) 및 [AzureAdTokenCredentials](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.Authentication/AzureAdTokenCredentials.cs) 클래스에 래핑되어 간소화되어 있습니다. 

예를 들어 Azure AD 인증 기관, Media Services 리소스 URI 또는 원시 Azure AD 애플리케이션 정보를 제공하지 않아도 됩니다. 이들은 Azure AD 액세스 토큰 공급자 클래스에 의해 이미 구성된 잘 알려진 값입니다. 

Azure Media Service .NET SDK를 사용하지 않는 경우 [Azure AD 인증 라이브러리](../../active-directory/develop/active-directory-authentication-libraries.md)를 사용하는 것이 좋습니다. Azure AD 인증 라이브러리와 함께 사용해야 하는 매개 변수 값을 가져오려면 [Azure Portal을 사용하여 Azure AD 인증 설정 액세스](media-services-portal-get-started-with-aad.md)를 참조하세요.

또한 **AzureAdTokenProvider**의 기본 구현을 사용자 고유의 구현으로 바꾸는 옵션도 있습니다.

## <a name="install-and-configure-azure-media-services-net-sdk"></a>Azure Media Services .NET SDK 설치 및 구성

>[!NOTE] 
>Media Services .NET SDK와 함께 Azure AD 인증을 사용하려면 최신 [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) 패키지가 있어야 합니다. 또한 **Microsoft.IdentityModel.Clients.ActiveDirectory** 어셈블리에 참조를 추가합니다. 기존 앱을 사용하고 있는 경우 **Microsoft.WindowsAzure.MediaServices.Client.Common.Authentication.dll** 어셈블리를 포함합니다. 

1. Visual Studio를 사용하여 새 C# 콘솔 애플리케이션을 만듭니다.
2. [windowsazure.mediaservices](https://www.nuget.org/packages/windowsazure.mediaservices) NuGet 패키지를 사용하여 **Azure Media Services .NET SDK**를 설치합니다. 

    NuGet을 사용하여 참조를 추가하려면 다음 단계를 수행합니다. **솔루션 탐색기**에서 마우스 오른쪽 단추로 프로젝트 이름을 클릭한 다음 **NuGet 패키지 관리**를 선택합니다. 그런 다음 **windowsazure.mediaservices**를 검색하고 **설치**를 선택합니다.
    
    또는

    Visual Studio의 **패키지 관리자 콘솔**에서 다음 명령을 실행합니다.

        Install-Package windowsazure.mediaservices -Version 4.0.0.4

3. 소스 코드에 **using**을 추가합니다.

        using Microsoft.WindowsAzure.MediaServices.Client; 

## <a name="use-user-authentication"></a>사용자 인증 사용

사용자 인증 옵션으로 Azure Media Service API에 연결하려면 클라이언트 앱에서 다음 매개 변수를 사용하여 Azure AD 토큰을 요청해야 합니다.  

- Azure AD 테넌트 엔드포인트. Azure Portal에서 테넌트 정보를 검색할 수 있습니다. 오른쪽 위 모서리에서 로그인한 사용자 위로 마우스를 가져갑니다.
- Media Services 리소스 URI.
- Media Services(원시) 애플리케이션 클라이언트 ID. 
- Media Services(원시) 애플리케이션 리디렉션 URI. 

이러한 매개 변수 값은 **AzureEnvironments.AzureCloudEnvironment**에서 확인할 수 있습니다. **AzureEnvironments.AzureCloudEnvironment** 상수는 공용 Azure 데이터 센터에 대해 적절한 환경 변수 설정을 가져오는 .NET SDK의 도우미입니다. 

공용 데이터 센터 전용의 Media Services에 액세스하기 위한 미리 정의된 환경 설정이 포함되어 있습니다. 독립 또는 정부 클라우드 지역의 경우 **AzureChinaCloudEnvironment**, **AzureUsGovernmentEnvironment** 또는 **AzureGermanCloudEnvironment**를 각각 사용할 수 있습니다.

다음 코드 예제에서는 토큰을 만듭니다.
    
    var tokenCredentials = new AzureAdTokenCredentials("microsoft.onmicrosoft.com", AzureEnvironments.AzureCloudEnvironment);
    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
  
서버 컨텍스트를 나타내는 **CloudMediaContext** 인스턴스를 만드는 데 필요한 Media Services에 대한 프로그래밍을 시작합니다. **CloudMediaContext** 에는 작업, 자산, 파일, 액세스 정책 및 로케이터를 비롯하여 중요한 컬렉션에 대한 참조가 포함됩니다. 

또한 **Media REST Services에 대한 리소스 URI**도 **CloudMediaContext** 생성자에 전달해야 합니다. Media REST Services에 대한 리소스 URI를 가져오려면 Azure Portal에 로그인하고 Azure Media Services 계정, **API 액세스**, **Connect to Azure Media Services with user authentication(사용자 인증으로 Azure Media Services에 연결)** 을 차례대로 선택합니다. 

다음 코드 예제는 **CloudMediaContext** 인스턴스를 만듭니다.

    CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);

다음 예제에서는 Azure AD 토큰 및 컨텍스트를 만드는 방법을 보여 줍니다.

    namespace AzureADAuthSample
    {
        class Program
        {
            static void Main(string[] args)
            {
                // Specify your Azure AD tenant domain, for example "microsoft.onmicrosoft.com".
                var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", AzureEnvironments.AzureCloudEnvironment);
    
                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
    
                // Specify your REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".
                CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
    
                var assets = context.Assets;
                foreach (var a in assets)
                {
                    Console.WriteLine(a.Name);
                }
            }
    
        }
    }

>[!NOTE]
>"원격 서버에서 (401) 권한 없음 오류를 반환했습니다"라는 예외가 표시되면 Azure AD 인증을 사용하여 Azure Media Services API 액세스 개요의 [액세스 제어](media-services-use-aad-auth-to-access-ams-api.md#access-control) 섹션을 참조하세요.

## <a name="use-service-principal-authentication"></a>서비스 주체 인증 사용
    
서비스 주체 옵션으로 Azure Media Services API에 연결하려면 중간 계층 앱(웹 API 또는 웹 애플리케이션)에서 다음 매개 변수와 함께 Azure AD 토큰을 요청해야 합니다.  

- Azure AD 테넌트 엔드포인트. Azure Portal에서 테넌트 정보를 검색할 수 있습니다. 오른쪽 위 모서리에서 로그인한 사용자 위로 마우스를 가져갑니다.
- Media Services 리소스 URI.
- Azure AD 애플리케이션 값: **클라이언트 ID** 및 **클라이언트 암호**.

**클라이언트 ID** 및 **클라이언트 암호** 매개 변수 값은 Azure Portal에서 확인할 수 있습니다. 자세한 내용은 [Azure Portal을 사용하여 Azure AD 인증 시작](media-services-portal-get-started-with-aad.md)을 참조하세요.

다음 코드 예제는 매개 변수로 **AzureAdClientSymmetricKey**를 사용하는 **AzureAdTokenCredentials** 생성자를 사용하여 토큰을 만듭니다. 
    
    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"), 
                                AzureEnvironments.AzureCloudEnvironment);

    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

또한 매개 변수로 **AzureAdClientCertificate**를 사용하는 **AzureAdTokenCredentials** 생성자도 지정할 수 있습니다. 

Azure AD에서 사용할 수 있는 형식으로 인증서를 만들고 구성하는 방법에 대한 지침은 [인증서로 디먼 앱에서 Azure AD 인증 - 수동 구성 단계](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/Manual-Configuration-Steps.md)를 참조하세요.

    var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                new AzureAdClientCertificate("{YOUR CLIENT ID HERE}", "{YOUR CLIENT CERTIFICATE THUMBPRINT}"), 
                                AzureEnvironments.AzureCloudEnvironment);

서버 컨텍스트를 나타내는 **CloudMediaContext** 인스턴스를 만드는 데 필요한 Media Services에 대한 프로그래밍을 시작합니다. 또한 **Media REST Services에 대한 리소스 URI**도 **CloudMediaContext** 생성자에 전달해야 합니다. Azure Portal에서도 **Media REST Services에 대한 리소스 URI**를 가져올 수 있습니다.

다음 코드 예제는 **CloudMediaContext** 인스턴스를 만듭니다.

    CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
    
다음 예제에서는 Azure AD 토큰 및 컨텍스트를 만드는 방법을 보여 줍니다.

    namespace AzureADAuthSample
    {
    
        class Program
        {
            static void Main(string[] args)
            {
                var tokenCredentials = new AzureAdTokenCredentials("{YOUR Azure AD TENANT DOMAIN HERE}", 
                                            new AzureAdClientSymmetricKey("{YOUR CLIENT ID HERE}", "{YOUR CLIENT SECRET}"), 
                                            AzureEnvironments.AzureCloudEnvironment);
            
                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);
    
                // Specify your REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".      
                CloudMediaContext context = new CloudMediaContext(new Uri("YOUR REST API ENDPOINT HERE"), tokenProvider);
    
                var assets = context.Assets;
                foreach (var a in assets)
                {
                    Console.WriteLine(a.Name);
                }
    
                Console.ReadLine();
            }
    
        }
    }

## <a name="next-steps"></a>다음 단계

[계정에 파일 업로드](media-services-dotnet-upload-files.md) 시작
