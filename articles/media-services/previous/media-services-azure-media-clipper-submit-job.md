---
title: Azure Media Clipper 클리핑 작업 제출 | Microsoft Docs
description: Azure Media Clipper에서 클리핑 작업을 제출하는 단계
services: media-services
keywords: 클립, 하위 클립, 인코딩, 미디어
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: f0dc6879ccbb22dbebd57de98e4610cd593318db
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61242858"
---
# <a name="submit-clipping-jobs-from-azure-media-clipper"></a>Azure Media Clipper에서 클리핑 작업 제출 

Azure Media Clipper에서 클리핑 작업 제출을 처리하려면 **submitSubclipCallback** 메서드가 구현되어야 합니다. 이 함수는 웹 서비스에 대한 Clipper 출력의 HTTP POST를 구현하는 데 사용됩니다. 이 웹 서비스에서 인코딩 작업을 제출할 수 있습니다. Clipper의 출력은 렌더링된 작업의 Media Encoder Standard 인코딩 사전 설정이거나 동적 매니페스트 필터 호출에 대한 REST API 페이로드입니다. 이 통과 모델은 미디어 서비스 계정 자격 증명이 클라이언트 브라우저에서 보호되지 않으므로 필요합니다.

다음 시퀀스 다이어그램에서는 브라우저 클라이언트, 웹 서비스 및 Azure Media Services 간의 워크플로를 설명합니다. ![Azure Media Clipper 시퀀스 다이어그램](media/media-services-azure-media-clipper-submit-job/media-services-azure-media-clipper-sequence-diagram.PNG)

앞의 순서도에는 사용자 브라우저, 웹 서비스, Clipper 리소스를 호스팅하는 CDN 엔드포인트, Azure Media Services 등의 4개 개체가 있습니다. 최종 사용자가 웹 페이지를 탐색하면 페이지가 호스팅 CDN 엔드포인트에서 Clipper JavaScript와 CSS 리소스를 가져옵니다. 최종 사용자는 자신의 브라우저에서 클리핑 작업이나 동적 매니페스트 필터 만들기 호출을 구성합니다. 최종 사용자가 작업 또는 필터 만들기 호출을 제출하면 브라우저가 작업 페이로드를 사용자가 배포해야 하는 웹 서비스에 넣습니다. 이 웹 서비스는 결과적으로 미디어 서비스 계정 자격 증명을 사용하여 클리핑 작업이나 필터 만들기 호출을 Azure Media Services에 제출하게 됩니다.

다음 코드 샘플은 샘플 **submitSubclipCallback** 메서드를 보여줍니다. 이 메서드에서는 Media Encoder Standard 인코딩 사전 설정의 HTTP POST를 구현합니다. POST가 성공하면(**결과**), **프라미스**가 확인되고, 그렇지 않으면 오류 정보와 함께 거부됩니다.

```javascript
// Submit Subclip Callback
//
// Parameter:
// - subclip: object that represents the subclip (output contract).
//
// Returns: a Promise object that, when resolved, returns true if the operation was accept in the back-end; otherwise, returns false.
var onSubmitSubclip = function (subclip) {
    var promise = new Promise(function (resolve, reject) {
        // TODO: perform the back-end AJAX request to submit the subclip job.
        var result = true;

        if (/* everything turned out fine */ result) {
            resolve(result);
        }
        else {
            reject(Error("error details"));
        }
    });

    return promise;
};

var subclipper = new subclipper({
    selector: '#root',
    restVersion: '2.0',
    submitSubclipCallback: onSubmitSubclip,
});
```
작업 제출의 출력은 렌더링된 작업의 Media Encoder Standard 인코딩 사전 설정이거나 동적 매니페스트 필터에 대한 REST API 페이로드입니다.

## <a name="submitting-encoding-job-to-create-video"></a>비디오를 만드는 인코딩 작업 제출
프레임이 정확한 비디오 클립을 만들기 위해 Media Encoder Standard 인코딩 작업을 제출할 수 있습니다. 인코딩 작업에서는 렌더링된 비디오인 새롭게 조각난 MP4 파일을 생성합니다.

렌더링된 클리핑에 대한 작업 출력 계약은 다음과 같은 속성을 갖는 JSON 개체입니다.

