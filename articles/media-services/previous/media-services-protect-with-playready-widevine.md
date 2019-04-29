---
title: PlayReady 및/또는 Widevine 동적 일반 암호화 사용 | Microsoft Docs
description: Azure Media Services를 사용하여 Microsoft PlayReady DRM으로 보호되는 MPEG-DASH, 부드러운 스트리밍 및 HLS(HTTP-Live-Streaming) 스트림을 배달할 수 있습니다. 또한 이를 사용하여 Widevine DRM으로 암호화된 DASH를 배달할 수도 있습니다. 이 항목에서는 PlayReady 및 Widevine DRM으로 동적으로 암호화하는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 548d1a12-e2cb-45fe-9307-4ec0320567a2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: df967c56d84650894d2e07054e9ec8d6f830192b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60711749"
---
# <a name="use-playready-andor-widevine-dynamic-common-encryption"></a>PlayReady 및/또는 Widevine 동적 일반 암호화 사용

> [!div class="op_single_selector"]
> * [.NET](media-services-protect-with-playready-widevine.md)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
>

> [!NOTE]
> 최신 버전의 Java SDK를 가져와서 Java를 사용하여 개발을 시작하려면 [Media Services용 Java 클라이언트 SDK 시작](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use)을 참조하세요. <br/>
> Media Services용 최신 PHP SDK를 다운로드하려면 [Packagist 리포지토리](https://packagist.org/packages/microsoft/windowsazure#v0.5.7)에서 Microsoft/WindowAzure 패키지 버전 0.5.7을 찾습니다. 

## <a name="overview"></a>개요

 Media Services를 사용하여 [PlayReady DRM(디지털 권한 관리)](https://www.microsoft.com/playready/overview/)으로 보호되는 MPEG-DASH, 부드러운 스트리밍 및 HLS(HTTP Live Streaming) 스트림을 배달할 수 있습니다. 또한 Widevine DRM 라이선스로 암호화된 DASH 스트림을 배달할 수도 있습니다. PlayReady와 Widevine은 모두 일반 암호화(ISO/IEC 23001-7 CENC) 사양에 따라 암호화됩니다. [Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/)(버전 3.5.1 이상) 또는 REST API를 통해 Widevine을 사용하도록 AssetDeliveryConfiguration을 구성할 수 있습니다.

Media Services는 PlayReady와 Widevine DRM 라이선스를 제공하는 서비스를 제공합니다. 또한 Media Services는 사용자가 보호된 콘텐츠를 재생할 때 PlayReady 또는 Widevine DRM 런타임에서 적용할 권한 및 제한을 구성하는 데 사용할 수 있는 API를 제공합니다. 사용자가 DRM으로 보호된 콘텐츠를 요청하면 플레이어 애플리케이션이 Media Services 라이선스 서비스에서 라이선스를 요청합니다. 플레이어 애플리케이션에 권한이 있으면 Media Services 라이선스 서비스에서 플레이어에 라이선스를 발급합니다. PlayReady 또는 Widevine 라이선스에는 클라이언트 플레이어가 콘텐츠를 해독하고 스트림하는 데 사용할 수 있는 해독 키가 들어 있습니다.

또한 다음 Media Services 파트너를 사용하여 Widevine 라이선스를 제공할 수 있습니다. 

* [Axinom](https://www.axinom.com/press/ibc-axinom-drm-6/) 
* [EZDRM](https://ezdrm.com/) 
* [castLabs](https://castlabs.com/company/partners/azure/) 

자세한 내용은 [Axinom](media-services-axinom-integration.md) 및 [castLabs](media-services-castlabs-integration.md)와의 통합을 참조하세요.

Media Services는 키를 요청 하는 사용자에 권한을 부여하는 여러 방법을 지원합니다. 콘텐츠 키 인증 정책에는 하나 이상의 권한 부여 제한(열기 또는 토큰 제한)이 있을 수 있습니다. 토큰 제한 정책에는 STS(보안 토큰 서비스)에서 발급한 토큰이 수반되어야 합니다. Media Services는 [SWT(단순 웹 토큰)](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) 형식 및 [JWT(JSON Web Token)](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) 형식의 토큰을 지원합니다. 

자세한 내용은 [콘텐츠 키의 인증 정책 구성](media-services-protect-with-aes128.md#configure_key_auth_policy)을 참조하세요.

동적 암호화를 이용하려면 다중 비트 전송률 MP4 파일 또는 다중 비트 전송률 부드러운 스트리밍 원본 파일의 집합이 포함된 자산이 필요합니다. 또한 자산의 배달 정책을 구성해야 합니다(이 항목의 뒷부분에서 설명). 그런 다음 스트리밍 URL에 지정된 형식에 따라 주문형 스트리밍 서버는 사용자가 선택한 프로토콜로 스트림이 배달되도록 합니다. 따라서 단일 저장소 형식으로만 파일을 저장하고 이에 대한 비용을 지불합니다. Media Services는 클라이언트의 각 요청에 따라 적절한 HTTP 응답을 작성하고 제공합니다.

이 문서는 PlayReady 및 Widevine과 같이 여러 DRM으로 보호된 미디어를 제공하는 애플리케이션에서 작업하는 개발자에게 유용합니다. 이 문서에서는 권한이 있는 클라이언트만 PlayReady 또는 Widevine 라이선스를 받을 수 있도록 권한 부여 정책을 사용하여 PlayReady 라이선스 배달 서비스를 구성하는 방법을 보여 줍니다. 또한 DASH에 대해 PlayReady 또는 Widevine DRM으로 동적 암호화를 사용하는 방법을 보여줍니다.

>[!NOTE]
>Azure Media Services 계정이 만들어지면 기본 스트리밍 엔드포인트가 “중지됨” 상태에 있는 계정에 추가됩니다. 콘텐츠 스트리밍을 시작하고 동적 패키징 및 동적 암호화를 활용하려면, 콘텐츠를 스트리밍하려는 스트리밍 엔드포인트가 "실행 중" 상태에 있어야 합니다. 

## <a name="download-the-sample"></a>샘플 다운로드
이 문서에서 설명하는 샘플은 [GitHub의 Azure 샘플](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm)에서 다운로드할 수 있습니다.

## <a name="configure-dynamic-common-encryption-and-drm-license-delivery-services"></a>동적 일반 암호화 및 DRM 라이선스 배달 서비스 구성

Media Services 라이선스 배달 서비스를 사용하거나 동적 암호화도 사용하여 PlayReady로 자산을 보호하는 경우 다음 일반 단계를 수행합니다.

1. 자산을 만들고 이 자산에 파일을 업로드합니다.

2. 파일이 포함된 자산을 적응 비트 전송률 MP4 집합으로 인코딩합니다.

3. 콘텐츠 키를 만들고 인코딩된 자산과 연결합니다. Media Services에서 콘텐츠 키에는 자산의 암호화 키가 들어 있습니다.

4. 콘텐츠 키의 인증 정책을 구성합니다. 콘텐츠 키 인증 정책을 구성해야 합니다. 콘텐츠 키가 클라이언트에 배달되려면 먼저 클라이언트에서 정책을 충족해야 합니다.

    콘텐츠 키 인증 정책을 만드는 경우 배달 방법(PlayReady 또는 Widevine) 및 제한(열기 또는 토큰)을 지정해야 합니다. 또한 키가 클라이언트에 배달되는 방식([PlayReady](media-services-playready-license-template-overview.md) 또는 [Widevine](media-services-widevine-license-template-overview.md) 라이선스 템플릿)을 정의하는 키 배달 유형과 관련된 정보를 지정해야 합니다.

5. 자산에 대한 배달 정책을 구성합니다. 배달 정책 구성에는 배달 프로토콜(예: MPEG-DASH, HLS, 부드러운 스트리밍 또는 모두)이 포함됩니다. 또한 구성에는 동적 암호화 종류(예: 일반 암호화)과 PlayReady 또는 Widevine 라이선스 취득 URL도 포함됩니다.

    동일한 자산의 각 프로토콜에 다른 정책을 적용할 수 있습니다. 예를 들어 PlayReady 암호화는 Smooth/DASH에 적용하고, AES 봉투(envelope)는 HLS에 적용할 수 있습니다. 배달 정책에 정의되지 않은 모든 프로토콜(예: HLS만 프로토콜로 지정한 단일 정책을 추가하는 경우)은 스트리밍에서 차단됩니다. 자산 배달 정책이 전혀 정의되어 있지 않은 경우는 예외입니다. 이렇게 하면 모든 프로토콜이 허용됩니다.

6. 스트리밍 URL을 얻기 위해 주문형 로케이터를 만듭니다.

이 문서의 끝부분에서 전체 .NET 예제가 나와 있습니다.

다음 이미지에서는 앞에서 설명한 워크플로를 보여 줍니다. 여기서는 인증에 토큰을 사용합니다.

![PlayReady로 보호](media/media-services-content-protection-overview/media-services-content-protection-with-drm.png)

이 문서의 나머지 부분에서는 앞에서 설명한 작업을 수행하는 방법을 보여 주는 자세한 설명, 코드 예제 및 항목에 대한 링크를 제공합니다.

## <a name="current-limitations"></a>현재 제한 사항
자산 배달 정책을 추가하거나 업데이트하는 경우 연결된 모든 로케이터를 삭제하고 새 로케이터를 만들어야 합니다.

현재 Media Services에서 Widevine을 사용하여 암호화하는 경우 여러 콘텐츠 키가 지원되지 않습니다. 

## <a name="create-an-asset-and-upload-files-into-the-asset"></a>자산 만들기 및 파일을 자산에 업로드
비디오를 관리, 인코딩 및 스트리밍하려면 먼저 콘텐츠를 Media Services에 업로드해야 합니다. 업로드되면 이후의 처리 및 스트리밍을 위해 콘텐츠가 클라우드에 안전하게 저장됩니다.

자세한 내용은 [Media Services 계정에 파일 업로드](media-services-dotnet-upload-files.md)를 참조하세요.

## <a name="encode-the-asset-that-contains-the-file-to-the-adaptive-bitrate-mp4-set"></a>파일이 포함된 자산을 적응 비트 전송률 MP4 집합으로 인코딩합니다.
동적 암호화를 사용하면 다중 비트 전송률 MP4 파일 또는 다중 비트 전송률 부드러운 스트리밍 원본 파일의 집합이 포함된 자산을 만들 수 있습니다. 다음으로 매니페스트 또는 조각 요청의 지정된 형식에 따라 주문형 스트리밍 서버는 선택한 프로토콜에서 스트림을 받을 수 있도록 합니다. 그런 다음 단일 저장소 형식으로만 파일을 저장하고 이에 대한 비용을 지불합니다. Media Services는 클라이언트의 요청에 따라 적절한 응답을 작성하고 제공합니다. 자세한 내용은 [동적 패키징 개요](media-services-dynamic-packaging-overview.md)를 참조하세요.

인코딩하는 방법에 관한 지침은 [Media Encoder Standard으로 자산 인코딩](media-services-dotnet-encode-with-media-encoder-standard.md)을 참조하세요.

## <a id="create_contentkey"></a>콘텐츠 키를 만들어 인코딩된 자산에 연결
Media Services에서 콘텐츠 키에는 자산을 암호화할 키가 들어 있습니다.

자세한 내용은 [콘텐츠 키 만들기](media-services-dotnet-create-contentkey.md)를 참조하세요.

## <a id="configure_key_auth_policy"></a>콘텐츠 키의 인증 정책 구성
Media Services는 키를 요청 하는 사용자를 인증 하는 여러 방법을 지원합니다. 콘텐츠 키 인증 정책을 구성해야 합니다. 키가 클라이언트에 배달되려면 먼저 클라이언트(플레이어)에서 정책을 충족해야 합니다. 콘텐츠 키 인증 정책에는 하나 이상의 권한 부여 제한(열기 또는 토큰 제한)이 있을 수 있습니다.

자세한 내용은 [콘텐츠 키 인증 정책 구성](media-services-dotnet-configure-content-key-auth-policy.md#playready-dynamic-encryption)을 참조하세요.

## <a id="configure_asset_delivery_policy"></a>자산 배달 정책 구성
자산에 대한 배달 정책을 구성합니다. 자산 배달 정책 구성에 포함되는 몇 가지 항목은 다음과 같습니다.

* DRM 라이선스 획득 URL.
* 자산 배달 프로토콜(예: MPEG DASH, HLS, 부드러운 스트리밍 또는 모두)
* 동적 암호화 형식(이 경우 일반 암호화)

자세한 내용은 [자산 배달 정책 구성](media-services-dotnet-configure-asset-delivery-policy.md)을 참조하세요.

## <a id="create_locator"></a>스트리밍 URL을 얻기 위해 주문형 스트리밍 로케이터 만들기
사용자에게 부드러운 스트리밍, DASH 또는 HLS에 대한 스트리밍 URL을 제공해야 합니다.

> [!NOTE]
> 자산 배달 정책을 추가하거나 업데이트하는 경우, 기존 로케이터를 삭제하고 새 로케이터를 만들어야 합니다.
>
>

자산을 게시하고 스트리밍 URL을 작성하는 방법은 [스트리밍 URL 작성](media-services-deliver-streaming-content.md)을 참조하세요.

## <a name="get-a-test-token"></a>테스트 토큰 가져오기
키 권한 부여 정책에 사용된 토큰 제한에 따라 테스트 토큰을 가져옵니다.

```csharp
// Deserializes a string containing an XML representation of a TokenRestrictionTemplate
// back into a TokenRestrictionTemplate class instance.
TokenRestrictionTemplate tokenTemplate =
TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

// Generate a test token based on the data in the given TokenRestrictionTemplate.
//The GenerateTestToken method returns the token without the word "Bearer" in front,
//so you have to add it in front of the token string.
string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
```

[Azure Media Services 플레이어](https://amsplayer.azurewebsites.net/azuremediaplayer.html)를 사용하여 스트림을 테스트할 수 있습니다.

## <a name="create-and-configure-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기 및 구성

1. 개발 환경을 설정하고 [.NET을 사용한 Media Services 환경](media-services-dotnet-how-to-use.md)에 설명된 대로 연결 정보를 사용하여 app.config 파일을 채웁니다.

2. 다음 요소를 app.config 파일에 정의된 **appSettings**에 추가합니다.

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

## <a name="example"></a>예

다음 샘플에서는 .NET용 Media Services SDK 버전 3.5.2에 도입된 기능을 보여 줍니다. (특히 Widevine 라이선스 템플릿을 정의하고 Media Services에서 Widevine 라이선스를 요청할 수 있는 기능이 포함되어 있습니다.)

Program.cs 파일에 있는 코드를 이 섹션에 나와 있는 코드로 덮어씁니다.

>[!NOTE]
>다양한 Media Services 정책(예: 로케이터 정책 또는 ContentKeyAuthorizationPolicy의 경우)에 대해 1백만 개 정책으로 제한됩니다. 항상 동일한 요일/액세스 권한을 사용하는 경우 동일한 정책 ID를 사용합니다. 예를 들어 오랜 시간 동안 유지하려는 로케이터에 대한 정책(비업로드 정책)이 있습니다. 

자세한 내용은 [Media Services .NET SDK를 사용하여 자산 및 관련 엔터티 관리](media-services-dotnet-manage-entities.md#limit-access-policies)를 참조하세요.

입력 파일이 있는 폴더를 가리키도록 변수를 업데이트해야 합니다.

```csharp
using System;
using System.Collections.Generic;
using System.Configuration;
using System.IO;
using System.Linq;
using System.Threading;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
using Microsoft.WindowsAzure.MediaServices.Client.Widevine;
using Newtonsoft.Json;

namespace DynamicEncryptionWithDRM
{
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

        private static readonly Uri _sampleIssuer =
            new Uri(ConfigurationManager.AppSettings["Issuer"]);
        private static readonly Uri _sampleAudience =
            new Uri(ConfigurationManager.AppSettings["Audience"]);

        // Field for service context.
        private static CloudMediaContext _context = null;

        private static readonly string _mediaFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            bool tokenRestriction = false;
            string tokenTemplateString = null;

            IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
            Console.WriteLine("Uploaded asset: {0}", asset.Id);

            IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
            Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);

            IContentKey key = CreateCommonTypeContentKey(encodedAsset);
            Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
            Console.WriteLine("PlayReady License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense));
            Console.WriteLine();

            if (tokenRestriction)
                tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
            else
                AddOpenAuthorizationPolicy(key);

            Console.WriteLine("Added authorization policy: {0}", key.AuthorizationPolicyId);
            Console.WriteLine();

            CreateAssetDeliveryPolicy(encodedAsset, key);
            Console.WriteLine("Created asset delivery policy. \n");
            Console.WriteLine();

            if (tokenRestriction && !String.IsNullOrEmpty(tokenTemplateString))
            {
                // Deserializes a string containing an XML representation of a TokenRestrictionTemplate
                // back into a TokenRestrictionTemplate class instance.
                TokenRestrictionTemplate tokenTemplate =
                    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

                // Generate a test token based on the data in the given TokenRestrictionTemplate.
                // Note that you need to pass the key ID GUID because 
                // TokenClaim.ContentKeyIdentifierClaim was specified during the creation of TokenRestrictionTemplate.
                Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
                string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey,
                                            DateTime.UtcNow.AddDays(365));
                Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                Console.WriteLine();
            }

            // You can use the https://amsplayer.azurewebsites.net/azuremediaplayer.html player to test streams.
            // Note that DASH works on Internet Explorer 11 (via PlayReady), Microsoft Edge (via PlayReady), and Chrome (via Widevine).

            string url = GetStreamingOriginLocator(encodedAsset);
            Console.WriteLine("Encrypted DASH URL: {0}/manifest(format=mpd-time-csf)", url);

            Console.ReadLine();
        }

        static public IAsset UploadFileAndCreateAsset(string singleFilePath)
        {
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.None);

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return inputAsset;
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
        {
            var encodingPreset = "Adaptive Streaming";

            IJob job = _context.Jobs.Create(String.Format("Encoding into Mp4 {0} to {1}",
                        inputAsset.Name,
                        encodingPreset));

            var mediaProcessors =
            _context.MediaProcessors.Where(p => p.Name.Contains("Media Encoder Standard")).ToList();

            var latestMediaProcessor =
            mediaProcessors.OrderBy(mp => new Version(mp.Version)).LastOrDefault();

            ITask encodeTask = job.Tasks.AddNew("Encoding", latestMediaProcessor, encodingPreset, TaskOptions.None);
            encodeTask.InputAssets.Add(inputAsset);
            encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset), AssetCreationOptions.StorageEncrypted);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }


        static public IContentKey CreateCommonTypeContentKey(IAsset asset)
        {

            Guid keyId = Guid.NewGuid();
            byte[] contentKey = GetRandomBuffer(16);

            IContentKey key = _context.ContentKeys.Create(
                        keyId,
                        contentKey,
                        "ContentKey",
                        ContentKeyType.CommonEncryption);

            // Associate the key with the asset.
            asset.ContentKeys.Add(key);

            return key;
        }

        static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
        {

            // Create ContentKeyAuthorizationPolicy with open restrictions
            // and create an authorization policy.         

            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                {
                new ContentKeyAuthorizationPolicyRestriction
                {
                    Name = "Open",
                    KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                    Requirements = null
                }
                };

            // Configure PlayReady and Widevine license templates.
            string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

            string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();

            IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
                ContentKeyDeliveryType.PlayReadyLicense,
                restrictions, PlayReadyLicenseTemplate);

            IContentKeyAuthorizationPolicyOption WidevinePolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
                ContentKeyDeliveryType.Widevine,
                restrictions, WidevineLicenseTemplate);

            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common Content Key with no restrictions").
                Result;


            contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
            contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);
            // Associate the content key authorization policy with the content key.
            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
            contentKey = contentKey.UpdateAsync().Result;
        }

        public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
        {
            string tokenTemplateString = GenerateTokenRequirements();

            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                {
                new ContentKeyAuthorizationPolicyRestriction
                {
                    Name = "Token Authorization Policy",
                    KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                    Requirements = tokenTemplateString,
                }
                };

            // Configure PlayReady and Widevine license templates.
            string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

            string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();

            IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                ContentKeyDeliveryType.PlayReadyLicense,
                restrictions, PlayReadyLicenseTemplate);

            IContentKeyAuthorizationPolicyOption WidevinePolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                ContentKeyDeliveryType.Widevine,
                restrictions, WidevineLicenseTemplate);

            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common Content Key with token restrictions").
                Result;

            contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
            contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);

            // Associate the content key authorization policy with the content key.
            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
            contentKey = contentKey.UpdateAsync().Result;

            return tokenTemplateString;
        }

        static private string GenerateTokenRequirements()
        {
            TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

            template.PrimaryVerificationKey = new SymmetricVerificationKey();
            template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();
            template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

            return TokenRestrictionTemplateSerializer.Serialize(template);
        }

        static private string ConfigurePlayReadyLicenseTemplate()
        {
            // The following code configures the PlayReady license template by using .NET classes
            // and returns the XML string.

            //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user.
            //It contains a field for a custom data string between the license server and the application
            //(which might be useful for custom app logic) as well as a list of one or more license templates.
            PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();

            // The PlayReadyLicenseTemplate class represents a license template you can use to create PlayReady licenses
            // to be returned to end users.
            //It contains the data on the content key in the license and any rights or restrictions to be
            //enforced by the PlayReady DRM runtime when you use the content key.
            PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
            //Configure whether the license is persistent (saved in persistent storage on the client)
            //or nonpersistent (held in memory only while the player uses the license).  
            licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;

            // AllowTestDevices controls whether test devices can use the license or not.  
            // If true, the MinimumSecurityLevel property of the license
            // is set to 150. If false (the default), the MinimumSecurityLevel property of the license is set to 2,000.
            licenseTemplate.AllowTestDevices = true;

            // You also can configure the PlayRight in the PlayReady license by using the PlayReadyPlayRight class.
            // It grants the user the ability to play back the content subject to the zero or more restrictions
            // configured in the license and on the PlayRight itself (for playback-specific policy).
            // Much of the policy on the PlayRight has to do with output restrictions,
            // which control the types of outputs that the content can be played over and
            // any restrictions that must be put in place when you use a given output.
            // For example, if DigitalVideoOnlyContentRestriction is enabled,
            // the DRM runtime allows the video to be displayed only over digital outputs
            //(analog video outputs aren't allowed to pass the content).

            //IMPORTANT: These types of restrictions can be very powerful but also can affect the consumer experience.
            // If output protections are too restrictive, 
            // content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.

            // For example:
            //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);

            responseTemplate.LicenseTemplates.Add(licenseTemplate);

            return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
        }

        private static string ConfigureWidevineLicenseTemplate()
        {
            var template = new WidevineMessage
            {
                allowed_track_types = AllowedTrackTypes.SD_HD,
                content_key_specs = new[]
            {
                    new ContentKeySpecs
                    {
                    required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                    security_level = 1,
                    track_type = "SD"
                    }
                },
                policy_overrides = new
                {
                    can_play = true,
                    can_persist = true,
                    can_renew = false
                }
            };

            string configuration = JsonConvert.SerializeObject(template);
            return configuration;
        }

        static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
        {
            // Get the PlayReady license service URL.
            Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);

            // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
            // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
            // WidevineBaseLicenseAcquisitionUrl (used in the following) also tells dynamic encryption
            // to append /? KID =< keyId > to the end of the URL when you create the manifest.
            // As a result, the Widevine license acquisition URL has the KID appended twice,
            // so you need to remove the KID in the URL when you call GetKeyDeliveryUrl.

            Uri widevineUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);
            UriBuilder uriBuilder = new UriBuilder(widevineUrl);
            uriBuilder.Query = String.Empty;
            widevineUrl = uriBuilder.Uri;

            Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                    {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
                    {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl, widevineUrl.ToString()}

            };

            // In this case, we specify only the DASH streaming protocol in the delivery policy.
            // All other protocols are blocked from streaming.
            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.Dash,
            assetDeliveryPolicyConfiguration);


            // Add AssetDelivery Policy to the asset.
            asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        }

        /// <summary>
        /// Gets the streaming origin locator.
        /// </summary>
        /// <param name="assets"></param>
        /// <returns></returns>
        static public string GetStreamingOriginLocator(IAsset asset)
        {

            // Get a reference to the streaming manifest file from the 
            // collection of files in the asset.

            var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                         EndsWith(".ism")).
                         FirstOrDefault();

            // Create a 30-day read-only access policy.
            IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

            // Create a locator to the streaming content on an origin.
            ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

            // Create a URL to the manifest file.
            return originLocator.Path + assetFile.Name;
        }

        static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
            ((IJob)sender).Name,
            e.CurrentState,
            DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
        }

        static private byte[] GetRandomBuffer(int length)
        {
            var returnValue = new byte[length];

            using (var rng =
            new System.Security.Cryptography.RNGCryptoServiceProvider())
            {
                rng.GetBytes(returnValue);
            }

            return returnValue;
        }
    }
}
```

## <a name="next-steps"></a>다음 단계

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>참고 항목
* [다중 DRM 및 Access Control이 포함된 CENC 사용](media-services-cenc-with-multidrm-access-control.md)
* [Media Services를 사용하여 Widevine 패키징 구성](https://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services)
* [Azure Media Services에서 Google Widevine 라이선스 전달 서비스 발표](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)
