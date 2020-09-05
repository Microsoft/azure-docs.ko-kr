---
title: 지식 저장소에서 프로젝션 정의
titleSuffix: Azure Cognitive Search
description: Power BI 또는 Azure ML과 함께 사용 하기 위해 문서를 기술 자료 저장소로 프로젝션 하는 방법에 대 한 일반적인 패턴의 예입니다.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 18646fc92dda7cebb7a8c1030285956d8fe4df74
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88924166"
---
# <a name="how-to-shape-and-export-enrichments"></a>강화 셰이프 및 내보내기 방법

프로젝션은 기술 자료 저장소에 있는 보강 문서의 물리적 식입니다. 보강 문서를 효과적으로 사용 하려면 구조가 필요 합니다. 이 문서에서는 구조와 관계를 모두 탐색 하 고, 프로젝션 속성을 빌드하는 방법 및 생성 된 프로젝션 형식 간에 데이터를 연결 하는 방법을 배웁니다. 

프로젝션을 만들려면 [Shaper 기술을](cognitive-search-skill-shaper.md) 사용 하 여 사용자 지정 개체를 만들거나 프로젝션 정의 내에서 인라인 셰이핑 구문을 사용 하 여 데이터 모양을 지정 합니다. 

데이터 셰이프에는 프로젝트에 사용 되는 모든 데이터가 포함 되며 노드 계층 구조로 구성 됩니다. 이 문서에서는 보고, 분석 또는 다운스트림 처리에 취약 물리적 구조에 프로젝션 할 수 있도록 데이터를 셰이핑 하는 몇 가지 기술을 보여 줍니다. 

이 문서에 나와 있는 예제는이 [REST API 샘플](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json)에서 찾을 수 있습니다 .이 샘플은 HTTP 클라이언트에서 다운로드 하 여 실행할 수 있습니다.

## <a name="introduction-to-projection-examples"></a>프로젝션 예제 소개

[투영](knowledge-store-projection-overview.md)에는 다음 세 가지 유형이 있습니다.

+ 테이블
+ 개체
+ Files

테이블 프로젝션은 Azure Table storage에 저장 됩니다. 개체 및 파일 프로젝션은 blob 저장소에 기록 되 고, 개체 프로젝션이 JSON 파일로 저장 되며, 원본 문서의 콘텐츠 뿐만 아니라 기술 출력 또는 강화 포함 될 수 있습니다. 또한 보강 파이프라인은 이미지와 같은 이진 파일을 추출할 수 있습니다. 이러한 이진 파일은 파일 프로젝션으로 프로젝션 됩니다. 이진 개체를 개체 프로젝션으로 프로젝션 하면 연결 된 메타 데이터만 JSON blob으로 저장 됩니다. 

데이터 셰이핑과 프로젝션 간의 교차를 이해 하기 위해 다양 한 구성을 탐색 하기 위한 기준으로 다음 기술을 사용 합니다. 이 기술는 원시 이미지 및 텍스트 콘텐츠를 처리 합니다. 예측은 원하는 시나리오에 대 한 문서의 내용과 기술 출력으로 정의 됩니다.

> [!IMPORTANT] 
> 프로젝션을 시험해 볼 때 비용 제어를 보장 하도록 [인덱서 캐시 속성을 설정](search-howto-incremental-index.md) 하는 것이 유용 합니다. 프로젝션을 편집 하면 인덱서 캐시가 설정 되지 않은 경우 전체 문서가 다시 보강 됩니다. 캐시를 설정 하 고 프로젝션을 업데이트 한 경우 이전에 보강 문서에 대 한 기술 실행을 실행 해도 새로운 Cognitive Services 요금이 발생 하지 않습니다.

