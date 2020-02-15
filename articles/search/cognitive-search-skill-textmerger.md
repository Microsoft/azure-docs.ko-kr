---
title: 텍스트 병합 인식 기술
titleSuffix: Azure Cognitive Search
description: 필드 컬렉션에서 통합된 하나의 필드로 텍스트를 병합합니다. Azure Cognitive Search의 AI 보강 파이프라인에서 이러한 인식 기술을 사용 합니다.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 98ea416305f080850d85498f74693eb2d45b0944
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162347"
---
#   <a name="text-merge-cognitive-skill"></a>텍스트 병합 인식 기술

**텍스트 병합** 기술은 필드 컬렉션에서 단일 필드로 텍스트를 통합합니다. 

> [!NOTE]
> 이 기술은 Cognitive Services API에 바인딩되지 않으며 사용 요금이 부과되지 않습니다. 그렇지만 [무료](cognitive-search-attach-cognitive-services.md) 리소스 옵션을 재정의하여 일별 보강 횟수를 낮게 제한하려면 계속해서 **Cognitive Services 리소스를 연결**해야 합니다.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.MergeSkill

## <a name="skill-parameters"></a>기술 매개 변수

매개 변수는 대/소문자를 구분합니다.

| 매개 변수 이름     | Description |
|--------------------|-------------|
| insertPreTag  | 모든 삽입 전에 포함될 문자열입니다. 기본값은 `" "`입니다. 공간을 생략하려면 값을 `""`로 설정합니다.  |
| insertPostTag | 모든 삽입 후에 포함될 문자열입니다. 기본값은 `" "`입니다. 공간을 생략하려면 값을 `""`로 설정합니다.  |


##  <a name="sample-input"></a>샘플 입력
이 기술에 대해 사용 가능한 입력을 제공하는 JSON 문서는 다음과 같을 수 있습니다.

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "text": "The brown fox jumps over the dog",
        "itemsToInsert": ["quick", "lazy"],
        "offsets": [3, 28]
      }
    }
  ]
}
```

##  <a name="sample-output"></a>샘플 출력
이 예제는 *insertPreTag*가 `" "`으로, *insertPostTag*가 `""`으로 설정된다고 가정하면서 이전 입력의 출력을 표시합니다. 

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "mergedText": "The quick brown fox jumps over the lazy dog"
      }
    }
  ]
}
```

## <a name="extended-sample-skillset-definition"></a>확장된 샘플 기술 집합 정의

텍스트 병합기를 사용하는 일반적인 시나리오는 이미지의 텍스트 표현(이미지의 캡션 또는 OCR 기술에서의 텍스트)을 문서의 콘텐츠 필드에 병합하는 것입니다. 

다음 기술 집합 예제는 OCR 기술을 사용하여 문서에 포함된 이미지에서 텍스트를 추출합니다. 다음으로 각 이미지에서 원본 및 OCR된 텍스트 모두를 포함하는 *merged_text* 필드를 만듭니다. OCR 기술에 대한 자세한 내용은 [여기](https://docs.microsoft.com/azure/search/cognitive-search-skill-ocr)에서 확인할 수 있습니다.

```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
      "description": "Extract text (plain and structured) from image.",
      "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
      "context": "/document/normalized_images/*",
      "defaultLanguageCode": "en",
      "detectOrientation": true,
      "inputs": [
        {
          "name": "image",
          "source": "/document/normalized_images/*"
        }
      ],
      "outputs": [
        {
          "name": "text"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " ",
      "inputs": [
        {
          "name":"text", "source": "/document/content"
        },
        {
          "name": "itemsToInsert", "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", "source": "/document/normalized_images/*/contentOffset" 
        }
      ],
      "outputs": [
        {
          "name": "mergedText", "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```
위의 예제는 정규화 이미지 필드가 있다고 가정합니다. 정규화 이미지 필드를 가져오려면 인덱서 정의에서 *imageAction* 구성을 아래와 같이 *generateNormalizedImages*로 설정합니다.

```json
{
  //...rest of your indexer definition goes here ...
  "parameters":{
    "configuration":{
        "dataToExtract":"contentAndMetadata",
        "imageAction":"generateNormalizedImages"
    }
  }
}
```

## <a name="see-also"></a>참고 항목

+ [기본 제공 기술](cognitive-search-predefined-skills.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [인덱서 만들기(REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
