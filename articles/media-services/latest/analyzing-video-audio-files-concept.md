---
title: Azure Media Services로 비디오 및 오디오 파일 분석 | Microsoft Docs
description: Azure Media Services를 사용하면 AudioAnalyzerPreset 및 VideoAnalyzerPreset을 사용하여 오디오 및 비디오 콘텐츠를 분석할 수 있습니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/21/2019
ms.author: juliako
ms.openlocfilehash: a4c643ecff5c33ec19c607da6ef8db41cfeb90c6
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63762824"
---
# <a name="analyzing-video-and-audio-files"></a>비디오 및 오디오 파일 분석

Azure Media Services v3을 사용하면 AMS v3 분석기 사전 설정(이 문서에 설명되어 있음)을 통해 Video Indexer로 비디오 및 오디오 파일에서 인사이트를 추출할 수 있습니다. 더 자세한 인사이트가 필요하면 Video Indexer를 직접 사용해 보세요. Video Indexer 및 Media Services 분석기 미리 설정을 사용하려는 경우를 이해하려면 [비교 문서](../video-indexer/compare-video-indexer-with-media-services-presets.md)를 확인하세요.

Media Services v3 사전 설정을 사용하여 콘텐츠를 분석하려면 **Transform**을 만들고 다음 사전 설정 중 하나를 사용하는 **Job**을 제출합니다. [VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset) 또는 **AudioAnalyzerPreset**. 다음 문서에서는 **VideoAnalyzerPreset**을 사용하는 방법을 설명합니다. [자습서: Azure Media Services를 통해 비디오 분석](analyze-videos-tutorial-with-api.md).

> [!NOTE]
> 비디오 또는 오디오 분석기 사전 설정을 사용할 때는 Azure Portal을 통해 S3 미디어 예약 10단위를 갖도록 계정을 설정합니다. 자세한 내용은 [미디어 처리 크기 조정](media-reserved-units-cli-how-to.md)을 참조하세요.

## <a name="built-in-presets"></a>기본 제공 미리 설정

Media Services에서 현재 지원하는 기본 제공 분석기 미리 설정은 다음과 같습니다.  

