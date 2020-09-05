---
title: 비디오 및 오디오 파일 분석
titleSuffix: Azure Media Services
description: Azure Media Services에서 AudioAnalyzerPreset 및 VideoAnalyzerPreset를 사용 하 여 오디오 및 비디오 콘텐츠를 분석 하는 방법을 알아봅니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 78ac75566eac120db4527b2e04324e7e6d40808c
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89258897"
---
# <a name="analyze-video-and-audio-files-with-azure-media-services"></a>Azure Media Services를 사용 하 여 비디오 및 오디오 파일 분석

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services v3를 사용 하면 Video Indexer를 통해 비디오 및 오디오 파일에서 정보를 추출할 수 있습니다. 이 문서에서는 이러한 정보를 추출 하는 데 사용 되는 Media Services v3 분석기 사전 설정을 설명 합니다. 더 자세한 인사이트가 필요하면 Video Indexer를 직접 사용해 보세요. Video Indexer와 Media Services analyzer 기본 설정을 사용 하는 경우를 이해 하려면 [비교 문서](../video-indexer/compare-video-indexer-with-media-services-presets.md)를 확인 하세요.

Media Services v3 사전 설정을 사용 하 여 콘텐츠를 분석 하려면 **변환을** 만들고 이러한 사전 설정 중 하나를 사용 하는 **작업** 을 제출 합니다. [VideoAnalyzerPreset](/rest/api/media/transforms/createorupdate#videoanalyzerpreset) 또는 **AudioAnalyzerPreset**. **VideoAnalyzerPreset**를 사용 하는 방법을 보여 주는 자습서는 [Azure Media Services으로 비디오 분석](analyze-videos-tutorial-with-api.md)을 참조 하세요.

> [!NOTE]
> 비디오 또는 오디오 분석기 사전 설정을 사용할 때는 Azure Portal을 통해 S3 미디어 예약 10단위를 갖도록 계정을 설정합니다. 자세한 내용은 [미디어 처리 크기 조정](media-reserved-units-cli-how-to.md)을 참조하세요.

## <a name="compliance-privacy-and-security"></a>규정 준수, 개인 정보 보호 및 보안

중요한 알림을 사용하는 경우 Video Indexer 사용 시 적용 가능한 모든 법률을 준수해야 하며, 다른 사용자의 권리를 위반하거나 다른 사용자에게 해로운 방법으로 Video Indexer 또는 다른 Azure 서비스를 사용해서는 안 됩니다. 처리 및 저장을 위해 Video Indexer 서비스에 모든 생체 인식 데이터를 비롯한 비디오를 업로드하려면, 비디오에 나온 모든 사람들의 적절한 동의를 비롯한 적절한 권한이 모두 있어야 합니다. Video Indexer의 규정 준수, 개인 정보 보호 및 보안에 대해 알아보려면 Microsoft [Cognitive Services 약관](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)을 참조하세요. Microsoft의 개인 정보 보호 의무 및 데이터 처리에 대한 내용은 Microsoft의 [개인정보처리방침](https://privacy.microsoft.com/PrivacyStatement), [Online Services 사용 약관](https://www.microsoft.com/licensing/product-licensing/products)(“OST”) 및 [Data Processing 추록](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67)(“DPA”)을 검토하세요. 데이터 보존, 삭제/소멸을 비롯한 추가 개인 정보 보호 정보는 OST 및 [여기](../video-indexer/faq.md)에서 사용할 수 있습니다. Video Indexer를 사용하면 Cognitive Services 사용 약관, OST, DPA 및 개인정보처리방침에 따라 바인딩되는 것에 동의합니다.

## <a name="built-in-presets"></a>기본 제공 미리 설정

Media Services에서 현재 지원하는 기본 제공 분석기 미리 설정은 다음과 같습니다.  

|**기본 설정 이름**|**시나리오**|**세부 정보**|
|---|---|---|
|[AudioAnalyzerPreset](/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|오디오 분석|사전 설정은 음성 기록을 포함하여 미리 정의된 AI 기반 분석 작업 세트를 적용합니다. 현재 사전 설정은 단일 언어 음성을 포함하는 단일 오디오 트랙을 사용하여 콘텐츠를 처리하도록 지원합니다. BCP-47 형식의 ‘language tag-region’을 사용하여 입력에서 오디오 페이로드의 언어를 지정할 수 있습니다. 지원 되는 언어는 영어 (' en-us ' 및 ' en-us '), 스페인어 (' es ' 및 ' es-mx '), 프랑스어 (' fr-fr '), 이탈리아어 (' it '), 일본어 (' ja-jp '), 포르투갈어 (' pt-BR '), 중국어 (' zh-cn '), 독일어 (' de-de '), 아랍어 (' ar-예 ' 및 ' ar-SY '), 러시아어 (' '), 힌디어 (' hi ') 및 한국어 (' ko ').<br/><br/> 언어가 지정 되지 않았거나 null로 설정 된 경우 자동 언어 검색은 검색 된 첫 번째 언어를 선택 하 고 파일 기간 동안 선택한 언어를 계속 합니다. 자동 언어 감지 기능은 현재 영어, 중국어, 프랑스어, 독일어, 이탈리아어, 일본어, 스페인어, 러시아어 및 포르투갈어를 지원합니다. 첫 번째 언어가 검색 된 후 언어 간의 동적 전환을 지원 하지 않습니다. 자동 언어 검색 기능은 분명히 구별할 수 있는 음성이 포함된 오디오 녹음에 가장 적합합니다. 자동 언어 검색에서 언어를 찾지 못하는 경우에는 해당 내용은 영어로 대체 됩니다.|
|[VideoAnalyzerPreset](/rest/api/media/transforms/createorupdate#videoanalyzerpreset)|오디오 및 비디오 분석|오디오 및 비디오 모두에서 통찰력(풍부한 메타데이터)을 추출하고 JSON 형식 파일을 출력합니다. 비디오 파일을 처리할 때 오디오 통찰력만 추출할지 여부를 지정할 수 있습니다. 자세한 내용은 [비디오 분석](analyze-videos-tutorial-with-api.md)을 참조하세요.|
|[FaceDetectorPreset](/rest/api/media/transforms/createorupdate#facedetectorpreset)|비디오에 있는 얼굴 감지|비디오를 분석 하 여 있는 모든 얼굴을 검색할 때 사용할 설정을 설명 합니다.|

### <a name="audioanalyzerpreset"></a>AudioAnalyzerPreset

미리 설정을 사용하면 오디오 또는 비디오 파일에서 여러 오디오 인사이트를 추출할 수 있습니다. 출력에는 오디오 대본의 VTT 파일과 JSON 파일(모든 인사이트 포함)이 포함됩니다. 이 사전 설정은 입력 파일의 언어를 [BCP47](https://tools.ietf.org/html/bcp47) 문자열의 형태로 지정하는 속성을 허용합니다. 오디오 인사이트는 다음 정보를 포함합니다.

* **오디오 녹음**: 타임 스탬프가 있는 음성의 기록입니다. 여러 언어가 지원 됩니다.
* **스피커 인덱싱**: 스피커와 해당 하는 음성 단어의 매핑입니다.
* **Speech 감정 analysis**: 오디오 녹음에 대해 수행 된 감정 분석의 출력입니다.
* **키워드**: 오디오 녹음에서 추출 되는 키워드입니다.

### <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

미리 설정을 사용하면 비디오 파일에서 여러 오디오 및 비디오 인사이트를 추출할 수 있습니다. 출력에는 JSON 파일(모든 인사이트 포함), 비디오 대본의 VTT 파일 및 썸네일 컬렉션이 포함됩니다. 이 사전 설정은 [BCP47](https://tools.ietf.org/html/bcp47) 문자열(비디오의 언어를 나타냄)을 속성으로 수락합니다. 비디오 인사이트에는 앞서 언급한 모든 오디오 인사이트와 다음과 같은 추가 항목이 포함됩니다.

* **얼굴 추적**: 비디오가 비디오에 표시 되는 시간입니다. 각 면에는 얼굴 ID와 해당 축소판 그림 컬렉션이 있습니다.
* **시각적 텍스트**: 광학 문자 인식을 통해 검색 되는 텍스트입니다. 텍스트는 타임 스탬프가 있으며 오디오 성적 외에도 키워드를 추출 하는 데 사용 됩니다.
* **키 프레임**: 비디오에서 추출한 키 프레임의 컬렉션입니다.
* **시각적 콘텐츠 중재**: 성인 또는 외설으로 플래그가 지정 된 비디오 부분입니다.
* **주석**: 미리 정의 된 개체 모델을 기반으로 비디오에 주석을 달고 생성 된 결과

## <a name="insightsjson-elements"></a>insights.json 요소

출력에는 비디오 또는 오디오에서 발견 된 모든 정보를 포함 하는 JSON 파일 (insights.js)이 포함 됩니다. JSON에는 다음 요소가 포함 될 수 있습니다.

### <a name="transcript"></a>대본

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

### <a name="ocr"></a>ocr

|Name|Description|
|---|---|
|id|OCR 줄 ID입니다.|
|text|OCR 텍스트입니다.|
|신뢰도|인식 신뢰도입니다.|
|language|OCR 언어입니다.|
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

|Name|Description|
|---|---|
|id|얼굴 ID입니다.|
|name|얼굴 이름입니다. ' 알 수 없는 #0 ', 식별 된 유명인 또는 고객이 학습 한 사용자 일 수 있습니다.|
|신뢰도|얼굴 인식 신뢰도입니다.|
|description|유명인에 대한 설명입니다. |
|thumbnailId|얼굴 썸네일의 ID입니다.|
|knownPersonId|내부 ID (알려진 사용자 인 경우)입니다.|
|referenceId|Bing ID (Bing 유명인 인 경우)입니다.|
|referenceType|현재는 그냥 Bing입니다.|
|title|제목 (예: "유명인")입니다 (예: "Microsoft CEO").|
|imageUrl|유명인 경우 이미지 URL입니다.|
|인스턴스|지정 된 시간 범위 내에서 얼굴이 나타난 인스턴스입니다. 인스턴스마다 thumbnailsId가 있습니다. |

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

|Name|Description|
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

|Name|설명|
|---|---|
|CorrespondenceCount|동영상의 해당 항목 수입니다.|
|WordCount|화자별 단어 수입니다.|
|SpeakerNumberOfFragments|비디오에서 화자가 있는 조각의 양입니다.|
|SpeakerLongestMonolog|화자의 가장 긴 단독 발언입니다. 스피커가 monolog 내에 silences 경우 포함 됩니다. 단독 발언의 시작과 끝 부분에 있는 침묵은 제거됩니다.|
|SpeakerTalkToListenRatio|화자의 단독 발언에 소요된 시간(그 사이 침묵 제외)을 비디오의 총 시간으로 나눈 값을 기반으로 계산됩니다. 시간은 소수점 이하 세 자리에서 반올림됩니다.|


### <a name="sentiments"></a>감정

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

### <a name="labels"></a>레이블

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

### <a name="keywords"></a>키워드

|Name|Description|
|---|---|
|id|키워드 ID입니다.|
|text|키워드 텍스트입니다.|
|신뢰도|키워드의 인식 신뢰도입니다.|
|language|키워드 언어(번역된 경우)입니다.|
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

visualContentModeration 블록에는 Video Indexer에서 잠재적 성인 콘텐츠가 있는 것으로 확인된 시간 범위가 포함됩니다. VisualContentModeration가 비어 있는 경우 식별 된 성인 콘텐츠가 없습니다.

성인 또는 외설 콘텐츠가 포함된 것으로 확인된 비디오는 프라이빗 보기만 가능합니다. 사용자는 콘텐츠에 대 한 사람 검토 요청을 제출할 수 있으며,이 경우 특성에는 사용자 `IsAdult` 리뷰 결과가 포함 됩니다.

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
## <a name="next-steps"></a>다음 단계

[자습서: Azure Media Services로 비디오 분석](analyze-videos-tutorial-with-api.md)
