---
title: Azure Media Clipper에 자산 로드 | Microsoft Docs
description: Azure Media Clipper에 자산을 로드하는 단계
services: media-services
keywords: 클립, 하위 클립, 인코딩, 미디어
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: ec8cd06be78bbd8df0bca390696e736c3a6ee075
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61465884"
---
# <a name="loading-assets-into-azure-media-clipper"></a>Azure Media Clipper에 자산 로드  

다음 두 가지 방법으로 Azure Media Clipper에 자산을 로드할 수 있습니다.
1. 고정적으로 자산 라이브러리 제공
2. API를 통해 동적으로 자산 목록 생성

## <a name="statically-load-videos-into-clipper"></a>Clipper에 정적으로 비디오 로드
Clipper에 정적으로 비디오를 로드하면 최종 사용자가 자산 선택 창에서 비디오를 선택하지 않고도 클립을 빌드할 수 있습니다.

이 경우 자산의 고정 집합을 Clipper에 제공합니다. 각 자산에는 AMS 자산/필터 ID, 이름, 게시된 스트리밍 URL이 포함됩니다. 해당하는 경우 콘텐츠 보호 인증 토큰 또는 썸네일 URL 배열이 제공될 수 있습니다. 제공될 경우 썸네일이 인터페이스를 채웁니다. 자산 라이브러리가 고정적이며 소규모인 시나리오에서는 라이브러리의 각 자산에 대해 자산 계약을 제공할 수 있습니다.

> [!NOTE]
> 자산을 Clipper에 정적으로 로드할 때는 자산이 **타임라인에 직접** 추가되며 **자산 창이 렌더링되지 않습니다**. 첫 번째 자산이 타임라인에 추가되고 나머지 자산은 타임라인 오른쪽에 쌓입니다.

고정 자산 라이브러리를 로드하려면 **load** 메서드를 사용하여 각 자산의 JSON 표현을 제공합니다. 다음 코드 샘플은 단일 자산에 대한 JSON 표현을 보여줍니다.

```javascript
var assets = [
    {
      /* Required: represents the Azure Media Services asset Id when "type" === "asset"; otherwise, represents the dynamic manifest asset filter Id ("type" === "filter")  */
      "id": "my-asset-or-dynamic-manifest-asset-filter-id",
    
      /* Required: represents the asset name as shown in the Clipper interface */
      "name": "My Asset or Dynamic Manifest Asset Filter Name",
    
      /* Required: must be one of the following values: "asset" or "filter" */
      /* NOTE: "asset" type represents a rendered asset; "filter" type represents a dynamic manifest asset filter */
      "type": "asset",
    
      /* Required */
      "source": {
    
        /* Required: represents the asset streaming locator, the base Smooth Streaming URL */
        "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
    
        /* Optional: default value "application/vnd.ms-sstr+xml" */
        "type": "application/vnd.ms-sstr+xml",
    
        /* Required: If the asset has content protection applied, then you must include an array with the different protection types along with the token to request the license/key; otherwise, provide an empty array */
        "protectionInfo": [{
            "type": "AES",
            "authenticationToken": "Bearer aes-token-placeholder"
          },
          {
            "type": "PlayReady",
            "authenticationToken": "Bearer playready-token-placeholder"
          },
          {
            "type": "Widevine",
            "authenticationToken": "Bearer widevine-token-placeholder"
          },
          {
            "type": "FairPlay",
            "certificateUrl": "//example/path/to/myfairplay.der",
            "authenticationToken": "Bearer fairplay-token-placeholder"
          }
        ]
      },
    
      /* Optional: array containing thumbnail URLs for the video. */
      /* NOTE: For the thumbnail URLs to work as expected in the Clipper timeline they must be evenly distributed across the video (based on the duration) and in chronological order within the array. */
      "thumbnails": [
        "//example/path/thumbnail_001.jpg",
        "//example/path/thumbnail_002.jpg",
        "//example/path/thumbnail_003.jpg",
        "//example/path/thumbnail_004.jpg",
        "//example/path/thumbnail_005.jpg"
        ]
    }
];

var subclipper = new subclipper({
    selector: '#root',
    restVersion: '2.0',
    submitSubclipCallback: onSubmitSubclip,
});
subclipper.ready(function () {
    subclipper.load(assets);
});

```

