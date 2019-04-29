---
title: Azure Media Services .NET SDK로 필터 생성
description: 이 항목에서는 클라이언트가 스트림의 특정 섹션을 스트리밍하는 데 사용할 수 있는 필터를 생성하는 방법을 설명합니다. 이 선택적 스트리밍은 Media Services가 동적 매니페스트를 생성하여 이루어집니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 2f6894ca-fb43-43c0-9151-ddbb2833cafd
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako;cenkdin
ms.openlocfilehash: 05b899658b5c58e15b2f30ab759eb49319979fee
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61465561"
---
# <a name="creating-filters-with-media-services-net-sdk"></a>Media Services .NET SDK로 필터 만들기 
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-dynamic-manifest.md)
> * [REST (영문)](media-services-rest-dynamic-manifest.md)
> 
> 

Media Services 2.17 버전부터 자산에 대한 필터를 정의할 수 있습니다. 이 필터는 고객이 전체 비디오를 재생하는 대신 비디오의 한 섹션만 재생하거나 자산과 연결된 모든 변환 대신 고객의 디바이스가 처리할 수 있는 오디오 및 비디오 변환의 하위 집합만 지정하는 작업 등을 선택할 수 있도록 하는 서버 측 규칙입니다. 지정한 필터에 따라 비디오를 스트림하는 고객의 요청에 따라 생성된 **동적 매니페스트**를 통해 자산의 필터링이 이루어집니다.

필터 및 동적 매니페스트에 대한 더 자세한 내용은 [동적 매니페스트 개요](media-services-dynamic-manifest-overview.md)를 참조하십시오.

이 문서에서는 Media Services .NET SDK를 사용하여 필더를 생성하고, 업데이트하며, 삭제하는 방법을 보여줍니다. 

필터를 업데이트하는 경우 스트리밍 엔드포인트가 규칙을 새로 고치는 데 최대 2분이 소요될 수 있습니다. 콘텐츠가 이 필터로 처리된 경우(및 프록시와 CDN 캐시에서 캐시된 경우) 이 필터를 업데이트하면 플레이어 오류가 발생할 수 있습니다. 필터를 업데이트한 후에 항상 캐시를 지웁니다. 이 옵션을 사용할 수 없는 경우에 서로 다른 필터를 사용 하는 것이 좋습니다. 

## <a name="types-used-to-create-filters"></a>필터 생성에 사용되는 형식
필터를 생성할 때는 다음 형식이 사용됩니다. 

* **IStreamingFilter**.  이 형식은 다음 REST API [Filter](https://docs.microsoft.com/rest/api/media/operations/filter)
* **IStreamingAssetFilter**. 이 형식은 다음 REST API [AssetFilter](https://docs.microsoft.com/rest/api/media/operations/assetfilter)
* **PresentationTimeRange**. 이 형식은 다음 REST API [PresentationTimeRange](https://docs.microsoft.com/rest/api/media/operations/presentationtimerange)
* **FilterTrackSelectStatement** 및 **IFilterTrackPropertyCondition**. 이 형식은 다음 REST API [FilterTrackSelect 및 FilterTrackPropertyCondition](https://docs.microsoft.com/rest/api/media/operations/filtertrackselect)

## <a name="createupdatereaddelete-global-filters"></a>전역 필터 생성/업데이트/읽기/삭제
다음 코드에서는 .NET을 사용하여 자산 필더를 생성, 업데이트, 읽기 및 삭제하는 방법을 보여줍니다.

```csharp
    string filterName = "GlobalFilter_" + Guid.NewGuid().ToString();

    List<FilterTrackSelectStatement> filterTrackSelectStatements = new List<FilterTrackSelectStatement>();

    FilterTrackSelectStatement filterTrackSelectStatement = new FilterTrackSelectStatement();
    filterTrackSelectStatement.PropertyConditions = new List<IFilterTrackPropertyCondition>();
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackNameCondition("Track Name", FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackBitrateRangeCondition(new FilterTrackBitrateRange(0, 1), FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackTypeCondition(FilterTrackType.Audio, FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatements.Add(filterTrackSelectStatement);

    // Create
    IStreamingFilter filter = _context.Filters.Create(filterName, new PresentationTimeRange(), filterTrackSelectStatements);

    // Update
    filter.PresentationTimeRange = new PresentationTimeRange(timescale: 500);
    filter.Update();

    // Read
    var filterUpdated = _context.Filters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);

    // Delete
    filter.Delete();
```

## <a name="createupdatereaddelete-asset-filters"></a>자산 필터 생성/업데이트/읽기/삭제
다음 코드에서는 .NET을 사용하여 자산 필더를 생성, 업데이트, 읽기 및 삭제하는 방법을 보여줍니다.

```csharp
    string assetName = "AssetFilter_" + Guid.NewGuid().ToString();
    var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);

    string filterName = "AssetFilter_" + Guid.NewGuid().ToString();


    // Create
    IStreamingAssetFilter filter = asset.AssetFilters.Create(filterName,
                                        new PresentationTimeRange(), 
                                        new List<FilterTrackSelectStatement>());

    // Update
    filter.PresentationTimeRange = 
            new PresentationTimeRange(start: 6000000000, end: 72000000000);

    filter.Update();

    // Read
    asset = _context.Assets.Where(c => c.Id == asset.Id).FirstOrDefault();
    var filterUpdated = asset.AssetFilters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);

    // Delete
    filterUpdated.Delete();

```


## <a name="build-streaming-urls-that-use-filters"></a>필터를 사용하는 스트리밍 URL 작성
자산을 게시하고 제공하는 방법에 대한 자세한 내용은 [고객에 콘텐츠 배달 개요](media-services-deliver-content-overview.md)를 참조하십시오.

다음 예제에서는 스트리밍 URL에 필터를 추가하는 방법을 보여줍니다.

**MPEG DASH** 

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HLS(HTTP 라이브 스트리밍) V4**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HLS(HTTP 라이브 스트리밍) V3**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**부드러운 스트리밍**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)


## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>관련 항목
[동적 매니페스트 개요](media-services-dynamic-manifest-overview.md)

