---
title: 기술 자료 저장소에서 프로젝션 정의
titleSuffix: Azure Cognitive Search
description: Power BI 또는 Azure ML과 함께 사용 하기 위해 문서를 기술 자료 저장소로 프로젝션 하는 방법에 대 한 일반적인 패턴의 예입니다.
manager: eladz
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: f29f4b91b85c0027df4be2fd5f26ef8f9749fe33
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77165515"
---
# <a name="knowledge-store-projections-how-to-shape-and-export-enrichments-to-the-knowledge-store"></a>기술 자료 저장소 프로젝션: 강화을 기술 자료 저장소로 셰이프 및 내보내는 방법

> [!IMPORTANT] 
> 지식 저장소는 현재 공개 미리 보기로 제공됩니다. 미리 보기 기능은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. [REST API 버전 2019-05-06-Preview](search-api-preview.md)는 미리 보기 기능을 제공합니다. 현재는 포털 지원이 제한적이며 .NET SDK를 지원하지 않습니다.

프로젝션은 기술 자료 저장소에 있는 보강 문서의 물리적 식입니다. 보강 문서를 효과적으로 사용 하려면 구조가 필요 합니다. 이 문서에서는 구조와 관계를 모두 탐색 하 고, 프로젝션 속성을 빌드하는 방법 및 사용자가 만든 프로젝션 형식 간에 데이터를 연결 하는 방법을 배웁니다. 

프로젝션을 만들려면 shaper 기술을 사용 하 여 데이터 모양을 지정 하 여 사용자 지정 개체를 만들거나 인라인 셰이핑 구문을 사용 해야 합니다. 데이터 셰이프에는 프로젝션 하려는 모든 데이터가 포함 됩니다. 이 문서에서는 각 옵션의 예제를 제공 하며, 사용자가 만드는 프로젝션에 대해 옵션 중 하나를 사용 하도록 선택할 수 있습니다.


투영에는 다음 세 가지 유형이 있습니다.
+ 테이블
+ 개체
+ 파일

테이블 프로젝션은 Azure Table storage에 저장 됩니다. 개체 및 파일 프로젝션은 blob 저장소에 기록 되 고 개체 프로젝션은 JSON 파일로 저장 되며 문서와 기술 출력 또는 강화의 콘텐츠를 포함할 수 있습니다. 또한 보강 파이프라인은 이미지와 같은 이진 파일을 추출할 수 있습니다. 이러한 이진 파일은 파일 프로젝션으로 프로젝션 됩니다. 이진 개체를 개체 프로젝션으로 프로젝션 하면 연결 된 메타 데이터만 JSON blob으로 저장 됩니다. 

데이터 셰이핑과 프로젝션 간의 교차를 이해 하기 위해 다양 한 구성을 탐색 하기 위한 기준으로 다음 기술을 사용 합니다. 이 기술는 원시 이미지 및 텍스트 콘텐츠를 처리 합니다. 예측은 지원 하려는 시나리오에 대 한 문서의 내용과 기술 출력에서 정의 됩니다.

또는이 연습의 모든 호출에서 [REST API 샘플](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/projections/Projections%20Docs.postman_collection.json) 을 다운로드 하 여 사용할 수 있습니다.

> [!IMPORTANT] 
> 프로젝션을 시험해 볼 때 비용 제어를 보장 하도록 [인덱서 캐시 속성을 설정](search-howto-incremental-index.md) 하는 것이 유용 합니다. 프로젝션을 편집 하면 인덱서 캐시가 설정 되지 않은 경우 전체 문서가 다시 보강 됩니다. 캐시를 설정 하 고 프로젝션을 업데이트 한 경우 이전에 보강 문서에 대 한 기술 실행으로 인해 Cognitive Services 요금이 부과 되지 않습니다.


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

이제 `knowledgeStore` 개체를 추가 하 고 필요에 따라 각 시나리오에 대 한 프로젝션을 구성할 수 있습니다. 

## <a name="projecting-to-tables-for-scenarios-like-power-bi"></a>Power BI와 같은 시나리오에 대 한 테이블 프로젝션

