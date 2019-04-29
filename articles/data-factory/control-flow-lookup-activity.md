---
title: Azure Data Factory에서 조회 작업 | Microsoft Docs
description: 조회 작업을 사용하여 외부 소스의 값을 조회하는 방법을 배웁니다. 이 출력을 이후 작업에서 계속 참조할 수 있습니다.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: shlo
ms.openlocfilehash: 4f0662a71ee14af3c2c1aafee210641fc8b51f1b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60768673"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Azure Data Factory에서 조회 작업

조회 작업은 Azure Data Factory에서 지원하는 데이터 원본에서 데이터 세트를 검색할 수 있습니다. 조회 작업은 다음과 같은 시나리오에 사용합니다.
- 개체 이름을 하드 코딩하는 대신, 후속 작업에서 작동할 개체를 동적으로 결정합니다. 개체의 예로는 파일과 테이블이 있습니다.

조회 작업은 구성 파일 또는 테이블의 내용을 읽고 반환합니다. 쿼리 또는 저장 프로시저를 실행한 결과도 반환합니다. 조회 작업의 출력은 싱글톤 값인 경우 후속 복사 또는 변환 작업에 사용할 수 있습니다. 특성의 배열인 경우 ForEach 작업에 사용할 수 있습니다.

## <a name="supported-capabilities"></a>지원되는 기능

다음은 조회 작업에 지원되는 데이터 원본입니다. 조회 작업에서 반환할 수 있는 최대 행 수는 5,000개, 최대 크기는 2MB입니다. 현재 시간 제한 전까지 가능한 최대 조회 작업 기간은 1시간입니다.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores-for-lookup-activity.md)]

## <a name="syntax"></a>구문

```json
{
    "name": "LookupActivity",
    "type": "Lookup",
    "typeProperties": {
        "source": {
            "type": "<source type>"
            <additional source specific properties (optional)>
        },
        "dataset": { 
            "referenceName": "<source dataset name>",
            "type": "DatasetReference"
        },
        "firstRowOnly": false
    }
}
```

## <a name="type-properties"></a>형식 속성

이름 | 설명 | Type | Required?
---- | ----------- | ---- | --------
데이터 세트 | 조회를 위한 데이터 세트 참조를 제공합니다. 자세한 내용은 해당하는 각 커넥터 문서의 **데이터 세트 속성** 섹션에서 확인하세요. | 키/값 쌍 | 예
source | 복사 작업 원본과 동일한 데이터 세트 관련 원본 속성을 포함하고 있습니다. 자세한 내용은 해당하는 각 커넥터 문서의 **복사 작업 속성** 섹션에서 확인하세요. | 키/값 쌍 | 예
firstRowOnly | 첫 번째 행만 반환할 것인지 아니면 모든 행을 반환할 것인지 여부를 나타냅니다. | Boolean | 아니요. 기본값은 `true`입니다.

> [!NOTE]
> 
> * **ByteArray** 형식의 원본 열은 지원되지 않습니다.
> * **구조체**는 데이터 세트 정의에서 지원되지 않습니다. 텍스트 서식 파일의 경우 헤더 행을 사용하여 열 이름을 입력합니다.
> * 조회 원본이 JSON 파일인 경우 JSON 개체의 모양을 변경하는 `jsonPathDefinition` 설정이 지원되지 않습니다. 전체 개체가 검색됩니다.

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>조회 작업 결과를 후속 작업에 사용

조회 결과는 작업 실행 결과의 `output` 섹션에 반환됩니다.

* **`firstRowOnly`가 `true`(기본값)로 설정되면** 출력 형식은 다음 코드와 같습니다. 조회 결과는 고정된 `firstRow` 키 아래에 있습니다. 결과를 후속 작업에 사용하려면 `@{activity('MyLookupActivity').output.firstRow.TableName}` 패턴을 사용합니다.

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **`firstRowOnly`가 `false`로 설정되면** 출력 형식은 다음 코드와 같습니다. `count` 필드는 반환되는 레코드 수를 나타냅니다. 자세한 값은 고정 `value` 배열 아래에 표시됩니다. 이 경우 조회 작업 후에 [Foreach 작업](control-flow-for-each-activity.md)이 이어집니다. `@activity('MyLookupActivity').output.value` 패턴을 사용하여 `value` 배열을 ForEach 작업 `items` 필드에 전달합니다. `value` 배열의 요소에 액세스하려면 `@{activity('lookupActivity').output.value[zero based index].propertyname}` 구문을 따릅니다. 예는 `@{activity('lookupActivity').output.value[0].tablename}`입니다.

    ```json
    {
        "count": "2",
        "value": [
            {
                "Id": "1",
                "TableName" : "Table1"
            },
            {
                "Id": "2",
                "TableName" : "Table2"
            }
        ]
    } 
    ```

### <a name="copy-activity-example"></a>복사 작업 예
이 예에서 복사 작업은 Azure SQL Database 인스턴스의 SQL 테이블에서 Azure Blob 스토리지로 데이터를 복사합니다. SQL 테이블의 이름은 Blob Storage의 JSON 파일에 저장됩니다. 조회 작업은 런타임 시 테이블 이름을 조회합니다. JSON은 이 방법을 사용하여 동적으로 수정됩니다. 따라서 사용자가 파이프라인 또는 데이터 세트를 다시 배포할 필요가 없습니다. 

