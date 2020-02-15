---
title: 기술 개념 및 워크플로
titleSuffix: Azure Cognitive Search
description: 기술력과는 Azure Cognitive Search에서 AI 보강 파이프라인을 작성 하는 위치입니다. 기술 컴퍼지션에 대 한 중요 한 개념 및 세부 정보를 알아보세요.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8b45840215092281c7fbc8d499e26b095b374dd6
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191037"
---
# <a name="skillset-concepts-and-composition-in-azure-cognitive-search"></a>Azure Cognitive Search의 기술 개념 및 컴퍼지션

이 문서는 보강 파이프라인이 작동 하는 방식을 심층적으로 이해 해야 하 고 AI 보강 프로세스를 개념적으로 이해 하 고 있다고 가정 하는 개발자를 위한 것입니다. 이러한 개념을 처음 접하는 경우 다음으로 시작 합니다.
+ [Azure Cognitive Search의 AI 보강](cognitive-search-concept-intro.md)
+ [지식 저장소(미리 보기)](knowledge-store-concept-intro.md)

## <a name="specify-the-skillset"></a>기술 지정
기술는 인덱싱 중에 텍스트 또는 이미지 콘텐츠를 분석, 변환 및 보강 하는 데 사용 되는 인식 기술 컬렉션을 지정 하는 Azure Cognitive Search의 재사용 가능한 리소스입니다. 기술를 만들면 데이터 수집 단계에서 텍스트 및 이미지 강화를 연결 하 여 원시 콘텐츠에서 새 정보 및 구조를 추출 하 고 만들 수 있습니다.

기술에는 다음과 같은 세 가지 속성이 있습니다.

+   ```skills```각 기술에 필요한 입력을 기반으로 하 여 실행 순서를 결정 하는 순서가 지정 되지 않은 기술 모음입니다.
+   ```cognitiveServices```호출한 인식 기술을 청구 하는 데 필요한 인식 서비스 키
+   ```knowledgeStore```보강 문서가 프로젝션 되는 저장소 계정입니다.



