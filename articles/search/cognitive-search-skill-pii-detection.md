---
title: PII 검색 인식 기술 (미리 보기)
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search의 보강 파이프라인에 있는 텍스트에서 개인적으로 식별 가능한 정보를 추출 하 고 마스크 합니다. 이 기술은 현재 공개 미리 보기로 제공됩니다.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: b2e35ba083e376f519ccbc32c71c1ac9b1e03a41
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935299"
---
#    <a name="pii-detection-cognitive-skill"></a>PII 검색 인식 기술

> [!IMPORTANT] 
> 이 기술은 현재 공개 미리 보기로 제공됩니다. 미리 보기 기능은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. 현재 포털 또는 .NET SDK가 지원 되지 않습니다.

**PII 검색** 기술은 입력 텍스트에서 개인 식별이 가능한 정보를 추출 하 고 다양 한 방법으로 해당 텍스트에서 마스킹할 수 있는 옵션을 제공 합니다. 이 기술은 Cognitive Services의 [Text Analytics](../cognitive-services/text-analytics/overview.md)에서 제공하는 기계 학습 모델을 사용합니다.

> [!NOTE]
> 처리 빈도를 늘리거나 문서를 추가하거나 AI 알고리즘을 추가하여 범위를 확장할 때 [청구 가능한 Cognitive Services 리소스를 연결](cognitive-search-attach-cognitive-services.md)해야 합니다. Cognitive Services에서 API를 호출하는 경우와 Azure Cognitiv Search에서 문서 크래킹 단계의 일부로 이미지를 추출하는 경우에는 요금이 부과됩니다. 문서에서 텍스트 추출할 때는 요금이 발생하지 않습니다.
>
> 기본 제공 기술을 실행하는 요금은 기존 [Cognitive Services 종량제 가격](https://azure.microsoft.com/pricing/details/cognitive-services/)으로 청구됩니다. 이미지 추출 가격 책정은 [Azure Cognitiv Search 가격 책정 페이지](https://azure.microsoft.com/pricing/details/search/)에 설명되어 있습니다.


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.PIIDetectionSkill

## <a name="data-limits"></a>데이터 제한
레코드의 최대 크기는 [`String.Length`](/dotnet/api/system.string.length)에 의해 측정된 대로 50,000자여야 합니다. 데이터를 기술에 보내기 전에 분리 해야 하는 경우 [텍스트 분할 기술을](cognitive-search-skill-textsplit.md)사용 하는 것이 좋습니다.

## <a name="skill-parameters"></a>기술 매개 변수

매개 변수는 대/소문자를 구분 하며 모두 선택 사항입니다.

| 매개 변수 이름     | Description |
|--------------------|-------------|
| `defaultLanguageCode` |    입력 텍스트의 언어 코드입니다. 지금은만 `en` 지원 됩니다. |
| `minimumPrecision` | 0.0에서 1.0 사이의 값입니다. 출력의 신뢰도 점수가 `piiEntities` 설정 된 값 보다 낮으면 `minimumPrecision` 엔터티가 반환 되거나 마스킹 되지 않습니다. 기본값은 0.0입니다. |
| `maskingMode` | 입력 텍스트에서 검색 된 PII를 마스킹하는 다양 한 방법을 제공 하는 매개 변수입니다. 다음과 같은 옵션이 지원됩니다. <ul><li>`none` (기본값): 마스킹을 수행 하지 않고 `maskedText` 출력이 반환 되지 않습니다. </li><li> `redact`:이 옵션은 입력 텍스트에서 검색 된 엔터티를 제거 하 고 다른 항목으로 바꾸지 않습니다. 이 경우 출력의 오프셋은 `piiEntities` 마스킹된 텍스트가 아니라 원래 텍스트를 기준으로 합니다. </li><li> `replace`:이 옵션은 검색 된 엔터티를 매개 변수에 지정 된 문자로 바꿉니다 `maskingCharacter` .  이 문자는 검색 된 엔터티의 길이로 반복 되어 오프셋이 입력 텍스트와 출력 모두에 올바르게 일치 하 게 됩니다 `maskedText` .</li></ul> |
| `maskingCharacter` | 매개 변수가로 설정 된 경우 텍스트를 마스킹하는 데 사용 되는 문자 `maskingMode` `replace` 입니다. 지원 되는 옵션은 다음과 같습니다. `*` (기본값), `#` , `X` . 이 매개 변수는이 `null` `maskingMode` 로 설정 되지 않은 경우에만 가능 `replace` 합니다. |


## <a name="skill-inputs"></a>기술 입력

| 입력 이름      | Description                   |
|---------------|-------------------------------|
| `languageCode`    | 선택 사항입니다. 기본값은 `en`입니다.  |
| `text`          | 분석할 텍스트입니다.          |

## <a name="skill-outputs"></a>기술 출력

| 출력 이름      | Description                   |
|---------------|-------------------------------|
| `piiEntities` | 다음 필드가 포함된 복합 형식의 배열입니다. <ul><li>텍스트 (추출 된 실제 PII)</li> <li>type</li><li>하위 형식</li><li>점수 (큰 값은 실제 엔터티가 될 가능성이 높음)</li><li>입력 텍스트에 대 한 오프셋입니다.</li><li>length</li></ul> </br> [가능한 유형 및 하위 유형은 여기에서 찾을 수 있습니다.](../cognitive-services/text-analytics/named-entity-types.md?tabs=personal) |
| `maskedText` | `maskingMode`가 이외의 값으로 설정 된 경우 `none` 이 출력은 선택한에 설명 된 대로 입력 텍스트에서 수행 된 마스킹의 문자열 결과가 됩니다 `maskingMode` .  `maskingMode`가로 설정 된 경우에는 `none` 이 출력이 표시 되지 않습니다. |

##    <a name="sample-definition"></a>샘플 정의

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.PIIDetectionSkill",
    "defaultLanguageCode": "en",
    "minimumPrecision": 0.5,
    "maskingMode": "replace",
    "maskingCharacter": "*",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "piiEntities"
      },
      {
        "name": "maskedText"
      }
    ]
  }