```json
{
    "name": "azureblob-skillset",
    "description": "Skillset created from the portal. skillsetName: azureblob-skillset; contentField: merged_content; enrichmentGranularity: document; knowledgeStoreStorageAccount: confdemo;",
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "name": "#1",
            "description": null,
            "context": "/document/merged_content",
            "categories": [
                "Person",
                "Quantity",
                "Organization",
                "URL",
                "Email",
                "Location",
                "DateTime"
            ],
            "defaultLanguageCode": "en",
            "minimumPrecision": null,
            "includeTypelessEntities": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "persons",
                    "targetName": "people"
                },
                {
                    "name": "organizations",
                    "targetName": "organizations"
                },
                {
                    "name": "locations",
                    "targetName": "locations"
                },
                {
                    "name": "entities",
                    "targetName": "entities"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "name": "#2",
            "description": null,
            "context": "/document/merged_content",
            "defaultLanguageCode": "en",
            "maxKeyPhraseCount": null,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/language"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyphrases"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "name": "#3",
            "description": null,
            "context": "/document",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/merged_content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "language"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
            "name": "#4",
            "description": null,
            "context": "/document",
            "insertPreTag": " ",
            "insertPostTag": " ",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "itemsToInsert",
                    "source": "/document/normalized_images/*/text"
                },
                {
                    "name": "offsets",
                    "source": "/document/normalized_images/*/contentOffset"
                }
            ],
            "outputs": [
                {
                    "name": "mergedText",
                    "targetName": "merged_content"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
            "name": "#5",
            "description": null,
            "context": "/document/normalized_images/*",
            "textExtractionAlgorithm": "printed",
            "lineEnding": "Space",
            "defaultLanguageCode": "en",
            "detectOrientation": true,
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                }
            ],
            "outputs": [
                {
                    "name": "text",
                    "targetName": "text"
                },
                {
                    "name": "layoutText",
                    "targetName": "layoutText"
                }
            ]
        }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "DemosCS",
        "key": "<COGNITIVE SERVICES KEY>"
    },
    "knowledgeStore": null
}
```

이 기술를 사용 하면 `knowledgeStore` 첫 번째 예제에서는 `knowledgeStore` 다른 시나리오에서 사용할 수 있는 테이블 형식 데이터 구조를 만드는 프로젝션으로 구성 된 개체를 채웁니다. 

## <a name="projecting-to-tables"></a>테이블로 프로젝션

Power BI와 같은 도구를 사용 하 여 보고 및 분석 하는 데 Azure Storage의 테이블로 프로젝션이 유용 합니다. Power BI는 프로젝션 중 생성 되는 키를 기준으로 테이블에서 읽고 관계를 검색할 수 있습니다. 대시보드를 작성 하려는 경우 관련 데이터가 있으면 해당 작업이 간단해 집니다. 

문서에서 추출 된 키 구를 word 클라우드로 시각화 하는 대시보드를 빌드 해 보겠습니다. 올바른 데이터 구조를 만들려면 Shaper 기술을 기술에 추가 하 여 문서 관련 세부 정보 및 키 구가 있는 사용자 지정 셰이프를 만듭니다. 사용자 지정 셰이프가 `pbiShape` `document` 루트 노드에서 호출 됩니다.

> [!NOTE] 
> 테이블 프로젝션은 Azure Storage에 의해 적용 되는 저장소 제한에 의해 관리 되는 Azure Storage 테이블입니다. 자세한 내용은 [테이블 저장소 제한](/rest/api/storageservices/understanding-the-table-service-data-model)을 참조 하세요. 엔터티 크기는 1mb를 초과할 수 없으며 단일 속성이 64 KB 보다 클 수 없다는 것을 알고 있는 것이 유용 합니다. 이러한 제약 조건을 통해 테이블은 많은 수의 작은 엔터티를 저장 하는 데 적합 한 솔루션입니다.

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>Shaper 기술을 사용 하 여 사용자 지정 모양 만들기

