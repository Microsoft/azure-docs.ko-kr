---
title: 텍스트 분할 인식 기술
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에서 AI 보강 파이프라인의 길이에 따라 텍스트를 텍스트의 페이지나 청크로 나눕니다.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 52aaeb01fef551eee350c6db662c2690ef7b3e78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "84981951"
---
# <a name="text-split-cognitive-skill"></a>텍스트 분할 인식 기술

**분할 텍스트** 기술은 텍스트를 텍스트의 청크로 나눕니다. 텍스트를 문장으로 또는 특정 길이의 페이지로 나눌 것인지 여부를 지정할 수 있습니다. 이 기술은 다른 기술 다운스트림에서 최대 텍스트 길이 요구 사항이 있는 경우 특히 유용합니다. 

> [!NOTE]
> 이 기술은 Cognitive Services API에 바인딩되지 않으며 사용 요금이 부과되지 않습니다. 그렇지만 **무료** 리소스 옵션을 재정의하여 일별 보강 횟수를 낮게 제한하려면 계속해서 [Cognitive Services 리소스를 연결](cognitive-search-attach-cognitive-services.md)해야 합니다.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SplitSkill 

## <a name="skill-parameters"></a>기술 매개 변수

매개 변수는 대/소문자를 구분합니다.

| 매개 변수 이름     | Description |
|--------------------|-------------|
| `textSplitMode`    | "페이지" 또는 "문장" 중 하나 | 
| `maximumPageLength` | TextSplitMode가 "페이지"로 설정되는 경우 이는 `String.Length`에 의해 측정된 대로 최대 페이지 길이를 참조합니다. 최소값은 300입니다.  textSplitMode가 "pages"로 설정된 경우 알고리즘은 텍스트를 최대 "maximumPageLength" 크기의 청크로 분할하려고 합니다. 이 경우 알고리즘은 청크 크기가 "maximumPageLength"보다 약간 더 작을 수 있게 문장 경계에서 문장을 나누기 위해 최선을 다합니다. | 
| `defaultLanguageCode` | (선택 사항) 다음 언어 코드 `da, de, en, es, fi, fr, it, ko, pt` 중 하나입니다. 기본값은 영어(en)입니다. 고려할 사항은 다음과 같습니다.<ul><li>languagecode-countrycode 형식을 전달하는 경우 형식의 languagecode 부분만 사용됩니다.</li><li>언어가 이전 목록에 없는 경우 분할 기술은 문자를 경계로 텍스트를 나눕니다.</li><li>언어 코드를 제공하면 중국어, 일본어, 한국어 등 비공백 언어에 대해 단어를 반으로 줄이지 못하게 하는 데 유용합니다.</li><li>언어를 모르는 경우(예: 입력 텍스트를 [LanguageDetectionSkill](cognitive-search-skill-language-detection.md)로 분할해야 하는 경우) 영어(en)의 기본값은 충분해야 합니다. </li></ul>  |


## <a name="skill-inputs"></a>기술 입력

| 매개 변수 이름       | Description      |
|----------------------|------------------|
| `text`    | 하위 문자열로 분할될 텍스트입니다. |
| `languageCode`    | (선택 사항) 문서에 대한 언어 코드입니다. 언어를 모르는 경우(예: 입력 텍스트를 [LanguageDetectionSkill](cognitive-search-skill-language-detection.md)로 분할해야 하는 경우) 이 입력을 제거하는 것이 안전합니다.  |

## <a name="skill-outputs"></a>기술 출력 

| 매개 변수 이름     | Description |
|--------------------|-------------|
| `textItems`   | 추출된 하위 문자열의 배열입니다. |


##  <a name="sample-definition"></a>샘플 정의

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
    "textSplitMode" : "pages", 
    "maximumPageLength": 1000,
    "defaultLanguageCode": "en",
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
            "name": "textItems",
            "targetName": "mypages"
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
            "data": {
                "text": "This is a the loan application for Joe Romero, a Microsoft employee who was born in Chile and who then moved to Australia…",
                "languageCode": "en"
            }
        },
        {
            "recordId": "2",
            "data": {
                "text": "This is the second document, which will be broken into several pages...",
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
            "data": {
                "textItems": [
                    "This is the loan…",
                    "On the second page we…"
                ]
            }
        },
        {
            "recordId": "2",
            "data": {
                "textItems": [
                    "This is the second document...",
                    "On the second page of the second doc…"
                ]
            }
        }
    ]
}
```

## <a name="error-cases"></a>오류 사례
언어가 지원되지 않는 경우 경고가 생성되고 텍스트가 문자를 경계로 분할됩니다.

## <a name="see-also"></a>참고 항목

+ [기본 제공 기술](cognitive-search-predefined-skills.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