> [!NOTE] 
> 기술 자료 저장소는 Azure Storage 계정 이므로 테이블 프로젝션은 테이블을 Azure Storage 테이블에 대 한 저장소 제한의 영향을 받습니다. 자세한 내용은 [테이블 저장소 제한](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model)을 참조 하세요. 엔터티 크기는 1mb를 초과할 수 없으며 단일 속성이 64 KB 보다 클 수 없다는 것을 알고 있는 것이 유용 합니다. 이러한 제약 조건을 통해 테이블은 많은 수의 작은 엔터티를 저장 하는 데 적합 한 솔루션입니다.

보강 데이터에 대 한 대시보드를 작성 하려고 할 때 테이블에서 데이터를 읽고 기술 자료 저장소에서 만드는 키를 기준으로 관계를 검색할 수 있습니다. Power BI 문서에서 추출 된 키 구를 word 클라우드로 시각화할 수 있는 대시보드를 빌드하려고 한다고 가정 하면 기술에 shaper 기술을 추가 하 여 문서 관련 세부 정보 및 키 구가 있는 사용자 지정 셰이프를 만들 수 있습니다. 기술에 shaper 기술을 추가 하 여 ```document```에 ```pbiShape``` 라는 새 보강를 만듭니다.

### <a name="using-a-shaper-skill-to-create-a-custom-shape"></a>Shaper 기술을 사용 하 여 사용자 지정 모양 만들기

테이블 저장소로 프로젝션 할 수 있는 사용자 지정 셰이프를 만듭니다. 사용자 지정 셰이프가 없으면 프로젝션은 단일 노드 (출력 마다 하나의 프로젝션)만 참조할 수 있습니다. 사용자 지정 셰이프를 만들면 여러 요소를 단일 테이블로 프로젝션 될 수 있는 새 논리적 전체로 집계 하 고 테이블의 컬렉션에 걸쳐 분리 하 고 배포할 수 있습니다. 이 예제에서 사용자 지정 셰이프는 메타 데이터와 식별 된 엔터티 및 키 구를 결합 합니다. 개체를 pbiShape 라고 하며 `/document`에서 부모로 합니다. 

> [!IMPORTANT] 
> 강화에 대 한 원본 경로를 프로젝션 하려면 먼저 올바른 형식의 JSON 개체를 작성 해야 합니다. 보강 트리는 잘 구성 된 JSON이 아닌 강화를 나타낼 수 있습니다. 예를 들어 보강가 문자열과 같은 primitve의 부모가 되는 경우입니다. `KeyPhrases` 및 `Entities`를 `sourceContext`를 사용 하 여 유효한 JSON 개체로 래핑하는 방법에 대 한 자세한 내용은 `keyphrases` 및 `entities`가 기본 형식에서 강화 하 고 프로젝션 하기 전에 유효한 JSON으로 변환 해야 합니다.

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
기술의 기술 목록에 방금 정의한 shaper 기술을 추가 합니다. 

이제 테이블에 프로젝트를 수행 하는 데 필요한 모든 데이터가 있으므로 테이블 정의를 사용 하 여 knowledgeStore 개체를 업데이트 합니다. 

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

```storageConnectionString``` 속성을 유효한 V2 범용 저장소 계정 연결 문자열로 설정 합니다. 이 시나리오에서는 ```tableName```, ```source``` 및 ```generatedKeyName``` 속성을 설정 하 여 프로젝션 개체에 3 개의 테이블을 정의 합니다. 이제 PUT 요청을 실행 하 여 기술를 업데이트할 수 있습니다.

### <a name="slicing"></a>조각화 

프로젝션 해야 하는 모든 콘텐츠가 단일 셰이프 (또는 보강 노드)에 있는 통합 도형으로 시작 하는 경우 조각화는 단일 노드를 여러 테이블 또는 개체로 조각화 하는 기능을 제공 합니다. 여기에서 ```pbiShape``` 개체는 여러 테이블로 나뉩니다. 조각화 기능을 사용 하면 셰이프의 일부를 ```keyPhrases``` 하 고 ```Entities```를 개별 테이블로 끌어올 수 있습니다. 이는 여러 엔터티 및 keyPhrases 각 문서와 연결 된 경우에 유용 합니다. 조각화 implicity 부모 테이블의 ```generatedKeyName```을 사용 하 여 부모 테이블과 자식 테이블 간에 관계를 생성 하 여 자식 테이블에 동일한 이름의 열을 만듭니다. 

