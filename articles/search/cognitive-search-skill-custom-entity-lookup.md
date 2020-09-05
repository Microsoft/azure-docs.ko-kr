---
title: 사용자 지정 엔터티 조회 인지 검색 기술
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 인식 검색 파이프라인의 텍스트에서 다른 사용자 지정 엔터티를 추출 합니다. 이 기술은 현재 공개 미리 보기로 제공됩니다.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 5511551f240fe4fdd2f2aa3bc8a3a2615505f35f
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88936115"
---
#     <a name="custom-entity-lookup-cognitive-skill-preview"></a>사용자 지정 엔터티 조회 인식 기술 (미리 보기)

> [!IMPORTANT] 
> 이 기술은 현재 공개 미리 보기로 제공됩니다. 미리 보기 기능은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. 현재 포털 또는 .NET SDK가 지원 되지 않습니다.

사용자 **지정 엔터티 조회** 기술은 사용자가 정의한 단어 및 구 목록에서 텍스트를 검색 합니다. 이 목록을 사용하면 일치하는 엔터티가 있는 모든 문서에 레이블이 지정됩니다. 또한 이 기술은 비슷하지만 정확하지 않는 일치 항목을 찾는 데 적용할 수 있는 유사 항목 일치 수준을 지원합니다.  

이 기술은 Cognitive Services API에 바인딩되지 않으며 미리 보기 기간 동안 무료로 사용할 수 있습니다. 그러나 매일 보강 한도를 재정의 하려면 여전히 [Cognitive Services 리소스를 연결](./cognitive-search-attach-cognitive-services.md)해야 합니다. 일일 한도는 Azure Cognitive Search를 통해 액세스할 때 Cognitive Services에 대 한 무료 액세스에 적용 됩니다.

## <a name="odatatype"></a>@odata.type  
Microsoft. 텍스트. CustomEntityLookupSkill 

## <a name="data-limits"></a>데이터 제한
+ 지원 되는 최대 입력 레코드 크기는 256 MB입니다. 사용자 지정 엔터티 조회 기술로 보내기 전에 데이터를 분할 해야 하는 경우 [텍스트 분할 기술을](cognitive-search-skill-textsplit.md)사용 하는 것이 좋습니다.
+ 지원 되는 최대 엔터티 정의 테이블은 *Entitiesdefinitionuri* 매개 변수를 사용 하 여 제공 되는 경우 10mb입니다. 
+ 엔터티가 인라인으로 정의 된 경우 *inlineEntitiesDefinition* 매개 변수를 사용 하 여 지원 되는 최대 크기는 10kb입니다.

## <a name="skill-parameters"></a>기술 매개 변수

매개 변수는 대/소문자를 구분합니다.

