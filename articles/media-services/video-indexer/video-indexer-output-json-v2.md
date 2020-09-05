---
title: V2 API에서 생성 된 Video Indexer 출력 검사-Azure
titleSuffix: Azure Media Services
description: 이 항목에서는 v2 API에 의해 생성 된 Azure Media Services Video Indexer 출력을 검사 합니다.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 08/27/2020
ms.author: juliako
ms.openlocfilehash: 6eecaaff836d3253d382fdf0280f9a15c3a7b00b
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89050865"
---
# <a name="examine-the-video-indexer-output"></a>Video Indexer 출력 검사

비디오가 인덱싱되는 경우 poduces은 지정 된 비디오 정보에 대 한 세부 정보가 포함 된 JSON 콘텐츠를 Video Indexer 합니다. 이 정보에는 성적 증명서, OCRs, 얼굴, 토픽, 블록 등이 포함 됩니다. 각 정보 형식에는 비디오에 정보를 표시 하는 시간 범위 인스턴스가 포함 됩니다. 

[Video Indexer](https://www.videoindexer.ai/) 웹 사이트의 비디오에서 **재생** 단추를 눌러 비디오의 요약 된 정보를 시각적으로 검사할 수 있습니다. 

**비디오 인덱스 가져오기** api를 호출 하 여 api를 사용할 수도 있으며, 응답 상태가 양호 이면 응답 콘텐츠로 자세한 JSON 출력을 가져옵니다.

![자세한 정보](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

이 문서에서는 Video Indexer 출력 (JSON 콘텐츠)을 검사 합니다. <br/>사용할 수 있는 기능 및 정보에 대 한 자세한 내용은 [Video Indexer insights](video-indexer-overview.md#video-insights)를 참조 하세요.

> [!NOTE]
> Video Indexer에서 모든 액세스 토큰의 만료 시간은 1시간입니다.

## <a name="get-the-insights"></a>정보 얻기

### <a name="insightsoutput-produced-in-the-websiteportal"></a>웹 사이트/포털에서 생성 된 정보/출력

1. [Video Indexer](https://www.videoindexer.ai/) 웹 사이트로 이동하고 로그인합니다.
1. 검사 하려는 출력에 대 한 비디오를 찾습니다.
1. **재생**을 누릅니다.
1. **Insights** 탭 (요약 된 정보) 또는 **타임 라인** 탭 (관련 정보를 필터링 할 수 있음)을 선택 합니다.
1. 아티팩트 및 아티팩트를 다운로드 합니다.

자세한 내용은 [비디오 인사이트 보기 및 편집](video-indexer-view-edit.md)을 참조하세요.

## <a name="insightsoutput-produced-by-api"></a>API에 의해 생성 된 정보/출력

1. JSON 파일을 검색 하려면 [비디오 인덱스 API 가져오기](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Index?) 를 호출 합니다.
1. 특정 아티팩트에도 관심이 있는 경우 [비디오 아티팩트 가져오기 다운로드 URL API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Artifact-Download-Url?) 를 호출 합니다.

    API 호출에서 요청 된 아티팩트 형식 (OCR, 얼굴, 키 프레임 등)을 지정 합니다.

## <a name="root-elements-of-the-insights"></a>정보에 대 한 루트 요소

|Name|Description|
|---|---|
|accountId|재생 목록의 VI 계정 ID입니다.|
|id|재생 목록의 ID입니다.|
|name|재생 목록의 이름입니다.|
|description|재생 목록의 설명입니다.|
|userName|재생 목록을 만든 사용자의 이름입니다.|
|created|재생 목록을 만든 시간입니다.|
|privacyMode|재생 목록의 개인 모드(프라이빗/공용)입니다.|
|state|재생 목록의 상태(uploaded(업로드됨), processing(처리 중), processed(처리됨), failed(실패), quarantined(격리됨))입니다.|
|isOwned|현재 사용자가 재생 목록을 만들었는지 여부를 나타냅니다.|
|isEditable|현재 사용자에게 재생 목록을 편집할 수 있는 권한이 있는지 여부를 나타냅니다.|
|isBase|재생 목록이 기본 재생 목록(비디오)인지, 아니면 다른 비디오로 구성된 재생 목록(파생됨)인지를 나타냅니다.|
|durationInSeconds|재생 목록의 총 기간입니다.|
|summarizedInsights|하나의 [summarizedInsights](#summarizedinsights)가 포함됩니다.
|videos|재생 목록을 구성하는 [videos](#videos)의 목록입니다.<br/>이 재생 목록이 다른 비디오(파생됨)의 시간 범위로 구성된 경우 이 목록의 비디오에는 포함된 시간 범위의 데이터만 포함됩니다.|

```json
{
  "accountId": "bca61527-1221-bca6-1527-a90000002000",
  "id": "abc3454321",
  "name": "My first video",
  "description": "I am trying VI",
  "userName": "Some name",
  "created": "2018/2/2 18:00:00.000",
  "privacyMode": "Private",
  "state": "Processed",
  "isOwned": true,
  "isEditable": false,
  "isBase": false,
  "durationInSeconds": 120, 
  "summarizedInsights" : { . . . }
  "videos": [{ . . . }]
}
```

## <a name="summarizedinsights"></a>summarizedInsights

이 섹션에서는 인사이트의 요약을 보여 줍니다.

|attribute | Description|
|---|---|
|name|비디오의 이름입니다. 예: Azure Monitor|
|id|비디오의 ID입니다. 예: 63c6d532ff|
|privacyMode|분석에는 **Private**, **Public** 모드 중 하나가 있을 수 있습니다. **Public** - 비디오가 계정의 모든 사용자와 비디오에 대한 링크가 있는 모든 사용자에게 표시됩니다. **프라이빗** - 비디오가 계정의 모든 사용자에게 표시됩니다.|
|duration|인사이트가 발생한 시간을 설명하는 하나의 기간이 포함됩니다. 기간은 초 단위입니다.|
|thumbnailVideoId|썸네일을 가져온 비디오의 ID입니다.
|thumbnailId|비디오의 썸네일 ID입니다. 실제 미리 보기를 가져오려면 [get-help](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail) 를 호출 하 고 ThumbnailVideoId 및 thumbnailId를 전달 합니다.|
|얼굴/animatedCharacters|0개 이상의 얼굴이 포함될 수 있습니다. 자세한 내용은 [얼굴/animatedCharacters](#facesanimatedcharacters)를 참조 하세요.|
|키워드|0개 이상의 키워드가 포함될 수 있습니다. 자세한 내용은 [keywords](#keywords)를 참조하세요.|
|감정|0개 이상의 감정이 포함될 수 있습니다. 자세한 내용은 [sentiments](#sentiments)를 참조하세요.|
|audioEffects| 0개 이상의 오디오 효과가 포함될 수 있습니다. 자세한 내용은 [audioEffects](#audioeffects)를 참조하세요.|
|레이블| 0개 이상의 레이블이 포함될 수 있습니다. 자세한 내용은 [labels](#labels)를 참조하세요.|
|brands| 0개 이상의 브랜드가 포함될 수 있습니다. 자세한 내용은 [brands](#brands)를 참조하세요.|
|통계 | 자세한 내용은 [statistics](#statistics)를 참조하세요.|
|emotions| 0개 이상의 감정이 포함될 수 있습니다. 자세한 내용은 [emotions](#emotions)를 참조하세요.|
|topics|0개 이상의 주제가 포함될 수 있습니다. [항목](#topics) 에 대해 자세히 설명 합니다.|

## <a name="videos"></a>videos

|Name|Description|
|---|---|
|accountId|비디오의 VI 계정 ID입니다.|
|id|비디오의 ID입니다.|
|name|비디오의 이름입니다.
|state|비디오의 재생 목록의 상태(uploaded, processing, processed, failed, quarantined)입니다.|
|processingProgress|처리하는 동안의 처리 진행률입니다(예: 20%).|
|failureCode|처리하는 데 실패한 경우의 오류 코드입니다(예: 'UnsupportedFileType').|
|failureMessage|처리하는 데 실패한 경우의 오류 메시지입니다.|
|externalId|비디오의 외부 ID입니다(사용자가 지정한 경우).|
|externalUrl|비디오의 외부 URL입니다(사용자가 지정한 경우).|
|metadata|비디오의 외부 메타데이터입니다(사용자가 지정한 경우).|
|isAdult|비디오가 수동으로 검토되고 성인 비디오로 식별되었는지 여부를 나타냅니다.|
|insights|인사이트 개체입니다. 자세한 내용은 [insights](#insights)를 참조하세요.|
|thumbnailId|비디오의 썸네일 ID입니다. 실제 미리 보기 호출을 가져오려면 [축소판 그림](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail) 을 표시 하 고 비디오 ID와 thumbnailId을 전달 합니다.|
|publishedUrl|비디오를 스트림하는 URL입니다.|
|publishedUrlProxy|비디오를 스트림하는 URL입니다(Apple 디바이스용).|
|viewToken|비디오를 스트림하기 위한 단기 보기 토큰입니다.|
|sourceLanguage|비디오의 소스 언어입니다.|
|language|비디오의 실제 언어입니다(변환).|
|indexingPreset|비디오를 인덱싱하는 데 사용되는 기본 설정입니다.|
|streamingPreset|비디오를 게시하는 데 사용되는 기본 설정입니다.|
|linguisticModelId|비디오를 인쇄하는 데 사용되는 CRIS 모델입니다.|
|통계 | 자세한 내용은 [통계](#statistics)를 참조 하세요.|

```json
{
    "videos": [{
        "accountId": "2cbbed36-1972-4506-9bc7-55367912df2d",
        "id": "142a356aa6",
        "state": "Processed",
        "privacyMode": "Private",
        "processingProgress": "100%",
        "failureCode": "General",
        "failureMessage": "",
        "externalId": null,
        "externalUrl": null,
        "metadata": null,
        "insights": {. . . },
        "thumbnailId": "89d7192c-1dab-4377-9872-473eac723845",
        "publishedUrl": "https://videvmediaservices.streaming.mediaservices.windows.net:443/d88a652d-334b-4a66-a294-3826402100cd/Xamarine.ism/manifest",
        "publishedProxyUrl": null,
        "viewToken": "Bearer=<token>",
        "sourceLanguage": "En-US",
        "language": "En-US",
        "indexingPreset": "Default",
        "linguisticModelId": "00000000-0000-0000-0000-000000000000"
    }],
}
```
### <a name="insights"></a>insights

각 정보 (예: 성적 증명서, 면, 브랜드 등)에는 고유 요소 목록 (예: face1, face2, face3)이 포함 되 고 각 요소에는 고유한 메타 데이터와 해당 인스턴스 목록 (추가 선택적 메타 데이터가 포함 된 시간 범위)이 포함 됩니다.

한 면에 ID, 이름, 미리 보기, 기타 메타 데이터 및 임시 인스턴스 목록 (예: 00:00:05 – 00:00:10, 00:01:00-00:02:30 및 00:41:21 – 00:41:49)이 있을 수 있습니다. 각 임시 인스턴스에는 추가 메타 데이터가 있을 수 있습니다. 예를 들어 얼굴의 사각형을 조정합니다(20,230,60,60).

|버전|코드 버전|
|---|---|
|sourceLanguage|비디오의 소스 언어입니다(하나의 마스터 언어를 가정함). [BCP-47](https://tools.ietf.org/html/bcp47) 문자열 형식입니다.|
|language|인사이트 언어입니다(소스 언어에서 변환됨). [BCP-47](https://tools.ietf.org/html/bcp47) 문자열 형식입니다.|
|대본|[성적 증명서](#transcript) 입니다.|
|ocr|[OCR](#ocr) 정보를 파악 합니다.|
|키워드|[키워드](#keywords) 를 파악 합니다.|
|blocks|하나 이상의 [blocks](#blocks)가 포함될 수 있습니다.|
|얼굴/animatedCharacters|[얼굴/animatedCharacters](#facesanimatedcharacters) 통찰력입니다.|
|레이블|[레이블](#labels) 정보입니다.|
|샷|[샷을](#shots) 파악 합니다.|
|brands|[브랜드](#brands) 통찰력.|
|audioEffects|[오디오 효과](#audioeffects) 통찰력입니다.|
|감정|[정서](#sentiments) 통찰력입니다.|
|visualContentModeration|[Visualcontentmoderation](#visualcontentmoderation) 정보입니다.|
|textualContentModeration|[TextualContentModeration](#textualcontentmoderation) 통찰력입니다.|
|emotions| [감정을](#emotions) 통찰력입니다.|
|topics|[항목](#topics) 에 대해 자세히 설명 합니다.|

예:

```json
{
  "version": "0.9.0.0",
  "sourceLanguage": "en-US",
  "language": "es-ES",
  "transcript": ...,
  "ocr": ...,
  "keywords": ...,
  "faces": ...,
  "labels": ...,
  "shots": ...,
  "brands": ...,
  "audioEffects": ...,
  "sentiments": ...,
  "visualContentModeration": ...,
  "textualContentModeration": ...
}
```

#### <a name="blocks"></a>blocks

attribute | Description
---|---
id|블록의 ID입니다.|
인스턴스|이 블록에 대한 시간 범위의 목록입니다.|

#### <a name="transcript"></a>대본

|Name|Description|
|---|---|
|id|줄 ID입니다.|
|text|자체 대본입니다.|
|language|대본 언어입니다. 각 줄마다 다른 언어가 사용될 수 있는 대본을 지원하기 위한 요소입니다.|
|인스턴스|이 줄이 나타나는 시간 범위 목록입니다. 인스턴스가 대본인 경우 인스턴스는 하나만 있습니다.|

예:

```json
"transcript": [
{
    "id": 0,
    "text": "Hi I'm Doug from office.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    }
    ]
},
{
    "id": 1,
    "text": "I have a guest. It's Michelle.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:02.7200000",
        "end": "00:00:03.9600000"
    }
    ]
}
] 
```

#### <a name="ocr"></a>ocr

|Name|Description|
|---|---|
|id|OCR 줄 ID입니다.|
|text|OCR 텍스트입니다.|
|신뢰도|인식 신뢰도입니다.|
|language|OCR 언어입니다.|
|인스턴스|이 OCR이 표시된 시간 범위 목록(동일한 OCR이 여러 번 나타날 수 있음)입니다.|
|키|OCR 사각형의 높이입니다.|
|top|Px의 위쪽 위치입니다.|
|left| Px의 왼쪽 위치입니다.|
|width|OCR 사각형의 너비입니다.|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 675.971,
      "height": 35,
      "language": "en-US",
      "left": 31,
      "top": 97,
      "width": 400,      
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    }
  ],
```

#### <a name="keywords"></a>키워드

|Name|Description|
|---|---|
|id|키워드 ID입니다.|
|text|키워드 텍스트입니다.|
|신뢰도|키워드의 인식 신뢰도입니다.|
|language|키워드 언어(번역된 경우)입니다.|
|인스턴스|키워드가 표시되는 시간 범위 목록(키워드가 여러 번 나타날 수 있음)입니다.|

```json
{
    id: 0,
    text: "technology",
    confidence: 1,
    language: "en-US",
    instances: [{
            adjustedStart: "0:05:15.782",
            adjustedEnd: "0:05:16.249",
            start: "0:05:15.782",
            end: "0:05:16.249"
    },
    {
            adjustedStart: "0:04:54.761",
            adjustedEnd: "0:04:55.228",
            start: "0:04:54.761",
            end: "0:04:55.228"
    }]
}
```

#### <a name="facesanimatedcharacters"></a>얼굴/animatedCharacters

`animatedCharacters``faces`비디오가 애니메이션 문자 모델로 인덱싱되는 경우 요소는를 대체 합니다. Custom Vision에서 사용자 지정 모델을 사용 하 여이 작업을 수행 하 Video Indexer는 키 프레임에서 실행 됩니다.

얼굴 (애니메이션 문자 아님)이 있는 경우 Video Indexer 모든 비디오 프레임에서 Face API를 사용 하 여 얼굴 및 유명인을 감지 합니다.

|Name|Description|
|---|---|
|id|얼굴 ID입니다.|
|name|얼굴의 이름입니다. 'Unknown #0, 즉 식별된 유명인 또는 고객이 학습한 사람일 수 있습니다.|
|신뢰도|얼굴 인식 신뢰도입니다.|
|description|유명인에 대한 설명입니다. |
|thumbnailId|얼굴 썸네일의 ID입니다.|
|knownPersonId|알려진 사람인 경우 내부 ID입니다.|
|referenceId|Bing 유명인인 경우 Bing ID입니다.|
|referenceType|현재 Bing만 지원됩니다.|
|title|유명인인 경우 직함(예: "Microsoft CEO")입니다.|
|imageUrl|유명인인 경우 이미지 URL입니다.|
|인스턴스|지정된 시간 범위 내에 얼굴이 나타난 인스턴스입니다. 인스턴스마다 thumbnailsId가 있습니다. |

```json
"faces": [{
    "id": 2002,
    "name": "Xam 007",
    "confidence": 0.93844,
    "description": null,
    "thumbnailId": "00000000-aee4-4be2-a4d5-d01817c07955",
    "knownPersonId": "8340004b-5cf5-4611-9cc4-3b13cca10634",
    "referenceId": null,
    "title": null,
    "imageUrl": null,
    "instances": [{
        "thumbnailsIds": ["00000000-9f68-4bb2-ab27-3b4d9f2d998e",
        "cef03f24-b0c7-4145-94d4-a84f81bb588c"],
        "adjustedStart": "00:00:07.2400000",
        "adjustedEnd": "00:00:45.6780000",
        "start": "00:00:07.2400000",
        "end": "00:00:45.6780000"
    },
    {
        "thumbnailsIds": ["00000000-51e5-4260-91a5-890fa05c68b0"],
        "adjustedStart": "00:10:23.9570000",
        "adjustedEnd": "00:10:39.2390000",
        "start": "00:10:23.9570000",
        "end": "00:10:39.2390000"
    }]
}]
```

#### <a name="labels"></a>레이블

|Name|Description|
|---|---|
|id|레이블 ID입니다.|
|name|레이블 이름(예: '컴퓨터', 'TV')입니다.|
|language|레이블 이름 언어(번역된 경우)입니다. BCP-47|
|인스턴스|레이블이 나타나는 시간 범위 목록(레이블은 여러 번 나타날 수 있음)입니다. 인스턴스마다 신뢰도 필드가 있습니다. |


```json
"labels": [
    {
      "id": 0,
      "name": "person",
      "language": "en-US",
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 00.0000000",
          "end": "00: 00: 25.6000000"
        },
        {
          "confidence": 1.0,
          "start": "00: 01: 33.8670000",
          "end": "00: 01: 39.2000000"
        }
      ]
    },
    {
      "name": "indoor",
      "language": "en-US",
      "id": 1,
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 06.4000000",
          "end": "00: 00: 07.4670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 09.6000000",
          "end": "00: 00: 10.6670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 11.7330000",
          "end": "00: 00: 20.2670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 21.3330000",
          "end": "00: 00: 25.6000000"
        }
      ]
    }
  ] 
```

#### <a name="scenes"></a>장면

|Name|Description|
|---|---|
|id|장면 ID입니다.|
|인스턴스|이 장면의 시간 범위 목록입니다 (장면에는 인스턴스가 1 개만 있을 수 있음).|

```json
"scenes":[  
    {  
      "id":0,
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },
    {  
      "id":1,
      "instances":[  
          {  
            "start":"0:00:06.34",
            "end":"0:00:47.047",
            "duration":"0:00:40.707"
          }
      ]
    },

]
```

#### <a name="shots"></a>샷

|Name|Description|
|---|---|
|id|샷 ID입니다.|
|keyFrames|샷 내의 키 프레임 목록 (각각 ID와 인스턴스 시간 범위의 목록이 있음). 각 키 프레임 인스턴스에는 키 프레임의 미리 보기 ID를 포함 하는 thumbnailId 필드가 있습니다.|
|인스턴스|이 샷의 시간 범위 목록입니다 (샷의 인스턴스는 1 개만 있을 수 있음).|

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

#### <a name="brands"></a>brands

음성 텍스트 변환 전사 및/또는 Video OCR에서 감지된 비즈니스 및 제품 브랜드 이름입니다. 여기에는 브랜드 또는 로고 감지의 시각적 인식이 포함되지 않습니다.

|Name|Description|
|---|---|
|id|브랜드 ID입니다.|
|name|브랜드 이름입니다.|
|referenceId | 브랜드의 Wikipedia URL에 대한 접미사입니다. 예를 들어 "Target_Corporation"은의 접미사입니다 [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation) .
|referenceUrl | 있는 경우 브랜드의 Wikipedia URL입니다. 예를 들어 [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation) 와 같습니다.
|description|브랜드 설명입니다.|
|tags|이 브랜드와 연결된 미리 정의된 태그의 목록입니다.|
|신뢰도|Video Indexer 브랜드 감지기의 신뢰도 값(0-1)입니다.|
|인스턴스|이 브랜드의 시간 범위에 대한 목록입니다. 각 인스턴스에는 이 브랜드가 전사 또는 OCR에 표시되는지 여부를 나타내는 brandType이 있습니다.|

```json
"brands": [
{
    "id": 0,
    "name": "MicrosoftExcel",
    "referenceId": "Microsoft_Excel",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft_Excel",
    "referenceType": "Wiki",
    "description": "Microsoft Excel is a sprea..",
    "tags": [],
    "confidence": 0.975,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 00: 31.3000000",
        "end": "00: 00: 39.0600000"
    }
    ]
},
{
    "id": 1,
    "name": "Microsoft",
    "referenceId": "Microsoft",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft",
    "description": "Microsoft Corporation is...",
    "tags": [
    "competitors",
    "technology"
    ],
    "confidence": 1.0,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 01: 44",
        "end": "00: 01: 45.3670000"
    },
    {
        "brandType": "Ocr",
        "start": "00: 01: 54",
        "end": "00: 02: 45.3670000"
    }
    ]
}
]
```

#### <a name="statistics"></a>통계

|Name|Description|
|---|---|
|CorrespondenceCount|동영상의 해당 항목 수입니다.|
|SpeakerWordCount|화자별 단어 수입니다.|
|SpeakerNumberOfFragments|비디오에서 화자가 있는 조각의 양입니다.|
|SpeakerLongestMonolog|화자의 가장 긴 단독 발언입니다. 가장 긴 단독 발언 내에 화자의 침묵이 있으면 포함됩니다. 단독 발언의 시작과 끝 부분에 있는 침묵은 제거됩니다.| 
|SpeakerTalkToListenRatio|화자의 단독 발언에 소요된 시간(그 사이 침묵 제외)을 비디오의 총 시간으로 나눈 값을 기반으로 계산됩니다. 시간은 소수점 이하 세 자리에서 반올림됩니다.|

#### <a name="audioeffects"></a>audioEffects

|Name|Description|
|---|---|
|id|오디오 효과 ID입니다.|
|형식|오디오 효과 유형(예: 박수, 음성, 음소거)입니다.|
|인스턴스|이 오디오 효과가 나타나는 시간 범위 목록입니다.|

```json
"audioEffects": [
{
    "id": 0,
    "type": "Clapping",
    "instances": [
    {
        "start": "00:00:00",
        "end": "00:00:03"
    },
    {
        "start": "00:01:13",
        "end": "00:01:21"
    }
    ]
}
]
```

#### <a name="sentiments"></a>감정

감정은 sentimentType 필드(긍정적/중립/부정적)로 집계됩니다. 예: 0-0.1, 0.1-0.2.

|Name|Description|
|---|---|
|id|감정 ID입니다.|
|averageScore |해당 감정 유형의 모든 인스턴스의 모든 점수 평균 – 긍정적/중립/부정적|
|인스턴스|이 감정이 나타나는 시간 범위 목록입니다.|
|sentimentType |가능한 유형은 'Positive', 'Neutral' 또는 'Negative'입니다.|

```json
"sentiments": [
{
    "id": 0,
    "averageScore": 0.87,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:23",
        "end": "00:00:41"
    }
    ]
}, {
    "id": 1,
    "averageScore": 0.11,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:13",
        "end": "00:00:21"
    }
    ]
}
]
```

#### <a name="visualcontentmoderation"></a>visualContentModeration

visualContentModeration 블록에는 Video Indexer에서 잠재적 성인 콘텐츠가 있는 것으로 확인된 시간 범위가 포함됩니다. visualContentModeration이 비어있는 경우에는 식별된 성인 콘텐츠가 없는 것입니다.

성인 또는 외설 콘텐츠가 포함된 것으로 확인된 비디오는 프라이빗 보기만 가능합니다. 콘텐츠를 사람이 검토하도록 요청을 제출할 수 있으며 이 경우 IsAdult 특성에 사람이 검토한 결과가 포함됩니다.

|Name|Description|
|---|---|
|id|시각 콘텐츠 조정 ID입니다.|
|adultScore|성인 점수(Content Moderator 기반)입니다.|
|racyScore|외설 점수(Content Moderator 기반)입니다.|
|인스턴스|이 시각 콘텐츠 조정이 나타난 시간 범위 목록입니다.|

```json
"VisualContentModeration": [
{
    "id": 0,
    "adultScore": 0.00069,
    "racyScore": 0.91129,
    "instances": [
    {
        "start": "00:00:25.4840000",
        "end": "00:00:25.5260000"
    }
    ]
},
{
    "id": 1,
    "adultScore": 0.99231,
    "racyScore": 0.99912,
    "instances": [
    {
        "start": "00:00:35.5360000",
        "end": "00:00:35.5780000"
    }
    ]
}
] 
```

#### <a name="textualcontentmoderation"></a>textualContentModeration 

|Name|Description|
|---|---|
|id|텍스트 콘텐츠 조정 ID입니다.|
|bannedWordsCount |금지된 단어의 수입니다.|
|bannedWordsRatio |총 단어 수에 대한 비율입니다.|

#### <a name="emotions"></a>emotions

Video Indexer는 음성 및 오디오 신호를 기반으로 감정을를 식별 합니다. 식별 되는 emotion는 분노, sadness 또는 일 수 있습니다.

|Name|Description|
|---|---|
|id|감정 ID입니다.|
|형식|음성 및 오디오 신호를 기반으로 식별 된 emotion 순간입니다. Emotion, sadness, 또는 그에 대 한 것입니다.|
|인스턴스|이 감정이 나타난 시간 범위의 목록입니다.|

```json
"emotions": [{
    "id": 0,
    "type": "Fear",
    "instances": [{
      "adjustedStart": "0:00:39.47",
      "adjustedEnd": "0:00:45.56",
      "start": "0:00:39.47",
      "end": "0:00:45.56"
    },
    {
      "adjustedStart": "0:07:19.57",
      "adjustedEnd": "0:07:23.25",
      "start": "0:07:19.57",
      "end": "0:07:23.25"
    }]
  },
  {
    "id": 1,
    "type": "Anger",
    "instances": [{
      "adjustedStart": "0:03:55.99",
      "adjustedEnd": "0:04:05.06",
      "start": "0:03:55.99",
      "end": "0:04:05.06"
    },
    {
      "adjustedStart": "0:04:56.5",
      "adjustedEnd": "0:05:04.35",
      "start": "0:04:56.5",
      "end": "0:05:04.35"
    }]
  },
  {
    "id": 2,
    "type": "Joy",
    "instances": [{
      "adjustedStart": "0:12:23.68",
      "adjustedEnd": "0:12:34.76",
      "start": "0:12:23.68",
      "end": "0:12:34.76"
    },
    {
      "adjustedStart": "0:12:46.73",
      "adjustedEnd": "0:12:52.8",
      "start": "0:12:46.73",
      "end": "0:12:52.8"
    },
    {
      "adjustedStart": "0:30:11.29",
      "adjustedEnd": "0:30:16.43",
      "start": "0:30:11.29",
      "end": "0:30:16.43"
    },
    {
      "adjustedStart": "0:41:37.23",
      "adjustedEnd": "0:41:39.85",
      "start": "0:41:37.23",
      "end": "0:41:39.85"
    }]
  },
  {
    "id": 3,
    "type": "Sad",
    "instances": [{
      "adjustedStart": "0:13:38.67",
      "adjustedEnd": "0:13:41.3",
      "start": "0:13:38.67",
      "end": "0:13:41.3"
    },
    {
      "adjustedStart": "0:28:08.88",
      "adjustedEnd": "0:28:18.16",
      "start": "0:28:08.88",
      "end": "0:28:18.16"
    }]
  }
],
```

#### <a name="topics"></a>topics

Video Indexer는 전사에서 주요 주제를 추정합니다. 가능 하면 두 번째 수준 [IPTC](https://iptc.org/standards/media-topics/) 분류가 포함 됩니다. 

|Name|Description|
|---|---|
|id|주제 ID입니다.|
|name|주제 이름입니다(예: "제약").|
|referenceId|주제 계층 구조를 반영하는 이동 경로입니다. 예: "보건 및 복지 / 의료 및 건강 / 제약"|
|신뢰도|범위의 신뢰도 점수[0,1]입니다. 점수가 높을수록 더 신뢰할 수 있습니다.|
|language|주제에 사용된 언어입니다.|
|iptcName|IPTC 미디어 코드 이름입니다(검색된 경우).|
|인스턴스 |현재 Video Indexer는 주제를 시간 간격으로 인덱싱하지 않으므로 전체 비디오가 간격으로 사용됩니다.|

```json
"topics": [{
    "id": 0,
    "name": "INTERNATIONAL RELATIONS",
    "referenceId": "POLITICS AND GOVERNMENT/FOREIGN POLICY/INTERNATIONAL RELATIONS",
    "referenceType": "VideoIndexer",
    "confidence": 1,
    "language": "en-US",
    "instances": [{
        "adjustedStart": "0:00:00",
        "adjustedEnd": "0:03:36.25",
        "start": "0:00:00",
        "end": "0:03:36.25"
    }]
}, {
    "id": 1,
    "name": "Politics and Government",
    "referenceType": "VideoIndexer",
    "iptcName": "Politics",
    "confidence": 0.9041,
    "language": "en-US",
    "instances": [{
        "adjustedStart": "0:00:00",
        "adjustedEnd": "0:03:36.25",
        "start": "0:00:00",
        "end": "0:03:36.25"
    }]
}]
. . .
```

## <a name="next-steps"></a>다음 단계

[Video Indexer 개발자 포털](https://api-portal.videoindexer.ai)

위젯을 애플리케이션에 포함하는 방법에 대한 자세한 내용은 [애플리케이션에 Video Indexer 위젯 포함](video-indexer-embed-widgets.md)을 참조하세요. 

