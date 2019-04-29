---
title: .NET을 사용한 주문형 콘텐츠 제공 시작 | Microsoft Docs
description: 이 자습서에서는 .NET을 사용한 Azure Media Services로 주문형 콘텐츠 배달 애플리케이션을 구현하는 단계를 안내합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 388b8928-9aa9-46b1-b60a-a918da75bd7b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 2d55af3e9ed3ad64f9ba7726799b31acb6b48580
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61465051"
---
# <a name="get-started-with-delivering-content-on-demand-using-net-sdk"></a>.NET SDK를 사용한 주문형 콘텐츠 제공 시작  

[!INCLUDE [media-services-selector-get-started](../../../includes/media-services-selector-get-started.md)]

이 자습서에서는 Azure Media Services .NET SDK를 사용하는 AMS(Azure Media Services) 애플리케이션으로 기본 VoD(주문형 비디오) 콘텐츠 배달 서비스를 구현하는 단계를 안내합니다.

## <a name="prerequisites"></a>필수 조건

자습서를 완료하는 데 필요한 조건은 다음과 같습니다.

* Azure 계정. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* Media Services 계정. Media Services 계정을 만들려면 [Media Services 계정을 만드는 방법](media-services-portal-create-account.md)을 참조하세요.
* .NET Framework 4.0 이상.
* 있습니다.

이 자습서에는 다음 작업이 포함되어 있습니다.

1. 스트리밍 엔드포인트 시작(Azure Portal 사용)
2. Visual Studio 프로젝트 만들기 및 구성
3. Media Services 계정에 연결합니다.
2. 비디오 파일을 업로드합니다.
3. 원본 파일을 적응 비트 전송률 MP4 파일 집합으로 인코딩합니다.
4. 자산을 게시하고, 스트리밍 기능을 사용하고, URL을 점진적으로 다운로드합니다.  
5. 콘텐츠를 재생합니다.

## <a name="overview"></a>개요
이 자습서에서는 Azure Media Services(AMS) SDK for .NET를 사용하여 VoD(주문형 비디오) 콘텐츠 배달 애플리케이션을 구현하는 단계를 안내합니다.

기본적인 Media Services 워크플로와 Media Services 개발에 필요한 가장 일반적인 프로그래밍 개체 및 작업을 소개합니다. 자습서를 마치면 업로드하고 인코딩하고 다운로드한 샘플 미디어 파일을 스트리밍하거나 점진적으로 다운로드할 수 있습니다.

### <a name="ams-model"></a>AMS 모델

다음 이미지에서는 Media Services OData 모델에 대해 VoD 애플리케이션을 개발할 때 가장 일반적으로 사용되는 개체 중 일부를 보여 줍니다.

전체 크기로 보려면 이미지를 클릭합니다.  

<a href="./media/media-services-dotnet-get-started/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-dotnet-get-started/media-services-overview-object-model-small.png"></a> 