테이블 저장소로 프로젝션 할 수 있는 사용자 지정 셰이프를 만듭니다. 사용자 지정 셰이프가 없으면 프로젝션은 단일 노드 (출력 마다 하나의 프로젝션)만 참조할 수 있습니다. 사용자 지정 셰이프를 만들면 여러 요소가 단일 테이블로 프로젝션 될 수 있는 새 논리적 전체로 집계 되거나 테이블 컬렉션에 걸쳐 분리 및 분산 됩니다. 

이 예제에서 사용자 지정 셰이프는 메타 데이터와 식별 된 엔터티 및 키 구를 결합 합니다. 개체가 호출 되 `pbiShape` 고의 부모가 됩니다 `/document` . 

> [!IMPORTANT] 
> 셰이핑의 한 가지 용도는 모든 보강 노드가 올바른 형식의 JSON으로 표현 되도록 하는 것입니다 .이는 기술 자료 저장소로 프로젝션 하는 데 필요 합니다. 보강 트리에 잘 구성 된 JSON이 아닌 노드가 포함 된 경우 (예: 보강가 문자열과 같은 기본 형식에 대 한 부모로 지정 된 경우) 특히 그렇습니다.
>
> 마지막 두 개의 노드인 및를 확인 합니다 `KeyPhrases` `Entities` . 이러한 개체는를 사용 하 여 유효한 JSON 개체에 래핑됩니다 `sourceContext` . 이는 `keyphrases` `entities` 기본 형식에서 강화이 필요 하며, 프로젝션 하기 전에 유효한 JSON으로 변환 해야 합니다.
>


```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForTables",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_content_type",
            "source": "/document/metadata_storage_content_type",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_storage_name",
            "source": "/document/metadata_storage_name",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_storage_path",
            "source": "/document/metadata_storage_path",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "metadata_content_type",
            "source": "/document/metadata_content_type",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "keyPhrases",
            "source": null,
            "sourceContext": "/document/merged_content/keyphrases/*",
            "inputs": [
                {
                    "name": "KeyPhrases",
                    "source": "/document/merged_content/keyphrases/*"
                }

            ]
        },
        {
            "name": "Entities",
            "source": null,
            "sourceContext": "/document/merged_content/entities/*",
            "inputs": [
                {
                    "name": "Entities",
                    "source": "/document/merged_content/entities/*/name"
                }

            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "pbiShape"
        }
    ]
}
```

위의 Shaper 기술을 기술에 추가 합니다. 

```json
    "name": "azureblob-skillset",
    "description": "A friendly description of the skillset goes here.",
    "skills": [
        {
            Shaper skill goes here
            }
        ],
    "cognitiveServices":  "A key goes here",
    "knowledgeStore": []
}  
```