### <a name="naming-relationships"></a>이름 지정 관계
```generatedKeyName``` 및 ```referenceKeyName``` 속성은 테이블 간 또는 프로젝션 형식 간에 데이터를 연결 하는 데 사용 됩니다. 자식 테이블/프로젝션의 각 행에는 부모를 가리키는 속성이 있습니다. 자식의 열 또는 속성 이름은 부모의 ```referenceKeyName```입니다. ```referenceKeyName```를 제공 하지 않으면 서비스는 부모에서 ```generatedKeyName```로 기본값을 설정 합니다. PowerBI는 이러한 생성 된 키를 사용 하 여 테이블 내에서 관계를 검색 합니다. 자식 테이블의 열이 다른 이름으로 지정 되어야 하는 경우 부모 테이블에서 ```referenceKeyName``` 속성을 설정 합니다. 한 가지 예는 pbiDocument ```referenceKeyName``` 테이블의 ID로 ```generatedKeyName```를 DocumentID로 설정 하는 것입니다. 이렇게 하면 이름이 DocumentID 인 문서 id가 포함 된 pbiEntities 및 pbiKeyPhrases 테이블의 열이 생성 됩니다.

업데이트 된 기술를 저장 하 고 인덱서를 실행 합니다. 이제 3 개의 테이블을 사용 하 여 작업 프로젝션을 만들었습니다. 이러한 테이블을 Power BI로 가져오면 관계 Power BI 자동 검색 됩니다.

## <a name="projecting-to-objects"></a>개체로 프로젝션

개체 프로젝션은 테이블 프로젝션과 동일한 제한 사항이 없으므로 많은 문서를 프로젝션 하는 데 더 적합 합니다. 이 예제에서는 전체 문서를 개체 프로젝션으로 프로젝션 합니다. 개체 프로젝션은 컨테이너에서 단일 프로젝션으로 제한 됩니다.
개체 프로젝션을 정의 하기 위해 프로젝션에서 ```objects``` 배열을 사용 합니다. Shaper 기술을 사용 하 여 새 도형을 생성 하거나 개체 투영의 인라인 셰이핑을 사용할 수 있습니다. 테이블 예제에서는 모양 및 조각화를 만드는 방법을 보여 주지만이 예제에서는 인라인 셰이핑을 사용 하는 방법을 보여 줍니다. 인라인 셰이핑은 프로젝션에 대 한 입력 정의에서 새 셰이프를 만들 수 있는 기능입니다. 인라인 셰이핑은 유사한 shaper에서 생성 하는 것과 동일한 익명 개체를 만듭니다. 인라인 셰이핑은 다시 사용할 계획이 없는 셰이프를 정의 하는 경우에 유용 합니다.
프로젝션 속성은 배열입니다 .이 예제에서는 새 프로젝션 인스턴스를 배열에 추가 합니다. 인라인으로 정의 된 프로젝션을 사용 하 여 knowledgeStore 정의를 업데이트 합니다. 인라인 프로젝션을 사용 하는 경우 shaper 기술은 필요 하지 않습니다.

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
## <a name="file-projections"></a>파일 프로젝션

파일 프로젝션은 소스 문서에서 추출 된 이미지 이거나 보강 프로세스에서 프로젝션 될 수 있는 강화의 출력입니다. 개체 프로젝션과 비슷한 파일 프로젝션은 blob으로 구현 되 고 이미지를 포함 합니다. 파일 프로젝션을 생성 하려면 프로젝션 개체에서 ```files``` 배열을 사용 합니다. 이 예제에서는 문서에서 추출 된 모든 이미지를 `samplefile`이라는 컨테이너로 프로젝션 합니다.

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