전체 모델은 [여기](https://media.windows.net/API/$metadata?api-version=2.15)서 볼 수 있습니다 .  

## <a name="start-streaming-endpoints-using-the-azure-portal"></a>Azure Portal을 사용하여 스트리밍 엔드포인트 시작

Azure Media Services 작업 시 가장 일반적인 시나리오 중 하나는 적응 비트 전송률 스트리밍을 통해 비디오를 제공하는 것입니다. Media Services는 적응 비트 전송률 MP4 인코딩 콘텐츠를 Media Services에서 적시에 지원되는 각 스트리밍 형식(MPEG DASH, HLS, 부드러운 스트리밍)의 사전 패키징된 버전을 저장하지 않고도 이런 스트리밍 형식으로 배달할 수 있게 하는 동적 패키징을 제공합니다.

>[!NOTE]
>AMS 계정이 만들어질 때 **기본** 스트리밍 엔드포인트는 **중지됨** 상태에서 계정에 추가됩니다. 콘텐츠 스트리밍을 시작하고 동적 패키징 및 동적 암호화를 활용하려면 콘텐츠를 스트리밍하려는 스트리밍 엔드포인트는 **실행** 상태에 있어야 합니다.

스트리밍 엔드포인트를 시작하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 설정 창에서 스트리밍 엔드포인트를 클릭합니다.
3. 기본 스트리밍 엔드포인트를 클릭합니다.

    기본 스트리밍 엔드포인트 세부 정보 창이 나타납니다.

4. 시작 아이콘을 클릭합니다.
5. 저장 단추를 클릭하여 변경 내용을 저장합니다.

## <a name="create-and-configure-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기 및 구성

1. 개발 환경을 설정하고 [.NET을 사용한 Media Services 환경](media-services-dotnet-how-to-use.md)에 설명된 대로 연결 정보를 사용하여 app.config 파일을 채웁니다. 
2. 로컬 드라이브 내 임의의 위치에 새 폴더를 만들고, 인코딩하여 스트리밍하거나 점진적으로 다운로드하려는 .mp4 파일을 복사합니다. 이 예제에서는 "C:\VideoFiles" 경로가 사용됩니다.

## <a name="connect-to-the-media-services-account"></a>Media Services 계정에 연결

Media Services를 .NET과 함께 사용하는 경우 Media Services 계정에 연결하는 작업이나 자산, 자산 파일, 작업, 액세스 정책, 로케이터 등의 개체를 만들고 업데이트하고 액세스하고 삭제하는 작업을 포함한 대부분의 Media Services 프로그래밍 작업에 **CloudMediaContext** 클래스를 사용해야 합니다.

기본 Program 클래스를 다음 코드로 덮어씁니다. 이 코드는 App.config 파일에서 연결 값을 읽는 방법 및 Media Services에 연결하기 위해 **CloudMediaContext** 개체를 만드는 방법을 보여 줍니다. 자세한 내용은 [Media Services API에 연결](media-services-use-aad-auth-to-access-ams-api.md)을 참조하세요.

파일 이름과 경로를 미디어 파일이 있는 위치로 업데이트합니다.

**Main** 함수는 이 섹션에서 자세히 정의되는 메서드를 호출합니다.

> [!NOTE]
> 이 문서의 뒤에서 정의된 모든 함수에 대한 정의를 추가하기 전까지는 컴파일 오류가 발생합니다.

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
        try
        {
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Add calls to methods defined in this section.
            // Make sure to update the file name and path to where you have your media file.
            IAsset inputAsset =
            UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.None);

            IAsset encodedAsset =
            EncodeToAdaptiveBitrateMP4s(inputAsset, AssetCreationOptions.None);

            PublishAssetGetURLs(encodedAsset);
        }
        catch (Exception exception)
        {
            // Parse the XML error message in the Media Services response and create a new
            // exception with its content.
            exception = MediaServicesExceptionParser.Parse(exception);

            Console.Error.WriteLine(exception.Message);
        }
        finally
        {
            Console.ReadLine();
        }
        }
```

## <a name="create-a-new-asset-and-upload-a-video-file"></a>새 자산 만들기 및 비디오 파일 업로드

Media Services에서 자산에 디지털 파일을 업로드(수집)합니다. **자산** 엔터티에는 비디오, 오디오, 이미지, 미리 보기 컬렉션, 텍스트 트랙 및 선택 자막 파일(및 이러한 파일에 대한 메타데이터)이 포함될 수 있습니다.  파일이 업로드되면 이후 처리 및 스트리밍을 위해 콘텐츠가 클라우드에 안전하게 저장됩니다. 자산에 포함된 파일을 **자산 파일**이라고 합니다.

아래에서 정의된 **UploadFile** 메서드는 **CreateFromFile**(.NET SDK 확장에 정의됨)을 호출합니다. **CreateFromFile** 은 지정된 원본 파일이 업로드되는 새 자산을 만듭니다.

**CreateFromFile** 메서드는 다음 자산 만들기 옵션 중 하나를 지정할 수 있는 **AssetCreationOptions**를 사용합니다.

* **없음** - 암호화가 사용되지 않습니다. 기본값입니다. 이 옵션을 사용하면 콘텐츠가 전송 중인 상태이거나 저장소에 저장된 상태일 때 보호되지 않습니다.
  MP4를 배달하려는 경우 이 옵션을 사용하세요.
* **StorageEncrypted** - AES(Advanced Encryption Standard) 256비트 암호화를 사용하여 암호화되지 않은 콘텐츠를 로컬에서 암호화한 다음에 암호화되어 저장된 Azure Storage에 업로드하려면 이 옵션을 사용합니다. Storage 암호화로 보호된 자산은 자동으로 암호 해제되어 인코딩되기 전에 암호화된 파일 시스템에 배치됩니다. 그리고 필요에 따라 새 출력 자산으로 다시 업로드되기 전에 다시 암호화됩니다. Storage 암호화를 사용하는 기본적인 사례는 디스크에 저장된 상태일 때 강력한 암호화로 고품질의 입력 미디어 파일을 보호하려는 경우입니다.
* **CommonEncryptionProtected** - 이미 암호화되어 일반적인 암호화 또는 PlayReady DRM(예: PlayReady DRM으로 보호되는 부드러운 스트리밍)으로 보호된 콘텐츠를 업로드하는 경우 이 옵션을 사용합니다.
* **EnvelopeEncryptionProtected** - AES로 암호화된 HLS를 업로드하는 경우 이 옵션을 사용합니다. 파일을 Transform Manager로 인코딩 및 암호화해야 합니다.

**CreateFromFile** 메서드는 또한 파일의 업로드 진행 상태를 보고하기 위해 콜백을 지정할 수 있습니다.

다음 예제에서는 자산 옵션으로 **없음**을 지정합니다.

Program 클래스에 다음 메서드를 추가합니다.

```csharp
    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Asset {0} created.", inputAsset.Id);

        return inputAsset;
    }
