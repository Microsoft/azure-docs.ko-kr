---
title: Azure Data Factory에서 ForEach 작업 | Microsoft Docs
description: ForEach 작업은 파이프라인의 반복 제어 흐름을 정의합니다. 컬렉션에 대한 반복 작업에 사용되며 특정 작업을 실행합니다.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: shlo
ms.openlocfilehash: c5c12a66e8f66195a096588d779648d7486ab47b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60808772"
---
# <a name="foreach-activity-in-azure-data-factory"></a>Azure Data Factory의 ForEach 작업
ForEach 작업은 파이프라인의 반복 제어 흐름을 정의합니다. 이 작업을 사용하여 컬렉션을 반복하고 루프의 지정된 작업을 실행합니다. 이 작업의 루프 구현은 프로그래밍 언어에서 구조를 반복하는 Foreach와 비슷합니다.

## <a name="syntax"></a>구문
속성은 이 문서의 뒷부분에서 설명합니다. 항목 속성은 컬렉션이며 컬렉션의 각 항목은 다음 구문에서처럼 `@item()`를 사용하여 참조합니다.  

```json
{  
   "name":"MyForEachActivityName",
   "type":"ForEach",
   "typeProperties":{  
      "isSequential":"true",
        "items": {
            "value": "@pipeline().parameters.mySinkDatasetFolderPathCollection",
            "type": "Expression"
        },
      "activities":[  
         {  
            "name":"MyCopyActivity",
            "type":"Copy",
            "typeProperties":{  
               ...
            },
            "inputs":[  
               {  
                  "referenceName":"MyDataset",
                  "type":"DatasetReference",
                  "parameters":{  
                     "MyFolderPath":"@pipeline().parameters.mySourceDatasetFolderPath"
                  }
               }
            ],
            "outputs":[  
               {  
                  "referenceName":"MyDataset",
                  "type":"DatasetReference",
                  "parameters":{  
                     "MyFolderPath":"@item()"
                  }
               }
            ]
         }
      ]
   }
}

```

## <a name="type-properties"></a>형식 속성

