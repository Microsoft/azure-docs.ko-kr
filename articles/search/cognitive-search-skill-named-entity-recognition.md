---
title: 명명된 엔터티 인식 검색 기술 - Azure Search
description: Azure Search 인식 검색 파이프라인의 텍스트에서 사람, 위치 및 조직에 대해 명명된 엔터티를 추출합니다.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 28fae27b52ea150c1fa732715212e2f2c9534bc6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61126918"
---
#    <a name="named-entity-recognition-cognitive-skill"></a>명명된 엔터티 인식 기술

**명명된 엔터티 인식** 기술은 텍스트에서 명명된 엔터티를 추출합니다. 사용 가능한 엔터티는 `person`, `location` 및 `organization` 형식을 포함합니다.

> [!IMPORTANT]
> 명명된 엔터티 인식 기술은 사용되지 않고 [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)로 대체됩니다. 이에 대한 지원은 2019년 2월 15일에 정지됩니다. [사용되지 않는 Cognitive Search 기술](cognitive-search-skill-deprecated.md) 페이지의 권장 사항에 따라 지원되는 기술로 마이그레이션하세요.

> [!NOTE]
> 2018년 12월 21일부터 Azure Search 기술 세트와 [Cognitive Services 리소스를 연결](cognitive-search-attach-cognitive-services.md)할 수 있습니다. 이를 통해 Microsoft는 기술 실행 요금을 부과할 수 있습니다. 또한 문서 해독 단계의 일부인 이미지 추출에 대한 요금 청구가 이 날짜에서 시작됩니다. 문서의 텍스트 추출은 추가 비용 없이 계속 제공됩니다.
>
> [기본 제공 인지 기술](cognitive-search-predefined-skills.md)을 실행하면 직접 작업을 수행한 것과 동일한 요율로 [Cognitive Services 종량제 가격](https://azure.microsoft.com/pricing/details/cognitive-services)이 부과됩니다. 이미지 추출은 현재 미리 보기 가격으로 Azure Search 요금이 청구됩니다. 자세한 내용은 [Azure Search 가격 책정 페이지](https://go.microsoft.com/fwlink/?linkid=2042400) 또는 [청구 작동 방식](search-sku-tier.md#how-billing-works)을 참조하세요.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.NamedEntityRecognitionSkill

## <a name="data-limits"></a>데이터 제한
레코드의 최대 크기는 `String.Length`에 의해 측정된 대로 50,000자여야 합니다. 핵심 구문 추출기로 보내기 전에 데이터를 분할해야 할 경우 [텍스트 분할 기술](cognitive-search-skill-textsplit.md) 사용을 고려합니다.

## <a name="skill-parameters"></a>기술 매개 변수

매개 변수는 대/소문자를 구분합니다.

| 매개 변수 이름     | 설명 |
|--------------------|-------------|
| 범주    | 추출되어야 하는 범주 배열입니다.  가능한 범주 형식: `"Person"`, `"Location"`, `"Organization"`입니다. 범주가 제공되지 않으면 모든 형식이 반환됩니다.|
|defaultLanguageCode |  입력 텍스트의 언어 코드입니다. 다음 언어 `de, en, es, fr, it`가 지원됩니다.|
| minimumPrecision  | 0에서 1 사이의 숫자입니다. 전체 자릿수가 이 값보다 낮은 경우 엔터티가 반환되지 않습니다. 기본값은 0입니다.|

## <a name="skill-inputs"></a>기술 입력

| 입력 이름      | 설명                   |
|---------------|-------------------------------|
| languageCode  | 선택 사항입니다. 기본값은 `"en"`입니다.  |
| text          | 분석할 텍스트입니다.          |

## <a name="skill-outputs"></a>기술 출력

| 출력 이름     | 설명                   |
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
             "text": "This is the loan application for Joe Romero, he is a Microsoft employee who was born in Chile and then moved to Australia… Ana Smith is provided as a reference.",
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

+ [미리 정의된 기술](cognitive-search-predefined-skills.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [엔터티 인식 기술](cognitive-search-skill-entity-recognition.md)
