---
title: Azure Data Factory에서 파이프라인 실행 작업 | Microsoft Docs
description: 파이프라인 실행 작업을 사용하여 하나의 Data Factory 파이프라인에서 다른 Data Factory 파이프라인을 호출하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: a0ece499262464bc28f55c37188698a3313e2c04
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60808858"
---
# <a name="execute-pipeline-activity-in-azure-data-factory"></a>Azure Data Factory에서 파이프라인 실행 작업
파이프라인 실행 작업을 사용하면 하나의 Data Factory 파이프라인에서 다른 파이프라인을 호출할 수 있습니다.

## <a name="syntax"></a>구문

```json
{
    "name": "MyPipeline",
    "properties": {
        "activities": [
            {
                "name": "ExecutePipelineActivity",
                "type": "ExecutePipeline",
                "typeProperties": {
                    "parameters": {                        
                        "mySourceDatasetFolderPath": {
                            "value": "@pipeline().parameters.mySourceDatasetFolderPath",
                            "type": "Expression"
                        }
                    },
                    "pipeline": {
                        "referenceName": "<InvokedPipelineName>",
                        "type": "PipelineReference"
                    },
                    "waitOnCompletion": true
                 }
            }
        ],
        "parameters": [
            {
                "mySourceDatasetFolderPath": {
                    "type": "String"
                }
            }
        ]
    }
}
```

## <a name="type-properties"></a>형식 속성

자산 | 설명 | 허용되는 값 | 필수
-------- | ----------- | -------------- | --------
이름 | 파이프라인 실행 작업의 이름입니다. | String | 예
형식 | 다음으로 설정되어야 합니다. **ExecutePipeline**. | String | 예
pipeline | 이 파이프라인을 호출하는 종속 파이프라인에 대한 파이프라인 참조입니다. 파이프라인 참조 개체에는 두 가지 속성(**referenceName** 및 **type**)이 있습니다. referenceName 속성은 참조 파이프라인의 이름을 지정합니다. type 속성은 PipelineReference로 설정되어야 합니다. | PipelineReference | 예
매개 변수 | 호출된 파이프라인으로 전달될 매개 변수 | 매개 변수 이름을 인수 값에 매핑하는 JSON 개체 | 아닙니다.
waitOnCompletion | 종속 파이프라인 실행이 완료될 때까지 작업 실행을 기다릴지 여부를 정의합니다. 기본값은 false입니다. | Boolean | 아닙니다.

## <a name="sample"></a>샘플
이 시나리오에는 두 개의 파이프라인이 있습니다.

- **마스터 파이프라인** - 이 파이프라인에는 호출된 파이프라인을 호출하는 하나의 파이프라인 실행 작업이 있습니다. 마스터 파이프라인에는 두 개의 매개 변수, `masterSourceBlobContainer`와 `masterSinkBlobContainer`가 필요합니다.
- **호출된 파이프라인** - 이 파이프라인에는 Azure Blob 원본에서 Azure Blob 싱크로 데이터를 복사하는 하나의 복사 작업이 있습니다. 호출된 파이프라인에는 두 개의 매개 변수, `sourceBlobContainer`와 `sinkBlobContainer`가 필요합니다.

### <a name="master-pipeline-definition"></a>마스터 파이프라인 정의

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ExecutePipeline",
        "typeProperties": {
          "pipeline": {
            "referenceName": "invokedPipeline",
            "type": "PipelineReference"
          },
          "parameters": {
            "sourceBlobContainer": {
              "value": "@pipeline().parameters.masterSourceBlobContainer",
              "type": "Expression"
            },
            "sinkBlobContainer": {
              "value": "@pipeline().parameters.masterSinkBlobContainer",
              "type": "Expression"
            }
          },
          "waitOnCompletion": true
        },
        "name": "MyExecutePipelineActivity"
      }
    ],
    "parameters": {
      "masterSourceBlobContainer": {
        "type": "String"
      },
      "masterSinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

### <a name="invoked-pipeline-definition"></a>호출된 파이프라인 정의

```json
{
  "name": "invokedPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "name": "CopyBlobtoBlob",
        "inputs": [
          {
            "referenceName": "SourceBlobDataset",
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sinkBlobDataset",
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

**연결된 서비스**

```json
{
    "name": "BlobStorageLinkedService",
    "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": {
        "value": "DefaultEndpointsProtocol=https;AccountName=*****",
        "type": "SecureString"
      }
    }
  }
}
```

**원본 데이터 세트**
```json
{
    "name": "SourceBlobDataset",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@pipeline().parameters.sourceBlobContainer",
        "type": "Expression"
      },
      "fileName": "salesforce.txt"
    },
    "linkedServiceName": {
      "referenceName": "BlobStorageLinkedService",
      "type": "LinkedServiceReference"
    }
  }
}
```

**싱크 데이터 세트**
```json
{
    "name": "sinkBlobDataset",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@pipeline().parameters.sinkBlobContainer",
        "type": "Expression"
      }
    },
    "linkedServiceName": {
      "referenceName": "BlobStorageLinkedService",
      "type": "LinkedServiceReference"
    }
  }
}
```

### <a name="running-the-pipeline"></a>파이프라인 실행

이 예제에서 마스터 파이프라인을 실행하려면 masterSourceBlobContainer 및 masterSinkBlobContainer 매개 변수에 다음 값이 전달됩니다. 

```json
{
  "masterSourceBlobContainer": "executetest",
  "masterSinkBlobContainer": "executesink"
}
```

다음 예제와 같이 마스터 파이프라인은 호출된 파이프라인에 이 값을 전달합니다. 

```json
{
    "type": "ExecutePipeline",
    "typeProperties": {
      "pipeline": {
        "referenceName": "invokedPipeline",
        "type": "PipelineReference"
      },
      "parameters": {
        "sourceBlobContainer": {
          "value": "@pipeline().parameters.masterSourceBlobContainer",
          "type": "Expression"
        },
        "sinkBlobContainer": {
          "value": "@pipeline().parameters.masterSinkBlobContainer",
          "type": "Expression"
        }
      },

      ....
}

```
## <a name="next-steps"></a>다음 단계
Data Factory에서 지원하는 다른 제어 흐름 작업을 참조하세요. 

- [ForEach 작업](control-flow-for-each-activity.md)
- [메타데이터 작업 가져오기](control-flow-get-metadata-activity.md)
- [조회 작업](control-flow-lookup-activity.md)
- [웹 작업](control-flow-web-activity.md)