자산 | 설명 | 허용되는 값 | 필수
-------- | ----------- | -------------- | --------
이름 | for-each 작업의 이름입니다. | String | 예
형식 | **ForEach**로 설정되어야 합니다. | String | 예
isSequential | 순차 또는 병렬로 루프를 실행할지 지정합니다.  한 번에 최대 20개의 루프 반복을 병렬로 실행할 수 있습니다. 예를 들어 **isSequential**이 False로 설정된 10개의 다른 원본과 싱크 데이터 세트가 있는 복사 작업에 대해 반복되는 ForEach 작업의 경우, 모든 복사가 한 번에 실행됩니다. 기본값은 False입니다. <br/><br/> "IsSequential"이 False로 설정된 경우 여러 실행 파일을 실행하기 위해 정확한 구성이 있는지 확인합니다. 그렇지 않으면 쓰기 충돌이 발생하지 않도록 이 속성을 주의하여 사용해야 합니다. 자세한 내용은 [병렬 실행](#parallel-execution) 섹션을 참조하세요. | Boolean | 아니요. 기본값은 False입니다.
batchCount | 병렬 실행 수를 제어하는 데 사용하는 Batch 계정입니다(IsSequential이 false로 설정된 경우). | 정수(최대값 50) | 아니요. 기본값은 20입니다.
항목 | 반복되는 JSON 배열을 반환하는 식 | 식(JSON 배열 반환)  | 예
활동 | 실행할 작업 | 작업 목록 | 예

## <a name="parallel-execution"></a>병렬 실행
**isSequential**이 false로 설정된 경우 최대 20개의 동시 반복에서 병렬로 작업이 반복됩니다. 이 설정은 주의해서 사용해야 합니다. 동시 반복을 동일한 폴더의 다른 파일에 쓰는 것은 괜찮습니다. 동시 반복을 동시에 정확히 동일한 파일에 쓸 경우 오류가 발생할 가능성이 높습니다. 

## <a name="iteration-expression-language"></a>반복 식 언어
ForEach 작업에서 **items** 속성에 대해 반복될 배열을 제공합니다. `@item()`를 사용하여 ForEach 작업에서 단일 열거에 대해 반복합니다. 예를 들어 **항목**이 배열: [1, 2, 3]인 경우 `@item()`는 첫 번째 반복에서 1, 두 번째 반복에서 2, 세 번째 반복에서 3을 반환합니다.

## <a name="iterating-over-a-single-activity"></a>단일 활동에 대한 반복
**시나리오:** Azure Blob에서 동일한 원본 파일을 Azure Blob의 여러 대상 파일에 복사합니다.

### <a name="pipeline-definition"></a>파이프라인 정의

```json
{
    "name": "<MyForEachPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyForEachActivity>",
                "type": "ForEach",
                "typeProperties": {
                    "isSequential": "true",
                    "items": {
                        "value": "@pipeline().parameters.mySinkDatasetFolderPath",
                        "type": "Expression"
                    },
                    "activities": [
                        {
                            "name": "MyCopyActivity",
                            "type": "Copy",
                            "typeProperties": {
                                "source": {
                                    "type": "BlobSource",
                                    "recursive": "false"
                                },
                                "sink": {
                                    "type": "BlobSink",
                                    "copyBehavior": "PreserveHierarchy"
                                }
                            },
                            "inputs": [
                                {
                                    "referenceName": "<MyDataset>",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "MyFolderPath": "@pipeline().parameters.mySourceDatasetFolderPath"
                                    }
                                }
                            ],
                            "outputs": [
                                {
                                    "referenceName": "MyDataset",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "MyFolderPath": "@item()"
                                    }
                                }
                            ]
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "mySourceDatasetFolderPath": {
                "type": "String"
            },
            "mySinkDatasetFolderPath": {
                "type": "String"
            }
        }
    }
}

```

### <a name="blob-dataset-definition"></a>Blob 데이터 세트 정의

```json
{  
   "name":"<MyDataset>",
   "properties":{  
      "type":"AzureBlob",
      "typeProperties":{  
         "folderPath":{  
            "value":"@dataset().MyFolderPath",
            "type":"Expression"
         }
      },
      "linkedServiceName":{  
         "referenceName":"StorageLinkedService",
         "type":"LinkedServiceReference"
      },
      "parameters":{  
         "MyFolderPath":{  
            "type":"String"
         }
      }
   }
}

```

### <a name="run-parameter-values"></a>실행 매개 변수 값 

```json
{
    "mySourceDatasetFolderPath": "input/",
    "mySinkDatasetFolderPath": [ "outputs/file1", "outputs/file2" ]
}

```

## <a name="iterate-over-multiple-activities"></a>여러 작업에 대해 반복
ForEach 작업에서는 여러 작업(예: 복사 및 웹 작업)에 대해 반복할 수 있습니다. 이 시나리오에서는 별도의 파이프라인으로 여러 활동을 추상화하는 것이 좋습니다. 그런 다음 ForEach 작업으로 파이프라인에서 [ExecutePipeline 작업](control-flow-execute-pipeline-activity.md)을 사용하여 여러 작업이 있는 별도의 파이프라인을 호출합니다. 


### <a name="syntax"></a>구문

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ForEach",
        "name": "<MyForEachMultipleActivities>"
        "typeProperties": {
          "isSequential": true,
          "items": {
            ...
          },
          "activities": [
            {
              "type": "ExecutePipeline",
              "name": "<MyInnerPipeline>"
              "typeProperties": {
                "pipeline": {
                  "referenceName": "<copyHttpPipeline>",
                  "type": "PipelineReference"
                },
                "parameters": {
                  ...
                },
                "waitOnCompletion": true
              }
            }
          ]
        }
      }
    ],
    "parameters": {
      ...
    }
  }
}

```

### <a name="example"></a>예
**시나리오:** 실행 파이프라인 작업으로 ForEach 작업 내 InnerPipeline에 대해 반복합니다. 내부 파이프라인이 매개 변수화된 스키마 정의로 복사합니다.

#### <a name="master-pipeline-definition"></a>마스터 파이프라인 정의

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ForEach",
        "name": "MyForEachActivity",
        "typeProperties": {
          "isSequential": true,
          "items": {
            "value": "@pipeline().parameters.inputtables",
            "type": "Expression"
          },
          "activities": [
            {
              "type": "ExecutePipeline",
              "typeProperties": {
                "pipeline": {
                  "referenceName": "InnerCopyPipeline",
                  "type": "PipelineReference"
                },
                "parameters": {
                  "sourceTableName": {
                    "value": "@item().SourceTable",
                    "type": "Expression"
                  },
                  "sourceTableStructure": {
                    "value": "@item().SourceTableStructure",
                    "type": "Expression"
                  },
                  "sinkTableName": {
                    "value": "@item().DestTable",
                    "type": "Expression"
                  },
                  "sinkTableStructure": {
                    "value": "@item().DestTableStructure",
                    "type": "Expression"
                  }
                },
                "waitOnCompletion": true
              },
              "name": "ExecuteCopyPipeline"
            }
          ]
        }
      }
    ],
    "parameters": {
      "inputtables": {
        "type": "Array"
      }
    }
  }
}

```

