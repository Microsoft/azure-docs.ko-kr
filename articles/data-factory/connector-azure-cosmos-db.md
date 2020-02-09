---
title: Azure Cosmos DB에서 데이터 복사 및 변환 (SQL API)
description: Data Factory를 사용 하 여 Azure Cosmos DB (SQL API) 간에 데이터를 복사 하 고 Azure Cosmos DB (SQL API)에서 데이터를 변환 하는 방법에 대해 알아봅니다.
services: data-factory, cosmosdb
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/11/2019
ms.openlocfilehash: 6e9e1d54599ab88092638762ccd7974e44c82cbf
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025812"
---
# <a name="copy-and-transform-data-in-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Azure Data Factory를 사용 하 여 Azure Cosmos DB (SQL API)에서 데이터 복사 및 변환

> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](v1/data-factory-azure-documentdb-connector.md)
> * [현재 버전](connector-azure-cosmos-db.md)

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Azure Cosmos DB(SQL API) 간에 데이터를 복사하고 Data Flow를 사용하여 Azure Cosmos DB(SQL API)에서 데이터를 변환하는 방법을 설명합니다. Azure Data Factory에 대해 자세히 알아보려면 [소개 문서](introduction.md)를 참조하세요.

>[!NOTE]
>이 커넥터는 SQL API Cosmos DB 지원 합니다. MongoDB API의 경우 [Azure Cosmos DB의 API for MongoDB용 커넥터](connector-azure-cosmos-db-mongodb-api.md)를 참조하세요. 다른 API 형식은 이제 지원되지 않습니다.

## <a name="supported-capabilities"></a>지원되는 기능

이 Azure Cosmos DB (SQL API) 커넥터는 다음과 같은 작업에 대해 지원 됩니다.