이제 테이블에 프로젝트를 수행 하는 데 필요한 모든 데이터가 있으므로 테이블 정의를 사용 하 여 knowledgeStore 개체를 업데이트 합니다. 이 예에는 `tableName` , 및 속성을 설정 하 여 정의 된 세 개의 테이블이 있습니다 `source` `generatedKeyName` .

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [
                {
                    "tableName": "pbiDocument",
                    "generatedKeyName": "Documentid",
                    "source": "/document/pbiShape"
                },
                {
                    "tableName": "pbiKeyPhrases",
                    "generatedKeyName": "KeyPhraseid",
                    "source": "/document/pbiShape/keyPhrases/*"
                },
                {
                    "tableName": "pbiEntities",
                    "generatedKeyName": "Entityid",
                    "source": "/document/pbiShape/Entities/*"
                }
            ],
            "objects": [],
            "files": []
        }
    ]
}
```

다음 단계를 수행 하 여 작업을 처리할 수 있습니다.

1. 속성을 ```storageConnectionString``` 유효한 V2 범용 저장소 계정 연결 문자열로 설정 합니다.  

1. PUT 요청을 실행 하 여 기술를 업데이트 합니다.

1. 기술를 업데이트 한 후 인덱서를 실행 합니다. 

이제 세 개의 테이블이 있는 작업 프로젝션이 있습니다. 이러한 테이블을 Power BI로 가져오면 관계가 Power BI 자동 검색 됩니다.

다음 예제를 진행 하기 전에 테이블 프로젝션의 측면을 검토 하 여 데이터 조각화 및 관련 데이터의 메커니즘을 이해 하겠습니다.

### <a name="slicing"></a>조각화 

조각화는 전체 통합 셰이프를 구성 부분으로 세분 하는 기술입니다. 결과는 개별적으로 작업할 수 있는 별도의 관련 테이블로 구성 됩니다.

예에서 `pbiShape` 는 통합 셰이프 (또는 보강 노드)입니다. 프로젝션 정의에서 `pbiShape` 는 추가 테이블로 분리 되며,이를 통해 도형의 일부를 끌어올 수 있습니다 ```keyPhrases``` ```Entities``` . Power BI이 기능은 여러 엔터티 및 keyPhrases 각 문서와 연결 되어 있고, 엔터티 및 keyPhrases을 범주화 된 데이터로 볼 수 있는 경우 더 많은 정보를 얻을 수 있는 경우에 유용 합니다.

조각화는 부모 테이블의를 사용 하 여 ```generatedKeyName``` 자식 테이블에 동일한 이름의 열을 만들어 부모 및 자식 테이블 간에 관계를 암시적으로 생성 합니다. 

### <a name="naming-relationships"></a>이름 지정 관계

```generatedKeyName```및 ```referenceKeyName``` 속성을 사용 하 여 테이블 간 또는 프로젝션 형식 간에 데이터를 연결할 수 있습니다. 자식 테이블/프로젝션의 각 행에는 부모를 가리키는 속성이 있습니다. 자식의 열 또는 속성 이름은 ```referenceKeyName``` 부모에서입니다. 를 ```referenceKeyName``` 제공 하지 않으면 서비스는 부모에서로 기본값을 설정 합니다 ```generatedKeyName``` . 

Power BI은 이러한 생성 된 키를 사용 하 여 테이블 내에서 관계를 검색 합니다. 자식 테이블의 열이 다른 이름으로 지정 되어야 하는 경우 ```referenceKeyName``` 부모 테이블에서 속성을 설정 합니다. 한 가지 예는 ```generatedKeyName``` pbiDocument 테이블에서 AS ID를 DocumentID로 설정 하는 것 ```referenceKeyName``` 입니다. 이렇게 하면 이름이 DocumentID 인 문서 id가 포함 된 pbiEntities 및 pbiKeyPhrases 테이블의 열이 생성 됩니다.

## <a name="projecting-to-objects"></a>개체로 프로젝션

개체 프로젝션은 테이블 프로젝션과 동일한 제한이 없으며 많은 문서를 프로젝션 하는 데 더 적합 합니다. 이 예제에서 전체 문서는 개체 프로젝션으로 전송 됩니다. 개체 프로젝션은 컨테이너에서 단일 프로젝션으로 제한 되며 분할 될 수 없습니다.

개체 프로젝션을 정의 하려면 ```objects``` 프로젝션에서 배열을 사용 합니다. Shaper 기술을 사용 하 여 새 도형을 생성 하거나 개체 투영의 인라인 셰이핑을 사용할 수 있습니다. 테이블 예제에서는 모양 및 조각화를 만드는 방법을 보여 주지만이 예제에서는 인라인 셰이핑을 사용 하는 방법을 보여 줍니다. 

인라인 셰이핑은 프로젝션에 대 한 입력 정의에 새 셰이프를 만들 수 있는 기능입니다. 인라인 셰이핑은 Shaper 기술이 생성 하는 것과 동일한 익명 개체를 만듭니다 (이 경우 `pbiShape` ). 인라인 셰이핑은 다시 사용할 계획이 없는 셰이프를 정의 하는 경우에 유용 합니다.

프로젝션 속성은 배열입니다. 이 예제에서는 knowledgeStore 정의에 인라인 프로젝션이 포함 된 새 프로젝션 인스턴스를 배열에 추가 합니다. 인라인 프로젝션을 사용 하는 경우 Shaper 기술을 생략할 수 있습니다.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
             {
                "tables": [ ],
                "objects": [
                    {
                        "storageContainer": "sampleobject",
                        "source": null,
                        "generatedKeyName": "myobject",
                        "sourceContext": "/document",
                        "inputs": [
                            {
                                "name": "metadata_storage_name",
                                "source": "/document/metadata_storage_name"
                            },
                            {
                                "name": "metadata_storage_path",
                                "source": "/document/metadata_storage_path"
                            },
                            {
                                "name": "content",
                                "source": "/document/content"
                            },
                            {
                                "name": "keyPhrases",
                                "source": "/document/merged_content/keyphrases/*"
                            },
                            {
                                "name": "entities",
                                "source": "/document/merged_content/entities/*/name"
                            },
                            {
                                "name": "ocrText",
                                "source": "/document/normalized_images/*/text"
                            },
                            {
                                "name": "ocrLayoutText",
                                "source": "/document/normalized_images/*/layoutText"
                            }
                        ]

                    }
                ],
                "files": []
            }
        ]
    }
```

