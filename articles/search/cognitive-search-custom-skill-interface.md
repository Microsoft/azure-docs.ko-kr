---
title: 인식 검색의 사용자 지정 기술에 대한 인터페이스 정의 - Azure Search
description: Azure Search에서 인식 검색 파이프라인의 web-api 사용자 지정 기술에 대한 사용자 지정 데이터 추출 인터페이스입니다.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 2c43a6b64f7b7f4f1adae78dca77fe71cf538e5e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61462616"
---
# <a name="how-to-add-a-custom-skill-to-a-cognitive-search-pipeline"></a>인식 검색 파이프라인에 사용자 지정 기술을 추가하는 방법

Azure Search의 [인식 검색 인덱싱 파이프라인](cognitive-search-concept-intro.md)은 [미리 정의된 기술](cognitive-search-predefined-skills.md)뿐 아니라 사용자가 개인적으로 만들어서 파이프라인에 추가하는 [사용자 지정 기술](cognitive-search-custom-skill-web-api.md)을 통해 조립할 수 있습니다. 이 문서에서는 인식 검색 파이프라인에 포함될 수 있도록 하는 인터페이스를 노출하는 사용자 지정 기술을 만드는 방법을 알아봅니다. 

사용자 지정 기술을 작성하면 콘텐츠에 고유한 변환을 삽입할 수 있습니다. 사용자 지정 기술은 독립적으로 실행되며 필요한 보강 단계를 적용합니다. 예를 들어, 필드별 사용자 지정 엔터티를 정의하거나, 사용자 지정 분류 모델을 작성하여 비즈니스 및 재무 계약과 문서를 구별하거나, 음성 인식 기술을 추가하여 관련 콘텐츠에 대해 오디오 파일을 더 자세히 조사할 수 있습니다. 단계별 예제는 [예제: 사용자 지정 기술 만들기](cognitive-search-create-custom-skill-example.md)를 참조하세요.

 필요한 사용자 지정 기능이 무엇이든 관계없이, 사용자 지정 기술을 보강 파이프라인의 나머지 부분에 연결하기 위한 간단하고 명확한 인터페이스가 있습니다. [기능](cognitive-search-defining-skillset.md)에 포함하기 위한 유일한 요구 사항은 기능 전체에서 사용할 수 있는 방식으로 입력을 수락하고 출력을 내보내는 것뿐입니다. 이 문서에서는 보강 파이프라인에 필요한 입력 및 출력 형식을 중심으로 설명합니다.

## <a name="web-api-custom-skill-interface"></a>Web API 사용자 지정 기술 인터페이스

사용자 지정 WebAPI 기술 엔드포인트는 기본적으로 30초 이내에 응답을 반환하지 않으면 시간 제한에 걸립니다. 인덱싱 파이프라인은 동기식이며, 인덱싱을 수행할 경우 해당 기간 내에 응답이 수신되지 않으면 시간 제한 오류가 생성됩니다.  timeout 매개 변수를 설정하여 시간 제한을 90초까지 구성할 수 있습니다.

```json
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill has a 90 second timeout",
        "uri": "https://[your custom skill uri goes here]",
        "timeout": "PT90S",
```

현재, 사용자 지정 기술을 조작하는 유일한 메커니즘은 Web API 인터페이스를 사용하는 것입니다. Web API는 이 섹션에 설명된 요구 사항을 충족해야 합니다.

### <a name="1--web-api-input-format"></a>1.  Web API 입력 형식

Web API가 처리할 레코드 배열을 수락해야 합니다. 각 레코드에는 Web API에 제공되는 입력인 “속성 모음”이 포함되어야 합니다. 

계약 텍스트에 언급된 첫 번째 날짜를 식별하는 간단한 보강자를 만든다고 가정해 보세요. 이 예제에서 기술은 단일 입력 *contractText*를 계약 텍스트로 수락합니다. 또한 기술에는 계약 날짜인 단일 출력이 있습니다. 이 보강자에서 더 흥미로운 점은 이 *contractDate*를 다중 파트 복합 형식의 형태로 반환한다는 것입니다.

Web API가 입력 레코드 배치를 수신할 준비가 되어 있어야 합니다. *values* 배열의 각 멤버는 특정 레코드의 입력을 나타냅니다. 각 레코드에 다음 요소가 있어야 합니다.

+ 특정 레코드의 고유 식별자인 *recordId* 멤버. 보강자는 결과를 반환할 때 호출자가 레코드 결과를 입력과 일치시킬 수 있도록 이 *recordId*를 제공해야 합니다.

+ 기본적으로 각 레코드의 입력 필드 모음인 *data* 멤버.

더 구체적으로 설명하면, 위의 예제에서 Web API는 다음과 같은 요청을 예상해야 합니다.

```json
{
    "values": [
      {
        "recordId": "a1",
        "data":
           {
             "contractText": 
                "This is a contract that was issues on November 3, 2017 and that involves... "
           }
      },
      {
        "recordId": "b5",
        "data":
           {
             "contractText": 
                "In the City of Seattle, WA on February 5, 2018 there was a decision made..."
           }
      },
      {
        "recordId": "c3",
        "data":
           {
             "contractText": null
           }
      }
    ]
}
```
실제로, 서비스는 여기에 표시된 세 개의 레코드만이 아니라 수백 또는 수천 개의 레코드로 호출될 수 있습니다.

### <a name="2-web-api-output-format"></a>2. Web API 출력 형식

출력 형식은 *recordId*와 속성 모음을 포함하는 레코드 집합입니다. 

```json
{
  "values": 
  [
      {
        "recordId": "b5",
        "data" : 
        {
            "contractDate":  { "day" : 5, "month": 2, "year" : 2018 }
        }
      },
      {
        "recordId": "a1",
        "data" : {
            "contractDate": { "day" : 3, "month": 11, "year" : 2017 }                    
        }
      },
      {
        "recordId": "c3",
        "data" : 
        {
        },
        "errors": [ { "message": "contractText field required "}   ],  
        "warnings": [ {"message": "Date not found" }  ]
      }
    ]
}
```

이 특정 예제에는 하나의 출력만 있지만, 둘 이상의 속성을 출력할 수 있습니다. 

### <a name="errors-and-warning"></a>오류 및 경고

앞의 예제와 같이 각 레코드에 대해 오류 및 경고 메시지를 반환할 수 있습니다.

## <a name="consuming-custom-skills-from-skillset"></a>기능의 사용자 지정 기술 사용

Web API 보강자를 만들 때 요청의 일부로 HTTP 헤더 및 매개 변수를 설명할 수 있습니다. 아래 코드 조각은 요청 매개 변수 및 HTTP 헤더를 기능 정의의 일부로 설명할 수 있는 방법을 보여 줍니다.

```json
{
    "skills": [
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill calls an Azure function, which in turn calls TA sentiment",
        "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/DateExtractor?language=en",
        "context": "/document",
        "httpHeaders": {
            "DateExtractor-Api-Key": "foo"
        },
        "inputs": [
          {
            "name": "contractText",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "contractDate",
            "targetName": "date"
          }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>다음 단계

+ [예제: Translator Text API에 대한 사용자 지정 기술 만들기](cognitive-search-create-custom-skill-example.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [기능 만들기(REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [보강 필드를 매핑하는 방법](cognitive-search-output-field-mapping.md)
