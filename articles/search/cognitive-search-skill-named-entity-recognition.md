---
title: 명명된 엔터티 인식 기술
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search AI 보강 파이프라인의 텍스트에서 사람, 위치 및 조직에 대 한 명명 된 엔터티를 추출 합니다.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8dc18af933a4570112beb025c1fb9163abafffff
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935945"
---
#    <a name="named-entity-recognition-cognitive-skill"></a>명명된 엔터티 인식 기술

**명명된 엔터티 인식** 기술은 텍스트에서 명명된 엔터티를 추출합니다. 사용 가능한 엔터티는 `person`, `location` 및 `organization` 형식을 포함합니다.

> [!IMPORTANT]
> 명명 된 엔터티 인식 기술은 이제 [EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)로 대체 되었습니다. 2019 년 2 월 15 일에 중지 된 지원은 2019 년 5 월 2 일에 제품에서 제거 되었습니다. 지원 [되지 않는 인식 검색 기술](cognitive-search-skill-deprecated.md) 의 권장 사항에 따라 지원 되는 기술로 마이그레이션합니다.

> [!NOTE]
> 처리 빈도를 늘리거나 문서를 추가하거나 AI 알고리즘을 추가하여 범위를 확장할 때 [청구 가능한 Cognitive Services 리소스를 연결](cognitive-search-attach-cognitive-services.md)해야 합니다. Cognitive Services에서 API를 호출하는 경우와 Azure Cognitiv Search에서 문서 크래킹 단계의 일부로 이미지를 추출하는 경우에는 요금이 부과됩니다. 문서에서 텍스트 추출할 때는 요금이 발생하지 않습니다.
>
> 기본 제공 기술을 실행하는 요금은 기존 [Cognitive Services 종량제 가격](https://azure.microsoft.com/pricing/details/cognitive-services/)으로 청구됩니다. 이미지 추출 가격 책정은 [Azure Cognitiv Search 가격 책정 페이지](https://azure.microsoft.com/pricing/details/search/)에 설명되어 있습니다.


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.NamedEntityRecognitionSkill

## <a name="data-limits"></a>데이터 제한
레코드의 최대 크기는 [`String.Length`](/dotnet/api/system.string.length)에 의해 측정된 대로 50,000자여야 합니다. 핵심 구문 추출기로 보내기 전에 데이터를 분할해야 할 경우 [텍스트 분할 기술](cognitive-search-skill-textsplit.md) 사용을 고려합니다.

## <a name="skill-parameters"></a>기술 매개 변수

매개 변수는 대/소문자를 구분합니다.

| 매개 변수 이름     | Description |
|--------------------|-------------|
| 범주    | 추출되어야 하는 범주 배열입니다.  가능한 범주 형식: `"Person"`, `"Location"`, `"Organization"`입니다. 범주가 제공되지 않으면 모든 형식이 반환됩니다.|
|defaultLanguageCode |  입력 텍스트의 언어 코드입니다. 다음 언어 `de, en, es, fr, it`가 지원됩니다.|
| minimumPrecision  | 0에서 1 사이의 숫자입니다. 전체 자릿수가 이 값보다 낮은 경우 엔터티가 반환되지 않습니다. 기본값은 0입니다.|

## <a name="skill-inputs"></a>기술 입력

| 입력 이름      | Description                   |
|---------------|-------------------------------|
| languageCode  | 선택 사항입니다. 기본값은 `"en"`입니다.  |
| text          | 분석할 텍스트입니다.          |

## <a name="skill-outputs"></a>기술 출력

| 출력 이름     | Description                   |
|---------------|-------------------------------|
| persons      | 각 문자열이 사람 이름을 나타내는 경우 문자열 배열입니다. |
| 위치  | 각 문자열이 위치를 나타내는 경우 문자열 배열입니다. |
| organizations  | 각 문자열이 조직을 나타내는 경우 문자열 배열입니다. |
| 엔터티 | 복합 형식의 배열입니다. 각 복합 형식에는 다음 필드가 포함됩니다. <ul><li>범주(`"person"`, `"organization"` 또는 `"location"`)</li> <li>값(실제 엔터티 이름)</li><li>오프셋(텍스트에 발견된 위치)</li><li>신뢰도(0과 1 사이의 값은 해당 값이 실제 엔터티라는 신뢰도를 나타냄)</li></ul> |

##  <a name="sample-definition"></a>샘플 정의

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
    "categories": [ "Person", "Location", "Organization"],
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
      }
    ]
  }
```
##  <a name="sample-input"></a>샘플 입력

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "This is the loan application for Joe Romero, a Microsoft employee who was born in Chile and who then moved to Australia… Ana Smith is provided as a reference.",
             "languageCode": "en"
           }
      }
    ]
}
```

##  <a name="sample-output"></a>샘플 출력

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "persons": [ "Joe Romero", "Ana Smith"],
        "locations": ["Chile", "Australia"],
        "organizations":["Microsoft"],
        "entities":  
        [
          {
            "category":"person",
            "value": "Joe Romero",
            "offset": 33,
            "confidence": 0.87
          },
          {
            "category":"person",
            "value": "Ana Smith",
            "offset": 124,
            "confidence": 0.87
          },
          {
            "category":"location",
            "value": "Chile",
            "offset": 88,
            "confidence": 0.99
          },
          {
            "category":"location",
            "value": "Australia",
            "offset": 112,
            "confidence": 0.99
          },
          {
            "category":"organization",
            "value": "Microsoft",
            "offset": 54,
            "confidence": 0.99
          }
        ]
      }
    }
  ]
}
```


## <a name="error-cases"></a>오류 사례
문서에 대한 언어 코드가 지원되지 않는 경우 오류가 반환되고 엔터티가 추출되지 않습니다.

## <a name="see-also"></a>참고 항목

+ [기본 제공 기술](cognitive-search-predefined-skills.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [엔터티 인식 기술](cognitive-search-skill-entity-recognition.md)