```
##    <a name="sample-input"></a>샘플 입력

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Microsoft employee with ssn 859-98-0987 is using our awesome API's."
           }
      }
    ]
}
```

##    <a name="sample-output"></a>샘플 출력

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "piiEntities":[ 
           { 
              "text":"859-98-0987",
              "type":"U.S. Social Security Number (SSN)",
              "subtype":"",
              "offset":28,
              "length":11,
              "score":0.65
           }
        ],
        "maskedText": "Microsoft employee with ssn *********** is using our awesome API's."
      }
    }
  ]
}
```

이 기술 출력의 엔터티에 대해 반환 되는 오프셋은 [텍스트 분석 API](../cognitive-services/text-analytics/overview.md)에서 직접 반환 됩니다. 즉, 원본 문자열을 인덱싱하는 데 사용 하는 경우에는 .Net에서 [쌍인지](/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) 클래스를 사용 하 여 올바른 콘텐츠를 추출 해야 합니다.  [자세한 내용은 여기를 참조 하세요.](../cognitive-services/text-analytics/concepts/text-offsets.md)

## <a name="error-and-warning-cases"></a>오류 및 경고 사례
문서에 대 한 언어 코드가 지원 되지 않는 경우 경고가 반환 되 고 엔터티가 추출 되지 않습니다.
텍스트가 비어 있는 경우 경고가 생성됩니다.
텍스트가 50,000자보다 큰 경우 처음 50,000자만 분석하고 경고를 발생합니다.

기술에서 경고를 반환 하는 경우 출력은 비어 있을 수 `maskedText` 있습니다.  이는 이후 기술을 입력 하기 위해 출력이 존재 하는 것으로 예상 되는 경우 의도 한 대로 작동 하지 않습니다. 기술 정의를 작성할 때는이 점을 염두에 두어야 합니다.

## <a name="see-also"></a>참고 항목

+ [기본 제공 기술](cognitive-search-predefined-skills.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)