- [지원 되는 원본/싱크 매트릭스](copy-activity-overview.md) 를 사용 하 여 [복사 작업](copy-activity-overview.md)
- [데이터 흐름 매핑](concepts-data-flow-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

복사 활동의 경우이 Azure Cosmos DB (SQL API) 커넥터는 다음을 지원 합니다.

- Azure Cosmos DB [SQL API](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction) 간에 데이터를 복사합니다.
- **insert** 또는 **upsert**로 Azure Cosmos DB에 씁니다.
- JSON 문서를 있는 그대로 가져오고 내보내거나 데이터를 테이블 형식 데이터 세트 간에 복사합니다. 예로는 SQL 데이터베이스 및 CSV 파일이 있습니다. JSON 파일 또는 다른 Azure Cosmos DB 컬렉션 간에 문서를 있는 그대로 복사 하려면 [json 문서 가져오기 및 내보내기](#import-and-export-json-documents)를 참조 하세요.

Data Factory는 Azure Cosmos DB에 쓸 때 최상의 성능을 제공하기 위해 [Azure Cosmos DB 대량 실행기 라이브러리](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)와 통합됩니다.

> [!TIP]
> [데이터 마이그레이션 동영상](https://youtu.be/5-SRNiC_qOU)에서는 Azure Blob Storage에서 Azure Cosmos DB로 데이터를 복사하는 단계를 안내합니다. 이 동영상에서는 또한 일반적으로 Azure Cosmos DB에 데이터를 수집하기 위한 성능 조정 고려 사항도 설명합니다.

## <a name="get-started"></a>시작하기

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Azure Cosmos DB(SQL API)에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Azure Cosmos DB(SQL API) 연결된 서비스에 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | **type** 속성은 **CosmosDb**로 설정해야 합니다. | 예 |
| connectionString |Azure Cosmos DB 데이터베이스에 연결하는 데 필요한 정보를 지정합니다.<br />**참고**: 다음 예제에 표시된 대로 연결 문자열에 데이터베이스 정보를 지정해야 합니다. <br/> Azure Key Vault에 계정 키를 넣고, 연결 문자열에서 `accountKey` 구성을 끌어올 수도 있습니다. 자세한 내용은 다음 샘플 및 [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md) 문서를 참조하세요. |예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 데이터 저장소가 프라이빗 네트워크에 있는 경우, 자체 호스팅 통합 런타임을 사용할 수 있습니다. 이 속성을 지정하지 않으면 기본 Azure Integration Runtime이 사용됩니다. |아닙니다. |

**예제**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**예제: Azure Key Vault에 계정 키 저장**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;Database=<Database>",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트 및 연결된 서비스](concepts-datasets-linked-services.md)를 참조하세요.

Azure Cosmos DB (SQL API) 데이터 집합에 대해 지원 되는 속성은 다음과 같습니다. 

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 데이터 집합의 **type** 속성은 **CosmosDbSqlApiCollection**로 설정 해야 합니다. |예 |
| collectionName |Azure Cosmos DB 문서 컬렉션의 이름입니다. |예 |

"DocumentDbCollection" 형식 데이터 집합을 사용 하는 경우 복사 및 조회 작업에 대해 이전 버전과의 호환성을 위해 계속 지원 되지만 데이터 흐름에는 지원 되지 않습니다. 앞으로 새 모델을 사용 하는 것이 좋습니다.

**예제**

```json
{
    "name": "CosmosDbSQLAPIDataset",
    "properties": {
        "type": "CosmosDbSqlApiCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [],
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 활동 속성

이 섹션에서는 Azure Cosmos DB(SQL API) 원본 및 싱크에서 지원하는 속성 목록을 제공합니다. 작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md)을 참조하세요.

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB(SQL API) 원본

Azure Cosmos DB(SQL API)에서 데이터를 복사하려면 복사 작업의 **source** 형식을 **DocumentDbCollectionSource**로 설정합니다. 

복사 작업 **source** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 **type** 속성은 **CosmosDbSqlApiSource**로 설정 해야 합니다. |예 |
| Query |데이터를 읽는 Azure Cosmos DB 쿼리를 지정합니다.<br/><br/>예:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |아닙니다. <br/><br/>지정하지 않는 경우 실행되는 SQL 문: `select <columns defined in structure> from mycollection` |
| preferredRegions | Cosmos DB에서 데이터를 검색할 때 연결할 기본 영역 목록입니다. | 아닙니다. |
| pageSize | 쿼리 결과의 페이지당 문서 수입니다. 기본값은 "-1" 이며,이는 서비스 쪽 동적 페이지 크기를 최대 1000까지 사용 함을 의미 합니다. | 아닙니다. |

"DocumentDbCollectionSource" 형식 원본을 사용 하는 경우 이전 버전과의 호환성을 위해 그대로 계속 지원 됩니다. Cosmos DB에서 데이터를 복사 하는 다양 한 기능을 제공 하는 새 모델을 사용 하는 것이 좋습니다.

**예제**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cosmos DB SQL API input dataset name>",
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
                "type": "CosmosDbSqlApiSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"",
                "preferredRegions": [
                    "East US"
                ]
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

[JSON 문서를 있는 그대로 내보내려는](#import-and-export-json-documents)경우를 제외 하 고 Cosmos DB에서 데이터를 복사 하는 경우 복사 작업에서 매핑을 지정 하는 것이 가장 좋습니다. Data Factory는 활동에 대해 지정한 매핑을 준수 합니다. 행에 열에 대한 값이 포함 되어 있지 않으면 열 값에 대해 null 값이 제공 됩니다. 매핑을 지정 하지 않으면 Data Factory은 데이터의 첫 번째 행을 사용 하 여 스키마를 유추 합니다. 첫 번째 행에 전체 스키마가 포함 되어 있지 않으면 일부 열이 활동 작업의 결과에서 누락 됩니다.

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Azure Cosmos DB(SQL API) 싱크

Azure Cosmos DB(SQL API)로 데이터를 복사하려면 복사 작업의 **sink** 형식을 **DocumentDbCollectionSink**로 설정합니다. 

복사 작업 **source** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 싱크의 **type** 속성은 **CosmosDbSqlApiSink**로 설정 해야 합니다. |예 |
| writeBehavior |Azure Cosmos DB에 데이터를 쓰는 방법을 설명합니다. 허용되는 값은 **insert** 및 **upsert**입니다.<br/><br/>**upsert**의 동작은 동일한 ID의 문서가 이미 존재하는 경우 문서를 바꾸는 것이며, 존재하지 않는 경우 문서를 삽입하는 것입니다.<br /><br />**참고**: ID가 원래 문서 또는 열 매핑에 지정되지 않은 경우 Data Factory는 문서에 대한 ID를 자동으로 생성합니다. 즉, **upsert**가 예상대로 작동하려면 문서에 ID가 있는지 확인해야 합니다. |아닙니다.<br />(기본값: **insert**) |
| writeBatchSize | Data Factory는 [Azure Cosmos DB 대량 실행기 라이브러리](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)를 사용하여 Azure Cosmos DB에 데이터를 씁니다. **Writebatchsize** 속성은 ADF가 라이브러리에 제공 하는 문서 크기를 제어 합니다. 성능을 개선하기 위해 **writeBatchSize**에 대한 값을 늘리고 문서 크기가 커지는 경우 값을 줄이도록 시도할 수 있습니다. 아래 팁을 참조하세요. |아닙니다.<br />(기본값: **10,000**) |
| disableMetricsCollection | Data Factory 복사 성능 최적화 및 권장 사항에 대한 Cosmos DB RUs와 같은 메트릭을 수집 합니다. 이 동작에 관심이 있는 경우 `true` 지정 하 여 해제 합니다. | 아니요(기본값: `false`) |

>[!TIP]
>JSON 문서를 있는 그대로 가져오려면 [json 문서 가져오기/내보내기](#import-and-export-json-documents) 섹션을 참조 하세요. 테이블 형식 데이터에서 복사 하려면 [관계형 데이터베이스에서 Cosmos DB로 마이그레이션](#migrate-from-relational-database-to-cosmos-db)을 참조 하세요.

>[!TIP]
>Cosmos DB는 단일 요청의 크기를 2MB로 제한합니다. 수식은 요청 크기 = 단일 문서 크기 * 쓰기 일괄 처리 크기입니다. **"요청 크기가 너무 큽니다."** 라는 오류가 발생하는 경우 **복사 싱크 구성에서 `writeBatchSize` 값**을 줄입니다.

"DocumentDbCollectionSink" 형식 원본을 사용 하는 경우 이전 버전과의 호환성을 위해 그대로 계속 지원 됩니다. Cosmos DB에서 데이터를 복사 하는 다양 한 기능을 제공 하는 새 모델을 사용 하는 것이 좋습니다.

**예제**

```json
"activities":[
    {
        "name": "CopyToCosmosDBSQLAPI",
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
                "type": "CosmosDbSqlApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

### <a name="schema-mapping"></a>스키마 매핑

Azure Cosmos DB에서 테이블 형식 싱크로 또는 역방향으로 데이터를 복사 하려면 [스키마 매핑](copy-activity-schema-and-type-mapping.md#schema-mapping)을 참조 하세요.

## <a name="mapping-data-flow-properties"></a>데이터 흐름 속성 매핑

매핑 데이터 흐름에서 데이터를 변환 하는 경우 Cosmos DB에서 컬렉션에 대한 읽기 및 쓰기를 수행할 수 있습니다. 자세한 내용은 데이터 흐름 매핑에서 [원본 변환](data-flow-source.md) 및 [싱크 변환을](data-flow-sink.md) 참조 하세요.

### <a name="source-transformation"></a>원본 변환

Azure Cosmos DB 관련 된 설정은 원본 변환의 **원본 옵션** 탭에서 사용할 수 있습니다. 

**시스템 열 포함:** True 이면 ```id```, ```_ts```및 기타 시스템 열이 CosmosDB의 데이터 흐름 메타 데이터에 포함 됩니다. 컬렉션을 업데이트할 때 기존 행 id를 가져올 수 있도록이를 포함 하는 것이 중요 합니다.

**페이지 크기:** 쿼리 결과의 페이지당 문서 수입니다. 기본값은 서비스 동적 페이지를 1000까지 사용 하는 "-1"입니다.

**처리량:** 읽기 작업을 수행 하는 동안이 데이터 흐름을 실행할 때마다 CosmosDB collection에 적용할 RUs 수에 대한 선택적 값을 설정 합니다. 최소값은 400입니다.

**기본 설정 영역:** 이 프로세스에 대한 기본 읽기 영역을 선택 합니다.

#### <a name="json-settings"></a>JSON 설정

**단일 문서:** ADF가 전체 파일을 단일 JSON doc로 처리 하려면이 옵션을 선택 합니다.

**따옴표 붙지 않은 열 이름:** JSON의 열 이름이 따옴표로 묶여 있지 않은 경우이 옵션을 선택 합니다.

**설명 포함:** JSON 문서에 데이터에 주석이 있는 경우이 선택을 사용 합니다.

**따옴표 붙은 작은따옴표:** 문서의 열과 값이 작은따옴표로 묶여 있는 경우이 값을 선택 해야 합니다.

**백슬래시가 이스케이프 되었습니다.** JSON에서 백슬래시를 사용 하 여 이스케이프 문자를 사용 하는 경우이 옵션을 선택 합니다.

### <a name="sink-transformation"></a>싱크 변환

Azure Cosmos DB 관련 된 설정은 싱크 변환의 **설정** 탭에서 사용할 수 있습니다.

**업데이트 방법:** 데이터베이스 대상에서 허용 되는 작업을 결정 합니다. 기본값은 삽입만 허용 하는 것입니다. 행을 업데이트, upsert 또는 삭제 하려면 해당 작업에 대한 행의 태그를 변경 하는 행을 변경 해야 합니다. 업데이트, upsert 및 삭제의 경우 변경할 행을 결정 하기 위해 키 열을 설정 해야 합니다.

**수집 작업:** 쓰기 전에 대상 컬렉션을 다시 만들지 여부를 결정 합니다.
* 없음: 컬렉션에 대한 작업이 수행 되지 않습니다.
* 다시 만들기: 컬렉션을 삭제 하 고 다시 만듭니다.

**일괄 처리 크기**: 각 버킷에 작성 되는 행 수를 제어 합니다. 일괄 처리 크기가 클수록 압축 및 메모리 최적화가 향상 되지만 데이터를 캐시할 때 메모리 예외가 발생할 위험이 있습니다.

**파티션 키:** 컬렉션에 대한 파티션 키를 나타내는 문자열을 입력 합니다. 예: ```/movies/title```

**처리량:** 이 데이터 흐름을 실행할 때마다 CosmosDB collection에 적용 하려는 RUs 수에 대한 선택적 값을 설정 합니다. 최소값은 400입니다.

**쓰기 처리량 예산:** 대량 수집 Spark 작업에 할당 하려는 RUs의 수를 나타내는 정수입니다. 이 수는 컬렉션에 할당 된 총 처리량을 초과 합니다.

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인 하세요.

## <a name="import-and-export-json-documents"></a>JSON 문서 가져오기 및 내보내기

이 Azure Cosmos DB(SQL API) 커넥터를 사용하여 손쉽게 다음을 수행할 수 있습니다.

* 두 Azure Cosmos DB 컬렉션 간에 문서를 있는 그대로 복사
* Azure Blob Storage, Azure Data Lake Store 및 기타 Azure Data Factory에서 지원하는 파일 기반 저장소 등 다양한 원본에서 Azure Cosmos DB로 JSON 문서 가져오기
* Azure Cosmos DB 컬렉션에서 다양한 파일 기반 저장소로 JSON 문서 내보내기

스키마 중립적 복사를 수행하려면 다음을 수행합니다.

* 데이터 복사 도구를 사용하는 경우, **JSON 파일 또는 Cosmos DB 컬렉션으로 있는 그대로 내보내기** 옵션을 선택합니다.
* 활동 제작을 사용 하는 경우 원본 또는 싱크에 해당 하는 파일 저장소를 사용 하 여 JSON 형식을 선택 합니다.

## <a name="migrate-from-relational-database-to-cosmos-db"></a>관계형 데이터베이스에서 Cosmos DB로 마이그레이션

관계형 데이터베이스 (예: SQL Server Azure Cosmos DB에서 마이그레이션할 때 복사 작업은 원본에서 테이블 형식 데이터를 Cosmos DB의 플랫 JSON 문서에 쉽게 매핑할 수 있습니다. 경우에 따라 [Azure Cosmos DB에서 데이터 모델링](../cosmos-db/modeling-data.md)에 따라 nosql 사용 사례에 최적화 하도록 데이터 모델을 다시 디자인 하는 것이 좋습니다. 예를 들어 하나의 JSON 문서에 모든 관련 하위 항목을 포함 하 여 데이터를 비 정규화 수 있습니다. 이러한 경우 Azure Data Factory 복사 작업을 사용 하 여이 작업을 수행 하는 방법에 대한 연습은 [이 블로그 게시물](https://medium.com/@ArsenVlad/denormalizing-via-embedding-when-copying-data-from-sql-to-cosmos-db-649a649ae0fb) 을 참조 하세요.

## <a name="next-steps"></a>다음 단계

Azure Data Factory의 복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