| 매개 변수 이름     | Description |
|--------------------|-------------|
| `entitiesDefinitionUri`    | 일치 시킬 대상 텍스트를 모두 포함 하는 JSON 또는 CSV 파일의 경로입니다. 이 엔터티 정의는 인덱서 실행이 시작 될 때 읽혀집니다. 이 파일 중간 실행에 대 한 모든 업데이트는 다음에 실행 될 때까지 인식 되지 않습니다. 이 구성은 HTTPS를 통해 액세스할 수 있어야 합니다. 필요한 CSV 또는 JSON 스키마는 아래에서 [사용자 지정 엔터티 정의](#custom-entity-definition-format) 형식을 참조 하세요.|
|`inlineEntitiesDefinition` | 인라인 JSON 엔터티 정의입니다. 이 매개 변수는 있는 경우 entitiesDefinitionUri 매개 변수를 대체 합니다. 10 KB 이하의 구성은 인라인으로 제공 될 수 있습니다. 필요한 JSON 스키마는 아래의 [사용자 지정 엔터티 정의](#custom-entity-definition-format) 를 참조 하세요. |
|`defaultLanguageCode` |    필드 입력 텍스트를 토큰화 하 고 설명 하는 데 사용 되는 입력 텍스트의 언어 코드입니다. 지원 되는 언어는 다음과 `da, de, en, es, fi, fr, it, ko, pt` 같습니다. 기본값은 영어 ( `en` )입니다. languagecode-countrycode 형식을 전달하는 경우 형식의 languagecode 부분만 사용됩니다.  |


## <a name="skill-inputs"></a>기술 입력

| 입력 이름      | Description                   |
|---------------|-------------------------------|
| `text`          | 분석할 텍스트입니다.          |
| `languageCode`    | 선택 사항입니다. 기본값은 `"en"`입니다.  |


## <a name="skill-outputs"></a>기술 출력


| 출력 이름      | Description                   |
|---------------|-------------------------------|
| `entities` | 찾은 일치 항목 및 관련 메타 데이터에 대 한 정보를 포함 하는 개체의 배열입니다. 식별 된 각 엔터티에는 다음과 같은 필드가 포함 될 수 있습니다.  <ul> <li> *이름*: 최상위 엔터티를 식별 합니다. 엔터티는 "정규화 된" 형식을 나타냅니다. </li> <li> *id*: "사용자 지정 엔터티 정의 형식"에서 사용자가 정의한 엔터티의 고유 식별자입니다.</li> <li> *설명*: 사용자가 "사용자 지정 엔터티 정의 형식"으로 정의한 엔터티 설명입니다. </li> <li> *유형:* 사용자가 "사용자 지정 엔터티 정의 형식"으로 정의한 엔터티 형식입니다.</li> <li> *하위 유형:* 사용자가 "사용자 지정 엔터티 정의 형식"으로 정의한 엔터티 하위 형식입니다.</li>  <li> *matches*: 소스 텍스트의 해당 엔터티와 일치 하는 각 항목을 설명 하는 컬렉션입니다. 각 일치 항목에는 다음 멤버가 포함 됩니다. </li> <ul> <li> *text*: 소스 문서와 일치 하는 원시 텍스트입니다. </li> <li> *offset*: 텍스트에서 일치 항목을 찾은 위치입니다. </li> <li> *length*: 일치 하는 텍스트의 길이입니다. </li> <li> *Matchdistance*:이와 일치 하는 문자 수가 원래 엔터티 이름 또는 별칭과 다릅니다.  </li> </ul> </ul>
  |

## <a name="custom-entity-definition-format"></a>사용자 지정 엔터티 정의 형식

사용자 지정 엔터티 목록을 사용자 지정 엔터티 조회 기술에 제공 하는 세 가지 방법이 있습니다. 에서 목록을 제공할 수 있습니다. CSV 파일. 기술 정의의 일부로 JSON 파일 또는 인라인 정의로 구성 됩니다.  

정의 파일이 이면이 고, CSV 또는 JSON 파일의 경우 파일 경로를 *Entitiesdefinitionuri* 매개 변수의 일부로 제공 해야 합니다. 이 경우 파일은 각 인덱서 실행이 시작 될 때 한 번 다운로드 됩니다. 인덱서를 실행 하려는 경우에만 파일에 액세스할 수 있어야 합니다. 또한 파일은 u t f-8로 인코딩해야 합니다.

정의가 인라인으로 제공 되는 경우 *inlineEntitiesDefinition* 기술 매개 변수의 콘텐츠로 인라인으로 제공 되어야 합니다. 

### <a name="csv-format"></a>CSV 형식

파일에 대 한 경로를 제공 하 고 *Entif Definitionuri*  기술 매개 변수에서 설정 하 여 CSV (쉼표로 구분 된 값) 파일에서 찾을 사용자 지정 엔터티의 정의를 제공할 수 있습니다. 경로는 https 위치에 있어야 합니다. 정의 파일의 크기는 최대 10mb 일 수 있습니다.

CSV 형식은 간단 합니다. 각 줄은 아래와 같이 고유한 엔터티를 나타냅니다.

```
Bill Gates, BillG, William H. Gates
Microsoft, MSFT
Satya Nadella 
```

이 경우 엔터티 (청구서 게이트, Satya Nadella, Microsoft)로 반환 될 수 있는 엔터티는 세 가지 이지만 줄의 조건 (별칭)이 텍스트에서 일치 하는 경우 식별 됩니다. 예를 들어, 문서에 "William" 문자열이 있는 경우 "청구서 게이트" 엔터티와 일치 하는 항목이 반환 됩니다.

### <a name="json-format"></a>JSON 형식

JSON 파일 에서도 검색할 사용자 지정 엔터티의 정의를 제공할 수 있습니다. JSON 형식을 사용 하면 용어 마다 일치 규칙을 정의할 수 있으므로 좀 더 유연 하 게 사용할 수 있습니다. 예를 들어 각 용어에 대해 유사 항목 일치 거리 (Damerau-Levenshtein distance)를 지정 하거나 일치 항목이 대/소문자를 구분 하는지 여부를 지정할 수 있습니다. 

 CSV 파일의 경우와 마찬가지로 JSON 파일에 대 한 경로를 제공 하 고 *Entitiesdefinitionuri* 기술 매개 변수에 설정 해야 합니다. 경로는 https 위치에 있어야 합니다. 정의 파일의 크기는 최대 10mb 일 수 있습니다.

가장 기본적인 JSON 사용자 지정 엔터티 목록 정의는 일치 시킬 엔터티의 목록이 될 수 있습니다.

```json
[ 
    { 
        "name" : "Bill Gates"
    }, 
    { 
        "name" : "Microsoft"
    }, 
    { 
        "name" : "Satya Nadella"
    }
]
```

JSON 정의의 좀 더 복잡 한 예는 필요에 따라 각 엔터티의 id, 설명, 유형 및 하위 유형 뿐만 아니라 다른 *별칭*을 제공할 수 있습니다. 별칭 용어가 일치 하면 엔터티도 반환 됩니다.

```json
[ 
    { 
        "name" : "Bill Gates",
        "description" : "Microsoft founder." ,
        "aliases" : [ 
            { "text" : "William H. Gates", "caseSensitive" : false },
            { "text" : "BillG", "caseSensitive" : true }
        ]
    }, 
    { 
        "name" : "Xbox One", 
        "type": "Harware",
        "subtype" : "Gaming Device",
        "id" : "4e36bf9d-5550-4396-8647-8e43d7564a76",
        "description" : "The Xbox One product"
    }, 
    { 
        "name" : "LinkedIn" , 
        "description" : "The LinkedIn company", 
        "id" : "differentIdentifyingScheme123", 
        "fuzzyEditDistance" : 0 
    }, 
    { 
        "name" : "Microsoft" , 
        "description" : "Microsoft Corporation", 
        "id" : "differentIdentifyingScheme987", 
        "defaultCaseSensitive" : false, 
        "defaultFuzzyEditDistance" : 1, 
        "aliases" : [ 
            { "text" : "MSFT", "caseSensitive" : true }
        ]
    } 
] 
```

아래 표에서는 일치 시킬 엔터티를 정의할 때 설정할 수 있는 다양 한 구성 매개 변수를 자세히 설명 합니다.

|  필드 이름  |        Description  |
|--------------|----------------------|
| `name` | 최상위 엔터티 설명자입니다. 기술 출력의 일치 항목은이 이름으로 그룹화 되 고 검색 되는 텍스트의 "정규화 된" 형식을 나타내야 합니다.  |
| `description`  | 필드 이 필드는 일치 하는 텍스트에 대 한 사용자 지정 메타 데이터에 대 한 통과로 사용할 수 있습니다. 이 필드의 값은 해당 엔터티와 일치 하는 모든 항목이 기술 출력에 표시 됩니다. |
| `type` | 필드 이 필드는 일치 하는 텍스트에 대 한 사용자 지정 메타 데이터에 대 한 통과로 사용할 수 있습니다. 이 필드의 값은 해당 엔터티와 일치 하는 모든 항목이 기술 출력에 표시 됩니다. |
| `subtype` | 필드 이 필드는 일치 하는 텍스트에 대 한 사용자 지정 메타 데이터에 대 한 통과로 사용할 수 있습니다. 이 필드의 값은 해당 엔터티와 일치 하는 모든 항목이 기술 출력에 표시 됩니다. |
| `id` | 필드 이 필드는 일치 하는 텍스트에 대 한 사용자 지정 메타 데이터에 대 한 통과로 사용할 수 있습니다. 이 필드의 값은 해당 엔터티와 일치 하는 모든 항목이 기술 출력에 표시 됩니다. |
| `caseSensitive` | 필드 기본값은 false입니다. 엔터티 이름과 비교할 때 문자 대/소문자를 구분 해야 하는지 여부를 나타내는 부울 값입니다. "Microsoft"와 일치 하는 대/소문자를 구분 하지 않는 샘플은 microsoft, microsoft, MICROSOFT입니다. |
| `fuzzyEditDistance` | 필드 기본값은 0입니다. 최 댓 값은 5입니다. 여전히 엔터티 이름과 일치 하는 항목을 구성 하는 허용 되는 문자 수를 나타냅니다. 지정 된 일치 항목에 대해 가능한 최소 허용량이 반환 됩니다.  예를 들어, 편집 거리가 3으로 설정 된 경우 "Windows 10"은 여전히 "Windows", "Windows10" 및 "windows 7"과 일치 합니다. <br/> 대/소문자 구분이 false로 설정 된 경우 대/소문자 차이는 허용량 허용 오차에 계산 되지 않지만 그렇지 않은 경우에는 그렇지 않습니다. |
| `defaultCaseSensitive` | 필드 이 엔터티에 대 한 기본 대/소문자 구분 값을 변경 합니다. 모든 별칭 caseSensitive 값의 기본값을 변경 하는 데 사용 됩니다. |
| `defaultFuzzyEditDistance` | 필드 이 엔터티에 대 한 기본 유사 항목 편집 거리 값을 변경 합니다. 모든 별칭 fuzzyEditDistance 값의 기본값을 변경 하는 데 사용할 수 있습니다. |
| `aliases` | 필드 루트 엔터티 이름에 대 한 대체 철자 또는 동의어를 지정 하는 데 사용할 수 있는 복합 개체의 배열입니다. |

| 별칭 속성 | Description |
|------------------|-------------|
| `text`  | 일부 대상 엔터티 이름의 대체 철자 또는 표현입니다.  |
| `caseSensitive` | 필드 위의 루트 엔터티 "caseSensitive" 매개 변수와 동일 하 게 작동 하지만이 별칭 하나에만 적용 됩니다. |
| `fuzzyEditDistance` | 필드 위의 루트 엔터티 "fuzzyEditDistance" 매개 변수와 동일 하 게 작동 하지만이 별칭 하나에만 적용 됩니다. |


### <a name="inline-format"></a>인라인 형식

경우에 따라 기술 정의에 직접 인라인으로 일치 시킬 사용자 지정 엔터티 목록을 제공 하는 것이 더 편리할 수 있습니다. 이 경우 위에 설명 된 것과 비슷한 JSON 형식을 사용할 수 있지만 기술 정의에서 인라인 됩니다.
크기가 10kb 미만 (직렬화 된 크기) 인 구성만 인라인으로 정의 될 수 있습니다. 

##    <a name="sample-definition"></a>샘플 정의

인라인 서식을 사용 하는 샘플 기술 정의는 다음과 같습니다.

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.CustomEntityLookupSkill",
    "context": "/document",
    "inlineEntitiesDefinition": 
    [
      { 
        "name" : "Bill Gates",
        "description" : "Microsoft founder." ,
        "aliases" : [ 
            { "text" : "William H. Gates", "caseSensitive" : false },
            { "text" : "BillG", "caseSensitive" : true }
        ]
      }, 
      { 
        "name" : "Xbox One", 
        "type": "Hardware",
        "subtype" : "Gaming Device",
        "id" : "4e36bf9d-5550-4396-8647-8e43d7564a76",
        "description" : "The Xbox One product"
      }
    ],    
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "entities",
        "targetName": "matchedEntities"
      }
    ]
  }
