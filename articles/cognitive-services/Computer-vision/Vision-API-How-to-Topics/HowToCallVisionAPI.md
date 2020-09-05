---
title: Computer Vision API 호출
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services에서 REST API를 사용하여 Computer Vision API를 호출하는 방법을 알아봅니다.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 085da17a29e1d5ff1fa69a62e0029fb917d56bb1
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88936336"
---
# <a name="call-the-computer-vision-api"></a>Computer Vision API 호출

이 문서에서는 REST API를 사용하여 Computer Vision API를 호출하는 방법을 보여줍니다. 샘플은 Computer Vision API 클라이언트 라이브러리를 사용하여 C#으로 작성되고 HTTP POST 또는 GET 호출로 작성됩니다. 이 문서에서는 다음 내용을 중점적으로 다룹니다.

- 태그, 설명 및 범주 가져오기
- 도메인 관련 정보 또는 "유명인" 가져오기

이 문서의 예제에서는 다음 기능을 보여줍니다.

* 이미지를 분석하여 태그 배열 및 설명 반환
* 도메인별 모델(특히 "유명인" 모델)을 사용하여 이미지를 분석하고 해당 결과를 JSON으로 반환

이 기능은 다음과 같은 옵션을 제공합니다.

- **옵션 1**: 범위 분석 - 지정된 모델만 분석
- **옵션 2**: 고급 분석 - [86개 범주 분류](../Category-Taxonomy.md)를 통해 분석하여 추가 정보 제공

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Computer Vision 리소스 만들기"  target="_blank">Computer Vision 리소스 <span class="docon docon-navigate-external x-hidden-focus"></span></a>를 만들어 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동**을 클릭합니다.
    * 애플리케이션을 Computer Vision 서비스에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.
* 로컬에 저장된 이미지의 이미지 URL 또는 경로.
* 지원되는 입력 방법: application/octet-stream 또는 이미지 URL 형식의 원시 이미지 이진
* 지원되는 이미지 파일 형식: JPEG, PNG, GIF, BMP
* 이미지 파일 크기: 4MB 이하
* 이미지 크기: 50 &times; 50 픽셀 이상
  
## <a name="authorize-the-api-call"></a>API 호출 권한 부여

Computer Vision API를 호출할 때마다 구독 키가 필요합니다. 이 키는 쿼리 문자열 매개 변수를 통해 전달하거나 요청 헤더에서 지정해야 합니다.

다음 방법 중 하나로 구독 키를 전달할 수 있습니다.

* 이 Computer Vision API 예제처럼 쿼리 문자열을 통해 전달:

  ```
  https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
  ```

* HTTP 요청 헤더에서 지정:

  ```
  ocp-apim-subscription-key: <Your subscription key>
  ```

* 클라이언트 라이브러리를 사용하는 경우 ComputerVisionClient의 생성자를 통해 키를 전달하고, 클라이언트의 속성에서 지역을 지정합니다.

    ```
    var visionClient = new ComputerVisionClient(new ApiKeyServiceClientCredentials("Your subscriptionKey"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com"
    }
    ```

## <a name="upload-an-image-to-the-computer-vision-api-service"></a>Computer Vision API 서비스에 이미지 업로드

Computer Vision API 호출을 수행하는 기본적인 방법은 이미지를 직접 업로드하여 태그, 설명 및 유명인을 반환하는 것입니다. 이렇게 하려면 이미지에서 읽은 데이터와 함께 HTTP 본문에 이진 이미지가 들어 있는 "POST" 요청을 보냅니다. upload 메서드는 모든 Computer Vision API 호출에 대해 동일합니다. 유일한 차이점은 사용자가 지정하는 쿼리 매개 변수입니다. 

지정된 이미지의 경우 다음 옵션 중 하나를 사용하여 태그와 설명을 가져옵니다.

### <a name="option-1-get-a-list-of-tags-and-a-description"></a>옵션 1: 태그 및 설명 목록 가져오기

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
```

```csharp
using System.IO;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

ImageAnalysis imageAnalysis;
var features = new VisualFeatureTypes[] { VisualFeatureTypes.Tags, VisualFeatureTypes.Description };

using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  imageAnalysis = await visionClient.AnalyzeImageInStreamAsync(fs, features);
}
```

### <a name="option-2-get-a-list-of-tags-only-or-a-description-only"></a>옵션 2: 태그만 있는 목록 또는 설명만 있는 목록 가져오기

태그만 있는 목록의 경우 다음을 실행합니다.

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/tag?subscription-key=<Your subscription key>
var tagResults = await visionClient.TagImageAsync("http://contoso.com/example.jpg");
```

설명만 있는 목록의 경우 다음을 실행합니다.

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/describe?subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  imageDescription = await visionClient.DescribeImageInStreamAsync(fs);
}
```

## <a name="get-domain-specific-analysis-celebrities"></a>도메인 특정 분석 가져오기(유명인)

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>옵션 1: 범위 분석 - 지정된 모델만 분석
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/models/celebrities/analyze
var celebritiesResult = await visionClient.AnalyzeImageInDomainAsync(url, "celebrities");
```

이 옵션의 경우 모든 다른 쿼리 매개 변수 {visualFeatures, details}가 유효하지 않습니다. 지원되는 모든 모델을 확인하려면 다음을 사용합니다.