#### <a name="inner-pipeline-definition"></a>내부 파이프라인 정의

```json
{
  "name": "InnerCopyPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            }
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "name": "CopyActivity",
        "inputs": [
          {
            "referenceName": "sqlSourceDataset",
            "parameters": {
              "SqlTableName": {
                "value": "@pipeline().parameters.sourceTableName",
                "type": "Expression"
              },
              "SqlTableStructure": {
                "value": "@pipeline().parameters.sourceTableStructure",
                "type": "Expression"
              }
            },
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sqlSinkDataset",
            "parameters": {
              "SqlTableName": {
                "value": "@pipeline().parameters.sinkTableName",
                "type": "Expression"
              },
              "SqlTableStructure": {
                "value": "@pipeline().parameters.sinkTableStructure",
                "type": "Expression"
              }
            },
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceTableName": {
        "type": "String"
      },
      "sourceTableStructure": {
        "type": "String"
      },
      "sinkTableName": {
        "type": "String"
      },
      "sinkTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="source-dataset-definition"></a>원본 데이터 세트 정의

```json
{
  "name": "sqlSourceDataset",
  "properties": {
    "type": "SqlServerTable",
    "typeProperties": {
      "tableName": {
        "value": "@dataset().SqlTableName",
        "type": "Expression"
      }
    },
    "structure": {
      "value": "@dataset().SqlTableStructure",
      "type": "Expression"
    },
    "linkedServiceName": {
      "referenceName": "sqlserverLS",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "SqlTableName": {
        "type": "String"
      },
      "SqlTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="sink-dataset-definition"></a>싱크 데이터 세트 정의

```json
{
  "name": "sqlSinkDataSet",
  "properties": {
    "type": "AzureSqlTable",
    "typeProperties": {
      "tableName": {
        "value": "@dataset().SqlTableName",
        "type": "Expression"
      }
    },
    "structure": {
      "value": "@dataset().SqlTableStructure",
      "type": "Expression"
    },
    "linkedServiceName": {
      "referenceName": "azureSqlLS",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "SqlTableName": {
        "type": "String"
      },
      "SqlTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="master-pipeline-parameters"></a>마스터 파이프라인 매개 변수
```json
{
    "inputtables": [
        {
            "SourceTable": "department",
            "SourceTableStructure": [
              {
                "name": "departmentid",
                "type": "int"
              },
              {
                "name": "departmentname",
                "type": "string"
              }
            ],
            "DestTable": "department2",
            "DestTableStructure": [
              {
                "name": "departmentid",
                "type": "int"
              },
              {
                "name": "departmentname",
                "type": "string"
              }
            ]
        }
    ]
    
}
```

## <a name="aggregating-outputs"></a>출력 집계

집계 출력에 __foreach__ 활동을 활용 하시기 바랍니다 _변수_ 하 고 _변수 추가_ 활동입니다.

먼저, 파이프라인에서 `array` _변수_를 선언합니다. 그런 다음, 각 __foreach__ 루프 내에서 _변수 추가_ 작업을 호출합니다. 이후에 배열에서 집계를 검색할 수 있습니다.

## <a name="limitations-and-workarounds"></a>제한 사항 및 해결 방법

ForEach 작업 및 제안된 해결 방법의 몇 가지 제한 사항은 다음과 같습니다.

| 제한 사항 | 해결 방법 |
|---|---|
| ForEach 루프를 또 다른 ForEach 루프(또는 Until 루프) 내부에 중첩할 수 없습니다. | 중첩된 루프가 있는 내부 파이프라인을 통해 외부 ForEach 루프가 있는 외부 파이프라인을 반복하는 두 수준의 파이프라인을 설계합니다. |
| ForEach 작업에는 병렬 처리를 위한 최대 50개 `batchCount` 및 최대 100,000개 항목이 있습니다. | 내부 파이프라인을 통해 ForEach 작업이 있는 외부 파이프라인을 반복하는 두 수준의 파이프라인을 설계합니다. |
| | |

## <a name="next-steps"></a>다음 단계
Data Factory에서 지원하는 다른 제어 흐름 작업을 참조하세요. 

- [파이프라인 실행 작업](control-flow-execute-pipeline-activity.md)
- [메타데이터 작업 가져오기](control-flow-get-metadata-activity.md)
- [조회 작업](control-flow-lookup-activity.md)
- [웹 작업](control-flow-web-activity.md)