## <a name="projecting-to-file"></a>파일로 프로젝션

파일 프로젝션은 소스 문서에서 추출 된 이미지 이거나 보강 프로세스에서 프로젝션 될 수 있는 보강의 출력입니다. 개체 프로젝션과 비슷한 파일 프로젝션은 Azure Storage에서 blob으로 구현 되 고 이미지를 포함 합니다. 

파일 프로젝션을 생성 하려면 `files` 프로젝션 개체에서 배열을 사용 합니다. 이 예제에서는 문서에서 추출한 모든 이미지를 라는 컨테이너로 프로젝션 `samplefile` 합니다.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [ ],
                "objects": [ ],
                "files": [
                    {
                        "storageContainer": "samplefile",
                        "source": "/document/normalized_images/*"
                    }
                ]
            }
        ]
    }
```

## <a name="projecting-to-multiple-types"></a>여러 형식으로 프로젝션

더 복잡 한 시나리오에서는 프로젝션 형식 간에 콘텐츠를 프로젝션 해야 할 수 있습니다. 예를 들어 키 구 및 엔터티와 같은 일부 데이터를 테이블에 프로젝션 해야 하는 경우 텍스트 및 레이아웃 텍스트의 OCR 결과를 개체로 저장 한 다음 이미지를 파일로 프로젝션 합니다. 

이 예에서는 기술를 다음 변경 내용으로 업데이트 합니다.

1. 각 문서에 대 한 행이 있는 테이블을 만듭니다.
1. 각 키 구가이 테이블의 행으로 식별 된 문서 테이블과 관련 된 테이블을 만듭니다.
1. 각 엔터티가이 테이블의 행으로 식별 된 문서 테이블과 관련 된 테이블을 만듭니다.
1. 각 이미지에 대 한 레이아웃 텍스트를 사용 하 여 개체 프로젝션을 만듭니다.
1. 추출 된 각 이미지를 프로젝션 하는 파일 프로젝션을 만듭니다.
1. Document 테이블에 대 한 참조, 레이아웃 텍스트와 파일 프로젝션을 포함 하는 개체 프로젝션을 포함 하는 상호 참조 테이블을 만듭니다.

이러한 변경 내용은 knowledgeStore 정의에 추가로 반영 됩니다. 

### <a name="shape-data-for-cross-projection"></a>크로스 프로젝션 용 셰이프 데이터

이러한 프로젝션에 필요한 셰이프를 가져오려면 먼저 라는 모양의 개체를 만드는 새 Shaper 스킬을 추가 `crossProjection` 합니다. 

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForCross",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_name",
            "source": "/document/metadata_storage_name",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "keyPhrases",
            "source": null,
            "sourceContext": "/document/merged_content/keyphrases/*",
            "inputs": [
                {
                    "name": "KeyPhrases",
                    "source": "/document/merged_content/keyphrases/*"
                }

            ]
        },
        {
            "name": "entities",
            "source": null,
            "sourceContext": "/document/merged_content/entities/*",
            "inputs": [
                {
                    "name": "Entities",
                    "source": "/document/merged_content/entities/*/name"
                }

            ]
        },
        {
            "name": "images",
            "source": null,
            "sourceContext": "/document/normalized_images/*",
            "inputs": [
                {
                    "name": "image",
                    "source": "/document/normalized_images/*"
                },
                {
                    "name": "layoutText",
                    "source": "/document/normalized_images/*/layoutText"
                },
                {
                    "name": "ocrText",
                    "source": "/document/normalized_images/*/text"
                }
                ]
        }
 
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "crossProjection"
        }
    ]
}
```