이 예제는 첫 번째 행만 조회합니다. 모든 행을 조회하고 결과를 ForEach 작업과 연결하려면 [Azure Data Factory를 사용하여 여러 테이블 대량 복사](tutorial-bulk-copy.md)의 샘플을 참조하세요.

### <a name="pipeline"></a>파이프라인
이 파이프라인은 조회 및 복사 작업을 포함합니다. 

- 조회 작업은 Azure Blob Storage의 위치를 참조하는 **LookupDataset**를 사용하도록 구성됩니다. 조회 작업은 이 위치에 있는 JSON 파일에서 SQL 테이블의 이름을 읽습니다. 
- 복사 작업은 SQL 테이블의 이름인 조회 작업의 출력을 사용합니다. **SourceDataset**의 **tableName** 속성은 조회 작업의 출력을 사용하도록 구성됩니다. 복사 작업은 SQL 테이블의 데이터를 Azure Blob Storage의 위치로 복사합니다. 위치는 **SinkDataset** 속성을 통해 지정합니다. 

```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "dataset": { 
                        "referenceName": "LookupDataset", 
                        "type": "DatasetReference" 
                    }
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": { 
                        "type": "SqlSource", 
                        "sqlReaderQuery": "select * from @{activity('LookupActivity').output.firstRow.tableName}" 
                    },
                    "sink": { 
                        "type": "BlobSink" 
                    }
                },                
                "dependsOn": [ 
                    { 
                        "activity": "LookupActivity", 
                        "dependencyConditions": [ "Succeeded" ] 
                    }
                 ],
                "inputs": [ 
                    { 
                        "referenceName": "SourceDataset", 
                        "type": "DatasetReference" 
                    } 
                ],
                "outputs": [ 
                    { 
                        "referenceName": "SinkDataset", 
                        "type": "DatasetReference" 
                    } 
                ]
            }
        ]
    }
}
```

### <a name="lookup-dataset"></a>조회 데이터 세트
**lookup** 데이터 세트는 **AzureStorageLinkedService** 형식으로 지정된 Azure Storage 조회 폴더의 **sourcetable.json** 파일입니다. 

```json
{
    "name": "LookupDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "lookup",
            "fileName": "sourcetable.json",
            "format": {
                "type": "JsonFormat",
                "filePattern": "SetOfObjects"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="source-dataset-for-copy-activity"></a>복사 작업의 **원본** 데이터 세트
**원본** 데이터 세트는 SQL 테이블의 이름인 조회 작업의 출력을 사용합니다. 복사 작업은 이 SQL 테이블의 데이터를 Azure Blob Storage의 위치로 복사합니다. 위치는 **sink** 속성을 통해 지정합니다. 

```json
{
    "name": "SourceDataset",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties":{
            "tableName": "@{activity('LookupActivity').output.firstRow.tableName}"
        },
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="sink-dataset-for-copy-activity"></a>복사 작업의 **Sink** 데이터 세트
복사 작업은 SQL 테이블의 데이터를 Azure Storage에 있는 **csv** 폴더의 **filebylookup.csv** 파일에 복사합니다. 이 파일은 **AzureStorageLinkedService** 속성을 통해 지정합니다. 

```json
{
    "name": "SinkDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "csv",
            "fileName": "filebylookup.csv",
            "format": {
                "type": "TextFormat"                                                                    
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Azure Storage 연결된 서비스
이 저장소 계정에는 SQL 테이블의 이름을 사용하는 JSON 파일이 포함되어 있습니다. 

```json
{
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<StorageAccountKey>",
                "type": "SecureString"
            }
        }
    },
        "name": "AzureStorageLinkedService"
}
```

### <a name="azure-sql-database-linked-service"></a>Azure SQL Database 연결된 서비스
이 Azure SQL Database 인스턴스에는 Blob 스토리지로 복사할 데이터가 포함되어 있습니다. 

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": {
                "value": "Server=<server>;Initial Catalog=<database>;User ID=<user>;Password=<password>;",
                "type": "SecureString"
            }
        }
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.json

#### <a name="set-of-objects"></a>개체의 집합

```json
{
  "Id": "1",
  "tableName": "Table1"
}
{
   "Id": "2",
  "tableName": "Table2"
}
```

#### <a name="array-of-objects"></a>개체의 배열

```json
[ 
    {
        "Id": "1",
        "tableName": "Table1"
    },
    {
        "Id": "2",
        "tableName": "Table2"
    }
]
```

## <a name="limitations-and-workarounds"></a>제한 사항 및 해결 방법

조회 활동 및 제안된 해결 방법의 몇 가지 제한 사항은 다음과 같습니다.

| 제한 사항 | 해결 방법 |
|---|---|
| 조회 작업에는 최대 5,000개의 행 및 최대 2MB의 크기가 있습니다. | 최대 행 수 또는 최대 크기를 초과하지 않는 데이터를 검색하는 내부 파이프라인을 통해 외부 파이프라인을 반복하는 두 수준의 파이프라인을 설계합니다. |
| | |

## <a name="next-steps"></a>다음 단계
Data Factory에서 지원하는 다른 제어 흐름 작업을 참조하세요. 

- [파이프라인 실행 작업](control-flow-execute-pipeline-activity.md)
- [ForEach 작업](control-flow-for-each-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)
- [웹 작업](control-flow-web-activity.md)