```

## <a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a>원본 파일을 적응 비트 전송률 MP4 파일 집합으로 인코딩
Media Services에 자산을 삽입하고 나면 미디어를 클라이언트에 배달하기 전에 인코딩, 트랜스먹싱, 워터마크 지정 등을 수행할 수 있습니다. 이러한 활동은 높은 성능과 가용성을 보장하기 위해 여러 백그라운드 역할 인스턴스에 대해 예약 및 실행합니다. 이러한 활동을 작업이라고 하며 각 작업은 자산 파일에서 실제 작업을 수행하는 원자성 작업으로 구성됩니다.

앞에서 언급한 대로, Azure Media Services 작업 시 가장 일반적인 시나리오 중 하나는 적응 비트 전송률 스트리밍을 클라이언트에 제공하는 것입니다. Media Services는 적응 비트 전송률 MP4 파일을 다음 형식 중 하나로 동적 패키징합니다. HLS(HTTP 라이브 스트리밍), 부드러운 스트리밍, MPEG DASH

동적 패키징을 활용하려면 mezzanine(원본) 파일을 적응 비트 전송률 MP4 파일 또는 적응 비트 전송률 부드러운 스트리밍 파일 집합으로 인코딩하거나 트랜스코딩해야 합니다.  

다음 코드는 인코딩 작업을 제출하는 방법을 보여 줍니다. 이 작업에는 **미디어 인코더 표준**을 사용하여 mezzanine 파일을 적응 비트 전송률 MP4 집합으로 트랜스코딩하도록 지정하는 한 가지 태스크가 포함됩니다. 이 코드는 작업을 제출하고 완료될 때까지 기다립니다.

작업이 완료되면 자산을 스트리밍하거나 트랜스코딩 결과로 생성된 MP4 파일을 점진적으로 다운로드할 수 있습니다.

Program 클래스에 다음 메서드를 추가합니다.

```csharp
    static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
    {

        // Prepare a job with a single task to transcode the specified asset
        // into a multi-bitrate asset.

        IJob job = _context.Jobs.CreateWithSingleTask(
            "Media Encoder Standard",
            "Adaptive Streaming",
            asset,
            "Adaptive Bitrate MP4",
            options);

        Console.WriteLine("Submitting transcoding job...");


        // Submit the job and wait until it is completed.
        job.Submit();

        job = job.StartExecutionProgressTask(
            j =>
            {
                Console.WriteLine("Job state: {0}", j.State);
                Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
            },
            CancellationToken.None).Result;

        Console.WriteLine("Transcoding job finished.");

        IAsset outputAsset = job.OutputMediaAssets[0];

        return outputAsset;
    }