|**미리 설정 이름**|**시나리오**|**세부 정보**|
|---|---|---|
|[AudioAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|오디오 분석|사전 설정은 음성 기록을 포함하여 미리 정의된 AI 기반 분석 작업 세트를 적용합니다. 현재 사전 설정은 단일 언어 음성을 포함하는 단일 오디오 트랙을 사용하여 콘텐츠를 처리하도록 지원합니다. BCP-47 형식의 ‘language tag-region’을 사용하여 입력에서 오디오 페이로드의 언어를 지정할 수 있습니다. 지원되는 언어는 영어('en-US' 및 'en-GB'), 스페인어('es-ES' 및 'es-MX'), 프랑스어('fr-FR'), 이탈리아어('it-IT'), 일본어('ja-JP'), 포르투갈어('pt-BR'), 중국어('zh-CN'), 독일어('de-DE'), 아라비아어('ar-EG'), 러시아어('ru-RU'), 힌디어('hi-IN') 및 한국어('ko-KR')입니다.<br/><br/> 언어가 지정되지 않았거나 Null로 설정된 경우 자동 언어 검색이 첫 번째 검색된 언어를 선택하고 파일의 지속시간 동안 선택된 언어로 처리합니다. 자동 언어 감지 기능은 현재 영어, 중국어, 프랑스어, 독일어, 이탈리아어, 일본어, 스페인어, 러시아어 및 포르투갈어를 지원합니다. 현재 첫 번째 언어가 검색된 후 언어 간에 동적으로 전환하는 기능은 지원되지 않습니다. 자동 언어 검색 기능은 분명히 구별할 수 있는 음성이 포함된 오디오 녹음에 가장 적합합니다. 자동 언어 감지가 언어를 찾지 못하면 전사가 영어로 폴백됩니다.|
|[VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset)|오디오 및 비디오 분석|오디오 및 비디오 모두에서 통찰력(풍부한 메타데이터)을 추출하고 JSON 형식 파일을 출력합니다. 비디오 파일을 처리할 때 오디오 통찰력만 추출할지 여부를 지정할 수 있습니다. 자세한 내용은 [비디오 분석](analyze-videos-tutorial-with-api.md)을 참조하세요.|
|[FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset)||있는 모든 얼굴을 감지 하기 위해 비디오를 분석할 때 사용할 설정을 설명 합니다.|

### <a name="audioanalyzerpreset"></a>AudioAnalyzerPreset

미리 설정을 사용하면 오디오 또는 비디오 파일에서 여러 오디오 인사이트를 추출할 수 있습니다. 출력에는 오디오 대본의 VTT 파일과 JSON 파일(모든 인사이트 포함)이 포함됩니다. 이 사전 설정은 입력 파일의 언어를 [BCP47](https://tools.ietf.org/html/bcp47) 문자열의 형태로 지정하는 속성을 허용합니다. 오디오 인사이트는 다음 정보를 포함합니다.

* 오디오 전사 – 타임 스탬프가 있는 음성의 대본입니다. 여러 언어 지원
* 화자 인덱싱 – 화자와 해당 음성의 매핑입니다.
* 음성 감정 분석 - 오디오 전사에 대해 수행한 감정 분석 결과입니다.
* 키워드 – 오디오 전사에서 추출한 키워드입니다.

### <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

미리 설정을 사용하면 비디오 파일에서 여러 오디오 및 비디오 인사이트를 추출할 수 있습니다. 출력에는 JSON 파일(모든 인사이트 포함), 비디오 대본의 VTT 파일 및 썸네일 컬렉션이 포함됩니다. 이 사전 설정은 [BCP47](https://tools.ietf.org/html/bcp47) 문자열(비디오의 언어를 나타냄)을 속성으로 수락합니다. 비디오 인사이트에는 앞서 언급한 모든 오디오 인사이트와 다음과 같은 추가 항목이 포함됩니다.

* 얼굴 추적 - 비디오에 얼굴이 있는 시간입니다. 얼굴마다 얼굴 ID와 해당 썸네일 컬렉션이 있습니다.
* 시각적 텍스트 - 광학 문자 인식을 통해 감지된 텍스트입니다. 텍스트에는 타임스탬프가 지정되며 키워드(오디오 대본과 함께)를 추출하는 데 사용되기도 합니다.
* 키 프레임 – 비디오에서 추출한 키 프레임 컬렉션입니다.
* 시각적 콘텐츠 조정 – 본질적으로 성인용 또는 선정적이라는 플래그가 지정된 비디오의 일부분입니다.
* 주석 – 미리 정의된 개체 모델을 기반으로 비디오에 주석을 추가한 결과입니다.

##  <a name="insightsjson-elements"></a>insights.json 요소

비디오 또는 오디오에서 찾은 모든 인사이트가 포함된 JSON 파일(insights.json)이 출력에 포함됩니다. json에는 다음 요소가 포함될 수 있습니다.

### <a name="transcript"></a>대본

|이름|설명|
|---|---|
|id|줄 ID입니다.|
|text|자체 대본입니다.|
|언어|대본 언어입니다. 각 줄마다 다른 언어가 사용될 수 있는 대본을 지원하기 위한 요소입니다.|
|인스턴스|이 줄이 나타나는 시간 범위 목록입니다. 인스턴스가 대본인 경우 인스턴스는 하나만 있습니다.|

예제:

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

### <a name="ocr"></a>ocr

|이름|설명|
|---|---|
|id|OCR 줄 ID입니다.|
|text|OCR 텍스트입니다.|
|신뢰도|인식 신뢰도입니다.|
|언어|OCR 언어입니다.|
|인스턴스|이 OCR이 표시된 시간 범위 목록(동일한 OCR이 여러 번 나타날 수 있음)입니다.|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 0.91,
      "language": "en-US",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    },
    {
      "id": 1,
      "text": "NOTICIAS EN VIVO",
      "confidence": 0.9,
      "language": "es-ES",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:28"
        },
        {
          "start": "00:00:32",
          "end": "00:00:38"
        }
      ]
    }
  ],