> [!NOTE]
> 앞의 예제에서처럼 load() 메서드 호출을 ready(handler) 메서드와 연결하는 것이 좋습니다. 앞의 예제에서는 자산 로드에 앞서 위젯이 준비되도록 보장합니다.

> [!NOTE]
> 썸네일 URL은 Clipper 타임라인에서 예상대로 작동될 수 있게 비디오에서 균일하게(기간에 따라), 배열 내에서 시간 순서로 분산되어야 합니다. 다음 JSON 사전 설정 코드 조각을 참조하여 'Media Encoder Standard' 프로세서로 이미지를 생성하는 데 도움을 얻을 수 있습니다.

```json
{
  "Start": "0",
  "Step": "00:00:05",
  "Range": "100%",
  "Type": "PngImage",
  "PngLayers": [
    {
      "Type": "PngLayer",
      "Width": 48,
      "Height": 26
    }
  ]
}
```

## <a name="dynamically-load-videos-in-clipper"></a>Clipper에 동적으로 비디오 로드
Clipper에 동적으로 비디오를 로드하면 최종 사용자가 자산 선택 창에서 클립에 대한 비디오를 선택할 수 있습니다.

또는 콜백을 통해 동적으로 자산을 로드할 수 있습니다. 자산이 동적으로 생성되거나 라이브러리가 큰 시나리오에서는 콜백을 통해 로드하는 것이 좋습니다. 자산을 동적으로 로드하려면 선택적 onLoadAssets 콜백 함수를 구현해야 합니다. 이 함수는 초기화 시 Clipper에 제공됩니다. 확인된 자산은 고정적으로 로드되는 자산과 동일한 계약을 준수해야 합니다. 다음 코드 샘플에서는 메서드 서명, 예상 입력 및 예상 출력을 보여줍니다.

> [!NOTE]
> Clipper에 동적으로 자산을 로드할 때는 자산이 **자산 선택 창**에서 렌더링됩니다.

```javascript
// Video Assets Pane Callback
    //
    // Filter Parameters:
    // - search: string value term that will be used in the back-end to filter assets by name.
    // - skip: int value used for pagination in the back-end that allows skipping a number of assets in the response.
    // - take: int value used for pagination in the back-end that allows defining the number of assets to include in the response.
    // - type: ('filter', 'asset') value that will be used in the back-end to filter assets by type.
    //
    // Returns: a Promise object that, when resolved, returns an object containing an array of assets (input contract)
    //          that satisfies the filter parameters, plus optionally the total types of files available:
    // {
    //  total: 100,
    //  assets: [{...}],
    // }
    var onLoadAssets = function (search, skip, take, type) {
        var promise = new Promise(function (resolve, reject) {
            // TODO: implement the getAssetsFromBackend method to get the assets from the back-end using the filter parameters (search, skip, take, type).
            getAssetsFromBackend(search, skip, take, type)
                .then(function (assets) {
                    resolve({
                        total: assets.length,
                        assets: assets
                    });
                }).catch(function () {
                    reject(Error("error details"));
                });
        });
    
        return promise;
    };

    // Create widget instance:
    // - using a root element selector
    // - enabling the Video Assets panel by registering a callback in the 'assetsPanelLoaderCallback' option parameter.
    var widget = new subclipper({
        selector: '#root',

        // Enable the Video Assets panel in the widget to automatically load assets (input contract)
        assetsPanelLoaderCallback: onLoadAssets
    });

    // ...
    // The widget will automatically invoke the 'assetsPanelLoaderCallback' callback with the filter parameters specified by the user 
    // and load assets returned by the Promise into the Video Assets panel.
    // ...
```