더 복잡 한 시나리오에서는 프로젝션 형식 간에 콘텐츠를 프로젝션 해야 할 수 있습니다. 예를 들어 키 구와 엔터티 등의 일부 데이터를 테이블에 프로젝션 해야 하는 경우 텍스트 및 레이아웃 텍스트의 OCR 결과를 개체로 저장 하 고 이미지를 파일로 프로젝션 합니다. 기술에 대 한이 업데이트는 다음과 같습니다.

1. 각 문서에 대 한 행이 있는 테이블을 만듭니다.
2. 각 키 구가이 테이블의 행으로 식별 된 문서 테이블과 관련 된 테이블을 만듭니다.
3. 각 엔터티가이 테이블의 행으로 식별 된 문서 테이블과 관련 된 테이블을 만듭니다.
4. 각 이미지에 대 한 레이아웃 텍스트를 사용 하 여 개체 프로젝션을 만듭니다.
5. 추출 된 각 이미지를 프로젝션 하는 파일 프로젝션을 만듭니다.
6. Document 테이블에 대 한 참조, 레이아웃 텍스트와 파일 프로젝션을 포함 하는 개체 프로젝션을 포함 하는 상호 참조 테이블을 만듭니다.

먼저 도형 개체를 만드는 기술 배열에 새 shaper 기술을 추가 합니다. 

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

### <a name="relationships"></a>관계

또한이 예제에서는 동일한 프로젝션 개체 내에서 여러 유형의 프로젝션을 정의 하 여 투영의 또 다른 기능을 강조 표시 합니다. 다른 유형 (테이블, 개체, 파일)에 대해 및 내에서 표시 되는 관계가 있습니다. 문서에 대 한 테이블 행에서 시작 하 여 개체 프로젝션에서 해당 문서 내에 있는 이미지에 대 한 모든 OCR 텍스트를 찾을 수 있습니다. 데이터를 연결 하지 않으려면 다른 프로젝션 개체에서 프로젝션을 정의 합니다. 예를 들어 다음 코드 조각과 함께 테이블이 관련 되지만 테이블과 OCR 텍스트 프로젝션 간의 관계는 발생 하지 않습니다. 프로젝션 그룹은 요구 사항에 따라 서로 다른 모양으로 동일한 데이터를 프로젝션 하려는 경우에 유용 합니다. 예를 들어, 데이터를 사용 하 여 기술에 대 한 AI 모델을 학습 하는 데 사용할 수 있는 Power BI 대시보드의 프로젝션 그룹과 기타 프로젝션 그룹입니다.
여러 유형의 프로젝션을 작성할 때 파일 및 개체 프로젝션이 먼저 생성 되 고 경로가 테이블에 추가 됩니다.

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

이러한 예제에서는 프로젝션을 사용 하는 방법에 대 한 일반적인 패턴을 보여 줍니다. 이제 특정 시나리오에 대 한 프로젝션 작성에 대 한 개념을 잘 이해 하 고 있어야 합니다.

## <a name="common-issues"></a>일반적인 문제

프로젝션을 정의할 때 예기치 않은 결과를 일으킬 수 있는 몇 가지 일반적인 문제가 있습니다.

1. 문자열 강화 셰이핑이 아닙니다. 문자열이 보강 때, 예를 들어 키 구가 있는 보강 ```merged_content``` 보강 속성은 보강 트리 내에서 merged_content의 자식으로 표시 됩니다. 그러나 프로젝션 시간에는 이름과 값을 사용 하 여 유효한 JSON 개체로 변환 해야 합니다.
2. 소스 경로의 끝에 ```/*``` 생략 합니다. 예를 들어, 프로젝션 소스가 ```/document/pbiShape/keyPhrases``` 키 구 배열은 단일 개체/행으로 프로젝션 됩니다. 원본 경로를 ```/document/pbiShape/keyPhrases/*``` 설정 하면 각 키 구에 대해 단일 행 또는 개체가 생성 됩니다.
3. 경로 선택기는 대/소문자를 구분 하며 선택기에 대해 정확한 대/소문자를 사용 하지 않는 경우 입력 경고가 누락 될 수 있습니다.