```json
{
  /* Required */
  "name": "My New Subclip Output Name",

  /* Required: string specifying the format of the Json file.
   */
  /* NOTE: The REST API version, currently 2.0 is supported
   */
  "restVersion": "2.0"

  /* Required: array containing all the input Ids (both "asset" or "filter" type) used in the subclipper timeline;
  it must contain at least one item. */
  /* NOTE: when "output"."type" === "job", all items must match the "inputsIds"[i]."type" === "asset" condition;
  this array can be used in the back-end to get the input assets for the subclipping job with the Media Encoder Standard (MES) processor. */
  /* NOTE: when "output"."type" === "filter", there must be a single item in the array ("inputsIds".length === 1)
  that can be used in the back-end to get the source for the dynamic manifest filter (create or update). */
  "inputsIds": [{
    /* Required */
    "id": "my-asset-id-1",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }, {
    /* Required */
    "id": "my-asset-id-2",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }],

  /* Required */
  "output": {

    /* Required: must be one of the following values: "job" or "filter" */
    "type": "job",

    /* Required if "type" === "job" */
    /* NOTE: This is the preset for the Media Encoder Standard (MES) processor that can be used in the back-end to submit the subclip job.
    The encoding profile ("Codecs" property) depends on the "singleBitrateMp4Profile" and "multiBitrateMp4Profile" option parameters
    specified when creating the widget instance. */
    /* REFERENCE: https://docs.microsoft.com/azure/media-services/media-services-advanced-encoding-with-mes */
    "job": {
      "Version": 1.0,
      "Codecs": [{
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": true,
          "H264Layers": [{
            "Profile": "Auto",
            "Level": "auto",
            "Bitrate": 6750,
            "MaxBitrate": 6750,
            "BufferWindow": "00:00:05",
            "Width": 1920,
            "Height": 1080,
            "BFrames": 3,
            "ReferenceFrames": 3,
            "AdaptiveBFrame": true,
            "Type": "H264Layer",
            "FrameRate": "0/1"
          }],
          "Type": "H264Video"
        },
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [{
        "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
        "Format": {
          "Type": "MP4Format"
        }
      }],
      "Sources": [{
        "AssetId": "my-asset-id-1",
        "StartTime": "0.00:01:15.000",
        "Duration": "0.00:00:25.000"
      }, {
        "AssetId": "my-asset-id-2",
        "StartTime": "0.00:20:04.000",
        "Duration": "0.00:33:07.500"
      }]
    }
  }
}
```

인코딩 작업을 수행하려면 연결된 사전 설정을 통해 Media Encoder Standard 인코딩 작업을 제출합니다. [.NET SDK](https://docs.microsoft.com/azure/media-services/media-services-dotnet-encode-with-media-encoder-standard) 또는 [REST API](https://docs.microsoft.com/azure/media-services/media-services-rest-encode-asset)를 사용한 인코딩 작업 제출에 관한 자세한 내용은 이 문서를 참조하세요.

## <a name="quickly-creating-video-clips-without-encoding"></a>인코딩 없이 신속하게 비디오 클립 만들기
인코딩 작업을 만드는 대신 Azure Media Clipper를 사용하여 동적 매니페스트 필터를 만들 수 있습니다. 필터는 인코딩이 필요하지 않으며, 새 자산이 만들어지지 않기 때문에 신속하게 만들 수 있습니다. 필터 클리핑에 대한 출력 계약은 다음과 같은 속성을 갖는 JSON 개체입니다.

```json
{
  /* Required: Filter name (Alphanumeric characters and hyphens only, no whitespace) */
  "name": "FilterName",

  /* Required: string specifying the format of the Json file.
   */
  /* NOTE: This subclipper version always returns '2.0' in this field.
   */
  "restVersion": "2.0"

  /* Required: array containing all the input Ids (both "asset" or "filter" type) used in the subclipper timeline;
  it must contain at least one item. */
  /* NOTE: when "output"."type" === "job", all items must match the "inputsIds"[i]."type" === "asset" condition;
  this array can be used in the back-end to get the input assets for the subclipping job with the Media Encoder Standard (MES) processor. */
  /* NOTE: when "output"."type" === "filter", there must be a single item in the array ("inputsIds".length === 1)
  that can be used in the back-end to get the source for the dynamic manifest filter (create or update). */
  "inputsIds": [{
    /* Required */
    "id": "my-asset-id",

    /* Required: must be one of the following values: "asset" or "filter" */
    "type": "asset"
  }],

  /* Required */
  "output": {

    /* Required: must be one of the following values: "job" or "filter" */
    "type": "filter",

    /* Required if "type" === "filter" */
    /* REFERENCE: https://docs.microsoft.com/rest/api/media/operations/assetfilter */
    "filter": {
      "PresentationTimeRange": {
        "StartTimestamp": "340000000",
        "EndTimestamp": "580000000",
        "Timescale": "10000000"
      },
      "Tracks": [{
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "video",
          "Operator": "Equal"
        }]
      }, {
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "audio",
          "Operator": "Equal"
        }, {
          "Property": "Name",
          "Value": "audio-track-1",
          "Operator": "Equal"
        }]
      }, {
        "PropertyConditions": [{
          "Property": "Type",
          "Value": "text",
          "Operator": "Equal"
        }, {
          "Property": "Language",
          "Value": "en",
          "Operator": "Equal"
        }]
      }]
    }
  }
}
```

동적 매니페스트 필터를 만들기 위해 REST 호출을 제출하려면 [REST API](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest)를 사용하여 연결된 필터 페이로드를 제출합니다.
