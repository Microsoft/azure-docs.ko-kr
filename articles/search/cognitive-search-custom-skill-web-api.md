---
title: 기술력과의 사용자 지정 웹 API 기술
titleSuffix: Azure Cognitive Search
description: Web Api를 호출 하 여 Azure Cognitive Search 기술력과의 기능을 확장 합니다. 사용자 지정 웹 API 기술을 사용 하 여 사용자 지정 코드를 통합 합니다.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 29928d78c2cfc2f21def363341f8383c4efa89d2
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484114"
---
# <a name="custom-web-api-skill-in-an-azure-cognitive-search-enrichment-pipeline"></a>Azure Cognitive Search 보강 파이프라인의 사용자 지정 웹 API 기술

**사용자 지정 WEB api** 기술을 사용 하면 사용자 지정 작업을 제공 하는 Web api 엔드포인트을 호출 하 여 AI 보강을 확장할 수 있습니다. 기본 제공 기술과 비슷하게 **사용자 지정 Web API** 기술에는 입/출력이 있습니다. 입력에 따라 웹 API는 인덱서가 실행 될 때 JSON 페이로드를 받고 성공 상태 코드와 함께 JSON 페이로드를 응답으로 출력 합니다. 응답은 사용자 지정 기술로 지정된 출력을 포함해야 합니다. 다른 응답은 오류로 간주되며 강화는 수행되지 않습니다.

JSON 페이로드의 구조는 이 문서 뒷부분에서 좀 더 자세히 설명합니다.

> [!NOTE]
> 인덱서는 Web API에서 반환된 특정 표준 HTTP 상태 코드에 대해 두 번 다시 시도합니다. 이러한 HTTP 상태 코드는 다음과 같습니다. 
> * `502 Bad Gateway`
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Custom.WebApiSkill

## <a name="skill-parameters"></a>기술 매개 변수

매개 변수는 대/소문자를 구분합니다.

| 매개 변수 이름     | 설명 |
|--------------------|-------------|
| Uri | _JSON_ 페이로드가 전송 될 웹 API의 URI입니다. **https** URI 체계만 허용됩니다. |
| httpMethod | 페이로드를 보내는 데 사용하는 메서드입니다. 허용되는 메서드는 `PUT` 또는 `POST`입니다. |
| httpHeaders | 키-값 쌍 컬렉션입니다. 여기서 키는 헤더 이름을 나타내고, 값은 페이로드와 함께 Web API로 보낼 헤더 값을 나타냅니다. 헤더 `Accept`, `Accept-Charset`, `Accept-Encoding`, `Content-Length`, `Content-Type`, `Cookie`, `Host`, `TE`, `Upgrade`, `Via`는 이 컬렉션에서 금지됩니다. |
| 시간 제한 | (선택 사항) 지정할 경우 API 호출을 수행하는 http 클라이언트에 대한 시간 제한을 나타냅니다. 형식은 XSD "dayTimeDuration" 값( [ISO 8601 기간](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) 값의 제한된 하위 집합)이어야 합니다. 예를 들어, 60초인 경우 `PT60S`입니다. 설정하지 않으면 기본값 30초가 선택됩니다. 제한 시간은 최대 230 초, 최소 1 초로 설정할 수 있습니다. |
| batchSize | (선택 사항) API 호출당 보낼 "데이터 레코드" 수를 나타냅니다(아래의 _JSON_ 페이로드 구조 참조). 설정하지 않으면 기본값인 1,000이 선택됩니다. 인덱싱 처리량과 API의 부하 간에 적절한 절충을 이루려면 이 매개 변수를 사용하는 것이 좋습니다. |
| degreeOfParallelism | 필드 지정 된 경우 인덱서가 제공 된 엔드포인트과 병렬로 수행 될 호출 수를 나타냅니다. 엔드포인트이 요청 부하를 너무 많이 초과 하 여 실패 하는 경우이 값을 줄일 수 있으며, 엔드포인트에서 더 많은 요청을 수락 하 고 인덱서 성능을 증가 시킬 수 있는 경우이 값을 낮출 수 있습니다.  이 값을 설정 하지 않으면 기본값인 5가 사용 됩니다. DegreeOfParallelism은 최대 10 개까지 설정할 수 있으며 최소값은 1입니다. |

## <a name="skill-inputs"></a>기술 입력

이 기술에 대해 "미리 정의된" 입력은 없습니다. 이 기술 실행 시에 이미 사용할 수 있는 하나 이상의 필드를 입력으로 선택할 수 있습니다. 그러면 Wep API로 보낸 _JSON_ 페이로드에 다른 필드가 표시됩니다.

## <a name="skill-outputs"></a>기술 출력

이 기술에 대해 "미리 정의된" 출력은 없습니다. Web API가 반환하는 응답에 따라, _JSON_ 응답에서 선택할 수 있도록 출력 필드를 추가합니다.


