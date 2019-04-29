---
title: Data Factory를 사용하여 MongoDB용 Azure Cosmos DB API에서/API로 데이터 복사 | Microsoft Docs
description: Data Factory를 사용하여 지원되는 원본 데이터 저장소에서 MongoDB용 Azure Cosmos DB API로, 또는 이러한 데이터 저장소나 API에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: 82418c03039219adedf45828d769d278a14499ff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61259726"
---
# <a name="copy-data-to-or-from-azure-cosmos-dbs-api-for-mongodb-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 MongoDB용 Azure Cosmos DB API에서/API로 데이터 복사

이 문서에서는 Azure Data Factory의 복사 활동을 사용하여 MongoDB용 Azure Cosmos DB API에서/API로 데이터를 복사하는 방법을 간략하게 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [Azure Data Factory의 복사 작업](copy-activity-overview.md)을 기반으로 합니다.

>[!NOTE]
>이 커넥터는 MongoDB용 Azure Cosmos DB API에서/API로 데이터를 복사하는 작업만 지원합니다. SQL API는 [Cosmos DB SQL API 커넥터](connector-azure-cosmos-db.md)를 참조하세요. 기타 API 형식은 이제 지원되지 않습니다.

## <a name="supported-capabilities"></a>지원되는 기능

MongoDB용 Azure Cosmos DB API에서 지원되는 싱크 데이터 저장소로 데이터를 복사할 수도 있고, 지원되는 원본 데이터 저장소에서 MongoDB용 Azure Cosmos DB API로 데이터를 복사할 수도 있습니다. 복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소 및 형식](copy-activity-overview.md#supported-data-stores-and-formats)을 참조하세요.

MongoDB용 Azure Cosmos DB API 커넥터를 사용하여 다음을 수행할 수 있습니다.

- [MongoDB용 Azure Cosmos DB API](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction)에서/API로 데이터를 복사합니다.
- **insert** 또는 **upsert**로 Azure Cosmos DB에 씁니다.
- JSON 문서를 있는 그대로 가져오고 내보내거나 데이터를 테이블 형식 데이터 세트 간에 복사합니다. 예로는 SQL 데이터베이스 및 CSV 파일이 있습니다. JSON 파일 간 또는 다른 Azure Cosmos DB 컬렉션 간에 문서를 있는 그대로 복사하려면 JSON 문서 가져오기 또는 내보내기를 참조하세요.

## <a name="get-started"></a>시작하기

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 MongoDB용 Azure Cosmos DB API 전용 Data Factory 엔터티를 정의하는 데 사용할 수 있는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

MongoDB용 Azure Cosmos DB API 연결된 서비스에서 지원되는 속성은 다음과 같습니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | **type** 속성을 **CosmosDbMongoDbApi**로 설정해야 합니다. | 예 |
| connectionString |MongoDB용 Azure Cosmos DB API의 연결 문자열을 지정합니다. Azure Portal -> Cosmos DB 블레이드 -> `mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb` 패턴의 기본 또는 보조 연결 문자열에서 찾을 수 있습니다. <br/><br />이 필드를 **SecureString** 형식으로 표시하여 Data Factory에서 안전하게 저장합니다. [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)할 수도 있습니다. |예 |
| 데이터베이스 | 액세스하려는 데이터베이스 이름입니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 데이터 저장소가 개인 네트워크에 있는 경우, 자체 호스팅 통합 런타임을 사용할 수 있습니다. 이 속성을 지정하지 않으면 기본 Azure Integration Runtime이 사용됩니다. |아닙니다. |

**예제**

```json
{
    "name": "CosmosDbMongoDBAPILinkedService",
    "properties": {
        "type": "CosmosDbMongoDbApi",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb"
            },
            "database": "myDatabase"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트 및 연결된 서비스](concepts-datasets-linked-services.md)를 참조하세요. MongoDB용 Azure Cosmos DB API 데이터 세트에서 지원되는 속성은 다음과 같습니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 데이터 세트의 **type** 속성을 **CosmosDbMongoDbApiCollection**으로 설정해야 합니다. |예 |
| collectionName |Azure Cosmos DB 컬렉션의 이름입니다. |예 |

**예제**

```json
{
    "name": "CosmosDbMongoDBAPIDataset",
    "properties": {
        "type": "CosmosDbMongoDbApiCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB's API for MongoDB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 활동 속성

이 섹션에서는 MongoDB용 Azure Cosmos DB API 원본 및 싱크에서 지원하는 속성 목록을 제공합니다.

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md)을 참조하세요.

### <a name="azure-cosmos-dbs-api-for-mongodb-as-source"></a>MongoDB용 Azure Cosmos DB API(원본)

복사 작업 **source** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 복사 작업 원본의 **type** 속성을 **CosmosDbMongoDbApiSource**로 설정해야 합니다. |예 |
| filter | 쿼리 연산자를 사용하여 선택 영역 필터를 지정합니다. 컬렉션의 모든 문서를 반환하려면 이 매개 변수를 생략하거나 빈 문서({})를 전달합니다. | 아닙니다. |
| cursorMethods.project | 프로젝션에 대한 문서에서 반환할 필드를 지정합니다. 일치하는 문서에서 모든 필드를 반환하려면 이 매개 변수를 생략합니다. | 아닙니다. |
| cursorMethods.sort | 쿼리가 일치하는 문서를 반환하는 순서를 지정합니다. [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort)를 참조하세요. | 아닙니다. |
| cursorMethods.limit | 서버에서 반환하는 문서의 최대 수를 지정합니다. [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit)를 참조하세요.  | 아닙니다. | 
| cursorMethods.skip | MongoDB가 결과를 반환하기 시작하는 위치에서 건너뛸 문서 수를 지정합니다. [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip)을 참조하세요. | 아닙니다. |
| batchSize | MongoDB 인스턴스의 응답을 각각 일괄 처리로 반환할 문서 수를 지정합니다. 대부분의 경우 일괄 처리 크기를 수정해도 사용자 또는 애플리케이션에 영향이 없습니다. Cosmos DB는 각 일괄 처리가 문서 크기의 batchSize 수의 합인 40MB를 초과할 수 없도록 제한하므로 문서 크기가 대규모인 경우 이 값을 줄입니다. | 아닙니다.<br/>(기본값은 **100**) |

>[!TIP]
>ADF는 **Strict 모드**로 BSON 문서를 사용하는 것을 지원합니다. 필터 쿼리가 Shell 모드가 아닌 Strict 모드에 있는지 확인합니다. 자세한 설명은 [MongoDB 설명서](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html)를 참조하세요.

**예제**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBMongoDBAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Cosmos DB's API for MongoDB input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "CosmosDbMongoDbApiSource",
                "filter": "{datetimeData: {$gte: ISODate(\"2018-12-11T00:00:00.000Z\"),$lt: ISODate(\"2018-12-12T00:00:00.000Z\")}, _id: ObjectId(\"5acd7c3d0000000000000000\") }",
                "cursorMethods": {
                    "project": "{ _id : 1, name : 1, age: 1, datetimeData: 1 }",
                    "sort": "{ age : 1 }",
                    "skip": 3,
                    "limit": 3
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-dbs-api-for-mongodb-as-sink"></a>MongoDB용 Azure Cosmos DB API(싱크)

복사 작업 **sink** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 복사 작업 싱크의 **type** 속성은 **CosmosDbMongoDbApiSink**로 설정해야 합니다. |예 |
| writeBehavior |Azure Cosmos DB에 데이터를 쓰는 방법을 설명합니다. 허용되는 값은 **insert** 및 **upsert**입니다.<br/><br/>**upsert**의 동작은 동일한 ID의 문서가 이미 존재하는 경우 문서를 바꾸는 것이며, 존재하지 않는 경우 문서를 삽입하는 것입니다.<br /><br />**참고**: ID가 원래 문서 또는 열 매핑에 지정되지 않은 경우 Data Factory는 문서에 대한 ID를 자동으로 생성합니다. 즉, **upsert**가 예상대로 작동하려면 문서에 ID가 있는지 확인해야 합니다. |아닙니다.<br />(기본값: **insert**) |
| writeBatchSize | **writeBatchSize** 속성은 각 일괄 처리에서 작성할 문서의 크기를 제어합니다. 성능을 개선하기 위해 **writeBatchSize**에 대한 값을 늘리고 문서 크기가 커지는 경우 값을 줄여 볼 수 있습니다. |아닙니다.<br />(기본값: **10,000**) |
| writeBatchTimeout | 시간 초과되기 전에 배치 삽입 작업을 완료하기 위한 대기 시간입니다. 허용되는 값은 시간 범위입니다. | 아닙니다.<br/>(기본값은 **00:30:00** - 30분) |

**예제**

```json
"activities":[
    {
        "name": "CopyToCosmosDBMongoDBAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "CosmosDbMongoDbApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

>[!TIP]
>JSON 문서를 그대로 가져오려면 [JSON 문서 가져오기 또는 내보내기](#import-or-export-json-documents) 섹션을 참조하세요. 테이블 형식 데이터를 복사하려면 [스키마 매핑](#schema-mapping)을 참조하세요.

## <a name="import-or-export-json-documents"></a>JSON 문서 가져오기 또는 내보내기

이 Azure Cosmos DB 커넥터를 사용하여 손쉽게 다음을 수행할 수 있습니다.

* Azure Blob Storage, Azure Data Lake Store 및 기타 Azure Data Factory에서 지원하는 파일 기반 저장소 등 다양한 원본에서 Azure Cosmos DB로 JSON 문서 가져오기
* Azure Cosmos DB 컬렉션에서 다양한 파일 기반 저장소로 JSON 문서 내보내기
* 두 Azure Cosmos DB 컬렉션 간에 문서를 있는 그대로 복사

이러한 스키마 독립적 복사를 완수하려면 데이터 세트 및 복사 작업의 스키마 매핑에서 "구조"(*스키마*라고도 함) 섹션을 건너뛰세요.

## <a name="schema-mapping"></a>스키마 매핑

MongoDB용 Azure Cosmos DB API에서 테이블 형식 싱크로 또는 싱크에서 API로 데이터를 복사하려면 [스키마 매핑](copy-activity-schema-and-type-mapping.md#schema-mapping)을 참조하세요.

Cosmos DB에 대한 쓰기 작업을 위해 특별히 원본 데이터에서 올바른 개체 ID로 Cosmos DB를 채울 수 있도록 하려면(예: SQL 데이터베이스 테이블에 "ID" 열이 있고 삽입/upsert하기 위해 MongoDB에서 문서 ID로 해당 값을 사용함) MongoDB strict 모드 정의(`_id.$oid`)에 따라 적절한 스키마 매핑을 다음과 같이 설정해야 합니다.

![MongoDB 싱크에서 ID 매핑](./media/connector-azure-cosmos-db-mongodb-api/map-id-in-mongodb-sink.png)

복사 작업을 실행한 후에 싱크에 아래 BSON ObjectId 작업이 생성됩니다.

```json
{
    "_id": ObjectId("592e07800000000000000000")
}
``` 

## <a name="next-steps"></a>다음 단계

Azure Data Factory의 복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md##supported-data-stores-and-formats)를 참조하세요.