```

### <a name="faces"></a>얼굴

|이름|설명|
|---|---|
|id|얼굴 ID입니다.|
|이름|얼굴 이름입니다. ‘Unknown #0’, 확인된 유명인 또는 고객 교육을 받은 사람일 수 있습니다.|
|신뢰도|얼굴 인식 신뢰도입니다.|
|description|유명인에 대한 설명입니다. |
|thumbnailId|얼굴 썸네일의 ID입니다.|
|knownPersonId|알려진 사람인 경우 내부 ID입니다.|
|referenceId|Bing 유명인인 경우 Bing ID입니다.|
|referenceType|현재는 그냥 Bing입니다.|
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

### <a name="shots"></a>샷

|이름|설명|
|---|---|
|id|샷 ID입니다.|
|keyFrames|샷 내의 키 프레임 목록(각각 ID와 인스턴스 시간 범위 목록이 있음)입니다. 키 프레임 인스턴스에는 keyFrame의 썸네일 ID와 thumbnailId 필드가 있습니다.|
|인스턴스|이 샷의 시간 범위 목록(샷에는 인스턴스가 하나만 있음)입니다.|

```json
"Shots": [
    {
      "id": 0,
      "keyFrames": [
        {
          "id": 0,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",
              "start": "00: 00: 00.1670000",
              "end": "00: 00: 00.2000000"
            }
          ]
        }
      ],
      "instances": [
        {
            "thumbnailId": "00000000-0000-0000-0000-000000000000",  
          "start": "00: 00: 00.2000000",
          "end": "00: 00: 05.0330000"
        }
      ]
    },
    {
      "id": 1,
      "keyFrames": [
        {
          "id": 1,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",      
              "start": "00: 00: 05.2670000",
              "end": "00: 00: 05.3000000"
            }
          ]
        }
      ],
      "instances": [
        {
      "thumbnailId": "00000000-0000-0000-0000-000000000000",
          "start": "00: 00: 05.2670000",
          "end": "00: 00: 10.3000000"
        }
      ]
    }
  ]
```

### <a name="statistics"></a>통계

|이름|설명|
|---|---|
|CorrespondenceCount|동영상의 해당 항목 수입니다.|
|WordCount|화자별 단어 수입니다.|
|SpeakerNumberOfFragments|비디오에서 화자가 있는 조각의 양입니다.|
|SpeakerLongestMonolog|화자의 가장 긴 단독 발언입니다. 가장 긴 단독 발언 내에 화자의 침묵이 있으면 포함됩니다. 단독 발언의 시작과 끝 부분에 있는 침묵은 제거됩니다.| 
|SpeakerTalkToListenRatio|화자의 단독 발언에 소요된 시간(그 사이 침묵 제외)을 비디오의 총 시간으로 나눈 값을 기반으로 계산됩니다. 시간은 소수점 이하 세 자리에서 반올림됩니다.|


### <a name="sentiments"></a>감정

감정은 sentimentType 필드(긍정적/중립/부정적)로 집계됩니다. 예: 0-0.1, 0.1-0.2.

|이름|설명|
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

### <a name="labels"></a>레이블

|이름|설명|
|---|---|
|id|레이블 ID입니다.|
|이름|레이블 이름(예: '컴퓨터', 'TV')입니다.|
|언어|레이블 이름 언어(번역된 경우)입니다. BCP-47|
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

### <a name="keywords"></a>키워드

|이름|설명|
|---|---|
|id|키워드 ID입니다.|
|text|키워드 텍스트입니다.|
|신뢰도|키워드의 인식 신뢰도입니다.|
|언어|키워드 언어(번역된 경우)입니다.|
|인스턴스|키워드가 표시되는 시간 범위 목록(키워드가 여러 번 나타날 수 있음)입니다.|

```json
"keywords": [
{
    "id": 0,
    "text": "office",
    "confidence": 1.6666666666666667,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    },
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    }
    ]
},
{
    "id": 1,
    "text": "icons",
    "confidence": 1.4,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    },
    {
        "start": "00:00:13.9900000",
        "end": "00:00:15.6100000"
    }
    ]
}
] 
```

#### <a name="visualcontentmoderation"></a>visualContentModeration

visualContentModeration 블록에는 Video Indexer에서 잠재적 성인 콘텐츠가 있는 것으로 확인된 시간 범위가 포함됩니다. visualContentModeration이 비어있는 경우에는 식별된 성인 콘텐츠가 없는 것입니다.

성인 또는 외설 콘텐츠가 포함된 것으로 확인된 비디오는 비공개 보기만 가능합니다. 콘텐츠를 사람이 검토하도록 요청을 제출할 수 있으며 이 경우 IsAdult 특성에 사람이 검토한 결과가 포함됩니다.

|이름|설명|
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
## <a name="next-steps"></a>다음 단계

[자습서: Azure Media Services로 비디오 분석](analyze-videos-tutorial-with-api.md)