```
GET https://westus.api.cognitive.microsoft.com/vision/v2.1/models 
var models = await visionClient.ListModelsAsync();
```

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-86-categories-taxonomy"></a>옵션 2: 고급 분석 - 86개 범주 분류를 통해 분석하여 추가 정보 제공

하나 이상의 도메인별 모델 세부 정보 외에 일반 이미지 분석을 가져오려는 애플리케이션의 경우 모델 쿼리 매개 변수를 사용하여 v1 API를 확장합니다.

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?details=celebrities
```

이 메서드를 호출할 때 먼저 [86-category](../Category-Taxonomy.md) 분류자를 호출합니다. 범주 중 하나라도 알려진 모델 또는 일치 모델의 범주와 일치하는 경우 분류자 호출이 두 번째로 전달됩니다. 예를 들어 "details=all"이거나 "details"에 "celebrities"가 포함된 경우 86-category 분류자를 호출한 후 유명인 모델을 호출합니다. 그 결과에는 person 범주가 포함됩니다. 옵션 1과는 달리, 이 방법은 유명인에 관심이 있는 사용자의 대기 시간이 증가합니다.

이 경우 모든 v1 쿼리 매개 변수가 동일한 방식으로 작동합니다. visualFeatures=categories를 지정하지 않으면 암시적으로 사용됩니다.

## <a name="retrieve-and-understand-the-json-output-for-analysis"></a>분석을 위한 JSON 출력 검색 및 이해

예를 들면 다음과 같습니다.

```json
{  
  "tags":[  
    {  
      "name":"outdoor",
      "score":0.976
    },
    {  
      "name":"bird",
      "score":0.95
    }
  ],
  "description":{  
    "tags":[  
      "outdoor",
      "bird"
    ],
    "captions":[  
      {  
        "text":"partridge in a pear tree",
        "confidence":0.96
      }
    ]
  }
}
```

필드 | Type | 콘텐츠
------|------|------|
태그들  | `object` | 태그 배열의 최상위 개체입니다.
tags[].Name | `string`    | 태그 분류자의 키워드입니다.
tags[].Score    | `number`    | 0에서 1 사이의 신뢰도 점수입니다.
description     | `object`    | 설명의 최상위 개체입니다.
description.tags[] |    `string`    | 태그 목록입니다.  캡션을 생성하는 기능에 대한 신뢰도가 부족한 경우 태그는 호출자에게 제공되는 유일한 정보일 수 있습니다.
description.captions[].text    | `string`    | 이미지를 설명하는 구.
description.captions[].confidence    | `number`    | 구의 신뢰도 점수입니다.

## <a name="retrieve-and-understand-the-json-output-of-domain-specific-models"></a>도메인 특정 모델의 JSON 출력 검색 및 이해

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>옵션 1: 범위 분석 - 지정된 모델만 분석

출력은 다음 예제처럼 태그의 배열입니다.

```json
{  
  "result":[  
    {  
      "name":"golden retriever",
      "score":0.98
    },
    {  
      "name":"Labrador retriever",
      "score":0.78
    }
  ]
}
```

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-the-86-categories-taxonomy"></a>옵션 2: 고급 분석 - "86개 범주" 분류를 통해 분석하여 추가 정보 제공

옵션 2(고급 분석)를 사용하는 도메인별 모델의 경우 다음 예제처럼 범주 반환 형식이 확장됩니다.

```json
{  
  "requestId":"87e44580-925a-49c8-b661-d1c54d1b83b5",
  "metadata":{  
    "width":640,
    "height":430,
    "format":"Jpeg"
  },
  "result":{  
    "celebrities":[  
      {  
        "name":"Richard Nixon",
        "faceRectangle":{  
          "left":107,
          "top":98,
          "width":165,
          "height":165
        },
        "confidence":0.9999827
      }
    ]
  }
}
```

범주 필드는 원래 분류의 [86개 범주](../Category-Taxonomy.md) 중 하나 이상이 포함된 목록입니다. 밑줄로 끝나는 범주는 해당 범주 및 자식과 일치합니다(예: 유명인 모델의 경우 "people_" 또는 "people_group").

필드    | Type    | 콘텐츠
------|------|------|
범주 | `object`    | 최상위 개체입니다.
categories[].name     | `string`    | 86개 범주 분류 목록의 이름입니다.
categories[].score    | `number`    | 0에서 1 사이의 신뢰도 점수입니다.
categories[].detail     | `object?`      | (선택 사항) 세부 정보 개체입니다.

여러 범주가 일치하는 경우(예: model=celebrities인 경우 86개 범주 분류자가 "people_" 및 "people_young"의 점수를 둘 다 반환하는 경우) 세부 정보는 가장 일반적인 수준 일치(이 예제에서는 "people_")에 연결됩니다.

## <a name="error-responses"></a>오류 응답

이러한 오류는 옵션 1 및 옵션 2 시나리오에서 반환될 수 있는 추가 NotSupportedModel 오류(HTTP 400)가 포함된 vision.analyze의 오류와 동일합니다. 옵션 2(고급 분석)의 경우 세부 정보에 지정된 모델이 인식되지 않으면 API는 모델 중 하나 이상이 유효하더라도 NotSupportedModel을 반환합니다. listModels를 호출하여 지원되는 모델을 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

REST API를 사용하려면 [Computer Vision API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/56f91f2e778daf14a499f21b)로 이동합니다.
