---
title: Azure Data Factory에서 분할된 데이터를 읽거나 쓰는 방법 | Microsoft Docs
description: Azure Data Factory에서 분할된 데이터를 읽거나 쓰는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: shlo
ms.openlocfilehash: f2d780900a0cd24f2d70499573a4055dc836ae0b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61466989"
---
# <a name="how-to-read-or-write-partitioned-data-in-azure-data-factory"></a>Azure Data Factory에서 분할된 데이터를 읽거나 쓰는 방법

Azure Data Factory 버전 1에서는 **SliceStart**, **SliceEnd**, **WindowStart** 및 **WindowEnd** 시스템 변수를 사용하여 분할된 데이터를 읽거나 쓸 수 있었습니다. 현재 버전의 Data Factory에서는 파이프라인 매개 변수와 트리거의 시작 시간/예약된 시간을 매개 변수 값으로 사용하여 이 동작을 수행할 수 있습니다. 

## <a name="use-a-pipeline-parameter"></a>파이프라인 매개 변수 사용 

Data Factory 버전 1에서는 다음 예제와 같이 **partitionedBy** 속성과 **SliceStart** 시스템 변수를 사용할 수 있었습니다. 

```json
"folderPath": "adfcustomerprofilingsample/logs/marketingcampaigneffectiveness/{Year}/{Month}/{Day}/",
"partitionedBy": [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "%M" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "%d" } }
],
```

**partitonedBy** 속성에 대한 자세한 내용은 [Azure Data Factory를 사용하여 다른 곳에서 Azure Blob Storage로 또는 그 반대로 데이터를 복사](v1/data-factory-azure-blob-connector.md#dataset-properties)를 참조하세요. 

현재 버전의 Data Factory에서 이 작업을 수행하려면: 

1. 형식 **문자열**의 *파이프라인 매개 변수*를 정의합니다. 다음 예제에서 파이프라인 매개 변수의 이름은 **windowStartTime**입니다. 
2. 데이터 세트 정의에서 **folderPath**를 파이프라인 매개 변수의 값을 참조하도록 설정합니다. 
3. 요청 시 파이프라인을 호출할 때 매개 변수의 실제 값을 전달합니다. 또한 런타임에서 트리거의 시작 시간 또는 예약된 시간을 동적으로 전달할 수 있습니다. 

```json
"folderPath": {
      "value": "adfcustomerprofilingsample/logs/marketingcampaigneffectiveness/@{formatDateTime(pipeline().parameters.windowStartTime, 'yyyy/MM/dd')}/",
      "type": "Expression"
},
```

## <a name="pass-in-a-value-from-a-trigger"></a>트리거에서 값 전달

다음 연속 창 트리거 정의에서 트리거의 창 시작 시간은 **windowStartTime** 파이프라인 매개 변수의 값으로 전달됩니다. 

```json
{
    "name": "MyTrigger",
    "properties": {
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": "1",
            "startTime": "2018-05-15T00:00:00Z",
            "delay": "00:10:00",
            "maxConcurrency": 10
        },
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipeline"
            },
            "parameters": {
                "windowStartTime": "@trigger().outputs.windowStartTime"
            }
        }
    }
}
```

## <a name="example"></a>예

다음은 샘플 데이터 세트 정의입니다.

```json
{
  "name": "SampleBlobDataset",
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "value": "adfcustomerprofilingsample/logs/marketingcampaigneffectiveness/@{formatDateTime(pipeline().parameters.windowStartTime, 'yyyy/MM/dd')}/",
      "type": "Expression"
    },
    "format": {
      "type": "TextFormat",
      "columnDelimiter": ","
    }
  },
  "structure": [
    { "name": "ProfileID", "type": "String" },
    { "name": "SessionStart", "type": "String" },
    { "name": "Duration", "type": "Int32" },
    { "name": "State", "type": "String" },
    { "name": "SrcIPAddress", "type": "String" },
    { "name": "GameType", "type": "String" },
    { "name": "Multiplayer", "type": "String" },
    { "name": "EndRank", "type": "String" },
    { "name": "WeaponsUsed", "type": "Int32" },
    { "name": "UsersInteractedWith", "type": "String" },
    { "name": "Impressions", "type": "String" }
  ],
  "linkedServiceName": {
    "referenceName": "churnStorageLinkedService",
    "type": "LinkedServiceReference"
  }
}
```

파이프라인 정의: 

```json
{
    "properties": {
        "activities": [{
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": {
                    "value": "@concat(pipeline().parameters.blobContainer, '/scripts/', pipeline().parameters.partitionHiveScriptFile)",
                    "type": "Expression"
                },
                "scriptLinkedService": {
                    "referenceName": "churnStorageLinkedService",
                    "type": "LinkedServiceReference"
                },
                "defines": {
                    "RAWINPUT": {
                        "value": "@concat('wasb://', pipeline().parameters.blobContainer, '@', pipeline().parameters.blobStorageAccount, '.blob.core.windows.net/logs/', pipeline().parameters.inputRawLogsFolder, '/')",
                        "type": "Expression"
                    },
                    "Year": {
                        "value": "@formatDateTime(pipeline().parameters.windowStartTime, 'yyyy')",
                        "type": "Expression"
                    },
                    "Month": {
                        "value": "@formatDateTime(pipeline().parameters.windowStartTime, 'MM')",
                        "type": "Expression"
                    },
                    "Day": {
                        "value": "@formatDateTime(pipeline().parameters.windowStartTime, 'dd')",
                        "type": "Expression"
                    }
                }
            },
            "linkedServiceName": {
                "referenceName": "HdiLinkedService",
                "type": "LinkedServiceReference"
            },
            "name": "HivePartitionGameLogs"
        }],
        "parameters": {
            "windowStartTime": {
                "type": "String"
            },
            "blobStorageAccount": {
                "type": "String"
            },
            "blobContainer": {
                "type": "String"
            },
            "inputRawLogsFolder": {
                "type": "String"
            }
        }
    }
}
```

## <a name="next-steps"></a>다음 단계

파이프라인이 있는 데이터 팩터리를 만드는 방법의 전체 연습은 [빠른 시작: 데이터 팩터리 만들기](quickstart-create-data-factory-powershell.md)를 참조하세요. 

