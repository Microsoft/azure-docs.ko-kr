---
title: 핵심 문구 추출 인식 기술
titleSuffix: Azure Cognitive Search
description: 구조화되지 않은 텍스트를 평가하고 각 레코드에 대해 Azure Cognitive Search의 AI 보강 파이프라인에서 핵심 구문 목록을 반환합니다.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 145d8265412c4efcaa2afad97feb23528a36191d
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88936064"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>핵심 문구 추출 인식 기술

**핵심 문구 추출** 기술은 구조화되지 않은 텍스트를 평가하고 각 레코드에 대해 핵심 문구 목록을 반환합니다. 이 기술은 Cognitive Services의 [Text Analytics](../cognitive-services/text-analytics/overview.md)에서 제공하는 기계 학습 모델을 사용합니다.

이 기능은 레코드에서 기본 대화 지점을 신속하게 식별해야 하는 경우 유용합니다. 예를 들어 "음식이 맛 있었으며 훌륭한 직원이 있었습니다"라는 입력 텍스트가 있는 경우 서비스는 "음식" 및 "훌륭한 직원"을 반환합니다.

> [!NOTE]
> 처리 빈도를 늘리거나 문서를 추가하거나 AI 알고리즘을 추가하여 범위를 확장할 때 [청구 가능한 Cognitive Services 리소스를 연결](cognitive-search-attach-cognitive-services.md)해야 합니다. Cognitive Services에서 API를 호출하는 경우와 Azure Cognitiv Search에서 문서 크래킹 단계의 일부로 이미지를 추출하는 경우에는 요금이 부과됩니다. 문서에서 텍스트 추출할 때는 요금이 발생하지 않습니다.
>
> 기본 제공 기술을 실행하는 요금은 기존 [Cognitive Services 종량제 가격](https://azure.microsoft.com/pricing/details/cognitive-services/)으로 청구됩니다. 이미지 추출 가격 책정은 [Azure Cognitiv Search 가격 책정 페이지](https://azure.microsoft.com/pricing/details/search/)에 설명되어 있습니다.


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.KeyPhraseExtractionSkill 

## <a name="data-limits"></a>데이터 제한
레코드의 최대 크기는 [`String.Length`](/dotnet/api/system.string.length)에 의해 측정된 대로 50,000자여야 합니다. 핵심 구문 추출기로 보내기 전에 데이터를 분할해야 할 경우 [텍스트 분할 기술](cognitive-search-skill-textsplit.md) 사용을 고려합니다.

## <a name="skill-parameters"></a>기술 매개 변수

매개 변수는 대/소문자를 구분합니다.

| 입력                | Description |
|---------------------|-------------|
| `defaultLanguageCode` | (선택 사항) 명시적으로 언어를 지정하지 않은 문서에 적용할 언어 코드입니다.  기본 언어 코드가 지정되지 않은 경우 영어(en)가 기본 언어 코드로 사용됩니다. <br/> [지원되는 언어 전체 목록](../cognitive-services/text-analytics/language-support.md)을 참조합니다. |
| `maxKeyPhraseCount`   | (선택 사항) 생성할 핵심 구문의 최대 수입니다. |

## <a name="skill-inputs"></a>기술 입력

| 입력  | Description |
|--------------------|-------------|
| `text` | 분석할 텍스트입니다.|
| `languageCode`    |  레코드의 언어를 나타내는 문자열입니다. 이 매개 변수를 지정하지 않으면 레코드를 분석하는 데 기본 언어 코드가 사용됩니다. <br/>[지원되는 언어 전체 목록](../cognitive-services/text-analytics/language-support.md) 참조|

## <a name="skill-outputs"></a>기술 출력

| 출력     | Description |
|--------------------|-------------|
| `keyPhrases` | 입력 텍스트에서 추출된 핵심 구문 목록입니다. 핵심 구문은 중요도 순으로 반환됩니다. |


##  <a name="sample-definition"></a>샘플 정의

다음 필드를 포함 하는 SQL 레코드를 고려 하십시오.

```json
{
    "content": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. They accumulate ice from snowfall and lose it through melting. As global temperatures have risen, many of the world’s glaciers have already started to shrink and retreat. Continued warming could see many iconic landscapes – from the Canadian Rockies to the Mount Everest region of the Himalayas – lose almost all their glaciers by the end of the century.",
    "language": "en"
}
```

그러면 기술 정의가 다음과 같이 표시 될 수 있습니다.

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      },
      {
        "name": "languageCode",
        "source": "/document/language" 
      }
    ],
    "outputs": [
      {
        "name": "keyPhrases",
        "targetName": "myKeyPhrases"
      }
    ]
  }
```

##  <a name="sample-output"></a>샘플 출력

위의 예제에서 기술 출력은 지정 된 이기 때문에 "document/myKeyPhrases" 라는 보강 트리의 새 노드에 기록 됩니다 `targetName` . 을 지정 하지 않으면 `targetName` "document/keyPhrases"이 됩니다.

#### <a name="documentmykeyphrases"></a>문서/myKeyPhrases 
```json
            [
              "world’s glaciers", 
              "huge rivers of ice", 
              "Canadian Rockies", 
              "iconic landscapes",
              "Mount Everest region",
              "Continued warming"
            ]
```

"Document/myKeyPhrases"를 다른 기술에 대 한 입력으로 사용 하거나 [출력 필드 매핑의](cognitive-search-output-field-mapping.md)원본으로 사용할 수 있습니다.

## <a name="errors-and-warnings"></a>오류 및 경고
지원되지 않는 언어 코드를 제공하는 경우 오류가 생성되고 핵심 문구는 추출되지 않습니다.
텍스트가 비어 있는 경우 경고가 생성됩니다.
텍스트가 50,000자보다 큰 경우 처음 50,000자만 분석하고 경고를 발생합니다.

## <a name="see-also"></a>참고 항목

+ [기본 제공 기술](cognitive-search-predefined-skills.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [출력 필드 매핑을 정의 하는 방법](cognitive-search-output-field-mapping.md)