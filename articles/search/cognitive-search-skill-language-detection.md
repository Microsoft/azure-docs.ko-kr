---
title: 언어 감지 인식 검색 기술 - Azure Search
description: 구조화되지 않은 텍스트를 평가하고 각 레코드에 대해 Azure Search 보강 파이프라인에서 분석의 강도를 나타내는 점수로 언어 식별자를 반환합니다.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 605f4c639cfc8c0f9732f7347532e1bd7edc055f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61341633"
---
#   <a name="language-detection-cognitive-skill"></a>언어 감지 인식 기술

합니다 **언어 감지** 기술 입력된 텍스트의 언어를 검색 하 고 요청에 제출 된 모든 문서에 대 한 단일 언어 코드를 보고 합니다. 언어 코드는 분석의 강도를 나타내는 점수와 쌍을 이룹니다. 이 기술은 Cognitive Services의 [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview)에서 제공하는 기계 학습 모델을 사용합니다.

이 기능은 텍스트 언어를 다른 기술에 대한 입력으로 제공해야 할 경우 특히 유용합니다(예를 들어 [감정 분석 기술](cognitive-search-skill-sentiment.md) 또는 [텍스트 분할 기술](cognitive-search-skill-textsplit.md)).

언어 감지는 수를 초과 하는 Bing의 자연어 처리 라이브러리를 활용의 [지원 되는 언어 및 지역](https://docs.microsoft.com/azure/cognitive-services/text-analytics/language-support) Text Analytics에 대해 나열 합니다. 언어의 정확한 목록을, 게시 되지 않지만 모든 널리 음성 언어, plus 변형, 언어 및 일부 지역 및 문화적 언어를 포함 합니다. 덜 사용 되는 언어로 표현 된 콘텐츠를 만든 경우 [언어 검색 API 사용해 보기](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) 확인 코드를 반환 합니다. 검색할 수 없는 언어에 대 한 응답은 `unknown`합니다.

> [!NOTE]
> 2018년 12월 21일부터 Azure Search 기술 세트와 [Cognitive Services 리소스를 연결](cognitive-search-attach-cognitive-services.md)할 수 있습니다. 이를 통해 Microsoft는 기술 실행 요금을 부과할 수 있습니다. 또한 문서 해독 단계의 일부인 이미지 추출에 대한 요금 청구가 이 날짜에서 시작됩니다. 문서의 텍스트 추출은 추가 비용 없이 계속 제공됩니다.
>
> [기본 제공 인지 기술](cognitive-search-predefined-skills.md)을 실행하면 직접 작업을 수행한 것과 동일한 요율로 [Cognitive Services 종량제 가격](https://azure.microsoft.com/pricing/details/cognitive-services)이 부과됩니다. 이미지 추출은 현재 미리 보기 가격으로 Azure Search 요금이 청구됩니다. 자세한 내용은 [Azure Search 가격 책정 페이지](https://go.microsoft.com/fwlink/?linkid=2042400) 또는 [청구 작동 방식](search-sku-tier.md#how-billing-works)을 참조하세요.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>데이터 제한
레코드의 최대 크기는 `String.Length`에 의해 측정된 대로 50,000자여야 합니다. 감성 분석기로 보내기 전에 데이터를 분할해야 할 경우 [텍스트 분할 기술](cognitive-search-skill-textsplit.md)을 사용할 수 있습니다.

## <a name="skill-inputs"></a>기술 입력

매개 변수는 대/소문자를 구분합니다.

| 입력     | 설명 |
|--------------------|-------------|
| text | 분석할 텍스트입니다.|

## <a name="skill-outputs"></a>기술 출력

| 출력 이름    | 설명 |
|--------------------|-------------|
| languageCode | 식별된 언어에 대한 ISO 6391 언어 코드입니다. 예: "en". |
| LanguageName | 언어의 이름입니다. 예: "영어". |
| score에 대한 임계값 수준 보기 | 0과 1 사이의 값입니다. 언어를 올바르게 식별하는 가능성입니다. 문장에 언어를 혼합하는 경우 점수는 1보다 작을 수 있습니다.  |

##  <a name="sample-definition"></a>샘플 정의

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "languageCode",
        "targetName": "myLanguageCode"
      },
      {
        "name": "languageName",
        "targetName": "myLanguageName"
      },
      {
        "name": "score",
        "targetName": "myLanguageScore"
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
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. "
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Estamos muy felices de estar con ustedes."
           }
      }
    ]
```


##  <a name="sample-output"></a>샘플 출력

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
            {
              "languageCode": "en",
              "languageName": "English",
              "score": 1,
            }
      },
      {
        "recordId": "2",
        "data":
            {
              "languageCode": "es",
              "languageName": "Spanish",
              "score": 1,
            }
      }
    ]
}
```


## <a name="error-cases"></a>오류 사례
텍스트가 지원되지 않는 언어에서 표현되는 경우 오류가 발생하고 언어 식별자가 반환되지 않습니다.

## <a name="see-also"></a>참고 항목

+ [미리 정의된 기술](cognitive-search-predefined-skills.md)
+ [기능을 정의하는 방법](cognitive-search-defining-skillset.md)