기술력과은 JSON으로 작성 됩니다. [식 언어](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional)를 사용 하 여 루프와 [분기](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional) 를 사용 하 여 복잡 한 기술력과을 빌드할 수 있습니다. 식 언어는 보강 트리의 노드를 식별 하기 위해 몇 가지 수정으로 [JSON 포인터](https://tools.ietf.org/html/rfc6901) 경로 표기법을 사용 합니다. ```"/"```는 트리에서 더 낮은 수준으로 이동 하 고 ```"*"```는 컨텍스트에서 각각의 연산자 역할을 합니다. 이러한 개념은 예를 들어 잘 설명 되어 있습니다. 몇 가지 개념과 기능을 설명 하기 위해 [호텔 리뷰 샘플](knowledge-store-connect-powerbi.md) 기술을 살펴보겠습니다. 기술를 보려면 데이터 가져오기 워크플로를 수행한 후에 REST API 클라이언트를 사용 하 여 [기술를 가져와야](https://docs.microsoft.com/rest/api/searchservice/get-skillset)합니다.

### <a name="enrichment-tree"></a>보강 트리

기술가 문서를 점진적으로 강화 하는 방법을 구상 하기 위해 모든 보강 앞에 문서 모양의 모양을 시작 하겠습니다. 문서 크랙의 출력은 데이터 원본 및 선택한 특정 구문 분석 모드에 따라 달라 집니다. 검색 인덱스에 데이터를 추가할 때 [필드 매핑에서](search-indexer-field-mappings.md) 콘텐츠를 원본으로 지정할 수 있는 문서의 상태 이기도 합니다.
![파이프라인의 지식 저장소 다이어그램](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "파이프라인의 지식 저장소 다이어그램")

문서가 보강 파이프라인에 있으면 콘텐츠 및 관련 강화 트리로 표시 됩니다. 이 트리는 문서 크랙의 출력으로 인스턴스화됩니다. 보강 트리 형식을 사용 하면 보강 파이프라인이 메타 데이터를 기본 데이터 형식으로 연결 하 고, 유효한 JSON 개체가 아니지만 유효한 JSON 형식으로 프로젝션 할 수 있습니다. 다음 표에서는 보강 파이프라인에 입력 하는 문서의 상태를 보여 줍니다.

|데이터 원본 \ 구문 분석 모드|기본값|JSON, JSON 줄 & CSV|
|---|---|---|
|Blob Storage|/문서/내용<br>/document/normalized_images/*<br>…|/document/{key1}<br>/document/{key2}<br>…|
|SQL|/document/{column1}<br>/document/{column2}<br>…|해당 없음 |
|Cosmos DB|/document/{key1}<br>/document/{key2}<br>…|해당 없음|

 기술이 실행 되 면 보강 트리에 새 노드를 추가 합니다. 이러한 새 노드는 다운스트림 기술에 대 한 입력으로 사용 하거나, 기술 자료 저장소로 프로젝션 하거나, 인덱스 필드에 매핑할 수 있습니다. 강화은 변경할 수 없습니다. 만든 후에는 노드를 편집할 수 없습니다. 기술력과가 더 복잡 하므로 보강 트리가 필요 하지만 보강 트리의 모든 노드가 인덱스나 기술 자료 저장소로 만들어야 하는 것은 아닙니다. 

인덱스 또는 기술 자료 저장소에 강화의 하위 집합만 선택적으로 보관할 수 있습니다.
이 문서의 나머지 부분에서는 [호텔 리뷰 예](https://docs.microsoft.com/azure/search/knowledge-store-connect-powerbi)를 사용 하는 것으로 가정 하지만 동일한 개념은 다른 모든 데이터 원본의 문서 보강 적용 됩니다.

### <a name="context"></a>Context
각 기술에는 컨텍스트가 필요 합니다. 컨텍스트는 다음을 결정 합니다.
+   선택 된 노드를 기준으로 기술이 실행 되는 횟수입니다. 형식 컬렉션의 컨텍스트 값의 경우 끝에 ```/*```을 추가 하면 컬렉션의 각 인스턴스에 대해 한 번씩 기술이 호출 됩니다. 
+   보강 트리에서는 기술 출력이 추가 됩니다. 출력은 항상 트리에 컨텍스트 노드의 자식으로 추가 됩니다. 
+   입력의 셰이프입니다. 다중 수준 컬렉션의 경우 컨텍스트를 부모 컬렉션으로 설정 하면 기술에 대 한 입력 모양에 영향을 줍니다. 예를 들어 국가 목록이 포함 된 보강 트리가 있는 경우 각 보강은 zipcodes 목록을 포함 하는 상태 목록을 포함 합니다.

|Context|입력|입력 모양|기술 호출|
|---|---|---|---|
|```/document/countries/*``` |```/document/countries/*/states/*/zipcodes/*``` |국가의 모든 zipcodes 목록 |국가 당 한 번 |
|```/document/countries/*/states/*``` |```/document/countries/*/states/*/zipcodes/*``` |상태의 zipcodes 목록 | 국가 및 상태 조합 당 한 번|

### <a name="sourcecontext"></a>SourceContext

`sourceContext`는 기술 입력 및 [프로젝션](knowledge-store-projection-overview.md)에서만 사용 됩니다. 이 클래스는 여러 수준의 중첩 된 개체를 생성 하는 데 사용 됩니다. 기술 자료 저장소에 기술 또는 프로젝트에 대 한 입력으로 전달 하는 새 개체를 만들어야 할 수도 있습니다. 보강 노드는 보강 트리의 유효한 JSON 개체가 될 수 없고 트리의 노드를 참조 하는 경우에만 생성 될 때 노드의 상태를 반환할 수 있습니다. 강화를 기술 입력 이나 프로젝션으로 사용 하면 올바른 형식의 JSON 개체를 만들어야 합니다. `sourceContext`를 사용 하 여 컨텍스트를 사용 하는 경우에는 여러 기술이 필요한 계층적 익명 형식 개체를 생성할 수 있습니다. `sourceContext` 사용은 다음 섹션에 나와 있습니다. 보강를 생성 한 기술 출력에서 기본 형식이 아닌 유효한 JSON 개체 인지 확인 합니다.

### <a name="projections"></a>프로젝션

프로젝션은 보강 트리에서 기술 자료 저장소에 저장 되는 노드를 선택 하는 프로세스입니다. 프로젝션은 테이블 또는 개체 프로젝션으로 출력할 수 있는 문서의 사용자 지정 모양 (content 및 강화)입니다. 프로젝션 사용에 대 한 자세한 내용은 [프로젝션 작업](knowledge-store-projection-overview.md)을 참조 하세요.

![필드 매핑 옵션](./media/cognitive-search-working-with-skillsets/field-mapping-options.png "보강 파이프라인에 대 한 필드 매핑 옵션")

위의 다이어그램에서는 보강 파이프라인의 위치를 기준으로 작업 하는 선택기에 대해 설명 합니다.

## <a name="generate-enriched-data"></a>보강 데이터 생성 

이제 호텔 리뷰 기술을 단계별로 진행 해 보겠습니다. [자습서](knowledge-store-connect-powerbi.md) 에 따라 기술를 만들거나 기술를 [볼](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/samples/skillset.json) 수 있습니다. 다음을 살펴보겠습니다.

* 보강 트리가 각 기술 실행과 어떻게 진화 합니까? 
* 기술이 실행 되는 횟수를 결정 하는 데 컨텍스트와 입력이 작동 하는 방식 
* 컨텍스트를 기반으로 하는 입력의 형태입니다. 

인덱서에서 구분 된 텍스트 구문 분석 모드를 사용 하 고 있으므로 보강 프로세스 내의 문서는 CSV 파일 내의 단일 행을 나타냅니다.

### <a name="skill-1-split-skill"></a>기술 #1: 분할 기술 

![문서 크랙 후 보강 트리](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "문서를 크랙 한 후 기술 실행 전 보강 트리")

이 기술은 ```"/document/reviews_text"```의 기술 컨텍스트에서 `reviews_text`에 대해 한 번 실행 됩니다. 기술 출력은 `reviews_text`를 5000 문자 세그먼트로 청크 분할 하는 목록입니다. 분할 된 기술 출력의 이름은 `pages`이 고 보강 트리에 추가 됩니다. `targetName` 기능을 사용 하면 보강 트리에 추가 되기 전에 기술 출력의 이름을 바꿀 수 있습니다.

이제 보강 트리에는 기술 컨텍스트 아래에 새로운 노드가 배치 됩니다. 이 노드는 모든 기술, 프로젝션 또는 출력 필드 매핑에서 사용할 수 있습니다.


모든 강화에 대 한 루트 노드가 `"/document"`되었습니다. Blob 인덱서를 사용 하는 경우 `"/document"` 노드에 `"/document/content"` 및 `"/document/normalized_images"`의 자식 노드가 있습니다. 이 예에서와 같이 CSV 데이터로 작업 하는 경우 열 이름은 `"/document"`아래의 노드에 매핑됩니다. 기술에 의해 노드에 추가 된 강화에 액세스 하려면 보강에 대 한 전체 경로가 필요 합니다. 예를 들어 ```pages``` 노드의 텍스트를 다른 기술에 대 한 입력으로 사용 하려는 경우에는 ```"/document/reviews_text/pages/*"```으로 지정 해야 합니다.
 
 ![기술 #1 후 보강 트리](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "기술 #1 실행 후 보강 트리")

### <a name="skill-2-language-detection"></a>기술 #2 언어 검색
 언어 검색 기술은 기술에서 정의 된 세 번째 (기술 #3) 기술 이지만 다음에 실행할 수 있는 기술입니다. 입력을 요구 하 여 차단 되지 않으므로 이전 기술과 병렬로 실행 됩니다. 앞에 있는 분할 기술과 마찬가지로 언어 감지 기술도 각 문서에 대해 한 번 호출 됩니다. 이제 보강 트리에는 언어에 대 한 새 노드가 있습니다.
 ![기술 #2 후 보강 트리](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "기술 #2 실행 후 보강 트리")
 
 ### <a name="skill-3-key-phrases-skill"></a>기술 #3: 핵심 구 기술 

```/document/reviews_text/pages/*``` 컨텍스트가 지정 된 경우 `pages` 컬렉션의 각 항목에 대해 키 구 기술이 한 번씩 호출 됩니다. 기술에 대 한 출력은 연결 된 page 요소 아래에 노드가 됩니다. 

 이제 기술의 나머지 기술을 살펴보고 각 기술을 실행 하 여 강화의 트리가 어떻게 계속 증가 하는지 시각화할 수 있습니다. 병합 기술 및 shaper 기술과 같은 일부 기술은 새 노드도 만들지만 기존 노드의 데이터만 사용 하 고 net new 강화는 만들지 않습니다.

![모든 기술 후 보강 트리](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "모든 기술 후 보강 트리")

위의 트리에서 커넥터 색은 강화가 다른 기술로 만들어졌지만 노드를 개별적으로 처리 해야 하며 부모 노드를 선택할 때 반환 되는 개체의 일부가 되지 않음을 의미 합니다.

## <a name="save-enrichments-in-a-knowledge-store"></a>기술 자료 저장소에 강화 저장 

또한 기술력과는 보강 문서를 테이블 또는 개체로 예측할 수 있는 기술 자료 저장소를 정의 합니다. 보강 데이터를 기술 자료 저장소에 저장 하려면 보강 문서에 대 한 일련의 프로젝션을 정의 합니다. 기술 자료 저장소에 대해 자세히 알아보려면 [기술 자료 저장소 개요](knowledge-store-concept-intro.md) 를 참조 하세요.

### <a name="slicing-projections"></a>조각화 프로젝션

테이블 프로젝션 그룹을 정의할 때 보강 트리의 단일 노드를 여러 관련 테이블로 분할할 수 있습니다. 기존 테이블 프로젝션의 자식인 원본 경로를 사용 하 여 테이블을 추가 하는 경우 결과 자식 노드는 기존 테이블 프로젝션의 자식이 아니라 새로운 관련 테이블에 프로젝션 됩니다. 이러한 조각화 기법을 사용 하면 모든 테이블 프로젝션의 원본이 될 수 있는 shaper 기술에서 단일 노드를 정의할 수 있습니다. 

### <a name="shaping-projections"></a>예측 모양 지정

프로젝션을 정의 하는 방법에는 두 가지가 있습니다. Shaper 기술을 사용 하 여 프로젝션 하는 모든 강화에 대 한 루트 노드인 새 노드를 만들 수 있습니다. 그런 다음, 프로젝션에서 shaper 기술에 대 한 출력만 참조할 수 있습니다. 프로젝션 정의 자체 내에서 프로젝션을 인라인 할 수도 있습니다.

Shaper 접근 방식은 인라인 셰이핑 보다 더 자세한 정보를 표시 하지만 보강 트리의 모든 변경이는 기술 내에 포함 되 고 출력은 다시 사용할 수 있는 개체 인지 확인 합니다. 인라인 셰이핑을 사용 하면 필요한 셰이프를 만들 수 있지만 익명 개체 이며 정의 된 프로젝션 에서만 사용할 수 있습니다. 접근 방식은 함께 또는 별도로 사용할 수 있습니다. 포털 워크플로에서 생성 된 기술에는 둘 다 포함 되어 있습니다. 테이블 프로젝션에 shaper 기술을 사용 하지만 키 구 테이블을 프로젝션 하는 인라인 셰이핑도 사용 합니다.

예제를 확장 하려면 인라인 셰이핑을 제거 하 고 shaper 기술을 사용 하 여 키 구에 대 한 새 노드를 만드는 방법을 선택할 수 있습니다. `hotelReviewsDocument`, `hotelReviewsPages`및 `hotelReviewsKeyPhrases`라는 세 개의 테이블로 프로젝션 된 셰이프를 만들려면 다음 섹션에서 두 가지 옵션에 대해 설명 합니다.


#### <a name="shaper-skill-and-projection"></a>Shaper 기술 및 프로젝션 

> [!Note]
> 문서 테이블의 일부 열이이 예제에서 간단 하 게 제거 되었습니다.
>
```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "#5",
    "description": null,
    "context": "/document",
    "inputs": [        
        {
            "name": "reviews_text",
            "source": "/document/reviews_text",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "reviews_title",
            "source": "/document/reviews_title",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "AzureSearch_DocumentKey",
            "source": "/document/AzureSearch_DocumentKey",
            "sourceContext": null,
            "inputs": []
        },  
        {
            "name": "pages",
            "source": null,
            "sourceContext": "/document/reviews_text/pages/*",
            "inputs": [
                {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "LanguageCode",
                    "source": "/document/Language",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "Page",
                    "source": "/document/reviews_text/pages/*",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "keyphrase",
                    "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                    "inputs": [
                        {
                            "source": "/document/reviews_text/pages/*/Keyphrases/*",
                            "name": "Keyphrases"
                        }
                    ]
                }
            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "tableprojection"
        }
    ]
}
```

위의 `outputs` 섹션에 정의 된 `tableprojection` 노드를 사용 하 여 이제 조각화 기능을 사용 하 여 `tableprojection` 노드의 일부를 여러 테이블로 프로젝션 할 수 있습니다.

> [!Note]
> 이는 기술 자료 저장소 구성 내의 프로젝션 코드 조각입니다.
>
```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsDocument",
                "generatedKeyName": "Documentid",
                "source": "/document/tableprojection"
            },
            {
                "tableName": "hotelReviewsPages",
                "generatedKeyName": "Pagesid",
                "source": "/document/tableprojection/pages/*"
            },
            {
                "tableName": "hotelReviewsKeyPhrases",
                "generatedKeyName": "KeyPhrasesid",
                "source": "/document/tableprojection/pages/*/keyphrase/*"
            }
        ]
    }
]
```

#### <a name="inline-shaping-projections"></a>인라인 모양 지정 프로젝션

프로젝션에 필요한 모든 셰이프가 필요할 때 생성 되므로 인라인 셰이핑 방법에는 shaper 기술이 필요 하지 않습니다. 이전 예제와 동일한 데이터를 프로젝션 하기 위해 인라인 프로젝션 옵션은 다음과 같습니다.

```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsInlineDocument",
                "generatedKeyName": "Documentid",
                "sourceContext": "/document",     
                "inputs": [
                    {
                        "name": "reviews_text",
                        "source": "/document/reviews_text"
                    },
                    {
                        "name": "reviews_title",
                        "source": "/document/reviews_title"
                    },
                    {
                        "name": "AzureSearch_DocumentKey",
                        "source": "/document/AzureSearch_DocumentKey"
                    }                             
                ]
            },
            {
                "tableName": "hotelReviewsInlinePages",
                "generatedKeyName": "Pagesid",
                "sourceContext": "/document/reviews_text/pages/*",
                "inputs": [
                        {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment"
                    },
                    {
                        "name": "LanguageCode",
                        "source": "/document/Language"
                    },
                    {
                        "name": "Page",
                        "source": "/document/reviews_text/pages/*"
                    }
                ]
            },
            {
                "tableName": "hotelReviewsInlineKeyPhrases",
                "generatedKeyName": "KeyPhraseId",
                "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                "inputs": [
                    {
                        "name": "Keyphrases",
                        "source": "/document/reviews_text/pages/*/Keyphrases/*"
                    }
                ]
            }
        ]
    }
]
```
  
두 방법의 한 가지 관찰은 `"Keyphrases"`의 값이 `"sourceContext"`를 사용 하 여 예측 되는 방법입니다. 문자열 컬렉션을 포함 하는 `"Keyphrases"` 노드 자체는 페이지 텍스트의 자식입니다. 그러나 프로젝션에 JSON 개체가 필요 하 고 페이지가 기본 (문자열) 이기 때문에 `"sourceContext"`를 사용 하 여 명명 된 속성을 가진 개체에 키 구를 래핑합니다. 이 방법을 사용 하면 기본 형식을 독립적으로 프로젝션 할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음 단계로 인식 기술을 사용 하 여 첫 번째 기술를 만듭니다.

> [!div class="nextstepaction"]
> [첫 번째 기술를 만듭니다](cognitive-search-defining-skillset.md).