```
또는 엔터티 정의 파일에 대 한 포인터를 제공 하려는 경우에는 형식을 사용 하는 샘플 기술 정의가 `entitiesDefinitionUri` 아래에 표시 됩니다.

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.CustomEntityLookupSkill",
    "context": "/document",
    "entitiesDefinitionUri": "https://myblobhost.net/keyWordsConfig.csv",    
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "entities",
        "targetName": "matchedEntities"
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
             "text": "The company, Microsoft, was founded by Bill Gates. Microsoft's gaming console is called Xbox",
             "languageCode": "en"
           }
      }
    ]
}
```

##    <a name="sample-output"></a>샘플 출력

```json
  { 
    "values" : 
    [ 
      { 
        "recordId": "1", 
        "data" : { 
          "entities": [
            { 
              "name" : "Microsoft", 
              "description" : "This document refers to Microsoft the company", 
              "id" : "differentIdentifyingScheme987", 
              "matches" : [ 
                { 
                  "text" : "microsoft", 
                  "offset" : 13, 
                  "length" : 9, 
                  "matchDistance" : 0 
                }, 
                { 
                  "text" : "Microsoft",
                  "offset" : 49, 
                  "length" : 9, 
                  "matchDistance" : 0
                }
              ] 
            },
            { 
              "name" : "Bill Gates",
              "description" : "William Henry Gates III, founder of Microsoft.", 
              "matches" : [
                { 
                  "text" : "Bill Gates",
                  "offset" : 37, 
                  "length" : 10,
                  "matchDistance" : 0 
                }
              ]
            }
          ] 
        } 
      } 
    ] 
  } 
```

## <a name="errors-and-warnings"></a>오류 및 경고

### <a name="warning-reached-maximum-capacity-for-matches-skipping-all-further-duplicate-matches"></a>경고: 일치에 대 한 최대 용량에 도달 하 여 모든 중복 일치 항목을 건너뜁니다.

이 경고는 검색 된 일치 수가 허용 되는 최대값 보다 큰 경우에 내보내집니다. 이 경우 중복 일치를 포함 하는 것이 중지 됩니다. 이 방법이 허용 되지 않는 경우 개별 사용 사례를 지원할 수 있도록 [지원 티켓](https://ms.portal.azure.com/#create/Microsoft.Support) 을 제출 하세요.

## <a name="see-also"></a>참고 항목

+ [기본 제공 기술](cognitive-search-predefined-skills.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [엔터티 인식 기술 (잘 알려진 엔터티를 검색 하려면)](cognitive-search-skill-entity-recognition.md)