### <a name="define-table-object-and-file-projections"></a>테이블, 개체 및 파일 프로젝션 정의

통합 된 간 프로젝션 프로젝션 개체에서 개체를 여러 테이블로 분할 하 고 OCR 출력을 blob으로 캡처한 다음 이미지를 파일로 저장 합니다 (Blob storage에도 해당).

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
             {
                "tables": [
                    {
                        "tableName": "crossDocument",
                        "generatedKeyName": "Id",
                        "source": "/document/crossProjection"
                    },
                    {
                        "tableName": "crossEntities",
                        "generatedKeyName": "EntityId",
                        "source": "/document/crossProjection/entities/*"
                    },
                    {
                        "tableName": "crossKeyPhrases",
                        "generatedKeyName": "KeyPhraseId",
                        "source": "/document/crossProjection/keyPhrases/*"
                    },
                    {
                        "tableName": "crossReference",
                        "generatedKeyName": "CrossId",
                        "source": "/document/crossProjection/images/*"
                    }
                     
                ],
                "objects": [
                    {
                        "storageContainer": "crossobject",
                        "generatedKeyName": "crosslayout",
                        "source": null,
                        "sourceContext": "/document/crossProjection/images/*/layoutText",
                        "inputs": [
                            {
                                "name": "OcrLayoutText",
                                "source": "/document/crossProjection/images/*/layoutText"
                            }
                        ]
                    }
                ],
                "files": [
                     {
                        "storageContainer": "crossimages",
                        "generatedKeyName": "crossimages",
                        "source": "/document/crossProjection/images/*/image"
                    }
                    ]
                
            }
        ]
    }