## <a name="sample-definition"></a>샘플 정의

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "A custom skill that can identify positions of different phrases in the source text",
        "uri": "https://contoso.count-things.com",
        "batchSize": 4,
        "context": "/document",
        "inputs": [
          {
            "name": "text",
            "source": "/document/content"
          },
          {
            "name": "language",
            "source": "/document/languageCode"
          },
          {
            "name": "phraseList",
            "source": "/document/keyphrases"
          }
        ],
        "outputs": [
          {
            "name": "hitPositions"
          }
        ]
      }
```
## <a name="sample-input-json-structure"></a>샘플 입력 JSON 구조

이 _JSON_ 구조는 Web API로 보낼 페이로드를 나타냅니다.
이 구조는 항상 다음과 같은 제약 조건을 따릅니다.

* 최상위 엔터티를 `values`라고 하며 개체의 배열이 됩니다. 이러한 개체의 수는 최대 `batchSize`가 됩니다.
* `values` 배열의 각 개체에는 다음이 지정됩니다.
    * 해당 레코드 식별에 사용되는 `recordId`고유**문자열인** 속성
    * `data`JSON_개체에 해당하는_ 속성. `data` 속성의 필드는 기술 정의의 `inputs` 섹션에 지정된 "names"에 해당합니다. 해당 필드의 값은 해당 필드의 `source`에서 가져옵니다(문서의 필드 또는 다른 기술에서 가져올 수 있음).

```json
{
    "values": [
      {
        "recordId": "0",
        "data":
           {
             "text": "Este es un contrato en Inglés",
             "language": "es",
             "phraseList": ["Este", "Inglés"]
           }
      },
      {
        "recordId": "1",
        "data":
           {
             "text": "Hello world",
             "language": "en",
             "phraseList": ["Hi"]
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Hello world, Hi world",
             "language": "en",
             "phraseList": ["world"]
           }
      },
      {
        "recordId": "3",
        "data":
           {
             "text": "Test",
             "language": "es",
             "phraseList": []
           }
      }
    ]
}
```

## <a name="sample-output-json-structure"></a>샘플 출력 JSON 구조

"출력"은 웹 API에서 반환 된 응답에 해당 합니다. Web API는 _JSON_ 페이로드를 반환 해야 하며 (`Content-Type` 응답 헤더를 확인 하 여 확인 됨) 다음 제약 조건을 충족 해야 합니다.

* 개체 배열에 해당하는 `values`라는 최상위 엔터티가 있어야 합니다.
* 배열의 개체 수는 Web API에 전송 된 개체의 수와 같아야 합니다.
* 각 개체에는 다음이 지정되어야 합니다.
   * `recordId` 속성
   * `data` 속성: 필드가 `output`의 “names”와 일치하는 강화에 해당하는 개체이며, 해당 값이 보강으로 간주됩니다.
   * `errors` 속성: 인덱서 실행 기록에 추가될 오류를 나열하는 배열입니다. 이 속성은 필요하지만 값이 `null`일 수 있습니다.
   * `warnings` 속성: 인덱서 실행 기록에 추가될 경고를 나열하는 배열입니다. 이 속성은 필요하지만 값이 `null`일 수 있습니다.
* `values` 배열의 개체가 Web API에 대한 요청으로 보낸 `values` 배열의 개체와 같을 필요는 없습니다. 그러나 `recordId`가 상관 관계 유지를 위해 사용되므로 Web API에 대한 원본 요청의 속하지 않는 `recordId`를 포함하는 응답의 모든 레코드는 삭제됩니다.

```json
{
    "values": [
        {
            "recordId": "3",
            "data": {
            },
            "errors": [
              {
                "message" : "'phraseList' should not be null or empty"
              }
            ],
            "warnings": null
        },
        {
            "recordId": "2",
            "data": {
                "hitPositions": [6, 16]
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "0",
            "data": {
                "hitPositions": [0, 23]
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "1",
            "data": {
                "hitPositions": []
            },
            "errors": null,
            "warnings": {
                "message": "No occurrences of 'Hi' were found in the input text"
            }
        },
    ]
}

```

## <a name="error-cases"></a>오류 사례
Web API가 사용 가능하지 않거나 성공적이지 않은 상태 코드를 보내는 경우 외에도 다음과 같은 경우가 오류로 간주됩니다.

* Web API가 성공 상태 코드를 반환하지만 응답에 `application/json`이 아니라고 표시되므로 응답은 잘못된 것으로 간주되고 강화는 수행되지 않습니다.
* 응답 **배열에**잘못된`recordId`(원본 요청에 `values`가 없거나 중복된 값이 있음) 레코드가 있으면 **해당** 레코드에 대해 강화가 수행되지 않습니다.

Web API가 사용 가능하지 않거나 HTTP 오류를 반환하는 경우 HTTP 오류에 대해 사용 가능한 모든 세부 정보를 포함하는 오류가 인덱서 실행 기록에 추가됩니다.

## <a name="see-also"></a>참고자료

+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [AI 보강 파이프라인에 사용자 지정 기술 추가](cognitive-search-custom-skill-interface.md)
+ [예: AI 보강에 대 한 사용자 지정 기술 만들기](cognitive-search-create-custom-skill-example.md)
