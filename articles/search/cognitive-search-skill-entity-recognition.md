---
title: 엔터티 인식 검색 기술 - Azure Search
description: Azure Search 인식 검색 파이프라인의 텍스트에서 다양한 형식의 엔터티를 추출합니다.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 02/27/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 2a245a6e3d76a7df41b5ef28f9bac8a2c2122402
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61127076"
---
#    <a name="entity-recognition-cognitive-skill"></a>엔터티 인식 기술

**엔터티 인식** 기술은 텍스트에서 다양한 형식의 엔터티를 추출합니다. 이 기술은 Cognitive Services의 [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)에서 제공하는 기계 학습 모델을 사용합니다.

> [!NOTE]
> 2018년 12월 21일부터 Azure Search 기술과 [Cognitive Services 리소스를 연결](cognitive-search-attach-cognitive-services.md)할 수 있습니다. 이를 통해 Microsoft는 기술 실행 요금을 부과할 수 있습니다. 또한 문서 해독 단계의 일부인 이미지 추출에 대한 요금 청구가 이 날짜에서 시작됩니다. 문서의 텍스트 추출은 추가 비용 없이 계속 제공됩니다.
>
> [기본 제공 인지 기술](cognitive-search-predefined-skills.md)을 실행하면 직접 작업을 수행한 것과 동일한 요율로 [Cognitive Services 종량제 가격](https://azure.microsoft.com/pricing/details/cognitive-services)이 부과됩니다. 이미지 추출은 현재 미리 보기 가격으로 Azure Search 요금이 청구됩니다. 자세한 내용은 [Azure Search 가격 책정 페이지](https://go.microsoft.com/fwlink/?linkid=2042400) 또는 [청구 작동 방식](search-sku-tier.md#how-billing-works)을 참조하세요.


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.EntityRecognitionSkill

## <a name="data-limits"></a>데이터 제한
레코드의 최대 크기는 `String.Length`에 의해 측정된 대로 50,000자여야 합니다. 핵심 구문 추출기로 보내기 전에 데이터를 분할해야 할 경우 [텍스트 분할 기술](cognitive-search-skill-textsplit.md) 사용을 고려합니다.

## <a name="skill-parameters"></a>기술 매개 변수

매개 변수는 대/소문자를 구분하며 모두 선택 사항입니다.

| 매개 변수 이름     | 설명 |
|--------------------|-------------|
| 범주    | 추출되어야 하는 범주 배열입니다.  가능한 범주 형식: `"Person"`, `"Location"`, `"Organization"`, `"Quantity"`, `"Datetime"`, `"URL"`, `"Email"`. 범주가 제공되지 않으면 모든 형식이 반환됩니다.|
|defaultLanguageCode |  입력 텍스트의 언어 코드입니다. 다음 언어 `de, en, es, fr, it`가 지원됩니다.|
|minimumPrecision | 사용되지 않습니다. 다음에 사용하도록 예약됩니다. |
|includeTypelessEntities | 텍스트에 잘 알려진 엔터티가 포함되어 있지만 지원되는 범주 중 하나로 분류할 수 없는 경우 `"entities"` 복합 출력 필드의 일부로 반환됩니다. 
이러한 값은 잘 알려져 있지만 현재 지원 되는 "범주"의 일환으로 분류 되지 않은 엔터티입니다. 예를 들어 "Windows 10"은 잘 알려진 엔터티 (제품) "제품"에 없지만 현재 지원 되는 범주입니다. 기본값은 `false`입니다. |


## <a name="skill-inputs"></a>기술 입력

| 입력 이름      | 설명                   |
|---------------|-------------------------------|
| languageCode  | 선택 사항입니다. 기본값은 `"en"`입니다.  |
| text          | 분석할 텍스트입니다.          |

## <a name="skill-outputs"></a>기술 출력

**참고**: 모든 엔터티 범주가 모든 언어로 지원되는 것은 아닙니다.
_en_, _es_만 `"Quantity"`, `"Datetime"`, `"URL"`, `"Email"` 형식의 추출을 지원합니다.

| 출력 이름     | 설명                   |
|---------------|-------------------------------|
| persons      | 각 문자열이 사람 이름을 나타내는 경우 문자열 배열입니다. |
| 위치  | 각 문자열이 위치를 나타내는 경우 문자열 배열입니다. |
| organizations  | 각 문자열이 조직을 나타내는 경우 문자열 배열입니다. |
| quantities  | 각 문자열이 수량을 나타내는 경우 문자열 배열입니다. |
| dateTimes  | 각 문자열이 DateTime(텍스트에 표시되는 대로) 값을 나타내는 경우 문자열 배열입니다. |
| urls | 각 문자열이 URL을 나타내는 경우 문자열 배열입니다. |
| emails | 각 문자열이 메일을 나타내는 경우 문자열 배열입니다. |
| namedEntities | 다음 필드가 포함된 복합 형식의 배열입니다. <ul><li>category</li> <li>값 (실제 엔터티 이름)</li><li>오프셋(텍스트에 발견된 위치)</li><li>confidence(현재 사용하지 않음. -1 값으로 설정됨)</li></ul> |
| 엔터티 | 다음 필드와 함께 텍스트에서 추출된 엔터티에 대한 풍부한 정보가 포함된 복합 형식의 배열입니다. <ul><li> name(실제 엔터티 이름. “정규화된” 형식을 나타냄)</li><li> wikipediaId</li><li>wikipediaLanguage</li><li>wikipediaUrl(엔터티에 대한 Wikipedia 페이지 링크)</li><li>bingId</li><li>type(인식된 엔터티의 범주)</li><li>subType(특정 범주에서만 사용 가능, 더 세부적인 엔터티 형식 보기를 제공함)</li><li> matches(포함된 복합 컬렉션)<ul><li>text(엔터티의 원시 텍스트)</li><li>offset(발견된 위치)</li><li>length(원시 엔터티 텍스트의 길이)</li></ul></li></ul> |

##  <a name="sample-definition"></a>샘플 정의

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person", "Email"],
    "defaultLanguageCode": "en",
    "includeTypelessEntities": true,
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
      },
      {
        "name": "emails",
        "targetName": "contact"
      },
      {
        "name": "entities"
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
             "text": "Contoso corporation was founded by John Smith. They can be reached at contact@contoso.com",
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
        "persons": [ "John Smith"],
        "emails":["contact@contoso.com"],
        "namedEntities": 
        [
          {
            "category":"Person",
            "value": "John Smith",
            "offset": 35,
            "confidence": -1
          }
        ],
        "entities":  
        [
          {
            "name":"John Smith",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Person",
            "subType": null,
            "matches": [{
                "text": "John Smith",
                "offset": 35,
                "length": 10
            }]
          },
          {
            "name": "contact@contoso.com",
            "wikipediaId": null,
            "wikipediaLanguage": null,
            "wikipediaUrl": null,
            "bingId": null,
            "type": "Email",
            "subType": null,
            "matches": [
            {
                "text": "contact@contoso.com",
                "offset": 70,
                "length": 19
            }]
          },
          {
            "name": "Contoso",
            "wikipediaId": "Contoso",
            "wikipediaLanguage": "en",
            "wikipediaUrl": "https://en.wikipedia.org/wiki/Contoso",
            "bingId": "349f014e-7a37-e619-0374-787ebb288113",
            "type": null,
            "subType": null,
            "matches": [
            {
                "text": "Contoso",
                "offset": 0,
                "length": 7
            }]
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
+ [기능을 정의하는 방법](cognitive-search-defining-skillset.md)
