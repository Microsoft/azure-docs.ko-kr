---
title: .NET을 사용하여 Azure Media Services 콘텐츠 게시 | Microsoft Docs
description: 스트리밍 URL을 작성하는 데 사용되는 로케이터를 만드는 방법에 대해 알아봅니다. 코드 샘플은 C#으로 작성되었으며 Media Services SDK for .NET을 사용합니다.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: c53b1f83-4cb1-4b09-840f-9c145b7d6f8d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 49e9fbc3f8d10dd1e272f5f9bc5a542328a3ddb4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61224245"
---
# <a name="publish-media-services-content-using-net"></a>.NET을 사용하여 Media Services 콘텐츠 게시  
> [!div class="op_single_selector"]
> * [REST (영문)](media-services-rest-deliver-streaming-content.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [포털](media-services-portal-publish.md)
> 
> 

## <a name="overview"></a>개요
적응 비트 전송률 MP4 집합은 주문형 스트리밍 로케이터를 만들고 스트리밍 URL을 작성하여 스트리밍할 수 있습니다. [자산 인코딩](media-services-encode-asset.md) 항목에서는 적응 비트 전송률 MP4 집합으로 인코딩하는 방법을 설명합니다. 

> [!NOTE]
> 콘텐츠가 암호화되어 있는 경우 [이 항목](media-services-dotnet-configure-asset-delivery-policy.md) 에서 설명하는 대로 자산 배달 정책을 구성한 후에 로케이터를 만듭니다. 
> 
> 

주문형 스트리밍 로케이터는 점진적으로 다운로드할 수 있는 MP4 파일을 가리키는 URL을 작성하는 데 사용할 수도 있습니다.  

이 항목에서는 자산을 게시하고 부드러운 MPEG DASH 및 HLS 스트리밍 URL을 작성하기 위해 OnDemand 스트리밍 로케이터를 만드는 방법을 설명합니다. 또한 점진적 다운로드 URL을 작성하는 핫을 보여 줍니다. 

## <a name="create-an-ondemand-streaming-locator"></a>주문형 스트리밍 로케이터 만들기
주문형 스트리밍 로케이터를 만들고 URL을 가져오려면 다음을 수행해야 합니다.

1. 콘텐츠가 암호화되어 있는 경우 액세스 정책을 정의합니다.
2. 주문형 스트리밍 로케이터를 만듭니다.
3. 스트리밍하려는 경우 자산의 스트리밍 매니페스트 파일(.ism)을 가져옵니다. 
   
   점진적으로 다운로드하려는 경우 자산의 MP4 파일의 이름을 가져옵니다.  
4. 매니페스트 파일 또는 MP4 파일에 URL을 작성합니다. 


>[!NOTE]
>다른 AMS 정책(예: 로케이터 정책 또는 ContentKeyAuthorizationPolicy의 경우)은 1,000,000개의 정책으로 제한됩니다. 항상 동일한 날짜/액세스 권한을 사용하는 경우 동일한 정책 ID를 사용합니다. 장기간 위치에 유지하려는 로케이터의 정책(비 업로드 정책)을 예로 들 수 있습니다. 자세한 내용은 [이 항목](media-services-dotnet-manage-entities.md#limit-access-policies)을 참조하세요.

### <a name="use-media-services-net-sdk"></a>Media Services .NET SDK 사용
스트리밍 URL 작성 

```csharp
    private static void BuildStreamingURLs(IAsset asset)
    {

        // Create a 30-day readonly access policy. 
          // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

        // Create a locator to the streaming content on an origin. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();

        // Get a reference to the streaming manifest file from the  
        // collection of files in the asset. 
        var manifestFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                                    EndsWith(".ism")).
                                    FirstOrDefault();

        // Create a full URL to the manifest file. Use this for playback
        // in streaming media clients. 
        string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest";
        Console.WriteLine("URL to manifest for client streaming using Smooth Streaming protocol: ");
        Console.WriteLine(urlForClientStreaming);
        Console.WriteLine("URL to manifest for client streaming using HLS protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=m3u8-aapl)");
        Console.WriteLine("URL to manifest for client streaming using MPEG DASH protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=mpd-time-csf)"); 
        Console.WriteLine();
    }
```

출력:

    URL to manifest for client streaming using Smooth Streaming protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest
    URL to manifest for client streaming using HLS protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)
    URL to manifest for client streaming using MPEG DASH protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)


> [!NOTE]
> SSL 연결을 통해 콘텐츠를 스트리밍할 수도 있습니다. 이 방법을 수행하려면 스트리밍 URL이 HTTPS로 시작해야 합니다. 현재 AMS는 사용자 지정 도메인을 사용하는 SSL을 지원하지 않습니다.
> 
> 

점진적 다운로드 URL 작성 

```csharp
    private static void BuildProgressiveDownloadURLs(IAsset asset)
    {
        // Create a 30-day readonly access policy. 
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

        // Create an OnDemandOrigin locator to the asset. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();

        // Get MP4 files.
        IEnumerable<IAssetFile> mp4AssetFiles = asset
            .AssetFiles
            .ToList()
            .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Create a full URL to the MP4 files. Use this to progressively download files.
        foreach (var pd in mp4AssetFiles)
            Console.WriteLine(originLocator.Path + pd.Name);
    }
```
출력:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4

    . . . 

### <a name="use-media-services-net-sdk-extensions"></a>Media Services .NET SDK Extensions 사용
다음 코드는 로케이터를 만들고 적응 스트리밍에 대한 부드러운 스트리밍, HLS 및 MPEG-DASH URL을 생성하는 .NET SDK 확장 메서드를 호출합니다.
```csharp
    // Create a loctor.
    _context.Locators.Create(
        LocatorType.OnDemandOrigin,
        inputAsset,
        AccessPermissions.Read,
        TimeSpan.FromDays(30));

    // Get the streaming URLs.
    Uri smoothStreamingUri = inputAsset.GetSmoothStreamingUri();
    Uri hlsUri = inputAsset.GetHlsUri();
    Uri mpegDashUri = inputAsset.GetMpegDashUri();

    Console.WriteLine(smoothStreamingUri);
    Console.WriteLine(hlsUri);
    Console.WriteLine(mpegDashUri);
```

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>다음 단계
* [자산 다운로드](media-services-deliver-asset-download.md)
* [자산 배달 정책 구성](media-services-dotnet-configure-asset-delivery-policy.md)