```

## <a name="publish-the-asset-and-get-urls-for-streaming-and-progressive-download"></a>자산 게시, 스트리밍 및 점진적 다운로드를 위한 URL 가져오기

자산을 스트리밍하거나 다운로드하려면 먼저 로케이터를 만들어 자산을 "게시"해야 합니다. 로케이터는 자산에 포함된 파일에 대한 액세스를 제공합니다. Media Services는 두 가지 유형의 로케이터를 지원합니다. 하나는 OnDemandOrigin 로케이터로써 미디어를 스트리밍하는 데 사용되고(예: MPEG DASH, HLS 또는 부드러운 스트리밍) 다른 하나는 SAS(공유 액세스 서명) 로케이터로써 미디어 파일을 다운로드하는 데 사용됩니다.

### <a name="some-details-about-url-formats"></a>URL 형식에 대한 일부 세부 정보

로케이터를 만든 후에 파일을 스트리밍하거나 다운로드하는 데 사용할 URL을 작성할 수 있습니다. 이 자습서의 샘플은 적절한 브라우저에 붙여넣을 수 있는 URL을 출력합니다. 이 섹션에서는 다양한 형식을 보여 주는 간단한 예제를 제공합니다.

#### <a name="a-streaming-url-for-mpeg-dash-has-the-following-format"></a>MPEG DASH에 대한 스트리밍 URL의 형식은 다음과 같습니다.

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest **(format=mpd-time-csf)**

#### <a name="a-streaming-url-for-hls-has-the-following-format"></a>HLS에 대한 스트리밍 URL의 형식은 다음과 같습니다.

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest **(format=m3u8-aapl)**

#### <a name="a-streaming-url-for-smooth-streaming-has-the-following-format"></a>부드러운 스트리밍에 대한 스트리밍 URL의 형식은 다음과 같습니다.

{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest


#### <a name="a-sas-url-used-to-download-files-has-the-following-format"></a>파일을 다운로드하는 데 사용되는 SAS URL의 형식은 다음과 같습니다.

{blob container name}/{asset name}/{file name}/{SAS signature}

Media Services .NET SDK Extensions는 게시된 자산에 대한 서식 지정된 URL을 반환하는 편리한 도우미 메서드를 제공합니다.

다음 코드는 .NET SDK Extensions를 사용하여 로케이터를 만들고 스트리밍 및 점진적 다운로드 URL을 가져옵니다. 코드는 또한 파일을 로컬 폴더에 다운로드하는 방법을 보여 줍니다.

Program 클래스에 다음 메서드를 추가합니다.

```csharp
    static public void PublishAssetGetURLs(IAsset asset)
    {
        // Publish the output asset by creating an Origin locator for adaptive streaming,
        // and a SAS locator for progressive download.

        _context.Locators.Create(
            LocatorType.OnDemandOrigin,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));

        _context.Locators.Create(
            LocatorType.Sas,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));


        IEnumerable<IAssetFile> mp4AssetFiles = asset
                .AssetFiles
                .ToList()
                .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Get the Smooth Streaming, HLS and MPEG-DASH URLs for adaptive streaming,
        // and the Progressive Download URL.
        Uri smoothStreamingUri = asset.GetSmoothStreamingUri();
        Uri hlsUri = asset.GetHlsUri();
        Uri mpegDashUri = asset.GetMpegDashUri();

        // Get the URls for progressive download for each MP4 file that was generated as a result
        // of encoding.
        List<Uri> mp4ProgressiveDownloadUris = mp4AssetFiles.Select(af => af.GetSasUri()).ToList();


        // Display  the streaming URLs.
        Console.WriteLine("Use the following URLs for adaptive streaming: ");
        Console.WriteLine(smoothStreamingUri);
        Console.WriteLine(hlsUri);
        Console.WriteLine(mpegDashUri);
        Console.WriteLine();

        // Display the URLs for progressive download.
        Console.WriteLine("Use the following URLs for progressive download.");
        mp4ProgressiveDownloadUris.ForEach(uri => Console.WriteLine(uri + "\n"));
        Console.WriteLine();

        // Download the output asset to a local folder.
        string outputFolder = "job-output";
        if (!Directory.Exists(outputFolder))
        {
            Directory.CreateDirectory(outputFolder);
        }

        Console.WriteLine();
        Console.WriteLine("Downloading output asset files to a local folder...");
        asset.DownloadToFolder(
            outputFolder,
            (af, p) =>
            {
                Console.WriteLine("Downloading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Output asset files available at '{0}'.", Path.GetFullPath(outputFolder));
    }
```

## <a name="test-by-playing-your-content"></a>콘텐츠를 재생하여 테스트합니다.

이전 섹션에 정의된 프로그램을 실행하고 나면 다음과 유사한 URL이 콘솔 창에 표시됩니다.

적응 스트리밍 URL:

부드러운 스트리밍

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

HLS

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

MPEG DASH

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)

점진적 다운로드 URL(오디오 및 비디오)

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


비디오를 스트리밍하려면 [Azure Media Services 플레이어](https://amsplayer.azurewebsites.net/azuremediaplayer.html)의 URL 텍스트 상자에서 URL을 붙여넣습니다.

점진적 다운로드를 테스트하려면 Internet Explorer, Chrome, Safari 등의 브라우저에 URL을 붙여넣습니다.

자세한 내용은 다음 항목을 참조하십시오.

- [기존 플레이어를 사용하여 콘텐츠 재생](media-services-playback-content-with-existing-players.md)
- [비디오 플레이어 애플리케이션 개발](media-services-develop-video-players.md)
- [DASH.js를 사용하여 HTML5 애플리케이션에 MPEG-DASH 적응 스트리밍 비디오 포함](media-services-embed-mpeg-dash-in-html5.md)

## <a name="download-sample"></a>샘플 다운로드
코드 샘플([샘플](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/))에는 이 자습서에서 만든 코드가 포함되어 있습니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]



<!-- Anchors. -->


<!-- URLs. -->
[Web Platform Installer]: https://go.microsoft.com/fwlink/?linkid=255386
[Portal]: https://portal.azure.com/