```

개체 프로젝션에는 각 프로젝션에 대해 컨테이너 이름이 필요 합니다. 개체 프로젝션 또는 파일 프로젝션에서는 컨테이너를 공유할 수 없습니다. 

### <a name="relationships-among-table-object-and-file-projections"></a>테이블, 개체 및 파일 프로젝션 간의 관계

또한이 예제에서는 투영의 또 다른 기능을 강조 합니다. 동일한 프로젝션 개체 내에서 여러 유형의 프로젝션을 정의 하면 여러 유형 (테이블, 개체, 파일) 내에서 및에 표시 되는 관계가 있습니다. 이를 통해 문서에 대 한 테이블 행부터 시작 하 여 개체 프로젝션에서 해당 문서 내에 있는 이미지에 대 한 모든 OCR 텍스트를 찾을 수 있습니다. 

데이터를 연결 하지 않으려면 다른 프로젝션 개체에서 프로젝션을 정의 합니다. 예를 들어 다음 코드 조각은 테이블과 개체 (OCR 텍스트) 프로젝션 간의 관계를 제외 하 고 테이블의 관련 관계를 생성 합니다. 

프로젝션 그룹은 요구 사항에 따라 서로 다른 모양으로 동일한 데이터를 프로젝션 하려는 경우에 유용 합니다. 예를 들어 Power BI 대시보드의 프로젝션 그룹과 사용자 지정 기술에 래핑된 기계 학습 모델을 학습 하는 데 사용 되는 데이터를 캡처하는 또 다른 프로젝션 그룹이 있습니다.

여러 유형의 프로젝션을 작성할 때 파일 및 개체 프로젝션이 먼저 생성 되 고 테이블에 경로가 추가 됩니다.

```json
"knowledgeStore" : {
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
        "projections": [
            {
                "tables": [
                    {
                        "tableName": "unrelatedDocument",
                        "generatedKeyName": "Documentid",
                        "source": "/document/pbiShape"
                    },
                    {
                        "tableName": "unrelatedKeyPhrases",
                        "generatedKeyName": "KeyPhraseid",
                        "source": "/document/pbiShape/keyPhrases"
                    }
                ],
                "objects": [
                    
                ],
                "files": []
            }, 
            {
                "tables": [],
                "objects": [
                    {
                        "storageContainer": "unrelatedocrtext",
                        "source": null,
                        "sourceContext": "/document/normalized_images/*/text",
                        "inputs": [
                            {
                                "name": "ocrText",
                                "source": "/document/normalized_images/*/text"
                            }
                        ]
                    },
                    {
                        "storageContainer": "unrelatedocrlayout",
                        "source": null,
                        "sourceContext": "/document/normalized_images/*/layoutText",
                        "inputs": [
                            {
                                "name": "ocrLayoutText",
                                "source": "/document/normalized_images/*/layoutText"
                            }
                        ]
                    }
                ],
                "files": []
            }
        ]
    }
```

## <a name="common-issues"></a>일반적인 문제

프로젝션을 정의할 때 예기치 않은 결과를 일으킬 수 있는 몇 가지 일반적인 문제가 있습니다. 기술 자료 저장소의 출력이 예상치 못한 경우 이러한 문제를 확인 합니다.

+ 문자열 강화를 유효한 JSON에 셰이핑 하지 않습니다. 문자열이 보강 경우 (예: `merged_content` 키 구가 있는 보강) 보강 속성은 `merged_content` 보강 트리 내에서의 자식으로 표시 됩니다. 기본 표현은 올바른 형식의 JSON이 아닙니다. 따라서 프로젝션 타임에 보강를 이름 및 값을 사용 하 여 유효한 JSON 개체로 변환 해야 합니다.

+ ```/*```소스 경로의 끝에를 생략 합니다. 프로젝션의 소스가 인 경우 `/document/pbiShape/keyPhrases` 키 구 배열은 단일 개체/행으로 프로젝션 됩니다. 대신의 원본 경로를로 설정 `/document/pbiShape/keyPhrases/*` 하 여 각 키 구에 대해 단일 행 또는 개체를 생성 합니다.

+ 경로 구문 오류입니다. 경로 선택기는 대/소문자를 구분 하며 선택기에 대해 정확한 대/소문자를 사용 하지 않는 경우 입력 경고가 누락 될 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서의 예제에서는 프로젝션을 만드는 방법에 대 한 일반적인 패턴을 보여 줍니다. 개념을 이해 했으므로 이제 특정 시나리오에 대 한 예측을 구축 하는 것이 더 좋습니다.

새 기능을 탐색할 때 다음 단계로 증분 보강을 고려 합니다. 증분 보강는 캐싱을 기반으로 하므로 기술 수정의 영향을 받지 않는 모든 강화을 다시 사용할 수 있습니다. 이는 OCR 및 이미지 분석이 포함 된 파이프라인에 특히 유용 합니다.

> [!div class="nextstepaction"]
> [증분 보강 및 캐싱 소개](cognitive-search-incremental-indexing-conceptual.md)

예측에 대 한 개요는 그룹 및 조각화와 같은 기능 및 [기술에서 정의](knowledge-store-projection-overview.md) 하는 방법에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [지식 저장소의 프로젝션](knowledge-store-projection-overview.md)